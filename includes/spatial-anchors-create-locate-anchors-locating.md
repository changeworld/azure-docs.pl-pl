---
ms.openlocfilehash: ac9fe1d367dba0ebdf4250b3213f191ced758dd3
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2020
ms.locfileid: "76694587"
---
## <a name="locate-a-cloud-spatial-anchor"></a>Znajdź kotwicę przestrzenną w chmurze

Możliwość lokalizowania wcześniej zapisanego zakotwiczenia w chmurze jest jednym z głównych powodów używania kotwic przestrzennych platformy Azure. Istnieje kilka różnych sposobów lokalizowania zakotwiczenia przestrzennego w chmurze. W danym momencie możesz użyć jednej strategii.
- Lokalizowanie kotwic według identyfikatora.
- Zlokalizuj kotwice połączone z wcześniej zlokalizowaną kotwicą. W [tym miejscu](/azure/spatial-anchors/concepts/anchor-relationships-way-finding.md)możesz poznać relacje kotwic.
- Znajdź kotwicę przy użyciu bardzo [dużej lokalizacji](/azure/spatial-anchors/concepts/coarse-reloc.md).

W przypadku lokalizowania kotwic w chmurze według identyfikatora, należy zapisać identyfikator kotwicy w chmurze w usłudze zaplecza aplikacji i udostępnić go wszystkim urządzeniom, które mogą prawidłowo uwierzytelniać się w nim. Aby zapoznać się z przykładem, zobacz [Samouczek: udostępnianie kotwic przestrzenny między urządzeniami](/azure/spatial-anchors/tutorials/tutorial-share-anchors-across-devices/).

Utwórz wystąpienie obiektu `AnchorLocateCriteria`, Ustaw identyfikatory, których szukasz, i Wywołaj metodę `CreateWatcher` w sesji, dostarczając `AnchorLocateCriteria`.