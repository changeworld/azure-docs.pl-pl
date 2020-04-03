---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/02/2020
ms.author: dacoulte
ms.openlocfilehash: 0cfa5cfa16237aa26cdcb23dddd0aca11a503bb1
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80624614"
---
|Nazwa |Opis |Efekt(-y) |Wersja |GitHub |
|---|---|---|---|---|
|[Konfiguracja aplikacji powinna używać klucza zarządzanego przez klienta](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F967a4b4b-2da9-43c1-b7d0-f98d0d74d0b1) |Ta zasada przeprowadza inspekcję dowolnego wystąpienia konfiguracji aplikacji, które nie używa klucza zarządzanego przez klienta. |Inspekcja, wyłączone |1.0.0 |[Link](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Configuration/CustomerManagedKey_Audit.json)
|[Konfiguracja aplikacji powinna używać łącza prywatnego](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fca610c1d-041c-4332-9d88-7ed3094967c7) |Ta zasada przeprowadza inspekcję dowolnego wystąpienia konfiguracji aplikacji, które nie używa łącza prywatnego. |AuditIfNotExists, Wyłączone |1.0.0 |[Link](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Configuration/PrivateLink_Audit.json)
