---
title: Opcje klastra pakietu HPC Pack dla systemu Linux na platformie Azure | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o opcjach za pomocą pakietu Microsoft HPC Pack do tworzenia i zarządzania o wysokiej wydajności systemu Linux obliczeń (HPC) klastra w chmurze platformy Azure
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
ms.openlocfilehash: 79600909387b1876b112219b5b9b1e45ba4054b7
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/16/2018
ms.locfileid: "49340079"
---
# <a name="options-with-hpc-pack-to-create-and-manage-a-cluster-for-linux-hpc-workloads-in-azure"></a>Opcje za pomocą pakietu HPC Pack do tworzenia klastra i zarządzanie nim w przypadku obciążeń HPC w systemie Linux na platformie Azure
[!INCLUDE [virtual-machines-common-hpcpack-cluster-options](../../../includes/virtual-machines-common-hpcpack-cluster-options.md)]

Ten artykuł koncentruje się na opcji platformy Azure do używania pakietu HPC Pack do uruchamiania obciążeń systemu Linux. Dostępne są również opcje dotyczące uruchamiania [obciążeń Windows HPC za pomocą pakietu HPC Pack](../windows/hpcpack-cluster-options.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="hpc-pack-cluster-in-azure-vms-and-vm-scale-sets"></a>Klastra pakietu HPC Pack w maszynach wirtualnych platformy Azure i zestawów skalowania maszyn wirtualnych
### <a name="azure-resource-manager-templates"></a>Szablony usługi Azure Resource Manager
* (GitHub) [Szablony klastra HPC Pack 2016 Update 1](https://github.com/MsHpcPack/HPCPack2016)
* (GitHub) [Szablony klastra pakietu HPC Pack 2012 R2](https://github.com/MsHpcPack/HPCPack2012R2)
* (Szybki Start) [Utwórz klaster HPC Pack 2012 R2 z węzłów obliczeniowych systemu Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/create-hpc-cluster-linux-cn)

### <a name="azure-vm-images"></a>Obrazy maszyn wirtualnych platformy Azure
Przeglądaj [pakietu HPC Pack obrazów w portalu Azure Marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps?page=1&search=%22HPC%20%20Pack%22) Jeśli chcą tworzyć własnego klastra pakietu HPC Pack na platformie Azure.

## <a name="hpc-pack-2012-r2-cluster-in-classic-deployment-model"></a>Klaster HPC Pack 2012 R2 w klasycznym modelu wdrażania
* [Utwórz klaster HPC systemu Linux ze skryptem wdrażania IaaS pakietu HPC Pack](../windows/classic/hpcpack-cluster-powershell-script.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [Samouczek: Rozpoczynanie pracy z węzłami obliczeniowymi systemu Linux w klastrze pakietu HPC Pack na platformie Azure](classic/hpcpack-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [Samouczek: Uruchom NAMD przy użyciu pakietu Microsoft HPC Pack w systemie Linux węzłów obliczeniowych w usłudze Azure](classic/hpcpack-cluster-namd.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [Samouczek: Uruchom OpenFOAM przy użyciu pakietu Microsoft HPC Pack na klaster RDMA systemu Linux na platformie Azure](classic/hpcpack-cluster-openfoam.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [Samouczek: Uruchom STAR-CCM + za pomocą pakietu Microsoft HPC Pack na RDMA systemu Linux klastra na platformie Azure](classic/hpcpack-cluster-starccm.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)

## <a name="job-submisssion"></a>Submisssion zadania
* [Przesyłanie zadań do klastra pakietu HPC Pack na platformie Azure](../windows/hpcpack-cluster-submit-jobs.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)




