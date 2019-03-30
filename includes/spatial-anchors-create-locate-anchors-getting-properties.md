---
ms.openlocfilehash: c5ca85c0dfe8d601821a78c02b2230c0909c8003
ms.sourcegitcommit: 956749f17569a55bcafba95aef9abcbb345eb929
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2019
ms.locfileid: "58632307"
---
Nie można zaktualizować lokalizacji elementu zakotwiczenia, gdy został on utworzony w usłudze — należy utworzyć nowy kontrolny i usuń starą śledzenie nowej pozycji.

Jeśli nie potrzebujesz zlokalizować elementu zakotwiczenia zaktualizować jego właściwości, możesz użyć `GetAnchorPropertiesAsync()` metody, która zwraca `CloudSpatialAnchor` obiekt z właściwościami.
