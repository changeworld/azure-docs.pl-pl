---
title: Monitorowanie zgodności usługi Azure Security Center przy użyciu interfejsu API REST | Dokumentacja firmy Microsoft
description: Przejrzyj wyniki skanowania zgodności automatycznych przy użyciu interfejsu API REST usługi Azure Security Center.
services: security-center
documentationcenter: na
author: rloutlaw
manager: angerobe
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/23/2018
ms.author: rloutlaw
ms.openlocfilehash: 3f07928897df01f5d654fa6a6bffce14290b24e4
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/10/2018
ms.locfileid: "44295314"
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






