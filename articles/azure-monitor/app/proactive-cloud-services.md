---
title: Alert dotyczący problemów w usługach Azure Cloud Services za pomocą integracji usługi Azure Diagnostics przy użyciu usługi Azure Application Insights | Dokumentacja firmy Microsoft
description: Monitor kwestie, takie jak błędy podczas uruchamiania, awarii i roli Odtwórz pętli w usługach Azure Cloud Services za pomocą usługi Azure Application Insights
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: ea2a28ed-4cd9-4006-bd5a-d4c76f4ec20b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 06/07/2018
ms.reviewer: harelbr
ms.author: mbullwin
ms.openlocfilehash: 219ba632d7688f1a428378309828b689698d2fe5
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60409555"
---
# <a name="alert-on-issues-in-azure-cloud-services-using-the-azure-diagnostics-integration-with-azure-application-insights"></a>Alert po wystąpieniu problemów w usługach Azure Cloud Services za pomocą integracji diagnostyki platformy Azure za pomocą usługi Azure Application Insights

W tym artykule opisujemy sposób konfigurowania reguł alertów, które monitorują problemów, takich jak błędy podczas uruchamiania, awarii i roli Odtwórz pętli w usługach Azure Cloud Services (role sieć web i proces roboczy).

Metody opisanej w tym artykule opiera się na [integracji usługi Azure Diagnostics przy użyciu usługi Application Insights](https://azure.microsoft.com/blog/azure-diagnostics-integration-with-application-insights/)i jest to udostępniona niedawno [alertów dzienników usługi Application Insights](https://azure.microsoft.com/blog/log-alerts-for-application-insights-preview/) możliwości.

## <a name="define-a-base-query"></a>Zdefiniuj zapytanie o podstawowej

Aby rozpocząć pracę, zdefiniujemy podstawowy zapytanie, które pobiera zdarzenia w dzienniku zdarzeń Windows z kanału usługi Windows Azure, które są przechwytywane do usługi Application Insights jako rekordów śledzenia.
Te rekordy mogą służyć do wykrywania różne problemy w usługach Azure Cloud Services, takich jak błędy podczas uruchamiania, błędy czasu wykonywania i odtwarzanie w pętli.

> [!NOTE]
> Poniższe zapytanie podstawowego sprawdza, czy problemy w przedziale czasowym, 30 minut, a przyjęto założenie, umożliwiając pozyskiwanie rejestruje dane telemetryczne opóźnieniem 10 minut. Te ustawienia domyślne można skonfigurować zgodnie z potrzebami.

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

## <a name="check-for-specific-event-ids"></a>Wyszukaj określone identyfikatory zdarzeń.

Po pobraniu Windows zdarzenia dziennika zdarzeń, określonych problemów może zostać wykryte przez sprawdzenie, czy ich odpowiednich Identyfikatora i komunikatu właściwości zdarzenia (Zobacz przykłady poniżej).
Po prostu Połącz zapytania bazowego powyżej za pomocą jednego zapytania poniżej i używane, które są połączone zapytania podczas definiowania reguł alertów dzienników.

> [!NOTE]
> W poniższych przykładach problem zostanie wykryty, jeśli więcej niż trzy zdarzenia znajdują się w przedziale czasu przeanalizowany. To ustawienie domyślne można skonfigurować tak, aby zmienić ważność reguły alertu.

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

W menu nawigacji w ramach zasobu usługi Application Insights, przejdź do **alerty**, a następnie wybierz pozycję **Nowa reguła alertu**.

![Zrzut ekranu opcji Utwórz regułę](./media/proactive-cloud-services/001.png)

W **Utwórz regułę** okna, w obszarze **Zdefiniuj warunek alertu** sekcji, kliknij pozycję **Dodaj kryteria**, a następnie wybierz pozycję **przeszukiwania dzienników niestandardowych**.

![Zrzut ekranu przedstawiający zdefiniuj kryteria warunku dla alertu](./media/proactive-cloud-services/002.png)

W **zapytania wyszukiwania** pole, Wklej zapytanie połączone został przygotowany w poprzednim kroku.

Następnie przejdź do **próg** polu i ustawić jej wartość na 0. Może być opcjonalnie dostosować **okres** i częstotliwość **pola**.
Kliknij przycisk **Gotowe**.

![Zrzut ekranu przedstawiający Konfigurowanie zapytania logiki sygnału](./media/proactive-cloud-services/003.png)

W obszarze **Zdefiniuj szczegóły alertu** sekcji, podaj **nazwa** i **opis** regułę alertu i ustaw jego **ważność**.
Ponadto upewnij się, że **Włącz regułę po utworzeniu** przycisk jest ustawiona na **tak**.

![Zrzut ekranu szczegóły alertu](./media/proactive-cloud-services/004.png)

W obszarze **zdefiniuj grupę akcji** sekcji można wybrać istniejącą **grupy akcji** lub utworzyć nowy.
Istnieje możliwość mają grupy akcji zawiera wiele akcji różnych typów.

![Zrzut ekranu grupy akcji](./media/proactive-cloud-services/005.png)

Po zdefiniowaniu grupy akcji zatwierdzić zmiany i kliknij **Utwórz regułę alertu**.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat automatycznego wykrywania:

[Anomalie](../../azure-monitor/app/proactive-failure-diagnostics.md)
[przecieki pamięci](../../azure-monitor/app/proactive-potential-memory-leak.md)
[anomalie wydajności](../../azure-monitor/app/proactive-performance-diagnostics.md)

