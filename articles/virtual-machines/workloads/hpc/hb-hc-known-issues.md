---
title: Znane problemy związane z serii a HB i HC maszyny wirtualne z serii — usłudze Azure Virtual Machines | Dokumentacja firmy Microsoft
description: Dowiedz się więcej na temat znanych problemów dotyczących rozmiarów maszyn wirtualnych serii HB na platformie Azure.
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
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2019
ms.locfileid: "67707792"
---
# <a name="known-issues-with-hb-series-and-hc-series-vms"></a>Znane problemy z maszynami wirtualnymi z serii HB i HC

Ten artykuł zawiera najbardziej typowe problemy i ich rozwiązania, korzystając z serii HB i maszyny wirtualne z serii połączenia Hybrydowego.

## <a name="dram-on-hb-series"></a>DRAM HB do serii

Tylko maszyny wirtualne z serii HB może narazić 228 GB pamięci RAM na potrzeby maszyn wirtualnych gościa, w tym momencie. Jest to spowodowane znanym ograniczeniem funkcja hypervisor platformy Azure do uniemożliwić stron przypisywanych do lokalnej pamięci DRAM z AMD CCX firmy (domen NUMA) zarezerwowana dla maszyny Wirtualnej gościa.

## <a name="accelerated-networking"></a>Accelerated Networking

Usługa Azure Accelerated Networking w tej chwili nie jest włączona, ale będą w miarę postępów za pomocą korzystania z wersji zapoznawczej. Firma Microsoft powiadomi klientów, gdy ta funkcja jest obsługiwana.

## <a name="qp0-access-restriction"></a>Ograniczenie dostępu qp0

Aby uzyskać dostęp do niskiego poziomu sprzętu, który może spowodować powstanie luk w zabezpieczeniach, pary kolejek 0 nie jest dostępna dla maszyn wirtualnych gościa. Tylko powinno to mieć wpływ na działania zwykle skojarzone z administracyjnej karty Sieciowej ConnectX-5 i uruchamiania niektórych diagnostyki InfiniBand, jak ibdiagnet, ale nie aplikacje dla użytkowników końcowych samodzielnie.

## <a name="ud-transport"></a>UD transportu

Po uruchomieniu serii połączenia Hybrydowego i HB nie obsługują dynamicznie połączony Transport (DCT). Obsługa DCT będzie wdrażany wraz z upływem czasu. Niezawodne transportów połączenia (RC) i zawodnych datagramów (UD) są obsługiwane.

## <a name="gss-proxy"></a>Serwer Proxy GSS

Serwer Proxy GSS ma to znana usterka, w wersji 7.5 CentOS/RHEL, który może objawiać się istotnie poprawiającą wydajność i szybkość reakcji spadek, gdy jest używana z systemem plików NFS. Może to ograniczyć, przy użyciu:

```console
sed -i 's/GSS_USE_PROXY="yes"/GSS_USE_PROXY="no"/g' /etc/sysconfig/nfs
```

## <a name="cache-cleaning"></a>Czyszczenie pamięci podręcznej

W systemach HPC często jest przydatne wyczyścić pamięć, po zakończeniu zadania, przed przypisaniem tego samego węzła następnego użytkownika. Po uruchomieniu aplikacji w systemie Linux może się okazać, że Twoje dostępnej pamięci zmniejsza podczas buforu zwiększa pamięci, pomimo nie uruchamiania żadnych aplikacji.

![Zrzut ekranu przedstawiający monit polecenia](./media/known-issues/cache-cleaning-1.png)

Za pomocą `numactl -H` pokaże NUMAnode(s), którego pamięć jest buforowana (prawdopodobnie wszystko). W systemie Linux, użytkownicy mogą wyczyścić pamięci podręczne w trzech sposoby zwracać buforowane lub pamięci podręcznej pamięci, aby "bezpłatnej". Musisz być użytkownikiem głównym lub uprawnień "sudo".

```console
echo 1 > /proc/sys/vm/drop_caches [frees page-cache]
echo 2 > /proc/sys/vm/drop_caches [frees slab objects e.g. dentries, inodes]
echo 3 > /proc/sys/vm/drop_caches [cleans page-cache and slab objects]
```

![Zrzut ekranu przedstawiający monit polecenia](./media/known-issues/cache-cleaning-2.png)

## <a name="kernel-warnings"></a>Ostrzeżenia jądra

Może pojawić się następujące komunikaty ostrzegawcze jądra, podczas rozruchu HB serii maszynę Wirtualną w systemie Linux.

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

Można zignorować to ostrzeżenie. Jest to spowodowane znanym ograniczeniem funkcja hypervisor platformy Azure, które zostaną rozwiązane wraz z upływem czasu.

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej o [o wysokiej wydajności obliczeniowej](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/) na platformie Azure.
