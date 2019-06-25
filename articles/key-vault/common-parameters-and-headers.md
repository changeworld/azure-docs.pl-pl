---
title: Typowe parametry i nagłówki
description: Parametry i nagłówki wspólne dla wszystkich operacji, które może wykonywać związane z zasobami usługi Key Vault.
services: key-vault
author: msmbaldwin
manager: barbkess
tags: azure-resource-manager
ms.service: key-vault
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: mbaldwin
ms.openlocfilehash: 01ac02ca0e449dcac8fcd05450566fd8138acba3
ms.sourcegitcommit: 1289f956f897786090166982a8b66f708c9deea1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/17/2019
ms.locfileid: "64696686"
---
# <a name="common-parameters-and-headers"></a>Typowe parametry i nagłówki

Wspólne dla wszystkich operacji, które może wykonywać związane z zasobami usługi Key Vault są następujące informacje:

- Zastąp `{api-version}` z wersją interfejsu api w identyfikatorze URI.
- Zastąp `{subscription-id}` identyfikatorem subskrypcji, w identyfikatorze URI
- Zastąp `{resource-group-name}` z grupą zasobów. Aby uzyskać więcej informacji zobacz Używanie grup zasobów do zarządzania zasobami platformy Azure.
- Zastąp `{vault-name}` nazwą magazynu kluczy w identyfikatorze URI.
- Ustaw nagłówek Content-Type application/json.
- Ustaw nagłówek autoryzacji JSON Web Token uzyskany z usługi Azure Active Directory (AAD). Aby uzyskać więcej informacji, zobacz [uwierzytelniania usługi Azure Resource Manager](authentication-requests-and-responses.md) żądań.

## <a name="common-error-response"></a>Odpowiedzi na typowe błąd
Kody stanu HTTP będzie używane przez usługę do wskazania powodzenia lub niepowodzenia. Ponadto błędy zawierają odpowiedzi w następującym formacie:

```
   {  
     "error": {  
     "code": "BadRequest",  
     "message": "The key vault sku is invalid."  
     }  
   }  
```

|Nazwa elementu | Typ | Opis |
|---|---|---|
| code | string | Typ błędu, który wystąpił.|
| message | string | Opis co było przyczyną błędu. |



## <a name="see-also"></a>Zobacz też
 [Dokumentacja interfejsu API REST usługi Azure Key Vault](/rest/api/keyvault/)
