---
title: Maszyny wirtualne generacji 2 (wersja zapoznawcza) na platformie Azure | Dokumentacja firmy Microsoft
description: Omówienie maszyn wirtualnych platformy Azure generacji 2
services: virtual-machines-linux
documentationcenter: ''
author: laurenhughes
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 05/23/2019
ms.author: lahugh
ms.openlocfilehash: 183e2144317bf3f1c9a60443d393bdcb3fd7c04a
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/30/2019
ms.locfileid: "66390563"
---
# <a name="generation-2-vms-preview-on-azure"></a>Maszyny wirtualne generacji 2 (wersja zapoznawcza) na platformie Azure

> [!IMPORTANT]
> Maszyny wirtualne generacji 2 są obecnie dostępne w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Obsługa generacji 2 maszyn wirtualnych (VM) jest teraz dostępna w publicznej wersji zapoznawczej na platformie Azure. Nie można zmienić generacji maszynę wirtualną po jej utworzeniu. Dlatego zaleca się przejrzenie zagadnienia [tutaj](https://docs.microsoft.com/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v) oraz informacje na tej stronie, przed wybraniem generacji.

2. generacji maszyn wirtualnych obsługi kluczowych funkcji, które nie są obsługiwane na maszynach wirtualnych generacji 1, takich jak: pamięć, Intel® Software Guard rozszerzenia (SGX) i wirtualnej pamięci trwałej (vPMEM). Maszyny wirtualne generacji 2 mają również niektórych funkcji, które nie są jeszcze obsługiwane na platformie Azure. Aby uzyskać więcej informacji, zobacz [funkcje i możliwości](#features-and-capabilities) sekcji.

Maszyny wirtualne generacji 2 używają cena subskrypcji nowej architektury rozruch z interfejsem UEFI architektury opartej na systemie BIOS, używane przez maszyny wirtualne generacji 1. W porównaniu do maszyn wirtualnych generacji 1 maszyny wirtualne generacji 2 mogą mieć zwiększona czasów instalacji i rozruchu. Omówienie maszyn wirtualnych generacji 2 i niektóre z najważniejszych różnic między generacji 1 i 2, zobacz [należy utworzyć maszyny wirtualnej generacji 1 lub 2 w funkcji Hyper-V?](https://docs.microsoft.com/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v).

## <a name="generation-2-vm-sizes"></a>Rozmiary maszyn wirtualnych generacji 2

Maszyny wirtualne generacji 1 są obsługiwane przez wszystkie rozmiary maszyn wirtualnych na platformie Azure. Platforma Azure oferuje teraz generacja 2 obsługę następujących wybrane maszyny Wirtualne serii w publicznej wersji zapoznawczej:

* [Dsv2](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-general#dsv2-series) i [seria Dsv3](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-general#dsv3-series-1)
* [Serii Esv3](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory#esv3-series)
* [Seria Fsv2](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-compute#fsv2-series-1)
* [Seria GS](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory#gs-series)
* [Seria ls](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-previous-gen#ls-series) i [Lsv2 serii](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-storage#lsv2-series)
* [Mv2 serii](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory#mv2-series)

## <a name="generation-2-vm-images-in-azure-marketplace"></a>Obrazy maszyn wirtualnych generacji 2 w witrynie Azure Marketplace

Maszyny wirtualne generacji 2 obsługują następujące obrazów portalu Azure Marketplace:

* Windows server 2019 Datacenter
* Windows server 2016 Datacenter
* Windows server 2012 R2 Datacenter
* Windows server 2012 Datacenter

## <a name="on-premises-vs-azure-generation-2-vms"></a>W środowisku lokalnym, a maszyny wirtualne platformy Azure generacji 2

Azure obecnie nie obsługuje niektórych funkcji, które on-premises Hyper-V obsługuje dla maszyn wirtualnych generacji 2.

| Funkcja generacji 2                | W środowisku lokalnym funkcji Hyper-V | Azure |
|-------------------------------------|---------------------|-------|
| Bezpieczny rozruch                         | :heavy_check_mark:  | : x:.   |
| Maszynę Wirtualną z osłoną                         | :heavy_check_mark:  | : x:.   |
| vTPM                                | :heavy_check_mark:  | : x:.   |
| Zabezpieczenia oparte na wirtualizacji (VBS) | :heavy_check_mark:  | : x:.   |
| VHDX format                         | :heavy_check_mark:  | : x:.   |

## <a name="features-and-capabilities"></a>Funkcje i możliwości

### <a name="generation-1-vs-generation-2-features"></a>Funkcje generacji 2 vs generacji 1

| Cecha | 1. generacji | 2. generacji |
|---------|--------------|--------------|
| Rozruch             | PCAT                      | UEFI                               |
| Kontrolery dysku | IDE                       | SCSI                               |
| Rozmiary maszyn wirtualnych         | Dostępny w wszystkich rozmiarów maszyn wirtualnych | Usługa Premium storage obsługiwane tylko maszyny wirtualne |

### <a name="generation-1-vs-generation-2-capabilities"></a>Możliwości generacja 2 vs generacji 1

| Możliwości | 1. generacji | 2. generacji |
|------------|--------------|--------------|
| > 2 TB dysku systemu operacyjnego                    | : x:.                        | :heavy_check_mark: |
| Niestandardowe wymiany dysku/obraz systemu operacyjnego.         | :heavy_check_mark:         | :heavy_check_mark: |
| Obsługa zestawu skalowania maszyn wirtualnych | :heavy_check_mark:         | :heavy_check_mark: |
| ASR/Backup                        | :heavy_check_mark:         | : x:.                |
| Galeria obrazów udostępnionych              | :heavy_check_mark:         | : x:.                |
| Usługa Azure Disk Encryption             | :heavy_check_mark:         | : x:.                |

## <a name="creating-a-generation-2-vm"></a>Tworzenie generacji 2 maszyn wirtualnych

### <a name="marketplace-image"></a>Obraz z witryny Marketplace

Maszyny wirtualne generacji 2 mogą być tworzone za pomocą obrazu portalu marketplace (który obsługuje rozruch z interfejsem UEFI) za pośrednictwem witryny Azure portal lub interfejsu wiersza polecenia platformy Azure.

`windowsserver-gen2preview` Oferta zawiera tylko obrazy generacji 2, Windows. Pozwala to uniknąć pomyłek w odniesieniu do generacji 1 vs generacji 2 obrazów. Aby utworzyć generacji 2 maszyny wirtualne, wybierz **obrazów** z tej oferty i postępuj zgodnie z standardowy proces tworzenia maszyny Wirtualnej.

Obecnie następujące generowania Windows 2 obrazy są publikowane w witrynie Azure Marketplace:

* 2019-datacenter-gen2
* 2016-datacenter-gen2
* 2012-r2-datacenter-gen2
* gen2-2012-datacenter

Zobacz sekcję możliwości dla listy obrazów z witryny marketplace obsługiwanych, jak firma Microsoft nalicza, dodając dodatkowe obrazy, które obsługują generacji 2.

### <a name="managed-image-or-managed-disk"></a>Zarządzany obraz lub dysk zarządzany

Generacji 2 maszyny wirtualne można tworzyć na podstawie obrazu zarządzanego lub dysku zarządzanego w taki sam sposób, należy utworzyć generacji 1 maszyny Wirtualnej.

### <a name="virtual-machine-scale-sets"></a>Zestawy skalowania maszyn wirtualnych

Generacji 2 maszyny wirtualne można również utworzyć przy użyciu zestawów skalowania maszyn wirtualnych. Możesz utworzyć generacji 2 maszyn wirtualnych przy użyciu zestawów skalowania maszyn wirtualnych platformy Azure za pomocą wiersza polecenia platformy Azure.

## <a name="frequently-asked-questions"></a>Często zadawane pytania

* **Czy generacji 2 maszyny wirtualne dostępne we wszystkich regionach platformy Azure?**  
    Tak; jednak nie wszystkie [rozmiarów maszyn wirtualnych generacji 2](#generation-2-vm-sizes) są dostępne w każdym regionie. Dostępność generacji 2 maszyn wirtualnych jest zależna od dostępności rozmiar maszyny Wirtualnej.

* **Brak ceny różnią się od generacji 1 i 2 maszynach wirtualnych?**  
    Nie ma różnic w zmienionych cennikach między generacji 1 i generacji 2 maszyn wirtualnych.

* **Jak zwiększyć rozmiar dysku systemu operacyjnego?**  
  Większe niż 2 TB dla dysków systemu operacyjnego są nowe do generacji 2 maszyn wirtualnych. Domyślnie większość dysków systemu operacyjnego są mniej niż 2 TB dla maszyn wirtualnych generacji 2, ale można zwiększyć rozmiar dysku zalecane maksymalnie do 4 TB. Możesz zwiększyć rozmiar dysku systemu operacyjnego za pomocą wiersza polecenia platformy Azure lub w portalu Azure. Aby uzyskać więcej informacji na temat powiększające się dyski programowo, zobacz [Zmienianie rozmiaru dysku](expand-disks.md).

  Aby zwiększyć rozmiar dysku systemu operacyjnego za pośrednictwem witryny Azure portal:

  * Przejdź do strony właściwości maszyny Wirtualnej w witrynie Azure portal.

  * Zamknij, a następnie Cofnij Przydział maszyny Wirtualnej przy użyciu **zatrzymać** przycisku.

  * W **dysków** wybierz dysk systemu operacyjnego, który chcesz zwiększyć.

  * Wybierz **konfiguracji** w **dysków** sekcji i zaktualizuj **rozmiar** na żądaną wartość.
  
  * Przejdź z powrotem do strony właściwości maszyny Wirtualnej i **Start** maszyny Wirtualnej.

  Może pojawić się ostrzeżenie, w przypadku większych niż 2 TB dla dysków systemu operacyjnego. To ostrzeżenie nie ma zastosowania do maszyn wirtualnych generacji 2; rozmiary dysków systemu operacyjnego jest większy niż 4 TB są jednak **niezalecane.**

* **Maszyny wirtualne generacji 2 obsługują przyspieszonej sieci?**  
    Tak, pomocy technicznej maszyn wirtualnych generacji 2 [Accelerated Networking](../../virtual-network/create-vm-accelerated-networking-cli.md).

* **Czy vhdx jest obsługiwany w generacji 2?**  
    Nie, tylko VHD jest obsługiwana na maszynach wirtualnych generacji 2.

* **Maszyny wirtualne generacji 2 obsługują Ultra dyski stałe stanu (SSD)?**  
    Tak, maszyny wirtualne generacji 2 obsługują Ultra dysków SSD.

* **Można przeprowadzić migrację z 1. generacji maszyn wirtualnych generacji 2?**  
    Nie, nie można zmienić generacji Maszynę wirtualną po jej utworzeniu. Jeśli trzeba przełączać się między generacji maszyny Wirtualnej, musisz utworzyć nową maszynę Wirtualną z innej generacji.

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się więcej o [generacji 2 maszyn wirtualnych funkcji Hyper-v](https://docs.microsoft.com/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v).
