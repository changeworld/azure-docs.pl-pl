---
title: Azure pojedynczego Wyloguj protokołu SAML | Dokumentacja firmy Microsoft
description: W tym artykule opisano jednego protokołu SAML Sign-Out w usłudze Azure Active Directory
services: active-directory
documentationcenter: .net
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 0e4aa75d-d1ad-4bde-a94c-d8a41fb0abe6
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2017
ms.author: celested
ms.custom: aaddev
ms.reviewer: hirsin
ms.openlocfilehash: c8373df67adbb93e25ab5a31a254efe70581d32d
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/22/2018
ms.locfileid: "36317681"
---
# <a name="single-sign-out-saml-protocol"></a>Protokół pojedynczego SAML wylogowania

Azure Active Directory (Azure AD) obsługuje SAML 2.0 sieci web przeglądarce jeden profil wylogowania. Dla pojedynczego wylogowania działał prawidłowo **LogoutURL** dla aplikacji musi być jawnie zarejestrowana w usłudze Azure AD podczas rejestracji aplikacji. Usługi Azure AD używa LogoutURL przekierowywać użytkowników po ich zarejestrowaniu.

Na poniższym diagramie przedstawiono przepływ pracy pojedynczy proces wylogowywania usługi Azure AD.

![Azure AD logowania jednokrotnego limit przepływu pracy](media/active-directory-single-sign-out-protocol-reference/active-directory-saml-single-sign-out-workflow.png)

## <a name="logoutrequest"></a>LogoutRequest
Wysyła usługi chmury `LogoutRequest` komunikatu do usługi Azure AD, aby wskazać, że sesja została zakończona. Poniższy fragment przedstawiono przykładowe `LogoutRequest` elementu.

```
<samlp:LogoutRequest xmlns="urn:oasis:names:tc:SAML:2.0:metadata" ID="idaa6ebe6839094fe4abc4ebd5281ec780" Version="2.0" IssueInstant="2013-03-28T07:10:49.6004822Z" xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
  <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://www.workaad.com</Issuer>
  <NameID xmlns="urn:oasis:names:tc:SAML:2.0:assertion"> Uz2Pqz1X7pxe4XLWxV9KJQ+n59d573SepSAkuYKSde8=</NameID>
</samlp:LogoutRequest>
```

### <a name="logoutrequest"></a>LogoutRequest
`LogoutRequest` Element wysyłane do usługi Azure AD wymaga następujących atrybutów:

* `ID` -Identyfikuje wylogowania żądania. Wartość `ID` nie powinny rozpoczynać się cyfrą. Typowy rozwiązaniem jest Dołącz **identyfikator** reprezentacji ciągu identyfikatora GUID.
* `Version` — Wartość tego elementu, aby ustawić **2.0**. Ta wartość jest wymagana.
* `IssueInstant` -To jest `DateTime` ciąg o wartości koordynować czasu uniwersalnego (UTC) i [obustronne formatu ("o")](https://msdn.microsoft.com/library/az4se3k1.aspx). Usługi Azure AD oczekuje wartości tego typu, ale nie jej wymusić.

### <a name="issuer"></a>Wystawca
`Issuer` Element `LogoutRequest` musi dokładnie pasować **ServicePrincipalNames** w usłudze w chmurze w usłudze Azure AD. Zwykle ta jest równa **identyfikator URI aplikacji** określonym podczas rejestracji aplikacji.

### <a name="nameid"></a>NameID
Wartość `NameID` elementu musi dokładnie odpowiadać `NameID` użytkownika, który jest wylogowany.

## <a name="logoutresponse"></a>LogoutResponse
Azure AD wysyła `LogoutResponse` w odpowiedzi na `LogoutRequest` elementu. Poniższy fragment przedstawiono przykładowe `LogoutResponse`.

```
<samlp:LogoutResponse ID="_f0961a83-d071-4be5-a18c-9ae7b22987a4" Version="2.0" IssueInstant="2013-03-18T08:49:24.405Z" InResponseTo="iddce91f96e56747b5ace6d2e2aa9d4f8c" xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
  <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://sts.windows.net/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
  <samlp:Status>
    <samlp:StatusCode Value="urn:oasis:names:tc:SAML:2.0:status:Success" />
  </samlp:Status>
</samlp:LogoutResponse>
```

### <a name="logoutresponse"></a>LogoutResponse
Azure AD zestawy `ID`, `Version` i `IssueInstant` wartości w `LogoutResponse` elementu. Ustawia również `InResponseTo` elementu na wartość `ID` atrybutu `LogoutRequest` ujawniająca odpowiedzi.

### <a name="issuer"></a>Wystawca
Usługi Azure AD ustawia tę wartość na `https://login.microsoftonline.com/<TenantIdGUID>/` gdzie <TenantIdGUID> jest identyfikator dzierżawcy dzierżawy usługi Azure AD.

Aby obliczyć wartość `Issuer` element, należy użyć wartości **identyfikator URI aplikacji** podane podczas rejestracji aplikacji.

### <a name="status"></a>Stan
Używa usługi Azure AD `StatusCode` element `Status` elementu, aby wskazywać powodzenie lub niepowodzenie wylogowania. W przypadku awarii próby wylogowania `StatusCode` element może także zawierać niestandardowe komunikaty o błędach.
