---
title: Skalowanie ról proces roboczy w usługach App Services — Azure Stack | Dokumentacja firmy Microsoft
description: Szczegółowe wskazówki dotyczące skalowania usługi Azure Stack App Services
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: 3cbe87bd-8ae2-47dc-a367-51e67ed4b3c0
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/08/2018
ms.author: jeffgilb
ms.reviewer: anwestg
ms.lastreviewed: 06/08/2018
ms.openlocfilehash: 0ac20ee9f6197a8322bb741020aee813c690847e
ms.sourcegitcommit: 6cab3c44aaccbcc86ed5a2011761fa52aa5ee5fa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/20/2019
ms.locfileid: "56445887"
---
# <a name="app-service-on-azure-stack-add-more-infrastructure-or-worker-roles"></a>Usługa App Service w usłudze Azure Stack: Dodawanie większej liczby ról infrastruktury lub proces roboczy

*Dotyczy: Zintegrowane usługi Azure Stack, systemy i usługi Azure Stack Development Kit*  

Ten dokument zawiera instrukcje dotyczące sposobu skalowania usługi App Service w ramach ról infrastruktury i proces roboczy usługi Azure Stack. Zawiera ona procedurę tworzenia ról dodatkowych procesów roboczych do obsługi aplikacji o dowolnej wielkości.

> [!NOTE]
> Jeśli środowiska Azure Stack nie ma więcej niż 96 GB pamięci RAM, możesz mieć trudności, dodawanie dodatkowej pojemności.

Usługa App Service w usłudze Azure Stack domyślnie obsługuje warstwy bezpłatna i współdzielona procesu roboczego. Aby dodać inne warstwy procesu roboczego, należy dodać większej liczby ról procesów roboczych.

Jeśli nie masz pewności, co zostało wdrożone przy użyciu domyślnego usługi App Service w usłudze Azure Stack instalacji, możesz przejrzeć dodatkowe informacje w [usługi App Service w usłudze Azure Stack — omówienie](azure-stack-app-service-overview.md).

Usługa Azure App Service w usłudze Azure Stack wdraża wszystkich ról przy użyciu zestawów skalowania maszyn wirtualnych i jako takie korzysta z możliwości skalowania danego obciążenia. W związku z tym wszystkie skalowanie warstwy procesu roboczego odbywa się za pośrednictwem administratora usługi aplikacji

> [!IMPORTANT]
> Obecnie nie jest możliwe skalowanie zestawów skalowania maszyn wirtualnych w portalu jak określone w informacjach o wersji usługi Azure Stack, zatem skalowanie w poziomie za pomocą do przykładu programu PowerShell.
>
>

## <a name="add-additional-workers-with-powershell"></a>Dodawanie dodatkowych procesów roboczych przy użyciu programu PowerShell

1. [Konfigurowanie środowiska administratora usługi Azure Stack w programie PowerShell](azure-stack-powershell-configure-admin.md)

2. Ten przykład umożliwia skalowanie zestawu skalowania:
   ```powershell
   
    ##### Scale out the AppService Role instances ######
   
    # Set context to AzureStack admin.
    Login-AzureRmAccount -EnvironmentName AzureStackAdmin
                                                 
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
   > Ten krok może zająć wiele godzin w zależności od tego, czy typ roli oraz liczbę wystąpień.
   >
   >

3. Monitor stanu nowych wystąpień roli w administracji usługi aplikacji, aby sprawdzić stan wystąpienia poszczególnych ról kliknij typ roli na liście.

## <a name="add-additional-workers-directly-within-the-app-service-resource-provider-admin"></a>Dodawanie dodatkowych procesów roboczych bezpośrednio z poziomu administratora dostawcy zasobów usługi aplikacji

1. Zaloguj się do portalu administratora usługi Azure Stack jako administratora usługi.

2. Przejdź do **usług App Services**.

    ![](media/azure-stack-app-service-add-worker-roles/image01.png)

3. Kliknij przycisk **role**. W tym miejscu wyświetlona lista wszystkich ról usługi App Service wdrożone.

4. Kliknij prawym przyciskiem myszy w wierszu Typ, który chcesz skalować, a następnie kliknij przycisk **zestawu skalowania**.

    ![](media/azure-stack-app-service-add-worker-roles/image02.png)

5. Kliknij przycisk **skalowanie**, wybierz liczbę wystąpień, które chcesz skalować, a następnie kliknij przycisk **Zapisz**.

    ![](media/azure-stack-app-service-add-worker-roles/image03.png)

6. Usługi App Service w usłudze Azure Stack będą teraz dodawania dodatkowych maszyn wirtualnych, ich konfigurowania, zainstalowania wymaganego oprogramowania i oznacz je jako gotowy, po zakończeniu tego procesu. Ten proces może potrwać około 80 minut.

7. Możesz monitorować postęp gotowości nowych ról, wyświetlając pracowników w **role** bloku.

## <a name="result"></a>Wynik

Po umieszczeniu ich w pełni wdrożony i będzie gotowy, procesy robocze staną się dostępne dla użytkowników wdrożyć swoje obciążenia na nich. Poniżej przedstawiono przykład wielu warstw cenowych dostępne domyślnie. Jeśli nie ma żadnych dostępnych procesów roboczych dla warstwy procesów roboczych w szczególności, możliwość wyboru odpowiedniej warstwy cenowej jest niedostępny.

![](media/azure-stack-app-service-add-worker-roles/image04.png)

>[!NOTE]
> Aby skalować zarządzania, roli frontonu lub wydawcy Dodaj musi skalowanie w poziomie odpowiedni typ roli. 
>
>

Aby skalować zarządzania, serwer sieci Web lub role wydawcy, te same czynności wybierania typu odpowiednią rolę. Kontrolery nie są wdrażane jako zestawy skalowania i w związku z tym dwa powinny być wdrażane w czasie instalacji dla wszystkich wdrożeń produkcyjnych.

### <a name="next-steps"></a>Kolejne kroki

[Konfigurowanie źródeł wdrożenia](azure-stack-app-service-configure-deployment-sources.md)
