---
title: Wprowadzenie do wyszukiwania poznawczego i wzbogacania AI — Azure Search
description: Wyodrębnianie zawartości, przetwarzanie języka naturalnego (NLP) i przetwarzanie obrazów w celu utworzenia zawartości z możliwością wyszukiwania w Azure Search indeksowania przy użyciu umiejętności poznawczych i algorytmów AI.
manager: cgronlun
author: HeidiSteen
services: search
ms.service: search
ms.subservice: cognitive-search
ms.devlang: NA
ms.topic: overview
ms.date: 05/28/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: d1cb0b687b97ce83d47aa0635f8999ced8594226
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/06/2019
ms.locfileid: "68841313"
---
# <a name="what-is-cognitive-search-in-azure-search"></a>Co to jest "wyszukiwanie poznawcze" w Azure Search?

Wyszukiwanie poznawcze to funkcja AI w Azure Search, służąca do wyodrębniania tekstu z obrazów, obiektów blob i innych źródeł danych bez struktury — wzbogacanie zawartości o większą możliwość wyszukiwania w indeksie Azure Search. Wyodrębnianie i wzbogacanie są implementowane przez *umiejętności poznawcze* dołączone do potoku indeksowania. Wzbogacania AI są obsługiwane w następujący sposób: 

+ Umiejętności **przetwarzania języka naturalnego** obejmują [rozpoznawanie jednostek](cognitive-search-skill-entity-recognition.md), [wykrywanie języka](cognitive-search-skill-language-detection.md), [wyodrębnianie kluczowych fraz](cognitive-search-skill-keyphrases.md), manipulowanie tekstem i [wykrywanie tonacji](cognitive-search-skill-sentiment.md). Dzięki tym umiejętnościom tekst niestrukturalny może przyjąć nowe formularze, zamapowane jako pola umożliwiające wyszukiwanie i filtrowanie w indeksie.

+ Umiejętności **przetwarzania obrazów** obejmują [optyczne rozpoznawanie znaków (OCR)](cognitive-search-skill-ocr.md) i identyfikację [funkcji wizualizacji](cognitive-search-skill-image-analysis.md), takich jak wykrywanie twarzy, interpretacja obrazu, rozpoznawanie obrazu (sławę osoby i punkty orientacyjne) lub atrybuty, takie jak kolory lub Orientacja obrazu. Można utworzyć tekstową reprezentację zawartości obrazu, przeszukiwanie przy użyciu wszystkich możliwości zapytań Azure Search.

![Diagram potoku wyszukiwania poznawczego](./media/cognitive-search-intro/cogsearch-architecture.png "Przegląd potoku wyszukiwania poznawczego")

