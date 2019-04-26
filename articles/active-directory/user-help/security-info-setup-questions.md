---
title: Skonfiguruj informacje o zabezpieczeniach (wersja zapoznawcza) na użycie tych pytań zabezpieczających — usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Jak skonfigurować swoje informacje zabezpieczające, aby zweryfikować swoją tożsamość przy użyciu wstępnie zdefiniowane pytania zabezpieczające.
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
ms.openlocfilehash: 3e5d1546c658631911f25c43e94275f00c7a5140
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60474643"
---
# <a name="set-up-security-info-preview-to-use-security-questions"></a>Skonfiguruj informacje o zabezpieczeniach (wersja zapoznawcza) na użycie tych pytań zabezpieczających
Można wykonaj następujące kroki, aby dodać metodę resetowania hasła. Po skonfigurowaniu to po raz pierwszy, można powrócić do **zabezpieczające** strony Dodawanie, aktualizowanie lub usuwanie informacji zabezpieczających.

Po skonfigurowaniu metody resetowania hasła, należy również skonfigurować metodę weryfikacji two-Factor Authentication, za pomocą [aplikacji authenticator](security-info-setup-auth-app.md), [wiadomości tekstowych](security-info-setup-text-msg.md), lub [połączeń telefonicznych](security-info-setup-phone-number.md).

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

## <a name="set-up-your-security-questions-from-the-security-info-page"></a>Skonfiguruj pytania zabezpieczające na stronie informacje zabezpieczeń
W zależności od ustawień organizacji można wybrać i Odpowiedz na kilka pytań zabezpieczeń jako jeden z metody zabezpieczeń informacji. Administrator konfiguruje liczbę pytań zabezpieczeń, które należy wybrać i odpowiedzi.

Jeśli używasz pytań zabezpieczających, firma Microsoft zaleca używanie ich razem z innej metody. Pytania zabezpieczeń może być mniej bezpieczne niż inne metody, ponieważ niektóre osoby mogą znać odpowiedzi na pytania.

> [!Note]
> Pytania zabezpieczające są przechowywane przez użytkowników i bezpiecznie w obiekcie użytkownika w katalogu, a tylko można uzyskać przez użytkownika podczas rejestracji. Nie ma możliwości dla administratora odczytać lub zmodyfikować swoje pytania lub odpowiedzi.
> 
> Jeśli nie widzisz opcji pytania zabezpieczeń, jest to możliwe, że Twoja organizacja nie zezwala na użycie tych pytań zabezpieczających podczas weryfikacji. Jeśli jest to możliwe, należy wybrać inną metodę, lub skontaktuj się z administratorem, aby uzyskać dalszą pomoc.
> 
> Konto administratora nie są dozwolone na użycie tych pytań zabezpieczających jako metody resetowania hasła. Jeśli zalogowano Cię jako administratora konta poziomu nie zobaczą te opcje.

### <a name="to-set-up-your-security-questions"></a>Aby skonfigurować pytania zabezpieczające

1. Zaloguj się do swojego konta firmowego lub szkolnego, a następnie przejdź do swojej https://myprofile.microsoft.com/ strony.

    ![Strona Moje profilu, pokazujący wyróżnione łączy informacje zabezpieczeń](media/security-info/securityinfo-myprofile-page.png)

2. Wybierz **zabezpieczające** z lewego okienka nawigacji lub linku w **zabezpieczające** zablokować, a następnie wybierz **Dodaj metodę** z **informacje zabezpieczające**  strony.

    ![Strona informacji zabezpieczających przy użyciu opcji Dodaj wyróżnione w — metoda](media/security-info/securityinfo-myprofile-addmethod-page.png)

3. Na **Dodaj metodę** wybierz opcję **pytania zabezpieczające** z listy rozwijanej, a następnie wybierz **Dodaj**.

    ![Dodaj pole Metoda, za pomocą pytań zabezpieczających wybrane](media/security-info/securityinfo-myprofile-addquestions.png)

4. Na **pytania zabezpieczające** stronie, wybierz i odpowiedzi na pytania zabezpieczające, a następnie wybierz **Zapisz**.

    ![Dodaj numer telefonu, a następnie wybierz połączeń telefonicznych](media/security-info/securityinfo-myprofile-securityquestions.png)

    Twoje informacje zabezpieczające są aktualizowane i można użyć pytania zabezpieczające, aby zweryfikować swoją tożsamość w przypadku korzystania z resetowania hasła.

