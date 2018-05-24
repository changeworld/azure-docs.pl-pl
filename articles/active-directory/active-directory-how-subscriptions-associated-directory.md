---
title: Jak dodać istniejącą subskrypcję platformy Azure do katalogu usługi Azure AD | Microsoft Docs
description: Jak dodać istniejącą subskrypcję do katalogu usługi Azure AD
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: users-groups-roles
ms.topic: get-started-article
ms.date: 12/12/2017
ms.author: curtand
ms.reviewer: jeffsta
ms.custom: oldportal;it-pro;
ms.openlocfilehash: 6664a2b9bb6375314090bf4dae9190c7d52565d2
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
---
# <a name="how-to-associate-or-add-an-azure-subscription-to-azure-active-directory"></a>Jak skojarzyć lub dodać subskrypcję platformy Azure do usługi Azure Active Directory

W tym artykule opisano relację między subskrypcją platformy Azure i usługą Azure Active Directory (Azure AD) oraz sposób dodawania istniejącej subskrypcji do katalogu usługi Azure AD. Twoja subskrypcja platformy Azure ma relację zaufania z usługą Azure AD, co oznacza, że subskrypcja ufa katalogowi podczas uwierzytelniania użytkowników, usług i urządzeń. Wiele subskrypcji może ufać temu samemu katalogowi, ale każda subskrypcja może ufać tylko jednemu katalogowi. 

Relacja zaufania między subskrypcją a katalogiem różni się od relacji z innymi zasobami na platformie Azure (witrynami internetowymi, bazami danych itd.). Jeśli subskrypcja wygaśnie, dostęp do innych zasobów skojarzonych z tą subskrypcją również nie będzie możliwy. Katalog usługi Azure AD pozostanie jednak na platformie Azure i będzie można skojarzyć z nim inną subskrypcję oraz zarządzać katalogiem przy użyciu nowej subskrypcji.

Wszyscy użytkownicy mają jeden katalog macierzysty, który ich uwierzytelnia, ale mogą również być gośćmi w innych katalogach. W usłudze Azure AD możesz zobaczyć katalogi, w których Twoje konto użytkownika jest członkiem lub gościem.

## <a name="before-you-begin"></a>Przed rozpoczęciem

* Musisz zalogować się przy użyciu konta mającego dostęp właściciela RBAC do subskrypcji.
* Musisz zalogować się przy użyciu konta, które istnieje w bieżącym katalogu, z którym jest skojarzona subskrypcja, oraz w katalogu, do którego chcesz ją dodać. Aby dowiedzieć się więcej na temat uzyskiwania dostępu do innego katalogu, zobacz [W jaki sposób administratorzy usługi Azure Active Directory dodają użytkowników we współpracy B2B?](active-directory-b2b-admin-add-users.md)
* Ta funkcja jest niedostępna dla subskrypcji dostawców usług w chmurze (MS-AZR-0145P, MS-AZR-0146P, MS-AZR-159P) ani subskrypcji Microsoft Imagine (MS-AZR-0144P).

## <a name="to-associate-an-existing-subscription-to-your-azure-ad-directory"></a>Aby skojarzyć lub dodać istniejącą subskrypcję do katalogu usługi Azure AD

1. Zaloguj się i wybierz subskrypcję ze [strony Subskrypcje w witrynie Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
2. Kliknij przycisk **Zmień katalog**.

    ![Zrzut ekranu przedstawiający przycisk Zmień katalog](./media/active-directory-how-subscriptions-associated-directory/edit-directory-button.PNG)
3. Przejrzyj ostrzeżenia. Po zmianie katalogu subskrypcji wszyscy użytkownicy usługi [Kontrola dostępu oparta na rolach (RBAC)](../role-based-access-control/role-assignments-portal.md) z przypisanym dostępem oraz wszyscy administratorzy subskrypcji utracą dostęp.
4. Wybierz katalog.

    ![Zrzut ekranu przedstawiający interfejs użytkownika podczas zmiany katalogu](./media/active-directory-how-subscriptions-associated-directory/edit-directory-ui.PNG)
5. Kliknij przycisk **Zmień**.
6. To wszystko! Użyj przełącznika katalogów, aby przejść do nowego katalogu. Może upłynąć do 10 minut, zanim wszystko zostanie poprawnie wyświetlone.

    ![Zrzut ekranu przedstawiający powiadomienie o pomyślnej zmianie katalogu](./media/active-directory-how-subscriptions-associated-directory/edit-directory-success.PNG)

    ![Zrzut ekranu przedstawiający przełącznik](./media/active-directory-how-subscriptions-associated-directory/directory-switcher.PNG)


Przeniesienie subskrypcji ma także wpływ na wszelkie posiadane magazyny kluczy platformy Azure, dlatego [zmień identyfikator dzierżawy magazynu kluczy](../key-vault/key-vault-subscription-move-fix.md) przed wznowieniem operacji.

Zmienianie katalogu subskrypcji jest operacją wykonywaną na poziomie usługi. Nie ma ona wpływu na własność rozliczeń subskrypcji, a administrator konta nadal może zmienić administratora usługi za pomocą [Centrum konta](https://account.azure.com/subscriptions). Jeśli chcesz usunąć oryginalny katalog, musisz przenieść własność rozliczeń subskrypcji na nowego administratora konta. Aby dowiedzieć się więcej na temat przenoszenia własności rozliczeń, zobacz [Transfer ownership of an Azure subscription to another account](../billing/billing-subscription-transfer.md) (Przenoszenie własności subskrypcji platformy Azure na inne konto). 

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej na temat bezpłatnego tworzenia nowego katalogu usługi Azure AD, zobacz [Jak uzyskać dzierżawę usługi Azure Active Directory](develop/active-directory-howto-tenant.md).
* Aby dowiedzieć się więcej na temat przenoszenia własności rozliczeń subskrypcji platformy Azure, zobacz [Transfer ownership of an Azure subscription to another account](../billing/billing-subscription-transfer.md) (Przenoszenie własności subskrypcji platformy Azure na inne konto).
* Aby dowiedzieć się więcej o kontrolowaniu dostępu do zasobów na platformie Microsoft Azure, zobacz [Understanding resource access in Azure](../role-based-access-control/rbac-and-directory-admin-roles.md) (Opis dostępu do zasobów na platformie Azure).
* Aby uzyskać więcej informacji na temat sposobu przypisywania ról w usłudze Azure AD, zobacz [Przypisywanie ról administratorów w usłudze Azure Active Directory](active-directory-assign-admin-roles-azure-portal.md).

<!--Image references-->
[1]: ./media/active-directory-how-subscriptions-associated-directory/WAAD_PassThruAuth.png
[2]: ./media/active-directory-how-subscriptions-associated-directory/WAAD_OrgAccountSubscription.png
[3]: ./media/active-directory-how-subscriptions-associated-directory/WAAD_SignInDisambiguation.PNG
