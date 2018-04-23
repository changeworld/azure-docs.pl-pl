---
title: Przewodnik Szybki start dotyczący samoobsługowego resetowania haseł — Azure Active Directory
description: Szybkie wdrażanie samoobsługowego resetowania haseł w usłudze Azure AD
services: active-directory
keywords: ''
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry
ms.assetid: bde8799f-0b42-446a-ad95-7ebb374c3bec
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/11/2018
ms.author: joflore
ms.custom: it-pro;seohack1
ms.openlocfilehash: 6add6e71c9acf6d63858ea921ec85924b4e6c60f
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2018
---
# <a name="azure-ad-self-service-password-reset-rapid-deployment"></a>Szybkie wdrażanie samoobsługowego resetowania haseł w usłudze Azure AD

> [!IMPORTANT]
> **Jesteś tutaj, ponieważ masz problemy z logowaniem?** Jeśli tak, zobacz [Nie pamiętam swojego hasła do usługi Azure AD](../active-directory-passwords-update-your-own-password.md).

Funkcja samoobsługowego resetowania haseł pozwala administratorom IT w prosty sposób dać użytkownikom możliwość resetowania lub odblokowywania ich haseł lub kont. System obejmuje szczegółowe raporty pozwalające śledzić, kiedy użytkownicy korzystają z systemu, oraz powiadomienia ostrzegające o jego nieprawidłowym użyciu lub nadużyciach związanych z zabezpieczeniami.

W tym przewodniku założono, że masz już działającą dzierżawę usługi Azure Active Directory (Azure AD) w wersji próbnej lub licencjonowanej. Jeśli potrzebujesz pomocy przy konfigurowaniu usługi Azure AD, zobacz [Wprowadzenie do usługi Azure AD](../get-started-azure-ad.md).

## <a name="enable-sspr-for-your-azure-ad-tenant"></a>Włączanie funkcji samoobsługowego resetowania haseł dla dzierżawy usługi Azure AD

1. W istniejącej dzierżawie usługi Azure AD w witrynie **Azure Portal** w obszarze **Azure Active Directory** wybierz pozycję **Resetowanie hasła**.

2. Na stronie **Właściwości** w ramach opcji **Funkcja samoobsługowego resetowania hasła jest włączona** wybierz jedną z następujących opcji:
   * **Brak**: nikt nie może korzystać z funkcji samoobsługowego resetowania haseł.
   * **Wybrano**: z funkcji samoobsługowego resetowania haseł mogą korzystać tylko członkowie określonej, wybranej przez Ciebie grupy usługi Azure AD. Zaleca się zdefiniowanie grupy użytkowników i korzystanie z tego ustawienia podczas wdrażania tej funkcji w celu weryfikacji koncepcji.
   * **Wszyscy**: z funkcji samoobsługowego resetowania haseł mogą korzystać wszyscy użytkownicy z kontami w Twojej dzierżawie usługi Azure AD. Zaleca się użycie tej opcji po osiągnięciu gotowości do wdrożenia tej funkcji w całej dzierżawie po zakończeniu etapu weryfikacji koncepcji.

   > [!IMPORTANT]
   > W przypadku kont administratorów platformy Azure resetowanie haseł będzie zawsze możliwe niezależnie od ustawień tej opcji. 

