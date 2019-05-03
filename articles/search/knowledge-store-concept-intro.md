---
title: Wprowadzenie do Store wiedzy i przegląd — usługa Azure Search
description: Wyślij wzbogaconego dokumenty do usługi Azure storage, gdzie można wyświetlić, zmienić kształt i używanie wzbogaconego dokumentów w usłudze Azure Search i w innych aplikacjach.
manager: cgronlun
author: HeidiSteen
services: search
ms.service: search
ms.devlang: NA
ms.topic: overview
ms.date: 05/02/2019
ms.author: heidist
ms.openlocfilehash: 3000016de934aaa3faab96821f9747ea4b571ef7
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/02/2019
ms.locfileid: "65026998"
---
# <a name="what-is-knowledge-store-in-azure-search"></a>Co to jest Store wiedzę w usłudze Azure Search?

Store Knowledge to opcjonalna funkcja usługi Azure Search, obecnie w publicznej wersji zapoznawczej, która zapisuje wzbogaconego dokumentów i metadane utworzone przez potokiem indeksowania oparte na sztucznej Inteligencji [(cognitive search)](cognitive-search-concept-intro.md). Store wiedzy jest wspierany przez konto usługi Azure storage, które można skonfigurować jako część potoku. Po włączeniu usługi wyszukiwania używa tego konta magazynu pamięci podręcznej reprezentację każdego wzbogaconego dokumentu. 

Jeśli używano wyszukiwania kognitywnego w przeszłości już wiesz, że dokładniejsze może służyć do której przenieść dokument za pośrednictwem sekwencji wzbogacenia. Wynik może być indeksu usługi Azure Search lub (nowe w tej wersji zapoznawczej) projekcji w magazynie wiedzy.

Prognozy są Twoje mechanizm do strukturyzowania danych do użycia w aplikacji podrzędnego. Możesz użyć [Eksploratora usługi Storage](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows) utworzony dla usługi Azure storage lub dowolnej aplikacji, który nawiązuje połączenie z usługi Azure storage, która otwiera nowe możliwości, co umożliwia korzystanie z wzbogacony dokumentów. Niektóre przykłady są potoków do nauki o danych i analiza niestandardowych.

![Store wiedzy na diagramie potoku](./media/knowledge-store-concept-intro/annotationstore_sans_internalcache.png "Store wiedzy na diagramie potoku")

Aby użyć Store wiedzy, Dodaj `knowledgeStore` element do zestawu umiejętności, definiujący stopniowy działań w potoku usługi indeksowania. W czasie wykonywania usługi Azure Search tworzy miejsce na Twoim koncie usługi Azure storage i wypełnia je z definicji i zawartości utworzonej przez potok.

## <a name="benefits-of-knowledge-store"></a>Zalety Store wiedzy

Udostępnia magazyn wiedzy, możesz struktury, kontekstu i rzeczywistej zawartości — zgromadzone od plików danych z częściową strukturą i bez struktury, takie jak obiekty BLOB, pliki obrazów, które zostały poddane analizie, lub nawet struktury danych, która to zmienić w formularzach nowy. W [przewodnik krok po kroku](knowledge-store-howto.md) przeznaczony dla tej wersji zapoznawczej i zostanie wyświetlony pierwszej ręki jak podzieleniu na partycje w podstruktury, odtworzona w nowych struktur i udostępnione dla podrzędnych gęstą dokumentu JSON procesy, takie jak obciążeń analizy nauki i dane maszyny.

Chociaż jest to przydatne zobaczyć, jakie można utworzyć potoku indeksowania oparte na sztucznej Inteligencji, rzeczywista siła Store wiedzy jest możliwość zmienić kształt danych. Może rozpoczynać się podstawowego zestawu umiejętności, a następnie przejść przez jego zwiększają poziom struktury, które można następnie połączyć w nowych struktur w innych aplikacjach, oprócz usługi Azure Search w użyciu.

Korzyści wynikające z wiedzy Store wyliczone, są następujące:

