---
title: Zarejestruj dzierżaw, aby zobaczyć sposób użycia śledzenia w usłudze Azure Stack | Dokumentacja firmy Microsoft
description: Szczegółowe informacje na temat operacji narzędzia używane do zarządzania rejestracji dzierżawy i sposób śledzenia użycia przez dzierżawcę w usłudze Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/08/2018
ms.author: brenduns
ms.reviewer: alfredo
ms.openlocfilehash: 18b34af8dc383cfa86017162ec48782f156156bc
ms.sourcegitcommit: e32ea47d9d8158747eaf8fee6ebdd238d3ba01f7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/17/2018
ms.locfileid: "39093127"
---
# <a name="manage-tenant-registration-in-azure-stack"></a>Zarządzanie rejestracją dzierżawy w usłudze Azure Stack

*Dotyczy: zintegrowane systemy usługi Azure Stack*

Ten artykuł zawiera szczegółowe informacje o operacjach, których można użyć do zarządzania rejestracje dzierżawy i sposób śledzenia użycia przez dzierżawcę. Można znaleźć szczegółowe informacje na temat dodawania listy, lub usunąć mapowania dzierżawy. Do zarządzania użytkowanie śledzenia, można użyć programu PowerShell lub punkty końcowe interfejsu API rozliczeń.

## <a name="add-tenant-to-registration"></a>Dodawanie dzierżawy do rejestracji

Należy użyć tej operacji, gdy chcesz dodać nową dzierżawę do rejestracji, dzięki czemu ich użycie jest zgłaszana w ramach subskrypcji platformy Azure związanych ze swojej dzierżawy usługi Azure Active Directory (Azure AD).

Umożliwia także tę operację, jeśli chcesz zmienić subskrypcję skojarzonych dzierżawę, można wywołać PUT/New-AzureRMResource ponownie. Stary mapowanie zostanie zastąpiona.

Należy zauważyć, że tylko jedną subskrypcję platformy Azure może być skojarzony z dzierżawą. Jeśli spróbujesz dodać drugi subskrypcji do istniejącej dzierżawy, pierwszy subskrypcja jest nadmiernie napisane. 


| Parametr                  | Opis |
|---                         | --- |
| registrationSubscriptionID | Subskrypcja platformy Azure, który został użyty podczas wstępnej rejestracji. |
| customerSubscriptionID     | Subskrypcja platformy Azure (nie usługi Azure Stack) należące do klientów do zarejestrowania. Musi zostać utworzona w ramach oferty dostawcy usług chmury (CSP). W praktyce oznacza to, za pośrednictwem Centrum partnerskiego. Jeśli klient ma więcej niż jednej dzierżawy, należy utworzyć tej subskrypcji w ramach dzierżawy, która będzie służyć do logowania się do usługi Azure Stack. |
| resourceGroup              | Grupa zasobów na platformie Azure, w którym przechowywany jest rejestracja. |
| registrationName           | Nazwa rejestracji usługi Azure Stack. Jest to obiekt przechowywanych na platformie Azure. Nazwa jest zwykle azurestack formularza-CloudID, gdzie CloudID jest identyfikator chmurze wdrożenia usługi Azure Stack. |

> [!Note]  
> Dzierżawy muszą być zarejestrowane przy użyciu każdego używają usługi Azure Stack. Jeśli dzierżawa korzysta z więcej niż jednej usługi Azure Stack, musisz zaktualizować początkowej rejestracje każdego wdrożenia przy użyciu subskrypcji dzierżawcy.

### <a name="powershell"></a>PowerShell

Użyj polecenia cmdlet New-AzureRmResource na aktualizację zasobu rejestracji. Logowanie do platformy Azure (`Add-AzureRmAccount`) przy użyciu konta używanego do pierwszej rejestracji. Oto przykład sposobu dodawania dzierżawy:

```powershell
  New-AzureRmResource -ResourceId "subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/{customerSubscriptionId}" -ApiVersion 2017-06-01 -Properties
```

### <a name="api-call"></a>Wywołanie interfejsu API

**Operacja**: Umieść  
**RequestURI**: `subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}  /providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/  
{customerSubscriptionId}?api-version=2017-06-01 HTTP/1.1`  
**Odpowiedź**: 201 utworzone  
**Treść odpowiedzi**: pusty  

