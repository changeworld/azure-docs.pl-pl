---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/10/2020
ms.author: dacoulte
ms.openlocfilehash: 645494688ed1fb2a29efa083d50e9456f6d2b1b4
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/12/2020
ms.locfileid: "77172730"
---
|Name (Nazwa) |Opis |Efekt (s) |Wersja |
|---|---|---|---|
|[Dzienniki diagnostyczne w Azure Data Lake Store powinny być włączone](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Data%20Lake/DataLakeStore_AuditDiagnosticLog_Audit.json) |Inspekcja włączenia dzienników diagnostycznych. Pozwala to na ponowne utworzenie śladów aktywności do użycia w celach dochodzeniowych; gdy wystąpi zdarzenie związane z bezpieczeństwem lub w przypadku naruszenia bezpieczeństwa sieci |AuditIfNotExists, wyłączone |2.0.0 |
|[Dzienniki diagnostyczne w Data Lake Analytics powinny być włączone](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Data%20Lake/DataLakeAnalytics_AuditDiagnosticLog_Audit.json) |Inspekcja włączenia dzienników diagnostycznych. Pozwala to na ponowne utworzenie śladów aktywności do użycia w celach dochodzeniowych; gdy wystąpi zdarzenie związane z bezpieczeństwem lub w przypadku naruszenia bezpieczeństwa sieci |AuditIfNotExists, wyłączone |2.0.0 |
|[Wymagaj szyfrowania na kontach Data Lake Store](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Data%20Lake/DataLakeStoreEncryption_Deny.json) |Te zasady zapewniają, że szyfrowanie jest włączone na wszystkich kontach Data Lake Store |pozbawić |1.0.0 |
