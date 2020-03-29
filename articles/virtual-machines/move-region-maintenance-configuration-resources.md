---
title: Przenoszenie zasobów skojarzonych z konfiguracją konserwacji do innego regionu
description: Dowiedz się, jak przenieść zasoby skojarzone z konfiguracją konserwacji maszyny Wirtualnej do innego regionu platformy Azure
services: virtual-machines
author: shants123
ms.service: virtual-machines
ms.topic: article
ms.tgt_pltfrm: vm
ms.date: 03/04/2020
ms.author: shants
ms.openlocfilehash: 3e271e2467b495e79a93ce5eab5edee36e65e619
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78304448"
---
# <a name="move-resources-in-a-maintenance-control-configuration-to-another-region"></a>Przenoszenie zasobów w konfiguracji kontroli konserwacji do innego regionu

W tym artykule należy przenieść zasoby skojarzone z konfiguracją kontroli konserwacji do innego regionu platformy Azure. Można przenieść konfigurację z wielu powodów. Na przykład, aby skorzystać z nowego regionu, wdrożyć funkcje lub usługi dostępne w określonym regionie, aby spełnić wymagania dotyczące zasad wewnętrznych i nadzoru lub w odpowiedzi na planowanie zdolności produkcyjnych.

Kontrola konserwacji z dostosowanymi konfiguracjami konserwacji umożliwia kontrolowanie sposobu stosowania aktualizacji platformy na maszynach wirtualnych [z systemem Windows](https://docs.microsoft.com/azure/virtual-machines/maintenance-control-cli?toc=/azure/virtual-machines/windows/toc.json&bc=/azure/virtual-machines/windows/breadcrumb/toc.json) i [Linux](https://docs.microsoft.com/azure/virtual-machines/maintenance-control-cli?toc=%2Fazure%2Fvirtual-machines%2Flinux%2Ftoc.json&bc=%2Fazure%2Fvirtual-machines%2Flinux%2Fbreadcrumb%2Ftoc.json&view=azure-java-stable) oraz na dedykowanych hostach platformy Azure. Istnieje kilka scenariuszy przenoszenia kontroli konserwacji w różnych regionach:

- Aby przenieść zasoby skojarzone z konfiguracją konserwacji, ale nie samą konfigurację, postępuj zgodnie z tym artykułem.
- Aby przenieść konfigurację kontroli konserwacji, ale nie zasoby skojarzone z konfiguracją, postępuj zgodnie z [tymi instrukcjami](move-region-maintenance-configuration.md).
- Aby przenieść zarówno konfigurację konserwacji, jak i związane z nią zasoby, należy najpierw postępować zgodnie z [tymi instrukcjami](move-region-maintenance-configuration.md). Następnie postępuj zgodnie z instrukcjami w tym artykule.

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem przenoszenia zasobów skojarzonych z konfiguracją kontroli konserwacji:

- Przed rozpoczęciem upewnij się, że w nowym regionie istnieją zasoby, które przenosisz.
- Sprawdź konfiguracje kontroli konserwacji skojarzone z maszynami wirtualnymi platformy Azure i dedykowanymi hostami platformy Azure, które chcesz przenieść. Sprawdź każdy zasób indywidualnie. Obecnie nie ma możliwości pobrania konfiguracji dla wielu zasobów.
- Podczas pobierania konfiguracji zasobu:
    - Upewnij się, że używasz identyfikatora subskrypcji dla konta, a nie identyfikatora hosta dedykowanego platformy Azure.
    - CLI: Parametr tabeli --output jest używany tylko do odczytu i można go usunąć lub zmienić.
    - Program PowerShell: Parametr Nazwa tabeli formatu jest używany tylko do odczytu i można go usunąć lub zmienić.
    - Jeśli używasz programu PowerShell, zostanie wyświetlony błąd, jeśli spróbujesz wyświetlić listę konfiguracji zasobu, który nie ma żadnych skojarzonych konfiguracji. Błąd będzie podobny do: "Operacja nie powiodła się ze stanem: 'Nie znaleziono'. Szczegóły: 404 Błąd klienta: Nie znaleziono adresu URL".

    
## <a name="prepare-to-move"></a>Przygotowanie do ruchu

1. Przed rozpoczęciem zdefiniuj te zmienne. Podaliśmy przykład dla każdego z nich.

    **Zmienna** | **Szczegóły** | **Przykład**
    --- | ---
    $subId | Identyfikator subskrypcji zawierającej konfiguracje konserwacji | "nasz identyfikator subskrypcji"
    $rsrcGroupName | Nazwa grupy zasobów (maszyna wirtualna platformy Azure) | "VMResourceGroup"
    $vmName | Nazwa zasobu maszyny Wirtualnej |  "myVM"
    $adhRsrcGroupName |  Grupa zasobów (hosty dedykowane) | "HostResourceGroup"
    $adh | Dedykowana nazwa hosta | "myHost"
    $adhParentName | Nazwa zasobu nadrzędnego | "Grupa hostów"
    
2. Aby pobrać konfiguracje konserwacji za pomocą polecenia [Get-AZFigurationAssignment](https://docs.microsoft.com/powershell/module/az.maintenance/Get-AzConfigurationAssignment?view=azps-3.5.0) programu PowerShell:

    - W przypadku dedykowanych hostów platformy Azure uruchom:
        ```
        Get-AzConfigurationAssignment -ResourceGroupName $adhRsrcGroupName -ResourceName $adh -ResourceType hosts -ProviderName Microsoft.Compute -ResourceParentName $adhParentName -ResourceParentType hostGroups | Format-Table Name
        ```

    - W przypadku maszyn wirtualnych platformy Azure uruchom:

        ```
        Get-AzConfigurationAssignment -ResourceGroupName $rgName -ResourceName $vmName -ProviderName Microsoft.Compute -ResourceType virtualMachines | Format-Table Name
        ```
3. Aby pobrać konfiguracje konserwacji przy użyciu polecenia [przypisania obsługi technicznej cli az:](https://docs.microsoft.com/cli/azure/ext/maintenance/maintenance/assignment?view=azure-cli-latest)

    - Dla hostów dedykowanych platformy Azure:

        ```
        az maintenance assignment list --subscription $subId --resource-group $adhRsrcGroupName --resource-name $adh --resource-type hosts --provider-name Microsoft.Compute --resource-parent-name $adhParentName --resource-parent-type hostGroups --query "[].{HostResourceGroup:resourceGroup,ConfigName:name}" --output table
        ```

    - Dla maszyn wirtualnych platformy Azure:

        ```
        az maintenance assignment list --subscription $subId --provider-name Microsoft.Compute --resource-group $rsrcGroupName --resource-name $vmName --resource-type virtualMachines --query "[].{HostResourceGroup:resourceGroup, ConfigName:name}" --output table
        ```


## <a name="move"></a>Move 

1. [Postępuj zgodnie z tymi instrukcjami,](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate?toc=/azure/virtual-machines/windows/toc.json&bc=/azure/virtual-machines/windows/breadcrumb/toc.json) aby przenieść maszyny wirtualne platformy Azure do nowego regionu.
2. Po przeniesieniu zasobów należy ponownie zastosować konfiguracje konserwacji do zasobów w nowym regionie, w zależności od tego, czy konfiguracje konserwacji zostały przeniesione. Konfigurację konserwacji można zastosować do zasobu przy użyciu programu [PowerShell](../virtual-machines/maintenance-control-powershell.md) lub [CLI](../virtual-machines/maintenance-control-cli.md).


## <a name="verify-the-move"></a>Sprawdź ruch

Weryfikuj zasoby w nowym regionie i weryfikuj skojarzone konfiguracje zasobów w nowym regionie. 

## <a name="clean-up-source-resources"></a>Oczyszczanie zasobów źródłowych

Po przeprowadzce należy rozważyć usunięcie przeniesionych zasobów w regionie źródłowym.


## <a name="next-steps"></a>Następne kroki

Postępuj zgodnie z [tymi instrukcjami,](move-region-maintenance-configuration.md) jeśli chcesz przenieść konfiguracje konserwacji. 
