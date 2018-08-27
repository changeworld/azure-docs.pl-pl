---
title: Dodawanie lub zmienianie ról subskrypcji platformy Azure administratora | Dokumentacja firmy Microsoft
description: Opisuje, jak dodać lub zmienić Azure Współadministratora, administratora usługi i Administrator konta
services: ''
documentationcenter: ''
author: genlin
manager: jlian
editor: ''
tags: billing
ms.assetid: 13a72d76-e043-4212-bcac-a35f4a27ee26
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/14/2018
ms.author: genli
ms.openlocfilehash: b0e24e498acd823242b3613abb62df978466d56d
ms.sourcegitcommit: ebb460ed4f1331feb56052ea84509c2d5e9bd65c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/24/2018
ms.locfileid: "42918316"
---
# <a name="add-or-change-azure-subscription-administrators"></a>Dodawanie lub zmienianie Administratorzy subskrypcji platformy Azure

Zarządzanie dostępem do zasobów platformy Azure wymaga odpowiedniej roli administratora. W tym artykule opisano, jak dodać lub zmienić roli administratora dla użytkownika na poziomie subskrypcji.

> [!div class="nextstepaction"]
> [Pomóż nam w usprawnianiu dokumentacja rozliczeń platformy Azure](https://go.microsoft.com/fwlink/p/?linkid=2010091)

## <a name="what-administrator-role-do-i-use"></a>Jaką rolę administratora należy używać?

Platforma Azure oferuje kilka różnych ról. Aby zarządzać dostępem do zasobów, można użyć klasyczni ról administratora, takie jak administrator usługi i administratora współpracującego lub nowszych systemach autoryzacji o nazwie kontroli dostępu opartej na rolach (RBAC). Aby zapewnić lepszą kontrolę i uprościć zarządzanie dostępem, zaleca się używać funkcji RBAC dla wszystkich potrzeb związanych z zarządzaniem dostępu. Jeśli to możliwe firma Microsoft zaleca, skonfiguruj ponownie istniejących zasad dostępu przy użyciu funkcji RBAC. Aby uzyskać więcej informacji, zobacz [co to jest kontrola dostępu oparta na rolach (RBAC)](../role-based-access-control/overview.md) i [zrozumienie różnych ról na platformie Azure](../role-based-access-control/rbac-and-directory-admin-roles.md).

<a name="add-an-admin-for-a-subscription"></a>

## <a name="add-an-rbac-owner-for-a-subscription-in-azure-portal"></a>Dodaj właściciela RBAC do subskrypcji w witrynie Azure portal 

Aby dodać kogoś jako administratora subskrypcji platformy Azure, przypisz do niego rolę [Właściciel](../role-based-access-control/built-in-roles.md#owner) (rolę RBAC) w zakresie subskrypcji. Rola Właściciel umożliwia zarządzanie zasobami w ramach przypisanej subskrypcji bez praw dostępu do innych subskrypcji.

1. Odwiedź stronę [ **subskrypcje** w witrynie Azure portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
2. Wybierz subskrypcję, dla której ma zostać nadany dostęp.
3. Wybierz pozycję **Dodaj**.
   (Jeśli brakuje przycisku Dodaj, to nie masz uprawnienia do nadawania uprawnień).
4. Wybierz pozycję **Kontrola dostępu (IAM)** na liście.
5. W polu **Rola** wybierz opcję **Właściciel**. 
6. W polu **Przypisz dostęp do** wybierz pozycję **Użytkownik, grupa lub aplikacja usługi Azure AD**. 
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

## <a name="need-help-contact-support"></a>Potrzebujesz pomocy? Skontaktuj się z pomocą techniczną.

Jeśli nadal potrzebujesz pomocy, [się z pomocą techniczną](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) można szybko rozwiązać swój problem.
