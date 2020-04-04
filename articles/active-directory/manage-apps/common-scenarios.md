---
title: Typowe scenariusze zarządzania aplikacjami dla usługi Azure Active Directory | Dokumenty firmy Microsoft
description: Scentralizowanie zarządzania aplikacjami za pomocą usługi Azure AD
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/02/2019
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: d21ff820470765b82e397e56a2458603b8e5a7c7
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80657951"
---
# <a name="centralize-application-management-with-azure-ad"></a>Scentralizowanie zarządzania aplikacjami za pomocą usługi Azure AD

Hasła, zarówno koszmar IT, jak i ból dla pracowników na całym świecie. Dlatego coraz więcej firm zwraca się do usługi Azure Active Directory, rozwiązania microsoftu do zarządzania tożsamościami i dostępem dla chmury i wszystkich innych zasobów. Przejdź z aplikacji do aplikacji bez konieczności wprowadzania hasła dla każdego z nich. Przejdź z Programu Outlook do Workday, do ADP tak szybko, jak można je otworzyć, szybko i bezpiecznie. Następnie współpracuj z partnerami, a nawet innymi osobami spoza organizacji, bez konieczności dzwonienia do IT. Co więcej, usługa Azure AD pomaga zarządzać ryzykiem, zabezpieczając aplikacje używane z uwierzytelnianiem wieloskładnikowym w celu weryfikacji tego, kim jesteś, przy użyciu stale adaptacyjnej analizy uczenia maszynowego i zabezpieczeń w celu wykrywania podejrzanych logów zapewniających bezpieczny dostęp do potrzebnych aplikacji, gdziekolwiek jesteś. Jest to nie tylko idealne rozwiązanie dla użytkowników, ale także dla it. Dzięki przeglądom dostępu just-in-time i pakietowi nadzoru pełnej skali usługa Azure AD pomaga zachować zgodność i egzekwować zasady. I dostać ten, można nawet zautomatyzować inicjowania obsługi administracyjnej kont użytkowników, dzięki czemu zarządzanie dostępem bardzo proste. zapoznaj się z niektórymi typowymi scenariuszami, dla których klient korzysta z funkcji zarządzania aplikacjami usługi Azure Active Directory.

**Typowe scenariusze**


> [!div class="checklist"]
> * SSO dla wszystkich aplikacji
> * Automatyzacja inicjowania obsługi administracyjnej i anulowania obsługi administracyjnej 
> * Zabezpiecz swoje aplikacje
> * Zarządzanie dostępem do aplikacji
> * Hybrydowy bezpieczny dostęp

## <a name="scenario-1-set-up-sso-for-all-your-applications"></a>Scenariusz 1: Konfigurowanie sylicy dla wszystkich aplikacji

Koniec z zarządzaniem hasłem. Bezpieczny dostęp do wszystkich potrzebnych zasobów za pomocą poświadczeń firmowych. 

