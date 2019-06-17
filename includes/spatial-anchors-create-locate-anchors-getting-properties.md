---
ms.openlocfilehash: c5ca85c0dfe8d601821a78c02b2230c0909c8003
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66110810"
---
Nie można zaktualizować lokalizacji elementu zakotwiczenia, gdy został on utworzony w usłudze — należy utworzyć nowy kontrolny i usuń starą śledzenie nowej pozycji.

Jeśli nie potrzebujesz zlokalizować elementu zakotwiczenia zaktualizować jego właściwości, możesz użyć `GetAnchorPropertiesAsync()` metody, która zwraca `CloudSpatialAnchor` obiekt z właściwościami.
