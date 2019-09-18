---
title: Zdefiniuj profil techniczny RESTful w zasadach niestandardowych w Azure Active Directory B2C | Microsoft Docs
description: Zdefiniuj profil techniczny RESTful w zasadach niestandardowych w Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 13eedeb66d826d212b814fac321f920e78758cb8
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/17/2019
ms.locfileid: "71063736"
---
# <a name="define-a-restful-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Zdefiniuj profil techniczny RESTful w zasadach niestandardowych Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) zapewnia obsługę własnej usługi RESTful. Azure AD B2C wysyła dane do usługi RESTful w kolekcji oświadczeń wejściowych i odbiera dane z powrotem w kolekcji oświadczeń wyjściowych. Dzięki integracji z usługą RESTful można:

- **Sprawdzanie poprawności danych wejściowych użytkownika** — uniemożliwia utrwalanie nieprawidłowych danych w Azure AD B2C. Jeśli wartość użytkownika jest nieprawidłowa, usługa RESTful zwraca komunikat o błędzie, który nakazuje użytkownikowi dostarczenie wpisu. Na przykład możesz sprawdzić, czy w bazie danych klienta istnieje adres e-mail podany przez użytkownika.
- **Zastąp oświadczenia wejściowe** — umożliwia ponowne formatowanie wartości w oświadczeniach wejściowych. Na przykład, jeśli użytkownik wprowadzi imię i nazwisko we wszystkich małych i wielkich literach, można sformatować nazwę tylko przy użyciu pierwszej litery.
- **Wzbogacanie danych użytkownika** — umożliwia dalsze integrację z firmowymi aplikacjami biznesowymi. Na przykład usługa RESTful może otrzymać adres e-mail użytkownika, wysłać zapytanie do bazy danych klienta i zwrócić numer lojalnościowy użytkownika w celu Azure AD B2C. Oświadczenia zwrotne mogą być przechowywane, oceniane w następnych krokach aranżacji lub zawarte w tokenie dostępu.
- **Uruchamianie niestandardowej logiki biznesowej** — umożliwia wysyłanie powiadomień wypychanych, aktualizowanie firmowych baz danych, uruchamianie procesu migracji użytkowników, zarządzanie uprawnieniami, inspekcję baz danych i wykonywanie innych akcji.

Zasady mogą wysyłać oświadczenia wejściowe do interfejsu API REST. Interfejs API REST może również zwracać oświadczenia wyjściowe, których można użyć w dalszej części zasad, lub może zgłosić komunikat o błędzie. Integrację z usługami RESTful można zaprojektować w następujący sposób:

- **Sprawdzanie poprawności profilu technicznego** — Walidacja profilu technicznego wywołuje usługę RESTful. Profil techniczny weryfikacji weryfikuje dane dostarczone przez użytkownika przed kontynuowaniem podróży użytkownika. W profilu technicznym walidacji komunikat o błędzie jest wyświetlany na stronie z własnym poproszonym i zwracany w oświadczeniach danych wyjściowych.
- **Wymiana oświadczeń** — wywołanie usługi RESTful jest nawiązywane przez krok aranżacji. W tym scenariuszu nie ma interfejsu użytkownika do renderowania komunikatu o błędzie. Jeśli interfejs API REST zwróci błąd, użytkownik zostanie przekierowany z powrotem do aplikacji jednostki uzależnionej z komunikatem o błędzie.

## <a name="protocol"></a>Protocol

Atrybut **name** elementu **Protocol** musi być ustawiony na `Proprietary`. Atrybut **programu obsługi** musi zawierać w pełni kwalifikowaną nazwę zestawu programu obsługi protokołu, który jest używany przez Azure AD B2C: `Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`.

W poniższym przykładzie przedstawiono profil techniczny RESTful:

```XML
<TechnicalProfile Id="REST-UserMembershipValidator">
  <DisplayName>Validate user input data and return loyaltyNumber claim</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  ...
```

## <a name="input-claims"></a>Oświadczenia wejściowe

