---
title: Dodają użytkowników we współpracy B2B w witrynie Azure portal — usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Pokazuje, jak administrator może dodać użytkowników-gości do swojego katalogu z organizacji partnerskiej użyciu funkcji współpracy B2B usługi Azure Active Directory (Azure AD).
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 04/10/2019
ms.author: mimart
author: msmimart
manager: celested
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: ef0a4e4a05427b1ed83b017b7a49862596aaf50a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60414557"
---
# <a name="add-azure-active-directory-b2b-collaboration-users-in-the-azure-portal"></a>Dodają użytkowników we współpracy B2B usługi Azure Active Directory w witrynie Azure portal

Jako użytkownik, który jest przypisany do żadnego z ról w katalogu ograniczony administrator można użyć witryny Azure portal, aby zaprosić użytkowników we współpracy B2B. Możesz zaprosić użytkowników-gości do katalogu, grupę lub aplikację. Po za pomocą dowolnej z tych metod można zaprosić użytkownika, zaproszonego użytkownika konto jest dodawane do usługi Azure Active Directory (Azure AD) z typem użytkownika *gościa*. Użytkownik-Gość musi następnie zrealizować zaproszenia uzyskują dostęp do zasobów.

Po dodaniu użytkownika-gościa do katalogu, możesz albo wysłać użytkownik-Gość bezpośredni link do udostępnionej aplikacji, lub kliknąć adres URL realizacji w wiadomości e-mail z zaproszeniem użytkownika gościa. Aby uzyskać więcej informacji na temat procesu realizacji zobacz [realizacja zaproszenia współpracy B2B](redemption-experience.md).

> [!IMPORTANT]
> Należy wykonać czynności opisane w [porad: Dodaj informacje o prywatności w organizacji w usłudze Azure Active Directory](https://aka.ms/adprivacystatement) można dodać adres URL zasady zachowania poufności informacji w Twojej organizacji. W ramach procesu realizacji zaproszenia czas pierwszego zaproszonego użytkownika musi wyrazić zgodę na Twoje warunki ochrony prywatności, aby kontynuować. 

## <a name="add-guest-users-to-the-directory"></a>Dodawanie użytkowników-gości do katalogu

Aby dodać użytkowników we współpracy B2B do katalogu, wykonaj następujące kroki:

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) użytkownika, który jest przypisany, dowolny ograniczony administrator ról w katalogu.
2. W okienku nawigacji wybierz **usługi Azure Active Directory**.
3. W obszarze **Zarządzaj** wybierz pozycję **Użytkownicy**.
4. Wybierz pozycję **Nowy użytkownik-gość**.

   ![Pokazuje, gdzie jest nowy użytkownik-Gość w interfejsie użytkownika](./media/add-users-administrator/NewGuestUser-Directory.png) 
 
   > [!NOTE]
   > **Nowy użytkownik-Gość** opcja jest dostępna również na **relacje w organizacji** strony. W **usługi Azure Active Directory**w obszarze **Zarządzaj**, wybierz opcję **relacje w organizacji**.

5. W obszarze **Nazwa użytkownika** wprowadź adres e-mail użytkownika zewnętrznego. Opcjonalnie możesz dodać komunikat powitalny. Na przykład:

   ![Pokazuje, gdzie jest nowy użytkownik-Gość w interfejsie użytkownika](./media/add-users-administrator/InviteGuest.png) 

    > [!NOTE]
    > Adres e-mail grupy nie są obsługiwane; Wprowadź adres e-mail osoby. Ponadto niektórzy dostawcy poczty e-mail użytkownicy mogą dodawać plus symbol (+), jak i dodatkowy tekst na ich adresy e-mail, ułatwiające wykonywanie elementów, takich jak filtrowanie skrzynki odbiorczej. Jednak usługa Azure AD nie obsługuje obecnie oraz symbole w adresy e-mail. Aby uniknąć problemów dostarczania, Pomiń symbol znaku plus i znaków do następnego znaku @.

6. Wybierz pozycję **Zaproś**, aby automatycznie wysłać zaproszenie do użytkownika-gościa. 
 
Po wysłaniu zaproszenia konto użytkownika zostanie automatycznie dodane do katalogu jako gość.


![Widoczne dla użytkownika B2B z typem użytkownika gościa](./media/add-users-administrator/GuestUserType.png)  

## <a name="add-guest-users-to-a-group"></a>Dodawanie użytkowników-gości do grupy
Jeśli musisz ręcznie dodać użytkowników we współpracy B2B do grupy jako administrator usługi Azure AD, wykonaj następujące kroki:

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) jako administrator usługi Azure AD.
2. W okienku nawigacji wybierz **usługi Azure Active Directory**.
3. W obszarze **Zarządzaj**, wybierz opcję **grup**.
4. Wybierz grupę (lub kliknij przycisk **nową grupę** Aby utworzyć nowe konto). To dobry pomysł, aby uwzględnić w opis grupy, że grupa zawiera użytkowników-gości B2B.
5. Wybierz **członków**. 
6. Wykonaj jedną z następujących czynności:
   - Jeśli użytkownik-Gość już istnieje w katalogu, należy wyszukać użytkownika B2B. Wybierz użytkownika, a następnie kliknij przycisk **wybierz** dodać użytkownika do grupy.
   - Jeśli użytkownik-Gość nie istnieje już w katalogu, Zaproś ich do grupy, wpisując ich adresy e-mail w polu wyszukiwania, wpisując opcjonalną wiadomość osobistych, a następnie klikając **wybierz**. Zaproszenie automatycznie trafia do zaproszonego użytkownika.
     
     ![Dodaj przycisk zaproszenie, aby dodać elementy członkowskie gościa](./media/add-users-administrator/GroupInvite.png)
   
