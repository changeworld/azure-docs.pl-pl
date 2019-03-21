---
ms.openlocfilehash: eafe71902ee849c310847f4537214722e129ad42
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/18/2019
ms.locfileid: "57907677"
---
## <a name="update-properties"></a>Właściwości aktualizacji

Aby zaktualizować właściwości elementu zakotwiczenia, należy użyć metody UpdateAnchorProperties. Jeśli dwie lub większą liczbę urządzeń do aktualizacji właściwości dla tego samego kotwicy w tym samym czasie, użyjemy modelu optymistycznej współbieżności. Oznacza to, że wygra pierwszej operacji zapisu.  Wszystkie inne operacje zapisu, zostanie wyświetlony błąd "Współbieżności": odświeżanie właściwości będzie potrzebna przed podjęciem ponownej próby.
