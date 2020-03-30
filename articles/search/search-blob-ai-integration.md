---
title: Używanie interfejsu AI do zrozumienia danych magazynu obiektów Blob
titleSuffix: Azure Cognitive Search
description: Dodaj semantyczne przetwarzanie języka naturalnego i analizę obrazów do obiektów blob platformy Azure przy użyciu potoku wzbogacania sztucznej inteligencji w usłudze Azure Cognitive Search.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 591437eb3951164d53388b6164103948e9ad65e0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73496431"
---
# <a name="use-ai-to-understand-blob-storage-data"></a>Używanie interfejsu AI do zrozumienia danych magazynu obiektów Blob

Dane w magazynie obiektów Blob platformy Azure to często wiele nieustrukturyzowanych treści, takich jak obrazy, długi tekst, pliki PDF i dokumenty pakietu Office. Korzystając z możliwości sztucznej inteligencji w usłudze Azure Cognitive Search, można zrozumieć i wyodrębnić cenne informacje z obiektów blob na różne sposoby. Przykłady zastosowania AI do zawartości obiektu blob obejmują:

+ Wyodrębnianie tekstu z obrazów za pomocą optycznego rozpoznawania znaków (OCR)
+ Tworzenie opisu sceny lub znaczników ze zdjęcia
+ Wykrywanie języka i tłumaczenie tekstu na różne języki
+ Przetwarzanie tekstu z rozpoznawaniem nazwanych jednostek (NER) w celu znajdowania odwołań do osób, dat, miejsc lub organizacji 

Chociaż może być potrzebny tylko jeden z tych możliwości AI, często łączy się wiele z nich w tym samym potoku (na przykład wyodrębnianie tekstu ze skanowanego obrazu, a następnie znajdowanie wszystkich dat i miejsc, do których się odwołuje). 

Wzbogacanie SI tworzy nowe informacje, przechwycone jako tekst, przechowywane w polach. Po wzbogaceniu można uzyskać dostęp do tych informacji z indeksu wyszukiwania za pomocą wyszukiwania pełnotekstowego lub wysłać wzbogacone dokumenty z powrotem do magazynu platformy Azure, aby zasilać nowe środowiska aplikacji, które obejmują eksplorowanie danych w scenariuszach odnajdywania lub analizy. 

W tym artykule możemy wyświetlić wzbogacanie AI przez szeroki obiektyw, dzięki czemu można szybko zrozumieć cały proces, od przekształcania nieprzetworzonych danych w obiektach blob, do queryable informacji w indeksie wyszukiwania lub magazynu wiedzy.

## <a name="what-it-means-to-enrich-blob-data-with-ai"></a>Co to znaczy "wzbogacić" dane obiektów blob za pomocą AI

*Wzbogacanie sztucznej inteligencji* jest częścią architektury indeksowania usługi Azure Cognitive Search, która integruje wbudowaną sztuczną inteligencję firmy Microsoft lub niestandardową sztuczną inteligencję, którą udostępniasz. Pomaga wdrożyć scenariusze end-to-end, w których trzeba przetwarzać obiekty blob (zarówno istniejące, jak i nowe, gdy wchodzą lub są aktualizowane), otwierać wszystkie formaty plików, aby wyodrębnić obrazy i tekst, wyodrębniać żądane informacje za pomocą różnych możliwości AI, i indeksować je w indeksie wyszukiwania do szybkiego wyszukiwania, wyszukiwania i eksploracji. 

Dane wejściowe są obiekty blobs, w jednym kontenerze, w magazynie obiektów Blob platformy Azure. Obiekty BLOB mogą być prawie dowolnym rodzajem tekstu lub danych obrazu. 

Dane wyjściowe są zawsze indeks wyszukiwania, używane do szybkiego wyszukiwania tekstu, pobierania i eksploracji w aplikacjach klienckich. Ponadto dane wyjściowe mogą być również *magazynem wiedzy,* który projektuje wzbogacone dokumenty w obiekty blob platformy Azure lub tabele platformy Azure do dalszej analizy w narzędziach, takich jak usługa Power BI lub w obciążeniach do nauki o danych.

