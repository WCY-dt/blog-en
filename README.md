# Ch3nyang's Blog (English)

## Basic Information

This repository is my personal blog. The blog uses a custom-built Jekyll theme from scratch, mainly for technical articles and personal notes. Updates are irregular, but I try to publish at least one article per month.

👉 [https://blog-en.ch3nyang.top/](https://blog-en.ch3nyang.top/)

## Local Development

You are free to use this blog's theme for your own blog.

Before building, install [Ruby](https://rubyinstaller.org/downloads/) (≥ 3.4.0) and [Jekyll](https://jekyllrb.com/docs/installation/), then install dependencies:

```bash
bundle install
```

Start the local server:

```bash
jekyll serve
```

Then use [`Live Server`](https://marketplace.visualstudio.com/items?itemName=ritwickdey.LiveServer) to preview real-time updates.

Most settings are in the [`_config.yml`](./_config.yml) file, which you can modify according to your needs.

Blog posts are stored in the [`_posts`](./_posts) folder, with the naming format `YYYY-MM-DD-title.md`. The front matter of blog posts should include the following information:

```yaml
layout:     post
title:      "Genshin Impact Gameplay Guide"
date:       2000-01-01 00:00:00 +0800
categories: Gaming // Only one category allowed
tags:       Open-World RPG Genshin // Multiple tags allowed, separated by spaces
summary:    "This article is a Genshin Impact gameplay guide, introducing basic gameplay, character development, resource acquisition, and more to help new players get started with Genshin Impact." // Optional
comments:   false // If set to true, the article will display a comment section; otherwise, it won't
mathjax:    true // Optional, default is false. If set to true, enables math formula support
mermaid:    true // Optional, default is false. If set to true, enables flowchart support
copyrights: Original // If set to "Original", copyright notice will be displayed at the end; otherwise, it won't
draft:      true // Optional, default is false. If set to true, the article won't appear on the homepage
archived:   true // Optional, default is false. If set to true, the article will be marked as archived
```

You may also need to modify the workflow files in the [`.github`](./.github) folder, the website icon [`favicon.svg`](./favicon.svg), and [`CNAME`](./CNAME) to suit your needs.

Images in articles are stored in the [`assets/post/images`](./assets/post/images) folder. To reference images, use relative paths, for example:

```markdown
![Image description](/assets/post/images/image-filename.webp)
```

The [`scripts`](./scripts) folder provides scripts that can help convert images to webp format and automatically identify and clean up unused images. If you need to run the scripts, please install the [webp](https://developers.google.com/speed/webp) tool first.

You can use the test articles in the [`_test`](./_test) folder for testing.

## Development Roadmap

- [x] Custom theme
- [x] Article categories
- [x] Article tags
- [x] Article series
- [x] Code highlighting
- [x] Code copying
- [x] RSS feed
- [x] Responsive design
- [x] SEO optimization
- [x] Copyright notice
- [x] Performance optimization
- [x] Article search
- [x] Related article recommendations
- [x] Comment system
- [x] Table of contents
- [x] Article sharing
- [x] Theme switching
- [x] Math formula support
- [x] Flowchart support
- [x] Accessibility
- [x] Draft system
- [x] Content collapsing
- [x] Article archiving
- [x] Embedded GitHub components
- [x] Image layout plugin
- [x] Article summaries
- [ ] More features...

## Copyright Notice

All **articles** in this blog are licensed under [CC BY-NC-SA 4.0](https://creativecommons.org/licenses/by-nc-sa/4.0/). Please cite the source when reposting.

All other **code** in this blog is licensed under [MIT](https://opensource.org/licenses/MIT).
