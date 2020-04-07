---
title: Limity
description: Ograniczenia kodu dla funkcji SDK
author: erscorms
ms.author: erscor
ms.date: 02/11/2020
ms.topic: reference
ms.openlocfilehash: 6e23c716a965cdf178c9f847900e66c141d5d7eb
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80680338"
---
# <a name="limits"></a>Limity

Wiele funkcji ma ograniczenia rozmiaru lub liczenia ze względu na wewnętrzne szczegóły uruchomionego systemu.

## <a name="azure-frontend"></a>Azure Frontend

* Całkowita azurefrontend wystąpień na proces: 16.
* Całkowita usługa AzureSession wystąpień na AzureFrontend: 16.

## <a name="objects"></a>Obiekty

* Całkowita dopuszczalna liczba obiektów pojedynczego typu (Entity, CutPlaneComponent itp.): 16 777 215.
* Całkowita dopuszczalna aktywna płaszczyzna cięcia: 8.

## <a name="materials"></a>Materiały

* Łączna wartość dopuszczalnych materiałów w aktach trwałych: 65 535.

## <a name="overall-number-of-polygons"></a>Ogólna liczba wielokątów

Dopuszczalna liczba wielokątów dla wszystkich załadowanych modeli zależy od rozmiaru maszyny Wirtualnej przekazywanej do [interfejsu API REST zarządzania sesją:](../how-tos/session-rest-api.md#create-a-session)

| Rozmiar maszyny wirtualnej | Maksymalna liczba wielokątów |
|:--------|:------------------|
|Standardowych| 20 milionów |
|Premium| bez limitu |



