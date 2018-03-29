---
title: Importuj dane usługi Analiza dzienników Azure do usługi Power BI | Dokumentacja firmy Microsoft
description: Usługa Power BI jest oparte na chmurze usługi analizy biznesowej firmy Microsoft, który udostępnia zaawansowane wizualizacje i raporty do analizy różne zestawy danych.  W tym artykule opisano sposób konfigurowania i zaimportuj dane analizy dzienników do usługi Power BI i skonfigurować jej automatyczne odświeżenie.
services: log-analytics
documentationcenter: ''
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: 83edc411-6886-4de1-aadd-33982147b9c3
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/19/2018
ms.author: bwren
ms.openlocfilehash: 725828c2acc5ac4bb53c5e6af14d20578a3d3652
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2018
---
# <a name="import-azure-log-analytics-data-into-power-bi"></a>Importuj dane usługi Analiza dzienników Azure do usługi Power BI


[Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/) jest oparte na chmurze usługi analizy biznesowej firmy Microsoft, który udostępnia zaawansowane wizualizacje i raporty do analizy różne zestawy danych.  Wyniki wyszukiwania dziennika analizy dzienników można zaimportować do usługi Power BI zestawu danych, można wykorzystać jej funkcje, takie jak udostępnianie raportów w sieci web i urządzeń przenośnych i łączenie danych z różnych źródeł.

