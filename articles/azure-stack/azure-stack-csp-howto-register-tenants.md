---
title: Dodaj dzierżaw dotyczące użycia i rozliczeń stos Azure | Dokumentacja firmy Microsoft
description: Kroki wymagane Dodaj użytkownika końcowego do stosu Azure zarządza dostawcy usług w chmurze.
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
ms.date: 03/08/2018
ms.author: mabrigg
ms.reviewer: alfredo
ms.openlocfilehash: 27473ce4057fdb06ab9faf0f46dede62b4ee2246
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37048843"
---
# <a name="add-tenant-for-usage-and-billing-to-azure-stack"></a>Dodaj dzierżawy użycie i rozliczenia Azure stosu

*Dotyczy: Azure stosu zintegrowane systemy*

W tym artykule opisano kroki wymagane Dodaj użytkownika końcowego do stosu Azure zarządzanych przez dostawcę usług chmury (CSP). Gdy nowej dzierżawy używa zasobów, stos Azure będzie podlegać użycia swoją subskrypcję dostawcy usług Kryptograficznych.

Dostawcy usług kryptograficznych często oferty usług dla wielu klientów (dzierżawców) na ich wdrożenia stosu Azure. Dodawanie dzierżawcy do rejestracji stosu Azure temu użycia każdego dzierżawcy zgłoszone i rozliczony odpowiednią subskrypcję dostawcy usług Kryptograficznych. Jeśli nie wykonasz kroki opisane w tym artykule, użycia przez dzierżawcę obciążających z subskrypcją użytą w początkowej rejestracji stosu Azure. Przed dodaniem odbiorcy końcowego Azure stos śledzenia użycia oraz do zarządzania swojej dzierżawy, należy skonfigurować stosu Azure jako dostawcy usług Kryptograficznych. Procedury i zasobów, zobacz [Zarządzanie użycie i rozliczenia Azure stosu jako dostawcy usług w chmurze](azure-stack-add-manage-billing-as-a-csp.md).

Na poniższym diagramie przedstawiono kroki, które należy wykonać, aby włączyć nowego klienta, aby użyć stosu Azure i skonfigurować śledzenie klienta użycia dostawcy usług Kryptograficznych. Dodając odbiorcy końcowego będzie również do zarządzania zasobami Azure stosu. Masz dwie opcje zarządzania zasobami:

1. Można Obsługa zakończenia dzierżawy klienta i podaj poświadczenia na potrzeby lokalnego subskrypcji Azure stosu do odbiorcy końcowego.  
2. Lub odbiorcy końcowego można pracować z ich subskrypcją lokalnie i dodać dostawcy usług Kryptograficznych jako gość z uprawnień właściciela.  

**Kroki, aby dodać klienta zakończenia**

![Konfigurowanie dostawcy usług w chmurze dla śledzenia użycia oraz do zarządzania na koncie klienta zakończenia](media\azure-stack-csp-enable-billing-usage-tracking\process-csp-enable-billing.png)

## <a name="create-a-new-customer-in-partner-center"></a>Tworzenie nowego klienta w Centrum partnerskiego

