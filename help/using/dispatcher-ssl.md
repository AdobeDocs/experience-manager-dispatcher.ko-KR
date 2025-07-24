---
title: Dispatcher에 SSL 사용
description: SSL 연결을 사용하여 AEM과 통신하도록 Dispatcher를 구성하는 방법에 대해 알아봅니다.
contentOwner: User
products: SG_EXPERIENCEMANAGER/DISPATCHER
topic-tags: dispatcher
content-type: reference
index: y
internal: n
snippet: y
exl-id: ec378409-ddb7-4917-981d-dbf2198aca98
source-git-commit: c41b4026a64f9c90318e12de5397eb4c116056d9
workflow-type: tm+mt
source-wordcount: '1305'
ht-degree: 91%

---

# Dispatcher에 SSL 사용 {#using-ssl-with-dispatcher}

Dispatcher와 렌더링 컴퓨터 간에 SSL 연결을 사용합니다.

* [단방향 SSL](#use-ssl-when-dispatcher-connects-to-aem)
* [상호 SSL](#configuring-mutual-ssl-between-dispatcher-and-aem)

>[!NOTE]
>
>SSL 인증서와 관련된 작업은 서드파티 제품에 바인딩됩니다. Adobe Platinum 유지 관리 및 지원 계약이 적용되지 않습니다.

## Dispatcher이 AEM에 연결할 때 SSL 사용 {#use-ssl-when-dispatcher-connects-to-aem}

SSL 연결을 사용하여 AEM 또는 CQ 렌더링 인스턴스와 통신하도록 Dispatcher를 구성합니다.

Dispatcher를 구성하기 전에 SSL을 사용하도록 AEM 또는 CQ를 구성하십시오. 자세한 내용은 다음을 참조하십시오.

* [SSL/TLS (기본값)](https://experienceleague.adobe.com/ko/docs/experience-manager-65/content/security/ssl-by-default)
* [AEM에서 SSL 마법사 사용](https://experienceleague.adobe.com/ko/docs/experience-manager-learn/foundation/security/use-the-ssl-wizard)

### SSL 관련 요청 헤더 {#ssl-related-request-headers}

Dispatcher가 HTTPS 요청을 수신하면 Dispatcher는 AEM 또는 CQ로 보내는 후속 요청에 다음 헤더를 포함합니다.

* `X-Forwarded-SSL`
* `X-Forwarded-SSL-Cipher`
* `X-Forwarded-SSL-Keysize`
* `X-Forwarded-SSL-Session-ID`

`mod_ssl`이 있는 Apache-2.4를 통한 요청에는 다음 예와 유사한 헤더가 포함됩니다.

```shell
X-Forwarded-SSL: on
X-Forwarded-SSL-Cipher: DHE-RSA-AES256-SHA
X-Forwarded-SSL-Session-ID: 814825E8CD055B4C166C2EF6D75E1D0FE786FFB29DEB6DE1E239D5C771CB5B4D
```

### SSL을 사용하도록 Dispatcher 구성 {#configuring-dispatcher-to-use-ssl}

SSL을 통해 AEM 또는 CQ와 연결하도록 Dispatcher를 구성하려면 [dispatcher.any](dispatcher-configuration.md) 파일에 다음 속성이 필요합니다.

* HTTPS 요청을 처리하는 가상 호스트입니다.
* 가상 호스트의 `renders` 섹션에는 HTTPS를 사용하는 CQ 또는 AEM 인스턴스의 호스트 이름과 포트를 식별하는 항목이 포함됩니다.
* `renders` 항목은 `1`값의 `secure` 라는 속성을 포함합니다.

참고: 필요한 경우 HTTP 요청을 처리하기 위해 다른 가상 호스트를 만듭니다.

다음 예제 `dispatcher.any` 파일은 호스트 `localhost` 및 포트 `8443`에서 실행 중인 CQ 인스턴스에 HTTPS를 사용하여 연결하기 위한 속성 값을 보여 줍니다.

```
/farms
{
   /secure
   { 
      /virtualhosts
      {
         # select this farm for all incoming HTTPS requests
         "https://*"
      }
      /renders
      {
      /0001
         {
            # hostname or IP of the render
            /hostname "localhost"
            # port of the render
            /port "8443"
            # connect via HTTPS
            /secure "1"
         }
      }
     # the rest of the properties are omitted
   }

   /non-secure
   { 
      /virtualhosts
      {
         # select this farm for all incoming HTTP requests
         "http://*"
      }
      /renders
      {
         /0001
      {
         # hostname or IP of the render
         /hostname "localhost"
         # port of the render
         /port "4503"
      }
   }
    # the rest of the properties are omitted
}
```

## Dispatcher과 AEM 간의 상호 SSL 구성 {#configuring-mutual-ssl-between-dispatcher-and-aem}

상호 SSL을 사용하도록 Dispatcher와 렌더링 컴퓨터(일반적으로 AEM 또는 CQ 게시 인스턴스) 간의 연결을 구성합니다.

* Dispatcher는 SSL을 통해 렌더링 인스턴스에 연결합니다.
* 렌더링 인스턴스는 Dispatcher 인증서의 유효성을 확인합니다.
* Dispatcher는 렌더링 인스턴스 인증서의 CA가 신뢰할 수 있는지 확인합니다.
* (선택 사항) Dispatcher는 렌더링 인스턴스의 인증서가 렌더링 인스턴스의 서버 주소와 일치하는지 확인합니다.

상호 SSL을 구성하려면 신뢰할 수 있는 인증 기관(CA)에서 서명한 인증서가 필요합니다. 자체 서명된 인증서는 적절하지 않습니다. 직접 CA 역할을 하거나 서드파티 CA의 서비스를 사용하여 인증서에 서명할 수 있습니다. 상호 SSL을 구성하려면 다음 항목이 필요합니다.

* 렌더링 인스턴스 및 Dispatcher에 대한 서명된 인증서
* CA 인증서 (직접 CA 역할을 하는 경우)
* CA, 인증서 및 인증서 요청을 생성하기 위한 OpenSSL 라이브러리.

상호 SSL을 구성하도록 다음 단계를 수행합니다.

1. 플랫폼을 위한 최신 버전의 Dispatcher를 [설치](dispatcher-install.md)합니다. SSL을 지원하는 Dispatcher 바이너리를 사용합니다. SSL은 파일 이름에 있습니다(예: `dispatcher-apache2.4-linux-x86-64-ssl10-4.1.7.tar`).
1. Dispatcher 및 렌더링 인스턴스를 위한 [CA 서명 인증서를 생성하거나 획득합니다](dispatcher-ssl.md#main-pars-title-3).
1. [렌더링 인증서를 포함하는 키 저장소를 생성하고](dispatcher-ssl.md#main-pars-title-6) 렌더링의 HTTP 서비스를 구성합니다.
1. 상호 SSL을 위한 [Dispatcher 웹 서버 모듈을 구성합니다](dispatcher-ssl.md#main-pars-title-4).

### CA 서명 인증서 생성 또는 획득 {#creating-or-obtaining-ca-signed-certificates}

게시 인스턴스 및 Dispatcher를 인증하는 CA 서명 인증서를 생성하거나 획득합니다.

#### 내 CA 만들기 {#creating-your-ca}

직접 CA 역할을 하는 경우 [OpenSSL](https://www.openssl.org/)을 사용하여 서버 및 클라이언트 인증서에 서명하는 인증 기관을 생성합니다. OpenSSL 라이브러리가 설치되어 있어야 합니다. 서드파티 CA를 사용하는 경우 이 절차를 수행하지 마십시오.

1. 터미널을 열고 현재 디렉터리를 `CA.sh` 파일이 포함된 디렉터리로 변경합니다(예: `/usr/local/ssl/misc`).
1. CA를 생성하려면 다음 명령을 입력한 다음 메시지가 표시될 때 값을 제공합니다

   ```shell
   ./CA.sh -newca
   ```

   >[!NOTE]
   >
   >`openssl.cnf` 파일의 여러 속성은 CA.sh 스크립트의 동작을 제어합니다. CA를 만들기 전에 필요에 따라 이 파일을 편집합니다.

#### 인증서 만들기 {#creating-the-certificates}

OpenSSL을 사용하여 서드파티 CA로 보내거나 자체 CA로 서명할 인증서 요청을 생성합니다.

인증서를 만들 때 OpenSSL은 일반 이름 속성을 사용하여 인증서 소유자를 식별합니다. 렌더링 인스턴스의 인증서에 대해 인증서를 수락하도록 Dispatcher를 구성하는 경우 인스턴스 컴퓨터의 호스트 이름을 일반 이름으로 사용합니다. 게시 인스턴스의 호스트 이름과 일치하는 경우에만 이 절차를 수행하십시오. [DispatcherCheckPeerCN](dispatcher-ssl.md#main-pars-title-11) 속성을 참조하십시오.

1. 터미널을 열고 현재 디렉터리를 OpenSSL 라이브러리의 CH.sh 파일이 포함된 디렉터리로 변경합니다.
1. 다음 명령을 입력하고 메시지가 표시되면 값을 제공합니다. 필요한 경우 게시 인스턴스의 호스트 이름을 일반 이름으로 사용하십시오. 호스트 이름은 렌더링의 IP 주소에 대한 DNS 확인 가능 이름입니다.

   ```shell
   ./CA.sh -newreq
   ```

   서드파티 CA를 사용하는 경우 newreq.pem 파일을 CA로 보내 서명합니다. 직접 CA 역할을 하는 경우 3단계로 이동합니다.

1. CA의 인증서를 사용하여 인증서에 서명하려면 다음 명령을 입력합니다.

   ```shell
   ./CA.sh -sign
   ```

   CA 관리 파일이 포함된 디렉터리에 및`newcert.pem` 및 `newkey.pem`이라는 두 개의 파일이 생성됩니다. 이러한 두 파일은 각각 렌더링 컴퓨터의 공개 인증서와 개인 키입니다.

1. `newcert.pem`을 `rendercert.pem`으로 이름을 바꾸고 `newkey.pem`을 `renderkey.pem`으로 이름을 바꿉니다.
1. 2단계와 3단계를 반복하여 Dispatcher 모듈을 위한 인증서와 공개 키를 만듭니다. Dispatcher 인스턴스에 고유한 일반 이름을 사용하는지 확인하십시오.
1. `newcert.pem`을 `dispcert.pem`으로 이름을 바꾸고 `newkey.pem`을 `dispkey.pem`으로 이름을 바꿉니다.

### 렌더링 컴퓨터에서 SSL 구성 {#configuring-ssl-on-the-render-computer}

`rendercert.pem` 및 `renderkey.pem` 파일을 사용하여 렌더링 인스턴스에서 SSL을 구성합니다.

#### 렌더링 인증서를 JKS(Java™ KeyStore) 형식으로 변환 {#converting-the-render-certificate-to-jks-format}

다음 명령을 사용하여 PEM 파일인 렌더링 인증서를 PKCS#12 파일로 변환합니다. 또한 렌더링 인증서에 서명한 CA의 인증서를 포함합니다.

1. 터미널 창에서 현재 디렉터리를 렌더링 인증서 및 개인 키 위치로 변경합니다.
1. PEM 파일인 렌더링 인증서를 PKCS#12 파일로 변환하려면 다음 명령을 입력합니다. 또한 렌더링 인증서에 서명한 CA의 인증서를 포함합니다.

   ```shell
   openssl pkcs12 -export -in rendercert.pem -inkey renderkey.pem  -certfile demoCA/cacert.pem -out rendercert.p12
   ```

1. PKCS#12 파일을 Java™ KeyStore(JKS) 포맷으로 변환하려면 다음 명령을 입력합니다.

   ```shell
   keytool -importkeystore -srckeystore servercert.p12 -srcstoretype pkcs12 -destkeystore render.keystore
   ```

1. Java™ KeyStore는 기본 별칭을 사용하여 작성됩니다. 원하는 경우 별칭을 변경합니다.

   ```shell
   keytool -changealias -alias 1 -destalias jettyhttp -keystore render.keystore
   ```

#### 렌더링의 Truststore에 CA 인증서 추가 {#adding-the-ca-cert-to-the-render-s-truststore}

CA 역할을 하는 경우 CA 인증서를 키 저장소로 가져옵니다. 그런 다음 키 저장소를 신뢰하도록 렌더링 인스턴스를 실행하는 JVM을 구성합니다.

<!-- 

Comment Type: remark
Last Modified By: unknown unknown (sbroders@adobe.com)
Last Modified Date: 2014-08-12T13:11:21.401-0400

<p style="font-family: tahoma, arial, helvetica, sans-serif; font-size: 12px;">The jetty http service has properties to specify trusted CA certificates for mutual SSL for 6.0. Whether they are operable is undetetermined. See https://issues.adobe.com/browse/DOC-4738.</p> 
<p style="font-family: tahoma, arial, helvetica, sans-serif; font-size: 12px;"> </p> 
<p style="font-family: tahoma, arial, helvetica, sans-serif; font-size: 12px;">For 5.6.1, you would specify the system property javax.net.ssl.trustStore, using the path to cacerts as value.</p>

 -->

1. 텍스트 편집기를 사용하여 cacert.pem 파일을 열고 다음 줄 앞에 있는 모든 텍스트를 제거합니다.

   `-----BEGIN CERTIFICATE-----`

1. 다음 명령을 사용하여 인증서를 키 저장소로 가져옵니다.

   ```shell
   keytool -import -keystore cacerts.keystore -alias myca -storepass password -file cacert.pem
   ```

1. 키 저장소를 신뢰하도록 렌더링 인스턴스를 실행하는 JVM을 구성하려면 다음 시스템 속성을 사용하십시오.

   ```shell
   -Djavax.net.ssl.trustStore=<location of cacerts.keystore>
   ```

   예를 들어 crx-quickstart/bin/quickstart 스크립트를 사용하여 게시 인스턴스를 시작하는 경우 CQ_JVM_OPTS 속성을 수정할 수 있습니다.

   ```shell
   CQ_JVM_OPTS='-server -Xmx2048m -XX:MaxPermSize=512M -Djavax.net.ssl.trustStore=/usr/lib/cq6.0/publish/ssl/cacerts.keystore'
   ```

#### 렌더링 인스턴스 구성 {#configuring-the-render-instance}

렌더링 인스턴스의 HTTP 서비스를 구성하여 SSL을 사용하려면 *`Enable SSL on the Publish Instance`* 섹션의 지침에 따라 렌더링 인증서를 사용합니다.

* AEM 6.2: [SSL을 통한 HTTP 활성화](https://experienceleague.adobe.com/ko/docs/experience-manager-release-information/aem-release-updates/previous-updates/aem-previous-versions)
* AEM 6.1: [SSL을 통한 HTTP 활성화](https://experienceleague.adobe.com/ko/docs/experience-manager-release-information/aem-release-updates/previous-updates/aem-previous-versions)
* 이전 AEM 버전: [이 페이지](https://experienceleague.adobe.com/ko/docs/experience-manager-release-information/aem-release-updates/previous-updates/aem-previous-versions)를 참조하십시오.

### Dispatcher 모듈에 대한 SSL 구성 {#configuring-ssl-for-the-dispatcher-module}

Dispatcher가 상호 SSL을 사용하도록 구성하려면 Dispatcher 인증서를 준비한 후 웹 서버 모듈을 구성합니다.

### 통합 Dispatcher 인증서 만들기 {#creating-a-unified-dispatcher-certificate}

Dispatcher 인증서와 암호화되지 않은 개인 키를 단일 PEM 파일로 결합합니다. 텍스트 편집기 또는 `cat` 명령을 사용하여 다음 예제와 유사한 파일을 생성하십시오.

1. 터미널을 열고 현재 디렉터리를 dispkey.pem 파일 위치로 변경합니다.
1. 개인 키를 해독하려면 다음 명령을 입력하십시오.

   ```shell
   openssl rsa -in dispkey.pem -out dispkey_unencrypted.pem
   ```

1. 텍스트 편집기 또는 `cat` 명령을 사용하여 다음 예와 유사한 단일 파일에서 암호화되지 않은 개인 키와 인증서를 결합하십시오.

   ```xml
   -----BEGIN RSA PRIVATE KEY-----
   MIICxjBABgkqhkiG9w0B...
   ...M2HWhDn5ywJsX
   -----END RSA PRIVATE KEY-----
   -----BEGIN CERTIFICATE-----
   MIIC3TCCAk...
   ...roZAs=
   -----END CERTIFICATE-----
   ```

### Dispatcher에 사용할 인증서 지정 {#specifying-the-certificate-to-use-for-dispatcher}

[Dispatcher 모듈 구성](dispatcher-install.md#main-pars-55-35-1022) (`httpd.conf`)에 다음 속성을 추가하십시오.

* `DispatcherCertificateFile`: 공개 인증서와 암호화되지 않은 개인 키가 포함된 Dispatcher 통합 인증서 파일의 경로입니다. 이 파일은 SSL 서버가 Dispatcher 클라이언트 인증서를 요청할 때 사용됩니다.
* `DispatcherCACertificateFile`: CA 인증서 파일에 대한 경로. SSL 서버가 루트 기관이 신뢰하지 않는 CA를 제시하는 경우에 사용됩니다.
* `DispatcherCheckPeerCN`: 원격 서버 인증서에 대한 호스트 이름 검사를 활성화(`On`) 또는 비활성화(`Off`)할지 여부입니다.

다음 코드는 구성의 예입니다.

```xml
<IfModule disp_apache2.c>
  DispatcherConfig conf/dispatcher.any
  DispatcherLog    logs/dispatcher.log
  DispatcherLogLevel 3
  DispatcherNoServerHeader 0
  DispatcherDeclineRoot 0
  DispatcherUseProcessedURL 0
  DispatcherPassError 0
  DispatcherCertificateFile disp_unified.pem
  DispatcherCACertificateFile cacert.pem
  DispatcherCheckPeerCN On
</IfModule>
```
