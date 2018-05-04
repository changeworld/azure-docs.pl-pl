---
title: Skalowanie w poziomie roli proces roboczy w usługach App - stosu Azure | Dokumentacja firmy Microsoft
description: Szczegółowe wskazówki dotyczące skalowania usługi aplikacji Azure stosu
services: azure-stack
documentationcenter: ''
author: apwestgarth
manager: stefsch
editor: ''
ms.assetid: 3cbe87bd-8ae2-47dc-a367-51e67ed4b3c0
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/08/2018
ms.author: anwestg
ms.reviewer: brenduns
ms.openlocfilehash: 3bbd39f99c72d92bd7969de52d893c892314fc99
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="app-service-on-azure-stack-add-more-infrastructure-or-worker-roles"></a>Usługi aplikacji Azure stosu: Dodaj więcej ról infrastruktury lub procesu roboczego

*Dotyczy: Azure stosu zintegrowanych systemów i Azure stosu Development Kit*  

Ten dokument zawiera instrukcje dotyczące sposobu skalowania usługi aplikacji Azure stosu ról infrastruktury i proces roboczy. Zawiera ona procedury służące do tworzenia ról dodatkowych procesów roboczych do obsługi aplikacji o dowolnym rozmiarze.

> [!NOTE]
> Jeśli środowiska Azure stosu nie ma więcej niż 96 GB pamięci RAM, mogą mieć trudności dodanie dodatkowej pojemności.

Usługi aplikacji Azure stosu, domyślnie obsługuje warstw bezpłatne i udostępniane procesu roboczego. Aby dodać inne warstw procesu roboczego, należy dodać jedną rolę procesu roboczego.

Jeśli nie masz pewności, jaka została wdrożona przy użyciu domyślnego usługi aplikacji Azure stosu instalacji, możesz przejrzeć dodatkowe informacje w [usługi aplikacji — omówienie stosu Azure](azure-stack-app-service-overview.md).

Usługa aplikacji Azure na stosie Azure wdraża wszystkich ról przy użyciu zestawy skalowania maszyny wirtualnej i jako takie korzysta z możliwości skalowania danego obciążenia. W związku z tym wszystkie skalowania warstwy procesu roboczego odbywa się za pośrednictwem administratora aplikacji usługi.

> [!IMPORTANT]
> Obecnie nie jest możliwe skalowanie zestawy skalowania maszyny wirtualnej w portalu określonej w informacjach o wersji stosu Azure, dlatego należy używać w przykładzie programu PowerShell do skalowania w poziomie.
>
>

## <a name="add-additional-workers-with-powershell"></a>Dodawanie dodatkowych pracowników przy użyciu programu PowerShell

1. [Konfigurowanie środowiska Azure stosu administratora w programie PowerShell](azure-stack-powershell-configure-admin.md)

2. W tym przykładzie umożliwiają skalowanie w poziomie zestaw skali:
   ```powershell
   
    ##### Scale out the AppService Role instances ######
   
    # Set context to AzureStack admin.
    Add-AzureRmAccount -EnvironmentName AzureStackAdmin
                                                 
    ## Name of the Resource group where AppService is deployed.
    $AppServiceResourceGroupName = "AppService.local"

    ## Name of the ScaleSet : e.g. FrontEndsScaleSet, ManagementServersScaleSet, PublishersScaleSet , LargeWorkerTierScaleSet,      MediumWorkerTierScaleSet, SmallWorkerTierScaleSet, SharedWorkerTierScaleSet
    $ScaleSetName = "SharedWorkerTierScaleSet"

    ## TotalCapacity is sum of the instances needed at the end of operation. 
    ## e.g. if your VMSS has 1 instance(s) currently and you need 1 more the TotalCapacity should be set to 2
    $TotalCapacity = 2  

    # Get current scale set
    $vmss = Get-AzureRmVmss -ResourceGroupName $AppServiceResourceGroupName -VMScaleSetName $ScaleSetName

    # Set and update the capacity
    $vmss.sku.capacity = $TotalCapacity
    Update-AzureRmVmss -ResourceGroupName $AppServiceResourceGroupName -Name $ScaleSetName -VirtualMachineScaleSet $vmss 
   ```    

   > [!NOTE]
   > Ten krok może zająć kilka godzin w zależności od typu roli i liczby wystąpień.
   >
   >

3. Monitor stanu nowych wystąpień roli w administracji usługi aplikacji, aby sprawdzić stan wystąpienia roli poszczególnych kliknij typ roli na liście.

## <a name="add-additional-workers-directly-within-the-app-service-resource-provider-admin"></a>Dodawanie dodatkowych pracowników bezpośrednio z poziomu administratora dostawcy zasobów usługi aplikacji.

1. Zaloguj się do portalu administracyjnego platformy Azure stosu jako administratora usługi.

2. Przejdź do **usługi aplikacji**.

    ![](media/azure-stack-app-service-add-worker-roles/image01.png)

3. Kliknij przycisk **ról**. W tym miejscu wyświetlić podział wszystkich ról usług aplikacji wdrożone.

4. Kliknij prawym przyciskiem myszy w wierszu typu, aby skalować, a następnie kliknij przycisk **ScaleSet**.

    ![](media/azure-stack-app-service-add-worker-roles/image02.png)

5. Kliknij przycisk **skalowanie**, wybierz liczbę wystąpień chcesz skalować, a następnie kliknij przycisk **zapisać**.

    ![](media/azure-stack-app-service-add-worker-roles/image03.png)

6. Usługi aplikacji Azure stosu będzie teraz Dodawanie dodatkowych maszyn wirtualnych, skonfigurować ich zainstalowania wymaganego oprogramowania i oznaczyć je jako gotowy, po zakończeniu tego procesu. Ten proces może potrwać około minuty 80.

7. Postęp gotowość nowe role można monitorować, wyświetlając pracowników w **ról** bloku.

## <a name="result"></a>Wynik

Po umieszczeniu ich w pełni wdrożone i gotowe, pracownicy stają się dostępne dla użytkowników wdrożyć swoje obciążeń na nich. Poniżej pokazano przykład wielu dostępne warstwy cenowe domyślnie. Jeśli nie ma żadnych dostępnych pracowników dla warstwy określonego procesu roboczego, może wybrać odpowiedniego warstwa cenowa jest niedostępna.

![](media/azure-stack-app-service-add-worker-roles/image04.png)

>[!NOTE]
> Do skalowania w poziomie zarządzania, role frontonu lub wydawcy dodać musi skalowania typu odpowiadającego roli. 
>
>

Do skalowania w poziomie zarządzania, frontonu lub role wydawcy, wykonaj te same czynności, wybierając typ odpowiednią rolę. Kontrolery nie są wdrażane jako zestawy skalowania i w związku z tym dwóch powinny zostać wdrożone w czasie instalacji dla wszystkich wdrożeń produkcyjnych.

### <a name="next-steps"></a>Kolejne kroki

[Konfigurowanie źródeł wdrożenia](azure-stack-app-service-configure-deployment-sources.md)
