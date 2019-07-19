---
title: Monitoruj metryki wydajności i magazynu w usłudze Azure Cosmos DB
description: Dowiedz się, jak monitorować swoje konto usługi Azure Cosmos DB dla metryki wydajności, takie jak żądania i błędy serwera i metryk użycia, takich jak użycie magazynu.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: sngun
ms.custom: seodec18
ms.openlocfilehash: 1c12007e04ff8cd08ddb7afdf9a3c2add9638de3
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/16/2019
ms.locfileid: "68277445"
---
# <a name="monitor-performance-and-storage-metrics-in-azure-cosmos-db"></a>Monitoruj metryki wydajności i magazynu w usłudze Azure Cosmos DB

Możesz monitorować kont usługi Azure Cosmos DB w [witryny Azure portal](https://portal.azure.com/). Dla każdego konta usługi Azure Cosmos DB pełny pakiet metryki są dostępne dla monitorowania przepływności, magazynu, dostępności, opóźnienia i spójności.

Metryki można wyświetlić na stronie konta nową stronę metryk lub w usłudze Azure Monitor.

## <a name="view-performance-metrics-on-the-metrics-page"></a>Zobacz metryki wydajności, na stronie Metrics
1. W [witryny Azure portal](https://portal.azure.com/), kliknij przycisk **wszystkich usług**, przewiń do **baz danych**, kliknij przycisk **usługi Azure Cosmos DB**, a następnie kliknij nazwę platformy Azure Konto usługi cosmos DB, dla którego chcesz wyświetlić metryki wydajności.
2. Gdy nowa strona ładuje, w menu zasobów w obszarze **monitorowanie**, kliknij przycisk **metryki**.
3. Po otwarciu strony metryki, zaznacz kolekcję, aby zapoznać się z **próbnego** listy rozwijanej.

   Witryna Azure portal Wyświetla zestaw dostępnych metryk kolekcji. Należy pamiętać, że metryki przepływności, magazynu, dostępności, opóźnienia i spójności znajdują się na osobnych kartach. Aby uzyskać dodatkowe szczegóły dotyczące metryk, pod warunkiem, kliknij przycisk podwójnej strzałki w prawym górnym każdego metryki w okienku po prawej stronie.

   ![Zrzut ekranu przedstawiający soczewkę monitorowania, który pokazuje pakiet metryk](./media/monitor-accounts/metrics-suite.png)

## <a name="view-performance-metrics-by-using-azure-monitoring"></a>Zobacz metryki wydajności za pomocą monitorowania platformy Azure
1. W [witryny Azure portal](https://portal.azure.com/), kliknij przycisk **Monitor** na pasku po lewej stronie.
2. W menu zasobów kliknij pozycję **metryki**.
3. W **monitorowanie — metryki** okna w **grupy zasobów** menu rozwijanym, wybierz grupę zasobów skojarzonych z kontem usługi Azure Cosmos DB, który chcesz monitorować. 
4. W **zasobów** menu rozwijanym, wybierz konto bazy danych, do monitorowania.
5. Na liście **dostępne metryki**, wybierz pozycję metryki do wyświetlenia. Użyj przycisku CTRL wielokrotnego wyboru. 

## <a name="view-performance-metrics-on-the-account-page"></a>Zobacz metryki wydajności na stronie konta
1. W [witryny Azure portal](https://portal.azure.com/), kliknij przycisk **wszystkich usług**, przewiń do **baz danych**, kliknij przycisk **usługi Azure Cosmos DB**, a następnie kliknij nazwę platformy Azure Konto usługi cosmos DB, dla którego chcesz wyświetlić metryki wydajności.
2. **Monitorowanie** obiektywu domyślnie wyświetla następujące Kafelki:
   
   * Łączna liczba żądań dla bieżącego dnia.
   * Magazyn jest używany.
   
   ![Zrzut ekranu przedstawiający soczewkę monitorowania pokazującą żądania i użycie magazynu](./media/monitor-accounts/documentdb-total-requests-and-usage.png)
3. Kliknięcie podwójnej strzałki w prawym górnym rogu **żądań** kafelka otwiera szczegółowe **metryki** strony.
4. **Metryki** strony są wyświetlane szczegóły dotyczące łączna liczba żądań. 

## <a name="set-up-alerts-in-the-portal"></a>Konfigurowanie alertów w portalu

> [!NOTE]
> Klasyczne alerty w Azure Monitor zostaną wycofane 31 sierpnia 2019. Azure Portal oferuje [Narzędzie migracji](../azure-monitor/platform/alerts-using-migration-tool.md) do migracji klasycznych reguł alertów. Nie można jednak migrować wszystkich klasycznych alertów dotyczących metryk Azure Cosmos DB, ale istnieją pewne wyjątki, zobacz artykuł dotyczący [usługi Azure monitor](../azure-monitor/platform/alerts-understand-migration.md#cosmos-db-metrics) , aby uzyskać listę alertów klasycznych, których nie można zmigrować. 

1. W [witryny Azure portal](https://portal.azure.com/), kliknij przycisk **wszystkich usług**, kliknij przycisk **usługi Azure Cosmos DB**, a następnie kliknij nazwę konta usługi Azure Cosmos DB, dla której chcesz skonfigurować wydajności alerty metryki.
2. W menu zasobów kliknij pozycję **reguł alertów** aby otworzyć stronę reguł alertów.  
   ![Zrzut ekranu przedstawiający część reguły alertów](./media/monitor-accounts/madocdb10.5.png)
3. W **reguły alertów** kliknij **Dodaj alert dotyczący**.  
   ![Zrzut ekranu przedstawiający stronę reguł alertów z wyróżnionym przyciskiem Dodaj Alert](./media/monitor-accounts/madocdb11.png)
4. W **Dodawanie reguły alertów** określ:
   
   * Nazwa reguły alertu, które konfigurujesz.
   * Opis nowej reguły alertu.
   * Metryki dla reguły alertu.
   * Warunek, próg i okres, które określają, kiedy aktywuje alertu. Na przykład błąd serwera liczba większa niż 5 w ciągu ostatnich 15 minut.
   * Czy administratora usługi i współadministratorów są wysłane pocztą e-mail po zgłoszeniu alertu.
   * Adresy e-mail dodatkowych powiadomień o alertach.  
     ![Zrzut ekranu przedstawiający stronę Dodawanie reguły alertu](./media/monitor-accounts/madocdb12.png)

## <a name="monitor-azure-cosmos-db-programmatically"></a>Programowe monitorowanie usługi Azure Cosmos DB
Metryki poziomu konta dostępne w portalu, takich jak żądania użycia i łączna liczba kont magazynu, nie są dostępne za pośrednictwem interfejsów API SQL. Jednakże możesz pobrać dane użycia na poziomie kolekcji przy użyciu interfejsów API SQL. Aby pobrać danych na poziomie kolekcji, wykonaj następujące czynności:

* Aby użyć interfejsu API REST [wykonać operację pobierania w kolekcji](https://msdn.microsoft.com/library/mt489073.aspx). Przydział i użycie informacji dla kolekcji jest zwracany w nagłówków x-ms-resource przydziału i x-ms-resource użycia w odpowiedzi.
* Aby użyć zestawu .NET SDK, użyj [DocumentClient.ReadDocumentCollectionAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.readdocumentcollectionasync.aspx) metody, która zwraca [ResourceResponse](https://msdn.microsoft.com/library/dn799209.aspx) zawierający wiele właściwości użycia takich jak  **CollectionSizeUsage**, **DatabaseUsage**, **DocumentUsage**i nie tylko.

Aby uzyskać dostęp do dodatkowych metryk, należy użyć [zestawu SDK usługi Azure Monitor](https://www.nuget.org/packages/Microsoft.Azure.Insights). Dostępne definicje metryk mogą być pobierane przez wywołanie metody:

    https://management.azure.com/subscriptions/{SubscriptionId}/resourceGroups/{ResourceGroup}/providers/Microsoft.DocumentDb/databaseAccounts/{DocumentDBAccountName}/metricDefinitions?api-version=2015-04-08

Zapytania, aby pobrać poszczególne metryki, użyj następującego formatu:

    https://management.azure.com/subscriptions/{SubscriptionId}/resourceGroups/{ResourceGroup}/providers/Microsoft.DocumentDb/databaseAccounts/{DocumentDBAccountName}/metrics?api-version=2015-04-08&$filter=%28name.value%20eq%20%27Total%20Requests%27%29%20and%20timeGrain%20eq%20duration%27PT5M%27%20and%20startTime%20eq%202016-06-03T03%3A26%3A00.0000000Z%20and%20endTime%20eq%202016-06-10T03%3A26%3A00.0000000Z

Aby uzyskać więcej informacji, zobacz [pobierania metryk zasobów za pomocą interfejsu API REST usługi Azure Monitor](https://blogs.msdn.microsoft.com/cloud_solution_architect/2016/02/23/retrieving-resource-metrics-via-the-azure-insights-api/). Należy pamiętać, że zmieniono nazwę "Usługi Azure Insights" "Azure Monitor".  Ten wpis w blogu dotyczy starsze nazwy.

## <a name="next-steps"></a>Kolejne kroki
Aby dowiedzieć się więcej na temat planowania pojemności w usłudze Azure Cosmos DB, zobacz [Kalkulator planista wydajności usługi Azure Cosmos DB](https://www.documentdb.com/capacityplanner).

