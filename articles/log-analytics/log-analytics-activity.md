---
title: Zbieranie i analizowanie dzienników Azure aktywności w Log Analytics | Dokumentacja firmy Microsoft
description: Rozwiązania Azure Dzienniki aktywności służy do analizowania i przeszukiwać dziennik aktywności platformy Azure Twojej subskrypcji platformy Azure.
services: log-analytics
documentationcenter: ''
author: MGoedtel
manager: carmonm
editor: ''
ms.assetid: dbac4c73-0058-4191-a906-e59aca8e2ee0
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/26/2018
ms.author: magoedte
ms.openlocfilehash: b6e823d9338d76a350569091d6794e3ac4a2eae9
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2018
ms.locfileid: "30283388"
---
# <a name="collect-and-analyze-azure-activity-logs-in-log-analytics"></a>Zbieranie i analizowanie dzienników aktywności platformy Azure w analizy dzienników

![Symbol Dzienniki aktywności platformy Azure](./media/log-analytics-activity/activity-log-analytics.png)

Rozwiązania analizy dziennika aktywności ułatwiają analizowanie i wyszukaj [dziennik aktywności platformy Azure](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md) wszystkich subskrypcji platformy Azure. Dziennik aktywności platformy Azure jest dziennika, który oferuje wgląd w operacji wykonywanych na zasobów w subskrypcji. Dziennik aktywności była wcześniej znana jako *dzienników inspekcji* lub *operacyjne dzienniki* ponieważ raporty zdarzeń dla subskrypcji.

Korzystając z dziennika aktywności, można określić *co*, *kto*, i *podczas* dla żadnego zapisu (PUT, POST, DELETE) dotyczące zasobów w ramach subskrypcji. Można także poznać stan działania i inne odpowiednie właściwości. Dziennik nie zawiera operacje odczytu (GET) lub operacji dla zasobów korzystających z klasycznego modelu wdrożenia.

Podczas nawiązywania połączenia dzienników działanie Azure Log Analytics, możesz:

- Analizowanie dzienników działania z wstępnie zdefiniowanych widoków
- Analizowanie i dzienniki wyszukiwania i działania z wieloma subskrypcjami platformy Azure
- Zachowaj dzienniki aktywności przez czas dłuższy niż 90 dni<sup>1</sup>
- Dzienniki aktywności jest skorelowany z innych Azure danych platformy i aplikacji
- Zobacz działań operacyjnych w stan
- Wyświetlanie trendów działania wykonywane na każdej z usługami Azure
- Raport dotyczący zmiany autoryzacji na wszystkich zasobów na platformie Azure
- Określenie awarii lub usługa zagadnień dotyczących kondycji wpływu na zasoby
- Użyj dziennika wyszukiwania służące do skorelowania aktywności użytkowników, wykonanie operacji skalowania automatycznego zmiany autoryzacji i usługi kondycji do innych dzienników lub metryk ze środowiska

<sup>1</sup>domyślnie analizy dzienników zachowuje Dzienniki aktywności platformy Azure przez 90 dni, nawet jeśli znajdują się na warstwę bezpłatna. Lub, jeśli masz obszar roboczy ustawienia przechowywania mniej niż 90 dni. Jeśli obszar roboczy ma przechowywania, która jest dłuższa niż 90 dni, dzienniki aktywności są przechowywane na podstawie na okres przechowywania obszaru roboczego.

Analiza dzienników zbiera Dzienniki aktywności bezpłatnie i przechowuje dzienniki bezpłatne 90 dni. Jeśli przechowujesz dzienniki przez czas dłuższy niż 90 dni spowoduje naliczenie opłaty przechowywania danych na dane przechowywane dłużej niż 90 dni.

Gdy użytkownik jest w warstwy cenowej bezpłatna, dzienniki aktywności nie dotyczą codziennego użycia danych.

## <a name="connected-sources"></a>Połączone źródła

W przeciwieństwie do większości innych rozwiązań analizy dzienników danych nie jest zbierane o Dzienniki aktywności przez agentów. Wszystkie dane używane przez to rozwiązanie pochodzi bezpośrednio z platformy Azure.

| Połączone źródło | Obsługiwane | Opis |
| --- | --- | --- |
| [Agenci dla systemu Windows](log-analytics-windows-agent.md) | Nie | Rozwiązanie nie zbiera informacje z agentów systemu Windows. |
| [Agenci dla systemu Linux](log-analytics-linux-agents.md) | Nie | Rozwiązanie nie zbiera informacje z agentów systemu Linux. |
| [Grupa zarządzania programu SCOM](log-analytics-om-agents.md) | Nie | Rozwiązanie nie zbiera informacje z agentów w podłączonej grupy zarządzania SCOM. |
| [Konto usługi Azure Storage](log-analytics-azure-storage.md) | Nie | Rozwiązanie nie zbiera informacje z usługi Azure storage. |

## <a name="prerequisites"></a>Wymagania wstępne

- Aby uzyskać dostęp do informacji dziennika aktywności platformy Azure, musi mieć subskrypcję platformy Azure.

## <a name="configuration"></a>Konfigurowanie

Wykonaj poniższe kroki, aby skonfigurować rozwiązanie działania analizy dzienników dla obszarów roboczych.

