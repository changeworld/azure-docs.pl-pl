---
title: Jak zarządzać współbieżne operacje zapisu do zasoby — Usługa Azure Search
description: Użyj optymistycznej współbieżności, aby uniknąć kolizji środku air w aktualizacji lub usuwania indeksów usługi Azure Search, indeksatorów i źródeł danych.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.topic: conceptual
ms.date: 07/21/2017
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 7e569fa30727f2df7411eee5fa6d48f9b9454460
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/02/2019
ms.locfileid: "65025337"
---
# <a name="how-to-manage-concurrency-in-azure-search"></a>Jak zarządzać współbieżnością w usłudze Azure Search

Podczas zarządzania zasobami usługi Azure Search, takich jak indeksy i źródeł danych, należy można zaktualizować zasobów bezpiecznie, zwłaszcza, jeśli zasoby są dostępne współbieżnie przez inne składniki aplikacji. Po dwóch klientów jednocześnie aktualizowanie zasobu bez koordynacji, możliwe są sytuacje wyścigu. Aby tego uniknąć, usługa Azure Search gwarantuje *modelu optymistycznej współbieżności*. Na zasób nie ma żadnych blokad. Zamiast tego jest element ETag zastępuje każdy zasób, który identyfikuje wersję zasobu, tak, aby tworzyć żądania, które pozwalają zapobiec przypadkowemu.

> [!Tip]
> Koncepcyjny kodu w [przykładowe C# rozwiązania](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetETagsExplainer) wyjaśniono, jak działa Kontrola współbieżności w usłudze Azure Search. Ten kod tworzy warunki, które wywołują kontroli współbieżności. Odczytywanie [poniższy fragment kodu](#samplecode) jest prawdopodobnie wystarczający dla większości deweloperów, ale jeśli chcesz uruchomić, edytować pliku appsettings.json, aby dodać nazwę usługi i klucz interfejsu api administratora. Podany adres URL usługi `http://myservice.search.windows.net`, nazwa usługi jest `myservice`.

## <a name="how-it-works"></a>Jak to działa

Oznacza, że współbieżności jest implementowane za pośrednictwem dostępu warunek sprawdza w wywołaniach interfejsu API zapisywania indeksy, indeksatory, źródła danych i zasobów synonymMap.

Wszystkie zasoby mają [ *tag jednostki (element ETag)* ](https://en.wikipedia.org/wiki/HTTP_ETag) zawierające informacje o wersji obiektu. Sprawdzając najpierw element ETag, możesz uniknąć równoczesne aktualizacje w typowym przepływie pracy (pobieranie, zmodyfikować lokalnie, zaktualizuj) przez zapewnienie im Twoja lokalna kopia zasobu element ETag dopasowań.

+ Korzysta z interfejsu API REST [ETag](https://docs.microsoft.com/rest/api/searchservice/common-http-request-and-response-headers-used-in-azure-search) w nagłówku żądania.
+ Zestaw .NET SDK ustawia tag ETag przez obiekt, który accessCondition, ustawienie [If-Match | Nagłówek If-Match — Brak](https://docs.microsoft.com/rest/api/searchservice/common-http-request-and-response-headers-used-in-azure-search) w zasobie. Każdy obiekt dziedziczący po [IResourceWithETag (.NET SDK)](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.iresourcewithetag) ma obiektu accessCondition.

Za każdym razem, gdy zostanie zaktualizowany zasób, jego element ETag zmienia się automatycznie. Podczas implementowania zarządzania współbieżności wszystko, co robimy jest umieszczenie warunku wstępnego na żądanie aktualizacji, która wymaga zdalnego zasobu, aby mieć ten sam element ETag jako kopię zasobu, który został zmodyfikowany na komputerze klienckim. Jeśli proces współbieżnych uległ zmianie zasobu zdalnego już, element ETag nie będą zgodne warunki wstępne i HTTP 412 Niepowodzenie żądania. Jeśli używasz zestawu SDK .NET to manifesty jako `CloudException` gdzie `IsAccessConditionFailed()` rozszerzenia, metoda zwraca wartość true.

> [!Note]
> Istnieje tylko jeden mechanizm współbieżność. Jest zawsze używana, niezależnie od tego, który interfejsu API jest używana w przypadku aktualizacji zasobu.

<a name="samplecode"></a>
## <a name="use-cases-and-sample-code"></a>Przypadki użycia i przykładowy kod

Poniższy kod demonstruje, że accessCondition sprawdza, czy operacje aktualizacji klucza:

+ Jeśli zasób nie istnieje już się nie powieść aktualizacji
+ Niepowodzenie aktualizacji zmiany wersji zasobu

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

## <a name="design-pattern"></a>wzorzec projektowy

Wzorzec projektowania do wdrażania optymistycznej współbieżności powinien zawierać pętli, która ponawia próbę warunki dostępu, sprawdź, test warunki dostępu i opcjonalnie umożliwia pobranie zaktualizowanego zasobu przed podjęciem próby ponownego zastosowania zmian.

Ten fragment kodu ilustruje dodanie synonymMap do indeksu, który już istnieje. Ten kod pochodzi z [synonim C# przykładzie przy użyciu usługi Azure Search](search-synonyms-tutorial-sdk.md).

Fragment kodu pobiera indeks "hotels", sprawdza, czy wersja obiektu w operacji aktualizacji, zgłasza wyjątek, jeśli warunek zakończy się niepowodzeniem, a następnie ponawia operację (maksymalnie trzy razy), począwszy od indeksu pobierania z serwera, aby uzyskać najnowszą wersję.

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

Przegląd [synonimy C# przykładowe](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToSynonyms) kontekstu więcej na temat bezpiecznego aktualizowania istniejącego indeksu.

Wypróbuj jedną z poniższych przykładów w celu uwzględnienia elementów etag lub AccessCondition obiektów modyfikowania.

+ [Przykład interfejsu API REST w witrynie GitHub](https://github.com/Azure-Samples/search-rest-api-getting-started)
+ [Przykładowy zestaw SDK platformy .NET w witrynie GitHub](https://github.com/Azure-Samples/search-dotnet-getting-started). To rozwiązanie zawiera projekt "DotNetEtagsExplainer", zawierający kod przedstawiony w tym artykule.

## <a name="see-also"></a>Zobacz także

[Typowe nagłówki żądania i odpowiedzi HTTP](https://docs.microsoft.com/rest/api/searchservice/common-http-request-and-response-headers-used-in-azure-search)
[kodów stanu HTTP](https://docs.microsoft.com/rest/api/searchservice/http-status-codes)
[indeksu operacji (interfejs API REST)](https://docs.microsoft.com/rest/api/searchservice/index-operations)
