---
title: Zapobieganie tłumaczeniu zawartości — interfejs API tekstu tłumacza
titleSuffix: Azure Cognitive Services
description: Zapobiegaj tłumaczeniu zawartości za pomocą interfejsu API tekstu tłumacza. Interfejs API tekstu tłumacza umożliwia oznaczanie zawartości, aby nie była tłumaczona.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 03/20/2020
ms.author: swmachan
ms.openlocfilehash: c7be4a0ea1a9d24a8b262132632a0bbb63ae1b96
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80052483"
---
# <a name="how-to-prevent-translation-of-content-with-the-translator-text-api"></a>Jak zapobiec tłumaczeniu zawartości za pomocą interfejsu API tekstu tłumacza

Interfejs API tekstu tłumacza umożliwia oznaczanie zawartości, aby nie była tłumaczona. Na przykład możesz oznaczyć kod, nazwę marki lub słowo/frazę, która nie ma sensu podczas lokalizacji.

## <a name="methods-for-preventing-translation"></a>Metody zapobiegania tłumaczeniom

1. Oznaczanie zawartości `notranslate`za pomocą pliku . Zgodnie z projektem działa to tylko wtedy, gdy tekst wejściowyType jest ustawiony jako HTML

   Przykład:

   ```html
   <span class="notranslate">This will not be translated.</span>
   <span>This will be translated. </span>
   ```
   
   ```html
   <div class="notranslate">This will not be translated.</div>
   <div>This will be translated. </div>
   ```

2. Użyj [słownika dynamicznego,](dynamic-dictionary.md) aby przepisać określone tłumaczenie.

3. Nie przekazuj ciągu do interfejsu API tekstu tłumacza do tłumaczenia.

4. Tłumacz [niestandardowy: Użyj słownika w tłumaczu niestandardowym,](custom-translator/what-is-dictionary.md) aby przepisać tłumaczenie frazy ze 100% prawdopodobieństwem.


## <a name="next-steps"></a>Następne kroki
> [!div class="nextstepaction"]
> [Unikaj tłumaczenia w wywołaniu interfejsu API translatora](reference/v3-0-translate.md)
