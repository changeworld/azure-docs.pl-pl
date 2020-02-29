---
title: Wymiana oświadczeń interfejsu API REST — Azure Active Directory B2C
description: Dodaj wymianę oświadczeń interfejsu API REST do zasad niestandardowych w Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/21/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 351b41f45fb84384ec0193f8e3130347d0b19401
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/29/2020
ms.locfileid: "78189093"
---
# <a name="add-rest-api-claims-exchanges-to-custom-policies-in-azure-active-directory-b2c"></a>Dodawanie wymiany oświadczeń interfejsu API REST do zasad niestandardowych w Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Interakcję z interfejsem API RESTful można dodać do [zasad niestandardowych](custom-policy-overview.md) w Azure Active Directory B2C (Azure AD B2C). W tym artykule opisano sposób tworzenia Azure AD B2Cj podróży użytkowników, która współdziała z usługami RESTful Services.

Interakcja obejmuje wymianę informacji między oświadczeniami interfejsu API REST a Azure AD B2C. Wymiany oświadczeń mają następującą charakterystykę:

- Może być zaprojektowana jako krok aranżacji.
- Może wyzwalać akcję zewnętrzną. Na przykład może rejestrować zdarzenie w zewnętrznej bazie danych.
- Można go użyć, aby pobrać wartość, a następnie zapisać ją w bazie danych użytkownika.
- Może zmienić przepływ wykonywania.

Scenariusz przedstawiony w tym artykule obejmuje następujące akcje:

1. Wyszukaj użytkownika w systemie zewnętrznym.
2. Zapoznaj się z miastom, w którym użytkownik jest zarejestrowany.
3. Zwróć ten atrybut do aplikacji jako element Claim.

## <a name="prerequisites"></a>Wymagania wstępne

- Wykonaj kroki opisane w temacie Wprowadzenie [do zasad niestandardowych](custom-policy-get-started.md).
- Punkt końcowy interfejsu API REST do współpracy z usługą. W tym artykule jest na przykład stosowana prosta funkcja platformy Azure. Aby utworzyć funkcję platformy Azure, zobacz [Tworzenie pierwszej funkcji w Azure Portal](../azure-functions/functions-create-first-azure-function.md).

## <a name="prepare-the-api"></a>Przygotowywanie interfejsu API

W tej sekcji przygotowasz funkcję platformy Azure, aby otrzymać wartość `email`, a następnie zwrócić wartość `city`, która może być używana przez Azure AD B2C jako rolę.

Zmień plik Run. CSX dla funkcji platformy Azure, która została utworzona, aby użyć następującego kodu:

```csharp
#r "Newtonsoft.Json"

using System.Net;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Primitives;
using Newtonsoft.Json;

public static async Task<IActionResult> Run(HttpRequest req, ILogger log)
{
  log.LogInformation("C# HTTP trigger function processed a request.");
  string email = req.Query["email"];
  string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
  dynamic data = JsonConvert.DeserializeObject(requestBody);
  email = email ?? data?.email;

  return email != null
    ? (ActionResult)new OkObjectResult(
      new ResponseContent
      {
        version = "1.0.0",
        status = (int) HttpStatusCode.OK,
        city = "Redmond"
      })
      : new BadRequestObjectResult("Please pass an email on the query string or in the request body");
}

public class ResponseContent
{
    public string version { get; set; }
    public int status { get; set; }
    public string city {get; set; }
}
```

## <a name="configure-the-claims-exchange"></a>Konfigurowanie wymiany oświadczeń

Profil techniczny zapewnia konfigurację dla wymiany roszczeń.

Otwórz plik *TrustFrameworkExtensions. XML* i Dodaj następujący element **ClaimsProvider** XML wewnątrz elementu **ClaimsProviders** .

