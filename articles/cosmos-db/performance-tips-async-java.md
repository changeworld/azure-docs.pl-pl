---
title: Wskazówki dotyczące wydajności usługi Azure Cosmos DB dla async Java
description: Poznaj opcje konfiguracji klienta w celu zwiększenia wydajności bazy danych usługi Azure Cosmos
author: SnehaGunda
ms.service: cosmos-db
ms.devlang: java
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: sngun
ms.openlocfilehash: 89df941eb6ebaad6e078c278f1ed883db5528c7e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77152565"
---
# <a name="performance-tips-for-azure-cosmos-db-and-async-java"></a>Porady dotyczące wydajności usługi Azure Cosmos DB i języka Async Java

> [!div class="op_single_selector"]
> * [Java (asynchroniczny)](performance-tips-async-java.md)
> * [Java](performance-tips-java.md)
> * [.NET](performance-tips.md)
> 

Usługa Azure Cosmos DB to szybka i elastyczna rozproszona baza danych, która bezproblemowo skaluje się z gwarantowanym opóźnieniem i przepływnością. Nie trzeba wprowadzać głównych zmian architektury lub napisać złożony kod, aby skalować bazę danych za pomocą usługi Azure Cosmos DB. Skalowanie w górę i w dół jest tak proste, jak wykonywanie wywołania pojedynczego interfejsu API lub wywołania metody SDK. Jednak ponieważ usługa Azure Cosmos DB jest dostępna za pośrednictwem wywołań sieciowych, istnieją optymalizacje po stronie klienta, które można wprowadzić, aby osiągnąć najwyższą wydajność podczas korzystania z [protokołu SQL Async Java SDK](sql-api-sdk-async-java.md).

Więc jeśli pytasz "Jak mogę poprawić wydajność mojej bazy danych?" rozważ następujące opcje:

## <a name="networking"></a>Obsługa sieci

* **Tryb połączenia: użyj trybu direct**
<a id="direct-connection"></a>
    
    Sposób, w jaki klient łączy się z usługą Azure Cosmos DB ma istotny wpływ na wydajność, szczególnie pod względem opóźnienia po stronie klienta. *ConnectionMode* jest ustawienie konfiguracji klucza dostępne do konfigurowania klienta *ConnectionPolicy*. W przypadku asynchronii Java SDK dwa dostępne tryby połączenia to:  
      
    * [Brama (domyślna)](/java/api/com.microsoft.azure.cosmosdb.connectionmode)  
    * [Direct](/java/api/com.microsoft.azure.cosmosdb.connectionmode)

    Tryb bramy jest obsługiwany na wszystkich platformach SDK i jest to opcja skonfigurowana domyślnie. Jeśli aplikacje są uruchamiane w sieci firmowej ze ścisłymi ograniczeniami zapory, tryb bramy jest najlepszym wyborem, ponieważ używa standardowego portu HTTPS i jednego punktu końcowego. Kompromisem wydajności jest jednak to, że tryb bramy obejmuje dodatkowy przeskok sieciowy za każdym razem, gdy dane są odczytywane lub zapisywane w usłudze Azure Cosmos DB. Z tego powodu tryb direct oferuje lepszą wydajność ze względu na mniejszą liczbę przeskoków sieciowych.

    *ConnectionMode* jest skonfigurowany podczas budowy *DocumentClient* wystąpienie z *ConnectionPolicy* parametru.
    
    ```java
        public ConnectionPolicy getConnectionPolicy() {
          ConnectionPolicy policy = new ConnectionPolicy();
          policy.setConnectionMode(ConnectionMode.Direct);
          policy.setMaxPoolSize(1000);
          return policy;
        }

        ConnectionPolicy connectionPolicy = new ConnectionPolicy();
        DocumentClient client = new DocumentClient(HOST, MASTER_KEY, connectionPolicy, null);
    ```

