---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/03/2020
ms.author: dacoulte
ms.openlocfilehash: 7100231e1cc4590d61724668f3941591c6b00206
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80758335"
---
|Nazwa |Opis |Efekt(-y) |Wersja |GitHub |
|---|---|---|---|---|
|[Należy włączyć tylko bezpieczne połączenia z pamięcią podręczną Redis](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |Inspekcja włączanie tylko połączeń za pośrednictwem protokołu SSL do pamięci podręcznej Redis. Korzystanie z bezpiecznych połączeń zapewnia uwierzytelnianie między serwerem a usługą i chroni przesyłane dane przed atakami warstw sieciowych, takimi jak man-in-the-middle, podsłuchiwanie i przejmowanie sesji |Inspekcja, Odmowa, Wyłączona |1.0.0 |[Link](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json)
