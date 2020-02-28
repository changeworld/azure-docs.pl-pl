---
title: Monitoruj Azure Cosmos DB z Azure Monitor dla Cosmos DB (wersja zapoznawcza) | Microsoft Docs
description: W tym artykule opisano Azure Monitor funkcji Cosmos DB, która zapewnia Cosmos DB właściciele z szybką wiedzą na temat problemów z wydajnością i wykorzystaniem ich kont CosmosDB.
ms.subservice: ''
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 10/27/2019
ms.openlocfilehash: d28db9907094a651835078f4459a985b9d15e589
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/27/2020
ms.locfileid: "77657388"
---
# <a name="explore-azure-monitor-for-azure-cosmos-db-preview"></a>Eksploruj Azure Monitor dla Azure Cosmos DB (wersja zapoznawcza)

Azure Monitor dla Azure Cosmos DB (wersja zapoznawcza) zawiera widok ogólnej wydajności, niepowodzeń, pojemności i kondycji operacyjnej wszystkich zasobów Azure Cosmos DB w ujednoliconym interaktywnym środowisku. Ten artykuł pomoże Ci zrozumieć korzyści płynące z nowego środowiska monitorowania oraz jak można modyfikować i dostosowywać środowisko, aby odpowiadało unikatowym potrzebom organizacji.   

## <a name="introduction"></a>Wprowadzenie

Przed wprowadzeniem do środowiska należy zrozumieć, jak prezentuje i wizualizuje informacje. 

Zapewnia:

* **Na dużą skalę** zasobów Azure Cosmos DB w ramach wszystkich subskrypcji w jednej lokalizacji, z możliwością selektywnego określania zakresu tylko do subskrypcji i zasobów, które chcesz oceniać.

* **Przechodzenie do szczegółów** konkretnego zasobu usługi Azure CosmosDB w celu ułatwienia zdiagnozowania problemów lub wykonywania szczegółowej analizy według kategorii, niepowodzeń, pojemności i operacji. Wybranie jednej z tych opcji zapewnia szczegółowy widok odpowiednich metryk Azure Cosmos DB.  

* **Dostosowywalne** — to środowisko zostało utworzone na podstawie Azure monitor szablonów skoroszytów, co umożliwia zmianę sposobu wyświetlania metryk, zmodyfikowanie lub ustawienie progów, które są wyrównane z limitami, a następnie zapisywanie w skoroszycie niestandardowym. Wykresy w skoroszytach można następnie przypinać do pulpitów nawigacyjnych platformy Azure.  

Ta funkcja nie wymaga włączenia ani skonfigurowania żadnych elementów, te Azure Cosmos DB metryki są domyślnie zbierane.

