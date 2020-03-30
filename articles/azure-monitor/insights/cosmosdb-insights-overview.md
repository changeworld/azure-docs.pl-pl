---
title: Monitoruj usługę Azure Cosmos DB za pomocą usługi Azure Monitor for Cosmos DB (wersja zapoznawcza)| Dokumenty firmy Microsoft
description: W tym artykule opisano funkcję usługi Azure Monitor for Cosmos DB, która zapewnia właścicielom usługi Cosmos DB szybkie zrozumienie problemów z wydajnością i wykorzystaniem ich kont usługi CosmosDB.
ms.subservice: ''
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 10/27/2019
ms.openlocfilehash: 9a900a2f2e950fe9b9846ebcc047d7c344284948
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78250676"
---
# <a name="explore-azure-monitor-for-azure-cosmos-db-preview"></a>Poznaj usługę Azure Monitor dla usługi Azure Cosmos DB (wersja zapoznawcza)

Usługa Azure Monitor for Azure Cosmos DB (wersja zapoznawcza) zapewnia widok ogólnej wydajności, awarii, pojemności i kondycji operacyjnej wszystkich zasobów usługi Azure Cosmos DB w ujednoliconym interaktywnym środowiskach. Ten artykuł pomoże Ci zrozumieć korzyści płynące z tego nowego środowiska monitorowania oraz jak można modyfikować i dostosowywać środowisko do unikatowych potrzeb organizacji.   

## <a name="introduction"></a>Wprowadzenie

Przed zagłębieniem się w doświadczenie, należy zrozumieć, jak prezentuje i wizualizuje informacje. 

Zapewnia:

* **W perspektywie skali** zasobów usługi Azure Cosmos DB we wszystkich subskrypcjach w jednej lokalizacji, z możliwością selektywnego zakresu tylko do tych subskrypcji i zasobów, które są zainteresowane oceną.

* **Szczegółowe omówienie** określonego zasobu usługi Azure CosmosDB w celu zdiagnozowania problemów lub przeprowadzenia szczegółowej analizy według kategorii — wykorzystania, awarii, pojemności i operacji. Wybranie jednej z tych opcji zapewnia szczegółowy widok odpowiednich metryk usługi Azure Cosmos DB.  

* **Konfigurowalne** — to środowisko jest oparte na szablonach skoroszytów usługi Azure Monitor, co pozwala zmienić wyświetlane metryki, zmodyfikować lub ustawić progi, które są zgodne z limitami, a następnie zapisać w niestandardowym skoroszycie. Wykresy w skoroszytach można następnie przypiąć do pulpitów nawigacyjnych platformy Azure.  

Ta funkcja nie wymaga włączania lub konfigurowania czegokolwiek, te metryki usługi Azure Cosmos DB są zbierane domyślnie.

>[!NOTE]
>Dostęp do tej funkcji nie podlega żadnym opłatom, a opłaty będą naliczane tylko za podstawowe funkcje usługi Azure Monitor, które konfigurujesz lub włączasz, zgodnie z opisem na stronie [szczegółów cen usługi Azure Monitor.](https://azure.microsoft.com/pricing/details/monitor/)

## <a name="view-utilization-and-performance-metrics-for-azure-cosmos-db"></a>Wyświetlanie metryk wykorzystania i wydajności dla usługi Azure Cosmos DB

Aby wyświetlić wykorzystanie i wydajność kont magazynu we wszystkich subskrypcjach, wykonaj następujące kroki.

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).

2. Wyszukaj **monitor** i wybierz pozycję **Monitor**.

    ![Pole wyszukiwania ze słowem "Monitor" i rozwijanym z napisem Usługi "Monitor" z obrazem w stylu prędkościomierza](./media/cosmosdb-insights-overview/search-monitor.png)

3. Wybierz **Cosmos DB (wersja zapoznawcza)**.

    ![Zrzut ekranu przedstawiający skoroszyt omówienia usługi Cosmos DB](./media/cosmosdb-insights-overview/cosmos-db.png)

### <a name="overview"></a>Omówienie

W **przeglądzie**w tabeli są wyświetlane interaktywne metryki usługi Azure Cosmos DB. Wyniki można filtrować na podstawie opcji wybranych z następujących list rozwijanych:

* **Subskrypcje** — wyświetlane są tylko subskrypcje, które mają zasób usługi Azure Cosmos DB.  

* **Usługi Cosmos DB** — można wybrać wszystkie, podzbiór lub pojedynczy zasób usługi Azure Cosmos DB.

* **Zakres czasu** — domyślnie wyświetlane są informacje z ostatnich 4 godzin na podstawie odpowiednich dokonanych wyborów.

Kafelek licznika w obszarze list rozwijanych rolkach w górę całkowita liczba zasobów usługi Azure Cosmos DB znajdują się w wybranych subskrypcji. Istnieje warunkowe kodowanie kolorów lub mapy cieplne dla kolumn w skoroszycie, które zgłaszają metryki transakcji. Najgłębszy kolor ma najwyższą wartość, a jaśniejszy kolor jest oparty na najniższych wartościach. 

Wybranie strzałki rozwijanej obok jednego z zasobów usługi Azure Cosmos DB spowoduje wyświetlinie podziału metryk wydajności na poziomie kontenera poszczególnych baz danych:

![Rozszerzona rozwijana aktualizacja ujawniająca poszczególne kontenery baz danych i powiązany podział wydajności](./media/cosmosdb-insights-overview/container-view.png)

