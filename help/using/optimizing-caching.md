---
title: 캐시 성능을 위한 웹 사이트 최적화
description: 캐싱의 이점을 극대화하기 위해 웹 사이트를 디자인하는 방법에 대해 알아봅니다.
contentOwner: User
products: SG_EXPERIENCEMANAGER/DISPATCHER
topic-tags: dispatcher
content-type: reference
redirecttarget: https://helpx.adobe.com/experience-manager/6-4/sites/deploying/using/configuring-performance.html
index: y
internal: n
snippet: y
source-git-commit: 9be9f5935c21ebbf211b5da52280a31772993c2e
workflow-type: tm+mt
source-wordcount: '1128'
ht-degree: 100%

---


# 캐시 성능을 위한 웹 사이트 최적화 {#optimizing-a-website-for-cache-performance}

<!-- 

Comment Type: remark
Last Modified By: Silviu Raiman (raiman)
Last Modified Date: 2017-10-25T04:13:34.919-0400

<p>This is a redirect to /experience-manager/6-2/sites/deploying/using/configuring-performance.html</p>

 -->

>[!NOTE]
>
>Dispatcher 버전은 AEM과 독립적입니다. Dispatcher 설명서 링크를 따라가면 이 페이지로 리디렉션되었을 수 있습니다. 해당 링크는 이전 버전의 AEM에 대한 설명서에 포함되어 있습니다.

Dispatcher는 성능을 최적화하는 데 사용할 수 있는 여러 기본 제공 메커니즘을 제공합니다. 이 섹션에서는 캐싱의 이점을 극대화하기 위해 웹 사이트를 디자인하는 방법을 설명합니다.

>[!NOTE]
>
>Dispatcher가 표준 웹 서버에 캐시를 저장한다는 것을 기억하는 데 도움이 될 수 있습니다. 이는 다음을 의미합니다.
>
>* 페이지로 저장할 수 있는 모든 것을 캐시하고 URL을 사용하여 요청할 수 있습니다.
>* HTTP 헤더, 쿠키, 세션 데이터 및 양식 데이터와 같은 다른 항목은 저장할 수 없습니다.
>
>일반적으로, 좋은 URL을 선택하고 이 추가 데이터에 의존하지 않는 것이 많은 캐싱 전략에 포함됩니다.

## 일관된 페이지 인코딩 사용 {#using-consistent-page-encoding}

HTTP 요청 헤더는 캐시되지 않으므로 헤더에 페이지 인코딩 정보를 저장하면 문제가 발생할 수 있습니다. 이 상황에서 Dispatcher가 캐시에서 페이지를 제공하면 웹 서버의 기본 인코딩이 페이지에 사용됩니다. 이 문제를 방지하는 두 가지 방법이 있습니다.

* 인코딩을 하나만 사용하는 경우 웹 서버에서 사용되는 인코딩이 AEM 웹 사이트의 기본 인코딩과 동일한지 확인합니다.
* 다음 예제와 같이, 인코딩을 설정하려면 HTML `head` 섹션에서 `<META>` 태그를 사용합니다.

```xml
        <META http-equiv="Content-Type" content="text/html; charset=EUC-JP">
```

## URL 매개변수 방지 {#avoid-url-parameters}

