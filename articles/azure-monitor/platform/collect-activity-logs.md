---
title: Zbieraj i Analizuj Dzienniki aktywności platformy Azure, w obszarze roboczym usługi Log Analytics | Dokumentacja firmy Microsoft
description: Rozwiązanie dzienników aktywności platformy Azure służy do analizowania i wyszukiwanie w dzienniku aktywności platformy Azure w Twojej subskrypcji platformy Azure.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: dbac4c73-0058-4191-a906-e59aca8e2ee0
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 04/11/2019
ms.author: magoedte
ms.openlocfilehash: 4476bb0a5a343fd43ce5ed70cf0e493d0ccae0e9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60741305"
---
# <a name="collect-and-analyze-azure-activity-logs-in-log-analytics-workspace-in-azure-monitor"></a>Zbieranie i analizowanie dzienników aktywności platformy Azure, w obszarze roboczym Log Analytics w usłudze Azure Monitor

![Symbol dzienników aktywności usługi Azure](./media/collect-activity-logs/activity-log-analytics.png)

Rozwiązanie Activity Log Analytics ułatwiają analizowanie i wyszukaj [dziennika aktywności platformy Azure](activity-logs-overview.md) we wszystkich subskrypcjach platformy Azure. Dziennik aktywności platformy Azure jest dziennik, który zawiera szczegółowe informacje dotyczące operacji wykonywanych na zasobach w Twoich subskrypcjach. Dziennik aktywności była wcześniej znana jako *dzienników inspekcji* lub *operacyjne dzienniki* od momentu jego raporty zdarzeń dla subskrypcji.

Korzystając z dziennika aktywności można określić *co*, *kto*, i *podczas* dla dowolnego zapisu (PUT, POST, DELETE) dla zasobów w ramach subskrypcji. Dodatkowo użytkownik rozumie stan działania i inne odpowiednie właściwości. Dziennik aktywności nie obejmują operacji odczytu (GET) ani operacji dotyczących zasobów, które używają klasycznego modelu wdrażania.

Po nawiązaniu połączenia dzienników aktywności platformy Azure z obszarem roboczym usługi Log Analytics, możesz wykonywać następujące czynności:

- Analizowanie dzienników aktywności, za pomocą wstępnie zdefiniowanych widoków
- Analizowanie i przeszukiwanie dzienników aktywności z wieloma subskrypcjami platformy Azure
- Zachowaj dzienników aktywności przez dłużej niż 90 dni<sup>1</sup>
- Skorelowania dzienników aktywności z platformą Azure, inne platformy i aplikacji danych
- Zobacz działań operacyjnych zagregowanych wg stanu
- Wyświetlanie trendów działania wykonywane na wszystkich usług platformy Azure
- Raport na temat zmian autoryzacji dla wszystkich zasobów platformy Azure
- Identyfikowanie awarii lub usługi problemy z kondycją wywierania wpływu na zasoby
- Umożliwia korelowanie aktywności użytkowników, operacje automatycznego skalowania, zmiany autoryzacji i kondycję usługi do innych dzienników i metryk ze środowiska wyszukiwania w Dzienniku

<sup>1</sup>Domyślnie usługa Azure Monitor przechowuje dzienników aktywności platformy Azure w obszarze roboczym usługi Log Analytics przez 90 dni, nawet jeśli znajdują się w ramach warstwy bezpłatna. Lub, jeśli masz obszar roboczy ustawienia przechowywania, mniej niż 90 dni. Jeśli obszar roboczy ma przechowywania, która jest większa niż 90 dni, dzienniki aktywności są przechowywane na podstawie na okres przechowywania obszaru roboczego.

Obszar roboczy usługi Log Analytics zbiera Dzienniki aktywności są bezpłatne i dzienniki są przechowywane przez 90 dni wolne od opłat. W przypadku przechowywania dzienników przez czas dłuższy niż 90 dni spowoduje naliczenie opłaty za przechowywanie danych na dane przechowywane dłużej niż 90 dni.

Podczas korzystania z warstwy cenowej bezpłatna, dzienniki aktywności nie dotyczą dzienne zużycie w magazynie danych.

## <a name="connected-sources"></a>Połączone źródła

W przeciwieństwie do większości innych rozwiązań usługi Azure Monitor dane nie są zbierane dla dzienników aktywności przez agentów. Wszystkie dane używane przez rozwiązania pochodzi bezpośrednio na platformie Azure.

| Połączone źródło | Obsługiwane | Opis |
| --- | --- | --- |
| [Agenci dla systemu Windows](agent-windows.md) | Nie | Rozwiązanie nie zbiera informacji od agentów Windows. |
| [Agenci dla systemu Linux](../learn/quick-collect-linux-computer.md) | Nie | Rozwiązanie zbiera informacje z agentów dla systemu Linux. |
| [Grupa zarządzania programu System Center Operations Manager](om-agents.md) | Nie | Rozwiązanie zbiera informacje z agentów raportujących do grupy zarządzania programu Operations Manager. |
| [Konto usługi Azure Storage](collect-azure-metrics-logs.md) | Nie | Rozwiązanie zbiera informacje z usługi Azure storage. |

## <a name="prerequisites"></a>Wymagania wstępne

Aby uzyskać dostęp do informacji z dziennika aktywności platformy Azure, musi mieć subskrypcję platformy Azure.

To rozwiązanie wymaga również, że następujących dwóch dostawców zasobów są zarejestrowane w ramach subskrypcji:

1. Microsoft.OperationalInsights
2. Microsoft.OperationsManagement

