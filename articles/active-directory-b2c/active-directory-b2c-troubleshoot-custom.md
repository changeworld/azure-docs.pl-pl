---
title: Usługi Application Insights do Rozwiązywanie problemów dotyczących zasad niestandardowych w usłudze Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: jak skonfigurować usługę Application Insights do śledzenia realizacji zasady niestandardowe.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/04/2017
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: df5d710792d8c47e491f5b06d88f4050e8eb4a01
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66508070"
---
# <a name="azure-active-directory-b2c-collecting-logs"></a>Azure Active Directory B2C: Zbieranie dzienników

Ten artykuł zawiera instrukcje do zbierania dzienników z usługi Azure AD B2C, dzięki czemu można diagnozować problemy z zasadami dotyczącymi zasobów niestandardowych.

>[!NOTE]
>Obecnie dzienniki szczegółowe działania opisane w tym miejscu są przeznaczone **tylko** ułatwiające tworzenie niestandardowych zasad. Nie należy używać trybu projektowania w środowisku produkcyjnym.  Dzienniki zbierać wszystkie oświadczenia wysyłane do i z dostawcami tożsamości, podczas programowania.  Jeśli używane w środowisku produkcyjnym, deweloper przyjmuje odpowiedzialność za dane osobowe (prywatnie osobowe) zebrane w dzienniku usługi App Insights, w której jest właścicielem.  Te dzienniki są zbierane tylko, gdy zasady jest umieszczany na **trybu opracowywania**.


## <a name="use-application-insights"></a>Korzystanie z usługi Application Insights

Usługa Azure AD B2C obsługuje funkcję wysyłania danych do usługi Application Insights.  Usługa Application Insights umożliwia wizualizowanie problemy z wydajnością aplikacji i diagnozowanie wyjątków.

### <a name="setup-application-insights"></a>Konfigurowanie usługi Application Insights

