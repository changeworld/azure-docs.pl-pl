---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/03/2020
ms.author: dacoulte
ms.openlocfilehash: 25a96f33addf13242a91372a630043bd8c9087cc
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80758160"
---
|Nazwa |Opis |Efekt(-y) |Wersja |GitHub |
|---|---|---|---|---|
|[Dzienniki diagnostyczne w magazynie usługi Azure Data Lake powinny być włączone](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F057ef27e-665e-4328-8ea3-04b3122bd9fb) |Inspekcja włączanie dzienników diagnostycznych. Dzięki temu można odtworzyć szlaki aktywności do wykorzystania w celach dochodzeniowych; w przypadku wystąpienia zdarzenia zabezpieczającego lub naruszenia zabezpieczeń sieci |AuditIfNotExists, Wyłączone |2.0.0 |[Link](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Lake/DataLakeStore_AuditDiagnosticLog_Audit.json)
|[Dzienniki diagnostyczne w usłudze Data Lake Analytics powinny być włączone](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc95c74d9-38fe-4f0d-af86-0c7d626a315c) |Inspekcja włączanie dzienników diagnostycznych. Dzięki temu można odtworzyć szlaki aktywności do wykorzystania w celach dochodzeniowych; w przypadku wystąpienia zdarzenia zabezpieczającego lub naruszenia zabezpieczeń sieci |AuditIfNotExists, Wyłączone |2.0.0 |[Link](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Lake/DataLakeAnalytics_AuditDiagnosticLog_Audit.json)
|[Wymagaj szyfrowania na kontach usługi Data Lake Store](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa7ff3161-0087-490a-9ad9-ad6217f4f43a) |Ta zasada gwarantuje, że szyfrowanie jest włączone na wszystkich kontach usługi Data Lake Store |Odmów |1.0.0 |[Link](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Lake/DataLakeStoreEncryption_Deny.json)
