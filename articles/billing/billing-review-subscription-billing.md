---
title: Przejrzyj dane rozliczeń subskrypcji platformy Azure przy użyciu interfejsu API REST | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać interfejsów API REST usługi Azure, aby przejrzeć szczegółowe informacje dotyczące rozliczeń subskrypcji.
services: billing
documentationcenter: na
author: lleonard-msft
manager: ''
editor: ''
ms.assetid: 82D50B98-40F2-44B1-A445-4391EA9EBBAA
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/06/2018
ms.author: erikre
ms.openlocfilehash: 0a73462b7fdbaf6386a3051a72da755f31ff8dd2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65192126"
---
# <a name="review-subscription-billing-using-rest-apis"></a>Przegląd rozliczeń subskrypcji przy użyciu interfejsów API REST

Pomoc platformy Azure interfejsy API raportowania usługi Przejrzyj i zarządzania kosztami platformy Azure.

Filtry pomagają dostosować wyniki do własnych potrzeb.

W tym miejscu możesz Dowiedz się, jak za pomocą interfejsu API REST do zwrócenia szczegółów rozliczeń subskrypcji w danym zakresie dat.

``` http
GET https://management.azure.com/subscriptions/${subscriptionID}/providers/Microsoft.Billing/billingPeriods/${billingPeriod}/providers/Microsoft.Consumption/usageDetails?$filter=properties/usageEnd ge '${startDate}' AND properties/usageEnd le '${endDate}'
Content-Type: application/json
Authorization: Bearer
```

## <a name="build-the-request"></a>Tworzenie żądania

`{subscriptionID}` Parametr jest wymagany i identyfikuje subskrypcji docelowej.

`{billingPeriod}` Parametr jest wymagany i określa bieżącej [okresu rozliczeniowego](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-billing-periods).

`${startDate}` i `${endDate}` parametry są wymagane dla tego przykładu, ale opcjonalne dla punktu końcowego. Określają zakres dat jako ciąg w postaci RRRR-MM-DD (przykłady: `'20180501'` i `'20180615'`).

Wymagane są następujące nagłówki:

|Nagłówek żądania|Opis|
|--------------------|-----------------|
|*Content-Type:*|Wymagany. Ustaw `application/json`.|
|*Authorization:*|Wymagany. Ustawić prawidłową `Bearer` [token dostępu](https://docs.microsoft.com/rest/api/azure/#authorization-code-grant-interactive-clients). |

## <a name="response"></a>Odpowiedź

Zwróciła kod stanu 200 (OK) pomyślnej odpowiedzi zawiera listę szczegółowych kosztów dla swojego konta.

``` json
{
  "value": [
    {
      "id": "/subscriptions/{$subscriptionID}/providers/Microsoft.Billing/billingPeriods/201702/providers/Microsoft.Consumption/usageDetails/{$detailsID}",
      "name": "{$detailsID}",
      "type": "Microsoft.Consumption/usageDetails",
      "properties": {
        "billingPeriodId": "/subscriptions/${subscriptionID}/providers/Microsoft.Billing/billingPeriods/${billingPeriod}",
        "invoiceId": "/subscriptions/${subscriptionID}/providers/Microsoft.Billing/invoices/${invoiceID}",
        "usageStart": "${startDate}}",
        "usageEnd": "${endDate}",
        "currency": "USD",
        "usageQuantity": "${usageQuantity}",
        "billableQuantity": "${billableQuantity}",
        "pretaxCost": "${cost}",
        "meterId": "${meterID}",
        "meterDetails": "${meterDetails}"
      }
    }
  ],
  "nextLink": "${nextLinkURL}"
}
```

Każdy element na **wartość** reprezentuje szczegóły dotyczące korzystania z usługi:

|Właściwość Response|Opis|
|----------------|----------|
|**subscriptionGuid** | Globalnie unikatowy identyfikator dla subskrypcji. |
|**startDate** | Data wykorzystania pracę. |
|**endDate** | Data wykorzystania zakończył się. |
|**useageQuantity** | Użytej ilości. |
|**billableQuantity** | Ilość rzeczywiście jest rozliczane. |
|**pretaxCost** | Koszt doliczany do faktury, przed obowiązujących podatków. |
|**meterDetails** | Szczegółowe informacje na temat użycia. |
|**nextLink**| Po ustawieniu, określa adres URL następnego "page" szczegółowe informacje. Pusty, gdy strona jest ostatnim blokiem. |

W tym przykładzie jest skracana; zobacz [uzyskać szczegółowe informacje o liście](https://docs.microsoft.com/rest/api/consumption/usagedetails/list#usagedetailslistforbillingperiod) pełny opis każdego pola w odpowiedzi.

Inne kody stanu wskazują błędy. W takich przypadkach obiekt odpowiedzi wyjaśnia, dlaczego żądanie nie powiodło się.

``` json
{
  "error": [
    {
      "code": "Error type.",
      "message": "Error response describing why the operation failed."
    }
  ]
}
```

## <a name="next-steps"></a>Kolejne kroki
- Przegląd [Przegląd raportowania dla przedsiębiorstw.](https://docs.microsoft.com/azure/billing/billing-enterprise-api)
- Badanie [przedsiębiorstwa, interfejs API REST rozliczeń](https://docs.microsoft.com/rest/api/billing/)
- [Wprowadzenie do interfejsu API REST platformy Azure](https://docs.microsoft.com/rest/api/azure/)
