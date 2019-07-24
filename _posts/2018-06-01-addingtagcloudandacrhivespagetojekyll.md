---
layout: post
title: "Jekyll에 태그랑 아카이브 페이지 추가하기"
author: "leaveittogod0123"
tags:
- jekyll
- githubpage
- tags
---

# 목표: jekyll 테마로 만든 깃헙블로그에 태그 기능추가하기
### 버전 : mac OS High Sierra 10.13.4

[참조](https://blog.meinside.pe.kr/Adding-tag-cloud-and-archives-page-to-Jekyll/).

참조한 사이트를 그대로 따라했습니다.

## 1. 로컬 깃허브블로그 페이지를 만든다.
tags.md 파일 블로그폴더 루트 디렉토리에 만듭니다.

{% highlight liquid %}
{% raw %}
---
layout: page
permalink: /tags/
---

<ul class="tag-cloud">
{% for tag in site.tags %}
  <li style="font-size: {{ tag | last | size | times: 100 | divided_by: site.tags.size | plus: 70  }}%">
    <a href="#{{ tag | first | slugize }}">
      {{ tag | first }}
    </a>
  </li>
{% endfor %}
</ul>

<div id="archives">
{% for tag in site.tags %}
  <div class="archive-group">
    {% capture tag_name %}{{ tag | first }}{% endcapture %}
    <h3 id="#{{ tag_name | slugize }}">{{ tag_name }}</h3>
    <a name="{{ tag_name | slugize }}"></a>
    {% for post in site.tags[tag_name] %}
    <article class="archive-item">
      <h4><a href="{{ root_url }}{{ post.url }}">{{ post.title }}</a></h4>
    </article>
    {% endfor %}
  </div>
{% endfor %}
</div>

{% endraw %}
{% endhighlight %}

------

## 2. 태그를 포스트에 보여주기
포스트에 추가될 include 파일을 만듭니다.

```
$ touch _includes/post-tags.html
```

아래의 내용으로:

{% highlight liquid %}
{% raw %}

<div class="post-tags">
  Tags:
  {% if post %}
    {% assign tags = post.tags %}
  {% else %}
    {% assign tags = page.tags %}
  {% endif %}
  {% for tag in tags %}
  <a href="/tags/#{{tag|slugize}}">{{tag}}</a>{% unless forloop.last %},{% endunless %}
  {% endfor %}
</div

{% endraw %}
{% endhighlight %}


그리고

```
_layouts/post.html 파일을 오픈해서
```

적당한 곳에 include해줍니다.

{% highlight liquid %}
{% raw %}
<div class="post">
  <div class="post-info">
    ...
  </div>

  <h1 class="post-title"> ...

  {% include post-tags.html %} // <- here

  <div class="post-line"> ...

</div>
{% endraw %}
{% endhighlight %}

------

## 3. 태그 클라우드랑 아카이브를 꾸며줄 css file 생성 기존테마 폴더에 styles.css 파일이 있으면 생성하지말고 사용하세요.

```
$touch styles.css

// 수정 할때는
$vi styles.css
```

{% highlight liquid %}
{% raw %}
/ for tag cloud and archives
.tag-cloud {
  list-style: none;
  padding: 0;
  text-align: justify;
  font-size: 16px;
  li {
    display: inline-block;
    margin: 0 12px 12px 0;
  }
}
#archives {
  padding: 5px;
}
.archive-group {
  margin: 5px;
  border-top: 1px solid #ddd;
}
.archive-item {
  margin-left: 5px;
}
.post-tags {
  text-align: right;
}
{% endraw %}
{% endhighlight %}
------

## 4. 포스팅할때 태그붙이기
이것은 예입니다:

{% highlight liquid %}
{% raw %}
---
layout: post
title: This is an example.
tags:  // <- here you go
- jekyll
- example
published: true
---

This is a post for an example.
{% endraw %}
{% endhighlight %}


------

## 5. 메인화면에 Tags 보여주기
```
_layouts/default.html 들어간 다음
```

{% highlight liquid %}
{% raw %}
...
  <body>
    <nav class="nav">
        ...
        <ul>
          <li><a href="{{ ... }}">About</a></li>
          <li><a href="{{ site.baseurl }}/tags">Tags</a></li>  <!-- <-here yo go -->
          <li><a href="{{ ...}}/">Posts</a></li>
        </ul>
      </div>
    </nav>

    <footer>
      ...
    </footer>
  </body>
{% endraw %}
{% endhighlight %}
