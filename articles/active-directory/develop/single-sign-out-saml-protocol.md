---
title: Azure logowanie się protokołu SAML | Dokumentacja firmy Microsoft
description: W tym artykule opisano protokół SAML wylogowania jednokrotnego w usłudze Azure Active Directory
services: active-directory
documentationcenter: .net
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 0e4aa75d-d1ad-4bde-a94c-d8a41fb0abe6
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2017
ms.author: celested
ms.custom: aaddev
ms.reviewer: hirsin
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0d4f9c69d6ddcbef96a88a0df10c3b0974cd5b74
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2019
ms.locfileid: "58437088"
---
# <a name="single-sign-out-saml-protocol"></a>Protokół SAML wylogowania pojedynczego

Usługa Azure Active Directory (Azure AD) obsługuje SAML 2.0 sieci web przeglądarce jeden profil wylogowania. Dla pojedynczego poprawne **LogoutURL** dla aplikacji, które muszą być jawnie zarejestrowane za pomocą usługi Azure AD podczas rejestracji aplikacji. Usługa Azure AD używa LogoutURL przekierowywać użytkowników po ich zarejestrowaniu.

Na poniższym diagramie przedstawiono przepływ pracy usługi Azure AD pojedynczego procesu wylogowywania.

![Usługa Azure AD logowania się przepływu pracy](./media/single-sign-out-saml-protocol/active-directory-saml-single-sign-out-workflow.png)

## <a name="logoutrequest"></a>LogoutRequest
Wysyła usługi w chmurze `LogoutRequest` komunikatu do usługi Azure AD, aby wskazać, że sesja została przerwana. Poniższy fragment prezentuje próbkę `LogoutRequest` elementu.

```
<samlp:LogoutRequest xmlns="urn:oasis:names:tc:SAML:2.0:metadata" ID="idaa6ebe6839094fe4abc4ebd5281ec780" Version="2.0" IssueInstant="2013-03-28T07:10:49.6004822Z" xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
  <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://www.workaad.com</Issuer>
  <NameID xmlns="urn:oasis:names:tc:SAML:2.0:assertion"> Uz2Pqz1X7pxe4XLWxV9KJQ+n59d573SepSAkuYKSde8=</NameID>
</samlp:LogoutRequest>
```

### <a name="logoutrequest"></a>LogoutRequest
`LogoutRequest` Element wysyłane do usługi Azure AD wymaga następujących atrybutów:

* `ID` -Identyfikuje żądania wyrejestrowywania. Wartość `ID` nie powinien zaczynać się liczbą. Typowym rozwiązaniem jest Dołącz **identyfikator** na reprezentację ciągu identyfikatora GUID.
* `Version` -Wartość tego elementu **2.0**. Ta wartość jest wymagana.
* `IssueInstant` — To `DateTime` ciągów o wartości koordynowanie czasu uniwersalnego (UTC) i [obustronne formatu ("o")](https://msdn.microsoft.com/library/az4se3k1.aspx). Usługa Azure AD oczekuje wartości tego typu, ale nie jej wymusić.

### <a name="issuer"></a>Wystawca
`Issuer` Element `LogoutRequest` musi dokładnie pasować **ServicePrincipalNames** w usłudze w chmurze w usłudze Azure AD. Zazwyczaj jest ono ustawione na **identyfikator URI Identyfikatora aplikacji** określona podczas rejestracji aplikacji.

### <a name="nameid"></a>NameID
Wartość `NameID` element musi dokładnie odpowiadać `NameID` użytkownika, który zostanie jest wylogowany.

## <a name="logoutresponse"></a>LogoutResponse
Usługa Azure AD wysyła `LogoutResponse` w odpowiedzi na `LogoutRequest` elementu. Poniższy fragment prezentuje próbkę `LogoutResponse`.

```
<samlp:LogoutResponse ID="_f0961a83-d071-4be5-a18c-9ae7b22987a4" Version="2.0" IssueInstant="2013-03-18T08:49:24.405Z" InResponseTo="iddce91f96e56747b5ace6d2e2aa9d4f8c" xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
  <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://sts.windows.net/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
  <samlp:Status>
    <samlp:StatusCode Value="urn:oasis:names:tc:SAML:2.0:status:Success" />
  </samlp:Status>
</samlp:LogoutResponse>
```

### <a name="logoutresponse"></a>LogoutResponse
Usługa Azure AD zestawy `ID`, `Version` i `IssueInstant` wartości w `LogoutResponse` elementu. Ustawia również `InResponseTo` element na wartość `ID` atrybutu `LogoutRequest` ujawniająca odpowiedzi.

### <a name="issuer"></a>Wystawca
Usługa Azure AD ustawi tę wartość na `https://login.microsoftonline.com/<TenantIdGUID>/` gdzie \<TenantIdGUID > jest identyfikator dzierżawy w dzierżawie usługi Azure AD.

Można obliczyć wartości `Issuer` elementu, użyj wartości **identyfikator URI Identyfikatora aplikacji** dostarczonych podczas rejestracji aplikacji.

### <a name="status"></a>Stan
Usługa Azure AD korzysta `StatusCode` element `Status` elementu, aby wskazywać powodzenie lub niepowodzenie wylogowywania. W przypadku awarii po próbie wylogowania `StatusCode` element może także zawierać niestandardowe komunikaty o błędach.
