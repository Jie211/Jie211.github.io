---
title: Move to Hexo
date: 2016-09-14 01:50:33
tags:
  - hexo
  - plugin
categories:
  - blog
keywords: hexo, plugin
description: blog move to Hexo-base
---
ブログ自体をHexoに移行しました，設定関連をメモしました．
<!-- more -->

<!-- toc -->

# hexo

Hexoはjavascriptで開発したから，npmでパッケージをインストールできる．

Hexoのインストールは簡単，[HP](https://hexo.io)を参照してやればいい．

themeは簡単の方が好きなので，[appollo](https://github.com/pinggod/hexo-theme-apollo)を使用します．

:duck_good:

<div class="tip">
themeのDependentを忘れないで

{% vimhl sh %}
hexo-renderer-jade hexo-generator-feed hexo-generator-sitemap hexo-browsersync hexo-generator-archive
{% endvimhl %}
</div>


themeの設定はthemes/apollo/_config.ymlに書きます．apolloはdisqusとgoogle-analytics機能もサポートします．

ブログ全体の設定は/_config.ymlに書きます，特にいじる必要がない．

# plugin

1. hexo-ruby-character

  support Ruby character tag for Hexo. [Github](https://github.com/JamesPan/hexo-ruby-character)

  {% ruby Batman|Superman %}

  {% ruby 好开心|累成狗 %}

  {% ruby 漢字|カンジ %}

2. hexo-tag-katex

  Latex(katex[^1]) support in Hexo.[Github](https://github.com/iamprasad88/hexo-tag-katex)

  {% katex %}
  a^{b^{\sqrt{c}}}
  {% endkatex %}

3. hexo-tag-vimhighlight

  syntax highlight by using vim syntax highlight color.[Github](https://github.com/ppwwyyxx/hexo-tag-vimhighlight)

  {% vimhl c %}
  #include "header.h"

    void
  gmres_CRS(double *val,
      const int *col,
      const int rs)
  {
    int i,j,k;
    FILE *p_x,*p_his,*p_loop;
    double *rvec, *axvec,
    int count = 0;
    /* #ifdef TIME */
    fclose(p_x);//中文
  fclose(p_his);//日本語
    fclose(p_loop);
  }
  {% endvimhl %}

4. hexo-footnotes

  support footnotes.[Github](https://github.com/LouisBarranqueiro/hexo-footnotes)

{% instagram _BbVNMvlgv %}

{% flickr 10222376893 z %}

{% twitter https://twitter.com/jie211/status/793375352890400768 %}

{% soundcloud fuckcalo/calo-dangerous 166 false ffff00 false false false false false %}

[^1]: Fast math typesetting for the web[Github](https://github.com/Khan/KaTeX)
