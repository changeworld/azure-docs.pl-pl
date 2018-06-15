---
title: Migrowanie z usług AWS i innych platform do zarządzanych dysków na platformie Azure | Dokumentacja firmy Microsoft
description: Tworzenie maszyn wirtualnych na platformie Azure za pomocą dysków VHD przekazanego z innych chmur, takich jak usług AWS lub innych platform wirtualizacji i korzystanie z dysków zarządzanych platformy Azure.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 10/07/2017
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 0440eccbdf76fc58fadf46de2508d362c0de6cc3
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
ms.locfileid: "32190789"
---
# <a name="migrate-from-amazon-web-services-aws-and-other-platforms-to-managed-disks-in-azure"></a>Migracji z usług sieci Web firmy Amazon (AWS) i innych platform do zarządzanych dysków na platformie Azure

Możesz przekazać pliki VHD z rozwiązania do wirtualizacji usług AWS lub lokalnego do platformy Azure do tworzenia maszyn wirtualnych, które korzystają z dysków zarządzanych. Azure dysków zarządzanych eliminuje konieczność zarządzania kontami magazynu dla maszyn wirtualnych IaaS platformy Azure. Należy określić tylko typ (Premium lub Standard) oraz rozmiar dysku należy i Azure tworzy i zarządza dysku. 

Możesz przekazać ogólnych i specjalnych wirtualne dyski twarde. 
- **Uogólniony wirtualny dysk twardy** -ma wszystkie informacje osobiste Konto usunięte za pomocą programu Sysprep. 
- **Specjalizowany wirtualnego dysku twardego** -przechowuje konta użytkowników, aplikacji i innych danych o stanie z oryginalnego maszyny Wirtualnej. 

