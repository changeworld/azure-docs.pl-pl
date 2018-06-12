---
title: Praca z zmiany źródła pomocy technicznej w usłudze Azure DB rozwiązania Cosmos | Dokumentacja firmy Microsoft
description: Obsługa kanału informacyjnego zmiany bazy danych Azure rozwiązania Cosmos umożliwia śledzenie zmian w dokumentach i wykonywać oparty na zdarzeniach przetwarzania, takich jak wyzwalaczy i aktualizowanie systemów pamięci podręcznych i analiza.
keywords: Zmiana źródła danych
services: cosmos-db
author: rafats
manager: kfile
ms.service: cosmos-db
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 03/26/2018
ms.author: rafats
ms.openlocfilehash: 2600565493a334c7227e5c0d67a5808f30751108
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2018
ms.locfileid: "35261073"
---
# <a name="working-with-the-change-feed-support-in-azure-cosmos-db"></a>Praca z zmiany źródła pomocy technicznej w usłudze Azure DB rozwiązania Cosmos

[Azure DB rozwiązania Cosmos](../cosmos-db/introduction.md) jest szybkie i elastyczne globalnie Replikacja bazy danych, nadają się do IoT gry detaliczna i rejestrowanie operacyjnej aplikacjach. Użyj zmiany danych, aby rozpocząć poza dodatkowe akcje jest wspólnego wzorca projektowego w tych aplikacjach. Te dodatkowe akcje może być jedną z następujących czynności: 

* Wyzwolenie powiadomienie lub wywołanie interfejsu API, gdy dokument zostanie wstawiony lub zmodyfikowany.
* Strumień przetwarzania dla IoT lub wykonywania analizy.
* Przenoszenie danych dodatkowych synchronizowanie z pamięci podręcznej, aparat wyszukiwania lub magazynu danych lub archiwizowaniem danych na chłodni.

**Zmiany źródła pomocy technicznej** w usłudze Azure DB rozwiązania Cosmos umożliwia tworzenie wydajnych i skalowalne rozwiązania dla każdego z tych wzorców, jak pokazano na poniższej ilustracji:

![Przy użyciu bazy danych Azure rozwiązania Cosmos zmiany źródła danych do analizy w czasie rzeczywistym zasilania i scenariuszach obliczeniowych o sterowane zdarzeniami](./media/change-feed/changefeedoverview.png)

> [!NOTE]
> Zmiana źródła pomocy technicznej jest dostępna dla wszystkich modeli danych i kontenerów w usłudze Azure DB rozwiązania Cosmos. Jednak zmiany źródła danych jest do odczytu za pomocą klienta programu SQL i serializuje elementów do formatu JSON. Z powodu JSON formatowania, bazy danych MongoDB, klienci będą występować niezgodność między dokumenty sformatowane formatu BSON i JSON w formacie zmiany źródła danych.

Poniższego klipu wideo Azure Menedżera programów DB rozwiązania Cosmos Andrew Liu pokazuje, jak zmiany bazy danych Azure rozwiązania Cosmos źródła działania.

