---
title: 'Azure AD: Resetowanie hasła | Dokumentacja firmy Microsoft'
description: Użyj samoobsługowego resetowania hasła, aby odzyskać dostęp do pracy lub szkołą konta użytkownika
services: active-directory
keywords: ''
documentationcenter: ''
author: barlanmsft
manager: mtillman
ms.reviewer: sahenry
ms.assetid: 7ba69b18-317a-4a62-afa3-924c4ea8fb49
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/11/2018
ms.author: barlan
ms.custom: end-user
ms.openlocfilehash: 5c850fe9eaf78ab5ee6a175e5433998e1f15ab2e
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/22/2018
ms.locfileid: "29463782"
---
# <a name="reset-your-work-or-school-password"></a>Resetowanie hasła lub szkoły

Jeśli nie pamiętasz hasła nigdy nie odebrał z działem pomocy technicznej firmy, zostały zablokowane z konta lub chcesz je zmienić, możemy pomóc. Jeśli znasz hasła i wystarczy, że można go zmienić, przejdź do [zmienić hasło](#change-my-password) sekcji.

   > [!NOTE]
   > Jeśli próbujesz Ci je odzyskać konta osobiste, takie jak Xbox, hotmail.com lub outlook.com, spróbuj sugestii w [po nie logowania się do konta Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant) artykułu.
   >

## <a name="reset-or-unlock-my-password-for-a-work-or-school-account"></a>Resetowanie lub odblokowywanie hasła do konta służbowego

Może się nie można uzyskać dostępu do konta usługi Azure Active Directory (Azure AD) z jednego z następujących powodów:

* Hasło nie działa, a chcesz je zresetować.
* Znać hasło, ale Twoje konto jest zablokowane i zechcesz je odblokować.

Dostęp do usługi Azure AD samoobsługowego resetowania hasła (SSPR) i Ci je odzyskać Twoje konto, wykonaj następujące kroki.

1. Z dowolnej pracy lub szkołą **logowania** wybierz pozycję **nie może uzyskać dostępu do konta?** łącza, a następnie wybierz **pracy konto służbowe** lub przejść bezpośrednio do [ Strony resetowania hasła](https://passwordreset.microsoftonline.com/).

    ![Nie możesz uzyskać dostępu do konta?][Login]

2. Wprowadź konto służbowe **identyfikator użytkownika**, potwierdzenia, wprowadzając znaki widoczne na ekranie, a następnie wybierz nie są robotem **dalej**.

   > [!NOTE]
   > Jeśli pracownicy działu informatycznego nie włączył tę funkcję, więc działu IT mogą pomóc w wiadomościach e-mail lub portalu sieci web we własnym pojawi się link "Skontaktuj się z administratorem".
   >
   > Jeśli potrzebujesz odblokować konto, w tym momencie wybierz opcję **znać hasło, ale nie można zarejestrować.**
   >

3. W zależności od sposobu działu IT skonfigurował SSPR powinien zostać wyświetlony co najmniej jeden z następujących metod uwierzytelniania. Użytkownik lub pracownicy działu informatycznego powinien wypełnił niektóre z tych informacji przedstawionych w [rejestrowanie się w celu samodzielnego resetowania hasła](active-directory-passwords-reset-register.md) artykułu.

   * **Wyślij wiadomość e-mail na mój alternatywny adres e-mail**
   * **Wyślij wiadomość SMS na mój telefon komórkowy**
   * **Zadzwoń na mój telefon komórkowy**
   * **Zadzwoń na mój telefon biurowy**
   * **Odpowiedz na moje pytania zabezpieczeń**

   Wybierz jedną z opcji, podaj poprawne odpowiedzi, a następnie wybierz **dalej**.

   ![Sprawdzanie danych uwierzytelniania][Verification]

4. Działu IT mogą wymagać więcej weryfikacji i może być konieczne powtórzenie krok 3 z innego wyboru.
5. Na **wybranie nowego hasła** , strony, wprowadź nowe hasło i potwierdzenie hasła, a następnie wybierz **Zakończ**. Hasło pracy lub nauki może mieć określone wymagania, które muszą być zgodne. Zaleca się, że Wybierz hasło, które jest 8 do 16 znaków i zawiera wielkich i małych liter, cyfry i znak specjalny.
6. Gdy zostanie wyświetlony komunikat **Twoje hasło zostało zresetowane**, można zalogować się przy użyciu nowego hasła.

    ![Hasło zostało zresetowane][Complete]

Teraz można uzyskać dostępu do konta. Jeśli nie masz dostępu do swojego konta, skontaktuj się z organizacji personelu IT w celu uzyskania dalszej pomocy.

Może pojawić się wiadomość e-mail z potwierdzeniem pochodzi z konta, takich jak "Microsoft w imieniu osób \<organizacji >." Jeśli nie używasz samoobsługowego resetowania hasła, aby odzyskać dostęp do Twojego konta otrzymasz wiadomość e-mail, takich jak ta, skontaktuj się z organizacji personel działu informatycznego.

## <a name="change-my-password"></a>Zmiana hasła

Jeśli już znasz hasła i chcesz je zmienić, wykonaj następujące kroki.

### <a name="change-your-password-from-the-office-365-portal"></a>Zmiana hasła w portalu usługi Office 365

Użyj tej metody, jeśli zwykle dostęp do aplikacji za pomocą portalu usługi Office:

1. Zaloguj się do Twojego [konto usługi Office 365](https://www.office.com) przy użyciu istniejącego hasła.
2. Wybierz profil, na stronie prawym górnym, a następnie wybierz **konta**.
3. Wybierz **bezpieczeństwo i prywatność** > **hasło**.
4. Wprowadź stare hasło, ustaw i Potwierdź nowe hasło, a następnie wybierz **przesyłania**.

### <a name="change-your-password-from-the-azure-access-panel"></a>Zmiana hasła w panelu dostępu platformy Azure

Użyj tej metody, jeśli zwykle dostęp do aplikacji w panelu dostępu platformy Azure (MyApps):

1. Zaloguj się do [panelu dostępu platformy Azure](https://myapps.microsoft.com/) przy użyciu istniejącego hasła.
2. Wybierz profil, na stronie prawym górnym, a następnie wybierz **profilu**.
3. Wybierz **Zmień hasło**.
4. Wprowadź stare hasło, ustaw i Potwierdź nowe hasło, a następnie wybierz **przesyłania**.

## <a name="reset-password-at-sign-in"></a>Resetowanie hasła podczas logowania

Jeśli administrator ma włączone funkcje, możesz teraz przeglądać łącze do **resetowania hasła** na ekranie logowania systemu Windows 10 spadek twórców aktualizacji.

![Ekran logowania][LoginScreen]

Wybierz **resetowania hasła** łącze, aby otworzyć proces uruchamiania SSPR na ekranie logowania, dzięki czemu można zresetować hasło, bez konieczności zaloguj się do normalnego doświadczenia opartych na sieci web.

1. Potwierdź swój identyfikator użytkownika, a następnie wybierz **dalej**.
2. Wybierz i Potwierdź metody kontaktu na potrzeby weryfikacji. Działu IT mogą wymagać więcej weryfikacji i może być konieczne Powtórz ten krok z innego wyboru.

   ![Metoda kontaktu][ContactMethod]

3. Na **utworzenie nowego hasła** , strony, wprowadź nowe hasło i potwierdzenie hasła, a następnie wybierz **dalej**. Zaleca się, że hasło jest długa 8 do 16 znaków i składa się z wielkie i małe litery, cyfry i znaki specjalne.

   ![Resetowanie hasła][ResetPassword]

4. Gdy zostanie wyświetlony komunikat **Twoje hasło zostało zresetowane**, wybierz pozycję **Zakończ**.

Teraz można uzyskać dostępu do konta. Jeśli nie, skontaktuj się z organizacji personelu IT w celu uzyskania dalszej pomocy.

## <a name="common-problems-and-their-solutions"></a>Typowe problemy i ich rozwiązania

 Poniżej przedstawiono niektóre typowe przypadki błąd i ich rozwiązania:

| Przypadek błędu| Jakie błąd znaleźć?| Rozwiązanie |
| --- | --- | --- |
| Widać wystąpił błąd podczas próby zmiany hasła. | Niestety hasło zawiera słowo, wyrażenie lub wzorca, który umożliwia łatwe do odgadnięcia hasła. Spróbuj ponownie, używając innego hasła. | Wybierz trudniej odgadnąć hasło. |
| Pojawia się Strona "Skontaktuj się z administratorem" po wprowadzeniu mój identyfikator użytkownika | Skontaktuj się z administratorem. <br> <br> Wykryliśmy, że hasło konta użytkownika nie jest zarządzany przez firmę Microsoft. W związku z tym nie możemy automatycznie zresetować hasła. <br> <br> Należy skontaktować się z działu IT, aby uzyskać dalszą pomoc. | Ten komunikat jest wyświetlany, ponieważ działu IT zarządza hasła w środowisku lokalnym. Nie można zresetować hasło przy użyciu łącza "Nie może uzyskać dostępu do konta". <br> <br> Aby zresetować hasło, bezpośrednio uzyskać pomoc, skontaktuj się z pracownikami działu IT i go poinformować, że chcesz zresetować hasło, więc ich włączenia tej funkcji można.|
| Pojawia się komunikat o błędzie "Twoje konto nie ma uprawnień do resetowania hasła" po wprowadzeniu mój identyfikator użytkownika | Twoje konto nie jest włączona w celu resetowania haseł. <br> <br> Przepraszamy, ale pracownicy działu informatycznego nie skonfigurował konta w do tej usługi. <br> <br> Jeśli chcesz, możemy skontaktować się z administratorem w Twojej organizacji, aby zresetował Twoje hasło. | Ten komunikat jest wyświetlany, ponieważ pracownicy działu informatycznego nie włączył resetowania haseł dla organizacji z łącza "Nie może uzyskać dostępu do konta" lub nie ma licencji można korzystać z funkcji. <br> <br> Aby zresetować hasło, wybierz opcję "Skontaktuj się z administratorem link" Aby wysłać wiadomość e-mail do firmy przez personel działu informatycznego i daj znać, aby zresetować hasło, więc ich włączenia tej funkcji można. |
| Pojawia się komunikat o błędzie "Nie można zweryfikować konto" po wprowadzeniu mój identyfikator użytkownika | Nie można zweryfikować Twoje konto. <br> <br> Jeśli chcesz, możemy skontaktować się z administratorem w Twojej organizacji, aby zresetował Twoje hasło. | Ten komunikat jest wyświetlany, ponieważ jest włączona w celu resetowania haseł, ale nie została zarejestrowana do korzystania z usługi. Aby zarejestrować do resetowania hasła, należy przejść do https://aka.ms/ssprsetup po ma odzyskać dostęp do Twojego konta. <br> <br> Aby zresetować hasło, wybierz łącze "Skontaktuj się z administratorem", aby wysłać wiadomość e-mail do firmy personel działu informatycznego. |

## <a name="next-steps"></a>Kolejne kroki

* [Jak zarejestrować się do używania samoobsługowego resetowania hasła](active-directory-passwords-reset-register.md)
* [Strona rejestracji w celu resetowania hasła](https://aka.ms/ssprsetup)
* [Portal resetowania hasła](https://passwordreset.microsoftonline.com/)
* [Nie można zalogować się do konta Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant)

[Login]: ./media/active-directory-passwords-update-your-own-password/reset-1-login.png "Strona logowania — Nie możesz uzyskać dostępu do konta?"
[Verification]: ./media/active-directory-passwords-update-your-own-password/reset-2-verification.png "Weryfikowanie danych uwierzytelniania"
[Change]: ./media/active-directory-passwords-update-your-own-password/reset-3-change.png "Zmiana hasła"
[Complete]: ./media/active-directory-passwords-update-your-own-password/reset-4-complete.png "Hasło zostało zresetowane"
[LoginScreen]: ./media/active-directory-passwords-update-your-own-password/login-screen.png "Windows 10 spadek twórców aktualizacji ekranu resetowania hasła łącza"
[ContactMethod]: ./media/active-directory-passwords-update-your-own-password/reset-contact-method-screen.png "Weryfikowanie danych uwierzytelniania"
[ResetPassword]: ./media/active-directory-passwords-update-your-own-password/reset-password-screen.png "Zmiana hasła"
