---
title: Protokół SAML logowania jednokrotnego na platformie Azure | Microsoft Docs
description: W tym artykule opisano Logowanie jednokrotne protokołu SAML w Azure Active Directory
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
ms.topic: conceptual
ms.date: 07/19/2017
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: hirsin
ms.openlocfilehash: cecb78a82eb2925813bdc7f6df2503fae94b6437
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79262427"
---
# <a name="single-sign-on-saml-protocol"></a>Protokół SAML logowania jednokrotnego

W tym artykule omówiono żądania uwierzytelniania SAML 2,0 i odpowiedzi, które Azure Active Directory (Azure AD) obsługują Logowanie jednokrotne.

Poniższy diagram protokołu opisuje sekwencję logowania jednokrotnego. Usługa w chmurze (dostawca usług) używa powiązania przekierowywania HTTP do przekazywania elementu `AuthnRequest` (żądanie uwierzytelnienia) do usługi Azure AD (dostawca tożsamości). Usługa Azure AD następnie używa powiązania post protokołu HTTP w celu opublikowania elementu `Response` w usłudze w chmurze.

![Przepływ pracy logowania jednokrotnego](./media/single-sign-on-saml-protocol/active-directory-saml-single-sign-on-workflow.png)

## <a name="authnrequest"></a>AuthnRequest

