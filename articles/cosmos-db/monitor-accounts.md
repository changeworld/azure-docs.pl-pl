---
title: Monitorowanie żądań bazy danych Azure rozwiązania Cosmos i magazynu | Dokumentacja firmy Microsoft
description: Dowiedz się, jak monitorować Twoje konto bazy danych Azure rozwiązania Cosmos metryki wydajności, takich jak żądań i błędów serwera i metryki użycia, takie jak wykorzystania magazynu.
services: cosmos-db
documentationcenter: ''
author: mimig1
manager: jhubbard
editor: cgronlun
ms.assetid: 4c6a2e6f-6e78-48e3-8dc6-f4498b235a9e
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2017
ms.author: mimig
ms.openlocfilehash: 0e9a47e6a1a5c7a44c5553742d6c5c81f8ca7286
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2018
---
# <a name="monitor-azure-cosmos-db"></a>Monitorowanie Azure rozwiązania Cosmos bazy danych
Można monitorować swoje konta bazy danych Azure rozwiązania Cosmos w [portalu Azure](https://portal.azure.com/). Dla każdego konta bazy danych Azure rozwiązania Cosmos pełny pakiet metryki jest dostępna do monitorowania przepływności, magazynu, dostępności, opóźnienia i spójności.

Metryki można przeglądać na stronie konta Nowa strona metryki, lub w monitorze Azure.

## <a name="view-performance-metrics-on-the-metrics-page"></a>Metryki wydajności widoku na stronie metryk
1. W [portalu Azure](https://portal.azure.com/), kliknij przycisk **wszystkie usługi**, przewiń do **baz danych**, kliknij przycisk **bazy danych Azure rozwiązania Cosmos**, a następnie kliknij nazwę platformy Azure Konto rozwiązania cosmos bazy danych, dla którego chcesz wyświetlić metryki wydajności.
2. Nowa strona załadowanie, w menu zasobów w obszarze **monitorowanie**, kliknij przycisk **metryki**.
3. Po otwarciu strony metryki wybrać kolekcję, aby zapoznać się z **kolekcji** listy rozwijanej.

   Azure portal zawiera zestaw dostępnych metryk kolekcji. Należy pamiętać, że przepływność, magazynu, dostępności, opóźnienia i spójności metryki są udostępniane na osobnych kartach. Aby uzyskać dodatkowe szczegóły metryki, pod warunkiem, kliknij na podwójną strzałkę w górnym prawym okienku każdego metryki.

   ![Zrzut ekranu przedstawiający obiektyw monitorowanie, pokazujący pakietu metryk](./media/monitor-accounts/metrics-suite.png)

## <a name="view-performance-metrics-by-using-azure-monitoring"></a>Metryki wydajności widoku przy użyciu monitorowania Azure
1. W [portalu Azure](https://portal.azure.com/), kliknij przycisk **Monitor** na pasku po lewej stronie.
2. W menu zasobów kliknij **metryki**.
3. W **Monitor — metryki** okna w **grupy zasobów** menu rozwijanego, wybierz grupę zasobów skojarzone z kontem bazy danych Azure rozwiązania Cosmos, który chcesz monitorować. 
4. W **zasobów** menu rozwijanego, wybierz konto bazy danych do monitorowania.
5. Na liście **dostępne metryki**, wybrać metryki do wyświetlenia. Użyj przycisku CTRL do wielokrotnego wyboru. 

## <a name="view-performance-metrics-on-the-account-page"></a>Metryki wydajności widoku na stronie konta
1. W [portalu Azure](https://portal.azure.com/), kliknij przycisk **wszystkie usługi**, przewiń do **baz danych**, kliknij przycisk **bazy danych Azure rozwiązania Cosmos**, a następnie kliknij nazwę platformy Azure Konto rozwiązania cosmos bazy danych, dla którego chcesz wyświetlić metryki wydajności.
2. **Monitorowanie** obiektyw domyślnie wyświetla następujące Kafelki:
   
   * Całkowita liczba żądań dla bieżącego dnia.
   * Magazyn używany.
   
   ![Zrzut ekranu przedstawiający obiektyw monitorowanie, pokazujący żądania i użycie magazynu](./media/monitor-accounts/documentdb-total-requests-and-usage.png)
3. Kliknięcie podwójną strzałkę w prawym górnym rogu z **żądań** kafelka otwiera szczegółowe **Metryka** strony.
4. **Metryka** strony są wyświetlane szczegóły dotyczące całkowita liczba żądań. 

## <a name="set-up-alerts-in-the-portal"></a>Konfigurowanie alertów w portalu
1. W [portalu Azure](https://portal.azure.com/), kliknij przycisk **wszystkie usługi**, kliknij przycisk **bazy danych Azure rozwiązania Cosmos**, a następnie kliknij nazwę konta Azure DB rozwiązania Cosmos, dla której chcesz skonfigurować wydajności metryki alerty.
2. W menu zasobów kliknij **reguły alertów** aby otworzyć stronę reguł alertów.  
   ![Zrzut ekranu przedstawiający Alert zasady wybrane części](./media/monitor-accounts/madocdb10.5.png)
3. W **reguły alertów** kliknij przycisk **Dodaj alert**.  
   ![Zrzut ekranu przedstawiający stronę reguły alertów, z wyróżnionym przyciskiem Dodaj alertu](./media/monitor-accounts/madocdb11.png)
4. W **dodać regułę alertu** określ:
   
   * Nazwa reguły alertów, które konfigurujesz.
   * Opis nowego alertu.
   * Metryki dla reguły alertów.
   * Stan, próg i okres określające, kiedy aktywuje alert. Na przykład błąd serwera liczba większa niż 5 w ciągu ostatnich 15 minut.
   * Określa, czy administrator usługi i coadministrators pocztą e-mail po zgłoszeniu alertu.
   * Adresy e-mail dodatkowych powiadomień o alertach.  
     ![Zrzut ekranu: Dodaj stronę reguły alertu](./media/monitor-accounts/madocdb12.png)

## <a name="monitor-azure-cosmos-db-programmatically"></a>Monitorowanie bazy danych Azure rozwiązania Cosmos programowo
Metryki poziomu konta dostępne w portalu, takich jak żądania użycia i całkowitej konta magazynu, nie są dostępne za pośrednictwem interfejsów API SQL. Jednak można pobrać dane użycia na poziomie kolekcji za pomocą interfejsów API SQL. Aby pobrać danych kolekcji na poziomie, wykonaj następujące czynności:

* Aby użyć interfejsu API REST, [wykonać operację pobierania na kolekcji](https://msdn.microsoft.com/library/mt489073.aspx). Limit przydziału i użycie informacji dla kolekcji, jest zwracana w nagłówkach x-ms-resource przydziału i x-ms-resource użycia w odpowiedzi.
* Aby użyć zestawu .NET SDK, użyj [DocumentClient.ReadDocumentCollectionAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.readdocumentcollectionasync.aspx) metody, która zwraca [ResourceResponse](https://msdn.microsoft.com/library/dn799209.aspx) takich jak zawiera wiele właściwości użycia **CollectionSizeUsage**, **DatabaseUsage**, **DocumentUsage**itd.

Aby uzyskać dostęp do dodatkowych metryk, użyj [zestawu SDK platformy Azure Monitor](https://www.nuget.org/packages/Microsoft.Azure.Insights). Dostępne metryki definicje można pobranej poprzez wywołanie:

    https://management.azure.com/subscriptions/{SubscriptionId}/resourceGroups/{ResourceGroup}/providers/Microsoft.DocumentDb/databaseAccounts/{DocumentDBAccountName}/metricDefinitions?api-version=2015-04-08

Zapytania można pobrać metryki poszczególnych Użyj następującego formatu:

    https://management.azure.com/subscriptions/{SubecriptionId}/resourceGroups/{ResourceGroup}/providers/Microsoft.DocumentDb/databaseAccounts/{DocumentDBAccountName}/metrics?api-version=2015-04-08&$filter=%28name.value%20eq%20%27Total%20Requests%27%29%20and%20timeGrain%20eq%20duration%27PT5M%27%20and%20startTime%20eq%202016-06-03T03%3A26%3A00.0000000Z%20and%20endTime%20eq%202016-06-10T03%3A26%3A00.0000000Z

Aby uzyskać więcej informacji, zobacz [pobieranie zasobu metryki za pośrednictwem interfejsu API REST Azure Monitor](https://blogs.msdn.microsoft.com/cloud_solution_architect/2016/02/23/retrieving-resource-metrics-via-the-azure-insights-api/). Należy pamiętać, że zmieniono nazwę "Azure Insights" "Azure Monitor".  Ten wpis w blogu odwołuje się do nazwy starszej.

## <a name="next-steps"></a>Kolejne kroki
Aby dowiedzieć się więcej na temat planowania wydajności bazy danych rozwiązania Cosmos Azure, zobacz [Kalkulator planowania pojemności bazy danych Azure rozwiązania Cosmos](https://www.documentdb.com/capacityplanner).

