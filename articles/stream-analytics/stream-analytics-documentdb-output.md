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
ms.openlocfilehash: de5febaeecd176a8718364720132d3fa4433c57f
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2019
ms.locfileid: "67443625"
---
# <a name="azure-stream-analytics-output-to-azure-cosmos-db"></a>Usługa Azure Stream Analytics dane wyjściowe usługi Azure Cosmos DB  
Stream Analytics można wskazać [usługi Azure Cosmos DB](https://azure.microsoft.com/services/documentdb/) dla danych wyjściowych JSON, włączanie danych archiwizowanie i małe opóźnienia zapytań na danych JSON bez struktury. W tym dokumencie opisano najlepsze rozwiązania dotyczące wdrażania tej konfiguracji.

Dla osób, które są Ci znane z usługą Cosmos DB, Przyjrzyj się [ścieżka szkoleniowa usługi Azure Cosmos DB](https://azure.microsoft.com/documentation/learning-paths/documentdb/) na rozpoczęcie pracy. 

> [!Note]
> W tej chwili usługi Azure Stream Analytics obsługuje tylko połączenie do usługi Azure Cosmos DB przy użyciu **interfejsu API SQL**.
> Innych interfejsów API usługi Azure Cosmos DB nie są jeszcze obsługiwane. Jeśli punkt Azure Stream Analytics do kont usługi Azure Cosmos DB utworzone z innymi interfejsami API, dane mogą nie być prawidłowo przechowywane. 

## <a name="basics-of-cosmos-db-as-an-output-target"></a>Podstawy usługi Cosmos DB jako obiekt docelowy danych wyjściowych
Dane wyjściowe usługi Azure Cosmos DB w usłudze Stream Analytics umożliwia zapisywanie strumienia przetwarzanie wyników jako dane wyjściowe JSON do kontenerów usługi Cosmos DB. Stream Analytics nie tworzyć kontenery w bazie danych, zamiast konieczności je utworzyć z wyprzedzeniem. Jest to tak, aby kosztów kontenerów usługi Cosmos DB są kontrolowane przez użytkownika i tak, aby można dostrajanie wydajności, spójności i pojemności kontenerów bezpośrednio przy użyciu [Cosmos DB API](https://msdn.microsoft.com/library/azure/dn781481.aspx).

> [!Note]
> Należy dodać do listy dozwolonych adresów IP 0.0.0.0 z zapory usługi Azure Cosmos DB.

Niektóre opcje kontenera usługi Cosmos DB są szczegółowo opisane poniżej.

## <a name="tune-consistency-availability-and-latency"></a>Dostosowywanie spójnością, dostępnością i opóźnieniem
Aby są zgodne z wymaganiami aplikacji, usługi Azure Cosmos DB umożliwia dostrajania bazy danych i kontenerów i kompromisu między spójnością, dostępnością, opóźnieniem i przepływnością. Zależności od tego, jakie poziomy spójności odczytu wymagań scenariusza dla zapisu i odczytu czas oczekiwania, możesz wybrać poziom spójności na Twoje konto bazy danych. Można zwiększyć przepływność, skalując w górę żądania Units(RUs) w kontenerze. Domyślnie usługa Azure Cosmos DB umożliwia także synchroniczne indeksowanie każdej operacji CRUD, do kontenera. Jest to przydatne zablokowałoby do kontrolowania wydajności zapisu/odczytu w usłudze Azure Cosmos DB. Aby uzyskać więcej informacji, zobacz [Zmienianie poziomów spójności bazy danych i zapytania](../cosmos-db/consistency-levels.md) artykułu.

## <a name="upserts-from-stream-analytics"></a>Wykonuje operację UPSERT z usługi Stream Analytics
Stream Analytics integracji z usługą Azure Cosmos DB umożliwia wstawianie lub aktualizowania rekordów w Twoim kontenerze, w oparciu o danej kolumnie identyfikator dokumentu. To jest również nazywany *Upsert*.

Stream Analytics korzysta z metody optymistycznej upsert których aktualizacje są wykonywane tylko podczas wstawiania zakończy się niepowodzeniem z konfliktem identyfikator dokumentu. Z wersji 1.0 poziom zgodności ta aktualizacja jest wykonywane jako poprawki, dzięki czemu umożliwia ona aktualizacje częściowe do dokumentu, to znaczy, dodanie nowych właściwości lub zastępując istniejącą właściwość jest wykonywana stopniowo. Jednak zmiany wartości właściwości tablicy w wyniku dokumentu JSON, korzystając z całej tablicy wprowadzenie zastąpione, czyli tablicy nie jest połączony. Za pomocą 1.2 zachowanie upsert zostanie zmodyfikowany na potrzeby wstawić lub Zastąp dokument. Jest to dokładniejszym opisem zawartym w poniższej sekcji 1,2 poziom zgodności.

Jeśli przychodzący dokument JSON zawiera istniejące pole ID, że pole jest automatycznie używane jako kolumna Identyfikatora dokumentu w usłudze Cosmos DB, a wszystkie kolejne operacje zapisu są obsługiwane jako takie, co prowadzi do jednej z tych sytuacji:
- unikatowe identyfikatory prowadzić do wstawienia
- Zduplikowane identyfikatory i "Identyfikator dokumentu" wartość "ID" prowadzi do wykonania operacji upsert
- nie zduplikowane identyfikatory i 'Identyfikator dokumentu' zestawu prowadzi do błędu, po pierwszym dokumentu

Jeśli chcesz zapisać <i>wszystkich</i> dokumenty w tym te o zduplikowanym identyfikatorze, Zmień nazwę pola Identyfikator w zapytaniu (za pomocą słowa kluczowego AS) i pozwól Cosmos DB, tworzenia pole Identyfikatora lub Zastąp identyfikator o wartości innej kolumny (przy użyciu słowa kluczowego AS lub za pomocą ustawienia "Dokumentu ID").

## <a name="data-partitioning-in-cosmos-db"></a>Partycjonowanie danych w usłudze Cosmos DB
Usługa Azure Cosmos DB [nieograniczone](../cosmos-db/partition-data.md) kontenerów: czym są zalecane podejście do partycjonowania danych, jak usługa Azure Cosmos DB automatycznie skaluje partycji na podstawie własnego obciążenia. Podczas zapisywania nieograniczone kontenery, Stream Analytics używa dowolną liczbę równoległych moduły zapisujące poprzednie zapytanie krok lub danych wejściowych schemat partycjonowania.
> [!NOTE]
> W tej chwili usługi Azure Stream Analytics obsługuje nieograniczone kontenery tylko przy użyciu kluczy partycji na najwyższym poziomie. Na przykład `/region` jest obsługiwana. Zagnieżdżone klucze partycji (np. `/region/name`) nie są obsługiwane. 

W zależności od wybranego klucza partycji można otrzymać to _ostrzeżenie_:

`CosmosDB Output contains multiple rows and just one row per partition key. If the output latency is higher than expected, consider choosing a partition key that contains at least several hundred records per partition key.`

Należy wybrać właściwość klucza partycji, ma wiele unikatowych wartości, która umożliwia równomierne rozłożenie obciążenia między te wartości. Jako fizyczną artefakt partycjonowanie żądań dotyczących tego samego klucza partycji są ograniczone przez maksymalną przepływność jednej partycji. Ponadto rozmiar magazynu dla dokumentów należących do tego samego klucza partycji jest ograniczona do 10GB. Klucz partycji idealnym rozwiązaniem jest taki, który pojawia się często, jak filtr zapytania i ma wystarczającą właściwość kardynalność na wartość upewnij się, że Twoje rozwiązanie jest skalowalna.

Klucz partycji jest również granic dla transakcji w procedur składowanych i wyzwalaczy usługi DocumentDB. Należy wybrać klucza partycji, dzięki czemu dokumenty, które występują ze sobą w transakcji mieć taką samą wartość klucza partycji. Artykuł [partycjonowanie w usłudze Cosmos DB](../cosmos-db/partitioning-overview.md) zapewnia szczegółowe informacje na temat wybór klucza partycji.

Naprawiono kontenery usługi Azure Cosmos DB Stream Analytics umożliwia sposób skalować w górę lub w poziomie, gdy są one pełne. Mają górnego limitu 10 GB i 10 000 jednostek RU/s przepływności.  Aby przeprowadzić migrację danych z kontener stały do nieograniczonego kontenera (na przykład jeden z co najmniej 1000 jednostek RU/s i klucz partycji), należy użyć [narzędzia migracji danych](../cosmos-db/import-data.md) lub [biblioteki kanału informacyjnego zmian](../cosmos-db/change-feed.md).

Możliwość zapisu do wielu kontenerów stałej jest on przestarzały i nie jest zalecane w przypadku skalowania w poziomie zadania usługi Stream Analytics.

## <a name="improved-throughput-with-compatibility-level-12"></a>Ulepszone przepływności, zapewniając 1,2 poziom zgodności
Poziom zgodności 1.2 natywna Integracja usługi Stream Analytics obsługuje do zbiorczego zapisu do usługi Cosmos DB. Umożliwia to efektywne zapisywania Cosmos DB przy użyciu maksymalizując przepływności i wydajnego ograniczania żądań dojście. Mechanizm ulepszone zapisu jest dostępne w nowy poziom zgodności z powodu różnic zachowanie upsert.  Przed 1.2 zachowanie upsert jest aby wstawić lub scalić dokument. Za pomocą 1.2 wykonuje operację UPSERT zachowanie zostanie zmodyfikowany na potrzeby wstawić lub Zastąp dokument. 

Przed 1.2 używa niestandardowej procedury składowanej do zbiorczego upsert dokumentów na klucz partycji do usługi Cosmos DB, w którym zapisywana jest partii jako transakcja. Nawet wtedy, gdy jest to błąd przejściowy (ograniczanie przepustowości) uderza w jeden rekord, musi zostać powtórzone, całą partię. Stało się scenariusze obejmujące usługę nawet uzasadnione ograniczania stosunkowo wolniej. Następujące porównania pokazuje, jak takie zadania będzie się zachowywał tak z 1.2.

Poniższy przykład przedstawia dwa identyczne zadania usługi Stream Analytics podczas odczytywania z tych samych danych wejściowych z Centrum zdarzeń. Oba zadania usługi Stream Analytics są [pełni podzielona na partycje](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization#embarrassingly-parallel-jobs) przy użyciu zapytania przekazującego i zapisu do identyczne kontenerów bazy danych cosmos DB. Metryki po lewej stronie są z zadania skonfigurowano poziom zgodności 1.0 i te, które po prawej stronie są skonfigurowane przy użyciu 1.2. Klucz partycji w kontenerze usługi Cosmos DB to unikatowy identyfikator GUID pochodzące z dane wejściowe zdarzenia.

![Stream analytics metryki porównania](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-3.png)

Szybkość odbierania zdarzeń w Centrum zdarzeń to 2 x większa niż kontenery usługi Cosmos DB (20K RU) są skonfigurowane do pobierania, dzięki czemu ograniczanie oczekuje się w usłudze Cosmos DB. Jednak zadania za pomocą 1.2, stale zapisuje przy większej przepływności (dane wyjściowe zdarzenia/minutę) i niższych średni % wykorzystania SU. W danym środowisku ta różnica będzie zależeć od kilku więcej czynniki, takie jak wybór formatu zdarzeń, rozmiar danych wejściowych/komunikatu o zdarzeniu, klucze partycji, zapytania itp.

![cosmos db metryki porównania](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-2.png)

1\.2 Stream Analytics jest bardziej inteligentne i w użyciu 100% dostępne przepływności w usłudze Cosmos DB z małą liczbą ponownych przesłań z ograniczania/szybkości. Zapewnia to lepsze środowisko dla innych obciążeń, takich jak zapytania, uruchomione w kontenerze, w tym samym czasie. W razie potrzeby możesz wypróbować jak ASA skalowania za pomocą usługi Cosmos DB jako obiekt sink dla 1 do 10 tys. obr komunikaty/sekundę w tym miejscu jest [projektu przykładów dla platformy azure](https://github.com/Azure-Samples/streaming-at-scale/tree/master/eventhubs-streamanalytics-cosmosdb) , która umożliwia to zrobić.
Należy pamiętać, że przepływności danych wyjściowych usługi Cosmos DB jest taka sama, jak 1.0 i 1.1. Ponieważ 1.2 nie jest obecnie domyślna, możesz [Ustaw poziom zgodności](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-compatibility-level) dla zadania usługi Stream Analytics, za pomocą portalu lub przy użyciu [wywołanie interfejsu API REST zadania tworzenia](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-job). Ma ona *zdecydowanie zaleca się* 1,2 poziom zgodności w ASA za pomocą usługi Cosmos DB. 



## <a name="cosmos-db-settings-for-json-output"></a>Ustawienia usługi cosmos DB dla danych wyjściowych JSON

Tworzenie usługi Cosmos DB jako dane wyjściowe w usłudze Stream Analytics generuje monit o podanie informacji, jak pokazano poniżej. Ta sekcja zawiera wyjaśnienie definicji właściwości.

![ekranu danych wyjściowych analizy strumienia bazy danych documentdb](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-1.png)

|Pole           | Opis|
|-------------   | -------------|
|Alias danych wyjściowych    | Odwoływanie się aliasu to dane wyjściowe w zapytaniu usługi ASA.|
|Subskrypcja    | Wybierz subskrypcję platformy Azure.|
|Identyfikator konta      | Nazwa lub identyfikator URI konta usługi Azure Cosmos DB punktu końcowego.|
|Klucz konta     | Klucz dostępu współdzielonego dla konta usługi Azure Cosmos DB.|
|Database (Baza danych)        | Nazwa bazy danych Azure Cosmos DB.|
|Nazwa kontenera | Nazwa kontenera, który ma być używany. `MyContainer` to przykładowe prawidłowe dane wejściowe — jeden kontener o nazwie `MyContainer` musi istnieć.  |
|Identyfikator dokumentu     | Opcjonalny. Nazwa kolumny w zdarzeniach wyjściowych służąca jako unikatowego klucza, na które insert nebo update operacji musi być oparta. Jeśli pole pozostanie puste, wszystkie zdarzenia zostaną wstawione, nie dając aktualizacji.|