|Funkcja  | Opis | Zalecenie |
|---------|---------|---------|
|Logowanie jednokrotne|SSO sycowe oparte na standardach opartych na standardach federacyjnego przy użyciu zaufanych standardów branżowych.|Zawsze używaj [SAML / OIDC,](https://docs.microsoft.com/azure/active-directory/manage-apps/isv-choose-multi-tenant-federation) aby włączyć funkcję SSO, gdy aplikacja go obsługuje.|
|Panel dostępu|Zaoferuj użytkownikom proste centrum do odnajdowania i uzyskiwania dostępu do wszystkich ich aplikacji. Zapewnij im większą produktywność dzięki funkcjom samoobsługowym, takim jak żądanie dostępu do aplikacji i grup lub zarządzanie dostępem do zasobów w imieniu innych osób.| Wdrażanie [panelu dostępu](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-deployment-plan) w organizacji po zintegrowaniu aplikacji z usługą Azure AD dla usługi SSO.|

## <a name="scenario-2-automate-provisioning-and-deprovisioning"></a>Scenariusz 2: Automatyzacja inicjowania obsługi administracyjnej i anulowania obsługi administracyjnej 


Większość aplikacji wymaga, aby użytkownik został aprowizny do aplikacji przed uzyskaniam dostępu do zasobów, które są potrzebne. Korzystanie z plików CSV lub złożonych skryptów może być kosztowne i trudne do zarządzania. Ponadto klienci muszą upewnić się, że konta są usuwane, gdy ktoś nie powinien mieć już dostępu. Skorzystaj z poniższych narzędzi, aby zautomatyzować aprowizacji i anulowania obsługi administracyjnej. 


|Funkcja  |Opis|Zalecenie |
|---------|---------|---------|
|Inicjowanie obsługi administracyjnej łańcucha przecięciowy|[SCIM](https://aka.ms/SICMOverview) jest najlepszym rozwiązaniem w branży do automatyzacji inicjowania obsługi administracyjnej użytkowników. Dowolną aplikację ze zgodnością z scim można zintegrować z usługą Azure AD. Automatyczne tworzenie, aktualizowanie i usuwanie kont użytkowników bez konieczności obsługi plików CSV, skryptów niestandardowych lub rozwiązań wstępnych.|Zapoznaj się z rosnącą listą [wstępnie zintegrowanych](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list) aplikacji w galerii aplikacji usługi Azure AD|
|Microsoft Graph|Wykorzystaj powiew i głębię danych, które usługa Azure AD musi wzbogacić aplikację o potrzebne dane.|Wykorzystaj [wykres firmy Microsoft,](https://developer.microsoft.com/graph/) aby uzyskać dane z całego ekosystemu firmy Microsoft. |


## <a name="scenario-3-secure-your-applications"></a>Scenariusz 3: Zabezpiecz swoje aplikacje
Tożsamość jest linchpin dla bezpieczeństwa. Jeśli tożsamość zostanie naruszona, niezwykle trudno jest zatrzymać efekt domina, zanim będzie za późno. Średnio ponad 100 dni mija, zanim organizacje odkryją, że doszło do kompromisu. Użyj narzędzi dostarczonych przez usługę Azure AD, aby poprawić poziom zabezpieczeń aplikacji. 

|Funkcja  |Opis| Zalecenie |
|---------|---------| ---------|
|Azure MFA|Azure Multi-Factor Authentication (MFA) to rozwiązanie firmy Microsoft służące do przeprowadzania weryfikacji dwuetapowej. Przy użyciu metod uwierzytelniania zatwierdzonych przez administratorów usługa Azure MFA pomaga chronić dostęp do danych i aplikacji, spełniając jednocześnie zapotrzebowanie na prosty proces logowania.| [Włącz usługę MFA](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/all-your-creds-are-belong-to-us/ba-p/855124) dla użytkowników.  |
|Dostęp warunkowy|Dzięki dostępowi warunkowemu można zaimplementować decyzje o automatycznej kontroli dostępu dla osób, które mogą uzyskiwać dostęp do aplikacji w chmurze na podstawie warunków.| Przejrzyj [ustawienia domyślne zabezpieczeń](https://docs.microsoft.com/azure/active-directory/fundamentals/concept-fundamentals-security-defaults) i [typowe zasady](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-policy-common) używane przez klientów. | 
|Identity Protection|Usługa Identity Protection korzysta z doświadczeń firmy Microsoft uzyskanych z ich pozycji w organizacjach korzystających z usługi Azure AD, przestrzeni konsumenckiej z kontami Microsoft oraz w grach z konsolą Xbox w celu ochrony użytkowników. Firma Microsoft analizuje 6,5 biliona sygnałów dziennie w celu identyfikacji i ochrony klientów przed zagrożeniami.|Włącz [domyślne zasady ochrony tożsamości](https://docs.microsoft.com/azure/active-directory/identity-protection/concept-identity-protection-policies) udostępniane przez naszą usługę. | 

## <a name="scenario-4-govern-access-to-your-applications"></a>Scenariusz 4: Zarządzanie dostępem do aplikacji
Zarządzanie tożsamościami pomaga organizacjom osiągnąć równowagę między wydajnością — jak szybko dana osoba może mieć dostęp do potrzebnych aplikacji, na przykład po dołączeniu do mojej organizacji? I bezpieczeństwo - Jak ich dostęp powinien się zmieniać w czasie, na przykład ze względu na zmiany statusu zatrudnienia tej osoby? 

|Funkcja  |Opis|Zalecenie |
|---------|---------| ---------|
|Elm|Zarządzanie uprawnieniami usługi Azure AD może pomóc użytkownikom zarówno w organizacji, jak i poza nią, w bardziej wydajnym zarządzaniu dostępem do ich aplikacji.| Zezwalaj osobom niebędącym administratorami na zarządzanie dostępem do aplikacji za pomocą [pakietów dostępu](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-access-package-first).|
|Przeglądy dostępu|Dostęp użytkownika do aplikacji można regularnie przeglądać, aby upewnić się, że tylko odpowiednie osoby mają stały dostęp.| [Przejrzyj dostęp](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview) do najbardziej poufnych aplikacji. |
|Log Analytics|Generowanie raportów o tym, kto uzyskuje dostęp do aplikacji i przechowywać je w wybranym narzędziu SIEM, aby skorelować dane między źródłami danych i w czasie.| Włącz [analizę dzienników](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-analyze-activity-logs-log-analytics) i skonfiguruj alerty o zdarzeniach krytycznych związanych z aplikacjami. |


## <a name="scenario-5-hybrid-secure-access"></a>Scenariusz 5: Hybrydowy bezpieczny dostęp
Tożsamość może być płaszczyzną sterowania tylko wtedy, gdy może łączyć wszystko w chmurze i aplikacjach lokalnych. Skorzystaj z narzędzi dostarczonych przez usługę Azure AD i jej partnerów, aby zabezpieczyć dostęp do aplikacji opartych na starszej pozycji.

|Funkcja  |Opis|Zalecenie |
|---------|---------|---------|
|Serwer proxy aplikacji|Obecnie pracownicy chcą pracować wydajnie w dowolnym miejscu i czasie, na dowolnym urządzeniu. Muszą uzyskać dostęp do aplikacji SaaS w chmurze i aplikacjach firmowych lokalnie. Serwer proxy aplikacji usługi Azure AD umożliwia ten niezawodny dostęp bez kosztownych i złożonych wirtualnych sieci prywatnych (VPN) lub stref zdemilitaryzowanych (DMZ).|Skonfiguruj [zdalny dostęp](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy) dla aplikacji przedwczesnych. |
|F5, Akamai, Zscaler|Korzystając z istniejącego kontrolera sieci i dostarczania, można łatwo chronić starsze aplikacje, które są nadal krytyczne dla procesów biznesowych, ale nie można było wcześniej chronić za pomocą usługi Azure AD. Prawdopodobnie masz już wszystko, czego potrzebujesz, aby rozpocząć ochronę tych aplikacji.| Korzystanie Z Akamai, Citrix, F5 lub Zscaler? Zapoznaj się z naszymi [wstępnie utworzonymi rozwiązaniami.](https://docs.microsoft.com/azure/active-directory/manage-apps/secure-hybrid-access) | 

## <a name="related-articles"></a>Pokrewne artykuły:

- [Zarządzanie aplikacjami](https://docs.microsoft.com/azure/active-directory/manage-apps/index)
- [Aprowizacja aplikacji](https://docs.microsoft.com/azure/active-directory/app-provisioning/user-provisioning)
- [Hybrydowy bezpieczny dostęp]()
- [Nadzór nad tożsamościami](https://docs.microsoft.com/azure/active-directory/governance/identity-governance-overview)
- [Platforma tożsamości firmy Microsoft](https://docs.microsoft.com/azure/active-directory/develop/v2-overview)
- [Bezpieczeństwo tożsamości](https://docs.microsoft.com/azure/active-directory/conditional-access/index)
