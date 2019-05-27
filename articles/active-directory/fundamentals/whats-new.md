---
title: Co nowego? Informacje o wersji — usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Aby dowiedzieć się o nowych za pomocą usługi Azure Active Directory, takie jak najnowszej wersji, znanych problemów i poprawek błędów przestarzałe funkcje i nadchodzących zmian.
services: active-directory
author: eross-msft
manager: daveba
featureFlags:
- clicktale
ms.assetid: 06a149f7-4aa1-4fb9-a8ec-ac2633b031fb
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 02/28/2019
ms.author: lizross
ms.reviewer: dhanyahk
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 74767e9d00f630efd2be026c3c3688c816c2ccee
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/23/2019
ms.locfileid: "66113358"
---
# <a name="whats-new-in-azure-active-directory"></a>What's new in Azure Active Directory?

>Otrzymaj powiadomienie o tym, kiedy Odwiedzaj tę stronę w celu aktualizacji przez kopiowanie i wklejanie tego adresu URL: `https://docs.microsoft.com/api/search/rss?search=%22release+notes+for+azure+AD%22&locale=en-us` do Twojej ![ikonę czytnik danych RSS](./media/whats-new/feed-icon-16x16.png) czytnik źródła.

Usługa Azure AD odbiera ulepszenia w sposób ciągły. Aby uzyskać najnowsze informacje o najnowszych zmianach, w tym artykule przedstawiono informacje o:

- Zainstalowane najnowsze wersje
- Znane problemy
- Poprawki błędów
- Przestarzałe funkcje
- Plany dotyczące zmian

