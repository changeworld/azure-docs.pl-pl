---
title: Konfigurowanie adresu e-mail jako metody weryfikacji — Azure AD
description: Jak skonfigurować stronę informacje zabezpieczające (wersja zapoznawcza), aby zweryfikować tożsamość przy użyciu adresu e-mail jako metody weryfikacji.
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
ms.openlocfilehash: 9488b96dda15b0ffb794f83826dc299a5f397b77
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77064006"
---
# <a name="set-up-an-email-address-as-your-verification-method"></a>Konfigurowanie adresu e-mail jako metody weryfikacji

Aby dodać metodę resetowania hasła, wykonaj następujące czynności. Po skonfigurowaniu tej opcji po raz pierwszy można powrócić do strony **Informacje o zabezpieczeniach,** aby dodać, zaktualizować lub usunąć informacje o zabezpieczeniach.

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

>[!Note]
>Jeśli nie widzisz opcji poczty e-mail, jest możliwe, że twoja organizacja nie zezwala na użycie tej opcji dla metody resetowania hasła. W takim przypadku musisz wybrać inną metodę lub skontaktować się z działem pomocy technicznej organizacji, aby uzyskać więcej pomocy.

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

## <a name="set-up-your-email-address-from-the-security-info-page"></a>Konfigurowanie adresu e-mail na stronie Informacje o zabezpieczeniach

W zależności od ustawień organizacji można użyć adresu e-mail jako jednej z metod informacji zabezpieczających.

>[!Note]
>Zalecamy używanie adresu e-mail, który nie wymaga hasła sieciowego, aby uzyskać dostęp. Jeśli nie widzisz opcji wiadomości e-mail, możesz, że Twoja organizacja nie zezwala na używanie wiadomości e-mail do weryfikacji. W takim przypadku musisz wybrać inną metodę lub skontaktować się z administratorem, aby uzyskać więcej pomocy.

### <a name="to-set-up-your-email-address"></a>Aby skonfigurować adres e-mail

1. Zaloguj się na swoje konto służbowe, https://myprofile.microsoft.com/ a następnie przejdź do strony.

    ![Strona Mój profil z wyróżnionymi linkami informacji o zabezpieczeniach](media/security-info/securityinfo-myprofile-page.png)

2. Wybierz **pozycję Informacje o zabezpieczeniach** z lewego okienka nawigacji lub z łącza w bloku Informacje o **zabezpieczeniach,** a następnie wybierz pozycję **Dodaj metodę** ze strony Informacje o **zabezpieczeniach.**

    ![Strona informacji o zabezpieczeniach z wyróżnioną opcją Dodaj metodę](media/security-info/securityinfo-myprofile-addmethod-page.png)

3. Na stronie **Dodawanie metody** wybierz pozycję **Poczta e-mail** z listy rozwijanej, a następnie wybierz pozycję **Dodaj**.

    ![Pole Dodaj metodę z zaznaczoną posokiem e-mail](media/security-info/securityinfo-myprofile-addemail.png)

4. Na stronie **Poczta e-mail** wpisz swój alain@gmail.comadres e-mail (na przykład), a następnie wybierz pozycję **Dalej**.

    ![Dodawanie numeru telefonu i wybieranie połączeń telefonicznych](media/security-info/securityinfo-myprofile-emailaddress.png)

    >[!Important]
    >Ten adres e-mail nie może być twoją służbą ani służbą e-mail.

5. Wpisz kod wysłany na określony adres e-mail, a następnie wybierz przycisk **Dalej**.

    ![Dodawanie numeru telefonu i wybieranie wiadomości tekstowych](media/security-info/securityinfo-myprofile-emailcode.png)

    Twoje informacje zabezpieczające są aktualizowane i możesz użyć swojego adresu e-mail, aby zweryfikować swoją tożsamość podczas resetowania hasła.

## <a name="delete-your-email-address-from-your-security-info-methods"></a>Usuwanie adresu e-mail z metod informacji zabezpieczających

Jeśli nie chcesz już używać adresu e-mail jako metody informacji zabezpieczających, możesz usunąć go ze strony **Informacje o zabezpieczeniach.**

