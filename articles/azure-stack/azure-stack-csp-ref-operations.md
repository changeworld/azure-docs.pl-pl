---
title: Zarejestruj dzierżaw użycia śledzenia stosu Azure | Dokumentacja firmy Microsoft
description: Szczegółowe informacje na temat operacji narzędzia używane do zarządzania rejestracji dzierżawcy i sposób śledzenia użycia przez dzierżawcę w stosie Azure.
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
ms.date: 02/22/2018
ms.author: mabrigg
ms.reviewer: alfredo
ms.openlocfilehash: ef7ca59647a1f8c15d85c809609060a5945bedde
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
ms.locfileid: "32159115"
---
# <a name="manage-tenant-registration-in-azure-stack"></a>Zarządzanie rejestracją dzierżawy Azure stosu

*Dotyczy: Azure stosu zintegrowane systemy*

Ten artykuł zawiera szczegółowe informacje o operacjach, który służy do zarządzania rejestracji dzierżawcy i sposób śledzenia użycia przez dzierżawcę. Można znaleźć szczegółowe informacje dotyczące sposobu dodawania, listy, lub Usuń mapowania dzierżawy. Do zarządzania Używanie śledzenia, można użyć programu PowerShell lub rozliczeń punkty końcowe interfejsu API.

## <a name="add-tenant-to-registration"></a>Dodaj dzierżawę do rejestracji

Ta operacja jest używane, gdy chcesz dodać nową dzierżawę do rejestracji, tak aby ich użycie jest zgłaszany w ramach subskrypcji platformy Azure, związanych z ich dzierżawy usługi Azure Active Directory (Azure AD).

Umożliwia także tej operacji, jeśli chcesz zmienić subskrypcję skojarzoną z dzierżawą, można ponownie wywołać metodę PUT/New-AzureRMResource. Mapowanie starego zostanie zastąpiona.

Należy pamiętać, że tylko jedną subskrypcją platformy Azure może być skojarzony z dzierżawą. Próba dodania drugiego subskrypcji do istniejącej dzierżawy pierwszej subskrypcji jest zastąpiona. 


| Parametr                  | Opis |
|---                         | --- |
| registrationSubscriptionID | Subskrypcja platformy Azure, którego użyto do pierwszej rejestracji. |
| customerSubscriptionID     | Subskrypcja platformy Azure (nie Azure stosu) należące do klienta, który ma zostać zarejestrowany. Należy utworzyć w ofercie dostawcy usług chmury (CSP). W praktyce oznacza to, za pośrednictwem Centrum partnerskiego. Jeśli klient ma więcej niż jednej dzierżawy, należy utworzyć tej subskrypcji w dzierżawie, która będzie służyć do logowania się do stosu Azure. |
| resourceGroup              | Grupy zasobów na platformie Azure, w którym przechowywany jest rejestracja. |
| registrationName           | Nazwa rejestracji stosu Azure. Jest to obiekt przechowywane na platformie Azure. Nazwa jest zazwyczaj azurestack formularza-CloudID, gdzie CloudID to identyfikator chmury Azure stosu wdrożenia. |

> [!Note]  
> Dzierżawcy muszą być zarejestrowane przy użyciu każdego stosu Azure używają. Jeśli dzierżawca korzysta z więcej niż jeden stos Azure, musisz zaktualizować początkowej rejestracje każdego wdrożenia z subskrypcji dzierżawcy.

### <a name="powershell"></a>PowerShell

Użyj polecenia cmdlet New-AzureRmResource można zaktualizować zasobu rejestracji. Logowanie do platformy Azure (`Add-AzureRmAccount`) przy użyciu konta używanego do pierwszej rejestracji. Poniżej przedstawiono przykład sposobu dodawania dzierżawy:

```powershell
  New-AzureRmResource -ResourceId "subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/{customerSubscriptionId}" -ApiVersion 2017-06-01 -Properties
```

### <a name="api-call"></a>Wywołanie interfejsu API

**Operacja**: umieszczanie  
**RequestURI**: `subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}  /providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/  
{customerSubscriptionId}?api-version=2017-06-01 HTTP/1.1`  
**Odpowiedź**: 201 utworzone  
**Treść odpowiedzi**: pusty  

## <a name="list-all-registered-tenants"></a>Wyświetl listę wszystkich zarejestrowanych dzierżawcy

Pobierz listę wszystkich dzierżawców, które zostały dodane do rejestracji.

 > [!Note]  
 > Jeśli nie zarejestrowano żadnych dzierżawcy, nie otrzymał odpowiedzi.

### <a name="parameters"></a>Parametry

| Parametr                  | Opis          |
|---                         | ---                  |
| registrationSubscriptionId | Subskrypcja platformy Azure, którego użyto do pierwszej rejestracji.   |
| resourceGroup              | Grupy zasobów na platformie Azure, w którym przechowywany jest rejestracja.    |
| registrationName           | Nazwa rejestracji stosu Azure. Jest to obiekt przechowywane na platformie Azure. Ta nazwa jest zwykle w postaci **azurestack**-***CloudID***, gdzie ***CloudID*** jest identyfikator chmury Azure stosu wdrożenia.   |

### <a name="powershell"></a>PowerShell

Aby wyświetlić listę wszystkich zarejestrowanych dzierżawcy, należy użyć polecenia cmdlet Get-AzureRmResovurce. Logowanie do platformy Azure (`Add-AzureRmAccount`) przy użyciu konta używanego do pierwszej rejestracji. Poniżej przedstawiono przykład sposobu dodawania dzierżawy:

```powershell
  Get-AzureRmResovurce -ResourceId "subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions" -ApiVersion 2017-06-01
```

### <a name="api-call"></a>Wywołanie interfejsu API

Zostanie wyświetlona lista wszystkich mapowań dzierżawcy przy użyciu operacji GET

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

## <a name="remove-a-tenant-mapping"></a>Usuń mapowanie dzierżawcy

Możesz usunąć dzierżawy, który został dodany do rejestracji. Jeśli tej dzierżawy nadal korzysta z zasobów na stosie Azure, ich użycia obciążających z subskrypcją użytą w początkowej rejestracji stosu Azure.

### <a name="parameters"></a>Parametry

| Parametr                  | Opis          |
|---                         | ---                  |
| registrationSubscriptionId | Identyfikator subskrypcji dla rejestracji.   |
| resourceGroup              | Grupa zasobów dla rejestracji.   |
| registrationName           | Nazwa rejestracji.  |
| customerSubscriptionId     | Identyfikator subskrypcji klienta.  |

### <a name="powershell"></a>PowerShell

```powershell
  Remove-AzureRmResource -ResourceId "subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/{customerSubscriptionId}" -ApiVersion 2017-06-01
```

### <a name="api-call"></a>Wywołanie interfejsu API

Można usunąć mapowania dzierżawcy przy użyciu operacji DELETE.

**Operacja**: usuwanie  
**RequestURI**: `subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}  
/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/  
{customerSubscriptionId}?api-version=2017-06-01 HTTP/1.1`  
**Odpowiedź**: 204 żadnej zawartości  
**Treść odpowiedzi**: pusty

## <a name="next-steps"></a>Kolejne kroki

 - Aby dowiedzieć się więcej o tym, jak można pobrać informacji o użyciu zasobów ze stosu Azure, zobacz [użycie i rozliczenia Azure stosu](/azure-stack-billing-and-chargeback.md).
