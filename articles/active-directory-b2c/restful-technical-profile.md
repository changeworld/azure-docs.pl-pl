---
title: Zdefiniuj RESTful profilu technicznego w zasadach niestandardowych w usłudze Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: W przypadku zasad niestandardowych w usłudze Azure Active Directory B2C, należy zdefiniować RESTful profilu technicznego.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 7ff14af756a55ccc6bbf40dd39d49c5168f4af1f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60418306"
---
# <a name="define-a-restful-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Zdefiniuj RESTful profilu technicznego w zasadach niestandardowych usługi Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Usługa Azure Active Directory (Azure AD) B2C zapewnia obsługę usługi RESTful. Usługa Azure AD B2C wysyła dane do usługi RESTful w danych wejściowych oświadczeń kolekcji i odbiera dane z powrotem w kolekcji oświadczeń danych wyjściowych. Dzięki integracji z usługą RESTful można wykonywać następujące czynności:

- **Sprawdza poprawność danych wejściowych użytkownika** -źle sformułowane chroni dane przed utrwaleniem do usługi Azure AD B2C. Jeśli wartość przez użytkownika nie jest prawidłowy, usługi RESTful zwraca komunikat o błędzie z monitem użytkownika o podanie wpis. Na przykład można sprawdzić, czy adres e-mail, dostarczone przez użytkownika istnieje w bazie danych klienta.
- **Zastąp oświadczeń wejściowych** — umożliwia ponownego formatowania wartości w danych wejściowych oświadczeń. Na przykład jeśli użytkownik wprowadzi nazwę pierwszego wszystkie małe lub wielkie litery, możesz sformatować nazwy tylko pierwszą literą wielką literą.
- **Wzbogacanie danych użytkownika** — pozwala na dalsze zintegrować z aplikacji firmowych line-of-business. Na przykład usługi RESTful może odbierać adres e-mail użytkownika, wykonywania zapytań klienta w bazie danych i zwracać numer lojalności użytkownika do usługi Azure AD B2C. Zwracany oświadczeń może być przechowywany, ocenione w następnych krokach aranżacji lub zawarte w tokenie dostępu.
- **Uruchom niestandardową logikę biznesową** — umożliwia wysyłanie powiadomień wypychanych, aktualizacji baz danych firmowych, uruchamiany jest proces migracji użytkowników, zarządzanie uprawnieniami, inspekcji bazy danych i wykonywania innych akcji.

Zasady mogą wysyłać oświadczenia wejściowego do interfejsu API REST. Interfejs API REST może również zwracać oświadczeń danych wyjściowych, które można użyć później w zasadach lub może ona zgłosić komunikat o błędzie. Integracja z usług RESTful można zaprojektować w następujący sposób:

- **Profil techniczny weryfikacji** -profilu technicznego weryfikacji wywołań usługi RESTful. Profil techniczny weryfikacji sprawdza poprawność danych wprowadzonych przez użytkownika przed kontynuacją podróży użytkownika. Z profilu technicznego sprawdzania poprawności komunikat o błędzie jest wyświetlana na stronie samodzielnie i zwracany w danych wyjściowych oświadczeń.
- **Wymiana oświadczeń** -wywołanie usługi RESTful za pomocą kroku aranżacji. W tym scenariuszu istnieje bez interfejsu użytkownika, do renderowania komunikatu o błędzie. Jeśli Twój interfejs API REST zwraca błąd, użytkownik jest przekierowany z powrotem do aplikacji jednostki uzależnionej z komunikatem o błędzie.

## <a name="protocol"></a>Protokół

**Nazwa** atrybutu **protokołu** element musi być równa `Proprietary`. **Obsługi** atrybutu musi zawierać w pełni kwalifikowaną nazwę zestawu obsługi protokołu, który jest używany przez usługę Azure AD B2C: `Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`.

Poniższy przykład przedstawia RESTful profilu technicznego:

```XML
<TechnicalProfile Id="REST-UserMembershipValidator">
  <DisplayName>Validate user input data and return loyaltyNumber claim</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  ...    
```

## <a name="input-claims"></a>Oświadczeń wejściowych

