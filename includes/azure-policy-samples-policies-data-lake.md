---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/02/2020
ms.author: dacoulte
ms.openlocfilehash: 16576e4076ebb6d361a4b0db699997edf83393ea
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80623983"
---
|Nazwa |Opis |Efekt(-y) |Wersja |GitHub |
|---|---|---|---|---|
|[Dzienniki diagnostyczne w magazynie usługi Azure Data Lake powinny być włączone](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F057ef27e-665e-4328-8ea3-04b3122bd9fb) |Inspekcja włączanie dzienników diagnostycznych. Dzięki temu można odtworzyć szlaki aktywności do wykorzystania w celach dochodzeniowych; w przypadku wystąpienia zdarzenia zabezpieczającego lub naruszenia zabezpieczeń sieci |AuditIfNotExists, Wyłączone |2.0.0 |[Link](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Lake/DataLakeStore_AuditDiagnosticLog_Audit.json)
|[Dzienniki diagnostyczne w usłudze Data Lake Analytics powinny być włączone](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc95c74d9-38fe-4f0d-af86-0c7d626a315c) |Inspekcja włączanie dzienników diagnostycznych. Dzięki temu można odtworzyć szlaki aktywności do wykorzystania w celach dochodzeniowych; w przypadku wystąpienia zdarzenia zabezpieczającego lub naruszenia zabezpieczeń sieci |AuditIfNotExists, Wyłączone |2.0.0 |[Link](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Lake/DataLakeAnalytics_AuditDiagnosticLog_Audit.json)
|[Wymagaj szyfrowania na kontach usługi Data Lake Store](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa7ff3161-0087-490a-9ad9-ad6217f4f43a) |Ta zasada gwarantuje, że szyfrowanie jest włączone na wszystkich kontach usługi Data Lake Store |Odmów |1.0.0 |[Link](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Lake/DataLakeStoreEncryption_Deny.json)
