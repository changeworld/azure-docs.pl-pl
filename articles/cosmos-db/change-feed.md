---
title: Praca ze zmianą Obsługa kanału informacyjnego w usłudze Azure Cosmos DB
description: Obsługa kanału informacyjnego zmian usługi Azure Cosmos DB umożliwia śledzenie zmian w dokumentach i wykonać przetwarzanie oparte na zdarzeniach, takich jak wyzwalacze i aktualizowanie systemów pamięci podręczne i analizy.
author: rimman
ms.author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/06/2018
ms.reviewer: sngun
ms.custom: seodec18
ms.openlocfilehash: 85a1dad9feb15550cf27cf032802af5055fdf155
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/12/2019
ms.locfileid: "59525640"
---
# <a name="change-feed-in-azure-cosmos-db---overview"></a>Zmiana źródła danych w usłudze Azure Cosmos DB — omówienie

Obsługa kanału informacyjnego zmian w usłudze Azure Cosmos DB działa przez nasłuchiwanie w kontenerze usługi Azure Cosmos DB zostały wprowadzone zmiany. Następnie wyświetla posortowaną listę dokumentów, które zostały zmienione w kolejności, w której zostały zmodyfikowane. Zmiany zostają utrwalone, mogą być przetwarzane asynchronicznie i przyrostowo, a dane wyjściowe mogą być rozpowszechniane wśród jednego lub większej liczby konsumentów w celu przetwarzania równoległego. 

Usługa Azure Cosmos DB jest odpowiednie dla IoT, gry detaliczna i operacyjne rejestrowania aplikacji. Typowy wzorzec projektowania w tych aplikacjach jest Wyzwól dodatkowe akcje za pomocą zmian danych. Przykłady dodatkowe akcje:

* Wyzwalania powiadomienie lub wywołanie interfejsu API po wstawieniu lub zaktualizowaniu elementu.
* W czasie rzeczywistym strumień przetwarzania IoT lub analizy w czasie rzeczywistym, przetwarzanie danych operacyjnych.
* Dodatkowego przenoszenia danych przez synchronizację z pamięci podręcznej lub aparatu wyszukiwania lub magazynu danych lub archiwizowania danych do zimnego magazynu.

Zmiana źródła danych w usłudze Azure Cosmos DB umożliwia wydajne i skalowalne rozwiązania dla każdego z tych wzorców, jak pokazano na poniższej ilustracji:

![Za pomocą usługi Azure Cosmos DB zmiany źródła danych do zasilania analizy w czasie rzeczywistym i scenariusze obliczeniowe sterowane zdarzeniami](./media/change-feed/changefeedoverview.png)

## <a name="supported-apis-and-client-sdks"></a>Obsługiwane interfejsy API i zestawów SDK klienta

Ta funkcja jest obecnie obsługiwana przez następujących interfejsów API usługi Azure Cosmos DB i zestawów SDK klienta.

| **Sterowniki klienta** | **Interfejs wiersza polecenia platformy Azure** | **INTERFEJS API SQL** | **Interfejs API rozwiązania Cassandra** | **Interfejs API usługi Azure Cosmos DB, bazy danych mongodb** | **Interfejs API języka gremlin**|**Interfejs API tabel** |
| --- | --- | --- | --- | --- | --- | --- |
| .NET | Nie dotyczy | Yes | Nie | Nie | Yes | Nie |
|Java|Nie dotyczy|Yes|Nie|Nie|Yes|Nie|
|Python|Nie dotyczy|Yes|Nie|Nie|Yes|Nie|
|Węzeł/JS|Nie dotyczy|Yes|Nie|Nie|Yes|Nie|

## <a name="change-feed-and-different-operations"></a>Zestawienia zmian i różnych operacji

Już dziś zobaczysz wszystkie operacje w zestawienia zmian. Funkcje, których można kontrolować, Zmień źródło danych dla określonych operacji takich, jak tylko aktualizacje i nie wstawienia nie jest jeszcze dostępna. Można dodać "nietrwałego znacznik" w elemencie aktualizacji i filtr oparty na tym, podczas przetwarzania elementów zestawienia zmian. Obecnie Kanał informacyjny zmian nie dziennika usuwania. Podobnie jak w poprzednim przykładzie, można dodać znacznika nietrwale elementy, które są usuwane, na przykład można dodać atrybutu w elemencie o nazwie "usunięta" i ustaw ją na wartość "true" i ustaw czas wygaśnięcia elementu, dzięki czemu może zostać automatycznie usunięty. Może odczytywać zmiany źródła danych historycznych elementów, na przykład, elementy, które zostały dodane pięć lat temu. Jeśli element nie zostanie usunięty. możesz przeczytać zmiany źródła danych, o ile jest to punkt początkowy kontenera.

