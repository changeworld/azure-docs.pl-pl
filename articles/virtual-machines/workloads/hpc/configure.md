---
title: Obliczenia o wysokiej wydajności — maszyny wirtualne platformy Azure | Dokumenty firmy Microsoft
description: Dowiedz się więcej o obliczeniach o wysokiej wydajności na platformie Azure.
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 05/07/2019
ms.author: amverma
ms.openlocfilehash: 10549abfbdacf1fc1ae6b99f4cab20a290c32a2d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67707820"
---
# <a name="optimization-for-linux"></a>Optymalizacja na potrzeby systemu Linux

W tym artykule przedstawiono kilka kluczowych technik optymalizacji obrazu systemu operacyjnego. Dowiedz się więcej o [włączaniu opaski InfiniBand](enable-infiniband.md) i optymalizowaniu obrazów systemu operacyjnego.

## <a name="update-lis"></a>Aktualizacja LIS

W przypadku wdrażania przy użyciu obrazu niestandardowego (na przykład starszego systemu operacyjnego, takiego jak CentOS/RHEL 7.4 lub 7.5), zaktualizuj lis na maszynie wirtualnej.

```bash
wget https://aka.ms/lis
tar xzf lis
pushd LISISO
./upgrade.sh
```

## <a name="reclaim-memory"></a>Odzyskaj pamięć

Zwiększ wydajność, automatycznie odzyskując pamięć, aby uniknąć zdalnego dostępu do pamięci.

```bash
echo 1 >/proc/sys/vm/zone_reclaim_mode
```

Aby to się utrzymać po ponownym uruchomieniu maszyny Wirtualnej:

```bash
echo "vm.zone_reclaim_mode = 1" >> /etc/sysctl.conf sysctl -p
```

## <a name="disable-firewall-and-selinux"></a>Wyłącz zaporę i SELinux

Wyłącz zaporę i SELinux.

```bash
systemctl stop iptables.service
systemctl disable iptables.service
systemctl mask firewalld
systemctl stop firewalld.service
systemctl disable firewalld.service
iptables -nL
sed -i -e's/SELINUX=enforcing/SELINUX=disabled/g' /etc/selinux/config
```

## <a name="disable-cpupower"></a>Wyłączanie mocy procesora

Wyłącz moc procesora.

```bash
service cpupower status
if enabled, disable it:
service cpupower stop
sudo systemctl disable cpupower
```

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [włączaniu infinibandu](enable-infiniband.md) i optymalizowaniu obrazów systemu operacyjnego.

* Dowiedz się więcej o [HPC](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/) na platformie Azure.