1. Włącz rozwiązania analizy dzienników działania z [witrynę Azure marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.AzureActivityOMS?tab=Overview) lub przy użyciu procesu opisanego w [rozwiązań dodać analizy dzienników z galerii rozwiązań](log-analytics-add-solutions.md).
2. Skonfiguruj Dzienniki aktywności, aby przejść do obszaru roboczego analizy dzienników.
    1. W portalu Azure wybierz obszar roboczy, a następnie kliknij przycisk **dziennik aktywności platformy Azure**.
    2. Dla każdej subskrypcji kliknij nazwę subskrypcji.  
        ![Dodaj subskrypcję](./media/log-analytics-activity/add-subscription.png)
    3. W *Nazwa subskrypcji* bloku, kliknij przycisk **Connect**.  
        ![Łączenie subskrypcji](./media/log-analytics-activity/subscription-connect.png)

Jeśli dodasz rozwiązania przy użyciu portalu OMS, zostanie wyświetlony następujący fragment. Zaloguj się do portalu Azure do połączenia subskrypcji platformy Azure do swojego obszaru roboczego.  
![zostanie wykonana oceny](./media/log-analytics-activity/tile-performing-assessment.png)

## <a name="using-the-solution"></a>Użycie rozwiązania

Po dodaniu rozwiązania analizy dziennika aktywności do swojego obszaru roboczego **Dzienniki aktywności Azure** kafelka jest dodawany do pulpitu nawigacyjnego przeglądu. Jest wyświetlana liczba Liczba rekordów działanie Azure dla subskrypcji platformy Azure, które rozwiązanie ma dostęp do tego kafelka.

![Kafelek Dzienniki aktywności platformy Azure](./media/log-analytics-activity/azure-activity-logs-tile.png)

### <a name="view-azure-activity-logs"></a>Rejestruje działanie usługi Azure widoku

Kliknij przycisk **Dzienniki aktywności Azure** Kafelek, aby otworzyć **Dzienniki aktywności Azure** pulpitu nawigacyjnego. Pulpit nawigacyjny zawiera bloki w poniższej tabeli. Każdy blok zawiera maksymalnie 10 elementów spełniających kryteria tego bloku dla określonego zakresu i zakres czasu. Można uruchomić wyszukiwania dziennika, który zwraca wszystkie rekordy, klikając **zobaczyć wszystkie** w dolnej części bloku lub przez kliknięcie nagłówka bloku.

Dane dziennika aktywności jest wyświetlany tylko *po* skonfigurowaniu Dzienniki aktywności można przejść do rozwiązania, więc nie można wyświetlić danych, przed upływem.

| Blok | Opis |
| --- | --- |
| Wpisy dziennika aktywności platformy Azure | Przedstawia wykres słupkowy TOP wpis dziennika aktywności platformy Azure rekordów sumy wybranego zakresu i pokazuje listę top wywołań 10 działania. Kliknij na wykresie słupkowym, aby uruchomić wyszukiwanie dziennika <code>AzureActivity</code>. Kliknij element wywołujący, aby uruchomić wyszukiwanie dziennika zwracanie wszystkich wpisów dziennika aktywności dla danego elementu. |
| Dzienniki aktywności według stanu | Przedstawia wykres pierścieniowy stanu dziennika aktywności platformy Azure wybranego zakresu. Również listy to lista top rekordów stanu 10. Kliknij na wykresie, aby uruchomić wyszukiwanie dziennika <code>AzureActivity &#124; summarize AggregatedValue = count() by ActivityStatus</code>. Kliknij element stanu, aby uruchomić wyszukiwanie dziennika zwracanie wszystkich wpisów dziennika aktywności dla tego rekordu stanu. |
| Dzienniki aktywności przez zasób | Zawiera całkowitą liczbę zasobów o Dzienniki aktywności oraz listę pierwszych dziesięciu zasobów z rekordem liczniki dla każdego zasobu. Kliknij przycisk całkowity obszar, aby uruchomić wyszukiwanie dziennika <code>AzureActivity &#124; summarize AggregatedValue = count() by Resource</code>, który zawiera wszystkie zasoby platformy Azure dostępna w rozwiązaniu. Kliknij zasób, aby uruchomić wyszukiwanie dziennika zwracanie wszystkich rekordów działania dla tego zasobu. |
| Dzienniki aktywności przez dostawcę zasobów | Przedstawia sumę dostawców zasobów, które powodują powstanie działania dzienniki i listy pierwszych dziesięć. Kliknij przycisk całkowity obszar, aby uruchomić wyszukiwanie dziennika <code>AzureActivity &#124; summarize AggregatedValue = count() by ResourceProvider</code>, który wskazuje wszystkich dostawców zasobów platformy Azure. Kliknij dostawcę zasobów, aby uruchomić wyszukiwanie dziennika zwracanie wszystkich rekordów działania dla dostawcy. |

![Azure Dzienniki aktywności pulpitu nawigacyjnego](./media/log-analytics-activity/activity-log-dash.png)

## <a name="next-steps"></a>Kolejne kroki

- Utwórz [alert](log-analytics-alerts-creating.md) po sytuacji określonego działania.
- Użyj [wyszukiwania dziennika](log-analytics-log-searches.md) do wyświetlenia szczegółowych informacji z dzienników działania.
