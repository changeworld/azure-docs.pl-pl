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
ms.date: 03/13/2019
ms.author: lizross
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: b141de4c22ba1ba1325982d8e027fa46e0909fbb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60248764"
---
# <a name="associate-or-add-an-azure-subscription-to-your-azure-active-directory-tenant"></a>Skojarzyć lub dodać subskrypcję platformy Azure z dzierżawą usługi Azure Active Directory

Subskrypcja platformy Azure ma relację zaufania z usługi Azure Active Directory (Azure AD), co oznacza, że subskrypcja ufa usługi Azure AD do uwierzytelniania użytkowników, usług i urządzeń. Wiele subskrypcji może ufać tego samego katalogu usługi Azure AD, ale każda subskrypcja zaufać tylko jeden katalog.

Jeśli subskrypcja wygaśnie, utracisz dostęp do wszystkich innych zasobów skojarzonych z tą subskrypcją. Jednak katalogu usługi Azure AD pozostanie na platformie Azure, umożliwiając skojarzenia i zarządzać katalogiem przy użyciu innej subskrypcji platformy Azure.

Wszyscy użytkownicy mają jeden *macierzystego* katalogu do uwierzytelniania. Jednak użytkownicy mogą również być gośćmi w innych katalogach. Możesz zobaczyć oba katalogi gości i macierzystego dla każdego użytkownika w usłudze Azure AD.

