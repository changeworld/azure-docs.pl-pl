---
title: Używanie maszyn wirtualnych platformy Azure intensywnie korzystających z mocy obliczeniowej z usługą Batch
description: Jak korzystać z rozmiarów maszyn wirtualnych HPC i GPU w pulach usługi Azure Batch. Dowiedz się więcej o zależnościach systemu operacyjnego i zobacz kilka przykładów scenariuszy.
documentationcenter: ''
author: LauraBrenner
manager: evansma
editor: ''
ms.assetid: ''
ms.service: batch
ms.workload: big-compute
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 12/17/2018
ms.author: labrenne
ms.openlocfilehash: d0238e0b5b882748218835d7f06a147d435a9f90
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80245059"
---
# <a name="use-rdma-or-gpu-instances-in-batch-pools"></a>Używanie wystąpień RDMA lub GPU w pulach wsadowych

Aby uruchomić niektóre zadania usługi Batch, można skorzystać z rozmiarów maszyn wirtualnych platformy Azure przeznaczonych do obliczeń na dużą skalę. Przykład:

* Aby uruchomić obciążenia MPI z wieloma [wystąpieniami,](batch-mpi.md)wybierz serię H lub inne rozmiary, które mają interfejs sieciowy dla zdalnego bezpośredniego dostępu do pamięci (RDMA). Te rozmiary łączą się z siecią InfiniBand do komunikacji między węzłami, co może przyspieszyć aplikacje MPI. 

* W przypadku aplikacji CUDA wybierz rozmiary serii N, które zawierają karty procesora graficznego NVIDIA Tesla (GPU).

Ten artykuł zawiera wskazówki i przykłady, aby użyć niektórych wyspecjalizowanych rozmiarów platformy Azure w pulach usługi Batch. Dane techniczne i tło można znaleźć na:

* Rozmiary maszyn wirtualnych o wysokiej wydajności obliczeniowej ([Linux](../virtual-machines/linux/sizes-hpc.md), [Windows](../virtual-machines/windows/sizes-hpc.md)) 

* Rozmiary maszyn wirtualnych z obsługą procesora GRAFICZNEGO[(Linux](../virtual-machines/linux/sizes-gpu.md), [Windows](../virtual-machines/windows/sizes-gpu.md)) 