```XML
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="AzureFunctions-WebHook">
      <DisplayName>Azure Function Web Hook</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://myfunction.azurewebsites.net/api/HttpTrigger1?code=bAZ4lLy//ZHZxmncM8rI7AgjQsrMKmVXBpP0vd9smOzdXDDUIaLljA==</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <!-- Set AuthenticationType to Basic or ClientCertificate in production environments -->
        <Item Key="AuthenticationType">None</Item>
        <!-- REMOVE the following line in production environments -->
        <Item Key="AllowInsecureAuthInProduction">true</Item>
      </Metadata>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="email" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="city" PartnerClaimType="city" />
      </OutputClaims>
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

Element **InputClaims** definiuje oświadczenia, które są wysyłane do usługi REST. W tym przykładzie wartość `givenName` żądania jest wysyłana do usługi REST jako `email`a żądania. Element **OutputClaims** definiuje oświadczenia, które są oczekiwane w usłudze Rest.

Komentarze powyżej `AuthenticationType` i `AllowInsecureAuthInProduction` określają zmiany, które należy wykonać po przejściu do środowiska produkcyjnego. Aby dowiedzieć się, jak zabezpieczyć interfejsy API usługi RESTful w środowisku produkcyjnym, zobacz [Secure RESTful interfejsy API z uwierzytelnianiem Basic](secure-rest-api-dotnet-basic-auth.md) i [Secure RESTful API z uwierzytelnianiem certyfikatu](secure-rest-api-dotnet-certificate-auth.md).

## <a name="add-the-claim-definition"></a>Dodaj definicję żądania

Dodaj definicję dla `city` wewnątrz elementu **BuildingBlocks** . Ten element można znaleźć na początku pliku TrustFrameworkExtensions. XML.

```XML
<BuildingBlocks>
  <ClaimsSchema>
    <ClaimType Id="city">
      <DisplayName>City</DisplayName>
      <DataType>string</DataType>
      <UserHelpText>Your city</UserHelpText>
      <UserInputType>TextBox</UserInputType>
    </ClaimType>
  </ClaimsSchema>
</BuildingBlocks>
```

## <a name="add-an-orchestration-step"></a>Dodaj krok aranżacji

Istnieje wiele przypadków użycia, w których wywołanie interfejsu API REST może być używane jako krok aranżacji. Jako krok aranżacji może służyć jako aktualizacja systemu zewnętrznego, gdy użytkownik pomyślnie ukończy zadanie, takie jak rejestracja w czasie pierwszym lub jako aktualizacja profilu, aby zachować synchronizację informacji. W takim przypadku służy do rozszerzenia informacji dostarczonych do aplikacji po przeprowadzeniu edycji profilu.

Dodaj krok do profilu Edytuj podróż użytkownika. Po uwierzytelnieniu użytkownika (kroki aranżacji 1-4 w poniższym kodzie XML), a użytkownik podał zaktualizowane informacje o profilu (krok 5). Skopiuj z pliku *TrustFrameworkBase. XML* do pliku *TrustFrameworkExtensions. XML* wewnątrz elementu **UserJourneys** . Następnie wprowadź modyfikację jako krok 6.

```XML
<OrchestrationStep Order="6" Type="ClaimsExchange">
  <ClaimsExchanges>
    <ClaimsExchange Id="GetLoyaltyData" TechnicalProfileReferenceId="AzureFunctions-WebHook" />
  </ClaimsExchanges>
</OrchestrationStep>
```

Końcowy kod XML dla podróży użytkownika powinien wyglądać podobnie do tego przykładu:

```XML
<UserJourney Id="ProfileEdit">
  <OrchestrationSteps>
    <OrchestrationStep Order="1" Type="ClaimsProviderSelection" ContentDefinitionReferenceId="api.idpselections">
      <ClaimsProviderSelections>
        <ClaimsProviderSelection TargetClaimsExchangeId="FacebookExchange" />
        <ClaimsProviderSelection TargetClaimsExchangeId="LocalAccountSigninEmailExchange" />
      </ClaimsProviderSelections>
    </OrchestrationStep>
    <OrchestrationStep Order="2" Type="ClaimsExchange">
      <ClaimsExchanges>
        <ClaimsExchange Id="FacebookExchange" TechnicalProfileReferenceId="Facebook-OAUTH" />
        <ClaimsExchange Id="LocalAccountSigninEmailExchange" TechnicalProfileReferenceId="SelfAsserted-LocalAccountSignin-Email" />
      </ClaimsExchanges>
    </OrchestrationStep>
    <OrchestrationStep Order="3" Type="ClaimsExchange">
      <Preconditions>
        <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
          <Value>authenticationSource</Value>
          <Value>localAccountAuthentication</Value>
          <Action>SkipThisOrchestrationStep</Action>
        </Precondition>
      </Preconditions>
      <ClaimsExchanges>
        <ClaimsExchange Id="AADUserRead" TechnicalProfileReferenceId="AAD-UserReadUsingAlternativeSecurityId" />
      </ClaimsExchanges>
    </OrchestrationStep>
    <OrchestrationStep Order="4" Type="ClaimsExchange">
      <Preconditions>
        <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
          <Value>authenticationSource</Value>
          <Value>socialIdpAuthentication</Value>
          <Action>SkipThisOrchestrationStep</Action>
        </Precondition>
      </Preconditions>
      <ClaimsExchanges>
        <ClaimsExchange Id="AADUserReadWithObjectId" TechnicalProfileReferenceId="AAD-UserReadUsingObjectId" />
      </ClaimsExchanges>
    </OrchestrationStep>
    <OrchestrationStep Order="5" Type="ClaimsExchange">
      <ClaimsExchanges>
        <ClaimsExchange Id="B2CUserProfileUpdateExchange" TechnicalProfileReferenceId="SelfAsserted-ProfileUpdate" />
      </ClaimsExchanges>
    </OrchestrationStep>
    <!-- Add a step 6 to the user journey before the JWT token is created-->
    <OrchestrationStep Order="6" Type="ClaimsExchange">
      <ClaimsExchanges>
        <ClaimsExchange Id="GetLoyaltyData" TechnicalProfileReferenceId="AzureFunctions-WebHook" />
      </ClaimsExchanges>
    </OrchestrationStep>
    <OrchestrationStep Order="7" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="JwtIssuer" />
  </OrchestrationSteps>
  <ClientDefinition ReferenceId="DefaultWeb" />