W międzyczasie jest sama architektura potoku. Potok jest oparty na funkcji *indeksatora,* do której można przypisać *zestaw umiejętności*, który składa się z jednej lub więcej *umiejętności* zapewniających AI. Celem potoku jest tworzenie *wzbogaconych dokumentów,* które wchodzą jako zawartość nieprzetworzona, ale odbierają dodatkową strukturę, kontekst i informacje podczas przechodzenia przez potok. Wzbogacone dokumenty są używane podczas indeksowania do tworzenia odwróconych indeksów i innych struktur używanych w pełnym wyszukiwaniu tekstu lub eksploracji i analizie.

## <a name="start-with-services"></a>Zacznij od usług

Potrzebujesz usługi Azure Cognitive Search i usługi Azure Blob storage. W magazynie obiektów Blob potrzebny jest kontener, który udostępnia zawartość źródłową.

Możesz rozpocząć bezpośrednio na stronie portalu konta magazynu. Na lewej stronie nawigacji w obszarze **Usługa obiektów Blob** kliknij pozycję **Dodaj usługę Azure Cognitive Search,** aby utworzyć nową usługę lub wybierz istniejącą. 

Po dodaniu usługi Azure Cognitive Search do konta magazynu można wykonać standardowy proces wzbogacenia danych w dowolnym źródle danych platformy Azure. Zalecamy **kreatora importu danych** w usłudze Azure Cognitive Search, aby ułatwić początkowe wprowadzenie do wzbogacania sztucznej inteligencji. Ten przewodnik Szybki start przeprowadzi Cię przez kroki: [Utwórz potok wzbogacania si w portalu](cognitive-search-quickstart-blob.md). 

W poniższych sekcjach omówimy więcej składników i koncepcji.

## <a name="use-a-blob-indexer"></a>Używanie indeksatora obiektów blob

Wzbogacanie sztucznej inteligencji jest dodatkiem do potoku indeksowania, a w usłudze Azure Cognitive Search te potoki są zbudowane na *podstawie indeksatora.* Indeksator jest podusługą obsługującą źródła danych wyposażoną w wewnętrzną logikę pobierania próbek danych, odczytywania danych metadanych, pobierania danych i serializacji danych z formatów natywnych do dokumentów JSON w celu późniejszego importu. Indeksatory są często używane przez siebie do importu, niezależnie od AI, ale jeśli chcesz zbudować potok wzbogacania AI, będziesz potrzebować indeksatora i zestaw umiejętności, aby przejść z nim. W tej sekcji wyróżniono indeksatora; następna sekcja koncentruje się na skillsets.

Obiekty BLOB w usłudze Azure Storage są indeksowane przy użyciu [indeksatora magazynu obiektów Blob usługi Azure Cognitive Search.](search-howto-indexing-azure-blob-storage.md) Ten indeksator można wywołać za pomocą **kreatora importu danych,** interfejsu API REST lub sdk .NET. W kodzie używasz tego indeksatora, ustawiając typ i udostępniając informacje o połączeniu, które zawiera konto usługi Azure Storage wraz z kontenerem obiektów blob. Obiekty blob można podzesetować, tworząc katalog wirtualny, który można następnie przekazać jako parametr, lub filtrując rozszerzenie typu pliku.

