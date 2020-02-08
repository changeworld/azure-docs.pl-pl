---
title: Przechowywanie wersji modelu
titleSuffix: Azure Cognitive Services
description: Określ wersje modelu w punktach końcowych v3
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 02/06/2020
ms.author: aahi
ms.openlocfilehash: 4cd8d0901ce23fb227bb6919dee18f5aa3d063ed
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/08/2020
ms.locfileid: "77088978"
---
Wersja 3 interfejs API analizy tekstu umożliwia wybranie wersji modelu, która jest najbardziej aktualna dla danych. Użyj opcjonalnego parametru `model-version`, aby wybrać wersję modelu, która jest odpowiednia dla żądań. Jeśli ten parametr nie jest określony, interfejs API będzie domyślnie `latest`, Najnowsza stabilna wersja. Mimo że w każdym żądaniu można użyć najnowszej wersji modelu, w każdej wersji są aktualizowane tylko niektóre funkcje. W poniższej tabeli opisano, które funkcje zostały zaktualizowane w poszczególnych wersjach modelu:

| Wersja modelu           | Zaktualizowane funkcje         | Najnowsza wersja dla:           |
|-------------------------|--------------------------|--------------------------|
| `2020-02-01`            | Rozpoznawanie jednostek                      | Rozpoznawanie jednostek                      |
| `2019-10-01`            | Rozpoznawanie jednostek, analiza tonacji  | Wykrywanie języka, wyodrębnianie kluczowych fraz, analiza tonacji|


Każda odpowiedź z punktów końcowych v3 zawiera pole `model-version` określające używaną wersję modelu.

```json
{
    "documents": […]
    "errors": []
    "model-version": "2019-10-01"
}
```
Zobacz [co nowego](../whats-new.md) , aby uzyskać szczegółowe informacje o aktualizacjach dla tych wersji modelu.
