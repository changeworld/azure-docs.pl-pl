---
title: Wprowadzenie do sklepu merytorycznego (wersja zapoznawcza)
titleSuffix: Azure Cognitive Search
description: Wysyłaj wzbogacone dokumenty do usługi Azure Storage, które umożliwiają wyświetlanie, przekształcanie i używanie wzbogaconych dokumentów na platformie Azure Wyszukiwanie poznawcze i w innych aplikacjach. Ta funkcja jest dostępna w publicznej wersji zapoznawczej.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/11/2019
ms.openlocfilehash: 9a6fa62384615f60da88bb41da8ad3538d34e62a
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/08/2020
ms.locfileid: "75754099"
---
# <a name="introduction-to-knowledge-stores-in-azure-cognitive-search"></a>Wprowadzenie do magazynów wiedzy na platformie Azure Wyszukiwanie poznawcze

> [!IMPORTANT] 
> Magazyn wiedzy jest obecnie w publicznej wersji zapoznawczej. Funkcje wersji zapoznawczej są dostępne bez umowy dotyczącej poziomu usług i nie są zalecane w przypadku obciążeń produkcyjnych. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). [Interfejs API REST w wersji 2019-05-06 — wersja zapoznawcza](search-api-preview.md) zapewnia funkcje w wersji zapoznawczej. Dostępna jest obecnie ograniczona obsługa portalu i nie ma obsługi zestawu SDK platformy .NET.

Sklep z bazami informacji to funkcja platformy Azure Wyszukiwanie poznawcze, która utrzymuje dane wyjściowe [potoku wzbogacania AI](cognitive-search-concept-intro.md) na potrzeby niezależnej analizy lub przetwarzania podrzędnego. *Wzbogacony dokument* to dane wyjściowe potoku, utworzone na podstawie zawartości wyodrębnionej, strukturalnej i analizowanej przy użyciu procesów AI. W standardowym potoku AI, wzbogacone dokumenty są nietrwałe, używane tylko podczas indeksowania, a następnie odrzucane. W sklepie merytorycznym chronione dokumenty są zachowywane. 

Jeśli w przeszłości korzystasz z umiejętności poznawczych, już wiesz, że *umiejętności* przenieść dokument przez sekwencję wzbogacania. Wynikiem może być indeks wyszukiwania lub (Nowość w tej wersji zapoznawczej) projekcje w sklepie z bazami danych. Dwa dane wyjściowe, indeks wyszukiwania i magazyn wiedzy są produktami tego samego potoku; pochodzące z tych samych danych wejściowych, ale wynikające z danych wyjściowych, które są strukturalne, przechowywane i używane na różne sposoby.

Fizycznie magazyn wiedzy to [Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-account-overview), Azure Table Storage, Azure Blob Storage lub oba te elementy. Wszystkie narzędzia i procesy, które mogą łączyć się z usługą Azure Storage, mogą zużywać zawartość sklepu z bazami wiedzy.

![Magazyn wiedzy w diagramie potoku](./media/knowledge-store-concept-intro/knowledge-store-concept-intro.svg "Magazyn wiedzy w diagramie potoku")

## <a name="benefits-of-knowledge-store"></a>Zalety sklepu z bazami danych

Magazyn wiedzy oferuje strukturę, kontekst i rzeczywistą zawartość — wydobyć z plików danych bez struktury i z częściową strukturą, takich jak obiekty blob, pliki obrazów, które zostały poddane analizie, a nawet dane strukturalne, Przekształć w nowe formularze. W [instruktażu krok po kroku](knowledge-store-create-rest.md)można zobaczyć, jak w pierwszej kolejności jest podzielony dokument o gęstym formacie JSON na podstruktury, który został przekształcony w nowe struktury i w inny sposób udostępniony dla procesów podrzędnych, takich jak obciążenia maszynowe i analizy danych.

