---
title: Migrowanie danych na konto interfejsu API Cassandra w usłudze Azure Cosmos DB
description: Dowiedz się, jak skopiować dane z bazy danych Apache Cassandra do interfejsu API Cassandra w usłudze Azure Cosmos DB za pomocą polecenia Copy języka CQL i platformy Spark.
services: cosmos-db
author: kanshiG
ms.service: cosmos-db
ms.component: cosmosdb-cassandra
ms.author: govindk
ms.topic: tutorial
ms.date: 09/24/2018
ms.reviewer: sngun
ms.openlocfilehash: 56fc07c6d775ee8015ce244acb7782607bda802a
ms.sourcegitcommit: ae45eacd213bc008e144b2df1b1d73b1acbbaa4c
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/01/2018
ms.locfileid: "50739791"
---
# <a name="tutorial-migrate-your-data-to-azure-cosmos-db-cassandra-api-account"></a>Samouczek: migrowanie danych do konta interfejsu API Cassandra usługi Azure Cosmos DB

Ten samouczek zawiera instrukcje dotyczące sposobu przeprowadzania migracji danych z bazy danych Apache Cassandra do interfejsu API Cassandra w usłudze Azure Cosmos DB. 

Ten samouczek obejmuje następujące zadania:

> [!div class="checklist"]
> * Planowanie migracji
> * Wymagania wstępne dotyczące migracji
> * Migrowanie danych przy użyciu polecenia cqlsh COPY
> * Migrowanie danych przy użyciu platformy Spark 

## <a name="plan-for-migration"></a>Planowanie migracji

Przed rozpoczęciem migracji danych do interfejsu API Cassandra w usłudze Azure Cosmos DB musisz oszacować przepływność potrzebną dla Twojego obciążenia. Na ogół zalecane jest rozpoczęcie od średniej przepływności wymaganej przez operacje CRUD, a następnie dołączenie dodatkowej przepływności wymaganej dla operacji ETL (wyodrębnianie, transformacja, ładowanie) lub operacji powodujących gwałtowne wzrosty obciążenia. Do zaplanowania migracji potrzebne są następujące szczegóły: 

* **Istniejący rozmiar danych lub szacowany rozmiar danych.** Definiuje minimalne wymagania dotyczące rozmiaru bazy danych i przepływności. Jeśli szacujesz rozmiar danych dla nowej aplikacji, możesz założyć, że dane są równomiernie rozłożone między wiersze i oszacować wartość, mnożąc ich liczbę przez rozmiar danych. 

* **Wymagana przepływność.** Przybliżona przepływność w przypadku operacji odczytu (query/get) i zapisu (update/delete/insert). Ta wartość jest wymagana do obliczenia wymaganych jednostek żądań wraz z rozmiarem danych w stanie stabilności.  

* **Pobranie schematu.** Połącz się z istniejącym klastrem Cassandra za pośrednictwem poleceń cqlsh i wyeksportuj schemat z bazy danych Cassandra: 

  ```bash
  cqlsh [IP] "-e DESC SCHEMA" > orig_schema.cql
  ```

Po zidentyfikowaniu wymagań dotyczących istniejącego obciążenia musisz utworzyć konto usługi Azure Cosmos DB, bazę danych i kontenery zgodnie z zebranymi wymaganiami dotyczącymi przepływności.  

* **Określenie opłaty za jednostkę żądania dla operacji.** Jednostki żądań możesz określić przy użyciu wybranego przez siebie zestawu SDK interfejsu API Cassandra w usłudze Azure Cosmos DB. W tym przykładzie przedstawiono sposób określania opłaty za jednostki żądań z użyciem wersji platformy .NET.

  ```csharp
  var tableInsertStatement = table.Insert(sampleEntity);
  var insertResult = await tableInsertStatement.ExecuteAsync();

  foreach (string key in insertResult.Info.IncomingPayload)
    {
       byte[] valueInBytes = customPayload[key];
       string value = Encoding.UTF8.GetString(valueInBytes);
       Console.WriteLine($"CustomPayload:  {key}: {value}");
    }
  ```

