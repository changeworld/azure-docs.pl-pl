---
title: Architektur wielu głównej bazy danych z bazy danych Azure rozwiązania Cosmos | Dokumentacja firmy Microsoft
description: Dowiedz się więcej na temat projektowania architektury aplikacji z lokalnym odczyty i zapisy w różnych regionach geograficznych z bazy danych Azure rozwiązania Cosmos.
services: cosmos-db
documentationcenter: ''
author: SnehaGunda
manager: kfile
ms.assetid: 706ced74-ea67-45dd-a7de-666c3c893687
ms.service: cosmos-db
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/23/2017
ms.author: sngun
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5e8853d521173a9a8d3c925361e43ce469471918
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="multi-master-globally-replicated-database-architectures-with-azure-cosmos-db"></a>Wzorzec wielu globalnie zreplikowany architektury bazy danych z bazy danych Azure rozwiązania Cosmos
Azure DB rozwiązania Cosmos obsługuje gotowe [globalnej replikacji](distribute-data-globally.md), który umożliwia dystrybucję danych do wielu regionów o małych opóźnieniach dostępu w dowolnym miejscu obciążenie. Ten model jest najczęściej używany w przypadku obciążeń wydawcy/konsumenta przypadku zapisywania w jednym regionie geograficznym i czytników globalnie rozproszone w różnych regionach (odczytu). 

Obsługa replikacji globalne DB rozwiązania Cosmos Azure służy również do tworzenia aplikacji, w których autorzy i czytników są globalnie rozproszone. W tym dokumencie przedstawiono wzorzec, która umożliwia uzyskanie lokalnego zapisu i lokalne dostęp do odczytu autorzy rozproszonego przy użyciu bazy danych Azure rozwiązania Cosmos.

## <a id="ExampleScenario"></a>Publikowanie — przykładowy scenariusz zawartości
Przyjrzyjmy się scenariusza rzeczywistych opisano, jak skorzystać z wzorców globalnie rozproszone kilku-region/kilku-główny odczytu zapisu z bazy danych Azure rozwiązania Cosmos. Należy wziąć pod uwagę zawartości platformy publikowania oparte na usłudze Azure DB rozwiązania Cosmos. Poniżej przedstawiono niektóre wymagania, które musi spełniać ta platforma obsługi użytkowników dla konsumentów i wydawców.

* Zarówno twórcy, jak i subskrybentów są rozkładane świata 
* Autorzy należy opublikować artykułów (Zapisz) do ich lokalnej regionu (najbliższego)
* Autorzy mają czytników/subskrybentów swoich artykułów, które są dystrybuowane na całym świecie. 
* Subskrybenci należy uzyskać powiadomienie, gdy nowe artykuły są publikowane.
* Subskrybenci musi mieć możliwość odczytu artykuły z ich lokalnego regionu. Powinny być również możliwość dodawania przeglądami tych artykułów. 
* Każda osoba, która tym autora artykułów powinna być stanie widoku wszystkie przeglądy dołączony do artykułów z lokalnego regionu. 

Zakładając, że miliony konsumentów i wydawców z miliardów artykułów, wkrótce mamy rozwiązanie problemów skali wraz z gwarantujących miejscowości dostępu. Podobnie jak w przypadku większości problemów skalowalności, rozwiązanie znajduje się w skutecznej strategii partycjonowania. Następnie Zobaczmy, jak model artykuły, przejrzyj i powiadomienia jako dokumenty, skonfiguruj konta bazy danych rozwiązania Cosmos Azure i wykonywania Warstwa dostępu do danych. 

Jeśli chcesz dowiedzieć się więcej na temat partycjonowania i kluczy partycji, zobacz [partycjonowania i skalowania w usłudze Azure DB rozwiązania Cosmos](partition-data.md).

