---
title: Elastyczna skala za pomocą interfejsu API Cassandra w usłudze Azure Cosmos DB
description: Dowiedz się więcej o dostępnych opcjach skalowania konta interfejsu API usługi Azure Cosmos DB Cassandra oraz o ich zaletach/wadach
author: TheovanKraay
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 01/13/2020
ms.author: thvankra
ms.openlocfilehash: 10d81de48c0d8f56c7c3fd26e3fd82a8c3df84c6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79474683"
---
# <a name="elastically-scale-an-azure-cosmos-db-cassandra-api-account"></a>Elastyczne skalowanie konta interfejsu API usługi Azure Cosmos DB Cassandra

Istnieje wiele opcji, aby eksplorować elastyczny charakter interfejsu API usługi Azure Cosmos DB dla Cassandra. Aby zrozumieć, jak skutecznie skalować w usłudze Azure Cosmos DB, ważne jest, aby zrozumieć, jak aprowizować odpowiednią ilość jednostek żądań (RU/s) do uwzględnienia wymagań dotyczących wydajności w systemie. Aby dowiedzieć się więcej o jednostkach żądań, zobacz artykuł [o jednostkach żądań.](request-units.md) 

W przypadku interfejsu API Cassandra można pobrać opłatę za jednostkę żądania dla poszczególnych zapytań przy użyciu [.NET i Java SDK](https://docs.microsoft.com/azure/cosmos-db/find-request-unit-charge#cassandra-api). Jest to pomocne w określeniu ilości RU/s, które należy udostępnić w usłudze.

![Operacje bazy danych zużywają jednostki żądań](./media/request-units/request-units.png)

## <a name="handling-rate-limiting-429-errors"></a>Ograniczenie szybkości obsługi (429 błędów)

Usługa Azure Cosmos DB zwróci błędy o ograniczonej szybkości (429), jeśli klienci zużywają więcej zasobów (RU/s) niż kwota, która została zainicjowana. Interfejs API Cassandra w usłudze Azure Cosmos DB tłumaczy te wyjątki na przeciążone błędy w protokole macierzystym Cassandra. 

Jeśli system nie jest wrażliwy na opóźnienie, może być wystarczające do obsługi ograniczania przepustowości przy użyciu ponownych prób. Zobacz [przykładowy kod Java,](https://github.com/Azure-Samples/azure-cosmos-cassandra-java-retry-sample) jak obsługiwać ograniczenie szybkości w sposób przejrzysty przy użyciu [rozszerzenia usługi Azure Cosmos DB](https://github.com/Azure/azure-cosmos-cassandra-extensions) dla [zasad ponawiania próby Cassandra](https://docs.datastax.com/en/developer/java-driver/4.4/manual/core/retries/) w języku Java. Można również użyć [rozszerzenia Platformy Spark](https://mvnrepository.com/artifact/com.microsoft.azure.cosmosdb/azure-cosmos-cassandra-spark-helper) do obsługi ograniczania szybkości.

## <a name="manage-scaling"></a>Zarządzanie skalowaniem

Jeśli chcesz zminimalizować opóźnienia, istnieje spektrum opcji zarządzania skalą i aprowizowania przepływności (RUs) w interfejsie API Cassandra:

* [Ręcznie przy użyciu witryny Azure portal](#use-azure-portal)
* [Programowo przy użyciu operacji płaszczyzny sterowania](#use-control-plane)
* [Programowo przy użyciu poleceń CQL z określonym plikiem SDK](#use-cql-queries)
* [Dynamicznie za pomocą autopilota](#use-autopilot)

W poniższych sekcjach wyjaśniono zalety i wady każdego podejścia. Następnie można zdecydować się na najlepszą strategię, aby zrównoważyć potrzeby skalowania systemu, ogólne koszty i potrzeby w zakresie wydajności rozwiązania.

## <a name="use-the-azure-portal"></a><a id="use-azure-portal"></a>Korzystanie z witryny Azure Portal

Zasoby można skalować na koncie interfejsu API usługi Azure Cosmos DB Cassandra przy użyciu witryny Azure portal. Aby dowiedzieć się więcej, zobacz artykuł [na temat aprowizowania przepływności kontenerów i baz danych](set-throughput.md). W tym artykule opisano względne korzyści wynikające z ustawiania przepływności na poziomie [bazy danych](set-throughput.md#set-throughput-on-a-database) lub [kontenera](set-throughput.md#set-throughput-on-a-container) w witrynie Azure portal. Terminy "baza danych" i "kontener" wymienione w tych artykułach są mapowane odpowiednio na "keyspace" i "table" dla interfejsu API Cassandra.

Zaletą tej metody jest to, że jest to prosty sposób "pod klucz", aby zarządzać przepustowością w bazie danych. Jednak wadą jest to, że w wielu przypadkach podejście do skalowania może wymagać pewnych poziomów automatyzacji, aby być zarówno opłacalne i wysokiej wydajności. W następnych sekcjach wyjaśniono odpowiednie scenariusze i metody.

## <a name="use-the-control-plane"></a><a id="use-control-plane"></a>Użyj płaszczyzny sterowania

Interfejs API usługi Azure Cosmos DB dla firmy Cassandra umożliwia programowe dostosowywanie przepływności przy użyciu naszych różnych funkcji płaszczyzny sterowania. Zapoznaj się z artykułami [usługi Azure Resource Manager](manage-cassandra-with-resource-manager.md), [Powershell](powershell-samples-cassandra.md)i [Azure CLI,](cli-samples-cassandra.md) aby uzyskać wskazówki i przykłady.

Zaletą tej metody jest to, że można zautomatyzować skalowanie w górę lub w dół zasobów na podstawie czasomierza do konta dla szczytowej aktywności lub okresów niskiej aktywności. Zapoznaj się z naszym przykładem [tutaj,](https://github.com/Azure-Samples/azure-cosmos-throughput-scheduler) aby dowiedzieć się, jak to zrobić za pomocą usługi Azure Functions i Powershell.

Wadą tego podejścia może być to, że nie można reagować na nieprzewidywalne potrzeby skali zmiany w czasie rzeczywistym. Zamiast tego może być konieczne wykorzystanie kontekstu aplikacji w systemie, na poziomie klienta/SDK lub za pomocą [autopilota](provision-throughput-autopilot.md).

## <a name="use-cql-queries-with-a-specific-sdk"></a><a id="use-cql-queries"></a>Używanie zapytań CQL z określonym sdk

System można skalować dynamicznie za pomocą kodu, wykonując [polecenia CQL ALTER](cassandra-support.md#keyspace-and-table-options) dla danej bazy danych lub kontenera.

Zaletą tego podejścia jest to, że pozwala na reagowanie na potrzeby skalowania dynamicznie i w niestandardowy sposób, który odpowiada aplikacji. Dzięki takiemu podejściu nadal możesz wykorzystać standardowe opłaty i stawki RU/s. Jeśli potrzeby skalowania systemu są w większości przewidywalne (około 70% lub więcej), używanie SDK z CQL może być bardziej opłacalną metodą automatycznego skalowania niż korzystanie z autopilota. Wadą tego podejścia jest to, że może być dość skomplikowane do zaimplementowania ponownych prób, podczas gdy ograniczenie szybkości może zwiększyć opóźnienie.

## <a name="use-autopilot"></a><a id="use-autopilot"></a>Korzystanie z autopilota

Oprócz ręcznego lub programowego sposobu inicjowania obsługi administracyjnej przepływności można również skonfigurować kontenery usługi Azure cosmos w trybie autopilota. Tryb autopilota będzie automatycznie i natychmiast skalowany do twoich potrzeb konsumpcyjnych w określonych zakresach RU bez uszczerbku dla sla. Aby dowiedzieć się więcej, zobacz [tworzenie kontenerów i baz danych usługi Azure Cosmos w trybie autopilota.](provision-throughput-autopilot.md)

Zaletą tego podejścia jest to, że jest to najprostszy sposób zarządzania potrzeb skalowania w systemie. Gwarantuje, że nie będzie stosować ograniczania szybkości **w skonfigurowanych zakresach RU.** Wadą jest to, że jeśli potrzeby skalowania w systemie są przewidywalne, Autopilot może być mniej opłacalnym sposobem obsługi potrzeb skalowania niż przy użyciu na zamówienie płaszczyzny sterowania lub podejścia na poziomie SDK wymienionych powyżej.

## <a name="next-steps"></a>Następne kroki

- Rozpocznij od [utworzenia konta, bazy danych i tabeli interfejsu API Cassandra](create-cassandra-api-account-java.md) przy użyciu aplikacji w języku Java
