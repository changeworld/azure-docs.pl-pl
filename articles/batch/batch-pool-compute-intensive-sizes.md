---
title: Maszyny wirtualne platformy Azure intensywnie korzystających z obliczeń za pomocą usługi Batch | Dokumentacja firmy Microsoft
description: Jak korzystać z obsługą dostępu RDMA lub włączonymi procesorami GPU rozmiarów maszyn wirtualnych w pulach usługi Azure Batch
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
ms.openlocfilehash: 6969f0c6a05ebf5b34fb746d2a83b884687ad710
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/07/2018
ms.locfileid: "51258259"
---
# <a name="use-rdma-capable-or-gpu-enabled-instances-in-batch-pools"></a>Korzystać z obsługą dostępu RDMA lub włączonymi procesorami GPU wystąpień w pulach usługi Batch

Do uruchomienia niektórych zadań wsadowych, można korzystać z zalet rozmiarów maszyny Wirtualnej platformy Azure, przeznaczony dla obliczeń na dużą skalę. Na przykład, aby uruchomić wiele wystąpień [obciążeń MPI](batch-mpi.md), możesz wybrać A8, A9, lub rozmiary serii H, które ma sieci interfejs dla zdalnego bezpośredniego dostępu do pamięci (RDMA). Te rozmiary nawiązać połączenia z siecią InfiniBand dla komunikacji między węzłami, która umożliwia przyspieszanie aplikacji MPI. W przypadku architektury CUDA aplikacji można też rozmiary serii N, obejmujące jednostkę GPU karty GPU NVIDIA Tesla.

Ten artykuł zawiera wskazówki i przykłady do używania niektórych specjalistycznych rozmiary platformy Azure w pulach usługi Batch. Aby uzyskać dane techniczne i tło Zobacz:

* Wysoka wydajność obliczenia rozmiarów maszyn wirtualnych ([Linux](../virtual-machines/linux/sizes-hpc.md), [Windows](../virtual-machines/windows/sizes-hpc.md)) 

* Rozmiary maszyn wirtualnych z włączonymi procesorami GPU ([Linux](../virtual-machines/linux/sizes-gpu.md), [Windows](../virtual-machines/windows/sizes-gpu.md)) 


## <a name="subscription-and-account-limits"></a>Limity konta i subskrypcji

