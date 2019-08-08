---
title: Wprowadzenie do sklepu merytorycznego (wersja zapoznawcza) — Azure Search
description: Wysyłaj wzbogacone dokumenty do usługi Azure Storage, w której można wyświetlać i przekształcać wzbogacone dokumenty oraz korzystać z nich w Azure Search i innych aplikacjach.
manager: cgronlun
author: HeidiSteen
services: search
ms.service: search
ms.subservice: cognitive-search
ms.devlang: NA
ms.topic: overview
ms.date: 08/02/2019
ms.author: heidist
ms.openlocfilehash: 107478f7e2d3c6726d3b8fb9c503ef13271c6571
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/06/2019
ms.locfileid: "68840818"
---
# <a name="what-is-knowledge-store-in-azure-search"></a>Co to jest sklep merytoryczny w Azure Search?

> [!Note]
> Magazyn wiedzy jest w wersji zapoznawczej i nie jest przeznaczony do użycia w środowisku produkcyjnym. [Interfejs API REST w wersji 2019-05-06 —](search-api-preview.md) wersja zapoznawcza zawiera tę funkcję. W tej chwili nie ma obsługi zestawu SDK platformy .NET.
>

Magazyn wiedzy to funkcja Azure Search, która zapisuje wzbogacone dokumenty i metadane utworzone przez potok indeksowania oparty na AI [(wyszukiwanie poznawcze)](cognitive-search-concept-intro.md). Wzbogacony dokument to dane wyjściowe potoku, utworzone na podstawie zawartości wyodrębnionej, strukturalnej i analizowanej przy użyciu zasobów w Cognitive Services. W standardowym potoku opartym na formacie AI, wzbogacone dokumenty są przejściowe, używane tylko podczas indeksowania, a następnie odrzucane. W sklepie z bazami danych dokumenty są zapisywane do późniejszej oceny, eksploracji i mogą stać się danymi wejściowymi w obciążeniu do nauki z danymi podrzędnymi. 

Jeśli używasz wyszukiwania poznawczego w przeszłości, wiesz już, że umiejętności są używane do przenoszenia dokumentu przez sekwencję wzbogacania. Wynikiem może być indeks Azure Search lub (Nowość w tej wersji zapoznawczej) projekcje w sklepie z bazami danych. Dwa dane wyjściowe, indeks wyszukiwania i magazyn wiedzy są fizycznie odrębne. Korzystają one z tej samej zawartości, ale są przechowywane i używane na różne sposoby.

Fizycznie magazyn wiedzy jest tworzony na koncie usługi Azure Storage, jako usługa Azure Table Storage lub BLOB Storage, w zależności od konfiguracji potoku. Wszystkie narzędzia i procesy, które mogą łączyć się z usługą Azure Storage, mogą zużywać zawartość sklepu z bazami wiedzy.

Projekcje to mechanizm służący do tworzenia struktury danych w sklepie z bazami wiedzy. Na przykład za pomocą projekcji można określić, czy dane wyjściowe mają być zapisywane jako pojedynczy obiekt BLOB, czy jako kolekcja powiązanych tabel. Prostym sposobem wyświetlania zawartości sklepu merytorycznego jest użycie wbudowanej [Eksplorator usługi Storage](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows) usługi Azure Storage.

![Magazyn wiedzy w diagramie potoku](./media/knowledge-store-concept-intro/annotationstore_sans_internalcache.png "Magazyn wiedzy w diagramie potoku")

Aby korzystać ze sklepu z bazami `knowledgeStore` danych, Dodaj element do elementu zestawu umiejętności, który definiuje operacje krok po kroku w potoku indeksowania. Podczas wykonywania Azure Search tworzy miejsce na koncie usługi Azure Storage i wypełnia je definicjami i zawartością utworzoną przez potok.

## <a name="benefits-of-knowledge-store"></a>Zalety sklepu z bazami danych

