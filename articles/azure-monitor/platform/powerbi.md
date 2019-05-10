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
ms.date: 05/01/219
ms.author: bwren
ms.openlocfilehash: 2db6ddf57802f6fcf38cfc3ad7094ed94eaca3d8
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2019
ms.locfileid: "65234201"
---
# <a name="import-azure-monitor-log-data-into-power-bi"></a>Importuj dane dziennika usługi Azure Monitor do usługi Power BI


[Usługa Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/) to usługa analizy biznesowej w chmurze firmy Microsoft, która udostępnia zaawansowane wizualizacje i raporty analizy różnych zestawów danych.  Można zaimportować wyników kwerendy dzienników usługi Azure Monitor do zestawu danych usługi Power BI, dzięki czemu można wykorzystać jej funkcje, takie jak łączenie danych z różnych źródeł oraz udostępnianie raportów w sieci web i urządzeń przenośnych.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="overview"></a>Omówienie
Aby zaimportować dane z [obszaru roboczego usługi Log Analytics](manage-access.md) w usłudze Azure Monitor do usługi Power BI, utworzysz zestaw danych w usłudze Power BI na podstawie [zapytanie dziennika](../log-query/log-query-overview.md) w usłudze Azure Monitor.  Zapytanie jest uruchamiane w każdym razem, gdy zestaw danych jest odświeżane.  Następnie można tworzyć raporty usługi Power BI, które używają danych z zestawu danych.  Aby utworzyć zestaw danych w usłudze Power BI, wyeksportować zapytanie z usługą Log Analytics do [języku Power Query (M)](https://msdn.microsoft.com/library/mt807488.aspx).  Użyj tego, aby utworzyć zapytanie w programie Power BI Desktop, a następnie opublikować go w usłudze Power BI jako zestaw danych.  Szczegóły tego procesu są opisane poniżej.

![Usługi log Analytics w usłudze Power BI](media/powerbi/overview.png)

## <a name="export-query"></a>Eksportowanie zapytania
Rozpocznij od utworzenia [zapytanie dziennika](../log-query/log-query-overview.md) zwracającego dane, które chcesz wypełnić zestaw danych usługi Power BI.  Następnie wyeksportować zapytanie do [języku Power Query (M)](https://msdn.microsoft.com/library/mt807488.aspx) których można używać w programie Power BI Desktop.

1. [Utwórz zapytanie dziennika w usłudze Log Analytics](../log-query/get-started-portal.md) do wyodrębniania danych dla zestawu danych.
2. Wybierz **wyeksportować** > **usługi Power BI zapytania (M)**.  Zapytanie zostanie wyeksportowany do pliku tekstowego o nazwie **PowerBIQuery.txt**. 

    ![Wyszukiwanie w dzienniku eksportu](media/powerbi/export-analytics.png)

3. Otwórz plik tekstowy, a następnie skopiuj jego zawartość.

## <a name="import-query-into-power-bi-desktop"></a>Importuj zapytanie do usługi Power BI Desktop
Usługa Power BI Desktop to aplikacja komputerowa, która pozwala na tworzenie zestawów danych i raportów, które mogą być publikowane w usłudze Power BI.  Można również użyć do utworzenia zapytania za pomocą dodatku Power Query language, wyeksportowany z usługi Azure Monitor. 

1. Zainstaluj [Power BI Desktop](https://powerbi.microsoft.com/desktop/) Jeśli nie jest jeszcze, a następnie otworzyć aplikację.
2. Wybierz **Pobierz dane** > **puste zapytanie** do otwierania kwerendy.  Następnie wybierz pozycję **edytora zaawansowanego** i Wklej zawartość wyeksportowany plik do zapytania. Kliknij przycisk **Gotowe**.

    ![Usługa Power BI Desktop zapytania](media/powerbi/desktop-new-query.png)

5. Zapytanie działa, a jego wyniki są wyświetlane.  Może pojawić się prośba o poświadczenia do połączenia z platformą Azure.  
6. Wpisz nazwę opisową dla zapytania.  Wartość domyślna to **zapytanie1**. Kliknij przycisk **Zamknij i Zastosuj** można dodać zestaw danych do raportu.

    ![Usługa Power BI Desktop nazwy](media/powerbi/desktop-results.png)



## <a name="publish-to-power-bi"></a>Publikowanie w usłudze Power BI
Podczas publikowania do usługi Power BI, zostanie utworzony zestaw danych i raport.  Jeśli utworzysz raport programu Power BI Desktop, następnie ta jest publikowana z danymi.  W przeciwnym razie zostanie utworzony pusty raport.  Można zmodyfikować raport w usłudze Power BI lub utworzyć nowy katalog, w oparciu o zestaw danych.

1. Tworzenie raportu na podstawie danych.  Użyj [dokumentacji usługi Power BI Desktop](https://docs.microsoft.com/power-bi/desktop-report-view) Jeśli nie jesteś zaznajomiony z nim.  
1. Gdy wszystko będzie gotowe do wysłania do usługi Power BI, kliknij przycisk **Publikuj**.  
1. Po wyświetleniu monitu wybierz lokalizację docelową na Twoim koncie usługi Power BI.  Jeśli nie masz określonego miejsca docelowego, pamiętając, użyj **Mój obszar roboczy**.

    ![Publikowanie w usłudze Power BI Desktop](media/powerbi/desktop-publish.png)

1. Po zakończeniu publikowania kliknij **Otwórz w usłudze Power BI** otworzyć usługi Power BI przy użyciu nowego zestawu danych.


### <a name="configure-scheduled-refresh"></a>Konfigurowanie zaplanowanego odświeżania
Zestaw danych utworzony w usłudze Power BI mają te same dane, który wcześniej był wyświetlany w programie Power BI Desktop.  Należy odświeżyć zestaw danych, które okresowo, aby ponownie uruchomić zapytanie i wypełnić ją przy użyciu najnowszych danych z usługi Azure Monitor.  

1. Kliknij obszar roboczy, do którego został przekazany, raport i wybierz **zestawów danych** menu. 
1. Wybierz menu kontekstowe obok nowego zestawu danych, a następnie wybierz pozycję **ustawienia**. 
1. W obszarze **poświadczenia źródła danych** powinny mieć komunikat, że poświadczenia są nieprawidłowe.  Jest to spowodowane nie zostały podane poświadczenia jeszcze dla zestawu danych do użycia podczas odświeżania danych.  
1. Kliknij przycisk **Edytuj poświadczenia** i określić poświadczenia z uprawnieniami do obszaru roboczego usługi Log Analytics w usłudze Azure Monitor. Jeśli wymagasz uwierzytelniania dwuskładnikowego, wybierz **OAuth2** dla **metodę uwierzytelniania** monit, aby zalogować się przy użyciu swoich poświadczeń.

    ![Usługa Power BI harmonogramu](media/powerbi/powerbi-schedule.png)

5. W obszarze **zaplanowane odświeżanie** włączyć opcję **aktualne dane**.  Opcjonalnie możesz zmienić **częstotliwość odświeżania** i jeden lub więcej razy określone do uruchomienia odświeżania.

    ![Usługa Power BI odświeżania](media/powerbi/powerbi-schedule-refresh.png)



## <a name="next-steps"></a>Kolejne kroki
* Dowiedz się więcej o [dziennikach](../log-query/log-query-overview.md) do kompilowania zapytań, które można eksportować do usługi Power BI.
* Dowiedz się więcej o [usługi Power BI](https://powerbi.microsoft.com) do tworzenia wizualizacji w oparciu o usługi Azure Monitor dziennika eksportu.