Umiejętności poznawcze w Azure Search są oparte na modelach uczenia maszynowego w interfejsy API usług Cognitive Services: Analiza [Przetwarzanie obrazów](https://docs.microsoft.com/azure/cognitive-services/computer-vision/) i [tekstu](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview). 

Przetwarzanie języka naturalnego i obrazu jest stosowane w fazie pozyskiwania danych, a wyniki stają się częścią kompozycji dokumentu w indeksie wyszukiwania w Azure Search. Dane są źródłem danych jako zestaw danych platformy Azure, a następnie wypychane za pośrednictwem potoku indeksowania przy użyciu zależnych [umiejętności](cognitive-search-predefined-skills.md) , które są potrzebne. Architektura jest rozszerzalna, dlatego jeśli wbudowane umiejętności nie są wystarczające, można tworzyć i dołączać [niestandardowe umiejętności](cognitive-search-create-custom-skill-example.md) umożliwiające integrację przetwarzania niestandardowego. Przykłady mogą być niestandardowym modułem jednostki lub klasyfikatorem dokumentu przeznaczonym dla konkretnej domeny, takiej jak finanse, publikacje naukowe lub medycyna.

> [!NOTE]
> Podczas rozszerzania zakresu przez zwiększenie częstotliwości przetwarzania, Dodawanie większej liczby dokumentów lub Dodawanie algorytmów AI, należy [dołączyć Cognitive Services rozliczanego zasobu](cognitive-search-attach-cognitive-services.md). Opłaty naliczane podczas wywoływania interfejsów API w Cognitive Services oraz do wyodrębniania obrazów w ramach etapu łamania dokumentu w Azure Search. Nie są naliczane opłaty za Wyodrębnianie tekstu z dokumentów.
>
> Do wykonania wbudowanych umiejętności są naliczane opłaty za istniejące [Cognitive Services cena płatność zgodnie z rzeczywistym](https://azure.microsoft.com/pricing/details/cognitive-services/)użyciem. Cennik wyodrębniania obrazów został opisany na [stronie cennika Azure Search](https://go.microsoft.com/fwlink/?linkid=2042400).
## <a name="components-of-cognitive-search"></a>Składniki wyszukiwania poznawczego

Potok wyszukiwania poznawczego bazuje na [Azure Search *indeksatorów* ](search-indexer-overview.md) , które przeszukują źródła danych i zapewniają kompleksowe przetwarzanie indeksów. Umiejętności są teraz dołączone do indeksatorów, przechwytuje i wzbogacają dokumenty zgodnie ze zdefiniowanym przez Ciebie zestawu umiejętności. Po indeksowaniu można uzyskać dostęp do zawartości za pośrednictwem żądań wyszukiwania przez wszystkie [typy zapytań obsługiwane przez Azure Search](search-query-overview.md).  Jeśli jesteś nowym indeksatorem, ta sekcja przeprowadzi Cię przez kroki.

### <a name="step-1-connection-and-document-cracking-phase"></a>Krok 1: Faza nawiązywania połączenia i dokumentu

Na początku potoku znajduje się tekst bez struktury lub zawartość nietekstowa (na przykład obrazy i zeskanowane pliki JPEG). Dane muszą istnieć w usłudze Azure Data Storage, do której można uzyskać dostęp za pomocą indeksatora. Indeksatory mogą "pęknięcia" dokumenty źródłowe w celu wyodrębnienia tekstu z danych źródłowych.

![Faza łamania dokumentu](./media/cognitive-search-intro/document-cracking-phase-blowup.png "łamanie dokumentów")

 Obsługiwane źródła obejmują usługę Azure Blob Storage, usługę Azure Table Storage, Azure SQL Database i Azure Cosmos DB. Zawartość oparta na tekście można wyodrębnić z następujących typów plików: Pliki PDF, Word, PowerPoint i CSV. Aby zapoznać się z pełną listą, zobacz [obsługiwane formaty](search-howto-indexing-azure-blob-storage.md#supported-document-formats).

### <a name="step-2-cognitive-skills-and-enrichment-phase"></a>Krok 2: Umiejętności poznawcze i fazy wzbogacania

Wzbogacanie polega na *umiejętnościach poznawczych* wykonujących operacje niepodzielne. Na przykład po umieszczeniu zawartości tekstowej z pliku PDF można zastosować wykrywanie języka rozpoznawania jednostek lub wyodrębnianie kluczowych fraz w celu utworzenia nowych pól w indeksie, które nie są dostępne natywnie w źródle. Całkowicie kolekcja umiejętności używanych w potoku jest nazywana *zestawu umiejętności*.  

![Faza wzbogacania](./media/cognitive-search-intro/enrichment-phase-blowup.png "faza wzbogacania")

Zestawu umiejętności jest oparta na [wstępnie zdefiniowanych umiejętnościach poznawczych](cognitive-search-predefined-skills.md) lub [niestandardowych umiejętnościach](cognitive-search-create-custom-skill-example.md) , które zapewniasz i łączą się z zestawu umiejętności. Zestawu umiejętności może być minimalny lub wysoce skomplikowany i określa nie tylko typ przetwarzania, ale również kolejność operacji. Zestawu umiejętności oraz mapowania pól zdefiniowane jako część indeksatora w pełni określają potok wzbogacania. Aby uzyskać więcej informacji na temat ściągania wszystkich z tych fragmentów, zobacz [Definiowanie zestawu umiejętności](cognitive-search-defining-skillset.md).

Wewnętrznie potok generuje kolekcję ulepszonych dokumentów. Można zdecydować, które części wzbogaconych dokumentów mają być mapowane do pól indeksowanych w indeksie wyszukiwania. Na przykład jeśli zastosowano wyodrębnianie kluczowych fraz i umiejętności rozpoznawania jednostek, wówczas te nowe pola staną się częścią wzbogaconego dokumentu i mogą być mapowane do pól w indeksie. Zobacz [Adnotacje](cognitive-search-concept-annotations-syntax.md) , aby dowiedzieć się więcej na temat formatów wejścia/wyjścia.

#### <a name="add-a-knowledgestore-element-to-save-enrichments"></a>Dodaj element knowledgeStore, aby zapisać wzbogacenia

[Interfejs API REST usługi Search — wersja = 2019-05-06](search-api-preview.md) rozszerza umiejętności z definicją knowledgeStore, która zapewnia połączenie z usługą Azure Storage i projekcje opisujące sposób przechowywania wzbogaceń. 

Dodanie sklepu z bazami danych do usługi zestawu umiejętności daje możliwość przedstawienia reprezentacji wzbogaceń dla scenariuszy innych niż wyszukiwanie pełnotekstowe. Aby uzyskać więcej informacji, zobacz artykuł [co to jest sklep merytoryczny](knowledge-store-concept-intro.md).

### <a name="step-3-search-index-and-query-based-access"></a>Krok 3: Indeks wyszukiwania i dostęp oparty na zapytaniach

Po zakończeniu przetwarzania masz indeks wyszukiwania składający się z wzbogaconych dokumentów, w pełni przeszukiwanych tekstu w Azure Search. [Zapytanie o indeks](search-query-overview.md) polega na tym, jak deweloperzy i użytkownicy uzyskują dostęp do wzbogaconej zawartości wygenerowanej przez potok. 

![Indeks przy użyciu ikony wyszukiwania](./media/cognitive-search-intro/search-phase-blowup.png "Indeks przy użyciu ikony wyszukiwania")

Indeks przypomina wszystkie inne elementy, które można utworzyć dla Azure Search: można uzupełnić za pomocą analizatorów niestandardowych, wywołać zapytania wyszukiwania rozmytego, dodać przefiltrowane wyszukiwanie lub eksperymentować z profilami oceniania, aby zmienić kształt wyników wyszukiwania.

Indeksy są generowane na podstawie schematu indeksu, który definiuje pola, atrybuty i inne konstrukcje dołączone do określonego indeksu, takie jak profile oceniania i mapy synonimów. Gdy indeks jest zdefiniowany i wypełniany, można indeksować przyrostowo, aby pobrać nowe i zaktualizowane dokumenty źródłowe. Niektóre modyfikacje wymagają pełnej kompilacji. Aby projekt schematu był stabilny, należy użyć małego zestawu danych. Aby uzyskać więcej informacji, zobacz [Jak odbudować indeks](search-howto-reindex.md).

<a name="feature-concepts"></a>

## <a name="key-features-and-concepts"></a>Kluczowe funkcje i pojęcia

| Pojęcie | Opis| Łącza |
|---------|------------|-------|
| Zestawu umiejętności | Zasób o nazwie najwyższego poziomu zawierający kolekcję umiejętności. Zestawu umiejętności to potok wzbogacania. Jest wywoływana podczas indeksowania przez indeksator. | [Zdefiniuj zestawu umiejętności](cognitive-search-defining-skillset.md) |
| Umiejętność | Niepodzielna transformacja w potoku wzbogacania. Często jest to składnik, który wyodrębnia lub wnioskuje strukturę i w związku z tym rozszerza zrozumienie danych wejściowych. Prawie zawsze, dane wyjściowe są oparte na tekście, a przetwarzanie polega na przetwarzaniu języka naturalnego lub przetwarzaniu obrazu, który wyodrębnia lub generuje tekst z danych wejściowych obrazu. Dane wyjściowe z umiejętności mogą być mapowane do pola w indeksie lub używane jako dane wejściowe dla wzbogacania podrzędnego. Umiejętności są wstępnie zdefiniowane i udostępniane przez firmę Microsoft albo niestandardowe: utworzone i wdrożone przez użytkownika. | [Wstępnie zdefiniowane umiejętności](cognitive-search-predefined-skills.md) |
| Wyodrębnianie danych | Obejmuje szeroką gamę procesów przetwarzania, ale odnoszące się do wyszukiwania poznawczego, umiejętność rozpoznawania jednostek zazwyczaj służy do wyodrębniania danych (jednostki) ze źródła, które nie zapewnia natywnej informacji. | [Umiejętność rozpoznawania jednostek](cognitive-search-skill-entity-recognition.md)| 
| Przetwarzanie obrazów | Wnioskuje tekst z obrazu, taki jak możliwość rozpoznawania punktu orientacyjnego, lub wyodrębniania tekstu z obrazu. Typowe przykłady obejmują OCR do podnoszenia znaków z pliku zeskanowanego dokumentu (JPEG) lub rozpoznania nazwy ulicy w fotografii zawierającej znak ulicy. | Umiejętność [analizy obrazów](cognitive-search-skill-image-analysis.md) lub [umiejętność OCR](cognitive-search-skill-ocr.md)
| Przetwarzanie języka naturalnego | Przetwarzanie tekstu na potrzeby wglądu i informacji na temat danych wejściowych tekstu. Wykrywanie języka, analiza tonacji i wyodrębnianie kluczowych fraz są umiejętnościami, które są objęte przetwarzaniem w języku naturalnym.  | [Wyodrębnianie kluczowych fraz umiejętności](cognitive-search-skill-keyphrases.md), [wykrywanie języka umiejętności](cognitive-search-skill-language-detection.md), [Analiza tonacji umiejętność](cognitive-search-skill-sentiment.md) |
| Łamanie dokumentów | Proces wyodrębniania lub tworzenia zawartości tekstowej ze źródeł nietekstowych podczas indeksowania. Optyczne rozpoznawanie znaków (OCR) to przykład, ale ogólnie odnosi się do podstawowej funkcjonalności indeksatora, ponieważ indeksator wyodrębnia zawartość z plików aplikacji. Źródło danych dostarczające lokalizację pliku źródłowego oraz definicję indeksatora dostarczającego mapowania pól są kluczowymi czynnikami w przypadku łamania dokumentów. | Zobacz [indeksatory](search-indexer-overview.md) |
| Kształtowania | Konsolidowanie fragmentów tekstu do większej struktury lub odwrotne rozdzielenie większych fragmentów tekstu do rozmiaru do zarządzania w celu przeprowadzenia dalszej obróbki podrzędnej. | [Umiejętność kształtu](cognitive-search-skill-shaper.md), [umiejętność łączenia tekstu](cognitive-search-skill-textmerger.md), [umiejętność dzielenia tekstu](cognitive-search-skill-textsplit.md) |
| Wzbogacone dokumenty | Przejściowa Struktura wewnętrzna, wygenerowana podczas przetwarzania, z końcowym wyjściem odzwierciedlonym w indeksie wyszukiwania. Zestawu umiejętności określa, które wzbogacania są wykonywane. Mapowania pól określają, które elementy danych są dodawane do indeksu. Opcjonalnie możesz utworzyć magazyn wiedzy, aby utrwalać i eksplorować wzbogacone dokumenty przy użyciu narzędzi takich jak Eksplorator usługi Storage, Power BI lub dowolne inne narzędzie, które nawiązuje połączenie z usługą Azure Blob Storage. | Zobacz artykuł [Magazyn wiedzy (wersja zapoznawcza)](knowledge-store-concept-intro.md). |
| Indeksator |  Przeszukiwarka, która wyodrębnia dane z możliwością wyszukiwania i metadane z zewnętrznego źródła danych i wypełnia indeks na podstawie mapowań pól między indeksem i źródłem danych na potrzeby łamania dokumentów. W przypadku wzbogacania wyszukiwania poznawczego indeksator wywołuje zestawu umiejętności i zawiera mapowania pól, które kojarzą dane wyjściowe wzbogacania z polami docelowymi w indeksie. Definicja indeksatora zawiera wszystkie instrukcje i odwołania do operacji potoku, a potok jest wywoływany po uruchomieniu indeksatora. | [Indexers](search-indexer-overview.md) (Indeksatory) |
| Źródło danych  | Obiekt używany przez indeksator do nawiązywania połączenia z zewnętrznym źródłem danych obsługiwanych typów na platformie Azure. | Zobacz [indeksatory](search-indexer-overview.md) |
| Indeks | Utrwalony indeks wyszukiwania w Azure Search utworzony na podstawie schematu indeksu, który definiuje strukturę pól i użycie. | [Indeksy w Azure Search](search-what-is-an-index.md) | 

<a name="where-do-i-start"></a>

## <a name="where-do-i-start"></a>Od czego zacząć?

**Krok 1. [Tworzenie zasobu Azure Search](search-create-service-portal.md)** 

**Krok 2. Wypróbuj Przewodniki Szybki Start i przykłady dla praktycznego środowiska**

+ [Szybki Start (Portal)](cognitive-search-quickstart-blob.md)
+ [Samouczek (żądania HTTP)](cognitive-search-tutorial-blob.md)
+ [Przykład: Tworzenie niestandardowej umiejętności dla wyszukiwania poznawczego (C#)](cognitive-search-create-custom-skill-example.md)

Zalecamy korzystanie z bezpłatnej usługi na potrzeby uczenia się, ale należy pamiętać, że liczba bezpłatnych transakcji jest ograniczona do 20 dokumentów dziennie. Aby uruchomić przewodnik Szybki Start i samouczek w ciągu jednego dnia, użyj mniejszego zestawu plików (10 dokumentów), aby można było dopasować oba ćwiczenia.

**Krok 3. Przegląd interfejsu API**

Możesz użyć żądań REST `api-version=2019-05-06` lub zestawu .NET SDK. 

Ten krok powoduje użycie interfejsów API REST w celu utworzenia rozwiązania wyszukiwania poznawczego. Tylko dwa interfejsy API są dodawane lub rozszerzane na potrzeby wyszukiwania poznawczego. Inne interfejsy API mają taką samą składnię jak ogólnie dostępne wersje.

| Interfejs API REST | Opis |
|-----|-------------|
| [Create Data Source](https://docs.microsoft.com/rest/api/searchservice/create-data-source) (Tworzenie źródła danych)  | Zasób identyfikujący zewnętrzne źródło danych udostępniający dane źródłowe używane do tworzenia wzbogaconych dokumentów.  |
| [Create zestawu umiejętności (API-Version = 2019-05-06)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)  | Zasób koordynujący używanie [wstępnie zdefiniowanych umiejętności](cognitive-search-predefined-skills.md) i [niestandardowych umiejętności poznawczych](cognitive-search-custom-skill-interface.md) używanych w potoku wzbogacania podczas indeksowania. |
| [Utwórz indeks](https://docs.microsoft.com/rest/api/searchservice/create-index)  | Schemat przedstawiający indeks Azure Search. Pola w indeksie mapują do pól w danych źródłowych lub do pól wyprodukowanych w fazie wzbogacania (na przykład pola nazw organizacji utworzonych przez funkcję rozpoznawania jednostek). |
| [Create indeksator (API-Version = 2019-05-06)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)  | Zasób definiujący składniki używane podczas indeksowania: w tym źródła danych, zestawu umiejętności, skojarzenia pól ze źródeł i pośrednich struktur danych do indeksu docelowego i samego indeksu. Uruchomienie indeksatora jest wyzwalaczem do pozyskiwania i wzbogacania danych. Wyjście jest indeksem wyszukiwania opartym na schemacie indeksu, wypełnionym danymi źródłowymi, wzbogaconym przez umiejętności.  |

**Wykaz Typowy przepływ pracy**

1. Podzbiór danych źródłowych platformy Azure do reprezentatywnej próbki. Indeksowanie odbywa się w czasie, w którym rozpoczyna się od małego, reprezentatywnego zestawu danych, a następnie kompiluje się przyrostowo wraz z oczekiwaniami.

1. Utwórz [obiekt źródła danych](https://docs.microsoft.com/rest/api/searchservice/create-data-source) w Azure Search, aby podać parametry połączenia na potrzeby pobierania danych.

1. Utwórz [zestawu umiejętności](https://docs.microsoft.com/rest/api/searchservice/create-skillset) z procedurami wzbogacania.

1. Zdefiniuj [schemat indeksu](https://docs.microsoft.com/rest/api/searchservice/create-index). Kolekcja *Fields* zawiera pola z danych źródłowych. Należy również utworzyć zastępcze dodatkowe pola, aby przechowywać wygenerowane wartości dla zawartości utworzonej podczas wzbogacania.

1. Zdefiniuj [indeksator](https://docs.microsoft.com/rest/api/searchservice/create-skillset) odwołujący się do źródła danych, zestawu umiejętności i indeksu.

1. W obszarze indeksatora Dodaj *outputFieldMappings*. Ta sekcja mapuje dane wyjściowe z zestawu umiejętności (w kroku 3) do pól danych wejściowych w schemacie indeksu (w kroku 4).

1. Wyślij właśnie utworzone żądanie *utworzenia indeksatora* (żądanie post z definicją indeksatora w treści żądania), aby wyrazić indeksator w Azure Search. Ten krok polega na tym, jak uruchomić indeksator, wywołując potok.

1. Uruchom zapytania, aby obliczyć wyniki i zmodyfikować kod w celu zaktualizowania konfiguracji umiejętności, schematu lub indeksatora.

1. [Zresetuj indeksator](search-howto-reindex.md) przed ponownym kompilacją potoku.

Aby uzyskać więcej informacji na temat określonych pytań lub problemów, zobacz [wskazówki dotyczące rozwiązywania problemów](cognitive-search-concept-troubleshooting.md).

## <a name="next-steps"></a>Następne kroki

+ [Dokumentacja wyszukiwania poznawczego](cognitive-search-resources-documentation.md)
+ [Szybki start: Wypróbuj usługę wyszukiwania poznawczego w przewodniku po portalu](cognitive-search-quickstart-blob.md)
+ [Samouczek: Poznaj interfejsy API wyszukiwania poznawczego](cognitive-search-tutorial-blob.md)
+ [Przegląd sklepu merytorycznego](knowledge-store-concept-intro.md)
+ [Przewodnik po sklepie merytorycznym](knowledge-store-howto.md)
