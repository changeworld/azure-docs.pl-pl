---
title: Ustawić informacje o zabezpieczeniach (wersja zapoznawcza), aby użyć aplikacji authenticator — usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Jak skonfigurować swoje informacje zabezpieczające, aby zweryfikować swoją tożsamość za pomocą aplikacji Microsoft Authenticator.
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
ms.openlocfilehash: b797cc6e1c0ef0df61c4d1384d2beb12c37112ba
ms.sourcegitcommit: 75fef8147209a1dcdc7573c4a6a90f0151a12e17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/20/2019
ms.locfileid: "56456864"
---
# <a name="set-up-security-info-preview-to-use-an-authenticator-app"></a>Ustawić informacje o zabezpieczeniach (wersja zapoznawcza), aby użyć aplikacji authenticator
Możesz wykonać następujące kroki, aby dodać weryfikację two-Factor Authentication i metod resetowania hasła. Po skonfigurowaniu to po raz pierwszy, można powrócić do **zabezpieczające** strony Dodawanie, aktualizowanie lub usuwanie informacji zabezpieczających.

Po wyświetleniu monitu o skonfigurowanie tego numeru, natychmiast, po zalogowaniu się do swojego konta firmowego lub szkolnego, zobacz szczegółowe instrukcje zostały podane w [skonfigurować swoje informacje zabezpieczające z wiersza na stronie rejestracji](security-info-setup-signin.md) artykułu.

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

>[!Note]
>Jeśli nie widzisz opcji aplikacji wystawcy uwierzytelnienia jest możliwe, że Twoja organizacja nie zezwala na korzystanie z aplikacji uwierzytelniania do weryfikacji. W takim przypadku należy wybrać inną metodę, lub skontaktuj się z administratorem, aby uzyskać dalszą pomoc.

## <a name="set-up-the-microsoft-authenticator-app-from-the-security-info-page"></a>Konfigurowanie aplikacji Microsoft Authenticator z poziomu stronę informacji zabezpieczających
W zależności od ustawień organizacji można skorzystać z aplikacji uwierzytelniania jako jeden z metody zabezpieczeń informacji. Nie są wymagane do korzystania z aplikacji Microsoft Authenticator i można wybrać inną aplikację, podczas konfigurowania procesu. Jednak w tym artykule używa aplikacji Microsoft Authenticator. 

### <a name="to-set-up-the-microsoft-authenticator-app"></a>Aby skonfigurować aplikację Microsoft Authenticator

1. Zaloguj się do swojego konta firmowego lub szkolnego, a następnie przejdź do swojej https://myprofile.microsoft.com/ strony.

    ![Strona Moje profilu, pokazujący wyróżnione łączy informacje zabezpieczeń](media/security-info/securityinfo-myprofile.png)

2. Wybierz **zabezpieczające** z lewego okienka nawigacji lub linku w **zabezpieczające** zablokować, a następnie wybierz **Dodaj metodę** z **informacje zabezpieczające**  strony.

    ![Strona informacji zabezpieczających przy użyciu opcji Dodaj wyróżnione w — metoda](media/security-info/securityinfo-myprofile-addmethod.png)

3. Na **Dodaj metodę** wybierz opcję **aplikacji Authenticator** z listy rozwijanej, a następnie wybierz **Dodaj**.

    ![Dodaj pole Metoda, za pomocą aplikacji uwierzytelniania wybrane](media/security-info/securityinfo-myprofile-addauthapp.png)

4. Na **od zebrania aplikacji** wybierz opcję **Pobierz** można pobrać i zainstalować aplikację Microsoft Authenticator na swoim urządzeniu przenośnym, a następnie wybierz **dalej**.

    Aby uzyskać więcej informacji o tym, jak pobrać i zainstalować aplikację, zobacz [Pobierz i zainstaluj aplikację Microsoft Authenticator](user-help-auth-app-download-install.md).

    ![Rozpocznij od pobierania strony aplikacji](media/security-info/securityinfo-myprofile-getauthapp.png)

    >[!Note]
    >Jeśli chcesz użyć aplikacji authenticator innej niż aplikacja Microsoft Authenticator, wybierz opcję **chcę użyć innej aplikacji uwierzytelniającej** łącza.
    
    >Jeśli Twoja organizacja pozwala wybrać inną metodę oprócz aplikacji wystawcy uwierzytelnienia, możesz wybrać **chcę, aby skonfigurować łącze inną metodę**.

