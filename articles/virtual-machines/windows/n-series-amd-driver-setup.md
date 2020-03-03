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
ms.openlocfilehash: 6dc8c54b9d138ab62e086cca59cd5b4801fa6130
ms.sourcegitcommit: 390cfe85629171241e9e81869c926fc6768940a4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/02/2020
ms.locfileid: "78228339"
---
# <a name="install-amd-gpu-drivers-on-n-series-vms-running-windows"></a>Instalowanie sterowników AMD GPU na maszynach wirtualnych serii N z systemem Windows

Aby skorzystać z możliwości procesora GPU nowych maszyn wirtualnych z serii Azure NVv4 z systemem Windows, należy zainstalować sterowniki procesora GPU AMD. Rozszerzenie sterownika AMD będzie dostępne w najbliższych tygodniach. W tym artykule przedstawiono obsługiwane systemy operacyjne, sterowniki oraz czynności ręcznej instalacji i weryfikacji.

Aby zapoznać się z podstawowymi danymi, pojemnościami magazynu i szczegółami dysku, zobacz [rozmiary maszyn wirtualnych w systemie Windows GPU](sizes-gpu.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).



## <a name="supported-operating-systems-and-drivers"></a>Obsługiwane systemy operacyjne i sterowniki

| System operacyjny | Sterownik |
| -------- |------------- |
| Windows 10 EVD — kompilacja 1903 <br/><br/>Windows 10 — kompilacja 1809<br/><br/>Windows Server 2016<br/><br/>Windows Server 2019 | [20. q 1.1](https://download.microsoft.com/download/3/8/9/3893407b-e8aa-4079-8592-735d7dd1c19a/Radeon-Pro-Software-for-Enterprise-GA.exe) (. exe) |


## <a name="driver-installation"></a>Instalacja sterownika

1. Połącz się Pulpit zdalny z każdą maszyną wirtualną serii NVv4.

2. Jeśli jesteś klientem usługi NVv4 w wersji zapoznawczej, Zatrzymaj maszynę wirtualną i zaczekaj, aż zostanie ona przeniesiona do stanu zatrzymana (cofnięta alokacja).

3. Uruchom maszynę wirtualną, a następnie Odinstaluj sterownik wersji zapoznawczej, uruchamiając polecenie "amdcleanuputility-x64. exe" znajdujące się w folderze ". ..\AMDCleanUninstallUtility". Dokładne ścieżki różnią się w zależności od tego, gdzie znajdują się pliki instalacyjne poprzednich sterowników.  

4. Pobierz i zainstaluj najnowszą wersję sterownika.

## <a name="verify-driver-installation"></a>Weryfikuj instalację sterownika

Instalację sterownika można sprawdzić w Menedżer urządzeń. W poniższym przykładzie przedstawiono pomyślną konfigurację karty Instinct MI25 na maszynie wirtualnej Azure NVv4.
<br />
![właściwości sterowników procesora GPU](./media/n-series-amd-driver-setup/device-manager.png)

Możesz użyć programu dxdiag do zweryfikowania właściwości wyświetlania procesora GPU, w tym pamięci RAM wideo. Poniższy przykład przedstawia 1/8-krotną partycję karty Instinct Radeon MI25 na maszynie wirtualnej Azure NVv4.
<br />
![właściwości sterowników procesora GPU](./media/n-series-amd-driver-setup/dxdiag.png)
