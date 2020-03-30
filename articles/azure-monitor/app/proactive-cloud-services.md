---
title: Alert o problemach w usługach w chmurze platformy Azure przy użyciu integracji diagnostyki platformy Azure z usługą Azure Application Insights | Dokumenty firmy Microsoft
description: Monitoruj problemy, takie jak błędy uruchamiania, awarie i pętle recyklingu ról w usługach w chmurze azure za pomocą usługi Azure Application Insights
ms.topic: conceptual
ms.date: 06/07/2018
ms.reviewer: harelbr
ms.openlocfilehash: 997c5e063c4181a597520e60e2a7669401b9677d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77669747"
---
# <a name="alert-on-issues-in-azure-cloud-services-using-the-azure-diagnostics-integration-with-azure-application-insights"></a>Alert o problemach w usługach w chmurze platformy Azure przy użyciu integracji diagnostyki platformy Azure z usługą Azure Application Insights

W tym artykule opisano, jak skonfigurować reguły alertów, które monitorują problemy, takie jak błędy uruchamiania, awarie i pętle odtwarzania ról w usłudze Azure Cloud Services (role sieci web i procesu roboczego).

Metoda opisana w tym artykule jest oparta na [integracji diagnostyki platformy Azure z usługą Application Insights](https://azure.microsoft.com/blog/azure-diagnostics-integration-with-application-insights/)i niedawno [wydanych alertów dziennika dla usługi Application Insights.](https://azure.microsoft.com/blog/log-alerts-for-application-insights-preview/)

## <a name="define-a-base-query"></a>Definiowanie kwerendy podstawowej

Aby rozpocząć, zdefiniujemy kwerendę podstawową, która pobiera zdarzenia dziennika zdarzeń systemu Windows z kanału systemu Windows Azure, które są przechwytywane w usłudze Application Insights jako rekordy śledzenia.
Te rekordy mogą służyć do wykrywania różnych problemów w usługach Azure Cloud Services, takich jak błędy uruchamiania, awarie środowiska uruchomieniowego i pętli recyklingu.

> [!NOTE]
> Zapytanie podstawowe poniżej sprawdza problemy w oknie czasu 30 minut i zakłada 10 minut opóźnienia w pozyskiwania rekordów telemetrycznych. Te wartości domyślne można skonfigurować według własnego uznania.

```
let window = 30m;
let endTime = ago(10m);
let EventLogs = traces
| where timestamp > endTime - window and timestamp < endTime
| extend channel = tostring(customDimensions.Channel), eventId = tostring(customDimensions.EventId)
| where channel == 'Windows Azure' and isnotempty(eventId)
| where tostring(customDimensions.DeploymentName) !contains 'deployment' // discard records captured from local machines
| project timestamp, channel, eventId, message, cloud_RoleInstance, cloud_RoleName, itemCount;
```

## <a name="check-for-specific-event-ids"></a>Sprawdzanie określonych identyfikatorów zdarzeń

Po pobraniu zdarzeń dziennika zdarzeń systemu Windows można wykryć określone problemy, sprawdzając ich identyfikator zdarzenia i właściwości wiadomości (zobacz przykłady poniżej).
Wystarczy połączyć zapytanie podstawowe powyżej z jednym z poniższych zapytań i użyć tej połączonej kwerendy podczas definiowania reguły alertu dziennika.

> [!NOTE]
> W poniższych przykładach problem zostanie wykryty, jeśli podczas analizowanego przedziału czasu zostanie znalezionych więcej niż trzy zdarzenia. Tę wartość domyślną można skonfigurować tak, aby zmieniała czułość reguły alertu.

```
// Detect failures in the OnStart method
EventLogs
| where eventId == '2001'
| where message contains '.OnStart()'
| summarize Failures = sum(itemCount) by cloud_RoleInstance, cloud_RoleName
| where Failures > 3
```

```
// Detect failures during runtime
EventLogs
| where eventId == '2001'
| where message contains '.Run()'
| summarize Failures = sum(itemCount) by cloud_RoleInstance, cloud_RoleName
| where Failures > 3
```

```
// Detect failures when running a startup task
EventLogs
| where eventId == '1000'
| summarize Failures = sum(itemCount) by cloud_RoleInstance, cloud_RoleName
| where Failures > 3
```

```
// Detect recycle loops
EventLogs
| where eventId == '1006'
| summarize Failures = sum(itemCount) by cloud_RoleInstance, cloud_RoleName
| where Failures > 3
```

## <a name="create-an-alert"></a>Tworzenie alertu

W menu nawigacyjnym w zasobie usługi Application Insights przejdź do **obszaru Alerty**, a następnie wybierz pozycję **Nowa reguła alertu**.

![Zrzut ekranu przedstawiający regułę tworzenia](./media/proactive-cloud-services/001.png)

W oknie **Tworzenie reguły** w sekcji **Definiowanie warunków alertu** kliknij pozycję **Dodaj kryteria**, a następnie wybierz pozycję **Niestandardowe wyszukiwanie w dzienniku**.

![Zrzut ekranu przedstawiający definiowanie kryteriów stanu alertu](./media/proactive-cloud-services/002.png)

W polu **Kwerenda wyszukiwania** wklej połączone zapytanie przygotowane w poprzednim kroku.

Następnie przejdź do pola **Próg** i ustaw jego wartość na 0. Opcjonalnie można dostosować **pola** **Okres** i Częstotliwość .
Kliknij przycisk **Gotowe**.

![Zrzut ekranu przedstawiający konfigurowanie kwerendy logiki sygnału](./media/proactive-cloud-services/003.png)

W sekcji **Definiowanie szczegółów alertu** podaj **nazwę** i **opis** reguły alertu i ustaw jej **ważność**.
Upewnij się również, że **przycisk Włącz regułę przy tworzeniu** jest ustawiony na **Tak**.

![Szczegóły alertu zrzutu ekranu](./media/proactive-cloud-services/004.png)

W sekcji **Definiowanie grupy akcji** można wybrać istniejącą **grupę akcji** lub utworzyć nową.
Można wybrać, aby grupa akcji zawierała wiele akcji różnych typów.

![Grupa akcji zrzutu ekranu](./media/proactive-cloud-services/005.png)

Po zdefiniowaniu grupy Akcja potwierdź zmiany i kliknij pozycję **Utwórz regułę alertu**.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o automatycznym wykrywaniu:

[Anomalie błędów](../../azure-monitor/app/proactive-failure-diagnostics.md)
[przecieków](../../azure-monitor/app/proactive-potential-memory-leak.md)
[pamięci Anomalie wydajności](../../azure-monitor/app/proactive-performance-diagnostics.md)

