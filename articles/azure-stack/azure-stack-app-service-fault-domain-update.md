---
title: 'Usługa App Service w usłudze Azure Stack: Aktualizacja domeny błędów | Dokumentacja firmy Microsoft'
description: Jak wykonać ponowną dystrybucję usługi Azure App Service w usłudze Azure Stack w domenach błędów
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
ms.openlocfilehash: 53766099f283f802482fe8e84144502d386b1d69
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/02/2018
ms.locfileid: "39440155"
---
# <a name="how-to-redistribute-azure-app-service-on-azure-stack-across-fault-domains"></a>Jak wykonać ponowną dystrybucję usługi Azure App Service w usłudze Azure Stack w domenach błędów

*Dotyczy: zintegrowane systemy usługi Azure Stack*

Dzięki aktualizacji 1802 usługi Azure Stack obsługuje teraz dystrybucji obciążeń w domenach błędów, funkcji, który ma kluczowe znaczenie w celu zapewnienia wysokiej dostępności.

>[!IMPORTANT]
>Aby skorzystać z pomocy technicznej domeny błędów, należy zaktualizować system zintegrowany z usługi Azure Stack do wersji 1802. Ten dokument ma zastosowanie tylko do wdrożeń dostawcy zasobów usługi App Service, które zostały zakończone przed aktualizacji 1802. Jeśli wdrożono usługi App Service w usłudze Azure Stack po aktualizacji 1802 została zastosowana do usługi Azure Stack, dostawcy zasobów jest już rozproszone w domenach błędów.

## <a name="rebalance-an-app-service-resource-provider-across-fault-domains"></a>Ponowne zrównoważenie dostawcy zasobów usługi App Service w domenach błędów

Aby rozpowszechniać zestawów skalowania, wdrożone dla dostawcy zasobów usługi App Service, należy wykonać kroki opisane w tym artykule dla każdego zestawu skalowania. Domyślnie nazwy zestawu skalowania są:

* ManagementServersScaleSet
* FrontEndsScaleSet
* PublishersScaleSet
* SharedWorkerTierScaleSet
* SmallWorkerTierScaleSet
* MediumWorkerTierScaleSet
* LargeWorkerTierScaleSet

>[!NOTE]
> Jeśli nie masz wystąpieniach wdrożonych w niektórych zestawów skalowania warstwy procesu roboczego nie ma potrzeby ponownego zrównoważenia tych zestawów skalowania. Zestawy skalowania będzie równoważone poprawnie, po użytkownik Skaluj je automatycznie w przyszłości.

Aby skalować zestawy skalowania, wykonaj następujące kroki:

1. Zaloguj się do witryny Azure Portal Administrator stosu.
1. Wybierz **więcej usług**.
1. W obszarze obliczeń, wybierz **zestawy skalowania maszyn wirtualnych**. Istniejące zestawy skalowania wdrożenia w ramach wdrożenia usługi App Service będzie wyświetlane z informacjami o liczbę wystąpień. Poniższy zrzut ekranu przedstawia przykład zestawów skalowania.

      ![Azure App Service Scale Sets UX zestawów skalowania maszyn wirtualnych na liście][1]

1. Skalowanie w poziomie każdego zestawu. Na przykład jeśli masz istniejące wystąpienia w zestawie skalowania możesz musi skalowanie w poziomie do 6, trzy nowe wystąpienia są wdrażane w domenach błędów. W poniższym przykładzie programu PowerShell pokazuje się do skalowania w poziomie zestawu skalowania.

   ```powershell
   Add-AzureRmAccount -EnvironmentName AzureStackAdmin 

   # Get current scale set
   $vmss = Get-AzureRmVmss -ResourceGroupName "AppService.local" -VMScaleSetName "SmallWorkerTierScaleSet"

   # Set and update the capacity of your scale set
   $vmss.sku.capacity = 6
   Update-AzureRmVmss -ResourceGroupName AppService.local" -Name "SmallWorkerTierScaleSet" -VirtualMachineScaleSet $vmss
   ```

   >[!NOTE]
   >Może to potrwać kilka godzin, w zależności od rodzaju roli oraz liczbę wystąpień.

1. W **ról administracyjnych usługi aplikacji**, monitorować stan nowych wystąpień roli. Aby sprawdzić stan wystąpienia roli, wybierz typ roli na liście

    ![Usługa Azure App Service w ramach ról usługi Azure Stack][2]

1. Gdy stan nowych wystąpień roli jest **gotowe**, wróć do **zestawu skalowania maszyn wirtualnych** i **Usuń** stare wystąpień roli.

1. Powtórz te kroki dla **każdego** zestawu skalowania maszyn wirtualnych.

## <a name="next-steps"></a>Kolejne kroki

Możesz również wypróbować inne [platformy jako usługi (PaaS)](azure-stack-tools-paas-services.md).

* [Dostawcy zasobów programu SQL Server](azure-stack-sql-resource-provider-deploy.md)
* [Dostawcy zasobów bazy danych MySQL](azure-stack-mysql-resource-provider-deploy.md)

<!--Image references-->
[1]: ./media/azure-stack-app-service-fault-domain-update/app-service-scale-sets.png
[2]: ./media/azure-stack-app-service-fault-domain-update/app-service-roles.png
