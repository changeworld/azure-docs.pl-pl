---
title: Praca z obsługą kanału informacyjnego zmian w usłudze Azure Cosmos DB
description: Obsługa kanału informacyjnego usługi Azure Cosmos DB umożliwia śledzenie zmian w dokumentach, przetwarzanie oparte na zdarzeniach, takie jak wyzwalacze, oraz utrzymywanie aktualności pamięci podręcznych i systemów analitycznych
author: TheovanKraay
ms.author: thvankra
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/25/2019
ms.reviewer: sngun
ms.custom: seodec18
ms.openlocfilehash: 898dfe7a619981b93af98effa942fdecbeb42dde
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79368132"
---
# <a name="change-feed-in-azure-cosmos-db---overview"></a>Zestawienie zmian w usłudze Azure Cosmos DB — omówienie

Obsługa zestawienia zmian w usłudze Azure Cosmos DB działa przez nasłuchiwanie zmian w kontenerze usługi Azure Cosmos. Następnie tworzone są dane wyjściowe w postaci posortowanej listy zmienionych dokumentów w kolejności, w której zostały zmodyfikowane. Zmiany zostają utrwalone, mogą być przetwarzane asynchronicznie i przyrostowo, a dane wyjściowe mogą być rozpowszechniane wśród jednego lub większej liczby użytkowników w celu przetwarzania równoległego. 

Usługa Azure Cosmos DB doskonale nadaje się do aplikacji do rejestrowania IoT, gier, sprzedaży detalicznej i rejestrowania operacyjnego. Typowy wzorzec projektu w tych aplikacjach jest użycie zmian w danych, aby wyzwolić dodatkowe akcje. Przykłady dodatkowych działań obejmują:

* Wyzwalanie powiadomienia lub wywołania interfejsu API, gdy element jest wstawiany lub aktualizowany.
* Przetwarzanie strumienia w czasie rzeczywistym dla IoT lub analizy w czasie rzeczywistym przetwarzania danych operacyjnych.
* Dodatkowe przenoszenie danych przez synchronizację z pamięcią podręczną lub wyszukiwarką lub magazynem danych lub archiwizowanie danych do magazynu chłodniczego.

Źródło danych zmian w usłudze Azure Cosmos DB umożliwia tworzenie wydajnych i skalowalnych rozwiązań dla każdego z tych wzorców, jak pokazano na poniższej ilustracji:

![Korzystanie z kanału informacyjnego usługi Azure Cosmos DB w celu zasilania analiz w czasie rzeczywistym i scenariuszy obliczeniowych opartych na zdarzeniach](./media/change-feed/changefeedoverview.png)

## <a name="supported-apis-and-client-sdks"></a>Obsługiwane interfejsy API i sdk klientów

Ta funkcja jest obecnie obsługiwana przez następujące interfejsy API bazy danych usługi Azure Cosmos DB i zestawy SDK klienta.

| **Sterowniki klienta** | **Interfejs wiersza polecenia platformy Azure** | **SQL API** | **Interfejs API usługi Azure Cosmos DB dla kasandry** | **Interfejs API usługi Azure Cosmos DB dla bazy danych MongoDB** | **Gremlin API**|**Interfejs API tabel** |
| --- | --- | --- | --- | --- | --- | --- |
| .NET | Nie dotyczy | Tak | Tak | Tak | Tak | Nie |
|Java|Nie dotyczy|Tak|Tak|Tak|Tak|Nie|
|Python|Nie dotyczy|Tak|Tak|Tak|Tak|Nie|
|Węzeł/JS|Nie dotyczy|Tak|Tak|Tak|Tak|Nie|

## <a name="change-feed-and-different-operations"></a>Zmiana kanału informacyjnego i różnych operacji

