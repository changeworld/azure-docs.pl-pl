---
title: Administratorzy subskrypcji klasycznej platformy Azure | Dokumenty firmy Microsoft
description: W tym artykule opisano sposób dodawania lub zmieniania ról współadministratora platformy Azure i administratora usługi oraz sposobu wyświetlania administratora konta.
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
ms.date: 01/22/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 2493e893f9afda0642bd838c94538dd0b984bce5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79243486"
---
# <a name="azure-classic-subscription-administrators"></a>Klasyczni administratorzy subskrypcji platformy Azure

Firma Microsoft zaleca zarządzanie dostępem do zasobów platformy Azure przy użyciu kontroli dostępu opartej na rolach (RBAC). Jeśli jednak nadal używasz klasycznego modelu wdrażania, musisz użyć klasycznej roli administratora subskrypcji: administratora usługi i współadministratora. Aby uzyskać więcej informacji, zobacz [Azure Resource Manager vs. wdrożenie klasyczne](../azure-resource-manager/management/deployment-models.md).

W tym artykule opisano sposób dodawania lub zmieniania ról współadministratora i administratora usługi oraz sposobu wyświetlania administratora konta.

## <a name="add-a-co-administrator"></a>Dodawanie współadministratora

> [!TIP]
> Wystarczy dodać współadministratora tylko wtedy, gdy użytkownik musi zarządzać klasycznymi wdrożeniami platformy Azure przy użyciu [modułu PowerShell usługi Azure Management](https://docs.microsoft.com/powershell/module/servicemanagement/azure). Jeśli użytkownik używa tylko witryny Azure Portal do zarządzania zasobami klasycznymi, nie trzeba dodawać administratora klasycznego dla użytkownika.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) jako administrator usługi lub współadministrator.

1. Otwórz blok [Subskrypcje](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) i wybierz subskrypcję.

    Współadministratorzy mogą być przypisywani tylko w zakresie subskrypcji.

1. Kliknij pozycję **Kontrola dostępu (IAM)**.

1. Kliknij kartę **Administratorzy klasyczni.**

    ![Zrzut ekranu otwierający administratorzy klasyczni](./media/classic-administrators/classic-administrators.png)

1. Kliknij **pozycję Dodaj** > **współadministratora,** aby otworzyć okienko Dodaj współadministratorów.

    Jeśli opcja Dodaj współadministratora jest wyłączona, nie masz uprawnień.

1. Wybierz użytkownika, którego chcesz dodać, a następnie kliknij przycisk **Dodaj**.

    ![Zrzut ekranu, który dodaje współadministratora](./media/classic-administrators/add-coadmin.png)

## <a name="add-a-guest-user-as-a-co-administrator"></a>Dodawanie użytkownika-gościa jako współadministratora

Aby dodać użytkownika-gościa jako współadministratora, wykonaj te same czynności, co w poprzedniej sekcji [Dodaj współadministratora.](#add-a-co-administrator) Użytkownik-gość musi spełniać następujące kryteria:

- Użytkownik-gość musi być obecny w katalogu. Oznacza to, że użytkownik został zaproszony do katalogu i zaakceptował zaproszenie.

Aby uzyskać więcej informacji na temat dodawania użytkownika-gościa do katalogu, zobacz [Dodawanie użytkowników współpracy usługi Azure Active Directory B2B w witrynie Azure portal](../active-directory/b2b/add-users-administrator.md).

### <a name="differences-for-guest-users"></a>Różnice dla użytkowników-gości

Użytkownicy-goście, którzy zostali przypisani do roli Współadministratora, mogą zobaczyć pewne różnice w porównaniu z użytkownikami członkowskimi z rolą Współadministrator. Poniżej przedstawiono przykładowy scenariusz:

- Użytkownik A z kontem usługi Azure AD (konto służbowe) jest administratorem usługi dla subskrypcji platformy Azure.
- Użytkownik B ma konto Microsoft.
- Użytkownik A przypisuje rolę Współadministratora do użytkownika B.
- Użytkownik B może zrobić prawie wszystko, ale nie może zarejestrować aplikacji lub wyszukać użytkowników w katalogu usługi Azure AD.

Można oczekiwać, że użytkownik B może zarządzać wszystkim. Powodem tej różnicy jest to, że konto Microsoft jest dodawany do subskrypcji jako użytkownik-gość zamiast użytkownika członkowskiego. Użytkownicy-goście mają różne uprawnienia domyślne w usłudze Azure AD w porównaniu do użytkowników elementów członkowskich. Na przykład użytkownicy członkowskie mogą odczytywać innych użytkowników w usłudze Azure AD i użytkownicy-goście nie mogą. Użytkownicy członkowskie mogą rejestrować nowe jednostki usługi w usłudze Azure AD i użytkownicy-goście nie mogą.

Jeśli użytkownik-gość musi mieć możliwość wykonywania tych zadań, możliwym rozwiązaniem jest przypisanie określonych ról administratora usługi Azure AD, które użytkownik-gość potrzebuje. Na przykład w poprzednim scenariuszu można przypisać rolę [Czytniki katalogów](../active-directory/users-groups-roles/directory-assign-admin-roles.md#directory-readers) do odczytu innych użytkowników i przypisać rolę [dewelopera aplikacji,](../active-directory/users-groups-roles/directory-assign-admin-roles.md#application-developer) aby móc tworzyć jednostki usługi. Aby uzyskać więcej informacji o użytkownikach elementów członkowskich i użytkownikach oraz ich uprawnieniach, zobacz [Jakie są domyślne uprawnienia użytkowników w usłudze Azure Active Directory?](../active-directory/fundamentals/users-default-permissions.md). Aby uzyskać więcej informacji na temat udzielania dostępu użytkownikom-gościom, zobacz [Zarządzanie dostępem do zasobów platformy Azure dla zewnętrznych użytkowników-gości przy użyciu funkcji RBAC](role-assignments-external-users.md).

Należy zauważyć, że [wbudowane role zasobów platformy Azure](../role-based-access-control/built-in-roles.md) różnią się od ról [administratora usługi Azure AD.](../active-directory/users-groups-roles/directory-assign-admin-roles.md) Wbudowane role nie udzielają dostępu do usługi Azure AD. Aby uzyskać więcej informacji, zobacz [Opis różnych ról](../role-based-access-control/rbac-and-directory-admin-roles.md).

Aby uzyskać informacje porównujące użytkowników elementów członkowskich i użytkowników-gości, zobacz [Jakie są domyślne uprawnienia użytkowników w usłudze Azure Active Directory?](../active-directory/fundamentals/users-default-permissions.md).

## <a name="remove-a-co-administrator"></a>Usuwanie współadministratora

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) jako administrator usługi lub współadministrator.

1. Otwórz blok [Subskrypcje](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) i wybierz subskrypcję.

1. Kliknij pozycję **Kontrola dostępu (IAM)**.

1. Kliknij kartę **Administratorzy klasyczni.**

1. Dodaj znacznik wyboru obok współadministratora, którego chcesz usunąć.

1. Kliknij pozycję **Usuń**.

1. W wyświetlonym oknie komunikatu kliknij przycisk **Tak**.

    ![Zrzut ekranu, który usuwa współadministratora](./media/classic-administrators/remove-coadmin.png)

## <a name="change-the-service-administrator"></a>Zmienianie administratora usługi

Tylko administrator konta może zmienić administratora usługi dla subskrypcji. Domyślnie podczas rejestracji w ramach subskrypcji platformy Azure administrator usługi jest taki sam jak administrator konta. Użytkownik z rolą Administratora konta nie ma dostępu do witryny Azure portal. Użytkownik z rolą administratora usługi ma pełny dostęp do witryny Azure portal. Jeśli administrator konta i administrator usługi są tym samym użytkownikiem i zmienisz administratora usługi na innego użytkownika, administrator konta utraci dostęp do witryny Azure portal. Administrator konta zawsze może jednak użyć Centrum kont, aby zmienić administratora usługi z powrotem na siebie.

Wykonaj następujące kroki, aby zmienić administratora usługi w **Centrum kont**.

### <a name="account-center"></a>Centrum konta

1. Upewnij się, że scenariusz jest obsługiwany, sprawdzając [ograniczenia dotyczące zmiany administratora usługi](#limitations-for-changing-the-service-administrator).

1. Zaloguj się do [Centrum kont](https://account.windowsazure.com/subscriptions) jako administrator konta.

1. Kliknij subskrypcję.

1. Po prawej stronie kliknij pozycję **Edytuj szczegóły subskrypcji**.

    ![Zrzut ekranu przedstawiający przycisk Edytuj subskrypcję w Centrum kont](./media/classic-administrators/editsub.png)

1. W polu **ADMINISTRATOR USŁUGI** wprowadź adres e-mail nowego administratora usługi.

    ![Zrzut ekranu przedstawiający pole zmiany adresu e-mail administratora usługi](./media/classic-administrators/change-service-admin.png)

1. Kliknij znacznik wyboru, aby zapisać zmianę.

### <a name="limitations-for-changing-the-service-administrator"></a>Ograniczenia dotyczące zmiany administratora usługi

Na subskrypcję platformy Azure może być tylko jeden administrator usługi. Zmiana administratora usługi będzie zachowywać się inaczej w zależności od tego, czy administrator konta jest kontem Microsoft, czy jest kontem usługi Azure AD (kontem służbowym).

| Konto administratora konta | Czy można zmienić administratora usługi na inne konto Microsoft? | Czy można zmienić administratora usługi na konto usługi Azure AD w tym samym katalogu? | Czy można zmienić administratora usługi na konto usługi Azure AD w innym katalogu? |
| --- | --- | --- | --- |
| Konto Microsoft | Tak | Nie | Nie |
| Konto Azure AD | Tak | Tak | Nie |

Jeśli administrator konta jest kontem usługi Azure AD, można zmienić administratora usługi na konto usługi Azure AD w tym samym katalogu, ale nie w innym katalogu. Na przykład abby@contoso.com można zmienić bob@contoso.comadministratora usługi na , john@notcontoso.com john@notcontoso.com ale nie można zmienić administratora usługi, chyba że ma obecność w katalogu contoso.com.

Aby uzyskać więcej informacji na temat kont Microsoft i kont usługi Azure AD, zobacz [Co to jest usługa Azure Active Directory?](../active-directory/fundamentals/active-directory-whatis.md).

## <a name="view-the-account-administrator"></a>Wyświetlanie administratora konta

Administrator konta jest użytkownikiem, który początkowo zarejestrował się w ramach subskrypcji platformy Azure i jest odpowiedzialny jako właściciel rozliczeń subskrypcji. Aby zmienić administratora konta subskrypcji, zobacz [Przeniesienie własności subskrypcji platformy Azure na inne konto](../cost-management-billing/manage/billing-subscription-transfer.md).

Wykonaj następujące kroki, aby wyświetlić administratora konta.

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).

1. Otwórz blok [Subskrypcje](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) i wybierz subskrypcję.

1. Kliknij pozycję **Właściwości**.

    Administrator konta subskrypcji jest wyświetlany w polu **Administrator konta.**

    ![Zrzut ekranu przedstawiający administratora konta](./media/classic-administrators/account-admin.png)

## <a name="next-steps"></a>Następne kroki

* [Understand the different roles in Azure (Omówienie ról na platformie Azure)](../role-based-access-control/rbac-and-directory-admin-roles.md)
* [Zarządzanie dostępem do zasobów platformy Azure za pomocą kontroli dostępu opartej na rolach i witryny Azure Portal](../role-based-access-control/role-assignments-portal.md)
* [Dodawanie lub zmienianie administratorów subskrypcji platformy Azure](../cost-management-billing/manage/add-change-subscription-administrator.md)
