---
title: Skonfiguruj informacje zabezpieczające do użycia w wiadomości SMS - usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Skonfiguruj swoje informacje zabezpieczające, aby zweryfikować swoją tożsamość za pomocą wiadomości tekstowej (SMS).
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
ms.openlocfilehash: f20bcb4245a2870d8eaa442c6f747ee533b362db
ms.sourcegitcommit: 30fd606162804fe8ceaccbca057a6d3f8c4dd56d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/30/2018
ms.locfileid: "39348393"
---
# <a name="set-up-security-info-to-use-text-messaging-preview"></a>Skonfiguruj informacje zabezpieczające do używania tekstu wiadomości (wersja zapoznawcza)

[!INCLUDE[preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

Definiowanie swoich informacji zabezpieczających wymaga Zaloguj się do swojego konta firmowego lub szkolnego, a następnie ukończ proces rejestracji. Jeśli użytkownik nigdy nie skonfigurowano swoje informacje zabezpieczające, użytkownik zostanie zapytany to zrobić teraz.

## <a name="set-up-text-messaging"></a>Skonfiguruj wiadomości tekstowe

W zależności od ustawień organizacji może być monitu o dodanie tekst wiadomości do swoich informacji zabezpieczających, po zalogowaniu. W przeciwnym razie, aby rozpocząć konfigurowanie tekst wiadomości zabezpieczających, postępuj zgodnie z instrukcjami w [Zarządzanie informacjom zabezpieczającym](security-info-manage-settings.md).

Opcji tekst komunikatu jest częścią opcji dla telefonu, więc będzie wszystko, co w taki sam sposób, jak ustawić o Twój numer telefonu, ale zamiast Microsoft Zadzwonimy do Ciebie, będzie zdecydujesz się używać wiadomości SMS. Jeśli nie widzisz opcji dla telefonu, istnieje możliwość, że Twoja organizacja nie zezwala na numer telefonu służy do identyfikacji. Jeśli jest to możliwe, należy wybrać inną metodę, lub skontaktuj się z administratorem, aby uzyskać dalszą pomoc.

### <a name="to-use-a-text-message"></a>Aby używać wiadomości SMS

1. Wybierz **Phone** opcji.

    **Skonfiguruj telefon** pojawi się Kreator.

    ![Konfigurowanie usługi kraj lub region, kod i numer telefonu](media/security-info/security-info-keep-secure-setup-text.png)

2. Wybierz swoje **kraj lub Region** w polu listy rozwijanej, wpisz numer telefonu (w tym numer kierunkowy, jeśli ma to zastosowanie) w **numer telefonu** wybierz opcję **tekstu do mnie kod** opcji, a następnie wybierz **dalej**.

    Otrzymasz wiadomość SMS z kodem, musisz wprowadzić do strony weryfikacji.

    ![Weryfikacja strony, aby wprowadzić wiadomość SMS z kodem](media/security-info/security-info-keep-secure-verify-text-msg.png)

    Twoje informacje zabezpieczające zostaną zaktualizowane na wysyłanie wiadomości SMS, aby zweryfikować swoją tożsamość, używając dwuetapowej weryfikacji lub samoobsługowego resetowania haseł.

    >[!Note]
    >Jeśli chcesz odbierać połączenie telefoniczne zamiast wiadomość SMS, wykonaj kroki opisane w [ustawiane informacje zabezpieczające, aby używać połączeń telefonicznych](security-info-setup-phone-number.md) artykułu.

## <a name="additional-security-info-options"></a>Dodatkowe informacje o opcje zabezpieczeń

Dostępne opcje jak kontakty organizacji możesz zweryfikować swoją tożsamość, oparte na tym, co możesz próby wykonania. Dostępne są następujące opcje:

- **Aplikacja Authenticator.** Pobierz i użyj aplikacji authenticator, aby uzyskać powiadomienie o zatwierdzeniu lub kod generowany losowo zatwierdzenia do resetowania dwuetapowej weryfikacji lub hasło. Aby uzyskać szczegółowe instrukcje dotyczące sposobu konfigurowania i używania aplikacji Microsoft Authenticator, zobacz [ustawić informacje zabezpieczające, aby użyć aplikacji authenticator](security-info-setup-auth-app.md).

- **Rozmowy telefoniczne pracy lub urządzenia przenośnego.** Wprowadź numer urządzenia przenośnego, a następnie uzyskać połączenie telefoniczne resetowania dwuetapowej weryfikacji lub hasło. Aby uzyskać szczegółowe instrukcje na temat zweryfikować swoją tożsamość za pomocą numeru telefonu, zobacz [ustawiane informacje zabezpieczające, aby używać połączeń telefonicznych](security-info-setup-phone-number.md).

- **Adres e-mail.** Wprowadź swój firmowy lub szkolny adres e-mail, aby otrzymywać wiadomość e-mail do resetowania hasła. Ta opcja jest dostępna na potrzeby weryfikacji dwuetapowej. Aby uzyskać szczegółowe instrukcje dotyczące sposobu konfigurowania swój adres e-mail, zobacz [ustawić informacje zabezpieczające, aby użyć adresu e-mail](security-info-setup-email.md).
   
    >[!Note]
    >Jeśli brakuje niektórych z tych opcji, prawdopodobnie najbardziej Twoja organizacja nie zezwala na jednej z tych metod. Jeśli jest to możliwe, należy wybrać metodę dostępne lub skontaktuj się z administratorem, aby uzyskać dalszą pomoc.

- **Pytania zabezpieczające.** Odpowiedz na pytania zabezpieczeń, niektóre utworzone przez administratora w Twojej organizacji. Ta opcja jest dostępna tylko do resetowania hasła, a nie na potrzeby weryfikacji dwuetapowej. Aby uzyskać szczegółowe instrukcje dotyczące sposobu konfigurowania pytań zabezpieczających, zobacz [ustawić informacje zabezpieczające, aby użycie tych pytań zabezpieczających](security-info-setup-questions.md) artykułu.

## <a name="next-steps"></a>Kolejne kroki

- Jeśli musisz zaktualizować swoje informacje zabezpieczające, postępuj zgodnie z instrukcjami [Zarządzanie informacjom zabezpieczającym](security-info-manage-settings.md) artykułu.

- Resetowanie hasła, jeśli został zgubienia lub zapomnienia, z [portal resetowania haseł](https://passwordreset.microsoftonline.com/) lub postępuj zgodnie z instrukcjami w [Resetowanie hasła służbowego lub szkolnego](user-help-reset-password.md) artykułu.

- Pobierz rozwiązania problemu, wskazówki i pomoc dotyczącą problemów z logowania w [nie zalogować się do swojego konta Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant) artykułu.