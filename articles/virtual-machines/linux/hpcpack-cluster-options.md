---
title: Opcje klastra pakiet HPC systemu Linux na platformie Azure | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o opcjach z pakietem Microsoft HPC tworzenie i zarządzanie nimi Linux wysokiej wydajności obliczeniowej klastra (HPC) w chmurze Azure
services: virtual-machines-linux,cloud-services
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
tags: azure-resource-manager,azure-service-management,hpc-pack
ms.assetid: ac60624e-aefa-40c3-8bc1-ef6d5c0ef1a2
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: big-compute
ms.date: 05/14/2018
ms.author: danlep
ms.openlocfilehash: 281867e30c78c7ed36ac739c8ae1a902463199cd
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/14/2018
ms.locfileid: "34166458"
---
# <a name="options-with-hpc-pack-to-create-and-manage-a-cluster-for-linux-hpc-workloads-in-azure"></a>Opcje pakietem HPC tworzenie i zarządzanie nimi w klastrze HPC systemu Linux obciążeń na platformie Azure
[!INCLUDE [virtual-machines-common-hpcpack-cluster-options](../../../includes/virtual-machines-common-hpcpack-cluster-options.md)]

Ten artykuł skupia się na temat opcji Azure na potrzeby uruchamiania obciążeń Linux HPC Pack. Dostępne są także opcje do uruchomienia [obciążeń HPC systemu Windows z pakietem HPC](../windows/hpcpack-cluster-options.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="hpc-pack-cluster-in-azure-vms-and-vm-scale-sets"></a>Klaster HPC Pack w maszynach wirtualnych platformy Azure i zestawy skalowania maszyny Wirtualnej
### <a name="azure-resource-manager-templates"></a>Szablony usługi Azure Resource Manager
* (GitHub) [Szablony klastra HPC Pack 2016 Update 1](https://github.com/MsHpcPack/HPCPack2016)
* (GitHub) [Szablony klastra HPC Pack 2012 R2](https://github.com/MsHpcPack/HPCPack2012R2)
* (Szybki Start) [Utworzyć klaster HPC Pack 2012 R2 z węzłami obliczeniowymi systemu Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/create-hpc-cluster-linux-cn)

### <a name="azure-vm-images"></a>Obrazy maszyny Wirtualnej platformy Azure
Przeglądaj [HPC Pack obrazów w portalu Azure Marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps?page=1&search=%22HPC%20%20Pack%22) Jeśli chcą tworzyć własnego klastra HPC Pack na platformie Azure.

## <a name="hpc-pack-2012-r2-cluster-in-classic-deployment-model"></a>Klaster HPC Pack 2012 R2 w klasycznym modelu wdrażania
* [Tworzenie klastra HPC systemu Linux przy użyciu skryptu wdrażania HPC Pack IaaS](../windows/classic/hpcpack-cluster-powershell-script.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [Konfigurowanie klastra Linux RDMA na uruchamianie aplikacji MPI](classic/rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [Samouczek: Rozpoczynanie pracy z węzłów obliczeniowych systemu Linux w klastrze HPC Pack na platformie Azure](classic/hpcpack-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [Samouczek: Uruchom NAMD z pakietem Microsoft HPC w systemie Linux obliczeniowe węzłów na platformie Azure](classic/hpcpack-cluster-namd.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [Samouczek: Uruchom OpenFOAM z pakietem Microsoft HPC w klastrze RDMA systemu Linux na platformie Azure](classic/hpcpack-cluster-openfoam.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [Samouczek: Uruchom GWIAZDKĘ — CCM + z pakietem Microsoft HPC na Linux RDMA klaster na platformie Azure](classic/hpcpack-cluster-starccm.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)

## <a name="job-submisssion"></a>Submisssion zadania
* [Przesyłanie zadań do klastra HPC Pack na platformie Azure](../windows/hpcpack-cluster-submit-jobs.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)




