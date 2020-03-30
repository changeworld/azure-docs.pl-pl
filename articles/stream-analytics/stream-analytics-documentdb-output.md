---
title: Usługa Azure Stream Analytics do usługi Azure Cosmos DB
description: W tym artykule opisano sposób używania usługi Azure Stream Analytics do zapisywania danych wyjściowych w usłudze Azure Cosmos DB dla danych wyjściowych JSON, do archiwizacji danych i zapytań o małym opóźnieniu dotyczących nieustrukturyzowanych danych JSON.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 02/2/2020
ms.custom: seodec18
ms.openlocfilehash: e58e36b3caa5a5ecd137cb9cb61dad7ddb95ff3a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79254445"
---
# <a name="azure-stream-analytics-output-to-azure-cosmos-db"></a>Usługa Azure Stream Analytics do usługi Azure Cosmos DB  
Usługa Azure Stream Analytics może kierować [usługę Azure Cosmos DB](https://azure.microsoft.com/services/documentdb/) dla danych wyjściowych JSON, umożliwiając archiwizację danych i zapytania o małym opóźnieniu na nieustrukturyzowanych danych JSON. Ten dokument zawiera kilka najlepszych rozwiązań dotyczących implementacji tej konfiguracji.

Jeśli nie znasz usługi Azure Cosmos DB, zapoznaj się z [dokumentacją usługi Azure Cosmos DB,](https://docs.microsoft.com/azure/cosmos-db/) aby rozpocząć. 

> [!Note]
> W tej chwili usługa Stream Analytics obsługuje połączenie z usługą Azure Cosmos DB tylko za pośrednictwem *interfejsu API SQL.*
> Inne interfejsy API bazy danych usługi Azure Cosmos nie są jeszcze obsługiwane. Jeśli wskażesz usługę Stream Analytics na konta usługi Azure Cosmos DB utworzone za pomocą innych interfejsów API, dane mogą nie być poprawnie przechowywane. 

## <a name="basics-of-azure-cosmos-db-as-an-output-target"></a>Podstawy usługi Azure Cosmos DB jako docelowego produktu wyjściowego
Dane wyjściowe usługi Azure Cosmos DB w usłudze Stream Analytics umożliwia zapisywanie wyników przetwarzania strumienia jako danych wyjściowych JSON do kontenerów usługi Azure Cosmos DB. 

Usługa Stream Analytics nie tworzy kontenerów w bazie danych. Zamiast tego wymaga tworzenia ich z góry. Następnie można kontrolować koszty rozliczeń kontenerów usługi Azure Cosmos DB. Można również dostroić wydajność, spójność i pojemność kontenerów bezpośrednio przy użyciu [interfejsów API bazy danych usługi Azure Cosmos DB.](https://msdn.microsoft.com/library/azure/dn781481.aspx)

> [!Note]
> Musisz dodać 0.0.0.0 do listy dozwolonych serwerów IP z zapory usługi Azure Cosmos DB.

W poniższych sekcjach szczegółowo niektóre opcje kontenera dla usługi Azure Cosmos DB.

## <a name="tuning-consistency-availability-and-latency"></a>Spójność, dostępność i opóźnienie dostrajania
Aby dopasować wymagania aplikacji, usługa Azure Cosmos DB umożliwia dostosowanie bazy danych i kontenerów oraz wprowadzanie kompromisów między spójnością, dostępnością, opóźnieniem i przepływnością. 

W zależności od poziomów spójności odczytu scenariusz potrzebuje przed opóźnienia odczytu i zapisu, można wybrać poziom spójności na koncie bazy danych. Można zwiększyć przepływność, skalując jednostki żądań (RUs) w kontenerze. 

Również domyślnie usługi Azure Cosmos DB umożliwia indeksowanie synchroniczne dla każdej operacji CRUD do kontenera. Jest to kolejna przydatna opcja do kontrolowania wydajności zapisu/odczytu w usłudze Azure Cosmos DB. 

Aby uzyskać więcej informacji, zapoznaj się z artykułem [Zmienianie poziomów spójności bazy danych i zapytań.](../cosmos-db/consistency-levels.md)

## <a name="upserts-from-stream-analytics"></a>Upserts z usługi Stream Analytics
Integracja usługi Stream Analytics z usługą Azure Cosmos DB umożliwia wstawianie lub aktualizowanie rekordów w kontenerze na podstawie danej kolumny **identyfikator dokumentu.** Jest to również nazywane *upsert*.

Usługa Stream Analytics wykorzystuje optymistyczne podejście upsert. Aktualizacje są nachodnie tylko wtedy, gdy wstawianie nie powiedzie się z konfliktem identyfikatora dokumentu. 

Z poziomem zgodności 1.0 usługa Stream Analytics wykonuje tę aktualizację jako operację PATCH, dzięki czemu umożliwia częściowe aktualizacje dokumentu. Usługa Stream Analytics dodaje nowe właściwości lub zastępuje istniejącą właściwość przyrostowo. Jednak zmiany wartości właściwości tablicy w dokumencie JSON powodują zastąpienie całej tablicy. Oznacza to, że tablica nie jest scalana. 

W 1.2 zachowanie upsert jest modyfikowane w celu wstawienia lub wymiany dokumentu. W dalszej części dotyczącej poziomu zgodności 1.2 opisano to zachowanie.

Jeśli przychodzący dokument JSON ma istniejące pole identyfikatora, to pole jest automatycznie używane jako **kolumna Identyfikator dokumentu** w usłudze Azure Cosmos DB. Wszelkie kolejne zapisy są traktowane jako takie, co prowadzi do jednej z następujących sytuacji:

- Unikatowe identyfikatory prowadzą do wstawienia.
- Zduplikowane identyfikatory i **identyfikator dokumentu** ustawione na **identyfikator** prowadzą do upsert.
- Zduplikowane identyfikatory i **identyfikator dokumentu,** które nie są ustawione, prowadzą do błędu po pierwszym dokumencie.

Jeśli chcesz zapisać *wszystkie* dokumenty, w tym te, które mają zduplikowany identyfikator, zmień nazwę pola identyfikatora w zapytaniu (używając słowa kluczowego **AS).** Pozwól usłudze Azure Cosmos DB utworzyć pole identyfikatora lub zastąpić identyfikator wartością innej kolumny (przy użyciu słowa kluczowego **AS** lub przy użyciu ustawienia **identyfikatora dokumentu).**

## <a name="data-partitioning-in-azure-cosmos-db"></a>Partycjonowanie danych w usłudze Azure Cosmos DB
Usługa Azure Cosmos DB automatycznie skaluje partycje na podstawie obciążenia. Dlatego zalecamy [nieograniczone](../cosmos-db/partition-data.md) kontenery jako podejście do partycjonowania danych. Gdy usługa Stream Analytics zapisuje do nieograniczonej liczby kontenerów, używa tyle równoległych modułów zapisujących, ile w poprzednim kroku kwerendy lub schematu partycjonowania wejściowego.

> [!NOTE]
> Usługa Azure Stream Analytics obsługuje tylko nieograniczone kontenery z kluczami partycji na najwyższym poziomie. Na przykład `/region` jest obsługiwany. Zagnieżdżone klucze partycji (na przykład `/region/name`) nie są obsługiwane. 

W zależności od wybranego klucza partycji może pojawić się _to ostrzeżenie:_

`CosmosDB Output contains multiple rows and just one row per partition key. If the output latency is higher than expected, consider choosing a partition key that contains at least several hundred records per partition key.`

Ważne jest, aby wybrać właściwość klucza partycji, która ma wiele różnych wartości i która pozwala równomiernie rozłożyć obciążenie na te wartości. Jako naturalny artefakt partycjonowania żądania, które zawierają ten sam klucz partycji są ograniczone przez maksymalną przepływność pojedynczej partycji. 

Rozmiar magazynu dla dokumentów, które należą do tego samego klucza partycji jest ograniczona do 10 GB. Idealny klucz partycji jest taki, który pojawia się często jako filtr w zapytaniach i ma wystarczającą kardynalność, aby upewnić się, że rozwiązanie jest skalowalne.

Klucz partycji jest również granicą dla transakcji w procedurach przechowywanych i wyzwalaczy dla usługi Azure Cosmos DB. Należy wybrać klucz partycji, tak aby dokumenty, które występują razem w transakcjach współużytkować tę samą wartość klucza partycji. Artykuł [Partycjonowanie w usłudze Azure Cosmos DB](../cosmos-db/partitioning-overview.md) zawiera więcej szczegółów na temat wybierania klucza partycji.

W przypadku stałych kontenerów usługi Azure Cosmos DB usługa Stream Analytics umożliwia niemoże skalować w górę ani w poziomie po ich zapełnienia. Mają górny limit 10 GB i 10 000 ru/s przepływności. Aby przeprowadzić migrację danych ze stałego kontenera do kontenera nieograniczonego (na przykład jednego z co najmniej 1000 RU/s i kluczem partycji), użyj [narzędzia do migracji danych](../cosmos-db/import-data.md) lub [biblioteki pliku danych zmian](../cosmos-db/change-feed.md).

Możliwość zapisu do wielu stałych kontenerów jest przestarzałe. Nie zalecamy skalowania zadania usługi Stream Analytics.

## <a name="improved-throughput-with-compatibility-level-12"></a>Większa przepustowość dzięki poziomowi zgodności 1.2
Dzięki poziomowi zgodności 1.2 usługa Stream Analytics obsługuje natywną integrację w celu zbiorczego zapisu w usłudze Azure Cosmos DB. Ta integracja umożliwia efektywne zapisywanie do usługi Azure Cosmos DB przy jednoczesnej maksymalizacji przepływności i wydajnej obsługi żądań ograniczania przepustowości. 

Ulepszony mechanizm zapisu jest dostępny w nowym poziomie zgodności ze względu na różnicę w zachowaniu upsert. W przypadku poziomów przed 1.2 zachowanie upsert polega na wstawieniu lub scaleniu dokumentu. W 1.2 zachowanie upsert jest modyfikowane w celu wstawienia lub wymiany dokumentu.

Z poziomami przed 1.2, Usługa Stream Analytics używa niestandardowej procedury składowanej do zbiorczego dostosowania dokumentów uperowych na klucz partycji do usługi Azure Cosmos DB. Tam partia jest zapisywana jako transakcja. Nawet wtedy, gdy pojedynczy rekord trafi błąd przejściowy (ograniczanie przepustowości), cała partia musi zostać ponowiona. To sprawia, że scenariusze z nawet rozsądne ograniczanie stosunkowo powolne.

W poniższym przykładzie przedstawiono dwa identyczne zadania usługi Stream Analytics odczytywania z tego samego wpisu usługi Azure Event Hubs. Oba zadania usługi Stream Analytics są [w pełni podzielone na partycje](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization#embarrassingly-parallel-jobs) za pomocą kwerendy przekazowej i zapisują do identycznych kontenerów usługi Azure Cosmos DB. Metryki po lewej stronie pochodzą z zadania skonfigurowane z poziomem zgodności 1.0. Metryki po prawej stronie są skonfigurowane z 1.2. Klucz partycji kontenera usługi Azure Cosmos DB to unikatowy identyfikator GUID pochodzący ze zdarzenia wejściowego.

![Porównanie danych usługi Stream Analytics](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-3.png)

Szybkość zdarzeń przychodzących w centrach zdarzeń jest dwa razy wyższa niż kontenery usługi Azure Cosmos DB (20 000 procesorów RUs) są skonfigurowane do podjęcia, więc ograniczanie jest oczekiwane w usłudze Azure Cosmos DB. Jednak zadanie z 1.2 jest konsekwentnie zapisuje przy wyższej przepływności (zdarzenia wyjściowe na minutę) i przy niższym średnim wykorzystaniu SU%. W twoim środowisku różnica ta będzie zależeć od kilku czynników. Czynniki te obejmują wybór formatu zdarzenia, rozmiar zdarzenia/wiadomości wejściowej, klucze partycji i kwerendy.

![Porównanie metryk usługi Azure Cosmos DB](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-2.png)

W 1.2 usługa Stream Analytics jest bardziej inteligentna w wykorzystaniu 100 procent dostępnej przepływności w usłudze Azure Cosmos DB przy bardzo niewielu ponownych przedsprzedarzenia z ograniczania przepustowości lub ograniczania szybkości. Zapewnia to lepsze środowisko dla innych obciążeń, takich jak zapytania uruchomione w kontenerze w tym samym czasie. Jeśli chcesz zobaczyć, jak usługa Stream Analytics skaluje się w poziomie za pomocą usługi Azure Cosmos DB jako ujście dla 1000 do 10 000 wiadomości na sekundę, wypróbuj [ten przykładowy projekt platformy Azure.](https://github.com/Azure-Samples/streaming-at-scale/tree/master/eventhubs-streamanalytics-cosmosdb)

Przepływność danych wyjściowych usługi Azure Cosmos DB jest identyczna z 1.0 i 1.1. *Zdecydowanie zaleca się* używanie poziomu zgodności 1.2 w usłudze Stream Analytics z usługą Azure Cosmos DB.

## <a name="azure-cosmos-db-settings-for-json-output"></a>Ustawienia usługi Azure Cosmos DB dla danych wyjściowych JSON

Korzystanie z usługi Azure Cosmos DB jako dane wyjściowe w usłudze Stream Analytics generuje następujący monit o informacje.

![Pola informacyjne dla strumienia wyjściowego usługi Azure Cosmos DB](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-1.png)

|Pole           | Opis|
|-------------   | -------------|
|Alias danych wyjściowych    | Alias, który ma się odwoływać do tego danych wyjściowych w zapytaniu usługi Stream Analytics.|
|Subskrypcja    | Subskrypcja platformy Azure.|
|Identyfikator konta      | Nazwa lub identyfikator URI punktu końcowego konta usługi Azure Cosmos DB.|
|Klucz konta     | Klucz dostępu współdzielonego dla konta usługi Azure Cosmos DB.|
|baza danych        | Nazwa bazy danych usługi Azure Cosmos DB.|
|Nazwa kontenera | Nazwa kontenera, `MyContainer`na przykład . Jeden kontener `MyContainer` o nazwie musi istnieć.  |
|Identyfikator dokumentu     | Element opcjonalny. Nazwa kolumny w zdarzeniach wyjściowych używanych jako unikatowy klucz, na którym muszą być oparte operacje wstawiania lub aktualizacji. Jeśli pozostawisz go pustym, wszystkie zdarzenia zostaną wstawione, bez opcji aktualizacji.|

Po skonfigurowaniu danych wyjściowych usługi Azure Cosmos DB można go używać w kwerendzie jako obiektu docelowego [instrukcji INTO.](https://docs.microsoft.com/stream-analytics-query/into-azure-stream-analytics) Gdy używasz danych wyjściowych usługi Azure Cosmos DB w ten sposób, [klucz partycji musi być ustawiony jawnie.](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization#partitions-in-sources-and-sinks) 

Rekord danych wyjściowych musi zawierać kolumnę z uwzględnieniem wielkości liter o nazwie po kluczu partycji w usłudze Azure Cosmos DB. Aby osiągnąć większą równoległość, instrukcja może wymagać [klauzuli PARTITION BY,](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization#embarrassingly-parallel-jobs) która używa tej samej kolumny.

Oto przykładowe zapytanie:

```SQL
    SELECT TollBoothId, PartitionId
    INTO CosmosDBOutput
    FROM Input1 PARTITION BY PartitionId
``` 

## <a name="error-handling-and-retries"></a>Obsługa błędów oraz wykonywanie ponownych prób

Jeśli przejściowy błąd, niedostępność usługi lub ograniczanie przepustowości dzieje się podczas usługi Stream Analytics wysyła zdarzenia do usługi Azure Cosmos DB, usługa Stream Analytics ponawia próby na czas nieokreślony, aby pomyślnie zakończyć operację. Ale nie próbuje ponownych prób dla następujących błędów:

- Nieautoryzowane (kod błędu HTTP 401)
- NotFound (kod błędu HTTP 404)
- Zabronione (kod błędu HTTP 403)
- BadRequest (kod błędu HTTP 400)
