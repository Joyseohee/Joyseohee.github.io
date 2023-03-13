---
layout: distill
title: "RESTful한 API 개발하기"
date: 2023-02-02 16:40:16
description: "REST란 무엇인가?"
tags: api
categories: back-end
giscus_comments: true
authors:
  - name: Seohee Park
    # url: "https://en.wikipedia.org/wiki/Albert_Einstein"
    # affiliations:
    #   name: IAS, Princeton

toc:
    - name: "REST(REpresentational State Transfer)"
    - subsections:
        - name: "리소스 중심의 API 구성"
        - name: "직관적인 웹 API"
        - name: "심플한 URI"
        - name: "HTTP 메서드를 기준으로 기본 작업 정의"
        - name: "요청/응답 형식 지정"
        - name: "HTTP 메서드별 다양한 상태 코드"
---

## REST(REpresentational State Transfer)

- 아키텍처 스타일. 프로토콜에 독립적 → 일반적인 rest 구현에서 http를 사용
    
    > **디자인 원칙**
    > 
    > 1. 리소스 중심 디자인
    > 2. 클라이언트에서 액세스할 수 있는 모든 종류의 개체/서비스가 리소스에 포함
    > 3. 리소스마다 해당 리소스를 고유하게 식별하는 식별자 → https://my-trip.com/trips/1/
    > 4. 요청/응답 포맷으로 흔히 JSON을 사용
    > 5. 균일한(uniform) 인터페이스 적용 → 리소스에 표준 HTTP 동사(GET, POST, PUT, PATCH, DELETE)를 적용

**참고**
[웹 API 디자인 모범 사례 - Azure Architecture Center](https://learn.microsoft.com/ko-kr/azure/architecture/best-practices/api-design)

### 리소스 중심의 API 구성

- `/orders/`로 **post** 요청 → O
- `/create-orders/`로 **post** 요청 → X

### 직관적인 웹 API

- `/customers/` → 고객 컬렉션
- `/customers/5` → PK가 5인 고객
- `/customers/5/orders` → 고객 5에 대한 모든 주문
- `/orders/99/customers/` → 주문 99의 고객

### 심플한 URI

- `/customers/1/orders/99/products` → X
- `/customers/1/orders/`로 고객1의 모든 주문을 찾은 뒤
- `orders/99/products`로 변경해 처리

### HTTP 메서드를 기준으로 기본 작업 정의

- **GET** : 조회(Read), 리소스의 표현, 응답 본문에 리소스의 세부 정보
- **POST** : 생성(Create), 응답 본문에 새 리소스의 세부 정보를 제공 →멱등성(idempotent) 미보장
- **PUT** : 수정(Update), 기존 리소스 속성 전부 대체 → 멱등성 반드시 보장
- **PATCH** : 수정(Update), 기존 리소스 속성 부분 대체 → 멱등성 미보장
- **DELETE** : 제거(Delete)
<br/>

| 리소스 | POST | GET | PUT | DELETE |
| --- | --- | --- | --- | --- |
| /posts/ | 새 포스팅 만들기 | 모든 포스팅 목록 | 포스팅 대량 업데이트 | 모든 포스팅 삭제 |
| /posts/1/ | 오류(없는 설계) | 포스팅 1에 대한 내용 | 포스팅 1의 정보 갱신 | 포스팅 1 삭제 |
| /posts/1/commets/ | 포스팅 1의 새 댓글 만들기 | 포스팅 1에 대한 모든 댓글 목록 | 포스팅 1의 댓글 대량 업데이트 | 포스팅 1의 모든 댓글 삭제 |

### 요청/응답 형식 지정

- 요청 : Content-Type 헤더
- 요청 시 처리를 원하는 형식 지정하면 서버에서 이 형식으로 응답
- 서버에서 해당 형식을 지원하지 않으면 HTTP 상태 코드 415(지원하지 않는 미디어 유형) 반환

### HTTP 메서드별 다양한 상태 코드

- **GET** : 200(OK), 404(Not Found)
- **POST** : 201(Created), 새 리소스의 URI는 응답의 Location 헤더에, 204(내용 없음), 400(잘못된 요청)
- **PUT** : 200(OK), 204(내용 없음),409(충돌)
- **DELETE** : 204(추가 정보 포함되지 않았다는 의미, 성공), 404(리소스 없음)
- **비동기 작업** : 작업 완료에 시간이 오래 걸릴 경우, 다른 Task Queue를 통해 비동기 처리 가능, 요청이 수락됐지만 아직 완료되기 전임을 알리는 202(수락됨) 응답, 클라이언트가 이 작업을 Polling을 통해 모니터링 할 수 있도록, 비동기 요청 상태를 반환하는 URI를 Location 헤더로 반환

