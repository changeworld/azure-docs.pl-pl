---
title: Administratorzy subskrypcji platformy Azure classic | Dokumentacja firmy Microsoft
description: W tym artykule opisano, jak dodać lub zmienić role administratora współpracującego platformy Azure i Administrator usługi i sposób wyświetlania administratora konta.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: ''
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/19/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: a19153677e691d2681c131df67100a43ef9532a0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60709532"
---
# <a name="azure-classic-subscription-administrators"></a>Administratorzy subskrypcji klasycznej platformy Azure

Firma Microsoft zaleca zarządzanie dostępem do zasobów platformy Azure za pomocą kontroli dostępu opartej na rolach (RBAC). Jednak jeśli nadal używasz klasycznego modelu wdrażania, należy użyć roli administrator klasyczni: Administrator usługi i administratora współpracującego. Aby uzyskać więcej informacji, zobacz [usługi Azure Resource Manager, a wdrożeniem klasycznym](../azure-resource-manager/resource-manager-deployment-model.md).

W tym artykule opisano, jak dodać lub zmienić role administratora współpracującego i Administrator usługi i sposób wyświetlania administratora konta.

## <a name="add-a-co-administrator"></a>Dodaj jako Współadministratora

> [!TIP]
> Musisz dodać Współadministratora, jeśli użytkownik chce zarządzać wdrożeń klasycznych na platformie Azure przy użyciu [moduł programu PowerShell Azure usługi](https://docs.microsoft.com/en-us/powershell/module/servicemanagement/azure). Jeśli użytkownik używa tylko witryny Azure portal do zarządzania zasobami klasycznymi, nie musisz dodać klasycznego administratora dla użytkownika.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) jako Administrator usługi.

1. Otwórz [subskrypcje](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) i wybierz subskrypcję.

    Współadministratorzy można przypisać tylko w zakresie subskrypcji.

1. Kliknij pozycję **Kontrola dostępu (IAM)**.

1. Kliknij przycisk **Administratorzy Classic** kartę.

    ![Zrzut ekranu, który otwiera Administratorzy klasyczny](./media/classic-administrators/classic-administrators.png)

1. Kliknij przycisk **Dodaj** > **dodawania administratora współpracującego** aby otworzyć okienko Dodawanie administratorów współpracujących.

    Wyłączenie opcji kontem administratora współpracującego Dodaj nie masz uprawnień.

1. Wybierz użytkownika, który chcesz dodać, a następnie kliknij przycisk **Dodaj**.

    ![Zrzut ekranu, który dodaje współadministratora](./media/classic-administrators/add-coadmin.png)

### <a name="adding-a-guest-user-as-a-co-administrator"></a>Dodawanie użytkownika-gościa jako Współadministratora

[Użytkownicy-goście](../active-directory/b2b/b2b-quickstart-add-guest-users-portal.md) przypisane administratora współpracującego roli napotkać pewne różnice w porównaniu do użytkowników z roli Współadministratora. Rozważmy następujący scenariusz:

- Użytkownik A, przy użyciu usługi Azure AD pracy lub nauki jest Administrator usługi dla subskrypcji platformy Azure.
- Użytkownik B ma konto Microsoft.
- Użytkownik A przypisuje rolę administratora współpracującego użytkownikowi B.
- Użytkownik B prawie wszystko, co zrobić, ale nie może rejestrować aplikacje lub wyszukać użytkowników w katalogu usługi Azure AD.

Można oczekiwać, że użytkownik B może zarządzać wszystkim. Przyczyna różnica ta jest, że konto Microsoft jest dodawane do subskrypcji, co użytkownik-Gość zamiast użytkownika elementu członkowskiego. Użytkownicy-goście mają różne domyślne uprawnienia w usłudze Azure AD w porównaniu do użytkowników. Na przykład użytkowników może odczytywać innych użytkowników w usłudze Azure AD i nie użytkowników-gości. Użytkowników można zarejestrować nowych jednostek usługi w usłudze Azure AD i nie użytkowników-gości. Jeśli użytkownik-Gość musi mieć możliwość wykonywania tych zadań, możliwe rozwiązanie jest przypisanie konkretne role administratora usługi Azure AD użytkownik Gość musi. Na przykład w poprzednim scenariuszu, należy przypisać [odczytywanie katalogów](../active-directory/users-groups-roles/directory-assign-admin-roles.md#directory-readers) roli do odczytywania innych użytkowników i przypisać [Deweloper aplikacji](../active-directory/users-groups-roles/directory-assign-admin-roles.md#application-developer) roli, aby można było utworzyć jednostki usługi. Aby uzyskać więcej informacji dotyczących elementu członkowskiego i użytkowników-gości i ich uprawnienia, zobacz [jakie są domyślne uprawnienia użytkowników usługi Azure Active Directory?](../active-directory/fundamentals/users-default-permissions.md).

Należy pamiętać, że [wbudowane role zasobów platformy Azure](../role-based-access-control/built-in-roles.md) różnią się od [role administratora usługi Azure AD](../active-directory/users-groups-roles/directory-assign-admin-roles.md). Wbudowane role nie udzielić dostępu do usługi Azure AD. Aby uzyskać więcej informacji, zobacz [zrozumieć różne role](../role-based-access-control/rbac-and-directory-admin-roles.md).

## <a name="remove-a-co-administrator"></a>Usuń Współadministratora

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) jako Administrator usługi.

1. Otwórz [subskrypcje](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) i wybierz subskrypcję.

1. Kliknij pozycję **Kontrola dostępu (IAM)**.

1. Kliknij przycisk **Administratorzy Classic** kartę.

1. Dodaj znacznik wyboru obok Administrator współpracujący ma zostać usunięty.

1. Kliknij pozycję **Usuń**.

1. W wyświetlonym oknie komunikatu kliknij **tak**.

    ![Zrzut ekranu, który usuwa współadministratora](./media/classic-administrators/remove-coadmin.png)

## <a name="change-the-service-administrator"></a>Zmienianie administratora usługi

Tylko Administrator konta może zmienić administratora usługi dla subskrypcji. Domyślnie po zarejestrowaniu się dla subskrypcji platformy Azure, Administrator usługi jest taka sama jako administratora konta. Użytkownik z rolą administratora konta ma Brak dostępu do witryny Azure portal. Użytkownik o roli administratora usługi ma pełny dostęp do witryny Azure portal. Jeśli Administrator konta i Administrator usługi są tego samego użytkownika, a zmiany administratora usługi do innego użytkownika, administratora konta utraci dostęp do portalu Azure. Jednak Administrator konta zawsze można użyć Centrum kont zmienić administratora usługi do siebie.

Istnieją dwa sposoby zmiany administratora usługi. Można zmienić w **witryny Azure portal** lub **Centrum kont**.

### <a name="azure-portal"></a>Azure Portal

1. Upewnij się, że dany scenariusz jest obsługiwany przez sprawdzanie ograniczenia dotyczące zmieniania administratorów usługi.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) stanowisku administratora konta.

