---
title: Opuścić organizację jako użytkownika-gościa — usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Pokazuje, jak użytkownik-Gość usługi Azure AD B2B można pozostawić organizacji za pomocą panelu dostępu.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 05/11/2018
ms.author: mimart
author: msmimart
manager: daveba
ms.reviewer: sasubram
ms.openlocfilehash: caca01411b5bd5f41a35dd99b36b21accba47e5c
ms.sourcegitcommit: 58dc0d48ab4403eb64201ff231af3ddfa8412331
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/26/2019
ms.locfileid: "55078002"
---
# <a name="leave-an-organization-as-a-guest-user"></a>Opuścić organizację jako użytkownika-gościa

Użytkownik-Gość usługi Azure Active Directory (Azure AD) B2B można zdecyduję się opuścić organizacji w dowolnym momencie, jeśli nie są już potrzebne, korzystanie z aplikacji z tej organizacji lub zachowanie skojarzenia. Użytkownik może pozostać organizacji samodzielnie, bez konieczności kontaktowania się z administratorem.

## <a name="leave-an-organization"></a>Opuszczanie organizacji

Aby opuścić organizację jako zalogowany użytkownik [panelu dostępu](https://myapps.microsoft.com), wykonaj następujące czynności:

1. Jeśli jeszcze nie zostało to zrobione organizacji, którą chcesz pozostawić, wybierz nazwę w prawym górnym rogu, a następnie kliknij przycisk organizacji, które chcesz wyjść.
2. W prawym górnym rogu wybierz swoją nazwę.
3. Obok pozycji **organizacje**, wybierz ikonę ustawienia (koło zębate).
 
   ![Zrzut ekranu przedstawiający ustawienia użytkownika w panelu dostępu](media/leave-the-organization/UserSettings.png) 

3. W obszarze **organizacje**, Znajdź, którą chcesz wystawić, a następnie wybierz organizację **opuścić organizację**.

   ![Zrzut ekranu przedstawiający opcję organizacji pozostaw w interfejsie użytkownika](media/leave-the-organization/LeaveOrg.png)

4. Po wyświetleniu monitu o potwierdzenie, wybierz **pozostaw**. 

## <a name="account-removal"></a>Usuwanie konta

Gdy użytkownik opuszcza organizację, usunięcia konta użytkownika "soft" w katalogu. Domyślnie obiekt użytkownika przenosi do **usuniętych użytkowników** obszar w usłudze Azure AD ale nie jest trwale usunięte przez 30 dni. Ta usuwania nietrwałego umożliwia administratorowi przywrócić konto użytkownika (w tym grupy i uprawnienia), jeśli użytkownik wysyła żądanie, aby przywrócić konto w ramach 30-dniowego okresu.

Jeśli to konieczne, administrator dzierżawy trwale usunąć to konto w dowolnym momencie w trakcie 30-dniowego okresu. W tym celu:

1. W [witryny Azure portal](https://portal.azure.com), wybierz opcję **usługi Azure Active Directory**.
2. W obszarze **Zarządzaj** wybierz pozycję **Użytkownicy**.
3. Wybierz **usuniętych użytkowników**.
4. Zaznacz pole wyboru obok usuniętego użytkownika, a następnie wybierz **trwałe usunięcie**.

Jeśli trwale usunąć użytkownika, ta akcja jest nieodwracalny.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="next-steps"></a>Kolejne kroki

- Omówienie usługi Azure AD B2B, zobacz [czym jest współpraca B2B w usłudze Azure AD?](what-is-b2b.md)



