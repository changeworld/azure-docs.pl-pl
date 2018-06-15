---
title: Obliczeniowych maszyn wirtualnych platformy Azure za pomocą partii | Dokumentacja firmy Microsoft
description: Zalety rozmiarów maszyn wirtualnych z funkcją RDMA lub włączone procesora GPU w pulach partii zadań Azure
services: batch
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: batch
ms.workload: big-compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/01/2018
ms.author: danlep
ms.openlocfilehash: 5a73e926b5979e573ccb0402ff2d23eae2463232
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2018
ms.locfileid: "29762445"
---
# <a name="use-rdma-capable-or-gpu-enabled-instances-in-batch-pools"></a>Użyj wystąpień z funkcją RDMA lub włączone procesora GPU w pulach partii

Aby uruchomić niektórych zadań wsadowych, można wykorzystać rozmiary maszyn wirtualnych Azure przeznaczone do obliczeń na dużą skalę. Na przykład, aby uruchomić wiele wystąpień [obciążeń MPI](batch-mpi.md), można wybrać A8 i A9, lub rozmiary H-series, które sieci interfejsu dla zdalnego bezpośredniego pamięci Access (RDMA). Rozmiary połączyć się z siecią InfiniBand, do komunikacji między węzłami, która umożliwia przyspieszanie MPI aplikacji. W przypadku aplikacji CUDA. można też rozmiary N-series, które obejmują grafiki tesla — NVIDIA przetwarzania kart jednostkę GPU.

Ten artykuł zawiera wskazówki i przykłady korzystać z niektórych rozmiarów specjalne platformy Azure w pulach partii. Aby uzyskać dane techniczne i tło Zobacz:

* Wysoka wydajność obliczeniowe rozmiarów maszyn wirtualnych ([Linux](../virtual-machines/linux/sizes-hpc.md), [Windows](../virtual-machines/windows/sizes-hpc.md)) 

* Rozmiary maszyn wirtualnych z obsługą procesora GPU ([Linux](../virtual-machines/linux/sizes-gpu.md), [Windows](../virtual-machines/windows/sizes-gpu.md)) 


## <a name="subscription-and-account-limits"></a>Subskrypcja oraz limity konta

