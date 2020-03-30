---
title: Przenoszenie konfiguracji konserwacji do innego regionu platformy Azure
description: Dowiedz się, jak przenieść konfigurację konserwacji maszyny Wirtualnej do innego regionu platformy Azure
services: virtual-machines
author: shants123
ms.service: virtual-machines
ms.topic: article
ms.tgt_pltfrm: vm
ms.date: 03/04/2020
ms.author: shants
ms.openlocfilehash: fe03bead238d3fb7bda3ee685bd5587c3e0dbc58
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78304461"
---
# <a name="move-a-maintenance-control-configuration-to-another-region"></a>Przenoszenie konfiguracji kontroli konserwacji do innego regionu

W tym artykule należy przenieść konfigurację kontroli konserwacji do innego regionu platformy Azure. Można przenieść konfigurację z wielu powodów. Na przykład, aby skorzystać z nowego regionu, wdrożyć funkcje lub usługi dostępne w określonym regionie, aby spełnić wymagania dotyczące zasad wewnętrznych i nadzoru lub w odpowiedzi na planowanie zdolności produkcyjnych.

Kontrola konserwacji z dostosowanymi konfiguracjami konserwacji umożliwia kontrolowanie sposobu stosowania aktualizacji platformy na maszynach wirtualnych [z systemem Windows](https://docs.microsoft.com/azure/virtual-machines/maintenance-control-cli?toc=/azure/virtual-machines/windows/toc.json&bc=/azure/virtual-machines/windows/breadcrumb/toc.json) i [Linux](https://docs.microsoft.com/azure/virtual-machines/maintenance-control-cli?toc=%2Fazure%2Fvirtual-machines%2Flinux%2Ftoc.json&bc=%2Fazure%2Fvirtual-machines%2Flinux%2Fbreadcrumb%2Ftoc.json&view=azure-java-stable) oraz na dedykowanych hostach platformy Azure. Istnieje kilka scenariuszy przenoszenia kontroli konserwacji w różnych regionach:

- Aby przenieść konfigurację kontroli konserwacji, ale nie zasoby skojarzone z konfiguracją, postępuj zgodnie z instrukcjami w tym artykule.
- Aby przenieść zasoby skojarzone z konfiguracją konserwacji, ale nie z samą konfiguracją, postępuj zgodnie z [tymi instrukcjami](move-region-maintenance-configuration-resources.md).
- Aby przenieść zarówno konfigurację konserwacji, jak i skojarzone z nią zasoby, należy najpierw postępować zgodnie z instrukcjami zawartymi w tym artykule. Następnie postępuj zgodnie z [tymi instrukcjami](move-region-maintenance-configuration-resources.md).

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem przenoszenia konfiguracji kontroli konserwacji:

- Konfiguracje konserwacji są skojarzone z maszynami wirtualnymi platformy Azure lub dedykowanymi hostami platformy Azure. Upewnij się, że zasoby maszyny Wirtualnej/hosta istnieją w nowym regionie przed rozpoczęciem.
- Identyfikacji: 
    - Istniejące konfiguracje kontroli konserwacji.
    - Grupy zasobów, w których obecnie znajdują się istniejące konfiguracje. 
    - Grupy zasobów, do których zostaną dodane konfiguracje po przejściu do nowego regionu. 
    - Zasoby skojarzone z konfiguracją konserwacji, którą chcesz przenieść.
    - Sprawdź, czy zasoby w nowym regionie są takie same, jak te skojarzone z bieżącymi konfiguracjami konserwacji. Konfiguracje mogą mieć takie same nazwy w nowym regionie, jak w starym, ale nie jest to wymagane.

## <a name="prepare-and-move"></a>Przygotowanie i przeniesienie 

1. Pobierz wszystkie konfiguracje konserwacji w każdej subskrypcji. Uruchom polecenie listy konfiguracji konserwacji interfejsu wiersza polecenia [az,](https://docs.microsoft.com/cli/azure/ext/maintenance/maintenance/configuration?view=azure-cli-latest#ext-maintenance-az-maintenance-configuration-list) aby to zrobić, zastępując $subId identyfikatorem subskrypcji.

    ```
    az maintenance configuration list --subscription $subId --query "[*].{Name:name, Location:location, ResGroup:resourceGroup}" --output table
    ```
2. Przejrzyj zwróconą listę rekordów konfiguracji w ramach subskrypcji. Oto przykład. Lista będzie zawierać wartości dla określonego środowiska.

    **Nazwa** | **Lokalizacja** | **Grupa zasobów**
    --- | --- | ---
    Pomiń konserwację | okręg wyborczy eastus2 | konfiguracja-grupa zasobów
    IgniteDemoConfig | okręg wyborczy eastus2 | konfiguracja-grupa zasobów
    defaultMaintenanceConfiguration-eastus | eastus | konfiguracja testowa
    

3. Zapisz listę w celach informacyjnych. Podczas przenoszenia konfiguracji pomaga sprawdzić, czy wszystko zostało przeniesione.
4. Jako punkt odniesienia należy mapować każdą grupę konfiguracji/zasobów do nowej grupy zasobów w nowym regionie.
5. Tworzenie nowych konfiguracji konserwacji w nowym regionie za pomocą [programu PowerShell](../virtual-machines/maintenance-control-powershell.md#create-a-maintenance-configuration)lub [cli](../virtual-machines/maintenance-control-cli.md#create-a-maintenance-configuration).
6. Skojarz konfiguracje z zasobami w nowym regionie za pomocą programu [PowerShell](../virtual-machines/maintenance-control-powershell.md#assign-the-configuration)lub [interfejsu wiersza polecenia](../virtual-machines/maintenance-control-cli.md#assign-the-configuration).


## <a name="verify-the-move"></a>Sprawdź ruch

Po przeniesieniu konfiguracji porównaj konfiguracje i zasoby w nowym regionie z przygotowaną listą tabel.


## <a name="clean-up-source-resources"></a>Oczyszczanie zasobów źródłowych

Po przeprowadzce należy rozważyć usunięcie przeniesionych konfiguracji konserwacji w regionie źródłowym, [programie PowerShell](../virtual-machines/maintenance-control-powershell.md#remove-a-maintenance-configuration)lub [CLI](../virtual-machines/maintenance-control-cli.md#delete-a-maintenance-configuration).


## <a name="next-steps"></a>Następne kroki

Postępuj zgodnie z [tymi instrukcjami,](move-region-maintenance-configuration-resources.md) jeśli chcesz przenieść zasoby skojarzone z konfiguracjami konserwacji. 
