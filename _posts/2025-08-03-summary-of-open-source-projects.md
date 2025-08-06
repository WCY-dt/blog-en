---
layout: post
title: "How I Made Three 100+ Stars Projects in Half a Year"
date:   2025-08-03 23:00:00 +0800
categories: opensource
tags: open-source
summary: "In the past six months, I planted three 'melons' on GitHub. Surprisingly, each of these melons has garnered over 100 stars! I've distilled my 'star-magnet method' into three parts: Product, Technology, and Promotion."
comments: true
copyrights: 原创
mermaid: true
lang: en
---

Over the past half year (or to be precise, a year), I’ve planted three “melons” on GitHub. To my surprise, these melons turned out to be real overachievers, each snagging more than 100 stars!

Let’s take a look at the melons I’ve planted:

| Project | Screenshot |
| --- | --- |
| {% github_link https://github.com/WCY-dt/EasyTransfer %}<br>A WebRTC-based end-to-end file transfer tool<br>*Launched on 2024-06-19*<br>*Released on 2024-12-02*<br>*172 stars in one day* | <img src="/assets/post/images/opensource1.webp" alt="EasyTransfer" style="max-width:300px;" /> |
| {% github_link https://github.com/WCY-dt/my-github-2024 %}<br>GitHub Year-End Personal Summary<br>*Launched on 2024-12-14*<br>*Released on 2024-12-15*<br>*26 stars in one day* | <img src="/assets/post/images/opensource2.webp" alt="my-github-2024" style="max-width:300px;" /> |
| {% github_link https://github.com/WCY-dt/PongHub %}<br>One-click port status monitoring tool<br>*Launched on 2025-07-20*<br>*Released on 2025-07-24*<br>*53 stars in one day* | <img src="/assets/post/images/opensource3.webp" alt="PongHub" style="max-width:300px;" /> |

The night before launching the third project, I lit some incense, did a bit of fortune-telling, and guess what? I could feel it — 100+ stars were definitely in the bag!

It’s not that I’m getting cocky, but after countless nights of pulling my hair out, I think I’ve... discovered the secret recipe for making personal open-source projects go viral without even trying.

Today, I’m sharing all my secrets and breaking down my “star-magnet method” into three parts: Product, Technology, and Promotion.

## First Move: Product

### Build a project that makes users say "Wow"

If you want heroes to stop by and hit that shiny star button, you just need to scratch an itch they didn’t know they had. Here are four magical ingredients:

- **Solve a pain point**: There’s a special kind of pain in this world called "unmet needs." Solve a problem that no one else has solved before.

  For instance, my own project {% github_link https://github.com/WCY-dt/my-github-2024 %} is the only project that I can find which can generate a year-end summary of your GitHub activity!

- **Ride the trend**: Stay on top of the latest buzz and use popular topics or technologies to grab attention. Your job is to be the trendiest pig in the wind.

  For example, my friend’s {% github_link https://github.com/16131zzzzzzzz/EveryoneNobel %} launched during last year’s Nobel Prize announcement, generating a Nobel Prize post according to the photo users uploaded, right when the hype was peaking.

- **Cutting-edge tech**: Build a tool so powerful that even companies want to use it.

  This happens a lot.

- **Awesome**: Be the knowledge courier, creating “awesome-xxx” lists or tutorials.

  My classmate’s {% github_link https://github.com/Lordog/dive-into-llms %} tutorial is a great example — it has a staggering number of stars.

Pick one of these four potions, and your project will have a solid “starter.”

### Don't Follow the Crowd

Every one of my projects gets hit with the same soul-crushing questions from users:

- *What’s different about this project compared to xxx?*
- *Can you add a feature like xxx?*

So, before diving in, take five minutes to check out what everyone else is doing:

1. What similar open-source/commercial projects exist?
2. What are their strengths and weaknesses?
3. Can my idea fill the gaps?

Then, look in the mirror and ask yourself: **Do I really need to reinvent the f\*\*king wheel?**

Let’s be real: the main reason most open-source projects go unnoticed is that they’re just reinventing the wheel. After all, why would users ditch their trusty old tool and embrace your mediocre backup project?

Also, a quick reminder: our innovations should ideally come from Step 1, not from market research. If your “innovation” comes from research, it’s probably just fluff. Anyone who’s been to grad school knows exactly what I mean.

### Build Projects You Actually Use

Open-source is essentially a labor of love. There’s no KPI, no bonus — just endless Issues and PRs, plus a few difficult-to-handle users.

A lot of project creators, driven by a spark of inspiration, think, “Hey, this idea is going to be huge!” and dive right in. But a month later, their GitHub repo is gathering dust, with the last update stuck at `init commit`.

In my experience, building a *personal-use project* can keep your open-source motivation high. You’ll spot bugs like, “Oh, that’s broken,” or “Hey, I could optimize that,” which will keep you iterating and improving the project over time — eventually attracting users.

## Second Move: Technology

Technology is probably *the least important* part of most open-source projects. Some projects have code that’s barely functional and still get thousands of stars.

I won’t go into the technical details, but here are a few personal tips for my own open-source projects.

### Choosing Your Tech Stack

That said, choosing the right tech stack is still important. You usually have two options:

- You can choose popular tech stacks to build your project, which speeds up development and provides great community support. If you run into issues, there are tons of “old wise men” to help you out. For example:

  - Frontend: Use frameworks like {% github_link https://github.com/facebook/react %}, {% github_link https://github.com/vuejs/core %}, with build tools like {% github_link https://github.com/vitejs/vite %}.
  - Backend: Pick mainstream languages like Python, Java, Golang, or C++, and frameworks like {% github_link https://github.com/nodejs/node %}, {% github_link https://github.com/pallets/flask %}, or {% github_link https://github.com/spring-projects/spring-framework %}.

  I’ve noticed that many indie developers seem to be all-in on {% github_link https://github.com/vercel/next.js %} — probably thanks to Vercel's great marketing.

  The downside of popular technologies is that making it to GitHub Trending is harder than snagging a ticket for Chinese New Year travel, and you need to innovate on the features to get noticed.

- Alternatively, you can go for niche tech stacks, which will naturally attract users interested in those technologies.

  A few years ago, this would be like saying “let’s rewrite everything in Rust”—  
  ***O mighty language, activate!***

  Even now, there are plenty of niche tech stacks to experiment with. For example, I’m currently playing around with {% github_link https://github.com/withastro/astro %} (though it's not as niche as it used to be).

  The downside of niche technologies is that you might run into issues with the stack itself, which means waiting for the community to fix them or fixing them yourself. For instance, I ran into a bug with a project, fixed it, and submitted a PR, but the author took six months to merge it!

### Avoid Time Bombs

At work, we can ~~write code that’s impossible for anyone else to maintain — just to make sure we’re safe if we get laid off~~. But for open-source projects, clean and tidy code makes it easier to iterate, and it also attracts other developers to chip in. However, when it’s just you, the code review process doesn’t happen, and your initial architecture probably won’t be perfect.

Refactoring code is tough in a company, but in a personal project, it’s much easier. For small projects, my advice is to focus on two things:

- Early on, add linters (like eslint, pylint) and integrate them into your CI process (e.g., GitHub Actions). This way, you catch issues early, before you get lost in your own code.

- Periodically evaluate if the project needs a refactor. Key moments to refactor include:

  - As your project grows and you add more features, it’s better to split things into modules.
  - If some complex logic starts getting too messy, break it down into separate functions or classes.
  - If you defined a bunch of global variables or utility functions early on, you’ll eventually want better encapsulation.

Remember: clean code makes your project more appetizing, while messy code will make you quit late-night snacks.

### Please, Stop with the Vibe Coding!

**Don’t Vibe Code! Don’t Vibe Code! Don’t Vibe Code!**

AI-assisted programming is fine, but code produced through “vibe coding” is a disaster. You’re responsible for the final result!

## Third Move: Promotion

### Building Your Community

Just like how you’d use a photoshopped profile picture and claim you’re a top-tier programmer on a dating site, your README is the face of your open-source project. A well-written README grabs attention instantly. On the flip side, projects with a one-liner README are usually closed with no second thought.

But it’s not just about the README. Building a community around your open-source project involves several easily overlooked aspects. Here’s what I do:

- **`LICENSE`**: Provide a clear open-source license so users can legally use and contribute. Add this when you first submit your code.
- **`CONTRIBUTING`**: Offer contribution guidelines to help users know how to get involved.
- **`Issue Template`**: Provide clear templates so users can describe issues or feature requests more effectively.
- **`PR Template`**: Offer a PR template to help contributors submit their code properly.
- **`Code of Conduct`**: Include a code of conduct to ensure a friendly, inclusive community.
- **`SECURITY`**: Provide a security reporting channel to ensure users can safely use your project.

If your project has a frontend or CLI, it’s a good idea to add a screenshot or even a demo. A picture is worth a thousand words, and a demo can win hearts instantly.

### Shout It from the Rooftops

Even the finest wine is wasted in a dark alley, and no matter how good your open-source project is, it needs some promotion. I remember {% github_link https://github.com/yyx990803 %} mentioning that his {% github_link https://github.com/vuejs/core %} project blew up after hitting the top of Hacker News.

It’s tough for individual developers to land on Hacker News' front page, but you can promote your project through multiple channels. Let’s break it down into domestic and international platforms. (*Here comes the real value*)

#### Domestic

There are quite a few open-source promotion platforms in China, though most are ignored by many. Here are the most useful ones:

- [Tech Enthusiast Weekly](https://www.ruanyifeng.com/blog/weekly/): Released weekly, it focuses on tech and open-source projects. You can subscribe via email or RSS. This newsletter has a massive readership, and many of my projects gained attention after appearing in it.
- [Minority Software](https://meta.appinn.net/): This platform showcases fun apps, and commercial projects are welcome. The best part? Users are super passionate and provide useful feedback.
- [ahhhhfs](https://www.ahhhhfs.com/): A blogger known as Sister A. She has a large following, but she only shares projects she finds interesting.
- [The Minority](https://sspai.com/): You probably know this one, but it’s more suited for commercial projects.
- [1Link.Fun](https://1link.fun/): This platform lists 10 projects or articles per week, focusing on quality over quantity. It has a high subscription rate, and the author writes great descriptions for your project.
- [Jike](https://web.okjike.com/): With lots of active users, this platform is great for indie developer projects.
- [V2EX](https://www.v2ex.com/): A famous community, though it’s not what it used to be, and you need an invite code to register.

#### International

I haven’t paid much attention to international platforms, but here are a few worth noting:

- [Hacker News](https://news.ycombinator.com/): Posting a `Show HN` usually gets attention. But be warned, Hacker News users are picky, so your project better be top-notch.
- [Reddit](https://www.reddit.com/): There are many tech-related subreddits, like `r/IMadeThis`, `r/programming`, and more. Just be careful — some subreddits have karma thresholds, and your post might be flagged as spam if you’re not careful.
- [Product Hunt](https://www.producthunt.com/): New products are launched daily, and there’s a voting system. However, Product Hunt isn’t very friendly to Chinese users, and some would say it’s even discriminatory. Check out {% github_link https://github.com/RSSNext/Folo %} for some drama.

## Summary and Some Questions

Here’s a mind map summarizing the three moves for easy reference:

```mermaid
mindmap
  root)Personal<br>Open-Source<br>Projects(
    ((📦<br>Product))
      Innovation<br>Sources
        Pain Points
        Trends
        Tech
        Knowledge Sharing
      Market<br>Research
      ))Build Projects You Actually Use((
    ((🛠️<br>Tech))
      Tech<br>Stack<br>Choices
        Popular
        Niche
      Code<br>Cleanliness
        Lint Tools
        Refactoring
      ))Avoid Vibe Coding!((
    ((📢<br>Operation))
      Community<br>Building
      Online<br>Promotion
        Domestic Platforms
        International Platforms
```

While I’ve mastered the art of “brewing” a 100+ star project, there are still two big questions I’m wrestling with:

1. **How do I keep growing, rather than peaking too soon?** Projects usually get a surge of stars during the big promotion push, but soon after, it’s back to getting no stars for days. How do I keep the momentum going?
2. **How do I attract more contributors, instead of flying solo?** I get the occasional PR, but most of the time, I’m doing all the work myself. How can I ~~trick~~ attract more people to contribute?

These are the questions I’m still pondering, and I’d love to hear your experiences and thoughts.
