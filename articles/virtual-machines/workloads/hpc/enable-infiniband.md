---
title: Włącz InifinBand za pomocą funkcji SR-IOV — usługa Azure Virtual Machines | Dokumentacja firmy Microsoft
description: Dowiedz się, jak włączyć InfiniBand za pomocą funkcji SR-IOV.
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 05/15/2019
ms.author: amverma
ms.openlocfilehash: 81acb804ed2ebb9e88bc7d8281a7fa52359d4455
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66810085"
---
# <a name="enable-infiniband-with-sr-iov"></a>Włącz InfiniBand za pomocą funkcji SR-IOV


Najprostszy i zalecany sposób konfigurowania niestandardowego obrazu maszyny Wirtualnej za pomocą InfiniBand (IB) jest dodanie rozszerzenia InfiniBandDriverLinux lub InfiniBandDriverWindows maszyn wirtualnych do wdrożenia.
Dowiedz się, jak używać tych rozszerzeń maszyn wirtualnych za pomocą [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-hpc#rdma-capable-instances) i [Windows](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-hpc#rdma-capable-instances)

Aby ręcznie skonfigurować InfiniBand na SR-IOV (obecnie serii HB i HC) maszyn wirtualnych z włączoną, wykonaj poniższe kroki. Te kroki są tylko RHEL/centos. Dla obrazów systemów Ubuntu (16.04 i 18.04) i w systemie SLES (12 z dodatkiem SP4 i 15) sterowniki wewnętrzne działać prawidłowo. Aby uzyskać systemie Ubuntu 


## <a name="manually-install-ofed"></a>Ręcznie zainstaluj OFED

Zainstaluj najnowsze sterowniki MLNX_OFED ConnectX-5 z [Mellanox](http://www.mellanox.com/page/products_dyn?product_family=26).

RHEL/centos (przykład poniżej 7.6):
```bash
sudo yum install -y kernel-devel python-devel
sudo yum install -y redhat-rpm-config rpm-build gcc-gfortran gcc-c++
sudo yum install -y gtk2 atk cairo tcl tk createrepo
wget --retry-connrefused --tries=3 --waitretry=5 http://content.mellanox.com/ofed/MLNX_OFED-4.5-1.0.1.0/MLNX_OFED_LINUX-4.5-1.0.1.0-rhel7.6-x86_64.tgz
tar zxvf MLNX_OFED_LINUX-4.5-1.0.1.0-rhel7.6-x86_64.tgz
sudo ./MLNX_OFED_LINUX-4.5-1.0.1.0-rhel7.6-x86_64/mlnxofedinstall --add-kernel-support
```

Dla Windows, należy pobrać i zainstalować sterowniki WinOF 2 ConnectX-5 z [Mellanox](http://www.mellanox.com/page/products_dyn?product_family=32&menu_section=34)

## <a name="assign-an-ip-address"></a>Przypisz adres IP

Przypisz adres IP interfejsu ib0, za pomocą:

- Ręcznie przypisać adres IP do interfejsu ib0 (jako element główny).

    ```bash
    ifconfig ib0 $(sed '/rdmaIPv4Address=/!d;s/.*rdmaIPv4Address="\([0-9.]*\)".*/\1/' /var/lib/waagent/SharedConfig.xml)/16
    ```

LUB

- WALinuxAgent umożliwia przypisanie adresu IP i przypisz ją utrwalić.

    ```bash
    yum install -y epel-release
    yum install -y python-pip
    python -m pip install --upgrade pip setuptools wheel
    wget "https://github.com/Azure/WALinuxAgent/archive/release-2.2.36.zip"
    unzip release-2.2.36.zip
    cd WALinuxAgent*
    python setup.py install --register-service --force
    sed -i -e 's/# OS.EnableRDMA=y/OS.EnableRDMA=y/g' /etc/waagent.conf
    sed -i -e 's/# AutoUpdate.Enabled=y/AutoUpdate.Enabled=y/g' /etc/waagent.conf
    systemctl restart waagent
    ```

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej o [HPC](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/) na platformie Azure.
