---
title: Ustawić informacje o zabezpieczeniach (wersja zapoznawcza), aby użyć adresu e-mail — usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Jak skonfigurować swoje informacje zabezpieczające, aby zweryfikować swoją tożsamość za pomocą adresu e-mail.
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
ms.openlocfilehash: 720aafac79a67f64b0974dba0dd60c6aa24a8c54
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60480625"
---
# <a name="set-up-security-info-preview-to-use-your-email-address"></a>Ustawić informacje o zabezpieczeniach (wersja zapoznawcza), aby użyć Twojego adresu e-mail
Można wykonaj następujące kroki, aby dodać metodę resetowania hasła. Po skonfigurowaniu to po raz pierwszy, można powrócić do **zabezpieczające** strony Dodawanie, aktualizowanie lub usuwanie informacji zabezpieczających.

Po skonfigurowaniu metody resetowania hasła, należy również skonfigurować metodę weryfikacji two-Factor Authentication, za pomocą [aplikacji authenticator](security-info-setup-auth-app.md), [wiadomości tekstowych](security-info-setup-text-msg.md), lub [połączeń telefonicznych](security-info-setup-phone-number.md).

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

## <a name="set-up-your-email-address-from-the-security-info-page"></a>Skonfiguruj swój adres e-mail z stronę informacji zabezpieczających
W zależności od ustawień organizacji można Użyj swojego adresu e-mail jako jeden z metody zabezpieczeń informacji.

>[!Note]
>Zaleca się przy użyciu adresu e-mail, która nie wymaga hasła dostępu do sieci. Jeśli nie widzisz opcji wiadomości e-mail, istnieje możliwość, że Twoja organizacja nie zezwala na użyć wiadomość e-mail na potrzeby weryfikacji. Jeśli jest to możliwe, należy wybrać inną metodę, lub skontaktuj się z administratorem, aby uzyskać dalszą pomoc.

### <a name="to-set-up-your-email-address"></a>Aby skonfigurować adres e-mail

1. Zaloguj się do swojego konta firmowego lub szkolnego, a następnie przejdź do swojej https://myprofile.microsoft.com/ strony.

    ![Strona Moje profilu, pokazujący wyróżnione łączy informacje zabezpieczeń](media/security-info/securityinfo-myprofile-page.png)

2. Wybierz **zabezpieczające** z lewego okienka nawigacji lub linku w **zabezpieczające** zablokować, a następnie wybierz **Dodaj metodę** z **informacje zabezpieczające**  strony.

    ![Strona informacji zabezpieczających przy użyciu opcji Dodaj wyróżnione w — metoda](media/security-info/securityinfo-myprofile-addmethod-page.png)

3. Na **Dodaj metodę** wybierz opcję **E-mail** z listy rozwijanej, a następnie wybierz **Dodaj**.

    ![Dodaj pole Metoda, za pomocą adresu e-mail wybrane](media/security-info/securityinfo-myprofile-addemail.png)

4. Na **E-mail** wpisz swój adres e-mail (na przykład alain@gmail.com), a następnie wybierz pozycję **dalej**.

    ![Dodaj numer telefonu, a następnie wybierz połączeń telefonicznych](media/security-info/securityinfo-myprofile-emailaddress.png)

    >[!Important]
    >Ten adres e-mail nie może być służbowy adres e-mail.

5. Wprowadź kod wysłany na adres e-mail określony, a następnie wybierz **dalej**.

    ![Dodaj numer telefonu, a następnie wybierz wiadomości SMS](media/security-info/securityinfo-myprofile-emailcode.png)

    Twoje informacje zabezpieczające są aktualizowane i swój adres e-mail można użyć, aby zweryfikować swoją tożsamość w przypadku korzystania z resetowania hasła.

## <a name="delete-your-email-address-from-your-security-info-methods"></a>Usunąć swój adres e-mail z metody informacje zabezpieczeń
Jeśli nie chcesz już Użyj swojego adresu e-mail jako metoda informacje zabezpieczeń, możesz usunąć go z **zabezpieczające** strony.

