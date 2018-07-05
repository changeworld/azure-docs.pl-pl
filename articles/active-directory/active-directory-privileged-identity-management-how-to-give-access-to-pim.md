---
title: Udzielanie dostępu do Privileged Identity Management — Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak Dodawanie ról do użytkowników z rozszerzeniem usługi Azure Active Directory Privileged Identity Management, mogli zarządzać usługą PIM.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.component: protection
ms.date: 06/06/2017
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 8d52469e11ac508ee8b353311f6040ac9d6f2e4e
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/03/2018
ms.locfileid: "37447153"
---
# <a name="giving-access-to-manage-azure-ad-privileged-identity-management"></a>Przyznawanie dostępu do zarządzania usługi Azure AD Privileged Identity Management
Administrator globalny, który umożliwia automatyczne usługi Azure AD Privileged Identity Management (PIM) dla organizacji, skorzystaj z przypisań ról i dostępu do usługi PIM. Nikt inny nie pobiera zapisu domyślnie, jednak tym innych administratorów globalnych. Inne Administratorzy globalni, Administratorzy zabezpieczeń i czytelnicy zabezpieczeń mają dostęp tylko do odczytu do usługi Azure AD PIM. Aby udzielić dostępu do usługi PIM, pierwszego użytkownika można przypisać inne osoby **administratorem ról uprzywilejowanych** roli.

> [!NOTE]
> Zarządzanie usługą Azure AD PIM wymaga usługi Azure MFA. Ponieważ kont Microsoft nie można zarejestrować usługi Azure MFA, użytkownik zaloguje się za pomocą konta Microsoft nie może uzyskać dostępu usługi Azure AD PIM.
> 
> 

Upewnij się, zawsze co najmniej dwóch użytkowników rolę administrator ról uprzywilejowanych w przypadku, gdy jeden użytkownik jest zablokowany lub jego konto zostało usunięte.

## <a name="give-another-user-access-to-manage-pim"></a>Udostępnij innego użytkownika do zarządzania usługi PIM
1. Zaloguj się do [witryny Azure portal](https://portal.azure.com/) i wybierz **usługi Azure AD Privileged Identity Management** aplikacji na pulpicie nawigacyjnym.
2. Wybierz **Zarządzanie rolami uprzywilejowanymi** > **administratorem ról uprzywilejowanych** > **Dodaj**.
   
    ![Dodaj administratorów ról uprzywilejowanych — zrzut ekranu][1]
3. W bloku Dodawanie zarządzanych użytkowników krok 1 została już wykonana. Wybierz krok 2, **wybranym użytkownikom** i Wyszukaj użytkownika, które chcesz dodać.
   
    ![Wybierz użytkowników — zrzut ekranu][2]
4. Wybierz użytkownika z poziomu wyników wyszukiwania, a następnie kliknij przycisk **gotowe**.
5. Kliknij przycisk **OK** Aby zapisać wybrane opcje. Użytkownik, który wybrano pojawi się na liście Administratorzy ról uprzywilejowanych.
   
   * Zawsze, gdy nowa rola zostanie przydzielone innej, ich są automatycznie konfigurowane jako uprawnionych aktywacji roli. Jeśli chcesz były one stałe w roli, kliknij użytkownika na liście. Wybierz **Ustaw jako trwałą** w menu informacji użytkownika.
6. Wyślij łącze do użytkownika [wprowadzenie do usługi Azure AD Privileged Identity Management](active-directory-privileged-identity-management-getting-started.md).

## <a name="remove-another-users-access-rights-for-managing-pim"></a>Usuń prawa dostępu przez innego użytkownika do zarządzania usługi PIM
Zanim usuniesz ktoś z administratorem ról uprzywilejowanych, zawsze upewnij się, nadal będą dwóch użytkowników przypisanych do niego.

1. Na pulpicie nawigacyjnym usługi PIM kliknij rolę **administratorem ról uprzywilejowanych**.  Zostanie wyświetlona lista użytkowników, obecnie w tej roli.
2. Kliknij użytkownika na liście użytkowników.
3. Kliknij pozycję **Usuń**.  Otrzymasz komunikat z potwierdzeniem.
4. Kliknij przycisk **tak** można usunąć użytkownika z roli.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Kolejne kroki
[!INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!--Image references-->

[1]: ./media/active-directory-privileged-identity-management-how-to-give-access-to-pim/PIM_add_PRA.png
[2]: ./media/active-directory-privileged-identity-management-how-to-give-access-to-pim/PIM_select_users.png
