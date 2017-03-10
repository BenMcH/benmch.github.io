---
layout: post
title:  "Reserved Words"
date:   2017-03-10 08:53:00 -0600
categories: blog
tags: ruby rails work
---

### What was the issue?

Recently at work, we developed a new ecommerce site to allow users to sign up and pay for classes without our education director having to manually sign them up. We had users complaining that they could not sign up, which was weird because both myself and my boss tested it (in production, mind you), and we were both able to sign up without issue.

We tried everything to reproduce this, but were unable to do so. We even went as far as reformatting our payment system(for the better), and eventually we thought we had a foolproof way to pay.

Wrong. We received a call a few days later from somebody saying that they could not sign up.
Throughout the call, we followed their actions and found that they were, in fact, paying successfully. They actually had paid 4 times in the end because they kept adding the classes to their cart and paying.

### What happened?

As we were on the phone with the student, they said that after paying, their browser said that there was an error with the site. This told us that we needed to check the production log. When I checked, it told me that there was an issue with the session hash in the sign up confirmation screen. I thought that that was weird, as the only place that the session is used in this project is for letting users have items in their cart when not logged in.

When I viewed the code, I saw something like this:

{% highlight Ruby %}
  <% @course_sessions.each do |session| %>
    ...
  <% end %>
{% endhighlight %}

Now, I have used a variable called session before in this very project without issue. The issue arose because I did not instantiate session to be something else. I relied on the loop doing so.

### How we fixed it

What we ended up changing was just renaming this "session" variable in each source file that had this issue to "course_session", which also matches the convention.

In the end, we reversed the transactions for this student and any other students that had signed up for the same classes multiple times.
