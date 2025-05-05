---
title: AEM에서 캐시된 페이지 무효화
description: 효과적인 캐시 관리를 위해 Dispatcher와 AEM 간의 상호 작용을 구성하는 방법에 대해 알아봅니다.
cmgrlastmodified: 01.11.2007 08 22 29 [aheimoz]
pageversionid: 1193211344162
template: /apps/docs/templates/contentpage
contentOwner: User
products: SG_EXPERIENCEMANAGER/DISPATCHER
topic-tags: dispatcher
content-type: reference
exl-id: 90eb6a78-e867-456d-b1cf-f62f49c91851
source-git-commit: 9be9f5935c21ebbf211b5da52280a31772993c2e
workflow-type: tm+mt
source-wordcount: '1407'
ht-degree: 100%

---

# AEM에서 캐시된 페이지 무효화 {#invalidating-cached-pages-from-aem}

AEM과 함께 Dispatcher를 사용하는 경우 효과적인 캐시 관리를 위해 상호 작용을 구성해야 합니다. 환경에 따라 구성을 통해 성능을 높일 수도 있습니다.

## AEM 사용자 계정 설정 {#setting-up-aem-user-accounts}

기본 `admin` 사용자 계정은 기본적으로 설치된 복제 에이전트를 인증하는 데 사용됩니다. 복제 에이전트에 사용할 전용 사용자 계정을 생성합니다.