가능하면 캐시하려는 페이지의 URL 매개변수를 사용하지 마십시오. 예를 들어 사진 갤러리가 있는 경우 다음 URL은 캐시되지 않습니다(Dispatcher가 [적절하게 구성](dispatcher-configuration.md#main-pars_title_24)되지 않은 경우).

```xml
www.myCompany.com/pictures/gallery.html?event=christmas&amp;page=1
```

단, 다음과 같이 이러한 매개변수를 페이지 URL에 넣을 수 있습니다.

```xml
www.myCompany.com/pictures/gallery.christmas.1.html
```

>[!NOTE]
>
>이 URL은 gallery.html과 동일한 페이지 및 동일한 템플릿을 호출합니다. 템플릿 정의에서 페이지를 렌더링하는 스크립트를 지정하거나 모든 페이지에 대해 동일한 스크립트를 사용할 수 있습니다.

## URL로 사용자 정의 {#customize-by-url}

사용자가 글꼴 크기(또는 기타 레이아웃 사용자 정의)를 변경할 수 있도록 허용하는 경우 다른 사용자 정의가 URL에 반영되었는지 확인합니다.

예를 들어 쿠키는 캐시되지 않으므로 글꼴 크기를 쿠키(또는 유사한 메커니즘)에 저장하면 캐시된 페이지에 대해 글꼴 크기가 유지되지 않습니다. 따라서 Dispatcher는 임의의 글꼴 크기 문서를 무작위로 반환합니다.

URL에 글꼴 크기를 선택기로 포함하면 이 문제를 피할 수 있습니다.

```xml
www.myCompany.com/news/main.large.html
```

>[!NOTE]
>
>대부분의 레이아웃 측면에서 스타일 시트 또는 클라이언트측 스크립트, 혹은 둘 다를 사용하는 것도 가능합니다. 둘 중 하나 또는 둘 다 캐싱과 잘 작동합니다.
>
>이 방법은 다음과 같은 URL을 사용할 수 있는 인쇄 버전에도 유용합니다.
>
>`www.myCompany.com/news/main.print.html`
>
>템플릿 정의의 스크립트 글로빙을 사용하여 인쇄 페이지를 렌더링하는 별도의 스크립트를 지정할 수 있습니다.

## 제목으로 사용된 이미지 파일 무효화 {#invalidating-image-files-used-as-titles}

페이지 제목 또는 기타 텍스트를 사진으로 렌더링하는 경우 페이지의 콘텐츠 업데이트 시 삭제되도록 파일을 저장합니다.

1. 이미지 파일을 페이지와 동일한 폴더에 넣습니다.
1. 이미지 파일에 다음 이름 지정 형식을 사용합니다.

   `<page file name>.<image file name>`

예를 들어 myPage.html 페이지의 제목을 myPage.title.gif 파일에 저장할 수 있습니다. 이 파일은 페이지가 업데이트되면 자동으로 삭제되므로 페이지 제목이 변경되면 캐시에 자동으로 반영됩니다.

>[!NOTE]
>
>이미지 파일이 AEM 인스턴스에 존재할 필요는 없습니다. 이미지 파일을 동적으로 생성하는 스크립트를 사용할 수 있습니다. 그러면 Dispatcher가 웹 서버에 파일을 저장합니다.

## 탐색에 사용된 이미지 파일 무효화 {#invalidating-image-files-used-for-navigation}

탐색 항목에 사진을 사용하는 경우 메서드는 기본적으로 제목과 동일하지만 약간 더 복잡합니다. 대상 페이지와 함께 모든 탐색 이미지를 저장합니다. 일반 및 활성에 대해 두 개의 사진을 사용하는 경우 다음 스크립트를 사용할 수 있습니다.

* 페이지를 정상적으로 표시하는 스크립트.
* `.normal` 요청을 처리하고 일반 사진을 반환하는 스크립트.
* `.active` 요청을 처리하고 활성화된 사진을 반환하는 스크립트.

콘텐츠 업데이트가 페이지와 함께 이러한 사진을 삭제하도록 하려면 페이지와 동일한 이름 지정 핸들을 사용하여 이러한 사진을 만드는 것이 중요합니다.

수정되지 않은 페이지의 경우 페이지 자체가 자동 무효화되더라도 사진은 캐시에 남아 있습니다.

## 개인화 {#personalization}

Dispatcher는 개인화된 데이터를 캐시할 수 없으므로 필요한 경우 개인화를 제한하는 것이 좋습니다. 이유는 다음과 같습니다.

* 자유롭게 사용자 정의할 수 있는 시작 페이지를 사용하는 경우 사용자가 요청할 때마다 해당 페이지를 구성해야 합니다.
* 반대로 10개의 서로 다른 시작 페이지를 선택할 수 있는 경우 각 시작 페이지를 캐시할 수 있으므로 성능이 향상됩니다.

>[!NOTE]
>
>각 페이지를 개인화하는 경우(예: 제목 표시줄에 사용자 이름을 입력하는 경우) 캐시할 수 없으므로 성능에 큰 영향을 미칠 수 있습니다.
>
>그러나 필요한 경우 다음을 수행할 수 있습니다.
>
>* iFrame을 사용하여 페이지를 모든 사용자에게 동일한 부분과 사용자의 모든 페이지에 동일한 부분으로 분할할 수 있습니다. 그런 다음 이 두 부분을 모두 캐시할 수 있습니다.
>* 클라이언트측 JavaScript를 사용하여 개인화된 정보를 표시할 수 있습니다. 하지만 사용자가 JavaScript를 끈 경우에도 페이지가 올바르게 표시되는지 확인해야 합니다.
>

## 고정 연결 {#sticky-connections}

[고정 연결](dispatcher.md#TheBenefitsofLoadBalancing)은 한 명의 사용자에 대한 문서가 모두 동일한 서버에서 구성되도록 합니다. 사용자가 이 폴더를 떠났다가 나중에 다시 돌아와도 연결은 계속 유지됩니다. 웹 사이트에 고정 연결이 필요한 모든 문서를 보관할 하나의 폴더를 정의하십시오. 다른 문서는 여기에 넣지 마십시오. 이렇게 하면 개인화된 페이지 및 세션 데이터를 사용하는 경우 로드 밸런싱에 영향을 미칩니다.

## MIME 유형 {#mime-types}

브라우저가 파일 유형을 결정할 수 있는 두 가지 방법이 있습니다.

1. 확장자별(예: .html, .gif 및 .jpg)
1. 서버가 파일과 함께 보내는 MIME 형식별

대부분의 파일에서 MIME 유형은 파일 확장자에 암시되어 있습니다.

1. 확장자별(예: .html, .gif 및 .jpg)
1. 서버가 파일과 함께 보내는 MIME 형식별

파일 이름에 확장자가 없으면 일반 텍스트로 표시됩니다.

MIME 유형은 HTTP 헤더의 일부이므로 Dispatcher는 이를 캐시하지 않습니다. AEM 애플리케이션이 인식된 파일 확장자가 없는 파일을 반환할 수 있습니다. 파일이 MIME 유형을 대신 사용하는 경우 해당 파일이 잘못 표시될 수 있습니다.

파일이 제대로 캐시되었는지 확인하려면 다음 지침을 따르십시오.

* 파일의 확장자가 항상 적절한지 확인하십시오.
* download.jsp?file=2214와 같은 URL이 있는 일반 파일 제공 스크립트를 사용하지 마십시오. 파일 사양이 포함된 URL을 사용하도록 스크립트를 다시 작성합니다. 이전 예제의 경우 `download.2214.pdf`가 될 것입니다.

