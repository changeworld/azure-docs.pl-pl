---
title: Zapobiegaj zawartości translation - interfejsu API tłumaczenia tekstu
titlesuffix: Azure Cognitive Services
description: Zapobiegaj tłumaczenia zawartości przy użyciu interfejsu API tłumaczenia tekstu.
services: cognitive-services
author: v-pawal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 02/21/2019
ms.author: v-jansko
ms.openlocfilehash: a9590a9a38859818e0b609d64fc12e30afd2e09e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60507015"
---
# <a name="how-to-prevent-translation-of-content-with-the-translator-text-api"></a>Jak uniemożliwić tłumaczenia zawartości przy użyciu interfejsu API tłumaczenia tekstu

Interfejs API tekstu usługi Translator umożliwia zawartość tagu tak, aby nie jest on translacji. Na przykład można oznaczyć kodu, Nazwa marki lub word/frazę, która nie ma sensu w przypadku zlokalizowanych.

## <a name="methods-for-preventing-translation"></a>Metody w celu zapobiegania tłumaczenia
1. Escape, aby tag usługi Twitter @somethingtopassthrough lub #somethingtopassthrough. UN ucieczki po translacji.

2. Oznaczanie zawartości przy użyciu `notranslate`.

   Przykład:

   ```html
   <div class="notranslate">This will not be translated.</div>
   <div>This will be translated. </div>
   ```

3. Użyj [dynamiczny słownik](dynamic-dictionary.md) programowi określonych tłumaczenia.

4. Nie można przekazać ciągu do interfejsu API tekstu usługi Translator do tłumaczenia.

5. Custom Translator: Użyj [słownika niestandardowego w usłudze Translator](custom-translator/what-is-dictionary.md) programowi tłumaczenie frazę z prawdopodobieństwem 100%.


## <a name="next-steps"></a>Kolejne kroki
> [!div class="nextstepaction"]
> [Należy unikać tłumaczenia w wywołania interfejsu API usługi Translator](reference/v3-0-translate.md)