Indeksator wykonuje "pękanie dokumentów", otwierając obiekt blob do inspekcji zawartości. Po nawiązaniu połączenia ze źródłem danych jest to pierwszy krok w potoku. W przypadku danych obiektów blob jest to miejsce, w którym są wykrywane pliki PDF, dokumenty biurowe, obrazy i inne typy zawartości. Pękanie dokumentów z wyodrębnieniem tekstu jest bez opłat. Pękanie dokumentów z ekstrakcją obrazu jest naliczane po cenach, które można znaleźć na [stronie cenowej](https://azure.microsoft.com/pricing/details/search/).

Chociaż wszystkie dokumenty zostaną złamane, wzbogacenie występuje tylko wtedy, gdy wyraźnie zapewnisz umiejętności, aby to zrobić. Na przykład jeśli potok składa się wyłącznie z analizy obrazu, tekst w kontenerze lub dokumentów jest ignorowany.

Indeksator obiektów blob jest wyposażony w parametry konfiguracji i obsługuje śledzenie zmian, jeśli dane źródłowe zawiera wystarczające informacje. Więcej informacji na temat podstawowych funkcji w [indeksatorze magazynu obiektów Blob usługi Azure Cognitive Search](search-howto-indexing-azure-blob-storage.md).

## <a name="add-ai-components"></a>Dodawanie komponentów AI

Wzbogacanie SI odnosi się do modułów, które szukają wzorców lub cech, a następnie wykonaj odpowiednio operację. Rozpoznawanie twarzy na zdjęciach, opisy tekstowe zdjęć, wykrywanie kluczowych fraz w dokumencie i OCR (lub rozpoznawanie drukowanego lub odręcznego tekstu w plikach binarnych) są przykładami ilustracyjnych.

W usłudze Azure Cognitive Search *umiejętności* są poszczególnymi składnikami przetwarzania sztucznej inteligencji, których można używać autonomicznie lub w połączeniu z innymi umiejętnościami. 

+ Wbudowane umiejętności są wspierane przez usługi Cognitive Services, z analizą obrazu opartą na wizji komputerowej i przetwarzaniem języka naturalnego na podstawie analizy tekstu. Aby uzyskać pełną listę, zobacz [Wbudowane umiejętności wzbogacania treści](cognitive-search-predefined-skills.md).

+ Umiejętności niestandardowe są kod niestandardowy, opakowane w [definicji interfejsu,](cognitive-search-custom-skill-interface.md) który umożliwia integrację z potoku. W rozwiązaniach dla klientów powszechną praktyką jest używanie obu tych rozwiązań, z niestandardowymi umiejętnościami zapewniającymi moduły AI typu open source, innych firm lub firm.

Zestaw *umiejętności* to zbiór umiejętności używanych w potoku i jest wywoływany po tym, jak faza pękania dokumentu udostępnia zawartość. Indeksator może zużywać dokładnie jeden zestaw umiejętności, ale ten zestaw umiejętności istnieje niezależnie od indeksatora, dzięki czemu można go ponownie użyć w innych scenariuszach.

Umiejętności niestandardowe mogą wydawać się skomplikowane, ale mogą być proste i proste pod względem implementacji. Jeśli masz istniejące pakiety, które zapewniają dopasowania wzorca lub modeli klasyfikacji, zawartość wyodrębnione z obiektów blob może być przekazywana do tych modeli do przetwarzania. Ponieważ wzbogacanie sztucznej inteligencji jest oparte na platformie Azure, model powinien być również na platformie Azure. Niektóre typowe metody hostingu obejmują korzystanie z [usług Azure Functions](cognitive-search-create-custom-skill-example.md) lub [Kontenery.](https://github.com/Microsoft/SkillsExtractorCognitiveSearch)

Wbudowane umiejętności wspierane przez usługi Cognitive Services wymagają [dołączonego](cognitive-search-attach-cognitive-services.md) klucza subskrypcji usługi Cognitive Services all-in-one, który zapewnia dostęp do zasobu. Klucz typu "wszystko w jednym" umożliwia analizę obrazu, wykrywanie języka, tłumaczenie tekstu i analizę tekstu. Inne wbudowane umiejętności są funkcje usługi Azure Cognitive Search i nie wymagają żadnej dodatkowej usługi lub klucza. Shaper, rozdzielacz tekstu i fuzja są przykładami umiejętności pomocnika, które są czasami niezbędne podczas projektowania potoku.

Jeśli używasz tylko umiejętności niestandardowych i wbudowanych umiejętności użytkowych, nie ma zależności ani kosztów związanych z usługami Cognitive Services.

<!-- ## Order of operations

Now we've covered indexers, content extraction, and skills, we can take a closer look at pipeline mechanisms and order of operations.

A skillset is a composition of one or more skills. When multiple skills are involved, the skillset operates as sequential pipeline, producing dependency graphs, where output from one skill becomes input to another. 

For example, given a large blob of unstructured text, a sample order of operations for text analytics might be as follows:

1. Use Text Splitter to break the blob into smaller parts.
1. Use Language Detection to determine if content is English or another language.
1. Use Text Translator to get all text into a common language.
1. Run Entity Recognition, Key Phrase Extraction, or Sentiment Analysis on chunks of text. In this step, new fields are created and populated. Entities might be location, people, organization, dates. Key phrases are short combinations of words that appear to belong together. Sentiment score is a rating on continuum of negative (0) to positive (1) sentiment.
1. Use Text Merger to reconstitute the document from the smaller chunks. -->

## <a name="consume-ai-enriched-output-in-downstream-solutions"></a>Zużywają produkty wzbogacone o AI w rozwiązaniach niższego rzędu

Dane wyjściowe wzbogacania sztucznej inteligencji to indeks wyszukiwania w usłudze Azure Cognitive Search lub magazyn wiedzy w [usłudze](knowledge-store-concept-intro.md) Azure Storage.

W usłudze Azure Cognitive Search indeks wyszukiwania jest używany do interaktywnej eksploracji przy użyciu tekstu wolnego i filtrowanych zapytań w aplikacji klienckiej. Wzbogacone dokumenty utworzone za pośrednictwem sztucznej inteligencji są formatowane w json i indeksowane w taki sam sposób, w jaki wszystkie dokumenty są indeksowane w usłudze Azure Cognitive Search, wykorzystując wszystkie korzyści, które zapewnia indeksator. Na przykład podczas indeksowania obiektów blob indeksator odnosi się do parametrów konfiguracji i ustawień, aby wykorzystać wszelkie mapowania pól lub logikę wykrywania zmian. Takie ustawienia są w pełni dostępne dla regularnych indeksowania i obciążeń wzbogaconych o AI. Po indeksowaniu, gdy zawartość jest przechowywana w usłudze Azure Cognitive Search, można tworzyć zaawansowane zapytania i filtrować wyrażenia, aby zrozumieć zawartość.

W usłudze Azure Storage magazyn wiedzy ma dwa przejawy: kontener obiektów blob lub tabele w magazynie tabel. 

+ Kontener obiektu blob przechwytuje wzbogacone dokumenty w całości, co jest przydatne, jeśli chcesz przesłać dane do innych procesów. 

+ Z kolei przechowywanie tabel może pomieścić fizyczne projekcje wzbogaconych dokumentów. Można tworzyć wycinki lub warstwy wzbogaconych dokumentów, które zawierają lub wykluczają określone części. Do analizy w usłudze Power BI tabele w usłudze Azure Table Storage stają się źródłem danych do dalszej wizualizacji i eksploracji.

Wzbogacony dokument na końcu potoku różni się od jego oryginalnej wersji wejściowej obecnością dodatkowych pól zawierających nowe informacje, które zostały wyodrębnione lub wygenerowane podczas wzbogacania. W związku z tym można pracować z kombinacją oryginalnej i utworzonej zawartości, niezależnie od używanej struktury danych wyjściowych.

## <a name="next-steps"></a>Następne kroki

Wzbogacanie sztucznej inteligencji w celu jak większego wykorzystania danych w usłudze Azure Storage jest o wiele więcej, w tym łączenie usług Cognitive Services na różne sposoby i tworzenie umiejętności niestandardowych w przypadkach, gdy nie ma istniejącej usługi Cognitive Service dla scenariusza. Możesz dowiedzieć się więcej, klikając poniższe linki.

+ [Przekazywanie, pobieranie i listy obiektów blob za pomocą witryny Azure portal (Azure Blob storage)](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal)
+ [Konfigurowanie indeksatora obiektów blob (usługi Azure Cognitive Search)](search-howto-indexing-azure-blob-storage.md) 
+ [Omówienie wzbogacania sztucznej inteligencji (Usługa Azure Cognitive Search)](cognitive-search-concept-intro.md) 
+ [Tworzenie zestawu umiejętności (Azure Cognitive Search)](cognitive-search-defining-skillset.md)
+ [Mapuj węzły w drzewie adnotacji (Usługa Azure Cognitive Search)](cognitive-search-output-field-mapping.md)