**InputClaims** element zawiera listę oświadczenia do wysłania do interfejsu API REST. Można również mapować nazwę Twojego oświadczenie z nazwą zdefiniowaną w interfejsie API REST. Poniższy przykład przedstawia mapowanie między zasad i interfejsu API REST. **GivenName** oświadczenia są wysyłane do interfejsu API REST jako **firstName**, podczas gdy **nazwisko** jest wysyłany jako **lastName**. **E-mail** oświadczeń jest ustawiona, ponieważ jest.

```XML
<InputClaims>
  <InputClaim ClaimTypeReferenceId="email" />
  <InputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="firstName" />
  <InputClaim ClaimTypeReferenceId="surname" PartnerClaimType="lastName" />
</InputClaims>
```

**InputClaimsTransformations** element może zawierać zbiór **InputClaimsTransformation** elementy, które są używane do modyfikowania danych wejściowych oświadczeń lub wygenerować nowe przed wysłaniem do interfejsu API REST.

## <a name="output-claims"></a>Oświadczeń danych wyjściowych

**OutputClaims** element zawiera listę oświadczeń zwracane przez interfejs API REST. Może być konieczne mapowania nazwy oświadczenia, zdefiniowane w zasadach do nazwy zdefiniowane w interfejsie API REST. Możesz również uwzględnić oświadczenia, które nie są zwracane przez interfejs API REST dostawcy tożsamości, tak długo, jak można ustawić `DefaultValue` atrybutu.

**OutputClaimsTransformations** element może zawierać zbiór **OutputClaimsTransformation** elementy, które są używane do modyfikowania oświadczeń danych wyjściowych lub wygenerować nowe.

Poniższy przykład przedstawia oświadczenia zwrócone przez interfejs API REST:

- **MembershipId** oświadczenia, który jest zamapowany **loyaltyNumber** Nazwa oświadczenia.

Profil techniczny zwraca również wartość oświadczenia, które nie są zwracane przez dostawcę tożsamości: 

- **LoyaltyNumberIsNew** oświadczenia, który ma wartość domyślna równa `true`.

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="loyaltyNumber" PartnerClaimType="MembershipId" />
  <OutputClaim ClaimTypeReferenceId="loyaltyNumberIsNew" DefaultValue="true" />
