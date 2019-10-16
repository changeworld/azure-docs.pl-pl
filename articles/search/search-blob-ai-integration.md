---
title: Korzystanie z AI do zrozumienia danych obiektów BLOB
titleSuffix: Azure Search
description: Dodaj semantykę, przetwarzanie języka naturalnego i analizę obrazu do obiektów blob platformy Azure przy użyciu potoku wzbogacania AI w Azure Search.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: search
ms.topic: conceptual
ms.date: 10/09/2019
ms.openlocfilehash: 5dc81f6e35f86c6dee77d44ff5c59c2657434a37
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/15/2019
ms.locfileid: "72376267"
---
# <a name="use-ai-to-understand-blob-data"></a>Korzystanie z AI do zrozumienia danych obiektów BLOB

Dane w usłudze Azure Blob Storage to często wiele zawartości bez struktury, takich jak obrazy, długie teksty, pliki PDF i dokumenty pakietu Office. Korzystając z możliwości systemu AI w Azure Search, można zrozumieć i wyodrębnić cenne informacje z obiektów BLOB na różne sposoby. Przykłady zastosowania AI do zawartości obiektów BLOB obejmują:

+ Wyodrębnij tekst z obrazów przy użyciu optycznego rozpoznawania znaków (OCR)
+ Tworzenie opisu sceny lub tagów ze zdjęcia
+ Wykrywaj język i Tłumacz tekst na różne języki
+ Przetwarzaj tekst za pomocą nazwanego rozpoznawania jednostek (NER), aby znaleźć odwołania do osób, dat, miejsc lub organizacji 

Chociaż może być wymagana tylko jedna z tych funkcji AI, często można połączyć wiele z nich w ten sam potok (na przykład Wyodrębnianie tekstu z zeskanowanego obrazu, a następnie znalezienie wszystkich dat i miejsc, do których się odwołuje). 

Wzbogacanie AI tworzy nowe informacje przechwycone jako tekst, przechowywane w polach. Po wzbogaceniu można uzyskać dostęp do tych informacji z indeksu wyszukiwania za pośrednictwem wyszukiwania pełnotekstowego lub wysłać wzbogacone dokumenty z powrotem do usługi Azure Storage, aby uzyskać nowe środowiska aplikacji, które obejmują Eksplorowanie danych w scenariuszach odnajdywania lub analizy. 

W tym artykule przedstawiono wzbogacenie AI za pośrednictwem szerokiego obiektywu, dzięki czemu można szybko opanujesz cały proces od przekształcenia nieprzetworzonych danych w obiektach Blob do Queryable informacji w indeksie wyszukiwania lub w sklepie z bazami wiedzy.

## <a name="what-it-means-to-enrich-blob-data"></a>Co oznacza "wzbogacanie" danych obiektów BLOB

*Wzbogacanie systemu AI* jest częścią architektury indeksowania Azure Search, która integruje wbudowane AI z firmy Microsoft lub niestandardowego AI. Ułatwia ona implementowanie kompleksowych scenariuszy, w których należy przetwarzać obiekty blob (zarówno istniejące, jak i nowe), ponieważ są one dostępne lub są aktualizowane), w tym celu należy otworzyć wszystkie formaty plików w celu wyodrębnienia obrazów i tekstu, wyodrębnienie wymaganych informacji przy użyciu różnych funkcji AI. Indeksuj je w indeks Azure Search, aby uzyskać szybkie wyszukiwanie, pobieranie i eksplorację. 

Dane wejściowe są obiektami BLOB w jednym kontenerze w usłudze Azure Blob Storage. Obiekty blob mogą być niemal dowolnym rodzajem danych tekstowych lub graficznych. 

