---
title: Dodawanie lub usuwanie użytkowników w usłudze Azure Active Directory | Dokumentacja firmy Microsoft
description: Wyjaśniono, jak dodać nowych użytkowników lub usunąć istniejących użytkowników w usłudze Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.component: users-groups-roles
ms.topic: article
ms.date: 01/08/2018
ms.author: curtand
ms.reviewer: jeffsta
ms.custom: it-pro
ms.openlocfilehash: 3e8b7061e57c1baf222e5f565a5b7efc6b997507
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
---
# <a name="quickstart-add-new-users-to-azure-active-directory"></a>Szybki Start: Dodawanie nowych użytkowników do usługi Azure Active Directory
W tym artykule wyjaśniono, jak usunąć lub dodanie użytkowników w organizacji do dzierżawy usługi Azure Active Directory (Azure AD) z orgnization przy użyciu portalu Azure lub synchronizowanie danych konta użytkownika systemu Windows Server AD lokalnymi. 

## <a name="add-cloud-based-users"></a>Dodaj użytkowników w chmurze
1. Zaloguj się do [Centrum administracyjnego usługi Azure Active Directory](https://aad.portal.azure.com) przy użyciu konta, które jest administratorem globalnym katalogu.
2. Wybierz **usługi Azure Active Directory** , a następnie **użytkowników i grup**.
3. Na **użytkowników i grup**, wybierz pozycję **wszyscy użytkownicy**, a następnie wybierz **nowego użytkownika**.
   ![Dodaj polecenie](./media/add-users-azure-active-directory/add-user.png)
4. Wprowadź szczegóły użytkownika, takich jak **nazwa** i **nazwy użytkownika**. Część nazwy domeny nazwa użytkownika musi być początkowej domyślnej domeny nazwa "[nazwa domeny].onmicrosoft.com" lub zweryfikowane, niefederacyjnych [niestandardowej nazwy domeny](add-custom-domain.md) takich jak "contoso.com".
5. Skopiuj lub w przeciwnym razie należy pamiętać wygenerowane hasło, dzięki czemu można udostępnić go użytkownikowi po zakończeniu tego procesu.
6. Opcjonalnie można otwierać i Wypełnij informacje w **profilu**, **grup**, lub **roli katalogu** dla użytkownika. Aby uzyskać więcej informacji dotyczących ról użytkowników i administratorów, zobacz [Przypisywanie ról administratorów w usłudze Azure AD](active-directory-assign-admin-roles-azure-portal.md).
7. Na **użytkownika**, wybierz pozycję **Utwórz**.
8. Bezpiecznie dystrybucji wygenerowane hasło dla nowego użytkownika, dzięki czemu użytkownicy mogą się logować.

> [!TIP]
> Możesz także zsynchronizować dane konto użytkownika z lokalnego systemu Windows Server AD. Firmy Microsoft tożsamościach span lokalnych i chmurze możliwości tworzenia tożsamością jednego użytkownika do uwierzytelniania i autoryzacji do wszystkich zasobów, niezależnie od lokalizacji. Nazywamy to tożsamość hybrydowa. [Azure AD Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect) może służyć do integracji katalogów lokalnych z usługą Azure Active Directory dla scenariuszy hybrydowych tożsamości. Dzięki temu użytkownicy mogą posługiwać się wspólną tożsamością dla usługi Office 365, platformy Azure i aplikacji SaaS zintegrowanych z usługą Azure AD. 

## <a name="delete-users-from-azure-ad"></a>Usuwanie użytkowników z usługi Azure AD
1. Zaloguj się do [Centrum administracyjnego usługi Azure Active Directory](https://aad.portal.azure.com) przy użyciu konta, które jest administratorem globalnym katalogu.
2. Wybierz **użytkowników i grup**.
3. Na **użytkowników i grup** bloku, wybierz użytkownika, aby usunąć z listy. 
4. W bloku dla wybranego użytkownika, wybierz **omówienie**, a następnie na pasku poleceń Wybierz **usunąć**.
   ![Dodaj polecenie](./media/add-users-azure-active-directory/delete-user.png)


### <a name="learn-more"></a>Dowiedz się więcej 
* [Dodaj gości z innego katalogu](active-directory-b2b-what-is-azure-ad-b2b.md) 
* [Przypisanie użytkownika do roli w usługi Azure AD](active-directory-users-assign-role-azure-portal.md)
* [Zarządzanie profilami użytkowników](active-directory-users-profile-azure-portal.md)
* [Przywracanie usuniętego użytkownika](active-directory-users-restore.md)



## <a name="next-steps"></a>Kolejne kroki
W tym szybkiego startu kiedy znasz już sposób dodawania nowych użytkowników do usługi Azure AD Premium. 

Poniższe łącze służy do tworzenia nowego użytkownika w usłudze Azure AD z portalu Azure.

> [!div class="nextstepaction"]
> [Dodawanie użytkowników do usługi Azure AD](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/All users) 