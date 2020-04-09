---
title: Praca z obsługą kanału informacyjnego zmian w usłudze Azure Cosmos DB
description: Obsługa kanału informacyjnego usługi Azure Cosmos DB umożliwia śledzenie zmian w dokumentach, przetwarzanie oparte na zdarzeniach, takie jak wyzwalacze, oraz utrzymywanie aktualności pamięci podręcznych i systemów analitycznych
author: TheovanKraay
ms.author: thvankra
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/08/2020
ms.reviewer: sngun
ms.custom: seodec18
ms.openlocfilehash: e36e95aeb25c83ccd94f11e25bfe9f1b8f7bfdad
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/09/2020
ms.locfileid: "80984865"
---
# <a name="change-feed-in-azure-cosmos-db"></a>Zestawienie zmian w usłudze Azure Cosmos DB

Obsługa zestawienia zmian w usłudze Azure Cosmos DB działa przez nasłuchiwanie zmian w kontenerze usługi Azure Cosmos. Następnie tworzone są dane wyjściowe w postaci posortowanej listy zmienionych dokumentów w kolejności, w której zostały zmodyfikowane. Zmiany zostają utrwalone, mogą być przetwarzane asynchronicznie i przyrostowo, a dane wyjściowe mogą być rozpowszechniane wśród jednego lub większej liczby użytkowników w celu przetwarzania równoległego.

Dowiedz się więcej o [wzorach projektowania kanału informacyjnego](change-feed-design-patterns.md).

## <a name="supported-apis-and-client-sdks"></a>Obsługiwane interfejsy API i sdk klientów

Ta funkcja jest obecnie obsługiwana przez następujące interfejsy API bazy danych usługi Azure Cosmos DB i zestawy SDK klienta.

| **Sterowniki klienta** | **SQL API** | **Interfejs API usługi Azure Cosmos DB dla kasandry** | **Interfejs API usługi Azure Cosmos DB dla bazy danych MongoDB** | **Interfejs API języka Gremlin**|**Interfejs API tabel** |
| --- | --- | --- | --- | --- | --- | --- |
| .NET | Tak | Tak | Tak | Tak | Nie |
|Java|Tak|Tak|Tak|Tak|Nie|
|Python|Tak|Tak|Tak|Tak|Nie|
|Węzeł/JS|Tak|Tak|Tak|Tak|Nie|

## <a name="change-feed-and-different-operations"></a>Zmiana kanału informacyjnego i różnych operacji

Obecnie wszystkie wstawia i aktualizacje są widoczne w pliku danych ze zmianami. Nie można filtrować źródła danych zmian dla określonego typu operacji. Jedną z możliwych alternatyw jest dodanie "znacznika miękkiego" na elemencie aktualizacji i filtrowania na podstawie tego podczas przetwarzania elementów w pliku danych o zmianach.

Obecnie zmienia plik danych nie rejestruje usuwania. Podobnie jak w poprzednim przykładzie, można dodać znacznik miękki na elementach, które są usuwane. Na przykład można dodać atrybut w elemencie o nazwie "usunięte" i ustawić go na "true" i ustawić czas wygaśnięcia na elemencie, dzięki czemu można go automatycznie usunąć. Możesz odczytać plik danych o zmianach dla elementów historycznych (najnowsza zmiana odpowiadająca elementowi, nie obejmuje ona zmian pośrednich), na przykład elementy, które zostały dodane pięć lat temu. Możesz odczytać plik danych o zmianach już w zależności od źródła kontenera, ale jeśli element zostanie usunięty, zostanie on usunięty z pliku danych ze zmian.

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

* Aplikacje mogą żądać wielu zmian kanałów informacyjnych w tym samym kontenerze jednocześnie. ChangeFeedOptions.StartTime może służyć do zapewnienia początkowego punktu wyjścia. Na przykład, aby znaleźć token kontynuacji odpowiadający danej godzinie zegara. KontynuacjaToken, jeśli określono, ma pierwszeństwo przed StartTime i StartFromBeginning wartości. Dokładność ChangeFeedOptions.StartTime wynosi ~5 sekund.

## <a name="change-feed-in-apis-for-cassandra-and-mongodb"></a>Zmiana kanału informacyjnego w interfejsach API dla Cassandra i MongoDB

Funkcja kanału informacyjnego zmian jest pojawiana jako strumień zmian w interfejsie API i zapytaniu MongoDB z predykatem w api Cassandra. Aby dowiedzieć się więcej o szczegółach implementacji interfejsu API usługi MongoDB, zobacz [Zmiany strumieni w interfejsie API bazy danych usługi Azure Cosmos DB dla usługi MongoDB.](mongodb-change-streams.md)

Natywny Apache Cassandra zapewnia przechwytywanie danych zmian (CDC), mechanizm do oznaczania określonych tabel do archiwizacji, a także odrzuca zapisy do tych tabel po osiągnięciu konfigurowalny rozmiar na dysku dla dziennika CDC. Funkcja kanału informacyjnego zmian w interfejsie API bazy danych usługi Azure Cosmos dla bazy danych dla firmy Cassandra zwiększa możliwość wykonywania zapytań o zmiany za pomocą predykatu za pośrednictwem CQL. Aby dowiedzieć się więcej o szczegółach implementacji, zobacz [Zmienianie źródła danych w interfejsie API usługi Azure Cosmos DB dla firmy Cassandra.](cassandra-change-feed.md)

## <a name="next-steps"></a>Następne kroki

Teraz możesz dowiedzieć się więcej o pliku danych o zmianach w następujących artykułach:

* [Opcje odczytu kanału informacyjnego zmiany](read-change-feed.md)
* [Korzystanie z kanału informacyjnego zmian za pomocą funkcji azure](change-feed-functions.md)
* [Korzystanie z procesora zmienić kanał informacyjny](change-feed-processor.md)
