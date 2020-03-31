---
title: Źródła danych skoroszytów usługi Azure Monitor | Dokumenty firmy Microsoft
description: Uprość składanie złożonych raportów dzięki wstępnie utworzonym i niestandardowych sparametryzowanym skoroszytom usługi Azure Monitor utworzonym z wielu źródeł danych
services: azure-monitor
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: d57910ae31d4db9be17b3dc46b5920a925ab4fcf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79248582"
---
# <a name="azure-monitor-workbooks-data-sources"></a>Źródła danych skoroszytów usługi Azure Monitor

Skoroszyty są zgodne z dużą liczbą źródeł danych. W tym artykule zostaną udostępnione źródła danych, które są obecnie dostępne dla skoroszytów usługi Azure Monitor.

## <a name="logs"></a>Dzienniki

Skoroszyty zezwalają na wykonywanie zapytań o dzienniki z następujących źródeł:

* Dzienniki usługi Azure Monitor (zasoby aplikacji i obszary robocze analizy dzienników).
* Dane zorientowane na zasoby (dzienniki aktywności)

Autorzy skoroszytu mogą używać kwerend KQL, które przekształcają podstawowe dane zasobów, aby wybrać zestaw wyników, który można wizualizować jako tekst, wykresy lub siatki.

![Zrzut ekranu przedstawiający interfejs raportu dzienników skoroszytów](./media/workbooks-overview/logs.png)

Autorzy skoroszytów mogą łatwo wyszukiwać w wielu zasobach, tworząc prawdziwie ujednolicone środowisko raportowania.

## <a name="metrics"></a>Metryki

Zasoby platformy Azure emitują [metryki,](data-platform-metrics.md) do których można uzyskać dostęp za pośrednictwem skoroszytów. Metryki są dostępne w skoroszytach za pomocą wyspecjalizowanego formantu, który umożliwia określenie zasobów docelowych, żądanych metryk i ich agregacji. Dane te można następnie wykreślać na wykresach lub w siatce.

![Zrzut ekranu przedstawiający wykresy metryki skoroszytu dotyczące wykorzystania procesora](./media/workbooks-overview/metrics-graph.png)

![Zrzut ekranu przedstawiający interfejs metryki skoroszytu](./media/workbooks-overview/metrics.png)

## <a name="azure-resource-graph"></a>Azure Resource Graph 

Skoroszyty obsługują kwerendy dotyczące zasobów i ich metadanych przy użyciu programu Azure Resource Graph (ARG). Ta funkcja jest używana głównie do tworzenia niestandardowych zakresów kwerend dla raportów. Zakres zasobu jest wyrażona za pośrednictwem podzbiór KQL, który obsługuje ARG — co często jest wystarczające dla typowych przypadków użycia.

Aby formant kwerendy używał tego źródła danych, użyj listy rozwijanej Typ kwerendy, aby wybrać program Azure Resource Graph i wybrać subskrypcje docelowe. Użyj formantu Kwerenda, aby dodać arg KQL-podzbiór, który wybiera ciekawy podzbiór zasobów.


![Zrzut ekranu przedstawiający kwerendę KQL programu Azure Resource Graph](./media/workbooks-overview/azure-resource-graph.png)

## <a name="alerts-preview"></a>Alerty (wersja zapoznawcza)

Skoroszyty umożliwiają użytkownikom wizualizowanie aktywnych alertów związanych z ich zasobami. Ta funkcja umożliwia tworzenie raportów, które łączą dane powiadomień (alert) i informacje diagnostyczne (metryki, dzienniki) w jednym raporcie. Te informacje można również połączyć ze sobą, aby utworzyć zaawansowane raporty, które łączą szczegółowe informacje w tych źródłach danych.

Aby formant kwerendy używał tego źródła danych, użyj listy rozwijanej Typ kwerendy, aby wybrać pozycję Alerty i wybrać subskrypcje, grupy zasobów lub zasoby do docelowego. Użyj listy rozwijanej filtru alertów, aby wybrać interesujący podzbiór alertów dla twoich potrzeb analitycznych.

![Zrzut ekranu przedstawiający kwerendę alertów](./media/workbooks-overview/alerts.png)

## <a name="workload-health-preview"></a>Kondycja obciążenia (wersja zapoznawcza)

Usługa Azure Monitor ma funkcje, które proaktywnie monitoruje dostępność i wydajność systemów operacyjnych windows lub linux gości. Usługa Azure Monitor modeluje kluczowe składniki i ich relacje, kryteria pomiaru kondycji tych składników i składniki, które ostrzegają użytkownika po wykryciu warunku w złej kondycji. Skoroszyty umożliwiają użytkownikom korzystanie z tych informacji do tworzenia rozszerzonych raportów interaktywnych.

To make a query control use this data source, use the **Query type** drop-down to choose Workload Health and select subscription, resource group or VM resources to target. Użyj rozwijanych filtrów kondycji, aby wybrać interesujący podzbiór zdarzeń dotyczących kondycji dla twoich potrzeb analitycznych.

![Zrzut ekranu przedstawiający kwerendę alertów](./media/workbooks-overview/workload-health.png)

## <a name="azure-resource-health"></a>Kondycja zasobów platformy Azure 

Skoroszyty obsługują uzyskiwanie kondycji zasobów platformy Azure i łączenie ich z innymi źródłami danych w celu tworzenia bogatych, interaktywnych raportów dotyczących kondycji

Aby formant kwerendy używał tego źródła danych, użyj listy rozwijanej **Typ kwerendy,** aby wybrać kondycję platformy Azure i wybrać zasoby docelowe. Użyj rozwijanych filtrów kondycji, aby wybrać interesujący podzbiór problemów z zasobami dla twoich potrzeb analitycznych.

![Zrzut ekranu przedstawiający kwerendę alertów](./media/workbooks-overview/resource-health.png)

## <a name="azure-data-explorer-preview"></a>Eksplorator danych platformy Azure (wersja zapoznawcza)

Skoroszyty mają teraz obsługę zapytań z klastrów [usługi Azure Data Explorer](https://docs.microsoft.com/azure/data-explorer/) za pomocą zaawansowanego języka zapytań [Kusto.](https://docs.microsoft.com/azure/kusto/query/index)   

![Zrzut ekranu przedstawiający okno zapytania Kusto](./media/workbooks-overview/data-explorer.png)

## <a name="next-steps"></a>Następne kroki

* [Rozpocznij](workbooks-visualizations.md) naukę o skoroszytach wiele rozbudowanych opcji wizualizacji.
* [Kontroluj](workbooks-access-control.md) i udostępniaj dostęp do zasobów skoroszytu.
