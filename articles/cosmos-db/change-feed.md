---
title: Obsługa kanału informacyjnego pracy zmiany w usłudze Azure Cosmos DB | Dokumentacja firmy Microsoft
description: Obsługa kanału informacyjnego zmian usługi Azure Cosmos DB umożliwia śledzenie zmian w dokumentach i wykonać przetwarzanie oparte na zdarzeniach, takich jak wyzwalacze i aktualizowanie systemów pamięci podręczne i analizy.
keywords: zestawienia zmian
services: cosmos-db
author: rafats
manager: kfile
ms.service: cosmos-db
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 03/26/2018
ms.author: rafats
ms.openlocfilehash: b6d05c5e9bc59df9df7ef8840b70ab027b6e2f74
ms.sourcegitcommit: f58fc4748053a50c34a56314cf99ec56f33fd616
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/04/2018
ms.locfileid: "48269500"
---
# <a name="working-with-the-change-feed-support-in-azure-cosmos-db"></a>Praca ze zmianą Obsługa kanału informacyjnego w usłudze Azure Cosmos DB

[Usługa Azure Cosmos DB](../cosmos-db/introduction.md) są szybkie i elastyczne globalnie replikowane bazy danych odpowiednie dla IoT, gry detaliczna i operacyjne rejestrowania aplikacji. Typowy wzorzec projektowania w tych aplikacjach jest także uruchamiać dodatkowe akcje za pomocą zmian danych. Te dodatkowe akcje, może być dowolny z następujących czynności: 

* Wyzwalanie powiadomienie lub wywołanie interfejsu API, gdy dokument zostanie wstawiony lub zmodyfikowany.
* Stream przetwarzania dla IoT lub przeprowadzania analizy.
* Dodatkowego przenoszenia danych przez synchronizację z pamięci podręcznej, aparat wyszukiwania lub magazynu danych lub archiwizowania danych do zimnego magazynu.

**Obsługa kanału informacyjnego zmian** w usłudze Azure Cosmos DB umożliwia wydajne i skalowalne rozwiązania dla każdego z tych wzorców, jak pokazano na poniższej ilustracji:

![Za pomocą usługi Azure Cosmos DB zmiany źródła danych do zasilania analizy w czasie rzeczywistym i scenariusze obliczeniowe sterowane zdarzeniami](./media/change-feed/changefeedoverview.png)

> [!NOTE]
> Obsługa kanału informacyjnego zmian jest udostępniana dla wszystkich modeli danych i kontenerów w usłudze Azure Cosmos DB. Jednak Kanał informacyjny zmian jest do odczytu za pomocą klienta programu SQL i wykonuje serializację elementów do formatu JSON. Ze względu na JSON formatowania, bazy danych MongoDB, klienci będą występować niezgodność między dokumentów w formacie BSON i JSON sformatowane zestawienia zmian.

## <a name="how-does-change-feed-work"></a>Jak kanału informacyjnego zmian pracy?

Obsługa kanału informacyjnego zmian w usłudze Azure Cosmos DB działa przez nasłuchiwanie w kolekcji usługi Azure Cosmos DB zostały wprowadzone zmiany. Następnie wyświetla posortowaną listę dokumentów, które zostały zmienione w kolejności, w której zostały zmodyfikowane. Zmiany są zachowywane, mogą być przetwarzane asynchronicznie i przyrostowo, a dane wyjściowe mogą być rozproszone między jednego lub wielu użytkowników do przetwarzania równoległego. 

Zmiana źródła danych na trzy różne sposoby, można przeczytać, zgodnie z opisem w dalszej części tego artykułu:

