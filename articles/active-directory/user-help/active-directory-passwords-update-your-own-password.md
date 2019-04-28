---
title: Resetowanie hasła — usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Używać samoobsługowego resetowania hasła, aby odzyskać dostęp do służbowej firmowego lub szkolnego konta użytkownika
services: active-directory
author: eross-msft
manager: daveba
ms.reviewer: sahenry
ms.assetid: 7ba69b18-317a-4a62-afa3-924c4ea8fb49
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 01/11/2018
ms.author: lizross
ms.custom: end-user
ms.collection: M365-identity-device-management
ms.openlocfilehash: d16b54df5b8392a294036978e0b917ecdb3ce1ca
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60862265"
---
# <a name="reset-your-work-or-school-password"></a>Resetowanie hasła służbowego

Jeśli nie pamiętasz hasła nigdy nie otrzyma jeden z pomocy technicznej Twojej firmy, została zablokowana ze swojego konta lub chcesz je zmienić, firma Microsoft może pomóc. Jeśli znasz swoje hasło i po prostu chcesz je zmienić, w dalszym ciągu [zmiana hasła](#change-my-password) sekcji.

   > [!NOTE]
   > Jeśli próbujesz Ci je odzyskać konto osobiste, takich jak Xbox, hotmail.com lub outlook.com, spróbuj sugestie w [Jeśli nie możesz zalogować się na koncie Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant) artykułu.
   >

## <a name="reset-or-unlock-my-password-for-a-work-or-school-account"></a>Resetowanie lub odblokowywanie hasła do konta służbowego

Może się nie można uzyskać dostępu do konta usługi Azure Active Directory (Azure AD) z jednego z następujących powodów:

* Hasło nie działa, a chcesz je zresetować.
* Znać hasło, ale Twoje konto jest zablokowane i zechcesz je odblokować.

Dostęp do usługi Azure AD samoobsługowego resetowania haseł (SSPR) i do niej wrócić do swojego konta, wykonaj następujące kroki.

1. Za pomocą dowolnego pracy lub szkołą **logowania** wybierz opcję **nie może uzyskać dostępu do konta?** połączyć, a następnie wybierz pozycję **pracy konta służbowego** lub przejdź bezpośrednio do [ Strony resetowania hasła](https://passwordreset.microsoftonline.com/).

    ![Nie możesz uzyskać dostępu do konta?][Login]

2. Wprowadź pracy lub nauki **identyfikator użytkownika**, udowodnić, że nie jesteś robotem, wprowadzając znaki widoczne na ekranie, a następnie wybierz **dalej**.

   > [!NOTE]
   > Jeśli dział IT nie włączył tej funkcji, więc dział informatyczny może pomóc za pośrednictwem poczty e-mail lub własnego portalu sieci web pojawi się link "Skontaktuj się z administratorem".
   >
   > Jeśli potrzebujesz odblokować konto, w tym momencie wybierz opcję **swoje hasło, ale nadal nie zalogować.**
   >

3. W zależności od jak dział informatyczny skonfigurował Samoobsługowe Resetowanie HASEŁ powinien zostać wyświetlony co najmniej jeden z następujących metod uwierzytelniania. Użytkownik lub pracownicy działu IT powinny wypełnili niektóre z tych informacji przedstawionych w [rejestrowanie na potrzeby samoobsługowego resetowania haseł](active-directory-passwords-reset-register.md) artykułu.

   * **Wyślij wiadomość e-mail na mój alternatywny adres e-mail**
   * **Wyślij wiadomość SMS na mój telefon komórkowy**
   * **Zadzwoń na mój telefon komórkowy**
   * **Zadzwoń na mój telefon biurowy**
   * **Odpowiedz na moje pytania zabezpieczeń**

   Wybierz jedną z opcji, podaj poprawne odpowiedzi, a następnie wybierz **dalej**.

   ![Sprawdzanie danych uwierzytelniania][Verification]

4. Dział IT może wymagać dodatkowej weryfikacji i może być konieczne powtórzenie kroku 3 z wyborem innej opcji.
5. Na **wybranie nowego hasła** , strony, wprowadź nowe hasło i Potwierdź hasło, a następnie wybierz **Zakończ**. Hasło do służbowego może mieć określone wymagania, które należy spełnić. Sugerujemy, że Wybierz hasło, które jest 8 do 16 znaków i zawiera wielkie i małe litery, liczbę i znak specjalny.
6. Gdy zostanie wyświetlony komunikat **Twoje hasło zostało zresetowane**, można zalogować się przy użyciu nowego hasła.

    ![Hasło zostało zresetowane][Complete]

Teraz można uzyskać dostępu do konta. Jeśli nie można uzyskać dostępu do konta, należy skontaktować się ze swojej organizacji działem IT w celu uzyskania dalszej pomocy.

Można otrzymać wiadomość e-mail z potwierdzeniem, która pochodzi z konta takiego jak "firmy Microsoft w imieniu \<organizacji >." Jeśli nie używasz samoobsługowego resetowania hasła, aby odzyskać dostęp do Twojego konta otrzymasz wiadomość e-mail, takich jak ta, skontaktuj się z Twojej organizacji pracowników wsparcia informatycznego.

## <a name="change-my-password"></a>Zmiana hasła

Jeśli znasz swoje hasło i chcesz je zmienić, wykonaj następujące kroki.

### <a name="change-your-password-from-the-office-365-portal"></a>Zmiana hasła w portalu usługi Office 365

Jeśli zwykle dostęp do aplikacji za pośrednictwem portalu pakietu Office, należy użyć tej metody:

1. Zaloguj się do Twojej [konta usługi Office 365](https://www.office.com) przy użyciu istniejącego hasła.
2. Wybierz swój profil w prawym górnym rogu po stronie, a następnie wybierz **wyświetlić konto**.
3. Wybierz **zabezpieczenia i ochrona prywatności** > **hasło**.
4. Wprowadź stare hasło, ustaw i Potwierdź nowe hasło, a następnie wybierz **przesyłania**.

### <a name="change-your-password-from-the-azure-access-panel"></a>Zmiana hasła w panelu dostępu platformy Azure

Użyj tej metody, jeśli zwykle dostęp do aplikacji przy użyciu panelu dostępu platformy Azure (Moje aplikacje):

1. Zaloguj się do [panelu dostępu platformy Azure](https://myapps.microsoft.com/) przy użyciu istniejącego hasła.
2. Wybierz swój profil w prawym górnym rogu po stronie, a następnie wybierz **profilu**.
3. Wybierz **Zmień hasło**.
4. Wprowadź stare hasło, ustaw i Potwierdź nowe hasło, a następnie wybierz **przesyłania**.

## <a name="reset-password-at-sign-in"></a>Resetowanie hasła podczas logowania

Jeśli administrator włączył funkcje, możesz teraz widoczny link do **Resetuj hasło** na ekranie logowania systemu Windows 10 Fall Creators Update.

![Ekran logowania][LoginScreen]

Wybierz **Resetuj hasło** link, aby otworzyć środowiska samoobsługowego resetowania HASEŁ na ekranie logowania, dzięki czemu możesz zresetować hasła, bez konieczności zaloguj się do dostępu do normalnego internetowy interfejs.

1. Potwierdź swój identyfikator użytkownika, a następnie wybierz **dalej**.
2. Wybierz i upewnij się, skontaktuj się z pomocą metodę weryfikacji. Dział IT może wymagać dodatkowej weryfikacji i może być konieczne powtórzenie tego kroku z wyborem innej opcji.

   ![Metoda kontaktu][ContactMethod]

3. Na **Utwórz nowe hasło** , strony, wprowadź nowe hasło i Potwierdź hasło, a następnie wybierz **dalej**. Zaleca się, że hasło 8 do 16 znaków długo i składa się z wielkie i małe litery, cyfry i znaki specjalne.

   ![Resetowanie hasła][ResetPassword]

4. Gdy zostanie wyświetlony komunikat **Twoje hasło zostało zresetowane**, wybierz opcję **Zakończ**.

Teraz można uzyskać dostępu do konta. Jeśli nie, skontaktuj się z Twojej organizacji działem IT w celu uzyskania dalszej pomocy.

## <a name="common-problems-and-their-solutions"></a>Typowe problemy i ich rozwiązania

 Poniżej przedstawiono niektóre typowe przypadki błędów i sposoby ich rozwiązywania:

| Przypadki błędów| Jaki dokładnie błąd widzisz?| Rozwiązanie |
| --- | --- | --- |
| Widzę błąd podczas próby zmiany hasła. | Niestety Twoje hasło zawiera słowo, frazę lub wzorzec, który sprawia, że hasło jest łatwe do odgadnięcia. Spróbuj ponownie, używając innego hasła. | Wybierz hasło, które jest trudniejsze do odgadnięcia. |
| Pojawia się Strona "Skontaktuj się z administratorem" po wprowadzeniu mój identyfikator użytkownika | Skontaktuj się z administratorem. <br> <br> Wykryliśmy, że hasło konta użytkownika nie jest zarządzana przez firmę Microsoft. W rezultacie nie możemy automatycznie resetowania hasła. <br> <br> Musisz skontaktować się z działu IT, aby uzyskać dalszą pomoc. | Widzisz ten komunikat, ponieważ dział IT zarządza hasła w środowisku w środowisku lokalnym. Nie można zresetować hasło przy użyciu linku "Nie może uzyskać dostępu do konta". <br> <br> Do zresetowania hasła, bezpośrednio w celu uzyskania pomocy skontaktuj się z pomocą działu IT i poinformuj ich o tym, że chcesz zresetować hasło, dzięki czemu mogą włączyć tę funkcję dla Ciebie.|
| Otrzymuję błąd "Twoje konto nie jest włączone do resetowania hasła", który po wprowadzeniu mój identyfikator użytkownika | Twoje konto nie jest włączone do resetowania hasła. <br> <br> Przepraszamy, ale działu IT nie skonfigurował Twoje konto w używać tej usługi. <br> <br> Jeśli chcesz, możemy skontaktować się z administratorem w Twojej organizacji, aby zresetował Twoje hasło. | Widzisz ten komunikat, ponieważ dział informatyczny nie włączył resetowania hasła dla organizacji z linku "Nie może uzyskać dostępu do konta" lub nie można używać funkcji licencjonowane. <br> <br> Aby zresetować hasło, wybierz pozycję "Skontaktuj się z administratorem łącze" Aby wysłać wiadomość e-mail do Twojej firmy przez personel IT i poinformuj ich, aby zresetować hasło, dzięki czemu mogą włączyć tę funkcję dla Ciebie. |
| Otrzymuję błąd "Nie można zweryfikować Twoje konto", który po wprowadzeniu mój identyfikator użytkownika | Nie można zweryfikować Twoje konto. <br> <br> Jeśli chcesz, możemy skontaktować się z administratorem w Twojej organizacji, aby zresetował Twoje hasło. | Widzisz ten komunikat, ponieważ masz uprawnień do resetowania hasła, ale nie została zarejestrowana do korzystania z usługi. Aby zarejestrować się do resetowania hasła, przejdź do https://aka.ms/ssprsetup po ma się odzyskać dostęp do Twojego konta. <br> <br> Aby zresetować hasło, wybierz link "Skontaktuj się z administratorem", aby wysłać wiadomość e-mail do Twojej firmy personel IT. |

## <a name="next-steps"></a>Kolejne kroki

* [Jak zarejestrować się do używania samoobsługowego resetowania hasła](active-directory-passwords-reset-register.md)
* [Strona rejestracji w celu resetowania hasła](https://aka.ms/ssprsetup)
* [Portal resetowania hasła](https://passwordreset.microsoftonline.com/)
* [Nie można zalogować się do konta Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant)

[Login]: ./media/active-directory-passwords-update-your-own-password/reset-1-login.png "Strona logowania — Nie możesz uzyskać dostępu do konta?"
[Verification]: ./media/active-directory-passwords-update-your-own-password/reset-2-verification.png "Weryfikowanie danych uwierzytelniania"
[Change]: ./media/active-directory-passwords-update-your-own-password/reset-3-change.png "Zmiana hasła"
[Complete]: ./media/active-directory-passwords-update-your-own-password/reset-4-complete.png "Hasło zostało zresetowane"
[LoginScreen]: ./media/active-directory-passwords-update-your-own-password/login-screen.png "Link systemu Windows 10 Fall Creators Update logowania ekranu resetowania hasła"
[ContactMethod]: ./media/active-directory-passwords-update-your-own-password/reset-contact-method-screen.png "Weryfikowanie danych uwierzytelniania"
[ResetPassword]: ./media/active-directory-passwords-update-your-own-password/reset-password-screen.png "Zmiana hasła"
