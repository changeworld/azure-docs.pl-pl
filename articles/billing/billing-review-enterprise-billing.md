---
title: Rejestracja Azure enterprise przeglądu rozliczeń danych za pomocą interfejsu API REST | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać interfejsów API REST Azure do przeglądania informacji dotyczących rozliczeń rejestracji enterprise.
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
ms.openlocfilehash: 046b2e31aaefa5916a42b3652f9e6a8fdceff367
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37063998"
---
# <a name="review-enterprise-enrollment-billing-using-rest-apis"></a>Przejrzyj rozliczeń rejestracji przedsiębiorstwa przy użyciu interfejsów API REST

Azure Pomoc raportowania na interfejsy API Przejrzyj i zarządzanie nimi kosztów platformy Azure.

W tym miejscu dowiesz się, można pobrać bieżącego BOM skojarzony z rejestracji enterprise konta.

Można pobrać bieżącego:
``` http
GET https://consumption.azure.com/v2/enrollments/{enrollmentID}/usagedetails
Content-Type: application/json   
Authorization: Bearer
```

## <a name="build-the-request"></a>Żądanie kompilacji  

`{enrollmentID}` Parametr jest wymagany i musi zawierać identyfikator rejestracji dla organizacji konta (EA).

Wymagane są następujące nagłówki: 

|Nagłówek żądania|Opis|  
|--------------------|-----------------|  
|*Typ zawartości:*|Wymagany. Ustaw `application/json`.|  
|*Autoryzacji:*|Wymagany. Ustaw prawidłową `Bearer` [klucz interfejsu API](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#asynchronous-call-polling-based). |  

Ten przykład przedstawia synchroniczne wywołanie, które zwraca szczegóły dla bieżącego cyklu rozliczeniowego. Ze względu na wydajność synchroniczne wywołania zwracają informacje z ostatniego miesiąca.  Możesz także wywołać [interfejsu API asynchronicznie](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#asynchronous-call-polling-based) aby zwrócić dane do 36 miesięcy.


## <a name="response"></a>Odpowiedź  

Kod stanu 200 (OK) jest zwracana dla pomyślnej odpowiedzi, która zawiera listę szczegółowych kosztów dla Twojego konta.

``` json
{
    "id": "${id}",
    "data": [
        {
            "cost": ${cost}, 
            "departmentId": ${departmentID},
            "subscriptionGuid" : ${subscriptionGuid} 
            "date": "${date}",
            "tags": "${tags}",
            "resourceGroup": "${resourceGroup}"
        } // ...
    ],
    "nextLink": "${nextLinkURL}"
}
```  

Każdy element **danych** reprezentuje opłat:

|Właściwość Response|Opis|
|----------------|----------|
|**Koszt** | Kwota naliczona opłata w walucie lokalizacji centrum danych. |
|**subscriptionGuid** | Globalnie unikatowy identyfikator subskrypcji. | 
|**departmentId** | Identyfikator dla działu, jeśli istnieje. |
|**Data** | Data została naliczane opłaty. |
|**tagów** | Ciąg JSON zawierający znaczniki skojarzone z subskrypcją. |
|**resourceGroup**|Nazwa grupy zasobów zawierającej obiekt, który poniesione koszty. |
|**nextLink**| Gdy są ustawione, określa adres URL dla następnego "page" szczegóły. Puste, gdy strona jest ostatnim blokiem. |  
||
  
Identyfikatory działu, grupy zasobów, znaczników i związane pola są zdefiniowane przez administratora administrator przedsiębiorstwa.  

W tym przykładzie jest skrócona; zobacz [pobrać szczegółów użycia](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail) pełny opis każdego pola odpowiedzi. 

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
