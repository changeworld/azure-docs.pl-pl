---
title: Importowanie danych usługi Azure Log Analytics do usługi Power BI | Dokumenty firmy Microsoft
description: Usługa Power BI to oparta na chmurze usługa analizy biznesowej firmy Microsoft, która zapewnia zaawansowane wizualizacje i raporty do analizy różnych zestawów danych.  W tym artykule opisano sposób konfigurowania i importowania danych usługi Log Analytics do usługi Power BI i konfigurowania ich do automatycznego odświeżania.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/01/2019
ms.openlocfilehash: 8ff24d508eb35c4f2a04c7d024254fa6f1875da8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77659289"
---
# <a name="import-azure-monitor-log-data-into-power-bi"></a>Importowanie danych dziennika usługi Azure Monitor do usługi Power BI


[Usługa Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/) to oparta na chmurze usługa analizy biznesowej firmy Microsoft, która zapewnia zaawansowane wizualizacje i raporty do analizy różnych zestawów danych.  Wyniki kwerendy dziennika usługi Azure Monitor można zaimportować do zestawu danych usługi Power BI, aby korzystać z jego funkcji, takich jak łączenie danych z różnych źródeł i udostępnianie raportów w sieci Web i urządzeniach przenośnych.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="overview"></a>Omówienie
Aby zaimportować dane z [obszaru roboczego usługi Log Analytics](manage-access.md) w usłudze Azure Monitor do usługi Power BI, należy utworzyć zestaw danych w usłudze Power BI na podstawie [kwerendy dziennika](../log-query/log-query-overview.md) w usłudze Azure Monitor.  Kwerenda jest uruchamiana przy każdym odświeżeniu zestawu danych.  Następnie można tworzyć raporty usługi Power BI, które używają danych z zestawu danych.  Aby utworzyć zestaw danych w usłudze Power BI, należy wyeksportować kwerendę z usługi Log Analytics do [języka dodatku Power Query (M).](https://docs.microsoft.com/powerquery-m/power-query-m-language-specification)  Następnie użyjesz tej kwerendy w programie Power BI Desktop, a następnie opublikuj ją w usłudze Power BI jako zestaw danych.  Szczegóły tego procesu są opisane poniżej.

![Usługa Log Analytics do usługi Power BI](media/powerbi/overview.png)

## <a name="export-query"></a>Eksportowanie zapytania
Zacznij od utworzenia [kwerendy dziennika,](../log-query/log-query-overview.md) która zwraca dane, które chcesz wypełnić zestaw danych usługi Power BI.  Następnie należy wyeksportować tę kwerendę do [języka dodatku Power Query (M),](https://docs.microsoft.com/powerquery-m/power-query-m-language-specification) który może być używany przez program Power BI Desktop.

1. [Utwórz kwerendę dziennika w usłudze Log Analytics,](../log-query/get-started-portal.md) aby wyodrębnić dane dla zestawu danych.
2. Wybierz **pozycję Eksportuj** > **zapytanie usługi Power BI (M)**.  Spowoduje to wyeksliowanie kwerendy do pliku tekstowego o nazwie **PowerBIQuery.txt**. 

    ![Eksportowanie wyszukiwania dzienników](media/powerbi/export-analytics.png)

3. Otwórz plik tekstowy i skopiuj jego zawartość.

## <a name="import-query-into-power-bi-desktop"></a>Importowanie kwerendy do programu Power BI Desktop
Power BI Desktop to aplikacja klasyczna, która umożliwia tworzenie zestawów danych i raportów, które można publikować w usłudze Power BI.  Można go również użyć do utworzenia kwerendy przy użyciu języka dodatku Power Query wyeksportowanego z usługi Azure Monitor. 

1. Zainstaluj [program Power BI Desktop,](https://powerbi.microsoft.com/desktop/) jeśli jeszcze go nie masz, a następnie otwórz aplikację.
2. Wybierz pozycję Pobierz**kwerendę z pustymi** **danymi,** > aby otworzyć nową kwerendę.  Następnie wybierz pozycję **Zaawansowany edytor** i wklej zawartość eksportowanego pliku do kwerendy. Kliknij przycisk **Gotowe**.

    ![Zapytanie dotyczące programu Power BI dla komputerów stacjonarnych](media/powerbi/desktop-new-query.png)

5. Kwerenda jest uruchamiana, a jej wyniki są wyświetlane.  Może zostać wyświetlony monit o poświadczenia, aby połączyć się z platformą Azure.  
6. Wpisz opisową nazwę kwerendy.  Wartość domyślna to **Query1**. Kliknij **przycisk Zamknij i Zastosuj,** aby dodać zestaw danych do raportu.

    ![Nazwa pulpitu usługi Power BI](media/powerbi/desktop-results.png)



## <a name="publish-to-power-bi"></a>Publikowanie w usłudze Power BI
Podczas publikowania w usłudze Power BI zostanie utworzony zestaw danych i raport.  Jeśli utworzysz raport w programie Power BI Desktop, zostanie on opublikowany wraz z danymi.  Jeśli nie, zostanie utworzony pusty raport.  Można zmodyfikować raport w usłudze Power BI lub utworzyć nowy raport na podstawie zestawu danych.

1. Utwórz raport na podstawie danych.  Jeśli nie znasz [jej dokumentacji programu Power BI Desktop,](https://docs.microsoft.com/power-bi/desktop-report-view) użyj dokumentacji programu Power BI Desktop.  
1. Gdy wszystko będzie gotowe do wysłania go do usługi Power BI, kliknij przycisk **Publikuj**.  
1. Po wyświetleniu monitu wybierz miejsce docelowe na koncie usługi Power BI.  Jeśli nie masz na myśli określonego miejsca docelowego, użyj mojego **obszaru roboczego**.

    ![Publikowanie w programie Power BI Desktop](media/powerbi/desktop-publish.png)

1. Po zakończeniu publikowania kliknij pozycję **Otwórz w usłudze Power BI,** aby otworzyć program Power BI z nowym zestawem danych.


### <a name="configure-scheduled-refresh"></a>Konfigurowanie zaplanowanego odświeżania
Zestaw danych utworzony w usłudze Power BI będzie miał te same dane, które zostały wcześniej dane, które zostały wcześniej dane w programie Power BI Desktop.  Należy okresowo odświeżyć zestaw danych, aby ponownie uruchomić kwerendę i wypełnić ją najnowszymi danymi z usługi Azure Monitor.  

1. Kliknij obszar roboczy, w którym został przekazany raport, i wybierz menu **Zestawy danych.** 
1. Wybierz menu kontekstowe obok nowego zestawu danych i wybierz pozycję **Ustawienia**. 
1. W **obszarze Poświadczenia źródła danych** powinien być wyświetlany komunikat, że poświadczenia są nieprawidłowe.  Jest tak, ponieważ nie podano jeszcze poświadczenia dla zestawu danych do użycia podczas odświeżania jego danych.  
1. Kliknij **pozycję Edytuj poświadczenia** i określ poświadczenia z dostępem do obszaru roboczego usługi Log Analytics w usłudze Azure Monitor. Jeśli potrzebujesz uwierzytelniania dwuskładnikowego, wybierz **OAuth2** dla **metody uwierzytelniania, która** ma zostać wyświetlana monit o zalogowanie się przy użyciu poświadczeń.

    ![Harmonogram usługi Power BI](media/powerbi/powerbi-schedule.png)

5. W obszarze **Zaplanowane odświeżanie** włącz opcję **Aby dane były aktualne**.  Opcjonalnie można zmienić **częstotliwość odświeżania** i co najmniej jeden określony czas, aby uruchomić odświeżanie.

    ![Odświeżanie usługi Power BI](media/powerbi/powerbi-schedule-refresh.png)



## <a name="next-steps"></a>Następne kroki
* Dowiedz się więcej o [wyszukiwaniach w dziennikach](../log-query/log-query-overview.md) w celu tworzenia zapytań, które można wyeksportować do usługi Power BI.
* Dowiedz się więcej o [usłudze Power BI](https://powerbi.microsoft.com) do tworzenia wizualizacji na podstawie eksportu dzienników usługi Azure Monitor.
