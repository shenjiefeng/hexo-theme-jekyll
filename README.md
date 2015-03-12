# Hexo Theme ： Prometheus v0.1

## Install

``` bash
//Prometheus requires Hexo 2.4 and above.
$ git clone https://github.com/pinggod/prometheus.git themes/prometheus
//Prometheus requires hexo-renderer-jade and hexo-renderer-sass
$ npm install hexo-renderer-jade --save
$ npm install hexo-renderer-sass --save
```

## Enable

Modify `theme` setting in `_config.yml` to `prometheus`.

## Update

``` bash
cd themes/prometheus
git pull
```

## Configuration

``` yml
# Header
menu:
  ALISTAPART: /categories/alistapart/
  GIANTS: /categories/giant/
  DOCS: /categories/docs/
  TOOLS: /categories/tools/
  BLOG: /categories/blog/
  ABOUT: http://localhost:4000/2015/01/01/EXP/
  GITHUB: https://github.com/pinggod
# rss: /atom.xml

# Content
excerpt_link: Read More

# Miscellaneous
google_analytics: UA-58564269-1
favicon: /favicon.png
twitter: 
```

## Contribute

- [Issue Tracker](https://github.com/pinggod/prometheus/issues)
- [Source Code](https://github.com/pinggod/prometheus)

## Support

If you are having issues, please let me know.
**MIALTO**: pinggodstudio@gmail.com

## License

MIT
