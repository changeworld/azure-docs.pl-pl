---
title: Zaloguj się przy użyciu aplikacji Microsoft Authenticator — Azure AD
description: Aplikacja Microsoft Authenticator umożliwia logowanie się do konta służbowego lub osobistego konta Microsoft i innych firm za pomocą weryfikacji dwuskładnikowej lub logowania telefonicznego.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 02/25/2020
ms.author: curtand
ms.reviewer: olhaun
ms.openlocfilehash: 9b8c44f99953d4518f0bc3f558f396250657c632
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79138947"
---
# <a name="sign-in-to-your-accounts-using-the-microsoft-authenticator-app"></a>Logowanie się do kont za pomocą aplikacji Microsoft Authenticator

Aplikacja Microsoft Authenticator ułatwia logowanie się do kont, jeśli korzystasz z weryfikacji dwuskładnikowej. Weryfikacja dwuskładnikowa zapewnia bezpieczniejszy dostęp do kont, szczególnie podczas wyświetlania informacji poufnych. Ponieważ hasła mogą zostać zapomniane, skradzione lub ujawnione, weryfikacja dwuskładnikowa zapewnia dodatkowy poziom zabezpieczeń, który pomaga chronić konto, utrudniając włamanie się na nie innym osobom.

Aplikacji Microsoft Authenticator można używać na wiele sposobów, na przykład:

- Udostępnienie monitu dla drugiej metody weryfikacji po zalogowaniu się przy użyciu nazwy użytkownika i hasła.

- Udostępnienie możliwości logowania się bez wymagania hasła, ale za to przy użyciu nazwy użytkownika i jego twarzy, odcisku palca lub numeru PIN dostarczanych przez urządzenie przenośne.

  >[!Important]
  >Ta metoda logowania do telefonu działa tylko z twoimi służbowymi lub szkolnymi i osobistymi kontami Microsoft. Konta innych firm wymagają użycia standardowego procesu weryfikacji dwuskładnikowej.

## <a name="prerequisites"></a>Wymagania wstępne

Aby można było korzystać z aplikacji Microsoft Authenticator, należy:

 1. Pobierz i zainstaluj aplikację Microsoft Authenticator. Jeśli jeszcze tego nie zrobiłeś, zobacz [Pobieranie i instalowanie aplikacji](user-help-auth-app-download-install.md).

 2. Dodaj swoje konta służbowe/szkolne, osobiste i innych firm do aplikacji Microsoft Authenticator. Aby uzyskać szczegółowe kroki, zobacz [Dodawanie konta służbowego lub szkolnego](user-help-auth-app-add-work-school-account.md), [Dodawanie kont osobistych](user-help-auth-app-add-personal-ms-account.md)i [Dodawanie kont innych niż Microsoft](user-help-auth-app-add-non-ms-account.md).

## <a name="turn-on-and-use-phone-sign-in-for-your-work-or-school-account"></a>Włączanie i używanie logowania telefonu do konta służbowego

Logowanie telefoniczne jest rodzajem weryfikacji dwuetapowej. Nadal musisz zweryfikować swoją tożsamość, podając coś, co znasz i coś, co masz, ale logowanie telefoniczne pozwala pominąć wprowadzenie hasła do konta i przeprowadzić całą weryfikację tożsamości na urządzeniu mobilnym.

Aby można było włączyć logowanie telefoniczne, musisz włączyć weryfikację dwuskładnikową. Aby uzyskać więcej informacji na temat włączania weryfikacji dwuskładnikowej dla konta, zobacz [Dodawanie konta służbowego i](user-help-auth-app-add-work-school-account.md) Dodawanie kont [osobistych.](user-help-auth-app-add-personal-ms-account.md)

Logowanie telefoniczne jest dostępne tylko na urządzeniach z systemem iOS i Android z systemem Android 6.0 lub wyższym.

### <a name="turn-on-phone-sign-in"></a>Włączanie logowania telefonu

Otwórz aplikację Microsoft Authenticator, przejdź do konta służbowego i włącz logowanie telefoniczne.

Na urządzeniu z Androidem:

