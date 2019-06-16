---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: search
author: HeidiSteen
ms.service: search
ms.topic: include
ms.date: 05/06/2019
ms.author: heidist
ms.custom: include file
ms.openlocfilehash: 1e147e8bd9260cd1ece60b70641968a229995ec1
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66160520"
---
Możesz utworzyć wiele usług w ramach subskrypcji. Każdy z nich można zainicjowane w określonej warstwy. Ograniczeniem tylko liczby jest dozwolona w poszczególnych warstwach usług. Na przykład można utworzyć maksymalnie 12 usług w warstwie podstawowa i innym 12 usług w warstwie S1, w ramach tej samej subskrypcji. Aby uzyskać więcej informacji na temat warstw, zobacz [wybierz jednostkę SKU lub warstwy usługi Azure Search](../articles/search/search-sku-tier.md).

Maksymalne limity można podnieść na żądanie. Jeśli potrzebujesz większej liczby usług w ramach tej samej subskrypcji, skontaktuj się z działem pomocy technicznej systemu Azure.

| Resource            | Bezpłatne<sup>1</sup> | Podstawowa | S1  | S2 | S3 | S3&nbsp;HD | L1 | PAMIĘCI PODRĘCZNEJ L2 |
| ------------------- | ---- | ----- | --- | -- | -- | ----- | -- | -- |
| Maksymalna usług    |1     | 16    | 16  | 8  | 6  | 6     | 6  | 6  |
| Maksymalne skalowanie jednostek wyszukiwania (SU)<sup>2</sup> |ND |3 SU |36 SU |36 SU |36 SU |36 SU |36 SU |36 SU |

<sup>1</sup> bezpłatna opiera się na udostępniony, nie dedykowanych zasobów. Skalowanie w górę nie jest obsługiwane ze współużytkowanych zasobów.

<sup>2</sup> jednostek wyszukiwania rozliczenia są jednostki, przydzielone jako *repliki* lub *partycji*. Oba zasoby są potrzebne do magazynu, indeksowania i operacje zapytań. Aby dowiedzieć się więcej na temat obliczeń SU, zobacz [poziomy skalowania zasobów dla obciążeń zapytań i indeksu](../articles/search/search-capacity-planning.md). 