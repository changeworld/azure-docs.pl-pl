---
title: Różnice i zagadnienia dotyczące maszyn wirtualnych w stosie Azure | Dokumentacja firmy Microsoft
description: Więcej informacji na temat różnic i zagadnienia dotyczące podczas pracy z maszynami wirtualnymi Azure stosu.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: 6613946D-114C-441A-9F74-38E35DF0A7D7
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/04/2018
ms.author: brenduns
ms.openlocfilehash: 8c9fd7d5824e5d315a7dd30e5052fe10802d197e
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
---
# <a name="considerations-for-virtual-machines-in-azure-stack"></a>Zagadnienia dotyczące maszyn wirtualnych Azure stosu

*Dotyczy: Azure stosu zintegrowanych systemów i Azure stosu Development Kit*

Maszyny wirtualne są na żądanie, skalowalnych zasobów obliczeniowych oferowane przez stos Azure. Korzystając z maszyn wirtualnych, należy zrozumieć, czy istnieją różnice między stosie Azure i funkcje, które są dostępne w systemie Azure. Ten artykuł zawiera omówienie unikatowy zagadnień dotyczących maszyn wirtualnych i jego funkcji w stosie Azure. Aby uzyskać informacje ogólne różnice między stosu Azure i usługi Azure, zobacz [kluczowe zagadnienia dotyczące](azure-stack-considerations.md) artykułu.

## <a name="cheat-sheet-virtual-machine-differences"></a>Ściągawka: różnice maszyny wirtualnej