## <a id="ModelingNotifications"></a>Modelowanie powiadomienia
Powiadomienia są strumieniowych źródeł danych określonego dla użytkownika. W związku z tym wzorce dostępu do dokumentów powiadomienia są zawsze w ramach jednego użytkownika. Czy na przykład "post powiadomienie do użytkownika" lub "Pobierz wszystkie powiadomienia dla danego użytkownika". Tak, będzie optymalny wybór partycjonowania klucz dla tego typu `UserId`.

    class Notification 
    { 
        // Unique ID for Notification. 
        public string Id { get; set; }

        // The user Id for which notification is addressed to. 
        public string UserId { get; set; }

        // The partition Key for the resource. 
        public string PartitionKey 
        { 
            get 
            { 
                return this.UserId; 
            }
        }

        // Subscription for which this notification is raised. 
        public string SubscriptionFilter { get; set; }

        // Subject of the notification. 
        public string ArticleId { get; set; } 
    }

## <a id="ModelingSubscriptions"></a>Modelowanie subskrypcji
Subskrypcje mogą być tworzone dla różnych kryteriów, takich jak określonej kategorii artykułów odsetek lub określonego wydawcy. Dlatego `SubscriptionFilter` jest dobrym rozwiązaniem dla klucza partycji.

    class Subscriptions 
    { 
        // Unique ID for Subscription 
        public string Id { get; set; }

        // Subscription source. Could be Author | Category etc. 
        public string SubscriptionFilter { get; set; }

        // subscribing User. 
        public string UserId { get; set; }

        public string PartitionKey 
        { 
            get 
            { 
                return this.SubscriptionFilter; 
            } 
        } 
    }

## <a id="ModelingArticles"></a>Artykuły modelowania
Po artykułu jest identyfikowane za pomocą powiadomień, kolejne zapytania są zwykle oparte na `Article.Id`. Wybieranie `Article.Id` jako partycja klucz w związku z tym zapewnia najlepszą dystrybucji do przechowywania artykułów w kolekcji usługi Azure DB rozwiązania Cosmos. 

    class Article 
    { 
        // Unique ID for Article 
        public string Id { get; set; }
        
        public string PartitionKey 
        { 
            get 
            { 
                return this.Id; 
            } 
        }
        
        // Author of the article
        public string Author { get; set; }

        // Category/genre of the article
        public string Category { get; set; }

        // Tags associated with the article
        public string[] Tags { get; set; }

        // Title of the article
        public string Title { get; set; }
        
        //... 
    }

## <a id="ModelingReviews"></a>Przegląda modelowania
Podobne artykuły recenzje przede wszystkim są zapisywane i do odczytu w kontekście artykułu. Wybieranie `ArticleId` jako partycja klucz zapewnia najlepsze dystrybucji i wydajny dostęp przeglądów skojarzone z artykułu. 

    class Review 
    { 
        // Unique ID for Review 
        public string Id { get; set; }

        // Article Id of the review 
        public string ArticleId { get; set; }

        public string PartitionKey 
        { 
            get 
            { 
                return this.ArticleId; 
            } 
        }
        
        //Reviewer Id 
        public string UserId { get; set; }
        public string ReviewText { get; set; }
        
        public int Rating { get; set; } }
    }

## <a id="DataAccessMethods"></a>Metody warstwy dostępu do danych
Teraz Przyjrzyjmy się główne dane należy implementować metody dostępu. Oto lista metod który `ContentPublishDatabase` musi:

    class ContentPublishDatabase 
    { 
        public async Task CreateSubscriptionAsync(string userId, string category);
    
        public async Task<IEnumerable<Notification>> ReadNotificationFeedAsync(string userId);
    
        public async Task<Article> ReadArticleAsync(string articleId);
    
        public async Task WriteReviewAsync(string articleId, string userId, string reviewText, int rating);
    
        public async Task<IEnumerable<Review>> ReadReviewsAsync(string articleId); 
    }

## <a id="Architecture"></a>Konfiguracja konta w usłudze Azure DB rozwiązania Cosmos
Zagwarantowanie lokalnego odczytuje i zapisuje, firma Microsoft musi partycji danych nie tylko na partycji klucza, ale także na podstawie wzorca dostępu geograficzne w regionach. Model zależy od tego, mając konto bazy danych Azure DB rozwiązania Cosmos replikacją geograficzną dla każdego regionu. Na przykład z dwóch regionach, w tym miejscu jest konfiguracji w przypadku zapisów:

| Nazwa konta | Region zapisu | Region odczytu |
| --- | --- | --- |
| `contentpubdatabase-usa.documents.azure.com` | `West US` |`North Europe` |
| `contentpubdatabase-europe.documents.azure.com` | `North Europe` |`West US` |

