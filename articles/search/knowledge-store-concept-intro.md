---
title: Wprowadzenie do magazynu wiedzy (wersja zapoznawcza)
titleSuffix: Azure Cognitive Search
description: Wysyłaj wzbogacone dokumenty do usługi Azure Storage, gdzie można wyświetlać, przekształcać i wykorzystywać wzbogacone dokumenty w usłudze Azure Cognitive Search i w innych aplikacjach. Ta funkcja jest dostępna w publicznej wersji zapoznawczej.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/11/2019
ms.openlocfilehash: 0ad780c04954c09ddfd432b3c7de3dc65f0841bf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78942999"
---
# <a name="introduction-to-knowledge-stores-in-azure-cognitive-search"></a>Wprowadzenie do magazynów wiedzy w usłudze Azure Cognitive Search

> [!IMPORTANT] 
> Magazyn wiedzy jest obecnie w publicznej wersji zapoznawczej. Funkcja w wersji zapoznawczej jest dostarczana bez umowy dotyczącej poziomu usług i nie jest zalecana dla obciążeń produkcyjnych. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Interfejs [API REST w wersji 2019-05-06-Preview](search-api-preview.md) udostępnia funkcje w wersji zapoznawczej. Obecnie istnieje ograniczona obsługa portalu i nie ma obsługi sdk .NET.

Magazyn wiedzy to funkcja usługi Azure Cognitive Search, która utrzymuje dane wyjściowe z [potoku wzbogacania sztucznej inteligencji](cognitive-search-concept-intro.md) do niezależnej analizy lub przetwarzania podrzędnego. *Wzbogacony dokument* to dane wyjściowe potoku, utworzone na podstawie zawartości, która została wyodrębniona, ustrukturyzowana i przeanalizowana przy użyciu procesów AI. W standardowym potoku AI wzbogacone dokumenty są przejściowe, używane tylko podczas indeksowania, a następnie odrzucane. Dzięki magazynowi wiedzy zachowane są wzbogacone dokumenty. 

Jeśli używałeś umiejętności poznawczych w przeszłości, już wiesz, że *skillsets* przenieść dokument poprzez sekwencję wzbogacenia. Wynikiem może być indeks wyszukiwania lub (nowy w tej wersji zapoznawczej) projekcje w magazynie wiedzy. Dwa wyjścia, indeks wyszukiwania i magazyn wiedzy, są produktami tego samego potoku; pochodzi z tych samych danych wejściowych, ale powoduje wyjście, które jest ustrukturyzowane, przechowywane i używane na bardzo różne sposoby.

Fizycznie magazyn wiedzy to [usługa Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-account-overview), usługa Azure Table storage, magazyn obiektów Blob platformy Azure lub oba te elementy. Każde narzędzie lub proces, który można połączyć z usługą Azure Storage może korzystać z zawartości magazynu wiedzy.

![Magazyn wiedzy w diagramie potoku](./media/knowledge-store-concept-intro/knowledge-store-concept-intro.svg "Magazyn wiedzy w diagramie potoku")

## <a name="benefits-of-knowledge-store"></a>Korzyści z magazynu wiedzy

Magazyn wiedzy zapewnia strukturę, kontekst i rzeczywistą zawartość — zebrane z nieustrukturyzowanych i częściowo ustrukturyzowanych plików danych, takich jak obiekty blob, pliki obrazów, które zostały poddane analizie, a nawet dane strukturalne, przekształcone w nowe formy. W [przewodniku krok po kroku](knowledge-store-create-rest.md)można zobaczyć z pierwszej ręki, jak gęsty dokument JSON jest podzielony na podstruktury, odtworzony na nowe struktury i w inny sposób udostępniony dla procesów podrzędnych, takich jak uczenie maszynowe i analizy danych.

Chociaż warto zobaczyć, co może produkować potok wzbogacania ai, prawdziwym potencjałem magazynu wiedzy jest możliwość przekształcenia danych. Możesz zacząć od podstawowego zestawu umiejętności, a następnie iterować nad nim, aby dodać rosnący poziom struktury, który można następnie połączyć w nowe struktury, materiały eksploatacyjne w innych aplikacjach oprócz usługi Azure Cognitive Search.

