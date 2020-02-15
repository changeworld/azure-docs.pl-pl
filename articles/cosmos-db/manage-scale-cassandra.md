---
title: Elastyczne skalowanie przy użyciu interfejs API Cassandra w Azure Cosmos DB
description: Dowiedz się więcej o opcjach dostępnych do skalowania Azure Cosmos DB konta interfejs API Cassandra i ich zalety/wady
author: TheovanKraay
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 01/13/2020
ms.author: thvankra
ms.openlocfilehash: 668e9ddadf151a86be0d8c09fc91b4c70db12f3a
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2020
ms.locfileid: "77210790"
---
# <a name="elastically-scale-an-azure-cosmos-db-cassandra-api-account"></a>Elastyczne skalowanie Azure Cosmos DB interfejs API Cassandra konta

Istnieje wiele opcji umożliwiających Eksplorowanie elastycznego charakteru interfejsu API Azure Cosmos DB Cassandra. Aby zrozumieć, jak efektywnie skalować w Azure Cosmos DB, ważne jest, aby zrozumieć, jak zapewnić odpowiednią ilość jednostek żądań (RU/s) w celu uwzględnienia wymagań dotyczących wydajności w systemie. Aby dowiedzieć się więcej na temat jednostek żądania, zobacz artykuł dotyczący [jednostek](request-units.md) żądania. 

![Operacje bazy danych zużywają jednostki żądań](./media/request-units/request-units.png)

## <a name="handling-rate-limiting-429-errors"></a>Ograniczanie szybkości obsługi (błędy 429)

W przypadku, gdy klienci zużywają więcej zasobów (RU/s) niż ilość, która została zainicjowana, Azure Cosmos DB będą zwracać błędy o szybkości ograniczonej (429). Interfejs API Cassandra w Azure Cosmos DB tłumaczy te wyjątki na przeciążone błędy w protokole natywnym Cassandra. 

