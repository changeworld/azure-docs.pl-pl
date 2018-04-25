---
title: Podręcznik wdrażania samoobsługowego resetowania haseł — Azure Active Directory
description: Porady pomagające w pomyślnym wdrażaniu samoobsługowego resetowania haseł w usłudze Azure AD
services: active-directory
keywords: ''
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry
ms.assetid: f8cd7e68-2c8e-4f30-b326-b22b16de9787
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/11/2018
ms.author: joflore
ms.custom: it-pro;seohack1
ms.openlocfilehash: 22086a0d9fc774b75e1b0873188b16fc548d0791
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2018
---
# <a name="how-to-successfully-roll-out-self-service-password-reset"></a>Jak pomyślnie wdrożyć funkcję samoobsługowego resetowania haseł

Aby zapewnić bezproblemowe wdrożenie funkcji samoobsługowego resetowania haseł (SSPR, self-service password reset) usługi Azure Active Directory (Azure AD), większość klientów wykonuje następujące czynności:

1. [Włącz resetowanie haseł w katalogu](../active-directory-passwords-getting-started.md).
2. [Skonfiguruj lokalne uprawnienia usługi Active Directory do zapisywania zwrotnego haseł](../active-directory-passwords-writeback.md#active-directory-permissions).
3. [Konfigurowanie zapisywania zwrotnego haseł](../active-directory-passwords-writeback.md#configure-password-writeback) w celu zapisywania haseł z usługi Azure AD z powrotem w katalogu lokalnym.
4. [Przypisz i zweryfikuj wymagane licencje](concept-sspr-licensing.md).
5. Określ, czy chcesz zastosować wdrażanie stopniowe. Jeśli chcesz przeprowadzić stopniowe wdrażanie funkcji samoobsługowego resetowania haseł, możesz ograniczyć dostęp do jednej grupy użytkowników i przeprowadzić dla tej grupy pilotażowe wdrożenie programu. Aby przeprowadzić wdrożenie dla określonej grupy, ustaw przełącznik **Funkcja samoobsługowego resetowania hasła jest włączona** na pozycję **Wybrano** i wybierz grupę zabezpieczeń, której chcesz umożliwić resetowanie haseł. 
6. Wypełnij [dane uwierzytelniania](howto-sspr-authenticationdata.md) potrzebne do zarejestrowania użytkowników, takie jak ich numer telefonu służbowego, numer telefonu komórkowego i alternatywny adres e-mail.
7. [Dostosuj środowisko logowania do usługi Azure AD, aby uwzględnić oznaczenie marką firmy](concept-sspr-customization.md).
8. Naucz użytkowników, jak korzystać z funkcji SSPR. Wyślij im instrukcje dotyczące rejestrowania się i resetowania haseł.
9. Określ, kiedy chcesz wymuszać rejestrację. Rejestrację można wymusić w dowolnym momencie. Możesz także wymagać od użytkowników ponownego potwierdzania ich informacji uwierzytelniających po określonym czasie.
10. Użyj funkcji raportowania. Po upływie pewnego czasu możesz przejrzeć dane dotyczące użycia i rejestracji użytkowników, korzystając z [funkcji raportowania oferowanej przez usługę Azure AD](howto-sspr-reporting.md).
11. Włącz resetowanie haseł. Gdy wszystko będzie gotowe, włącz resetowanie haseł dla wszystkich użytkowników, ustawiając przełącznik **Funkcja samoobsługowego resetowania hasła jest włączona** na wartość **Wszystkie**. 

   > [!NOTE]
   > Zmiana tego ustawienia z opcji Wybrana grupa na Wszyscy nie powoduje unieważnienia istniejących danych uwierzytelniania, zarejestrowanych przez użytkownika w grupie testowej. Skonfigurowani użytkownicy z zarejestrowanymi prawidłowymi danymi uwierzytelniania będą nadał działać.

12. [Umożliwianie użytkownikom systemu Windows 10 resetowania hasła na ekranie logowania](../active-directory-passwords-login.md).

   > [!IMPORTANT]
   > Przetestuj samoobsługowe resetowanie haseł na koncie użytkownika, a nie na koncie administratora, ponieważ firma Microsoft narzuca silne wymagania w zakresie uwierzytelniania dla kont administratorów platformy Azure. Aby uzyskać więcej informacji na temat zasad haseł administratorów, zobacz nasz [artykuł dotyczący zasad haseł](concept-sspr-policy.md#administrator-password-policy-differences).

## <a name="email-based-rollout"></a>Wdrożenie bazujące na poczcie e-mail

Wielu klientów uważa, że najłatwiejszym sposobem zachęcenia użytkowników do korzystania z samoobsługowego resetowania haseł jest kampania e-mail z prostymi do zastosowania instrukcjami. [Przygotowaliśmy trzy proste wiadomości e-mail, których możesz użyć jako szablonów w swoim wdrożeniu](https://www.microsoft.com/download/details.aspx?id=56768):

* **Dostępne wkrótce**: szablon wiadomości e-mail do zastosowania w tygodniach lub dniach poprzedzających wdrażanie w celu powiadomienia użytkowników o tym, że mają coś zrobić.
* **Już dostępne**: szablon wiadomości e-mail do zastosowania w dniu uruchomienia programu, aby zachęcić użytkowników do rejestracji i potwierdzenia swoich danych uwierzytelniania. Jeśli użytkownicy zarejestrują się od razu, będą mogli skorzystać z samoobsługowego resetowania haseł, gdy będą tego potrzebować.
* **Przypomnienie o rejestracji**: szablon wiadomości e-mail do zastosowania w ciągu kilku dni lub tygodni po wdrożeniu w celu przypomnienia użytkownikom, aby zarejestrowali się i potwierdzili swoje dane uwierzytelniania.

![Wiadomość e-mail][Email]

## <a name="create-your-own-password-portal"></a>Tworzenie własnego portalu haseł

Wielu klientów decyduje się na hostowanie strony internetowej i utworzenie głównego wpisu w usłudze DNS, takiego jak https://passwords.contoso.com. Wypełniają oni tę stronę linkami do następujących informacji:

* [Portal resetowania haseł usługi Azure AD — https://aka.ms/sspr](https://aka.ms/sspr)
* [Portal rejestracji w celu korzystania z funkcji resetowania haseł usługi Azure AD — https://aka.ms/ssprsetup](https://aka.ms/ssprsetup)
* [Portal zmieniania haseł usługi Azure AD — https://account.activedirectory.windowsazure.com/ChangePassword.aspx](https://account.activedirectory.windowsazure.com/ChangePassword.aspx)
* Inne informacje dotyczące organizacji

W dowolnej komunikacji e-mail i rozsyłanych materiałach marketingowych możesz umieścić firmowe, łatwe do zapamiętania adresy URL, z których mogą skorzystać użytkownicy chcący użyć tych usług. Aby ułatwić Ci pracę, utworzyliśmy także [przykładową stronę resetowania hasła](https://github.com/ajamess/password-reset-page), którą możesz dostosować do potrzeb swojej organizacji.

## <a name="use-enforced-registration"></a>Używanie rejestracji wymuszonej

Jeśli chcesz, aby użytkownicy rejestrowali się w celu zresetowania hasła, możesz wymagać od nich rejestracji podczas logowania się przy użyciu usługi Azure AD. Tę opcję możesz włączyć w okienku **Resetowanie hasła** swojego katalogu, włączając opcję **Czy wymagać od użytkowników rejestrowania się podczas logowania?** na karcie **Rejestracja**.

Administratorzy mogą wymagać od użytkowników ponownej rejestracji po upływie określonego czasu. Mogą oni ustawić dla opcji **Liczba dni, zanim użytkownicy zostaną poproszeni o ponowne potwierdzenie swoich informacji uwierzytelniania** wartość w zakresie od 0 do 730 dni.

Po włączeniu tej opcji logujący się użytkownicy zobaczą komunikat informujący, że administrator wymaga od nich zweryfikowania ich informacji uwierzytelniania.

## <a name="populate-authentication-data"></a>Wypełnianie danych uwierzytelniania

Należy [wypełnić dane uwierzytelniania dla użytkowników](howto-sspr-authenticationdata.md). Dzięki temu użytkownicy nie będą musieli rejestrować się w celu resetowania hasła, aby móc korzystać z funkcji SSPR. O ile użytkownicy dostarczą dane uwierzytelniania spełniające zdefiniowane przez Ciebie zasady resetowania haseł, będą mogli resetować swoje hasła.

## <a name="disable-self-service-password-reset"></a>Wyłączanie samoobsługowego resetowania haseł

Samoobsługowe resetowanie haseł można w łatwy sposób wyłączyć. Otwórz dzierżawę usługi Azure AD, przejdź do obszaru **Resetowanie hasła** > **Właściwości**, a następnie wybierz pozycję **Brak** w obszarze **Funkcja samoobsługowego resetowania hasła jest włączona**.

## <a name="next-steps"></a>Następne kroki

* [Resetowanie lub zmienianie hasła](../active-directory-passwords-update-your-own-password.md)
* [Rejestrowanie na potrzeby samoobsługowego resetowania haseł](../active-directory-passwords-reset-register.md)
* [Czy masz pytanie dotyczące licencjonowania?](concept-sspr-licensing.md)
* [Jakie dane są używane przez funkcję samoobsługowego resetowania haseł i jakie dane powinny zostać wypełnione dla użytkowników?](howto-sspr-authenticationdata.md)
* [Jakie są opcje zasad dla funkcji samoobsługowego resetowania haseł?](concept-sspr-policy.md)
* [Co to jest funkcja zapisywania zwrotnego haseł i dlaczego jest ona tak ważna?](../active-directory-passwords-writeback.md)
* [Jak zgłosić działanie funkcji samoobsługowego resetowania haseł?](howto-sspr-reporting.md)
* [Jakie są dostępne opcje funkcji samoobsługowego resetowania haseł i do czego one służą?](concept-sspr-howitworks.md)
* [Myślę, że coś działa niewłaściwie. Jak rozwiązywać problemy z funkcją samoobsługowego resetowania haseł?](active-directory-passwords-troubleshoot.md)
* [Mam pytanie, na które nie mogę znaleźć odpowiedzi](active-directory-passwords-faq.md)

[Email]: ./media/howto-sspr-deployment/sspr-emailtemplates.png "Dostosuj te szablony wiadomości e-mail do wymagań swojej organizacji"
