---
title: Przechowywanie wersji modelu
titleSuffix: Azure Cognitive Services
description: Określanie wersji modelu w punktach końcowych V3
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 02/06/2020
ms.author: aahi
ms.openlocfilehash: 4cd8d0901ce23fb227bb6919dee18f5aa3d063ed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77088978"
---
Wersja 3 interfejsu API analizy tekstu umożliwia wybranie wersji modelu, która jest najbardziej aktualna dla danych. Użyj parametru opcjonalnego, `model-version` aby wybrać wersję modelu, która jest wymagana dla twoich żądań. Jeśli ten parametr nie jest określony, `latest`domyślnie będzie to domyślnie interfejs API , najnowsza stabilna wersja. Mimo że można użyć najnowszej wersji modelu w dowolnym żądaniu, tylko niektóre funkcje są aktualizowane w każdej wersji. W poniższej tabeli opisano, które funkcje zostały zaktualizowane w każdej wersji modelu:

| Wersja modelu           | Funkcje zaktualizowane         | Najnowsza wersja dla:           |
|-------------------------|--------------------------|--------------------------|
| `2020-02-01`            | Rozpoznawanie jednostek                      | Rozpoznawanie jednostek                      |
| `2019-10-01`            | Rozpoznawanie encji, analiza tonacji  | Wykrywanie języka, wyodrębnianie fraz kluczowych, analiza tonacji|


Każda odpowiedź z punktów końcowych w `model-version` wersji 3 zawiera pole określające wersję modelu, która została użyta.

```json
{
    "documents": […]
    "errors": []
    "model-version": "2019-10-01"
}
```
Zobacz [Co nowego,](../whats-new.md) aby uzyskać szczegółowe informacje na temat aktualizacji dla tych wersji modelu.
