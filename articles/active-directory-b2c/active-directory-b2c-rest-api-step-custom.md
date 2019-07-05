---
title: Interfejs API REST oświadczeń wymiany - Azure Active Directory B2C
description: Wymiana oświadczeń interfejsu API REST należy dodać do zasad niestandardowych w Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 0bdef508e12a3b11143149b330da73838b53f860
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2019
ms.locfileid: "67439011"
---
# <a name="add-rest-api-claims-exchanges-to-custom-policies-in-azure-active-directory-b2c"></a>Dodawanie interfejsu API REST oświadczeń wymiany do zasad niestandardowych w usłudze Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Możesz dodać interakcję z interfejsu API RESTful do Twojej [zasady niestandardowe](active-directory-b2c-overview-custom.md) w usłudze Azure Active Directory (Azure AD) B2C. W tym artykule przedstawiono sposób tworzenia podróży użytkownika usługi Azure AD B2C, który współdziała z usług RESTful.

Interakcja zawiera oświadczenia wymianę informacji między oświadczenia interfejsu API REST i usługi Azure AD B2C. Wymiana oświadczeń mają następującą charakterystykę:

- Może być zaprojektowane jako kroku aranżacji.
- Można wyzwolić akcję zewnętrznych. Na przykład zarejestrować zdarzenie w zewnętrznej bazie danych.
- Może służyć do pobierania wartości, a następnie zapisać ją w bazie danych użytkownika.
- Można zmienić przepływem wykonania.

Scenariusz, który jest reprezentowany w tym artykule zawiera następujące akcje:

1. Wyszukaj użytkownika w systemie zewnętrznym.
2. Uzyskaj miasta, w którym użytkownik jest zarejestrowany.
3. Ten atrybut należy powrócić do aplikacji jako oświadczenia.

## <a name="prerequisites"></a>Wymagania wstępne

- Wykonaj kroki [wprowadzenie do zasad niestandardowych](active-directory-b2c-get-started-custom.md).
- Punkt końcowy interfejsu API REST do interakcji z. To proste Azure działa jako przykład korzysta z artykułu. Aby utworzyć funkcję platformy Azure, zobacz [tworzenie pierwszej funkcji w witrynie Azure portal](../azure-functions/functions-create-first-azure-function.md).

## <a name="prepare-the-api"></a>Przygotowanie interfejsu API

W tej sekcji należy przygotować funkcji platformy Azure, aby otrzymać wartość `email`, a następnie zwracają wartość `city` mogą służyć za pomocą usługi Azure AD B2C jako oświadczenia.

Zmień pliku run.csx dla funkcji platformy Azure, utworzony w celu użyj następującego kodu:

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

## <a name="configure-the-claims-exchange"></a>Skonfiguruj oświadczenia w programie exchange

Profil techniczny zawiera konfigurację programu exchange oświadczenia.

Otwórz *TrustFrameworkExtensions.xml* pliku i Dodaj następujący kod **ClaimsProvider** — element XML wewnątrz **ClaimsProviders** elementu.

```XML
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="AzureFunctions-WebHook">
      <DisplayName>Azure Function Web Hook</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://myfunction.azurewebsites.net/api/HttpTrigger1?code=bAZ4lLy//ZHZxmncM8rI7AgjQsrMKmVXBpP0vd9smOzdXDDUIaLljA==</Item>
        <Item Key="AuthenticationType">None</Item>
        <Item Key="SendClaimsIn">Body</Item>
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

**InputClaims** element definiuje oświadczenia, które są wysyłane do usługi REST. W tym przykładzie wartość oświadczenia `givenName` są wysyłane do usługi REST jako oświadczenie `email`. **OutputClaims** element definiuje oświadczenia, które oczekują usługi REST.

## <a name="add-the-claim-definition"></a>Dodawanie definicji oświadczenia

Dodaj definicję dla `city` wewnątrz **BuildingBlocks** elementu. Możesz znaleźć tego elementu na początku pliku TrustFrameworkExtensions.xml.

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

## <a name="add-an-orchestration-step"></a>Dodawanie kroku aranżacji

Istnieje wiele przypadków użycia, w którym wywołania interfejsu API REST może służyć jako kroku aranżacji. Krok aranżacji może służyć jako aktualizację do systemu zewnętrznego po pomyślnym zakończeniu zadania, takie jak rejestracji po raz pierwszy lub aktualizacja profilu Aby zachować synchronizację informacji. W tym przypadku jest używany rozszerzyć informacje do aplikacji po edycji profilu.

Dodawanie kroku do podróży użytkownika edycji profilu. Po użytkownik jest uwierzytelniany (aranżacji: kroki 1 – 4 następujący kod XML), a użytkownik udostępnił informacje zaktualizowany profil (krok 5). Kopia profilu edycji kodu XML podróży użytkownika z *TrustFrameworkBase.xml* pliku do usługi *TrustFrameworkExtensions.xml* pliku wewnątrz **podróży użytkowników** elementu. Następnie wprowadzić zmiany w kroku 6.

```XML
<OrchestrationStep Order="6" Type="ClaimsExchange">
  <ClaimsExchanges>
    <ClaimsExchange Id="GetLoyaltyData" TechnicalProfileReferenceId="AzureFunctions-WebHook" />
  </ClaimsExchanges>
</OrchestrationStep>
```

Końcowe XML dla podróży użytkownika powinien wyglądać następująco:

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

## <a name="add-the-claim"></a>Dodaj oświadczenie

Edytuj *ProfileEdit.xml* pliku i Dodaj `<OutputClaim ClaimTypeReferenceId="city" />` do **OutputClaims** elementu.

Po dodaniu nowego oświadczenia, profilu technicznego wygląda następująco:

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

## <a name="upload-your-changes-and-test"></a>Przekazywanie zmian i testowanie

1. (Opcjonalne:) Zapisz (pobierając) istniejącą wersję plików przed kontynuowaniem.
2. Przekaż *TrustFrameworkExtensions.xml* i *ProfileEdit.xml* a następnie wybierz pozycję Tak, aby zastąpić istniejący plik.
3. Wybierz **B2C_1A_ProfileEdit**.
4. Aby uzyskać **wybierz aplikację** na stronie Przegląd zasad niestandardowych, wybierz aplikację sieci web o nazwie *webapp1* który został wcześniej zarejestrowany. Upewnij się, że **adres URL odpowiedzi** jest `https://jwt.ms`.
4. Wybierz **Uruchom teraz**. Zaloguj się przy użyciu poświadczeń konta, a następnie kliknij przycisk **Kontynuuj**.

Jeśli wszystko jest prawidłowo skonfigurowane, token zawiera nowe oświadczenie `city`, z wartością `Redmond`.

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

## <a name="next-steps"></a>Kolejne kroki

- Istnieje również możliwość projektowania interakcji jako profil sprawdzania poprawności. Aby uzyskać więcej informacji, zobacz [instruktażu: Integracja interfejsu API REST wymianą oświadczeń podróży użytkownika usługi Azure AD B2C jako sprawdzanie poprawności danych wejściowych użytkownika](active-directory-b2c-rest-api-validation-custom.md).
- [Modyfikowanie edytowania profilu do zbierania dodatkowych informacji od użytkowników](active-directory-b2c-create-custom-attributes-profile-edit-custom.md)
