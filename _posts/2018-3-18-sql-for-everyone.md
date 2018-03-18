---
layout: post
title:  "비전공자를 위한 SQL"
subtitle:   "비전공자를 위한 SQL"
categories: development
tags: sql
comments: true
---
데이터 사이언스를 공부하기 전, 마케팅/광고를 공부했습니다. 마케터가 배우면 제일 좋을 도구가 GA일까? 엑셀일까? 이것저것 고민하다가 그 당시 내린 결정은 SQL이었습니다. 일단 데이터를 스스로 가지고 올 수 있어야 분석을 할 수 있습니다(개발자들에게 매번 부탁하면 업무의 디펜던시가 개발자에게 걸리니..) 개발을 하지 않은 분들이 조금 쉽게 SQL을 다룰 수 있도록 글을 써볼 예정입니다 
<br/><br/>


# 목차
- "서랍에 있는 연필 중 길이가 10cm 이상인 연필을 찾고 싶은" 경우
- "서랍에 있는 연필 중 길이가 10cm 이상이고 색상이 빨간색인 연필을 찾고 싶은" 경우
- 특정 조건을 갖고 데이터를 추출하고 싶은 경우
- 특정 이벤트를 수행한 유저가 몇 명이고 몇 회 했는지 알고 싶은 경우
- 기간을 1일이 아닌 늘려서 데이터를 뽑고싶은 경우
- GROUP의 결과에 조건을 걸고싶은 경우


SQL은 **Structured Query Language**의 약자로 관계형 데이터베이스 관리 시스템(RDBMS)의 데이터를 관리하기 위해 설계된 특수 목적의 프로그래밍 언어입니다. 그냥 데이터를 뽑아내기 위한 도구라고 생각하면 조금 이해하기 쉬울 것 같습니다<br/><br/>

기본적인 구조는 다음과 같습니다<br/>	

```
SELECT [컬럼 이름]
FROM [테이블 이름]
WHERE [조건]
GROUP BY [그룹화할 컬럼]
HAVING [그룹화한 뒤 조건]
LIMIT [제한할 개수]
```
<br/><br/>
## SELECT / FROM
우선 가장 중심이 되는것은 ```SELECT```와 ```FROM```입니다!  
예를 들면 **"서랍에 있는 연필을 찾고 싶은"** 경우 SQL문으로 변경하면 다음과 같습니다
<br/><br/>

```
SELECT 연필
FROM 서랍
```

SELECT 뒤에는 ```찾고싶은 목적 대상들(Column)을 나열```하면 되고, FROM 뒤에는 ```찾을 대상이 있는 공간(Table)```을 작성하면 됩니다<br/><br/>

## WHERE
만약 위의 예에서 조건을 추가해서 **"서랍에 있는 연필 중 길이가 10cm 이상인 연필을 찾고 싶은"** 경우를 SQL문으로 변경하면 다음과 같습니다

```
SELECT 연필
FROM 서랍
WHERE 길이 >= 10cm
```

<br/><br/>

```조건을 주고싶은 경우엔 WHERE 뒤에``` 작성해주시면 됩니다. WHERE 절은 엑셀 함수에서 IF와 비슷한 느낌이라고 생각하시면 좋을 것 같습니다

조건을 늘려 **"서랍에 있는 연필 중 길이가 10cm 이상이고 색상이 빨간색인 연필을 찾고 싶은"** 경우

```
SELECT 연필
FROM 서랍
WHERE 길이 >= 10 AND 색상 = "빨간색"
```
<br/><br/>

위 쿼리문으로 변경할 수 있습니다. WHERE 뒤에 조건의 순서를 어떻게 하냐에 따라서 결과가 나오는 속도가 다른 경우가 있습니다. 이것은 ```인덱스```와 관련이 있습니다.  
회사의 개발자(Database를 담당하시는)에게 저희 DB 인덱스가 어떻게 되어있는지 알려주세요!라고 물어보시면 알려주실 겁니다! 인덱스를 어떻게 타야하는지는(=WHERE에 어떻게 작성해야 빠른지) 추후 알려드리겠습니다

<br/><br/>
쿼리를 조금 짜보신분들에겐 정말 쉬운 SELECT문이지만, 처음 해보시는 분들을 위해 실습을 해보겠습니다

