---
title: Obsługa języków — przetwarzanie obrazów
titleSuffix: Azure Cognitive Services
description: Lista języków naturalnych obsługiwanych przez funkcje przetwarzania obrazów.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: article
ms.date: 04/17/2019
ms.author: pafarley
ms.openlocfilehash: 1a70d1b2ea504d0ccfba925810a2d19d0c7583cc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60759609"
---
# <a name="language-support-for-computer-vision"></a>Obsługa języka dla przetwarzania obrazów

Niektóre funkcje przetwarzania obrazów w obsłudze wielu języków; wszystkie funkcje, które nie są wymienione w tym miejscu obsługują tylko język angielski.

## <a name="text-recognition"></a>Rozpoznawanie tekstu

Przetwarzanie obrazów może rozpoznawać tekst w wielu językach. W szczególności [optyczne rozpoznawanie znaków](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc) interfejs API obsługuje różne języki, natomiast [odczytu](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/2afb498089f74080d7ef85eb) interfejsu API i [Rozpoznaj tekst](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/587f2c6a154055056008f200) interfejs API obsługuje tylko język angielski. Zobacz [rozpoznawanie tekstu drukowanego i pisma odręcznego](concept-recognizing-text.md) Aby uzyskać więcej informacji na temat tej funkcji i zalet każdego interfejsu API.

Optyczne rozpoznawanie znaków automatycznie wykrywa język wprowadzania materiału, dlatego nie ma potrzeby określania kod języka w wywołaniu interfejsu API. Jednak kod języka zawsze są zwracane jako wartość `"language"` węzła w odpowiedzi JSON.

|Język| Kod języka | INTERFEJS API OPTYCZNE ROZPOZNAWANIE ZNAKÓW |
|:-----|:----:|:-----:|
|Arabski | `ar`|✔ |
|Chiński (uproszczony) | `zh-Hans`|✔ |
|Chiński (tradycyjny) | `zh-Hant`|✔ |
|Czeski | `cs` |✔ |
|Duński | `da` |✔ |
|Holenderski | `nl` |✔ |
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
|Portugalski | `pt` |✔ |
|Rumuński | `ro` |✔ |
|Rosyjski | `ru` |✔ |
|Serbski (cyrylica) | `sr-Cyrl` |✔ |
|Serbski (łaciński) | `sr-Latn` |✔ |
|Słowacki | `sk` |✔ |
|Hiszpański | `es` |✔ |
|Szwedzki | `sw` |✔ |
|Turecki | `tr` |✔ |

## <a name="image-analysis"></a>Analiza obrazu

Niektóre akcje [analizowanie — obraz](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) interfejsu API może zwrócić wyniki w różnych językach, określony za pomocą `language` parametr zapytania. Inne akcje zwracają wyniki w języku angielskim niezależnie od tego, jaki język jest określony, a inne zgłoszenie wyjątku dla języków nieobsługiwanych. Akcje są określane za pomocą `visualFeatures` i `details` parametry zapytania; zobacz [Przegląd](home.md) listę wszystkich działań, które można zrobić za pomocą analizy obrazów.

|Język | Kod języka | Categories | Tagi | Opis | Dla dorosłych | Marki | Kolor | Twarze | Typ obrazu | Obiekty | Celebryci | Charakterystycznych elementów krajobrazu |
|:---|:---:|:----:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
|Chiński | `zh`    | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|
|Polski | `en`   | ✔ | ✔| ✔|✔|✔|✔|✔|✔|✔|✔|✔|
|Japoński | `ja`   | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|
|Portugalski | `pt` | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|
|Hiszpański | `es`    | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|

## <a name="next-steps"></a>Kolejne kroki

Rozpocznij pracę, korzystając z funkcji przetwarzania obrazów, wymienione w tym przewodniku.

* [Analizowanie lokalny obraz (REST)](./quickstarts/csharp-analyze.md)
* [Wyodrębnianie drukowanych tekstu (REST)](./quickstarts/csharp-print-text.md)