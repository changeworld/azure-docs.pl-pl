---
title: Skonfiguruj informacje o zabezpieczeniach (wersja zapoznawcza) do użycia w wiadomości SMS - usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Jak skonfigurować swoje informacje zabezpieczające, aby zweryfikować swoją tożsamość za pomocą wiadomości SMS i urządzenia przenośnego.
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
ms.openlocfilehash: ea9e4ae21ecc6538b33aed1566c10ddcd22b86c7
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60482251"
---
# <a name="set-up-security-info-preview-to-use-text-messaging"></a>Skonfiguruj informacje o zabezpieczeniach (wersja zapoznawcza) do użycia w wiadomości SMS
Możesz wykonać następujące kroki, aby dodać weryfikację two-Factor Authentication i metod resetowania hasła. Po skonfigurowaniu to po raz pierwszy, można powrócić do **zabezpieczające** strony Dodawanie, aktualizowanie lub usuwanie informacji zabezpieczających.

Po wyświetleniu monitu o skonfigurowanie tego numeru, natychmiast, po zalogowaniu się do swojego konta firmowego lub szkolnego, zobacz szczegółowe instrukcje zostały podane w [skonfigurować swoje informacje zabezpieczające z wiersza na stronie rejestracji](security-info-setup-signin.md) artykułu.

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

>[!Note]
>Jeśli nie widzisz opcji dla telefonu, istnieje możliwość, że Twoja organizacja nie zezwala na numer telefonu służy do identyfikacji. W takim przypadku należy wybrać inną metodę, lub skontaktuj się z administratorem, aby uzyskać dalszą pomoc.

## <a name="set-up-text-messages-from-the-security-info-page"></a>Konfigurowanie wiadomości SMS z stronę informacji zabezpieczających
W zależności od ustawień organizacji można użyć jednej z metod informacje użytkownika zabezpieczeń komunikatów tekstu. Opcji tekst komunikatu jest częścią opcji dla telefonu, więc będzie wszystko, co w taki sam sposób, jak ustawić o Twój numer telefonu, ale zamiast Microsoft Zadzwonimy do Ciebie, będzie zdecydujesz się używać wiadomości SMS.

>[!Note]
>Jeśli chcesz odbierać połączenie telefoniczne zamiast wiadomość SMS, wykonaj kroki opisane w [ustawiane informacje zabezpieczające, aby używać połączeń telefonicznych](security-info-setup-phone-number.md) artykułu.

### <a name="to-set-up-text-messages"></a>Aby skonfigurować wiadomości tekstowe

1. Zaloguj się do swojego konta firmowego lub szkolnego, a następnie przejdź do swojej https://myprofile.microsoft.com/ strony.

    ![Strona Moje profilu, pokazujący wyróżnione łączy informacje zabezpieczeń](media/security-info/securityinfo-myprofile-page.png)

2. Wybierz **zabezpieczające** z lewego okienka nawigacji lub linku w **zabezpieczające** zablokować, a następnie wybierz **Dodaj metodę** z **informacje zabezpieczające**  strony.

    ![Strona informacji zabezpieczających przy użyciu opcji Dodaj wyróżnione w — metoda](media/security-info/securityinfo-myprofile-addmethod-page.png)

3. Na **Dodaj metodę** wybierz opcję **Phone** z listy rozwijanej, a następnie wybierz **Dodaj**.

    ![Dodaj pole Metoda, za pomocą telefonu wybrane](media/security-info/securityinfo-myprofile-addphonetext.png)

4. Na **Phone** strony, wpisz numer telefonu dla urządzeń przenośnych, wybierz **tekstu do mnie kod**, a następnie wybierz pozycję **dalej**.

    ![Dodaj numer telefonu, a następnie wybierz wiadomości SMS](media/security-info/securityinfo-myprofile-phonetext-addnumber.png)

