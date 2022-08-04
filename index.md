---
title: 온라인 호스팅 지침
permalink: index.html
layout: home
---

# 콘텐츠 디렉터리

랩을 완료하는 데 필요한 파일은 [여기에서 다운로드](https://github.com/MicrosoftLearning/AZ-304KO-Microsoft-Azure-Architect-Design/archive/master.zip)할 수 있습니다.

각 랩 연습의 하이퍼링크는 아래에 나열되어 있습니다.

## 랩

{% assign labs = site.pages | where_exp:"page", "page.url contains '/Instructions/Labs'" %}
| 모듈 | 랩 |
| --- | --- | 
{% for activity in labs  %}| {{ activity.lab.module }} | [{{ activity.lab.title }}{% if activity.lab.type %} - {{ activity.lab.type }}{% endif %}]({{ site.github.url }}{{ activity.url }}) |
{% endfor %}

