---
title: Pozostaw organizacji jako gość — Azure Active Directory | Dokumentacja firmy Microsoft
description: Pokazuje, jak użytkownika gościa B2B usługi Azure AD można pozostawić organizacji, korzystając z panelu dostępu.
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 05/11/2018
ms.author: twooley
author: twooley
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: 942439da3e116415c77a28950df69d44744b2dd8
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/12/2018
ms.locfileid: "34077543"
---
# <a name="leave-an-organization-as-a-guest-user"></a>Pozostaw organizacji jako Gość

Gość B2B usługi Azure Active Directory (Azure AD) można zdecydować pozostawić organizacji w dowolnym momencie, jeśli już muszą korzystać z aplikacji z tej organizacji lub przechowywać wszystkie skojarzenia. Użytkownik może wyjść organizacji samodzielnie, bez konieczności kontaktowania się z administratorem.

## <a name="leave-an-organization"></a>Pozostaw organizacji

Aby opuścić organizacji, jako użytkownik jest zalogowany w usłudze [panelu dostępu](https://myapps.microsoft.com), wykonaj następujące czynności:

1. Jeśli jeszcze nie zalogowano Cię do organizacji, której chcesz opuścić, wybierz nazwę w prawym górnym rogu, a następnie kliknij przycisk organizacji, które chcesz opuścić.
2. W prawym górnym rogu wybierz nazwę.
3. Obok pozycji **organizacji**, wybierz ikonę ustawień (koło zębate).
 
   ![Zrzut ekranu przedstawiający ustawień użytkownika w panelu dostępu](media/active-directory-b2b-leave-the-organization/UserSettings.png) 

3. W obszarze **organizacji**, Znajdź organizacji, który chcesz pozostawić, a następnie wybierz **pozostaw organizacji**.

   ![Zrzut ekranu przedstawiający pozostaw opcję organizacji w interfejsie użytkownika](media/active-directory-b2b-leave-the-organization/LeaveOrg.png)

4. Gdy monit o potwierdzenie, wybierz **pozostaw**. 

## <a name="account-removal"></a>Usunięcie konta

Gdy użytkownik opuści organizacji, usunięcia konta użytkownika "soft" w katalogu. Domyślnie obiekt użytkownika przenosi do **usunąć użytkowników** obszar w usłudze Azure AD ale nie jest trwale usunięte przez 30 dni. Ta usuwania nietrwałego umożliwia administratorowi przywrócić konto użytkownika (w tym grup i uprawnień), jeśli użytkownik wysyła żądanie do przywrócenia konta w ramach 30-dniowego okresu.

W razie potrzeby administrator dzierżawy może trwałe usunięcie konta w dowolnym momencie w ciągu 30-dniowego okresu. W tym celu:

1. W [portalu Azure](https://portal.azure.com), wybierz pozycję **usługi Azure Active Directory**.
2. W obszarze **Zarządzaj**, wybierz pozycję **użytkowników**.
3. Wybierz **usunąć użytkowników**.
4. Zaznacz pole wyboru obok usuniętego użytkownika, a następnie wybierz **trwale usunąć**.

Jeśli trwale usunąć użytkownika, ta akcja jest nieodwracalny.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="next-steps"></a>Kolejne kroki

- Omówienie B2B usługi Azure AD, zobacz [co to jest współpraca B2B usługi Azure AD?](active-directory-b2b-what-is-azure-ad-b2b.md)



