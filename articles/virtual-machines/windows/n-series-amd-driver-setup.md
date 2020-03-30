---
title: Konfiguracja sterownika procesora graficznego AMD z serii N dla systemu Windows
description: Jak skonfigurować sterowniki procesorów graficznych AMD dla maszyn wirtualnych z serii N z systemem Windows Server lub Windows na platformie Azure
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
ms.openlocfilehash: 164f07f6545c1c225814958bba5722536b11a9b4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78269440"
---
# <a name="install-amd-gpu-drivers-on-n-series-vms-running-windows"></a>Instalowanie sterowników procesorów graficznych AMD na maszynach wirtualnych z systemem Windows z systemem Windows

Aby korzystać z możliwości procesora GPU nowych maszyn wirtualnych z serii Azure NVv4 z systemem Windows, należy zainstalować sterowniki PROCESORA GRAFICZNEGO AMD. Rozszerzenie sterownika AMD będzie dostępne w najbliższych tygodniach. Ten artykuł zawiera obsługiwane systemy operacyjne, sterowniki oraz instrukcje instalacji i weryfikacji.

Aby uzyskać podstawowe specyfikacje, pojemność pamięci masowej i szczegóły dysku, zobacz [Rozmiary maszyn wirtualnych systemu GPU systemu Windows](sizes-gpu.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).



## <a name="supported-operating-systems-and-drivers"></a>Obsługiwane systemy operacyjne i sterowniki

| System operacyjny | Sterownik |
| -------- |------------- |
| Windows 10 EVD - Kompilacja 1903 <br/><br/>Windows 10 - Kompilacja 1809<br/><br/>Windows Server 2016<br/><br/>Windows Server 2019 | [20.Q1.1](https://download.microsoft.com/download/3/8/9/3893407b-e8aa-4079-8592-735d7dd1c19a/Radeon-Pro-Software-for-Enterprise-GA.exe) (.exe) |


## <a name="driver-installation"></a>Instalacja sterownika

1. Połącz się za pomocą pulpitu zdalnego z każdą maszyną wirtualną z serii NVv4.

2. Jeśli jesteś klientem w wersji zapoznawczej NVv4, zatrzymaj maszynę wirtualną i poczekaj, aż przejdzie do stanu zatrzymanego(cofniętego).

3. Uruchom maszynę wirtualną, a następnie odinstaluj sterownik podglądu, uruchamiając "amdcleanuputility-x64.exe" znajdującego się w folderze "...\AMDCleanUninstallUtility". Dokładna ścieżka będzie się różnić w zależności od tego, gdzie znajdują się poprzednie pliki instalacyjne sterownika.  

4. Pobierz i zainstaluj najnowszy sterownik.

5. Uruchom ponownie maszynę wirtualną.

## <a name="verify-driver-installation"></a>Weryfikowanie instalacji sterownika

Instalację sterownika można zweryfikować w Menedżerze urządzeń. Poniższy przykład przedstawia pomyślną konfigurację karty Radeon Instinct MI25 na maszynie Wirtualnej NVv4 platformy Azure.
<br />
![Właściwości sterownika GPU](./media/n-series-amd-driver-setup/device-manager.png)

Za pomocą dxdiag można zweryfikować właściwości wyświetlania gpu, w tym pamięć RAM wideo. W poniższym przykładzie przedstawiono partycję 1/8 karty Radeon Instinct MI25 na maszynie wirtualnej Azure NVv4.
<br />
![Właściwości sterownika GPU](./media/n-series-amd-driver-setup/dxdiag.png)
