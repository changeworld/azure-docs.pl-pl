---
title: Konfigurowanie zarządzania sesjami uwierzytelniania przy użyciu dostępu warunkowego Azure Active Directory
description: Dostosuj konfigurację sesji uwierzytelniania usługi Azure AD, w tym częstotliwość logowania użytkownika i trwałość sesji przeglądarki.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 04/26/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 05c93c9fe2b34ae3b87c44608cc5c5c8947ecc73
ms.sourcegitcommit: a0b37e18b8823025e64427c26fae9fb7a3fe355a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/25/2019
ms.locfileid: "68499853"
---
# <a name="configure-authentication-session-management-with-conditional-access"></a>Konfigurowanie zarządzania sesjami uwierzytelniania przy użyciu dostępu warunkowego

W złożonych wdrożeniach organizacje mogą wymagać ograniczenia sesji uwierzytelniania. Niektóre scenariusze mogą obejmować:

* Dostęp do zasobów z urządzenia niezarządzanego lub udostępnionego
* Dostęp do poufnych informacji z sieci zewnętrznej
* Użytkownicy o dużym wpływie
* Krytyczne aplikacje biznesowe

Kontrole dostępu warunkowego umożliwiają tworzenie zasad przeznaczonych dla konkretnych przypadków użycia w organizacji bez wywierania wpływu na wszystkich użytkowników.

Przed wprowadzeniem szczegółowych informacji na temat sposobu konfigurowania zasad Sprawdźmy konfigurację domyślną.

## <a name="user-sign-in-frequency"></a>Częstotliwość logowania użytkownika

Częstotliwość logowania określa czas, po którym użytkownik zostanie poproszony o ponowne zalogowanie przy próbie uzyskania dostępu do zasobu.

Domyślna konfiguracja Azure Active Directory (Azure AD) dla częstotliwości logowania użytkownika jest oknem kroczącym 90 dni. Monitowanie użytkowników o poświadczenia często wygląda na to, że jest to rozsądne działanie, ale może się to wiązać z atakami: Użytkownicy, którzy są przeszkoleni, aby wprowadzić swoje poświadczenia bez względu na to, że mogą przypadkowo dostarczyć je do złośliwego monitu o poświadczenia.

