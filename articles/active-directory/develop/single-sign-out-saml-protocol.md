---
title: Protokół SAML logowania jednokrotnego na platformie Azure | Microsoft Docs
description: W tym artykule opisano protokół SAML logowania jednokrotnego w Azure Active Directory
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
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2020
ms.locfileid: "76701369"
---
# <a name="single-sign-out-saml-protocol"></a>Protokół SAML wylogowania jednokrotnego

Azure Active Directory (Azure AD) obsługuje profil rejestracji jednokrotnej w przeglądarce internetowej protokołu SAML 2,0. Aby logowanie jednokrotne działało prawidłowo, **LogoutURL** aplikacji musi być jawnie zarejestrowana w usłudze Azure AD podczas rejestracji aplikacji. Usługa Azure AD korzysta z LogoutURL, aby przekierować użytkowników po ich wylogowaniu.

Na poniższym diagramie przedstawiono przepływ pracy procesu rejestracji jednokrotnej usługi Azure AD.

![Przepływ pracy rejestracji jednokrotnej usługi Azure AD](./media/single-sign-out-saml-protocol/active-directory-saml-single-sign-out-workflow.png)

## <a name="logoutrequest"></a>LogoutRequest
Usługa w chmurze wysyła wiadomość `LogoutRequest` do usługi Azure AD w celu wskazania, że sesja została przerwana. Poniższy fragment przedstawia przykładowy element `LogoutRequest`.

```
<samlp:LogoutRequest xmlns="urn:oasis:names:tc:SAML:2.0:metadata" ID="idaa6ebe6839094fe4abc4ebd5281ec780" Version="2.0" IssueInstant="2013-03-28T07:10:49.6004822Z" xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
  <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://www.workaad.com</Issuer>
  <NameID xmlns="urn:oasis:names:tc:SAML:2.0:assertion"> Uz2Pqz1X7pxe4XLWxV9KJQ+n59d573SepSAkuYKSde8=</NameID>
</samlp:LogoutRequest>
```

### <a name="logoutrequest"></a>LogoutRequest
Element `LogoutRequest` wysyłany do usługi Azure AD wymaga następujących atrybutów:

* `ID` — identyfikuje żądanie wylogowania. Wartość `ID` nie powinna rozpoczynać się od cyfry. Typowym sposobem jest dołączenie **identyfikatora** do ciągu REPREZENTUJĄCEGO identyfikator GUID.
* `Version` — ustaw wartość tego elementu na **2,0**. Ta wartość jest wymagana.
* `IssueInstant` — jest to ciąg `DateTime` z wartością uniwersalnego czasu koordynowanego (UTC) i [formatem błądzenia ("o")](https://msdn.microsoft.com/library/az4se3k1.aspx). Usługa Azure AD oczekuje wartości tego typu, ale nie wymusza jej.

### <a name="issuer"></a>Wystawca
Element `Issuer` w `LogoutRequest` musi dokładnie pasować do jednego z **ServicePrincipalNames** w usłudze w chmurze w usłudze Azure AD. Zwykle jest to **Identyfikator URI aplikacji** określony podczas rejestracji aplikacji.

### <a name="nameid"></a>NameID
Wartość elementu `NameID` musi być dokładnie zgodna z `NameID` użytkownika, który jest wylogowany.

## <a name="logoutresponse"></a>LogoutResponse
Usługa Azure AD wysyła `LogoutResponse` w odpowiedzi na element `LogoutRequest`. Poniższy fragment przedstawia przykład `LogoutResponse`.

```
<samlp:LogoutResponse ID="_f0961a83-d071-4be5-a18c-9ae7b22987a4" Version="2.0" IssueInstant="2013-03-18T08:49:24.405Z" InResponseTo="iddce91f96e56747b5ace6d2e2aa9d4f8c" xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
  <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://sts.windows.net/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
  <samlp:Status>
    <samlp:StatusCode Value="urn:oasis:names:tc:SAML:2.0:status:Success" />
  </samlp:Status>
</samlp:LogoutResponse>
```

### <a name="logoutresponse"></a>LogoutResponse
Usługa Azure AD ustawia `ID`, `Version` i `IssueInstant` wartości w elemencie `LogoutResponse`. Ustawia również element `InResponseTo` na wartość atrybutu `ID` `LogoutRequest`, który nadaje odpowiedzi.

### <a name="issuer"></a>Wystawca
Usługa Azure AD ustawia tę wartość na `https://login.microsoftonline.com/<TenantIdGUID>/`, gdzie \<TenantIdGUID > jest IDENTYFIKATORem dzierżawy dzierżawy usługi Azure AD.

Aby oszacować wartość `Issuer` elementu, użyj wartości **identyfikatora URI aplikacji** podanej podczas rejestracji aplikacji.

### <a name="status"></a>Stan
Usługa Azure AD używa elementu `StatusCode` w elemencie `Status`, aby wskazać powodzenie lub niepowodzenie wylogowywania. Gdy próba wylogowania nie powiedzie się, element `StatusCode` może również zawierać niestandardowe komunikaty o błędach.
