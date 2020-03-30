---
author: genlin
ms.service: virtual-network
ms.topic: include
ms.date: 11/09/2018
ms.author: genli
ms.openlocfilehash: 3df4108907a4e1e65a444faf1049163966b7accf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "67183153"
---
## <a name="scenario"></a>Scenariusz
Maszyna wirtualna z pojedynczą kartą sieciową jest tworzona i połączona z siecią wirtualną. Maszyna wirtualna wymaga trzech różnych *prywatnych* adresów IP i dwóch *publicznych* adresów IP. Adresy IP są przypisywane do następujących konfiguracji IP:

* **IPConfig-1:** Przypisuje *statyczny* prywatny adres IP i *statyczny* publiczny adres IP.
* **IPConfig-2:** Przypisuje *statyczny* prywatny adres IP i *statyczny* publiczny adres IP.
* **IPConfig-3:** Przypisuje *statyczny* prywatny adres IP i nie ma publicznego adresu IP.
  
    ![Wiele adresów IP](./media/virtual-network-multiple-ip-addresses-scenario/multiple-ipconfigs.png)

Konfiguracje adresów IP są skojarzone z kartą sieciową podczas tworzenia karty sieciowej, a karta sieciowa jest dołączona do maszyny Wirtualnej podczas tworzenia maszyny wirtualnej. Typy adresów IP używane w scenariuszu są do ilustracji. Można przypisać dowolny adres IP i typy przypisania, których potrzebujesz.

> [!NOTE]
> Chociaż kroki opisane w tym artykule przypisują wszystkie konfiguracje adresów IP do pojedynczej karty sieciowej, można również przypisać wiele konfiguracji adresów IP do dowolnej karty sieciowej na maszynie wirtualnej z wieloma kartami sieciowymi. Aby dowiedzieć się, jak utworzyć maszynę wirtualną z wieloma kartami sieciowym, przeczytaj artykuł [Tworzenie maszyny wirtualnej z wieloma kartami sieciowym.](../articles/virtual-machines/windows/multiple-nics.md)