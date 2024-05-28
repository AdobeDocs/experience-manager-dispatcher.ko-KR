---
title: Dispatcher 보안 검사 목록
description: 프로덕션을 시작하기 전에 완료해야 하는 Dispatcher 보안 체크리스트에 대해 알아봅니다.
contentOwner: User
products: SG_EXPERIENCEMANAGER/DISPATCHER
topic-tags: dispatcher
content-type: reference
jcr-lastmodifiedby: remove-legacypath-6-1
index: y
internal: n
snippet: y
exl-id: 49009810-b5bf-41fd-b544-19dd0c06b013
source-git-commit: 0a1aa854ea286a30c3527be8fc7c0998726a663f
workflow-type: tm+mt
source-wordcount: '590'
ht-degree: 64%

---

# Dispatcher 보안 검사 목록{#the-dispatcher-security-checklist}

<!-- 

Comment Type: remark
Last Modified By: unknown unknown (ims-author-00AF43764F54BE740A490D44@AdobeID)
Last Modified Date: 2015-06-05T05:14:35.365-0400

<p>Food for thought listed on <a href="https://jira.corp.adobe.com/browse/DOC-5649">DOC-5649</a>. To be considered while proof-reading.</p> 
<p> </p>

 -->

Adobe는 프로덕션을 시작하기 전에 다음 체크리스트를 완료할 것을 권장합니다.