>[!NOTE]
>Dostęp do tej funkcji nie jest naliczany, a opłaty są naliczane tylko za Azure Monitor podstawowe funkcje, które konfigurujesz lub włączasz, zgodnie z opisem na stronie [szczegóły cennika Azure monitor](https://azure.microsoft.com/pricing/details/monitor/) .

## <a name="view-operation-level-metrics-for-azure-cosmos-db"></a>Wyświetl metryki poziomu operacji dla Azure Cosmos DB

1. Zaloguj się do [Azure portal](https://portal.azure.com/).

1. Wybierz pozycję **monitor** na pasku nawigacyjnym po lewej stronie, a następnie wybierz pozycję **metryki**.

   ![Okienko metryki w Azure Monitor](./media/cosmosdb-insights-overview/monitor-metrics-blade.png)

1. W okienku **metryki** > **Wybierz zasób** > Wybierz wymaganą **subskrypcję**i **grupę zasobów**. W polu **Typ zasobu**wybierz pozycję **konta Azure Cosmos DB**, wybierz jedno z istniejących kont usługi Azure Cosmos i wybierz pozycję **Zastosuj**.

   ![Wybierz konto Cosmos DB, aby wyświetlić metryki](./media/cosmosdb-insights-overview/select-cosmosdb-account.png)

1. Następnie możesz wybrać metrykę z listy dostępnych metryk. Można wybrać metryki specyficzne dla jednostek żądań, magazynu, opóźnień, dostępności, Cassandra i innych. Aby uzyskać szczegółowe informacje na temat wszystkich dostępnych metryk na tej liście, zobacz artykuł [metryki według kategorii](../../cosmos-db/monitor-cosmos-db-reference.md) . W tym przykładzie wybieramy **jednostki żądania** i **średnika** jako wartość agregacji.

   Oprócz tych szczegółów można także wybrać **zakres czasu** i **stopień szczegółowości** metryk. Co więcej, można wyświetlić metryki z ostatnich 30 dni.  Po zastosowaniu filtru na podstawie filtru zostanie wyświetlony wykres. W wybranym okresie można zobaczyć średnią liczbę jednostek żądań zużytych na minutę.  

   ![Wybierz metrykę z Azure Portal](./media/cosmosdb-insights-overview/metric-types.png)

### <a name="add-filters-to-metrics"></a>Dodawanie filtrów do metryk

Można również filtrować metryki i wykres wyświetlany przez określoną **CollectionName**, **DatabaseName**, **OperationType**, **region**i **StatusCode**. Aby odfiltrować metryki, wybierz pozycję **Dodaj filtr** i wybierz wymaganą właściwość, taką jak **OperationType** , i wybierz wartość, taką jak **zapytanie**. Następnie Wykres wyświetla jednostki żądania wykorzystane dla operacji zapytania w wybranym okresie. Operacje wykonywane za pośrednictwem procedury składowanej nie są rejestrowane, więc nie są dostępne w ramach metryki operacji.

![Dodaj filtr, aby wybrać stopień szczegółowości metryki](./media/cosmosdb-insights-overview/add-metrics-filter.png)

Metryki można grupować przy użyciu opcji **Zastosuj dzielenie** . Na przykład można grupować jednostki żądań na typ operacji i wyświetlać wykres dla wszystkich operacji na raz, jak pokazano na poniższej ilustracji:

![Dodaj filtr podziału zastosowania](./media/cosmosdb-insights-overview/apply-metrics-splitting.png)

## <a name="view-utilization-and-performance-metrics-for-azure-cosmos-db"></a>Wyświetl metryki użycia i wydajności dla Azure Cosmos DB

Aby wyświetlić użycie i wydajność kont magazynu we wszystkich subskrypcjach, wykonaj następujące czynności.

1. Zaloguj się do [Azure portal](https://portal.azure.com).

2. Wyszukaj ciąg **monitor** i wybierz pozycję **Monitoruj**.

    ![Pole wyszukiwania z słowem "Monitor" oraz listą rozwijaną "Monitor" z obrazem stylu prędkościomierza](./media/cosmosdb-insights-overview/search-monitor.png)

3. Wybierz pozycję **Cosmos dB (wersja zapoznawcza)** .

    ![Zrzut ekranu przedstawiający skoroszyt omówienia Cosmos DB](./media/cosmosdb-insights-overview/cosmos-db.png)

### <a name="overview"></a>Omówienie

W obszarze **Przegląd**w tabeli są wyświetlane metryki interakcyjne Azure Cosmos DB. Wyniki można filtrować na podstawie opcji wybranych z następujących list rozwijanych:

* Są wyświetlane subskrypcje obsługujące tylko **subskrypcje** , które mają zasób Azure Cosmos DB.  

* **Cosmos DB** — można wybrać wszystkie, podzestaw lub pojedynczy zasób Azure Cosmos DB.

* **Zakres czasu** — domyślnie wyświetla ostatnie 4 godziny informacji na podstawie odpowiednich opcji.

Kafelek licznika pod listą rozwijaną podsumowuje łączną liczbę zasobów Azure Cosmos DB w wybranych subskrypcjach. Istnieje warunkowe kodowanie kolorami lub map cieplnych dla kolumn w skoroszycie, które raportują metryki transakcji. Optymalny kolor ma najwyższą wartość, a jaśniejszy kolor jest oparty na najniższych wartościach. 

Wybranie strzałki listy rozwijanej obok jednego z zasobów Azure Cosmos DB spowoduje wyświetlenie podziału metryk wydajności na poziomie kontenera poszczególnych baz danych:

![Rozwinięto listę rozwijaną z ujawnianiem poszczególnych kontenerów bazy danych i powiązanego podziału wydajności](./media/cosmosdb-insights-overview/container-view.png)

Wybranie nazwy zasobu Azure Cosmos DB wyróżnionej na niebiesko spowoduje przejście do domyślnego **omówienia** dla skojarzonego konta Azure Cosmos DB. 

### <a name="failures"></a>Błędy

Wybierz pozycję **Błędy** w górnej części strony i zostanie otwarty fragment **błędów** szablonu skoroszytu. Pokazuje ona łączne żądania z rozkładem odpowiedzi składających się na te żądania:

![Zrzut ekranu przedstawiający awarie z podziałem według typu żądania HTTP](./media/cosmosdb-insights-overview/failures.png)

| Kod      |  Opis       | 
|-----------|:--------------------|
| `200 OK`  | Jedna z następujących operacji REST została wykonana pomyślnie: </br>— Pobierz zasób. </br> — Umieść w zasobie. </br> -Publikuj w zasobie. </br> — Opublikuj w zasobie procedury składowanej, aby wykonać procedurę składowaną.|
| `201 Created` | Operacja POST w celu utworzenia zasobu zakończyła się pomyślnie. |
| `404 Not Found` | Operacja podejmuje próbę wykonania operacji na zasobie, który już nie istnieje. Na przykład zasób mógł już zostać usunięty. |

Aby zapoznać się z pełną listą kodów stanu, zapoznaj się z [artykułem kod stanu HTTP Azure Cosmos DB](https://docs.microsoft.com/rest/api/cosmos-db/http-status-codes-for-cosmosdb).

### <a name="capacity"></a>Pojemność

Wybierz pozycję **pojemność** w górnej części strony i zostanie otwarta część **pojemności** szablonu skoroszytu. Przedstawiono w nim liczbę dokumentów, wzrost dokumentu w czasie, użycie danych i łączną ilość dostępnego miejsca do magazynowania.  Może to służyć do identyfikowania potencjalnych problemów z magazynowaniem i wykorzystaniem danych.

![Skoroszyt pojemności](./media/cosmosdb-insights-overview/capacity.png) 

Podobnie jak w przypadku skoroszytu z omówieniem, wybranie listy rozwijanej obok zasobu Azure Cosmos DB w kolumnie **subskrypcja** spowoduje wyświetlenie podziału poszczególnych kontenerów tworzących bazę danych.

### <a name="operations"></a>Operacje 

Wybierz pozycję **operacje** w górnej części strony, a zostanie otwarta część **operacje** szablonu skoroszytu. Daje ona możliwość wyświetlenia żądań, które zostały podzielone według typu żądań. 

Tak więc w poniższym przykładzie widzisz, że `eastus-billingint` jest głównie odbierać żądania odczytu, ale z niewielką liczbą żądań upsert i Create. `westeurope-billingint` jest tylko do odczytu z perspektywy żądania, co najmniej w ciągu ostatnich czterech godzin, do których ten skoroszyt jest obecnie objęty zakresem czasu.

![Skoroszyt operacji](./media/cosmosdb-insights-overview/operation.png) 

## <a name="pin-export-and-expand"></a>Przypnij, Eksportuj i rozwiń

Możesz przypiąć każdą z sekcji metryk do [pulpitu nawigacyjnego platformy Azure](https://docs.microsoft.com/azure/azure-portal/azure-portal-dashboards) , wybierając ikonę pinezki w prawym górnym rogu sekcji.

![Przykład numeru PIN sekcji Metryka na pulpit nawigacyjny](./media/cosmosdb-insights-overview/pin.png)

Aby wyeksportować dane do formatu programu Excel, wybierz ikonę strzałki w dół znajdującą się po lewej stronie ikony pinezki.

![Ikona eksportowania skoroszytu](./media/cosmosdb-insights-overview/export.png)

Aby rozwinąć lub zwinąć wszystkie widoki rozwijane w skoroszycie, wybierz ikonę rozwiń z lewej strony ikony eksportu:

![Rozwiń ikonę skoroszytu](./media/cosmosdb-insights-overview/expand.png)

## <a name="customize-azure-monitor-for-azure-cosmos-db-preview"></a>Dostosowywanie Azure Monitor dla Azure Cosmos DB (wersja zapoznawcza)

Ponieważ to środowisko zostało utworzone na podstawie Azure Monitor szablonów skoroszytów, można **dostosować** > **edytować** i **zapisywać** kopię zmodyfikowanej wersji w skoroszycie niestandardowym. 

![Dostosuj pasek](./media/cosmosdb-insights-overview/customize.png)

Skoroszyty są zapisywane w obrębie grupy zasobów, w sekcji **Moje raporty** prywatnej dla użytkownika lub w sekcji **raporty udostępnione** dostępne dla wszystkich użytkowników mających dostęp do grupy zasobów. Po zapisaniu skoroszytu niestandardowego musisz przejść do galerii skoroszytów, aby go uruchomić.

![Uruchom galerię skoroszytów z poziomu paska poleceń](./media/cosmosdb-insights-overview/gallery.png)

## <a name="next-steps"></a>Następne kroki

* Konfigurowanie [alertów metryk](../platform/alerts-metric.md) i [powiadomień o kondycji usług](../../service-health/alerts-activity-log-service-notifications.md) w celu skonfigurowania zautomatyzowanego alertu w celu uzyskania pomocy w wykrywaniu problemów.

* Dowiedz się, jakie scenariusze skoroszyty są przeznaczone do obsługi, jak tworzyć nowe i dostosowywać istniejące raporty, a inne dzięki przeglądowi [Tworzenie interaktywnych raportów przy użyciu skoroszytów Azure monitor](../app/usage-workbooks.md).
