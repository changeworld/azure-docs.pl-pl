---
title: Przypisywanie użytkownika do ról administratorów w usłudze Azure Active Directory | Microsoft Docs
description: Jak zmienić administracyjne informacje użytkownika w usłudze Azure Active Directory
services: active-directory
documentationcenter: ''
author: eross-msft
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: fundamentals
ms.topic: quickstart
ms.date: 06/25/2018
ms.author: lizross
ms.reviewer: jeffsta
ms.openlocfilehash: 7ed564d5954841f96109568b33183908d25bb8be
ms.sourcegitcommit: 828d8ef0ec47767d251355c2002ade13d1c162af
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/25/2018
ms.locfileid: "36939544"
---
# <a name="assign-a-user-to-administrator-roles-in-azure-active-directory"></a>Przypisywanie użytkownika do ról administratorów w usłudze Azure Active Directory
W tym artykule wyjaśniono, jak przypisać użytkownikowi rolę administracyjną w usłudze Azure Active Directory (Azure AD). Aby uzyskać informacje dotyczące dodawania nowych użytkowników w organizacji, zobacz [Dodawanie nowych użytkowników do usługi Azure Active Directory](../add-users-azure-active-directory.md). Dodani użytkownicy domyślnie nie mają uprawnień administratora, ale możesz przypisać im role w dowolnym momencie.

## <a name="assign-a-role-to-a-user"></a>Przypisywanie użytkownikowi roli
1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com) przy użyciu konta, które jest administratorem globalnym lub administratorem ról uprzywilejowanych dla katalogu.

2. Wybierz usługę **Azure Active Directory**, wybierz pozycję **Użytkownicy**, a następnie wybierz z listy określonego użytkownika.

    ![Otwieranie okna zarządzania użytkownikami](./media/active-directory-users-assign-role-azure-portal/create-users-user-management.png)

3. Dla wybranego użytkownika wybierz pozycję **Role katalogu**, wybierz polecenie **Dodaj rolę**, a następnie wybierz odpowiednie role administratora z listy **Role katalogu**, na przykład **Administrator dostępu warunkowego**. Aby uzyskać więcej informacji o rolach administratorów, zobacz [Przypisywanie ról administratorów w usłudze Azure AD](../active-directory-assign-admin-roles-azure-portal.md). 

    ![Przypisywanie użytkownika do roli](./media/active-directory-users-assign-role-azure-portal/create-users-assign-role.png)

1. Naciśnij pozycję **Wybierz**, aby zapisać.

## <a name="next-steps"></a>Następne kroki
* [Szybki start: dodawanie i usuwanie użytkowników w usłudze Azure Active Directory](add-users-azure-active-directory.md)
* [Zarządzanie profilami użytkowników](active-directory-users-profile-azure-portal.md)
* [Dodawanie użytkowników-gości z innego katalogu](../b2b/what-is-b2b.md) 
* [Przypisywanie użytkownika do roli w usłudze Azure AD](active-directory-users-assign-role-azure-portal.md)
* [Przywracanie usuniętego użytkownika](active-directory-users-restore.md)
