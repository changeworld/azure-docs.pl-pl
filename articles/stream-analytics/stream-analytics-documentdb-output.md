---
title: Azure Stream Analytics dane wyjściowe do Cosmos DB
description: W tym artykule opisano, jak używać Azure Stream Analytics do zapisywania danych wyjściowych do Azure Cosmos DB na potrzeby danych wyjściowych JSON, do archiwizacji danych i zapytań o małym opóźnieniu dla danych JSON bez struktury.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 01/11/2019
ms.custom: seodec18
ms.openlocfilehash: aa4ac011a7b6258958ac1ac176fd63b18a4ef856
ms.sourcegitcommit: c31dbf646682c0f9d731f8df8cfd43d36a041f85
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/27/2019
ms.locfileid: "74560184"
---
# <a name="azure-stream-analytics-output-to-azure-cosmos-db"></a>Azure Stream Analytics dane wyjściowe do Azure Cosmos DB  
Stream Analytics może kierować [Azure Cosmos DB](https://azure.microsoft.com/services/documentdb/) do danych wyjściowych JSON, co umożliwia archiwizowanie danych i uruchamianie zapytań o małym opóźnieniu na dane JSON bez struktury. W tym dokumencie opisano najlepsze rozwiązania dotyczące wdrażania tej konfiguracji.

W przypadku osób, które nie znają Cosmos DB, zapoznaj się ze [ścieżką uczenia Azure Cosmos DB](https://azure.microsoft.com/documentation/learning-paths/documentdb/) , aby rozpocząć pracę. 

> [!Note]
> W tej chwili Azure Stream Analytics obsługuje tylko połączenie z Azure Cosmos DB przy użyciu **interfejsu API SQL**.
> Inne interfejsy API Azure Cosmos DB nie są jeszcze obsługiwane. Jeśli użytkownik wskaże Azure Stream Analytics kont Azure Cosmos DB utworzonych z innymi interfejsami API, dane mogą nie być prawidłowo przechowywane. 

## <a name="basics-of-cosmos-db-as-an-output-target"></a>Podstawy Cosmos DB jako miejsce docelowe danych wyjściowych
Azure Cosmos DB dane wyjściowe w Stream Analytics umożliwiają zapisywanie wyników przetwarzania strumienia jako danych wyjściowych JSON w kontenerach Cosmos DB. Stream Analytics nie tworzy kontenerów w bazie danych, ale wymaga utworzenia ich z góry. Jest to tak, aby koszty rozliczania kontenerów Cosmos DB są kontrolowane przez użytkownika i aby można było dostosować wydajność, spójność i pojemność kontenerów bezpośrednio przy użyciu [Cosmos DB interfejsów API](https://msdn.microsoft.com/library/azure/dn781481.aspx).

> [!Note]
> Należy dodać 0.0.0.0 do listy dozwolonych adresów IP z zapory Azure Cosmos DBej.

Poniżej przedstawiono niektóre opcje kontenera Cosmos DB.

## <a name="tune-consistency-availability-and-latency"></a>Dostrajanie spójności, dostępności i opóźnień
Aby dopasować wymagania dotyczące aplikacji, Azure Cosmos DB umożliwia precyzyjne dostosowanie bazy danych i kontenerów oraz wprowadzenie kompromisu między spójnością, dostępnością, opóźnieniami i przepływności. W zależności od poziomów spójności odczytu potrzebnych do odczytu i zapisu można wybrać poziom spójności na koncie bazy danych. Przepustowość można ulepszyć przez skalowanie jednostek żądań (jednostek ru) w kontenerze. Domyślnie Azure Cosmos DB umożliwia indeksowanie synchroniczne dla każdej operacji CRUD w kontenerze. Jest to kolejna przydatna opcja kontrolująca wydajność zapisu/odczytu w Azure Cosmos DB. Aby uzyskać więcej informacji, zapoznaj się z artykułem [Zmienianie poziomów spójności bazy danych i zapytań](../cosmos-db/consistency-levels.md) .

## <a name="upserts-from-stream-analytics"></a>Upserts z Stream Analytics
Integracja Stream Analytics z Azure Cosmos DB umożliwia wstawianie lub aktualizowanie rekordów w kontenerze na podstawie danej kolumny identyfikatora dokumentu. Jest to również nazywane *upsert*.

Stream Analytics używa podejścia optymistycznego Upsert, w którym aktualizacje są wykonywane tylko wtedy, gdy operacja INSERT kończy się niepowodzeniem z powodu konfliktu identyfikatorów dokumentów. W przypadku poziomu zgodności 1,0 Ta aktualizacja jest wykonywana jako poprawka, dzięki czemu umożliwia częściową aktualizację dokumentu, czyli dodanie nowych właściwości lub zastąpienie istniejącej właściwości jest wykonywane przyrostowo. Jednak zmiany wartości właściwości tablicy w dokumencie JSON powodują przesłonięcie całej tablicy, czyli nie są scalone. W przypadku 1,2 upsert zachowanie jest modyfikowane w celu wstawienia lub zamiany dokumentu. Opisano to dokładniej w poniższej sekcji poziom zgodności 1,2.

Jeśli dokument przychodzącego JSON ma istniejące pole identyfikatora, to pole jest automatycznie używane jako kolumna Identyfikator dokumentu w Cosmos DB i wszystkie kolejne operacje zapisu są obsługiwane w taki sposób, co może prowadzić do jednej z następujących sytuacji:
- unikatowe identyfikatory prowadzące do wstawienia
- zduplikowane identyfikatory i "Identyfikator dokumentu" są dla liderów upsert
- zduplikowane identyfikatory i "Identyfikator dokumentu" nie zostały ustawione na błąd, po pierwszym dokumencie

Jeśli chcesz zapisać <i>wszystkie</i> dokumenty, w tym te z powielonym identyfikatorem, Zmień nazwę pola ID w zapytaniu (za pomocą słowa kluczowego AS) i pozwól Cosmos DB utworzyć pole ID lub ZAmienić identyfikator na wartość innej kolumny (za pomocą słowa kluczowego AS lub przy użyciu ustawienia "Identyfikator dokumentu").

## <a name="data-partitioning-in-cosmos-db"></a>Partycjonowanie danych w Cosmos DB
Azure Cosmos DB [nieograniczone](../cosmos-db/partition-data.md) kontenery to zalecane podejście do partycjonowania danych, ponieważ Azure Cosmos DB automatycznie skaluje partycje na podstawie obciążenia. Podczas zapisywania do nieograniczonych kontenerów Stream Analytics używa jako wielu modułów zapisujących równoległych jako poprzedni krok zapytania lub schemat partycjonowania danych wejściowych.
> [!NOTE]
> W tej chwili Azure Stream Analytics obsługuje tylko nieograniczone kontenery z kluczami partycji na najwyższym poziomie. Na przykład `/region` jest obsługiwana. Zagnieżdżone klucze partycji (np. `/region/name`) nie są obsługiwane. 

W zależności od wybranego klucza partycji mogą pojawić się następujące _Ostrzeżenie_:

`CosmosDB Output contains multiple rows and just one row per partition key. If the output latency is higher than expected, consider choosing a partition key that contains at least several hundred records per partition key.`

Ważne jest, aby wybrać właściwość klucza partycji, która ma różne wartości, i umożliwia równomierne dystrybuowanie obciążeń między tymi wartościami. Naturalnym artefaktem partycjonowania żądania związane z tym samym kluczem partycji są ograniczone przez maksymalną przepływność pojedynczej partycji. Ponadto rozmiar magazynu dla dokumentów należących do tego samego klucza partycji jest ograniczony do 10 GB. Idealnym kluczem partycji jest ten, który pojawia się często jako filtr w zapytaniach i ma wystarczającą Kardynalność, aby zapewnić skalowalność rozwiązania.

Klucz partycji jest również granicą dla transakcji w procedurach i wyzwalaczach przechowywanych w programie DocumentDB. Należy wybrać klucz partycji, aby dokumenty, które występują razem w transakcjach miały tę samą wartość klucza partycji. [Partycjonowanie artykułu w Cosmos DB](../cosmos-db/partitioning-overview.md) zawiera więcej szczegółów na temat wybierania klucza partycji.

W przypadku kontenerów o stałym Azure Cosmos DB Stream Analytics nie pozwala na skalowanie w górę i w dół po ich zapełnieniu. Mają górny limit wynoszący 10 GB i 10 000 RU/s.  Aby przeprowadzić migrację danych ze stałego kontenera do nieograniczonego kontenera (na przykład jeden z co najmniej 1 000 RU/s i kluczem partycji), należy użyć [Narzędzia do migracji danych](../cosmos-db/import-data.md) lub [biblioteki źródła zmian](../cosmos-db/change-feed.md).

Możliwość zapisu w wielu stałych kontenerach jest przestarzała i nie jest zalecana do skalowania zadania Stream Analytics.

## <a name="improved-throughput-with-compatibility-level-12"></a>Zwiększona przepływność z poziomem zgodności 1,2
W przypadku poziomu zgodności 1,2 Stream Analytics obsługuje natywną integrację do zapisu zbiorczego w Cosmos DB. Dzięki temu można w sposób efektywny zapisywać Cosmos DB z maksymalizacją przepływności i wydajnie obsługiwać żądania ograniczania. Ulepszony mechanizm pisania jest dostępny w ramach nowego poziomu zgodności z powodu różnicy zachowania upsert.  Przed 1,2m, zachowanie upsert polega na wstawieniu lub scaleniu dokumentu. W przypadku 1,2 upserts zachowanie jest modyfikowane w celu wstawienia lub zamiany dokumentu.

Przed 1,2, program używa niestandardowej procedury składowanej do zbiorczego upsert dokumentów na klucz partycji w Cosmos DB, gdzie partia jest zapisywana jako transakcja. Nawet wtedy, gdy pojedynczy rekord osiągnie błąd przejściowy (ograniczanie), cała partia musi być ponowiona. Dzięki temu scenariusze z równomiernym ograniczeniem przepustowości są stosunkowo wolniejsze. Poniższe porównanie pokazuje, jak te zadania byłyby zachowane z 1,2.

W poniższym przykładzie przedstawiono dwa identyczne zadania Stream Analytics odczytywane z tego samego danych wejściowych centrum zdarzeń. Oba zadania Stream Analytics są w [pełni podzielone na partycje](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization#embarrassingly-parallel-jobs) przy użyciu kwerendy przekazującej i zapisu w identycznych kontenerach CosmosDB. Metryki po lewej stronie pochodzą z zadania skonfigurowanego z poziomem zgodności 1,0, a te z prawej strony są skonfigurowane z 1,2. Klucz partycji kontenera Cosmos DB jest unikatowym identyfikatorem GUID, który pochodzi ze zdarzenia wejściowego.

![Porównanie metryk usługi Stream Analytics](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-3.png)

Szybkość zdarzeń przychodzących w centrum zdarzeń jest większa niż w przypadku kontenerów Cosmos DB (20 000 jednostek ru), dlatego w Cosmos DB. Jednak zadanie o 1,2, regularnie zapisuje w wyższej przepływności (zdarzenia wyjściowe/minutę) i z niższym średnim użyciem SU%. W danym środowisku ta różnica będzie zależeć od kilku innych czynników, takich jak wybór formatu zdarzeń, zdarzeń wejściowych/rozmiaru komunikatu, kluczy partycji, kwerendy itp.

![Porównanie metryk Cosmos DB](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-2.png)

Dzięki 1,2 Stream Analytics jest bardziej inteligentna w korzystaniu z 100% dostępnej przepływności w Cosmos DB z nielicznymi reterminami z ograniczeniami ograniczenia przepustowości. Zapewnia to lepszy komfort obsługi innych obciążeń, takich jak zapytania uruchomione w kontenerze w tym samym czasie. Jeśli chcesz wypróbować sposób skalowania ASA do Cosmos DB jako ujścia dla od 1000 do 10 000 komunikatów/sekundę, Oto [projekt przykładów platformy Azure](https://github.com/Azure-Samples/streaming-at-scale/tree/master/eventhubs-streamanalytics-cosmosdb) , który umożliwia wykonanie tej czynności.
Należy pamiętać, że przepływność wyjściowa Cosmos DB jest taka sama jak w przypadku 1,0 i 1,1. Ponieważ 1,2 nie jest obecnie domyślnym, można [ustawić poziom zgodności](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-compatibility-level) dla zadania Stream Analytics przy użyciu portalu lub za pomocą [wywołania interfejsu API Rest tworzenia zadania](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-job). *Zdecydowanie zaleca* się używanie poziomu zgodności 1,2 w ASA z Cosmos DB.



## <a name="cosmos-db-settings-for-json-output"></a>Ustawienia Cosmos DB dla danych wyjściowych JSON

Tworzenie Cosmos DB jako danych wyjściowych w Stream Analytics generuje monit o podanie informacji, jak pokazano poniżej. Ta sekcja zawiera wyjaśnienie definicji właściwości.

![ekran danych wyjściowych usługi Stream Analytics DocumentDB](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-1.png)

|Pole           | Opis|
|-------------   | -------------|
|Alias danych wyjściowych    | Alias do odwoływania danych wyjściowych w zapytaniu ASA.|
|Subskrypcja    | Wybierz subskrypcję platformy Azure.|
|Identyfikator konta      | Nazwa lub identyfikator URI punktu końcowego konta Azure Cosmos DB.|
|Klucz konta     | Współużytkowany klucz dostępu dla konta Azure Cosmos DB.|
|Database (Baza danych)        | Nazwa bazy danych Azure Cosmos DB.|
|Nazwa kontenera | Nazwa kontenera do użycia. `MyContainer` to przykładowa prawidłowa wartość wejściowa — jeden kontener o nazwie `MyContainer` musi istnieć.  |
|Identyfikator dokumentu     | Opcjonalny. Nazwa kolumny w zdarzeniach wyjściowych używanych jako unikatowy klucz, na którym muszą być oparte operacje INSERT lub Update. Jeśli pole pozostanie puste, zostaną wstawione wszystkie zdarzenia bez opcji aktualizacji.|

Po skonfigurowaniu danych wyjściowych Cosmos DB może być używany w zapytaniu jako element docelowy [instrukcji into](https://docs.microsoft.com/stream-analytics-query/into-azure-stream-analytics). W przypadku korzystania z Cosmos DB danych wyjściowych jako takich należy [jawnie ustawić klucz partycji](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization#partitions-in-sources-and-sinks). Rekord wyjściowy musi zawierać kolumnę z uwzględnieniem wielkości liter o nazwie po kluczu partycji w Cosmos DB. Aby osiągnąć większą przetwarzanie równoległe, instrukcja może wymagać użycia [klauzuli Partition by](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization#embarrassingly-parallel-jobs) w tej samej kolumnie.

**Przykładowe zapytanie**:

```SQL
    SELECT TollBoothId, PartitionId
    INTO CosmosDBOutput
    FROM Input1 PARTITION BY PartitionId
``` 

## <a name="error-handling-and-retries"></a>Obsługa błędów i ponawianie prób

W przypadku awarii przejściowej Usługa niedostępna lub ograniczanie przepustowości podczas wysyłania zdarzeń do Cosmos DB, Stream Analytics ponawianie prób w celu pomyślnego zakończenia operacji. Jednak istnieją pewne błędy, w przypadku których ponowne próby nie wykonywane. Są to:

- Nieautoryzowany (kod błędu HTTP 401)
- NotFound (kod błędu HTTP 404)
- Zabronione (kod błędu HTTP 403)
- Nieprawidłowego żądania (kod błędu HTTP 400)
