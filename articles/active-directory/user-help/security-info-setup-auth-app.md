---
title: Konfigurowanie aplikacji Microsoft Authenticator jako metody weryfikacji — Azure AD
description: Jak skonfigurować stronę informacje zabezpieczające (wersja zapoznawcza), aby zweryfikować tożsamość przy użyciu aplikacji Microsoft Authenticator jako metody weryfikacji.
services: active-directory
author: curtand
manager: daveba
ms.reviewer: sahenry
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 02/13/2019
ms.author: curtand
ms.openlocfilehash: 16cb512d3f8f8afcc199cb52c13e09b12107576f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77062425"
---
# <a name="set-up-the-microsoft-authenticator-app-as-your-verification-method"></a>Konfigurowanie aplikacji Microsoft Authenticator jako metody weryfikacji

Wykonaj następujące kroki, aby dodać metody weryfikacji dwuskładnikowej i resetowania hasła. Po skonfigurowaniu tej opcji po raz pierwszy można powrócić do strony **Informacje o zabezpieczeniach,** aby dodać, zaktualizować lub usunąć informacje o zabezpieczeniach.

Jeśli zostanie wyświetlony monit o skonfigurowanie tej funkcji natychmiast po zalogowaniu się na konto służbowe, zapoznaj się ze szczegółowymi czynnościami artykułu [Konfigurowanie informacji zabezpieczających na stronie logowania.](security-info-setup-signin.md)

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

>[!Note]
> Jeśli nie widzisz opcji aplikacji uwierzytelniającego, możesz, że Twoja organizacja nie zezwala na używanie tej opcji do weryfikacji. W takim przypadku musisz wybrać inną metodę lub skontaktować się z działem pomocy technicznej organizacji, aby uzyskać więcej pomocy.

## <a name="security-vs-password-reset-verification"></a>Weryfikacja zabezpieczeń a resetowania hasła

Metody informacji zabezpieczających są używane zarówno do weryfikacji zabezpieczeń dwuskładnikowych, jak i resetowania hasła. Jednak nie wszystkie metody mogą służyć do obu celów.

| Metoda | Używana do |
| ------ | -------- |
| Aplikacja Authenticator | Weryfikacja dwuskładnikowa i uwierzytelnianie za pomocą resetowania hasła. |
| Wiadomości SMS | Weryfikacja dwuskładnikowa i uwierzytelnianie za pomocą resetowania hasła. |
| Rozmowy telefoniczne | Weryfikacja dwuskładnikowa i uwierzytelnianie za pomocą resetowania hasła. |
| Klucz zabezpieczeń | Weryfikacja dwuskładnikowa i uwierzytelnianie za pomocą resetowania hasła. |
| Konto e-mail | Tylko uwierzytelnianie za pomocą resetowania hasła. Należy wybrać inną metodę na potrzeby weryfikacji dwuskładnikowej. |
| Pytania zabezpieczające | Tylko uwierzytelnianie za pomocą resetowania hasła. Należy wybrać inną metodę na potrzeby weryfikacji dwuskładnikowej. |

## <a name="set-up-the-microsoft-authenticator-app-from-the-security-info-page"></a>Konfigurowanie aplikacji Microsoft Authenticator na stronie Informacje o zabezpieczeniach

W zależności od ustawień organizacji można użyć aplikacji uwierzytelniania jako jednej z metod informacji zabezpieczających. Nie musisz korzystać z aplikacji Microsoft Authenticator i możesz wybrać inną aplikację podczas procesu konfiguracji. Jednak w tym artykule używa się aplikacji Microsoft Authenticator.

### <a name="to-set-up-the-microsoft-authenticator-app"></a>Aby skonfigurować aplikację Microsoft Authenticator

1. Zaloguj się na swoje konto służbowe, https://myprofile.microsoft.com/ a następnie przejdź do strony.

    ![Strona Mój profil z wyróżnionymi linkami informacji o zabezpieczeniach](media/security-info/securityinfo-myprofile-page.png)

