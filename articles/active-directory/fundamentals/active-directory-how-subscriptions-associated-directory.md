---
title: Dodaj istniejącą subskrypcję platformy Azure do swojej dzierżawy — Azure Active Directory | Microsoft Docs
description: Instrukcje dotyczące dodawania istniejącej subskrypcji platformy Azure do dzierżawy Azure Active Directory.
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
ms.openlocfilehash: f6bce05b99c14fb464cd1a17587d9cf254909d87
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73473392"
---
# <a name="associate-or-add-an-azure-subscription-to-your-azure-active-directory-tenant"></a>Kojarzenie lub Dodawanie subskrypcji platformy Azure do dzierżawy Azure Active Directory

Subskrypcja platformy Azure ma relację zaufania z usługą Azure Active Directory (Azure AD), co oznacza, że subskrypcja ufa usłudze Azure AD w celu uwierzytelniania użytkowników, usług i urządzeń. Wiele subskrypcji może ufać temu samemu katalogowi usługi Azure AD, ale każda subskrypcja może ufać tylko jednemu katalogowi.

Jeśli subskrypcja wygaśnie, utracisz dostęp do wszystkich innych zasobów skojarzonych z subskrypcją. Jednak katalog usługi Azure AD pozostaje na platformie Azure, co umożliwia skojarzenie i Zarządzanie katalogiem przy użyciu innej subskrypcji platformy Azure.

Wszyscy użytkownicy mają jeden katalog *macierzysty* na potrzeby uwierzytelniania. Jednak użytkownicy mogą również być Gośćmi w innych katalogach. Dla każdego użytkownika w usłudze Azure AD można zobaczyć zarówno katalog macierzysty, jak i gość.