Można również użyć grup dynamicznych przy użyciu funkcji współpracy B2B usługi Azure AD. Aby uzyskać więcej informacji, zobacz [grupy dynamiczne i współpracy B2B usługi Azure Active Directory](use-dynamic-groups.md).

## <a name="add-guest-users-to-an-application"></a>Dodawanie użytkowników-gości do aplikacji

Aby dodać użytkowników we współpracy B2B do aplikacji jako administrator usługi Azure AD, wykonaj następujące kroki:

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) jako administrator usługi Azure AD.
2. W okienku nawigacji wybierz **usługi Azure Active Directory**.
3. W obszarze **Zarządzaj**, wybierz opcję **aplikacje dla przedsiębiorstw** > **wszystkie aplikacje**.
4. Wybierz aplikację, do której chcesz dodać użytkowników-gości.
5. Na pulpicie nawigacyjnym aplikacji, wybierz **całkowitej liczby użytkowników** otworzyć **użytkowników i grup** okienka.

    ![Łączna liczba użytkowników przycisk, aby dodać, otwórz przystawkę Użytkownicy i grupy](./media/add-users-administrator/AppUsersAndGroups.png)

6. Wybierz przycisk **Dodaj użytkownika**.
7. W obszarze **Dodaj przydziału**, wybierz opcję **użytkowników i grup**.
8. Wykonaj jedną z następujących czynności:
   - Jeśli użytkownik-Gość już istnieje w katalogu, należy wyszukać użytkownika B2B. Wybierz użytkownika, kliknij przycisk **wybierz**, a następnie kliknij przycisk **przypisać** dodać użytkownika do aplikacji.
   - Jeśli użytkownik-Gość nie istnieje w katalogu, w obszarze **Wybierz członka lub Zaproś użytkownika zewnętrznego**, wpisz adres e-mail użytkownika. W oknie komunikatu wpisz opcjonalny osobistą wiadomość. W oknie komunikatu kliknij **zaprosić**.
           
       ![Dodaj przycisk zaproszenie, aby dodać elementy członkowskie gościa](./media/add-users-administrator/AppInviteUsers.png)
   
      Kliknij przycisk **wybierz**, a następnie kliknij przycisk **przypisać** dodać użytkownika do aplikacji. Zaproszenie automatycznie trafia do zaproszonego użytkownika.

9. Użytkownik Gość zostanie wyświetlony w aplikacji **użytkowników i grup** listy przypisanej roli usługi **domyślnego dostępu**. Jeśli chcesz zmienić tę rolę, wykonaj następujące czynności:
   - Wybierz użytkownika gościa, a następnie wybierz **Edytuj**. 
   - W obszarze **edytować przypisania**, kliknij przycisk **wybierz rolę**i wybierz rolę, którą chcesz przypisać do wybranego użytkownika.
   - Kliknij pozycję **Wybierz**.
   - Kliknij przycisk **Przypisz**.
 
## <a name="resend-invitations-to-guest-users"></a>Ponownie wysłać zaproszenia użytkowników-gości

Jeśli użytkownik-Gość nie ma jeszcze zrealizować zaproszenia, można ponownie wysłać wiadomość e-mail z zaproszeniem.

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) jako administrator usługi Azure AD.
2. W okienku nawigacji wybierz **usługi Azure Active Directory**.
3. W obszarze **Zarządzaj** wybierz pozycję **Użytkownicy**.
5. Wybierz konto użytkownika.
6. W obszarze **Zarządzaj**, wybierz opcję **profilu**.
7. Jeśli użytkownik nie zaakceptował jeszcze zaproszenia, **Wyślij ponownie zaproszenie** opcja jest dostępna. Wybierz ten przycisk, aby wysłać ponownie.

   ![Wyślij ponownie zaproszenie opcję w profilu użytkownika](./media/add-users-administrator/Resend-Invitation.png)

> [!NOTE]
> Można ponownie wysłać zaproszenie, który pierwotnie przekierowanie użytkownika do konkretnej aplikacji, Dowiedz się, że link w nowe zaproszenie powoduje otwarcie do najwyższego poziomu panelu dostępu zamiast tego.

## <a name="next-steps"></a>Kolejne kroki

- Aby dowiedzieć się, jak dodać użytkowników-gości B2B Administratorzy spoza platformy Azure AD, zobacz [jak pracownicy przetwarzający informacje mogą dodać użytkowników we współpracy B2B?](add-users-information-worker.md)
- Aby uzyskać informacje na temat wiadomości e-mail z zaproszeniem, zobacz [elementy wiadomości e-mail z zaproszeniem współpracy B2B](invitation-email-elements.md).

