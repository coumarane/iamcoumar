# Hugo Framework
https://gohugo.io/

## Install Hugo on macOS
Install via Homebrew
```bash
brew install hugo

# check version
hugo version
```

## Create a New Hugo Site

```bash
hugo new site iamcoumar

cd iamcoumar
```

## Add a Hugo Theme
Example using the popular hugo-clarity theme:
```bash
git init
git submodule add https://github.com/chipzoller/hugo-clarity themes/hugo-clarity
```

Update `hugo.toml`:
```bash
baseURL = "https://iamcoumar.com/"
languageCode = "en-us"
title = "My Blog"
theme = "hugo-clarity"
```

Start Hugo’s development server to view the site.
```bash
hugo server
```

## Create The First Post
New post
```bash
hugo new posts/first-post.md
```

Edit the new post `content/posts/first-post.md` to make it visible:
```
draft: false
```

Example content:
```
+++
date = '2025-05-29T12:13:29+02:00'
draft = false
title = 'Linux Kernel'
+++

Welcome to my Hugo blog!
```

Run the server to see the firt blog:
```bash
hugo server
```

Access the site at:
http://localhost:1313
