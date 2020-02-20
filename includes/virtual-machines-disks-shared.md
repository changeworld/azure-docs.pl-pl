---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 02/18/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: a14ae76e15c1adb59917e61fbcbdaa34a7efa2d8
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/19/2020
ms.locfileid: "77472024"
---
Azure Shared disks (wersja zapoznawcza) to nowa funkcja dysków zarządzanych platformy Azure, która umożliwia równoczesne dołączanie dysku zarządzanego platformy Azure do wielu maszyn wirtualnych. Dołączenie dysku zarządzanego do wielu maszyn wirtualnych pozwala wdrożyć nowe lub migrować istniejące aplikacje klastrowane na platformę Azure.

## <a name="how-it-works"></a>Jak to działa

Maszyny wirtualne w klastrze mogą odczytywać lub zapisywać na dołączonym dysku na podstawie rezerwacji wybranych przez aplikację klastrowaną przy użyciu [rezerwacji trwałych SCSI](https://www.t10.org/members/w_spc3.htm) (SCSI PR). SCSI PR to dobrze znana branża standardowa, wykorzystywana przez aplikacje działające w sieci magazynowania (SAN) w środowisku lokalnym. Włączenie żądania ściągnięcia SCSI na dysku zarządzanym umożliwia migrowanie tych aplikacji na platformę Azure.

Dyski zarządzane z włączonymi dyskami udostępnionymi oferują udostępniony magazyn blokowy, do którego można uzyskać dostęp za pomocą wielu maszyn wirtualnych. jest to udostępniane jako numery jednostek logicznych (LUN). Jednostki LUN są następnie prezentowane inicjatorowi (VM) z miejsca docelowego (dysku). Te jednostki LUN wyglądają jak bezpośrednio dołączone do magazynu (DAS) lub dysk lokalny na maszynę wirtualną.

Dyski zarządzane z włączonymi dyskami udostępnionymi nie zapewniają natywnej obsługi w pełni zarządzanego systemu plików, do którego można uzyskać dostęp przy użyciu protokołu SMB/NFS. Konieczne będzie użycie Menedżera klastra, takiego jak Windows Server failover Cluster (WSFC) lub Pacemaker, który obsługuje komunikację węzłów klastra, a także blokowanie zapisu.

## <a name="limitations"></a>Ograniczenia

[!INCLUDE [virtual-machines-disks-shared-limitations](virtual-machines-disks-shared-limitations.md)]

## <a name="disk-sizes"></a>Rozmiary dysków

[!INCLUDE [virtual-machines-disks-shared-sizes](virtual-machines-disks-shared-sizes.md)]

## <a name="sample-workloads"></a>Przykładowe obciążenia

### <a name="windows"></a>Windows

Większość klastrów opartych na systemie Windows kompiluje się w usłudze WSFC, która obsługuje wszystkie podstawowe infrastruktury komunikacji węzłów klastra, co umożliwia aplikacjom korzystanie z wzorców dostępu równoległego. W zależności od używanej wersji systemu Windows Server usługi WSFC umożliwiają zarówno używanie woluminów CSV, jak i nieopartych na woluminach CSV. Aby uzyskać szczegółowe informacje, zobacz [Tworzenie klastra trybu failover](https://docs.microsoft.com/windows-server/failover-clustering/create-failover-cluster).

Niektóre popularne aplikacje działające w usłudze WSFC obejmują:

- SQL Server wystąpienia klastra trybu failover (FCI)
- Serwer plików skalowalny w poziomie (SoFS)
- Serwer plików do użytku ogólnego (obciążenie IW)
- Dysk profilu użytkownika serwera Pulpit zdalny (RDS UPD)
- SAP ASCS/SCS

### <a name="linux"></a>Linux

Klastry systemu Linux mogą korzystać z menedżerów klastra, takich jak [Pacemaker](https://wiki.clusterlabs.org/wiki/Pacemaker). Pacemaker kompiluje w [Corosync](http://corosync.github.io/corosync/), co umożliwia komunikację klastra w przypadku aplikacji wdrożonych w środowiskach o wysokiej dostępności. Niektóre typowe klastrowane systemy plików to [OCFS2](https://oss.oracle.com/projects/ocfs2/) i [GFS2](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/global_file_system_2/ch-overview-gfs2). Można manipulować rezerwacjami i rejestracjami przy użyciu narzędzi, takich jak [fence_scsi](http://manpages.ubuntu.com/manpages/eoan/man8/fence_scsi.8.html) i [sg_persist](https://linux.die.net/man/8/sg_persist).

## <a name="persistent-reservation-flow"></a>Trwały przepływ rezerwacji

Na poniższym diagramie przedstawiono przykładową klastrowaną aplikację bazy danych 2-węzłową, która wykorzystuje funkcję SCSI PR do przełączania trybu failover z jednego węzła do drugiego.

![Klaster z dwoma węzłami. Aplikacja działająca w klastrze obsługuje dostęp do dysku](media/virtual-machines-disks-shared-disks/shared-disk-updated-two-node-cluster-diagram.png)

Przepływ jest następujący:

1. Klastrowana aplikacja działająca na platformie Azure VM1 i VM2 rejestruje swój cel odczytu lub zapisu na dysku.
1. Wystąpienie aplikacji w witrynie VM1 pobiera wyłączną rezerwację do zapisu na dysku.
1. Ta rezerwacja jest wymuszana na dysku platformy Azure, a baza danych może teraz zapisywać dane na dysku. Wszystkie zapisy z wystąpienia aplikacji w systemie VM2 nie powiodą się.
1. Jeśli wystąpienie aplikacji w witrynie VM1 ulegnie awarii, wystąpienie w usłudze VM2 może teraz inicjować pracę w trybie failover bazy danych i przejmowanie dysku.
1. Ta rezerwacja jest teraz wymuszana na dysku platformy Azure, a dysk nie akceptuje już operacji zapisu z VM1. Akceptuje tylko zapisy z VM2.
1. Klastrowana aplikacja może zakończyć pracę w trybie failover bazy danych i obsłużyć żądania z VM2.

Na poniższym diagramie przedstawiono inne typowe obciążenie klastrowane składające się z wielu węzłów odczytywania danych z dysku na potrzeby uruchamiania procesów równoległych, takich jak szkolenie modeli uczenia maszynowego.

![Klaster z czterema węzłami VM, każdy węzeł rejestruje przeznaczenie do zapisu, aplikacja pobiera wyłączną rezerwację, aby prawidłowo obsługiwać wyniki zapisu](media/virtual-machines-disks-shared-disks/shared-disk-updated-machine-learning-trainer-model.png)

Przepływ jest następujący:

1. Aplikacja klastrowana uruchomiona na wszystkich maszynach wirtualnych rejestruje zamiar odczytu lub zapisu na dysku.
1. Wystąpienie aplikacji na VM1 pobiera wyłączną rezerwację do zapisu na dysku podczas otwierania odczytów na dysku z innych maszyn wirtualnych.
1. Ta rezerwacja jest wymuszana na dysku platformy Azure.
1. Wszystkie węzły w klastrze mogą teraz odczytywać z dysku. Tylko jeden węzeł zapisuje wyniki z powrotem na dysku w imieniu wszystkich węzłów w klastrze.