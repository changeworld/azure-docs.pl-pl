---
title: Jak zarządzać hasłami aplikacji — usługa Azure Active Directory | Dokumenty firmy Microsoft
description: Dowiedz się więcej o hasłach do aplikacji i o tym, do czego służą w przypadku weryfikacji dwuetapowej.
services: active-directory
author: curtand
manager: daveba
ms.reviewer: richagi
ms.assetid: 345b757b-5a2b-48eb-953f-d363313be9e5
ms.workload: identity
ms.service: active-directory
ms.subservice: user-help
ms.topic: conceptual
ms.date: 08/13/2019
ms.author: curtand
ms.custom: user-help, seo-update-azuread-jan
ms.openlocfilehash: 5f81181ac3107307a352cdbcd0b5cc4a555deacb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79253223"
---
# <a name="manage-app-passwords-for-two-step-verification"></a>Zarządzanie hasłami aplikacji do weryfikacji dwuetapowej

>[!Important]
>Administrator może nie zezwolić na używanie haseł aplikacji. Jeśli nie widzisz **opcji haseł aplikacji,** nie są one dostępne w Twojej organizacji.

Podczas korzystania z haseł aplikacji należy pamiętać:

- Hasła aplikacji są generowane automatycznie i powinny być tworzone i wprowadzane raz na aplikację.

