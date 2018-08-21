---
title: Sprawdź użycie zasobów usługi platformy Azure przy użyciu interfejsu API REST | Dokumentacja firmy Microsoft
description: Dowiedz się, jak za pomocą interfejsów API REST platformy Azure Sprawdź użycie zasobów usługi platformy Azure.
services: billing
documentationcenter: na
author: lleonard-msft
manager: MBaldwin
editor: ''
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/15/2018
ms.author: alleonar
ms.openlocfilehash: 1b7b1455413fb4886b317d468e6d278111c094b1
ms.sourcegitcommit: 974c478174f14f8e4361a1af6656e9362a30f515
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/20/2018
ms.locfileid: "40225960"
---
# <a name="review-azure-resource-usage-using-the-rest-api"></a>Sprawdź użycie zasobów platformy Azure przy użyciu interfejsu API REST


Azure [użycia interfejsów API](https://docs.microsoft.com/rest/api/consumption/) pomoc, przejrzyj dane kosztów i użycia zasobów platformy Azure.

W tym artykule dowiesz się, jak pobrać i zagregowane informacje o użyciu zasobów dla zasobów w grupie zasobów platformy Azure również jak filtrować wyniki na podstawie [usługi Azure resource manager tagi](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags).

## <a name="get-usage-for-a-resource-group"></a>Pobierz użycie dla grupy zasobów

Aby uzyskać użycia zasobów obliczeniowych, bazy danych i innych zasobów w grupie zasobów, użyj `usageDetails` operację REST i filtrować wyniki według grupy zasobów.

```http
https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Consumption/usageDetails?api-version=2018-06-30&filter=properties/resourceGroup eq '{resource-group}]
Content-Type: application/json   
Authorization: Bearer
```

`{subscription-id}` Parametr jest wymagany i może zawierać Identyfikatora subskrypcji, które mogą uzyskiwać dostęp do grupy zasobów {grupy zasobów} z rolą Czytelnik. 

Wymagane są następujące nagłówki: 

|Nagłówek żądania|Opis|  
|--------------------|-----------------|  
|*Typ zawartości:*| Wymagany. Ustaw `application/json`. |  
|*Autoryzacja:*| Wymagany. Ustawić prawidłową `Bearer` tokenu. |

### <a name="response"></a>Odpowiedź  

Kod stanu 200 (OK) jest zwracany w przypadku pomyślnej odpowiedzi, który zawiera listę statystyk użycia poszczególnych zasobów platformy Azure w grupie zasobów o identyfikatorze subscriptipon `00000000-0000-0000-0000-000000000000`.

```json
{
  "value": [
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Billing/billingPeriods/201702/providers/Microsoft.Consumption/usageDetails/usageDetailsId1",
      "name": "usageDetailsId1",
      "type": "Microsoft.Consumption/usageDetails",
      "properties": {
        "billingPeriodId": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Billing/billingPeriods/201702",
        "invoiceId": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Billing/invoices/201703-123456789",
        "usageStart": "2017-02-13T00:00:00Z",
        "usageEnd": "2017-02-13T23:59:59Z",
        "instanceName": "shared1",
        "instanceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group/providers/Microsoft.Web/sites/shared1",
        "instanceLocation": "eastasia",
        "currency": "USD",
        "usageQuantity": 0.00328,
        "billableQuantity": 0.00328,
        "pretaxCost": 0.67,
        "isEstimated": false,
        "meterId": "00000000-0000-0000-0000-000000000000",
        "partNumber": "Part Number 1",
        "resourceGuid": "00000000-0000-0000-0000-000000000000",
        "offerId": "Offer Id 1",
        "chargesBilledSeparately": true,
        "location": "EU West"
      }
    } ] }
```

## <a name="get-usage-for-tagged-resources"></a>Pobierz użycie dla otagowanych zasobów

Można pobrać użycie zasobów dla zasobów w uporządkowane według tagów, użyj `usageDetails` operację REST oraz filtrowanie wyników za pomocą nazwy tagu `$filter` parametr zapytania.

```http
https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Consumption/usageDetails?$filter=tags eq 'tag1'&api-version=2018-06-30
Content-Type: application/json   
Authorization: Bearer
```

`{subscription-id}` Parametr jest wymagany i może zawierać Identyfikatora subskrypcji, mającej dostęp do otagowanych zasobów.


### <a name="response"></a>Odpowiedź  

Kod stanu 200 (OK) jest zwracany w przypadku pomyślnej odpowiedzi, który zawiera listę statystyk użycia poszczególnych zasobów platformy Azure w grupie zasobów o identyfikatorze subscriptipon `00000000-0000-0000-0000-000000000000` i para kluczy nazwa tagu jest `dev` i `tools`. 

Przykładowa odpowiedź:

```json
{
  "value": [
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Billing/billingPeriods/201702/providers/Microsoft.Consumption/usageDetails/usageDetailsId1",
      "name": "usageDetailsId1",
      "type": "Microsoft.Consumption/usageDetails",
      "tags": {
        "dev": "tools"
      },
      "properties": {
        "billingPeriodId": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Billing/billingPeriods/201702",
        "invoiceId": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Billing/invoices/201703-123456789",
        "usageStart": "2017-02-13T00:00:00Z",
        "usageEnd": "2017-02-13T23:59:59Z",
        "instanceName": "shared1",
        "instanceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Default-Web-eastasia/providers/Microsoft.Web/sites/shared1",
        "instanceLocation": "eastasia",
        "currency": "USD",
        "usageQuantity": 0.00328,
        "billableQuantity": 0.00328,
        "pretaxCost": 0.67,
        "isEstimated": false,
        "meterId": "00000000-0000-0000-0000-000000000000",
        "partNumber": "Part Number 1",
        "resourceGuid": "00000000-0000-0000-0000-000000000000",
        "offerId": "Offer Id 1",
        "chargesBilledSeparately": true,
        "location": "EU West"
      }
    }
  ]
}
```

## <a name="next-steps"></a>Kolejne kroki
- [Wprowadzenie do interfejsu API REST platformy Azure](https://docs.microsoft.com/rest/api/azure/)   
