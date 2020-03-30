---
title: Konfigurowanie pytań zabezpieczających jako metody weryfikacji — Usługa Azure AD
description: Jak skonfigurować stronę Informacje zabezpieczające (wersja zapoznawcza), aby zweryfikować swoją tożsamość przy użyciu wstępnie zdefiniowanych pytań zabezpieczających jako metody weryfikacji.
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
ms.openlocfilehash: 236f1e4e41488b926d9fc2e5e717e68090a0ed7b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063972"
---
# <a name="set-up-security-questions-as-your-verification-method"></a>Konfigurowanie pytań zabezpieczających jako metody weryfikacji

Aby dodać metodę resetowania hasła, wykonaj następujące czynności. Po skonfigurowaniu tej opcji po raz pierwszy można powrócić do strony **Informacje o zabezpieczeniach,** aby dodać, zaktualizować lub usunąć informacje o zabezpieczeniach.

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

>[!Note]
>Jeśli nie widzisz opcji pytań zabezpieczających, możliwe, że twoja organizacja nie zezwala na użycie tej opcji dla metody resetowania hasła. W takim przypadku musisz wybrać inną metodę lub skontaktować się z działem pomocy technicznej organizacji, aby uzyskać więcej pomocy.

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

## <a name="set-up-your-security-questions-from-the-security-info-page"></a>Konfigurowanie pytań zabezpieczających na stronie Informacje o zabezpieczeniach

W zależności od ustawień organizacji można wybrać i odpowiedzieć na kilka pytań zabezpieczających jako jedną z metod informacji zabezpieczających. Administrator konfiguruje liczbę pytań zabezpieczających, które musisz wybrać i odpowiedzieć.

Jeśli używasz pytań zabezpieczających, zalecamy używanie ich w połączeniu z inną metodą. Pytania zabezpieczające mogą być mniej bezpieczne niż inne metody, ponieważ niektóre osoby mogą znać odpowiedzi na Twoje pytania.

> [!Note]
> Pytania zabezpieczające są przechowywane prywatnie i bezpiecznie na obiekcie użytkownika w katalogu i mogą być odbierane tylko przez Użytkownika podczas rejestracji. Administrator nie może czytać ani modyfikować pytań ani odpowiedzi.
>
> Jeśli nie widzisz opcji pytań zabezpieczających, możesz, że Twoja organizacja nie zezwala na używanie pytań zabezpieczających do weryfikacji. W takim przypadku musisz wybrać inną metodę lub skontaktować się z administratorem, aby uzyskać więcej pomocy.
>
> Konta administratorów nie mogą używać pytań zabezpieczających jako metody resetowania hasła. Jeśli jesteś zalogowany jako konto na poziomie administratora, nie zobaczysz tych opcji.

### <a name="to-set-up-your-security-questions"></a>Aby skonfigurować pytania zabezpieczające

1. Zaloguj się na swoje konto służbowe, https://myprofile.microsoft.com/ a następnie przejdź do strony.

    ![Strona Mój profil z wyróżnionymi linkami informacji o zabezpieczeniach](media/security-info/securityinfo-myprofile-page.png)

2. Wybierz **pozycję Informacje o zabezpieczeniach** z lewego okienka nawigacji lub z łącza w bloku Informacje o **zabezpieczeniach,** a następnie wybierz pozycję **Dodaj metodę** ze strony Informacje o **zabezpieczeniach.**

    ![Strona informacji o zabezpieczeniach z wyróżnioną opcją Dodaj metodę](media/security-info/securityinfo-myprofile-addmethod-page.png)

3. Na stronie **Dodawanie metody** wybierz pozycję **Pytania zabezpieczeń** z listy rozwijanej, a następnie wybierz pozycję **Dodaj**.

    ![Pole Dodaj metodę z zaznaczonymi pytaniami zabezpieczającymi](media/security-info/securityinfo-myprofile-addquestions.png)

4. Na stronie **Pytania zabezpieczające** wybierz pozycję i odpowiedz na pytania zabezpieczające, a następnie wybierz pozycję **Zapisz**.

    ![Dodawanie numeru telefonu i wybieranie połączeń telefonicznych](media/security-info/securityinfo-myprofile-securityquestions.png)

    Informacje zabezpieczające są aktualizowane i można użyć pytań zabezpieczających, aby zweryfikować swoją tożsamość podczas resetowania hasła.

## <a name="delete-security-questions-from-your-security-info-methods"></a>Usuwanie pytań zabezpieczających z metod informacji zabezpieczających

Jeśli nie chcesz już używać pytań zabezpieczających jako metody informacji zabezpieczających, możesz usunąć je ze strony **Informacje o zabezpieczeniach.**

