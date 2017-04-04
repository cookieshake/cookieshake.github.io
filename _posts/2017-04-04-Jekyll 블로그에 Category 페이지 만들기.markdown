---
layout: post
title:  "Jekyll 블로그에 카테고리 페이지 만들기"
date:   2017-04-04 14:31:00 +0900
categories: 팁
tags: jekyll 팁 vue.js axios
---
{% raw %}
Jekyll은 정적인 웹페이지를 만들어냅니다. 그래서 검색이나 카테고리 모아보기 페이지와 같은, 이용자의 요청을 받아 처리하는 페이지를 만들기가 어렵습니다. 하지만 그렇다고 불가능한 것은 아닙니다.

### 1. Jekyll 플러그인 설치하기

Jekyll 생태계에는 많은 플러그인들이 있습니다. 그 중에는 카테고리 페이지를 만들기 위한 플러그인도 있습니다. 하지만 이 블로그가 올라가 있는 Github Page에서는, 보안상의 이유로 플러그인을 사용할 수가 없습니다.

Github Page에서 플러그인을 이용하기 위해서는 git 브랜치를 이용해야 합니다.  현재 사용하는 Jekyll 폴더를 develop 브랜치로 옮기고 블로그를 빌드한 후, Jekyll이 생성한 \_pages 폴더 내의 내용물을 master 브랜치로 옮기면 됩니다.

하지만 저는 이 방법을 사용하지 않을 것이므로, 이 글에서 다루지 않겠습니다. 이 방법을 사용하고 싶으신 분은 "[Github Pages에 Jekyll custom plugin 적용하기](http://gumpcha.github.io/blog/github-pages-with-jekyll-custom-plugin/)"를 참고해주세요.

### 2. 카테고리 별로 HTML 파일 만들기

Jekyll의 [site.categories](http://jekyllrb-ko.github.io/docs/variables/)는 블로그 내의 모든 카테고리와 글들에 대한 정보를 갖고 있습니다. 이 정보를 이용하면  카테고리와 관련된 내용을 표시하는 HTML 파일을 만들 수 있을 겁니다. 각각의 카테고리마다 HTML 파일을 생성 후 원하는 곳에 링크를 걸면 됩니다.

``` html
<!-- /category/[카테고리이름].html -->
---
layout: [사용할_레이아웃]
---
[카테고리이름]에 포함된 글들:
<ul>
	{% for post in site.categories.[카테고리이름] %}
	<li><a href="{{ post.url }}">{{ post.title }}</a></li>
	{% endfor %}
</ul>
```

위와 같은 내용의 HTML 파일을 ~~번거롭게도~~ 카테고리 이름마다 작성하신 후, 필요한 곳에 적당히 링크하셔서 사용하시면 됩니다.

### 3. vue.js를 이용해 카테고리 페이지 만들기

2번 방법은 카테고리가 추가될 때마다 카테고리 페이지를 만들어야 하는 단점이 있습니다. 글 쓰는 것보단 안 귀찮겠지만, 나중의 귀찮음을 대비해서 자동으로 생성되는 카테고리 페이지를 만들어 봅시다.

먼저 블로그 내의 글들을 모두 모아놓은 통짜 파일이 필요합니다. 나중에 편하게 불러올 수 있도록 JSON 형식으로 작성하겠습니다.

``` html
<!-- /posts.json -->
---
---
{% capture json %}
{
  "posts": [
    {% for post in site.posts %}
    {
      "title": "{{ post.title }}",
      "date": "{{ post.date | date: "%Y-%m-%d"}}",
      "categories": {{ post.categories | jsonify }},
      "url": "{{ post.url }}"
    }
    {% unless forloop.last %},{% endunless %}
    {% endfor -%}
  ]
}
{% endcapture %}
{{ json | strip_newlines | normalize_whitespace }}
```

위와 같이 생긴 posts.json 파일을 생성합시다. 위의 파일을 만들고 http://<블로그\_주소>/posts.json으로 접속하면 다음과 같은 형식의 JSON 데이터를 얻게 됩니다.

``` json
{
  "posts": [
    {
      "title": "welcome",
      "date": "2017-04-03",
      "categories": [
        "jekyll"
      ],
      "url": "/posts/welcome"
    }
  ]
}
```

JSON 데이터를 받아와 화면에 표시하기 위해서는 다른 도구들의 도움을 받아야 합니다. 저는 vue.js와 axios의 도움을 받기로 했습니다. 블로그 레이아웃의 header에 다음을 추가해주세요.

> *주의: vue.js와 axios는 인터넷 익스플로러 및 구형 브라우저에 대한 호환성이 없습니다. 모든 브라우저에서 기능이 동작하길 바라신다면 다른 방법을 사용하셔야 합니다.*

``` html
<script src="https://cdnjs.cloudflare.com/ajax/libs/vue/2.2.6/vue.min.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/axios/0.16.0/axios.min.js"></script>
```

다음으로 카테고리 페이지 HTML을 작성하겠습니다.

``` html
---
layout: default
permalink: /category
---
{% assign esc_title="{{ p.title }}" %}
{% assign esc_date="{{ p.date }}" %}
{% assign esc_ctgname="{{ hash }}" %}

<div id="post-list">
  <h2 class="title is-2">"{{ esc_ctgname }}" 카테고리에 있는 글</h2>
  <ul>
    <li v-for="p in posts" v-if="p.categories.indexOf(hash) != -1">
      <a v-bind:href="p.url">{{ esc_title }}</a> <small>{{ esc_date }}</small>
    </li>
  </ul>
</div>

<script>
var hash = decodeURI(window.location.hash.substr(1));

var postList = new Vue({
  el: '#post-list',
  data: {
    posts: []
  }
});

axios.get('/posts.json')
  .then(function (response) {
    postList.posts = response.data.posts;
  })
  .catch(function (error) {
    console.log(error);
  });
</script>
```

이렇게 하면 axios가 post.json에서 블로그 글 정보를 받아와, vue.js가 그 정보를 바탕으로 페이지를 그려내게 됩니다. 이제 http://<블로그\_주소>/category[#](http://devbox.tistory.com/entry/%E3%84%B4%E3%85%87)[카테고리이름] 으로 접속하시면 블로그 내의 글 중 해당 카테고리에 속한 글들만 모아 보실 수 있습니다.

현재(2017년 4월) 이 블로그는 이 방식으로 카테고리 페이지를 생성하고 있습니다. 글이 적을 때에는 이 방법도 잘 동작하지만, 글의 갯수가 많아질수록 posts.json이 커지므로 비효율적일 수 있습니다. 그럴 때는 ~~DB를 사용한다던지~~ posts.json을 여러개로 분할하는 등 적절한 방법으로 최적화를 해주시면 될 것 같습니다.

### 참고자료
[Fully Functional Jekyll Blog](https://www.sitepoint.com/fully-functional-jekyll-blog/)
[지킬 블로그에 페이지네이션과 태그 등 추가](https://nolboo.kim/blog/2014/01/09/upgrade-jekyll-github-blog/)

{% endraw %}
