---
title: Włącz opaskę InifinBand z SR-IOV — maszyny wirtualne platformy Azure | Dokumenty firmy Microsoft
description: Dowiedz się, jak włączyć infiniband z SR-IOV.
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 10/17/2019
ms.author: amverma
ms.openlocfilehash: 7f7907482da886d9da17ef1e7844b205f3e4b906
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74196765"
---
# <a name="enable-infiniband-with-sr-iov"></a>Włącz infiniband z SR-IOV

Maszyny wirtualne z serii Nc, ND i H platformy Azure są wspierane przez dedykowaną sieć InfiniBand. Wszystkie rozmiary z obsługą RDMA umożliwiają wykorzystanie tej sieci przy użyciu interfejsu Intel MPI. Niektóre serie maszyn wirtualnych rozszerzyły obsługę wszystkich implementacji MPI i zleceń RDMA za pośrednictwem SR-IOV. Maszyny wirtualne obsługujące rdma obejmują [maszyny wirtualne zoptymalizowane pod kątem procesora GPU](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu) i [maszyny wirtualne o wysokiej wydajności obliczeniowej (HPC).](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-hpc)

## <a name="choose-your-installation-path"></a>Wybierz ścieżkę instalacji

Aby rozpocząć, najprostszą opcją jest użycie obrazu platformy wstępnie skonfigurowanego dla InfiniBand, jeśli jest dostępny:

- **Maszyny wirtualne HPC IaaS** — aby rozpocząć korzystanie z maszyn wirtualnych IaaS dla HPC, najprostszym rozwiązaniem jest użycie [obrazu systemu operacyjnego CentOS-HPC 7.6 VM](https://techcommunity.microsoft.com/t5/Azure-Compute/CentOS-HPC-VM-Image-for-SR-IOV-enabled-Azure-HPC-VMs/ba-p/665557), który jest już skonfigurowany z infiniband. Ponieważ ten obraz jest już skonfigurowany z opaską InfiniBand, nie trzeba go ręcznie konfigurować. Aby zapoznać się ze zgodnymi wersjami systemu Windows, zobacz [Wystąpienia obsługujące technologię RDMA systemu Windows](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-hpc#rdma-capable-instances).

- **Gpu IaaS VM -** Żadne obrazy platformy nie są obecnie wstępnie skonfigurowane dla maszyn wirtualnych zoptymalizowanych pod kątem gpu, z wyjątkiem [obrazu centos-HPC 7.6 VM OS](https://techcommunity.microsoft.com/t5/Azure-Compute/CentOS-HPC-VM-Image-for-SR-IOV-enabled-Azure-HPC-VMs/ba-p/665557). Aby skonfigurować obraz niestandardowy za pomocą infiniBand, zobacz [Ręczne instalowanie programu Mellanox OFED](#manually-install-mellanox-ofed).

Jeśli używasz niestandardowego obrazu maszyny Wirtualnej lub maszyny wirtualnej [zoptymalizowanej pod kątem procesora GPU,](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu) należy skonfigurować ją za pomocą programu InfiniBand, dodając do wdrożenia rozszerzenie infiniBandDriverLinux lub InfiniBandDriverWindows. Dowiedz się, jak korzystać z tych rozszerzeń maszyn wirtualnych w [systemach Linux](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-hpc#rdma-capable-instances) i [Windows](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-hpc#rdma-capable-instances).

## <a name="manually-install-mellanox-ofed"></a>Ręcznie zainstaluj Mellanox OFED

Aby ręcznie skonfigurować opaskę InfiniBand z sr-iov, należy wykonać następujące kroki. W poniższym kroki kroki przedstawiono składnię RHEL/CentOS, ale kroki są ogólne i mogą być używane dla dowolnego zgodnego systemu operacyjnego, takiego jak Ubuntu (16.04, 18.04 19.04) i SLES (12 SP4 i 15). Sterowniki skrzynki odbiorczej również działają, ale sterowniki Mellanox OpenFabrics zapewniają więcej funkcji.

Aby uzyskać więcej informacji na temat obsługiwanych dystrybucji sterownika Mellanox, zobacz najnowsze [sterowniki Mellanox OpenFabrics](https://www.mellanox.com/page/products_dyn?product_family=26). Więcej informacji na temat sterownika Mellanox OpenFabrics można znaleźć w [podręczniku użytkownika Mellanox.](https://docs.mellanox.com/category/mlnxofedib)

Zobacz poniższy przykład, jak skonfigurować InfiniBand w systemie Linux:

```bash
# Modify the variable to desired Mellanox OFED version
MOFED_VERSION=#4.7-1.0.0.1
# Modify the variable to desired OS
MOFED_OS=#rhel7.6
pushd /tmp
curl -fSsL https://www.mellanox.com/downloads/ofed/MLNX_OFED-${MOFED_VERSION}/MLNX_OFED_LINUX-${MOFED_VERSION}-${MOFED_OS}-x86_64.tgz | tar -zxpf -
cd MLNX_OFED_LINUX-*
sudo ./mlnxofedinstall
popd
```

W przypadku systemu Windows pobierz i zainstaluj [sterowniki Mellanox OFED dla systemu Windows](https://www.mellanox.com/page/products_dyn?product_family=32&menu_section=34).

## <a name="enable-ip-over-infiniband"></a>Włączanie protokołu IP za sprawą infinibandu

Użyj następujących poleceń, aby włączyć protokół IP za pomocą paska InfiniBand.

```bash
sudo sed -i -e 's/# OS.EnableRDMA=y/OS.EnableRDMA=y/g' /etc/waagent.conf
sudo systemctl restart waagent
```

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [HPC](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/) na platformie Azure.
