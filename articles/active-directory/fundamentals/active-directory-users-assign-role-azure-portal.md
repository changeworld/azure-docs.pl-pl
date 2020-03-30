---
title: Przypisywanie ról katalogów użytkownikom — usługa Azure Active Directory | Dokumenty firmy Microsoft
description: Instrukcje dotyczące przypisywania ról administratora i osób niebędących administratorami do użytkowników za pomocą usługi Azure Active Directory.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75422900"
---
# <a name="assign-administrator-and-non-administrator-roles-to-users-with-azure-active-directory"></a>Przypisywanie ról administratora i osób niebędących administratorami do użytkowników za pomocą usługi Azure Active Directory
Jeśli użytkownik w organizacji potrzebuje uprawnień do zarządzania zasobami usługi Azure Active Directory (Azure AD), należy przypisać użytkownikowi odpowiednią rolę w usłudze Azure AD, na podstawie akcji, które użytkownik potrzebuje uprawnień do wykonania.

Aby uzyskać więcej informacji na temat dostępnych ról, zobacz [Przypisywanie ról administratora w usłudze Azure Active Directory](../users-groups-roles/directory-assign-admin-roles.md). Aby uzyskać więcej informacji na temat dodawania użytkowników, zobacz [Dodawanie nowych użytkowników do usługi Azure Active Directory](add-users-azure-active-directory.md).

## <a name="assign-roles"></a>Przypisywanie ról
Typowym sposobem przypisywania ról usługi Azure AD do użytkownika jest strona **roli katalogu** dla użytkownika.

Można również przypisać role przy użyciu zarządzania tożsamościami uprzywilejowanymi (PIM). Aby uzyskać bardziej szczegółowe informacje dotyczące korzystania z usługi PIM, zobacz [Zarządzanie tożsamościami uprzywilejowanymi](https://docs.microsoft.com/azure/active-directory/privileged-identity-management).

### <a name="to-assign-a-role-to-a-user"></a>Aby przypisać rolę do użytkownika
1. Przejdź do [witryny Azure portal](https://portal.azure.com/) i zaloguj się przy użyciu konta administratora globalnego dla katalogu. 

2. Wyszukaj i wybierz pozycję **Azure Active Directory**.

      ![Wyszukiwanie usługi Azure portal dla usługi Azure Active Directory](media/active-directory-users-assign-role-azure-portal/search-azure-active-directory.png)


3. Wybierz pozycję **Użytkownicy**.

4. Wyszukaj i wybierz użytkownika, który otrzymuje przypisanie roli. Na przykład _Alain Charon_.

      ![Strona Wszyscy użytkownicy - wybierz użytkownika](media/active-directory-users-assign-role-azure-portal/directory-role-select-user.png)

5. Na stronie **Alain Charon - Profil** wybierz pozycję **Przypisane role**.

    Zostanie wyświetlona strona **roli Alain Charon - Directory.**

6. Wybierz **pozycję Dodaj przypisanie**, wybierz rolę, która ma zostać przypisana do Alaina (na przykład _administrator aplikacji),_ a następnie wybierz pozycję **Wybierz**.

    ![Strona Przypisane role — pokazuje wybraną rolę](media/active-directory-users-assign-role-azure-portal/directory-role-select-role.png)

    Rola administratora aplikacji jest przypisana do Alain Charon i pojawia się na stronie **roli Alain Charon - Katalog.**

## <a name="remove-a-role-assignment"></a>Usuwanie przypisania roli
Jeśli chcesz usunąć przypisanie roli z użytkownika, możesz to również zrobić ze strony **roli Alain Charon - Directory.**

### <a name="to-remove-a-role-assignment-from-a-user"></a>Aby usunąć przypisanie roli z użytkownika

1. Wybierz **pozycję Azure Active Directory**, wybierz pozycję **Użytkownicy**, a następnie wyszukaj i wybierz użytkownika, który usuwa przypisanie roli. Na przykład _Alain Charon_.

2. Wybierz **pozycję Przypisane role**, wybierz pozycję Administrator **aplikacji**, a następnie wybierz pozycję **Usuń przypisanie**.

    ![Strona Przypisane role z wybraną rolą i opcją usuń](media/active-directory-users-assign-role-azure-portal/directory-role-remove-role.png)

    Rola administratora aplikacji jest usuwana z Alain Charon i nie jest już wyświetlana na stronie **roli Alain Charon - Directory.**

## <a name="next-steps"></a>Następne kroki
- [Dodawanie lub usuwanie użytkowników](add-users-azure-active-directory.md)

- [Dodawanie lub zmienianie informacji o profilu](active-directory-users-profile-azure-portal.md)

- [Dodawanie użytkowników-gości z innego katalogu](../b2b/what-is-b2b.md)

Można też wykonywać inne zadania zarządzania użytkownikami, takie jak przypisywanie pełnomocników, korzystanie z zasad i udostępnianie kont użytkowników. Aby uzyskać więcej informacji na temat innych dostępnych akcji, zobacz [Dokumentacja zarządzania użytkownikami usługi Azure Active Directory](../users-groups-roles/index.yml).


