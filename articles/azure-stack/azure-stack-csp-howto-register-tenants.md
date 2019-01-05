---
title: Dodaj dzierżawców za użycie i rozliczenia w usłudze Azure Stack | Dokumentacja firmy Microsoft
description: Czynności wymagane Dodaj użytkownika końcowego do usługi Azure Stack, zarządzane przez dostawcę usług chmury (CSP).
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/05/2019
ms.author: sethm
ms.reviewer: alfredo
ms.openlocfilehash: be240ea17a8679d25623f971f7be5ea5c3ad2e8f
ms.sourcegitcommit: d61faf71620a6a55dda014a665155f2a5dcd3fa2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/04/2019
ms.locfileid: "54053246"
---
# <a name="add-tenant-for-usage-and-billing-to-azure-stack"></a>Dodawanie dzierżawy za użycie i rozliczenia w usłudze Azure Stack

*Dotyczy: Zintegrowane systemy usługi Azure Stack*

W tym artykule opisano kroki wymagane do dodania konta użytkownika końcowego do wdrożenia usługi Azure Stack, zarządzane przez dostawcę usług chmury (CSP). Gdy nowa dzierżawa korzysta z zasobów, usługi Azure Stack raportów użycia do swoich subskrypcji dostawcy CSP.

Dostawcy usług kryptograficznych często oferty usług dla wielu klientów końcowych (dzierżawcy) na to wdrożenie usługi Azure Stack. Dodawanie dzierżawy do rejestracji w usłudze Azure Stack temu użycia każdego dzierżawcy jest zgłaszane i rozliczane w odpowiedniej subskrypcji dostawcy CSP. Jeśli nie wykonasz kroki opisane w tym artykule, wykorzystanie dzierżaw jest obciążany opłatą za subskrypcję używaną w wstępnej rejestracji usługi Azure Stack. Przed dodaniem klienta końcowego do usługi Azure Stack dla śledzenia użycia oraz zarządzanie ich dzierżawy, należy skonfigurować usługę Azure Stack jako dostawca CSP. Kroki i zasobów, zobacz [Zarządzanie użycia i rozliczeń dla usługi Azure Stack jako dostawca usług w chmurze](azure-stack-add-manage-billing-as-a-csp.md).

Na poniższej ilustracji przedstawiono kroki, które należy wykonać, aby włączyć nowego klienta do używania usługi Azure Stack i skonfigurować śledzenie klienta użycia dostawcy usług Kryptograficznych. Dodając klienta końcowego, jesteś także możliwość zarządzania zasobami w usłudze Azure Stack. Masz dwie opcje do zarządzania zasobami:

1. Można zachować klienta końcowego i podaj poświadczenia dla lokalnych subskrypcji usługi Azure Stack dla klienta końcowego.  
2. Odbiorcy końcowego można pracować lokalnie swoją subskrypcję i dostawcy usług Kryptograficznych jako gościa z uprawnieniami właściciela.  

## <a name="steps-to-add-an-end-customer"></a>Kroki, aby dodać klienta końcowego

![Konfigurowanie dostawcy usług w chmurze, do śledzenia użycia i zarządzać kontem klienta końcowego](media/azure-stack-csp-enable-billing-usage-tracking/process-csp-enable-billing.png)

### <a name="create-a-new-customer-in-partner-center"></a>Tworzenie nowego klienta w Centrum partnerskiego

