---
title: Azure funkcji logowania jednokrotnego w protokole SAML | Dokumentacja firmy Microsoft
description: W tym artykule opisano protokołu SAML na znak pojedynczego w usłudze Azure Active Directory
services: active-directory
documentationcenter: .net
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: ad8437f5-b887-41ff-bd77-779ddafc33fb
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
ms.openlocfilehash: 6f567edd64aef2f106e1077e4b0cf06ab625b234
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/22/2018
ms.locfileid: "36317623"
---
# <a name="single-sign-on-saml-protocol"></a>Jeden protokół logowania jednokrotnego SAML

W tym artykule omówiono SAML 2.0 uwierzytelniania żądań i odpowiedzi, które obsługuje usługi Azure Active Directory (Azure AD) dla logowania jednokrotnego.

Na poniższym diagramie protokołu zawiera opis jednego sekwencji logowania jednokrotnego. Usługi w chmurze (usługodawca) używa powiązanie HTTP, przekierowanie do przekazania `AuthnRequest` elementu (żądania uwierzytelniania) do usługi Azure AD (dostawcy tożsamości). Następnie usługi Azure AD używa HTTP post, wiązanie po `Response` element do usługi w chmurze.

![Rejestracja jednokrotna przepływu pracy](media/active-directory-single-sign-on-protocol-reference/active-directory-saml-single-sign-on-workflow.png)

## <a name="authnrequest"></a>AuthnRequest

