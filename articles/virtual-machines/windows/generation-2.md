---
title: Pomoc techniczna platformy Azure dla maszyn wirtualnych 2. generacji
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
ms.date: 12/03/2019
ms.author: lahugh
ms.openlocfilehash: 6f03826bf0b82150fa89ad6e17cbcb76f98bb835
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/03/2019
ms.locfileid: "74790037"
---
# <a name="support-for-generation-2-vms-on-azure"></a>Obsługa maszyn wirtualnych 2. generacji na platformie Azure

Obsługa maszyn wirtualnych 2. generacji jest teraz dostępna na platformie Azure. Nie można zmienić generacji maszyny wirtualnej po jej utworzeniu. przed wybraniem generacji zapoznaj się z uwagami na tej stronie.

Maszyny wirtualne generacji 2 obsługują kluczowe funkcje, które nie są obsługiwane przez maszyny wirtualne generacji 1. Te funkcje obejmują zwiększoną ilość pamięci, rozszerzenia funkcji ochrony oprogramowania firmy Intel (Intel SGX) i zwirtualizowaną pamięć trwałą (vPMEM). Maszyny wirtualne 2. generacji działające lokalnie mają pewne funkcje, które nie są jeszcze obsługiwane na platformie Azure. Aby uzyskać więcej informacji, zobacz sekcję [funkcje i możliwości](#features-and-capabilities) .

Maszyny wirtualne generacji 2 wykorzystują nową architekturę rozruchową opartą na interfejsie UEFI zamiast architektury opartej na systemie BIOS używanej przez maszyny wirtualne generacji 1. W porównaniu z maszynami wirtualnymi generacji 1 maszyny wirtualne generacji 2 mogą mieć ulepszony czas rozruchu i instalacji. Aby zapoznać się z omówieniem maszyn wirtualnych 2. generacji i niektórych różnic między generacjami 1 i 2, zobacz temat [Tworzenie maszyny wirtualnej generacji 1 lub 2 w funkcji Hyper-V?](https://docs.microsoft.com/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v).

## <a name="generation-2-vm-sizes"></a>Rozmiary maszyn wirtualnych generacji 2

Maszyny wirtualne generacji 1 są obsługiwane przez wszystkie rozmiary maszyn wirtualnych na platformie Azure. Platforma Azure oferuje teraz obsługę generacji 2 dla następującej wybranej serii maszyn wirtualnych:

* [Seria B](https://docs.microsoft.com/azure/virtual-machines/windows/b-series-burstable)
* [Seria DC](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-general#dc-series)
* Seria [Dsv2](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-general#dsv2-series) i [Dsv3](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-general#dsv3-series-1)
* [Seria Esv3](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-memory#esv3-series)
* [Seria Fsv2](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-compute#fsv2-series-1)
* [Seria GS](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-previous-gen#gs-series)
* [HB — seria](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-hpc#hb-series)
* [Seria HC](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-hpc#hc-series)
* Seria [ls](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-previous-gen#ls-series) i seria [Lsv2](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-storage#lsv2-series)
* [Seria Mv2](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-memory#mv2-series)
* Seria [NCv2](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-gpu#ncv2-series) i [Seria NCV3](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-gpu#ncv3-series)
* [Seria ND](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-gpu#nd-series)
* [Seria NVv3](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-gpu#nvv3-series--1)

> [!NOTE]
> Użycie obrazów maszyn wirtualnych generacji 2 dla maszyn wirtualnych z serii Mv2 jest ogólnie dostępne, ponieważ seria Mv2 współpracuje wyłącznie z obrazami maszyny wirtualnej generacji 2. Obrazy maszyn wirtualnych generacji 1 nie są obsługiwane na maszynach wirtualnych z serii Mv2. 

## <a name="generation-2-vm-images-in-azure-marketplace"></a>Obrazy maszyn wirtualnych generacji 2 w witrynie Azure Marketplace

Maszyny wirtualne 2. generacji obsługują następujące obrazy w portalu Marketplace:

* Windows Server 2019, 2016, 2012 R2, 2012
* Windows 10
* SUSE Linux Enterprise Server 15 SP1
* SUSE Linux Enterprise Server 12 SP4
* Ubuntu Server 16,04, 18,04, 19,04, 19,10 
* RHEL 8,0

## <a name="on-premises-vs-azure-generation-2-vms"></a>Lokalna a maszyny wirtualne 2. generacji platformy Azure

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

| Funkcja | Generacja 1 | Generacja 2 |
|---------|--------------|--------------|
| Rozruch             | PCAT                      | UEFI                               |
| Kontrolery dysków | IDE                       | SCSI                               |
| Rozmiary maszyn wirtualnych         | Wszystkie rozmiary maszyn wirtualnych | Tylko maszyny wirtualne obsługujące usługę Premium Storage |

### <a name="generation-1-vs-generation-2-capabilities"></a>Możliwości generacji 1 a generacja 2

| Możliwość | Generacja 1 | Generacja 2 |
|------------|--------------|--------------|
| Dysk systemu operacyjnego > 2 TB                    | y                | :heavy_check_mark: |
| Niestandardowy dysk/obraz/system operacyjny wymiany         | :heavy_check_mark: | :heavy_check_mark: |
| Obsługa zestawu skalowania maszyn wirtualnych | :heavy_check_mark: | :heavy_check_mark: |
| Azure Site Recovery               | :heavy_check_mark: | :heavy_check_mark: |
| Tworzenie kopii zapasowej/przywracanie                    | :heavy_check_mark: | :heavy_check_mark: |
| Galeria obrazów udostępnionych              | :heavy_check_mark: | :heavy_check_mark: |
| Szyfrowanie dysków Azure             | :heavy_check_mark: | y                |

## <a name="creating-a-generation-2-vm"></a>Tworzenie maszyny wirtualnej generacji 2

### <a name="marketplace-image"></a>Obraz witryny Marketplace

W Azure Portal lub interfejsie wiersza polecenia platformy Azure można tworzyć maszyny wirtualne 2. generacji z obrazu portalu Marketplace, który obsługuje rozruch z interfejsem UEFI.

#### <a name="azure-portal"></a>Azure Portal

Obrazy generacji 2 dla systemu Windows i SLES są zawarte w tej samej ofercie serwera co obrazy Gen1. Co to znaczy z perspektywy przepływu, należy wybrać ofertę i jednostkę SKU z portalu dla maszyny wirtualnej. Jeśli jednostka SKU obsługuje zarówno obrazy generacji 1, jak i 2, możesz wybrać opcję utworzenia maszyny wirtualnej generacji 2 z karty *Zaawansowane* w przepływie tworzenia maszyny wirtualnej.

Obecnie następujące jednostki SKU obsługują obrazy generacji 1 i 2. generacji:

* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016
* Windows Server 2019

Po wybraniu jednostki SKU systemu Windows Server jako oferty na karcie **Zaawansowane** można utworzyć maszynę wirtualną **generacji 1** (BIOS) lub **Gen 2** (UEFI). W przypadku wybrania **generacji 2**upewnij się, że rozmiar maszyny wirtualnej wybrany na karcie **podstawy** jest [obsługiwany w przypadku maszyn wirtualnych 2. generacji](#generation-2-vm-sizes).

![Wybierz maszynę wirtualną generacji 1 lub Gen 2](./media/generation-2/gen1-gen2-select.png)

#### <a name="powershell"></a>PowerShell

Możesz również użyć programu PowerShell do utworzenia maszyny wirtualnej, bezpośrednio odwołującej się do jednostki SKU generacji 1 lub 2.

Na przykład użyj następującego polecenia cmdlet programu PowerShell, aby wyświetlić listę jednostek SKU w ofercie `WindowsServer`.

```powershell
Get-AzVMImageSku -Location westus2 -PublisherName MicrosoftWindowsServer -Offer WindowsServer
```

Jeśli tworzysz maszynę wirtualną z systemem Windows Server 2012 jako system operacyjny, wybierz jednostkę SKU maszyny wirtualnej generacji 1 (BIOS) lub 2 (UEFI), która wygląda następująco:

```powershell
2012-Datacenter
2012-datacenter-gensecond
```

Zapoznaj się z sekcją [funkcje i możliwości](#features-and-capabilities) , aby zapoznać się z bieżącą listą obsługiwanych obrazów z portalu Marketplace.

### <a name="managed-image-or-managed-disk"></a>Zarządzany obraz lub dysk zarządzany

Maszynę wirtualną generacji 2 można utworzyć na podstawie zarządzanego obrazu lub dysku zarządzanego w taki sam sposób jak w przypadku tworzenia maszyny wirtualnej generacji 1.

### <a name="virtual-machine-scale-sets"></a>Virtual Machine Scale Sets

Można również tworzyć maszyny wirtualne 2. generacji przy użyciu zestawów skalowania maszyn wirtualnych. W interfejsie wiersza polecenia platformy Azure Użyj zestawów skalowania platformy Azure, aby utworzyć maszyny wirtualne 2. generacji.

## <a name="frequently-asked-questions"></a>Często zadawane pytania

* **Czy maszyny wirtualne 2. generacji są dostępne we wszystkich regionach świadczenia usługi Azure?**  
    Tak. Ale nie wszystkie [rozmiary maszyn wirtualnych generacji 2](#generation-2-vm-sizes) są dostępne w każdym regionie. Dostępność maszyny wirtualnej generacji 2 zależy od dostępności rozmiaru maszyny wirtualnej.

* **Czy istnieją różnice cenowe między maszynami wirtualnymi generacji 1 i 2?**  
   Nie.

* **Mam plik VHD z lokalnej maszyny wirtualnej generacji 2. Czy można użyć tego pliku VHD do utworzenia maszyny wirtualnej generacji 2 na platformie Azure?**
  Tak, możesz przenieść plik VHD 2. generacji na platformę Azure i użyć go do utworzenia maszyny wirtualnej generacji 2. Wykonaj następujące kroki, aby to zrobić:
    1. Przekaż plik VHD do konta magazynu w tym samym regionie, w którym chcesz utworzyć maszynę wirtualną.
    1. Utwórz dysk zarządzany na podstawie pliku VHD. Ustaw właściwość generacja funkcji Hyper-V na v2. Następujące polecenia programu PowerShell ustawiają Właściwość generowania funkcji Hyper-V podczas tworzenia dysku zarządzanego.

        ```powershell
        $sourceUri = 'https://xyzstorage.blob.core.windows.net/vhd/abcd.vhd'. #<Provide location to your uploaded .vhd file>
        $osDiskName = 'gen2Diskfrmgenvhd'  #<Provide a name for your disk>
        $diskconfig = New-AzDiskConfig -Location '<location>' -DiskSizeGB 127 -AccountType Standard_LRS -OsType Windows -HyperVGeneration "V2" -SourceUri $sourceUri -CreateOption 'Import'
        New-AzDisk -DiskName $osDiskName -ResourceGroupName '<Your Resource Group>' -Disk $diskconfig
        ```

    1. Po udostępnieniu dysku utwórz maszynę wirtualną, dołączając ten dysk. Utworzona maszyna wirtualna będzie maszyną wirtualną 2. generacji.
    Po utworzeniu maszyny wirtualnej generacji 2 można opcjonalnie uogólnić obraz tej maszyny wirtualnej. Uogólnienie obrazu pozwala na utworzenie wielu maszyn wirtualnych.

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