W Centrum partnerskim należy utworzyć nową subskrypcję platformy Azure dla klienta. Aby uzyskać instrukcje, zobacz [dodać nowego klienta](https://msdn.microsoft.com/partner-center/add-a-new-customer).

### <a name="create-an-azure-subscription-for-the-end-customer"></a>Utwórz subskrypcję platformy Azure dla klientów końcowych

Po utworzeniu rekord klienta w Centrum partnerskim, możesz sprzedawać subskrypcje produktów w katalogu. Aby uzyskać instrukcje, zobacz [tworzenia, zawiesić lub anulować subskrypcje klientów](https://msdn.microsoft.com/partner-center/create-a-new-subscription).

### <a name="create-a-guest-user-in-the-end-customer-directory"></a>Tworzenie użytkownika-gościa w katalogu klientów końcowych

Jeśli odbiorcy końcowego zarządza własnego konta, Utwórz użytkownik-Gość w ich katalogu, a następnie wysłać im informacje. Użytkownik końcowy będzie następnie Dodaj gościa i podnieść poziom uprawnień gościa do **właściciela** dla konta usługi Azure Stack dostawcy usług Kryptograficznych.

### <a name="update-the-registration-with-the-end-customer-subscription"></a>Aktualizowanie rejestracji przy użyciu subskrypcji klienta końcowego

Zaktualizuj swoją rejestrację przy użyciu nowej subskrypcji klienta. Azure raporty użycia przez klientów przy użyciu tożsamości odbiorcy z Centrum partnerskiego. Ten krok zapewnia, że raportowania użycia każdego klienta w ramach poszczególnych subskrypcji dostawcy CSP tego klienta. Ułatwia to śledzenie użytkownika użycia i rozliczeń.

> [!NOTE]  
> Aby wykonać ten krok, musisz mieć [zarejestrowane w usłudze Azure Stack](azure-stack-register.md).

1. Otwórz środowiska Windows PowerShell z podwyższonym poziomem uprawnień wiersza i uruchom:  
    `Add-AzureRmAccount`
2. Wpisz swoje poświadczenia platformy Azure.
3. W sesji programu PowerShell Uruchom polecenie:

   ```powershell
   New-AzureRmResource -ResourceId "subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/{customerSubscriptionId}" -ApiVersion 2017-06-01 -Properties <PSObject>
   ```

### <a name="new-azurermresource-powershell-parameters"></a>Parametry nowej AzureRmResource programu PowerShell

W poniższej sekcji opisano parametry **New-AzureRmResource** polecenia cmdlet:

| Parametr | Opis |
| --- | --- |
|registrationSubscriptionID | Subskrypcja platformy Azure, który został użyty podczas wstępnej rejestracji usługi Azure Stack.|
| customerSubscriptionID | Subskrypcja platformy Azure (nie usługi Azure Stack) należące do klientów do zarejestrowania. Musi być utworzona w ramach oferty dostawcy usług Kryptograficznych; w praktyce oznacza to, za pośrednictwem Centrum partnerskiego. Jeśli klient ma więcej niż jedną dzierżawę usługi Azure Active Directory, należy utworzyć tej subskrypcji w ramach dzierżawy, która będzie służyć do logowania się do usługi Azure Stack. Identyfikator subskrypcji klienta, należy użyć małe litery. |
| resourceGroup | Grupa zasobów na platformie Azure, w którym przechowywany jest rejestracja. |
| registrationName | Nazwa rejestracji usługi Azure Stack. Jest to obiekt przechowywanych na platformie Azure. |
| Właściwości | Określa właściwości zasobu. Ten parametr umożliwia określenie wartości właściwości, które są specyficzne dla typu zasobu.

> [!NOTE]  
> Dzierżawy muszą być zarejestrowane w usłudze każdego używają usługi Azure Stack. Jeśli masz dwa wdrożenia usługi Azure Stack oraz dzierżawy będą wykorzystywane serwery z nich należy zaktualizować początkowej rejestracje każdego wdrożenia przy użyciu subskrypcji dzierżawcy.

### <a name="onboard-tenant-to-azure-stack"></a>Dołączanie dzierżawy do usługi Azure Stack

Konfigurowanie usługi Azure Stack w celu obsługi użytkowników z wielu dzierżaw usługi Azure AD do korzystania z usług w usłudze Azure Stack. Aby uzyskać instrukcje, zobacz [Włączanie wielodostępu w usłudze Azure Stack](azure-stack-enable-multitenancy.md).

### <a name="create-a-local-resource-in-the-end-customer-tenant-in-azure-stack"></a>Tworzenie zasobu lokalnego w dzierżawie klienta końcowego w usłudze Azure Stack

Po dodaniu nowego klienta do usługi Azure Stack lub dzierżawie klienta końcowego została włączona na koncie gościa z uprawnieniami właściciela, sprawdź, czy można utworzyć zasobu w ramach ich dzierżawy. Na przykład mogą oni [utworzyć maszynę wirtualną Windows za pomocą portalu usługi Azure Stack](user/azure-stack-quick-windows-portal.md).

## <a name="next-steps"></a>Kolejne kroki

- Aby przejrzeć komunikaty o błędach, jeśli są one wyzwalane w procesie rejestracji, zobacz [dzierżawy komunikaty o błędach rejestracji](azure-stack-csp-ref-infrastructure.md#usage-and-billing-error-codes).
- Aby dowiedzieć się więcej o tym, jak pobrać informacje o użyciu zasobów z usługi Azure Stack, zobacz [użycie i rozliczenia w usłudze Azure Stack](azure-stack-billing-and-chargeback.md).
- Aby zapoznać się, jak klienta końcowego może dodać, jako dostawcy usług Kryptograficznych, jako Menedżer ds. dla swojej dzierżawy usługi Azure Stack, zobacz [włączenia dostawcy usług w chmurze do zarządzania subskrypcją usługi Azure Stack](user/azure-stack-csp-enable-billing-usage-tracking.md).
