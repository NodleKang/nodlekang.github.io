---
layout: single
title: "[ElasticSearch] 데이터 타입"
date: 2025-01-06 11:00:00 +0900
categories: 
  - ElasticSearch
tag: 
  - ElasticSearch
toc: true
toc_label: 목차
toc_sticky: true
---

본 포스트는 ElasticSearch 매핑 정의에 알아둬야 할 내용을 정리한 포스트입니다.

# Mapping

ElasticSearch에서 데이터를 색인 처리 할 때, 데이터가 어디에 어떻게 저장될지를 결정하는 설정을 매핑이라고 합니다.

RDB의 schema에 대응되는 개념이라고 볼 수도 있습니다.

ElasticSearch는 동적 매핑(dynamic mapping)을 합니다. 

즉, 매핑을 명시하지 않아도 데이터를 보고 필드를 자동으로 감지해서 추가하고 매핑 정보가 자동으로 생성됩니다.

이런 자동 매핑 방식은 실수로 잘못된 타입이 지정될 경우, 수정할 방법이 없기 때문에 주의해야 합니다.

# 메타데이터 필드

ElasticSearch는 "_index", "_id", "_source"와 같은 도큐먼트를 관리하는 데 사용되는 필드들이 있습니다.

RDB 였으면 '테이블 스키마'라고 했을텐데, ElasticSearch에서는 그런 정보들을 메타데이터 필드라고 합니다.
메타 데이터 필드들은 다음과 같습니다.

# 데이터타입

## 문자열 타입 (Keyword, Text)

Keyword와 Text는 문자열 데이터를 담는 매우 중요한 타입들입니다.

둘 모두 텍스트 데이터를 담는데, 용도에 따라서 각각 다른 타입을 선택해야 합니다.

* **Keyword**
  * 별도의 분석기를 거치지 않고 **원문 그대로** 색인합니다.
  * 공통 코드나 키워드 등 정형화된 컨텐츠에 주로 사용됩니다.
  * 주로 집계, 정렬 등에 사용할 필드에서 사용됩니다.
  * 이 타입으로 설정된 필드를 검색할 때는 Query DSL에서 term 조건으로 주로 사용됩니다. 
* **Text**
  * ElasticSearch를 **검색 엔진으로 사용**할 때에 이용되는 타입입니다.
  * 전문검색(Full text search) 기능을 위한 데이터 타입으로, 데이터를 색인하면 전체 텍스트가 토큰화되어서 생성되어 검색하려는 단어와 최대한 관련된 검색을 하기가 용이해집니다.
  * 이 타입으로 설정된 필드를 검색할 때는 Query DSL에서 match 조건으로 주로 사용됩니다.

## 숫자 타입

ElasticSearch는 숫자 데이터가 입력될 때에 명시된 필드 타입이 없으면 정수는 long 타입으로, 실수는 double 타입이 매핑됩니다.

| 타입 | 설명 |
|:---:|:---|
| long	| 최소값과 최대값을 가지는 부호가 있는 64비트 정수, -2^63 ~ 2^63-1 |
| integer	| 최소값과 최대값을 가지는 부호가 있는 32비트 정수, -2^31 ~ 2^31-1 |
| short	| 최소값과 최대값을 가지는 부호가 있는 16비트 정수, -32768 ~ 32767 |
| byte	| 최소값과 최대값을 가지는 부호가 있는 8비트 정수, -128 ~ 127 |
| double	| 64비트 부동 소수점을 갖는 수 |
| float	| 32비트 부동 소수점을 갖는 수 |
| harf_float	| 16비트 부동 소수점을 갖는 수 |

## 날짜 타입

JSON 형식의 데이터에서 날짜 타입은 문자열로 처리됩니다. 즉, 필드가 날짜 타입이라고 알려주는 지표가 없습니다.

그래서 ElasticSearch는 데이터가 ISO8601 형식에 맞춰 입력되면 날짜 타입으로 **유추**합니다.

예를 들어, 아래와 같은 형식이라면 elasticsearch는 이 필드는 date 타입이라고 추론합니다.

```
{ "{날짜컬럼}": "2015-01-01" }
{ "{날짜컬럼}": "2015-01-01T12:10:30Z" }
```

날짜는 다양하게 표현될 수 있기 때문에 날짜 문자열 형식은 명시적으로 설정할 수 있습니다.

만약에 별도의 형식을 지정하지 않으면 기본적으로 "yyyy-MM-ddTHH:mm:ssZ" 형식으로 지정됩니다.

다만, 미리 지정한다면 Integer, Long 타입을 날짜 필드로 표현도 가능합니다. (실제는 내부에서 모두 long 타입의 epoch_millis로 저장합니다.)

* Integer 타입: 1970-01-01 00:00:00부터 초 단위로 계산한 값, epoch_second 형태
* Long 타입: 1970-01-01 00:00:00부터 밀리초 단위로 계산한 값, epoch_millis 형태

```
// 다음은 날짜를 받는 필드를 아래 형태로 입력 받도록 지정하는 예입니다.
// "2019-06-10 12:10:30" 혹은 "2019/06/10" 혹은 "epoch_millis"
curl -XPUT http://{es_host}:{es_port}/{인덱스이름} -d'
{
        "mappings": {
                "properties": {
                        "{날짜컬럼}": {
                                "type": "date",
                                "format": "yyyy-MM-dd HH:mm:ss||yyyy-MM-dd||epoch_millis"
                        }
                }
        }
}'
```

날짜 타입 포멧으로 사용할 수 있는 심볼 기호는 아래와 같습니다.

