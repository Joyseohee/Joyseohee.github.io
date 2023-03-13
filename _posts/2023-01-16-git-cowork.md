---
layout: distill
title: "Github로 협업하기"
date: 2023-01-16 16:40:16
description: "깃으로 협업하려면 알아야 할 기본 흐름"
tags: git github
categories: git
giscus_comments: true
authors:
  - name: Seohee Park
    # url: "https://en.wikipedia.org/wiki/Albert_Einstein"
    # affiliations:
    #   name: IAS, Princeton

toc:
    - name: "기본적인 깃 협업 플로우"
    - name: "응용하기"
    - subsections:
        - name: "1. 기능별로 원격 브랜치 생성하기"
        - name: "2. 개발자별로 원격 브랜치 생성하기"
    - name: "원격 기능/개발자 브랜치 main브랜치에 병합하기"
    - subsections:
        - name: "Pull Reqeusts"
        - name: "Merge"
        - name: "Pull"
---

## 기본적인 깃 협업 플로우

<aside>
🚧 <b>잠깐!</b><br/>
이 포스트는 git 기본 개념을 다루고 있지 않습니다. git에 익숙하지 않다면 <a href="https://joyseohee.github.io/git-basic">Git으로 형상 관리하기</a> 페이지를 참고하시기 바랍니다!
</aside>

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/post_img/2023-01-16-git-cowork/git-small-team-flow.png" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>
<div class="caption">
        소규모 팀 프로젝트(출처: git 공식 사이트)
</div>

1. 각 개발자(Jessica, John)은 서버로부터 `git clone`이라는 명령어로 서버의 내용물을 복제해온다.
2. 이후 각자 PC(로컬 저장소)에서 작업물을 `git commit`하고 서버에 `git push`해 다른 개발자에게 자신의 코드를 공유한다.
3. 이미 로컬에 git 저장소를 복제해 온 개발자(John)는 `git fetch` 명령어로 서버의 코드를 불러올 수 있다.
4. `git merge`로 자신의 코드와 서버에서 불러온 코드를 병합한다. 이 때 충돌이 발생할 수 있다.
5. 충돌을 해결하고 `git push`로 병합(merge)한 코드를 서버에 올려 공유한다.
6. 이를 반복한다.

## 응용하기

### 1. 기능별로 원격 브랜치 생성하기

기본적인 깃 협업 플로우를 따를 시 협업 플로우는 단순해보인다. 하지만 서버에 메인브랜치만 뒀을 때 몇가지 치명적인 단점이 존재한다.

> 1. 메인브랜치에 변화가 잦다.
> 2. 다른 개발자가 올린 코드를 따로 볼 수 없고 오로지 main 브랜치에 병합된 형태로만 공유된다.
> 3. 기능별로 병합할 수 없고 무조건 main 브랜치에 전체 병합한다.

이런 문제를 해결하기 위해 서버에 기능별로 브랜치를 생성할 수 있다.

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-3">
        {% include figure.html path="assets/img/post_img/2023-01-16-git-cowork/git-managed-team-flow.png" class="img-fluid rounded z-depth-1" height="50px" zoomable=true %}
    </div>
</div>
<div class="caption">
        깃-기능별-브랜치(출처: git 공식 사이트)
</div>

1. 각자 PC(로컬 저장소)에서 작업물을 `git commit`하고 서버의 기능A 브랜치(server:featureA)에 push해 다른 개발자에게 자신의 코드를 공유한다.
2. 기능A 브랜치만 확인하고 싶은 개발자(Josie)는 `git fetch` 명령어로 서버의 기능A 브랜치 코드를 불러올 수 있다.
3. `git merge`로 자신의 코드와 서버 기능A 브랜치에서 불러온 코드를 병합한다. 이 때 충돌이 발생할 수 있다.
4. 충돌을 해결하고 `git push`로 병합(merge)한 코드를 서버에 올려 공유한다.
5. 이를 기능별로 반복한다.

### 2. 개발자별로 원격 브랜치 생성하기

소규모 협업 프로젝트를 할 때 간편하게 이용할 수 있는 방법이다. 기능별로 브랜치를 생성하기엔 기능이 적을 때 개발자별 원격 브랜치를 생성하는 편이 실용적일 수 있다. main브랜치와 별도로 개인 원격 브랜치를 두면 다른 팀원의 코드를 확인할 수 있다.

## 원격 기능/개발자 브랜치 main브랜치에 병합하기

위의 방법대로 개발자는 자신의 코드를 다른 개발자들과 공유하고 협업할 수 있다. 그렇다면 나눠둔 브랜치를 배포하기 위한 브랜치인 main브랜치에 합치기 위해 어떤 작업이 필요할까?

### Pull Reqeusts

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/post_img/2023-01-16-git-cowork/git-create-pull-request.png" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>
<div class="caption">
        깃-pullrequest생성1(출처: git 공식 사이트)
</div>

- `Pull Reqeusts`를 생성하면 다른 작업자에게 변경 내용 검토를 요처할 수 있습니다.
- main브랜치에 병합되기 전에 후속 커밋을 추가할 수 있습니다.
- 충돌 관리를 할 수 있습니다.

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/post_img/2023-01-16-git-cowork/git-create-pull-request2.png" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>
<div class="caption">
        깃-pullrequest생성2(출처: git 공식 사이트)
</div>

- 검토할 팀원과 해당 커밋들이 포함될 `project`, `milestone` 등을 설정할 수 있습니다.

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/post_img/2023-01-16-git-cowork/git-resolve-conflict.png" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>
<div class="caption">
        깃-충돌관리1(출처: git 공식 사이트)
</div>

- 만약 충돌이 있을 경우 노란박스로 표시된 Resolve conflicts를 눌러 충돌 관리를 시작합니다.
- 원격에서 충돌 관리하기 불편하다면 로컬 브랜치에서 `git pull origin main`한 뒤 충돌관리를 진행하고 push합니다.
  - 로컬에서 충돌을 관리할 경우 불필요한 commit(원격 브랜치 merge)이 생성되긴 하지만 IDE로 코드를 확인하기 쉽다는 장점이 있습니다.
  - 로컬에서 충돌을 관리할 경우 `Pull Requests` 생성 시 별도의 수정 없이 자동으로 병합을 진행합니다.

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/post_img/2023-01-16-git-cowork/git-resolve-conflict2.png" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>
<div class="caption">
        깃-충돌관리2(출처: git 공식 사이트)
</div>

- `<<<<<<<<<< 내브랜치 ====== 메인브랜치 >>>>>>>>>>>>` 사이의 내용을 수정합니다.

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/post_img/2023-01-16-git-cowork/git-resolve-conflict-save.png" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>
<div class="caption">
      깃-충돌관리-저장(출처: git 공식 사이트)
</div>

- `Mark as Resolved`를 눌러 수정사항을 저장하고 충돌을 해결합니다.

### Merge

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/post_img/2023-01-16-git-cowork/git-merge.png" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>
<div class="caption">
        깃-충돌관리-저장(출처: git 공식 사이트)
</div>

- Merge pull request를 눌러 병합을 완료합니다.

### Pull

위의 단계까지 마쳤다면 서버 작업은 완료된 상태입니다. 여기까지 마치고 나면 로컬보다 서버에 `commit`이 하나 더 앞서 있는 상태인데요. 로컬 서버로 이 `commit`을 `pull`한 뒤 새로운 작업을 시작하면 `git log`를 깔끔하게 유지할 수 있습니다.
