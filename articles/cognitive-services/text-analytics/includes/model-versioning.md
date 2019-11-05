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
ms.openlocfilehash: 5a06e26e5f1640024e343c714db3df134422115c
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73488630"
---
Wersja 3 interfejs API analizy tekstu pozwala wybrać model analiza tekstu używany na danych. Użyj opcjonalnego parametru `model-version`, aby wybrać wersję modelu w żądaniach. Jeśli ten parametr nie jest określony, interfejs API będzie domyślnie `latest`, Najnowsza stabilna wersja modelu.

Dostępne wersje modelu:
* `2019-10-01` (`latest`)

Każda odpowiedź z punktów końcowych v3 zawiera pole `model-version` określające używaną wersję modelu.

```json
{
    “documents”: […]
    “errors”: []
    “model-version”: “2019-10-01”
}
```
