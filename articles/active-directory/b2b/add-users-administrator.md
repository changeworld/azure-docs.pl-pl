---
title: Dodawanie użytkowników współpracy B2B w witrynie Azure portal — Azure AD
description: Pokazuje, jak administrator może dodawać użytkowników-gości do swojego katalogu z organizacji partnerskiej przy użyciu współpracy B2B usługi Azure Active Directory (Azure AD).
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: cf2ca3bde0cfee97adee8bb875df1522e95b1f53
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79263506"
---
# <a name="add-azure-active-directory-b2b-collaboration-users-in-the-azure-portal"></a>Dodawanie użytkowników współpracy usługi Azure Active Directory B2B w witrynie Azure portal

Jako użytkownik, któremu przypisano dowolną z ról katalogu ograniczonego administratora, możesz użyć witryny Azure Portal, aby zaprosić użytkowników współpracy B2B. Można zaprosić użytkowników-gościa do katalogu, do grupy lub do aplikacji. Po zaproszeniu użytkownika za pomocą dowolnej z tych metod konto zaproszonego użytkownika jest dodawane do usługi Azure Active Directory (Azure AD), z typem użytkownika *gość*. Użytkownik-gość musi następnie zrealizować zaproszenie do uzyskiwania dostępu do zasobów. Zaproszenie użytkownika nie wygasa.

Po dodaniu użytkownika-gościa do katalogu można wysłać użytkownikowi-gościowi bezpośrednie łącze do udostępnionej aplikacji lub użytkownik-gość może kliknąć adres URL realizacji w wiadomości e-mail z zaproszeniem. Aby uzyskać więcej informacji na temat procesu realizacji, zobacz [Wykup zaproszenia do współpracy B2B](redemption-experience.md).

> [!IMPORTANT]
> Należy wykonać kroki opisane w [instrukcjach: Dodaj informacje o ochronie prywatności organizacji w usłudze Azure Active Directory,](https://aka.ms/adprivacystatement) aby dodać adres URL zasad zachowania poufności informacji organizacji. W ramach procesu realizacji zaproszenia po raz pierwszy zaproszony użytkownik musi wyrazić zgodę na korzystanie z warunków prywatności użytkownika, aby kontynuować. 

## <a name="before-you-begin"></a>Przed rozpoczęciem

Upewnij się, że ustawienia współpracy zewnętrznej w organizacji są skonfigurowane w taki sposób, że możesz zapraszać gości. Domyślnie wszyscy użytkownicy i administratorzy mogą zapraszać gości. Ale zasady współpracy zewnętrznej organizacji mogą być skonfigurowane tak, aby uniemożliwić niektórym typom użytkowników lub administratorów zapraszanie gości. Aby dowiedzieć się, jak wyświetlić i ustawić te zasady, zobacz [Włączanie współpracy zewnętrznej B2B i zarządzanie osobami, które mogą zapraszać gości.](delegate-invitations.md)

## <a name="add-guest-users-to-the-directory"></a>Dodawanie użytkowników-gości do katalogu

Aby dodać użytkowników współpracy B2B do katalogu, wykonaj następujące kroki:

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) jako użytkownik, któremu przypisano rolę katalogu ograniczonego administratora lub rolę osoby zapraszania gościa.
2. Wyszukaj i wybierz **usługę Azure Active Directory** z dowolnej strony.
3. W obszarze **Zarządzaj** wybierz pozycję **Użytkownicy**.
4. Wybierz pozycję **Nowy użytkownik-gość**.

   ![Pokazuje, gdzie nowy użytkownik-gość znajduje się w interfejsie użytkownika](./media/add-users-administrator/new-guest-user-in-all-users.png) 
 
   > [!NOTE]
   > Opcja **Nowy użytkownik-gość** jest również dostępna na stronie **Relacje organizacyjne.** W **usłudze Azure Active Directory**w obszarze **Zarządzanie**wybierz pozycję **Relacje organizacyjne**.

