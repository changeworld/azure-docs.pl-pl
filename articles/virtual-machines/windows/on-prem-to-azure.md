---
title: Migrowanie z usług AWS i innych platform do usługi Managed Disks na platformie Azure | Dokumentacja firmy Microsoft
description: Tworzenie maszyn wirtualnych na platformie Azure przy użyciu wirtualnych dysków twardych przekazany z innych chmur, takich jak usługi AWS lub innych platform wirtualizacji i korzystać z zalet usługi Azure Managed Disks.
services: virtual-machines-windows
documentationcenter: ''
author: roygara
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 10/07/2017
ms.author: rogarana
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 42ad7bc10cb7b93bd4db9260f950ae4ca12aba44
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61126899"
---
# <a name="migrate-from-amazon-web-services-aws-and-other-platforms-to-managed-disks-in-azure"></a>Migrowanie z usług Amazon Web Services (AWS) i innych platform do usługi Managed Disks na platformie Azure

Możesz przekazać pliki VHD z rozwiązania do wirtualizacji usług AWS lub lokalnych na platformę Azure do tworzenia maszyn wirtualnych, które korzystają z dysków zarządzanych. Usługa Azure Managed Disks eliminuje konieczność zarządzania kontami magazynu maszyn wirtualnych IaaS platformy Azure. Należy tylko określić typ (Premium lub standardowa) i rozmiar dysku należy i platforma Azure utworzy i zarządza dysku. 

Możesz przekazać uogólniony, wyspecjalizowana wirtualnych dysków twardych. 
- **Uogólniony wirtualny dysk twardy** -miał wszystkie informacje osobiste Konto usunięte za pomocą programu Sysprep. 
- **Wyspecjalizowane wirtualnego dysku twardego** — przechowuje konta użytkowników, aplikacji i innych danych o stanie z oryginalną maszynę Wirtualną. 

