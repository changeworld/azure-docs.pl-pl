---
title: Problemy z logowaniem do aplikacji firmy Microsoft | Dokumentacja firmy Microsoft
description: Rozwiązywanie typowych problemów z zmierzyła się z zespołem podczas logowania się do firmy Microsoft Applications za pomocą usługi Azure AD (takich jak Office 365)
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/10/2018
ms.author: celested
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 256ca5c2f26a6bac6bdfd09e4dd6294ec5a569ca
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60292201"
---
# <a name="problems-signing-in-to-a-microsoft-application"></a>Problemy z logowaniem do aplikacji firmy Microsoft

Microsoft Applications (np. Office 365 Exchange, SharePoint, Yammer itp.) są przypisane i nieco inaczej niż 3 aplikacji SaaS innych firm lub innych aplikacji, którą możesz zintegrować z usługą Azure AD na potrzeby logowania jednokrotnego na zarządzane.

Istnieją trzy sposoby, czy użytkownik może uzyskać dostęp do aplikacji opublikowane przez firmę Microsoft.

-   W przypadku aplikacji w usłudze Office 365 lub innych mechanizmów płatnych użytkownicy uzyskują dostęp za pośrednictwem **przypisania licencji** albo bezpośrednio do swojego konta użytkownika, albo przez grupę przy użyciu naszego możliwość przypisania oparte na grupach licencji.

-   W przypadku aplikacji, które firmy Microsoft lub inna firma publikuje bezpłatnie dla wszystkich użytkowników może otrzymać dostęp za pośrednictwem **zgody użytkownika**. Oznacza to, zaloguj się do aplikacji za pomocą konta usługi Azure AD konta służbowego i zezwala na dostęp do niektórych ograniczony zestaw danych na koncie.

-   W przypadku aplikacji, które publikuje firmy Microsoft lub 3 bezpłatnie dla wszystkich użytkowników może również uzyskać dostęp za pośrednictwem **zgody administratora**. Oznacza to, że administrator wykrył, że aplikacji mogą być używane przez wszystkich użytkowników w organizacji, tak aby zalogować się do aplikacji przy użyciu konta administratora globalnego i przyznać dostęp do wszystkich osób w organizacji.

