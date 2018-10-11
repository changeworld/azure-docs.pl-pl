---
title: Dodawanie obrazów systemu Linux w usłudze Azure Stack
description: Dowiedz się, jak dodać obrazy systemu Linux w usłudze Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2018
ms.author: sethm
ms.reviewer: jeffgo
ms.openlocfilehash: db52d145c3bfbd9415072be13ccb502969f07374
ms.sourcegitcommit: 4b1083fa9c78cd03633f11abb7a69fdbc740afd1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/10/2018
ms.locfileid: "49077651"
---
# <a name="add-linux-images-to-azure-stack"></a>Dodawanie obrazów systemu Linux w usłudze Azure Stack

*Dotyczy: Usługa Azure Stack zintegrowane systemy i usługi Azure Stack Development Kit*

Przez dodanie obrazów systemu Linux w witrynie Azure Marketplace stosu, można wdrożyć maszyny wirtualne systemu Linux (VM) w usłudze Azure Stack. Najprostszym sposobem dodania obrazu systemu Linux w usłudze Azure Stack jest za pośrednictwem witryny Marketplace zarządzania. Te obrazy zostały przetworzone i przetestowane pod kątem zgodności z usługą Azure Stack.

## <a name="marketplace-management"></a>Zarządzanie w portalu Marketplace

Aby pobrać obrazy systemu Linux w portalu Azure Marketplace, korzystając z procedur w następującym artykułem. Wybierz obrazy systemu Linux, którzy chcą oferować użytkowników usługi Azure Stack. 

[Pobieranie elementów portalu marketplace z platformy Azure do usługi Azure Stack](azure-stack-download-azure-marketplace-item.md).

Należy pamiętać, że częste aktualizacje do tych obrazów, dlatego należy sprawdzić zarządzania portalu Marketplace, często w celu zapewnienia aktualności.

## <a name="prepare-your-own-image"></a>Przygotuj swój własny obraz

 Wszędzie tam, gdzie to możliwe, należy pobrać obrazów dostępnych za pośrednictwem witryny Marketplace zarządzania, która została przygotowana i sprawdzane pod kątem usługi Azure Stack. 
 
 Agent systemu Linux platformy Azure (zazwyczaj nazywany `WALinuxAgent` lub `walinuxagent`) jest wymagana, i nie wszystkie wersje agenta będzie działać w usłudze Azure Stack. Należy używać wersji 2.2.18 lub nowszej, jeśli tworzysz własny obraz. Należy pamiętać, że [pakietu cloud-init](https://cloud-init.io/) nie jest obsługiwana w usłudze Azure Stack w tej chwili.

 Można przygotować własnego obrazu systemu Linux przy użyciu następujących instrukcji:

   * [Dystrybucje systemu centOS](../virtual-machines/linux/create-upload-centos.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
   * [Debian systemu Linux](../virtual-machines/linux/debian-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
   * [Red Hat Enterprise Linux](azure-stack-redhat-create-upload-vhd.md)
   * [SLES & openSUSE](../virtual-machines/linux/suse-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
   * [Serwer Ubuntu](../virtual-machines/linux/create-upload-ubuntu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

    
## <a name="add-your-image-to-the-marketplace"></a>Dodawanie obrazu do portalu marketplace
 
Postępuj zgodnie z [dodać obraz do portalu Marketplace](azure-stack-add-vm-image.md). Upewnij się, że `OSType` parametr ma wartość `Linux`.

Po dodaniu obrazu portalu Marketplace, tworzony jest element portalu Marketplace, a użytkownicy mogą wdrażać maszyny wirtualnej systemu Linux.
