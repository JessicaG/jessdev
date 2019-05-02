---
title: Glitch - You're Not in the Matrix
date: 2018-02-16
cover_index: /assets/ruby-warrior.jpg
cover_detail: /assets/ruby-warrior.jpg
---

_Originally appeared on [JavaScript January](https://www.javascriptjanuary.com/blog/glitch-youre-not-in-the-matrix), thank you [Emily](https://twitter.com/editingemily)!_

# Intro
Hello, I’m Jessica! Thanks for taking the time to read my post. 🤗 I love my job. Why? Because I get to spend my day building apps, writing about them and then traveling to speak about them. I’m a Developer Advocate at Algolia, a wicked fast hosted search API. Most days I get to work with web applications in JavaScript, and because of that I've come across some really cool things, like [Glitch](https://glitch.com/).

# What even is Glitch
Some of you Javascript nerds may have heard of this thing called Glitch, rumoring around the interwebz the past year. Glitch gives power back to the user for real life examples; it’s an online IDE with the power of collaboration and community features like in GitHub. If you haven’t had a chance to work with it, I highly recommend giving it a try.

![heman_power](https://media.tenor.com/images/036e534a4a45a39713e439f67de3463a/tenor.gif)

The great thing about Glitch is the reduction in time it takes to get a code sample up and running. So many times, you've had an idea for a feature to show someone only to have to send them individual snippets of code that just do _not_ get your point across. Then you're stuck messaging back and forth trying to find a time to screen share just so you can share what you have locally. Even then, the user doesn’t have context for what your example should look like on _their_ computer, so maybe you send them the code base and they fork their own copy and then _ah shit_ they’re using Yarn and you’re using NPM, their version is older than yours, or they don’t have the environment variables you have. Frustrated at having spent way too long on this already, you send them your API keys over a secure channel, but they don’t have .gitignore set up properly so your API keys accidentally get committed into a random GitHub repo. You weep. So do they. No one is having fun.

![willsmith_sobbing](https://78.media.tumblr.com/6d8094ea45e9bb248c03b65ed45636cd/tumblr_nmgfr7FvDt1t9sksvo1_250.gif)

All that changes with Glitch. Because of its online IDE functionality, it allows other Glitch users to see your example code in real time. They can pair with you on the same code without relying on Hangouts, Zoom, Skype, JoinMe or whatever screen sharing software you are using blurring the screen out and losing the person in the process. 

You can see where each user is within the project and it’s easy to collaborate, even if it’s over a messaging platform sans video. 

Amazing. How did we even live before?

Glitch allows you to create unlimited projects, import existing projects from GitHub in one click, and - most fun of all - “Remix” applications. Remixing is a superb ‘clone’-like function which allows users to take what someone has built already, plug in their own keys and build on top of starter templates.

You can check out more about why [Glitch was started](https://blog.fogcreek.com/coding-dinosaurs-newbs-rockstars/) straight from the fish’s mouth.

So now you know _what_ Glitch is, let’s dive into a few things that help with workflow.

# Working locally vs Glitch
A lot of the power that Glitch wields is in getting that initial collaboration up and running, yet, sometimes you just want a little version control and some solid emoji commit messages. Good news for you, Glitch has a nice _import_ and _export_ function you can utilize.

<img src="https://gist.githubusercontent.com/JessicaG/024d85342b16731c0c91cb18ddc82428/raw/b19a39258beef678bc2641229df9fef7fcd5e8db/glitch_import-export-gh.png" width="250" height="425" style="display: block" alt="glitch_import-export-gh">

How you start really depends on your preference for initiating projects. You can kick it off locally and then import your project into Glitch or see what they do out of the box and then export into GitHub. 

However, after this initial project commit, you’ll want to keep a few things in mind for your git workflow. 

## Follow a Git workflow
Glitch is keeping track of all your projects and the version under the hood, however there is currently not an option to revert back. But as developers, let’s face it, we break shit. 

Following git workflow is helpful here when working with Glitch. What I mean by that is, follow the philosophy of having a clean master branch and doing all those lovely ‘wip’ commits in a branch.

Using a good git workflow can help ensure you have less breaking code while working on your Glitch app. 

##  Clean up your branches
It it is always good rule of thumb to get rid of branches that are no longer in use or that have been merged into master already. However, this is particularly important when you are using both the import and export function with Glitch. The way Glitch handles an export into your repo is by creating a branch for you, called “Glitch”. After you export to GitHub and merge your branch into master, you'll want to `git branch -D` that shit. This means that if you have an old "Glitch" branch you didn’t delete, you’re going to have some duplicate code on that branch that you've already merged in mixing in with your new changes. Merge conflicts, le sigh; `git remote prune origin` is your friend. 

Assuming you have cleaned up your branches, you can import and export at will no issues!
<img src="https://gist.githubusercontent.com/JessicaG/024d85342b16731c0c91cb18ddc82428/raw/b19a39258beef678bc2641229df9fef7fcd5e8db/export_glitch_gh.png" alt="export_glitch_to_gh" style="display: block">

_ProTip:_ Currently, you can only export and import from Glitch from your master branch. A way to get around this however, is you can set your project branch on GitHub as upstream to master. 

GitHub GUI has a section where you can easily [set your default branch](https://help.github.com/articles/setting-the-default-branch/) to whatever you would like. BOOM! Easy as pie.

<img src="https://gist.githubusercontent.com/JessicaG/024d85342b16731c0c91cb18ddc82428/raw/6361f6b375a2040c67041fd31fde839c3843342e/gh_screenshot.png" alt="export_glitch_to_gh" width="600" height="300" style="display: block">

Be sure to change this back when you're ready to use master again for your default import and export. 😎

## Project Domain
Glitch uses a handy environment variable `PROJECT_DOMAIN` for all projects. This is super handy for keeping track of the dynamically changing urls when someone remixes a project. Since we don’t have that locally, we can use that as a way to identify when to use an .env file locally or when it’s on Glitch. This helps us not export variables to our bash sessions each time or take up time in our profile. I personally like to use [dotenv](https://www.npmjs.com/package/dotenv) for managing this and keeping a similar .env file locally as I have on Glitch. 

I have this little snippet of code I use in my `server.js` to make sure things do not blow up. These notes let people who are looking at my code or remixing a project, know _why_ I have that and also, a good reminder to me later on if _I_ forget. 😅

```
// only do if not running on glitch
if (!process.env.PROJECT_DOMAIN) {
  // read environment variables (only necessary locally, not on Glitch)
  require('dotenv').config();
}
```

# A good README
Because, _documentation_. As devs, we can often put this to the side. For Glitch, documentation is really important to remember because people will be searching for projects or examples based on a something they want to build. Your project _may_ be in those results, so we want to be kind to each other ([rubyist at heart](https://twitter.com/hashtag/minaswan?lang=en)), and help set up our next person for success by making it clear wtf our project actually does.

# Asking for help
As I mentioned earlier, Glitch is super collaborative and has a great tool to allow users to ask for help on _public_ projects. When you do ask for help, to ensure you get the best response, leave some comments surrounding the line you are asking for help on. When you do ask for help, your request will be shown on the homepage along with your comment/question. However, this is still a new feature and platform, so be patient if you don’t get an answer right away.

<img src="https://gist.githubusercontent.com/JessicaG/024d85342b16731c0c91cb18ddc82428/raw/b19a39258beef678bc2641229df9fef7fcd5e8db/glitch_describe_problem.png" width="250" height="275" alt="glitch_asking_help" style="display: block">

This context within the code base is really helpful for when you’re either mentoring or working through a problem you may have with one or more developers. When you highlight a line you are requesting help on, Glitch auto-tags the languages or frameworks you are using. Let's take this picture for example; I'm in my `server.js` file and on a function with nunjucks and express, so it was automajically tagged with `js`, `nunjucks` and `express`. Saaawwweeeeeeett. This is _super_ helpful off the bat for whomever is coming to look at what you need help on. Maybe someday we'll have filtering on languages on the help homescreen so you can pick up mad js tickets. Hint hint, nudge nudge, Glitch peeps. 😉

# Public vs Private
You have the option to have a public or a private project and what I like to start off with is a private project until I’m closer to finishing. This allows for anyone to not remix your code without a finished project. Keep in mind you won’t be able to request help on a _private_ project, but you can always open it up and close it down if you’re working on some top secret release. You can also invite users to help collaborate when needed on a public or private project.

# Conclusion
So, that’s it! Now you’re ready to go take over the world, one Glitch app at a time! Even if you don’t want to build something; it’s good to go help give back to the community as well. So maybe check out if anyone is asking help when you have some spare time, we can only get stronger as a community.

![jess_yeah](https://media.giphy.com/media/rxyop0115ZpW8/giphy.gif)

Speaking of community, a few personal plugs! 

Thanks again for reading! If you want to see some of things I’m building on Glitch, check out our [Algolia Glitch page](https://glitch.com/algolia)!

I also get the pleasure of working with [Steve Kinney](https://twitter.com/stevekinney) organizing [DinosaurJS](https://twitter.com/dinosaur_js), we are on our THIRD year and I’m so pumped. If you haven’t been before, check it out, we have a good time and maybe you’ll learn a thing or two about this cool language, Javascript.

Come [say hi](https://twitter.com/jessicaewest) 👋 to me on the interwebz, see you around, Dev.to friends!