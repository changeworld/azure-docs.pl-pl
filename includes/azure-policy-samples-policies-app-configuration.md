---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/12/2020
ms.author: dacoulte
ms.openlocfilehash: bd86457bccb417dc086355b856a5956b83f30fa8
ms.sourcegitcommit: c29b7870f1d478cec6ada67afa0233d483db1181
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79381635"
---
|Name (Nazwa) |Opis |Efekt (s) |Wersja |GitHub |
|---|---|---|---|---|
|[Konfiguracja aplikacji powinna używać klucza zarządzanego przez klienta](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F967a4b4b-2da9-43c1-b7d0-f98d0d74d0b1) |Ta zasada przeprowadza inspekcję wszystkich wystąpień konfiguracji aplikacji, które nie korzystają z klucza zarządzanego przez klienta. |Inspekcja, wyłączona |1.0.0 |[Link](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Configuration/CustomerManagedKey_Audit.json)
|[W przypadku konfiguracji aplikacji należy użyć prywatnego linku](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fca610c1d-041c-4332-9d88-7ed3094967c7) |Ta zasada przeprowadza inspekcję wszystkich wystąpień konfiguracji aplikacji, które nie korzystają z prywatnego linku. |AuditIfNotExists, wyłączone |1.0.0 |[Link](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Configuration/PrivateLink_Audit.json)
