---
title: Problemy przy logowaniu do aplikacji firmy Microsoft | Dokumentacja firmy Microsoft
description: Rozwiązywanie typowych problemów, które muszą ponieść po zalogowaniu się do firmy Microsoft Applications przy użyciu usługi Azure AD (takich jak usługi Office 365)
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: barbkess
ms.reviewer: asteen
ms.openlocfilehash: 4053c272fe78647ac646e0feefa884cf014a6b72
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "36334228"
---
## <a name="problems-signing-in-to-a-microsoft-application"></a>Problemy przy logowaniu do aplikacji firmy Microsoft

Microsoft Applications (takich jak Office 365 Exchange, SharePoint, Yammer itp.) są przypisane i zarządzane nieco inaczej niż 3 aplikacji SaaS innych producentów i inne aplikacje, które integracji z usługą Azure AD na logowanie jednokrotne w.

Istnieją trzy sposoby, czy użytkownik może uzyskać dostęp do aplikacji publikowanych przez firmy Microsoft.

-   W przypadku aplikacji w usłudze Office 365 lub innych mechanizmów płatną użytkownicy uzyskują dostęp za pośrednictwem **przypisania licencji** albo bezpośrednio do swojego konta użytkownika, albo przez grupę przy użyciu naszego możliwość przypisania na podstawie grupy licencji.

-   Dla aplikacji, które firmy Microsoft lub stron trzecich publikuje za darmo dla każdego z nich do używania, użytkownicy mogą otrzymać dostęp za pośrednictwem **zgody użytkownika**. This0 oznacza, że Zaloguj się do aplikacji z ich Azure AD konta służbowego i zezwolenie na dostęp do niektórych ograniczony zestaw danych na koncie.

-   Dla aplikacji, które firmy Microsoft lub 3rd Strona publikuje za darmo dla każdego z nich do używania, użytkownicy mogą również uzyskać dostęp za pośrednictwem **zgody administratora**. Oznacza to, że administrator wykrył, że aplikacja może być używany przez wszyscy użytkownicy w organizacji, aby zalogować się do aplikacji przy użyciu konta administratora globalnego i przyznać dostęp wszystkim użytkownikom w organizacji.

