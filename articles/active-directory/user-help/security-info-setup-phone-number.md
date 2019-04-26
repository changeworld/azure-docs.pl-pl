---
title: Skonfiguruj informacje o zabezpieczeniach (wersja zapoznawcza) do użycia połączeń telefonicznych — usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Jak skonfigurować swoje informacje zabezpieczające, aby zweryfikować swoją tożsamość za pomocą połączeń telefonicznych.
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
ms.openlocfilehash: 9c1620be30d8cdf3a592ab0fc118938783579689
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60474997"
---
# <a name="set-up-security-info-preview-to-use-phone-calls"></a>Skonfiguruj informacje o zabezpieczeniach (wersja zapoznawcza) do użycia połączeń telefonicznych
Możesz wykonać następujące kroki, aby dodać weryfikację two-Factor Authentication i metod resetowania hasła. Po skonfigurowaniu to po raz pierwszy, można powrócić do **zabezpieczające** strony Dodawanie, aktualizowanie lub usuwanie informacji zabezpieczających.

Po wyświetleniu monitu o skonfigurowanie tego numeru, natychmiast, po zalogowaniu się do swojego konta firmowego lub szkolnego, zobacz szczegółowe instrukcje zostały podane w [skonfigurować swoje informacje zabezpieczające z wiersza na stronie rejestracji](security-info-setup-signin.md) artykułu.

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

> [!Note]
> Informacje o zabezpieczeniach nie obsługuje wewnętrzne numery telefonów. Nawet jeśli dodasz prawidłowego formatu, 4255551234 + 1 X 12345, rozszerzenia zostaną usunięte przed wykonaniem wywołania.
> 
> Jeśli nie widzisz opcji dla telefonu, istnieje możliwość, że Twoja organizacja nie zezwala na numer telefonu służy do identyfikacji. W takim przypadku należy wybrać inną metodę, lub skontaktuj się z administratorem, aby uzyskać dalszą pomoc.

## <a name="set-up-phone-calls-from-the-security-info-page"></a>Konfigurowanie połączeń telefonicznych z stronę informacji zabezpieczających
W zależności od ustawień organizacji można użyć połączeń telefonicznych jako jeden z metody zabezpieczeń informacji.

>[!Note]
>Jeśli chcesz otrzymywać wiadomość SMS, a nie połączenie telefoniczne, wykonaj kroki opisane w [ustawiane informacje zabezpieczające, aby używać wiadomości SMS](security-info-setup-text-msg.md) artykułu.

### <a name="to-set-up-phone-calls"></a>Aby skonfigurować połączeń telefonicznych

1. Zaloguj się do swojego konta firmowego lub szkolnego, a następnie przejdź do swojej https://myprofile.microsoft.com/ strony.

    ![Strona Moje profilu, pokazujący wyróżnione łączy informacje zabezpieczeń](media/security-info/securityinfo-myprofile-page.png)

2. Wybierz **zabezpieczające** z lewego okienka nawigacji lub linku w **zabezpieczające** zablokować, a następnie wybierz **Dodaj metodę** z **informacje zabezpieczające**  strony.

    ![Strona informacji zabezpieczających przy użyciu opcji Dodaj wyróżnione w — metoda](media/security-info/securityinfo-myprofile-addmethod-page.png)

3. Na **Dodaj metodę** wybierz opcję **Phone** z listy rozwijanej, a następnie wybierz **Dodaj**.

    ![Dodaj pole Metoda, za pomocą telefonu wybrane](media/security-info/securityinfo-myprofile-addphonetext.png)

4. Na **Phone** strony, wpisz numer telefonu dla urządzeń przenośnych, wybierz **Zadzwoń do mnie**, a następnie wybierz pozycję **dalej**.

    ![Dodaj numer telefonu, a następnie wybierz połączeń telefonicznych](media/security-info/securityinfo-myprofile-phonecall-addnumber.png)

