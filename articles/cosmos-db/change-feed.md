---
title: Praca z obsługą kanału informacyjnego zmian w Azure Cosmos DB
description: Użyj Azure Cosmos DB obsługi kanału informacyjnego zmiany, aby śledzić zmiany w dokumentach i przeprowadzać przetwarzanie oparte na zdarzeniach, takie jak wyzwalacze i utrzymywanie Aktualności pamięci podręcznych i systemów analitycznych.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.reviewer: sngun
ms.custom: seodec18
ms.openlocfilehash: 8e6bd3dadd636127f212db0ea0c0755a6b52a087
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/22/2019
ms.locfileid: "72757020"
---
# <a name="change-feed-in-azure-cosmos-db---overview"></a>Źródło zmian w Azure Cosmos DB — Omówienie

Obsługa kanałów informacyjnych zmian w Azure Cosmos DB działa przez nasłuchiwanie kontenera usługi Azure Cosmos dla wszelkich zmian. Następnie tworzone są dane wyjściowe w postaci posortowanej listy zmienionych dokumentów w kolejności, w której zostały zmodyfikowane. Zmiany zostają utrwalone, mogą być przetwarzane asynchronicznie i przyrostowo, a dane wyjściowe mogą być rozpowszechniane wśród jednego lub większej liczby konsumentów w celu przetwarzania równoległego. 

Azure Cosmos DB doskonale nadaje się w przypadku aplikacji IoT, gier, sprzedaży detalicznej i rejestrowania operacyjnego. Typowy Wzorzec projektowy w tych aplikacjach polega na użyciu zmian danych w celu wyzwolenia dodatkowych akcji. Przykłady dodatkowych akcji obejmują:

* Wyzwalanie powiadomienia lub wywołania interfejsu API, gdy element zostanie wstawiony lub zaktualizowany.
* Przetwarzanie strumienia w czasie rzeczywistym dla usługi IoT lub przetwarzania analiz w czasie rzeczywistym na danych operacyjnych.
* Dodatkowe przenoszenie danych przez synchronizację z pamięcią podręczną lub aparatem wyszukiwania lub magazynem danych lub archiwizowanie danych w chłodnym magazynie.

Źródło zmian w Azure Cosmos DB umożliwia tworzenie wydajnych i skalowalnych rozwiązań dla każdego z tych wzorców, jak pokazano na poniższej ilustracji:

![Korzystanie z kanału informacyjnego zmiany Azure Cosmos DB do analiz w czasie rzeczywistym i scenariuszy obliczeniowych opartych na zdarzeniach](./media/change-feed/changefeedoverview.png)

## <a name="supported-apis-and-client-sdks"></a>Obsługiwane interfejsy API i zestawy SDK klienta

Ta funkcja jest obecnie obsługiwana przez następujące Azure Cosmos DB interfejsów API i zestawów SDK klienta.

| **Sterowniki klienta** | **Interfejs wiersza polecenia platformy Azure** | **INTERFEJS API SQL** | **interfejs API Cassandra** | **Interfejs API Azure Cosmos DB dla MongoDB** | **Interfejs API Gremlin**|**Interfejs API tabel** |
| --- | --- | --- | --- | --- | --- | --- |
| .NET | Nie dotyczy | Tak | Nie | Nie | Tak | Nie |
|Java|Nie dotyczy|Tak|Nie|Nie|Tak|Nie|
|Python|Nie dotyczy|Tak|Nie|Nie|Tak|Nie|
|Node/JS|Nie dotyczy|Tak|Nie|Nie|Tak|Nie|

## <a name="change-feed-and-different-operations"></a>Zmień źródło danych i różne operacje

Dzisiaj wszystkie operacje są wyświetlane w kanale zmian. Funkcja, w której można kontrolować źródła zmian, dla określonych operacji, takich jak tylko aktualizacje, a nie do wstawiania, jest jeszcze niedostępna. Można dodać "miękki znacznik" w elemencie dla aktualizacji i filtru na podstawie tego, czy podczas przetwarzania elementów ze źródła zmian. Obecnie Źródło zmian nie powoduje usunięcia dziennika. Podobnie jak w poprzednim przykładzie, można dodać znacznik elastyczny dla elementów, które są usuwane, na przykład można dodać atrybut w elemencie o nazwie "usunięty" i ustawić go na wartość "true" i ustawić dla elementu wartość TTL, tak aby można go było automatycznie usunąć. Możesz odczytać Źródło zmian dla elementów historycznych (Ostatnia zmiana odpowiadająca elementowi nie obejmuje zmian pośrednich), na przykład elementów, które zostały dodane pięć lat temu. Jeśli element nie został usunięty, można odczytać kanał informacyjny zmiany jako źródło kontenera.

