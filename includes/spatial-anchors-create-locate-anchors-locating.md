---
ms.openlocfilehash: b5fec8bbc0db78454b080a411702014bd96f7db9
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "76887868"
---
## <a name="locate-a-cloud-spatial-anchor"></a>Lokalizowanie zakotwiczenia przestrzennego chmury

Możliwość zlokalizowania wcześniej zapisanej zakotwiczenia przestrzennego chmury jest jednym z głównych powodów korzystania z zakotwiczeń przestrzennych platformy Azure. Istnieje kilka różnych sposobów lokalizowania zakotwiczenia przestrzennego chmury. Możesz użyć jednej strategii na obserwatorze naraz.
- Znajdź zakotwiczenia według identyfikatora.
- Znajdź kotwice połączone z wcześniej umiejscowioną kotwicą. Możesz dowiedzieć się więcej o relacjach kotwicy [tutaj](/azure/spatial-anchors/concepts/anchor-relationships-way-finding/).
- Znajdź kotwicę za pomocą [kolokacji grubej](/azure/spatial-anchors/concepts/coarse-reloc/).

Jeśli lokalizujesz zakotwiczenia przestrzenne chmury według identyfikatora, należy przechowywać identyfikator zakotwiczenia przestrzennego chmury w usłudze zaplecza aplikacji i udostępnić go wszystkim urządzeniom, które mogą być poprawnie uwierzytelniane. Na przykład zobacz [Samouczek: Udostępnianie zakotwiczeń przestrzennych na różnych urządzeniach](/azure/spatial-anchors/tutorials/tutorial-share-anchors-across-devices/).

Tworzenie wystąpienia `AnchorLocateCriteria` obiektu, ustawianie identyfikatorów, których szukasz, i `CreateWatcher` wywoływanie metody w `AnchorLocateCriteria`sesji, podając program .