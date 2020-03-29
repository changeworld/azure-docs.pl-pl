---
title: Znane problemy z maszynami wirtualnymi z serii HB i HC — maszyny wirtualne platformy Azure | Dokumenty firmy Microsoft
description: Dowiedz się więcej o znanych problemach z rozmiarami maszyn wirtualnych z serii HB na platformie Azure.
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
ms.openlocfilehash: 8d4b57fb2fee3849e102868c86fe3cab465fc70d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67707792"
---
# <a name="known-issues-with-hb-series-and-hc-series-vms"></a>Znane problemy z maszynami wirtualnymi z serii HB i HC

Ten artykuł zawiera najczęstsze problemy i rozwiązania podczas korzystania z maszyn wirtualnych serii HB i HC.

## <a name="dram-on-hb-series"></a>DRAM w serii HB

Maszyny wirtualne z serii HB mogą udostępniać tylko 228 GB pamięci RAM na maszynach wirtualnych gościa w tej chwili. Jest to spowodowane znanym ograniczeniem funkcji hypervisor platformy Azure, aby zapobiec przypisywaniu stron do lokalnego pamięci DRAM w domenach AMD CCX (NUMA) zarezerwowanych dla maszyny Wirtualnej gościa.

## <a name="accelerated-networking"></a>Accelerated Networking

Usługa Azure Accelerated Networking nie jest włączona w tej chwili, ale będzie w miarę postępów w okresie wersji zapoznawczej. Powiadomimy klientów, gdy ta funkcja jest obsługiwana.

## <a name="qp0-access-restriction"></a>Ograniczenie dostępu qp0

Aby zapobiec niskiemu poziomowi dostępu sprzętowego, który może spowodować luki w zabezpieczeniach, para kolejek 0 nie jest dostępna dla maszyn wirtualnych gości. Powinno to dotyczyć tylko akcji zwykle skojarzonych z administrowaniem kartą sieciową ConnectX-5 i uruchamianiem niektórych diagnostyki InfiniBand, takich jak ibdiagnet, ale nie z samymi aplikacjami użytkowników końcowych.

## <a name="ud-transport"></a>UD Transport

Po uruchomieniu seria HB i HC nie obsługuje dynamicznie połączonego transportu (DCT). Obsługa DCT będzie wdrażana w czasie. Obsługiwane są transporty niezawodnego połączenia (RC) i unreliable Datagram (UD).

## <a name="gss-proxy"></a>GSS Proxy

Serwer proxy GSS ma znany błąd w CentOS/RHEL 7.5, który może objawiać się jako znaczna wydajność i kara responsywności w przypadku użycia z NFS. Można to złagodzić za pomocą:

```console
sed -i 's/GSS_USE_PROXY="yes"/GSS_USE_PROXY="no"/g' /etc/sysconfig/nfs
```

## <a name="cache-cleaning"></a>Czyszczenie pamięci podręcznej

W systemach HPC często warto wyczyścić pamięć po zakończeniu zadania, zanim następnemu użytkownikowi zostanie przypisany ten sam węzeł. Po uruchomieniu aplikacji w systemie Linux może się okazać, że dostępna pamięć zmniejsza się, podczas gdy pamięć buforowa wzrasta, mimo że nie są uruchomione żadne aplikacje.

![Zrzut ekranu przedstawiający wiersz polecenia](./media/known-issues/cache-cleaning-1.png)

Użycie `numactl -H` pokaże, które NUMAnode(s) pamięć jest buforowana z (ewentualnie wszystkie). W systemie Linux użytkownicy mogą czyścić pamięci podręczne na trzy sposoby, aby przywrócić buforowaną lub buforowaną pamięć do "wolnej". Musisz być root lub mieć uprawnienia sudo.

```console
echo 1 > /proc/sys/vm/drop_caches [frees page-cache]
echo 2 > /proc/sys/vm/drop_caches [frees slab objects e.g. dentries, inodes]
echo 3 > /proc/sys/vm/drop_caches [cleans page-cache and slab objects]
```

![Zrzut ekranu przedstawiający wiersz polecenia](./media/known-issues/cache-cleaning-2.png)

## <a name="kernel-warnings"></a>Ostrzeżenia jądra

Podczas uruchamiania maszyny wirtualnej z serii HB w systemie Linux mogą pojawić się następujące komunikaty ostrzegawcze jądra.

```console
[  0.004000] WARNING: CPU: 4 PID: 0 at arch/x86/kernel/smpboot.c:376 topology_sane.isra.3+0x80/0x90
[  0.004000] sched: CPU #4's llc-sibling CPU #0 is not on the same node! [node: 1 != 0]. Ignoring dependency.
[  0.004000] Modules linked in:
[  0.004000] CPU: 4 PID: 0 Comm: swapper/4 Not tainted 3.10.0-957.el7.x86_64 #1
[  0.004000] Hardware name: Microsoft Corporation Virtual Machine/Virtual Machine, BIOS 090007 05/18/2018
[  0.004000] Call Trace:
[  0.004000] [<ffffffffb8361dc1>] dump_stack+0x19/0x1b
[  0.004000] [<ffffffffb7c97648>] __warn+0xd8/0x100
[  0.004000] [<ffffffffb7c976cf>] warn_slowpath_fmt+0x5f/0x80
[  0.004000] [<ffffffffb7c02b34>] ? calibrate_delay+0x3e4/0x8b0
[  0.004000] [<ffffffffb7c574c0>] topology_sane.isra.3+0x80/0x90
[  0.004000] [<ffffffffb7c57782>] set_cpu_sibling_map+0x172/0x5b0
[  0.004000] [<ffffffffb7c57ce1>] start_secondary+0x121/0x270
[  0.004000] [<ffffffffb7c000d5>] start_cpu+0x5/0x14
[  0.004000] ---[ end trace 73fc0e0825d4ca1f ]---
```

Można zignorować to ostrzeżenie. Jest to spowodowane znanym ograniczeniem funkcji hypervisor platformy Azure, które zostaną omówione w czasie.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [obliczeniach o wysokiej wydajności na](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/) platformie Azure.
