---
title: Zapobiegaj zawartości translation - interfejsu API tłumaczenia tekstu
titlesuffix: Azure Cognitive Services
description: Zapobiegaj tłumaczenia zawartości przy użyciu interfejsu API tłumaczenia tekstu.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: 02c4e90879b81e29f3972d262f36d5c6b6d8e841
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2019
ms.locfileid: "67448287"
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