Dzisiaj widzisz wszystkie operacje w kanale zmian. Funkcja, w której można kontrolować plik danych o zmianach, dla określonych operacji, takich jak tylko aktualizacje i nie wstawia nie jest jeszcze dostępna. Możesz dodać "znacznik miękki" na elemencie do aktualizacji i filtrowania na podstawie tego podczas przetwarzania elementów w pliku danych ze zmianami. Obecnie zmienia plik danych nie rejestruje usuwania. Podobnie jak w poprzednim przykładzie, można dodać znacznik miękki na elementy, które są usuwane, na przykład, można dodać atrybut w elemencie o nazwie "usunięte" i ustawić go na "true" i ustawić czas wygaśnięcia na elemencie, tak aby można było go automatycznie usunąć. Możesz odczytać plik danych o zmianach dla elementów historycznych (najnowsza zmiana odpowiadająca elementowi, nie obejmuje ona zmian pośrednich), na przykład elementy, które zostały dodane pięć lat temu. Jeśli element nie zostanie usunięty, możesz odczytać plik danych o zmianach, jeśli chodzi o pochodzenie kontenera.

### <a name="sort-order-of-items-in-change-feed"></a>Kolejność sortowania elementów w pliku danych o zmianach

Elementy pliku danych zmian są uporządkowane w kolejności ich modyfikacji. Ta kolejność sortowania jest gwarantowana dla klucza partycji logicznej.

### <a name="consistency-level"></a>Poziom spójności

Podczas korzystania z pliku danych o zmianie na poziomie spójność ostateczna, może istnieć zduplikowane zdarzenia między kolejnymi operacjami odczytu kanału informacyjnego zmiany (ostatnie zdarzenie jednej operacji odczytu pojawia się jako pierwsza z następnych).

### <a name="change-feed-in-multi-region-azure-cosmos-accounts"></a>Zmienianie kanału informacyjnego na wieloregionowych kontach usługi Azure Cosmos

W wielu regionach usługi Azure Cosmos konta, jeśli region zapisu nie powiedzie się, plik danych o zmianie będzie działać w ręcznej operacji pracy awaryjnej i będzie ciągła.

### <a name="change-feed-and-time-to-live-ttl"></a>Zmień kanał informacyjny i czas na czas wygaśnięcia (TTL)

Jeśli TTL (Time to Live) właściwość jest ustawiona na element do -1, zmiana kanału informacyjnego będzie trwać na zawsze. Jeśli dane nie zostaną usunięte, pozostaną w pliku danych ze zmianami.  

### <a name="change-feed-and-_etag-_lsn-or-_ts"></a>Zmienianie pliku danych i _etag, _lsn lub _ts

Format _etag jest wewnętrzny i nie należy przyjmować zależności od niego, ponieważ można go zmienić w dowolnym momencie. _ts jest modyfikacją lub sygnaturą czasową tworzenia. Można użyć _ts do porównania chronologicznego. _lsn jest identyfikatorem partii, który jest dodawany tylko dla źródła danych zmian; reprezentuje identyfikator transakcji. Wiele elementów może mieć takie same _lsn. ETag na FeedResponse różni się od _etag widocznej na elemencie. _etag jest identyfikatorem wewnętrznym i jest używany do kontroli współbieżności informuje o wersji elementu, podczas gdy ETag jest używany do sekwencjonowania kanału informacyjnego.

## <a name="change-feed-use-cases-and-scenarios"></a>Zmienianie przypadków użycia pliku danych i scenariuszy

Plik danych o zmianie umożliwia wydajne przetwarzanie dużych zestawów danych z dużą liczbą zapisów. Plik danych o zmianie oferuje również alternatywę dla wykonywania zapytań dotyczących całego zestawu danych w celu zidentyfikowania zmian.

### <a name="use-cases"></a>Przypadki zastosowań

Na przykład za pomocą kanału informacyjnego zmian można wydajnie wykonywać następujące zadania:

* Zaktualizuj pamięć podręczną, zaktualizuj indeks wyszukiwania lub zaktualizuj magazyn danych danymi przechowywanymi w usłudze Azure Cosmos DB.