> [!IMPORTANT]
> Przed przekazaniem jakiegokolwiek dysku VHD na platformę Azure, należy wykonać [Przygotowywanie wirtualnego dysku twardego Windows lub VHDX do przekazania do platformy Azure](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
>
>


| Scenariusz                                                                                                                         | Dokumentacja                                                                                                                       |
|----------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------|
| Masz istniejącego wystąpienia usług AWS EC2, które chcesz przeprowadzić migrację do maszyn wirtualnych platformy Azure przy użyciu dysków zarządzanych                              | [Przenieś Maszynę wirtualną z usług Amazon Web Services (AWS) na platformie Azure](aws-to-azure.md)                           |
| Masz Maszynę wirtualną z innej platformy wirtualizacji, który ma być używana jako obraz do tworzenia wielu maszyn wirtualnych platformy Azure. | [Przekazywanie uogólniony wirtualny dysk twardy i go użyć do utworzenia nowej maszyny Wirtualnej na platformie Azure](upload-generalized-managed.md) |
| Masz jednoznacznie dostosowane maszyny Wirtualnej, który chcesz ponownie utworzyć na platformie Azure.                                                      | [Przekazywanie specjalne wirtualnego dysku twardego na platformie Azure i utworzyć nową maszynę Wirtualną](create-vm-specialized.md)         |


## <a name="overview-of-managed-disks"></a>Omówienie dysków zarządzanych

Dyskach zarządzanych platformy Azure upraszcza zarządzanie maszyny Wirtualnej, usuwając konieczność zarządzania kontami magazynu. Zarządzany dysków również korzyści z większą niezawodność maszyn wirtualnych w zestawie dostępności. Gwarantuje, że dyski różnych maszyn wirtualnych w zestawie dostępności są wystarczająco odizolowane od siebie, aby uniknąć pojedynczego punktu awarii. Automatycznie umieszcza dysków różnych maszyn wirtualnych w zestawie dostępności w różnych jednostkach skalowania magazynu (sygnatury) ograniczająca skutków błędów jednostki skalowania magazynu w jednym spowodowany sprzętu i oprogramowania błędów. Na podstawie Twoich potrzeb, możesz wybrać z dwóch typów pamięci masowej: 
 
- [Dysków zarządzanych w warstwie Premium](premium-storage.md) nośniki zapewnia wysoką wydajność, małe opóźnienia dysku obsługę maszyn wirtualnych działających obciążeń/O wykonujących podstawie stałych dysków stanu (SSD). Zaletą szybkości i wydajności tych dysków może potrwać przy użyciu funkcji migracji do dysków zarządzanych w warstwie Premium.  

- [Dyski standardowe zarządzane](standard-storage.md) Użyj stacji dysków twardych (HDD) na podstawie nośnikach magazynowania i są najbardziej odpowiednie dla: tworzenie i testowanie i inne obciążenia rzadkim dostępu, które są mniej wrażliwe na zmienności wydajności.  

## <a name="plan-for-the-migration-to-managed-disks"></a>Planowanie migracji do zarządzanych dysków

Ta sekcja umożliwia podjęcie najlepszych decyzji w typach maszyny Wirtualnej i dysku.

Jeśli planujesz na temat migracji z dysków niezarządzanych do zarządzanych dysków, należy zwrócić uwagę przez użytkowników z [Współautor·maszyny·wirtualnej](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) rola nie będzie mógł zmienić rozmiar maszyny Wirtualnej (ponieważ mogą one wstępne konwersji). Jest to spowodowane maszyny wirtualne z dyskami zarządzanych wymagają od użytkownika posiadania uprawnienia Microsoft.Compute/disks/write na dyskach systemu operacyjnego.

### <a name="location"></a>Lokalizacja

Wybierz lokalizację, w której są dostępne dyski zarządzanych Azure. W przypadku migracji dysków zarządzanych w warstwie Premium również upewnij się, że magazyn w warstwie Premium jest dostępna w regionie, w którym jest planowana migracja do. Zobacz [regionów platformy Azure](https://azure.microsoft.com/regions/#services) aktualne informacje o dostępnych lokalizacji.

### <a name="vm-sizes"></a>Rozmiary maszyn wirtualnych

W przypadku migracji dysków zarządzanych w warstwie Premium, należy zaktualizować rozmiaru maszyny wirtualnej do magazyn w warstwie Premium obsługuje rozmiaru dostępna w regionie, w którym znajduje się maszyna wirtualna. Przejrzyj rozmiarów maszyn wirtualnych, które są funkcją Magazyn w warstwie Premium. Specyfikacje rozmiaru maszyny Wirtualnej platformy Azure są wymienione w [rozmiary maszyn wirtualnych](sizes.md).
Przejrzyj charakterystyki wydajności maszyn wirtualnych, które pracy z magazyn w warstwie Premium i wybierz najbardziej odpowiedni rozmiar maszyny Wirtualnej, który najlepiej odpowiada obciążenie. Należy upewnić się, że wystarczającą przepustowość dostępne na maszynie Wirtualnej do kierowania ruchu dysku.

### <a name="disk-sizes"></a>Rozmiary dysków

**Dysków zarządzanych w warstwie Premium**

Istnieją trzy typy dysków zarządzanych w warstwie Premium, które mogą być używane z maszyny Wirtualnej, a każdy ma określonych IOPs i przepływność limity. Należy wziąć pod uwagę następujące limity podczas wybierania typu dysku Premium dla maszyny Wirtualnej na podstawie potrzeb aplikacji pod względem wydajności, wydajności, skalowalności i ładuje godzinami szczytu.

| Typ dysków Premium  | P10               | P20               | P30               |
|---------------------|-------------------|-------------------|-------------------|
| Rozmiar dysku           | 128 GB            | 512 GB            | 1024 GB (1 TB)    |
| Liczba operacji wejścia/wyjścia na sekundę na dysk       | 500               | 2300              | 5000              |
| Przepływność na dysk | 100 MB na sekundę | 150 MB na sekundę | 200 MB / s |

**Dyski standardowe zarządzanych**

Istnieje pięć typów zarządzane standardowych dysków, które mogą być używane z maszyny Wirtualnej. Każdej z nich ma inną wydajności, ale ma tego samego IOPS i limity przepustowości. Wybierz typ dyski standardowe zarządzane na podstawie potrzeb wydajność aplikacji.

| Typ dysku standardowego  | S4               | S6               | S10              | S20              | S30              |
|---------------------|------------------|------------------|------------------|------------------|------------------|
| Rozmiar dysku           | 30 GB            | 64 GB            | 128 GB           | 512 GB           | 1024 GB (1 TB)   |
| Liczba operacji wejścia/wyjścia na sekundę na dysk       | 500              | 500              | 500              | 500              | 500              |
| Przepływność na dysk | 60 MB na sekundę | 60 MB na sekundę | 60 MB na sekundę | 60 MB na sekundę | 60 MB na sekundę |

### <a name="disk-caching-policy"></a>Dyskowej pamięci podręcznej zasad 

**Dysków zarządzanych w warstwie Premium**

Domyślnie jest dyskowej pamięci podręcznej zasad *tylko do odczytu* dla wszystkich danych dysków Premium i *odczytu i zapisu* dla dysku systemu operacyjnego Premium dołączony do maszyny Wirtualnej. To ustawienie konfiguracji jest zalecane w celu osiągnięcia optymalnej wydajności dla aplikacji systemu IOs. W przypadku dysków ciężki zapisu lub w trybie tylko do zapisu danych (takich jak pliki dziennika programu SQL Server) Wyłącz buforowanie dysku, dzięki czemu można osiągnąć lepszą wydajność aplikacji.

### <a name="pricing"></a>Cennik

Przegląd [ceny dysków zarządzanych](https://azure.microsoft.com/pricing/details/managed-disks/). Cen dysków zarządzanych w warstwie Premium jest taka sama jak niezarządzane dysków Premium. Ale ceny dyski standardowe zarządzanych jest inny niż dyski standardowe niezarządzane.


## <a name="next-steps"></a>Następne kroki

- Przed przekazaniem jakiegokolwiek dysku VHD na platformę Azure, należy wykonać [Przygotowywanie wirtualnego dysku twardego Windows lub VHDX do przekazania do platformy Azure](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
