---
title: Porady dotyczące wydajności Azure Cosmos DB dla platformy .NET
description: Dowiedz się więcej na temat opcji konfiguracji klienta, aby zwiększyć wydajność usługi Azure Cosmos Database
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 01/15/2020
ms.author: sngun
ms.openlocfilehash: eec5ab6cdf4afd63db2e77046bb19436e600ece6
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/24/2020
ms.locfileid: "76721000"
---
# <a name="performance-tips-for-azure-cosmos-db-and-net"></a>Porady dotyczące wydajności Azure Cosmos DB i .NET

> [!div class="op_single_selector"]
> * [Java (asynchroniczny)](performance-tips-async-java.md)
> * [Java](performance-tips-java.md)
> * [.NET](performance-tips.md)
> 

Azure Cosmos DB to szybka i elastyczna dystrybuowana baza danych, która bezproblemowo skaluje się do gwarantowanych opóźnień i przepływności. Nie trzeba wprowadzać głównych zmian architektury ani pisać złożonego kodu w celu skalowania bazy danych za pomocą Azure Cosmos DB. Skalowanie w górę i w dół jest tak proste jak w przypadku jednego wywołania interfejsu API. Aby dowiedzieć się więcej, zobacz [jak zainicjować przepływność kontenera](how-to-provision-container-throughput.md) lub [jak zapewnić przepływność bazy danych](how-to-provision-database-throughput.md). Ponieważ jednak dostęp do Azure Cosmos DB jest uzyskiwany za pośrednictwem wywołań sieciowych, istnieją optymalizacje po stronie klienta, które umożliwiają osiągnięcie szczytowej wydajności podczas korzystania z [zestawu SQL .NET SDK](sql-api-sdk-dotnet-standard.md).

Tak więc w przypadku pytania "jak można poprawić wydajność bazy danych?" Należy wziąć pod uwagę następujące opcje:

## <a name="networking"></a>Networking
<a id="direct-connection"></a>

