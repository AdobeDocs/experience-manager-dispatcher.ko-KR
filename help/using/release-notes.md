---
title: AEM Dispatcher 릴리스 정보
description: Adobe Experience Manager Dispatcher 관련 릴리스 정보
topic-tags: release-notes
content-type: reference
products: SG_EXPERIENCEMANAGER/6.4
exl-id: b55c7a34-d57b-4d45-bd83-29890f1524de
source-git-commit: e38352c25737c2b2a2ccd71fe23ce673a2390f51
workflow-type: tm+mt
source-wordcount: '1062'
ht-degree: 96%

---

# AEM Dispatcher 릴리스 정보{#aem-dispatcher-release-notes}

## 릴리스 정보 {#release-information}

|  |  |
|--- |--- |
| 제품 | Adobe Experience Manager(AEM) Dispatcher |
| 버전 | 4.3.7 |
| 유형 | 마이너 릴리스 |
| 날짜 | 2024년 3월 27일 |
| 다운로드 URL | <ul><li>[Apache 2.4](#apache)</li><li>[Microsoft® Internet Information Services (IIS)](#iis)</li></ul> |
| 호환성 | AEM 6.1 이상 |

## 시스템 요구 사항 및 사전 요구 사항 {#system-requirements-and-prerequisites}

시스템 요구 사항 및 필수 요구 사항에 대한 자세한 내용은 [지원되는 플랫폼](https://experienceleague.adobe.com/kr/docs/experience-manager-64/deploying/introduction/technical-requirements)을 참조하십시오.

Adobe는 유용한 최신 기능, 최신 버그 수정 및 최상의 성능을 이용하기 위해 최신 버전의 AEM Dispatcher를 사용할 것을 권장합니다.

## 설치 지침 {#installation-instructions}

자세한 지침은 [Dispatcher 설치](dispatcher-install.md)를 참조하십시오.

## 릴리스 기록 {#release-history}

### Release 4.3.7 (2024년 3월 27일) {#march}

**개선 사항**:

* DISP-1009 - 헤더 길이 재설정
* DISP-1013 - Linux®에 대한 Openssl 3.0 지원 추가
* DISP-1014 - 잘못된 리디렉션으로 이어지는 response.location 처리
* DISP-1017 - DTD 정의 변경

### 릴리스 4.3.6 (2023년 7월 25일) {#jyly}

**개선 사항**:

* DISP-911 AEM-05 - X-Edge-Key가 disp_apache2.c에서 누출될 수 있습니다.
* DISP-937 모든 선택기 로깅.
* DISP-998 시작 시 vanity URL 로드 구성 가능.

### 릴리스 4.3.5 (2022년 4월 4일) {#apr}

**개선 사항**:

* DISP-954 - 만료일이 경과되지 않았음에도 지원이 이루어지지 않음
* DISP-949 - 필터에 의해 POST 요청이 차단되더라도 Dispatcher가 404 대신 200을 반환

### 릴리스 4.3.4 (2021년 11월 29일) {#nov}

**버그 수정**:

* DISP-833 - X-Forwarded-Host 헤더에 쉼표로 구분된 호스트 이름의 목록이 포함될 수 있습니다.
* DISP-835 - DispatcherUseForwardedHost는 호스트 헤더가 마지막에 올 경우 호스트 헤더를 흡수합니다.

**개선 사항**:

* DISP-874 - 플래그 `DispatcherRestrictUncacheableContent`를 통해 DISP-818의 구현을 설정하거나 해제하는 Dispatcher 구성을 만듭니다. 기본값은 해제(Off)입니다. 옵션을 켜면 캐시 불가능한 콘텐츠에 대해 mod 만료별로 설정된 캐싱 헤더가 제거됩니다. 이 설정은 버전 4.3.3(기본값 켜짐(On))의 비헤이비어와 다르지만 4.3.3 이전의 버전(기본값 꺼짐(Off))과는 동일합니다. 브라우저 캐시의 보다 높은 유연성을 위해 `DispatcherRestrictUncacheableContent`의 기본값인 해제(Off) 상태를 유지하는 것이 좋습니다. 버전 4.3.3에서 4.3.4로 업그레이드할 때 버전 4.3.3과 동일한 비헤이비어를 유지하고자 한다면 `DispatcherRestrictUncacheableContent`를 명시적으로 설정(On)해야 합니다.
* DISP-841 - Dispatcher가 504 응답 코드에 대한 /serverStaleOnError를 무시합니다.
* DISP-874 - DISP-818의 구현을 설정하거나 해제하는 Dispatcher 구성을 만듭니다.
* DISP-883 - Dispatcher에서 URL 요청 분해를 보여 주는 추적입니다.
* DISP-944 - Vanity URL 사전 로드

### 릴리스 4.3.3 (2019년 10월 18일) {#october}

**버그 수정**:

* DISP-739 - LogLevel Dispatcher: **수준**&#x200B;이 작동하지 않음
* DISP-749 - Alpine Linux® Dispatcher가 추적 로그 수준과 충돌함

**개선 사항**:

* DISP-813 - openssl 1.1.x용 Dispatcher 지원
* DISP-814 - 캐시 플러시 중 Apache 40x 오류
* DISP-818 - mod_expires는 캐시할 수 없는 콘텐츠에 대한 Cache-Control 헤더를 추가함
* DISP-821 - 소켓에 로그 컨텍스트를 저장하지 않음
* DISP-822 - Dispatcher는 `pselect` 대신 `ppoll`을 사용해야 합니다.
* DISP-824 - 보안 DispatcherUseForwardedHost
* DISP-825 - 디스크에 더 이상 공간이 없을 때 특수 메시지를 기록함
* DISP-826 - 쿼리 문자열로 URI 다시 가져오기 지원

**새로운 기능**:

* DISP-703 - 팜별 캐시 적중률
* DISP-827 - 테스트용 로컬 서버
* DISP-828 - Dispatcher용 테스트 도커 이미지 생성

### 릴리스 4.3.2 (2019년 1월 31일) {#jan}

**버그 수정**:

* DISP-734 - 핸들러로 설정되지 않은 경우 Dispatcher가 insert_output_filter에서 충돌을 일으킴
* DISP-735 - RE가 Alpine Linux®에서 작동하지 않음
* DISP-740 - macOS Mojave에서 Dispatcher 로드는 기본적으로 비활성화되어 있음
* DISP-742 - 차단된 요청이 인증 검사기 보호 리소스에 정보를 유출할 수 있음

**개선 사항**:

* DISP-746 - dispatcher.any의 레이블이 지정되지 않은 문자열이 경고를 생성해야 합니다.

**새로운 기능**:

* DISP-747 - Apache 환경에서 요청 정보 제공

### 릴리스 4.3.1 (2018년 10월 16일) {#oct}

**버그 수정**:

* DISP-656 - Dispatcher가 잘못된 ETag 헤더를 제공함
* DISP-694 - 연결 유지가 오래되면 경고 표시 안 함
* DISP-714 - 쿠키 기반 세션 관리가 IIS에서 작동하지 않음
* DISP-715 - renderid 쿠키에 대한 보안 플래그
* DISP-720 - 임시 파일이 닫히지 않아 소모될 수 있음(열린 파일이 너무 많음)
* DISP-721 - Apache가 하위를 정상적으로 다시 시작할 때 Dispatcher가 poll()을 중단함
* DISP-722 - 캐시 파일이 8진수 모드 0600으로 생성됨
* DISP-723 - 렌더링 시간 제한이 0으로 설정된 경우 암시적 10분 시간 초과(및 재시도)
* DISP-725 - 문자열 뒤의 후행 문자가 이름 없는 값으로 자동 변환됨
* DISP-726 - 들어오는 호스트와 실제로 일치하는 팜이 없을 때 경고 기록
* DISP-727 - Dispatcher가 빈 캐시 파일에 대한 요청 콘텐츠 길이를 확인함
* DISP-730 - Dispatcher를 통해 헤더 파일에 액세스하려고 할 때 404
* DISP-731 - Dispatcher가 로그 삽입에 취약함
* DISP-732 - Dispatcher가 URL에서 연속적인 “/”를 제거해야 합니다.
* DISP-733 - Dispatcher가 연령 헤더를 설정(계산)해야 합니다.

**개선 사항**:

* DISP-656 - Dispatcher가 잘못된 ETag 헤더를 제공함
* DISP-694 - 연결 유지가 오래되면 경고 표시 안 함
* DISP-715 - renderid 쿠키에 대한 보안 플래그
* DISP-722 - 캐시 파일이 8진수 모드 0600으로 생성됨
* DISP-726 - 들어오는 호스트와 실제로 일치하는 팜이 없을 때 경고 기록

### 릴리스 4.3.0 (2018년 6월 13일) {#jun}

**버그 수정**:

* DISP-682 - 숫자 로그 수준이 잘못 적용됨
* DISP-685 - 32비트 Solaris™ SPARC® 바이너리에 __divdi3에 대한 정의되지 않은 참조가 있음
* DISP-688 - Dispatcher가 404 응답에서 “X-Cache-Info” 헤더를 반환하지 않음
* DISP-690 - 마지막으로 수정한 헤더를 캐시할 수 없음
* DISP-691 - w3wp.exe의 액세스 위반
* DISP-693 - Dispatcher 다운로드 페이지에서 Solaris™ 서버에 대한 아키텍처 세부 정보를 업데이트해야 함
* DISP-695 - Dispatcher 모듈 4.2.3의 DispatcherLog 수준 문제
* DISP-698 - Dispatcher TTL은 s-maxage 및 private 지시문을 지원해야 합니다
* DISP-700 - Alpine Linux®에서 모듈이 올바르게 작동하지 않음
* DISP-704 - %2b를 포함하는 브라우저 요청이 인코딩되지 않은 게시자에게 전송됨
* DISP-705 - 이중 자유 또는 손상으로 인한 Dispatcher 충돌(fasttop)
* DISP-706 - 무효화하는 동안 Dispatcher가 역참조 심볼릭 링크를 따라가면 무한 루프가 발생할 수 있음
* DISP-709 - 일부 vanity URL 확장 차단
* DISP-710 - Cent OS 6에서 사용할 수 없는 Linux®용 빌드

**개선 사항**:

* DISP-652 - Dispatcher가 잘못된 날짜 헤더를 제공함

## 유용한 리소스 {#helpful-resources}

* [AEM Dispatcher 개요](dispatcher.md)

## 다운로드 {#downloads}

### Apache 2.4 {#apache}

| 플랫폼 | 아키텍처 | OpenSSL 지원 | 다운로드하려면 클릭 |
|---|---|---|---|
| Linux® | i686 (32비트) | 없음 | [`dispatcher-apache2.4-linux-i686-4.3.7.tar.gz`](https://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-linux-i686-4.3.7.tar.gz) |
| Linux® | i686 (32비트) | 1.0 | [`dispatcher-apache2.4-linux-i686-ssl1.0-4.3.7.tar.gz`](https://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-linux-i686-ssl1.0-4.3.7.tar.gz) |
| Linux® | i686 (32비트) | 1.1 | [`dispatcher-apache2.4-linux-i686-ssl1.1-4.3.7.tar.gz`](https://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-linux-i686-ssl1.1-4.3.7.tar.gz) |
| Linux® | i686 (32비트) | 3.0 | [`dispatcher-apache2.4-linux-i686-ssl3.0-4.3.7.tar.gz`](https://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-linux-i686-ssl3.0-4.3.7.tar.gz) |
| Linux® | x86_64 (64비트) | 없음 | [`dispatcher-apache2.4-linux-x86_64-4.3.7.tar.gz`](https://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-linux-x86_64-4.3.7.tar.gz) |
| Linux® | x86_64 (64비트) | 1.0 | [`dispatcher-apache2.4-linux-x86_64-ssl1.0-4.3.7.tar.gz`](https://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-linux-x86_64-ssl1.0-4.3.7.tar.gz) |
| Linux® | x86_64 (64비트) | 1.1 | [`dispatcher-apache2.4-linux-x86_64-ssl1.1-4.3.7.tar.gz`](https://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-linux-x86_64-ssl1.1-4.3.7.tar.gz) |
| Linux® | x86_64 (64비트) | 3.0 | [`dispatcher-apache2.4-linux-x86_64-ssl3.0-4.3.7.tar.gz`](https://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-linux-x86_64-ssl3.0-4.3.7.tar.gz) |
| Linux® | aarch64 (64비트) | 없음 | [`dispatcher-apache2.4-linux-aarch64-4.3.7.tar.gz`](https://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-linux-aarch64-4.3.7.tar.gz) |
| Linux® | aarch64 (64비트) | 1.0 | [`dispatcher-apache2.4-linux-aarch64-ssl1.0-4.3.7.tar.gz`](https://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-linux-aarch64-ssl1.0-4.3.7.tar.gz) |
| Linux® | aarch64 (64비트) | 1.1 | [`dispatcher-apache2.4-linux-aarch64-ssl1.1-4.3.7.tar.gz`](https://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-linux-aarch64-ssl1.1-4.3.7.tar.gz) |
| Linux® | aarch64 (64비트) | 3.0 | [`dispatcher-apache2.4-linux-aarch64-ssl3.0-4.3.7.tar.gz`](https://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-linux-aarch64-ssl3.0-4.3.7.tar.gz) |
| macOS | arm64 (64비트) | 없음 | [`dispatcher-apache2.4-darwin-arm64-4.3.7.tar.gz`](https://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-darwin-arm64-4.3.7.tar.gz) |
| macOS | x86_64 (64비트) | 없음 | [`dispatcher-apache2.4-darwin-x86_64-4.3.7.tar.gz`](https://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-darwin-x86_64-4.3.7.tar.gz) |

### IIS {#iis}

| 플랫폼 | 아키텍처 | OpenSSL 지원 | 다운로드하려면 클릭 |
|---|---|---|---|
| Windows | x86(32비트) | 없음 | [`dispatcher-iis-windows-x86-4.3.7.zip`](https://download.macromedia.com/dispatcher/download/dispatcher-iis-windows-x86-4.3.7.zip) |
| Windows | x86(32비트) | 1.0 | [`dispatcher-iis-windows-x86-ssl1.0-4.3.7.zip`](https://download.macromedia.com/dispatcher/download/dispatcher-iis-windows-x86-ssl1.0-4.3.7.zip) |
| Windows | x86(32비트) | 1.1 | [`dispatcher-iis-windows-x86-ssl1.1-4.3.7.zip`](https://download.macromedia.com/dispatcher/download/dispatcher-iis-windows-x86-ssl1.1-4.3.7.zip) |
| Windows | x64(64비트) | 없음 | [`dispatcher-iis-windows-x64-4.3.7.zip`](https://download.macromedia.com/dispatcher/download/dispatcher-iis-windows-x64-4.3.7.zip) |
| Windows | x64(64비트) | 1.0 | [`dispatcher-iis-windows-x64-ssl1.0-4.3.7.zip`](https://download.macromedia.com/dispatcher/download/dispatcher-iis-windows-x64-ssl1.0-4.3.7.zip) |
| Windows | x64(64비트) | 1.1 | [`dispatcher-iis-windows-x64-ssl1.1-4.3.7.zip`](https://download.macromedia.com/dispatcher/download/dispatcher-iis-windows-x64-ssl1.1-4.3.7.zip) |
| Windows | x64(64비트) | 3.0 | [`dispatcher-iis-windows-x64-ssl3.0-4.3.7.zip`](https://download.macromedia.com/dispatcher/download/dispatcher-iis-windows-x64-ssl3.0-4.3.7.zip) |