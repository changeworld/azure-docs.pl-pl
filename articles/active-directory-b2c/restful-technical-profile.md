---
title: Definiowanie profilu technicznego RESTful w zasadach niestandardowych
titleSuffix: Azure AD B2C
description: Zdefiniuj profil techniczny RESTful w zasadach niestandardowych w usłudze Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/26/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 410f413fc8450c0ee33c3ca95e860a3e8de34107
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80332604"
---
# <a name="define-a-restful-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Definiowanie profilu technicznego restful w zasadach niestandardowych usługi Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Usługa Azure Active Directory B2C (Azure AD B2C) zapewnia obsługę integracji własnej usługi RESTful. Usługa Azure AD B2C wysyła dane do usługi RESTful w kolekcji oświadczeń wejściowych i odbiera dane z powrotem w kolekcji oświadczeń danych wyjściowych. Aby uzyskać więcej informacji, zobacz [Integrowanie wymiany oświadczeń interfejsu API REST w zasadach niestandardowych usługi Azure AD B2C](custom-policy-rest-api-intro.md).  

## <a name="protocol"></a>Protocol (Protokół)

Atrybut **Nazwa** elementu **Protokołu** musi być ustawiony `Proprietary`na . Atrybut **programu obsługi** musi zawierać w pełni kwalifikowaną nazwę zestawu obsługi protokołu `Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`używanego przez usługę Azure AD B2C: .

W poniższym przykładzie pokazano profil techniczny RESTful:

```XML
<TechnicalProfile Id="REST-UserMembershipValidator">
  <DisplayName>Validate user input data and return loyaltyNumber claim</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  ...
```

## <a name="input-claims"></a>Oświadczenia wejściowe

**InputClaims** element zawiera listę oświadczeń do wysłania do interfejsu API REST. Można również mapować nazwę oświadczenia na nazwę zdefiniowaną w interfejsie API REST. Poniższy przykład pokazuje mapowanie między zasadami a interfejsem API REST. Oświadczenie **givenName** jest wysyłane do interfejsu API REST jako **imię**, podczas gdy **nazwisko** jest wysyłane jako **nazwisko.** Oświadczenie **e-mail** jest ustawione w stanie, w jakim jest.

```XML
<InputClaims>
  <InputClaim ClaimTypeReferenceId="email" />
  <InputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="firstName" />
  <InputClaim ClaimTypeReferenceId="surname" PartnerClaimType="lastName" />
</InputClaims>
```

**InputClaimsTransformations** element może zawierać kolekcję **InputClaimsTransformation** elementów, które są używane do modyfikowania oświadczeń wejściowych lub generowania nowych przed wysłaniem do interfejsu API REST.

## <a name="send-a-json-payload"></a>Wysyłanie ładunku JSON

Profil techniczny interfejsu API REST umożliwia wysłanie złożonego ładunku JSON do punktu końcowego.

Aby wysłać złożoną ładowność JSON:

1. Tworzenie ładunku JSON z [GenerateJson](json-transformations.md) roszczeń transformacji.
1. W profilu technicznym INTERFEJSU API REST:
    1. Dodaj transformację oświadczeń wejściowych `GenerateJson` z odwołaniem do transformacji oświadczeń.
    1. Ustawianie `SendClaimsIn` opcji metadanych na`body`
    1. Ustaw `ClaimUsedForRequestPayload` opcję metadanych na nazwę oświadczenia zawierającego ładunek JSON.
    1. W oświadczeniu wejściowym dodaj odwołanie do oświadczenia wejściowego zawierającego ładunek JSON.

Poniższy `TechnicalProfile` przykład wysyła weryfikacyjną wiadomość e-mail przy użyciu usługi poczty e-mail innej firmy (w tym przypadku SendGrid).

```XML
<TechnicalProfile Id="SendGrid">
  <DisplayName>Use SendGrid's email API to send the code the the user</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://api.sendgrid.com/v3/mail/send</Item>
    <Item Key="AuthenticationType">Bearer</Item>
    <Item Key="SendClaimsIn">Body</Item>
    <Item Key="ClaimUsedForRequestPayload">sendGridReqBody</Item>
  </Metadata>
  <CryptographicKeys>
    <Key Id="BearerAuthenticationToken" StorageReferenceId="B2C_1A_SendGridApiKey" />
  </CryptographicKeys>
  <InputClaimsTransformations>
    <InputClaimsTransformation ReferenceId="GenerateSendGridRequestBody" />
  </InputClaimsTransformations>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="sendGridReqBody" />
  </InputClaims>
</TechnicalProfile>
```

