---
title: Jak monitorować opóźnienie po stronie serwera dla operacji w usłudze Azure Cosmos DB
description: Dowiedz się, jak monitorować opóźnienia serwera dla operacji na koncie usługi Azure Cosmos DB lub kontenerze. Właściciele konta usługi Azure Cosmos DB mogą zrozumieć problemy z opóźnieniami po stronie serwera z kontami usługi Azure Cosmos.
ms.service: cosmos-db
ms.topic: conceptual
author: kanshiG
ms.author: govindk
ms.date: 04/07/2020
ms.openlocfilehash: 38f5778e363a16117bab482a758666ec81352afd
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80887569"
---
# <a name="how-to-monitor-the-server-side-latency-for-operations-in-an-azure-cosmos-db-container-or-account"></a>Jak monitorować opóźnienie po stronie serwera pod kątem operacji w kontenerze lub koncie usługi Azure Cosmos DB

Usługa Azure Monitor for Azure Cosmos DB udostępnia widok metryki do monitorowania konta i tworzenia pulpitów nawigacyjnych. Metryki usługi Azure Cosmos DB są zbierane domyślnie, ta funkcja nie wymaga, aby włączyć lub skonfigurować coś jawnie. Metryka opóźnienia po stronie serwera służy do wyświetlania opóźnienia po stronie serwera operacji. Usługa Azure Cosmos DB zapewnia umowy SLA o masie mniejszej niż 10 ms dla operacji odczytu/zapisu punktowego z bezpośrednią łącznością. W przypadku operacji odczytu i zapisu punktowego umowy SLA są obliczane zgodnie z opisem w [dokumencie umowy SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_3/).

Jeśli widzisz niezwykle duże opóźnienie dla operacji punktowych, takich jak:

* Operacja get lub set z kluczem partycji i identyfikatorem w trybie bezpośrednim
* Operacja odczytu lub zapisu lub
* Kwerenda

Można wyszukać dziennik diagnostyczny, aby zobaczyć rozmiar zwracanych danych. Jeśli widzisz trwałe duże opóźnienie dla operacji kwerendy, można wyszukać dziennik diagnostyczny dla rozmiaru zwracanych danych, [przepływności lub RU/s](cosmosdb-monitor-resource-logs.md#diagnostic-queries) używane lub liczba takich operacji w danym okresie. W ten sposób można debugować problemy z opóźnieniem po stronie serwera.

## <a name="view-server-side-latency-metric"></a>Wyświetlanie metryki opóźnienia po stronie serwera

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

1. Wybierz **pozycję Monitor** z lewego paska nawigacyjnego i wybierz pozycję **Metryki**.

   ![Okienko Metryki w usłudze Azure Monitor](./media/monitor-server-side-latency/monitor-metrics-blade.png)

1. W okienku **Metryki** > **Wybierz zasób** > wybierz wymaganą **subskrypcję**i **grupę zasobów**. W przypadku **typu zasobu**wybierz pozycję **Konta usługi Azure Cosmos DB**, wybierz jedno z istniejących kont usługi Azure Cosmos i wybierz pozycję **Zastosuj**.
   
   ![Wybieranie konta usługi Cosmos DB w celu wyświetlenia danych](./media/monitor-server-side-latency/select-cosmosdb-account.png)

1. Następnie wybierz metrykę **opóźnienie strony serwera** z listy dostępnych metryk. Aby dowiedzieć się szczegółowo o wszystkich dostępnych danych na tej liście, zobacz [Metryki według](monitor-cosmos-db-reference.md) kategorii artykułu. W tym przykładzie wybierzmy opóźnienie **po stronie serwera** i **Średnia** jako wartość agregacji. Oprócz tych szczegółów można również wybrać **zakres czasu** i **szczegółowość czasu** metryk. Maksymalnie możesz wyświetlać dane z ostatnich 30 dni.  Po zastosowaniu filtru wykres jest wyświetlany na podstawie filtru. Można zobaczyć średnią liczbę jednostek żądań zużytych na minutę dla wybranego okresu.  

   ![Wybierz metrykę opóźnienie po stronie serwera z witryny Azure portal](./media/monitor-server-side-latency/serverside-latency-metric.png)

## <a name="filters-for-server-side-latency"></a>Filtry dla opóźnienia po stronie serwera

Można również filtrować metryki i wykres wyświetlany przez określony **Nazwa kolekcji**, **ConnectionMode**, **DatabaseName**, **OperationType**, **Region**i **PublicAPIType**. 

Aby filtrować metryki, wybierz **dodaj filtr** i wybierz wymaganą właściwość, taką jak **PublicAPIType** i wybierz wartość **sql**. Dodaj kolejny filtr dla **OperationType**. Wykres następnie wyświetla opóźnienie po stronie serwera dla różnych operacji w wybranym okresie. Operacje wykonywane za pośrednictwem procedury składowanej nie są rejestrowane, więc nie są dostępne w ramach OperationType metryki.

Metryki **opóźnienia po stronie serwera** dla każdej operacji są wyświetlane w sposób pokazany na poniższej ilustracji:

![Filtry dla metryk opóźnienia po stronie serwera](./media/monitor-server-side-latency/serverside-latency-filters.png)

Metryki można również grupować za pomocą opcji **Zastosuj podział.**  

## <a name="next-steps"></a>Następne kroki

* Monitorowanie danych usługi Azure Cosmos DB przy użyciu [ustawień diagnostycznych](cosmosdb-monitor-resource-logs.md) na platformie Azure
* [Inspekcja operacji płaszczyzny sterowania usługi Azure Cosmos DB](audit-control-plane-logs.md)