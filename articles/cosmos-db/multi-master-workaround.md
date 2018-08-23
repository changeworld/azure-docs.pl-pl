---
title: Wykonaj operacje zapisu w wielu regionach i operacji odczytu przez wybieranie odpowiedniego klucza partycji | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o projektowaniu architektury aplikacji za pomocą lokalnego odczyty i zapisy w wielu regionach geograficznych, usługa Azure Cosmos DB, wybierając klucza partycji.
services: cosmos-db
author: rimman
manager: kfile
ms.service: cosmos-db
ms.devlang: multiple
ms.topic: conceptual
ms.date: 06/6/2018
ms.author: rimman
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 3d38b7cd7d1f28f706e94782602926abc8fc11e3
ms.sourcegitcommit: 387d7edd387a478db181ca639db8a8e43d0d75f7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/10/2018
ms.locfileid: "42059128"
---
# <a name="perform-multi-region-writes-and-reads-by-choosing-the-right-partitioning-key"></a>Wykonaj operacje zapisu w wielu regionach i operacji odczytu przez wybieranie odpowiedniego klucza partycji

Usługa Azure Cosmos DB obsługuje gotowe do pracy [globalnej replikacji](distribute-data-globally.md), co pozwala na dystrybuowania danych do wielu regionów za pomocą dostępie z małymi opóźnieniami obciążenie w dowolnym miejscu. Ten model jest często używane do wydawcy i odbiorcy obciążeń w przypadku, gdy istnieje zapisywania w jednym regionie geograficznym i globalnie dystrybuowane czytelnicy w innych regionach (odczytu). 

Umożliwia także obsługę globalnej replikacji usługi Azure Cosmos DB do tworzenia aplikacji, w których autorzy i odbiorcy są globalnie rozproszone. W tym dokumencie przedstawiono wzorzec, która umożliwia osiągnięcie zapisu lokalnych i lokalny dostęp do odczytu dla rozproszonego modułów zapisujących, za pomocą usługi Azure Cosmos DB.

## <a id="ExampleScenario"></a>Publikowanie zawartości — przykładowy scenariusz
Spójrzmy na rzeczywistych Scenariusz opisujący, jak używać wzorców globalnie dystrybuowane wielu-region/wielu — główny odczytu zapisu z usługi Azure Cosmos DB. Należy wziąć pod uwagę zawartości platformy publikowania oparte na usłudze Azure Cosmos DB. Poniżej przedstawiono niektóre wymagania, które ta platforma musi spełniać świetnego środowiska użytkownika dla wydawcy i konsumentów.

* Autorzy i subskrybentów zostały rozmieszczone na cały świat 
* Autorzy musi opublikować artykułów (zapis) w ich lokalnych regionów (najbliższego)
* Autorzy mają czytelnicy/abonentów swoich artykułów są dystrybuowane na całym świecie. 
* Subskrybenci powinien otrzymaj powiadomienie, gdy nowe artykuły są publikowane.
* Subskrybenci musi mieć możliwość zapoznaj się z artykułami z ich lokalnych regionów. Również powinno być możliwe dodanie recenzji do tych artykułów. 
* Każda osoba tym autora artykułów powinny być możliwe Wyświetl wszystkie przeglądy dołączone do artykułów z regionu lokalnego. 

Miliony konsumentów i wydawców z miliardami artykułów, zakładając, że wkrótce mamy rozwiązanie problemów są skalowane wraz z gwarantujących miejscowość dostępu. Podobnie jak w przypadku większości problemy ze skalowalnością, rozwiązanie znajduje się w strategii partycjonowania. Następnie Przyjrzyjmy się jak model artykułów, przejrzyj i powiadomień w postaci dokumentów, należy skonfigurować konta usługi Azure Cosmos DB i wykonywania warstwy dostępu do danych. 

Jeśli chcesz dowiedzieć się więcej na temat partycjonowania i klucze partycji, zobacz [partycjonowanie i skalowanie w usłudze Azure Cosmos DB](partition-data.md).

## <a id="ModelingNotifications"></a>Modelowanie powiadomienia
Powiadomienia są określone dla użytkownika źródeł danych. W związku z tym wzorce dostępu dla dokumentów powiadomienia są zawsze w ramach pojedynczego użytkownika. Czy na przykład "post powiadomienie do użytkownika" lub "Pobierz wszystkie powiadomienia dla danego użytkownika". Tak, będzie optymalnym wyborem partycjonowania klucza dla tego typu `UserId`.

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
Subskrypcje mogą być tworzone dla różnych kryteriów, takich jak określonej kategorii artykułów odsetek lub określonego wydawcy. Dlatego `SubscriptionFilter` jest dobrym wyborem dla klucza partycji.

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
Po artykułu jest identyfikowany poprzez powiadomienia, kolejne zapytań są zwykle oparte na `Article.Id`. Wybieranie `Article.Id` jako partycja klucz ten sposób zapewnia najlepsze dystrybucji do przechowywania artykułów w kolekcji usługi Azure Cosmos DB. 

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

## <a id="ModelingReviews"></a>Przeglądy modelowania
Podobne artykuły przeglądy przede wszystkim są zapisywane i odczytu w kontekście artykułu. Wybieranie `ArticleId` jako partycja klucz zapewnia najlepsze dystrybucji i skutecznego dostępu przeglądy skojarzone z artykułu. 

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

