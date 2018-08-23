---
title: Podstawy zarządzania tożsamościami na platformie Azure | Microsoft Docs
description: Tożsamości w chmurze są teraz najlepszym sposobem na zachowanie kontroli i wglądu w to, w jaki sposób i kiedy użytkownicy uzyskują dostęp do firmowych danych i aplikacji.
keywords: ''
author: eross-msft
manager: mtillman
ms.reviewer: jsnow
ms.author: lizross
ms.date: 08/07/2018
ms.topic: overview
ms.prod: ''
ms.service: active-directory
ms.component: fundamentals
ms.technology: ''
ms.assetid: ''
ms.custom: it-pro
ms.openlocfilehash: 327cecd129befb56c33d7fcf2d59ee5b58a18549
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/18/2018
ms.locfileid: "42022562"
---
# <a name="fundamentals-of-azure-identity-management"></a>Podstawy zarządzania tożsamościami na platformie Azure

Coraz więcej firmowych zasobów cyfrowych jest używanych poza siecią firmową, w chmurze i na urządzeniach, dlatego posiadanie niezawodnego rozwiązania do zarządzania dostępem i tożsamościami w chmurze staje się koniecznością. Tożsamości w chmurze są teraz najlepszym sposobem na zachowanie kontroli i wglądu w to, w jaki sposób i kiedy użytkownicy uzyskują dostęp do firmowych danych i aplikacji.

Firma Microsoft już od ponad dziesięciu lat zabezpiecza tożsamości w chmurze, a teraz dzięki usłudze [Azure Active Directory (AD)](active-directory-whatis.md) te systemy zabezpieczeń są dostępne również dla Ciebie. Dzięki usłudze Azure AD administratorzy w przedsiębiorstwach mogą łatwo zwiększyć poziom odpowiedzialności użytkowników i administratorów, korzystając z lepszych zabezpieczeń i funkcji zarządzania niż kiedykolwiek wcześniej.