2. Wybierz **pozycję Informacje o zabezpieczeniach** z lewego okienka nawigacji lub z łącza w bloku Informacje o **zabezpieczeniach,** a następnie wybierz pozycję **Dodaj metodę** ze strony Informacje o **zabezpieczeniach.**

    ![Strona informacji o zabezpieczeniach z wyróżnioną opcją Dodaj metodę](media/security-info/securityinfo-myprofile-addmethod-page.png)

3. Na stronie **Dodawanie metody** wybierz pozycję **Aplikacja Authenticator** z listy rozwijanej, a następnie wybierz pozycję **Dodaj**.

    ![Pole Dodaj metodę z wybraną aplikacją Authenticator](media/security-info/securityinfo-myprofile-addauthapp.png)

4. Na stronie **Rozpocznij, pobierając aplikację,** wybierz pozycję **Pobierz teraz,** aby pobrać i zainstalować aplikację Microsoft Authenticator na urządzeniu przenośnym, a następnie wybierz pozycję **Dalej**.

    Aby uzyskać więcej informacji o pobieraniu i instalowaniu aplikacji, zobacz [Pobieranie i instalowanie aplikacji Microsoft Authenticator](user-help-auth-app-download-install.md).

    ![Zacznij od wyświetlenie strony aplikacji](media/security-info/securityinfo-myprofile-getauthapp.png)

   > [!Note]
   > Jeśli chcesz użyć aplikacji uwierzytelniającej innej niż aplikacja Microsoft Authenticator, wybierz link **Chcę użyć innej aplikacji uwierzytelniania**.
   >
   > Jeśli organizacja umożliwia wybranie innej metody niż aplikacja uwierzytelniająca, możesz wybrać **link Chcę skonfigurować inną metodę**.

5. Pozostań na stronie **Konfigurowanie konta** podczas konfigurowania aplikacji Microsoft Authenticator na urządzeniu mobilnym.

    ![Konfigurowanie strony aplikacji uwierzytelniającego](media/security-info/securityinfo-myprofile-setupauthapp.png)

6. Otwórz aplikację Microsoft Authenticator, zezwól na powiadomienia (jeśli pojawi się monit), wybierz pozycję **Dodaj konto** z menu ikony **Dostosowywanie i kontrolowanie** w prawym górnym rogu, a następnie wybierz pozycję **Konto służbowe**.

    >[!Note]
    >Jeśli po raz pierwszy konfigurujesz aplikację Microsoft Authenticator, może pojawić się monit z pytaniem, czy zezwolić aplikacji na dostęp do aparatu (iOS), czy zezwolić aplikacji na robienia zdjęć i nagrywania wideo (Android). Musisz wybrać **opcję Zezwalaj,** aby aplikacja uwierzytelniającego mogła uzyskać dostęp do aparatu, aby zrobić zdjęcie kodu QR w następnym kroku. Jeśli nie zezwolisz na aparat, nadal możesz skonfigurować aplikację uwierzytelniacza, ale musisz ręcznie dodać informacje o kodzie. Aby uzyskać informacje dotyczące ręcznego dodawania kodu, zobacz [Ręczne dodawanie konta do aplikacji](user-help-auth-app-add-account-manual.md).

7. Wróć na stronę **Konfigurowanie konta** na komputerze, a następnie wybierz pozycję **Dalej**.

    Zostanie wyświetlona strona **Skanowanie kodu QR**.

    ![Skanowanie kodu QR przy użyciu aplikacji Authenticator](media/security-info/securityinfo-myprofile-qrcodeauthapp.png)

8. Zeskanuj podany kod za pomocą czytnika kodów QR aplikacji Microsoft Authenticator, który pojawił się na urządzeniu przenośnym po utworzeniu konta służbowego w kroku 6.

    Aplikacja Authenticator powinna pomyślnie dodać konto służbowe bez konieczności podawania żadnych dodatkowych informacji przez użytkownika. Jednak jeśli czytnik kodów QR nie może odczytać kodu, możesz wybrać **link Nie mogę zeskanować kodu QR** oraz ręcznie podać kod i adres URL w aplikacji Microsoft Authenticator. Aby uzyskać więcej informacji na temat ręcznego dodawania kodu, zobacz [Ręcznie dodawanie konta do aplikacji](user-help-auth-app-add-account-manual.md).