Ta strona jest aktualizowany co miesiąc, więc ponownie regularnie. Jeśli szukasz dla elementów, które są starsze niż sześć miesięcy, możesz je znaleźć w [archiwizowania na potrzeby What's new in Azure Active Directory](whats-new-archive.md).

---

## <a name="april-2019"></a>2019 kwietnia

### <a name="azure-active-directory-azure-ad-entitlement-management-is-now-available-public-preview"></a>Zarządzanie uprawnieniami w usłudze Azure Active Directory (Azure AD) jest teraz dostępny (publiczna wersja zapoznawcza)

**Typ:** Nowa funkcja  
**Kategoria usług:** Nadzór nad tożsamościami  
**Możliwości produktu:** Nadzór nad tożsamościami

Zarządzanie uprawnieniami w usłudze Azure AD, teraz w publicznej wersji zapoznawczej, ułatwia klientom delegować Zarządzanie pakietów dostępu, który definiuje sposób pracownikom i partnerom biznesowym zażądać dostępu, który musi zatwierdzić i jak długo mają dostęp. Dostęp do pakietów można zarządzać, członkostwo w usłudze Azure AD i Office 365 grupy przypisań ról w aplikacjach dla przedsiębiorstw i przypisania roli dla witryn usługi SharePoint Online. Dowiedz się więcej o zarządzanie uprawnieniami w [Omówienie usługi Azure AD, zarządzanie uprawnieniami](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-overview). Aby dowiedzieć się więcej na temat zakresu funkcji zarządzania usługi Azure AD Identity, w tym Privileged Identity Management, przeglądy dostępu i użytkowania, zobacz [co to jest zarządzanie usługi Azure AD Identity?](../governance/identity-governance-overview.md).

---

### <a name="configure-a-naming-policy-for-office-365-groups-in-azure-ad-portal-public-preview"></a>Konfigurowanie zasad nazewnictwa dla grup usługi Office 365 w portalu usługi Azure AD (publiczna wersja zapoznawcza)

**Typ:** Nowa funkcja  
**Kategoria usług:** Zarządzanie grupami  
**Możliwości produktu:** Współpraca

Administratorzy mogą teraz konfigurować zasady nazewnictwa dla grup usługi Office 365, przy użyciu portalu usługi Azure AD. Ta zmiana pomaga wymusić spójne konwencje nazewnictwa dla grup usługi Office 365 utworzonych lub edytowanych przez użytkowników w Twojej organizacji. 

Zasady nazewnictwa dla grup usługi Office 365 można skonfigurować na dwa sposoby:

- Zdefiniuj prefiksów lub sufiksów, które są automatycznie dodawane do nazwy grupy.

- Przekaż dostosowany zestaw zablokowanych słowa dla Twojej organizacji, które nie są dozwolone w nazwach grupy (na przykład "wp list płac, Dyrektor Generalny").

Aby uzyskać więcej informacji, zobacz [wymusić zasady nazewnictwa dla grup usługi Office 365](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-naming-policy).

---

### <a name="azure-ad-activity-logs-are-now-available-in-azure-monitor-general-availability"></a>Dzienniki aktywności usługi AD platformy Azure są teraz dostępne w usłudze Azure Monitor (ogólnie)

**Typ:** Nowa funkcja  
**Kategoria usług:** Raportowanie  
**Możliwości produktu:** Monitorowanie i raportowanie

Do rozwiązywania swoją opinią na temat wizualizacji przy użyciu dzienników usługi Azure AD Activity, wprowadzamy nową funkcję szczegółowych informacji w usłudze Log Analytics. Ta funkcja ułatwia uzyskiwanie szczegółowych informacji o Twoich zasobów usługi Azure AD za pomocą naszych szablonów interaktywne, o nazwie skoroszytów. Tych wstępnie skompilowanych skoroszyty Podaj szczegóły dotyczące aplikacji lub użytkowników i obejmować:

- **Logowania.** Zawiera szczegółowe informacje dla aplikacji i użytkowników, w tym logowania w lokalizacji, systemu operacyjnego w użyciu lub klient przeglądarki i wersji oraz liczbę logowań powodzeniem lub niepowodzeniem.

- **Starsze uwierzytelnianie i dostęp warunkowy.** Zawiera szczegółowe informacje dla aplikacji i użytkowników za pomocą starszej wersji uwierzytelnianie, w tym użycie uwierzytelniania wieloskładnikowego wyzwalany przez zasady dostępu warunkowego, aplikacjami korzystającymi z zasad dostępu warunkowego i tak dalej.

- **Analiza niepowodzenia logowania.** Pomaga ustalić, jeśli występują błędy logowania, ze względu na akcję użytkownika, problemy dotyczące zasad lub infrastruktury.

- **Raporty niestandardowe.** Tworzenie nowych lub edytowanie istniejących skoroszytach ułatwia dostosowywanie funkcji szczegółowych informacji w organizacji.

Aby uzyskać więcej informacji, zobacz [jak używać usługi Azure Monitor skoroszyty dla raportów usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-use-azure-monitor-workbooks).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---april-2019"></a>Nowe aplikacje federacyjne dostępne w galerii aplikacji usługi Azure AD — 2019 kwietnia

**Typ:** Nowa funkcja  
**Kategoria usług:** Aplikacje dla przedsiębiorstw  
**Możliwości produktu:** Integracje innych firm

W kwietniu 2019 r Dodaliśmy obsługę tych 21 nowe aplikacje z Federacją w galerii aplikacji:

[Rozwiązaniu SAP Fiori](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-fiori-tutorial), [HRworks jednokrotnej](https://docs.microsoft.com/azure/active-directory/saas-apps/hrworks-single-sign-on-tutorial), [przesączyć](https://docs.microsoft.com/azure/active-directory/saas-apps/percolate-tutorial), [MobiControl](https://docs.microsoft.com/azure/active-directory/saas-apps/mobicontrol-tutorial), [Citrix NetScaler](https://docs.microsoft.com/azure/active-directory/saas-apps/citrix-netscaler-tutorial), [ Shibumi](https://docs.microsoft.com/azure/active-directory/saas-apps/shibumi-tutorial), [Benchling](https://docs.microsoft.com/azure/active-directory/saas-apps/benchling-tutorial), [MileIQ](https://mileiq.onelink.me/991934284/7e980085), [PageDNA](https://docs.microsoft.com/azure/active-directory/saas-apps/pagedna-tutorial), [EduBrite LMS](https://docs.microsoft.com/azure/active-directory/saas-apps/edubrite-lms-tutorial), [programu RStudio Połącz](https://docs.microsoft.com/azure/active-directory/saas-apps/rstudio-connect-tutorial), [AMMS](https://docs.microsoft.com/azure/active-directory/saas-apps/amms-tutorial), [połączyć Mitel](https://docs.microsoft.com/azure/active-directory/saas-apps/mitel-connect-tutorial), [firmę Alibaba w chmurze (Role-based SSO)](https://docs.microsoft.com/azure/active-directory/saas-apps/alibaba-cloud-service-role-based-sso-tutorial), [Certent kapitałowych, zarządzania](https://docs.microsoft.com/azure/active-directory/saas-apps/certent-equity-management-tutorial), [Menedżera certyfikatów Sectigo](https://docs.microsoft.com/azure/active-directory/saas-apps/sectigo-certificate-manager-tutorial), [GreenOrbit](https://docs.microsoft.com/azure/active-directory/saas-apps/greenorbit-tutorial), [Workgrid](https://docs.microsoft.com/azure/active-directory/saas-apps/workgrid-tutorial), [monday.com](https://docs.microsoft.com/azure/active-directory/saas-apps/mondaycom-tutorial), [ Enterprise usługę SurveyMonkey](https://docs.microsoft.com/azure/active-directory/saas-apps/surveymonkey-enterprise-tutorial), [Indiggo](https://indiggolead.com/)

Aby uzyskać więcej informacji o aplikacjach, zobacz [integracji aplikacji SaaS w usłudze Azure Active Directory](https://aka.ms/appstutorial). Aby uzyskać więcej informacji o ofercie swoją aplikację w galerii aplikacji Azure AD, zobacz [umieść swoją aplikację w galerii aplikacji usługi Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="new-access-reviews-frequency-option-and-multiple-role-selection"></a>Przeglądy dostępu w nowej opcji częstotliwości i wielokrotnego wyboru roli

**Typ:** Nowa funkcja  
**Kategoria usług:** Kontrole uprawnień dostępu  
**Możliwości produktu:** Nadzór nad tożsamościami

Nowe aktualizacje w usłudze Azure AD przeglądów dostępu pozwala na:

- Zmiana częstotliwości dostępu do usługi przeglądów **częściową annually**, oprócz uprzednio istniejące opcje co tydzień, co miesiąc, co kwartał i co roku.

- Wybieranie wielu usługi Azure AD i przejrzeć role zasobów platformy Azure, tworząc jednolity dostęp. W takiej sytuacji wszystkie role są konfigurowane przy użyciu tych samych ustawień, a wszyscy recenzenci są powiadamiani, w tym samym czasie.

Aby uzyskać więcej informacji o sposobie tworzenia przeglądu dostępu, zobacz [tworzenie przeglądu dostępu grup lub przeglądów dostępu dla aplikacji w usłudze Azure AD](https://docs.microsoft.com/azure/active-directory/governance/create-access-review).

---

### <a name="azure-ad-connect-email-alert-systems-are-transitioning-sending-new-email-sender-information-for-some-customers"></a>Przejście usługi Azure AD Connect poczty e-mail alertów systemy, wysyłając nowe informacje o nadawcy wiadomości e-mail dla niektórych klientów

**Typ:** Zmieniona funkcja  
**Kategoria usług:** AD Sync  
**Możliwości produktu:** Platforma

Program Azure AD Connect jest w trakcie przechodzenia nasze systemy alertów e-mail potencjalnie przedstawiający niektórzy klienci nowy nadawca wiadomości e-mail. Aby rozwiązać ten problem, należy dodać `azure-noreply@microsoft.com` lub listy dozwolonych w Twojej organizacji będą mogli nadal ważne alerty z usługi Office 365, Azure lub usługi synchronizacji.

---

### <a name="upn-suffix-changes-are-now-successful-between-federated-domains-in-azure-ad-connect"></a>Zmiany sufiks nazwy UPN są teraz pomyślne między domenami Sfederowane w programie Azure AD Connect

**Typ:** Ustalone  
**Kategoria usług:** AD Sync  
**Możliwości produktu:** Platforma

Możesz teraz pomyślnie zmienić sufiks nazwy UPN użytkownika z jednej domeny federacyjne do innej domeny federacyjne w programie Azure AD Connect. Ta poprawka oznacza, że nie jest już powinien wystąpić komunikat o błędzie FederatedDomainChangeError cyklu synchronizacji lub odbieranie powiadomień e-mail z informacją, "nie można zaktualizować tego obiektu w usłudze Azure Active Directory, ponieważ [atrybutu FederatedUser.UserPrincipalName] jest nieprawidłowa. Zaktualizuj tę wartość w lokalnych usługach katalogowych".

Aby uzyskać więcej informacji, zobacz [Rozwiązywanie problemów z błędami występującymi podczas synchronizacji](https://docs.microsoft.com/azure/active-directory/hybrid/tshoot-connect-sync-errors#federateddomainchangeerror).

---

### <a name="increased-security-using-the-app-protection-based-conditional-access-policy-in-azure-ad-public-preview"></a>Większe bezpieczeństwo, za pomocą zasad dostępu warunkowego na podstawie ochrony aplikacji w usłudze Azure AD (publiczna wersja zapoznawcza)

**Typ:** Nowa funkcja  
**Kategoria usług:** Dostęp warunkowy  
**Możliwości produktu:** Ochrona i zabezpieczanie tożsamości

Dostęp warunkowy oparty na ochronę aplikacji jest teraz dostępna za pomocą **wymagają ochrony aplikacji** zasad. Nowe zasady pomaga zwiększyć bezpieczeństwo w organizacji, zapobiegając:

- Użytkownicy, którzy mają dostęp do aplikacji bez licencji Microsoft Intune.

- Użytkownicy nie można pobrać zasad ochrony aplikacji Microsoft Intune.

- Użytkownicy, którzy mają dostęp do aplikacji bez skonfigurowanych zasad ochrony aplikacji Microsoft Intune.

Aby uzyskać więcej informacji, zobacz [sposób wymagać zasad ochrony aplikacji w celu uzyskania dostępu do aplikacji w chmurze przy użyciu dostępu warunkowego](https://docs.microsoft.com/azure/active-directory/conditional-access/app-protection-based-conditional-access).

---

### <a name="new-support-for-azure-ad-single-sign-on-and-conditional-access-in-microsoft-edge-public-preview"></a>Nowa funkcja obsługi usługi Azure AD pojedynczego logowania jednokrotnego i warunkowego dostępu w programie Microsoft Edge (publiczna wersja zapoznawcza)

**Typ:** Nowa funkcja  
**Kategoria usług:** Dostęp warunkowy  
**Możliwości produktu:** Ochrona i zabezpieczanie tożsamości

Ulepszyliśmy nasze obsługę usługi Azure AD dla programu Microsoft Edge, umieszczając nowa funkcja obsługi pojedynczego logowania jednokrotnego i warunkowego dostępu usługi Azure AD. Jeśli wcześniej używano Microsoft Intune Managed Browser, możesz teraz użyć Microsoft Edge.

Aby uzyskać więcej informacji na temat konfigurowania i zarządzania urządzeniami i aplikacjami przy użyciu dostępu warunkowego, zobacz [wymagają zarządzanych urządzeń, aby uzyskać dostęp do aplikacji w chmurze przy użyciu dostępu warunkowego](https://docs.microsoft.com/azure/active-directory/conditional-access/require-managed-devices) i [wymagają zatwierdzonych aplikacji klienckich dla chmury dostęp do aplikacji przy użyciu dostępu warunkowego](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access). Aby uzyskać więcej informacji o zarządzaniu dostępem przy użyciu Microsoft Edge zasady w usłudze Microsoft Intune, zobacz [zarządzanie dostępem do Internetu za pomocą usługi Microsoft Intune chronione przez zasady przeglądarki](https://docs.microsoft.com/intune/app-configuration-managed-browser).

---

## <a name="march-2019"></a>Marca 2019 r

### <a name="identity-experience-framework-and-custom-policy-support-in-azure-active-directory-b2c-is-now-available-ga"></a>Struktura środowiska tożsamości i zasad niestandardowych w usłudze Azure Active Directory B2C jest teraz dostępna (GA)

**Typ:** Nowa funkcja  
**Kategoria usług:** B2C — Consumer Identity Management  
**Możliwości produktu:** B2B/B2C

Teraz można utworzyć zasady niestandardowe w usłudze Azure AD B2C, w tym następujące czynności, które są obsługiwane na dużą skalę i w umowach SLA platformy Azure:

- Utwórz i przekaż podróży użytkownika uwierzytelniania niestandardowego za pomocą zasad niestandardowych.

- Opisz podróży użytkownika krok po kroku, jak wymiany między dostawców oświadczeń.

- Zdefiniuj rozgałęzienia warunkowe w podróży użytkownika.

- Przekształcanie i zamapuj oświadczenia do użycia w czasie rzeczywistym decyzje i komunikację.

- Użyj usług korzystających z interfejsu API REST w swojej podróży użytkownika uwierzytelniania niestandardowego. Na przykład za pomocą dostawców poczty e-mail, CRM i systemów własności autoryzacji.

- Utworzyć Federację z dostawcami tożsamości, którzy są zgodni z protokołu OpenIDConnect. Na przykład z wieloma dzierżawami usługi Azure AD, dostawców kont sieci społecznościowych lub dostawców weryfikacji two-Factor Authentication.

Aby uzyskać więcej informacji na temat tworzenia zasad niestandardowych, zobacz [uwagi dla deweloperów dla zasad niestandardowych w usłudze Azure Active Directory B2C](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-developer-notes-custom) i przeczytać [Alex Simon wpis w blogu, w tym przypadków](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-custom-policies-to-build-your-own-identity-journeys/ba-p/382791).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---march-2019"></a>Nowe aplikacje federacyjne dostępne w galerii aplikacji usługi Azure AD — marca 2019 r

**Typ:** Nowa funkcja  
**Kategoria usług:** Aplikacje dla przedsiębiorstw  
**Możliwości produktu:** Integracje innych firm

W marca 2019 r Dodaliśmy obsługę 14 nowych aplikacjach z Federacją w galerii aplikacji:

[Delegat Exchange Mobile ISEC7](https://www.isec7.com/english/), [MediusFlow](https://office365.cloudapp.mediusflow.com/), [ePlatform](https://docs.microsoft.com/azure/active-directory/saas-apps/eplatform-tutorial), [Fulcrum](https://docs.microsoft.com/azure/active-directory/saas-apps/fulcrum-tutorial), [ExcelityGlobal](https://docs.microsoft.com/azure/active-directory/saas-apps/excelityglobal-tutorial), [Oparte na wyjaśnienie systemu inspekcji](https://docs.microsoft.com/azure/active-directory/saas-apps/explanation-based-auditing-system-tutorial), [Odchudzonego](https://docs.microsoft.com/azure/active-directory/saas-apps/lean-tutorial), [sprawach wydajności Powerschool](https://docs.microsoft.com/azure/active-directory/saas-apps/powerschool-performance-matters-tutorial), [Cinode](https://cinode.com/), [irysów Intranet](https://docs.microsoft.com/azure/active-directory/saas-apps/iris-intranet-tutorial), [Empactis](https://docs.microsoft.com/azure/active-directory/saas-apps/empactis-tutorial), [SmartDraw](https://docs.microsoft.com/azure/active-directory/saas-apps/smartdraw-tutorial), [horyzonty Confirmit](https://docs.microsoft.com/azure/active-directory/saas-apps/confirmit-horizons-tutorial), [zadań](https://docs.microsoft.com/azure/active-directory/saas-apps/tas-tutorial)

Aby uzyskać więcej informacji o aplikacjach, zobacz [integracji aplikacji SaaS w usłudze Azure Active Directory](https://aka.ms/appstutorial). Aby uzyskać więcej informacji o ofercie swoją aplikację w galerii aplikacji Azure AD, zobacz [umieść swoją aplikację w galerii aplikacji usługi Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="new-zscaler-and-atlassian-provisioning-connectors-in-the-azure-ad-gallery---march-2019"></a>Nowe rozwiązania Zscaler i aprowizacji łączników w galerii usługi Azure AD — marca 2019 Atlassian

**Typ:** Nowa funkcja  
**Kategoria usług:** Aprowizowanie aplikacji  
**Możliwości produktu:** Integracje innych firm

Zautomatyzować tworzenie, aktualizowanie i usuwanie kont użytkowników dla następujących aplikacji:

[Zscaler](https://aka.ms/ZscalerProvisioning), [Zscaler Beta](https://aka.ms/ZscalerBetaProvisioning), [Zscaler One](https://aka.ms/ZscalerOneProvisioning), [Zscaler Two](https://aka.ms/ZscalerTwoProvisioning), [Zscaler Three](https://aka.ms/ZscalerThreeProvisioning), [Zscaler ZSCloud](https://aka.ms/ZscalerZSCloudProvisioning), [Atlassian Cloud](https://aka.ms/atlassianCloudProvisioning)

Aby uzyskać więcej informacji na temat lepiej zabezpieczyć Twoją organizację za pośrednictwem Inicjowanie obsługi administracyjnej konta użytkownika automatycznych, zobacz [automatyczne Inicjowanie obsługi użytkowników do aplikacji SaaS z usługą Azure AD](https://aka.ms/ProvisioningDocumentation).

---

### <a name="restore-and-manage-your-deleted-office-365-groups-in-the-azure-ad-portal"></a>Przywracanie i zarządzanie usuniętych grup usługi Office 365 w portalu usługi Azure AD

**Typ:** Nowa funkcja  
**Kategoria usług:** Zarządzanie grupami  
**Możliwości produktu:** Współpraca

Można teraz wyświetlać i zarządzać usuniętych grup usługi Office 365 z portalu usługi Azure AD. Ta zmiana ułatwia grupy, które są dostępne do przywrócenia, wraz z informacją o trwale usunąć wszystkie grupy, które nie są wymagane przez Twoją organizację.

Aby uzyskać więcej informacji, zobacz [przywracania wygasła lub usunąć grupy](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-restore-deleted#view-and-manage-the-deleted-office-365-groups-that-are-available-to-restore).

---

### <a name="single-sign-on-is-now-available-for-azure-ad-saml-secured-on-premises-apps-through-application-proxy-public-preview"></a>Logowanie jednokrotne jest teraz dostępny do aplikacji zabezpieczonej przez usługi Azure AD SAML w środowisku lokalnym za pośrednictwem serwera Proxy aplikacji (publiczna wersja zapoznawcza)

**Typ:** Nowa funkcja  
**Kategoria usług:** Serwer proxy aplikacji  
**Możliwości produktu:** Access Control

Teraz możesz podać środowisko logowania jednokrotnego (SSO), uwierzytelnianie SAML aplikacji lokalnych, wraz z zdalny dostęp do tych aplikacji za pośrednictwem serwera Proxy aplikacji. Aby uzyskać więcej informacji na temat sposobu konfigurowania logowania jednokrotnego SAML za pomocą aplikacji ze środowiska lokalnego, zobacz [SAML logowania jednokrotnego dla aplikacji w środowisku lokalnym dzięki serwerowi Proxy aplikacji (wersja zapoznawcza)](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-single-sign-on-on-premises-apps).

---

### <a name="client-apps-in-request-loops-will-be-interrupted-to-improve-reliability-and-user-experience"></a>Aplikacje klienckie w pętlach żądanie zostaną przerwane w celu zwiększenia niezawodności i środowisko użytkownika

**Typ:** Nowa funkcja  
**Kategoria usług:** Uwierzytelnienia (logowania)  
**Możliwości produktu:** Uwierzytelnianie użytkownika

Aplikacje klienckie mogą niepoprawnie wydać kilkuset żądań logowania w krótkim okresie. Te żądania czy zakończą się one pomyślnie lub nie, wszystkie udział niską komfortu i podwyższonym obciążeń dla dostawcy tożsamości, poprawia czas oczekiwania dla wszystkich użytkowników i zmniejsza dostępności przez dostawcę tożsamości.

Ta aktualizacja wysyła `invalid_grant` błąd: `AADSTS50196: The server terminated an operation because it encountered a loop while processing a request` do aplikacji klienta, które wysyłają żądania zduplikowane wielokrotnie w krótkim czasie poza zakres tematyczny normalnego działania. Aplikacje klienckie, które wystąpi ten problem powinien być wyświetlony monitu interakcyjnego, użytkownik musi zalogować się ponownie. Aby uzyskać więcej informacji na temat tej zmiany i o tym, jak naprawić aplikacji, jeśli wykryje nieważną tego błędu, zobacz [What's new do uwierzytelniania?](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#looping-clients-will-be-interrupted).

---

### <a name="new-audit-logs-user-experience-now-available"></a>Nowe środowisko użytkownika dzienników inspekcji teraz dostępne

**Typ:** Zmieniona funkcja  
**Kategoria usług:** Raportowanie  
**Możliwości produktu:** Monitorowanie i raportowanie

Utworzyliśmy nowej usługi Azure AD **dzienniki inspekcji** strony, aby zwiększyć czytelność i jak wyszukiwanie w celach informacyjnych. Aby zobaczyć nowe **dzienniki inspekcji** wybierz opcję **dzienniki inspekcji** w **działania** sekcji usługi Azure AD.

![Nowa strona dzienniki inspekcji, z użyciem informacji próbki](media/whats-new/audit-logs-page.png)

Aby uzyskać więcej informacji o nowym **dzienniki inspekcji** stronie, zobacz [raporty dotyczące inspekcji w portalu Azure Active Directory](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-audit-logs#audit-logs).

---

### <a name="new-warnings-and-guidance-to-help-prevent-accidental-administrator-lockout-from-misconfigured-conditional-access-policies"></a>Nowe ostrzeżenia i wskazówki, aby zapobiec przypadkowemu administrator blokady z nieprawidłowej konfiguracji zasad dostępu warunkowego

**Typ:** Zmieniona funkcja  
**Kategoria usług:** Dostęp warunkowy  
**Możliwości produktu:** Ochrona i zabezpieczanie tożsamości

Aby zapobiec Administratorzy przed zablokowaniem sobie przypadkowo poza dzierżawcom przy użyciu nieprawidłowej konfiguracji zasad dostępu warunkowego, utworzyliśmy nowe ostrzeżenia oraz zaktualizowano wskazówki w witrynie Azure portal. Aby uzyskać więcej informacji na temat nowych wytycznych, zobacz [zależności usług co to są usługi Azure Active Directory dostępu warunkowego](https://docs.microsoft.com/azure/active-directory/conditional-access/service-dependencies).

---

### <a name="improved-end-user-terms-of-use-experiences-on-mobile-devices"></a>Ulepszone przez użytkownika końcowego warunki Użyj środowiska na urządzeniach przenośnych

**Typ:** Zmieniona funkcja  
**Kategoria usług:** Warunki użytkowania  
**Możliwości produktu:** Ład

Zaktualizowaliśmy nasze istniejące warunki użycia środowiska do ulepszania sposobu przeglądu i wyrazić zgodę na warunki użytkowania na urządzeniu przenośnym. Teraz można powiększać i pomniejszać, przejdź wstecz, pobrać informacje i wybierz hiperłącza. Aby uzyskać więcej informacji na temat Zaktualizowano warunki użytkowania, zobacz [Azure Active Directory — warunki dotyczące użycia funkcji](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use#what-terms-of-use-looks-like-for-users).

---

### <a name="new-azure-ad-activity-logs-download-experience-available"></a>Nowe dzienniki usługi Azure AD Activity pobieranie najnowszych dostępnych

**Typ:** Zmieniona funkcja  
**Kategoria usług:** Raportowanie  
**Możliwości produktu:** Monitorowanie i raportowanie

Dużymi ilościami dzienników aktywności można teraz pobrać bezpośrednio z witryny Azure portal. Ta aktualizacja zapewnia następujące możliwości:

- Pobierz maksymalnie 250 000 wierszy.

- Otrzymaj powiadomienie po ukończeniu pobierania.

- Dostosuj nazwy pliku.

- Określ format danych wyjściowych, JSON lub CSV.

Aby uzyskać więcej informacji na temat tej funkcji, zobacz [Szybki Start: Pobierz raport dotyczący inspekcji w witrynie Azure portal](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-download-audit-report)

---

### <a name="breaking-change-updates-to-condition-evaluation-by-exchange-activesync-eas"></a>Zmiana powodująca niezgodność: Aktualizacje do oceny warunku przez program Exchange ActiveSync (EAS)

**Typ:** Plan zmiany  
**Kategoria usług:** Dostęp warunkowy  
**Możliwości produktu:** Access Control

Jesteśmy w trakcie aktualizowania, jak program Exchange ActiveSync (EAS) ocenia następujące warunki:

- Lokalizacja użytkownika, na podstawie kraju, regionu lub adres IP

- Ryzyko logowania

- Platforma urządzeń

Jeśli wcześniej używano tych warunków w zasady dostępu warunkowego, należy pamiętać, że zachowanie warunek może ulec zmianie. Na przykład jeśli wcześniej używano warunek lokalizacji użytkownika w zasadach, może się okazać zasad teraz pominięte zależy od lokalizacji użytkownika.

---

## <a name="february-2019"></a>Lutego 2019 r

### <a name="configurable-azure-ad-saml-token-encryption-public-preview"></a>Szyfrowanie tokenu SAML usługi Azure AD z możliwością konfigurowania (publiczna wersja zapoznawcza) 

**Typ:** Nowa funkcja  
**Kategoria usług:** Aplikacje dla przedsiębiorstw  
**Możliwości produktu:** Logowanie jednokrotne

Teraz można skonfigurować żadnej obsługiwanej aplikacji SAML, aby otrzymać zaszyfrowanych tokenów SAML. Skonfigurowane i używane z aplikacją, usługi Azure AD koduje emitowany twierdzenia SAML przy użyciu klucza publicznego otrzymane od certyfikatu przechowywanego w usłudze Azure AD.

Aby uzyskać więcej informacji o konfigurowaniu szyfrowania tokenu SAML, zobacz [szyfrowania tokenu Konfigurowanie usługi Azure AD SAML](https://docs.microsoft.com/azure/active-directory/manage-apps/howto-saml-token-encryption).

---

### <a name="create-an-access-review-for-groups-or-apps-using-azure-ad-access-reviews"></a>Tworzenie przeglądu dostępu dla grup i aplikacji za pomocą przeglądów dostępu usługi Azure AD

**Typ:** Nowa funkcja  
**Kategoria usług:** Kontrole uprawnień dostępu  
**Możliwości produktu:** Ład

Teraz możesz uwzględnić wiele grup lub aplikacji w pojedynczej usłudze Azure AD dostęp Przegląd dla członkostwa w grupie lub przypisaniem aplikacji. Przeglądy dostępu dotyczące dostępu z wieloma grupami lub aplikacje są konfigurowane przy użyciu tych samych ustawień i wszyscy recenzenci uwzględniane są powiadamiani, w tym samym czasie.

Aby uzyskać więcej informacji o tym, jak tworzenie przeglądu dostępu za pomocą przeglądów dostępu usługi Azure AD, zobacz [tworzenie przeglądu dostępu, grupy lub aplikacji w przeglądach dostępu usługi Azure AD](https://docs.microsoft.com/azure/active-directory/governance/create-access-review)

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---february-2019"></a>Nowe aplikacje federacyjne dostępne w galerii aplikacji Azure AD — luty 2019

**Typ:** Nowa funkcja  
**Kategoria usług:** Aplikacje dla przedsiębiorstw  
**Możliwości produktu:** Integracje innych firm
 
W lutym 2019 r Dodaliśmy obsługę tych 27 nowe aplikacje z Federacją w galerii aplikacji:

[Euromonitor Passport](https://docs.microsoft.com/azure/active-directory/saas-apps/euromonitor-passport-tutorial), [MindTickle](https://docs.microsoft.com/azure/active-directory/saas-apps/mindtickle-tutorial), [FAT FINGER](https://seeforgetest-exxon.azurewebsites.net/Account/create?Length=7), [AirStack](https://docs.microsoft.com/azure/active-directory/saas-apps/airstack-tutorial), [Fusion Oracle ERP i przeniesieniu jej](https://docs.microsoft.com/azure/active-directory/saas-apps/oracle-fusion-erp-tutorial), [ IDrive](https://docs.microsoft.com/azure/active-directory/saas-apps/idrive-tutorial), [Skyward Qmlativ](https://docs.microsoft.com/azure/active-directory/saas-apps/skyward-qmlativ-tutorial), [Brightidea](https://docs.microsoft.com/azure/active-directory/saas-apps/brightidea-tutorial), [AlertOps](https://docs.microsoft.com/azure/active-directory/saas-apps/alertops-tutorial), [logowania jednokrotnego Soloinsight CloudGate](https://docs.microsoft.com/azure/active-directory/saas-apps/soloinsight-cloudgate-sso-tutorial), Kliknij uprawnienie [Brandfolder](https://docs.microsoft.com/azure/active-directory/saas-apps/brandfolder-tutorial), [StoregateSmartFile](https://docs.microsoft.com/azure/active-directory/saas-apps/smartfile-tutorial), [Pexip](https://docs.microsoft.com/azure/active-directory/saas-apps/pexip-tutorial), [Stormboard](https://docs.microsoft.com/azure/active-directory/saas-apps/stormboard-tutorial), [podyktowane ](https://docs.microsoft.com/azure/active-directory/saas-apps/seismic-tutorial), [Marzenie udziału](https://www.shareadream.org/how-it-works), [Bugsnag](https://docs.microsoft.com/azure/active-directory/saas-apps/bugsnag-tutorial), [elementów WebMethod integracji chmury](https://docs.microsoft.com/azure/active-directory/saas-apps/webmethods-integration-cloud-tutorial), [wiedzy gdziekolwiek LMS](https://docs.microsoft.com/azure/active-directory/saas-apps/knowledge-anywhere-lms-tutorial), [Campus jednostki Organizacyjnej](https://docs.microsoft.com/azure/active-directory/saas-apps/ou-campus-tutorial), [danych w programie Periscope](https://docs.microsoft.com/azure/active-directory/saas-apps/periscope-data-tutorial), [Netop Portal](https://docs.microsoft.com/azure/active-directory/saas-apps/netop-portal-tutorial), [smartvid.io](https://docs.microsoft.com/azure/active-directory/saas-apps/smartvid.io-tutorial), [PureCloud przez Genesys](https://docs.microsoft.com/azure/active-directory/saas-apps/purecloud-by-genesys-tutorial), [ClickUp produktywności platformy](https://docs.microsoft.com/azure/active-directory/saas-apps/clickup-productivity-platform-tutorial)

Aby uzyskać więcej informacji o aplikacjach, zobacz [integracji aplikacji SaaS w usłudze Azure Active Directory](https://aka.ms/appstutorial). Aby uzyskać więcej informacji o ofercie swoją aplikację w galerii aplikacji Azure AD, zobacz [umieść swoją aplikację w galerii aplikacji usługi Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="enhanced-combined-mfasspr-registration"></a>Rozszerzona połączona rejestracja uwierzytelniania wieloskładnikowego lub samoobsługowego resetowania hasła

**Typ:** Zmieniona funkcja  
**Kategoria usług:** Samodzielne resetowanie hasła  
**Możliwości produktu:** Uwierzytelnianie użytkownika
 
W odpowiedzi na opinie klientów ulepszyliśmy połączone środowisko podglądu rejestracji MFA/samoobsługowego resetowania HASEŁ, pomaganie użytkownikom w taki sposób, aby szybciej zarejestrować swoje informacje zabezpieczające dla uwierzytelniania Wieloskładnikowego i samoobsługowego resetowania HASEŁ. 

**Aby włączyć już dziś udoskonalone środowisko dla użytkowników, wykonaj następujące kroki:**

1. Jako administrator globalny lub administrator użytkowników, zaloguj się do witryny Azure portal i przejdź do **usługi Azure Active Directory > Ustawienia użytkownika > Zarządzaj ustawieniami funkcji w wersji zapoznawczej panelu dostępu**. 

2. W **użytkowników, którzy mogą korzystać z wersji zapoznawczej funkcji rejestracji i zarządzania informacji zabezpieczających — odświeżanie** wybierz włączyć funkcje **wybraną grupę użytkowników** lub **wszystkich użytkowników** .

W ciągu kilku następnych tygodni będziemy usuwać możliwość włączyć stare połączone usługi MFA/środowiska samoobsługowego resetowania HASEŁ rejestracji (wersja zapoznawcza) w przypadku dzierżaw, które nie mają już ona włączona.

**Aby wyświetlić formant spowoduje usunięcie dzierżawy, wykonaj następujące kroki:**

1. Jako administrator globalny lub administrator użytkowników, zaloguj się do witryny Azure portal i przejdź do **usługi Azure Active Directory > Ustawienia użytkownika > Zarządzaj ustawieniami funkcji w wersji zapoznawczej panelu dostępu**.  

2. Jeśli **użytkowników, którzy mogą używać funkcji w wersji zapoznawczej dla rejestracji i zarządzania informacji zabezpieczających** opcja jest ustawiona na **Brak**, opcja zostaną usunięte z Twojej dzierżawy.

Niezależnie od tego, czy wcześniej włączona stare połączone rejestracji usługi MFA/samoobsługowego resetowania HASEŁ w wersji zapoznawczej środowisko dla użytkowników lub nie, stare środowisko, zostaną wyłączone w przyszłości. Z tego powodu zdecydowanie zalecamy możliwie jak przenieść do środowiska nowe, rozszerzone.

Aby uzyskać więcej informacji na temat środowiska rejestracji rozszerzone zobacz [ciekawe ulepszeń do usługi Azure AD MFA w połączeniu i proces rejestracji resetowania haseł](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Cool-enhancements-to-the-Azure-AD-combined-MFA-and-password/ba-p/354271).

---

### <a name="updated-policy-management-experience-for-user-flows"></a>Zaktualizowano środowisko zarządzania zasadami przepływów użytkownika

**Typ:** Zmieniona funkcja  
**Kategoria usług:** B2C — Consumer Identity Management  
**Możliwości produktu:** B2B/B2C

Zaktualizowaliśmy proces tworzenia i zarządzania zasad łatwiejsze przepływy użytkownika (wcześniej znane jako, wbudowane zasady). Nowe środowisko jest teraz domyślnie dla wszystkich dzierżaw usługi Azure AD.

Możesz podać dodatkowe opinie i sugestie przy użyciu uśmiech lub łuk w dół ikony w **Prześlij nam swoją opinię** u góry ekranu portalu.

Aby uzyskać więcej informacji na temat nowego środowiska zarządzania zasad, zobacz [usługi Azure AD B2C ma teraz Dostosowywanie języka JavaScript i wiele więcej nowych funkcji](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-now-has-JavaScript-customization-and-many-more-new/ba-p/353595) blogu.

---

### <a name="choose-specific-page-element-versions-provided-by-azure-ad-b2c"></a>Wybierz wersje elementów strony udostępnione przez usługę Azure AD B2C

**Typ:** Nowa funkcja  
**Kategoria usług:** B2C — Consumer Identity Management  
**Możliwości produktu:** B2B/B2C

Teraz można wybrać określoną wersję elementów strony udostępniane przez usługę Azure AD B2C. Wybierając określoną wersję, możesz przetestować aktualizacje przed pojawiają się na stronie, a uzyskasz zachowanie przewidywalne. Ponadto można teraz zgody na uczestnictwo w wymuszanie wersji określonej strony umożliwiające dostosowywanie języka JavaScript. Aby włączyć tę funkcję, przejdź do **właściwości** strony w swoich przepływach użytkowników.

Aby uzyskać więcej informacji na temat określonych wersji elementów na stronie Wybieranie zobacz [usługi Azure AD B2C ma teraz Dostosowywanie języka JavaScript i wiele więcej nowych funkcji](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-now-has-JavaScript-customization-and-many-more-new/ba-p/353595) blogu.

---

### <a name="configurable-end-user-password-requirements-for-b2c-ga"></a>Wymagania dotyczące hasła użytkownika końcowego z możliwością konfigurowania dla usługi B2C (ogólna dostępność)

**Typ:** Nowa funkcja  
**Kategoria usług:** B2C — Consumer Identity Management  
**Możliwości produktu:** B2B/B2C

Możesz teraz skonfigurować Twojej organizacji, złożoność hasła dla użytkowników końcowych, zamiast używać natywnego zasad haseł usługi Azure AD. Z **właściwości** bloku użytkownika przepływów (wcześniej znane jako wbudowanych zasad), możesz wybrać złożoność hasła **proste** lub **silne**, możesz też Tworzenie **niestandardowe** ustalony zbiór wymogów dotyczących.

Aby uzyskać więcej informacji na temat konfiguracji wymagań złożoności hasła, zobacz [konfigurowanie wymagań dotyczących złożoności haseł w usłudze Azure Active Directory B2C](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-password-complexity).

---

### <a name="new-default-templates-for-custom-branded-authentication-experiences"></a>Nowe szablony domyślne dla niestandardowych środowisk uwierzytelniania z oznakowaniem marki

**Typ:** Nowa funkcja  
**Kategoria usług:** B2C — Consumer Identity Management  
**Możliwości produktu:** B2B/B2C

Można użyć nasze nowe szablony domyślne, znajduje się na **strony układów** bloku przepływów użytkownika (wcześniej znane jako wbudowane zasady), aby utworzyć niestandardową marki środowisko uwierzytelniania dla użytkowników.

Aby uzyskać więcej informacji na temat korzystania z szablonów, zobacz [usługi Azure AD B2C ma teraz Dostosowywanie języka JavaScript i wiele więcej nowych funkcji](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-now-has-JavaScript-customization-and-many-more-new/ba-p/353595).

---

## <a name="january-2019"></a>Styczeń 2019

### <a name="active-directory-b2b-collaboration-using-one-time-passcode-authentication-public-preview"></a>Współpraca B2B w usłudze Active Directory przy użyciu uwierzytelniania za pomocą jednorazowego kodu dostępu (publiczna wersja zapoznawcza)

**Typ:** Nowa funkcja  
**Kategoria usług:** B2B  
**Możliwości produktu:** B2B/B2C

Wprowadziliśmy uwierzytelniania jednorazowy kod dostępu (OTP) dla użytkowników-gości B2B, którzy nie może zostać uwierzytelnione za pośrednictwem innych metod, takich jak usługa Azure AD, konta Microsoft (MSA) lub federacyjnych Google. Ta nowa metoda uwierzytelniania oznacza, że gościa, użytkownicy nie musieli utworzyć nowe konto Microsoft. Zamiast tego podczas realizacji zaproszenia, lub dostęp do udostępnionego zasobu, użytkownik-Gość mogą żądać tymczasowy kod do wysłania do adresu e-mail. Za pomocą tego tymczasowy kod, użytkownik-Gość można kontynuować Zaloguj się.

Aby uzyskać więcej informacji, zobacz [uwierzytelniania jednorazowy kod dostępu poczty E-mail (wersja zapoznawcza)](https://docs.microsoft.com/azure/active-directory/b2b/one-time-passcode) i blogu [usługi Azure AD sprawia, że udostępniania i współpracy bezproblemowe dla każdego użytkownika, przy użyciu dowolnego konta](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-makes-sharing-and-collaboration-seamless-for-any-user/ba-p/325949).

### <a name="new-azure-ad-application-proxy-cookie-settings"></a>Nowe ustawienia plików cookie serwera Proxy aplikacji usługi Azure AD

**Typ:** Nowa funkcja  
**Kategoria usług:** Serwer proxy aplikacji  
**Możliwości produktu:** Access Control

Wprowadziliśmy trzy nowe ustawienia plików cookie, dostępne dla Twoich aplikacji, które są publikowane za pośrednictwem serwera Proxy aplikacji:

- **Używaj plików cookie tylko do protokołu HTTP.** Zestawy **HTTPOnly** flagi na pliki cookie dostępu i sesji serwera Proxy aplikacji. Włączenie tego ustawienia zapewnia korzyści dodatkowe zabezpieczenia, takie jak pomaga w zapobieganiu kopiowanie lub modyfikowanie plików cookie, korzystając ze skryptów po stronie klienta. Zaleca się włączenie tej flagi (wybierz **tak**) Aby uzyskać dodatkowe korzyści.

- **Użyj bezpiecznego pliku cookie.** Zestawy **bezpiecznego** flagi na pliki cookie dostępu i sesji serwera Proxy aplikacji. Włączenie tego ustawienia zapewnia korzyści dodatkowe zabezpieczenia, upewniając się, że pliki cookie tylko są przesyłane za pośrednictwem kanałów bezpiecznego protokołu TLS, taki jak HTTPS. Zaleca się włączenie tej flagi (wybierz **tak**) Aby uzyskać dodatkowe korzyści.

- **Użyj trwały plik cookie.** Uniemożliwia dostęp do plików cookie wygasa po zamknięciu przeglądarki sieci web. Te pliki cookie trwać przez okres istnienia tokenu dostępu. Jednak pliki cookie są resetowane w przypadku osiągnięcia czas wygaśnięcia, lub jeśli użytkownik ręcznie usuwa plik cookie. Firma Microsoft zaleca, aby pozostawić domyślne ustawienie **nie**, tylko, włączając ustawienie starsze aplikacje, które nie mają plików cookie między procesami.

Aby uzyskać więcej informacji na temat nowych plików cookie, zobacz [ustawienia plików Cookie na potrzeby uzyskiwania dostępu do aplikacji lokalnych w usłudze Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-cookie-settings).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---january-2019"></a>Nowe aplikacje federacyjne dostępne w galerii aplikacji Azure AD — styczeń 2019

**Typ:** Nowa funkcja  
**Kategoria usług:** Aplikacje dla przedsiębiorstw  
**Możliwości produktu:** Integracje innych firm
 
W styczniu 2019 r Dodaliśmy obsługę tych 35 nowe aplikacje z Federacją w galerii aplikacji:

[Firstbird](https://docs.microsoft.com/azure/active-directory/saas-apps/firstbird-tutorial), [Folloze](https://docs.microsoft.com/azure/active-directory/saas-apps/folloze-tutorial), [palety Talent](https://docs.microsoft.com/azure/active-directory/saas-apps/talent-palette-tutorial), [Infor CloudSuite](https://docs.microsoft.com/azure/active-directory/saas-apps/infor-cloud-suite-tutorial), [parasola Cisco](https://docs.microsoft.com/azure/active-directory/saas-apps/cisco-umbrella-tutorial), [rozwiązania Zscaler Administrator dostępu do Internetu](https://docs.microsoft.com/azure/active-directory/saas-apps/zscaler-internet-access-administrator-tutorial), [przypomnienie o wygaśnięciu](https://docs.microsoft.com/azure/active-directory/saas-apps/expiration-reminder-tutorial), [podglądu InstaVR](https://docs.microsoft.com/azure/active-directory/saas-apps/instavr-viewer-tutorial), [CorpTax](https://docs.microsoft.com/azure/active-directory/saas-apps/corptax-tutorial), [zlecenie](https://app.verb.net/login), [OpenLattice](https://openlattice.com/agora), [TheOrgWiki](https://www.theorgwiki.com/signup), [Zamknij cyfrowy Pavaso](https://docs.microsoft.com/azure/active-directory/saas-apps/pavaso-digital-close-tutorial), [GoodPractice Toolkit](https://docs.microsoft.com/azure/active-directory/saas-apps/goodpractice-toolkit-tutorial), [ PICCO usługi w chmurze](https://docs.microsoft.com/azure/active-directory/saas-apps/cloud-service-picco-tutorial), [AuditBoard](https://docs.microsoft.com/azure/active-directory/saas-apps/auditboard-tutorial), [iProva](https://docs.microsoft.com/azure/active-directory/saas-apps/iprova-tutorial), [Workable](https://docs.microsoft.com/azure/active-directory/saas-apps/workable-tutorial), [CallPlease](https://webapp.callplease.com/create-account/create-account.html), [Systemu GTNexus SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/gtnexus-sso-module-tutorial), [CBRE ServiceInsight](https://docs.microsoft.com/azure/active-directory/saas-apps/cbre-serviceinsight-tutorial), [Deskradar](https://docs.microsoft.com/azure/active-directory/saas-apps/deskradar-tutorial), [Coralogixv](https://docs.microsoft.com/azure/active-directory/saas-apps/coralogix-tutorial), [Signagelive](https://docs.microsoft.com/azure/active-directory/saas-apps/signagelive-tutorial), [ARACH dla przedsiębiorstw](https://docs.microsoft.com/azure/active-directory/saas-apps/ares-for-enterprise-tutorial), [K2 dla usługi Office 365](https://www.k2.com/O365), [Xledger](https://www.xledger.net/), [iDiD Menedżera](https://docs.microsoft.com/azure/active-directory/saas-apps/idid-manager-tutorial), [HighGear](https://docs.microsoft.com/azure/active-directory/saas-apps/highgear-tutorial), [Visitly](https://docs.microsoft.com/azure/active-directory/saas-apps/visitly-tutorial), [ALP promy Korn](https://docs.microsoft.com/azure/active-directory/saas-apps/korn-ferry-alp-tutorial), [Acadia](https://docs.microsoft.com/azure/active-directory/saas-apps/acadia-tutorial), [cSaas Adoddle platformy](https://docs.microsoft.com/azure/active-directory/saas-apps/adoddle-csaas-platform-tutorial)<!-- , [CaféX Portal (Meetings)](https://docs.microsoft.com/azure/active-directory/saas-apps/cafexportal-meetings-tutorial), [MazeMap Link](https://docs.microsoft.com/azure/active-directory/saas-apps/mazemaplink-tutorial)-->  

Aby uzyskać więcej informacji o aplikacjach, zobacz [integracji aplikacji SaaS w usłudze Azure Active Directory](https://aka.ms/appstutorial). Aby uzyskać więcej informacji o ofercie swoją aplikację w galerii aplikacji Azure AD, zobacz [umieść swoją aplikację w galerii aplikacji usługi Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="new-azure-ad-identity-protection-enhancements-public-preview"></a>Nowe ulepszenia usługi Azure AD Identity Protection (publicznego wersja zapoznawcza)

**Typ:** Zmieniona funkcja  
**Kategoria usług:** Identity Protection  
**Możliwości produktu:** Ochrona i zabezpieczanie tożsamości

Cieszymy się, mogąc oferty publicznej wersji zapoznawczej usługi Azure AD Identity Protection, dodaliśmy następujące ulepszenia w tym:

- Interfejs użytkownika zaktualizowany i integracji

- Dodatkowe interfejsy API

- Ocena ryzyka ulepszone dzięki uczeniu maszynowemu

- Wyrównanie całego produktu ryzykownych użytkowników i ryzykownych logowań

Aby uzyskać więcej informacji na temat udoskonaleń dotyczących zobacz [co to jest Azure Active Directory Identity Protection (odświeżane)?](https://aka.ms/IdentityProtectionDocs) Aby dowiedzieć się więcej i dziel się pomysłami wskazówkami, w ramach produktu.

---

### <a name="new-app-lock-feature-for-the-microsoft-authenticator-app-on-ios-and-android-devices"></a>Nowa funkcja blokady aplikacji dla aplikacji Microsoft Authenticator na urządzeniach z systemami iOS i Android

**Typ:** Nowa funkcja  
**Kategoria usług:** Aplikacja Microsoft Authenticator  
**Możliwości produktu:** Ochrona i zabezpieczanie tożsamości

Aby zachować bezpieczniejsze jednorazowe kodów dostępu, informacje o aplikacji i ustawień aplikacji, można włączyć funkcję blokady aplikacji w aplikacji Microsoft Authenticator. Włączanie blokady aplikacji oznacza, że będziesz zadawane próbę uwierzytelnienia przy użyciu numeru PIN lub biometrycznych za każdym razem, gdy otworzysz aplikację Microsoft Authenticator.

Aby uzyskać więcej informacji, zobacz [aplikacji Microsoft Authenticator — często zadawane pytania](https://docs.microsoft.com/azure/active-directory/user-help/microsoft-authenticator-app-faq).

---

### <a name="enhanced-azure-ad-privileged-identity-management-pim-export-capabilities"></a>Rozszerzone funkcje eksportowania w usłudze Azure AD Privileged Identity Management (PIM)

**Typ:** Nowa funkcja  
**Kategoria usług:** Privileged Identity Management  
**Możliwości produktu:** Privileged Identity Management

Privileged Identity Management (PIM) administratorzy mogą teraz eksportować wszystkie przypisania roli aktywnych i kwalifikujących się do określonego zasobu, który zawiera przypisania roli dla wszystkich zasobów podrzędnych. Wcześniej było trudne dla administratorów uzyskać pełną listę przypisań ról w ramach subskrypcji i powodowały one na wyeksportowanie przypisań roli dla każdego określonego zasobu.

Aby uzyskać więcej informacji, zobacz [Wyświetl historię działań i inspekcji dla ról zasobów platformy Azure w usłudze PIM](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/azure-pim-resource-rbac).

---

## <a name="novemberdecember-2018"></a>Listopad/grudnia 2018 r.

### <a name="users-removed-from-synchronization-scope-no-longer-switch-to-cloud-only-accounts"></a>Użytkownicy usunięci z zakresu synchronizacji nie są już przełączani na konta tylko w chmurze

**Typ:** Ustalone  
**Kategoria usług:** Zarządzanie użytkownikami  
**Możliwości produktu:** Katalog

>[!Important]
>Wysłuchaliśmy i zrozumieć swoje Rozczarowanie ze względu na tę poprawkę. W związku z tym firma Microsoft został przywrócony tej zmiany do czasu, firma Microsoft może ułatwić poprawki można zaimplementować w Twojej organizacji.

Naprawiono usterkę, w którym flagi DirSyncEnabled użytkownika będzie błędnie przełącza się na **False** po wykluczony z zakresu synchronizacji i następnie przeniesiona do Kosza w obiektu usługi Active Directory Domain Services (AD DS) Usługa Azure AD na następujący cykl synchronizacji. Skutek tej poprawki, jeśli użytkownik jest wykluczony z zakresu synchronizacji, a potem przywrócić z usługi Azure AD Kosza, konto użytkownika pozostają zsynchronizowane z lokalnej usługi AD, zgodnie z oczekiwaniami i nie mogą być zarządzane w chmurze, ponieważ jego źródło SOA (adres) pozostaje jako lokalnej usługi AD.

Przed tej poprawki wystąpił problem podczas, gdy flaga DirSyncEnabled została przełączona na wartość False. Dała nieprawidłową wrażenie te konta były konwertowane na obiekty tylko w chmurze i kont mogą być zarządzane w chmurze. Jednak konta nadal przechowywane ich SoA w środowisku lokalnym oraz wszystkich zsynchronizowanych właściwości (atrybutów w tle) pochodzące z lokalnej usługi AD. Ten stan jest spowodowany wiele problemów w usłudze Azure AD i innych obciążeń w chmurze powinien traktować te konta synchronizowane z usługi Active Directory, które zostały teraz zachowuje się podobnie jak konta tylko w chmurze (np. Exchange Online).

W tej chwili jedynym sposobem, aby naprawdę Konwertowanie konta synchronizowane z AD konto tylko do chmury jest po wyłączeniu narzędzia DirSync na poziomie dzierżawy, co powoduje wyzwolenie operacji wewnętrznej bazy danych, aby przenieść SoA. Tego rodzaju zmiany SoA wymaga (ale nie jest ograniczona do) czyszczenia wszystkie lokalne powiązanych atrybutów (takie jak LastDirSyncTime i atrybuty w tle) i wysyła sygnał do innych obciążeń w chmurze do jego odpowiedniego obiektu przekonwertowane na konto tylko na chmurze za .

Ta poprawka w związku z tym uniemożliwia bezpośredni aktualizacji dla atrybutu wartość ImmutableID użytkownika synchronizowane z usługi AD, które w niektórych scenariuszach, w przeszłości były wymagane. Zgodnie z projektem wartość ImmutableID obiektu w usłudze Azure AD, jak wskazuje nazwa, jest przeznaczona do być niezmienialne. Zaimplementowane w kliencie usługi Azure AD Connect Health i synchronizacji usługi Azure AD Connect są dostępne nowe funkcje dla takich scenariuszy:

- **Na dużą skalę aktualizacji ImmutableID dla wielu użytkowników w podejście etapowe**
  
  Na przykład należy długich migrację między lasami usługi AD DS. Rozwiązanie: Użyj usługi Azure AD Connect, aby **Konfiguruj zakotwiczenie źródła** i użytkownik jest migrowana, Kopiuj istniejące wartości ImmutableID z usługi Azure AD na lokalnego użytkownika usług AD DS atrybut ms-DS-spójności — identyfikator Guid nowego lasu. Aby uzyskać więcej informacji, zobacz [przy użyciu ms-DS-ConsistencyGuid jako sourceAnchor](/azure/active-directory/hybrid/plan-connect-design-concepts#using-ms-ds-consistencyguid-as-sourceanchor).

- **Na dużą skalę aktualizacje ImmutableID dla wielu użytkowników w jednym działaniu**

  Na przykład podczas wdrażania usługi Azure AD Connect popełnisz błąd, a teraz musisz zmienić atrybut SourceAnchor. Rozwiązanie: Wyłącz narzędzie DirSync na poziomie dzierżawy, a następnie wyczyść wszystkie nieprawidłowe wartości ImmutableID. Aby uzyskać więcej informacji, zobacz [wyłączyć funkcję synchronizacji katalogów w usłudze Office 365](/office365/enterprise/turn-off-directory-synchronization).

- **Rematch użytkownika lokalnego przy użyciu istniejącego użytkownika w usłudze Azure AD** na przykład użytkownik, który został utworzony ponownie w usługach AD DS generuje duplikat na koncie usługi Azure AD zamiast rematching go z istniejącym kontem usługi Azure AD (obiektu oddzielonego). Rozwiązanie: Umożliwia usłudze Azure AD Connect Health w witrynie Azure portal ponownie zamapuj zakotwiczenie źródła/ImmutableID. Aby uzyskać więcej informacji, zobacz [scenariusza obiektu Orphaned](/azure/active-directory/hybrid/how-to-connect-health-diagnose-sync-errors#orphaned-object-scenario).

### <a name="breaking-change-updates-to-the-audit-and-sign-in-logs-schema-through-azure-monitor"></a>Zmiana powodująca niezgodność: Aktualizacje inspekcji i rejestrowania dzienników schematu za pomocą usługi Azure Monitor

**Typ:** Zmieniona funkcja  
**Kategoria usług:** Raportowanie  
**Możliwości produktu:** Monitorowanie i raportowanie

Firma Microsoft obecnie publikowania zarówno inspekcji i logowania strumienie dzienników za pośrednictwem usługi Azure Monitor, dzięki czemu można bezproblemowo zintegrować pliki dziennika z Twoimi narzędziami rozwiązania SIEM lub za pomocą usługi Log Analytics. Na podstawie opinii użytkowników, a w ramach przygotowania do ogłoszeniu ogólnej dostępności tej funkcji, wprowadzamy następujące zmiany do naszych schematu. Te zmiany schematu i jej powiązanej dokumentacji aktualizacji nastąpi w pierwszym tygodniu stycznia.

#### <a name="new-fields-in-the-audit-schema"></a>Nowe pola w schemacie inspekcji
Dodajemy nowy **typ operacji** pola, aby zapewnić typ operacji wykonywane w zasobie. Na przykład **Dodaj**, **aktualizacji**, lub **Usuń**.

#### <a name="changed-fields-in-the-audit-schema"></a>Zmienione pola w schemacie inspekcji
Następujące pola są zmiany w schemacie inspekcji:

|Nazwa pola|Co się zmieniło|Stare wartości|Nowe wartości|
|----------|------------|----------|----------|
|Category|To była **nazwa usługi** pola. Jest teraz **kategorie inspekcji** pola. **Nazwa usługi** została zmieniona na **loggedByService** pola.|<ul><li>Aprowizacja kont</li><li>Katalog podstawowy</li><li>Samoobsługowe Resetowanie hasła</li></ul>|<ul><li>Zarządzanie użytkownikami</li><li>Zarządzanie grupami</li><li>Zarządzanie aplikacjami</li></ul>|
|targetResources|Obejmuje **TargetResourceType** na najwyższym poziomie.|&nbsp;|<ul><li>Zasady</li><li>Aplikacja</li><li>Użytkownik</li><li>Grupa</li></ul>|
|loggedByService|Zawiera nazwę usługi, która wygenerowała dziennika inspekcji.|Null|<ul><li>Aprowizacja kont</li><li>Katalog podstawowy</li><li>Samoobsługowe resetowanie hasła</li></ul>|
|Wynik|Zwraca wynik funkcji dzienników inspekcji. Wcześniej był to wyliczenia, ale teraz pokazujemy wartości rzeczywistej.|<ul><li>0</li><li>1</li></ul>|<ul><li>Sukces</li><li>Niepowodzenie</li></ul>|

#### <a name="changed-fields-in-the-sign-in-schema"></a>Zmienione pola w schemacie logowania
Następujące pola są zmiany w schemacie logowania:

|Nazwa pola|Co się zmieniło|Stare wartości|Nowe wartości|
|----------|------------|----------|----------|
|appliedConditionalAccessPolicies|To była **conditionalaccessPolicies** pola. Jest teraz **appliedConditionalAccessPolicies** pola.|Bez zmian|Bez zmian|
|conditionalAccessStatus|Zwraca wynik funkcji o stanie zasad dostępu warunkowego przy logowaniu. Wcześniej był to wyliczenia, ale teraz pokazujemy wartości rzeczywistej.|<ul><li>0</li><li>1</li><li>2</li><li>3</li></ul>|<ul><li>Sukces</li><li>Niepowodzenie</li><li>Nie zastosowano</li><li>Wyłączono</li></ul>|
|appliedConditionalAccessPolicies: result|Zwraca wynik funkcji poszczególnych stan zasady warunkowego dostępu w podczas logowania. Wcześniej był to wyliczenia, ale teraz pokazujemy wartości rzeczywistej.|<ul><li>0</li><li>1</li><li>2</li><li>3</li></ul>|<ul><li>Sukces</li><li>Niepowodzenie</li><li>Nie zastosowano</li><li>Wyłączono</li></ul>|

Aby uzyskać więcej informacji na temat schematu, zobacz [inspekcji usługi Azure AD interpretację rejestruje schematu w usłudze Azure Monitor (wersja zapoznawcza)](https://docs.microsoft.com/azure/active-directory/reports-monitoring/reference-azure-monitor-audit-log-schema)

---

### <a name="identity-protection-improvements-to-the-supervised-machine-learning-model-and-the-risk-score-engine"></a>Ulepszenia usługi Identity Protection dotyczące nadzorowanego modelu uczenia maszynowego i aparatu oceny ryzyka

**Typ:** Zmieniona funkcja  
**Kategoria usług:** Identity Protection  
**Możliwości produktu:** Oceny ryzyka

Ulepszenia dotyczące ochrony tożsamości użytkownika i zaloguj się ryzyko oceny aparat może pomóc zwiększyć dokładność ryzyka użytkownika i pokrycia. Administratorzy mogą Zwróć uwagę, że poziom ryzyka użytkownika zostało już bezpośrednio połączone z poziomu ryzyka wykrywania określonych i czy jest wzrost liczba i poziom ryzykownych zdarzeń logowania.

Wykrywanie ryzyka, teraz są oceniane przez nadzorowanych modelu, który oblicza ryzyko związane z użytkownikiem za pomocą dodatkowych funkcji logowania użytkownika i wzorzec wykrywania uczenia maszynowego. Na podstawie tego modelu, administrator może okazać się użytkownikom ocen wysokiego ryzyka, nawet jeśli wykryć w skojarzonym z tym użytkownikiem zagrożenia na poziomie niskim lub średnim. 

---

### <a name="administrators-can-reset-their-own-password-using-the-microsoft-authenticator-app-public-preview"></a>Administratorzy mogą resetować swoje hasła przy użyciu aplikacji Microsoft Authenticator (publiczna wersja zapoznawcza)

**Typ:** Zmieniona funkcja  
**Kategoria usług:** Samodzielne resetowanie hasła  
**Możliwości produktu:** Uwierzytelnianie użytkownika

Administratorzy usługi Azure AD, mogą teraz zresetować swoje hasła przy użyciu powiadomienia w aplikacji Microsoft Authenticator lub kod z aplikacji mobilnej wystawcy uwierzytelnienia lub sprzęt tokenu. Aby zresetować własne hasło, Administratorzy teraz będą mogli korzystać z dwóch z następujących metod:

- Powiadomienie w aplikacji Microsoft Authenticator

- Innych aplikacji mobilnej wystawcy uwierzytelnienia / sprzętu token kodu

- Email

- Połączenie telefoniczne

- Wiadomość SMS

Aby uzyskać więcej informacji na temat korzystania z aplikacji Microsoft Authenticator resetowania haseł, zobacz [usługi Azure AD samoobsługowego resetowania haseł — aplikacji mobilnych i funkcji samoobsługowego resetowania HASEŁ (wersja zapoznawcza)](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-howitworks#mobile-app-and-sspr-preview)

---

### <a name="new-azure-ad-cloud-device-administrator-role-public-preview"></a>Nowa rola Administrator urządzeń w chmurze w usłudze Azure AD (publiczna wersja zapoznawcza)

**Typ:** Nowa funkcja  
**Kategoria usług:** Rejestracja urządzeń i zarządzanie nimi  
**Możliwości produktu:** Kontrola dostępu

Administratorzy mogą przypisywać użytkowników do nowej roli Administrator urządzenia w chmurze do wykonywania zadań administratora urządzenia w chmurze. Użytkownicy przypisani do roli Administratorzy urządzenia w chmurze można włączać, wyłączać i usuwać urządzenia w usłudze Azure AD oraz możliwość odczytu klucze funkcji BitLocker 10 systemu Windows (jeśli istnieje) w witrynie Azure portal.

Aby uzyskać więcej informacji na temat ról i uprawnień, zobacz [przypisywanie ról administratorów w usłudze Azure Active Directory](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)

---

### <a name="manage-your-devices-using-the-new-activity-timestamp-in-azure-ad-public-preview"></a>Zarządzanie urządzeniami przy użyciu nowego znacznika czasu aktywności w usłudze Azure AD (publiczna wersja zapoznawcza)

**Typ:** Nowa funkcja  
**Kategoria usług:** Rejestracja urządzeń i zarządzanie nimi  
**Możliwości produktu:** Zarządzanie cyklem życia urządzenia

Zdajemy sobie sprawę, że wraz z upływem czasu należy odświeżyć i wycofywania urządzeń w swojej organizacji w usłudze Azure AD pozwala uniknąć nieaktualne urządzenia w środowisku. Aby ułatwić ten proces, teraz usługi Azure AD aktualizuje urządzeń za pomocą nowa sygnatura czasowa działania pomaga zarządzać cyklu życia urządzenia.

Aby uzyskać więcej informacji na temat i korzystaj z tą sygnaturą czasową zobacz [How to: Zarządzanie starych urządzeniami w usłudze Azure AD](https://docs.microsoft.com/azure/active-directory/devices/manage-stale-devices)

---

### <a name="administrators-can-require-users-to-accept-a-terms-of-use-on-each-device"></a>Administratorzy mogą wymagać od użytkowników zaakceptowania warunków użytkowania na każdym urządzeniu

**Typ:** Nowa funkcja  
**Kategoria usług:** Warunki użytkowania  
**Możliwości produktu:** Ład
 
Administratorzy mogą włączyć **wymagać od użytkowników akceptacji na każdym urządzeniu** opcję użytkownikom zaakceptować warunki użytkowania na każdym urządzeniu używanych w dzierżawie.

Aby uzyskać więcej informacji, zobacz [warunków na poziomie urządzenia użyj postanowień dotyczących usługi Azure Active Directory użyj funkcji](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use#per-device-terms-of-use).

---

### <a name="administrators-can-configure-a-terms-of-use-to-expire-based-on-a-recurring-schedule"></a>Administratorzy mogą skonfigurować warunki użytkowania tak, aby wygasały zgodnie z harmonogramem cyklicznym

**Typ:** Nowa funkcja  
**Kategoria usług:** Warunki użytkowania  
**Możliwości produktu:** Ład
 

Administratorzy mogą włączyć **wygaśnie zgody** opcję warunki użytkowania wygaśnie dla wszystkich użytkowników na podstawie zgodnie z określonym harmonogramem cyklicznym. Harmonogram może być rocznie, bi rocznie, co kwartał i co miesiąc. Po wygaśnięciu warunki użytkowania, użytkownicy muszą akceptować.

Aby uzyskać więcej informacji, zobacz [warunki Użyj postanowień dotyczących usługi Azure Active Directory użyj funkcji Dodaj](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use#add-terms-of-use).

---

### <a name="administrators-can-configure-a-terms-of-use-to-expire-based-on-each-users-schedule"></a>Administratorzy mogą skonfigurować warunki użytkowania do wygaśnięcia na podstawie harmonogramu każdego użytkownika

**Typ:** Nowa funkcja  
**Kategoria usług:** Warunki użytkowania  
**Możliwości produktu:** Ład

Administratorzy mogą teraz określić czas trwania ten użytkownik musi ponownie zaakceptowali warunki użytkowania. Na przykład Administratorzy mogą określić, czy użytkownicy muszą ponownie zaakceptowali warunki użytkowania co 90 dni.

Aby uzyskać więcej informacji, zobacz [warunki Użyj postanowień dotyczących usługi Azure Active Directory użyj funkcji Dodaj](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use#add-terms-of-use).
 
---

### <a name="new-azure-ad-privileged-identity-management-pim-emails-for-azure-active-directory-roles"></a>Nowe wiadomości e-mail usługi Azure AD Privileged Identity Management (PIM) dla ról usługi Azure Active Directory

**Typ:** Nowa funkcja  
**Kategoria usług:** Privileged Identity Management  
**Możliwości produktu:** Privileged Identity Management
 
Klienci korzystający z usługi Azure AD Privileged Identity Management (PIM) mogą teraz otrzymać cotygodniowe podsumowanie wiadomości e-mail, w tym następujące informacje w ciągu ostatnich siedmiu dni:

- Omówienie przypisań najważniejszych ról uprawnionych i trwałych

- Liczba użytkowników aktywujących ról

- Liczba użytkowników przypisanych do ról w usłudze PIM

- Liczba użytkowników przypisanych do ról poza usługą PIM

- Liczba użytkowników "jako trwałe" w usłudze PIM

Aby uzyskać więcej informacji na temat PIM i powiadomienia e-mail dostępne zobacz [wiadomości E-mail z powiadomieniami w usłudze PIM](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-email-notifications).

---

### <a name="group-based-licensing-is-now-generally-available"></a>Licencjonowanie oparte na grupach jest teraz ogólnie dostępne

**Typ:** Zmieniona funkcja  
**Kategoria usług:** Inny  
**Możliwości produktu:** Katalog

Licencjonowanie na podstawie grupy jest poza publicznej wersji zapoznawczej i jest teraz ogólnie dostępna. W ramach tej wersji głównej ta funkcja została wprowadziliśmy bardziej skalowalna i dodano możliwość ponownego przetworzenia oparte na grupach przypisania licencji dla pojedynczego użytkownika i możliwość używania oparte na grupach licencji z licencjami pakietu Office 365 E3/A3.

Aby uzyskać więcej informacji na temat licencjonowania opartego na grupach, zobacz [co to jest oparte na grupach Licencjonowanie w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-licensing-whatis-azure-portal)

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---november-2018"></a>Nowe aplikacje federacyjne dostępne w galerii aplikacji usługi Azure AD — listopad 2018

**Typ:** Nowa funkcja  
**Kategoria usług:** Aplikacje dla przedsiębiorstw  
**Możliwości produktu:** Integracje innych firm
 
Na listopada 2018 Dodaliśmy obsługę 26 nowych aplikacjach z Federacją w galerii aplikacji:

[CoreStack](https://cloud.corestack.io/site/login), [HubSpot](https://docs.microsoft.com/azure/active-directory/saas-apps/HubSpot-tutorial), [GetThere](https://docs.microsoft.com/azure/active-directory/saas-apps/getthere-tutorial), [Pe gramatyczne](https://docs.microsoft.com/azure/active-directory/saas-apps/grape-tutorial), [eHour](https://getehour.com/try-now), [Consent2Go](https://docs.microsoft.com/azure/active-directory/saas-apps/Consent2Go-tutorial), [Appinux](https://docs.microsoft.com/azure/active-directory/saas-apps/appinux-tutorial), [DriveDollar](https://azuremarketplace.microsoft.com/marketplace/apps/savitas.drivedollar-azuread?tab=Overview), [Useall](https://docs.microsoft.com/azure/active-directory/saas-apps/useall-tutorial), [Campus nieskończonej](https://docs.microsoft.com/azure/active-directory/saas-apps/infinitecampus-tutorial), [Alaya](https://alayagood.com/en/demo/), [ HeyBuddy](https://docs.microsoft.com/azure/active-directory/saas-apps/heybuddy-tutorial), [Wrike SAML](https://docs.microsoft.com/azure/active-directory/saas-apps/wrike-tutorial), [dryfu](https://docs.microsoft.com/azure/active-directory/saas-apps/drift-tutorial), [Zenegy dla firm centralnej 365](https://accounting.zenegy.com/), [Everbridge elementu członkowskiego portalu](https://docs.microsoft.com/azure/active-directory/saas-apps/everbridge-tutorial), [IDEO](https://profile.ideo.com/users/sign_up), [Ivanti programu Service Manager (ISM)](https://docs.microsoft.com/azure/active-directory/saas-apps/ivanti-service-manager-tutorial), [Peakon](https://docs.microsoft.com/azure/active-directory/saas-apps/peakon-tutorial), [logowania jednokrotnego Allbound](https://docs.microsoft.com/azure/active-directory/saas-apps/allbound-sso-tutorial), [obiekt typu Plex aplikacje — Test klasycznego](https://test.plexonline.com/signon), [obiekt typu Plex aplikacji — Model Klasyczny](https://www.plexonline.com/signon), [obiekt typu Plex aplikacje — testowego UX](https://test.cloud.plex.com/sso), [obiekt typu Plex aplikacji — UX](https://cloud.plex.com/sso), [obiekt typu Plex aplikacje zarządzania tożsamościami i Dostępem](https://accounts.plex.com/), [RZEMIEŚLNICZYCH - uczestnictwa, System śledzenia danych finansowych i pielęgnacji rekordów](https://getcrafts.ca/craftsregistration) 

Aby uzyskać więcej informacji o aplikacjach, zobacz [integracji aplikacji SaaS w usłudze Azure Active Directory](https://aka.ms/appstutorial). Aby uzyskać więcej informacji o ofercie swoją aplikację w galerii aplikacji Azure AD, zobacz [umieść swoją aplikację w galerii aplikacji usługi Azure Active Directory](https://aka.ms/azureadapprequest).

---

## <a name="october-2018"></a>Październik 2018 r.

### <a name="azure-ad-logs-now-work-with-azure-log-analytics-public-preview"></a>Dzienniki usługi Azure AD działają teraz w usłudze Azure Log Analytics (publiczna wersja zapoznawcza)

**Typ:** Nowa funkcja  
**Kategoria usług:** Raportowanie  
**Możliwości produktu:** Monitorowanie i raportowanie

Cieszymy się, mogąc mogą obecnie przekazywać dzienników usługi Azure AD do usługi Azure Log Analytics! Najczęściej żądanych pomaga umożliwiają jeszcze lepsze dostęp do analizy dla Twojej firmy, operacji i zabezpieczeń, a także sposób, aby wspomóc monitorowanie infrastruktury. Aby uzyskać więcej informacji, zobacz [działanie usługi Azure Active Directory rejestruje się w usłudze Azure Log Analytics jest teraz dostępna](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-Active-Directory-Activity-logs-in-Azure-Log-Analytics-now/ba-p/274843) blogu.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---october-2018"></a>Nowe aplikacje federacyjne dostępne w galerii aplikacji usługi Azure AD — październik 2018

**Typ:** Nowa funkcja  
**Kategoria usług:** Aplikacje dla przedsiębiorstw  
**Możliwości produktu:** Integracje innych firm
 
Na października 2018 Dodaliśmy obsługę 14 nowych aplikacjach z Federacją w galerii aplikacji:

[Moje punkty Award](https://docs.microsoft.com/azure/active-directory/saas-apps/myawardpoints-tutorial), [Vibe HCM](https://docs.microsoft.com/azure/active-directory/saas-apps/vibehcm-tutorial), ambyint, [MyWorkDrive](https://docs.microsoft.com/azure/active-directory/saas-apps/myworkdrive-tutorial), [BorrowBox](https://docs.microsoft.com/azure/active-directory/saas-apps/borrowbox-tutorial), Dialpad, [środowiska wirtualnego ON24](https://docs.microsoft.com/azure/active-directory/saas-apps/on24-tutorial), [RingCentral](https://docs.microsoft.com/azure/active-directory/saas-apps/ringcentral-tutorial), [rozwiązania Zscaler trzech](https://docs.microsoft.com/azure/active-directory/saas-apps/zscaler-three-tutorial), [Phraseanet](https://docs.microsoft.com/azure/active-directory/saas-apps/phraseanet-tutorial), [Appraisd](https://docs.microsoft.com/azure/active-directory/saas-apps/appraisd-tutorial), [kontroli Workspot](https://docs.microsoft.com/azure/active-directory/saas-apps/workspotcontrol-tutorial), [Shuccho Navi](https://docs.microsoft.com/azure/active-directory/saas-apps/shucchonavi-tutorial), [Glassfrog](https://docs.microsoft.com/azure/active-directory/saas-apps/glassfrog-tutorial)

Aby uzyskać więcej informacji o aplikacjach, zobacz [integracji aplikacji SaaS w usłudze Azure Active Directory](https://aka.ms/appstutorial). Aby uzyskać więcej informacji o ofercie swoją aplikację w galerii aplikacji Azure AD, zobacz [umieść swoją aplikację w galerii aplikacji usługi Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="azure-ad-domain-services-email-notifications"></a>Powiadomienia e-mail usługi Azure AD Domain Services

**Typ:** Nowa funkcja  
**Kategoria usług:** Azure AD Domain Services  
**Możliwości produktu:** Azure AD Domain Services

Azure AD Domain Services udostępnia alerty w portalu Azure dotyczące błędów konfiguracji lub problemów z Twoją domeną zarządzaną. Te alerty zawierają przewodniki krok po kroku, dzięki czemu można spróbować rozwiązać problemy bez konieczności kontaktowania się z pomocą techniczną.

Począwszy od października, będzie można dostosować ustawienia powiadomień dla domeny zarządzanej, gdy nowe alerty występują, do wyznaczonej grupy osób, eliminując konieczność stale Sprawdź portal aktualizacji zostanie wysłana wiadomość e-mail.

Aby uzyskać więcej informacji, zobacz [ustawienia powiadomień w usługach domenowych Azure AD](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-notifications).

---

### <a name="azure-ad-portal-supports-using-the-forcedelete-domain-api-to-delete-custom-domains"></a>Portal usługi Azure AD obsługuje usuwanie domen niestandardowych przy użyciu interfejsu API domeny ForceDelete 

**Typ:** Zmieniona funkcja  
**Kategoria usług:** Zarządzanie katalogiem  
**Możliwości produktu:** Katalog

Mamy przyjemność ogłosić, że teraz można użyć domeny ForceDelete interfejsu API można usunąć nazwy domeny niestandardowej, asynchronicznie zmieniając nazwę odwołania, takich jak użytkownicy, grupy i aplikacje z niestandardowej nazwy domeny (contoso.com) do (nazwa domeny początkowej domyślnej contoso.onmicrosoft.com).

Ta zmiana pomaga szybciej usunąć nazwy domeny niestandardowej, czy Twoja organizacja już używa nazwy, czy należy użyć nazwy domeny za pomocą innej usługi Azure AD.

Aby uzyskać więcej informacji, zobacz [usuwanie nazwy domeny niestandardowej](https://docs.microsoft.com/azure/active-directory/users-groups-roles/domains-manage#delete-a-custom-domain-name).

---
