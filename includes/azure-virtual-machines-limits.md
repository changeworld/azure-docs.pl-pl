---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 11/09/2018
ms.author: cynthn
ms.openlocfilehash: 083d59a2d8720a08abc7ea8998c1fbe048db3f28
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2019
ms.locfileid: "67183589"
---
| Resource | Limit domyślny | Limit maksymalny |
| --- | --- | --- |
| [Maszyny wirtualne](../articles/virtual-machines/virtual-machines-linux-about.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) dla usługi w chmurze<sup>1</sup> |50 |50 |
| Wejściowe punkty końcowe dla usługi w chmurze<sup>2</sup> |150 |150 |

<sup>1</sup>maszyn wirtualnych utworzonych przy użyciu klasycznego modelu wdrażania, a nie usługi Azure Resource Manager automatycznie są przechowywane w usłudze w chmurze. Do tej usługi w chmurze dla równoważenia obciążenia i dostępność, możesz dodać więcej maszyn wirtualnych. 

<sup>2</sup>dane wejściowe punkty końcowe umożliwiają komunikacji z maszyną wirtualną spoza usługi w chmurze maszyny wirtualnej. Maszyny wirtualne w tej samej usługi w chmurze lub sieci wirtualnej mogą komunikować się automatycznie ze sobą. Aby uzyskać więcej informacji, zobacz [jak konfigurować punkty końcowe do maszyny wirtualnej](../articles/virtual-machines/windows/classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json). 
