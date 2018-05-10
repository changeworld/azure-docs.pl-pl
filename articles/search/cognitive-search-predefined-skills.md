---
title: Wstępnie zdefiniowane wyodrębniania danych, języka naturalnego, obrazu przetwarzania umiejętności (Azure Search) | Dokumentacja firmy Microsoft
description: Wyodrębniania danych, języka naturalnego przetwarzania kognitywnych umiejętności obrazu dodać semantykę i struktura do nieprzetworzonej zawartości w potoku działanie usługi Azure.
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: luisca
ms.openlocfilehash: 870cf9629c7af8faee0ce5709199b64910b27ffb
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
---
# <a name="predefined-skills-for-content-enrichment-azure-search"></a>Umiejętności wstępnie zdefiniowanych dla zawartości wzbogacenia (Azure Search)

W tym artykule opisano kognitywnych umiejętności wyposażone kognitywnych wyszukiwania. A *kognitywnych umiejętności* jest operacją przekształca zawartość w określony sposób. Często jest składnikiem, który wyodrębnia dane lub wnioskuje struktury i w związku z tym wspomaga wiedzę na temat naszych danych wejściowych. Dane wyjściowe są prawie zawsze tekstowych. A *skillset* zbiór umiejętności definiujące wzbogacenia potoku. 

## <a name="predefined-skills"></a>Wstępnie zdefiniowane umiejętności

Kilka umiejętności są elastyczne, w jakie ich typów lub utworzyć. Ogólnie rzecz biorąc większość umiejętności są oparte na wstępnie przeszkolone modeli, co oznacza, że nie nauczenia modelu, używając danych szkoleniowych. Aby uzyskać wskazówki na temat tworzenia niestandardowych umiejętności, zobacz [sposób definiowania niestandardowego interfejsu](cognitive-search-custom-skill-interface.md) i [przykład: Tworzenie niestandardowego umiejętności](cognitive-search-create-custom-skill-example.md). Poniższa tabela wylicza oraz opis umiejętności obsługiwane przez firmę Microsoft. 

| Umiejętności | Opis |
|-------|-------------|
| [Microsoft.Skills.Text.KeyPhraseSkill](cognitive-search-skill-keyphrases.md) | Ta umiejętności wykorzystuje pretrained model wykryć ważne fraz na podstawie terminu umieszczania, językowe reguł, bliskości inne postanowienia i jak nietypowe termin znajduje się w danych źródłowych. |
| [Microsoft.Skills.Text.LanguageDetectionSkill](cognitive-search-skill-language-detection.md)  | Ta jest używana umiejętności pretrained model do wykrywania języka, w którym jest używana (jeden identyfikator języka dla dokumentu). W przypadku używania wielu języków w tym samym segmentach tekst dane wyjściowe są LCID głównie używane języka.|
| [Microsoft.Skills.Text.MergerSkill](cognitive-search-skill-textmerger.md) | Konsoliduje tekstu z kolekcji pól w jedno pole.  |
| [Microsoft.Skills.Text.NamedEntityRecognitionSkill](cognitive-search-skill-named-entity-recognition.md) | Ten umiejętności wykorzystuje pretrained model ustanowienie jednostki dla ustalony zbiór kategorii: osób, lokalizacji, w organizacji. |
| [Microsoft.Skills.Text.SentimentSkill](cognitive-search-skill-sentiment.md)  | Ta umiejętności wykorzystuje pretrained model wyniki na podstawie rekordu na podstawie dodatnie lub ujemne wskaźniki nastrojów klientów. Wynik jest od 0 do 1. Neutralne wyniki występują zarówno w przypadku wartości null, jeśli nie można wykryć wskaźniki nastrojów klientów i tekstu który jest uznawane za neutralne.  |
| [Microsoft.Skills.Text.SplitSkill](cognitive-search-skill-textsplit.md) | Dzieli tekst na stron, dzięki czemu można wzbogacić lub przyrostowo rozszerzyć zawartości. |
| [Microsoft.Skills.Vision.ImageAnalysisSkill](cognitive-search-skill-image-analysis.md) | Ta umiejętności używa nieobsługiwanego algorytmu wykrywania obrazu do identyfikacji zawartości obrazu oraz do generowania tekst opisu. |
| [Microsoft.Skills.Vision.OcrSkill](cognitive-search-skill-ocr.md) | OCR. |
| [Microsoft.Skills.Util.ShaperSkill](cognitive-search-skill-shaper.md) | Dane wyjściowe mapy do typu złożonego (wieloczęściowych typu danych, która może służyć do pełna nazwa, adres wiele wierszy lub kombinacji nazwisko i osobistego identyfikatora.) |

## <a name="see-also"></a>Zobacz także

+ [Sposób definiowania skillset](cognitive-search-defining-skillset.md)
+ [Definicja interfejsu umiejętności niestandardowych](cognitive-search-custom-skill-interface.md)
+ [Samouczek: Wzbogacone indeksowanie z kognitywnych wyszukiwania](cognitive-search-tutorial-blob.md)