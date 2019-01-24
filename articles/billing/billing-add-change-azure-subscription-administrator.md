---
title: Dodawanie lub zmienianie ról subskrypcji platformy Azure administratora | Dokumentacja firmy Microsoft
description: Opisuje, jak dodać lub zmienić Azure Współadministratora, administratora usługi i Administrator konta
services: ''
documentationcenter: ''
author: genlin
manager: adpick
editor: ''
tags: billing
ms.assetid: 13a72d76-e043-4212-bcac-a35f4a27ee26
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 1/23/2019
ms.author: cwatson
ms.openlocfilehash: 4490db0c479abdda19957be98335edeefc08bb59
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2019
ms.locfileid: "54808752"
---
# <a name="add-or-change-azure-subscription-administrators"></a>Dodawanie lub zmienianie Administratorzy subskrypcji platformy Azure

Zarządzanie dostępem do zasobów platformy Azure wymaga odpowiedniej roli administratora. W tym artykule opisano, jak dodać lub zmienić roli administratora dla użytkownika na poziomie subskrypcji.

## <a name="what-administrator-role-do-i-use"></a>Jaką rolę administratora należy używać?

Platforma Azure oferuje kilka różnych ról. Aby zarządzać dostępem do zasobów, można użyć klasyczni ról administratora, takie jak administrator usługi i administratora współpracującego lub nowszych systemach autoryzacji o nazwie kontroli dostępu opartej na rolach (RBAC). Aby zapewnić lepszą kontrolę i uprościć zarządzanie dostępem, zaleca się używać funkcji RBAC dla wszystkich potrzeb związanych z zarządzaniem dostępu. Jeśli to możliwe firma Microsoft zaleca, skonfiguruj ponownie istniejących zasad dostępu przy użyciu funkcji RBAC. Aby uzyskać więcej informacji, zobacz [co to jest kontrola dostępu oparta na rolach (RBAC)](../role-based-access-control/overview.md) i [zrozumienie różnych ról na platformie Azure](../role-based-access-control/rbac-and-directory-admin-roles.md).

<a name="add-an-admin-for-a-subscription"></a>

## <a name="add-an-rbac-owner-for-a-subscription-in-azure-portal"></a>Dodaj właściciela RBAC do subskrypcji w witrynie Azure portal 

