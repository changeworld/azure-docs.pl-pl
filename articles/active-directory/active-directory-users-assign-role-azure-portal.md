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
ms.component: fundamentals
ms.topic: article
ms.date: 01/08/2018
ms.author: curtand
ms.reviewer: jeffsta
ms.openlocfilehash: 580c5e66bedbec00495fa5fd0a7632005fa7af2f
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2018
ms.locfileid: "34712997"
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