>[!CAUTION]
>
>시작하기 전에 AEM 버전의 보안 체크리스트를 완료합니다. 해당 [Adobe Experience Manager 설명서](https://experienceleague.adobe.com/en/docs/experience-manager-65/content/security/security-checklist)를 참조하십시오.

## 최신 버전의 Dispatcher 사용 {#use-the-latest-version-of-dispatcher}

사용 가능한 최신 버전을 플랫폼에 설치합니다. 제품 및 보안 개선 사항을 활용하려면 Dispatcher 인스턴스를 업그레이드하여 최신 버전을 사용하십시오. [Dispatcher 설치](dispatcher-install.md)를 참조하십시오.

>[!NOTE]
>
>Dispatcher 로그 파일을 보면 Dispatcher 설치의 현재 버전을 확인할 수 있습니다.
>
>`[Thu Apr 30 17:30:49 2015] [I] [23171(140735307338496)] Dispatcher initialized (build 4.1.9)`
>
>로그 파일을 찾으려면 `httpd.conf`에서 Dispatcher 구성을 점검하십시오.

## 캐시를 플러시할 수 있는 클라이언트 제한 {#restrict-clients-that-can-flush-your-cache}

Adobe는 [캐시를 플러시할 수 있는 클라이언트를 제한](dispatcher-configuration.md#limiting-the-clients-that-can-flush-the-cache)할 것을 권장합니다.

## 전송 계층 보안을 위해 HTTPS 활성화 {#enable-https-for-transport-layer-security}

Adobe은 작성자 및 게시 인스턴스 모두에서 HTTPS 전송 레이어를 활성화할 것을 권장합니다.

<!-- 

Comment Type: remark
Last Modified By: unknown unknown (ims-author-00AF43764F54BE740A490D44@AdobeID)
Last Modified Date: 2015-06-26T04:41:28.841-0400

<p>Recommended to have SSL termination, front end SSL.</p> 
<p>Question is do we want to have SSL communication between dispatcher and AEM instances (publish and/or author).</p> 
<p>We might want to have two items:</p> 
<ul> 
 <li>MUST HTTPS clients -&gt; dispatcher / load balancer</li> 
 <li>NICE load balancer -&gt; dispatcher<br /> </li> 
 <li>NICE dispatcher -&gt; instances if sensitive information such as credit cards / or infrastructure requirements such as DMZ</li> 
</ul>

 -->

## 액세스 제한 {#restrict-access}

Dispatcher를 구성할 때 외부 액세스를 가능한 한 제한합니다. Dispatcher 설명서에서 [예제 /filter 섹션](dispatcher-configuration.md#main-pars_184_1_title)을 참조하십시오.

## 관리 URL에 대한 액세스가 거부되었는지 확인 {#make-sure-access-to-administrative-urls-is-denied}

필터를 사용하여 웹 콘솔과 같은 관리 URL에 대한 외부 액세스를 차단해야 합니다.

차단해야 하는 URL 목록은 [Dispatcher 보안 테스트](dispatcher-configuration.md#testing-dispatcher-security)를 참조하십시오.

## 차단 목록 대신 허용 목록 사용 {#use-allowlists-instead-of-blocklists}

허용 목록은 기본적으로 액세스 제어를 제공하는 더 나은 방법이므로 허용 목록에 명시적으로 포함되지 않는 한 모든 액세스 요청이 거부되어야 한다고 가정합니다. 이 모델은 아직 검토되지 않았거나 특정 구성 단계에서 고려되지 않았을 수 있는 새 요청에 대해 보다 제한적인 제어를 제공합니다.

## 전용 시스템 사용자로 Dispatcher 실행 {#run-dispatcher-with-a-dedicated-system-user}

Dispatcher를 구성할 때 최소 권한을 가진 전용 사용자가 웹 서버를 실행하는지 확인하십시오. Dispatcher 캐시 폴더에 대한 쓰기 액세스 권한만 부여하는 것이 좋습니다.

또한 IIS 사용자는 다음과 같이 웹 사이트를 구성해야 합니다.

1. 웹 사이트의 물리적 경로 설정에서 을 선택합니다. **특정 사용자로 연결**.
1. 사용자를 설정합니다.

## 서비스 거부(DoS) 공격 방지 {#prevent-denial-of-service-dos-attacks}

서비스 거부(DoS) 공격은 의도한 사용자가 컴퓨터 리소스를 사용할 수 없게 하려고 시도하는 것입니다.

Dispatcher 수준에서 DoS 공격을 방지하도록 구성하는 두 가지 방법이 있습니다. [필터](https://experienceleague.adobe.com/en/docs#/filter)

* mod_rewrite 모듈(예: [Apache 2.4](https://httpd.apache.org/docs/2.4/mod/mod_rewrite.html))을 사용하여 URL 유효성 검사를 수행합니다(URL 패턴 규칙이 너무 복잡하지 않은 경우).

* [필터](dispatcher-configuration.md#configuring-access-to-content-filter)를 사용하여 Dispatcher가 가짜 확장으로 URL을 캐시하지 못하도록 합니다.\
  예를 들어 다음과 같은 예상 MIME 유형으로 캐싱을 제한하도록 캐싱 규칙을 변경합니다.

   * `.html`
   * `.jpg`
   * `.gif`
   * `.swf`
   * `.js`
   * `.doc`
   * `.pdf`
   * `.ppt`

  다음에 대한 구성 파일 예를 볼 수 있습니다. [외부 액세스 제한](#restrict-access). 여기에는 MIME 유형에 대한 제한 사항이 포함됩니다.

게시 인스턴스에서 전체 기능을 활성화하려면 다음 노드에 대한 액세스를 방지하도록 필터를 구성합니다.

* `/etc/`
* `/libs/`

그런 다음, 아래의 노드 경로에 대한 액세스를 허용하도록 필터를 구성합니다.

* `/etc/designs/*`
* `/etc/clientlibs/*`
* `/etc/segmentation.segment.js`
* `/libs/cq/personalization/components/clickstreamcloud/content/config.json`
* `/libs/wcm/stats/tracker.js`
* `/libs/cq/personalization/*` (JS, CSS 및 JSON)
* `/libs/cq/security/userinfo.json` (CQ 사용자 정보)
* `/libs/granite/security/currentuser.json` (**데이터가 캐시되지 않아야 함**)

* `/libs/cq/i18n/*` (내재화)

<!-- 

Comment Type: remark
Last Modified By: unknown unknown (ims-author-00AF43764F54BE740A490D44@AdobeID)
Last Modified Date: 2015-06-26T04:38:17.016-0400

<p>We need to highlight whether a path applies to all versions or specific ones.<br /> </p>

 -->

## CSRF 공격을 방지하도록 Dispatcher 구성 {#configure-dispatcher-to-prevent-csrf-attacks}

AEM은 크로스 사이트 요청 위조 공격을 방지하는 [프레임워크](https://experienceleague.adobe.com/en/docs/experience-manager-release-information/aem-release-updates/previous-updates/aem-previous-versions#verification-steps)를 제공합니다. 허용 목록에 추가하다 이 프레임워크를 제대로 사용하려면 Dispatcher에서 다음을 수행하여 CSRF 토큰을 지원하십시오.

1. `/libs/granite/csrf/token.json` 경로를 허용하는 필터 만들기
1. Dispatcher 구성의 `clientheaders` 섹션에 `CSRF-Token` 헤더를 추가합니다.

## 클릭재킹 방지 {#prevent-clickjacking}

Adobe는 클릭재킹을 방지하기 위해 `SAMEORIGIN`으로 설정된 `X-FRAME-OPTIONS` HTTP 헤더를 제공하도록 웹 서버를 구성하는 것을 권장합니다.

클릭재킹에 대한 자세한 내용은 [OWASP 사이트](https://owasp.org/www-community/attacks/Clickjacking)를 참조하십시오.

## 침투 테스트 수행 {#perform-a-penetration-test}

Adobe은 프로덕션을 시작하기 전에 AEM 인프라의 침투 테스트를 수행할 것을 강력히 권장합니다.