* **Przydziały i limity** - [limit przydziału rdzeni na konto usługi partia zadań](batch-quota-limit.md#resource-quotas) można ograniczyć liczbę węzłów dany rozmiar można dodać do puli partii. Jest bardziej prawdopodobne do osiągnięcia przydziału po wybraniu wielordzeniowych rozmiarów maszyn wirtualnych z funkcją RDMA, włączone GPU lub inne. 

  Ponadto używać niektórych rodzin maszyn wirtualnych na koncie usługi partia zadań, takich jak NCv2, NCv3 i ND, jest ograniczony z powodu ograniczonej pojemności. Użyj tych rodzin jest dostępna tylko przez żądanie zwiększenia limitu przydziału w domyślnej liczby rdzeni (0).  

  Jeśli to konieczne, [zażądać zwiększenia limitu przydziału](batch-quota-limit.md#increase-a-quota) bez dodatkowych opłat.

* **Dostępność w danym regionie** — obliczeniowych maszyn wirtualnych mogą nie być dostępne w regionach, gdzie tworzenie konta usługi partia zadań. Aby sprawdzić, czy rozmiar jest dostępny, zobacz [produkty, które są dostępne w regionie](https://azure.microsoft.com/regions/services/).


## <a name="dependencies"></a>Zależności

RDMA i procesora GPU możliwości obliczeniowych rozmiary są obsługiwane tylko w niektórych systemach operacyjnych. W zależności od systemu operacyjnego może być konieczne zainstalować lub skonfigurować dodatkowe sterowników lub innego oprogramowania. Poniższe tabele zawierają podsumowanie tych zależności. Zobacz połączonych artykułów, aby uzyskać szczegółowe informacje. Dla opcji, aby skonfigurować pule partii zobacz w dalszej części tego artykułu.


### <a name="linux-pools---virtual-machine-configuration"></a>Pule Linux - konfiguracji maszyny wirtualnej

| Rozmiar | Możliwości | Systemy operacyjne | Wymagane oprogramowanie | Ustawienia puli |
| -------- | -------- | ----- |  -------- | ----- |
| [H16r, H16mr, A8, A9](../virtual-machines/linux/sizes-hpc.md#rdma-capable-instances) | RDMA | Ubuntu 16.04 LTS,<br/>SUSE Linux Enterprise Server 12 HPC, or<br/>Na podstawie centOS HPC<br/>(Azure Marketplace) | Intel MPI 5 | Włącz komunikację między węzłami, uniemożliwić wykonywanie zadań jednoczesnych |
| [NCv3 NC, NCv2, ND serii *](../virtual-machines/linux/n-series-driver-setup.md) | NVIDIA tesla — GPU (zależnie od serii) | Ubuntu 16.04 LTS,<br/>Red Hat Enterprise Linux 7.3 lub 7.4, lub<br/>CentOS 7.3 lub 7.4<br/>(Azure Marketplace) | Sterowniki NVIDIA CUDA Toolkit | ND | 
| [Seria wirtualizacją sieci](../virtual-machines/linux/n-series-driver-setup.md) | NVIDIA Tesla M60 GPU | Ubuntu 16.04 LTS,<br/>Red Hat Enterprise Linux 7.3, lub<br/>CentOS 7.3<br/>(Azure Marketplace) | Sterowniki NVIDIA siatki | ND |

* Mogą wymagać łączności RDMA na maszynach wirtualnych z funkcją RDMA N-series [dodatkowe czynności konfiguracyjne](../virtual-machines/linux/n-series-driver-setup.md#rdma-network-connectivity) która jest zależna od dystrybucji.



### <a name="windows-pools---virtual-machine-configuration"></a>Pule systemu Windows — konfiguracja maszyny wirtualnej

| Rozmiar | Możliwości | Systemy operacyjne | Wymagane oprogramowanie | Ustawienia puli |
| -------- | ------ | -------- | -------- | ----- |
| [H16r, H16mr, A8, A9](../virtual-machines/windows/sizes-hpc.md#rdma-capable-instances) | RDMA | Windows Server 2016, 2012 R2 lub<br/>2012 (azure Marketplace) | Microsoft MPI 2012 R2 lub nowszym, lub<br/> Intel MPI 5<br/><br/>Rozszerzenia maszyny Wirtualnej Azure HpcVMDrivers | Włącz komunikację między węzłami, uniemożliwić wykonywanie zadań jednoczesnych |
| [NCv3 NC, NCv2, ND serii *](../virtual-machines/windows/n-series-driver-setup.md) | NVIDIA tesla — GPU (zależnie od serii) | Windows Server 2016 lub <br/>2012 R2 (Azure Marketplace) | Tesla — NVIDIA sterowników lub sterowników CUDA Toolkit| ND | 
| [Seria wirtualizacją sieci](../virtual-machines/windows/n-series-driver-setup.md) | NVIDIA Tesla M60 GPU | Windows Server 2016 lub<br/>2012 R2 (Azure Marketplace) | Sterowniki NVIDIA siatki | ND |

* Łączność RDMA na maszynach wirtualnych z funkcją RDMA N-series jest obsługiwana w systemie Windows Server 2016 lub Windows Server 2012 R2 (z portalu Azure Marketplace) z rozszerzeniem HpcVMDrivers i MPI firmy Microsoft lub Intel MPI.

### <a name="windows-pools---cloud-services-configuration"></a>Pule systemu Windows — Konfiguracja usług w chmurze

> [!NOTE]
> Rozmiary serii N nie są obsługiwane w pulach partii z konfiguracją usługi w chmurze.
>

| Rozmiar | Możliwości | Systemy operacyjne | Wymagane oprogramowanie | Ustawienia puli |
| -------- | ------- | -------- | -------- | ----- |
| [H16r, H16mr, A8, A9](../virtual-machines/windows/sizes-hpc.md#rdma-capable-instances) | RDMA | Windows Server 2016, 2012 R2, 2012 lub<br/>2008 R2 (rodziny systemów operacyjnych gościa) | Microsoft MPI 2012 R2 lub nowszym, lub<br/>Intel MPI 5<br/><br/>Rozszerzenia maszyny Wirtualnej Azure HpcVMDrivers | Włącz komunikację między węzłami<br/> Wyłącz wykonywanie zadań jednoczesnych |





## <a name="pool-configuration-options"></a>Opcje konfiguracji puli

Aby skonfigurować specjalne rozmiar maszyny Wirtualnej z puli partii, narzędzia i interfejsy API partii podać kilka opcji do zainstalowania wymaganego oprogramowania lub sterowników, w tym:

* [Uruchom zadanie](batch-api-basics.md#start-task) -przekazać do konta magazynu Azure, w tym samym regionie co konto usługi partia zadań pakietu instalacyjnego jako plik zasobów. Utwórz wiersz polecenia zadania uruchamiania instalacji dyskretnej pliku zasobów, po uruchomieniu puli. Aby uzyskać więcej informacji, zobacz [dokumentacja interfejsu API REST](/rest/api/batchservice/add-a-pool-to-an-account#bk_starttask).

  > [!NOTE] 
  > Zadania uruchamiania musi działać z uprawnieniami z podniesionymi uprawnieniami (Administrator), a musi czekać, aż Powodzenie.
  >

* [Pakiet aplikacji](batch-application-packages.md) — Dodawanie pakietu ZIP instalacji do konta partii zadań i skonfigurować odwołanie do pakietu w puli. To ustawienie przekazuje i unzips pakiet we wszystkich węzłach w puli. Jeśli pakiet jest Instalator, Utwórz rozpoczęcia zadania wiersza polecenia do przeprowadzenia instalacji dyskretnej aplikacji we wszystkich węzłach puli. Opcjonalnie należy zainstalować pakiet, gdy zadanie jest zaplanowane do uruchomienia w węźle.

* [Obraz niestandardowy puli](batch-custom-images.md) — Tworzenie niestandardowego obrazu systemu Windows lub maszyny Wirtualnej systemu Linux, zawierający sterowniki, oprogramowanie, lub inne ustawienia wymagane dla rozmiaru maszyny Wirtualnej. 

* [Partii stoczni](https://github.com/Azure/batch-shipyard) automatycznie konfiguruje procesora GPU i dostępu RDMA do pracy niewidocznie konteneryzowanych obciążeń w partii zadań Azure. Stoczni partii całkowicie wynikają z plikami konfiguracyjnymi. Istnieje wiele przykładowych przepisu konfiguracji dostępne takie jak pozwalające obciążeń procesora GPU i dostępu RDMA [CNTK GPU przepisu](https://github.com/Azure/batch-shipyard/tree/master/recipes/CNTK-GPU-OpenMPI) co wstępnie konfiguruje wersji sterowników procesora GPU na maszynach wirtualnych N serii i ładuje oprogramowania kognitywnych zestaw narzędzi firmy Microsoft jako obraz Docker.


## <a name="example-microsoft-mpi-on-an-a8-vm-pool"></a>Przykład: Microsoft MPI w puli maszyna wirtualna A8

Do uruchamiania aplikacji systemu Windows MPI w puli węzłów Azure A8, należy zainstalować obsługiwaną implementację MPI. Poniżej przedstawiono przykładowe kroki, aby zainstalować [Microsoft MPI](https://msdn.microsoft.com/library/bb524831(v=vs.85).aspx) na Windows puli przy użyciu pakietu aplikacji partii.

1. Pobierz [pakiet instalacyjny](http://go.microsoft.com/FWLink/p/?LinkID=389556) (MSMpiSetup.exe) do najnowszej wersji programu Microsoft MPI.
2. Utwórz plik zip pakietu.
3. Przekaż pakiet do konta partii zadań. Aby uzyskać instrukcje, zobacz [pakietów aplikacji](batch-application-packages.md) wskazówki. Określ identyfikator aplikacji, takich jak *MSMPI*i wersji, takich jak *8.1*. 
4. Za pomocą interfejsów API partii lub portalu Azure, Utwórz pulę w konfiguracji usługi chmury z odpowiednią liczbę węzłów i skali. W poniższej tabeli przedstawiono przykładowe ustawienia, aby skonfigurować MPI w trybie instalacji nienadzorowanej za pomocą zadania uruchamiania:

| Ustawienie | Wartość |
| ---- | ----- | 
| **Typ obrazu** | Cloud Services |
| **Rodzina systemów operacyjnych** | Windows Server 2012 R2 (rodziny systemów operacyjnych 4) |
| **Rozmiaru węzła** | A8 Standard |
| **Włączono komunikację między węzłami** | True |
| **Maksymalna liczba zadań na węzeł** | 1 |
| **Odwołania do pakietu aplikacji** | MSMPI |
| **Uruchom zadanie włączone** | True<br>**Wiersz polecenia** - `"cmd /c %AZ_BATCH_APP_PACKAGE_MSMPI#8.1%\\MSMpiSetup.exe -unattend -force"`<br/>**Tożsamość użytkownika** -autouser puli, administracja<br/>**Poczekaj, aż Powodzenie** — PRAWDA

## <a name="example-nvidia-tesla-drivers-on-nc-vm-pool"></a>Przykład: Tesla — NVIDIA sterowników w puli NC maszyny Wirtualnej

Do uruchamiania aplikacji CUDA w puli węzłów Linux NC, musisz zainstalować CUDA Toolkit 9.0 w węzłach. Zestaw narzędzi instaluje niezbędne sterowniki NVIDIA tesla — GPU. Poniżej przedstawiono przykładowe kroki w celu wdrożenia niestandardowego obrazu Ubuntu 16.04 LTS sterowników procesora GPU:

1. Wdrożenie usługi Azure serii NC maszyny Wirtualnej z systemem Ubuntu 16.04 LTS. Na przykład utworzyć maszynę Wirtualną w regionie nam Południowa centralnej. Upewnij się, utwórz maszynę Wirtualną z dyskiem zarządzanym.
2. Wykonaj kroki, aby nawiązać połączenie z maszyną Wirtualną i [zainstalować sterowniki CUDA](../virtual-machines/linux/n-series-driver-setup.md).
3. Anulowanie zastrzeżenia agenta systemu Linux, a następnie [Przechwyć obraz maszyny Wirtualnej systemu Linux](../virtual-machines/linux/capture-image.md).
4. Tworzenie konta usługi partia zadań w obszarze obsługującego NC maszyn wirtualnych.
5. Za pomocą interfejsów API partii lub portalu Azure, Utwórz pulę [przy użyciu niestandardowego obrazu](batch-custom-images.md) i z odpowiednią liczbę węzłów i skali. W poniższej tabeli przedstawiono przykładowe ustawienia puli obrazu:

| Ustawienie | Wartość |
| ---- | ---- |
| **Typ obrazu** | Obraz niestandardowy |
| **Obraz niestandardowy** | Nazwa obrazu |
| **Agent węzła jednostki SKU** | batch.node.ubuntu 16.04 |
| **Rozmiaru węzła** | NC6 Standard |



## <a name="next-steps"></a>Kolejne kroki

* Uruchamianie zadań MPI w puli partii zadań Azure, zobacz [Windows](batch-mpi.md) lub [Linux](https://blogs.technet.microsoft.com/windowshpc/2016/07/20/introducing-mpi-support-for-linux-on-azure-batch/) przykłady.

* Przykłady obciążeń procesora GPU w partii, zobacz [stoczni partii](https://github.com/Azure/batch-shipyard/) przepisami.