Aby rozwiązać problem, uruchom przy użyciu [ogólne obszarów problemów z dostępem do aplikacji wziąć pod uwagę](#general-problem-areas-with-application-access-to-consider) , a następnie odczytywane [wskazówki: kroki rozwiązywania problemów z Microsoft Application dostępu](#walkthrough-steps-to-troubleshoot-microsoft-application-access) umożliwia pobranie do szczegółowe informacje.

## <a name="general-problem-areas-with-application-access-to-consider"></a>Ogólne obszarów problemów z dostępem aplikacji do uwzględnienia

Poniżej przedstawiono listę obszarów problemów ogólne, które można przejść do szczegółów Jeśli wiadomo, gdzie można uruchomić, ale zaleca się przeczytanie wskazówki, aby zacząć szybko: [wskazówki: kroki rozwiązywania problemów z Microsoft Application dostępu](#walkthrough-steps-to-troubleshoot-microsoft-application-access).

-   [Problemy z kontem użytkownika](#problems-with-the-users-account)

-   [Problemy z grupy](#problems-with-groups)

-   [Problemy z zasadami dostępu warunkowego](#problems-with-conditional-access-policies)

-   [Problemy z zgody aplikacji](#problems-with-application-consent)

## <a name="steps-to-troubleshoot-microsoft-application-access"></a>Kroki rozwiązywania problemów z Microsoft Application dostępu

Poniżej przedstawiono kilka typowych problemów, które pracowników wystąpiły podczas ich użytkownik nie zaloguje się do aplikacji firmy Microsoft.

-   Ogólne problemy, aby sprawdzić w pierwszej kolejności

  * Upewnij się, że użytkownik loguje się do **Popraw adres URL** , a nie adres URL lokalnej aplikacji.

  * Upewnij się, że konto użytkownika jest **bez blokady.**

  * Upewnij się, że **konto użytkownika istnieje** w usłudze Azure Active Directory. [Sprawdź, czy konto użytkownika istnieje w usłudze Azure Active Directory](#problems-with-the-users-account)

  * Upewnij się, że konto użytkownika jest **włączone** dla logowania. [Sprawdź stan konta użytkownika](#problems-with-the-users-account)

  * Upewnij się, że użytkownika **nie wygasł lub zapomnienia hasła.** [Resetuj hasło użytkownika](#reset-a-users-password) lub [włączyć samoobsługowe Resetowanie hasła](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-getting-started)

   * Upewnij się, że **uwierzytelnianie wieloskładnikowe** nie blokuje dostępu użytkownika. [Sprawdź stan usługi Multi-Factor authentication użytkownika](#check-a-users-multi-factor-authentication-status) lub [Sprawdź informacje kontaktowe uwierzytelniania użytkownika](#check-a-users-authentication-contact-info)

   * Upewnij się, że **zasady dostępu warunkowego** lub **Identity Protection** zasad nie blokuje dostępu użytkownika. [Sprawdź zasady dostępu warunkowego określonych](#problems-with-conditional-access-policies) lub [Sprawdź zasady dostępu warunkowego określonej aplikacji](#check-a-specific-applications-conditional-access-policy) lub [zasad dostępu warunkowego określonych wyłączone](#disable-a-specific-conditional-access-policy)

   * Upewnij się, że użytkownik **informacje kontaktowe uwierzytelniania** jest aktualny, aby umożliwić uwierzytelnianie wieloskładnikowe lub dostępu warunkowego zasad, które mają być egzekwowane. [Sprawdź stan usługi Multi-Factor authentication użytkownika](#check-a-users-multi-factor-authentication-status) lub [Sprawdź informacje kontaktowe uwierzytelniania użytkownika](#check-a-users-authentication-contact-info)

-   Aby uzyskać **Microsoft** **aplikacje, które wymagają licencji** (takich jak usługi Office 365), poniżej przedstawiono niektóre określonych problemów, aby sprawdzić po zostały wykluczone powyższe problemy ogólne:

   * Upewnij się, użytkownika lub ma **przypisanej licencji.** [Sprawdź przypisane licencje użytkownika](#check-a-users-assigned-licenses) lub [Sprawdź grupy przypisane licencje.](#check-a-groups-assigned-licenses)

   * W przypadku licencji **przypisane do** **Grupa statyczna**, upewnij się, że **użytkownik jest członkiem** tej grupy. [Sprawdzanie członkostwa w grupach użytkownika](#check-a-users-group-memberships)

   * W przypadku licencji **przypisane do** **Dynamiczna grupa**, upewnij się, że **grupa dynamiczna reguła została poprawnie ustawiona**. [Sprawdź kryteria członkostwa grupy dynamicznej](#check-a-dynamic-groups-membership-criteria)

   * W przypadku licencji **przypisane do** **Dynamiczna grupa**, upewnij się, że grupa dynamiczna ma **zakończyło się przetwarzanie** członkostwa oraz że **użytkownik jest członkiem**  (może to zająć pewien czas). [Sprawdzanie członkostwa w grupach użytkownika](#check-a-users-group-memberships)

   *  Po należy upewnić się, licencja jest przypisany, upewnij się, że licencja jest **niewygasły**.

   *  Upewnij się, że licencja jest **dla aplikacji** uzyskują oni dostęp.

-   Dla **Microsoft** **aplikacje, które nie wymagają licencji**, poniżej przedstawiono niektóre inne czynności do wykonania:

   * Jeśli aplikacja żąda **uprawnienia na poziomie użytkownika** (na przykład "dostęp do skrzynek pocztowych użytkowników"), upewnij się, że użytkownik zalogował się do aplikacji i wykonał **zgody użytkownika na poziomie operacji** aby umożliwić aplikacji dostęp do swoich danych.

   * Jeśli aplikacja żąda **uprawnień na poziomie administratora** (na przykład "dostęp do skrzynek pocztowych wszystkich użytkowników"), upewnij się, że przeprowadził administratora globalnego **operacja zgody na poziomie administratora imieniu wszyscy użytkownicy** w organizacji.

## <a name="problems-with-the-users-account"></a>Problemy z kontem użytkownika

Dostęp do aplikacji mogą zostać zablokowane z powodu problemu z użytkownikiem, który jest przypisany do aplikacji. Poniżej przedstawiono kilka sposobów umożliwiają rozwiązywanie problemów oraz rozwiązywania problemów z użytkownikami i ich ustawienia konta:

-   [Sprawdź, czy konto użytkownika istnieje w usłudze Azure Active Directory](#check-if-a-user-account-exists-in-azure-active-directory)

-   [Sprawdź stan konta użytkownika](#check-a-users-account-status)

-   [Resetowanie hasła użytkownika](#reset-a-users-password)

-   [Włącz samoobsługowe resetowanie haseł](#enable-self-service-password-reset)

-   [Sprawdź stan usługi Multi-Factor authentication użytkownika](#check-a-users-multi-factor-authentication-status)

-   [Sprawdź informacje kontaktowe uwierzytelniania użytkownika](#check-a-users-authentication-contact-info)

-   [Sprawdzanie członkostwa w grupach użytkownika](#check-a-users-group-memberships)

-   [Sprawdź przypisane licencje użytkownika](#check-a-users-assigned-licenses)

-   [Przypisywanie licencji użytkownika](#assign-a-user-a-license)

### <a name="check-if-a-user-account-exists-in-azure-active-directory"></a>Sprawdź, czy konto użytkownika istnieje w usłudze Azure Active Directory

Aby sprawdzić, czy konto użytkownika jest obecne, wykonaj następujące kroki:

1.  Otwórz [ **portalu Azure** ](https://portal.azure.com/) i zaloguj się jako **administratora globalnego.**

2.  Otwórz **rozszerzenia usług Azure Active Directory** klikając **wszystkie usługi** w górnej części menu nawigacji po lewej stronie głównej.

3.  Wpisz w **"Azure Active Directory**" w polu wyszukiwania filtr a wybierz **usługi Azure Active Directory** elementu.

4.  Kliknij przycisk **użytkowników i grup** w menu nawigacji.

5.  Kliknij przycisk **wszyscy użytkownicy**.

6.  **Wyszukiwanie** dla użytkownika planuje się i **kliknij wiersz** do wybrania.

7.  Sprawdź właściwości obiektu użytkownika, należy upewnić się, że wyglądają zgodnie z oczekiwaniami i żadne dane nie istnieje.

### <a name="check-a-users-account-status"></a>Sprawdź stan konta użytkownika

Aby sprawdzić stan konta użytkownika, wykonaj następujące kroki:

1.  Otwórz [ **portalu Azure** ](https://portal.azure.com/) i zaloguj się jako **administratora globalnego.**

2.  Otwórz **rozszerzenia usług Azure Active Directory** klikając **wszystkie usługi** w górnej części menu nawigacji po lewej stronie głównej.

3.  Wpisz w **"Azure Active Directory**" w polu wyszukiwania filtr a wybierz **usługi Azure Active Directory** elementu.

4.  Kliknij przycisk **użytkowników i grup** w menu nawigacji.

5.  Kliknij przycisk **wszyscy użytkownicy**.

6.  **Wyszukiwanie** dla użytkownika planuje się i **kliknij wiersz** do wybrania.

7.  Kliknij przycisk **profilu**.

8.  W obszarze **ustawienia** upewnij się, że **Zaloguj bloku** ustawiono **nr**.

### <a name="reset-a-users-password"></a>Resetowanie hasła użytkownika

Aby zresetować hasło użytkownika, wykonaj następujące kroki:

1.  Otwórz [ **portalu Azure** ](https://portal.azure.com/) i zaloguj się jako **administratora globalnego.**

2.  Otwórz **rozszerzenia usług Azure Active Directory** klikając **wszystkie usługi** w górnej części menu nawigacji po lewej stronie głównej.

3.  Wpisz w **"Azure Active Directory**" w polu wyszukiwania filtr a wybierz **usługi Azure Active Directory** elementu.

4.  Kliknij przycisk **użytkowników i grup** w menu nawigacji.

5.  Kliknij przycisk **wszyscy użytkownicy**.

6.  **Wyszukiwanie** dla użytkownika planuje się i **kliknij wiersz** do wybrania.

7.  Kliknij przycisk **resetowania hasła** w górnej części okienka użytkownika.

8.  Kliknij przycisk **resetowania hasła** znajdującego się na **resetowania hasła** okienko zostanie wyświetlone.

9.  Kopiuj **hasło tymczasowe** lub **wprowadź nowe hasło** dla użytkownika.

10. Komunikować się z tego nowego hasła dla użytkownika, konieczności zmianę hasła podczas kolejnego logowania w usłudze Azure Active Directory.

### <a name="enable-self-service-password-reset"></a>Włącz samoobsługowe Resetowanie hasła

Aby włączyć samoobsługowe Resetowanie hasła, wykonaj poniższe kroki wdrażania:

-   [Umożliwianie użytkownikom resetowania swoich haseł w usłudze Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-getting-started#enable-users-to-reset-their-azure-ad-passwords)

-   [Umożliwianie użytkownikom resetowania lub zmieniania swoich haseł lokalnej usługi Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-getting-started#enable-users-to-reset-or-change-their-ad-passwords)

### <a name="check-a-users-multi-factor-authentication-status"></a>Sprawdź stan usługi Multi-Factor authentication użytkownika

Aby sprawdzić stan usługi Multi-Factor authentication użytkownika, wykonaj następujące kroki:

1.  Otwórz [ **portalu Azure** ](https://portal.azure.com/) i zaloguj się jako **administratora globalnego.**

2.  Otwórz **rozszerzenia usług Azure Active Directory** klikając **wszystkie usługi** w górnej części menu nawigacji po lewej stronie głównej.

3.  Wpisz w **"Azure Active Directory**" w polu wyszukiwania filtr a wybierz **usługi Azure Active Directory** elementu.

4.  Kliknij przycisk **użytkowników i grup** w menu nawigacji.

5.  Kliknij przycisk **wszyscy użytkownicy**.

6.  Kliknij przycisk **uwierzytelnianie wieloskładnikowe** u góry okienka.

7.  Raz **portalu administracyjnego uwierzytelniania wieloskładnikowego** obciążeń, upewnij się, czy na **użytkowników** kartę.

8.  Znajdź użytkownika, na liście użytkowników przez wyszukiwanie, filtrowanie i sortowanie.

9.  Wybierz użytkownika z listy użytkowników i **włączyć**, **wyłączyć**, lub **Wymuś** usługi Multi-Factor authentication zgodnie z potrzebami.

  * **Uwaga**: Jeśli użytkownik znajduje się w **wymuszone** stanu, użytkownik może ustawić ich **wyłączone** tymczasowo w celu umożliwienia im wrócić do swojego konta. Gdy są one ponownie, można zmienić ich stan, aby **włączone** ponownie, aby wymagały ponownie zarejestrować swoje informacje kontaktowe podczas ich następnego logowania. Alternatywnie możesz wykonać kroki opisane w [Sprawdź informacje kontaktowe uwierzytelniania użytkownika](#check-a-users-authentication-contact-info) Sprawdź lub ustaw dla nich dane.

### <a name="check-a-users-authentication-contact-info"></a>Sprawdź informacje kontaktowe uwierzytelniania użytkownika

Aby sprawdzić informacje kontaktowe uwierzytelniania użytkownika używane do uwierzytelniania wieloskładnikowego, dostępu warunkowego, ochrony tożsamości i resetowania hasła, wykonaj następujące kroki:

1.  Otwórz [ **portalu Azure** ](https://portal.azure.com/) i zaloguj się jako **administratora globalnego.**

2.  Otwórz **rozszerzenia usług Azure Active Directory** klikając **wszystkie usługi** w górnej części menu nawigacji po lewej stronie głównej.

3.  Wpisz w **"Azure Active Directory**" w polu wyszukiwania filtr a wybierz **usługi Azure Active Directory** elementu.

4.  Kliknij przycisk **użytkowników i grup** w menu nawigacji.

5.  Kliknij przycisk **wszyscy użytkownicy**.

6.  **Wyszukiwanie** dla użytkownika planuje się i **kliknij wiersz** do wybrania.

7.  Kliknij przycisk **profilu**.

8.  Przewiń w dół do **informacje kontaktowe uwierzytelniania**.

9.  **Przegląd** danych zarejestrowanych dla użytkowników i aktualizacji zgodnie z potrzebami.

### <a name="check-a-users-group-memberships"></a>Sprawdzanie członkostwa w grupach użytkownika

Aby sprawdzić członkostwa w grupach użytkownika, wykonaj następujące kroki:

1.  Otwórz [ **portalu Azure** ](https://portal.azure.com/) i zaloguj się jako **administratora globalnego.**

2.  Otwórz **rozszerzenia usług Azure Active Directory** klikając **wszystkie usługi** w górnej części menu nawigacji po lewej stronie głównej.

3.  Wpisz w **"Azure Active Directory**" w polu wyszukiwania filtr a wybierz **usługi Azure Active Directory** elementu.

4.  Kliknij przycisk **użytkowników i grup** w menu nawigacji.

5.  Kliknij przycisk **wszyscy użytkownicy**.

6.  **Wyszukiwanie** dla użytkownika planuje się i **kliknij wiersz** do wybrania.

7.  Kliknij przycisk **grup** Aby wyświetlić grupy, które użytkownik jest członkiem.

### <a name="check-a-users-assigned-licenses"></a>Sprawdź przypisane licencje użytkownika

Aby sprawdzić przypisane licencje użytkownika, wykonaj następujące kroki:

1.  Otwórz [ **portalu Azure** ](https://portal.azure.com/) i zaloguj się jako **administratora globalnego.**

2.  Otwórz **rozszerzenia usług Azure Active Directory** klikając **wszystkie usługi** w górnej części menu nawigacji po lewej stronie głównej.

3.  Wpisz w **"Azure Active Directory**" w polu wyszukiwania filtr a wybierz **usługi Azure Active Directory** elementu.

4.  Kliknij przycisk **użytkowników i grup** w menu nawigacji.

5.  Kliknij przycisk **wszyscy użytkownicy**.

6.  **Wyszukiwanie** dla użytkownika planuje się i **kliknij wiersz** do wybrania.

7.  Kliknij przycisk **licencji** aby zobaczyć, które obecnie licencje użytkownika został przypisany.

### <a name="assign-a-user-a-license"></a>Przypisywanie licencji użytkownika 

Aby przypisać licencję do użytkownika, wykonaj następujące kroki:

1.  Otwórz [ **portalu Azure** ](https://portal.azure.com/) i zaloguj się jako **administratora globalnego.**

2.  Otwórz **rozszerzenia usług Azure Active Directory** klikając **wszystkie usługi** w górnej części menu nawigacji po lewej stronie głównej.

3.  Wpisz w **"Azure Active Directory**" w polu wyszukiwania filtr a wybierz **usługi Azure Active Directory** elementu.

4.  Kliknij przycisk **użytkowników i grup** w menu nawigacji.

5.  Kliknij przycisk **wszyscy użytkownicy**.

6.  **Wyszukiwanie** dla użytkownika planuje się i **kliknij wiersz** do wybrania.

7.  Kliknij przycisk **licencji** aby zobaczyć, które obecnie licencje użytkownika został przypisany.

8.  Kliknij przycisk **przypisać** przycisku.

9.  Wybierz **jeden lub więcej produktów** z listy dostępnych produktów.

10. **Opcjonalne** kliknij **opcje przydziału** element, aby przypisać częściami produktów. Kliknij przycisk **Ok** po zakończeniu.

11. Kliknij przycisk **przypisać** przycisk, aby przypisać licencje do tego użytkownika.

## <a name="problems-with-groups"></a>Problemy z grupy

Dostęp do aplikacji mogą zostać zablokowane z powodu problemu z grupy, który jest przypisany do aplikacji. Poniżej przedstawiono kilka sposobów, można rozwiązać i rozwiązać problemy z grupy i członkostwa w grupach:

-   [Sprawdź członkostwo w grupie](#check-a-groups-membership)

-   [Sprawdź kryteria członkostwa grupy dynamicznej](#check-a-dynamic-groups-membership-criteria)

-   [Sprawdź grupy przypisane licencje.](#check-a-groups-assigned-licenses)

-   [Ponownie przetworzyć grupy licencji](#reprocess-a-groups-licenses)

-   [Przypisz grupę licencji](#assign-a-group-a-license)

### <a name="check-a-groups-membership"></a>Sprawdź członkostwo w grupie

Aby sprawdzić członkostwa w grupie, wykonaj następujące kroki:

1.  Otwórz [ **portalu Azure** ](https://portal.azure.com/) i zaloguj się jako **administratora globalnego.**

2.  Otwórz **rozszerzenia usług Azure Active Directory** klikając **wszystkie usługi** w górnej części menu nawigacji po lewej stronie głównej.

3.  Wpisz w **"Azure Active Directory**" w polu wyszukiwania filtr a wybierz **usługi Azure Active Directory** elementu.

4.  Kliknij przycisk **użytkowników i grup** w menu nawigacji.

5.  Kliknij przycisk **wszystkich grup**.

6.  **Wyszukiwanie** dla grupy Użytkownicy zainteresowani i **kliknij wiersz** do wybrania.

7.  Kliknij przycisk **członków** Aby przejrzeć listę użytkowników przypisanych do tej grupy.

### <a name="check-a-dynamic-groups-membership-criteria"></a>Sprawdź kryteria członkostwa grupy dynamicznej 

Aby sprawdzić kryteria członkostwa grupy dynamicznej, wykonaj następujące kroki:

1.  Otwórz [ **portalu Azure** ](https://portal.azure.com/) i zaloguj się jako **administratora globalnego.**

2.  Otwórz **rozszerzenia usług Azure Active Directory** klikając **wszystkie usługi** w górnej części menu nawigacji po lewej stronie głównej.

3.  Wpisz w **"Azure Active Directory**" w polu wyszukiwania filtr a wybierz **usługi Azure Active Directory** elementu.

4.  Kliknij przycisk **użytkowników i grup** w menu nawigacji.

5.  Kliknij przycisk **wszystkich grup**.

6.  **Wyszukiwanie** dla grupy Użytkownicy zainteresowani i **kliknij wiersz** do wybrania.

7.  Kliknij przycisk **członkostwo dynamiczne reguły.**

8.  Przegląd **proste** lub **zaawansowane** reguły zdefiniowane dla tej grupy i upewnij się, że mają być członkami tej grupy użytkownika spełnia te kryteria.

### <a name="check-a-groups-assigned-licenses"></a>Sprawdź grupy przypisane licencje.

Aby sprawdzić grupy przypisane licencje, wykonaj następujące kroki:

1.  Otwórz [ **portalu Azure** ](https://portal.azure.com/) i zaloguj się jako **administratora globalnego.**

2.  Otwórz **rozszerzenia usług Azure Active Directory** klikając **wszystkie usługi** w górnej części menu nawigacji po lewej stronie głównej.

3.  Wpisz w **"Azure Active Directory**" w polu wyszukiwania filtr a wybierz **usługi Azure Active Directory** elementu.

4.  Kliknij przycisk **użytkowników i grup** w menu nawigacji.

5.  Kliknij przycisk **wszystkich grup**.

6.  **Wyszukiwanie** dla grupy Użytkownicy zainteresowani i **kliknij wiersz** do wybrania.

7.  Kliknij przycisk **licencji** aby zobaczyć, które obecnie licencje grupy został przypisany.

### <a name="reprocess-a-groups-licenses"></a>Ponownie przetworzyć grupy licencji

Aby ponownie przetworzyć grupy przypisane licencje, wykonaj następujące kroki:

1.  Otwórz [ **portalu Azure** ](https://portal.azure.com/) i zaloguj się jako **administratora globalnego.**

2.  Otwórz **rozszerzenia usług Azure Active Directory** klikając **wszystkie usługi** w górnej części menu nawigacji po lewej stronie głównej.

3.  Wpisz w **"Azure Active Directory**" w polu wyszukiwania filtr a wybierz **usługi Azure Active Directory** elementu.

4.  Kliknij przycisk **użytkowników i grup** w menu nawigacji.

5.  Kliknij przycisk **wszystkich grup**.

6.  **Wyszukiwanie** dla grupy Użytkownicy zainteresowani i **kliknij wiersz** do wybrania.

7.  Kliknij przycisk **licencji** aby zobaczyć, które obecnie licencje grupy został przypisany.

8.  Kliknij przycisk **ponownie przetworzyć** przycisk, aby upewnić się, że licencji przypisanych do członków tej grupy są aktualne. Może to zająć dużo czasu, w zależności od rozmiaru i złożoności grupy.

   >[!NOTE]
   >Aby szybciej to zrobić, należy wziąć pod uwagę tymczasowo bezpośrednio przypisywania licencji do użytkownika. [Przypisywanie licencji użytkownika](#problems-with-application-consent).
   >
   >

### <a name="assign-a-group-a-license"></a>Przypisz grupę licencji

Aby przypisać licencję do grupy, wykonaj następujące kroki:

1.  Otwórz [ **portalu Azure** ](https://portal.azure.com/) i zaloguj się jako **administratora globalnego.**

2.  Otwórz **rozszerzenia usług Azure Active Directory** klikając **wszystkie usługi** w górnej części menu nawigacji po lewej stronie głównej.

3.  Wpisz w **"Azure Active Directory**" w polu wyszukiwania filtr a wybierz **usługi Azure Active Directory** elementu.

4.  Kliknij przycisk **użytkowników i grup** w menu nawigacji.

5.  Kliknij przycisk **wszystkich grup**.

6.  **Wyszukiwanie** dla grupy Użytkownicy zainteresowani i **kliknij wiersz** do wybrania.

7.  Kliknij przycisk **licencji** aby zobaczyć, które obecnie licencje grupy został przypisany.

8.  Kliknij przycisk **przypisać** przycisku.

9.  Wybierz **jeden lub więcej produktów** z listy dostępnych produktów.

10. **Opcjonalne** kliknij **opcje przydziału** element, aby przypisać częściami produktów. Kliknij przycisk **Ok** po zakończeniu.

11. Kliknij przycisk **przypisać** przycisk, aby przypisać licencje do tej grupy. Może to zająć dużo czasu, w zależności od rozmiaru i złożoności grupy.

   >[!NOTE]
   >Aby szybciej to zrobić, należy wziąć pod uwagę tymczasowo bezpośrednio przypisywania licencji do użytkownika. [Przypisywanie licencji użytkownika](#problems-with-application-consent).
   > 
   >

## <a name="problems-with-conditional-access-policies"></a>Problemy z zasadami dostępu warunkowego

### <a name="check-a-specific-conditional-access-policy"></a>Sprawdzanie zasad określonych dostępu warunkowego

Aby sprawdzić lub zweryfikować zasady dostępu warunkowego pojedynczego:

1.  Otwórz [ **portalu Azure** ](https://portal.azure.com/) i zaloguj się jako **administratora globalnego.**

2.  Otwórz **rozszerzenia usług Azure Active Directory** klikając **wszystkie usługi** w górnej części menu nawigacji po lewej stronie głównej.

3.  Wpisz w **"Azure Active Directory**" w polu wyszukiwania filtr a wybierz **usługi Azure Active Directory** elementu.

4.  Kliknij przycisk **aplikacje dla przedsiębiorstw** w menu nawigacji.

5.  Kliknij przycisk **dostępu warunkowego** element nawigacji.

6.  Kliknij zasady, o których planuje się zapoznanie się.

7.  Sprawdź, czy ma żadnych określonych warunków, przydziałów lub innych ustawień, które mogą być blokowane dostępu użytkowników.

   >[!NOTE]
   >Możesz też chcieć tymczasowo wyłączyć tę zasadę, aby upewnić się, nie wpływa logowania. Aby to zrobić, ustaw **Włącz zasady** Przełącz, aby **nr** i kliknij przycisk **zapisać** przycisku.
   >
   >

### <a name="check-a-specific-applications-conditional-access-policy"></a>Sprawdź zasady dostępu warunkowego określonej aplikacji

Sprawdź lub sprawdzić poprawności pojedynczej aplikacji aktualnie skonfigurowane zasady dostępu warunkowego:

1.  Otwórz [ **portalu Azure** ](https://portal.azure.com/) i zaloguj się jako **administratora globalnego.**

2.  Otwórz **rozszerzenia usług Azure Active Directory** klikając **wszystkie usługi** w górnej części menu nawigacji po lewej stronie głównej.

3.  Wpisz w **"Azure Active Directory**" w polu wyszukiwania filtr a wybierz **usługi Azure Active Directory** elementu.

4.  Kliknij przycisk **aplikacje dla przedsiębiorstw** w menu nawigacji.

5.  Kliknij przycisk **wszystkie aplikacje**.

6.  Wyszukaj aplikację, która planuje się lub użytkownik próbuje zalogować się do nazwy wyświetlanej aplikacji lub identyfikator aplikacji.

     >[!NOTE]
     >Jeśli nie widzisz aplikacji, którego szukasz, kliknij przycisk **filtru** przycisk i zakres na liście, aby rozwinąć **wszystkie aplikacje**. Jeśli chcesz zobaczyć więcej kolumn, kliknij przycisk **kolumn** przycisk, aby dodać dodatkowe szczegóły dla aplikacji.
     >
     >

7.  Kliknij przycisk **dostępu warunkowego** element nawigacji.

8.  Kliknij zasady, o których planuje się zapoznanie się.

9.  Sprawdź, czy ma żadnych określonych warunków, przydziałów lub innych ustawień, które mogą być blokowane dostępu użytkowników.

     >[!NOTE]
     >Możesz też chcieć tymczasowo wyłączyć tę zasadę, aby upewnić się, nie wpływa logowania. Aby to zrobić, ustaw **Włącz zasady** Przełącz, aby **nr** i kliknij przycisk **zapisać** przycisku.
     >
     >

### <a name="disable-a-specific-conditional-access-policy"></a>Wyłącz zasady dostępu warunkowego określonych

Aby sprawdzić lub zweryfikować zasady dostępu warunkowego pojedynczego:

1.  Otwórz [ **portalu Azure** ](https://portal.azure.com/) i zaloguj się jako **administratora globalnego.**

2.  Otwórz **rozszerzenia usług Azure Active Directory** klikając **wszystkie usługi** w górnej części menu nawigacji po lewej stronie głównej.

3.  Wpisz w **"Azure Active Directory**" w polu wyszukiwania filtr a wybierz **usługi Azure Active Directory** elementu.

4.  Kliknij przycisk **aplikacje dla przedsiębiorstw** w menu nawigacji.

5.  Kliknij przycisk **dostępu warunkowego** element nawigacji.

6.  Kliknij zasady, o których planuje się zapoznanie się.

7.  Wyłącz zasady przez ustawienie **Włącz zasady** Przełącz, aby **nr** i kliknij przycisk **zapisać** przycisku.

## <a name="problems-with-application-consent"></a>Problemy z zgody aplikacji

Dostęp do aplikacji mogą zostać zablokowane, ponieważ nie przeprowadzono operacji zgody odpowiednie uprawnienia. Poniżej przedstawiono kilka sposobów umożliwiają rozwiązywanie problemów oraz rozwiązywaniu problemów zgody aplikacji:

-   [Wykonanie operacji zgody na poziomie użytkownika](#perform-a-user-level-consent-operation)

-   [Operacja zgody na poziomie administratora dla dowolnej aplikacji](#perform-administrator-level-consent-operation-for-any-application)

-   [Wykonaj zgody na poziomie administratora dla aplikacji pojedynczej dzierżawy](#perform-administrator-level-consent-for-a-single-tenant-application)

-   [Wykonaj zgody na poziomie administratora dla wielodostępnych aplikacji](#perform-administrator-level-consent-for-a-multi-tenant-application)

### <a name="perform-a-user-level-consent-operation"></a>Wykonanie operacji zgody na poziomie użytkownika

-   Dla dowolnego Open ID Connect aplikacja obsługująca żąda uprawnień przechodząc do rejestrowania aplikacji na ekranie wykonuje poziomu zgody użytkownika do aplikacji dla zalogowanego użytkownika.

-   Jeśli chcesz to zrobić programowo, zobacz [żąda zgody użytkownika](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes#requesting-individual-user-consent).

### <a name="perform-administrator-level-consent-operation-for-any-application"></a>Operacja zgody na poziomie administratora dla dowolnej aplikacji

-   Dla **tylko aplikacje opracowane za pomocą modelu aplikacji V1**, możesz wymusić występuje przez dodanie poziomu zgody użytkownika tego administratora "**? prompt = admin\_zgody**" na końcu Zaloguj się adres URL aplikacji.

-   Dla **wszelkie aplikacje opracowane za pomocą modelu aplikacji V2**, można wymusić tej zgody poziomie administratora nastąpić, postępując zgodnie z instrukcjami dotyczącymi **poprosić uprawnienia administratora usługi directory** sekcji [przy użyciu punktu końcowego zgody administratora](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes#using-the-admin-consent-endpoint).

### <a name="perform-administrator-level-consent-for-a-single-tenant-application"></a>Wykonaj zgody na poziomie administratora dla aplikacji pojedynczej dzierżawy

-   Dla **aplikacji pojedynczej dzierżawy** który zażądać uprawnień (np. te opracowujesz lub własny w organizacji), można wykonać **zgody na poziomie administracyjnym** operacji imieniu wszystkie użytkownikom zalogować się jako Administrator globalny i klikając **udzielić uprawnień** przycisk w górnej części **rejestru aplikacji -&gt; wszystkie aplikacje —&gt; wybierz aplikację -&gt; Wymagane uprawnienia** okienka.

-   Dla **wszelkie aplikacje opracowane za pomocą modelu aplikacji V1 lub V2**, można wymusić tej zgody poziomie administratora nastąpić, postępując zgodnie z instrukcjami dotyczącymi **poprosić uprawnienia administratora katalogu**  sekcji [przy użyciu punktu końcowego zgody administratora](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes#using-the-admin-consent-endpoint).

### <a name="perform-administrator-level-consent-for-a-multi-tenant-application"></a>Wykonaj zgody na poziomie administratora dla wielodostępnych aplikacji

-   Dla **aplikacje wielodostępne** tego zażądać uprawnień (np. aplikacji innej firmy lub firmy Microsoft, które zostały zaakceptowane), można wykonać **zgody na poziomie administracyjnym** operacji. Zaloguj się jako Administrator globalny i klikając **udzielić uprawnień** przycisku w obszarze **aplikacje przedsiębiorstwa -&gt; wszystkie aplikacje —&gt; wybierz aplikację -&gt; uprawnień**  okienka (dostępne wkrótce).

-   Można również wymusić tej zgody poziomie administratora nastąpić, postępując zgodnie z instrukcjami dotyczącymi **poprosić uprawnienia administratora katalogu** sekcji [przy użyciu punktu końcowego zgody administratora](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes#using-the-admin-consent-endpoint).

## <a name="next-steps"></a>Kolejne kroki
[Przy użyciu punktu końcowego zgody administratora](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes#using-the-admin-consent-endpoint)

