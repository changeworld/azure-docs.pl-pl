---
title: Przejrzyj danych rozliczeń subskrypcji platformy Azure za pomocą interfejsu API REST | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać interfejsów API REST usługi Azure, aby przejrzeć szczegółowe informacje dotyczące rozliczeń subskrypcji.
services: billing
documentationcenter: na
author: lleonard-msft
manager: MBaldwin
editor: ''
ms.assetid: 82D50B98-40F2-44B1-A445-4391EA9EBBAA
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/06/2018
ms.author: alleonar
ms.openlocfilehash: cc29d1f613af67604d50654be794cc90080098bb
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37063924"
---
# <a name="review-subscription-billing-using-rest-apis"></a>Przejrzyj rozliczeń subskrypcji przy użyciu interfejsów API REST

Azure Pomoc raportowania na interfejsy API Przejrzyj i zarządzanie nimi kosztów platformy Azure.  

Filtry pomóc dostosować wyniki do własnych potrzeb.

W tym miejscu możesz dowiedzieć się użyć interfejsu API REST w celu zwracania subskrypcji szczegółów rozliczeń dla danego zakresu.

``` http
GET https://management.azure.com/subscriptions/${subscriptionID}/providers/Microsoft.Billing/billingPeriods/${billingPeriod}/providers/Microsoft.Consumption/usageDetails?$filter=properties/usageEnd ge '${startDate}' AND properties/usageEnd le '${endDate}'
Content-Type: application/json   
Authorization: Bearer
```

## <a name="build-the-request"></a>Żądanie kompilacji  

`{subscriptionID}` Parametr jest wymagany i identyfikuje subskrypcji docelowej.

`{billingPeriod}` Parametr jest wymagany i określa bieżącej [okresie rozliczeniowym](https://docs.microsoft.com/rest/api/billing/billingperiods/get#billingperiod).

`${startDate}` i `${endDate}` parametry są wymagane w tym przykładzie, ale opcjonalne dla punktu końcowego.  Określa zakres dat jako ciągi w postaci RRRR-MM-DD (przykłady: `'20180501'` i `'20180615'`). 

Wymagane są następujące nagłówki: 

|Nagłówek żądania|Opis|  
|--------------------|-----------------|  
|*Typ zawartości:*|Wymagany. Ustaw `application/json`.|  
|*Autoryzacji:*|Wymagany. Ustaw prawidłową `Bearer` [token dostępu](https://docs.microsoft.com/rest/api/azure/#authorization-code-grant-interactive-clients). |  

## <a name="response"></a>Odpowiedź  

Kod stanu 200 (OK) jest zwracana dla pomyślnej odpowiedzi, która zawiera listę szczegółowych kosztów dla Twojego konta.

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
        "usageQuantity": ${usageQuantity},
        "billableQuantity": ${billableQuantity},
        "pretaxCost": ${cost},
        "meterId": "${meterID}",
        "meterDetails": ${meterDetails}
      }
    }
    ],
    "nextLink": "${nextLinkURL}"
} 
```  

Każdy element **wartość** reprezentuje szczegóły dotyczące korzystania z usługi:

|Właściwość Response|Opis|
|----------------|----------|
|**subscriptionGuid** | Globalnie unikatowy identyfikator subskrypcji. | 
|**datą rozpoczęcia** | Data używana uruchomiona. |
|**datą zakończenia** | Data używanie upłynął. |
|**useageQuantity** | Ilość używane. | 
|**billableQuantity** | Ilość faktycznie rozliczane. |
|**pretaxCost** | Koszt fakturowania przed podatków. | 
|**meterDetails** | Szczegółowe informacje na temat używania. |
|**nextLink**| Gdy są ustawione, określa adres URL dla następnego "page" szczegóły. Puste, gdy strona jest ostatnim blokiem. |  
||
  
W tym przykładzie jest skrócona; zobacz [listy Szczegóły użycia](https://docs.microsoft.com/rest/api/consumption/usagedetails/listbybillingperiod#usagedetailslistresult) pełny opis każdego pola odpowiedzi. 

Pozostałe kody stanu wskazują na warunki błędu. W takich przypadkach obiekt odpowiedzi wyjaśnia, dlaczego żądanie zawiodło.

``` json
{  
  "error": [  
    { "code": "Error type." 
      "message": "Error response describing why the operation failed."  
    }  
  ]  
}  
```  

## <a name="next-steps"></a>Kolejne kroki 
- Przegląd [Enterprise Omówienie raportowania](https://docs.microsoft.com/azure/billing/billing-enterprise-api)
- Zbadaj [Enterprise rozliczeń interfejsu API REST](https://docs.microsoft.com/rest/api/billing/)   
- [Wprowadzenie do interfejsu API REST Azure](https://docs.microsoft.com/rest/api/azure/)   
