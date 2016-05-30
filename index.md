---
layout: default
---

<body>
  <div class="index-wrapper">
    <div class="aside">
      <div class="info-card">
        <h1>onzing</h1>
        <a href="http://weibo.com/f1focus/" target="_blank"><img src="http://www.weibo.com/favicon.ico" alt="" width="25"/></a>
        <a href="https://github.com/y-j" target="_blank"><img src="/images/favicon.ico" alt="" width="25"/></a>
      </div>
      <div id="particles-js"></div>
    </div>

    <div class="index-content">
      <ul class="artical-list">
        {% for post in site.categories.blog %}
        <li>
          <a href="{{ post.url }}" class="title">{{ post.title }}</a>
          <div class="title-desc">{{ post.description }}</div>
        </li>
        {% endfor %}
      </ul>
    </div>
  </div>
</body>