1. **Zasady połączenia: Użyj trybu połączenia bezpośredniego**

    Sposób, w jaki klient nawiązuje połączenie z usługą Azure Cosmos DB, ma istotny wpływ na wydajność, szczególnie jeśli chodzi o zaobserwowane opóźnienie po stronie klienta. Dostępne są dwa ustawienia konfiguracji klucza do konfigurowania zasad połączenia klienta — *tryb* połączenia i *Protokół*połączenia.  Dostępne są dwa tryby:

   * Tryb bramy
      
     Tryb bramy jest obsługiwany na wszystkich platformach SDK i jest skonfigurowany domyślnie dla [zestawu SDK Microsoft. Azure. DocumentDB](sql-api-sdk-dotnet.md). Jeśli aplikacja działa w sieci firmowej z rygorystycznymi ograniczeniami zapory, najlepszym wyborem jest tryb bramy, ponieważ używa on standardowego portu HTTPS i jednego punktu końcowego. Jednak jest to, że tryb bramy obejmuje dodatkowy przeskok sieciowy za każdym razem, gdy dane są odczytywane lub zapisywane w Azure Cosmos DB. Z tego powodu tryb bezpośredni zapewnia lepszą wydajność ze względu na mniejszą liczbę przeskoków sieci. Tryb połączenia bramy jest również zalecany w przypadku uruchamiania aplikacji w środowiskach z ograniczoną liczbą połączeń gniazd. 

     W przypadku korzystania z zestawu SDK w Azure Functions, szczególnie w [planie zużycia](../azure-functions/functions-scale.md#consumption-plan), należy mieć na uwadze bieżące [limity połączeń](../azure-functions/manage-connections.md). W takim przypadku można zalecać tryb bramy, jeśli pracujesz również z innymi klientami opartymi na protokole HTTP w aplikacji Azure Functions.

   * Tryb bezpośredni

     Tryb bezpośredni obsługuje łączność za pośrednictwem protokołu TCP i jest domyślnym trybem łączności, jeśli używany jest [pakiet Microsoft. Azure. Cosmos/.NET v3 SDK](sql-api-sdk-dotnet-standard.md).

     W przypadku korzystania z trybu bramy Cosmos DB korzysta z portu 443 i portów 10250, 10255 i 10256 przy użyciu interfejsu API Azure Cosmos DB dla MongoDB. Port 10250 jest mapowany na domyślne wystąpienie MongoDB bez replikacji geograficznej i 10255/10256 portów do wystąpienia MongoDB z funkcją replikacji geograficznej. W przypadku korzystania z protokołu TCP w trybie bezpośrednim oprócz portów bramy należy upewnić się, że zakres portów od 10000 do 20000 jest otwarty, ponieważ Azure Cosmos DB używa dynamicznych portów TCP. Jeśli te porty nie są otwarte i podjęto próbę użycia protokołu TCP, zostanie wyświetlony błąd niedostępności usługi 503. W poniższej tabeli przedstawiono tryby łączności dostępne dla różnych interfejsów API i użytkowników portów usługi dla każdego interfejsu API:

     |Tryb połączenia  |Obsługiwany protokół  |Obsługiwane zestawy SDK  |Port API/usługi  |
     |---------|---------|---------|---------|
     |Brama  |   HTTPS    |  Wszystkie zestawy SDK    |   SQL(443), Mongo(10250, 10255, 10256), Table(443), Cassandra(10350), Graph(443)    |
     |Direct    |     TCP    |  Zestaw SDK dla platformy .NET    | Porty w zakresie 10000-20000 |

     Azure Cosmos DB oferuje prosty i otwarty model programowania RESTful za pośrednictwem protokołu HTTPS. Ponadto oferuje wydajny protokół TCP, który jest również RESTful w swoim modelu komunikacji i jest dostępny za pośrednictwem zestawu SDK klienta platformy .NET. Protokół TCP używa protokołu SSL do uwierzytelniania początkowego i szyfrowania ruchu sieciowego. Aby uzyskać najlepszą wydajność, Użyj protokołu TCP, gdy jest to możliwe.

     W przypadku zestawu SDK v3 tryb łączności jest konfigurowany podczas tworzenia wystąpienia CosmosClient, jako część CosmosClientOptions, należy pamiętać, że tryb bezpośredni jest domyślnie.

     ```csharp
     var serviceEndpoint = new Uri("https://contoso.documents.net");
     var authKey = "your authKey from the Azure portal";
     CosmosClient client = new CosmosClient(serviceEndpoint, authKey,
     new CosmosClientOptions
     {
        ConnectionMode = ConnectionMode.Gateway // ConnectionMode.Direct is the default
     });
     ```

     W przypadku zestawu SDK Microsoft. Azure. DocumentDB tryb łączności jest konfigurowany podczas konstruowania wystąpienia DocumentClient z parametrem ConnectionPolicy. Jeśli jest używany tryb bezpośredni, protokół można także ustawić w parametrze ConnectionPolicy.

     ```csharp
     var serviceEndpoint = new Uri("https://contoso.documents.net");
     var authKey = "your authKey from the Azure portal";
     DocumentClient client = new DocumentClient(serviceEndpoint, authKey,
     new ConnectionPolicy
     {
        ConnectionMode = ConnectionMode.Direct, //ConnectionMode.Gateway is the default
        ConnectionProtocol = Protocol.Tcp
     });
     ```

     Ponieważ protokół TCP jest obsługiwany tylko w trybie bezpośrednim, w przypadku użycia trybu bramy protokół HTTPS jest zawsze używany do komunikacji z bramą, a wartość protokołu w ConnectionPolicy jest ignorowana.

     ![Ilustracja zasad połączenia Azure Cosmos DB](./media/performance-tips/connection-policy.png)

2. **Wywołaj OpenAsync, aby uniknąć opóźnień uruchamiania przy pierwszym żądaniu**

    Domyślnie pierwsze żądanie ma wyższy czas opóźnienia, ponieważ musi pobrać tabelę routingu adresów. W przypadku korzystania z [zestawu SDK V2](sql-api-sdk-dotnet.md)w celu uniknięcia opóźnień uruchamiania pierwszego żądania należy wywołać openAsync () raz podczas inicjowania w następujący sposób.

        await client.OpenAsync();

    > [!NOTE] 
    > Metoda OpenAsync będzie generować żądania, aby uzyskać tabelę routingu adresów dla wszystkich kontenerów na koncie. W przypadku kont, które mają wiele kontenerów, ale ich aplikacja uzyskuje dostęp do podzbioru z nich, wygeneruje niepotrzebny ruch, który spowalnia inicjalizację. Użycie metody OpenAsync może nie być przydatne w tym scenariuszu, ponieważ spowalnia uruchamianie aplikacji.

   <a id="same-region"></a>
3. **Kolokacja klientów w tym samym regionie świadczenia usługi Azure na potrzeby wydajności**

    Jeśli to możliwe, należy umieścić dowolne aplikacje wywołujące Azure Cosmos DB w tym samym regionie, w którym znajduje się baza danych usługi Azure Cosmos. Dla przybliżonego porównania, wywołania do Azure Cosmos DB w tym samym regionie, kompletne w ciągu 1-2 MS, ale opóźnienie między zachodnim i wschodnim wybrzeżem Stanów Zjednoczonych jest > 50 ms. To opóźnienie może się różnić od żądania żądania w zależności od trasy wykonywanej przez żądanie, gdy przechodzi od klienta do granicy centrum danych platformy Azure. Najniższe możliwe opóźnienie jest realizowane przez zagwarantowanie, że aplikacja wywołująca znajduje się w tym samym regionie platformy Azure, co punkt końcowy Azure Cosmos DB aprowizacji. Aby uzyskać listę dostępnych regionów, zobacz [regiony platformy Azure](https://azure.microsoft.com/regions/#services).

    ![ilustracja zasad połączenia Azure Cosmos DB](./media/performance-tips/same-region.png)
   <a id="increase-threads"></a>
4. **Zwiększ liczbę wątków/zadań**

    Ponieważ wywołania Azure Cosmos DB są realizowane za pośrednictwem sieci, może być konieczne zróżnicowanie stopnia równoległości żądań, aby aplikacja kliencka poświęca bardzo mało czasu na żądania. Na przykład jeśli korzystasz z programu. [Biblioteka zadań równoległych](https://msdn.microsoft.com//library/dd460717.aspx)w sieci, tworzenie w kolejności setek zadań odczytywania lub zapisywania do Azure Cosmos DB.

5. **Włącz przyspieszone sieci**

   Aby zmniejszyć opóźnienia i wahania procesora, zalecamy, aby maszyny wirtualne klienta były przyspieszone dla sieci. Aby włączyć przyspieszone sieci, zobacz artykuł [Tworzenie maszyny wirtualnej z systemem Windows przy użyciu przyspieszonej sieci](../virtual-network/create-vm-accelerated-networking-powershell.md) lub [Tworzenie maszyny wirtualnej z systemem Linux przy użyciu przyspieszonej](../virtual-network/create-vm-accelerated-networking-cli.md) sieci.


## <a name="sdk-usage"></a>Użycie zestawu SDK
1. **Zainstaluj najnowszy zestaw SDK**

    Zestawy SDK Azure Cosmos DB są stale ulepszane w celu zapewnienia najlepszej wydajności. Zobacz strony [zestawu sdk Azure Cosmos DB](sql-api-sdk-dotnet-standard.md) , aby określić najnowszy zestaw SDK i zapoznać się z ulepszeniami.

2. **Korzystanie z interfejsów API przesyłania strumieniowego**

    [Zestaw .NET SDK v3](sql-api-sdk-dotnet-standard.md) zawiera interfejsy API przesyłania strumieniowego, które mogą odbierać i zwracać dane bez serializacji. 

    Aplikacje warstwy środkowej, które nie zużywają odpowiedzi z zestawu SDK bezpośrednio, ale przekazują je do innych warstw aplikacji, mogą korzystać z interfejsów API usługi Stream. Zobacz przykłady [zarządzania elementami](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ItemManagement) na potrzeby obsługi strumienia.

3. **Używanie pojedynczego klienta Azure Cosmos DB w okresie istnienia aplikacji**

    Każde wystąpienie DocumentClient i CosmosClient jest bezpieczne wątkowo i wykonuje wydajne zarządzanie połączeniami oraz buforowanie adresów podczas pracy w trybie bezpośrednim. Aby umożliwić efektywne zarządzanie połączeniami i lepszą wydajność przez klienta zestawu SDK, zaleca się użycie jednego wystąpienia na domenę aplikacji przez okres istnienia tej usługi.

   <a id="max-connection"></a>
4. **Zwiększ System.Net MaxConnections na hosta podczas korzystania z trybu bramy**

    Żądania Azure Cosmos DB są wykonywane za pośrednictwem protokołu HTTPS/REST podczas korzystania z trybu bramy i podlegają domyślnemu limitowi połączeń na nazwę hosta lub adres IP. Może być konieczne ustawienie dla elementu MaxConnections wyższej wartości (100-1000), aby Biblioteka klienta mogła używać wielu jednoczesnych połączeń do Azure Cosmos DB. W 1.8.0 .NET SDK i nowszych wartość domyślna dla [ServicePointManager. DefaultConnectionLimit](https://msdn.microsoft.com/library/system.net.servicepointmanager.defaultconnectionlimit.aspx) to 50 i aby zmienić wartość, można ustawić [dokumenty. Client. ConnectionPolicy. MaxConnectionLimit](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.connectionpolicy.maxconnectionlimit.aspx) na wyższą wartość.   
5. **Dostrajanie równoległych zapytań dla kolekcji partycjonowanych**

     Zestaw SDK programu SQL .NET w wersji 1.9.0 lub nowszej obsługuje zapytania równoległe, które umożliwiają równoległe wykonywanie zapytań do kolekcji partycjonowanej. Aby uzyskać więcej informacji, zobacz [przykłady kodu](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/Queries/Program.cs) związane z pracą z zestawami SDK. Zapytania równoległe są przeznaczone do poprawiania opóźnienia zapytań i przepływności w porównaniu z ich odpowiednikami seryjnymi. Zapytania równoległe zawierają dwa parametry, które użytkownicy mogą dostosowywać do własnych wymagań, (a) MaxDegreeOfParallelism: aby kontrolować maksymalną liczbę partycji, a następnie można wykonywać zapytania równoległe i (b) MaxBufferedItemCount: w celu kontrolowania liczby wstępnie pobrane wyniki.

    (a) ***stopień strojenia równoległości\:*** równoległym działaniem zapytań równoległych do wielu partycji. Jednak dane z pojedynczej partycji są pobierane sekwencyjnie w odniesieniu do zapytania. Ustawienie `MaxDegreeOfParallelism` w [zestawie SDK V2](sql-api-sdk-dotnet.md) lub `MaxConcurrency` w [zestawie SDK v3](sql-api-sdk-dotnet-standard.md) na liczbę partycji ma największą szansę na osiągnięcie najbardziej wydajnego zapytania, pod warunkiem, że wszystkie inne warunki systemu pozostają takie same. Jeśli nie znasz liczby partycji, możesz ustawić stopień równoległości na wysoki, a system wybierze minimalną (liczbę partycji, dane wejściowe podane przez użytkownika) jako stopień równoległości.

    Należy pamiętać, że zapytania równoległe generują najlepsze korzyści, jeśli dane są równomiernie dystrybuowane we wszystkich partycjach w odniesieniu do zapytania. Jeśli partycjonowana kolekcja jest partycjonowana w taki sposób, że wszystkie lub większość danych zwróconych przez zapytanie jest skoncentrowana na kilku partycjach (jedna partycja w najgorszym przypadku), wydajność zapytania zostałaby przekazana przez te partycje.

    (b) ***dostrajania MaxBufferedItemCount\:*** Parallel Query została zaprojektowana w celu wstępnego pobrania, podczas gdy bieżąca partia wyników jest przetwarzana przez klienta. Wstępne pobieranie pomaga w ogólnym ulepszaniu opóźnienia zapytania. MaxBufferedItemCount jest parametrem, aby ograniczyć liczbę wstępnie pobranych wyników. Ustawienie MaxBufferedItemCount na oczekiwaną liczbę zwracanych wyników (lub wyższą liczbę) umożliwia zapytanie, aby otrzymać maksymalną korzyść z pobierania wstępnego.

    Wstępne pobieranie działa tak samo, niezależnie od stopnia równoległości i istnieje pojedynczy bufor dla danych ze wszystkich partycji.  
6. **Włącz funkcję GC po stronie serwera**

    Zmniejszenie częstotliwości wyrzucania elementów bezużytecznych może pomóc w niektórych przypadkach. W programie .NET Ustaw [gcServer](https://msdn.microsoft.com/library/ms229357.aspx) na wartość true.
7. **Implementowanie wycofywania w interwałach RetryAfter**

    Podczas testowania wydajności należy zwiększyć obciążenie, dopóki nie zostanie ograniczona niewielka liczba żądań. W przypadku ograniczenia przepustowości aplikacja kliencka powinna wycofywania przy użyciu ograniczenia przepustowości dla interwału ponawiania określonych przez serwer. Poszanowanie wycofywania gwarantuje, że spędzasz minimalny czas oczekiwania między ponownymi próbami. Obsługa zasad ponawiania jest dostępna w wersji 1.8.0 i nowszych od programów SQL [.NET](sql-api-sdk-dotnet.md) i [Java](sql-api-sdk-java.md), w wersji 1.9.0 i nowszych od [Node. js](sql-api-sdk-node.md) i [Python](sql-api-sdk-python.md)oraz wszystkich obsługiwanych wersji zestawów SDK [platformy .NET Core](sql-api-sdk-dotnet-core.md) . Aby uzyskać więcej informacji, [RetryAfter](https://msdn.microsoft.com/library/microsoft.azure.documents.documentclientexception.retryafter.aspx).
    
    W wersji 1,19 i nowszych zestawu .NET SDK istnieje mechanizm rejestrowania dodatkowych informacji diagnostycznych i rozwiązywania problemów z opóźnieniami, jak pokazano w poniższym przykładzie. Można rejestrować ciąg diagnostyczny dla żądań, które mają wyższy czas oczekiwania na odczyt. Przechwycony ciąg diagnostyczny pomoże Ci zrozumieć, ile razy zaobserwowano 429s dla danego żądania.
    ```csharp
    ResourceResponse<Document> readDocument = await this.readClient.ReadDocumentAsync(oldDocuments[i].SelfLink);
    readDocument.RequestDiagnosticsString 
    ```
    
8. **Skalowanie obciążenia klienta**

    Jeśli testujesz na poziomach o wysokiej przepływności (> 50 000 RU/s), aplikacja kliencka może stać się wąskim gardłem z powodu ograniczenia przepustowości procesora lub sieci. Jeśli docierasz do tego punktu, możesz kontynuować wypychanie konta Azure Cosmos DB przez skalowanie aplikacji klienckich na wiele serwerów.
9. **Identyfikatory URI dokumentów pamięci podręcznej dla niskiego opóźnienia odczytu**

    W miarę możliwości można buforować identyfikatory URI dokumentów w celu uzyskania najlepszej wydajności odczytu. Należy zdefiniować logikę do buforowania ResourceID podczas tworzenia zasobu. Wyszukiwania oparte na ResourceID są szybsze niż wyszukiwania oparte na nazwach, więc buforowanie tych wartości zwiększa wydajność. 

   <a id="tune-page-size"></a>
10. **Dostosuj rozmiar strony dla zapytań/Odczytaj źródła w celu uzyskania lepszej wydajności**

   Podczas wykonywania zbiorczej odczytu dokumentów przy użyciu funkcji odczytywania kanału informacyjnego (na przykład ReadDocumentFeedAsync) lub podczas wystawiania zapytania SQL wyniki są zwracane w postaci segmentacji, jeśli zestaw wyników jest zbyt duży. Domyślnie wyniki są zwracane w fragmentach 100 elementów lub 1 MB, w zależności od tego, który limit zostanie osiągnięty jako pierwszy.

   Aby zmniejszyć liczbę podróży w sieci wymaganych do pobrania wszystkich odpowiednich wyników, można zwiększyć rozmiar strony przy użyciu nagłówka żądania [x-MS-Max-Item-Count](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) do 1000. W przypadkach, gdy konieczne jest wyświetlenie tylko kilku wyników, na przykład jeśli interfejs użytkownika lub interfejsu API aplikacji zwraca tylko 10 wyników, można również zmniejszyć rozmiar strony do 10, aby zmniejszyć przepływność wykorzystywaną do odczytu i zapytań.

   > [!NOTE] 
   > Właściwość maxItemCount nie powinna być używana tylko do celów stronicowania. Jest to główne użycie, aby zwiększyć wydajność zapytań, zmniejszając maksymalną liczbę elementów zwracanych na jednej stronie.  

   Możesz również ustawić rozmiar strony przy użyciu dostępnych zestawów SDK Azure Cosmos DB. Właściwość [MaxItemCount](/dotnet/api/microsoft.azure.documents.client.feedoptions.maxitemcount?view=azure-dotnet) w FeedOptions umożliwia ustawienie maksymalnej liczby elementów, które mają być zwracane w operacji wyliczania. Gdy `maxItemCount` ma wartość-1, zestaw SDK automatycznie znajdzie najbardziej optymalną wartość w zależności od rozmiaru dokumentu. Przykład:
    
   ```csharp
    IQueryable<dynamic> authorResults = client.CreateDocumentQuery(documentCollection.SelfLink, "SELECT p.Author FROM Pages p WHERE p.Title = 'About Seattle'", new FeedOptions { MaxItemCount = 1000 });
   ```
    
   Po wykonaniu zapytania dane uzyskane są wysyłane w ramach pakietu TCP. Jeśli określisz zbyt niską wartość dla `maxItemCount`, liczba podróży wymagana do wysłania danych w ramach pakietu TCP jest wysoka, co ma wpływ na wydajność. Dlatego jeśli nie masz pewności, jaka wartość jest ustawiana dla właściwości `maxItemCount`, najlepiej ustawić ją na wartość-1 i pozwolić zestawowi SDK na wybranie wartości domyślnej. 

11. **Zwiększ liczbę wątków/zadań**

    Zobacz temat [zwiększanie liczby wątków/zadań](#increase-threads) w sekcji Sieć.

12. **Korzystanie z 64-bitowego przetwarzania hosta**

    Zestaw SQL SDK działa w procesie hosta 32-bitowego w przypadku korzystania z zestawu SQL .NET SDK w wersji 1.11.4 lub nowszej. Jeśli jednak używasz zapytań między partycjami, 64-bitowe przetwarzanie hosta jest zalecane w celu zwiększenia wydajności. Następujące typy aplikacji mają 32-bitowy proces hosta jako domyślny, więc aby zmienić go na 64-bitowy, wykonaj następujące kroki na podstawie typu aplikacji:

    - W przypadku aplikacji wykonywalnych można to zrobić, usuwając zaznaczenie opcji **preferuj 32-bitową** w oknie **właściwości projektu** na karcie **kompilacja** .

    - W przypadku projektów testowych opartych na VSTest można to zrobić, wybierając pozycję **testuj**->**Testuj ustawienia**->**domyślnej architektury procesora jako x64**z opcji menu **test programu Visual Studio** .

    - W przypadku lokalnie wdrożonych aplikacji sieci Web ASP.NET można to zrobić, sprawdzając, czy **używane są 64-bitowe wersje IIS Express dla witryn i projektów sieci Web**, w obszarze **narzędzia**->**opcje**->**projekty i rozwiązania**->**projekty sieci Web**.

    - W przypadku aplikacji sieci Web ASP.NET wdrożonych na platformie Azure można to zrobić, wybierając **platformę jako 64-bitową** w **ustawieniach aplikacji** na Azure Portal.

## <a name="indexing-policy"></a>Zasady indeksowania
 
1. **Wykluczanie nieużywanych ścieżek z indeksowania w celu przyspieszenia operacji zapisu**

    Zasady indeksowania Cosmos DB umożliwiają również określenie, które ścieżki dokumentów mają być dołączone lub wykluczone z indeksowania przy użyciu ścieżek indeksowania (IndexingPolicy. IncludedPaths i IndexingPolicy. ExcludedPaths). Użycie ścieżek indeksowania może oferować ulepszoną wydajność zapisu i niższy indeks magazynu dla scenariuszy, w których wzorce zapytania są znane wcześniej, ponieważ koszty indeksowania są bezpośrednio skorelowane z liczbą unikatowych ścieżek indeksowanych.  Na przykład poniższy kod pokazuje, jak wykluczyć całą sekcję dokumentów (poddrzewo) z indeksowania przy użyciu symbolu wieloznacznego "*".

    ```csharp
    var collection = new DocumentCollection { Id = "excludedPathCollection" };
    collection.IndexingPolicy.IncludedPaths.Add(new IncludedPath { Path = "/*" });
    collection.IndexingPolicy.ExcludedPaths.Add(new ExcludedPath { Path = "/nonIndexedContent/*");
    collection = await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), excluded);
    ```

    Aby uzyskać więcej informacji, zobacz [Azure Cosmos DB zasad indeksowania](index-policy.md).

## <a name="throughput"></a>Przepływność
<a id="measure-rus"></a>

1. **Mierzenie i dostrajanie dla niższych jednostek żądań/drugiego użycia**

    Azure Cosmos DB oferuje bogaty zestaw operacji bazy danych, w tym relacyjne i hierarchiczne zapytania z UDF, procedurami składowanymi i wyzwalaczami — wszystko to działa na dokumentach w ramach kolekcji baz danych. Koszt związany z każdą z tych operacji zależy od procesora, danych We/Wy i pamięci wymaganej do wykonania danej operacji. Zamiast rozważać zasoby sprzętowe i zarządzać nimi, można traktować jednostkę żądania (RU) jako pojedynczą miarę dla zasobów wymaganych do wykonywania różnych operacji bazy danych i obsługi żądania aplikacji.

    Obsługa przepływności zależy od liczby [jednostek żądania](request-units.md) ustawionych dla każdego kontenera. Użycie jednostki żądania jest oceniane jako stawka na sekundę. Aplikacje, które przekraczają zainicjowaną stawkę jednostkową żądania dla ich kontenera, są ograniczone do momentu spadku stawki poniżej poziomu aprowizacji kontenera. Jeśli aplikacja wymaga wyższego poziomu przepływności, można zwiększyć przepływność przez zainicjowanie obsługi dodatkowych jednostek żądania. 

    Złożoność zapytania wpływa na liczbę jednostek żądań używanych dla operacji. Liczba predykatów, charakter predykatów, liczba UDF i rozmiar zestawu danych źródłowych wpływają na koszt operacji zapytania.

    Aby zmierzyć obciążenie związane z jakąkolwiek operacją (tworzenie, aktualizowanie lub usuwanie), Sprawdź nagłówek [x-MS-Request-opłata](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-response-headers) (lub równoważną właściwość RequestCharge w ResourceResponse\<t > lub FeedResponse\<t > w zestawie .NET SDK), aby zmierzyć liczbę jednostek żądania używanych przez te operacje.

    ```csharp
    // Measure the performance (request units) of writes
    ResourceResponse<Document> response = await client.CreateDocumentAsync(collectionSelfLink, myDocument);
    Console.WriteLine("Insert of document consumed {0} request units", response.RequestCharge);
    // Measure the performance (request units) of queries
    IDocumentQuery<dynamic> queryable = client.CreateDocumentQuery(collectionSelfLink, queryString).AsDocumentQuery();
    while (queryable.HasMoreResults)
         {
              FeedResponse<dynamic> queryResponse = await queryable.ExecuteNextAsync<dynamic>();
              Console.WriteLine("Query batch consumed {0} request units", queryResponse.RequestCharge);
         }
    ```             

    Opłata za żądanie zwrócona w tym nagłówku jest częścią alokowanej przepływności (tj. 2000 jednostek ru/sekundę). Na przykład, jeśli poprzednie zapytanie zwraca 1000 rozmiarze 1 KB-Documents, koszt operacji wynosi 1000. W związku z tym w ciągu jednej sekundy serwer honoruje tylko dwa takie żądania przed szybkością ograniczania kolejnych żądań. Aby uzyskać więcej informacji, zobacz [jednostki żądań](request-units.md) i [Kalkulator jednostek żądania](https://www.documentdb.com/capacityplanner).
<a id="429"></a>
2. **Zbyt duży współczynnik obsługi — limit liczby żądań**

    Gdy klient próbuje przekroczyć zarezerwowaną przepływność dla konta, nie ma obniżenia wydajności na serwerze i nie będzie korzystać z wydajności przepływności poza poziomem zarezerwowanym. Serwer zapobiegawczo zakończyć żądanie z RequestRateTooLarge (kod stanu HTTP 429) i zwróci nagłówek [x-MS-retry-After-MS](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-response-headers) wskazujący ilość czasu (w milisekundach), przez który użytkownik musi czekać przed ponowną próbą wykonania żądania.

        HTTP Status 429,
        Status Line: RequestRateTooLarge
        x-ms-retry-after-ms :100

    Zestaw SDK niejawnie przechwytuje tę odpowiedź, przestrzegając określonego przez serwer nagłówka retry-After i ponów próbę wykonania żądania. O ile Twoje konto nie jest dostępne współbieżnie przez wielu klientów, kolejna próba powiodła się.

    Jeśli masz więcej niż jeden klient, który działa w sposób ciągły nad częstotliwością żądań, domyślna liczba ponownych prób aktualnie ustawiona na 9 wewnętrznie przez klienta może nie być wystarczająca; w takim przypadku klient zgłasza DocumentClientException z kodem stanu 429 do aplikacji. Domyślną liczbę ponownych prób można zmienić przez ustawienie RetryOptions w wystąpieniu ConnectionPolicy. Domyślnie DocumentClientException z kodem stanu 429 jest zwracany po skumulowanym czasie oczekiwania 30 sekund, jeśli żądanie będzie nadal działać powyżej stawki żądania. Dzieje się tak nawet wtedy, gdy bieżąca liczba ponownych prób jest mniejsza niż maksymalna liczba ponownych prób, być wartością domyślną 9 lub wartości zdefiniowanej przez użytkownika.

    Mimo że automatyczne zachowanie ponowienia próby pozwala zwiększyć odporność i użyteczność dla większości aplikacji, może się to zdarzyć szanse podczas wykonywania testów wydajnościowych, szczególnie podczas mierzenia opóźnień.  Opóźnienie obserwowane przez klienta zostanie wykonane, jeśli eksperyment trafi na ograniczenia serwera i spowoduje, że zestaw SDK klienta zostanie ponownie powtórzony. Aby uniknąć opóźnień opóźnienia podczas eksperymentów w wydajności, należy zmierzyć opłaty zwrócone przez poszczególne operacje i upewnić się, że żądania działają poniżej zarezerwowanej stawki żądania. Aby uzyskać więcej informacji, zobacz [jednostki żądania](request-units.md).
3. **Projektowanie dla mniejszych dokumentów w celu zwiększenia przepływności**

    Opłata za żądanie (tj. koszt przetwarzania żądania) danej operacji jest bezpośrednio skorelowana z rozmiarem dokumentu. Operacje na dużych dokumentach są droższe niż operacje w przypadku małych dokumentów.

## <a name="next-steps"></a>Następne kroki
Aby zapoznać się z przykładową aplikacją służącą do oceny Azure Cosmos DB w scenariuszach o wysokiej wydajności na kilku komputerach klienckich, zobacz [testowanie wydajności i skalowania przy użyciu Azure Cosmos DB](performance-testing.md).

Ponadto, aby dowiedzieć się więcej na temat projektowania aplikacji na potrzeby skalowania i wysokiej wydajności, zobacz [partycjonowanie i skalowanie w Azure Cosmos DB](partition-data.md).
