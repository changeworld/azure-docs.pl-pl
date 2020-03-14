---
title: Praca ze zmianą Obsługa kanału informacyjnego w usłudze Azure Cosmos DB
description: Korzystanie z obsługi kanału informacyjnego Azure Cosmos DB zmiany w celu śledzenia zmian w dokumentach, przetwarzania opartego na zdarzeniach, takich jak wyzwalacze i utrzymywania aktualności pamięci podręcznych i systemów analitycznych
author: TheovanKraay
ms.author: thvankra
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/25/2019
ms.reviewer: sngun
ms.custom: seodec18
ms.openlocfilehash: 898dfe7a619981b93af98effa942fdecbeb42dde
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/14/2020
ms.locfileid: "79368132"
---
# <a name="change-feed-in-azure-cosmos-db---overview"></a>Źródło zmian w Azure Cosmos DB — Omówienie

Obsługa zestawienia zmian w usłudze Azure Cosmos DB działa przez nasłuchiwanie zmian w kontenerze usługi Azure Cosmos. Następnie tworzone są dane wyjściowe w postaci posortowanej listy zmienionych dokumentów w kolejności, w której zostały zmodyfikowane. Zmiany zostają utrwalone, mogą być przetwarzane asynchronicznie i przyrostowo, a dane wyjściowe mogą być rozpowszechniane wśród jednego lub większej liczby użytkowników w celu przetwarzania równoległego. 

Usługa Azure Cosmos DB jest odpowiednie dla IoT, gry detaliczna i operacyjne rejestrowania aplikacji. Typowy wzorzec projektowania w tych aplikacjach jest Wyzwól dodatkowe akcje za pomocą zmian danych. Przykłady dodatkowe akcje:

* Wyzwalania powiadomienie lub wywołanie interfejsu API po wstawieniu lub zaktualizowaniu elementu.
* W czasie rzeczywistym strumień przetwarzania IoT lub analizy w czasie rzeczywistym, przetwarzanie danych operacyjnych.
* Dodatkowego przenoszenia danych przez synchronizację z pamięci podręcznej lub aparatu wyszukiwania lub magazynu danych lub archiwizowania danych do zimnego magazynu.

Zmiana źródła danych w usłudze Azure Cosmos DB umożliwia wydajne i skalowalne rozwiązania dla każdego z tych wzorców, jak pokazano na poniższej ilustracji:

![Za pomocą usługi Azure Cosmos DB zmiany źródła danych do zasilania analizy w czasie rzeczywistym i scenariusze obliczeniowe sterowane zdarzeniami](./media/change-feed/changefeedoverview.png)

## <a name="supported-apis-and-client-sdks"></a>Obsługiwane interfejsy API i zestawów SDK klienta

Ta funkcja jest obecnie obsługiwana przez następujących interfejsów API usługi Azure Cosmos DB i zestawów SDK klienta.

| **Sterowniki klienta** | **Interfejs wiersza polecenia platformy Azure** | **INTERFEJS API SQL** | **Interfejs API Azure Cosmos DB dla Cassandra** | **Interfejs API Azure Cosmos DB dla MongoDB** | **Interfejs API Gremlin**|**Interfejs API tabel** |
| --- | --- | --- | --- | --- | --- | --- |
| .NET | Nie dotyczy | Yes | Yes | Yes | Yes | Nie |
|Java|Nie dotyczy|Yes|Yes|Yes|Yes|Nie|
|Python|Nie dotyczy|Yes|Yes|Yes|Yes|Nie|
|Węzeł/JS|Nie dotyczy|Yes|Yes|Yes|Yes|Nie|

## <a name="change-feed-and-different-operations"></a>Zestawienia zmian i różnych operacji

Już dziś zobaczysz wszystkie operacje w zestawienia zmian. Funkcje, których można kontrolować, Zmień źródło danych dla określonych operacji takich, jak tylko aktualizacje i nie wstawienia nie jest jeszcze dostępna. Można dodać "miękki znacznik" w elemencie dla aktualizacji i filtru na podstawie tego, czy podczas przetwarzania elementów ze źródła zmian. Obecnie Źródło zmian nie powoduje usunięcia dziennika. Podobnie jak w poprzednim przykładzie, można dodać znacznika nietrwale elementy, które są usuwane, na przykład można dodać atrybutu w elemencie o nazwie "usunięta" i ustaw ją na wartość "true" i ustaw czas wygaśnięcia elementu, dzięki czemu może zostać automatycznie usunięty. Możesz odczytać Źródło zmian dla elementów historycznych (Ostatnia zmiana odpowiadająca elementowi nie obejmuje zmian pośrednich), na przykład elementów, które zostały dodane pięć lat temu. Jeśli element nie zostanie usunięty. możesz przeczytać zmiany źródła danych, o ile jest to punkt początkowy kontenera.

### <a name="sort-order-of-items-in-change-feed"></a>Kolejność elementów na liście zestawienia zmian do sortowania

Zmiany elementów kanału informacyjnego pochodzą zgodnie z kolejnością czas ich modyfikacji. Ta kolejność sortowania jest gwarantowana na klucz partycji logicznej.

