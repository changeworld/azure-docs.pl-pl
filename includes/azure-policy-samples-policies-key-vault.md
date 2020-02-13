---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/10/2020
ms.author: dacoulte
ms.openlocfilehash: 53494bc9642a3df91492d2353a6aa3c6875c1fff
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/12/2020
ms.locfileid: "77172772"
---
|Name (Nazwa) |Opis |Efekt (s) |Wersja |
|---|---|---|---|
|[Wdróż ustawienia diagnostyczne dla Key Vault w centrum zdarzeń](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Key%20Vault/KeyVault_DiagnosticLog_Deploy.json) |Wdraża ustawienia diagnostyczne dla Key Vault w celu przesyłania strumieniowego do regionalnego centrum zdarzeń, gdy jest tworzony lub aktualizowany dowolny Key Vault, dla którego brakuje tych ustawień diagnostycznych. |deployIfNotExists |1.0.0 |
|[Dzienniki diagnostyczne w Key Vault powinny być włączone](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Key%20Vault/KeyVault_AuditDiagnosticLog_Audit.json) |Inspekcja włączenia dzienników diagnostycznych. Pozwala to na ponowne utworzenie śladów aktywności do użycia w celach dochodzeniowych; gdy wystąpi zdarzenie związane z bezpieczeństwem lub w przypadku naruszenia bezpieczeństwa sieci |AuditIfNotExists, wyłączone |1.0.0 |
|[Key Vault obiektów powinno być możliwe do odzyskania](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Key%20Vault/KeyVault_Recoverable_Audit.json) |Ta zasada przeprowadza inspekcję w przypadku braku możliwości odzyskania obiektów magazynu kluczy. Funkcja usuwania nietrwałego pomaga efektywnie przechowywać zasoby dla danego okresu przechowywania (90 dni) nawet po operacji usuwania, jednocześnie zapewniając, że obiekt jest usuwany. Gdy jest włączona ochrona przed czyszczeniem, nie można wyczyścić magazynu lub obiektu w stanie usuniętym, dopóki nie zostanie przekroczony okres przechowywania 90 dni. Te magazyny i obiekty nadal mogą być odzyskiwane, dzięki czemu klienci będą przestrzegani zasad przechowywania. |Inspekcja, wyłączona |1.0.0 |
