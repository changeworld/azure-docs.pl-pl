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
ms.date: 06/29/2018
ms.author: anwestg
ms.openlocfilehash: ce57e153dcab6a386150ebefe1ecb4a018514247
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/29/2018
ms.locfileid: "37130374"
---
# <a name="how-to-redistribute-azure-app-service-on-azure-stack-across-fault-domains"></a>Jak wykonać ponowną dystrybucję usłudze Azure App Service na stosie Azure domen błędów

*Dotyczy: Azure stosu zintegrowane systemy*

Z aktualizacją 1802 stosu Azure obsługuje teraz dystrybucji obciążeń między domenami błędów, funkcja, która jest szczególnie ważne w celu zapewnienia wysokiej dostępności.

>[!IMPORTANT]
>Aby móc korzystać z obsługi domeny błędów, musisz zaktualizować system Azure stosu zintegrowane do 1802. Ten dokument ma zastosowanie tylko do wdrożeń dostawcy zasobów usługi aplikacji, które zostały zakończone przed aktualizacją 1802. Jeśli wdrożono usługi aplikacji Azure stosu, po zastosowaniu aktualizacji 1802 Azure stosu, dostawca zasobów jest już dystrybuowana do domen błędów.

## <a name="rebalance-an-app-service-resource-provider-across-fault-domains"></a>Równoważenie wielu domenach awarii dostawcy zasobów usługi aplikacji

Aby ponownie rozesłać zestawy skalowania wdrożone dla dostawcy zasobów usługi aplikacji, należy wykonać kroki w tym artykule dla każdego zestawu skali. Domyślnie nazwy scaleset są:

* ManagementServersScaleSet
* FrontEndsScaleSet
* PublishersScaleSet
* SharedWorkerTierScaleSet
* SmallWorkerTierScaleSet
* MediumWorkerTierScaleSet
* LargeWorkerTierScaleSet

>[!NOTE]
> Jeśli nie masz wdrożone w niektórych zestawy skalowania warstwy proces roboczy, nie trzeba przeprowadzić ponowne równoważenie te zestawy skalowania. Zestawy skalowania są rozmieszczane równomiernie poprawnie, gdy można skalować je w przyszłości.

Aby skalować zestawy skalowania w poziomie, wykonaj następujące kroki:

1. Zaloguj się do portalu Azure Administrator stosu.
2. Wybierz **więcej usług**.
3. W obszarze obliczeń, wybierz **zestawy skalowania maszyny wirtualnej**. Istniejące zestawy skalowania wdrożenia w ramach wdrożenia usługi App Service będzie wyświetlane z informacje o liczbie wystąpień. Następujący zrzut ekranu przedstawia przykład zestawów skalowania.

      ![Zestawy skalowania usługi aplikacji w Azure UX zestawy skalowania maszyny wirtualnej na liście][1]

4. Skalowanie w poziomie każdego zestawu. Na przykład jeśli masz istniejące wystąpienia w zestawie skalowania musi skalowanie w poziomie do 6, trzy nowe wystąpienia są wdrażane w domenach awarii. W poniższym przykładzie programu PowerShell pokazano wychodzących do skalowania w poziomie zestaw skali.

   ```powershell
   Add-AzureRmAccount -EnvironmentName AzureStackAdmin 

   # Get current scale set
   $vmss = Get-AzureRmVmss -ResourceGroupName "AppService.local" -VMScaleSetName "SmallWorkerTierScaleSet"

   # Set and update the capacity of your scale set
   $vmss.sku.capacity = 6
   Update-AzureRmVmss -ResourceGroupName AppService.local" -Name "SmallWorkerTierScaleSet" -VirtualMachineScaleSet $vmss
   ```

   >[!NOTE]
   >Ten krok może zająć kilka godzin, w zależności od typu roli i liczby wystąpień.

5. W **aplikacji usługi administracyjnej ról**, monitorować stan nowych wystąpień roli. Aby sprawdzić stan wystąpienia roli, wybierz typ roli z listy

    ![Usługa aplikacji Azure w przypadku ról Azure stosu][2]

6. Gdy jest w stanie nowych wystąpień roli **gotowe**, wróć do **zestawu skalowania maszyn wirtualnych** i **usunąć** starego wystąpień roli.

7. Powtórz te kroki dla **każdego** zestaw skali maszyny wirtualnej.

## <a name="next-steps"></a>Kolejne kroki

Możesz również wypróbować innych [platforma jako usługa (PaaS) usługi](azure-stack-tools-paas-services.md).

* [Dostawcy zasobów programu SQL Server](azure-stack-sql-resource-provider-deploy.md)
* [Dostawca zasobów MySQL](azure-stack-mysql-resource-provider-deploy.md)

<!--Image references-->
[1]: ./media/azure-stack-app-service-fault-domain-update/app-service-scale-sets.png
[2]: ./media/azure-stack-app-service-fault-domain-update/app-service-roles.png
