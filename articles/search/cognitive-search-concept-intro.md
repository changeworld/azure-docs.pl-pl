---
title: Wprowadzenie do wzbogacania SI
titleSuffix: Azure Cognitive Search
description: Wyodrębnianie zawartości, przetwarzanie języka naturalnego (NLP) i przetwarzanie obrazu są używane do tworzenia zawartości z wyszukują w indeksach usługi Azure Cognitive Search z predefiniowanych umiejętności poznawczych i niestandardowych algorytmów sztucznej inteligencji.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/24/2020
ms.openlocfilehash: a41dcc9c7ec86f41c64a69ea1aba762b960b2633
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80283025"
---
# <a name="getting-started-with-ai-enrichment"></a>Pierwsze kroki z wzbogacaniem SI

Wzbogacanie sztucznej inteligencji to możliwość indeksowania usługi Azure Cognitive Search używanego do wyodrębniania tekstu z obrazów, obiektów blob i innych nieustrukturyzowanych źródeł danych. Wzbogacanie i ekstrakcja sprawiają, że zawartość jest bardziej przeszukiwana w [indeksie](search-what-is-an-index.md) lub [magazynie wiedzy.](knowledge-store-concept-intro.md) Wyodrębnianie i wzbogacanie są implementowane przy użyciu *umiejętności poznawczych* dołączonych do potoku indeksowania. Umiejętności poznawcze wbudowane w usługę należą do tych kategorii: 

+ **Umiejętności przetwarzania języka naturalnego** obejmują [rozpoznawanie jednostek,](cognitive-search-skill-entity-recognition.md) [wykrywanie języka,](cognitive-search-skill-language-detection.md) [wyodrębnianie fraz kluczowych,](cognitive-search-skill-keyphrases.md)manipulowanie tekstami, [wykrywanie tonacji](cognitive-search-skill-sentiment.md)i [wykrywanie danych osobowych.](cognitive-search-skill-pii-detection.md) Dzięki tym umiejętnościom tekst bez struktury jest mapowany jako pola zbywalne i filtrowalne w indeksie.

+ **Umiejętności przetwarzania obrazu** obejmują [optyczne rozpoznawanie znaków (OCR)](cognitive-search-skill-ocr.md) i identyfikację [cech wizualnych,](cognitive-search-skill-image-analysis.md)takich jak wykrywanie twarzy, interpretacja obrazu, rozpoznawanie obrazu (znane osoby i punkty orientacyjne) lub atrybuty, takie jak orientacja obrazu. Te umiejętności tworzenia reprezentacji tekstu zawartości obrazu, dzięki czemu można przeszukiwać przy użyciu możliwości zapytań usługi Azure Cognitive Search.

![Diagram potoku wzbogacania](./media/cognitive-search-intro/cogsearch-architecture.png "omówienie potoku wzbogacania")

