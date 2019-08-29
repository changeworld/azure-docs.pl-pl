---
title: Pomoc techniczna platformy Azure dla maszyn wirtualnych 2. generacji (wersja zapoznawcza) | Microsoft Docs
description: Omówienie pomocy technicznej platformy Azure dla maszyn wirtualnych 2. generacji
services: virtual-machines-windows
documentationcenter: ''
author: laurenhughes
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 05/23/2019
ms.author: lahugh
ms.openlocfilehash: fd794662ef41112cb04bdfde087253c8abdb6983
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70079383"
---
# <a name="support-for-generation-2-vms-preview-on-azure"></a>Obsługa maszyn wirtualnych 2. generacji (wersja zapoznawcza) na platformie Azure

> [!IMPORTANT]
> Pomoc techniczna platformy Azure dla maszyn wirtualnych 2. generacji jest obecnie w wersji zapoznawczej.
> Ta wersja zapoznawcza jest świadczona bez umowy dotyczącej poziomu usług i nie jest zalecana w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [dodatkowe warunki użytkowania wersji](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)zapoznawczych Microsoft Azure.

Obsługa maszyn wirtualnych 2. generacji jest teraz dostępna w wersji zapoznawczej na platformie Azure. Nie można zmienić generacji maszyny wirtualnej po jej utworzeniu. przed wybraniem generacji zapoznaj się z uwagami na tej stronie. 