Usługa Azure AD — wersja Premium to rozwiązanie do zarządzania tożsamościami i dostępem w chmurze zawierające zaawansowane funkcje zabezpieczeń, które umożliwiają korzystanie z jednej bezpiecznej tożsamości dla wszystkich aplikacji, ochrony tożsamości (rozszerzoną dzięki systemowi [Microsoft Intelligence Security Graph](https://www.microsoft.com/security/intelligence)) oraz technologii Privileged Identity Management. To nie jest po prostu kolejne narzędzie do monitorowania i raportowania. Usługa Azure AD — wersja Premium chroni tożsamości użytkowników w czasie rzeczywistym i umożliwia tworzenie opartych na ryzyku zasad elastycznego dostępu, aby chronić dane organizacji.

Obejrzyj ten krótki film wideo, aby zapoznać się z omówieniem zarządzania tożsamościami i ochrony tożsamości w usłudze Azure AD:
>[!VIDEO https://www.youtube.com/embed/9LGIJ2-FKIM]

Firma Microsoft zapewnia nie tylko tożsamość umożliwiającą wszechstronny dostęp, ale również zestaw narządzi do automatyzacji, ochrony i zarządzania systemami IT w Twojej organizacji. Mimo dostępności chmury obliczeniowej nadal istnieje potrzeba wykonywania i kontrolowania zadań IT, takich jak obsługa zgłoszeń do działu pomocy technicznej w celu resetowania haseł użytkowników, zarządzanie grupami użytkowników oraz żądania aplikacji. Sytuację jeszcze bardziej komplikuje fakt, że pracownicy obecnie korzystają w pracy z własnych urządzeń i łatwo dostępnych aplikacji SaaS, co sprawia, że utrzymanie kontroli nad ich aplikacjami we wszystkich firmowych centrach danych i na publicznych platformach w chmurze staje się jeszcze większym wyzwaniem.

[!INCLUDE [identity](../../../includes/azure-ad-licenses.md)]

## <a name="connect-on-premises-active-directory-with-azure-ad-and-office-365"></a>Łączenie lokalnej usługi Active Directory z usługą Azure AD oraz Office 365
Organizacje, które dużo zainwestowały w lokalną usługę Active Directory, mogą uzupełnić te inwestycje o chmurę, integrując lokalne katalogi z usługą Azure AD w modelu [hybrydowego zarządzania tożsamościami](https://aka.ms/aadframework). Dzięki temu użytkownicy mogą pracować wydajniej, korzystając z jednej tożsamości przy uzyskiwaniu dostępu do zasobów, bez względu na lokalizację. To umożliwia użytkownikom i organizacjom korzystanie z logowania jednokrotnego w celu uzyskania dostępu zarówno do zasobów lokalnych, jak i usług w chmurze, na przykład Office 365.

Program [Azure AD Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect) to jedyne narzędzie potrzebne do wykonania integracji. Program Azure AD Connect posiada funkcje umożliwiające obsługę synchronizacji tożsamości i zastępuje starsze wersje narzędzi do integracji tożsamości, takie jak DirSync i Azure AD Sync. Program Azure AD Connect umożliwia zarządzanie tożsamościami i synchronizację zasobów lokalnych z usługą Azure AD za pośrednictwem następujących funkcji:

- Synchronizacja — ten składnik odpowiada za tworzenie użytkowników, grup i innych obiektów. Odpowiada także za zapewnienie zgodności informacji o tożsamości lokalnych użytkowników i grup z informacjami w chmurze. Można również włączyć funkcję zapisywania zwrotnego haseł, aby utrzymać synchronizację katalogów lokalnych, gdy użytkownik zaktualizuje swoje hasło w usłudze Azure AD.
- Uwierzytelnianie — gdy usługa Azure AD to Twoja nowa warstwa kontroli, uwierzytelnianie stanowi podstawę dostępu w chmurze. Wybór właściwej metody uwierzytelniania to kluczowa decyzja podczas konfigurowania rozwiązania tożsamości hybrydowej usługi Azure AD. Zapoznaj się z [tym przewodnikiem](https://aka.ms/auth-options), aby wybrać opcję uwierzytelniania w chmurze (Synchronizacja skrótów haseł/Uwierzytelnianie przekazywane) lub uwierzytelniania federacyjnego (AD FS) w swojej organizacji.
- Monitorowanie kondycji — składnik [Azure AD Connect Health](https://docs.microsoft.com/azure/active-directory/connect-health/active-directory-aadconnect-health) zapewnia zaawansowane monitorowanie z możliwością wyświetlania aktywności w centralnej lokalizacji w witrynie Azure Portal.

## <a name="increase-productivity-and-reduce-helpdesk-costs-with-self-service-and-single-sign-on-experiences"></a>Zwiększanie produktywności i obniżanie kosztów pomocy technicznej dzięki funkcjom samoobsługi i logowania jednokrotnego

Pracownicy są bardziej wydajni, gdy muszą zapamiętać tylko jedną nazwę użytkownika i jedno hasło oraz mają spójne środowisko pracy na każdym urządzeniu. Oszczędzają również czas, gdy mogą wykonywać zadania samodzielnie, na przykład [zresetować zapomniane hasło](https://docs.microsoft.com/azure/active-directory/active-directory-passwords) lub zażądać dostępu do aplikacji bez konieczności oczekiwania na pomoc techniczną.

Usługa Azure AD [rozszerza lokalną usługę Active Directory](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect) do chmury, umożliwiając użytkownikom korzystanie z podstawowego konta organizacji zarówno podczas korzystania z urządzeń przyłączonych do domeny i zasobów firmy, jak i wszelkich aplikacji internetowych i SaaS, których potrzebują do pracy. Użytkownicy nie muszą zapamiętywać kilku zestawów nazw użytkownika i haseł, a ponadto mogą automatycznie uzyskiwać (i tracić) dostęp do aplikacji na podstawie przynależności do grup w organizacji oraz tego, czy są pracownikami firmy. Możesz również kontrolować dostęp do aplikacji galerii lub własnych aplikacji lokalnych opracowanych i opublikowanych za pośrednictwem [serwera proxy aplikacji usługi Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-get-started).

## <a name="manage-and-control-access-to-corporate-resources"></a>Kontrola dostępu do zasobów firmy i zarządzanie nim
Rozwiązania do zarządzania tożsamościami i dostępem firmy Microsoft umożliwiają pracownikom działu IT ochronę dostępu do aplikacji i zasobów w firmowym centrum danych oraz w chmurze dzięki dodatkowym poziomom weryfikacji, takim jak [uwierzytelnianie wieloskładnikowe](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-whats-next) oraz [zasady dostępu warunkowego](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal). Monitorowanie podejrzanej aktywności przy użyciu zaawansowanych raportów zabezpieczeń, inspekcji i alertów umożliwia rozwiązywanie potencjalnych problemów z zabezpieczeniami.

Zasady dostępu warunkowego w usłudze Azure AD — wersja Premium dają administratorom w przedsiębiorstwach możliwość tworzenia reguł dostępu opartych na zasadach dla wszelkich aplikacji połączonych z usługą Azure AD (aplikacji SaaS, niestandardowych aplikacji w chmurze i lokalnych aplikacji internetowych). Usługa Azure AD ocenia te zasady w czasie rzeczywistym i wymusza ich stosowanie zawsze wtedy, gdy użytkownik próbuje uzyskać dostęp do aplikacji. Zasady ochrony tożsamości platformy Azure umożliwiają automatyczne podjęcie działań po wykryciu podejrzanej aktywności. To na przykład blokowanie dostępu użytkownikom o wysokim ryzyku, wymuszanie uwierzytelniania wieloskładnikowego oraz resetowanie haseł użytkowników, jeśli istnieje podejrzenie, że poświadczenia zostały naruszone.


## <a name="azure-active-directory-privileged-identity-management"></a>Azure Active Directory Privileged Identity Management

Funkcja [Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-getting-started), dostępna w ramach oferty Azure Active Directory — wersja Premium P2, umożliwia znajdowanie, ograniczanie i monitorowanie kont administratorów oraz ich dostępu do zasobów w usłudze Azure Active Directory oraz innych usługach online firmy Microsoft. Ułatwia ona również udzielanie dostępu administracyjnego na żądanie, dokładnie na taki czas, przez jaki jest potrzebny.

Funkcja Privileged Identity Management może obsługiwać udzielanie uprawnień administratora na żądanie, tak aby administratorzy mogli żądać, pod warunkiem uwierzytelnienia wieloskładnikowego, tymczasowego podniesienia swoich uprawnień na określony czas, po którym ich konto wróci do stanu normalnego użytkownika.

## <a name="benefits-of-azure-identity"></a>Korzyści z zarządzania tożsamościami na platformie Azure

Dzięki zarządzaniu tożsamościami na platformie Azure możesz:

-   Utworzyć jedną tożsamość dla jednego użytkownika w całej firmie i zarządzać nią, synchronizując użytkowników, grupy i urządzenia dzięki programowi [Azure Active Directory Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect).

-   Zapewnić dostęp przy użyciu logowania jednokrotnego do aplikacji, w tym do tysięcy wstępnie zintegrowanych aplikacji SaaS, lub zapewnić bezpieczny dostęp zdalny do lokalnych aplikacji SaaS przy użyciu [serwera proxy aplikacji usługi Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-get-started).

-   Zabezpieczyć dostęp do aplikacji przez wymuszenie [uwierzytelniania wieloskładnikowego](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-whats-next) na podstawie zasad zarówno dla aplikacji lokalnych, jak i dla aplikacji w chmurze.

-   Zwiększyć produktywność użytkowników dzięki [samoobsługowemu resetowaniu haseł](https://docs.microsoft.com/azure/active-directory/active-directory-passwords) oraz żądaniom dostępu do aplikacji i grup przy użyciu portalu [MyApps](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-user-help).

-   Skorzystać z [wysokiej dostępności i niezawodności](https://docs.microsoft.com/azure/architecture/resiliency/high-availability-azure-applications) globalnego rozwiązania klasy korporacyjnej umożliwiającego zarządzanie tożsamościami i dostępem w chmurze.

## <a name="next-steps"></a>Następne kroki
[Dowiedz się więcej na temat rozwiązań do obsługi tożsamości na platformie Azure](https://docs.microsoft.com/azure/active-directory/understand-azure-identity-solutions)