1. Otwórz [subskrypcje](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) i wybierz subskrypcję.

1. Kliknij pozycję **Właściwości**.

    ![Zrzut ekranu przedstawiający administratora konta](./media/classic-administrators/account-admin.png)

1. U góry strony, kliknij przycisk **administratora usługi** aby otworzyć okienko administratora usługi.

    Jeśli przycisk administratora usługi jest wyłączona, nie masz uprawnień. Tylko użytkownik, który jest Administrator konta może zmienić administratora usługi.

1. Wybierz nowego administratora usługi, a następnie kliknij przycisk **Zapisz**.

### <a name="account-center"></a>Centrum konta

1. Upewnij się, że dany scenariusz jest obsługiwany przez sprawdzanie ograniczenia dotyczące zmieniania administratorów usługi.

1. Zaloguj się do [Centrum kont](https://account.windowsazure.com/subscriptions) stanowisku administratora konta.

1. Kliknij subskrypcję.

1. Po prawej stronie kliknij pozycję **Edytuj szczegóły subskrypcji**.

    ![Zrzut ekranu przedstawiający przycisk Edytuj subskrypcji w Centrum konta](./media/classic-administrators/editsub.png)

1. W **administratora usługi** wprowadź adres e-mail nowego administratora usługi.

    ![Zrzut ekranu przedstawiający okno Zmień adres e-mail administratora usługi](./media/classic-administrators/change-service-admin.png)

1. Kliknij znacznik wyboru, aby zapisać zmiany.

### <a name="limitations-for-changing-the-service-administrator"></a>Ograniczenia dotyczące zmiany administratora usługi

Każda subskrypcja jest skojarzona z katalogiem usługi Azure AD. Aby znaleźć katalogu subskrypcji jest skojarzony z, otwórz **subskrypcje** w witrynie Azure portal i wybierz subskrypcję, aby zobaczyć katalogu.

Jeśli zalogujesz się za pomocą konta służbowego lub szkolnego, możesz dodać inne konta w organizacji jako Administrator usługi. Na przykład abby@contoso.com można dodać bob@contoso.com jako Administrator usługi, ale nie można dodać john@notcontoso.com chyba że john@notcontoso.com ma obecności w katalogu contoso.com. Użytkownikom logować się na pracy lub konta służbowe mogą w dalszym ciągu Dodawanie użytkowników kont Microsoft jako administratora usługi.

  | Metoda logowania | Dodawanie użytkownika do konta Microsoft jako administratora usługi? | W tej samej organizacji jako Administrator usługi, należy dodać konto służbowe lub szkolne? | Dodaj konto służbowe lub szkolne w innej organizacji jako Administrator usługi? |
  | --- | --- | --- | --- |
  |  Konto Microsoft |Yes |Nie |Nie |
  |  Konto służbowe |Yes |Yes |Nie |

## <a name="view-the-account-administrator"></a>Widok administratora konta

Administrator konta jest początkowo zarejestrowali się do subskrypcji platformy Azure, która odpowiada właściciel rozliczeń subskrypcji użytkownika. Aby zmienić administratora konta, subskrypcji, zobacz [przenieść własność subskrypcji platformy Azure do innego konta](../billing/billing-subscription-transfer.md).

Wykonaj następujące kroki, aby wyświetlić administratora konta.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

1. Otwórz [subskrypcje](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) i wybierz subskrypcję.

1. Kliknij pozycję **Właściwości**.

    Administrator konta subskrypcji są wyświetlane w **administrator konta** pole.

    ![Zrzut ekranu przedstawiający administratora konta](./media/classic-administrators/account-admin.png)

## <a name="next-steps"></a>Kolejne kroki

* [Understand the different roles in Azure (Omówienie ról na platformie Azure)](../role-based-access-control/rbac-and-directory-admin-roles.md)
* [Zarządzanie dostępem do zasobów platformy Azure za pomocą kontroli dostępu opartej na rolach i witryny Azure Portal](../role-based-access-control/role-assignments-portal.md)
* [Dodawanie lub zmienianie administratorów subskrypcji platformy Azure](../billing/billing-add-change-azure-subscription-administrator.md)
