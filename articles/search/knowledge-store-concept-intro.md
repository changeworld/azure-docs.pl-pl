---
title: Wprowadzenie do sklepu merytorycznego (wersja zapoznawcza)
titleSuffix: Azure Cognitive Search
description: Wysyłaj wzbogacone dokumenty do usługi Azure Storage, które umożliwiają wyświetlanie, przekształcanie i używanie wzbogaconych dokumentów na platformie Azure Wyszukiwanie poznawcze i w innych aplikacjach.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 82f8606f4b4201833667347d3ed16fdd73f70a36
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2019
ms.locfileid: "72790367"
---
# <a name="introduction-to-knowledge-stores-in-azure-cognitive-search"></a>Wprowadzenie do magazynów wiedzy na platformie Azure Wyszukiwanie poznawcze

> [!Note]
> Magazyn wiedzy jest w wersji zapoznawczej i nie jest przeznaczony do użycia w środowisku produkcyjnym. [Interfejs API REST w wersji 2019-05-06 — wersja zapoznawcza](search-api-preview.md) zawiera tę funkcję. W tej chwili nie ma obsługi zestawu SDK platformy .NET.
>

Magazyn wiedzy to funkcja platformy Azure Wyszukiwanie poznawcze, która utrzymuje dane wyjściowe z [potoku wzbogacania AI](cognitive-search-concept-intro.md) na potrzeby późniejszej analizy lub innego przetwarzania podrzędnego. *Wzbogacony dokument* to dane wyjściowe potoku, utworzone na podstawie zawartości wyodrębnionej, strukturalnej i analizowanej przy użyciu procesów AI. W standardowym potoku AI, wzbogacone dokumenty są nietrwałe, używane tylko podczas indeksowania, a następnie odrzucane. W sklepie merytorycznym chronione dokumenty są zachowywane. 

Jeśli w przeszłości korzystasz z umiejętności poznawczych w usłudze Azure Wyszukiwanie poznawcze, już wiesz, że *umiejętności* przenieść dokument przez sekwencję wzbogacania. Wynikiem może być indeks wyszukiwania lub (Nowość w tej wersji zapoznawczej) projekcje w sklepie z bazami danych. Dwa dane wyjściowe, indeks wyszukiwania i magazyn wiedzy mają tę samą zawartość, ale są przechowywane i używane na różne sposoby.

Fizycznie magazyn wiedzy to [Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-account-overview), Azure Table Storage, Azure Blob Storage lub oba te elementy. Wszystkie narzędzia i procesy, które mogą łączyć się z usługą Azure Storage, mogą zużywać zawartość sklepu z bazami wiedzy.

![Magazyn wiedzy w diagramie potoku](./media/knowledge-store-concept-intro/knowledge-store-concept-intro.svg "Magazyn wiedzy w diagramie potoku")

Projekcje to mechanizm służący do tworzenia struktury danych w sklepie z bazami wiedzy. Na przykład za pomocą projekcji można określić, czy dane wyjściowe mają być zapisywane jako pojedynczy obiekt BLOB, czy jako kolekcja powiązanych tabel. 

Aby korzystać ze sklepu z bazami danych, Dodaj `knowledgeStore` element do zestawu umiejętności, który definiuje operacje krok po kroku w potoku indeksowania. Podczas wykonywania usługa Azure Wyszukiwanie poznawcze tworzy miejsce na koncie usługi Azure Storage i projektuje wzbogacone dokumenty jako obiekty blob lub tabele, w zależności od konfiguracji.

## <a name="benefits-of-knowledge-store"></a>Zalety sklepu z bazami danych

Magazyn wiedzy oferuje strukturę, kontekst i rzeczywistą zawartość — wydobyć z plików danych bez struktury i z częściową strukturą, takich jak obiekty blob, pliki obrazów, które zostały poddane analizie, a nawet dane strukturalne, które są przekształcane w nowe formularze. W [instruktażu krok po kroku](knowledge-store-howto.md) Zapisano dla tej wersji zapoznawczej można zobaczyć, jak na początku dokumentu jest podzielony na partycje w podstrukturach, które zostały przekształcone w nowe struktury i w inny sposób udostępnione dla procesów podrzędnych, takich jak maszyna obciążenia uczenia i analizy danych.