- **Jeśli widzisz ikonę ![z ikoną,](media/user-help-auth-app-sign-in/icon.png)na której jest ustawiona ikona .** Jeśli obok nazwy konta służbowego pojawi się ta ikona, oznacza to, że konto zostało już skonfigurowane przez telefon. Może pojawić się prośba o dodanie powiadomień wypychanych dla swojego konta, aby otrzymywać powiadomienia o żądaniach uwierzytelniania poza aplikacją.
- **Jeśli korzystasz już z aplikacji do weryfikacji dwuskładnikowej.** Jeśli korzystasz już z aplikacji i weryfikacji dwuskładnikowej, możesz wybrać strzałkę obok nazwy konta, a następnie wybrać **pozycję Włącz logowanie do telefonu**.
- **Jeśli nie możesz znaleźć konta służbowego.** Jeśli nie możesz znaleźć konta służbowego na ekranie **Konta** aplikacji, oznacza to, że nie zostało jeszcze dodane do aplikacji. Dodaj swoje konto służbowe, wykonując czynności opisane w artykule [Dodaj swoje konto służbowe.](user-help-auth-app-add-work-school-account.md)

Na urządzeniu z systemem iOS:

- **Po dotknięciu kafelka konta**konto otworzy się w widoku pełnym ekranu konta. Jeśli widzisz **włączoną funkcję logowania telefonicznego,** oznacza to, że jesteś w pełni skonfigurowany do logowania się bez hasła. Jeśli widzisz pozycję **Włącz logowanie do telefonu,** naciśnij go, aby włączyć logowanie się do telefonu.
- **Jeśli korzystasz już z aplikacji do weryfikacji dwuskładnikowej,** możesz dotknąć kafelka konta, który następnie zostanie rozszerzony do widoku pełnego ekranu konta. Następnie naciśnij pozycję **Włącz logowanie telefonu,** aby włączyć logowanie się do telefonu.
- **Jeśli nie możesz znaleźć konta służbowego** na ekranie **Konta** aplikacji, oznacza to, że nie zostało jeszcze dodane do aplikacji. Dodaj swoje konto służbowe, wykonując czynności opisane w artykule Dodaj swoje konto służbowe.

Po włączeniu logowania telefonu możesz zalogować się tylko za pomocą aplikacji Microsoft Authenticator.

1. Zaloguj się na swoje konto służbowe.

    Po wpisaniu nazwy użytkownika zostanie wyświetlony ekran **Zatwierdź logowanie** z dwucyfrowym numerem i z prośbą o zalogowanie się za pośrednictwem aplikacji Microsoft Authenticator. Jeśli nie chcesz używać tej metody logowania, możesz wybrać opcję **Użyj hasła**i zalogować się przy użyciu hasła.

    ![Zatwierdzanie pola logowania na komputerze](media/user-help-auth-app-sign-in/microsoft-auth-app-sign-in.png)

2. Otwórz powiadomienie lub aplikację Microsoft Authenticator na urządzeniu, a następnie naciśnij numer odpowiadający numerowi widocznemu na ekranie **zatwierdzania logowania** na komputerze.

    ![Zatwierdzanie pola logowania na urządzeniu](media/user-help-auth-app-sign-in/microsoft-auth-app-sign-in-numbers.png)

3. Wybierz pozycję **Zatwierdź**, jeśli rozpoznajesz próbę logowania. W przeciwnym razie wybierz pozycję **Odmów**.

4. Użyj numeru PIN telefonu lub klucza biometrycznego, aby zakończyć uwierzytelnianie.

## <a name="turn-on-and-use-phone-sign-in-for-your-personal-microsoft-accounts"></a>Włączanie i używanie logowania telefonu dla osobistych kont Microsoft

Możesz włączyć logowanie telefoniczne dla osobistego konta Microsoft, takiego jak konto używane do logowania się do Outlook.com, konsoli Xbox lub Skype.

>[!NOTE]
>Aby chronić swoje konto, aplikacja Microsoft Authenticator wymaga kodu PIN lub blokady biometrycznej na urządzeniu. Jeśli telefon jest odblokowany, aplikacja wymaga skonfigurowania blokady zabezpieczeń przed włączeniem logowania się do telefonu.

### <a name="turn-on-phone-sign-in"></a>Włączanie logowania telefonu 

Otwórz aplikację Microsoft Authenticator, przejdź do konta służbowego i włącz logowanie telefoniczne.

Na urządzeniu z Androidem:

- **Jeśli widzisz ikonę ![z ikoną,](media/user-help-auth-app-sign-in/icon.png)na której jest ustawiona ikona .** Jeśli ta ikona pojawi się obok twojej osobistej nazwy konta Microsoft, oznacza to, że masz już skonfigurowane logowanie telefoniczne dla konta. Może pojawić się prośba o dodanie powiadomień wypychanych dla swojego konta, aby otrzymywać powiadomienia o żądaniach uwierzytelniania poza aplikacją.
- **Jeśli używasz aplikacji do weryfikacji dwuskładnikowej.** Jeśli korzystasz już z aplikacji i weryfikacji dwuskładnikowej, możesz wybrać strzałkę obok nazwy konta, a następnie wybrać **pozycję Włącz logowanie do telefonu**.
- **Jeśli nie możesz znaleźć konta służbowego.** Jeśli nie możesz znaleźć swojego konta na ekranie **Konta** aplikacji, oznacza to, że nie zostało jeszcze dodane do aplikacji. Dodaj swoje konto, wykonując czynności opisane w artykule [Dodawanie osobistych kont Microsoft.](user-help-auth-app-add-personal-ms-account.md)

