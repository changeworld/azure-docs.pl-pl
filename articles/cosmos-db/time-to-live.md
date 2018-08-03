---
title: Wygasanie danych w usłudze Azure Cosmos DB przy użyciu czasu wygaśnięcia | Dokumentacja firmy Microsoft
description: Czas wygaśnięcia Microsoft Azure Cosmos DB zapewnia możliwość wprowadzenia dokumentów automatycznie usuwane z systemu po upływie określonego czasu.
services: cosmos-db
keywords: czas wygaśnięcia
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 08/29/2017
ms.author: sngun
ms.openlocfilehash: 49f6d6ee65ffae71cba8c73301355bfe2bdcd1d6
ms.sourcegitcommit: fc5555a0250e3ef4914b077e017d30185b4a27e6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/03/2018
ms.locfileid: "39480560"
---
# <a name="expire-data-in-azure-cosmos-db-collections-automatically-with-time-to-live"></a>Wygasanie danych w kolekcjach usługi Azure Cosmos DB automatycznie przy użyciu czasu wygaśnięcia
Aplikacje można tworzyć i przechowywać ogromne ilości danych. Niektóre z tych danych, takich jak maszyny wygenerowanych zdarzeń danych, dzienników i użytkownika sesji informacji przydaje się tylko na czas określony czas. Gdy dane będą nadwyżki na potrzeby aplikacji, jest bezpieczne przeczyścić dane i zmniejszyć wymagania magazynu aplikacji.

"Czas wygaśnięcia" lub czas wygaśnięcia Microsoft Azure Cosmos DB zapewnia możliwość wprowadzenia dokumentów automatycznie usuwane z bazy danych, po upływie określonego czasu. Domyślny czas wygaśnięcia można ustawić na poziomie kolekcji i zastąpione na podstawie poszczególnych dokumentów. Po ustawieniu TTL jako domyślnego kolekcji lub na poziomie dokumentu Cosmos DB automatycznie usunie dokumenty znajdujące się po upływie tego czasu w sekundach, od czasu ostatniej modyfikacji.

Czas wygaśnięcia w usłudze Azure Cosmos DB używa przesunięcie względem ostatniej modyfikacji dokumentu. W tym używa `_ts` pola, które istnieje w każdym dokumencie. Pola _ts jest sygnatura czasowa typu unix epoki reprezentujący datę i godzinę. `_ts` Jest ono aktualizowane za każdym razem, gdy dokument zostanie zmodyfikowany. 

## <a name="ttl-behavior"></a>Zachowanie czasu wygaśnięcia
Funkcja czas wygaśnięcia jest kontrolowana przez właściwości TTL na dwóch poziomach — na poziomie kolekcji i poziomie dokumentu. Wartości są ustawiane w ciągu kilku sekund i są traktowane jako różnicowych z `_ts` czy dokument został ostatnio zmodyfikowany.

1. DefaultTTL dla kolekcji
   
   * Jeśli brak (lub ustawionej na wartość null) dokumenty nie są usuwane automatycznie.
   * Jeśli istnieje, a wartość jest równa "-1" = nieskończonej — dokumenty nie wygasa domyślnie
   * Jeśli istnieje, a wartość jest ustawiona na niektórych numer ("n") — dokumenty wygasają "n" (w sekundach) po ostatniej modyfikacji
2. Czas wygaśnięcia dokumentów: 
   
   * Właściwość dotyczy tylko wtedy, gdy dla kolekcji nadrzędnej DefaultTTL.
   * Zastępuje wartość DefaultTTL kolekcji nadrzędnej.

Jak najszybciej wygasł dokumentu (`ttl`  +  `_ts` < = bieżący czas serwera), dokument zostanie oznaczony jako "wygasła." Operacja nie może być w tych dokumentach, po upływie tego czasu i będzie można wykluczyć z wyników wszelkie zapytania wykonywane. Dokumenty są fizycznie usunięte z systemu i są usuwane w tle używana w późniejszym czasie. To nie używa żadnego [jednostek żądań (ru)](request-units.md) z budżetu kolekcji.

Powyższe logiki można wyświetlać w poniższej tabeli:

