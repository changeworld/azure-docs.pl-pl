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
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2018
ms.locfileid: "38756235"
---
Możesz utworzyć wiele usług w ramach subskrypcji, każdy z nich zainicjowane w określonej warstwie ograniczony tylko ilością liczby jest dozwolona w poszczególnych warstwach usług. Na przykład można utworzyć maksymalnie 12 usług w warstwie podstawowa i innym 12 usług w warstwie S1, w ramach tej samej subskrypcji. Aby uzyskać więcej informacji na temat warstw, zobacz [wybierz jednostkę SKU lub warstwy usługi Azure Search](../articles/search/search-sku-tier.md).

Maksymalne limity można podnieść na żądanie. Jeśli potrzebujesz większej liczby usług w ramach tej samej subskrypcji, skontaktuj się z działem pomocy technicznej systemu Azure.

| Zasób            | Bezpłatne&nbsp;<sup>1</sup> | Podstawowa | S1  | S2 | S3 | S3&nbsp;HD |
| ------------------- | ---- | ----- | --- | -- | -- | ----- |
| Maksymalna usług    |1     | 12    | 12  | 6  | 6  | 6     |
| Maksymalne skalowanie SU&nbsp;<sup>2</sup> |ND |3 SU |36 SU |36 SU |36 SU |36 SU |

<sup>1</sup> bezpłatna opiera się na udostępniony, nie dedykowanych zasobów. Skalowanie w górę nie jest obsługiwane ze współużytkowanych zasobów.

<sup>2</sup> jednostek wyszukiwania (SU) rozliczenia są jednostki, przydzielone jako *repliki* lub *partycji*. Oba zasoby są potrzebne do magazynu, indeksowania i operacje zapytań. Aby dowiedzieć się więcej na temat obliczeń SU, zobacz [poziomy skalowania zasobów dla obciążeń zapytań i indeksu](../articles/search/search-capacity-planning.md). 