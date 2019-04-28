---
title: Maszyny wirtualne platformy Azure intensywnie korzystających z obliczeń za pomocą usługi Batch | Dokumentacja firmy Microsoft
description: Jak korzystać z zalet HPC i maszyn wirtualnych GPU rozmiary w pulach usługi Azure Batch
documentationcenter: ''
author: laurenhughes
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: batch
ms.workload: big-compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/17/2018
ms.author: lahugh
ms.openlocfilehash: 3974be886b57fbf685b211369094edf844d96ab6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60776528"
---
# <a name="use-rdma-or-gpu-instances-in-batch-pools"></a>Użyj wystąpień RDMA lub procesora GPU w pulach usługi Batch

Do uruchomienia niektórych zadań wsadowych, możesz korzystać z zalet rozmiarów maszyny Wirtualnej platformy Azure, przeznaczony dla obliczeń na dużą skalę. Na przykład:

* Aby uruchomić wiele wystąpień [obciążeń MPI](batch-mpi.md), wybierz serii H lub inne rozmiary, mających interfejs sieciowy dla zdalnego bezpośredniego dostępu do pamięci (RDMA). Te rozmiary nawiązać połączenia z siecią InfiniBand dla komunikacji między węzłami, która umożliwia przyspieszanie aplikacji MPI. 

* W przypadku aplikacji CUDA wybierz rozmiary serii N, obejmujące jednostkę GPU karty GPU NVIDIA Tesla.

Ten artykuł zawiera wskazówki i przykłady do używania niektórych specjalistycznych rozmiary platformy Azure w pulach usługi Batch. Aby uzyskać dane techniczne i tło Zobacz:

* Wysoka wydajność obliczenia rozmiarów maszyn wirtualnych ([Linux](../virtual-machines/linux/sizes-hpc.md), [Windows](../virtual-machines/windows/sizes-hpc.md)) 

* Rozmiary maszyn wirtualnych z włączonymi procesorami GPU ([Linux](../virtual-machines/linux/sizes-gpu.md), [Windows](../virtual-machines/windows/sizes-gpu.md)) 

