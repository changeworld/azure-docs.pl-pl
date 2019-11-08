---
title: Konfigurowanie informacji o zabezpieczeniach (wersja zapoznawcza) na potrzeby pytań zabezpieczających — usługa Azure AD
description: Jak skonfigurować informacje zabezpieczające, aby zweryfikować Twoją tożsamość przy użyciu wstępnie zdefiniowanych pytań zabezpieczających.
services: active-directory
author: eross-msft
manager: daveba
ms.reviewer: sahenry
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 02/13/2019
ms.author: lizross
ms.collection: M365-identity-device-management
ms.openlocfilehash: c2513b52c347db428b5ac0236a2561e5d4f1cdd2
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73820303"
---
# <a name="set-up-security-info-preview-to-use-security-questions"></a>Skonfiguruj informacje zabezpieczające (wersja zapoznawcza), aby korzystać z pytań zabezpieczających

Poniższe kroki umożliwiają dodanie metody resetowania hasła. Po wybraniu tej opcji po raz pierwszy możesz wrócić do strony **informacje zabezpieczające** , aby dodać, zaktualizować lub usunąć informacje o zabezpieczeniach.

Po skonfigurowaniu metody resetowania hasła należy również skonfigurować metodę weryfikacji dwuskładnikowej przy użyciu [aplikacji uwierzytelniającej](security-info-setup-auth-app.md), [wiadomości tekstowych](security-info-setup-text-msg.md)lub [połączenia telefonicznego](security-info-setup-phone-number.md).

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

## <a name="set-up-your-security-questions-from-the-security-info-page"></a>Konfigurowanie pytań zabezpieczających na stronie informacje zabezpieczające

W zależności od ustawień organizacji może być możliwe wybranie i odpowiedzenie kilku pytań zabezpieczających jako jednej z metod informacji zabezpieczających. Administrator konfiguruje liczbę pytań zabezpieczających, które są wymagane do wyboru i odpowiedzi.

Jeśli używasz pytań zabezpieczających, zalecamy ich użycie w połączeniu z inną metodą. Pytania zabezpieczające mogą być mniej bezpieczne niż inne metody, ponieważ niektóre osoby mogą znać odpowiedzi na pytania.

> [!Note]
> Pytania zabezpieczające są przechowywane prywatnie i bezpiecznie w obiekcie użytkownika w katalogu i mogą być przez Ciebie odbierane tylko przez użytkownika podczas rejestracji. Administrator nie ma możliwości odczytywania lub modyfikowania pytań lub odpowiedzi.
>
> Jeśli nie widzisz opcji pytania zabezpieczające, być może Twoja organizacja nie zezwala na używanie pytań zabezpieczających do weryfikacji. W takim przypadku należy wybrać inną metodę lub skontaktować się z administratorem w celu uzyskania dodatkowej pomocy.
>
> Konta administratorów nie mogą używać pytań zabezpieczających jako metody resetowania hasła. Jeśli użytkownik jest zalogowany jako konto na poziomie administratora, te opcje nie będą wyświetlane.

### <a name="to-set-up-your-security-questions"></a>Aby skonfigurować pytania zabezpieczające

1. Zaloguj się do swojego konta służbowego, a następnie przejdź na stronę https://myprofile.microsoft.com/.

    ![Strona mój profil zawierająca wyróżnione linki do informacji zabezpieczających](media/security-info/securityinfo-myprofile-page.png)

2. W okienku nawigacji po lewej stronie wybierz pozycję **informacje zabezpieczające** lub link w bloku **informacje zabezpieczające** , a następnie wybierz pozycję **Dodaj metodę** ze strony **informacje zabezpieczające** .

    ![Strona z informacjami o zabezpieczeniach z wyróżnioną opcją dodawania metody](media/security-info/securityinfo-myprofile-addmethod-page.png)

3. Na stronie **Dodawanie metody** wybierz z listy rozwijanej pozycję **pytania zabezpieczające** , a następnie wybierz pozycję **Dodaj**.

    ![Dodaj pole metody z wybranymi pytaniami zabezpieczeń](media/security-info/securityinfo-myprofile-addquestions.png)

4. Na stronie **pytania zabezpieczające** wybierz i Odpowiedz na pytania zabezpieczające, a następnie wybierz pozycję **Zapisz**.

    ![Dodawanie numeru telefonu i Wybieranie połączeń telefonicznych](media/security-info/securityinfo-myprofile-securityquestions.png)

    Twoje informacje zabezpieczające zostaną zaktualizowane i możesz użyć pytań zabezpieczających, aby zweryfikować swoją tożsamość w przypadku korzystania z funkcji resetowania haseł.