Na poniższym diagramie przedstawiono, jak odczyty i zapisy są wykonywane w typowej aplikacji z tej konfiguracji:

![Azure architektura wieloma serwerami głównymi DB rozwiązania Cosmos](./media/multi-region-writers/multi-master.png)

Oto fragment kodu będący jak zainicjować klientów w DAL, uruchomionych w `West US` regionu.
    
    ConnectionPolicy writeClientPolicy = new ConnectionPolicy { ConnectionMode = ConnectionMode.Direct, ConnectionProtocol = Protocol.Tcp };
    writeClientPolicy.PreferredLocations.Add(LocationNames.WestUS);
    writeClientPolicy.PreferredLocations.Add(LocationNames.NorthEurope);

    DocumentClient writeClient = new DocumentClient(
        new Uri("https://contentpubdatabase-usa.documents.azure.com"), 
        writeRegionAuthKey,
        writeClientPolicy);

    ConnectionPolicy readClientPolicy = new ConnectionPolicy { ConnectionMode = ConnectionMode.Direct, ConnectionProtocol = Protocol.Tcp };
    readClientPolicy.PreferredLocations.Add(LocationNames.NorthEurope);
    readClientPolicy.PreferredLocations.Add(LocationNames.WestUS);

    DocumentClient readClient = new DocumentClient(
        new Uri("https://contentpubdatabase-europe.documents.azure.com"),
        readRegionAuthKey,
        readClientPolicy);

Z poprzedniej instalacji Warstwa dostępu do danych można przekazywać wszystkich zapisów do lokalnego konta, na którym jest wdrażany podstawie. Odczyty są wykonywane przez odczyt z oba konta, aby pobrać globalne widoku danych. Ta metoda może zostać rozszerzony do w wielu regionach, zgodnie z wymaganiami. Na przykład poniżej przedstawiono ustawienia z trzech regionów geograficznych:

| Nazwa konta | Region zapisu | Region odczytu 1 | Region odczytu 2 |
| --- | --- | --- | --- |
| `contentpubdatabase-usa.documents.azure.com` | `West US` |`North Europe` |`Southeast Asia` |
| `contentpubdatabase-europe.documents.azure.com` | `North Europe` |`West US` |`Southeast Asia` |
| `contentpubdatabase-asia.documents.azure.com` | `Southeast Asia` |`North Europe` |`West US` |

## <a id="DataAccessImplementation"></a>Implementacja warstwy dostępu do danych
Teraz Przyjrzyjmy się implementacja Warstwa dostępu do danych (DAL) dla aplikacji z dwóch regionach zapisywalny. Warstwa DAL musi implementować następujące czynności:

* Tworzenie wielu wystąpień `DocumentClient` dla poszczególnych kont. Z dwóch regionach, każde wystąpienie warstwy DAL ma jeden `writeClient` i jeden `readClient`. 
* Oparte na region wdrożonej aplikacji, skonfiguruj punkty końcowe dla `writeclient` i `readClient`. Na przykład warstwy DAL wdrożone w `West US` używa `contentpubdatabase-usa.documents.azure.com` do wykonywania operacji zapisu. Warstwa DAL wdrożone w `NorthEurope` używa `contentpubdatabase-europ.documents.azure.com` dla operacji zapisu.

Można zaimplementować metody dostępu do danych z poprzedniej instalacji. Zapis operacji przekazywania zapisu w odpowiedniej `writeClient`.

    public async Task CreateSubscriptionAsync(string userId, string category)
    {
        await this.writeClient.CreateDocumentAsync(this.contentCollection, new Subscriptions
        {
            UserId = userId,
            SubscriptionFilter = category
        });
    }

    public async Task WriteReviewAsync(string articleId, string userId, string reviewText, int rating)
    {
        await this.writeClient.CreateDocumentAsync(this.contentCollection, new Review
        {
            UserId = userId,
            ArticleId = articleId,
            ReviewText = reviewText,
            Rating = rating
        });
    }

