---
layout: single
title: "[ElasticSearch] 클러스터 파라미터 변경"
date: 2025-01-06 11:00:00 +0900
categories: 
  - ElasticSearch
tag: 
  - ElasticSearch
  - cluster.routing.allocation
  - 디스크 사용량
toc: true
toc_label: 목차
toc_sticky: true
---

본 포스트는 ElasticSearch 클러스터 세팅을 확인하고, 수정하는 방법을 정리한 것입니다.

# ElasticSearch 클러스터 세팅 확인

현재 사용하고 있는 ElasticSearch 클러스터의 세팅은 아래 명령으로 확인할 수 있습니다.

```
curl -XGET localhost:9200/_cluster/settings?include_defaults=true
```

# 주요 파라미터 - 디스크 사용량 관련

ElasticSearch 클러스터 세팅에는 다양한 파라미터가 있는데, 여기서는 디스크 사용량과 관련된 주요 파라미터 몇 가지만 알아 보겠습니다. 

| 파라미터 | 기본 값 | 설명 |
|:---|:---|:---|
| cluster.routing.allocation.disk.threshold_enabled	 | true | 디스크 사용량을 기반으로 샤드 할당을 관리할지 여부 <br> 동적으로 적용되는 설정으로 변경시 ElasticSearch를 재시작하지 않아도 됩니다. |
| cluster.routing.allocation.disk.watermark.low	 | 85% | 노드의 디스크사용량이 85%를 넘으면 해당 노드에 샤드를 할당하지 않습니다. <br> 이미 생성된 인덱스나 새로 생성되는 인덱스에는 영향을 주지 못 하지만, 복제본 생성에는 영향을 줍니다. <br> 동적으로 적용되는 설정으로 변경시 ElasticSearch를 재시작하지 않아도 됩니다. |
| cluster.routing.allocation.disk.watermark.high	| 90% | 노드의 디스크사용량이 90%가 넘으면 해당 노드의 샤드를 다른 노드로 옮기려고 합니다. <br> 동적으로 적용되는 설정으로 변경시 ElasticSearch를 재시작하지 않아도 됩니다. |
| cluster.routing.allocation.disk.watermark.flood_stage	| 95% | 노드의 디스크샤용량이 95%를 넘으면 인덱스를 읽기 전용으로 변경합니다. (디스크 공간 부족을 막기 위한 최후의 수단) <br> 동적으로 적용되는 설정으로 변경시 ElasticSearch를 재시작하지 않아도 됩니다. <br> 디스크 사용량이 cluster.routing.allocation.disk.watermark.high 보다 낮아지면 자동으로 해제됩니다. |
| cluster.info.update.interval	| 30s | 노드의 디스크 사용량 점검 주기 |

# 파라미터 수정 방법

ElasticSearch 클러스터 세팅을 변경하기 위해서는 REST API를 사용합니다.

## 디스크 사용량 관련 세팅 수정

아래 명령은 다음과 같은 작업을 실행하게 합니다.
* 노드의 디스크 사용량이 96%를 넘으면 해당 노드의 샤드(들) 중에 일부를 다른 노드로 옮기게 함
* 노드의 디스크 사용량이 99%를 넘으면 해당 노드의 모든 인덱스를 읽기 전용으로 변경하게 함
* 노드의 디스크 사용량을 1분마다 확인함

```
curl -XPUT "localhost:9200/_cluster/settings?pretty" \
-H 'Content-Type: application/json' -d'
{
  "transient": {
    "cluster.routing.allocation.disk.watermark.high": "96%",
    "cluster.routing.allocation.disk.watermark.flood_stage": "99%",
    "cluster.info.update.interval": "1m"
  }
}'
```

## 인덱스 Read/Write 세팅 수정

아래 명령은 ElasticSearch 클러스터 안의 모든 인덱스를 읽기/쓰기가 가능하도록 수정합니다. 

```
curl -XPUT "localhost:9200/_all/_settings?pretty" \
-H 'Content-Type: application/json' -d'
{
    "index": {
        "blocks": {
            "read_only_allow_delete": "false"
        }
    }
}'
```
