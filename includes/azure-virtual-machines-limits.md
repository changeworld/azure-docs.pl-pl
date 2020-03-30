---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 11/09/2018
ms.author: cynthn
ms.openlocfilehash: 2cfd376f595ae70daf9ab468d464dd9c8ff13d74
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335013"
---
| Zasób | Limit |
| --- | --- |
| [Maszyny wirtualne](../articles/virtual-machines/virtual-machines-linux-about.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) na usługę w<sup>chmurze 1</sup> |50 |
| Wejściowe punkty końcowe na usługę w<sup>chmurze 2</sup> |150 |

<sup>1.</sup> Maszyny wirtualne utworzone przy użyciu klasycznego modelu wdrażania zamiast usługi Azure Resource Manager są automatycznie przechowywane w usłudze w chmurze. Można dodać więcej maszyn wirtualnych do tej usługi w chmurze do równoważenia obciążenia i dostępności. 

<sup>2.</sup> Wejściowe punkty końcowe umożliwiają komunikację z maszyną wirtualną spoza usługi w chmurze maszyny wirtualnej. Maszyny wirtualne w tej samej usłudze w chmurze lub sieci wirtualnej mogą automatycznie komunikować się ze sobą. Aby uzyskać więcej informacji, zobacz [Jak skonfigurować punkty końcowe na maszynie wirtualnej](../articles/virtual-machines/windows/classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json). 
