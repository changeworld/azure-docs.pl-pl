---
title: Rozwiązywanie problemów z zasadami niestandardowymi za pomocą usługi Application Insights
titleSuffix: Azure AD B2C
description: Jak skonfigurować usługa Application Insights do śledzenia wykonywania zasad niestandardowych.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 403dbe6106cb7a1d277ba672112d2bc45dbc2987
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78186271"
---
# <a name="collect-azure-active-directory-b2c-logs-with-application-insights"></a>Zbieranie dzienników B2C usługi Azure Active Directory za pomocą aplikacji

Ten artykuł zawiera kroki do zbierania dzienników z usługi Active Directory B2C (Azure AD B2C), dzięki czemu można zdiagnozować problemy z zasadami niestandardowymi. Usługa Application Insights umożliwia diagnozowanie wyjątków i wizualizowanie problemów z wydajnością aplikacji. Usługa Azure AD B2C zawiera funkcję wysyłania danych do usługi Application Insights.

Szczegółowe dzienniki aktywności opisane w tym miejscu powinny być włączone **TYLKO** podczas opracowywania zasad niestandardowych.

> [!WARNING]
> Nie należy włączać trybu programowania w produkcji. Dzienniki zbierają wszystkie oświadczenia wysyłane do i z dostawców tożsamości. Użytkownik jako deweloper ponosi odpowiedzialność za wszelkie dane osobowe zebrane w dziennikach usługi Application Insights. Te szczegółowe dzienniki są zbierane tylko wtedy, gdy zasady są umieszczane w **trybie deweloperskim**.

## <a name="set-up-application-insights"></a>Konfigurowanie usługi Application Insights

Jeśli jeszcze go nie masz, utwórz wystąpienie usługi Application Insights w ramach subskrypcji.

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).
1. Wybierz filtr **subskrypcja katalogu +** w górnym menu, a następnie wybierz katalog zawierający subskrypcję platformy Azure (nie katalog usługi Azure AD B2C).
1. Wybierz **pozycję Utwórz zasób** w menu nawigacji po lewej stronie.
1. Wyszukaj i wybierz **pozycję Usługa Application Insights**, a następnie wybierz pozycję **Utwórz**.
1. Wypełnij formularz, wybierz **pozycję Przejrzyj + utwórz**, a następnie wybierz pozycję **Utwórz**.
1. Po zakończeniu wdrażania wybierz pozycję **Przejdź do zasobu**.
1. W obszarze **Konfiguruj** w menu Statystyka aplikacji wybierz polecenie **Właściwości**.
1. Zapisz **klucz instrumentacji** do użycia w późniejszym kroku.

## <a name="configure-the-custom-policy"></a>Konfigurowanie zasad niestandardowych

1. Otwórz plik jednostki uzależnionej (RP), na przykład *SignUpOrSignin.xml*.
1. Dodaj następujące atrybuty `<TrustFrameworkPolicy>` do elementu:

   ```XML
   DeploymentMode="Development"
   UserJourneyRecorderEndpoint="urn:journeyrecorder:applicationinsights"
   ```

1. Jeśli jeszcze nie istnieje, dodaj `<UserJourneyBehaviors>` węzeł podrzędny do węzła. `<RelyingParty>` Musi znajdować się `<DefaultUserJourney ReferenceId="UserJourney Id" from your extensions policy, or equivalent (for example:SignUpOrSigninWithAAD" />`natychmiast po .
1. Dodaj następujący węzeł jako element `<UserJourneyBehaviors>` podrzędny elementu. Pamiętaj, aby `{Your Application Insights Key}` zastąpić **klucz instrumentacji** usługi Application Insights, który został nagrany wcześniej.

    ```XML
    <JourneyInsights TelemetryEngine="ApplicationInsights" InstrumentationKey="{Your Application Insights Key}" DeveloperMode="true" ClientEnabled="false" ServerEnabled="true" TelemetryVersion="1.0.0" />
    ```

    * `DeveloperMode="true"`informuje ApplicationInsights, aby przyspieszyć dane telemetryczne za pośrednictwem potoku przetwarzania. Dobre dla rozwoju, ale ograniczone w dużych ilościach.
    * `ClientEnabled="true"`wysyła ApplicationInsights skrypt po stronie klienta do śledzenia widoku strony i błędów po stronie klienta. Można je wyświetlić w **tabeli przeglądarkiTimings** w portalu usługi Application Insights. Przez `ClientEnabled= "true"`ustawienie , dodać usługi Application Insights do skryptu strony i uzyskać chronometrażu wczytywanych stron i wywołań AJAX, liczy, szczegóły wyjątków przeglądarki i awarii AJAX i liczby użytkowników i sesji. To pole jest **opcjonalne** `false` i jest domyślnie ustawione na wartość.
    * `ServerEnabled="true"`wysyła istniejący UserJourneyRecorder JSON jako zdarzenie niestandardowe do usługi Application Insights.

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

1. Prześlij zasady.

## <a name="see-the-logs-in-application-insights"></a>Wyświetlanie dzienników w usłudze Application Insights

Istnieje krótkie opóźnienie, zazwyczaj mniej niż pięć minut, zanim będzie można zobaczyć nowe dzienniki w usłudze Application Insights.

1. Otwórz zasób usługi Application Insights utworzony w [witrynie Azure portal](https://portal.azure.com).
1. W menu **Przegląd** wybierz pozycję **Analytics**.
1. Otwórz nową kartę w usłudze Application Insights.

Oto lista zapytań, których można użyć, aby zobaczyć dzienniki:

| Zapytanie | Opis |
|---------------------|--------------------|
`traces` | Zobacz wszystkie dzienniki generowane przez usługę Azure AD B2C |
`traces | where timestamp > ago(1d)` | Zobacz wszystkie dzienniki generowane przez usługę Azure AD B2C dla ostatniego dnia

Wpisy mogą być długie. Eksportuj do pliku CSV, aby przyjrzeć się bliżej.

Aby uzyskać więcej informacji na temat wykonywania zapytań, zobacz [Omówienie zapytań dziennika w usłudze Azure Monitor](../azure-monitor/log-query/log-query-overview.md).

## <a name="next-steps"></a>Następne kroki

Społeczność opracowała przeglądarkę podróży użytkowników, aby pomóc deweloperom tożsamości. Odczytuje z wystąpienia usługi Application Insights i zapewnia dobrze ustrukturyzowany widok zdarzeń podróży użytkownika. Można uzyskać kod źródłowy i wdrożyć go we własnym rozwiązaniu.

Odtwarzacz podróży użytkownika nie jest obsługiwany przez firmę Microsoft i jest udostępniany w stanie rzeczywistym.

Wersję przeglądarki, która odczytuje zdarzenia z usługi Application Insights w usłudze GitHub, można znaleźć tutaj:

[Zasady zaawansowane azure-samples/active-directory-b2c](https://github.com/Azure-Samples/active-directory-b2c-advanced-policies/tree/master/wingtipgamesb2c/src/WingTipUserJourneyPlayerWebApplication)
