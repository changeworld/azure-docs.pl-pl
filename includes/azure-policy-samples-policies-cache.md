---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/02/2020
ms.author: dacoulte
ms.openlocfilehash: e4edc91e5b3d99b2d05614c228b9ff5c3cc69d14
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2020
ms.locfileid: "78262204"
---
|Name (Nazwa) |Opis |Efekt (s) |Wersja |GitHub |
|---|---|---|---|---|
|[Należy włączyć tylko bezpieczne połączenia z Redis Cache](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |Inspekcja włączenia tylko połączeń za pośrednictwem protokołu SSL do Redis Cache. Użycie bezpiecznych połączeń zapewnia uwierzytelnianie między serwerem a usługą i chroni dane przesyłane z ataków warstwy sieciowej, takich jak ataki typu man-in-the-Middle, podsłuchiwanie i przejmowanie sesji |Inspekcja, Odmów, wyłączone |1.0.0 |[Link](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json)