Maszyny wirtualne generacji 2 obsługują kluczowe funkcje, które nie są obsługiwane przez maszyny wirtualne generacji 1. Te funkcje obejmują zwiększoną ilość pamięci, rozszerzenia funkcji ochrony oprogramowania firmy Intel (Intel SGX) i zwirtualizowaną pamięć trwałą (vPMEM). Maszyny wirtualne generacji 2 mają również pewne funkcje, które nie są jeszcze obsługiwane na platformie Azure. Aby uzyskać więcej informacji, zobacz sekcję [funkcje i możliwości](#features-and-capabilities) .

Maszyny wirtualne generacji 2 wykorzystują nową architekturę rozruchową opartą na interfejsie UEFI zamiast architektury opartej na systemie BIOS używanej przez maszyny wirtualne generacji 1. W porównaniu z maszynami wirtualnymi generacji 1 maszyny wirtualne generacji 2 mogą mieć ulepszony czas rozruchu i instalacji. Aby zapoznać się z omówieniem maszyn wirtualnych 2. generacji i niektórych różnic między generacjami 1 i 2, zobacz temat [Tworzenie maszyny wirtualnej generacji 1 lub 2 w funkcji Hyper-V?](https://docs.microsoft.com/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v).

## <a name="generation-2-vm-sizes"></a>Rozmiary maszyn wirtualnych generacji 2

Maszyny wirtualne generacji 1 są obsługiwane przez wszystkie rozmiary maszyn wirtualnych na platformie Azure. Platforma Azure oferuje teraz obsługę wersji zapoznawczej 2 dla następującej wybranej serii maszyn wirtualnych:

* [Seria B](https://docs.microsoft.com/azure/virtual-machines/windows/b-series-burstable)
* Seria [Dsv2](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-general#dsv2-series) i [Dsv3](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-general#dsv3-series-1)
* [Seria Esv3](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-memory#esv3-series)
* [Seria Fsv2](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-compute#fsv2-series-1)
* [Seria GS](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-previous-gen#gs-series)
* Seria [ls](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-previous-gen#ls-series) i seria [Lsv2](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-storage#lsv2-series)
* [Seria Mv2](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-memory#mv2-series)
* Seria [NCv2](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-gpu#ncv2-series) i [Seria NCV3](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-gpu#ncv3-series)
* [Seria ND](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-gpu#nd-series)

## <a name="generation-2-vm-images-in-azure-marketplace"></a>Obrazy maszyn wirtualnych generacji 2 w witrynie Azure Marketplace

Maszyny wirtualne 2. generacji obsługują następujące obrazy w portalu Marketplace:

* Windows Server 2019 Datacenter
* Windows Server 2016 Datacenter
* Windows Server 2012 R2 Datacenter
* Windows Server 2012 Datacenter

## <a name="on-premises-vs-azure-generation-2-vms"></a>W środowisku lokalnym a Maszyny wirtualne generacji 2 platformy Azure

Platforma Azure nie obsługuje obecnie niektórych funkcji, które są obsługiwane przez funkcję Hyper-V w przypadku maszyn wirtualnych 2. generacji.

| Funkcja generacji 2                | Lokalna funkcja Hyper-V | Azure |
|-------------------------------------|---------------------|-------|
| Bezpieczny rozruch                         | :heavy_check_mark:  | y   |
| Chroniona maszyna wirtualna                         | :heavy_check_mark:  | y   |
| vTPM                                | :heavy_check_mark:  | y   |
| Zabezpieczenia oparte na wirtualizacji (VBS) | :heavy_check_mark:  | y   |
| Format VHDX                         | :heavy_check_mark:  | y   |

## <a name="features-and-capabilities"></a>Funkcje i możliwości

### <a name="generation-1-vs-generation-2-features"></a>Funkcje generacji 1 a generacja 2

| Cecha | Generacja 1 | Generacja 2 |
|---------|--------------|--------------|
| Rozruch             | PCAT                      | UEFI                               |
| Kontrolery dysków | IDE                       | SCSI                               |
| Rozmiary maszyn wirtualnych         | Wszystkie rozmiary maszyn wirtualnych | Tylko maszyny wirtualne obsługujące usługę Premium Storage |

### <a name="generation-1-vs-generation-2-capabilities"></a>Możliwości generacji 1 a generacja 2

| Możliwość | Generacja 1 | Generacja 2 |
|------------|--------------|--------------|
| Dysk systemu operacyjnego > 2 TB                    | y                        | :heavy_check_mark: |
| Niestandardowy dysk/obraz/system operacyjny wymiany         | :heavy_check_mark:         | :heavy_check_mark: |
| Obsługa zestawu skalowania maszyn wirtualnych | :heavy_check_mark:         | :heavy_check_mark: |
| ASR/backup                        | :heavy_check_mark:         | y                |
| Galeria obrazów udostępnionych              | :heavy_check_mark:         | y                |
| Szyfrowanie dysków Azure             | :heavy_check_mark:         | y                |

## <a name="creating-a-generation-2-vm"></a>Tworzenie maszyny wirtualnej generacji 2

### <a name="marketplace-image"></a>Obraz witryny Marketplace

W Azure Portal lub interfejsie wiersza polecenia platformy Azure można tworzyć maszyny wirtualne 2. generacji z obrazu portalu Marketplace, który obsługuje rozruch z interfejsem UEFI.

`windowsserver-gen2preview` Oferta zawiera tylko obrazy generacji 2 systemu Windows. To opakowanie pozwala uniknąć pomyłek w obrazach generacji 1 i 2. generacji. Aby utworzyć maszynę wirtualną generacji 2, wybierz pozycję **obrazy** z tej oferty i postępuj zgodnie ze standardowym procesem, aby utworzyć maszynę wirtualną.

Obecnie witryna Marketplace oferuje następujące obrazy generacji 2 systemu Windows:

* 2019 — Datacenter-Gen2
* 2016 — Datacenter-Gen2
* 2012-R2-Datacenter-Gen2
* 2012 — Datacenter-Gen2

Zapoznaj się z sekcją [funkcje i możliwości](#features-and-capabilities) , aby zapoznać się z bieżącą listą obsługiwanych obrazów z portalu Marketplace.

### <a name="managed-image-or-managed-disk"></a>Zarządzany obraz lub dysk zarządzany

Maszynę wirtualną generacji 2 można utworzyć na podstawie zarządzanego obrazu lub dysku zarządzanego w taki sam sposób jak w przypadku tworzenia maszyny wirtualnej generacji 1.

### <a name="virtual-machine-scale-sets"></a>Zestawy skalowania maszyn wirtualnych

Można również tworzyć maszyny wirtualne 2. generacji przy użyciu zestawów skalowania maszyn wirtualnych. W interfejsie wiersza polecenia platformy Azure Użyj zestawów skalowania platformy Azure, aby utworzyć maszyny wirtualne 2. generacji.

## <a name="frequently-asked-questions"></a>Często zadawane pytania

* **Czy maszyny wirtualne 2. generacji są dostępne we wszystkich regionach świadczenia usługi Azure?**  
    Tak. Ale nie wszystkie [rozmiary maszyn wirtualnych generacji 2](#generation-2-vm-sizes) są dostępne w każdym regionie. Dostępność maszyny wirtualnej generacji 2 zależy od dostępności rozmiaru maszyny wirtualnej.

* **Czy istnieją różnice cenowe między maszynami wirtualnymi generacji 1 i 2?**  
   Nie.

* **Jak mogę zwiększyć rozmiar dysku systemu operacyjnego?**  
  Dyski systemu operacyjnego o rozmiarze większym niż 2 TB są nowe do maszyn wirtualnych 2. generacji. Domyślnie dyski systemu operacyjnego są mniejsze niż 2 TB w przypadku maszyn wirtualnych 2. generacji. Można zwiększyć rozmiar dysku do zalecanej maksymalnie 4 TB. Użyj interfejsu wiersza polecenia platformy Azure lub Azure Portal, aby zwiększyć rozmiar dysku systemu operacyjnego. Aby uzyskać informacje o sposobach rozszerzania dysków, zobacz [zmiana rozmiaru dysku](expand-os-disk.md).

  Aby zwiększyć rozmiar dysku systemu operacyjnego z Azure Portal:

  1. W Azure Portal przejdź do strony właściwości maszyny wirtualnej.
  1. Aby wyłączyć i cofnąć alokację maszyny wirtualnej, wybierz przycisk **Zatrzymaj** .
  1. W sekcji **dyski** wybierz dysk systemu operacyjnego, który chcesz zwiększyć.
  1. W sekcji **dyski** wybierz pozycję **Konfiguracja**, a następnie zaktualizuj **rozmiar** do wartości, która ma zostać wybrana.
  1. Wróć do strony właściwości maszyny wirtualnej i **Uruchom** maszynę wirtualną.
  
  Może pojawić się ostrzeżenie dotyczące dysków systemu operacyjnego większych niż 2 TB. Ostrzeżenie nie dotyczy maszyn wirtualnych 2. generacji. Jednak rozmiary dysków systemu operacyjnego większe niż 4 TB *nie są zalecane.*

* **Czy maszyny wirtualne generacji 2 obsługują przyspieszone sieci?**  
    Tak. Aby uzyskać więcej informacji, zobacz [Tworzenie maszyny wirtualnej za pomocą przyspieszonej sieci](../../virtual-network/create-vm-accelerated-networking-cli.md).

* **Czy plik VHDX jest obsługiwany w generacji 2?**  
    Nie, maszyny wirtualne generacji 2 obsługują tylko dysk VHD.

* **Czy maszyny wirtualne generacji 2 obsługują platformę Azure Ultra Disk Storage?**  
    Tak.

* **Czy można migrować maszynę wirtualną z generacji 1 do generacji 2?**  
    Nie, nie można zmienić generacji maszyny wirtualnej po jej utworzeniu. Jeśli chcesz przełączać się między generacjami maszyn wirtualnych, Utwórz nową maszynę wirtualną innej generacji.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [maszynach wirtualnych 2. generacji w funkcji Hyper-V](https://docs.microsoft.com/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v).

* Dowiedz się, jak [przygotować wirtualny dysk twardy](prepare-for-upload-vhd-image.md) do przekazania z systemów lokalnych na platformę Azure.
