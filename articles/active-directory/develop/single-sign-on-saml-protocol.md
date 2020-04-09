---
title: Protokół azure single sign na saml
description: W tym artykule opisano protokół jednokrotnego logowania na SAML w usłudze Azure Active Directory
services: active-directory
documentationcenter: .net
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 07/19/2017
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: hirsin
ms.openlocfilehash: f1437ec5d9c3fd0ff69be0c884c340cb857ee181
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80881286"
---
# <a name="single-sign-on-saml-protocol"></a>Protokół SAML logowania jednokrotnego

W tym artykule omówiono żądania i odpowiedzi uwierzytelniania SAML 2.0, które obsługuje usługa Azure Active Directory (Azure AD) dla logowania jednokrotnego.

Diagram protokołu poniżej opisuje sekwencję logowania jednokrotnego. Usługa w chmurze (dostawca usług) używa powiązania `AuthnRequest` przekierowania HTTP do przekazywania elementu (żądanie uwierzytelniania) do usługi Azure AD (dostawcy tożsamości). Usługa Azure AD następnie używa powiązania `Response` wpisu HTTP, aby opublikować element w usłudze w chmurze.

![Przepływ pracy logowania jednokrotnego](./media/single-sign-on-saml-protocol/active-directory-saml-single-sign-on-workflow.png)

## <a name="authnrequest"></a>AuthnRequest (Prośba o authn)