## <a name="delete-security-questions-from-your-security-info-methods"></a>Usuń pytania zabezpieczające z metod informacji zabezpieczających

Jeśli nie chcesz już korzystać z pytania zabezpieczającego jako metody informacji zabezpieczających, możesz je usunąć ze strony **Informacje o zabezpieczeniach** .

>[!Important]
>Jeśli usuniesz pytania zabezpieczające przez pomyłkę, nie ma możliwości jego cofnięcia. Musisz ponownie dodać metodę, wykonując czynności opisane w sekcji [Konfigurowanie pytań zabezpieczających](#set-up-your-security-questions-from-the-security-info-page) w tym artykule.

### <a name="to-delete-your-security-questions"></a>Aby usunąć pytania zabezpieczające

1. Na stronie **informacje zabezpieczające** wybierz łącze **Usuń** obok opcji **pytania zabezpieczające** .

    ![Link umożliwiający usunięcie metody telefonu z informacji zabezpieczających](media/security-info/securityinfo-myprofile-questionsdelete.png)

2. Wybierz opcję **tak** w polu potwierdzenia, aby usunąć **pytania zabezpieczające**. Po usunięciu pytań zabezpieczających Metoda zostanie usunięta z informacji zabezpieczających i zniknie ze strony z **informacjami o zabezpieczeniach** .

## <a name="additional-security-info-methods"></a>Dodatkowe metody informacji zabezpieczających

Dostępne są dodatkowe opcje dotyczące sposobu, w jaki Twoja organizacja kontaktuje się z Twoją tożsamością, w oparciu o to, co you're próbę wykonania. Dostępne są następujące opcje:

- **Aplikacja uwierzytelniania.** Pobierz aplikację Authenticator i użyj jej do uzyskania powiadomienia o zatwierdzeniu lub losowo wygenerowanego kodu zatwierdzenia na potrzeby weryfikacji dwuetapowej lub resetowania hasła. Instrukcje krok po kroku dotyczące konfigurowania i używania aplikacji Microsoft Authenticator można znaleźć w temacie [set up Security Info to use the Authenticator App](security-info-setup-auth-app.md).

- **Tekst urządzenia przenośnego.** Wprowadź numer urządzenia przenośnego i uzyskaj tekst kodu, który będzie używany do weryfikacji dwuetapowej lub resetowania hasła. Aby uzyskać instrukcje krok po kroku dotyczące weryfikowania tożsamości za pomocą wiadomości tekstowych (SMS), zobacz [Konfigurowanie informacji zabezpieczających do używania wiadomości](security-info-setup-text-msg.md)SMS.

- **Urządzenie przenośne lub połączenie telefoniczne.** Wprowadź numer urządzenia przenośnego i otrzymaj połączenie telefoniczne w celu weryfikacji dwuetapowej lub resetowania hasła. Aby uzyskać instrukcje krok po kroku dotyczące weryfikowania tożsamości przy użyciu numeru telefonu, zobacz [Konfigurowanie informacji zabezpieczających do korzystania z połączeń telefonicznych](security-info-setup-phone-number.md).

- **Klucz zabezpieczeń.** Zarejestruj klucz zabezpieczeń zgodny z firmą Microsoft i użyj go wraz z numerem PIN w celu weryfikacji dwuetapowej lub resetowania hasła. Instrukcje krok po kroku dotyczące weryfikowania tożsamości przy użyciu klucza zabezpieczeń znajdują się w temacie [set up Security Info to use a Security Key](security-info-setup-security-key.md).

- **Adres e-mail.** Wprowadź służbowy adres e-mail, aby uzyskać wiadomość e-mail na potrzeby resetowania hasła. Ta opcja jest niedostępna w przypadku weryfikacji dwuetapowej. Aby uzyskać instrukcje krok po kroku dotyczące konfigurowania poczty e-mail, zobacz [Konfigurowanie informacji zabezpieczających do używania poczty e-mail](security-info-setup-email.md).

    >[!Note]
    >Jeśli brakuje niektórych z tych opcji, prawdopodobnie organizacja nie zezwala na te metody. W takim przypadku należy wybrać dostępną metodę lub skontaktować się z administratorem w celu uzyskania pomocy.

## <a name="next-steps"></a>Następne kroki

- Zagubione lub zapomniane hasło możesz zresetować w [portalu resetowania hasła](https://passwordreset.microsoftonline.com/) lub wykonując kroki przedstawione w artykule [Resetowanie hasła służbowego](active-directory-passwords-update-your-own-password.md).

- Wskazówki i pomoc dotyczące rozwiązywania problemów z logowaniem znajdują się w artykule [Nie można zalogować się na konto Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant).
