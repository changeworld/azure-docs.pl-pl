---
title: Przeglądanie danych rozliczeniowych rejestracji w przedsiębiorstwie platformy Azure za pomocą interfejsu API REST | Microsoft Docs
description: Dowiedz się, jak używać interfejsów API REST platformy Azure do przeglądania informacji rozliczeniowych dotyczących rejestracji przedsiębiorstwa.
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
ms.openlocfilehash: 25d9b48696dc2a83ea0ba77c1be2c7aad7627fff
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/24/2019
ms.locfileid: "68443147"
---
# <a name="review-enterprise-enrollment-billing-using-rest-apis"></a>Przegląd rozliczeń rejestracji przedsiębiorstwa przy użyciu interfejsów API REST

Interfejsy API usługi Azure Reporting ułatwiają przeglądanie kosztów platformy Azure i zarządzanie nimi.

Ten artykuł zawiera informacje dotyczące pobierania informacji rozliczeniowych skojarzonych z kontami rejestracji kont, działu lub umowy Enterprise Agreement (EA) przy użyciu interfejsów API REST platformy Azure. 

## <a name="individual-account-billing"></a>Rozliczanie poszczególnych kont

Aby uzyskać szczegółowe informacje dotyczące użycia kont w dziale:

```http
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/providers/Microsoft.Consumption/usageDetails?api-version=2018-06-30
Content-Type: application/json   
Authorization: Bearer
```

`{billingAccountId}` Parametr jest wymagany i powinien zawierać identyfikator konta.

Wymagane są następujące nagłówki: 

|Nagłówek żądania|Opis|  
|--------------------|-----------------|  
|*Content-Type:*|Wymagany. Ustaw wartość `application/json`.|  
|*Authorization:*|Wymagana. Ustaw prawidłowy `Bearer` [klucz interfejsu API](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#asynchronous-call-polling-based). |  

Ten przykład pokazuje synchroniczne wywołanie zwracające szczegóły bieżącego cyklu rozliczeniowego. Ze względu na wydajność wywołania synchroniczne zwracają informacje z ostatniego miesiąca.  Możesz również wywołać [interfejs API asynchronicznie](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#asynchronous-call-polling-based) , aby zwrócić dane przez 36 miesięcy.


## <a name="response"></a>Odpowiedź  

Kod stanu 200 (OK) jest zwracany dla pomyślnej odpowiedzi, która zawiera listę szczegółowych kosztów konta.

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

Ten przykład jest skrócony; Zobacz [pobieranie szczegółów użycia konta rozliczeniowego](/rest/api/consumption/usagedetails/list#billingaccountusagedetailslist) , aby uzyskać pełny opis każdego pola odpowiedzi i obsługi błędów.

## <a name="department-billing"></a>Rozliczenia działu 

Pobierz szczegóły użycia zagregowane dla wszystkich kont w dziale. 

```http
GET https://management.azure.com/providers/Microsoft.Billing/departments/{departmentId}/providers/Microsoft.Consumption/usageDetails?api-version=2018-06-30
Content-Type: application/json   
Authorization: Bearer
```

`{departmentId}` Parametr jest wymagany i powinien zawierać identyfikator działu na koncie rejestracji.

Wymagane są następujące nagłówki: 

|Nagłówek żądania|Opis|  
|--------------------|-----------------|  
|*Content-Type:*|Wymagany. Ustaw wartość `application/json`.|  
|*Authorization:*|Wymagany. Ustaw prawidłowy `Bearer` [klucz interfejsu API](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#asynchronous-call-polling-based). |  

Ten przykład pokazuje synchroniczne wywołanie zwracające szczegóły bieżącego cyklu rozliczeniowego. Ze względu na wydajność wywołania synchroniczne zwracają informacje z ostatniego miesiąca.  Możesz również wywołać [interfejs API asynchronicznie](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#asynchronous-call-polling-based) , aby zwrócić dane przez 36 miesięcy.

### <a name="response"></a>Odpowiedź  

Kod stanu 200 (OK) jest zwracany dla pomyślnej odpowiedzi, który zawiera listę szczegółowych informacji dotyczących użycia i kosztów dla danego okresu rozliczeniowego i identyfikatora faktury dla działu.


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

Ten przykład jest skrócony; Zobacz [Uzyskiwanie szczegółowych informacji o użyciu dla działu](/rest/api/consumption/usagedetails/list#departmentusagedetailslist) , aby uzyskać pełny opis każdego pola odpowiedzi i obsługi błędów.

## <a name="enrollment-account-billing"></a>Rozliczanie konta rejestracji

Pobierz szczegóły użycia agregowane dla konta rejestracji.

```http
GET GET https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts/{enrollmentAccountId}/providers/Microsoft.Consumption/usageDetails?api-version=2018-06-30
Content-Type: application/json   
Authorization: Bearer
```

`{enrollmentAccountId}` Parametr jest wymagany i powinien zawierać identyfikator konta rejestracji.

Wymagane są następujące nagłówki: 

|Nagłówek żądania|Opis|  
|--------------------|-----------------|  
|*Content-Type:*|Wymagana. Ustaw wartość `application/json`.|  
|*Authorization:*|Wymagany. Ustaw prawidłowy `Bearer` [klucz interfejsu API](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#asynchronous-call-polling-based). |  

Ten przykład pokazuje synchroniczne wywołanie zwracające szczegóły bieżącego cyklu rozliczeniowego. Ze względu na wydajność wywołania synchroniczne zwracają informacje z ostatniego miesiąca.  Możesz również wywołać [interfejs API asynchronicznie](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#asynchronous-call-polling-based) , aby zwrócić dane przez 36 miesięcy.

### <a name="response"></a>Odpowiedź  

Kod stanu 200 (OK) jest zwracany dla pomyślnej odpowiedzi, który zawiera listę szczegółowych informacji dotyczących użycia i kosztów dla danego okresu rozliczeniowego i identyfikatora faktury dla działu.

Poniższy przykład przedstawia dane wyjściowe interfejsu API REST na potrzeby rejestracji `1234`w przedsiębiorstwie.

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

Ten przykład jest skrócony; Zobacz [pobieranie szczegółów użycia konta rejestracji](/rest/api/consumption/usagedetails/list#enrollmentaccountusagedetailslist) , aby uzyskać pełny opis każdego pola odpowiedzi i obsługi błędów.

## <a name="next-steps"></a>Kolejne kroki 
- Przegląd [raportowania Enterprise Reporting](https://docs.microsoft.com/azure/billing/billing-enterprise-api)
- Badanie [interfejsu API REST](https://docs.microsoft.com/rest/api/billing/) rozliczeń w przedsiębiorstwie   
- [Wprowadzenie do interfejsu API REST platformy Azure](https://docs.microsoft.com/rest/api/azure/)   
