---
title: Śledzenie zachowania użytkowników przy użyciu zdarzeń w usłudze Application Insights z usługi Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Przewodnik krok po kroku, aby włączyć dzienniki zdarzeń w usłudze Application Insights z podróży użytkownika usługi Azure AD B2C za pomocą zasad niestandardowych (wersja zapoznawcza)
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.date: 04/16/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 300a8a15552fe1e8ec9d6b434a14a31b3d827f19
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/03/2018
ms.locfileid: "37445589"
---
# <a name="track-user-behavior-in-azure-ad-b2c-journeys-by-using-application-insights"></a>Śledzenie zachowania użytkowników w usłudze Azure AD B2C podróży za pomocą usługi Application Insights

Usługa Azure Active Directory B2C (Azure AD B2C) działa dobrze z usługi Azure Application Insights. Zapewniają one dzienniki zdarzeń szczegółowa i dostosowane do Twojej podróży,-, utworzonym przez użytkownika. W tym artykule pokazano, jak rozpocząć pracę, dzięki czemu możesz:

* Uzyskiwanie szczegółowych informacji dotyczących zachowania użytkowników.
* Rozwiązywanie problemów z własnych zasad w trakcie opracowywania lub w środowisku produkcyjnym.
* Mierzenie wydajności.
* Tworzenie powiadomienia z usługi Application Insights.

> [!NOTE]
> Ta funkcja jest dostępna w wersji zapoznawczej.

## <a name="how-it-works"></a>Jak to działa

Struktura środowiska tożsamości w usłudze Azure AD B2C zawiera teraz dostawcę `Handler="Web.TPEngine.Providers.UserJourneyContextProvider, Web.TPEngine, Version=1.0.0.0`.  Wysyła dane zdarzenia bezpośrednio do usługi Application Insights przy użyciu klucza Instrumentacji dostarczane do usługi Azure AD B2C.

Profil techniczny używa tego dostawcy, aby zdefiniować określone zdarzenie z usługi B2C.  Profil, który określa nazwę zdarzenia, oświadczenia, które mają być rejestrowane i klucz instrumentacji.  Na potrzeby publikowania zdarzenia, profilu technicznego jest dodawane jako `orchestration step` lub jako `validation technical profile` w podróży użytkownika niestandardowego.

Usługa Application Insights ujednolicić zdarzeń za pomocą identyfikator korelacji, aby zarejestrować sesję użytkownika. Usługa Application Insights udostępnia zdarzenia i sesji w ciągu kilku sekund i przedstawia wiele wizualizacji, eksportowanie i narzędzi analitycznych.

## <a name="prerequisites"></a>Wymagania wstępne

Wykonaj kroki [wprowadzenie do zasad niestandardowych](active-directory-b2c-get-started-custom.md). W tym artykule założono, że używasz pakiet startowy niestandardowe zasady. Ale pakiet startowy nie jest wymagane.

## <a name="step-1-create-an-application-insights-resource-and-get-the-instrumentation-key"></a>Krok 1. Tworzenie zasobu usługi Application Insights i uzyskiwanie klucza Instrumentacji