> [!VIDEO https://www.youtube.com/embed/mFnxoxeXlaU]
>
>

## <a name="how-does-change-feed-work"></a>Jak zmiany źródła pracy?

Zmiana źródła strumieniowego pomocy technicznej w działania bazy danych Azure rozwiązania Cosmos przez nasłuchiwanie w kolekcji usługi Azure DB rozwiązania Cosmos zostały wprowadzone zmiany. Następnie danych wyjściowych posortowaną listę dokumentów, które zostały zmienione w kolejności, w którym zostały zmodyfikowane. Zmiany są trwałe, mogą być przetwarzane asynchronicznie i przyrostowo, a dane wyjściowe mogą być rozproszone na co najmniej jeden konsumentów w celu równoległego przetwarzania. 

Zgodnie z opisem w dalszej części tego artykułu można przeczytać zmiany źródła danych na trzy sposoby:

*   [Przy użyciu funkcji platformy Azure](#azure-functions)
*   [Za pomocą usługi Azure rozwiązania Cosmos SDK bazy danych](#sql-sdk)
*   [Przy użyciu zmian bazy danych Azure rozwiązania Cosmos źródła danych biblioteki procesora](#change-feed-processor)

Zmiany źródła danych jest dostępne dla każdego zakresu klucza partycji w obrębie kolekcji dokumentów i w związku z tym mogą być rozproszone na co najmniej jeden konsumentów w celu równoległego przetwarzania jak pokazano na poniższej ilustracji.

![Przetwarzanie rozproszone zmiany bazy danych Azure rozwiązania Cosmos źródła danych](./media/change-feed/changefeedvisual.png)

Dodatkowe szczegóły:
* Zmiana źródła danych jest włączone domyślnie dla wszystkich kont.
* Można użyć programu [udostępnionej przepływności](request-units.md) w Twoim regionie zapisu lub w dowolnej [odczytu region](distribute-data-globally.md) odczytywać zmian źródła danych, podobnie jak wszystkie inne działania bazy danych Azure rozwiązania Cosmos.
* Zmiana źródła strumieniowego obejmuje wstawienia i operacje aktualizacji do dokumentów w kolekcji. Można przechwycić usuwa przez ustawienie w dokumentach zamiast usuwa flagę "soft-delete". Alternatywnie, można ustawić okresu wygaśnięcia skończoną dla dokumentów za pomocą [możliwości TTL](time-to-live.md), na przykład, 24 godzin i użyj wartości tej właściwości, aby przechwycić usuwa. W tym rozwiązaniu ma przetwarzać zmiany w określonym czasie krótszy niż okres ważności TTL.
* Każdej zmiany do dokumentu zostanie wyświetlone tylko raz w przypadku zmiany źródła danych, a klienci zarządzania ich logiki tworzenie punktów kontrolnych. Biblioteka źródła procesora zmianami zapewnia automatyczne tworzenie punktów kontrolnych i "co najmniej raz" semantyki.
* Tylko najnowsze zmiany w danym dokumencie znajduje się w dzienniku zmian. Pośrednie zmiany mogą nie być dostępne.
* Zmiana źródła danych jest sortowana numer modyfikacji w każdej wartości klucza partycji. Brak nie gwarantuje kolejności między wartości klucza partycji.
* Zmiany mogą być synchronizowane z dowolnego punktu w czasie, oznacza to, że istnieje nie okres przechowywania danych, dla której zmiany są dostępne.
* Zmiany są dostępne w fragmentów zakresami kluczy partycji. Ta funkcja umożliwia zmiany w dużych kolekcjach do przetworzenia równolegle przez wielu użytkowników/serwerów.
* Aplikacje mogą żądać wiele źródeł zmiany równocześnie w tej samej kolekcji.
* ChangeFeedOptions.StartTime umożliwia zapewnienie pierwszy punkt początkowy, na przykład, aby znaleźć token kontynuacji odpowiadający danemu czasu zegara. ContinuationToken, jeśli jest określony, wins za pośrednictwem wartości StartTime i StartFromBeginning. Dokładność ChangeFeedOptions.StartTime jest ~ 5 s. 

## <a name="use-cases-and-scenarios"></a>Przypadki użycia i scenariusze

Źródła danych zmian umożliwia wydajne przetwarzanie dużych zestawów danych z dużą liczbę operacji zapisu i oferuje alternatywę do badania cały zestaw danych, aby określić, co się zmieniło. 

Na przykład w przypadku zmiany, źródła danych, można wykonać następujące zadania wydajnie:

* Aktualizacji pamięci podręcznej, indeksu wyszukiwania lub magazynu danych z danych przechowywanych w usłudze Azure DB rozwiązania Cosmos.
* Implementuje warstw i archiwizowania danych na poziomie aplikacji, czyli przechowywać "gorących danych" w usłudze Azure DB rozwiązania Cosmos i przedawniają "zimnych danych", aby [magazyn obiektów Blob Azure](../storage/common/storage-introduction.md) lub [Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md).
* Wykonywać zero czas przestoju migracji na inne konto bazy danych Azure rozwiązania Cosmos z różnych schemat partycjonowania.
* Implementowanie [potoki lambda na platformie Azure](https://blogs.technet.microsoft.com/msuspartner/2016/01/27/azure-partner-community-big-data-advanced-analytics-and-lambda-architecture/) z bazy danych Azure rozwiązania Cosmos. Azure DB rozwiązania Cosmos zapewnia rozwiązanie skalowalne bazy danych, które można obsługiwać wprowadzanie i zapytań oraz implementował architektury lambda z niskim całkowitego kosztu posiadania. 
* Odbieranie i przechowywania danych o zdarzeniach z urządzeń, czujników, infrastruktury i aplikacje i przetworzyć tych zdarzeń w czasie rzeczywistym z [Azure Stream Analytics](../stream-analytics/stream-analytics-documentdb-output.md), [Apache Storm](../hdinsight/storm/apache-storm-overview.md), lub [Apache Platforma Spark](../hdinsight/spark/apache-spark-overview.md). 

Na poniższej ilustracji przedstawiono, jak potoki lambda, zarówno pozyskiwania, które można użyć zapytania przy użyciu bazy danych Azure rozwiązania Cosmos zmienić źródła pomocy technicznej: 

![Azure na podstawie DB rozwiązania Cosmos lambda potoku dla wprowadzanie i zapytań](./media/change-feed/lambda.png)

Ponadto w ramach Twojej [niekorzystającą](http://azure.com/serverless) sieci web i aplikacji mobilnych można Śledź zdarzenia, takie jak zmiany do klienta profilu, preferencje lub lokalizacji do wyzwalania określonych akcji, takich jak wysyłanie powiadomień wypychanych do urządzeń przy użyciu [Usługę azure Functions](#azure-functions). Jeśli używasz bazy danych Azure rozwiązania Cosmos do tworzenia gier, można wykonywać następujące czynności, na przykład używanie zmiany źródła danych do zaimplementowania w czasie rzeczywistym tablice wyników oparte na wyniki z gry ukończone.

<a id="azure-functions"></a>
## <a name="using-azure-functions"></a>Przy użyciu funkcji platformy Azure 

Jeśli używasz usługi Azure Functions Najprostszym sposobem, aby połączyć się źródłem zmiany bazy danych Azure rozwiązania Cosmos jest dodanie wyzwalacz bazy danych Azure rozwiązania Cosmos do aplikacji usługi Azure Functions. Po utworzeniu wyzwalacz bazy danych Azure rozwiązania Cosmos w aplikacji usługi Azure Functions, wybierz kolekcję bazy danych Azure rozwiązania Cosmos do nawiązania połączenia, a funkcja jest zawsze wyzwalane, gdy zostaną zmienione do kolekcji. 

Wyzwalacze mogą być tworzone w portalu Azure Functions w portalu Azure DB rozwiązania Cosmos lub programowo. Aby uzyskać więcej informacji, zobacz [bazy danych Azure rozwiązania Cosmos: pliki bazy danych obliczeniowych, przy użyciu usługi Azure Functions](serverless-computing-database.md).

<a id="sql-sdk"></a>
## <a name="using-the-sdk"></a>Używanie zestawu SDK

[SQL SDK](sql-api-sdk-dotnet.md) dla bazy danych rozwiązania Cosmos Azure udostępnia wszystkie uprawnienia do odczytu i zarządzanie nimi zmiany źródła danych. Jednak z dużą zasilania pochodzi zbyt wiele zadań. Jeśli chcesz zarządzać punktami kontrolnymi, postępowania w przypadku numerów sekwencji dokumentów i mają kontrolę nad kluczy partycji przy użyciu zestawu SDK może być odpowiednie podejście.

W tej sekcji przedstawiono sposób użycia zestawu SDK SQL do pracy z zmiany źródła danych.

1. Początek przeczytaj następujące zasoby z appconfig. Instrukcje dotyczące pobierania klucza punktu końcowego i autoryzacji są dostępne w [zaktualizować parametry połączenia](create-sql-api-dotnet.md#update-your-connection-string).

    ``` csharp
    DocumentClient client;
    string DatabaseName = ConfigurationManager.AppSettings["database"];
    string CollectionName = ConfigurationManager.AppSettings["collection"];
    string endpointUrl = ConfigurationManager.AppSettings["endpoint"];
    string authorizationKey = ConfigurationManager.AppSettings["authKey"];
    ```

2. Tworzenie klienta w następujący sposób:

    ```csharp
    using (client = new DocumentClient(new Uri(endpointUrl), authorizationKey,
    new ConnectionPolicy { ConnectionMode = ConnectionMode.Direct, ConnectionProtocol = Protocol.Tcp }))
    {
    }
    ```

3. Pobierz zakresami kluczy partycji:

    ```csharp
    FeedResponse pkRangesResponse = await client.ReadPartitionKeyRangeFeedAsync(
        collectionUri,
        new FeedOptions
            {RequestContinuation = pkRangesResponseContinuation });
     
    partitionKeyRanges.AddRange(pkRangesResponse);
    pkRangesResponseContinuation = pkRangesResponse.ResponseContinuation;
    ```

4. Wywołanie ExecuteNextAsync dla każdego zakresu klucza partycji:

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
> Zamiast `ChangeFeedOptions.PartitionKeyRangeId`, można użyć `ChangeFeedOptions.PartitionKey` do określenia klucza jednej partycji, dla którego można pobrać zmiany źródła danych. Na przykład `PartitionKey = new PartitionKey("D8CFA2FD-486A-4F3E-8EA6-F3AA94E5BD44")`.
> 
>

Jeśli masz wiele czytników, możesz użyć **ChangeFeedOptions** rozłożenie obciążenia odczytu w różnych wątkach lub różnych klientów.

To wszystko, te kilku wierszy kodu można rozpocząć odczytywanie źródła zmiany. Kompletny kod używany w tym artykule można uzyskać [repozytorium GitHub](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/code-samples/ChangeFeed).

W kodzie w kroku 4 powyżej **ResponseContinuation** w ciągu ostatnich wiersz ma ostatni numer sekwencji logiczne (LSN) dokumentu, który będzie używany przy następnym odczytu nowych dokumentów po ten numer sekwencji. Za pomocą **StartTime** z **ChangeFeedOption** poszerzyć Twojej sieci, można pobrać dokumentów. Tak, jeśli Twoje **ResponseContinuation** ma wartość null, ale Twoje **StartTime** Przechodzi wstecz w czasie, a następnie pobierze wszystkie dokumenty, które zmianie od czasu **StartTime**. Jednak jeśli Twoje **ResponseContinuation** ma wartość, a następnie system będzie można wszystkie dokumenty od tego numeru LSN.

Tak tablica punkt kontrolny tylko przechowywanie numeru LSN dla każdej partycji. Ale jeśli nie chcesz uwzględniać partycji, punkty kontrolne, numer LSN, godzina rozpoczęcia, itp. opcja prostsze jest użycie zmiany źródła danych biblioteki procesora.

<a id="change-feed-processor"></a>
## <a name="using-the-change-feed-processor-library"></a>Przy użyciu zmian źródła danych biblioteki procesora 

[Zmiany bazy danych Azure rozwiązania Cosmos źródła danych biblioteki procesora](https://docs.microsoft.com/azure/cosmos-db/sql-api-sdk-dotnet-changefeed) pomoże Ci łatwo rozpowszechniają przetwarzania zdarzeń wielu klientów. Ta biblioteka upraszcza odczytu zmiany na partycje i wiele wątków działające równolegle.

Największą zaletą zmiany procesora źródła biblioteki jest nie trzeba zarządzać każdej partycji oraz token kontynuacji i nie trzeba ręcznie sondowania każdej kolekcji.

Biblioteka źródła procesora zmianami upraszcza odczytu zmiany na partycje i wiele wątków działające równolegle.  Automatycznie zarządza odczytu zmiany na partycje przy użyciu mechanizmu dzierżawy. Jak widać na poniższej ilustracji, po uruchomieniu dwóch klientów, którzy korzystają zmiany źródła danych biblioteki procesora, rozdzielenie pracy między sobą. Jak zwiększyć klientów, ich zachować podziału pracy między sobą.

![Przetwarzanie rozproszone zmiany bazy danych Azure rozwiązania Cosmos źródła danych](./media/change-feed/change-feed-output.png)

Po lewej stronie klient został uruchomiony w pierwszej i jego uruchomienia monitorowania wszystkie partycje, a następnie drugi klienta została uruchomiona, a następnie pierwszy let Przejdź niektórych dzierżaw do drugiego klienta. Jak widać to jest dobry sposób rozdzielania pracy między kilka różnych maszyn i klientami.

Należy pamiętać, że jeśli dwie niekorzystającą funtions Azure monitorowanie tej samej kolekcji i przy użyciu tej samej dzierżawy, a następnie dwie funkcje mogą wystąpić różne dokumenty jak biblioteki procesora postanawia processs partycji w zależności od.

<a id="understand-cf"></a>
### <a name="understanding-the-change-feed-processor-library"></a>Opis zmiany źródła danych biblioteki procesora

Istnieją cztery główne składniki wdrażania biblioteki źródła procesora zmiany: monitorowanych kolekcję, Kolekcja dzierżawy hosta procesora i konsumentów. 

> [!WARNING]
> Tworzenie kolekcji ma wpływ na cenę, ponieważ rezerwujesz przepływność aplikacji na potrzeby komunikowania się z usługą Azure Cosmos DB. Aby uzyskać więcej informacji, odwiedź naszą [stronę z cennikiem](https://azure.microsoft.com/pricing/details/cosmos-db/)
> 
> 

**Kolekcja monitorowanych:** monitorowanych kolekcja jest danych, z którego jest generowany zmiany źródła danych. Żadnych instrukcji INSERT i zmiany w kolekcji monitorowane są uwzględniane w źródle danych zmiany kolekcji. 

**Kolekcja dzierżawy:** współrzędne kolekcji dzierżawy przetwarzania zmian źródła danych w wielu pracowników. Oddzielne kolekcji jest używany do przechowywania dzierżaw z jednej dzierżawy dla każdej partycji. Jest korzystne w przypadku przechowywania tej kolekcji dzierżawy na inne konto o regionowi zapisu na którym jest uruchomiona zmiany źródła procesora. Obiekt dzierżawy zawiera następujące atrybuty: 
* Właściciel: Określa hosta, który jest właścicielem dzierżawy
* Kontynuacja: Określa położenie (token kontynuacji) w przypadku zmiany dla określonej partycji
* Znacznik czasu: Czas ostatniego dzierżawy został zaktualizowany; Sygnatura czasowa może służyć do sprawdzenia, czy dzierżawy jest uznawany za wygasły 

**Host procesora:** każdy host Określa, ile partycje do przetworzenia na podstawie liczby wystąpień hostów mają aktywne dzierżawy. 
1.  Po uruchomieniu hosta, uzyskuje dzierżawy w celu zrównoważenia obciążenia na wszystkich hostach. Hosta okresowo odnowieniu dzierżawy, więc pozostaną aktywne dzierżawy. 
2.  Punkty kontrolne hosta ostatniego token kontynuacji do dzierżawy dla każdego do odczytu. Do zapewnienia bezpieczeństwa współbieżności, host sprawdza ETag dla każdej aktualizacji dzierżawy. Obsługiwane są także inne strategie punktu kontrolnego.  
3.  Podczas zamykania systemu host zwalnia wszystkie dzierżawy, ale przechowuje informacje kontynuacji, aby go ponownie później odczytu z przechowywanych punktu kontrolnego. 

W tym momencie liczba hostów nie może być większa niż liczba partycji (dzierżawy).

**Konsumenci:** użytkowników lub pracowników, są wątki przetwarzania zmian źródła danych inicjowane przez każdego hosta. Każdy host procesora może mieć wielu klientów. Każdy odbiorca odczytuje zmiany źródła danych z partycji, który jest przypisany do powiadamia jej hosta zmian i ważność dzierżawy.

Aby jeszcze lepiej zrozumieć, jak te cztery elementy zmiany źródła pracy procesora, Przyjrzyjmy się przykład na poniższym diagramie. Kolekcja monitorowanych przechowuje dokumenty i używa "Miasto" jako klucza partycji. Widzimy niebieski partycji i tak dalej zawiera dokumenty z polem "Miasto" od "A-E". Istnieją dwa hosty, każda z dwóch konsumentów odczytu z cztery partycje równolegle. Strzałki oznaczają konsumentów czytania z określonego punktu w przypadku zmiany źródła danych. W pierwszej partycji niebieski ciemniejszego reprezentuje nieprzeczytana zmiany podczas koloru niebieskiego reprezentuje już odczytu zmiany w przypadku zmiany źródła danych. Hosty używają kolekcji dzierżawy do przechowywania wartości "kontynuacji", aby śledzić bieżącą pozycję odczytu dla każdego konsumenta. 

![Przy użyciu zmian bazy danych Azure rozwiązania Cosmos źródła danych hosta procesora](./media/change-feed/changefeedprocessornew.png)

### <a name="working-with-the-change-feed-processor-library"></a>Praca z zmiany źródła danych biblioteki procesora

Przed zainstalowaniem zmiany źródła danych pakietu NuGet procesora, najpierw zainstalować: 

* Microsoft.Azure.DocumentDB, wersja 1.13.1 lub nowszy 
* Newtonsoft.Json, wersja 9.0.1 lub nowszy

Następnie zainstaluj [pakietu Microsoft.Azure.DocumentDB.ChangeFeedProcessor Nuget](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.ChangeFeedProcessor/) i dołączyć go jako odwołanie.

Aby zaimplementować biblioteki procesora źródła zmiany, musisz wykonać następujące:

1. Implementowanie **DocumentFeedObserver** obiektu, który implementuje **IChangeFeedObserver**.

2. Implementowanie **DocumentFeedObserverFactory**, który implementuje **IChangeFeedObserverFactory**.

3. W **CreateObserver** metody **DocumentFeedObserverFacory**, wystąpienia **ChangeFeedObserver** utworzonego w kroku 1 i przywrócić go.

    ```
    public IChangeFeedObserver CreateObserver()
    {
              DocumentFeedObserver newObserver = new DocumentFeedObserver(this.client, this.collectionInfo);
              return newObserver;
    }
    ```

4. Utwórz wystąpienie **DocumentObserverFactory**.

5. Utwórz wystąpienie **ChangeFeedEventHost**:

    ```csharp
    ChangeFeedEventHost host = new ChangeFeedEventHost(
                     hostName,
                     documentCollectionLocation,
                     leaseCollectionLocation,
                     feedOptions,
                     feedHostOptions);
    ```

6. Zarejestruj **DocumentFeedObserverFactory** z hostem.

Kod kroki od 4 do 6 jest: 

```
ChangeFeedOptions feedOptions = new ChangeFeedOptions();
feedOptions.StartFromBeginning = true;

ChangeFeedHostOptions feedHostOptions = new ChangeFeedHostOptions();
 
// Customizing lease renewal interval to 15 seconds.
// Can customize LeaseRenewInterval, LeaseAcquireInterval, LeaseExpirationInterval, FeedPollDelay
feedHostOptions.LeaseRenewInterval = TimeSpan.FromSeconds(15);
 
using (DocumentClient destClient = new DocumentClient(destCollInfo.Uri, destCollInfo.MasterKey))
{
        DocumentFeedObserverFactory docObserverFactory = new DocumentFeedObserverFactory(destClient, destCollInfo);
        ChangeFeedEventHost host = new ChangeFeedEventHost(hostName, documentCollectionLocation, leaseCollectionLocation, feedOptions, feedHostOptions);
        await host.RegisterObserverFactoryAsync(docObserverFactory);
        await host.UnregisterObserversAsync();
}
```

To wszystko. Po wykonaniu tych kroków kilka dokumentów rozpocznie się do **DocumentFeedObserver ProcessChangesAsync** metody. Znajdź powyżej kod w [repozytorium GitHub](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/code-samples/ChangeFeedProcessor)

## <a name="faq"></a>Często zadawane pytania

### <a name="what-are-the-different-ways-you-can-read-change-feed-and-when-to-use-each-method"></a>Jakie są różne sposoby, możesz przeczytać zmiany źródła danych? i kiedy należy używać każdej metody?

Istnieją trzy opcje służące do zmiany źródła danych do odczytu:

* **[Za pomocą usługi Azure rozwiązania Cosmos bazy danych SQL interfejsu API zestawu .NET SDK](#sql-sdk)**
   
   Za pomocą tej metody, otrzymasz niski poziom kontroli na zmiany źródła danych. Można zarządzać punktu kontrolnego, można uzyskać dostęp itp klucza określonej partycji. Jeśli masz wiele czytników, możesz użyć [ChangeFeedOptions](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.changefeedoptions?view=azure-dotnet) rozłożenie obciążenia odczytu w różnych wątkach lub różnych klientów. .

* **[Przy użyciu zmian bazy danych Azure rozwiązania Cosmos źródła danych biblioteki procesora](#change-feed-processor)**

   Jeśli chcesz zewnętrzny partii złożoności zmiany źródła danych, a następnie zmień procesora źródła biblioteki można używać. Ta biblioteka ukrywa partii złożoności, ale nadal umożliwia pełnej kontroli w zmienić źródła danych. Ta biblioteka następuje [wzorzec obserwatora](https://en.wikipedia.org/wiki/Observer_pattern), funkcja przetwarzania jest wywoływana przez zestaw SDK. 

   Jeśli zmiana wysokiej przepływności, źródła danych, można utworzyć wystąpienia wielu klientów do odczytu źródła danych zmian. Ponieważ używasz "Zmień biblioteki procesora źródła strumieniowego" automatycznie podzieli obciążenia między różnych klientów. Nie trzeba wykonywać żadnych czynności. Wszystkie złożoności jest obsługiwane przez zestaw SDK. Jednak jeśli chcesz mieć własną usługę równoważenia obciążenia, następnie można wdrożyć IParitionLoadBalancingStrategy strategii niestandardowych partycji. Implementuje IPartitionProcessor — niestandardowe przetwarzania zmian na partycji. Jednak przy użyciu zestawu SDK, można przetwarzać zakres partycji, ale jeśli chcesz przetworzyć klucza określonej partycji następnie należy użyć zestawu SDK dla interfejsu API SQL.

* **[Przy użyciu funkcji platformy Azure](#azure-functions)** 
   
   Ostatnia opcja funkcji platformy Azure jest to najprostsza opcja. Firma Microsoft zaleca użycie tej opcji. Po utworzeniu wyzwalacz bazy danych Azure rozwiązania Cosmos w aplikacji usługi Azure Functions, wybierz kolekcję bazy danych Azure rozwiązania Cosmos do nawiązania połączenia i funkcji jest zawsze wyzwalane, gdy zostaną zmienione do kolekcji. Obejrzyj [rzutowania ekranu](https://www.youtube.com/watch?v=Mnq0O91i-0s&t=14s) użycia Azure funkcji i zmienić źródła danych

   Wyzwalacze mogą być tworzone w portalu Azure Functions w portalu Azure DB rozwiązania Cosmos lub programowo. Visual Studio i kodzie VS ma doskonałą pomoc techniczną do pisania funkcji platformy Azure. Można zapisać i debugowania kodu na pulpicie, a następnie wdrożyć funkcja jednym kliknięciem. Aby uzyskać więcej informacji, zobacz [bazy danych Azure rozwiązania Cosmos: pliki bazy danych obliczeniowych, przy użyciu usługi Azure Functions](serverless-computing-database.md) artykułu.

### <a name="what-is-the-sort-order-of-documents-in-change-feed"></a>Co to jest porządek sortowania dokumentów w przypadku zmiany źródła danych?

Zmiana źródła danych dokumentów jest dostarczany w kolejności ich czas modyfikacji. Ta kolejność sortowania jest gwarantowana tylko jednej partycji.

### <a name="for-a-multi-region-account-what-happens-to-the-change-feed-when-the-write-region-fails-over-does-the-change-feed-also-failover-would-the-change-feed-still-appear-contiguous-or-would-the-fail-over-cause-change-feed-to-reset"></a>W przypadku konta co się dzieje zmiany źródła danych, gdy region zapisu nie powiedzie się w tryb failover? Zmiana źródła również trybu failover? Zmiana źródła danych nadal pojawiały się ciągłe lub Przyczyna awaryjnej zmieniłby źródła danych, aby zresetować?

Tak, zmiany źródła danych będą działać w operacji ręcznego przełączania trybu failover i będzie mieć postać ciągłą.

### <a name="how-long-change-feed-persist-the-changed-data-if-i-set-the-ttl-time-to-live-property-for-the-document-to--1"></a>Jak długo zmiany źródła utrwalić zmienione dane, jeśli właściwość czas wygaśnięcia (czas wygaśnięcia) w dokumencie I ustawioną wartość -1?

Zmiana źródła pozostanie nieskończona. Jeśli dane nie są usuwane, pozostanie w źródle strumieniowym zmiany.

### <a name="how-can-i-configure-azure-functions-to-read-from-a-particular-region-as-change-feed-is-available-in-all-the-read-regions-by-default"></a>Jak skonfigurować usługę Azure functions można odczytać z obszarem określonym jako źródła danych zmian jest dostępna we wszystkich regionach odczytu domyślnie?

Obecnie nie jest możliwe do skonfigurowania usługi Azure Functions można odczytać z określonym regionie. Istnieje problem GitHub w repozytorium Azure Functions można ustawić preferowaną regionów powiązanie bazy danych Azure rozwiązania Cosmos i wyzwalacza.

Środowisko Azure Functions używa domyślnej zasady połączenia. Można skonfigurować tryb połączenia usługi Azure Functions i domyślnie, odczytuje z regionu zapisu, najlepiej na wspólne umieszczanie usługi Azure Functions w tym samym regionie.

### <a name="what-is-the-default-size-of-batches-in-azure-functions"></a>Co to jest domyślny rozmiar partii w usługi Azure Functions?

100 dokumentów na każdego wywołania usługi Azure Functions. Jednak ta liczba jest konfigurowane w pliku function.json. W tym miejscu zakończeniu [listę opcji konfiguracji](../azure-functions/functions-run-local.md). Jeśli tworzysz lokalnie, należy zaktualizować ustawienia aplikacji w ramach [local.settings.json](../azure-functions/functions-run-local.md) pliku.

### <a name="i-am-monitoring-a-collection-and-reading-its-change-feed-however-i-see-i-am-not-getting-all-the-inserted-document-some-documents-are-missing-what-is-going-on-here"></a>Jestem I monitorowania kolekcji i odczytywania zmian źródła danych, jednak Zobacz się, że nie są zwracane wstawianego dokumentu, brakuje niektórych dokumentów. Co się dzieje w tym miejscu?

Upewnij się, że nie jest żadnych innych funkcji odczytywania tej samej kolekcji o tej samej kolekcji dzierżawy. Zdarzyło się to do mnie, a później zrealizowane się, że brakuje dokumenty są przetwarzane przez mój usługę Azure functions, który także używa tej samej dzierżawy.

W związku z tym w przypadku tworzenia wielu usługi Azure Functions można odczytać takie same zmienić źródła danych, a następnie należy użyć innej dzierżawy kolekcji lub użyć konfiguracji "leasePrefix" na współużytkowanie tej samej kolekcji. Jednak używania biblioteki źródła procesora zmiany można uruchomić wiele wystąpień funkcji i dzieli dokumentów między różnymi wystąpieniami automatycznie dla zestawu SDK.

### <a name="my-document-is-updated-every-second-and-i-am-not-getting-all-the-changes-in-azure-functions-listening-to-change-feed"></a>Dokument jest aktualizowany co sekundę i nie są zwracane wszystkie zmiany w funkcjach Azure nasłuchiwania do zmiany źródła danych.

Azure zmiany sond funkcji źródła danych dla co 5 sekund, wszystkie zmiany wprowadzone od 5 sekund zostaną utracone. Azure DB rozwiązania Cosmos przechowuje tylko jedną wersję co 5 sekund, 5. zmiana zostanie wyświetlony w dokumencie. Jednak jeśli chcesz przejść do poniżej 5-sekundowego i chcesz przeprowadzać sondowanie zmian źródła danych w ciągu sekundy, można skonfigurować podczas sondowania "feedPollTime", zobacz [powiązania bazy danych Azure rozwiązania Cosmos](../azure-functions/functions-bindings-cosmosdb.md#trigger---configuration). Jest on zdefiniowany w milisekundach z domyślną 5000. Poniżej 1 sekundę jest możliwe, ale nie jest zalecane, ponieważ rozpocznie nagrywania więcej procesora CPU.

### <a name="i-inserted-a-document-in-the-mongo-api-collection-but-when-i-get-the-document-in-change-feed-it-shows-a-different-id-value-what-is-wrong-here"></a>Wstawiona dokumentu w kolekcji Mongo interfejsu API, ale pojawia się dokumentu w źródle strumieniowym zmiany, zawiera wartość inny identyfikator. Co to jest nieprawidłowy w tym miejscu?

Kolekcja jest kolekcja Mongo interfejsu API. Pamiętaj, że zmiany źródła danych do odczytu za pomocą klienta programu SQL i serializuje elementów do formatu JSON. Z powodu JSON formatowania, bazy danych MongoDB, klienci będą występować niezgodność między dokumenty sformatowane formatu BSON i JSON w formacie zmiany źródła danych. Widzisz reprezentacja BSON dokumentu w formacie JSON. Jeśli używasz konta Mongo atrybuty binarne są konwertowane na formacie JSON.

### <a name="is-there-a-way-to-control-change-feed-for-updates-only-and-not-inserts"></a>Czy istnieje sposób kontrolować zmiany źródła danych dotyczy tylko aktualizacji i nie wstawia?

Nie jest już dzisiaj ale ta funkcja jest na plan. Obecnie można dodać nietrwałego znacznika w dokumencie aktualizacji.

### <a name="is-there-a-way-to-get-deletes-in-change-feed"></a>Czy istnieje sposób można pobrać usunięć w źródle strumieniowym zmiany?

Obecnie zmiany źródła strumieniowego nie dziennika usuwa. Stale w celu ulepszania zmiany źródła danych, a jest to na plan. Obecnie można dodać znacznik nietrwałego dokumentu do usunięcia. Dodaj atrybut w dokumencie o nazwie "usunięte" i ustaw ją na wartość "true" i ustaw wartości TTL w dokumencie, aby można było automatycznie usunąć.

### <a name="can-i-read-change-feed-for-historic-documentsfor-example-documents-that-were-added-5-years-back-"></a>Czy mogę odczytywać zmian źródła danych historycznych dokumentów (na przykład dokumenty, które zostały dodane ponownie 5 lat)

Tak, jeśli dokument nie zostanie usunięta. zmiany można znaleźć źródła danych, zależnie od źródła kolekcji.

### <a name="can-i-read-change-feed-using-javascript"></a>Czy można czytać zmiany źródła danych przy użyciu języka JavaScript?

Tak, Node.js SDK początkowej pomocy technicznej dla źródła danych zmian ostatnio dodane. Mogą być używane, jak pokazano w poniższym przykładzie należy moduł documentdb aktualizacji do wersji bieżącej przed uruchomieniem kodu:

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

### <a name="can-i-read-change-feed-using-java"></a>Czy mogę odczytywać zmian źródła danych przy użyciu języka Java

Biblioteka języka Java można odczytać zmiany źródła danych jest dostępna w [repozytorium Github](https://github.com/Azure/azure-documentdb-changefeedprocessor-java). Jednak obecnie biblioteka języka Java jest kilka wersji za biblioteki .NET. Wkrótce zarówno biblioteki zostaną zsynchronizowane.

### <a name="can-i-use-etag-lsn-or-ts-for-internal-bookkeeping-which-i-get-in-response"></a>Dla wewnętrznej księgowości, która pojawia się w odpowiedzi można użyć _etag, _lsn lub _ts?

wewnętrzny _etag format i powinien nie zależą od niej (nie przeanalizować go) nie można zmienić w każdej chwili.
_ts jest sygnaturę czasową utworzenia lub modyfikacji. _Ts służy do porównywania chronologicznym.
_lsn to identyfikator partii, który zostanie dodany tylko w przypadku zmiany źródła danych, reprezentuje identyfikator transakcji z magazynu... Wiele dokumentów mogą mieć tej samej _lsn.
Jedyną operacją więcej należy pamiętać, ETag na FeedResponse różni się od _etag, można znaleźć w dokumencie. _etag jest wewnętrzny identyfikator i umożliwia współbieżności, informuje o wersji dokumentu i element ETag jest używany do sekwencjonowania kanału informacyjnego.

### <a name="does-reading-change-feed-add-any-additional-cost-"></a>Czy odczytywanie źródła danych zmian dodaje żadnych dodatkowych kosztów?

Naliczane są opłaty dla RU używane np. przenoszenie danych do i kolekcje bazy danych Azure rozwiązania Cosmos zawsze używać RU. Użytkownicy będą naliczane opłaty dla RU wykorzystanych w ramach kolekcji dzierżawy.

### <a name="can-multiple-azure-functions-read-one-collections-change-feed"></a>Wiele funkcji Azure może odczytywać źródło zmiany w jednej kolekcji?

Tak. Wiele funkcji Azure może odczytywać źródło zmiany tej samej kolekcji. Jednak usługi Azure Functions musi być oddzielne leaseCollectionPrefix zdefiniowane.

### <a name="should-the-lease-collection-be-partitioned"></a>Można wyznaczyć kolekcji dzierżawy?

Nie można naprawić kolekcji dzierżawy. Kolekcji partycjonowanych dzierżawy nie jest wymagana i nie jest obecnie jest obsługiwany.

### <a name="can-i-read-change-feed-from-spark"></a>Zmiana może odczytywać źródła danych z platformy Spark?

Tak, możesz. Zobacz [łącznika usługi Azure rozwiązania Cosmos DB Spark](spark-connector.md). Oto [rzutowania ekranu](https://www.youtube.com/watch?v=P9Qz4pwKm_0&t=1519s) przedstawiający sposób może przetwarzać zmiany źródła danych jako strumień strukturalnych.

### <a name="if-i-am-processing-change-feed-by-using-azure-functions-say-a-batch-of-10-documents-and-i-get-an-error-at-7th-document-in-that-case-the-last-three-documents-are-not-processed-how-can-i-start-processing-from-the-failed-documentie-7th-document-in-my-next-feed"></a>Jeśli używam I przetwarzania zmian źródła danych za pomocą usługi Azure Functions, powiedz partii dokumentów 10 i pojawia się błąd w dokumencie 7. W takim przypadku ostatnich trzech dokumenty nie są przetwarzane jak można rozpocząć przetwarzanie z dokumentu nie powiodło się (tj. dokument 7) w mojej dalej źródła danych?

Do obsługi błędu, wzorzec zalecane jest zawijany kodu za pomocą bloku try-catch. CATCH błąd i umieścić w kolejce (utraconych) tego dokumentu, a następnie zdefiniuj logiki radzenia sobie z dokumentów, które spowodowało błąd. Ta metoda partii 200 dokumentu i tylko jeden dokument nie powiodło się, nie trzeba wyrzucać całą partię.

W przypadku wystąpienia błędu nie powinien rewind punkt wyboru powrót do początku else będzie można pojawiają się tych dokumentów z zmiany źródła danych. Należy pamiętać, że zmiany źródła zachowuje ostatni zrzut końcowego przystawki dokumentów, ze względu na to Ty może spowodować utratę poprzedniej migawki na dokument. zmiany źródła przechowuje tylko jedno ostatniej wersji dokumentu, a między inne procesy mogą pochodzić i zmieniać dokument.

Jak zachować naprawienie kodu i wkrótce znajdziesz żadnych dokumentów do kolejki utraconych wiadomości.
Środowisko Azure Functions automatycznie jest wywoływana przez system źródła zmiany i punkt wyboru itp obsługiwany wewnętrznie przez funkcji platformy Azure. Jeśli chcesz wycofać punkt wyboru i kontrolowanie wszystkich aspektów, należy rozważyć przy użyciu zmian źródła danych procesora zestawu SDK.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji dotyczących korzystania z bazy danych Azure rozwiązania Cosmos w środowisku Azure Functions zobacz [bazy danych Azure rozwiązania Cosmos: pliki bazy danych obliczeniowych, przy użyciu usługi Azure Functions](serverless-computing-database.md).

Aby uzyskać więcej informacji na temat używania zmiany źródła danych biblioteki procesora korzystać z następujących zasobów:

* [Strona informacji o](sql-api-sdk-dotnet-changefeed.md) 
* [Pakiet Nuget](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.ChangeFeedProcessor/)
* [Przykładowy kod, przedstawiający kroki od 1 do 6 powyżej](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/code-samples/ChangeFeedProcessor)
* [Dodatkowe przykłady z witryny GitHub](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/ChangeFeedProcessor)

Aby uzyskać więcej informacji na temat używania zmiany źródła danych za pomocą zestawu SDK Użyj następujących zasobów:

* [Strona informacji o zestawie SDK](sql-api-sdk-dotnet.md)