### <a name="sort-order-of-items-in-change-feed"></a>Kolejność elementów na liście zestawienia zmian do sortowania

Zmiany elementów kanału informacyjnego pochodzą zgodnie z kolejnością czas ich modyfikacji. Ta kolejność sortowania jest zachowywana na klucz partycji logicznej.

### <a name="change-feed-in-multi-region-azure-cosmos-accounts"></a>Zmiana źródła danych na kontach usługi Azure Cosmos w wielu regionach

Na koncie usługi Azure Cosmos wielu regionów, jeśli region zapisu w trybie Failover, kanał informacyjny zmian będzie działać w operacji ręcznej pracy awaryjnej i będą ciągłe.

### <a name="change-feed-and-time-to-live-ttl"></a>Zmień źródło danych i czas wygaśnięcia (TTL)

Jeśli właściwość czas wygaśnięcia (czas wygaśnięcia) ustawiono element na -1, kanał informacyjny zmian zostanie utrzymany nieskończoność. Jeśli dane nie zostaną usunięte, pozostanie w zestawienia zmian.  

### <a name="change-feed-and-etag-lsn-or-ts"></a>Zmień źródło danych i _etag, _lsn lub _ts

_Etag format wewnętrzny i użytkownik nie powinna przyjmować zależności, ponieważ go można zmienić w dowolnym momencie. _ts jest modyfikację lub tworzenia sygnatury czasowej. _Ts służy do porównywania chronologicznym. _lsn jest identyfikator partii, który jest dodawany do zmiany źródła danych. reprezentuje identyfikator transakcji. Wiele elementów może być tym samym _lsn. Tag ETag na FeedResponse różni się od _etag, którą widzisz w elemencie. _etag jest wewnętrznym identyfikatorem i służy do współbieżności kontroli informuje o wersji elementu, natomiast element ETag jest używany do sekwencjonowania źródła danych.

## <a name="change-feed-use-cases-and-scenarios"></a>Przypadki użycia i scenariuszy kanału informacyjnego zmian

Umożliwia wydajne przetwarzanie dużych zestawów danych z dużą liczbę zapisów kanału informacyjnego zmian. Kanał informacyjny zmian zapewnia również alternatywę do wykonywania zapytań w całym zestawie danych, aby zidentyfikować, co zmieniło się.

### <a name="use-cases"></a>Przypadki zastosowań

Na przykład za pomocą zestawienia zmian można wykonać następujące zadania efektywnie:

* Aktualizacja pamięci podręcznej, zaktualizować indeksu wyszukiwania lub magazynu danych z danymi przechowywanymi w usłudze Azure Cosmos DB.

* Implementowanie danych na poziomie aplikacji warstw i archiwizacji, na przykład przechowywania "gorących danych" w usłudze Azure Cosmos DB i przedawniają "zimnych danych" do innych systemów pamięci masowej, na przykład [usługi Azure Blob Storage](../storage/common/storage-introduction.md).

* Wykonaj zero migracje czas przestoju do innego konta usługi Azure Cosmos lub innego kontenera w usłudze Azure Cosmos kluczem różnych partycji logicznej.

