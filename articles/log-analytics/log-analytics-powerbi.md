---
title: Importuj dane usługi Azure Log Analytics w usłudze Power BI | Dokumentacja firmy Microsoft
description: Power BI to usługa analizy biznesowej w chmurze firmy Microsoft, która udostępnia zaawansowane wizualizacje i raporty analizy różnych zestawów danych.  W tym artykule opisano sposób konfigurowania i importowania danych usługi Log Analytics w usłudze Power BI i skonfigurować go do automatycznego odświeżania.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: 83edc411-6886-4de1-aadd-33982147b9c3
ms.service: log-analytics
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/19/2018
ms.author: bwren
ms.component: ''
ms.openlocfilehash: 68a2ecc734ab3bbb98d14bef3d30109a2d018781
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/04/2018
ms.locfileid: "52847885"
---
# <a name="import-azure-log-analytics-data-into-power-bi"></a>Importuj dane usługi Azure Log Analytics w usłudze Power BI


[Usługa Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/) to usługa analizy biznesowej w chmurze firmy Microsoft, która udostępnia zaawansowane wizualizacje i raporty analizy różnych zestawów danych.  Wyniki przeszukiwania dzienników usługi Log Analytics można zaimportować do zestawu danych usługi Power BI, dzięki czemu można wykorzystać jej funkcje, takie jak łączenie danych z różnych źródeł oraz udostępnianie raportów w sieci web i urządzeń przenośnych.

