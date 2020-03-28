---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/12/2020
ms.author: dacoulte
ms.openlocfilehash: 6663c3db87442642a0251d87609669c1d858388b
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "79381800"
---
|Nazwa |Opis |Efekt(-y) |Wersja |GitHub |
|---|---|---|---|---|
|[Wszystkie reguły autoryzacji z wyjątkiem funkcji RootManageSharedAccessKey powinny zostać usunięte z obszaru nazw Usługi Event Hub](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb278e460-7cfc-4451-8294-cccc40a940d7) |Klienci Centrum zdarzeń nie powinni używać zasad dostępu na poziomie obszaru nazw, która zapewnia dostęp do wszystkich kolejek i tematów w obszarze nazw. Aby wyrównać z modelem zabezpieczeń najmniejszych uprawnień, należy utworzyć zasady dostępu na poziomie encji dla kolejek i tematów, aby zapewnić dostęp tylko do określonej encji |Inspekcja, Odmowa, Wyłączona |1.0.1 |[Link](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Event%20Hub/EventHub_AuditNamespaceAccessRules_Audit.json)
|[Reguły autoryzacji wystąpienia Usługi Event Hub powinny być zdefiniowane](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff4826e5f-6a27-407c-ae3e-9582eb39891d) |Inspekcja istnienia reguł autoryzacji w jednostkach Usługi Event Hub w celu udzielenia dostępu najmniej uprzywilejowanego |AuditIfNotExists, Wyłączone |1.0.0 |[Link](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Event%20Hub/EventHub_AuditEventHubAccessRules_Audit.json)
|[Dzienniki diagnostyczne w Centrum zdarzeń powinny być włączone](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F83a214f7-d01a-484b-91a9-ed54470c9a6a) |Inspekcja włączanie dzienników diagnostycznych. Dzięki temu można odtworzyć szlaki aktywności do wykorzystania w celach dochodzeniowych; w przypadku wystąpienia zdarzenia zabezpieczającego lub naruszenia zabezpieczeń sieci |AuditIfNotExists, Wyłączone |2.0.0 |[Link](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Event%20Hub/EventHub_AuditDiagnosticLog_Audit.json)
