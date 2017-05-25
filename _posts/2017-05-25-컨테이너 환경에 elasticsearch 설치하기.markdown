---
layout: post
title:  "컨테이너 환경에 elasticsearch 설치하기"
date:   2017-05-25 16:20:00 +0900
categories: elasticsearch
tags: elasticsearch tip install
---
{% raw %}
elasticsearch를 docker나 여타 컨테이너 환경에 설치하려고 하면 오류가 납니다. 설치 중에 커널 파라미터를 변경하려 하기 때문입니다. 그래서 컨테이너 환경에서 elasticsearch를 설치할 때에는 ES_SKIP_SET_KERNEL_PARAMETERS 환경변수를 true로 설정해 주어야 합니다.

예를 들어, apt를 이용해 elasticsearch를 설치하고자 하신다면 아래와 같이 하시면 됩니다.

``` sh
ES_SKIP_SET_KERNEL_PARAMETERS=true apt install elasticsearch
```

### 참고자료
[Install Elasticsearch with Debian Package](https://www.elastic.co/guide/en/elasticsearch/reference/current/deb.html)

{% endraw %}
