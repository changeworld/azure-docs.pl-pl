---
title: Administratorzy subskrypcji klasycznej platformy Azure | Microsoft Docs
description: Opisuje, jak dodać lub zmienić role administratora współpracującego z platformą Azure oraz administrator usługi oraz jak wyświetlić administratora konta.
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
ms.openlocfilehash: c2096b6c5ddb72c8ab5c5d3203a05c94db51f6c5
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/24/2019
ms.locfileid: "68444333"
---
# <a name="azure-classic-subscription-administrators"></a>Administratorzy klasycznej subskrypcji platformy Azure

Firma Microsoft zaleca, aby zarządzać dostępem do zasobów platformy Azure przy użyciu kontroli dostępu opartej na rolach (RBAC). Jeśli jednak nadal korzystasz z klasycznego modelu wdrażania, musisz użyć klasycznej roli administratora subskrypcji: Administrator usługi i współadministrator. Aby uzyskać więcej informacji, zobacz [Azure Resource Manager a wdrożenie klasyczne](../azure-resource-manager/resource-manager-deployment-model.md).

W tym artykule opisano, jak dodać lub zmienić role administratora współadministratora i usługi oraz jak wyświetlić administratora konta.

## <a name="add-a-co-administrator"></a>Dodawanie współadministratora

