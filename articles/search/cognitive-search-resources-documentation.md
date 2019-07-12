---
title: Usługa cognitive search zasoby dokumentacji — usługa Azure Search
description: Adnotacjami listę artykułów, samouczki, przykłady i blog publikuje obciążeń związanych z cognitive wyszukiwania w usłudze Azure Search.
services: search
manager: cgronlun
author: HeidiSteen
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 41637fae5592ac292da22303071d51b43116c78b
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2019
ms.locfileid: "67671904"
---
# <a name="documentation-resources-for-cognitive-search-workloads"></a>Zasoby dokumentacji dla obciążeń wyszukiwania kognitywnego

Usługa cognitive search, teraz ogólnie dostępna, jest nową wzbogacania warstwę usługi Azure Search indeksowanie, odnajduje informacje ukryte w źródłach innych niż tekst i tekstu takimi samymi, przekształcania go w zawartość do przeszukiwania pełnotekstowego w usłudze Azure Search.

Pełną dokumentację usługi cognitive search są następujące artykuły.

## <a name="getting-started"></a>Wprowadzenie
+ [Co to jest usługa cognitive search?](cognitive-search-concept-intro.md)
+ [Szybki start: Wypróbuj wyszukiwanie kognitywne w portalu](cognitive-search-quickstart-blob.md)
+ [Samouczek: Dowiedz się, usługa cognitive search interfejsów API](cognitive-search-tutorial-blob.md)
+ [Przykład: Tworzenie niestandardowych umiejętności do wyszukiwania kognitywnego](cognitive-search-create-custom-skill-example.md)

## <a name="how-to-guidance"></a>Wskazówki dotyczące wykonywania określonych zadań
+ [Jak Definiowanie zestawu umiejętności](cognitive-search-defining-skillset.md)
+ [Jak odwoływać się do adnotacji w zestawu umiejętności](cognitive-search-concept-annotations-syntax.md)
+ [Sposób mapowania pól do indeksu](cognitive-search-output-field-mapping.md)
+ [Jak przetwarzanie i wyodrębnianie informacji z obrazów](cognitive-search-concept-image-scenarios.md)
+ [Jak odbudować indeksu usługi Azure Search](search-howto-reindex.md)
+ [Jak zdefiniować interfejs umiejętności niestandardowe](cognitive-search-custom-skill-interface.md)
+ [Wskazówki dotyczące rozwiązywania problemów](cognitive-search-concept-troubleshooting.md)

## <a name="reference"></a>Tematy pomocy

+ [Wstępnie zdefiniowane umiejętności](cognitive-search-predefined-skills.md)
  + [Microsoft.Skills.Text.KeyPhraseSkill](cognitive-search-skill-keyphrases.md)
  + [Microsoft.Skills.Text.LanguageDetectionSkill](cognitive-search-skill-language-detection.md)
  + [Microsoft.Skills.Text.NamedEntityRecognitionSkill](cognitive-search-skill-named-entity-recognition.md)
  + [Microsoft.Skills.Text.MergeSkill](cognitive-search-skill-textmerger.md)
  + [Microsoft.Skills.Text.SplitSkill](cognitive-search-skill-textsplit.md)
  + [Microsoft.Skills.Text.SentimentSkill](cognitive-search-skill-sentiment.md)
  + [Microsoft.Skills.Vision.ImageAnalysisSkill](cognitive-search-skill-image-analysis.md)
  + [Microsoft.Skills.Vision.OcrSkill](cognitive-search-skill-ocr.md)
  + [Microsoft.Skills.Util.ShaperSkill](cognitive-search-skill-shaper.md)

+ [REST API](https://docs.microsoft.com/rest/api/searchservice/)
  + [Tworzenie zestawu umiejętności (wersja api-version = 2019-05-06)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
  + [Tworzenie indeksatora (wersja api-version = 2019-05-06)](https://docs.microsoft.com/rest/api/searchservice/create-indexer)

## <a name="see-also"></a>Zobacz także

+ [Interfejs API REST usługi Azure Search](https://docs.microsoft.com/rest/api/searchservice/)
+ [Indeksatory w usłudze Azure Search](search-indexer-overview.md)
+ [Co to jest usługa Azure Search?](search-what-is-azure-search.md)