</UserJourney>
```

## <a name="add-the-claim"></a>Dodawanie żądania

Edytuj plik *ProfileEdit. XML* i Dodaj `<OutputClaim ClaimTypeReferenceId="city" />` do elementu **OutputClaims** .

Po dodaniu nowego żądania profil techniczny wygląda podobnie do tego przykładu:

```XML
<TechnicalProfile Id="PolicyProfile">
  <DisplayName>PolicyProfile</DisplayName>
  <Protocol Name="OpenIdConnect" />
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
    <OutputClaim ClaimTypeReferenceId="tenantId" AlwaysUseDefaultValue="true" DefaultValue="{Policy:TenantObjectId}" />
    <OutputClaim ClaimTypeReferenceId="city" />
  </OutputClaims>
  <SubjectNamingInfo ClaimType="sub" />
</TechnicalProfile>
```

## <a name="upload-your-changes-and-test"></a>Przekaż zmiany i przetestuj

1. Obowiązkowe Przed kontynuowaniem Zapisz istniejącą wersję (pobierając) plików.
2. Przekaż pliki *TrustFrameworkExtensions. XML* i *ProfileEdit. XML* i wybierz, aby zastąpić istniejący plik.
3. Wybierz **B2C_1A_ProfileEdit**.
4. Dla **opcji wybierz aplikację** na stronie Przegląd zasad niestandardowych wybierz aplikację sieci Web o nazwie *webapp1* , która została wcześniej zarejestrowana. Upewnij się, że **adres URL odpowiedzi** jest `https://jwt.ms`.
4. Wybierz pozycję **Uruchom teraz**. Zaloguj się przy użyciu poświadczeń konta, a następnie kliknij przycisk **Kontynuuj**.

Jeśli wszystko jest poprawnie skonfigurowane, token zawiera nowe `city`z `Redmond`wartością.

```JSON
{
  "exp": 1493053292,
  "nbf": 1493049692,
  "ver": "1.0",
  "iss": "https://contoso.b2clogin.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
  "sub": "a58e7c6c-7535-4074-93da-b0023fbaf3ac",
  "aud": "4e87c1dd-e5f5-4ac8-8368-bc6a98751b8b",
  "acr": "b2c_1a_profileedit",
  "nonce": "defaultNonce",
  "iat": 1493049692,
  "auth_time": 1493049692,
  "city": "Redmond"
}
```

## <a name="next-steps"></a>Następne kroki

Interakcję można także zaprojektować jako profil weryfikacji. Aby uzyskać więcej informacji, zobacz [Przewodnik: Integrowanie wymiany oświadczeń interfejsu API REST w trakcie Azure AD B2C użytkownika](custom-policy-rest-api-claims-validation.md).

[Zmodyfikuj edytowanie profilu w celu zebrania dodatkowych informacji od użytkowników](custom-policy-custom-attributes.md)

[Dokumentacja: profil techniczny RESTful](restful-technical-profile.md)

Aby dowiedzieć się, jak zabezpieczyć interfejsy API, zobacz następujące artykuły:

* [Zabezpieczanie interfejsu API usługi RESTful przy użyciu uwierzytelniania podstawowego (nazwa użytkownika i hasło)](secure-rest-api-dotnet-basic-auth.md)
* [Zabezpieczanie interfejsu API usługi RESTful przy użyciu certyfikatów klienta](secure-rest-api-dotnet-certificate-auth.md)
