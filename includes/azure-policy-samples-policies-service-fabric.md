---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/12/2020
ms.author: dacoulte
ms.openlocfilehash: efddb110f776bd370756685eb5667ab985f7960b
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "79381788"
---
|Nazwa |Opis |Efekt(-y) |Wersja |GitHub |
|---|---|---|---|---|
|[Klastry sieci szkieletowej usług powinny mieć właściwość ClusterProtectionLevel ustawiona na EncryptAndSign](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F617c02be-7f02-4efd-8836-3180d47b6c68) |Sieć szkieletowa usług zapewnia trzy poziomy ochrony (Brak, Podpisywanie i Szyfrowanie ISign) dla komunikacji węzeł-węzeł przy użyciu certyfikatu klastra podstawowego. Ustaw poziom ochrony, aby upewnić się, że wszystkie komunikaty węzła do węzła są szyfrowane i podpisane cyfrowo |Inspekcja, wyłączone |1.0.0 |[Link](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Fabric/ServiceFabric_AuditClusterProtectionLevel_Audit.json)
|[Klastry sieci szkieletowej usług powinny używać tylko usługi Azure Active Directory do uwierzytelniania klienta](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb54ed75b-3e1a-44ac-a333-05ba39b99ff0) |Inspekcja użycia uwierzytelniania klienta tylko za pośrednictwem usługi Azure Active Directory w sieci szkieletowej usług |Inspekcja, wyłączone |1.0.0 |[Link](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Fabric/ServiceFabric_AuditADAuth_Audit.json)
