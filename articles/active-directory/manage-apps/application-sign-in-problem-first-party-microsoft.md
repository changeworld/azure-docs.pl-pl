---
title: Problemy z logowaniem się do aplikacji firmy Microsoft | Dokumenty firmy Microsoft
description: Rozwiązywanie typowych problemów napotkanych podczas logowania się do aplikacji microsoft w usłudze Azure AD (np.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/10/2018
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7ee8802aeb2a760e255ab4f5e99010dfedc45e0d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67108297"
---
# <a name="problems-signing-in-to-a-microsoft-application"></a>Problemy z logowaniem się do aplikacji firmy Microsoft

Aplikacje firmy Microsoft (takie jak Usługa Office 365 Exchange, SharePoint, Yammer itp.) są przypisywane i zarządzane nieco inaczej niż aplikacje SaaS innych firm lub inne aplikacje integrane z usługą Azure AD do logowania jednokrotnego.

Istnieją trzy główne sposoby, aby użytkownik mógł uzyskać dostęp do aplikacji opublikowanej przez firmę Microsoft.

-   W przypadku aplikacji w usłudze Office 365 lub innych płatnych pakietach użytkownicy mają dostęp za pośrednictwem **przypisania licencji** bezpośrednio do konta użytkownika lub za pośrednictwem grupy korzystającej z naszej możliwości przypisywania licencji opartej na grupach.

-   W przypadku aplikacji, które firma Microsoft lub osoba trzecia publikuje swobodnie dla każdego, aby korzystać, użytkownicy mogą uzyskać dostęp za **zgodą użytkownika**. Oznacza to, że logują się do aplikacji za pomocą konta usługi Azure AD Work lub School i umożliwiają jej dostęp do pewnego ograniczonego zestawu danych na swoim koncie.

-   W przypadku aplikacji, które firma Microsoft lub osoba trzecia publikuje swobodnie dla każdego, aby korzystać, użytkownicy mogą również uzyskać dostęp za **zgodą administratora**. Oznacza to, że administrator ustalił, że aplikacja może być używana przez wszystkich w organizacji, więc logują się do aplikacji za pomocą konta administratora globalnego i udzielają dostępu wszystkim osobom w organizacji.

Aby rozwiązać problem, należy rozpocząć od [ogólnych obszarów problemów z dostępem do aplikacji do rozważenia,](#general-problem-areas-with-application-access-to-consider) a następnie przeczytaj przewodnik: Kroki rozwiązywania problemów z dostępem do aplikacji firmy Microsoft, aby uzyskać szczegółowe informacje.

## <a name="general-problem-areas-with-application-access-to-consider"></a>Ogólne obszary problemów z dostępem do aplikacji do rozważenia

Poniżej znajduje się lista ogólnych obszarów problemowych, które można drążyć, jeśli masz pomysł, gdzie rozpocząć, ale zalecamy przeczytanie przewodnika, aby szybko rozpocząć pracę: Przewodnik: Kroki rozwiązywania problemów z dostępem do aplikacji firmy Microsoft.

-   [Problemy z kontem użytkownika](#problems-with-the-users-account)

-   [Problemy z grupami](#problems-with-groups)

-   [Problemy z zasadami dostępu warunkowego](#problems-with-conditional-access-policies)

-   [Problemy z zgodą aplikacji](#problems-with-application-consent)

## <a name="steps-to-troubleshoot-microsoft-application-access"></a>Kroki rozwiązywania problemów z dostępem do aplikacji firmy Microsoft

Poniżej przedstawiono niektóre typowe problemy, na które ludzie napomnieli, gdy ich użytkownicy nie mogą zalogować się do aplikacji firmy Microsoft.

- Kwestie ogólne do sprawdzenia w pierwszej kolejności

  * Upewnij się, że użytkownik loguje się do **prawidłowego adresu URL,** a nie do lokalnego adresu URL aplikacji.

  * Upewnij się, że konto użytkownika nie jest **zablokowane.**

  * Upewnij się, że **konto użytkownika istnieje** w usłudze Azure Active Directory. [Sprawdzanie, czy konto użytkownika istnieje w usłudze Azure Active Directory](#problems-with-the-users-account)

  * Upewnij się, że konto użytkownika jest **włączone** dla [logowania.](#problems-with-the-users-account)

  * Upewnij się, że hasło użytkownika **nie wygasło ani nie zostało zapomniane.** [Resetowanie hasła użytkownika](#reset-a-users-password) lub [włączanie samoobsługowego resetowania hasła](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-getting-started)

  * Upewnij się, że **uwierzytelnianie wieloskładnikowe** nie blokuje dostępu użytkowników. [Sprawdzanie stanu uwierzytelniania wieloskładnikowego użytkownika](#check-a-users-multi-factor-authentication-status) lub [sprawdzanie informacji kontaktowych uwierzytelniania użytkownika](#check-a-users-authentication-contact-info)

  * Upewnij się, że **zasady dostępu warunkowego** lub zasady **ochrony tożsamości** nie blokują dostępu użytkowników. [Sprawdź określone zasady dostępu warunkowego](#problems-with-conditional-access-policies) lub [sprawdź zasady dostępu warunkowego określonej aplikacji](#check-a-specific-applications-conditional-access-policy) lub wyłącz [określoną zasadę dostępu warunkowego](#disable-a-specific-conditional-access-policy)

  * Upewnij się, że **informacje kontaktowe uwierzytelniania** użytkownika są aktualne, aby umożliwić wymuszanie zasad uwierzytelniania wieloskładnikowego lub dostępu warunkowego. [Sprawdzanie stanu uwierzytelniania wieloskładnikowego użytkownika](#check-a-users-multi-factor-authentication-status) lub [sprawdzanie informacji kontaktowych uwierzytelniania użytkownika](#check-a-users-authentication-contact-info)

- W przypadku aplikacji **firmy Microsoft,** **które wymagają licencji** (takiej jak Office365), oto kilka konkretnych problemów, które należy sprawdzić po wykluczeniu powyższych ogólnych problemów:

  * Upewnij się, że użytkownik lub ma **przypisaną licencję.** [Sprawdzanie przypisanych licencji użytkownika](#check-a-users-assigned-licenses) lub [sprawdzanie przypisanych licencji grupy](#check-a-groups-assigned-licenses)

  * Jeśli licencja jest przypisana do grupy **statycznej,** **static group**upewnij się, że **użytkownik jest członkiem** tej grupy. [Sprawdzanie członkostwa w grupie użytkownika](#check-a-users-group-memberships)

  * Jeśli licencja jest przypisana do grupy **dynamicznej,** **dynamic group**upewnij się, że **reguła grupy dynamicznej jest ustawiona poprawnie**. [Sprawdzanie kryteriów członkostwa grupy dynamicznej](#check-a-dynamic-groups-membership-criteria)

  * Jeśli licencja jest przypisana do grupy **dynamicznej,** **dynamic group**upewnij się, że grupa dynamiczna **zakończyła przetwarzanie** swojego członkostwa i że użytkownik **jest członkiem** (może to zająć trochę czasu). [Sprawdzanie członkostwa w grupie użytkownika](#check-a-users-group-memberships)

  *  Po upewnieniu się, że licencja jest **przypisana,** upewnij się, że licencja nie wygasła .

  *  Upewnij się, że licencja jest **dla aplikacji,** do których mają dostęp.

- W przypadku aplikacji **firmy Microsoft,** **które nie wymagają licencji,** sprawdź też kilka innych czynności:

  * Jeśli aplikacja żąda **uprawnień na poziomie użytkownika** (na przykład "Dostęp do skrzynki pocztowej tego użytkownika"), upewnij się, że użytkownik zalogował się do aplikacji i wykonał **operację zgody na poziomie użytkownika,** aby umożliwić aplikacji dostęp do ich danych.

  * Jeśli aplikacja żąda **uprawnień na poziomie administratora** (na przykład "Dostęp do wszystkich skrzynek pocztowych użytkownika"), upewnij się, że administrator globalny wykonał **operację zgody na poziomie administratora w imieniu wszystkich użytkowników** w organizacji.

## <a name="problems-with-the-users-account"></a>Problemy z kontem użytkownika

Dostęp do aplikacji można zablokować z powodu problemu z użytkownikiem przypisanym do aplikacji. Oto kilka sposobów rozwiązywania problemów z użytkownikami i ustawieniami konta:

-   [Sprawdzanie, czy konto użytkownika istnieje w usłudze Azure Active Directory](#check-if-a-user-account-exists-in-azure-active-directory)

-   [Sprawdzanie stanu konta użytkownika](#check-a-users-account-status)

-   [Resetowanie hasła użytkownika](#reset-a-users-password)

-   [Włącz samoobsługowe resetowanie haseł](#enable-self-service-password-reset)

-   [Sprawdzanie stanu uwierzytelniania wieloskładnikowego użytkownika](#check-a-users-multi-factor-authentication-status)

-   [Sprawdzanie informacji kontaktowych uwierzytelniania użytkownika](#check-a-users-authentication-contact-info)

-   [Sprawdzanie członkostwa w grupie użytkownika](#check-a-users-group-memberships)

-   [Sprawdzanie przypisanych licencji użytkownika](#check-a-users-assigned-licenses)

-   [Przypisywanie użytkownikowi licencji](#assign-a-user-a-license)

### <a name="check-if-a-user-account-exists-in-azure-active-directory"></a>Sprawdzanie, czy konto użytkownika istnieje w usłudze Azure Active Directory

Aby sprawdzić, czy konto użytkownika jest obecne, wykonaj następujące kroki:

1.  Otwórz [**witrynę Azure portal**](https://portal.azure.com/) i zaloguj się jako **administrator globalny.**

2.  Otwórz **rozszerzenie usługi Azure Active Directory,** klikając **pozycję Wszystkie usługi** u góry głównego menu nawigacji po lewej stronie.

3.  Wpisz **"Usługa Azure Active Directory"** w polu wyszukiwania filtru i wybierz element **usługi Azure Active Directory.**

4.  kliknij **pozycję Użytkownicy i grupy** w menu nawigacyjnym.

5.  kliknij pozycję **Wszyscy użytkownicy**.

6.  **Wyszukaj** użytkownika, którego jesteś zainteresowany, i **kliknij wiersz,** aby wybrać.

7.  Sprawdź właściwości obiektu użytkownika, aby upewnić się, że wyglądają zgodnie z oczekiwaniami i nie brakuje żadnych danych.

### <a name="check-a-users-account-status"></a>Sprawdzanie stanu konta użytkownika

Aby sprawdzić stan konta użytkownika, wykonaj następujące czynności:

1.  Otwórz [**witrynę Azure portal**](https://portal.azure.com/) i zaloguj się jako **administrator globalny.**

2.  Otwórz **rozszerzenie usługi Azure Active Directory,** klikając **pozycję Wszystkie usługi** u góry głównego menu nawigacji po lewej stronie.

3.  Wpisz **"Usługa Azure Active Directory"** w polu wyszukiwania filtru i wybierz element **usługi Azure Active Directory.**

4.  kliknij **pozycję Użytkownicy i grupy** w menu nawigacyjnym.

5.  kliknij pozycję **Wszyscy użytkownicy**.

6.  **Wyszukaj** użytkownika, którego jesteś zainteresowany, i **kliknij wiersz,** aby wybrać.

7.  kliknij pozycję **Profil**.

8.  W obszarze **Ustawienia** upewnij się, że **logowanie bloku** jest ustawione na **Nie**.

### <a name="reset-a-users-password"></a>Resetowanie hasła użytkownika

Aby zresetować hasło użytkownika, wykonaj następujące czynności:

1.  Otwórz [**witrynę Azure portal**](https://portal.azure.com/) i zaloguj się jako **administrator globalny.**

2.  Otwórz **rozszerzenie usługi Azure Active Directory,** klikając **pozycję Wszystkie usługi** u góry głównego menu nawigacji po lewej stronie.

3.  Wpisz **"Usługa Azure Active Directory"** w polu wyszukiwania filtru i wybierz element **usługi Azure Active Directory.**

4.  kliknij **pozycję Użytkownicy i grupy** w menu nawigacyjnym.

5.  kliknij pozycję **Wszyscy użytkownicy**.

6.  **Wyszukaj** użytkownika, którego jesteś zainteresowany, i **kliknij wiersz,** aby wybrać.

7.  kliknij przycisk **Resetuj hasło** w górnej części okienka użytkownika.

8.  kliknij przycisk **Resetuj hasło** w wyświetlonym okienku **Resetowanie hasła.**

9.  Skopiuj **hasło tymczasowe** lub **wprowadź nowe hasło** dla użytkownika.

10. Przekaż to nowe hasło do użytkownika, muszą zmienić to hasło podczas następnego logowania do usługi Azure Active Directory.

### <a name="enable-self-service-password-reset"></a>Włączanie samoobsługowego resetowania hasła

Aby włączyć samoobsługowe resetowanie hasła, wykonaj poniższe czynności związane z wdrażaniem:

-   [Umożliwianie użytkownikom resetowania haseł usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-getting-started)

-   [Umożliwianie użytkownikom resetowania lub zmieniania haseł lokalnych usługi Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-getting-started)

### <a name="check-a-users-multi-factor-authentication-status"></a>Sprawdzanie stanu uwierzytelniania wieloskładnikowego użytkownika

Aby sprawdzić stan uwierzytelniania wieloskładnikowego użytkownika, wykonaj następujące czynności:

1. Otwórz [**witrynę Azure portal**](https://portal.azure.com/) i zaloguj się jako **administrator globalny.**

2. Otwórz **rozszerzenie usługi Azure Active Directory,** klikając **pozycję Wszystkie usługi** u góry głównego menu nawigacji po lewej stronie.

3. Wpisz **"Usługa Azure Active Directory"** w polu wyszukiwania filtru i wybierz element **usługi Azure Active Directory.**

4. kliknij **pozycję Użytkownicy i grupy** w menu nawigacyjnym.

5. kliknij pozycję **Wszyscy użytkownicy**.

6. kliknij przycisk **Uwierzytelnianie wieloskładnikowe** w górnej części okienka.

7. Po załadowaniu **portalu administracji uwierzytelnianiem wieloskładnikowym** upewnij się, że znajdujesz się na karcie **Użytkownicy.**

8. Znajdź użytkownika na liście użytkowników, wyszukując, filtrując lub sortując.

9. Wybierz użytkownika z listy użytkowników i **Włącz**, **Wyłącz**lub **Wymuń** uwierzytelnianie wieloskładnikowe zgodnie z potrzebami.

   * **Uwaga:** Jeśli użytkownik jest w stanie **wymuszone,** można ustawić je **na Wyłączone** tymczasowo, aby umożliwić mu powrót do konta. Po powrocie można zmienić ich stan na **Włączone** ponownie, aby wymagać od nich ponownego zarejestrowania swoich informacji kontaktowych podczas następnego logowania. Alternatywnie można wykonać kroki opisane w [sprawdź informacje kontaktowe uwierzytelniania użytkownika,](#check-a-users-authentication-contact-info) aby zweryfikować lub ustawić te dane dla nich.

### <a name="check-a-users-authentication-contact-info"></a>Sprawdzanie informacji kontaktowych uwierzytelniania użytkownika

Aby sprawdzić informacje kontaktowe uwierzytelniania użytkownika używane do uwierzytelniania wieloskładnikowego, dostępu warunkowego, ochrony tożsamości i resetowania hasła, wykonaj następujące kroki:

1.  Otwórz [**witrynę Azure portal**](https://portal.azure.com/) i zaloguj się jako **administrator globalny.**

2.  Otwórz **rozszerzenie usługi Azure Active Directory,** klikając **pozycję Wszystkie usługi** u góry głównego menu nawigacji po lewej stronie.

3.  Wpisz **"Usługa Azure Active Directory"** w polu wyszukiwania filtru i wybierz element **usługi Azure Active Directory.**

4.  kliknij **pozycję Użytkownicy i grupy** w menu nawigacyjnym.

5.  kliknij pozycję **Wszyscy użytkownicy**.

6.  **Wyszukaj** użytkownika, którego jesteś zainteresowany, i **kliknij wiersz,** aby wybrać.

7.  kliknij pozycję **Profil**.

8.  Przewiń w dół do **informacji kontaktowych uwierzytelniania**.

9.  **Przejrzyj** dane zarejestrowane dla użytkownika i w razie potrzeby zaktualizuj.

### <a name="check-a-users-group-memberships"></a>Sprawdzanie członkostwa w grupie użytkownika

Aby sprawdzić członkostwo użytkownika w grupach, wykonaj następujące czynności:

1.  Otwórz [**witrynę Azure portal**](https://portal.azure.com/) i zaloguj się jako **administrator globalny.**

2.  Otwórz **rozszerzenie usługi Azure Active Directory,** klikając **pozycję Wszystkie usługi** u góry głównego menu nawigacji po lewej stronie.

3.  Wpisz **"Usługa Azure Active Directory"** w polu wyszukiwania filtru i wybierz element **usługi Azure Active Directory.**

4.  kliknij **pozycję Użytkownicy i grupy** w menu nawigacyjnym.

5.  kliknij pozycję **Wszyscy użytkownicy**.

6.  **Wyszukaj** użytkownika, którego jesteś zainteresowany, i **kliknij wiersz,** aby wybrać.

7.  kliknij pozycję **Grupy,** aby zobaczyć, do których grup jest członkiem.

### <a name="check-a-users-assigned-licenses"></a>Sprawdzanie przypisanych licencji użytkownika

Aby sprawdzić przypisane licencje użytkownika, wykonaj następujące czynności:

1.  Otwórz [**witrynę Azure portal**](https://portal.azure.com/) i zaloguj się jako **administrator globalny.**

2.  Otwórz **rozszerzenie usługi Azure Active Directory,** klikając **pozycję Wszystkie usługi** u góry głównego menu nawigacji po lewej stronie.

3.  Wpisz **"Usługa Azure Active Directory"** w polu wyszukiwania filtru i wybierz element **usługi Azure Active Directory.**

4.  kliknij **pozycję Użytkownicy i grupy** w menu nawigacyjnym.

5.  kliknij pozycję **Wszyscy użytkownicy**.

6.  **Wyszukaj** użytkownika, którego jesteś zainteresowany, i **kliknij wiersz,** aby wybrać.

7.  kliknij **pozycję Licencje,** aby zobaczyć, które licencje użytkownik aktualnie ma przypisane.

### <a name="assign-a-user-a-license"></a>Przypisywanie użytkownikowi licencji 

Aby przypisać licencję do użytkownika, wykonaj następujące czynności:

1.  Otwórz [**witrynę Azure portal**](https://portal.azure.com/) i zaloguj się jako **administrator globalny.**

2.  Otwórz **rozszerzenie usługi Azure Active Directory,** klikając **pozycję Wszystkie usługi** u góry głównego menu nawigacji po lewej stronie.

3.  Wpisz **"Usługa Azure Active Directory"** w polu wyszukiwania filtru i wybierz element **usługi Azure Active Directory.**

4.  kliknij **pozycję Użytkownicy i grupy** w menu nawigacyjnym.

5.  kliknij pozycję **Wszyscy użytkownicy**.

6.  **Wyszukaj** użytkownika, którego jesteś zainteresowany, i **kliknij wiersz,** aby wybrać.

7.  kliknij **pozycję Licencje,** aby zobaczyć, które licencje użytkownik aktualnie ma przypisane.

8.  kliknij przycisk **Przypisz.**

9.  Wybierz **jeden lub więcej produktów** z listy dostępnych produktów.

10. **Opcjonalnie** kliknij element **opcji przydziału,** aby dokładnie przypisać produkty. Po zakończeniu kliknij przycisk **Ok.**

11. Kliknij przycisk **Przypisz,** aby przypisać te licencje do tego użytkownika.

## <a name="problems-with-groups"></a>Problemy z grupami

Dostęp do aplikacji można zablokować z powodu problemu z grupą przypisaną do aplikacji. Oto kilka sposobów rozwiązywania problemów z grupami i członkostwem w grupach:

-   [Sprawdzanie członkostwa w grupie](#check-a-groups-membership)

-   [Sprawdzanie kryteriów członkostwa grupy dynamicznej](#check-a-dynamic-groups-membership-criteria)

-   [Sprawdzanie przypisanych licencji grupy](#check-a-groups-assigned-licenses)

-   [Ponowne przetwarzanie licencji grupy](#reprocess-a-groups-licenses)

-   [Przypisywanie grupie licencji](#assign-a-group-a-license)

### <a name="check-a-groups-membership"></a>Sprawdzanie członkostwa w grupie

Aby sprawdzić przynależność do grupy, wykonaj następujące czynności:

1.  Otwórz [**witrynę Azure portal**](https://portal.azure.com/) i zaloguj się jako **administrator globalny.**

2.  Otwórz **rozszerzenie usługi Azure Active Directory,** klikając **pozycję Wszystkie usługi** u góry głównego menu nawigacji po lewej stronie.

3.  Wpisz **"Usługa Azure Active Directory"** w polu wyszukiwania filtru i wybierz element **usługi Azure Active Directory.**

4.  kliknij **pozycję Użytkownicy i grupy** w menu nawigacyjnym.

5.  kliknij pozycję **Wszystkie grupy**.

6.  **Wyszukaj** interesującą Cię grupę i **kliknij wiersz,** aby ją zaznaczyć.

7.  kliknij pozycję **Członkowie,** aby przejrzeć listę użytkowników przypisanych do tej grupy.

### <a name="check-a-dynamic-groups-membership-criteria"></a>Sprawdzanie kryteriów członkostwa grupy dynamicznej 

Aby sprawdzić kryteria członkostwa grupy dynamicznej, wykonaj następujące czynności:

1.  Otwórz [**witrynę Azure portal**](https://portal.azure.com/) i zaloguj się jako **administrator globalny.**

2.  Otwórz **rozszerzenie usługi Azure Active Directory,** klikając **pozycję Wszystkie usługi** u góry głównego menu nawigacji po lewej stronie.

3.  Wpisz **"Usługa Azure Active Directory"** w polu wyszukiwania filtru i wybierz element **usługi Azure Active Directory.**

4.  kliknij **pozycję Użytkownicy i grupy** w menu nawigacyjnym.

5.  kliknij pozycję **Wszystkie grupy**.

6.  **Wyszukaj** interesującą Cię grupę i **kliknij wiersz,** aby ją zaznaczyć.

7.  kliknij **pozycję Dynamiczne reguły członkostwa.**

8.  Przejrzyj **prostą** lub **zaawansowaną** regułę zdefiniowaną dla tej grupy i upewnij się, że użytkownik, którego chcesz być członkiem tej grupy, spełnia te kryteria.

### <a name="check-a-groups-assigned-licenses"></a>Sprawdzanie przypisanych licencji grupy

Aby sprawdzić przypisane licencje grupy, wykonaj następujące czynności:

1.  Otwórz [**witrynę Azure portal**](https://portal.azure.com/) i zaloguj się jako **administrator globalny.**

2.  Otwórz **rozszerzenie usługi Azure Active Directory,** klikając **pozycję Wszystkie usługi** u góry głównego menu nawigacji po lewej stronie.

3.  Wpisz **"Usługa Azure Active Directory"** w polu wyszukiwania filtru i wybierz element **usługi Azure Active Directory.**

4.  kliknij **pozycję Użytkownicy i grupy** w menu nawigacyjnym.

5.  kliknij pozycję **Wszystkie grupy**.

6.  **Wyszukaj** interesującą Cię grupę i **kliknij wiersz,** aby ją zaznaczyć.

7.  kliknij **pozycję Licencje,** aby zobaczyć, które licencje grupa jest aktualnie przypisana.

### <a name="reprocess-a-groups-licenses"></a>Ponowne przetwarzanie licencji grupy

Aby ponownie przetworzyć przypisane licencje grupy, wykonaj następujące czynności:

1. Otwórz [**witrynę Azure portal**](https://portal.azure.com/) i zaloguj się jako **administrator globalny.**

2. Otwórz **rozszerzenie usługi Azure Active Directory,** klikając **pozycję Wszystkie usługi** u góry głównego menu nawigacji po lewej stronie.

3. Wpisz **"Usługa Azure Active Directory"** w polu wyszukiwania filtru i wybierz element **usługi Azure Active Directory.**

4. kliknij **pozycję Użytkownicy i grupy** w menu nawigacyjnym.

5. kliknij pozycję **Wszystkie grupy**.

6. **Wyszukaj** interesującą Cię grupę i **kliknij wiersz,** aby ją zaznaczyć.

7. kliknij **pozycję Licencje,** aby zobaczyć, które licencje grupa jest aktualnie przypisana.

8. Kliknij przycisk **Ponownie przetworzyć,** aby upewnić się, że licencje przypisane do członków tej grupy są aktualne. Może to zająć dużo czasu, w zależności od wielkości i złożoności grupy.

   >[!NOTE]
   >Aby to zrobić szybciej, należy rozważyć tymczasowe przypisanie licencji do użytkownika bezpośrednio. [Przypisywanie użytkownikowi licencji](#problems-with-application-consent).
   >
   >

### <a name="assign-a-group-a-license"></a>Przypisywanie grupie licencji

Aby przypisać licencję do grupy, wykonaj następujące czynności:

1. Otwórz [**witrynę Azure portal**](https://portal.azure.com/) i zaloguj się jako **administrator globalny.**

2. Otwórz **rozszerzenie usługi Azure Active Directory,** klikając **pozycję Wszystkie usługi** u góry głównego menu nawigacji po lewej stronie.

3. Wpisz **"Usługa Azure Active Directory"** w polu wyszukiwania filtru i wybierz element **usługi Azure Active Directory.**

4. kliknij **pozycję Użytkownicy i grupy** w menu nawigacyjnym.

5. kliknij pozycję **Wszystkie grupy**.

6. **Wyszukaj** interesującą Cię grupę i **kliknij wiersz,** aby ją zaznaczyć.

7. kliknij **pozycję Licencje,** aby zobaczyć, które licencje grupa jest aktualnie przypisana.

8. kliknij przycisk **Przypisz.**

9. Wybierz **jeden lub więcej produktów** z listy dostępnych produktów.

10. **Opcjonalnie** kliknij element **opcji przydziału,** aby dokładnie przypisać produkty. Po zakończeniu kliknij przycisk **Ok.**

11. Kliknij przycisk **Przypisz,** aby przypisać te licencje do tej grupy. Może to zająć dużo czasu, w zależności od wielkości i złożoności grupy.

    >[!NOTE]
    >Aby to zrobić szybciej, należy rozważyć tymczasowe przypisanie licencji do użytkownika bezpośrednio. [Przypisywanie użytkownikowi licencji](#problems-with-application-consent).
    > 
    >

## <a name="problems-with-conditional-access-policies"></a>Problemy z zasadami dostępu warunkowego

### <a name="check-a-specific-conditional-access-policy"></a>Sprawdź określone zasady dostępu warunkowego

Aby sprawdzić lub zweryfikować jedną zasadę dostępu warunkowego:

1. Otwórz [**witrynę Azure portal**](https://portal.azure.com/) i zaloguj się jako **administrator globalny.**

2. Otwórz **rozszerzenie usługi Azure Active Directory,** klikając **pozycję Wszystkie usługi** u góry głównego menu nawigacji po lewej stronie.

3. Wpisz **"Usługa Azure Active Directory"** w polu wyszukiwania filtru i wybierz element **usługi Azure Active Directory.**

4. kliknij polecenie **Aplikacje przedsiębiorstwa** w menu nawigacyjnym.

5. kliknij element nawigacji **Dostęp warunkowy.**

6. kliknij zasady, które Cię interesują.

7. Sprawdź, czy nie ma żadnych określonych warunków, przypisań ani innych ustawień, które mogą blokować dostęp użytkowników.

   >[!NOTE]
   >Możesz tymczasowo wyłączyć te zasady, aby upewnić się, że nie ma to wpływu na logowania. Aby to zrobić, ustaw przełącznik **Włącz zasady** na **Nie** i kliknij przycisk **Zapisz.**
   >
   >

### <a name="check-a-specific-applications-conditional-access-policy"></a>Sprawdzanie zasad dostępu warunkowego określonej aplikacji

Aby sprawdzić lub zweryfikować aktualnie skonfigurowane zasady dostępu warunkowego jednej aplikacji:

1.  Otwórz [**witrynę Azure portal**](https://portal.azure.com/) i zaloguj się jako **administrator globalny.**

2.  Otwórz **rozszerzenie usługi Azure Active Directory,** klikając **pozycję Wszystkie usługi** u góry głównego menu nawigacji po lewej stronie.

3.  Wpisz **"Usługa Azure Active Directory"** w polu wyszukiwania filtru i wybierz element **usługi Azure Active Directory.**

4.  kliknij polecenie **Aplikacje przedsiębiorstwa** w menu nawigacyjnym.

5.  kliknij pozycję **Wszystkie aplikacje**.

6.  Wyszukaj interesującą Cię aplikację lub użytkownik próbuje zalogować się według nazwy wyświetlanej aplikacji lub identyfikatora aplikacji.

     >[!NOTE]
     >Jeśli nie widzisz szukanej aplikacji, kliknij przycisk **Filtruj** i rozwiń zakres listy do **wszystkich aplikacji**. Jeśli chcesz wyświetlić więcej kolumn, kliknij przycisk **Kolumny,** aby dodać dodatkowe szczegóły dla aplikacji.
     >
     >

7.  kliknij element nawigacji **Dostęp warunkowy.**

8.  kliknij zasady, które Cię interesują.

9.  Sprawdź, czy nie ma żadnych określonych warunków, przypisań ani innych ustawień, które mogą blokować dostęp użytkowników.

     >[!NOTE]
     >Możesz tymczasowo wyłączyć te zasady, aby upewnić się, że nie ma to wpływu na logowania. Aby to zrobić, ustaw przełącznik **Włącz zasady** na **Nie** i kliknij przycisk **Zapisz.**
     >
     >

### <a name="disable-a-specific-conditional-access-policy"></a>Wyłączanie określonych zasad dostępu warunkowego

Aby sprawdzić lub zweryfikować jedną zasadę dostępu warunkowego:

1.  Otwórz [**witrynę Azure portal**](https://portal.azure.com/) i zaloguj się jako **administrator globalny.**

2.  Otwórz **rozszerzenie usługi Azure Active Directory,** klikając **pozycję Wszystkie usługi** u góry głównego menu nawigacji po lewej stronie.

3.  Wpisz **"Usługa Azure Active Directory"** w polu wyszukiwania filtru i wybierz element **usługi Azure Active Directory.**

4.  kliknij polecenie **Aplikacje przedsiębiorstwa** w menu nawigacyjnym.

5.  kliknij element nawigacji **Dostęp warunkowy.**

6.  kliknij zasady, które Cię interesują.

7.  Wyłącz zasady, ustawiając przełącznik **Włącz zasady** na **Nie** i kliknij przycisk **Zapisz.**

## <a name="problems-with-application-consent"></a>Problemy z zgodą aplikacji

Dostęp do aplikacji można zablokować, ponieważ nie wystąpiła operacja zgody na odpowiednie uprawnienia. Oto kilka sposobów rozwiązywania problemów z zgodą aplikacji i rozwiązywania ich:

-   [Wykonywanie operacji zgody na poziomie użytkownika](#perform-a-user-level-consent-operation)

-   [Wykonywanie operacji zgody na poziomie administratora dla dowolnej aplikacji](#perform-administrator-level-consent-operation-for-any-application)

-   [Wykonywanie zgody na poziomie administratora dla aplikacji z jedną dzierżawą](#perform-administrator-level-consent-for-a-single-tenant-application)

-   [Wykonywanie zgody na poziomie administratora dla aplikacji wielodostępnych](#perform-administrator-level-consent-for-a-multi-tenant-application)

### <a name="perform-a-user-level-consent-operation"></a>Wykonywanie operacji zgody na poziomie użytkownika

-   W przypadku dowolnej aplikacji obsługującej połączenie z funkcją Open ID, która żąda uprawnień, przejście do ekranu logowania aplikacji umożliwia wykonanie zgody na poziomie użytkownika aplikacji dla zalogowanego użytkownika.

-   Jeśli chcesz to zrobić programowo, zobacz [Żądanie zgody poszczególnych użytkowników](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes#requesting-individual-user-consent).

### <a name="perform-administrator-level-consent-operation-for-any-application"></a>Wykonywanie operacji zgody na poziomie administratora dla dowolnej aplikacji

-   W przypadku **tylko aplikacji opracowanych przy użyciu modelu aplikacji V1**można wymusić na tym poziomie administratora zgodę, dodając "**?prompt=admin\_consent**" na końcu adresu URL logowania aplikacji.

-   Dla **każdej aplikacji opracowanej przy użyciu modelu aplikacji V2,** można wymusić tę zgodę na poziomie administratora, wykonując instrukcje w sekcji **Żądanie uprawnień z** sekcji administrator katalogu przy użyciu punktu [końcowego zgody administratora](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes#using-the-admin-consent-endpoint).

### <a name="perform-administrator-level-consent-for-a-single-tenant-application"></a>Wykonywanie zgody na poziomie administratora dla aplikacji z jedną dzierżawą

-   W przypadku **aplikacji z jedną dzierżawą,** które żądają uprawnień (takich jak te, które tworzysz lub jesteś właścicielem w organizacji), można wykonać operację zgody na **poziomie administracyjnym** w imieniu wszystkich użytkowników, logując się jako administrator globalny i klikając przycisk **Udziel uprawnień** u góry **rejestru aplikacji —&gt; Wszystkie aplikacje —&gt; Wybierz aplikację —&gt; Wymagane uprawnienia.**

-   Dla **każdej aplikacji opracowanej przy użyciu modelu aplikacji V1 lub V2,** można wymusić tę zgodę na poziomie administratora, wykonując instrukcje w sekcji **Żądaj uprawnień z** sekcji administrator katalogu przy użyciu punktu [końcowego zgody administratora](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes#using-the-admin-consent-endpoint).

### <a name="perform-administrator-level-consent-for-a-multi-tenant-application"></a>Wykonywanie zgody na poziomie administratora dla aplikacji wielodostępnych

-   W przypadku **aplikacji z wieloma dzierżawami,** które żądają uprawnień (takich jak aplikacja innej firmy lub firma Microsoft), można wykonać operację zgody na **poziomie administracyjnym.** Zaloguj się jako administrator globalny i kliknij przycisk **Udziel uprawnień** w obszarze **Aplikacje dla przedsiębiorstw —&gt; Wszystkie aplikacje —&gt; Wybierz aplikację —&gt; Uprawnienia** (dostępne wkrótce).

-   Można również wymusić tę zgodę na poziomie administratora, postępując zgodnie z instrukcjami w sekcji **Żądanie uprawnień od administratora katalogu** w sekcji Korzystanie z [punktu końcowego zgody administratora](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes#using-the-admin-consent-endpoint).

## <a name="next-steps"></a>Następne kroki
[Korzystanie z punktu końcowego zgody administratora](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes#using-the-admin-consent-endpoint)