W Centrum partnerskiego Utwórz nową subskrypcję platformy Azure dla klienta. Aby uzyskać instrukcje, zobacz [dodać nowego klienta](https://msdn.microsoft.com/partner-center/add-a-new-customer).


##  <a name="create-an-azure-subscription-for-the-end-customer"></a>Utwórz subskrypcję platformy Azure dla klientów końcowych

Po utworzeniu rekordu klienta w Centrum partnera, możesz je sprzedać subskrypcje produktów w katalogu. Aby uzyskać instrukcje, zobacz [tworzenia, wstrzymać lub anulować subskrypcji klienta](https://msdn.microsoft.com/partner-center/create-a-new-subscription).

## <a name="create-a-guest-user-in-the-end-customer-directory"></a>Utwórz użytkownika gościa na końcu katalogu klienta

Jeśli odbiorcy końcowego będzie zarządzać własne konto, Utwórz użytkownika gościa w ich katalogu, a następnie wysłać informacje. Użytkownik końcowy będą Dodaj gościa i podniesienia uprawnień gościa do **właściciela** na koncie Azure stosu dostawcy usług Kryptograficznych.
 
## <a name="update-the-registration-with-the-end-customer-subscription"></a>Aktualizacja rejestracji w usłudze zakończenia subskrypcji klienta

Zaktualizuj rejestrację z subskrypcją nowego klienta. Azure raporty użycia klienta przy użyciu tożsamości klienta z centralnej partnera. Ten krok zapewnia, że użycie każdego klienta jest zgłaszany w ramach poszczególnych subskrypcji dostawcy usług Kryptograficznych tego klienta. Ułatwia to śledzenie użytkownika użycia i rozliczeń znacznie.

> [!Note]  
> Aby wykonać ten krok, musisz mieć [zarejestrowany stosu Azure](azure-stack-register.md).

1. Otwórz program Windows PowerShell z podniesionego wiersza, a następnie uruchom:  
    `Add-AzureRmAccount`
2. Wpisz poświadczenia platformy Azure.
3. W sesji programu PowerShell Uruchom polecenie:

```powershell
    New-AzureRmResource -ResourceId "subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/{customerSubscriptionId}" -ApiVersion 2017-06-01 -Properties <PSObject>
```
### <a name="new-azurermresource-powershell-parameters"></a>Parametry nowej AzureRmResource programu PowerShell
| Parametr | Opis |
| --- | --- | 
|registrationSubscriptionID | Subskrypcja platformy Azure, którego użyto do pierwszej rejestracji stosu Azure. |
| customerSubscriptionID | Subskrypcja platformy Azure (nie Azure stosu) należące do klienta, który ma zostać zarejestrowany. Musi być utworzona w ofercie dostawcy usług Kryptograficznych; w praktyce oznacza to, za pośrednictwem Centrum partnerskiego. Jeśli klient ma więcej niż jednej dzierżawy usługi Azure Active Directory, należy utworzyć w dzierżawie, która będzie służyć do logowania się do stosu usługi Azure tej subskrypcji.
| resourceGroup | Grupy zasobów na platformie Azure, w którym przechowywany jest rejestracja. 
| registrationName | Nazwa rejestracji stosu Azure. Jest to obiekt przechowywane na platformie Azure. | 
| Właściwości | Określa właściwości zasobu. Ten parametr umożliwia określenie wartości właściwości, które są specyficzne dla typu zasobu.


> [!Note]  
> Dzierżawcy muszą być zarejestrowane przy użyciu każdego stosu Azure używają. Jeśli masz dwa wdrożenia stosu Azure i dzierżawca ma korzystać z obu z nich, musisz zaktualizować początkowej rejestracje każdego wdrożenia z subskrypcji dzierżawcy.

## <a name="onboard-tenant-to-azure-stack"></a>Dołączyć dzierżawy Azure stosu

Skonfiguruj stosu Azure do obsługi użytkowników z wieloma dzierżawcami usługi Azure AD, aby korzystać z usług Azure stosu. Aby uzyskać instrukcje, zobacz [włączyć wielodostępność stosu Azure](azure-stack-enable-multitenancy.md).


## <a name="create-a-local-resource-in-the-end-customer-tenant-in-azure-stack"></a>Tworzenie zasobu lokalnego w dzierżawie klienta zakończenia w stosie Azure

Po dodaniu nowego klienta do stosu Azure lub zakończenia dzierżawy klienta włączył konta gościa z uprawnieniami właściciela, sprawdź, czy można utworzyć zasobu w swojej dzierżawy. Na przykład można [Utwórz maszynę wirtualną z systemem Windows przy użyciu portalu Azure stosu](user\azure-stack-quick-windows-portal.md).

## <a name="next-steps"></a>Kolejne kroki

 - Aby przejrzeć komunikaty o błędach, jeśli są one wyzwalane w procesie rejestracji, zobacz [dzierżawy komunikaty o błędach rejestracji](azure-stack-csp-ref-infrastructure.md#usage-and-billing-error-codes).
 - Aby dowiedzieć się więcej o tym, jak można pobrać informacji o użyciu zasobów ze stosu Azure, zobacz [użycie i rozliczenia Azure stosu](/azure-stack-billing-and-chargeback.md).
 - Aby sprawdzić, jak klienta końcowy może dodać, jako dostawca usług Kryptograficznych, Menedżer ich stosu Azure dzierżawy, zobacz [włączenia dostawcy usług w chmurze do zarządzania subskrypcją Azure stosu](user\azure-stack-csp-enable-billing-usage-tracking.md).
