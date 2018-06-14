---
title: Jak zarządzać równoczesnych zapisów do zasobów w usłudze Azure Search
description: Aby uniknąć kolizji pośredniej lotniczego w aktualizacji lub usuwania indeksów usługi Azure Search, indeksatorów i źródeł danych, należy użyć optymistycznej współbieżności.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.topic: conceptual
ms.date: 07/21/2017
ms.author: heidist
ms.openlocfilehash: f5fa495c1266c847cabc0eb4e35b85132550bc3c
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2018
ms.locfileid: "31796384"
---
# <a name="how-to-manage-concurrency-in-azure-search"></a>Jak zarządzać współbieżność w usłudze Azure Search

Podczas zarządzania zasobami Azure Search, takich jak indeksy i źródeł danych, ważne jest można zaktualizować zasobów bezpiecznie, szczególnie w przypadku, gdy zasoby są dostępne jednocześnie różnych składników aplikacji. Po dwóch klientów jednocześnie aktualizować zasobu bez koordynacji, wyścigu są możliwe. Aby tego uniknąć, usługi Azure Search udostępnia *modelu optymistycznej współbieżności*. Na zasób nie ma żadnych blokad. Zamiast tego jest tag ETag dla spowoduje zastąpienie wszystkich zasobów, który identyfikuje wersję zasobów, dzięki czemu można sformułować żądań, które uniknąć przypadkowego.

