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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77472024"
---
Dyski udostępnione platformy Azure (wersja zapoznawcza) to nowa funkcja dla dysków zarządzanych platformy Azure, która umożliwia jednoczesne dołączanie dysku zarządzanego platformy Azure do wielu maszyn wirtualnych.Azure shared disks (preview) is a new feature for Azure managed disks that enables attaching an Azure managed disk to multiple virtual machines (VMs) simultaneously. Dołączanie dysku zarządzanego do wielu maszyn wirtualnych umożliwia wdrożenie nowych lub migrację istniejących aplikacji klastrowanych na platformę Azure.

## <a name="how-it-works"></a>Jak to działa

Maszyny wirtualne w klastrze mogą odczytywać lub zapisywać na dołączonym dysku na podstawie rezerwacji wybranej przez aplikację klastrowane przy użyciu [trwałych rezerwacji SCSI](https://www.t10.org/members/w_spc3.htm) (SCSI PR). SCSI PR jest znanym standardem branżowym, który jest korzystać z aplikacji działających lokalnie w sieci sieci magazynowej (SAN). Włączenie funkcji ŚT SCSI na dysku zarządzanym umożliwia migrację tych aplikacji na platformę Azure w stanie— jest.

Dyski zarządzane z włączonymi dyskami udostępnionymi oferują udostępniony magazyn bloków, do którego dostęp może uzyskać wiele maszyn wirtualnych, jest to udostępniane jako numery jednostek logicznych (LUN). Nazwy LU są następnie przedstawiane inicjatorowi (VM) z obiektu docelowego (dysku). Te numery LUN wyglądają jak bezpośrednio dołączonej pamięci masowej (DAS) lub dysku lokalnego do maszyny Wirtualnej.

Dyski zarządzane z włączonymi dyskami udostępnionymi nie oferują natywnie w pełni zarządzanego systemu plików, do który można uzyskać dostęp przy użyciu plików SMB/NFS. Należy użyć menedżera klastra, takiego jak Klaster trybu failover systemu Windows Server (WSFC) lub Rozrusznik, który obsługuje komunikację węzła klastra, a także blokowanie zapisu.

## <a name="limitations"></a>Ograniczenia

[!INCLUDE [virtual-machines-disks-shared-limitations](virtual-machines-disks-shared-limitations.md)]

## <a name="disk-sizes"></a>Rozmiary dysków

[!INCLUDE [virtual-machines-disks-shared-sizes](virtual-machines-disks-shared-sizes.md)]

## <a name="sample-workloads"></a>Przykładowe obciążenia

### <a name="windows"></a>Windows

Większość klastrowania opartego na systemie Windows opiera się na wydziale WSFC, który obsługuje całą podstawową infrastrukturę komunikacji węzłów klastra, umożliwiając aplikacjom korzystanie z wzorców dostępu równoległego. WSFC włącza opcje csv i inne niż csv w zależności od wersji systemu Windows Server. Aby uzyskać szczegółowe informacje, zobacz [Tworzenie klastra trybu failover](https://docs.microsoft.com/windows-server/failover-clustering/create-failover-cluster).

Niektóre popularne aplikacje działające w WSFC obejmują:

- Wystąpienia klastra trybu failover programu SQL Server (FCI)
- Skalowany w poziomie serwer plików (SoFS)
- Serwer plików do użytku ogólnego (obciążenie wywołują)
- Dysk profilu użytkownika serwera usług pulpitu zdalnego (RDS UPD)
- SAP ASCS/SCS

### <a name="linux"></a>Linux

Klastry Linuksa mogą korzystać z menedżerów klastrów, takich jak [Pacemaker.](https://wiki.clusterlabs.org/wiki/Pacemaker) Rozrusznik serca opiera się na [corosync,](http://corosync.github.io/corosync/)umożliwiając komunikację klastra dla aplikacji wdrożonych w środowiskach o wysokiej dostępności. Niektóre typowe klastrowane systemy plików obejmują [ocfs2](https://oss.oracle.com/projects/ocfs2/) i [gfs2](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/global_file_system_2/ch-overview-gfs2). Możesz manipulować rezerwacjami i rejestracjami za pomocą narzędzi, takich jak [fence_scsi](http://manpages.ubuntu.com/manpages/eoan/man8/fence_scsi.8.html) i [sg_persist](https://linux.die.net/man/8/sg_persist).

## <a name="persistent-reservation-flow"></a>Przepływ rezerwacji trwałej

Na poniższym diagramie przedstawiono przykładową aplikację klastrowane bazy danych 2 węzłów, która wykorzystuje informacje PR SCSI, aby włączyć pracy awaryjnej z jednego węzła do drugiego.

![Klaster dwóch węzłów. Aplikacja uruchomiona w klastrze obsługuje dostęp do dysku](media/virtual-machines-disks-shared-disks/shared-disk-updated-two-node-cluster-diagram.png)

Przepływ jest następujący:

1. Aplikacja klastrowana uruchomiona na platformie Azure VM1 i VM2 rejestruje zamiar odczytu lub zapisu na dysku.
1. Wystąpienie aplikacji na VM1 następnie trwa wyłączną rezerwację do zapisu na dysku.
1. Ta rezerwacja jest wymuszana na dysku platformy Azure i bazy danych można teraz zapisywać wyłącznie na dysku. Wszelkie zapisy z wystąpienia aplikacji na VM2 nie powiedzie się.
1. Jeśli wystąpienie aplikacji na VM1 ulegnie awarii, wystąpienie na maszynie VM2 może teraz zainicjować przemijają się w zależności od pracy awaryjnej bazy danych i przejęcia dysku.
1. Ta rezerwacja jest teraz wymuszana na dysku platformy Azure i dysk nie będzie już akceptować zapisów z maszyny Wirtualnej1. Będzie akceptować zapisy tylko z VM2.
1. Aplikacja klastrowana może zakończyć pracy awaryjnej bazy danych i obsługiwać żądania z VM2.

Na poniższym diagramie przedstawiono inne typowe obciążenie klastrowane składające się z wielu węzłów odczytujących dane z dysku do uruchamiania procesów równoległych, takich jak szkolenie modeli uczenia maszynowego.

![Czterowęzłowy klaster maszyn wirtualnych, każdy węzeł rejestruje zamiar zapisu, aplikacja przyjmuje wyłączną rezerwację, aby prawidłowo obsługiwać wyniki zapisu](media/virtual-machines-disks-shared-disks/shared-disk-updated-machine-learning-trainer-model.png)

Przepływ jest następujący:

1. Aplikacja klastrowana uruchomiona na wszystkich maszynach wirtualnych rejestruje zamiar odczytu lub zapisu na dysku.
1. Wystąpienie aplikacji na maszynie VM1 przyjmuje wyłączną rezerwację do zapisu na dysku podczas otwierania odczytów na dysku z innych maszyn wirtualnych.
1. Ta rezerwacja jest wymuszana na dysku platformy Azure.
1. Wszystkie węzły w klastrze można teraz odczytać z dysku. Tylko jeden węzeł zapisuje wyniki na dysku w imieniu wszystkich węzłów w klastrze.