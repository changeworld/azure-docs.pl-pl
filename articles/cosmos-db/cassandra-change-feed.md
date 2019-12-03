---
title: Źródło zmian w interfejsie API Azure Cosmos DB dla Cassandra
description: Dowiedz się, jak używać kanału informacyjnego zmian w interfejsie API Azure Cosmos DB Cassandra, aby uzyskać zmiany wprowadzone w danych.
author: TheovanKraay
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: conceptual
ms.date: 11/25/2019
ms.author: thvankra
ms.openlocfilehash: c2c695608653130b97bf29cc9ce48e2fbb429209
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/02/2019
ms.locfileid: "74694625"
---
# <a name="change-feed-in-the-azure-cosmos-db-api-for-cassandra"></a>Źródło zmian w interfejsie API Azure Cosmos DB dla Cassandra

Obsługa [kanału informacyjnego zmian](change-feed.md) w interfejsie API Azure Cosmos DB dla Cassandra jest dostępna za pomocą predykatów zapytań w języku zapytań CASSANDRA (CQL). Za pomocą tych warunków predykatu można wysyłać zapytania do interfejsu API źródła zmian. Aplikacje mogą pobrać zmiany wprowadzone do tabeli przy użyciu klucza podstawowego (zwanego również kluczem partycji), jak jest to wymagane w CQL. Następnie można wykonać dalsze czynności na podstawie wyników. Zmiany w wierszach w tabeli są przechwytywane w kolejności ich modyfikacji, a porządek sortowania jest gwarantowany na klucz partycji.

Poniższy przykład pokazuje, jak uzyskać Źródło zmian dla wszystkich wierszy w interfejs API Cassandra tabeli przestrzeni kluczy przy użyciu platformy .NET. Predykat COSMOS_CHANGEFEED_START_TIME () jest używany bezpośrednio w CQL do wykonywania zapytań o elementy ze źródła zmian od określonego czasu rozpoczęcia (w tym przypadku Current DateTime). Pełny przykład możesz pobrać [tutaj](https://docs.microsoft.com/samples/azure-samples/azure-cosmos-db-cassandra-change-feed/cassandra-change-feed/).

W każdej iteracji zapytanie zostaje wznowione w ostatnim momencie odczytu, przy użyciu stanu stronicowania. W obszarze kluczy można zobaczyć ciągły strumień nowych zmian w tabeli. Zobaczymy zmiany w wierszach, które są wstawiane lub aktualizowane. Obserwowanie operacji usuwania przy użyciu źródła zmian w interfejs API Cassandra nie jest obecnie obsługiwane. 

```C#
    //set initial start time for pulling the change feed
     DateTime timeBegin = DateTime.UtcNow;

    //initialise variable to store the continuation token
    byte[] pageState = null;
    while (true)
    {
        try
        {

            //Return the latest change for all rows in 'user' table    
            IStatement changeFeedQueryStatement = new SimpleStatement(
            $"SELECT * FROM uprofile.user where COSMOS_CHANGEFEED_START_TIME() = '{timeBegin.ToString("yyyy-MM-ddTHH:mm:ss.fffZ", CultureInfo.InvariantCulture)}'");
            if (pageState != null)
            {
                changeFeedQueryStatement = changeFeedQueryStatement.SetPagingState(pageState);
            }
            Console.WriteLine("getting records from change feed at last page state....");
            RowSet rowSet = session.Execute(changeFeedQueryStatement);

            //store the continuation token here
            pageState = rowSet.PagingState;

            List<Row> rowList = rowSet.ToList();
            if (rowList.Count != 0)
            {
                for (int i = 0; i < rowList.Count; i++)
                {
                    string value = rowList[i].GetValue<string>("user_name");
                    int key = rowList[i].GetValue<int>("user_id");
                    // do something with the data - e.g. compute, forward to another event, function, etc.
                    // here, we just print the user name field
                    Console.WriteLine("user_name: " + value);
                }
            }
            else
            {
                Console.WriteLine("zero documents read");
            }
        }
        catch (Exception e)
        {
            Console.WriteLine("Exception " + e);
        }
    }

```

Aby uzyskać zmiany w pojedynczym wierszu według klucza podstawowego, można dodać klucz podstawowy w zapytaniu. Poniższy przykład pokazuje, jak śledzić zmiany w wierszu, w którym "user_id = 1"

```C#
    //Return the latest change for all row in 'user' table where user_id = 1
    IStatement changeFeedQueryStatement = new SimpleStatement(
    $"SELECT * FROM uprofile.user where user_id = 1 AND COSMOS_CHANGEFEED_START_TIME() = '{timeBegin.ToString("yyyy-MM-ddTHH:mm:ss.fffZ", CultureInfo.InvariantCulture)}'");

```

## <a name="current-limitations"></a>Bieżące ograniczenia

W przypadku korzystania z kanału informacyjnego zmian z interfejs API Cassandra są stosowane następujące ograniczenia:

* Operacje INSERT i Update są obecnie obsługiwane. Operacja usuwania nie jest jeszcze obsługiwana. Aby obejść ten element, można dodać znacznik elastyczny do wierszy, które są usuwane. Na przykład Dodaj pole w wierszu o nazwie "usunięte" i ustaw je na wartość "true".
* Ostatnia aktualizacja jest utrwalona, ponieważ w podstawowym interfejsie API SQL Server nie są dostępne aktualizacje pośrednie.


## <a name="error-handling"></a>Obsługa błędów

Następujące kody błędów i komunikaty są obsługiwane w przypadku używania źródła zmian w interfejs API Cassandra:

* **Kod błędu HTTP 429** — gdy kanał informacyjny zmiany ma ograniczoną szybkość, zwraca pustą stronę.

## <a name="next-steps"></a>Następne kroki

* [Zarządzanie zasobami interfejs API Cassandra Azure Cosmos DB przy użyciu szablonów Azure Resource Manager](manage-cassandra-with-resource-manager.md)
