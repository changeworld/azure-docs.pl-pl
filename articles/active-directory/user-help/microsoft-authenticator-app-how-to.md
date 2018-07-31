---
title: Rozpoczynanie pracy z aplikacją Microsoft Authenticator — usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Dowiedz się, jak uaktualnić do najnowszej wersji programu Microsoft Authenticator.
services: active-directory
author: eross-msft
manager: mtillman
ms.assetid: 3065a1ee-f253-41f0-a68d-2bd84af5ffba
ms.service: active-directoary
ms.workload: identity
ms.component: user-help
ms.topic: conceptual
ms.date: 07/30/2018
ms.author: lizross
ms.reviewer: librown
ms.openlocfilehash: 8afd743f6010822709bf3d49f7a3bbcd51b19edf
ms.sourcegitcommit: 30fd606162804fe8ceaccbca057a6d3f8c4dd56d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/30/2018
ms.locfileid: "39346491"
---
# <a name="get-started-with-the-microsoft-authenticator-app"></a>Rozpoczynanie pracy z aplikacją Microsoft Authenticator

Aplikacja Microsoft Authenticator pomagają zapobiegać nieautoryzowanemu dostępowi do kont i zatrzymania fałszywe transakcje dzięki zapewnieniu dodatkowego poziomu zabezpieczeń dla Twojej pracy konta służbowego (na przykład alain@contoso.com) lub osobistego konta Microsoft (w przypadku przykład alain@outlook.com). Możesz użyć go jako drugi metodę weryfikacji lub zastąpienie o podanie hasła przy użyciu logowanie za pomocą telefonu. Aby uzyskać więcej informacji na temat sposobu konfigurowania i używania logowanie za pomocą telefonu bez hasła, zobacz [Zaloguj się przy użyciu telefonu, nie hasła](microsoft-authenticator-app-phone-signin-faq.md).

Podczas korzystania z aplikacji, podczas weryfikacji dwuetapowej, pracy w jeden z dwóch sposobów:

- **Powiadomienie.** Aplikacja wysyła powiadomienie do Twojego urządzenia. Upewnij się, powiadomienia jest odpowiednie, a następnie wybierz **Sprawdź**. Jeśli nie rozpoznajesz powiadomienia wybierz **Odmów**.

- **Kod weryfikacyjny.** Po wpisaniu nazwy użytkownika i hasła, możesz otworzyć aplikację i skopiować kod weryfikacyjny na **kont** ekranu na ekranie logowania. Kod weryfikacyjny, który działa jako drugiej formy uwierzytelniania.

## <a name="opt-in-for-two-step-verification"></a>Zgoda na weryfikację dwuetapową

Twoja organizacja decyduje o tym, czy używać weryfikacji dwuetapowej za pomocą konta służbowego lub szkolnego. Administrator zostanie wyświetlone powiadomienie, które metody weryfikacji, które są potrzebne do konfigurowania i używania. Aby uzyskać więcej informacji, zobacz [co usługi Azure Multi-Factor Authentication oznacza dla mnie](multi-factor-authentication-end-user.md).

