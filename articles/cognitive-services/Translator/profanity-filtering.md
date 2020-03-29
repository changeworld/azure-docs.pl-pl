---
title: Filtrowanie wulgaryzmów — interfejs API tekstu tłumacza
titleSuffix: Azure Cognitive Services
description: Użyj filtrowania wulgaryzmów, aby określić poziom wulgaryzmów przetłumaczonych w tekście w interfejsie API tekstu usługi Azure Cognitive Services Translator.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: e7e2097e0d3daf360f1fa0f30bf3fd2c62c07163
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "73836228"
---
# <a name="add-profanity-filtering-with-the-translator-text-api"></a>Dodawanie filtrowania wulgaryzmów za pomocą interfejsu API tekstu tłumacza

Zwykle usługa Translator zachowuje wulgaryzmy, które są obecne w źródle w tłumaczeniu. Stopień wulgaryzmów i kontekst, który sprawia, że słowa wulgarne różnią się między kulturami. W rezultacie stopień wulgaryzmów w języku docelowym może zostać wzmocniony lub zmniejszony.

Jeśli chcesz uniknąć wulgaryzmów w tłumaczeniu, nawet jeśli w tekście źródłowym są wulgaryzmy, użyj opcji filtrowania wulgaryzmów dostępnej w metodzie Translate(). Ta opcja umożliwia wybranie, czy wulgaryzmy mają być usuwane, oznaczane odpowiednimi znacznikami, czy też nie podejmować żadnych działań.

Metoda Translate() przyjmuje parametr "options", który zawiera nowy element "ProfanityAction". Akceptowane wartości wulgaryzmówAkcja to "NoAction", "Marked" i "Deleted".

## <a name="accepted-values-of-profanityaction-and-examples"></a>Przyjęte wartości wulgaryzmówAkcja i przykłady
|WulgaryzmyWartość reakcji | Akcja | Przykład: Źródło - japoński | Przykład: Target - Angielski|
| :---|:---|:---|:---|
| NoAction ( NoAction ) | Domyślne. Tak samo jak nie ustawianie opcji. Wulgaryzmy przechodzi od źródła do celu. | 中田田田田田田田 | On jest kretynem. |
| Oznaczone | Wulgarne słowa są otoczone tagami XML wulgaryzmami \<> ... \</wulgaryzmy>. | 中田田田田田田田 | Jest wulgaryzmem \<>szarpnięcie\</wulgaryzmy>. |
| Usunięte | Wulgarne słowa są usuwane z wyjścia bez wymiany. | 中田田田田 | On jest. |

## <a name="next-steps"></a>Następne kroki
> [!div class="nextstepaction"]
> [Stosowanie filtrowania wulgaryzmów za pomocą wywołania interfejsu API translatora](reference/v3-0-translate.md)