## <a name="overview"></a>Przegląd
Aby zaimportować dane z obszaru roboczego usługi Log Analytics w usłudze Power BI, musisz utworzyć zestaw danych w usłudze Power BI oparte na zapytaniu przeszukiwania dzienników w usłudze Log Analytics.  Zapytanie jest uruchamiane w każdym razem, gdy zestaw danych jest odświeżane.  Następnie można tworzyć raporty usługi Power BI, które używają danych z zestawu danych.  Aby utworzyć zestaw danych w usłudze Power BI, wyeksportować zapytanie z usługą Log Analytics do [języku Power Query (M)](https://msdn.microsoft.com/library/mt807488.aspx).  Użyj tego, aby utworzyć zapytanie w programie Power BI Desktop, a następnie opublikować go w usłudze Power BI jako zestaw danych.  Szczegóły tego procesu są opisane poniżej.

![Usługi log Analytics w usłudze Power BI](media/log-analytics-powerbi/overview.png)

## <a name="export-query"></a>Eksportowanie zapytania
Rozpocznij od utworzenia [wyszukiwanie w dzienniku](../azure-monitor/log-query/log-query-overview.md) , zwraca dane z usługi Log Analytics mają do wypełniania zestawu danych usługi Power BI.  Następnie wyeksportować zapytanie do [języku Power Query (M)](https://msdn.microsoft.com/library/mt807488.aspx) których można używać w programie Power BI Desktop.

1. Tworzenie przeszukiwania dzienników w usłudze Log Analytics, aby wyodrębnić dane dla zestawu danych.
2. Jeśli używasz portalu przeszukiwania dzienników, kliknij przycisk **usługi Power BI**.  Jeśli używasz portalu analiza, wybierz **wyeksportować** > **usługi Power BI zapytania (M)**.  Te dwie opcje eksportowania zapytania do pliku tekstowego o nazwie **PowerBIQuery.txt**. 

    ![Wyszukiwanie w dzienniku eksportu](media/log-analytics-powerbi/export-logsearch.png) ![Wyszukiwanie w dzienniku eksportu](media/log-analytics-powerbi/export-analytics.png)

3. Otwórz plik tekstowy, a następnie skopiuj jego zawartość.

## <a name="import-query-into-power-bi-desktop"></a>Importuj zapytanie do usługi Power BI Desktop
Usługa Power BI Desktop to aplikacja komputerowa, która pozwala na tworzenie zestawów danych i raportów, które mogą być publikowane w usłudze Power BI.  Można również użyć do utworzenia zapytania za pomocą dodatku Power Query language, wyeksportowany z usługi Log Analytics. 

1. Zainstaluj [Power BI Desktop](https://powerbi.microsoft.com/desktop/) Jeśli nie jest jeszcze, a następnie otworzyć aplikację.
2. Wybierz **Pobierz dane** > **puste zapytanie** do otwierania kwerendy.  Następnie wybierz pozycję **edytora zaawansowanego** i Wklej zawartość wyeksportowany plik do zapytania. Kliknij przycisk **Gotowe**.

    ![Usługa Power BI Desktop zapytania](media/log-analytics-powerbi/desktop-new-query.png)

5. Zapytanie działa, a jego wyniki są wyświetlane.  Może pojawić się prośba o poświadczenia do połączenia z platformą Azure.  
6. Wpisz nazwę opisową dla zapytania.  Wartość domyślna to **zapytanie1**. Kliknij przycisk **Zamknij i Zastosuj** można dodać zestaw danych do raportu.

    ![Usługa Power BI Desktop nazwy](media/log-analytics-powerbi/desktop-results.png)



## <a name="publish-to-power-bi"></a>Publikowanie w usłudze Power BI
Podczas publikowania do usługi Power BI, zostanie utworzony zestaw danych i raport.  Jeśli utworzysz raport programu Power BI Desktop, następnie ta jest publikowana z danymi.  W przeciwnym razie zostanie utworzony pusty raport.  Można zmodyfikować raport w usłudze Power BI lub utworzyć nowy katalog, w oparciu o zestaw danych.

8. Tworzenie raportu na podstawie danych.  Użyj [dokumentacji usługi Power BI Desktop](https://docs.microsoft.com/power-bi/desktop-report-view) Jeśli nie jesteś zaznajomiony z nim.  Gdy wszystko będzie gotowe do wysłania do usługi Power BI, kliknij przycisk **Publikuj**.  Po wyświetleniu monitu wybierz lokalizację docelową na Twoim koncie usługi Power BI.  Jeśli nie masz określonego miejsca docelowego, pamiętając, użyj **Mój obszar roboczy**.

    ![Publikowanie w usłudze Power BI Desktop](media/log-analytics-powerbi/desktop-publish.png)

3. Po zakończeniu publikowania kliknij **Otwórz w usłudze Power BI** otworzyć usługi Power BI przy użyciu nowego zestawu danych.


### <a name="configure-scheduled-refresh"></a>Konfigurowanie zaplanowanego odświeżania
Zestaw danych utworzony w usłudze Power BI mają te same dane, który wcześniej był wyświetlany w programie Power BI Desktop.  Należy odświeżyć zestaw danych, które okresowo, aby ponownie uruchomić zapytanie i wypełnić ją przy użyciu najnowszych danych z usługi Log Analytics.  

1. Kliknij obszar roboczy, do którego został przekazany, raport i wybierz **zestawów danych** menu. Wybierz menu kontekstowe obok nowego zestawu danych, a następnie wybierz pozycję **ustawienia**. W obszarze **poświadczenia źródła danych** powinny mieć komunikat, że poświadczenia są nieprawidłowe.  Jest to spowodowane nie zostały podane poświadczenia jeszcze dla zestawu danych do użycia podczas odświeżania danych.  Kliknij przycisk **Edytuj poświadczenia** i określić poświadczenia z uprawnieniami do usługi Log Analytics.

    ![Usługa Power BI harmonogramu](media/log-analytics-powerbi/powerbi-schedule.png)

5. W obszarze **zaplanowane odświeżanie** włączyć opcję **aktualne dane**.  Opcjonalnie możesz zmienić **częstotliwość odświeżania** i jeden lub więcej razy określone do uruchomienia odświeżania.

    ![Usługa Power BI odświeżania](media/log-analytics-powerbi/powerbi-schedule-refresh.png)



## <a name="next-steps"></a>Kolejne kroki
* Dowiedz się więcej o [dziennikach](../azure-monitor/log-query/log-query-overview.md) do kompilowania zapytań, które można eksportować do usługi Power BI.
* Dowiedz się więcej o [usługi Power BI](https://powerbi.microsoft.com) do tworzenia wizualizacji w oparciu eksportu usługi Log Analytics.