Magazyn wiedzy oferuje strukturę, kontekst i rzeczywistą zawartość — wydobyć z plików danych bez struktury i z częściową strukturą, takich jak obiekty blob, pliki obrazów, które zostały poddane analizie, a nawet dane strukturalne, które są przekształcane w nowe formularze. W [instruktażu krok po kroku](knowledge-store-howto.md) Zapisano dla tej wersji zapoznawczej można zobaczyć, jak na początku dokumentu jest podzielony na partycje w podstrukturach, które zostały przekształcone w nowe struktury i w inny sposób udostępnione dla procesów podrzędnych, takich jak maszyna obciążenia uczenia i analizy danych.

Mimo że warto zobaczyć, co może wyprodukować potok indeksowania oparty na formacie AI, realne możliwości sklepu z bazami danych umożliwiają Przekształć dane. Możesz zacząć od podstawowej zestawu umiejętności, a następnie wykonać iterację w celu dodania rosnących poziomów struktury, którą można następnie połączyć z nowymi strukturami, przydatnymi w innych aplikacjach niż Azure Search.

Wyliczane są korzyści z używania sklepu z bazami danych:

+ Korzystaj z ulepszonych dokumentów w narzędziach do [analizy i raportowania](#tools-and-apps) innych niż wyszukiwanie. Power BI z Power Query jest atrakcyjnym wyborem, ale dowolne narzędzie lub aplikacja, która może łączyć się z usługą Azure Storage, może pobierać z utworzonego przez siebie magazynu wiedzy.

+ Uściślij potok indeksowania AI podczas debugowania kroków i definicji zestawu umiejętności. Magazyn wiedzy przedstawia produkt definicji zestawu umiejętności w potoku indeksowania systemu AI. Możesz użyć tych wyników, aby zaprojektować lepszy zestawu umiejętności, ponieważ można zobaczyć dokładnie, jak wyglądają wzbogacenia. Możesz użyć [Eksplorator usługi Storage](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows) w usłudze Azure Storage, aby wyświetlić zawartość sklepu z bazami wiedzy.

+ Kształtowanie danych w nowych formularzach. Zmiana kształtu jest skodyfikowana w umiejętności, ale punkt jest, że zestawu umiejętności może teraz zapewnić tę możliwość. [Umiejętność kształtu](cognitive-search-skill-shaper.md) w Azure Search została rozszerzona w celu uwzględnienia tego zadania. Zmiana kształtu pozwala zdefiniować projekcję, która jest wyrównana do zamierzonego użycia danych przy zachowaniu relacji.

> [!Note]
> Nie znasz indeksowania opartego na formacie AI przy użyciu Cognitive Services? Azure Search integruje się z funkcjami obsługi Cognitive Services i języka, aby wyodrębnić i wzbogacić dane źródłowe za pomocą optycznego rozpoznawania znaków (OCR) przez pliki obrazów, rozpoznawanie jednostek i wyodrębnianie kluczowych fraz z plików tekstowych. Aby uzyskać więcej informacji, zobacz [co to jest wyszukiwanie poznawcze?](cognitive-search-concept-intro.md).

## <a name="create-a-knowledge-store"></a>Tworzenie sklepu merytorycznego

Magazyn wiedzy jest częścią definicji zestawu umiejętności. W tej wersji zapoznawczej tworzenie jej wymaga interfejsu API REST `api-version=2019-05-06-Preview` , przy użyciu programu lub kreatora **importu danych** w portalu.

Poniższy kod JSON określa `knowledgeStore`, który jest częścią zestawu umiejętności, który jest wywoływany przez indeksator (niepokazywany). Specyfikacja projekcji w programie określa, `knowledgeStore` czy tabele lub obiekty są tworzone w usłudze Azure Storage.

Jeśli znasz już indeksowanie oparte na formacie AI, definicja zestawu umiejętności określa tworzenie, organizację i istotę każdego wzbogaconego dokumentu.

```json
{
  "name": "my-new-skillset",
  "description": 
  "Example showing knowledgeStore placement, supported in api-version=2019-05-06-Preview. You need at least one skill, most likely a Shaper skill if you are modulating data structures.",
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

## <a name="components-backing-a-knowledge-store"></a>Składniki obsługujące tworzenie kopii zapasowej sklepu merytorycznego

Do utworzenia sklepu z bazami danych potrzebne są następujące usługi i artefakty.

### <a name="1---source-data"></a>1 — dane źródłowe

Dane lub dokumenty, które chcesz wzbogacić, muszą istnieć w źródle danych platformy Azure obsługiwanym przez Azure Search indeksatorów: 

* [Azure SQL](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)

* [Usługi Azure Cosmos DB](search-howto-index-cosmosdb.md)

* [Azure Blob Storage](search-howto-indexing-azure-blob-storage.md)

[Usługi Azure Table Storage](search-howto-indexing-azure-tables.md) można używać na potrzeby danych wychodzących w sklepie z bazami informacji, ale nie można ich używać jako zasobu danych przychodzących do potoku indeksowania opartego na AI.

### <a name="2---azure-search-service"></a>2 — usługa Azure Search

Potrzebna jest również usługa Azure Search i interfejs API REST do tworzenia i konfigurowania obiektów używanych do wzbogacania danych. Interfejs API REST do tworzenia sklepu z bazami `api-version=2019-05-06-Preview`danych to.

Azure Search udostępnia funkcję indeksatora, a Indeksatory są używane do kierowania całego procesu do końca, co spowodowało trwałe, wzbogacone dokumenty w usłudze Azure Storage. Indeksatory używają źródła danych, indeksu i zestawu umiejętności — wszystkie są wymagane do tworzenia i wypełniania sklepu z bazami wiedzy.

| Object | Interfejs API REST | Opis |
|--------|----------|-------------|
| źródło danych | [Create Data Source](https://docs.microsoft.com/rest/api/searchservice/create-data-source) (Tworzenie źródła danych)  | Zasób, który identyfikuje zewnętrzne źródło danych platformy Azure, dostarczając dane źródłowe używane do tworzenia wzbogaconych dokumentów.  |
| zestawu umiejętności | [Create zestawu umiejętności (API-Version = 2019-05-06)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)  | Zasób koordynujący korzystanie z [wbudowanych umiejętności](cognitive-search-predefined-skills.md) i [niestandardowych umiejętności poznawczych](cognitive-search-custom-skill-interface.md) używanych w potoku wzbogacenia podczas indeksowania. |
| index | [Utwórz indeks](https://docs.microsoft.com/rest/api/searchservice/create-index)  | Schemat przedstawiający indeks Azure Search. Pola w indeksie mapują do pól w danych źródłowych lub do pól wyprodukowanych w fazie wzbogacania (na przykład pola nazw organizacji utworzonych przez funkcję rozpoznawania jednostek). |
| indeksator | [Create indeksator (API-Version = 2019-05-06)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)  | Zasób definiujący składniki używane podczas indeksowania: w tym źródła danych, zestawu umiejętności, skojarzenia pól ze źródeł i pośrednich struktur danych do indeksu docelowego i samego indeksu. Uruchomienie indeksatora jest wyzwalaczem do pozyskiwania i wzbogacania danych. Wyjście jest indeksem wyszukiwania opartym na schemacie indeksu, wypełnionym danymi źródłowymi, wzbogaconym przez umiejętności.  |

### <a name="3---cognitive-services"></a>3 — Cognitive Services

Wzbogacania określone w zestawu umiejętności są oparte na przetwarzanie obrazów i funkcjach języka w Cognitive Services. Funkcja Cognitive Services jest używana podczas indeksowania przez zestawu umiejętności. Zestawu umiejętności to skład umiejętności, a umiejętności są powiązane z konkretnymi funkcjami przetwarzanie obrazów i języka. Aby zintegrować Cognitive Services, dołączysz [zasób Cognitive Services](cognitive-search-attach-cognitive-services.md) do zestawu umiejętności.

### <a name="4---storage-account"></a>4 — konto magazynu

Na koncie usługi Azure Storage Azure Search tworzy kontener obiektów blob lub tabele, w zależności od sposobu konfigurowania zestawu umiejętności. Jeśli dane pochodzą z usługi Azure Blob lub magazynu tabel, jest już ustawiony. W przeciwnym razie musisz utworzyć konto usługi Azure Storage. Tabele i obiekty w usłudze Azure Storage zawierają wzbogacone dokumenty utworzone przez potok indeksowania oparty na AI.

Konto magazynu jest określone w zestawu umiejętności. W `api-version=2019-05-06-Preview`programie definicja zestawu umiejętności zawiera definicję magazynu wiedzy, dzięki czemu można podać informacje o koncie.

<a name="tools-and-apps"></a>

### <a name="5---access-and-consume"></a>5 — dostęp i korzystanie z

Po wprowadzeniu wzbogaceń do magazynu dowolne narzędzie lub technologia, która łączy się z obiektem blob platformy Azure lub magazynem tabel, może służyć do eksplorowania, analizowania lub korzystania z zawartości. Na poniższej liście przedstawiono początek:

+ [Eksplorator usługi Storage](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows) , aby wyświetlić wzbogaconą strukturę dokumentu i zawartość. Rozważmy to jako narzędzie podstawowe do wyświetlania zawartości w sklepie z bazami danych.

+ [Power BI z Power Query](https://support.office.com/article/connect-to-microsoft-azure-blob-storage-power-query-f8165faa-4589-47b1-86b6-7015b330d13e) dla zapytań w języku naturalnym lub użyć narzędzi do raportowania i analizy, jeśli masz dane liczbowe.

+ [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/) dalsze manipulowanie.

+ Indeks Azure Search dla wyszukiwania pełnotekstowego zawartości indeksowanej przy użyciu [wyszukiwania poznawczego](cognitive-search-concept-intro.md).

## <a name="document-persistence"></a>Trwałość dokumentu

W ramach konta magazynu wzbogacania mogą być wyrażone jako tabele w usłudze Azure Table Storage lub jako obiekty w usłudze Azure Blob Storage. Wycofaj te wzbogacania, gdy są przechowywane, mogą służyć jako źródło do ładowania danych do innych baz danych i narzędzi,

+ Magazyn tabel jest przydatny, gdy potrzebna jest reprezentacja danych w formie tabelarycznej. Jeśli chcesz zmienić kształt lub połączyć elementy na nowe sposoby, magazyn tabel zapewnia niezbędne stopnia szczegółowości.

+ Magazyn obiektów BLOB tworzy jedną reprezentację w formacie JSON dla każdego dokumentu. Aby uzyskać pełny zakres wyrażeń, można użyć opcji magazynu w jednym zestawu umiejętności.

+ Azure Search utrzymuje zawartość w indeksie. Jeśli scenariusz nie jest powiązany z wyszukiwaniem, na przykład jeśli cel jest analizowany w innym narzędziu, można usunąć indeks utworzony przez potok. Możesz również zachować indeks i użyć wbudowanego narzędzia, takiego jak [Eksplorator wyszukiwania](search-explorer.md) jako trzeci średni (w tle Eksplorator usługi Storage i aplikacji analizy) na potrzeby korzystania z zawartości.

Wraz z zawartością dokumentu wzbogacone dokumenty obejmują metadane wersji zestawu umiejętności, która wyprodukowała wzbogacenia.  

## <a name="inside-a-knowledge-store"></a>W sklepie merytorycznym

Magazyn informacji składa się z pamięci podręcznej adnotacji i projekcji. *Pamięć podręczna* jest używana wewnętrznie przez usługę w celu buforowania wyników z umiejętności i śledzenia zmian. *Rzutowanie* definiuje schemat i strukturę wzbogacania, które pasują do zamierzonego użycia. Istnieje jedna pamięć podręczna na magazyn wiedzy, ale wiele projekcji. 

Pamięć podręczna jest zawsze kontenera obiektów blob, ale projekcje mogą być łączone jako tabele lub obiekty:

+ Jako obiekt, projekcja mapuje do magazynu obiektów blob, gdzie Projekcja jest zapisywana w kontener, w którym są obiekty lub hierarchiczne reprezentacje w formacie JSON dla scenariuszy, takich jak potok analizy danych.

+ Jako tabela, Projekcja jest mapowana na magazyn tabel. Reprezentacja tabelaryczna zachowuje relacje dotyczące scenariuszy, takich jak analiza danych lub eksportowanie jako ramki danych na potrzeby uczenia maszynowego. Wzbogacone projekcje można następnie łatwo zaimportować do innych magazynów danych. 

Można utworzyć wiele projekcji w sklepie z bazami danych w celu uwzględnienia różnych constituencies w organizacji. Deweloper może potrzebować dostępu do pełnej reprezentacji w formacie JSON wzbogaconego dokumentu, podczas gdy analityki danych lub analitycy mogą chcieć, aby zestawu umiejętności się ze szczegółowymi lub modularnymi strukturami danych.

Na przykład, jeśli jednym z celów procesu wzbogacania jest również utworzenie zestawu danych używanego do uczenia modelu, projekcja danych w magazynie obiektów będzie jednym ze sposobów używania danych w potokach analizy danych. Alternatywnie, jeśli chcesz utworzyć szybki Power BI pulpit nawigacyjny oparty na ulepszonych dokumentach, będzie on działał prawidłowo.

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

## <a name="where-do-i-start"></a>Od czego zacząć?

Zalecamy korzystanie z bezpłatnej usługi na potrzeby uczenia się, ale należy pamiętać, że liczba bezpłatnych transakcji jest ograniczona do 20 dokumentów dziennie na subskrypcję.

W przypadku korzystania z wielu usług Utwórz wszystkie usługi w tym samym regionie w celu uzyskania najlepszej wydajności i minimalizacji kosztów. Nie jest naliczana opłata za przepustowość danych przychodzących lub danych wychodzących, które przechodzą do innej usługi w tym samym regionie.

**Krok 1. [Tworzenie zasobu Azure Search](search-create-service-portal.md)** 

**Krok 2. [Utwórz konto usługi Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal)** 

**Krok 3. [Tworzenie zasobu Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)** 

**Krok 4. Rozpoczynanie [pracy z portalem](cognitive-search-quickstart-blob.md) — lub — Rozpoczynanie [pracy z przykładowymi danymi przy użyciu REST i programu Poster](knowledge-store-howto.md)** 

Możesz użyć REST `api-version=2019-05-06-Preview` , aby utworzyć potok oparty na formacie AI, który zawiera informacje o sklepie z bazami danych. W najnowszym interfejsie API w wersji zapoznawczej `knowledgeStore` obiekt zestawu umiejętności zawiera definicję.

## <a name="takeaways"></a>Wnioski

Magazyn wiedzy oferuje różne korzyści, w tym, ale nie ogranicza się do korzystania z ulepszonych dokumentów w scenariuszach innych niż wyszukiwanie, kontrola kosztów i zarządzanie dryfem w procesie wzbogacania. Te funkcje są dostępne do użycia po prostu przez dodanie konta magazynu do zestawu umiejętności i użycie zaktualizowanego języka wyrażeń zgodnie z opisem w [artykule jak rozpocząć pracę z magazynem wiedzy](knowledge-store-howto.md). 

## <a name="next-steps"></a>Następne kroki

Najprostszym podejściem do tworzenia wzbogaconych dokumentów jest użycie kreatora **importu danych** .

> [!div class="nextstepaction"]
> [Szybki start: Wypróbuj usługę wyszukiwania poznawczego w przewodniku po portalu](cognitive-search-quickstart-blob.md)
