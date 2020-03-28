---
ms.openlocfilehash: 8ebb10f955be8f3004fdbdc595ea0fefc0d2b7ea
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "67182944"
---
## <a name="update-properties"></a>Aktualizuj właściwości

Aby zaktualizować właściwości zakotwiczenia, `UpdateAnchorProperties()` należy użyć tej metody. Jeśli dwa lub więcej urządzeń próbuje zaktualizować właściwości dla tej samej kotwicy w tym samym czasie, używamy modelu współbieżności optymistyczne. Co oznacza, że pierwszy zapis wygra.  Wszystkie inne zapisy otrzymają błąd "Współbieżność": odświeżenie właściwości będzie potrzebne przed ponowną próbą.
