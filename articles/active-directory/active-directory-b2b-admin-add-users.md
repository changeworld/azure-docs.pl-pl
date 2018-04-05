---
title: Dodawanie użytkowników współpracy B2B w portalu Azure - Azure Active Directory | Dokumentacja firmy Microsoft
description: Pokazuje, jak administrator może dodawać użytkowników gościa do ich katalogu z organizacji partnerskiej przy użyciu współpracy B2B usługi Azure Active Directory (Azure AD).
services: active-directory
documentationcenter: ''
author: twooley
manager: mtillman
editor: ''
tags: ''
ms.assetid: ''
ms.service: active-directory
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: identity
ms.date: 04/02/2018
ms.author: twooley
ms.reviewer: sasubram
ms.openlocfilehash: 34bd5b51089045c4cd20f29d179bb230e5e3fac2
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2018
---
# <a name="add-azure-active-directory-b2b-collaboration-users-in-the-azure-portal"></a>Dodawanie użytkowników współpracy usługi Azure Active Directory B2B w portalu Azure

Jako administrator globalny lub użytkownika, który jest przypisany do żadnego z ról katalogu administratorem ograniczonym można użyć portalu Azure, aby zaprosić użytkowników współpracy B2B. Można zaprosić użytkowników gościa do katalogu, grupę lub aplikację. Po zaproszeniu użytkownika za pomocą jednej z tych metod, zaproszonych użytkowników konto jest dodawane do usługi Azure Active Directory (Azure AD), z typem użytkownika *gościa*. Gość musi następnie zrealizować zaproszenia, ich dostęp do zasobów.

## <a name="add-guest-users-to-the-directory"></a>Dodaj gości do katalogu

Aby dodać użytkowników współpracy B2B w katalogu, wykonaj następujące kroki:

1. Zaloguj się do [portalu Azure](https://portal.azure.com) jako administrator usługi Azure AD.
2. W okienku nawigacji wybierz **usługi Azure Active Directory**.
3. W obszarze **Zarządzaj**, wybierz pozycję **użytkowników i grup** > **wszyscy użytkownicy**.
4. Wybierz **nowego użytkownika gościa**.

   ![Wskazuje, gdzie nowego użytkownika gościa jest w interfejsie użytkownika](./media/active-directory-b2b-admin-add-users/NewGuestUser-Directory.png) 
 
7. W obszarze **zaprosić Gość**, wprowadź adres e-mail użytkownika zewnętrznego. Opcjonalnie komunikat powitalny. Na przykład:

   ![Wskazuje, gdzie nowego użytkownika gościa jest w interfejsie użytkownika](./media/active-directory-b2b-admin-add-users/InviteGuest.png) 

8. Wybierz **zaprosić** można automatycznie wysłać zaproszenie do użytkownika gościa. W **powiadomień** obszaru, poszukaj **pomyślnie zaproszonych użytkowników** wiadomości. 
 
Po wysłaniu zaproszenia, konto użytkownika jest automatycznie dodawane do katalogu jako Gość.


![Pokazuje B2B użytkownik mający typ użytkownika gościa](./media/active-directory-b2b-admin-add-users/GuestUserType.png)  

## <a name="add-guest-users-to-a-group"></a>Dodaj do grupy Goście
Jeśli musisz ręcznie dodać użytkowników współpracy B2B do grupy jako administrator usługi Azure AD, wykonaj następujące kroki:

1. Zaloguj się do [portalu Azure](https://portal.azure.com) jako administrator usługi Azure AD.
2. W okienku nawigacji wybierz **usługi Azure Active Directory**.
3. W obszarze **Zarządzaj**, wybierz pozycję **użytkowników i grup** > **wszystkich grup**.
4. Wybierz grupę (lub kliknij przycisk **nową grupę** Aby utworzyć nową). Jest warto uwzględnić w opis grupy, że grupa zawiera B2B gości.
5. Wybierz **członków** > **dodawać członków**. 
6. Wykonaj jedną z następujących czynności:
   - Jeśli użytkownik gościa już istnieje w katalogu, Wyszukaj użytkownika B2B. Wybierz użytkownika > kliknij **wybierz** Aby dodać użytkownika do grupy.
   - Jeśli użytkownik-Gość jeszcze nie istnieje w katalogu, wybierz **zaprosić**.
   ![Dodawanie przycisku zaproszenie do dodawania elementów członkowskich gościa](./media/active-directory-b2b-admin-add-users/GroupInvite.png)
   
      W obszarze **zaprosić Gość**, wprowadź adres e-mail i opcjonalnie osobistą wiadomość > Wybierz **zaprosić**. Kliknij przycisk **wybierz** Aby dodać użytkownika do grupy.

      Zaproszenie automatycznie trafia do zaproszonych użytkowników. W **powiadomień** obszaru, poszukaj pomyślnie **użytkownika Invited** wiadomości. 

Można również używać grup dynamicznych z współpracy B2B usługi Azure AD. Aby uzyskać więcej informacji, zobacz [grupami dynamicznymi i współpracy usługi Azure Active Directory B2B](active-directory-b2b-dynamic-groups.md).

## <a name="add-guest-users-to-an-application"></a>Dodaj gości do aplikacji

Aby dodać użytkowników współpracy B2B do aplikacji jako administrator usługi Azure AD, wykonaj następujące kroki:

1. Zaloguj się do [portalu Azure](https://portal.azure.com) jako administrator usługi Azure AD.
2. W okienku nawigacji wybierz **usługi Azure Active Directory**.
3. W obszarze **Zarządzaj**, wybierz pozycję **aplikacje dla przedsiębiorstw** > **wszystkie aplikacje**.
4. Wybierz aplikację, do której chcesz dodać gości.
5. W obszarze **Zarządzaj**, wybierz pozycję **użytkowników i grup**.
6. Wybierz **Dodaj użytkownika**.
7. W obszarze **Dodaj przydziału**, wybierz pozycję **użytkowników i grup**.
8. Wykonaj jedną z następujących czynności:
   - Jeśli użytkownik gościa już istnieje w katalogu, Wyszukaj użytkownika B2B. Wybierz użytkownika, a następnie kliknij przycisk **wybierz** Aby dodać użytkownika do aplikacji.
   - Jeśli użytkownik-Gość jeszcze nie istnieje w katalogu, wybierz **zaprosić**.
   ![Dodawanie przycisku zaproszenie do dodawania elementów członkowskich gościa](./media/active-directory-b2b-admin-add-users/AppInviteUsers.png)
   
      W obszarze **zaprosić Gość**, wprowadź adres e-mail i opcjonalnie osobistą wiadomość > Wybierz **zaprosić**. Kliknij przycisk **wybierz** Aby dodać użytkownika do aplikacji.

      Zaproszenie automatycznie trafia do zaproszonych użytkowników. W **powiadomień** obszaru, poszukaj pomyślnie **użytkownika Invited** wiadomości.

9. W obszarze **Dodaj przydziału**, kliknij przycisk **wybierz rolę** > Wybierz rolę do zastosowania do wybranego użytkownika (jeśli dotyczy) > Wybierz **OK**.
10. Kliknij przycisk **Przypisz**.
 
## <a name="resend-invitations-to-guest-users"></a>Ponownie Wyślij zaproszenia do gości

Jeśli użytkownik-Gość ich zaproszenie nie ma jeszcze zrealizowane, należy wysłać zaproszenie.

1. Zaloguj się do [portalu Azure](https://portal.azure.com) jako administrator usługi Azure AD.
2. W okienku nawigacji wybierz **usługi Azure Active Directory**.
3. W obszarze **Zarządzaj**, wybierz pozycję **użytkowników i grup**.
4. Wybierz **wszyscy użytkownicy**.
5. Wybierz konto użytkownika.
6. W obszarze **Zarządzaj**, wybierz pozycję **profilu**.
7. Jeśli użytkownik nie zaakceptował jeszcze zaproszenia, **ponownie wysłać zaproszenie** opcja jest dostępna. Wybierz ten przycisk, aby ponownie wysłać.

   ![Wyślij ponownie opcja zaproszenie w profilu użytkownika](./media/active-directory-b2b-admin-add-users/Resend-Invitation.png)

> [!NOTE]
> W przypadku ponownego wysłania zaproszenie pierwotnie przekierowanie użytkownika do określonej aplikacji, należy zrozumieć, że łącze w nowe zaproszenie prowadzi użytkownika do panelu dostępu najwyższego poziomu zamiast tego.

## <a name="next-steps"></a>Kolejne kroki

- Aby dowiedzieć się, jak dodać administratorów innych niż Azure AD B2B gości, zobacz [jak pracownicy przetwarzający informacje dodać użytkowników współpracy B2B?](active-directory-b2b-iw-add-users.md)
- Aby uzyskać informacje o wiadomość e-mail z zaproszeniem, zobacz [elementy wiadomość e-mail z zaproszeniem współpracy B2B](active-directory-b2b-invitation-email.md).
- Aby uzyskać informacje na temat procesu realizacji zaproszenia, zobacz [realizacji zaproszenia współpracy B2B](active-directory-b2b-redemption-experience.md).