Aby zażądać uwierzytelnienia użytkownika, usługi w chmurze wysyłają `AuthnRequest` elementu do usługi Azure AD. Przykładowy `AuthnRequest` SAML 2,0 może wyglądać następująco:

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
| ID | Wymagany | Usługa Azure AD używa tego atrybutu do wypełniania atrybutu `InResponseTo` zwróconej odpowiedzi. Identyfikator nie może zaczynać się od liczby, więc typową strategią jest dołączenie ciągu takiego jak "ID" do ciągu reprezentującego identyfikator GUID. Na przykład `id6c1c178c166d486687be4aaf5e482730` jest prawidłowym IDENTYFIKATORem. |
| Wersja | Wymagany | Dla tego parametru należy ustawić wartość **2,0**. |
| IssueInstant | Wymagany | Jest to ciąg daty i godziny z wartością czasu UTC i [formatem rundy ("o")](https://msdn.microsoft.com/library/az4se3k1.aspx). Usługa Azure AD oczekuje wartości daty i godziny tego typu, ale nie oblicza ani nie używa wartości. |
| AssertionConsumerServiceUrl | Optional (Opcjonalność) | Jeśli ta wartość jest określona, ten parametr musi być zgodny z `RedirectUri` usługi w chmurze w usłudze Azure AD. |
| ForceAuthn | Optional (Opcjonalność) | Jest to wartość logiczna. W przypadku wartości true oznacza to, że użytkownik zostanie zmuszony do ponownego uwierzytelnienia, nawet jeśli ma prawidłową sesję z usługą Azure AD. |
| Ispassy | Optional (Opcjonalność) | Jest to wartość logiczna określająca, czy usługa Azure AD powinna uwierzytelniać użytkownika w trybie dyskretnym, bez interakcji z użytkownikiem, jeśli taki istnieje. Jeśli ta wartość jest równa true, usługa Azure AD podejmie próbę uwierzytelnienia użytkownika przy użyciu pliku cookie sesji. |

Wszystkie inne atrybuty `AuthnRequest`, takie jak zgody, Destination, AssertionConsumerServiceIndex, AttributeConsumerServiceIndex i ProviderName, są **ignorowane**.

Usługa Azure AD ignoruje także element `Conditions` w `AuthnRequest`.

### <a name="issuer"></a>Wystawca

Element `Issuer` w `AuthnRequest` musi dokładnie pasować do jednego z **ServicePrincipalNames** w usłudze w chmurze w usłudze Azure AD. Zwykle jest to **Identyfikator URI aplikacji** określony podczas rejestracji aplikacji.

Fragment SAML zawierający `Issuer` element wygląda podobnie do poniższego przykładu:

```
<Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://www.contoso.com</Issuer>
```

### <a name="nameidpolicy"></a>NameIDPolicy

Ten element żąda określonego formatu identyfikatora nazwy w odpowiedzi i jest opcjonalny w `AuthnRequest` elementów wysyłanych do usługi Azure AD.

Element `NameIdPolicy` wygląda podobnie do poniższego przykładu:

```
<NameIDPolicy Format="urn:oasis:names:tc:SAML:2.0:nameid-format:persistent"/>
```

W przypadku podanej `NameIDPolicy` można uwzględnić jej opcjonalny atrybut `Format`. Atrybut `Format` może mieć tylko jedną z następujących wartości: Każda inna wartość spowoduje wystąpienie błędu.

* `urn:oasis:names:tc:SAML:2.0:nameid-format:persistent`: Azure Active Directory wystawia żądania NameID jako identyfikator parowania.
* `urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress`: Azure Active Directory wystawia w formacie adresu e-mail NameID.
* `urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified`: Ta wartość umożliwia Azure Active Directory wybór formatu żądania. Azure Active Directory wystawia NameID jako identyfikator parowania.
* `urn:oasis:names:tc:SAML:2.0:nameid-format:transient`: Azure Active Directory wystawia rolę NameID jako losowo wygenerowaną wartość, która jest unikatowa dla bieżącej operacji logowania jednokrotnego. Oznacza to, że wartość jest tymczasowa i nie można jej użyć do zidentyfikowania użytkownika uwierzytelniającego.

Usługa Azure AD ignoruje atrybut `AllowCreate`.

### <a name="requestauthncontext"></a>RequestAuthnContext
Element `RequestedAuthnContext` określa odpowiednie metody uwierzytelniania. Jest ona opcjonalna w `AuthnRequest` elementów wysyłanych do usługi Azure AD. Usługa Azure AD obsługuje wartości `AuthnContextClassRef`, takich jak `urn:oasis:names:tc:SAML:2.0:ac:classes:Password`.

### <a name="scoping"></a>Zakresów
Element `Scoping`, który zawiera listę dostawców tożsamości, jest opcjonalny w `AuthnRequest` elementów wysyłanych do usługi Azure AD.

Jeśli ta wartość jest określona, nie należy uwzględniać `ProxyCount` atrybutu `IDPListOption` lub `RequesterID` elementu, ponieważ nie są one obsługiwane.

### <a name="signature"></a>Sygnatura
Nie dołączaj `Signature` elementu do elementów `AuthnRequest`, ponieważ usługa Azure AD nie obsługuje podpisanych żądań uwierzytelniania.

### <a name="subject"></a>Podmiot
Usługa Azure AD ignoruje `Subject` elementu `AuthnRequest` elementów.

## <a name="response"></a>Odpowiedź
Po pomyślnym zakończeniu logowania usługa Azure AD ogłasza odpowiedź do usługi w chmurze. Odpowiedź na pomyślną próbę zalogowania wygląda jak w poniższym przykładzie:

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

Element `Response` obejmuje wynik żądania autoryzacji. Usługa Azure AD ustawia `ID`, `Version` i `IssueInstant` wartości w elemencie `Response`. Ustawia również następujące atrybuty:

* `Destination`: po pomyślnym zalogowaniu zostanie ustawiony `RedirectUri` dostawcy usług (usługa w chmurze).
* `InResponseTo`: jest to atrybut `ID` elementu `AuthnRequest`, który zainicjował odpowiedź.

### <a name="issuer"></a>Wystawca

Usługa Azure AD ustawia `Issuer` element `https://login.microsoftonline.com/<TenantIDGUID>/`, gdzie \<TenantIDGUID > jest IDENTYFIKATORem dzierżawy dzierżawy usługi Azure AD.

Na przykład odpowiedź z elementem Issuer może wyglądać jak w następującym przykładzie:

```
<Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion"> https://login.microsoftonline.com/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
```

### <a name="status"></a>Stan

Element `Status` przekazuje sukces lub niepowodzenie logowania. Zawiera element `StatusCode`, który zawiera kod lub zestaw zagnieżdżonych kodów reprezentujących stan żądania. Zawiera również element `StatusMessage`, który zawiera niestandardowe komunikaty o błędach, które są generowane podczas procesu logowania.

<!-- TODO: Add an authentication protocol error reference -->

Poniższy przykład jest odpowiedzią SAML na nieudaną próbę logowania.

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

### <a name="assertion"></a>Twierdz

Oprócz `ID`, `IssueInstant` i `Version`usługa Azure AD ustawia następujące elementy w elemencie `Assertion` odpowiedzi.

#### <a name="issuer"></a>Wystawca

Jest to `https://sts.windows.net/<TenantIDGUID>/`, gdzie \<TenantIDGUID > jest IDENTYFIKATORem dzierżawy dzierżawy usługi Azure AD.

```
<Issuer>https://login.microsoftonline.com/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
```

#### <a name="signature"></a>Sygnatura

Usługa Azure AD podpisuje potwierdzenie w odpowiedzi na pomyślne logowanie. Element `Signature` zawiera podpis cyfrowy, którego usługa w chmurze może użyć do uwierzytelnienia źródła w celu zweryfikowania integralności potwierdzenia.

Aby wygenerować ten podpis cyfrowy, usługa Azure AD używa klucza podpisywania w `IDPSSODescriptor` elemencie dokumentu metadanych.

```
<ds:Signature xmlns:ds="https://www.w3.org/2000/09/xmldsig#">
      digital_signature_here
    </ds:Signature>
```

#### <a name="subject"></a>Podmiot

Określa podmiot zabezpieczeń, który jest podmiotem instrukcji w potwierdzeniu. Zawiera element `NameID`, który reprezentuje uwierzytelnionego użytkownika. Wartość `NameID` jest identyfikatorem ukierunkowanym skierowanym tylko do dostawcy usług, który jest odbiorcą dla tokenu. Jest on trwały — można go odwołać, ale nigdy nie jest ponownie przypisywany. Jest on również nieprzezroczysty, w tym, że nie ujawnia żadnych informacji o użytkowniku i nie może być używany jako identyfikator zapytań dotyczących atrybutów.

Atrybut `Method` elementu `SubjectConfirmation` jest zawsze ustawiany jako `urn:oasis:names:tc:SAML:2.0:cm:bearer`.

```
<Subject>
      <NameID>Uz2Pqz1X7pxe4XLWxV9KJQ+n59d573SepSAkuYKSde8=</NameID>
      <SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer">
        <SubjectConfirmationData InResponseTo="id758d0ef385634593a77bdf7e632984b6" NotOnOrAfter="2013-03-18T07:43:15.144Z" Recipient="https://contoso.com/identity/inboundsso.aspx" />
      </SubjectConfirmation>
</Subject>
```

#### <a name="conditions"></a>Warunki

Ten element określa warunki, które definiują akceptowalne użycie potwierdzeń SAML.

```
<Conditions NotBefore="2013-03-18T07:38:15.128Z" NotOnOrAfter="2013-03-18T08:48:15.128Z">
      <AudienceRestriction>
        <Audience>https://www.contoso.com</Audience>
      </AudienceRestriction>
</Conditions>
```

Atrybuty `NotBefore` i `NotOnOrAfter` określają interwał, w którym potwierdzenie jest prawidłowe.

* Wartość atrybutu `NotBefore` jest równa lub nienieco (mniejsza niż sekunda) późniejsza niż wartość atrybutu `IssueInstant` elementu `Assertion`. Usługa Azure AD nie uwzględnia żadnej różnicy czasu między nią a usługą w chmurze (dostawcy usługi) i nie dodaje żadnych buforów do tej godziny.
* Wartość atrybutu `NotOnOrAfter` wynosi 70 minut później niż wartość atrybutu `NotBefore`.

#### <a name="audience"></a>Grupy odbiorców

Zawiera identyfikator URI, który identyfikuje zamierzone odbiorcy. Usługa Azure AD ustawia wartość tego elementu na wartość `Issuer` elementu `AuthnRequest`, który zainicjował logowanie. Aby oszacować wartość `Audience`, użyj wartości `App ID URI` określonej podczas rejestracji aplikacji.

```
<AudienceRestriction>
        <Audience>https://www.contoso.com</Audience>
</AudienceRestriction>
```

Podobnie jak `Issuer` wartość, wartość `Audience` musi dokładnie odpowiadać jednej z głównych nazw usług, która reprezentuje usługę w chmurze w usłudze Azure AD. Jeśli jednak wartość `Issuer` elementu nie jest wartością identyfikatora URI, `Audience` wartość w odpowiedzi jest wartością `Issuer` poprzedzoną `spn:`.

#### <a name="attributestatement"></a>AttributeStatement

Zawiera ona oświadczenia dotyczące tematu lub użytkownika. Poniższy fragment zawiera przykładowy element `AttributeStatement`. Wielokropek wskazuje, że element może zawierać wiele atrybutów i wartości atrybutów.

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

* **Nazwa** — wartość atrybutu `Name` (`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`) to główna nazwa użytkownika uwierzytelnionego użytkownika, taka jak `testuser@managedtenant.com`.
* **Elemencie objectidentifier** — wartość atrybutu `ObjectIdentifier` (`http://schemas.microsoft.com/identity/claims/objectidentifier`) to `ObjectId` obiektu katalogu, który reprezentuje uwierzytelnionego użytkownika w usłudze Azure AD. `ObjectId` jest niezmiennym, globalnie unikatowym i ponownie używanym identyfikatorem uwierzytelnionego użytkownika.

#### <a name="authnstatement"></a>AuthnStatement

Ten element potwierdza, że podmiot potwierdzający został uwierzytelniony przez określone środki w określonym czasie.

* Atrybut `AuthnInstant` określa czas, w którym użytkownik uwierzytelnił się z usługą Azure AD.
* Element `AuthnContext` określa kontekst uwierzytelniania używany do uwierzytelniania użytkownika.

```
<AuthnStatement AuthnInstant="2013-03-18T07:33:56.000Z" SessionIndex="_bf9c623d-cc20-407a-9a59-c2d0aee84d12">
      <AuthnContext>
        <AuthnContextClassRef> urn:oasis:names:tc:SAML:2.0:ac:classes:Password</AuthnContextClassRef>
      </AuthnContext>
</AuthnStatement>
```
