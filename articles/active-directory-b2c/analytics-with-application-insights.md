---
title: Śledzenie zachowania użytkowników przy użyciu Application Insights
titleSuffix: Azure AD B2C
description: Dowiedz się, jak włączyć dzienniki zdarzeń w Application Insights z Azure AD B2C użytkowników przy użyciu zasad niestandardowych.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.date: 02/11/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: f36b04113a753607b9242681cb62270e37bf7067
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/29/2020
ms.locfileid: "78190199"
---
# <a name="track-user-behavior-in-azure-active-directory-b2c-using-application-insights"></a>Śledzenie zachowania użytkowników w Azure Active Directory B2C przy użyciu Application Insights

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

W przypadku korzystania z Azure Active Directory B2C (Azure AD B2C) razem z usługą Azure Application Insights można uzyskać szczegółowe i dostosowane dzienniki zdarzeń dla podróży użytkownika. W tym artykule omówiono sposób wykonywania następujących zadań:

* Uzyskaj wgląd w zachowania użytkowników.
* Rozwiązywanie problemów z własnymi zasadami w programowaniu lub w środowisku produkcyjnym.
* Mierzenie wydajności.
* Utwórz powiadomienia z Application Insights.

## <a name="how-it-works"></a>Jak to działa

Platforma obsługi tożsamości w Azure AD B2C obejmuje `Handler="Web.TPEngine.Providers.AzureApplicationInsightsProvider, Web.TPEngine, Version=1.0.0.0`dostawcy. Dane zdarzenia są wysyłane bezpośrednio do Application Insights przy użyciu klucza Instrumentacji dostarczonego do Azure AD B2C.

Profil techniczny używa tego dostawcy do definiowania zdarzenia z Azure AD B2C. Profil określa nazwę zdarzenia, rejestrowane oświadczenia oraz klucz Instrumentacji. W celu opublikowania zdarzenia profil techniczny zostanie następnie dodany jako `orchestration step` w trakcie niestandardowej podróży użytkownika.

Application Insights może ujednolicić zdarzenia, używając identyfikatora korelacji do rejestrowania sesji użytkownika. Application Insights powoduje, że zdarzenie i sesja są dostępne w ciągu kilku sekund i prezentuje wiele narzędzi do wizualizacji, eksportowania i analitycznych.

## <a name="prerequisites"></a>Wymagania wstępne

Wykonaj kroki opisane w temacie Wprowadzenie [do zasad niestandardowych](custom-policy-get-started.md). W tym artykule przyjęto założenie, że używany jest pakiet startowy zasad niestandardowych. Ale pakiet początkowy nie jest wymagany.

## <a name="create-an-application-insights-resource"></a>Tworzenie zasobu usługi Application Insights

Jeśli używasz Application Insights z Azure AD B2C, wystarczy utworzyć zasób i uzyskać klucz Instrumentacji.

