---
title: Zapobiegaj translacji zawartości — interfejs API tłumaczenia tekstu w usłudze Translator
titleSuffix: Azure Cognitive Services
description: Zapobiegaj translacji zawartości przy użyciu interfejs API tłumaczenia tekstu w usłudze Translator. Interfejs API tłumaczenia tekstu w usłudze Translator pozwala oznaczyć zawartość tak, aby nie została przetłumaczona.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: f3bf784898f7f51beea890d8d2a8401af1403fbc
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/09/2019
ms.locfileid: "73888114"
---
# <a name="how-to-prevent-translation-of-content-with-the-translator-text-api"></a>Jak zapobiec translacji zawartości przy użyciu interfejs API tłumaczenia tekstu w usłudze Translator

Interfejs API tłumaczenia tekstu w usłudze Translator pozwala oznaczyć zawartość tak, aby nie została przetłumaczona. Na przykład możesz chcieć zakodować kod, nazwę marki lub wyraz/frazę, która nie ma sensu, gdy jest zlokalizowany.

## <a name="methods-for-preventing-translation"></a>Metody uniemożliwiające tłumaczenie
1. Przeciąganie w serwisie Twitter @somethingtopassthrough lub #somethingtopassthrough. Anulowanie anulowania po przetłumaczeniu.

2. Oznacz zawartość za pomocą `notranslate`.

   Przykład:

   ```html
   <div class="notranslate">This will not be translated.</div>
   <div>This will be translated. </div>
   ```

3. Użyj [słownika dynamicznego](dynamic-dictionary.md) , aby określić tłumaczenie.

4. Nie przekazuj ciągu do interfejs API tłumaczenia tekstu w usłudze Translator na potrzeby tłumaczenia.

5. Translator niestandardowy: Użyj [słownika w translatoru niestandardowym](custom-translator/what-is-dictionary.md) , aby określić tłumaczenie frazy z prawdopodobieństwem 100%.


## <a name="next-steps"></a>Następne kroki
> [!div class="nextstepaction"]
> [Unikaj tłumaczenia w wywołaniu interfejsu API usługi translator](reference/v3-0-translate.md)
