---
title: Interfejsy API usługi pomiarowej marketplace | Azure Marketplace
description: Zdarzenie użycia dla ofert SaaS w portalu Azure Marketplace.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 07/11/2019
ms.openlocfilehash: 315f36e5aed9dee0a89e1f9f504b18a6bed806e0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80275751"
---
# <a name="marketplace-metering-service-apis"></a>Interfejsy API usługi pomiaru w witrynie Marketplace

Interfejs API zdarzenia użycia umożliwia emitowanie zdarzeń użycia dla określonej zakupionej encji. Żądanie zdarzenia użycia odwołuje się do wymiaru usług pomiarowych zdefiniowanych przez wydawcę podczas publikowania oferty.

## <a name="usage-event"></a>Zdarzenie użycia

**POST**:`https://marketplaceapi.microsoft.com/api/usageEvent?api-version=<ApiVersion>`

*Parametry kwerendy:*

|            |          |
| ---------- | ---------------------- |
| `ApiVersion` | Wersja operacji do użycia dla tego żądania. Najnowsza wersja INTERFEJSU API to 2018-08-31. |

*Nagłówki żądań:*

| Typ zawartości       | `application/json`    |
| ------------------ | ---------------------------- |
| `x-ms-requestid`     | Unikatowa wartość ciągu do śledzenia żądania od klienta, najlepiej identyfikator GUID. Jeśli ta wartość nie zostanie podana, zostanie wygenerowana i podana w nagłówkach odpowiedzi. |
| `x-ms-correlationid` | Unikatowa wartość ciągu dla operacji na kliencie. Ten parametr koreluje wszystkie zdarzenia z operacji klienta ze zdarzeniami po stronie serwera. Jeśli ta wartość nie zostanie podana, zostanie wygenerowana i podana w nagłówkach odpowiedzi. |
| `authorization`   | [Pobierz token na okaziciela tokenu sieci Web JSON (JWT).](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-registration#get-a-token-based-on-the-azure-ad-app) Uwaga: Podczas wysyłania żądania `Bearer` HTTP prefiks do tokenu uzyskany z łącza, do którego istnieje odwołanie. |

*Żądanie:*

```json
{
  "resourceId": "Identifier of the resource against which usage is emitted",
  "quantity": 5.0,
  "dimension": "Dimension identifier",
  "effectiveStartTime": "Time in UTC when the usage event occurred",
  "planId": "Plan associated with the purchased offer"
}
```

### <a name="responses"></a>Odpowiedzi

Kod: 200<br>
OK 

```json
{
  "usageEventId": "Unique identifier associated with the usage event",
  "status": "Accepted",
  "messageTime": "Time this message was created in UTC",
  "resourceId": "Identifier of the resource against which usage is emitted",
  "quantity": 5.0,
  "dimension": "Dimension identifier",
  "effectiveStartTime": "Time in UTC when the usage event occurred",
  "planId": "Plan associated with the purchased offer"
}
```

Kod: 400 <br>
Złe żądanie, brakujące lub nieprawidłowe dane dostarczone lub wygasły

```json
{
  "message": "One or more errors have occurred.",
  "target": "usageEventRequest",
  "details": [
    {
      "message": "The resourceId is required.",
      "target": "ResourceId",
      "code": "BadArgument"
    }
  ],
  "code": "BadArgument"
}
```

Kod: 403<br>
Złe żądanie, brakujące lub nieprawidłowe dane dostarczone lub wygasły

```json
{
  "code": "Forbidden",
  "message": "User is not allowed authorized to call this"
}
```

Kod: 409<br>
Konflikt, gdy otrzymamy wywołanie użycia dla identyfikatora zasobu użycia i efektywne użycie, które już istnieje. Odpowiedź będzie `additionalInfo` zawierać pole zawierające informacje o zaakceptowanym komunikacie.

```json
{
  "code": "Conflict",
  "additionalInfo": {
    "usageEventId": "Unique identifier associated with the usage event",
    "status": "Accepted|NotProcessed|Expired",
    "messageTime": "Time this message was created in UTC",
    "resourceId": "Identifier of the resource against which usage is emitted",
    "quantity": 5.0,
    "dimension": "Dimension identifier",
    "effectiveStartTime": "Time in UTC when the usage event occurred",
    "planId": "Plan associated with the purchased offer"
  }
}
```

## <a name="batch-usage-event"></a>Zdarzenie użycia wsadowego

Interfejs API zdarzenia użycia wsadowego umożliwia emitowanie zdarzeń użycia dla więcej niż jednej zakupionej jednostki naraz. Żądanie zdarzenia użycia partii odwołuje się do wymiaru usług pomiarowych zdefiniowanych przez wydawcę podczas publikowania oferty.

>[!Note]
>W komercyjnym rynku firmy Microsoft można zarejestrować wiele ofert SaaS. Każda zarejestrowana oferta SaaS ma unikatową aplikację usługi Azure AD, która jest zarejestrowana do celów uwierzytelniania i autoryzacji. Zdarzenia emitowane w partii powinny należeć do ofert z tą samą aplikacją usługi Azure AD w momencie rejestrowania oferty.

**POST:**`https://marketplaceapi.microsoft.com/api/batchUsageEvent?api-version=<ApiVersion>`

*Parametry kwerendy:*

|            |     |
| ---------- | -------------------- |
| `ApiVersion` | Wersja operacji do użycia dla tego żądania. Najnowsza wersja INTERFEJSU API to 2018-08-31. |

*Nagłówki żądań:*

| Typ zawartości       | `application/json`       |
| ------------------ | ------ |
| `x-ms-requestid`     | Unikatowa wartość ciągu do śledzenia żądania od klienta, najlepiej identyfikator GUID. Jeśli ta wartość nie zostanie podana, zostanie wygenerowana i podana w nagłówkach odpowiedzi. |
| `x-ms-correlationid` | Unikatowa wartość ciągu dla operacji na kliencie. Ten parametr koreluje wszystkie zdarzenia z operacji klienta ze zdarzeniami po stronie serwera. Jeśli ta wartość nie zostanie podana, zostanie wygenerowana i podana w nagłówkach odpowiedzi. |
| `authorization`      | [Pobierz token na okaziciela tokenu sieci Web JSON (JWT).](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-registration#get-a-token-based-on-the-azure-ad-app) Uwaga: Podczas wysyłania żądania `Bearer` HTTP prefiks do tokenu uzyskany z łącza, do którego istnieje odwołanie.  |

*Żądanie:*
```json
{
  "request": [
    {
      "resourceId": "Identifier of the resource against which usage is emitted",
      "quantity": 5.0,
      "dimension": "Dimension identifier",
      "effectiveStartTime": "Time in UTC when the usage event occurred",
      "planId": "Plan associated with the purchased offer"
    },
    {
      "resourceId": "Identifier of the resource against which usage is emitted",
      "quantity": 5.0,
      "dimension": "Dimension identifier",
      "effectiveStartTime": "Time in UTC when the usage event occurred",
      "planId": "Plan associated with the purchased offer"
    }
  ]
}
```
### <a name="responses"></a>Odpowiedzi

Kod: 200<br>
OK

```json
{
  "count": 2,
  "result": [
    {
      "usageEventId": "Unique identifier associated with the usage event",
      "status": "Accepted|Expired|Duplicate|Error|ResourceNotFound|ResourceNotAuthorized|InvalidDimension|BadArgument",
      "messageTime": "Time this message was created in UTC",
      "resourceId": "Identifier of the resource against which usage is emitted",
      "quantity": 5.0,
      "dimension": "Dimension identifier",
      "effectiveStartTime": "Time in UTC when the usage event occurred",
      "planId": "Plan associated with the purchased offer",
      "error": "Error object (optional)"
    },
    {
      "usageEventId": "Unique identifier associated with the usage event",
      "status": "Accepted|Expired|Duplicate|Error|ResourceNotFound|ResourceNotAuthorized|InvalidDimension|BadArgument",
      "messageTime": "Time this message was created in UTC",
      "resourceId": "Identifier of the resource against which usage is emitted",
      "quantity": 5.0,
      "dimension": "Dimension identifier",
      "effectiveStartTime": "Time in UTC when the usage event occurred",
      "planId": "Plan associated with the purchased offer",
      "error": "Error object (optional)"
    }
  ]
}
```

Opis kodu stanu, `BatchUsageEvent` do którego odwołuje się odpowiedź interfejsu API:

| Kod stanu  | Opis |
| ---------- | -------------------- |
| `Accepted` | Zaakceptowany kod. |
| `Expired` | Wygasłe użycie. |
| `Duplicate` | Zduplikowane użycie pod warunkiem. |
| `Error` | Kod błędu. |
| `ResourceNotFound` | Podany zasób użycia jest nieprawidłowy. |
| `ResourceNotAuthorized` | Użytkownik nie jest autoryzowany do zapewnienia użycia dla tego zasobu. |
| `InvalidDimension` | Wymiar, dla którego zostało przekazane użycie jest nieprawidłowy dla tej oferty/planu. |
| `InvalidQuantity` | Przekazana ilość jest < 0. |
| `BadArgument` | Brak danych wejściowych lub zniekształcone. |

Kod: 400<br>
Złe żądanie, brakujące lub nieprawidłowe dane dostarczone lub wygasły

```json
{
  "message": "One or more errors have occurred.",
  "target": "usageEventRequest",
  "details": [
    {
      "message": "Invalid data format.",
      "target": "usageEventRequest",
      "code": "BadArgument"
    }
  ],
  "code": "BadArgument"
}
```
Kod: 403<br>
Użytkownik jest nieautoryzowany do nawiązać połączenie

```json
{
  "code": "Forbidden",
  "message": "User is not allowed to call this"
}
```

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji, zobacz [SaaS taryfowych rozliczeń](./saas-metered-billing.md).
