---
title: Zasoby dokumentacji wyszukiwania poznawczego — Azure Search
description: Lista artykułów, samouczków, przykładów i wpisów w blogu związanych z obciążeniami wyszukiwania poznawczego w Azure Search.
services: search
manager: cgronlun
author: HeidiSteen
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: heidist
ms.subservice: cognitive-search
ms.openlocfilehash: c7c0d22944726959ae3eaf036d30bcde3ca86900
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/06/2019
ms.locfileid: "68841124"
---
# <a name="documentation-resources-for-cognitive-search-workloads"></a>Zasoby dokumentacji dotyczące obciążeń wyszukiwania poznawczego

Wyszukiwanie poznawcze, teraz ogólnie dostępna, jest nową warstwą wzbogacania w Azure Search indeksowania, która umożliwia znalezienie ukrytych informacji w źródłach nietekstowych i niezróżnicowanym tekście, przeciąganie go do zawartości w postaci tekstu pełnotekstowego w Azure Search.

Poniższe artykuły stanowią kompletną dokumentację wyszukiwania poznawczego.

## <a name="getting-started"></a>Wprowadzenie
+ [Co to jest wyszukiwanie poznawcze?](cognitive-search-concept-intro.md)
+ [Szybki start: Wypróbuj usługę wyszukiwania poznawczego w portalu](cognitive-search-quickstart-blob.md)
+ [Samouczek: Poznaj interfejsy API wyszukiwania poznawczego](cognitive-search-tutorial-blob.md)
+ [Przykład: Tworzenie niestandardowej umiejętności wyszukiwania poznawczego](cognitive-search-create-custom-skill-example.md)

## <a name="how-to-guidance"></a>Wskazówki dotyczące porady
+ [Jak zdefiniować zestawu umiejętności](cognitive-search-defining-skillset.md)
+ [Jak odwoływać się do adnotacji w zestawu umiejętności](cognitive-search-concept-annotations-syntax.md)
+ [Jak mapować pola na indeks](cognitive-search-output-field-mapping.md)
+ [Jak przetwarzać i wyodrębniać informacje z obrazów](cognitive-search-concept-image-scenarios.md)
+ [Jak skompilować indeks Azure Search](search-howto-reindex.md)
+ [Jak zdefiniować niestandardowy interfejs umiejętności](cognitive-search-custom-skill-interface.md)
+ [Wskazówki dotyczące rozwiązywania problemów](cognitive-search-concept-troubleshooting.md)

## <a name="reference"></a>Tematy pomocy

+ [Wstępnie zdefiniowane umiejętności](cognitive-search-predefined-skills.md)
  + [Microsoft. umiejętności. Text. KeyPhraseExtractionSkill](cognitive-search-skill-keyphrases.md)
  + [Microsoft.Skills.Text.LanguageDetectionSkill](cognitive-search-skill-language-detection.md)
  + [Microsoft.Skills.Text.EntityRecognitionSkill](cognitive-search-skill-entity-recognition.md)
  + [Microsoft.Skills.Text.MergeSkill](cognitive-search-skill-textmerger.md)
  + [Microsoft.Skills.Text.SplitSkill](cognitive-search-skill-textsplit.md)
  + [Microsoft.Skills.Text.SentimentSkill](cognitive-search-skill-sentiment.md)
  + [Microsoft. umiejętności. Text. TranslationSkill](cognitive-search-skill-text-translation.md)
  + [Microsoft.Skills.Vision.ImageAnalysisSkill](cognitive-search-skill-image-analysis.md)
  + [Microsoft.Skills.Vision.OcrSkill](cognitive-search-skill-ocr.md)
  + [Microsoft. umiejętności. util. ConditionalSkill](cognitive-search-skill-conditional.md)
  + [Microsoft.Skills.Util.ShaperSkill](cognitive-search-skill-shaper.md)

+ Umiejętności niestandardowe
  + [Microsoft.Skills.Custom.WebApiSkill](cognitive-search-custom-skill-web-api.md)

+ [Przestarzałe umiejętności](cognitive-search-skill-deprecated.md)
  + [Microsoft.Skills.Text.NamedEntityRecognitionSkill](cognitive-search-skill-named-entity-recognition.md)

+ [REST API](https://docs.microsoft.com/rest/api/searchservice/)
  + [Create zestawu umiejętności (API-Version = 2019-05-06)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
  + [Create indeksator (API-Version = 2019-05-06)](https://docs.microsoft.com/rest/api/searchservice/create-indexer)

## <a name="see-also"></a>Zobacz także

+ [Interfejs API REST Azure Search](https://docs.microsoft.com/rest/api/searchservice/)
+ [Indeksatory w Azure Search](search-indexer-overview.md)
+ [Co to jest usługa Azure Search?](search-what-is-azure-search.md)