Element **InputClaims** zawiera listę oświadczeń do wysłania do interfejsu API REST. Możesz również zmapować nazwę swojego zgłoszenia na nazwę zdefiniowaną w interfejsie API REST. Poniższy przykład pokazuje mapowanie między zasadami i interfejsem API REST. **Dana** wartość tego żądania jest wysyłana do interfejsu API REST jako **FirstName**, a **nazwisko** jest wysyłane jako **LastName**. To ustawienie jest ustawione na wartość.

```XML
<InputClaims>
  <InputClaim ClaimTypeReferenceId="email" />
  <InputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="firstName" />
  <InputClaim ClaimTypeReferenceId="surname" PartnerClaimType="lastName" />
</InputClaims>
```

Element **InputClaimsTransformations** może zawierać kolekcję elementów **InputClaimsTransformation** , które są używane do modyfikowania oświadczeń wejściowych lub generować nowe przed wysłaniem do interfejsu API REST.

## <a name="output-claims"></a>Oświadczenia wyjściowe

Element **OutputClaims** zawiera listę oświadczeń zwracanych przez interfejs API REST. Może być konieczne zamapowanie nazwy żądania zdefiniowanego w zasadach na nazwę zdefiniowaną w interfejsie API REST. Można również uwzględnić oświadczenia, które nie są zwracane przez dostawcę tożsamości interfejsu API REST, o ile atrybut ten `DefaultValue` jest ustawiony.

Element **OutputClaimsTransformations** może zawierać kolekcję elementów **OutputClaimsTransformation** , które są używane do modyfikowania oświadczeń wyjściowych lub generowania nowych.

Poniższy przykład przedstawia zgłoszenie zwrócone przez interfejs API REST:

- **MembershipId** , która jest mapowana na nazwę żądania **loyaltyNumber** .

Profil techniczny zwraca również oświadczenia, które nie są zwracane przez dostawcę tożsamości:

- **LoyaltyNumberIsNew** , które ma wartość domyślną ustawioną na `true`.

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="loyaltyNumber" PartnerClaimType="MembershipId" />
  <OutputClaim ClaimTypeReferenceId="loyaltyNumberIsNew" DefaultValue="true" />
