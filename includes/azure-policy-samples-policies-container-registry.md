---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/12/2020
ms.author: dacoulte
ms.openlocfilehash: a1b12a72434034ecc6cbe527cc3de6454e4293a0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79381759"
---
|Nazwa |Opis |Efekt(-y) |Wersja |GitHub |
|---|---|---|---|---|
|[Rejestry kontenerów powinny być szyfrowane przy pomocą klucza zarządzanego przez klienta (CMK)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5b9159ae-1701-4a6f-9a7a-aa9c8ddd0580) |Rejestry kontenerów inspekcji, które nie mają włączonego szyfrowania za pomocą kluczy zarządzanych przez klienta (CMK). Aby uzyskać więcej informacji na temat https://aka.ms/acr/CMKszyfrowania CMK, odwiedź stronę: . |Inspekcja, wyłączone |1.0.0-podgląd |[Link](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_CMKEncryptionEnabled_Audit.json)
|[Rejestry kontenerów nie powinny zezwalać na nieograniczony dostęp do sieci](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd0793b48-0edc-4296-a390-4c75d1bdfd71) |Rejestry kontenerów inspekcji, które nie mają żadnych reguł sieci (IP lub VNET) skonfigurowane i domyślnie zezwalają na dostęp do sieci. Rejestry kontenerów z co najmniej jedną regułą IP/ Zapora lub skonfigurowana siecią wirtualną zostaną uznane za zgodne. Aby uzyskać więcej informacji na temat https://aka.ms/acr/vnetreguł sieci rejestru kontenerów, odwiedź stronę: . |Inspekcja, wyłączone |1.0.0-podgląd |[Link](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_NetworkRulesExist_Audit.json)
