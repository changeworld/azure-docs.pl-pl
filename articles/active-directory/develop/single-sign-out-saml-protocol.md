---
title: Protokół SAML wylogowywania się z usługi Azure | Dokumenty firmy Microsoft
description: W tym artykule opisano protokół SAML pojedynczego wylogowania w usłudze Azure Active Directory
services: active-directory
documentationcenter: .net
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 0e4aa75d-d1ad-4bde-a94c-d8a41fb0abe6
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/19/2017
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: hirsin
ms.openlocfilehash: 95d3deff73ce357f012b15a7fc1cfa3decdb4bda
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76701369"
---
# <a name="single-sign-out-saml-protocol"></a>Protokół SAML wylogowania jednokrotnego

Usługa Azure Active Directory (Azure AD) obsługuje profil pojedynczego wylogowania przeglądarki sieci Web SAML 2.0. Aby wylogowanie jednokrotne działało poprawnie, **logoutURL** dla aplikacji musi być jawnie zarejestrowany w usłudze Azure AD podczas rejestracji aplikacji. Usługa Azure AD używa funkcji LogoutURL do przekierowywania użytkowników po ich wylogowaniu.

Na poniższym diagramie przedstawiono przepływ pracy procesu wylogowania jednokrotnego usługi Azure AD.

![Przepływ pracy wylogowywania pojedynczego wylogowywania usługi Azure AD](./media/single-sign-out-saml-protocol/active-directory-saml-single-sign-out-workflow.png)

## <a name="logoutrequest"></a>Prośba o wylogowanie
Usługa w chmurze wysyła `LogoutRequest` komunikat do usługi Azure AD, aby wskazać, że sesja została zakończona. Poniższy fragment przedstawia `LogoutRequest` przykładowy element.

```
<samlp:LogoutRequest xmlns="urn:oasis:names:tc:SAML:2.0:metadata" ID="idaa6ebe6839094fe4abc4ebd5281ec780" Version="2.0" IssueInstant="2013-03-28T07:10:49.6004822Z" xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
  <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://www.workaad.com</Issuer>
  <NameID xmlns="urn:oasis:names:tc:SAML:2.0:assertion"> Uz2Pqz1X7pxe4XLWxV9KJQ+n59d573SepSAkuYKSde8=</NameID>
</samlp:LogoutRequest>
```

### <a name="logoutrequest"></a>Prośba o wylogowanie
Element `LogoutRequest` wysłany do usługi Azure AD wymaga następujących atrybutów:

* `ID`- Identyfikuje to wniosek o wylogowanie. Wartość nie `ID` powinna zaczynać się od liczby. Typowym rozwiązaniem jest dołączyć **identyfikator** do reprezentacji ciągu identyfikatora GUID.
* `Version`- Ustaw wartość tego elementu na **2.0**. Ta wartość jest wymagana.
* `IssueInstant`- Jest `DateTime` to ciąg o wartości współrzędnych czasu uniwersalnego (UTC) i [formacie w obie strony ("o")..](https://msdn.microsoft.com/library/az4se3k1.aspx) Usługa Azure AD oczekuje wartości tego typu, ale nie wymusza jej.

### <a name="issuer"></a>Wystawca
Element `Issuer` w `LogoutRequest` musi dokładnie odpowiadać jednej z **ServicePrincipalNames** w usłudze w chmurze w usłudze Azure AD. Zazwyczaj jest to ustawione na **identyfikator URI identyfikatora aplikacji,** który jest określony podczas rejestracji aplikacji.

### <a name="nameid"></a>NazwaID
Wartość `NameID` elementu musi dokładnie odpowiadać `NameID` użytkownikowi, który jest wylogowywany.

## <a name="logoutresponse"></a>Wylogujodpowiedzialność
Usługa Azure `LogoutResponse` AD wysyła `LogoutRequest` w odpowiedzi na element. Poniższy fragment przedstawia `LogoutResponse`próbkę .

```
<samlp:LogoutResponse ID="_f0961a83-d071-4be5-a18c-9ae7b22987a4" Version="2.0" IssueInstant="2013-03-18T08:49:24.405Z" InResponseTo="iddce91f96e56747b5ace6d2e2aa9d4f8c" xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
  <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://sts.windows.net/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
  <samlp:Status>
    <samlp:StatusCode Value="urn:oasis:names:tc:SAML:2.0:status:Success" />
  </samlp:Status>
</samlp:LogoutResponse>
```

### <a name="logoutresponse"></a>Wylogujodpowiedzialność
Usługa Azure `ID`AD `Version` `IssueInstant` ustawia , `LogoutResponse` i wartości w elemencie. Ustawia również `InResponseTo` element do wartości `ID` atrybutu, `LogoutRequest` który wywołał odpowiedź.

### <a name="issuer"></a>Wystawca
Usługa Azure AD `https://login.microsoftonline.com/<TenantIdGUID>/` ustawia \<tę wartość na miejsce, w którym> TenantIdGUID jest identyfikatorem dzierżawy dzierżawy usługi Azure AD.

Aby ocenić wartość `Issuer` elementu, należy użyć wartości identyfikatora **aplikacji identyfikatora URI** pod warunkiem podczas rejestracji aplikacji.

### <a name="status"></a>Stan
Usługa Azure AD `StatusCode` używa `Status` elementu w elemencie, aby wskazać powodzenie lub niepowodzenie wylogowywania się. Gdy próba wylogowania `StatusCode` zakończy się niepowodzeniem, element może również zawierać niestandardowe komunikaty o błędach.
