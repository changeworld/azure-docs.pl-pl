---
title: Konfigurowanie informacji zabezpieczających, aby użyć aplikacji authenticator — usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Skonfiguruj swoje informacje zabezpieczające, aby zweryfikować swoją tożsamość za pomocą aplikacji Microsoft Authenticator.
services: active-directory
author: eross-msft
manager: mtillman
ms.reviewer: sahenry
ms.service: active-directory
ms.workload: identity
ms.component: user-help
ms.topic: conceptual
ms.date: 07/30/2018
ms.author: lizross
ms.openlocfilehash: 776f98269e3e0c31766246ca96636528f60c3e09
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/07/2018
ms.locfileid: "44160630"
---
# <a name="set-up-security-info-to-use-an-authenticator-app-preview"></a>Ustawić informacje zabezpieczające, aby użyć aplikacji authenticator (wersja zapoznawcza)

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

Definiowanie swoich informacji zabezpieczających wymaga Zaloguj się do swojego konta firmowego lub szkolnego, a następnie ukończ proces rejestracji. Jeśli użytkownik nigdy nie skonfigurowano swoje informacje zabezpieczające, użytkownik zostanie zapytany to zrobić teraz.

## <a name="set-up-the-microsoft-authenticator-app"></a>Konfigurowanie aplikacji Microsoft Authenticator

W zależności od ustawień organizacji może monit do skonfigurowania aplikacji Microsoft Authenticator, po zalogowaniu. W przeciwnym razie, aby rozpocząć konfigurowanie aplikacji Microsoft Authenticator zabezpieczających, postępuj zgodnie z instrukcjami w [Zarządzanie informacjom zabezpieczającym](security-info-manage-settings.md).

Aby pobrać i Dowiedz się więcej na temat aplikacji Microsoft Authenticator, zobacz [Rozpoczynanie pracy z aplikacją Microsoft Authenticator](microsoft-authenticator-app-how-to.md).

>[!Note]
>Jeśli nie chcesz korzystać z aplikacji Microsoft Authenticator, można wybrać inną aplikację podczas konfigurowania. W tym artykule używa aplikacji Microsoft Authenticator. Jeśli nie widzisz opcji aplikacji wystawcy uwierzytelnienia jest możliwe, że Twoja organizacja nie zezwala na korzystanie z aplikacji uwierzytelniania do weryfikacji. Jeśli jest to możliwe, należy wybrać inną metodę, lub skontaktuj się z administratorem, aby uzyskać dalszą pomoc.

### <a name="to-use-the-microsoft-authenticator-app"></a>Aby korzystać z aplikacji Microsoft Authenticator

1. Wybierz **aplikacji Authenticator** opcji.

    **Pobierz aplikację** pojawi się Kreator.

    ![Skorzystaj z Kreatora aplikacji, ekran początkowy](media/security-info/security-info-auth-app-wizard.png)

    Jeśli nie chcesz korzystać z aplikacji Microsoft Authenticator, możesz kliknąć **chcę użyć innej aplikacji uwierzytelniającej** link z **Pobierz aplikację** ekranu.

2. Po zainstalowaniu aplikacji Microsoft Authenticator, wybierz **dalej**.

    Jeśli zostanie wyświetlony monit, Zezwalaj na powiadomienia, dodać nowe konto, a następnie wybierz **pracy konta służbowego**.

3. Wybierz opcję **Dalej**.

    **Zeskanuj kod QR** pojawi się ekran.

    ![Zeskanuj kod QR przy użyciu aplikacji uwierzytelniającej](media/security-info/security-info-scan-qr.png)

4. Otwórz aplikację Microsoft Authenticator, wybierz opcję **Dodaj konto** z **dostosowywanie i sterowania** ikonę w prawym górnym rogu, a następnie wybierz **pracy konta służbowego**. 

5. Jeśli masz aplikację czytnika kodu QR, zeskanuj kod podany. Jeśli nie masz aplikacji czytnika kodu, możesz wybrać **nie możesz zeskanować link do kodu QR** i ręcznie wprowadzić kod i adres URL do aplikacji Microsoft Authenticator.