Podczas korzystania z usługi Application Insights z usługą Azure AD B2C, jedynym wymaganiem jest, aby utworzyć zasób i uzyskiwanie klucza instrumentacji. Utwórz zasób w [witryny Azure portal.](https://portal.azure.com)

1. W witrynie Azure portal w ramach subskrypcji dzierżawy, wybierz **+ Utwórz zasób**. Ta dzierżawa nie jest dzierżawą usługi Azure AD B2C.  
2. Wyszukaj i wybierz pozycję **usługi Application Insights**.  
3. Tworzenie zasobu, który używa **aplikacji sieci web ASP.NET** jako **typ aplikacji**, w ramach subskrypcji z preferencjami.
4. Po utworzeniu zasobu usługi Application Insights, otwórz go, a następnie zanotuj klucz instrumentacji.

![Omówienie usługi Application Insights i klucz Instrumentacji](./media/active-directory-b2c-custom-guide-eventlogger-appins/app-ins-key.png)

## <a name="step-2-add-new-claimtype-definitions-to-your-trust-framework-extension-file"></a>Krok 2. Dodaj nowych definicji oświadczenia do pliku rozszerzenie framework zaufania

Otwórz plik rozszerzenia z pakietu startowego i dodaj następujące elementy do `<BuildingBlocks>` węzła. Nazwa pliku jest zwykle `yourtenant.onmicrosoft.com-B2C_1A_TrustFrameworkExtensions.xml`

```xml
<ClaimsSchema>
  <ClaimType Id="EventType">
    <DisplayName>EventType</DisplayName>
    <DataType>string</DataType>
    <AdminHelpText />
    <UserHelpText />
  </ClaimType>
  <ClaimType Id="PolicyId">
    <DisplayName>PolicyId</DisplayName>
    <DataType>string</DataType>
    <AdminHelpText />
    <UserHelpText />
  </ClaimType>
  <ClaimType Id="Culture">
    <DisplayName>Culture</DisplayName>
    <DataType>string</DataType>
    <AdminHelpText />
    <UserHelpText />
  </ClaimType>
  <ClaimType Id="CorrelationId">
    <DisplayName>CorrelationId</DisplayName>
    <DataType>string</DataType>
    <AdminHelpText />
    <UserHelpText />
  </ClaimType>
  <!--Additional claims used for passing claims to Application Insights Provider -->
  <ClaimType Id="federatedUser">
    <DisplayName>federatedUser</DisplayName>
    <DataType>boolean</DataType>
    <UserHelpText />
  </ClaimType>
  <ClaimType Id="parsedDomain">
    <DisplayName>Parsed Domain</DisplayName>
    <DataType>string</DataType>
    <UserHelpText>The domain portion of the email address.</UserHelpText>
  </ClaimType>
  <ClaimType Id="userInLocalDirectory">
    <DisplayName>userInLocalDirectory</DisplayName>
    <DataType>boolean</DataType>
    <UserHelpText />
  </ClaimType>
</ClaimsSchema>
```

## <a name="step-3-add-new-technical-profiles-that-use-the-application-insights-provider"></a>Krok 3. Dodaj nowe profile techniczne, korzystających z dostawcy usługi Application Insights

Profile techniczne mogą zostać uwzględnione funkcje w tożsamości środowisko Framework programu Azure AD B2C. W tym przykładzie definiuje pięć profile techniczne w celu otwórz sesję i publikowania zdarzeń:

| Profil techniczny | Zadanie |
| ----------------- | -----|
| AzureInsights-Common | Tworzy zestaw typowych parametrów, które mają zostać uwzględnione we wszystkich profilach techniczne AzureInsights | 
| JourneyContextForInsights | Zostanie otwarta sesja w usłudze Application Insights, a następnie wysyła identyfikator korelacji |
| AzureInsights-SignInRequest | Tworzy `SignIn` zdarzenie z zestaw oświadczeń, gdy otrzymano żądanie logowania | 
| AzureInsights-UserSignup | Tworzy zdarzenie UserSignup po użytkownik wyzwoleniu opcji rejestracji w podróży konta-dokonywania/logowania | 
| AzureInsights-SignInComplete | Rejestruje pomyślnego ukończenia uwierzytelniania, gdy token zostało wysłane do aplikacji jednostki uzależnionej | 

Dodawanie profilów do rozszerzenie pliku z pakietu startowego, dodając te elementy, aby `<ClaimsProviders>` węzła.  Nazwa pliku jest zwykle `yourtenant.onmicrosoft.com-B2C_1A_TrustFrameworkExtensions.xml`

> [!IMPORTANT]
> Zmień klucz Instrumentacji w `ApplicationInsights-Common` profilu technicznego identyfikator GUID, który zawiera zasób usługi Application Insights.

```xml
<ClaimsProvider>
  <DisplayName>Application Insights</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="JourneyContextForInsights">
      <DisplayName>Application Insights</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.UserJourneyContextProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="CorrelationId" />
      </OutputClaims>
    </TechnicalProfile>
    <TechnicalProfile Id="AzureInsights-SignInRequest">
      <InputClaims>
        <!-- An input claim with a PartnerClaimType="eventName" is required. This is used by the AzureApplicationInsightsProvider to create an event with the specified value. -->
        <InputClaim ClaimTypeReferenceId="EventType" PartnerClaimType="eventName" DefaultValue="SignInRequest" />
      </InputClaims>
      <IncludeTechnicalProfile ReferenceId="AzureInsights-Common" />
    </TechnicalProfile>
    <TechnicalProfile Id="AzureInsights-SignInComplete">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="EventType" PartnerClaimType="eventName" DefaultValue="SignInComplete" />
        <InputClaim ClaimTypeReferenceId="federatedUser" PartnerClaimType="{property:FederatedUser}" DefaultValue="false" />
        <InputClaim ClaimTypeReferenceId="parsedDomain" PartnerClaimType="{property:FederationPartner}" DefaultValue="Not Applicable" />
      </InputClaims>
      <IncludeTechnicalProfile ReferenceId="AzureInsights-Common" />
    </TechnicalProfile>
    <TechnicalProfile Id="AzureInsights-UserSignup">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="EventType" PartnerClaimType="eventName" DefaultValue="UserSignup" />
      </InputClaims>
      <IncludeTechnicalProfile ReferenceId="AzureInsights-Common" />
    </TechnicalProfile>
    <TechnicalProfile Id="AzureInsights-Common">
      <DisplayName>Alternate Email</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.Insights.AzureApplicationInsightsProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <!-- The ApplicationInsights instrumentation key which will be used for logging the events -->
        <Item Key="InstrumentationKey">xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx</Item>
        <!-- A Boolean that indicates whether developer mode is enabled. This controls how events are buffered. In a development environment with minimal event volume, enabling developer mode results in events being sent immediately to ApplicationInsights. -->
        <Item Key="DeveloperMode">false</Item>
        <!-- A Boolean that indicates whether telemetry should be enabled or not. -->
        <Item Key="DisableTelemetry ">false</Item>
      </Metadata>
      <InputClaims>
        <!-- Properties of an event are added through the syntax {property:NAME}, where NAME is property being added to the event. DefaultValue can be either a static value or a value that's resolved by one of the supported DefaultClaimResolvers. -->
        <InputClaim ClaimTypeReferenceId="PolicyId" PartnerClaimType="{property:Policy}" DefaultValue="{Policy:PolicyId}" />
        <InputClaim ClaimTypeReferenceId="CorrelationId" PartnerClaimType="{property:JourneyId}" />
        <InputClaim ClaimTypeReferenceId="Culture" PartnerClaimType="{property:Culture}" DefaultValue="{Culture:RFC5646}" />
      </InputClaims>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="step-4-add-the-technical-profiles-for-application-insights-as-orchestration-steps-in-an-existing-user-journey"></a>Krok 4. Dodaj profile techniczne dla usługi Application Insights, zgodnie z aranżacji kroki w istniejących podróży użytkownika

Wywołaj `JournyeContextForInsights` aranżacji w kroku 1:

```xml
<!-- Initialize a session with Application Insights -->
<OrchestrationStep Order="1" Type="ClaimsExchange">
  <ClaimsExchanges>
    <ClaimsExchange Id="JourneyContextForInsights" TechnicalProfileReferenceId="JourneyContextForInsights" />
  </ClaimsExchanges>
</OrchestrationStep>
```

Wywołaj `Azure-Insights-SignInRequest` jako aranżacji krok 2, aby śledzić, Odebrano żądanie logowania — w/rejestracją:

```xml
<!-- Track that we have received a sign in request -->
<OrchestrationStep Order="2" Type="ClaimsExchange">
  <ClaimsExchanges>
    <ClaimsExchange Id="TrackSignInRequest" TechnicalProfileReferenceId="AzureInsights-SignInRequest" />
  </ClaimsExchanges>
</OrchestrationStep>
```

Od razu *przed* `SendClaims` aranżacji kroku, Dodaj nowy krok, który wywołuje `Azure-Insights-UserSignup`. Jest on wyzwalany, gdy użytkownik wybierze przycisk rejestracji w podróży konta-dokonywania/Zaloguj się.

```xml
<!-- Handles the user clicking the sign up link in the local account sign in page -->
<OrchestrationStep Order="9" Type="ClaimsExchange">
  <Preconditions>
    <Precondition Type="ClaimsExist" ExecuteActionsIf="false">
      <Value>newUser</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
    <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
      <Value>newUser</Value>
      <Value>false</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
  </Preconditions>
  <ClaimsExchanges>
    <ClaimsExchange Id="TrackUserSignUp" TechnicalProfileReferenceId="AzureInsights-UserSignup" />
  </ClaimsExchanges>
```

Natychmiast po `SendClaims` kroku aranżacji, wywołanie `Azure-Insights-SignInComplete`. W tym kroku odzwierciedla podróż pomyślnie ukończone.

```xml
<!-- Track that we have successfully sent a token -->
<OrchestrationStep Order="11" Type="ClaimsExchange">
  <ClaimsExchanges>
    <ClaimsExchange Id="TrackSignInComplete" TechnicalProfileReferenceId="AzureInsights-SignInComplete" />
  </ClaimsExchanges>
</OrchestrationStep>
```

> [!IMPORTANT]
> Po dodaniu nowych krokach aranżacji, numerowania kroki sekwencyjnie bez pomijania dowolnej liczby całkowite z zakresu od 1 do n


## <a name="step-5-upload-your-modified-extensions-file-run-the-policy-and-view-events-in-application-insights"></a>Krok 5. Przekazywanie pliku zmodyfikowanego rozszerzenia, uruchamianie zasad i wyświetlać zdarzenia w usłudze Application Insights

Zapisz i Przekaż nowy plik rozszerzenia framework zaufania. Następnie wywołaj jednostki uzależnionej zasad firmy z aplikacji lub użyj `Run Now` w interfejsie usługi Azure AD B2C. W ciągu kilku sekund zdarzenia są dostępne w usłudze Application Insights.

1. Otwórz **usługi Application Insights** zasobów w Twojej dzierżawie usługi Azure Active Directory.
2. Wybierz **użycia** > **zdarzenia**.
3. Ustaw **podczas** do **Ostatnia godzina** i **przez** do **więcej niż trzy minuty**.  Może być konieczne wybranie **Odśwież** Aby wyświetlić wyniki.

![Application Insights USAGE-Events Blase](./media/active-directory-b2c-custom-guide-eventlogger-appins/app-ins-graphic.png)

##  <a name="next-steps"></a>Kolejne kroki

Dodaj do swojej podróży użytkownika odpowiednio do potrzeb typów oświadczeń i zdarzenia. Oto lista możliwych oświadczeń, używanie elementów rozpoznawania dodatkowych oświadczeń

### <a name="culture-specific-claims"></a>Oświadczenia właściwe dla kultury

```xml
Referenced using: {Culture:One of the property names below}
```

| Claim | Definicja | Przykład |
| ----- | -----------| --------|
| NazwaJęzyka | Dwuliterowa kod ISO dla języka | pl |
| RegionName | Dwuliterowa kod ISO dla regionu | USA |
| RFC5646 | Kod języka RFC5646 | pl-PL |
| LCID   | Identyfikator LCID kod języka | 21 |

### <a name="policy-specific-claims"></a>Oświadczenia specyficznych dla zasad

```xml
Referenced using {Policy:One of the property names below}
```

| Claim | Definicja | Przykład |
| ----- | -----------| --------|
| TrustFrameworkTenantId | Identyfikator dzierżawy trustframework | ND |
| RelyingPartyTenantId | Identyfikator dzierżawy uzależnionej | ND |
| PolicyId | Identyfikator zasad zasad | ND |
| TenantObjectId | Identyfikator obiektu dzierżawy zasad | ND |

### <a name="openid-connect-specific-claims"></a>Oświadczenia specyficzne dla protokołu OpenID Connect

```xml
Referenced using {OIDC:One of the property names below}
```

| Claim | Parametr OpenIdConnect | Przykład |
| ----- | ----------------------- | --------|
| wiersz | wiersz | ND |
| LoginHint |  login_hint | ND |
| DomainHint | Element domain_hint | ND |
|  MaxAge | max_age | ND |
| ClientId | client_id | ND |
| Nazwa użytkownika | login_hint | ND |
|  Zasób | zasób| ND |
| AuthenticationContextReferences | acr_values | ND |

### <a name="non-protocol-parameters-included-with-oidc--oauth2-requests"></a>Parametry protokołu bez dołączonej OIDC & OAuth2 żądań

```xml
Referenced using { OAUTH-KV:Querystring parameter name }
```

Nazwa parametru dołączone jako część żądania OIDC lub OAuth2 mogą zostać zmapowane do oświadczeń w podróży użytkownika. Można następnie zapisz go w zdarzeniu. Na przykład żądania od aplikacji może zawierać parametr ciągu zapytania o nazwie `app_session`, `loyalty_number` lub `any_string`.

Oto przykładowe żądanie od aplikacji:

```
https://login.microsoftonline.com/sampletenant.onmicrosoft.com/oauth2/v2.0/authorize?p=B2C_1A_signup_signin&client_id=e1d2612f-c2bc-4599-8e7b-d874eaca1ae1&nonce=defaultNonce&redirect_uri=https%3A%2F%2Fjwt.ms&scope=openid&response_type=id_token&prompt=login&app_session=0a2b45c&loyalty_number=1234567

```
Następnie można dodać oświadczenia, dodając `Input Claim` element zdarzenia usługi Application Insights. Właściwości zdarzenia są dodawane przy użyciu składni {właściwości: nazwa}, którego nazwa jest właściwość dodawany do zdarzenia. Na przykład:

```
<InputClaim ClaimTypeReferenceId="app_session" PartnerClaimType="{property:app_session}" DefaultValue="{OAUTH-KV:app_session}" />
<InputClaim ClaimTypeReferenceId="loyalty_number" PartnerClaimType="{property:loyalty_number}" DefaultValue="{OAUTH-KV:loyalty_number}" />
```

### <a name="other-system-claims"></a>Inne oświadczenia systemu

Niektóre oświadczenia systemu należy dodać do zbioru oświadczeń, zanim staną się dostępne dla rejestrowania jako zdarzenia. Profil techniczny `SimpleUJContext` musi zostać wywołana jako kroku aranżacji lub profil techniczny sprawdzania poprawności, zanim te oświadczenia są dostępne.

```xml
<ClaimsProvider>
  <DisplayName>User Journey Context Provider</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="SimpleUJContext">
      <DisplayName>User Journey Context Provide</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.UserJourneyContextProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="IP-Address" />
        <OutputClaim ClaimTypeReferenceId="CorrelationId" />
        <OutputClaim ClaimTypeReferenceId="DateTimeInUtc" />
        <OutputClaim ClaimTypeReferenceId="Build" />
      </OutputClaims>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```