| 심볼 | 의미 | 예: 2019-09-12T17:13:17.428+09:00 |
|:---:|:---:|:---|
| yyyy	| 연도	| 2019 |
| MM	| 월 - 숫자	| 09 |
| MMM	| 월 - 문자(3자리)	| Sep |
| MMMM	| 월 - 문자(전체)	| September |
| dd	| 일	| 12 |
| a	| 오전/오후	| PM |
| HH	| 시각(0~23)	| 17 |
| kk	| 시각(01~24)	| 17 |
| hh	| 시각(01~12)	| 05 |
| h	| 시각(1~12)	| 5 |
| mm	| 분(00~59)	| 13 |
| m	| 분(0~59)	| 13 |
| ss	| 초(00~59)	| 07 |
| s	| 초(0~59)	| 7 |
| SSS	| 밀리초	| 428 |
| Z	| 타임존	| +0900 / +09:00 |
| e	| 요일(숫자 1:월 ~ 7:일)	| 4 |
| E	| 요일(텍스트)	| Thu |

## Object 타입

ElasticSearch에서 Object 타입이라는 건 매우 모호해서 내부적으로 본다면 **Object 타입은 없다**고 생각할 수 있습니다.

아래와 같은 데이터를 예를 들어 보겠습니다.
```
{
    "region": "US",
    "manager": {
        "age": 30,
        "name": {
            "first": "John",
            "last": "Smith"
        }
    }
}
```

이 데이터가 ElasticSearch 내부에는 아래와 같이 저장됩니다.

```
{
    "region": "US",
    "manager.age": 30,
    "manager.name.first": "John",
    "manager.name.last": "Smith"
}
```

데이터가 저장되는 형식을 고려한다면  **Object 타입은 없다**고 생각할 수 있습니다.

## Nested 타입

Nested 타입은 Object의 배열을 색인하기 위한 특수 타입이라고 보면 됩니다.

아래와 같은 데이터를 ElasticSearch에서는 어떻게 저장해야 할까요?

```
{
    "group": "fans",
    "user": [
        {
            "first": "John",
            "last": "Smith"
        },
        {
            "first": "Alice",
            "last": "White"
        }
    ]
}
```

user.first가 여러 개 있으니 아래와 같이 저장될 것 같습니다.

```
{
    "group": "fans",
    "user.first": [ "alice", "john" ],
    "user.last": [ "smith", "white" ]
}
```

하지만 위와 같이 저장되면 john smith라는 이름과 alice white 이름의 연관성은 사라집니다.

이를 해결하기 위해서 Nested 라는 타입을 지정할 수 있습니다.

Nested를 사용하면 ElasticSearch는 배열을 **내부적으로 숨겨진 도큐먼트에 따로 저장**합니다.

만약 user가 11명이면, 11개의 숨겨진 user 도큐먼트와 1개의 본 도큐먼트가 저장됩니다. (도큐먼트가 늘어나는 만큼 검색 비용도 증가)

## Arrays (배열)

ElasticSearch에는 전용 Arrarys(배열) 데이터 유형은 없습니다.

모든 필드는 단일 값이나 배열을 모두 저장할 수 있습니다.

다만, 배열에 포함된 모든 값은 동일한 데이터 타입이어야 합니다.

```
// 인덱스 생성
curl -XPUT http://{es_host}:{es_port}/{인덱스이름} -d'
{
    "mappings": {
        "properties": {
            "{필드이름}": {
                "type": "keyword"
            }
        }
    }
}'
```
```
// 1번 데이터 입력 - 배열 데이터
curl -XPUT http://{es_host}:{es_port}/{인덱스이름}/_doc/1?pretty \
-H 'Content-Type: application/json' -d'
{
    "{필드이름}":    [ "elasticsearch", "wow" ]
}'
```
```
// 2번 데이터 입력 - 단일 데이터
curl -XPUT http://{es_host}:{es_port}/{인덱스이름}/_doc/2?pretty \
-H 'Content-Type: application/json' -d'
{
    "{필드이름}":    "elasticsearch"
}'
```
```
// 데이터 조회 - 앞서 입력한 두 건의 데이터가 모두 조회됨
curl -XPOST http://{es_host}:{es_port}/{인덱스이름}/_search \
-H 'Content-Type: application/json' -d'
{
    "{필드이름}":    "elasticsearch"
}'
```

## Multi-value 필드(다중 데이터 타입)

ElasticSearch에서는 동일한 필드를 여러 방식으로 색인하는 것이 유용할 때가 있습니다.

예를 들어서 "제품명"이라는 필드를 사용하는데, "제품명"으로 데이터를 검색하기도 하고, "제품명" 별로 수량을 취합하기도 하는 경우를 생각해보겠습니다.

"제품명" 필드가 사용자 검색의 대상이 될 때는 **Text** 타입으로, 수량 취합을 할 떄는 **Keyword** 타입으로 사용되는 것이 좋습니다.

이런 경우에는 하나의 필드에 두 가지 타입을 모두 매핑할 수 있습니다.

```
// multi-value(다중값) 필드를 가진 인덱스 생성
curl -XPUT http://{es_host}:{es_port}/{테스트인덱스} -H 'Content-Type: application/json' -d'
{
    "mappings": {
        "properties": {
            "제품명": {
                "type": "text",
                "fields": {
                    "raw": {
                        "type": "keyword"
                    }
                }
            }
        }
    }
}'
```

하나의 필드에 두 가지 타입을 매핑하고, 데이터를 입력하면 하나의 데이터가 Text 타입과 Keyword 타입 각각에 입력됩니다. 

```
curl -XPUT http://{es_host}:{es_port}/{테스트인덱스}/_doc/1?pretty -H 'Content-Type: application/json' -d'
{
    "제품명":    "Cosmetic Powder"
}'
```
