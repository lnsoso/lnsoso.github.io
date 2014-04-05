---
layout: post
permalink: how-to-fixed-brew-install-git-bug-on-osx-10-6-with-ssd
title: OS X 10.6 with SSD 下 brew install git 失败的问题
author: Alvin
date: !binary |-
  MjAxMS0wNC0yOCAxMDozNTo0OSArMDgwMA==
date_gmt: !binary |-
  MjAxMS0wNC0yOCAwMjozNTo0OSArMDgwMA==
---
几日前把 MB133 的光驱拆掉，在光驱位换上了 SSD 。结果在 brew install git 时发现提示以下错误。

```bash
mv: cannot resolve index.html: /private/tmp/homebrew-UNKNOWN-1.7.4.4-QvlZ/git.html
```

上 homebrew issues 里一查，看到有人和我同样错误，也是因为安装了第二块 SSD 的问题。
<https://github.com/mxcl/homebrew/issues/5113>

只要按以下操作重新设置 homebrew 的临时目录即可解决。

```bash
mkdir $HOME/tmp
export HOMEBREW_TEMP=$HOME/tmp
```

原因很简单，通过 brew doctor 就能看到，OS X 现在不支持跨巻的符号链接。