### <a name="consistency-level"></a>Poziom spójności

Podczas konsumowania źródła zmian na poziomie spójności ostatecznej może istnieć zduplikowane zdarzenie między kolejnymi operacjami odczytu kanału informacyjnego zmian (ostatnie zdarzenie jednej operacji odczytu pojawia się jako pierwsza z następnych).

### <a name="change-feed-in-multi-region-azure-cosmos-accounts"></a>Zmiana źródła danych na kontach usługi Azure Cosmos w wielu regionach

Na koncie usługi Azure Cosmos wielu regionów, jeśli region zapisu w trybie Failover, kanał informacyjny zmian będzie działać w operacji ręcznej pracy awaryjnej i będą ciągłe.

### <a name="change-feed-and-time-to-live-ttl"></a>Zmień źródło danych i czas wygaśnięcia (TTL)

Jeśli właściwość czas wygaśnięcia (czas wygaśnięcia) ustawiono element na -1, kanał informacyjny zmian zostanie utrzymany nieskończoność. Jeśli dane nie zostaną usunięte, pozostanie w zestawienia zmian.  

### <a name="change-feed-and-_etag-_lsn-or-_ts"></a>Zmień źródło danych i _etag, _lsn lub _ts

_Etag format wewnętrzny i użytkownik nie powinna przyjmować zależności, ponieważ go można zmienić w dowolnym momencie. _ts jest modyfikację lub tworzenia sygnatury czasowej. _Ts służy do porównywania chronologicznym. _lsn to identyfikator wsadu, który jest dodawany wyłącznie dla źródła zmian. reprezentuje identyfikator transakcji. Wiele elementów może być tym samym _lsn. Tag ETag na FeedResponse różni się od _etag, którą widzisz w elemencie. _etag jest wewnętrznym identyfikatorem i służy do współbieżności kontroli informuje o wersji elementu, natomiast element ETag jest używany do sekwencjonowania źródła danych.

## <a name="change-feed-use-cases-and-scenarios"></a>Przypadki użycia i scenariuszy kanału informacyjnego zmian

Umożliwia wydajne przetwarzanie dużych zestawów danych z dużą liczbę zapisów kanału informacyjnego zmian. Kanał informacyjny zmian zapewnia również alternatywę do wykonywania zapytań w całym zestawie danych, aby zidentyfikować, co zmieniło się.

### <a name="use-cases"></a>Przypadki zastosowań

Na przykład za pomocą zestawienia zmian można wykonać następujące zadania efektywnie:

* Aktualizacja pamięci podręcznej, zaktualizować indeksu wyszukiwania lub magazynu danych z danymi przechowywanymi w usłudze Azure Cosmos DB.

* Zaimplementuj warstwy i archiwizację danych na poziomie aplikacji, na przykład przechowuj "gorącą dane" w Azure Cosmos DB i o wieku "zimnych" danych do innych systemów magazynowania, na przykład [Azure Blob Storage](../storage/common/storage-introduction.md).

* Wykonaj zero migracje czas przestoju do innego konta usługi Azure Cosmos lub innego kontenera w usłudze Azure Cosmos kluczem różnych partycji logicznej.