|  | DefaultTTL brakuje nie nastavit kolekcji | DefaultTTL = -1 w kolekcji | DefaultTTL = "n" w kolekcji |
| --- |:--- |:--- |:--- |
| Czas wygaśnięcia Brak dokumentu |Nie ma niczego do zastąpić na poziomie dokumentu, ponieważ dokumentu i kolekcji mają koncepcja czasu wygaśnięcia. |Wygaśnie żaden dokument w tej kolekcji. |Dokumenty w tej kolekcji wygaśnie po upływie interwału n. |
| Czas wygaśnięcia = -1 w dokumencie |Nie ma niczego do zastąpić na poziomie dokumentu, ponieważ kolekcja nie definiuje właściwości DefaultTTL, którą można zastąpić dokumentu. Wartość TTL dokumentu jest niezinterpretowane przez system. |Wygaśnie żaden dokument w tej kolekcji. |Nigdy nie wygasa dokumentu z czasem wygaśnięcia = 1 w tej kolekcji. Inne dokumenty wygaśnie po upływie interwału "n". |
| Czas wygaśnięcia = n dokumentu |Nie ma niczego do zastąpić na poziomie dokumentu. Wartość TTL dokumentu jest niezinterpretowane przez system. |Dokument z czasem wygaśnięcia = n wygasną po upływie n interwał, w ciągu kilku sekund. Inne dokumenty będą dziedziczyć z przedziału od -1 i nigdy nie wygasa. |Dokument z czasem wygaśnięcia = n wygasną po upływie n interwał, w ciągu kilku sekund. Inne dokumenty będą dziedziczyć "n" Interwał kolekcji. |

## <a name="configuring-ttl"></a>Konfigurowanie czasu wygaśnięcia
Domyślny czas wygaśnięcia jest domyślnie wyłączona, we wszystkich kolekcjach usługi Cosmos DB i na wszystkich dokumentach. Czas wygaśnięcia można ustawić programowo lub za pomocą witryny Azure portal. Aby skonfigurować czas wygaśnięcia z witryny Azure portal, wykonaj następujące kroki:

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com/) i przejdź do swojego konta usługi Azure Cosmos DB.  

2. Przejdź do kolekcji, w której chcesz ustawić wartość TTL, otwórz **skalowanie i ustawienia** okienka. Widać, że czas wygaśnięcia jest domyślnie ustawiona na **poza**. Można go zmienić **na (Brak wartości domyślnej)** lub **na**.

   **Wyłącz** — dokumenty nie są usuwane automatycznie.  
   **na (Brak wartości domyślnej)** — ta opcja umożliwia ustawienie wartości TTL "-1" (bez ograniczeń czasowych) oznacza to, dokumenty nie wygasa domyślnie.  
   **na** — dokumenty wygasają "n" sekund po ostatniej modyfikacji.  

   ![Ustaw czas wygaśnięcia](./media/time-to-live/set-ttl-in-portal.png)

## <a name="enabling-ttl"></a>Włączanie czas wygaśnięcia
Aby włączyć TTL w kolekcji lub dokumentów w kolekcji, należy ustawić właściwość DefaultTTL kolekcji na wartość -1 lub różna od zera dodatnia. Ustawienia DefaultTTL-1 oznacza, że domyślny wszystkie dokumenty w kolekcji, nieskończoność na żywo, ale usługi Cosmos DB monitorować tej kolekcji dokumentów, które ma zastąpić to ustawienie domyślne.

    DocumentCollection collectionDefinition = new DocumentCollection();
    collectionDefinition.Id = "orders";
    collectionDefinition.PartitionKey.Paths.Add("/customerId");
    collectionDefinition.DefaultTimeToLive =-1; //never expire by default

    DocumentCollection ttlEnabledCollection = await client.CreateDocumentCollectionAsync(
        UriFactory.CreateDatabaseUri(databaseName),
        collectionDefinition,
        new RequestOptions { OfferThroughput = 20000 });

