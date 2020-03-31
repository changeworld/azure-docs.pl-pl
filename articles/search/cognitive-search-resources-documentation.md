---
title: Łącza do dokumentacji wzbogacenia SI
titleSuffix: Azure Cognitive Search
description: Lista artykułów, samouczków, przykładów i wpisów w blogu z adnotacjami związanych z obciążeniami wzbogacania sztucznej inteligencji w usłudze Azure Cognitive Search.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/25/2020
ms.openlocfilehash: d2b25fb93a1e35ffa82cf49c60d181b841b1692d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77616189"
---
# <a name="documentation-resources-for-ai-enrichment-in-azure-cognitive-search"></a>Zasoby dokumentacji dotyczące wzbogacania sztucznej inteligencji w usłudze Azure Cognitive Search

Wzbogacanie sztucznej inteligencji to funkcja indeksowania usługi Azure Cognitive Search, która znajduje ukryte informacje w źródłach nietekstowych i niezróżnikowanym tekście, przekształcając je w pełnotekstową zawartość z możliwością przeszukiwania w usłudze Azure Cognitive Search.

Poniższe artykuły są kompletną dokumentacją wzbogacania SI.

## <a name="getting-started"></a>Wprowadzenie
+ [Wprowadzenie do sztucznej inteligencji w usłudze Azure Cognitive Search](cognitive-search-concept-intro.md)
+ [Szybki start: tworzenie zestawu umiejętności poznawczych w witrynie Azure portal](cognitive-search-quickstart-blob.md)
+ [Samouczek: Wzbogacone indeksowanie za pomocą ai](cognitive-search-tutorial-blob.md)
+ [Przykład: Tworzenie niestandardowej umiejętności wzbogacania SI](cognitive-search-create-custom-skill-example.md)

## <a name="how-to-guidance"></a>Wskazówki dotyczące poradnictwa
+ [Jak zdefiniować zestaw umiejętności](cognitive-search-defining-skillset.md)
+ [Jak odwoływać się do adnotacji w zestawie umiejętności](cognitive-search-concept-annotations-syntax.md)
+ [Jak mapować pola do indeksu](cognitive-search-output-field-mapping.md)
+ [Jak przetwarzać i wyodrębniać informacje z obrazów](cognitive-search-concept-image-scenarios.md)
+ [Jak odbudować indeks usługi Azure Cognitive Search](search-howto-reindex.md)
+ [Jak zdefiniować niestandardowy interfejs umiejętności](cognitive-search-custom-skill-interface.md)
+ [Wskazówki dotyczące rozwiązywania problemów](cognitive-search-concept-troubleshooting.md)

## <a name="reference"></a>Tematy pomocy

+ [Wbudowane umiejętności](cognitive-search-predefined-skills.md)
  + [Microsoft.Skills.Text.KeyPhraseExtractionSkill](cognitive-search-skill-keyphrases.md)
  + [Microsoft.Skills.Text.LanguageDetectionSkill](cognitive-search-skill-language-detection.md)
  + [Microsoft.Skills.Text.EntityRecognitionSkill](cognitive-search-skill-entity-recognition.md)
  + [Microsoft.Skills.Text.MergeSkill](cognitive-search-skill-textmerger.md)
  + [Microsoft.skills.text.PIIDetectionSkill](cognitive-search-skill-pii-detection.md)
  + [Microsoft.Skills.Text.SplitSkill](cognitive-search-skill-textsplit.md)
  + [Microsoft.Skills.Text.SentimentSkill](cognitive-search-skill-sentiment.md)
  + [Microsoft.Skills.Text.TranslationSkill](cognitive-search-skill-text-translation.md)
  + [Microsoft.Skills.Vision.ImageAnalysisSkill](cognitive-search-skill-image-analysis.md)
  + [Microsoft.Skills.Vision.OcrSkill](cognitive-search-skill-ocr.md)
  + [Umiejętności firmy Microsoft.Util.ConditionalSkill](cognitive-search-skill-conditional.md)
  + [Microsoft.Skills.Util.DocumentExtractionSkill](cognitive-search-skill-document-extraction.md)
  + [Microsoft.Skills.Util.ShaperSkill](cognitive-search-skill-shaper.md)

+ Umiejętności niestandardowe
  + [Microsoft.Skills.Custom.WebApiSkill](cognitive-search-custom-skill-web-api.md)

+ [Przestarzałe umiejętności](cognitive-search-skill-deprecated.md)
  + [Microsoft.Skills.Text.NamedEntityRecognitionSkill](cognitive-search-skill-named-entity-recognition.md)

+ [INTERFEJS API ODPOCZYNKU](https://docs.microsoft.com/rest/api/searchservice/)
  + [Tworzenie skillset (api-version=2019-05-06)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
  + [Utwórz indeksator (api-version=2019-05-06)](https://docs.microsoft.com/rest/api/searchservice/create-indexer)

## <a name="see-also"></a>Zobacz też

+ [Interfejs API REST usługi Azure Cognitive Search](https://docs.microsoft.com/rest/api/searchservice/)
+ [Indeksatory w usłudze Azure Cognitive Search](search-indexer-overview.md)
+ [Co to jest wyszukiwanie poznawcze na platformie Azure?](search-what-is-azure-search.md)
