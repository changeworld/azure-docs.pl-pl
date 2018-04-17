---
title: Wygasić dane w usłudze Azure DB rozwiązania Cosmos z czasu wygaśnięcia | Dokumentacja firmy Microsoft
description: TTL bazy danych programu Microsoft Azure rozwiązania Cosmos zapewnia możliwość dokumentów automatycznie usunięte z systemu po upływie określonego czasu.
services: cosmos-db
documentationcenter: ''
keywords: czas wygaśnięcia
author: SnehaGunda
manager: kfile
ms.assetid: 25fcbbda-71f7-414a-bf57-d8671358ca3f
ms.service: cosmos-db
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/29/2017
ms.author: sngun
ms.openlocfilehash: 61db8f85e73d2c071bdec0ace60911813fa4f0e8
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="expire-data-in-azure-cosmos-db-collections-automatically-with-time-to-live"></a>Ważność danych w kolekcjach bazy danych rozwiązania Cosmos Azure automatycznie z czasu wygaśnięcia
Aplikacje można tworzyć i przechowywania dużych ilości danych. Niektóre z tych danych, takich jak machine generowane zdarzenie danych, dzienników i użytkownika sesji informacji przydaje się tylko ograniczone okres czasu. Gdy dane będą nadwyżka na potrzeby aplikacji jest bezpieczne przeczyścić tych danych i zmniejszyć wymagania dotyczące magazynu aplikacji.

"Czas wygaśnięcia" lub TTL bazy danych programu Microsoft Azure rozwiązania Cosmos zapewnia możliwość dokumentów automatycznie usunięte z bazy danych po upływie określonego czasu. Domyślny czas wygaśnięcia można ustawić na poziomie kolekcji i zastąpiona na podstawie powiązany z dokumentem. Po ustawieniu TTL domyślnie kolekcji lub na poziomie dokumentu, DB rozwiązania Cosmos automatycznie usunie dokumenty znajdujące się po upływie tego czasu w sekundach od ostatniej modyfikacji.

Czas wygaśnięcia w bazie danych rozwiązania Cosmos używa przesunięcia względem datę ostatniej modyfikacji dokumentu. W tym używa `_ts` pola, które znajduje się na każdy dokument. Pole _ts jest sygnatura czasowa typu unix epoki reprezentujący datę i godzinę. `_ts` Pole jest aktualizowane za każdym razem, gdy dokument zostanie zmodyfikowany. 

## <a name="ttl-behavior"></a>Zachowanie TTL
Funkcja czas wygaśnięcia jest kontrolowana przez właściwości TTL na dwa poziomy - poziomem kolekcji i dokumentu. Wartości są ustawiane w sekundach i są traktowane jako różnicowej z `_ts` czy dokument został ostatnio zmodyfikowany.

1. DefaultTTL dla kolekcji
   
   * Jeśli brakuje (lub ustawionej na wartość null), dokumenty nie są automatycznie usuwane.
   * Jeśli obecny i wartość to "-1" = nieskończone — dokumenty nie wygasa domyślnie
   * Jeśli jest obecny i wartość jest niektórych numer ("n") — dokumenty wygasają "n" sekundach od ostatniej modyfikacji
2. Czas wygaśnięcia dokumentów: 
   
   * Właściwość ma zastosowanie, tylko wtedy, gdy dla kolekcji nadrzędnej DefaultTTL.
   * Zastępuje wartość DefaultTTL dla kolekcji nadrzędnej.

Jak wygasł dokumentu (`ttl`  +  `_ts` < = bieżący czas serwera), dokument jest oznaczony jako "wygasł". Żadna operacja będą dozwolone na tych dokumentów po upływie tego czasu i będzie można wykluczyć z wyników kwerendy wykonywane. Dokumenty są fizycznie usunięte z systemu i są usuwane w tle używana w późniejszym czasie. To nie zużywa żadnego [jednostek żądania (RUs)](request-units.md) z budżetu kolekcji.

