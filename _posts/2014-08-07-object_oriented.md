---
layout: post
title: 객체지향 / 절차지향 / 정보은닉 / 캡슐화 / 모듈화
category: blog
tags: [java, oop]
---

### 절차지향 : C언어
 - 데이터와, 그들을 조작하는 절차가 구분됨.
 - 데이터를 중심으로, 각 함수들이 데이터를 조작하고 사용함
 - 하나의 데이터 type에 변화가 생기면? 많은 함수를 수정해야 할 것임

### 객체지향 : C++, Java 등등
 - 객체 : 명백한 한계와 의미를 가지는 사물/개념을 추상화 한 것. 애트리뷰트+메소드
 - 객체(데이터, 기능을 모두 포함)들이 서로 메시지를 주고 받으며 협업함
 - 하나의 데이터 type에 변화가 생기면? 해당 객체만 수정하면 될 것임, 다른 객체들에는 영향이 가지 않음
 - 추상화, 캡슐화, 모듈화, 재사용성, 계층성, 상속성,  다형성(같은 메시지에 대해 다르게 반응하는 것)

### 정보은닉 / 캡슐화
 - 객체간에 데이터를 주고받기 보다는, 기능을 실행해달라고 요청함
 - 외부에서 데이터에 직접 접근할 수 없고, 인터페이스를 통해서만 이용할 수 있음
 - 객체는 인터페이스를 제공하기만 하고, 객체의 내부가 어떻게 구현되었는지는 숨긴다.
 - 외부에서는, 객체의 내부가 어떻게 구현되었는지 알 필요가 없다 (변화시 영향 안받음)

### 모듈화
 - 크고 복잡한 것을, 좀 더 작고 관리가능한 단위로 나누어서 독립적으로 개발하는 것.
 - 모듈간의 결합(Coupling)은 낮고, 응집(Cohesion)은 높게
 - 유지보수, 재사용 용이