* Zaimplementuj warstwę danych i archiwizację na poziomie aplikacji, na przykład przechowuj "gorące dane" w usłudze Azure Cosmos DB i rozlatuj "zimne dane" w innych systemach magazynowania, na przykład [usługi Azure Blob Storage.](../storage/common/storage-introduction.md)

* Wykonaj zerową migrację w dół do innego konta usługi Azure Cosmos lub innego kontenera usługi Azure Cosmos z innym kluczem partycji logicznej.

* Zaimplementuj [architekturę lambda](https://blogs.technet.microsoft.com/msuspartner/2016/01/27/azure-partner-community-big-data-advanced-analytics-and-lambda-architecture/) przy użyciu usługi Azure Cosmos DB, w której usługa Azure Cosmos DB obsługuje warstwy obsługujące zarówno w czasie rzeczywistym, wsadowe, jak i zapytania, umożliwiając w ten sposób architekturę lambda o niskim przydzieleniu rzeczywistego posiadania.

* Odbieraj i przechowuj dane zdarzeń z urządzeń, czujników, infrastruktury i aplikacji oraz przetwarzaj te zdarzenia w czasie rzeczywistym, na przykład za pomocą [platformy Spark.](../hdinsight/spark/apache-spark-overview.md)  Na poniższej ilustracji pokazano, jak można zaimplementować architekturę lambda przy użyciu usługi Azure Cosmos DB za pośrednictwem źródła danych zmian:

![Potok lambdy oparty na usłudze Azure Cosmos DB do pozyskiwania i wykonywania zapytań](./media/change-feed/lambda.png)

### <a name="scenarios"></a>Scenariusze

Oto niektóre ze scenariuszy, które można łatwo wdrożyć za pomocą źródła danych o zmianach:

* W aplikacjach sieci web lub aplikacjach mobilnych [bezserwerowych](https://azure.microsoft.com/solutions/serverless/) można śledzić zdarzenia, takie jak wszystkie zmiany w profilu klienta, preferencjach lub ich lokalizacji, i wyzwalać określone akcje, na przykład wysyłanie powiadomień wypychanych na swoje urządzenia za pomocą [usługi Azure Functions](change-feed-functions.md).

* Jeśli używasz usługi Azure Cosmos DB do tworzenia gry, możesz na przykład użyć kanału informacyjnego zmian, aby zaimplementować rankingi w czasie rzeczywistym na podstawie wyników z ukończonych gier.


## <a name="working-with-change-feed"></a>Praca z kanałem zmian

Możesz pracować z kanałem informacyjnym zmian, korzystając z następujących opcji:

* [Korzystanie z kanału informacyjnego zmian za pomocą funkcji azure](change-feed-functions.md)
* [Korzystanie z kanału informacyjnego zmian z procesorem zmienić kanał](change-feed-processor.md) 

Plik danych o zmianie jest dostępny dla każdego klucza partycji logicznej w kontenerze i może być dystrybuowany między jednym lub kilkoma konsumentami do przetwarzania równoległego, jak pokazano na poniższej ilustracji.

![Rozproszone przetwarzanie kanału informacyjnego usługi Azure Cosmos DB](./media/change-feed/changefeedvisual.png)

## <a name="features-of-change-feed"></a>Funkcje kanału zmian

* Źródło danych o zmianach jest domyślnie włączone dla wszystkich kont usługi Azure Cosmos.

* Można użyć [aprowizowanej przepływności](request-units.md) do odczytu z kanału informacyjnego zmian, podobnie jak każda inna operacja usługi Azure Cosmos DB, w dowolnym regionie skojarzonym z bazą danych usługi Azure Cosmos.

* Źródło danych zawiera wstawia i aktualizuj operacje wprowadzone do elementów w kontenerze. Usuwanie można przechwytywać, ustawiając flagę "soft-delete" w elementach (na przykład dokumentach) zamiast usuwania. Alternatywnie można ustawić skończony okres wygaśnięcia elementów z [możliwością czasu wygaśnięcia.](time-to-live.md) Na przykład 24 godzin i użyć wartości tej właściwości do przechwytywania usuwa. Dzięki temu rozwiązaniu należy przetworzyć zmiany w krótszym przedziale czasu niż okres wygaśnięcia. 

* Każda zmiana elementu pojawia się dokładnie raz w zestawieniu zmian, a klienci muszą zarządzać logiką punktów kontrolnych. Jeśli chcesz uniknąć złożoności zarządzania punktami kontrolnymi, procesor pliku danych zmian zapewnia automatyczne punkty kontrolne i semantykę "co najmniej raz". Zobacz [korzystanie z funkcji źródła danych zmian za pomocą procesora zdawania pliku danych](change-feed-processor.md).

* Tylko ostatnia zmiana dla danego elementu jest uwzględniona w dzienniku zmian. Zmiany pośrednie mogą być niedostępne.

* Źródło danych o zmianach jest sortowane według kolejności modyfikacji w ramach każdej wartości klucza partycji logicznej. Nie ma gwarantowanej kolejności w wartościach klucza partycji.

* Zmiany mogą być synchronizowane z dowolnego punktu w czasie, czyli nie ma stałego okresu przechowywania danych, dla których zmiany są dostępne.

* Zmiany są dostępne równolegle dla wszystkich kluczy partycji logicznych kontenera usługi Azure Cosmos. Ta funkcja umożliwia zmiany z dużych kontenerów do przetwarzania równolegle przez wielu konsumentów.

* Aplikacje mogą żądać wielu zmian kanałów informacyjnych w tym samym kontenerze jednocześnie. ChangeFeedOptions.StartTime może służyć do zapewnienia początkowego punktu wyjścia. Na przykład, aby znaleźć token kontynuacji odpowiadający danej godzinie zegara. KontynuacjaToken, jeśli określono, wygrywa przez StartTime i StartFromBeginning wartości. Dokładność ChangeFeedOptions.StartTime wynosi ~5 sekund. 

## <a name="change-feed-in-apis-for-cassandra-and-mongodb"></a>Zmiana kanału informacyjnego w interfejsach API dla Cassandra i MongoDB

Funkcja kanału informacyjnego zmian jest pojawiana jako strumień zmian w interfejsie API i zapytaniu MongoDB z predykatem w api Cassandra. Aby dowiedzieć się więcej o szczegółach implementacji interfejsu API usługi MongoDB, zobacz [Zmiany strumieni w interfejsie API bazy danych usługi Azure Cosmos DB dla usługi MongoDB.](mongodb-change-streams.md)

Natywny Apache Cassandra zapewnia przechwytywanie danych zmian (CDC), mechanizm do oznaczania określonych tabel do archiwizacji, a także odrzuca zapisy do tych tabel po osiągnięciu konfigurowalny rozmiar na dysku dla dziennika CDC. Funkcja kanału informacyjnego zmian w interfejsie API bazy danych usługi Azure Cosmos dla bazy danych dla firmy Cassandra zwiększa możliwość wykonywania zapytań o zmiany za pomocą predykatu za pośrednictwem CQL. Aby dowiedzieć się więcej o szczegółach implementacji, zobacz [Zmienianie źródła danych w interfejsie API usługi Azure Cosmos DB dla firmy Cassandra.](cassandra-change-feed.md)

## <a name="next-steps"></a>Następne kroki

Teraz możesz dowiedzieć się więcej o pliku danych o zmianach w następujących artykułach:

* [Opcje odczytu kanału informacyjnego zmiany](read-change-feed.md)
* [Korzystanie z kanału informacyjnego zmian za pomocą funkcji azure](change-feed-functions.md)
* [Korzystanie z procesora zmienić kanał informacyjny](change-feed-processor.md)
