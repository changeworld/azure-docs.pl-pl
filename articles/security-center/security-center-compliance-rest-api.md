---
title: Monitorowanie zgodności usługi Azure Security Center przy użyciu interfejsu API REST | Dokumentacja firmy Microsoft
description: Przejrzyj wyniki skanowania zgodności automatycznych przy użyciu interfejsu API REST usługi Azure Security Center.
services: security-center
documentationcenter: na
author: rloutlaw
manager: angerobe
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/23/2018
ms.author: rloutlaw
ms.openlocfilehash: 651d7737258f1b1b17c8392a09882388ddf95635
ms.sourcegitcommit: 58c5cd866ade5aac4354ea1fe8705cee2b50ba9f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/24/2018
ms.locfileid: "42820265"
---
# <a name="review-security-center-compliance-results-using-the-azure-rest-apis"></a>Przejrzyj wyniki sprawdzania zgodności usługi Security Center przy użyciu interfejsów API REST platformy Azure

W tym artykule dowiesz się, można pobrać informacji o zgodności zabezpieczeń dla listy subskrypcji przy użyciu interfejsów API REST platformy Azure.

## <a name="review-compliance-for-each-subscription"></a>Przeglądanie zgodności dla każdej subskrypcji

Poniższym przykładzie pobiera informacje o zabezpieczeniach do oceny zgodności danego i subskrypcji za pomocą [zachowania zgodności z Pobierz prawnymi](/rest/api/securitycenter/compliances/get) operacji.

```http
GET https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Security/compliances/{complianceName}?api-version=2017-08-01-preview
```

`{complianceName}` Parametr jest wymagany i powinien zawierać nazwę oceny zgodności dla `{subscription-id}`. Dane wyjściowe będą zawierać wyniki oceny, takie jak:

```json
{
...
  "properties": {
    "assessmentResult": [
      {
        "segmentType": "Compliant",
        "percentage": 77.777777777777786
      }
    ],
    "resourceCount": 18,
    "assessmentTimestampUtcDate": "2018-01-01T00:00:00Z"
  }
}
```

## <a name="review-compliance-for-multiple-subscriptions"></a>Przeglądanie zgodności w wielu subskrypcjach

Aby uzyskać dane na wiele subskrypcji, wywoływania poniżej najpierw uzyskać listę subskrypcji przy użyciu [subskrypcji listy](/rest/api/resources/subscriptions/list) operacji. Powyższe invoke [zachowania zgodności z Pobierz prawnymi](/rest/api/securitycenter/compliances/get) dla każdego zwróconego identyfikatory subskrypcji.

Wywołanie interfejsu API to:

```http
GET https://management.azure.com/subscriptions?api-version=2016-06-01
```

Zwraca tablicę z wartościami, podobny do następującego. Aby przejrzeć informacje o zgodności dla wszystkich subskrypcji, należy użyć wartości subscriptionId powyższego wywołania.

```json
"value": [
    {
      "id": "/subscriptions/{subscription-id}",
      "subscriptionId": "{subscription-id}",
      "displayName": "Demo subscription",
      ...
    }
```






