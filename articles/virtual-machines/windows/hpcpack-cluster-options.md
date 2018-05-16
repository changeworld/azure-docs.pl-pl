---
title: Opcje klastra systemu Windows HPC Pack na platformie Azure | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o opcjach z pakietem Microsoft HPC tworzenie i zarządzanie nimi Windows wysokiej wydajności obliczeniowej klastra (HPC) w chmurze Azure
services: virtual-machines-windows,cloud-services,batch
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
tags: azure-resource-manager,azure-service-management,hpc-pack
ms.assetid: 02c5566d-2129-483c-9ecf-0d61030442d7
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: big-compute
ms.date: 05/14/2018
ms.author: danlep
ms.openlocfilehash: 1232228d5e2fcd05f41a096a933072dffcca2119
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/14/2018
---
# <a name="options-with-hpc-pack-to-create-and-manage-a-cluster-for-windows-hpc-workloads-in-azure"></a>Opcje pakietem HPC tworzenie i zarządzanie nimi w klastrze HPC systemu Windows obciążeń na platformie Azure
[!INCLUDE [virtual-machines-common-hpcpack-cluster-options](../../../includes/virtual-machines-common-hpcpack-cluster-options.md)]

Ten artykuł skupia się na temat opcji Azure do tworzenia klastrów HPC Pack do uruchamiania obciążeń systemu Windows. Dostępne są także opcje tworzenia klastrów HPC Pack do uruchomienia [obciążeń HPC systemu Linux](../linux/hpcpack-cluster-options.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


## <a name="hpc-pack-cluster-in-azure-vms-and-vm-scale-sets"></a>Klaster HPC Pack w maszynach wirtualnych platformy Azure i zestawy skalowania maszyny Wirtualnej
### <a name="azure-resource-manager-templates"></a>Szablony usługi Azure Resource Manager
* (GitHub) [Szablony klastra HPC Pack 2016 Update 1](https://github.com/MsHpcPack/HPCPack2016)
* (GitHub) [Szablony klastra HPC Pack 2012 R2](https://github.com/MsHpcPack/HPCPack2012R2)
* (Szybki Start) [Utworzyć klaster HPC Pack 2012 R2](https://github.com/Azure/azure-quickstart-templates/tree/master/create-hpc-cluster)
* (Szybki Start) [Utworzyć klaster HPC Pack 2012 R2 z obrazu węzła obliczeń niestandardowych](https://github.com/Azure/azure-quickstart-templates/tree/master/create-hpc-cluster-custom-image)

### <a name="azure-vm-images"></a>Obrazy maszyny Wirtualnej platformy Azure
Przeglądaj [HPC Pack obrazów w portalu Azure Marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps?page=1&search=%22HPC%20%20Pack%22) Jeśli chcą tworzyć własnego klastra HPC Pack na platformie Azure.


### <a name="tutorials"></a>Samouczki
* [Samouczek: Wdrażanie klastra HPC Pack 2016 na platformie Azure](hpcpack-2016-cluster.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Zarządzanie klastra HPC Pack 2016 na platformie Azure w usłudze Azure Active Directory](hpcpack-cluster-active-directory.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)


## <a name="hpc-pack-2012-r2-cluster-deployment-in-the-classic-deployment-model"></a>Wdrożenie klastra HPC Pack 2012 R2 w klasycznym modelu wdrażania
* [Tworzenie klastra HPC z skrypt wdrożenia HPC Pack IaaS](classic/hpcpack-cluster-powershell-script.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)
* [Samouczek: Rozpoczynanie pracy z klastra HPC Pack na platformie Azure, aby uruchomić program Excel i SOA obciążeń](excel-cluster-hpcpack.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Zarządzanie węzłów obliczeniowych w klastrze HPC Pack na platformie Azure](classic/hpcpack-cluster-node-manage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)
* [Zwiększyć lub zmniejszyć zasoby obliczeniowe systemu Azure w klastrze HPC Pack](classic/hpcpack-cluster-node-autogrowshrink.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)
* [Konfigurowanie klastra RDMA systemu Windows z pakietem HPC do uruchamiania aplikacji MPI](classic/hpcpack-rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)


## <a name="burst-to-azure-from-hpc-pack-2012-r2"></a>Serii na platformie Azure z HPC Pack 2012 R2
* [Serii do wystąpień procesu roboczego platformy Azure z pakietem Microsoft HPC](https://technet.microsoft.com/library/gg481749.aspx)
* [Serii do partii zadań Azure z pakietem HPC](https://technet.microsoft.com/library/mt612877.aspx)
* [Samouczek: Konfigurowanie klastra hybrydowego pakietem HPC w systemie Azure](../../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md)

## <a name="job-submission"></a>Przesyłanie zadań

* [Przesyłanie zadań do klastra HPC Pack na platformie Azure](hpcpack-cluster-submit-jobs.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)