## <a name="delete-security-questions-from-your-security-info-methods"></a>Usuń pytania zabezpieczające z metody informacje zabezpieczeń
Jeśli nie chcesz już użycie tych pytań zabezpieczających jako metody informacje zabezpieczeń, można usunąć go z **zabezpieczające** strony.

>[!Important]
>Jeśli usuniesz pytania zabezpieczające przez pomyłkę, nie ma możliwości jej cofnąć. Musisz ponownie, Dodaj metodę czynności opisane w [skonfiguruj pytania zabezpieczające](#set-up-your-security-questions-from-the-security-info-page) dalszej części tego artykułu.

### <a name="to-delete-your-security-questions"></a>Aby usunąć pytania zabezpieczające

1. Na **zabezpieczające** wybierz opcję **Usuń** łącze obok **pytania zabezpieczające** opcji.

    ![Łącze, aby usunąć metodę telefonu z informacje zabezpieczające](media/security-info/securityinfo-myprofile-questionsdelete.png)

2. Wybierz **tak** z okno dialogowe potwierdzenia, aby usunąć swoje **pytania zabezpieczające**. Po pytania zabezpieczające są usuwane, metoda jest usuwany z informacjom zabezpieczającym i znika ono z **zabezpieczające** strony.

## <a name="additional-security-info-methods"></a>Dodatkowe informacje o metody zabezpieczeń
Masz dodatkowe opcje jak kontakty organizacji możesz zweryfikować swoją tożsamość, oparte na tym, co możesz próby wykonania. Dostępne są następujące opcje:

- **Aplikacja Authenticator.** Pobierz i użyj aplikacji authenticator, aby uzyskać powiadomienie o zatwierdzeniu lub kod generowany losowo zatwierdzenia do resetowania dwuetapowej weryfikacji lub hasło. Aby uzyskać szczegółowe instrukcje dotyczące sposobu konfigurowania i używania aplikacji Microsoft Authenticator, zobacz [ustawić informacje zabezpieczające, aby użyć aplikacji authenticator](security-info-setup-auth-app.md).

- **Tekst urządzenia przenośnego.** Wprowadź numer urządzenia przenośnego, a następnie otrzymaj wiadomość SMS, kod służący do celów weryfikacji dwuetapowej lub hasło resetowania. Aby uzyskać szczegółowe instrukcje na temat zweryfikować swoją tożsamość za pomocą wiadomości SMS (SMS), zobacz [ustawiane informacje zabezpieczające, aby używać wiadomości tekstowych (SMS)](security-info-setup-text-msg.md).

- **Rozmowy telefoniczne pracy lub urządzenia przenośnego.** Wprowadź numer urządzenia przenośnego, a następnie uzyskać połączenie telefoniczne resetowania dwuetapowej weryfikacji lub hasło. Aby uzyskać szczegółowe instrukcje na temat zweryfikować swoją tożsamość za pomocą numeru telefonu, zobacz [ustawiane informacje zabezpieczające, aby używać połączeń telefonicznych](security-info-setup-phone-number.md).

- **Adres e-mail.** Wprowadź swój firmowy lub szkolny adres e-mail, aby otrzymywać wiadomość e-mail do resetowania hasła. Ta opcja jest dostępna na potrzeby weryfikacji dwuetapowej. Aby uzyskać szczegółowe instrukcje dotyczące sposobu konfigurowania swój adres e-mail, zobacz [ustawić informacje zabezpieczające, aby użyć adresu e-mail](security-info-setup-email.md).
   
    >[!Note]
    >Jeśli brakuje niektórych z tych opcji, prawdopodobnie najbardziej Twoja organizacja nie zezwala na jednej z tych metod. Jeśli jest to możliwe, należy wybrać metodę dostępne lub skontaktuj się z administratorem, aby uzyskać dalszą pomoc.

## <a name="next-steps"></a>Kolejne kroki

- Zagubione lub zapomniane hasło możesz zresetować w [portalu resetowania hasła](https://passwordreset.microsoftonline.com/) lub wykonując kroki przedstawione w artykule [Resetowanie hasła służbowego](user-help-reset-password.md).

- Wskazówki i pomoc dotyczące rozwiązywania problemów z logowaniem znajdują się w artykule [Nie można zalogować się na konto Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant).