Do odczytywania powiadomienia i recenzji, musi przeczytanie z regiony i Unii wyniki pokazane na następujący fragment kodu:

    public async Task<IEnumerable<Notification>> ReadNotificationFeedAsync(string userId)
    {
        IDocumentQuery<Notification> writeAccountNotification = (
            from notification in this.writeClient.CreateDocumentQuery<Notification>(this.contentCollection) 
            where notification.UserId == userId 
            select notification).AsDocumentQuery();
        
        IDocumentQuery<Notification> readAccountNotification = (
            from notification in this.readClient.CreateDocumentQuery<Notification>(this.contentCollection) 
            where notification.UserId == userId 
            select notification).AsDocumentQuery();

        List<Notification> notifications = new List<Notification>();

        while (writeAccountNotification.HasMoreResults || readAccountNotification.HasMoreResults)
        {
            IList<Task<FeedResponse<Notification>>> results = new List<Task<FeedResponse<Notification>>>();

            if (writeAccountNotification.HasMoreResults)
            {
                results.Add(writeAccountNotification.ExecuteNextAsync<Notification>());
            }

            if (readAccountNotification.HasMoreResults)
            {
                results.Add(readAccountNotification.ExecuteNextAsync<Notification>());
            }

            IList<FeedResponse<Notification>> notificationFeedResult = await Task.WhenAll(results);

            foreach (FeedResponse<Notification> feed in notificationFeedResult)
            {
                notifications.AddRange(feed);
            }
        }
        return notifications;
    }

    public async Task<IEnumerable<Review>> ReadReviewsAsync(string articleId)
    {
        IDocumentQuery<Review> writeAccountReviews = (
            from review in this.writeClient.CreateDocumentQuery<Review>(this.contentCollection) 
            where review.ArticleId == articleId 
            select review).AsDocumentQuery();
        
        IDocumentQuery<Review> readAccountReviews = (
            from review in this.readClient.CreateDocumentQuery<Review>(this.contentCollection) 
            where review.ArticleId == articleId 
            select review).AsDocumentQuery();

        List<Review> reviews = new List<Review>();
        
        while (writeAccountReviews.HasMoreResults || readAccountReviews.HasMoreResults)
        {
            IList<Task<FeedResponse<Review>>> results = new List<Task<FeedResponse<Review>>>();

            if (writeAccountReviews.HasMoreResults)
            {
                results.Add(writeAccountReviews.ExecuteNextAsync<Review>());
            }

            if (readAccountReviews.HasMoreResults)
            {
                results.Add(readAccountReviews.ExecuteNextAsync<Review>());
            }

            IList<FeedResponse<Review>> notificationFeedResult = await Task.WhenAll(results);

            foreach (FeedResponse<Review> feed in notificationFeedResult)
            {
                reviews.AddRange(feed);
            }
        }

        return reviews;
    }

W związku z tym, wybierając odpowiednim kluczem partycjonowania i partycjonowania statycznego na podstawie konta, można osiągnąć w przypadku lokalnego zapisy i odczyty przy użyciu bazy danych Azure rozwiązania Cosmos.

## <a id="NextSteps"></a>Następne kroki
W tym artykule opisano możemy wykorzystania wzorce globalnie rozproszone w przypadku zapisu odczytu z bazy danych rozwiązania Cosmos Azure przy użyciu publikowania zawartości jako przykładowy scenariusz.

* Dowiedz się więcej o sposobie obsługi bazy danych Azure rozwiązania Cosmos [dystrybucji globalne](distribute-data-globally.md)
* Dowiedz się więcej o [automatycznej i ręcznej pracy awaryjnej w usłudze Azure DB rozwiązania Cosmos](regional-failover.md)
* Dowiedz się więcej o [globalne spójności z bazy danych Azure rozwiązania Cosmos](consistency-levels.md)
* Tworzenie z wielu regionach, przy użyciu [Azure DB rozwiązania Cosmos - interfejsu API SQL](tutorial-global-distribution-sql-api.md)
* Tworzenie z wielu regionach, przy użyciu [Azure DB rozwiązania Cosmos - API bazy danych MongoDB](tutorial-global-distribution-MongoDB.md)
* Tworzenie z wielu regionach, przy użyciu [Azure DB rozwiązania Cosmos - API tabeli](tutorial-global-distribution-table.md)