3. Na stronie **Metody uwierzytelniania** wybierz następujące opcje:
   * **Liczba metod wymaganych do zresetowania**: obsługiwana jest jedna lub dwie metody.
   * **Metody dostępne dla użytkowników**: potrzebna jest co najmniej jedna metoda, ale zawsze dobrze jest mieć wybór.
      * **Wiadomość e-mail**: wiadomość e-mail z kodem jest wysyłana na adres e-mail uwierzytelniania skonfigurowany przez użytkownika.
      * **Telefon komórkowy**: umożliwia odebranie połączenia lub wiadomości SMS z kodem pod numerem telefonu komórkowego skonfigurowanym przez użytkownika.
      * **Telefon biurowy**: na numer telefonu w biurze skonfigurowany przez użytkownika wykonywane jest połączenie w celu przekazania kodu.
      * Pozycja **Pytania zabezpieczające** wymaga wybrania następujących opcji:
         * **Liczba pytań wymaganych do zarejestrowania się**: jest to minimum potrzebne do pomyślnej rejestracji. Użytkownik może odpowiedzieć na więcej pytań, aby utworzyć pulę pytań do wyboru. Tę opcję można ustawić na wartość z zakresu od trzech do pięciu pytań i musi ona być co najmniej równa liczbie pytań wymaganych do zresetowania hasła użytkownika. Użytkownik może dodać niestandardowe pytania, jeśli podczas wyboru pytań zabezpieczających zostanie wybrany przycisk**Niestandardowe**.
         * **Liczba pytań wymaganych do zresetowania**: tę opcję można ustawić na wartość z zakresu od trzech do pięciu pytań, na które musi zostać udzielona poprawna odpowiedź, aby użytkownik mógł zresetować lub odblokować hasło.
            
    ![Uwierzytelnianie][Authentication]

4. Zalecane: w obszarze **Dostosowanie** można zmienić link **Skontaktuj się z administratorem**w taki sposób, aby wskazywał na zdefiniowaną przez Ciebie stronę lub adres e-mail. Zaleca się ustawienie tego linku na adres e-mail lub witrynę internetową, z których użytkownicy korzystają w celu zadawania pytań dotyczących pomocy technicznej.

5. Opcjonalnie: na stronie **Rejestracja** dostępne są następujące opcje dla administratorów:
   * Wymaganie od użytkowników rejestrowania się podczas logowania.
   * Ustawienie liczby dni, zanim użytkownicy zostaną poproszeni o ponowne potwierdzenie swoich informacji uwierzytelniania.

6. Opcjonalnie: na stronie **Powiadomienia** dostępne są następujące opcje dla administratorów:
   * Powiadamianie użytkowników o resetowaniu hasła.
   * Powiadamianie wszystkich administratorów, gdy inni administratorzy zresetują swoje hasło.

W tym momencie skonfigurowano samoobsługowe resetowanie haseł dla używanej dzierżawy usługi Azure AD. Użytkownicy mogą teraz używać instrukcji opisanych w artykułach [Rejestrowanie na potrzeby samoobsługowego resetowania hasła](../active-directory-passwords-reset-register.md) i [Resetowanie lub zmienianie hasła](../active-directory-passwords-update-your-own-password.md) do aktualizowania swoich haseł bez interwencji administratora. Możesz się zatrzymać w tym miejscu, jeśli praca odbywa się tylko w chmurze. Możesz również przejść do następnej sekcji, aby skonfigurować synchronizację haseł na lokalną domenę usługi Active Directory.

