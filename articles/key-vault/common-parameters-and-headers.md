---
title: Wspólne parametry i nagłówki
description: Parametry i nagłówków wspólne dla wszystkich operacji, które może wykonywać związane z zasobów magazynu kluczy.
services: key-vault
documentationcenter: ''
author: lleonard-msft
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: a715d13ca9-d6e8-4e54-ac5e-0ed9400fb15b15d13ca9-d6e8-4e54-ac5e-0ed9400fb15b
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/09/2018
ms.author: alleonar
ms.openlocfilehash: ead1ac550c9b7c489edefd35d5672a9955e78255
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/10/2018
---
# <a name="common-parameters-and-headers"></a>Wspólne parametry i nagłówki

Następujące informacje są wspólne dla wszystkich operacji, które może wykonać związane z zasobami usługi Key Vault:

- Zastąp `{api-version}` z wersją interfejsu api w identyfikatorze URI.
- Zastąp `{subscription-id}` z identyfikatorem subskrypcji w identyfikatorze URI
- Zastąp `{resource-group-name}` z grupą zasobów. Aby uzyskać więcej informacji zobacz Używanie grup zasobów do zarządzania zasobami platformy Azure.
- Zastąp `{vault-name}` nazwą magazynu kluczy w identyfikatorze URI.
- Ustawić nagłówek typu zawartości application/json.
- Ustaw nagłówek autoryzacji żetonu Web JSON, który można uzyskać z usługi Azure Active Directory (AAD). Aby uzyskać więcej informacji, zobacz [uwierzytelniania usługi Azure Resource Manager](authentication-requests-and-responses.md) żądań.

## <a name="common-error-response"></a>Typowe odpowiedzi na błąd
Kody stanu HTTP będzie używać usługa programu do wskazania powodzenia lub niepowodzenia. Ponadto błędy zawierają odpowiedzi w następującym formacie:

   {  
     "błąd": {  
     'code': "Element BadRequest",  
     "komunikat": "sku magazynu kluczy jest nieprawidłowy."  
     }  
   }  

|Nazwa elementu | Typ | Opis |
|---|---|---|
| Kod | ciąg | Typ błędu, który wystąpił.|
| message | ciąg | Opis co spowodowało błąd. |



## <a name="see-also"></a>Zobacz też
 [Dokumentacja interfejsu API REST usługi Azure Key Vault](/rest/api/keyvault/)
