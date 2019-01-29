---
title: Dodać istniejącą subskrypcję platformy Azure z dzierżawą — usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Instrukcje dotyczące sposobu dodawania istniejącej subskrypcji platformy Azure z dzierżawą usługi Azure Active Directory.
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: lizross
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.openlocfilehash: 86ba8f77121e6d4f6358887e13b8f86a5e020f1b
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/28/2019
ms.locfileid: "55103704"
---
# <a name="associate-or-add-an-azure-subscription-to-your-azure-active-directory-tenant"></a>Skojarzyć lub dodać subskrypcję platformy Azure z dzierżawą usługi Azure Active Directory
Subskrypcja platformy Azure ma relację zaufania w usłudze Azure Active Directory (Azure AD), co oznacza, że subskrypcja ufa usługi Azure AD do uwierzytelniania użytkowników, usług i urządzeń. Wiele subskrypcji może ufać tego samego katalogu usługi Azure AD, ale każda subskrypcja zaufać tylko jeden katalog.

Jeśli subskrypcja wygaśnie, utracisz dostęp do wszystkich innych zasobów skojarzonych z tą subskrypcją. Jednak katalogu usługi Azure AD pozostanie na platformie Azure, umożliwiając skojarzenia i zarządzać katalogiem przy użyciu innej subskrypcji platformy Azure.

Wszyscy użytkownicy mają jeden katalog "domowy" dla uwierzytelniania. Jednak użytkownicy mogą również być gośćmi w innych katalogach. Możesz zobaczyć oba katalogi gości i macierzystego dla każdego użytkownika w usłudze Azure AD.

>[!Important]
>Wszystkie [kontroli dostępu opartej na rolach (RBAC)](../../role-based-access-control/role-assignments-portal.md) użytkowników za pomocą przypisanego dostępu oraz wszyscy administratorzy subskrypcji utracą dostęp po subskrypcji zmiany w katalogu. Ponadto jeśli masz żadnych magazynów kluczy, ich będzie również zostaną objęte przeniesienia subskrypcji. Aby rozwiązać ten problem, musisz mieć [zmiana Identyfikatora dzierżawy magazynu kluczy](../../key-vault/key-vault-subscription-move-fix.md) przed wznowieniem operacji.


## <a name="before-you-begin"></a>Przed rozpoczęciem
Zanim można skojarzyć lub dodać subskrypcję, należy wykonać następujące zadania:

- Zaloguj się przy użyciu konta, które:
    - Ma **właściciela RBAC** dostępu do subskrypcji.

    - Istnieje w bieżącym katalogu, który jest skojarzony z subskrypcją i nowego katalogu, który jest, które chcesz skojarzyć subskrypcję, w przyszłości. Aby uzyskać więcej informacji na temat uzyskiwania dostępu do innego katalogu, zobacz [jak Administratorzy usługi Azure Active Directory dodać użytkowników we współpracy B2B?](../b2b/add-users-administrator.md).

- Upewnij się, że nie używasz subskrypcję dostawcy usługi Azure w chmurze (CSP) (MS-AZR - 0145 P, MS - AZR - 0146 P, MS - AZR - 159 P), subskrypcję Internal firmy Microsoft (MS-AZR - 0015 P) lub subskrypcję programu Microsoft Imagine (MS-AZR - 0144 P).
    
## <a name="to-associate-an-existing-subscription-to-your-azure-ad-directory"></a>Aby skojarzyć lub dodać istniejącą subskrypcję do katalogu usługi Azure AD
1. Zaloguj się i wybierz subskrypcję, której chcesz użyć z [strony subskrypcje w witrynie Azure portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).

2. Wybierz **Zmień katalog**.

    ![Strony subskrypcje, z podświetloną opcją katalogu zmiany](media/active-directory-how-subscriptions-associated-directory/change-directory-button.png)

3. Przejrzyj wszelkie ostrzeżenia, które są wyświetlane, a następnie wybierz **zmiany**.

    ![Zmień przedstawiający katalogu, aby zmienić stronę katalogu](media/active-directory-how-subscriptions-associated-directory/edit-directory-ui.png)

    Zmianie katalogu subskrypcji, i otrzymujesz komunikat o powodzeniu.

    ![Komunikat o powodzeniu](media/active-directory-how-subscriptions-associated-directory/edit-directory-success.png)    

4. Użyj przełącznika katalogów, aby przejść do nowego katalogu. Może upłynąć do 10 minut, zanim wszystko zostanie poprawnie wyświetlone.

    ![Strony przełącznik katalogu](media/active-directory-how-subscriptions-associated-directory/directory-switcher.png)

Zmienianie katalogu subskrypcji jest operacją poziomu usług, dzięki czemu nie ma wpływu na własność rozliczeń subskrypcji. Administrator konta nadal może zmienić administratora usługi z [Centrum kont](https://account.azure.com/subscriptions). Aby usunąć oryginalny katalog, trzeba przekazać subskrypcji, rozliczeń prawa własności do nowego administratora konta. Aby dowiedzieć się więcej na temat przenoszenia własności rozliczeń, zobacz [Transfer ownership of an Azure subscription to another account](../../billing/billing-subscription-transfer.md) (Przenoszenie własności subskrypcji platformy Azure na inne konto). 

## <a name="next-steps"></a>Kolejne kroki

- Aby utworzyć nowe usługi Azure AD dzierżawy, zobacz [dostępu do usługi Azure Active Directory, aby utworzyć nową dzierżawę](active-directory-access-create-new-tenant.md)

- Aby dowiedzieć się więcej o kontrolowaniu dostępu do zasobów na platformie Microsoft Azure, zobacz [Understanding resource access in Azure](../../role-based-access-control/rbac-and-directory-admin-roles.md) (Opis dostępu do zasobów na platformie Azure).

- Aby dowiedzieć się więcej na temat sposobu przypisywania ról w usłudze Azure AD, zobacz [sposobu przypisywania ról w katalogu dla użytkowników w usłudze Azure Active Directory](active-directory-users-assign-role-azure-portal.md)