## <a id="DataAccessMethods"></a>Metod warstwy dostępu do danych
Teraz Przyjrzyjmy się dane główne metody dostępu, należy zaimplementować. Poniżej przedstawiono listę metod, `ContentPublishDatabase` musi:

    class ContentPublishDatabase 
    { 
        public async Task CreateSubscriptionAsync(string userId, string category);
    
        public async Task<IEnumerable<Notification>> ReadNotificationFeedAsync(string userId);
    
        public async Task<Article> ReadArticleAsync(string articleId);
    
        public async Task WriteReviewAsync(string articleId, string userId, string reviewText, int rating);
    
        public async Task<IEnumerable<Review>> ReadReviewsAsync(string articleId); 
    }

## <a id="Architecture"></a>Konfiguracja konta usługi Azure Cosmos DB
Zagwarantowanie lokalne odczytuje i zapisuje, możemy tworzyć partycje dane nie tylko na partycji klucza, ale również na podstawie wzorca dostępu geograficznych do regionów. Model opiera się na posiadanie replikacji geograficznej usługi Azure Cosmos DB konto bazy danych dla każdego regionu. Na przykład za pomocą dwóch regionach poniżej przedstawiono ustawienia do zapisu w wielu regionach:

| Nazwa konta | Regionu zapisu | Region odczytu |
| --- | --- | --- |
| `contentpubdatabase-usa.documents.azure.com` | `West US` |`North Europe` |
| `contentpubdatabase-europe.documents.azure.com` | `North Europe` |`West US` |

Na poniższym diagramie przedstawiono, jak odczyty i zapisy są wykonywane w typowej aplikacji za pomocą tego Instalatora:

![Architektura usługi Azure Cosmos DB wielu wzorców](./media/multi-master-workaround/multi-master.png)

Poniżej przedstawiono fragment kodu będący sposób inicjowania klientami w DAL, działające w `West US` regionu.
    
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

W przypadku poprzedniej konfiguracji warstwy dostępu do danych może przekazywać wszystkie operacje zapisu do konta lokalnego, w którym jest wdrażany w oparciu. Operacje odczytu są wykonywane przez odczyt z obu kont można pobrać globalny widok danych. To podejście można rozszerzyć do dowolnej liczby regionów, zgodnie z wymaganiami. Na przykład poniżej przedstawiono ustawienia z trzech regionach geograficznych:

| Nazwa konta | Regionu zapisu | Region odczytu 1 | Region odczytu 2 |
| --- | --- | --- | --- |
| `contentpubdatabase-usa.documents.azure.com` | `West US` |`North Europe` |`Southeast Asia` |
| `contentpubdatabase-europe.documents.azure.com` | `North Europe` |`West US` |`Southeast Asia` |
| `contentpubdatabase-asia.documents.azure.com` | `Southeast Asia` |`North Europe` |`West US` |

## <a id="DataAccessImplementation"></a>Implementacja warstwy dostępu do danych
Teraz Przyjrzyjmy się stosowania warstwy dostępu do danych (DAL) dla aplikacji z dwoma regionami zapisu. Warstwy DAL należy zaimplementować następujące czynności:

* Tworzenie wielu wystąpień `DocumentClient` dla każdego konta. Za pomocą dwóch regionach, każde wystąpienie warstwy DAL ma jeden `writeClient` i jeden `readClient`. 
* Oparte na wdrożonym regionie aplikacji, skonfigurować punkty końcowe dla `writeclient` i `readClient`. Na przykład warstwy DAL wdrożone w `West US` używa `contentpubdatabase-usa.documents.azure.com` do wykonywania operacji zapisu. Warstwy DAL wdrożone w `NorthEurope` używa `contentpubdatabase-europ.documents.azure.com` do zapisu.

W przypadku poprzedniej konfiguracji można zaimplementować metod dostępu do danych. Zapis operacji przekazywania zapisu do odpowiednich `writeClient`.

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

Odczytywanie powiadomień i przeglądy, możesz musi odczytać zarówno z regionów i Unii wyników jak pokazano w poniższym fragmencie kodu:

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

W związku z tym wybierając dobry klucz partycji i partycjonowania statycznego oparte na koncie, można osiągnąć zapisu lokalnie w wielu regionach i operacji odczytu przy użyciu usługi Azure Cosmos DB.

## <a id="NextSteps"></a>Następne kroki
W tym artykule opisano firma Microsoft, jak używać wzorców globalnie dystrybuowane wielu regionów odczytu zapisu. z usługi Azure Cosmos DB przy użyciu publikowania zawartości jako przykładowy scenariusz.

* Dowiedz się więcej o sposobie obsługi usługi Azure Cosmos DB [dystrybucja globalna](distribute-data-globally.md)
* Dowiedz się więcej o [automatycznego i ręcznego trybu failover w usłudze Azure Cosmos DB](regional-failover.md)
* Dowiedz się więcej o [globalnego spójności za pomocą usługi Azure Cosmos DB](consistency-levels.md)
* Programowanie z użyciem wielu regionów za pomocą [Azure Cosmos DB — interfejs API SQL](tutorial-global-distribution-sql-api.md)
* Programowanie z użyciem wielu regionów za pomocą [Azure Cosmos DB — interfejs API usługi MongoDB](tutorial-global-distribution-MongoDB.md)
* Programowanie z użyciem wielu regionów za pomocą [Azure Cosmos DB — interfejs API tabel](tutorial-global-distribution-table.md)
