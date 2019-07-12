---
title: Pomoc techniczna platformy Azure dla maszyn wirtualnych generacji 2 (wersja zapoznawcza) | Dokumentacja firmy Microsoft
description: Omówienie pomocy technicznej platformy Azure dla maszyn wirtualnych generacji 2
services: virtual-machines-linux
documentationcenter: ''
author: laurenhughes
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 05/23/2019
ms.author: lahugh
ms.openlocfilehash: 653d4baa89e28255f11df1c5e2d813d37535793a
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2019
ms.locfileid: "67667559"
---
# <a name="support-for-generation-2-vms-preview-on-azure"></a>Obsługa generacji 2 maszyny wirtualne (wersja zapoznawcza) na platformie Azure

> [!IMPORTANT]
> Pomoc techniczna platformy Azure dla maszyn wirtualnych generacji 2 jest obecnie w wersji zapoznawczej. Ta wersja zapoznawcza jest dostarczane bez umowy dotyczącej poziomu usług, a nie jest zalecane w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [dodatkowe warunki użytkowania wersji zapoznawczych usług Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). 

Obsługa generacji 2 maszyn wirtualnych (VM) jest teraz dostępna w wersji zapoznawczej na platformie Azure. Nie można zmienić generacji maszynę wirtualną po jej utworzeniu, więc zapoznaj się z uwagami na tej stronie przed wybraniem generacji. 