Na urządzeniu z systemem iOS:

- **Po dotknięciu kafelka konta**konto otworzy się w widoku pełnym ekranu konta. Jeśli widzisz **włączoną funkcję logowania telefonicznego,** oznacza to, że jesteś w pełni skonfigurowany do logowania się bez hasła. Jeśli widzisz pozycję **Włącz logowanie do telefonu,** naciśnij go, aby włączyć logowanie się do telefonu.
- **Jeśli korzystasz już z aplikacji do weryfikacji dwuskładnikowej,** możesz dotknąć kafelka konta, który następnie zostanie rozszerzony do widoku pełnego ekranu konta. Następnie naciśnij pozycję **Włącz logowanie telefonu,** aby włączyć logowanie się do telefonu.
- **Jeśli nie możesz znaleźć swojego konta** na ekranie **Konta** aplikacji, oznacza to, że nie zostało jeszcze dodane do aplikacji. Dodaj osobiste konto Microsoft, wykonując czynności opisane w artykule [Dodawanie osobistych kont Microsoft.](user-help-auth-app-add-personal-ms-account.md)

### <a name="sign-in-to-your-account-using-phone-sign-in"></a>Logowanie się na konto przy użyciu logowania telefonu

1. Przejdź do strony logowania do osobistego konta Microsoft, a następnie zamiast wpisywać hasło, wybierz **łącze Użyj aplikacji Microsoft Authenticator.**

    Firma Microsoft wysyła powiadomienie do telefonu.

2. Zatwierdź powiadomienie.

## <a name="sign-in-using-two-factor-verification-for-your-account"></a>Zaloguj się przy użyciu weryfikacji dwuskładnikowej dla swojego konta

Standardowa metoda weryfikacji dwuskładnikowej wymaga wprowadzenia nazwy użytkownika i hasła do urządzenia, na które się logujesz. Następnie otwórz aplikację Microsoft Authenticator i wpisz losowo wygenerowany kod konta w polu **Wprowadź kod.** Na urządzeniu z Androidem te kody weryfikacyjne można znaleźć na ekranie **Konta.** Na urządzeniu z systemem iOS te kody weryfikacyjne można znaleźć na ekranie **Konta** lub w widoku pełnym ekranu konta, w zależności od typu konta. Włącz weryfikację dwuskładnikową dla swojego konta w ramach procesu, aby dodać konto do aplikacji Microsoft Authenticator.

>[!Note]
>Jeśli nie widzisz konta służbowego ani konta osobistego na ekranie **Konta** aplikacji Microsoft Authenticator, oznacza to, że konto nie zostało dodane do aplikacji Microsoft Authenticator. Aby dodać konto, zobacz [Dodawanie konta służbowego lub](user-help-auth-app-add-work-school-account.md) Dodawanie kont [osobistych.](user-help-auth-app-add-personal-ms-account.md)

Aby uzyskać kroki niezbędne do zalogowania się do pracy, szkoły lub konta osobistego, korzystając z różnych metod weryfikacji dwuskładnikowej, zobacz [Logowanie się przy użyciu weryfikacji dwuetapowej lub informacji zabezpieczających.](user-help-sign-in.md)

## <a name="frequently-asked-questions"></a>Często zadawane pytania