Wybranie nazwy zasobu usługi Azure Cosmos DB wyróżnionej na niebiesko spowoduje, że przejdziesz do domyślnego **przeglądu** skojarzonego konta usługi Azure Cosmos DB. 

### <a name="failures"></a>Błędy

Wybierz **pozycję Błędy** u góry strony i zostanie otwarta część **Awarie** szablonu skoroszytu. Pokazuje całkowitą liczbę żądań z dystrybucją odpowiedzi, które składają się na te żądania:

![Zrzut ekranu przedstawiający błędy z podziałem według typu żądania HTTP](./media/cosmosdb-insights-overview/failures.png)

| Code      |  Opis       | 
|-----------|:--------------------|
| `200 OK`  | Jedna z następujących operacji REST zakończyła się pomyślnie: </br>- GET na zasób. </br> - UMIEŚCIĆ na zasobie. </br> - POST na zasób. </br> - POST na zasób procedury składowanej do wykonania procedury składowanej.|
| `201 Created` | Operacja POST w celu utworzenia zasobu zakończy się pomyślnie. |
| `404 Not Found` | Operacja próbuje działać na zasób, który już nie istnieje. Na przykład zasób mógł już zostać usunięty. |

Aby uzyskać pełną listę kodów stanu, zapoznaj się z [artykułem kodu stanu HTTP usługi Azure Cosmos DB](https://docs.microsoft.com/rest/api/cosmos-db/http-status-codes-for-cosmosdb).

### <a name="capacity"></a>Pojemność

Wybierz **pozycję Pojemność** u góry strony i zostanie otwarta część **Pojemność** szablonu skoroszytu. Pokazuje, ile masz dokumentów, wzrost dokumentu w czasie, użycie danych i całkowitą ilość dostępnego magazynu, który opuściłeś.  Może to służyć do identyfikowania potencjalnych problemów z magazynowaniem i wykorzystaniem danych.

![Skoroszyt pojemności](./media/cosmosdb-insights-overview/capacity.png) 

Podobnie jak w przypadku skoroszytu przeglądu, wybranie listy rozwijanej obok zasobu usługi Azure Cosmos DB w kolumnie **Subskrypcja** spowoduje udostępnienie podziału według poszczególnych kontenerów, które tworzą bazę danych.

### <a name="operations"></a>Operacje 

Wybierz **pozycję Operacje** u góry strony i zostanie otwarta część **Operacje** szablonu skoroszytu. Daje możliwość zobaczenia twoich żądań w podziale według typu żądań. 

Tak więc w poniższym `eastus-billingint` przykładzie widać, że jest głównie odbieranie żądań odczytu, ale z niewielką liczbą upsert i tworzenie żądań. Mając `westeurope-billingint` na uwadze, że jest tylko do odczytu z punktu widzenia żądania, co najmniej w ciągu ostatnich czterech godzin, że skoroszyt jest obecnie zakres za pośrednictwem jego parametr zakresu czasu.

![Skoroszyt operacji](./media/cosmosdb-insights-overview/operation.png) 

## <a name="pin-export-and-expand"></a>Przypinanie, eksportowanie i rozwijanie

Możesz przypiąć dowolną sekcję metryki do [pulpitu nawigacyjnego platformy Azure,](https://docs.microsoft.com/azure/azure-portal/azure-portal-dashboards) wybierając ikonę pinezki w prawym górnym rogu sekcji.

![Przykład numeru pin sekcji metryki do pulpitu nawigacyjnego](./media/cosmosdb-insights-overview/pin.png)

Aby wyeksportować dane do formatu programu Excel, wybierz ikonę strzałki w dół po lewej stronie ikony pinezy.

![Ikona Eksportuj skoroszyt](./media/cosmosdb-insights-overview/export.png)

Aby rozwinąć lub zwinąć wszystkie widoki rozwijane w skoroszycie, zaznacz ikonę rozwijania po lewej stronie ikony eksportu:

![Ikona Rozwiń skoroszyt](./media/cosmosdb-insights-overview/expand.png)

## <a name="customize-azure-monitor-for-azure-cosmos-db-preview"></a>Dostosowywanie usługi Azure Monitor dla usługi Azure Cosmos DB (wersja zapoznawcza)

Ponieważ to środowisko jest oparte na szablonach skoroszytów usługi Azure Monitor, masz możliwość **dostosowywania** > **edycji** i **zapisywania** kopii zmodyfikowanej wersji w skoroszycie niestandardowym. 

![Dostosowywanie paska](./media/cosmosdb-insights-overview/customize.png)

Skoroszyty są zapisywane w grupie zasobów w sekcji **Moje raporty,** która jest prywatna dla Ciebie, lub w sekcji **Raporty udostępnione,** dostępnej dla wszystkich osób z dostępem do grupy zasobów. Po zapisaniu niestandardowego skoroszytu należy przejść do galerii skoroszytu, aby go uruchomić.

![Uruchamianie galerii skoroszytu na pasku poleceń](./media/cosmosdb-insights-overview/gallery.png)

## <a name="next-steps"></a>Następne kroki

* Skonfiguruj [alerty metryk](../platform/alerts-metric.md) i [powiadomienia o kondycji usługi,](../../service-health/alerts-activity-log-service-notifications.md) aby skonfigurować automatyczne alerty ułatwiające wykrywanie problemów.

* Dowiedz się, jakie scenariusze skoroszyty są przeznaczone do obsługi, tworzenia nowych i dostosowywania istniejących raportów oraz nie tylko, przeglądając [tworzenie interaktywnych raportów za pomocą skoroszytów usługi Azure Monitor](../app/usage-workbooks.md).
