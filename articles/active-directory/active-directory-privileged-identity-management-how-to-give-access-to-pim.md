---
title: Jak zapewnić dostęp do Privileged Identity Management - Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak dodać role użytkowników na podstawie rozszerzenia usługi Azure Active Directory Privileged Identity Management, którymi zarządzają usługi PIM.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.component: users-groups-roles
ms.date: 06/06/2017
ms.author: curtand
ms.custom: pim
ms.openlocfilehash: f7e08e35ce4575715a72b0880d038ce0db766b66
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
---
# <a name="giving-access-to-manage-azure-ad-privileged-identity-management"></a>Przyznawanie dostępu do zarządzania usługi Azure AD Privileged Identity Management
Administrator globalny, który umożliwia usłudze Azure AD Privileged Identity Management (PIM) dla organizacji, automatycznie pobrać przypisania ról i dostępu do usługi PIM. Nikt pobiera zapisu domyślnie, łącznie z innych administratorów globalnych. Innych administratorów globalnych, administratorów zabezpieczeń i czytników zabezpieczeń mają dostęp tylko do odczytu do usługi Azure AD PIM. Aby udzielić dostępu do usługi PIM, pierwszego użytkownika można przypisać użytkownikom **administrator ról uprzywilejowanych** roli. Ten przydział musi być wykonywane za pomocą programu PIM się i nie można zmienić za pomocą programu PowerShell lub innych portalach.

> [!NOTE]
> Zarządzanie programem Azure AD PIM wymaga usługi Azure MFA. Ponieważ konta Microsoft nie można zarejestrować dla usługi Azure MFA, użytkownik, który zaloguje się za pomocą konta Microsoft nie może uzyskać dostępu usługi Azure AD PIM.
> 
> 

Upewnij się, zawsze istnieją co najmniej dwóch użytkowników należących do roli administrator ról uprzywilejowanych w przypadku, gdy jeden użytkownik jest zablokowany lub ich konto zostało usunięte.

## <a name="give-another-user-access-to-manage-pim"></a>Udostępnij innym użytkownika do zarządzania PIM
1. Zaloguj się do [portalu Azure](https://portal.azure.com/) i wybierz **Azure AD Privileged Identity Management** aplikacji na pulpicie nawigacyjnym.
2. Wybierz **Zarządzanie ról uprzywilejowanych** > **administrator ról uprzywilejowanych** > **Dodaj**.
   
    ![Dodawanie ról uprzywilejowanych administratorów — zrzut ekranu][1]
3. W bloku użytkowników zarządzanych Dodaj krok 1 została już ukończona. Zaznacz krok 2, **Wybierz użytkowników** i Wyszukaj użytkownika, które chcesz dodać.
   
    ![Wybierz użytkowników — zrzut ekranu][2]
4. Wybierz użytkownika z wyników wyszukiwania, a następnie kliknij przycisk **gotowe**.
5. Kliknij przycisk **OK** Aby zapisać wybrane opcje. Użytkownik, który wybrano będą wyświetlane na liście ról uprzywilejowanych administratorów.
   
   * Zawsze, gdy przypisaniu roli dla kogoś, kto ich są automatycznie konfigurowane jako kwalifikujących się do aktywowania roli. Jeśli chcesz uczynić je stałym w tej roli, kliknij użytkownika na liście. Wybierz **upewnij uprawnienie** w menu informacji użytkownika.
6. Wyślij łącze do użytkownika [wprowadzenie do korzystania z usługi Azure AD Privileged Identity Management](active-directory-privileged-identity-management-getting-started.md).

## <a name="remove-another-users-access-rights-for-managing-pim"></a>Usuwanie praw dostępu przez innego użytkownika do zarządzania usługi PIM
Zawsze przed usunięciem ktoś z roli administrator ról uprzywilejowanych, upewnij się, nadal będą dwóch użytkowników przypisanych do niej.

1. Na pulpicie nawigacyjnym usługi PIM kliknij rolę **administrator ról uprzywilejowanych**.  Zostanie wyświetlona lista użytkowników obecnie w tej roli.
2. Kliknij na użytkownika na liście użytkowników.
3. Polecenie **Usuń**.  Otrzymasz komunikat potwierdzenia.
4. Kliknij przycisk **tak** Aby usunąć użytkownika z roli.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Kolejne kroki
[!INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!--Image references-->

[1]: ./media/active-directory-privileged-identity-management-how-to-give-access-to-pim/PIM_add_PRA.png
[2]: ./media/active-directory-privileged-identity-management-how-to-give-access-to-pim/PIM_select_users.png
