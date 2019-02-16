---
title: Migrowanie maszyn wirtualnych platformy Azure do usługi Managed Disks | Dokumentacja firmy Microsoft
description: Migrowanie maszyn wirtualnych platformy Azure utworzone przy użyciu dysków niezarządzanych na kontach magazynu, aby korzystać z dysków zarządzanych.
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
ms.subservice: disks
ms.openlocfilehash: e9c3e10f9b48bfe2efa5396c9e64d3d87be3d826
ms.sourcegitcommit: d2329d88f5ecabbe3e6da8a820faba9b26cb8a02
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/16/2019
ms.locfileid: "56330651"
---
# <a name="migrate-azure-vms-to-managed-disks-in-azure"></a>Migrowanie maszyn wirtualnych platformy Azure do usługi Managed Disks na platformie Azure

Usługa Azure Managed Disks upraszcza zarządzanie magazynu, usuwając konieczność oddzielnie Zarządzanie kontami magazynu.  Możesz również migrować istniejące maszyny wirtualne platformy Azure do usługi Managed Disks, aby korzystać z większą niezawodność, maszyn wirtualnych w zestawie dostępności. Zapewnia, że dyski różnych maszyn wirtualnych w zestawie dostępności są wystarczająco odizolowane od siebie, aby uniknąć pojedynczego punktu awarii. Automatycznie przełącza dyski różnych maszyn wirtualnych w zestawie dostępności w różnych jednostkach skalowania magazynu (sygnatury) co ogranicza wpływ błędy jednostki skali magazynu w jednym spowodowany sprzętu i oprogramowania błędów.
Zgodnie z potrzebami, można wybierać spośród czterech typy opcji magazynu. Informacje na temat typów dostępnego miejsca na dysku znajdują się w artykule naszych [wybierz typ dysku](disks-types.md)

Można migrować do usługi Managed Disks w następujących scenariuszach:

| Migrowanie...                                            | Link do dokumentacji                                                                                                                                                                                                                                                                  |
|----------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Konwertuj autonomiczny maszyn wirtualnych i maszyn wirtualnych w zestawie do usługi managed disks dostępności   | [Konwertowanie maszyn wirtualnych w celu używania dysków zarządzanych](convert-unmanaged-to-managed-disks.md) |
| Pojedyncza maszyna wirtualna z wersji klasycznej do usługi Resource Manager na dyskach zarządzanych     | [Tworzenie maszyny Wirtualnej z klasycznym wirtualnego dysku twardego](create-vm-specialized-portal.md)  | 
| Wszystkie maszyny wirtualne w sieci wirtualnej z wersji klasycznej do usługi Resource Manager na dyskach zarządzanych     | [Migrację zasobów IaaS od modelu klasycznego do usługi Resource Manager](migration-classic-resource-manager-ps.md) i następnie [konwertowanie maszyny Wirtualnej z dysków niezarządzanych do usługi managed disks](convert-unmanaged-to-managed-disks.md) | 






## <a name="plan-for-the-conversion-to-managed-disks"></a>Planowanie konwersji do usługi Managed Disks

Ta sekcja ułatwia najlepszych decyzji o typach maszyn wirtualnych i dysków.


## <a name="location"></a>Lokalizacja

Wybierz lokalizację, w której są dostępne usługi Azure Managed Disks. Jeśli przenosisz do dysków Premium Managed Disks, upewnij się również Premium storage jest dostępna w regionie, w którym jest planowana można przenieść do. Zobacz [usług platformy Azure według regionu](https://azure.microsoft.com/regions/#services) dla aktualnych informacji o dostępnych lokalizacji.

## <a name="vm-sizes"></a>Rozmiary maszyn wirtualnych

Jeśli użytkownik migruje do usługi Premium Managed Disks, musi być aktualizowana rozmiar maszyny Wirtualnej do magazynu w warstwie Premium stanie rozmiaru dostępne w regionie, w którym znajduje się maszyna wirtualna. Przejrzyj rozmiarów maszyn wirtualnych, które są zdolne do magazynu w warstwie Premium. Specyfikacje rozmiaru maszyny Wirtualnej platformy Azure są wymienione w [rozmiary maszyn wirtualnych](sizes.md).
Sprawdź charakterystyki wydajności maszyn wirtualnych, które współpracują z magazynu w warstwie Premium i wybierz odpowiedni rozmiar maszyny Wirtualnej, najlepiej pasujące do obciążenia. Upewnij się, że jest dostępna wystarczająca przepustowość na maszynie Wirtualnej do kierowania ruchu dysku.

## <a name="disk-sizes"></a>Rozmiary dysków

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

## <a name="disk-caching-policy"></a>Zasady buforowania dysku

**Premium Managed Disks**

Domyślnie zasady buforowania dysku jest *tylko do odczytu* wszystkich dysków w warstwie Premium danych, a *odczytu i zapisu* dla dysku systemu operacyjnego w warstwie Premium dołączonych do maszyny Wirtualnej. To ustawienie konfiguracji jest zalecane, aby osiągnąć optymalną wydajność dla aplikacji systemu IOs. Dla dysków z danymi zapisu przy odczycie czy tylko do zapisu (takich jak pliki dziennika programu SQL Server) należy wyłączyć buforowanie dysku, dzięki czemu można osiągnąć lepszą wydajność aplikacji.

## <a name="pricing"></a>Cennik

Przegląd [cennika usługi Managed Disks](https://azure.microsoft.com/pricing/details/managed-disks/). Cennik dysków Premium Managed Disks jest taka sama jak dysków niezarządzanych w warstwie Premium. Jednak ceny dysków zarządzanych w warstwie standardowa jest inne niż standardowe dyski niezarządzane.



## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się więcej o [Managed Disks](managed-disks-overview.md)