5. Pozostają na **skonfigurować konto** strony podczas konfigurowania aplikacji Microsoft Authenticator na swoim urządzeniu przenośnym.

    ![Konfigurowanie strony aplikacji authenticator](media/security-info/securityinfo-myprofile-setupauthapp.png)

6. Otwórz aplikację Microsoft Authenticator, wybierz, aby zezwolić na powiadomienia (Jeśli pojawi się monit), wybierz **Dodaj konto** z **dostosowywanie i kontroli** ikonę w prawym górnym rogu, a następnie wybierz pozycję **pracy lub konta służbowego**.

7. Wróć do **skonfigurować konto** stronie usługi, a następnie wybierz pozycję **dalej**.

    **Zeskanuj kod QR** zostanie wyświetlona strona.

    ![Zeskanuj kod QR przy użyciu aplikacji uwierzytelniającej](media/security-info/securityinfo-myprofile-qrcodeauthapp.png)

6. Zeskanuj kod podany przy użyciu czytnika kodu QR aplikacji Microsoft Authenticator, która znajdowała się na swoim urządzeniu przenośnym, po utworzeniu swojego konta firmowego lub szkolnego w kroku 6.

    Aplikacji authenticator należy pomyślnie dodać pracy konta służbowego bez żadnych dodatkowych informacji od użytkownika. Jednakże, jeśli czytnik kodów QR nie może odczytać kodu, możesz wybrać **nie możesz zeskanować link do kodu QR** i ręcznie wprowadzić kod i adres URL do aplikacji Microsoft Authenticator. Aby uzyskać więcej informacji na temat ręcznego dodawania kodu, zobacz [ręcznie dodać konto do aplikacji](user-help-auth-app-add-account-manual.md).

7. Wybierz **dalej** na **Zeskanuj kod QR** stronie użytkownika.

    Powiadomienie jest wysyłane do aplikacji Microsoft Authenticator na swoim urządzeniu przenośnym, aby przetestować swoje konto.

    ![Testowanie Twoje konto za pomocą aplikacji authenticator](media/security-info/securityinfo-myprofile-tryitauthapp.png)

8. Zatwierdź powiadomienie w aplikacji Microsoft Authenticator, a następnie wybierz **dalej**.

    ![Powiadomienie o powodzeniu, łączenia aplikacji i konta](media/security-info/securityinfo-myprofile-successauthapp.png)

    Twoje informacje zabezpieczające jest aktualizowany do korzystania z aplikacji Microsoft Authenticator domyślnie, aby zweryfikować swoją tożsamość w przypadku korzystania z resetowania dwuetapowej weryfikacji lub hasło.

## <a name="delete-your-authenticator-app-from-your-security-info-methods"></a>Usuwanie aplikacji Authenticator z metody informacje zabezpieczeń
Jeśli nie chcesz już użyć aplikacji Authenticator jako metoda informacje zabezpieczeń, można usunąć go z **zabezpieczające** strony. Działa to w przypadku wszystkich aplikacji wystawcy uwierzytelnienia, a nie tylko aplikacji Microsoft Authenticator. Po usunięciu aplikacji, musisz przejść do aplikacji authenticator na swoim urządzeniu przenośnym, a następnie usunąć konta.