W poniższej tabeli można pokazać logiki powyżej:

|  | DefaultTTL Brak nie ustawiać kolekcji | DefaultTTL = -1 w kolekcji | DefaultTTL = "n" w kolekcji |
| --- |:--- |:--- |:--- |
| Brak TTL dokumentu |Nie można zastąpić na poziomie dokumentu, ponieważ dokumentu i kolekcji nie ma żadnych koncepcji TTL. |Wygaśnie żaden dokument w tej kolekcji. |Dokumenty w tej kolekcji wygaśnie po upływie interwału n. |
| Czas wygaśnięcia = -1 do dokumentu |Nie można zastąpić na poziomie dokumentu od kolekcji nie zdefiniowano Właściwość DefaultTTL, którą można zastąpić dokumentu. Wartość TTL dokumentu jest nie interpretowany przez system. |Wygaśnie żaden dokument w tej kolekcji. |Nigdy nie wygasa dokument z = TTL-1 w tej kolekcji. Wszystkie inne dokumenty wygaśnie po upływie interwału "n". |
| Czas wygaśnięcia = n dokumentu |Nie można zastąpić na poziomie dokumentu. Wartość TTL dokumentu jest nie interpretowany przez system. |Dokument z TTL = n wygaśnie po n interwał, w sekundach. Inne dokumenty będą dziedziczyć interwał-1 i nigdy nie wygasa. |Dokument z TTL = n wygaśnie po n interwał, w sekundach. Inne dokumenty będzie dziedziczyć interwał "n" z kolekcji. |

## <a name="configuring-ttl"></a>Konfigurowanie TTL
Domyślnie czas wygaśnięcia jest domyślnie wyłączona, we wszystkich zbiorach DB rozwiązania Cosmos i na wszystkich dokumentach. Czas wygaśnięcia można ustawić programowo lub w portalu Azure w **ustawienia** sekcji dla kolekcji. 

## <a name="enabling-ttl"></a>Włączanie TTL
Włącz TTL kolekcji lub dokumentów w kolekcji, należy ustawić właściwość DefaultTTL kolekcji -1 lub liczbą dodatnią inną niż zero. Ustawienie DefaultTTL-1 oznacza domyślne wszystkich dokumentów w kolekcji, zawsze na żywo, ale usługa DB rozwiązania Cosmos monitorować tej kolekcji dokumentów, które zostały zastąpione to ustawienie domyślne.

    DocumentCollection collectionDefinition = new DocumentCollection();
    collectionDefinition.Id = "orders";
    collectionDefinition.PartitionKey.Paths.Add("/customerId");
    collectionDefinition.DefaultTimeToLive =-1; //never expire by default

    DocumentCollection ttlEnabledCollection = await client.CreateDocumentCollectionAsync(
        UriFactory.CreateDatabaseUri(databaseName),
        collectionDefinition,
        new RequestOptions { OfferThroughput = 20000 });

## <a name="configuring-default-ttl-on-a-collection"></a>Konfigurowanie domyślny czas wygaśnięcia w kolekcji
Jesteś w stanie skonfigurować domyślny czas wygaśnięcia na poziomie kolekcji. Aby ustawić czas wygaśnięcia w kolekcji, należy podać liczbą dodatnią niezerowa wskazuje okres, w sekundach, wygaśnie wszystkich dokumentów w kolekcji po jego ostatniej modyfikacji sygnatury czasowej dokumentu (`_ts`). Alternatywnie można ustawić wartość domyślna -1, co oznacza, że wszystkie dokumenty wstawione do kolekcji będzie domyślnie funkcjonować w nieskończoność.

    DocumentCollection collectionDefinition = new DocumentCollection();
    collectionDefinition.Id = "orders";
    collectionDefinition.PartitionKey.Paths.Add("/customerId");
    collectionDefinition.DefaultTimeToLive = 90 * 60 * 60 * 24; // expire all documents after 90 days
    
    DocumentCollection ttlEnabledCollection = await client.CreateDocumentCollectionAsync(
        "/dbs/salesdb",
        collectionDefinition,
        new RequestOptions { OfferThroughput = 20000 });