## <a name="list-all-registered-tenants"></a>Lista wszystkich zarejestrowanych dzierżaw

Zostanie wyświetlona lista wszystkich dzierżaw, które zostały dodane do rejestracji.

 > [!Note]  
 > Jeśli nie zarejestrowano żadnych dzierżawy, nie otrzyma odpowiedzi.

### <a name="parameters"></a>Parametry

| Parametr                  | Opis          |
|---                         | ---                  |
| registrationSubscriptionId | Subskrypcja platformy Azure, który został użyty podczas wstępnej rejestracji.   |
| resourceGroup              | Grupa zasobów na platformie Azure, w którym przechowywany jest rejestracja.    |
| registrationName           | Nazwa rejestracji usługi Azure Stack. Jest to obiekt przechowywanych na platformie Azure. Nazwa jest zwykle w formie **azurestack**-***CloudID***, gdzie ***CloudID*** jest identyfikator chmurze wdrożenia usługi Azure Stack.   |

### <a name="powershell"></a>PowerShell

Aby wyświetlić listę wszystkich zarejestrowanych dzierżaw, należy użyć polecenia cmdlet Get-AzureRmResovurce. Logowanie do platformy Azure (`Add-AzureRmAccount`) przy użyciu konta używanego do pierwszej rejestracji. Oto przykład sposobu dodawania dzierżawy:

```powershell
  Get-AzureRmResovurce -ResourceId "subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions" -ApiVersion 2017-06-01
```

### <a name="api-call"></a>Wywołanie interfejsu API

Możesz uzyskać listę wszystkich mapowań dzierżawy przy użyciu operacji GET

**Operacja**: pobieranie  
**RequestURI**: `subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}  
/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions?  
api-version=2017-06-01 HTTP/1.1`  
**Odpowiedź**: 200  
**Treść odpowiedzi**: 

```JSON  
{
    "value": [{
            "id": " subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/{ cspSubscriptionId 1}”,
            "name": " cspSubscriptionId 1",
            "type": “Microsoft.AzureStack\customerSubscriptions”,
            "properties": { "tenantId": "tId1" }
        },
        {
            "id": " subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/{ cspSubscriptionId 2}”,
            "name": " cspSubscriptionId2 ",
            "type": “Microsoft.AzureStack\customerSubscriptions”,
            "properties": { "tenantId": "tId2" }
        }
    ],
    "nextLink": "{originalRequestUrl}?$skipToken={opaqueString}"
}
```

## <a name="remove-a-tenant-mapping"></a>Usuń mapowanie dzierżawy

Istnieje możliwość usunięcia dzierżawy, który został dodany do rejestracji. Jeśli w tej dzierżawie nadal korzysta z zasobów w usłudze Azure Stack, ich użycie jest obciążany opłatą za subskrypcję używaną podczas początkowej rejestracji usługi Azure Stack.

### <a name="parameters"></a>Parametry

| Parametr                  | Opis          |
|---                         | ---                  |
| registrationSubscriptionId | Identyfikator subskrypcji dla rejestracji.   |
| resourceGroup              | Grupa zasobów o rejestrację.   |
| registrationName           | Nazwa rejestracji.  |
| customerSubscriptionId     | Identyfikator subskrypcji klienta.  |

### <a name="powershell"></a>PowerShell

```powershell
  Remove-AzureRmResource -ResourceId "subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/{customerSubscriptionId}" -ApiVersion 2017-06-01
```

### <a name="api-call"></a>Wywołanie interfejsu API

Możesz usunąć mapowania dzierżawy przy użyciu operacji usuwania.

**Operacja**: usuwanie  
**RequestURI**: `subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}  
/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/  
{customerSubscriptionId}?api-version=2017-06-01 HTTP/1.1`  
**Odpowiedź**: 204 żadnej zawartości  
**Treść odpowiedzi**: pusty

## <a name="next-steps"></a>Kolejne kroki

 - Aby dowiedzieć się więcej o tym, jak pobrać informacje o użyciu zasobów z usługi Azure Stack, zobacz [użycie i rozliczenia w usłudze Azure Stack](/azure-stack-billing-and-chargeback.md).
