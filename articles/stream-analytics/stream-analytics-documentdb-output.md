---
title: Usługa Azure Stream Analytics danych wyjściowych Cosmos DB
description: W tym artykule opisano sposób użycia usługi Azure Stream Analytics można zapisać danych wyjściowych do usługi Azure Cosmos DB dla danych wyjściowych JSON, archiwizowania danych i zapytań o małych opóźnieniach na danych JSON bez struktury.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 01/11/2019
ms.custom: seodec18
ms.openlocfilehash: 52bbb52b13a3606e3ddc8deca2da8505233c9352
ms.sourcegitcommit: 388c8f24434cc96c990f3819d2f38f46ee72c4d8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/27/2019
ms.locfileid: "70062013"
---
# <a name="azure-stream-analytics-output-to-azure-cosmos-db"></a>Usługa Azure Stream Analytics dane wyjściowe usługi Azure Cosmos DB  
Stream Analytics można wskazać [usługi Azure Cosmos DB](https://azure.microsoft.com/services/documentdb/) dla danych wyjściowych JSON, włączanie danych archiwizowanie i małe opóźnienia zapytań na danych JSON bez struktury. W tym dokumencie opisano najlepsze rozwiązania dotyczące wdrażania tej konfiguracji.

Dla osób, które są Ci znane z usługą Cosmos DB, Przyjrzyj się [ścieżka szkoleniowa usługi Azure Cosmos DB](https://azure.microsoft.com/documentation/learning-paths/documentdb/) na rozpoczęcie pracy. 

> [!Note]
> W tej chwili usługi Azure Stream Analytics obsługuje tylko połączenie do usługi Azure Cosmos DB przy użyciu **interfejsu API SQL**.
> Innych interfejsów API usługi Azure Cosmos DB nie są jeszcze obsługiwane. Jeśli punkt Azure Stream Analytics do kont usługi Azure Cosmos DB utworzone z innymi interfejsami API, dane mogą nie być prawidłowo przechowywane. 

## <a name="basics-of-cosmos-db-as-an-output-target"></a>Podstawy usługi Cosmos DB jako obiekt docelowy danych wyjściowych
Azure Cosmos DB dane wyjściowe w Stream Analytics umożliwiają zapisywanie wyników przetwarzania strumienia jako danych wyjściowych JSON w kontenerach Cosmos DB. Stream Analytics nie tworzy kontenerów w bazie danych, ale wymaga utworzenia ich z góry. Jest to tak, aby koszty rozliczania kontenerów Cosmos DB są kontrolowane przez użytkownika i aby można było dostosować wydajność, spójność i pojemność kontenerów bezpośrednio przy użyciu [Cosmos DB interfejsów API](https://msdn.microsoft.com/library/azure/dn781481.aspx).

> [!Note]
> Należy dodać 0.0.0.0 do listy dozwolonych adresów IP z zapory Azure Cosmos DBej.

Poniżej przedstawiono niektóre opcje kontenera Cosmos DB.

## <a name="tune-consistency-availability-and-latency"></a>Dostosowywanie spójnością, dostępnością i opóźnieniem
Aby dopasować wymagania dotyczące aplikacji, Azure Cosmos DB umożliwia precyzyjne dostosowanie bazy danych i kontenerów oraz wprowadzenie kompromisu między spójnością, dostępnością, opóźnieniami i przepływności. Zależności od tego, jakie poziomy spójności odczytu wymagań scenariusza dla zapisu i odczytu czas oczekiwania, możesz wybrać poziom spójności na Twoje konto bazy danych. Przepustowość można ulepszyć przez skalowanie jednostek żądań (jednostek ru) w kontenerze. Domyślnie Azure Cosmos DB umożliwia indeksowanie synchroniczne dla każdej operacji CRUD w kontenerze. Jest to przydatne zablokowałoby do kontrolowania wydajności zapisu/odczytu w usłudze Azure Cosmos DB. Aby uzyskać więcej informacji, zobacz [Zmienianie poziomów spójności bazy danych i zapytania](../cosmos-db/consistency-levels.md) artykułu.

## <a name="upserts-from-stream-analytics"></a>Wykonuje operację UPSERT z usługi Stream Analytics
Integracja Stream Analytics z Azure Cosmos DB umożliwia wstawianie lub aktualizowanie rekordów w kontenerze na podstawie danej kolumny identyfikatora dokumentu. To jest również nazywany *Upsert*.

Stream Analytics korzysta z metody optymistycznej upsert których aktualizacje są wykonywane tylko podczas wstawiania zakończy się niepowodzeniem z konfliktem identyfikator dokumentu. W przypadku poziomu zgodności 1,0 Ta aktualizacja jest wykonywana jako poprawka, dzięki czemu umożliwia częściową aktualizację dokumentu, czyli dodanie nowych właściwości lub zastąpienie istniejącej właściwości jest wykonywane przyrostowo. Jednak zmiany wartości właściwości tablicy w wyniku dokumentu JSON, korzystając z całej tablicy wprowadzenie zastąpione, czyli tablicy nie jest połączony. W przypadku 1,2 upsert zachowanie jest modyfikowane w celu wstawienia lub zamiany dokumentu. Opisano to dokładniej w poniższej sekcji poziom zgodności 1,2.

Jeśli przychodzący dokument JSON zawiera istniejące pole ID, że pole jest automatycznie używane jako kolumna Identyfikatora dokumentu w usłudze Cosmos DB, a wszystkie kolejne operacje zapisu są obsługiwane jako takie, co prowadzi do jednej z tych sytuacji:
- unikatowe identyfikatory prowadzić do wstawienia
- Zduplikowane identyfikatory i "Identyfikator dokumentu" wartość "ID" prowadzi do wykonania operacji upsert
- nie zduplikowane identyfikatory i 'Identyfikator dokumentu' zestawu prowadzi do błędu, po pierwszym dokumentu

Jeśli chcesz zapisać <i>wszystkich</i> dokumenty w tym te o zduplikowanym identyfikatorze, Zmień nazwę pola Identyfikator w zapytaniu (za pomocą słowa kluczowego AS) i pozwól Cosmos DB, tworzenia pole Identyfikatora lub Zastąp identyfikator o wartości innej kolumny (przy użyciu słowa kluczowego AS lub za pomocą ustawienia "Dokumentu ID").

## <a name="data-partitioning-in-cosmos-db"></a>Partycjonowanie danych w usłudze Cosmos DB
Azure Cosmos DB [nieograniczone](../cosmos-db/partition-data.md) kontenery to zalecane podejście do partycjonowania danych, ponieważ Azure Cosmos DB automatycznie skaluje partycje na podstawie obciążenia. Podczas zapisywania do nieograniczonych kontenerów Stream Analytics używa jako wielu modułów zapisujących równoległych jako poprzedni krok zapytania lub schemat partycjonowania danych wejściowych.
> [!NOTE]
> W tej chwili Azure Stream Analytics obsługuje tylko nieograniczone kontenery z kluczami partycji na najwyższym poziomie. Na przykład `/region` jest obsługiwana. Zagnieżdżone klucze partycji (np. `/region/name`) nie są obsługiwane. 

W zależności od wybranego klucza partycji mogą pojawić się następujące _Ostrzeżenie_:

`CosmosDB Output contains multiple rows and just one row per partition key. If the output latency is higher than expected, consider choosing a partition key that contains at least several hundred records per partition key.`

Ważne jest, aby wybrać właściwość klucza partycji, która ma różne wartości, i umożliwia równomierne dystrybuowanie obciążeń między tymi wartościami. Naturalnym artefaktem partycjonowania żądania związane z tym samym kluczem partycji są ograniczone przez maksymalną przepływność pojedynczej partycji. Ponadto rozmiar magazynu dla dokumentów należących do tego samego klucza partycji jest ograniczony do 10 GB. Idealnym kluczem partycji jest ten, który pojawia się często jako filtr w zapytaniach i ma wystarczającą Kardynalność, aby zapewnić skalowalność rozwiązania.

Klucz partycji jest również granicą dla transakcji w procedurach i wyzwalaczach przechowywanych w programie DocumentDB. Należy wybrać klucz partycji, aby dokumenty, które występują razem w transakcjach miały tę samą wartość klucza partycji. Partycjonowanie artykułu [w Cosmos DB](../cosmos-db/partitioning-overview.md) zawiera więcej szczegółów na temat wybierania klucza partycji.

W przypadku kontenerów o stałym Azure Cosmos DB Stream Analytics nie pozwala na skalowanie w górę i w dół po ich zapełnieniu. Mają górnego limitu 10 GB i 10 000 jednostek RU/s przepływności.  Aby przeprowadzić migrację danych z kontener stały do nieograniczonego kontenera (na przykład jeden z co najmniej 1000 jednostek RU/s i klucz partycji), należy użyć [narzędzia migracji danych](../cosmos-db/import-data.md) lub [biblioteki kanału informacyjnego zmian](../cosmos-db/change-feed.md).

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



## <a name="cosmos-db-settings-for-json-output"></a>Ustawienia usługi cosmos DB dla danych wyjściowych JSON

Tworzenie usługi Cosmos DB jako dane wyjściowe w usłudze Stream Analytics generuje monit o podanie informacji, jak pokazano poniżej. Ta sekcja zawiera wyjaśnienie definicji właściwości.

![ekranu danych wyjściowych analizy strumienia bazy danych documentdb](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-1.png)

|Pole           | Opis|
|-------------   | -------------|
|Alias danych wyjściowych    | Alias do odwoływania danych wyjściowych w zapytaniu ASA.|
|Subscription    | Wybierz subskrypcję platformy Azure.|
|Identyfikator konta      | Nazwa lub identyfikator URI punktu końcowego konta Azure Cosmos DB.|
|Klucz konta     | Współużytkowany klucz dostępu dla konta Azure Cosmos DB.|
|Database (Baza danych)        | Nazwa bazy danych Azure Cosmos DB.|
|Nazwa kontenera | Nazwa kontenera do użycia. `MyContainer`to przykład prawidłowe dane wejściowe — jeden kontener o `MyContainer` nazwie musi istnieć.  |
|Identyfikator dokumentu     | Opcjonalny. Nazwa kolumny w zdarzeniach wyjściowych służąca jako unikatowego klucza, na które insert nebo update operacji musi być oparta. Jeśli pole pozostanie puste, wszystkie zdarzenia zostaną wstawione, nie dając aktualizacji.|

## <a name="error-handling-and-retries"></a>Obsługa błędów i ponawianie prób

W przypadku awarii przejściowej Usługa niedostępna lub ograniczanie przepustowości podczas wysyłania zdarzeń do Cosmos DB, Stream Analytics ponawianie prób w celu pomyślnego zakończenia operacji. Jednak istnieją pewne błędy, w przypadku których ponowne próby nie wykonywane. Są to:

- Nieautoryzowany (kod błędu HTTP 401)
- NotFound (kod błędu HTTP 404)
- Zabronione (kod błędu HTTP 403)
- Nieprawidłowego żądania (kod błędu HTTP 400)