## <a name="setting-ttl-on-a-document"></a>Ustawienie TTL w dokumencie
Poza ustawieniem domyślny czas wygaśnięcia w kolekcji można ustawić TTL określonych na poziomie dokumentu. W ten sposób zastępują domyślne w kolekcji.

* Aby ustawić czas wygaśnięcia dokument, musisz podać liczbą dodatnią inną niż zero, co oznacza okres, w sekundach, wygaśnie dokumentu po jego ostatniej modyfikacji sygnatury czasowej dokumentu (`_ts`).
* Jeśli dokument nie zawiera TTL pola, domyślnej kolekcji zostaną zastosowane.
* Jeśli czas wygaśnięcia jest wyłączona na poziomie kolekcji, pola TTL dokumentu zostanie zignorowany, aż do ponownego włączenia TTL w kolekcji.

Oto fragment przedstawiający sposób ustawiania wygaśnięcia TTL w dokumencie:

    // Include a property that serializes to "ttl" in JSON
    public class SalesOrder
    {
        [JsonProperty(PropertyName = "id")]
        public string Id { get; set; }
        
        [JsonProperty(PropertyName="cid")]
        public string CustomerId { get; set; }
        
        // used to set expiration policy
        [JsonProperty(PropertyName = "ttl", NullValueHandling = NullValueHandling.Ignore)]
        public int? TimeToLive { get; set; }
        
        //...
    }
    
    // Set the value to the expiration in seconds
    SalesOrder salesOrder = new SalesOrder
    {
        Id = "SO05",
        CustomerId = "CO18009186470",
        TimeToLive = 60 * 60 * 24 * 30;  // Expire sales orders in 30 days 
    };


## <a name="extending-ttl-on-an-existing-document"></a>Rozszerzanie TTL na istniejącego dokumentu
Wartość TTL dokumentu można zresetować wykonując żadnej operacji zapisu w dokumencie. Spowoduje to ustawi `_ts` bieżący czas i odlicza czas do wygaśnięcia dokumentu, zgodnie z ustawieniami `ttl`, rozpocznie się ponownie. Jeśli chcesz zmienić `ttl` dokumentu, można zaktualizować pola, jak mogą z innego pola można ustawić.

    response = await client.ReadDocumentAsync(
        "/dbs/salesdb/colls/orders/docs/SO05"), 
        new RequestOptions { PartitionKey = new PartitionKey("CO18009186470") });
    
    Document readDocument = response.Resource;
    readDocument.TimeToLive = 60 * 30 * 30; // update time to live
    
    response = await client.ReplaceDocumentAsync(salesOrder);

## <a name="removing-ttl-from-a-document"></a>Usuwanie TTL z dokumentu
Jeśli wartości TTL został ustawiony w dokumencie i nie ma już ten dokument wygasa, następnie można pobrać dokumentu, usuń pola TTL i zastąpić dokument na serwerze. Pola TTL zostanie usunięty z dokumentu, zostaną zastosowane domyślne w kolekcji. Aby zatrzymać dokumentu z wygasa i nie dziedziczy z kolekcji należy ustawić wartość TTL-1.

    response = await client.ReadDocumentAsync(
        "/dbs/salesdb/colls/orders/docs/SO05"), 
        new RequestOptions { PartitionKey = new PartitionKey("CO18009186470") });
    
    Document readDocument = response.Resource;
    readDocument.TimeToLive = null; // inherit the default TTL of the collection
    
    response = await client.ReplaceDocumentAsync(salesOrder);

