---
title: Plik dyrektywy include
description: Plik dyrektywy include
author: HeidiSteen
ms.service: cognitive-search
ms.topic: include
ms.date: 05/06/2019
ms.author: heidist
ms.custom: include file
ms.openlocfilehash: ee430241173a6c19e2a32e176f28411631d9cb19
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80272689"
---
Można utworzyć wiele usług w ramach subskrypcji. Każdy z nich może być aprowiowany w określonej warstwie. Ogranicza cię tylko liczba usług dozwolonych w każdej warstwie. Na przykład można utworzyć maksymalnie 12 usług w warstwie Podstawowa i kolejne 12 usług w warstwie S1 w ramach tej samej subskrypcji. Aby uzyskać więcej informacji na temat warstw, zobacz [Wybieranie jednostki SKU lub warstwy dla usługi Azure Cognitive Search](../articles/search/search-sku-tier.md).

Maksymalne limity usług mogą zostać podniesione na życzenie. Jeśli potrzebujesz więcej usług w ramach tej samej subskrypcji, skontaktuj się z pomocą techniczną platformy Azure.

| Zasób            | Za darmo<sup>1</sup> | Podstawowa (Basic) | S1  | S2 | S3 | S3&nbsp;HD | L1 (właso. | L2 (l2) |
| ------------------- | ---- | ----- | --- | -- | -- | ----- | -- | -- |
| Maksymalna liczba usług    |1     | 16    | 16  | 8  | 6  | 6     | 6  | 6  |
| Maksymalna skala w jednostkach wyszukiwania (SU)<sup>2</sup> |Nie dotyczy |3 su |36 su |36 su |36 su |36 su |36 su |36 su |

<sup>1</sup> Bezpłatny opiera się na wspólnych, a nie dedykowanych zasobach. Skalowanie w górę nie jest obsługiwane w zasobach udostępnionych.

<sup>2</sup> Jednostki wyszukiwania to jednostki rozliczeniowe, przydzielone jako *replika* lub *partycja*. Potrzebne są zarówno zasoby do przechowywania, indeksowania i operacji kwerend. Aby dowiedzieć się więcej o obliczeniach jednostek SU, zobacz [Skalowanie poziomów zasobów dla obciążeń zapytań i indeksów](../articles/search/search-capacity-planning.md). 