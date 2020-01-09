---
title: Rejestrowanie informacji o uwierzytelnianiu w celu zresetowania własnego hasła — Azure AD
description: Zarejestruj informacje o metodzie weryfikacji dla funkcji samoobsługowego resetowania hasła usługi Azure AD, aby można było zresetować własne hasło bez pomocy administratora.
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
ms.openlocfilehash: a6dddd13f31366e5a99d6c68ab82b048de4064b2
ms.sourcegitcommit: a100e3d8b0697768e15cbec11242e3f4b0e156d3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/06/2020
ms.locfileid: "75681365"
---
# <a name="register-your-verification-method-info-to-reset-your-own-password"></a>Zarejestruj informacje o metodzie weryfikacji, aby zresetować własne hasło

> [!IMPORTANT]
> Jesteś tutaj, ponieważ nie możesz się zalogować? Jeśli tak, zobacz [Resetowanie hasła](active-directory-passwords-update-your-own-password.md)służbowego.

Jako użytkownik końcowy możesz zresetować hasło lub odblokować konto przez siebie, jeśli używasz usługi Azure Active Directory (Azure AD) samoobsługowego resetowania hasła (SSPR). Aby móc korzystać z tej funkcji, należy zarejestrować metody weryfikacji lub potwierdzić wstępnie zdefiniowane metody weryfikacji, które zostały wypełnione przez administratora.

## <a name="register-or-confirm-authentication-data-with-sspr"></a>Rejestrowanie lub potwierdzanie danych uwierzytelniania na potrzeby samoobsługowego resetowania hasła

1. Otwórz przeglądarkę sieci Web na urządzeniu i przejdź do [strony rejestracji resetowania haseł](https://account.activedirectory.windowsazure.com/PasswordReset/Register.aspx?regref=ssprsetup).
2. Wprowadź nazwę użytkownika i hasło podane przez administratora.
3. W zależności od tego, jak pracownicy IT skonfigurowali elementy, można skonfigurować i zweryfikować co najmniej jedną z następujących opcji. Jeśli administrator ma uprawnienia do korzystania z Twoich informacji, może wypełnić niektóre informacje.
    * **Telefon biurowy**: tylko administrator może ustawić tę opcję.
    * **Telefon uwierzytelniania**: Ustaw tę opcję na inny numer telefonu, do którego masz dostęp. Przykładem jest numer telefonu komórkowego, który może odbierać tekst lub wywołanie.
    * **Wiadomość e-mail z uwierzytelnianiem**: Ustaw tę opcję na alternatywny adres e-mail, do którego można uzyskać dostęp bez użycia hasła, które chcesz zresetować.
    * **Pytania zabezpieczające**: Administrator zatwierdził tę listę pytań, na które można odpowiedzieć. Nie można użyć tego samego pytania lub odpowiedzi więcej niż raz.
4. Podaj i sprawdź informacje wymagane przez administratora. Jeśli dostępna jest więcej niż jedna opcja, zalecamy zarejestrowanie wielu metod. Zapewnia to elastyczność, gdy jedna z metod jest niedostępna. Przykładem jest podróż i nie można uzyskać dostępu do telefonu biurowego.

    ![Zarejestruj metody weryfikacyjne i wybierz pozycję Zakończ.][Register]

5. Wybierz pozycję **Zakończ**. Możesz teraz używać SSPR, gdy zachodzi taka potrzeba w przyszłości.

Jeśli wprowadzisz dane dla **telefonu uwierzytelniania** lub **poczty e-mail**, nie jest ona widoczna w katalogu globalnym. Jedynie Ty i Twoi administratorzy mogą wyświetlać te dane. Odpowiedzi na pytania zabezpieczające są widoczne tylko dla użytkownika.

Administratorzy mogą wymagać potwierdzenia metody weryfikacyjnej po upływie czasu, aby upewnić się, że nadal zarejestrowano odpowiednie metody.

## <a name="common-problems-and-their-solutions"></a>Typowe problemy i ich rozwiązania

 Poniżej przedstawiono kilka typowych przypadków błędów i ich rozwiązań:

| Przypadek błędu| Jakiego błędu widzisz?| Rozwiązanie |
| --- | --- | --- |
| Po wprowadzeniu identyfikatora użytkownika otrzymuję stronę "Skontaktuj się z administratorem". | Skontaktuj się z administratorem. <br> <br> Wykryliśmy, że hasło konta użytkownika nie jest zarządzane przez firmę Microsoft. W związku z tym nie można automatycznie zresetować hasła. <br> <br> Skontaktuj się z pracownikami IT w celu uzyskania dalszej pomocy. | Ten komunikat jest wyświetlany, ponieważ personel IT zarządza hasłem w środowisku lokalnym i nie pozwala na zresetowanie hasła przy użyciu linku **nie można uzyskać dostępu do konta** . <br> <br> Aby zresetować hasło, skontaktuj się bezpośrednio z pracownikami IT w celu uzyskania pomocy. Daj im znać, że chcesz zresetować hasło, aby umożliwić im włączenie tej funkcji.|
| Otrzymuję komunikat o błędzie "Twoje konto nie jest włączone do resetowania hasła" po wprowadzeniu identyfikatora użytkownika | Twoje konto nie ma włączonej obsługi resetowania hasła. <br> <br> Niestety, pracownicy IT nie skonfigurowali Twojego konta do użycia w ramach tej usługi. <br> <br> Jeśli chcesz, możemy skontaktować się z administratorem w organizacji w celu zresetowania hasła. | Ten komunikat jest wyświetlany, ponieważ personel IT nie włączył resetowania hasła dla Twojej organizacji z poziomu linku **nie można uzyskać dostępu do konta** lub nie ma licencji na korzystanie z tej funkcji. <br> <br> Aby zresetować hasło, wybierz link **skontaktuj się z administratorem** . Wiadomość e-mail zostanie wysłana do działu IT Twojej firmy. Wiadomość e-mail z informacją o tym, że chcesz zresetować hasło, aby umożliwić im włączenie tej funkcji. |
| Otrzymuję komunikat o błędzie "nie można zweryfikować Twojego konta" po wprowadzeniu identyfikatora użytkownika | Nie można zweryfikować Twojego konta. <br> <br> Jeśli chcesz, możemy skontaktować się z administratorem w organizacji w celu zresetowania hasła. | Ten komunikat jest wyświetlany, ponieważ włączono funkcję resetowania haseł, ale nie zarejestrowano Cię do korzystania z usługi. Aby zarejestrować się w celu zresetowania hasła, przejdź do [strony rejestracji resetowania hasła](https://aka.ms/ssprsetup) po odzyskania dostępu do konta. <br> <br> Aby zresetować hasło, wybierz link **skontaktuj się z administratorem** w celu wysłania wiadomości e-mail do działu IT Twojej firmy. |

## <a name="next-steps"></a>Następne kroki

* [Zmienianie hasła przy użyciu funkcji samoobsługowego resetowania hasła](active-directory-passwords-update-your-own-password.md)
* [Strona rejestracji w celu resetowania hasła](https://aka.ms/ssprsetup)
* [Portal resetowania hasła](https://passwordreset.microsoftonline.com/)
* [Gdy nie możesz zalogować się do konto Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant)

[Register]: ./media/active-directory-passwords-reset-register/register-2-methods.png "Strona rejestracji resetowania hasła przedstawiająca zarejestrowane metody i przycisk Zakończ"