## <a name="output-claims"></a>Oświadczenia wyjściowe

**OutputClaims** element zawiera listę oświadczeń zwracanych przez interfejs API REST. Może być konieczne mapowanie nazwy oświadczenia zdefiniowanego w zasadach na nazwę zdefiniowaną w interfejsie API REST. Można również dołączyć oświadczenia, które nie są zwracane przez dostawcę `DefaultValue` tożsamości interfejsu API REST, tak długo, jak ustawić atrybut.

**OutputClaimsTransformations** element może zawierać kolekcję **OutputClaimsTransformation** elementów, które są używane do modyfikowania oświadczeń danych wyjściowych lub generowania nowych.

W poniższym przykładzie przedstawiono oświadczenie zwrócone przez interfejs API REST:

- **Oświadczenie MembershipId,** które jest mapowane na nazwę oświadczenia **loyaltyNumber.**

Profil techniczny zwraca również oświadczenia, które nie są zwracane przez dostawcę tożsamości:

- Oświadczenie **loyaltyNumberIsNew,** które ma ustawioną wartość domyślną na `true`.

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="loyaltyNumber" PartnerClaimType="MembershipId" />
  <OutputClaim ClaimTypeReferenceId="loyaltyNumberIsNew" DefaultValue="true" />
</OutputClaims>
```

## <a name="metadata"></a>Metadane

| Atrybut | Wymagany | Opis |
| --------- | -------- | ----------- |
| ServiceUrl (ServiceUrl) | Tak | Adres URL punktu końcowego interfejsu API REST. |
| Authenticationtype | Tak | Typ uwierzytelniania wykonywany przez dostawcę oświadczeń RESTful. Możliwe `None`wartości: `Basic` `Bearer`, `ClientCertificate`, , lub . Wartość `None` wskazuje, że interfejs API REST nie jest anonimowy. Wartość `Basic` wskazuje, że interfejs API REST jest zabezpieczony za pomocą uwierzytelniania podstawowego HTTP. Tylko zweryfikowani użytkownicy, w tym usługa Azure AD B2C, mogą uzyskać dostęp do interfejsu API. `ClientCertificate` Wartość (zalecane) wskazuje, że interfejs API REST ogranicza dostęp przy użyciu uwierzytelniania certyfikatu klienta. Tylko usługi, które mają odpowiednie certyfikaty, na przykład usługi Azure AD B2C, można uzyskać dostęp do interfejsu API. Wartość `Bearer` wskazuje, że interfejs API REST ogranicza dostęp przy użyciu tokenu okaziciela klienta OAuth2. |
| AllowInsecureAuthInProdukcja| Nie| `AuthenticationType` Wskazuje, czy można ustawić `none` w środowisku produkcyjnym (`DeploymentMode` [TrustFrameworkPolicy](trustframeworkpolicy.md) jest ustawiona na `Production`, czy nie określono). Możliwe wartości: true lub false (domyślnie). |
| WyślijClaimsIn | Nie | Określa sposób wysyłania oświadczeń wejściowych do dostawcy oświadczeń RESTful. Możliwe `Body` wartości: `Form`(domyślnie), , `Header`lub `QueryString`. Wartość `Body` jest oświadczenie danych wejściowych, który jest wysyłany w treści żądania w formacie JSON. Wartość `Form` jest oświadczenie danych wejściowych, który jest wysyłany w treści żądania w formacie wartości klucza rozdzielonego ampersand "&". Wartość `Header` jest oświadczenie danych wejściowych, który jest wysyłany w nagłówku żądania. Wartość `QueryString` jest oświadczenie danych wejściowych, który jest wysyłany w ciągu kwerendy żądania. Zlecenia HTTP wywoływane przez każdego z nich są następujące:<br /><ul><li>`Body`: POST</li><li>`Form`: POST</li><li>`Header`: GET</li><li>`QueryString`: GET</li></ul> |
| OświadczeniaFormat | Nie | Nie jest obecnie używany, mogą być ignorowane. |
| ClaimUsedForRequestPayload (ClaimUsedForRequestPayload)| Nie | Nazwa oświadczenia ciągu, który zawiera ładunek, który ma być wysyłany do interfejsu API REST. |
| Debugmode | Nie | Uruchamia profil techniczny w trybie debugowania. Możliwe wartości: `true` `false` , lub (domyślnie). W trybie debugowania interfejs API REST może zwracać więcej informacji. Zobacz sekcję [Zwracany komunikat o błędzie.](#returning-error-message) |
| IncludeClaimResolvingInClaimsHandling  | Nie | W przypadku oświadczeń wejściowych i wyjściowych określa, czy [rozpoznawanie oświadczeń](claim-resolver-overview.md) jest uwzględnione w profilu technicznym. Możliwe wartości: `true` `false`  , lub (domyślnie). Jeśli chcesz użyć programu rozpoznawania oświadczeń w profilu technicznym, ustaw to na `true`. |
| ResolveJsonPathsInJsonTokens  | Nie | Wskazuje, czy profil techniczny rozpoznaje ścieżki JSON. Możliwe wartości: `true` `false` , lub (domyślnie). Użyj tych metadanych do odczytu danych z zagnieżdżonego elementu JSON. W [OutputClaim](technicalprofiles.md#outputclaims)ustaw `PartnerClaimType` na element ścieżki JSON, który chcesz wyprowadzić. Na przykład: `firstName.localized` `data.0.to.0.email`, lub .|
| UżyjClaimAsBearerToken| Nie| Nazwa oświadczenia, który zawiera token okaziciela.|

## <a name="cryptographic-keys"></a>Klucze kryptograficzne

Jeśli typ uwierzytelniania jest `None`ustawiony na , **cryptographicKeys** element nie jest używany.

```XML
<TechnicalProfile Id="REST-API-SignUp">
  <DisplayName>Validate user's input data and return loyaltyNumber claim</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://your-app-name.azurewebsites.NET/api/identity/signup</Item>
    <Item Key="AuthenticationType">None</Item>
    <Item Key="SendClaimsIn">Body</Item>
  </Metadata>
