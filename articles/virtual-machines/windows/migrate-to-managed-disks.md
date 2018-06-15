---
title: Migrowanie maszyn wirtualnych platformy Azure do zarządzanych dysków | Dokumentacja firmy Microsoft
description: Migrowanie maszyn wirtualnych platformy Azure utworzonych przy użyciu niezarządzanych dysków na kontach magazynu do zarządzanych dysków.
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
ms.date: 01/03/2018
ms.author: cynthn
ms.openlocfilehash: d280ad1180949167bb8ebfc6b21521736db0f55d
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
ms.locfileid: "33777162"
---
# <a name="migrate-azure-vms-to-managed-disks-in-azure"></a>Migrowanie maszyn wirtualnych platformy Azure do zarządzanych dysków na platformie Azure

Dyskach zarządzanych platformy Azure upraszcza zarządzanie magazynu, usuwając konieczność oddzielnie zarządzania kontami magazynu.  Istniejące maszyny wirtualne Azure można migrować do zarządzanych dysków, aby korzystać z większą niezawodność maszyn wirtualnych w zestawie dostępności. Gwarantuje, że dyski różnych maszyn wirtualnych w zestawie dostępności jest wystarczająco odizolowane od siebie, aby uniknąć pojedynczy punkt awarii. Automatycznie umieszcza dysków różnych maszyn wirtualnych w zestawie dostępności w różnych jednostkach skalowania magazynu (sygnatury) ograniczająca skutków błędów jednostki skalowania magazynu w jednym spowodowany sprzętu i oprogramowania błędów.
Na podstawie Twoich potrzeb, możesz wybrać z dwóch typów pamięci masowej:

- [Dysków zarządzanych w warstwie Premium](premium-storage.md) nośniki, które zapewniają wysoką wydajność, małe opóźnienia dysku obsługę maszyn wirtualnych działających obciążeń/O wykonujących podstawie stałych dysków stanu (SSD). Zaletą szybkości i wydajności tych dysków może potrwać przy użyciu funkcji migracji do dysków zarządzanych w warstwie Premium.

- [Dyski standardowe zarządzane](standard-storage.md) Użyj stacji dysków twardych (HDD) na podstawie nośnikach magazynowania i są najbardziej odpowiednie dla: tworzenie i testowanie i inne obciążenia rzadkim dostępu, które są mniej wrażliwe na zmienności wydajności.

Można przeprowadzić migrację do zarządzanych dysków w następujących scenariuszach:

| Migrowanie...                                            | Łącze dokumentacji                                                                                                                                                                                                                                                                  |
|----------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Konwertuj autonomiczny maszyny wirtualne i maszyny wirtualne w zestawie do zarządzanych dysków dostępności   | [Konwertuj maszyn wirtualnych do zarządzanych dysków](convert-unmanaged-to-managed-disks.md) |
| Jednej maszyny Wirtualnej ze środowiska klasycznego do Menedżera zasobów na dyskach zarządzanych     | [Migracja jednej maszyny Wirtualnej](migrate-single-classic-to-resource-manager.md)  | 
| Wszystkie maszyny wirtualne w sieci wirtualnej ze środowiska klasycznego do Menedżera zasobów na dyskach zarządzanych     | [Migracja zasobów IaaS ze środowiska klasycznego do Menedżera zasobów](migration-classic-resource-manager-ps.md) , a następnie [Konwertuj Maszynę wirtualną z dysków niezarządzanych do zarządzanych dysków](convert-unmanaged-to-managed-disks.md) | 






## <a name="plan-for-the-conversion-to-managed-disks"></a>Plan konwersji do dysków zarządzanych

Ta sekcja umożliwia podjęcie najlepszych decyzji w typach maszyny Wirtualnej i dysku.


## <a name="location"></a>Lokalizacja

