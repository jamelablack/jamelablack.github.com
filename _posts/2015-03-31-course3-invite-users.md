---
layout: post
title: "Build Robust & Production Quality Applications - Lesson 6: Inviting Users"
tags: tealeaf
---
Let's write add functionality to allow users to invite their friends:
###Workflow:
1. User can input friends name and email, click invite friend link
2. An email is sent to friend
3. Friend open email, clicks link and then is directed to Registration Form
4. Email address is pre-filled
5. Upon sign up, user automatically follows recommender and vice versa

###Steps:
#####1. Copy ui controller into views/invitations/new.html.haml
{%highlight ruby%}
%section.invite.container
  .row
    .col-sm-10.col-sm-offset-1
      %form.invite
        %header
          %h1 Invite a friend to join MyFlix!
        %fieldset
          .form-group
            %label Friend's Name
            .row
              .col-sm-4
                %input.form-control(type="text")
          .form-group
            %label Friend's Email Address
            .row
              .col-sm-4
                %input.form-control(type="email")
          .form-group
            %label Invitation Message
            .row
              .col-sm-6
                %textarea.form-control(rows=6) Please join this really cool site!
        %fieldset.form-group.action
          %input(type="submit" value="Send Invitation" class="btn btn-default")
{%endhighlight%}

####2. Edit template to create need attributes: recipient name, email, message
{%highlight ruby%}
section.invite.container
  .row
    .col-sm-10.col-sm-offset-1
      %form.invite
      = bootstrap_form_for @invitation, class: "invite" do |f|
        %header
          %h1 Invite a friend to join MyFlix!
        %fieldset
          = f.text_field :recipient_name, label: "Friend's Name"
          = f.text_field :recipient_email, label: "Friend's Email Address"
          = f.text_area :message, class: "span4", rows: 6, placeholder:  "Please join this really cool site!", label: "Message"
        %fieldset.form-group.action
          %input(type="submit" value="Send Invitation" class="btn btn-default")
{%endhighlight%}

####3. Create new and create routes for invitations
{%highlight ruby%}
 resources :invitations, only: [:new, :create]
 {%endhighlight%}

####4. Create InvitationsController
####5. Begin with InvitationsController spec
####6. Create context: for valid inputs and invalid inputs

### GET new
{%highlight ruby%}
#invitation_controller_spec.rb
require 'spec_helper'

describe InvitationsController do
  describe "GET new" do
    it "sets @invitation to a new invitation" do
      set_current_user
      get :new
      expect(assigns(:invitation)).to be_new_record
      expect(assigns(:invitation)).to be_instance_of Invitation
    end
    it_behaves_like "requires sign in" do
      let(:action) { get :new }
    end
  end
{%endhighlight%}

{%highlight ruby%}
class InvitationsController < ApplicationController
  before_filter :require_user
  def new
    @invitation = Invitation.new
  end
{%endhighlight%}

###Generate Migration
{%highlight ruby%}
class CreateInvitations < ActiveRecord::Migration
  def change
    create_table :invitations do |t|
      t.integer :inviter_id
      t.string :recipient_name, :recipient_email
      t.text :message
      t.timestamps
    end
  end
end
{%endhighlight%}


###Create Invitation Model/Model Spec
{%highlight ruby%}
#invitation.rb
class Invitation < ActiveRecord::Base
before_create :generate_token
  belongs_to :inviter, class_name: "User"
  validates_presence_of :recipient_name, :recipient_email, :message

  def generate_token
    self.token = SecureRandom.urlsafe_base64
    end
  end

{%endhighlight%}

{%highlight ruby%}
#invitation_model_spec.rb
require 'spec_helper'

describe Invitation do
  it { should validate_presence_of(:recipient_name) }
  it { should validate_presence_of(:recipient_email) }
  it { should validate_presence_of(:message) }
{%endhighlight%}


###POST create
#### Context: with valid Inputs
{%highlight ruby%}
#invitations_controller_spec.rb
  describe "POST create" do
    it_behaves_like "requires sign in" do
      let(:action) { post :create }
    end
    context "with valid input" do
      it "redirects to ithe invitation new page" do
        set_current_user
        post :create, invitation: { recipient_name: "Jam Black", recipient_email: "jam@jamblack.com", message: "Join Myflix!" }
        expect(response).to redirect_to new_invitation_path
      end

      it "creates the invitation" do
        set_current_user
        post :create, invitation: { recipient_name: "Jam Black", recipient_email: "jam@jamblack.com", message: "Join Myflix!" }
        expect(Invitation.count).to eq(1)
      end
      it "sends an email to the recipient" do
        set_current_user
        post :create, invitation: { recipient_name: "Jam Black", recipient_email: "jam@jamblack.com", message: "Join Myflix!" }
        expect(ActionMailer::Base.deliveries.last.to).to eq(['jam@jamblack.com'])
      end

      it "sets the flash success message" do
        set_current_user
        post :create, invitation: { recipient_name: "Jam Black", recipient_email: "jam@jamblack.com", message: "Join Myflix!" }
        expect(flash[:success]).to be_present
      end
    end
{%endhighlight%}

#### Context: With invalid inputs
{%highlight ruby%}
#invitations_controller_spec.rb
    context "with invalid input" do
      after { ActionMailer::Base.deliveries.clear }
      it "renders the :new template" do
        set_current_user
        post :create, invitation: { recipient_email: "jam@jamblack.com", message: "Join Myflix!" }
        expect(response).to render_template :new
      end

      it "does not create an invitation" do
        set_current_user
        post :create, invitation: { recipient_email: "jam@jamblack.com", message: "Join Myflix!" }
        expect(Invitation.count).to eq(0)
      end
      it "does not send out an email" do
        set_current_user
        post :create, invitation: { recipient_email: "jam@jamblack.com", message: "Join Myflix!" }
        expect(ActionMailer::Base.deliveries).to be_empty
      end

      it "sets the flash error message" do
        set_current_user
        post :create, invitation: { recipient_email: "jam@jamblack.com", message: "Join Myflix!" }
        expect(flash[:error]). to be_present
      end

      it "sets #@invitation" do
        set_current_user
        post :create, invitation: { recipient_email: "jam@jamblack.com", message: "Join Myflix!" }
        expect(assigns(:invitation)).to be_present
      end
    end
  end
end
{%endhighlight%}

{%highlight ruby%}
#invitations_controller.rb
  def create
    @invitation = Invitation.create(invitation_params.merge!(inviter_id: current_user.id))

    if @invitation.save
      AppMailer.send_invitation_email(@invitation).deliver
      flash[:success] = "You've successfully invited #{@invitation.recipient_name}."
      redirect_to new_invitation_path
    else
      flash[:error] = "Please fill in all inputs."
      render :new
    end
  end

  private
  def invitation_params
    params.require(:invitation).permit(:recipient_name, :recipient_email, :message, :inviter_id)
  end
end
{%endhighlight%}

{%highlight ruby%}
class AppMailer < ActionMailer::Base
  def send_invitation_email(invitation)
    @invitation = invitation
    mail to: invitation.recipient_email, from: "info@myflix.com", subject: "Invitation to join Myflix"
  end
end
{%endhighlight%}

###Remember to:
clear the ActionMailer deliveries in the valid input context so they don't interfer with
the invalid inputs. - we want that number to be 0
