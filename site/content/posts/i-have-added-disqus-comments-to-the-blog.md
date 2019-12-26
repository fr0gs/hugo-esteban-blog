+++
author = "Esteban"
date = 2017-09-01T12:44:15Z
description = "Well, I added Disqus comments in the blog."
draft = false
slug = "i-have-added-disqus-comments-to-the-blog"
title = "I have added Disqus comments to the blog"

+++


Hello! I moved my blog to **Ghost** as the previous one was hosted in a **Wordpress** free plan and I linked it from my personal website, and I wanted more control over that and also having a more uniform user interface, provided that my former site's design was a modified free *bootstrap & some jquery* template and it was frankly ugly.

The ghost theme I chose for this blog may not be the fanciest on the internet but it is damn sure it is simple and does not draw attention away from the main point of the site, which is writing posts and giving some information.

However this solution didn't come with out-of-the-box comments functionality, and I really want users who want to engage with my content to have a way to do it other than pinging me over twitter or sending me an email.

I chose **Disqus** because taking a look at all the alternatives, it provides an stupid simple way of integrating the widget, just registering on Disqus' site, a sprinkle of html and javascript and you have it up & running.

Additionally, the fact of utilizing an external service relieves me of the hassle of hosting comments myself, and gives multiple advantages for free: security, social buttons, sharing, and synchronizing with comments made in other disqus-powered comment boards.

I see two drawbacks though: on one side, it involves an additional http request that user's browser must make to activate the comment board. On the other side, Disqus does not allow anonymous comments, but perhaps that's a good thing right? I don't really need XSS attempts and viagra advertisements all over my posts.

Have fun!

