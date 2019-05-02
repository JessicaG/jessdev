---
title: Ruby Gems - what are they?
date: 2013-04-05
cover_index: /assets/ruby-warrior.jpg
cover_detail: /assets/ruby-warrior.jpg
---
I currently work at Commerce Kitchen, a fully integrated web shop. We do Rails development, CMS (Content Management System) manipulation, inbound marketing and SEO, and we can even provide IT support from our friends at Lark. Because of this, we have a lot of lingo and buzzwords zipping around, some of which we don’t all understand.

I found myself speaking ‘developer talk’ last week among some marketing folks and realized we needed a connection. The topic of the conversation was custom CMSs for Rails apps compared to some CMS gems we had researched a few months back. The question from the marketing team was: what is a gem?

Ruby Guides describes it fairly well, labeling it a ‘software package’ that you can bring into your application and manipulate on your system. Gems provide awesome tools to add to your toolbox, and there are many out there. You need to be careful about which gems you choose though; even if a gem may do all the things you are looking for, when was the last time it was updated? What systems is it running on? Is there good documentation from the developer? Once you have done your due diligence, you can choose that particular (or many) gems to install on your system, requiring it in your gemfile and doing a bundle install. Mind you, you will need to have bundler installed first in order for this to work properly (you can do this by typing `gem install bundler` in your command line).

Making this connection was interesting because when we were discussing what a gem was, one of my co-workers made the comparison to a WordPress plugin. With a plugin on WordPress, you can find additional functionality to your blog, e.g. wp-touch which allows you to install the plugin and have a mobile-friendly theme ready to go. You can do this by either downloading the file and uploading into WordPress and activating it, or you can use the WordPress UI with a simple search.

Now, this is definitely not an apples-to-apples comparison, as WordPress plugins give you a really nice UI to work with for easy integration. Rubygems extend what you can do in Ruby and can provide nice abstractions for dealing with complex tasks, but you still have to integrate with them in the code itself.

Another comparison is Excel Macros. If you are an advanced Excel user, you may want to add functionality to use across your projects and spreadsheets, but without repeating steps each time so things stay DRY. You can accomplish this by encapsulating the functionality you need into repeatable macros that manipulate your data. This is similar to a Ruby Gem as you are working with the code to extend the capabilities of Excel, but you still have tell Excel where and when to apply the macro.
See how much fun technology can be? We can find ourselves geeking out in one fashion or another all the time, whether it be a developer, a marketer, and ITer or just a plain nerd. Hope this helps clarify some things for you all. If you’re in Colorado, stay warm with this wet spring snow!
