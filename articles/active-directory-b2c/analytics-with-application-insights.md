---
title: Śledzenie zachowania użytkowników za pomocą usługi Application Insights
titleSuffix: Azure AD B2C
description: Dowiedz się, jak włączyć dzienniki zdarzeń w usłudze Application Insights z podróży użytkowników usługi Azure AD B2C przy użyciu zasad niestandardowych.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.date: 04/05/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 25e62e7c6865f91daa242a33a0f491f8015be41a
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80672531"
---
# <a name="track-user-behavior-in-azure-active-directory-b2c-using-application-insights"></a>Śledzenie zachowania użytkowników w usłudze Azure Active Directory B2C przy użyciu usługi Application Insights

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

Usługa Azure Active Directory B2C (Azure AD B2C) obsługuje wysyłanie danych zdarzeń bezpośrednio do [usługi Application Insights](../azure-monitor/app/app-insights-overview.md) przy użyciu klucza instrumentacji dostarczonego do usługi Azure AD B2C.  Dzięki profilowi technicznemu usługi Application Insights można uzyskać szczegółowe i dostosowane dzienniki zdarzeń dla podróży użytkownika do:

* Uzyskaj szczegółowe informacje na temat zachowania użytkownika.
* Rozwiązywanie problemów z własnymi zasadami w programach programistów lub w produkcji.
* Zmierz wydajność.
* Tworzenie powiadomień z usługi Application Insights.

## <a name="how-it-works"></a>Jak to działa

Profil techniczny [usługi Application Insights](application-insights-technical-profile.md) definiuje zdarzenie z usługi Azure AD B2C. Profil określa nazwę zdarzenia, oświadczenia, które są rejestrowane i klucz instrumentacji. Aby opublikować zdarzenie, profil techniczny jest następnie dodawany jako krok aranżacji w [podróży użytkownika](userjourneys.md).

Usługa Application Insights można ujednolicić zdarzenia przy użyciu identyfikatora korelacji do rejestrowania sesji użytkownika. Usługa Application Insights udostępnia zdarzenie i sesję w ciągu kilku sekund i przedstawia wiele narzędzi do wizualizacji, eksportu i analizy.

## <a name="prerequisites"></a>Wymagania wstępne

Wykonaj kroki opisane w [wprowadzenie do zasad niestandardowych](custom-policy-get-started.md). Aby zarejestrować się i zalogować za pomocą kont lokalnych, powinny być dostępne robocze zasady niestandardowe.

## <a name="create-an-application-insights-resource"></a>Tworzenie zasobu usługi Application Insights

Podczas korzystania z usługi Application Insights z usługą Azure AD B2C, wszystko, co musisz zrobić, to utworzyć zasób i uzyskać klucz instrumentacji. Aby uzyskać informacje, zobacz [Tworzenie zasobu usługi Application Insights](../azure-monitor/app/create-new-resource.md)

