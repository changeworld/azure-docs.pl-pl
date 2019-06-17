---
title: Konfigurowanie zarządzania sesji uwierzytelniania przy użyciu usługi Azure Active Directory dostępu warunkowego
description: Dostosuj konfigurację sesji uwierzytelniania usługi Azure AD tym częstotliwość i przeglądarki trwałość sesji logowania użytkownika.
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
ms.openlocfilehash: e15cf9b2e10a581c72a5035b52be47c3e2c9dfda
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67112338"
---
# <a name="configure-authentication-session-management-with-conditional-access"></a>Konfigurowanie zarządzania sesji uwierzytelniania przy użyciu dostępu warunkowego

W przypadku złożonych wdrożeń w organizacji może być konieczności ograniczania sesji uwierzytelniania. Niektóre scenariusze mogą być następujące:

* Dostęp do zasobów z niezarządzanego lub udostępnionego urządzenia
* Dostęp do poufnych informacji z sieci zewnętrznej
* Użytkownicy o dużym znaczeniu
* Kluczowych aplikacji biznesowych

Mechanizmy kontroli dostępu warunkowego pozwalają tworzyć zasady, których platformą docelową wybrane przypadki użycia w Twojej organizacji, bez wywierania wpływu na wszystkich użytkowników.

Przed zagłębieniem się w szczegóły dotyczące sposobu konfigurowania zasad, Przeanalizujmy konfiguracji domyślnej.

## <a name="user-sign-in-frequency"></a>Częstotliwość logowania użytkownika

Zaloguj się częstotliwość określa okres czasu, zanim użytkownik zostanie poproszony o Zaloguj się ponownie podczas próby uzyskania dostępu do zasobu.

Domyślna konfiguracja usługi Azure Active Directory (Azure AD) podczas logowania użytkownika w obszarze częstotliwość jest oknem stopniowe 90 dni. Monitu o podanie poświadczeń często wydaje się być rozsądne niczego robić, ale może powieść: użytkownicy, którzy są uczone niezamierzonego wprowadzenia poświadczeń bez myśleć może dostarczać ich do monicie o poświadczenia złośliwe.