Maszyny wirtualne generacji 2 obsługują kluczowych funkcji, które nie są obsługiwane na maszynach wirtualnych generacji 1. Te funkcje obejmują większej ilości pamięci, rozszerzenia Guard oprogramowania firmy Intel (Intel SGX) i zwirtualizowanych pamięci trwałej (vPMEM). Maszyny wirtualne generacji 2 mają również niektórych funkcji, które nie są jeszcze obsługiwane na platformie Azure. Aby uzyskać więcej informacji, zobacz [funkcje i możliwości](#features-and-capabilities) sekcji.

Maszyny wirtualne generacji 2 użyć nowej architektury rozruch z interfejsem UEFI, zamiast architektury opartej na systemie BIOS używane przez generacji 1 maszyny wirtualne. W porównaniu do maszyn wirtualnych generacji 1 maszyny wirtualne generacji 2 mogą ulepszyła czasów instalacji i rozruchu. Omówienie maszyn wirtualnych generacji 2 i niektóre różnice między generacji 1 i 2, zobacz [należy utworzyć maszyny wirtualnej generacji 1 lub 2 w funkcji Hyper-V?](https://docs.microsoft.com/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v).

## <a name="generation-2-vm-sizes"></a>Rozmiary maszyn wirtualnych generacji 2

Maszyny wirtualne generacji 1 są obsługiwane przez wszystkie rozmiary maszyn wirtualnych na platformie Azure. Platforma Azure oferuje teraz obsługę następujące wybrane maszyny Wirtualne serii w generacji 2 (wersja zapoznawcza):

* [Seria Dsv2](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-general#dsv2-series) i [seria Dsv3](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-general#dsv3-series-1)
* [Serii Esv3](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory#esv3-series)
* [Seria Fsv2](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-compute#fsv2-series-1)
* [Seria GS](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-previous-gen#gs-series)
* [Seria ls](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-previous-gen#ls-series) i [Lsv2 serii](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-storage#lsv2-series)
* [Mv2 serii](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory#mv2-series)

## <a name="generation-2-vm-images-in-azure-marketplace"></a>Obrazy maszyn wirtualnych generacji 2 w witrynie Azure Marketplace

Maszyny wirtualne generacji 2 obsługują następujące obrazy z witryny Marketplace:

* Windows Server 2019 Datacenter
* Windows Server 2016 Datacenter
* Windows Server 2012 R2 Datacenter
* Windows Server 2012 Datacenter

## <a name="on-premises-vs-azure-generation-2-vms"></a>Vs w środowisku lokalnym. Maszyny wirtualne platformy Azure generacji 2

Platforma Azure obecnie nie obsługuje niektórych funkcji, które on-premises Hyper-V obsługuje dla maszyn wirtualnych generacji 2.

| Funkcja generacji 2                | W środowisku lokalnym funkcji Hyper-V | Azure |
|-------------------------------------|---------------------|-------|
| Bezpieczny rozruch                         | :heavy_check_mark:  | : x:.   |
| Maszynę Wirtualną z osłoną                         | :heavy_check_mark:  | : x:.   |
| vTPM                                | :heavy_check_mark:  | : x:.   |
| Zabezpieczenia oparte na wirtualizacji (VBS) | :heavy_check_mark:  | : x:.   |
| VHDX format                         | :heavy_check_mark:  | : x:.   |

## <a name="features-and-capabilities"></a>Funkcje i możliwości

### <a name="generation-1-vs-generation-2-features"></a>Generacji 1 i 2. generacji funkcji

| Cecha | 1\. generacji | 2\. generacji |
|---------|--------------|--------------|
| Rozruch             | PCAT                      | UEFI                               |
| Kontrolery dysku | IDE                       | SCSI                               |
| Rozmiary maszyn wirtualnych         | Wszystkie rozmiary maszyn wirtualnych | Tylko maszyny wirtualne obsługujące usługę premium storage |

### <a name="generation-1-vs-generation-2-capabilities"></a>Generacji 1 i generacji 2 możliwości

| Możliwość | 1\. generacji | 2\. generacji |
|------------|--------------|--------------|
| > 2 TB dysku systemu operacyjnego                    | : x:.                        | :heavy_check_mark: |
| Niestandardowe/image/wymiany dysku systemu operacyjnego         | :heavy_check_mark:         | :heavy_check_mark: |
| Obsługa zestawu skalowania maszyn wirtualnych | :heavy_check_mark:         | :heavy_check_mark: |
| ASR/backup                        | :heavy_check_mark:         | : x:.                |
| Galeria obrazów udostępnionych              | :heavy_check_mark:         | : x:.                |
| Usługa Azure disk encryption             | :heavy_check_mark:         | : x:.                |

## <a name="creating-a-generation-2-vm"></a>Tworzenie generacji 2 maszyn wirtualnych

### <a name="marketplace-image"></a>Obraz z witryny Marketplace

W witrynie Azure portal lub interfejsu wiersza polecenia platformy Azure można utworzyć generacji 2 maszyn wirtualnych z obrazu portalu Marketplace, który obsługuje rozruch z interfejsem UEFI.

`windowsserver-gen2preview` Oferta zawiera tylko obrazy generacji 2, Windows. Tego pakietu pozwala uniknąć pomylenia generacji 1 i generacji 2 obrazów. Aby utworzyć generacji 2 maszyny Wirtualnej wybierz pozycję **obrazów** z tej oferty i postępuj zgodnie z standardowy proces tworzenia maszyny Wirtualnej.

Obecnie Portal Marketplace oferuje następujące generowania Windows 2 obrazów:

* 2019-datacenter-gen2
* 2016-datacenter-gen2
* 2012-r2-datacenter-gen2
* gen2-2012-datacenter

Zobacz [funkcje i możliwości](#features-and-capabilities) sekcji do bieżącej listy obsługiwanych obrazów portalu Marketplace.

### <a name="managed-image-or-managed-disk"></a>Zarządzany obraz lub dysk zarządzany

Generacji 2 można utworzyć maszynę Wirtualną z obrazu zarządzanego lub dysku zarządzanego w taki sam sposób, należy utworzyć generacji 1 maszyny Wirtualnej.

### <a name="virtual-machine-scale-sets"></a>Zestawy skalowania maszyn wirtualnych

Można również utworzyć generacji 2 maszyny wirtualne za pomocą zestawów skalowania maszyn wirtualnych. W interfejsie wiersza polecenia platformy Azure Użyj skalowania na platformie Azure ustawia tworzenie generacji 2 maszyn wirtualnych.

## <a name="frequently-asked-questions"></a>Często zadawane pytania

* **Czy generacji 2 maszyny wirtualne dostępne we wszystkich regionach platformy Azure?**  
    Tak. Ale nie wszystkich [rozmiarów maszyn wirtualnych generacji 2](#generation-2-vm-sizes) są dostępne w każdym regionie. Dostępność generacji 2 maszyn wirtualnych zależy od dostępności rozmiar maszyny Wirtualnej.

* **Brak ceny różnią się od generacji 1 i 2 maszynach wirtualnych?**  
    Nie.

* **Jak zwiększyć rozmiar dysku systemu operacyjnego?**  
  Większe niż 2 TB dla dysków systemu operacyjnego są nowe do generacji 2 maszyn wirtualnych. Domyślnie dyski systemu operacyjnego są mniejsze niż 2 TB dla maszyn wirtualnych generacji 2. Możesz zwiększyć rozmiar dysku, zalecane maksymalnie 4 TB. Aby zwiększyć rozmiar dysku systemu operacyjnego, należy użyć wiersza polecenia platformy Azure lub w portalu Azure. Dla informacji na temat sposobu rozszerzania dysków programowo, zobacz [Zmienianie rozmiaru dysku](expand-disks.md).

  Aby zwiększyć rozmiar dysku systemu operacyjnego w witrynie Azure portal:

  1. W witrynie Azure portal przejdź do strony właściwości maszyny Wirtualnej.
  1. Zamknąć i Cofnij Przydział maszyny Wirtualnej, wybierz **zatrzymać** przycisku.
  1. W **dysków** wybierz dysk systemu operacyjnego, aby zwiększyć.
  1. W **dysków** zaznacz **konfiguracji**i zaktualizuj **rozmiar** wartość ma.
  1. Wróć do strony właściwości maszyny Wirtualnej i **Start** maszyny Wirtualnej.

  Może pojawić się ostrzeżenie, dla większych niż 2 TB dla dysków systemu operacyjnego. To ostrzeżenie nie ma zastosowania do maszyn wirtualnych generacji 2. Rozmiary dysków systemu operacyjnego jest większy niż 4 TB są jednak *niezalecane.*

* **Czy generacji 2 obsługi maszyn wirtualnych w funkcji przyspieszonej łączności sieciowej?**  
    Tak. Aby uzyskać więcej informacji, zobacz [Utwórz Maszynę wirtualną z przyspieszoną siecią](../../virtual-network/create-vm-accelerated-networking-cli.md).

* **Czy dysk VHDX jest obsługiwany w generacji 2?**  
    Nie, maszyny wirtualne generacji 2 obsługują tylko wirtualny dysk twardy.

* **Maszyny wirtualne generacji 2 obsługują Ultra usługi Azure Disk Storage?**  
    Tak.

* **Można przeprowadzić migrację maszyny Wirtualnej z generacji 1 generacji 2?**  
    Nie, nie można zmienić generację maszyny Wirtualnej, po jego utworzeniu. Jeśli trzeba przełączać się między generacji maszyny Wirtualnej, Utwórz nową maszynę Wirtualną z innej generacji.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [generacji 2 maszyn wirtualnych funkcji Hyper-v](https://docs.microsoft.com/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v).