## <a name="disabling-ttl"></a>Wyłączanie TTL
Wyłączenie TTL całkowicie w kolekcji i zatrzymać proces w tle z wyszukiwanie wygasłe dokumenty Właściwość DefaultTTL w kolekcji należy go usunąć. Usunięcie tej właściwości jest inna niż ustawieniem dla niego wartość -1. Ustawienie, aby nowe dokumenty-1 oznacza dodać do kolekcji, zawsze na żywo, ale można zmienić na określonych dokumentów w kolekcji. Usunięcie tej właściwości całkowicie z kolekcji oznacza, że żaden dokument wygaśnie, nawet jeśli istnieją dokumenty, które zostały jawnie przesłonięte wcześniejszy domyślny.

    DocumentCollection collection = await client.ReadDocumentCollectionAsync("/dbs/salesdb/colls/orders");
    
    // Disable TTL
    collection.DefaultTimeToLive = null;
    
    await client.ReplaceDocumentCollectionAsync(collection);

<a id="ttl-and-index-interaction"></a> 
## <a name="ttl-and-index-interaction"></a>Interakcja TTL i indeks
Dodawanie lub zmienianie ustawienia TTL na kolekcję zmiany podstawowego indeksu. Po zmianie wartości TTL z poza się na kolekcji jest ponownie indeksowane. Podczas wprowadzania zmian do zasady indeksowania, gdy tryb indeksowania jest spójne, użytkownicy nie zauważyć zmian do indeksu. Gdy tryb indeksowania jest ustawiono do opóźnieniem indeks jest zawsze przechwytywanie i po zmianie wartości TTL indeks zostaje odtworzona od początku. Gdy zostanie zmieniona wartość TTL i tryb indeksu jest ustawiony na opóźnieniem, zapytania wykonywane podczas odbudowywania indeksu nie zwracają wyniki pełną lub niepoprawne.

Aby uzyskać dokładne dane zwrócone, nie należy zmieniać wartości TTL podczas indeksowania tryb jest ustawiony na opóźnieniem. W idealnym przypadku spójne indeksu należy wybrać, aby zapewnić spójne wyniki. 

## <a name="faq"></a>Często zadawane pytania
**Co to jest czas wygaśnięcia koszt mnie?**

Nie ma żadnych dodatkowych kosztów do ustawiania wartości TTL w dokumencie.

**Jak długo trwa usuwanie dokumentu po czas wygaśnięcia jest uruchomiony?**

Dokumenty są wygasł natychmiast po czas wygaśnięcia jest uruchomiony i nie będzie dostępny za pośrednictwem CRUD lub kwerendy interfejsów API. 

**Zostanie TTL w dokumencie miały wpływu na RU opłat**

Nie, nie będzie bez wpływu na RU opłat za usunięcia wygasłych dokumentów za pomocą TTL w bazie danych rozwiązania Cosmos.

**Funkcja TTL tylko dotyczy całego dokumenty lub pojedynczy dokument wartości właściwości mogą wygaśnie?**

Czas wygaśnięcia ma zastosowanie do całego dokumentu. Jeśli chcesz tylko części dokumentu wygaśnie, następnie zalecane jest aby wyodrębnić część z głównego dokumentu w oddzielnych "połączony" dokument, a następnie użyć TTL na wyodrębnione dokumentu.

**Funkcja TTL ma szczególne wymagania indeksowania?**

Tak. Kolekcja musi mieć [indeksowania zestawu zasad](indexing-policies.md) spójność lub opóźnieniem. Ustawiany DefaultTTL w kolekcji z indeksowania zestaw None spowoduje błąd, podobnie jak w trakcie wyłączyć indeksowanie w kolekcji, której DefaultTTL został już ustawiony.

## <a name="next-steps"></a>Kolejne kroki
Aby dowiedzieć się więcej na temat bazy danych Azure rozwiązania Cosmos, zapoznaj się z usługą [ *dokumentacji* ](https://azure.microsoft.com/documentation/services/cosmos-db/) strony.