5. Wprowadź kod wysłany do użytkownika za pośrednictwem wiadomości SMS do Twojego urządzenia przenośnego, a następnie wybierz **dalej**.

    ![Dodaj numer telefonu, a następnie wybierz wiadomości SMS](media/security-info/securityinfo-myprofile-phonetext-entercode.png)

    Zmiany strony do wyświetlenia sukcesu.

    ![Powiadomienie o powodzeniu, połączenie telefonu numer, odebranie messags tekstu, a Twoje konto](media/security-info/securityinfo-myprofile-phonetext-success.png)

    Twoje informacje zabezpieczające są aktualizowane i można użyć tekstu wiadomości, aby zweryfikować swoją tożsamość w przypadku korzystania z resetowania dwuetapowej weryfikacji lub hasło. Jeśli chcesz wprowadzić tekst wiadomości metodą domyślną, zobacz [domyślną metodę informacje zabezpieczeń](#change-your-default-security-info-method) dalszej części tego artykułu.

## <a name="delete-text-messaging-from-your-security-info-methods"></a>Usuń tekst wiadomości z metody informacje zabezpieczeń
Jeśli chcesz już używać wiadomości SMS jako metoda informacje zabezpieczeń, możesz usunąć go z **zabezpieczające** strony.

>[!Important]
>Jeśli usuniesz przez pomyłkę wiadomości tekstowych, nie ma możliwości jej cofnąć. Musisz ponownie, Dodaj metodę czynności opisane w [skonfigurować wiadomości tekstowe](#set-up-text-messages-from-the-security-info-page) dalszej części tego artykułu.

### <a name="to-delete-text-messaging"></a>Aby usunąć wiadomości SMS

1. Na **zabezpieczające** wybierz opcję **Usuń** łącze obok **Phone** opcji.

    ![Łącze, aby usunąć telefonu i metody z zabezpieczające wiadomości tekstowych](media/security-info/securityinfo-myprofile-phonetext-delete.png)

2. Wybierz **tak** z okno dialogowe potwierdzenia, aby usunąć **Phone** numer. Po numer telefonu został usunięty, zostanie on usunięty z informacjom zabezpieczającym i znika ono z **zabezpieczające** strony. Jeśli **Phone** jest metodą domyślną, wartość domyślna zmieni się na innej metody dostępne.

## <a name="change-your-default-security-info-method"></a>Domyślną metodę informacje zabezpieczeń
Jeśli chcesz, aby tekst wiadomości jako domyślną metodę używaną podczas logowania do konta firmowego lub szkolnego, przy użyciu weryfikacji two-Factor Authentication, lub dla żądań resetowania hasła, można ustawić go z **zabezpieczające** strony.

### <a name="to-change-your-default-security-info-method"></a>Aby zmienić metodę informacje zabezpieczeń domyślne

1. Na **zabezpieczające** wybierz opcję **zmiany** łącze obok **domyślna metoda logowania** informacji.

    ![Łącze Zmień domyślną metodę logowania](media/security-info/securityinfo-myprofile-phonetext-defaultchange.png)

2. Wybierz **Phone — tekst ( *_your_phone_number_* )** z listy rozwijanej dostępnych metod, a następnie wybierz **Potwierdź**.

    ![Wybierz metodę logowania domyślne](media/security-info/securityinfo-myprofile-phonetext-changeddefault.png)

    Domyślną metodą używane do logowania zmiany **Phone — tekst ( *_your_phone_number_* )** .

## <a name="additional-security-info-methods"></a>Dodatkowe informacje o metody zabezpieczeń
Masz dodatkowe opcje jak kontakty organizacji możesz zweryfikować swoją tożsamość, oparte na tym, co możesz próby wykonania. Dostępne są następujące opcje:

- **Aplikacja Authenticator.** Pobierz i użyj aplikacji authenticator, aby uzyskać powiadomienie o zatwierdzeniu lub kod generowany losowo zatwierdzenia do resetowania dwuetapowej weryfikacji lub hasło. Aby uzyskać szczegółowe instrukcje dotyczące sposobu konfigurowania i używania aplikacji Microsoft Authenticator, zobacz [ustawić informacje zabezpieczające, aby użyć aplikacji authenticator](security-info-setup-auth-app.md).

- **Rozmowy telefoniczne pracy lub urządzenia przenośnego.** Wprowadź numer urządzenia przenośnego, a następnie uzyskać połączenie telefoniczne resetowania dwuetapowej weryfikacji lub hasło. Aby uzyskać szczegółowe instrukcje na temat zweryfikować swoją tożsamość za pomocą numeru telefonu, zobacz [ustawiane informacje zabezpieczające, aby używać połączeń telefonicznych](security-info-setup-phone-number.md).

- **Adres e-mail.** Wprowadź swój firmowy lub szkolny adres e-mail, aby otrzymywać wiadomość e-mail do resetowania hasła. Ta opcja jest dostępna na potrzeby weryfikacji dwuetapowej. Aby uzyskać szczegółowe instrukcje dotyczące sposobu konfigurowania swój adres e-mail, zobacz [ustawić informacje zabezpieczające, aby użyć adresu e-mail](security-info-setup-email.md).

- **Pytania zabezpieczające.** Odpowiedz na pytania zabezpieczeń, niektóre utworzone przez administratora w Twojej organizacji. Ta opcja jest dostępna tylko do resetowania hasła, a nie na potrzeby weryfikacji dwuetapowej. Aby uzyskać szczegółowe instrukcje dotyczące sposobu konfigurowania pytań zabezpieczających, zobacz [ustawić informacje zabezpieczające, aby użycie tych pytań zabezpieczających](security-info-setup-questions.md) artykułu.
    
    >[!Note]
    >Jeśli brakuje niektórych z tych opcji, prawdopodobnie najbardziej Twoja organizacja nie zezwala na jednej z tych metod. Jeśli jest to możliwe, należy wybrać metodę dostępne lub skontaktuj się z administratorem, aby uzyskać dalszą pomoc.

## <a name="next-steps"></a>Kolejne kroki

- Zagubione lub zapomniane hasło możesz zresetować w [portalu resetowania hasła](https://passwordreset.microsoftonline.com/) lub wykonując kroki przedstawione w artykule [Resetowanie hasła służbowego](user-help-reset-password.md).

- Wskazówki i pomoc dotyczące rozwiązywania problemów z logowaniem znajdują się w artykule [Nie można zalogować się na konto Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant).