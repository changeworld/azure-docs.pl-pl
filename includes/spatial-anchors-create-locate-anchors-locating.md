---
ms.openlocfilehash: b5fec8bbc0db78454b080a411702014bd96f7db9
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2020
ms.locfileid: "76887868"
---
## <a name="locate-a-cloud-spatial-anchor"></a>Znajdź kotwicę przestrzenną w chmurze

Możliwość lokalizowania wcześniej zapisanego zakotwiczenia w chmurze jest jednym z głównych powodów używania kotwic przestrzennych platformy Azure. Istnieje kilka różnych sposobów lokalizowania zakotwiczenia przestrzennego w chmurze. W danym momencie możesz użyć jednej strategii.
- Lokalizowanie kotwic według identyfikatora.
- Zlokalizuj kotwice połączone z wcześniej zlokalizowaną kotwicą. W [tym miejscu](/azure/spatial-anchors/concepts/anchor-relationships-way-finding/)możesz poznać relacje kotwic.
- Znajdź kotwicę przy użyciu bardzo [dużej lokalizacji](/azure/spatial-anchors/concepts/coarse-reloc/).

W przypadku lokalizowania kotwic w chmurze według identyfikatora, należy zapisać identyfikator kotwicy w chmurze w usłudze zaplecza aplikacji i udostępnić go wszystkim urządzeniom, które mogą prawidłowo uwierzytelniać się w nim. Aby zapoznać się z przykładem, zobacz [Samouczek: udostępnianie kotwic przestrzenny między urządzeniami](/azure/spatial-anchors/tutorials/tutorial-share-anchors-across-devices/).

Utwórz wystąpienie obiektu `AnchorLocateCriteria`, Ustaw identyfikatory, których szukasz, i Wywołaj metodę `CreateWatcher` w sesji, dostarczając `AnchorLocateCriteria`.