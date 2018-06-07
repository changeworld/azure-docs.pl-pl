---
title: Wyszukiwanie kognitywnych wyodrębniania danych, języka naturalnego przetwarzania w usłudze Azure Search | Dokumentacja firmy Microsoft
description: Wyodrębnianie danych, przetwarzanie języka naturalnego (NLP) i przetwarzania obrazu do tworzenia zawartości można wyszukiwać w usłudze Azure Search indeksowanie za pomocą kognitywnych umiejętności.
manager: cgronlun
author: HeidiSteen
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 05/04/2018
ms.author: heidist
ms.openlocfilehash: ca6c285348208a7ad24faf966073d641810039fc
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34641115"
---
# <a name="what-is-cognitive-search"></a>Co to jest kognitywnych wyszukiwania?

Wyszukiwanie kognitywnych jest funkcja w wersji zapoznawczej [usługi Azure Search](search-what-is-azure-search.md), są dostępne na wszystkich warstw w południowo-środkowe Stany i Europa Zachodnia, który dodaje AI do indeksowania obciążeń. Wyodrębniania danych, przetwarzanie języka naturalnego i przetwarzania obrazu podczas indeksowania odnajduje informacje ukryty w zawartości bez struktury lub niemożliwych i umożliwia wyszukiwanie w usłudze Azure Search.

Integracja AI jest za pośrednictwem *kognitywnych umiejętności* który wzbogacić źródła dokumentów za pomocą procesów sekwencyjnych, na trasie do indeksu wyszukiwania. 

![Diagram potoku wyszukiwania kognitywnych](./media/cognitive-search-intro/cogsearch-architecture.png "omówienie potoku kognitywnych wyszukiwania")

Używane podczas indeksowania umiejętności można wstępnie zdefiniowanych lub niestandardowych:

+ [Prefined umiejętności](cognitive-search-predefined-skills.md) są oparte na tej samej AI algorytmów w kognitywnych interfejsów API usługi: [rozpoznawanie jednostek o nazwie](cognitive-search-skill-named-entity-recognition.md), [wyodrębniania frazy klucza](cognitive-search-skill-keyphrases.md), i [Rozpoznawania](cognitive-search-skill-ocr.md) jest kilka. 

+ [Niestandardowe umiejętności](cognitive-search-create-custom-skill-example.md) mogą być opracowane przez Ciebie wszelkie specjalne przetwarzania wymagany. Przykłady niestandardowych umiejętności może być przeznaczonych dla określonej domeny, takie jak finance, publikacji naukowych lub medycznych klasyfikatora modułu lub dokument niestandardowej jednostki.

> [!NOTE]
> Usługa Cognitive Search jest w publicznej wersji zapoznawczej i wykonywanie zestawu umiejętności jest obecnie oferowane bezpłatnie. Cennik dla tej możliwości zostanie opublikowany w późniejszym czasie.

## <a name="components-of-cognitive-search"></a>Składniki wyszukiwania kognitywnych

Potok kognitywnych wyszukiwania jest oparta na [usługi Azure Search *indeksatory* ](search-indexer-overview.md) przeszukiwania źródeł danych i zapewnienia przetwarzania indeksu end-to-end. Umiejętności teraz są dołączone do indeksatory przechwytywaniu i wzbogacenie dokumentów na podstawie skillset zdefiniować. Po indeksowane, mają dostęp do zawartości za pomocą żądań wyszukiwania przez wszystkie [zapytania typy obsługiwanych przez usługi Azure Search](search-query-overview.md).  Jeśli jesteś nowym użytkownikiem indeksatorów, ta sekcja przeprowadzi Cię przez kroki.

### <a name="source-data-and-document-cracking-phase"></a>Źródło danych i zarządzania dokumentami łamania fazy

Na początku potoku, masz tekstu bez struktury lub zawartości nietekstowych (na przykład obraz i pliki JPEG skanowanego dokumentu). Danych musi istnieć usługi magazynu danych Azure, który można uzyskać, sprawdzając indeksatora. Indeksatory można "złamanie" dokumenty źródłowe można wyodrębnić tekst ze źródła danych.