>[!Important]
>Jeśli usuniesz swój adres e-mail przez pomyłkę, nie ma sposobu, aby go cofnąć. Musisz dodać metodę ponownie, wykonując kroki opisane w sekcji [Konfigurowanie adresu e-mail](#set-up-your-email-address-from-the-security-info-page) w tym artykule.

### <a name="to-delete-your-email-address"></a>Aby usunąć swój adres e-mail

1. Na stronie **Informacje o zabezpieczeniach** wybierz łącze **Usuń** obok opcji **Poczta e-mail.**

    ![Łącze, aby usunąć metodę telefonu z informacji zabezpieczających](media/security-info/securityinfo-myprofile-emaildelete.png)

2. Wybierz **tak** w polu potwierdzenia, aby usunąć konto **e-mail.** Po usunięciu konta e-mail zostanie ono usunięte z informacji zabezpieczających i zniknie ze strony **Informacje o zabezpieczeniach.**

## <a name="additional-security-info-methods"></a>Dodatkowe metody informacji o zabezpieczeniach

Masz dodatkowe opcje, w jaki sposób twoja organizacja kontaktuje się z Tobą, aby zweryfikować swoją tożsamość, w zależności od tego, co próbujesz zrobić. Dostępne są następujące opcje:

- **Authenticator.** Pobierz aplikację uwierzytelniającego i użyj aplikacji uwierzytelniającego, aby uzyskać powiadomienie o zatwierdzeniu lub losowo wygenerowany kod zatwierdzenia do weryfikacji dwuetapowej lub resetowania hasła. Aby uzyskać instrukcje krok po kroku dotyczące konfigurowania i używania aplikacji Microsoft Authenticator, zobacz [Konfigurowanie informacji zabezpieczających do korzystania z aplikacji uwierzytelniającego](security-info-setup-auth-app.md).

- **Tekst urządzenia mobilnego.** Wpisz numer urządzenia mobilnego i otrzymasz sms-a z kodem, którego użyjesz do weryfikacji dwuetapowej lub resetowania hasła. Aby uzyskać instrukcje krok po kroku dotyczące weryfikacji tożsamości za pomocą wiadomości SMS, zobacz [Konfigurowanie informacji zabezpieczających do korzystania z wiadomości tekstowych (SMS).](security-info-setup-text-msg.md)

- **Połączenie telefoniczne z urządzeniem mobilnym lub służbowym.** Wprowadź numer urządzenia mobilnego i uzyskaj połączenie telefoniczne w celu weryfikacji dwuetapowej lub zresetowania hasła. Aby uzyskać instrukcje krok po kroku dotyczące weryfikacji tożsamości za pomocą numeru telefonu, zobacz [Konfigurowanie informacji zabezpieczających do korzystania z połączeń telefonicznych.](security-info-setup-phone-number.md)

- **Klucz zabezpieczeń.** Zarejestruj klucz zabezpieczeń zgodny z firmą Microsoft i użyj go wraz z kodem PIN do weryfikacji dwuetapowej lub resetowania hasła. Aby uzyskać instrukcje krok po kroku dotyczące weryfikacji tożsamości za pomocą klucza zabezpieczeń, zobacz [Konfigurowanie informacji zabezpieczających w celu używania klucza zabezpieczeń](security-info-setup-security-key.md).

- **Pytania zabezpieczające.** Odpowiedz na niektóre pytania zabezpieczające utworzone przez administratora w organizacji. Ta opcja jest dostępna tylko w przypadku resetowania hasła, a nie weryfikacji dwuetapowej. Aby uzyskać instrukcje krok po kroku dotyczące konfigurowania pytań zabezpieczających, zobacz artykuł [Konfigurowanie informacji zabezpieczających do używania pytań zabezpieczających.](security-info-setup-questions.md)

    >[!Note]
    >Jeśli brakuje niektórych z tych opcji, jest to najprawdopodobniej, ponieważ organizacja nie zezwala na te metody. W takim przypadku należy wybrać dostępną metodę lub skontaktować się z administratorem, aby uzyskać dodatkową pomoc.

## <a name="next-steps"></a>Następne kroki

- Zagubione lub zapomniane hasło możesz zresetować w [portalu resetowania hasła](https://passwordreset.microsoftonline.com/) lub wykonując kroki przedstawione w artykule [Resetowanie hasła służbowego](active-directory-passwords-update-your-own-password.md).

- Wskazówki i pomoc dotyczące rozwiązywania problemów z logowaniem znajdują się w artykule [Nie można zalogować się na konto Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant).