| Cecha | Azure (globalna) | Azure Stack |
| --- | --- | --- |
| Obrazy maszyny wirtualnej | W portalu Azure Marketplace zawiera obrazy, których można użyć do utworzenia maszyny wirtualnej. Zobacz [portalu Azure Marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/category/compute?subcategories=virtual-machine-images&page=1) strony w celu wyświetlenia listy obrazów, które są dostępne w portalu Azure Marketplace. | Domyślnie nie ma żadnych obrazów dostępne w witrynie marketplace stosu Azure. Administrator chmury Azure stosu należy opublikować lub pobrać obrazów do stosu Azure marketplace, zanim użytkownicy mogą korzystać z nich. |
| Rozmiary maszyn wirtualnych | Azure obsługuje różnych rozmiarów maszyn wirtualnych. Aby dowiedzieć się więcej na temat dostępne rozmiary i opcji, zapoznaj się [rozmiary maszyn wirtualnych systemu Windows](../../virtual-machines/virtual-machines-windows-sizes.md) i [rozmiarów maszyn wirtualnych systemu Linux](../../virtual-machines/linux/sizes.md) tematów. | Stos Azure obsługuje podzbiór rozmiarów maszyn wirtualnych, które są dostępne w systemie Azure. Aby wyświetlić listę obsługiwanych rozmiarów, zobacz [rozmiarów maszyn wirtualnych](#virtual-machine-sizes) sekcji tego artykułu. |
| Przydziały maszyny wirtualnej | [Limity przydziału](../../azure-subscription-service-limits.md#service-specific-limits) są ustawiane przez firmę Microsoft | Administrator chmury Azure stosu przypisać przydziały przed oferują maszyn wirtualnych dla użytkowników. |
| Rozszerzenia maszyny wirtualnej |Azure obsługuje wiele rozszerzeń maszyny wirtualnej. Aby dowiedzieć się więcej o dostępnych rozszerzeń, zapoznaj się [rozszerzenia maszyn wirtualnych i funkcji](../../virtual-machines/windows/extensions-features.md) artykułu.| Stos Azure obsługuje podzbiór rozszerzeń, które są dostępne w systemie Azure i rozszerzenia o określonych wersji. Administrator chmury Azure stosu może zdecydować, które rozszerzenia udostępniane na rzecz własnych użytkowników. Aby wyświetlić listę obsługiwanych rozszerzeń, zobacz [rozszerzenia maszyny wirtualnej](#virtual-machine-extensions) sekcji tego artykułu. |
| Sieć maszyny wirtualnej | Publiczne adresy IP przypisane do dzierżawy maszyny wirtualnej są dostępne za pośrednictwem Internetu.<br><br><br>Maszyny wirtualne platformy Azure ma stały nazwy DNS | Publiczne adresy IP przypisane do maszyny wirtualnej dzierżawcy są dostępne tylko w środowisku Azure stosu Development Kit. Użytkownik musi mieć dostęp do zestaw deweloperski stosu Azure za pośrednictwem [RDP](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop) lub [VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn) nawiązać połączenia z maszyny wirtualnej, która jest tworzona w stosie Azure.<br><br>Maszyny wirtualne utworzone w ramach określonego wystąpienia stosu Azure mają nazw DNS na podstawie wartości, który został skonfigurowany przez administratora chmury. |
| Magazyn maszyny wirtualnej | Obsługuje [dyskach zarządzanych.](../../virtual-machines/windows/managed-disks-overview.md) | Dyski zarządzane nie są jeszcze obsługiwane w stosie Azure. |
| Wersje interfejsu API | Platforma Azure ma zawsze najnowsze wersje interfejsu API dla wszystkich funkcji maszyny wirtualnej. | Stos Azure obsługuje określonych usług platformy Azure i określonych wersji interfejsu API dla tych usług. Aby wyświetlić listę obsługiwanych wersji interfejsu API, zobacz [wersji interfejsu API](#api-versions) sekcji tego artykułu. |
|Zestawy dostępności maszyny wirtualnej|Wiele domen błędów (2 lub 3 dla regionu)<br>Wiele domen aktualizacji<br>Obsługa dysku zarządzanego|Wiele domen błędów (2 lub 3 dla regionu)<br>Wiele domen aktualizacji (maksymalnie 20)<br>Brak obsługi dysków zarządzanych|
|Zestawy skalowania maszyn wirtualnych|Obsługiwane automatycznego skalowania|Automatycznego skalowania nie jest obsługiwane.<br>Dodaj więcej wystąpień do skali ustawić za pomocą portalu, szablony usługi Resource Manager lub programu PowerShell.

## <a name="virtual-machine-sizes"></a>Rozmiary maszyn wirtualnych
Azure nakłada ograniczenia zasobów na kilka sposobów, aby uniknąć nadmierne zużycie zasobów (serwer lokalny i poziomu usług). Bez wprowadzania niektóre limity na zużycie zasobów dzierżawcy, środowisko dzierżawcy mogą występować podczas zakłócenia sąsiada overconsumes zasobów. 
- Dla sieci ruch wychodzący z maszyny Wirtualnej istnieją ograniczyć przepustowość. Włączony klawisz Caps w stosie Azure odpowiada Wersaliki na platformie Azure.  
- Dla zasobów magazynu Azure stosu implementuje limity liczby magazynu, aby uniknąć podstawowe nadmierne zużycie zasobów przez dzierżawców na potrzeby dostępu do magazynu. 
- Dla maszyn wirtualnych z wieloma dyskami dołączonych danych maksymalną przepływność każdy dysk danych poszczególnych jest 500 IOPS dla HHDs i 2300 IOPS dla dysków SSD.

Poniższa tabela zawiera listę maszyn wirtualnych, które są obsługiwane na stosie Azure wraz z ich konfiguracji:

| Typ           | Rozmiar          | Zakres obsługiwanych rozmiarów. |
| ---------------| ------------- | ------------------------ |
|Zastosowania ogólne |Podstawowa A        |[A0 - A4](azure-stack-vm-sizes.md#basic-a)                   |
|Zastosowania ogólne |Standard A     |[A0 - A7](azure-stack-vm-sizes.md#standard-a)              |
|Zastosowania ogólne |Seria D       |[D1 - D4](azure-stack-vm-sizes.md#d-series)              |
|Zastosowania ogólne |Seria Dv2     |[D1_v2 - D5_v2](azure-stack-vm-sizes.md#ds-series)        |
|Zastosowania ogólne |Seria DS      |[DS1 - DS4](azure-stack-vm-sizes.md#dv2-series)            |
|Zastosowania ogólne |Seria DSv2    |[DS1_v2 - DS5_v2](azure-stack-vm-sizes.md#dsv2-series)      |
|Optymalizacja pod kątem pamięci|Seria D       |[D11 - D14](azure-stack-vm-sizes.md#mo-d)            |
|Optymalizacja pod kątem pamięci|Seria DS      |[DS11 - DS14](azure-stack-vm-sizes.md#mo-ds)|
|Optymalizacja pod kątem pamięci|Seria Dv2     |[D11_v2 - DS14_v2](azure-stack-vm-sizes.md#mo-dv2)     |
|Optymalizacja pod kątem pamięci|Seria DSv2-  |[DS11_v2 - DS14_v2](azure-stack-vm-sizes.md#mo-dsv2)    |

Rozmiary maszyn wirtualnych i ich ilości zasobów są spójne stosu Azure i na platformie Azure. Ta spójności obejmuje ilość pamięci, liczby rdzeni i numer/rozmiaru dysków z danymi, które mogą zostać utworzone. Jednak wydajność ten sam rozmiar maszyny Wirtualnej Azure stosu zależy od podstawowej właściwości określonym środowisku Azure stosu.

## <a name="virtual-machine-extensions"></a>Rozszerzenia maszyny wirtualnej

 Stos Azure zawiera niewielki zestaw rozszerzeń. Aktualizacje i dodatkowe rozszerzenia i są dostępne za pośrednictwem portalu Marketplace zespolonego.

Użyj następującego skryptu programu PowerShell można pobrać listy rozszerzeń maszyny wirtualnej, które są dostępne w środowisku Azure stosu:

```powershell
Get-AzureRmVmImagePublisher -Location local | `
  Get-AzureRmVMExtensionImageType | `
  Get-AzureRmVMExtensionImage | `
  Select Type, Version | `
  Format-Table -Property * -AutoSize
```

## <a name="api-versions"></a>Wersje interfejsu API

Funkcje maszyny wirtualnej w stosie Azure obsługuje następujące wersje interfejsu API:

![Typy zasobów maszyny Wirtualnej](media/azure-stack-vm-considerations/vm-resoource-types.png)

Poniższy skrypt programu PowerShell umożliwia pobieranie wersji interfejsu API dla funkcji maszyny wirtualnej, które są dostępne w środowisku Azure stosu:

```powershell
Get-AzureRmResourceProvider | `
  Select ProviderNamespace -Expand ResourceTypes | `
  Select * -Expand ApiVersions | `
  Select ProviderNamespace, ResourceTypeName, @{Name="ApiVersion"; Expression={$_}} | `
  where-Object {$_.ProviderNamespace -like “Microsoft.compute”}
```
Lista obsługiwane typy zasobów i wersje interfejsu API może się różnić, jeśli operatorowi chmury aktualizacji środowiska Azure stosu do nowszej wersji.

## <a name="windows-activation"></a>Aktywacja systemu Windows

Produkty Windows musi być używany zgodnie z prawami używania produktu i postanowienia licencyjne firmy Microsoft. Używa stosu Azure [automatyczna aktywacja maszyny Wirtualnej](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn303421(v%3dws.11)) (AVMA), aby aktywować maszynach wirtualnych systemu Windows Server (VM). 
 - Ponieważ hosta Azure stosu aktywuje z klucze AVMA dla systemu Windows Server 2016, wszystkich maszyn wirtualnych który systemem Windows Server 2012 lub później są automatycznie aktywowane.
 - Maszyny wirtualne, które uruchamiania systemu Windows Server 2008 R2 nie są automatycznie aktywowane i musi zostać aktywowana przy użyciu [klucza MAK](https://technet.microsoft.com/library/ff793438.aspx). 

Microsoft Azure można aktywować maszyn wirtualnych systemu Windows przy użyciu aktywacji usługi KMS. Jeśli przenosisz Maszynę wirtualną z stosu Azure na platformie Azure oraz podczas potyczki aktywować problemów, zobacz [Rozwiązywanie problemów z systemu Windows Azure maszyny wirtualnej aktywacji problemów](https://docs.microsoft.com/azure/virtual-machines/windows/troubleshoot-activation-problems). Dodatkowe informacje można znaleźć w folderze [błędy aktywacji Rozwiązywanie problemów z systemu Windows na maszynach wirtualnych Azure](https://blogs.msdn.microsoft.com/mast/2017/06/14/troubleshooting-windows-activation-failures-on-azure-vms/) post na blogu zespołu pomocy technicznej platformy Azure.




## <a name="next-steps"></a>Kolejne kroki

[Utwórz maszynę wirtualną z systemem Windows przy użyciu programu PowerShell w stosie Azure](azure-stack-quick-create-vm-windows-powershell.md)
