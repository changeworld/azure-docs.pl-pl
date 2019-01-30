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
ms.date: 01/08/2019
ms.author: mabrigg
ms.reviewer: alfredop
ms.lastreviewed: 01/08/2019
ms.openlocfilehash: 5ae8297f8e189fbe9374cec826bf5e566e5403da
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/30/2019
ms.locfileid: "55241954"
---
# <a name="manage-tenant-registration-in-azure-stack"></a>Zarządzanie rejestracją dzierżawy w usłudze Azure Stack

*Dotyczy: Zintegrowane systemy usługi Azure Stack*

Ten artykuł zawiera szczegółowe informacje o operacji rejestracji. Można użyć tych operacji, aby:
- Zarządzanie rejestracjami dzierżawy
- Zarządzanie śledzenia użycia dzierżawy

Można znaleźć szczegółowe informacje na temat dodawania listy, lub usunąć mapowania dzierżawy. Do zarządzania użytkowanie śledzenia, można użyć programu PowerShell lub punkty końcowe interfejsu API rozliczeń. Można znaleźć szczegółowe informacje na temat dodawania listy, lub usunąć mapowania dzierżawy. Do zarządzania użytkowanie śledzenia, można użyć programu PowerShell lub punkty końcowe interfejsu API rozliczeń.

## <a name="add-tenant-to-registration"></a>Dodawanie dzierżawy do rejestracji

Użyj operacji, jeśli chcesz dodać nową dzierżawę do rejestracji. Wykorzystanie dzierżaw jest zgłaszana w ramach subskrypcji platformy Azure związanych ze swojej dzierżawy usługi Azure Active Directory (Azure AD).

Umożliwia także wykonać operację, jeśli chcesz zmienić subskrypcję skojarzoną z dzierżawą. Wywołania PUT/New-AzureRMResource zastąpienie poprzedniej mapowania.

Można skojarzyć pojedynczej subskrypcji platformy Azure z dzierżawą. Jeśli spróbujesz dodać drugi subskrypcji do istniejącej dzierżawy, pierwszy subskrypcja jest nadmiernie napisane.

### <a name="use-api-profiles"></a>Użycie profilów interfejsu API

Polecenia cmdlet rejestracji wymagają określić profil interfejsu API, podczas uruchamiania programu PowerShell. Profile interfejsu API reprezentuje zestaw dostawców zasobów platformy Azure i ich wersje interfejsu API. Ułatwiają one Użyj właściwej wersji interfejsu API podczas interakcji z wieloma chmurami platformy Azure. Na przykład możesz pracować z wieloma chmurami podczas pracy z globalnej platformy Azure i usługi Azure Stack. Profile, określ nazwę, która odpowiada ich daty wydania. Należy użyć **2017-09-03** profilu.

Aby uzyskać więcej informacji na temat usługi Azure Stack i profilami interfejsu API, zobacz [Zarządzanie profilami wersji interfejsu API w usłudze Azure Stack](user/azure-stack-version-profiles.md). Aby uzyskać instrukcje i przeprowadzanie z profilem interfejsu API przy użyciu programu PowerShell, zobacz [korzystanie z interfejsu API w wersji profilów dla programu PowerShell w usłudze Azure Stack](user/azure-stack-version-profiles-powershell.md).

### <a name="parameters"></a>Parametry

| Parametr                  | Opis |
|---                         | --- |
| registrationSubscriptionID | Subskrypcja platformy Azure, który został użyty podczas wstępnej rejestracji. |
| customerSubscriptionID     | Subskrypcja platformy Azure (nie usługi Azure Stack) należące do klientów do zarejestrowania. Musi zostać utworzona w ramach oferty dostawcy usług chmury (CSP), za pośrednictwem Centrum partnerskiego. Jeśli klient ma więcej niż jedną dzierżawę, utworzono subskrypcję dla dzierżawy zalogować się do usługi Azure Stack. |
| resourceGroup              | Grupa zasobów na platformie Azure, w którym przechowywany jest rejestracja. |
| registrationName           | Nazwa rejestracji usługi Azure Stack. Jest to obiekt przechowywanych na platformie Azure. Nazwa jest zwykle azurestack formularza-CloudID, gdzie CloudID jest identyfikator chmurze wdrożenia usługi Azure Stack. |

> [!Note]  
> Dzierżawy muszą być zarejestrowane przy użyciu każdego używają usługi Azure Stack. Jeśli dzierżawa korzysta z więcej niż jednej usługi Azure Stack, musisz zaktualizować początkowej rejestracje każdego wdrożenia przy użyciu subskrypcji dzierżawcy.

### <a name="powershell"></a>PowerShell

Użyj polecenia cmdlet New-AzureRmResource na aktualizację zasobu rejestracji. Oto przykład sposobu dodawania dzierżawy:

```powershell
  New-AzureRmResource -ResourceId "subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/{customerSubscriptionId}" -ApiVersion 2017-06-01 -Properties
```

### <a name="api-call"></a>Wywołanie interfejsu API

**Operacja**: PUT  
**RequestURI**: `subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}  /providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/  
{customerSubscriptionId}?api-version=2017-06-01 HTTP/1.1`  
**Odpowiedź**: 201 utworzono  
**Treść odpowiedzi**: Pusty  

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

Aby wyświetlić listę wszystkich zarejestrowanych dzierżaw, należy użyć polecenia cmdlet Get-AzureRmResource. Logowanie do platformy Azure (`Add-AzureRmAccount`) przy użyciu konta używanego do pierwszej rejestracji. Oto przykład sposobu dodawania dzierżawy:

```powershell
  Get-AzureRmResource -ResourceId "subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions" -ApiVersion 2017-06-01
```

### <a name="api-call"></a>Wywołanie interfejsu API

Możesz uzyskać listę wszystkich mapowań dzierżawy przy użyciu operacji GET

**Operacja**: GET  
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

**Operacja**: DELETE  
**RequestURI**: `subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}  
/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/  
{customerSubscriptionId}?api-version=2017-06-01 HTTP/1.1`  
**Odpowiedź**: 204 No Content  
**Treść odpowiedzi**: Pusty

## <a name="next-steps"></a>Kolejne kroki

 - Aby dowiedzieć się więcej o tym, jak pobrać informacje o użyciu zasobów z usługi Azure Stack, zobacz [użycie i rozliczenia w usłudze Azure Stack](azure-stack-billing-and-chargeback.md).