Mimo że warto zobaczyć, co można utworzyć za pomocą potoku wzbogacenia AI, realny potencjał magazynu wiedzy jest możliwość zmiany kształtu danych. Możesz zacząć od podstawowej zestawu umiejętności, a następnie wykonać iterację w celu dodania rosnących poziomów struktury, którą można następnie połączyć do nowych struktur, przydatnych w innych aplikacjach poza platformą Azure Wyszukiwanie poznawcze.

Wyliczane są korzyści z używania sklepu z bazami danych:

+ Korzystaj z ulepszonych dokumentów w [narzędziach do analizy i raportowania](#tools-and-apps) innych niż wyszukiwanie. Power BI z Power Query jest atrakcyjnym wyborem, ale dowolne narzędzie lub aplikacja, która może łączyć się z usługą Azure Storage, może pobierać z utworzonego przez siebie magazynu wiedzy.

+ Uściślij potok indeksowania AI podczas debugowania kroków i definicji zestawu umiejętności. Magazyn wiedzy przedstawia produkt definicji zestawu umiejętności w potoku indeksowania systemu AI. Możesz użyć tych wyników, aby zaprojektować lepszy zestawu umiejętności, ponieważ można zobaczyć dokładnie, jak wyglądają wzbogacenia. Możesz użyć [Eksplorator usługi Storage](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows) w usłudze Azure Storage, aby wyświetlić zawartość sklepu z bazami wiedzy.

+ Kształtowanie danych w nowych formularzach. Zmiana kształtu jest skodyfikowana w umiejętności, ale punkt jest, że zestawu umiejętności może teraz zapewnić tę możliwość. [Umiejętność kształtowania](cognitive-search-skill-shaper.md) w usłudze Azure wyszukiwanie poznawcze została rozszerzona w celu uwzględnienia tego zadania. Zmiana kształtu pozwala zdefiniować projekcję, która jest wyrównana do zamierzonego użycia danych przy zachowaniu relacji.

> [!Note]
> Jesteś nowym sposobem wzbogacania i umiejętności poznawczych? Platforma Azure Wyszukiwanie poznawcze integruje się z funkcjami obsługi Cognitive Services i języka, aby wyodrębnić i wzbogacić dane źródłowe za pomocą optycznego rozpoznawania znaków (OCR) przez pliki obrazów, rozpoznawanie jednostek i wyodrębnianie kluczowych fraz z plików tekstowych. Aby uzyskać więcej informacji, zobacz [wzbogacanie AI na platformie Azure wyszukiwanie poznawcze](cognitive-search-concept-intro.md).

## <a name="physical-storage"></a>Magazyn fizyczny

Wyrażenie fizyczne magazynu wiedzy jest przegubem za pomocą elementu `projections` definicji `knowledgeStore` w zestawu umiejętności. Projekcja definiuje strukturę danych wyjściowych, tak aby była zgodna z zamierzonym użyciem.

Projekcje mogą być łączone jako tabele, obiekty lub pliki.

```json
"knowledgeStore": { 
    "storageConnectionString": "<YOUR-AZURE-STORAGE-ACCOUNT-CONNECTION-STRING>", 
    "projections": [ 
        { 
            "tables": [ ], 
            "objects": [ ], 
            "files": [ ]
        },
                { 
            "tables": [ ], 
            "objects": [ ], 
            "files": [ ]
        }
```

Typ projekcji określony w tej strukturze określa typ magazynu używanego przez magazyn wiedzy.

+ Magazyn tabel jest używany podczas definiowania `tables`. Zdefiniuj projekcję tabeli, gdy potrzebujesz tabelarycznych struktur raportowania dla danych wejściowych w narzędziach analitycznych lub wyeksportować jako ramki danych do innych magazynów danych. Możesz określić wiele `tables`, aby uzyskać podzbiór lub skrzyżowane dokumenty wzbogacone. W ramach tej samej grupy projekcji relacje tabeli są zachowywane, aby można było korzystać ze wszystkich z nich.

+ Magazyn obiektów BLOB jest używany podczas definiowania `objects` lub `files`. Fizyczna reprezentacja `object` jest hierarchiczną strukturą JSON, która reprezentuje wzbogacony dokument. `file` to obraz wyodrębniony z dokumentu, który został przeniesiony do magazynu obiektów BLOB.

Pojedynczy obiekt projekcji zawiera jeden zestaw `tables`, `objects`, `files`i dla wielu scenariuszy, co może być wystarczające. 

Można jednak utworzyć wiele zestawów `table`-`object`-`file` projekcje i można to zrobić, jeśli chcesz mieć różne relacje danych. W zestawie dane są powiązane, przy założeniu, że te relacje istnieją i mogą być wykrywane. W przypadku tworzenia dodatkowych zestawów dokumenty w każdej grupie nigdy nie są powiązane. Przykładem użycia wielu grup projekcji może być, jeśli chcesz, aby te same dane były przeznaczone do użytku z systemem online i muszą być reprezentowane w określony sposób, a także chcesz, aby te same dane były przeznaczone do użycia w potoku analizy danych, który jest reprezentowany otrzymywać.

## <a name="requirements"></a>Wymagania 

[Usługa Azure Storage](https://docs.microsoft.com/azure/storage/) jest wymagana. Zapewnia magazyn fizyczny. Można użyć usługi BLOB Storage, magazynu tabel lub obu. Magazyn obiektów BLOB jest używany w nienaruszonych dokumentach, zazwyczaj gdy dane wyjściowe przechodzą do procesów podrzędnych. Magazyn tabel jest przeznaczony dla wycinków ulepszonych dokumentów, często używanych na potrzeby analizy i raportowania.

[Zestawu umiejętności](cognitive-search-working-with-skillsets.md) jest wymagany. Zawiera definicję `knowledgeStore` i określa strukturę i kompozycję wzbogaconego dokumentu. Nie można utworzyć sklepu z wiedzą przy użyciu pustej zestawu umiejętności. Musisz mieć co najmniej jedną umiejętność w zestawu umiejętności.

Wymagany jest [indeksator](search-indexer-overview.md) . Zestawu umiejętności jest wywoływany przez indeksator, który jest dyskiem wykonania. Indeksatory mają swój własny zestaw wymagań i atrybutów. Niektóre z tych atrybutów mają bezpośredni wpływ na magazyn wiedzy:

+ Indeksatory wymagają [obsługiwanego źródła danych platformy Azure](search-indexer-overview.md#supported-data-sources) (potok, który ostatecznie tworzy magazyn wiedzy, jest uruchamiany przez ściąganie danych z obsługiwanego źródła na platformie Azure). 

+ Indeksatory wymagają indeksu wyszukiwania. Indeksator wymaga podania schematu indeksu, nawet jeśli nie planujesz go używać. Minimalny indeks ma jedno pole ciągu oznaczone jako klucz.

+ Indeksatory zapewniają opcjonalne mapowania pól używane do aliasowania pola źródłowego w polu docelowym. Jeśli domyślne mapowanie pola wymaga modyfikacji (aby użyć innej nazwy lub typu), można utworzyć [Mapowanie pola](search-indexer-field-mappings.md) w indeksatorze. W przypadku danych wyjściowych magazynu wiedzy miejsce docelowe może być polem w obiekcie obiektu BLOB lub tabeli.

+ Indeksatory mają harmonogramy i inne właściwości, takie jak mechanizmy wykrywania zmian zapewniane przez różne źródła danych, mogą być również stosowane do magazynu wiedzy. Na przykład można [zaplanować](search-howto-schedule-indexers.md) wzbogacanie w regularnych odstępach czasu, aby odświeżyć zawartość. 

## <a name="how-to-create-a-knowledge-store"></a>Jak utworzyć sklep merytoryczny

Aby utworzyć magazyn wiedzy, użyj portalu lub interfejsu API REST (`api-version=2019-05-06-Preview`).

### <a name="use-the-azure-portal"></a>Korzystanie z witryny Azure Portal

Kreator **importu danych** zawiera opcje tworzenia sklepu z bazami informacji. W przypadku początkowej eksploracji [Utwórz swój pierwszy magazyn wiedzy w czterech krokach](knowledge-store-connect-power-bi.md).

1. Wybierz obsługiwane źródło danych.

1. Określ wzbogacanie: dołączanie zasobu, wybieranie umiejętności i określanie sklepu merytorycznego. 

1. Utwórz schemat indeksu. Kreator wymaga go i może wywnioskować go dla Ciebie.

1. Uruchom kreatora. W tym ostatnim kroku występuje Ekstrakcja, wzbogacanie i magazyn.

### <a name="use-create-skillset-and-the-preview-rest-api"></a>Korzystanie z interfejsu API REST Create zestawu umiejętności i wersji zapoznawczej

`knowledgeStore` jest zdefiniowany w [zestawu umiejętności](cognitive-search-working-with-skillsets.md), który z kolei jest wywoływany przez [indeksator](search-indexer-overview.md). Podczas wzbogacania usługa Azure Wyszukiwanie poznawcze tworzy miejsce na koncie usługi Azure Storage i projektuje wzbogacone dokumenty jako obiekty blob lub tabele, w zależności od konfiguracji.

Obecnie interfejs API REST w wersji zapoznawczej jest jedynym mechanizmem, za pomocą którego można programowo utworzyć magazyn wiedzy. Prostą metodą eksplorowania jest [utworzenie pierwszego sklepu z bazami danych przy użyciu programu Poster i interfejsu API REST](knowledge-store-create-rest.md).

Zawartość referencyjna tej funkcji w wersji zapoznawczej znajduje się w sekcji [Dokumentacja interfejsu API](#kstore-rest-api) w tym artykule. 

<a name="tools-and-apps"></a>

## <a name="how-to-connect-with-tools-and-apps"></a>Jak nawiązać połączenie z narzędziami i aplikacjami

Po wprowadzeniu wzbogaceń do magazynu dowolne narzędzie lub technologia, która łączy się z obiektem blob platformy Azure lub magazynem tabel, może służyć do eksplorowania, analizowania lub korzystania z zawartości. Na poniższej liście przedstawiono początek:

+ [Eksplorator usługi Storage](knowledge-store-view-storage-explorer.md) , aby wyświetlić wzbogaconą strukturę dokumentu i zawartość. Rozważmy to jako narzędzie podstawowe do wyświetlania zawartości w sklepie z bazami danych.

+ [Power BI](knowledge-store-connect-power-bi.md) na potrzeby raportowania i analizy. 

+ [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/) dalsze manipulowanie.

<a name="kstore-rest-api"></a>

## <a name="api-reference"></a>Dokumentacja interfejsów API

Ta sekcja jest wersją dokumentu referencyjnego [Create zestawu umiejętności (interfejs API REST)](https://docs.microsoft.com/rest/api/searchservice/create-skillset) zmodyfikowaną w celu uwzględnienia definicji `knowledgeStore`. 

### <a name="example---knowledgestore-embedded-in-a-skillset"></a>Przykład — knowledgeStore osadzony w zestawu umiejętności

Poniższy przykład pokazuje `knowledgeStore` w dolnej części definicji zestawu umiejętności. 

* Użyj **wpisu post** lub **Put** , aby sformułować żądanie.
* Użyj `api-version=2019-05-06-Preview` wersji interfejsu API REST, aby uzyskać dostęp do funkcji sklepu z bazami danych. 

```http
POST https://[servicename].search.windows.net/skillsets?api-version=2019-05-06-Preview
api-key: [admin key]
Content-Type: application/json
```

Treść żądania jest dokumentem JSON, który definiuje zestawu umiejętności, który obejmuje `knowledgeStore`.

```json
{
  "name": "my-skillset-name",
  "description": "Extract organization entities and generate a positive-negative sentiment score from each document.",
  "skills":
  [
    {
      "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
      "categories": [ "Organization" ],
      "defaultLanguageCode": "en",
      "inputs": [
        {
          "name": "text",
          "source": "/document/content"
        }
      ],
      "outputs": [
        {
          "name": "organizations",
          "targetName": "organizations"
        }
      ]
    },
    {
      "@odata.type": "#Microsoft.Skills.Text.SentimentSkill",
      "inputs": [
        {
          "name": "text",
          "source": "/document/content"
        }
      ],
      "outputs": [
        {
          "name": "score",
          "targetName": "mySentiment"
        }
      ]
    },
  ],
  "cognitiveServices": 
    {
    "@odata.type": "#Microsoft.Azure.Search.CognitiveServicesByKey",
    "description": "mycogsvcs resource in West US 2",
    "key": "<YOUR-COGNITIVE-SERVICES-KEY>"
    },
    "knowledgeStore": { 
        "storageConnectionString": "<YOUR-AZURE-STORAGE-ACCOUNT-CONNECTION-STRING>", 
        "projections": [ 
            { 
                "tables": [  
                { "tableName": "Organizations", "generatedKeyName": "OrganizationId", "source": "/document/organizations*"}, 
                { "tableName": "Sentiment", "generatedKeyName": "SentimentId", "source": "/document/mySentiment"}
                ], 
                "objects": [ ], 
                "files": [  ]       
            }    
        ]     
    } 
}
```

### <a name="request-body-syntax"></a>Składnia treści żądania  

Poniższy kod JSON określa `knowledgeStore`, który jest częścią [`skillset`](https://docs.microsoft.com/rest/api/searchservice/create-skillset), który jest wywoływany przez `indexer` (niepokazywany). Jeśli masz już doświadczenie w korzystaniu z wzbogacania AI, zestawu umiejętności określa kompozycję wzbogaconego dokumentu. Zestawu umiejętności musi zawierać co najmniej jedną umiejętność, najprawdopodobniej umiejętność kształtowania, jeśli są modulowane struktury danych.

Składnia do tworzenia struktury ładunku żądania jest następująca.

```json
{   
    "name" : "Required for POST, optional for PUT requests which sets the name on the URI",  
    "description" : "Optional. Anything you want, or null",  
    "skills" : "Required. An array of skills. Each skill has an odata.type, name, input and output parameters",
    "cognitiveServices": "A key to Cognitive Services, used for billing.",
    "knowledgeStore": { 
        "storageConnectionString": "<YOUR-AZURE-STORAGE-ACCOUNT-CONNECTION-STRING>", 
        "projections": [ 
            { 
                "tables": [ 
                    { "tableName": "<NAME>", "generatedKeyName": "<FIELD-NAME>", "source": "<DOCUMENT-PATH>" },
                    { "tableName": "<NAME>", "generatedKeyName": "<FIELD-NAME>", "source": "<DOCUMENT-PATH>" },
                    . . .
                ], 
                "objects": [ 
                    {
                    "storageContainer": "<BLOB-CONTAINER-NAME>", 
                    "source": "<DOCUMENT-PATH>", 
                    }
                ], 
                "files": [ 
                    {
                    "storageContainer": "<BLOB-CONTAINER-NAME>",
                    "source": "/document/normalized_images/*"
                    }
                ]  
            },
            {
                "tables": [ ],
                "objects": [ ],
                "files":  [ ]
            }  
        ]     
    } 
}
```

`knowledgeStore` ma dwie właściwości: `storageConnectionString` do konta usługi Azure Storage, a `projections` definiujący magazyn fizyczny. Możesz użyć dowolnego konta magazynu, ale jest to ekonomiczne, aby korzystać z usług w tym samym regionie.

Kolekcja `projections` zawiera obiekty projekcji. Każdy obiekt projekcji musi mieć `tables`, `objects`, `files` (jeden z nich), który jest określony lub ma wartość null. Powyższa składnia zawiera dwa obiekty, jeden w pełni określony, a druga pełna wartość null. W obiekcie projekcji, gdy zostanie on wyrażony w magazynie, wszelkie relacje między danymi, jeśli zostały wykryte, są zachowywane. 

Utwórz tyle obiektów projekcji, ile potrzebujesz, aby obsługiwać izolację i konkretne scenariusze (na przykład struktury danych używane do eksploracji, a także te, które są potrzebne w obciążeniu analizy danych). Możesz uzyskać izolację i dostosowanie dla konkretnych scenariuszy, ustawiając `source` i `storageContainer` lub `table` na różne wartości w obiekcie. Aby uzyskać więcej informacji i przykładów, zobacz [Praca z projekcjami w sklepie z bazami](knowledge-store-projection-overview.md)danych.

|Właściwość      | Informacje zawarte w tym artykule dotyczą | Opis|  
|--------------|------------|------------|  
|`storageConnectionString`| `knowledgeStore` | Wymagany. W tym formacie: `DefaultEndpointsProtocol=https;AccountName=<ACCOUNT-NAME>;AccountKey=<ACCOUNT-KEY>;EndpointSuffix=core.windows.net`|  
|`projections`| `knowledgeStore` | Wymagany. Kolekcja obiektów właściwości składająca się z `tables`, `objects`, `files` i ich odpowiednich właściwości. Nieużywane projekcje można ustawić na wartość null.|  
|`source`| Wszystkie projekcje| Ścieżka do węzła drzewa wzbogacania, który jest elementem głównym projekcji. Ten węzeł jest wynikiem dowolnych umiejętności w zestawu umiejętności. Ścieżki rozpoczynają się od `/document/`, reprezentującego wzbogacony dokument, ale można je rozszerzyć do `/document/content/` lub do węzłów w drzewie dokumentu. Przykłady: `/document/countries/*` (wszystkie kraje) lub `/document/countries/*/states/*` (wszystkie Stany we wszystkich krajach). Aby uzyskać więcej informacji na temat ścieżek dokumentów, zobacz [pojęcia i kompozycje zestawu umiejętności](cognitive-search-working-with-skillsets.md).|
|`tableName`| `tables`| Tabela do utworzenia w usłudze Azure Table Storage. |
|`storageContainer`| `objects`, `files`| Nazwa kontenera do utworzenia w usłudze Azure Blob Storage. |
|`generatedKeyName`| `tables`| Kolumna utworzona w tabeli, która jednoznacznie identyfikuje dokument. Potok wzbogacania wypełnia tę kolumnę wygenerowanymi wartościami.|


### <a name="response"></a>Odpowiedź  

 W przypadku pomyślnego żądania powinien zostać wyświetlony kod stanu "201 utworzony". Domyślnie treść odpowiedzi będzie zawierać kod JSON dla utworzonej definicji zestawu umiejętności. Odwołaj, że magazyn wiedzy nie jest tworzony do momentu wywołania indeksatora, który odwołuje się do tego zestawu umiejętności.

## <a name="next-steps"></a>Następne kroki

Magazyn wiedzy oferuje trwałość ulepszonych dokumentów, przydatnych podczas projektowania zestawu umiejętności lub tworzenia nowych struktur i zawartości do użycia przez dowolne aplikacje klienckie, które mogą uzyskać dostęp do konta usługi Azure Storage.

Najprostszym podejściem do tworzenia wzbogaconych dokumentów jest użycie portalu, ale można również użyć [programu](knowledge-store-create-portal.md)Poster i interfejsu API REST, który jest bardziej przydatny, jeśli chcesz uzyskać wgląd w sposób tworzenia obiektów i odwoływania się do nich.

> [!div class="nextstepaction"]
> [Tworzenie sklepu z wiedzą przy użyciu programu Poster i REST](knowledge-store-create-rest.md)
