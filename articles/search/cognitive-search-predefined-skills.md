---
title: Wbudowane wyodrębnianie danych, język naturalny, przetwarzanie obrazów — Azure Search
description: Wyodrębnianie danych, język naturalny, przetwarzanie obrazów umiejętności poznawcze Dodawanie semantyki i struktury do nieprzetworzonej zawartości w potoku Azure Search.
manager: nitinme
author: luiscabrer
services: search
ms.service: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: luisca
ms.openlocfilehash: 061d969269eb526b8f02f14de58e2da20c459a38
ms.sourcegitcommit: 3f22ae300425fb30be47992c7e46f0abc2e68478
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2019
ms.locfileid: "71265491"
---
# <a name="predefined-skills-for-content-enrichment-azure-search"></a>Wstępnie zdefiniowane umiejętności wzbogacania zawartości (Azure Search)

Ten artykuł zawiera informacje na temat umiejętności poznawczych, które są dostępne w Azure Search. *Umiejętność* jest operacją, która przekształca zawartość w jakiś sposób. Często jest to składnik, który wyodrębnia dane lub rozpoznaje strukturę, i w związku z tym rozszerza nasze zrozumienie danych wejściowych. Prawie zawsze, dane wyjściowe są oparte na tekście. *Zestawu umiejętności* to zbiór umiejętności, które definiują potok wzbogacania. 

> [!NOTE]
> Podczas rozszerzania zakresu przez zwiększenie częstotliwości przetwarzania, Dodawanie większej liczby dokumentów lub Dodawanie algorytmów AI, należy [dołączyć Cognitive Services rozliczanego zasobu](cognitive-search-attach-cognitive-services.md). Opłaty naliczane podczas wywoływania interfejsów API w Cognitive Services oraz do wyodrębniania obrazów w ramach etapu łamania dokumentu w Azure Search. Nie są naliczane opłaty za Wyodrębnianie tekstu z dokumentów.
>
> Do wykonania wbudowanych umiejętności są naliczane opłaty za istniejące [Cognitive Services cena płatność zgodnie z rzeczywistym](https://azure.microsoft.com/pricing/details/cognitive-services/)użyciem. Cennik wyodrębniania obrazów został opisany na [stronie cennika Azure Search](https://go.microsoft.com/fwlink/?linkid=2042400).


## <a name="predefined-skills"></a>Wstępnie zdefiniowane umiejętności

Kilka umiejętności jest elastycznych do użycia lub produkcji. Ogólnie rzecz biorąc, większość umiejętności opiera się na wstępnie szkolonych modelach, co oznacza, że nie można nauczyć modelu przy użyciu własnych danych szkoleniowych. W poniższej tabeli wymieniono i opisano umiejętności udostępniane przez firmę Microsoft. 

| Czy | Opis |
|-------|-------------|
| [Microsoft.Skills.Text.KeyPhraseSkill](cognitive-search-skill-keyphrases.md) | Ta umiejętność używa przedniego modelu do wykrywania ważnych fraz opartych na rozmieszczeniu, regułach językowych, sąsiedztwie z innymi postanowieniami oraz o tym, jak nietypowy termin znajduje się w danych źródłowych. |
| [Microsoft.Skills.Text.LanguageDetectionSkill](cognitive-search-skill-language-detection.md)  | Ta umiejętność używa preszkolenego modelu do wykrywania używanego języka (jeden identyfikator języka na dokument). Gdy w tych samych segmentach tekstu są używane wiele języków, dane wyjściowe są identyfikatorem LCID gotowego do użycia języka.|
| [Microsoft.Skills.Text.MergeSkill](cognitive-search-skill-textmerger.md) | Konsoliduje tekst z kolekcji pól w jedno pole.  |
| [Microsoft.Skills.Text.EntityRecognitionSkill](cognitive-search-skill-entity-recognition.md) | Ta umiejętność używa przedniego modelu do ustanowienia jednostek dla ustalonego zestawu kategorii: ludzie, lokalizacja, organizacja, wiadomości e-mail, adresy URL, pola DateTime. |
| [Microsoft.Skills.Text.SentimentSkill](cognitive-search-skill-sentiment.md)  | Ta umiejętność używa przedszkolnego modelu do oceny wartości dodatnich lub ujemnych tonacji w rekordach według podstaw rekordów. Wynik jest z przedziału od 0 do 1. Neutralne oceny występują dla przypadku wartości null, gdy nie można wykryć tonacji oraz dla tekstu, który jest uznawany za neutralny.  |
| [Microsoft.Skills.Text.SplitSkill](cognitive-search-skill-textsplit.md) | Dzieli tekst na strony, aby można było wzbogacić lub rozszerzyć zawartość. |
| [Microsoft. umiejętności. Text. TranslationSkill](cognitive-search-skill-text-translation.md) | Ta umiejętność korzysta ze zintegrowanego modelu, aby przetłumaczyć tekst wejściowy na różne języki do normalizacji lub przypadków użycia. |
| [Microsoft.Skills.Vision.ImageAnalysisSkill](cognitive-search-skill-image-analysis.md) | Ta umiejętność używa algorytmu wykrywania obrazu do identyfikowania zawartości obrazu i generowania opisu tekstu. |
| [Microsoft.Skills.Vision.OcrSkill](cognitive-search-skill-ocr.md) | Optyczne rozpoznawanie znaków. |
| [Microsoft. umiejętności. util. ConditionalSkill](cognitive-search-skill-conditional.md) | Umożliwia filtrowanie, przypisywanie wartości domyślnej i scalanie danych na podstawie warunku.|
| [Microsoft.Skills.Util.ShaperSkill](cognitive-search-skill-shaper.md) | Mapuje dane wyjściowe do typu złożonego (wieloczęściowego typu danych, który może być używany do pełnej nazwy, adresu wielowierszowego lub kombinacji nazwiska i identyfikatora osobistego). |
| [Microsoft.Skills.Custom.WebApiSkill](cognitive-search-custom-skill-web-api.md) | Umożliwia rozszerzanie potoku wyszukiwania poznawczego przez nadanie połączenia HTTP do niestandardowego interfejsu API sieci Web |


Aby uzyskać wskazówki dotyczące tworzenia [niestandardowych umiejętności](cognitive-search-custom-skill-web-api.md), zobacz [jak zdefiniować niestandardowy interfejs](cognitive-search-custom-skill-interface.md) i [przykład: Tworzenie niestandardowej umiejętności wyszukiwania](cognitive-search-create-custom-skill-example.md)poznawczego.

## <a name="see-also"></a>Zobacz także

+ [Jak zdefiniować zestawu umiejętności](cognitive-search-defining-skillset.md)
+ [Niestandardowa definicja interfejsu umiejętności](cognitive-search-custom-skill-interface.md)
+ [Samouczek: Ulepszony indeksowanie za pomocą wyszukiwania poznawczego](cognitive-search-tutorial-blob.md)