</OutputClaims>
```

## <a name="metadata"></a>Metadane

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| ServiceUrl | Yes | Adres URL punktu końcowego interfejsu API REST. | 
| AuthenticationType | Yes | Typ uwierzytelniania jest wykonywane przez dostawcę oświadczeń typu RESTful. Możliwe wartości: `None`, `Basic`, lub `ClientCertificate`. `None` Wartość wskazuje, że interfejs API REST jest anonimowy. `Basic` Wartość wskazuje, że interfejs API REST jest zabezpieczony za pomocą podstawowego uwierzytelniania protokołu HTTP. Weryfikowane tylko użytkowników, w tym usługi Azure AD B2C, mogą uzyskiwać dostęp do interfejsu API. `ClientCertificate` (Zalecane) wartość wskazuje, że interfejs API REST ogranicza dostęp przy użyciu uwierzytelniania certyfikatu klienta. Tylko w przypadku usług, które mają odpowiednie certyfikaty, takich jak usługi Azure AD B2C może uzyskać dostęp do usługi. | 
| SendClaimsIn | Nie | Określa, jak oświadczeń wejściowych są wysyłane do dostawcy oświadczeń typu RESTful. Możliwe wartości: `Body` (ustawienie domyślne), `Form`, `Header`, lub `QueryString`. `Body` Wartość oświadczeń przychodzących, który będzie wysyłany w treści żądania w formacie JSON. `Form` Wartość oświadczeń przychodzących, który będzie wysyłany w treści żądania w handlowe "i" "&" rozdzielonych format wartości klucza. `Header` Wartość oświadczeń przychodzących, który będzie wysyłany w nagłówku żądania. `QueryString` Wartość oświadczeń przychodzących, który będzie wysyłany w ciągu zapytania żądania. | 
| ClaimsFormat | Nie | Określa format dla oświadczeń danych wyjściowych. Możliwe wartości: `Body` (ustawienie domyślne), `Form`, `Header`, lub `QueryString`. `Body` Wartość oświadczeń danych wyjściowych, który będzie wysyłany w treści żądania w formacie JSON. `Form` Wartość oświadczeń wychodzących, który będzie wysyłany w treści żądania w handlowe "i" "&" rozdzielonych format wartości klucza. `Header` Wartość oświadczeń danych wyjściowych, który będzie wysyłany w nagłówku żądania. `QueryString` Wartość oświadczeń danych wyjściowych, który będzie wysyłany w ciągu zapytania żądania. | 
| Element DebugMode | Nie | Uruchamia profil techniczny w trybie debugowania. W trybie debugowania interfejs API REST może zwrócić więcej informacji. Zobacz sekcję zwracanie komunikatu błędu. | 

## <a name="cryptographic-keys"></a>Klucze kryptograficzne

Jeśli ustawiono typ uwierzytelniania `None`, **CryptographicKeys** element nie jest używany.

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

Jeśli ustawiono typ uwierzytelniania `Basic`, **CryptographicKeys** element zawiera następujące atrybuty:

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| BasicAuthenticationUsername | Yes | Nazwa użytkownika, który jest używany do uwierzytelniania. | 
| BasicAuthenticationPassword | Yes | Hasło, które służy do uwierzytelniania. |

Poniższy przykład przedstawia profilu technicznego z uwierzytelnianiem podstawowym:

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

Jeśli ustawiono typ uwierzytelniania `ClientCertificate`, **CryptographicKeys** element zawiera następujący atrybut:

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| ClientCertificate | Yes | X509 certyfikat (zestawu kluczy RSA) ma być używany do uwierzytelniania. | 

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

## <a name="returning-error-message"></a>Zwraca komunikat o błędzie

Interfejs API REST może być konieczne zwrócić komunikat o błędzie, takie jak "nie znaleziono użytkownika w systemie CRM". Błąd występuje, interfejs API REST powinien zwrócić komunikat o błędzie HTTP 409 (konflikt kod stanu odpowiedzi) z następującymi atrybutami:

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| version | Yes | 1.0.0 | 
| status | Yes | 409 | 
| kod | Nie | Kod błędu z dostawcy typu RESTful punktu końcowego, który jest wyświetlany, gdy `DebugMode` jest włączona. | 
| requestId | Nie | Identyfikator żądania od dostawcy punktem końcowym RESTful, które jest wyświetlane, gdy `DebugMode` jest włączona. | 
| userMessage | Yes | Komunikat o błędzie, który jest wyświetlany użytkownikowi. | 
| developerMessage | Nie | Szczegółowy opis problemu i jak rozwiązać problem, który jest wyświetlany, gdy `DebugMode` jest włączona. | 
| moreInfo | Nie | Identyfikator URI, który wskazuje, aby uzyskać dodatkowe informacje, które jest wyświetlane, gdy `DebugMode` jest włączona. | 

Interfejs API REST, która zwraca sformatowany komunikat o błędzie w formacie JSON można znaleźć w poniższym przykładzie:

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

Klasa C#, która zwraca komunikat o błędzie można znaleźć w poniższym przykładzie:

```C#
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
- [Integracja interfejsu API REST wymianą oświadczeń podróży użytkownika usługi Azure AD B2C jako sprawdzanie poprawności danych wejściowych użytkownika](active-directory-b2c-custom-rest-api-netfw.md) 
- [Zabezpieczanie usługi RESTful za pomocą podstawowego uwierzytelniania protokołu HTTP](active-directory-b2c-custom-rest-api-netfw-secure-basic.md)
- [Zabezpieczanie usługi RESTful przy użyciu certyfikatów klienta](active-directory-b2c-custom-rest-api-netfw-secure-cert.md)
- [Wskazówki: Integracja interfejsu API REST wymianą oświadczeń podróży użytkownika usługi Azure AD B2C jako sprawdzanie poprawności danych wejściowych użytkownika](active-directory-b2c-rest-api-validation-custom.md)

 














