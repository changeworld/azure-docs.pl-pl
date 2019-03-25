---
title: Śledzenie zachowania użytkowników przy użyciu zdarzeń w usłudze Application Insights z usługi Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Dowiedz się, jak włączyć dzienniki zdarzeń w usłudze Application Insights z podróży użytkownika usługi Azure AD B2C za pomocą zasad niestandardowych (wersja zapoznawcza).
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.date: 10/12/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 51342a7544295b465d1f2dfc3e1b27d8285b3588
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/25/2019
ms.locfileid: "58403820"
---
# <a name="track-user-behavior-in-azure-active-directory-b2c-using-application-insights"></a>Śledzenie zachowania użytkowników w usłudze Azure Active Directory B2C przy użyciu usługi Application Insights

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

Gdy używasz usługi Azure Active Directory (Azure AD) B2C wraz z usługi Azure Application Insights, można uzyskać szczegółowe i dostosować dzienniki zdarzeń dla swojej podróży użytkownika. W tym artykule omówiono sposób wykonywania następujących zadań:

* Uzyskiwanie szczegółowych informacji dotyczących zachowania użytkowników.
* Rozwiązywanie problemów z własnych zasad w trakcie opracowywania lub w środowisku produkcyjnym.
* Mierzenie wydajności.
* Tworzenie powiadomienia z usługi Application Insights.

## <a name="how-it-works"></a>Jak to działa

Struktura środowiska tożsamości w usłudze Azure AD B2C zawiera dostawcę `Handler="Web.TPEngine.Providers.AzureApplicationInsightsProvider, Web.TPEngine, Version=1.0.0.0`. Wysyła dane zdarzenia bezpośrednio do usługi Application Insights przy użyciu klucza Instrumentacji dostarczane do usługi Azure AD B2C.

Profil techniczny używa tego dostawcy, aby zdefiniować określone zdarzenie z usługi Azure AD B2C. Profil, który określa nazwę zdarzenia, oświadczenia, które są rejestrowane i klucz instrumentacji. Na potrzeby publikowania zdarzenia, profilu technicznego jest dodawane jako `orchestration step`, lub jako `validation technical profile` w podróży użytkownika niestandardowego.

Usługa Application Insights ujednolicić zdarzeń za pomocą identyfikator korelacji, aby zarejestrować sesję użytkownika. Usługa Application Insights udostępnia zdarzenia i sesji w ciągu kilku sekund i przedstawia wiele wizualizacji, eksportowanie i narzędzi analitycznych.

## <a name="prerequisites"></a>Wymagania wstępne

Wykonaj kroki [wprowadzenie do zasad niestandardowych](active-directory-b2c-get-started-custom.md). W tym artykule założono, że używasz pakiet startowy niestandardowe zasady. Ale pakiet startowy nie jest wymagane.

## <a name="create-an-application-insights-resource"></a>Tworzenie zasobu usługi Application Insights

Podczas korzystania z usługi Application Insights z usługą Azure AD B2C, jest wszystko, co należy zrobić, utwórz zasób i uzyskiwanie klucza instrumentacji.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
2. Pamiętaj, że używasz katalogu, który zawiera subskrypcję platformy Azure, klikając **filtr katalogów i subskrypcji** w górnym menu i wybierając katalog, który zawiera Twoją subskrypcję. Ta dzierżawa nie jest dzierżawą usługi Azure AD B2C.
3. Wybierz **Utwórz zasób** w lewym górnym rogu witryny Azure portal, a następnie wyszukaj i wybierz **usługi Application Insights**.
4. Kliknij pozycję **Utwórz**.
5. Wprowadź **nazwa** dla zasobu.
6. Aby uzyskać **typ aplikacji**, wybierz opcję **aplikacji sieci web ASP.NET**.
7. Aby uzyskać **grupy zasobów**, wybierz istniejącą grupę lub wprowadź nazwę nowej grupy.
8. Kliknij pozycję **Utwórz**.
4. Po utworzeniu zasobu usługi Application Insights, otwórz go, rozwiń węzeł **Essentials**, a następnie skopiuj klucz instrumentacji.

![Omówienie usługi Application Insights i klucz Instrumentacji](./media/active-directory-b2c-custom-guide-eventlogger-appins/app-insights.png)

## <a name="add-new-claimtype-definitions"></a>Dodawanie nowych definicji oświadczenia

Otwórz *TrustFrameworkExtensions.xml* pliku z pakietu startowego i dodaj następujące elementy do [BuildingBlocks](buildingblocks.md) elementu:

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

Profile techniczne mogą zostać uwzględnione funkcje w tożsamości środowisko Framework programu Azure AD B2C. Ta tabela określa profile techniczne, które są używane w celu otwórz sesję i publikowania zdarzeń.

