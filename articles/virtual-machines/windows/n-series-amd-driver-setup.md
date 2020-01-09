---
title: Konfiguracja sterowników procesora GPU AMD na platformie Azure dla systemu Windows
description: Jak skonfigurować sterowniki AMD GPU dla maszyn wirtualnych serii N z systemem Windows Server lub Windows na platformie Azure
services: virtual-machines-windows
author: vikancha
manager: jkabat
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 12/4/2019
ms.author: vikancha
ms.openlocfilehash: 6f0aa8a08b8a502edbd15d3ede157b78a13b8588
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75479360"
---
# <a name="install-amd-gpu-drivers-on-n-series-vms-running-windows"></a>Instalowanie sterowników AMD GPU na maszynach wirtualnych serii N z systemem Windows

Aby skorzystać z możliwości procesora GPU nowych maszyn wirtualnych z serii Azure NVv4 z systemem Windows, należy zainstalować sterowniki procesora GPU AMD. Rozszerzenie sterownika AMD będzie dostępne w najbliższych tygodniach. W tym artykule przedstawiono obsługiwane systemy operacyjne, sterowniki oraz czynności ręcznej instalacji i weryfikacji.

Aby zapoznać się z podstawowymi danymi, pojemnościami magazynu i szczegółami dysku, zobacz [rozmiary maszyn wirtualnych w systemie Windows GPU](sizes-gpu.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).



## <a name="supported-operating-systems-and-drivers"></a>Obsługiwane systemy operacyjne i sterowniki

| System operacyjny | Sterownik |
| -------- |------------- |
| Windows 10 EVD — kompilacja 1903 <br/><br/>Windows 10 — kompilacja 1809<br/><br/>Windows Server 2016<br/><br/>Windows Server 2019 | [19. q 4.1](https://download.microsoft.com/download/7/e/5/7e558ac0-3fff-413d-af62-800285a2fc53/Radeon-Pro-Software-for-Enterprise-19.Q4.1-Technical-Preview.exe) (. exe) |

## <a name="driver-installation"></a>Instalacja sterownika

1. Połącz się Pulpit zdalny z każdą maszyną wirtualną serii NVv4.

1. Pobierz, Wyodrębnij i zainstaluj obsługiwany sterownik dla systemu operacyjnego Windows.

## <a name="verify-driver-installation"></a>Weryfikuj instalację sterownika

Instalację sterownika można sprawdzić w Menedżer urządzeń. W poniższym przykładzie przedstawiono pomyślną konfigurację karty Instinct MI25 na maszynie wirtualnej Azure NVv4.
<br />
![właściwości sterowników procesora GPU](./media/n-series-amd-driver-setup/device-manager.png)

Możesz użyć programu dxdiag do zweryfikowania właściwości wyświetlania procesora GPU, w tym pamięci RAM wideo. Poniższy przykład przedstawia 1/8-krotną partycję karty Instinct Radeon MI25 na maszynie wirtualnej Azure NVv4.
<br />
![właściwości sterowników procesora GPU](./media/n-series-amd-driver-setup/dxdiag.png)
