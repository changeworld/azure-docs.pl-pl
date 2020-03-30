---
title: Konfigurowanie zarządzania sesjami uwierzytelniania — usługa Azure Active Directory
description: Dostosuj konfigurację sesji uwierzytelniania usługi Azure AD, w tym częstotliwość logowania użytkownika i trwałość sesji przeglądarki.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jlu, calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6e9c0c88064c00c97de7dc58a500910e81c04eef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79263285"
---
# <a name="configure-authentication-session-management-with-conditional-access"></a>Konfigurowanie zarządzania sesjami uwierzytelniania za pomocą dostępu warunkowego

W złożonych wdrożeniach organizacje mogą wymagać ograniczenia sesji uwierzytelniania. Niektóre scenariusze mogą obejmować:

* Dostęp do zasobów z urządzenia niezarządzanego lub udostępnionego
* Dostęp do poufnych informacji z sieci zewnętrznej
* Użytkownicy o dużym wpływie
* Krytyczne aplikacje biznesowe

Formanty dostępu warunkowego umożliwiają tworzenie zasad ukierunkowanych na określone przypadki użycia w organizacji bez wpływu na wszystkich użytkowników.

Przed zagłębieniem się w szczegóły dotyczące konfigurowania zasad, sprawdźmy konfigurację domyślną.

## <a name="user-sign-in-frequency"></a>Częstotliwość logowania użytkownika

Częstotliwość logowania określa okres, w którym użytkownik zostanie poproszony o ponowne zalogowanie się podczas próby uzyskania dostępu do zasobu.

Domyślna konfiguracja usługi Azure Active Directory (Azure AD) dla częstotliwości logowania użytkownika jest oknom kroczącym 90 dni. Proszenie użytkowników o poświadczenia często wydaje się rozsądną rzeczą do zrobienia, ale może backfire: użytkownicy, którzy są przeszkoleni, aby wprowadzić swoje poświadczenia bez myślenia może przypadkowo dostarczyć je do złośliwego monitu poświadczeń.

Może to brzmieć alarmująco, aby nie prosić użytkownika o zalogowanie się, w rzeczywistości każde naruszenie zasad IT spowoduje odwołanie sesji. Niektóre przykłady obejmują (ale nie są ograniczone do) zmianę hasła, niezgodne urządzenie lub konto wyłączyć. Można również jawnie [odwołać sesje użytkowników za pomocą programu PowerShell](/powershell/module/azuread/revoke-azureaduserallrefreshtoken?view=azureadps-2.0). Domyślna konfiguracja usługi Azure AD sprowadza się do "nie proś użytkowników o podanie poświadczeń, jeśli poziom zabezpieczeń ich sesji nie uległ zmianie".

Ustawienie częstotliwości logowania działa z aplikacjami, które zaimplementowały protokoły OAUTH2 lub OIDC zgodnie ze standardami. Większość natywnych aplikacji firmy Microsoft dla systemów Windows, Mac i Mobile, w tym następujące aplikacje sieci Web, jest zgodna z tym ustawieniem.

- Word, Excel, PowerPoint Online
- OneNote Online
- Office.com
- Portal administracyjny usługi O365
- Exchange Online
- Usługi SharePoint i OneDrive
- Klient internetowy zespołów
- Dynamics CRM Online
- Portal Azure

### <a name="user-sign-in-frequency-and-device-identities"></a>Częstotliwość logowania użytkownika i tożsamości urządzeń

Jeśli masz przyłączoną do usługi Azure AD, sprzężenie z hybrydową usługą Azure AD lub urządzenia zarejestrowane w usłudze Azure AD, gdy użytkownik odblokuje swoje urządzenie lub zaloguje się interaktywnie, to zdarzenie również spełni zasady dotyczące częstotliwości logowania. W poniższych 2 przykładach częstotliwość logowania użytkownika jest ustawiona na 1 godzinę:

Przykład 1:

