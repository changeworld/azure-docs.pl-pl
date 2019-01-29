---
title: 'Szybki start: Dodawanie użytkowników-gości w witrynie Azure Portal'
description: Korzystając z tego przewodnika Szybki start, możesz dowiedzieć się, jak administratorzy usługi Azure AD mogą dodawać użytkowników-gości B2B w witrynie Azure Portal, oraz zapoznać się z przepływem pracy zaproszenia B2B.
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: quickstart
ms.date: 07/02/2018
ms.author: mimart
author: msmimart
manager: daveba
ms.reviewer: sasubram
ms.openlocfilehash: 5e62d8679a76c024b0dcada5cfaad50659fcd4c8
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/22/2019
ms.locfileid: "54431694"
---
# <a name="quickstart-add-guest-users-to-your-directory-in-the-azure-portal"></a>Szybki start: dodawanie użytkowników-gości do katalogu w witrynie Azure Portal

Dodając dowolną osobę do Twojego katalogu jako użytkownika-gościa, możesz zaprosić ją do współpracy z Twoją organizacją. Następnie możesz wysłać wiadomość e-mail z zaproszeniem zawierającym link do realizacji lub wysłać bezpośredni link do aplikacji, którą chcesz udostępnić. Użytkownicy-goście mogą zalogować się za pomocą własnych tożsamości służbowych lub społecznościowych.

W tym przewodniku Szybki start dodasz do usługi Azure AD nowego użytkownika-gościa, wyślesz zaproszenie i zobaczysz, jak wygląda proces realizacji zaproszenia użytkownika-gościa.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia scenariusza z tego samouczka są potrzebne następujące elementy:

 - Rola umożliwiająca tworzenie użytkowników w Twoim katalogu dzierżawy, na przykład rola administratora globalnego lub dowolna spośród ról administratora katalogu z ograniczonymi uprawnieniami.
 - Ważne konto e-mail, które możesz dodać do swojego katalogu dzierżawy i którego możesz użyć do odebrania wiadomości e-mail z zaproszeniem testowym.

## <a name="add-a-new-guest-user-in-azure-ad"></a>Dodawanie nowego użytkownika-gościa w usłudze Azure AD

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com/) jako administrator usługi Azure AD.
2. W lewym okienku wybierz pozycję **Azure Active Directory**.
3.  W obszarze **Zarządzaj** wybierz pozycję **Użytkownicy**.

    ![Wybierz pozycję Azure Active Directory](media/quickstart-add-users-portal/quickstart-users-portal-user.png)

4.  Wybierz pozycję **Nowy użytkownik-gość**.

    ![Wybierz pozycję Azure Active Directory](media/quickstart-add-users-portal/quickstart-users-portal-user-3.png)

5.  W obszarze **Nazwa użytkownika** wprowadź adres e-mail użytkownika zewnętrznego. W obszarze **Dołącz osobistą wiadomość do zaproszenia** wpisz wiadomość powitalną. 

    ![Wybierz pozycję Azure Active Directory](media/quickstart-add-users-portal/quickstart-users-portal-user-4.png)

6. Wybierz pozycję **Zaproś**, aby automatycznie wysłać zaproszenie do użytkownika-gościa. W prawym górnym rogu zostanie wyświetlone powiadomienie z komunikatem **Pomyślnie zaproszono użytkownika**. 
7.  Po wysłaniu zaproszenia konto użytkownika zostanie automatycznie dodane do katalogu jako gość.

## <a name="assign-an-app-to-the-guest-user"></a>Przypisywanie aplikacji do użytkownika-gościa
Dodaj aplikację Salesforce do swojej dzierżawy testowej i przypisz testowego użytkownika-gościa do aplikacji.
1.  Zaloguj się do witryny Azure Portal jako administrator usługi Azure AD.
2.  W lewym okienku wybierz pozycję **Aplikacje dla przedsiębiorstw**.
3.  Wybierz pozycję **Nowa aplikacja**.
4. W obszarze **Dodaj z galerii** wyszukaj pozycję **Salesforce**, a następnie wybierz ją.

    ![Wybierz pozycję Azure Active Directory](media/quickstart-add-users-portal/quickstart-users-portal-select-salesforce.png)
5. Wybierz pozycję **Dodaj**.
6. W obszarze **Zarządzaj** wybierz pozycję **Logowanie jednokrotne**, a w obszarze **Tryb logowania jednokrotnego** wybierz pozycję **Logowanie oparte na haśle** i kliknij przycisk  **Zapisz**.
7. W obszarze **Zarządzaj** wybierz pozycję **Użytkownicy i grupy** > **Dodaj użytkownika** > **Użytkownicy i grupy**.
8. Za pomocą pola wyszukiwania wyszukaj użytkownika testowego (jeśli to konieczne) i wybierz go z listy. Następnie kliknij pozycję **Wybierz**.
9. Wybierz opcję **Przypisz**. 

## <a name="accept-the-invitation"></a>Akceptowanie zaproszenia
Teraz zaloguj się jako użytkownik-gość, aby zobaczyć zaproszenie.
1.  Zaloguj się do konta e-mail testowego użytkownika-gościa.
2.  W skrzynce odbiorczej znajdź wiadomość e-mail „Zaproszenie”.

    ![Wiadomość e-mail z zaproszeniem B2B](media/quickstart-add-users-portal/quickstart-users-portal-email-small.png)

3.  W treści wiadomości e-mail wybierz pozycję **Rozpocznij**. W przeglądarce zostanie otwarta strona **Przejrzyj uprawnienia**. 

    ![Strona akceptowania zaproszenia B2B](media/quickstart-add-users-portal/quickstart-users-portal-accept.png)

4. Wybierz pozycję **Zaakceptuj**. Zostanie otwarty Panel dostępu zawierający listę aplikacji, do których może uzyskać dostęp użytkownik-gość.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów
Usuń testowego użytkownika-gościa i aplikację testową, gdy przestaną być potrzebne.
1.  Zaloguj się do witryny Azure Portal jako administrator usługi Azure AD.
2.  W lewym okienku wybierz pozycję **Azure Active Directory**.
3.  W obszarze **Zarządzaj** wybierz pozycję **Aplikacje dla przedsiębiorstw**.
4.  Otwórz aplikację **Salesforce**, a następnie wybierz pozycję **Usuń**.
5.  W lewym okienku wybierz pozycję **Azure Active Directory**.
6.  W obszarze **Zarządzaj** wybierz pozycję **Użytkownicy**.
7.  Wybierz użytkownika testowego, a następnie wybierz pozycję **Usuń użytkownika**.

## <a name="next-steps"></a>Następne kroki
W tym samouczku utworzyliśmy użytkownika-gościa w witrynie Azure Portal i wysłaliśmy zaproszenie do udostępnienia aplikacji. Następnie zobaczyliśmy proces realizacji z punktu widzenia użytkownika-gościa i sprawdziliśmy, czy aplikacja pojawiła się na jego Panelu dostępu. Aby dowiedzieć się więcej na temat dodawania użytkowników-gości do współpracy, zobacz [Dodawanie użytkowników współpracy usługi Azure Active Directory B2B w witrynie Azure Portal](add-users-administrator.md).