1. Przejdź do witryny [Azure Portal](https://portal.azure.com). Upewnij się, że w ramach dzierżawy z subskrypcją platformy Azure (nie w dzierżawie usługi Azure AD B2C).
1. Kliknij przycisk **+ nowy** w menu nawigacji po lewej stronie.
1. Wyszukaj i wybierz pozycję **usługi Application Insights**, następnie kliknij przycisk **Utwórz**.
1. Wypełnij formularz, a następnie kliknij przycisk **Utwórz**. Wybierz **ogólne** dla **typ aplikacji**.
1. Po utworzeniu zasobu otwórz zasób usługi Application Insights.
1. Znajdź **właściwości** w menu po lewej stronie i kliknij go.
1. Kopiuj **klucz Instrumentacji** i zapisać go do następnej sekcji.

### <a name="set-up-the-custom-policy"></a>Konfigurowanie zasad niestandardowych

1. Otwórz plik RP (na przykład SignUpOrSignin.xml).
1. Dodaj następujące atrybuty do `<TrustFrameworkPolicy>` elementu:

   ```XML
   DeploymentMode="Development"
   UserJourneyRecorderEndpoint="urn:journeyrecorder:applicationinsights"
   ```

1. Jeśli nie istnieje już, Dodaj węzeł podrzędny `<UserJourneyBehaviors>` do `<RelyingParty>` węzła. Znajdować się natychmiast po `<DefaultUserJourney ReferenceId="UserJourney Id from your extensions policy, or equivalent (for example:SignUpOrSigninWithAAD" />`
2. Dodaj następujący węzeł jako element podrzędny elementu `<UserJourneyBehaviors>` elementu. Upewnij się zastąpić `{Your Application Insights Key}` z **klucz Instrumentacji** uzyskany z usługi Application Insights w poprzedniej sekcji.

   ```XML
   <JourneyInsights TelemetryEngine="ApplicationInsights" InstrumentationKey="{Your Application Insights Key}" DeveloperMode="true" ClientEnabled="false" ServerEnabled="true" TelemetryVersion="1.0.0" />
   ```

   * `DeveloperMode="true"` informuje dotycząca usługi Application Insights, aby przyspieszyć dane telemetryczne za pośrednictwem potoku przetwarzania dobre do tworzenia aplikacji, ale ograniczone w dużej ilości danych.
   * `ClientEnabled="true"` wysyła skryptu po stronie klienta dotycząca usługi Application Insights do śledzenia błędów po stronie klienta i widoku strony (nie jest wymagane).
   * `ServerEnabled="true"` wysyła istniejących JSON UserJourneyRecorder jako zdarzenie niestandardowe do usługi Application Insights.
   Przykład:

   ```XML
   <TrustFrameworkPolicy
    ...
    TenantId="fabrikamb2c.onmicrosoft.com"
    PolicyId="SignUpOrSignInWithAAD"
    DeploymentMode="Development"
    UserJourneyRecorderEndpoint="urn:journeyrecorder:applicationinsights"
   >
    ...
    <RelyingParty>
      <DefaultUserJourney ReferenceId="UserJourney ID from your extensions policy, or equivalent (for example: SignUpOrSigninWithAzureAD)" />
      <UserJourneyBehaviors>
        <JourneyInsights TelemetryEngine="ApplicationInsights" InstrumentationKey="{Your Application Insights Key}" DeveloperMode="true" ClientEnabled="false" ServerEnabled="true" TelemetryVersion="1.0.0" />
      </UserJourneyBehaviors>
      ...
   </TrustFrameworkPolicy>
   ```

3. Przekazywanie zasad.

### <a name="see-the-logs-in-application-insights"></a>Przeglądanie dzienników w usłudze Application Insights

>[!NOTE]
> Brak krótki opóźnienie (mniej niż pięć minut), zanim zobaczysz nowe dzienniki w usłudze Application Insights.

1. Otwórz zasób usługi Application Insights, który został utworzony w [witryny Azure portal](https://portal.azure.com).
1. W **Przegląd** menu, kliknij pozycję **analizy**.
1. Otwórz nową kartę w usłudze Application Insights.
1. Poniżej przedstawiono listę zapytań, których można użyć, aby wyświetlić dzienniki

| Zapytanie | Opis |
|---------------------|--------------------|
ślady | Zobacz wszystkie dzienniki generowane przez usługę Azure AD B2C |
ślady \| której sygnatura czasowa > ago(1d) | Zobacz wszystkie dzienniki generowane przez usługę Azure AD B2C w ciągu ostatniego dnia

Wpisy może być długi.  Eksportuj do pliku CSV, aby uzyskać bliższe spojrzenie.

Dowiedz się więcej o narzędziu Analytics [tutaj](https://docs.microsoft.com/azure/application-insights/app-insights-analytics).

>[!NOTE]
>Społeczność opracowała podglądu podróż użytkownika, aby pomóc deweloperom w tożsamości.  Nie jest obsługiwane przez firmę Microsoft i udostępniane wyłącznie jako-to.  Odczytuje z wystąpienia usługi Application Insights i udostępnia widok dobrze użytkownika podróż zdarzenia.  Możesz uzyskać kod źródłowy i wdrożyć ją w własnego rozwiązania.

Wersji przeglądarki, która odczytuje zdarzenia z usługi Application Insights znajduje się [tutaj](https://github.com/Azure-Samples/active-directory-b2c-advanced-policies/tree/master/wingtipgamesb2c/src/WingTipUserJourneyPlayerWebApplication)

>[!NOTE]
>Obecnie dzienniki szczegółowe działania opisane w tym miejscu są przeznaczone **tylko** ułatwiające tworzenie niestandardowych zasad. Nie należy używać trybu projektowania w środowisku produkcyjnym.  Dzienniki zbierać wszystkie oświadczenia wysyłane do i z dostawcami tożsamości, podczas programowania.  Jeśli używane w środowisku produkcyjnym, deweloper przyjmuje odpowiedzialność za dane osobowe (prywatnie osobowe) zebrane w dzienniku usługi App Insights, w której jest właścicielem.  Te dzienniki są zbierane tylko, gdy zasady jest umieszczany na **trybu opracowywania**.

[Repozytorium GitHub na potrzeby nieobsługiwanych Przykłady zasad niestandardowych oraz pokrewnych narzędzi](https://github.com/Azure-Samples/active-directory-b2c-advanced-policies)



## <a name="next-steps"></a>Następne kroki

Eksplorowanie danych w usłudze Application Insights, aby lepiej zrozumieć, jak napotyka struktura środowiska tożsamości podstawowej B2C działa w celu dostarczenia swojej tożsamości.
