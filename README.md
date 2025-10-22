# Ch3nyang's Blog (English Version)

## Basic Information

This repository is my personal english blog. Update frequency is irregular, aiming for at least one article per month.

👉 [https://blog-en.ch3nyang.top/](https://blog-en.ch3nyang.top/) (part of the content)

Chinese version: [https://blog.ch3nyang.top/](https://blog.ch3nyang.top/)

## Features

The blog is built using the [Jekyll](https://jekyllrb.com/) static site generator, utilizing a fully custom theme called [tangerine](https://github.com/wcy-dt/tangerine), and integrates various practical features:

| Basic Features | Content Organization | User Experience | Enhanced Features | Extension Plugins |
|----------------|---------------------|------------------|-------------------|-------------------|
| Custom Theme | Article Categories | Responsive Design | Code Highlighting | GitHub Plugin |
| RSS Feed | Article Tags | Theme Switching | Code Copy | Image Layout Plugin |
| Comment System | Article Series | Accessibility | Formula Support | iframe Plugin |
| SEO Optimization | Table of Contents | Article Search | Flowchart Support | Result Preview Plugin |
| Performance Optimization | Article Archive | Article Sharing | Content Folding | External Reference Plugin |
|                | Draft System | Copyright Notice | Article Summary | Code Execution Plugin |
|                |             | Article Recommendations | Fullscreen Display |                   |

## Local Development

You are free to use this blog theme for your own blog.

### Installation and Setup

Before building, please install [Ruby](https://rubyinstaller.org/downloads/) (≥ 3.4.0) and [Jekyll](https://jekyllrb.com/docs/installation/), then install dependencies:

```bash
bundle install
```

Start local server:

```bash
jekyll serve
```

Then use [`Live Server`](https://marketplace.visualstudio.com/items?itemName=ritwickdey.LiveServer) to preview with live updates.

Most settings are in the [`_config.yml`](./_config.yml) file, which you can modify according to your needs.

### Post Editing

Blog posts are stored in the [`_posts`](./_posts) folder, with naming format `YYYY-MM-DD-title.md`. Blog post headers should contain the following information:

```yaml
layout:     post
title:      "Genshin Impact Gameplay Guide"
date:       2000-01-01 00:00:00 +0800
categories: Gaming // Only one category allowed
tags:       Open-World RPG Genshin-Impact // Multiple tags separated by spaces
summary:    "This article is a Genshin Impact gameplay guide, introducing basic gameplay, character development, resource acquisition, and more to help new players get started quickly." // Optional
comments:   false // Optional, defaults to true. If set to true, comments section will be displayed; otherwise hidden
mathjax:    true // Optional, defaults to false. If set to true, enables math formula support
mermaid:    true // Optional, defaults to false. If set to true, enables flowchart support
copyrights: Original // Optional, defaults to original. If set to "Original", copyright notice will be displayed at the end; otherwise hidden
draft:      true // Optional, defaults to false. If set to true, article won't appear on homepage
archived:   true // Optional, defaults to false. If set to true, article will be marked as archived
```

You may also need to modify workflow files in the [`.github`](./.github) folder, website icon [`favicon.svg`](./favicon.svg), and [`CNAME`](./CNAME) to suit your needs.

Images in articles are stored in the [`assets/post/images`](./assets/post/images) folder. When referencing images, please use relative paths, for example:

```markdown
![Image description](/assets/post/images/image-filename.webp)
```

The [`scripts`](./scripts) folder provides scripts to help convert images to webp format and automatically identify and clean unused images. If you need to run scripts, please install the [webp](https://developers.google.com/speed/webp) tool first.

You can use test articles in the [`_test`](./_test) folder for testing.

### Plugin System

See [Plugin Testing](./_test/2000-01-02-plugin-testing.md) for details.

## Copyright Notice

All **articles** in this blog are licensed under [CC BY-NC-SA 4.0](https://creativecommons.org/licenses/by-nc-sa/4.0/). Please attribute when reposting.

All other **code** in this blog is licensed under [MIT](https://opensource.org/licenses/MIT).