* **Przydzielenie wymaganej przepływności.** Usługa Azure Cosmos DB potrafi automatycznie skalować magazyn i przepływność w miarę wzrostu wymagań. Potrzebną przepływność możesz oszacować, korzystając z [kalkulatora jednostek żądań usługi Azure Cosmos DB](https://www.documentdb.com/capacityplanner). 

## <a name="prerequisites-for-migration"></a>Wymagania wstępne dotyczące migracji

* **Utworzenie tabel na koncie interfejsu API Cassandra w usłudze Azure Cosmos DB.** Przed rozpoczęciem migracji danych utwórz wstępnie wszystkie tabele z poziomu witryny Azure Portal lub serwera cqlsh. W przypadku migracji do konta usługi Azure Cosmos DB, która ma przepływność na poziomie bazy danych, pamiętaj o podaniu klucza partycji podczas tworzenia kontenerów usługi Azure Cosmos DB.

* **Zwiększenie przepływności.** Czas trwania migracji danych zależy od przepływności aprowizowanej dla tabel w usłudze Azure Cosmos DB. Na czas trwania migracji zwiększ przepływność. Dzięki większej przepływności można uniknąć ograniczania przepustowości i przeprowadzać migrację w krótszym czasie. Po ukończeniu migracji zmniejsz przepływność, aby ograniczyć koszty. Aby uzyskać więcej informacji na temat zwiększania przepływności, zobacz [ustawianie przepływności](set-throughput.md) dla kontenerów usługi Azure Cosmos DB. Zalecane jest również posiadanie konta usługi Azure Cosmos DB w tym samym regionie, w którym znajduje się źródłowa baza danych. 

* **Włączenie protokołu SSL:** usługa Azure Cosmos DB ma ścisłe wymagania i standardy dotyczące bezpieczeństwa. Pamiętaj, aby włączyć protokół SSL w przypadku interakcji z kontem. W przypadku korzystania z języka CQL z protokołem SSH możesz udostępnić informacje wymagane przez protokół SSL.

## <a name="options-to-migrate-data"></a>Opcje migracji danych

Dane z istniejących obciążeń bazy danych Cassandra można przenieść do usługi Azure Cosmos DB przy użyciu następujących opcji:

* [Przy użyciu polecenia cqlsh COPY](#using-cqlsh-copy-command)  
* [Przy użyciu platformy Spark](#using-spark) 

## <a name="migrate-data-using-cqlsh-copy-command"></a>Migrowanie danych przy użyciu polecenia cqlsh COPY

[Polecenie COPY języka CQL](http://cassandra.apache.org/doc/latest/tools/cqlsh.html#cqlsh) służy do kopiowania danych lokalnych na konto interfejsu API Cassandra w usłudze Azure Cosmos DB. Aby skopiować dane, wykonaj następujące kroki:

1. Uzyskaj informacje o parametrach połączenia konta interfejsu API Cassandra:

   * Zaloguj się do witryny [Azure Portal](https://portal.azure.com) i przejdź do swojego konta usługi Azure Cosmos DB.

   * Otwórz okienko **Parametry połączenia** zawierające wszystkie informacje, których potrzebujesz, aby połączyć się z kontem interfejsu API Cassandra z poziomu serwera cqlsh.

2. Zaloguj się do serwera cqlsh przy użyciu informacji o połączeniu z portalu.

3. Użyj polecenia CQL COPY, aby skopiować dane lokalne na konto interfejsu API Cassandra.

   ```bash
   COPY exampleks.tablename FROM filefolderx/*.csv 
   ```

## <a name="migrate-data-using-spark"></a>Migrowanie danych przy użyciu platformy Spark 

Aby przeprowadzić migrację danych z interfejsu API Cassandra w usłudze Azure Cosmos DB za pomocą platformy Spark, wykonaj następujące kroki:

- Przeprowadź aprowizację usługi [Azure Databricks](cassandra-spark-databricks.md) lub [klastra HDInsight](cassandra-spark-hdinsight.md) 

- Przenieś dane do docelowego punktu końcowego interfejsu API Cassandra przy użyciu [operacji kopiowania tabeli](cassandra-spark-table-copy-ops.md) 

Migracja danych przy użyciu zadań platformy Spark jest opcją zalecaną, gdy Twoje dane znajdują się w istniejącym klastrze na maszynach wirtualnych platformy Azure lub w jakiejkolwiek innej chmurze. Ta opcja wymaga skonfigurowania platformy Spark jako elementu pośredniczącego na potrzeby jednorazowego lub regularnego pozyskiwania. Migrację możesz przyspieszyć, używając łączności ExpressRoute między środowiskiem lokalnym i platformą Azure. 

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono sposób migrowania danych na konto interfejsu API Cassandra w usłudze Azure Cosmos DB. Teraz możesz przejść do sekcji pojęć, aby uzyskać więcej informacji na temat usługi Azure Cosmos DB. 

> [!div class="nextstepaction"]
> [Dostosowywalne poziomy spójności danych w usłudze Azure Cosmos DB](../cosmos-db/consistency-levels.md)


