---
ms.openlocfilehash: c5ca85c0dfe8d601821a78c02b2230c0909c8003
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60232382"
---
Nie można zaktualizować lokalizacji elementu zakotwiczenia, gdy został on utworzony w usłudze — należy utworzyć nowy kontrolny i usuń starą śledzenie nowej pozycji.

Jeśli nie potrzebujesz zlokalizować elementu zakotwiczenia zaktualizować jego właściwości, możesz użyć `GetAnchorPropertiesAsync()` metody, która zwraca `CloudSpatialAnchor` obiekt z właściwościami.