Wyliczone, korzyści z magazynu wiedzy są następujące:

+ Korzystaj ze wzbogaconych dokumentów w [narzędziach analitycznych i raportowania](#tools-and-apps) innych niż wyszukiwanie. Usługa Power BI z dodatkiem Power Query to atrakcyjny wybór, ale każde narzędzie lub aplikacja, które można połączyć z usługą Azure Storage, może pobierać dane z utworzonego magazynu wiedzy.

+ Uściślanie potoku indeksowania AI podczas debugowania kroków i definicji zestawów umiejętności. Magazyn wiedzy pokazuje produkt definicji zestawu umiejętności w potoku indeksowania AI. Możesz użyć tych wyników, aby zaprojektować lepszy zestaw umiejętności, ponieważ możesz zobaczyć dokładnie, jak wyglądają wzbogacenia. [Explorer magazynu w usłudze](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows) Azure Storage służy do wyświetlania zawartości magazynu wiedzy.

+ Kształtuj dane w nowe formularze. Zmiana kształtu jest skodyfikowana w skillsets, ale chodzi o to, że zestaw umiejętności może teraz zapewnić tę zdolność. Umiejętności [Shaper](cognitive-search-skill-shaper.md) w usłudze Azure Cognitive Search zostały rozszerzone, aby uwzględnić to zadanie. Zmiana kształtu umożliwia zdefiniowanie projekcji, która jest zgodna z zamierzonym użyciem danych przy jednoczesnym zachowaniu relacji.

> [!Note]
> Jesteś nowym nowym wcielenia si i umiejętności poznawczych? Usługa Azure Cognitive Search integruje się z funkcjami programu Cognitive Services Vision i Language w celu wyodrębniania i wzbogacania danych źródłowych przy użyciu optycznego rozpoznawania znaków (OCR) za pomocą plików obrazów, rozpoznawania jednostek i wyodrębniania fraz kluczowych z plików tekstowych i innych. Aby uzyskać więcej informacji, zobacz [wzbogacanie sztucznej inteligencji w usłudze Azure Cognitive Search](cognitive-search-concept-intro.md).

## <a name="physical-storage"></a>Pamięć fizyczna

Fizyczna ekspresja magazynu wiedzy jest `projections` artykułowana `knowledgeStore` przez element definicji w skillset. Projekcja definiuje strukturę danych wyjściowych, tak aby odpowiadała zamierzonemu użyciu.

Projekcje mogą być wyrażone jako tabele, obiekty lub pliki.

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

+ Magazyn tabel jest używany `tables`podczas definiowania . Zdefiniuj projekcję tabeli, gdy potrzebne są struktury raportowania tabelarycznego dla danych wejściowych do narzędzi analitycznych lub eksportowanie jako ramek danych do innych magazynów danych. Można określić `tables` wiele, aby uzyskać podzbiór lub przekrój poprzeczny wzbogaconych dokumentów. W tej samej grupie rzutowania relacje tabel są zachowywane, dzięki czemu można pracować ze wszystkimi z nich.

+ Magazyn obiektów blob jest `objects` `files`używany podczas definiowania lub . Fizyczna reprezentacja `object` jest hierarchiczną strukturą JSON, która reprezentuje wzbogacony dokument. A `file` jest obrazem wyodrębniony z dokumentu, przeniesione w stanie nienaruszonym do magazynu obiektów Blob.

Pojedynczy obiekt rzutowania zawiera `tables` `objects`jeden `files`zestaw , , i dla wielu scenariuszy, tworzenie jednej projekcji może być wystarczające. 

Jednak istnieje możliwość utworzenia wielu `table` - `object` - `file` zestawów projekcji i można to zrobić, jeśli chcesz różnych relacji danych. W ramach zestawu dane są powiązane, przy założeniu, że te relacje istnieją i mogą być wykryte. Jeśli utworzysz dodatkowe zestawy, dokumenty w każdej grupie nigdy nie są powiązane. Przykładem użycia wielu grup projekcji może być, jeśli chcesz, aby te same dane były wyświetlane do użycia z systemem online i muszą być reprezentowane w określony sposób, chcesz również te same dane przewidywane do użycia w potoku nauki o danych, który jest reprezentowany Inaczej.

## <a name="requirements"></a>Wymagania 

[Usługa Azure Storage](https://docs.microsoft.com/azure/storage/) jest wymagana. Zapewnia pamięć fizyczną. Można użyć magazynu obiektów Blob, magazynu tabel lub obu. Magazyn obiektów blob jest używany dla nienaruszonych wzbogaconych dokumentów, zwykle, gdy dane wyjściowe idą do procesów niższego rzędu. Przechowywanie tabel jest przeznaczone dla wycinków wzbogaconych dokumentów, powszechnie używanych do analizy i raportowania.

[Skillset](cognitive-search-working-with-skillsets.md) jest wymagane. Zawiera definicję `knowledgeStore` i określa strukturę i skład wzbogaconego dokumentu. Nie można utworzyć magazynu wiedzy przy użyciu pustego zestawu umiejętności. Musisz mieć co najmniej jedną umiejętność w zestaw umiejętności.

Wymagany jest [indeksator.](search-indexer-overview.md) Zestaw umiejętności jest wywoływany przez indeksatora, który napędza wykonanie. Indeksatory pochodzą z własnym zestawem wymagań i atrybutów. Kilka z tych atrybutów ma bezpośredni wpływ na magazyn wiedzy:

+ Indeksatory wymagają [obsługiwanego źródła danych platformy Azure](search-indexer-overview.md#supported-data-sources) (potok, który ostatecznie tworzy magazyn wiedzy, rozpoczyna się od ściągania danych z obsługiwanego źródła na platformie Azure). 

+ Indeksatory wymagają indeksu wyszukiwania. Indeksator wymaga podania schematu indeksu, nawet jeśli nigdy nie planujesz go używać. Minimalny indeks ma jedno pole ciągu, wyznaczone jako klucz.

+ Indeksatory zapewniają opcjonalne mapowania pól, używane do aliasowania pola źródłowego do pola docelowego. Jeśli domyślne mapowanie pól wymaga modyfikacji (aby użyć innej nazwy lub typu), można utworzyć [mapowanie pól](search-indexer-field-mappings.md) w indeksatorze. W przypadku danych wyjściowych magazynu wiedzy miejscem docelowym może być pole w obiekcie obiektu blob lub tabeli.

+ Indeksatory mają harmonogramy i inne właściwości, takie jak mechanizmy wykrywania zmian dostarczane przez różne źródła danych, mogą być również stosowane do magazynu wiedzy. Na przykład można [zaplanować](search-howto-schedule-indexers.md) wzbogacanie w regularnych odstępach czasu, aby odświeżyć zawartość. 

## <a name="how-to-create-a-knowledge-store"></a>Jak utworzyć magazyn wiedzy

Aby utworzyć magazyn wiedzy, użyj portalu lub`api-version=2019-05-06-Preview`interfejsu API REST w wersji zapoznawczej ( ).

### <a name="use-the-azure-portal"></a>Korzystanie z witryny Azure Portal

Kreator **importu danych** zawiera opcje tworzenia magazynu wiedzy. Aby uzyskać wstępną eksplorację, [utwórz pierwszy magazyn wiedzy w czterech krokach.](knowledge-store-connect-power-bi.md)

1. Wybierz obsługiwane źródło danych.

1. Określ wzbogacenie: dołącz zasób, wybierz umiejętności i określ magazyn wiedzy. 

1. Tworzenie schematu indeksu. Kreator tego wymaga i może wywnioskować jeden dla Ciebie.

1. Uruchom kreatora. Ekstrakcji, wzbogacania i przechowywania występują w tym ostatnim kroku.

### <a name="use-create-skillset-and-the-preview-rest-api"></a>Użyj create skillset i interfejsu API REST w wersji zapoznawczej

A `knowledgeStore` jest zdefiniowany w obrębie [skillset](cognitive-search-working-with-skillsets.md), który z kolei jest wywoływany przez [indeksatora](search-indexer-overview.md). Podczas wzbogacania usługi Azure Cognitive Search tworzy miejsce na koncie usługi Azure Storage i projektuje wzbogacone dokumenty jako obiekty blob lub w tabelach, w zależności od konfiguracji.

Obecnie interfejs API REST w wersji zapoznawczej jest jedynym mechanizmem, za pomocą którego można programowo utworzyć magazyn wiedzy. Łatwym sposobem na eksplorowanie jest [stworzenie pierwszego magazynu wiedzy za pomocą postmana i interfejsu API REST.](knowledge-store-create-rest.md)

Zawartość referencyjna dla tej funkcji w wersji zapoznawczej znajduje się w sekcji [odwołania interfejsu API](#kstore-rest-api) w tym artykule. 

<a name="tools-and-apps"></a>

## <a name="how-to-connect-with-tools-and-apps"></a>Jak połączyć się z narzędziami i aplikacjami

Gdy wzbogacenia istnieją w magazynie, dowolne narzędzie lub technologia, która łączy się z usługą Azure Blob lub magazynu tabel może służyć do eksplorowania, analizowania lub korzystania z zawartości. Poniższa lista jest początkiem:

+ [Eksplorator magazynu](knowledge-store-view-storage-explorer.md) do wyświetlania wzbogaconej struktury i zawartości dokumentu. Należy wziąć to pod uwagę jako narzędzie linii bazowej do przeglądania zawartości magazynu wiedzy.

+ [Usługa Power BI](knowledge-store-connect-power-bi.md) do raportowania i analizy. 

+ [Usługa Azure Data Factory](https://docs.microsoft.com/azure/data-factory/) do dalszej manipulacji.

<a name="kstore-rest-api"></a>

## <a name="api-reference"></a>Odwołanie API

Wersja `2019-05-06-Preview` interfejsu API REST zapewnia magazyn wiedzy za pomocą dodatkowych definicji zestawów umiejętności. Oprócz odwołania, zobacz [Tworzenie magazynu wiedzy przy użyciu postman,](knowledge-store-create-rest.md) aby uzyskać szczegółowe informacje na temat sposobu wywoływania interfejsów API.

+ [Tworzenie skillset (api-version=2019-05-06-Preview)](https://docs.microsoft.com/rest/api/searchservice/2019-05-06-preview/create-skillset) 
+ [Aktualizacja Skillset (api-version=2019-05-06-Preview)](https://docs.microsoft.com/rest/api/searchservice/2019-05-06-preview/update-skillset) 


## <a name="next-steps"></a>Następne kroki

Magazyn wiedzy oferuje trwałość wzbogaconych dokumentów, przydatne podczas projektowania zestaw umiejętności lub tworzenie nowych struktur i zawartości do użycia przez aplikacje klienckie mogące uzyskać dostęp do konta usługi Azure Storage.

Najprostszym podejściem do tworzenia wzbogaconych dokumentów jest [za pośrednictwem portalu](knowledge-store-create-portal.md), ale można również użyć interfejsu API postmana i REST, co jest bardziej przydatne, jeśli chcesz uzyskać wgląd w sposób tworzenia obiektów i odwoływania się do nich.

> [!div class="nextstepaction"]
> [Tworzenie magazynu wiedzy przy użyciu listonosza i REST](knowledge-store-create-rest.md)

Aby dowiedzieć się więcej o projekcjach, możliwościach i sposobie [definiowania ich w](knowledge-store-projection-overview.md)

> [!div class="nextstepaction"]
> [Projekcje w magazynie wiedzy](knowledge-store-projection-overview.md)

W przypadku samouczka obejmującego zaawansowane koncepcje projekcji, takie jak krojenie, kształtowanie w linii i relacje, zacznij od [zdefiniowania projekcji w magazynie wiedzy](knowledge-store-projections-examples.md)

> [!div class="nextstepaction"]
> [Definiowanie projekcji w magazynie wiedzy](knowledge-store-projections-examples.md)