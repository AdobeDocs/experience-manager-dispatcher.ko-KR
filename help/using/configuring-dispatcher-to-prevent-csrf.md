---
title: CSRF 공격을 방지하도록 Adobe Experience Manager Dispatcher 구성
description: 크로스 사이트 요청 위조 공격을 방지하도록 Adobe Experience Manager Dispatcher를 구성하는 방법에 대해 알아봅니다.
topic-tags: dispatcher
content-type: reference
exl-id: bcd38878-f977-46a6-b01a-03e4d90aef01
source-git-commit: 0a1aa854ea286a30c3527be8fc7c0998726a663f
workflow-type: tm+mt
source-wordcount: '235'
ht-degree: 48%

---

# CSRF 공격을 방지하도록 Adobe Experience Manager Dispatcher 구성{#configuring-dispatcher-to-prevent-csrf-attacks}

AEM(Adobe Experience Manager)은 크로스 사이트 요청 위조 공격을 방지하는 프레임워크를 제공합니다. 이 프레임워크를 적절하게 사용하려면 Dispatcher 구성을 다음과 같이 변경하십시오.

>[!NOTE]
>
>기존 구성을 기반으로 아래 예제에서 규칙 번호를 업데이트해야 합니다. Dispatcher는 마지막 일치 규칙을 사용하여 허용 또는 거부를 부여하므로 규칙을 기존 목록의 맨 아래에 배치합니다.

1. `author-farm.any` 및 `publish-farm.any`의 `/clientheaders` 섹션에서 목록 맨 아래에 다음 항목을 추가합니다.\
   `CSRF-Token`
1. `author-farm.any` 및 `publish-farm.any` 또는 `publish-filters.any` 파일의 /filters 섹션에 다음 행을 추가하여 Dispatcher에서 `/libs/granite/csrf/token.json`에 대한 요청을 허용합니다.\
   `/0999 { /type "allow" /glob " * /libs/granite/csrf/token.json*" }`

1. `publish-farm.any`의 `/cache /rules` 섹션 아래에서 Dispatcher가 `token.json` 파일을 캐시하지 못하도록 차단하는 규칙을 추가합니다. 일반적으로 작성자는 캐싱을 무시하므로 `author-farm.any`에 규칙을 추가할 필요가 없습니다.

   `/0999 { /glob "/libs/granite/csrf/token.json" /type "deny" }`

구성이 작동하는지 확인하려면 DEBUG 모드에서 dispatcher.log를 확인하십시오. 다음을 확인하는 데 도움이 됩니다. `token.json` 파일이 필터에 의해 캐시되거나 차단되지 않는지 확인합니다. 다음과 유사한 메시지가 표시되어야 합니다.\
`... checking [/libs/granite/csrf/token.json]  `
`... request URL not in cache rules: /libs/granite/csrf/token.json`\
`... cache-action for [/libs/granite/csrf/token.json]: NONE`

Apache `access_log`에서 요청이 성공하고 있는지 확인할 수도 있습니다. ``/libs/granite/csrf/token.json에 대한 요청은 HTTP 200 상태 코드를 반환해야 합니다.
