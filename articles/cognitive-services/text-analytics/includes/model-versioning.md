---
title: Przechowywanie wersji modelu
titleSuffix: Azure Cognitive Services
description: Określ wersje modelu w punktach końcowych v3
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 10/25/2019
ms.author: aahi
ms.openlocfilehash: 29850cb9cb40eae0829b5d8c2b58b5f9518f18d5
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/13/2019
ms.locfileid: "74021149"
---
Wersja 3 interfejs API analizy tekstu pozwala wybrać model analiza tekstu używany na danych. Użyj opcjonalnego parametru `model-version`, aby wybrać wersję modelu w żądaniach. Jeśli ten parametr nie jest określony, interfejs API będzie domyślnie `latest`, Najnowsza stabilna wersja modelu.

Dostępne wersje modelu:
* `2019-10-01` (`latest`)

Każda odpowiedź z punktów końcowych v3 zawiera pole `model-version` określające używaną wersję modelu.

```json
{
    "documents": […]
    "errors": []
    "model-version": "2019-10-01"
}
```
