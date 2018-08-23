---
title: Typowe parametry i nagłówki
description: Parametry i nagłówki wspólne dla wszystkich operacji, które może wykonywać związane z zasobami usługi Key Vault.
services: key-vault
documentationcenter: ''
author: bryanla
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: a715d13ca9-d6e8-4e54-ac5e-0ed9400fb15b15d13ca9-d6e8-4e54-ac5e-0ed9400fb15b
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/09/2018
ms.author: bryanla
ms.openlocfilehash: a319dc670b5b1dab163b2d3aa623fc4fb9ce1c3a
ms.sourcegitcommit: 0fcd6e1d03e1df505cf6cb9e6069dc674e1de0be
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/14/2018
ms.locfileid: "42056946"
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

   {  
     "error": {  
     "code": "Element BadRequest",  
     "message": "jednostka sku magazynu kluczy jest nieprawidłowy."  
     }  
   }  

|Nazwa elementu | Typ | Opis |
|---|---|---|
| Kod | ciąg | Typ błędu, który wystąpił.|
| message | ciąg | Opis co było przyczyną błędu. |



## <a name="see-also"></a>Zobacz też
 [Dokumentacja interfejsu API REST usługi Azure Key Vault](/rest/api/keyvault/)