* Implementowanie [architektury lambda](https://blogs.technet.microsoft.com/msuspartner/2016/01/27/azure-partner-community-big-data-advanced-analytics-and-lambda-architecture/) przy użyciu Azure Cosmos DB, gdzie Azure Cosmos DB obsługuje zarówno w czasie rzeczywistym, jak i na potrzeby wykonywania zapytań dotyczących warstw, umożliwiając w ten sposób włączenie architektury lambda przy niskim koszcie.

* Odbieraj i przechowuj dane zdarzeń z urządzeń, czujników, infrastruktury i aplikacji, a następnie Przetwarzaj te zdarzenia w czasie rzeczywistym, na przykład przy użyciu [platformy Spark](../hdinsight/spark/apache-spark-overview.md).  Na poniższej ilustracji przedstawiono, jak można implementować architektury lambda przy użyciu usługi Azure Cosmos DB za pomocą kanału informacyjnego zmian:

![Potok usługi Azure Cosmos DB na podstawie lambda pozyskiwanie danych i zapytań](./media/change-feed/lambda.png)

### <a name="scenarios"></a>Scenariusze

Poniżej przedstawiono niektóre scenariusze, które można łatwo zaimplementować za pomocą kanału informacyjnego zmian:

* W aplikacjach sieci Web [bezserwerowych](https://azure.microsoft.com/solutions/serverless/) lub mobilnych można śledzić zdarzenia, takie jak wszystkie zmiany w profilu lub preferencjach klienta, a także wyzwalać pewne działania, na przykład wysyłanie powiadomień wypychanych do urządzeń przy użyciu [Azure Functions](change-feed-functions.md).

* Jeśli używasz usługi Azure Cosmos DB do tworzenia gier, możesz, na przykład użyj Zmień źródło danych do zaimplementowania rankingi w czasie rzeczywistym, w oparciu o wyniki z gry ukończone.


## <a name="working-with-change-feed"></a>Praca z zestawienia zmian

Możesz pracować z kanału informacyjnego zmian, korzystając z następujących opcji:

* [Używanie kanału informacyjnego zmiany z Azure Functions](change-feed-functions.md)
* [Używanie kanału informacyjnego zmiany z procesorem źródła zmian](change-feed-processor.md) 

Kanał informacyjny zmian jest dostępna dla każdego klucza partycji logicznej w ramach kontenera i jego mogą być rozproszone między jednego lub wielu użytkowników do przetwarzania równoległego, jak pokazano na poniższej ilustracji.

![Rozproszone przetwarzanie zestawienia zmian usługi Azure Cosmos DB](./media/change-feed/changefeedvisual.png)

## <a name="features-of-change-feed"></a>Funkcje Kanał informacyjny zmian

* Kanał informacyjny zmian jest domyślnie włączone dla wszystkich kont usługi Azure Cosmos.

* [Zainicjowanej przepływności](request-units.md) można użyć do odczytu ze źródła zmian, podobnie jak każda inna operacja Azure Cosmos DB, w dowolnym regionie skojarzonym z bazą danych Azure Cosmos.

* Kanał informacyjny zmian zawiera wstawienia i operacje aktualizacji, wprowadzone do elementów w kontenerze. Można przechwycić usuwa przez ustawienie flagi "opcji soft-delete" w obrębie elementów (na przykład dokumenty) zamiast usuwania. Alternatywnie można ustawić skończone okresy wygaśnięcia dla elementów z [możliwością czasu wygaśnięcia](time-to-live.md). Na przykład 24 godziny i użyj Usuwa wartość tej właściwości do przechwytywania. Za pomocą tego rozwiązania należy przetworzyć zmiany w przedziale czasu krótszy niż okres ważności czasu wygaśnięcia. 

* Wszystkie zmiany do elementu dokładnie jeden raz w zestawienia zmian, a klienci muszą zarządzać logiki procesu tworzenia punktów kontrolnych. Jeśli chcesz uniknąć złożoności zarządzania punktami kontrolnymi, procesor źródła zmian zapewnia automatyczne tworzenie punktów kontrolnych i semantykę "co najmniej raz". Zobacz [Używanie kanału informacyjnego zmiany z procesorem źródła zmian](change-feed-processor.md).

* Tylko najnowsze zmiany dla danego elementu znajduje się w dzienniku zmian. Pośrednie zmiany mogą nie być dostępne.

* Kanał informacyjny zmian jest sortowana przez kolejność modyfikacji w każdej wartości klucza partycji logicznej. Nie jest zagwarantowana kolejność, w wartości klucza partycji.

* Zmiany mogą być synchronizowane z dowolnego punktu w czasie, który jest nie okres przechowywania danych, dla której zmiany są dostępne.

* Zmiany są dostępne w sposób równoległy, dla wszystkich kluczy partycji logicznej kontenera usługi Azure Cosmos. Ta funkcja umożliwia zmiany w porównaniu z dużych kontenerów, które ma być przetwarzana równolegle przez wielu odbiorców.

* Aplikacje mogą jednocześnie zażądać wielu źródeł zmian w tym samym kontenerze. ChangeFeedOptions.StartTime może służyć do zapewnienia pierwszego punktu początkowego. Na przykład, aby znaleźć token kontynuacji odpowiadający godziny zegarowej. Token kontynuacji, jeśli zostanie określony, wins przez wartości StartTime i StartFromBeginning. Dokładność ChangeFeedOptions.StartTime jest OK. 5 sekund. 

## <a name="change-feed-in-apis-for-cassandra-and-mongodb"></a>Źródło zmian w interfejsach API dla Cassandra i MongoDB

Funkcja zmiany kanału informacyjnego jest oferowana jako strumień zmiany w interfejsie API MongoDB i zapytanie z predykatem w interfejs API Cassandra. Aby dowiedzieć się więcej na temat szczegółów implementacji interfejsu API MongoDB, zobacz [zmiana strumieni w interfejsie api Azure Cosmos DB dla MongoDB](mongodb-change-streams.md).

Natywny program Apache Cassandra udostępnia funkcję przechwytywania zmian danych (rerzucij), mechanizm do flagi określonych tabel do archiwizacji, a także odrzucanie zapisów w tych tabelach po osiągnięciu konfigurowalnego rozmiaru na dysku dla dziennika przechwytywania zmian. Funkcja zmiany kanału informacyjnego w interfejsie Azure Cosmos DB API for Cassandra zwiększa możliwość wykonywania zapytań o zmiany przy użyciu predykatu za pośrednictwem CQL. Aby dowiedzieć się więcej na temat szczegółów implementacji, zobacz temat [Zmiana kanału informacyjnego w interfejsie API Azure Cosmos DB Cassandra](cassandra-change-feed.md).

## <a name="next-steps"></a>Następne kroki

Można teraz kontynuować, aby dowiedzieć się więcej na temat zmiany źródła danych w następujących artykułach:

* [Opcje odczytu źródła zmian](read-change-feed.md)
* [Używanie kanału informacyjnego zmiany z Azure Functions](change-feed-functions.md)
* [Korzystanie z procesora danych zmiany](change-feed-processor.md)