1. Zaloguj się do [Azure portal](https://portal.azure.com/).
2. Upewnij się, że używasz katalogu, który zawiera subskrypcję platformy Azure, wybierając filtr **katalog + subskrypcja** w górnym menu i wybierając katalog zawierający twoją subskrypcję. Ta dzierżawa nie jest dzierżawą Azure AD B2C.
3. Wybierz pozycję **Utwórz zasób** w lewym górnym rogu Azure Portal, a następnie wyszukaj i wybierz pozycję **Application Insights**.
4. Kliknij przycisk **Utwórz**.
5. Wprowadź **nazwę** zasobu.
6. W obszarze **Typ aplikacji**wybierz pozycję **aplikacja sieci Web ASP.NET**.
7. W obszarze **Grupa zasobów**wybierz istniejącą grupę lub wprowadź nazwę nowej grupy.
8. Kliknij przycisk **Utwórz**.
4. Po utworzeniu zasobu Application Insights Otwórz go, rozwiń węzeł **Essentials**i skopiuj klucz Instrumentacji.

![Przegląd Application Insights i klucz Instrumentacji](./media/analytics-with-application-insights/app-insights.png)

## <a name="add-new-claimtype-definitions"></a>Dodaj nowe definicje oświadczenia

Otwórz plik *TrustFrameworkExtensions. XML* z pakietu Starter Pack i Dodaj następujące elementy do elementu [BuildingBlocks](buildingblocks.md) :

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

## <a name="add-new-technical-profiles"></a>Dodaj nowe profile techniczne

Profile techniczne mogą być uznawane za funkcje w środowisku tożsamości Azure AD B2C. W tej tabeli opisano profile techniczne, które są używane do otwierania sesji i publikowania zdarzeń.

| Profil techniczny | Zadanie |
| ----------------- | -----|
| AzureInsights-Common | Tworzy wspólny zestaw parametrów do uwzględnienia we wszystkich profilach technicznych AzureInsights. |
| AzureInsights-SignInRequest | Tworzy zdarzenie logowania przy użyciu zestawu oświadczeń po odebraniu żądania logowania. |
| AzureInsights-UserSignup | Tworzy zdarzenie UserSignup, gdy użytkownik wyzwala opcję tworzenia konta w podróży/logowaniu. |
| AzureInsights-SignInComplete | Rejestruje pomyślne zakończenie uwierzytelniania po wysłaniu tokenu do aplikacji jednostki uzależnionej. |

Dodaj profile do pliku *TrustFrameworkExtensions. XML* z pakietu początkowego. Dodaj te elementy do elementu **ClaimsProviders** :

```xml
<ClaimsProvider>
  <DisplayName>Application Insights</DisplayName>
  <TechnicalProfiles>
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
        <InputClaim ClaimTypeReferenceId="CorrelationId" PartnerClaimType="{property:JourneyId}" DefaultValue="{Context:CorrelationId}" />
        <InputClaim ClaimTypeReferenceId="Culture" PartnerClaimType="{property:Culture}" DefaultValue="{Culture:RFC5646}" />
      </InputClaims>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

> [!IMPORTANT]
> Zmień klucz Instrumentacji w profilu technicznym `AzureInsights-Common` na identyfikator GUID, który zapewnia Application Insights zasób.

## <a name="add-the-technical-profiles-as-orchestration-steps"></a>Dodaj profile techniczne jako kroki aranżacji

Wywołaj `Azure-Insights-SignInRequest` jako aranżacja krok 2, aby śledzić, że Odebrano żądanie logowania/rejestracji:

```xml
<!-- Track that we have received a sign in request -->
<OrchestrationStep Order="1" Type="ClaimsExchange">
  <ClaimsExchanges>
    <ClaimsExchange Id="TrackSignInRequest" TechnicalProfileReferenceId="AzureInsights-SignInRequest" />
  </ClaimsExchanges>
</OrchestrationStep>
```

Bezpośrednio *przed* krokiem `SendClaims` aranżacji Dodaj nowy krok, który wywoła `Azure-Insights-UserSignup`. Jest wyzwalane, gdy użytkownik wybierze przycisk rejestracji w podróży/logowaniu.

```xml
<!-- Handles the user clicking the sign up link in the local account sign in page -->
<OrchestrationStep Order="8" Type="ClaimsExchange">
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
</OrchestrationStep>
```

Natychmiast po kroku aranżacji `SendClaims` Wywołaj `Azure-Insights-SignInComplete`. Ten krok przedstawia pomyślne zakończenie podróży.

```xml
<!-- Track that we have successfully sent a token -->
<OrchestrationStep Order="10" Type="ClaimsExchange">
  <ClaimsExchanges>
    <ClaimsExchange Id="TrackSignInComplete" TechnicalProfileReferenceId="AzureInsights-SignInComplete" />
  </ClaimsExchanges>
</OrchestrationStep>
```

> [!IMPORTANT]
> Po dodaniu nowych kroków aranżacji ponownie przystąpi kolejne kroki bez pomijania wartości całkowitych z zakresu od 1 do N.


## <a name="upload-your-file-run-the-policy-and-view-events"></a>Przekaż plik, uruchom zasady i Wyświetl zdarzenia

Zapisz i Przekaż plik *TrustFrameworkExtensions. XML* . Następnie należy wywołać zasady jednostki uzależnionej z poziomu aplikacji lub użyć **Uruchom teraz** w Azure Portal. W ciągu kilku sekund Twoje zdarzenia są dostępne w Application Insights.

1. Otwórz zasób **Application Insights** w dzierżawie Azure Active Directory.
2. Wybierz pozycję **zdarzenia** > **użycia** .
3. Ustawiaj w **ciągu** **ostatniej godziny** i **przez** maksymalnie **3 minuty**.  Może być konieczne wybranie opcji **Odśwież** , aby wyświetlić wyniki.

![Application Insights USAGE-Events Blase](./media/analytics-with-application-insights/app-ins-graphic.png)

## <a name="next-steps"></a>Następne kroki

Dodawanie typów i zdarzeń roszczeń do podróży użytkownika w celu dopasowania do Twoich potrzeb. Można użyć [resolverów oświadczeń](claim-resolver-overview.md) lub dowolnego typu oświadczenia ciągu, dodać oświadczenia poprzez dodanie elementu **oświadczenia wejściowego** do zdarzenia Application Insights lub do profilu technicznego AzureInsights-Common.

- **ClaimTypeReferenceId** jest odwołaniem do typu zgłoszenia.
- **PartnerClaimType** to nazwa właściwości, która pojawia się w usłudze Azure Insights. Użyj składni `{property:NAME}`, gdzie `NAME` jest dodawana właściwość do zdarzenia.
- **Właściwość DefaultValue** używa dowolnej wartości ciągu lub mechanizmu rozwiązywania konfliktów.

```XML
<InputClaim ClaimTypeReferenceId="app_session" PartnerClaimType="{property:app_session}" DefaultValue="{OAUTH-KV:app_session}" />
<InputClaim ClaimTypeReferenceId="loyalty_number" PartnerClaimType="{property:loyalty_number}" DefaultValue="{OAUTH-KV:loyalty_number}" />
<InputClaim ClaimTypeReferenceId="language" PartnerClaimType="{property:language}" DefaultValue="{Culture:RFC5646}" />
```

