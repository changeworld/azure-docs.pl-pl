---
title: Obsługa języka - Computer Vision
titleSuffix: Azure Cognitive Services
description: Ten artykuł zawiera listę języków naturalnych obsługiwanych przez funkcje przetwarzania. OCR, Rozpoznawanie tekstu i Odczyt.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: pafarley
ms.openlocfilehash: a834c68119340d796f87971912a07fc0524a6d21
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79220139"
---
# <a name="language-support-for-computer-vision"></a>Obsługa języka dla computer vision

Niektóre funkcje funkcji przetwarzania obrazów obsługują wiele języków; wszelkie funkcje nie wymienione tutaj tylko wsparcie języka angielskiego.

## <a name="text-recognition"></a>Rozpoznawanie tekstu

Usługa Computer Vision może rozpoznawać tekst w wielu językach. W szczególności interfejs API [OCR](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc) obsługuje różne języki, podczas gdy interfejs API [odczytu](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/2afb498089f74080d7ef85eb) i [rozpoznawania tekstu](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/587f2c6a154055056008f200) obsługuje tylko język angielski. Zobacz [Rozpoznawanie drukowanego i odręcznego tekstu, aby](concept-recognizing-text.md) uzyskać więcej informacji na temat tej funkcji i zalet każdego interfejsu API.

Funkcja OCR automatycznie wykrywa język materiału wejściowego, więc nie ma potrzeby określania kodu języka w wywołaniu interfejsu API. Jednak kody języków są zawsze zwracane jako wartość węzła `"language"` w odpowiedzi JSON.

|Język| Kod języka | OCR API |
|:-----|:----:|:-----:|
|Arabski | `ar`|✔ |
|Chiński uproszczony | `zh-Hans`|✔ |
|Chiński (tradycyjny) | `zh-Hant`|✔ |
|Czeski | `cs` |✔ |
|duński | `da` |✔ |
|Niderlandzki | `nl` |✔ |
|Polski | `en` |✔ |
|fiński | `fi` |✔ |
|Francuski | `fr` |✔ |
|Niemiecki | `de` |✔ |
|grecki | `el` |✔ |
|węgierski | `hu` |✔ |
|Włoski | `it` |✔ |
|Japoński | `ja` |✔ |
|Koreański | `ko` |✔ |
|Norweski | `nb` |✔ |
|Polski | `pl` |✔ |
|Portugalski | `pt` |✔ |
|Rumuński | `ro` |✔ |
|Rosyjski | `ru` |✔ |
|Serbski (cyrylica) | `sr-Cyrl` |✔ |
|Serbski (łaciński) | `sr-Latn` |✔ |
|Słowacki | `sk` |✔ |
|Hiszpański | `es` |✔ |
|szwedzki | `sw` |✔ |
|Turecki | `tr` |✔ |

## <a name="image-analysis"></a>Analiza obrazu

Niektóre akcje [interfejsu](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) API analizy — obrazu mogą zwracać `language` wyniki w innych językach, określone za pomocą parametru zapytania. Inne akcje zwracają wyniki w języku angielskim, niezależnie od tego, jaki język jest określony, a inne zgłaszają wyjątek dla nieobsługiwałych języków. Akcje są określone `visualFeatures` `details` z parametrami i zapytania; zobacz [Omówienie](home.md) listy wszystkich czynności, które można wykonać z analizą obrazu.

|Język | Kod języka | Kategorie | Tagi | Opis | Dorosły | Marki | Kolor | Twarze | Imagetype | Obiekty | Celebryci | Charakterystycznych obiektów |
|:---|:---:|:----:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
|Chiński | `zh`    | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|
|Polski | `en`   | ✔ | ✔| ✔|✔|✔|✔|✔|✔|✔|✔|✔|
|Japoński | `ja`   | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|
|Portugalski | `pt` | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|
|Hiszpański | `es`    | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|

## <a name="next-steps"></a>Następne kroki

Rozpocznij korzystanie z funkcji przetwarzania obrazów komputerowych wymienionych w tym przewodniku.

* [Analizowanie obrazu lokalnego (REST)](./quickstarts/csharp-analyze.md)
* [Wyodrębnianie drukowanego tekstu (REST)](./quickstarts/csharp-print-text.md)