>[!Important]
>Jeśli usuniesz aplikację wystawcy uwierzytelnienia przez pomyłkę, nie ma możliwości jej cofnąć. Musisz ponownie dodać aplikację authenticator czynności opisane w [Konfigurowanie aplikacji uwierzytelniającej](#set-up-the-microsoft-authenticator-app-from-the-security-info-page) dalszej części tego artykułu.

### <a name="to-delete-the-authenticator-app"></a>Aby usunąć aplikację authenticator

1. Na **zabezpieczające** wybierz opcję **Usuń** łącze obok aplikacji wystawcy uwierzytelnienia.

    ![Link, aby usunąć aplikację authenticator z informacje zabezpieczające](media/security-info/securityinfo-myprofile-deleteauthapp.png)

2. Wybierz **tak** z okno dialogowe potwierdzenia, aby usunąć aplikację wystawcy uwierzytelnienia. Po usunięciu aplikacji wystawcy uwierzytelnienia, jest usuwany ze swoich informacji zabezpieczających i znika ono z **zabezpieczające** strony. Jeśli aplikacja authenticator jest metodą domyślną, wartość domyślna zmieni się na innej metody dostępne.

3. Otwórz aplikację Authenticator na swoim urządzeniu przenośnym, wybierz opcję **Edycja kont**, a następnie usuń swojego konta firmowego lub szkolnego z aplikacji wystawcy uwierzytelnienia.

    Twoje konto jest całkowicie usunięte z aplikacji uwierzytelniania dwuskładnikowego weryfikacji i żądania resetowania hasła.

## <a name="change-your-default-security-info-method"></a>Domyślną metodę informacje zabezpieczeń
Chcąc aplikacji authenticator jako domyślną metodę używaną podczas logowania do konta firmowego lub szkolnego, przy użyciu weryfikacji two-Factor Authentication, lub dla żądań resetowania hasła, jest on ustawiany z zabezpieczenia **informacje** strony.

### <a name="to-change-your-default-security-info-method"></a>Aby zmienić metodę informacje zabezpieczeń domyślne

1. Na **zabezpieczające** wybierz opcję **zmiany** łącze obok **domyślna metoda logowania** informacji.

    ![Łącze Zmień domyślną metodę logowania](media/security-info/securityinfo-myprofile-changedefaultauthapp.png)

2. Wybierz **Microsoft Authenticator — powiadomienie** z listy rozwijanej dostępnych metod. Jeśli nie używasz aplikacji Microsoft Authenticator, wybierz opcję **aplikacji Authenticator lub sprzęt tokenu** opcji.

    ![Wybierz metodę logowania domyślne](media/security-info/securityinfo-myprofile-defaultauthapp.png)

3. Wybierz **potwierdzić**.

    Domyślną metodą używane przy zmianach logowania do aplikacji Microsoft Authenticator.

## <a name="additional-security-info-methods"></a>Dodatkowe informacje o metody zabezpieczeń
Masz dodatkowe opcje jak kontakty organizacji możesz zweryfikować swoją tożsamość, oparte na tym, co możesz próby wykonania. Dostępne są następujące opcje:

- **Tekst urządzenia przenośnego.** Wprowadź numer urządzenia przenośnego, a następnie otrzymaj wiadomość SMS, kod służący do celów weryfikacji dwuetapowej lub hasło resetowania. Aby uzyskać szczegółowe instrukcje na temat zweryfikować swoją tożsamość za pomocą wiadomości SMS (SMS), zobacz [ustawiane informacje zabezpieczające, aby używać wiadomości tekstowych (SMS)](security-info-setup-text-msg.md).

- **Rozmowy telefoniczne pracy lub urządzenia przenośnego.** Wprowadź numer urządzenia przenośnego, a następnie uzyskać połączenie telefoniczne resetowania dwuetapowej weryfikacji lub hasło. Aby uzyskać szczegółowe instrukcje na temat zweryfikować swoją tożsamość za pomocą numeru telefonu, zobacz [ustawiane informacje zabezpieczające, aby używać połączeń telefonicznych](security-info-setup-phone-number.md).

- **Adres e-mail.** Wprowadź swój firmowy lub szkolny adres e-mail, aby otrzymywać wiadomość e-mail do resetowania hasła. Ta opcja jest dostępna na potrzeby weryfikacji dwuetapowej. Aby uzyskać szczegółowe instrukcje dotyczące sposobu konfigurowania swój adres e-mail, zobacz [ustawić informacje zabezpieczające, aby użyć adresu e-mail](security-info-setup-email.md).

- **Pytania zabezpieczające.** Odpowiedz na pytania zabezpieczeń, niektóre utworzone przez administratora w Twojej organizacji. Ta opcja jest dostępna tylko do resetowania hasła, a nie na potrzeby weryfikacji dwuetapowej. Aby uzyskać szczegółowe instrukcje dotyczące sposobu konfigurowania pytań zabezpieczających, zobacz [ustawić informacje zabezpieczające, aby użycie tych pytań zabezpieczających](security-info-setup-questions.md) artykułu.
    
    >[!Note]
    >Jeśli brakuje niektórych z tych opcji, prawdopodobnie najbardziej Twoja organizacja nie zezwala na jednej z tych metod. Jeśli jest to możliwe, należy wybrać metodę dostępne lub skontaktuj się z administratorem, aby uzyskać dalszą pomoc.

## <a name="next-steps"></a>Kolejne kroki

- Resetowanie hasła, jeśli został zgubienia lub zapomnienia, z [portal resetowania haseł](https://passwordreset.microsoftonline.com/) lub postępuj zgodnie z instrukcjami w [Resetowanie hasła służbowego lub szkolnego](user-help-reset-password.md) artykułu.

- Pobierz rozwiązania problemu, wskazówki i pomoc dotyczącą problemów z logowania w [nie zalogować się do swojego konta Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant) artykułu.