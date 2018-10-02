---
title: Ustawić informacje zabezpieczające, aby użyć adresu e-mail — usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Skonfiguruj swoje informacje zabezpieczające do zweryfikowania Twojej tożsamości przy użyciu służbowego adresu e-mail.
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
ms.openlocfilehash: f6a574f169310f2779fb20353fda748e6893a4bd
ms.sourcegitcommit: 7bc4a872c170e3416052c87287391bc7adbf84ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/02/2018
ms.locfileid: "48016744"
---
# <a name="set-up-security-info-to-use-email-preview"></a>Ustawić informacje zabezpieczające, aby użyć adresu e-mail (wersja zapoznawcza)

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

Definiowanie swoich informacji zabezpieczających wymaga Zaloguj się do swojego konta firmowego lub szkolnego, a następnie ukończ proces rejestracji. Jeśli użytkownik nigdy nie skonfigurowano swoje informacje zabezpieczające, użytkownik zostanie zapytany to zrobić teraz.

## <a name="set-up-email"></a>Konfigurowanie adresu e-mail

W zależności od ustawień organizacji może monit o Dodaj adres e-mail do informacji zabezpieczających, podczas logowania. W przeciwnym razie, aby rozpocząć konfigurowanie poczty e-mail w informacji zabezpieczających, postępuj zgodnie z instrukcjami w [Zarządzanie informacjom zabezpieczającym](security-info-manage-settings.md).

>[!Note]
>Zaleca się przy użyciu konta e-mail, która nie wymaga hasła dostępu do sieci.<br>Jeśli nie widzisz opcji wiadomości e-mail, istnieje możliwość, że Twoja organizacja nie zezwala na użyć wiadomość e-mail na potrzeby weryfikacji. Jeśli jest to możliwe, należy wybrać inną metodę, lub skontaktuj się z administratorem, aby uzyskać dalszą pomoc.

### <a name="to-use-your-email-address"></a>Aby użyć adresu e-mail

1. Wybierz **E-mail** opcji, a następnie wpisz swój adres e-mail w polu. Ten adres e-mail nie może być służbowy adres e-mail.

     ![Strona informacji zabezpieczających, za pomocą poczty e-mail, pole wprowadzania](media/security-info/security-info-keep-secure-setup-email.png)

2. Sprawdź, czy wiadomość e-mail od firmy Microsoft dla organizacji, wpisz kod weryfikacyjny dołączone do **Zweryfikuj adres e-mail** , a następnie wybierz **gotowe**.

     ![Strona informacji zabezpieczających, za pomocą pole wprowadzania kodu weryfikacji wiadomości e-mail](media/security-info/security-info-verify-email.png)

    >[!Note]
    >Jeśli nie widzisz wiadomości e-mail od firmy Microsoft w imieniu swojej organizacji, upewnij się, że Twój adres e-mail został wpisany prawidłowo, a następnie sprawdź foldery wiadomości-śmieci lub spamu.

3. W **zabezpieczyć swoje konto** wybierz opcję **gotowe**.

    Twoje informacje zabezpieczające jest aktualizowany do Użyj swojego adresu e-mail, aby zweryfikować swoją tożsamość w przypadku korzystania z resetowania hasła.

## <a name="additional-security-info-options"></a>Dodatkowe informacje o opcje zabezpieczeń

Dostępne opcje jak kontakty organizacji możesz zweryfikować swoją tożsamość, oparte na tym, co możesz próby wykonania. Dostępne są następujące opcje:

- **Aplikacja Authenticator.** Pobierz i użyj aplikacji authenticator, aby uzyskać powiadomienie o zatwierdzeniu lub kod generowany losowo zatwierdzenia do resetowania dwuetapowej weryfikacji lub hasło. Aby uzyskać szczegółowe instrukcje dotyczące sposobu konfigurowania i używania aplikacji Microsoft Authenticator, zobacz [ustawić informacje zabezpieczające, aby użyć aplikacji authenticator](security-info-setup-auth-app.md).

- **Tekst urządzenia przenośnego.** Wprowadź numer urządzenia przenośnego, a następnie otrzymaj wiadomość SMS, kod służący do celów weryfikacji dwuetapowej lub hasło resetowania. Aby uzyskać szczegółowe instrukcje na temat zweryfikować swoją tożsamość za pomocą wiadomości SMS (SMS), zobacz [ustawiane informacje zabezpieczające, aby używać wiadomości tekstowych (SMS)](security-info-setup-text-msg.md).

- **Rozmowy telefoniczne pracy lub urządzenia przenośnego.** Wprowadź numer urządzenia przenośnego, a następnie uzyskać połączenie telefoniczne resetowania dwuetapowej weryfikacji lub hasło. Aby uzyskać szczegółowe instrukcje na temat zweryfikować swoją tożsamość za pomocą numeru telefonu, zobacz [ustawiane informacje zabezpieczające, aby używać połączeń telefonicznych](security-info-setup-phone-number.md).

- **Pytania zabezpieczające.** Odpowiedz na pytania zabezpieczeń, niektóre utworzone przez administratora w Twojej organizacji. Ta opcja jest dostępna tylko do resetowania hasła, a nie na potrzeby weryfikacji dwuetapowej. Aby uzyskać szczegółowe instrukcje dotyczące sposobu konfigurowania pytań zabezpieczających, zobacz [ustawić informacje zabezpieczające, aby użycie tych pytań zabezpieczających](security-info-setup-questions.md) artykułu.
    
    >[!Note]
    >Jeśli brakuje niektórych z tych opcji, prawdopodobnie najbardziej Twoja organizacja nie zezwala na jednej z tych metod. Jeśli jest to możliwe, należy wybrać metodę dostępne lub skontaktuj się z administratorem, aby uzyskać dalszą pomoc.

## <a name="next-steps"></a>Kolejne kroki

- Jeśli musisz zaktualizować swoje informacje zabezpieczające, postępuj zgodnie z instrukcjami [Zarządzanie informacjom zabezpieczającym](security-info-manage-settings.md) artykułu.

- Resetowanie hasła, jeśli został zgubienia lub zapomnienia, z [portal resetowania haseł](https://passwordreset.microsoftonline.com/) lub postępuj zgodnie z instrukcjami w [Resetowanie hasła służbowego lub szkolnego](user-help-reset-password.md) artykułu.

- Pobierz rozwiązania problemu, wskazówki i pomoc dotyczącą problemów z logowania w [nie zalogować się do swojego konta Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant) artykułu.
