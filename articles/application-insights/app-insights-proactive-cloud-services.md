---
title: Alert dotyczący problemów dotyczących usługi w chmurze Azure za pomocą diagnostyki Azure integracji z usługą Azure Application Insights | Dokumentacja firmy Microsoft
description: Monitor kwestie, takie jak błędy podczas uruchamiania, awarii (Crash) i rola Odtwórz pętli w usług Azure Cloud Services z usługą Azure Application Insights
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: ea2a28ed-4cd9-4006-bd5a-d4c76f4ec20b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 06/07/2018
ms.author: harelbr; mbullwin
ms.openlocfilehash: 18817fd84a86a72d379f96973b71658f2cdf4afd
ms.sourcegitcommit: 3c3488fb16a3c3287c3e1cd11435174711e92126
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/07/2018
ms.locfileid: "34851322"
---
# <a name="alert-on-issues-in-azure-cloud-services-using-the-azure-diagnostics-integration-with-azure-application-insights"></a>Alert po wystąpieniu problemów dotyczących usługi w chmurze Azure przy użyciu integracji diagnostycznych platformy Azure z usługą Azure Application Insights

W tym artykule firma Microsoft będzie opisują sposób ustawiania reguł alertów, które monitorują problemów, takich jak błędy podczas uruchamiania, awarii (Crash) i rola Odtwórz pętli w usługi w chmurze Azure (role sieci web i proces roboczy).

Metoda opisane w tym artykule jest oparta na [diagnostyki Azure integracji z usługą Application Insights](https://azure.microsoft.com/blog/azure-diagnostics-integration-with-application-insights/)i nowszych [alerty dziennika dla usługi Application Insights](https://azure.microsoft.com/blog/log-alerts-for-application-insights-preview/) możliwości.

## <a name="define-a-base-query"></a>Zdefiniuj zapytanie o podstawowej

Aby rozpocząć, zdefiniujemy podstawowej kwerendę, która pobiera zdarzenia w dzienniku zdarzeń systemu Windows z kanału systemu Windows Azure, które są przechwytywane do usługi Application Insights jako rekordy śledzenia.
Te rekordy mogą służyć do wykrywania różne problemy w usług Azure Cloud Services, takich jak błędy podczas uruchamiania, błędy środowiska uruchomieniowego i Odtwórz pętle.

> [!NOTE]
> Poniższe zapytanie podstawowej sprawdza problemów z oknem czasu 30 minut i założono w wprowadzania rejestruje dane telemetryczne opóźnieniem 10 minut. Te ustawienia domyślne można skonfigurować zgodnie z własnymi potrzebami.

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

## <a name="check-for-specific-event-ids"></a>Sprawdź, czy określone identyfikatory zdarzeń.

Po pobraniu zdarzenia dziennika zdarzeń systemu Windows, określonych problemów mogą być wykrywane przez wyszukiwanie ich odpowiednich Identyfikatora i komunikatu właściwości zdarzenia (Zobacz przykłady poniżej).
Po prostu połączyć zapytania bazowego powyżej jednego zapytania poniżej i używane, które są połączone zapytania, definiując reguły alertu dziennika.

> [!NOTE]
> W poniższych przykładach zostanie wykryty problem w przypadku znalezienia więcej niż trzech zdarzeń podczas przedział czasu analizy. To ustawienie domyślne można skonfigurować tak, aby zmienić ważność reguły alertu.

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

W menu nawigacji w ramach zasobu usługi Application Insights, przejdź do **alerty**, a następnie wybierz **nową regułę alertu**.

![Zrzut ekranu Utwórz regułę](./media/app-insights-proactive-cloud-services/001.png)

W **Utwórz reguły** okna, w obszarze **Zdefiniuj warunek alertu** kliknij na **Dodaj kryteria**, a następnie wybierz **wyszukiwania dziennika niestandardowego**.

![Zrzut ekranu przedstawiający zdefiniuj kryteria warunku dla alertu](./media/app-insights-proactive-cloud-services/002.png)

W **zapytania wyszukiwania** Wklej zapytanie Scalonej przygotowanym w poprzednim kroku.

Następnie przejdź do **próg** pole i ustaw jej wartość na 0. Opcjonalnie może dostosować **okres** i częstotliwość **pola**.
Kliknij przycisk **Gotowe**.

![Zrzut ekranu przedstawiający Konfigurowanie sygnału logiki kwerendy](./media/app-insights-proactive-cloud-services/003.png)

W obszarze **Zdefiniuj szczegóły alertu** sekcji, podaj **nazwa** i **opis** reguły alertów i zestawu jego **ważność**.
Ponadto upewnij się, że **Włącz regułę po utworzeniu** ustawiono przycisk **tak**.

![Zrzut ekranu szczegółów alertu](./media/app-insights-proactive-cloud-services/004.png)

W obszarze **grupy akcji Definiuj** sekcji, można wybrać istniejącą **grupy akcji** lub Utwórz nową.
Istnieje możliwość grupa akcji zawiera wiele akcji różnych typów.

![Zrzut ekranu grupy akcji](./media/app-insights-proactive-cloud-services/005.png)

Po zdefiniowaniu grupy akcji, Potwierdź wprowadzone zmiany i kliknij przycisk **Utwórz regułę alertu**.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat automatycznego wykrywania:

[Błąd anomalii](app-insights-proactive-failure-diagnostics.md)
[przecieki pamięci](app-insights-proactive-potential-memory-leak.md)
[anomalii wydajności](app-insights-proactive-performance-diagnostics.md)

