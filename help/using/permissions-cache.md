---
title: 보안 콘텐츠 캐싱
description: Dispatcher에서 권한 구분 캐싱이 작동하는 방식에 대해 알아봅니다.
contentOwner: User
products: SG_EXPERIENCEMANAGER/DISPATCHER
topic-tags: dispatcher
content-type: reference
exl-id: 3d8d8204-7e0d-44ad-b41b-6fec2689c6a6
source-git-commit: 2d90738d01fef6e37a2c25784ed4d1338c037c23
workflow-type: ht
source-wordcount: '910'
ht-degree: 100%

---

# 보안 콘텐츠 캐싱 {#caching-secured-content}

권한 구분 캐싱을 사용하면 보안 페이지를 캐시할 수 있습니다. Dispatcher는 캐시된 페이지를 전달하기 전에 페이지에 대한 사용자의 액세스 권한을 확인합니다.

Dispatcher에는 권한에 구분 캐싱을 구현하는 AuthChecker 모듈이 포함되어 있습니다. 모듈이 활성화되면 Dispatcher는 AEM 서블릿을 호출하여 요청된 콘텐츠에 대한 사용자 인증 및 권한 부여를 수행합니다. 서블릿 응답은 콘텐츠가 캐시에서 웹 브라우저로 전달되는지 여부를 결정합니다.

인증 및 권한 부여 방법은 AEM 배포에 따라 다르므로 서블릿을 생성해야 합니다.

>[!NOTE]
>
>`deny` 필터를 사용하여 포괄적인 보안 제한을 적용하십시오. 사용자 또는 그룹의 하위 집합에 대한 액세스를 허용하도록 구성된 페이지에 대해 권한 구분 캐싱을 사용하십시오.

다음 다이어그램은 웹 브라우저가 권한 구분 캐싱이 사용되는 페이지를 요청할 때 발생하는 이벤트의 순서를 보여 줍니다.

## 페이지가 캐시되고 사용자가 승인됨 {#page-is-cached-and-user-is-authorized}

![](assets/chlimage_1.png)

1. Dispatcher가 요청된 콘텐츠가 캐시되고 유효한지 확인합니다.
1. Dispatcher가 렌더링에 요청 메시지를 보냅니다. HEAD 섹션에는 브라우저 요청의 모든 헤더 행이 포함됩니다.
1. 렌더링이 AuthChcker 서블릿을 호출하여 보안 검사를 수행하고 Dispatcher에 응답합니다. 응답 메시지에는 사용자에게 권한이 부여되었음을 나타내는 HTTP 상태 코드 200이 포함됩니다.
1. Dispatcher가 렌더링 응답의 헤더 행과 본문의 캐시된 콘텐츠로 구성된 응답 메시지를 브라우저에 보냅니다.

## 페이지가 캐시되지 않고 사용자가 승인됨 {#page-is-not-cached-and-user-is-authorized}

![](assets/chlimage_1-1.png)

1. Dispatcher가 콘텐츠가 캐시되지 않았는지 또는 업데이트가 필요한지 확인합니다.
1. Dispatcher가 원본 요청을 렌더링에 전달합니다.
1. 렌더링이 AEM 권한 부여자 서블릿(Dispatcher AuthChcker 서블릿이 아님)을 호출하여 보안 검사를 수행합니다. 사용자에게 권한이 부여되면 렌더링이 렌더링된 페이지를 응답 메시지 본문에 포함합니다.
1. Dispatcher가 응답을 브라우저에 전달합니다. Dispatcher가 렌더링 응답 메시지의 본문을 캐시에 추가합니다.

## 사용자가 승인되지 않음 {#user-is-not-authorized}

![](assets/chlimage_1-2.png)

1. Dispatcher가 캐시를 확인합니다.
1. Dispatcher가 브라우저 요청의 모든 헤더 행을 포함하는 요청 메시지를 렌더링에 보냅니다.
1. 렌더링이 AuthChcker 서블릿을 호출하여 실패한 보안 검사를 수행하고, 렌더링이 원본 요청을 Dispatcher로 전달합니다.
1. Dispatcher가 원본 요청을 렌더링에 전달합니다.
1. 렌더링이 AEM 권한 부여자 서블릿(Dispatcher AuthChcker 서블릿이 아님)을 호출하여 보안 검사를 수행합니다. 사용자에게 권한이 부여되면 렌더링이 렌더링된 페이지를 응답 메시지 본문에 포함합니다.
1. Dispatcher가 응답을 브라우저에 전달합니다. Dispatcher가 렌더링 응답 메시지의 본문을 캐시에 추가합니다.

## 권한 구분 캐싱 구현 {#implementing-permission-sensitive-caching}

권한 구분 캐싱을 구현하려면 다음 작업을 수행하십시오.

* 인증 및 권한 부여를 수행하는 서블릿 개발
* Dispatcher 구성

>[!NOTE]
>
>일반적으로 보안 리소스는 비보안 파일과 별도의 폴더에 저장됩니다. 예: /content/secure/

