---
title: Zarejestruj się w celu samodzielnego resetowania haseł — Azure Active Directory
description: Zarejestruj dane uwierzytelniania dla usługi Azure AD hasła samoobsługi resetowania
services: active-directory
keywords: ''
documentationcenter: ''
author: barlanmsft
manager: mtillman
ms.reviewer: sahenry
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/11/2018
ms.author: barlan
ms.custom: end-user;seohack1
ms.openlocfilehash: dbb82e125381a4307ed742294f880600612ffc52
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/22/2018
ms.locfileid: "29460479"
---
# <a name="register-for-self-service-password-reset"></a>Rejestrowanie na potrzeby samoobsługowego resetowania hasła

> [!IMPORTANT]
> Jesteś tutaj ponieważ nie można zalogować się? Jeśli tak, zobacz [zresetować hasło lub szkoły](active-directory-passwords-update-your-own-password.md).

Jako użytkownik końcowy może zresetować hasło lub odblokować konto samodzielnie, jeśli używasz usługi Azure Active Directory (Azure AD) samoobsługowego resetowania hasła (SSPR). Przed użyciem tej funkcji, należy zarejestrować metody uwierzytelniania lub potwierdź metody uwierzytelniania wstępnie zdefiniowane, które administrator ma wypełnione.

## <a name="register-or-confirm-authentication-data-with-sspr"></a>Rejestrowanie lub potwierdzanie danych uwierzytelniania na potrzeby samoobsługowego resetowania hasła

1. Otwórz przeglądarkę sieci web na urządzeniu i przejdź do [strony rejestracji resetowania haseł](https://aka.ms/ssprsetup).
2. Wprowadź nazwy użytkownika i hasła, zapewnianej przez administratora.
3. W zależności od tego, jak działu IT skonfigurował rzeczy są dostępne do konfigurowania i sprawdź, co najmniej jeden z następujących opcji. Jeśli administrator ma możliwość używania danych osobowych, można wypełnić niektóre informacje dla Ciebie.
    * **Telefon biurowy**: tylko administrator może ustawić tę opcję.
    * **Numer telefonu uwierzytelniania**: Ustaw tę opcję na inny numer telefonu, który ma dostęp do. Przykładem jest telefonu komórkowego, czy może odbierać tekstu albo wywołanie.
    * **Adres E-mail uwierzytelniania**: Ustaw tę opcję na alternatywny adres e-mail dostępnym bez użycia chcesz zresetować hasło.
    * **Pytania zabezpieczające**: administrator została zatwierdzona tej listy do odpowiedzi na pytania. Nie można użyć tego samego pytania lub odpowiedzi na więcej niż raz.
4. Podaj i sprawdź informacje, która wymaga od administratora. Jeśli więcej niż jedna opcja jest dostępna, zalecamy zarejestrowanie wielu metod. Zapewnia to elastyczność podczas jednej z metod jest niedostępna. Przykładem jest podczas podróży i nie uda się uzyskać dostępu do telefon biurowy.

    ![Zarejestruj metod uwierzytelniania, a następnie wybierz przycisk Zakończ][Register]

5. Wybierz **Zakończ**. Można teraz używać SSPR, gdy trzeba w przyszłości.

Po wprowadzeniu danych dla **numer telefonu uwierzytelniania** lub **E-mail uwierzytelniania**, nie jest widoczne w katalogu globalnego. Jedynie Ty i Twoi administratorzy mogą wyświetlać te dane. Tylko widać odpowiedzi na pytania zabezpieczeń.

Administratorzy w Twojej organizacji mogą wymagać potwierdzić metody uwierzytelniania po upływie określonego czasu upewnij się, że nadal masz odpowiednie metody zarejestrowany.

## <a name="common-problems-and-their-solutions"></a>Typowe problemy i ich rozwiązania

 Poniżej przedstawiono niektóre typowe przypadki błąd i ich rozwiązania:

| Przypadek błędu| Jakie błąd znaleźć?| Rozwiązanie |
| --- | --- | --- |
| Pojawia się Strona "Skontaktuj się z administratorem" po wprowadzeniu mój identyfikator użytkownika | Skontaktuj się z administratorem. <br> <br> Wykryliśmy, że hasło konta użytkownika nie jest zarządzany przez firmę Microsoft. W związku z tym nie możemy automatycznie zresetować hasła. <br> <br> Aby uzyskać dalszą pomoc, skontaktuj się z działu IT. | Ten komunikat jest wyświetlany, ponieważ działu IT zarządza hasła w środowisku lokalnym i nie pozwala na zresetowanie hasła z **nie może uzyskać dostępu do konta** łącza. <br> <br> Aby zresetować hasło, skontaktuj się z działu IT bezpośrednio w celu uzyskania pomocy. Daj znać, że chcesz zresetować hasło, więc ich włączenia tej funkcji można.|
| Pojawia się komunikat o błędzie "Twoje konto nie ma uprawnień do resetowania hasła" po wprowadzeniu mój identyfikator użytkownika | Twoje konto nie jest włączona w celu resetowania haseł. <br> <br> Przepraszamy, ale pracownicy działu informatycznego nie skonfigurował tego konta do korzystania z tej usługi. <br> <br> Jeśli chcesz, możemy skontaktować się z administratorem w Twojej organizacji, aby zresetował Twoje hasło. | Ten komunikat jest wyświetlany, ponieważ pracownicy działu informatycznego nie włączył resetowania haseł dla organizacji z **nie może uzyskać dostępu do konta** łącze lub nie ma licencji można korzystać z funkcji. <br> <br> Aby zresetować hasło, wybierz **skontaktuj się z administratorem** łącza. Wiadomości e-mail będą wysyłane do firmy personel działu informatycznego. Wiadomości e-mail umożliwia go poinformować, że chcesz zresetować hasło, więc ich włączenia tej funkcji można. |
| Pojawia się komunikat o błędzie "nie można zweryfikować konto" po wprowadzeniu mój identyfikator użytkownika | Nie można zweryfikować Twoje konto. <br> <br> Jeśli chcesz, możemy skontaktować się z administratorem w Twojej organizacji, aby zresetował Twoje hasło. | Ten komunikat jest wyświetlany, ponieważ jest włączona w celu resetowania haseł, ale nie został zarejestrowany do korzystania z usługi. Aby zarejestrować do resetowania hasła, przejdź do [strony rejestracji resetowania haseł](https://aka.ms/ssprsetup) po ma odzyskać dostęp do Twojego konta. <br> <br> Aby zresetować hasło, wybierz **skontaktuj się z administratorem** łącze, aby wysłać wiadomość e-mail w Twojej firmie personel działu informatycznego. |

## <a name="next-steps"></a>Kolejne kroki

* [Zmienianie hasła przy użyciu samodzielnego resetowania hasła](active-directory-passwords-update-your-own-password.md)
* [Strona rejestracji w celu resetowania hasła](https://aka.ms/ssprsetup)
* [Portal resetowania hasła](https://passwordreset.microsoftonline.com/)
* [Jeśli nie możesz się zarejestrować na swoim koncie Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant)

[Register]: ./media/active-directory-passwords-reset-register/register-2-methods.png "Strona rejestracji resetowania hasła przedstawiający zarejestrowane metody i przycisk Zakończ"

