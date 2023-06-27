# Blog

Cliff notes for writing and publishing blog articles.

## Add content

Add a newe page to the site.

```shell
hugo new blog/blog-post.md
```

The file will be created in the `blog` directory. Open that file with your editor and noticed the draft value.

Hugo doesn't publish draft content by default.

```shell
---
title: "Ipython Autoreload"
date: 2023-06-26T23:23:08-06:00
draft: true
---


```

Add some markdown to the body of the post, but don't yet change the `draft` value.

```shell
---
title: "My First Post"
date: 2022-11-20T09:03:20-08:00
draft: true
---
## Introduction

This is **bold** text, and this is *emphasized* text.

Visit the [Hugo](https://gohugo.io) website!
```

## Start the development server to view the site

```shell
hugo server --buildDrafts
hugo server -D
```

## Publish the site

```shell
hugo
```