>[!Important]
>Jeśli Twój adres e-mail zostanie usunięty przez pomyłkę, nie ma możliwości jej cofnąć. Musisz ponownie, Dodaj metodę czynności opisane w [skonfigurować swój adres e-mail](#set-up-your-email-address-from-the-security-info-page) dalszej części tego artykułu.

### <a name="to-delete-your-email-address"></a>Aby usunąć swój adres e-mail

1. Na **informacje zabezpieczające** wybierz opcję **Usuń** łącze obok **E-mail** opcji.

    ![Łącze, aby usunąć metodę telefonu z informacje zabezpieczające](media/security-info/securityinfo-myprofile-emaildelete.png)

2. Wybierz **tak** z okno dialogowe potwierdzenia, aby usunąć **E-mail** konta. Po usunięciu konta e-mail, zostanie on usunięty z informacjom zabezpieczającym i znika ono z **zabezpieczające** strony.

## <a name="additional-security-info-methods"></a>Dodatkowe informacje o metody zabezpieczeń
Masz dodatkowe opcje jak kontakty organizacji możesz zweryfikować swoją tożsamość, oparte na tym, co możesz próby wykonania. Dostępne są następujące opcje:

- **Aplikacja Authenticator.** Pobierz i użyj aplikacji authenticator, aby uzyskać powiadomienie o zatwierdzeniu lub kod generowany losowo zatwierdzenia do resetowania dwuetapowej weryfikacji lub hasło. Aby uzyskać szczegółowe instrukcje dotyczące sposobu konfigurowania i używania aplikacji Microsoft Authenticator, zobacz [ustawić informacje zabezpieczające, aby użyć aplikacji authenticator](security-info-setup-auth-app.md).

- **Tekst urządzenia przenośnego.** Wprowadź numer urządzenia przenośnego, a następnie otrzymaj wiadomość SMS, kod służący do celów weryfikacji dwuetapowej lub hasło resetowania. Aby uzyskać szczegółowe instrukcje na temat zweryfikować swoją tożsamość za pomocą wiadomości SMS (SMS), zobacz [ustawiane informacje zabezpieczające, aby używać wiadomości tekstowych (SMS)](security-info-setup-text-msg.md).

- **Rozmowy telefoniczne pracy lub urządzenia przenośnego.** Wprowadź numer urządzenia przenośnego, a następnie uzyskać połączenie telefoniczne resetowania dwuetapowej weryfikacji lub hasło. Aby uzyskać szczegółowe instrukcje na temat zweryfikować swoją tożsamość za pomocą numeru telefonu, zobacz [ustawiane informacje zabezpieczające, aby używać połączeń telefonicznych](security-info-setup-phone-number.md).

- **Pytania zabezpieczające.** Odpowiedz na pytania zabezpieczeń, niektóre utworzone przez administratora w Twojej organizacji. Ta opcja jest dostępna tylko do resetowania hasła, a nie na potrzeby weryfikacji dwuetapowej. Aby uzyskać szczegółowe instrukcje dotyczące sposobu konfigurowania pytań zabezpieczających, zobacz [ustawić informacje zabezpieczające, aby użycie tych pytań zabezpieczających](security-info-setup-questions.md) artykułu.
    
    >[!Note]
    >Jeśli brakuje niektórych z tych opcji, prawdopodobnie najbardziej Twoja organizacja nie zezwala na jednej z tych metod. Jeśli jest to możliwe, należy wybrać metodę dostępne lub skontaktuj się z administratorem, aby uzyskać dalszą pomoc.

## <a name="next-steps"></a>Kolejne kroki

- Zagubione lub zapomniane hasło możesz zresetować w [portalu resetowania hasła](https://passwordreset.microsoftonline.com/) lub wykonując kroki przedstawione w artykule [Resetowanie hasła służbowego](user-help-reset-password.md).

- Wskazówki i pomoc dotyczące rozwiązywania problemów z logowaniem znajdują się w artykule [Nie można zalogować się na konto Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant).
