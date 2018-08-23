---
title: Usługa Azure Stream Analytics danych wyjściowych Cosmos DB
description: W tym artykule opisano sposób użycia usługi Azure Stream Analytics można zapisać danych wyjściowych do usługi Azure Cosmos DB dla danych wyjściowych JSON, archiwizowania danych i zapytań o małych opóźnieniach na danych JSON bez struktury.
services: stream-analytics
author: jseb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/28/2017
ms.openlocfilehash: 95cfc7e6d9515274aa7a3c5fde382244f3b33fab
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/20/2018
ms.locfileid: "42062095"
---
# <a name="azure-stream-analytics-output-to-azure-cosmos-db"></a>Usługa Azure Stream Analytics dane wyjściowe usługi Azure Cosmos DB  
Stream Analytics można wskazać [usługi Azure Cosmos DB](https://azure.microsoft.com/services/documentdb/) dla danych wyjściowych JSON, włączanie danych archiwizowanie i małe opóźnienia zapytań na danych JSON bez struktury. W tym dokumencie opisano najlepsze rozwiązania dotyczące wdrażania tej konfiguracji.

Dla osób, które są Ci znane z usługą Cosmos DB, Przyjrzyj się [ścieżka szkoleniowa usługi Azure Cosmos DB](https://azure.microsoft.com/documentation/learning-paths/documentdb/) na rozpoczęcie pracy. 

> [!Note]
> W tej chwili usługi Azure Stream Analytics obsługuje tylko połączenie do usługi Azure Cosmos DB przy użyciu **interfejsu API SQL**.
> Innych interfejsów API usługi Azure Cosmos DB nie są jeszcze obsługiwane. Jeśli punkt Azure Stream Analytics do kont usługi Azure Cosmos DB utworzone z innymi interfejsami API, dane mogą nie być prawidłowo przechowywane. 

## <a name="basics-of-cosmos-db-as-an-output-target"></a>Podstawy usługi Cosmos DB jako obiekt docelowy danych wyjściowych
Dane wyjściowe usługi Azure Cosmos DB w usłudze Stream Analytics umożliwia zapisywanie strumienia przetwarzanie wyników jako dane wyjściowe JSON do kolekcji usługi Cosmos DB. Stream Analytics nie tworzy kolekcji w bazie danych, zamiast konieczności je utworzyć z wyprzedzeniem. Jest to tak, aby kosztów kolekcji usługi Cosmos DB są kontrolowane przez użytkownika i tak, aby można dostrajanie wydajności, spójności i pojemności bezpośrednio przy użyciu kolekcji [Cosmos DB API](https://msdn.microsoft.com/library/azure/dn781481.aspx). 

Niektóre opcje kolekcję usługi Cosmos DB są szczegółowo opisane poniżej.

## <a name="tune-consistency-availability-and-latency"></a>Dostosowywanie spójnością, dostępnością i opóźnieniem
Aby są zgodne z wymaganiami aplikacji, usługi Azure Cosmos DB umożliwia prawidłowo dostrajania bazy danych i kolekcji i kompromisu między spójnością, dostępnością i opóźnieniem. Zależności od tego, jakie poziomy spójności odczytu wymagań scenariusza dla zapisu i odczytu czas oczekiwania, możesz wybrać poziom spójności na Twoje konto bazy danych. Domyślnie usługa Azure Cosmos DB umożliwia także synchroniczne indeksowanie każdej operacji CRUD, do swojej kolekcji. Jest to przydatne zablokowałoby do kontrolowania wydajności zapisu/odczytu w usłudze Azure Cosmos DB. Aby uzyskać więcej informacji, zobacz [Zmienianie poziomów spójności bazy danych i zapytania](../cosmos-db/consistency-levels.md) artykułu.

## <a name="upserts-from-stream-analytics"></a>Wykonuje operację UPSERT z usługi Stream Analytics
Stream Analytics integracji z usługą Azure Cosmos DB umożliwia wstawianie lub aktualizowania rekordów w kolekcji na podstawie danej kolumny Identyfikatora dokumentu. To jest również nazywany *Upsert*.

Stream Analytics korzysta z metody optymistycznej upsert których aktualizacje są wykonywane tylko podczas wstawiania zakończy się niepowodzeniem z konfliktem identyfikator dokumentu. Ta aktualizacja jest wykonywane jako poprawki, dzięki czemu umożliwia ona aktualizacje częściowe do dokumentu, to znaczy, dodanie nowych właściwości lub zastępując istniejącą właściwość jest wykonywana stopniowo. Jednak zmiany wartości właściwości tablicy w wyniku dokumentu JSON, korzystając z całej tablicy wprowadzenie zastąpione, czyli tablicy nie jest połączony.

## <a name="data-partitioning-in-cosmos-db"></a>Partycjonowanie danych w usłudze Cosmos DB
Usługa Azure Cosmos DB [nieograniczone](../cosmos-db/partition-data.md) są zalecane podejście do partycjonowania danych, jak usługa Azure Cosmos DB automatycznie skaluje partycji na podstawie własnego obciążenia. Podczas zapisywania nieograniczone kontenery, Stream Analytics używa dowolną liczbę równoległych składników zapisywania jako poprzedniego kroku zapytania lub schemat partycjonowania danych wejściowych.
> [!Note]
> W tej chwili usługi Azure Stream Analytics obsługuje nieograniczonych kolekcji tylko przy użyciu kluczy partycji na najwyższym poziomie. Na przykład `/region` jest obsługiwana. Zagnieżdżone klucze partycji (np. `/region/name`) nie są obsługiwane. 

Dla stałej kolekcji usługi Azure Cosmos DB Stream Analytics umożliwia sposób skalować w górę lub w poziomie, gdy są one pełne. Mają górnego limitu 10 GB i 10 000 jednostek RU/s przepływności.  Aby przeprowadzić migrację danych z kontener stały do nieograniczonego kontenera (na przykład jeden z co najmniej 1000 jednostek RU/s i klucz partycji), należy użyć [narzędzia migracji danych](../cosmos-db/import-data.md) lub [biblioteki kanału informacyjnego zmian](../cosmos-db/change-feed.md).

Zapisywanie wielu kontenerów stałej jest on przestarzały i nie jest zalecane podejście do skalowania w poziomie zadania usługi Stream Analytics. Artykuł [partycjonowanie i skalowanie w usłudze Cosmos DB](../cosmos-db/sql-api-partition-data.md) zawiera dalsze szczegóły.

## <a name="cosmos-db-settings-for-json-output"></a>Ustawienia usługi cosmos DB dla danych wyjściowych JSON
Tworzenie usługi Cosmos DB jako dane wyjściowe w usłudze Stream Analytics generuje monit o podanie informacji, jak pokazano poniżej. Ta sekcja zawiera wyjaśnienie definicji właściwości.


![ekranu danych wyjściowych analizy strumienia bazy danych documentdb](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-1.png)

Pole           | Opis 
-------------   | -------------
Alias danych wyjściowych    | Alias do odwoływania się te dane wyjściowe w zapytaniu usługi ASA   
Nazwa konta    | Nazwa lub identyfikator URI konta usługi Azure Cosmos DB punktu końcowego 
Klucz konta     | Klucz dostępu współdzielonego dla konta usługi Azure Cosmos DB
Database (Baza danych)        | Nazwa bazy danych Azure Cosmos DB
Nazwa kolekcji | Nazwa kolekcji dla kolekcji, który ma być używany. `MyCollection` to przykładowe prawidłowe dane wejściowe — jedna kolekcja o nazwie `MyCollection` musi istnieć.  
Identyfikator dokumentu     | Opcjonalny. Nazwa kolumny w zdarzeniach wyjściowych służąca jako unikatowego klucza, na które insert nebo update operacji musi być oparta. Jeśli pole pozostanie puste, wszystkie zdarzenia zostaną wstawione, nie dając aktualizacji.
