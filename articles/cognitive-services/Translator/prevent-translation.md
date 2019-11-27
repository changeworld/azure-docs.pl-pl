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
ms.date: 11/21/2019
ms.author: swmachan
ms.openlocfilehash: 15a36451c18d65df6667f24284f3f69f3d1c06b8
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/22/2019
ms.locfileid: "74326756"
---
# <a name="how-to-prevent-translation-of-content-with-the-translator-text-api"></a>Jak zapobiec translacji zawartości przy użyciu interfejs API tłumaczenia tekstu w usłudze Translator

Interfejs API tłumaczenia tekstu w usłudze Translator pozwala oznaczyć zawartość tak, aby nie została przetłumaczona. Na przykład możesz chcieć zakodować kod, nazwę marki lub wyraz/frazę, która nie ma sensu, gdy jest zlokalizowany.

## <a name="methods-for-preventing-translation"></a>Metody uniemożliwiające tłumaczenie
1. Przeciąganie w serwisie Twitter @somethingtopassthrough lub #somethingtopassthrough. Anulowanie anulowania po przetłumaczeniu. Jest to wyrażenie regularne dla prawidłowych tagów usługi Twitter: `\B@[A-Za-z]+[A-Za-z0-9_]+)`. Tag powinien rozpoczynać się od znaku "@", po którym następuje znak, po którym następuje jeden lub wiele znaków, cyfr lub podkreślenia. Zaleca się pozostawienie tagów Short, a otwierając tag musi być poprzedzony spacją.

2. Oznacz zawartość za pomocą `notranslate`. Jest to projekt, który działa tylko wtedy, gdy typ danych wejściowych jest ustawiony jako HTML

   Przykład:

   ```html
   <span class="notranslate">This will not be translated.</span>
   <span>This will be translated. </span>
   ```
   
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