* **Kolokacji klientów w tym samym regionie platformy Azure w celu uzyskania wydajności**<a id="same-region"></a>

    Jeśli to możliwe, umieść wszystkie aplikacje wywołujące usługę Azure Cosmos DB w tym samym regionie co baza danych usługi Azure Cosmos. Dla porównania przybliżonego wywołania usługi Azure Cosmos DB w tym samym regionie zakończyć w ciągu 1-2 ms, ale opóźnienie między zachodnim i wschodnim wybrzeżu STANÓW Zjednoczonych jest >50 ms. To opóźnienie może się różnić od żądania do żądania w zależności od trasy podjęte przez żądanie, jak przechodzi z klienta do granicy centrum danych platformy Azure. Najniższe możliwe opóźnienie uzyskuje się przez zapewnienie, że aplikacja wywołująca znajduje się w tym samym regionie platformy Azure, co aprowizowany punkt końcowy usługi Azure Cosmos DB. Aby uzyskać listę dostępnych regionów, zobacz [Regiony platformy Azure](https://azure.microsoft.com/regions/#services).

    ![Ilustracja zasad połączeń usługi Azure Cosmos DB](./media/performance-tips/same-region.png)

## <a name="sdk-usage"></a>Użycie SDK
* **Instalowanie najnowszego pakietu SDK**

    Zestawy SDK usługi Azure Cosmos DB są stale ulepszane, aby zapewnić najlepszą wydajność. Zobacz strony [SDK usługi Azure Cosmos DB,](sql-api-sdk-async-java.md) aby określić najnowsze zestaw SDK i przejrzeć ulepszenia.

* **Używanie klienta usługi Azure Cosmos DB w celu uzyskania okresu istnienia aplikacji**

    Każde wystąpienie AsyncDocumentClient jest bezpieczne dla wątków i wykonuje wydajne zarządzanie połączeniami i buforowanie adresów. Aby umożliwić efektywne zarządzanie połączeniami i lepszą wydajność przez AsyncDocumentClient, zaleca się użycie pojedynczego wystąpienia AsyncDocumentClient na AppDomain przez cały okres istnienia aplikacji.

   <a id="max-connection"></a>

* **Dostrajanie ConnectionPolicy**

    Domyślnie żądania usługi Cosmos DB trybu bezpośredniego są dokonywane za pośrednictwem protokołu TCP podczas korzystania z asynchronicznego sdk Java. Wewnętrznie SDK używa specjalnej architektury trybu bezpośredniego do dynamicznego zarządzania zasobami sieciowymi i uzyskania najlepszej wydajności.

    W async Java SDK tryb direct jest najlepszym wyborem, aby poprawić wydajność bazy danych przy większości obciążeń. 

    * ***Omówienie trybu bezpośredniego***

        ![Ilustracja architektury trybu bezpośredniego](./media/performance-tips-async-java/rntbdtransportclient.png)

        Architektura po stronie klienta zastosowana w trybie bezpośrednim umożliwia przewidywalne wykorzystanie sieci i multipleksowany dostęp do replik usługi Azure Cosmos DB. Na powyższym diagramie pokazano, jak tryb bezpośredni kieruje żądania klientów do replik w wewnętrznej bazy danych usługi Cosmos. Architektura trybu bezpośredniego przydziela do 10 **kanałów** po stronie klienta na replikę bazy danych. Kanał jest połączenieM TCP poprzedzonym buforem żądań, który jest 30 żądań głęboko. Kanały należące do repliki są dynamicznie przydzielane zgodnie z potrzebami **punktu końcowego usługi repliki**. Gdy użytkownik wystawia żądanie w trybie direct, **TransportClient** kieruje żądanie do punktu końcowego usługi na podstawie klucza partycji. Kolejka **żądań** buforuje żądania przed punktem końcowym usługi.

    * ***Opcje konfiguracji connectionpolicy dla trybu bezpośredniego***

        W pierwszym kroku należy użyć poniższych zalecanych ustawień konfiguracyjnych. Skontaktuj się z [zespołem usługi Azure Cosmos DB,](mailto:CosmosDBPerformanceSupport@service.microsoft.com) jeśli napotkasz problemy w tym konkretnym temacie.

        Jeśli używasz usługi Azure Cosmos DB jako referencyjnej bazy danych (oznacza to, że baza danych jest używana dla wielu operacji odczytu punktu i kilka operacji zapisu), może być dopuszczalne, aby ustawić *idleEndpointTimeout* do 0 (oznacza to, że bez limitu czasu).


        | Opcja konfiguracji       | Domyślne    |
        | :------------------:       | :-----:    |
        | rozmiar strony bufferPageSize             | 8192       |
        | Connectiontimeout          | "PT1M"     |
        | idleChannelCzas na czas         | "PT0S"     |
        | idleEndpointTimeout        | "PT1M10S"  |
        | maxBufferCapacity          | 8388608    |
        | maxChannelsPerEndpoint     | 10         |
        | maxRequestsPerChannel      | 30         |
        | receiveHangDetectionTime   | "PT1M5S"   |
        | żądanieExpiryInterval      | "PT5S"     |
        | requestTimeout             | "PT1M"     |
        | rozwiązanie requestTimerResolution     | "PT0.5S"   |
        | sendHangDetectionCzas      | "PT10S"    |
        | Shutdowntimeout            | "PT15S"    |

    * ***Wskazówki dotyczące programowania trybu bezpośredniego***

        Przejrzyj artykuł [rozwiązywania problemów z zestawem Problemów z zestawem Async Java SDK](troubleshoot-java-async-sdk.md) usługi Azure Cosmos jako punktem odniesienia w celu rozwiązania problemów z zestawem Async Java SDK.

        Kilka ważnych wskazówek dotyczących programowania podczas korzystania z trybu direct:

        + **Użyj wielowątkowej w aplikacji do wydajnego transferu danych TCP** — po złożeniu żądania aplikacja powinna subskrybować odbieranie danych w innym wątku. Nie wymusza to niezamierzone "pół dupleksu" operacji i kolejne żądania są blokowane oczekiwania na odpowiedź poprzedniego żądania.

        + **Wykonywanie obciążeń intensywnie korzystających z mocy obliczeniowej w dedykowanym wątku** — z powodów podobnych do poprzedniej końcówki operacje, takie jak złożone przetwarzanie danych, są najlepiej umieszczane w osobnym wątku. Żądanie, które pobiera dane z innego magazynu danych (na przykład jeśli wątek korzysta z usługi Azure Cosmos DB i spark magazynów danych jednocześnie) może wystąpić zwiększone opóźnienie i zaleca się spawn dodatkowy wątek, który oczekuje na odpowiedź z innych przechowywania danych.

            + Podstawowa sieć We/Wy w async Java SDK jest zarządzany przez Netty, zobacz te [wskazówki dotyczące unikania wzorców kodowania, które blokują wątki We/Wy Netty](troubleshoot-java-async-sdk.md#invalid-coding-pattern-blocking-netty-io-thread).

        + **Modelowanie danych** — usługa Azure Cosmos DB SLA zakłada, że rozmiar dokumentu jest mniejszy niż 1 KB. Optymalizacja modelu danych i programowania w celu faworyzowania mniejszego rozmiaru dokumentu zazwyczaj prowadzi do zmniejszenia opóźnienia. Jeśli zamierzasz potrzebować magazynu i pobierania dokumentów większych niż 1 KB, zalecane podejście jest dla dokumentów do łącza do danych w usłudze Azure Blob Storage.


* **Dostrajanie zapytań równoległych dla kolekcji podzielonych na partycje**

    Usługa Azure Cosmos DB SQL Async Java SDK obsługuje zapytania równoległe, które umożliwiają równoległe wykonywanie zapytań o partycjonowane kolekcje. Aby uzyskać więcej informacji, zobacz [przykłady kodu](https://github.com/Azure/azure-cosmosdb-java/tree/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples) związane z pracą z zestawami SDK. Zapytania równoległe mają na celu zwiększenie opóźnienia zapytania i przepływności za pośrednictwem ich odpowiednika szeregowego.

    * ***Zestaw strojeniaMaxDegreeOfParallelism\:***
    
        Zapytania równoległe działają, badając równolegle wiele partycji. Jednak dane z pojedynczej kolekcji podzielonej na partycje są pobierane szeregowo w odniesieniu do kwerendy. Tak, użyj setMaxDegreeOfParallelism ustawić liczbę partycji, która ma maksymalną szansę osiągnięcia najbardziej wydajne zapytanie, pod warunkiem, że wszystkie inne warunki systemowe pozostają takie same. Jeśli nie znasz liczby partycji, można użyć setMaxDegreeOfParallelism ustawić dużą liczbę, a system wybiera minimalną (liczbę partycji, dane wejściowe podane przez użytkownika) jako maksymalny stopień równoległości.

        Należy pamiętać, że zapytania równoległe dają najlepsze korzyści, jeśli dane są równomiernie rozłożone na wszystkie partycje w odniesieniu do kwerendy. Jeśli kolekcja podzielona na partycje jest podzielona na partycje w taki sposób, że wszystkie lub większość danych zwracanych przez kwerendę jest skoncentrowana w kilku partycjach (jedna partycja w najgorszym przypadku), a następnie wydajność kwerendy będzie wąskie gardło przez te partycje.

    * ***Zestaw dostrajaniaMaxBufferedItemCount\:***
    
        Kwerenda równoległa jest przeznaczona do wstępnego pobierania wyników, podczas gdy bieżąca partia wyników jest przetwarzana przez klienta. Pobieranie wstępne pomaga w ogólnej poprawy opóźnienia kwerendy. setMaxBufferedItemCount ogranicza liczbę wstępnie pobranych wyników. Ustawienie setMaxBufferedItemCount do oczekiwanej liczby zwracanych wyników (lub większej liczby) umożliwia kwerendzie otrzymanie maksymalnych korzyści z pobierania wstępnego.

        Pobieranie wstępne działa w ten sam sposób, niezależnie od MaxDegreeOfParallelism i istnieje pojedynczy bufor dla danych ze wszystkich partycji.

* **Implementowanie wycofywania w interwałach getRetryAfterInMilliseconds**

    Podczas testowania wydajności należy zwiększyć obciążenie, dopóki nie będzie ograniczać niewielkiej liczby żądań. Jeśli jest ograniczona, aplikacja kliencka powinna wycofać się dla interwału ponawiania określonych przez serwer. Poszanowanie backoff zapewnia, że można spędzić minimalną ilość czasu oczekiwania między ponownych prób.

* **Skalowanie w poziomie obciążenia klienta**

    Jeśli testujesz na wysokim poziomie przepływności (>50 000 RU/s), aplikacja kliencka może stać się wąskim gardłem ze względu na ograniczenie komputera w procesorze CPU lub wykorzystaniu sieci. Jeśli osiągniesz ten punkt, możesz kontynuować wypychanie konta usługi Azure Cosmos DB, skalując aplikacje klienckie w poziomie na wielu serwerach.

* **Używanie adresowania opartego na nazwie**

    Użyj adresowania opartego na nazwach, gdzie łącza mają `dbs/MyDatabaseId/colls/MyCollectionId/docs/MyDocumentId`\_format , zamiast `dbs/<database_rid>/colls/<collection_rid>/docs/<document_rid>` SelfLinks (self), które mają format, aby uniknąć pobierania ResourceIds wszystkich zasobów używanych do konstruowania łącza. Ponadto, ponieważ te zasoby są odtworzone (prawdopodobnie o tej samej nazwie), buforowanie ich może nie pomóc.

   <a id="tune-page-size"></a>

* **Dostrajanie rozmiaru strony dla zapytań/kanałów informacyjnych w celu uzyskania lepszej wydajności**

    Podczas wykonywania zbiorczego odczytu dokumentów przy użyciu funkcji dodawania odczytu (na przykład readDocuments) lub podczas wystawiania kwerendy SQL, wyniki są zwracane w sposób segmentowany, jeśli zestaw wyników jest zbyt duży. Domyślnie wyniki są zwracane w fragmentach 100 elementów lub 1 MB, w zależności od tego, który limit zostanie osiągnięty jako pierwszy.

    Aby zmniejszyć liczbę rund sieciowych wymaganych do pobrania wszystkich odpowiednich wyników, można zwiększyć rozmiar strony za pomocą nagłówka żądania [x-ms-max-item-count](/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) do maksymalnie 1000. W przypadkach, gdy trzeba wyświetlić tylko kilka wyników, na przykład jeśli interfejs użytkownika lub interfejs API aplikacji zwraca tylko 10 wyników na czas, można również zmniejszyć rozmiar strony do 10, aby zmniejszyć przepływność zużywaną dla odczytów i zapytań.

    Można również ustawić rozmiar strony przy użyciu setMaxItemCount metody.

* **Użyj odpowiedniego harmonogramu (unikaj kradzieży wątków IO Netty w pętli zdarzeń)**

    Async Java SDK używa [netty](https://netty.io/) do nieblokujące We/Wy. SDK używa stałej liczby wątków pętli zdarzeń netty we/wy (tyle rdzeni procesora CPU, które ma komputer) do wykonywania operacji we/wy. Observable zwracany przez interfejs API emituje wynik w jednym z wątków netty pętli zdarzeń współużytkowania współużytkowania. Dlatego ważne jest, aby nie blokować udostępnionej pętli zdarzeń we/wy netty wątków. Wykonanie intensywnej pracy procesora CPU lub blokowanie operacji w wątku netty pętli zdarzeń operacji we/wy może spowodować zakleszczenie lub znacznie zmniejszyć przepływność SDK.

    Na przykład następujący kod wykonuje intensywnej pracy procesora na wątku netty pętli zdarzeń:

    ```java
    Observable<ResourceResponse<Document>> createDocObs = asyncDocumentClient.createDocument(
      collectionLink, document, null, true);

    createDocObs.subscribe(
      resourceResponse -> {
        //this is executed on eventloop IO netty thread.
        //the eventloop thread is shared and is meant to return back quickly.
        //
        // DON'T do this on eventloop IO netty thread.
        veryCpuIntensiveWork();
      });
    ```

    Po otrzymaniu wyniku, jeśli chcesz wykonać intensywną pracę procesora CPU nad wynikiem, należy unikać wykonywania tego w wątku netty w pętli zdarzeń. Zamiast tego można podać własny harmonogram, aby zapewnić własny wątek do uruchamiania pracy.

    ```java
    import rx.schedulers;

    Observable<ResourceResponse<Document>> createDocObs = asyncDocumentClient.createDocument(
      collectionLink, document, null, true);

    createDocObs.subscribeOn(Schedulers.computation())
    subscribe(
      resourceResponse -> {
        // this is executed on threads provided by Scheduler.computation()
        // Schedulers.computation() should be used only when:
        //   1. The work is cpu intensive 
        //   2. You are not doing blocking IO, thread sleep, etc. in this thread against other resources.
        veryCpuIntensiveWork();
      });
    ```

    W zależności od rodzaju pracy należy użyć odpowiedniego istniejącego Harmonogramu RxJava do swojej pracy. Przeczytaj [``Schedulers``](http://reactivex.io/RxJava/1.x/javadoc/rx/schedulers/Schedulers.html)tutaj .

    Aby uzyskać więcej informacji, zajrzyj na [stronę GitHub](https://github.com/Azure/azure-cosmosdb-java) dla async Java SDK.

* **Wyłącz rejestrowanie netty**

    Rejestrowanie biblioteki Netty jest gadatliwe i musi być wyłączone (pomijanie logowania w konfiguracji może nie wystarczyć), aby uniknąć dodatkowych kosztów procesora. Jeśli nie jesteś w trybie debugowania, całkowicie wyłącz rejestrowanie netty. Więc jeśli używasz log4j, aby usunąć dodatkowe ``org.apache.log4j.Category.callAppenders()`` koszty procesora CPU poniesione przez netty dodać następujący wiersz do bazy kodu:

    ```java
    org.apache.log4j.Logger.getLogger("io.netty").setLevel(org.apache.log4j.Level.OFF);
    ```

 * **Limit zasobów otwartych plików systemu operacyjnego**
 
    Niektóre systemy Linux (takie jak Red Hat) mają górny limit liczby otwartych plików, a więc całkowitą liczbę połączeń. Uruchom następujące czynności, aby wyświetlić bieżące limity:

    ```bash
    ulimit -a
    ```

    Liczba otwartych plików (nofile) musi być wystarczająco duża, aby mieć wystarczająco dużo miejsca dla skonfigurowanego rozmiaru puli połączeń i innych otwartych plików przez system operacyjny. Można go zmodyfikować, aby umożliwić większy rozmiar puli połączeń.

    Otwórz plik limits.conf:

    ```bash
    vim /etc/security/limits.conf
    ```
    
    Dodaj/zmodyfikuj następujące wiersze:

    ```
    * - nofile 100000
    ```

* **Użyj natywnej implementacji SSL dla netty**

    Netty można użyć OpenSSL bezpośrednio dla stosu implementacji SSL, aby osiągnąć lepszą wydajność. W przypadku braku tej konfiguracji netty powróci do domyślnej implementacji SSL java.

    na Ubuntu:
    ```bash
    sudo apt-get install openssl
    sudo apt-get install libapr1
    ```

    i dodaj następującą zależność do zależności maven projektu:
    ```xml
    <dependency>
      <groupId>io.netty</groupId>
      <artifactId>netty-tcnative</artifactId>
      <version>2.0.20.Final</version>
      <classifier>linux-x86_64</classifier>
    </dependency>
    ```

Inne platformy (Red Hat, Windows, Mac itp.) można znaleźć w niniejszych instrukcjachhttps://netty.io/wiki/forked-tomcat-native.html

## <a name="indexing-policy"></a>Zasady indeksowania
 
* **Wykluczanie nieużywanych ścieżek z indeksowania w celu przyspieszenia operacji zapisu**

    Zasady indeksowania usługi Azure Cosmos DB umożliwiają określenie ścieżek dokumentu, które mają być uwzględniane lub wykluczane z indeksowania, wykorzystując ścieżki indeksowania (setIncludedPaths i setExcludedPaths). Użycie ścieżek indeksowania może zaoferować lepszą wydajność zapisu i niższy magazyn indeksu dla scenariuszy, w których wzorce zapytań są znane wcześniej, ponieważ koszty indeksowania są bezpośrednio skorelowane z liczbą indeksowanych ścieżek unikatowych. Na przykład poniższy kod pokazuje, jak wykluczyć całą sekcję dokumentów (znany również jako poddrzewo) z indeksowania przy użyciu symbolu wieloznacznego "*".

    ```Java
    Index numberIndex = Index.Range(DataType.Number);
    numberIndex.set("precision", -1);
    indexes.add(numberIndex);
    includedPath.setIndexes(indexes);
    includedPaths.add(includedPath);
    indexingPolicy.setIncludedPaths(includedPaths);
    collectionDefinition.setIndexingPolicy(indexingPolicy);
    ```

    Aby uzyskać więcej informacji, zobacz [zasady indeksowania usługi Azure Cosmos DB](indexing-policies.md).

## <a name="throughput"></a>Przepływność
<a id="measure-rus"></a>

* **Mierzenie i dostrajanie dla niższych jednostek żądań/drugie użycie**

    Usługa Azure Cosmos DB oferuje bogaty zestaw operacji bazy danych, w tym relacyjnych i hierarchicznych zapytań z UDFs, procedur przechowywanych i wyzwalaczy — wszystkie działające na dokumentach w kolekcji bazy danych. Koszt związany z każdą z tych operacji zależy od procesora, danych We/Wy i pamięci wymaganej do wykonania danej operacji. Zamiast myśleć o zasobach sprzętowych i zarządzać nimi, można myśleć o jednostce żądania (RU) jako o pojedynczej miary dla zasobów wymaganych do wykonywania różnych operacji bazy danych i obsługi żądania aplikacji.

    Przepływność jest aprowizowana na podstawie liczby [jednostek żądań ustawionych](request-units.md) dla każdego kontenera. Zużycie jednostkowe żądania jest oceniane jako stawka na sekundę. Aplikacje, które przekraczają aprowizowaną stawkę jednostkową żądania dla ich kontenera są ograniczone, dopóki stawka spadnie poniżej poziomu aprowizacji dla kontenera. Jeśli aplikacja wymaga wyższego poziomu przepływności, można zwiększyć przepływność, inicjując inicjowanie obsługi administracyjnej dodatkowych jednostek żądań.

    Złożoność kwerendy wpływa na liczbę jednostek żądań są używane dla operacji. Liczba predykatów, charakter predykatów, liczba plików UDF i rozmiar zestawu danych źródłowych mają wpływ na koszt operacji kwerendy.

    Aby zmierzyć obciążenie każdej operacji (tworzenie, aktualizowanie lub usuwanie), sprawdź nagłówek [x-ms-request-charge,](/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) aby zmierzyć liczbę jednostek żądań zużywanych przez te operacje. Można również sprawdzić równoważne RequestCharge właściwości w\<ResourceResponse T> lub\<FeedResponse T>.

    ```Java
    ResourceResponse<Document> response = asyncClient.createDocument(collectionLink, documentDefinition, null,
                                                     false).toBlocking.single();
    response.getRequestCharge();
    ```

    Opłata za żądanie zwrócona w tym nagłówku jest ułamek aprowizowanej przepływności. Na przykład jeśli masz 2000 RU/s aprowizowanych i jeśli poprzednie zapytanie zwraca 1000 1KB dokumentów, koszt operacji wynosi 1000. W związku z tym w ciągu jednej sekundy serwer honoruje tylko dwa takie żądania przed ograniczeniem szybkości kolejnych żądań. Aby uzyskać więcej informacji, zobacz [Jednostki żądania](request-units.md) i [kalkulator jednostki żądania](https://www.documentdb.com/capacityplanner).

<a id="429"></a>
* **Zbyt duża szybkość obchodzenia się z ograniczeniem/żądaniem**

    Gdy klient próbuje przekroczyć przepływność zarezerwowaną dla konta, nie ma pogorszenia wydajności na serwerze i nie ma użycia przepustowości poza poziomem zastrzeżonym. Serwer prewyminanie zakończy żądanie za pomocą RequestRateTooLarge (kod stanu HTTP 429) i zwróci nagłówek [x-ms-retry-after-ms](/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) wskazujący czas w milisekundach, przez który użytkownik musi poczekać przed ponownym użyciem żądania.

        HTTP Status 429,
        Status Line: RequestRateTooLarge
        x-ms-retry-after-ms :100

    Wszystkie SDK wszystkie niejawnie przechwytują tę odpowiedź, respektują nagłówek ponawiania po próbie określone przez serwer i ponów próbę żądania. Jeśli konto nie jest dostępne jednocześnie przez wielu klientów, następna ponowna próby zakończy się pomyślnie.

    Jeśli masz więcej niż jednego klienta łącznie działającego konsekwentnie powyżej szybkości żądania, domyślna liczba ponownych prób aktualnie ustawiona na 9 wewnętrznie przez klienta może nie wystarczyć; w takim przypadku klient zgłasza DocumentClientException z kodem stanu 429 do aplikacji. Domyślną liczbę ponownych prób można zmienić za pomocą setRetryOptions w ConnectionPolicy wystąpienia. Domyślnie DocumentClientException z kodem stanu 429 jest zwracany po skumulowanym czasie oczekiwania 30 sekund, jeśli żądanie nadal działa powyżej stawki żądania. Dzieje się tak nawet wtedy, gdy bieżąca liczba ponownych prób jest mniejsza niż maksymalna liczba ponownych prób, czy to domyślna wartość 9, czy wartość zdefiniowana przez użytkownika.

    Podczas gdy automatyczne zachowanie ponawiania pomaga zwiększyć odporność i użyteczność dla większości aplikacji, może to mieć zastosowanie podczas wykonywania testów porównawczych wydajności, szczególnie podczas pomiaru opóźnienia. Obserwowane przez klienta opóźnienie będzie skok, jeśli eksperyment uderza przepustnicy serwera i powoduje, że zestaw SDK klienta po cichu ponowić próbę. Aby uniknąć skoków opóźnień podczas eksperymentów wydajności, zmierz opłatę zwróconą przez każdą operację i upewnij się, że żądania działają poniżej stawki zarezerwowane żądanie. Aby uzyskać więcej informacji, zobacz [Żądania jednostek](request-units.md).

* **Projektowanie mniejszych dokumentów dla większej przepustowości**

    Opłata za żądanie (koszt przetwarzania żądania) danej operacji jest bezpośrednio skorelowana z rozmiarem dokumentu. Operacje na dużych dokumentach kosztują więcej niż operacje dla małych dokumentów.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat projektowania aplikacji pod kątem skali i wysokiej wydajności, zobacz [Partycjonowanie i skalowanie w usłudze Azure Cosmos DB.](partition-data.md)