>[!Important]
>Jeśli przez pomyłkę usuniesz pytania zabezpieczające, nie ma możliwości ich cofnięcia. Należy dodać metodę ponownie, wykonując kroki opisane w sekcji [Konfigurowanie pytań zabezpieczających](#set-up-your-security-questions-from-the-security-info-page) w tym artykule.

### <a name="to-delete-your-security-questions"></a>Aby usunąć pytania zabezpieczające

1. Na stronie **Informacje o zabezpieczeniach** wybierz łącze **Usuń** obok opcji **Pytania zabezpieczające.**

    ![Łącze, aby usunąć metodę telefonu z informacji zabezpieczających](media/security-info/securityinfo-myprofile-questionsdelete.png)

2. Z pola potwierdzenia wybierz **opcję Tak,** aby usunąć **pytania zabezpieczające**. Po usunięciu pytań zabezpieczających metoda jest usuwana z informacji zabezpieczających i znika ze strony **Informacje o zabezpieczeniach.**

## <a name="additional-security-info-methods"></a>Dodatkowe metody informacji o zabezpieczeniach

Masz dodatkowe opcje, w jaki sposób twoja organizacja kontaktuje się z Tobą, aby zweryfikować swoją tożsamość, w zależności od tego, co próbujesz zrobić. Dostępne są następujące opcje:

- **Authenticator.** Pobierz aplikację uwierzytelniającego i użyj aplikacji uwierzytelniającego, aby uzyskać powiadomienie o zatwierdzeniu lub losowo wygenerowany kod zatwierdzenia do weryfikacji dwuetapowej lub resetowania hasła. Aby uzyskać instrukcje krok po kroku dotyczące konfigurowania i używania aplikacji Microsoft Authenticator, zobacz [Konfigurowanie informacji zabezpieczających do korzystania z aplikacji uwierzytelniającego](security-info-setup-auth-app.md).

- **Tekst urządzenia mobilnego.** Wpisz numer urządzenia mobilnego i otrzymasz sms-a z kodem, którego użyjesz do weryfikacji dwuetapowej lub resetowania hasła. Aby uzyskać instrukcje krok po kroku dotyczące weryfikacji tożsamości za pomocą wiadomości SMS, zobacz [Konfigurowanie informacji zabezpieczających do korzystania z wiadomości tekstowych (SMS).](security-info-setup-text-msg.md)

- **Połączenie telefoniczne z urządzeniem mobilnym lub służbowym.** Wprowadź numer urządzenia mobilnego i uzyskaj połączenie telefoniczne w celu weryfikacji dwuetapowej lub zresetowania hasła. Aby uzyskać instrukcje krok po kroku dotyczące weryfikacji tożsamości za pomocą numeru telefonu, zobacz [Konfigurowanie informacji zabezpieczających do korzystania z połączeń telefonicznych.](security-info-setup-phone-number.md)

- **Klucz zabezpieczeń.** Zarejestruj klucz zabezpieczeń zgodny z firmą Microsoft i użyj go wraz z kodem PIN do weryfikacji dwuetapowej lub resetowania hasła. Aby uzyskać instrukcje krok po kroku dotyczące weryfikacji tożsamości za pomocą klucza zabezpieczeń, zobacz [Konfigurowanie informacji zabezpieczających w celu używania klucza zabezpieczeń](security-info-setup-security-key.md).

- **Adres e-mail.** Wprowadź służbowy adres e-mail, aby otrzymać wiadomość e-mail z prośbą o zresetowanie hasła. Ta opcja nie jest dostępna w przypadku weryfikacji dwuetapowej. Aby uzyskać instrukcje krok po kroku dotyczące konfigurowania poczty e-mail, zobacz [Konfigurowanie informacji zabezpieczających do korzystania z poczty e-mail](security-info-setup-email.md).

    >[!Note]
    >Jeśli brakuje niektórych z tych opcji, jest to najprawdopodobniej, ponieważ organizacja nie zezwala na te metody. W takim przypadku należy wybrać dostępną metodę lub skontaktować się z administratorem, aby uzyskać dodatkową pomoc.

## <a name="next-steps"></a>Następne kroki

- Zagubione lub zapomniane hasło możesz zresetować w [portalu resetowania hasła](https://passwordreset.microsoftonline.com/) lub wykonując kroki przedstawione w artykule [Resetowanie hasła służbowego](active-directory-passwords-update-your-own-password.md).

- Wskazówki i pomoc dotyczące rozwiązywania problemów z logowaniem znajdują się w artykule [Nie można zalogować się na konto Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant).