## 실습 환경
실습 환경은 별도 구성 없이 온라인에서 진행합니다 <br/>
[http://sqlfiddle.com/](http://sqlfiddle.com/)으로 들어가신 후, 왼쪽 창에 아래 내용을 복사 붙여넣기하고 Build Schema 버튼을 눌러주세요!<br/>

```
CREATE TABLE IF NOT EXISTS `user_log` (
  `index` INTEGER NOT NULL AUTO_INCREMENT,
  `user_id` VARCHAR(6) NOT NULL,
  `event` VARCHAR(200) NOT NULL,
  `event_date` date NOT NULL,
  PRIMARY KEY (`index`, `user_id`)
) DEFAULT CHARSET=utf8;
INSERT INTO `user_log` (`user_id`, `event`,`event_date`) VALUES
  ('1', 'login_facebook', '2018-03-12'),
  ('1', 'write_posting', '2018-03-12'),
  ('1', 'write_comment', '2018-03-12'),
  ('1', 'view_posting', '2018-03-12'),
  ('1', 'view_posting', '2018-03-12'),
  ('2', 'login_facebook', '2018-03-12'),
  ('2', 'view_posting', '2018-03-12'),
  ('2', 'view_posting', '2018-03-12'),
  ('2', 'write_comment', '2018-03-12'),
  ('2', 'logout', '2018-03-12'),
  ('2', 'login_facebook', '2018-03-13'),
  ('3', 'login_google', '2018-03-13'),
  ('3', 'write_posting', '2018-03-13'),
  ('3', 'view_posting', '2018-03-13'),
  ('3', 'view_posting', '2018-03-13'),
  ('3', 'purchase_item', '2018-03-15'),
  ('3', 'write_comment', '2018-03-14'),
  ('1', 'view_posting', '2018-03-14'),
  ('4', 'view_posting', '2018-03-14'),
  ('5', 'purchase_item', '2018-03-13');

```
<br/>
제가 임의로 만든 테이블로 이름은 ```user_log```입니다.  
블로그 서비스를 하는 업체의 유저 로그 데이터라고 생각해주세요!   
이 테이블엔 user\_id, event, event\_date가 기록되어 있습니다. 이제 우리는 화면의 우측에 쿼리를 작성한 후, RUN SQL 버튼을 눌러봅시다!!!! 


## 1. "1"번 유저의 모든 이벤트 로그를 확인해보세요
- 날짜는 고려하지 않으셔도 됩니다
- Column : ?
- Table : ?
- WHERE : ?


<br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/>

### 정답

```
SELECT user_id, event, event_date
FROM user_log
WHERE user_id = '1'
```


### 결과

| user_id |          event | event_date |
|---------|----------------|------------|
|       1 | login_facebook | 2018-03-12 |
|       1 |  write_posting | 2018-03-12 |
|       1 |  write_comment | 2018-03-12 |
|       1 |   view_posting | 2018-03-12 |
|       1 |   view_posting | 2018-03-12 |
|       1 |   view_posting | 2018-03-14 |

<br/>
3줄로 원하는 데이터를 뽑아냈습니다! 데이터를 보면 1번 유저는 3월 12일에 페이스북으로 로그인했습니다. 그리고 포스팅을 1번 작성했고 포스팅을 2번 봤습니다. 3월 14일에 포스팅을 1번 봤습니다.  
 
<br/>
위의 결과를 통해 이런 정보를 알 수 있었는데, 여기서 아쉬운 점은 **같은 이벤트가 중복해서 2줄로 나와있는 점**입니다. 위 결과에선 1번 유저가 view_posting을 3월 12일에 2번 했다는 것을 저희가 직접 Count를 해야합니다. 

<br/>
이것을 쿼리로 간단하게 해보겠습니다!

## GROUP BY
여기서 나오는 개념은 ```GROUP BY``` 입니다! GROUP BY [컬럼 이름] 이런 방식으로 사용하는데, 직관적으로 설명하자면 컬럼들을 그룹화한다(aggregate)라고 생각해주세요

바로 코드를 통해 보여드리겠습니다

```
SELECT user_id, event, event_date, COUNT(DISTINCT user_id) AS 'unique', COUNT(user_id) AS 'total'
FROM user_log
WHERE user_id = '1'
GROUP BY user_id, event, event_date
```
<br/>

처음 보는 것들을 설명드리겠습니다

- COUNT
	- 개수를 세주는 친구입니다
	- COUNT(개수를 셀 컬럼) 이런 방식으로 사용합니다
	- COUNT(user\_id)를 하면 user\_id의 개수를 세주는 것입니다
- DISTINCT
	- COUNT(DISTINCT user_id)를 하면 중복을 제외한 고유한 user\_id의 개수를 세주는 것입니다
- AS 'unique'
	-  값을 센 후 이름을 unique로 칭하겠다라는 뜻입니다 

위 쿼리의 결과는 아래와 같습니다

| user_id |          event | event_date | unique | total |
|---------|----------------|------------|--------|-------|
|       1 | login_facebook | 2018-03-12 |      1 |     1 |
|       1 |   view_posting | 2018-03-12 |      1 |     2 |
|       1 |   view_posting | 2018-03-14 |      1 |     1 |
|       1 |  write_comment | 2018-03-12 |      1 |     1 |
|       1 |  write_posting | 2018-03-12 |      1 |     1 |

<br/><br/>

여기서 또 조금 아쉬운 점은 **event_date** 기준으로 정렬되어 있지 않은 점!!! 이건 ```ORDER BY``` 로 해결할 수 있습니다
<br/><br/>

```
SELECT user_id, event, event_date, COUNT(DISTINCT user_id) AS 'unique', COUNT(user_id) AS 'total'
FROM user_log
WHERE user_id = '1'
GROUP BY user_id, event, event_date
ORDER BY event_date
```
<br/>

## ORDER BY
- ORDER BY [컬럼 이름] 으로 사용합니다. 기본 옵션은 오름차순이며 ```DESC```을 붙여주면 내림차순으로 정렬됩니다. ORDER BY event\_date DESC 이런 식으로 하면 최신 날짜부터 정렬됩니다
	
### 결과

| user_id |          event | event_date | unique | total |
|---------|----------------|------------|--------|-------|
|       1 |  write_posting | 2018-03-12 |      1 |     1 |
|       1 |   view_posting | 2018-03-12 |      1 |     2 |
|       1 |  write_comment | 2018-03-12 |      1 |     1 |
|       1 | login_facebook | 2018-03-12 |      1 |     1 |
|       1 |   view_posting | 2018-03-14 |      1 |     1 |	


다시 돌아와서 또 다른 쿼리를 짜볼게요. 아마 데이터를 자주 보는 회사라면 DAU, WAU, MAU라는 말을 들을 수 있을거에요! DAU를 뽑아내는 쿼리를 만들어 봅시다
<br/>

## 2. DAU 뽑기
- DAU의 정의 : Daily Active User로 저희 서비스에서 어떤 이벤트라도 했던 사람을 Active로 정의하겠습니다

<br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/>

### 정답

```
SELECT event_date, COUNT(DISTINCT user_id) AS 'DAU'
FROM user_log
GROUP BY event_date
ORDER BY event_date
```

### 결과

| event_date | DAU |
|------------|-----|
| 2018-03-12 |   2 |
| 2018-03-13 |   3 |
| 2018-03-14 |   3 |
| 2018-03-15 |   1 |


## 2-1. DAU가 2 이상인 날짜 뽑기
- GROUP BY를 통해 나온 값을 조건으로 걸고싶은 경우는 어떻게 해야할까요? 
- 이럴 경우엔 ```HAVING```이란 친구가 나옵니다

## HAVING
- GROUP BY의 바로 아래에 작성해주시면 됩니다
- HAVING 조건
<br/><br/>
그룹화를 하기 전 컬럼에 대한 조건이라면 WHERE, 그룹화를 한 후의 컬럼에 대한 조건은 HAVING을 사용하는 것입니다


### 정답

```
SELECT event_date, COUNT(DISTINCT user_id) AS 'DAU'
FROM user_log
GROUP BY event_date
HAVING DAU >= 2
ORDER BY event_date
```
<br/><br/>
### 결과
| event_date | DAU |
|------------|-----|
| 2018-03-12 |   2 |
| 2018-03-13 |   3 |
| 2018-03-14 |   3 |

