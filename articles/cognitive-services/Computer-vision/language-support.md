---
title: Obsługa języka — przetwarzanie obrazów
titleSuffix: Azure Cognitive Services
description: Ten artykuł zawiera listę języków naturalnych obsługiwanych przez funkcje przetwarzanie obrazów. OCR, Rozpoznawanie tekstu i odczytywanie.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: pafarley
ms.openlocfilehash: a834c68119340d796f87971912a07fc0524a6d21
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78365480"
---
# <a name="language-support-for-computer-vision"></a>Obsługa języka dla przetwarzanie obrazów

Niektóre funkcje przetwarzanie obrazów obsługują wiele języków; wszystkie funkcje, które nie są wymienione w tym miejscu, obsługują tylko język angielski.

## <a name="text-recognition"></a>Rozpoznawanie tekstu

Przetwarzanie obrazów może rozpoznawać tekst w wielu językach. Interfejs API [OCR](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc) obsługuje wiele języków, podczas gdy interfejs API [odczytu](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/2afb498089f74080d7ef85eb) i interfejs API [rozpoznawanie tekstu](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/587f2c6a154055056008f200) obsługują tylko język angielski. Aby uzyskać więcej informacji na temat tej funkcji oraz zalety poszczególnych interfejsów API, zobacz [rozpoznawanie wydrukowanych i pisanych tekstu](concept-recognizing-text.md) .

OCR automatycznie wykrywa język materiału wejściowego, dlatego nie trzeba określać kodu języka w wywołaniu interfejsu API. Jednak kody języków są zawsze zwracane jako wartość węzła `"language"` w odpowiedzi JSON.

|Język| Kod języka | INTERFEJS API OCR |
|:-----|:----:|:-----:|
|arabski | `ar`|✔ |
|Chiński uproszczony | `zh-Hans`|✔ |
|Chiński tradycyjny | `zh-Hant`|✔ |
|Czeski | `cs` |✔ |
|Duński | `da` |✔ |
|holenderski | `nl` |✔ |
|Polski | `en` |✔ |
|Fiński | `fi` |✔ |
|Francuski | `fr` |✔ |
|Niemiecki | `de` |✔ |
|Grecki | `el` |✔ |
|Węgierski | `hu` |✔ |
|Włoski | `it` |✔ |
|Japoński | `ja` |✔ |
|Koreański | `ko` |✔ |
|Norweski | `nb` |✔ |
|Polski | `pl` |✔ |
|portugalski | `pt` |✔ |
|rumuński | `ro` |✔ |
|Rosyjski | `ru` |✔ |
|Serbski (Cyrylica) | `sr-Cyrl` |✔ |
|Serbski (łaciński) | `sr-Latn` |✔ |
|Słowacki | `sk` |✔ |
|Hiszpański | `es` |✔ |
|Szwedzki | `sw` |✔ |
|Turecki | `tr` |✔ |

## <a name="image-analysis"></a>Analiza obrazu

Niektóre akcje interfejsu API [analizy obrazu](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) mogą zwracać wyniki w innych językach, określone za pomocą `language` parametru zapytania. Inne akcje zwracają wyniki w języku angielskim niezależnie od tego, jaki język jest określony, a inne zgłaszają wyjątek dla nieobsługiwanych języków. Akcje są określone za pomocą parametrów zapytania `visualFeatures` i `details`; Zapoznaj się z [omówieniem](home.md) , aby zapoznać się z listą wszystkich akcji, które można wykonać za pomocą analizy obrazu.

|Język | Kod języka | Kategorie | Tagi | Opis | Treści | Marki | Kolor | Twarze | ImageType | Obiekty | Celebryci | Charakterystycznych elementów krajobrazu |
|:---|:---:|:----:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
|Chiński | `zh`    | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|
|Polski | `en`   | ✔ | ✔| ✔|✔|✔|✔|✔|✔|✔|✔|✔|
|Japoński | `ja`   | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|
|portugalski | `pt` | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|
|Hiszpański | `es`    | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|

## <a name="next-steps"></a>Następne kroki

Zacznij korzystać z funkcji przetwarzanie obrazów wymienionych w tym przewodniku.

* [Analizowanie obrazu lokalnego (REST)](./quickstarts/csharp-analyze.md)
* [Wyodrębnij drukowany tekst (REST)](./quickstarts/csharp-print-text.md)