*   [Użycie usługi Azure Functions](#azure-functions)
*   [Za pomocą usługi Azure Cosmos DB zestawu SDK](#sql-sdk)
*   [Za pomocą zmian usługi Azure Cosmos DB źródła danych z biblioteką procesora](#change-feed-processor)

Kanał informacyjny zmian jest dostępny dla każdy zakres kluczy partycji w kolekcji dokumentów i dlatego mogą być rozproszone między jednego lub wielu użytkowników do przetwarzania równoległego jak pokazano na poniższej ilustracji.

![Rozproszone przetwarzanie zestawienia zmian usługi Azure Cosmos DB](./media/change-feed/changefeedvisual.png)

Dodatkowe szczegóły:
* Kanał informacyjny zmian jest domyślnie włączone dla wszystkich kont.
* Możesz użyć swojej [aprowizowanej przepływności](request-units.md) w Twoim regionie zapisu lub w dowolnej [region odczytu](distribute-data-globally.md) odczytywanie zestawienia zmian, podobnie jak wszystkie inne działania usługi Azure Cosmos DB.
* Kanał informacyjny zmian zawiera INSERT i update operacje wykonywane na dokumentach w obrębie kolekcji. Można przechwycić usuwa przez ustawienie flagi "opcji soft-delete" w dokumentach zamiast usuwania. Alternatywnie, można ustawić okres ważności skończoną dla dokumentów za pomocą [możliwości TTL](time-to-live.md), na przykład, 24 godzin i użyj wartości tej właściwości, aby przechwycić usuwa. Za pomocą tego rozwiązania należy przetworzyć zmiany w przedziale czasu krótszy niż okres ważności czasu wygaśnięcia.
* Każda zmiana w dokumencie dokładnie jeden raz w zestawienia zmian, a klienci zarządzanie ich logiki procesu tworzenia punktów kontrolnych. Biblioteką procesora zestawienia zmian zapewnia automatyczne tworzenie punktów kontrolnych i "co najmniej raz" semantyki.
* Tylko najnowsze zmiany w danym dokumencie znajduje się w dzienniku zmian. Pośrednie zmiany mogą nie być dostępne.
* Kanał informacyjny zmian jest posortowana w kolejności ich modyfikacji w każdej wartości klucza partycji. Nie jest zagwarantowana kolejność, w wartości klucza partycji.
* Zmiany mogą być synchronizowane z dowolnego punktu w czasie, oznacza to, że nie ma żadnych okres przechowywania danych, dla której zmiany są dostępne.
* Zmiany są dostępne we fragmentach zakresów kluczy partycji. Ta funkcja umożliwia zmiany w dużych kolekcjach ma być przetwarzana równolegle przez wielu odbiorców/serwerów.
* Aplikacje mogą żądać wielu ze źródeł zmian równocześnie w tej samej kolekcji.
* ChangeFeedOptions.StartTime może służyć zapewnienie pierwszego punktu początkowego, na przykład, aby znaleźć token kontynuacji odpowiadający danemu godziny zegara. Token kontynuacji, jeśli zostanie określony, wins przez wartości StartTime i StartFromBeginning. Dokładność ChangeFeedOptions.StartTime jest OK. 5 sekund. 

## <a name="use-cases-and-scenarios"></a>Przypadki użycia i scenariuszy

Kanał informacyjny zmian umożliwia wydajne przetwarzanie dużych zestawów danych za pomocą dużej liczby zapisów i oferuje alternatywę do wykonywania zapytań w całym zestawie danych, aby zidentyfikować, co zmieniło się. 

Na przykład za pomocą zestawienia zmian, można wykonać następujące zadania efektywnie:

* Zaktualizuj pamięć podręczną, indeks wyszukiwania lub magazynu danych przy użyciu danych przechowywanych w usłudze Azure Cosmos DB.
* Implementowanie danych na poziomie aplikacji warstw oraz archiwizacji, oznacza to, przechowywania "gorących danych" w usłudze Azure Cosmos DB i przedawniają "zimnych danych", aby [usługi Azure Blob Storage](../storage/common/storage-introduction.md) lub [usługi Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md).
* Wykonaj migracje czas przestoju, zero do innego konta usługi Azure Cosmos DB przy użyciu innego schematu partycjonowania.
* Implementowanie [potoki lambda na platformie Azure](https://blogs.technet.microsoft.com/msuspartner/2016/01/27/azure-partner-community-big-data-advanced-analytics-and-lambda-architecture/) za pomocą usługi Azure Cosmos DB. Usługa Azure Cosmos DB zapewnia rozwiązanie skalowalna baza danych, który może obsługiwać pozyskiwania i zapytania i implementowanie architektury lambda przy użyciu niski całkowity koszt posiadania. 
* Odbieranie i przechowywania danych o zdarzeniach z urządzeń, czujników, infrastruktury i aplikacji oraz przetwarzania tych zdarzeń w czasie rzeczywistym za pomocą [usługi Azure Stream Analytics](../stream-analytics/stream-analytics-documentdb-output.md), [Apache Storm](../hdinsight/storm/apache-storm-overview.md), lub [Apache Platforma Spark](../hdinsight/spark/apache-spark-overview.md). 

Na poniższej ilustracji przedstawiono, jak zmienia się potoki lambda, które zarówno odbieranie i zapytania przy użyciu usługi Azure Cosmos DB można użyć kanału informacyjnego pomocy technicznej: 

![Potok usługi Azure Cosmos DB na podstawie lambda pozyskiwanie danych i zapytań](./media/change-feed/lambda.png)

Ponadto w ramach Twojej [bez użycia serwera](http://azure.com/serverless) aplikacje internetowe i mobilne, możesz śledzić zdarzenia, takie jak zmiany klienta profilu, preferencje lub lokalizacji do wyzwalania określonych akcji, takich jak wysyłanie powiadomień wypychanych do urządzeń za pomocą [Usługi azure Functions](#azure-functions). Jeśli używasz usługi Azure Cosmos DB do tworzenia gier, możesz, na przykład użyj Zmień źródło danych do zaimplementowania rankingi w czasie rzeczywistym, w oparciu o wyniki z gry ukończone.

<a id="azure-functions"></a>
## <a name="using-azure-functions"></a>Użycie usługi Azure Functions 

Jeśli używasz usługi Azure Functions Dodaj wyzwalacz usługi Azure Cosmos DB do swojej aplikacji usługi Azure Functions jest najprostszym sposobem, aby nawiązać połączenie Kanał informacyjny zmian usługi Azure Cosmos DB. Po utworzeniu wyzwalacza usługi Azure Cosmos DB w aplikacji usługi Azure Functions, należy wybrać kolekcji usługi Azure Cosmos DB, aby nawiązać połączenie, a funkcja jest wyzwalana po każdej zmianie kolekcji. 

Wyzwalacze mogą być tworzone w portalu usługi Azure Functions w portalu usługi Azure Cosmos DB lub programowo. Aby uzyskać więcej informacji, zobacz [usługi Azure Cosmos DB: obliczeniowych bez użycia serwera bazy danych, przy użyciu usługi Azure Functions](serverless-computing-database.md).

<a id="sql-sdk"></a>
## <a name="using-the-sdk"></a>Używanie zestawu SDK

[Zestaw SDK SQL](sql-api-sdk-dotnet.md) dla usługi Azure Cosmos DB zapewnia wszystkie uprawnienia do odczytu i zarządzanie zestawienia zmian. Jednak z mocą zawiera zbyt wiele zadań. Jeśli chcesz zarządzać punktami kontrolnymi przeciwdziałania numerów sekwencji dokumentów i mają precyzyjną kontrolę nad kluczami partycji, a następnie przy użyciu zestawu SDK może być właściwej metody postępowania.

Ta sekcja zawiera szczegółowe instrukcje dotyczące pracy za pomocą zestawienia zmian przy użyciu zestawu SDK SQL.

1. Rozpocznij od zawierają następujące zasoby z appconfig. Instrukcje dotyczące pobierania klucza punktu końcowego i autoryzacja są dostępne w [aktualizowanie parametrów połączenia](create-sql-api-dotnet.md#update-your-connection-string).

    ``` csharp
    DocumentClient client;
    string DatabaseName = ConfigurationManager.AppSettings["database"];
    string CollectionName = ConfigurationManager.AppSettings["collection"];
    string endpointUrl = ConfigurationManager.AppSettings["endpoint"];
    string authorizationKey = ConfigurationManager.AppSettings["authKey"];
    ```

2. Utwórz klienta w następujący sposób:

    ```csharp
    using (client = new DocumentClient(new Uri(endpointUrl), authorizationKey,
    new ConnectionPolicy { ConnectionMode = ConnectionMode.Direct, ConnectionProtocol = Protocol.Tcp }))
    {
    }
    ```

3. Pobierz partycję kluczy zakresów:

    ```csharp
    FeedResponse pkRangesResponse = await client.ReadPartitionKeyRangeFeedAsync(
        collectionUri,
        new FeedOptions
            {RequestContinuation = pkRangesResponseContinuation });
     
    partitionKeyRanges.AddRange(pkRangesResponse);
    pkRangesResponseContinuation = pkRangesResponse.ResponseContinuation;
    ```

4. Wywołaj ExecuteNextAsync dla każdy zakres kluczy partycji:

    ```csharp
    foreach (PartitionKeyRange pkRange in partitionKeyRanges){
        string continuation = null;
        checkpoints.TryGetValue(pkRange.Id, out continuation);
        IDocumentQuery<Document> query = client.CreateDocumentChangeFeedQuery(
            collectionUri,
            new ChangeFeedOptions
            {
                PartitionKeyRangeId = pkRange.Id,
                StartFromBeginning = true,
                RequestContinuation = continuation,
                MaxItemCount = -1,
                // Set reading time: only show change feed results modified since StartTime
                StartTime = DateTime.Now - TimeSpan.FromSeconds(30)
            });
        while (query.HasMoreResults)
            {
                FeedResponse<dynamic> readChangesResponse = query.ExecuteNextAsync<dynamic>().Result;
    
                foreach (dynamic changedDocument in readChangesResponse)
                    {
                         Console.WriteLine("document: {0}", changedDocument);
                    }
                checkpoints[pkRange.Id] = readChangesResponse.ResponseContinuation;
            }
    }
    ```

> [!NOTE]
> Zamiast `ChangeFeedOptions.PartitionKeyRangeId`, możesz użyć `ChangeFeedOptions.PartitionKey` do określenia pojedynczego klucza partycji dla którego należy pobrać zmiany źródła danych. Na przykład `PartitionKey = new PartitionKey("D8CFA2FD-486A-4F3E-8EA6-F3AA94E5BD44")`.
> 
>

Jeśli masz wielu elementów odczytujących, możesz użyć **ChangeFeedOptions** rozłożenie obciążenia odczytu w różnych wątkach lub różnych klientów.

I to wszystko, te kilku wierszy kodu można rozpocząć odczytywanie zestawienia zmian. Możesz uzyskać kompletny kod używany w tym artykule [repozytorium GitHub](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/code-samples/ChangeFeed).

W kodzie w kroku 4 powyżej **ResponseContinuation** w ciągu ostatnich wiersz ma numer ostatniej sekwencji logiczne (LSN) z dokumentu, który będzie używany przy następnym przeczytać nowych dokumentów po ten numer sekwencyjny. Za pomocą **StartTime** z **ChangeFeedOption** poszerzyć swoje net można pobrać dokumentów. Jeśli Twoje **ResponseContinuation** ma wartość null, Twoja **StartTime** Przechodzi wstecz w czasie, a następnie otrzymasz wszystkie dokumenty, które zmieniło **StartTime**. Jednak jeśli Twoja **ResponseContinuation** ma wartość, a następnie system uzyskasz wszystkie dokumenty od tego numeru LSN.

Macierz punktu kontrolnego jest tak, po prostu zachowanie numeru LSN dla każdej partycji. Ale jeśli nie chcesz poradzić sobie z partycjami, punkty kontrolne, numer LSN, czas rozpoczęcia, itp. opcja prostsze jest użycie zestawienia bibliotece procesora zmian.

<a id="change-feed-processor"></a>
## <a name="using-the-change-feed-processor-library"></a>Za pomocą zmiany źródła danych z biblioteką procesora 

[Zmian usługi Azure Cosmos DB kanału informacyjnego w bibliotece procesora](https://docs.microsoft.com/azure/cosmos-db/sql-api-sdk-dotnet-changefeed) pomoże Ci łatwo Dystrybuuj przetwarzanie zdarzeń w wielu odbiorców. Ta biblioteka upraszcza zmiany odczytywania różnych partycji i wiele wątków działających równolegle.

Główną zaletą biblioteką procesora zestawienia zmian jest nie trzeba zarządzać każdej partycji i token kontynuacji i nie trzeba ręcznie wykonać sondowanie każdej kolekcji.

Biblioteką procesora zestawienia zmian upraszcza zmiany odczytu w partycji i wiele wątków działających równolegle.  Automatycznie zarządza odczytu zmiany na partycje przy użyciu mechanizmu dzierżawy. Jak widać na poniższej ilustracji, jeśli zaczniesz dwóch klientów, którzy korzystają z zestawienia bibliotece procesora zmian, ich podzielić pracę między sobą. W miarę postępu zwiększyć klientów, ich zachowania podzielenie pracy między sobą.

![Rozproszone przetwarzanie zestawienia zmian usługi Azure Cosmos DB](./media/change-feed/change-feed-output.png)

Po lewej stronie klient został uruchomiony w pierwszej i jego uruchomienia, monitorowania, które wszystkie partycje, a następnie drugiego klienta została uruchomiona, a następnie pierwszy porzuciła niektóre dzierżawy do drugiego klienta. Jak widać, to jest dobre rozwiązanie sposób rozdzielania pracy między różnych komputerów i klientów.

Należy pamiętać, że jeśli masz dwa bez użycia serwera funtions Azure monitorowania tej samej kolekcji i przy użyciu tej samej dzierżawy, a następnie dwie funkcje mogą wystąpić różne dokumenty, w zależności od sposobu bibliotece procesora postanawia proces przekazywania partycje.

<a id="understand-cf"></a>
### <a name="understanding-the-change-feed-processor-library"></a>Opis zmiany źródła danych z biblioteką procesora

Istnieją cztery główne składniki wykonawcze biblioteką procesora zestawienia zmian: monitorowanej kolekcji, kolekcję dzierżaw, hosta procesora i konsumentów. 

> [!WARNING]
> Tworzenie kolekcji ma wpływ na cenę, ponieważ rezerwujesz przepływność aplikacji na potrzeby komunikowania się z usługą Azure Cosmos DB. Aby uzyskać więcej informacji, odwiedź naszą [stronę z cennikiem](https://azure.microsoft.com/pricing/details/cosmos-db/)
> 
> 

**Kolekcja monitorowanych:** monitorowanej kolekcji jest danych, z którego jest generowany zestawienia zmian. Wszystkie operacje wstawiania i zmiany w monitorowanej kolekcji są odzwierciedlane w zestawienia zmian kolekcji. 

**Kolekcję dzierżaw:** współrzędne kolekcji dzierżawy przetwarzania zestawienia zmian na wielu procesów roboczych. Oddzielne kolekcji służy do przechowywania dzierżaw przy użyciu jednej dzierżawy dla każdej partycji. Jest lepiej jest przechowywać tej kolekcji dzierżawy na innym koncie z regionem zapisu bliżej na którym działa zestawienia procesora zmian. Obiekt dzierżawy zawiera następujące atrybuty: 
* Właściciel: Określa hosta, który jest właścicielem dzierżawy
* Kontynuacja: Określa położenie (token kontynuacji) w Zmień źródło danych dla określonej partycji
* Sygnatura czasowa: Czas ostatniego dzierżawy została zaktualizowana; Sygnatura czasowa może służyć do sprawdzania, czy dzierżawa jest uznawany za wygasły 

**Host procesora:** każdy host Określa, ile partycje do przetworzenia na podstawie liczby wystąpień hostów mają aktywne dzierżawy. 
1.  Podczas uruchamiania hosta, uzyskuje dzierżawę, aby zrównoważyć obciążenie na wszystkich hostach. Host okresowo odnowieniu dzierżawy, więc dzierżawy pozostają aktywne. 
2.  Punkty kontrolne hosta ostatni token kontynuacji do dzierżawy dla każdego do odczytu. Aby zapewnić bezpieczeństwo współbieżności, hosta sprawdza, czy element ETag dla każdej aktualizacji dzierżawy. Obsługiwane są również inne strategie punktu kontrolnego.  
3.  Podczas zamykania systemu host zwalnia wszystkie dzierżawy, ale zachowuje informacji kontynuacji, dzięki czemu może wznowić działanie czytania od przechowywanej punktu kontrolnego później. 

W tym momencie liczba hostów nie może być większa niż liczba partycji (dzierżawy).

**Odbiorcy:** użytkowników lub pracowników, są wątki, które wykonują przetwarzania zestawienia zmian inicjowane przez każdego hosta. Każdy host procesor może mieć wielu odbiorców. Każdy odbiorca odczytuje zmiany źródła danych z partycji, który jest przypisany do powiadamia o jego hosta zmiany i Wygasłe dzierżawy.

Aby jeszcze lepiej zrozumieć sposób tych czterech elementów kanału informacyjnego zmian pracy procesora ze sobą, Spójrzmy na przykład na poniższym diagramie. Monitorowane kolekcja przechowuje dokumenty i używa "city" jako klucza partycji. Widzimy, że niebieski partycja zawiera dokumenty z polem "city" od "A-E" i tak dalej. Istnieją dwa hosty, każdy z dwóch odbiorców podczas odczytywania z cztery partycje równolegle. Strzałki oznaczają odbiorców podczas odczytywania z określonego miejsca w zestawienia zmian. W pierwszej partycji niebieskim ciemniejsze reprezentuje nieprzeczytane zmiany, gdy jasnoniebieski reprezentuje już odczytu zmiany w przypadku zmiany źródła danych. Hosty używają kolekcję dzierżaw, do przechowywania wartości "kontynuacja", aby śledzić bieżącą pozycję odczytu dla każdego użytkownika. 

![Za pomocą zmian usługi Azure Cosmos DB kanału informacyjnego hosta procesora](./media/change-feed/changefeedprocessornew.png)

### <a name="working-with-the-change-feed-processor-library"></a>Praca z zestawienia bibliotece procesora zmian

Przed zainstalowaniem zmiany źródła danych pakietu NuGet procesora, najpierw zainstaluj: 

* Microsoft.Azure.DocumentDB, najnowszej wersji.
* Newtonsoft.Json, najnowsza wersja

Następnie zainstaluj [pakietu Microsoft.Azure.DocumentDB.ChangeFeedProcessor Nuget](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.ChangeFeedProcessor/) i dołączyć go jako odwołanie.

Aby zaimplementować z biblioteką procesora zestawienia zmian, należy wykonać następujące:

1. Implementowanie **DocumentFeedObserver** obiektu, który implementuje **IChangeFeedObserver**.
    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Azure.Documents;
    using Microsoft.Azure.Documents.ChangeFeedProcessor.FeedProcessing;
    using Microsoft.Azure.Documents.Client;

    /// <summary>
    /// This class implements the IChangeFeedObserver interface and is used to observe 
    /// changes on change feed. ChangeFeedEventHost will create as many instances of 
    /// this class as needed. 
    /// </summary>
    public class DocumentFeedObserver : IChangeFeedObserver
    {
    private static int totalDocs = 0;

        /// <summary>
        /// Initializes a new instance of the <see cref="DocumentFeedObserver" /> class.
        /// Saves input DocumentClient and DocumentCollectionInfo parameters to class fields
        /// </summary>
        /// <param name="client"> Client connected to destination collection </param>
        /// <param name="destCollInfo"> Destination collection information </param>
        public DocumentFeedObserver()
        {
            
        }

        /// <summary>
        /// Called when change feed observer is opened; 
        /// this function prints out observer partition key id. 
        /// </summary>
        /// <param name="context">The context specifying partition for this observer, etc.</param>
        /// <returns>A Task to allow asynchronous execution</returns>
        public Task OpenAsync(IChangeFeedObserverContext context)
        {
            Console.ForegroundColor = ConsoleColor.Magenta;
            Console.WriteLine("Observer opened for partition Key Range: {0}", context.PartitionKeyRangeId);
            return Task.CompletedTask;
        }

        /// <summary>
        /// Called when change feed observer is closed; 
        /// this function prints out observer partition key id and reason for shut down. 
        /// </summary>
        /// <param name="context">The context specifying partition for this observer, etc.</param>
        /// <param name="reason">Specifies the reason the observer is closed.</param>
        /// <returns>A Task to allow asynchronous execution</returns>
        public Task CloseAsync(IChangeFeedObserverContext context, ChangeFeedObserverCloseReason reason)
        {
            Console.ForegroundColor = ConsoleColor.Cyan;
            Console.WriteLine("Observer closed, {0}", context.PartitionKeyRangeId);
            Console.WriteLine("Reason for shutdown, {0}", reason);
            return Task.CompletedTask;
        }

        public Task ProcessChangesAsync(IChangeFeedObserverContext context, IReadOnlyList<Document> docs, CancellationToken cancellationToken)
        {
            Console.ForegroundColor = ConsoleColor.Green;
            Console.WriteLine("Change feed: PartitionId {0} total {1} doc(s)", context.PartitionKeyRangeId, Interlocked.Add(ref totalDocs, docs.Count));
            foreach (Document doc in docs)
            {
                Console.ForegroundColor = ConsoleColor.Yellow;
                Console.WriteLine(doc.Id.ToString());
            }

            return Task.CompletedTask;
        }
    }
    ```

2. Implementowanie **DocumentFeedObserverFactory**, który implementuje **IChangeFeedObserverFactory**.
    ```csharp
     using Microsoft.Azure.Documents.ChangeFeedProcessor.FeedProcessing;

    /// <summary>
    /// Factory class to create instance of document feed observer. 
    /// </summary>
    public class DocumentFeedObserverFactory : IChangeFeedObserverFactory
    {
        /// <summary>
        /// Initializes a new instance of the <see cref="DocumentFeedObserverFactory" /> class.
        /// Saves input DocumentClient and DocumentCollectionInfo parameters to class fields
        /// </summary>
        public DocumentFeedObserverFactory()
        {
        }

        /// <summary>
        /// Creates document observer instance with client and destination collection information
        /// </summary>
        /// <returns>DocumentFeedObserver with client and destination collection information</returns>
        public IChangeFeedObserver CreateObserver()
        {
            DocumentFeedObserver newObserver = new DocumentFeedObserver();
            return newObserver as IChangeFeedObserver;
        }
    }
    ```

3. Zdefiniuj *cancellationtokensource —* i *ChangeFeedProcessorBuilder*

    ```csharp
    private readonly CancellationTokenSource cancellationTokenSource = new CancellationTokenSource();
    private readonly ChangeFeedProcessorBuilder builder = new ChangeFeedProcessorBuilder();
    ```

5. Tworzenie **ChangeFeedProcessorBuilder** po zdefiniowaniu odpowiednich obiektów 

    ```csharp
            string hostName = Guid.NewGuid().ToString();
      
            // monitored collection info 
            DocumentCollectionInfo documentCollectionInfo = new DocumentCollectionInfo
            {
                Uri = new Uri(this.monitoredUri),
                MasterKey = this.monitoredSecretKey,
                DatabaseName = this.monitoredDbName,
                CollectionName = this.monitoredCollectionName
            };
            
            DocumentCollectionInfo leaseCollectionInfo = new DocumentCollectionInfo
                {
                    Uri = new Uri(this.leaseUri),
                    MasterKey = this.leaseSecretKey,
                    DatabaseName = this.leaseDbName,
                    CollectionName = this.leaseCollectionName
                };
            DocumentFeedObserverFactory docObserverFactory = new DocumentFeedObserverFactory();
       
            ChangeFeedProcessorOptions feedProcessorOptions = new ChangeFeedProcessorOptions();

            // ie. customizing lease renewal interval to 15 seconds
            // can customize LeaseRenewInterval, LeaseAcquireInterval, LeaseExpirationInterval, FeedPollDelay 
            feedProcessorOptions.LeaseRenewInterval = TimeSpan.FromSeconds(15);
            feedProcessorOptions.StartFromBeginning = true;

            this.builder
                .WithHostName(hostName)
                .WithFeedCollection(documentCollectionInfo)
                .WithLeaseCollection(leaseCollectionInfo)
                .WithProcessorOptions (feedProcessorOptions)
                .WithObserverFactory(new DocumentFeedObserverFactory());               
                //.WithObserver<DocumentFeedObserver>();  If no factory then just pass an observer

            var result =  await this.builder.BuildAsync();
            await result.StartAsync();
            Console.Read();
            await result.StopAsync();    
    ```

To wszystko. Po wykonaniu tych kroków kilku dokumentów rozpocznie się pojawią się w **DocumentFeedObserver.ProcessChangesAsync** metody.

Powyżej kod znajduje się w celu ilustracji wyświetlić inny rodzaj obiektów i ich interakcji. Należy zdefiniować odpowiednie zmienne i zainicjować ich przy użyciu prawidłowych wartości. Możesz uzyskać kompletny kod używany w tym artykule [repozytorium GitHub](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/code-samples/ChangeFeedProcessorV2).

> [!NOTE]
> Nigdy nie powinny mieć klucz główny, w kodzie lub w pliku konfiguracji, jak pokazano powyżej kodu. Zobacz [jak używać usługi Key Vault można pobrać kluczy](https://sarosh.wordpress.com/2017/11/23/cosmos-db-and-key-vault/).


## <a name="faq"></a>Często zadawane pytania

### <a name="what-are-the-different-ways-you-can-read-change-feed-and-when-to-use-each-method"></a>Jakie są różne sposoby, można znaleźć kanału informacyjnego zmian? i kiedy należy używać każdej metody?

Istnieją trzy opcje do odczytu kanału informacyjnego zmian:

* **[Za pomocą usługi Azure Cosmos DB SQL API .NET SDK](#sql-sdk)**
   
   Za pomocą tej metody, możesz uzyskać niski poziom kontroli na kanał informacyjny zmian. Możesz zarządzać punktu kontrolnego, itp do klucza określonej partycji można uzyskać dostęp. Jeśli masz wielu elementów odczytujących, możesz użyć [ChangeFeedOptions](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.changefeedoptions?view=azure-dotnet) rozłożenie obciążenia odczytu w różnych wątkach lub różnych klientów. .

* **[Za pomocą zmian usługi Azure Cosmos DB źródła danych z biblioteką procesora](#change-feed-processor)**

   Jeśli chcesz zlecają obsługę dużej złożoności Kanał informacyjny zmian można użyć biblioteką procesora zestawienia zmian. Ta biblioteka ukrywa wiele złożoności, ale nadal umożliwia Pełna kontrola na zestawienia zmian. Ta biblioteka jest zgodna [wzorzec obserwatora](https://en.wikipedia.org/wiki/Observer_pattern), funkcja przetwarzania jest wywoływana przez zestaw SDK. 

   Jeśli masz zestawienia zmian wysokiej przepływności, można utworzyć wystąpienie wielu klientów na odczytywanie zestawienia zmian. Ponieważ używasz "Zmień z biblioteką procesora zestawienia" automatycznie dzieli obciążenie między różnych klientów. Nie trzeba podejmować żadnych działań. Wszystkie złożoności jest obsługiwany przez zestaw SDK. Jednak jeśli chcesz mieć własną usługę równoważenia obciążenia, następnie można zaimplementować IParitionLoadBalancingStrategy strategii niestandardowe partycji. Implementowanie IPartitionProcessor — dla niestandardowego przetwarzania zmian na partycji. Jednak przy użyciu zestawu SDK, może przetwarzać zakres partycji, ale jeśli chcesz przetwarzać klucza partycji określonym musisz używać zestawu SDK dla interfejsu API SQL.

* **[Użycie usługi Azure Functions](#azure-functions)** 
   
   Ostatnia opcja funkcji platformy Azure jest to najprostsza opcja. Firma Microsoft zaleca użycie tej opcji. Po utworzeniu wyzwalacza usługi Azure Cosmos DB w aplikacji usługi Azure Functions wybrania kolekcji usługi Azure Cosmos DB, aby nawiązać połączenie, a funkcja jest wyzwalana po każdej zmianie kolekcji. Obejrzyj [rzutowania ekranu](https://www.youtube.com/watch?v=Mnq0O91i-0s&t=14s) z korzystania z systemu Azure funkcji i zestawienia zmian

   Wyzwalacze mogą być tworzone w portalu usługi Azure Functions w portalu usługi Azure Cosmos DB lub programowo. Program Visual Studio i programu VS Code ma fantastyczną pomoc techniczną do pisania funkcji platformy Azure. Można napisać i debugować kod na pulpicie i następnie wdrażanie funkcji za pomocą jednego kliknięcia. Aby uzyskać więcej informacji, zobacz [usługi Azure Cosmos DB: obliczeniowych bez użycia serwera bazy danych, przy użyciu usługi Azure Functions](serverless-computing-database.md) artykułu.

### <a name="what-is-the-sort-order-of-documents-in-change-feed"></a>Co to jest kolejność sortowania dokumentów w zestawienia zmian?

Zmiana źródła danych dokumentów jest dostarczany w kolejności ich czas modyfikacji. Ta kolejność sortowania jest gwarantowane tylko jednej partycji.

### <a name="for-a-multi-region-account-what-happens-to-the-change-feed-when-the-write-region-fails-over-does-the-change-feed-also-failover-would-the-change-feed-still-appear-contiguous-or-would-the-fail-over-cause-change-feed-to-reset"></a>Multiregionalne konta co się dzieje zmiany źródła danych, gdy region zapisu trybu failover kończy się niepowodzeniem? Kanału informacyjnego zmian również trybu failover? Czy źródło nadal zmian wrażenie ciągłych lub Przyczyna trybu failover zmienić źródło danych, aby zresetować?

Tak, kanał informacyjny zmian będzie działać w operacji ręcznej pracy awaryjnej i będą ciągłe.

### <a name="how-long-change-feed-persist-the-changed-data-if-i-set-the-ttl-time-to-live-property-for-the-document-to--1"></a>Jak długo Kanał informacyjny zmian utrwala dane zmienione, jeśli właściwość czas wygaśnięcia (czas wygaśnięcia) w dokumencie ustawioną wartość -1?

Kanał informacyjny zmian zostanie utrzymany nieskończoność. Jeśli dane nie są usuwane, pozostanie w Kanał informacyjny zmian.

### <a name="how-can-i-configure-azure-functions-to-read-from-a-particular-region-as-change-feed-is-available-in-all-the-read-regions-by-default"></a>Jak można skonfigurować usługi Azure functions do odczytu w danym regionie, ponieważ Kanał informacyjny zmian jest dostępna we wszystkich regionach odczytu domyślnie?

Obecnie nie jest możliwe do skonfigurowania usługi Azure Functions do odczytu z określonego regionu. Istnieje problem w usłudze GitHub w repozytorium usługi Azure Functions, aby ustawić preferowane regiony wyzwalacz i powiązania usługi Azure Cosmos DB.

Usługa Azure Functions używa domyślnych zasad połączenia. Tryb połączenia można skonfigurować w usłudze Azure Functions i domyślnie, odczytuje z regionu zapisu, dlatego zaleca się na jednej taśmie usługi Azure Functions na tym samym regionie.

### <a name="what-is-the-default-size-of-batches-in-azure-functions"></a>Co to jest domyślny rozmiar partii w usłudze Azure Functions?

100 dokumentów o każdego wywołania usługi Azure Functions. Jednak ta liczba jest konfigurowane w pliku function.json. Oto kompletny [listę opcji konfiguracji](../azure-functions/functions-run-local.md). Jeśli tworzysz lokalnie, należy zaktualizować ustawienia aplikacji w ramach [local.settings.json](../azure-functions/functions-run-local.md) pliku.

### <a name="i-am-monitoring-a-collection-and-reading-its-change-feed-however-i-see-i-am-not-getting-all-the-inserted-document-some-documents-are-missing-what-is-going-on-here"></a>Monitoruję kolekcji i odczytywanie ich zmian źródła danych, jednak widzę, że nie są zwracane wstawiono dokumentu, brakuje niektórych dokumentów. Co się dzieje w tym miejscu?

Upewnij się, że nie ma innych funkcji odczytu tej samej kolekcji za pomocą tej samej kolekcji dzierżawy. Zdarzyło się to do mnie, a później zrealizowany czy brakujących dokumentów są przetwarzane przez Moja usługa Azure functions, która jest również za pomocą tej samej dzierżawy.

W związku z tym Jeśli tworzysz wiele funkcji platformy Azure do odczytu takie same zestawienia zmian, a następnie należy używać kolekcji bezpiecznych różne dzierżawy lub udostępnianie tej samej kolekcji przy użyciu konfiguracji "leasePrefix". Jednak korzystając z biblioteką procesora zestawienia zmian można uruchomić wiele wystąpień funkcji i zestawu SDK dzieli dokumentów między różne wystąpienia automatycznie dla Ciebie.

### <a name="my-document-is-updated-every-second-and-i-am-not-getting-all-the-changes-in-azure-functions-listening-to-change-feed"></a>Dokument jest aktualizowany co sekundę, a nie są zwracane wszystkie zmiany w usłudze Azure Functions nasłuchiwanie zestawienia zmian.

Więc wszelkie zmiany wprowadzone od 5 sekund zostaną utracone, co 5 sekund kanału informacyjnego zmian sond w usłudze Azure Functions. Usługa Azure Cosmos DB przechowuje tylko jedną wersję co 5 sekund, dzięki czemu otrzymasz 5. zmiany w dokumencie. Jednak jeśli chcesz zejść poniżej 5-sekundowego, i sondować zmiany źródła danych co sekundę, można skonfigurować godziny sondowania "feedPollTime", zobacz [powiązań usługi Azure Cosmos DB](../azure-functions/functions-bindings-cosmosdb.md#trigger---configuration). Jest on zdefiniowany w milisekundach z domyślną 5000. Poniżej 1 sekundę jest możliwe, ale nie jest zalecane, ponieważ rozpocznie nagrywania większej mocy Procesora.

### <a name="i-inserted-a-document-in-the-mongo-api-collection-but-when-i-get-the-document-in-change-feed-it-shows-a-different-id-value-what-is-wrong-here"></a>Wstawiona dokumentu w kolekcji interfejs API Mongo, ale gdy otrzymam dokumentu w Kanał informacyjny zmian pokazuje wartość inny identyfikator. Co to jest nieprawidłowy w tym miejscu?

Twoja kolekcja to kolekcja Mongo interfejsu API. Należy pamiętać, że kanał informacyjny zmian jest do odczytu za pomocą klienta programu SQL i wykonuje serializację elementów do formatu JSON. Ze względu na JSON formatowania, bazy danych MongoDB, klienci będą występować niezgodność między dokumentów w formacie BSON i JSON sformatowane zestawienia zmian. Widzisz, to reprezentacja BSON dokumentu w formacie JSON. Jeśli używasz konta Mongo atrybuty binarne są konwertowane na JSON.

### <a name="is-there-a-way-to-control-change-feed-for-updates-only-and-not-inserts"></a>Czy istnieje sposób kontroluje Zmień źródło danych dla tylko aktualizacje, a nie wstawia?

Nie jest już dziś ale ta funkcja znajduje się w planie. Obecnie można dodać znacznika nietrwałego dokumentu aktualizacji.

### <a name="is-there-a-way-to-get-deletes-in-change-feed"></a>Czy istnieje sposób, aby uzyskać usuwa w Kanał informacyjny zmian?

Obecnie Kanał informacyjny zmian nie dziennika usuwania. Kanał informacyjny zmian jest nieustannie ulepsza. Ponadto ta funkcja znajduje się w planie. Obecnie można dodać znacznika nietrwałego dokumentu do usunięcia. Dodawanie atrybutu w dokumencie o nazwie "usunięta" i ustaw ją na wartość "true" i ustaw czasu wygaśnięcia dokumentu, dzięki czemu może zostać automatycznie usunięty.

### <a name="can-i-read-change-feed-for-historic-documentsfor-example-documents-that-were-added-5-years-back-"></a>Czy mogę odczytywać zmian źródła danych historycznych dokumentów (na przykład dokumenty, które zostały dodane ponownie 5 lat)

Tak, jeśli dokument nie zostanie usunięta. możesz przeczytać zmiany źródła danych, o ile jest to punkt początkowy kolekcji.

### <a name="can-i-read-change-feed-using-javascript"></a>Czy mogę odczytywać Kanał informacyjny zmian przy użyciu języka JavaScript

Tak, zestawu SDK środowiska Node.js wstępna Obsługa Kanał informacyjny zmian ostatnio dodane. Mogą być używane, jak pokazano w poniższym przykładzie, aktualizacja modułu documentdb na bieżącą wersję przed uruchomieniem kodu:

```js

var DocumentDBClient = require('documentdb').DocumentClient;
const host = "https://your_host:443/";
const masterKey = "your_master_key==";
const databaseId = "db";
const collectionId = "c1";
const dbLink = 'dbs/' + databaseId;
const collLink = dbLink + '/colls/' + collectionId;
var client = new DocumentDBClient(host, { masterKey: masterKey });
let options = {
    a_im: "Incremental feed",
    accessCondition: {
        type: "IfNoneMatch",        // Use: - empty condition (or remove accessCondition entirely) to start from beginning.
        //      - '*' to start from current.
        //      - specific etag value to start from specific continuation.
        condition: ""
    }
};
 
var query = client.readDocuments(collLink, options);
query.executeNext((err, results, headers) =&gt; {
    // Now we have headers.etag, which can be used in next readDocuments in accessCondition option.
    console.log(results);
    console.log(headers.etag);
    console.log(results.length);
    options.accessCondition = { type: "IfNoneMatch", condition: headers.etag };
    var query = client.readDocuments(collLink, options);
    query.executeNext((err, results, headers) =&gt; {
        console.log("next one:", results[0]);
    });
});<span id="mce_SELREST_start" style="overflow:hidden;line-height:0;"></span>

```

### <a name="can-i-read-change-feed-using-java"></a>Czy mogę odczytywać Kanał informacyjny zmian przy użyciu języka Java

Biblioteka języka Java można odczytać Kanał informacyjny zmian jest dostępna w [repozytorium Github](https://github.com/Azure/azure-documentdb-changefeedprocessor-java). Obecnie biblioteka języka Java jest jednak kilka wersji za biblioteki .NET. Wkrótce będzie synchronizowany bibliotek programu.

### <a name="can-i-use-etag-lsn-or-ts-for-internal-bookkeeping-which-i-get-in-response"></a>Dla wewnętrznej księgowości, który pojawia się w odpowiedzi można użyć _etag, _lsn lub _ts?

_etag format jest wewnętrznym i nie powinien być zależny na nim (nie analizy), ponieważ go można zmienić w dowolnym momencie.
_ts jest sygnatura czasowa modyfikacji lub utworzenia. _Ts służy do porównywania chronologicznym.
_lsn jest dodawana tylko w przypadku Kanał informacyjny zmian identyfikator partii, reprezentuje identyfikator transakcji z magazynu... Wiele dokumentów może mieć tej samej _lsn.
I jeszcze jedno należy pamiętać, element ETag na FeedResponse różni się od _etag, którą widzisz w dokumencie. _etag jest wewnętrznym identyfikatorem i używany do współbieżności, informuje o wersję dokumentu, a element ETag dla źródła danych harmonogramu.

### <a name="does-reading-change-feed-add-any-additional-cost-"></a>Odczytywanie Kanał informacyjny zmian dodaje żadnego dodatkowego kosztu?

Opłaty są naliczane dla jednostek RU używane np przenoszenia danych i kolekcji usługi Azure Cosmos DB zawsze będą mogli skorzystać jednostek RU. Użytkownikom zostanie naliczona dla jednostek RU wykorzystane przez kolekcję dzierżaw.

### <a name="can-multiple-azure-functions-read-one-collections-change-feed"></a>Wiele funkcji Azure przeczytasz Kanał informacyjny zmian w jednej kolekcji?

Tak. Wiele funkcji platformy Azure mogą odczytywać Kanał informacyjny zmian w tej samej kolekcji. Jednak musisz mieć oddzielne leaseCollectionPrefix, definicja usługi Azure Functions.

### <a name="should-the-lease-collection-be-partitioned"></a>Powinny być dzielone kolekcję dzierżaw

Nie można naprawić kolekcję dzierżaw. Kolekcji partycjonowanej dzierżawy nie jest konieczna, a aktualnie nie jest obsługiwana.

### <a name="can-i-read-change-feed-from-spark"></a>Czy mogę odczytywać zmian źródła danych z platformy Spark?

Tak, możesz. Zobacz [łącznika usługi Azure Cosmos DB Spark](spark-connector.md). Oto [rzutowania ekranu](https://www.youtube.com/watch?v=P9Qz4pwKm_0&t=1519s) pokazujący, jak może przetwarzać zmiany źródła danych jako strumień ze strukturą.

### <a name="if-i-am-processing-change-feed-by-using-azure-functions-say-a-batch-of-10-documents-and-i-get-an-error-at-7th-document-in-that-case-the-last-three-documents-are-not-processed-how-can-i-start-processing-from-the-failed-documentie-7th-document-in-my-next-feed"></a>Jeśli am I przetwarzania zmian źródła danych za pomocą usługi Azure Functions, powiedz partii dokumentów 10 i pojawia się błąd atuj dokument 7. W takim przypadku ostatnie trzy dokumenty nie są przetwarzane jak mogę zacząć przetwarzania z dokumentu nie powiodło się (tj.) dokument 7) w mojej następne źródła danych?

Aby obsłużyć błąd, zalecany wzorzec jest opakowanie kodu za pomocą bloku try-catch. CATCH błąd i umieść tego dokumentu w kolejce (utraconych wiadomości), a następnie zdefiniować logikę radzenia sobie z dokumentów, które spowodowało błąd. Przy użyciu tej metody w przypadku partii 200 dokumentu i tylko jeden dokument nie powiodło się, nie trzeba Pozbywać się całą partię.

W przypadku wystąpienia błędu nie powinien rewind punktu sprawdzania powrót do początku else będzie można nadal jest wyświetlany te dokumenty z kanału informacyjnego zmian. Należy pamiętać, że przechowuje kanału informacyjnego zmian ostatniego zrzut końcowego przystawki dokumentów, w związku z tym możesz utracić poprzednią migawkę do dokumentu. Kanał informacyjny zmian zapewnia tylko jedna wersja ostatniej części dokumentu, a między inne procesy mogą pochodzić i zmian w dokumencie.

Jak zachować naprawianie kodu, wkrótce znajdziesz żadnych dokumentów na kolejki utraconych wiadomości.
Usługa Azure Functions jest wywoływana automatycznie przez system kanału informacyjnego zmian i wyboru punktu itp wewnętrznie obsługiwany przez funkcję platformy Azure. Jeśli chcesz wycofać w punkcie wyboru i kontrolowanie wszystkich aspektów, należy rozważyć przy użyciu zmian źródła danych zestawu SDK procesora.


## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji na temat usługi Azure Cosmos DB przy użyciu usługi Azure Functions zobacz [usługi Azure Cosmos DB: obliczeniowych bez użycia serwera bazy danych, przy użyciu usługi Azure Functions](serverless-computing-database.md).

Aby uzyskać więcej informacji na temat korzystania z zestawienia bibliotece procesora zmian użyj następujących zasobów:

* [Strona z informacjami](sql-api-sdk-dotnet-changefeed.md) 
* [Pakiet Nuget](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.ChangeFeedProcessor/)
* [Przykładowy kod pokazujący kroki 1 – 6 powyżej](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/code-samples/ChangeFeedProcessor)
* [Dodatkowe przykłady w witrynie GitHub](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/ChangeFeedProcessor)

Aby uzyskać więcej informacji na temat korzystania z zmiany źródła danych za pomocą zestawu SDK Użyj następujących zasobów:

* [Strona informacji o zestawie SDK](sql-api-sdk-dotnet.md)
