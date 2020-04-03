---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/02/2020
ms.author: dacoulte
ms.openlocfilehash: e8b79c8a2e83a77f1f0de344306b8cf825c428c6
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80624145"
---
|Nazwa |Opis |Efekt(-y) |Wersja |GitHub |
|---|---|---|---|---|
|[Wszystkie reguły autoryzacji z wyjątkiem funkcji RootManageSharedAccessKey powinny zostać usunięte z obszaru nazw usługi Service Bus](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa1817ec0-a368-432a-8057-8371e17ac6ee) |Klienci usługi Service Bus nie powinni używać zasad dostępu na poziomie obszaru nazw, która zapewnia dostęp do wszystkich kolejek i tematów w obszarze nazw. Aby wyrównać z modelem zabezpieczeń najmniejszych uprawnień, należy utworzyć zasady dostępu na poziomie encji dla kolejek i tematów, aby zapewnić dostęp tylko do określonej encji |Inspekcja, Odmowa, Wyłączona |1.0.1 |[Link](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Bus/ServiceBus_AuditNamespaceAccessRules_Audit.json)
|[Dzienniki diagnostyczne w usłudze Service Bus powinny być włączone](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff8d36e2f-389b-4ee4-898d-21aeb69a0f45) |Inspekcja włączanie dzienników diagnostycznych. Dzięki temu można odtworzyć szlaki aktywności do wykorzystania w celach dochodzeniowych; w przypadku wystąpienia zdarzenia zabezpieczającego lub naruszenia zabezpieczeń sieci |AuditIfNotExists, Wyłączone |2.0.0 |[Link](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Bus/ServiceBus_AuditDiagnosticLog_Audit.json)
