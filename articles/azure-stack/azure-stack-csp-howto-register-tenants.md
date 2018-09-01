---
title: Dodaj dzierżawców za użycie i rozliczenia w usłudze Azure Stack | Dokumentacja firmy Microsoft
description: Czynności wymagane Dodaj użytkownika końcowego do usługi Azure Stack, zarządzane przez dostawcę usług chmury (CSP).
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
ms.date: 07/12/2018
ms.author: brenduns
ms.reviewer: alfredo
ms.openlocfilehash: d3fc3ef6c5fdcf5a87c691c73169ef2bec95805e
ms.sourcegitcommit: a3a0f42a166e2e71fa2ffe081f38a8bd8b1aeb7b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/01/2018
ms.locfileid: "43382692"
---
# <a name="add-tenant-for-usage-and-billing-to-azure-stack"></a>Dodawanie dzierżawy za użycie i rozliczenia w usłudze Azure Stack

*Dotyczy: zintegrowane systemy usługi Azure Stack*

W tym artykule opisano procedurę dodawania użytkownika końcowego do usługi Azure Stack, zarządzane przez dostawcę usług chmury (CSP). Gdy nowa dzierżawa korzysta z zasobów, usługi Azure Stack zgłosi użycie ich subskrypcji dostawcy CSP.

Dostawcy usług kryptograficznych często oferty usług dla wielu klientów końcowych (dzierżawcy) na to wdrożenie usługi Azure Stack. Dodawanie dzierżawy do rejestracji w usłudze Azure Stack zapewnia użycia każdej dzierżawy zgłaszane i rozliczane w odpowiedniej subskrypcji dostawcy CSP. Jeśli nie wykonasz kroki opisane w tym artykule, wykorzystanie dzierżaw jest obciążany opłatą za subskrypcję używaną w wstępnej rejestracji usługi Azure Stack. Przed dodaniem klienta końcowego do usługi Azure Stack dla śledzenia użycia oraz zarządzanie ich dzierżawy, należy skonfigurować usługę Azure Stack jako dostawcy usług Kryptograficznych. Kroki i zasobów, zobacz [Zarządzanie użycia i rozliczeń dla usługi Azure Stack jako dostawca usług w chmurze](azure-stack-add-manage-billing-as-a-csp.md).

Na poniższym diagramie przedstawiono kroki, które należy wykonać, aby włączyć nowego klienta do używania usługi Azure Stack i skonfigurować sposób użycia śledzenia dla klienta dostawcy usług Kryptograficznych. Dodając klienta końcowego, będzie można również zarządzać zasobami w usłudze Azure Stack. Masz dwie opcje do zarządzania zasobami:

1. Można zachować klienta końcowego i podanie poświadczeń w celu lokalnego subskrypcji usługi Azure Stack dla klienta końcowego.  
2. Lub klienta końcowego może pracować lokalnie swoją subskrypcję i dodać dostawcy rozwiązań w Chmurze jako gościa z uprawnieniami właściciela.  

**Kroki, aby dodać klienta końcowego**

![Konfigurowanie dostawcy usług w chmurze, do śledzenia użycia i zarządzać kontem klienta końcowego](media\azure-stack-csp-enable-billing-usage-tracking\process-csp-enable-billing.png)

## <a name="create-a-new-customer-in-partner-center"></a>Tworzenie nowego klienta w Centrum partnerskiego