![Dokument łamania fazy](./media/cognitive-search-intro/document-cracking-phase-blowup.png "łamania dokumentu")

 Obsługiwanych źródeł obejmują magazynu obiektów blob platformy Azure, Magazyn tabel Azure, baza danych SQL Azure i bazy danych Azure rozwiązania Cosmos. Można wyodrębnić tekstową z następujących typów plików: pliki PDF, Word, PowerPoint, plików CSV. Aby uzyskać pełną listę, zobacz [obsługiwane formaty](search-howto-indexing-azure-blob-storage.md#supported-document-formats).

### <a name="cognitive-skills-and-enrichment-phase"></a>Kognitywnych umiejętności i wzbogacenia fazy

Wzbogacenia odbywa się za pośrednictwem *kognitywnych umiejętności* atomic operacji. Na przykład gdy zawartość tekstu z pliku PDF, można użyć wykrywania języka rozpoznawania jednostek lub wyodrębniania klucza frazę do tworzenia nowych pól w indeksie, które nie są dostępne natywnie w źródle. Nosi nazwę kolekcji umiejętności używane planowaną *skillset*.  

![Faza wzbogacenia](./media/cognitive-search-intro/enrichment-phase-blowup.png "wzbogacenia fazy")

Skillset jest oparta na [wstępnie zdefiniowane umiejętności kognitywnych](cognitive-search-predefined-skills.md) lub [niestandardowych umiejętności](cognitive-search-create-custom-skill-example.md) Podaj i połącz się skillset. Skillset może być minimalny lub skomplikowanej i określa nie tylko typ przetwarzania, ale również kolejność operacji. Skillset plus zdefiniowane jako część indeksatora pełni określa potoku wzbogacenia mapowania pól. Aby uzyskać więcej informacji na temat ściąganie wszystkie te elementy jednocześnie, zobacz [zdefiniować skillset](cognitive-search-defining-skillset.md).

Wewnętrznie potoku generuje kolekcji wzbogaconego dokumentów. Można zdecydować, które części dokumentów wzbogaconego powinien być zamapowany na indeksowalnych pól w indeksie wyszukiwania. Na przykład po zastosowaniu wyodrębniania klucza fraz i umiejętności rozpoznawania jednostek, następnie te nowe pola stanie się częścią dokumentu wzbogaconego i mogą być mapowane do pól w indeksie. Zobacz [adnotacje](cognitive-search-concept-annotations-syntax.md) Aby dowiedzieć się więcej na temat Tworzenie wejścia/wyjścia.

### <a name="search-index-and-query-based-access"></a>Indeks wyszukiwania i dostępu na podstawie zapytania

Po zakończeniu przetwarzania masz Boże wyszukiwania, składające się z wzbogaconego dokumentów, tekst przeszukiwać w usłudze Azure Search. [Wykonywanie zapytania dotyczącego indeksu](search-query-overview.md) jest sposób deweloperom i użytkownikom uzyskiwanie dostępu do zawartości wzbogaconego generowane przez potok. 

![Indeks o ikonę wyszukiwania](./media/cognitive-search-intro/search-phase-blowup.png "indeks o ikonę wyszukiwania")

Indeks działa jak inne można utworzyć dla usługi wyszukiwanie Azure: można uzupełnić z niestandardowych analizatorów, wywołania zapytania wyszukiwania rozmytego, Dodaj wyszukiwanie filtrowane lub eksperymentować oceniania profilów, aby przekształcić w wynikach wyszukiwania.

Indeksy zostaną wygenerowane na podstawie schematu indeksu, który definiuje pól atrybutów, i innych konstrukcji dołączony do konkretnego indeksu, takich jak profile oceniania synonim mapy. Gdy indeks jest zdefiniowany i wypełnione, można zaindeksować przyrostowo do pobrania dokumentów źródłowych nowe i zaktualizowane. Niektóre zmiany wymagają ponownej pełnej kompilacji. Należy używać niewielki zestaw danych, aż do projektu schematu jest stabilna. Aby uzyskać więcej informacji, zobacz [Jak odbudować indeks](search-howto-reindex.md).

<a name="feature-concepts"></a>

## <a name="key-features-and-concepts"></a>Kluczowe funkcje i pojęcia

| Pojęcie | Opis| Linki |
|---------|------------|-------|
| Skillset | Najwyższego poziomu o nazwie zasób zawierający kolekcję umiejętności. Skillset jest wzbogacenia potoku. Jest wywoływana podczas indeksowania według indeksatora. | [Zdefiniuj skillset](cognitive-search-defining-skillset.md) |
| Kognitywnych umiejętności | Niepodzielne transformacji w potoku wzbogacenia. Często jest składnikiem, który wyodrębnia lub wnioskuje struktury i w związku z tym wspomaga zrozumienie danych wejściowych. Prawie zawsze dane wyjściowe są oparte na tekst i przetwarzania jest przetwarzania języka naturalnego lub przetwarzania obrazu, który wyodrębnia lub generuje tekstu obrazu danych wejściowych. Dane wyjściowe z umiejętności mogą być zamapowane do pola w indeksie lub używane jako dane wejściowe dla wzbogacenia podrzędne. Wstępnie zdefiniowane i udostępniane przez firmę Microsoft lub niestandardowe umiejętności: generowanych i wdrażanych przez użytkownika. | [Wstępnie zdefiniowane umiejętności](cognitive-search-predefined-skills.md) |
| Wyodrębnianie danych | Obejmuje szeroką gamę przetwarzania, ale odnoszące się do wyszukiwania kognitywnych, umiejętności rozpoznawania nazwanej jednostki jest najczęściej używany do wyodrębniania danych (jednostki) ze źródła, która nie zapewnia natywnej tych informacji. | [Umiejętności rozpoznawania nazwanej jednostki](cognitive-search-skill-named-entity-recognition.md)| 
| Przetwarzanie obrazu | Wnioskuje tekstu z obrazu, takie jak możliwość rozpoznaje dzielnicę lub wyodrębnia tekst z obrazu. Typowe przykłady obejmują Rozpoznawania do podnoszenia znaków z pliku skanowanego dokumentu (JPEG), lub rozpoznawanie nazwy ulicy w fotografii zawierających znak ulicy. | [Obraz umiejętności analizy](cognitive-search-skill-image-analysis.md) lub [umiejętności Rozpoznawania](cognitive-search-skill-ocr.md)
| Przetwarzanie języka naturalnego | Tekst przetwarzania insights i informacje o danych wejściowych tekstu. Wykrywanie języka, analizy wskaźniki nastrojów klientów i wyodrębniania klucza frazy są umiejętności, które są objęte przetwarzania języka naturalnego.  | [Klucz umiejętności wyodrębniania frazy](cognitive-search-skill-keyphrases.md), [umiejętności wykrywania języka](cognitive-search-skill-language-detection.md), [umiejętności analizy wskaźniki nastrojów klientów](cognitive-search-skill-sentiment.md) |
| Dokument łamania | Proces wyodrębniania lub tworzenie zawartości tekstowej ze źródeł nietekstowych podczas indeksowania. Przykładem jest OCR (Rozpoznawania), ale zazwyczaj odnosi się do podstawowych funkcji indeksatora jako indeksatora wyodrębnia zawartość z plików aplikacji. Źródło danych, podając lokalizację pliku źródłowego i definicji indeksatora, zapewniając mapowań pól są obie kluczowymi czynnikami w dokumencie łamania. | Zobacz [indeksatorów](search-indexer-overview.md) |
| Kształtowania | Konsolidowanie fragmentów tekstu w większej struktury lub odwrotnie podziału większych fragmentów tekstu do rozmiarów dla dalszego przetwarzania podrzędnego. | [Umiejętności korektorze](cognitive-search-skill-shaper.md), [umiejętności fuzji tekst](cognitive-search-skill-textmerger.md), [umiejętności podziału tekstu](cognitive-search-skill-textsplit.md) |
| Wzbogaconego dokumentów | Przejściowymi wewnętrznej struktury, nie jest bezpośrednio dostępny w kodzie. Wzbogaconego dokumenty są generowane podczas przetwarzania, ale tylko ostateczne dane wyjściowe są zachowywane w indeksie wyszukiwania. Pole — mapowanie określają, które elementy danych zostaną dodane do indeksu. | Zobacz [podczas uzyskiwania dostępu do dokumentów wzbogaconego](cognitive-search-tutorial-blob.md#access-enriched-document). |
| Indeksator |  Przeszukiwarką, która umożliwia wyodrębnianie metadanych i danych z możliwością wyszukiwania z zewnętrznego źródła danych i wypełnienie indeksu według pola do mapowania między indeks i źródło danych dla łamania dokumentu. W przypadku wzbogacenia kognitywnych wyszukiwania indeksatora wywołuje skillset i kojarzenie dane wyjściowe wzbogacenia do pól docelowych. w indeksie mapowania pól zawiera. Definicja indeksatora znajdują się instrukcje i odwołania dla operacji potoku i potok jest wywoływany po uruchomieniu indeksatora. | [Indexers](search-indexer-overview.md) (Indeksatory) |
| Źródło danych  | Obiekt używany przez indeksator nawiązywania połączenia z zewnętrznym źródłem danych z obsługiwanych typów na platformie Azure. | Zobacz [indeksatorów](search-indexer-overview.md) |
| Indeks | Boże utrwalonego wyszukiwania w usłudze Azure Search, zbudowane na podstawie schematu indeksu, który definiuje strukturę pola i użycia. | [Indeksy w usłudze Azure Search](search-what-is-an-index.md) | 


## <a name="where-do-i-start"></a>Od czego zacząć?

**Krok 1: Tworzenie usługi wyszukiwania w regionie, który udostępnia interfejsy API** 

+ Środkowo-południowe stany USA
+ Europa Zachodnia

**Krok 2: W praktyce do głównego przepływu pracy**

+ [Szybki Start (portal)](cognitive-search-quickstart-blob.md)
+ [Samouczek (liczba żądań HTTP)](cognitive-search-tutorial-blob.md)
+ [Przykład niestandardowych umiejętności (C#)](cognitive-search-create-custom-skill-example.md)

**Krok 3: Przejrzyj interfejsu API (REST tylko)**

Obecnie są udostępniane tylko interfejsów API REST. Użyj `api-version=2017-11-11-Preview` we wszystkich żądaniach. Użyj poniższych interfejsów API umożliwiają utworzenie rozwiązania do wyszukiwania kognitywnych. Tylko dwa interfejsy API są dodawane lub rozszerzony na użytek kognitywnych wyszukiwania. Innych interfejsów API mieć takiej samej składni jak ogólnie dostępne wersje.

| Interfejs API REST | Opis |
|-----|-------------|
| [Utwórz źródło danych](https://docs.microsoft.com/rest/api/searchservice/create-data-source)  | Zasób identyfikowanie zewnętrzne źródło danych dostarczania źródła danych używany do tworzenia wzbogaconego dokumentów.  |
| [Utwórz Skillset (interfejs api-version = 2017-11-11-Preview)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)  | Koordynowanie używania zasobu [wstępnie zdefiniowane umiejętności](cognitive-search-predefined-skills.md) i [niestandardowych umiejętności kognitywnych](cognitive-search-custom-skill-interface.md) używane w potoku wzbogacenia podczas indeksowania. |
| [Tworzenie indeksu](https://docs.microsoft.com/rest/api/searchservice/create-index)  | Schemat wyrażenia indeksu usługi Azure Search. Pola w indeksie mapowania pól źródła danych lub pola wyprodukowany w fazie wzbogacenia (np. pola utworzone przez jednostki rozpoznawania nazw organizacji). |
| [Utwórz indeksator (interfejs api-version = 2017-11-11-Preview)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)  | Definiowanie składniki używane podczas indeksowania zasobu: w tym źródłem danych, skillset, pole skojarzenia ze źródła i struktury danych pośredniczące do indeksu docelowego i sam indeks. Uruchomienie indeksatora jest wprowadzanie danych i wzbogacenia wyzwalacza. Dane wyjściowe są Boże wyszukiwania, na podstawie schematu indeksu, wypełniane przy użyciu źródła danych, wzbogacone przez skillsets.  |

**Lista kontrolna: Typowy przepływ pracy**

1. Podzbiór danych źródłowych Azure do reprezentatywna próbka. Indeksowanie wymaga czasu tak rozpoczynać się od małej, reprezentatywny zestaw danych i następnie zbudowania go przyrostowo miarę rozwoju rozwiązania.

1. Utwórz [obiektu źródła danych](https://docs.microsoft.com/rest/api/searchservice/create-data-source) w usłudze Azure Search udostępnia ciąg połączenia dla pobierania danych.

1. Utwórz [skillset](https://docs.microsoft.com/rest/api/searchservice/create-skillset) wzbogacenia kroków.

1. Zdefiniuj [schematu indeksu](https://docs.microsoft.com/rest/api/searchservice/create-index). *Pola* kolekcja zawiera pola źródła danych. Należy również stub, limit dodatkowe pola do przechowywania wartości wygenerowany dla zawartości tworzone podczas wzbogacenia.

1. Zdefiniuj [indeksatora](https://docs.microsoft.com/rest/api/searchservice/create-skillset) odwołujące się do źródła danych, skillset i indeksu.

1. W ramach indeksatora, Dodaj *outputFieldMappings*. W tej sekcji mapuje dane wyjściowe z skillset (w kroku 3) do pól danych wejściowych w schemacie indeksu (w kroku 4).

1. Wyślij *Utwórz indeksator* żądania właśnie utworzony (żądanie POST z definicją indeksatora w treści żądania) do wyrażenia indeksatora w usłudze Azure Search. Ten krok jest sposób uruchamiania indeksatora, wywoła potok.

1. Uruchamianie kwerend w celu oceny wyników i zmodyfikuj kod do skillsets aktualizacji, schemat lub indeksator konfiguracji.

1. [Zresetować indeksatora](search-howto-reindex.md) przed odbudowywania potoku.

Aby uzyskać więcej informacji o określonych pytań lub problemów, zobacz [porady dotyczące rozwiązywania problemów](cognitive-search-concept-troubleshooting.md).

## <a name="next-steps"></a>Kolejne kroki

+ [Dokumentacja kognitywnych wyszukiwania](cognitive-search-resources-documentation.md)
+ [Szybki Start: Spróbuj kognitywnych search w portalu wskazówki](cognitive-search-quickstart-blob.md)
+ [Samouczek: Dowiedz się kognitywnych wyszukiwania interfejsów API](cognitive-search-tutorial-blob.md)