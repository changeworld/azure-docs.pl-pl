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
ms.sourcegitcommit: 23389df08a9f4cab1f3bb0f474c0e5ba31923f12
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/10/2019
ms.locfileid: "67183610"
---
Można utworzyć wiele usług w ramach subskrypcji. Każdy z nich może być inicjowany w określonej warstwie. Ograniczenie jest ograniczone tylko do liczby usług dozwolonych w każdej warstwie. Można na przykład utworzyć maksymalnie 12 usług w warstwie Podstawowa i inne 12 usług w warstwie S1 w ramach tej samej subskrypcji. Aby uzyskać więcej informacji o warstwach, zobacz [Wybieranie jednostki SKU lub warstwy dla Azure Search](../articles/search/search-sku-tier.md).

Na żądanie można zwiększyć maksymalną liczbę limitów usług. Jeśli potrzebujesz więcej usług w ramach tej samej subskrypcji, skontaktuj się z pomocą techniczną platformy Azure.

| Resource            | Bezpłatnie<sup>1</sup> | Podstawowa | S1  | S2 | S3 | S3&nbsp;HD | L1 | PAMIĘCI PODRĘCZNEJ L2 |
| ------------------- | ---- | ----- | --- | -- | -- | ----- | -- | -- |
| Maksymalna liczba usług    |1     | 16    | 16  | 8  | 6  | 6     | 6  | 6  |
| Maksymalna skala w jednostkach wyszukiwania (SU)<sup>2</sup> |ND |3 SU |36 SU |36 SU |36 SU |36 SU |36 SU |36 SU |

<sup>1</sup> bezpłatna jest oparta na udostępnionych, niededykowanych zasobach. Skalowanie w górę nie jest obsługiwane w zasobach udostępnionych.

<sup>2</sup> jednostki wyszukiwania to jednostki rozliczeń, które są przydzielone jako replika lub *partycja*. Potrzebne są oba zasoby dotyczące magazynu, indeksowania i operacji zapytań. Aby dowiedzieć się więcej na temat obliczeń SU, zobacz [skalowanie poziomów zasobów dla obciążeń zapytań i indeksów](../articles/search/search-capacity-planning.md). 