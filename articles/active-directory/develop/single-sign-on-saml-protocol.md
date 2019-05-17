---
title: Usługa Azure funkcji logowania jednokrotnego protokołu SAML | Dokumentacja firmy Microsoft
description: W tym artykule opisano protokołu pojedynczy znak na SAML w usłudze Azure Active Directory
services: active-directory
documentationcenter: .net
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: ad8437f5-b887-41ff-bd77-779ddafc33fb
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2017
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: hirsin
ms.collection: M365-identity-device-management
ms.openlocfilehash: 593f07b27fec16c3df90a073479effb130bc5721
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/11/2019
ms.locfileid: "65545279"
---
# <a name="single-sign-on-saml-protocol"></a>Protokół SAML logowania jednokrotnego

W tym artykule opisano żądania uwierzytelniania SAML 2.0 i odpowiedzi, obsługiwanych przez usługi Azure Active Directory (Azure AD) do logowania jednokrotnego.

Na poniższym diagramie protokołu opisuje pojedynczej sekwencji logowania jednokrotnego. Usługi w chmurze (usługodawca) używa wiązaniu przekierowania HTTP do przekazania `AuthnRequest` — element (żądanie uwierzytelnienia) do usługi Azure AD (dostawcy tożsamości). Następnie usługa Azure AD używa metody post protokołu HTTP, powiązanie do opublikowania `Response` element do usługi w chmurze.

![Logowanie jednokrotne przepływu pracy](./media/single-sign-on-saml-protocol/active-directory-saml-single-sign-on-workflow.png)

## <a name="authnrequest"></a>AuthnRequest

Aby żądać uwierzytelniania użytkowników, usług cloud services — Wyślij `AuthnRequest` element do usługi Azure AD. Przykładowe SAML 2.0 `AuthnRequest` może wyglądać następująco:

```
<samlp:AuthnRequest
xmlns="urn:oasis:names:tc:SAML:2.0:metadata"
ID="id6c1c178c166d486687be4aaf5e482730"
Version="2.0" IssueInstant="2013-03-18T03:28:54.1839884Z"
xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
<Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://www.contoso.com</Issuer>
</samlp:AuthnRequest>
```

