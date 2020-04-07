---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/03/2020
ms.author: dacoulte
ms.openlocfilehash: 6497a1444b3f502d3e5169ff8ace2884e4e045c9
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80758782"
---
|Nazwa |Opis |Efekt(-y) |Wersja |GitHub |
|---|---|---|---|---|
|[Rejestry kontenerów powinny być szyfrowane przy pomocą klucza zarządzanego przez klienta (CMK)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5b9159ae-1701-4a6f-9a7a-aa9c8ddd0580) |Rejestry kontenerów inspekcji, które nie mają włączonego szyfrowania za pomocą kluczy zarządzanych przez klienta (CMK). Aby uzyskać więcej informacji na temat [https://aka.ms/acr/CMK](https://aka.ms/acr/CMK)szyfrowania CMK, odwiedź stronę: . |Inspekcja, wyłączone |1.0.0-podgląd |[Link](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_CMKEncryptionEnabled_Audit.json)
|[Rejestry kontenerów nie powinny zezwalać na nieograniczony dostęp do sieci](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd0793b48-0edc-4296-a390-4c75d1bdfd71) |Rejestry kontenerów inspekcji, które nie mają żadnych reguł sieci (IP lub VNET) skonfigurowane i domyślnie zezwalają na dostęp do sieci. Rejestry kontenerów z co najmniej jedną regułą IP/ Zapora lub skonfigurowana siecią wirtualną zostaną uznane za zgodne. Aby uzyskać więcej informacji na temat [https://aka.ms/acr/vnet](https://aka.ms/acr/vnet)reguł sieci rejestru kontenerów, odwiedź stronę: . |Inspekcja, wyłączone |1.0.0-podgląd |[Link](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_NetworkRulesExist_Audit.json)
