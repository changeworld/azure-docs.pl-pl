---
title: Przypisz użytkownika do ról administratora w usłudze Azure Active Directory | Dokumentacja firmy Microsoft
description: Wyjaśniono, jak zmienić administratora informacje o użytkowniku w usłudze Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: users-groups-roles
ms.topic: article
ms.date: 01/08/2018
ms.author: curtand
ms.reviewer: jeffsta
ms.openlocfilehash: 61662acb10a6f2085d297eae473e1330c619d48d
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
---
# <a name="assign-a-user-to-administrator-roles-in-azure-active-directory"></a>Przypisz użytkownika do ról administratora w usłudze Azure Active Directory
W tym artykule opisano sposób przypisywania roli administracyjnej dla użytkownika w usłudze Azure Active Directory (Azure AD). Aby uzyskać informacje dotyczące dodawania nowych użytkowników w organizacji, zobacz [Dodawanie nowych użytkowników do usługi Azure Active Directory](active-directory-users-create-azure-portal.md). Dodani użytkownicy domyślnie nie mają uprawnień administratora, ale możesz przypisać im role w dowolnym momencie.

## <a name="assign-a-role-to-a-user"></a>Przypisywanie użytkownikowi roli
1. Zaloguj się w [centrum administracyjnym usługi Azure AD](https://aad.portal.azure.com) przy użyciu konta, które jest administratorem globalnym katalogu.
2. Wybierz **użytkowników i grup**.

   ![Otwieranie Zarządzanie użytkownikami](./media/active-directory-users-assign-role-azure-portal/create-users-user-management.png)
3. Wybierz **wszyscy użytkownicy**.
  
  ![Otwieranie wszystkie grupy użytkowników](./media/active-directory-users-assign-role-azure-portal/create-users-open-users-blade.png)
4. Wybierz użytkownika z listy.
5. Dla wybranego użytkownika, wybierz **roli katalogu** , a następnie przypisz użytkownika do roli z **roli katalogu** listy. Aby uzyskać więcej informacji dotyczących ról użytkowników i administratorów, zobacz [Przypisywanie ról administratorów w usłudze Azure AD](active-directory-assign-admin-roles-azure-portal.md).

      ![Przypisanie użytkownika do roli](./media/active-directory-users-assign-role-azure-portal/create-users-assign-role.png)
6. Wybierz pozycję **Zapisz**.

## <a name="next-steps"></a>Kolejne kroki
* [Szybki Start: Dodawanie lub usuwanie użytkowników w usłudze Azure Active Directory](add-users-azure-active-directory.md)
* [Zarządzanie profilami użytkowników](active-directory-users-profile-azure-portal.md)
* [Dodaj gości z innego katalogu](active-directory-b2b-what-is-azure-ad-b2b.md) 
* [Przypisanie użytkownika do roli w usługi Azure AD](active-directory-users-assign-role-azure-portal.md)
* [Przywracanie usuniętego użytkownika](active-directory-users-restore.md)
