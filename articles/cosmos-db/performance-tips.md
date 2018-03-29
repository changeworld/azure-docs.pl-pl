---
title: Azure porady dotyczące wydajności rozwiązania Cosmos DB dla programu .NET | Dokumentacja firmy Microsoft
description: Dowiedz się więcej opcji konfiguracji klienta w celu poprawy wydajności bazy danych Azure DB rozwiązania Cosmos
keywords: jak poprawić wydajność bazy danych
services: cosmos-db
author: mimig1
manager: jhubbard
editor: ''
documentationcenter: ''
ms.assetid: 94ff155e-f9bc-488f-8c7a-5e7037091bb9
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2018
ms.author: mimig
ms.openlocfilehash: 6b8ff8d2efd2039e7b71f4e8f25b2756d324940f
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2018
---
> [!div class="op_single_selector"]
> * [Java](performance-tips-java.md)
> * [.NET](performance-tips.md)
> 
> 

# <a name="performance-tips-for-azure-cosmos-db-and-net"></a>Porady dotyczące wydajności bazy danych Azure rozwiązania Cosmos i .NET

Azure DB rozwiązania Cosmos jest szybkie i elastyczne rozproszoną bazę danych, która skaluje bezproblemowo gwarantowane opóźnienia i przepływności. Nie trzeba zmieniać architektura głównych ani pisania złożonego kodu można skalować bazy danych z bazy danych Azure rozwiązania Cosmos. Skalowanie w górę i w dół sprowadza się do jednego wywołania interfejsu API lub [zestawu SDK wywołania metody](set-throughput.md#set-throughput-sdk). Jednak ponieważ bazy danych Azure rozwiązania Cosmos jest dostępny za pośrednictwem połączeń sieci istnieją optymalizacje po stronie klienta, możesz wprowadzić, aby osiągnąć najwyższą wydajność przy użyciu [zestawu .NET SDK SQL](documentdb-sdk-dotnet.md).

Dlatego jeśli "jak poprawić wydajność mojej bazy danych?" należy wziąć pod uwagę następujące opcje:

## <a name="networking"></a>Networking
<a id="direct-connection"></a>

1. **Zasady połączeń: Użyj trybu bezpośredniego połączenia**

    Jak klient nawiąże połączenie bazy danych Azure rozwiązania Cosmos ma istotny wpływ na wydajność, szczególnie pod względem obserwowanych opóźnienia po stronie klienta. Dostępne są dwa ustawienia konfiguracji klucza dla konfiguracji klienta zasady połączenia — połączenie *tryb* i [połączenia *protokołu*](#connection-protocol).  Są dwa tryby dostępne:

   1. Tryb bramy (ustawienie domyślne)
   2. W trybie bezpośrednim

      Tryb bramy jest obsługiwana na wszystkich platformach zestawu SDK i jest skonfigurowana domyślna.  Jeśli aplikacja działa w sieci firmowej z ograniczeń zapory strict, najlepszym rozwiązaniem jest w trybie bramy ponieważ używa standardowego portu HTTPS i jeden punkt końcowy. Jednak z zależnościami wydajności jest tryb bramy obejmuje przeskoku dodatkowe sieci, za każdym razem, gdy danych jest odczytywanych lub zapisywanych do bazy danych Azure rozwiązania Cosmos. W związku z tym w trybie bezpośrednim oferuje lepszą wydajność ze względu na mniejszą liczbę przeskoków sieciowych.
<a id="use-tcp"></a>
2. **Zasady połączeń: używanie protokołu TCP**

    Podczas korzystania z trybu bezpośredniego, istnieją dwie opcje protokołu:

   * TCP
   * HTTPS

     Azure DB rozwiązania Cosmos oferuje proste i Otwórz model programowania RESTful za pośrednictwem protokołu HTTPS. Ponadto zapewnia ona wydajne protokołu TCP, który jest również RESTful jego model komunikacji i jest dostępna za pośrednictwem klienta .NET SDK. Bezpośrednie TCP i HTTPS używają protokołu SSL dla początkowego uwierzytelniania i szyfrowania ruchu. Aby uzyskać najlepszą wydajność należy użyć protokołu TCP, gdy jest to możliwe.

     Korzystając z protokołu TCP w trybie bramy, TCP Port 443 jest port bazy danych Azure rozwiązania Cosmos i 10255 jest port interfejsu API bazy danych MongoDB. Korzystając z protokołu TCP w trybie bezpośredniego, oprócz portów bramy, należy się upewnić port zakresie 10000 i 20000 jest otwarty, ponieważ korzysta z bazy danych Azure rozwiązania Cosmos dynamiczne porty TCP. Jeśli te porty nie są otwarte, a próba użycia protokołu TCP, komunikat o błędzie 503 Usługa niedostępna.

     Tryb łączności skonfigurowano podczas konstruowania obiektu DocumentClient z parametrem ConnectionPolicy. Jeśli używany jest tryb Direct, protokół można również ustawić w parametrze ConnectionPolicy.

    ```csharp
    var serviceEndpoint = new Uri("https://contoso.documents.net");
    var authKey = new "your authKey from the Azure portal";
    DocumentClient client = new DocumentClient(serviceEndpoint, authKey,
    new ConnectionPolicy
    {
        ConnectionMode = ConnectionMode.Direct,
        ConnectionProtocol = Protocol.Tcp
    });
    ```

    Ponieważ TCP jest obsługiwany tylko w trybie bezpośredniego, jeśli używany jest tryb bramy protokołu HTTPS zawsze jest używany do komunikacji z bramą i wartość protokołu w ConnectionPolicy jest ignorowana.

    ![Ilustracja zasady połączenia bazy danych Azure rozwiązania Cosmos](./media/performance-tips/connection-policy.png)

3. **Wywołanie OpenAsync, aby uniknąć opóźnienia uruchomienia na pierwsze żądanie**

    Domyślnie to pierwsze żądanie ma większego opóźnienia, ponieważ ma ona można pobrać tabeli routingu adresów. Aby uniknąć tego opóźnienia uruchomienia na pierwsze żądanie, należy wywołać OpenAsync() raz podczas inicjowania w następujący sposób.

        await client.OpenAsync();
   <a id="same-region"></a>
4. **Klienci, w tym samym regionie Azure wydajności w ten sposób rozmieszczać**

    Jeśli to możliwe, należy umieścić wszystkie aplikacje wywoływanie bazy danych Azure rozwiązania Cosmos w tym samym regionie co baza danych bazy danych Azure rozwiązania Cosmos. Przybliżony porównanie wywołań do bazy danych Azure rozwiązania Cosmos w ramach tego samego regionu ukończona w ciągu 1 i 2 ms, ale opóźnienie między zachód i wschodniego USA > 50 ms. Tego opóźnienia prawdopodobnie zależy od trasę przez żądanie przesyłanych z klienta do granicy centrum danych Azure do innego żądania. Najniższym opóźnieniu możliwe jest to osiągane przez zapewnienie, że aplikacja wywołująca znajduje się w tym samym regionie Azure jako punkt końcowy elastycznie bazy danych Azure rozwiązania Cosmos. Aby uzyskać listę dostępnych regionów, zobacz [regiony platformy Azure](https://azure.microsoft.com/regions/#services).

    ![Ilustracja zasady połączenia bazy danych Azure rozwiązania Cosmos](./media/performance-tips/same-region.png)
   <a id="increase-threads"></a>
5. **Zwiększenie liczby wątków/zadań**

    Ponieważ wywołania do bazy danych Azure rozwiązania Cosmos są wykonywane za pośrednictwem sieci, może być konieczne różnią się stopień równoległości żądania, dzięki czemu aplikacja kliencka zużywa bardzo mało czasu oczekiwania między żądaniami. Na przykład, jeśli używasz. W sieci [Biblioteka zadań równoległych](https://msdn.microsoft.com//library/dd460717.aspx), Utwórz kolejności 100s zadań odczytu lub zapisu do bazy danych Azure rozwiązania Cosmos.

## <a name="sdk-usage"></a>Użycie zestawu SDK
1. **Zainstaluj najnowsze zestaw SDK**

    Zestawy SDK Azure rozwiązania Cosmos bazy danych jest stale ulepszana aby zapewnić najlepszą wydajność. Zobacz [zestawu SDK usługi Azure rozwiązania Cosmos DB](documentdb-sdk-dotnet.md) strony, aby określić najbardziej aktualnych zestawu SDK i przejrzyj ulepszenia.
2. **Okres istnienia aplikacji za pomocą klienta pojedyncze bazy danych Azure rozwiązania Cosmos**

    Każde wystąpienie DocumentClient wątkowo i wykonuje zarządzania wydajność połączenia i adres buforowanie w trybie bezpośredniego. Aby umożliwić zarządzanie połączeniami wydajne i większą wydajność przez DocumentClient, zaleca się używać jednego wystąpienia DocumentClient dla domeny AppDomain przez czas ich istnienia aplikacji.

   <a id="max-connection"></a>
3. **Zwiększ System.Net MaxConnections na host w trybie bramy**

    Azure DB rozwiązania Cosmos żądania są wykonywane za pośrednictwem protokołu HTTPS/REST w trybie bramy i są poddawane domyślny limit połączeń na nazwę hosta lub adres IP. Konieczne może być ustawiona MaxConnections wyższej wartości (100-1000), aby biblioteka klienta może korzystać z wielu równoczesnych połączeń do bazy danych Azure rozwiązania Cosmos. W zestawie SDK .NET 1.8.0 i powyżej wartości domyślnej dla [ServicePointManager.DefaultConnectionLimit](https://msdn.microsoft.com/library/system.net.servicepointmanager.defaultconnectionlimit.aspx) wynosi 50 i zmienić wartość, można ustawić [Documents.Client.ConnectionPolicy.MaxConnectionLimit](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.connectionpolicy.maxconnectionlimit.aspx) wyższej wartości.   
4. **Dostrajanie równoległe zapytania dla kolekcji partycjonowanych**

     SQL .NET SDK w wersji 1.9.0 lub nowszym zapytania równoległe pomocy technicznej, które umożliwiają kwerenda dotycząca kolekcji partycjonowanych równoległe (zobacz [Praca z zestawów SDK](sql-api-partition-data.md#working-with-the-azure-cosmos-db-sdks) i pokrewnych [przykłady kodu](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/Queries/Program.cs) Aby uzyskać więcej informacji). Zapytania równoległe są przeznaczone do poprawy opóźnienia zapytania i przepływności za pośrednictwem ich odpowiednika szeregowego. Zapytania równoległe podać dwa parametry, które użytkownicy można dostosować do dopasowania niestandardowe ich wymaganiami, () MaxDegreeOfParallelism: do formantu maksymalną liczbę partycji następnie można tworzyć zapytania równoległe i (b) MaxBufferedItemCount: Aby kontrolować liczbę wyników pobranych wstępnie.

    () ***dostrajanie MaxDegreeOfParallelism\:***  równoległe zapytania działa badając równocześnie wiele partycji. Jednak dane z poszczególnych zbieranie podzielonym na partycje jest pobierana szeregowo względem zapytania. Tak ustawienie MaxDegreeOfParallelism liczbę partycji przewiduje maksymalną prawdopodobieństwo osiągnięcia większości kwerend wydajność, wszystkie warunki systemu pozostają takie same. Jeśli nie znasz liczby partycji MaxDegreeOfParallelism można ustawić wyższy i co najmniej (liczba partycji, dane wejściowe podane przez użytkownika) jako MaxDegreeOfParallelism wybierana przez system.

    Należy pamiętać zapytania równoległe tworzy najważniejsze korzyści, jeśli dane jest równomiernie rozłożona wszystkie partycje w odniesieniu do zapytania. Jeśli kolekcja podzielonym na partycje jest podzielona na partycje taki sposób, że wszystkie lub większość danych zwróconych przez kwerendę koncentrują się w kilku partycji (jedną partycję w najgorszym przypadku), a następnie wydajności kwerendy może być wydajność ruchu przez te partycje.

    (b) ***dostrajanie MaxBufferedItemCount\:***  zapytania równoległe zaprojektowano w celu wstępnie pobrać wyniki podczas bieżącej partii wyników jest przetwarzana przez klienta. Pobierania pomaga w ogólnej poprawy opóźnienia zapytania. MaxBufferedItemCount jest parametr, aby ograniczyć liczbę wyników pobranych wcześniej. Ustawienie MaxBufferedItemCount do oczekiwanej liczby wyników zwróconych (lub większa liczba) umożliwia wykonanie zapytania w celu maksymalnego skorzystają z pobierania.

    Pobierania działa tak samo, niezależnie od MaxDegreeOfParallelism, i istnieje pojedynczy bufor dla danych z wszystkich partycji.  
5. **Włącz GC po stronie serwera**

    W niektórych przypadkach może pomóc zmniejszyć częstotliwość operacji wyrzucania elementów bezużytecznych. W środowisku .NET, należy ustawić [gcserver —](https://msdn.microsoft.com/library/ms229357.aspx) na wartość true.
6. **Implementowanie wycofywania odstępach RetryAfter**

    Podczas testowania wydajności, należy zwiększyć obciążenie dopóki mała liczba żądań pobrania ograniczany. Jeśli ograniczany, aplikacja kliencka powinna wycofywania na ograniczania dla interwału ponawiania określić serwer. Przestrzeganie wycofywania zapewnia spędzają na skraca czas oczekiwania między kolejnymi próbami. Obsługa zasad ponawiania znajduje się w wersji 1.8.0 lub nowszym programu SQL Server [.NET](sql-api-sdk-dotnet.md) i [Java](sql-api-sdk-java.md), wersja 1.9.0 i nowszymi wersjami z [Node.js](sql-api-sdk-node.md) i [Python](sql-api-sdk-python.md), a wszystkie obsługiwane wersje [.NET Core](sql-api-sdk-dotnet-core.md) zestawów SDK. Aby uzyskać więcej informacji, zobacz [Exceeding zastrzeżone ograniczenia przepływności](request-units.md#RequestRateTooLarge) i [RetryAfter](https://msdn.microsoft.com/library/microsoft.azure.documents.documentclientexception.retryafter.aspx).
    
    Z wersji 1,19 i nowszych zestawu .NET SDK istnieje mechanizm rejestrowania dodatkowych informacji diagnostycznych i rozwiązywania problemów opóźnienia, jak pokazano w poniższym przykładzie. Możesz zalogować się ciąg diagnostycznych dla żądania, które mają wyższe opóźnienie odczytu. Przechwycony ciąg diagnostycznych pomoże poznać liczbę obserwowanych 429s dla danego żądania.
    ```csharp
    ResourceResponse<Document> readDocument = await this.readClient.ReadDocumentAsync(oldDocuments[i].SelfLink);
    readDocument.RequestDiagnosticsString 
    ```
    
7. **Skalowanie w poziomie obciążenia klientami**

    Jeśli testujesz na poziomach wysokiej przepływności (> 50 000 RU/s), może stać się aplikacja kliencka "wąskie gardło" z powodu maszyny nakładanie się na użycie procesora CPU lub sieci. Jeśli osiągnąć tego punktu, można nadal push dodatkowe konto bazy danych Azure rozwiązania Cosmos przez skalowania aplikacji klienta na wielu serwerach.
8. **Dokument pamięci podręcznej URI na mniejsze opóźnienia odczytu**

    Dokument pamięci podręcznej URI, jeśli to możliwe, aby uzyskać najlepszą wydajność odczytu.
   <a id="tune-page-size"></a>
9. **Dostosuj rozmiar strony dla źródła danych zapytania/odczytu w celu poprawy wydajności**

    Podczas wykonywania masowego odczytać dokumentów za pomocą funkcji (na przykład ReadDocumentFeedAsync) źródło odczytu lub, jeśli zapytania SQL, wyniki są zwracane w sposób segmentu, jeśli zestaw wyników jest zbyt duży. Domyślnie są zwracane w fragmentów 100 elementów lub 1 MB, jednego z tych limitów trafień pierwszej.

    Aby zmniejszyć liczbę sieci przekazywanych wymagany do pobrania wszystkich odpowiednich wyników, można zwiększyć za pomocą rozmiar strony [x-ms-max elementu count](https://docs.microsoft.com/rest/api/documentdb/common-documentdb-rest-request-headers) nagłówek żądania do maksymalnie 1000. W przypadkach, gdy konieczne jest wyświetlenie tylko kilka wyniki, na przykład jeśli użytkownik interfejsu lub aplikacji interfejsu API zwraca tylko 10 powoduje przez czas, można także zmniejszyć rozmiar strony do 10 do zmniejszenia przepustowości używane dla operacji odczytu i zapytań.

    Mogą również ustawić za pomocą dostępnych zestawów SDK DB rozwiązania Cosmos Azure rozmiar strony.  Na przykład:

        IQueryable<dynamic> authorResults = client.CreateDocumentQuery(documentCollection.SelfLink, "SELECT p.Author FROM Pages p WHERE p.Title = 'About Seattle'", new FeedOptions { MaxItemCount = 1000 });
10. **Zwiększenie liczby wątków/zadań**

    Zobacz [zwiększyć liczbę wątków/zadania](#increase-threads) w sekcji sieci.

11. **Użyj przetwarzania przez hosta 64-bitowych**

    SQL SDK działa w procesie 32-bitowego hosta, gdy używasz zestawu SDK .NET SQL w wersji 1.11.4 lub nowszym. Jeśli używasz krzyżowego partycji zapytania przetwarzania przez hosta 64-bitowych jest zalecane zwiększonej wydajności. Następujących typów aplikacji ma procesu 32-bitowego hosta jako domyślne, tak aby można było zmienić do 64-bitowy, wykonaj następujące czynności na podstawie typu aplikacji:

    - Dla pliku wykonywalnego aplikacji, można to zrobić przez zaznaczenie pola wyboru **preferowane jest 32-bitowych** opcji **właściwości projektu** okna na **kompilacji** kartę.

    - VSTest podstawie projekty testowe, można to zrobić, wybierając **Test**->**testowanie ustawień**->**domyślne architektury procesora jako X64**, z **programu Visual Studio Test** opcji menu.

    - Lokalnie wdrożonych aplikacji sieci Web ASP.NET, można to zrobić przez sprawdzenie **używać 64-bitowej wersji programu IIS Express dla projektów i stron sieci web**w obszarze **narzędzia**->**opcje**->**projekty i rozwiązania**->**projekty sieci Web**.

    - Dla aplikacji sieci Web ASP.NET wdrożonego na platformie Azure, można to zrobić, wybierając **platforma jako 64-bitowych** w **ustawienia aplikacji** w portalu Azure.

## <a name="indexing-policy"></a>Zasady indeksowania
 
1. **Wyklucz ścieżki nieużywane indeksowania dla szybsze zapisy**

    Zasady indeksowania rozwiązania cosmos w DB umożliwia również określić, które ścieżki dokumentu do dołączania lub wykluczania indeksowania dzięki wykorzystaniu indeksowania ścieżki (IndexingPolicy.IncludedPaths i IndexingPolicy.ExcludedPaths). Użycie indeksowania ścieżki zaoferować zapisu lepszą wydajność i dolnym indeksu magazynu w scenariuszach, w których wzorców zapytań są znane wcześniej, jak koszty indeksowania bezpośrednio skorelowanych liczby unikatowych ścieżki zaindeksowane.  Na przykład poniższy kod przedstawia sposób wykluczyć całą sekcję dokumentów () poddrzewo) z indeksowania przy użyciu "*" symboli wieloznacznych.

    ```csharp
    var collection = new DocumentCollection { Id = "excludedPathCollection" };
    collection.IndexingPolicy.IncludedPaths.Add(new IncludedPath { Path = "/*" });
    collection.IndexingPolicy.ExcludedPaths.Add(new ExcludedPath { Path = "/nonIndexedContent/*");
    collection = await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), excluded);
    ```

    Aby uzyskać więcej informacji, zobacz [Azure DB rozwiązania Cosmos zasady indeksowania](indexing-policies.md).

## <a name="throughput"></a>Przepływność
<a id="measure-rus"></a>

1. **Pomiar i dostrajania dla żądania niższe jednostek na sekundę użycia**

    Azure DB rozwiązania Cosmos oferuje bogaty zestaw operacji bazy danych, w tym relacyjnych i hierarchicznych zapytania z funkcji UDF, procedury składowane i wyzwalaczy — wszystkie działania w dokumentach w ramach kolekcji bazy danych. Koszt związany z każdej z tych operacji zależy od Procesora, we/wy i pamięci wymaganej do ukończenia tej operacji. Zamiast analiza zasobów i zarządzania nimi sprzętu można traktować jednostki żądań (RU) jako pojedynczy miary dla zasobów wymaganych do wykonywania różnych operacji bazy danych i usługi żądania aplikacji.

    Przepływność zostanie zainicjowana na podstawie liczby [jednostek żądania](request-units.md) ustawić dla każdego kontenera. Zużycie jednostka żądania jest szacowana jako szybkość na sekundę. Aplikacje, które przekroczyć współczynnika jednostki żądania elastycznie ich kontenera są ograniczone, dopóki częstotliwość spadnie poniżej poziomu elastycznie kontenera. Jeśli aplikacja wymaga wyższego poziomu przepływności, można zwiększyć przepływność sieci przez Inicjowanie obsługi administracyjnej jednostki dodatkowe żądania. 

    Złożoność kwerendy wpływa na liczbę jednostek żądania są używane dla operacji. Liczba predykatów, rodzaj predykaty, liczba funkcji UDF i rozmiaru zestawu danych źródła wszystkich wpływ kosztów operacji zapytania.

    Do mierzenia obciążenie wszelkie operacje (Tworzenie, aktualizowanie lub usuwanie), sprawdzić [x-ms żądania — opłata](https://docs.microsoft.com/rest/api/documentdb/common-documentdb-rest-response-headers) nagłówka (lub równoważne właściwości RequestCharge w ResourceResponse<T> lub FeedResponse<T> w. SDK NET) w celu mierzenia liczby jednostek żądania używanych przez te operacje.

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

    Żądanie zwrócony w nagłówku to jest część sieci udostępnionej przepływności (tj., RUs 2000 / s). Na przykład jeśli poprzednie zapytanie zwraca 1000 dokumentów o rozmiarze 1KB, kosztów operacji to 1000. Tak w ciągu sekundy, serwer będzie honorować tylko dwa takich żądań przed ograniczania kolejnych żądań. Aby uzyskać więcej informacji, zobacz [jednostek żądania](request-units.md) i [Kalkulator jednostki żądania](https://www.documentdb.com/capacityplanner).
<a id="429"></a>
2. **Współczynnik ograniczanie żądań szybkość dojścia za duży**

    Klient próbuje przekracza zarezerwowaną przepływnością dla konta, nie ma bez spadku wydajności na serwerze i nie stosowania przepływności poza poziomem zastrzeżone. Serwer zostanie preemptively zakończyć żądania z RequestRateTooLarge (kod stanu HTTP 429) i zwraca [x-ms ponawiania — po ms](https://docs.microsoft.com/rest/api/documentdb/common-documentdb-rest-response-headers) nagłówek wskazujący ilość czasu (w milisekundach), które użytkownik musi czekać przed ponowną próbą wykonania żądanie.

        HTTP Status 429,
        Status Line: RequestRateTooLarge
        x-ms-retry-after-ms :100

    Zestawy SDK wszystkie niejawnie catch tej odpowiedzi, Szanujemy określony serwer ponownych prób po nagłówka i ponów żądanie. Następna ponowna próba powiedzie się, chyba że Twoje konto jest uzyskiwany jednocześnie przez wielu klientów.

    Jeśli masz więcej niż jednego klienta zbiorczo operacyjnego stale powyżej liczby żądań domyślnej liczby ponownych prób obecnie ustawioną 9 wewnętrznie przez klienta mogą być niewystarczające; w takim przypadku klient zgłasza DocumentClientException z kodem stanu 429 do aplikacji. Przez ustawienie RetryOptions w wystąpieniu ConnectionPolicy można zmienić domyślną liczbę ponownych prób. Domyślnie DocumentClientException z kodem stanu 429 po skumulowany czas oczekiwania 30 sekund jest zwracany, jeśli żądanie będzie kontynuował pracę nad liczby żądań. Dzieje się tak nawet jeśli bieżąca liczba ponownych prób jest mniejsza od liczby ponownych prób max, jest to wartość domyślną 9 lub wartości zdefiniowanej przez użytkownika.

    Podczas zachowanie zautomatyzowanych ponownych prób pomaga zwiększyć odporność oraz poziom użyteczności dla większości aplikacji, może ona w odds podczas wykonywania testów porównawczych wydajności, szczególnie w przypadku pomiaru opóźnienia.  Opóźnienie obserwowanych klienta zostanie chwilowo wzrastają Jeśli eksperyment trafienia ograniczania serwera i powoduje, że klient zestaw SDK, aby dyskretnie spróbuj ponownie. Aby uniknąć opóźnienia rzędu podczas eksperymenty wydajności, mierzyć opłata zwrócony przez każdej operacji i upewnij się, działania żądań poniżej liczby żądań zastrzeżone. Aby uzyskać więcej informacji, zobacz [jednostek żądania](request-units.md).
3. **Projekt dla mniejszych dokumentów dotyczących wyższej przepustowości**

    Rozmiar dokumentu bezpośrednio są korelowane opłata żądania (tj. koszt przetwarzania żądania) dla danej operacji. Operacje na dużych dokumentów koszty były wyższe niż operacje dla małych dokumentów.

## <a name="next-steps"></a>Kolejne kroki
Przykładową aplikację używane do analizowania bazy danych Azure rozwiązania Cosmos w scenariuszach wysokiej wydajności na kilka komputerów klienckich, zobacz [wydajności i skalowania testowania z bazy danych Azure rozwiązania Cosmos](performance-testing.md).

Aby dowiedzieć się więcej na temat projektowania aplikacji zapewnienia skalowalności i wysokiej wydajności, zobacz też [dzielenia na partycje i skalowania w usłudze Azure DB rozwiązania Cosmos](partition-data.md).
