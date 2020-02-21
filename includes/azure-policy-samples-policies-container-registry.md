---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/19/2020
ms.author: dacoulte
ms.openlocfilehash: 3569f570653207404d6d65e3ed3eab6bb12729f5
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/20/2020
ms.locfileid: "77495386"
---
|Name (Nazwa) |Opis |Efekt (s) |Wersja |Element źródłowy |
|---|---|---|---|
|[Rejestry kontenerów powinny być szyfrowane za pomocą klucza zarządzanego przez klienta (CMK)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5b9159ae-1701-4a6f-9a7a-aa9c8ddd0580) |Inspekcja rejestrów kontenerów, dla których szyfrowanie nie jest włączone z kluczami zarządzanymi przez klienta (CMK). Aby uzyskać więcej informacji na temat szyfrowania CMK, odwiedź stronę: https://aka.ms/acr/CMK. |Inspekcja, wyłączona |1.0.0 — wersja zapoznawcza |[GitHub](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_CMKEncryptionEnabled_Audit.json)
|[Rejestry kontenerów nie powinny zezwalać na nieograniczony dostęp do sieci](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd0793b48-0edc-4296-a390-4c75d1bdfd71) |Przeprowadź inspekcję rejestrów kontenerów, w których nie skonfigurowano żadnych reguł sieci (IP lub VNET) i domyślnie Zezwól na cały dostęp do sieci. Rejestry kontenerów z co najmniej jedną regułą IP/zaporą lub skonfigurowaną siecią wirtualną będą uznawane za zgodne. Aby uzyskać więcej informacji na temat Container Registry reguł sieci, odwiedź stronę: https://aka.ms/acr/vnet. |Inspekcja, wyłączona |1.0.0 — wersja zapoznawcza |[GitHub](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_NetworkRulesExist_Audit.json)