| Pytanie | Rozwiązanie |
| -------- | -------- |
| W jaki sposób logowanie się za pomocą telefonu jest bezpieczniejsze niż wpisanie hasła? | Obecnie większość osób loguje się do witryn internetowych lub aplikacji przy użyciu nazwy użytkownika i hasła. Niestety, hasła mogą zostać utracone, skradzione lub odgadnięty przez hakerów.<br><br>Po skonfigurowaniu aplikacji Microsoft Authenticator w telefonie zostanie utworzony klucz do odblokowania konta chronionego kodem PIN lub blokadą biometryczną telefonu. Ten klucz jest następnie używany do potwierdzania tożsamości podczas logowania.<br><br>**Ważne**<br>Twoje dane są używane tylko do ochrony klucza lokalnie. Nigdy nie jest wysyłany ani przechowywany w chmurze. |
| Czy logowanie telefoniczne zastępuje weryfikację dwuetapową? Czy należy go wyłączyć? | Logowanie telefoniczne to typ weryfikacji dwuetapowej, w której dwa kroki mają miejsce na urządzeniu przenośnym. Należy włączyć weryfikację dwuetapową, aby zapewnić dodatkowe bezpieczeństwo konta. |
| Jeśli na moim koncie włączę weryfikację dwuetapową, czy muszę zatwierdzić dwa powiadomienia? | Nie. Logowanie się do konta Microsoft za pomocą telefonu jest również liczone jako weryfikacja dwuetapowa, więc nie jest wymagane drugie zatwierdzenie. |
| Co zrobić, jeśli zgubię telefon lub nie mam go przy sobie? Jak uzyskać dostęp do konta? | Zawsze możesz wybrać łącze Użyj hasła na stronie logowania, aby wrócić do używania hasła. Jeśli jednak korzystasz z weryfikacji dwuetapowej, nadal musisz użyć drugiej metody, aby zweryfikować swoją tożsamość.<br><br>**Ważne**<br>Zdecydowanie zachęcamy do upewnienia się, że masz więcej niż jedną, aktualną metodę weryfikacji powiązaną z Twoim kontem.<br><br>Możesz zarządzać metodami weryfikacji kont osobistych na stronie [Ustawienia zabezpieczeń.](https://account.live.com/proofs/manage) W przypadku kont służbowych możesz przejść do strony [Dodatkowa weryfikacja zabezpieczeń](https://account.activedirectory.windowsazure.com/proofup.aspx?proofup=1) w organizacji lub na stronie **Zachowaj bezpieczeństwo konta,** jeśli administrator włączył informacje zabezpieczające. Aby uzyskać więcej informacji o informacjach zabezpieczających, zobacz [Omówienie informacji o zabezpieczeniach (wersja zapoznawcza).](user-help-security-info-overview.md)<br><br>Jeśli nie możesz zarządzać metodami weryfikacji, skontaktuj się z administratorem. |
| Jak przestać korzystać z tej funkcji i wrócić do używania hasła? | W przypadku kont osobistych wybierz **łącze Użyj hasła** podczas logowania. Twój najnowszy wybór jest zapamiętywany i oferowany domyślnie przy następnym loguchić. Jeśli kiedykolwiek chcesz wrócić do logowania się na telefon, wybierz **link Użyj aplikacji** podczas logowania.<br><br>W przypadku kont służbowych należy wyrejestrować urządzenie na stronie **Ustawienia** aplikacji Microsoft Authenticator lub wyłączyć urządzenie z obszaru **aktywności Urządzenia &** profilu. Aby uzyskać więcej informacji na temat wyłączania urządzenia z profilu, zobacz [Aktualizowanie informacji o profilu i koncie w portalu Moje aplikacje](my-apps-portal-end-user-update-profile.md#view-your-organization-related-profile-information). |
| Dlaczego nie mogę używać więcej niż jednego konta służbowego do logowania się przez telefon? | Telefon musi być zarejestrowany na jednym koncie służbowym. Jeśli chcesz włączyć logowanie telefoniczne dla innego konta służbowego, musisz wyrejestrować konto z tego urządzenia na stronie **Ustawienia.** |
| Czy mogę zalogować się do komputera za pomocą telefonu? | W przypadku komputera zalecamy zalogowanie się przy użyciu funkcji Windows Hello w systemie Windows 10. Funkcja Windows Hello umożliwia logowanie się za pomocą twarzy, odcisku palca lub numeru PIN. |

## <a name="next-steps"></a>Następne kroki

- Jeśli masz problemy z uzyskaniem kodu weryfikacyjnego dla osobistego konta Microsoft, zobacz sekcję **Rozwiązywanie problemów z kodem weryfikacyjnym** w artykule [& kody weryfikacyjne dotyczące zabezpieczeń konta Microsoft.](https://support.microsoft.com/help/12428/microsoft-account-security-info-verification-codes)

- Jeśli masz bardziej ogólne pytania dotyczące aplikacji, zapoznaj się z [często zadawanymi pytaniami dotyczącymi wystawcy uwierzytelniającego firmy Microsoft](user-help-auth-app-faq.md)

- Jeśli chcesz uzyskać więcej informacji na temat weryfikacji dwuetapowej, zobacz [Konfigurowanie konta do weryfikacji dwuetapowej](multi-factor-authentication-end-user-first-time.md)

- Jeśli chcesz uzyskać więcej informacji o informacjach zabezpieczających, zobacz [Omówienie informacji o zabezpieczeniach (wersja zapoznawcza)](user-help-security-info-overview.md)