> [!TIP]
> Wystarczy dodać współadministratora, jeśli użytkownik musi zarządzać klasycznymi wdrożeniami platformy Azure przy użyciu [modułu Azure Service Management PowerShell](https://docs.microsoft.com/powershell/module/servicemanagement/azure). Jeśli użytkownik używa tylko Azure Portal do zarządzania zasobami klasycznymi, nie trzeba dodawać administratora klasycznego dla użytkownika.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) jako administrator usługi.

1. Otwórz [subskrypcje](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) i wybierz subskrypcję.

    Współadministratory mogą być przypisani tylko w zakresie subskrypcji.

1. Kliknij przycisk **kontrola dostępu (IAM)** .

1. Kliknij kartę **klasyczne Administratorzy** .

    ![Zrzut ekranu, który otwiera administratorów klasycznych](./media/classic-administrators/classic-administrators.png)

1. Kliknij przycisk **Dodaj** > **Dodaj współadministrator** , aby otworzyć okienko Dodaj współadministratorów.

    Jeśli opcja Dodaj administratora współpracującego jest wyłączona, nie masz uprawnień.

1. Wybierz użytkownika, którego chcesz dodać, a następnie kliknij przycisk **Dodaj**.

    ![Zrzut ekranu, który dodaje współadministratora](./media/classic-administrators/add-coadmin.png)

### <a name="adding-a-guest-user-as-a-co-administrator"></a>Dodawanie użytkownika-gościa jako współadministratora

[Użytkownicy-Goście](../active-directory/b2b/b2b-quickstart-add-guest-users-portal.md) , którym przypisano rolę współadministrator, mogą zobaczyć pewne różnice w porównaniu z użytkownikami należącymi do roli współadministratora. Rozważmy następujący scenariusz:

- Użytkownik A przy użyciu konta służbowego usługi Azure AD to administrator usługi dla subskrypcji platformy Azure.
- Użytkownik B ma konto Microsoft.
- Użytkownik A przypisuje rolę współadministratora użytkownikowi B.
- Użytkownik B może wykonać prawie wszystko, ale nie może zarejestrować aplikacji ani wyszukać użytkowników w katalogu usługi Azure AD.

Należy się spodziewać, że użytkownik B może zarządzać wszystkimi elementami. Przyczyna tej różnicy polega na tym, że konto Microsoft jest dodawana do subskrypcji jako użytkownik będący gościem, a nie użytkownikiem będącym członkiem. Użytkownicy-Goście mają różne domyślne uprawnienia w usłudze Azure AD w porównaniu z użytkownikami należącymi do członków. Na przykład użytkownicy z członkami mogą odczytywać innych użytkowników w usłudze Azure AD, a użytkownicy-Goście nie mogą. Użytkownicy będący członkami mogą rejestrować nowe jednostki usługi w usłudze Azure AD, a użytkownicy-Goście nie mogą. Jeśli użytkownik-Gość musi być w stanie wykonać te zadania, możliwe jest przypisanie określonych ról administratora usługi Azure AD, których potrzebuje użytkownik gościa. Na przykład w poprzednim scenariuszu można przypisać rolę [czytelnicy Directory](../active-directory/users-groups-roles/directory-assign-admin-roles.md#directory-readers) do odczytu innych użytkowników i przypisać rolę [Deweloper aplikacji](../active-directory/users-groups-roles/directory-assign-admin-roles.md#application-developer) , aby móc tworzyć jednostki usługi. Aby uzyskać więcej informacji na temat członków i użytkowników-Gości i ich uprawnień, zobacz [co to są domyślne uprawnienia użytkownika w Azure Active Directory?](../active-directory/fundamentals/users-default-permissions.md).

Należy pamiętać, że [wbudowane role dla zasobów platformy Azure](../role-based-access-control/built-in-roles.md) są inne niż [role administratorów usługi Azure AD](../active-directory/users-groups-roles/directory-assign-admin-roles.md). Wbudowane role nie udzielają dostępu do usługi Azure AD. Aby uzyskać więcej informacji, zobacz [Omówienie różnych ról](../role-based-access-control/rbac-and-directory-admin-roles.md).

## <a name="remove-a-co-administrator"></a>Usuwanie współadministratora

1. Zaloguj się do [Azure Portal](https://portal.azure.com) jako administrator usługi.

1. Otwórz [subskrypcje](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) i wybierz subskrypcję.

1. Kliknij przycisk **kontrola dostępu (IAM)** .

1. Kliknij kartę **klasyczne Administratorzy** .

1. Dodaj znacznik wyboru obok administratora współpracującego, który chcesz usunąć.

1. Kliknij pozycję **Usuń**.

1. W wyświetlonym oknie komunikatu kliknij przycisk **tak**.

    ![Zrzut ekranu, który usuwa współadministratora](./media/classic-administrators/remove-coadmin.png)

## <a name="change-the-service-administrator"></a>Zmienianie administratora usługi

Tylko administrator konta może zmienić administratora usługi dla subskrypcji. Domyślnie podczas rejestrowania się w celu uzyskania subskrypcji platformy Azure administrator usługi jest taki sam jak administrator konta. Użytkownik z rolą administratora konta nie ma dostępu do Azure Portal. Użytkownik z rolą administratora usługi ma pełny dostęp do Azure Portal. Jeśli administrator konta i administrator usługi są tego samego użytkownika, a administrator usługi zostanie zmieniony na innego użytkownika, administrator konta utraci dostęp do Azure Portal. Jednak administrator konta zawsze może użyć centrum kont, aby zmienić administratora usługi z powrotem na siebie.

Istnieją dwa sposoby zmiany administratora usługi. Można zmienić w centrum **Azure Portal** lub **konta**.

### <a name="azure-portal"></a>Azure Portal

1. Upewnij się, że scenariusz jest obsługiwany, sprawdzając ograniczenia dotyczące zmiany administratorów usługi.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) jako administrator konta.

1. Otwórz [subskrypcje](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) i wybierz subskrypcję.

1. Kliknij pozycję **Właściwości**.

    ![Zrzut ekranu przedstawiający administratora konta](./media/classic-administrators/account-admin.png)

1. W górnej części kliknij pozycję **administrator usługi** , aby otworzyć okienko administrator usługi.

    Jeśli przycisk administratora usługi jest wyłączony, nie masz uprawnień. Tylko użytkownik będący administratorem konta może zmienić administratora usługi.

1. Wybierz nowego administratora usługi, a następnie kliknij przycisk **Zapisz**.

### <a name="account-center"></a>Centrum konta

1. Upewnij się, że scenariusz jest obsługiwany, sprawdzając ograniczenia dotyczące zmiany administratorów usługi.

1. Zaloguj się do [centrum konta](https://account.windowsazure.com/subscriptions) jako administrator konta.

1. Kliknij subskrypcję.

1. Po prawej stronie kliknij pozycję **Edytuj szczegóły subskrypcji**.

    ![Zrzut ekranu przedstawiający przycisk Edytuj subskrypcję w centrum konta](./media/classic-administrators/editsub.png)

1. W polu **administrator usługi** wprowadź adres E-mail nowego administratora usługi.

    ![Zrzut ekranu przedstawiający pole umożliwiające zmianę adresu e-mail administratora usługi](./media/classic-administrators/change-service-admin.png)

1. Kliknij znacznik wyboru, aby zapisać zmiany.

### <a name="limitations-for-changing-the-service-administrator"></a>Ograniczenia dotyczące zmiany administratora usługi

Każda subskrypcja jest skojarzona z katalogiem usługi Azure AD. Aby znaleźć katalog, z którym jest skojarzona subskrypcja, Otwórz pozycję **subskrypcje** w Azure Portal a następnie wybierz subskrypcję, aby wyświetlić katalog.

Jeśli logujesz się przy użyciu konta służbowego, możesz dodać inne konta w organizacji jako administrator usługi. Na przykład abby@contoso.com program może dodać bob@contoso.com jako administratora usługi, ale nie może go john@notcontoso.com dodać john@notcontoso.com , chyba że ma obecność w katalogu contoso.com. Użytkownicy zalogowani przy użyciu konta służbowego mogą nadal dodawać konto Microsoft użytkowników jako administratora usługi. Może istnieć tylko jeden administrator usługi dla każdej subskrypcji platformy Azure.

  | Metoda logowania | Czy dodać konto Microsoft użytkownika jako administratora usługi? | Czy dodać konto służbowe w tej samej organizacji, w której znajduje się administrator usługi? | Czy dodać konto służbowe w innej organizacji jako administrator usługi? |
  | --- | --- | --- | --- |
  |  Konto Microsoft |Tak |Nie |Nie |
  |  Konto służbowe |Tak |Yes |Nie |

## <a name="view-the-account-administrator"></a>Wyświetlanie administratora konta

Administrator konta jest użytkownikiem, który początkowo zarejestrował się w ramach subskrypcji platformy Azure i jest odpowiedzialny za właściciela rozliczania subskrypcji. Aby zmienić administratora konta subskrypcji, zobacz [przenoszenie własności subskrypcji platformy Azure na inne konto](../billing/billing-subscription-transfer.md).

Wykonaj następujące kroki, aby wyświetlić administratora konta.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

1. Otwórz [subskrypcje](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) i wybierz subskrypcję.

1. Kliknij pozycję **Właściwości**.

    Administrator konta w subskrypcji zostanie wyświetlony w polu **administrator konta** .

    ![Zrzut ekranu przedstawiający administratora konta](./media/classic-administrators/account-admin.png)

## <a name="next-steps"></a>Następne kroki

* [Understand the different roles in Azure (Omówienie ról na platformie Azure)](../role-based-access-control/rbac-and-directory-admin-roles.md)
* [Zarządzanie dostępem do zasobów platformy Azure za pomocą kontroli dostępu opartej na rolach i witryny Azure Portal](../role-based-access-control/role-assignments-portal.md)
* [Dodawanie lub zmienianie administratorów subskrypcji platformy Azure](../billing/billing-add-change-azure-subscription-administrator.md)