> [!NOTE]
> Niektóre rozmiary maszyn wirtualnych mogą nie być dostępne w regionach, w którym tworzenia kont usługi Batch. Aby sprawdzić, czy rozmiar jest dostępny, zobacz [dostępność produktów według regionów](https://azure.microsoft.com/regions/services/) i [wybierz rozmiar maszyny Wirtualnej dla puli usługi Batch](batch-pool-vm-sizes.md).

## <a name="dependencies"></a>Zależności

Możliwości RDMA lub procesora GPU rozmiarów obliczeniowych w usłudze Batch są obsługiwane tylko w niektórych systemach operacyjnych. (Lista obsługiwanych systemów operacyjnych jest podzbiorem obsługiwana przez maszyny wirtualne utworzone w tych rozmiarów). W zależności od sposobu tworzenia puli usługi Batch może być konieczne instalowania lub konfigurowania sterowników dodatkowe lub inne oprogramowanie na węzłach. W poniższej tabeli podsumowano te zależności. Zobacz połączonych artykułów, aby uzyskać szczegółowe informacje. Dla opcji, aby skonfigurować pule usługi Batch zobacz w dalszej części tego artykułu.

### <a name="linux-pools---virtual-machine-configuration"></a>Pule Linux — konfiguracja maszyny wirtualnej

| Rozmiar | Możliwości | Systemy operacyjne | Wymagane oprogramowanie | Ustawienia puli |
| -------- | -------- | ----- |  -------- | ----- |
| [H16r, H16mr, A8, A9](../virtual-machines/linux/sizes-hpc.md#rdma-capable-instances)<br/>[NC24r, NC24rs_v2, NC24rs_v3, ND24rs<sup>*</sup>](../virtual-machines/linux/n-series-driver-setup.md#rdma-network-connectivity) | RDMA | Ubuntu 16.04 LTS, lub<br/>HPC opartych na systemie centOS<br/>(Azure Marketplace) | Intel MPI 5<br/><br/>Sterowniki RDMA systemu Linux | Włączanie komunikacji między węzłami, wyłączanie wykonywanie zadań jednoczesnych |
| [NC, NCv2, NCv3, NDv2 serii](../virtual-machines/linux/n-series-driver-setup.md) | Procesory GPU NVIDIA Tesla GPU (różni się od serii) | Ubuntu 16.04 LTS, lub<br/>CentOS 7.3 lub wersji 7.4<br/>(Azure Marketplace) | Sterowniki NVIDIA CUDA lub CUDA Toolkit | ND | 
| [NV, NVv2 serii](../virtual-machines/linux/n-series-driver-setup.md) | NVIDIA Tesla M60 GPU | Ubuntu 16.04 LTS, lub<br/>CentOS 7.3<br/>(Azure Marketplace) | Sterowniki NVIDIA GRID | ND |

<sup>*</sup>Procesory GPU NVIDIA Tesla także obsługą dostępu RDMA rozmiary serii N

### <a name="windows-pools---virtual-machine-configuration"></a>Pule Windows — konfiguracja maszyny wirtualnej

| Rozmiar | Możliwości | Systemy operacyjne | Wymagane oprogramowanie | Ustawienia puli |
| -------- | ------ | -------- | -------- | ----- |
| [H16r, H16mr, A8, A9](../virtual-machines/windows/sizes-hpc.md#rdma-capable-instances)<br/>[NC24r, NC24rs_v2, NC24rs_v3, ND24rs<sup>*</sup>](../virtual-machines/windows/n-series-driver-setup.md#rdma-network-connectivity) | RDMA | Windows Server 2016, 2012 R2 lub<br/>2012 (Azure Marketplace) | Microsoft MPI 2012 R2 lub nowszym, lub<br/> Intel MPI 5<br/><br/>Sterowniki Windows RDMA | Włączanie komunikacji między węzłami, wyłączanie wykonywanie zadań jednoczesnych |
| [NC, NCv2, NCv3, ND, NDv2 serii](../virtual-machines/windows/n-series-driver-setup.md) | Procesory GPU NVIDIA Tesla GPU (różni się od serii) | System Windows Server 2016 lub <br/>2012 R2 (Azure Marketplace) | Sterowniki NVIDIA CUDA lub CUDA Toolkit| ND | 
| [NV, NVv2 serii](../virtual-machines/windows/n-series-driver-setup.md) | NVIDIA Tesla M60 GPU | System Windows Server 2016 lub<br/>2012 R2 (Azure Marketplace) | Sterowniki NVIDIA GRID | ND |

<sup>*</sup>Procesory GPU NVIDIA Tesla także obsługą dostępu RDMA rozmiary serii N

### <a name="windows-pools---cloud-services-configuration"></a>Pule Windows — Konfiguracja usług Cloud services

> [!NOTE]
> Rozmiary serii N są nieobsługiwane w pulach usługi Batch za pomocą konfiguracji usługi w chmurze.
>

| Rozmiar | Możliwości | Systemy operacyjne | Wymagane oprogramowanie | Ustawienia puli |
| -------- | ------- | -------- | -------- | ----- |
| [H16r, H16mr, A8, A9](../virtual-machines/windows/sizes-hpc.md#rdma-capable-instances) | RDMA | Windows Server 2016, 2012 R2, 2012 lub<br/>2008 R2 (rodzina systemów operacyjnych gościa) | Microsoft MPI 2012 R2 lub nowszym, lub<br/>Intel MPI 5<br/><br/>Sterowniki Windows RDMA | Włączanie komunikacji między węzłami,<br/> Wyłącz zadanie współbieżne wykonywanie |

## <a name="pool-configuration-options"></a>Opcje konfiguracji puli

Aby skonfigurować wyspecjalizowane rozmiar maszyny Wirtualnej w puli usługi Batch, masz kilka opcji, aby zainstalować wymagane oprogramowanie i sterowniki:

* W pulach w konfiguracji maszyny wirtualnej, wybierz wstępnie skonfigurowany [portalu Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/) obrazu maszyny Wirtualnej, sterowników i wstępnie zainstalowanym oprogramowaniem. Przykłady: 

  * [Opartych na systemie centOS 7.4 HPC](https://azuremarketplace.microsoft.com/marketplace/apps/RogueWave.CentOSbased74HPC?tab=Overview) — sterowniki RDMA i Intel MPI 5.1

  * [Maszyna wirtualna do nauki o danych](../machine-learning/data-science-virtual-machine/overview.md) dla systemu Linux lub Windows — zawiera sterowniki NVIDIA CUDA

  * Obrazy systemu Linux dla obciążeń kontenerów usługi Batch, które również zawierał żadnych sterowników procesora GPU i RDMA:

    * [CentOS (ze sterownikami procesora GPU i RDMA) dla pul kontenera usługi Azure Batch](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-azure-batch.centos-container-rdma?tab=Overview)

    * [Serwer Ubuntu (ze sterownikami procesora GPU i RDMA) dla pul kontenera usługi Azure Batch](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-azure-batch.ubuntu-server-container-rdma?tab=Overview)

* Tworzenie [niestandardowego obrazu Windows lub maszyny Wirtualnej systemu Linux](batch-custom-images.md) , na którym zainstalowano, sterowników, oprogramowania lub inne ustawienia wymagane dla rozmiaru maszyny Wirtualnej. 

* Tworzenie partii [pakiet aplikacji](batch-application-packages.md) zip sterownika lub Instalator aplikacji i skonfigurować usługę Batch wdrożenie pakietu w węzłach puli i zainstalować, gdy podczas tworzenia każdego węzła. Na przykład, jeśli pakiet aplikacji jest Instalator, Utwórz [zadanie podrzędne uruchamiania](batch-api-basics.md#start-task) wiersza polecenia do przeprowadzenia instalacji dyskretnej aplikacji we wszystkich węzłach puli. Należy wziąć pod uwagę przy użyciu pakietu aplikacji, a zadanie podrzędne uruchamiania puli, jeśli obciążenie jest zależna od wersji określonego sterownika.

  > [!NOTE] 
  > Zadanie podrzędne uruchamiania, należy uruchomić z uprawnieniami podniesionych uprawnień (Administrator), a następnie musi czekać w celu osiągnięcia sukcesu. Długotrwałych zadań spowoduje wydłużenie czasu do aprowizacji puli usługi Batch.
  >

* [Batch usługa Shipyard](https://github.com/Azure/batch-shipyard) automatycznie konfiguruje sterowniki procesora GPU i RDMA do działają w sposób przezroczysty konteneryzowanych obciążeń w usłudze Azure Batch. Usługa Batch Shipyard jest całkowicie oparte na za pomocą plików konfiguracji. Istnieje wiele przykładowych przepisu dostępne konfiguracje, które umożliwiają obsługiwanie obciążeń procesora GPU i RDMA, takie jak [przepisu GPU CNTK](https://github.com/Azure/batch-shipyard/tree/master/recipes/CNTK-GPU-OpenMPI) której wstępnie sterowników procesora GPU na maszynach wirtualnych serii N i ładuje oprogramowania Microsoft Cognitive Toolkit jako obraz platformy Docker.


## <a name="example-nvidia-gpu-drivers-on-windows-nc-vm-pool"></a>Przykład: Sterowniki procesora GPU firmy NVIDIA na pulę maszyn wirtualnych kontrolera sieci Windows

Aby uruchamiać aplikacje CUDA w puli, węzłów kontrolera sieci Windows, musisz zainstalować sterowniki procesora GPU NVDIA. Następujące przykładowe Użyj pakietu aplikacji, aby zainstalować sterowniki procesora GPU firmy NVIDIA. Możesz wybrać tę opcję, jeśli obciążenie jest zależna od określonej wersji sterowników procesora GPU.

1. Pobierz pakiet instalacyjny dla sterowników procesora GPU w systemie Windows Server 2016 z [witryny sieci Web firmy NVIDIA](https://www.nvidia.com/Download/index.aspx) — na przykład [wersji 411.82](https://us.download.nvidia.com/Windows/Quadro_Certified/411.82/411.82-tesla-desktop-winserver2016-international.exe). Zapisz plik lokalnie przy użyciu krótką nazwę, takich jak *GPUDriverSetup.exe*.
2. Utwórz plik zip pakietu.
3. Przekaż pakiet do konta usługi Batch. Aby uzyskać instrukcje, zobacz [pakiety aplikacji](batch-application-packages.md) wskazówki. Określ identyfikator aplikacji, takich jak *GPUDriver*i wersji, takich jak *411.82*.
1. Korzystając z interfejsów API usługi Batch lub witryny Azure portal, Utwórz pulę w konfiguracji maszyny wirtualnej z żądaną liczbę węzłów i skalowania. W poniższej tabeli przedstawiono przykładowe ustawienia, aby zainstalować sterowniki procesora GPU NVIDIA dyskretnie za pomocą zadania uruchamiania:

| Ustawienie | Wartość |
| ---- | ----- | 
| **Typ obrazu** | Witryna Marketplace (Linux/Windows) |
| **Wydawca** | MicrosoftWindowsServer |
| **Oferta** | WindowsServer |
| **Jednostka SKU** | 2016-Datacenter |
| **Rozmiar węzła** | NC6 Standard |
| **Odwołania do pakietu aplikacji** | GPUDriver, wersja 411.82 |
| **Zadanie podrzędne uruchamiania włączone** | True<br>**Wiersz polecenia** - `cmd /c "%AZ_BATCH_APP_PACKAGE_GPUDriver#411.82%\\GPUDriverSetup.exe /s"`<br/>**Tożsamość użytkownika** — użytkownik automatyczny: puli, administratora<br/>**Zaczekaj na powodzenie** — jest to wartość True

## <a name="example-nvidia-gpu-drivers-on-a-linux-nc-vm-pool"></a>Przykład: Sterowniki procesora GPU NVIDIA na pulę maszyn wirtualnych kontrolera sieci systemu Linux

Do uruchamiania aplikacji CUDA na pulę węzłów systemu Linux przez kontroler sieci, należy zainstalować potrzebne sterowniki procesor GPU NVIDIA Tesla z zestawu narzędzi CUDA. Następujące przykładowe tworzenie i wdrażanie niestandardowych obrazów systemu Ubuntu 16.04 LTS ze sterownikami procesora GPU:

1. Wdrażanie platformy Azure serii NC maszyny Wirtualnej z systemem Ubuntu 16.04 LTS. Na przykład utworzyć maszynę Wirtualną w regionie południowo-środkowych stanów USA. 
2. Dodaj [rozszerzenia sterowników procesora GPU NVIDIA](../virtual-machines/extensions/hpccompute-gpu-linux.md
) z maszyną wirtualną przy użyciu witryny Azure portal, komputer kliencki łączący się z subskrypcją platformy Azure lub usługi Azure Cloud Shell. Alternatywnie, wykonaj kroki, aby nawiązać połączenie z maszyną Wirtualną i [zainstalować sterowniki CUDA](../virtual-machines/linux/n-series-driver-setup.md) ręcznie.
3. Postępuj zgodnie z instrukcjami, aby utworzyć [migawki i niestandardowego obrazu maszyny Wirtualnej systemu Linux](batch-custom-images.md) dla usługi Batch.
4. Utwórz konto usługi Batch w regionie, który obsługuje maszyny wirtualne kontrolera sieci.
5. Za pomocą interfejsów API usługi Batch lub witryny Azure portal, Utwórz pulę [przy użyciu niestandardowego obrazu](batch-custom-images.md) i żądaną liczbę węzłów i skalowania. W poniższej tabeli przedstawiono przykładowe ustawienia puli dla obrazu:

| Ustawienie | Wartość |
| ---- | ---- |
| **Typ obrazu** | Obraz niestandardowy |
| **Obraz niestandardowy** | *Nazwa obrazu* |
| **Jednostkę SKU węzła agenta** | batch.node.ubuntu 16.04 |
| **Rozmiar węzła** | NC6 Standard |

## <a name="example-microsoft-mpi-on-a-windows-h16r-vm-pool"></a>Przykład: Microsoft MPI w puli maszyn wirtualnych H16r Windows

Do uruchamiania aplikacji Windows MPI w ramach puli węzłów maszyny Wirtualnej platformy Azure H16r, musisz skonfigurować rozszerzenie HpcVmDrivers i zainstalować [Microsoft MPI](https://docs.microsoft.com/message-passing-interface/microsoft-mpi). Poniżej przedstawiono przykładowe kroki, aby wdrożyć niestandardowy obraz systemu Windows Server 2016 za pomocą niezbędne sterowniki i oprogramowanie:

1. Wdróż Maszynę wirtualną platformy Azure H16r systemem Windows Server 2016. Na przykład utworzyć maszynę Wirtualną w regionie zachodnie stany USA. 
2. Dodaje do maszyny Wirtualnej przez rozszerzenie HpcVmDrivers [polecenia programu Azure PowerShell](../virtual-machines/windows/sizes-hpc.md#rdma-capable-instances
) z poziomu komputera klienckiego, który nawiązuje połączenie z subskrypcją platformy Azure lub przy użyciu usługi Azure Cloud Shell. 
1. Utwórz połączenie pulpitu zdalnego do maszyny Wirtualnej.
1. Pobierz [pakiet instalacyjny](https://www.microsoft.com/download/details.aspx?id=57467) (MSMpiSetup.exe) Aby uzyskać najnowszą wersję programu Microsoft MPI oraz instalacja Microsoft MPI.
1. Postępuj zgodnie z instrukcjami, aby utworzyć [migawki i niestandardowego obrazu maszyny Wirtualnej Windows](batch-custom-images.md) dla usługi Batch.
1. Za pomocą interfejsów API usługi Batch lub witryny Azure portal, Utwórz pulę [przy użyciu niestandardowego obrazu](batch-custom-images.md) i żądaną liczbę węzłów i skalowania. W poniższej tabeli przedstawiono przykładowe ustawienia puli dla obrazu:

| Ustawienie | Wartość |
| ---- | ---- |
| **Typ obrazu** | Obraz niestandardowy |
| **Obraz niestandardowy** | *Nazwa obrazu* |
| **Jednostkę SKU węzła agenta** | Batch.node.Windows amd64 |
| **Rozmiar węzła** | Standardowa H16r |
| **Włączono komunikację między węzłami** | True |
| **Maksymalna liczba zadań na węzeł** | 1 |

## <a name="example-intel-mpi-on-a-linux-h16r-vm-pool"></a>Przykład: Intel MPI w puli maszyn wirtualnych Linux H16r

Do uruchamiania aplikacji MPI w puli systemu Linux H-series na węzłach, jednym rozwiązaniem jest użycie [opartych na systemie CentOS 7.4 HPC](https://azuremarketplace.microsoft.com/marketplace/apps/RogueWave.CentOSbased74HPC?tab=Overview) obrazu z witryny Azure Marketplace. Zostanie wstępnie zainstalowane sterowniki RDMA systemu Linux i Intel MPI. Ten obraz obsługuje również obciążeń kontenerów platformy Docker.

Za pomocą interfejsów API usługi Batch lub witrynie Azure portal utworzyć pulę przy użyciu tego obrazu i żądaną liczbę węzłów i skalowania. W poniższej tabeli przedstawiono przykładowe ustawienia puli:

| Ustawienie | Wartość |
| ---- | ---- |
| **Typ obrazu** | Witryna Marketplace (Linux/Windows) |
| **Wydawca** | OpenLogic |
| **Oferta** | CentOS-HPC |
| **Jednostka SKU** | 7.4 |
| **Rozmiar węzła** | Standardowa H16r |
| **Włączono komunikację między węzłami** | True |
| **Maksymalna liczba zadań na węzeł** | 1 |

## <a name="next-steps"></a>Kolejne kroki

* Do uruchamiania zadań MPI w puli usługi Azure Batch, zobacz [Windows](batch-mpi.md) lub [Linux](https://blogs.technet.microsoft.com/windowshpc/2016/07/20/introducing-mpi-support-for-linux-on-azure-batch/) przykłady.

* Aby uzyskać przykłady obciążeń procesora GPU w usłudze Batch, zobacz [usługa Batch Shipyard](https://github.com/Azure/batch-shipyard/) przepisy.