</TechnicalProfile>
```

Jeśli typ uwierzytelniania jest `Basic`ustawiony na , **CryptographicKeys** element zawiera następujące atrybuty:

| Atrybut | Wymagany | Opis |
| --------- | -------- | ----------- |
| PodstawoweAuthenticationUsername | Tak | Nazwa użytkownika używana do uwierzytelniania. |
| BasicAuthenticationPassword | Tak | Hasło używane do uwierzytelniania. |

W poniższym przykładzie pokazano profil techniczny z uwierzytelnianiem podstawowym:

```XML
<TechnicalProfile Id="REST-API-SignUp">
  <DisplayName>Validate user's input data and return loyaltyNumber claim</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://your-app-name.azurewebsites.NET/api/identity/signup</Item>
    <Item Key="AuthenticationType">Basic</Item>
    <Item Key="SendClaimsIn">Body</Item>
  </Metadata>
  <CryptographicKeys>
    <Key Id="BasicAuthenticationUsername" StorageReferenceId="B2C_1A_B2cRestClientId" />
    <Key Id="BasicAuthenticationPassword" StorageReferenceId="B2C_1A_B2cRestClientSecret" />
  </CryptographicKeys>
</TechnicalProfile>
```

Jeśli typ uwierzytelniania jest `ClientCertificate`ustawiony na , **CryptographicKeys** element zawiera następujący atrybut:

| Atrybut | Wymagany | Opis |
| --------- | -------- | ----------- |
| Clientcertificate | Tak | Certyfikat X509 (zestaw kluczy RSA) używany do uwierzytelniania. |

```XML
<TechnicalProfile Id="REST-API-SignUp">
  <DisplayName>Validate user's input data and return loyaltyNumber claim</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://your-app-name.azurewebsites.NET/api/identity/signup</Item>
    <Item Key="AuthenticationType">ClientCertificate</Item>
    <Item Key="SendClaimsIn">Body</Item>
  </Metadata>
  <CryptographicKeys>
    <Key Id="ClientCertificate" StorageReferenceId="B2C_1A_B2cRestClientCertificate" />
  </CryptographicKeys>