## <a name="configuring-default-ttl-on-a-collection"></a>Konfigurowanie domyślnego czasu wygaśnięcia na kolekcję
Jesteś w stanie skonfigurować domyślny czas wygaśnięcia na poziomie kolekcji. Aby ustawić czas wygaśnięcia w kolekcji, należy podać różna od zera dodatnia wskazujący okres, w ciągu kilku sekund wygaśnie wszystkie dokumenty w kolekcji po ostatniej modyfikacji sygnatura czasowa dokumentu (`_ts`). Alternatywnie można ustawić domyślną wartość-1, co oznacza, że wszystkie dokumenty wstawione do kolekcji będą znajdować się na czas nieokreślony domyślnie.

    DocumentCollection collectionDefinition = new DocumentCollection();
    collectionDefinition.Id = "orders";
    collectionDefinition.PartitionKey.Paths.Add("/customerId");
    collectionDefinition.DefaultTimeToLive = 90 * 60 * 60 * 24; // expire all documents after 90 days
    
    DocumentCollection ttlEnabledCollection = await client.CreateDocumentCollectionAsync(
        "/dbs/salesdb",
        collectionDefinition,
        new RequestOptions { OfferThroughput = 20000 });


## <a name="setting-ttl-on-a-document"></a>Ustawienie czasu wygaśnięcia do dokumentu
Oprócz skonfigurowania domyślny czas wygaśnięcia w kolekcji, można ustawić określonego czasu wygaśnięcia na poziomie dokumentu. W ten sposób spowoduje zastąpienie domyślnej kolekcji.

* Aby ustawić czas wygaśnięcia na dokument, musisz podać różna od zera dodatnia, co oznacza okres, w ciągu kilku sekund do wygaśnięcia dokumentu po ostatniej modyfikacji sygnatura czasowa dokumentu (`_ts`).
* Jeśli dokument nie ma czasu wygaśnięcia pola, domyślne w kolekcji zostaną zastosowane.
* Jeśli czas wygaśnięcia jest wyłączona na poziomie kolekcji, pola Czas wygaśnięcia w dokumencie zostaną zignorowane, aż do czasu wygaśnięcia ponownie włączenia w kolekcji.

Poniżej przedstawiono fragment przedstawiająca sposób ustawić czas wygaśnięcia TTL w dokumencie:

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


## <a name="extending-ttl-on-an-existing-document"></a>Rozszerzanie TTL dla istniejącego dokumentu
Możesz zresetować czasu wygaśnięcia do dokumentu, wykonując żadnych operacji zapisu dokumentu. Spowoduje to ustawienie ustawi `_ts` do bieżącej godziny i odliczanie do wygaśnięcia dokumentu, zgodnie z ustawieniem `ttl`, rozpoczną się ponownie. Jeśli chcesz zmienić `ttl` dokumentu, można zaktualizować pola, tak jak inne pola do ustawienia.

    response = await client.ReadDocumentAsync(
        "/dbs/salesdb/colls/orders/docs/SO05"), 
        new RequestOptions { PartitionKey = new PartitionKey("CO18009186470") });
    
    Document readDocument = response.Resource;
    readDocument.TimeToLive = 60 * 30 * 30; // update time to live
    
    response = await client.ReplaceDocumentAsync(readDocument);

## <a name="removing-ttl-from-a-document"></a>Usuwanie TTL dokumentu
Jeśli nie chcesz już tego dokumentu do wygaśnięcia czasu wygaśnięcia została ustawiona w dokumencie, następnie można pobrać dokumentu, usuń pola Czas wygaśnięcia i zastąpić dokument na serwerze. Po usunięciu pola Czas wygaśnięcia z dokumentu domyślnego kolekcji zostaną zastosowane. Aby zatrzymać dokumentu przed wygaśnięciem, a nie dziedziczą z kolekcji należy następnie ustaw wartość czasu wygaśnięcia na -1.

    response = await client.ReadDocumentAsync(
        "/dbs/salesdb/colls/orders/docs/SO05"), 
        new RequestOptions { PartitionKey = new PartitionKey("CO18009186470") });
    
    Document readDocument = response.Resource;
    readDocument.TimeToLive = null; // inherit the default TTL of the collection
    
    response = await client.ReplaceDocumentAsync(readDocument);

