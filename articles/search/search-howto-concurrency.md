---
title: Jak zarządzać równoczesnymi zapisami w zasobach
titleSuffix: Azure Cognitive Search
description: Użyj optymistycznej współbieżności, aby uniknąć kolizji w powietrzu na aktualizacjach lub usuwa do indeksów usługi Azure Cognitive Search, indeksatorów, źródeł danych.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: edfb2fe5cc37a00335ca7b5be851a88825b03eb1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72792214"
---
# <a name="how-to-manage-concurrency-in-azure-cognitive-search"></a>Jak zarządzać współbieżnością w usłudze Azure Cognitive Search

Podczas zarządzania zasobami usługi Azure Cognitive Search, takimi jak indeksy i źródła danych, ważne jest bezpieczne aktualizowanie zasobów, zwłaszcza jeśli zasoby są dostępne jednocześnie przez różne składniki aplikacji. Gdy dwóch klientów jednocześnie aktualizować zasób bez koordynacji, warunki wyścigu są możliwe. Aby temu zapobiec, usługa Azure Cognitive Search oferuje *optymistyczny model współbieżności.* Nie ma żadnych blokad na zasobie. Zamiast tego istnieje ETag dla każdego zasobu, który identyfikuje wersję zasobu, dzięki czemu można tworzyć żądania, które unikają przypadkowych zastąpień.