+ Używanie wzbogaconego dokumentów w [analizy i narzędzi do raportowania](#tools-and-apps) niż wyszukiwania. Usługa Power BI za pomocą dodatku Power Query jest wybór atrakcyjnych, ale ściągnąć narzędzie dowolnej aplikacji, które można podłączyć do usługi Azure storage ze sklepu wiedzy, którą tworzysz.

+ Dostosuj potoku indeksowania sztucznej Inteligencji podczas debugowania kroki i definicji zestawu umiejętności. Magazyn wiedzy pokazuje produktu definicji zestawu umiejętności w potoku indeksowania sztucznej Inteligencji. Do projektowania lepszych zestawu umiejętności, ponieważ widać dokładnie wygląd wszystkich wzbogacenia, można użyć tych wyników. Możesz użyć [Eksploratora usługi Storage](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows) w usłudze Azure storage, aby wyświetlić zawartość magazynu wiedzy.

+ Kształt danych w formularzach nowy. Przekształcanie są oznaczone w dokładniejsze, ale punkt znajduje się że zestawu umiejętności teraz może zapewnić tę możliwość. [Umiejętności Shaper](cognitive-search-skill-shaper.md) w usłudze Azure Search został rozszerzony w celu uwzględnienia tego zadania. Przekształcanie pozwala na zdefiniowanie projekcji, która wyrównuje zamierzonym użyciem danych przy jednoczesnym zachowaniu relacji.

> [!Note]
> Nie znasz indeksowania oparte na sztucznej Inteligencji, za pomocą usług Cognitive Services? Usługa Azure Search integruje się z funkcjami przetwarzania usługi Cognitive Services i język do wyodrębniania i Wzbogacanie danych źródłowych, za pomocą funkcji optycznego rozpoznawania znaków (OCR), pliki obrazów, rozpoznawania jednostek i wyodrębnianie kluczowych fraz z plików tekstowych i innych. Aby uzyskać więcej informacji, zobacz [co to jest usługa cognitive search?](cognitive-search-concept-intro.md).

## <a name="create-a-knowledge-store"></a>Utwórz magazyn wiedzy

Magazyn wiedzy jest częścią definicji zestawu umiejętności. W tej wersji zapoznawczej jej tworzenia wymaga interfejsu API REST przy użyciu `api-version=2019-05-06-Preview` lub **importowania danych** kreatora w portalu.

Określa następujące dane JSON `knowledgeStore`, który jest częścią zestawu umiejętności, który jest wywoływany przez indeksator (niewyświetlany). Specyfikacja projekcji w ramach `knowledgeStore` Określa, czy tabele lub obiekty są tworzone w usłudze Azure storage.

Jeśli znasz już indeksowania oparte na sztucznej Inteligencji, definicji zestawu umiejętności określa tworzenie, organizacji i treść każdego wzbogaconego dokumentu.

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

## <a name="components-backing-a-knowledge-store"></a>Składniki magazyn wiedzy zapasowy

Aby utworzyć magazyn wiedzy, potrzebne są następujące usługi i artefaktów.

### <a name="1---source-data"></a>1 — źródło danych

Dane lub dokumenty, które chcesz wzbogacić musi istnieć w źródle danych platformy Azure obsługiwane przez indeksatorów usługi Azure Search: 

* [Azure SQL](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)

* [Azure Cosmos DB](search-howto-index-cosmosdb.md)

* [Azure Blob Storage](search-howto-indexing-azure-blob-storage.md)

[Usługa Azure Table storage](search-howto-indexing-azure-tables.md) może służyć do danych wychodzących w magazynie wiedzy, ale nie może służyć jako zasób dla danych przychodzących do potoku indeksowania oparte na sztucznej Inteligencji.

### <a name="2---azure-search-service"></a>2 — Usługa azure Search

Należy również usługę Azure Search i interfejsu API REST do tworzenia i konfigurowania obiektów używanych dla Wzbogacanie danych. Interfejs API REST do tworzenia magazynu wiedzy jest `api-version=2019-05-06-Preview`.

Usługa Azure Search udostępnia funkcję indeksatora i indeksatory są używane do kierowania cały proces end-to-end, skutkuje utrwalonych, wzbogaconego dokumentów w usłudze Azure storage. Indeksatory używać źródła danych, indeksu i zestawu umiejętności — które są wymagane do tworzenia i zapełnianie magazynu wiedzy.

| Object | Interfejs API REST | Opis |
|--------|----------|-------------|
| źródło danych | [Create Data Source](https://docs.microsoft.com/rest/api/searchservice/create-data-source) (Tworzenie źródła danych)  | Identyfikowanie zewnętrzne Azure źródło danych dostarczania źródła danych używany do tworzenia dokumentów wzbogaconego zasób.  |
| Zestawu umiejętności | [Tworzenie zestawu umiejętności (wersja api-version = 2019-05-06)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)  | Zasobu, koordynowania użytkowania [wbudowanych umiejętności](cognitive-search-predefined-skills.md) i [niestandardowe umiejętności poznawcze](cognitive-search-custom-skill-interface.md) używane w wzbogacony potok podczas indeksowania. |
| indeks | [Tworzenie indeksu](https://docs.microsoft.com/rest/api/searchservice/create-index)  | Schemat może przedstawiać indeksu usługi Azure Search. Pola w indeksie mapowania pól w danych źródłowych lub pola wyprodukowany w fazie wzbogacania (na przykład pole nazwy organizacji utworzone przez rozpoznawanie jednostek). |
| indeksator | [Tworzenie indeksatora (wersja api-version = 2019-05-06)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)  | Zasób Definiowanie składniki używane podczas indeksowania: w tym źródłem danych, zestawu umiejętności, skojarzeń pól ze źródła i struktury są dane pośrednie indeksu docelowego i sam indeks. Uruchamianie indeksatora jest wyzwalacz dla pozyskiwania danych i wzbogacanie. Dane wyjściowe są indeksu wyszukiwania na podstawie schematu indeks, wypełniony danych źródłowych, wzbogacone za pośrednictwem zestawu umiejętności.  |

### <a name="3---cognitive-services"></a>3 — cognitive Services

Wzbogacenia określone w zestawu umiejętności są oparte na funkcji przetwarzania obrazów i język w usługach Cognitive Services. Funkcje usługi cognitive Services jest wykorzystywane podczas indeksowania za pośrednictwem Twojego zestawu umiejętności. Zestawu umiejętności jest złożenia umiejętności i umiejętności są powiązane z określonych funkcji przetwarzania obrazów i język. Aby zintegrować usługi Cognitive Services, należy [dołączenia zasobu usług Cognitive Services](cognitive-search-attach-cognitive-services.md) do zestawu umiejętności.

### <a name="4---storage-account"></a>4 - konto magazynu

W ramach konta usługi Azure Storage usługi Azure Search tworzy kontener obiektów Blob lub tabel, w zależności od sposobu skonfigurowania zestawu umiejętności. Dane pochodzą z usługi Azure blob Storage lub Table storage, są już ustawiony. W przeciwnym razie należy utworzyć konto magazynu platformy Azure. Zawierają wzbogaconego dokumenty, utworzone przez z potokiem indeksowania oparte na sztucznej Inteligencji, tabel i obiektów w usłudze Azure storage.

Konto magazynu zostało określone w zestawu umiejętności. W `api-version=2019-05-06-Preview`, definicję zestawu umiejętności zawiera definicję magazynu wiedzy, dzięki czemu możesz podać informacje o koncie.

<a name="tools-and-apps"></a>

### <a name="5---access-and-consume"></a>5 - dostępu i zużywa

Gdy wzbogacenia istnieje w magazynie, narzędzi lub technologii, który nawiązuje połączenie z usługi Azure blob Storage czy Table storage można eksplorować, analizowania lub używanie zawartości. Poniższa lista jest rozpoczęcia:

+ [Eksplorator usługi Storage](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows) do wyświetlania dokumentu wzbogaconego struktury i zawartości. Należy wziąć pod uwagę to jako narzędzie odniesienia do przeglądania zawartość magazynu wiedzy.

+ [Usługa Power BI za pomocą dodatku Power Query](https://support.office.com/article/connect-to-microsoft-azure-blob-storage-power-query-f8165faa-4589-47b1-86b6-7015b330d13e) dla zapytań w języku naturalnym oraz korzystanie z raportowania i analizy narzędzia w przypadku danych liczbowych.

+ [Usługa Azure Data Factory](https://docs.microsoft.com/azure/data-factory/) do dalszych manipulacji.

+ Usługa Azure indeks wyszukiwania dla wyszukiwania pełnotekstowego w zawartości, który został indeksowane, za pomocą [wyszukiwania kognitywnego](cognitive-search-concept-intro.md).

## <a name="document-persistence"></a>Trwałość dokumentu

W ramach konta magazynu wzbogacenia może być wyrażona jako tabel w usłudze Azure Table storage lub obiektów w usłudze Azure Blob storage. Pamiętaj, że wzbogacenia, po umieszczeniu może służyć jako źródło do ładowania danych do innych baz danych i narzędzia

+ Magazyn tabel jest przydatne, gdy chcesz obsługujących schematu reprezentacja danych w formie tabelarycznej. Jeśli chcesz zmienić kształt lub ponownie połączyć elementy na nowe sposoby, Table storage zapewnia niezbędne stopień szczegółowości.

+ Usługi blob storage tworzy jedną kompleksowych reprezentacji JSON poszczególnych dokumentów. Obie opcje magazynowania w jednym zestawu umiejętności umożliwia dostęp do pełnej gamy wyrażeń.

+ Usługa Azure Search są utrwalane zawartość w indeksie. W przypadku danego scenariusza wyszukiwania niepowiązaną, na przykład jeśli celu jest analizę innego narzędzia, możesz usunąć indeks, który tworzy potok. Ale można również przechowywać indeks i użyć wbudowanego narzędzia, takiego jak [Eksploratora wyszukiwania](search-explorer.md) jako trzeci medium (związany z Eksploratora usługi Storage i analizy aplikacji) do interakcji z zawartością.

Wraz z zawartości dokumentu wzbogaconego dokumenty zawierają metadane wytworzonego wzbogacenia wersji zestawu umiejętności.  

## <a name="inside-a-knowledge-store"></a>W sklepie wiedzy

Magazyn wiedzy składa się z pamięci podręcznej adnotacji i projekcji. *Pamięci podręcznej* jest używana przez usługę wewnętrznie w celu buforowania wyników z umiejętności i śledzenie zmian. A *projekcji* definiuje schematu i struktury wzbogacenia, które pasują do zamierzonego użycia. Istnieje jeden pamięci podręcznej na wiedzy magazynu, ale wiele projekcji. 

Pamięć podręczna jest zawsze kontener obiektów blob, ale może być przegubowe projekcje jako tabele lub obiekty:

+ Jako obiekt projekcji mapuje do magazynu obiektów Blob, w której projekcji jest zapisywany do kontenera, w którym są obiekty lub hierarchiczny reprezentacji w formacie JSON dla scenariuszy, takich jak potoku wydobywania danych.

+ Jako tabelę projekcji mapuje do usługi Table storage. Reprezentacja tabelarycznych zachowuje relacji dla scenariuszy, takich jak analiza danych lub eksportu jako ramki danych dla usługi machine learning. Następnie można łatwo zaimportować do innych magazynów danych wzbogaconego projekcji. 

Możesz utworzyć wiele projekcji w magazynie wiedzy w celu uwzględnienia różnych wyborcze w Twojej organizacji. Deweloper może być konieczne dostęp do pełnego reprezentacji JSON dokument wzbogaconego, natomiast naukowcy i analitycy zechcieć struktur danych szczegółowych lub moduły ukształtowane przez Twojego zestawu umiejętności.

Na przykład jeśli jest jednym z celów procesu wzbogacania będzie również utworzenie zestawu danych używane do trenowania modelu, projekcji danych do magazynu obiektów byłoby jeden ze sposobów użycia danych w potoków do nauki o danych. Alternatywnie Jeśli chcesz tworzyć szybkie pulpitu nawigacyjnego usługi Power BI oparte na dokumentach wzbogaconego tabelarycznych projekcji będzie świetnie.

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

Firma Microsoft zaleca bezpłatna usługa dla celów szkoleniowych, ale należy pamiętać, że liczba bezpłatnych transakcji jest ograniczona do 20 dokumentów każdego dnia na subskrypcję.

Korzystając z wieloma usługami, tworzenie wszystkich usług w tym samym regionie, w celu uzyskania najlepszej wydajności oraz zminimalizować koszty. Nie jest obciążana za przepustowość dla danych przychodzących lub wychodzących dane wprowadzane do innej usługi, w tym samym regionie.

**Krok 1. [Utwórz zasób usługi Azure Search](search-create-service-portal.md)** 

**Krok 2. [Tworzenie konta usługi Azure storage](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal)** 

**Krok 3. [Tworzenie zasobu usług Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)** 

**Krok 4. [Rozpoczynanie pracy z portalem](cognitive-search-quickstart-blob.md) - lub - [Rozpoczynanie pracy z przykładowymi danymi przy użyciu protokołu REST i narzędzia Postman](knowledge-store-howto.md)** 

Możesz użyć REST `api-version=2019-05-06-Preview` do budowy potoku oparte na sztucznej Inteligencji, obejmującą Store wiedzy. W najnowszej wersji zapoznawczej interfejsu API, zawiera obiekt zestawu umiejętności `knowledgeStore` definicji.

## <a name="takeaways"></a>Wnioski

Wiedza Store oferuje wiele korzyści, w tym, ale nie tylko włączenie użycie wzbogaconego dokumentów w scenariuszach niż wyszukiwania, kosztem kontrolek i zarządzanie odejściem od tego stanu w procesie wzbogacania. Te funkcje są dostępne do użycia po prostu przez dodanie konta do przechowywania Twojego zestawu umiejętności oraz przy użyciu języka wyrażeń zaktualizowane zgodnie z opisem w [jak rozpocząć pracę z wiedzy Store](knowledge-store-howto.md). 

## <a name="next-steps"></a>Kolejne kroki

Najprostszą metodą tworzenia wzbogaconego dokumentów jest za pośrednictwem **importowania danych** kreatora.

> [!div class="nextstepaction"]
> [Szybki start: Wypróbuj wyszukiwanie kognitywne w Przewodnik po portalu](cognitive-search-quickstart-blob.md)
