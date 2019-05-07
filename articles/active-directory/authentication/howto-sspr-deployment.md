---
title: Podręcznik wdrażania samoobsługowego resetowania haseł — Azure Active Directory
description: Porady pomagające w pomyślnym wdrażaniu samoobsługowego resetowania haseł w usłudze Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9c254ef3a71e95b33df2a779c728d47fff3c3759
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2019
ms.locfileid: "65190371"
---
# <a name="how-to-successfully-roll-out-self-service-password-reset"></a>Jak pomyślnie wdrożyć funkcję samoobsługowego resetowania haseł

Aby zapewnić bezproblemowe wdrożenie funkcji samoobsługowego resetowania haseł (SSPR, self-service password reset) usługi Azure Active Directory (Azure AD), większość klientów wykonuje następujące czynności:

> [!VIDEO https://www.youtube.com/embed/OZn5btP6ZXw]

1. Ukończ pilotażowe wdrożenie się z małego podzbioru organizacji.
   * Informacje na temat sposobu projekt pilotażowy można znaleźć w [samouczka: Kompletny projekt pilotażowy resetowania haseł usługi Azure AD wdrażanie](tutorial-sspr-pilot.md).
1. Wiedzę techniczną.
   * Jak będą one ułatwić użytkownikom?
   * Czy wymusi użytkownikom korzystać z funkcji samoobsługowego resetowania HASEŁ i nie zezwalaj, aby ułatwić użytkownikom?
   * Masz usiłować adresy URL do rejestracji i resetowania?
      * Rejestracja:  https://aka.ms/ssprsetup
      * Resetuj: https://aka.ms/sspr

   > [!WARNING]
   > Użyj pola wyboru "użytkownik musi zmienić hasło przy następnym logowaniu" w narzędziach administracyjnych lokalnej usługi Active Directory użytkownicy usługi Active Directory i komputerów lub Centrum administracyjne usługi Active Directory nie jest obsługiwana. Podczas zmiany hasła w środowisku lokalnym nie należy zaznaczać tej opcji. 

1. Poinformuj użytkowników.
   * Poniższe sekcje tego dokumentu omijają przykładowej komunikacji, portali hasła, wymuszanie rejestracji i wypełnianie danych uwierzytelniania.
   * Grupa ds. produktów usługi Azure Active Directory utworzyła [plan wdrożenia krok po kroku](https://aka.ms/SSPRDeploymentPlan), którego organizacje mogą używać równolegle z dokumentacją z tej witryny, aby analizować własną działalność i planować wdrożenie samoobsługowego resetowania hasła.
1. Włącz samoobsługowe resetowanie haseł dla całej organizacji.
   * Gdy wszystko będzie gotowe, włącz resetowanie haseł dla wszystkich użytkowników, ustawiając przełącznik **Funkcja samoobsługowego resetowania hasła jest włączona** na wartość **Wszystkie**.

## <a name="sample-communication"></a>Przykładowy komunikat

Wielu klientów uważa, że najłatwiejszym sposobem zachęcenia użytkowników do korzystania z samoobsługowego resetowania haseł jest kampania e-mail z prostymi do zastosowania instrukcjami. [Utworzono proste wiadomości e-mail i inne dodatkowe materiały, które możesz można użyć jako szablonów w swoim wdrożeniu](https://www.microsoft.com/download/details.aspx?id=56768):

* **Wkrótce**: Szablon wiadomości e-mail używanego w tygodniach lub dniach poprzedzających, aby powiadomić użytkowników, które są im potrzebne coś zrobić.
* **Dostępne teraz**: Szablon wiadomości e-mail, że używasz dnia program Uruchom, aby zachęcić użytkowników do rejestracji i potwierdzenia swoich danych uwierzytelniania. Jeśli użytkownicy zarejestrują się od razu, będą mogli skorzystać z samoobsługowego resetowania haseł, gdy będą tego potrzebować.
* **Przypomnienie o rejestracji**: Szablon wiadomości e-mail przez kilka dni lub tygodni po wdrożeniu w celu przypomnienia użytkownikom, aby zarejestrować się i potwierdzili swoje dane uwierzytelniania.
* **Plakaty samoobsługowego resetowania HASEŁ**: Plakaty można dostosować i wyświetlać wokół organizacji w dniach i tygodnie, co prowadzi do i od Twojego procesu.
* **Samoobsługowe Resetowanie HASEŁ w identyfikatorach**: Karty tabeli można umieścić w tym pomieszczeniu obiad salach konferencyjnych lub działami zachęcić użytkowników, aby zakończyć rejestrację.
* **Samoobsługowe Resetowanie HASEŁ nalepki**: Nalepki szablony można dostosować i Drukuj, aby umieścić laptopów, monitorów, klawiatur lub telefony komórkowe, do zapamiętania sposobu dostępu z funkcji samoobsługowego resetowania HASEŁ.

![Przykłady e-mail samoobsługowego resetowania HASEŁ dla wdrożenia dla użytkowników][Email]

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

Należy wziąć pod uwagę [wstępne wypełnianie niektóre dane uwierzytelniania dla swoich użytkowników](howto-sspr-authenticationdata.md). Dzięki temu użytkownicy nie będą musieli rejestrować się w celu resetowania hasła, aby móc korzystać z funkcji SSPR. O ile użytkownicy dostarczą dane uwierzytelniania spełniające zdefiniowane przez Ciebie zasady resetowania haseł, będą mogli resetować swoje hasła.

## <a name="disable-self-service-password-reset"></a>Wyłączanie samoobsługowego resetowania haseł

Jeśli organizacja postanawia wyłączanie samoobsługowego resetowania hasła jest prostym procesem. Otwórz dzierżawę usługi Azure AD, przejdź do obszaru **Resetowanie hasła** > **Właściwości**, a następnie wybierz pozycję **Brak** w obszarze **Funkcja samoobsługowego resetowania hasła jest włączona**. Użytkownicy będą nadal utrzymuje metodami uwierzytelniania zarejestrowanych do użycia w przyszłości.

## <a name="next-steps"></a>Kolejne kroki

* [Resetowanie lub zmienianie hasła](../user-help/active-directory-passwords-update-your-own-password.md)
* [Rejestrowanie na potrzeby samoobsługowego resetowania haseł](../user-help/active-directory-passwords-reset-register.md)
* [Włącz konwergentnej rejestracji dla usługi Azure Multi-Factor Authentication i Azure AD samoobsługowego resetowania haseł](concept-registration-mfa-sspr-converged.md)
* [Czy masz pytanie dotyczące licencjonowania?](concept-sspr-licensing.md)
* [Jakie dane są używane przez funkcję samoobsługowego resetowania haseł i jakie dane powinny zostać wypełnione dla użytkowników?](howto-sspr-authenticationdata.md)
* [Jakie są opcje zasad dla funkcji samoobsługowego resetowania haseł?](concept-sspr-policy.md)
* [Co to jest funkcja zapisywania zwrotnego haseł i dlaczego jest ona tak ważna?](howto-sspr-writeback.md)
* [Jak zgłosić działanie funkcji samoobsługowego resetowania haseł?](howto-sspr-reporting.md)
* [Jakie są dostępne opcje funkcji samoobsługowego resetowania haseł i do czego one służą?](concept-sspr-howitworks.md)
* [Myślę, że coś działa niewłaściwie. Jak rozwiązywać problemy z funkcją samoobsługowego resetowania haseł?](active-directory-passwords-troubleshoot.md)
* [Mam pytanie, na które nie mogę znaleźć odpowiedzi](active-directory-passwords-faq.md)

[Email]: ./media/howto-sspr-deployment/sspr-emailtemplates.png "Dostosuj te szablony wiadomości e-mail do wymagań swojej organizacji"
