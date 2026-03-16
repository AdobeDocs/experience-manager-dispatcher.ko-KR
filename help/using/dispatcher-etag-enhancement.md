---
title: CDN 유효성 재검사를 위한 Dispatcher ETag 개선 사항
description: AEM as a Cloud Service에서 INTERNAL_AEM_DISPATCHER_ETAG_ENHANCEMENT의 가용성, 지원 상태 및 동작.
source-git-commit: ac0fafd060643903735ff565072ef2c5bee970be
workflow-type: tm+mt
source-wordcount: '308'
ht-degree: 0%

---

# CDN 유효성 재검사를 위한 Dispatcher ETag 개선 사항

## 개요

`INTERNAL_AEM_DISPATCHER_ETAG_ENHANCEMENT` 플래그를 사용하면 Dispatcher에서 캐시 히트에서 `If-None-Match` 요청 헤더를 평가할 수 있습니다. 들어오는 `If-None-Match` 값이 캐시된 `ETag`과(와) 일치하는 경우 Dispatcher은 `200 OK` 대신 `304 Not Modified`을(를) 반환할 수 있습니다.

이 동작은 CDN과 Dispatcher 간의 불필요한 페이로드 전송을 줄이고 조건부 캐시 효율성을 높이기 위해 설계되었습니다.

## 사용 가능

- Dispatcher 버전: `2.0.264`
- AEM SDK 빌드: `aem-sdk-2026.2.24464.20260214T050318Z-260100`

## AEM as a Cloud Service 지원

AEM as a Cloud Service에서 이 기능은 고객이 사용할 수 있도록 지원됩니다.

고객은 Cloud Manager에서 `INTERNAL_AEM_DISPATCHER_ETAG_ENHANCEMENT` 환경 변수를 설정하여 활성화할 수 있습니다. 필요할 때 Adobe에서 고객을 대신하여 활성화할 수도 있습니다.

사용하도록 설정하고 CDN에서 `If-None-Match`을(를) 보내고 관련 `ETag`이(가) Dispatcher 캐시에 있는 경우 CDN과 Dispatcher 간에 더 높은 `304` 응답률이 예상됩니다. 이러한 증가는 의도한 결과입니다.

## 구성 예(캐시 ETag 헤더)

이 개선 사항이 적용되려면 Dispatcher이 `ETag` 응답 헤더를 캐시하고 웹 서버가 파일 시스템 기반 ETags를 생성하지 않도록 구성되어 있는지 확인하십시오.

예제 `dispatcher.any` 캐시 섹션:

```text
/cache {
  /headers {
    "Cache-Control"
    "Content-Type"
    "Expires"
    "Last-Modified"
    "ETag"
  }
}
```

Dispatcher vhost 컨텍스트의 Apache 지시문 예:

```apache
FileETag none
```

기준 헤더 캐싱 지침은 [HTTP 응답 헤더 캐싱](dispatcher-configuration.md#caching-http-response-headers)을 참조하십시오.

## 유효성 검사 예

환경 변수를 활성화하고 구성 변경 사항을 배포한 후:

1. 캐시를 데우고 반환된 `ETag`을(를) 캡처하도록 한 번 요청합니다.
1. `If-None-Match: <etag-value>`(으)로 다시 요청합니다.
1. Dispatcher에서 캐시 적중 재유효성 검사 흐름에 대해 `304 Not Modified`을(를) 반환하는지 확인하십시오.

## 공개 참조(관련 동작)

Dispatcher의 헤더 캐싱 및 `ETag` 처리에 대한 고객 기준 지침을 보려면 다음을 참조하십시오.

- [Dispatcher 구성 - HTTP 응답 헤더 캐싱](https://experienceleague.adobe.com/en/docs/experience-manager-dispatcher/using/configuring/dispatcher-configuration#caching-http-response-headers)

&quot;이 기능은 Dispatcher `2.0.264`(AEM SDK `2026.2.24464`)에서 사용할 수 있습니다. 활성화되면 Dispatcher은 캐시된 `ETag` 값에 대해 `If-None-Match`의 유효성을 검사하고 캐시 적중 시 `304 Not Modified`을(를) 반환할 수 있습니다. AEM as a Cloud Service에서 이 기능이 지원되며 Cloud Manager 환경 구성을 통해 활성화할 수 있습니다.&quot;