> [!Tip]
> Kod koncepcyjny w [przykładowe rozwiązanie C#](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetETagsExplainer) wyjaśniono sposób działania sterowania współbieżnością w usłudze Azure Search. Kod tworzy warunki, które wywołują formant współbieżności. Odczytywanie [poniższy fragment kodu](#samplecode) jest prawdopodobnie wystarczający dla deweloperów większości, ale jeśli chcesz uruchomić, edytować appsettings.json, aby dodać nazwę usługi i klucz interfejsu api administratora. Podany adres URL usługi `http://myservice.search.windows.net`, nazwa usługi jest `myservice`.

## <a name="how-it-works"></a>Jak to działa

Oznacza, że współbieżności jest implementowane za pomocą dostępu warunek sprawdza w wywołaniach interfejsu API zapisywania indeksów, indeksatorów źródeł danych i zasobów synonymMap. 

Wszystkie zasoby mają [ *tag jednostki (ETag)* ](https://en.wikipedia.org/wiki/HTTP_ETag) udostępniająca informacje o wersji obiektu. Sprawdzając tag ETag najpierw, można uniknąć równoczesnych aktualizacji w typowym przepływie pracy (get, zmodyfikuj lokalnie, zaktualizować) przez zapewnienie im dopasowań ETag zasobu kopii lokalnej. 

+ Używa interfejsu API REST [ETag](https://docs.microsoft.com/rest/api/searchservice/common-http-request-and-response-headers-used-in-azure-search) w nagłówku żądania.
+ Zestaw .NET SDK ustawia tag ETag za pośrednictwem obiektu accessCondition, ustawienie [If-Match | Nagłówek If-Match — Brak](https://docs.microsoft.com/rest/api/searchservice/common-http-request-and-response-headers-used-in-azure-search) zasobu. Dowolny obiekt dziedziczących [IResourceWithETag (.NET SDK)](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.iresourcewithetag) ma obiekt accessCondition.

Za każdym razem, gdy zasób, powoduje automatyczną zmianę jego ETag. Podczas implementowania współbieżności zarządzania wszystkie są wykonywane jest umieszczenie warunku wstępnego na żądanie aktualizacji, które wymaga zasób zdalny ma tego samego ETag jako kopię zasobu, który zmodyfikował na kliencie. Zmiana równoczesnych procesu zdalnego zasobu już element ETag nie będzie odpowiadała warunki wstępne i HTTP 412 Niepowodzenie żądania. Jeśli używasz zestawu .NET SDK to manifesty jako `CloudException` gdzie `IsAccessConditionFailed()` rozszerzenia metoda zwraca wartość true.

> [!Note]
> Istnieje tylko jeden mechanizm współbieżności. Jest on używany zawsze niezależnie od tego, które interfejs API jest używany dla aktualizacji zasobów. 

<a name="samplecode"></a>
## <a name="use-cases-and-sample-code"></a>Przypadki użycia i przykładowy kod

Poniższy kod ilustruje, że accessCondition sprawdza, czy operacje aktualizacji klucza:

+ Niepowodzenie aktualizacji, jeśli zasób już istnieje
+ Niepowodzenie aktualizacji, w przypadku zmiany wersji zasobów

### <a name="sample-code-from-dotnetetagsexplainer-programhttpsgithubcomazure-samplessearch-dotnet-getting-startedtreemasterdotnetetagsexplainer"></a>Przykładowy kod z [DotNetETagsExplainer programu](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetETagsExplainer)

```
    class Program
    {
        // This sample shows how ETags work by performing conditional updates and deletes
        // on an Azure Search index.
        static void Main(string[] args)
        {
            IConfigurationBuilder builder = new ConfigurationBuilder().AddJsonFile("appsettings.json");
            IConfigurationRoot configuration = builder.Build();

            SearchServiceClient serviceClient = CreateSearchServiceClient(configuration);

            Console.WriteLine("Deleting index...\n");
            DeleteTestIndexIfExists(serviceClient);

            // Every top-level resource in Azure Search has an associated ETag that keeps track of which version
            // of the resource you're working on. When you first create a resource such as an index, its ETag is
            // empty.
            Index index = DefineTestIndex();
            Console.WriteLine(
                $"Test index hasn't been created yet, so its ETag should be blank. ETag: '{index.ETag}'");

            // Once the resource exists in Azure Search, its ETag will be populated. Make sure to use the object
            // returned by the SearchServiceClient! Otherwise, you will still have the old object with the
            // blank ETag.
            Console.WriteLine("Creating index...\n");
            index = serviceClient.Indexes.Create(index);
            
            Console.WriteLine($"Test index created; Its ETag should be populated. ETag: '{index.ETag}'");

            // ETags let you do some useful things you couldn't do otherwise. For example, by using an If-Match
            // condition, we can update an index using CreateOrUpdate and be guaranteed that the update will only
            // succeed if the index already exists.
            index.Fields.Add(new Field("name", AnalyzerName.EnMicrosoft));
            index =
                serviceClient.Indexes.CreateOrUpdate(
                    index,
                    accessCondition: AccessCondition.GenerateIfExistsCondition());

            Console.WriteLine(
                $"Test index updated; Its ETag should have changed since it was created. ETag: '{index.ETag}'");

            // More importantly, ETags protect you from concurrent updates to the same resource. If another
            // client tries to update the resource, it will fail as long as all clients are using the right
            // access conditions.
            Index indexForClient1 = index;
            Index indexForClient2 = serviceClient.Indexes.Get("test");

            Console.WriteLine("Simulating concurrent update. To start, both clients see the same ETag.");
            Console.WriteLine($"Client 1 ETag: '{indexForClient1.ETag}' Client 2 ETag: '{indexForClient2.ETag}'");

            // Client 1 successfully updates the index.
            indexForClient1.Fields.Add(new Field("a", DataType.Int32));
            indexForClient1 =
                serviceClient.Indexes.CreateOrUpdate(
                    indexForClient1,
                    accessCondition: AccessCondition.IfNotChanged(indexForClient1));

            Console.WriteLine($"Test index updated by client 1; ETag: '{indexForClient1.ETag}'");

            // Client 2 tries to update the index, but fails, thanks to the ETag check.
            try
            {
                indexForClient2.Fields.Add(new Field("b", DataType.Boolean));
                serviceClient.Indexes.CreateOrUpdate(
                    indexForClient2, 
                    accessCondition: AccessCondition.IfNotChanged(indexForClient2));

                Console.WriteLine("Whoops; This shouldn't happen");
                Environment.Exit(1);
            }
            catch (CloudException e) when (e.IsAccessConditionFailed())
            {
                Console.WriteLine("Client 2 failed to update the index, as expected.");
            }

            // You can also use access conditions with Delete operations. For example, you can implement an
            // atomic version of the DeleteTestIndexIfExists method from this sample like this:
            Console.WriteLine("Deleting index...\n");
            serviceClient.Indexes.Delete("test", accessCondition: AccessCondition.GenerateIfExistsCondition());

            // This is slightly better than using the Exists method since it makes only one round trip to
            // Azure Search instead of potentially two. It also avoids an extra Delete request in cases where
            // the resource is deleted concurrently, but this doesn't matter much since resource deletion in
            // Azure Search is idempotent.

            // And we're done! Bye!
            Console.WriteLine("Complete.  Press any key to end application...\n");
            Console.ReadKey();
        }

        private static SearchServiceClient CreateSearchServiceClient(IConfigurationRoot configuration)
        {
            string searchServiceName = configuration["SearchServiceName"];
            string adminApiKey = configuration["SearchServiceAdminApiKey"];

            SearchServiceClient serviceClient =
                new SearchServiceClient(searchServiceName, new SearchCredentials(adminApiKey));
            return serviceClient;
        }

        private static void DeleteTestIndexIfExists(SearchServiceClient serviceClient)
        {
            if (serviceClient.Indexes.Exists("test"))
            {
                serviceClient.Indexes.Delete("test");
            }
        }

        private static Index DefineTestIndex() =>
            new Index()
            {
                Name = "test",
                Fields = new[] { new Field("id", DataType.String) { IsKey = true } }
            };
    }
}
```

## <a name="design-pattern"></a>Wzorzec projektowy

Wzorzec projektowania wykonywania optymistycznej współbieżności powinien zawierać pętli ponownych prób warunki dostępu Sprawdź test na warunki dostępu i opcjonalnie pobiera zaktualizowanego zasobu przed podjęciem próby ponownego zastosowania zmian. 

Następujący fragment kodu przedstawia dodanie synonymMap do indeksu, która już istnieje. Ten kod jest z [samouczek synonim (wersja zapoznawcza) C# dla usługi Azure Search](https://docs.microsoft.com/azure/search/search-synonyms-tutorial-sdk). 

Pobiera indeks "hotels" fragment kodu, sprawdza wersję obiektu w przypadku operacji aktualizacji, zgłasza wyjątek, jeśli warunek nie powiedzie się, a następnie ponawia operację (maksymalnie trzy razy), zaczynając od indeksu pobierania z serwerem w celu pobrania najnowszej wersji.

        private static void EnableSynonymsInHotelsIndexSafely(SearchServiceClient serviceClient)
        {
            int MaxNumTries = 3;

            for (int i = 0; i < MaxNumTries; ++i)
            {
                try
                {
                    Index index = serviceClient.Indexes.Get("hotels");
                    index = AddSynonymMapsToFields(index);

                    // The IfNotChanged condition ensures that the index is updated only if the ETags match.
                    serviceClient.Indexes.CreateOrUpdate(index, accessCondition: AccessCondition.IfNotChanged(index));

                    Console.WriteLine("Updated the index successfully.\n");
                    break;
                }
                catch (CloudException e) when (e.IsAccessConditionFailed())
                {
                    Console.WriteLine($"Index update failed : {e.Message}. Attempt({i}/{MaxNumTries}).\n");
                }
            }
        }
        
        private static Index AddSynonymMapsToFields(Index index)
        {
            index.Fields.First(f => f.Name == "category").SynonymMaps = new[] { "desc-synonymmap" };
            index.Fields.First(f => f.Name == "tags").SynonymMaps = new[] { "desc-synonymmap" };
            return index;
        }


## <a name="next-steps"></a>Kolejne kroki

Przegląd [próbki synonimy C#](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToSynonyms) dla kontekstu więcej na temat bezpiecznego aktualizowania istniejący indeks.

Spróbuj zmodyfikować następujące przykłady do dołączenia elementy etag lub AccessCondition obiektów.

+ [Przykładowy interfejs API REST w usłudze Github](https://github.com/Azure-Samples/search-rest-api-getting-started) 
+ [Przykład zestawu SDK .NET w witrynie Github](https://github.com/Azure-Samples/search-dotnet-getting-started). To rozwiązanie zawiera projekt "DotNetEtagsExplainer" zawierający kod przedstawiony w tym artykule.

## <a name="see-also"></a>Zobacz także

  [Typowe nagłówki żądań i odpowiedzi HTTP](https://docs.microsoft.com/rest/api/searchservice/common-http-request-and-response-headers-used-in-azure-search)    
  [Kody stanu HTTP](https://docs.microsoft.com/rest/api/searchservice/http-status-codes) [indeksu operacji (interfejsu API REST)](https://docs.microsoft.com/\rest/api/searchservice/index-operations)