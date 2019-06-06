---
title: Usługa Azure porady dotyczące wydajności usługi Cosmos DB dla platformy .NET
description: Dowiedz się więcej opcji konfiguracji klienta w celu poprawy wydajności bazy danych Azure Cosmos DB
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: sngun
ms.openlocfilehash: c8907f1b1c8069a3a3e92d01a5fa6341c06ec952
ms.sourcegitcommit: 6932af4f4222786476fdf62e1e0bf09295d723a1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/05/2019
ms.locfileid: "66688806"
---
# <a name="performance-tips-for-azure-cosmos-db-and-net"></a>Porady dotyczące wydajności dla usługi Azure Cosmos DB i platformy .NET

> [!div class="op_single_selector"]
> * [Java (asynchroniczny)](performance-tips-async-java.md)
> * [Java](performance-tips-java.md)
> * [.NET](performance-tips.md)
> 

Usługa Azure Cosmos DB jest szybka i elastyczna rozproszonej bazy danych, która miało miejsce płynne skalowanie dzięki gwarancji opóźnień i przepływności. Nie trzeba wprowadzić zmiany architektury głównych lub pisania złożonego kodu do skalowania bazy danych za pomocą usługi Azure Cosmos DB. Skalowanie w górę i w dół jest łatwe jak tworzenie jednego wywołania interfejsu API. Aby dowiedzieć się więcej, zobacz [sposób aprowizowania przepływności kontenerów](how-to-provision-container-throughput.md) lub [sposób aprowizowania przepływności bazy danych](how-to-provision-database-throughput.md). Ponieważ usługi Azure Cosmos DB jest dostępna za pośrednictwem wywołań sieci istnieją optymalizacje po stronie klienta, które można wprowadzić do osiągnięcia maksymalnej wydajności, korzystając z [zestawu .NET SDK SQL](documentdb-sdk-dotnet.md).

Dlatego jeśli "jak mogę poprawić wydajność mojej bazy danych?" należy wziąć pod uwagę następujące opcje:

## <a name="networking"></a>Networking
<a id="direct-connection"></a>

