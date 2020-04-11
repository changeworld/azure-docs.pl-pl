---
title: Monitorowanie użycia przepływności operacji w usłudze Azure Cosmos DB
description: Dowiedz się, jak monitorować przepływność lub żądanie użycia jednostki operacji w usłudze Azure Cosmos DB. Właściciele konta usługi Azure Cosmos DB mogą zrozumieć, które operacje przyjmują więcej jednostek żądania.
ms.service: cosmos-db
ms.topic: conceptual
author: kanshiG
ms.author: govindk
ms.date: 04/09/2020
ms.openlocfilehash: bc39ef199a5d40d3eaa75023277d3e00b93e131a
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/10/2020
ms.locfileid: "81115432"
---
# <a name="how-to-monitor-throughput-or-request-unit-usage-of-an-operation-in-azure-cosmos-db"></a>Jak monitorować przepływność lub żądanie użycia jednostki operacji w usłudze Azure Cosmos DB

Usługa Azure Monitor for Azure Cosmos DB udostępnia widok metryki do monitorowania konta i tworzenia pulpitów nawigacyjnych. Metryki usługi Azure Cosmos DB są zbierane domyślnie, ta funkcja nie wymaga, aby włączyć lub skonfigurować coś jawnie. **Metryka Całkowita liczba jednostek żądań** jest używana do uzyskania użycia jednostek żądań dla różnych typów operacji. Później można analizować, które operacje używane większość przepływności. Domyślnie dane przepływności są agregowane w odstępach jednominutowych. Można jednak zmienić jednostkę agregacji, zmieniając opcję szczegółowości czasu.

Istnieją dwa sposoby analizowania danych użycia jednostki żądania:

* W danym przedziale czasowym, które operacje przyjmują więcej jednostek żądań.
* Które operacje w ogóle dominują obciążenia przez zużywanie więcej jednostek żądania.
Ta analiza pozwala skupić się na operacjach, takich jak wstawianie, upsert i przyjrzeć się ich indeksowania. Możesz dowiedzieć się, czy jesteś ponad/poniżej indeksowania określonych pól i zmodyfikować [zasady indeksowania,](index-policy.md#include-exclude-paths) aby uwzględnić lub wykluczyć ścieżki.

Jeśli zauważysz, że niektóre zapytania przyjmują więcej jednostek żądań, możesz podjąć działania, takie jak:

* Jeśli żądasz odpowiedniej ilości danych, ponownie rozważ, jeśli poprosisz o odpowiednią ilość danych.
* Zmodyfikuj kwerendę, aby użyć indeksu z klauzulą filtru.
* Wykonywanie tańszych wywołań funkcji UDF.
* Zdefiniuj klucze partycji, aby zminimalizować brak zapytania do różnych partycji.
* Można również użyć metryki kwerendy zwrócone w odpowiedzi na wezwanie, szczegóły dziennika diagnostycznego i odnoszą się do [dostrajania wydajności kwerendy](sql-api-query-metrics.md) artykułu, aby dowiedzieć się więcej na temat wykonywania kwerendy.
* Można rozpocząć od sumy, a następnie spojrzeć na avg wykorzystania przy użyciu odpowiedniego wymiaru.

## <a name="view-the-total-request-unit-usage-metric"></a>Wyświetlanie metryki całkowitego użycia jednostki żądania

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

1. Wybierz **pozycję Monitor** z lewego paska nawigacyjnego i wybierz pozycję **Metryki**.

   ![Okienko Metryki w usłudze Azure Monitor](./media/monitor-request-unit-usage/monitor-metrics-blade.png)

1. W okienku **Metryki** > **Wybierz zasób** > wybierz wymaganą **subskrypcję**i **grupę zasobów**. W przypadku **typu zasobu**wybierz pozycję **Konta usługi Azure Cosmos DB**, wybierz jedno z istniejących kont usługi Azure Cosmos i wybierz pozycję **Zastosuj**.

   ![Wybierz konto usługi Azure Cosmos DB, aby wyświetlić metryki](./media/monitor-request-unit-usage/select-cosmos-db-account.png)

1. Następnie wybierz metrykę **Łączna liczba jednostek żądań** z listy dostępnych danych. Aby dowiedzieć się szczegółowo o wszystkich dostępnych danych na tej liście, zobacz [Metryki według](monitor-cosmos-db-reference.md) kategorii artykułu. W tym przykładzie wybierzmy **opcję Całkowita liczba jednostek żądań** i **Średnia** jako wartość agregacji. Oprócz tych szczegółów można również wybrać **zakres czasu** i **szczegółowość czasu** metryk. Maksymalnie możesz wyświetlać dane z ostatnich 30 dni.  Po zastosowaniu filtru wykres jest wyświetlany na podstawie filtru. Można zobaczyć średnią liczbę jednostek żądań zużytych na minutę dla wybranego okresu.  

   ![Wybieranie metryki z witryny Azure portal](./media/monitor-request-unit-usage/request-unit-usage-metric.png)

## <a name="filters-for-request-unit-usage"></a>Filtry do użycia jednostki żądania

Można również filtrować dane i uzyskać wykresy wyświetlane przez określony **Nazwa Kolekcji**, **Nazwa bazy danych**, **OperationType**, **Region**, **Status**i **StatusCode**. Opcje **Dodawania filtru** i **Rozlicz podziały** umożliwiają filtrowanie użycia jednostki żądania i grupowanie metryk.

Aby uzyskać użycie jednostki żądania dla każdej operacji według sumy(sumy) lub średniej, wybierz opcję **Zastosuj dzielenie** i wybierz **typ operacji** oraz wartość filtru, jak pokazano na poniższej ilustracji:

   ![Jednostki żądania usługi Cosmos DB dla operacji w monitorze platformy Azure](./media/monitor-request-unit-usage/request-unit-usage-operations.png)

Jeśli chcesz zobaczyć użycie jednostki żądania według kolekcji, wybierz **Zastosuj dzielenie** i wybierz nazwę kolekcji jako filtr. Zobaczysz czat podobny do następującego z wyborem kolekcji na pulpicie nawigacyjnym. Następnie można wybrać określoną nazwę kolekcji, aby wyświetlić więcej szczegółów:

   ![Jednostki żądania usługi Cosmos DB dla wszystkich operacji przez kolekcję na monitorze platformy Azure](./media/monitor-request-unit-usage/request-unit-usage-collection.png)

## <a name="next-steps"></a>Następne kroki

* Monitoruj dane usługi Azure Cosmos DB przy użyciu [ustawień diagnostycznych](cosmosdb-monitor-resource-logs.md) na platformie Azure.
* [Inspekcja operacji płaszczyzny sterowania usługi Azure Cosmos DB](audit-control-plane-logs.md)