Wybierz lokalizację, w której są dostępne dyski zarządzanych Azure. Jeśli przenosisz do dysków zarządzanych w warstwie Premium, upewnij się również magazyn w warstwie Premium jest dostępna w regionie, w którym zamierzasz przenieść. Zobacz [regionów platformy Azure](https://azure.microsoft.com/regions/#services) aktualne informacje o dostępnych lokalizacji.

## <a name="vm-sizes"></a>Rozmiary maszyn wirtualnych

W przypadku migracji dysków zarządzanych w warstwie Premium, należy zaktualizować rozmiaru maszyny wirtualnej do magazyn w warstwie Premium obsługuje rozmiaru dostępna w regionie, w którym znajduje się maszyna wirtualna. Przejrzyj rozmiarów maszyn wirtualnych, które są funkcją Magazyn w warstwie Premium. Specyfikacje rozmiaru maszyny Wirtualnej platformy Azure są wymienione w [rozmiary maszyn wirtualnych](sizes.md).
Przejrzyj charakterystyki wydajności maszyn wirtualnych, które pracy z magazyn w warstwie Premium i wybierz najbardziej odpowiedni rozmiar maszyny Wirtualnej, który najlepiej odpowiada obciążenie. Należy upewnić się, że wystarczającą przepustowość dostępne na maszynie Wirtualnej do kierowania ruchu dysku.

## <a name="disk-sizes"></a>Rozmiary dysków

**Dysków zarządzanych w warstwie Premium**

Istnieje siedem typów dysków zarządzanych w warstwie premium, które mogą być używane z maszyny Wirtualnej i każdy ma określonych IOPs i przepływność limity. Wziąć pod uwagę następujące limity gdy wybieranie typu dysku Premium dla maszyny Wirtualnej na podstawie potrzeb aplikacji pod względem wydajności, wydajności, skalowalności i ładuje godzinami szczytu.

| Typ dysków Premium  | P4    | P6    | P10   | P15   | P20   | P30   | P40   | P50   | 
|---------------------|-------|-------|-------|-------|-------|-------|-------|-------|
| Rozmiar dysku           | 32 GB| 64 GB| 128 GB| 256 GB|512 GB | 1024 GB (1 TB)    | 2048 GB (2 TB)    | 4095 GB (4 TB)    | 
| Liczba operacji wejścia/wyjścia na sekundę na dysk       | 120   | 240   | 500   | 1100  |2300              | 5000              | 7500              | 7500              | 
| Przepływność na dysk | 25 MB na sekundę  | 50 MB / s  | 100 MB na sekundę | 125 MB na sekundę |150 MB na sekundę | 200 MB / s | 250 MB na sekundę | 250 MB na sekundę |

**Dyski standardowe zarządzanych**

Istnieje siedem typów dyski standardowe zarządzanych, które mogą być używane z maszyny Wirtualnej. Każdej z nich ma inną wydajności, ale ma tego samego IOPS i limity przepustowości. Wybierz typ dyski standardowe zarządzane na podstawie potrzeb wydajność aplikacji.

| Typ dysku standardowego  | S4               | S6               | S10              | S15              | S20              | S30              | S40              | S50              | 
|---------------------|------------------|------------------|------------------|------------------|------------------|------------------|------------------|------------------| 
| Rozmiar dysku           | 30 GB            | 64 GB            | 128 GB           | 256 GB           |512 GB           | 1024 GB (1 TB)   | 2048 GB (2TB)    | 4095 GB (4 TB)   | 
| Liczba operacji wejścia/wyjścia na sekundę na dysk       | 500              | 500              | 500              | 500              |500              | 500              | 500             | 500              | 
| Przepływność na dysk | 60 MB na sekundę | 60 MB na sekundę | 60 MB na sekundę | 60 MB na sekundę |60 MB na sekundę | 60 MB na sekundę | 60 MB na sekundę | 60 MB na sekundę | 

## <a name="disk-caching-policy"></a>Dyskowej pamięci podręcznej zasad

**Dysków zarządzanych w warstwie Premium**

Domyślnie jest dyskowej pamięci podręcznej zasad *tylko do odczytu* dla wszystkich danych dysków Premium i *odczytu i zapisu* dla dysku systemu operacyjnego Premium dołączony do maszyny Wirtualnej. To ustawienie konfiguracji jest zalecane w celu osiągnięcia optymalnej wydajności dla aplikacji systemu IOs. W przypadku dysków ciężki zapisu lub w trybie tylko do zapisu danych (takich jak pliki dziennika programu SQL Server) Wyłącz buforowanie dysku, dzięki czemu można osiągnąć lepszą wydajność aplikacji.

## <a name="pricing"></a>Cennik

Przegląd [ceny dysków zarządzanych](https://azure.microsoft.com/pricing/details/managed-disks/). Cen dysków zarządzanych w warstwie Premium jest taka sama jak niezarządzane dysków Premium. Ale ceny dyski standardowe zarządzanych jest inny niż dyski standardowe niezarządzane.



## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się więcej o [dysków zarządzanych](managed-disks-overview.md)
