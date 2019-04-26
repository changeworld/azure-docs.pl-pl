---
title: Zarejestruj dla samoobsługowego resetowania haseł — Azure Active Directory | Dokumentacja firmy Microsoft
description: Zarejestruj dane uwierzytelniania dla hasła usługi Azure AD z samoobsługowego resetowania
services: active-directory
author: eross-msft
manager: daveba
ms.reviewer: sahenry
ms.assetid: ''
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 01/11/2018
ms.author: lizross
ms.collection: M365-identity-device-management
ms.openlocfilehash: f113c42ff45811f31eb77a92873fb187dd51ea6b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60530897"
---
# <a name="register-for-self-service-password-reset"></a>Rejestrowanie na potrzeby samoobsługowego resetowania hasła

> [!IMPORTANT]
> Jesteś tutaj, ponieważ nie możesz się zarejestrować? Jeśli tak, zobacz [Resetowanie hasła służbowego](active-directory-passwords-update-your-own-password.md).

Jako użytkownik końcowy może zresetować hasło lub odblokować konto samodzielnie, korzystając z usługi Azure Active Directory (Azure AD) samoobsługowego resetowania haseł (SSPR). Przed użyciem tej funkcji, musisz Zarejestruj swoje metody uwierzytelniania lub potwierdzić wstępnie zdefiniowane metody uwierzytelniania, które ma być wypełniane administratora.

## <a name="register-or-confirm-authentication-data-with-sspr"></a>Rejestrowanie lub potwierdzanie danych uwierzytelniania na potrzeby samoobsługowego resetowania hasła

1. Otwórz przeglądarkę sieci web na urządzeniu i przejdź do [strony rejestracji resetowania hasła](https://aka.ms/ssprsetup).
2. Wprowadź swoją nazwę użytkownika i hasło podane przez administratora.
3. W zależności od tego, jak dział informatyczny skonfigurował rzeczy co najmniej jeden z następujących opcji są dostępne do konfigurowania i weryfikowania. Jeśli administrator ma Twojej zgody, aby używać Twoich informacji, można wypełnić niektóre informacje dla Ciebie.
    * **Telefon biurowy**: Tylko administrator może ustawić tę opcję.
    * **Numer telefonu uwierzytelniania**: Ustaw tę opcję na numer innego telefonu, którego można uzyskać dostęp do. Przykładem jest telefonu komórkowego, który umożliwia odbieranie wiadomości SMS lub połączenie.
    * **Adres E-mail uwierzytelniania**: Ustaw tę opcję na alternatywny adres e-mail, możesz uzyskać dostęp bez użycia hasła, które chcesz zresetować.
    * **Pytania zabezpieczające**: Administrator zatwierdził tej listy pytań dotyczących musisz odpowiedzieć. Nie można użyć tego samego zapytania lub odpowiedzi na więcej niż jeden raz.
4. Podaj i sprawdź informacje, których wymaga administratora. Jeśli więcej niż jedną z opcji jest dostępna, zalecamy zarejestrowanie wielu metod. Zapewnia to elastyczność przy jednej z metod jest niedostępna. Na przykład sytuacja podczas podróży i nie możesz się dostęp do telefonu biurowego.

    ![Rejestrowanie metod uwierzytelniania i wybierz przycisk Zakończ][Register]

5. Wybierz **Zakończ**. Teraz można użyć funkcji samoobsługowego resetowania HASEŁ, gdy trzeba w przyszłości.

Jeśli wprowadzasz dane **numer telefonu uwierzytelniania** lub **adres E-mail uwierzytelniania**, nie jest widoczne w katalogu globalnym. Jedynie Ty i Twoi administratorzy mogą wyświetlać te dane. Tylko Ty możesz zobaczyć odpowiedzi na pytania zabezpieczające.

Administratorzy powinni może wymagać potwierdzenia metod uwierzytelniania po upływie określonego czasu, aby upewnić się, że nadal masz zarejestrowane właściwe metody.

## <a name="common-problems-and-their-solutions"></a>Typowe problemy i ich rozwiązania

 Poniżej przedstawiono niektóre typowe przypadki błędów i sposoby ich rozwiązywania:

| Przypadki błędów| Jaki dokładnie błąd widzisz?| Rozwiązanie |
| --- | --- | --- |
| Pojawia się Strona "Skontaktuj się z administratorem" po wprowadzeniu mój identyfikator użytkownika | Skontaktuj się z administratorem. <br> <br> Wykryliśmy, że hasło konta użytkownika nie jest zarządzana przez firmę Microsoft. W rezultacie nie możemy automatycznie resetowania hasła. <br> <br> Aby uzyskać dalszą pomoc, skontaktuj się z pomocą działu IT. | Ten komunikat jest wyświetlany, ponieważ działu IT zarządza hasło w środowiska lokalne i nie pozwala na zresetowanie hasła z **nie może uzyskać dostępu do konta** łącza. <br> <br> Aby zresetować hasło, skontaktuj się z pomocą działu IT bezpośrednio w celu uzyskania pomocy. Poinformuj ich o tym, że chcesz zresetować hasło, dzięki czemu mogą włączyć tę funkcję dla Ciebie.|
| Otrzymuję błąd "Twoje konto nie jest włączone do resetowania hasła", który po wprowadzeniu mój identyfikator użytkownika | Twoje konto nie jest włączone do resetowania hasła. <br> <br> Przepraszamy, ale działu IT nie skonfigurował Twoje konto do użycia z tą usługą. <br> <br> Jeśli chcesz, możemy skontaktować się z administratorem w Twojej organizacji, aby zresetował Twoje hasło. | Ten komunikat jest wyświetlany, ponieważ dział IT nie ma włączone resetowanie haseł dla organizacji z **nie może uzyskać dostępu do konta** połączenia lub nie można używać funkcji licencjonowane. <br> <br> Aby zresetować hasło, wybierz pozycję **skontaktuj się z administratorem** łącza. Wiadomości e-mail będą wysyłane do firmy personel IT. Wiadomości e-mail umożliwia ich o tym, że chcesz zresetować hasło, dzięki czemu mogą włączyć tę funkcję dla Ciebie. |
| Otrzymuję błąd "nie można zweryfikować Twoje konto", który po wprowadzeniu mój identyfikator użytkownika | Nie można zweryfikować Twoje konto. <br> <br> Jeśli chcesz, możemy skontaktować się z administratorem w Twojej organizacji, aby zresetował Twoje hasło. | Widzisz ten komunikat, ponieważ masz uprawnień do resetowania hasła, ale nie została zarejestrowana do korzystania z usługi. Aby zarejestrować się do resetowania hasła, przejdź do [strony rejestracji resetowania hasła](https://aka.ms/ssprsetup) po ma się odzyskać dostęp do Twojego konta. <br> <br> Aby zresetować hasło, wybierz pozycję **skontaktuj się z administratorem** łącze, aby wysłać wiadomość e-mail do Twojej firmy personel IT. |

## <a name="next-steps"></a>Kolejne kroki

* [Zmienić hasło przy użyciu samoobsługowego resetowania haseł](active-directory-passwords-update-your-own-password.md)
* [Strona rejestracji w celu resetowania hasła](https://aka.ms/ssprsetup)
* [Portal resetowania hasła](https://passwordreset.microsoftonline.com/)
* [Jeśli nie możesz zalogować się na koncie Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant)

[Register]: ./media/active-directory-passwords-reset-register/register-2-methods.png "Strona rejestracji resetowania hasła, zarejestrowane metody i przycisk Zakończ"