### <a name="sort-order-of-items-in-change-feed"></a>Porządek sortowania elementów w strumieniu zmian

Zmiany elementów kanału informacyjnego są wprowadzane w kolejności ich modyfikacji. Ta kolejność sortowania jest gwarantowana na klucz partycji logicznej.

### <a name="change-feed-in-multi-region-azure-cosmos-accounts"></a>Źródło zmian w wieloregionowych kontach usługi Azure Cosmos

W wieloregionowym koncie usługi Azure Cosmos, jeśli region zapisu zostanie przełączona w tryb failover, kanał informacyjny zmiany będzie działać w ramach operacji ręcznego przełączania awaryjnego i będzie ciągły.

### <a name="change-feed-and-time-to-live-ttl"></a>Zmień źródło danych i czas wygaśnięcia (TTL)

Jeśli właściwość czasu wygaśnięcia (Time to Live) jest ustawiona dla elementu na wartość-1, kanał informacyjny zmiany będzie trwały w nieskończoność. Jeśli dane nie zostaną usunięte, pozostanie w strumieniu zmian.  

### <a name="change-feed-and-_etag-_lsn-or-_ts"></a>Zmień źródło danych i _etag, _lsn lub _ts

Format _etag jest wewnętrzny i nie powinien być zależny od niego, ponieważ może ulec zmianie w dowolnym momencie. _ts jest modyfikacją lub sygnaturą czasową utworzenia. Można użyć _ts do chronologicznego porównania. _lsn to identyfikator wsadu, który jest dodawany wyłącznie do źródła zmian. reprezentuje identyfikator transakcji. Wiele elementów może mieć ten sam _lsn. Element ETag w FeedResponse różni się od _etag widocznej dla elementu. _etag jest identyfikatorem wewnętrznym i jest używany na potrzeby kontroli współbieżności informuje o wersji elementu, natomiast element ETag jest używany do sekwencjonowania źródła danych.

## <a name="change-feed-use-cases-and-scenarios"></a>Zmień przypadki użycia i scenariusze w kanale informacyjnym

Kanał informacyjny zmiany umożliwia wydajne przetwarzanie dużych zestawów danych przy użyciu dużej ilości operacji zapisu. Kanał informacyjny zmiany oferuje również alternatywę do wykonywania zapytań dotyczących całego zestawu danych w celu zidentyfikowania zmian.

### <a name="use-cases"></a>Przypadki zastosowań

Na przykład ze źródłem zmian można wydajnie wykonywać następujące zadania:

* Zaktualizuj pamięć podręczną, zaktualizuj indeks wyszukiwania lub zaktualizuj magazyn danych przy użyciu danych przechowywanych w Azure Cosmos DB.

* Zaimplementuj warstwy i archiwizację danych na poziomie aplikacji, na przykład przechowuj "gorącą dane" w Azure Cosmos DB i o wieku "zimnych" danych do innych systemów magazynowania, na przykład [Azure Blob Storage](../storage/common/storage-introduction.md).

* Wykonaj zero migracji w dół do innego konta usługi Azure Cosmos lub innego kontenera platformy Azure Cosmos z innym kluczem partycji logicznej.