Dane wyjściowe są zawsze indeksem Azure Search używanym do szybkiego wyszukiwania tekstu, pobierania i eksploracji w aplikacjach klienckich. Ponadto dane wyjściowe mogą być również *magazynem wiedzy* , który projektuje wzbogacone dokumenty do obiektów blob platformy Azure lub tabel platformy Azure w celu przeprowadzenia analizy podrzędnej w przypadku narzędzi takich jak Power BI lub obciążeń związanych z nauką danych.

Między programem jest samą architekturą potoku. Potok jest oparty na funkcji *indeksatora* , do której można przypisać *zestawu umiejętności*, która składa się z co najmniej jednej *umiejętności* zapewniającej AI. Potok jest przeznaczony do tworzenia *wzbogaconych dokumentów* , które wprowadzają jako nieprzetworzoną zawartość, ale pobierają dodatkową strukturę, kontekst i informacje podczas poruszania się po potoku. Wzbogacone dokumenty są używane podczas indeksowania do tworzenia odwróconych indeksów i innych struktur używanych w wyszukiwaniu pełnotekstowym lub eksploracji i analizie.

## <a name="how-to-get-started"></a>Jak zacząć

Możesz rozpocząć bezpośrednio na stronie portalu konta magazynu. Kliknij przycisk **dodaj Azure Search** i Utwórz nową usługę Azure Search lub wybierz istniejącą. Jeśli masz już istniejącą usługę wyszukiwania w ramach tej samej subskrypcji, kliknij przycisk **dodaj Azure Search** otwiera Kreatora importu danych, dzięki czemu możesz natychmiast przejść od indeksowania, wzbogacania i definicji indeksu.

Po dodaniu Azure Search do konta magazynu można wykonać standardowy proces, aby wzbogacić dane w dowolnym źródle danych platformy Azure. Przy założeniu, że masz już zawartość obiektu BLOB, możesz użyć Kreatora importu danych w Azure Search, aby łatwo uzyskać wstępne wprowadzenie do wzbogacania zasobów AI. Ten przewodnik Szybki Start wyjaśnia kroki: [Tworzenie potoku wzbogacania AI w portalu](cognitive-search-quickstart-blob.md). 

W poniższych sekcjach omówiono więcej składników i koncepcji.

## <a name="begin-with-blob-indexers"></a>Rozpocznij od indeksatorów obiektów BLOB

Wzbogacanie AI jest dodatkiem do potoku indeksowania, a w Azure Search te potoki są zbudowane na podstawie *indeksatora*. Indeksator to podusługa z obsługą źródła danych, która jest wyposażona w wewnętrzną logikę do próbkowania danych, odczytywanie danych metadanych, pobieranie danych i Serializowanie danych z formatów natywnych do dokumentów JSON w celu późniejszego zaimportowania. Indeksatory są często używane do importowania, oddziel je od AI, ale jeśli chcesz skompilować potok wzbogacania AI, będziesz potrzebować indeksatora i zestawu umiejętności. W tej sekcji skupmy się na samym indeksatorze.

Obiekty blob w usłudze Azure Storage są indeksowane przy użyciu [indeksatora magazynu obiektów blob Azure Search](search-howto-indexing-azure-blob-storage.md). Ten indeksator jest wywoływany przez ustawienie typu i podanie informacji o połączeniu, które zawierają konto usługi Azure Storage oraz kontenera obiektów BLOB. O ile wcześniej nie zostały zorganizowane obiekty blob do katalogu wirtualnego, który można przekazać jako parametr, indeksator obiektu BLOB pobiera z całego kontenera.