Może być dźwięku, zatrważającej nie zadawać na Zaloguj się ponownie przez 90 dni, w rzeczywistości naruszeniem zasad IT spowoduje to wycofanie sesji użytkownika. Przykłady obejmują (ale nie są ograniczone do) zmianę hasła, niezgodnego urządzenia lub wyłączenie konta. Można także jawnie [odwołać sesje użytkowników przy użyciu programu PowerShell](https://docs.microsoft.com/powershell/module/azuread/revoke-azureaduserallrefreshtoken?view=azureadps-2.0). Domyślna konfiguracja usługi Azure AD sprowadza się do "nie Poproś użytkowników o podanie poświadczeń, jeśli nie zmienił stan zabezpieczeń swoich sesji".

Ustawienie częstotliwości logowania działa z aplikacjami, które wdrożyły OATH2 lub OIDC protokołów zgodnie ze standardami. Większość Microsoft natywne aplikacje dla Windows, Mac i Mobile są zgodne z ustawieniem.

## <a name="persistence-of-browsing-sessions"></a>Stan trwały sesji przeglądania

Trwałe sesję umożliwia użytkownikom umożliwiającą pozostanie zalogowanym po zamknięciu i ponownym otwarciu ich okna przeglądarki.

Wartość domyślna usługi Azure AD dla trwałości sesji przeglądarki umożliwia użytkownikom na urządzeniach osobistych, aby wybrać, czy chcesz zachować sesję, pokazując "Pobytu zalogowany?" monit o po pomyślnym uwierzytelnieniu. Jeśli przeglądarki trwałość jest skonfigurowana w usługach AD FS, zgodnie ze wskazówkami zawartymi w artykule [pojedynczego logowania jednokrotnego ustawienia usług AD FS](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/ad-fs-single-sign-on-settings#enable-psso-for-office-365-users-to-access-sharepoint-online
), firma Microsoft jest zgodne z tą zasadą i utrzymują również sesji usługi AD platformy Azure. Możesz również określić, czy użytkownicy w Twojej dzierżawie, zobacz "Pobytu zalogowany?" Prompt, zmieniając odpowiednie ustawienie w okienku w portalu Azure, zgodnie ze wskazówkami zawartymi w artykule firmowe [Dostosuj swoją stronę logowania w usłudze Azure AD](../fundamentals/customize-branding.md).

## <a name="configuring-authentication-session-controls"></a>Konfigurowanie kontrolki sesji uwierzytelniania

Dostęp warunkowy jest możliwość usługi Azure AD Premium i wymaga licencji premium. Jeśli chcesz dowiedzieć się więcej na temat dostępu warunkowego, zobacz [co to jest dostęp warunkowy w usłudze Azure Active Directory?](overview.md#license-requirements)

> [!WARNING]
> Jeśli używasz [można skonfigurować okres istnienia tokenu](../develop/active-directory-configurable-token-lifetimes.md) są wyposażone w obecnie w publicznej wersji zapoznawczej, należy pamiętać, że firma Microsoft nie obsługuje tworzenia dwóch różnych zasad dla tej samej kombinacji aplikacji lub użytkownika: jeden z tej funkcji i inną z żądanymi funkcję można skonfigurować okres istnienia tokenu. Firma Microsoft planuje wycofanie funkcji można skonfigurować okres istnienia tokenu 1 listopada i zastąp go za pomocą funkcji zarządzania sesji uwierzytelniania dostępu warunkowego.  

### <a name="policy-1-sign-in-frequency-control"></a>Zasady 1: Kontrolka logowania częstotliwości

1. Utwórz nowe zasady
1. Wybierz wszystkie wymagane warunki środowiska klienta, w tym docelowej aplikacji w chmurze.

   > [!NOTE]
   > Zalecane jest, aby ustawić częstotliwość monitu równy uwierzytelniania dla klucza aplikacji Microsoft Office, takich jak Exchange Online i SharePoint Online dla najlepsze środowisko użytkownika.

1. Przejdź do **kontroli dostępu** > **sesji** i kliknij przycisk **częstotliwość logowania**
1. Wprowadź wymagane wartości dni i godziny w pierwszym polu tekstowym
1. Wybierz wartość **godzin** lub **dni** z listy rozwijanej
1. Zapisz zasady

![Zasady dostępu warunkowego skonfigurowane podczas logowania w obszarze częstotliwość](media/howto-conditional-access-session-lifetime/conditional-access-policy-session-sign-in-frequency.png)

W usłudze Azure AD zarejestrowanej Windows urządzenia logowanie do urządzenia jest traktowany jako monit. Na przykład po skonfigurowaniu logowania w obszarze częstotliwość 24 godziny dla aplikacji pakietu Office, użytkowników w usłudze Azure AD zarejestrowany Windows, urządzenia będą spełniały warunki logowania w zasadach częstotliwości, logując się do urządzenia i nie wyświetli ponownie podczas otwierania aplikacji pakietu Office.

Skonfigurowanie częstotliwość różnych logowania dla aplikacji internetowej innej, które są uruchomione w tej samej sesji przeglądarki najbardziej rygorystyczne zasady zostaną zastosowane do obu aplikacji, ponieważ wszystkie aplikacje uruchomione w tej samej sesji przeglądarki, Udostępnij token jednej sesji.

### <a name="policy-2-persistent-browser-session"></a>Zasady 2: Trwałe sesję

1. Utwórz nowe zasady
1. Wybierz wszystkie wymagane warunki.

   > [!NOTE]
   > Należy pamiętać, że ta kontrolka wymaga, aby wybierz pozycję "Wszystkie aplikacje w chmurze" jako warunek. Przeglądarki trwałość sesji jest kontrolowana przez tokenu sesji uwierzytelniania. Wszystkie karty w sesji przeglądarki Udostępnij token jednej sesji i w związku z tym wszystkie one muszą mieć stan trwałości.

1. Przejdź do **kontroli dostępu** > **sesji** i kliknij przycisk **sesji przeglądarki trwałego**
1. Wybierz wartość z listy rozwijanej
1. Zaoszczędzić zasad

![Zasady dostępu warunkowego skonfigurowane dla trwałego przeglądarki](media/howto-conditional-access-session-lifetime/conditional-access-policy-session-persistent-browser.png)

> [!NOTE]
> Spowoduje to zastąpienie trwałego konfiguracji sesji przeglądarki w funkcji dostępu warunkowego usługi Azure AD "pobytu zalogowany?" ustawienie w firmie znakowania okienku w portalu Azure dla tego samego użytkownika, jeśli obie zasady zostały skonfigurowane.

## <a name="validation"></a>Walidacja

Narzędzie warunkowej do symulowania logowania użytkownika do aplikacji docelowej i innych warunków, oparte na konfiguracji zasad. Kontrolki zarządzania sesji uwierzytelniania wyświetlany w wyniku tego narzędzia.

![Wyniki działania narzędzia warunkowego dostępu What If](media/howto-conditional-access-session-lifetime/conditional-access-what-if-tool-result.png)

## <a name="policy-deployment"></a>Wdrażanie zasad

Aby upewnić się, że zasad usługi działa zgodnie z oczekiwaniami, zalecanym najlepszym rozwiązaniem jest przetestowanie go przed udostępnieniem jej w środowisku produkcyjnym. W idealnym przypadku umożliwia to dzierżawa testowa Sprawdź, czy nowe zasady działa zgodnie z oczekiwaniami. Aby uzyskać więcej informacji, zobacz artykuł [najlepsze rozwiązania dotyczące dostępu warunkowego w usłudze Azure Active Directory](best-practices.md).

## <a name="next-steps"></a>Kolejne kroki

* Jeśli chcesz wiedzieć, jak skonfigurować zasady dostępu warunkowego, zobacz artykuł [wymagają usługi MFA dla określonych aplikacji przy użyciu usługi Azure Active Directory dostępu warunkowego](app-based-mfa.md).
* Jeśli wszystko jest gotowe do skonfigurowania zasad dostępu warunkowego dla danego środowiska, zobacz artykuł [najlepsze rozwiązania dotyczące dostępu warunkowego w usłudze Azure Active Directory](best-practices.md).