</TechnicalProfile>
```

Jeśli typ uwierzytelniania jest `Bearer`ustawiony na , **CryptographicKeys** element zawiera następujący atrybut:

| Atrybut | Wymagany | Opis |
| --------- | -------- | ----------- |
| OkazicielaUthenticationToken | Nie | OAuth 2.0 Żeton na okaziciela. |

```XML
<TechnicalProfile Id="REST-API-SignUp">
  <DisplayName>Validate user's input data and return loyaltyNumber claim</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://your-app-name.azurewebsites.NET/api/identity/signup</Item>
    <Item Key="AuthenticationType">Bearer</Item>
    <Item Key="SendClaimsIn">Body</Item>
  </Metadata>
  <CryptographicKeys>
    <Key Id="BearerAuthenticationToken" StorageReferenceId="B2C_1A_B2cRestClientAccessToken" />
  </CryptographicKeys>
</TechnicalProfile>
```

## <a name="returning-error-message"></a>Zwracany komunikat o błędzie

Interfejs API REST może wymagać zwrócenia komunikatu o błędzie, takiego jak "Użytkownik nie został znaleziony w systemie CRM". Jeśli wystąpi błąd, interfejs API REST powinien zwrócić komunikat o błędzie HTTP 4xx, taki jak 400 (złe żądanie) lub 409 (konflikt) kod stanu odpowiedzi. Treść odpowiedzi zawiera komunikat o błędzie sformatowany w JSON:

```JSON
{
  "version": "1.0.0",
  "status": 409,
  "code": "API12345",
  "requestId": "50f0bd91-2ff4-4b8f-828f-00f170519ddb",
  "userMessage": "Message for the user",
  "developerMessage": "Verbose description of problem and how to fix it.",
  "moreInfo": "https://restapi/error/API12345/moreinfo"
}
```

| Atrybut | Wymagany | Opis |
| --------- | -------- | ----------- |
| version | Tak | Twoja wersja interfejsu API REST. Na przykład: 1.0.1 |
| status | Tak | Musi być 409 |
| kod | Nie | Kod błędu od dostawcy punktu końcowego RESTful, `DebugMode` który jest wyświetlany po włączeniu. |
| Requestid | Nie | Identyfikator żądania od dostawcy punktu końcowego RESTful, `DebugMode` który jest wyświetlany po włączeniu. |
| userMessage | Tak | Komunikat o błędzie wyświetlany użytkownikowi. |
| developerMessage | Nie | Pełny opis problemu i sposób jego rozwiązania, który jest wyświetlany `DebugMode` po włączeniu. |
| więcejInfo | Nie | Identyfikator URI, który wskazuje na dodatkowe `DebugMode` informacje, które są wyświetlane, gdy jest włączona. |


W poniższym przykładzie pokazano klasę Języka C#, która zwraca komunikat o błędzie:

```csharp
public class ResponseContent
{
  public string version { get; set; }
  public int status { get; set; }
  public string code { get; set; }
  public string userMessage { get; set; }
  public string developerMessage { get; set; }
  public string requestId { get; set; }
  public string moreInfo { get; set; }
}
```

## <a name="next-steps"></a>Następne kroki

Zobacz następujące artykuły, aby zapoznać się z przykładami korzystania z profilu technicznego RESTful:

- [Integrowanie wymiany oświadczeń interfejsu API REST w zasadach niestandardowych usługi Azure AD B2C](custom-policy-rest-api-intro.md)
- [Przewodnik: Integrowanie wymiany oświadczeń interfejsu API REST w podróży użytkownika usługi Azure AD B2C jako sprawdzanie poprawności danych wejściowych użytkownika](custom-policy-rest-api-claims-validation.md)
- [Przewodnik: Dodawanie wymiany oświadczeń interfejsu API REST do zasad niestandardowych w usłudze Azure Active Directory B2C](custom-policy-rest-api-claims-validation.md)
- [Zabezpieczanie usług INTERFEJSU API REST](secure-rest-api.md)

