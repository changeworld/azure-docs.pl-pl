---
title: Zapobiegaj zawartości translation - interfejsu API tłumaczenia tekstu
titlesuffix: Azure Cognitive Services
description: Zapobiegaj tłumaczenia zawartości przy użyciu interfejsu API tłumaczenia tekstu.
services: cognitive-services
author: Jann-Skotdal
manager: cgronlun
ms.service: cognitive-services
ms.component: translator-text
ms.topic: conceptual
ms.date: 11/20/2018
ms.author: v-jansko
ms.openlocfilehash: 70bc4a50240abd8e4b67ff572f8c472f7519569a
ms.sourcegitcommit: b767a6a118bca386ac6de93ea38f1cc457bb3e4e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/18/2018
ms.locfileid: "53556853"
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

5. Niestandardowe w usłudze Translator: Użyj [słownika niestandardowego w usłudze Translator](custom-translator/what-is-dictionary.md) programowi tłumaczenie frazę z prawdopodobieństwem 100%.


## <a name="next-steps"></a>Kolejne kroki
> [!div class="nextstepaction"]
> [Należy unikać tłumaczenia w wywołania interfejsu API usługi Translator](reference/v3-0-translate.md)
