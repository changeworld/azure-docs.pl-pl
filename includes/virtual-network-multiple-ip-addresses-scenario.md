---
author: rockboyfor
ms.service: virtual-network
ms.topic: include
origin.date: 11/09/2018
ms.date: 01/21/2019
ms.author: v-yeche
ms.openlocfilehash: 3df4108907a4e1e65a444faf1049163966b7accf
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60742262"
---
## <a name="scenario"></a>Scenariusz
Maszynę Wirtualną z jedną kartą Sieciową jest utworzony i połączony z siecią wirtualną. Maszyna wirtualna wymaga trzech różnych *prywatnej* IP adresów i dwóch *publicznych* adresów IP. Adresy IP są przypisywane do poniższej konfiguracji adresów IP:

* **IPConfig-1:** Przypisuje *statyczne* prywatny adres IP i *statyczne* publicznego adresu IP.
* **IPConfig-2:** Przypisuje *statyczne* prywatny adres IP i *statyczne* publicznego adresu IP.
* **IPConfig-3:** Przypisuje *statyczne* prywatny adres IP i publicznego adresu IP.

    ![Wiele adresów IP](./media/virtual-network-multiple-ip-addresses-scenario/multiple-ipconfigs.png)

Konfiguracje adresów IP są skojarzone z kartą Sieciową, po utworzeniu karty Sieciowej i karty Sieciowej jest dołączony do maszyny Wirtualnej, po utworzeniu maszyny Wirtualnej. Typy adresów IP używanych w ramach scenariusza opisywanego są do celów informacyjnych. Możesz przypisać niezależnie od adresów IP adres i przypisywania typów potrzebujesz.

> [!NOTE]
> Jednak kroki opisane w tym artykule przypisuje wszystkie konfiguracje adresów IP do jednej karty Sieciowej, można także przypisać wiele konfiguracji adresów IP do dowolnej karty Sieciowej na maszynie Wirtualnej z wieloma kartami. Aby dowiedzieć się, jak utworzyć maszynę Wirtualną z wieloma kartami sieciowymi, przeczytaj [Utwórz Maszynę wirtualną z wieloma kartami sieciowymi](../articles/virtual-machines/windows/multiple-nics.md) artykułu.