> [!NOTE]
> Niektóre rozmiary maszyn wirtualnych mogą nie być dostępne w regionach, w których utworzono konta usługi Batch. Aby sprawdzić, czy rozmiar jest dostępny, zobacz [Produkty dostępne według regionu](https://azure.microsoft.com/regions/services/) i [Wybierz rozmiar maszyny Wirtualnej dla puli partii](batch-pool-vm-sizes.md).

## <a name="dependencies"></a>Zależności

Możliwości RDMA lub GPU rozmiarów intensywnie korzystających z mocy obliczeniowej w udziale Batch są obsługiwane tylko w niektórych systemach operacyjnych. (Lista obsługiwanych systemów operacyjnych jest podzbiorem obsługiwanych dla maszyn wirtualnych utworzonych w tych rozmiarach). W zależności od sposobu tworzenia puli partii może być konieczne zainstalowanie lub skonfigurowanie dodatkowego sterownika lub innego oprogramowania w węzłach. W poniższych tabelach podsumowano te zależności. Szczegółowe informacje można znaleźć w połączonych artykułach. Aby uzyskać opcje konfigurowania pule usługi Batch, zobacz dalsze w tym artykule.

### <a name="linux-pools---virtual-machine-configuration"></a>Pule Linuksa — konfiguracja maszyny wirtualnej

| Rozmiar | Możliwości | Systemy operacyjne | Wymagane oprogramowanie | Ustawienia puli |
| -------- | -------- | ----- |  -------- | ----- |
| [H16r, H16mr, A8, A9](../virtual-machines/linux/sizes-hpc.md)<br/>[NC24r, NC24rs_v2, NC24rs_v3, ND24rs<sup>*</sup>](../virtual-machines/linux/n-series-driver-setup.md#rdma-network-connectivity) | Dostęp RDMA | Ubuntu 16.04 LTS, lub<br/>HPC oparte na centos<br/>(Azure Marketplace) | Intel MPI 5<br/><br/>Sterowniki RDMA linuksa | Włącz komunikację między węzłami, wyłącz wykonywanie równoczesnych zadań |
| [Seria NC, NCv2, NCv3, NDv2](../virtual-machines/linux/n-series-driver-setup.md) | NVIDIA Tesla GPU (zależy od serii) | Ubuntu 16.04 LTS, lub<br/>CentOS 7,3 lub 7,4<br/>(Azure Marketplace) | Sterowniki NVIDIA CUDA lub CUDA Toolkit | Nie dotyczy | 
| [Seria NV, NVv2](../virtual-machines/linux/n-series-driver-setup.md) | KARTA GRAFICZNA NVIDIA Tesla M60 | Ubuntu 16.04 LTS, lub<br/>CentOS 7,3<br/>(Azure Marketplace) | Sterowniki NVIDIA GRID | Nie dotyczy |

<sup>*</sup>Rozmiary serii N obsługujące RDMA obejmują również procesory graficzne NVIDIA Tesla

### <a name="windows-pools---virtual-machine-configuration"></a>Pule systemu Windows — konfiguracja maszyny wirtualnej

| Rozmiar | Możliwości | Systemy operacyjne | Wymagane oprogramowanie | Ustawienia puli |
| -------- | ------ | -------- | -------- | ----- |
| [H16r, H16mr, A8, A9](../virtual-machines/sizes-hpc.md)<br/>[NC24r, NC24rs_v2, NC24rs_v3, ND24rs<sup>*</sup>](../virtual-machines/windows/n-series-driver-setup.md#rdma-network-connectivity) | Dostęp RDMA | Windows Server 2016, 2012 R2 lub<br/>2012 (Azure Marketplace) | Microsoft MPI 2012 R2 lub nowsza, lub<br/> Intel MPI 5<br/><br/>Sterowniki RDMA systemu Windows | Włącz komunikację między węzłami, wyłącz wykonywanie równoczesnych zadań |
| [Seria NC, NCv2, NCv3, ND, NDv2](../virtual-machines/windows/n-series-driver-setup.md) | NVIDIA Tesla GPU (zależy od serii) | Windows Server 2016 lub <br/>2012 R2 (azure marketplace) | Sterowniki NVIDIA CUDA lub CUDA Toolkit| Nie dotyczy | 
| [Seria NV, NVv2](../virtual-machines/windows/n-series-driver-setup.md) | KARTA GRAFICZNA NVIDIA Tesla M60 | Windows Server 2016 lub<br/>2012 R2 (azure marketplace) | Sterowniki NVIDIA GRID | Nie dotyczy |

<sup>*</sup>Rozmiary serii N obsługujące RDMA obejmują również procesory graficzne NVIDIA Tesla

### <a name="windows-pools---cloud-services-configuration"></a>Pule systemu Windows — konfiguracja usług w chmurze

> [!NOTE]
> Rozmiary serii N nie są obsługiwane w pulach wsadowych z konfiguracją usługi w chmurze.
>

| Rozmiar | Możliwości | Systemy operacyjne | Wymagane oprogramowanie | Ustawienia puli |
| -------- | ------- | -------- | -------- | ----- |
| [H16r, H16mr, A8, A9](../virtual-machines/sizes-hpc.md) | Dostęp RDMA | Windows Server 2016, 2012 R2, 2012 lub<br/>2008 R2 (rodzina gościnnego systemu operacyjnego) | Microsoft MPI 2012 R2 lub nowsza, lub<br/>Intel MPI 5<br/><br/>Sterowniki RDMA systemu Windows | Włącz komunikację między węzłami,<br/> wyłączanie równoczesnego wykonywania zadań |

## <a name="pool-configuration-options"></a>Opcje konfiguracji puli

Aby skonfigurować rozmiar wyspecjalizowanej maszyny Wirtualnej dla puli wsadowej, istnieje kilka opcji zainstalowania wymaganego oprogramowania lub sterowników:

* W przypadku pul w konfiguracji maszyny wirtualnej wybierz wstępnie skonfigurowany obraz maszyny wirtualnej [usługi Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/) z preinstalowanymi sterownikami i oprogramowaniem. Przykłady: 

  * [CentOS-based 7.4 HPC](https://azuremarketplace.microsoft.com/marketplace/apps/openlogic.centos-hpc?tab=Overview) - zawiera sterowniki RDMA i Intel MPI 5.1

  * [Data Science Virtual Machine](../machine-learning/data-science-virtual-machine/overview.md) dla Linuksa lub Windows - zawiera sterowniki NVIDIA CUDA

  * Obrazy systemu Linux dla obciążeń kontenerów wsadowych, które obejmują również sterowniki GPU i RDMA:

    * [CentOS (ze sterownikami GPU i RDMA) dla pul kontenerów usługi Azure Batch](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-azure-batch.centos-container-rdma?tab=Overview)

    * [Ubuntu Server (ze sterownikami GPU i RDMA) dla pul kontenerów usługi Azure Batch](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-azure-batch.ubuntu-server-container-rdma?tab=Overview)

* Utwórz [niestandardowy obraz maszyny Wirtualnej systemu Windows lub Linux,](batch-sig-images.md) na którym zainstalowano sterowniki, oprogramowanie lub inne ustawienia wymagane dla rozmiaru maszyny Wirtualnej. 

* Utwórz [pakiet aplikacji](batch-application-packages.md) batch z spakowanego sterownika lub instalatora aplikacji i skonfiguruj usługę Batch, aby wdrożyć pakiet w węzłach puli i zainstalować raz podczas tworzenia każdego węzła. Jeśli na przykład pakiet aplikacji jest instalatorem, utwórz wiersz polecenia [zadania startowego,](batch-api-basics.md#start-task) aby dyskretnie zainstalować aplikację we wszystkich węzłach puli. Należy rozważyć użycie pakietu aplikacji i zadania uruchamiania puli, jeśli obciążenie zależy od określonej wersji sterownika.

  > [!NOTE] 
  > Zadanie startu musi być uruchamiane z podwyższonym poziomem uprawnień (administrator) i musi czekać na powodzenie. Długotrwałe zadania wydłużą czas aprowizowania puli partii.
  >

* [Batch Shipyard](https://github.com/Azure/batch-shipyard) automatycznie konfiguruje sterowniki GPU i RDMA do pracy w sposób przezroczysty z konteneryzowanymi obciążeniami w usłudze Azure Batch. Batch Shipyard jest w całości napędzany plikami konfiguracyjnymi. Dostępnych jest wiele przykładowych konfiguracji receptur, które umożliwiają obciążenia gpu i RDMA, takie jak [przepis gpu CNTK,](https://github.com/Azure/batch-shipyard/tree/master/recipes/CNTK-GPU-OpenMPI) który wstępnie konfiguruje sterowniki GPU na maszynach wirtualnych serii N i ładuje oprogramowanie Microsoft Cognitive Toolkit jako obraz platformy Docker.


## <a name="example-nvidia-gpu-drivers-on-windows-nc-vm-pool"></a>Przykład: sterowniki GPU NVIDIA w puli maszyn wirtualnych windows NC

Aby uruchomić aplikacje CUDA na puli węzłów Nc systemu Windows, należy zainstalować sterowniki GPU NVDIA. Poniższe przykładowe kroki używają pakietu aplikacji do zainstalowania sterowników GPU NVIDIA. Możesz wybrać tę opcję, jeśli obciążenie zależy od określonej wersji sterownika GPU.

1. Pobierz pakiet instalacyjny sterowników GPU w systemie Windows Server 2016 ze [strony internetowej firmy NVIDIA](https://www.nvidia.com/Download/index.aspx) - na przykład [w wersji 411.82](https://us.download.nvidia.com/Windows/Quadro_Certified/411.82/411.82-tesla-desktop-winserver2016-international.exe). Zapisz plik lokalnie przy użyciu krótkiej nazwy, takiej jak *GPUDriverSetup.exe*.
2. Utwórz plik zip pakietu.
3. Przekaż pakiet na swoje konto usługi Batch. Aby uzyskać kroki, zobacz wskazówki [dotyczące pakietów aplikacji.](batch-application-packages.md) Określ identyfikator aplikacji, taki jak *GPUDriver,* oraz wersję taką jak *411.82*.
1. Korzystając z interfejsów API usługi wsadowych lub witryny Azure portal, utwórz pulę w konfiguracji maszyny wirtualnej z żądaną liczbą węzłów i skalą. W poniższej tabeli przedstawiono przykładowe ustawienia instalowania sterowników gpu NVIDIA w trybie dyskretnym przy użyciu zadania początkowego:

| Ustawienie | Wartość |
| ---- | ----- | 
| **Typ obrazu** | Witryna Marketplace (Linux/Windows) |
| **Wydawca** | MicrosoftWindowsServer |
| **Oferta** | WindowsServer |
| **Numer jednostki magazynowej** | Centrum danych 2016 |
| **Rozmiar węzła** | NC6 Standard |
| **Odwołania do pakietów aplikacji** | GPUDriver, wersja 411.82 |
| **Uruchom zadanie włączone** | True<br>**Wiersz polecenia** - `cmd /c "%AZ_BATCH_APP_PACKAGE_GPUDriver#411.82%\\GPUDriverSetup.exe /s"`<br/>**Tożsamość użytkownika** — autoużytnik puli, administrator<br/>**Poczekaj na sukces** - True

## <a name="example-nvidia-gpu-drivers-on-a-linux-nc-vm-pool"></a>Przykład: sterowniki GPU NVIDIA w puli maszyn wirtualnych NC z systemem Linux

Aby uruchomić aplikacje CUDA na puli węzłów NC Linux, musisz zainstalować niezbędne sterowniki GRAFICZNE NVIDIA Tesla z zestawu narzędzi CUDA. Poniższe przykładowe kroki tworzą i wdrażają niestandardowy obraz Ubuntu 16.04 LTS ze sterownikami GPU:

1. Wdrażanie maszyny wirtualnej z serii NC platformy Azure z systemem Ubuntu 16.04 LTS. Na przykład utwórz maszynę wirtualną w regionie południowo-środkowa stanów USA. 
2. Dodaj [rozszerzenie sterowniki procesora GRAFICZNEGO NVIDIA](../virtual-machines/extensions/hpccompute-gpu-linux.md
) do maszyny Wirtualnej przy użyciu portalu Azure portal, komputera klienckiego, który łączy się z subskrypcją platformy Azure lub usługi Azure Cloud Shell. Alternatywnie wykonaj kroki, aby połączyć się z maszyną wirtualną i [ręcznie zainstalować sterowniki CUDA.](../virtual-machines/linux/n-series-driver-setup.md)
3. Wykonaj kroki, aby utworzyć [obraz galerii obrazów udostępnionych](batch-sig-images.md) dla usługi Batch.
4. Utwórz konto usługi Batch w regionie obsługującym maszyny wirtualne NC.
5. Korzystając z interfejsów API usługi wsadowych lub witryny Azure portal, utwórz pulę [przy użyciu obrazu niestandardowego](batch-sig-images.md) i z żądaną liczbą węzłów i skali. W poniższej tabeli przedstawiono przykładowe ustawienia puli obrazu:

| Ustawienie | Wartość |
| ---- | ---- |
| **Typ obrazu** | Obraz niestandardowy |
| **Obraz niestandardowy** | *Nazwa obrazu* |
| **Jednostka SKU agenta węzła** | batch.node.ubuntu 16.04 |
| **Rozmiar węzła** | NC6 Standard |

## <a name="example-microsoft-mpi-on-a-windows-h16r-vm-pool"></a>Przykład: microsoft mpi na pulę maszyn wirtualnych systemu Windows H16r

Aby uruchamiać aplikacje MPI systemu Windows w puli węzłów maszyn wirtualnych usługi Azure H16r, należy skonfigurować rozszerzenie HpcVmDrivers i zainstalować [program Microsoft MPI](https://docs.microsoft.com/message-passing-interface/microsoft-mpi). Oto przykładowe kroki wdrażania niestandardowego obrazu systemu Windows Server 2016 z niezbędnymi sterownikami i oprogramowaniem:

1. Wdrażanie maszyny Wirtualnej usługi Azure H16r z systemem Windows Server 2016. Na przykład utwórz maszynę wirtualną w regionie Zachodnia stanów USA. 
2. Dodaj rozszerzenie HpcVmDrivers do maszyny [Wirtualnej, uruchamiając polecenie programu Azure PowerShell](../virtual-machines/sizes-hpc.md) z komputera klienckiego, który łączy się z subskrypcją platformy Azure lub przy użyciu usługi Azure Cloud Shell. 
1. Nawiązuj połączenie pulpitu zdalnego z maszyną wirtualną.
1. Pobierz [pakiet instalacyjny](https://www.microsoft.com/download/details.aspx?id=57467) (MSMpiSetup.exe) dla najnowszej wersji pakietu Microsoft MPI i zainstaluj microsoft mpi.
1. Wykonaj kroki, aby utworzyć [obraz galerii obrazów udostępnionych](batch-sig-images.md) dla usługi Batch.
1. Korzystając z interfejsów API usługi wsadowych lub witryny Azure portal, utwórz pulę [przy użyciu galerii obrazów udostępnionych](batch-sig-images.md) oraz z żądaną liczbą węzłów i skali. W poniższej tabeli przedstawiono przykładowe ustawienia puli obrazu:

| Ustawienie | Wartość |
| ---- | ---- |
| **Typ obrazu** | Obraz niestandardowy |
| **Obraz niestandardowy** | *Nazwa obrazu* |
| **Jednostka SKU agenta węzła** | plik batch.node.windows amd64 |
| **Rozmiar węzła** | H16r Standard |
| **Komunikacja międzyodowa włączona** | True |
| **Maksymalna liczba zadań na węzeł** | 1 |

## <a name="example-intel-mpi-on-a-linux-h16r-vm-pool"></a>Przykład: Intel MPI w puli maszyn wirtualnych Linux H16r

Aby uruchomić aplikacje MPI na puli węzłów z serii H systemu Linux, jedną z opcji jest użycie obrazu [HPC opartego na centosze 7.4](https://azuremarketplace.microsoft.com/marketplace/apps/openlogic.centos-hpc?tab=Overview) z portalu Azure Marketplace. Sterowniki RDMA Linuksa i Intel MPI są preinstalowane. Ten obraz obsługuje również obciążeń kontenera platformy Docker.

Korzystając z interfejsów API usługi wsadowych lub witryny Azure portal, utwórz pulę przy użyciu tego obrazu i z żądaną liczbą węzłów i skali. W poniższej tabeli przedstawiono przykładowe ustawienia puli:

| Ustawienie | Wartość |
| ---- | ---- |
| **Typ obrazu** | Witryna Marketplace (Linux/Windows) |
| **Wydawca** | OpenLogic |
| **Oferta** | CentOS-HPC |
| **Numer jednostki magazynowej** | 7.4 |
| **Rozmiar węzła** | H16r Standard |
| **Komunikacja międzyodowa włączona** | True |
| **Maksymalna liczba zadań na węzeł** | 1 |

## <a name="next-steps"></a>Następne kroki

* Aby uruchomić zadania MPI w puli usługi Azure Batch, zobacz przykłady [systemu Windows](batch-mpi.md) lub [Linux.](https://blogs.technet.microsoft.com/windowshpc/2016/07/20/introducing-mpi-support-for-linux-on-azure-batch/)

* Przykłady obciążeń gpu w usadceniu można znaleźć w przepisach [stoczni wsadowej.](https://github.com/Azure/batch-shipyard/)
