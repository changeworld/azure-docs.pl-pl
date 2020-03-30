---
title: Usługa Azure Application Insights | Dokumenty firmy Microsoft
description: Skorelowanie danych z usługi Application Insights z innymi zestawami danych, takimi jak tabele wzbogacania danych lub odnośników, źródła danych usługi non-Application Insights i dane niestandardowe.
ms.topic: conceptual
author: eternovsky
ms.author: evternov
ms.date: 08/08/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 121e4699bd6a72f6865d3a6ffdef58c1b3806047
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79082765"
---
# <a name="correlating-application-insights-data-with-custom-data-sources"></a>Korelowanie danych usługi Application Insights z niestandardowymi źródłami danych

Usługa Application Insights zbiera kilka różnych typów danych: wyjątki, ślady, widoki strony i inne. Chociaż jest to często wystarczające do zbadania wydajności, niezawodności i użycia aplikacji, istnieją przypadki, gdy jest to przydatne do skorelowania danych przechowywanych w usłudze Application Insights z innymi całkowicie niestandardowymi zestawami danych.

Niektóre sytuacje, w których możesz chcieć danych niestandardowych, obejmują:

- Tabele wzbogacania danych lub wyszukiwania: na przykład uzupełniają nazwę serwera z właścicielem serwera i lokalizacją laboratorium, w której można ją znaleźć 
- Korelacja ze źródłami danych usługi non-Application Insights: na przykład skorelowanie danych o zakupie w sklepie internetowym z informacjami z usługi realizacji zakupu, aby określić, jak dokładne były szacowane czas wysyłki 
- Całkowicie niestandardowe dane: wielu naszych klientów uwielbia język zapytań i wydajność platformy dziennika usługi Azure Monitor, która wspiera usługę Application Insights i chce używać jej do wykonywania zapytań o dane, które nie są w ogóle powiązane z usługą Application Insights. Na przykład, aby śledzić wydajność panelu słonecznego w ramach instalacji inteligentnego domu, jak opisano [tutaj](https://www.catapultsystems.com/blogs/using-log-analytics-and-a-special-guest-to-forecast-electricity-generation/).

## <a name="how-to-correlate-custom-data-with-application-insights-data"></a>Jak skorelować dane niestandardowe z danymi usługi Application Insights 

Ponieważ usługa Application Insights jest wspierana przez zaawansowana platforma dziennika usługi Azure Monitor, możemy użyć pełnej mocy usługi Azure Monitor do pozyskiwania danych. Następnie będziemy pisać zapytania przy użyciu operatora "join", który skoreluje te dane niestandardowe z danymi dostępnymi dla nas w dziennikach usługi Azure Monitor. 

## <a name="ingesting-data"></a>Pozyskiwanie danych

W tej sekcji przecenimy sposób wprowadzania danych do dzienników usługi Azure Monitor.

Jeśli jeszcze go nie masz, aprowizuj nowy obszar roboczy usługi Log Analytics, postępując zgodnie z [tymi instrukcjami](../learn/quick-collect-azurevm.md) za pomocą kroku "utwórz obszar roboczy" i dołączając do nich.

Aby rozpocząć wysyłanie danych dziennika do usługi Azure Monitor. Istnieje kilka opcji:

- W przypadku mechanizmu synchronicznie można bezpośrednio wywołać [interfejs API modułu zbierającego dane](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-collector-api) lub użyć naszego łącznika aplikacji logiki — po prostu poszukaj opcji "Usługa Azure Log Analytics" i wybierz opcję "Wyślij dane":

  ![Zrzut ekranu wybierz i akcja](./media/custom-data-correlation/01-logic-app-connector.png)  

- Dla opcji asynchronicznie, użyj interfejsu API modułu zbierającego dane do tworzenia potoku przetwarzania. Zobacz [ten artykuł,](https://docs.microsoft.com/azure/log-analytics/log-analytics-create-pipeline-datacollector-api) aby uzyskać szczegółowe informacje.

## <a name="correlating-data"></a>Korelowanie danych

Usługa Application Insights jest oparta na platformie dziennika usługi Azure Monitor. W związku z tym możemy użyć [sprzężeń między zasobami,](https://docs.microsoft.com/azure/log-analytics/log-analytics-cross-workspace-search) aby skorelować wszystkie dane, które zostały zebrane w usłudze Azure Monitor z naszymi danymi usługi Application Insights.

Na przykład możemy pozyskiwania naszych zapasów laboratorium i lokalizacje do tabeli o nazwie "LabLocations_CL" w obszarze roboczym usługi Log Analytics o nazwie "myLA". Jeśli następnie chcieliśmy przejrzeć nasze żądania śledzone w aplikacji Usługi Application Insights o nazwie "myAI" i skorelować nazwy komputerów, które obsługiwały żądania z lokalizacjami tych maszyn przechowywanych w wcześniej wymienionej tabeli niestandardowej, możemy uruchomić następujące zapytanie z kontekst usługi Application Insights lub Azure Monitor:

```
app('myAI').requests
| join kind= leftouter (
    workspace('myLA').LabLocations_CL
    | project Computer_S, Owner_S, Lab_S
) on $left.cloud_RoleInstance == $right.Computer
```

## <a name="next-steps"></a>Następne kroki

- Zapoznaj się z odwołaniem do [interfejsu API modułu zbierającego dane.](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-collector-api)
- Aby uzyskać więcej informacji na temat [sprzężeń między zasobami](https://docs.microsoft.com/azure/log-analytics/log-analytics-cross-workspace-search).
