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
ms.date: 11/18/2019
ms.author: swmachan
ms.openlocfilehash: dd3684cbd7c03851bfcc75293a9690f77b4652b2
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/19/2019
ms.locfileid: "74184819"
---
# <a name="how-to-prevent-translation-of-content-with-the-translator-text-api"></a>Jak zapobiec translacji zawartości przy użyciu interfejs API tłumaczenia tekstu w usłudze Translator

Interfejs API tłumaczenia tekstu w usłudze Translator pozwala oznaczyć zawartość tak, aby nie została przetłumaczona. Na przykład możesz chcieć zakodować kod, nazwę marki lub wyraz/frazę, która nie ma sensu, gdy jest zlokalizowany.

## <a name="methods-for-preventing-translation"></a>Metody uniemożliwiające tłumaczenie
1. Przeciąganie w serwisie Twitter @somethingtopassthrough lub #somethingtopassthrough. Anulowanie anulowania po przetłumaczeniu. Jest to wyrażenie regularne dla prawidłowych tagów usługi Twitter: `\B@[A-Za-z]+[A-Za-z0-9_]+)`. Tag powinien rozpoczynać się od znaku "@", po którym następuje znak, po którym następuje jeden lub wiele znaków, cyfr lub podkreślenia. Zaleca się pozostawienie tagów Short, a otwierając tag musi być poprzedzony spacją.

2. Oznacz zawartość za pomocą `notranslate`.

   Przykład:

   ```html
   <div class="notranslate">This will not be translated.</div>
   <div>This will be translated. </div>
   ```

3. Użyj [słownika dynamicznego](dynamic-dictionary.md) , aby określić tłumaczenie.

4. Nie przekazuj ciągu do interfejs API tłumaczenia tekstu w usłudze Translator na potrzeby tłumaczenia.

5. Translator niestandardowy: Użyj [słownika w translatoru niestandardowym](custom-translator/what-is-dictionary.md) , aby określić tłumaczenie frazy z prawdopodobieństwem 100%.


## <a name="next-steps"></a>Kolejne kroki
> [!div class="nextstepaction"]
> [Unikaj tłumaczenia w wywołaniu interfejsu API usługi translator](reference/v3-0-translate.md)