- O godzinie 00:00 użytkownik loguje się do urządzenia przyłączanego do usługi Azure AD systemu Windows 10 i rozpoczyna pracę nad dokumentem przechowywanym w usłudze SharePoint Online.
- Użytkownik kontynuuje pracę nad tym samym dokumentem na swoim urządzeniu przez godzinę.
- O godzinie 01:00 użytkownik jest monitowany o ponowne zalogowanie się na podstawie wymagań dotyczących częstotliwości logowania w zasadach dostępu warunkowego skonfigurowanych przez administratora.

Przykład 2:

- O godzinie 00:00 użytkownik loguje się do urządzenia przyłączanego do usługi Azure AD systemu Windows 10 i rozpoczyna pracę nad dokumentem przechowywanym w usłudze SharePoint Online.
- O 00:30 użytkownik wstaje i robi sobie przerwę blokując swoje urządzenie.
- O 00:45 użytkownik powraca z przerwy i odblokowuje urządzenie.
- O godzinie 01:45 użytkownik jest monitowany o ponowne zalogowanie się na podstawie wymagań dotyczących częstotliwości logowania w zasadach dostępu warunkowego skonfigurowanych przez administratora od czasu ostatniego logowania o godzinie 00:45.

## <a name="persistence-of-browsing-sessions"></a>Trwałość sesji przeglądania

Trwała sesja przeglądarki pozwala użytkownikom pozostać zalogowanym po zamknięciu i ponownym otwarciu okna przeglądarki.

Domyślna trwałość sesji usługi Azure AD umożliwia użytkownikom na urządzeniach osobistych wybór, czy utrwalić sesję, wyświetlając opcję "Pozostań zalogowany?". monit po pomyślnym uwierzytelnieniu. Jeśli trwałość przeglądarki jest skonfigurowana w usługach AD FS przy użyciu wskazówek zawartych w artykule [Ustawienia logowania jednokrotnego usług AD FS,](/windows-server/identity/ad-fs/operations/ad-fs-single-sign-on-settings#enable-psso-for-office-365-users-to-access-sharepoint-online
)będziemy przestrzegać tej zasady i utrwalić sesję usługi Azure AD. Można również skonfigurować, czy użytkownicy w dzierżawie zobaczą "Pozostań zalogowany?" monituj, zmieniając odpowiednie ustawienie w okienku znakowania firmy w witrynie Azure portal, korzystając ze wskazówek [zawartych w artykule Dostosowywanie strony logowania usługi Azure AD](../fundamentals/customize-branding.md).

## <a name="configuring-authentication-session-controls"></a>Konfigurowanie formantów sesji uwierzytelniania

