---
title: Wskazówki dotyczące wydajności usługi Azure Cosmos DB dla oprogramowania Java
description: Poznaj opcje konfiguracji klienta w celu zwiększenia wydajności bazy danych usługi Azure Cosmos
author: SnehaGunda
ms.service: cosmos-db
ms.devlang: java
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: sngun
ms.openlocfilehash: a20b7d91a927d48a14812110ca714491cd726071
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80548775"
---
# <a name="performance-tips-for-azure-cosmos-db-and-java"></a>Porady dotyczące wydajności usługi Azure Cosmos DB i języka Java

> [!div class="op_single_selector"]
> * [Java (asynchroniczny)](performance-tips-async-java.md)
> * [Java](performance-tips-java.md)
> * [.NET](performance-tips.md)
> 

Usługa Azure Cosmos DB to szybka i elastyczna rozproszona baza danych, która bezproblemowo skaluje się z gwarantowanym opóźnieniem i przepływnością. Nie trzeba wprowadzać głównych zmian architektury lub napisać złożony kod, aby skalować bazę danych za pomocą usługi Azure Cosmos DB. Skalowanie w górę i w dół jest tak proste, jak wykonywanie pojedynczego wywołania interfejsu API. Aby dowiedzieć się więcej, zobacz [jak aprowizować przepływność kontenera](how-to-provision-container-throughput.md) lub [jak aprowizować przepływność bazy danych](how-to-provision-database-throughput.md). Jednak ponieważ usługa Azure Cosmos DB jest dostępna za pośrednictwem wywołań sieciowych, istnieją optymalizacje po stronie klienta, które można wprowadzić, aby osiągnąć najwyższą wydajność podczas korzystania z [protokołu SQL Java SDK](documentdb-sdk-java.md).

Więc jeśli pytasz "Jak mogę poprawić wydajność mojej bazy danych?" rozważ następujące opcje:

## <a name="networking"></a>Networking
<a id="direct-connection"></a>

1. **Tryb połączenia: używanie funkcji DirectHttps**

    Sposób, w jaki klient nawiązuje połączenie z usługą Azure Cosmos DB, ma istotny wpływ na wydajność, szczególnie jeśli chodzi o zaobserwowane opóźnienie po stronie klienta. Dostępne jest jedno ustawienie konfiguracji klucza do konfigurowania klienta [ConnectionPolicy](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.connectionpolicy) – [ConnectionMode](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.connectionmode).  Dwa dostępne connectionmodes są:

   1. [Brama (domyślna)](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.connectionmode)
   2. [Bezpośredniehttps](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.connectionmode)

      Tryb bramy jest obsługiwany na wszystkich platformach SDK i jest skonfigurowany domyślnie.  Jeśli aplikacja działa w sieci firmowej ze ścisłymi ograniczeniami zapory, Gateway jest najlepszym wyborem, ponieważ używa standardowego portu HTTPS i jednego punktu końcowego. Kompromisem wydajności jest jednak to, że tryb bramy obejmuje dodatkowy przeskok sieciowy za każdym razem, gdy dane są odczytywane lub zapisywane w usłudze Azure Cosmos DB. Z tego powodu tryb DirectHttps oferuje lepszą wydajność ze względu na mniejszą liczbę przeskoków sieciowych. 

      Java SDK używa protokołu HTTPS jako protokołu transportu. Protokół HTTPS używa protokołu TLS do uwierzytelniania początkowego i szyfrowania ruchu. Podczas korzystania z java SDK musi być otwarty tylko port HTTPS 443. 

      ConnectionMode jest skonfigurowany podczas budowy DocumentClient wystąpienie z ConnectionPolicy parametru. 

      ```Java
      public ConnectionPolicy getConnectionPolicy() {
        ConnectionPolicy policy = new ConnectionPolicy();
        policy.setConnectionMode(ConnectionMode.DirectHttps);
        policy.setMaxPoolSize(1000);
        return policy;
      }
        
      ConnectionPolicy connectionPolicy = new ConnectionPolicy();
      DocumentClient client = new DocumentClient(HOST, MASTER_KEY, connectionPolicy, null);
      ```

      ![Ilustracja zasad połączeń usługi Azure Cosmos DB](./media/performance-tips-java/connection-policy.png)

   <a id="same-region"></a>
