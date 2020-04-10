---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 04/08/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: c3e5beaef7fcc9d407103834e2040957ff32984c
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/10/2020
ms.locfileid: "81008560"
---
Dyski udostępnione platformy Azure (wersja zapoznawcza) to nowa funkcja dla dysków zarządzanych platformy Azure, która umożliwia jednoczesne dołączanie dysku zarządzanego do wielu maszyn wirtualnych (VM). Dołączanie dysku zarządzanego do wielu maszyn wirtualnych umożliwia wdrożenie nowych lub migrację istniejących aplikacji klastrowanych na platformę Azure.

## <a name="how-it-works"></a>Jak to działa

Maszyny wirtualne w klastrze mogą odczytywać lub zapisywać na dołączonym dysku na podstawie rezerwacji wybranej przez aplikację klastrowane przy użyciu [trwałych rezerwacji SCSI](https://www.t10.org/members/w_spc3.htm) (SCSI PR). SCSI PR jest standardem branżowym, który jest w siecią san (network) w środowisku lokalnym. Włączenie funkcji ŚT SCSI na dysku zarządzanym umożliwia migrację tych aplikacji na platformę Azure w stanie— jest.

Udostępnianie dysków zarządzanych oferują udostępniony magazyn bloków, do którego można uzyskać dostęp z wielu maszyn wirtualnych, są one udostępniane jako numery jednostek logicznych (LUN). Nazwy LU są następnie przedstawiane inicjatorowi (VM) z obiektu docelowego (dysku). Te numery LUN wyglądają jak bezpośrednio dołączonej pamięci masowej (DAS) lub dysku lokalnego do maszyny Wirtualnej.

Udostępnione dyski zarządzane nie oferują natywnie w pełni zarządzanego systemu plików, do który można uzyskać dostęp przy użyciu usług SMB/NFS. Należy użyć menedżera klastra, takiego jak Klaster trybu failover systemu Windows Server (WSFC) lub Rozrusznik, który obsługuje komunikację węzła klastra, a także blokowanie zapisu.

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

### <a name="ultra-disks-reservation-flow"></a>Przepływ rezerwacji dysków ultra

Dyski ultra oferują dodatkową przepustnicę, w sumie dwie przepustnice. W związku z tym przepływ rezerwacji dysków ultra może działać zgodnie z opisem we wcześniejszej sekcji lub może ograniczać i dystrybuować wydajność bardziej szczegółową.

:::image type="content" source="media/virtual-machines-disks-shared-disks/ultra-reservation-table.png" alt-text=" ":::

## <a name="ultra-disk-performance-throttles"></a>Przepustnice wydajności ultradysków dysków

Dyski ultra mają unikalną możliwość pozwalając na ustawienie wydajności, ujawniając modyfikowalne atrybuty i umożliwiając ich modyfikowanie. Domyślnie istnieją tylko dwa modyfikowalne atrybuty, ale udostępnione dyski ultra mają dwa dodatkowe atrybuty.


|Atrybut  |Opis  |
|---------|---------|
|DiskIOPSReadWrite     |Całkowita liczba we/wy dozwolone na wszystkich maszynach wirtualnych montażu dysku udziału z dostępem do zapisu.         |
|DiskMBpsReadWrite (DiskMBpsReadWrite)     |Całkowita przepływność (MB/s) dozwolona na wszystkich maszynach wirtualnych montujących dysk udostępniony z dostępem do zapisu.         |
|DiskIOPSTyp tylko*     |Całkowita liczba we/wy dozwolone na wszystkich maszynach wirtualnych montujących dysk udostępniony jako ReadOnly.         |
|DiskMBpsTyp tylko*     |Całkowita przepływność (MB/s) dozwolona na wszystkich maszynach wirtualnych montujących dysk udostępniony jako ReadOnly.         |

\*Dotyczy tylko współdzielonych dysków ultra

Następujące formuły wyjaśniają, jak można ustawić atrybuty wydajności, ponieważ można je modyfikować przez użytkownika:

- DiskIOPSReadWrite/DiskIOPSCzytaj tylko: 
    - Limity we/wy we/wy 300 IOPS/GiB, maksymalnie 160 000 we/wy na dysk
    - Minimum of 100 IOPS
    - DiskIOPSReadWrite + DiskIOPSReadOnly jest co najmniej 2 IOPS / GiB
- DiskMBpsRead Zapis/DiskMBpsReadOnly:
    - Limit przepływności pojedynczego dysku wynosi 256 KiB/s dla każdego aprowizowanego we/wy, maksymalnie do 2000 MB/s na dysk
    - Minimalna gwarantowana przepustowość na dysku wynosi 4KiB/s dla każdego aprowizowanego we/wy, przy ogólnym poziomie wyjściowym minimum 1 MB/s

### <a name="examples"></a>Przykłady

Poniższe przykłady przedstawiają kilka scenariuszy, które pokazują, jak ograniczanie może pracować z udostępnionych dysków ultra, w szczególności.

#### <a name="two-nodes-cluster-using-cluster-shared-volumes"></a>Klaster dwóch węzłów przy użyciu udostępnionych woluminów klastra

Poniżej przedstawiono przykład 2-węzłowego WSFC przy użyciu klastrowanych woluminów udostępnionych. W tej konfiguracji obie maszyny wirtualne mają jednoczesny dostęp do zapisu do dysku, co powoduje, że przepustnica ReadWrite jest dzielona między dwie maszyny wirtualne i nie jest używana dławienie readonly.

:::image type="complex" source="media/virtual-machines-disks-shared-disks/ultra-two-node-example.png" alt-text="CSV dwa węzły ultra przykład":::

:::image-end:::

#### <a name="two-node-cluster-without-cluster-share-volumes"></a>Klaster dwóch węzłów bez woluminów udziału klastra

Poniżej przedstawiono przykład 2-węzłowego WSFC, który nie używa klastrowanych woluminów udostępnionych. W tej konfiguracji tylko jedna maszyna wirtualna ma dostęp do zapisu na dysku. Powoduje to, że przepustnica ReadWrite jest używany wyłącznie dla podstawowej maszyny Wirtualnej i Przepustnicy ReadOnly używane tylko przez pomocniczego.

:::image type="complex" source="media/virtual-machines-disks-shared-disks/ultra-two-node-no-csv.png" alt-text="CSV dwa węzły nie csv ultra przykład dysku":::

:::image-end:::

#### <a name="four-node-linux-cluster"></a>Klaster Linuksa z czterema węzłami

Poniżej przedstawiono przykład 4-węzłowego klastra Linuksa z pojedynczym modułem zapisu i trzema czytnikami skalowa w poziomie. W tej konfiguracji tylko jedna maszyna wirtualna ma dostęp do zapisu na dysku. Powoduje to, że przepustnica ReadWrite jest używana wyłącznie dla podstawowej maszyny Wirtualnej i przepustnicy ReadOnly są dzielone przez pomocnicze maszyny wirtualne.

:::image type="complex" source="media/virtual-machines-disks-shared-disks/ultra-four-node-example.png" alt-text="Przykład ultra ograniczania przepustowości czterech węzłów":::

:::image-end:::