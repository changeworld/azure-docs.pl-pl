---
title: Konfigurowanie numeru telefonu jako metody weryfikacji — Usługa Azure AD
description: Jak skonfigurować stronę Informacje zabezpieczające (wersja zapoznawcza), aby zweryfikować swoją tożsamość przy użyciu numeru telefonu i urządzenia mobilnego jako metody weryfikacji.
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
ms.openlocfilehash: e85be9140aabe5b66e63d3ccdd4a3ea907a1d6fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77062408"
---
# <a name="set-up-a-phone-number-as-your-verification-method"></a>Konfigurowanie numeru telefonu jako metody weryfikacji

Wykonaj następujące kroki, aby dodać metody weryfikacji dwuskładnikowej i resetowania hasła. Po skonfigurowaniu tej opcji po raz pierwszy można powrócić do strony **Informacje o zabezpieczeniach,** aby dodać, zaktualizować lub usunąć informacje o zabezpieczeniach.

Jeśli zostanie wyświetlony monit o skonfigurowanie tej funkcji natychmiast po zalogowaniu się na konto służbowe, zapoznaj się ze szczegółowymi czynnościami artykułu [Konfigurowanie informacji zabezpieczających na stronie logowania.](security-info-setup-signin.md)

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

> [!Note]
> Informacje zabezpieczające nie obsługują używania rozszerzeń telefonu. Nawet jeśli dodasz odpowiedni format+1 4255551234X12345, rozszerzenia zostaną usunięte przed najechaniem.
>
> Jeśli nie widzisz opcji telefonu, możesz, że Twoja organizacja nie zezwala na korzystanie z tej opcji do weryfikacji. W takim przypadku musisz wybrać inną metodę lub skontaktować się z działem pomocy technicznej organizacji, aby uzyskać więcej pomocy.

## <a name="security-verification-versus-password-reset-authentication"></a>Weryfikacja zabezpieczeń a uwierzytelnianie resetowania hasła

Metody informacji zabezpieczających są używane zarówno do weryfikacji zabezpieczeń dwuskładnikowych, jak i resetowania hasła. Jednak nie wszystkie metody mogą służyć do obu celów.

| Metoda | Używana do |
| ------ | -------- |
| Aplikacja Authenticator | Weryfikacja dwuskładnikowa i uwierzytelnianie za pomocą resetowania hasła. |
| Wiadomości SMS | Weryfikacja dwuskładnikowa i uwierzytelnianie za pomocą resetowania hasła. |
| Rozmowy telefoniczne | Weryfikacja dwuskładnikowa i uwierzytelnianie za pomocą resetowania hasła. |
| Klucz zabezpieczeń | Weryfikacja dwuskładnikowa i uwierzytelnianie za pomocą resetowania hasła. |
| Konto e-mail | Tylko uwierzytelnianie za pomocą resetowania hasła. Należy wybrać inną metodę na potrzeby weryfikacji dwuskładnikowej. |
| Pytania zabezpieczające | Tylko uwierzytelnianie za pomocą resetowania hasła. Należy wybrać inną metodę na potrzeby weryfikacji dwuskładnikowej. |

## <a name="set-up-phone-calls-from-the-security-info-page"></a>Konfigurowanie połączeń telefonicznych na stronie Informacje o zabezpieczeniach

W zależności od ustawień organizacji można używać połączeń telefonicznych jako jednej z metod informacji zabezpieczających.

>[!Note]
>Jeśli chcesz otrzymywać wiadomości tekstowe zamiast połączenia telefonicznego, wykonaj czynności opisane w artykule [Konfigurowanie informacji zabezpieczających do używania wiadomości tekstowych.](security-info-setup-text-msg.md)

### <a name="to-set-up-phone-calls"></a>Aby skonfigurować połączenia telefoniczne

1. Zaloguj się na swoje konto służbowe, https://myprofile.microsoft.com/ a następnie przejdź do strony.

    ![Strona Mój profil z wyróżnionymi linkami informacji o zabezpieczeniach](media/security-info/securityinfo-myprofile-page.png)

