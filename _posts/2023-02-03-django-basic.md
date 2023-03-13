---
layout: distill
title: "Django로 웹사이트 만들기"
date: 2023-02-03 16:40:16
description: "파이썬 웹 프레임워크 장고로 프로젝트를 만드는 가장 기본적인 방법"
tags: python
categories: framework back-end
giscus_comments: true
authors:
  - name: Seohee Park
    # url: "https://en.wikipedia.org/wiki/Albert_Einstein"
    # affiliations:
    #   name: IAS, Princeton

toc:
  - name: "Model 생성"
  - name: Migration
  - name: "API사용"
  - name: "URL 설계"
  - name: "View 작성"
  - name: "Template 작성"
---

# Django로 웹사이트 만들기

> python과 Django로 웹사이트를 만드는 가장 기초적인 방법입니다.
> 장고 내부의 template(사용자가 보는 화면)을 활용합니다.

## Model 생성

### Model 설계

```python
# mysite/news/models.py
from django.db import models

class Reporter(models.Model):
    full_name = models.CharField(max_length=70)

    def __str__(self):
        return self.full_name

class Article(models.Model):
    pub_date = models.DateField()
    headline = models.CharField(max_length=200)
    content = models.TextField()
    reporter = models.ForeignKey(Reporter, on_delete=models.CASCADE)

    def __str__(self):
        return self.headline
```

## Migration

### makemigrations 명령어

```python
python manage.py makemigrations
```

테이블이 없을 경우 migrations을 생성

### migrate 명령어

```python
python manage.py migrate
```

마이그레이션을 실행하고 사용자의 데이터베이스에 테이블 생성

## API사용

## Admin으로 DB 관리

관리자 인터페이스는 인증된 사용자가 객체를 추가, 변경 및 삭제할 수 있는 웹사이트로 관리자 페이지에 modes.py를 등록해서 사용할 수 있다.

```python
# mysite/news/models.py
from django.db import models

class Article(models.Model):
    pub_date = models.DateField()
    headline = models.CharField(max_length=200)
    content = models.TextField()
    reporter = models.ForeignKey(Reporter, on_delete=models.CASCADE)
```

```python
# mysite/news/admin.py
from django.contrib import admin

from . import models

admin.site.register(models.Article)
```

## URL 설계

```python
# mysite/news/urls.py
from django.urls import path

from . import views

urlpatterns = [
    path('articles/<int:year>/', views.year_archive),
    path('articles/<int:year>/<int:month>/', views.month_archive),
    path('articles/<int:year>/<int:month>/<int:pk>/', views.article_detail),
]
```

- 장고는 URL 경로들을 파이썬 콜백 함수들 `views`로 연결
- 경로를 나타내는 문자열들은 매개 변수 태그를 사용해 URL로부터 값을 캡처
- 사용자가 페이지를 요청하면 장고는 각 경로를 순서대로 실행하고 요청된 URL과 일치하는 첫 순서에서 정지
- 매치하는 URL이 없다면 404view를 호출
- 일치하는 URL 패턴이 있다면 주어진 뷰를 호출 → 뷰는 파이썬 함수로 각각의 뷰에는 요청 메타데이터가 포함된 요청 개체와 패턴에 잡힌 값이 전달
  - (ex) 사용자가 URL “/articles/2005/05/39323/”로 요청하면 장고는 `news.views.article_detail(request, year=2005, month=5, pk=39323)` 함수 호출

## View 작성

- 모든 뷰는 1. HttpResponse객체를 반환하거나 2. Http404 같은 예외를 발생시킨다.
- 일반적으로 뷰는 파라미터들에 따라 데이터를 가져오며 템플릿을 로드하고 템플릿을 가져온 데이터로 render

```python
# mysite/news/views.py
from django.shortcuts import render

from .models import Article

def year_archive(request, year):
    a_list = Article.objects.filter(pub_date__year=year)
    context = {'year': year, 'article_list': a_list}
    return render(request, 'news/year_archive.html', context)
```

## Template 작성

{% raw %}

```
# mysite/news/templates/news/year_archive.html

{% extends "base.html" %}

{% block title %}Articles for {{ year }}{% endblock %}

{% block content %}
<h1>Articles for {{ year }}</h1>

{% for article in article_list %}
    <p>{{ article.headline }}</p>
    <p>By {{ article.reporter.full_name }}</p>
    <p>Published {{ article.pub_date|date:"F j, Y" }}</p>
{% endfor %}
{% endblock %}
{% endextends %}
```

{% endraw %}

- `{ { } }` 변수 → `{ {article.headline} }`는 ‘article의 headline 속성 값을 출력’이라는 의미
- `.` 속성 조회/사전의 키 조회/인덱스 조회/함수 호출에 사용
- `{ { article.pub_date|date:"F j, Y" } }`에서 파이프(’|’)는 템플릿 필터를 호출하고 변수의 값을 필터링, 여기서는 date필터로 파이썬의 datetime 개체를 지정한 포맷으로 변환
  custom 템플릿도 작성 가능

<!-- <script src="https://giscus.app/client.js"
        data-repo="Joyseohee/joyseohee.github.io"
        data-repo-id="R_kgDOJG0eCg"
        data-category="Announcements"
        data-category-id="DIC_kwDOJG0eCs4CUyOA"
        data-mapping="pathname"
        data-strict="0"
        data-reactions-enabled="1"
        data-emit-metadata="0"
        data-input-position="top"
        data-theme="preferred_color_scheme"
        data-lang="ko"
        crossorigin="anonymous"
        async>
</script> -->