Mimo że warto zobaczyć, co można utworzyć za pomocą potoku wzbogacenia, rzeczywista moc magazynu wiedzy jest możliwość zmiany kształtu danych. Możesz zacząć od podstawowej zestawu umiejętności, a następnie wykonać iterację w celu dodania rosnących poziomów struktury, którą można następnie połączyć do nowych struktur, przydatnych w innych aplikacjach poza platformą Azure Wyszukiwanie poznawcze.

Wyliczane są korzyści z używania sklepu z bazami danych:

+ Korzystaj z ulepszonych dokumentów w [narzędziach do analizy i raportowania](#tools-and-apps) innych niż wyszukiwanie. Power BI z Power Query jest atrakcyjnym wyborem, ale dowolne narzędzie lub aplikacja, która może łączyć się z usługą Azure Storage, może pobierać z utworzonego przez siebie magazynu wiedzy.

+ Uściślij potok indeksowania AI podczas debugowania kroków i definicji zestawu umiejętności. Magazyn wiedzy przedstawia produkt definicji zestawu umiejętności w potoku indeksowania systemu AI. Możesz użyć tych wyników, aby zaprojektować lepszy zestawu umiejętności, ponieważ można zobaczyć dokładnie, jak wyglądają wzbogacenia. Możesz użyć [Eksplorator usługi Storage](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows) w usłudze Azure Storage, aby wyświetlić zawartość sklepu z bazami wiedzy.

+ Kształtowanie danych w nowych formularzach. Zmiana kształtu jest skodyfikowana w umiejętności, ale punkt jest, że zestawu umiejętności może teraz zapewnić tę możliwość. [Umiejętność kształtowania](cognitive-search-skill-shaper.md) w usłudze Azure wyszukiwanie poznawcze została rozszerzona w celu uwzględnienia tego zadania. Zmiana kształtu pozwala zdefiniować projekcję, która jest wyrównana do zamierzonego użycia danych przy zachowaniu relacji.

> [!Note]
> Nie znasz usługi AI enrichcment przy użyciu Cognitive Services? Platforma Azure Wyszukiwanie poznawcze integruje się z funkcjami obsługi Cognitive Services i języka, aby wyodrębnić i wzbogacić dane źródłowe za pomocą optycznego rozpoznawania znaków (OCR) przez pliki obrazów, rozpoznawanie jednostek i wyodrębnianie kluczowych fraz z plików tekstowych. Aby uzyskać więcej informacji, zobacz [wzbogacanie AI na platformie Azure wyszukiwanie poznawcze](cognitive-search-concept-intro.md).

## <a name="creating-a-knowledge-store"></a>Tworzenie sklepu merytorycznego

Magazyn wiedzy jest częścią [zestawu umiejętności](cognitive-search-working-with-skillsets.md), która z kolei jest częścią [indeksatora](search-indexer-overview.md). 

W tej wersji zapoznawczej można utworzyć magazyn wiedzy przy użyciu interfejsu API REST i `api-version=2019-05-06-Preview` lub za pomocą kreatora **importu danych** w portalu.

### <a name="json-representation-of-a-knowledge-store"></a>Reprezentacja w formacie JSON magazynu wiedzy

Poniższy kod JSON określa `knowledgeStore`, który jest częścią zestawu umiejętności, który jest wywoływany przez indeksator (niepokazywany). Jeśli masz już doświadczenie z wzbogacaniem AI, zestawu umiejętności określa tworzenie, organizację i istotę każdego wzbogaconego dokumentu. Zestawu umiejętności musi zawierać co najmniej jedną umiejętność, najprawdopodobniej umiejętność kształtowania, jeśli są modulowane struktury danych.

@No__t_0 składa się z połączeń i projekcji. 

+ Połączenie jest kontem magazynu w tym samym regionie co Azure Search. 

+ Projekcje to pary obiektów Tables. `Tables` zdefiniować fizyczne wyrażenie ulepszonych dokumentów w usłudze Azure Table Storage. `Objects` zdefiniować obiekty fizyczne w usłudze Azure Blob Storage.

```json
{
  "name": "my-new-skillset",
  "description": "Example showing knowledgeStore placement in a skillset.",
  "skills":
  [
    {
    "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
    "context": "/document/content/phrases/*",
    "inputs": [
        {
        "name": "text",
        "source": "/document/content/phrases/*"
        },
        {
        "name": "sentiment",
        "source": "/document/content/phrases/*/sentiment"
        }
    ],
    "outputs": [
        {
        "name": "output",
        "targetName": "analyzedText"
        }
    ]
    },
  ],
  "cognitiveServices": 
    {
    "@odata.type": "#Microsoft.Azure.Search.CognitiveServicesByKey",
    "description": "mycogsvcs resource in West US 2",
    "key": "<your key goes here>"
    },
  "knowledgeStore": { 
    "storageConnectionString": "<your connection string goes here>", 
    "projections": [ 
        { 
            "tables": [  
            { "tableName": "Reviews", "generatedKeyName": "ReviewId", "source": "/document/Review" , "sourceContext": null, "inputs": []}, 
            { "tableName": "Sentences", "generatedKeyName": "SentenceId", "source": "/document/Review/Sentences/*", "sourceContext": null, "inputs": []}, 
            { "tableName": "KeyPhrases", "generatedKeyName": "KeyPhraseId", "source": "/document/Review/Sentences/*/KeyPhrases", "sourceContext": null, "inputs": []}, 
            { "tableName": "Entities", "generatedKeyName": "EntityId", "source": "/document/Review/Sentences/*/Entities/*" ,"sourceContext": null, "inputs": []} 

            ], 
            "objects": [ 
               
            ]      
        },
        { 
            "tables": [ 
            ], 
            "objects": [ 
                { 
                "storageContainer": "Reviews", 
                "format": "json", 
                "source": "/document/Review", 
                "key": "/document/Review/Id" 
                } 
            ]      
        }        
    ]     
    } 
}
```

### <a name="sources-of-data-for-a-knowledge-store"></a>Źródła danych dla sklepu z bazami wiedzy

Jeśli magazyn wiedzy jest wyprowadzany z potoku wzbogacenia AI, jakie są dane wejściowe? Oryginalne dane, które mają zostać wyodrębnione, wzbogacone i ostatecznie zapisane w sklepie wiedzy, mogą pochodzić od dowolnego źródła danych platformy Azure obsługiwanego przez indeksatory wyszukiwania: 

* [Azure Cosmos DB](search-howto-index-cosmosdb.md)

* [Azure Blob Storage](search-howto-indexing-azure-blob-storage.md)

* [Azure Table storage](search-howto-indexing-azure-tables.md)

* [Azure SQL](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)

Indeksatory i umiejętności umożliwiają tworzenie wyodrębniania i wzbogacanie zawartości w ramach obciążeń indeksowania, a następnie zapisywanie wyników w sklepie z bazami danych.

### <a name="rest-apis-used-in-creation-of-a-knowledge-store"></a>Interfejsy API REST używane podczas tworzenia sklepu z bazami danych

Tylko dwa interfejsy API mają rozszerzenia wymagane do tworzenia sklepu z bazami danych (Utwórz zestawu umiejętności i Utwórz indeksator). Inne interfejsy API są używane jako-is.

| Obiekt | Interfejs API REST | Opis |
|--------|----------|-------------|
| Źródło danych | [Create Data Source](https://docs.microsoft.com/rest/api/searchservice/create-data-source) (Tworzenie źródła danych)  | Zasób, który identyfikuje zewnętrzne źródło danych platformy Azure, dostarczając dane źródłowe używane do tworzenia wzbogaconych dokumentów.  |
| zestawu umiejętności | [Create zestawu umiejętności (API-Version = 2019-05 -06-Preview)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)  | Zasób koordynujący korzystanie z [wbudowanych umiejętności](cognitive-search-predefined-skills.md) i [niestandardowych umiejętności poznawczych](cognitive-search-custom-skill-interface.md) używanych w potoku wzbogacenia podczas indeksowania. Zestawu umiejętności ma definicję `knowledgeStore` jako element podrzędny. |
| indeks | [Utwórz indeks](https://docs.microsoft.com/rest/api/searchservice/create-index)  | Schemat przedstawiający indeks wyszukiwania. Pola w indeksie mapują do pól w danych źródłowych lub do pól wyprodukowanych w fazie wzbogacania (na przykład pola nazw organizacji utworzonych przez funkcję rozpoznawania jednostek). |
| indeksatora | [Create indeksator (API-Version = 2019-05-06)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)  | Zasób definiujący składniki używane podczas indeksowania: w tym źródła danych, zestawu umiejętności, skojarzenia pól ze źródeł i pośrednich struktur danych do indeksu docelowego i samego indeksu. Uruchomienie indeksatora jest wyzwalaczem do pozyskiwania i wzbogacania danych. Wyjście jest indeksem wyszukiwania opartym na schemacie indeksu, wypełnionym danymi źródłowymi, wzbogaconym przez umiejętności.  |

### <a name="physical-composition-of-a-knowledge-store"></a>Fizyczna kompozycja sklepu z bazami danych

 *Rzutowanie*, które jest elementem definicji `knowledgeStore`, przegubuje schemat i strukturę danych wyjściowych, tak aby pasował do zamierzonego użycia. Można zdefiniować wiele projekcji, jeśli masz aplikacje, które zużywają dane w różnych formatach i kształtach. 

Projekcje mogą być przegubowe jako obiekty lub tabele:

+ Jako obiekt, projekcja mapuje do magazynu obiektów blob, gdzie Projekcja jest zapisywana w kontener, w którym są obiekty lub hierarchiczne reprezentacje w formacie JSON dla scenariuszy, takich jak potok analizy danych.

+ Jako tabela, Projekcja jest mapowana na magazyn tabel. Reprezentacja tabelaryczna zachowuje relacje dotyczące scenariuszy, takich jak analiza danych lub eksportowanie jako ramki danych na potrzeby uczenia maszynowego. Wzbogacone projekcje można następnie łatwo zaimportować do innych magazynów danych. 

Można utworzyć wiele projekcji w sklepie z bazami danych w celu uwzględnienia różnych constituencies w organizacji. Deweloper może potrzebować dostępu do pełnej reprezentacji w formacie JSON wzbogaconego dokumentu, podczas gdy analityki danych lub analitycy mogą chcieć, aby zestawu umiejętności się ze szczegółowymi lub modularnymi strukturami danych.

Na przykład, jeśli jednym z celów procesu wzbogacania jest również utworzenie zestawu danych używanego do uczenia modelu, projekcja danych w magazynie obiektów będzie jednym ze sposobów używania danych w potokach analizy danych. Alternatywnie, jeśli chcesz utworzyć szybki Power BI pulpit nawigacyjny oparty na ulepszonych dokumentach, będzie on działał prawidłowo.

<a name="tools-and-apps"></a>

## <a name="connecting-with-tools-and-apps"></a>Łączenie z narzędziami i aplikacjami

Po wprowadzeniu wzbogaceń do magazynu dowolne narzędzie lub technologia, która łączy się z obiektem blob platformy Azure lub magazynem tabel, może służyć do eksplorowania, analizowania lub korzystania z zawartości. Na poniższej liście przedstawiono początek:

+ [Eksplorator usługi Storage](knowledge-store-view-storage-explorer.md) , aby wyświetlić wzbogaconą strukturę dokumentu i zawartość. Rozważmy to jako narzędzie podstawowe do wyświetlania zawartości w sklepie z bazami danych.

+ [Power BI](knowledge-store-connect-power-bi.md) dla narzędzi do raportowania i analizy, jeśli masz dane liczbowe.

+ [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/) dalsze manipulowanie.


<!---
## Data lifecycle and billing

Each time you run the indexer, the cache in Azure storage is updated if the skillset definition or underlying source data has changed. As input documents are edited or deleted, changes are propagated through the annotation cache to the projections, ensuring that your projected data is a current representation of your inputs at the end of the indexer run. 

Generally speaking, pipeline processing can be an all-or-nothing operation, but Azure Search can process incremental changes, which saves you time and money.

If a document is new or updated, all skills are run. If only the skillset changes, reprocessing is scoped to just those skills and documents affected by your edit.

### Changes to a skillset
Suppose that you have a pipeline composed of multiple skills, operating over a large body of static data (for example, scanned documents), that takes 8 hours and costs $200 to create the knowledge store. Now suppose you need to tweak one of the skills in the skillset. Rather than starting over, Azure Search can determine which skill is affected, and reprocess only that skill. Cached data and projections that are unaffected by the change remain intact in the knowledge store.

### Changes in the data
Scenarios can vary considerably, but let's suppose instead of static data, you have volatile data that changes between indexer invocations. Given no changes to the skillset, you are charged for processing the delta of new and modified document. The timestamp information varies by data source, but for illustration, in a Blob container, Azure Search looks at the `lastmodified` date to determine which blobs need to be ingested.

> [!Note]
> While you can edit the data in the projections, any edits will be overwritten on the next pipeline invocation, assuming the document in source data is updated. 

### Deletions

Although Azure Search creates and updates structures and content in Azure storage, it does not delete them. Projections and cached documents continue to exist even when the skillset is deleted. As the owner of the storage account, you should delete a projection if it is no longer needed. 

### Tips for development

+ Start small with a representative sample of your data as you make significant changes to skillset composition. As your design finalizes, you can slowly add more data during later-stage development, and then roll in the entire data set when you are comfortable with the pipeline composition.

+ Retain control over indexer invocation. Indexers can run on a schedule, which is helpful for solutions that are rolled into production, but less helpful if you are actively developing your pipeline. During development, avoid schedules so that you don’t lose track of cache or projection state. Once your solution is in production and skillset composition is static, you can put the indexer on a schedule to pick up routine changes in the external source data. 

-->

<!-- ## Where do I start?

We recommend the Free service for learning purposes, but be aware that the number of free transactions is limited to 20 documents per day, per subscription.

When using multiple services, create all of your services in the same region for best performance and to minimize costs. You are not charged for bandwidth for inbound data or outbound data that goes to another service in the same region.

**Step 1: [Create an Azure Cognitive Search resource](search-create-service-portal.md)** 

**Step 2: [Create an Azure storage account](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal)** 

**Step 3: [Create a Cognitive Services resource](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)** 

**Step 4: [Get started with the portal](cognitive-search-quickstart-blob.md) - or - [Get started with sample data using REST and Postman](knowledge-store-howto.md)** 

You can use REST `api-version=2019-05-06-Preview` to construct an AI-based pipeline that includes knowledge store. In the newest preview API, the Skillset object provides the `knowledgeStore` definition. -->

## <a name="next-steps"></a>Następne kroki

Magazyn wiedzy oferuje trwałość ulepszonych dokumentów, przydatnych podczas projektowania zestawu umiejętności lub tworzenia nowych struktur i zawartości do użycia przez dowolne aplikacje klienckie, które mogą uzyskać dostęp do konta usługi Azure Storage.

Najprostszym podejściem do tworzenia wzbogaconych dokumentów jest użycie kreatora **importu danych** , ale można również użyć programu Poster i interfejsu API REST, który jest bardziej przydatny, jeśli chcesz uzyskać wgląd w sposób tworzenia obiektów i odwoływania się do nich.

> [!div class="nextstepaction"]
> [Tworzenie sklepu z bazami danych przy użyciu portalu](knowledge-store-create-portal.md) 
> [tworzenia sklepu z bazami danych przy użyciu programu Poster i interfejsu APi REST](knowledge-store-create-rest.md)