>[!NOTE]
>
>Dispatcher 앞에 콘텐츠 전송 네트워크(또는 다른 캐시)이 있는 경우에는 이 콘텐츠 전송 네트워크가 비공개 콘텐츠를 캐시하지 않도록 캐싱 헤더를 설정해야 합니다. 예: `Header always set Cache-Control private`.
>AEM as a Cloud Service의 경우 비공개 캐싱 헤더를 설정하는 방법에 대한 자세한 내용은 [캐싱](https://experienceleague.adobe.com/en/docs/experience-manager-cloud-service/content/implementing/content-delivery/caching) 페이지를 참조하십시오.

## AuthChcker 서블릿 만들기 {#create-the-auth-checker-servlet}

웹 콘텐츠를 요청하는 사용자의 인증 및 권한 부여를 수행하는 서블릿을 만들고 배포합니다. 서블릿은 AEM 사용자 계정 및 저장소 ACL 또는 LDAP 조회 서비스와 같은 모든 인증 및 권한 부여 메서드를 사용할 수 있습니다. Dispatcher가 렌더링으로 사용하는 AEM 인스턴스에 서블릿을 배포합니다.

서블릿은 모든 사용자가 액세스할 수 있어야 합니다. 따라서 서블릿은 시스템에 대한 읽기 전용 액세스를 제공하는 `org.apache.sling.api.servlets.SlingSafeMethodsServlet` 클래스를 확장해야 합니다.

서블릿은 렌더링에서 HEAD 요청만 수신하므로 `doHead` 메서드만 구현하면 됩니다.

렌더링에는 요청된 리소스의 URI가 HTTP 요청의 매개변수로 포함됩니다. 예를 들어 권한 부여 서블릿은 `/bin/permissioncheck`를 통해 액세스됩니다. /content/geometrixx-outdoors/en.html 페이지에서 보안 검사를 수행하려면 렌더링은 HTTP 요청에 다음 URL을 포함합니다.

`/bin/permissioncheck?uri=/content/geometrixx-outdoors/en.html`

서블릿 응답 메시지에는 다음 HTTP 상태 코드가 포함되어야 합니다.

* 200: 인증 및 권한 부여가 통과되었습니다.

다음 예제 서블릿은 HTTP 요청에서 요청된 리소스의 URL을 가져옵니다. 이 코드는 Felix SCR `Property` 주석을 사용하여 `sling.servlet.paths` 속성의 값을 /bin/permissioncheck로 설정합니다. `doHead` 메서드에서 서블릿은 세션 오브젝트를 가져오고 `checkPermission` 메서드를 사용하여 적절한 응답 코드를 결정합니다.

>[!NOTE]
>
>Sling Servlet Resolver(org.apache.sling.servlets.resolver.SlingServletResolver) 서비스에서 sling.servlet.paths 속성 값을 활성화해야 합니다.

### 예제 서블릿 {#example-servlet}

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

import javax.jcr.Session;

@Component(metatype=false)
@Service
public class AuthcheckerServlet extends SlingSafeMethodsServlet {
 
    @Property(value="/bin/permissioncheck")
    static final String SERVLET_PATH="sling.servlet.paths";
    
    private Logger logger = LoggerFactory.getLogger(this.getClass());
    
    public void doHead(SlingHttpServletRequest request, SlingHttpServletResponse response) {
     try{ 
      //retrieve the requested URL
      String uri = request.getParameter("uri");
      //obtain the session from the request
      Session session = request.getResourceResolver().adaptTo(javax.jcr.Session.class);     
      //perform the permissions check
      try {
       session.checkPermission(uri, Session.ACTION_READ);
       logger.info("authchecker says OK");
       response.setStatus(SlingHttpServletResponse.SC_OK);
      } catch(Exception e) {
       logger.info("authchecker says READ access DENIED!");
       response.setStatus(SlingHttpServletResponse.SC_FORBIDDEN);
      }
     }catch(Exception e){
      logger.error("authchecker servlet exception: " + e.getMessage());
     }
    }
}
```

## 권한 구분 캐싱을 위한 Dispatcher 구성 {#configure-dispatcher-for-permission-sensitive-caching}

>[!NOTE]
>
>요구 조건이 인증된 문서의 캐싱을 허용하는 경우 /cache 섹션의 /allowAuthorized 속성을 `/allowAuthorized 1`로 설정합니다. 자세한 내용은 [인증 사용 시 캐싱](/help/using/dispatcher-configuration.md) 항목을 참조하십시오.

dispatcher.any 파일의 auth_checker 섹션은 권한 구분 캐싱의 동작을 제어합니다. auth_checker 섹션에는 다음 하위 섹션이 포함됩니다.

* `url`: 보안 검사를 수행하는 서블릿의 `sling.servlet.paths` 속성 값입니다.

* `filter`: 권한 구분 캐싱이 적용되는 폴더를 지정하는 필터입니다. 일반적으로 `deny` 필터는 모든 폴더에 적용되고 `allow` 필터는 보안 폴더에 적용됩니다.

* `headers`: 권한 부여 서블릿이 응답에 포함하는 HTTP 헤더를 지정합니다.

Dispatcher가 시작되면 Dispatcher 로그 파일에 다음 디버그 수준 메시지가 포함됩니다.

`AuthChecker: initialized with URL 'configured_url'.`

다음 예제 auth_checker 섹션은 이전 토픽의 서블릿을 사용하도록 Dispatcher를 구성합니다. 필터 섹션으로 인해 보안 HTML 리소스에서만 권한 검사가 수행됩니다.

### 예제 구성 {#example-configuration}

```xml
/auth_checker
  {
  # request is sent to this URL with '?uri=<page>' appended
  /url "/bin/permissioncheck"
      
  # only the requested pages matching the filter section below are checked,
  # all other pages get delivered unchecked
  /filter
    {
    /0000
      {
      /glob "*"
      /type "deny"
      }
    /0001
      {
      /glob "/content/secure/*.html"
      /type "allow"
      }
    }
  # any header line returned from the auth_checker's HEAD request matching
  # the section below will be returned as well
  /headers
    {
    /0000
      {
      /glob "*"
      /type "deny"
      }
    /0001
      {
      /glob "Set-Cookie:*"
      /type "allow"
      }
    }
  }
```