9. Wybierz pozycję **Dalej** na stronie **Zeskanuj kod QR** na komputerze.

    Do aplikacji Microsoft Authenticator na urządzeniu przenośnym zostanie wysłane powiadomienie o konieczności przetestowania konta.

    ![Testowanie konta za pomocą aplikacji Authenticator](media/security-info/securityinfo-myprofile-tryitauthapp.png)

10. Zatwierdź powiadomienie w aplikacji Microsoft Authenticator, a następnie wybierz pozycję **Dalej**.

     ![Powiadomienie o powodzeniu łączenia aplikacji z kontem](media/security-info/securityinfo-myprofile-successauthapp.png)

     Informacje zabezpieczające zostały zaktualizowane, tak aby aplikacja Microsoft Authenticator była domyślnie używana do weryfikowania tożsamości podczas korzystania z weryfikacji dwuetapowej lub resetowania hasła.

## <a name="delete-your-authenticator-app-from-your-security-info-methods"></a>Usuwanie aplikacji uwierzytelniającego z metod informacji zabezpieczających

Jeśli nie chcesz już używać aplikacji uwierzytelniającego jako metody informacji zabezpieczających, możesz usunąć ją ze strony **Informacje o zabezpieczeniach.** Działa to we wszystkich aplikacjach uwierzytelniającego, a nie tylko w aplikacji Microsoft Authenticator. Po usunięciu aplikacji musisz przejść do aplikacji uwierzytelniającego na urządzeniu mobilnym i usunąć konto.