Umiejętności poznawcze w usłudze Azure Cognitive Search są oparte na wstępnie przeszkolonych modelach uczenia maszynowego w interfejsach API usług Cognitive Services: [Computer Vision](https://docs.microsoft.com/azure/cognitive-services/computer-vision/) i [Text Analytics.](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview) 

Przetwarzanie języka naturalnego i obrazu jest stosowane podczas fazy pozyskiwania danych, a wyniki stają się częścią kompozycji dokumentu w indeksie z możliwością wyszukiwania w usłudze Azure Cognitive Search. Dane są pozyskiwane jako zestaw danych platformy Azure, a następnie wypychane przez potok indeksowania przy użyciu wbudowanych [umiejętności,](cognitive-search-predefined-skills.md) których potrzebujesz. Architektura jest rozszerzalna, więc jeśli wbudowane umiejętności nie są wystarczające, można tworzyć i dołączać [umiejętności niestandardowe,](cognitive-search-create-custom-skill-example.md) aby zintegrować przetwarzanie niestandardowe. Przykładami może być niestandardowy moduł jednostki lub klasyfikator dokumentu, który jest ukierunkowany na określoną domenę, taką jak finanse, publikacje naukowe lub medycyna.

## <a name="when-to-use-ai-enrichment"></a>Kiedy stosować wzbogacenie AI

Należy rozważyć użycie wbudowanych umiejętności poznawczych, jeśli zawartość nieprzetworzona to tekst, zawartość obrazu lub zawartość, która wymaga wykrywania i tłumaczenia języka. Zastosowanie SI za pomocą wbudowanych umiejętności poznawczych może odblokować tę zawartość, zwiększając jej wartość i użyteczność w aplikacjach do wyszukiwania i nauki o danych. 

Ponadto można rozważyć dodanie umiejętności niestandardowej, jeśli masz kod open source, innej firmy lub innej firmy, który chcesz zintegrować z potokiem. Modele klasyfikacji, które identyfikują istotne cechy różnych typów dokumentów należą do tej kategorii, ale każdy pakiet, który dodaje wartość do zawartości może być używany.

### <a name="more-about-built-in-skills"></a>Więcej informacji o wbudowanych umiejętnościach

Zestaw [umiejętności,](cognitive-search-defining-skillset.md) który jest montowany przy użyciu wbudowanych umiejętności, jest odpowiedni dla następujących scenariuszy aplikacji:

+ Zeskanowane dokumenty (JPEG), które mają być przeszukiwane w pełnym tekście. Umiejętność optycznego rozpoznawania znaków (OCR) umożliwia identyfikację, wyodrębnianie i pozyskiwanie tekstu z plików JPEG.

+ Pliki PDF z połączonym obrazem i tekstem. Tekst w plikach PDF może być wyodrębniany podczas indeksowania bez użycia kroków wzbogacania, ale dodanie przetwarzania obrazu i języka naturalnego często może przynieść lepsze wyniki niż zapewnia standardowe indeksowanie.

+ Wielojęzyczna zawartość, względem której chcesz zastosować wykrywanie języka i ewentualnie tłumaczenie tekstu.

+ Dokumenty nieustrukturyzowane lub częściowo ustrukturyzowane zawierające zawartość, która ma nieodłączne znaczenie lub kontekst, który jest ukryty w większym dokumencie. 

  W szczególności obiekty blob często zawierają dużą treść zawartości, która jest pakowana w pojedyncze "pole". Dołączając umiejętności przetwarzania obrazu i języka naturalnego do indeksatora, można utworzyć nowe informacje, które są dostępne w zawartości nieprzetworzonej, ale w inny sposób nie są przedstawiane jako odrębne pola. Niektóre gotowe do użycia wbudowane umiejętności poznawcze, które mogą pomóc: wyodrębnianie fraz kluczowych, analiza tonacji i rozpoznawanie jednostek (osoby, organizacje i lokalizacje).

  Ponadto wbudowane umiejętności mogą być również używane restrukturyzacji zawartości poprzez dzielenie tekstu, scalanie i operacje kształtu.

### <a name="more-about-custom-skills"></a>Więcej informacji o umiejętnościach niestandardowych

Umiejętności niestandardowe mogą obsługiwać bardziej złożone scenariusze, takie jak rozpoznawanie formularzy lub niestandardowe wykrywanie encji przy użyciu modelu, który udostępniasz i zawijasz w [niestandardowym interfejsie internetowym umiejętności.](cognitive-search-custom-skill-interface.md) Kilka przykładów umiejętności niestandardowych to [aparat rozpoznawania formularzy,](/azure/cognitive-services/form-recognizer/overview)integracja [interfejsu API wyszukiwania jednostek Bing](https://docs.microsoft.com/azure/search/cognitive-search-create-custom-skill-example)i [rozpoznawanie encji niestandardowych.](https://github.com/Microsoft/SkillsExtractorCognitiveSearch)


## <a name="steps-in-an-enrichment-pipeline"></a>Kroki w rurociągu wzbogacania

Potok wzbogacania jest oparty na [*indeksatorach*](search-indexer-overview.md). Indeksatory wypełniają indeks na podstawie mapowań pola do pola między indeksem a źródłem danych w celu złamania dokumentu. Umiejętności, teraz dołączone do indeksatorów, przechwytują i wzbogacają dokumenty zgodnie z zdefiniowanymi umiejętnościami. Po zindeksja można uzyskać dostęp do zawartości za pośrednictwem żądań wyszukiwania za pośrednictwem wszystkich [typów zapytań obsługiwanych przez usługę Azure Cognitive Search.](search-query-overview.md)  Jeśli jesteś nowy w indeksatorów, w tej sekcji przeprowadzi Cię przez kroki.

### <a name="step-1-connection-and-document-cracking-phase"></a>Krok 1: Faza połączenia i pękania dokumentów

Na początku potoku masz nieustrukturyzowaną zawartość tekstową lub nietekstową (taką jak obrazy, zeskanowane dokumenty lub pliki JPEG). Dane muszą istnieć w usłudze magazynu danych platformy Azure, do których może uzyskać dostęp indeksator. Indeksatory mogą "pękać" dokumenty źródłowe, aby wyodrębnić tekst z danych źródłowych. Pękanie dokumentów to proces wyodrębniania lub tworzenia zawartości tekstowej ze źródeł nietekstowych podczas indeksowania.

![Faza pękania dokumentów](./media/cognitive-search-intro/document-cracking-phase-blowup.png "pękanie dokumentów")

 Obsługiwane źródła obejmują magazyn obiektów blob platformy Azure, magazyn tabel platformy Azure, usługę Azure SQL Database i usługę Azure Cosmos DB. Zawartość tekstową można wyodrębnić z następujących typów plików: pliki PDF, Word, PowerPoint, PLIKI CSV. Aby uzyskać pełną listę, zobacz [Obsługiwane formaty](search-howto-indexing-azure-blob-storage.md#supported-document-formats). Indeksowanie wymaga czasu, więc zacznij od małego, reprezentatywnego zestawu danych, a następnie skompiluj go stopniowo w miarę dojrzewania rozwiązania.

### <a name="step-2-cognitive-skills-and-enrichment-phase"></a>Krok 2: Umiejętności poznawcze i faza wzbogacania

Wzbogacenie odbywa się za pomocą *umiejętności poznawczych* wykonujących operacje atomowe. Na przykład po pęknięciu pliku PDF można zastosować rozpoznawanie jednostek, wykrywanie języka lub wyodrębnianie fraz kluczowych w celu wytworzenia nowych pól w indeksie, które nie są dostępne natywnie w źródle. W sumie zbiór umiejętności wykorzystywanych w potoku nazywa się *skillset*.  

![Faza wzbogacania](./media/cognitive-search-intro/enrichment-phase-blowup.png "faza wzbogacania")

Zestaw umiejętności jest oparty na [wbudowanych umiejętnościach poznawczych](cognitive-search-predefined-skills.md) lub [umiejętnościach niestandardowych,](cognitive-search-create-custom-skill-example.md) które udostępniasz i łączysz się z zestawem umiejętności. Zestaw umiejętności może być minimalny lub bardzo złożony i określa nie tylko rodzaj przetwarzania, ale także kolejność operacji. Zestaw umiejętności oraz mapowania pól zdefiniowane jako część indeksatora w pełni określa potok wzbogacania. Aby uzyskać więcej informacji na temat łączenia wszystkich tych elementów, zobacz [Definiowanie zestawu umiejętności](cognitive-search-defining-skillset.md).

Wewnętrznie potok generuje kolekcję wzbogaconych dokumentów. Można zdecydować, które części wzbogaconych dokumentów mają być mapowane na pola z wymienialne w indeksie wyszukiwania. Na przykład jeśli zastosowano wyodrębnianie frazy kluczowej i umiejętności rozpoznawania encji, te nowe pola staną się częścią wzbogaconego dokumentu i mogą być mapowane na pola w indeksie. Zobacz [Adnotacje,](cognitive-search-concept-annotations-syntax.md) aby dowiedzieć się więcej o formacjach wejścia/wyjścia.

#### <a name="add-a-knowledgestore-element-to-save-enrichments"></a>Dodawanie elementu knowledgeStore w celu zapisania wzbogacenia

[Szukaj REST api-version=2019-05-06-Preview](search-api-preview.md) rozszerza `knowledgeStore` skillsets z definicją, która zapewnia połączenie magazynu platformy Azure i projekcje, które opisują, jak wzbogacenia są przechowywane. Jest to dodatek do indeksu. W standardowym potoku AI wzbogacone dokumenty są przejściowe, używane tylko podczas indeksowania, a następnie odrzucane. Dzięki magazynowi wiedzy zachowane są wzbogacone dokumenty. Aby uzyskać więcej informacji, zobacz [Magazyn wiedzy (wersja zapoznawcza)](knowledge-store-concept-intro.md).

### <a name="step-3-search-index-and-query-based-access"></a>Krok 3: Indeks wyszukiwania i dostęp oparty na zapytaniach

Po zakończeniu przetwarzania masz indeks wyszukiwania składający się z wzbogaconych dokumentów, w pełni przeszukiwania tekstu w usłudze Azure Cognitive Search. [Kwerendy indeksu](search-query-overview.md) jest jak deweloperzy i użytkownicy uzyskać dostęp do wzbogaconej zawartości generowane przez potok. 

![Indeks z ikoną wyszukiwania](./media/cognitive-search-intro/search-phase-blowup.png "Indeks z ikoną wyszukiwania")

Indeks jest jak każdy inny, który można utworzyć dla usługi Azure Cognitive Search: można uzupełnić o analizatory niestandardowe, wywołać rozmyte zapytania wyszukiwania, dodać filtrowane wyszukiwanie lub eksperymentować z profilami oceniania, aby zmienić kształt wyników wyszukiwania.

Indeksy są generowane na podstawie schematu indeksu, który definiuje pola, atrybuty i inne konstrukcje dołączone do określonego indeksu, takie jak profile oceniania i mapy synonimów. Po zdefiniowaniu i wypełnieniu indeksu można indeksować stopniowo, aby odebrać nowe i zaktualizowane dokumenty źródłowe. Niektóre modyfikacje wymagają pełnej przebudowy. Należy użyć małego zestawu danych, dopóki projekt schematu nie będzie stabilny. Aby uzyskać więcej informacji, zobacz [Jak odbudować indeks](search-howto-reindex.md).

**Lista kontrolna: typowy przepływ pracy**

1. Podzbiór danych źródłowych platformy Azure do próbki reprezentatywnej. Indeksowanie wymaga czasu, więc zacznij od małego, reprezentatywnego zestawu danych, a następnie skompiluj go stopniowo w miarę dojrzewania rozwiązania.

1. Utwórz [obiekt źródła danych](https://docs.microsoft.com/rest/api/searchservice/create-data-source) w usłudze Azure Cognitive Search, aby zapewnić parametry połączenia do pobierania danych.

1. Utwórz [zestaw umiejętności](https://docs.microsoft.com/rest/api/searchservice/create-skillset) z krokami wzbogacania.

1. Zdefiniuj [schemat indeksu](https://docs.microsoft.com/rest/api/searchservice/create-index). Kolekcja *Pola* zawiera pola z danych źródłowych. Należy również wykreślić dodatkowe pola do przechowywania wygenerowanych wartości zawartości utworzonej podczas wzbogacania.

1. Zdefiniuj [indeksatora](https://docs.microsoft.com/rest/api/searchservice/create-skillset) odwołującego się do źródła danych, zestaw umiejętności i indeks.

1. W obrębie indeksatora dodaj *dane wyjściowePolmapowanie*. W tej sekcji mapuje dane wyjściowe z skillset (w kroku 3) do pól danych wejściowych w schemacie indeksu (w kroku 4).

1. Wyślij żądanie *utwórz indeksatora,* które właśnie utworzono (żądanie POST z definicją indeksatora w treści żądania), aby wyrazić indeksatora w usłudze Azure Cognitive Search. Ten krok jest jak uruchomić indeksatora, wywołując potoku.

1. Uruchom kwerendy, aby ocenić wyniki i zmodyfikować kod, aby zaktualizować skillsets, schematu lub konfiguracji indeksatora.

1. [Zresetuj indeksator](search-howto-reindex.md) przed przebudową potoku.

## <a name="next-steps"></a>Następne kroki

+ [Łącza do dokumentacji wzbogacania SI](cognitive-search-resources-documentation.md)
+ [Przykład: Tworzenie niestandardowej umiejętności wzbogacania SI (C#)](cognitive-search-create-custom-skill-example.md)
+ [Szybki start: wypróbuj wzbogacanie si w przewodniku po portalu](cognitive-search-quickstart-blob.md)
+ [Samouczek: Dowiedz się więcej o interfejsach API wzbogacania si](cognitive-search-tutorial-blob.md)
+ [Magazyn wiedzy (wersja zapoznawcza)](knowledge-store-concept-intro.md)
+ [Tworzenie magazynu wiedzy w rest](knowledge-store-create-rest.md)
+ [Wskazówki dotyczące rozwiązywania problemów](cognitive-search-concept-troubleshooting.md)