> [!Tip]
> Kod koncepcyjny w [przykładowym rozwiązaniu języka C#](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetETagsExplainer) wyjaśnia, jak działa kontrola współbieżności w usłudze Azure Cognitive Search. Kod tworzy warunki, które wywołują kontrolę współbieżności. Odczytanie [fragmentu kodu poniżej](#samplecode) jest prawdopodobnie wystarczające dla większości deweloperów, ale jeśli chcesz go uruchomić, edytuj appsettings.json, aby dodać nazwę usługi i klucz api administratora. Biorąc pod uwagę `http://myservice.search.windows.net`adres URL `myservice`usługi , nazwa usługi jest .

## <a name="how-it-works"></a>Jak to działa

Optymistyczna współbieżność jest implementowana za pomocą kontroli warunków dostępu w wywołaniach interfejsu API do zapisywania indeksów, indeksatorów, źródeł danych i zasobów synonymMap.

Wszystkie zasoby mają [*tag jednostki (ETag),*](https://en.wikipedia.org/wiki/HTTP_ETag) który zawiera informacje o wersji obiektu. Sprawdzając najpierw etag, można uniknąć równoczesnych aktualizacji w typowym przepływie pracy (get, modify locally, update), upewniając się, że etag zasobu jest zgodny z lokalną kopią.

+ Interfejs API REST używa [znacznika ETag](https://docs.microsoft.com/rest/api/searchservice/common-http-request-and-response-headers-used-in-azure-search) w nagłówku żądania.
+ Zestaw SDK .NET ustawia znacznik ETag za pomocą obiektu accessCondition, ustawiając [if-match | If-Match-None nagłówka](https://docs.microsoft.com/rest/api/searchservice/common-http-request-and-response-headers-used-in-azure-search) w zasobie. Każdy obiekt dziedziczący z [IResourceWithETag (NET SDK)](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.iresourcewithetag) ma obiekt accessCondition.

Za każdym razem, gdy aktualizujesz zasób, jego etag zmienia się automatycznie. Po zaimplementowanie zarządzania współbieżnością, wszystko, co robisz, to wprowadzenie warunku wstępnego na żądanie aktualizacji, które wymaga zasobu zdalnego mieć ten sam ETag jako kopię zasobu, który został zmodyfikowany na kliencie. Jeśli proces równoczesnych zmienił już zasób zdalny, ETag nie będzie zgodny z warunkiem wstępnym, a żądanie zakończy się niepowodzeniem z protokołem HTTP 412. Jeśli używasz .NET SDK, to manifestuje `CloudException` jako, gdzie metoda `IsAccessConditionFailed()` rozszerzenia zwraca true.

> [!Note]
> Istnieje tylko jeden mechanizm współbieżności. Jest zawsze używany niezależnie od tego, który interfejs API jest używany do aktualizacji zasobów.

<a name="samplecode"></a>
## <a name="use-cases-and-sample-code"></a>Przypadki użycia i przykładowy kod

Poniższy kod pokazuje accessCondition sprawdza dla operacji aktualizacji klucza:

+ Niepowodzenie aktualizacji, jeśli zasób już nie istnieje
+ Niepowodzenie aktualizacji w przypadku zmiany wersji zasobu

### <a name="sample-code-from-dotnetetagsexplainer-program"></a>Przykładowy kod z [programu DotNetETagsExplainer](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetETagsExplainer)

```
    class Program
    {
        // This sample shows how ETags work by performing conditional updates and deletes
        // on an Azure Cognitive Search index.
        static void Main(string[] args)
        {
            IConfigurationBuilder builder = new ConfigurationBuilder().AddJsonFile("appsettings.json");
            IConfigurationRoot configuration = builder.Build();

            SearchServiceClient serviceClient = CreateSearchServiceClient(configuration);

            Console.WriteLine("Deleting index...\n");
            DeleteTestIndexIfExists(serviceClient);

            // Every top-level resource in Azure Cognitive Search has an associated ETag that keeps track of which version
            // of the resource you're working on. When you first create a resource such as an index, its ETag is
            // empty.
            Index index = DefineTestIndex();
            Console.WriteLine(
                $"Test index hasn't been created yet, so its ETag should be blank. ETag: '{index.ETag}'");

            // Once the resource exists in Azure Cognitive Search, its ETag will be populated. Make sure to use the object
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
            // Azure Cognitive Search instead of potentially two. It also avoids an extra Delete request in cases where
            // the resource is deleted concurrently, but this doesn't matter much since resource deletion in
            // Azure Cognitive Search is idempotent.

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

## <a name="design-pattern"></a>Wzór projektu

Wzorzec projektu do implementowania optymistycznej współbieżności powinien zawierać pętlę, która ponawia sprawdzanie warunku dostępu, test dla warunku dostępu i opcjonalnie pobiera zaktualizowany zasób przed próbą ponownego zastosowania zmian.

Ten fragment kodu ilustruje dodanie synonimMap do indeksu, który już istnieje. Ten kod pochodzi z [przykładu Synonim C# dla usługi Azure Cognitive Search](search-synonyms-tutorial-sdk.md).

Fragment kodu pobiera indeks "hotele", sprawdza wersję obiektu w operacji aktualizacji, zgłasza wyjątek, jeśli warunek nie powiedzie się, a następnie ponawia operację (do trzech razy), począwszy od pobierania indeksu z serwera, aby uzyskać najnowszą wersję.

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


## <a name="next-steps"></a>Następne kroki

Przejrzyj [przykład synonimów C#,](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToSynonyms) aby uzyskać więcej kontekstu na temat bezpiecznego aktualizowania istniejącego indeksu.

Spróbuj zmodyfikować jedną z następujących próbek, aby uwzględnić obiekty ETags lub AccessCondition.

+ [Przykład interfejsu API REST w usłudze GitHub](https://github.com/Azure-Samples/search-rest-api-getting-started)
+ [Przykład SDK .NET w usłudze GitHub](https://github.com/Azure-Samples/search-dotnet-getting-started). To rozwiązanie obejmuje projekt "DotNetEtagsExplainer" zawierający kod przedstawiony w tym artykule.

## <a name="see-also"></a>Zobacz też

[Typowe nagłówki](https://docs.microsoft.com/rest/api/searchservice/common-http-request-and-response-headers-used-in-azure-search)
żądań HTTP i odpowiedzi[HTTP kody](https://docs.microsoft.com/rest/api/searchservice/http-status-codes)
stanu[index operacji (REST API)](https://docs.microsoft.com/rest/api/searchservice/index-operations)