</OutputClaims>
```

## <a name="metadata"></a>Metadane

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| ServiceUrl | Tak | Adres URL punktu końcowego interfejsu API REST. |
| AuthenticationType | Tak | Typ uwierzytelniania wykonywanego przez dostawcę oświadczeń RESTful. Możliwe wartości: `None`, `Basic`, lub `ClientCertificate`. `None` Wartość wskazuje, że interfejs API REST nie jest anonimowy. `Basic` Wartość oznacza, że interfejs API REST jest zabezpieczony za pomocą uwierzytelniania podstawowego protokołu HTTP. Tylko zweryfikowani użytkownicy, w tym Azure AD B2C, mogą uzyskiwać dostęp do interfejsu API. Wartość `ClientCertificate` (zalecane) wskazuje, że interfejs API REST ogranicza dostęp przy użyciu uwierzytelniania certyfikatu klienta. Tylko usługi, które mają odpowiednie certyfikaty, takie jak Azure AD B2C, mogą uzyskać dostęp do usługi. |
| SendClaimsIn | Nie | Określa sposób, w jaki oświadczenia wejściowe są wysyłane do dostawcy oświadczeń RESTful. Możliwe wartości: `Body` (wartość domyślna) `Form`, `Header`, lub `QueryString`. `Body` Wartość jest wartością wejściową, która jest wysyłana w treści żądania w formacie JSON. `Form` Wartość jest wartością wejściową, która jest wysyłana w treści żądania w formacie wartości klucza "&". `Header` Wartość jest wartością wejściową, która jest wysyłana w nagłówku żądania. `QueryString` Wartość jest wartością wejściową, która jest wysyłana w ciągu zapytania żądania. |
| ClaimsFormat | Nie | Określa format oświadczeń wyjściowych. Możliwe wartości: `Body` (wartość domyślna) `Form`, `Header`, lub `QueryString`. `Body` Wartość jest wynikiem zgłoszenia, który jest wysyłany w treści żądania w formacie JSON. `Form` Wartość jest wynikiem zgłoszenia, który jest wysyłany w treści żądania w formacie wartości klucza "&". `Header` Wartość jest wynikiem zgłoszenia, który jest wysyłany w nagłówku żądania. `QueryString` Wartość jest wynikiem zgłoszenia wysyłanego w ciągu zapytania żądania. |
| Debugujmode | Nie | Uruchamia profil techniczny w trybie debugowania. W trybie debugowania interfejs API REST może zwrócić więcej informacji. Zobacz sekcję zwracającą komunikat o błędzie. |

## <a name="cryptographic-keys"></a>Klucze kryptograficzne

Jeśli typ uwierzytelniania jest ustawiony na `None`, element **CryptographicKeys** nie jest używany.

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

Jeśli typ uwierzytelniania jest ustawiony na `Basic`, element **CryptographicKeys** zawiera następujące atrybuty:

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| BasicAuthenticationUsername | Tak | Nazwa użytkownika, która jest używana do uwierzytelniania. |
| BasicAuthenticationPassword | Tak | Hasło, które jest używane do uwierzytelniania. |

Poniższy przykład przedstawia profil techniczny z uwierzytelnianiem podstawowym:

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

Jeśli typ uwierzytelniania jest ustawiony na `ClientCertificate`, element **CryptographicKeys** zawiera następujący atrybut:

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| Kolekcja | Tak | Certyfikat x509 (zestaw kluczy RSA) do uwierzytelniania. |

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

## <a name="returning-error-message"></a>Zwracany komunikat o błędzie

Interfejs API REST może wymagać zwrócenia komunikatu o błędzie, takiego jak "nie znaleziono użytkownika w systemie CRM". W przypadku wystąpienia błędu interfejs API REST powinien zwrócić komunikat o błędzie HTTP 409 (kod stanu odpowiedzi konfliktu) z następującymi atrybutami:

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| version | Tak | 1.0.0 |
| status | Tak | 409 |
| code | Nie | Kod błędu od dostawcy punktu końcowego RESTful, który jest wyświetlany, gdy `DebugMode` jest włączony. |
| requestId | Nie | Identyfikator żądania od dostawcy punktu końcowego RESTful, który jest wyświetlany, gdy `DebugMode` jest włączony. |
| userMessage | Tak | Komunikat o błędzie, który jest widoczny dla użytkownika. |
| developerMessage | Nie | Pełny opis problemu i sposób jego naprawy, który jest wyświetlany, gdy `DebugMode` jest włączony. |
| moreInfo | Nie | Identyfikator URI wskazujący na dodatkowe informacje, które są wyświetlane, `DebugMode` gdy jest włączone. |

Poniższy przykład przedstawia interfejs API REST, który zwraca komunikat o błędzie sformatowany w formacie JSON:

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

Poniższy przykład pokazuje C# klasę, która zwraca komunikat o błędzie:

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

## <a name="examples"></a>Przykłady:
- [Integruj wymianę oświadczeń interfejsu API REST w trakcie Azure AD B2C użytkownika w trakcie sprawdzania poprawności danych wejściowych użytkownika](active-directory-b2c-custom-rest-api-netfw.md)
- [Zabezpieczanie usług RESTful za pomocą uwierzytelniania podstawowego protokołu HTTP](active-directory-b2c-custom-rest-api-netfw-secure-basic.md)
- [Zabezpieczanie usługi RESTful przy użyciu certyfikatów klienta](active-directory-b2c-custom-rest-api-netfw-secure-cert.md)
- [Instruktaż Integruj wymianę oświadczeń interfejsu API REST w Azure AD B2C przeniesieniu użytkownika na dane wejściowe użytkownika](active-directory-b2c-rest-api-validation-custom.md)

 