5. Odpowiedzi na połączenie telefoniczne weryfikacji, wysyłanych na numer telefonu, które zostały wprowadzone i postępuj zgodnie z instrukcjami.

    Zmiany strony do wyświetlenia sukcesu.

    ![Powiadomienie o powodzeniu, połączenie telefonu numer, odebranie połączenia telefoniczne i konta](media/security-info/securityinfo-myprofile-phonetext-success.png)

    Twoje informacje zabezpieczające są aktualizowane i połączeń telefonicznych można użyć, aby zweryfikować swoją tożsamość w przypadku korzystania z resetowania dwuetapowej weryfikacji lub hasło. Jeśli chcesz wykonywać połączeń telefonicznych metodą domyślną, zobacz [domyślną metodę informacje zabezpieczeń](#change-your-default-security-info-method) dalszej części tego artykułu.

## <a name="delete-phone-calls-from-your-security-info-methods"></a>Usuwanie połączeń telefonicznych z metody informacje zabezpieczeń
Jeśli chcesz już używać połączeń telefonicznych jako metoda informacje zabezpieczeń, możesz usunąć go z **zabezpieczające** strony.

>[!Important]
>Połączeń telefonicznych zostanie usunięty przez pomyłkę, czy można go cofnąć. Musisz ponownie, Dodaj metodę czynności opisane w [Konfigurowanie połączeń telefonicznych](#set-up-phone-calls-from-the-security-info-page) dalszej części tego artykułu.

### <a name="to-delete-phone-calls"></a>Aby usunąć połączeń telefonicznych

1. Na **zabezpieczające** wybierz opcję **Usuń** łącze obok **Phone** opcji.

    ![Łącze, aby usunąć metodę telefonu z informacje zabezpieczające](media/security-info/securityinfo-myprofile-phonetext-delete.png)

2. Wybierz **tak** z okno dialogowe potwierdzenia, aby usunąć **Phone** numer. Po numer telefonu został usunięty, zostanie on usunięty z informacjom zabezpieczającym i znika ono z **zabezpieczające** strony. Jeśli **Phone** jest metodą domyślną, wartość domyślna zmieni się na innej metody dostępne.
    
## <a name="change-your-default-security-info-method"></a>Domyślną metodę informacje zabezpieczeń
Jeśli chcesz połączenia telefoniczne jako domyślną metodę używaną podczas logowania do konta firmowego lub szkolnego, przy użyciu weryfikacji two-Factor Authentication, lub dla żądań resetowania hasła, można ustawić go z **zabezpieczające** strony.

### <a name="to-change-your-default-security-info-method"></a>Aby zmienić metodę informacje zabezpieczeń domyślne

1. Na **zabezpieczające** wybierz opcję **zmiany** łącze obok **domyślna metoda logowania** informacji.

    ![Łącze Zmień domyślną metodę logowania](media/security-info/securityinfo-myprofile-phonetext-defaultchange.png)

2. Wybierz **Phone — wywołania (*_your_phone_number_*)** z listy rozwijanej dostępnych metod, a następnie wybierz **Potwierdź**.

    ![Wybierz metodę logowania domyślne](media/security-info/securityinfo-myprofile-phonecall-changeddefault.png)

    Domyślną metodą używane do logowania zmiany **Phone — wywołania (*_your_phone_number_*)**.

## <a name="additional-security-info-methods"></a>Dodatkowe informacje o metody zabezpieczeń
Masz dodatkowe opcje jak kontakty organizacji możesz zweryfikować swoją tożsamość, oparte na tym, co możesz próby wykonania. Dostępne są następujące opcje:

- **Aplikacja Authenticator.** Pobierz i użyj aplikacji authenticator, aby uzyskać powiadomienie o zatwierdzeniu lub kod generowany losowo zatwierdzenia do resetowania dwuetapowej weryfikacji lub hasło. Aby uzyskać szczegółowe instrukcje dotyczące sposobu konfigurowania i używania aplikacji Microsoft Authenticator, zobacz [ustawić informacje zabezpieczające, aby użyć aplikacji authenticator](security-info-setup-auth-app.md).

- **Tekst urządzenia przenośnego.** Wprowadź numer urządzenia przenośnego, a następnie otrzymaj wiadomość SMS, kod służący do celów weryfikacji dwuetapowej lub hasło resetowania. Aby uzyskać szczegółowe instrukcje na temat zweryfikować swoją tożsamość za pomocą wiadomości SMS (SMS), zobacz [ustawiane informacje zabezpieczające, aby używać wiadomości tekstowych (SMS)](security-info-setup-text-msg.md).

- **Adres e-mail.** Wprowadź swój firmowy lub szkolny adres e-mail, aby otrzymywać wiadomość e-mail do resetowania hasła. Ta opcja jest dostępna na potrzeby weryfikacji dwuetapowej. Aby uzyskać szczegółowe instrukcje dotyczące sposobu konfigurowania swój adres e-mail, zobacz [ustawić informacje zabezpieczające, aby użyć adresu e-mail](security-info-setup-email.md).

- **Pytania zabezpieczające.** Odpowiedz na pytania zabezpieczeń, niektóre utworzone przez administratora w Twojej organizacji. Ta opcja jest dostępna tylko do resetowania hasła, a nie na potrzeby weryfikacji dwuetapowej. Aby uzyskać szczegółowe instrukcje dotyczące sposobu konfigurowania pytań zabezpieczających, zobacz [ustawić informacje zabezpieczające, aby użycie tych pytań zabezpieczających](security-info-setup-questions.md) artykułu.
    
    >[!Note]
    >Jeśli brakuje niektórych z tych opcji, prawdopodobnie najbardziej Twoja organizacja nie zezwala na jednej z tych metod. Jeśli jest to możliwe, należy wybrać metodę dostępne lub skontaktuj się z administratorem, aby uzyskać dalszą pomoc.

## <a name="next-steps"></a>Kolejne kroki

- Zagubione lub zapomniane hasło możesz zresetować w [portalu resetowania hasła](https://passwordreset.microsoftonline.com/) lub wykonując kroki przedstawione w artykule [Resetowanie hasła służbowego](user-help-reset-password.md).

- Wskazówki i pomoc dotyczące rozwiązywania problemów z logowaniem znajdują się w artykule [Nie można zalogować się na konto Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant).