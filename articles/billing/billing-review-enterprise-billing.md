---
title: Przegląd platformy Azure w przedsiębiorstwach rejestrowania danych za pomocą interfejsu API REST rozliczeń | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać interfejsów API REST platformy Azure do przeglądania informacji dotyczących rozliczeń rejestracji enterprise.
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
ms.openlocfilehash: 9a0b536426ab024d5af7b257e44a2d5e20f14def
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60371040"
---
# <a name="review-enterprise-enrollment-billing-using-rest-apis"></a>Przegląd rozliczeń rejestracji enterprise za pomocą interfejsów API REST

Pomoc platformy Azure interfejsy API raportowania usługi Przejrzyj i zarządzania kosztami platformy Azure.

W tym artykule dowiesz się pobrać informacji dotyczących rozliczeń związanych z konta rozliczeniowego, działu lub kont rejestracji enterprise agreement (EA) z przy użyciu interfejsów API REST platformy Azure. 

## <a name="individual-account-billing"></a>Karta indywidualne konto

Aby uzyskać szczegóły użycia dla kont w dziale:

```http
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/providers/Microsoft.Consumption/usageDetails?api-version=2018-06-30
Content-Type: application/json   
Authorization: Bearer
```

`{billingAccountId}` Parametr jest wymagany i może zawierać identyfikator dla tego konta.

Wymagane są następujące nagłówki: 

|Nagłówek żądania|Opis|  
|--------------------|-----------------|  
|*Content-Type:*|Wymagany. Ustaw `application/json`.|  
|*Authorization:*|Wymagany. Ustawić prawidłową `Bearer` [klucz interfejsu API](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#asynchronous-call-polling-based). |  

Ten przykład przedstawia synchroniczne wywołanie, które zwraca szczegółowe informacje dla bieżącego cyklu rozliczeniowego. Ze względu na wydajność synchroniczne wywołania zwrócić informacji dla ostatniego miesiąca.  Można również wywołać [API asynchronicznie](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#asynchronous-call-polling-based) zwrócić danych przez 36 miesięcy.


## <a name="response"></a>Odpowiedź  

Zwróciła kod stanu 200 (OK) pomyślnej odpowiedzi, która zawiera listę szczegółowych kosztów dla konta.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/BillingAccounts/1234/providers/Microsoft.Billing/billingPeriods/201702/providers/Microsoft.Consumption/usageDetails/usageDetailsId1",
      "name": "usageDetailsId1",
      "type": "Microsoft.Consumption/usageDetails",
      "properties": {
        ...
        "usageStart": "2017-02-13T00:00:00Z",
        "usageEnd": "2017-02-13T23:59:59Z",
        "instanceName": "shared1",
        "instanceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Default-Web-eastasia/providers/Microsoft.Web/sites/shared1",
        "currency": "USD",
        "usageQuantity": 0.00328,
        "billableQuantity": 0.00328,
        "pretaxCost": 0.67,
        "isEstimated": false,
        ...
      }
    }
  ]
}
```  

W tym przykładzie jest skracana; zobacz [uzyskać szczegóły użycia dla konta rozliczeniowego](/rest/api/consumption/usagedetails/list#billingaccountusagedetailslist) pełny opis każdego pola odpowiedzi oraz obsługi błędów.

## <a name="department-billing"></a>Dział rozliczeń 

Pobierz szczegóły użycia agregowane dla wszystkich kont w dziale. 

```http
GET https://management.azure.com/providers/Microsoft.Billing/departments/{departmentId}/providers/Microsoft.Consumption/usageDetails?api-version=2018-06-30
Content-Type: application/json   
Authorization: Bearer
```

`{departmentId}` Parametr jest wymagany i może zawierać identyfikator dla działu, w ramach konta rejestracji.

Wymagane są następujące nagłówki: 

|Nagłówek żądania|Opis|  
|--------------------|-----------------|  
|*Content-Type:*|Wymagany. Ustaw `application/json`.|  
|*Authorization:*|Wymagany. Ustawić prawidłową `Bearer` [klucz interfejsu API](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#asynchronous-call-polling-based). |  

Ten przykład przedstawia synchroniczne wywołanie, które zwraca szczegółowe informacje dla bieżącego cyklu rozliczeniowego. Ze względu na wydajność synchroniczne wywołania zwrócić informacji dla ostatniego miesiąca.  Można również wywołać [API asynchronicznie](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#asynchronous-call-polling-based) zwrócić danych przez 36 miesięcy.

### <a name="response"></a>Odpowiedź  

Zwróciła kod stanu 200 (OK) pomyślnej odpowiedzi, która zawiera listę informacje szczegółowe dotyczące użycia i kosztów dla danego rozliczeń Identyfikatora okres i faktury dla działu.


Poniższy przykład przedstawia dane wyjściowe interfejsu API REST dla działu `1234`.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/Departments/1234/providers/Microsoft.Billing/billingPeriods/201702/providers/Microsoft.Consumption/usageDetails/usageDetailsId1",
      "name": "usageDetailsId1",
      "type": "Microsoft.Consumption/usageDetails",
      "properties": {
        "billingPeriodId": "/providers/Microsoft.Billing/Departments/1234/providers/Microsoft.Billing/billingPeriods/201702",
        "invoiceId": "/providers/Microsoft.Billing/Departments/1234/providers/Microsoft.Billing/invoices/201703-123456789",
        "usageStart": "2017-02-13T00:00:00Z",
        "usageEnd": "2017-02-13T23:59:59Z",
        "instanceName": "shared1",
        "instanceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Default-Web-eastasia/providers/Microsoft.Web/sites/shared1",
        "instanceLocation": "eastasia",
        "currency": "USD",
        "usageQuantity": 0.00328,
        "billableQuantity": 0.00328,
        "pretaxCost": 0.67,
        ...
      }
    }
  ]
}
```  

W tym przykładzie jest skracana; zobacz [uzyskać szczegóły użycia dla działu](/rest/api/consumption/usagedetails/list#departmentusagedetailslist) pełny opis każdego pola odpowiedzi oraz obsługi błędów.

## <a name="enrollment-account-billing"></a>Rozliczanie konta rejestracji

Pobierz szczegóły użycia agregowane dla konta rejestracji.

```http
GET GET https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts/{enrollmentAccountId}/providers/Microsoft.Consumption/usageDetails?api-version=2018-06-30
Content-Type: application/json   
Authorization: Bearer
```

`{enrollmentAccountId}` Parametr jest wymagany i może zawierać identyfikator konta rejestracji.

Wymagane są następujące nagłówki: 

|Nagłówek żądania|Opis|  
|--------------------|-----------------|  
|*Content-Type:*|Wymagany. Ustaw `application/json`.|  
|*Authorization:*|Wymagany. Ustawić prawidłową `Bearer` [klucz interfejsu API](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#asynchronous-call-polling-based). |  

Ten przykład przedstawia synchroniczne wywołanie, które zwraca szczegółowe informacje dla bieżącego cyklu rozliczeniowego. Ze względu na wydajność synchroniczne wywołania zwrócić informacji dla ostatniego miesiąca.  Można również wywołać [API asynchronicznie](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#asynchronous-call-polling-based) zwrócić danych przez 36 miesięcy.

### <a name="response"></a>Odpowiedź  

Zwróciła kod stanu 200 (OK) pomyślnej odpowiedzi, która zawiera listę informacje szczegółowe dotyczące użycia i kosztów dla danego rozliczeń Identyfikatora okres i faktury dla działu.

Poniższy przykład przedstawia dane wyjściowe interfejs API REST rejestracji enterprise `1234`.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/EnrollmentAccounts/1234/providers/Microsoft.Billing/billingPeriods/201702/providers/Microsoft.Consumption/usageDetails/usageDetailsId1",
      "name": "usageDetailsId1",
      "type": "Microsoft.Consumption/usageDetails",
      "properties": {
        "billingPeriodId": "/providers/Microsoft.Billing/EnrollmentAccounts/1234/providers/Microsoft.Billing/billingPeriods/201702",
        "invoiceId": "/providers/Microsoft.Billing/EnrollmentAccounts/1234/providers/Microsoft.Billing/invoices/201703-123456789",
        "usageStart": "2017-02-13T00:00:00Z",
        "usageEnd": "2017-02-13T23:59:59Z",
        ....
        "currency": "USD",
        "usageQuantity": 0.00328,
        "billableQuantity": 0.00328,
        "pretaxCost": 0.67,
        ...
      }
    }
  ]
}
``` 

W tym przykładzie jest skracana; zobacz [uzyskać szczegóły użycia konta rejestracji](/rest/api/consumption/usagedetails/list#enrollmentaccountusagedetailslist) pełny opis każdego pola odpowiedzi oraz obsługi błędów.

## <a name="next-steps"></a>Kolejne kroki 
- Przegląd [Przegląd raportowania dla przedsiębiorstw.](https://docs.microsoft.com/azure/billing/billing-enterprise-api)
- Badanie [przedsiębiorstwa, interfejs API REST rozliczeń](https://docs.microsoft.com/rest/api/billing/)   
- [Wprowadzenie do interfejsu API REST platformy Azure](https://docs.microsoft.com/rest/api/azure/)   