* **Limity przydziału i limity** — [limit przydziału rdzeni na konto usługi Batch](batch-quota-limit.md#resource-quotas) można ograniczyć liczbę węzłów o podanej wielkości, możesz dodać do puli usługi Batch. Jesteś bardziej prawdopodobne osiągnięcie limitu przydziału w przypadku wybrania wyspecjalizowanymi funkcją RDMA, obsługujące procesor GPU lub inne rozmiary maszyn wirtualnych. 

  Ponadto korzystanie z niektórych rodzin maszyn wirtualnych na koncie usługi Batch, takie jak NCv2, NCv3 i ND, są ograniczone ze względu na ograniczoną pojemność. Użyj tych rodzin jest dostępna tylko przez żądanie zwiększenia limitu przydziału w domyślnej 0 rdzeni.  

  Jeśli to konieczne, [zażądać zwiększenia limitu przydziału](batch-quota-limit.md#increase-a-quota) bez dodatkowych opłat.

* **Dostępność w poszczególnych regionach** — intensywnych obliczeń maszyny wirtualne mogą nie być dostępne w regionach, w którym tworzenia kont usługi Batch. Aby sprawdzić, czy rozmiar jest dostępny, zobacz [dostępność produktów według regionów](https://azure.microsoft.com/regions/services/).


## <a name="dependencies"></a>Zależności

Z funkcji RDMA i procesora GPU rozmiary wystąpień intensywnie są obsługiwane tylko w niektórych systemach operacyjnych. W zależności od systemu operacyjnego może być konieczne instalowania lub konfigurowania dodatkowych sterowników lub innego oprogramowania. W poniższej tabeli podsumowano te zależności. Zobacz połączonych artykułów, aby uzyskać szczegółowe informacje. Dla opcji, aby skonfigurować pule usługi Batch zobacz w dalszej części tego artykułu.


### <a name="linux-pools---virtual-machine-configuration"></a>Pule Linux — konfiguracja maszyny wirtualnej

| Rozmiar | Możliwości | Systemy operacyjne | Wymagane oprogramowanie | Ustawienia puli |
| -------- | -------- | ----- |  -------- | ----- |
| [H16r, H16mr, A8, A9](../virtual-machines/linux/sizes-hpc.md#rdma-capable-instances) | RDMA | Ubuntu 16.04 LTS,<br/>SUSE Linux Enterprise Server 12 HPC, or<br/>HPC opartych na systemie centOS<br/>(Portal azure Marketplace) | Intel MPI 5 | Włączanie komunikacji między węzłami, wyłączanie wykonywanie zadań jednoczesnych |
| [NCv3 NC, NCv2, seria ND *](../virtual-machines/linux/n-series-driver-setup.md) | Procesory GPU NVIDIA Tesla GPU (różni się od serii) | Ubuntu 16.04 LTS,<br/>Red Hat Enterprise Linux w wersji 7.3 lub 7.4, lub<br/>CentOS 7.3 lub wersji 7.4<br/>(Portal azure Marketplace) | Sterowniki NVIDIA CUDA Toolkit | ND | 
| [Seria NV](../virtual-machines/linux/n-series-driver-setup.md) | Procesory GPU NVIDIA Tesla M60 procesora GPU | Ubuntu 16.04 LTS,<br/>Red Hat Enterprise Linux 7.3, lub<br/>CentOS 7.3<br/>(Portal azure Marketplace) | Sterowniki NVIDIA GRID | ND |

* Mogą wymagać łączności RDMA na maszynach wirtualnych z funkcją RDMA serii N [dodatkowych czynności konfiguracyjnych](../virtual-machines/linux/n-series-driver-setup.md#rdma-network-connectivity) , zależy od dystrybucji.



### <a name="windows-pools---virtual-machine-configuration"></a>Pule Windows — konfiguracja maszyny wirtualnej

| Rozmiar | Możliwości | Systemy operacyjne | Wymagane oprogramowanie | Ustawienia puli |
| -------- | ------ | -------- | -------- | ----- |
| [H16r, H16mr, A8, A9](../virtual-machines/windows/sizes-hpc.md#rdma-capable-instances) | RDMA | Windows Server 2016, 2012 R2 lub<br/>2012 (Portal azure Marketplace) | Microsoft MPI 2012 R2 lub nowszym, lub<br/> Intel MPI 5<br/><br/>Rozszerzenie maszyny Wirtualnej platformy Azure HpcVMDrivers | Włączanie komunikacji między węzłami, wyłączanie wykonywanie zadań jednoczesnych |
| [NCv3 NC, NCv2, seria ND *](../virtual-machines/windows/n-series-driver-setup.md) | Procesory GPU NVIDIA Tesla GPU (różni się od serii) | System Windows Server 2016 lub <br/>2012 R2 (Portal Azure Marketplace) | Sterowniki NVIDIA Tesla lub sterowniki CUDA Toolkit| ND | 
| [Seria NV](../virtual-machines/windows/n-series-driver-setup.md) | Procesory GPU NVIDIA Tesla M60 procesora GPU | System Windows Server 2016 lub<br/>2012 R2 (Portal Azure Marketplace) | Sterowniki NVIDIA GRID | ND |

* Łączność RDMA na maszynach wirtualnych z funkcją RDMA serii N jest obsługiwana w systemie Windows Server 2016 lub Windows Server 2012 R2 (z witryny Azure Marketplace) przy użyciu rozszerzenia HpcVMDrivers i Microsoft MPI lub Intel MPI.

### <a name="windows-pools---cloud-services-configuration"></a>Pule Windows — Konfiguracja usług Cloud services

> [!NOTE]
> Rozmiary serii N są nieobsługiwane w pulach usługi Batch za pomocą konfiguracji usługi w chmurze.
>

| Rozmiar | Możliwości | Systemy operacyjne | Wymagane oprogramowanie | Ustawienia puli |
| -------- | ------- | -------- | -------- | ----- |
| [H16r, H16mr, A8, A9](../virtual-machines/windows/sizes-hpc.md#rdma-capable-instances) | RDMA | Windows Server 2016, 2012 R2, 2012 lub<br/>2008 R2 (rodzina systemów operacyjnych gościa) | Microsoft MPI 2012 R2 lub nowszym, lub<br/>Intel MPI 5<br/><br/>Rozszerzenie maszyny Wirtualnej platformy Azure HpcVMDrivers | Włączanie komunikacji między węzłami,<br/> Wyłącz zadanie współbieżne wykonywanie |





## <a name="pool-configuration-options"></a>Opcje konfiguracji puli

Aby skonfigurować wyspecjalizowane rozmiar maszyny Wirtualnej w puli usługi Batch, narzędzia i interfejsy API usługi Batch zapewniają kilka opcji do zainstalowania wymaganego oprogramowania lub sterowników, w tym:

* [Zadanie podrzędne uruchamiania](batch-api-basics.md#start-task) — przekazywanie pakietu instalacyjnego jako plik zasobów do konta usługi Azure storage w taki sposób, w tym samym regionie co konto usługi Batch. Utwórz wiersz polecenia zadania uruchamiania do instalacji pliku zasobów w trybie dyskretnym, podczas uruchamiania puli. Więcej informacji zawiera [dokumentacja interfejsu API REST](/rest/api/batchservice/add-a-pool-to-an-account#bk_starttask).

  > [!NOTE] 
  > Zadanie podrzędne uruchamiania, należy uruchomić z uprawnieniami podniesionych uprawnień (Administrator), a następnie musi czekać w celu osiągnięcia sukcesu.
  >

* [Pakiet aplikacji](batch-application-packages.md) — dodawanie do pakietu instalacyjnego zip z kontem usługi Batch i konfigurowanie odwołania do pakietu w puli. To ustawienie zostanie przesłany i unzips pakietu we wszystkich węzłach w puli. Jeśli pakiet jest Instalator, Utwórz wiersz polecenia zadania uruchamiania do przeprowadzenia instalacji dyskretnej aplikacji we wszystkich węzłach puli. Opcjonalnie należy zainstalować pakiet, gdy zadanie jest zaplanowane do uruchomienia w węźle.

* [Obraz niestandardowy puli](batch-custom-images.md) — Tworzenie obrazu niestandardowego Windows lub maszyny Wirtualnej systemu Linux, zawierający sterowniki, oprogramowanie lub inne ustawienia wymagane dla rozmiaru maszyny Wirtualnej. 

* [Batch usługa Shipyard](https://github.com/Azure/batch-shipyard) automatycznie konfiguruje procesorów GPU i funkcję RDMA, które chcesz pracować przezroczyste konteneryzowanych obciążeń w usłudze Azure Batch. Usługa Batch Shipyard jest całkowicie oparte na za pomocą plików konfiguracji. Istnieje wiele przykładowych przepisu dostępne konfiguracje, które umożliwiają obsługiwanie obciążeń procesora GPU i RDMA, takie jak [przepisu GPU CNTK](https://github.com/Azure/batch-shipyard/tree/master/recipes/CNTK-GPU-OpenMPI) której wstępnie sterowników procesora GPU na maszynach wirtualnych serii N i ładuje oprogramowania Microsoft Cognitive Toolkit jako obraz platformy Docker.


## <a name="example-microsoft-mpi-on-an-a8-vm-pool"></a>Przykład: Microsoft MPI w puli maszyn wirtualnych A8

Do uruchamiania aplikacji Windows MPI w ramach puli węzłów A8 platformy Azure, należy zainstalować obsługiwaną implementację MPI. Poniżej przedstawiono przykładowe kroki, aby zainstalować [Microsoft MPI](https://msdn.microsoft.com/library/bb524831(v=vs.85).aspx) na Windows puli przy użyciu pakietu aplikacji usługi Batch.

1. Pobierz [pakiet instalacyjny](https://go.microsoft.com/FWLink/p/?LinkID=389556) (MSMpiSetup.exe) do najnowszej wersji programu Microsoft MPI.
2. Utwórz plik zip pakietu.
3. Przekaż pakiet do konta usługi Batch. Aby uzyskać instrukcje, zobacz [pakiety aplikacji](batch-application-packages.md) wskazówki. Określ identyfikator aplikacji, takich jak *MSMPI*i wersji, takich jak *8.1*. 
4. Korzystając z interfejsów API usługi Batch lub witryny Azure portal, Utwórz pulę w konfiguracji usług w chmurze z żądaną liczbę węzłów i skalowania. W poniższej tabeli przedstawiono przykładowe ustawienia, aby skonfigurować MPI w trybie nienadzorowanym, za pomocą zadania uruchamiania:

| Ustawienie | Wartość |
| ---- | ----- | 
| **Typ obrazu** | Cloud Services |
| **Rodzina systemów operacyjnych** | Windows Server 2012 R2 (4. Rodzina systemów operacyjnych) |
| **Rozmiar węzła** | Standardowa a8 |
| **Włączono komunikację między węzłami** | True |
| **Maksymalna liczba zadań na węzeł** | 1 |
| **Odwołania do pakietu aplikacji** | MSMPI |
| **Zadanie podrzędne uruchamiania włączone** | True<br>**Wiersz polecenia** - `"cmd /c %AZ_BATCH_APP_PACKAGE_MSMPI#8.1%\\MSMpiSetup.exe -unattend -force"`<br/>**Tożsamość użytkownika** — użytkownik automatyczny: puli, administratora<br/>**Zaczekaj na powodzenie** — jest to wartość True

## <a name="example-nvidia-tesla-drivers-on-nc-vm-pool"></a>Przykład: Sterowniki NVIDIA Tesla w puli maszyn wirtualnych kontrolera sieci

Do uruchamiania aplikacji CUDA na pulę węzłów systemu Linux przez kontroler sieci, należy zainstalować CUDA Toolkit 9.0 w węzłach. Zestaw narzędzi, instaluje niezbędne sterowniki procesor GPU NVIDIA Tesla. Poniżej przedstawiono przykładowe kroki, aby wdrożyć niestandardowy obraz Ubuntu 16.04 LTS ze sterownikami procesora GPU:

1. Wdrażanie platformy Azure serii NC maszyny Wirtualnej z systemem Ubuntu 16.04 LTS. Na przykład utworzyć maszynę Wirtualną w regionie południowo-środkowych stanów USA. Upewnij się, utworzyć maszynę Wirtualną w przypadku dysków zarządzanych.
2. Postępuj zgodnie z instrukcjami, aby nawiązać połączenie z maszyną Wirtualną i [zainstalować sterowniki CUDA](../virtual-machines/linux/n-series-driver-setup.md).
3. Anulowanie aprowizacji agenta systemu Linux, a następnie [Przechwytywanie obrazu maszyny Wirtualnej systemu Linux](../virtual-machines/linux/capture-image.md).
4. Utwórz konto usługi Batch w regionie, który obsługuje maszyny wirtualne kontrolera sieci.
5. Za pomocą interfejsów API usługi Batch lub witryny Azure portal, Utwórz pulę [przy użyciu niestandardowego obrazu](batch-custom-images.md) i żądaną liczbę węzłów i skalowania. W poniższej tabeli przedstawiono przykładowe ustawienia puli dla obrazu:

| Ustawienie | Wartość |
| ---- | ---- |
| **Typ obrazu** | Obraz niestandardowy |
| **Obraz niestandardowy** | Nazwa obrazu |
| **Jednostkę SKU węzła agenta** | batch.node.ubuntu 16.04 |
| **Rozmiar węzła** | Standardowa NC6 |



## <a name="next-steps"></a>Kolejne kroki

* Do uruchamiania zadań MPI w puli usługi Azure Batch, zobacz [Windows](batch-mpi.md) lub [Linux](https://blogs.technet.microsoft.com/windowshpc/2016/07/20/introducing-mpi-support-for-linux-on-azure-batch/) przykłady.

* Aby uzyskać przykłady obciążeń procesora GPU w usłudze Batch, zobacz [usługa Batch Shipyard](https://github.com/Azure/batch-shipyard/) przepisy.