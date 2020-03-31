---
title: Rejestrowanie informacji uwierzytelniających w celu zresetowania własnego hasła — Usługa Azure AD
description: Zarejestruj informacje o metodzie weryfikacji do samodzielnego resetowania hasła usługi Azure AD, aby można było zresetować własne hasło bez pomocy administratora.
services: active-directory
author: curtand
manager: daveba
ms.reviewer: sahenry
ms.assetid: ''
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 01/15/2020
ms.author: curtand
ms.openlocfilehash: 397e1fd7695fd7e74e1f22959d0f9f24af7d1ea3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77062646"
---
# <a name="register-your-verification-method-info-to-reset-your-own-password"></a>Zarejestruj informacje o metodzie weryfikacji, aby zresetować własne hasło

Jeśli nie pamiętasz hasła służbowego lub szkolnego, nigdy nie otrzymałeś hasła od organizacji lub nie masz konta, możesz użyć informacji zabezpieczających i urządzenia mobilnego, aby zresetować hasło służbowe lub szkolne.

Administrator musi włączyć tę funkcję, aby móc zarejestrować swoje informacje i zresetować własne hasło. Jeśli nie widzisz opcji **Nie pamiętasz hasła,** oznacza to, że administrator nie włączył tej funkcji w organizacji. Jeśli uważasz, że jest to nieprawidłowe, skontaktuj się z działem pomocy technicznej, aby uzyskać pomoc.