Indeksator wykonuje "pęknięcia dokumentu" i po nawiązaniu połączenia ze źródłem danych jest pierwszym krokiem w potoku. W przypadku danych obiektów BLOB są wykrywane pliki PDF, dokumenty pakietu Office, obrazy i inne typy zawartości. Łamanie dokumentów przy użyciu wyodrębniania tekstu nie jest naliczane. W przypadku rozpakowywania dokumentu przy użyciu wyodrębniania obrazów opłaty są naliczone według stawek, które można znaleźć na [stronie cennika](https://azure.microsoft.com/pricing/details/search/)Azure Search.

Chociaż wszystkie dokumenty będą rozpękane, wzbogaca się tylko wtedy, gdy użytkownik jawnie udostępni odpowiednie umiejętności. Na przykład jeśli potok składa się wyłącznie z analizy tekstu, wszystkie obrazy w kontenerze lub dokumentach zostaną zignorowane.

Indeksator obiektów BLOB zawiera parametry konfiguracji i obsługuje śledzenie zmian, jeśli dane podstawowe zawierają wystarczające informacje. Więcej informacji na temat podstawowych funkcji można znaleźć w [Azure Search indeksator magazynu obiektów BLOB](search-howto-indexing-azure-blob-storage.md).

## <a name="add-ai"></a>Dodaj AI

*Umiejętności* są indywidualnymi składnikami przetwarzania AI, których można używać autonomiczne lub w połączeniu z innymi umiejętnościami do przetwarzania sekwencyjnego. 

+ Wbudowane umiejętności są obsługiwane przez Cognitive Services, z analizą obrazów na podstawie przetwarzanie obrazów i przetwarzania w języku naturalnym opartym na analiza tekstu. Kilka przykładów to [OCR](cognitive-search-skill-ocr.md), [rozpoznawanie jednostek](cognitive-search-skill-entity-recognition.md)i [Analiza obrazu](cognitive-search-skill-image-analysis.md). Możesz zapoznać się z pełną listą wbudowanych umiejętności we [wstępnie zdefiniowanych umiejętnościach związanych z wzbogacaniem zawartości](cognitive-search-predefined-skills.md).

+ Niestandardowe umiejętności to kod niestandardowy, opakowany w definicję interfejsu, która umożliwia integrację z potokiem. W rozwiązaniach klientów powszechną techniką jest korzystanie z nich zarówno z niestandardowymi umiejętnościami, jak i modułami AI "open source", innych firm lub firm.

*Zestawu umiejętności* to zbiór umiejętności użytych w potoku i jest wywoływany po fazie pękania dokumentów. Indeksator może używać dokładnie jednego zestawu umiejętności, ale zestawu umiejętności istnieje niezależnie od indeksatora, aby można go było ponownie wykorzystać w innych scenariuszach.

Umiejętności niestandardowe mogą być solidnie skomplikowane, ale mogą być proste i łatwe w realizacji. Jeśli masz istniejące pakiety, które zapewniają dopasowania wzorców lub modele klasyfikacji, zawartość wyodrębnianą z obiektów BLOB może zostać przeniesiona do tych modeli w celu przetworzenia. Ponieważ wzbogacanie AI korzysta z platformy Azure, model powinien również znajdować się na platformie Azure. Niektóre typowe metody hostingu obejmują używanie [Azure Functions](cognitive-search-create-custom-skill-example.md) lub [kontenerów](https://github.com/Microsoft/SkillsExtractorCognitiveSearch).

Wbudowane umiejętności, które są obsługiwane przez Cognitive Services wymagają [dołączonego Cognitive Services](cognitive-search-attach-cognitive-services.md) klucz subskrypcji All-in-one, który zapewnia dostęp do zasobu. Klucz "wszystko w jednym" umożliwia analizę obrazu, wykrywanie języka, tłumaczenie tekstu i analizę tekstu. Inne wbudowane umiejętności to funkcje Azure Search i nie wymagają dodatkowych usług ani kluczy. Kształtowanie tekstu, rozdzielacz i scalanie to przykłady umiejętności pomocnika, które są czasami niezbędne podczas projektowania potoku.

Jeśli używasz tylko umiejętności niestandardowych i wbudowanych narzędzi, nie ma żadnej zależności ani kosztów związanych z Cognitive Services.

<!-- ## Order of operations

Now we've covered indexers, content extraction, and skills, we can take a closer look at pipeline mechanisms and order of operations.

A skillset is a composition of one or more skills. When multiple skills are involved, the skillset operates as sequential pipeline, producing dependency graphs, where output from one skill becomes input to another. 

For example, given a large blob of unstructured text, a sample order of operations for text analytics might be as follows:

1. Use Text Splitter to break the blob into smaller parts.
1. Use Language Detection to determine if content is English or another language.
1. Use Text Translator to get all text into a common language.
1. Run Entity Recognition, Key Phrase Extraction, or Sentiment Analysis on chunks of text. In this step, new fields are created and populated. Entities might be location, people, organization, dates. Key phrases are short combinations of words that appear to belong together. Sentiment score is a rating on continuum of negative (0) to positive (1) sentiment.
1. Use Text Merger to reconstitute the document from the smaller chunks. -->

## <a name="how-to-use-ai-enriched-content"></a>Jak korzystać z zawartości wzbogaconej AI

Dane wyjściowe wzbogacania AI to indeks wyszukiwania na Azure Search lub magazyn wiedzy w usłudze Azure Storage.

W Azure Search indeks wyszukiwania jest używany na potrzeby interaktywnej eksploracji przy użyciu dowolnego tekstu i filtrowanych zapytań w aplikacji klienckiej. Wzbogacone dokumenty utworzone za pomocą AI są sformatowane w formacie JSON i indeksowane w taki sam sposób, jak wszystkie dokumenty są indeksowane w Azure Search, przy użyciu wszystkich korzyści oferowanych przez indeksator. Na przykład podczas indeksowania indeksator obiektu BLOB odwołuje się do parametrów i ustawień konfiguracji w celu wykorzystania dowolnego mapowania pól lub logiki wykrywania zmian. Takie ustawienia są w pełni dostępne dla regularnego indeksowania i wzbogaconych obciążeń AI. Indeksowanie końcowe, gdy zawartość jest przechowywana w Azure Search, można tworzyć rozbudowane zapytania i wyrażenia filtru, aby zrozumieć zawartość.

W usłudze Azure Storage magazyn wiedzy ma dwie manifesty: kontener obiektów blob lub tabele w magazynie tabel. 

+ Kontener obiektów BLOB przechwytuje w całości wzbogacone dokumenty, co jest przydatne, jeśli chcesz uzyskać strumieniowe źródło danych do innych procesów. 

+ W przeciwieństwie do magazynu tabel można obsłużyć fizyczne projekcje ulepszonych dokumentów. Można tworzyć wycinki lub warstwy wzbogaconych dokumentów, które zawierają lub wykluczają określone części. Do analizy w Power BI tabele w usłudze Azure Table Storage stają się źródłem danych w celu przeprowadzenia dalszej wizualizacji i eksploracji.

Wzbogacony dokument na końcu potoku różni się od oryginalnej wersji wejściowej przez obecność dodatkowych pól zawierających nowe informacje, które zostały wyodrębnione lub wygenerowane podczas wzbogacania. W związku z tym można korzystać z kombinacji oryginalnej i utworzonej zawartości niezależnie od używanej struktury danych wyjściowych.

## <a name="next-steps"></a>Następne kroki

Istnieje dużo więcej możliwości dzięki wzbogacaniu AI, aby maksymalnie wykorzystać swoje dane w usłudze Azure Storage, w tym łączenie Cognitive Services na różne sposoby i tworzenie niestandardowych umiejętności w przypadkach, gdy nie ma istniejącej usługi poznawczej dla tego scenariusza. Więcej informacji można uzyskać, korzystając z poniższych linków.

> [!div class="nextstepaction"]
> [Omówienie wzbogacenia AI](cognitive-search-concept-intro.md) 
>  Utwórz węzły mapy[zestawu umiejętności](cognitive-search-defining-skillset.md)
> [w drzewie adnotacji](cognitive-search-output-field-mapping.md)