## <a name="overview"></a>Przegląd
Aby zaimportować dane z obszaru roboczego analizy dzienników do usługi Power BI, należy utworzyć zestawu danych w usłudze Power BI na podstawie zapytania wyszukiwania dziennika w analizy dzienników.  Zapytanie jest uruchamiane na każdym odświeżeniu zestawu danych.  Następnie można tworzyć raporty usługi Power BI, które używają danych z zestawu danych.  Można utworzyć zestawu danych w usłudze Power BI, możesz wyeksportować zapytania z analizy dzienników do [języka Power Query (M)](https://msdn.microsoft.com/library/mt807488.aspx).  Następnie wykorzystać te informacje, aby utworzyć zapytanie w programie Power BI Desktop, a następnie opublikować go do usługi Power BI jako zestawu danych.  Szczegóły tego procesu są opisane poniżej.

![Analiza dzienników do usługi Power BI](media/log-analytics-powerbi/overview.png)

## <a name="export-query"></a>Kwerendy eksportu
Rozpocznij od utworzenia [wyszukiwania dziennika](log-analytics-log-search-new.md) która zwraca dane z analizy dzienników chcesz wypełnić dataset usługi Power BI.  Następnie wyeksportować tego zapytania do [Power Query (M) języka](https://msdn.microsoft.com/library/mt807488.aspx) które mogą być używane przez program Power BI Desktop.

1. Utwórz wyszukiwania dziennika w analizy dzienników można wyodrębnić danych dla zestawu danych.
2. Jeśli używasz portalu dziennik wyszukiwania, kliknij przycisk **usługi Power BI**.  Jeśli używasz portalu usługi analiza wybierz **wyeksportować** > **Power BI kwerendy (M)**.  Zapytanie obu tych opcji eksportu w pliku tekstowym o nazwie **PowerBIQuery.txt**. 

    ![Eksportuj Dziennik wyszukiwania](media/log-analytics-powerbi/export-logsearch.png) ![Eksportuj Dziennik wyszukiwania](media/log-analytics-powerbi/export-analytics.png)

3. Otwórz plik tekstowy i skopiuj jej zawartość.

## <a name="import-query-into-power-bi-desktop"></a>Importowanie zapytania do Power BI Desktop
Power BI Desktop jest aplikacją, która umożliwia tworzenie zestawów danych i raportów, które mogą być publikowane do usługi Power BI.  Można również użyć do utworzenia zapytania przy użyciu języka dodatku Power Query wyeksportowane z analizy dzienników. 

1. Zainstaluj [Power BI Desktop](https://powerbi.microsoft.com/desktop/) , jeśli nie mają już go, a następnie otwórz aplikację.
2. Wybierz **Pobierz dane** > **puste zapytania** i otworzyć nowe zapytanie.  Następnie wybierz **Zaawansowany edytor** i Wklej zawartość wyeksportowany plik do zapytania. Kliknij przycisk **Gotowe**.

    ![Power BI Desktop zapytania](media/log-analytics-powerbi/desktop-new-query.png)

5. Uruchamia zapytanie, a jego wyniki są wyświetlane.  Może zostać wyświetlony monit o poświadczenia do połączenia z platformą Azure.  
6. Wpisz opisową nazwę zapytania.  Wartość domyślna to **Query1**. Kliknij przycisk **zamknąć i zastosować** można dodać zestaw danych do raportu.

    ![Power BI Desktop nazwy](media/log-analytics-powerbi/desktop-results.png)



## <a name="publish-to-power-bi"></a>Publikowanie do usługi Power BI
Podczas publikowania do usługi Power BI zostanie utworzony zestawu danych i raportu.  Jeśli utworzysz raport w programie Power BI Desktop, następnie ta jest publikowana z danymi.  Jeśli nie, następnie zostanie utworzony pusty raport.  Można zmodyfikować raport w usłudze Power BI lub Utwórz nowy, oparty na zestawie danych.

8. Tworzenie raportu na podstawie danych.  Użyj [dokumentacji Power BI Desktop](https://docs.microsoft.com/power-bi/desktop-report-view) Jeśli nie znasz z nim.  Gdy wszystko jest gotowe do wysyłania do usługi Power BI, kliknij przycisk **publikowania**.  Po wyświetleniu monitu wybierz lokalizację docelową na koncie usługi Power BI.  Jeśli nie ma określonego miejsca docelowego, pamiętając, użyj **obszar Mój obszar roboczy**.

    ![Publikowanie Power BI Desktop](media/log-analytics-powerbi/desktop-publish.png)

3. Po zakończeniu publikowania, kliknij **Otwórz w usłudze Power BI** otworzyć usługi Power BI z nowego zestawu danych.


### <a name="configure-scheduled-refresh"></a>Skonfiguruj zaplanowane odświeżanie
Zestaw danych utworzone w usłudze Power BI będzie mieć tych samych danych, który wcześniej był wyświetlany w programie Power BI Desktop.  Należy odświeżyć zestawu danych, aby ponownie uruchomić zapytanie i wypełnić je przy użyciu najnowszych danych z analizy dzienników.  

1. Kliknij opcję w obszarze roboczym, w którym przekazać raport i wybierz **zestawów danych** menu. Wybierz menu kontekstowe obok nowy zestaw danych, a następnie wybierz **ustawienia**. W obszarze **poświadczenia źródła danych** powinien mieć komunikat, że poświadczenia są nieprawidłowe.  Jest to spowodowane nie zostały podane poświadczenia jeszcze dla zestawu danych do użycia podczas odświeżania danych.  Kliknij przycisk **Edycja poświadczeń** i określić poświadczenia z dostępem do analizy dzienników.

    ![Power BI harmonogramu](media/log-analytics-powerbi/powerbi-schedule.png)

5. W obszarze **zaplanowane odświeżanie** włączyć opcję **aktualności danych**.  Opcjonalnie można zmienić **częstotliwość odświeżania** i jeden lub więcej razy określone do uruchomienia odświeżania.

    ![Power BI odświeżania](media/log-analytics-powerbi/powerbi-schedule-refresh.png)



## <a name="next-steps"></a>Kolejne kroki
* Dowiedz się więcej o [dziennika wyszukiwania](log-analytics-log-searches.md) do tworzenia zapytań, które mogą być eksportowane do usługi Power BI.
* Dowiedz się więcej o [usługi Power BI](http://powerbi.microsoft.com) tworzenie wizualizacji opartych na eksportuje analizy dzienników.
