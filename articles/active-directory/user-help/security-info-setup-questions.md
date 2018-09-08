---
title: Skonfiguruj informacje zabezpieczające na użycie tych pytań zabezpieczających — usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Skonfiguruj swoje informacje zabezpieczające, aby zweryfikować swoją tożsamość za pomocą pytań zabezpieczających wstępnie zdefiniowane.
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
ms.openlocfilehash: b4913d55ee3d254f197512d9251ae750d8896f53
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/07/2018
ms.locfileid: "44160442"
---
# <a name="set-up-security-info-to-use-pre-defined-security-questions-preview"></a>Skonfiguruj informacje zabezpieczające na użycie tych pytań zabezpieczających wstępnie zdefiniowane (wersja zapoznawcza)

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

Definiowanie swoich informacji zabezpieczających wymaga Zaloguj się do swojego konta firmowego lub szkolnego, a następnie ukończ proces rejestracji. Jeśli użytkownik nigdy nie skonfigurowano swoje informacje zabezpieczające, użytkownik zostanie zapytany to zrobić teraz.

## <a name="set-up-security-questions"></a>Konfigurowanie pytań zabezpieczających

W zależności od ustawień organizacji może monit dodać pytania zabezpieczające do swoich informacji zabezpieczających, po zalogowaniu. W przeciwnym razie, aby rozpocząć konfigurowanie zabezpieczeń pytań zabezpieczających, postępuj zgodnie z instrukcjami w [Zarządzanie informacjom zabezpieczającym](security-info-manage-settings.md).

Jeśli używasz pytań zabezpieczających, firma Microsoft zaleca używanie ich razem z innej metody. Pytania zabezpieczeń może być mniej bezpieczne niż inne metody, ponieważ niektóre osoby mogą znać odpowiedzi na pytania przez inną osobę.

>[!Note]
>Pytania zabezpieczające są przechowywane przez użytkowników i bezpiecznie w obiekcie użytkownika w katalogu, a tylko można uzyskać przez użytkownika podczas rejestracji. Nie ma możliwości dla administratora odczytać lub zmodyfikować swoje pytania lub odpowiedzi.<br>Jeśli nie widzisz opcji pytania zabezpieczeń, jest to możliwe, że Twoja organizacja nie zezwala na użycie tych pytań zabezpieczających podczas weryfikacji. Jeśli jest to możliwe, należy wybrać inną metodę, lub skontaktuj się z administratorem, aby uzyskać dalszą pomoc.

### <a name="to-choose-and-answer-your-security-questions"></a>Aby wybrać i odpowiedzi na pytania zabezpieczające

1. Wybierz **pytania zabezpieczające**, a następnie wybierz pytania zabezpieczające, które chcesz odpowiedzieć. 

    Liczba pytań zabezpieczających, które mają do wyboru są określane przez administratora.

    ![Informacje o zabezpieczeniach strony i pobranie pytań zabezpieczających](media/security-info/security-info-keep-secure-setup-pick-questions.png)

2. Podaj odpowiedzi dla wybranego pytania, a następnie wybierz pozycję **gotowe**.

## <a name="additional-security-info-options"></a>Dodatkowe informacje o opcje zabezpieczeń

Dostępne opcje jak kontakty organizacji możesz zweryfikować swoją tożsamość, oparte na tym, co możesz próby wykonania. Dostępne są następujące opcje:

- **Aplikacja Authenticator.** Pobierz i użyj aplikacji authenticator, aby uzyskać powiadomienie o zatwierdzeniu lub kod generowany losowo zatwierdzenia do resetowania dwuetapowej weryfikacji lub hasło. Aby uzyskać szczegółowe instrukcje dotyczące sposobu konfigurowania i używania aplikacji Microsoft Authenticator, zobacz [ustawić informacje zabezpieczające, aby użyć aplikacji authenticator](security-info-setup-auth-app.md).

- **Tekst urządzenia przenośnego.** Wprowadź numer urządzenia przenośnego, a następnie otrzymaj wiadomość SMS, kod służący do celów weryfikacji dwuetapowej lub hasło resetowania. Aby uzyskać szczegółowe instrukcje na temat zweryfikować swoją tożsamość za pomocą wiadomości SMS (SMS), zobacz [ustawiane informacje zabezpieczające, aby używać wiadomości tekstowych (SMS)](security-info-setup-text-msg.md).

- **Rozmowy telefoniczne pracy lub urządzenia przenośnego.** Wprowadź numer urządzenia przenośnego, a następnie uzyskać połączenie telefoniczne resetowania dwuetapowej weryfikacji lub hasło. Aby uzyskać szczegółowe instrukcje na temat zweryfikować swoją tożsamość za pomocą numeru telefonu, zobacz [ustawiane informacje zabezpieczające, aby używać połączeń telefonicznych](security-info-setup-phone-number.md).

- **Adres e-mail.** Wprowadź swój firmowy lub szkolny adres e-mail, aby otrzymywać wiadomość e-mail do resetowania hasła. Ta opcja jest dostępna na potrzeby weryfikacji dwuetapowej. Aby uzyskać szczegółowe instrukcje dotyczące sposobu konfigurowania swój adres e-mail, zobacz [ustawić informacje zabezpieczające, aby użyć adresu e-mail](security-info-setup-email.md).
   
    >[!Note]
    >Jeśli brakuje niektórych z tych opcji, prawdopodobnie najbardziej Twoja organizacja nie zezwala na jednej z tych metod. Jeśli jest to możliwe, należy wybrać metodę dostępne lub skontaktuj się z administratorem, aby uzyskać dalszą pomoc.

## <a name="next-steps"></a>Kolejne kroki

- Jeśli musisz zaktualizować swoje informacje zabezpieczające, postępuj zgodnie z instrukcjami [Zarządzanie informacjom zabezpieczającym](security-info-manage-settings.md) artykułu.

- Resetowanie hasła, jeśli został zgubienia lub zapomnienia, z [portal resetowania haseł](https://passwordreset.microsoftonline.com/) lub postępuj zgodnie z instrukcjami w [Resetowanie hasła służbowego lub szkolnego](user-help-reset-password.md) artykułu.

- Pobierz rozwiązania problemu, wskazówki i pomoc dotyczącą problemów z logowania w [nie zalogować się do swojego konta Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant) artykułu.