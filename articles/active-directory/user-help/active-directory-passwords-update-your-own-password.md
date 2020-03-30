---
title: Resetowanie hasła przy użyciu informacji zabezpieczających — usługa Azure Active Directory | Dokumenty firmy Microsoft
description: Jak zresetować własne hasło, jeśli go zapomnisz, korzystając z informacji zabezpieczających i weryfikacji dwuetapowej.
services: active-directory
author: curtand
manager: daveba
ms.reviewer: sahenry
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 01/15/2020
ms.author: curtand
ms.openlocfilehash: 3ed79be318319009aabb1b1ef0c42c4021bbbabe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77062663"
---
# <a name="reset-your-work-or-school-password-using-security-info"></a>Resetowanie hasła służbowego lub szkolnego przy użyciu informacji zabezpieczających

Jeśli nie pamiętasz hasła służbowego lub szkolnego, nigdy nie otrzymałeś hasła od organizacji lub nie masz konta, możesz użyć informacji zabezpieczających i urządzenia mobilnego, aby zresetować hasło służbowe lub szkolne. Administrator musi włączyć tę funkcję, aby móc skonfigurować informacje i zresetować własne hasło.

Jeśli znasz hasło, ale chcesz go zmienić, zobacz sekcje [Kroki zmieniania hasła](https://docs.microsoft.com/azure/active-directory/user-help/active-directory-passwords-update-your-own-password#how-to-change-your-password) w tym artykule.

>[!Important]
>Ten artykuł jest przeznaczony dla użytkowników próbujących użyć hasła do konta zapomnianego lub nieznanego. Jeśli jesteś administratorem szukającym informacji o tym, jak włączyć samoobsługowe resetowanie hasła dla pracowników lub innych użytkowników, zobacz [resetowanie hasła samoobsługowego usługi Azure AD i inne artykuły](https://docs.microsoft.com/azure/active-directory/authentication/howto-sspr-deployment).

## <a name="how-to-reset-or-unlock-your-password-for-a-work-or-school-account"></a>Jak zresetować lub odblokować hasło do konta służbowego

Jeśli nie możesz uzyskać dostępu do konta usługi Azure Active Directory (Azure AD), może to być spowodowane:

- Hasło nie działa i chcesz go zresetować, lub

- Znasz swoje hasło, ale Twoje konto jest zablokowane i musisz je odblokować.

### <a name="to-reset-your-password-and-get-back-into-your-account"></a>Aby zresetować hasło i wrócić na konto

1. Na ekranie **Wprowadź hasło** wybierz pozycję **Nie pamiętasz hasła**.

2. Na ekranie **Wróć do konta** wpisz **służbowy** lub szkolny identyfikator użytkownika (na przykład adres e-mail), udowodnij, że nie jesteś robotem, wprowadzając znaki widoczne na ekranie, a następnie wybierz przycisk **Dalej**.

   ![Powrót do ekranu konta](media/security-info/security-info-back-into-acct.png)

   >[!NOTE]
   >Jeśli administrator nie włączył możliwości resetowania własnego hasła, zamiast ekranu **Wróć do konta** zobaczysz łącze Skontaktuj się z **administratorem.** Ten link umożliwia skontaktowanie się z administratorem w sprawie zresetowania hasła za pośrednictwem poczty e-mail lub portalu internetowego.

3. Wybierz jedną z następujących metod, aby zweryfikować swoją tożsamość i zmienić hasło. W zależności od tego, jak administrator skonfigurował organizację, może być konieczne przejście przez ten proces po raz drugi, dodając informacje o drugim kroku weryfikacji.

    ![Wróć na swoje konto, etap weryfikacji #1](media/security-info/security-info-back-into-acct2.png)

    >[!NOTE]
    >W zależności od tego, jak administrator skonfigurował organizację, niektóre z tych opcji weryfikacji mogą być niedostępne. Urządzenie przenośne musi wcześniej skonfigurować do weryfikacji przy użyciu co najmniej jednej z tych metod.<br><br>Ponadto nowe hasło może wymagać spełnienia określonych wymagań dotyczących siły. Silne hasła mają zazwyczaj od 8 do 16 znaków, w tym wielkie i małe litery, co najmniej jedną liczbę i co najmniej jeden znak specjalny.

- **Zresetuj hasło przy użyciu adresu e-mail.** Wysyła wiadomość e-mail na adres e-mail skonfigurowany wcześniej w weryfikacji dwuetapowej lub informacjach zabezpieczających. Jeśli administrator włączył informacje zabezpieczające, więcej informacji o konfigurowaniu adresu e-mail można znaleźć w artykule [Konfigurowanie informacji zabezpieczających do używania poczty e-mail (wersja zapoznawcza).](security-info-setup-email.md) Jeśli nie używasz jeszcze informacji zabezpieczających, więcej informacji na temat konfigurowania adresu e-mail można znaleźć w artykule [Konfigurowanie konta dla weryfikacji dwuetapowej.](multi-factor-authentication-end-user-first-time.md) 

    1. Wybierz **pozycję Wyślij wiadomość e-mail do mojej alternatywnej wiadomości e-mail,** a następnie wybierz pozycję Wyślij wiadomość **e-mail**.

    2. Wpisz kod weryfikacyjny z wiadomości e-mail w polu, a następnie wybierz przycisk **Dalej**.

    3. Wpisz i potwierdź nowe hasło, a następnie wybierz pozycję **Zakończ**.

- **Zresetuj hasło za pomocą wiadomości tekstowej.** Wysyła wiadomość tekstową na numer telefonu, który został wcześniej skonfigurowany w informacjach zabezpieczających. Jeśli administrator włączył środowisko informacji zabezpieczających, więcej informacji na temat konfigurowania wiadomości tekstowych można znaleźć w artykule [Konfigurowanie informacji zabezpieczających do używania wiadomości tekstowych (wersja zapoznawcza).](security-info-setup-text-msg.md) Jeśli nie używasz jeszcze informacji zabezpieczających, więcej informacji na temat konfigurowania wiadomości tekstowych można znaleźć w artykule [Konfigurowanie konta do weryfikacji dwuetapowej.](multi-factor-authentication-end-user-first-time.md)

    1. Wybierz **pozycję Tekst telefonu komórkowego**, wpisz swój numer telefonu, a następnie wybierz pozycję **Tekst**.

    2. Wpisz kod weryfikacyjny z wiadomości tekstowej w polu, a następnie wybierz przycisk **Dalej**.

    3. Wpisz i potwierdź nowe hasło, a następnie wybierz pozycję **Zakończ**.

- **Zresetuj hasło przy użyciu numeru telefonu.** Wysyła wiadomość tekstową na numer telefonu, który został wcześniej skonfigurowany w informacjach zabezpieczających. Jeśli administrator włączył informacje zabezpieczające, więcej informacji o konfigurowaniu numeru telefonu można znaleźć w artykule [Konfigurowanie informacji zabezpieczających do korzystania z połączenia telefonicznego (wersja zapoznawcza).](security-info-setup-phone-number.md) Jeśli nie korzystasz jeszcze z informacji zabezpieczających, więcej informacji na temat konfigurowania numeru telefonu można znaleźć w artykule [Konfigurowanie konta do weryfikacji dwuetapowej.](multi-factor-authentication-end-user-first-time.md)

    1. Wybierz **pozycję Zadzwoń do mojego telefonu komórkowego,** wpisz swój numer telefonu, a następnie wybierz pozycję **Zadzwoń**.

    2. Odbierz połączenie telefoniczne i postępuj zgodnie z instrukcjami, aby zweryfikować swoją tożsamość, a następnie wybierz **przycisk Dalej**.

    3. Wpisz i potwierdź nowe hasło, a następnie wybierz pozycję **Zakończ**.

- **Zresetuj hasło za pomocą pytań zabezpieczających.** Pokazuje listę pytań zabezpieczających skonfigurowanych w informacjach zabezpieczających. Jeśli administrator włączył środowisko informacji zabezpieczających, więcej informacji na temat konfigurowania pytań zabezpieczających można znaleźć w artykule [Konfigurowanie informacji zabezpieczających do używania wstępnie zdefiniowanych pytań zabezpieczających (wersja zapoznawcza).](security-info-setup-questions.md) Jeśli nie używasz jeszcze informacji zabezpieczających, więcej informacji na temat konfigurowania pytań zabezpieczających można znaleźć w artykule [Konfigurowanie konta dla weryfikacji dwuetapowej.](multi-factor-authentication-end-user-first-time.md)

    1. Wybierz **pozycję Odpowiedz na moje pytania zabezpieczające**, odpowiedz na pytania, a następnie wybierz pozycję **Dalej**.

    2. Wpisz i potwierdź nowe hasło, a następnie wybierz pozycję **Zakończ**.

- **Zresetuj hasło za pomocą powiadomienia z aplikacji uwierzytelniającego.** Wysyła powiadomienie o zatwierdzeniu do aplikacji uwierzytelniającego. Jeśli administrator włączył środowisko informacji zabezpieczających, możesz znaleźć więcej informacji na temat konfigurowania aplikacji uwierzytelniającego, aby wysłać powiadomienie w artykule [Konfigurowanie informacji zabezpieczających do korzystania z aplikacji uwierzytelniającego (wersja zapoznawcza).](security-info-setup-auth-app.md) Jeśli nie korzystasz jeszcze z informacji zabezpieczających, możesz znaleźć więcej informacji na temat konfigurowania aplikacji uwierzytelniającego, aby wysłać powiadomienie w artykule [Konfigurowanie mojego konta dla weryfikacji dwuetapowej.](multi-factor-authentication-end-user-first-time.md)

    1. Wybierz **pozycję Zatwierdź powiadomienie w aplikacji uwierzytelniającej,** a następnie wybierz pozycję **Wyślij powiadomienie**.

    2. Zatwierdź logowanie z aplikacji uwierzytelniającej.

    3. Wpisz i potwierdź nowe hasło, a następnie wybierz pozycję **Zakończ**.

- **Zresetuj hasło przy użyciu kodu z aplikacji uwierzytelniającego.** Akceptuje losowy kod dostarczony przez aplikację uwierzytelniającą. Jeśli administrator włączył środowisko informacji zabezpieczających, możesz znaleźć więcej informacji na temat konfigurowania aplikacji uwierzytelniającego, aby podać kod w [artykule Konfigurowanie informacji zabezpieczających do korzystania z aplikacji uwierzytelniającego (wersja zapoznawcza).](security-info-setup-auth-app.md) Jeśli nie używasz jeszcze informacji zabezpieczających, możesz znaleźć więcej informacji na temat konfigurowania aplikacji uwierzytelniającego, aby podać kod w artykule [Konfigurowanie mojego konta dla weryfikacji dwuetapowej.](multi-factor-authentication-end-user-first-time.md)

  1. Wybierz **pozycję Wprowadź kod z mojej aplikacji uwierzytelniającego,** a następnie wybierz pozycję Wyślij **powiadomienie**.

  2. Otwórz aplikację uwierzytelniacza, wpisz kod weryfikacyjny konta w polu, a następnie wybierz **przycisk Dalej**.

  3. Wpisz i potwierdź nowe hasło, a następnie wybierz pozycję **Zakończ**.

  4. Po odebraniu komunikatu informującego o zresetowaniu hasła możesz zalogować się na swoje konto przy użyciu nowego hasła.

     Jeśli nadal nie możesz uzyskać dostępu do konta, skontaktuj się z administratorem organizacji, aby uzyskać więcej pomocy.

Po zresetowaniu hasła możesz otrzymać wiadomość e-mail z potwierdzeniem, która \<pochodzi z konta, takiego jak "Microsoft w imieniu *your_organization*>". Jeśli otrzymasz podobną wiadomość e-mail, ale hasło nie zostało ostatnio zresetowane, należy natychmiast skontaktować się z administratorem organizacji.

## <a name="how-to-change-your-password"></a>How to change your password (Jak zmienić hasło)

Jeśli chcesz tylko zmienić hasło, możesz to zrobić za pośrednictwem portalu usługi Office 365, panelu dostępu platformy Azure lub strony logowania systemu Windows 10.

### <a name="to-change-your-password-using-the-office-365-portal"></a>Aby zmienić hasło za pomocą portalu usługi Office 365

Użyj tej metody, jeśli zazwyczaj uzyskujesz dostęp do aplikacji za pośrednictwem portalu pakietu Office:

1. Zaloguj się na [swoje konto usługi Office 365](https://portal.office.com)przy użyciu istniejącego hasła.

2. Wybierz swój profil w prawym górnym rogu, a następnie wybierz pozycję **Wyświetl konto**.

3. Wybierz **pozycję Bezpieczeństwo & hasło prywatności** > **Password**.

4. Wpisz stare hasło, utwórz i potwierdź nowe hasło, a następnie wybierz pozycję **Prześlij**.

### <a name="to-change-your-password-from-the-azure-access-panel"></a>Aby zmienić hasło w panelu programu Azure Access

Użyj tej metody, jeśli zazwyczaj uzyskujesz dostęp do aplikacji z panelu azure access (MyApps):

1. Zaloguj się do [panelu programu Azure Access,](https://myapps.microsoft.com/)używając istniejącego hasła.

2. Wybierz swój profil w prawym górnym rogu, a następnie wybierz **pozycję Profil**.

3. Wybierz **pozycję Zmień hasło**.

4. Wpisz stare hasło, utwórz i potwierdź nowe hasło, a następnie wybierz pozycję **Prześlij**.

### <a name="to-change-your-password-at-windows-sign-in"></a>Aby zmienić hasło podczas logowania do systemu Windows

Jeśli administrator włączył tę funkcję, na ekranie logowania do systemu Windows 7, Windows 8, Windows 8.1 lub Windows 10 zostanie wyświetlone łącze do **resetowania hasła.**

1. Wybierz **łącze Resetuj hasło,** aby rozpocząć proces resetowania hasła bez konieczności korzystania z normalnego środowiska opartego na sieci Web.

2. Potwierdź swój identyfikator użytkownika i wybierz **pozycję Dalej**.

3. Wybierz i potwierdź metodę kontaktu do weryfikacji. Jeśli to konieczne, wybierz drugą opcję weryfikacji, która różni się od poprzedniej, wypełniając niezbędne informacje.

4. Na stronie **Tworzenie nowego hasła** wpisz i potwierdź nowe hasło, a następnie wybierz pozycję **Dalej**.

    Silne hasła mają zazwyczaj od 8 do 16 znaków, w tym wielkie i małe litery, co najmniej jedną liczbę i co najmniej jeden znak specjalny.

5. Po odebraniu komunikatu informującego o zresetowaniu hasła można wybrać **opcję Zakończ**.

    Jeśli nadal nie możesz uzyskać dostępu do konta, skontaktuj się z administratorem organizacji, aby uzyskać więcej pomocy.

## <a name="common-problems-and-their-solutions"></a>Typowe problemy i ich rozwiązania

Oto kilka typowych przypadków błędów i ich rozwiązań:

|Problem|Opis|Rozwiązanie|
| --- | --- | --- |
|Gdy próbuję zmienić hasło, pojawia się błąd. |Hasło zawiera słowo, frazę lub wzór, który ułatwia odgadnięcie hasła.| Spróbuj ponownie użyć silniejszego hasła.|
|Po wprowadzeniu identyfikatora użytkownika przejdźę do strony z napisem "Skontaktuj się z administratorem".|Firma Microsoft ustaliła, że hasło do konta użytkownika jest zarządzane przez administratora w środowisku lokalnym. W związku z tym nie możesz zresetować hasła z linku "Nie możesz uzyskać dostępu do konta". |Skontaktuj się z administratorem, aby uzyskać więcej pomocy.|
|Po wprowadzeniu identyfikatora użytkownika pojawia się komunikat "Twoje konto nie jest włączone do resetowania hasła".|Administrator nie skonfigurował twojego konta, aby można było zresetować własne hasło.|Administrator nie włączył resetowania hasła w organizacji z linku "Nie można uzyskać dostępu do konta" lub nie licencjonował Cię na korzystanie z tej funkcji.<br><br> Aby zresetować hasło, należy wybrać "skontaktuj się z administratorem", aby wysłać wiadomość e-mail do administratora firmy i poinformować go, że chcesz zresetować hasło.|
|Po wprowadzeniu identyfikatora użytkownika pojawia się komunikat "Nie mogliśmy zweryfikować Twojego konta".|Proces logowania nie może zweryfikować informacji o koncie.|Istnieją dwa powody, dla których możesz zobaczyć ten komunikat.<br><br>1. Administrator włączył resetowanie hasła w organizacji, ale nie zarejestrowałeś się, aby korzystać z usługi. Aby zarejestrować się w celu resetowania hasła, zobacz jeden z następujących artykułów na podstawie metody weryfikacji: [Konfigurowanie informacji zabezpieczających do używania aplikacji uwierzytelniającego (wersja zapoznawcza)](security-info-setup-auth-app.md), [Konfigurowanie informacji zabezpieczających do korzystania z połączenia telefonicznego (wersja zapoznawcza)](security-info-setup-phone-number.md), [Set up security info to use email (preview)](security-info-setup-email.md) [Konfigurowanie informacji zabezpieczających do używania wiadomości tekstowych (wersja zapoznawcza)](security-info-setup-text-msg.md) [Set up security info to use security questions (preview)](security-info-setup-questions.md)<br><br>2. Administrator nie włączył resetowania hasła w organizacji. W takiej sytuacji należy wybrać "skontaktuj się z administratorem linku", aby wysłać wiadomość e-mail do administratora z prośbą o zresetowanie hasła.|

## <a name="next-steps"></a>Następne kroki

- Informacje o informacjach zabezpieczających w [artykule Omówienie informacji o zabezpieczeniach (wersja zapoznawcza).](user-help-security-info-overview.md)

- Jeśli próbujesz wrócić na konto osobiste, takie jak Xbox, hotmail.com lub outlook.com, wypróbuj sugestie w [artykule Kiedy nie możesz zalogować się do konta Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant).
