---
title: Dane wyjściowe JSON dla usługi Stream Analytics | Dokumentacja firmy Microsoft
description: Dowiedz się, jak Stream Analytics można kierować bazy danych rozwiązania Cosmos Azure dla danych wyjściowych JSON archiwizowania danych i zapytania o małych opóźnieniach na dane JSON bez struktury.
keywords: Dane wyjściowe JSON
documentationcenter: ''
services: stream-analytics,documentdb
author: jseb225
manager: ryanw
ms.assetid: 5d2a61a6-0dbf-4f1b-80af-60a80eb25dd1
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/28/2017
ms.author: jeanb
ms.openlocfilehash: 8bda2abda6f2b7207a5a7195c24b07da9089fb06
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2018
---
# <a name="target-azure-cosmos-db-for-json-output-from-stream-analytics"></a>Docelowej bazy danych rozwiązania Cosmos Azure dla danych wyjściowych JSON z usługi Stream Analytics
Analiza strumienia może kierować [bazy danych Azure rozwiązania Cosmos](https://azure.microsoft.com/services/documentdb/) dla danych wyjściowych JSON, włączanie archiwizacji i małych opóźnieniach kwerend danych na dane JSON bez struktury. W tym dokumencie opisano najważniejsze wskazówki dotyczące implementowania tej konfiguracji.

Dla osób, które znają rozwiązania Cosmos DB, Przyjrzyjmy się [ścieżka szkoleniowa dotycząca usługi Azure rozwiązania Cosmos DB](https://azure.microsoft.com/documentation/learning-paths/documentdb/) rozpocząć pracę. 

> [!Note]
> W tej chwili Azure Stream Analytics obsługuje tylko połączenia za pomocą CosmosDB **interfejsu API SQL**.
> Innych interfejsów API Azure rozwiązania Cosmos bazy danych nie są jeszcze obsługiwane. Jeśli punkt Azure Stream Analytics do kont Azure DB rozwiązania Cosmos tworzone za pomocą innych interfejsów API, dane mogą nie być poprawnie przechowywane. 

## <a name="basics-of-cosmos-db-as-an-output-target"></a>Podstawy DB rozwiązania Cosmos jako miejsce docelowe danych wyjściowych
Dane wyjściowe bazy danych Azure rozwiązania Cosmos w Stream Analytics umożliwia zapisywanie strumienia przetwarzanie wyników jako dane wyjściowe JSON do kolekcji z bazy danych rozwiązania Cosmos. Analiza strumienia nie powoduje utworzenia kolekcji w bazie danych, zamiast konieczności wyprzedzeniem je utworzyć. Jest tak, aby rozliczeń kosztów rozwiązania Cosmos DB kolekcje są niewidoczne dla użytkownika i tak, aby dostroić wydajność, spójność i pojemność kolekcji bezpośrednio za pomocą [rozwiązania Cosmos DB API](https://msdn.microsoft.com/library/azure/dn781481.aspx). Zalecamy użycie jednej bazy danych DB rozwiązania Cosmos na zadanie przesyłania strumieniowego logicznie Rozdziel kolekcji dla zadania przesyłania strumieniowego.

Niektóre opcje kolekcji DB rozwiązania Cosmos są szczegółowo opisane poniżej.

## <a name="tune-consistency-availability-and-latency"></a>Dostosuj spójności, dostępnością i opóźnieniem
Aby była zgodna z wymaganiami aplikacji z DB rozwiązania Cosmos umożliwia prawidłowo dostrajania bazy danych i kolekcji i wprowadzić kompromis między spójności, dostępności i opóźnień. W zależności od tego, jakiego poziomu spójności odczytu potrzeb scenariusz przed zapisu i odczytu opóźnienia, możesz wybrać poziom spójności na konta bazy danych. Domyślnie DB rozwiązania Cosmos umożliwia również synchroniczne indeksowania na każdej operacji CRUD do kolekcji. To jest inną opcją przydatne do kontrolowania wydajności zapisu/odczytu w bazie danych rozwiązania Cosmos. Aby uzyskać więcej informacji na ten temat, zapoznaj się [Zmień poziomy spójności bazy danych i zapytania](../cosmos-db/consistency-levels.md) artykułu.

## <a name="upserts-from-stream-analytics"></a>Upserts z usługi Stream Analytics
Stream Analytics integracji z rozwiązania Cosmos DB umożliwia wstawić lub zaktualizować rekord w kolekcji rozwiązania Cosmos bazy danych na podstawie danego kolumny Identyfikator dokumentu. To jest również nazywany *Upsert*.

Stream Analytics wykorzystuje optymistycznej podejście Upsert, w której aktualizacje są wykonywane tylko po insert nie powiedzie się z powodu konfliktu identyfikator dokumentu. Ta aktualizacja jest wykonywane przez Stream Analytics poprawek, dlatego umożliwia korzystanie z częściowa aktualizacji do dokumentu, tj. dodanie nowych właściwości lub zastąpienie istniejącej właściwości jest wykonywana stopniowo. Należy pamiętać, że zmiany w wartości właściwości tablicy w wyników dokumentu JSON w tablicy całego pobierania zastąpione, tj. tablicy nie są scalane.

## <a name="data-partitioning-in-cosmos-db"></a>Partycjonowanie danych w bazie danych rozwiązania Cosmos
Rozwiązania cosmos DB [kolekcje partycjonowane](../cosmos-db/partition-data.md) są zalecane podejście do partycjonowania danych. 

Dla jednej kolekcji rozwiązania Cosmos DB Stream Analytics nadal umożliwia partycji danych na podstawie wzorców zapytań i wymagania dotyczące wydajności aplikacji. Każda kolekcja może zawierać maksymalnie 10GB danych (maksymalnie), a obecnie nie istnieje sposób skalowanie w górę (lub przepełnienie) kolekcji. Do skalowania, Stream Analytics umożliwia pisanie w wielu kolekcjach z danego prefiksu (zobacz poniżej szczegóły obciążenia). Stream Analytics korzysta z spójne [rozpoznawania partycji skrótu](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.partitioning.hashpartitionresolver.aspx) strategii na podstawie użytkownika podane kolumny PartitionKey do partycjonowania jego rekordów danych wyjściowych. Liczba kolekcji z danego prefiksu podczas uruchamiania zadania przesyłania strumieniowego jest używany jako dane wyjściowe liczba partycji, do którego zadanie zapisuje równoległe (kolekcje DB rozwiązania Cosmos = partycje dane wyjściowe). Dla jednej kolekcji z opóźnieniem indeksowania czynności tylko wstawia, można oczekiwać o 0,4 przepływność zapisu MB/s. Przy użyciu wielu kolekcji umożliwiają osiągnięcie wyższej przepustowości i zwiększenia pojemności.

Jeśli planujesz zwiększyć liczbę partycji w przyszłości, może być konieczne zatrzymanie zadania, partycje danych z istniejącej kolekcji do nowej kolekcji, a następnie ponownie uruchom zadanie usługi Stream Analytics. Więcej informacji na temat przy użyciu PartitionResolver i ponownie partycjonowania wraz z przykładowym kodzie będą uwzględniane w kolejnych post. Artykuł [dzielenia na partycje i skalowania w bazie danych rozwiązania Cosmos](../cosmos-db/sql-api-partition-data.md) także szczegółowe informacje na ten.

## <a name="cosmos-db-settings-for-json-output"></a>Ustawienia rozwiązania cosmos bazy danych dla danych wyjściowych JSON
Tworzenie rozwiązania Cosmos bazy danych jako dane wyjściowe w Stream Analytics generuje monit o podanie informacji, jak pokazano poniżej. Ta sekcja zawiera wyjaśnienie definicji właściwości.

Kolekcja podzielonym na partycje | Wiele kolekcji "Jednej partycji"
---|---
![documentdb stream analytics dane wyjściowe ekranu](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-1.png) |  ![documentdb stream analytics dane wyjściowe ekranu](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-2.png)


  
> [!NOTE]
> **Wielu kolekcji "Jednej partycji"** scenariusz wymaga klucza partycji i konfiguracja jest obsługiwana. 

* **Dane wyjściowe Alias** — jako alias, aby znaleźć te dane wyjściowe w kwerendzie ASA  
* **Nazwa konta** — nazwa lub identyfikator URI konta DB rozwiązania Cosmos punktu końcowego.  
* **Klucz konta** — współużytkowanego klucza dostępu dla konta DB rozwiązania Cosmos.  
* **Baza danych** — Nazwa bazy danych DB rozwiązania Cosmos.  
* **Wzorzec nazwy kolekcji** — Nazwa kolekcji lub ich wzorca kolekcji do użycia. Format nazw kolekcji można skonstruować przy użyciu tokenu opcjonalne {partition}, gdzie partycje zaczynają się od 0. Poniżej przedstawiono przykładowe prawidłowe wartości wejściowe:  
  1\) MyCollection — jedną kolekcję o nazwie "MyCollection" musi istnieć.  
  2\) MyCollection {partition} — takie kolekcje muszą istnieć — "MyCollection0", "MyCollection1", "MyCollection2" itd.  
* **Klucz partycji** — jest to opcjonalne. Jest to potrzebne tylko, jeśli używasz tokenu {partition} we wzorcu nazwy Twojej kolekcji. Nazwa pola w zdarzeniach wyjściowych służąca do określenia klucza do partycjonowania danych wyjściowych na kolekcje. Dla danych wyjściowych jednej kolekcji, wszystkie kolumny wyjściowej dowolnego mogą być używane np. PartitionId.  
* **Identyfikator dokumentu** — jest to opcjonalne. Nazwa pola w zdarzeniach wyjściowych służąca do określenia klucza podstawowego, na które insert lub update bazują operacje.  