> [!Important]
> Kiedy należy skojarzyć subskrypcję z innym katalogiem, użytkowników, którzy mają przypisane przy użyciu ról [kontroli dostępu opartej na rolach (RBAC)](../../role-based-access-control/role-assignments-portal.md) utracą dostęp. Klasyczni Administratorzy (Administrator usługi i Współadministratorzy) również utracą dostęp.
> 
> Ponadto przenoszenie klastra Azure Kubernetes Service (AKS) do innej subskrypcji lub przenoszenie subskrypcji będącej właścicielem klastra do nowego dzierżawcy, powoduje, że klaster, aby utracić funkcjonalność ze względu na przypisań ról utracone i praw podmiotów zabezpieczeń usługi. Aby uzyskać więcej informacji o usłudze AKS, zobacz [Azure Kubernetes Service (AKS)](https://docs.microsoft.com/en-us/azure/aks/).

## <a name="before-you-begin"></a>Przed rozpoczęciem

Zanim można skojarzyć lub dodać subskrypcję, należy wykonać następujące zadania:

1. Przejrzyj następujące listy zmian i jak użytkownik może mieć wpływ na:

    - Użytkownicy, które zostały przypisane do ról za pomocą funkcji RBAC spowoduje utratę dostępu
    - Administrator usługi i Współadministratorzy utracą dostęp
    - Jeśli masz żadnych magazynów kluczy, będą niedostępne i trzeba będzie je naprawić po dokonaniu skojarzenia
    - Jeśli masz zarejestrowane usługi Azure Stack, musisz ponownie zarejestrować po dokonaniu skojarzenia

1. Zaloguj się przy użyciu konta, które:
    - Ma [właściciela](../../role-based-access-control/built-in-roles.md#owner) przypisania roli dla subskrypcji. Aby uzyskać informacje dotyczące sposobu przypisywania roli właściciel, zobacz [zarządzanie dostępem do zasobów platformy Azure przy użyciu RBAC i witryny Azure portal](../../role-based-access-control/role-assignments-portal.md).
    - Istnieje w bieżącym katalogu, który jest skojarzony z subskrypcją i nowego katalogu, który jest, które chcesz skojarzyć subskrypcję, w przyszłości. Aby uzyskać więcej informacji na temat uzyskiwania dostępu do innego katalogu, zobacz [jak Administratorzy usługi Azure Active Directory dodać użytkowników we współpracy B2B?](../b2b/add-users-administrator.md).

1. Upewnij się, że nie używasz subskrypcję dostawcy usługi Azure w chmurze (CSP) (MS-AZR - 0145 P, MS - AZR - 0146 P, MS - AZR - 159 P), subskrypcję Internal firmy Microsoft (MS-AZR - 0015 P) lub subskrypcję programu Microsoft Imagine (MS-AZR - 0144 P).
    
## <a name="to-associate-an-existing-subscription-to-your-azure-ad-directory"></a>Aby skojarzyć lub dodać istniejącą subskrypcję do katalogu usługi Azure AD

1. Zaloguj się i wybierz subskrypcję, której chcesz użyć z [strony subskrypcje w witrynie Azure portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).

2. Wybierz **Zmień katalog**.

    ![Strony subskrypcje, z podświetloną opcją katalogu zmiany](media/active-directory-how-subscriptions-associated-directory/change-directory-button.png)

3. Przejrzyj wszelkie ostrzeżenia, które są wyświetlane, a następnie wybierz **zmiany**.

    ![Zmień przedstawiający katalogu, aby zmienić stronę katalogu](media/active-directory-how-subscriptions-associated-directory/edit-directory-ui.png)

    Zmianie katalogu subskrypcji, i otrzymujesz komunikat o powodzeniu.

    ![Komunikat informujący o zmianach katalogu](media/active-directory-how-subscriptions-associated-directory/edit-directory-success.png)    
4. Użyj **przełącznika katalogów** można przejść do nowego katalogu. Może upłynąć do 10 minut, zanim wszystko zostanie poprawnie wyświetlone.

    ![Strona przełącznik katalogu, z przykładowe informacje](media/active-directory-how-subscriptions-associated-directory/directory-switcher.png)

Zmienianie katalogu subskrypcji jest operacją poziomu usług, dzięki czemu nie ma wpływu na własność rozliczeń subskrypcji. Administrator konta nadal może zmienić administratora usługi z [Centrum kont](https://account.azure.com/subscriptions). Aby usunąć oryginalny katalog, trzeba przekazać subskrypcji, rozliczeń prawa własności do nowego administratora konta. Aby dowiedzieć się więcej na temat przenoszenia własności rozliczeń, zobacz [Transfer ownership of an Azure subscription to another account](../../billing/billing-subscription-transfer.md) (Przenoszenie własności subskrypcji platformy Azure na inne konto).

## <a name="post-association-steps"></a>Skojarzenie kroki po
Po skojarzeniu subskrypcję z innym katalogiem, może to być dodatkowe kroki, które należy wykonać, aby wznowić operacje.

1. Jeśli masz żadnych magazynów kluczy, należy zmienić identyfikator dzierżawy magazynu kluczy Aby uzyskać więcej informacji, zobacz [zmiana Identyfikatora dzierżawy magazynu kluczy po przeniesieniu subskrypcji](../../key-vault/key-vault-subscription-move-fix.md).

2. Jeśli została zarejestrowana przy użyciu tej subskrypcji usługi Azure Stack, należy ponownie zarejestrować. Aby uzyskać więcej informacji, zobacz [zarejestrować w usłudze Azure Stack z platformą Azure](/azure-stack/operator/azure-stack-registration).



## <a name="next-steps"></a>Kolejne kroki

- Aby utworzyć nowe usługi Azure AD dzierżawy, zobacz [dostępu do usługi Azure Active Directory, aby utworzyć nową dzierżawę](active-directory-access-create-new-tenant.md)

- Aby dowiedzieć się więcej o kontrolowaniu dostępu do zasobów na platformie Microsoft Azure, zobacz [Understanding resource access in Azure](../../role-based-access-control/rbac-and-directory-admin-roles.md) (Opis dostępu do zasobów na platformie Azure).

- Aby dowiedzieć się więcej na temat sposobu przypisywania ról w usłudze Azure AD, zobacz [sposobu przypisywania ról w katalogu dla użytkowników w usłudze Azure Active Directory](active-directory-users-assign-role-azure-portal.md)
