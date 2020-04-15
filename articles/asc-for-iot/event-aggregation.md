---
title: Agregacja zdarzeń
description: Dowiedz się więcej o agregacji zdarzeń usługi Azure Security Center dla IoT.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: d40dac0b-abd0-4ff5-82eb-0f511ee13725
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/26/2019
ms.author: mlottner
ms.openlocfilehash: f72ef8cc5161bd6f885249e7d39344a57fa2368e
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81311415"
---
# <a name="azure-security-center-for-iot-event-aggregation"></a>Usługa Azure Security Center dla agregacji zdarzeń IoT

Usługa Azure Security Center dla agentów zabezpieczeń IoT zbiera dane i zdarzenia systemowe z urządzenia lokalnego i wysyła te dane do chmury platformy Azure w celu przetwarzania i analizy. Agent zabezpieczeń zbiera wiele typów zdarzeń urządzenia, w tym nowy proces i nowe zdarzenia połączenia. Zarówno nowy proces, jak i nowe zdarzenia połączenia mogą się legalnie występować często na urządzeniu w ciągu sekundy i chociaż jest to ważne dla niezawodnych i kompleksowych zabezpieczeń, liczba wiadomości, które agenci zabezpieczeń są zmuszeni wysyłać, może szybko osiągnąć lub przekroczyć limity przydziału i kosztów usługi IoT Hub. Jednak te zdarzenia zawierają bardzo cenne informacje zabezpieczające, które mają kluczowe znaczenie dla ochrony urządzenia.

Aby zmniejszyć dodatkowy przydział i koszty przy jednoczesnym zachowaniu ochrony urządzeń, Usługa Azure Security Center dla agentów IoT agreguje te typy zdarzeń.

Agregacja zdarzeń jest domyślnie **włączona** i chociaż nie jest to zalecane, można ręcznie **wyłączyć** w dowolnym momencie.

Agregacja jest obecnie dostępna dla następujących typów zdarzeń:

* ProcesTworzenie
* PołączenieTworzenie
* ProcessTerminate (tylko windows)

## <a name="how-does-event-aggregation-work"></a>Jak działa agregacja zdarzeń?

Gdy agregacja zdarzeń jest pozostawiona **w programie,** Usługa Azure Security Center dla agentów IoT agreguje zdarzenia dla okresu interwału lub przedziału czasu.
Po upływie okresu interwału agent wysyła zagregowane zdarzenia do chmury platformy Azure w celu dalszej analizy.
Zdarzenia zagregowane są przechowywane w pamięci do momentu wysłania do chmury platformy Azure.

Aby zmniejszyć zużycie pamięci agenta, gdy agent zbiera identyczne zdarzenie do zdarzenia, które jest już przechowywane w pamięci, agent zwiększa liczbę trafień tego konkretnego zdarzenia. Po upływie przedziału czasu agregacji agent wysyła liczbę trafień każdego określonego typu zdarzenia, które wystąpiło. Agregacja zdarzeń jest po prostu agregacją liczby trafień każdego zebranego typu zdarzenia.

Zdarzenia są uważane za identyczne tylko wtedy, gdy spełnione są następujące warunki:

* ProcessCreate events - gdy **commandLine**, **plik wykonywalny**, **nazwa użytkownika**i identyfikator **użytkownika** są identyczne
* ConnectionCreate events - gdy **polecenie Wiersz,** **identyfikator użytkownika,** **kierunek,** **adres lokalny,** **adres zdalny,****protokół i **port docelowy** są identyczne
* Zdarzenia ProcessTerminate — gdy stan **pliku wykonywalnego** i **zakończenia** jest identyczny

### <a name="working-with-aggregated-events"></a>Praca z zdarzeniami zagregowanymi

Podczas agregacji właściwości zdarzeń, które nie są agregowane są odrzucane i pojawiają się w analizie dziennika o wartości 0.

* ProcessCreate events - **processId**, and parentProcessId are set to 0 ProcessCreate events - processId , and parentProcessId are set to 0 ProcessCreate events — processId , and **parentProcessId** are set to 0 ProcessCre
* ConnectionCreate events - **processId**, a **port źródłowy** są ustawione na 0

