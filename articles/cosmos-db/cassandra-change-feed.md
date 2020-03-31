---
title: Zmienianie kanału informacyjnego w interfejsie API bazy danych usługi Azure Cosmos dla usługi Cassandra
description: Dowiedz się, jak korzystać z kanału informacyjnego usługi Azure Cosmos DB dla firmy Cassandra, aby uzyskać zmiany wprowadzone w danych.
author: TheovanKraay
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: conceptual
ms.date: 11/25/2019
ms.author: thvankra
ms.openlocfilehash: c2c695608653130b97bf29cc9ce48e2fbb429209
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74694625"
---
# <a name="change-feed-in-the-azure-cosmos-db-api-for-cassandra"></a>Zmienianie kanału informacyjnego w interfejsie API bazy danych usługi Azure Cosmos dla usługi Cassandra

[Obsługa kanału informacyjnego zmiany](change-feed.md) w interfejsie API bazy danych usługi Azure Cosmos dla bazy danych Cassandra jest dostępna za pośrednictwem predykatów kwerendy w języku kasandra query language (CQL). Korzystając z tych warunków predykatu, można zbadać interfejs API źródła danych zmian. Aplikacje mogą uzyskać zmiany wprowadzone do tabeli przy użyciu klucza podstawowego (znany również jako klucz partycji), zgodnie z wymaganiami w CQL. Następnie można podjąć dalsze działania na podstawie wyników. Zmiany w wierszach w tabeli są przechwytywane w kolejności ich czasu modyfikacji i kolejność sortowania jest gwarantowana na klucz partycji.

W poniższym przykładzie pokazano, jak uzyskać źródło danych o zmianach we wszystkich wierszach w tabeli obszaru kluczowego interfejsu API Cassandra przy użyciu platformy .NET. Predykat COSMOS_CHANGEFEED_START_TIME() jest używany bezpośrednio w CQL do wykonywania zapytań o elementy w pliku danych o zmianach z określonego czasu rozpoczęcia (w tym przypadku bieżącego datetime). Pełną próbkę można pobrać [tutaj](https://docs.microsoft.com/samples/azure-samples/azure-cosmos-db-cassandra-change-feed/cassandra-change-feed/).

W każdej iteracji kwerenda wznawia w ostatnim punkcie zmiany zostały odczytane przy użyciu stanu stronicowania. Widzimy ciągły strumień nowych zmian w tabeli w przestrzeni kluczy. Zobaczymy zmiany w wierszach, które są wstawiane lub aktualizowane. Obserwowanie operacji usuwania przy użyciu źródła danych w api Cassandra nie jest obecnie obsługiwane. 

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

Aby uzyskać zmiany w jednym wierszu według klucza podstawowego, można dodać klucz podstawowy w kwerendzie. W poniższym przykładzie pokazano, jak śledzić zmiany w wierszu, w którym "user_id = 1"

```C#
    //Return the latest change for all row in 'user' table where user_id = 1
    IStatement changeFeedQueryStatement = new SimpleStatement(
    $"SELECT * FROM uprofile.user where user_id = 1 AND COSMOS_CHANGEFEED_START_TIME() = '{timeBegin.ToString("yyyy-MM-ddTHH:mm:ss.fffZ", CultureInfo.InvariantCulture)}'");

```

## <a name="current-limitations"></a>Bieżące ograniczenia

Następujące ograniczenia mają zastosowanie w przypadku korzystania z pliku danych o zmianach w interfejsie API Cassandra:

* Wstawia i aktualizacje są obecnie obsługiwane. Operacja usuwania nie jest jeszcze obsługiwana. Aby obejść ten problem, można dodać znacznik miękki w wierszach, które są usuwane. Na przykład dodaj pole w wierszu o nazwie "usunięte" i ustaw je na "true".
* Ostatnia aktualizacja jest zachowywana, jak w rdzeniu SQL API i pośrednie aktualizacje do jednostki nie są dostępne.


## <a name="error-handling"></a>Obsługa błędów

Następujące kody błędów i komunikaty są obsługiwane podczas korzystania z źródła danych w api Cassandra:

* **Kod błędu HTTP 429** — gdy wskaźnik dostępu do zmian jest ograniczony, zwraca pustą stronę.

## <a name="next-steps"></a>Następne kroki

* [Zarządzanie zasobami interfejsu API usługi Azure Cosmos DB Cassandra przy użyciu szablonów usługi Azure Resource Manager](manage-cassandra-with-resource-manager.md)
