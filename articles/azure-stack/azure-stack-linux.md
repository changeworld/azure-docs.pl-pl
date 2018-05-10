---
title: Dodawanie obrazów systemu Linux Azure stosu
description: Dowiedz się, jak dodać obrazy Linux Azure stosu.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2018
ms.author: brenduns
ms.reviewer: jeffgo
ms.openlocfilehash: 64a860bc925b9c7499363c1fe39d03df88a9a51d
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/10/2018
---
# <a name="add-linux-images-to-azure-stack"></a>Dodawanie obrazów systemu Linux Azure stosu

*Dotyczy: Azure stosu zintegrowanych systemów i Azure stosu Development Kit*

Można wdrożyć maszyn wirtualnych systemu Linux (VM) na stosie Azure przez dodanie do obrazu opartych na systemie Linux w portalu Azure Marketplace stosu. Najprostszym sposobem, aby dodać obraz systemu Linux Azure stos jest za pośrednictwem zarządzania Marketplace. Te obrazy zostały przygotowane i sprawdzane pod kątem zgodności z stosu Azure.

## <a name="marketplace-management"></a>Zarządzanie Marketplace

Aby pobrać Linux obrazów z portalu Azure Marketplace, procedury przedstawione w artykule. Wybierz obrazy systemu Linux, które chcesz zapewnić użytkownikom na stosie Azure. 

[Pobieranie elementów marketplace z platformy Azure do stosu Azure](azure-stack-download-azure-marketplace-item.md).

Należy pamiętać, że istnieją aktualizacje częste do tych obrazów, więc zarządzania Marketplace często w celu zapewnienia aktualności.

## <a name="prepare-your-own-image"></a>Przygotowanie własnego obrazu

 Tam gdzie to możliwe, należy pobrać obrazy dostępne za pośrednictwem portalu Marketplace zarządzania, które zostały przygotowane i sprawdzane pod kątem stosu Azure. 
 
 Agenta systemu Linux platformy Azure (zwykle nazywane `WALinuxAgent` lub `walinuxagent`) jest wymagane, a nie wszystkie wersje agenta będą działać na stosie Azure. Należy używać wersji 2.2.18 lub nowszym w przypadku tworzenia własnego obrazu. Należy pamiętać, że [init chmury](https://cloud-init.io/) na stosie Azure nie jest obsługiwana w tej chwili.

 Można przygotować własny obraz systemu Linux przy użyciu poniższych instrukcji:

   * [Na podstawie centOS dystrybucji](../virtual-machines/linux/create-upload-centos.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
   * [Debian systemu Linux](../virtual-machines/linux/debian-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
   * [Red Hat Enterprise Linux](azure-stack-redhat-create-upload-vhd.md)
   * [SLES & openSUSE](../virtual-machines/linux/suse-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
   * [Serwer Ubuntu](../virtual-machines/linux/create-upload-ubuntu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

    
## <a name="add-your-image-to-the-marketplace"></a>Dodawanie obrazu do witryny marketplace
 
Postępuj zgodnie z [dodać obraz do witryny Marketplace](azure-stack-add-vm-image.md). Upewnij się, że `OSType` ustawiono parametr `Linux`.

Po dodaniu go do portalu Marketplace, zostanie utworzony element Marketplace, a użytkownicy będą mogli wdrażać maszyny wirtualnej systemu Linux.