## <a name="disabling-ttl"></a>Wyłączenie czasu wygaśnięcia
Wyłączenie czasu wygaśnięcia całkowicie w kolekcji i Zatrzymaj proces w tle z szukasz wygasłe dokumenty Właściwość DefaultTTL kolekcji powinny być usuwane. Usunięcie tej właściwości jest inny niż ustawienie go na -1. Ustawienie na wartość-1 oznacza, że nowe dokumenty dodawane do kolekcji, nieskończoność na żywo, ale można ją zastąpić w określonych dokumentach w kolekcji. Usunięcie tej właściwości z kolekcji oznacza, że żaden dokument wygaśnie, nawet w przypadku dokumentów, które zostały jawnie przesłonięte wcześniejszy domyślny.

    DocumentCollection collection = await client.ReadDocumentCollectionAsync("/dbs/salesdb/colls/orders");
    
    // Disable TTL
    collection.DefaultTimeToLive = null;
    
    await client.ReplaceDocumentCollectionAsync(collection);

<a id="ttl-and-index-interaction"></a> 
## <a name="ttl-and-index-interaction"></a>Czas wygaśnięcia i indeks interakcji
Dodanie lub zmiana ustawienie czasu wygaśnięcia kolekcji zmienia podstawowego indeksu. Po zmianie wartości TTL z poza się na kolekcji jest ponownie indeksowane. Podczas wprowadzania zmian zasad indeksowania, przy włączonym trybie indeksowania spójne, użytkownicy nie zauważyć zmiany do indeksu. Podczas indeksowania tryb ustawiono z opóźnieniem, indeks jest zawsze przechwytywanie i po zmianie wartości TTL indeksu są odtwarzane od podstaw. Po zmodyfikowaniu wartości TTL i tryb indeksu jest równa z opóźnieniem, zapytania wykonywane podczas odbudowywanie indeksu nie zwracają pełną lub niepoprawne wyniki.

Aby uzyskać dokładne dane zwrócone, nie należy zmieniać wartości TTL podczas indeksowania tryb jest ustawiony na z opóźnieniem. W idealnym spójne indeks powinien zostać dobrany zapewnić spójne wyniki. 

## <a name="faq"></a>Często zadawane pytania
**Jaki będzie TTL koszt mnie?**

Istnieje ustawienie czasu wygaśnięcia do dokumentu bez żadnych dodatkowych kosztów.

**Jak długo potrwa można usunąć dokumentu, gdy czas wygaśnięcia jest uruchomiona?**

Dokumenty są wygasłe natychmiast, gdy czas wygaśnięcia jest włączony i nie będzie dostępny za pośrednictwem operacji CRUD lub interfejsami API zapytań. 

**Zostanie wartość TTL dokumentu ma żadnego wpływu na opłaty RU**

Nie, nie będzie żadnego wpływu na RU opłaty usunięcia wygasłych dokumentów przy użyciu czasu wygaśnięcia w usłudze Cosmos DB.

**Funkcją czasu wygaśnięcia dotyczą tylko całe dokumenty, czy można unieważnić wartości właściwości pojedynczego dokumentu?**

Czas wygaśnięcia ma zastosowanie do całego dokumentu. Jeśli chcesz tylko części dokumentu wygaśnie, następnie zalecane jest, prowadzenie część głównego dokumentu do innego dokumentu "połączone", a następnie użyć czasu wygaśnięcia na wyodrębniony dokumentu.

**Funkcja TTL ma szczególne wymagania indeksowania?**

Tak. Kolekcja musi mieć [zestawu zasad indeksowania](indexing-policies.md) spójność lub leniwy. Ustawiany DefaultTTL w kolekcji za pomocą indeksowania zestaw None spowoduje błąd, podobnie jak próby wyłączyć indeksowanie w kolekcji zawierającej DefaultTTL, został już ustawiony.

## <a name="next-steps"></a>Kolejne kroki
Aby dowiedzieć się więcej o usłudze Azure Cosmos DB, zapoznaj się z usługą [ *dokumentacji* ](https://azure.microsoft.com/documentation/services/cosmos-db/) strony.