Aby zażądać uwierzytelnienia użytkowników, `AuthnRequest` usługi w chmurze wysyłają element do usługi Azure AD. Przykładowy SAML 2.0 `AuthnRequest` może wyglądać następująco:

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
| ID | Wymagany | Usługa Azure AD używa tego atrybutu `InResponseTo` do wypełniania atrybutu zwróconej odpowiedzi. Identyfikator nie może zaczynać się od liczby, więc wspólną strategią jest poprzedzanie ciągu, takiego jak "id" do reprezentacji ciągu identyfikatora GUID. Na przykład `id6c1c178c166d486687be4aaf5e482730` jest prawidłowym identyfikatorem. |
| Wersja | Wymagany | Ten parametr należy ustawić na **2.0**. |
| ProblemInstant | Wymagany | Jest to ciąg DateTime o wartości UTC i [formacie w obie strony ("o").](https://msdn.microsoft.com/library/az4se3k1.aspx) Usługa Azure AD oczekuje datetime wartość tego typu, ale nie ocenia ani nie używa wartości. |
| TwierdzenieConsumerServiceUrl | Optional (Opcjonalność) | Jeśli zostanie podany, ten `RedirectUri` parametr musi być zgodny z usługą w chmurze w usłudze Azure AD. |
| ForceAuthn ( ForceAuthn ) | Optional (Opcjonalność) | Jest to wartość logiczna. Jeśli true, oznacza to, że użytkownik będzie zmuszony do ponownego uwierzytelnienia, nawet jeśli mają prawidłową sesję z usługą Azure AD. |
| IsPasyt | Optional (Opcjonalność) | Jest to wartość logiczna, która określa, czy usługa Azure AD powinna uwierzytelniać użytkownika w trybie dyskretnym, bez interakcji z użytkownikiem, przy użyciu pliku cookie sesji, jeśli taki istnieje. Jeśli to prawda, usługa Azure AD spróbuje uwierzytelnić użytkownika przy użyciu pliku cookie sesji. |

Wszystkie `AuthnRequest` inne atrybuty, takie jak Zgoda, Miejsce docelowe, TwierdzenieConsumerServiceIndex, AttributeConsumerServiceIndex i ProviderName są **ignorowane**.

Usługa Azure AD `Conditions` ignoruje `AuthnRequest`również element w pliku .

### <a name="issuer"></a>Wystawca

Element `Issuer` w `AuthnRequest` musi dokładnie odpowiadać jednej z **ServicePrincipalNames** w usłudze w chmurze w usłudze Azure AD. Zazwyczaj jest to ustawione na **identyfikator URI identyfikatora aplikacji,** który jest określony podczas rejestracji aplikacji.

Fragment SAML zawierający `Issuer` element wygląda następująco:

```
<Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://www.contoso.com</Issuer>
```

### <a name="nameidpolicy"></a>NazwaIDPolicy

Ten element żąda określonego formatu identyfikatora nazwy `AuthnRequest` w odpowiedzi i jest opcjonalny w elementach wysyłanych do usługi Azure AD.

Element `NameIdPolicy` wygląda następująco:

```
<NameIDPolicy Format="urn:oasis:names:tc:SAML:2.0:nameid-format:persistent"/>
```

Jeśli `NameIDPolicy` jest podana, można `Format` dołączyć jego atrybut opcjonalny. Atrybut `Format` może mieć tylko jedną z następujących wartości; każda inna wartość powoduje błąd.

* `urn:oasis:names:tc:SAML:2.0:nameid-format:persistent`: Usługa Azure Active Directory wystawia oświadczenie NameID jako identyfikator parowania.
* `urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress`: Usługa Azure Active Directory wystawia oświadczenie NameID w formacie adresu e-mail.
* `urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified`: Ta wartość umożliwia usłudze Azure Active Directory wybranie formatu oświadczeń. Usługa Azure Active Directory wystawia identyfikator nameid jako identyfikator parowania.
* `urn:oasis:names:tc:SAML:2.0:nameid-format:transient`: Usługa Azure Active Directory wystawia oświadczenie NameID jako losowo wygenerowaną wartość, która jest unikatowa dla bieżącej operacji samoużytku. Oznacza to, że wartość jest tymczasowa i nie może służyć do identyfikowania użytkownika uwierzytelniającego.

Usługa Azure AD `AllowCreate` ignoruje atrybut.

### <a name="requestauthncontext"></a>RequestAuthnContext (Tekst na żądanie)
Element `RequestedAuthnContext` określa żądane metody uwierzytelniania. Jest opcjonalny `AuthnRequest` w elementach wysyłanych do usługi Azure AD. Usługa Azure `AuthnContextClassRef` AD `urn:oasis:names:tc:SAML:2.0:ac:classes:Password`obsługuje wartości, takie jak .

### <a name="scoping"></a>Zakresu
Element, `Scoping` który zawiera listę dostawców tożsamości, `AuthnRequest` jest opcjonalny w elementach wysyłanych do usługi Azure AD.

Jeśli podana, nie `ProxyCount` należy uwzględniać atrybutu `IDPListOption` lub `RequesterID` elementu, ponieważ nie są one obsługiwane.

### <a name="signature"></a>Sygnatura
Nie dołączaj `Signature` elementu `AuthnRequest` do elementów, ponieważ usługa Azure AD nie obsługuje podpisanych żądań uwierzytelniania.

### <a name="subject"></a>Podmiot
Usługa Azure AD `Subject` ignoruje element elementów. `AuthnRequest`

## <a name="response"></a>Odpowiedź
Po pomyślnym zakończeniu żądanego logowania usługa Azure AD publikuje odpowiedź na usługę w chmurze. Odpowiedź na pomyślną próbę logowania wygląda następująco:

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

Element `Response` zawiera wynik żądania autoryzacji. Usługa Azure `ID`AD `Version` `IssueInstant` ustawia , `Response` i wartości w elemencie. Ustawia również następujące atrybuty:

* `Destination`: Po pomyślnym zakończeniu logowania jest to `RedirectUri` ustawione na dostawcę usług (usługa w chmurze).
* `InResponseTo`: Jest to `ID` ustawiona na `AuthnRequest` atrybut elementu, który zainicjował odpowiedź.

### <a name="issuer"></a>Wystawca

Usługa Azure `Issuer` AD `https://login.microsoftonline.com/<TenantIDGUID>/` ustawia \<element, w którym> TenantIDGUID jest identyfikatorem dzierżawy dzierżawy usługi Azure AD.

Na przykład odpowiedź z elementem wystawcy może wyglądać następująco:

```
<Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion"> https://login.microsoftonline.com/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
```

### <a name="status"></a>Stan

Element `Status` przekazuje sukces lub niepowodzenie logowania. Zawiera `StatusCode` element, który zawiera kod lub zestaw kodów zagnieżdżonych, który reprezentuje stan żądania. Zawiera również `StatusMessage` element, który zawiera niestandardowe komunikaty o błędach, które są generowane podczas procesu logowania.

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

### <a name="assertion"></a>Asercja

Oprócz `ID`, `IssueInstant` i `Version`, Usługa Azure AD ustawia `Assertion` następujące elementy w elemencie odpowiedzi.

#### <a name="issuer"></a>Wystawca

Jest to `https://sts.windows.net/<TenantIDGUID>/`ustawione \<na miejsce, w którym> TenantIDGUID jest identyfikatorem dzierżawy dzierżawy usługi Azure AD.

```
<Issuer>https://login.microsoftonline.com/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
```

#### <a name="signature"></a>Sygnatura

Usługa Azure AD podpisuje asercja w odpowiedzi na pomyślne logowanie. Element `Signature` zawiera podpis cyfrowy, który usługa w chmurze może służyć do uwierzytelniania źródła w celu zweryfikowania integralności potwierdzenia.

Aby wygenerować ten podpis cyfrowy, usługa `IDPSSODescriptor` Azure AD używa klucza podpisywania w elemencie jego dokumentu metadanych.

```
<ds:Signature xmlns:ds="https://www.w3.org/2000/09/xmldsig#">
      digital_signature_here
    </ds:Signature>
```

#### <a name="subject"></a>Podmiot

Określa podmiot, który jest przedmiotem instrukcji w potwierdzeniu. Zawiera `NameID` element, który reprezentuje uwierzytelnionego użytkownika. Wartość `NameID` jest identyfikatorem docelowym, który jest kierowany tylko do dostawcy usług, który jest odbiorcą tokenu. Jest trwały — można go odwołać, ale nigdy nie jest ponownie przypisany. Jest również nieprzezroczysty, ponieważ nie ujawnia niczego o użytkowniku i nie może być używany jako identyfikator dla zapytań atrybutów.

Atrybut `Method` `SubjectConfirmation` elementu jest zawsze ustawiony `urn:oasis:names:tc:SAML:2.0:cm:bearer`na .

```
<Subject>
      <NameID>Uz2Pqz1X7pxe4XLWxV9KJQ+n59d573SepSAkuYKSde8=</NameID>
      <SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer">
        <SubjectConfirmationData InResponseTo="id758d0ef385634593a77bdf7e632984b6" NotOnOrAfter="2013-03-18T07:43:15.144Z" Recipient="https://contoso.com/identity/inboundsso.aspx" />
      </SubjectConfirmation>
</Subject>
```

#### <a name="conditions"></a>Warunki

Ten element określa warunki, które definiują dopuszczalne użycie potwierdzeń SAML.

```
<Conditions NotBefore="2013-03-18T07:38:15.128Z" NotOnOrAfter="2013-03-18T08:48:15.128Z">
      <AudienceRestriction>
        <Audience>https://www.contoso.com</Audience>
      </AudienceRestriction>
</Conditions>
```

`NotBefore` Atrybuty `NotOnOrAfter` i określić interwał, w którym twierdzenie jest prawidłowe.

* Wartość atrybutu `NotBefore` jest równa lub nieznacznie (mniej niż sekundę) `IssueInstant` później niż `Assertion` wartość atrybutu elementu. Usługa Azure AD nie uwzględnia żadnej różnicy czasu między sobą a usługą w chmurze (dostawcą usług) i nie dodaje żadnego buforu do tego czasu.
* Wartość atrybutu `NotOnOrAfter` jest 70 minut później niż `NotBefore` wartość atrybutu.

#### <a name="audience"></a>Grupy odbiorców

Zawiera identyfikator URI identyfikujący zamierzoną grupę odbiorców. Usługa Azure AD ustawia wartość tego `Issuer` elementu na `AuthnRequest` wartość elementu, który zainicjował logowanie. Aby ocenić `Audience` wartość, należy `App ID URI` użyć wartości, która została określona podczas rejestracji aplikacji.

```
<AudienceRestriction>
        <Audience>https://www.contoso.com</Audience>
</AudienceRestriction>
```

Podobnie `Issuer` jak wartość, `Audience` wartość musi dokładnie odpowiadać jednej z nazw głównej usługi, która reprezentuje usługę w chmurze w usłudze Azure AD. Jeśli jednak wartość `Issuer` elementu nie jest wartością URI, `Audience` wartością `Issuer` w odpowiedzi jest `spn:`wartość poprzedzone .

#### <a name="attributestatement"></a>Opis atrybutu

Zawiera oświadczenia dotyczące tematu lub użytkownika. Poniższy fragment zawiera `AttributeStatement` przykładowy element. Wielokropek wskazuje, że element może zawierać wiele atrybutów i wartości atrybutów.

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

* **Oświadczenie nazwy** — wartość `Name` atrybutu`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`( ) jest główną nazwą użytkownika `testuser@managedtenant.com`uwierzytelnionego użytkownika, taką jak .
* **Oświadczenie ObjectIdentifier** — wartość `ObjectIdentifier` atrybutu`http://schemas.microsoft.com/identity/claims/objectidentifier`( `ObjectId` ) jest obiektem katalogu, który reprezentuje uwierzytelnionego użytkownika w usłudze Azure AD. `ObjectId`jest niezmiennym, globalnie unikatowym i ponownie użyj bezpiecznego identyfikatora uwierzytelnionego użytkownika.

#### <a name="authnstatement"></a>AuthnPaństwo

Ten element twierdzi, że temat twierdzenia został uwierzytelniony za pomocą określonych środków w określonym czasie.

* Atrybut `AuthnInstant` określa czas, w którym użytkownik uwierzytelnił się za pomocą usługi Azure AD.
* Element `AuthnContext` określa kontekst uwierzytelniania używany do uwierzytelniania użytkownika.

```
<AuthnStatement AuthnInstant="2013-03-18T07:33:56.000Z" SessionIndex="_bf9c623d-cc20-407a-9a59-c2d0aee84d12">
      <AuthnContext>
        <AuthnContextClassRef> urn:oasis:names:tc:SAML:2.0:ac:classes:Password</AuthnContextClassRef>
      </AuthnContext>
</AuthnStatement>
```
