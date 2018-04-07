---
title: Praca z zmiany źródła pomocy technicznej w usłudze Azure DB rozwiązania Cosmos | Dokumentacja firmy Microsoft
description: Obsługa kanału informacyjnego zmiany bazy danych Azure rozwiązania Cosmos umożliwia śledzenie zmian w dokumentach i wykonywać oparty na zdarzeniach przetwarzania, takich jak wyzwalaczy i aktualizowanie systemów pamięci podręcznych i analiza.
keywords: Zmiana źródła danych
services: cosmos-db
author: rafats
manager: kfile
documentationcenter: ''
ms.assetid: 2d7798db-857f-431a-b10f-3ccbc7d93b50
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: ''
ms.topic: article
ms.date: 03/26/2018
ms.author: rafats
ms.openlocfilehash: 8cc4d8110db0a650b8355f96fee490093826ac30
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
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

1.  [Przy użyciu funkcji platformy Azure](#azure-functions)
2.  [Za pomocą usługi Azure rozwiązania Cosmos SDK bazy danych](#rest-apis)
3.  [Za pomocą procesora Azure rozwiązania Cosmos DB zmiany źródła danych biblioteki](#change-feed-processor)

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

<a id="rest-apis"></a>
## <a name="using-the-sdk"></a>Przy użyciu zestawu SDK

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

Jeśli masz wiele czytników, możesz użyć **ChangeFeedOptions** rozłożenie obciążenia odczytu w różnych wątkach lub różnych klientów.

To wszystko, te kilku wierszy kodu można rozpocząć odczytywanie źródła zmiany. Kompletny kod używany w tym artykule można uzyskać [repozytorium GitHub](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/code-samples/ChangeFeedProcessor).

W kodzie w kroku 4 powyżej **ResponseContinuation** w ciągu ostatnich wiersz ma ostatni numer sekwencji logiczne (LSN) dokumentu, który będzie używany przy następnym odczytu nowych dokumentów po ten numer sekwencji. Za pomocą **StartTime** z **ChangeFeedOption** poszerzyć Twojej sieci, można pobrać dokumentów. Tak, jeśli Twoje **ResponseContinuation** ma wartość null, ale Twoje **StartTime** Przechodzi wstecz w czasie, a następnie pobierze wszystkie dokumenty, które zmianie od czasu **StartTime**. Jednak jeśli Twoje **ResponseContinuation** ma wartość, a następnie system będzie można wszystkie dokumenty od tego numeru LSN.

Tak tablica punkt kontrolny tylko przechowywanie numeru LSN dla każdej partycji. Ale jeśli nie chcesz uwzględniać partycji, punkty kontrolne, numer LSN, godzina rozpoczęcia, itp. opcja prostsze jest korzystanie z biblioteki procesora zmiany źródła danych.

<a id="change-feed-processor"></a>
## <a name="using-the-change-feed-processor-library"></a>Za pomocą biblioteki zmiany źródła procesora 

[Biblioteki Azure rozwiązania Cosmos DB zmiany źródła procesora](https://docs.microsoft.com/azure/cosmos-db/sql-api-sdk-dotnet-changefeed) pomoże Ci łatwo rozpowszechniają przetwarzania zdarzeń wielu klientów. Ta biblioteka upraszcza odczytu zmiany na partycje i wiele wątków działające równolegle.

Największą zaletą biblioteki zmiany źródła procesora jest nie trzeba zarządzać każdej partycji oraz token kontynuacji i nie trzeba ręcznie sondowania każdej kolekcji.

Biblioteka procesora źródła danych zmian upraszcza odczytu zmiany na partycje i wiele wątków działające równolegle.  Automatycznie zarządza odczytu zmiany na partycje przy użyciu mechanizmu dzierżawy. Jak widać na poniższej ilustracji, po uruchomieniu dwóch klientów korzystających z biblioteki procesora źródła danych zmian, ich podzielić pracy między sobą. Jak zwiększyć klientów, ich zachować podziału pracy między sobą.

![Przetwarzanie rozproszone zmiany bazy danych Azure rozwiązania Cosmos źródła danych](./media/change-feed/change-feed-output.png)

Po lewej stronie klient został uruchomiony w pierwszej i jego uruchomienia monitorowania wszystkie partycje, a następnie drugi klienta została uruchomiona, a następnie pierwszy let Przejdź niektórych dzierżaw do drugiego klienta. Jak widać to jest dobry sposób rozdzielania pracy między kilka różnych maszyn i klientami.

Należy pamiętać, że jeśli dwie niekorzystającą funtions Azure monitorowanie tej samej kolekcji i przy użyciu tej samej dzierżawy, a następnie dwie funkcje mogą wystąpić różne dokumenty jak biblioteki procesora postanawia processs partycji w zależności od.

<a id="understand-cf"></a>
### <a name="understanding-the-change-feed-processor-library"></a>Opis biblioteki zmiany źródła procesora

Istnieją cztery główne składniki wdrażania procesora źródła danych zmian: monitorowanych kolekcję, Kolekcja dzierżawy hosta procesora i konsumentów. 

> [!WARNING]
> Tworzenie kolekcji ma wpływ na cenę, ponieważ rezerwujesz przepływność aplikacji na potrzeby komunikowania się z usługą Azure Cosmos DB. Aby uzyskać więcej informacji, odwiedź stronę [stronie dotyczącej cen](https://azure.microsoft.com/pricing/details/cosmos-db/)
> 
> 

**Kolekcja monitorowanych:** monitorowanych kolekcja jest danych, z którego jest generowany zmiany źródła danych. Żadnych instrukcji INSERT i zmiany w kolekcji monitorowane są uwzględniane w źródle danych zmiany kolekcji. 

**Kolekcja dzierżawy:** współrzędne kolekcji dzierżawy przetwarzania zmian źródła danych w wielu pracowników. Oddzielne kolekcji jest używany do przechowywania dzierżaw z jednej dzierżawy dla każdej partycji. Jest korzystne w przypadku przechowywania tej kolekcji dzierżawy na inne konto o regionowi zapisu na którym jest uruchomiona procesora źródła danych zmian. Obiekt dzierżawy zawiera następujące atrybuty: 
* Właściciel: Określa hosta, który jest właścicielem dzierżawy
* Kontynuacja: Określa położenie (token kontynuacji) w przypadku zmiany dla określonej partycji
* Znacznik czasu: Czas ostatniego dzierżawy został zaktualizowany; Sygnatura czasowa może służyć do sprawdzenia, czy dzierżawy jest uznawany za wygasły 

**Host procesora:** każdy host Określa, ile partycje do przetworzenia na podstawie liczby wystąpień hostów mają aktywne dzierżawy. 
1.  Po uruchomieniu hosta, uzyskuje dzierżawy w celu zrównoważenia obciążenia na wszystkich hostach. Hosta okresowo odnowieniu dzierżawy, więc pozostaną aktywne dzierżawy. 
2.  Punkty kontrolne hosta ostatniego token kontynuacji do dzierżawy dla każdego do odczytu. Do zapewnienia bezpieczeństwa współbieżności, host sprawdza ETag dla każdej aktualizacji dzierżawy. Obsługiwane są także inne strategie punktu kontrolnego.  
3.  Podczas zamykania systemu host zwalnia wszystkie dzierżawy, ale przechowuje informacje kontynuacji, aby go ponownie później odczytu z przechowywanych punktu kontrolnego. 

W tym momencie liczba hostów nie może być większa niż liczba partycji (dzierżawy).

**Konsumenci:** użytkowników lub pracowników, są wątki przetwarzania zmian źródła danych inicjowane przez każdego hosta. Każdy host procesora może mieć wielu klientów. Każdy odbiorca odczytuje zmiany źródła danych z partycji, który jest przypisany do powiadamia jej hosta zmian i ważność dzierżawy.

Aby jeszcze lepiej zrozumieć, jak te cztery elementy procesora źródła danych zmian współdziałać ze sobą, Przyjrzyjmy się przykład na poniższym diagramie. Kolekcja monitorowanych przechowuje dokumenty i używa "Miasto" jako klucza partycji. Widzimy niebieski partycji i tak dalej zawiera dokumenty z polem "Miasto" od "A-E". Istnieją dwa hosty, każda z dwóch konsumentów odczytu z cztery partycje równolegle. Strzałki oznaczają konsumentów czytania z określonego punktu w przypadku zmiany źródła danych. W pierwszej partycji niebieski ciemniejszego reprezentuje nieprzeczytana zmiany podczas koloru niebieskiego reprezentuje już odczytu zmiany w przypadku zmiany źródła danych. Hosty używają kolekcji dzierżawy do przechowywania wartości "kontynuacji", aby śledzić bieżącą pozycję odczytu dla każdego konsumenta. 

![Przy użyciu zmian bazy danych Azure rozwiązania Cosmos źródła danych hosta procesora](./media/change-feed/changefeedprocessornew.png)

### <a name="working-with-the-change-feed-processor-library"></a>Praca z biblioteką zmiany źródła procesora

Przed zainstalowaniem pakietu NuGet procesora źródła danych zmian należy najpierw zainstalować: 

* Microsoft.Azure.DocumentDB, wersja 1.13.1 lub nowszy 
* Newtonsoft.Json, wersja 9.0.1 lub nowszy

Następnie zainstaluj [pakietu Microsoft.Azure.DocumentDB.ChangeFeedProcessor Nuget](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.ChangeFeedProcessor/) i dołączyć go jako odwołanie.

Aby zaimplementować biblioteki zmiany procesora źródła danych, musisz wykonać następujące:

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

To wszystko. Po wykonaniu tych kroków kilka dokumentów rozpocznie się do **DocumentFeedObserver ProcessChangesAsync** metody.

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji dotyczących korzystania z bazy danych Azure rozwiązania Cosmos w środowisku Azure Functions zobacz [bazy danych Azure rozwiązania Cosmos: pliki bazy danych obliczeniowych, przy użyciu usługi Azure Functions](serverless-computing-database.md).

Aby uzyskać więcej informacji na temat używania biblioteki zmiany procesora źródła danych użyj następujących zasobów:

* [Strona informacji o](sql-api-sdk-dotnet-changefeed.md) 
* [Pakiet Nuget](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.ChangeFeedProcessor/)
* [Przykładowy kod, przedstawiający kroki od 1 do 6 powyżej](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/code-samples/ChangeFeedProcessor)
* [Dodatkowe przykłady z witryny GitHub](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/ChangeFeedProcessor)

Aby uzyskać więcej informacji na temat używania zmiany źródła danych za pomocą zestawu SDK Użyj następujących zasobów:

* [Strona informacji o zestawie SDK](sql-api-sdk-dotnet.md)
