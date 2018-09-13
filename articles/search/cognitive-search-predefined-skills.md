---
title: Wstępnie zdefiniowane wyodrębnianie danych, języka naturalnego, obrazów, przetwarzania umiejętności (Azure Search) | Dokumentacja firmy Microsoft
description: Wyodrębnianie danych, języka naturalnego, obrazów, przetwarzania umiejętności poznawcze Dodaj semantyki i struktury do nieprzetworzonej zawartości w potoku usługi wyszukiwanie Azure.
manager: pablocas
author: luiscabrer
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: luisca
ms.openlocfilehash: cf02946c772ce2dfd04fcd0ae478a560d095c092
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/12/2018
ms.locfileid: "35938654"
---
# <a name="predefined-skills-for-content-enrichment-azure-search"></a>Wstępnie zdefiniowane umiejętności wzbogacania zawartości (Azure Search)

W tym artykule poznasz umiejętności poznawcze dostarczane z usługą Azure Search. A *cognitive umiejętności* jest operacją, która przekształca zawartość w jakiś sposób. Często jest składnikiem, który wyodrębnia dane lub wnioskuje struktury, a w związku z tym rozszerzają zrozumienie danych wejściowych. Prawie zawsze wynikiem jest oparte na tekście. A *zestawu umiejętności* to zbiór umiejętności, które definiują wzbogacony potok. 

## <a name="predefined-skills"></a>Wstępnie zdefiniowane umiejętności

Kilka umiejętności są elastyczne, w jaki ich tworzą lub wykorzystują. Ogólnie rzecz biorąc większość umiejętności są oparte na wstępnie szkolone modele, co oznacza, że nie uczenie modelu przy użyciu danych szkoleniowych. Aby uzyskać wskazówki na temat tworzenia niestandardowych umiejętności, zobacz [jak zdefiniować niestandardowy interfejs](cognitive-search-custom-skill-interface.md) i [przykład: Tworzenie niestandardowego umiejętności](cognitive-search-create-custom-skill-example.md). Poniższa tabela wylicza i opisuje umiejętności, obsługiwane przez firmę Microsoft. 

| Umiejętności | Opis |
|-------|-------------|
| [Microsoft.Skills.Text.KeyPhraseSkill](cognitive-search-skill-keyphrases.md) | To umiejętności korzysta z modelu pretrained wykrywania ważne fraz na podstawie położenia termin, zasady językowe, bliskość inne postanowienia i jak nietypowe termin mieści się w danych źródłowych. |
| [Microsoft.Skills.Text.LanguageDetectionSkill](cognitive-search-skill-language-detection.md)  | Ta używana umiejętności pretrained model, aby wykryć język, który jest używany (jeden język identyfikator dla dokumentu). Gdy używanych jest wiele języków, w tym samym segmentach tekstu, wynikiem jest LCID głównie używane języka.|
| [Microsoft.Skills.Text.MergerSkill](cognitive-search-skill-textmerger.md) | Konsoliduje tekst z kolekcji pól do pojedynczego pola.  |
| [Microsoft.Skills.Text.NamedEntityRecognitionSkill](cognitive-search-skill-named-entity-recognition.md) | To umiejętności korzysta z modelu pretrained nawiązać jednostki dla stały zestaw kategorii: osób, lokalizacji, w organizacji. |
| [Microsoft.Skills.Text.SentimentSkill](cognitive-search-skill-sentiment.md)  | To umiejętności wykorzystuje pretrained model ocena tonacji dodatnie lub ujemne na podstawie rekordu na podstawie. Wynik jest od 0 do 1. Neutralne wyniki występować zarówno w przypadku wartości null nie można wykryć opinii, gdy tekst, są uznawane za neutralne.  |
| [Microsoft.Skills.Text.SplitSkill](cognitive-search-skill-textsplit.md) | Dzieli tekst na stronach, tak, aby można wzbogacić lub rozszerzyć zawartości przyrostowo. |
| [Microsoft.Skills.Vision.ImageAnalysisSkill](cognitive-search-skill-image-analysis.md) | To umiejętności używa algorytmu wykrywania obrazu do identyfikacji zawartości obrazu i generowania opis tekstowy. |
| [Microsoft.Skills.Vision.OcrSkill](cognitive-search-skill-ocr.md) | Optyczne rozpoznawanie znaków. |
| [Microsoft.Skills.Util.ShaperSkill](cognitive-search-skill-shaper.md) | Dane wyjściowe mapy typu złożonego (wieloczęściowy typu danych, która może służyć do Podaj pełną nazwę, adres wielowierszowego pola lub kombinację nazwisko i identyfikator osobisty.) |

## <a name="see-also"></a>Zobacz także

+ [Jak Definiowanie zestawu umiejętności](cognitive-search-defining-skillset.md)
+ [Niestandardowa definicja interfejsu umiejętności](cognitive-search-custom-skill-interface.md)
+ [Samouczek: Wzbogacone indeksowanie za pomocą wyszukiwania kognitywnego](cognitive-search-tutorial-blob.md)