1. Zaloguj się do [Portalu Azure](https://portal.azure.com/).
2. Upewnij się, że używasz katalogu, który zawiera subskrypcję platformy Azure, wybierając filtr **subskrypcja katalog +** w górnym menu i wybierając katalog zawierający subskrypcję. Ta dzierżawa nie jest dzierżawą usługi Azure AD B2C.
3. Wybierz **pozycję Utwórz zasób** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz pozycję **Application Insights**.
4. Kliknij przycisk **Utwórz**.
5. Wprowadź **nazwę** zasobu.
6. W przypadku **aplikacji Typ**wybierz ASP.NET **aplikację internetową**.
7. W przypadku **grupy zasobów**wybierz istniejącą grupę lub wprowadź nazwę nowej grupy.
8. Kliknij przycisk **Utwórz**.
4. Po utworzeniu zasobu usługi Application Insights otwórz go, rozwiń **pozycję Essentials**i skopiuj klucz instrumentacji.

![Przegląd aplikacji i klucz instrumentacji](./media/analytics-with-application-insights/app-insights.png)

## <a name="define-claims"></a>Definiowanie oświadczeń

Oświadczenie zapewnia tymczasowe przechowywanie danych podczas wykonywania zasad usługi Azure AD B2C. [Schemat oświadczeń](claimsschema.md) jest miejscem, w którym deklarujesz swoje oświadczenia.

1. Otwórz plik rozszerzeń zasad. Na przykład <em> `SocialAndLocalAccounts/` </em>.
1. Wyszukaj [element BuildingBlocks.](buildingblocks.md) Jeśli element nie istnieje, dodaj go.
1. Znajdź [ClaimsSchema](claimsschema.md) element. Jeśli element nie istnieje, dodaj go.
1. Dodaj następujące oświadczenia do **claimsschema** elementu. 

```xml
<ClaimType Id="EventType">
  <DisplayName>Event type</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="EventTimestamp">
  <DisplayName>Event timestamp</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="PolicyId">
  <DisplayName>Policy Id</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="Culture">
  <DisplayName>Culture ID</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="CorrelationId">
  <DisplayName>Correlation Id</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="federatedUser">
  <DisplayName>Federated user</DisplayName>
  <DataType>boolean</DataType>
</ClaimType>
<ClaimType Id="parsedDomain">
  <DisplayName>Domain name</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>The domain portion of the email address.</UserHelpText>
</ClaimType>
<ClaimType Id="userInLocalDirectory">
  <DisplayName>userInLocalDirectory</DisplayName>
  <DataType>boolean</DataType>
</ClaimType>
```

## <a name="add-new-technical-profiles"></a>Dodawanie nowych profili technicznych

Profile techniczne można uznać za funkcje w ramach środowiska tożsamości usługi Azure AD B2C. Ta tabela definiuje profile techniczne, które są używane do otwierania sesji i publikowania zdarzeń.

| Profil techniczny | Zadanie |
| ----------------- | -----|
| AppInsights-Wspólne | Wspólny zestaw parametrów, które mają zostać uwzględnione we wszystkich profilach technicznych usługi Azure Insights. |
| AppInsights-SignInRequest | Rejestruje `SignInRequest` zdarzenie z zestawem oświadczeń po odebraniu żądania logowania. |
| AppInsights-UserSignUp | Rejestruje `UserSignUp` zdarzenie, gdy użytkownik wyzwala opcję rejestracji w podróży rejestracji/logowania. |
| AppInsights-SignInComplete | Rejestruje `SignInComplete` zdarzenie po pomyślnym zakończeniu uwierzytelniania, gdy token został wysłany do aplikacji jednostki uzależniona. |

Dodaj profile do pliku *TrustFrameworkExtensions.xml* z pakietu startowego. Dodaj te elementy do **claimsproviders** elementu:

```xml
<ClaimsProvider>
  <DisplayName>Application Insights</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="AppInsights-Common">
      <DisplayName>Application Insights</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.Insights.AzureApplicationInsightsProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <!-- The ApplicationInsights instrumentation key which will be used for logging the events -->
        <Item Key="InstrumentationKey">xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx</Item>
        <Item Key="DeveloperMode">false</Item>
        <Item Key="DisableTelemetry ">false</Item>
      </Metadata>
      <InputClaims>
        <!-- Properties of an event are added through the syntax {property:NAME}, where NAME is property being added to the event. DefaultValue can be either a static value or a value that's resolved by one of the supported DefaultClaimResolvers. -->
        <InputClaim ClaimTypeReferenceId="EventTimestamp" PartnerClaimType="{property:EventTimestamp}" DefaultValue="{Context:DateTimeInUtc}" />
        <InputClaim ClaimTypeReferenceId="PolicyId" PartnerClaimType="{property:Policy}" DefaultValue="{Policy:PolicyId}" />
        <InputClaim ClaimTypeReferenceId="CorrelationId" PartnerClaimType="{property:CorrelationId}" DefaultValue="{Context:CorrelationId}" />
        <InputClaim ClaimTypeReferenceId="Culture" PartnerClaimType="{property:Culture}" DefaultValue="{Culture:RFC5646}" />
      </InputClaims>
    </TechnicalProfile>

    <TechnicalProfile Id="AppInsights-SignInRequest">
      <InputClaims>
        <!-- An input claim with a PartnerClaimType="eventName" is required. This is used by the AzureApplicationInsightsProvider to create an event with the specified value. -->
        <InputClaim ClaimTypeReferenceId="EventType" PartnerClaimType="eventName" DefaultValue="SignInRequest" />
      </InputClaims>
      <IncludeTechnicalProfile ReferenceId="AppInsights-Common" />
    </TechnicalProfile>

    <TechnicalProfile Id="AppInsights-UserSignUp">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="EventType" PartnerClaimType="eventName" DefaultValue="UserSignUp" />
      </InputClaims>
      <IncludeTechnicalProfile ReferenceId="AppInsights-Common" />
    </TechnicalProfile>
    
    <TechnicalProfile Id="AppInsights-SignInComplete">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="EventType" PartnerClaimType="eventName" DefaultValue="SignInComplete" />
        <InputClaim ClaimTypeReferenceId="federatedUser" PartnerClaimType="{property:FederatedUser}" DefaultValue="false" />
        <InputClaim ClaimTypeReferenceId="parsedDomain" PartnerClaimType="{property:FederationPartner}" DefaultValue="Not Applicable" />
      </InputClaims>
      <IncludeTechnicalProfile ReferenceId="AppInsights-Common" />
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

> [!IMPORTANT]
> Zmień klucz instrumentacji `AppInsights-Common` w profilu technicznym na identyfikator GUID, który zapewnia zasób usługi Application Insights.

## <a name="add-the-technical-profiles-as-orchestration-steps"></a>Dodawanie profili technicznych jako kroków aranżacji

Wywołanie `AppInsights-SignInRequest` jako krok aranżacji 2, aby śledzić, że odebrano żądanie logowania/rejestracji:

```xml
<!-- Track that we have received a sign in request -->
<OrchestrationStep Order="1" Type="ClaimsExchange">
  <ClaimsExchanges>
    <ClaimsExchange Id="TrackSignInRequest" TechnicalProfileReferenceId="AppInsights-SignInRequest" />
  </ClaimsExchanges>
</OrchestrationStep>
```

Bezpośrednio *before* przed `SendClaims` krokiem aranżacji dodaj `AppInsights-UserSignup`nowy krok, który wywołuje . Jest wyzwalany, gdy użytkownik wybierze przycisk rejestracji w podróży rejestracji/logowania.

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
    <ClaimsExchange Id="TrackUserSignUp" TechnicalProfileReferenceId="AppInsights-UserSignup" />
  </ClaimsExchanges>
</OrchestrationStep>
```

Natychmiast po `SendClaims` kroku aranżacji, wywołać `AppInsights-SignInComplete`. Ten krok pokazuje pomyślnie ukończoną podróż.

```xml
<!-- Track that we have successfully sent a token -->
<OrchestrationStep Order="10" Type="ClaimsExchange">
  <ClaimsExchanges>
    <ClaimsExchange Id="TrackSignInComplete" TechnicalProfileReferenceId="AppInsights-SignInComplete" />
  </ClaimsExchanges>
</OrchestrationStep>
```

> [!IMPORTANT]
> Po dodaniu nowych kroków aranżacji, ponownie numerować kroki sekwencyjnie bez pomijania żadnych liczby całkowite od 1 do N.


## <a name="upload-your-file-run-the-policy-and-view-events"></a>Przekazywanie pliku, uruchamianie zasad i wyświetlanie zdarzeń

Zapisz i przekaż plik *TrustFrameworkExtensions.xml.* Następnie należy wywołać zasady jednostki uzależniającej z aplikacji lub użyć **uruchom teraz** w witrynie Azure portal. W ciągu kilku sekund zdarzenia są dostępne w usłudze Application Insights.

1. Otwórz zasób **usługi Application Insights** w dzierżawie usługi Azure Active Directory.
2. Wybierz **pozycję Zdarzenia użycia** > **Events**.
3. Ustaw **w czasie** do ostatniej **godziny** i **przez** **3 minuty**.  Może być konieczne **wybranie opcji Odśwież,** aby wyświetlić wyniki.

![Application Insights USAGE-Events Blase](./media/analytics-with-application-insights/app-ins-graphic.png)

## <a name="optional-collect-more-data"></a>[Opcjonalnie] Zbieranie większej ilości danych

Dodaj typy oświadczeń i zdarzenia do podróży użytkownika, aby dopasować je do swoich potrzeb. Można użyć [rozpoznawania oświadczeń](claim-resolver-overview.md) lub dowolnego typu oświadczenia ciągu, dodać oświadczenia, dodając element Oświadczenie **wejściowe** do zdarzenia usługi Application Insights lub do profilu technicznego AppInsights-Common.

- **ClaimTypeReferenceId** jest odwołaniem do typu oświadczenia.
- **PartnerClaimType** to nazwa właściwości, która pojawia się w usłudze Azure Insights. Użyj składni `{property:NAME}`, gdzie `NAME` jest właściwość dodawana do zdarzenia.
- **DefaultValue** użyj dowolnej wartości ciągu lub rozpoznawania oświadczeń.

```XML
<InputClaim ClaimTypeReferenceId="app_session" PartnerClaimType="{property:app_session}" DefaultValue="{OAUTH-KV:app_session}" />
<InputClaim ClaimTypeReferenceId="loyalty_number" PartnerClaimType="{property:loyalty_number}" DefaultValue="{OAUTH-KV:loyalty_number}" />
<InputClaim ClaimTypeReferenceId="language" PartnerClaimType="{property:language}" DefaultValue="{Culture:RFC5646}" />
```

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o profilu technicznym [usługi Application Insights](application-insights-technical-profile.md) w odwołaniu do IEF. 