> [!IMPORTANT]
> Przed przekazaniem jakiegokolwiek dysku VHD na platformie Azure, należy przestrzegać [przygotowanie Windows dysku VHD lub VHDX można przekazać na platformę Azure](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
>
>


| Scenariusz                                                                                                                         | Dokumentacja                                                                                                                       |
|----------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------|
| Masz istniejące wystąpienia usługi EC2 usług AWS, które chcesz przeprowadzić migrację do maszyn wirtualnych platformy Azure, które korzystają z dysków zarządzanych                              | [Przenoszenie maszyny Wirtualnej z usług Amazon Web Services (AWS) na platformie Azure](aws-to-azure.md)                           |
| Masz maszynę Wirtualną z innej platformy wirtualizacji, który chcesz użyć jako obraz do tworzenia wielu maszyn wirtualnych platformy Azure. | [Przekazywanie uogólnionego wirtualnego dysku twardego i użyć go do utworzenia nowej maszyny Wirtualnej na platformie Azure](upload-generalized-managed.md) |
| Masz jednoznacznie dostosowane maszynę Wirtualną, który chcesz ponownie utworzyć na platformie Azure.                                                      | [Przekazywanie wyspecjalizowanego wirtualnego dysku twardego do systemu Azure i Utwórz nową maszynę Wirtualną](create-vm-specialized.md)         |


## <a name="overview-of-managed-disks"></a>Omówienie dysków zarządzanych

Usługa Azure Managed Disks upraszcza zarządzanie maszyną Wirtualną, usuwając konieczność zarządzania kontami magazynu. Usługa Managed Disks również korzyści z większą niezawodność, maszyn wirtualnych w zestawie dostępności. Zapewnia, że dyski różnych maszyn wirtualnych w zestawie dostępności są wystarczająco odizolowane od siebie, aby uniknąć pojedynczego punktu awarii. Automatycznie przełącza dyski różnych maszyn wirtualnych w zestawie dostępności w różnych jednostkach skalowania magazynu (sygnatury) co ogranicza wpływ błędy jednostki skali magazynu w jednym spowodowany sprzętu i oprogramowania błędów.
Zgodnie z potrzebami, można wybierać spośród czterech typy opcji magazynu. Informacje na temat typów dostępnego miejsca na dysku znajdują się w artykule naszych [wybierz typ dysku](disks-types.md).

## <a name="plan-for-the-migration-to-managed-disks"></a>Planowanie migracji do usługi Managed Disks

Ta sekcja ułatwia najlepszych decyzji o typach maszyn wirtualnych i dysków.

Jeśli planowane jest na temat migracji z dysków niezarządzanych do dysków zarządzanych, należy mieć świadomość, że użytkownicy z [Współautor maszyny wirtualnej](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) roli nie będzie można zmienić rozmiar maszyny Wirtualnej (tak jak byłoby to możliwe wstępne konwersji). Jest to spowodowane maszyny wirtualne z dyskami zarządzanymi wymagają użytkownik musi mieć uprawnienie Microsoft.Compute/disks/write na dyskach systemu operacyjnego.

### <a name="location"></a>Lokalizacja

Wybierz lokalizację, w której są dostępne usługi Azure Managed Disks. Jeśli użytkownik migruje do usługi Premium Managed Disks, upewnij się również Premium storage jest dostępna w regionie, w którym jest planowana migracja do. Zobacz [usług platformy Azure według regionu](https://azure.microsoft.com/regions/#services) dla aktualnych informacji o dostępnych lokalizacji.

### <a name="vm-sizes"></a>Rozmiary maszyn wirtualnych

Jeśli użytkownik migruje do usługi Premium Managed Disks, musi być aktualizowana rozmiar maszyny Wirtualnej do magazynu w warstwie Premium stanie rozmiaru dostępne w regionie, w którym znajduje się maszyna wirtualna. Przejrzyj rozmiarów maszyn wirtualnych, które są zdolne do magazynu w warstwie Premium. Specyfikacje rozmiaru maszyny Wirtualnej platformy Azure są wymienione w [rozmiary maszyn wirtualnych](sizes.md).
Sprawdź charakterystyki wydajności maszyn wirtualnych, które współpracują z magazynu w warstwie Premium i wybierz odpowiedni rozmiar maszyny Wirtualnej, najlepiej pasujące do obciążenia. Upewnij się, że jest dostępna wystarczająca przepustowość na maszynie Wirtualnej do kierowania ruchu dysku.

### <a name="disk-sizes"></a>Rozmiary dysków

**Premium Managed Disks**

Istnieje siedem typów dysków zarządzanych w warstwie premium, które mogą być używane z maszyny Wirtualnej i każdy z nich ma określone operacje We/Wy i przepływność limitów. Wziąć pod uwagę te limity Wybieranie typu dysku Premium dla swojej maszyny Wirtualnej odpowiednio do potrzeb aplikacji pod względem wydajności, wydajność, skalowalność, gdy ładuje szczytowego.

| Typ magazynu dysków Premium  | P4    | P6    | P10   | P15   | P20   | P30   | P40   | P50   | 
|---------------------|-------|-------|-------|-------|-------|-------|-------|-------|
| Rozmiar dysku           | 32 GB| 64 GB| 128 GB| 256 GB|512 GB | 1024 GB (1 TB)    | 2048 GB (2 TB)    | 4095 GB (4 TB)    | 
| Liczba operacji wejścia/wyjścia na sekundę na dysk       | 120   | 240   | 500   | 1100  |2300              | 5000              | 7500              | 7500              | 
| Przepływność na dysk | 25 MB na sekundę  | 50 MB na sekundę  | 100 MB na sekundę | 125 MB na sekundę |150 MB na sekundę | 200 MB na sekundę | 250 MB na sekundę | 250 MB na sekundę |

**Dyski zarządzane w warstwie standardowa**

Istnieje siedem Typy standardowe dyski zarządzane, które mogą być używane z maszyny Wirtualnej. Każdy z nich mają innej pojemności, ale mają limity przepływności i tej samej operacji We/Wy. Wybierz typ Standardowy Managed disks, które są oparte na potrzeby aplikacji związane z pojemnością.

| Typ dysku standardowego  | S4               | S6               | S10              | S15              | S20              | S30              | S40              | S50              | 
|---------------------|------------------|------------------|------------------|------------------|------------------|------------------|------------------|------------------| 
| Rozmiar dysku           | 30 GB            | 64 GB            | 128 GB           | 256 GB           |512 GB           | 1024 GB (1 TB)   | 2048 GB (2TB)    | 4095 GB (4 TB)   | 
| Liczba operacji wejścia/wyjścia na sekundę na dysk       | 500              | 500              | 500              | 500              |500              | 500              | 500             | 500              | 
| Przepływność na dysk | 60 MB na sekundę | 60 MB na sekundę | 60 MB na sekundę | 60 MB na sekundę |60 MB na sekundę | 60 MB na sekundę | 60 MB na sekundę | 60 MB na sekundę | 

### <a name="disk-caching-policy"></a>Zasady buforowania dysku 

**Premium Managed Disks**

Domyślnie zasady buforowania dysku jest *tylko do odczytu* wszystkich dysków w warstwie Premium danych, a *odczytu i zapisu* dla dysku systemu operacyjnego w warstwie Premium dołączonych do maszyny Wirtualnej. To ustawienie konfiguracji jest zalecane, aby osiągnąć optymalną wydajność dla aplikacji systemu IOs. Dla dysków z danymi zapisu przy odczycie czy tylko do zapisu (takich jak pliki dziennika programu SQL Server) należy wyłączyć buforowanie dysku, dzięki czemu można osiągnąć lepszą wydajność aplikacji.

### <a name="pricing"></a>Cennik

Przegląd [cennika usługi Managed Disks](https://azure.microsoft.com/pricing/details/managed-disks/). Cennik dysków Premium Managed Disks jest taka sama jak dysków niezarządzanych w warstwie Premium. Jednak ceny dysków zarządzanych w warstwie standardowa jest inne niż standardowe dyski niezarządzane.


## <a name="next-steps"></a>Następne kroki

- Przed przekazaniem jakiegokolwiek dysku VHD na platformie Azure, należy przestrzegać [przygotowanie Windows dysku VHD lub VHDX można przekazać na platformę Azure](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