> [!TIP]
> Przetestuj samoobsługowe resetowanie haseł na koncie użytkownika, a nie koncie administratora, ponieważ firma Microsoft narzuca silne wymagania w zakresie uwierzytelniania dla kont administratorów platformy Azure. Aby uzyskać więcej informacji na temat zasad haseł administratorów, zobacz nasz [artykuł dotyczący zasad haseł](concept-sspr-policy.md#administrator-password-policy-differences).

## <a name="configure-synchronization-to-an-existing-identity-source"></a>Konfigurowanie synchronizacji z istniejącym źródłem tożsamości

Aby włączyć synchronizację lokalnych tożsamości z usługą Azure AD, musisz zainstalować i skonfigurować aplikację [Azure AD Connect](./../connect/active-directory-aadconnect.md) na serwerze w swojej organizacji. Ta aplikacja zajmuje się synchronizowaniem użytkowników i grup w istniejącym źródle tożsamości z dzierżawą usługi Azure AD. Aby uzyskać więcej informacji, zobacz:

* [Uaktualnienie z programu DirSync lub Azure AD Sync do programu Azure AD Connect](./../connect/active-directory-aadconnect-dirsync-deprecated.md)
* [Rozpoczynanie pracy z programem Azure AD Connect przy użyciu ustawień ekspresowych](./../connect/active-directory-aadconnect-get-started-express.md)
* [Konfigurowanie zapisywania zwrotnego haseł](howto-sspr-writeback.md#configure-password-writeback) w celu zapisywania haseł z usługi Azure AD z powrotem w katalogu lokalnym

### <a name="on-premises-policy-change"></a>Zmienianie zasad lokalnych

Jeśli synchronizujesz użytkowników z lokalnej domeny usługi Active Directory i chcesz dać im możliwość natychmiastowego resetowania swoich haseł, wprowadź następującą zmianę w lokalnych zasadach haseł:

1. Przejdź do obszaru **Konfiguracja komputera** > **Zasady** > **Ustawienia systemu Windows** > **Ustawienia zabezpieczeń** > **Zasady konta** > **Zasady haseł**.

2. Ustaw **Minimalny okres ważności hasła** na wartość **0 dni**.

To ustawienie zabezpieczeń określa czas (w dniach), przez jaki hasło musi być używane, zanim użytkownik będzie mógł je zmienić. Określenie dla ustawienia minimalnego użycia wartości **0 dni** powoduje, że użytkownicy mogą korzystać z funkcji samoobsługowego resetowania haseł, jeśli ich hasła zostaną zmienione przez zespoły pomocy technicznej.

![Zasady][Policy]

## <a name="disable-self-service-password-reset"></a>Wyłączanie samoobsługowego resetowania haseł

Samoobsługowe resetowanie haseł można w łatwy sposób wyłączyć. Otwórz dzierżawę usługi Azure AD, przejdź do obszaru **Resetowanie hasła** > **Właściwości**, a następnie wybierz pozycję **Brak** w obszarze **Funkcja samoobsługowego resetowania hasła jest włączona**.

### <a name="learn-more"></a>Dowiedz się więcej
Poniższe artykuły zawierają dodatkowe informacje dotyczące resetowania haseł za pomocą usługi Azure AD:

* [Jak wykonać pomyślne wdrożenie funkcji samoobsługowego resetowania haseł?](howto-sspr-deployment.md)
* [Resetowanie lub zmienianie hasła](../active-directory-passwords-update-your-own-password.md)
* [Rejestrowanie na potrzeby samoobsługowego resetowania haseł](../active-directory-passwords-reset-register.md)
* [Czy masz pytanie dotyczące licencjonowania?](concept-sspr-licensing.md)
* [Jakie dane są używane przez funkcję samoobsługowego resetowania haseł i jakie dane powinny zostać wypełnione dla użytkowników?](howto-sspr-authenticationdata.md)
* [Jakie metody uwierzytelniania są dostępne dla użytkowników?](concept-sspr-howitworks.md#authentication-methods)
* [Jakie są opcje zasad dla funkcji samoobsługowego resetowania haseł?](concept-sspr-policy.md)
* [Co to jest funkcja zapisywania zwrotnego haseł i dlaczego jest ona tak ważna?](howto-sspr-writeback.md)
* [Jak zgłosić działanie funkcji samoobsługowego resetowania haseł?](howto-sspr-reporting.md)
* [Jakie są dostępne opcje funkcji samoobsługowego resetowania haseł i do czego one służą?](concept-sspr-howitworks.md)
* [Myślę, że coś działa niewłaściwie. Jak rozwiązywać problemy z funkcją samoobsługowego resetowania haseł?](active-directory-passwords-troubleshoot.md)
* [Mam pytanie, na które nie mogę znaleźć odpowiedzi](active-directory-passwords-faq.md)

## <a name="next-steps"></a>Następne kroki

Po zapoznaniu się z tym przewodnikiem szybkiego startu wiesz już, jak skonfigurować samoobsługowe resetowanie haseł dla swoich użytkowników. Aby wykonać te kroki, przejdź do witryny Azure Portal:

> [!div class="nextstepaction"]
> [Włącz samoobsługowe resetowanie haseł](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/PasswordReset)

[Authentication]: ./media/quickstart-sspr/sspr-authentication-methods.png "Dostępne metody uwierzytelniania w usłudze Azure AD i wymagane ilości"
[Policy]: ./media/quickstart-sspr/password-policy.png "Lokalne zasady grupy haseł ustawione na wartość 0 dni"

