---
title: Tworzenie maszyny Wirtualnej w portalu Azure | Dokumentacja firmy Microsoft
description: Utwórz maszynę wirtualną systemu Windows w portalu Azure.
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
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
---
# <a name="create-a-virtual-machine-running-windows-in-the-azure-portal"></a>Utwórz maszynę wirtualną z systemem Windows w portalu Azure
> [!div class="op_single_selector"]
> * [Azure Portal](tutorial.md)
> * [Środowiska PowerShell: Wdrożenia klasycznego](create-powershell.md)
>
>

<br>

> [!IMPORTANT]
> Platforma Azure ma dwa różne modele wdrażania do tworzenia i pracy z zasobami: [Resource Manager i Model Klasyczny](../../../resource-manager-deployment-model.md). W tym artykule omówiono przy użyciu klasycznego modelu wdrożenia. Firma Microsoft zaleca, aby w przypadku większości nowych wdrożeń korzystać z modelu opartego na programie Resource Manager. Dowiedz się, jak [wykonaj te czynności przy użyciu modelu wdrażania usługi Resource Manager](../../virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) przy użyciu **portalu Azure**.
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]

Ten samouczek przedstawia sposób tworzenia maszyny wirtualnej platformy Azure (VM) systemu Windows w portalu Azure. Na przykład użyjemy obrazu systemu Windows Server, ale jest to tylko jeden z wielu obrazów oferowanych na platformie Azure. Należy pamiętać, że obrazy dostępne do wyboru zależą subskrypcji. Przykładowo obrazy komputerowe systemu Windows mogą być dostępne dla subskrybentów MSDN.

W tej sekcji przedstawia sposób użycia **pulpitu nawigacyjnego** w portalu Azure wybierz, a następnie utworzyć maszyny wirtualnej.

Można również tworzyć maszyny wirtualne przy użyciu [własnych obrazów](createupload-vhd.md). Aby dowiedzieć się więcej na temat tego i innych metod, zobacz [różne sposoby tworzenia maszyny wirtualnej systemu Windows](../../virtual-machines-windows-creation-choices.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a id="createvirtualmachine"> </a>Utwórz maszynę wirtualną
[!INCLUDE [virtual-machines-create-WindowsVM](../../../../includes/virtual-machines-create-windowsvm.md)]

## <a name="next-steps"></a>Kolejne kroki
* Dowiedz się, jak [utworzyć Maszynę wirtualną przy użyciu modelu wdrażania usługi Resource Manager](../../virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) w portalu Azure.
* Zaloguj się do maszyny wirtualnej. Aby uzyskać instrukcje, zobacz [Zaloguj się do maszyny wirtualnej z systemem Windows Server](connect-logon.md).
* Dołączanie dysku do przechowywania danych. Możesz dołączyć zarówno puste dyski i dyski, które zawierają dane. Aby uzyskać instrukcje, zobacz [dołączenie dysku danych do maszyny wirtualnej systemu Windows utworzone z klasycznym modelu wdrażania](attach-disk.md).
