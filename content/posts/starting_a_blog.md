+++
title="Building a blog"
date=2023-08-04

[taxonomies]
categories = ["General"]
tags = ["blog", "zola"]

[extra]
comments = true
+++

As tradition requires it, this post will do a tour of how this blog is built but also about the rationale behind the blog itself.
As a blog owner I cheated because this is in fact not my first post but who cares ?

So without further ado, let's get started !

# Why ?
The reason is pretty simple, I always liked to share knowledge with others.
Mostly via conferences and some articles posted on my previous companies blogs.

But multiple factors made me reduce this activity:
* I changed job and I don't have the same amount of time to dedicate to this
* I also became a dad thus I have less time out of work to prepare for such events

So If I have less time, and I still want to share knowledge, what can I do ?

* I am not very active on social media (twitter and mastodon), I am more there to look for knowledge. 
  Also, I am not very comfortable with the threads format, and I don't want to be limited by the number of characters.
* I don't have the competency nor the time to work on video contents. I used to stream on Twitch, and may come back to this in the future,
  but It's too time-consuming for me. 

So it leaves me with writing blog posts. Even though a lot is already available on the internet, 
I feel that writing contents for myself can help my future self and maybe others ?
After 10 years in the IT industry working in multiple domains (consulting, gambling, health startup),
I want to share tips, I want to share my experience, I want to share my knowledge and this on multiple subjects: technical and organizational ones.

My current company don't really have a company blog (for now) and I want to keep something for me.
My previous blog posts are still available, but It is a bit sad that I somehow never really had ownership on those.
So let's change that, and build my own blog ! I already have a lot of content In my mind that I want to share.

# How ?
I wanted to build something simple, something that I can easily maintain and host.
That's why I decided to use a static site generator.

## Static site generator
A static site generator is a tool that takes a bunch of files and generate a static website out of them.
So nothing too complex on my side, I can just write content and don't have to bother about what's around it.
I preferably wanted to use a static site generator that uses markdown files, I use markdown a lot in my daily life, and I am very comfortable with it.
Like this our blog is a stateless website that serve HTML files, like in the good old days.

No JS ! No CSS ! No HTML ! No Database ! Just markdown files !

### Zola
Zola is a static site generator, it's comparable with Hugo, Jekyll, Pelican, etc...
It's based on [CommonMark](https://commonmark.org/) a specification for Markdown.

One of the main reason I chose to use Zola instead of the competition is that it's an OSS project written in Rust, with an active community.
I am a Rust enthusiast, I've used the language in the past (not during my day jobs) and I really like it.
Having my site generator written in Rust will allow me to contribute to it if I need to.

To use Zola you only need a static binary, no need to install a runtime like NodeJS or Ruby.
Also, I am not good in all the design things (👋CSS), and Zola has a lot of themes available for free.

This was enough to convince me.

## Hosting
One of the first thing that I thought was that I am not sure how much time I will be able to dedicate to this blog.
My family is my priority and I also maintain an open source project that takes some time. I plan to feed this blog when the occasions arise.
For now, I don't want to invest money if I am not active enough here, So finding something free was one of the criteria.

### Github pages
I chose to use [GitHub pages](https://pages.github.com/) to host this blog.
It is a free hosting solution for static websites, it's very simple to use and has a lot of features.

Why did I choose GitHub pages ?
* I am already familiar with GitHub and it's ecosystem. That's where my OSS projects are versioned.
* I also used GitHub actions in the past, and I am very comfortable with them.
* It's free

# Conclusion
Now you know almost everything behind the technical choices I made.
Piping Zola and GitHub pages together is very simple and documented in Zola.
You can check the source code of this blog [here](https://github.com/ImFlog/Imflog.github.io) 
and the CI/CD pipeline [here](https://github.com/ImFlog/imflog.github.io/blob/main/.github/workflows/build.yml)

Hope you will enjoy reading my future posts, and don't hesitate to comment on them !

Flo.