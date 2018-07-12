---
title: Tworzenie maszyny Wirtualnej w witrynie Azure portal | Dokumentacja firmy Microsoft
description: Utwórz maszynę wirtualną Windows w witrynie Azure portal.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-service-management
ROBOTS: NOINDEX
ms.assetid: 1871f823-ebd7-4eff-9a22-8e2411555595
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 05/30/2017
ms.author: cynthn
ms.openlocfilehash: 5fd2128ff436d3211f41c7dfdcc4c2b8aabd0eb0
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2018
ms.locfileid: "38232330"
---
# <a name="create-a-virtual-machine-running-windows-in-the-azure-portal"></a>Tworzenie maszyny wirtualnej z systemem Windows w witrynie Azure portal
> [!div class="op_single_selector"]
> * [Azure Portal](tutorial.md)
> * [Programu PowerShell: Wdrożenia klasycznego](create-powershell.md)
>
>

<br>

> [!IMPORTANT]
> Platforma Azure ma dwa różne modele wdrażania do tworzenia i pracy z zasobami: [usługi Resource Manager i Model Klasyczny](../../../resource-manager-deployment-model.md). Ten artykuł dotyczy klasycznego modelu wdrażania. Firma Microsoft zaleca, aby w przypadku większości nowych wdrożeń korzystać z modelu opartego na programie Resource Manager. Dowiedz się, jak [wykonania tych kroków przy użyciu modelu wdrażania usługi Resource Manager](../../virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) przy użyciu **witryny Azure portal**.
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]

W tym samouczku przedstawiono sposób tworzenia maszyn wirtualnych (VM) systemem Windows w witrynie Azure portal. Jako przykład użyto obrazu systemu Windows Server, ale jest to tylko jeden z wielu obrazów oferowanych na platformie Azure. Należy pamiętać, że obrazy dostępne do wyboru zależą od subskrypcji. Przykładowo obrazy komputerowe Windows może być dostępna dla subskrybentów MSDN.

W tej sekcji dowiesz się, jak używać **pulpit nawigacyjny** w witrynie Azure portal, aby wybrać, a następnie utwórz maszynę wirtualną.

Można również utworzyć maszyn wirtualnych przy użyciu [własnych obrazów](createupload-vhd.md). Aby dowiedzieć się więcej na temat tego i innych metod, zobacz [różne sposoby tworzenia maszyny wirtualnej Windows](../../virtual-machines-windows-creation-choices.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a id="createvirtualmachine"> </a>Tworzenie maszyny wirtualnej
[!INCLUDE [virtual-machines-create-WindowsVM](../../../../includes/virtual-machines-create-windowsvm.md)]

## <a name="next-steps"></a>Kolejne kroki
* Dowiedz się, jak [Utwórz Maszynę wirtualną przy użyciu modelu wdrażania usługi Resource Manager](../../virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) w witrynie Azure portal.
* Zaloguj się do maszyny wirtualnej. Aby uzyskać instrukcje, zobacz [Zaloguj się do maszyny wirtualnej z systemem Windows Server](connect-logon.md).
* Dołączanie dysku do przechowywania danych. Możesz dołączyć pustych dysków i dyski, które zawierają dane. Aby uzyskać instrukcje, zobacz [dołączanie dysku danych do maszyny wirtualnej Windows utworzonych za pomocą klasycznego modelu wdrażania](attach-disk.md).
