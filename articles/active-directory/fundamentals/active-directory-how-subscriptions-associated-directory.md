---
title: Dodać istniejącą subskrypcję platformy Azure z dzierżawą — usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Instrukcje dotyczące sposobu dodawania istniejącej subskrypcji platformy Azure z dzierżawą usługi Azure Active Directory.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: fb4fa92d8b3c174cdf9b3695f8564cc11c1ad291
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/08/2019
ms.locfileid: "68851747"
---
# <a name="associate-or-add-an-azure-subscription-to-your-azure-active-directory-tenant"></a>Skojarzyć lub dodać subskrypcję platformy Azure z dzierżawą usługi Azure Active Directory

Subskrypcja platformy Azure ma relację zaufania z usługą Azure Active Directory (Azure AD), co oznacza, że subskrypcja ufa usłudze Azure AD w celu uwierzytelniania użytkowników, usług i urządzeń. Wiele subskrypcji może ufać tego samego katalogu usługi Azure AD, ale każda subskrypcja zaufać tylko jeden katalog.

Jeśli subskrypcja wygaśnie, utracisz dostęp do wszystkich innych zasobów skojarzonych z tą subskrypcją. Jednak katalogu usługi Azure AD pozostanie na platformie Azure, umożliwiając skojarzenia i zarządzać katalogiem przy użyciu innej subskrypcji platformy Azure.

Wszyscy użytkownicy mają jeden katalog macierzysty na potrzeby uwierzytelniania. Jednak użytkownicy mogą również być gośćmi w innych katalogach. Możesz zobaczyć oba katalogi gości i macierzystego dla każdego użytkownika w usłudze Azure AD.