2. Wybierz **pozycję Informacje o zabezpieczeniach** z lewego okienka nawigacji lub z łącza w bloku Informacje o **zabezpieczeniach,** a następnie wybierz pozycję **Dodaj metodę** ze strony Informacje o **zabezpieczeniach.**

    ![Strona informacji o zabezpieczeniach z wyróżnioną opcją Dodaj metodę](media/security-info/securityinfo-myprofile-addmethod-page.png)

3. Na stronie **Dodawanie metody** wybierz pozycję **Telefon** z listy rozwijanej, a następnie wybierz pozycję **Dodaj**.

    ![Pole Dodaj metodę z wybraną wybraną popisem Telefon](media/security-info/securityinfo-myprofile-addphonetext.png)

4. Na stronie **Telefon** wpisz numer telefonu urządzenia przenośnego, wybierz pozycję **Zadzwoń do mnie,** a następnie wybierz pozycję **Dalej**.

    ![Dodawanie numeru telefonu i wybieranie połączeń telefonicznych](media/security-info/securityinfo-myprofile-phonecall-addnumber.png)

5. Odbierz weryfikacyjną rozmowę telefoniczną, wysłano na wprowadzony numer telefonu i postępuj zgodnie z instrukcjami.

    Strona zmieni się, aby pokazać swój sukces.

    ![Powiadomienie o sukcesie, połączenie numeru telefonu, możliwość odbierania połączeń telefonicznych i konta](media/security-info/securityinfo-myprofile-phonetext-success.png)

    Informacje zabezpieczające są aktualizowane i można użyć połączeń telefonicznych, aby zweryfikować swoją tożsamość podczas korzystania z weryfikacji dwuetapowej lub resetowania hasła. Jeśli chcesz, aby połączenia telefoniczne były metodą domyślną, zobacz sekcję [Zmienianie domyślnej metody informacji zabezpieczających](#change-your-default-security-info-method) tego artykułu.

## <a name="delete-phone-calls-from-your-security-info-methods"></a>Usuwanie połączeń telefonicznych z metod informacji zabezpieczających

Jeśli nie chcesz już używać połączeń telefonicznych jako metody informacji zabezpieczających, możesz usunąć je ze strony **Informacje o zabezpieczeniach.**

>[!Important]
>Jeśli usuniesz połączenia telefoniczne przez pomyłkę, nie ma sposobu, aby je cofnąć. Musisz dodać metodę ponownie, wykonując kroki opisane w sekcji [Konfigurowanie połączeń telefonicznych](#set-up-phone-calls-from-the-security-info-page) w tym artykule.

### <a name="to-delete-phone-calls"></a>Aby usunąć połączenia telefoniczne

1. Na stronie **Informacje o zabezpieczeniach** wybierz łącze **Usuń** obok opcji **Telefon.**

    ![Łącze, aby usunąć metodę telefonu z informacji zabezpieczających](media/security-info/securityinfo-myprofile-phonetext-delete.png)

2. Wybierz **pozycję Tak** w polu potwierdzenia, aby usunąć numer **telefonu.** Po usunięciu numeru telefonu zostanie on usunięty z informacji zabezpieczających i zniknie ze strony **Informacje o zabezpieczeniach.** Jeśli domyślną metodą jest **Telefon,** domyślna metoda zmieni się na inną dostępną metodę.

## <a name="change-your-default-security-info-method"></a>Zmienianie domyślnej metody informacji zabezpieczających

Jeśli chcesz, aby połączenia telefoniczne były domyślną metodą używaną podczas logowania się na konto służbowe lub szkolne przy użyciu weryfikacji dwuskładnikowej lub żądań resetowania hasła, możesz ustawić je na stronie **Informacje o zabezpieczeniach.**

### <a name="to-change-your-default-security-info-method"></a>Aby zmienić domyślną metodę informacji zabezpieczających

1. Na stronie **Informacje o zabezpieczeniach** wybierz łącze **Zmień** obok domyślnych informacji o **metodzie logowania.**

    ![Łącze zmień domyślną metodę logowania](media/security-info/securityinfo-myprofile-phonetext-defaultchange.png)

2. Z listy rozwijanej dostępnych metod wybierz **pozycję Telefon - zadzwoń*_(your_phone_number),_*** a następnie wybierz pozycję **Potwierdź**.

    ![Wybierz metodę domyślnego logowania](media/security-info/securityinfo-myprofile-phonecall-changeddefault.png)

    Domyślna metoda używana do zmiany logowania do **telefonu - połączenia (*_your_phone_number_*)**.

## <a name="additional-security-info-methods"></a>Dodatkowe metody informacji o zabezpieczeniach

Masz dodatkowe opcje, w jaki sposób twoja organizacja kontaktuje się z Tobą, aby zweryfikować swoją tożsamość, w zależności od tego, co próbujesz zrobić. Dostępne są następujące opcje:

- **Authenticator.** Pobierz aplikację uwierzytelniającego i użyj aplikacji uwierzytelniającego, aby uzyskać powiadomienie o zatwierdzeniu lub losowo wygenerowany kod zatwierdzenia do weryfikacji dwuetapowej lub resetowania hasła. Aby uzyskać instrukcje krok po kroku dotyczące konfigurowania i używania aplikacji Microsoft Authenticator, zobacz [Konfigurowanie informacji zabezpieczających do korzystania z aplikacji uwierzytelniającego](security-info-setup-auth-app.md).

- **Tekst urządzenia mobilnego.** Wpisz numer urządzenia mobilnego i otrzymasz sms-a z kodem, którego użyjesz do weryfikacji dwuetapowej lub resetowania hasła. Aby uzyskać instrukcje krok po kroku dotyczące weryfikacji tożsamości za pomocą wiadomości SMS, zobacz [Konfigurowanie informacji zabezpieczających do korzystania z wiadomości tekstowych (SMS).](security-info-setup-text-msg.md)

- **Klucz zabezpieczeń.** Zarejestruj klucz zabezpieczeń zgodny z firmą Microsoft i użyj go wraz z kodem PIN do weryfikacji dwuetapowej lub resetowania hasła. Aby uzyskać instrukcje krok po kroku dotyczące weryfikacji tożsamości za pomocą klucza zabezpieczeń, zobacz [Konfigurowanie informacji zabezpieczających w celu używania klucza zabezpieczeń](security-info-setup-security-key.md).

- **Adres e-mail.** Wprowadź służbowy adres e-mail, aby otrzymać wiadomość e-mail z prośbą o zresetowanie hasła. Ta opcja nie jest dostępna w przypadku weryfikacji dwuetapowej. Aby uzyskać instrukcje krok po kroku dotyczące konfigurowania poczty e-mail, zobacz [Konfigurowanie informacji zabezpieczających do korzystania z poczty e-mail](security-info-setup-email.md).

- **Pytania zabezpieczające.** Odpowiedz na niektóre pytania zabezpieczające utworzone przez administratora w organizacji. Ta opcja jest dostępna tylko w przypadku resetowania hasła, a nie weryfikacji dwuetapowej. Aby uzyskać instrukcje krok po kroku dotyczące konfigurowania pytań zabezpieczających, zobacz artykuł [Konfigurowanie informacji zabezpieczających do używania pytań zabezpieczających.](security-info-setup-questions.md)

    >[!Note]
    >Jeśli brakuje niektórych z tych opcji, jest to najprawdopodobniej, ponieważ organizacja nie zezwala na te metody. W takim przypadku należy wybrać dostępną metodę lub skontaktować się z administratorem, aby uzyskać dodatkową pomoc.

## <a name="next-steps"></a>Następne kroki

- Zagubione lub zapomniane hasło możesz zresetować w [portalu resetowania hasła](https://passwordreset.microsoftonline.com/) lub wykonując kroki przedstawione w artykule [Resetowanie hasła służbowego](active-directory-passwords-update-your-own-password.md).

- Wskazówki i pomoc dotyczące rozwiązywania problemów z logowaniem znajdują się w artykule [Nie można zalogować się na konto Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant).