5. Na stronie **Nowy użytkownik** wybierz pozycję **Zaproś użytkownika,** a następnie dodaj informacje o użytkowniku-gościu. 

    > [!NOTE]
    > Grupowe adresy e-mail nie są obsługiwane; wpisz adres e-mail danej osoby. Ponadto niektórzy dostawcy poczty e-mail umożliwiają użytkownikom dodawanie symbolu plus (+) i dodatkowego tekstu na ich adresy e-mail, aby ułatwić filtrowanie skrzynki odbiorczej. Jednak usługa Azure AD obecnie nie obsługuje symboli plus w adresach e-mail. Aby uniknąć problemów z dostawą, należy pominąć symbol plus i wszelkie znaki następujące po nim do symbolu @.

   - **Nazwa.** Imię i nazwisko użytkownika-gościa.
   - **Adres e-mail (wymagany)**. Adres e-mail użytkownika-gościa.
   - **Wiadomość osobista (opcjonalnie)** Dołącz osobistą wiadomość powitalną do użytkownika-gościa.
   - **Grupy:** Możesz dodać użytkownika-gościa do jednej lub więcej istniejących grup lub zrobić to później.
   - **Rola katalogu:** Jeśli potrzebujesz uprawnień administracyjnych usługi Azure AD dla użytkownika, możesz dodać je do roli usługi Azure AD. 

7. Wybierz pozycję **Zaproś**, aby automatycznie wysłać zaproszenie do użytkownika-gościa. 
 
Po wysłaniu zaproszenia konto użytkownika zostanie automatycznie dodane do katalogu jako gość.


![Pokazuje użytkownika B2B z typem użytkownika gościa](./media/add-users-administrator/GuestUserType.png)  

## <a name="add-guest-users-to-a-group"></a>Dodawanie użytkowników-gości do grupy
Jeśli chcesz ręcznie dodać użytkowników współpracy B2B do grupy, wykonaj następujące kroki:

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) jako administrator usługi Azure AD.
2. Wyszukaj i wybierz **usługę Azure Active Directory** z dowolnej strony.
3. W obszarze **Zarządzaj**wybierz pozycję **Grupy**.
4. Wybierz grupę (lub kliknij pozycję **Nowa grupa,** aby utworzyć nową). Warto dołączyć do opisu grupy, że grupa zawiera użytkowników-gości B2B.
5. Wybierz **członków**. 
6. Wykonaj jedną z następujących czynności:
   - Jeśli użytkownik-gość już istnieje w katalogu, wyszukaj użytkownika B2B. Wybierz użytkownika, a następnie kliknij przycisk **Wybierz,** aby dodać go do grupy.
   - Jeśli użytkownik-gość jeszcze nie istnieje w katalogu, zaproś go do grupy, wpisując swój adres e-mail w polu wyszukiwania, wpisując opcjonalną wiadomość osobistą, a następnie klikając przycisk **Wybierz**. Zaproszenie automatycznie trafi do zaproszonego użytkownika.
     
     ![Przycisk Dodaj zaproszenie, aby dodać członków-gościa](./media/add-users-administrator/GroupInvite.png)
   
Można również użyć grup dynamicznych z współpracy usługi Azure AD B2B. Aby uzyskać więcej informacji, zobacz [Grupy dynamiczne i współpraca b2b usługi Azure Active Directory](use-dynamic-groups.md).

## <a name="add-guest-users-to-an-application"></a>Dodawanie użytkowników-gości do aplikacji