Może to spowodować, że dźwięk nie poprosił użytkownika o ponowne zalogowanie przez 90 dni, w rzeczywistości każde naruszenie zasad IT spowoduje odwołanie do tej sesji. Niektóre przykłady obejmują (ale nie ograniczone do) zmianę hasła, niezgodne urządzenie lub wyłączone konto. Możesz również jawnie [odwołać sesje użytkowników przy użyciu programu PowerShell](https://docs.microsoft.com/powershell/module/azuread/revoke-azureaduserallrefreshtoken?view=azureadps-2.0). Domyślna konfiguracja usługi Azure AD to "nie Monituj użytkowników o podanie swoich poświadczeń, jeśli zabezpieczenia stan ich sesji nie zostały zmienione".

Ustawienie częstotliwości logowania działa z aplikacjami, które mają wdrożone protokoły OAUTH2 lub OIDC zgodnie ze standardami. Większość aplikacji natywnych firmy Microsoft dla systemu Windows, komputerów Mac i urządzeń przenośnych jest zgodna z tym ustawieniem.

## <a name="persistence-of-browsing-sessions"></a>Trwałość sesji przeglądania

W trwałej sesji przeglądarki użytkownicy mogą pozostać zalogowani po zamknięciu i ponownym otwarciu okna przeglądarki.

Wartość domyślna usługi Azure AD na potrzeby trwałości sesji przeglądarki umożliwia użytkownikom na urządzeniach osobistych wybieranie, czy sesja ma być utrwalana, pokazując "pozostania w rejestracji". Monituj po pomyślnym uwierzytelnieniu. Jeśli trwałość przeglądarki jest skonfigurowana w AD FS przy użyciu wskazówek w artykule [AD FS ustawienia](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/ad-fs-single-sign-on-settings#enable-psso-for-office-365-users-to-access-sharepoint-online
)logowania jednokrotnego, firma Microsoft będzie przestrzegać tych zasad i utrzymywać również sesję usługi Azure AD. Możesz również określić, czy użytkownicy w dzierżawie zobaczą "pozostania w Twojej rejestracji"? Monituj o zmianę odpowiedniego ustawienia w okienku znakowanie firmowe w Azure Portal przy użyciu wskazówek w artykule [Dostosowywanie strony logowania usługi Azure AD](../fundamentals/customize-branding.md).

## <a name="configuring-authentication-session-controls"></a>Konfigurowanie kontrolek sesji uwierzytelniania

Dostęp warunkowy jest możliwością Azure AD — wersja Premium i wymaga licencji Premium. Jeśli chcesz dowiedzieć się więcej na temat dostępu warunkowego, zobacz [co to jest dostęp warunkowy w Azure Active Directory?](overview.md#license-requirements)

> [!WARNING]
> Jeśli używasz funkcji okresowego [istnienia tokenu](../develop/active-directory-configurable-token-lifetimes.md) , który jest obecnie w publicznej wersji zapoznawczej, pamiętaj, że nie obsługujemy tworzenia dwóch różnych zasad dla tej samej kombinacji użytkownika lub aplikacji: jeden z tą funkcją i inną z konfigurowalnym tokenem Funkcja okresu istnienia. Firma Microsoft planuje wycofać funkcję konfigurowalnego okresu istnienia tokenu 1 listopada i zamienić ją na funkcję zarządzania sesjami uwierzytelniania dostępu warunkowego.  

### <a name="policy-1-sign-in-frequency-control"></a>Zasady 1: Kontrola częstotliwości logowania

1. Utwórz nowe zasady
1. Wybierz wszystkie wymagane warunki dla środowiska klienta, w tym docelowe aplikacje w chmurze.

   > [!NOTE]
   > Zaleca się skonfigurowanie równej częstotliwości monitów uwierzytelniania dla Microsoft Office najważniejszych aplikacji, takich jak Exchange Online i SharePoint Online, w celu zapewnienia najlepszego środowiska użytkownika.

1. Przejdź do**sesji** **kontroli** > dostępu, a następnie kliknij pozycję **częstotliwość logowania**
1. Wprowadź wymaganą wartość dni i godziny w pierwszym polu tekstowym
1. Wybierz wartość **godzin** lub **dni** z listy rozwijanej
1. Zapisywanie zasad

![Zasady dostępu warunkowego skonfigurowane dla częstotliwości logowania](media/howto-conditional-access-session-lifetime/conditional-access-policy-session-sign-in-frequency.png)

Na zarejestrowanych urządzeniach z systemem Windows w usłudze Azure AD Zaloguj się na urządzeniu jako monit. Jeśli na przykład skonfigurowano częstotliwość logowania na 24 godziny dla aplikacji pakietu Office, użytkownicy usługi Azure AD zarejestrowane urządzenia z systemem Windows będą spełniać zasady częstotliwości logowania, logując się na urządzeniu i nie będą monitowani ponownie podczas otwierania aplikacji pakietu Office.

Jeśli skonfigurowano inną częstotliwość logowania dla różnych aplikacji sieci Web, które są uruchomione w tej samej sesji przeglądarki, najwyższe zasady zostaną zastosowane do obu aplikacji, ponieważ wszystkie aplikacje uruchomione w tej samej sesji przeglądarki współdzielą token sesji.

### <a name="policy-2-persistent-browser-session"></a>Zasady 2: Trwała sesja przeglądarki

1. Utwórz nowe zasady
1. Wybierz wszystkie wymagane warunki.

   > [!NOTE]
   > Należy pamiętać, że ta kontrolka wymaga wybrania opcji "wszystkie aplikacje w chmurze" jako warunek. Trwałość sesji przeglądarki jest kontrolowana przez token sesji uwierzytelniania. Wszystkie karty w sesji przeglądarki współdzielą jeden token sesji i w związku z tym wszystkie muszą mieć stan trwałości.

1. Przejdź do**sesji** **kontroli** > dostępu, a następnie kliknij pozycję **trwała sesja przeglądarki**
1. Wybierz wartość z listy rozwijanej
1. Zapisz zasady

![Zasady dostępu warunkowego skonfigurowane dla trwałej przeglądarki](media/howto-conditional-access-session-lifetime/conditional-access-policy-session-persistent-browser.png)

> [!NOTE]
> Trwała konfiguracja sesji przeglądarki w usłudze Azure AD dostęp warunkowy spowoduje zastąpienie "pozostać zalogowanym?" ustawienie w okienku znakowanie firmowe w Azure Portal dla tego samego użytkownika, jeśli skonfigurowano obie zasady.

## <a name="validation"></a>Weryfikacja

Za pomocą narzędzia do wykonywania symulacji Zasymuluj Logowanie użytkownika do aplikacji docelowej i innych warunków w zależności od sposobu skonfigurowania zasad. Kontrolki zarządzanie sesjami uwierzytelniania są wyświetlane w wyniku tego narzędzia.

![Wyniki narzędzia What If dostępu warunkowego](media/howto-conditional-access-session-lifetime/conditional-access-what-if-tool-result.png)

## <a name="policy-deployment"></a>Wdrażanie zasad

Aby upewnić się, że zasady działają zgodnie z oczekiwaniami, zalecanym najlepszym rozwiązaniem jest przetestowanie go przed wycofaniem do produkcji. Najlepiej użyć dzierżawy testowej, aby sprawdzić, czy nowe zasady działają zgodnie z oczekiwaniami. Aby uzyskać więcej informacji, zapoznaj się z [najlepszymi rozwiązaniami dotyczącymi dostępu warunkowego w Azure Active Directory](best-practices.md).

## <a name="next-steps"></a>Kolejne kroki

* Aby dowiedzieć się, jak skonfigurować zasady dostępu warunkowego, zapoznaj się z artykułem [Wymagaj uwierzytelniania wieloskładnikowego dla określonych aplikacji, Azure Active Directory dostępu warunkowego](app-based-mfa.md).
* Aby skonfigurować zasady dostępu warunkowego dla środowiska, zapoznaj się z [najlepszymi rozwiązaniami dotyczącymi dostępu warunkowego w Azure Active Directory](best-practices.md).
