---
title: Azure Stream Analytics danych wyjściowych do rozwiązania Cosmos bazy danych.
description: W tym artykule opisano sposób użycia usługi Azure Stream Analytics można zapisać danych wyjściowych do bazy danych rozwiązania Cosmos Azure dla danych wyjściowych JSON archiwizowania danych i zapytania o małych opóźnieniach na dane JSON bez struktury.
services: stream-analytics
author: jseb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/28/2017
ms.openlocfilehash: ff2071a703b0b5e94cd68122a878b51e9d97669a
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/29/2018
ms.locfileid: "37112755"
---
# <a name="azure-stream-analytics-output-to-azure-cosmos-db"></a>Azure Stream Analytics dane wyjściowe do bazy danych Azure rozwiązania Cosmos  
Analiza strumienia może kierować [bazy danych Azure rozwiązania Cosmos](https://azure.microsoft.com/services/documentdb/) dla danych wyjściowych JSON, włączanie archiwizacji i małych opóźnieniach kwerend danych na dane JSON bez struktury. W tym dokumencie opisano najważniejsze wskazówki dotyczące implementowania tej konfiguracji.

Dla osób, które znają rozwiązania Cosmos DB, Przyjrzyjmy się [ścieżka szkoleniowa dotycząca usługi Azure rozwiązania Cosmos DB](https://azure.microsoft.com/documentation/learning-paths/documentdb/) rozpocząć pracę. 

> [!Note]
> W tej chwili Azure Stream Analytics obsługuje tylko połączenia do korzystania z bazy danych Azure rozwiązania Cosmos **interfejsu API SQL**.
> Innych interfejsów API Azure rozwiązania Cosmos bazy danych nie są jeszcze obsługiwane. Jeśli punkt Azure Stream Analytics do kont Azure DB rozwiązania Cosmos tworzone za pomocą innych interfejsów API, dane mogą nie być poprawnie przechowywane. 

## <a name="basics-of-cosmos-db-as-an-output-target"></a>Podstawy DB rozwiązania Cosmos jako miejsce docelowe danych wyjściowych
Dane wyjściowe bazy danych Azure rozwiązania Cosmos w Stream Analytics umożliwia zapisywanie strumienia przetwarzanie wyników jako dane wyjściowe JSON do kolekcji z bazy danych rozwiązania Cosmos. Analiza strumienia nie tworzenia kolekcji w bazie danych, zamiast konieczności wyprzedzeniem je utworzyć. Jest tak, aby rozliczeń kosztów rozwiązania Cosmos DB kolekcje są kontrolowane przez użytkownika i tak, aby dostroić wydajności, spójność i pojemność kolekcji bezpośrednio za pomocą [rozwiązania Cosmos DB API](https://msdn.microsoft.com/library/azure/dn781481.aspx). 

Niektóre opcje kolekcji DB rozwiązania Cosmos są szczegółowo opisane poniżej.

## <a name="tune-consistency-availability-and-latency"></a>Dostosuj spójności, dostępnością i opóźnieniem
Aby była zgodna z wymaganiami aplikacji z bazy danych Azure rozwiązania Cosmos umożliwia prawidłowo dostrajania bazy danych i kolekcji i wprowadzić kompromis między spójności, dostępnością i opóźnieniem. W zależności od tego, jakiego poziomu spójności odczytu potrzeb scenariusz przed zapisu i odczytu opóźnienia, możesz wybrać poziom spójności na konta bazy danych. Domyślnie bazy danych rozwiązania Cosmos Azure umożliwia również synchroniczne indeksowania na każdej operacji CRUD do kolekcji. To jest inną opcją przydatne do kontrolowania wydajności zapisu/odczytu w usłudze Azure DB rozwiązania Cosmos. Aby uzyskać więcej informacji, przejrzyj [Zmień poziomy spójności bazy danych i zapytania](../cosmos-db/consistency-levels.md) artykułu.

## <a name="upserts-from-stream-analytics"></a>Upserts z usługi Stream Analytics
Stream Analytics integracji z bazy danych Azure rozwiązania Cosmos służy do wstawiania lub aktualizowania rekordów w kolekcji na podstawie danego kolumny Identyfikator dokumentu. To jest również nazywany *Upsert*.

Analiza strumienia używa optymistycznej upsert podejście, gdy aktualizacje są wykonywane tylko po insert nie powiodło się konflikt identyfikatorów dokumentów. Ta aktualizacja jest wykonywane poprawki, więc umożliwia częściowej aktualizacji do dokumentu, który jest, dodanie nowych właściwości lub zastąpienie istniejącej właściwości jest wykonywana stopniowo. Jednak zmiany w wartości właściwości tablicy w wyników dokumentu JSON w tablicy całego pobierania zastąpione, czyli tablicy nie jest scalone.

## <a name="data-partitioning-in-cosmos-db"></a>Partycjonowanie danych w bazie danych rozwiązania Cosmos
Azure DB rozwiązania Cosmos [nieograniczone](../cosmos-db/partition-data.md) są zalecane podejście do automatycznego partycjonowania danych, jako bazy danych Azure rozwiązania Cosmos skaluje partycje oparte na obciążenie. Podczas zapisywania nieograniczoną liczbę kontenerów, Stream Analytics korzysta tyle równoległego zapisywania jako poprzedniego kroku zapytania lub schemat partycjonowania danych wejściowych.

Usunięte bazy danych Azure rozwiązania Cosmos kolekcje Stream Analytics umożliwia można skalować w górę lub w się, gdy są one pełna. Mają one maksymalnie 10 GB i 10 000 RU/s przepustowości.  Aby przeprowadzić migrację danych z kontenera stałym nieograniczone kontenera (na przykład jeden z co najmniej 1 000 RU/s i klucz partycji), należy użyć [narzędzia migracji danych](../cosmos-db/import-data.md) lub [zmiany źródła danych biblioteki](../cosmos-db/change-feed.md).

Zapisywanie wielu kontenerów stałej jest przestarzały i nie jest zalecane podejście do skalowania zadania usługi analiza strumienia. Artykuł [dzielenia na partycje i skalowania w bazie danych rozwiązania Cosmos](../cosmos-db/sql-api-partition-data.md) zawiera dalsze szczegóły.

## <a name="cosmos-db-settings-for-json-output"></a>Ustawienia rozwiązania cosmos bazy danych dla danych wyjściowych JSON
Tworzenie rozwiązania Cosmos bazy danych jako dane wyjściowe w Stream Analytics generuje monit o podanie informacji, jak pokazano poniżej. Ta sekcja zawiera wyjaśnienie definicji właściwości.


![documentdb stream analytics dane wyjściowe ekranu](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-1.png)

Pole           | Opis 
-------------   | -------------
Alias wyjściowy    | Alias do odwoływania się te dane wyjściowe w kwerendzie ASA   
Nazwa konta    | Nazwa lub identyfikator URI konta bazy danych Azure rozwiązania Cosmos punktu końcowego 
Klucz konta     | Klucz dostępu współdzielonego dla konta bazy danych Azure rozwiązania Cosmos
Database (Baza danych)        | Nazwa bazy danych Azure DB rozwiązania Cosmos
Nazwa kolekcji | Nazwa kolekcji dla kolekcji do użycia. `MyCollection` to przykładowe prawidłową wartość wejściową — jedną kolekcję o nazwie `MyCollection` musi istnieć.  
Identyfikator dokumentu     | Opcjonalny. Nazwa kolumny w zdarzeniach wyjściowych służąca jako unikatowy klucz, na które insert lub update musi być oparta operacji. Jeśli pole pozostanie puste, wszystkie zdarzenia zostanie wstawiony, bez opcji aktualizacji.