Aby dodać użytkowników współpracy B2B do aplikacji, wykonaj następujące kroki:

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) jako administrator usługi Azure AD.
2. Wyszukaj i wybierz **usługę Azure Active Directory** z dowolnej strony.
3. W obszarze **Zarządzanie**wybierz pozycję **Aplikacje przedsiębiorstwa** > **Wszystkie aplikacje**.
4. Wybierz aplikację, do której chcesz dodać użytkowników-gościa.
5. Na pulpicie nawigacyjnym aplikacji wybierz **pozycję Łączna liczba użytkowników,** aby otworzyć okienko **Użytkownicy i grupy.**

    ![Przycisk Łączna liczba użytkowników umożliwiająca dodanie otwartych grup Użytkowników i Grup](./media/add-users-administrator/AppUsersAndGroups.png)

6. Wybierz przycisk **Dodaj użytkownika**.
7. W obszarze **Dodaj przydział**wybierz pozycję Użytkownik **i grupy**.
8. Wykonaj jedną z następujących czynności:
   - Jeśli użytkownik-gość już istnieje w katalogu, wyszukaj użytkownika B2B. Wybierz użytkownika, kliknij przycisk **Wybierz**, a następnie kliknij przycisk **Przypisz,** aby dodać użytkownika do aplikacji.
   - Jeśli użytkownik-gość jeszcze nie istnieje w katalogu, w obszarze **Wybierz element członkowski lub zaproś użytkownika zewnętrznego**wpisz adres e-mail użytkownika. W oknie komunikatu wpisz opcjonalną wiadomość osobistą. W obszarze okna komunikatu kliknij pozycję **Zaproś**.
           
       ![Przycisk Dodaj zaproszenie, aby dodać członków-gościa](./media/add-users-administrator/AppInviteUsers.png)
   
      Kliknij **pozycję Wybierz**, a następnie kliknij pozycję **Przypisz,** aby dodać użytkownika do aplikacji. Zaproszenie automatycznie wychodzi do zaproszonego użytkownika.

9. Użytkownik-gość pojawia się na liście **Użytkownicy i grupy** aplikacji z przypisaną rolą **dostępu domyślnego**. Jeśli chcesz zmienić rolę, wykonaj następujące czynności:
   - Wybierz użytkownika-gościa, a następnie wybierz pozycję **Edytuj**. 
   - W obszarze **Edytowanie przypisania**kliknij pozycję **Wybierz rolę**i wybierz rolę, którą chcesz przypisać do wybranego użytkownika.
   - Kliknij **pozycję Wybierz**.
   - Kliknij przycisk **Przypisz**.
 
## <a name="resend-invitations-to-guest-users"></a>Ponowne wysyłanie zaproszeń do użytkowników-gości

Jeśli użytkownik-gość nie zrealizował jeszcze zaproszenia, możesz wysłać je ponownie.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) jako administrator usługi Azure AD.
2. Wyszukaj i wybierz **usługę Azure Active Directory** z dowolnej strony.
3. W obszarze **Zarządzaj** wybierz pozycję **Użytkownicy**.
5. Wybierz konto użytkownika.
6. W obszarze **Zarządzaj**wybierz **pozycję Profil**.
7. Jeśli użytkownik nie zaakceptował jeszcze zaproszenia, dostępna jest opcja **Wyślij ponownie zaproszenie.** Wybierz ten przycisk, aby ponownie wyśmiać.

   ![Opcja wyślij ponownie zaproszenie w profilu użytkownika](./media/add-users-administrator/b2b-user-resend-invitation.png)

> [!NOTE]
> Jeśli ponownie wyślij zaproszenie, które pierwotnie kierowało użytkownika do określonej aplikacji, zrozum, że łącze w nowym zaproszeniu przeniesie użytkownika do panelu dostępu najwyższego poziomu.

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się, jak administratorzy usług AD spoza platformy Azure mogą dodawać użytkowników-gości B2B, zobacz [Jak pracownicy informacji dodają użytkowników współpracy B2B?](add-users-information-worker.md)
- Aby uzyskać informacje na temat wiadomości e-mail z zaproszeniem, zobacz [Elementy wiadomości e-mail z zaproszeniem do współpracy B2B](invitation-email-elements.md).

