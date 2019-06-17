---
ms.openlocfilehash: 8ebb10f955be8f3004fdbdc595ea0fefc0d2b7ea
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66110771"
---
## <a name="update-properties"></a>Właściwości aktualizacji

Aby zaktualizować właściwości elementu zakotwiczenia, należy użyć `UpdateAnchorProperties()` metody. Jeśli dwie lub większą liczbę urządzeń do aktualizacji właściwości dla tego samego kotwicy w tym samym czasie, użyjemy modelu optymistycznej współbieżności. Oznacza to, że wygra pierwszej operacji zapisu.  Wszystkie inne operacje zapisu, zostanie wyświetlony błąd "Współbieżności": odświeżanie właściwości będzie potrzebna przed podjęciem ponownej próby.