* Implementowanie [architektury lambda](https://blogs.technet.microsoft.com/msuspartner/2016/01/27/azure-partner-community-big-data-advanced-analytics-and-lambda-architecture/) przy użyciu Azure Cosmos DB, gdzie Azure Cosmos DB obsługuje zarówno w czasie rzeczywistym, jak i na potrzeby wykonywania zapytań dotyczących warstw, umożliwiając w ten sposób włączenie architektury lambda przy niskim koszcie.

* Odbieraj i przechowuj dane zdarzeń z urządzeń, czujników, infrastruktury i aplikacji, a następnie Przetwarzaj te zdarzenia w czasie rzeczywistym, na przykład przy użyciu [platformy Spark](../hdinsight/spark/apache-spark-overview.md).  Na poniższej ilustracji przedstawiono sposób implementacji architektury lambda przy użyciu Azure Cosmos DB za pośrednictwem źródła zmian:

![Potok lambda oparty na Azure Cosmos DBach na potrzeby pozyskiwania i zapytania](./media/change-feed/lambda.png)

### <a name="scenarios"></a>Scenariusze

Poniżej przedstawiono niektóre scenariusze, które można łatwo zaimplementować ze źródłem zmian:

* W aplikacjach sieci Web [bezserwerowych](https://azure.microsoft.com/solutions/serverless/) lub mobilnych można śledzić zdarzenia, takie jak wszystkie zmiany w profilu lub preferencjach klienta, a także wyzwalać pewne działania, na przykład wysyłanie powiadomień wypychanych do urządzeń przy użyciu [platformy Azure Funkcja](change-feed-functions.md).

* Jeśli używasz Azure Cosmos DB do kompilowania gry, możesz na przykład użyć kanału informacyjnego zmiany w celu zaimplementowania rankingi w czasie rzeczywistym na podstawie wyników z ukończonych gier.


## <a name="working-with-change-feed"></a>Praca ze źródłem zmian

Możesz współpracować ze źródłem zmian, korzystając z następujących opcji:

* [Używanie kanału informacyjnego zmiany z Azure Functions](change-feed-functions.md)
* [Używanie kanału informacyjnego zmiany z procesorem źródła zmian](change-feed-processor.md) 

Kanał informacyjny zmiany jest dostępny dla każdego klucza partycji logicznej w kontenerze i może być dystrybuowany przez jednego lub kilku odbiorców do przetwarzania równoległego, jak pokazano na poniższej ilustracji.

![Rozproszone przetwarzanie Azure Cosmos DB źródła zmian](./media/change-feed/changefeedvisual.png)

## <a name="features-of-change-feed"></a>Funkcje źródła zmian

* Kanał informacyjny zmiany jest domyślnie włączony dla wszystkich kont usługi Azure Cosmos.

* [Zainicjowanej przepływności](request-units.md) można użyć do odczytu ze źródła zmian, podobnie jak każda inna operacja Azure Cosmos DB, w dowolnym regionie skojarzonym z bazą danych Azure Cosmos.

* Kanał informacyjny zmiany zawiera operacje wstawiania i aktualizacji wykonywane do elementów w kontenerze. Można przechwytywać usunięcia, ustawiając flagę "unsoft-Delete" w obrębie elementów (na przykład dokumentów) zamiast usunięć. Alternatywnie można ustawić skończone okresy wygaśnięcia dla elementów z [możliwością czasu wygaśnięcia](time-to-live.md). Na przykład, 24 godziny i użyj wartości tej właściwości do przechwytywania usunięć. W tym rozwiązaniu należy przetwarzać zmiany w krótszym przedziale czasu niż okres ważności czasu wygaśnięcia. 

* Każda zmiana elementu pojawia się dokładnie jeden raz w źródle zmian, a klienci muszą zarządzać logiką tworzenia punktów kontrolnych. Jeśli chcesz uniknąć złożoności zarządzania punktami kontrolnymi, procesor źródła zmian zapewnia automatyczne tworzenie punktów kontrolnych i semantykę "co najmniej raz". Zobacz [Używanie kanału informacyjnego zmiany z procesorem źródła zmian](change-feed-processor.md).

* Tylko Ostatnia zmiana dla danego elementu jest uwzględniona w dzienniku zmian. Zmiany pośrednie mogą być niedostępne.

* Kanał informacyjny zmiany jest sortowany według kolejności modyfikacji w ramach każdej wartości klucza partycji logicznej. Nie ma kolejności gwarantowanej w wartościach klucza partycji.

* Zmiany można synchronizować z dowolnego punktu w czasie, w którym nie ma stałego okresu przechowywania danych, dla którego zmiany są dostępne.

* Zmiany są dostępne równolegle dla wszystkich kluczy partycji logicznych kontenera usługi Azure Cosmos. Dzięki tej możliwości zmiany z dużych kontenerów mogą być przetwarzane równolegle przez wielu użytkowników.

* Aplikacje mogą jednocześnie zażądać wielu źródeł zmian w tym samym kontenerze. ChangeFeedOptions. StartTime może służyć do zapewnienia początkowego punktu początkowego. Na przykład, aby znaleźć token kontynuacji odpowiadający podanemu czasowi zegara. ContinuationToken, jeśli jest określony, usługa WINS na wartości StartTime i StartFromBeginning. Precyzja ChangeFeedOptions. StartTime to ~ 5 s. 

## <a name="next-steps"></a>Następne kroki

Teraz możesz dowiedzieć się więcej na temat źródła zmian w następujących artykułach:

* [Opcje odczytu źródła zmian](read-change-feed.md)
* [Używanie kanału informacyjnego zmiany z Azure Functions](change-feed-functions.md)
* [Korzystanie z procesora danych zmiany](change-feed-processor.md)