Aby rozwiązać problem, zacznij od [ogólne obszarów problemów z dostępem aplikacji do należy wziąć pod uwagę](#general-problem-areas-with-application-access-to-consider) , a następnie zapoznaj się z tym przewodnikiem: Kroki rozwiązywania problemów z Microsoft Application dostępu, aby uzyskać szczegóły.

## <a name="general-problem-areas-with-application-access-to-consider"></a>Ogólne obszarów problemów z dostępem aplikacji do uwzględnienia

Lista obszarów ogólny problem, które można rozwinąć Jeśli masz pomysł gdzie zacząć, ale zalecamy przeczytać wskazówki, aby szybko zacząć: Przewodnik: Kroki rozwiązywania problemów z Microsoft Application dostępu.

-   [Problemy z konta użytkownika](#problems-with-the-users-account)

-   [Problemy z grupy](#problems-with-groups)

-   [Problemy z zasadami dostępu warunkowego](#problems-with-conditional-access-policies)

-   [Problemy z zgody aplikacji](#problems-with-application-consent)

## <a name="steps-to-troubleshoot-microsoft-application-access"></a>Kroki umożliwiające rozwiązywanie problemów z dostępem Microsoft Application

Poniżej przedstawiono niektóre typowe problemy, które osoby zajmujące się napotkać podczas ich użytkownicy nie logują się do aplikacji firmy Microsoft.

- Zagadnienia ogólne w celu sprawdzenia

  * Upewnij się, że użytkownik loguje się do **Popraw adres URL** a nie adres URL lokalnej aplikacji.

  * Upewnij się, że konto użytkownika jest **bez blokady.**

  * Upewnij się, że **konto użytkownika istnieje** w usłudze Azure Active Directory. [Sprawdź, czy konto użytkownika istnieje w usłudze Azure Active Directory](#problems-with-the-users-account)

  * Upewnij się, że konto użytkownika jest **włączone** dla logowania. [Sprawdź stan konta użytkownika](#problems-with-the-users-account)

  * Upewnij się, że użytkownika **nie wygasł lub zapomnienia hasła.** [Resetowanie hasła użytkownika](#reset-a-users-password) lub [włączyć samoobsługowe Resetowanie hasła](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-getting-started)

  * Upewnij się, że **uwierzytelnianie wieloskładnikowe** nie blokuje dostęp użytkownika. [Sprawdź stan usługi Multi-Factor authentication użytkownika](#check-a-users-multi-factor-authentication-status) lub [Sprawdź informacje kontaktowe uwierzytelniania użytkownika](#check-a-users-authentication-contact-info)

  * Upewnij się, że **zasad dostępu warunkowego** lub **Identity Protection** zasad nie blokuje dostęp użytkownika. [Sprawdź zasady dostępu warunkowego określonych](#problems-with-conditional-access-policies) lub [Sprawdź zasady dostępu warunkowego określonej aplikacji](#check-a-specific-applications-conditional-access-policy) lub [Wyłącz zasady dostępu warunkowego określonych](#disable-a-specific-conditional-access-policy)

  * Upewnij się, że użytkownik **informacje kontaktowe uwierzytelniania** pozostają aktualne, aby umożliwić uwierzytelnianie wieloskładnikowe lub dostępu warunkowego zasad, które mają być egzekwowane. [Sprawdź stan usługi Multi-Factor authentication użytkownika](#check-a-users-multi-factor-authentication-status) lub [Sprawdź informacje kontaktowe uwierzytelniania użytkownika](#check-a-users-authentication-contact-info)

- Aby uzyskać **Microsoft** **aplikacji, które wymagają licencji** (np. usługi Office 365), Oto kilka konkretnych problemów, aby sprawdzić, gdy wykluczasz powyższe problemy ogólne:

  * Upewnij się, użytkownik lub ma **przypisaną licencję.** [Sprawdź przypisanych licencji użytkownika](#check-a-users-assigned-licenses) lub [Sprawdź przypisanych licencji grupy](#check-a-groups-assigned-licenses)

  * W przypadku licencji **przypisane do** **Grupa statyczna**, upewnij się, że **użytkownik jest członkiem** tej grupy. [Sprawdzanie członkostwa grupy użytkownika](#check-a-users-group-memberships)

  * W przypadku licencji **przypisane do** **grupy dynamicznej**, upewnij się, że **poprawnie ustawiono regułę grupy dynamicznej**. [Sprawdź kryteria członkostwa grupy dynamicznej](#check-a-dynamic-groups-membership-criteria)

  * W przypadku licencji **przypisane do** **grupy dynamicznej**, upewnij się, że grupa dynamiczna ma **zakończeniu przetwarzania** członkostwo i że **użytkownik jest członkiem**  (może to zająć trochę czasu). [Sprawdzanie członkostwa grupy użytkownika](#check-a-users-group-memberships)

  *  Po wprowadzeniu się, że ma przypisaną licencję, upewnij się, licencja jest **niewygasły**.

  *  Upewnij się, że licencja jest **aplikacji** uzyskują dostęp do.

- Aby uzyskać **Microsoft** **aplikacje, które nie wymagają licencji**, poniżej przedstawiono niektóre inne czynności do wykonania:

  * Jeśli aplikacja żąda **uprawnienia na poziomie użytkownika** (na przykład "dostęp do skrzynek pocztowych użytkowników"), upewnij się, że użytkownik zalogował się do aplikacji i przeprowadził **operacji zezwoleniem użytkownika** aby umożliwić aplikacji dostęp do jej danych.

  * Jeśli aplikacja żąda **uprawnienia na poziomie administratora** (na przykład "dostęp do skrzynek pocztowych wszystkich użytkowników"), upewnij się, że przeprowadził administratora globalnego **operacja zgody na poziomie administratora w imieniu wszystkich użytkowników** w organizacji.

## <a name="problems-with-the-users-account"></a>Problemy z konta użytkownika

Dostęp do aplikacji może zostać zablokowany z powodu problemu z użytkownikiem, który jest przypisany do aplikacji. Poniżej przedstawiono kilka sposobów, rozwiązywanie problemów oraz rozwiązywanie problemów z użytkownikami i ich ustawienia konta:

-   [Sprawdź, czy konto użytkownika istnieje w usłudze Azure Active Directory](#check-if-a-user-account-exists-in-azure-active-directory)

-   [Sprawdź stan konta użytkownika](#check-a-users-account-status)

-   [Resetowanie hasła użytkownika](#reset-a-users-password)

-   [Włącz samoobsługowe resetowanie haseł](#enable-self-service-password-reset)

-   [Sprawdź stan uwierzytelniania wieloskładnikowego dla użytkownika](#check-a-users-multi-factor-authentication-status)

-   [Sprawdź informacje kontaktowe uwierzytelniania użytkownika](#check-a-users-authentication-contact-info)

-   [Sprawdzanie członkostwa grupy użytkownika](#check-a-users-group-memberships)

-   [Sprawdź przypisanych licencji użytkownika](#check-a-users-assigned-licenses)

-   [Przypisywanie licencji użytkownika](#assign-a-user-a-license)

### <a name="check-if-a-user-account-exists-in-azure-active-directory"></a>Sprawdź, czy konto użytkownika istnieje w usłudze Azure Active Directory

Aby sprawdzić, czy konto użytkownika jest obecny, wykonaj następujące kroki:

1.  Otwórz [ **witryny Azure portal** ](https://portal.azure.com/) i zaloguj się jako **administratora globalnego.**

2.  Otwórz **rozszerzenia usługi Azure Active Directory** , klikając **wszystkich usług** w górnej części menu główne menu nawigacji po lewej stronie.

3.  Wpisz **"Azure Active Directory**" w polu wyszukiwania filtru i wybierz pozycję **usługi Azure Active Directory** elementu.

4.  Kliknij przycisk **użytkowników i grup** w menu nawigacji.

5.  Kliknij przycisk **wszyscy użytkownicy**.

6.  **Wyszukiwanie** dla użytkownika, jesteś zainteresowany i **kliknij wiersz** do wybrania.

7.  Sprawdź właściwości obiektu użytkownika, należy upewnić się, że spojrzeć zgodnie z oczekiwaniami i żadne dane nie istnieje.

### <a name="check-a-users-account-status"></a>Sprawdź stan konta użytkownika

Aby sprawdzić stan konta użytkownika, wykonaj następujące kroki:

1.  Otwórz [ **witryny Azure portal** ](https://portal.azure.com/) i zaloguj się jako **administratora globalnego.**

2.  Otwórz **rozszerzenia usługi Azure Active Directory** , klikając **wszystkich usług** w górnej części menu główne menu nawigacji po lewej stronie.

3.  Wpisz **"Azure Active Directory**" w polu wyszukiwania filtru i wybierz pozycję **usługi Azure Active Directory** elementu.

4.  Kliknij przycisk **użytkowników i grup** w menu nawigacji.

5.  Kliknij przycisk **wszyscy użytkownicy**.

6.  **Wyszukiwanie** dla użytkownika, jesteś zainteresowany i **kliknij wiersz** do wybrania.

7.  Kliknij przycisk **profilu**.

8.  W obszarze **ustawienia** upewnij się, że **Blokuj logowanie** ustawiono **nie**.

### <a name="reset-a-users-password"></a>Resetowanie hasła użytkownika

Aby zresetować hasło użytkownika, wykonaj następujące kroki:

1.  Otwórz [ **witryny Azure portal** ](https://portal.azure.com/) i zaloguj się jako **administratora globalnego.**

2.  Otwórz **rozszerzenia usługi Azure Active Directory** , klikając **wszystkich usług** w górnej części menu główne menu nawigacji po lewej stronie.

3.  Wpisz **"Azure Active Directory**" w polu wyszukiwania filtru i wybierz pozycję **usługi Azure Active Directory** elementu.

4.  Kliknij przycisk **użytkowników i grup** w menu nawigacji.

5.  Kliknij przycisk **wszyscy użytkownicy**.

6.  **Wyszukiwanie** dla użytkownika, jesteś zainteresowany i **kliknij wiersz** do wybrania.

7.  Kliknij przycisk **Resetuj hasło** znajdujący się u góry okienka użytkownika.

8.  Kliknij przycisk **Resetuj hasło** znajdujący się na **Resetuj hasło** wyświetlonym okienku.

9.  Kopiuj **hasło tymczasowe** lub **wprowadź nowe hasło** dla użytkownika.

10. Komunikować się z tego nowego hasła dla użytkownika, mogą być wymagane, aby zmienić to hasło podczas następnego logowania w usłudze Azure Active Directory.

### <a name="enable-self-service-password-reset"></a>Włączanie samoobsługowego resetowania hasła

Aby włączyć samoobsługowe Resetowanie hasła, wykonaj poniższe kroki wdrożenia:

-   [Umożliwianie użytkownikom resetowania swoich haseł w usłudze Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-getting-started)

-   [Umożliwianie użytkownikom Resetowanie lub zmienianie swoich haseł lokalnych w Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-getting-started)

### <a name="check-a-users-multi-factor-authentication-status"></a>Sprawdź stan uwierzytelniania wieloskładnikowego dla użytkownika

Aby sprawdzić stan uwierzytelniania wieloskładnikowego dla użytkownika, wykonaj następujące kroki:

1. Otwórz [ **witryny Azure portal** ](https://portal.azure.com/) i zaloguj się jako **administratora globalnego.**

2. Otwórz **rozszerzenia usługi Azure Active Directory** , klikając **wszystkich usług** w górnej części menu główne menu nawigacji po lewej stronie.

3. Wpisz **"Azure Active Directory**" w polu wyszukiwania filtru i wybierz pozycję **usługi Azure Active Directory** elementu.

4. Kliknij przycisk **użytkowników i grup** w menu nawigacji.

5. Kliknij przycisk **wszyscy użytkownicy**.

6. Kliknij przycisk **uwierzytelnianie wieloskładnikowe** znajdujący się u góry okienka.

7. Raz **portalu administracyjnego uwierzytelniania wieloskładnikowego** obciążeniami, upewnij się, znajdują się na **użytkowników** kartę.

8. Znajdź użytkownika, na liście użytkowników przez wyszukiwanie, filtrowanie i sortowanie.

9. Wybierz użytkownika z listy użytkowników i **Włącz**, **wyłączyć**, lub **Wymuś** uwierzytelnianie wieloskładnikowe zgodnie z potrzebami.

   * **Uwaga**: Jeśli użytkownik znajduje się w **wymuszone** stanu, możesz je ustawić **wyłączone** tymczasowo w celu umożliwienia im do swojego konta. Gdy są one ponownie, można zmienić ich stan, aby **włączone** ponownie, aby wymagać od nich, aby ponownie zarejestrować informacje kontaktowe podczas następnego logowania w. Alternatywnie, możesz wykonać kroki opisane w [Sprawdź informacje kontaktowe uwierzytelniania użytkownika](#check-a-users-authentication-contact-info) Sprawdź lub ustaw te dane dla nich.

### <a name="check-a-users-authentication-contact-info"></a>Sprawdź informacje kontaktowe uwierzytelniania użytkownika

Aby sprawdzić informacje kontaktowe uwierzytelniania użytkownika używane do uwierzytelniania wieloskładnikowego, dostępu warunkowego, ochrony tożsamości i resetowania hasła, wykonaj następujące kroki:

1.  Otwórz [ **witryny Azure portal** ](https://portal.azure.com/) i zaloguj się jako **administratora globalnego.**

2.  Otwórz **rozszerzenia usługi Azure Active Directory** , klikając **wszystkich usług** w górnej części menu główne menu nawigacji po lewej stronie.

3.  Wpisz **"Azure Active Directory**" w polu wyszukiwania filtru i wybierz pozycję **usługi Azure Active Directory** elementu.

4.  Kliknij przycisk **użytkowników i grup** w menu nawigacji.

5.  Kliknij przycisk **wszyscy użytkownicy**.

6.  **Wyszukiwanie** dla użytkownika, jesteś zainteresowany i **kliknij wiersz** do wybrania.

7.  Kliknij przycisk **profilu**.

8.  Przewiń w dół do **informacje kontaktowe uwierzytelniania**.

9.  **Przegląd** dane zarejestrowane dla użytkowników i aktualizacji, zgodnie z potrzebami.

### <a name="check-a-users-group-memberships"></a>Sprawdzanie członkostwa grupy użytkownika

Aby sprawdzić członkostwa w grupach użytkowników, wykonaj następujące kroki:

1.  Otwórz [ **witryny Azure portal** ](https://portal.azure.com/) i zaloguj się jako **administratora globalnego.**

2.  Otwórz **rozszerzenia usługi Azure Active Directory** , klikając **wszystkich usług** w górnej części menu główne menu nawigacji po lewej stronie.

3.  Wpisz **"Azure Active Directory**" w polu wyszukiwania filtru i wybierz pozycję **usługi Azure Active Directory** elementu.

4.  Kliknij przycisk **użytkowników i grup** w menu nawigacji.

5.  Kliknij przycisk **wszyscy użytkownicy**.

6.  **Wyszukiwanie** dla użytkownika, jesteś zainteresowany i **kliknij wiersz** do wybrania.

7.  Kliknij przycisk **grup** aby zobaczyć, który grupuje użytkownik jest członkiem.

### <a name="check-a-users-assigned-licenses"></a>Sprawdź przypisanych licencji użytkownika

Aby sprawdzić przypisanych licencji użytkownika, wykonaj następujące kroki:

1.  Otwórz [ **witryny Azure portal** ](https://portal.azure.com/) i zaloguj się jako **administratora globalnego.**

2.  Otwórz **rozszerzenia usługi Azure Active Directory** , klikając **wszystkich usług** w górnej części menu główne menu nawigacji po lewej stronie.

3.  Wpisz **"Azure Active Directory**" w polu wyszukiwania filtru i wybierz pozycję **usługi Azure Active Directory** elementu.

4.  Kliknij przycisk **użytkowników i grup** w menu nawigacji.

5.  Kliknij przycisk **wszyscy użytkownicy**.

6.  **Wyszukiwanie** dla użytkownika, jesteś zainteresowany i **kliknij wiersz** do wybrania.

7.  Kliknij przycisk **licencji** aby zobaczyć, które obecnie licencji użytkownika został przypisany.

### <a name="assign-a-user-a-license"></a>Przypisywanie licencji użytkownika 

Aby przypisać licencję do użytkownika, wykonaj następujące kroki:

1.  Otwórz [ **witryny Azure portal** ](https://portal.azure.com/) i zaloguj się jako **administratora globalnego.**

2.  Otwórz **rozszerzenia usługi Azure Active Directory** , klikając **wszystkich usług** w górnej części menu główne menu nawigacji po lewej stronie.

3.  Wpisz **"Azure Active Directory**" w polu wyszukiwania filtru i wybierz pozycję **usługi Azure Active Directory** elementu.

4.  Kliknij przycisk **użytkowników i grup** w menu nawigacji.

5.  Kliknij przycisk **wszyscy użytkownicy**.

6.  **Wyszukiwanie** dla użytkownika, jesteś zainteresowany i **kliknij wiersz** do wybrania.

7.  Kliknij przycisk **licencji** aby zobaczyć, które obecnie licencji użytkownika został przypisany.

8.  Kliknij przycisk **przypisać** przycisku.

9.  Wybierz **jeden lub więcej produktów** z listy dostępnych produktów.

10. **Opcjonalnie** kliknij **opcje przydziału** element do szczegółowego przypisania produktów. Kliknij przycisk **Ok** po zakończeniu.

11. Kliknij przycisk **przypisać** przycisk, aby przypisać licencje do tego użytkownika.

## <a name="problems-with-groups"></a>Problemy z grupy

Dostęp do aplikacji może zostać zablokowany z powodu problemu z grupy, która jest przypisana do aplikacji. Poniżej przedstawiono kilka sposobów, rozwiązywanie problemów oraz rozwiązywanie problemów z grup i członkostw w grupie:

-   [Sprawdź członkostwo w grupie](#check-a-groups-membership)

-   [Sprawdź kryteria członkostwa grupy dynamicznej](#check-a-dynamic-groups-membership-criteria)

-   [Sprawdź grupy przypisane licencje](#check-a-groups-assigned-licenses)

-   [Ponownego przetworzenia licencji grupy](#reprocess-a-groups-licenses)

-   [Przypisz do grupy licencji](#assign-a-group-a-license)

### <a name="check-a-groups-membership"></a>Sprawdź członkostwo w grupie

Aby sprawdzić członkostwa w grupie, wykonaj następujące kroki:

1.  Otwórz [ **witryny Azure portal** ](https://portal.azure.com/) i zaloguj się jako **administratora globalnego.**

2.  Otwórz **rozszerzenia usługi Azure Active Directory** , klikając **wszystkich usług** w górnej części menu główne menu nawigacji po lewej stronie.

3.  Wpisz **"Azure Active Directory**" w polu wyszukiwania filtru i wybierz pozycję **usługi Azure Active Directory** elementu.

4.  Kliknij przycisk **użytkowników i grup** w menu nawigacji.

5.  Kliknij przycisk **wszystkich grup**.

6.  **Wyszukiwanie** grupy jesteś zainteresowany i **kliknij wiersz** do wybrania.

7.  Kliknij przycisk **członków** Aby przejrzeć listę użytkowników przypisanych do tej grupy.

### <a name="check-a-dynamic-groups-membership-criteria"></a>Sprawdź kryteria członkostwa grupy dynamicznej 

Aby sprawdzić kryteria członkostwa grupy dynamicznej, wykonaj następujące kroki:

1.  Otwórz [ **witryny Azure portal** ](https://portal.azure.com/) i zaloguj się jako **administratora globalnego.**

2.  Otwórz **rozszerzenia usługi Azure Active Directory** , klikając **wszystkich usług** w górnej części menu główne menu nawigacji po lewej stronie.

3.  Wpisz **"Azure Active Directory**" w polu wyszukiwania filtru i wybierz pozycję **usługi Azure Active Directory** elementu.

4.  Kliknij przycisk **użytkowników i grup** w menu nawigacji.

5.  Kliknij przycisk **wszystkich grup**.

6.  **Wyszukiwanie** grupy jesteś zainteresowany i **kliknij wiersz** do wybrania.

7.  Kliknij przycisk **reguły członkostwa dynamicznego.**

8.  Przegląd **proste** lub **zaawansowane** reguły zdefiniowane dla tej grupy i upewnij się, że użytkownik, powinien należeć do tej grupy spełnia podane kryteria.

### <a name="check-a-groups-assigned-licenses"></a>Sprawdź grupy przypisane licencje

Aby sprawdzić grupy przypisane licencje, wykonaj następujące kroki:

1.  Otwórz [ **witryny Azure portal** ](https://portal.azure.com/) i zaloguj się jako **administratora globalnego.**

2.  Otwórz **rozszerzenia usługi Azure Active Directory** , klikając **wszystkich usług** w górnej części menu główne menu nawigacji po lewej stronie.

3.  Wpisz **"Azure Active Directory**" w polu wyszukiwania filtru i wybierz pozycję **usługi Azure Active Directory** elementu.

4.  Kliknij przycisk **użytkowników i grup** w menu nawigacji.

5.  Kliknij przycisk **wszystkich grup**.

6.  **Wyszukiwanie** grupy jesteś zainteresowany i **kliknij wiersz** do wybrania.

7.  Kliknij przycisk **licencji** aby zobaczyć, które obecnie licencji grupy został przypisany.

### <a name="reprocess-a-groups-licenses"></a>Ponownego przetworzenia licencji grupy

Aby ponownie przetworzyć grupy przypisane licencje, wykonaj następujące kroki:

1. Otwórz [ **witryny Azure portal** ](https://portal.azure.com/) i zaloguj się jako **administratora globalnego.**

2. Otwórz **rozszerzenia usługi Azure Active Directory** , klikając **wszystkich usług** w górnej części menu główne menu nawigacji po lewej stronie.

3. Wpisz **"Azure Active Directory**" w polu wyszukiwania filtru i wybierz pozycję **usługi Azure Active Directory** elementu.

4. Kliknij przycisk **użytkowników i grup** w menu nawigacji.

5. Kliknij przycisk **wszystkich grup**.

6. **Wyszukiwanie** grupy jesteś zainteresowany i **kliknij wiersz** do wybrania.

7. Kliknij przycisk **licencji** aby zobaczyć, które obecnie licencji grupy został przypisany.

8. Kliknij przycisk **ponownie przetworzyć** przycisk, aby upewnić się, że licencje przypisane do członków tej grupy są aktualne. Może to potrwać długo w zależności od rozmiaru i złożoności grupy.

   >[!NOTE]
   >Aby szybciej to zrobić, należy wziąć pod uwagę tymczasowo bezpośrednie przypisanie licencji do użytkownika. [Przypisywanie licencji użytkownika](#problems-with-application-consent).
   >
   >

### <a name="assign-a-group-a-license"></a>Przypisz do grupy licencji

Aby przypisać licencję do grupy, wykonaj następujące kroki:

1. Otwórz [ **witryny Azure portal** ](https://portal.azure.com/) i zaloguj się jako **administratora globalnego.**

2. Otwórz **rozszerzenia usługi Azure Active Directory** , klikając **wszystkich usług** w górnej części menu główne menu nawigacji po lewej stronie.

3. Wpisz **"Azure Active Directory**" w polu wyszukiwania filtru i wybierz pozycję **usługi Azure Active Directory** elementu.

4. Kliknij przycisk **użytkowników i grup** w menu nawigacji.

5. Kliknij przycisk **wszystkich grup**.

6. **Wyszukiwanie** grupy jesteś zainteresowany i **kliknij wiersz** do wybrania.

7. Kliknij przycisk **licencji** aby zobaczyć, które obecnie licencji grupy został przypisany.

8. Kliknij przycisk **przypisać** przycisku.

9. Wybierz **jeden lub więcej produktów** z listy dostępnych produktów.

10. **Opcjonalnie** kliknij **opcje przydziału** element do szczegółowego przypisania produktów. Kliknij przycisk **Ok** po zakończeniu.

11. Kliknij przycisk **przypisać** przycisk, aby przypisać licencje do tej grupy. Może to potrwać długo w zależności od rozmiaru i złożoności grupy.

    >[!NOTE]
    >Aby szybciej to zrobić, należy wziąć pod uwagę tymczasowo bezpośrednie przypisanie licencji do użytkownika. [Przypisywanie licencji użytkownika](#problems-with-application-consent).
    > 
    >

## <a name="problems-with-conditional-access-policies"></a>Problemy z zasadami dostępu warunkowego

### <a name="check-a-specific-conditional-access-policy"></a>Sprawdź zasady dostępu warunkowego określonych

Aby sprawdzić lub Sprawdzanie poprawności zasad dostępu warunkowego pojedynczego:

1. Otwórz [ **witryny Azure portal** ](https://portal.azure.com/) i zaloguj się jako **administratora globalnego.**

2. Otwórz **rozszerzenia usługi Azure Active Directory** , klikając **wszystkich usług** w górnej części menu główne menu nawigacji po lewej stronie.

3. Wpisz **"Azure Active Directory**" w polu wyszukiwania filtru i wybierz pozycję **usługi Azure Active Directory** elementu.

4. Kliknij przycisk **aplikacje dla przedsiębiorstw** w menu nawigacji.

5. Kliknij przycisk **dostępu warunkowego** element nawigacji.

6. Kliknij zasady, jesteś zainteresowany sprawdzania.

7. Zapoznaj się, że nie określone warunki, przypisania ani innych ustawień, które mogą blokować dostęp użytkownika.

   >[!NOTE]
   >Możesz też chcieć tymczasowo wyłączyć tę zasadę, aby upewnić się, go nie wpływają na logowania. Aby to zrobić, należy ustawić **Włącz zasady** Przełącz, aby **nie** i kliknij przycisk **Zapisz** przycisku.
   >
   >

### <a name="check-a-specific-applications-conditional-access-policy"></a>Sprawdź zasady dostępu warunkowego konkretnej aplikacji

Aby sprawdzić lub zweryfikować pojedynczej aplikacji obecnie skonfigurowane zasady dostępu warunkowego:

1.  Otwórz [ **witryny Azure portal** ](https://portal.azure.com/) i zaloguj się jako **administratora globalnego.**

2.  Otwórz **rozszerzenia usługi Azure Active Directory** , klikając **wszystkich usług** w górnej części menu główne menu nawigacji po lewej stronie.

3.  Wpisz **"Azure Active Directory**" w polu wyszukiwania filtru i wybierz pozycję **usługi Azure Active Directory** elementu.

4.  Kliknij przycisk **aplikacje dla przedsiębiorstw** w menu nawigacji.

5.  Kliknij przycisk **wszystkie aplikacje**.

6.  Wyszukaj aplikację, która Cię interesuje, lub użytkownik próbuje zalogować się do aplikacji nazwę wyświetlaną lub identyfikator aplikacji.

     >[!NOTE]
     >Jeśli nie widzisz aplikacji, którego szukasz, kliknij przycisk **filtru** przycisk i rozszerzyć zakres na liście, aby **wszystkie aplikacje**. Jeśli chcesz zobaczyć więcej kolumn, kliknij przycisk **kolumn** przycisk, aby dodać dodatkowe szczegóły dla aplikacji.
     >
     >

7.  Kliknij przycisk **dostępu warunkowego** element nawigacji.

8.  Kliknij zasady, jesteś zainteresowany sprawdzania.

9.  Zapoznaj się, że nie określone warunki, przypisania ani innych ustawień, które mogą blokować dostęp użytkownika.

     >[!NOTE]
     >Możesz też chcieć tymczasowo wyłączyć tę zasadę, aby upewnić się, go nie wpływają na logowania. Aby to zrobić, należy ustawić **Włącz zasady** Przełącz, aby **nie** i kliknij przycisk **Zapisz** przycisku.
     >
     >

### <a name="disable-a-specific-conditional-access-policy"></a>Wyłącz zasady dostępu warunkowego określonych

Aby sprawdzić lub Sprawdzanie poprawności zasad dostępu warunkowego pojedynczego:

1.  Otwórz [ **witryny Azure portal** ](https://portal.azure.com/) i zaloguj się jako **administratora globalnego.**

2.  Otwórz **rozszerzenia usługi Azure Active Directory** , klikając **wszystkich usług** w górnej części menu główne menu nawigacji po lewej stronie.

3.  Wpisz **"Azure Active Directory**" w polu wyszukiwania filtru i wybierz pozycję **usługi Azure Active Directory** elementu.

4.  Kliknij przycisk **aplikacje dla przedsiębiorstw** w menu nawigacji.

5.  Kliknij przycisk **dostępu warunkowego** element nawigacji.

6.  Kliknij zasady, jesteś zainteresowany sprawdzania.

7.  Wyłącz zasady, ustawiając **Włącz zasady** Przełącz, aby **nie** i kliknij przycisk **Zapisz** przycisku.

## <a name="problems-with-application-consent"></a>Problemy z zgody aplikacji

Może zostać zablokowany dostęp do aplikacji, ponieważ nie przeprowadzono operacji wyrażania zgody odpowiednie uprawnienia. Poniżej przedstawiono kilka sposobów, rozwiązywanie problemów oraz rozwiązywania problemów z aplikacjami zgody:

-   [Wykonaj operację zezwoleniem użytkownika](#perform-a-user-level-consent-operation)

-   [Operacja zgody na poziomie administratora dla każdej aplikacji](#perform-administrator-level-consent-operation-for-any-application)

-   [Wykonaj zgody na poziomie administratora dla aplikacji jednej dzierżawy](#perform-administrator-level-consent-for-a-single-tenant-application)

-   [Wykonaj zgody na poziomie administratora dla aplikacji wielodostępnych](#perform-administrator-level-consent-for-a-multi-tenant-application)

### <a name="perform-a-user-level-consent-operation"></a>Wykonaj operację zezwoleniem użytkownika

-   Dla dowolnego Open ID Connect aplikacja obsługująca żąda uprawnień przejść do ekranu logowania aplikacji wykonuje poziomu zgody użytkownika, do aplikacji dla zalogowanego użytkownika.

-   Jeśli chcesz to zrobić programowo, zobacz [żądanie zgody użytkownika](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes#requesting-individual-user-consent).

### <a name="perform-administrator-level-consent-operation-for-any-application"></a>Operacja zgody na poziomie administratora dla każdej aplikacji

-   Dla **tylko aplikacje opracowane przy użyciu modelu aplikacji w wersji 1**, możesz wymusić tego poziomu zgody administratora wystąpią, dodając "**? prompt = administrator\_zgody**" na końcu podpisanie aplikacji w adresie URL.

-   Dla **dowolnej aplikacji opracowanych za pomocą modelu aplikacji w wersji 2**, można wymusić użycie tego administratora zezwoleniem wystąpią, postępując zgodnie z instrukcjami dotyczącymi **żądania uprawnień z administratorem katalogu** części [przy użyciu punktu końcowego zgody administratora](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes#using-the-admin-consent-endpoint).

### <a name="perform-administrator-level-consent-for-a-single-tenant-application"></a>Wykonaj zgody na poziomie administratora dla aplikacji jednej dzierżawy

-   Dla **aplikacje z jedną dzierżawą** , zażądać uprawnień (podobnie jak tworzysz, albo mieć w swojej organizacji), można wykonać **administracyjne zezwoleniem** operacji w imieniu wszystkich użytkownikom logowania się jako Administrator globalny, a następnie klikając **udzielić uprawnień** znajdujący się u góry **rejest aplikacji —&gt; wszystkie aplikacje —&gt; wybierz aplikację, -&gt; Wymagane uprawnienia** okienka.

-   Dla **dowolnej aplikacji opracowanych za pomocą modelu aplikacji V1 lub V2**, można wymusić użycie tego administratora zezwoleniem wystąpią, postępując zgodnie z instrukcjami dotyczącymi **żądania uprawnień od administratora katalogu**  części [przy użyciu punktu końcowego zgody administratora](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes#using-the-admin-consent-endpoint).

### <a name="perform-administrator-level-consent-for-a-multi-tenant-application"></a>Wykonaj zgody na poziomie administratora dla aplikacji wielodostępnych

-   Dla **aplikacjom wielodostępnym** tego zażądać uprawnień (np. strony trzeciej aplikacji lub firmie Microsoft opracowuje), można wykonać **administracyjne zezwoleniem** operacji. Zaloguj się jako Administrator globalny i klikając **udzielić uprawnień** przycisku w obszarze **aplikacje dla przedsiębiorstw —&gt; wszystkie aplikacje —&gt; wybierz aplikację, -&gt; uprawnień**  okienko (dostępne wkrótce).

-   Można również wymusić tego administratora zezwoleniem wystąpią, postępując zgodnie z instrukcjami dotyczącymi **żądania uprawnień od administratora katalogu** części [przy użyciu punktu końcowego zgody administratora](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes#using-the-admin-consent-endpoint).

## <a name="next-steps"></a>Kolejne kroki
[Przy użyciu punktu końcowego zgody administratora](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes#using-the-admin-consent-endpoint)

