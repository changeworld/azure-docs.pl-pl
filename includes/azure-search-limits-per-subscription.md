---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: search
author: HeidiSteen
ms.service: search
ms.topic: include
ms.date: 04/04/2018
ms.author: heidist
ms.custom: include file
ms.openlocfilehash: 8a4f794c8ef24a90498954629c131904621c5b43
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2018
---
Możesz utworzyć wiele usług w ramach subskrypcji, każdy z nich zainicjowane w określonej warstwy, ograniczona tylko liczby dozwolone w poszczególnych warstwach usług. Na przykład można utworzyć maksymalnie 12 usług w warstwie podstawowa i innym 12 usług w warstwie S1 w ramach tej samej subskrypcji. Aby uzyskać więcej informacji na temat warstw, zobacz [wybrać SKU lub warstwy usługi Azure Search](../articles/search/search-sku-tier.md).

Ograniczenia usługi maksymalną można uruchamiany na żądanie. Jeśli potrzebujesz większej liczby usług w ramach tej samej subskrypcji, skontaktuj się z pomocą techniczną platformy Azure.

| Zasób            | Bezpłatne&nbsp;<sup>1</sup> | Podstawowa | S1  | S2 | S3 | S3&nbsp;HD |
| ------------------- | ---- | ----- | --- | -- | -- | ----- |
| Maksymalna usług    |1     | 12    | 12  | 6  | 6  | 6     |
| Maksymalną skalę w SU&nbsp;<sup>2</sup> |ND |3 SU |36 SU |36 SU |36 SU |36 SU |

<sup>1</sup> wolne opiera się na udostępnionych zasobów nie dedykowanych. Skalowanie w pionie nie jest obsługiwana na udostępnionych zasobów.

<sup>2</sup> jednostek wyszukiwania (SU) są rozliczeń jednostki, przydzielone jako *repliki* lub *partycji*. Oba zasoby są wymagane dla magazynu, indeksowanie i operacje zapytań. Aby dowiedzieć się więcej na temat SU obliczenia, zobacz [skalować poziomy zasobów dla obciążeń zapytań i indeksu](../articles/search/search-capacity-planning.md). 