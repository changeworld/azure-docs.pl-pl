---
title: Przypisywanie ról katalogu użytkownikom — usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Instrukcje dotyczące sposobu przypisywania ról administratora i użytkowników niebędących administratorami dla użytkowników w usłudze Azure Active Directory.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 09/06/2018
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2df52969ea79e5d1af132aa82c2ec1ceedb92b82
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75422900"
---
# <a name="assign-administrator-and-non-administrator-roles-to-users-with-azure-active-directory"></a>Przypisywanie ról administratora i użytkowników niebędących administratorami do użytkowników z usługą Azure Active Directory
Jeśli użytkownik w organizacji wymaga uprawnień do zarządzania zasobami usługi Azure Active Directory (Azure AD), należy przypisać użytkownika odpowiednią rolę w usłudze Azure AD oparty na czynności użytkownika wymaga uprawnień do wykonania.

Aby uzyskać więcej informacji na temat dostępnych ról, zobacz [przypisywanie ról administratorów w usłudze Azure Active Directory](../users-groups-roles/directory-assign-admin-roles.md). Aby uzyskać więcej informacji na temat dodawania użytkowników, zobacz [Dodawanie nowych użytkowników do usługi Azure Active Directory](add-users-azure-active-directory.md).

## <a name="assign-roles"></a>Przypisywanie ról
Typowym sposobem przypisać role usługi Azure AD do użytkownika znajduje się na **roli w katalogu** strony dla użytkownika.

Można także przypisać ról za pomocą Privileged Identity Management (PIM). Aby uzyskać szczegółowe informacje o sposobie używania usługi PIM, zobacz [Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management).

### <a name="to-assign-a-role-to-a-user"></a>Aby przypisać rolę użytkownikowi
1. Przejdź do [Azure Portal](https://portal.azure.com/) i zaloguj się przy użyciu konta administratora globalnego dla katalogu. 

2. Wyszukaj i wybierz **Azure Active Directory**.

      ![Azure Portal Wyszukaj Azure Active Directory](media/active-directory-users-assign-role-azure-portal/search-azure-active-directory.png)


3. Wybierz pozycję **Użytkownicy**.

4. Wyszukaj i wybierz użytkownika pobierającego przypisanie roli. Na przykład _Alain Charon_.

      ![Strona wszyscy użytkownicy — Wybieranie użytkownika](media/active-directory-users-assign-role-azure-portal/directory-role-select-user.png)

5. Na stronie **Alain Charon — profil** wybierz pozycję **przypisane role**.

    **Alain Charon - rola katalogu** zostanie wyświetlona strona.

6. Wybierz pozycję **Dodaj przypisanie**, wybierz rolę, która ma zostać przypisana do Alain (na przykład _administrator aplikacji_), a następnie wybierz **pozycję Wybierz**.

    ![Strona przypisane role — wyświetlana została wybrana rola](media/active-directory-users-assign-role-azure-portal/directory-role-select-role.png)

    Rola administrator aplikacji jest przypisywana do Alain Charon i pojawia się w **Alain Charon - rola katalogu** strony.

## <a name="remove-a-role-assignment"></a>Usuwanie przypisania roli
Jeśli musisz usunąć przypisania roli z danych użytkownika, możesz również zrobić z **Alain Charon - rola katalogu** strony.

### <a name="to-remove-a-role-assignment-from-a-user"></a>Usuwanie przypisania roli użytkownika

1. Wybierz **usługi Azure Active Directory**, wybierz opcję **użytkowników**, a następnie wyszukaj i wybierz użytkownika pobierania przypisania roli usunięty. Na przykład _Alain Charon_.

2. Wybierz pozycję **przypisane role**, wybierz pozycję **administrator aplikacji**, a następnie wybierz pozycję **Usuń przypisanie**.

    ![Strona przypisane role, która wyświetla wybraną rolę i opcję Usuń](media/active-directory-users-assign-role-azure-portal/directory-role-remove-role.png)

    Rola administrator aplikacji zostanie usunięty z Alain Charon i nie będzie już wyświetlany na **Alain Charon - rola katalogu** strony.

## <a name="next-steps"></a>Następne kroki
- [Dodawanie lub usuwanie użytkowników](add-users-azure-active-directory.md)

- [Dodać lub zmienić informacje o profilu](active-directory-users-profile-azure-portal.md)

- [Dodawanie użytkowników-gości z innego katalogu](../b2b/what-is-b2b.md)

Lub można wykonywać inne zadania zarządzania użytkownika, takich jak przypisanie delegatów, przy użyciu zasad oraz udostępnianie kont użytkowników. Aby uzyskać więcej informacji na temat innych dostępnych akcji, zobacz [dokumentacja zarządzania użytkownika usługi Azure Active Directory](../users-groups-roles/index.yml).


