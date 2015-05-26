---
layout: post
title: "Build Robust & Production Quality Applications - Lesson 4: Transactions & Exception Resues"
tags: tealeaf
---

The[ previous example reegarding updating video review while in queue does not follow typical Rails convention:
- We are using in the update_queue method.
- we have to use parameter naming conventions to parse out the queue_item id and
position id in the form.
- We have to specify the order of queue_items using in the user model according to position.


###Using Transactions & Exception Rescues when the value does not save.
{%highlight ruby%}
def update_queue
  begin
    ActiveRecord::Base.transaction do
      params[:queue_items].each do |queue_item_data|
        queue_item = QueueItem.find(queue_item_data["id"])
        queue_item.update_attributes!(position: queue_item_data["position"])
      end
    end
  rescue ActiveRecord::RecordInvalid
    flash[:error] = "You must use whole numbers for play position."
    redirect_to my_queue_path
    return
  end

  current_user.queue_items.each_with_index do |queue_item, index|
    queue_item.update_attributes(position: index+1)
  end
  redirect_to my_queue_path
end
{%endhighlight%}