Aby dowiedzieć się, jak zarejestrować lub upewnij się, są rejestrowane, zobacz [dostawcy zasobów platformy Azure i ich typy](../../azure-resource-manager/resource-manager-supported-services.md)

## <a name="configuration"></a>Konfigurowanie

Wykonaj poniższe kroki, aby skonfigurować rozwiązanie Activity Log Analytics dla obszarów roboczych.

1. Zaloguj się do witryny Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com).

2. Włącz rozwiązanie Activity Log Analytics z [portalu Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.AzureActivityOMS?tab=Overview) lub przy użyciu procesu opisanego w [Add Log Analytics solutions from the Solutions Gallery (Dodawanie rozwiązań usługi Log Analytics z galerii rozwiązań)](../insights/solutions.md).

2. Konfigurowanie dzienników aktywności, aby przejść do obszaru roboczego usługi Log Analytics.
    1. W witrynie Azure portal, wybierz swój obszar roboczy, a następnie kliknij przycisk **dziennik aktywności platformy Azure**.
    2. Dla każdej subskrypcji kliknij nazwę subskrypcji.  
        
        ![Dodaj subskrypcję](./media/collect-activity-logs/add-subscription.png)
    
    3. W *SubscriptionName* bloku kliknij **Connect**.  
    
        ![Łączenie subskrypcji](./media/collect-activity-logs/subscription-connect.png)

## <a name="using-the-solution"></a>Użycie rozwiązania

Po dodaniu rozwiązania Activity Log Analytics do swojego obszaru roboczego **dzienników aktywności platformy Azure** zostanie dodany Kafelek do pulpitu nawigacyjnego przeglądu. Ten Kafelek zawiera liczbę rekordów działań platformy Azure dla subskrypcji platformy Azure, które rozwiązanie ma dostęp do.

![Kafelek Dzienniki aktywności platformy Azure](./media/collect-activity-logs/azure-activity-logs-tile.png)

### <a name="view-azure-activity-logs"></a>Dzienniki aktywności platformy Azure w widoku

Kliknij przycisk **dzienników aktywności platformy Azure** Kafelek, aby otworzyć **dzienników aktywności platformy Azure** pulpitu nawigacyjnego. Na pulpicie nawigacyjnym znajdują się bloki wymienione w poniższej tabeli. Każdy blok zawiera do 10 elementów spełniających kryteria tego bloku dla określonego zakresu i czasu. Możesz uruchomić przeszukiwanie dzienników, które zwróci wszystkie rekordy. W tym celu kliknij przycisk **Zobacz wszystko** na dole bloku lub kliknij nagłówek bloku.

Dane dzienników aktywności jest wyświetlany tylko *po* zostały skonfigurowane z dzienników aktywności, aby przejść do rozwiązania, więc nie można wyświetlić danych przed tym dniem.

| Blok | Opis |
| --- | --- |
| Wpisy dziennika aktywności platformy Azure | Przedstawia wykres słupkowy w górnej części wpis dziennika aktywności platformy Azure rekordu sumy dla zakresu dat, który wybrano i wyświetla listę najważniejszych wywołań 10 działania. Kliknij wykres słupkowy Aby uruchomić wyszukiwanie w dzienniku dla <code>AzureActivity</code>. Kliknij element wywołujący, aby uruchomić wyszukiwanie w dzienniku zwraca wszystkie wpisy dziennika aktywności dla tego elementu. |
| Dzienniki aktywności według stanu | Przedstawia wykres pierścieniowy stanu dziennika aktywności platformy Azure dotyczące zakresu dat, które zostały wybrane. Lista pierwszych rekordów stanu dziesięć również pokazuje listę. Kliknij wykres, aby uruchomić wyszukiwanie w dzienniku dla <code>AzureActivity &#124; summarize AggregatedValue = count() by ActivityStatus</code>. Kliknij element stanu, aby uruchomić wyszukiwanie w dzienniku zwraca wszystkie wpisy dziennika aktywności dla tego rekordu stanu. |
| Dzienniki aktywności według zasobu | Zawiera całkowitą liczbę zasobów przy użyciu dzienników aktywności i zawiera listę pierwszych dziesięciu zasobów przy użyciu rekordu jest liczona dla każdego zasobu. Kliknij obszar całkowitej, aby uruchomić wyszukiwanie w dzienniku dla <code>AzureActivity &#124; summarize AggregatedValue = count() by Resource</code>, które wyświetla wszystkie zasoby platformy Azure dostępnych do rozwiązania. Kliknij zasób, aby uruchomić wyszukiwanie w dzienniku zwraca wszystkie rekordy działanie dla tego zasobu. |
| Dzienniki aktywności przez dostawcę zasobów | Pokazuje, całkowita liczba dostawców zasobów, które generują działania rejestruje i wyświetla listę pierwszych dziesięć. Kliknij obszar całkowitej, aby uruchomić wyszukiwanie w dzienniku dla <code>AzureActivity &#124; summarize AggregatedValue = count() by ResourceProvider</code>, które wyświetla wszystkich dostawców zasobów platformy Azure. Kliknij dostawcę zasobów, aby uruchomić wyszukiwanie w dzienniku zwracanie wszystkich rekordów działań dla dostawcy. |

![Pulpitu nawigacyjnego dzienników aktywności platformy Azure](./media/collect-activity-logs/activity-log-dash.png)

## <a name="next-steps"></a>Kolejne kroki

- Tworzenie [alert](../platform/alerts-metric.md) podczas określonego działania się dzieje.
- Użyj [wyszukiwanie w dzienniku](../log-query/log-query-overview.md) Aby wyświetlić szczegółowe informacje z dzienników aktywności.