Aby dodać kogoś jako administratora subskrypcji platformy Azure, przypisz do niego rolę [Właściciel](../role-based-access-control/built-in-roles.md#owner) (rolę RBAC) w zakresie subskrypcji. Rola Właściciel umożliwia zarządzanie zasobami w ramach przypisanej subskrypcji bez praw dostępu do innych subskrypcji.

1. Odwiedź stronę [ **subskrypcje** w witrynie Azure portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
2. Wybierz subskrypcję, dla której ma zostać nadany dostęp.
3. Wybierz pozycję **Kontrola dostępu (IAM)** na liście.
4. Wybierz **Dodaj przypisanie roli**.
   (Jeśli brakuje przycisku Dodaj przypisanie roli, nie masz uprawnień, aby dodać uprawnienia.)
5. W polu **Rola** wybierz opcję **Właściciel**. 
6. W **Przypisz dostęp do** wybierz opcję **użytkownika, grupy lub jednostki usługi Azure AD**. 
7. W polu **Wybierz** wpisz adres e-mail użytkownika, którego chcesz dodać jako właściciela. Wybierz użytkownika, a następnie wybierz polecenie **Zapisz**.

    ![Zrzut ekranu przedstawiający rolę właściciela, zaznaczone](./media/billing-add-change-azure-subscription-administrator/add-role.png)

Daje to pełny dostęp użytkownika do wszystkich zasobów łącznie z prawej strony można delegować dostępu do innych osób. Aby udzielić dostępu w zakresie różnych, takich jak grupy zasobów, odwiedź stronę **kontrola dostępu (IAM)** blok dla tego zakresu.

## <a name="add-or-change-co-administrator"></a>Dodawanie lub zmienianie administratora współpracującego

Jako współadministratora można dodać tylko użytkownika z rolą [Właściciel](../role-based-access-control/built-in-roles.md#owner). Innych użytkowników, z rolami takimi jak [Współautor](../role-based-access-control/built-in-roles.md#contributor) i [Czytelnik](../role-based-access-control/built-in-roles.md#reader), nie można dodać jako współadministratorów.

> [!TIP]
> Wystarczy dodać właściciela jako współadministratora, jeśli użytkownik chce zarządzać wdrożeń klasycznych na platformie Azure. Firma Microsoft zaleca, korzystając z modelu RBAC do innych celów.

1. Jeśli jeszcze nie, należy dodać kogoś jako właściciel, postępując zgodnie z instrukcjami z góry.
2. **Kliknij prawym przyciskiem myszy** użytkownika właściciela użytkownik właśnie został dodany, a następnie wybierz **Dodaj jako współadministratora**. Jeśli nie widzisz **Dodaj jako współadministratora** opcji, Odśwież stronę lub spróbuj innej przeglądarki internetowej. 

    ![Zrzut ekranu, który dodaje współadministratora](./media/billing-add-change-azure-subscription-administrator/add-coadmin.png)

    Aby usunąć uprawnienie administratora współpracującego **kliknij prawym przyciskiem myszy** użytkownika administratora współpracującego, a następnie wybierz pozycję **Usuń współadministratora**.

    ![Zrzut ekranu, który usuwa współadministratora](./media/billing-add-change-azure-subscription-administrator/remove-coadmin.png)

### <a name="adding-a-guest-user-as-a-co-administrator"></a>Dodawanie użytkownika-gościa jako współadministratora

[Użytkownicy-goście](../active-directory/b2b/b2b-quickstart-add-guest-users-portal.md) przypisane administratora współpracującego roli napotkać pewne różnice w porównaniu do użytkowników z roli współadministratora. Rozważmy następujący scenariusz:

- Użytkownik A, przy użyciu usługi Azure AD pracy lub nauki jest administrator usługi dla subskrypcji platformy Azure.
- Użytkownik B ma konto Microsoft.
- Użytkownik A przypisuje rolę administratora współpracującego użytkownikowi B.
- Użytkownik B prawie wszystko, co zrobić, ale nie może rejestrować aplikacje lub wyszukać użytkowników w katalogu usługi Azure AD.

Można oczekiwać, że użytkownik B może zarządzać wszystkim. Przyczyna różnica ta jest, że konto Microsoft jest dodawane do subskrypcji, co użytkownik-Gość zamiast użytkownika elementu członkowskiego. Użytkownicy-goście mają różne domyślne uprawnienia w usłudze Azure AD w porównaniu do użytkowników. Na przykład użytkowników może odczytywać innych użytkowników w usłudze Azure AD i nie użytkowników-gości. Użytkowników można zarejestrować nowych jednostek usługi w usłudze Azure AD i nie użytkowników-gości. Jeśli użytkownik-Gość musi mieć możliwość wykonywania tych zadań, możliwe rozwiązanie jest przypisanie konkretne role administratora usługi Azure AD użytkownik Gość musi. Na przykład w poprzednim scenariuszu, należy przypisać [odczytywanie katalogów](../active-directory/users-groups-roles/directory-assign-admin-roles.md#directory-readers) roli do odczytywania innych użytkowników i przypisać [Deweloper aplikacji](../active-directory/users-groups-roles/directory-assign-admin-roles.md#application-developer) roli, aby można było utworzyć jednostki usługi. Aby uzyskać więcej informacji dotyczących elementu członkowskiego i użytkowników-gości i ich uprawnienia, zobacz [jakie są domyślne uprawnienia użytkowników usługi Azure Active Directory?](../active-directory/fundamentals/users-default-permissions.md). 

Należy pamiętać, że [wbudowane role zasobów platformy Azure](../role-based-access-control/built-in-roles.md) różnią się od [role administratora usługi Azure AD](../active-directory/users-groups-roles/directory-assign-admin-roles.md). Wbudowane role nie udzielić dostępu do usługi Azure AD. Aby uzyskać więcej informacji, zobacz [zrozumieć różne role](../role-based-access-control/rbac-and-directory-admin-roles.md).

<a name="change-service-administrator-for-a-subscription"></a>

## <a name="change-the-service-administrator-for-an-azure-subscription"></a>Zmiana administratora usługi dla subskrypcji platformy Azure

Tylko administrator konta może zmienić administratora usługi dla subskrypcji. Domyślnie podczas logowania, administrator usługi jest taka sama jak administratora konta. Jeśli administrator usługi zostało zmienione na innego użytkownika, następnie administrator konta utraci dostęp do witryny Azure portal. Jednak administrator konta zawsze można użyć Centrum kont zmienić administratora usługi do siebie.

1. Pamiętaj, że dany scenariusz jest obsługiwany przez sprawdzanie [limity dotyczące zmieniania administratorów usługi](#limits).
1. Zaloguj się do [Centrum kont](https://account.windowsazure.com/subscriptions) jako administratora konta.
1. Wybierz subskrypcję.
1. Po prawej stronie wybierz **Edytuj szczegóły subskrypcji**.

    ![Zrzut ekranu przedstawiający przycisk Edytuj subskrypcji w Centrum konta](./media/billing-add-change-azure-subscription-administrator/editsub.png)
1. W **administratora usługi** wprowadź adres e-mail nowego administratora usługi.

    ![Zrzut ekranu przedstawiający okno Zmień adres e-mail administratora usługi](./media/billing-add-change-azure-subscription-administrator/changeSA.png)

<a name="limits"></a>

### <a name="limitations-for-changing-service-administrators"></a>Ograniczenia dotyczące zmieniania administratorów usługi

* Każda subskrypcja jest skojarzona z katalogiem usługi Azure AD. Aby znaleźć katalogu subskrypcji jest skojarzony z, przejdź do [ **subskrypcje**](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade), następnie wybierz subskrypcję, aby wyświetlić katalog.
* Jeśli zalogujesz się za pomocą konta firmowego lub szkolnego, można dodać inne konta w organizacji jako administrator usługi. Na przykład abby@contoso.com można dodać bob@contoso.com jako administrator usługi, ale nie można dodać john@notcontoso.com chyba że john@notcontoso.com ma obecności w katalogu contoso.com. Zalogowany za pomocą kont służbowych użytkowników mogą w dalszym ciągu dodawać użytkowników Account Microsoft jako administratora usługi.

  | Metoda logowania | Dodaj użytkownika Account Microsoft jako administratora usługi? | W tej samej organizacji, administrator usługi, należy dodać konta firmowego lub szkolnego? | Dodaj konto służbowe w innej organizacji jako administrator usługi? |
  | --- | --- | --- | --- |
  |  Konto Microsoft |Yes |Nie |Nie |
  |  Konto służbowe |Yes |Yes |Nie |

## <a name="change-the-account-administrator-for-an-azure-subscription"></a>Zmienić administratora konta dla subskrypcji platformy Azure

Administrator konta jest początkowo zarejestrowali się do subskrypcji platformy Azure, która odpowiada właściciel rozliczeń subskrypcji użytkownika. Aby zmienić administratora konta, subskrypcji, zobacz [przenieść własność subskrypcji platformy Azure do innego konta](billing-subscription-transfer.md).

<a name="check-the-account-administrator-of-the-subscription"></a>

**Nie masz pewności, który jest administratorem konta?** Wykonaj następujące kroki:

1. Odwiedź stronę [ **subskrypcje** w witrynie Azure portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
1. Wybierz subskrypcję, aby sprawdzić, a następnie sprawdź w obszarze **ustawienia**.
1. Wybierz **właściwości**. Administrator konta subskrypcji są wyświetlane w **administrator konta** pole.  

## <a name="learn-more-about-resource-access-control-and-active-directory"></a>Dowiedz się więcej na temat kontroli dostępu do zasobów i usługi Active Directory

* Aby dowiedzieć się więcej o ROLACH, zobacz [co to jest kontrola dostępu oparta na rolach (RBAC)?](../role-based-access-control/overview.md)
* Aby dowiedzieć się więcej na temat wszystkich ról na platformie Azure, zobacz [zrozumienie różnych ról na platformie Azure](../role-based-access-control/rbac-and-directory-admin-roles.md).
* Aby uzyskać więcej informacji na temat usługi Azure Active Directory, zobacz [jak subskrypcje platformy Azure są skojarzone z usługą Azure Active Directory](../active-directory/active-directory-how-subscriptions-associated-directory.md) i [przypisywanie ról administratorów w usłudze Azure Active Directory](../active-directory/users-groups-roles/directory-assign-admin-roles.md).

## <a name="need-help-contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami.

Jeśli masz pytania lub potrzebujesz pomocy, [Utwórz żądanie obsługi](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).