## <a name="event-aggregation-based-alerts"></a>Alerty oparte na agregacji zdarzeń

Po analizie usługa Azure Security Center for IoT tworzy alerty zabezpieczeń dla podejrzanych zdarzeń zagregowanych. Alerty utworzone na podstawie zdarzeń zagregowanych są wyświetlane tylko raz dla każdego zdarzenia zagregowanego.

Czas rozpoczęcia agregacji, czas zakończenia i liczba trafień dla każdego zdarzenia są rejestrowane w polu **Event ExtraDetails** w usłudze Log Analytics do użycia podczas badań.

Każde zdarzenie zagregowane reprezentuje 24-godzinny okres zebranych alertów. Korzystając z menu opcji zdarzeń w lewym górnym rogu każdego zdarzenia, można **odrzucić** każde pojedyncze zdarzenie zagregowane.

## <a name="event-aggregation-twin-configuration"></a>Konfiguracja bliźniaczej reprezentacji agregacji zdarzeń

Wprowadzać zmiany w konfiguracji usługi Azure Security Center dla agregacji zdarzeń IoT wewnątrz [obiektu konfiguracji agenta](how-to-agent-configuration.md) tożsamości bliźniaczej reprezentacji modułu **modułu azureiotsecurity** modułu.

| Nazwa konfiguracji | Możliwe wartości | Szczegóły | Uwagi |
|:-----------|:---------------|:--------|:--------|
| agregacjaEnabledProcessTworzenie | wartość logiczna | Włączanie / wyłączanie agregacji zdarzeń dla zdarzeń tworzenia procesu |
| agregacjaIntervalProcessTworzenie | Ciąg timespan ISO8601 | Interwał agregacji dla zdarzeń tworzenia procesu |
| agregacjaEnabledConnectionTworzenie | wartość logiczna| Włączanie / wyłączanie agregacji zdarzeń dla zdarzeń tworzenia połączenia |
| agregacjaIntervalConnectionTworzenie | Ciąg timespan ISO8601 | Interwał agregacji dla zdarzeń tworzenia połączenia |
| agregacjaEnabledProcessTerminate | wartość logiczna | Włączanie / wyłączanie agregacji zdarzeń dla zdarzeń zakończenia procesu | Tylko Windows|
| agregacjaIntervalProcessTerminate | Ciąg timespan ISO8601 | Interwał agregacji dla zdarzeń zakończenia procesu | Tylko Windows|
|

## <a name="default-configurations-settings"></a>Ustawienia konfiguracji domyślnej

| Nazwa konfiguracji | Wartości domyślne |
|:-----------|:---------------|
| agregacjaEnabledProcessTworzenie | true |
| agregacjaIntervalProcessTworzenie | "PT1H"|
| agregacjaEnabledConnectionTworzenie | true |
| agregacjaIntervalConnectionTworzenie | "PT1H"|
| agregacjaEnabledProcessTerminate | true |
| agregacjaIntervalProcessTerminate | "PT1H"|
|

## <a name="next-steps"></a>Następne kroki

W tym artykule dowiesz się o usłudze Azure Security Center dla agregacji agentów zabezpieczeń IoT i dostępnych opcji konfiguracji zdarzeń.

Aby kontynuować pracę z usługą Azure Security Center dla wdrożenia IoT, należy użyć następujących artykułów:

- Poznaj [metody uwierzytelniania agenta zabezpieczeń](concept-security-agent-authentication-methods.md)
- Wybieranie i wdrażanie [agenta zabezpieczeń](how-to-deploy-agent.md)
- Sprawdź [wymagania wstępne usługi](service-prerequisites.md) IoT usługi Azure Security Center
- Dowiedz się, jak [włączyć usługę Azure Security Center dla usługi IoT w centrum IoT Hub](quickstart-onboard-iot-hub.md)
- Dowiedz się więcej o usłudze z [często zadawanych pytań dotyczących usługi Azure Security Center for IoT](resources-frequently-asked-questions.md)
