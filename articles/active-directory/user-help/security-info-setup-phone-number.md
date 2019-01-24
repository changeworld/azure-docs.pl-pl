---
title: Ustawiane informacje zabezpieczające, aby używać połączeń telefonicznych — usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Skonfiguruj informacjom zabezpieczającym, aby zweryfikować swoją tożsamość, używając urządzenia przenośnego lub służbowy numer telefonu.
services: active-directory
author: eross-msft
manager: daveba
ms.reviewer: sahenry
ms.service: active-directory
ms.workload: identity
ms.component: user-help
ms.topic: conceptual
ms.date: 07/30/2018
ms.author: lizross
ms.openlocfilehash: f33982a584f831fbab190d94ce89a8d7c93d7913
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2019
ms.locfileid: "54825225"
---
# <a name="set-up-security-info-to-use-phone-calls-preview"></a>Ustawiane informacje zabezpieczające, aby używać połączeń telefonicznych (wersja zapoznawcza)

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

Definiowanie swoich informacji zabezpieczających wymaga Zaloguj się do swojego konta firmowego lub szkolnego, a następnie ukończ proces rejestracji. Jeśli użytkownik nigdy nie skonfigurowano swoje informacje zabezpieczające, użytkownik zostanie zapytany to zrobić teraz.

## <a name="set-up-phone-calls"></a>Konfigurowanie połączeń telefonicznych

W zależności od ustawień organizacji może być prośba o Dodaj numer telefonu do informacji zabezpieczających, po zalogowaniu. W przeciwnym razie, aby rozpocząć konfigurowanie połączeń telefonicznych zabezpieczających, postępuj zgodnie z instrukcjami w [Zarządzanie informacjom zabezpieczającym](security-info-manage-settings.md).

>[!Note]
>Informacje o zabezpieczeniach nie obsługuje wewnętrzne numery telefonów. Nawet jeśli dodasz prawidłowego formatu, 4255551234 + 1 X 12345, rozszerzenia zostaną usunięte przed wykonaniem wywołania.<br>Jeśli nie widzisz opcji dla telefonu, istnieje możliwość, że Twoja organizacja nie zezwala na użyć połączeń telefonicznych na potrzeby weryfikacji. Jeśli jest to możliwe, należy wybrać inną metodę, lub skontaktuj się z administratorem, aby uzyskać dalszą pomoc.

### <a name="to-use-your-phone-number"></a>Aby użyć numeru telefonu

1. Wybierz **Phone** opcji.

    **Skonfiguruj telefon** pojawi się Kreator.

    ![Konfigurowanie usługi kraj lub region, kod i numer telefonu](media/security-info/security-info-keep-secure-setup-phone.png)

2. Wybierz swoje **kraj lub Region** w polu listy rozwijanej, wpisz numer telefonu (w tym numer kierunkowy, jeśli ma to zastosowanie) w **numer telefonu** wybierz opcję **Zadzwoń do mnie** opcji a następnie wybierz **dalej**.

    Otrzymasz połączenia telefonicznego, aby upewnić się, że wpisany numer telefonu w prawo. W tym momencie użytkownik zostanie zapytany wypychania klucz krzyżyk (#), aby potwierdzić i zakończyć konfigurację.

    ![Sprawdź ekranu telefonu, pokazujący, że połączenie zostało pomyślnie odebrane](media/security-info/security-info-keep-secure-verify-phone-call.png)

    Twoje informacje zabezpieczające jest aktualizowany do użycia numer telefonu, aby zweryfikować swoją tożsamość, używając dwuetapowej weryfikacji lub samoobsługowego resetowania haseł.

    >[!Note]
    >Jeśli chcesz otrzymywać wiadomość SMS, a nie połączenie telefoniczne do Twojego urządzenia przenośnego, wykonaj kroki opisane w [ustawiane informacje zabezpieczające, aby używać wiadomości tekstowych (SMS)](security-info-setup-text-msg.md) artykułu.

## <a name="additional-security-info-options"></a>Dodatkowe informacje o opcje zabezpieczeń

Masz dodatkowe opcje jak kontakty organizacji możesz zweryfikować swoją tożsamość, oparte na tym, co możesz próby wykonania. Dostępne są następujące opcje:

- **Aplikacja Authenticator.** Pobierz i użyj aplikacji authenticator, aby uzyskać powiadomienie o zatwierdzeniu lub kod generowany losowo zatwierdzenia do resetowania dwuetapowej weryfikacji lub hasło. Aby uzyskać szczegółowe instrukcje dotyczące sposobu konfigurowania i używania aplikacji Microsoft Authenticator, zobacz [ustawić informacje zabezpieczające, aby użyć aplikacji authenticator](security-info-setup-auth-app.md).

- **Tekst urządzenia przenośnego.** Wprowadź numer urządzenia przenośnego, a następnie otrzymaj wiadomość SMS, kod służący do celów weryfikacji dwuetapowej lub hasło resetowania. Aby uzyskać szczegółowe instrukcje na temat zweryfikować swoją tożsamość za pomocą wiadomości SMS (SMS), zobacz [ustawiane informacje zabezpieczające, aby używać wiadomości tekstowych (SMS)](security-info-setup-text-msg.md).

- **Adres e-mail.** Wprowadź swój firmowy lub szkolny adres e-mail, aby otrzymywać wiadomość e-mail do resetowania hasła. Ta opcja jest dostępna na potrzeby weryfikacji dwuetapowej. Aby uzyskać szczegółowe instrukcje dotyczące sposobu konfigurowania swój adres e-mail, zobacz [ustawić informacje zabezpieczające, aby użyć adresu e-mail](security-info-setup-email.md).

- **Pytania zabezpieczające.** Odpowiedz na pytania zabezpieczeń, niektóre utworzone przez administratora w Twojej organizacji. Ta opcja jest dostępna tylko do resetowania hasła, a nie na potrzeby weryfikacji dwuetapowej. Aby uzyskać szczegółowe instrukcje dotyczące sposobu konfigurowania pytań zabezpieczających, zobacz [ustawić informacje zabezpieczające, aby użycie tych pytań zabezpieczających](security-info-setup-questions.md) artykułu.
    
    >[!Note]
    >Jeśli brakuje niektórych z tych opcji, prawdopodobnie najbardziej Twoja organizacja nie zezwala na jednej z tych metod. Jeśli jest to możliwe, należy wybrać metodę dostępne lub skontaktuj się z administratorem, aby uzyskać dalszą pomoc.

## <a name="next-steps"></a>Kolejne kroki

- Jeśli musisz zaktualizować swoje informacje zabezpieczające, postępuj zgodnie z instrukcjami [Zarządzanie informacjom zabezpieczającym](security-info-manage-settings.md) artykułu.

- Resetowanie hasła, jeśli został zgubienia lub zapomnienia, z [portal resetowania haseł](https://passwordreset.microsoftonline.com/) lub postępuj zgodnie z instrukcjami w [Resetowanie hasła służbowego lub szkolnego](user-help-reset-password.md) artykułu.

- Pobierz rozwiązania problemu, wskazówki i pomoc dotyczącą problemów z logowania w [nie zalogować się do swojego konta Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant) artykułu.