>[!Important]
>Ten artykuł jest przeznaczony dla użytkowników próbujących użyć rejestracji do samodzielnego resetowania hasła. Oznacza to, że będziesz mógł zresetować własne hasło służbowe alain@contoso.comlub szkolne (np. ), bez konieczności konieczności pomocy administratora. Jeśli jesteś administratorem szukającym informacji o tym, jak włączyć samoobsługowe resetowanie hasła dla pracowników lub innych użytkowników, zobacz [resetowanie hasła samoobsługowego usługi Azure AD i inne artykuły](https://docs.microsoft.com/azure/active-directory/authentication/howto-sspr-deployment).

## <a name="set-up-your-password-reset-verification-method"></a>Konfigurowanie metody weryfikacji resetowania hasła

1. Otwórz przeglądarkę internetową na urządzeniu i przejdź do [strony z informacjami zabezpieczającymi](https://account.activedirectory.windowsazure.com/PasswordReset/Register.aspx?regref=ssprsetup).

2. W zależności od tego, jak administrator skonfigurował organizację, co najmniej jedna z następujących opcji będzie dostępna do skonfigurowania jako metody weryfikacji zabezpieczeń. Jeśli dostępnych jest wiele opcji, zdecydowanie zaleca się użycie więcej niż jednej metody weryfikacji zabezpieczeń w przypadku, gdy jedna z metod stanie się niedostępna.

    - **Uwierzytelnianie aplikacji.** Wybierz opcję używania aplikacji Microsoft Authenticator lub innej aplikacji uwierzytelniającego jako metody weryfikacji zabezpieczeń. Aby uzyskać więcej informacji na temat konfigurowania aplikacji, zobacz [Konfigurowanie aplikacji Microsoft Authenticator jako metody weryfikacji](security-info-setup-auth-app.md).

    - **Wiadomości tekstowe.** Wybierz opcję wysyłania wiadomości tekstowych na urządzenie mobilne. Aby uzyskać więcej informacji na temat konfigurowania wiadomości tekstowych, zobacz [Konfigurowanie wiadomości tekstowych jako metody weryfikacji](security-info-setup-text-msg.md).

    - **Telefoniczne.** Wybierz opcję połączenia telefonicznego na zarejestrowany numer telefonu. Aby uzyskać więcej informacji na temat konfigurowania połączeń telefonicznych, zobacz [Konfigurowanie numeru telefonu jako metody weryfikacji](security-info-setup-phone-number.md).

    - **Klucz zabezpieczeń.** Wybierz opcję używania klucza zabezpieczeń zgodnego z firmą Microsoft. Aby uzyskać więcej informacji, zobacz [Konfigurowanie klucza zabezpieczeń jako metody weryfikacji](security-info-setup-security-key.md).

    - **Adres e-mail.** Wybierz alternatywny adres e-mail, który może być używany bez konieczności używania zapomnianego lub brakującego hasła. Działa to tylko w przypadku resetowania hasła, a nie jako metoda weryfikacji zabezpieczeń. Aby uzyskać więcej informacji na temat konfigurowania adresu e-mail, zobacz [Konfigurowanie adresu e-mail jako metody weryfikacji](security-info-setup-email.md).

    - **Pytania zabezpieczające.** Wybierz opcję konfigurowania i odpowiadania na wstępnie zdefiniowane pytania zabezpieczające skonfigurowane przez administratora. Działa to tylko w przypadku resetowania hasła, a nie jako metoda weryfikacji zabezpieczeń. Aby uzyskać więcej informacji na temat pytań zabezpieczających, zobacz [Konfigurowanie pytań zabezpieczających jako metody weryfikacji](security-info-setup-questions.md).

3. Po wybraniu i skonfigurowaniu metod wybierz pozycję **Zakończ,** aby zakończyć proces.

    > [!Note]
    > Informacje dodane dla numeru telefonu lub adresu e-mail nie są udostępniane katalogowi globalnemu organizacji. Jedynymi osobami, które mogą zobaczyć te informacje, są Ty i Twój administrator. Tylko Ty możesz zobaczyć odpowiedzi na swoje pytania zabezpieczające.

## <a name="common-problems-and-their-solutions"></a>Typowe problemy i ich rozwiązania

 Oto kilka typowych przypadków błędów i ich rozwiązań:

| Komunikat o błędzie |  Możliwe rozwiązanie |
| --- | --- | --- |
| Skontaktuj się z administratorem.<br>Wykryliśmy, że hasło do konta użytkownika nie jest zarządzane przez firmę Microsoft. W rezultacie nie możemy automatycznie zresetować hasła.<br>Aby uzyskać dalszą pomoc, skontaktuj się z personelem IT.| Jeśli po wpisaniu identyfikatora użytkownika zostanie wyświetlony ten komunikat o błędzie, oznacza to, że organizacja wewnętrznie zarządza twoim hasłem i nie chce, aby resetować hasło z łącza **Nie można uzyskać dostępu do konta.** Aby zresetować hasło w tej sytuacji, należy skontaktować się z działem pomocy technicznej organizacji lub administratorem, aby uzyskać pomoc. |
| Twoje konto nie jest włączone do resetowania hasła.<br>Przykro nam, ale pracownicy IT nie skonfigurowali Twojego konta do korzystania z tej usługi.<br>Jeśli chcesz, możemy skontaktować się z administratorem w Twojej organizacji, aby zresetować hasło dla Ciebie. | Jeśli po wpisaniu nazwy użytkownika zostanie wyświetlony ten komunikat o błędzie, oznacza to, że organizacja nie włączyła funkcji resetowania hasła lub nie możesz jej używać. Aby zresetować hasło w tej sytuacji, należy wybrać **łącze Kontakt z administratorem.** Po kliknięciu łącza wiadomość e-mail jest wysyłana do działu pomocy technicznej lub administratora organizacji, informując ich, że chcesz zresetować hasło. |
| Nie możemy zweryfikować Twojego konta.<br>Jeśli chcesz, możemy skontaktować się z administratorem w Twojej organizacji, aby zresetować hasło dla Ciebie. | Jeśli po wpisaniu identyfikatora użytkownika zostanie wyświetlony ten komunikat o błędzie, oznacza to, że organizacja włączyła resetowanie hasła i można jej używać, ale nie została zarejestrowana w usłudze. W takiej sytuacji należy skontaktować się z działem pomocy technicznej lub administratorem organizacji, aby zresetować hasło. Aby uzyskać informacje na temat rejestracji do resetowania hasła po powrocie na urządzenie, zobacz proces powyżej w tym artykule. |

## <a name="next-steps"></a>Następne kroki

- [Zmienianie hasła przy użyciu samoobsługowego resetowania hasła](active-directory-passwords-update-your-own-password.md)

- [Strona informacji o zabezpieczeniach](https://mysignins.microsoft.com/security-info)

- [Portal resetowania hasła](https://passwordreset.microsoftonline.com/)

- [Kiedy nie możesz zalogować się na swoje konto Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant)
