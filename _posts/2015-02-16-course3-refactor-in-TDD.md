---
layout: post
title: "Build Robust & Production Quality Applications - Lesson 2: Refactor in TDD"
tags: tealeaf
---
###Add feature in the Red and refactor in the green.
1. Pull out anything complex into a separate method and give it a name
2. Clean up public facing methods.. pulling complex code into private methods


Example:
{%highlight ruby%}

class Todo < ActiveRecord:;Base
  has_many :taggings
  has_many :tags, through: :taggings
  validates_presence_of :name


  def name_only?
    description.blank?
  end

  def display_text
    name + tag_text
  end


  private

  def tag_text
    if tags.any?
      " (#{tags.one? ? 'tag' : 'tags' } : #{tags.map(&:name).first(4).join(", ")}
      #{', more...' if tags.count > 4})"
    else
      ""
    end
  end
end
{%endhighlight%}