| Profil techniczny | Zadanie |
| ----------------- | -----|
| AzureInsights-Common | Tworzy zestaw typowych parametrów, które mają zostać uwzględnione we wszystkich profilach techniczne AzureInsights. | 
| AzureInsights-SignInRequest | Tworzy zdarzenie logowania przy użyciu zestawu oświadczeń, gdy otrzymano żądanie logowania. | 
| AzureInsights-UserSignup | Tworzy zdarzenie UserSignup po użytkownik wyzwoleniu opcji rejestracji w podróży konta-dokonywania/logowania. | 
| AzureInsights-SignInComplete | Rejestruje pomyślnego ukończenia uwierzytelniania, gdy token zostało wysłane do aplikacji jednostki uzależnionej. | 

Dodawanie profilów do *TrustFrameworkExtensions.xml* plik z pakietu startowego. Dodaj te elementy, aby **ClaimsProviders** elementu:

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
        <InputClaim ClaimTypeReferenceId="CorrelationId" PartnerClaimType="{property:JourneyId}" />
        <InputClaim ClaimTypeReferenceId="Culture" PartnerClaimType="{property:Culture}" DefaultValue="{Culture:RFC5646}" />
      </InputClaims>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

> [!IMPORTANT]
> Zmień klucz Instrumentacji w `ApplicationInsights-Common` profilu technicznego identyfikator GUID, który zawiera zasób usługi Application Insights.

## <a name="add-the-technical-profiles-as-orchestration-steps"></a>Dodaj profile techniczne jako kroki aranżacji

Wywołaj `Azure-Insights-SignInRequest` jako aranżacji krok 2, aby śledzić, Odebrano żądanie logowania — w/rejestracją:

```xml
<!-- Track that we have received a sign in request -->
<OrchestrationStep Order="1" Type="ClaimsExchange">
  <ClaimsExchanges>
    <ClaimsExchange Id="TrackSignInRequest" TechnicalProfileReferenceId="AzureInsights-SignInRequest" />
  </ClaimsExchanges>
</OrchestrationStep>
```

Od razu *przed* `SendClaims` aranżacji kroku, Dodaj nowy krok, który wywołuje `Azure-Insights-UserSignup`. Jest on wyzwalany, gdy użytkownik wybierze przycisk rejestracji w podróży konta-dokonywania/Zaloguj się.

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

Natychmiast po `SendClaims` kroku aranżacji, wywołanie `Azure-Insights-SignInComplete`. W tym kroku przedstawiono zakończoną pomyślnie podróży.

```xml
<!-- Track that we have successfully sent a token -->
<OrchestrationStep Order="10" Type="ClaimsExchange">
  <ClaimsExchanges>
    <ClaimsExchange Id="TrackSignInComplete" TechnicalProfileReferenceId="AzureInsights-SignInComplete" />
  </ClaimsExchanges>
</OrchestrationStep>
```

> [!IMPORTANT]
> Po dodaniu nowych krokach aranżacji, numerowania kroki sekwencyjnie bez pomijania dowolnej liczby całkowite z zakresu od 1 do n


## <a name="upload-your-file-run-the-policy-and-view-events"></a>Przekazywanie pliku, uruchamianie zasad i wyświetlać zdarzenia

Zapisz i załaduj *TrustFrameworkExtensions.xml* pliku. Następnie wywołaj jednostki uzależnionej zasad firmy z aplikacji lub użyj **Uruchom teraz** w witrynie Azure portal. W ciągu kilku sekund zdarzenia są dostępne w usłudze Application Insights.

1. Otwórz **usługi Application Insights** zasobów w Twojej dzierżawie usługi Azure Active Directory.
2. Wybierz **użycia** > **zdarzenia**.
3. Ustaw **podczas** do **Ostatnia godzina** i **przez** do **więcej niż trzy minuty**.  Może być konieczne wybranie **Odśwież** Aby wyświetlić wyniki.

![Application Insights USAGE-Events Blase](./media/active-directory-b2c-custom-guide-eventlogger-appins/app-ins-graphic.png)

## <a name="next-steps"></a>Kolejne kroki

Dodaj do swojej podróży użytkownika odpowiednio do potrzeb typów oświadczeń i zdarzenia. Można użyć [oświadczenia rozwiązujący](claim-resolver-overview.md) lub dowolny ciąg, typ oświadczenia, dodawanie oświadczeń, dodając **oświadczeń przychodzących** element zdarzenia usługi Application Insights lub profil techniczny AzureInsights znane. 

- **ClaimTypeReferenceId** jest odwołanie do typu oświadczenia.
- **PartnerClaimType** jest nazwą właściwości, która pojawia się w usłudze Azure Insights. Należy użyć składni z `{property:NAME}`, gdzie `NAME` jest właściwością dodawany do zdarzenia. 
- **DefaultValue** dowolną wartość ciągu lub oświadczeń programu rozpoznawania nazw. 

```XML
<InputClaim ClaimTypeReferenceId="app_session" PartnerClaimType="{property:app_session}" DefaultValue="{OAUTH-KV:app_session}" />
<InputClaim ClaimTypeReferenceId="loyalty_number" PartnerClaimType="{property:loyalty_number}" DefaultValue="{OAUTH-KV:loyalty_number}" />
<InputClaim ClaimTypeReferenceId="language" PartnerClaimType="{property:language}" DefaultValue="{Culture:RFC5646}" />
```

