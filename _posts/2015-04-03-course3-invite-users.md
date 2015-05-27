---
layout: post
title: "Build Robust & Production Quality Applications - Lesson 6: Inviting Users- Part 2"
tags: tealeaf
---
##Inviting Users - 2nd part of Workflow:
1. User gets email and selects the "Accept this invitation" link. URL will contain special token
2. Registation form has email prefilled
3. Upon sign up, user automatically follows recommender and vice versa

###Step 1. User recieves email:
{%highlight ruby%}
#views/app_mailers/send_invitation_email.html.haml
!!! 5
%html(lang="en-US")
  %body
  %p You are inivited by #{@invitation.inviter.full_name} to join Myflix!
  %p= @invitation.message
  %p= link_to "Accept this invitation", register_with_token_url(@invitation.token)
{%endhighlight%}

###Create routes
{%highlight ruby%}
 resources :users, only: [:create, :show]
  get 'register/:token', to: "users#new_with_invitation_token", as: 'register_with_token'
{%endhighlight%}

{%highlight ruby%}
#users_controller_spec.rb
describe "GET new_with_invitation_token" do
  it "render the :new view template" do
    invitation = Fabricate(:invitation)
    get :new_with_invitation_token, token: invitation.token
    expect(response).to render_template :new
  end

  it "sets @user with recipient's email" do
    invitation = Fabricate(:invitation)
    get :new_with_invitation_token, token: invitation.token
    expect(assigns(:user).email).to eq(invitation.recipient_email)
  end

  it "sets @invitation_token" do
    invitation = Fabricate(:invitation)
    get :new_with_invitation_token, token: invitation.token
    expect(assigns(:invitation_token)).to eq(invitation.token)
  end

  it "redirects to expired token page for invalid tokens" do
    get :new_with_invitation_token, token: 'asdffdhh'
    expect(response).to redirect_to expired_token_path
  end
{%endhighlight%}

### We need to fabricate an invitation:
{%highlight ruby%}
#fabricators/invitation_fabricators.rb
Fabricator(:invitation) do
  recipient_name { Faker::Name.name }
  recipient_email { Faker::Internet.email }
  message { Faker::Lorem.paragraphs(2).join(" ") }
end
{%endhighlight%}

###Add Tokens Column to Invitations:
{%highlight ruby%}
class AddTokenToInvitations < ActiveRecord::Migration
  def change
    add_column :invitations, :token, :string
  end
end
{%endhighlight%}

{%highlight ruby%}
#users_controller.rb
  def new_with_invitation_token
    invitation = Invitation.where(token: params[:token]).first
    if invitation
      @user = User.new(email: invitation.recipient_email)
      @invitation_token = invitation.token
      render :new
    else
      redirect_to expired_token_path
    end
  end

  private
  def user_params
    params.require(:user).permit!
  end

  def handle_invitations
    if params[:invitation_token].present?
      invitation = Invitation.where(token: params[:invitation_token]).first
      @user.follow(invitation.inviter)
      invitation.inviter.follow(@user)
      invitation.update_column(:token, nil)
    end
  end
end
{%endhighlight%}

###User registers upon clicking link
{%highlight ruby%}
#users/new.html.haml
%section.register.container
  .row
    .col-sm-10.col-sm-offset-1
      = bootstrap_form_for @user, html: { class: "form-horizontal"} do |f|
        %header
          %h1 Register
        %fieldset
          .col-sm-6
            = f.email_field :email, label: "Email Address"
            = f.password_field :password
            = f.text_field :full_name, label: "Full Name"
            = hidden_field_tag :invitation_token, @invitation_token
        %fieldset.actions.control-group.col-sm-offset-0
          .controls
            %input(type="submit" value="Sign Up" class="btn btn-default")
{%endhighlight%}
###Gotchas
Another issue with the current form is when the invitee signs up, we lose the
 lose inviter's info and they are not not following the inviter. To fix this, we need a hidden field on the invitee registration form. This hidden field is a field tag, rather than model backed becasue we don't want this token to be stored under the user's token.

###Step 3: Inviter/Invitee automatically follow each other after being invited:
{%highlight ruby%}
#users_controller_spec.rb
   describe "POST create" do
    context "with valid input" do
      before { post :create, user: Fabricate.attributes_for(:user) }
      it "creates the user" do
        expect(User.count).to eq(1)
      end
      it "redirects to th sign on page" do
        expect(response).to redirect_to sign_in_path
      end

      it "makes the user follow the inviter" do
        alice = Fabricate(:user)
        invitation = Fabricate(:invitation, inviter: alice, recipient_email: 'jam@jamblack.com')
        post :create, user: {email: 'jam@jamblack.com', password: 'password', full_name: 'Jam Black'}, invitation_token: invitation.token
        jam = User.where(email: 'jam@jamblack.com').first
        expect(jam.follows?(alice)).to be_true
      end

      it "makes the inviter follow the user" do
        alice = Fabricate(:user)
        invitation = Fabricate(:invitation, inviter: alice, recipient_email: 'jam@jamblack.com')
        post :create, user: {email: 'jam@jamblack.com', password: 'password', full_name: 'Jam Black'}, invitation_token: invitation.token
        jam = User.where(email: 'jam@jamblack.com').first
        expect(alice.follows?(jam)).to be_true
      end
      it "expires the invitation upon acceptance" do
        alice = Fabricate(:user)
        invitation = Fabricate(:invitation, inviter: alice, recipient_email: 'jam@jamblack.com')
        post :create, user: {email: 'jam@jamblack.com', password: 'password', full_name: 'Jam Black'}, invitation_token: invitation.token
        jam = User.where(email: 'jam@jamblack.com').first
        expect(Invitation.first.token).to be_nil
      end
    end
{%endhighlight%}

{%highlight ruby%}
 describe "#follows?" do
    it "returns true if the user has a following relationship with another user" do
      alice = Fabricate(:user)
      bob = Fabricate(:user)
      Fabricate(:relationship, leader: bob, follower: alice)
      expect(alice.follows?(bob)).to be_true
    end

    it "returns false if the user does not have a following relationship with another user" do
      alice = Fabricate(:user)
      bob = Fabricate(:user)
      Fabricate(:relationship, leader: alice, follower: bob)
      expect(alice.follows?(bob)).to be_false
    end
  end

  describe "#follow" do
    it "follows another user" do
      alice = Fabricate(:user)
      bob = Fabricate(:user)
      alice.follow(bob)
      expect(alice.follows?(bob)).to be_true
    end

    it "does not follow one self" do
      alice = Fabricate(:user)
      alice.follow(alice)
      expect(alice.follows?(alice)).to be_false
    end
  end
end
{%endhighlight%}

{%highlight ruby%}
#users.rb
  def follows?(another_user)
    following_relationships.map(&:leader).include?(another_user)
  end

  def can_follow?(another_user)
    !(self.follows?(another_user) || self == another_user)
  end

  def follow(another_user)
    following_relationships.create(leader: another_user) if can_follow?(another_user)
  end
end
{%endhighlight%}

