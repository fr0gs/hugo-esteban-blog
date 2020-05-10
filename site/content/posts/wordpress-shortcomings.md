+++
author = "Esteban"
title = "Wordpress shortcomings when creating a web application"
description = "A small analysis about the potential shortcomings a creator may encounter if they decide to use Wordpress as the stack of choice to develop a web application."
date = 2020-05-10T19:34:02+02:00
image = ""
slug = "wordpress-shortcomings"
categories = []
tags = ["wordpress", "learning"]
draft = false
+++

# Introduction

Recently this week I was discussing with a friend whether Wordpress could be a viable choice for a web application over a custom solution in order to have a working MVP as fast as possible. This app would include some more complex features such as in-app video chat & messaging. 

Ideally the huge amount of available plugins combined with the ease of installation and minimal code would provide a solid solution almost out of the box. 

Since my experience with Wordpress is limited and as a developer my first reaction is *develop something from scratch*, the possibility of using Wordpress to fulfill all the business cases just combining plugins seemed interesting. 

Thus, I resolved to do some research and enumerate some of its shortcomings for future reference, as well as when is it a good choice to roll your web application with.


# Complexity & Customization

* Wordpress is very lean, so little amount of functionality is there from scrach as initially it was thought of as a blog. 
you often need to add a lot of plugins for baseline functionality.
* Not all functionality is reachable within the available plugins, might need to do a lot of code customization by forking them.
* Not that many PHP developers anymore, it's easier to find for Javascript, Python or Ruby. [Check the interest in Belgium, where I live.](https://trends.google.com/trends/explore?geo=BE&q=%2Fm%2F060kv,%2Fm%2F05z1_,%2Fm%2F02p97,%2Fm%2F06ff5,%2Fm%2F02vtpl)
* Over time plugins get out of sync with each other. For instance, a change in the MailChimp plugin could break your current theme.
* Each plugin is a piece of functionality maintained by someone else that now you need to test separately.
* Wordpress needs to be constantly updated for security reasons, it is the main world target for open source hacking, so if you fall back in a security update for a plugin you may have inadvertently opened a security hole that an attacker can exploit.
	
	
# Highly Structured Content

Wordpress enforces a data model oriented towards content, where entities are laid out in a rigid way and relationships between them are not modifiable, or are very hard to. If the project's data can be mapped into a relational database, Wordpress perhaps is not the best choice. For example, a Wordpress plugin may provide a list of users that you will internally divide into dentists and patients, but they are all stored into the plugin's context. 

Let's say you want to make a dental clinic management software with Wordpress. If you only need to know who's a **Dentist** and who's a **Patient** you can trick your way into that logic and associate a patient to a dentist with some sort of affiliate links plugin (by assigning an affiliate id to a dentist and making the patient purchase from the dentist's affiliate id), but eventually if you need to extend the information related to either of them, you need to create your own data model. 

What if you want to associate treatments to patients, or add a new feature where dentists can recommend different products based on patients' medical data? Eventually you'll need to break the mold to attain that level of customization.


# Database management

* The official documentation for Wordpress states that [only MySQL/MariaDB](https://wordpress.org/support/article/faq-installation/#can-i-use-a-database-other-than-mysqlmariadb) databases are supported. What if you want to use a NoSQL database that adjusts better to your app's requirements? You may want the flexibility of *MongoDB*, or you foresee the need to process a huge amount of distributed data and think of *HBase*. As stated before Wordpress is mostly oriented towards content, and will fall short in any case where other kind of data needs to be modeled and stored.
* Too many database queries, you have no control over how many queries are made against the database and how complex they are. Additionally, Wordpress is a generic solution, so often it will fetch a lot of unnecessary data.	


# Roles & Permissions

If you need a feature on your web application that is only available to certain users, the way the privilege system is laid out in Wordpress may make it extremely hard to limit the access of those users for that custom feature. It might be necessary to either provide them with too much access to Wordpress' admin panel, or force them to contact you to manually apply changes.


	
# When to use it?


* No coding required to stand up a decent looking website. Very nice for landing pages.
* Very easy to set up, normally a couple of clicks in a huge variety of hosting solutions.
* Most common website functionality is available as plugins.
* Excellent documentation and community, Wordpress powers ~30% of websites.
* Barrier of entry for custom development is low.
* If you want to have an almost ready-made API for content and use a custom frontend framework for the view (headless Wordpress) 
	


# References

* [https://www.wakefly.com/blog/when-is-wordpress-enough/](https://www.wakefly.com/blog/when-is-wordpress-enough/)
* [https://matthewdaly.co.uk/blog/2015/08/22/when-you-should-not-use-wordpress/](https://matthewdaly.co.uk/blog/2015/08/22/when-you-should-not-use-wordpress/)
* [https://www.elegantthemes.com/blog/wordpress/how-to-use-wordpress-as-a-back-end-resources-for-getting-started-with-the-rest-api](https://www.elegantthemes.com/blog/wordpress/how-to-use-wordpress-as-a-back-end-resources-for-getting-started-with-the-rest-api)
* [https://blog.mailtrap.io/laravel-vs-wordpress/](https://blog.mailtrap.io/laravel-vs-wordpress/)

