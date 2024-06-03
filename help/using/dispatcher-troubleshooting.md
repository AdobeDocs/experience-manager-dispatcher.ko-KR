---
title: Dispatcher 문제 해결
description: Dispatcher 문제를 해결하는 방법에 대해 알아봅니다.
cmgrlastmodified: 01.11.2007 08 22 29 [aheimoz]
pageversionid: 1193211344162
template: /apps/docs/templates/contentpage
contentOwner: User
products: SG_EXPERIENCEMANAGER/DISPATCHER
topic-tags: dispatcher
content-type: reference
exl-id: 29f338ab-5d25-48a4-9309-058e0cc94cff
source-git-commit: 0a1aa854ea286a30c3527be8fc7c0998726a663f
workflow-type: ht
source-wordcount: '539'
ht-degree: 100%

---

# Dispatcher 문제 해결 {#troubleshooting-dispatcher-problems}

>[!NOTE]
>
>Dispatcher 버전은 AEM과 독립적입니다. 단, Dispatcher 설명서는 AEM 설명서에 포함되어 있습니다. 항상 최신 버전의 AEM 설명서에 임베드된 Dispatcher 설명서를 사용하십시오.
>
>Dispatcher 설명서 링크를 따라가면 이 페이지로 리디렉션되었을 수 있습니다. 해당 링크는 이전 버전의 AEM에 대한 설명서에 포함되어 있습니다.

>[!NOTE]
>
>자세한 내용은 [Dispatcher 기술 자료](https://helpx.adobe.com/kr/experience-manager/kb/index/dispatcher.html), [Dispatcher 플러싱 문제 해결](https://experienceleague.adobe.com/search.html?lang=ko-KR#q=troubleshooting%20dispatcher%20flushing%20issues&amp;sort=relevancy&amp;f:el_product=[Experience%20Manager]) 및 [Dispatcher 주요 문제 FAQ](dispatcher-faq.md)를 참조하십시오.

## 기본 구성 확인 {#check-the-basic-configuration}

항상 그렇듯이 첫 번째 단계는 기본 사항을 확인하는 것입니다.

* [기본 작동 확인](/help/using/dispatcher-configuration.md#confirming-basic-operation)
* 웹 서버 및 Dispatcher에 대한 모든 로그 파일을 확인하십시오. 필요한 경우 Dispatcher [로깅](/help/using/dispatcher-configuration.md#logging)에 사용되는 `loglevel`을 높입니다.

* [구성 확인](/help/using/dispatcher-configuration.md):

   * 여러 Dispatcher가 있습니까?

      * 조사 중인 웹 사이트/페이지를 어떤 Dispatcher에서 처리하는지 확인했습니까?

   * 필터를 구현했습니까?

      * 해당 필터가 조사 중인 문제에 영향을 미치고 있습니까?

## IIS 진단 도구 {#iis-diagnostic-tools}

IIS는 실제 버전에 따라 다양한 추적 도구를 제공합니다.

* IIS 6 - IIS 진단 도구를 다운로드하고 구성할 수 있음
* IIS 7 - 추적이 완전히 통합됨

해당 도구는 활동을 모니터링하는 데 도움이 될 수 있습니다.

## IIS 및 404를 찾을 수 없음 {#iis-and-not-found}

IIS를 사용할 때 다양한 시나리오에서 `404 Not Found`가 반환되는 것을 경험할 수 있습니다. 이 경우 다음 기술 자료 문서를 참조하십시오.

* [IIS 6/7: POST 메서드가 404를 반환합니다](https://helpx.adobe.com/kr/experience-manager/kb/IIS6IsapiFilters.html)
* [IIS 6: 기본 경로 `/bin`이 포함된 URL에 대한 요청은 `404 Not Found`](https://helpx.adobe.com/kr/experience-manager/kb/RequestsToBinDirectoryFailInIIS6.html)을 반환합니다.

또한 Dispatcher 캐시 루트와 IIS 문서 루트가 동일한 디렉터리로 설정되어 있는지 확인합니다.

## 워크플로 모델 삭제 문제 {#problems-deleting-workflow-models}

**증상**

Dispatcher를 통해 AEM 작성자 인스턴스에 액세스할 때 워크플로 모델을 삭제하려고 하면 문제가 발생합니다.

**재현 단계:**

1. 작성자 인스턴스에 로그인합니다(요청이 Dispatcher를 통해 라우팅되고 있는지 확인).
1. 워크플로를 만듭니다. 예를 들어 Title이 workflowToDelete로 설정된 경우입니다.
1. 워크플로가 성공적으로 생성되었는지 확인합니다.
1. 워크플로를 선택하고 마우스 오른쪽 버튼으로 클릭한 다음 **삭제**&#x200B;를 클릭합니다.

1. **예**&#x200B;를 클릭하여 확인합니다.
1. 다음과 같은 내용을 보여 주는 오류 메시지 상자가 나타납니다.\
   `ERROR 'Could not delete workflow model!!`

**해결**

`dispatcher.any` 파일의 `/clientheaders` 섹션에 다음 헤더를 추가합니다.

* `x-http-method-override`
* `x-requested-with`

```
{  
{  
/clientheaders  
{  
...  
"x-http-method-override"  
"x-requested-with"  
}
```

## mod_dir 간섭 (Apache) {#interference-with-mod-dir-apache}

이 프로세스에서는 Dispatcher가 Apache 웹 서버 내에서 `mod_dir`과 상호 작용하는 방법을 설명합니다. 이러면 잠재적으로 예상치 못한 다양한 영향이 발생할 수 있습니다.

### Apache 1.3 {#apache}

Apache 1.3에서 `mod_dir`은 URL이 파일 시스템의 디렉터리에 매핑될 때 모든 요청을 처리합니다.

다음 중 하나를 수행합니다.

* 기존 `index.html` 파일로 요청 리디렉션
* 디렉터리 목록 생성

Dispatcher가 활성화되면 콘텐츠 유형 `httpd/unix-directory`에 대한 핸들러로 직접 등록되어 이러한 요청을 처리합니다.

### Apache 2.x {#apache-x}

Apache 2.x에서는 상황이 다릅니다. 모듈은 URL 수정과 같은 요청의 여러 단계를 처리할 수 있습니다. `mod_dir`은 (URL이 디렉터리에 매핑될 때) 요청을 `/`가 추가된 URL로 리디렉션하여 이 단계를 처리합니다.

Dispatcher가 `mod_dir` 수정을 가로채지는 않지만 리디렉션된 URL에 대한 요청을 전적으로 처리합니다(즉, `/`가 추가됨). 이 프로세스에서 원격 서버(예: AEM)가 `/a_path`에 대한 요청을 `/a_path/`에 대한 요청과 다르게 처리하는 경우(`/a_path`가 기존 디렉터리에 매핑될 때) 문제를 일으킬 수 있습니다.

이러한 상황이 발생하면 다음 중 하나를 수행해야 합니다.

* Dispatcher가 처리하는 `Directory` 또는 `Location` 하위 트리에 대해 `mod_dir` 비활성화

* `DirectorySlash Off`를 사용하여 `/`를 추가하지 않도록 `mod_dir` 구성
