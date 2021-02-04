---
layout: post
title: jekyll-paginate
category: root
summary: 'jekyll--分页插件的使用'
author: hjy
---

### 分页

* 1、需要在 gems 中安装 jekyll-paginate 插件，并添加到你的 Gemfile 和 _config.yml 中：

* 2、开启分页功能， 只需要在 _config.yml 里边加一行，指明每页该展示多少项目：

```javascript
  paginate: 5
```

这个数字应当是你希望在生成的站点中每页展示博客数目的最大值。

* 3、你可能还需要指定分页页面的目标路径：

```javascript
  paginate_path: "/blog/page:num" // blog为baseurl
```

`blog/index.html` 将会读取这个设置，把它传给每个分页页面，然后从第 2 页开始输出到 `blog/page:num`, `:num` 是页码。如果有 12 篇文章并且做如下配置 `paginate: 5`, Jekyll 会将前 5 篇文章写入 `blog/index.html`，把接下来的 5 篇文章写入 `blog/page2/index.html`，最后 2 篇写入 `blog/page3/index.html。`

* 4、完整分页代码：
{% raw %}
```javascript
  <!-- Pagination links -->
  {%- if paginator.total_pages > 1 -%}
    <div class="pagination">
      {% if paginator.previous_page %}
        <a href="{{ paginator.previous_page_path | prepend: site.baseurl | replace: '//', '/'  }}" class="pagination-item previous">
          &lt;
        </a>
      {% else %}
        <span class="pagination-item previous-disabled">&lt;</span>
      {% endif %}
      <ul class="pagination-pager">
        {% for page in (1..paginator.total_pages) %}
          <li class="pagination-item">
            {% if page == paginator.page %}
              <span class="pagination-item-current">{{ page }}</span>
            {% elsif page == 1 %}
              <a href="{{ paginator.previous_page_path | prepend: site.baseurl | replace: '//', '/' }}" >{{ page }}</a>
            {% else %}
              <a href="{{ site.paginate_path | prepend: site.baseurl | replace: '//', '/' | replace: ':num', page }}">{{ page }}</a>
            {% endif %}
          </li>
        {% endfor %}
      </ul>
      {% if paginator.next_page %}
        <a href="{{ paginator.next_page_path | prepend: site.baseurl | replace: '//', '/'  }}" class="pagination-item next">&gt;</a>
      {% else %}
        <span class="pagination-item next-disabled">&gt;</span>
      {% endif %}
    </div>
  {%- endif -%}
```
{% endraw %}

* 5、更多的分页信息配置，查看[文档](https://jekyllrb.com/docs/pagination/)

* END

