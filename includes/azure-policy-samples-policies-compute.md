---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/10/2020
ms.author: dacoulte
ms.openlocfilehash: 667d017e8febcf1abcc1cfe21ecfaa43ae75ea6d
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/12/2020
ms.locfileid: "77172751"
---
|Name (Nazwa) |Opis |Efekt (s) |Wersja |
|---|---|---|---|
|[Dozwolone jednostki SKU maszyny wirtualnej](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Compute/VMSkusAllowed_Deny.json) |Te zasady umożliwiają określenie zestawu jednostek SKU maszyn wirtualnych, które organizacja może wdrażać. |Zablokuj |1.0.0 |
|[Inspekcja maszyn wirtualnych bez skonfigurowanego odzyskiwania po awarii](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Compute/RecoveryServices_DisasterRecovery_Audit.json) |Inspekcja maszyn wirtualnych, dla których nie skonfigurowano odzyskiwania po awarii. Aby dowiedzieć się więcej na temat odzyskiwania po awarii, odwiedź stronę https://aka.ms/asr-doc. |auditIfNotExists |1.0.0 |
|[Inspekcja maszyn wirtualnych, które nie korzystają z dysków zarządzanych](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Compute/VMRequireManagedDisk_Audit.json) |Ta zasada przeprowadza inspekcję maszyn wirtualnych, które nie korzystają z dysków zarządzanych |wizyjn |1.0.0 |
|[Wdróż domyślne rozszerzenie Microsoft IaaSAntimalware dla systemu Windows Server](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Compute/VMAntimalwareExtension_Deploy.json) |Te zasady wdrażają rozszerzenie Microsoft IaaSAntimalware z konfiguracją domyślną, gdy maszyna wirtualna nie jest skonfigurowana przy użyciu rozszerzenia ochrony przed złośliwym kodem. |deployIfNotExists |1.0.0 |
|[Dzienniki diagnostyczne w Virtual Machine Scale Sets powinny być włączone](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Compute/ServiceFabric_and_VMSS_AuditVMSSDiagnostics.json) |Zaleca się włączenie dzienników, aby można było odtworzyć dziennik aktywności w przypadku, gdy w przypadku incydentu jest wymagane badanie lub naruszenie zabezpieczeń. |AuditIfNotExists, wyłączone |1.0.0 |
|[Program Microsoft chroniący przed złośliwym kodem dla platformy Azure powinien zostać skonfigurowany do automatycznego aktualizowania sygnatur ochrony](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Compute/VirtualMachines_AntiMalwareAutoUpdate_AuditIfNotExists.json) |Ta zasada przeprowadza inspekcję wszystkich maszyn wirtualnych systemu Windows, które nie są skonfigurowane z automatyczną aktualizacją sygnatur ochrony przed złośliwym oprogramowaniem firmy Microsoft. |AuditIfNotExists, wyłączone |1.0.0 |
|[Rozszerzenie Microsoft IaaSAntimalware należy wdrożyć na serwerach z systemem Windows](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Compute/WindowsServers_AntiMalware_AuditIfNotExists.json) |Ta zasada przeprowadza inspekcję wszystkich maszyn wirtualnych z systemem Windows Server bez wdrożonego rozszerzenia Microsoft IaaSAntimalware. |AuditIfNotExists, wyłączone |1.0.0 |
|[Należy zainstalować tylko zatwierdzone rozszerzenia maszyn wirtualnych](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Compute/VirtualMachines_ApprovedExtensions_Audit.json) |Te zasady regulują rozszerzenia maszyny wirtualnej, które nie są zatwierdzone. |Inspekcja, Odmów, wyłączone |1.0.0 |
|[Wymagaj automatycznej poprawki obrazu systemu operacyjnego na Virtual Machine Scale Sets](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Compute/VMSSOSUpgradeHealthCheck_Deny.json) |Te zasady wymuszają włączenie automatycznej poprawki obrazu systemu operacyjnego na Virtual Machine Scale Sets, aby zawsze utrzymywać Virtual Machines zabezpieczonych przez bezpieczne stosowanie najnowszych poprawek zabezpieczeń co miesiąc. |pozbawić |1.0.0 |
|[Niedołączone dyski powinny być szyfrowane](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Compute/UnattachedDisk_Encryption_Audit.json) |Ta zasada przeprowadza inspekcję wszelkich niedołączonych dysków bez włączonego szyfrowania. |Inspekcja, wyłączona |1.0.0 |
|[Maszyny wirtualne należy migrować do nowych zasobów Azure Resource Manager](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Compute/ClassicCompute_Audit.json) |Użyj nowych Azure Resource Manager dla maszyn wirtualnych, aby zapewnić lepsze zabezpieczenia, takie jak: silniejsza kontrola dostępu (RBAC), lepsza Inspekcja, wdrażanie i zarządzanie oparte na usłudze ARM, dostęp do zarządzanych tożsamości, dostęp do magazynu kluczy dla wpisów tajnych, Azure Uwierzytelnianie oparte na usłudze AD i obsługa tagów i grup zasobów w celu łatwiejszego zarządzania zabezpieczeniami |Inspekcja, Odmów, wyłączone |1.0.0 |
