---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/02/2020
ms.author: dacoulte
ms.openlocfilehash: 29bfe8554573ed087848f3cb7ef8e9f92752f87d
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2020
ms.locfileid: "78262211"
---
|Name (Nazwa) |Opis |Efekt (s) |Wersja |GitHub |
|---|---|---|---|---|
|[W klastrach Service Fabric Właściwość ClusterProtectionLevel ma ustawioną wartość EncryptAndSign](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F617c02be-7f02-4efd-8836-3180d47b6c68) |Usługa Service Fabric udostępnia trzy poziomy ochrony (Brak, logowania i EncryptAndSign) do komunikacji między węzłami, przy użyciu certyfikatu klastra podstawowego. Ustaw poziom ochrony, aby upewnić się, że wszystkie komunikaty między węzłami są szyfrowane i podpisane cyfrowo |Inspekcja, wyłączona |1.0.0 |[Link](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Fabric/ServiceFabric_AuditClusterProtectionLevel_Audit.json)
|[W klastrach Service Fabric należy używać tylko Azure Active Directory do uwierzytelniania klientów](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb54ed75b-3e1a-44ac-a333-05ba39b99ff0) |Inspekcja użycia uwierzytelniania klienta tylko za pośrednictwem Azure Active Directory w Service Fabric |Inspekcja, wyłączona |1.0.0 |[Link](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Fabric/ServiceFabric_AuditADAuth_Audit.json)