Jeśli system nie jest wrażliwy na opóźnienia, może być wystarczające do obsłużenia ograniczenia przepływności przy użyciu ponownych prób. Zobacz [przykład kodu Java](https://github.com/Azure-Samples/azure-cosmos-cassandra-java-retry-sample) , aby dowiedzieć się, jak obsługiwać ograniczanie szybkości w sposób niewidoczny przy użyciu [rozszerzenia Azure Cosmos DB](https://github.com/Azure/azure-cosmos-cassandra-extensions) dla [zasad ponawiania Cassandra](https://docs.datastax.com/drivers/java/2.0/com/datastax/driver/core/policies/RetryPolicy.html) w języku Java. Można również użyć [rozszerzenia Spark](https://mvnrepository.com/artifact/com.microsoft.azure.cosmosdb/azure-cosmos-cassandra-spark-helper) , aby obsłużyć ograniczenie szybkości.

## <a name="manage-scaling"></a>Zarządzanie skalowaniem

Jeśli zachodzi potrzeba zminimalizowania opóźnień, istnieje szeroki zakres opcji zarządzania przepływem i skalowaniem (jednostek ru) w interfejs API Cassandra:

* [Ręczne przy użyciu Azure Portal](#use-azure-portal)
* [Programowo przy użyciu funkcji płaszczyzny kontroli](#use-control-plane)
* [Programowo przy użyciu poleceń CQL z określonym zestawem SDK](#use-cql-queries)
* [Dynamicznie przy użyciu funkcji autopilotaż](#use-autopilot)

W poniższych sekcjach wyjaśniono zalety i wady poszczególnych rozwiązań. Następnie można określić najlepszą strategię, aby zrównoważyć wymagania dotyczące skalowania systemu, łączny koszt oraz potrzeby związane z wydajnością rozwiązania.

## <a id="use-azure-portal"></a>Użyj Azure Portal

Możesz skalować zasoby w Azure Cosmos DB interfejs API Cassandra koncie przy użyciu Azure Portal. Aby dowiedzieć się więcej, zobacz artykuł dotyczący [obsługi przepływności na kontenerach i bazach danych](set-throughput.md). W tym artykule wyjaśniono względne zalety ustawiania przepływności na poziomie [bazy danych](set-throughput.md#set-throughput-on-a-database) lub [kontenera](set-throughput.md#set-throughput-on-a-container) w Azure Portal. Warunki "Database" i "Container" wymienione w tych artykułach są mapowane na "przestrzeń kluczy" i "Tabela" odpowiednio dla interfejs API Cassandra.

Zaletą tej metody jest to, że jest to prosty gotowe sposób zarządzania wydajnością przepływności w bazie danych. Jednak Wadą jest to, że w wielu przypadkach podejście do skalowania może wymagać, aby niektóre poziomy automatyzacji były opłacalne i wysokie. W następnych sekcjach opisano odpowiednie scenariusze i metody.

## <a id="use-control-plane"></a>Korzystanie z płaszczyzny kontroli

Interfejs API Azure Cosmos DB dla Cassandra umożliwia programistyczne Dostosowywanie przepływności przy użyciu różnych funkcji płaszczyzny kontroli. Zobacz artykuły dotyczące [Azure Resource Manager](manage-cassandra-with-resource-manager.md), [programu PowerShell](powershell-samples-cassandra.md)i [interfejsu wiersza polecenia platformy Azure](cli-samples-cassandra.md) , aby uzyskać wskazówki i przykłady.

Zaletą tej metody jest Automatyzacja skalowania w górę lub w dół zasobów na podstawie czasomierza w celu uwzględnienia aktywności szczytowej lub okresów niskiej aktywności. Zapoznaj się z naszym przykładem, aby dowiesz się, jak to [zrobić za pomocą](https://github.com/Azure-Samples/azure-cosmos-throughput-scheduler) Azure Functions i programu PowerShell.

Wadą tego podejścia może być, że nie można odpowiedzieć na nieprzewidywalne potrzeby zmiany skalowania w czasie rzeczywistym. Zamiast tego może być konieczne wykorzystanie kontekstu aplikacji w systemie na poziomie klienta/zestawu SDK lub przy użyciu opcji [autopilotaż](provision-throughput-autopilot.md).

## <a id="use-cql-queries"></a>Korzystanie z zapytań CQL z określonym zestawem SDK

Można dynamicznie skalować system przy użyciu kodu, wykonując [polecenia CQL ALTER](cassandra-support.md#keyspace-and-table-options) dla danej bazy danych lub kontenera.

Zaletą tego podejścia jest to, że pozwala to na dynamiczne reagowanie na potrzeby skalowania i w sposób niestandardowy, który jest przeznaczony dla aplikacji. Korzystając z tej metody, można nadal korzystać ze standardowych stawek za RU/s. Jeśli wymagania dotyczące skalowania systemu są w większości przewidywalne (około 70% lub więcej), korzystanie z zestawu SDK z CQL może być bardziej opłacalną metodą automatycznego skalowania, niż przy użyciu programu pilotażowego. Wadą tego podejścia jest to, że może być dość skomplikowane do wdrożenia ponownych prób, podczas gdy ograniczenie szybkości może zwiększyć opóźnienia.

## <a id="use-autopilot"></a>Użyj autopilotażu

Oprócz ręcznych i programistycznych metod aprowizacji można także skonfigurować kontenery usługi Azure Cosmos w trybie autopilotażu. Tryb automatycznego pilotażu będzie automatycznie i natychmiast skalowany na potrzeby użycia w określonych zakresach RU bez naruszenia umowy SLA. Aby dowiedzieć się więcej, zobacz artykuł [Tworzenie kontenerów i baz danych usługi Azure Cosmos w trybie autopilotażu](provision-throughput-autopilot.md) .

Zaletą tego podejścia jest najprostszy sposób zarządzania potrzebami skalowania w systemie. Gwarantuje to, że nie ma zastosowania ograniczenia stawki **w skonfigurowanych zakresach ru**. Wadą jest to, że jeśli wymagania dotyczące skalowania w systemie są przewidywalne, automatyczne rozwiązanie pilotażowe może być tańszym sposobem obsługi wymagań dotyczących skalowania niż korzystanie z Bespoke płaszczyzny kontroli lub poziomu zestawu SDK wymienionych powyżej.

## <a name="next-steps"></a>Następne kroki

- Rozpocznij od [utworzenia konta, bazy danych i tabeli interfejsu API Cassandra](create-cassandra-api-account-java.md) przy użyciu aplikacji w języku Java