1. **Zasady połączenia: Użyj trybu bezpośredniego połączenia**

    Jak klient nawiąże połączenie do usługi Azure Cosmos DB ma istotny wpływ na wydajność, szczególnie w zakresie odczytaną opóźnienie po stronie klienta. Istnieją dwa ustawienia konfiguracji klucza dla konfiguracji klienta zasad połączenia — połączenie *tryb* i połączenia *protokołu*.  Są dostępne dwa tryby:

   * Tryb bramy (ustawienie domyślne)
      
     Tryb bramy jest obsługiwane na wszystkich platformach zestawu SDK i jest domyślnie skonfigurowany. Jeśli aplikacja działa w sieci firmowej za pomocą ograniczeń zapory strict, tryb bramy jest najlepszym rozwiązaniem, ponieważ używa standardowego portu HTTPS i jeden punkt końcowy. Kosztem wydajności, jest tryb bramy obejmuje przeskok dodatkowe sieci, za każdym razem, gdy danych jest odczytywanych lub zapisywanych do usługi Azure Cosmos DB. W związku z tym w trybie bezpośrednim oferuje lepszą wydajność, ze względu na mniejszą liczbę przeskoków sieciowych. Tryb połączenia bramy zaleca się podczas uruchamiania aplikacji w środowiskach z ograniczoną liczbę takich połączeń gniazda, na przykład podczas korzystania z usługi Azure Functions lub jeśli korzystasz z planu zużycie. 

   * W trybie bezpośrednim

     Tryb Direct obsługuje łączność za pośrednictwem protokołów TCP i HTTPS. Jeśli używasz najnowszej wersji zestawu SDK platformy .NET, tryb połączenie bezpośrednie jest obsługiwany w .NET Standard 2.0 i .NET framework. Podczas korzystania z trybu bezpośrednich, istnieją dwie opcje protokołu:

     * TCP
     * HTTPS

     Podczas korzystania z trybu bramy usługi Cosmos DB używa portu 443 i portów 10250, 10255 i 10256, korzystając z interfejsu API usługi Azure Cosmos DB dla bazy danych MongoDB. Mapowania portów 10250 domyślnym wystąpieniem bazy danych MongoDB, bez replikacji geograficznej i mapowania portów 10255/10256 do wystąpienia bazy danych MongoDB przy użyciu funkcji replikacji geograficznej. Korzystając z protokołu TCP w trybie bezpośrednim oprócz portów do bramy, należy upewnić się port zakresie 10000 i 20000 jest otwarty, ponieważ korzysta z usługi Azure Cosmos DB dynamiczne porty TCP. Jeśli te porty są otwarte, i próbuj używać protokołu TCP, błąd 503 Usługa niedostępna. W poniższej tabeli przedstawiono dostępne tryby łączności dla różnych interfejsów API i użytkownik portów usługi dla każdego interfejsu API:

     |Tryb połączenia  |Obsługiwany protokół  |Obsługiwanych zestawów SDK  |Port usługi/interfejsu API  |
     |---------|---------|---------|---------|
     |Brama  |   HTTPS    |  Wszystkie zestawy SDK    |   SQL(443), Mongo(10250, 10255, 10256), Table(443), Cassandra(10350), Graph(443)    |
     |Direct    |    HTTPS     |  Zestaw SDK platformy .NET i Java    |   Porty w zakresie 20 000 10 000 operacji    |
     |Direct    |     TCP    |  Zestaw SDK .NET    | Porty w zakresie 20 000 10 000 operacji |

     Usługa Azure Cosmos DB oferuje proste i Otwórz model programowania RESTful przy użyciu protokołu HTTPS. Ponadto oferuje wydajne protokołu TCP, który jest również zgodne ze specyfikacją REST swój model komunikacji i jest dostępny za pośrednictwem zestawu SDK klienta platformy .NET. Zarówno w przypadku bezpośredniego połączenia TCP, jak i protokołu HTTPS na użytek SSL początkowego uwierzytelniania i szyfrowania ruchu. Aby uzyskać najlepszą wydajność należy użyć protokołu TCP, gdy jest to możliwe.

     Podczas tworzenia wystąpienia DocumentClient z parametr ConnectionPolicy jest konfigurowana tryb łączności. Jeśli jest używany w trybie bezpośrednim, protokół można również ustawić w ramach parametr ConnectionPolicy.

     ```csharp
     var serviceEndpoint = new Uri("https://contoso.documents.net");
     var authKey = "your authKey from the Azure portal";
     DocumentClient client = new DocumentClient(serviceEndpoint, authKey,
     new ConnectionPolicy
     {
        ConnectionMode = ConnectionMode.Direct,
        ConnectionProtocol = Protocol.Tcp
     });
     ```

     Ponieważ protokół TCP jest obsługiwany tylko w trybie bezpośredniego, jeśli używany jest tryb bramy protokołu HTTPS zawsze jest używany do komunikacji z bramą i wartość protokołu w ConnectionPolicy jest ignorowana.

     ![Ilustracja zasad połączenia usługi Azure Cosmos DB](./media/performance-tips/connection-policy.png)

2. **Wywołaj OpenAsync, aby uniknąć opóźnienia uruchomienia na pierwsze żądanie**

    Domyślnie pierwsze żądanie ma wyższe opóźnienie, ponieważ ma ona można pobrać tabeli routingu adresów. Aby uniknąć tego opóźnienia uruchomienia na pierwsze żądanie, należy wywołać OpenAsync() raz podczas inicjowania w następujący sposób.

        await client.OpenAsync();
   <a id="same-region"></a>
