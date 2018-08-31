---
title: Udzielić dostępu do innych administratorów do zarządzania usługą PIM — Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak udzielić dostępu do innych administracji do zarządzania usługi Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.component: pim
ms.date: 06/06/2017
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: d6b2d9f43ce9bb86f4557c92887689c83beb49fa
ms.sourcegitcommit: 63613e4c7edf1b1875a2974a29ab2a8ce5d90e3b
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/29/2018
ms.locfileid: "43189559"
---
# <a name="grant-access-to-other-administrators-to-manage-pim"></a>Udzielanie dostępu do innych administratorów do zarządzania usługi PIM
Administrator globalny, który umożliwia automatyczne usługi Azure AD Privileged Identity Management (PIM) dla organizacji, skorzystaj z przypisań ról i dostępu do usługi PIM. Nikt inny nie pobiera zapisu domyślnie, jednak tym innych administratorów globalnych. Inne Administratorzy globalni, Administratorzy zabezpieczeń i czytelnicy zabezpieczeń mają dostęp tylko do odczytu do usługi Azure AD PIM. Aby udzielić dostępu do usługi PIM, pierwszego użytkownika można przypisać inne osoby **administratorem ról uprzywilejowanych** roli.

> [!NOTE]
> Zarządzanie usługą Azure AD PIM wymaga usługi Azure MFA. Ponieważ kont Microsoft nie można zarejestrować usługi Azure MFA, użytkownik zaloguje się za pomocą konta Microsoft nie może uzyskać dostępu usługi Azure AD PIM.
> 
> 

Upewnij się, zawsze co najmniej dwóch użytkowników rolę administrator ról uprzywilejowanych w przypadku, gdy jeden użytkownik jest zablokowany lub jego konto zostało usunięte.

## <a name="give-another-user-access-to-manage-pim"></a>Udostępnij innego użytkownika do zarządzania usługi PIM
1. Zaloguj się do [witryny Azure portal](https://portal.azure.com/) i wybierz **usługi Azure AD Privileged Identity Management** aplikacji na pulpicie nawigacyjnym.
2. Wybierz **Zarządzanie rolami uprzywilejowanymi** > **administratorem ról uprzywilejowanych** > **Dodaj**.
   
    ![Dodaj administratorów ról uprzywilejowanych — zrzut ekranu](./media/pim-how-to-give-access-to-pim/PIM_add_PRA.png)
3. W bloku Dodawanie zarządzanych użytkowników krok 1 została już wykonana. Wybierz krok 2, **wybranym użytkownikom** i Wyszukaj użytkownika, które chcesz dodać.
   
    ![Wybierz użytkowników — zrzut ekranu](./media/pim-how-to-give-access-to-pim/PIM_select_users.png)
4. Wybierz użytkownika z poziomu wyników wyszukiwania, a następnie kliknij przycisk **gotowe**.
5. Kliknij przycisk **OK** Aby zapisać wybrane opcje. Użytkownik, który wybrano pojawi się na liście Administratorzy ról uprzywilejowanych.
   
   * Zawsze, gdy nowa rola zostanie przydzielone innej, ich są automatycznie konfigurowane jako uprawnionych aktywacji roli. Jeśli chcesz były one stałe w roli, kliknij użytkownika na liście. Wybierz **Ustaw jako trwałą** w menu informacji użytkownika.
6. Wyślij łącze do użytkownika [wprowadzenie do usługi Azure AD Privileged Identity Management](pim-getting-started.md).

## <a name="remove-another-users-access-rights-for-managing-pim"></a>Usuń prawa dostępu przez innego użytkownika do zarządzania usługi PIM
Zanim usuniesz ktoś z administratorem ról uprzywilejowanych, zawsze upewnij się, nadal będą dwóch użytkowników przypisanych do niego.

1. Na pulpicie nawigacyjnym usługi PIM kliknij rolę **administratorem ról uprzywilejowanych**.  Zostanie wyświetlona lista użytkowników, obecnie w tej roli.
2. Kliknij użytkownika na liście użytkowników.
3. Kliknij pozycję **Usuń**.  Otrzymasz komunikat z potwierdzeniem.
4. Kliknij przycisk **tak** można usunąć użytkownika z roli.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Kolejne kroki

- [Włączanie zarządzania subskrypcjami w Twojej dzierżawie](pim-resource-roles-enable-subscription-management.md)