- Istnieje limit 40 haseł na użytkownika. Jeśli spróbujesz utworzyć go po tym limicie, zostanie wyświetlony monit o usunięcie istniejącego hasła, zanim zostanie wyświetlony możliwość utworzenia nowego.

    >[!Note]
    >Klienci pakietu Office 2013 (w tym program Outlook) obsługują nowe protokoły uwierzytelniania i mogą być używane z weryfikacją dwuetapową. Ta obsługa oznacza, że po włączeniu weryfikacji dwuetapowej nie będziesz już potrzebować haseł aplikacji dla klientów pakietu Office 2013. Aby uzyskać więcej informacji, zobacz [artykuł Jak działa nowoczesne uwierzytelnianie dla pakietu Office 2013 i aplikacji klienckich pakietu Office 2016.](https://support.office.com/article/how-modern-authentication-works-for-office-2013-and-office-2016-client-apps-e4c45989-4b1a-462e-a81b-2a13191cf517)

## <a name="create-new-app-passwords"></a>Tworzenie nowych haseł aplikacji

Podczas początkowego procesu weryfikacji dwuskładnikowej otrzymasz jedno hasło do aplikacji. Jeśli potrzebujesz więcej niż jednego, musisz utworzyć je samodzielnie. Hasła aplikacji można tworzyć z wielu obszarów, w zależności od konfiguracji weryfikacji dwuskładnikowej w organizacji. Aby uzyskać więcej informacji na temat rejestrowania się w celu korzystania z weryfikacji dwuskładnikowej na koncie służbowym, zobacz [Omówienie weryfikacji dwuskładnikowej oraz konta służbowego i](multi-factor-authentication-end-user-first-time.md) powiązanych z nim artykułów.

### <a name="where-to-create-and-delete-your-app-passwords"></a>Gdzie tworzyć i usuwać hasła aplikacji

Możesz tworzyć i usuwać hasła aplikacji na podstawie sposobu korzystania z weryfikacji dwuskładnikowej:

- **Organizacja korzysta z weryfikacji dwuskładnikowej i strony Dodatkowa weryfikacja zabezpieczeń.** Jeśli korzystasz z konta służbowego (np. alain@contoso.com) z weryfikacją dwuskładnikową w organizacji, możesz zarządzać hasłami aplikacji na [stronie Dodatkowa weryfikacja zabezpieczeń](https://account.activedirectory.windowsazure.com/Proofup.aspx). Aby uzyskać szczegółowe instrukcje, zobacz [Tworzenie i usuwanie haseł aplikacji przy użyciu strony Dodatkowa weryfikacja zabezpieczeń](#create-and-delete-app-passwords-from-the-additional-security-verification-page) w tym artykule.

- **Twoja organizacja korzysta z weryfikacji dwuskładnikowej i portalu usługi Office 365.** Jeśli używasz konta służbowego (np. alain@contoso.com), weryfikacji dwuskładnikowej i aplikacji usługi Office 365 w organizacji, możesz zarządzać hasłami aplikacji na [stronie portalu usługi Office 365](https://www.office.com). Aby uzyskać szczegółowe instrukcje, zobacz [Tworzenie i usuwanie haseł aplikacji przy użyciu portalu usługi Office 365](#create-and-delete-app-passwords-using-the-office-365-portal) w tym artykule.

- **Używasz weryfikacji dwuskładnikowej przy użyciu osobistego konta Microsoft.** Jeśli używasz osobistego konta Microsoft (np. alain@outlook.com) z weryfikacją dwuskładnikową, możesz zarządzać hasłami aplikacji na stronie Podstawowe [zabezpieczenia](https://account.microsoft.com/security/). Aby uzyskać szczegółowe instrukcje, zobacz [Używanie haseł aplikacji z aplikacjami, które nie obsługują weryfikacji dwuetapowej.](https://support.microsoft.com/help/12409/microsoft-account-app-passwords-and-two-step-verification)

## <a name="create-and-delete-app-passwords-from-the-additional-security-verification-page"></a>Tworzenie i usuwanie haseł aplikacji ze strony Dodatkowa weryfikacja zabezpieczeń

Możesz tworzyć i usuwać hasła aplikacji ze strony **Dodatkowa weryfikacja zabezpieczeń** dla konta służbowego.

1. Zaloguj się na [stronie Dodatkowa weryfikacja zabezpieczeń,](https://account.activedirectory.windowsazure.com/Proofup.aspx)a następnie wybierz pozycję **Hasła aplikacji**.

    ![Strona Hasła aplikacji z wyróżnioną kartą Hasła aplikacji](media/multi-factor-authentication-end-user-app-passwords/mfa-app-passwords-page.png)

2. Wybierz **pozycję Utwórz**, wpisz nazwę aplikacji, która wymaga hasła aplikacji, a następnie wybierz pozycję **Dalej**.

    ![Tworzenie strony haseł aplikacji z nazwą aplikacji, która wymaga hasła](media/multi-factor-authentication-end-user-app-passwords/mfa-create-app-password-page.png)

3. Skopiuj hasło ze strony **Hasło aplikacji,** a następnie wybierz pozycję **Zamknij**.

    ![Strona hasła aplikacji z hasłem dla określonej aplikacji](media/multi-factor-authentication-end-user-app-passwords/mfa-your-app-password-page.png)

4. Na stronie **Hasła aplikacji** upewnij się, że aplikacja znajduje się na liście.

     ![Strona hasła aplikacji z nową aplikacją wyświetlaną na liście](media/multi-factor-authentication-end-user-app-passwords/mfa-app-passwords-page-with-new-password.png)  

5. Otwórz aplikację, dla której utworzono hasło aplikacji (na przykład Outlook 2010), a następnie wklej hasło aplikacji, gdy zostanie o to poproszony. Należy to zrobić tylko raz na aplikację.

### <a name="to-delete-an-app-password-using-the-app-passwords-page"></a>Aby usunąć hasło aplikacji na stronie Hasła aplikacji

1. Na stronie **Hasła aplikacji** wybierz pozycję **Usuń** obok hasła aplikacji, które chcesz usunąć.

   ![Usuwanie hasła aplikacji](media/multi-factor-authentication-end-user-app-passwords/mfa-app-passwords-page-delete.png)

2. Wybierz **pozycję Tak,** aby potwierdzić, że chcesz usunąć hasło, a następnie wybierz pozycję **Zamknij**.

    Hasło aplikacji zostało pomyślnie usunięte.

## <a name="create-and-delete-app-passwords-using-the-office-365-portal"></a>Tworzenie i usuwanie haseł aplikacji za pomocą portalu usługi Office 365

Jeśli korzystasz z weryfikacji dwuetapowej z kontem służbowym i aplikacjami usługi Office 365, możesz utworzyć i usunąć hasła aplikacji za pomocą portalu usługi Office 365.

### <a name="to-create-app-passwords-using-the-office-365-portal"></a>Aby utworzyć hasła aplikacji przy użyciu portalu usługi Office 365

1. Zaloguj się do usługi Office 365, a następnie przejdź do [strony Moje konto](https://portal.office.com), wybierz pozycję Zabezpieczenia & **prywatność**, a następnie rozwiń pozycję **Dodatkowa weryfikacja zabezpieczeń**.

    ![Portal pakietu Office z rozszerzonym dodatkowym obszarem weryfikacji zabezpieczeń](media/multi-factor-authentication-end-user-app-passwords/mfa-app-passwords-o365-my-account-page.png)

2. Zaznacz tekst, który mówi, **Tworzenie haseł aplikacji i zarządzanie nimi,** aby otworzyć stronę Hasła **aplikacji.**

    ![Strona Hasła aplikacji z wyróżnioną kartą Hasła aplikacji](media/multi-factor-authentication-end-user-app-passwords/mfa-app-passwords-page.png)

3. Wybierz **pozycję Utwórz**, wpisz nazwę aplikacji, która wymaga hasła aplikacji, a następnie wybierz pozycję **Dalej**.

    ![Tworzenie strony haseł aplikacji z nazwą aplikacji, która wymaga hasła](media/multi-factor-authentication-end-user-app-passwords/mfa-create-app-password-page.png)

4. Skopiuj hasło ze strony **Hasło aplikacji,** a następnie wybierz pozycję **Zamknij**.

    ![Strona hasła aplikacji z hasłem dla określonej aplikacji](media/multi-factor-authentication-end-user-app-passwords/mfa-your-app-password-page.png)

5. Na stronie **Hasła aplikacji** upewnij się, że aplikacja znajduje się na liście.

     ![Strona hasła aplikacji z nową aplikacją wyświetlaną na liście](media/multi-factor-authentication-end-user-app-passwords/mfa-app-passwords-page-with-new-password.png)  

6. Otwórz aplikację, dla której utworzono hasło aplikacji (na przykład Outlook 2010), a następnie wklej hasło aplikacji, gdy zostanie o to poproszony. Należy to zrobić tylko raz na aplikację.

### <a name="to-delete-app-passwords-using-the-app-passwords-page"></a>Aby usunąć hasła aplikacji na stronie Hasła aplikacji

1. Na stronie **Hasła aplikacji** wybierz pozycję **Usuń** obok hasła aplikacji, które chcesz usunąć.

   ![Usuwanie hasła aplikacji](media/multi-factor-authentication-end-user-app-passwords/mfa-app-passwords-page-delete.png)

2. W polu potwierdzenia wybierz **pozycję Tak,** a następnie wybierz pozycję **Zamknij**.

    Hasło aplikacji zostało pomyślnie usunięte.

## <a name="if-your-app-passwords-arent-working-properly"></a>Jeśli hasła aplikacji nie działają prawidłowo

Upewnij się, że hasło zostało wpisane poprawnie. Jeśli masz pewność, że hasło zostało wprowadzone poprawnie, możesz spróbować zalogować się ponownie i utworzyć nowe hasło aplikacji. Jeśli żadna z tych opcji nie rozwiąże problemu, skontaktuj się z działem pomocy technicznej organizacji, aby usunąć istniejące hasła aplikacji, umożliwiając tworzenie zupełnie nowych.

## <a name="next-steps"></a>Następne kroki

- [Zarządzanie ustawieniami weryfikacji dwuetapowej](multi-factor-authentication-end-user-manage-settings.md)

- Wypróbuj [aplikację Microsoft Authenticator,](user-help-auth-app-download-install.md) aby zweryfikować logowanie za pomocą powiadomień aplikacji, zamiast otrzymywać smsy lub połączenia.