* Implementowanie [architektury lambda](https://blogs.technet.microsoft.com/msuspartner/2016/01/27/azure-partner-community-big-data-advanced-analytics-and-lambda-architecture/) przy użyciu usługi Azure Cosmos DB, w którym usługi Azure Cosmos DB obsługuje usługi batch w czasie rzeczywistym i zapytania obsługująca warstwy, umożliwiając w ten sposób architektury lambda przy użyciu niski całkowity koszt posiadania.

* Odbieranie i przechowywania danych o zdarzeniach z urządzeń, czujników, infrastruktury i aplikacji oraz przetwarzania tych zdarzeń w czasie rzeczywistym, na przykład za pomocą [Spark](../hdinsight/spark/apache-spark-overview.md).  Na poniższej ilustracji przedstawiono, jak można implementować architektury lambda przy użyciu usługi Azure Cosmos DB za pomocą kanału informacyjnego zmian:

![Potok usługi Azure Cosmos DB na podstawie lambda pozyskiwanie danych i zapytań](./media/change-feed/lambda.png)

### <a name="scenarios"></a>Scenariusze

Poniżej przedstawiono niektóre scenariusze, które można łatwo zaimplementować za pomocą kanału informacyjnego zmian:

* W ramach Twojej [bezserwerowe](https://azure.microsoft.com/solutions/serverless/) aplikacje internetowe lub mobilne, można śledzić zdarzenia, takie jak wszystkie zmiany do profilu klienta, preferencje lub ich lokalizacji i wyzwalania określonych czynności, na przykład wysyłanie powiadomień wypychanych do urządzeń za pomocą [usługi Azure Functions](change-feed-functions.md).

* Jeśli używasz usługi Azure Cosmos DB do tworzenia gier, możesz, na przykład użyj Zmień źródło danych do zaimplementowania rankingi w czasie rzeczywistym, w oparciu o wyniki z gry ukończone.


## <a name="working-with-change-feed"></a>Praca z zestawienia zmian

Możesz pracować z kanału informacyjnego zmian, korzystając z następujących opcji:

* [Za pomocą zmian źródła danych za pomocą usługi Azure Functions](change-feed-functions.md)
* [Za pomocą zmian źródła danych z biblioteką procesora zestawienia zmian](change-feed-processor.md) 

Kanał informacyjny zmian jest dostępna dla każdego klucza partycji logicznej w ramach kontenera i jego mogą być rozproszone między jednego lub wielu użytkowników do przetwarzania równoległego, jak pokazano na poniższej ilustracji.

![Rozproszone przetwarzanie zestawienia zmian usługi Azure Cosmos DB](./media/change-feed/changefeedvisual.png)

## <a name="features-of-change-feed"></a>Funkcje Kanał informacyjny zmian

* Kanał informacyjny zmian jest domyślnie włączone dla wszystkich kont usługi Azure Cosmos.

* Możesz użyć swojej [aprowizowanej przepływności](request-units.md) odczytywanie zestawienia zmian, podobnie jak dowolnej innej operacji usługi Azure Cosmos DB, we wszystkich regionach skojarzonych z bazą danych Azure Cosmos.

* Kanał informacyjny zmian zawiera wstawienia i operacje aktualizacji, wprowadzone do elementów w kontenerze. Można przechwycić usuwa przez ustawienie flagi "opcji soft-delete" w obrębie elementów (na przykład dokumenty) zamiast usuwania. Alternatywnie, można ustawić okres ważności skończoną dla elementów z [możliwości TTL](time-to-live.md). Na przykład 24 godziny i użyj Usuwa wartość tej właściwości do przechwytywania. Za pomocą tego rozwiązania należy przetworzyć zmiany w przedziale czasu krótszy niż okres ważności czasu wygaśnięcia. 

* Wszystkie zmiany do elementu dokładnie jeden raz w zestawienia zmian, a klienci muszą zarządzać logiki procesu tworzenia punktów kontrolnych. Jeśli chcesz uniknąć skomplikowanych konfiguracji Zarządzanie punktami kontrolnymi biblioteką procesora zestawienia zmian zapewnia automatyczne tworzenie punktów kontrolnych i "co najmniej raz" semantyki. Zobacz [przy użyciu zmian źródła danych z biblioteką procesora zestawienia zmian](change-feed-processor.md).

* Tylko najnowsze zmiany dla danego elementu znajduje się w dzienniku zmian. Pośrednie zmiany mogą nie być dostępne.

* Kanał informacyjny zmian jest sortowana przez kolejność modyfikacji w każdej wartości klucza partycji logicznej. Nie jest zagwarantowana kolejność, w wartości klucza partycji.

* Zmiany mogą być synchronizowane z dowolnego punktu w czasie, który jest nie okres przechowywania danych, dla której zmiany są dostępne.

* Zmiany są dostępne w sposób równoległy, dla wszystkich kluczy partycji logicznej kontenera usługi Azure Cosmos. Ta funkcja umożliwia zmiany w porównaniu z dużych kontenerów, które ma być przetwarzana równolegle przez wielu odbiorców.

* Aplikacje mogą żądać równocześnie wiele zmian źródła danych na tym samym kontenerze. ChangeFeedOptions.StartTime może służyć do zapewnienia pierwszego punktu początkowego. Na przykład, aby znaleźć token kontynuacji odpowiadający godziny zegarowej. Token kontynuacji, jeśli zostanie określony, wins przez wartości StartTime i StartFromBeginning. Dokładność ChangeFeedOptions.StartTime jest OK. 5 sekund. 

## <a name="next-steps"></a>Kolejne kroki

Można teraz kontynuować, aby dowiedzieć się więcej na temat zmiany źródła danych w następujących artykułach:

* [Opcje na odczytywanie zestawienia zmian](read-change-feed.md)
* [Za pomocą zmian źródła danych za pomocą usługi Azure Functions](change-feed-functions.md)
* [Za pomocą zmian źródła danych z biblioteką procesora](change-feed-processor.md)