Aby żądań uwierzytelniania użytkownika, chmura usług wysyłania `AuthnRequest` element do usługi Azure AD. Przykładowe SAML 2.0 `AuthnRequest` może wyglądać następująco:

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
| ID | Wymagane | Usługi Azure AD użyje tego atrybutu, aby wypełnić `InResponseTo` atrybutu zwrócona odpowiedź. Identyfikator nie musi rozpoczynać się od numeru, więc wspólnej strategii jest dołączenie wartości ciągu, takich jak "id" na końcu reprezentacji ciągu identyfikatora GUID. Na przykład `id6c1c178c166d486687be4aaf5e482730` jest poprawnym identyfikatorem. |
| Wersja | Wymagane | Ten parametr powinien mieć ustawioną **2.0**. |
| IssueInstant | Wymagane | Jest to ciąg daty/godziny z wartości UTC i [obustronne formatu ("o")](https://msdn.microsoft.com/library/az4se3k1.aspx). Usługi Azure AD oczekuje wartości typu DateTime tego typu, ale nie ocenę lub użyj wartości. |
| AssertionConsumerServiceUrl | Optional (Opcjonalność) | Jeśli zostanie podana, ten parametr musi być zgodna `RedirectUri` usługi w chmurze w usłudze Azure AD. |
| ForceAuthn | Optional (Opcjonalność) | Jest to wartość logiczna. Jeśli PRAWDA, oznacza to, że użytkownik zostanie wymuszone ponownego uwierzytelnienia, nawet jeśli mają prawidłowy sesji z usługą Azure AD. |
| IsPassive | Optional (Opcjonalność) | Jest to wartość logiczna określająca, czy usługi Azure AD powinien uwierzytelnić użytkownika w trybie dyskretnym, bez interakcji użytkownika, przy użyciu pliku cookie sesji, jeśli taka istnieje. Jeśli to PRAWDA, usługi Azure AD będzie podejmować próby uwierzytelnienia przy użyciu pliku cookie sesji użytkownika. |

Wszystkie inne `AuthnRequest` atrybutów, takich jak zgody, docelowy AssertionConsumerServiceIndex, AttributeConsumerServiceIndex i ProviderName są **ignorowane**.

Ignoruje także usługi Azure AD `Conditions` element `AuthnRequest`.

### <a name="issuer"></a>Wystawca

`Issuer` Element `AuthnRequest` musi dokładnie pasować **ServicePrincipalNames** w usłudze w chmurze w usłudze Azure AD. Zwykle ta jest równa **identyfikator URI aplikacji** określonym podczas rejestracji aplikacji.

Fragment SAML zawierający `Issuer` element wygląda w następującym przykładowym:

```
<Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://www.contoso.com</Issuer>
```

### <a name="nameidpolicy"></a>NameIDPolicy

Ten element żądania format Identyfikatora nazwy określonej w odpowiedzi i jest opcjonalna w `AuthnRequest` elementy wysyłane do usługi Azure AD.

A `NameIdPolicy` element wygląda w następującym przykładowym:

```
<NameIDPolicy Format="urn:oasis:names:tc:SAML:2.0:nameid-format:persistent"/>
```

Jeśli `NameIDPolicy` została podana, możesz podać jego opcjonalne `Format` atrybutu. `Format` Atrybut może mieć tylko jedną z następujących wartości; żadnych innych wartości powoduje błąd.

* `urn:oasis:names:tc:SAML:2.0:nameid-format:persistent`: Azure Active Directory powoduje wystawienie oświadczenia NameID identyfikatorowi parowania.
* `urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress`: Azure Active Directory powoduje wystawienie oświadczenia NameID format adresu e-mail.
* `urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified`: Ta wartość umożliwia usłudze Azure Active Directory, aby wybrać format oświadczeń. Usługa Azure Active Directory wystawia NameID identyfikatorowi parowania.
* `urn:oasis:names:tc:SAML:2.0:nameid-format:transient`: Azure Active Directory powoduje wystawienie oświadczenia NameID jako losowo generowany wartość, która jest unikatowa dla bieżącej operacji logowania jednokrotnego. Oznacza to, że wartość jest tymczasowe i nie może służyć do identyfikowania użytkownika.

Ignoruje usługi Azure AD `AllowCreate` atrybutu.

### <a name="requestauthncontext"></a>RequestAuthnContext
`RequestedAuthnContext` Element określa metody uwierzytelniania żądany. Jest to pozycja opcjonalna w `AuthnRequest` elementy wysyłane do usługi Azure AD. Usługi Azure AD obsługuje tylko jeden `AuthnContextClassRef` wartość: `urn:oasis:names:tc:SAML:2.0:ac:classes:Password`.

### <a name="scoping"></a>Określanie zakresu
`Scoping` Element, który zawiera listę dostawców tożsamości, jest opcjonalna w `AuthnRequest` elementy wysyłane do usługi Azure AD.

Jeśli zostanie podana, nie dołączaj `ProxyCount` atrybutu `IDPListOption` lub `RequesterID` elementu, ponieważ nie są obsługiwane.

### <a name="signature"></a>Podpis
Nie dołączaj `Signature` element `AuthnRequest` elementów, nie obsługuje usługi Azure AD podpisanego żądania uwierzytelnienia.

### <a name="subject"></a>Temat
Ignoruje usługi Azure AD `Subject` elementu `AuthnRequest` elementów.

## <a name="response"></a>Odpowiedź
Gdy żądane jednokrotnego zakończy się pomyślnie, usługi Azure AD zapisuje odpowiedź do usługi w chmurze. Odpowiedź do pomyślnej próby logowania jednokrotnego wygląda następującym przykładowym:

```
<samlp:Response ID="_a4958bfd-e107-4e67-b06d-0d85ade2e76a" Version="2.0" IssueInstant="2013-03-18T07:38:15.144Z" Destination="https://contoso.com/identity/inboundsso.aspx" InResponseTo="id758d0ef385634593a77bdf7e632984b6" xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
  <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion"> https://login.microsoftonline.com/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
  <ds:Signature xmlns:ds="http://www.w3.org/2000/09/xmldsig#">
    ...
  </ds:Signature>
  <samlp:Status>
    <samlp:StatusCode Value="urn:oasis:names:tc:SAML:2.0:status:Success" />
  </samlp:Status>
  <Assertion ID="_bf9c623d-cc20-407a-9a59-c2d0aee84d12" IssueInstant="2013-03-18T07:38:15.144Z" Version="2.0" xmlns="urn:oasis:names:tc:SAML:2.0:assertion">
    <Issuer>https://login.microsoftonline.com/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
    <ds:Signature xmlns:ds="http://www.w3.org/2000/09/xmldsig#">
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

`Response` Element zawiera wynik żądania autoryzacji. Azure AD zestawy `ID`, `Version` i `IssueInstant` wartości w `Response` elementu. Ustawia również następujące atrybuty:

* `Destination`: Podczas logowania jednokrotnego zakończy się pomyślnie, to ma ustawioną wartość `RedirectUri` dostawcy usług (usługi w chmurze).
* `InResponseTo`: Ta wartość jest równa `ID` atrybutu `AuthnRequest` element, który zainicjował odpowiedzi.

### <a name="issuer"></a>Wystawca

Azure AD zestawy `Issuer` elementu `https://login.microsoftonline.com/<TenantIDGUID>/` gdzie <TenantIDGUID> jest identyfikator dzierżawcy dzierżawy usługi Azure AD.

Na przykład odpowiedzi z elementem wystawcy może wyglądać następującym przykładowym:

```
<Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion"> https://login.microsoftonline.com/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
```

### <a name="status"></a>Stan

`Status` Element umożliwia przekazywanie powodzenie lub Niepowodzenie logowania jednokrotnego. Obejmuje on `StatusCode` element, który zawiera kod lub zestaw kodów zagnieżdżonych, który reprezentuje stan żądania. Zawiera także `StatusMessage` element, który zawiera niestandardowe komunikaty o błędach wygenerowanych podczas procesu rejestracji.

<!-- TODO: Add a authentication protocol error reference -->

Poniższy przykład jest odpowiedzi SAML nieudanej próbie logowania.

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

### <a name="assertion"></a>potwierdzenia

Oprócz `ID`, `IssueInstant` i `Version`, usługi Azure AD ustawia następujące elementy w `Assertion` element odpowiedzi.

#### <a name="issuer"></a>Wystawca

Ta wartość jest równa `https://sts.windows.net/<TenantIDGUID>/`gdzie <TenantIDGUID> jest identyfikator dzierżawcy dzierżawy usługi Azure AD.

```
<Issuer>https://login.microsoftonline.com/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
```

#### <a name="signature"></a>Podpis

Usługi Azure AD podpisuje potwierdzenia w odpowiedzi na pomyślne logowania jednokrotnego. `Signature` Element zawiera podpis cyfrowy, który usługa w chmurze można użyć do uwierzytelniania źródła, aby sprawdzić integralność potwierdzenia.

Do generowania podpisu cyfrowego, korzysta z klucza podpisywania w usłudze Azure AD `IDPSSODescriptor` elementu jego dokumentu metadanych.

```
<ds:Signature xmlns:ds="http://www.w3.org/2000/09/xmldsig#">
      digital_signature_here
    </ds:Signature>
```

#### <a name="subject"></a>Temat

To ustawienie określa podmiot zabezpieczeń, który jest przedmiotem oświadczeń do potwierdzenia. Zawiera on `NameID` element, który reprezentuje uwierzytelnionego użytkownika. `NameID` Wartość jest identyfikatorem docelowego, który jest przekierowywany tylko do dostawcy usług, który jest odbiorców tokenu. Jest trwały — mogą być odwoływane, ale nigdy nie jest ponownie przypisywane. Możliwe jest również nieprzezroczyste, ponieważ nie ujawnia niczego o użytkowniku i nie można użyć jako identyfikatora atrybutu zapytań.

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

`NotBefore` i `NotOnOrAfter` atrybuty określa interwał, w którym to potwierdzenie jest prawidłowy.

* Wartość `NotBefore` atrybutu jest równoważna lub nieco (mniej niż sekundę) później niż wartość `IssueInstant` atrybutu `Assertion` elementu. Usługi Azure AD nie bierze pod uwagę różnicę czasu między sobą i usługi w chmurze (dostawca usług), a nie dodaje żadnych buforu do tego czasu.
* Wartość `NotOnOrAfter` atrybut jest nowszy niż wartość 70 minut `NotBefore` atrybutu.

#### <a name="audience"></a>Grupy odbiorców

Zawiera identyfikator URI, który identyfikuje określonej grupy odbiorców. Usługi Azure AD ustawia wartość tego elementu do wartości `Issuer` elementu `AuthnRequest` który zainicjował logowania jednokrotnego. Aby ocenić `Audience` wartość, należy użyć wartości `App ID URI` , która została określona podczas rejestracji aplikacji.

```
<AudienceRestriction>
        <Audience>https://www.contoso.com</Audience>
</AudienceRestriction>
```

Podobnie jak `Issuer` wartość `Audience` wartość musi dokładnie odpowiadać jedną z głównych nazw usługi, które reprezentuje usługę w chmurze w usłudze Azure AD. Jednak jeśli wartość `Issuer` element nie jest wartością identyfikatora URI `Audience` wartość w odpowiedzi jest `Issuer` wartość prefiks `spn:`.

#### <a name="attributestatement"></a>AttributeStatement

Zawiera oświadczenia dotyczące podmiotu lub użytkownika. Poniższy fragment podano przykładowe `AttributeStatement` elementu. Wielokropek wskazuje, czy element może zawierać wiele atrybutów i wartości atrybutów.

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

* **Nazwa oświadczenia** — wartość `Name` atrybutu (`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`) jest główna nazwa użytkownika uwierzytelnionego użytkownika, takich jak `testuser@managedtenant.com`.
* **Oświadczenia w elemencie ObjectIdentifier** — wartość `ObjectIdentifier` atrybutu (`http://schemas.microsoft.com/identity/claims/objectidentifier`) jest `ObjectId` obiektu katalogu, który reprezentuje uwierzytelnionego użytkownika w usłudze Azure AD. `ObjectId` Nie można modyfikować, globalnie unikatowa i ponowne użycie bezpiecznego identyfikatora uwierzytelnionego użytkownika.

#### <a name="authnstatement"></a>AuthnStatement

Ten element potwierdza, że podmiotu potwierdzenia został uwierzytelniony w szczególności sposób w określonym czasie.

* `AuthnInstant` Atrybut określa czas, w którym użytkownik jest uwierzytelniony z usługą Azure AD.
* `AuthnContext` Element Określa kontekst uwierzytelniania używany do uwierzytelniania użytkownika.

```
<AuthnStatement AuthnInstant="2013-03-18T07:33:56.000Z" SessionIndex="_bf9c623d-cc20-407a-9a59-c2d0aee84d12">
      <AuthnContext>
        <AuthnContextClassRef> urn:oasis:names:tc:SAML:2.0:ac:classes:Password</AuthnContextClassRef>
      </AuthnContext>
</AuthnStatement>
```
