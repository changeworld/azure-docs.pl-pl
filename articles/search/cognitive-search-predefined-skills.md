---
title: Wyodrębnianie danych wbudowane, języka naturalnego obrazu przetwarzanie — usługa Azure Search
description: Wyodrębnianie danych, języka naturalnego, obrazów, przetwarzania umiejętności poznawcze Dodaj semantyki i struktury do nieprzetworzonej zawartości w potoku usługi Azure Search.
manager: pablocas
author: luiscabrer
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: c9dfa6af4fb13018051c06783e5ae2bc3f49c0da
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2019
ms.locfileid: "67672088"
---
# <a name="predefined-skills-for-content-enrichment-azure-search"></a>Wstępnie zdefiniowane umiejętności wzbogacania zawartości (Azure Search)

W tym artykule poznasz umiejętności poznawcze dostarczane z usługą Azure Search. A *cognitive umiejętności* jest operacją, która przekształca zawartość w jakiś sposób. Często jest składnikiem, który wyodrębnia dane lub wnioskuje struktury, a w związku z tym rozszerzają zrozumienie danych wejściowych. Prawie zawsze wynikiem jest oparte na tekście. A *zestawu umiejętności* to zbiór umiejętności, które definiują wzbogacony potok. 

> [!NOTE]
> Możesz rozwiń zakres, zwiększając częstotliwości przetwarzania, dodając więcej dokumentów lub dodanie więcej algorytmów sztucznej Inteligencji, konieczne będzie [dołączyć płatnych zasobu usług Cognitive Services](cognitive-search-attach-cognitive-services.md). Opłaty są naliczane podczas wywoływania interfejsów API w usługach Cognitive Services i wyodrębniania obrazu jako część etap łamania dokumentów w usłudze Azure Search. Opłaty nie będą naliczane do wyodrębniania tekstu z dokumentów.
>
> Wykonanie wbudowanego umiejętności podlega opłacie za istniejącą [usług Cognitive Services, płatności — jako — można przejść cena](https://azure.microsoft.com/pricing/details/cognitive-services/). Cennik wyodrębniania obraz został opisany na [usługi Azure Search stronę z cennikiem](https://go.microsoft.com/fwlink/?linkid=2042400).


## <a name="predefined-skills"></a>Wstępnie zdefiniowane umiejętności

Kilka umiejętności są elastyczne, w jaki ich tworzą lub wykorzystują. Ogólnie rzecz biorąc większość umiejętności są oparte na wstępnie szkolone modele, co oznacza, że nie uczenie modelu przy użyciu danych szkoleniowych. Poniższa tabela wylicza i opisuje umiejętności, obsługiwane przez firmę Microsoft. 

| Umiejętności | Opis |
|-------|-------------|
| [Microsoft.Skills.Text.KeyPhraseSkill](cognitive-search-skill-keyphrases.md) | To umiejętności korzysta z modelu pretrained wykrywania ważne fraz na podstawie położenia termin, zasady językowe, bliskość inne postanowienia i jak nietypowe termin mieści się w danych źródłowych. |
| [Microsoft.Skills.Text.LanguageDetectionSkill](cognitive-search-skill-language-detection.md)  | Ta używana umiejętności pretrained model, aby wykryć język, który jest używany (jeden język identyfikator dla dokumentu). Gdy używanych jest wiele języków, w tym samym segmentach tekstu, wynikiem jest LCID głównie używane języka.|
| [Microsoft.Skills.Text.MergeSkill](cognitive-search-skill-textmerger.md) | Konsoliduje tekst z kolekcji pól do pojedynczego pola.  |
| [Microsoft.Skills.Text.EntityRecognitionSkill](cognitive-search-skill-entity-recognition.md) | To umiejętności korzysta z modelu pretrained nawiązać jednostki dla stały zestaw kategorii: osób, lokalizacji, organizacji, wysłanie wiadomości e-mail, adresy URL, pola daty/godziny. |
| [Microsoft.Skills.Text.SentimentSkill](cognitive-search-skill-sentiment.md)  | To umiejętności wykorzystuje pretrained model ocena tonacji dodatnie lub ujemne na podstawie rekordu na podstawie. Wynik jest od 0 do 1. Neutralne wyniki występować zarówno w przypadku wartości null nie można wykryć opinii, gdy tekst, są uznawane za neutralne.  |
| [Microsoft.Skills.Text.SplitSkill](cognitive-search-skill-textsplit.md) | Dzieli tekst na stronach, tak, aby można wzbogacić lub rozszerzyć zawartości przyrostowo. |
| [Microsoft.Skills.Vision.ImageAnalysisSkill](cognitive-search-skill-image-analysis.md) | To umiejętności używa algorytmu wykrywania obrazu do identyfikacji zawartości obrazu i generowania opis tekstowy. |
| [Microsoft.Skills.Vision.OcrSkill](cognitive-search-skill-ocr.md) | Optyczne rozpoznawanie znaków. |
| [Microsoft.Skills.Util.ShaperSkill](cognitive-search-skill-shaper.md) | Dane wyjściowe mapy typu złożonego (wieloczęściowy typu danych, która może służyć do Podaj pełną nazwę, adres wielowierszowego pola lub kombinację nazwisko i identyfikator osobisty.) |
| [Microsoft.Skills.Custom.WebApiSkill](cognitive-search-custom-skill-web-api.md) | Umożliwia rozszerzalność w potoku w usłudze wyszukiwania poznawczego przez wywołania HTTP do niestandardowego internetowego interfejsu API |


Aby uzyskać wskazówki dotyczące tworzenia [umiejętności niestandardowe](cognitive-search-custom-skill-web-api.md), zobacz [jak zdefiniować niestandardowy interfejs](cognitive-search-custom-skill-interface.md) i [przykładu: Tworzenie niestandardowych umiejętności do wyszukiwania kognitywnego](cognitive-search-create-custom-skill-example.md).

## <a name="see-also"></a>Zobacz także

+ [Jak Definiowanie zestawu umiejętności](cognitive-search-defining-skillset.md)
+ [Niestandardowa definicja interfejsu umiejętności](cognitive-search-custom-skill-interface.md)
+ [Samouczek: Wzbogacone indeksowanie za pomocą wyszukiwania kognitywnego](cognitive-search-tutorial-blob.md)