| Parametr |  | Opis |
| --- | --- | --- |
| ID | Wymagane | Usługa Azure AD używa tego atrybutu, aby wypełnić `InResponseTo` atrybut zwrócona odpowiedź. Identyfikator musi zaczyna się od liczby, więc typowych strategii jest dołączana ciągów, takich jak "id" na reprezentację ciągu identyfikatora GUID. Na przykład `id6c1c178c166d486687be4aaf5e482730` jest poprawnym identyfikatorem. |
| Wersja | Wymagane | Ten parametr powinien być ustawiony na **2.0**. |
| IssueInstant | Wymagane | Jest to ciąg daty/godziny z wartości UTC i [obustronne formatu ("o")](https://msdn.microsoft.com/library/az4se3k1.aspx). Usługa Azure AD oczekuje wartości daty/godziny tego typu, ale nie oceny lub użyj wartości. |
| AssertionConsumerServiceUrl | Opcjonalne | Jeśli nie dostarczono, ten parametr musi być zgodna `RedirectUri` usługi w chmurze w usłudze Azure AD. |
| ForceAuthn | Opcjonalne | Jest to wartość typu boolean. W przypadku opcji true oznacza, że użytkownik będzie zmuszany konieczność ponownego uwierzytelnienia, nawet jeśli mają prawidłowy sesji z usługą Azure AD. |
| IsPassive | Opcjonalne | Jest to wartość logiczna określająca, czy usługi Azure AD powinien uwierzytelnić użytkownika w trybie dyskretnym, bez interakcji z użytkownikiem przy użyciu pliku cookie sesji, jeśli taka istnieje. Jeśli to PRAWDA, usługi Azure AD będzie podejmować próby uwierzytelnienia użytkownika za pomocą pliku cookie sesji. |

Wszystkie inne `AuthnRequest` atrybutów, takich jak zgody, miejsce docelowe, AssertionConsumerServiceIndex, AttributeConsumerServiceIndex i ProviderName są **ignorowane**.

Usługa Azure AD ignoruje także `Conditions` element `AuthnRequest`.

### <a name="issuer"></a>Wystawca

`Issuer` Element `AuthnRequest` musi dokładnie pasować **ServicePrincipalNames** w usłudze w chmurze w usłudze Azure AD. Zazwyczaj jest ono ustawione na **identyfikator URI Identyfikatora aplikacji** określona podczas rejestracji aplikacji.

Fragment SAML zawierający `Issuer` element będzie wyglądać jak w następującym przykładzie:

```
<Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://www.contoso.com</Issuer>
```

### <a name="nameidpolicy"></a>NameIDPolicy

Ten element format Identyfikatora nazwy określonej w odpowiedzi na żądania i jest opcjonalna w `AuthnRequest` elementów wysłanych do usługi Azure AD.

A `NameIdPolicy` element będzie wyglądać jak w następującym przykładzie:

```
<NameIDPolicy Format="urn:oasis:names:tc:SAML:2.0:nameid-format:persistent"/>
```

Jeśli `NameIDPolicy` zostanie podana, możesz uwzględnić jego opcjonalne `Format` atrybutu. `Format` Atrybut może mieć tylko jedną z następujących wartości; wszystkie inne wartości będzie skutkowało błędem.

* `urn:oasis:names:tc:SAML:2.0:nameid-format:persistent`: Usługa Azure Active Directory wystawia oświadczenia NameID jako identyfikator parowania.
* `urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress`: Usługa Azure Active Directory wystawia oświadczenia NameID w formacie adresu e-mail.
* `urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified`: Ta wartość umożliwia usłudze Azure Active Directory, aby wybrać format oświadczenia. Usługa Azure Active Directory wystawia NameID jako identyfikator parowania.
* `urn:oasis:names:tc:SAML:2.0:nameid-format:transient`: Usługa Azure Active Directory wystawia oświadczenia NameID jako wartość losowo generowany unikatowy dla bieżącej operacji logowania jednokrotnego. Oznacza to, że wartość jest tymczasowe i nie może służyć do identyfikowania użytkownika.

Usługa Azure AD ignoruje `AllowCreate` atrybutu.

### <a name="requestauthncontext"></a>RequestAuthnContext
`RequestedAuthnContext` Element określa metody uwierzytelnianie żądaną. Jest to opcjonalne w `AuthnRequest` elementów wysłanych do usługi Azure AD. Usługa Azure AD obsługuje `AuthnContextClassRef` wartości, takich jak `urn:oasis:names:tc:SAML:2.0:ac:classes:Password`.

### <a name="scoping"></a>Wyznaczanie zakresu
`Scoping` Elementu, który zawiera listę dostawców tożsamości, jest opcjonalny w `AuthnRequest` elementów wysłanych do usługi Azure AD.

Jeśli podana, nie dołączaj `ProxyCount` atrybutu, `IDPListOption` lub `RequesterID` elementu, ponieważ nie są one obsługiwane.

### <a name="signature"></a>Podpis
Nie dołączaj `Signature` element `AuthnRequest` elementów, usługa Azure AD nie obsługuje podpisanego żądania uwierzytelniania.

### <a name="subject"></a>Subject
Usługa Azure AD ignoruje `Subject` elementu `AuthnRequest` elementów.

## <a name="response"></a>Odpowiedź
Jeśli żądana logowania zakończy się pomyślnie, usługi Azure AD publikuje odpowiedzi do usługi w chmurze. Odpowiedź pomyślna próba logowania jednokrotnego wyglądają jak w następującym przykładzie:

```
<samlp:Response ID="_a4958bfd-e107-4e67-b06d-0d85ade2e76a" Version="2.0" IssueInstant="2013-03-18T07:38:15.144Z" Destination="https://contoso.com/identity/inboundsso.aspx" InResponseTo="id758d0ef385634593a77bdf7e632984b6" xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
  <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion"> https://login.microsoftonline.com/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
  <ds:Signature xmlns:ds="https://www.w3.org/2000/09/xmldsig#">
    ...
  </ds:Signature>
  <samlp:Status>
    <samlp:StatusCode Value="urn:oasis:names:tc:SAML:2.0:status:Success" />
  </samlp:Status>
  <Assertion ID="_bf9c623d-cc20-407a-9a59-c2d0aee84d12" IssueInstant="2013-03-18T07:38:15.144Z" Version="2.0" xmlns="urn:oasis:names:tc:SAML:2.0:assertion">
    <Issuer>https://login.microsoftonline.com/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
    <ds:Signature xmlns:ds="https://www.w3.org/2000/09/xmldsig#">
      ...
    </ds:Signature>
    <Subject>
      <NameID>Uz2Pqz1X7pxe4XLWxV9KJQ+n59d573SepSAkuYKSde8=</NameID>
      <SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer">
        <SubjectConfirmationData InResponseTo="id758d0ef385634593a77bdf7e632984b6" NotOnOrAfter="2013-03-18T07:43:15.144Z" Recipient="https://contoso.com/identity/inboundsso.aspx" />
      </SubjectConfirmation>
    </Subject>
    <Conditions NotBefore="2013-03-18T07:38:15.128Z" NotOnOrAfter="2013-03-18T08:48:15.128Z">
      <AudienceRestriction>
        <Audience>https://www.contoso.com</Audience>
      </AudienceRestriction>
    </Conditions>
    <AttributeStatement>
      <Attribute Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name">
        <AttributeValue>testuser@contoso.com</AttributeValue>
      </Attribute>
      <Attribute Name="http://schemas.microsoft.com/identity/claims/objectidentifier">
        <AttributeValue>3F2504E0-4F89-11D3-9A0C-0305E82C3301</AttributeValue>
      </Attribute>
      ...
    </AttributeStatement>
    <AuthnStatement AuthnInstant="2013-03-18T07:33:56.000Z" SessionIndex="_bf9c623d-cc20-407a-9a59-c2d0aee84d12">
      <AuthnContext>
        <AuthnContextClassRef> urn:oasis:names:tc:SAML:2.0:ac:classes:Password</AuthnContextClassRef>
      </AuthnContext>
    </AuthnStatement>
  </Assertion>
</samlp:Response>
```

### <a name="response"></a>Odpowiedź

`Response` Element zawiera wynik żądania autoryzacji. Usługa Azure AD zestawy `ID`, `Version` i `IssueInstant` wartości w `Response` elementu. Ustawia również następujące atrybuty:

* `Destination`: Podczas logowania jednokrotnego zakończy się pomyślnie, jest ono ustawione na `RedirectUri` usługodawcy (usługi w chmurze).
* `InResponseTo`: Jest ono ustawione na `ID` atrybutu `AuthnRequest` element, który zainicjował odpowiedzi.

### <a name="issuer"></a>Wystawca

Usługa Azure AD zestawy `Issuer` elementu `https://login.microsoftonline.com/<TenantIDGUID>/` gdzie \<TenantIDGUID > jest identyfikator dzierżawy w dzierżawie usługi Azure AD.

Na przykład odpowiedzi z elementem wystawcy mogłoby wyglądać jak w następującym przykładzie:

```
<Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion"> https://login.microsoftonline.com/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
```

### <a name="status"></a>Stan

`Status` Elementu powoduje powodzenie lub Niepowodzenie logowania jednokrotnego. Zawiera ona `StatusCode` element, który zawiera kod lub zestaw zagnieżdżonych kodów, który reprezentuje stan żądania. Obejmuje również `StatusMessage` element, który zawiera niestandardowe komunikaty o błędach, które są generowane podczas procesu logowania.

<!-- TODO: Add an authentication protocol error reference -->

Następujące przykładowe dane stanowią odpowiedź SAML nieudanej próbie logowania jednokrotnego.

```
<samlp:Response ID="_f0961a83-d071-4be5-a18c-9ae7b22987a4" Version="2.0" IssueInstant="2013-03-18T08:49:24.405Z" InResponseTo="iddce91f96e56747b5ace6d2e2aa9d4f8c" xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
  <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://sts.windows.net/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
  <samlp:Status>
    <samlp:StatusCode Value="urn:oasis:names:tc:SAML:2.0:status:Requester">
      <samlp:StatusCode Value="urn:oasis:names:tc:SAML:2.0:status:RequestUnsupported" />
    </samlp:StatusCode>
    <samlp:StatusMessage>AADSTS75006: An error occurred while processing a SAML2 Authentication request. AADSTS90011: The SAML authentication request property 'NameIdentifierPolicy/SPNameQualifier' is not supported.
Trace ID: 66febed4-e737-49ff-ac23-464ba090d57c
Timestamp: 2013-03-18 08:49:24Z</samlp:StatusMessage>
  </samlp:Status>
```

### <a name="assertion"></a>potwierdzenie

Oprócz `ID`, `IssueInstant` i `Version`, usługi Azure AD ustawia następujące elementy w `Assertion` element odpowiedzi.

#### <a name="issuer"></a>Wystawca

Jest ono ustawione na `https://sts.windows.net/<TenantIDGUID>/`gdzie \<TenantIDGUID > jest identyfikator dzierżawy w dzierżawie usługi Azure AD.

```
<Issuer>https://login.microsoftonline.com/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
```

#### <a name="signature"></a>Podpis

Usługa Azure AD podpisuje potwierdzenia w odpowiedzi na pomyślne logowanie. `Signature` Element zawiera podpis cyfrowy, używanego przez usługę w chmurze do uwierzytelniania źródła, aby sprawdzić integralność potwierdzenia.

Do generowania podpisu cyfrowego, usługa Azure AD używa klucza podpisywania w `IDPSSODescriptor` element jego dokumentu metadanych.

```
<ds:Signature xmlns:ds="https://www.w3.org/2000/09/xmldsig#">
      digital_signature_here
    </ds:Signature>
```

#### <a name="subject"></a>Subject

To ustawienie określa jednostki, będącego przedmiotem oświadczeń do potwierdzenia. Zawiera on `NameID` element, który reprezentuje uwierzytelnionego użytkownika. `NameID` Wartość docelowa identyfikator, który będzie kierowany tylko do dostawcy usług, który jest odbiorców tokenu. Jest trwały — mogą być odwoływane, ale nigdy nie jest ponownie przypisywany. Należy również nieprzezroczysty, ponieważ nie ujawnia niczego o użytkowniku i nie można użyć jako identyfikatora dla zapytań dotyczących atrybutów.

`Method` Atrybutu `SubjectConfirmation` element jest zawsze ustawiony na `urn:oasis:names:tc:SAML:2.0:cm:bearer`.

```
<Subject>
      <NameID>Uz2Pqz1X7pxe4XLWxV9KJQ+n59d573SepSAkuYKSde8=</NameID>
      <SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer">
        <SubjectConfirmationData InResponseTo="id758d0ef385634593a77bdf7e632984b6" NotOnOrAfter="2013-03-18T07:43:15.144Z" Recipient="https://contoso.com/identity/inboundsso.aspx" />
      </SubjectConfirmation>
</Subject>
```

#### <a name="conditions"></a>Warunki

Ten element określa warunki, które definiują dopuszczalnych potwierdzeń SAML.

```
<Conditions NotBefore="2013-03-18T07:38:15.128Z" NotOnOrAfter="2013-03-18T08:48:15.128Z">
      <AudienceRestriction>
        <Audience>https://www.contoso.com</Audience>
      </AudienceRestriction>
</Conditions>
```

`NotBefore` i `NotOnOrAfter` atrybuty Określ interwał, podczas którego potwierdzenia jest nieprawidłowy.

* Wartość `NotBefore` atrybutu jest równoważna lub nieco (niecałej sekundy) później niż wartość `IssueInstant` atrybutu `Assertion` elementu. Usługi Azure AD nie uwzględniać żadnych różnica czasu między sobą i usługi w chmurze (dostawca usług), a nie dodaje żadnych buforu do tego czasu.
* Wartość `NotOnOrAfter` atrybut jest nowszy niż wartość 70 minut `NotBefore` atrybutu.

#### <a name="audience"></a>Odbiorcy

Zawiera identyfikator URI, który identyfikuje określonej grupy odbiorców. Usługa Azure AD ustawia wartość tego elementu na wartość `Issuer` elementu `AuthnRequest` zainicjowane, logowania jednokrotnego. Aby ocenić `Audience` wartość, należy użyć wartości `App ID URI` podany podczas rejestracji aplikacji.

```
<AudienceRestriction>
        <Audience>https://www.contoso.com</Audience>
</AudienceRestriction>
```

Podobnie jak `Issuer` wartość `Audience` wartość musi dokładnie odpowiadać jeden z głównych nazw usług, które reprezentuje usługę w chmurze w usłudze Azure AD. Jednak jeśli wartość `Issuer` element nie jest wartością identyfikatora URI `Audience` wartość w odpowiedzi jest `Issuer` wartość prefiks `spn:`.

#### <a name="attributestatement"></a>AttributeStatement

Zawiera oświadczenia dotyczące podmiotu lub użytkownika. Poniższy fragment zawiera przykładowe `AttributeStatement` elementu. Wielokropek wskazuje, czy element może zawierać wiele atrybutów i wartości atrybutów.

```
<AttributeStatement>
      <Attribute Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name">
        <AttributeValue>testuser@contoso.com</AttributeValue>
      </Attribute>
      <Attribute Name="http://schemas.microsoft.com/identity/claims/objectidentifier">
        <AttributeValue>3F2504E0-4F89-11D3-9A0C-0305E82C3301</AttributeValue>
      </Attribute>
      ...
</AttributeStatement>
```        

* **Nadaj nazwę oświadczenia** — wartość `Name` atrybutu (`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`) jest główna nazwa użytkownika uwierzytelnionego użytkownika, takie jak `testuser@managedtenant.com`.
* **Oświadczenia w elemencie ObjectIdentifier** — wartość `ObjectIdentifier` atrybutu (`http://schemas.microsoft.com/identity/claims/objectidentifier`) jest `ObjectId` obiektu katalogu, który reprezentuje uwierzytelnionego użytkownika w usłudze Azure AD. `ObjectId` jest się niezmienne globalnie unikatowa i ponowne użycie bezpiecznego identyfikatora uwierzytelnionego użytkownika.

#### <a name="authnstatement"></a>AuthnStatement

Ten element potwierdza, że temat potwierdzeń został uwierzytelniony przez konkretnego oznacza, że w danym momencie.

* `AuthnInstant` Atrybut określa czas, w którym użytkownik jest uwierzytelniany przy użyciu usługi Azure AD.
* `AuthnContext` Element Określa kontekst uwierzytelniania używany do uwierzytelniania użytkownika.

```
<AuthnStatement AuthnInstant="2013-03-18T07:33:56.000Z" SessionIndex="_bf9c623d-cc20-407a-9a59-c2d0aee84d12">
      <AuthnContext>
        <AuthnContextClassRef> urn:oasis:names:tc:SAML:2.0:ac:classes:Password</AuthnContextClassRef>
      </AuthnContext>
</AuthnStatement>
```
