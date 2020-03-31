---
title: Opuszczanie organizacji jako użytkownik-gość — usługa Azure Active Directory
description: Pokazuje, jak użytkownik-gość usługi Azure AD B2B może opuścić organizację za pomocą Panelu dostępu.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 06/13/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8bce67c81b924d768826402b707c41c085b7767b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74272494"
---
# <a name="leave-an-organization-as-a-guest-user"></a>Opuszczanie organizacji jako użytkownika-gościa

Użytkownik-gość usługi Azure Active Directory (Azure AD) B2B może zdecydować o opuszczeniu organizacji w dowolnym momencie, jeśli nie musi już korzystać z aplikacji z tej organizacji ani utrzymywać żadnego skojarzenia. Użytkownik może opuścić organizację samodzielnie, bez konieczności kontaktowego się z administratorem.

> [!NOTE]
> Użytkownik-gość nie może opuścić organizacji, jeśli jego konto jest wyłączone w dzierżawie domowej lub dzierżawie zasobów. Jeśli ich konto jest wyłączone, użytkownik-gość będzie musiał skontaktować się z administratorem dzierżawy, który może usunąć konto gościa lub włączyć konto gościa, aby użytkownik mógł opuścić organizację.

## <a name="leave-an-organization"></a>Opuszczanie organizacji

Aby opuścić organizację, wykonaj następujące kroki.

1. Przejdź do strony profilu panelu dostępu, wykonując jedną z następujących czynności:
   
   - W [witrynie Azure portal](https://portal.azure.com)kliknij swoją nazwę w prawym górnym rogu i wybierz pozycję **Wyświetl konto**.
   - Otwórz [Panel dostępu](https://myapps.microsoft.com), kliknij swoje imię i nazwisko w prawym górnym rogu, a następnie obok **pozycji Organizacje**wybierz ikonę ustawień (bieg).
 
   ![Zrzut ekranu przedstawiający ustawienia użytkownika w Panelu programu Access](media/leave-the-organization/UserSettings.png) 

   > [!NOTE]
   > Jeśli nie jesteś jeszcze zalogowany w organizacji, którą chcesz opuścić, w obszarze **Organizacje**kliknij łącze **Zaloguj się, aby pozostawić łącze organizacyjne** obok nazwy organizacji. Po zalogowaniu kliknij ponownie swoje imię i nazwisko w prawym górnym rogu i obok **pozycji Organizacje**wybierz ikonę ustawień (bieg).

3. W obszarze **Organizacje**znajdź organizację, którą chcesz opuścić, i wybierz pozycję **Opuść organizację**.

   ![Zrzut ekranu przedstawiający opcję Zostaw organizację w interfejsie użytkownika](media/leave-the-organization/LeaveOrg.png)

4. Gdy pojawi się prośba o potwierdzenie, wybierz **opcję Opuść**. 

## <a name="account-removal"></a>Usunięcie konta

Gdy użytkownik opuszcza organizację, konto użytkownika jest "nietrwale usunięte" w katalogu. Domyślnie obiekt użytkownika przenosi się do obszaru **Użytkownicy uszkadkieni w** usłudze Azure AD, ale nie jest trwale usuwany przez 30 dni. To nietremne usunięcie umożliwia administratorowi przywrócenie konta użytkownika (w tym grup i uprawnień), jeśli użytkownik złoży żądanie przywrócenia konta w ciągu 30-dniowego okresu.

W razie potrzeby administrator dzierżawy może trwale usunąć konto w dowolnym momencie w okresie 30 dni. W tym celu:

1. W [witrynie Azure portal](https://portal.azure.com)wybierz pozycję **Azure Active Directory**.
2. W obszarze **Zarządzaj** wybierz pozycję **Użytkownicy**.
3. Wybierz pozycję **Usunięci użytkownicy**.
4. Zaznacz pole wyboru obok usuniętego użytkownika, a następnie wybierz pozycję **Usuń trwale**.

Jeśli trwale usuniesz użytkownika, ta akcja jest nieodwołalna.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="next-steps"></a>Następne kroki

- Aby zapoznać się z omówieniem usługi Azure AD B2B, zobacz [Co to jest współpraca usługi Azure AD B2B?](what-is-b2b.md)