W Centrum partnerskim należy utworzyć nową subskrypcję platformy Azure dla klienta. Aby uzyskać instrukcje, zobacz [dodać nowego klienta](https://msdn.microsoft.com/partner-center/add-a-new-customer).


##  <a name="create-an-azure-subscription-for-the-end-customer"></a>Utwórz subskrypcję platformy Azure dla klientów końcowych

Po utworzeniu rekord klienta w Centrum partnerskim, możesz sprzedawać subskrypcje produktów w katalogu. Aby uzyskać instrukcje, zobacz [tworzenia, zawiesić lub anulować subskrypcje klientów](https://msdn.microsoft.com/partner-center/create-a-new-subscription).

## <a name="create-a-guest-user-in-the-end-customer-directory"></a>Tworzenie użytkownika-gościa w katalogu klientów końcowych

Jeśli odbiorcy końcowego będą zarządzać ich własnego konta, Utwórz użytkownika-gościa w ich katalogu i wysłać im informacje. Użytkownik końcowy będzie następnie Dodaj gościa i podnieść poziom uprawnień gościa do **właściciela** do konta usługi Azure Stack dostawcy usług Kryptograficznych.
 
## <a name="update-the-registration-with-the-end-customer-subscription"></a>Aktualizowanie rejestracji przy użyciu subskrypcji klienta końcowego

Zaktualizuj swoją rejestrację przy użyciu subskrypcji na nowego klienta. Azure raporty użycia przez klientów przy użyciu tożsamości odbiorcy z partnerem Central. Ten krok zapewnia, że raportowania użycia każdego klienta w ramach poszczególnych subskrypcji dostawcy CSP tego klienta. To sprawia, że śledzenie użytkownika użycia i rozliczeń znacznie łatwiejsze.

> [!Note]  
> Aby wykonać ten krok, musisz mieć [zarejestrowane w usłudze Azure Stack](azure-stack-register.md).

1. Otwórz środowiska Windows PowerShell z podwyższonym poziomem uprawnień wiersza i uruchom:  
    `Add-AzureRmAccount`
2. Wpisz swoje poświadczenia platformy Azure.
3. W sesji programu PowerShell Uruchom polecenie:

```powershell
    New-AzureRmResource -ResourceId "subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/{customerSubscriptionId}" -ApiVersion 2017-06-01 -Properties <PSObject>
```
### <a name="new-azurermresource-powershell-parameters"></a>Parametry nowej AzureRmResource programu PowerShell
| Parametr | Opis |
| --- | --- | 
|registrationSubscriptionID | Subskrypcja platformy Azure, który został użyty podczas wstępnej rejestracji usługi Azure Stack. |
| customerSubscriptionID | Subskrypcja platformy Azure (nie usługi Azure Stack) należące do klientów do zarejestrowania. Musi być utworzona w ramach oferty dostawcy usług Kryptograficznych; w praktyce oznacza to, za pośrednictwem Centrum partnerskiego. Jeśli klient ma więcej niż jedną dzierżawę usługi Azure Active Directory, należy utworzyć tej subskrypcji w ramach dzierżawy, która będzie służyć do logowania się do usługi Azure Stack.
| resourceGroup | Grupa zasobów na platformie Azure, w którym przechowywany jest rejestracja. 
| registrationName | Nazwa rejestracji usługi Azure Stack. Jest to obiekt przechowywanych na platformie Azure. | 
| Właściwości | Określa właściwości zasobu. Ten parametr umożliwia określenie wartości właściwości, które są specyficzne dla typu zasobu.


> [!Note]  
> Dzierżawy muszą być zarejestrowane przy użyciu każdego używają usługi Azure Stack. Jeśli masz dwa wdrożenia usługi Azure Stack oraz dzierżawy zamierza korzystać z obu z nich należy zaktualizować początkowej rejestracje każdego wdrożenia przy użyciu subskrypcji dzierżawcy.

## <a name="onboard-tenant-to-azure-stack"></a>Dołączanie dzierżawy do usługi Azure Stack

Konfigurowanie usługi Azure Stack w celu obsługi użytkowników z wielu dzierżaw usługi Azure AD do korzystania z usług w usłudze Azure Stack. Aby uzyskać instrukcje, zobacz [Włączanie wielodostępu w usłudze Azure Stack](azure-stack-enable-multitenancy.md).


## <a name="create-a-local-resource-in-the-end-customer-tenant-in-azure-stack"></a>Tworzenie zasobu lokalnego w dzierżawie klienta końcowego w usłudze Azure Stack

Po dodaniu nowego klienta do usługi Azure Stack lub dzierżawie klienta końcowego została włączona na koncie gościa z uprawnieniami właściciela, sprawdź, czy można utworzyć zasobu w ramach ich dzierżawy. Na przykład mogą oni [utworzyć maszynę wirtualną Windows za pomocą portalu usługi Azure Stack](user\azure-stack-quick-windows-portal.md).

## <a name="next-steps"></a>Kolejne kroki

 - Aby przejrzeć komunikaty o błędach, jeśli są one wyzwalane w procesie rejestracji, zobacz [dzierżawy komunikaty o błędach rejestracji](azure-stack-csp-ref-infrastructure.md#usage-and-billing-error-codes).
 - Aby dowiedzieć się więcej o tym, jak pobrać informacje o użyciu zasobów z usługi Azure Stack, zobacz [użycie i rozliczenia w usłudze Azure Stack](azure-stack-billing-and-chargeback.md).
 - Aby sprawdzić, jak klienta końcowego może dodać użytkownika, jako dostawcy usług Kryptograficznych, Menedżer dla ich usługi Azure Stack dzierżawy, zobacz [włączenia dostawcy usług w chmurze do zarządzania subskrypcją usługi Azure Stack](user\azure-stack-csp-enable-billing-usage-tracking.md).