2. **Kolokacji klientów w tym samym regionie platformy Azure w celu uzyskania wydajności**

    Jeśli to możliwe, umieść wszystkie aplikacje wywołujące usługę Azure Cosmos DB w tym samym regionie co baza danych usługi Azure Cosmos. Dla porównania przybliżonego wywołania usługi Azure Cosmos DB w tym samym regionie zakończyć w ciągu 1-2 ms, ale opóźnienie między zachodnim i wschodnim wybrzeżu STANÓW Zjednoczonych jest >50 ms. To opóźnienie może się różnić od żądania do żądania w zależności od trasy podjęte przez żądanie, jak przechodzi z klienta do granicy centrum danych platformy Azure. Najniższe możliwe opóźnienie uzyskuje się przez zapewnienie, że aplikacja wywołująca znajduje się w tym samym regionie platformy Azure, co aprowizowany punkt końcowy usługi Azure Cosmos DB. Aby uzyskać listę dostępnych regionów, zobacz [Regiony platformy Azure](https://azure.microsoft.com/regions/#services).

    ![Ilustracja zasad połączeń usługi Azure Cosmos DB](./media/performance-tips/same-region.png)
   
## <a name="sdk-usage"></a>Użycie SDK
1. **Instalowanie najnowszego pakietu SDK**

    Zestawy SDK usługi Azure Cosmos DB są stale ulepszane, aby zapewnić najlepszą wydajność. Zobacz strony [SDK usługi Azure Cosmos DB,](documentdb-sdk-java.md) aby określić najnowsze zestaw SDK i przejrzeć ulepszenia.
2. **Używanie klienta usługi Azure Cosmos DB w celu uzyskania okresu istnienia aplikacji**

    Każde [wystąpienie DocumentClient](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentclient) jest bezpieczne dla wątków i wykonuje efektywne zarządzanie połączeniami i buforowanie adresów podczas pracy w trybie bezpośrednim. Aby umożliwić efektywne zarządzanie połączeniami i lepszą wydajność przez DocumentClient, zaleca się użycie pojedynczego wystąpienia DocumentClient per AppDomain przez cały okres istnienia aplikacji.

   <a id="max-connection"></a>
3. **Zwiększanie maksymalnej wielkości maksymalnej liczby hostów podczas korzystania z trybu bramy**

    Żądania usługi Azure Cosmos DB są dokonywane za pośrednictwem protokołu HTTPS/REST podczas korzystania z trybu bramy i są poddawane domyślnemu limitowi połączenia na adres hosta lub adres IP. Może być konieczne ustawienie MaxPoolSize na wyższą wartość (200-1000), aby biblioteka klienta mogła korzystać z wielu jednoczesnych połączeń z usługą Azure Cosmos DB. W pliku Java SDK wartość domyślna dla [connectionpolicy.getMaxPoolSize](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.connectionpolicy.getmaxpoolsize) wynosi 100. Użyj [setMaxPoolSize,]( https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.connectionpolicy.setmaxpoolsize) aby zmienić wartość.

4. **Dostrajanie zapytań równoległych dla kolekcji podzielonych na partycje**

    Usługa Azure Cosmos DB SQL Java SDK w wersji 1.9.0 i powyżej obsługuje zapytania równoległe, które umożliwiają równoległe wykonywanie zapytań o partycjonowane kolekcję. Aby uzyskać więcej informacji, zobacz [przykłady kodu](https://github.com/Azure/azure-documentdb-java/tree/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples) związane z pracą z zestawami SDK. Zapytania równoległe mają na celu zwiększenie opóźnienia zapytania i przepływności za pośrednictwem ich odpowiednika szeregowego.

    (a) ***Strojenie setMaxDegreeOfParallelism\: *** Zapytania równoległe działają przez wykonywanie zapytań o wiele partycji równolegle. Jednak dane z pojedynczej kolekcji podzielonej na partycje są pobierane szeregowo w odniesieniu do kwerendy. Tak, użyj [setMaxDegreeOfParallelism](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.feedoptions.setmaxdegreeofparallelism) ustawić liczbę partycji, która ma maksymalną szansę osiągnięcia najbardziej wydajne zapytanie, pod warunkiem, że wszystkie inne warunki systemowe pozostają takie same. Jeśli nie znasz liczby partycji, można użyć setMaxDegreeOfParallelism ustawić dużą liczbę, a system wybiera minimalną (liczbę partycji, dane wejściowe podane przez użytkownika) jako maksymalny stopień równoległości. 

    Należy pamiętać, że zapytania równoległe dają najlepsze korzyści, jeśli dane są równomiernie rozłożone na wszystkie partycje w odniesieniu do kwerendy. Jeśli kolekcja podzielona na partycje jest podzielona na partycje w taki sposób, że wszystkie lub większość danych zwracanych przez kwerendę jest skoncentrowana w kilku partycjach (jedna partycja w najgorszym przypadku), a następnie wydajność kwerendy będzie wąskie gardło przez te partycje.

    (b) ***Dostrajanie setMaxBufferedItemCount\: *** Parallel kwerenda jest przeznaczony do wstępnego pobierania wyników, podczas gdy bieżąca partia wyników jest przetwarzany przez klienta. Pobieranie wstępne pomaga w ogólnej poprawy opóźnienia kwerendy. setMaxBufferedItemCount ogranicza liczbę wstępnie pobranych wyników. Przez ustawienie [setMaxBufferedItemCount](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.feedoptions.setmaxbuffereditemcount) do oczekiwanej liczby wyników zwracanych (lub większą liczbę), dzięki temu kwerendy, aby otrzymać maksymalne korzyści z pobierania wstępnego.

    Pobieranie wstępne działa w ten sam sposób, niezależnie od MaxDegreeOfParallelism i istnieje pojedynczy bufor dla danych ze wszystkich partycji.  

5. **Implementowanie wycofywania w interwałach getRetryAfterInMilliseconds**

    Podczas testowania wydajności należy zwiększyć obciążenie, dopóki nie będzie ograniczać niewielkiej liczby żądań. Jeśli jest ograniczona, aplikacja kliencka powinna wycofać się z przepustnicy dla interwału ponawiania określonego przez serwer. Poszanowanie backoff zapewnia, że można spędzić minimalną ilość czasu oczekiwania między ponownych prób. Obsługa zasad ponawiania próby jest uwzględniona w wersji 1.8.0 i wyższej od [zestawie Java SDK](documentdb-sdk-java.md). Aby uzyskać więcej informacji, zobacz [getRetryAfterInMilliseconds](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentclientexception.getretryafterinmilliseconds).

6. **Skalowanie w poziomie obciążenia klienta**

    Jeśli testujesz na wysokim poziomie przepływności (>50 000 RU/s), aplikacja kliencka może stać się wąskim gardłem ze względu na ograniczenie komputera w procesorze CPU lub wykorzystaniu sieci. Jeśli osiągniesz ten punkt, możesz kontynuować wypychanie konta usługi Azure Cosmos DB, skalując aplikacje klienckie w poziomie na wielu serwerach.

7. **Używanie adresowania opartego na nazwie**

    Użyj adresowania opartego na nazwach, gdzie łącza mają `dbs/MyDatabaseId/colls/MyCollectionId/docs/MyDocumentId`\_format , zamiast `dbs/<database_rid>/colls/<collection_rid>/docs/<document_rid>` SelfLinks (self), które mają format, aby uniknąć pobierania ResourceIds wszystkich zasobów używanych do konstruowania łącza. Ponadto, ponieważ te zasoby są odtworzone (prawdopodobnie o tej samej nazwie), buforowanie tych może nie pomóc.

   <a id="tune-page-size"></a>
8. **Dostrajanie rozmiaru strony dla zapytań/kanałów informacyjnych w celu uzyskania lepszej wydajności**

    Podczas wykonywania zbiorczego odczytu dokumentów przy użyciu funkcji dodawania odczytu (na przykład [readDocuments)](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentclient.readdocuments)lub podczas wystawiania kwerendy SQL, wyniki są zwracane w sposób segmentowany, jeśli zestaw wyników jest zbyt duży. Domyślnie wyniki są zwracane w fragmentach 100 elementów lub 1 MB, w zależności od tego, który limit zostanie osiągnięty jako pierwszy.

    Aby zmniejszyć liczbę rund sieciowych wymaganych do pobrania wszystkich odpowiednich wyników, można zwiększyć rozmiar strony za pomocą nagłówka żądania [x-ms-max-item-count](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) do maksymalnie 1000. W przypadkach, gdy trzeba wyświetlić tylko kilka wyników, na przykład jeśli interfejs użytkownika lub interfejs API aplikacji zwraca tylko 10 wyników na czas, można również zmniejszyć rozmiar strony do 10, aby zmniejszyć przepływność zużywaną dla odczytów i zapytań.

    Rozmiar strony można również ustawić za pomocą [metody setPageSize](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.feedoptionsbase.setpagesize).

## <a name="indexing-policy"></a>Zasady indeksowania
 
1. **Wykluczanie nieużywanych ścieżek z indeksowania w celu przyspieszenia operacji zapisu**

    Zasady indeksowania usługi Azure Cosmos DB umożliwiają określenie, które ścieżki dokumentu mają być uwzględniane lub wykluczane z indeksowania, wykorzystując ścieżki indeksowania[(setIncludedPaths](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.indexingpolicy.setincludedpaths) i [setExcludedPaths](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.indexingpolicy.setexcludedpaths)). Użycie ścieżek indeksowania może zaoferować lepszą wydajność zapisu i niższy magazyn indeksu dla scenariuszy, w których wzorce zapytań są znane wcześniej, ponieważ koszty indeksowania są bezpośrednio skorelowane z liczbą indeksowanych ścieżek unikatowych.  Na przykład poniższy kod pokazuje, jak wykluczyć całą sekcję dokumentów (aka. poddrzewa) z indeksowania przy użyciu symbolu wieloznacznego "*".

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

1. **Mierzenie i dostrajanie dla niższych jednostek żądań/drugie użycie**

    Usługa Azure Cosmos DB oferuje bogaty zestaw operacji bazy danych, w tym relacyjnych i hierarchicznych zapytań z UDFs, procedur przechowywanych i wyzwalaczy — wszystkie działające na dokumentach w kolekcji bazy danych. Koszt związany z każdą z tych operacji zależy od procesora, danych We/Wy i pamięci wymaganej do wykonania danej operacji. Zamiast myśleć o zasobach sprzętowych i zarządzać nimi, można myśleć o jednostce żądania (RU) jako o pojedynczej miary dla zasobów wymaganych do wykonywania różnych operacji bazy danych i obsługi żądania aplikacji.

    Przepływność jest aprowizowana na podstawie liczby [jednostek żądań ustawionych](request-units.md) dla każdego kontenera. Zużycie jednostkowe żądania jest oceniane jako stawka na sekundę. Aplikacje, które przekraczają aprowizowaną stawkę jednostkową żądania dla ich kontenera są ograniczone, dopóki stawka spadnie poniżej poziomu aprowizacji dla kontenera. Jeśli aplikacja wymaga wyższego poziomu przepływności, można zwiększyć przepływność, inicjując inicjowanie obsługi administracyjnej dodatkowych jednostek żądań. 

    Złożoność kwerendy wpływa na liczbę jednostek żądań są używane dla operacji. Liczba predykatów, charakter predykatów, liczba plików UDF i rozmiar zestawu danych źródłowych mają wpływ na koszt operacji kwerendy.

    Aby zmierzyć obciążenie każdej operacji (tworzenie, aktualizowanie lub usuwanie), sprawdź nagłówek [x-ms-request-charge](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-response-headers) (lub równoważną właściwość RequestCharge w [ResourceResponse\<T>](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.resourceresponse) lub [FeedResponse\<T>](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.feedresponse) zmierzyć liczbę jednostek żądań zużywanych przez te operacje.

    ```Java
    ResourceResponse<Document> response = client.createDocument(collectionLink, documentDefinition, null, false);

    response.getRequestCharge();
    ```             

    Opłata za żądanie zwrócona w tym nagłówku jest ułamek aprowizowanej przepływności. Na przykład jeśli masz 2000 RU/s aprowizowanych i jeśli poprzednie zapytanie zwraca 1000 1KB dokumentów, koszt operacji wynosi 1000. W związku z tym w ciągu jednej sekundy serwer honoruje tylko dwa takie żądania przed ograniczeniem szybkości kolejnych żądań. Aby uzyskać więcej informacji, zobacz [Jednostki żądania](request-units.md) i [kalkulator jednostki żądania](https://www.documentdb.com/capacityplanner).
   <a id="429"></a>
1. **Zbyt duża szybkość obchodzenia się z ograniczeniem/żądaniem**

    Gdy klient próbuje przekroczyć przepływność zarezerwowaną dla konta, nie ma pogorszenia wydajności na serwerze i nie ma użycia przepustowości poza poziomem zastrzeżonym. Serwer prewyminanie zakończy żądanie za pomocą RequestRateTooLarge (kod stanu HTTP 429) i zwróci nagłówek [x-ms-retry-after-ms](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-response-headers) wskazujący czas w milisekundach, przez który użytkownik musi poczekać przed ponownym użyciem żądania.

        HTTP Status 429,
        Status Line: RequestRateTooLarge
        x-ms-retry-after-ms :100

    Wszystkie SDK wszystkie niejawnie przechwytują tę odpowiedź, respektują nagłówek ponawiania po próbie określone przez serwer i ponów próbę żądania. Jeśli konto nie jest dostępne jednocześnie przez wielu klientów, następna ponowna próby zakończy się pomyślnie.

    Jeśli masz więcej niż jednego klienta łącznie działającego konsekwentnie powyżej szybkości żądania, domyślna liczba ponownych prób aktualnie ustawiona na 9 wewnętrznie przez klienta może nie wystarczyć; w takim przypadku klient zgłasza [DocumentClientException](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentclientexception) z kodem stanu 429 do aplikacji. Domyślną liczbę ponownych prób można zmienić za pomocą [setRetryOptions](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.connectionpolicy.setretryoptions) w [ConnectionPolicy](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.connectionpolicy) wystąpienia. Domyślnie DocumentClientException z kodem stanu 429 jest zwracany po skumulowanym czasie oczekiwania 30 sekund, jeśli żądanie nadal działa powyżej stawki żądania. Dzieje się tak nawet wtedy, gdy bieżąca liczba ponownych prób jest mniejsza niż maksymalna liczba ponownych prób, czy to domyślna wartość 9, czy wartość zdefiniowana przez użytkownika.

    Podczas gdy automatyczne zachowanie ponawiania pomaga zwiększyć odporność i użyteczność dla większości aplikacji, może to mieć zastosowanie podczas wykonywania testów porównawczych wydajności, szczególnie podczas pomiaru opóźnienia.  Obserwowane przez klienta opóźnienie będzie skok, jeśli eksperyment uderza przepustnicy serwera i powoduje, że zestaw SDK klienta po cichu ponowić próbę. Aby uniknąć skoków opóźnień podczas eksperymentów wydajności, zmierz opłatę zwróconą przez każdą operację i upewnij się, że żądania działają poniżej stawki zarezerwowane żądanie. Aby uzyskać więcej informacji, zobacz [Żądania jednostek](request-units.md).
3. **Projektowanie mniejszych dokumentów dla większej przepustowości**

    Opłata za żądanie (koszt przetwarzania żądania) danej operacji jest bezpośrednio skorelowana z rozmiarem dokumentu. Operacje na dużych dokumentach kosztują więcej niż operacje dla małych dokumentów.

## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się więcej na temat projektowania aplikacji pod kątem skali i wysokiej wydajności, zobacz [Partycjonowanie i skalowanie w usłudze Azure Cosmos DB.](partition-data.md)
