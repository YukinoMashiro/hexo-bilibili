---
title: about
date: 2018-12-12 22:14:36
keywords: 关于
description:
comments: false
photos: https://cdn.staticaly.com/gh/Yukino831143/CDN@master/img/banner/about.jpg
---

{% raw %}

<!-- 因为vue和botui更新导至bug,现将对话移至js下的botui中配置 -->
<div class="entry-content">
  <div class="moe-mashiro" style="text-align:center; font-size: 50px; margin-bottom: 20px;">[Yukino]</div>
  <div id="hello-mashiro" class="popcontainer" style="min-height: 300px; padding: 2px 6px 4px; background-color: rgba(242, 242, 242, 0.5); border-radius: 10px;">
    <center>
    <p>
    </p>
    <h4>
    与&nbsp;<ruby>
    Yukino&nbsp;<rp>
    （</rp>
    <rt>
    Yukino</rt>
    <rp>
    ）</rp>
    </ruby>
    对话中...</h4>
    <p>
    </p>
    </center>
    <bot-ui></botui>
  </div>
</div>
<script src="/js/botui.js"></script>
<script>
bot_ui_ini()
</script>
{% endraw %}
