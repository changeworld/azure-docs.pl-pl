---
title: 'Usługi aplikacji Azure stosu: Fault Aktualizacja domeny | Dokumentacja firmy Microsoft'
description: Jak wykonać ponowną dystrybucję usłudze Azure App Service na stosie Azure domen błędów
services: azure-stack
documentationcenter: ''
author: apwestgarth
manager: stefsch
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/09/2018
ms.author: anwestg
ms.openlocfilehash: 42adef66fb1b1141ab44aab3a1ccdaae022202b5
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
ms.locfileid: "32150978"
---
# <a name="how-to-redistribute-azure-app-service-on-azure-stack-across-fault-domains"></a>Jak wykonać ponowną dystrybucję usłudze Azure App Service na stosie Azure domen błędów

*Dotyczy: Azure stosu zintegrowane systemy*

Z aktualizacją 1802 stosu Azure obsługuje teraz dystrybucji obciążeń między domenami usterek, funkcji, co jest szczególnie ważne w celu zapewnienia wysokiej dostępności.

> [!IMPORTANT]
> System Azure stosu zintegrowane musi zostały zaktualizowane do 1802, aby można było wykorzystać zalety domeny błędów.  Ten dokument ma zastosowanie tylko do wdrożenia dostawcy zasobów usługi aplikacji, które zostały ukończone przed aktualizacją 1802.  Jeśli wdrożono usługi aplikacji Azure stosu po zastosowaniu aktualizacji 1802 stos Azure dostawcy zasobów jest już dystrybuowana do domen błędów.
>
>

## <a name="rebalance-an-app-service-resource-provider-across-fault-domains"></a>Równoważenie wielu domenach awarii dostawcy zasobów usługi aplikacji

Aby można było ponownie dystrybuować zestawy skalowania wdrożone dla dostawcy zasobów usługi aplikacji, należy wykonać następujące kroki dla każdego zestawu skali.  Domyślnie nazwy scaleset są:

* ManagementServersScaleSet
* FrontEndsScaleSet
* PublishersScaleSet
* SharedWorkerTierScaleSet
* SmallWorkerTierScaleSet
* MediumWorkerTierScaleSet
* LargeWorkerTierScaleSet

> [!NOTE]
> Jeśli użytkownik nie mają żadnych wystąpień, wdrożone w niektóre zestawy skalowania warstwy procesu roboczego, jest konieczne ponowne zrównoważenie te zestawy skalowania.  Zestawy skalowania są rozmieszczane równomiernie poprawnie, gdy można skalować je w przyszłości.
>
>

1. Przejdź do zestawy skalowania maszyny wirtualnej w portalu Azure Administrator stosu.  Istniejące zestawy skalowania wdrożenia w ramach wdrożenia usługi App Service będzie wyświetlane z informacje o liczbie wystąpień.

    ![Zestawy skalowania usługi aplikacji w Azure UX zestawy skalowania maszyny wirtualnej na liście][1]

2. Skalowanie obok każdego zestawu.  Na przykład jeśli masz istniejące wystąpienia w zestawie skalowania musi skalowanie w poziomie z do 6, tak aby trzech nowych wystąpień, które będą udostępniane w domenach awarii.
    a. [Konfigurowanie środowiska Azure stosu administratora w programie PowerShell](azure-stack-powershell-configure-admin.md) b. W tym przykładzie umożliwiają skalowanie w poziomie zestaw skali:
        ```powershell
                Add-AzureRmAccount -EnvironmentName AzureStackAdmin 

                # Get current scale set
                $vmss = Get-AzureRmVmss -ResourceGroupName "AppService.local" -VMScaleSetName "SmallWorkerTierScaleSet"

                # Set and update the capacity of your scale set
                $vmss.sku.capacity = 6
                Update-AzureRmVmss -ResourceGroupName "AppService.local" -Name "SmallWorkerTierScaleSet" -VirtualMachineScaleSet $vmss
        '''
> [!NOTE]
> Ten krok może zająć kilka godzin w zależności od typu roli i liczby wystąpień.
>
>

3. Monitorowanie stanu nowych wystąpień roli w bloku aplikacji usługi administracyjnej ról.  Sprawdź stan wystąpienia roli poszczególnych klikając Typ roli na liście

    ![Usługa aplikacji Azure w przypadku ról Azure stosu][2]

4. Jeden nowe wystąpienia są w **gotowe** stanu, wróć do bloku zestawu skalowania maszyn wirtualnych i **usunąć** starego wystąpień.

5. Powtórz te kroki dla **każdego** zestaw skali maszyny wirtualnej.

## <a name="next-steps"></a>Kolejne kroki

Możesz również wypróbować innych [platforma jako usługa (PaaS) usługi](azure-stack-tools-paas-services.md).

* [Dostawcy zasobów programu SQL Server](azure-stack-sql-resource-provider-deploy.md)
* [Dostawca zasobów MySQL](azure-stack-mysql-resource-provider-deploy.md)

<!--Image references-->
[1]: ./media/azure-stack-app-service-fault-domain-update/app-service-scale-sets.png
[2]: ./media/azure-stack-app-service-fault-domain-update/app-service-roles.png