> [!Important]
> Po skojarzeniu subskrypcji z innym katalogiem użytkownicy, którzy mają przypisane role przy użyciu [kontroli dostępu opartej na rolach (RBAC)](../../role-based-access-control/role-assignments-portal.md) , utracą dostęp. Administratorzy subskrypcji klasycznych (administrator usługi i współadministratorzy) również utracą dostęp. 
>
> Przypisania zasad są również usuwane z subskrypcji, gdy subskrypcja jest skojarzona z innym katalogiem.
> 
> Ponadto przeniesienie klastra usługi Azure Kubernetes Service (AKS) do innej subskrypcji lub przeniesienie subskrypcji będącej właścicielem klastra do nowej dzierżawy powoduje utratę funkcjonalności klastra z powodu utraty przypisań ról i uprawnień do podmiotów usługi. Aby uzyskać więcej informacji na temat AKS, zobacz [Azure Kubernetes Service (AKS)](https://docs.microsoft.com/azure/aks/).

## <a name="before-you-begin"></a>Przed rozpoczęciem

Aby można było skojarzyć lub dodać subskrypcję, należy wykonać następujące zadania:

1. Przejrzyj poniższą listę zmian i dowiesz się, jak to miało wpływ:

    - Użytkownicy, którym przypisano role przy użyciu RBAC, utracą dostęp
    - Administrator usługi i współadministratorzy utracą dostęp
    - Jeśli masz jakieś magazyny kluczy, staną się niedostępne i będziesz musiał rozwiązać te problemy po skojarzeniu
    - Jeśli masz jakiekolwiek zarządzane tożsamości dla zasobów, takich jak Virtual Machines lub Logic Apps, musisz je ponownie włączyć lub utworzyć po skojarzeniu
    - Jeśli masz zarejestrowaną Azure Stack, musisz ponownie zarejestrować ją po skojarzeniu

1. Zaloguj się przy użyciu konta, które:
    - Ma przypisanie roli [właściciela](../../role-based-access-control/built-in-roles.md#owner) do subskrypcji. Aby uzyskać informacje na temat sposobu przypisywania roli właściciela, zobacz [Zarządzanie dostępem do zasobów platformy Azure przy użyciu RBAC i Azure Portal](../../role-based-access-control/role-assignments-portal.md).
    - Istnieje zarówno w bieżącym katalogu, który jest skojarzony z subskrypcją, jak i w nowym katalogu, w którym chcesz skojarzyć subskrypcję do przodu. Aby uzyskać więcej informacji o uzyskiwaniu dostępu do innego katalogu, zobacz [jak administratorzy Azure Active Directory dodawać użytkowników współpracy B2B?](../b2b/add-users-administrator.md).

1. Upewnij się, że nie używasz subskrypcji dostawcy usług w chmurze (CSP) platformy Azure (MS-AZR-0145P, MS-AZR-0146P, MS-AZR-159P), wewnętrznej subskrypcji firmy Microsoft (MS-AZR-0015P) ani subskrypcji Microsoft Imagine (MS-AZR-0144P).
    
## <a name="to-associate-an-existing-subscription-to-your-azure-ad-directory"></a>Aby skojarzyć lub dodać istniejącą subskrypcję do katalogu usługi Azure AD

1. Zaloguj się i wybierz subskrypcję, której chcesz użyć na [stronie Subskrypcje w Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).

2. Wybierz pozycję **Zmień katalog**.

    ![Strona subskrypcje z wyróżnioną opcją zmiany katalogu](media/active-directory-how-subscriptions-associated-directory/change-directory-button.png)

3. Przejrzyj wszystkie wyświetlone ostrzeżenia, a następnie wybierz pozycję **Zmień**.

    ![Zmień stronę katalogu, pokazując katalog do zmiany](media/active-directory-how-subscriptions-associated-directory/edit-directory-ui.png)

    Katalog zostanie zmieniony dla subskrypcji i zostanie wyświetlony komunikat o powodzeniu.

    ![Komunikat o powodzeniu zmiany katalogu](media/active-directory-how-subscriptions-associated-directory/edit-directory-success.png)
4. Użyj przełącznika **katalogu** , aby przejść do nowego katalogu. Poprawne wyświetlanie wszystkiego może potrwać kilka godzin. Jeśli wydaje się, że trwa zbyt długo, sprawdź **globalną** subskrypcję filtru dla przeniesionej subskrypcji, aby upewnić się, że nie jest ukryta. Może być konieczne wylogowanie się z Azure Portal i ponowne zalogowanie się, aby można było zobaczyć nowy katalog. 

    ![Strona przełączników katalogów z przykładowymi informacjami](media/active-directory-how-subscriptions-associated-directory/directory-switcher.png)

Zmiana katalogu subskrypcji jest operacją na poziomie usługi, więc nie ma ona wpływu na własność rozliczeń subskrypcji. Administrator konta nadal może zmienić administratora usługi z [centrum konta](https://account.azure.com/subscriptions). Aby usunąć oryginalny katalog, należy przenieść własność rozliczeń subskrypcji do nowego administratora konta. Aby dowiedzieć się więcej na temat przenoszenia własności rozliczeń, zobacz [przenoszenie własności subskrypcji platformy Azure na inne konto](../../billing/billing-subscription-transfer.md).

## <a name="post-association-steps"></a>Kroki po skojarzeniu
Po skojarzeniu subskrypcji z innym katalogiem mogą wystąpić dodatkowe kroki, które należy wykonać w celu wznowienia operacji.

1. Jeśli masz jakieś magazyny kluczy, musisz zmienić identyfikator dzierżawy magazynu kluczy. Aby uzyskać więcej informacji, zobacz [zmiana identyfikatora dzierżawy magazynu kluczy po przeniesieniu subskrypcji](../../key-vault/key-vault-subscription-move-fix.md).

2. Jeśli używasz tożsamości zarządzanych przez system do zasobów, musisz je ponownie włączyć. W przypadku korzystania z tożsamości zarządzanych przypisanych przez użytkownika należy je utworzyć ponownie. Po ponownym włączeniu lub odtworzeniu tożsamości zarządzanych należy ponownie ustanowić uprawnienia przypisane do tych tożsamości. Aby uzyskać więcej informacji, zobacz [co to są tożsamości zarządzane dla zasobów platformy Azure?](../managed-identities-azure-resources/overview.md).

3. Jeśli zarejestrowano Azure Stack przy użyciu tej subskrypcji, należy ponownie zarejestrować. Aby uzyskać więcej informacji, zobacz [rejestrowanie Azure Stack na platformie Azure](/azure-stack/operator/azure-stack-registration).



## <a name="next-steps"></a>Następne kroki

- Aby utworzyć nową dzierżawę usługi Azure AD, zobacz [Azure Active Directory dostępu, aby utworzyć nową dzierżawę](active-directory-access-create-new-tenant.md)

- Aby dowiedzieć się więcej o kontrolowaniu dostępu do zasobów na platformie Microsoft Azure, zobacz [Understanding resource access in Azure](../../role-based-access-control/rbac-and-directory-admin-roles.md) (Opis dostępu do zasobów na platformie Azure).

- Aby dowiedzieć się więcej o sposobach przypisywania ról w usłudze Azure AD, zobacz [jak przypisywać role katalogu użytkownikom przy użyciu Azure Active Directory](active-directory-users-assign-role-azure-portal.md)
