---
title: Włączanie InifinBand przy użyciu funkcji SR-IOV-Azure Virtual Machines | Microsoft Docs
description: Dowiedz się, jak włączyć funkcję InfiniBand przy użyciu funkcji SR-IOV.
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 05/15/2019
ms.author: amverma
ms.openlocfilehash: 7218fceae71969f204c6c25ba4793a7c94341693
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/10/2019
ms.locfileid: "70858489"
---
# <a name="enable-infiniband-with-sr-iov"></a>Włączanie funkcji InfiniBand przy użyciu funkcji SR-IOV

Najprostszym i zalecanym sposobem rozpoczęcia pracy z maszynami wirtualnymi IaaS dla HPC jest użycie obrazu systemu operacyjnego CentOS-HPC 7,6. W przypadku korzystania z niestandardowego obrazu maszyny wirtualnej Najprostszym sposobem skonfigurowania go przy użyciu funkcji InfiniBand (IB) jest dodanie rozszerzenia maszyny wirtualnej InfiniBandDriverLinux lub InfiniBandDriverWindows do wdrożenia.
Dowiedz się, jak używać tych rozszerzeń maszyn wirtualnych z systemami [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-hpc#rdma-capable-instances) i [Windows](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-hpc#rdma-capable-instances)

Aby ręcznie skonfigurować InfiniBand na maszynach wirtualnych z obsługą wirtualizacji SR-IOV (obecnie HB i HC), wykonaj poniższe kroki. Te kroki dotyczą tylko RHEL/CentOS. W przypadku Ubuntu (16,04 i 18,04) oraz SLES (12 SP4 i 15) Sterowniki skrzynki odbiorczej działają prawidłowo.

## <a name="manually-install-ofed"></a>Ręczne instalowanie OFED

Zainstaluj najnowsze sterowniki MLNX_OFED dla systemu ConnectX-5 z [Mellanox](https://www.mellanox.com/page/products_dyn?product_family=26).

Dla RHEL/CentOS (przykład poniżej dla 7,6):

```bash
sudo yum install -y kernel-devel python-devel
sudo yum install -y redhat-rpm-config rpm-build gcc-gfortran gcc-c++
sudo yum install -y gtk2 atk cairo tcl tk createrepo
wget --retry-connrefused --tries=3 --waitretry=5 http://content.mellanox.com/ofed/MLNX_OFED-4.5-1.0.1.0/MLNX_OFED_LINUX-4.5-1.0.1.0-rhel7.6-x86_64.tgz
tar zxvf MLNX_OFED_LINUX-4.5-1.0.1.0-rhel7.6-x86_64.tgz
sudo ./MLNX_OFED_LINUX-4.5-1.0.1.0-rhel7.6-x86_64/mlnxofedinstall --add-kernel-support
```

W przypadku systemu Windows Pobierz i zainstaluj sterowniki WinOF-2 dla programu ConnectX-5 z [Mellanox](https://www.mellanox.com/page/products_dyn?product_family=32&menu_section=34)

## <a name="enable-ipoib"></a>Włącz program IPoIB

```bash
sudo sed -i 's/LOAD_EIPOIB=no/LOAD_EIPOIB=yes/g' /etc/infiniband/openib.conf
sudo /etc/init.d/openibd restart
if [ $? -eq 1 ]
then
  sudo modprobe -rv  ib_isert rpcrdma ib_srpt
  sudo /etc/init.d/openibd restart
fi
```

## <a name="assign-an-ip-address"></a>Przypisywanie adresu IP

Przypisz adres IP do interfejsu ib0 przy użyciu:

- Ręcznie Przypisz adres IP do interfejsu ib0 (jako główny).

    ```bash
    ifconfig ib0 $(sed '/rdmaIPv4Address=/!d;s/.*rdmaIPv4Address="\([0-9.]*\)".*/\1/' /var/lib/waagent/SharedConfig.xml)/16
    ```

LUB

- Użyj WALinuxAgent, aby przypisać adres IP i zachować jego trwałość.

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

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [HPC](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/) na platformie Azure.