>[!Important]
>Jeśli usuniesz aplikację uwierzytelniającą przez pomyłkę, nie ma możliwości jej cofnięcia. Musisz ponownie dodać aplikację uwierzytelniacza, wykonując czynności opisane w sekcji [Konfigurowanie aplikacji uwierzytelniacza](#set-up-the-microsoft-authenticator-app-from-the-security-info-page) w tym artykule.

### <a name="to-delete-the-authenticator-app"></a>Aby usunąć aplikację uwierzytelniającego

1. Na stronie **Informacje o zabezpieczeniach** wybierz łącze **Usuń** obok aplikacji Authenticator.

    ![Łącze, aby usunąć aplikację uwierzytelniającego z informacji zabezpieczających](media/security-info/securityinfo-myprofile-deleteauthapp.png)

2. Wybierz **tak** z pola potwierdzenia, aby usunąć aplikację uwierzytelniacza. Po usunięciu aplikacji uwierzytelniającego jest usuwana z informacji zabezpieczających i znika ze strony **Informacje o zabezpieczeniach.** Jeśli aplikacja wystawcy uwierzytelnienia jest metodą domyślną, domyślna zmieni się na inną dostępną metodę.

3. Otwórz aplikację uwierzytelniacza na urządzeniu przenośnym, wybierz **pozycję Edytuj konta**, a następnie usuń konto służbowe z aplikacji uwierzytelniacza.

    Twoje konto zostanie całkowicie usunięte z aplikacji uwierzytelniającego w celu weryfikacji dwuskładnikowej i żądania resetowania hasła.

## <a name="change-your-default-security-info-method"></a>Zmienianie domyślnej metody informacji zabezpieczających

Jeśli chcesz, aby aplikacja uwierzytelniacza była domyślną metodą używaną podczas logowania się do konta służbowego przy użyciu weryfikacji dwuskładnikowej lub żądań resetowania hasła, możesz ją ustawić na stronie **Informacje o zabezpieczeniach.**

### <a name="to-change-your-default-security-info-method"></a>Aby zmienić domyślną metodę informacji zabezpieczających

1. Na stronie **Informacje o zabezpieczeniach** wybierz łącze **Zmień** obok domyślnych informacji o **metodzie logowania.**

    ![Łącze zmień domyślną metodę logowania](media/security-info/securityinfo-myprofile-changedefaultauthapp.png)

2. Wybierz **pozycję Microsoft Authenticator — powiadomienie** z listy rozwijanej dostępnych metod. Jeśli nie używasz aplikacji Microsoft Authenticator, wybierz opcję **Aplikacji Uwierzytelniaj lub tokenu sprzętowego.**

    ![Wybierz metodę domyślnego logowania](media/security-info/securityinfo-myprofile-defaultauthapp.png)

3. Wybierz **pozycję Potwierdź**.

    Domyślna metoda używana do zmiany logowania w aplikacji Microsoft Authenticator.

## <a name="additional-security-info-methods"></a>Dodatkowe metody informacji o zabezpieczeniach

Masz dodatkowe opcje, w jaki sposób twoja organizacja kontaktuje się z Tobą, aby zweryfikować swoją tożsamość, w zależności od tego, co próbujesz zrobić. Dostępne są następujące opcje:

- **Tekst urządzenia mobilnego.** Wpisz numer urządzenia mobilnego i otrzymasz sms-a z kodem, którego użyjesz do weryfikacji dwuetapowej lub resetowania hasła. Aby uzyskać instrukcje krok po kroku dotyczące weryfikacji tożsamości za pomocą wiadomości SMS, zobacz [Konfigurowanie informacji zabezpieczających do korzystania z wiadomości tekstowych (SMS).](security-info-setup-text-msg.md)

- **Połączenie telefoniczne z urządzeniem mobilnym lub służbowym.** Wprowadź numer urządzenia mobilnego i uzyskaj połączenie telefoniczne w celu weryfikacji dwuetapowej lub zresetowania hasła. Aby uzyskać instrukcje krok po kroku dotyczące weryfikacji tożsamości za pomocą numeru telefonu, zobacz [Konfigurowanie informacji zabezpieczających do korzystania z połączeń telefonicznych.](security-info-setup-phone-number.md)

- **Klucz zabezpieczeń.** Zarejestruj klucz zabezpieczeń zgodny z firmą Microsoft i użyj go wraz z kodem PIN do weryfikacji dwuetapowej lub resetowania hasła. Aby uzyskać instrukcje krok po kroku dotyczące weryfikacji tożsamości za pomocą klucza zabezpieczeń, zobacz [Konfigurowanie informacji zabezpieczających w celu używania klucza zabezpieczeń](security-info-setup-security-key.md).

- **Adres e-mail.** Wprowadź służbowy adres e-mail, aby otrzymać wiadomość e-mail z prośbą o zresetowanie hasła. Ta opcja nie jest dostępna w przypadku weryfikacji dwuetapowej. Aby uzyskać instrukcje krok po kroku dotyczące konfigurowania poczty e-mail, zobacz [Konfigurowanie informacji zabezpieczających do korzystania z poczty e-mail](security-info-setup-email.md).

- **Pytania zabezpieczające.** Odpowiedz na niektóre pytania zabezpieczające utworzone przez administratora w organizacji. Ta opcja jest dostępna tylko w przypadku resetowania hasła, a nie weryfikacji dwuetapowej. Aby uzyskać instrukcje krok po kroku dotyczące konfigurowania pytań zabezpieczających, zobacz artykuł [Konfigurowanie informacji zabezpieczających do używania pytań zabezpieczających.](security-info-setup-questions.md)

    >[!Note]
    >Jeśli brakuje niektórych z tych opcji, jest to najprawdopodobniej, ponieważ organizacja nie zezwala na te metody. W takim przypadku należy wybrać dostępną metodę lub skontaktować się z administratorem, aby uzyskać dodatkową pomoc.

## <a name="next-steps"></a>Następne kroki

- Zaloguj się przy użyciu aplikacji Microsoft Authenticator, wykonując kroki opisane w artykule [Logowanie przy użyciu weryfikacji dwuetapowej lub informacji zabezpieczających.](security-info-setup-signin.md)

- Zagubione lub zapomniane hasło możesz zresetować w [portalu resetowania hasła](https://passwordreset.microsoftonline.com/) lub wykonując kroki przedstawione w artykule [Resetowanie hasła służbowego](active-directory-passwords-update-your-own-password.md).

- Wskazówki i pomoc dotyczące rozwiązywania problemów z logowaniem znajdują się w artykule [Nie można zalogować się na konto Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant).
