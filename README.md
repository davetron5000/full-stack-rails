# full-stack-rails.com

This is the source for the website for my book “Rails, Angular, Postgres, and Bootstrap: Powerful, Effective, and Efficient Full-Stack Web Development”.

It's also a bit of an experiment in how compact and small I could make a single-page site.

## Setup

```
bundle install
npm install
rake
foreman start
```

Then navigate to http://localhost:4000

## Editing

* `html/index.html` is the source markup.
* `css/styles.css` is the source CSS.

This uses [BassCSS](http://www.basscss.com/) for most of its styling.  I've tried to add styles only when needed and in the spirit of that
framework.

When you run `rake`, this will:

* Consolidate all CSS into one file
* Use [uncss](https://github.com/giakki/uncss) to remove any bits of CSS that aren't in use
* Minify the remaining CSS
* Insert that CSS into a `<style>` tag in the HTML
* Minify the HTML

The source is about 25K, but after the above process, it's 10K.  Not bad.