자세한 내용은 AEM 보안 체크리스트의 [복제 및 전송 사용자 구성](https://experienceleague.adobe.com/ko/docs/experience-manager-release-information/aem-release-updates/previous-updates/aem-previous-versions#VerificationSteps) 섹션을 참조하십시오.

<!-- OLD URL from above https://helpx.adobe.com/kr/experience-manager/6-3/sites/administering/using/security-checklist.html#VerificationSteps -->

## 작성 환경에서 Dispatcher 캐시 무효화 {#invalidating-dispatcher-cache-from-the-authoring-environment}

AEM 작성자 인스턴스의 복제 에이전트는 페이지가 게시될 때 캐시 무효화 요청을 Dispatcher에 보냅니다. 새 콘텐츠가 게시되면 Dispatcher가 결국 캐시 파일을 새로 고칩니다.

<!-- 

Comment Type: draft

<p>Cache invalidation requests for a page are also generated for any aliases or vanity URLs that are configured in the page properties. </p>

 -->

<!-- 

Comment Type: remark
Last Modified By: unknown unknown (ims-author-0436B4A35714BFF67F000101@AdobeID)
Last Modified Date: 2017-05-25T10:37:23.679-0400

<p>Hiding this information due to <a href="https://jira.corp.adobe.com/browse/CQDOC-5805">CQDOC-5805</a>.</p>

 -->

AEM 작성자 인스턴스에서 복제 에이전트를 구성하려면 다음 절차를 따르십시오. 구성은 페이지 활성화 시 Dispatcher 캐시를 무효화합니다.

1. AEM 도구 콘솔을 엽니다. (`https://localhost:4502/miscadmin#/etc`)
1. 작성자의 도구/복제/에이전트 아래에서 필요한 복제 에이전트를 엽니다. 기본적으로 설치되어 있는 Dispatcher 플러시 에이전트를 사용할 수 있습니다.
1. 편집을 클릭하고 설정 탭에서 **사용**&#x200B;이 선택되었는지 확인합니다.

1. (선택 사항) 별칭 또는 vanity 경로 무효화 요청을 활성화하려면 **별칭 업데이트** 옵션을 선택합니다.
1. 전송 탭에서 URI를 입력하여 Dispatcher에 액세스합니다.

   표준 Dispatcher 플러시 에이전트를 사용하는 경우 호스트 이름과 포트를 업데이트합니다. 예: https://&lt;*dispatcherHost*>:&lt;*portApache*>/dispatcher/invalidate.cache

   **참고:** Dispatcher 플러시 에이전트의 경우, 경로 기반 가상 호스트 항목을 사용하여 팜을 구분하는 경우에만 URI 속성이 사용됩니다. 이 필드를 사용하여 무효화할 팜을 대상으로 지정합니다. 예를 들어 팜 #1에는 `www.mysite.com/path1/*`의 가상 호스트가 있고 팜 #2에는 `www.mysite.com/path2/*`의 가상 호스트가 있습니다. URL `/path1/invalidate.cache`를 사용하여 첫 번째 팜을 대상으로 지정하고 `/path2/invalidate.cache`를 사용하여 두 번째 팜을 대상으로 지정할 수 있습니다. 자세한 내용은 [여러 도메인에 Dispatcher 사용](dispatcher-domains.md)을 참조하십시오.

1. 필요에 따라 다른 매개변수를 구성합니다.
1. 확인을 클릭하여 에이전트를 활성화할 수 있습니다.

또는 [AEM Touch UI](https://experienceleague.adobe.com/ko/docs/experience-manager-65/content/implementing/deploying/configuring/replication#configuring-a-dispatcher-flush-agent)에서 Dispatcher 플러시 에이전트에 액세스하고 구성할 수도 있습니다.

vanity URL에 대한 액세스를 활성화하는 방법에 대한 자세한 내용은 [Vanity URL에 대한 액세스 활성화](dispatcher-configuration.md#enabling-access-to-vanity-urls-vanity-urls)를 참조하십시오.

>[!NOTE]
>
>Dispatcher 캐시 플러시를 위한 에이전트에는 사용자 이름과 암호가 필요하지 않지만, 구성된 경우 기본 인증을 사용하여 전송됩니다.

이 접근 방식에는 두 가지 잠재적인 문제가 있습니다.

* 작성 인스턴스에서 Dispatcher에 연결할 수 있어야 합니다. 네트워크(예: 방화벽)가 둘 사이의 액세스가 제한되도록 구성된 경우에는 연결할 수 없을 수도 있습니다.

* 게시 및 캐시 무효화는 동시에 발생합니다. 시기에 따라 사용자는 캐시에서 페이지가 제거된 직후와 새 페이지가 게시되기 직전에 페이지를 요청할 수 있습니다. AEM은 바로 이때 이전 페이지를 반환하고 Dispatcher는 이를 다시 캐시합니다. 대규모 사이트의 경우에는 더 큰 문제가 됩니다.

## 게시 인스턴스에서 Dispatcher 캐시 무효화 {#invalidating-dispatcher-cache-from-a-publishing-instance}

특정 상황에서 작성 환경에서 게시 인스턴스로 캐시 관리를 전송하여 성능을 향상시킬 수 있습니다. 그러면 게시된 페이지가 수신될 때 Dispatcher에 캐시 무효화 요청을 보내는 게시 환경(AEM 작성 환경이 아닌)이 됩니다.

이러한 상황은 다음과 같습니다.

<!-- 

Comment Type: draft

<p>Cache invalidation requests for a page are also generated for any aliases or vanity URLs that are configured in the page properties. </p>

 -->

* AEM Dispatcher와 게시 인스턴스 간에 가능한 시간 충돌이 발생하지 않도록 합니다([작성 환경에서 Dispatcher 캐시 무효화](#invalidating-dispatcher-cache-from-the-authoring-environment) 참조).
* 시스템에는 고성능 서버에 상주하는 여러 개의 게시 인스턴스와 한 개의 작성 인스턴스만 포함되어 있습니다.

>[!NOTE]
>
>이 메서드를 사용하기 위해서는 숙련된 AEM 관리자가 결정을 내려야 합니다.

게시 인스턴스에서 작동하는 복제 에이전트가 Dispatcher 플러시를 제어합니다. 단, 구성은 작성 환경에서 이루어진 다음 에이전트를 활성화하여 전송됩니다.

1. AEM 도구 콘솔을 엽니다.
1. 게시의 도구/복제/에이전트 아래에서 필요한 복제 에이전트를 엽니다. 기본적으로 설치되어 있는 Dispatcher 플러시 에이전트를 사용할 수 있습니다.
1. 편집을 클릭하고 설정 탭에서 **사용**&#x200B;이 선택되었는지 확인합니다.
1. (선택 사항) 별칭 또는 vanity 경로 무효화 요청을 활성화하려면 **별칭 업데이트** 옵션을 선택합니다.
1. 전송 탭에서 필요한 URI를 입력하여 Dispatcher에 액세스합니다.\
   표준 Dispatcher 플러시 에이전트를 사용하는 경우, 호스트 이름과 포트를 업데이트해야 합니다. 예: `http://<dispatcherHost>:<portApache>/dispatcher/invalidate.cache`

   **참고:** Dispatcher 플러시 에이전트의 경우, 경로 기반 가상 호스트 항목을 사용하여 팜을 구분하는 경우에만 URI 속성이 사용됩니다. 이 필드를 사용하여 무효화할 팜을 대상으로 지정합니다. 예를 들어 팜 #1에는 `www.mysite.com/path1/*`의 가상 호스트가 있고 팜 #2에는 `www.mysite.com/path2/*`의 가상 호스트가 있습니다. URL `/path1/invalidate.cache`를 사용하여 첫 번째 팜을 대상으로 지정하고 `/path2/invalidate.cache`를 사용하여 두 번째 팜을 대상으로 지정할 수 있습니다. 자세한 내용은 [여러 도메인에 Dispatcher 사용](dispatcher-domains.md)을 참조하십시오.

1. 필요에 따라 다른 매개변수를 구성합니다.
1. 게시 인스턴스에 로그인하고 플러시 에이전트 구성을 확인합니다. 또한 이 기능이 활성화되어 있는지 확인합니다.
1. 영향을 받는 모든 게시 인스턴스에 대해 이 작업을 반복합니다.

구성 후 작성자에서 게시로 페이지를 활성화하면 이 에이전트가 표준 복제를 시작합니다. 로그에는 게시 서버에서 오는 요청을 나타내는 메시지가 포함됩니다. 다음 예제와 유사합니다.

1. `<publishserver> 13:29:47 127.0.0.1 POST /dispatcher/invalidate.cache 200`

## Dispatcher 캐시 수동 무효화 {#manually-invalidating-the-dispatcher-cache}

페이지를 활성화하지 않고 Dispatcher 캐시를 무효화(또는 플러시)하려면 Dispatcher에 HTTP 요청을 발행할 수 있습니다. 예를 들어 관리자 또는 다른 애플리케이션이 캐시를 플러시할 수 있도록 하는 AEM 애플리케이션을 만들 수 있습니다.

HTTP 요청으로 인해 Dispatcher가 캐시에서 특정 파일을 삭제합니다. 그런 다음 선택적으로 Dispatcher는 새 복사본으로 캐시를 새로 고칩니다.

### 캐시된 파일 삭제 {#delete-cached-files}

AEM Dispatcher가 캐시에서 파일을 삭제하도록 하는 HTTP 요청을 발행합니다. Dispatcher는 페이지에 대한 클라이언트 요청을 수신할 때만 파일을 다시 캐시합니다. 캐시된 파일을 이러한 방식으로 삭제하는 것은 동일한 페이지에 대한 동시 요청을 수신할 가능성이 없는 웹 사이트에 적합합니다.

HTTP 요청의 형식은 다음과 같습니다.

```xml
POST /dispatcher/invalidate.cache HTTP/1.1  
CQ-Action: Activate  
CQ-Handle: path-pattern
Content-Length: 0
```

Dispatcher는 `CQ-Handler` 헤더 값과 일치하는 이름을 가진 캐시된 파일 및 폴더를 플러시(삭제)합니다. 예를 들어 `/content/geomtrixx-outdoors/en`의 `CQ-Handle`은 다음 항목과 일치합니다.

* `geometrixx-outdoors` 디렉터리에 있는 `en`이라는 이름의 (모든 파일 확장자의) 모든 파일

* `en` 디렉터리 아래에 `_jcr_content`라는 모든 디렉터리(존재하는 경우, 페이지의 하위 노드에 대한 캐시된 렌더링 포함)

Dispatcher 캐시의 다른 모든 파일(또는 `/statfileslevel` 설정에 따라 특정 수준까지)은 `.stat` 파일 터치에 의해 무효화됩니다. 이 파일의 마지막 수정 날짜는 캐시된 문서의 마지막 수정 날짜와 비교되며 `.stat` 파일이 최신 파일인 경우 문서를 다시 가져옵니다. 자세한 내용은 [폴더 수준별 파일 무효화](dispatcher-configuration.md#main-pars_title_26)를 참조하십시오.

추가 헤더 `CQ-Action-Scope: ResourceOnly`를 전송하여 무효화(즉, .stat 파일 터치)를 방지할 수 있습니다. 이 기능을 사용하여 특정 리소스를 플러시할 수 있습니다. JSON 데이터와 같은 캐시의 다른 부분을 무효화하지 않고도 모두 가능합니다. 해당 데이터는 동적으로 생성되며 캐시와 독립적으로 정기적인 플러시가 필요합니다. 예를 들어 뉴스 및 주식 시세 표시기 등을 표시하기 위해 서드파티 시스템에서 얻은 데이터를 나타냅니다.

### 파일 삭제 및 다시 캐시 {#delete-and-recache-files}

Dispatcher가 캐시된 파일을 삭제하고 즉시 파일을 검색하고 다시 캐시하도록 하는 HTTP 요청을 발행합니다. 웹 사이트가 동일한 페이지에 대한 동시 클라이언트 요청을 수신할 가능성이 있는 경우 파일을 삭제하고 즉시 다시 캐시합니다. 즉시 다시 캐시하면 Dispatcher가 페이지를 한 번만 검색하고 캐시합니다(각 동시 클라이언트 요청에 대해 한 번씩 검색하고 캐시하는 대신).

**참고:** 파일 삭제 및 다시 캐시는 게시 인스턴스에서만 수행해야 합니다. 작성자 인스턴스에서 수행할 때 리소스가 게시되기 전에 다시 캐시하려는 시도가 발생하면 경합 상태가 발생합니다.

HTTP 요청의 형식은 다음과 같습니다.

```xml
POST /dispatcher/invalidate.cache HTTP/1.1  
CQ-Action: Activate   
`Content-Type: text/plain  
CQ-Handle: path-pattern  
Content-Length: numchars in bodypage_path0

page_path1 
...  
page_pathn
```

다시 캐시할 페이지 경로는 즉시 메시지 본문의 별도 행에 나열됩니다. `CQ-Handle` 값은 다시 캐시할 페이지를 무효화하는 페이지의 경로입니다. ([캐시](dispatcher-configuration.md#main-pars_146_44_0010) 구성 항목의 `/statfileslevel` 매개변수를 참조하십시오.) 다음 예제 HTTP 요청 메시지는 `/content/geometrixx-outdoors/en.html page`를 삭제하고 다시 캐시합니다.

```xml
POST /dispatcher/invalidate.cache HTTP/1.1  
CQ-Action: Activate  
Content-Type: text/plain   
CQ-Handle: /content/geometrixx-outdoors/en/men.html  
Content-Length: 36

/content/geometrixx-outdoors/en.html
```

### 예제 플러시 서블릿 {#example-flush-servlet}

다음 코드는 Dispatcher에 무효화 요청을 보내는 서블릿을 구현합니다. 서블릿은 `handle` 및 `page` 매개변수가 포함된 요청 메시지를 수신합니다. 이러한 매개변수는 각각 `CQ-Handle` 헤더의 값과 다시 캐시할 페이지의 경로를 제공합니다. 서블릿은 값을 사용하여 Dispatcher에 대한 HTTP 요청을 구성합니다.

서블릿이 게시 인스턴스에 배포되면 다음 URL로 인해 Dispatcher가 /content/geometrixx-outdoors/en.html 페이지를 삭제한 다음 새 복사본을 캐시합니다.

`10.36.79.223:4503/bin/flushcache/html?page=/content/geometrixx-outdoors/en.html&handle=/content/geometrixx-outdoors/en/men.html`

>[!NOTE]
>
>이 예제 서블릿은 안전하지 않으며 HTTP Post 요청 메시지의 사용만 보여 줍니다. 솔루션은 서블릿에 대한 액세스 보안을 유지해야 합니다.
>

```java
package com.adobe.example;

import org.apache.felix.scr.annotations.Component;
import org.apache.felix.scr.annotations.Service;
import org.apache.felix.scr.annotations.Property;

import org.apache.sling.api.SlingHttpServletRequest;
import org.apache.sling.api.SlingHttpServletResponse;
import org.apache.sling.api.servlets.SlingSafeMethodsServlet;

import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

import org.apache.commons.httpclient.*;
import org.apache.commons.httpclient.methods.PostMethod;
import org.apache.commons.httpclient.methods.StringRequestEntity;

@Component(metatype=true)
@Service
public class Flushcache extends SlingSafeMethodsServlet {

 @Property(value="/bin/flushcache")
 static final String SERVLET_PATH="sling.servlet.paths";

 private Logger logger = LoggerFactory.getLogger(this.getClass());

 public void doGet(SlingHttpServletRequest request, SlingHttpServletResponse response) {
  try{ 
      //retrieve the request parameters
      String handle = request.getParameter("handle");
      String page = request.getParameter("page");

      //hard-coding connection properties is a bad practice, but is done here to simplify the example
      String server = "localhost"; 
      String uri = "/dispatcher/invalidate.cache";

      HttpClient client = new HttpClient();

      PostMethod post = new PostMethod("https://"+host+uri);
      post.setRequestHeader("CQ-Action", "Activate");
      post.setRequestHeader("CQ-Handle",handle);
   
      StringRequestEntity body = new StringRequestEntity(page,null,null);
      post.setRequestEntity(body);
      post.setRequestHeader("Content-length", String.valueOf(body.getContentLength()));
      client.executeMethod(post);
      post.releaseConnection();
      //log the results
      logger.info("result: " + post.getResponseBodyAsString());
      }
  }catch(Exception e){
      logger.error("Flushcache servlet exception: " + e.getMessage());
  }
 }
}
```
