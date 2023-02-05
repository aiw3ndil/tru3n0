---
layout: post
title:  "Using Tailwind with Ruby on Rails 7"
tags: ruby rails
---

I decided to start a new project this year. I'm a big fan of Ruby On Rails so I
decided to use the latest version (7.0.4) at the time of this post. And for
Ruby the version 3.1.3

After following the guide on [Tailwind documentation](https://tailwindcss.com/docs/guides/ruby-on-rails)
I used the command:

`rails new my-app --css tailwind`

I have created a new css file on the directory `app > assets > stylesheets` named `session.css` with
the following content:

```
.body-session {
  @apply bg-gray-100 font-sans h-screen w-scren;
}
```

And then imported the file on app > assets > stylesheets > application.css this way:

```
@import "session";
```

But the modifications seems to not work.

I have been reading the documentation and find out the elements must be loaded in certain order.
For example you fist load the base styles, then the components and last the utilities.

So it would look similar to this:

```
@tailwind base;
@tailwind components;
@tailwind utilities;

@layer base {
  .container {
    @apply font-sans;
  }
}
```

As you can see inside the layer you can modify your base elements. This also has to be included on
the file `app/assets/stylesheets/application.tailwind.css`.

I would be looking for more information and updating the post soon.