Dla Twojego osobistego konta Microsoft należy zdefiniować weryfikację dwuetapową dla siebie. Aby uzyskać szczegółowe informacje i instrukcje, zobacz [o weryfikacji dwuetapowej](https://support.microsoft.com/help/12408/microsoft-account-about-two-step-verification).

Aplikacja Microsoft Authenticator umożliwia również za pomocą kont innych niż Microsoft. Te konta mogą wywołać funkcję coś innego niż weryfikacji dwuetapowej, ale można go znaleźć w ramach zabezpieczeń lub ustawienia logowania. Aby uzyskać więcej informacji o sposobie konfigurowania tych kont innych niż Microsoft, zobacz [wideo obsługi klienta firmy Microsoft](https://www.youtube.com/playlist?list=PLyhj1WZ29G65QdD9NxTOAm8HwOS-OBUrX).

## <a name="install-the-app"></a>Zainstaluj aplikację

Aplikacja Microsoft Authenticator jest dostępna dla systemów [Android](https://go.microsoft.com/fwlink/?linkid=866594), [iOS](https://go.microsoft.com/fwlink/?linkid=866594) i [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071). Aby uzyskać najlepsze wyniki, należy udostępnić aplikację, Otrzymuj powiadomienia, gdy pojawi się prośba. 

## <a name="add-accounts-to-the-app"></a>Dodaj konta do aplikacji

Możesz dodać pracy lub konta służbowego lub konta osobiste do aplikacji Microsoft Authenticator. 

### <a name="add-a-personal-microsoft-account"></a>Dodaj osobiste konto Microsoft

W przypadku osobistego konta Microsoft (jedno, którego używasz do logowania do usługi Outlook.com, Xbox, Skype, itp.) jest wszystko, co należy zrobić, zaloguj się do swojego konta w aplikacji Microsoft Authenticator.

### <a name="add-a-work-or-school-account"></a>Dodaj konto służbowe

1. Jeśli to możliwe, przejdź do [dodatkowej weryfikacji zabezpieczeń](http://aka.ms/mfasetup) ekranu na innym komputerze lub urządzeniu. Aby dowiedzieć się, jak uzyskać dostęp do tego ekranu, zobacz [zmiana ustawień zabezpieczeń](multi-factor-authentication-end-user-manage-settings.md#where-to-find-the-settings-page) lub skontaktuj się z administratorem.

    >[!Note]
    >Jeśli administrator wyłączył środowisko podglądu tak zabezpieczeń informacji, możesz wykonać zgodnie z instrukcjami w [ustawić informacje zabezpieczające, aby użyć aplikacji authenticator](security-info-setup-auth-app.md) sekcji.

2. Zaznacz pole wyboru obok pozycji **aplikacji Authenticator**, a następnie wybierz pozycję **Konfiguruj**.

    ![Przycisk Konfiguruj, na ekranie ustawień weryfikacji zabezpieczeń](./media/microsoft-authenticator-app-how-to/auth-app-configure.png)

    **Konfiguracja aplikacji mobilnej** pojawi się ekran z kodem QR umożliwia skanowanie za pomocą aplikacji wystawcy uwierzytelnienia.

    ![Ekran, który zawiera kod QR](./media/microsoft-authenticator-app-how-to/auth-app-barcode.png)

3. Otwórz aplikację Microsoft Authenticator. Na **kont** ekranu, wybierz opcję **Dodaj konto**, a następnie wybierz pozycję **pracy konta służbowego**.

4. Użyj aparatu urządzenia, aby zeskanować kod QR, a następnie wybierz **gotowe** aby zamknąć ekran kodu QR.

    >[!Note]
    >Jeśli aparat fotograficzny nie działa prawidłowo, możesz to zrobić [ręcznie wprowadzić kod QR i adres URL](#add-an-account-to-the-app-manually).

    **Kont** ekranu aplikacji dowiesz się, nazwę konta i 6 cyfrowy kod weryfikacyjny. Dla dodatkowego bezpieczeństwa kod weryfikacyjny zmienia co 30 sekund, które uniemożliwiają korzystanie dwa razy ten sam kod.  

    ![ekran kont](./media/microsoft-authenticator-app-how-to/auth-app-accounts.png)

### <a name="add-an-account-to-the-app-manually"></a>Ręcznie dodaj konto do aplikacji

1. Przejdź do **dodatkowej weryfikacji zabezpieczeń** ekranu. Aby uzyskać informacje, jak uzyskać dostęp do tego ekranu, zobacz [zmiana ustawień zabezpieczeń](multi-factor-authentication-end-user-manage-settings.md#where-to-find-the-settings-page).

2. Zaznacz pole wyboru obok pozycji **aplikacji Authenticator**, a następnie wybierz pozycję **Konfiguruj**.

    **Konfiguracja aplikacji mobilnej** pojawi się ekran.

3. Skopiuj kod i adres URL informacji z **Konfiguracja aplikacji mobilnej** ekranu, dzięki czemu może ręcznie wpisać do skanera kodów QR.

4. Otwórz aplikację Microsoft Authenticator. Na **kont** ekranu, wybierz opcję **Dodaj konto**, a następnie wybierz pozycję **pracy konta służbowego**.

5. Na ekranie skanera kodów QR wybierz **wprowadź kod ręcznie**.

    ![Ekran skanowanie kodu QR](./media/microsoft-authenticator-app-how-to/auth-app-manual-code.png)
   
6. Wpisz kod i adres URL z ekranu przy użyciu kodu QR w **Dodaj konto** ekranu, a następnie wybierz pozycję **Zakończ**.

    ![Ekran do wprowadzania kod i adres URL](./media/microsoft-authenticator-app-how-to/auth-app-code-url.png)

    **Kont** ekranu aplikacji dowiesz się, nazwę konta i 6 cyfrowy kod weryfikacyjny. Dla dodatkowego bezpieczeństwa kod weryfikacyjny zmienia co 30 sekund, które uniemożliwiają korzystanie dwa razy ten sam kod.

### <a name="using-your-devices-fingerprint-or-facial-recognition-capabilities"></a>Za pomocą odcisku palca lub rozpoznawanie twarzy możliwości urządzenia

Twoja organizacja może wymagać numeru PIN w celu ukończenia weryfikacji tożsamości. Można skonfigurować aplikację Microsoft Authenticator, aby użyć urządzenia możliwości rozpoznawanie twarzy lub odcisku palca zamiast numeru PIN. Należy wybrać tę opcję po raz pierwszy używasz aplikacji wystawcy uwierzytelnienia do zweryfikowania konta, wybierając opcję używania urządzeniami biometrycznych jako identyfikator zamiast numeru PIN.

## <a name="use-the-app-when-you-sign-in"></a>Po zalogowaniu przy użyciu aplikacji

Po dodaniu konta do aplikacji, można użyć aplikacji do logowania do konta.

Jeśli została wybrana opcja używania kodów weryfikacyjnych w aplikacji, użytkownik rozpoczyna się mogli je widzieć na **kont** strony. Kody zmienić co 30 sekund, aby zawsze mieć nowy kod gdy będą potrzebne. Ale nie trzeba podejmować żadnych działań, które z nich, dopóki Zaloguj się i monit o wprowadzenie kodu weryfikacyjnego.

## <a name="next-steps"></a>Kolejne kroki

- Jeśli masz więcej ogólne pytania dotyczące aplikacji, zobacz [Microsoft Authenticator często zadawane pytania](microsoft-authenticator-app-faq.md)

- Jeśli masz pytania dotyczące sposobu używania logowanie telefonem, zobacz [Zaloguj się przy użyciu telefonu, nie hasła](microsoft-authenticator-app-phone-signin-faq.md)

- Jeśli chcesz, aby dowiedzieć się więcej o weryfikację dwuetapową, zobacz [Skonfiguruj moje konto na potrzeby weryfikacji dwuetapowej](multi-factor-authentication-end-user-first-time.md)

- Jeśli chcesz, aby dowiedzieć się więcej o informacje o zabezpieczeniach, zobacz [Zarządzanie swoje informacje zabezpieczające](security-info-manage-settings.md)