3. **W ten sposób rozmieszczać klientów, w tym samym regionie platformy Azure dla wydajności**

    Jeśli to możliwe, należy umieścić wszystkie aplikacje podczas wywoływania usługi Azure Cosmos DB w tym samym regionie co baza danych usługi Azure Cosmos DB. Przybliżony porównanie wywołań do usługi Azure Cosmos DB w ramach tego samego regionu zakończyć w ciągu 1 – 2 ms, ale opóźnienie między zachodnim i wschodnim wybrzeżem Stanów Zjednoczonych wynosi > 50 ms. Ten czas oczekiwania prawdopodobnie zależy do innego żądania trasy żądania przekazywanego z klienta do granicy centrum danych platformy Azure. Najniższe możliwe opóźnienie jest osiągane poprzez zapewnienie, że aplikacja wywołująca znajduje się w obrębie tego samego regionu platformy Azure aprowizowane punktu końcowego usługi Azure Cosmos DB. Aby uzyskać listę dostępnych regionów, zobacz [regionów platformy Azure](https://azure.microsoft.com/regions/#services).

    ![Ilustracja zasad połączenia usługi Azure Cosmos DB](./media/performance-tips/same-region.png)
   <a id="increase-threads"></a>
4. **Zwiększ liczbę wątków/zadań**

    Ponieważ wywołania do usługi Azure Cosmos DB są wykonywane za pośrednictwem sieci, może być konieczne różnią się stopień równoległości żądań, tak, aby aplikacja kliencka zużywa bardzo mało czasu oczekiwania między żądaniami. Na przykład, jeśli używasz. NET firmy [Biblioteka zadań równoległych](https://msdn.microsoft.com//library/dd460717.aspx), Utwórz zgodnie z kolejnością 100s zadania Odczyt lub zapis do usługi Azure Cosmos DB.

5. **Włącz przyspieszona sieć**

   W celu zmniejszenia opóźnienia i zakłócenia procesora CPU, zaleca się, że maszyny wirtualne klienta są funkcji przyspieszonej łączności sieciowej włączone. Zobacz [Utwórz maszynę wirtualną Windows dzięki przyspieszonej sieci](../virtual-network/create-vm-accelerated-networking-powershell.md) lub [Utwórz maszynę wirtualną systemu Linux przy użyciu Accelerated Networking](../virtual-network/create-vm-accelerated-networking-cli.md) artykuły, aby włączyć przyspieszonej łączności sieciowej.


## <a name="sdk-usage"></a>Użycie zestawu SDK
1. **Instalowanie najnowszych zestawu SDK**

    Zestawów SDK usługi Azure Cosmos DB są ciągle ulepszane w celu zapewnienia najlepszej wydajności. Zobacz [zestawu SDK usługi Azure Cosmos DB](documentdb-sdk-dotnet.md) strony do określenia najnowszej zestawu SDK i przejrzyj ulepszenia.
2. **Okres istnienia aplikacji za pomocą pojedynczego wystąpienia klienta usługi Azure Cosmos DB**

    Każde wystąpienie DocumentClient jest bezpieczna dla wątków i wykonuje połączenie efektywne zarządzanie i buforowanie adresów, podczas pracy w trybie bezpośredniego. Aby zezwolić na połączenia skutecznego zarządzania i zwiększając wydajność przez DocumentClient, zaleca się używać jednego wystąpienia DocumentClient dla domeny AppDomain dla cyklu życia aplikacji.

   <a id="max-connection"></a>
3. **Zwiększenie przestrzeni nazw System.Net MaxConnections na każdym hoście podczas korzystania z trybu bramy**

    Usługa Azure Cosmos DB żądania są wykonywane za pośrednictwem protokołu HTTPS REST podczas korzystania z trybu bramy i są poddawane domyślny limit połączeń dla hosta lub adres IP. Konieczne może być równa MaxConnections wyższa wartość (100-1000) tak, aby biblioteki klienta mogą wykorzystywać wiele jednoczesnych połączeń z usługą Azure Cosmos DB. W zestawie .NET SDK 1.8.0 lub nowszego wartością domyślną dla [ServicePointManager.DefaultConnectionLimit](https://msdn.microsoft.com/library/system.net.servicepointmanager.defaultconnectionlimit.aspx) to 50, a aby zmienić wartość, możesz ustawić [Documents.Client.ConnectionPolicy.MaxConnectionLimit](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.connectionpolicy.maxconnectionlimit.aspx)wyższa wartość.   
4. **Dostosowywanie zapytania równolegle do kolekcji podzielonych na partycje**

     Zestaw SDK SQL platformy .NET w wersji 1.9.0 i nowszych zapytania równoległe pomocy technicznej, które umożliwiają kwerenda dotycząca kolekcji partycjonowanej równolegle. Aby uzyskać więcej informacji, zobacz [przykłady kodu](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/Queries/Program.cs) związanych z pracą z zestawami SDK. Zapytania równoległe są przeznaczone do kwerendy opóźnienia i przepływności starają się ich odpowiedników szeregowe. Zapytania równoległe Podaj dwa parametry, których użytkowników można dostrajanie aby dopasowany do ich wymagań, () MaxDegreeOfParallelism: do kontroli maksymalną liczbę partycji następnie mogą być wyszukiwane in parallel oraz (b) MaxBufferedItemCount: można określić liczbę wcześniej pobrano wyniki.

    () ***dostrajania MaxDegreeOfParallelism\:***  równoległe zapytanie działa, badając równocześnie wiele partycji. Jednak z poszczególnych zbieranie podzielonym na partycje jest pobierane dane szeregowo w odniesieniu do zapytania. Tak ustawienie MaxDegreeOfParallelism liczbę partycji przewiduje maksymalną prawdopodobieństwo osiągnięcia większość zapytań wydajna, inne warunki systemu pozostają takie same. Jeśli nie znasz liczbę partycji, możesz ustawić MaxDegreeOfParallelism dużą liczbą, a system wybiera co najmniej (liczba partycji, dane wejściowe podane przez użytkownika) jako MaxDegreeOfParallelism.

    Należy pamiętać, zapytania równoległe generuje najważniejsze korzyści, jeśli danych jest równomiernie rozłożona na wszystkie partycje w odniesieniu do zapytania. Jeśli kolekcji partycjonowanej jest podzielona na partycje tak, wszystkie lub większość danych zwróconych przez kwerendę koncentrują się w kilku partycji (jedną partycję w najgorszym przypadku), a następnie wykonywania zapytania, czy bottlenecked przez te partycje.

    (b) ***dostrajania MaxBufferedItemCount\:***  zapytanie równoległe zaprojektowano w celu wstępnego pobierania wyników, podczas gdy bieżącej partii wyników jest przetwarzany przez klienta. Pobierania pomaga w ogólnej poprawy opóźnienie kwerendy. MaxBufferedItemCount jest to parametr, aby ograniczyć liczbę wstępnie pobrano wyniki. Ustawienie MaxBufferedItemCount do oczekiwanej liczby wyników zwróconych (lub większa liczba zostanie podana) umożliwia zapytania do odbierania maksymalnych korzyści z pobierania z wyprzedzeniem.

    Pobierania z wyprzedzeniem działa tak samo niezależnie od tego, MaxDegreeOfParallelism i istnieje pojedynczy bufor dla danych ze wszystkich partycji.  
5. **Włącz GC po stronie serwera**

    W niektórych przypadkach może pomóc zmniejszyć częstotliwość uruchamiania operacji wyrzucania elementów bezużytecznych. Na platformie .NET, należy ustawić [gcserver —](https://msdn.microsoft.com/library/ms229357.aspx) na wartość true.
6. **Implementowanie wycofywania w odstępach czasu RetryAfter**

    Podczas testowania wydajności, należy zwiększyć obciążenie do momentu ograniczeni mała liczba żądań. Jeśli jest dławiona, aplikacja kliencka powinna wycofywania na ograniczenie przepustowości przez dany interwał ponawiania określony serwer. Uwzględnienie wycofywania daje pewność, możesz wydać skraca czas oczekiwania między ponownymi próbami. Obsługa zasad ponawiania i jest dołączone w wersji 1.8.0 powyżej SQL [.NET](sql-api-sdk-dotnet.md) i [Java](sql-api-sdk-java.md), wersji 1.9.0 i nowsze wersje z [Node.js](sql-api-sdk-node.md) i [językaPython](sql-api-sdk-python.md), a wszystkie obsługiwane wersje [platformy .NET Core](sql-api-sdk-dotnet-core.md) zestawów SDK. Aby uzyskać więcej informacji [RetryAfter](https://msdn.microsoft.com/library/microsoft.azure.documents.documentclientexception.retryafter.aspx).
    
    W wersji 1,19 i nowszych zestawu .NET SDK istnieje mechanizm rejestrować dodatkowych informacji diagnostycznych i rozwiązywanie problemów z opóźnieniem, jak pokazano w następującym przykładzie. Umożliwia rejestrowanie diagnostyczne ciągów dla żądania, które mają wyższe opóźnienie odczytu. Przechwycone ciągu diagnostycznych pomoże poznać liczbę razy zaobserwowane 429s dla danego żądania.
    ```csharp
    ResourceResponse<Document> readDocument = await this.readClient.ReadDocumentAsync(oldDocuments[i].SelfLink);
    readDocument.RequestDiagnosticsString 
    ```
    
7. **Skalowanie w poziomie obciążenia klientami**

    Jeśli testujesz na poziomach wysoką przepływność (> 50 000 jednostek RU/s), aplikacja kliencka może stać się wąskim gardłem ze względu na maszynie są takie same się na użycie procesora CPU lub sieci. Jeśli przekroczysz ten punkt, może nadal wypychania dalsze za pomocą konta usługi Azure Cosmos DB skalowanie aplikacji klienckich na wielu serwerach.
8. **Mniejsze opóźnienie odczytu dokumentu identyfikatory URI w pamięci podręcznej**

    Identyfikatory URI w miarę możliwości, aby uzyskać najlepszą wydajność odczytu dokumentu w pamięci podręcznej. Należy zdefiniować logikę do pamięci podręcznej resourceid podczas tworzenia zasobu. Wyszukiwanie na podstawie RESOURCEID są szybsze niż wyszukiwanie na podstawie nazwy, więc buforowanie tych wartości poprawia wydajność. 

   <a id="tune-page-size"></a>
1. **Dostosuj rozmiar strony dla źródeł danych zapytania/odczytu w celu zapewnienia lepszej wydajności**

   Podczas wykonywania masowego odczytywania dokumentów za pomocą odczytu zestawienia (na przykład ReadDocumentFeedAsync) lub, wydając zapytania SQL, wyniki są zwracane w sposób segmentu, jeśli zestaw wyników jest za duży. Domyślnie wyniki są zwracane we fragmentach po 100 elementów lub 1 MB, jednego z tych limitów zostanie osiągnięty jako pierwszy.

   Aby zmniejszyć liczbę rund sieci, wymagany do pobrania wszystkich odpowiednich wyników, można zwiększyć, używając rozmiaru strony [x-ms-max-item-count](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) nagłówek żądania do 1000. W przypadkach, w których należy wyświetlić tylko kilka wyników, na przykład, jeśli użytkownik interfejsu lub aplikacji interfejsu API zwróci tylko 10 powoduje przez czas, można także zmniejszyć rozmiar strony do 10, aby ograniczyć przepustowość używana dla odczytów i zapytań.

   > [!NOTE] 
   > Nie można użyć właściwości maxItemCount tylko w celu dzielenia na strony. Jest głównym użycia go, aby poprawić wydajność zapytań, zmniejszając maksymalną liczbę elementów zwracanych w jednej strony.  

   Można również ustawić rozmiar strony, przy użyciu dostępnych zestawów SDK usługi Azure Cosmos DB. [MaxItemCount](/dotnet/api/microsoft.azure.documents.client.feedoptions.maxitemcount?view=azure-dotnet) właściwość FeedOptions pozwala ustawić maksymalną liczbę elementów, które mają być zwracane w operacji enmuration. Gdy `maxItemCount` jest ustawiona na wartość -1, zestaw SDK automatycznie znajduje najbardziej optymalną wartość w zależności od rozmiaru dokumentu. Na przykład:
    
   ```csharp
    IQueryable<dynamic> authorResults = client.CreateDocumentQuery(documentCollection.SelfLink, "SELECT p.Author FROM Pages p WHERE p.Title = 'About Seattle'", new FeedOptions { MaxItemCount = 1000 });
   ```
    
   Po wykonaniu zapytania wynikowe dane są wysyłane w pakiecie protokołu TCP. Jeśli określono zbyt niską wartość `maxItemCount`, liczba wymaganą do przesyłania danych w ramach pakietu TCP są wysokie, która wpływa na wydajność. Jeśli nie masz pewności, jaka wartość do ustawienia dla `maxItemCount` właściwości, najlepiej jest równa -1 i umożliwić zestawowi SDK, wybierz wartość domyślną. 

10. **Zwiększ liczbę wątków/zadań**

    Zobacz [zwiększyć liczbę wątków/zadań](#increase-threads) w sekcji sieci.

11. **Użyj hosta 64-bitowego przetwarzania**

    Zestaw SDK SQL działa w procesie 32-bitowego hosta, korzystając z zestawu SDK .NET SQL w wersji 1.11.4 lub nowszym. Jeśli używasz wielu partycji zapytań hosta 64-bitowego przetwarzania jest zalecane w celu zwiększenia wydajności. Następujące typy aplikacji mają proces 32-bitowego hosta jako domyślne, tak aby można było zmienić tę instrukcję 64-bitowy, wykonaj następujące kroki na podstawie typu aplikacji:

    - W przypadku pliku wykonywalnego aplikacji, można to zrobić, usuwając zaznaczenie pola **Preferuj 32-bitowe** opcji **właściwości projektu** okna na **kompilacji** kartę.

    - VSTest podstawie projektów testów, można to zrobić, wybierając **Test**->**ustawienia testu**->**domyślna architektura procesora jako X64**, z **Test programu Visual Studio** opcji menu.

    - Dla lokalnie wdrożonych aplikacji sieci Web platformy ASP.NET, można to zrobić, sprawdzając **użyć 64-bitowej wersji usług IIS Express dla projektów i witryn sieci web**w obszarze **narzędzia**->**opcje**  -> **Projekty i rozwiązania**->**projektów sieci Web**.

    - Dla aplikacji ASP.NET sieci Web jest wdrażane na platformie Azure, można to zrobić, wybierając **platformy jako 64-bitowych** w **ustawienia aplikacji** w witrynie Azure portal.

## <a name="indexing-policy"></a>Zasady indeksowania
 
1. **Wyklucz ścieżki nieużywane indeksowania do szybszego zapisu**

    Zasady indeksowania usługi cosmos DB umożliwia również określić, które ścieżki dokumentu do dołączania lub wykluczania z indeksowania dzięki wykorzystaniu indeksowania ścieżki (IndexingPolicy.IncludedPaths i IndexingPolicy.ExcludedPaths). Użycie indeksowania ścieżki oferują zapisu ulepszoną wydajność i niższe magazyn indeksów dla scenariuszy, w których wzorców zapytań są znane wcześniej, ponieważ koszty indeksowania bezpośrednio skorelowanych liczby unikatowych ścieżek indeksowane.  Na przykład, poniższy kod przedstawia sposób wykluczania całą sekcję dokumentów (poddrzewo) z pomocą indeksowaniem "*" symboli wieloznacznych.

    ```csharp
    var collection = new DocumentCollection { Id = "excludedPathCollection" };
    collection.IndexingPolicy.IncludedPaths.Add(new IncludedPath { Path = "/*" });
    collection.IndexingPolicy.ExcludedPaths.Add(new ExcludedPath { Path = "/nonIndexedContent/*");
    collection = await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), excluded);
    ```

    Aby uzyskać więcej informacji, zobacz [zasady indeksowania usługi Azure Cosmos DB](index-policy.md).

## <a name="throughput"></a>Przepływność
<a id="measure-rus"></a>

1. **Pomiar i dostrajania dla żądania niższe jednostek na sekundę użycia**

    Usługa Azure Cosmos DB oferuje bogaty zestaw operacji bazy danych, takich jak relacyjne i hierarchiczne zapytania z funkcji UDF, procedury składowane i wyzwalacze. wszystkie na dokumentach w obrębie kolekcji bazy danych. Koszt związany z każdą z tych operacji zależy od procesora CPU, we/wy i pamięci wymaganej do ukończenia tej operacji. Zamiast myśleć o zasobach i zarządzaniu nimi sprzętu można traktować jednostek żądań (RU) jako pojedynczej mierze zasobów wymaganych do wykonywania różnych operacji bazy danych i obsługiwania żądania aplikacji.

    Przepływność jest przygotowany na podstawie liczby [jednostek żądania](request-units.md) ustawić dla każdego kontenera. Użycie jednostek żądania zostanie ocenione jako wskaźnik na sekundę. Aplikacje, które wykraczają stawki za jednostkę elastycznie żądania dla ich kontenera są ograniczone, aż współczynnik nie spadnie poniżej poziomu aprowizowaną dla kontenera. Jeśli aplikacja wymaga wyższego poziomu przepływności, można zwiększyć przepływność przez udostępnienie dodatkowych jednostek żądań. 

    Złożoność zapytania ma wpływ na liczbę jednostek żądania są używane dla operacji. Liczba predykatów, rodzaj predykaty, liczba funkcji zdefiniowanych przez użytkownika oraz rozmiar źródłowy zestaw danych, wszystkie mają wpływ na koszt operacje zapytań.

    Do mierzenia obciążenie związane z każdej operacji (Tworzenie, aktualizowanie lub usuwanie), sprawdź [x-ms żądanie — opłata](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-response-headers) nagłówka (lub równoważne właściwość RequestCharge ResourceResponse<T> lub FeedResponse<T> w. Zestaw SDK netto) do mierzenia liczby jednostek żądania, używanych przez te operacje.

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

    Opłata za żądanie wyrażana zwrócony w nagłówku to jest część aprowizowanej przepływności (czyli 2000 jednostek żądania / s). Na przykład jeśli poprzednie zapytanie zwraca 1KB 1000 dokumentów, koszty działania to 1000. Jako takie w ciągu sekundy, serwer honoruje tylko dwa takich żądań przed kolejnymi żądaniami ograniczania szybkości. Aby uzyskać więcej informacji, zobacz [jednostek żądania](request-units.md) i [kalkulatora jednostek żądania](https://www.documentdb.com/capacityplanner).
<a id="429"></a>
2. **Uchwyt współczynnik ograniczanie żądań zakończonych zbyt duży**

    Gdy klient próbuje przekracza zarezerwowaną przepływnością dla konta, istnieje bez spadku wydajności na serwerze i zakaz używania przepływność poza poziomem zastrzeżone. Serwer będzie prewencyjnego kończy żądanie z RequestRateTooLarge (kod stanu HTTP 429) i zwracają [x-ms ponawiania — po ms](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-response-headers) nagłówek wskazujący ilość czasu w milisekundach, które użytkownik musi czekać przed ponowną próbą wykonania żądanie.

        HTTP Status 429,
        Status Line: RequestRateTooLarge
        x-ms-retry-after-ms :100

    Zestawy SDK wszystkie niejawnie catch tej odpowiedzi, przestrzegać określony serwer nagłówka retry-after i ponowić próbę żądania. Chyba, że Twoje konto jest uzyskiwany współbieżnie przez wielu klientów, następne ponowienie próby zakończy się pomyślnie.

    Jeśli masz więcej niż jeden klient łącznie operacyjnego stale powyżej żądań zakończonych, domyślna liczba ponownych prób aktualnie ustawiona na 9 wewnętrznie przez klienta mogą być niewystarczające; w takim przypadku klient zgłasza DocumentClientException z kodem stanu 429 do aplikacji. Domyślna liczba ponownych prób, można zmienić przez ustawienie RetryOptions wystąpieniu ConnectionPolicy. Domyślnie DocumentClientException z kodem stanu 429 po skumulowany czas oczekiwania równej 30 sekund jest zwracany, jeśli żądanie będzie kontynuował pracę nad liczba żądań. Dzieje się tak nawet gdy aktualna liczba ponownych prób jest mniejsza niż liczba ponowień max, są to domyślne 9 lub wartości zdefiniowane przez użytkownika.

    Podczas sposób automatycznego ponawiania pomaga zwiększyć odporność i użyteczność dla większości aplikacji, jego mogą pochodzić w odds podczas wykonywania testów porównawczych wydajności, szczególnie w przypadku, gdy pomiaru opóźnienia.  Opóźnienie zaobserwowane klienta będzie tymczasowe, jeśli eksperymentu trafienia ograniczania serwera i powoduje, że klient zestawu SDK, aby dyskretnie ponowić próbę. Aby uniknąć opóźnienia rzędu podczas wydajności eksperymentów, zmierzyć opłaty zwrócony przez każdej operacji i upewnij się, czy żądania działają poniżej stawki żądanie zastrzeżonego. Aby uzyskać więcej informacji, zobacz [jednostek żądania](request-units.md).
3. **Projektowanie pod kątem mniejszych dokumentów w celu uzyskania wyższej przepustowości**

    Opłata za żądanie wyrażana (tj. koszt przetwarzania żądania) dla danej operacji są bezpośrednio skorelowane do rozmiaru dokumentu. Operacje na dużych dokumentów kosztuje więcej niż operacji przy małym dokumentom.

## <a name="next-steps"></a>Kolejne kroki
Dla przykładowej aplikacji używane do oceny usługi Azure Cosmos DB dla scenariuszy o wysokiej wydajności na kilku maszynach klienckich, zobacz [wydajność i skalę, testowanie za pomocą usługi Azure Cosmos DB](performance-testing.md).

Aby dowiedzieć się więcej o projektowaniu aplikacji w celu zapewnienia skalowalności i wysokiej wydajności, zobacz też [partycjonowanie i skalowanie w usłudze Azure Cosmos DB](partition-data.md).
