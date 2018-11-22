---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 11/09/2018
ms.author: cynthn
ms.openlocfilehash: 7e390e2134df02b0ca9c0d1752c3207aff7b9314
ms.sourcegitcommit: 8d88a025090e5087b9d0ab390b1207977ef4ff7c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/21/2018
ms.locfileid: "52279820"
---
| Zasób | Limit domyślny | Limit maksymalny |
| --- | --- | --- |
| [Maszyny wirtualne](../articles/virtual-machines/virtual-machines-linux-about.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) dla usługi w chmurze<sup>1</sup> |50 |50 |
| Wejściowe punkty końcowe dla usługi w chmurze<sup>2</sup> |150 |150 |

<sup>1</sup>maszyn wirtualnych utworzonych w zarządzaniu usługami (zamiast usługi Resource Manager) automatycznie są przechowywane w usłudze w chmurze. Do tej usługi w chmurze dla równoważenia obciążenia i dostępność, możesz dodać więcej maszyn wirtualnych. 

<sup>2</sup>dane wejściowe punkty końcowe umożliwiają komunikacji z maszyną wirtualną spoza usługi w chmurze maszyny wirtualnej. Maszyny wirtualne w tej samej usługi w chmurze lub sieci wirtualnej mogą komunikować się automatycznie ze sobą. Zobacz [jak skonfigurować punkty końcowe do maszyny wirtualnej](../articles/virtual-machines/windows/classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json). 