6. Użyj aplikacji Microsoft Authenticator, aby zatwierdzić powiadomienie, aby aktywować aplikację.

    Twoje informacje zabezpieczające jest aktualizowany na potrzeby aplikacji Microsoft Authenticator, aby zweryfikować swoją tożsamość, używając dwuetapowej weryfikacji lub samoobsługowego resetowania haseł.

    >[!Note]
    >Jeśli Twoja organizacja pozwala na to, również otrzymasz kod weryfikacyjny, wraz z powiadomienie w aplikacji Microsoft Authenticator. Jeśli chcesz wprowadzić kod metodą domyślną, postępuj zgodnie z instrukcjami [Zarządzanie informacjom zabezpieczającym](security-info-setup-auth-app.md).

## <a name="additional-security-info-options"></a>Dodatkowe informacje o opcje zabezpieczeń

Masz dodatkowe opcje jak kontakty organizacji możesz zweryfikować swoją tożsamość, oparte na tym, co możesz próby wykonania. Dostępne są następujące opcje:

- **Tekst urządzenia przenośnego.** Wprowadź numer urządzenia przenośnego, a następnie otrzymaj wiadomość SMS, kod służący do celów weryfikacji dwuetapowej lub hasło resetowania. Aby uzyskać szczegółowe instrukcje na temat zweryfikować swoją tożsamość za pomocą wiadomości SMS (SMS), zobacz [ustawiane informacje zabezpieczające, aby używać wiadomości tekstowych (SMS)](security-info-setup-text-msg.md).

- **Rozmowy telefoniczne pracy lub urządzenia przenośnego.** Wprowadź numer urządzenia przenośnego, a następnie uzyskać połączenie telefoniczne resetowania dwuetapowej weryfikacji lub hasło. Aby uzyskać szczegółowe instrukcje na temat zweryfikować swoją tożsamość za pomocą numeru telefonu, zobacz [ustawiane informacje zabezpieczające, aby używać połączeń telefonicznych](security-info-setup-phone-number.md).

- **Adres e-mail.** Wprowadź swój firmowy lub szkolny adres e-mail, aby otrzymywać wiadomość e-mail do resetowania hasła. Ta opcja jest dostępna na potrzeby weryfikacji dwuetapowej. Aby uzyskać szczegółowe instrukcje dotyczące sposobu konfigurowania swój adres e-mail, zobacz [ustawić informacje zabezpieczające, aby użyć adresu e-mail](security-info-setup-email.md).

- **Pytania zabezpieczające.** Odpowiedz na pytania zabezpieczeń, niektóre utworzone przez administratora w Twojej organizacji. Ta opcja jest dostępna tylko do resetowania hasła, a nie na potrzeby weryfikacji dwuetapowej. Aby uzyskać szczegółowe instrukcje dotyczące sposobu konfigurowania pytań zabezpieczających, zobacz [ustawić informacje zabezpieczające, aby użycie tych pytań zabezpieczających](security-info-setup-questions.md) artykułu.
    
    >[!Note]
    >Jeśli brakuje niektórych z tych opcji, prawdopodobnie najbardziej Twoja organizacja nie zezwala na jednej z tych metod. Jeśli jest to możliwe, należy wybrać metodę dostępne lub skontaktuj się z administratorem, aby uzyskać dalszą pomoc.

## <a name="next-steps"></a>Kolejne kroki

- Jeśli musisz zaktualizować swoje informacje zabezpieczające, postępuj zgodnie z instrukcjami [Zarządzanie informacjom zabezpieczającym](security-info-manage-settings.md) artykułu.

- Resetowanie hasła, jeśli został zgubienia lub zapomnienia, z [portal resetowania haseł](https://passwordreset.microsoftonline.com/) lub postępuj zgodnie z instrukcjami w [Resetowanie hasła służbowego lub szkolnego](user-help-reset-password.md) artykułu.

- Pobierz rozwiązania problemu, wskazówki i pomoc dotyczącą problemów z logowania w [nie zalogować się do swojego konta Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant) artykułu.