> [!Important]
> Po skojarzeniu subskrypcji z innym katalogiem użytkownicy, którzy mają przypisane role przy użyciu [kontroli dostępu opartej na rolach (RBAC)](../../role-based-access-control/role-assignments-portal.md) , utracą dostęp. Administratorzy subskrypcji klasycznych (administrator usługi i współadministratorzy) również utracą dostęp.
> 
> Ponadto przeniesienie klastra usługi Azure Kubernetes Service (AKS) do innej subskrypcji lub przeniesienie subskrypcji będącej właścicielem klastra do nowej dzierżawy powoduje utratę funkcjonalności klastra z powodu utraty przypisań ról i uprawnień do podmiotów usługi. Aby uzyskać więcej informacji na temat AKS, zobacz [Azure Kubernetes Service (AKS)](https://docs.microsoft.com/azure/aks/).

## <a name="before-you-begin"></a>Przed rozpoczęciem

Zanim można skojarzyć lub dodać subskrypcję, należy wykonać następujące zadania:

1. Przejrzyj poniższą listę zmian i dowiesz się, jak to miało wpływ:

    - Użytkownicy, którym przypisano role przy użyciu RBAC, utracą dostęp
    - Administrator usługi i współadministratorzy utracą dostęp
    - Jeśli masz jakieś magazyny kluczy, staną się niedostępne i będziesz musiał rozwiązać te problemy po skojarzeniu
    - Jeśli masz jakiekolwiek zarządzane tożsamości dla zasobów, takich jak Virtual Machines lub Logic Apps, musisz je ponownie włączyć lub utworzyć po skojarzeniu
    - Jeśli masz zarejestrowaną Azure Stack, musisz ponownie zarejestrować ją po skojarzeniu

1. Zaloguj się przy użyciu konta, które:
    - Ma przypisanie roli [właściciela](../../role-based-access-control/built-in-roles.md#owner) do subskrypcji. Aby uzyskać informacje na temat sposobu przypisywania roli właściciela, zobacz [Zarządzanie dostępem do zasobów platformy Azure przy użyciu RBAC i Azure Portal](../../role-based-access-control/role-assignments-portal.md).
    - Istnieje w bieżącym katalogu, który jest skojarzony z subskrypcją i nowego katalogu, który jest, które chcesz skojarzyć subskrypcję, w przyszłości. Aby uzyskać więcej informacji na temat uzyskiwania dostępu do innego katalogu, zobacz [jak Administratorzy usługi Azure Active Directory dodać użytkowników we współpracy B2B?](../b2b/add-users-administrator.md).

1. Upewnij się, że nie używasz subskrypcję dostawcy usługi Azure w chmurze (CSP) (MS-AZR - 0145 P, MS - AZR - 0146 P, MS - AZR - 159 P), subskrypcję Internal firmy Microsoft (MS-AZR - 0015 P) lub subskrypcję programu Microsoft Imagine (MS-AZR - 0144 P).
    
## <a name="to-associate-an-existing-subscription-to-your-azure-ad-directory"></a>Aby skojarzyć lub dodać istniejącą subskrypcję do katalogu usługi Azure AD

1. Zaloguj się i wybierz subskrypcję, której chcesz użyć z [strony subskrypcje w witrynie Azure portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).

2. Wybierz **Zmień katalog**.

    ![Strony subskrypcje, z podświetloną opcją katalogu zmiany](media/active-directory-how-subscriptions-associated-directory/change-directory-button.png)

3. Przejrzyj wszelkie ostrzeżenia, które są wyświetlane, a następnie wybierz **zmiany**.

    ![Zmień przedstawiający katalogu, aby zmienić stronę katalogu](media/active-directory-how-subscriptions-associated-directory/edit-directory-ui.png)

    Zmianie katalogu subskrypcji, i otrzymujesz komunikat o powodzeniu.

    ![Komunikat o powodzeniu zmiany katalogu](media/active-directory-how-subscriptions-associated-directory/edit-directory-success.png)
4. Użyj przełącznika **katalogu** , aby przejść do nowego katalogu. Poprawne wyświetlanie wszystkiego może potrwać kilka godzin. Jeśli wydaje się, że trwa zbyt długo, sprawdź globalną subskrypcję **filtru** dla przeniesionej subskrypcji, aby upewnić się, że nie jest ukryta. Może być konieczne wylogowanie się z Azure Portal i ponowne zalogowanie się, aby można było zobaczyć nowy katalog. 

    ![Strona przełączników katalogów z przykładowymi informacjami](media/active-directory-how-subscriptions-associated-directory/directory-switcher.png)

Zmienianie katalogu subskrypcji jest operacją poziomu usług, dzięki czemu nie ma wpływu na własność rozliczeń subskrypcji. Administrator konta nadal może zmienić administratora usługi z [Centrum kont](https://account.azure.com/subscriptions). Aby usunąć oryginalny katalog, trzeba przekazać subskrypcji, rozliczeń prawa własności do nowego administratora konta. Aby dowiedzieć się więcej na temat przenoszenia własności rozliczeń, zobacz [Transfer ownership of an Azure subscription to another account](../../billing/billing-subscription-transfer.md) (Przenoszenie własności subskrypcji platformy Azure na inne konto).

## <a name="post-association-steps"></a>Kroki po skojarzeniu
Po skojarzeniu subskrypcji z innym katalogiem mogą wystąpić dodatkowe kroki, które należy wykonać w celu wznowienia operacji.

1. Jeśli masz jakieś magazyny kluczy, musisz zmienić identyfikator dzierżawy magazynu kluczy. Aby uzyskać więcej informacji, zobacz [zmiana identyfikatora dzierżawy magazynu kluczy po przeniesieniu subskrypcji](../../key-vault/key-vault-subscription-move-fix.md).

2. Jeśli używasz tożsamości zarządzanych przez system do zasobów, musisz je ponownie włączyć. W przypadku korzystania z tożsamości zarządzanych przypisanych przez użytkownika należy je utworzyć ponownie. Po ponownym włączeniu lub odtworzeniu tożsamości zarządzanych należy ponownie ustanowić uprawnienia przypisane do tych tożsamości. Aby uzyskać więcej informacji, zobacz [co to są tożsamości zarządzane dla zasobów platformy Azure?](../managed-identities-azure-resources/overview.md).

3. Jeśli zarejestrowano Azure Stack przy użyciu tej subskrypcji, należy ponownie zarejestrować. Aby uzyskać więcej informacji, zobacz [rejestrowanie Azure Stack na platformie Azure](/azure-stack/operator/azure-stack-registration).



## <a name="next-steps"></a>Następne kroki

- Aby utworzyć nowe usługi Azure AD dzierżawy, zobacz [dostępu do usługi Azure Active Directory, aby utworzyć nową dzierżawę](active-directory-access-create-new-tenant.md)

- Aby dowiedzieć się więcej o kontrolowaniu dostępu do zasobów na platformie Microsoft Azure, zobacz [Understanding resource access in Azure](../../role-based-access-control/rbac-and-directory-admin-roles.md) (Opis dostępu do zasobów na platformie Azure).

- Aby dowiedzieć się więcej na temat sposobu przypisywania ról w usłudze Azure AD, zobacz [sposobu przypisywania ról w katalogu dla użytkowników w usłudze Azure Active Directory](active-directory-users-assign-role-azure-portal.md)
