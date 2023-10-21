---
layout: post
title:  "Rails 7 Devise not showing errors or validations"
tags: ruby rails
---

I'm my new application I'm using Rails 7.0.4 as I commented on
my last post.

I don't know yet how works importmap. I have just a little idea.

So let's say in you have an file named `app/views/layouts/application.html.erb`.
This file is the entire layout for your site.

As you can see, inside this file there is a declaration to load the 
javascript files using importmap:

```
  <%= javascript_importmap_tags %>
```

Well, this method does not behave as desired with our devise templates.

If you go to your sign_in url, usually `http://127.0.0.1:3000/users/sign_in` 
and click on the submit button without entering no data. You will notice 
nothing at all.

This is because importmap is using an XHR request but not loading the turbo
data in the page. I have searched a lot about this issue but found nothing.

The only thing I found useful was the idea of using another layout only for 
devise without using importmap. So I did that. I created an exact copy of the
`app/views/layouts/application.html.erb` named `app/views/layouts/session.html.erb`
replacing the javascript part mentioned before for this one:

```
  <%= javascript_include_tag %>
```

And let decide the application which layout use in each case in the file
`app/controllers/application_controller.rb` with this content:

```
# frozen_string_literal: true

class ApplicationController < ActionController::Base
  layout :layout_by_resource

  private

    def layout_by_resource
      devise_controller? ? "session" : "application"
    end
end
```

So now I can see and format my Devise validations.