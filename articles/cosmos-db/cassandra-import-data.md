---
title: Importowanie danych bazy danych Cassandra do usługi Azure Cosmos DB | Microsoft Docs
description: Dowiedz się, jak używać polecenia CQL Copy do kopiowania danych bazy danych Cassandra do usługi Azure Cosmos DB.
services: cosmos-db
author: govindk
manager: kfile
ms.service: cosmos-db
ms.component: cosmosdb-cassandra
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 11/15/2017
ms.author: govindk
ms.custom: mvc
ms.openlocfilehash: 73c9f1fc26f5cb36cc475a66b67705c6177bebf8
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/29/2018
ms.locfileid: "37095676"
---
# <a name="azure-cosmos-db-import-cassandra-data"></a>Azure Cosmos DB: importowanie danych bazy danych Cassandra

Ten samouczek zawiera instrukcje dotyczące importowania danych bazy danych Cassandra do usługi Azure Cosmos DB przy użyciu polecenia COPY języka CQL (Cassandra Query Language). 

Ten samouczek obejmuje następujące zadania:

> [!div class="checklist"]
> * Pobieranie parametrów połączenia
> * Importowanie danych przy użyciu polecenia cqlsh COPY
> * Importowanie przy użyciu łącznika platformy Spark 

# <a name="prerequisites"></a>Wymagania wstępne

* Zainstaluj bazę danych [Apache Cassandra](http://cassandra.apache.org/download/) i upewnij się, że zainstalowano też język *cqlsh*.
* Zwiększ przepływność. Czas trwania migracji danych zależy od przepływności aprowizowanej dla tabel. Pamiętaj o zwiększeniu przepływności w przypadku większych migracji danych. Po ukończeniu migracji zmniejsz przepływność, aby ograniczyć koszty. Aby uzyskać więcej informacji na temat zwiększania przepływności w witrynie [Azure Portal](https://portal.azure.com), zobacz [Set throughput for Azure Cosmos DB containers](set-throughput.md) (Ustawianie przepływności dla kontenerów usługi Azure Cosmos DB).
* Włączenie protokołu SSL: usługa Azure Cosmos DB ma ścisłe wymagania i standardy dotyczące bezpieczeństwa. Pamiętaj, aby włączyć protokół SSL w przypadku interakcji z kontem. W przypadku korzystania z języka CQL z protokołem SSH możesz udostępnić informacje wymagane przez protokół SSL. 

## <a name="find-your-connection-string"></a>Znajdywanie parametrów połączenia

1. W witrynie [Azure Portal](https://portal.azure.com) na końcu z lewej strony kliknij pozycję **Azure Cosmos DB**.

2. W okienku **Subskrypcje** wybierz nazwę konta.

3. Kliknij pozycję **Parametry połączenia**. Prawe okienko zawiera wszystkie informacje potrzebne do pomyślnego połączenia z kontem.

    ![Strona z parametrami połączenia](./media/cassandra-import-data/keys.png)

## <a name="use-cqlsh-copy"></a>Korzystanie z polecenia cqlsh COPY

Aby zaimportować dane bazy danych Cassandra do usługi Azure Cosmos DB do korzystania za pomocą interfejsu API Cassandra, użyj poniższych wskazówek:

1. Zaloguj się do serwera cqhsh przy użyciu informacji o połączeniu z portalu.
2. Użyj [polecenia CQL COPY](http://cassandra.apache.org/doc/latest/tools/cqlsh.html#cqlsh), aby skopiować dane lokalne do punktu końcowego interfejsu API Apache Cassandra. Aby zminimalizować problemy z opóźnieniem, upewnij się, że źródło i cel są w tym samym centrum danych.

### <a name="guide-for-moving-data-with-cqlsh"></a>Przewodnik przenoszenia danych za pomocą poleceń cqlsh

1. Wstępnie utwórz tabelę i przeprowadź jej skalowanie:
    * Domyślnie usługa Azure Cosmos DB aprowizuje nową tabelę interfejsu API Cassandra z szybkością 1000 jednostek żądań na sekundę (RU/s) (podczas tworzenia za pomocą języka CQL aprowizowanie zachodzi z szybkością 400 RU/s). Przed rozpoczęciem migracji za pomocą polecenia cqlsh utwórz wstępnie wszystkie tabele za pomocą witryny [Azure Portal](https://portal.azure.com) lub języka cqlsh. 

    * W witrynie [Azure Portal](https://portal.azure.com) zwiększ przepływność tabel z domyślnej wartości przepływności (400 lub 1000 RU/s) do 10 000 RU/s na czas trwania migracji. Dzięki większej przepływności można uniknąć ograniczania przepustowości i przeprowadzać migrację w krótszym czasie. Korzystając z rozliczeń godzinowych w usłudze Azure Cosmos DB, można zmniejszyć przepustowość natychmiast po migracji w celu ograniczenia kosztów.

2. Określ opłatę za jednostki żądań dla operacji. Można to zrobić przy użyciu dowolnego zestawu SDK interfejsu API Cassandra usługi Azure Cosmos DB. W tym przykładzie przedstawiono sposób określania opłaty za jednostki żądań z użyciem wersji platformy .NET. 

    ```csharp
    var tableInsertStatement = table.Insert(sampleEntity);
    var insertResult = await tableInsertStatement.ExecuteAsync();

    foreach (string key in insertResult.Info.IncomingPayload)
            {
                byte[] valueInBytes = customPayload[key];
                string value = Encoding.UTF8.GetString(valueInBytes);
                Console.WriteLine($“CustomPayload:  {key}: {value}”);
            }
 
    ``` 

3. Określ opóźnienie między maszyną i usługą Azure Cosmos DB. Jeśli jesteś w centrum danych platformy Azure, opóźnienie powinno mieć wartość jednocyfrowej liczby milisekund. Jeśli jesteś poza centrum danych platformy Azure, możesz użyć polecenia psping lub strony azurespeed.com w celu uzyskania przybliżonego opóźnienia ze swojej lokalizacji.   

4. Oblicz odpowiednie wartości dla parametrów (NUMPROCESS, INGESTRATE, MAXBATCHSIZE lub MINBATCHSIZE), które zapewniają dobrą wydajność. 

5. Uruchom końcowe polecenie migracji. Przed uruchomieniem tego polecenia należy uruchomić narzędzie cqlsh z użyciem informacji o parametrach połączenia.

   ```
   COPY exampleks.tablename FROM filefolderx/*.csv 
   ```

## <a name="use-spark-to-import-data"></a>Korzystanie z platformy Spark do importowania danych

Dane znajdujące się w istniejącym klastrze na maszynach wirtualnych platformy Azure można również zaimportować, korzystając z platformy Spark. Ta metoda wymaga skonfigurowania platformy Spark jako elementu pośredniczącego w jednorazowym lub regularnym pozyskiwaniu. 

## <a name="next-steps"></a>Następne kroki

W tym samouczku pokazano, jak wykonać następujące zadania:

> [!div class="checklist"]
> * Pobieranie parametrów połączenia
> * Importowanie danych przy użyciu polecenia cql copy
> * Importowanie przy użyciu łącznika platformy Spark 

Teraz możesz przejść do sekcji Pojęcia, aby uzyskać więcej informacji na temat usługi Azure Cosmos DB. 

> [!div class="nextstepaction"]
>[Dostosowywalne poziomy spójności danych w usłudze Azure Cosmos DB](../cosmos-db/consistency-levels.md)
