[![Build Status](https://travis-ci.org/hbahuguna/hbahuguna.github.io.svg?branch=master)](https://travis-ci.org/hbahuguna/hbahuguna.github.io)

Welcome to my blog: [https://hbahuguna.github.io/](https://hbahuguna.github.io/)

It is is built with Jekyll, GitHub Pages, and the [Jalpc Theme](https://github.com/jarrekk/Jalpc)
(Check out Jalpc for more instructions).

### How I publish post

git clone this repository locally to my macbook.

Make sure git repository has the appropriate remote:

Use an IDE (I use Webstorm, though Sublime text will also do) and a terminal (e.g. iterm2, or Mac Terminal).

Create a new blog post:

- Got to `/_posts`
- Create a markdown file with a name like this: `yyyy-mm-dd-hello-this-is-my-title.md`
- Create a meta block at the top (so called "front matters"). I usually just copy and paste from older posts and tweak.
- Write the post in markdown syntax
- Save post

To preview post:

```
jekyll serve
```

This will serve the blog post locally at [http://127.0.0.1:4000/](http://127.0.0.1:4000/)

Ready to push to [https://hbahuguna.github.io/](https://hbahuguna.github.io/)?

Easy, just do push master to origin:

```
git push origin master
```

(or just `git push`). The actual GitHub pages [https://hbahuguna.github.io/] may take a few seconds to build / update.

### How you may create a blog like this:

Again, please check out the [Jalpc Theme](https://github.com/jarrekk/Jalpc)) - it has all the instructions
on how to download the base repository and tweak. This is how I got started.

Cheers,
Himanshu