Dostęp warunkowy to usługa Azure AD Premium i wymaga licencji premium. Jeśli chcesz dowiedzieć się więcej o dostępie warunkowym, zobacz [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](overview.md#license-requirements)

> [!WARNING]
> Jeśli używasz [konfigurowalnej](../develop/active-directory-configurable-token-lifetimes.md) funkcji okresu istnienia tokenu obecnie w publicznej wersji zapoznawczej, należy pamiętać, że nie obsługujemy tworzenia dwóch różnych zasad dla tej samej kombinacji użytkownika lub aplikacji: jednej z tą funkcją, a drugiej z konfigurowalną funkcją okresu istnienia tokenu. Firma Microsoft planuje wycofać konfigurowalną funkcję okresu istnienia tokenu 1 maja 2020 r. i zastąpić ją funkcją zarządzania sesjami uwierzytelniania dostępu warunkowego.  

### <a name="policy-1-sign-in-frequency-control"></a>Zasady 1: Kontrola częstotliwości logowania

1. Tworzenie nowych zasad
1. Wybierz wszystkie wymagane warunki dla środowiska klienta, w tym docelowe aplikacje w chmurze.

   > [!NOTE]
   > Zaleca się ustawienie równej częstotliwości monitu uwierzytelniania dla kluczowych aplikacji pakietu Microsoft Office, takich jak Exchange Online i SharePoint Online, aby uzyskać najlepsze środowisko użytkownika.

1. Przejdź do**sesji** **kontroli dostępu** > i kliknij pozycję **Częstotliwość logowania**
1. Wprowadź wymaganą wartość dni i godzin w pierwszym polu tekstowym
1. Wybierz wartość **godziny** lub **dni** z listy rozwijanej
1. Zapisz swoje zasady

![Zasady dostępu warunkowego skonfigurowane do częstotliwości logowania](media/howto-conditional-access-session-lifetime/conditional-access-policy-session-sign-in-frequency.png)

Na urządzeniach z systemem Windows zarejestrowanymi usługami Azure AD logowania się do urządzenia jest uważany za monit. Jeśli na przykład skonfigurowano częstotliwość logowania do 24 godzin dla aplikacji pakietu Office, użytkownicy na urządzeniach z systemem Windows zarejestrowanymi w usłudze Azure AD będą spełniać zasady logowania, logując się na urządzeniu i nie zostaną ponownie monitowani podczas otwierania aplikacji pakietu Office.

Jeśli skonfigurowano inną częstotliwość logowania dla różnych aplikacji sieci web, które są uruchomione w tej samej sesji przeglądarki, najsurowsze zasady zostaną zastosowane do obu aplikacji, ponieważ wszystkie aplikacje uruchomione w tej samej sesji przeglądarki współużytkują token pojedynczej sesji.

### <a name="policy-2-persistent-browser-session"></a>Zasady 2: Trwała sesja przeglądarki

1. Tworzenie nowych zasad
1. Wybierz wszystkie wymagane warunki.

   > [!NOTE]
   > Należy pamiętać, że ten formant wymaga wybrania "Wszystkie aplikacje w chmurze" jako warunek. Trwałość sesji przeglądarki jest kontrolowana przez token sesji uwierzytelniania. Wszystkie karty w sesji przeglądarki współużytkują token pojedynczej sesji i dlatego wszystkie muszą współużytkuj stan trwałości.

1. Przejdź do**sesji** **kontroli dostępu** > i kliknij pozycję **Trwała sesja przeglądarki**
1. Wybieranie wartości z listy rozwijanej
1. Zapisz zasady

![Zasady dostępu warunkowego skonfigurowane dla przeglądarki trwałej](media/howto-conditional-access-session-lifetime/conditional-access-policy-session-persistent-browser.png)

> [!NOTE]
> Konfiguracja trwałej sesji przeglądarki w usłudze Azure AD Conditional Access zastąpi "Pozostań zalogowany?" ustawienie w okienku znakowania firmy w witrynie Azure portal dla tego samego użytkownika, jeśli skonfigurowano obie zasady.

## <a name="validation"></a>Sprawdzanie poprawności

Użyj narzędzia Co jeśli, aby symulować logowanie od użytkownika do aplikacji docelowej i inne warunki na podstawie sposobu skonfigurowania zasad. Kontrolki zarządzania sesjami uwierzytelniania są wyświetlane w wyniku narzędzia.

![Warunkowy dostęp Co zrobić, jeśli wyniki narzędzia](media/howto-conditional-access-session-lifetime/conditional-access-what-if-tool-result.png)

## <a name="policy-deployment"></a>Wdrażanie zasad

Aby upewnić się, że zasady działają zgodnie z oczekiwaniami, zalecaną najlepszą praktyką jest przetestowanie go przed wprowadzeniem go do produkcji. W idealnym przypadku należy użyć dzierżawy testowej, aby sprawdzić, czy nowe zasady działają zgodnie z oczekiwaniami. Aby uzyskać więcej informacji, zobacz artykuł [Najważniejsze wskazówki dotyczące dostępu warunkowego w usłudze Azure Active Directory](best-practices.md).

## <a name="next-steps"></a>Następne kroki

* Jeśli chcesz wiedzieć, jak skonfigurować zasady dostępu warunkowego, zobacz artykuł [Wymaganie usługi MFA dla określonych aplikacji z dostępem warunkowym usługi Azure Active Directory](app-based-mfa.md).
* Jeśli chcesz skonfigurować zasady dostępu warunkowego dla swojego środowiska, zobacz artykuł [Najważniejsze wskazówki dotyczące dostępu warunkowego w usłudze Azure Active Directory](best-practices.md).
