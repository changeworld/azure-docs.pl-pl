---
ms.openlocfilehash: 430302d8b1b2a01febbbe2f11057bb331ec80c28
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/18/2019
ms.locfileid: "57907811"
---
## <a name="locate-a-cloud-spatial-anchor"></a>Znajdź kotwica przestrzenne chmury

Aby zlokalizować przestrzenne kotwic chmury, musisz znać ich identyfikatorów. Identyfikatory zakotwiczeń może być przechowywane w usłudze zaplecza aplikacji i dostępne dla wszystkich urządzeń, które poprawnie mógł uwierzytelniać się go. Przykładem tego zobacz [samouczka: Udostępniaj przestrzenne zakotwiczenia między urządzeniami](/azure/spatial-anchors/tutorials/tutorial-share-anchors-across-devices/).

Utwórz wystąpienie obiektu AnchorLocateCriteria, Ustaw identyfikatory, czego szukasz i wywoływać metodę CreateWatcher sesji, zapewniając Twojej AnchorLocateCriteria.
