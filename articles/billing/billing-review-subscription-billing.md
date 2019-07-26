---
title: Przeglądanie danych rozliczeń subskrypcji platformy Azure za pomocą interfejsu API REST | Microsoft Docs
description: Dowiedz się, jak używać interfejsów API REST platformy Azure, aby przeglądać szczegóły rozliczeń subskrypcji.
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
ms.author: banders
ms.openlocfilehash: 8cfa429b18fb282f5c1f85d2fd1637704653b855
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/24/2019
ms.locfileid: "68443045"
---
# <a name="review-subscription-billing-using-rest-apis"></a>Przegląd rozliczeń subskrypcji przy użyciu interfejsów API REST

Interfejsy API usługi Azure Reporting ułatwiają przeglądanie kosztów platformy Azure i zarządzanie nimi.

Filtry pomagają dostosować wyniki do Twoich potrzeb.

Tutaj dowiesz się, jak używać interfejsu API REST, aby zwrócić szczegóły rozliczania subskrypcji dla danego zakresu dat.

``` http
GET https://management.azure.com/subscriptions/${subscriptionID}/providers/Microsoft.Billing/billingPeriods/${billingPeriod}/providers/Microsoft.Consumption/usageDetails?$filter=properties/usageEnd ge '${startDate}' AND properties/usageEnd le '${endDate}'
Content-Type: application/json
Authorization: Bearer
```

## <a name="build-the-request"></a>Żądanie kompilacji

`{subscriptionID}` Parametr jest wymagany i identyfikuje subskrypcję docelową.

Parametr jest wymagany i określa bieżący [okres rozliczeniowy.](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-billing-periods) `{billingPeriod}`

Parametry `${startDate}` i`${endDate}` są wymagane dla tego przykładu, ale opcjonalne dla punktu końcowego. Określają one zakres dat jako ciągi w postaci rrrr-mm-dd (przykłady: `'20180501'` i `'20180615'`).

Wymagane są następujące nagłówki:

|Nagłówek żądania|Opis|
|--------------------|-----------------|
|*Content-Type:*|Wymagana. Ustaw wartość `application/json`.|
|*Authorization:*|Wymagana. Ustaw prawidłowy `Bearer` [token dostępu](https://docs.microsoft.com/rest/api/azure/#authorization-code-grant-interactive-clients). |

## <a name="response"></a>Odpowiedź

Kod stanu 200 (OK) jest zwracany dla pomyślnej odpowiedzi, która zawiera listę szczegółowych kosztów dla Twojego konta.

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

Każdy element w **wartości** reprezentuje szczegóły dotyczące korzystania z usługi:

|Właściwość odpowiedzi|Opis|
|----------------|----------|
|**subscriptionGuid** | Globalnie unikatowy identyfikator subskrypcji. |
|**startDate** | Data rozpoczęcia użycia. |
|**endDate** | Data zakończenia użycia. |
|**useageQuantity** | Użyta ilość. |
|**billableQuantity** | Liczba rzeczywiście rozliczona. |
|**pretaxCost** | Koszt jest fakturowany, przed zastosowaniem podatków. |
|**meterDetails** | Szczegółowe informacje na temat użycia. |
|**nextLink**| Po ustawieniu określa adres URL dla następnej "strony" szczegółów. Puste, gdy strona jest Ostatnia. |

Ten przykład jest skrócony; Zobacz [szczegóły użycia](https://docs.microsoft.com/rest/api/consumption/usagedetails/list#usagedetailslistforbillingperiod) , aby uzyskać pełny opis każdego pola odpowiedzi.

Inne kody stanu wskazują na błędy. W takich przypadkach obiekt Response wyjaśnia, dlaczego żądanie nie powiodło się.

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
- Przegląd [raportowania Enterprise Reporting](https://docs.microsoft.com/azure/billing/billing-enterprise-api)
- Badanie [interfejsu API REST](https://docs.microsoft.com/rest/api/billing/) rozliczeń w przedsiębiorstwie
- [Wprowadzenie do interfejsu API REST platformy Azure](https://docs.microsoft.com/rest/api/azure/)
