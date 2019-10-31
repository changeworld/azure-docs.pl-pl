---
title: Co nowego w Azure Active Directory? | Microsoft Docs
description: Informacje o nowościach wersji w sekcji przegląd tego zestawu zawartości zawierają 6 miesięcy aktywności. Po 6 miesiącach elementy zostaną usunięte z głównego artykułu i umieszczone w tym artykule archiwum.
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 07/31/2019
ms.author: lizross
ms.reviewer: dhanyahk
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6fcf1e5966be7708e7c6278839cbfbe5d65cdffa
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2019
ms.locfileid: "73149131"
---
# <a name="archive-for-whats-new-in-azure-active-directory"></a>Co nowego w Azure Active Directory?

Podstawowe informacje o [nowościach w Azure Active Directory?](whats-new.md) artykuł dotyczący wersji zawiera aktualizacje dla ostatnich sześciu miesięcy, a ten artykuł zawiera wszystkie starsze dane.

Co nowego w Azure Active Directory? Informacje o wersji zawierają:

- Najnowsze wersje
- Znane problemy
- Poprawki błędów
- Przestarzałe funkcje
- Plany zmian

---

## <a name="april-2019"></a>Kwiecień 2019 r.

### <a name="new-azure-ad-threat-intelligence-detection-is-now-available-as-part-of-azure-ad-identity-protection"></a>Nowe wykrywanie analizy zagrożeń w usłudze Azure AD jest teraz dostępne w ramach Azure AD Identity Protection

**Typ:** Nowa funkcja  
**Kategoria usługi:** Azure AD Identity Protection  
**Możliwość produktu:** Ochrona tożsamości & zabezpieczenia

Funkcja wykrywania analizy zagrożeń w usłudze Azure AD jest teraz dostępna w ramach zaktualizowanej Azure AD Identity Protection funkcji. Ta nowa funkcja pomaga wskazać nietypowe działania użytkownika dla określonego użytkownika lub działania, które są zgodne ze znanymi wzorcami ataków na podstawie wewnętrznych i zewnętrznych źródeł analizy zagrożeń firmy Microsoft.

Aby uzyskać więcej informacji na temat odświeżonej wersji Azure AD Identity Protection, zobacz [cztery główne Azure AD Identity Protection ulepszenia znajdują się teraz w publicznym blogu w wersji zapoznawczej](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Four-major-Azure-AD-Identity-Protection-enhancements-are-now-in/ba-p/326935) , a [co to jest Azure Active Directory Identity Protection (odświeżane)?](https://docs.microsoft.com/azure/active-directory/identity-protection/overview-v2) art. Aby uzyskać więcej informacji o wykrywaniu analizy zagrożeń w usłudze Azure AD, zobacz artykuł dotyczący [wykrywania zagrożeń Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/identity-protection/risk-events-reference#azure-ad-threat-intelligence) .

---

### <a name="azure-ad-entitlement-management-is-now-available-public-preview"></a>Zarządzanie prawami w usłudze Azure AD jest teraz dostępne (publiczna wersja zapoznawcza)

**Typ:** Nowa funkcja  
**Kategoria usługi:** Zarządzanie tożsamościami  
**Możliwość produktu:** Zarządzanie tożsamościami

Zarządzanie prawami usługi Azure AD, teraz w publicznej wersji zapoznawczej, ułatwia klientom delegowanie zarządzania pakietami dostępu, które określają sposób, w jaki pracownicy i partnerzy biznesowi mogą żądać dostępu, którzy muszą zatwierdzić i jak długo mają dostęp. Pakiety dostępu mogą zarządzać członkostwem w grupach usługi Azure AD i Office 365, przypisaniach ról w aplikacjach dla przedsiębiorstw i przypisaniach ról dla witryn programu SharePoint Online. Przeczytaj więcej na temat zarządzania uprawnieniami w temacie [Omówienie zarządzania prawami usługi Azure AD](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-overview). Aby dowiedzieć się więcej o szerokiej części funkcji Azure AD Identity Governance, w tym o Privileged Identity Management, przeglądach dostępu i warunkach użytkowania, zobacz [co to jest Azure AD Identity Governance?](../governance/identity-governance-overview.md).

---

### <a name="configure-a-naming-policy-for-office-365-groups-in-azure-ad-portal-public-preview"></a>Konfigurowanie zasad nazewnictwa dla grup Office 365 w portalu usługi Azure AD (publiczna wersja zapoznawcza)

**Typ:** Nowa funkcja  
**Kategoria usługi:** Zarządzanie grupami  
**Możliwość produktu:** Społeczności

Administratorzy mogą teraz konfigurować zasady nazewnictwa dla grup pakietu Office 365 przy użyciu portalu usługi Azure AD. Ta zmiana pomaga wymusić spójne konwencje nazewnictwa dla grup pakietu Office 365 utworzonych lub edytowanych przez użytkowników w organizacji.

Zasady nazewnictwa dla grup pakietu Office 365 można skonfigurować na dwa różne sposoby:

- Zdefiniuj prefiksy lub sufiksy, które są automatycznie dodawane do nazwy grupy.

- Przekaż dostosowany zestaw zablokowanych słów dla swojej organizacji, które nie są dozwolone w nazwach grup (na przykład "dyrektor generalny, ListaPłac, HR").

Aby uzyskać więcej informacji, zobacz [wymuszanie zasad nazewnictwa dla grup pakietu Office 365](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-naming-policy).

---

### <a name="azure-ad-activity-logs-are-now-available-in-azure-monitor-general-availability"></a>Dzienniki aktywności usługi Azure AD są teraz dostępne w Azure Monitor (ogólna dostępność)

**Typ:** Nowa funkcja  
**Kategoria usługi:** Reporting  
**Możliwość produktu:** Monitorowanie & raportowania

Aby pomóc w rozwiązaniu opinii na temat wizualizacji z dziennikami aktywności usługi Azure AD, wprowadzamy nową funkcję Insights w Log Analytics. Ta funkcja ułatwia uzyskiwanie szczegółowych informacji o zasobach usługi Azure AD przy użyciu interaktywnych szablonów, nazywanych skoroszytami. Te wstępnie skompilowane skoroszyty mogą zawierać szczegóły dotyczące aplikacji lub użytkowników, a także:

- **Logowania.** Zawiera szczegółowe informacje dotyczące aplikacji i użytkowników, w tym lokalizacji logowania, systemu operacyjnego lub klienta przeglądarki oraz wersji, a także liczbę logowań zakończonych powodzeniem lub niepowodzeniem.

- **Starsza wersja uwierzytelniania i dostęp warunkowy.** Zawiera szczegółowe informacje dotyczące aplikacji i użytkowników korzystających ze starszego uwierzytelniania, w tym Multi-Factor Authentication użycie wyzwalane przez zasady dostępu warunkowego, aplikacje korzystające z zasad dostępu warunkowego itd.

- **Analiza niepowodzeń logowania.** Pomaga ustalić, czy występują błędy logowania z powodu akcji użytkownika, problemów z zasadami lub infrastruktury.

- **Raporty niestandardowe.** Możesz tworzyć nowe lub edytować istniejące skoroszyty, aby pomóc w dostosowaniu funkcji wglądu w dane organizacji.

Aby uzyskać więcej informacji, zobacz [jak używać skoroszytów Azure monitor dla Azure Active Directory raportów](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-use-azure-monitor-workbooks).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---april-2019"></a>Nowe aplikacje federacyjne dostępne w galerii aplikacji usługi Azure AD — kwiecień 2019

**Typ:** Nowa funkcja  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwości produktu:** integracja innej firmy

W kwietniu 2019 dodaliśmy następujące 21 nowych aplikacji z obsługą Federacji do galerii aplikacji:

[SAP Fiori](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-fiori-tutorial), [HRworks logowania](https://docs.microsoft.com/azure/active-directory/saas-apps/hrworks-single-sign-on-tutorial)jednokrotnego, [Percolate](https://docs.microsoft.com/azure/active-directory/saas-apps/percolate-tutorial), [MobiControl](https://docs.microsoft.com/azure/active-directory/saas-apps/mobicontrol-tutorial), [Citrix](https://docs.microsoft.com/azure/active-directory/saas-apps/citrix-netscaler-tutorial), [SHIBUMI](https://docs.microsoft.com/azure/active-directory/saas-apps/shibumi-tutorial), [kanaper](https://docs.microsoft.com/azure/active-directory/saas-apps/benchling-tutorial), [MileIQ](https://mileiq.onelink.me/991934284/7e980085), [PageDNA](https://docs.microsoft.com/azure/active-directory/saas-apps/pagedna-tutorial), EduBrite systemu [LMS](https://docs.microsoft.com/azure/active-directory/saas-apps/edubrite-lms-tutorial), [RStudio Connect ](https://docs.microsoft.com/azure/active-directory/saas-apps/rstudio-connect-tutorial), [AMMS](https://docs.microsoft.com/azure/active-directory/saas-apps/amms-tutorial), [](https://docs.microsoft.com/azure/active-directory/saas-apps/mitel-connect-tutorial) [Alibabae, Chmura (Logowanie jednokrotne oparte na rolach)](https://docs.microsoft.com/azure/active-directory/saas-apps/alibaba-cloud-service-role-based-sso-tutorial), [Certent Management](https://docs.microsoft.com/azure/active-directory/saas-apps/certent-equity-management-tutorial), [Sectigo Certificate Manager](https://docs.microsoft.com/azure/active-directory/saas-apps/sectigo-certificate-manager-tutorial), [GreenOrbit](https://docs.microsoft.com/azure/active-directory/saas-apps/greenorbit-tutorial), [Workgrid](https://docs.microsoft.com/azure/active-directory/saas-apps/workgrid-tutorial), [Monday.com](https://docs.microsoft.com/azure/active-directory/saas-apps/mondaycom-tutorial), [SurveyMonkey Enterprise](https://docs.microsoft.com/azure/active-directory/saas-apps/surveymonkey-enterprise-tutorial), [Indiggo](https://indiggolead.com/)

Aby uzyskać więcej informacji na temat aplikacji, zobacz [SaaS Application Integration with Azure Active Directory](https://aka.ms/appstutorial). Aby uzyskać więcej informacji na temat tworzenia listy aplikacji w galerii aplikacji usługi Azure AD, zobacz [Wyświetlanie listy aplikacji w galerii aplikacji Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="new-access-reviews-frequency-option-and-multiple-role-selection"></a>Nowa opcja częstotliwości przeglądy dostępu i wybór wielu ról

**Typ:** Nowa funkcja  
**Kategoria usługi:** Przeglądy dostępu  
**Możliwość produktu:** Zarządzanie tożsamościami

Nowe aktualizacje w przeglądach dostępu usługi Azure AD umożliwiają:

- Oprócz wcześniej istniejących opcji co tydzień, co miesiąc, co kwartał i co rok należy zmienić częstotliwość kontroli dostępu do **półrocza**.

- Podczas tworzenia jednego przeglądu dostępu wybierz wiele ról usługi Azure AD i Azure Resource. W tej sytuacji wszystkie role są skonfigurowane z tymi samymi ustawieniami, a wszyscy recenzenci są powiadamiani w tym samym czasie.

Aby uzyskać więcej informacji na temat sposobu tworzenia przeglądu dostępu, zobacz [Tworzenie przeglądu dostępu do grup lub aplikacji w przeglądach dostępu usługi Azure AD](https://docs.microsoft.com/azure/active-directory/governance/create-access-review).

---

### <a name="azure-ad-connect-email-alert-systems-are-transitioning-sending-new-email-sender-information-for-some-customers"></a>Azure AD Connect przechodzenia do systemu alertów poczty e-mail, co umożliwia wysyłanie nowych informacji o nadawcy wiadomości e-mail dla niektórych klientów

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** AD Sync  
**Możliwość produktu:** Platformach

Azure AD Connect jest w trakcie przechodzenia do naszych systemów alertów e-mail, co może spowodować, że niektórzy klienci są nowym nadawcą poczty e-mail. Aby rozwiązać ten problemy, należy dodać `azure-noreply@microsoft.com` do listy dozwolonych w organizacji lub nie będzie można kontynuować otrzymywania ważnych alertów z pakietu Office 365, Azure lub usług synchronizacji.

---

### <a name="upn-suffix-changes-are-now-successful-between-federated-domains-in-azure-ad-connect"></a>Zmiany sufiksu UPN są teraz pomyślne między domenami federacyjnymi w Azure AD Connect

**Typ:** FIXED  
**Kategoria usługi:** AD Sync  
**Możliwość produktu:** Platformach

Teraz można pomyślnie zmienić sufiks nazwy UPN użytkownika z jednej domeny federacyjnej na inną domenę federacyjną w Azure AD Connect. Ta poprawka oznacza, że w trakcie cyklu synchronizacji nie ma już komunikatu o błędzie FederatedDomainChangeError lub odebrać wiadomość e-mail z powiadomieniem "nie można zaktualizować tego obiektu w Azure Active Directory, ponieważ atrybut [ FederatedUser. UserPrincipalName] jest nieprawidłowy. Zaktualizuj wartość w lokalnych usługach katalogowych.

Aby uzyskać więcej informacji, zobacz [Rozwiązywanie problemów podczas synchronizacji](https://docs.microsoft.com/azure/active-directory/hybrid/tshoot-connect-sync-errors#federateddomainchangeerror).

---

### <a name="increased-security-using-the-app-protection-based-conditional-access-policy-in-azure-ad-public-preview"></a>Zwiększone bezpieczeństwo przy użyciu zasad dostępu warunkowego opartego na ochronie aplikacji w usłudze Azure AD (publiczna wersja zapoznawcza)

**Typ:** Nowa funkcja  
**Kategoria usługi:** Dostęp warunkowy  
**Możliwość produktu:** Ochrona tożsamości & zabezpieczenia

Dostęp warunkowy oparty na ochronie aplikacji jest teraz dostępny przy użyciu zasad **Wymagaj ochrony aplikacji** . Nowe zasady ułatwiają zwiększenie bezpieczeństwa organizacji, pomagając w uniknięciu:

- Użytkownicy uzyskują dostęp do aplikacji bez licencji na Microsoft Intune.

- Użytkownicy nie mogą uzyskać Microsoft Intune zasad ochrony aplikacji.

- Użytkownicy uzyskują dostęp do aplikacji bez skonfigurowanych zasad ochrony aplikacji Microsoft Intune.

Aby uzyskać więcej informacji, zobacz [jak wymagać zasad ochrony aplikacji dla dostępu do aplikacji w chmurze przy użyciu dostępu warunkowego](https://docs.microsoft.com/azure/active-directory/conditional-access/app-protection-based-conditional-access).

---

### <a name="new-support-for-azure-ad-single-sign-on-and-conditional-access-in-microsoft-edge-public-preview"></a>Nowa obsługa logowania jednokrotnego w usłudze Azure AD i dostępu warunkowego w programie Microsoft Edge (publiczna wersja zapoznawcza)

**Typ:** Nowa funkcja  
**Kategoria usługi:** Dostęp warunkowy  
**Możliwość produktu:** Ochrona tożsamości & zabezpieczenia

Ulepszono obsługę usługi Azure AD dla przeglądarki Microsoft Edge, w tym zapewnianie nowej obsługi logowania jednokrotnego usługi Azure AD i dostępu warunkowego. Jeśli wcześniej używasz Managed Browser Microsoft Intune, możesz teraz użyć przeglądarki Microsoft Edge.

Aby uzyskać więcej informacji na temat konfigurowania urządzeń i aplikacji oraz zarządzania nimi przy użyciu dostępu warunkowego, zobacz [Wymagaj zarządzanych urządzeń dla dostępu do aplikacji w chmurze z dostępem warunkowym](https://docs.microsoft.com/azure/active-directory/conditional-access/require-managed-devices) i [Wymagaj zatwierdzonych aplikacji klienckich do uzyskiwania dostępu do aplikacji w chmurze przy użyciu dostępu warunkowego ](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access). Aby uzyskać więcej informacji o sposobach zarządzania dostępem przy użyciu programu Microsoft Edge z zasadami Microsoft Intune, zobacz [Zarządzanie dostępem do Internetu za pomocą przeglądarki Microsoft Intune chronionej przez zasady](https://docs.microsoft.com/intune/app-configuration-managed-browser).

---

## <a name="march-2019"></a>Marzec 2019 r.

### <a name="identity-experience-framework-and-custom-policy-support-in-azure-active-directory-b2c-is-now-available-ga"></a>Platforma obsługi tożsamości i obsługa zasad niestandardowych w programie Azure Active Directory B2C jest teraz dostępna (GA)

**Typ:** Nowa funkcja  
**Kategoria usługi:** B2C — Zarządzanie tożsamościami konsumentów  
**Możliwość produktu:** B2B/B2C

Teraz można tworzyć niestandardowe zasady w Azure AD B2C, w tym następujące zadania, które są obsługiwane na skalę i w ramach umowy SLA platformy Azure:

- Tworzenie i przekazywanie niestandardowych podróży użytkowników uwierzytelniania przy użyciu zasad niestandardowych.

- Opisz przedziały użytkownika krok po kroku jako wymianę między dostawcami oświadczeń.

- Definiowanie rozgałęziania warunkowego w podróży użytkownika.

- Przekształcaj i Mapuj oświadczenia do użycia w decyzjach i komunikacji w czasie rzeczywistym.

- Korzystanie z usług z obsługą interfejsu API REST w przypadku niestandardowych podróży użytkowników uwierzytelniania. Na przykład z dostawcami poczty e-mail, CRMs i systemami autoryzacji własnościowej.

- Sfederować z dostawcami tożsamości, którzy są zgodni z protokołem OpenIDConnect. Na przykład z wieloma dzierżawcami usługi Azure AD, dostawcami kont społecznościowych lub dostawcami weryfikacji dwuskładnikowej.

Aby uzyskać więcej informacji na temat tworzenia zasad niestandardowych, zobacz [uwagi dla deweloperów dotyczące zasad niestandardowych w Azure Active Directory B2C](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-developer-notes-custom) i przeczytaj [wpis w blogu pozostałej Simon, w tym analizy przypadków](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-custom-policies-to-build-your-own-identity-journeys/ba-p/382791).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---march-2019"></a>Nowe aplikacje federacyjne dostępne w galerii aplikacji usługi Azure AD — marzec 2019

**Typ:** Nowa funkcja  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwości produktu:** integracja innej firmy

W marcu 2019 dodaliśmy te 14 nowych aplikacji z obsługą Federacji do galerii aplikacji:

[ISEC7 Mobile Exchange Delegate](https://www.isec7.com/english/), [MediusFlow](https://office365.cloudapp.mediusflow.com/), [ePlatform](https://docs.microsoft.com/azure/active-directory/saas-apps/eplatform-tutorial), [Fulcrum](https://docs.microsoft.com/azure/active-directory/saas-apps/fulcrum-tutorial), [ExcelityGlobal](https://docs.microsoft.com/azure/active-directory/saas-apps/excelityglobal-tutorial), [system inspekcji oparty na wyjaśnieniu](https://docs.microsoft.com/azure/active-directory/saas-apps/explanation-based-auditing-system-tutorial), [Leaning](https://docs.microsoft.com/azure/active-directory/saas-apps/lean-tutorial), [Powerschool — zagadnienia dotyczące wydajności](https://docs.microsoft.com/azure/active-directory/saas-apps/powerschool-performance-matters-tutorial), [Cinode ](https://cinode.com/), [Sieć intranetowa Iris](https://docs.microsoft.com/azure/active-directory/saas-apps/iris-intranet-tutorial), [Empactis](https://docs.microsoft.com/azure/active-directory/saas-apps/empactis-tutorial), [SmartDraw](https://docs.microsoft.com/azure/active-directory/saas-apps/smartdraw-tutorial), [Confirmit Horizons](https://docs.microsoft.com/azure/active-directory/saas-apps/confirmit-horizons-tutorial) [, zadanie](https://docs.microsoft.com/azure/active-directory/saas-apps/tas-tutorial)

Aby uzyskać więcej informacji na temat aplikacji, zobacz [SaaS Application Integration with Azure Active Directory](https://aka.ms/appstutorial). Aby uzyskać więcej informacji na temat tworzenia listy aplikacji w galerii aplikacji usługi Azure AD, zobacz [Wyświetlanie listy aplikacji w galerii aplikacji Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="new-zscaler-and-atlassian-provisioning-connectors-in-the-azure-ad-gallery---march-2019"></a>Nowe łączniki inicjowania obsługi rozwiązania Zscaler i Atlassian w galerii usługi Azure AD — marzec 2019

**Typ:** Nowa funkcja  
**Kategoria usługi:** Inicjowanie obsługi aplikacji  
**Możliwości produktu:** integracja innej firmy

Automatyzuj tworzenie, aktualizowanie i usuwanie kont użytkowników dla następujących aplikacji:

[Rozwiązania Zscaler](https://aka.ms/ZscalerProvisioning), [rozwiązania Zscaler beta](https://aka.ms/ZscalerBetaProvisioning), [rozwiązania Zscaler One](https://aka.ms/ZscalerOneProvisioning), [rozwiązania Zscaler dwa](https://aka.ms/ZscalerTwoProvisioning), [rozwiązania Zscaler trzy](https://aka.ms/ZscalerThreeProvisioning), [rozwiązania Zscaler ZSCloud](https://aka.ms/ZscalerZSCloudProvisioning), [Atlassian Cloud](https://aka.ms/atlassianCloudProvisioning)

Aby uzyskać więcej informacji o tym, jak lepiej zabezpieczyć organizację przez automatyczne Inicjowanie obsługi kont użytkowników, zobacz [Automatyzowanie aprowizacji użytkowników w aplikacjach SaaS za pomocą usługi Azure AD](https://aka.ms/ProvisioningDocumentation).

---

### <a name="restore-and-manage-your-deleted-office-365-groups-in-the-azure-ad-portal"></a>Przywracanie usuniętych grup programu Office 365 i zarządzanie nimi w portalu usługi Azure AD

**Typ:** Nowa funkcja  
**Kategoria usługi:** Zarządzanie grupami  
**Możliwość produktu:** Społeczności

Można teraz wyświetlać usunięte grupy programu Office 365 i zarządzać nimi z poziomu portalu usługi Azure AD. Ta zmiana ułatwia sprawdzenie, które grupy są dostępne do przywrócenia, a także umożliwia trwałe usunięcie wszystkich grup, które nie są potrzebne w organizacji.

Aby uzyskać więcej informacji, zobacz [przywracanie wygasłych lub usuniętych grup](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-restore-deleted#view-and-manage-the-deleted-office-365-groups-that-are-available-to-restore).

---

### <a name="single-sign-on-is-now-available-for-azure-ad-saml-secured-on-premises-apps-through-application-proxy-public-preview"></a>Logowanie jednokrotne jest teraz dostępne dla aplikacji lokalnych opartych na protokole SAML usługi Azure AD za pomocą serwera proxy aplikacji (publiczna wersja zapoznawcza)

**Typ:** Nowa funkcja  
**Kategoria usługi:** Serwer proxy aplikacji  
**Możliwość produktu:** Access Control

Teraz możesz udostępnić Logowanie jednokrotne (SSO) dla aplikacji lokalnych, uwierzytelnianych przy użyciu protokołu SAML oraz dostępu zdalnego do tych aplikacji za pomocą serwera proxy aplikacji. Aby uzyskać więcej informacji na temat sposobu konfigurowania logowania jednokrotnego SAML przy użyciu aplikacji lokalnych, zobacz Usługa [SAML Single Sign-in dla aplikacji lokalnych z serwerem proxy aplikacji (wersja zapoznawcza)](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-single-sign-on-on-premises-apps).

---

### <a name="client-apps-in-request-loops-will-be-interrupted-to-improve-reliability-and-user-experience"></a>Pętle aplikacji klienckich w żądaniach zostaną zakłócone w celu poprawy niezawodności i środowiska użytkownika

**Typ:** Nowa funkcja  
**Kategoria usługi:** Uwierzytelnienia (nazwy logowania)  
**Możliwość produktu:** Uwierzytelnianie użytkownika

Aplikacje klienckie mogą nieprawidłowo wystawiać setki tych samych żądań logowania w krótkim czasie. Te żądania, niezależnie od tego, czy zakończyły się powodzeniem, czy nie, przyczyniają się do słabego środowiska użytkownika i zwiększonego obciążenia dla dostawcy tożsamości, zwiększając opóźnienia dla wszystkich użytkowników i zmniejszając dostępność dostawcy tożsamości.

Ta aktualizacja wysyła `invalid_grant` błąd: `AADSTS50196: The server terminated an operation because it encountered a loop while processing a request` do aplikacji klienckich, które wydają zduplikowane żądania wiele razy w krótkim czasie, poza zakresem normalnej operacji. Aplikacje klienckie, które napotykają ten problem, powinny wyświetlać interaktywny monit, co wymaga ponownego zalogowania użytkownika. Aby uzyskać więcej informacji na temat tej zmiany i sposobu naprawy aplikacji w przypadku wystąpienia tego błędu, zobacz [co nowego w uwierzytelnianiu?](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#looping-clients-will-be-interrupted).

---

### <a name="new-audit-logs-user-experience-now-available"></a>Nowe środowisko użytkownika dzienników inspekcji jest teraz dostępne

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Reporting  
**Możliwość produktu:** Monitorowanie & raportowania

Utworzyliśmy nową stronę **dzienników inspekcji** usługi Azure AD, która ułatwia zwiększenie czytelności i sposób wyszukiwania informacji. Aby wyświetlić stronę nowe **dzienniki inspekcji** , wybierz pozycję **dzienniki inspekcji** w sekcji **aktywność** usługi Azure AD.

![Nowa strona dzienników inspekcji z przykładowymi informacjami](media/whats-new/audit-logs-page.png)

Aby uzyskać więcej informacji na temat nowych **dzienników inspekcji** , zobacz [Raporty aktywności inspekcji w portalu Azure Active Directory](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-audit-logs#audit-logs).

---

### <a name="new-warnings-and-guidance-to-help-prevent-accidental-administrator-lockout-from-misconfigured-conditional-access-policies"></a>Nowe ostrzeżenia i wskazówki pomagające w zapobieganiu przypadkowej blokady administratora z nieprawidłowo skonfigurowanymi zasadami dostępu warunkowego

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Dostęp warunkowy  
**Możliwość produktu:** Ochrona tożsamości & zabezpieczenia

Aby zapobiec przypadkowemu zablokowaniu przez administratorów własnych dzierżaw za pomocą nieprawidłowo skonfigurowanych zasad dostępu warunkowego, zostały utworzone nowe ostrzeżenia i zaktualizowane wskazówki w Azure Portal. Aby uzyskać więcej informacji o nowych wskazówkach, zobacz [co to są zależności usługi w Azure Active Directory dostęp warunkowy](https://docs.microsoft.com/azure/active-directory/conditional-access/service-dependencies).

---

### <a name="improved-end-user-terms-of-use-experiences-on-mobile-devices"></a>Udoskonalone środowiska użytkowania dotyczące użytkowników końcowych na urządzeniach przenośnych

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Warunki użytkowania  
**Możliwość produktu:** Dobrego

Zaktualizowaliśmy nasze istniejące warunki użytkowania, aby pomóc w ulepszaniu sposobu przeglądania i wyrażania zgody na warunki użytkowania na urządzeniu przenośnym. Teraz możesz powiększać i pomniejszać, wrócić, pobrać informacje i wybierać hiperlinki. Aby uzyskać więcej informacji o zaktualizowanych warunkach użytkowania, zobacz [Azure Active Directory warunki użytkowania](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use#what-terms-of-use-looks-like-for-users).

---

### <a name="new-azure-ad-activity-logs-download-experience-available"></a>Dostępne są nowe środowisko pobierania dzienników aktywności usługi Azure AD

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Reporting  
**Możliwość produktu:** Monitorowanie & raportowania

Teraz można pobrać duże ilości dzienników aktywności bezpośrednio z Azure Portal. Ta aktualizacja umożliwia:

- Pobierz do 250 000 wierszy.

- Otrzymuj powiadomienie po zakończeniu pobierania.

- Dostosuj nazwę pliku.

- Określ format danych wyjściowych — JSON lub CSV.

Aby uzyskać więcej informacji na temat tej funkcji, zobacz [Szybki Start: pobieranie raportu inspekcji przy użyciu Azure Portal](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-download-audit-report)

---

### <a name="breaking-change-updates-to-condition-evaluation-by-exchange-activesync-eas"></a>Zmiana podziału: aktualizacje oceny warunku przez program Exchange ActiveSync (EAS)

**Typ:** Planowanie zmiany  
**Kategoria usługi:** Dostęp warunkowy  
**Możliwość produktu:** Access Control

Jesteśmy w trakcie aktualizowania sposobu, w jaki program Exchange ActiveSync (EAS) ocenia następujące warunki:

- Lokalizacja użytkownika, na podstawie kraju, regionu lub adresu IP

- Ryzyko związane z logowaniem

- Platforma urządzeń

Jeśli te warunki zostały wcześniej użyte w zasadach dostępu warunkowego, należy pamiętać, że zachowanie warunku może się zmienić. Na przykład jeśli w zasadach użyto wcześniej warunku lokalizacji użytkownika, zasady te mogą być teraz pomijane na podstawie lokalizacji użytkownika.

---

## <a name="february-2019"></a>Luty 2019 r.

### <a name="configurable-azure-ad-saml-token-encryption-public-preview"></a>Konfigurowalne szyfrowanie tokenów SAML usługi Azure AD (publiczna wersja zapoznawcza) 

**Typ:** Nowa funkcja  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwość produktu:** ZWRÓCIŁ

Teraz można skonfigurować dowolną obsługiwaną aplikację SAML do odbierania szyfrowanych tokenów SAML. Po skonfigurowaniu i użyciu z aplikacją usługa Azure AD szyfruje wyemitowane potwierdzenia SAML przy użyciu klucza publicznego uzyskanego z certyfikatu przechowywanego w usłudze Azure AD.

Aby uzyskać więcej informacji o konfigurowaniu szyfrowania tokenów SAML, zobacz [Konfigurowanie szyfrowania tokenów SAML usługi Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/howto-saml-token-encryption).

---

### <a name="create-an-access-review-for-groups-or-apps-using-azure-ad-access-reviews"></a>Tworzenie przeglądu dostępu dla grup lub aplikacji za pomocą przeglądów dostępu w usłudze Azure AD

**Typ:** Nowa funkcja  
**Kategoria usługi:** Przeglądy dostępu  
**Możliwość produktu:** Dobrego

Teraz można uwzględnić wiele grup lub aplikacji w jednym przeglądzie dostępu do usługi Azure AD na potrzeby członkostwa w grupie lub przypisywania aplikacji. Przeglądy dostępu z wieloma grupami lub aplikacjami są konfigurowane przy użyciu tych samych ustawień, a wszyscy włączeni recenzenci są powiadamiani w tym samym czasie.

Aby uzyskać więcej informacji na temat tworzenia przeglądu dostępu za pomocą przeglądów dostępu w usłudze Azure AD, zobacz [Tworzenie przeglądu dostępu do grup lub aplikacji w przeglądach dostępu do usługi Azure AD](https://docs.microsoft.com/azure/active-directory/governance/create-access-review)

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---february-2019"></a>Nowe aplikacje federacyjne dostępne w galerii aplikacji usługi Azure AD — luty 2019

**Typ:** Nowa funkcja  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwości produktu:** integracja innej firmy
 
W lutym 2019 dodaliśmy następujące 27 nowych aplikacji z obsługą Federacji do galerii aplikacji:

[Euromonitor paszport](https://docs.microsoft.com/azure/active-directory/saas-apps/euromonitor-passport-tutorial), [MindTickle](https://docs.microsoft.com/azure/active-directory/saas-apps/mindtickle-tutorial), [odcisk palca](https://seeforgetest-exxon.azurewebsites.net/Account/create?Length=7),, [stosy](https://docs.microsoft.com/azure/active-directory/saas-apps/airstack-tutorial)zawartości i [Oracle Fusion ERP](https://docs.microsoft.com/azure/active-directory/saas-apps/oracle-fusion-erp-tutorial), [iDrive](https://docs.microsoft.com/azure/active-directory/saas-apps/idrive-tutorial), [skyward Qmlativ](https://docs.microsoft.com/azure/active-directory/saas-apps/skyward-qmlativ-tutorial), [Brightidea](https://docs.microsoft.com/azure/active-directory/saas-apps/brightidea-tutorial), [AlertOps](https://docs.microsoft.com/azure/active-directory/saas-apps/alertops-tutorial), [Soloinsight-CloudGate SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/soloinsight-cloudgate-sso-tutorial), Uprawnienie kliknij, [Brandfolder](https://docs.microsoft.com/azure/active-directory/saas-apps/brandfolder-tutorial), [StoregateSmartFile](https://docs.microsoft.com/azure/active-directory/saas-apps/smartfile-tutorial), [Pexip](https://docs.microsoft.com/azure/active-directory/saas-apps/pexip-tutorial), [Stormboard](https://docs.microsoft.com/azure/active-directory/saas-apps/stormboard-tutorial), [sejsmiczne](https://docs.microsoft.com/azure/active-directory/saas-apps/seismic-tutorial), [Udostępnij marzenie](https://www.shareadream.org/how-it-works), [Bugsnag](https://docs.microsoft.com/azure/active-directory/saas-apps/bugsnag-tutorial), [webMethods Cloud Integration](https://docs.microsoft.com/azure/active-directory/saas-apps/webmethods-integration-cloud-tutorial), [wiedza System LMS z dowolnego miejsca](https://docs.microsoft.com/azure/active-directory/saas-apps/knowledge-anywhere-lms-tutorial), [kampus w jednostce organizacyjnej](https://docs.microsoft.com/azure/active-directory/saas-apps/ou-campus-tutorial), [dane Periscope](https://docs.microsoft.com/azure/active-directory/saas-apps/periscope-data-tutorial), [Portal NetOp](https://docs.microsoft.com/azure/active-directory/saas-apps/netop-portal-tutorial), [smartvid.IO](https://docs.microsoft.com/azure/active-directory/saas-apps/smartvid.io-tutorial), [PureCloud przez Genesys](https://docs.microsoft.com/azure/active-directory/saas-apps/purecloud-by-genesys-tutorial), [platforma produktywności ClickUp](https://docs.microsoft.com/azure/active-directory/saas-apps/clickup-productivity-platform-tutorial)

Aby uzyskać więcej informacji na temat aplikacji, zobacz [SaaS Application Integration with Azure Active Directory](https://aka.ms/appstutorial). Aby uzyskać więcej informacji na temat tworzenia listy aplikacji w galerii aplikacji usługi Azure AD, zobacz [Wyświetlanie listy aplikacji w galerii aplikacji Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="enhanced-combined-mfasspr-registration"></a>Ulepszona Rejestracja usługi MFA/SSPR

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Samoobsługowe resetowanie hasła  
**Możliwość produktu:** Uwierzytelnianie użytkownika

W odpowiedzi na Opinie klientów Ulepszono środowisko w wersji zapoznawczej usługi MFA/SSPR, dzięki czemu użytkownicy mogą szybko rejestrować swoje informacje zabezpieczające dla usługi MFA i SSPR. 

**Aby włączyć już dziś udoskonalone środowisko dla użytkowników, wykonaj następujące kroki:**

1. Jako Administrator globalny lub administrator użytkownika Zaloguj się do Azure Portal i przejdź do pozycji **Azure Active Directory > Ustawienia użytkownika > Zarządzaj ustawieniami funkcji w wersji zapoznawczej panelu dostępu**. 

2. W przypadku **użytkowników, którzy mogą używać funkcji w wersji zapoznawczej do rejestrowania i zarządzania informacjami o zabezpieczeniach — odświeżanie** , wybierz opcję włączenia funkcji dla **wybranej grupy użytkowników** lub dla **wszystkich użytkowników**.

W ciągu najbliższych kilku tygodni usuniemy możliwość włączenia starego połączenia usługi SSPR w wersji zapoznawczej usługi MFA w przypadku dzierżawców, które nie są jeszcze włączone.

**Aby sprawdzić, czy formant zostanie usunięty dla dzierżawy, wykonaj następujące kroki:**

1. Jako Administrator globalny lub administrator użytkownika Zaloguj się do Azure Portal i przejdź do pozycji **Azure Active Directory > Ustawienia użytkownika > Zarządzaj ustawieniami funkcji w wersji zapoznawczej panelu dostępu**.  

2. Jeśli **Użytkownicy, którzy mogą korzystać z funkcji w wersji zapoznawczej do rejestrowania i zarządzania informacjami o zabezpieczeniach** , mają ustawioną wartość **Brak**, opcja zostanie usunięta z dzierżawy.

Bez względu na to, czy wcześniej włączono poprzednie środowisko w wersji zapoznawczej rejestracji usługi MFA/SSPR dla użytkowników, stare środowisko zostanie wyłączone w przyszłości. Ze względu na to zdecydowanie zalecamy przechodzenie do nowego, ulepszonego środowiska tak szybko, jak to możliwe.

Aby uzyskać więcej informacji na temat udoskonalonego środowiska rejestracji, zobacz [chłodne ulepszenia usługi Azure AD połączenia MFA i rejestracji resetowania haseł](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Cool-enhancements-to-the-Azure-AD-combined-MFA-and-password/ba-p/354271).

---

### <a name="updated-policy-management-experience-for-user-flows"></a>Zaktualizowano środowisko zarządzania zasadami dla przepływów użytkowników

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** B2C — Zarządzanie tożsamościami konsumentów  
**Możliwość produktu:** B2B/B2C

Zaktualizowaliśmy proces tworzenia i zarządzania zasadami dla przepływów użytkowników (wcześniej znanych jako wbudowane zasady). To nowe środowisko jest teraz domyślne dla wszystkich dzierżawców usługi Azure AD.

Dodatkowe opinie i sugestie można przekazać, korzystając z ikon uśmiech lub niezadowolenia w obszarze **Wyślij nam opinię** w górnej części ekranu portalu.

Aby uzyskać więcej informacji na temat nowego środowiska zarządzania zasadami, zapoznaj się z tematem [Azure AD B2C teraz zawiera on dostosowanie JavaScript i wiele nowych funkcji](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-now-has-JavaScript-customization-and-many-more-new/ba-p/353595) .

---

### <a name="choose-specific-page-element-versions-provided-by-azure-ad-b2c"></a>Wybierz określone wersje elementów strony udostępniane przez Azure AD B2C

**Typ:** Nowa funkcja  
**Kategoria usługi:** B2C — Zarządzanie tożsamościami konsumentów  
**Możliwość produktu:** B2B/B2C

Teraz można wybrać określoną wersję elementów strony dostarczonych przez Azure AD B2C. Wybierając określoną wersję, można przetestować aktualizacje, zanim zostaną one wyświetlone na stronie i można uzyskać przewidywalne zachowanie. Ponadto możesz teraz wyrazić zgodę na wymuszanie określonych wersji strony, aby umożliwić dostosowywanie języka JavaScript. Aby włączyć tę funkcję, przejdź do strony **Właściwości** w obszarze przepływy użytkownika.

Aby uzyskać więcej informacji na temat wybierania określonych wersji elementów strony, zapoznaj się z tematem [Azure AD B2C teraz zawiera on dostosowanie JavaScript i wiele nowych funkcji](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-now-has-JavaScript-customization-and-many-more-new/ba-p/353595) .

---

### <a name="configurable-end-user-password-requirements-for-b2c-ga"></a>Konfigurowalne wymagania dotyczące hasła użytkownika końcowego dla B2C (GA)

**Typ:** Nowa funkcja  
**Kategoria usługi:** B2C — Zarządzanie tożsamościami konsumentów  
**Możliwość produktu:** B2B/B2C

Teraz możesz skonfigurować złożoność hasła organizacji dla użytkowników końcowych, zamiast korzystać z natywnych zasad haseł usługi Azure AD. W bloku **Właściwości** przepływów użytkownika (wcześniej znanych jako wbudowane zasady) można wybrać złożoność hasła lub **silne** **lub można** utworzyć **niestandardowy** zestaw wymagań.

Więcej informacji o konfiguracji wymagań dotyczących złożoności haseł znajduje się [w temacie Konfigurowanie wymagań dotyczących złożoności haseł w Azure Active Directory B2C](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-password-complexity).

---

### <a name="new-default-templates-for-custom-branded-authentication-experiences"></a>Nowe domyślne szablony dla niestandardowych środowisk uwierzytelniania markowego

**Typ:** Nowa funkcja  
**Kategoria usługi:** B2C — Zarządzanie tożsamościami konsumentów  
**Możliwość produktu:** B2B/B2C

Możesz użyć naszych nowych szablonów domyślnych znajdujących się w bloku **układy stron** przepływów użytkownika (znanych wcześniej jako zasady wbudowane), aby utworzyć niestandardowe środowisko uwierzytelniania markowego dla użytkowników.

Aby uzyskać więcej informacji o korzystaniu z szablonów, zobacz [Azure AD B2C teraz zawiera ona dostosowanie JavaScript i wiele nowych funkcji](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-now-has-JavaScript-customization-and-many-more-new/ba-p/353595).

---

## <a name="january-2019"></a>Styczeń 2019

### <a name="active-directory-b2b-collaboration-using-one-time-passcode-authentication-public-preview"></a>Active Directory współpracy B2B przy użyciu jednorazowego uwierzytelniania kodu dostępu (publiczna wersja zapoznawcza)

**Typ:** Nowa funkcja  
**Kategoria usługi:** B2B  
**Możliwość produktu:** B2B/B2C

Wprowadziliśmy jednorazowe uwierzytelnianie kodu dostępu (OTP) dla użytkowników Gości B2B, którzy nie mogą być uwierzytelniani przy użyciu innych metod, takich jak Azure AD, konto Microsoft (MSA) lub Google Federation. Ta nowa metoda uwierzytelniania oznacza, że użytkownicy-Goście nie muszą tworzyć nowych konto Microsoft. Zamiast tego podczas realizowania zaproszenia lub uzyskiwania dostępu do zasobu udostępnionego użytkownik-Gość może zażądać, aby kod tymczasowy został wysłany na adres e-mail. Przy użyciu tego kodu tymczasowego użytkownik-Gość może nadal logować się.

Aby uzyskać więcej informacji, zobacz [jednorazowe uwierzytelnianie kodu dostępu za pomocą poczty e-mail (wersja zapoznawcza)](https://docs.microsoft.com/azure/active-directory/b2b/one-time-passcode) i blog, [usługa Azure AD zapewnia bezproblemowe udostępnianie i współpracę dla dowolnego użytkownika z dowolnym kontem](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-makes-sharing-and-collaboration-seamless-for-any-user/ba-p/325949).

### <a name="new-azure-ad-application-proxy-cookie-settings"></a>Nowe ustawienia pliku cookie usługi Azure serwer proxy aplikacji usługi Azure AD

**Typ:** Nowa funkcja  
**Kategoria usługi:** Serwer proxy aplikacji  
**Możliwość produktu:** Access Control

Wprowadziliśmy trzy nowe ustawienia plików cookie dostępne dla aplikacji publikowanych za poorednictwem serwera proxy aplikacji:

- **Używaj tylko protokołu HTTP.** Ustawia flagę **HTTPOnly** na serwerze proxy aplikacji i plikach cookie sesji. Włączenie tego ustawienia zapewnia dodatkowe korzyści z zabezpieczeń, takie jak uniemożliwienie kopiowania lub modyfikowania plików cookie za pomocą skryptów po stronie klienta. Zalecamy włączenie tej flagi (wybierz opcję **tak**), aby uzyskać dodatkowe korzyści.

- **Użyj bezpiecznego pliku cookie.** Ustawia **bezpieczną** flagę na serwerze proxy aplikacji i plikach cookie sesji. Włączenie tego ustawienia zapewnia dodatkowe korzyści z zabezpieczeń, zapewniając, że pliki cookie są przesyłane tylko za pośrednictwem bezpiecznych kanałów protokołu TLS, takich jak HTTPS. Zalecamy włączenie tej flagi (wybierz opcję **tak**), aby uzyskać dodatkowe korzyści.

- **Użyj trwałego pliku cookie.** Zapobiega utracie dostępu plików cookie po zamknięciu przeglądarki sieci Web. Te pliki cookie są ostatnie przez okres istnienia tokenu dostępu. Pliki cookie są jednak resetowane po osiągnięciu czasu wygaśnięcia lub ręcznym usunięciu pliku cookie przez użytkownika. Zalecamy zachowanie ustawienia domyślnego **nie**, włączając ustawienie dla starszych aplikacji, które nie udostępniają plików cookie między procesami.

Aby uzyskać więcej informacji na temat nowych plików cookie, zobacz [Ustawienia plików cookie dotyczące uzyskiwania dostępu do aplikacji lokalnych w Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-cookie-settings).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---january-2019"></a>Nowe aplikacje federacyjne dostępne w galerii aplikacji usługi Azure AD — styczeń 2019

**Typ:** Nowa funkcja  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwości produktu:** integracja innej firmy
 
W styczniu 2019 dodaliśmy do galerii aplikacji 35 te nowe aplikacje z obsługą Federacji:

[Firstbird](https://docs.microsoft.com/azure/active-directory/saas-apps/firstbird-tutorial), [Folloze](https://docs.microsoft.com/azure/active-directory/saas-apps/folloze-tutorial), [paleta talent](https://docs.microsoft.com/azure/active-directory/saas-apps/talent-palette-tutorial), [infor CloudSuite](https://docs.microsoft.com/azure/active-directory/saas-apps/infor-cloud-suite-tutorial), [Cisco parasol](https://docs.microsoft.com/azure/active-directory/saas-apps/cisco-umbrella-tutorial), [rozwiązania Zscaler dostęp do Internetu](https://docs.microsoft.com/azure/active-directory/saas-apps/zscaler-internet-access-administrator-tutorial), [przypomnienia o wygaśnięciu](https://docs.microsoft.com/azure/active-directory/saas-apps/expiration-reminder-tutorial), [przeglądarka InstaVR](https://docs.microsoft.com/azure/active-directory/saas-apps/instavr-viewer-tutorial), [CorpTax](https://docs.microsoft.com/azure/active-directory/saas-apps/corptax-tutorial), [ Verb](https://app.verb.net/login), [OpenLattice](https://openlattice.com/agora), [TheOrgWiki](https://www.theorgwiki.com/signup), [Pavaso Digital Close](https://docs.microsoft.com/azure/active-directory/saas-apps/pavaso-digital-close-tutorial), [GoodPractice Toolkit](https://docs.microsoft.com/azure/active-directory/saas-apps/goodpractice-toolkit-tutorial), [Cloud Service Picco](https://docs.microsoft.com/azure/active-directory/saas-apps/cloud-service-picco-tutorial), [AuditBoard](https://docs.microsoft.com/azure/active-directory/saas-apps/auditboard-tutorial), [iProva](https://docs.microsoft.com/azure/active-directory/saas-apps/iprova-tutorial), [in](https://docs.microsoft.com/azure/active-directory/saas-apps/workable-tutorial), [CallPlease ](https://webapp.callplease.com/create-account/create-account.html) [GTNexus system rejestracji jednokrotnej](https://docs.microsoft.com/azure/active-directory/saas-apps/gtnexus-sso-module-tutorial), [CBRE ServiceInsight](https://docs.microsoft.com/azure/active-directory/saas-apps/cbre-serviceinsight-tutorial), [Deskradar](https://docs.microsoft.com/azure/active-directory/saas-apps/deskradar-tutorial), [Coralogixv](https://docs.microsoft.com/azure/active-directory/saas-apps/coralogix-tutorial), [Signagelive](https://docs.microsoft.com/azure/active-directory/saas-apps/signagelive-tutorial), [arach dla przedsiębiorstw](https://docs.microsoft.com/azure/active-directory/saas-apps/ares-for-enterprise-tutorial), [K2 dla Office 365](https://www.k2.com/O365), [Xledger](https://www.xledger.net/), [iDiD Manager ](https://docs.microsoft.com/azure/active-directory/saas-apps/idid-manager-tutorial), [HighGear](https://docs.microsoft.com/azure/active-directory/saas-apps/highgear-tutorial), [wiedza](https://docs.microsoft.com/azure/active-directory/saas-apps/visitly-tutorial), [Alp prom](https://docs.microsoft.com/azure/active-directory/saas-apps/korn-ferry-alp-tutorial), [Acadia](https://docs.microsoft.com/azure/active-directory/saas-apps/acadia-tutorial), [Adoddle cSaas platform](https://docs.microsoft.com/azure/active-directory/saas-apps/adoddle-csaas-platform-tutorial)<!-- , [CaféX Portal (Meetings)](https://docs.microsoft.com/azure/active-directory/saas-apps/cafexportal-meetings-tutorial), [MazeMap Link](https://docs.microsoft.com/azure/active-directory/saas-apps/mazemaplink-tutorial)-->  

Aby uzyskać więcej informacji na temat aplikacji, zobacz [SaaS Application Integration with Azure Active Directory](https://aka.ms/appstutorial). Aby uzyskać więcej informacji na temat tworzenia listy aplikacji w galerii aplikacji usługi Azure AD, zobacz [Wyświetlanie listy aplikacji w galerii aplikacji Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="new-azure-ad-identity-protection-enhancements-public-preview"></a>Nowe udoskonalenia Azure AD Identity Protection (publiczna wersja zapoznawcza)

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Ochrona tożsamości  
**Możliwość produktu:** Ochrona tożsamości & zabezpieczenia

Przyjemnością się, że dodaliśmy następujące usprawnienia do oferty Azure AD Identity Protection publicznej wersji zapoznawczej, w tym:

- Zaktualizowany i bardziej zintegrowany interfejs użytkownika

- Dodatkowe interfejsy API

- Ulepszona Ocena ryzyka za poorednictwem uczenia maszynowego

- Wyrównywanie całego produktu wśród ryzykownych użytkowników i ryzykowne logowania

Aby uzyskać więcej informacji na temat ulepszeń, zobacz [co to jest Azure Active Directory Identity Protection (odświeżone)?](https://aka.ms/IdentityProtectionDocs) Aby dowiedzieć się więcej i podzielić się swoimi pomysłami, zapoznaj się z instrukcjami w ramach produktu.

---

### <a name="new-app-lock-feature-for-the-microsoft-authenticator-app-on-ios-and-android-devices"></a>Nowa funkcja blokowania aplikacji dla aplikacji Microsoft Authenticator na urządzeniach z systemami iOS i Android

**Typ:** Nowa funkcja  
**Kategoria usługi:** Aplikacja Microsoft Authenticator  
**Możliwość produktu:** Ochrona tożsamości & zabezpieczenia

Aby zapewnić bezpieczeństwo jednorazowych kodów dostępu, informacji o aplikacji i ustawień aplikacji, możesz włączyć funkcję blokowania aplikacji w aplikacji Microsoft Authenticator. Włączenie blokady aplikacji oznacza, że użytkownik zostanie poproszony o uwierzytelnienie przy użyciu kodu PIN lub biometrycznego za każdym razem, gdy otworzysz aplikację Microsoft Authenticator.

Aby uzyskać więcej informacji, zobacz [często zadawane pytania dotyczące aplikacji Microsoft Authenticator](https://docs.microsoft.com/azure/active-directory/user-help/microsoft-authenticator-app-faq).

---

### <a name="enhanced-azure-ad-privileged-identity-management-pim-export-capabilities"></a>Ulepszone możliwości eksportowania Azure AD Privileged Identity Management (PIM)

**Typ:** Nowa funkcja  
**Kategoria usługi:** Privileged Identity Management  
**Możliwość produktu:** Privileged Identity Management

Administratorzy usługi Privileged Identity Management (PIM) mogą teraz wyeksportować wszystkie aktywne i kwalifikujące się przypisania ról dla określonego zasobu, w tym przypisania ról dla wszystkich zasobów podrzędnych. Wcześniej było trudne, aby administratorzy mogli uzyskać pełną listę przypisań ról dla subskrypcji i musiały wyeksportować przypisania ról dla każdego określonego zasobu.

Aby uzyskać więcej informacji, zobacz [Wyświetlanie historii aktywności i inspekcji dla ról zasobów platformy Azure w programie PIM](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/azure-pim-resource-rbac).

---

## <a name="novemberdecember-2018"></a>Listopad/grudzień 2018

### <a name="users-removed-from-synchronization-scope-no-longer-switch-to-cloud-only-accounts"></a>Użytkownicy usunięci z zakresu synchronizacji nie przełączają się już do kont tylko w chmurze

**Typ:** FIXED  
**Kategoria usługi:** Zarządzanie użytkownikami  
**Możliwość produktu:** Katalogi

>[!Important]
>W związku z tym nasłuchuje i rozumiemy frustrację. Z tego powodu przywrócono tę zmianę do momentu, w którym można ułatwić wdrożenie w organizacji.

Rozwiązano problem polegający na tym, że flaga DirSyncEnabled użytkownika powinna zostać błędnie przełączona na **Fałsz** , gdy obiekt Active Directory Domain Services (AD DS) został wykluczony z zakresu synchronizacji, a następnie przeniesiony do kosza w usłudze Azure AD w następujący sposób cykl synchronizacji. W wyniku tej poprawki Jeśli użytkownik zostanie wykluczony z zakresu synchronizacji, a następnie przywrócono z Kosza usługi Azure AD, konto użytkownika pozostanie jako zsynchronizowane z lokalnej usługi AD zgodnie z oczekiwaniami i nie będzie możliwe do zarządzania w chmurze, ponieważ jego źródło (SoA) pozostaje jako lokalna usługi AD.

Przed tą poprawką wystąpił problem podczas przełączania flagi DirSyncEnabled na wartość false. Nastąpiło niewłaściwe wrażenie, że te konta zostały przekonwertowane na obiekty tylko w chmurze, a konta mogą być zarządzane w chmurze. Jednak konta nadal przechowują swoje SoA jako lokalne i wszystkie zsynchronizowane właściwości (atrybuty cienia) pochodzące z lokalnej usługi AD. Ten stan powodował wiele problemów dotyczących usługi Azure AD i innych obciążeń w chmurze (na przykład usługi Exchange Online), które oczekują traktowania tych kont jako zsynchronizowanych z usługi AD, ale teraz zachowują się one jak konta tylko w chmurze.

W tym momencie jedynym sposobem na prawdziwą konwersję konta synchronizacji z usługi AD na konto tylko w chmurze jest wyłączenie narzędzia DirSync na poziomie dzierżawy, który wyzwala operację zaplecza do transferu SoA. Ten typ zmiany SoA wymaga (ale nie jest ograniczony do) czyszczenia wszystkich lokalnych atrybutów powiązanych (takich jak atrybuty LastDirSyncTime i Shadow) i wysyłania sygnału do innych obciążeń w chmurze, aby miało on odpowiedni obiekt przekonwertowany na konto tylko w chmurze .

W związku z tym ta poprawka uniemożliwia bezpośrednie aktualizacje atrybutu ImmutableID użytkownika synchronizowanego z usługi AD, co w niektórych scenariuszach w przeszłości było wymagane. Zgodnie z projektem, ImmutableID obiektu w usłudze Azure AD, jak nazwa oznacza, ma być niezmienny. Nowe funkcje zaimplementowane w Azure AD Connect Health i Azure AD Connect klienta synchronizacji są dostępne do rozwiązania takich scenariuszy:

- **Aktualizacja ImmutableID o dużej skali dla wielu użytkowników w podejściu przygotowanym**
  
  Na przykład należy wykonać długość AD DS migracji między lasami. Rozwiązanie: Użyj Azure AD Connect, aby **skonfigurować kotwicę źródłową** i, w miarę migrowania użytkownika, skopiować istniejące wartości ImmutableID z usługi Azure AD do atrybutu "MS-ds-Consistency-GUID systemu lokalnego AD DS użytkownika" w nowym lesie. Aby uzyskać więcej informacji, zobacz [Używanie MS-ds-ConsistencyGuid jako sourceAnchor](/azure/active-directory/hybrid/plan-connect-design-concepts#using-ms-ds-consistencyguid-as-sourceanchor).

- **Aktualizacje ImmutableID na dużą skalę dla wielu użytkowników w jednym zrzucie**

  Na przykład podczas wdrażania Azure AD Connect pomyłkę i teraz trzeba zmienić atrybut SourceAnchor. Rozwiązanie: Wyłącz narzędzie DirSync na poziomie dzierżawy i wyczyść wszystkie nieprawidłowe wartości ImmutableID. Aby uzyskać więcej informacji, zobacz Wyłączanie [synchronizacji katalogów dla pakietu Office 365](/office365/enterprise/turn-off-directory-synchronization).

- **Dopasowanie użytkownika lokalnego z istniejącym użytkownikiem w usłudze Azure AD** Na przykład użytkownik, który został utworzony w AD DS, generuje duplikat na koncie usługi Azure AD, a nie dopasowuje go przy użyciu istniejącego konta usługi Azure AD (obiektu oddzielonego). Rozwiązanie: Użyj Azure AD Connect Health w Azure Portal, aby ponownie zmapować kotwicę źródłową/ImmutableID. Aby uzyskać więcej informacji, zobacz [scenariusz obiektu oddzielonego](/azure/active-directory/hybrid/how-to-connect-health-diagnose-sync-errors#orphaned-object-scenario).

### <a name="breaking-change-updates-to-the-audit-and-sign-in-logs-schema-through-azure-monitor"></a>Zmiana istotna: aktualizacje schematu inspekcji i dzienników logowania za pomocą Azure Monitor

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Reporting  
**Możliwość produktu:** Monitorowanie & raportowania

Obecnie publikujemy strumienie inspekcji i logowania za pomocą Azure Monitor, dzięki czemu możesz bezproblemowo zintegrować pliki dziennika z narzędziami SIEM lub z Log Analytics. Na podstawie opinii i przygotowania do ogólnej dostępności tej funkcji wprowadzamy następujące zmiany w naszym schemacie. Te zmiany schematu i powiązane z nimi aktualizacje dokumentacji będą wykonywane przez pierwszy tydzień stycznia.

#### <a name="new-fields-in-the-audit-schema"></a>Nowe pola w schemacie inspekcji
Dodajemy nowe pole **typu operacji** , aby zapewnić typ operacji wykonanej na zasobie. Na przykład **Dodaj**, **zaktualizuj**lub **Usuń**.

#### <a name="changed-fields-in-the-audit-schema"></a>Zmieniono pola w schemacie inspekcji
Następujące pola są zmieniane w schemacie inspekcji:

|Nazwa pola|Co zostało zmienione|Stare wartości|Nowe wartości|
|----------|------------|----------|----------|
|Kategoria|To jest pole **nazwa usługi** . Jest teraz polem **kategorie inspekcji** . **Nazwa usługi** została zmieniona na pole **loggedByService** .|<ul><li>Aprowizacja kont</li><li>Katalog podstawowy</li><li>Samoobsługowe resetowanie hasła</li></ul>|<ul><li>Zarządzanie użytkownikami</li><li>Zarządzanie grupami</li><li>Zarządzanie aplikacjami</li></ul>|
|targetResources|Obejmuje **TargetResourceType** na najwyższego poziomu.|&nbsp;|<ul><li>Zasady</li><li>aplikacja</li><li>Użytkownik</li><li>Grupa</li></ul>|
|loggedByService|Zawiera nazwę usługi, która wygenerowała dziennik inspekcji.|Null|<ul><li>Aprowizacja kont</li><li>Katalog podstawowy</li><li>Samoobsługowe resetowanie haseł</li></ul>|
|Wynik|Zawiera wyniki dzienników inspekcji. Wcześniej było to wyliczone, ale teraz pokazywane są rzeczywiste wartości.|<ul><li>0</li><li>1</li></ul>|<ul><li>Powodzenie</li><li>Niepowodzenie</li></ul>|

#### <a name="changed-fields-in-the-sign-in-schema"></a>Zmieniono pola w schemacie logowania
Następujące pola są zmieniane w schemacie logowania:

|Nazwa pola|Co zostało zmienione|Stare wartości|Nowe wartości|
|----------|------------|----------|----------|
|appliedConditionalAccessPolicies|To było pole **conditionalaccessPolicies** . To teraz pole **appliedConditionalAccessPolicies** .|Bez zmian|Bez zmian|
|conditionalAccessStatus|Zapewnia wynik stanu zasad dostępu warunkowego podczas logowania. Wcześniej było to wyliczone, ale teraz pokazywane są rzeczywiste wartości.|<ul><li>0</li><li>1</li><li>2</li><li>3</li></ul>|<ul><li>Powodzenie</li><li>Niepowodzenie</li><li>Nie zastosowano</li><li>Disabled (Wyłączony)</li></ul>|
|appliedConditionalAccessPolicies: wynik|Zapewnia wynik poszczególnych stanów zasad dostępu warunkowego podczas logowania. Wcześniej było to wyliczone, ale teraz pokazywane są rzeczywiste wartości.|<ul><li>0</li><li>1</li><li>2</li><li>3</li></ul>|<ul><li>Powodzenie</li><li>Niepowodzenie</li><li>Nie zastosowano</li><li>Disabled (Wyłączony)</li></ul>|

Aby uzyskać więcej informacji na temat schematu, zobacz [interpretowanie schematu dzienników inspekcji usługi Azure AD w Azure monitor (wersja zapoznawcza)](https://docs.microsoft.com/azure/active-directory/reports-monitoring/reference-azure-monitor-audit-log-schema)

---

### <a name="identity-protection-improvements-to-the-supervised-machine-learning-model-and-the-risk-score-engine"></a>Ulepszenia ochrony tożsamości dla nadzorowanego modelu uczenia maszynowego i aparatu oceny ryzyka

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Ochrona tożsamości  
**Możliwość produktu:** Oceny ryzyka

Ulepszenia aparatu do oceny ryzyka związanego z ochroną tożsamości oraz możliwości logowania mogą pomóc w zwiększeniu dokładności i zakresu ryzyka dla użytkowników. Administratorzy mogą zauważyć, że poziom ryzyka użytkownika nie jest już bezpośrednio połączony z poziomem ryzyka specyficznych wykryć i że występuje wzrost liczby i poziomu ryzykownych zdarzeń związanych z logowaniem.

Wykrywanie ryzyka jest teraz oceniane przez nadzorowany model uczenia maszynowego, który oblicza ryzyko użytkownika przy użyciu dodatkowych funkcji logowania użytkownika i wzorca wykrywania. W oparciu o ten model administrator może znaleźć użytkowników z wynikami wysokiego ryzyka, nawet jeśli wykrycia związane z tym użytkownikiem mają niski lub średni ryzyko. 

---

### <a name="administrators-can-reset-their-own-password-using-the-microsoft-authenticator-app-public-preview"></a>Administratorzy mogą zresetować własne hasło przy użyciu aplikacji Microsoft Authenticator (publiczna wersja zapoznawcza)

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Samoobsługowe resetowanie hasła  
**Możliwość produktu:** Uwierzytelnianie użytkownika

Administratorzy usługi Azure AD mogą teraz resetować swoje hasła przy użyciu powiadomień aplikacji Microsoft Authenticator lub kodu z dowolnej aplikacji lub tokenu sprzętowego usług uwierzytelniania mobilnego. Aby zresetować własne hasło, Administratorzy mogą teraz korzystać z dwóch następujących metod:

- Powiadomienie Microsoft Authenticator aplikacji

- Inna aplikacja/token sprzętowy do uwierzytelniania mobilnego

- Adres e-mail

- Połączenie telefoniczne

- Wiadomość SMS

Aby uzyskać więcej informacji na temat używania aplikacji Microsoft Authenticator do resetowania haseł, zobacz samoobsługowe [resetowanie haseł w usłudze Azure AD — aplikacja mobilna i SSPR (wersja zapoznawcza)](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-howitworks#mobile-app-and-sspr)

---

### <a name="new-azure-ad-cloud-device-administrator-role-public-preview"></a>Nowa rola administratora urządzenia w chmurze usługi Azure AD (publiczna wersja zapoznawcza)

**Typ:** Nowa funkcja  
**Kategoria usługi:** Rejestrowanie urządzeń i zarządzanie nimi  
**Możliwość produktu:** Kontrola dostępu

Administratorzy mogą przypisywać użytkowników do nowej roli administratora urządzenia w chmurze w celu wykonywania zadań administratora urządzenia w chmurze. Użytkownicy z przypisaną rolą Administratorzy urządzeń w chmurze mogą włączać, wyłączać i usuwać urządzenia w usłudze Azure AD oraz mogą odczytywać klucze funkcji BitLocker systemu Windows 10 (jeśli istnieją) w Azure Portal.

Aby uzyskać więcej informacji o rolach i uprawnieniach, zobacz [Przypisywanie ról administratorów w Azure Active Directory](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)

---

### <a name="manage-your-devices-using-the-new-activity-timestamp-in-azure-ad-public-preview"></a>Zarządzanie urządzeniami przy użyciu nowej sygnatury czasowej działania w usłudze Azure AD (publiczna wersja zapoznawcza)

**Typ:** Nowa funkcja  
**Kategoria usługi:** Rejestrowanie urządzeń i zarządzanie nimi  
**Możliwość produktu:** Zarządzanie cyklem życia urządzeń

Zdajemy sobie sprawę, że w miarę upływu czasu musisz odświeżyć i wycofać urządzenia organizacji w usłudze Azure AD, aby uniknąć używania starych urządzeń w środowisku. Aby pomóc w tym procesie, usługa Azure AD teraz aktualizuje urządzenia przy użyciu nowej sygnatury czasowej działania, co ułatwia zarządzanie cyklem życia urządzenia.

Aby uzyskać więcej informacji o tym, jak uzyskać i korzystać z tej sygnatury czasowej, zobacz [How to: Manage The Devices in Azure AD](https://docs.microsoft.com/azure/active-directory/devices/manage-stale-devices)

---

### <a name="administrators-can-require-users-to-accept-a-terms-of-use-on-each-device"></a>Administratorzy mogą wymagać od użytkowników zaakceptowania warunków użytkowania na poszczególnych urządzeniach

**Typ:** Nowa funkcja  
**Kategoria usługi:** Warunki użytkowania  
**Możliwość produktu:** Dobrego
 
Administratorzy mogą teraz włączyć opcję **Wymagaj od użytkowników zgody na każde urządzenie** , aby wymagać od użytkowników akceptacji warunków użytkowania na każdym urządzeniu, na którym są używane w dzierżawie.

Aby uzyskać więcej informacji, zobacz [sekcję warunki użytkowania poszczególnych urządzeń w funkcji Azure Active Directory warunki użytkowania](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use#per-device-terms-of-use).

---

### <a name="administrators-can-configure-a-terms-of-use-to-expire-based-on-a-recurring-schedule"></a>Administratorzy mogą skonfigurować warunki użytkowania, aby wygasnąć na podstawie harmonogramu cyklicznego

**Typ:** Nowa funkcja  
**Kategoria usługi:** Warunki użytkowania  
**Możliwość produktu:** Dobrego
 

Administratorzy mogą teraz włączyć opcję **wygasania** , aby wygaśnie warunki użytkowania dla wszystkich użytkowników na podstawie określonego harmonogramu cyklicznego. Harmonogram może być roczny, dwuroczny, kwartalny lub miesięczny. Po wygaśnięciu warunków użytkowania użytkownicy muszą ponownie zaakceptować.

Aby uzyskać więcej informacji, zobacz [sekcję Dodawanie warunków użytkowania w artykule Azure Active Directory warunki użytkowania](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use#add-terms-of-use).

---

### <a name="administrators-can-configure-a-terms-of-use-to-expire-based-on-each-users-schedule"></a>Administratorzy mogą skonfigurować warunki użytkowania, aby wygasnąć na podstawie harmonogramu każdego użytkownika

**Typ:** Nowa funkcja  
**Kategoria usługi:** Warunki użytkowania  
**Możliwość produktu:** Dobrego

Administratorzy mogą teraz określić czas trwania, przez który użytkownik musi ponownie zaakceptować warunki użytkowania. Administratorzy mogą na przykład określić, że użytkownicy muszą ponownie akceptować warunki użytkowania co 90 dni.

Aby uzyskać więcej informacji, zobacz [sekcję Dodawanie warunków użytkowania w artykule Azure Active Directory warunki użytkowania](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use#add-terms-of-use).
 
---

### <a name="new-azure-ad-privileged-identity-management-pim-emails-for-azure-active-directory-roles"></a>Nowe wiadomości e-mail w usłudze Azure AD Privileged Identity Management (PIM) dla ról Azure Active Directory

**Typ:** Nowa funkcja  
**Kategoria usługi:** Privileged Identity Management  
**Możliwość produktu:** Privileged Identity Management
 
Klienci korzystający z programu Azure AD Privileged Identity Management (PIM) mogą teraz odbierać cotygodniowe wiadomości e-mail z podsumowaniem, w tym następujące informacje w ciągu ostatnich siedmiu dni:

- Przegląd najbardziej uprawnionych i trwałych przypisań ról

- Liczba użytkowników, którzy aktywują role

- Liczba użytkowników przypisanych do ról w usłudze PIM

- Liczba użytkowników przypisanych do ról poza usługą PIM

- Liczba użytkowników trwałych w usłudze PIM

Aby uzyskać więcej informacji na temat usługi PIM i dostępnych powiadomień e-mail, zobacz [powiadomienia e-mail w usłudze PIM](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-email-notifications).

---

### <a name="group-based-licensing-is-now-generally-available"></a>Licencjonowanie oparte na grupach jest teraz ogólnie dostępne

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Różnych  
**Możliwość produktu:** Katalogi

Licencjonowanie oparte na grupach jest w publicznej wersji zapoznawczej i jest teraz ogólnie dostępne. W ramach tej ogólnej wersji ta funkcja jest bardziej skalowalna i dodaliśmy możliwość przetworzenia przypisań licencjonowania opartego na grupach dla jednego użytkownika i możliwość korzystania z licencjonowania opartego na grupach z licencjami pakietu Office 365 E3/a3.

Aby uzyskać więcej informacji na temat licencjonowania opartego na grupach, zobacz [co to jest Licencjonowanie oparte na grupach w Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-licensing-whatis-azure-portal)

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---november-2018"></a>Nowe aplikacje federacyjne dostępne w galerii aplikacji usługi Azure AD — listopad 2018

**Typ:** Nowa funkcja  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwości produktu:** integracja innej firmy
 
W listopadzie 2018 dodaliśmy następujące 26 nowych aplikacji z obsługą Federacji do galerii aplikacji:

[CoreStack](https://cloud.corestack.io/site/login), [HubSpot](https://docs.microsoft.com/azure/active-directory/saas-apps/HubSpot-tutorial), [](https://docs.microsoft.com/azure/active-directory/saas-apps/getthere-tutorial)Getin, [gra-PE](https://docs.microsoft.com/azure/active-directory/saas-apps/grape-tutorial), [eHour](https://getehour.com/try-now), [Consent2Go](https://docs.microsoft.com/azure/active-directory/saas-apps/Consent2Go-tutorial), [Appinux](https://docs.microsoft.com/azure/active-directory/saas-apps/appinux-tutorial), [DriveDollar](https://azuremarketplace.microsoft.com/marketplace/apps/savitas.drivedollar-azuread?tab=Overview), [Useall](https://docs.microsoft.com/azure/active-directory/saas-apps/useall-tutorial), [nieograniczone kampus](https://docs.microsoft.com/azure/active-directory/saas-apps/infinitecampus-tutorial), [Alaya](https://alayagood.com/en/demo/), [HeyBuddy](https://docs.microsoft.com/azure/active-directory/saas-apps/heybuddy-tutorial), [ Wrike SAML](https://docs.microsoft.com/azure/active-directory/saas-apps/wrike-tutorial), [dryf](https://docs.microsoft.com/azure/active-directory/saas-apps/drift-tutorial), [Zenegy for Business Central 365](https://accounting.zenegy.com/), [EverBridge Portal członków](https://docs.microsoft.com/azure/active-directory/saas-apps/everbridge-tutorial), [ideo](https://profile.ideo.com/users/sign_up), [Ivanti Service Manager (ISM)](https://docs.microsoft.com/azure/active-directory/saas-apps/ivanti-service-manager-tutorial), [Peakon](https://docs.microsoft.com/azure/active-directory/saas-apps/peakon-tutorial), [Allbound SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/allbound-sso-tutorial), [aplikacje typu plex — test klasyczny ](https://test.plexonline.com/signon), [Aplikacje typu plex — klasyczne](https://www.plexonline.com/signon), [aplikacje typu plex — test środowiska](https://test.cloud.plex.com/sso)użytkownika, [aplikacje obiektów typu plex — UX](https://cloud.plex.com/sso), [aplikacje typu plex — IAM](https://accounts.plex.com/), [łodzie, zapisy, frekwencja, & finansowy system śledzenia](https://getcrafts.ca/craftsregistration) 

Aby uzyskać więcej informacji na temat aplikacji, zobacz [SaaS Application Integration with Azure Active Directory](https://aka.ms/appstutorial). Aby uzyskać więcej informacji na temat tworzenia listy aplikacji w galerii aplikacji usługi Azure AD, zobacz [Wyświetlanie listy aplikacji w galerii aplikacji Azure Active Directory](https://aka.ms/azureadapprequest).

---

## <a name="october-2018"></a>Październik 2018 r.

### <a name="azure-ad-logs-now-work-with-azure-log-analytics-public-preview"></a>Dzienniki usługi Azure AD teraz pracują z usługą Azure Log Analytics (publiczna wersja zapoznawcza)

**Typ:** Nowa funkcja  
**Kategoria usługi:** Reporting  
**Możliwość produktu:** Monitorowanie & raportowania

Przyjemnością się, że możesz teraz przekazać dzienniki usługi Azure AD do usługi Azure Log Analytics! Ta funkcja w górnej części pomaga zapewnić jeszcze lepszy dostęp do analiz dla Twojej firmy, operacji i zabezpieczeń, a także ułatwić monitorowanie infrastruktury. Aby uzyskać więcej informacji, zobacz [dzienniki aktywności Azure Active Directory w usłudze Azure log Analytics teraz dostępny](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-Active-Directory-Activity-logs-in-Azure-Log-Analytics-now/ba-p/274843) blog.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---october-2018"></a>Nowe aplikacje federacyjne dostępne w galerii aplikacji usługi Azure AD — październik 2018

**Typ:** Nowa funkcja  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwości produktu:** integracja innej firmy

W październiku 2018 dodaliśmy następujące 14 nowych aplikacji z obsługą Federacji do galerii aplikacji:

[Moje punkty nagrody](https://docs.microsoft.com/azure/active-directory/saas-apps/myawardpoints-tutorial), [Vibe HCM](https://docs.microsoft.com/azure/active-directory/saas-apps/vibehcm-tutorial), ambyint, [MyWorkDrive](https://docs.microsoft.com/azure/active-directory/saas-apps/myworkdrive-tutorial), [BorrowBox](https://docs.microsoft.com/azure/active-directory/saas-apps/borrowbox-tutorial), Dialpad, [ON24 Virtual Environment](https://docs.microsoft.com/azure/active-directory/saas-apps/on24-tutorial), [RingCentral](https://docs.microsoft.com/azure/active-directory/saas-apps/ringcentral-tutorial), [rozwiązania Zscaler trzy](https://docs.microsoft.com/azure/active-directory/saas-apps/zscaler-three-tutorial), [Phraseanet](https://docs.microsoft.com/azure/active-directory/saas-apps/phraseanet-tutorial), [Appraisd](https://docs.microsoft.com/azure/active-directory/saas-apps/appraisd-tutorial), [Workspot Kontrolka](https://docs.microsoft.com/azure/active-directory/saas-apps/workspotcontrol-tutorial), [Shuccho Navi](https://docs.microsoft.com/azure/active-directory/saas-apps/shucchonavi-tutorial), [Glassfrog](https://docs.microsoft.com/azure/active-directory/saas-apps/glassfrog-tutorial)

Aby uzyskać więcej informacji na temat aplikacji, zobacz [SaaS Application Integration with Azure Active Directory](https://aka.ms/appstutorial). Aby uzyskać więcej informacji na temat tworzenia listy aplikacji w galerii aplikacji usługi Azure AD, zobacz [Wyświetlanie listy aplikacji w galerii aplikacji Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="azure-ad-domain-services-email-notifications"></a>Azure AD Domain Services powiadomienia E-mail

**Typ:** Nowa funkcja  
**Kategoria usługi:** Azure AD Domain Services  
**Możliwość produktu:** Azure AD Domain Services

Azure AD Domain Services zapewnia alerty dotyczące Azure Portal dotyczących błędów konfiguracji lub problemów z domeną zarządzaną. Te alerty obejmują Przewodniki krok po kroku, dzięki czemu można spróbować rozwiązać problemy bez konieczności kontaktowania się z pomocą techniczną.

Od października będzie można dostosować ustawienia powiadomień dla domeny zarządzanej, tak aby w przypadku wystąpienia nowych alertów wiadomość e-mail została wysłana do wygenerowanej grupy osób, co eliminuje konieczność stałego sprawdzenia portalu pod kątem aktualizacji.

Aby uzyskać więcej informacji, zobacz [Ustawienia powiadomień w Azure AD Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-notifications).

---

### <a name="azure-ad-portal-supports-using-the-forcedelete-domain-api-to-delete-custom-domains"></a>Portal usługi Azure AD obsługuje używanie interfejsu API domeny ForceDelete do usuwania domen niestandardowych 

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Zarządzanie katalogami  
**Możliwość produktu:** Katalogi

Z przyjemnością ogłaszamy, że możesz teraz używać interfejsu API domeny ForceDelete do usuwania niestandardowych nazw domen, asynchronicznie zmieniając nazwy odwołań, takich jak użytkownicy, grupy i aplikacje, z niestandardowej nazwy domeny (contoso.com) z powrotem do początkowej domyślnej nazwy domeny ( contoso.onmicrosoft.com).

Ta zmiana ułatwia szybkie usuwanie niestandardowych nazw domen, jeśli organizacja nie używa już nazwy lub jeśli musisz użyć nazwy domeny z inną usługą Azure AD.

Aby uzyskać więcej informacji, zobacz [usuwanie niestandardowej nazwy domeny](https://docs.microsoft.com/azure/active-directory/users-groups-roles/domains-manage#delete-a-custom-domain-name).

---

## <a name="september-2018"></a>Wrzesień 2018 r.
 
### <a name="updated-administrator-role-permissions-for-dynamic-groups"></a>Zaktualizowano uprawnienia roli administratora dla grup dynamicznych

**Typ:** FIXED  
**Kategoria usługi:** Zarządzanie grupami  
**Możliwość produktu:** Społeczności

Rozwiązano problem, dzięki czemu określone role administratorów mogą teraz tworzyć i aktualizować dynamiczne reguły członkostwa, bez konieczności być właścicielem grupy.

Role są następujące:

- Administrator globalny

- Administrator usługi Intune

- Administrator użytkowników

Aby uzyskać więcej informacji, zobacz [Tworzenie grupy dynamicznej i sprawdzanie stanu](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-create-rule)

---

### <a name="simplified-single-sign-on-sso-configuration-settings-for-some-third-party-apps"></a>Uproszczone ustawienia konfiguracji logowania jednokrotnego (SSO) dla niektórych aplikacji innych firm

**Typ:** Nowa funkcja  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwość produktu:** ZWRÓCIŁ

Zdajemy sobie sprawę, że skonfigurowanie logowania jednokrotnego (SSO) dla aplikacji oprogramowania jako usługi (SaaS) może być trudne ze względu na unikatowy charakter każdej konfiguracji aplikacji. Utworzyliśmy uproszczone środowisko konfiguracji, aby automatycznie wypełnić ustawienia konfiguracji logowania jednokrotnego dla następujących aplikacji SaaS innych firm:

- Zendesk

- ArcGis w trybie online

- Jamf Pro

Aby rozpocząć korzystanie z tego samego kliknięcia, przejdź do strony **Konfiguracja logowania jednokrotnego** w **Azure Portal** > dla aplikacji. Aby uzyskać więcej informacji, zobacz [SaaS Application Integration with Azure Active Directory](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list)

---

### <a name="azure-active-directory---where-is-your-data-located-page"></a>Azure Active Directory — gdzie znajdują się Twoje dane? stronic

**Typ:** Nowa funkcja  
**Kategoria usługi:** Różnych  
**Możliwość produktu:** GoLocal

Wybierz region firmy na stronie **Azure Active Directory — gdzie znajdują się Twoje dane** , aby zobaczyć, które centrum danych platformy Azure przechowuje dane usługi Azure AD w spoczynku dla wszystkich usług Azure AD. Informacje można filtrować według określonych usług Azure AD dla regionu firmy.

Aby uzyskać dostęp do tej funkcji i uzyskać więcej informacji, zobacz [Azure Active Directory — gdzie znajdują się Twoje dane](https://aka.ms/AADDataMap).

---

### <a name="new-deployment-plan-available-for-the-my-apps-access-panel"></a>Nowy plan wdrożenia dostępny dla panelu dostępu moje aplikacje

**Typ:** Nowa funkcja  
**Kategoria usługi:** Moje aplikacje  
**Możliwość produktu:** ZWRÓCIŁ

Zapoznaj się z nowym planem wdrożenia dostępnym dla panelu dostępu moje aplikacje (https://aka.ms/deploymentplans).
Panel dostępu moje aplikacje udostępnia użytkownikom pojedyncze miejsce do znajdowania i uzyskiwania dostępu do aplikacji. Ten portal udostępnia również użytkownikom możliwości samoobsługi, takich jak żądanie dostępu do aplikacji i grup lub zarządzanie dostępem do tych zasobów w imieniu innych użytkowników.

Aby uzyskać więcej informacji, zobacz artykuł [co to jest portal moje aplikacje?](https://docs.microsoft.com/azure/active-directory/user-help/active-directory-saas-access-panel-introduction)

---

### <a name="new-troubleshooting-and-support-tab-on-the-sign-ins-logs-page-of-the-azure-portal"></a>Nowa karta Rozwiązywanie problemów i pomoc techniczna na stronie dzienniki logowania Azure Portal

**Typ:** Nowa funkcja  
**Kategoria usługi:** Reporting  
**Możliwość produktu:** Monitorowanie & raportowania

Nowa karta **Rozwiązywanie problemów i pomoc techniczna** na stronie **logowania** Azure Portal ma pomóc administratorom i inżynierom pomocy technicznej rozwiązywać problemy związane z logowaniem do usługi Azure AD. Ta nowa karta zawiera kod błędu, komunikat o błędzie i zalecenia dotyczące korygowania (jeśli istnieją), aby pomóc w rozwiązaniu problemu. Jeśli nie możesz rozwiązać tego problemu, udostępnimy również nowy sposób tworzenia biletu pomocy technicznej przy użyciu funkcji **kopiowania do schowka** , która wypełnia pola **Identyfikator żądania** i **Data (UTC)** dla pliku dziennika w biletu pomocy technicznej.  

![Dzienniki logowania pokazujące nową kartę](media/whats-new/troubleshooting-and-support.png)

---

### <a name="enhanced-support-for-custom-extension-properties-used-to-create-dynamic-membership-rules"></a>Ulepszona obsługa niestandardowych właściwości rozszerzeń używanych do tworzenia reguł członkostwa dynamicznego

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Zarządzanie grupami  
**Możliwość produktu:** Społeczności

Dzięki tej aktualizacji można teraz kliknąć link **Pobierz niestandardowe właściwości rozszerzenia** z konstruktora reguły dynamicznej grupy użytkowników, wprowadzić unikatowy identyfikator aplikacji i otrzymać pełną listę niestandardowych właściwości rozszerzenia, które będą używane podczas tworzenia reguły członkostwa dynamicznego dla użytkowników. Tę listę można również odświeżyć, aby uzyskać nowe niestandardowe właściwości rozszerzenia dla tej aplikacji.

Aby uzyskać więcej informacji o używaniu niestandardowych właściwości rozszerzenia dla reguł członkostwa dynamicznego, zobacz [Właściwości rozszerzenia i niestandardowe właściwości rozszerzenia](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership#extension-properties-and-custom-extension-properties) .

---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Nowe zatwierdzone aplikacje klienckie dla dostępu warunkowego opartego na aplikacji usługi Azure AD

**Typ:** Planowanie zmiany  
**Kategoria usługi:** Dostęp warunkowy  
**Możliwość produktu:** Bezpieczeństwo i Ochrona tożsamości

Następujące aplikacje znajdują się na liście [zatwierdzonych aplikacji klienckich](https://docs.microsoft.com/azure/active-directory/conditional-access/technical-reference#approved-client-app-requirement):

- Microsoft To-Do

- Microsoft Stream

Aby uzyskać więcej informacji, zobacz:

- [Dostęp warunkowy oparty na aplikacji usługi Azure AD](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)

---

### <a name="new-support-for-self-service-password-reset-from-the-windows-7881-lock-screen"></a>Nowa Obsługa funkcji samoobsługowego resetowania haseł z poziomu ekranu blokady systemu Windows 7/8/8.1

**Typ:** Nowa funkcja  
**Kategoria usługi:** SSPR  
**Możliwość produktu:** Uwierzytelnianie użytkownika

Po skonfigurowaniu tej nowej funkcji użytkownicy zobaczą link do resetowania hasła na ekranie **blokady** urządzenia z systemem Windows 7, Windows 8 lub Windows 8.1. Kliknięcie tego linku powoduje przeprowadzenie tego samego przepływu resetowania hasła, tak jak w przypadku przeglądarki sieci Web.

Aby uzyskać więcej informacji, zobacz [jak włączyć resetowanie haseł w systemach Windows 7, 8 i 8,1](https://aka.ms/ssprforwindows78)

---

### <a name="change-notice-authorization-codes-will-no-longer-be-available-for-reuse"></a>Zmiana powiadomienia: kody autoryzacji nie będą już dostępne do ponownego użycia 

**Typ:** Planowanie zmiany  
**Kategoria usługi:** Uwierzytelnienia (nazwy logowania)  
**Możliwość produktu:** Uwierzytelnianie użytkownika

Od 15 listopada 2018 usługa Azure AD nie będzie akceptować poprzednio używanych kodów uwierzytelniania dla aplikacji. Ta zmiana zabezpieczeń pomaga w przeniesieniu usługi Azure AD zgodnie ze specyfikacją OAuth i wymuszać zarówno punkty końcowe V1, jak i v2.

Jeśli aplikacja ponownie używa kodów autoryzacji w celu uzyskania tokenów dla wielu zasobów, zalecamy użycie kodu w celu uzyskania tokenu odświeżania, a następnie użycie tego tokenu odświeżania do uzyskania dodatkowych tokenów dla innych zasobów. Kody autoryzacji mogą być używane tylko raz, ale tokeny odświeżania można wielokrotnie używać w wielu zasobach. Aplikacja próbująca ponownie użyć kodu uwierzytelniania podczas przepływu kodu OAuth spowoduje wystąpienie błędu invalid_grant.

Aby zapoznać się z tymi i innymi zmianami dotyczącymi protokołów, zapoznaj [się z pełną listą nowości związanych z uwierzytelnianiem](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---september-2018"></a>Nowe aplikacje federacyjne dostępne w galerii aplikacji usługi Azure AD — wrzesień 2018

**Typ:** Nowa funkcja  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwości produktu:** integracja innej firmy
 
We wrześniu 2018 dodaliśmy te 16 nowych aplikacji z obsługą Federacji do galerii aplikacji:

[Uberflip](https://docs.microsoft.com/azure/active-directory/saas-apps/uberflip-tutorial), [Współspotykać oprogramowanie do rekrutacji](https://docs.microsoft.com/azure/active-directory/saas-apps/comeetrecruitingsoftware-tutorial), [Workteam](https://docs.microsoft.com/azure/active-directory/saas-apps/workteam-tutorial), [ArcGIS Enterprise](https://docs.microsoft.com/azure/active-directory/saas-apps/arcgisenterprise-tutorial), [Nuclino](https://docs.microsoft.com/azure/active-directory/saas-apps/nuclino-tutorial), [JDA Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/jdacloud-tutorial), [płatki śniegu](https://docs.microsoft.com/azure/active-directory/saas-apps/snowflake-tutorial), NavigoCloud, [Figma, join.me](https://docs.microsoft.com/azure/active-directory/saas-apps/figma-tutorial), [ZephyrSSO](https://docs.microsoft.com/azure/active-directory/saas-apps/zephyrsso-tutorial), [Silverback](https://docs.microsoft.com/azure/active-directory/saas-apps/silverback-tutorial), Riverbed Xirrus EasyPass, [RACKSPACE SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/rackspacesso-tutorial), Enlyft SSO dla platformy Azure, SurveyMonkey, [zwoływanie](https://docs.microsoft.com/azure/active-directory/saas-apps/convene-tutorial), [dmarcian](https://docs.microsoft.com/azure/active-directory/saas-apps/dmarcian-tutorial)

Aby uzyskać więcej informacji na temat aplikacji, zobacz [SaaS Application Integration with Azure Active Directory](https://aka.ms/appstutorial). Aby uzyskać więcej informacji na temat tworzenia listy aplikacji w galerii aplikacji usługi Azure AD, zobacz [Wyświetlanie listy aplikacji w galerii aplikacji Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="support-for-additional-claims-transformations-methods"></a>Obsługa dodatkowych metod przekształceń oświadczeń

**Typ:** Nowa funkcja  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwość produktu:** ZWRÓCIŁ

Wprowadziliśmy nowe metody przekształcania roszczeń, ToLower () i ToUpper (), które można zastosować do tokenów SAML na stronie **konfiguracji logowania** jednokrotnego opartego na protokole SAML.

Aby uzyskać więcej informacji, zobacz [How to Dostosowywanie oświadczeń wystawionych w tokenie SAML dla aplikacji dla przedsiębiorstw w usłudze Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization)

---

### <a name="updated-saml-based-app-configuration-ui-preview"></a>Zaktualizowano interfejs użytkownika konfiguracji aplikacji opartej na protokole SAML (wersja zapoznawcza)

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwość produktu:** ZWRÓCIŁ

W ramach naszego zaktualizowanego interfejsu użytkownika konfiguracji aplikacji opartej na protokole SAML uzyskasz następujące korzyści:

- Zaktualizowane środowisko instruktażowe dotyczące konfigurowania aplikacji opartych na protokole SAML.

- Więcej informacji na temat braku lub nieprawidłowego działania w konfiguracji.

- Możliwość dodawania wielu adresów e-mail w celu powiadomienia o wygaśnięciu certyfikatu.

- Nowe metody przekształcania roszczeń, ToLower () i ToUpper () i nie tylko.

- Sposób przekazania własnego tokenu certyfikatu podpisywania aplikacji dla przedsiębiorstw.

- Sposób ustawiania formatu NameID dla aplikacji SAML i sposobu ustawiania wartości NameID jako rozszerzeń katalogów.

Aby włączyć ten zaktualizowany widok, kliknij link **Wypróbuj nowe środowisko** w górnej części strony **logowania** jednokrotnego. Aby uzyskać więcej informacji, zobacz [Samouczek: Konfigurowanie logowania jednokrotnego opartego na protokole SAML dla aplikacji przy użyciu Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-single-sign-on-portal).

---

## <a name="august-2018"></a>Sierpień 2018 r.

### <a name="changes-to-azure-active-directory-ip-address-ranges"></a>Zmiany w zakresach adresów IP Azure Active Directory

**Typ:** Planowanie zmiany  
**Kategoria usługi:** Różnych  
**Możliwość produktu:** Platformach

Wprowadzamy większy zakres adresów IP do usługi Azure AD, co oznacza, że w przypadku skonfigurowania zakresów adresów IP usługi Azure AD dla zapór, routerów lub grup zabezpieczeń sieci należy je zaktualizować. Wprowadzamy tę aktualizację, aby nie trzeba było ponownie zmieniać konfiguracji zakresu adresów IP zapory, routera lub sieci grupy zabezpieczeń, gdy usługa Azure AD dodaje nowe punkty końcowe. 

Ruch sieciowy jest przenoszony do tych nowych zakresów w ciągu następnych dwóch miesięcy. Aby nadal korzystać z niezakłóconej usługi, należy dodać te zaktualizowane wartości do adresów IP przed 10 września 2018:

- 20.190.128.0/18 

- 40.126.0.0/18 

Zdecydowanie zalecamy, aby nie usuwać starych zakresów adresów IP, dopóki cały ruch sieciowy nie zostanie przeniesiony do nowych zakresów. Aby uzyskać aktualizacje dotyczące przenoszenia i dowiedzieć się, kiedy można usunąć stare zakresy, zobacz [adresy URL i zakresy adresów IP usługi Office 365](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2).

---

### <a name="change-notice-authorization-codes-will-no-longer-be-available-for-reuse"></a>Zmiana powiadomienia: kody autoryzacji nie będą już dostępne do ponownego użycia 

**Typ:** Planowanie zmiany  
**Kategoria usługi:** Uwierzytelnienia (nazwy logowania)  
**Możliwość produktu:** Uwierzytelnianie użytkownika

Od 15 listopada 2018 usługa Azure AD nie będzie akceptować poprzednio używanych kodów uwierzytelniania dla aplikacji. Ta zmiana zabezpieczeń pomaga w przeniesieniu usługi Azure AD zgodnie ze specyfikacją OAuth i wymuszać zarówno punkty końcowe V1, jak i v2.

Jeśli aplikacja ponownie używa kodów autoryzacji w celu uzyskania tokenów dla wielu zasobów, zalecamy użycie kodu w celu uzyskania tokenu odświeżania, a następnie użycie tego tokenu odświeżania do uzyskania dodatkowych tokenów dla innych zasobów. Kody autoryzacji mogą być używane tylko raz, ale tokeny odświeżania można wielokrotnie używać w wielu zasobach. Aplikacja próbująca ponownie użyć kodu uwierzytelniania podczas przepływu kodu OAuth spowoduje wystąpienie błędu invalid_grant.

Aby zapoznać się z tymi i innymi zmianami dotyczącymi protokołów, zapoznaj [się z pełną listą nowości związanych z uwierzytelnianiem](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes).
 
---

### <a name="converged-security-info-management-for-self-service-password-sspr-and-multi-factor-authentication-mfa"></a>Zbieżne zarządzanie informacjami o zabezpieczeniach dla hasła samoobsługowego (SSPR) i Multi-Factor Authentication (MFA)

**Typ:** Nowa funkcja  
**Kategoria usługi:** SSPR  
**Możliwość produktu:** Uwierzytelnianie użytkownika
 
Ta nowa funkcja ułatwia osobom zarządzanie swoimi informacjami o zabezpieczeniach (na przykład numeru telefonu, aplikacji mobilnej itd.) dla usługi SSPR i uwierzytelniania wieloskładnikowego w jednej lokalizacji i doświadczeniu. w porównaniu do wcześniej, gdzie zostało wykonane w dwóch różnych lokalizacjach.

To zbieżne środowisko działa również dla osób korzystających z usługi SSPR lub MFA. Ponadto, jeśli Twoja organizacja nie wymusi rejestracji MFA lub SSPR, użytkownicy mogą nadal rejestrować wszelkie metody dotyczące zabezpieczeń MFA lub SSPR, które mogą być używane przez organizację, z portalu My Apps.

To jest publiczna wersja zapoznawcza. Administratorzy mogą włączyć nowe środowisko (w razie potrzeby) dla wybranej grupy lub dla wszystkich użytkowników w dzierżawie. Aby uzyskać więcej informacji na temat zbieżności środowiska, zobacz [Blog dotyczący zbieżności](https://cloudblogs.microsoft.com/enterprisemobility/2018/08/06/mfa-and-sspr-updates-now-in-public-preview/)

---

### <a name="new-http-only-cookies-setting-in-azure-ad-application-proxy-apps"></a>Nowe ustawienie plików cookie tylko HTTP w aplikacjach serwera proxy aplikacji usługi Azure AD

**Typ:** Nowa funkcja  
**Kategoria usługi:** Serwer proxy aplikacji  
**Możliwość produktu:** Access Control

Istnieje nowe ustawienie o nazwie, czyli **pliki cookie** w aplikacjach serwera proxy aplikacji. To ustawienie pomaga zapewnić dodatkowe zabezpieczenia, dołączając flagę HTTPOnly w nagłówku odpowiedzi HTTP w przypadku dostępu do serwera proxy aplikacji i plików cookie sesji, zatrzymując dostęp do pliku cookie ze skryptu po stronie klienta i bardziej zapobiegając tym akcjom, takich jak kopiowanie lub Modyfikowanie pliku cookie. Mimo że ta flaga nie została wcześniej użyta, pliki cookie są zawsze szyfrowane i przesyłane przy użyciu połączenia SSL, aby chronić je przed niewłaściwymi modyfikacjami.

To ustawienie nie jest zgodne z aplikacjami korzystającymi z kontrolek ActiveX, takich jak Pulpit zdalny. W tej sytuacji zalecamy wyłączenie tego ustawienia.

Aby uzyskać więcej informacji na temat ustawienia plików cookie tylko HTTP, zobacz [publikowanie aplikacji przy użyciu usługi Azure serwer proxy aplikacji usługi Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-publish-azure-portal).

---

### <a name="privileged-identity-management-pim-for-azure-resources-supports-management-group-resource-types"></a>Privileged Identity Management (PIM) dla zasobów platformy Azure obsługuje typy zasobów grupy zarządzania

**Typ:** Nowa funkcja  
**Kategoria usługi:** Privileged Identity Management  
**Możliwość produktu:** Privileged Identity Management
 
Ustawienia aktywacji just in Time i przypisania można teraz zastosować do typów zasobów grupy zarządzania, podobnie jak w przypadku subskrypcji, grup zasobów i zasobów (takich jak maszyny wirtualne, App Services i inne). Ponadto każda osoba z rolą, która zapewnia dostęp administratora dla grupy zarządzania, może odnajdywać ten zasób i zarządzać nim w usłudze PIM.

Aby uzyskać więcej informacji na temat usług PIM i zasobów platformy Azure, zobacz [odnajdywanie zasobów platformy Azure i zarządzanie nimi przy użyciu Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-resource-roles-discover-resources)
 
---

### <a name="application-access-preview-provides-faster-access-to-the-azure-ad-portal"></a>Dostęp do aplikacji (wersja zapoznawcza) zapewnia szybszy dostęp do portalu usługi Azure AD

**Typ:** Nowa funkcja  
**Kategoria usługi:** Privileged Identity Management  
**Możliwość produktu:** Privileged Identity Management
 
Obecnie w przypadku aktywowania roli przy użyciu usługi PIM, aby uprawnienia zaczęły obowiązywać, może upłynąć ponad 10 minut. Jeśli zdecydujesz się korzystać z dostępu do aplikacji, który jest obecnie dostępny w publicznej wersji zapoznawczej, Administratorzy mogą uzyskać dostęp do portalu usługi Azure AD zaraz po zakończeniu żądania aktywacji.

Obecnie dostęp do aplikacji obsługuje tylko środowisko portalu usługi Azure AD i zasoby platformy Azure. Aby uzyskać więcej informacji na temat usługi PIM i dostępu do aplikacji, zobacz [co to jest Azure AD Privileged Identity Management?](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure)
 
---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---august-2018"></a>Nowe aplikacje federacyjne dostępne w galerii aplikacji usługi Azure AD — sierpień 2018

**Typ:** Nowa funkcja  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwości produktu:** integracja innej firmy
 
W sierpniu 2018 dodaliśmy te 16 nowych aplikacji z obsługą Federacji do galerii aplikacji:

[Hornbill](https://docs.microsoft.com/azure/active-directory/saas-apps/hornbill-tutorial), [bridgeline Unbound](https://docs.microsoft.com/azure/active-directory/saas-apps/bridgelineunbound-tutorial), [sosy Labs — testowanie dla urządzeń przenośnych i sieci Web](https://docs.microsoft.com/azure/active-directory/saas-apps/saucelabs-mobileandwebtesting-tutorial), [Łączniki meta Networks](https://docs.microsoft.com/azure/active-directory/saas-apps/metanetworksconnector-tutorial), [jak to zrobisz](https://docs.microsoft.com/azure/active-directory/saas-apps/waywedo-tutorial), [Spotinst](https://docs.microsoft.com/azure/active-directory/saas-apps/spotinst-tutorial), [premaster (Inlogik)](https://docs.microsoft.com/azure/active-directory/saas-apps/promaster-tutorial), SchoolBooking, [4me](https://docs.microsoft.com/azure/active-directory/saas-apps/4me-tutorial), [Dokumentacja](https://docs.microsoft.com/azure/active-directory/saas-apps/DOSSIER-tutorial), [N2F — raporty wydatków](https://docs.microsoft.com/azure/active-directory/saas-apps/n2f-expensereports-tutorial), [Comm100 Live Chat](https://docs.microsoft.com/azure/active-directory/saas-apps/comm100livechat-tutorial), [SafeConnect](https://docs.microsoft.com/azure/active-directory/saas-apps/safeconnect-tutorial), [ZenQMS](https://docs.microsoft.com/azure/active-directory/saas-apps/zenqms-tutorial), [eLuminate](https://docs.microsoft.com/azure/active-directory/saas-apps/eluminate-tutorial), [Dovetale](https://docs.microsoft.com/azure/active-directory/saas-apps/dovetale-tutorial).

Aby uzyskać więcej informacji na temat aplikacji, zobacz [SaaS Application Integration with Azure Active Directory](https://aka.ms/appstutorial). Aby uzyskać więcej informacji na temat tworzenia listy aplikacji w galerii aplikacji usługi Azure AD, zobacz [Wyświetlanie listy aplikacji w galerii aplikacji Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="native-tableau-support-is-now-available-in-azure-ad-application-proxy"></a>Natywna obsługa Tableau jest teraz dostępna na platformie Azure serwer proxy aplikacji usługi Azure AD

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Serwer proxy aplikacji  
**Możliwość produktu:** Access Control

Nasza Aktualizacja z usługi OpenID Connect nawiązuje połączenie z protokołem uwierzytelniania OAuth 2,0 dla tego protokołu, nie trzeba już wykonywać żadnych dodatkowych czynności konfiguracyjnych, aby używać Tableau z serwerem proxy aplikacji. Ta zmiana protokołu pomaga również lepiej obsługiwać serwer proxy aplikacji, używając tylko przekierowań HTTP, które są powszechnie obsługiwane w języku JavaScript i w tagach HTML.

Aby uzyskać więcej informacji na temat naszego natywnej obsługi Tableau, zobacz [Azure serwer proxy aplikacji usługi Azure AD teraz z natywną obsługą Tableau](https://blogs.technet.microsoft.com/applicationproxyblog/2018/08/14/azure-ad-application-proxy-now-with-native-tableau-support).

---

### <a name="new-support-to-add-google-as-an-identity-provider-for-b2b-guest-users-in-azure-active-directory-preview"></a>Nowe wsparcie do dodawania usługi Google jako dostawcy tożsamości dla użytkowników gościa B2B w Azure Active Directory (wersja zapoznawcza)

**Typ:** Nowa funkcja  
**Kategoria usługi:** B2B  
**Możliwość produktu:** B2B/B2C

Przez skonfigurowanie Federacji w usłudze Google w organizacji możesz zezwolić zaproszonym użytkownikom usługi Gmail na logowanie się do udostępnionych aplikacji i zasobów przy użyciu istniejącego konta Google, bez konieczności tworzenia osobistego konta Microsoft (kont MSA) lub konta usługi Azure AD.

To jest publiczna wersja zapoznawcza. Aby uzyskać więcej informacji na temat usługi Google Federation, zobacz [Dodawanie usługi Google jako dostawcy tożsamości dla użytkowników-Gości B2B](https://docs.microsoft.com/azure/active-directory/b2b/google-federation).

---

## <a name="july-2018"></a>Lipiec 2018 r.

### <a name="improvements-to-azure-active-directory-email-notifications"></a>Ulepszenia Azure Active Directory powiadomień e-mail

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Różnych  
**Możliwość produktu:** Zarządzanie cyklem życia tożsamości
 
Wiadomości e-mail w usłudze Azure Active Directory (Azure AD) teraz oferują zaktualizowany projekt, a także zmiany w adresie e-mail nadawcy i nazwę wyświetlaną nadawcy, gdy są wysyłane z następujących usług:
 
- Przeglądy dostępu w usłudze Azure AD
- Azure AD Connect Health 
- Usługa Azure AD Identity Protection 
- Azure AD Privileged Identity Management
- Powiadomienie o wygaśnięciu certyfikatu aplikacji dla przedsiębiorstw
- Powiadomienia usługi aprowizacji aplikacji dla przedsiębiorstw
 
Powiadomienia e-mail będą wysyłane z następującego adresu e-mail i nazwy wyświetlanej:

- Adres e-mail: azure-noreply@microsoft.com
- Nazwa wyświetlana: Microsoft Azure
 
Aby zapoznać się z przykładem niektórych nowych projektów poczty e-mail i więcej informacji, zobacz [powiadomienia e-mail w usłudze Azure AD PIM](https://go.microsoft.com/fwlink/?linkid=2005832).

---

### <a name="azure-ad-activity-logs-are-now-available-through-azure-monitor"></a>Dzienniki aktywności usługi Azure AD są teraz dostępne za pomocą Azure Monitor

**Typ:** Nowa funkcja  
**Kategoria usługi:** Reporting  
**Możliwość produktu:** Monitorowanie & raportowania

Dzienniki aktywności usługi Azure AD są teraz dostępne w publicznej wersji zapoznawczej dla Azure Monitor (usługa monitorowania na poziomie platformy Azure). Azure Monitor oferuje długoterminowe przechowywanie i bezproblemową integrację, a także następujące ulepszenia:

- Długoterminowe przechowywanie przez kierowanie plików dziennika na własne konto usługi Azure Storage.

- Bezproblemowa integracja z usługą SIEM, bez konieczności pisania i konserwowania skryptów niestandardowych.

- Bezproblemowa integracja z własnymi rozwiązaniami niestandardowymi, narzędziami do analizy lub rozwiązaniami do zarządzania zdarzeniami.

Aby uzyskać więcej informacji na temat tych nowych funkcji, zobacz [dzienniki aktywności usługi Azure AD w blogu w Azure monitor Diagnostyka jest teraz w publicznej wersji zapoznawczej](https://cloudblogs.microsoft.com/enterprisemobility/2018/07/26/azure-ad-activity-logs-in-azure-monitor-diagnostics-now-in-public-preview/) i naszej dokumentacji, [Azure Active Directory dzienników aktywności w Azure monitor (wersja zapoznawcza)](https://docs.microsoft.com/azure/active-directory/reporting-azure-monitor-diagnostics-overview).

---

### <a name="conditional-access-information-added-to-the-azure-ad-sign-ins-report"></a>Informacje o dostępie warunkowym dodane do raportu logowania do usługi Azure AD

**Typ:** Nowa funkcja  
**Kategoria usługi:** Reporting  
**Możliwość produktu:** Ochrona tożsamości & zabezpieczenia
 
Ta aktualizacja pozwala zobaczyć, które zasady są oceniane po zalogowaniu się użytkownika wraz z wynikiem zasad. Ponadto raport zawiera teraz typ aplikacji klienckiej używanej przez użytkownika, aby można było zidentyfikować starszy ruch protokołu. W przypadku wpisów raportu można również przeszukać identyfikator korelacji, który można znaleźć w komunikacie o błędzie dostępnym dla użytkownika i może służyć do identyfikowania i rozwiązywania problemów dotyczących pasującego żądania logowania.

---

### <a name="view-legacy-authentications-through-sign-ins-activity-logs"></a>Wyświetlanie starszych uwierzytelnień za poorednictwem dzienników aktywności logowania

**Typ:** Nowa funkcja  
**Kategoria usługi:** Reporting  
**Możliwość produktu:** Monitorowanie & raportowania
 
Po wprowadzeniu pola **aplikacji klienta** w dziennikach aktywności logowania klienci mogą teraz zobaczyć użytkowników korzystających ze starszych uwierzytelnień. Klienci będą mogli uzyskać dostęp do tych informacji przy użyciu MS interfejs API programu Graph logowania lub dzienników aktywności logowania w portalu usługi Azure AD, w których można użyć kontroli **aplikacji klienta** do filtrowania starszych uwierzytelnień. Zapoznaj się z dokumentacją, aby uzyskać więcej szczegółów.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---july-2018"></a>Nowe aplikacje federacyjne dostępne w galerii aplikacji usługi Azure AD — lipiec 2018

**Typ:** Nowa funkcja  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwości produktu:** integracja innej firmy
 
W lipcu 2018 dodaliśmy te 16 nowych aplikacji z obsługą Federacji do galerii aplikacji:

[Centra innowacyjne](https://docs.microsoft.com/azure/active-directory/saas-apps/innovationhub-tutorial), [Leapsome](https://docs.microsoft.com/azure/active-directory/saas-apps/leapsome-tutorial), [niektóre administratora logowania jednokrotnego](https://docs.microsoft.com/azure/active-directory/saas-apps/certainadminsso-tutorial), PSUC przemieszczania, [IPass SmartConnect](https://docs.microsoft.com/azure/active-directory/saas-apps/ipasssmartconnect-tutorial), [zrzut ekranu przedstawiający-O-Matic](https://docs.microsoft.com/azure/active-directory/saas-apps/screencast-tutorial), PowerSchool ujednoliconej klasy, dołączania [do eli,](https://docs.microsoft.com/azure/active-directory/saas-apps/elionboarding-tutorial) [Zdalna pomoc techniczna](https://docs.microsoft.com/azure/active-directory/saas-apps/bomgarremotesupport-tutorial), [Bomgar ](https://docs.microsoft.com/azure/active-directory/saas-apps/nimblex-tutorial), [Wyobraź sobie webvision](https://docs.microsoft.com/azure/active-directory/saas-apps/imagineerwebvision-tutorial), [Insight4GRC](https://docs.microsoft.com/azure/active-directory/saas-apps/insight4grc-tutorial), [SecureW2 JoinNow Connector](https://docs.microsoft.com/azure/active-directory/saas-apps/securejoinnow-tutorial), [Kanbanize](https://review.docs.microsoft.com/azure/active-directory/saas-apps/kanbanize-tutorial), [SmartLPA](https://review.docs.microsoft.com/azure/active-directory/saas-apps/smartlpa-tutorial), [podstawa umiejętności](https://docs.microsoft.com/azure/active-directory/saas-apps/skillsbase-tutorial)

Aby uzyskać więcej informacji na temat aplikacji, zobacz [SaaS Application Integration with Azure Active Directory](https://aka.ms/appstutorial). Aby uzyskać więcej informacji na temat tworzenia listy aplikacji w galerii aplikacji usługi Azure AD, zobacz [Wyświetlanie listy aplikacji w galerii aplikacji Azure Active Directory](https://aka.ms/azureadapprequest).

---
 
### <a name="new-user-provisioning-saas-app-integrations---july-2018"></a>Nowi użytkownicy z integracją aplikacji SaaS — lipiec 2018

**Typ:** Nowa funkcja  
**Kategoria usługi:** Inicjowanie obsługi aplikacji  
**Możliwości produktu:** integracja innej firmy
 
Usługa Azure AD umożliwia automatyzację tworzenia, obsługi i usuwania tożsamości użytkowników w aplikacjach SaaS, takich jak Dropbox, Salesforce, usługi ServiceNow i inne. W lipcu 2018 dodaliśmy obsługę aprowizacji użytkowników dla następujących aplikacji w galerii aplikacji usługi Azure AD:

- [Cisco WebEx](https://docs.microsoft.com/azure/active-directory/saas-apps/cisco-webex-provisioning-tutorial)

- [Dodatkowe](https://docs.microsoft.com/azure/active-directory/saas-apps/bonusly-provisioning-tutorial)

Aby uzyskać listę wszystkich aplikacji, które obsługują Inicjowanie obsługi użytkowników w galerii usługi Azure AD, zobacz [SaaS Application Integration with Azure Active Directory](https://aka.ms/appstutorial).

---

### <a name="connect-health-for-sync---an-easier-way-to-fix-orphaned-and-duplicate-attribute-sync-errors"></a>Łączenie kondycji z synchronizacją — łatwiejszym sposobem naprawienia błędów synchronizacji atrybutów oddzielonych i zduplikowanych

**Typ:** Nowa funkcja  
**Kategoria usługi:** Połączenie AD  
**Możliwość produktu:** Monitorowanie & raportowania
 
Azure AD Connect Health wprowadza korekty samoobsługowe, aby pomóc w wyróżnieniu i usunięciu błędów synchronizacji. Ta funkcja rozwiązuje zduplikowane błędy synchronizacji atrybutów i naprawia obiekty oddzielone od usługi Azure AD. Ta Diagnostyka ma następujące zalety:

- Ogranicza powielone błędy synchronizacji atrybutów, dostarczając określone poprawki

- Stosuje poprawkę dla dedykowanych scenariuszy usługi Azure AD, rozwiązując błędy w jednym kroku

- Aby włączyć tę funkcję i korzystać z niej, uaktualnienie lub konfiguracja nie są wymagane

Aby uzyskać więcej informacji, zobacz [diagnozowanie i korygowanie zduplikowanych błędów synchronizacji atrybutów](https://docs.microsoft.com/azure/active-directory/connect-health/active-directory-aadconnect-health-diagnose-sync-errors)

---

### <a name="visual-updates-to-the-azure-ad-and-msa-sign-in-experiences"></a>Aktualizacje wizualne dla środowiska logowania do usługi Azure AD i MSA

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Usługa Azure AD  
**Możliwość produktu:** Uwierzytelnianie użytkownika

Zaktualizowaliśmy interfejs użytkownika dla środowiska logowania Usługi online firmy Microsoft, na przykład dla pakietu Office 365 i platformy Azure. Ta zmiana powoduje, że ekrany są mniej czytelne i bardziej proste. Aby uzyskać więcej informacji na temat tej zmiany, zobacz [nadchodzące ulepszenia blogu logowania do usługi Azure AD](https://cloudblogs.microsoft.com/enterprisemobility/2018/04/04/upcoming-improvements-to-the-azure-ad-sign-in-experience/) .

---

### <a name="new-release-of-azure-ad-connect---july-2018"></a>Nowa wersja Azure AD Connect — lipiec 2018

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Inicjowanie obsługi aplikacji  
**Możliwość produktu:** Zarządzanie cyklem życia tożsamości

Najnowsza wersja Azure AD Connect obejmuje: 

- Poprawki błędów i obsługa aktualizacji 

- Ogólna dostępność integracji polecenia ping-Sfederować

- Aktualizacje najnowszego klienta programu SQL 2012 

Aby uzyskać więcej informacji na temat tej aktualizacji, zobacz [Azure AD Connect: historia](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-version-history) wersji

---

### <a name="updates-to-the-terms-of-use-end-user-ui"></a>Aktualizacje warunków użytkowania użytkownika końcowego

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Warunki użytkowania  
**Możliwość produktu:** Dobrego

Aktualizujemy ciąg akceptacji w INTERFEJSie użytkownika końcowego warunków użytkowania.

**Bieżący tekst.** Aby uzyskać dostęp do zasobów [dzierżawcname], musisz zaakceptować warunki użytkowania.<br>**Nowy tekst.** Aby uzyskać dostęp do zasobu [dzierżawcname], należy przeczytać warunki użytkowania.

**Bieżący tekst:** Wybranie opcji Akceptuj oznacza, że akceptujesz wszystkie powyższe warunki użytkowania.<br>**Nowy tekst:** Kliknij przycisk Akceptuj, aby potwierdzić, że znasz i rozumiesz warunki użytkowania.

---
 
### <a name="pass-through-authentication-supports-legacy-protocols-and-applications"></a>Uwierzytelnianie przekazywane obsługuje starsze protokoły i aplikacje

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Uwierzytelnienia (nazwy logowania)  
**Możliwość produktu:** Uwierzytelnianie użytkownika
 
Uwierzytelnianie przekazywane obsługuje teraz starsze protokoły i aplikacje. Następujące ograniczenia są obecnie w pełni obsługiwane:

- Logowania użytkowników do starszych aplikacji klienckich pakietu Office, pakietu Office 2010 i pakietu Office 2013, bez konieczności korzystania z nowoczesnego uwierzytelniania.

- Dostęp do udostępniania kalendarza oraz informacji o wolnych/zajętych środowiskach programu Exchange można uzyskać tylko w pakiecie Office 2010.

- Logowania użytkowników do aplikacji klienckich Skype dla firm, które nie wymagają nowoczesnego uwierzytelniania.

- Logowania użytkowników do programu PowerShell w wersji 1,0.

- Apple Device Enrollment Program (Apple DEP) przy użyciu Asystenta ustawień systemu iOS. 

---
 
### <a name="converged-security-info-management-for-self-service-password-reset-and-multi-factor-authentication"></a>Zbieżne zarządzanie informacjami o zabezpieczeniach w celu samoobsługowego resetowania haseł i Multi-Factor Authentication

**Typ:** Nowa funkcja  
**Kategoria usługi:** SSPR  
**Możliwość produktu:** Uwierzytelnianie użytkownika

Ta nowa funkcja umożliwia użytkownikom zarządzanie informacjami o zabezpieczeniach (na przykład numeru telefonu, adresu e-mail, aplikacji mobilnej itd.) na potrzeby samoobsługowego resetowania haseł (SSPR) i Multi-Factor Authentication (MFA) w jednym środowisku. Użytkownicy nie będą już musieli rejestrować tych samych informacji zabezpieczających dla usługi SSPR i MFA w dwóch różnych środowiskach. To nowe środowisko dotyczy również użytkowników, którzy mają SSPR lub MFA.

Jeśli organizacja nie wymusza rejestracji MFA lub SSPR, użytkownicy mogą zarejestrować swoje informacje zabezpieczające za pomocą portalu **My Apps** . W tym miejscu użytkownicy mogą rejestrować wszystkie metody z włączonym uwierzytelnianiem MFA lub SSPR. 

To jest publiczna wersja zapoznawcza. Administratorzy mogą włączyć nowe środowisko (jeśli to konieczne) dla wybranej grupy użytkowników lub wszystkich użytkowników w dzierżawie.

---
 
### <a name="use-the-microsoft-authenticator-app-to-verify-your-identity-when-you-reset-your-password"></a>Użyj aplikacji Microsoft Authenticator, aby zweryfikować swoją tożsamość w przypadku zresetowania hasła

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** SSPR  
**Możliwość produktu:** Uwierzytelnianie użytkownika

Ta funkcja umożliwia administratorom innym niż administratorzy weryfikowanie tożsamości podczas resetowania hasła przy użyciu powiadomienia lub kodu z Microsoft Authenticator (lub dowolnej innej aplikacji uwierzytelniania). Gdy administratorzy włączą tę metodę samoobsługowego resetowania hasła, użytkownicy, którzy zarejestrowali aplikację mobilną za pomocą usługi aka.ms/mfasetup lub aka.ms/setupsecurityinfo, mogą używać swojej aplikacji mobilnej jako metody weryfikacji podczas resetowania hasła.

Powiadomienie aplikacji mobilnej można włączyć tylko w ramach zasad, które wymagają dwóch metod resetowania hasła.

---

## <a name="june-2018"></a>Czerwiec 2018 r.

### <a name="change-notice-security-fix-to-the-delegated-authorization-flow-for-apps-using-azure-ad-activity-logs-api"></a>Zmiana powiadomienia: Poprawka zabezpieczeń do delegowanego przepływu autoryzacji dla aplikacji korzystających z interfejsu API dzienników aktywności usługi Azure AD

**Typ:** Planowanie zmiany  
**Kategoria usługi:** Reporting  
**Możliwość produktu:** Monitorowanie & raportowania

Ze względu na nasze silniejsze Wymuszanie zabezpieczeń musiałeś wprowadzić zmianę uprawnień dla aplikacji, które korzystają z delegowanego przepływu autoryzacji w celu uzyskiwania dostępu do [interfejsów API dzienników aktywności usługi Azure AD](https://aka.ms/aadreportsapi). Ta zmiana będzie miała miejsce do **26 czerwca 2018**.

Jeśli dowolna z aplikacji korzysta z interfejsów API dzienników aktywności usługi Azure AD, wykonaj następujące kroki, aby upewnić się, że aplikacja nie przerwie się po zmianie.

**Aby zaktualizować uprawnienia aplikacji**

1. Zaloguj się do Azure Portal, wybierz pozycję **Azure Active Directory**, a następnie wybierz pozycję **rejestracje aplikacji**.
2. Wybierz aplikację korzystającą z interfejsu API dzienników aktywności usługi Azure AD, wybierz pozycję **Ustawienia**, wybierz pozycję **wymagane uprawnienia**, a następnie wybierz pozycję **Windows Azure Active Directory** API.
3. W obszarze **delegowane uprawnienia** w bloku **Włącz dostęp** zaznacz pole wyboru obok pozycji **Odczytaj dane katalogu** , a następnie wybierz pozycję **Zapisz**.
4. Wybierz pozycję **Udziel uprawnień**, a następnie wybierz pozycję **tak**.
    
    >[!Note]
    >Musisz być administratorem globalnym, aby udzielić uprawnień do aplikacji.

Aby uzyskać więcej informacji, zobacz temat [udzielanie uprawnień](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-prerequisites-azure-portal#grant-permissions) w obszarze wymagania wstępne, aby uzyskać dostęp do artykułu interfejsu API raportowania usługi Azure AD.

---

### <a name="configure-tls-settings-to-connect-to-azure-ad-services-for-pci-dss-compliance"></a>Konfigurowanie ustawień protokołu TLS w celu nawiązania połączenia z usługami Azure AD na potrzeby PCI DSS zgodności

**Typ:** Nowa funkcja  
**Kategoria usługi:** NIE DOTYCZY  
**Możliwość produktu:** Platformach

Transport Layer Security (TLS) to protokół, który zapewnia prywatność i integralność danych między dwiema komunikującymi się aplikacjami i jest najczęściej wdrożonym protokołem zabezpieczeń.

[Rada standardu zabezpieczeń PCI](https://www.pcisecuritystandards.org/) ustaliła, że wczesne wersje protokołu TLS i SSL (SSL) muszą zostać wyłączone na korzyść, aby włączyć nowe i bezpieczniejsze protokoły aplikacji, z zachowaniem zgodności rozpoczynającym się **30 czerwca 2018**. Ta zmiana oznacza, że w przypadku nawiązania połączenia z usługami Azure AD i wymagania zgodności PCI DSS należy wyłączyć protokół TLS 1,0. Dostępnych jest wiele wersji protokołu TLS, ale protokół TLS 1,2 to Najnowsza wersja dostępna dla Azure Active Directory usług. Zdecydowanie zalecamy Przechodzenie bezpośrednio do protokołu TLS 1,2 dla kombinacji klient/serwer i przeglądarka/serwer.

Nieaktualne przeglądarki mogą nie obsługiwać nowszych wersji protokołu TLS, takich jak TLS 1,2. Aby sprawdzić, które wersje protokołu TLS są obsługiwane przez przeglądarkę, przejdź do witryny [Qualyse SSL Labs](https://www.ssllabs.com/) i kliknij pozycję **Testuj przeglądarkę**. Zalecamy przeprowadzenie uaktualnienia do najnowszej wersji przeglądarki sieci Web i zaleca włączenie tylko protokołu TLS 1,2.

**Aby włączyć protokół TLS 1,2 w przeglądarce**

- **Microsoft Edge i Internet Explorer (oba są ustawiane przy użyciu programu Internet Explorer)**

    1. Otwórz program Internet Explorer, wybierz pozycję **narzędzia** > **Opcje internetowe** > **Zaawansowane**.
    2. W obszarze **zabezpieczenia** wybierz opcję **użyj protokołu TLS 1,2**, a następnie wybierz przycisk **OK**.
    3. Zamknij wszystkie okna przeglądarki i ponownie uruchom program Internet Explorer. 

- **Google Chrome**

    1. Otwórz program Google Chrome, wpisz *Chrome://Settings/* na pasku adresu i naciśnij klawisz **Enter**.
    2. Rozwiń opcje **Zaawansowane** , przejdź do obszaru **system** , a następnie wybierz pozycję **Otwórz ustawienia serwera proxy**.
    3. W oknie **Właściwości internetowe** wybierz kartę **Zaawansowane** , przejdź do obszaru **zabezpieczenia** , wybierz opcję **Użyj protokołu TLS 1,2**, a następnie wybierz przycisk **OK**.
    4. Zamknij wszystkie okna przeglądarki i uruchom ponownie usługę Google Chrome.

- **Mozilla Firefox**

    1. Otwórz przeglądarkę Firefox, wpisz *informacje: config* na pasku adresu, a następnie naciśnij klawisz **Enter**.
    2. Wyszukaj termin, *TLS*, a następnie wybierz wpis **Security. TLS. Version. Max** .
    3. Ustaw wartość na **3** , aby wymusić, aby przeglądarka korzystała z do wersji TLS 1,2, a następnie wybierz **przycisk OK**.

        >[!NOTE]
        >Firefox w wersji 60,0 obsługuje protokół TLS 1,3, więc można także ustawić wartość Security. TLS. Version. max na **4**.

    4. Zamknij wszystkie okna przeglądarki i uruchom ponownie Mozilla Firefox.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---june-2018"></a>Nowe aplikacje federacyjne dostępne w galerii aplikacji usługi Azure AD — czerwiec 2018

**Typ:** Nowa funkcja  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwości produktu:** integracja innej firmy
 
W czerwcu 2018 dodaliśmy następujące 15 nowych aplikacji z obsługą Federacji do galerii aplikacji:

[Skytap](https://docs.microsoft.com/azure/active-directory/active-directory-saas-skytap-tutorial), [rozliczanie muzyki](https://docs.microsoft.com/azure/active-directory/active-directory-saas-settlingmusic-tutorial), [obsługa tokenów SAML 1,1 — aplikacja biznesowa](https://docs.microsoft.com/azure/active-directory/active-directory-saas-saml-tutorial), [Nadstrój](https://docs.microsoft.com/azure/active-directory/active-directory-saas-supermood-tutorial), [autozadanie](https://docs.microsoft.com/azure/active-directory/active-directory-saas-autotaskendpointbackup-tutorial), [kopia zapasowa punktu końcowego](https://docs.microsoft.com/azure/active-directory/active-directory-saas-autotaskendpointbackup-tutorial), [sieci SkyHigh](https://docs.microsoft.com/azure/active-directory/active-directory-saas-skyhighnetworks-tutorial), Smartway2, [TonicDM](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tonicdm-tutorial), [Moconavi](https://docs.microsoft.com/azure/active-directory/active-directory-saas-moconavi-tutorial), [Zoho](https://docs.microsoft.com/azure/active-directory/active-directory-saas-zohoone-tutorial), [Lokalna SharePoint](https://docs.microsoft.com/azure/active-directory/active-directory-saas-sharepoint-on-premises-tutorial), [przewidywanie pakietu CX](https://docs.microsoft.com/azure/active-directory/active-directory-saas-foreseecxsuite-tutorial), [Vidyard](https://docs.microsoft.com/azure/active-directory/active-directory-saas-vidyard-tutorial), [ChronicX](https://docs.microsoft.com/azure/active-directory/active-directory-saas-chronicx-tutorial)

Aby uzyskać więcej informacji na temat aplikacji, zobacz [SaaS Application Integration with Azure Active Directory](https://aka.ms/appstutorial). Aby uzyskać więcej informacji na temat tworzenia listy aplikacji w galerii aplikacji usługi Azure AD, zobacz [Wyświetlanie listy aplikacji w galerii aplikacji Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing). 

---

### <a name="azure-ad-password-protection-is-available-in-public-preview"></a>Ochrona hasłem w usłudze Azure AD jest dostępna w publicznej wersji zapoznawczej

**Typ:** Nowa funkcja  
**Kategoria usługi:** Ochrona tożsamości  
**Możliwość produktu:** Uwierzytelnianie użytkownika

Użyj ochrony hasłem usługi Azure AD, aby wyeliminować łatwo odgadnąć hasła ze środowiska. Usunięcie tych haseł ułatwia zmniejszenie ryzyka naruszenia typu rozpylania hasła.

W programie Ochrona hasłem w usłudze Azure AD ułatwia:

- Chroń konta organizacji zarówno w usłudze Azure AD, jak i w systemie Windows Server Active Directory (AD). 
- Uniemożliwia użytkownikom korzystanie z haseł na liście ponad 500 najczęściej używanych haseł oraz o ponad 1 000 000 zmienności znaków zastępczych tych haseł.
- Administruj ochroną hasłem usługi Azure AD z pojedynczej lokalizacji w portalu usługi Azure AD, w przypadku usługi Azure AD i lokalnej usługi AD systemu Windows Server.

Aby uzyskać więcej informacji na temat ochrony hasłem w usłudze Azure AD, zobacz [eliminowanie nieprawidłowych haseł w organizacji](https://aka.ms/aadpasswordprotectiondocs).

---

### <a name="new-all-guests-conditional-access-policy-template-created-during-terms-of-use-creation"></a>Nowy szablon zasad dostępu warunkowego "Wszyscy Goście" utworzony podczas tworzenia warunków użytkowania

**Typ:** Nowa funkcja  
**Kategoria usługi:** Warunki użytkowania  
**Możliwość produktu:** Dobrego

Podczas tworzenia warunków użytkowania nowy szablon zasad dostępu warunkowego jest również tworzony dla "Wszyscy Goście" i "wszystkie aplikacje". Ten nowy szablon zasad stosuje nowo utworzone warunków użytkowania, usprawniając proces tworzenia i wymuszania dla Gości.

Aby uzyskać więcej informacji, zobacz [Azure Active Directory warunki użytkowania funkcji](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use).

---

### <a name="new-custom-conditional-access-policy-template-created-during-terms-of-use-creation"></a>Nowy "niestandardowy" szablon zasad dostępu warunkowego utworzony podczas tworzenia warunków użytkowania

**Typ:** Nowa funkcja  
**Kategoria usługi:** Warunki użytkowania  
**Możliwość produktu:** Dobrego

Podczas tworzenia warunków użytkowania tworzony jest również nowy "niestandardowy" szablon zasad dostępu warunkowego. Ten nowy szablon zasad umożliwia utworzenie warunków użytkowania, a następnie natychmiastowe przejście do bloku tworzenia zasad dostępu warunkowego, bez konieczności ręcznego nawigowania po portalu.

Aby uzyskać więcej informacji, zobacz [Azure Active Directory warunki użytkowania funkcji](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use).

---

### <a name="new-and-comprehensive-guidance-about-deploying-azure-multi-factor-authentication"></a>Nowe i kompleksowe wskazówki dotyczące wdrażania usługi Azure Multi-Factor Authentication

**Typ:** Nowa funkcja  
**Kategoria usługi:** Różnych  
**Możliwość produktu:** Ochrona tożsamości & zabezpieczenia
 
Opublikowano nowe wskazówki krok po kroku dotyczące sposobu wdrażania usługi Azure Multi-Factor Authentication (MFA) w organizacji.

Aby wyświetlić przewodnik wdrażania usługi MFA, przejdź do repozytorium [przewodniki wdrażania tożsamości](https://aka.ms/DeploymentPlans) w witrynie GitHub. Aby przekazać opinię na temat przewodników wdrażania, użyj [formularza Opinia dotycząca planu wdrożenia](https://aka.ms/deploymentplanfeedback). Jeśli masz jakieś pytania dotyczące przewodników wdrażania, skontaktuj się z nami pod adresem [IDGitDeploy](mailto:idgitdeploy@microsoft.com).

---

### <a name="azure-ad-delegated-app-management-roles-are-in-public-preview"></a>Role zarządzania aplikacjami delegowanymi usługi Azure AD znajdują się w publicznej wersji zapoznawczej

**Typ:** Nowa funkcja  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwość produktu:** Access Control

Administratorzy mogą teraz delegować zadania zarządzania aplikacjami bez przypisywania roli administratora globalnego. Nowe role i możliwości są następujące:

- **Nowe role administratorów usługi Azure AD:**

    - **Administrator aplikacji.** Umożliwia zarządzanie wszystkimi aspektami wszystkich aplikacji, w tym rejestrowaniem, ustawieniami logowania jednokrotnego, przypisaniami aplikacji i licencjonowaniem, ustawieniami serwera proxy aplikacji oraz zgodą (z wyjątkiem zasobów usługi Azure AD).

    - **Administrator aplikacji w chmurze.** Przyznaje wszystkim możliwościom administratora aplikacji, z wyjątkiem serwera proxy aplikacji, ponieważ nie zapewnia dostępu lokalnego.

    - **Deweloper aplikacji.** Przyznaje możliwość tworzenia rejestracji aplikacji, nawet jeśli opcja **Zezwalaj użytkownikom na rejestrowanie aplikacji** jest wyłączona.

- **Własność (skonfiguruj rejestrację dla aplikacji i aplikację dla przedsiębiorstw, podobnie jak w przypadku procesu własności grupy:**
 
    - **Właściciel rejestracji aplikacji.** Umożliwia zarządzanie wszystkimi aspektami rejestracji aplikacji, w tym manifestem aplikacji i dodawaniem dodatkowych właścicieli.

    - **Właściciel aplikacji przedsiębiorstwa.** Umożliwia zarządzanie wieloma aspektami należącymi do firmowych aplikacji, w tym ustawieniami logowania jednokrotnego, przypisaniami aplikacji i zgodą (z wyjątkiem zasobów usługi Azure AD).

Aby uzyskać więcej informacji na temat publicznej wersji zapoznawczej, zobacz [role zarządzania aplikacjami delegowanymi usługi Azure AD są w publicznej wersji zapoznawczej](https://cloudblogs.microsoft.com/enterprisemobility/2018/06/13/hallelujah-azure-ad-delegated-application-management-roles-are-in-public-preview/) . wpisów. Aby uzyskać więcej informacji o rolach i uprawnieniach, zobacz [Przypisywanie ról administratorów w Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal).

---

## <a name="may-2018"></a>Maj 2018 r.

### <a name="expressroute-support-changes"></a>ExpressRoute zmiany obsługi

**Typ:** Planowanie zmiany  
**Kategoria usługi:** Uwierzytelnienia (nazwy logowania)  
**Możliwość produktu:** Platformach  

Oprogramowanie jako oferta usługi, takie jak Azure Active Directory (Azure AD), jest przeznaczone do pracy najlepiej, przechodząc bezpośrednio przez Internet, bez konieczności ExpressRoute lub innych prywatnych tuneli VPN. W związku z tym w dniu **1 sierpnia 2018**ExpressRoutemy obsługę usługi Azure AD przy użyciu publicznej komunikacji równorzędnej Azure i społeczności platformy Azure w komunikacji równorzędnej firmy Microsoft. Wszystkie usługi, których dotyczy ta zmiana, mogą zauważyć, że ruch usługi Azure AD stopniowo przesunie się z ExpressRoute do Internetu.

Chociaż zmieniamy naszą pomoc techniczną, wiemy, że nadal są sytuacje, w których może być konieczne użycie dedykowanego zestawu obwodów dla ruchu związanego z uwierzytelnianiem. W związku z tym usługa Azure AD będzie kontynuowała obsługę ograniczeń zakresu adresów IP dla dzierżawców przy użyciu ExpressRoute i usług, które są już w komunikacji równorzędnej firmy Microsoft z społecznością "inne usługi Office 365 Online Services". Jeśli ma to wpływ na usługi, ale wymagane jest ExpressRoute, należy wykonać następujące czynności:

- **Jeśli korzystasz z publicznej komunikacji równorzędnej Azure.** Przejdź do komunikacji równorzędnej firmy Microsoft i zarejestruj się, aby skorzystać z **innych społeczności usługi Office 365 Online Services (12076:5100)** . Aby uzyskać więcej informacji na temat przenoszenia z publicznej komunikacji równorzędnej Azure do komunikacji równorzędnej firmy Microsoft, zobacz artykuł [przenoszenie publicznej komunikacji równorzędnej do firmy Microsoft](https://docs.microsoft.com/azure/expressroute/how-to-move-peering) .

- **Jeśli używasz komunikacji równorzędnej firmy Microsoft.** Utwórz konto w **innej społeczności usługi Office 365 Online Service (12076:5100)** . Więcej informacji o wymaganiach dotyczących routingu znajduje się w [sekcji Obsługa Wspólnot protokołu BGP](https://docs.microsoft.com/azure/expressroute/expressroute-routing#bgp) w artykule wymagania dotyczące routingu ExpressRoute.

Jeśli musisz nadal używać dedykowanych obwodów, musisz skontaktować się z zespołem ds. kont Microsoft, aby uzyskać autoryzację do korzystania z **innej społeczności usługi Office 365 Online Service (12076:5100)** . Tablica przeglądu zarządzanego przez pakiet MS Office sprawdzi, czy potrzebne są te obwody, i zadbaj o to, aby poznać techniczne konsekwencje ich utrzymania. Nieautoryzowane subskrypcje próbujące utworzyć filtry tras dla pakietu Office 365 otrzymają komunikat o błędzie. 
 
---

### <a name="microsoft-graph-apis-for-administrative-scenarios-for-tou"></a>Microsoft Graph interfejsów API dla scenariuszy administracyjnych dla warunków użytkowania

**Typ:** Nowa funkcja  
**Kategoria usługi:** Warunki użytkowania  
**Możliwość produktu:** Środowisko deweloperskie
 
Dodaliśmy Microsoft Graph interfejsów API dla operacji administrowania warunkami użytkowania usługi Azure AD. Możesz tworzyć, aktualizować i usuwać obiekty warunków użytkowania.

---

### <a name="add-azure-ad-multi-tenant-endpoint-as-an-identity-provider-in-azure-ad-b2c"></a>Dodawanie wielodostępnego punktu końcowego usługi Azure AD jako dostawcy tożsamości w Azure AD B2C

**Typ:** Nowa funkcja  
**Kategoria usługi:** B2C — Zarządzanie tożsamościami konsumentów  
**Możliwość produktu:** B2B/B2C
 
Korzystając z zasad niestandardowych, można teraz dodać wspólny punkt końcowy usługi Azure AD jako dostawcę tożsamości w Azure AD B2C. Dzięki temu można mieć pojedynczy punkt wejścia dla wszystkich użytkowników usługi Azure AD, którzy logują się do aplikacji. Aby uzyskać więcej informacji, zobacz [Azure Active Directory B2C: Zezwalanie użytkownikom na logowanie się do dostawcy tożsamości usługi Azure AD z wieloma dzierżawcami przy użyciu zasad niestandardowych](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-setup-commonaad-custom).

---

### <a name="use-internal-urls-to-access-apps-from-anywhere-with-our-my-apps-sign-in-extension-and-the-azure-ad-application-proxy"></a>Użyj wewnętrznych adresów URL, aby uzyskiwać dostęp do aplikacji z dowolnego miejsca przy użyciu rozszerzenia logowania moje aplikacje i usługi Azure serwer proxy aplikacji usługi Azure AD

**Typ:** Nowa funkcja  
**Kategoria usługi:** Moje aplikacje  
**Możliwość produktu:** ZWRÓCIŁ
 
Użytkownicy mogą teraz uzyskiwać dostęp do aplikacji za pośrednictwem wewnętrznych adresów URL nawet w przypadku, gdy poza siecią firmową, za pomocą rozszerzenia moje aplikacje bezpieczne logowanie do usługi Azure AD. Będzie to współpracować z każdą aplikacją opublikowaną przy użyciu usługi Azure serwer proxy aplikacji usługi Azure AD, w dowolnej przeglądarce, która ma także zainstalowane rozszerzenie przeglądarki panelu dostępu. Funkcja przekierowywania adresów URL jest włączana automatycznie po zalogowaniu się użytkownika do rozszerzenia. Rozszerzenie jest dostępne do pobrania w przeglądarkach [Microsoft Edge](https://go.microsoft.com/fwlink/?linkid=845176), [Chrome](https://go.microsoft.com/fwlink/?linkid=866367)i [Firefox](https://go.microsoft.com/fwlink/?linkid=866366).

---
 
### <a name="azure-active-directory---data-in-europe-for-europe-customers"></a>Azure Active Directory — dane w Europie dla klientów Europy

**Typ:** Nowa funkcja  
**Kategoria usługi:** Różnych  
**Możliwość produktu:** GoLocal

Klienci w Europie wymagają, aby dane były przechowywane w Europie i nie zostały zreplikowane poza europejskimi centrami danych w celu zachowania poufności i prawa europejskiego. Ten [artykuł](https://go.microsoft.com/fwlink/?linkid=872328) zawiera szczegółowe informacje dotyczące informacji o tożsamościach, które będą przechowywane w Europie, a także informacje o informacjach, które będą przechowywane poza europejskimi centrami danych. 

---
 
### <a name="new-user-provisioning-saas-app-integrations---may-2018"></a>Nowe Integracje aplikacji SaaS dla użytkowników — może 2018

**Typ:** Nowa funkcja  
**Kategoria usługi:** Inicjowanie obsługi aplikacji  
**Możliwości produktu:** integracja innej firmy
 
Usługa Azure AD umożliwia automatyzację tworzenia, obsługi i usuwania tożsamości użytkowników w aplikacjach SaaS, takich jak Dropbox, Salesforce, usługi ServiceNow i inne. W przypadku 2018 maja dodaliśmy obsługę aprowizacji użytkowników dla następujących aplikacji w galerii aplikacji usługi Azure AD:

- [BlueJeans](https://docs.microsoft.com/azure/active-directory/active-directory-saas-bluejeans-provisioning-tutorial)

- [OnDemand kamień](https://docs.microsoft.com/azure/active-directory/active-directory-saas-cornerstone-ondemand-provisioning-tutorial)

- [Systemu Zendesk](https://docs.microsoft.com/azure/active-directory/active-directory-saas-zendesk-provisioning-tutorial)

Aby uzyskać listę wszystkich aplikacji, które obsługują Inicjowanie obsługi użytkowników w galerii usługi Azure AD, zobacz [https://aka.ms/appstutorial](https://aka.ms/appstutorial).

---
 
### <a name="azure-ad-access-reviews-of-groups-and-app-access-now-provides-recurring-reviews"></a>Przeglądy dostępu w usłudze Azure AD dotyczące grup i dostępu do aplikacji oferują teraz cykliczne przeglądy

**Typ:** Nowa funkcja  
**Kategoria usługi:** Przeglądy dostępu  
**Możliwość produktu:** Dobrego
 
Przegląd dostępu do grup i aplikacji jest teraz ogólnie dostępny w ramach Azure AD — wersja Premium P2.  Administratorzy będą mogli konfigurować przeglądy dostępu dla członkostwa w grupach i przypisania aplikacji, aby automatycznie powtarzać się w regularnych odstępach czasu, takich jak co miesiąc lub co kwartał.

---

### <a name="azure-ad-activity-logs-sign-ins-and-audit-are-now-available-through-ms-graph"></a>Dzienniki aktywności usługi Azure AD (logowania i inspekcja) są teraz dostępne za pomocą programu MS Graph

**Typ:** Nowa funkcja  
**Kategoria usługi:** Reporting  
**Możliwość produktu:** Monitorowanie & raportowania
 
Dzienniki aktywności usługi Azure AD, które obejmują logowania i dzienniki inspekcji, są teraz dostępne za pomocą programu MS Graph. Do uzyskiwania dostępu do tych dzienników zostały uwidocznione dwa punkty końcowe za poorednictwem programu MS Graph. Zapoznaj się z naszymi [dokumentami](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-getting-started-azure-portal) , aby uzyskać programistyczny dostęp do interfejsów API raportowania usługi Azure AD, aby rozpocząć pracę. 

---
 
### <a name="improvements-to-the-b2b-redemption-experience-and-leave-an-org"></a>Ulepszenia środowiska związanego z wykupem B2B i opuszczanie organizacji

**Typ:** Nowa funkcja  
**Kategoria usługi:** B2B  
**Możliwość produktu:** B2B/B2C

**Wykupu just in Time:** Po udostępnieniu zasobu użytkownikowi gość przy użyciu interfejsu API B2B — nie musisz wysyłać specjalnej wiadomości e-mail z zaproszeniem. W większości przypadków użytkownik-Gość może uzyskać dostęp do zasobu i zostanie on przetworzony przez cały czas. Nie ma żadnego wpływu ze względu na brakujące wiadomości e-mail. Nie ma więcej pytania dla użytkowników-Gości "po kliknięciu linku do wykupu przez system?". Oznacza to, że gdy SPO korzysta z Menedżera zaproszeń — załączniki w chmurze mogą mieć ten sam kanoniczny adres URL dla wszystkich użytkowników — wewnętrzny i zewnętrzny — w dowolnym stanie realizacji.

**Nowoczesne środowisko wykupu:** Nie ma więcej informacji na stronie początkowej dzielenia ekranu. Użytkownicy będą widzieć nowoczesne środowisko wyrażania zgody z zasadami zachowania poufności informacji w organizacji, podobnie jak w przypadku aplikacji innych firm.

**Użytkownicy-Goście mogą opuścić organizacji:** Gdy relacja użytkownika z organizacją zostanie przełączona, może ona samoobsługowo wychodzić z organizacji. Nie ma więcej informacji o tym, że zapraszasz administratora organizacji do usuwania ", nie ma więcej dodatkowych biletów pomocy technicznej.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---may-2018"></a>Nowe aplikacje federacyjne dostępne w galerii aplikacji usługi Azure AD — maj 2018

**Typ:** Nowa funkcja  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwości produktu:** integracja innej firmy
 
W maju 2018 dodaliśmy te 18 nowych aplikacji z obsługą Federacji do naszej galerii aplikacji:

[AwardSpring](https://docs.microsoft.com/azure/active-directory/active-directory-saas-awardspring-tutorial), Infogix Data3Sixty, [Yodeck](https://docs.microsoft.com/azure/active-directory/active-directory-saas-infogix-tutorial), [Jamf Pro](https://docs.microsoft.com/azure/active-directory/active-directory-saas-jamfprosamlconnector-tutorial), [KnowledgeOwl](https://docs.microsoft.com/azure/active-directory/active-directory-saas-knowledgeowl-tutorial), [ENVI MMIS](https://docs.microsoft.com/azure/active-directory/active-directory-saas-envimmis-tutorial), [LaunchDarkly](https://docs.microsoft.com/azure/active-directory/active-directory-saas-launchdarkly-tutorial), [Adobe Captivateal](https://docs.microsoft.com/azure/active-directory/active-directory-saas-adobecaptivateprime-tutorial), [Montage online](https://docs.microsoft.com/azure/active-directory/active-directory-saas-montageonline-tutorial),[まなびポケット](https://docs.microsoft.com/azure/active-directory/active-directory-saas-manabipocket-tutorial), OpenReel, [łuk Publikowanie — Logowanie jednokrotne](https://docs.microsoft.com/azure/active-directory/active-directory-saas-arc-tutorial), [PlanGrid](https://docs.microsoft.com/azure/active-directory/active-directory-saas-plangrid-tutorial), [iWellnessNow](https://docs.microsoft.com/azure/active-directory/active-directory-saas-iwellnessnow-tutorial), [Proxyclick](https://docs.microsoft.com/azure/active-directory/active-directory-saas-proxyclick-tutorial), [potencjalnie szkodliwe oprogramowanie](https://docs.microsoft.com/azure/active-directory/active-directory-saas-riskware-tutorial), [stado](https://docs.microsoft.com/azure/active-directory/active-directory-saas-flock-tutorial), [ReviewSnap](https://docs.microsoft.com/azure/active-directory/active-directory-saas-reviewsnap-tutorial)

Aby uzyskać więcej informacji na temat aplikacji, zobacz [SaaS Application Integration with Azure Active Directory](https://aka.ms/appstutorial).

Aby uzyskać więcej informacji na temat tworzenia listy aplikacji w galerii aplikacji usługi Azure AD, zobacz [Wyświetlanie listy aplikacji w galerii aplikacji Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing).

---
 
### <a name="new-step-by-step-deployment-guides-for-azure-active-directory"></a>Nowe przewodniki wdrażania krok po kroku dla Azure Active Directory

**Typ:** Nowa funkcja  
**Kategoria usługi:** Różnych  
**Możliwość produktu:** Katalogi
 
Nowe wskazówki krok po kroku dotyczące sposobu wdrażania Azure Active Directory (Azure AD), w tym samoobsługowego resetowania hasła (SSPR), logowania jednokrotnego (SSO), dostępu warunkowego (CA), serwera proxy aplikacji, aprowizacji użytkowników, Active Directory Federation Services (ADFS) do programu Uwierzytelnianie przekazywane (PTA) i usługi ADFS do synchronizacji skrótów haseł (PHS).

Aby wyświetlić przewodniki wdrażania, przejdź do repozytorium [przewodniki wdrażania tożsamości](https://aka.ms/DeploymentPlans) w witrynie GitHub. Aby przekazać opinię na temat przewodników wdrażania, użyj [formularza Opinia dotycząca planu wdrożenia](https://aka.ms/deploymentplanfeedback). Jeśli masz jakieś pytania dotyczące przewodników wdrażania, skontaktuj się z nami pod adresem [IDGitDeploy](mailto:idgitdeploy@microsoft.com).

---

### <a name="enterprise-applications-search---load-more-apps"></a>Wyszukiwanie aplikacji dla przedsiębiorstw — Załaduj więcej aplikacji

**Typ:** Nowa funkcja  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwość produktu:** ZWRÓCIŁ
 
Masz problemy z znalezieniem aplikacji/nazw głównych usług? Dodaliśmy możliwość załadowania większej liczby aplikacji na liście wszystkie aplikacje dla aplikacji w przedsiębiorstwie. Domyślnie pokazujemy 20 aplikacji. Teraz możesz kliknąć przycisk **Załaduj więcej** , aby wyświetlić dodatkowe aplikacje. 

---
 
### <a name="the-may-release-of-aadconnect-contains-a-public-preview-of-the-integration-with-pingfederate-important-security-updates-many-bug-fixes-and-new-great-new-troubleshooting-tools"></a>Wersja AADConnect może zawierać publiczną wersję zapoznawczą integracji z serwera pingfederate, ważne aktualizacje zabezpieczeń, wiele poprawek błędów i nowe wspaniałe nowe narzędzia do rozwiązywania problemów. 

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Połączenie AD  
**Możliwość produktu:** Zarządzanie cyklem życia tożsamości
 
Wersja AADConnect może zawierać publiczną wersję zapoznawczą integracji z serwera pingfederate, ważne aktualizacje zabezpieczeń, wiele poprawek błędów i nowe wspaniałe nowe narzędzia do rozwiązywania problemów. Informacje o wersji można znaleźć [tutaj](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-version-history#118190).

---

### <a name="azure-ad-access-reviews-auto-apply"></a>Przeglądy dostępu do usługi Azure AD: autoapply

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Przeglądy dostępu  
**Możliwość produktu:** Dobrego

Przeglądy dostępu do grup i aplikacji są teraz ogólnie dostępne w ramach Azure AD — wersja Premium P2. Administrator może skonfigurować program w taki sposób, aby automatycznie stosował zmiany recenzenta do tej grupy lub aplikacji po zakończeniu przeglądu dostępu. Administrator może także określić, co się dzieje z ciągłym dostępem użytkownika, jeśli recenzenci nie odpowiedzieli, Usuń dostęp, Zachowaj dostęp lub zapoznaj się z zaleceniami dotyczącymi systemu. 

---

### <a name="id-tokens-can-no-longer-be-returned-using-the-query-response_mode-for-new-apps"></a>Tokenów identyfikatorów nie można już zwrócić przy użyciu response_mode zapytania dla nowych aplikacji. 

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Uwierzytelnienia (nazwy logowania)  
**Możliwość produktu:** Uwierzytelnianie użytkownika
 
Aplikacje utworzone w dniu lub po 25 kwietnia 2018 nie będą już mogły żądać **id_token** przy użyciu **zapytania** response_mode.  Dzięki temu usługa Azure AD jest wbudowana ze specyfikacjami OIDC i pomaga w zmniejszeniu obszaru ataków na aplikacje.  Aplikacje utworzone przed 25 kwietnia 2018 nie są blokowane przed użyciem **zapytania** response_mode z response_typeem **id_token**.  Zwrócony błąd podczas żądania id_token z usługi AAD jest **AADSTS70007: "Query" nie jest obsługiwaną wartością elementu "response_mode" podczas żądania tokenu**.

**Fragmenty** i **form_post** response_modes nadal działają — podczas tworzenia nowych obiektów aplikacji (na przykład w przypadku użycia serwera proxy aplikacji) przed utworzeniem nowej aplikacji upewnij się, że użyto jednego z tych response_modes.  

---
 
## <a name="april-2018"></a>Kwiecień 2018 r. 

### <a name="azure-ad-b2c-access-token-are-ga"></a>Token dostępu Azure AD B2C to GA

**Typ:** Nowa funkcja  
**Kategoria usługi:** B2C — Zarządzanie tożsamościami konsumentów  
**Możliwość produktu:** B2B/B2C 

Teraz możesz uzyskiwać dostęp do interfejsów API sieci Web zabezpieczonych przez Azure AD B2C przy użyciu tokenów dostępu. Ta funkcja jest przenoszona z publicznej wersji zapoznawczej na GA. Udoskonalono interfejs użytkownika służący do konfigurowania Azure AD B2C aplikacji i interfejsów API sieci Web, a także wprowadzono inne drobne ulepszenia.
 
Aby uzyskać więcej informacji, zobacz [Azure AD B2C: żądanie tokenów dostępu](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-access-tokens).

---

### <a name="test-single-sign-on-configuration-for-saml-based-applications"></a>Testowanie konfiguracji logowania jednokrotnego dla aplikacji opartych na protokole SAML

**Typ:** Nowa funkcja  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwość produktu:** ZWRÓCIŁ

Podczas konfigurowania aplikacji SSO opartych na protokole SAML można testować integrację na stronie Konfiguracja. Jeśli wystąpi błąd podczas logowania, możesz podać błąd w środowisku testowym, a usługa Azure AD udostępnia kroki umożliwiające rozwiązanie określonego problemu.

Aby uzyskać więcej informacji, zobacz:

- [Konfigurowanie logowania jednokrotnego do aplikacji, które nie znajdują się w galerii aplikacji Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-custom-apps)
- [Jak debugować Logowanie jednokrotne oparte na protokole SAML do aplikacji w Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-debugging)

---
 
### <a name="azure-ad-terms-of-use-now-has-per-user-reporting"></a>Warunki użytkowania usługi Azure AD mają teraz raportowanie na użytkownika

**Typ:** Nowa funkcja  
**Kategoria usługi:** Warunki użytkowania  
**Możliwość produktu:** Przepisów
 
Administratorzy mogą teraz wybrać daną warunków użytkowania i zobaczyć wszystkich użytkowników, którzy wyraziły zgodę na tę warunków użytkowaniaę oraz datę/czas, w którym miało miejsce.

Aby uzyskać więcej informacji, zobacz [Funkcja warunki użytkowania usługi Azure AD](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use).

---
 
### <a name="azure-ad-connect-health-risky-ip-for-ad-fs-extranet-lockout-protection"></a>Azure AD Connect Health: ryzykowny adres IP dla AD FS ochrony blokad w ekstranecie 

**Typ:** Nowa funkcja  
**Kategoria usługi:** Różnych  
**Możliwość produktu:** Monitorowanie & raportowania

Program Connect Health obsługuje teraz możliwość wykrywania adresów IP, które przekraczają próg nieudanych logowań U/P co godzinę lub codziennie. Możliwości oferowane przez tę funkcję są następujące:

- Obszerny raport przedstawiający adres IP oraz liczbę nieudanych logowań generowanych w ciągu godzinowym/codziennym przez dostosowywalny próg.
- Alerty na podstawie poczty e-mail pokazujące, kiedy określony adres IP przekroczył próg nieudanych logowań U/P w ujęciu godzinowym/codziennym.
- Opcja pobierania służąca do wykonania szczegółowej analizy danych

Aby uzyskać więcej informacji, zobacz [raport dotyczący ryzykownych adresów IP](https://aka.ms/aadchriskyip).

---
 
### <a name="easy-app-config-with-metadata-file-or-url"></a>Łatwa konfiguracja aplikacji przy użyciu pliku lub adresu URL metadanych

**Typ:** Nowa funkcja  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwość produktu:** ZWRÓCIŁ

Na stronie aplikacje przedsiębiorstwa Administratorzy mogą przekazać plik metadanych SAML w celu skonfigurowania logowania opartego na protokole SAML dla galerii usługi AAD i aplikacji spoza galerii.

Ponadto możesz użyć adresu URL metadanych federacji aplikacji usługi Azure AD, aby skonfigurować Logowanie jednokrotne za pomocą aplikacji dostosowanej.

Aby uzyskać więcej informacji, zobacz [Konfigurowanie logowania jednokrotnego do aplikacji, które nie znajdują się w galerii aplikacji Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-custom-apps).

---

### <a name="azure-ad-terms-of-use-now-generally-available"></a>Usługa Azure AD Warunki użytkowania teraz ogólnie dostępna

**Typ:** Nowa funkcja  
**Kategoria usługi:** Warunki użytkowania  
**Możliwość produktu:** Przepisów
 

Warunki użytkowania usługi Azure AD zostały przeniesione z publicznej wersji zapoznawczej do ogólnie dostępnej.

Aby uzyskać więcej informacji, zobacz [Funkcja warunki użytkowania usługi Azure AD](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use).

---

### <a name="allow-or-block-invitations-to-b2b-users-from-specific-organizations"></a>Zezwalaj lub Blokuj zaproszenia użytkownikom B2B z określonych organizacji

**Typ:** Nowa funkcja  
**Kategoria usługi:** B2B  
**Możliwość produktu:** B2B/B2C
 

Teraz możesz określić, którzy organizacje partnerskie mają być współużytkowane i współpracujące z usługą Azure AD B2B. W tym celu można wybrać opcję tworzenia listy określonych domen dozwolonych lub Odmów. Gdy domena zostanie zablokowana przy użyciu tych funkcji, pracownicy nie będą już mogli wysyłać zaproszeń do osób w tej domenie.

Dzięki temu można kontrolować dostęp do zasobów, jednocześnie zapewniając bezproblemowe środowisko dla zatwierdzonych użytkowników.

Ta funkcja współpracy B2B jest dostępna dla wszystkich Azure Active Directory klientów i może być używana w połączeniu z funkcjami Azure AD — wersja Premium, takimi jak dostęp warunkowy i Ochrona tożsamości, aby uzyskać bardziej szczegółową kontrolę nad tym, kiedy i jak zewnętrznym użytkownikom biznesowym jest znak w programie i uzyskać dostęp.

Aby uzyskać więcej informacji, zobacz [Zezwalanie lub blokowanie zaproszeń użytkownikom B2B z określonych organizacji](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-allow-deny-list).

---
 
### <a name="new-federated-apps-available-in-azure-ad-app-gallery"></a>Nowe aplikacje federacyjne dostępne w galerii aplikacji usługi Azure AD

**Typ:** Nowa funkcja  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwości produktu:** integracja innej firmy

W kwietniu 2018 dodaliśmy te 13 nowych aplikacji z obsługą Federacji do naszej galerii aplikacji:

Kryterium HCM, [FiscalNote](https://docs.microsoft.com/azure/active-directory/active-directory-saas-fiscalnote-tutorial), [Secret Server (on-premises)](https://docs.microsoft.com/azure/active-directory/active-directory-saas-secretserver-on-premises-tutorial), [sygnał dynamiczny](https://docs.microsoft.com/azure/active-directory/active-directory-saas-dynamicsignal-tutorial), [mindWireless](https://docs.microsoft.com/azure/active-directory/active-directory-saas-mindwireless-tutorial), [schemat organizacyjny teraz](https://docs.microsoft.com/azure/active-directory/active-directory-saas-orgchartnow-tutorial), [Ziflow](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ziflow-tutorial), [AppNeta Performance Monitor](https://docs.microsoft.com/azure/active-directory/active-directory-saas-appneta-tutorial), [Elium](https://docs.microsoft.com/azure/active-directory/active-directory-saas-elium-tutorial) , [FluxX Labs](https://docs.microsoft.com/azure/active-directory/active-directory-saas-fluxxlabs-tutorial), [ Cisco Cloud](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ciscocloud-tutorial), półka, [SafetyNET](https://docs.microsoft.com/azure/active-directory/active-directory-saas-safetynet-tutorial)

Aby uzyskać więcej informacji na temat aplikacji, zobacz [SaaS Application Integration with Azure Active Directory](https://aka.ms/appstutorial).

Aby uzyskać więcej informacji na temat tworzenia listy aplikacji w galerii aplikacji usługi Azure AD, zobacz [Wyświetlanie listy aplikacji w galerii aplikacji Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing).

---
 
### <a name="grant-b2b-users-in-azure-ad-access-to-your-on-premises-applications-public-preview"></a>Przyznaj użytkownikom B2B dostęp do aplikacji lokalnych (publiczna wersja zapoznawcza) w usłudze Azure AD

**Typ:** Nowa funkcja  
**Kategoria usługi:** B2B  
**Możliwość produktu:** B2B/B2C

Jako organizacja, która używa funkcji współpracy B2B Azure Active Directory (Azure AD), aby zapraszać użytkowników-Gości od organizacji partnerskich do usługi Azure AD, możesz teraz udostępnić tym użytkownikom B2B dostęp do aplikacji lokalnych. Te aplikacje lokalne mogą korzystać z uwierzytelniania opartego na języku SAML lub zintegrowanego uwierzytelniania systemu Windows (IWA) przy użyciu ograniczonego delegowania protokołu Kerberos (KCD).

Aby uzyskać więcej informacji, zobacz [udzielanie użytkownikom B2B w usłudze Azure AD dostępu do aplikacji lokalnych](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-hybrid-cloud-to-on-premises).

---
 
### <a name="get-sso-integration-tutorials-from-the-azure-marketplace"></a>Pobierz samouczki dotyczące integracji z logowaniem jednokrotnym z portalu Azure Marketplace

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Różnych  
**Możliwości produktu:** integracja innej firmy

Jeśli aplikacja, która jest wymieniona w [portalu Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps?page=1) obsługuje logowanie jednokrotne oparte na protokole SAML, kliknij przycisk **Pobierz teraz** udostępnia samouczek integracji skojarzony z tą aplikacją. 

---

### <a name="faster-performance-of-azure-ad-automatic-user-provisioning-to-saas-applications"></a>Szybsza wydajność automatycznej aprowizacji użytkowników w usłudze Azure AD do aplikacji SaaS

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Inicjowanie obsługi aplikacji  
**Możliwości produktu:** integracja innej firmy
 
Wcześniej klienci korzystający z łączników aprowizacji użytkowników Azure Active Directory dla aplikacji SaaS (na przykład Salesforce, usługi ServiceNow i Box) mogą korzystać z wolnej wydajności, jeśli dzierżawy usługi Azure AD znajdują się ponad 100 000 połączonych użytkowników i grupy i korzystają z przypisań użytkowników i grup w celu ustalenia, którzy użytkownicy powinni być obsługiwani.

2 kwietnia 2018, w usłudze Azure AD Provisioning zostały wdrożone znaczące ulepszenia wydajności, które znacznie skracają czas wymagany do wykonywania synchronizacji początkowej między Azure Active Directory i docelowymi aplikacjami SaaS.

W związku z tym wielu klientów, którzy mieli wstępne synchronizacje dla aplikacji, które zajęły wiele dni lub nigdy nie ukończyłą pracy, są teraz wykonywane w ciągu kilku minut lub godzin.

Aby uzyskać więcej informacji, zobacz [co się stanie w trakcie aprowizacji?](https://docs.microsoft.com/azure/active-directory/active-directory-saas-app-provisioning#what-happens-during-provisioning)

---

### <a name="self-service-password-reset-from-windows-10-lock-screen-for-hybrid-azure-ad-joined-machines"></a>Samoobsługowe resetowanie haseł z ekranu blokady systemu Windows 10 dla maszyn przyłączonych do hybrydowej usługi Azure AD

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Samoobsługowe resetowanie hasła  
**Możliwość produktu:** Uwierzytelnianie użytkownika
 
Funkcja SSPR systemu Windows 10 została zaktualizowana w celu uwzględnienia obsługi maszyn, które są dołączone do hybrydowej usługi Azure AD. Ta funkcja jest dostępna w systemie Windows 10 RS4 umożliwia użytkownikom Resetowanie swoich haseł na ekranie blokady na komputerze z systemem Windows 10. Użytkownicy, którzy są włączeni do samoobsługowego resetowania hasła, mogą korzystać z tej funkcji.

Aby uzyskać więcej informacji, zobacz [Resetowanie hasła usługi Azure AD na ekranie logowania](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-sspr-windows).

---

## <a name="march-2018"></a>Marzec 2018 r.
 
### <a name="certificate-expire-notification"></a>Powiadomienie o wygaśnięciu certyfikatu

**Typ:** FIXED  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwość produktu:** ZWRÓCIŁ
 
Usługa Azure AD wysyła powiadomienie, gdy certyfikat galerii lub aplikacji z galerii nie zostanie wkrótce wygaśnie. 

Niektórzy użytkownicy nie otrzymali powiadomień o aplikacjach dla przedsiębiorstw skonfigurowanych do logowania jednokrotnego opartego na protokole SAML. Ten problem został rozwiązany. Usługa Azure AD wysyła powiadomienie o certyfikatach, które wygasną w dniach 7, 30 i 60. To zdarzenie można zobaczyć w dziennikach inspekcji. 

Aby uzyskać więcej informacji, zobacz:

- [Zarządzanie certyfikatami federacyjnego logowania jednokrotnego w Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-sso-certs)
- [Raporty dotyczące inspekcji w portalu Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-audit-logs)
 
---
 
### <a name="twitter-and-github-identity-providers-in-azure-ad-b2c"></a>Dostawcy tożsamości Twitter i GitHub w Azure AD B2C

**Typ:** Nowa funkcja  
**Kategoria usługi:** B2C — Zarządzanie tożsamościami konsumentów  
**Możliwość produktu:** B2B/B2C
 
W Azure AD B2C możesz teraz dodawać usługi Twitter lub GitHub jako dostawcę tożsamości. Serwis Twitter jest przenoszony z publicznej wersji zapoznawczej na GA. Serwis GitHub jest publikowany w publicznej wersji zapoznawczej.

Aby uzyskać więcej informacji, zobacz [co to jest współpraca B2B w usłudze Azure AD?](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b).
 
---

### <a name="restrict-browser-access-using-intune-managed-browser-with-azure-ad-application-based-conditional-access-for-ios-and-android"></a>Ograniczanie dostępu do przeglądarki przy użyciu Intune Managed Browser z dostępem warunkowym opartym na aplikacji w usłudze Azure AD dla systemów iOS i Android

**Typ:** Nowa funkcja  
**Kategoria usługi:** Dostęp warunkowy  
**Możliwość produktu:** Ochrona tożsamości & zabezpieczenia
 
**Teraz w publicznej wersji zapoznawczej!**

**Intune Managed Browser Logowanie jednokrotne:** Pracownicy mogą korzystać z logowania jednokrotnego na klientach natywnych (takich jak program Microsoft Outlook) i Intune Managed Browser dla wszystkich aplikacji połączonych z usługą Azure AD.

**Intune Managed Browser obsługa dostępu warunkowego:** Teraz można wymagać od pracowników korzystania z zarządzanej przeglądarki Intune przy użyciu zasad dostępu warunkowego opartego na aplikacji.

Więcej informacji na ten temat można znaleźć w naszym [wpisie w blogu](https://cloudblogs.microsoft.com/enterprisemobility/2018/03/15/the-intune-managed-browser-now-supports-azure-ad-sso-and-conditional-access/).

Aby uzyskać więcej informacji, zobacz:

- [Konfigurowanie dostępu warunkowego opartego na aplikacji](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)

- [Konfigurowanie zasad przeglądarki Managed Browser](https://aka.ms/managedbrowser)  

---
 
### <a name="app-proxy-cmdlets-in-powershell-ga-module"></a>Polecenia cmdlet serwera proxy aplikacji w module środowiska PowerShell GA

**Typ:** Nowa funkcja  
**Kategoria usługi:** Serwer proxy aplikacji  
**Możliwość produktu:** Access Control
 
Obsługa poleceń cmdlet serwera proxy aplikacji jest teraz w module GA programu PowerShell. Wymaga to pozostawania aktualizacji w modułach programu PowerShell — w przypadku przekroczenia roku niektóre polecenia cmdlet mogą przestać działać. 

Aby uzyskać więcej informacji, zobacz [AzureAD](https://docs.microsoft.com/powershell/module/Azuread/?view=azureadps-2.0).
 
---
 
### <a name="office-365-native-clients-are-supported-by-seamless-sso-using-a-non-interactive-protocol"></a>Natywni klienci pakietu Office 365 są obsługiwani przez bezproblemowe logowanie jednokrotne przy użyciu protokołu nieinterakcyjnego

**Typ:** Nowa funkcja  
**Kategoria usługi:** Uwierzytelnienia (nazwy logowania)  
**Możliwość produktu:** Uwierzytelnianie użytkownika
 
Użytkownik korzystający z natywnych klientów pakietu Office 365 (wersja 16.0.8730. xxxx i nowsze) uzyskuje ciche środowisko logowania, wykorzystując bezproblemową rejestrację jednokrotną. To wsparcie jest zapewniane przez dodanie protokołu nieinterakcyjnego (WS-Trust) do usługi Azure AD.

Aby uzyskać więcej informacji, zobacz [Jak zalogować się na natywnym kliencie z bezproblemowym logowaniem JEDNOkrotnym?](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso-how-it-works#how-does-sign-in-on-a-native-client-with-seamless-sso-work)
 
---

### <a name="users-get-a-silent-sign-on-experience-with-seamless-sso-if-an-application-sends-sign-in-requests-to-azure-ads-tenant-endpoints"></a>Użytkownicy uzyskują dyskretne środowisko logowania z bezproblemowym logowaniem jednokrotnym, jeśli aplikacja wysyła żądania logowania do punktów końcowych dzierżawy usługi Azure AD

**Typ:** Nowa funkcja  
**Kategoria usługi:** Uwierzytelnienia (nazwy logowania)  
**Możliwość produktu:** Uwierzytelnianie użytkownika
 
Użytkownicy uzyskują ciche środowisko logowania za pomocą bezproblemowego logowania jednokrotnego, jeśli aplikacja (na przykład `https://contoso.sharepoint.com`) wysyła żądania logowania do punktów końcowych dzierżawy usługi Azure AD, czyli `https://login.microsoftonline.com/contoso.com/<..>` lub `https://login.microsoftonline.com/<tenant_ID>/<..>`-zamiast wspólnego punktu końcowego usługi Azure AD (`https://login.microsoftonline.com/common/<...>`).

Aby uzyskać więcej informacji, zobacz [Azure Active Directory bezproblemowe logowanie jednokrotne](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso). 

---
 
### <a name="need-to-add-only-one-azure-ad-url-instead-of-two-urls-previously-to-users-intranet-zone-settings-to-roll-out-seamless-sso"></a>Aby zapewnić bezproblemowe logowanie jednokrotne, należy dodać tylko jeden adres URL usługi Azure AD, a nie dwa adresy URL w ustawieniach strefy intranetowej użytkownika.

**Typ:** Nowa funkcja  
**Kategoria usługi:** Uwierzytelnienia (nazwy logowania)  
**Możliwość produktu:** Uwierzytelnianie użytkownika
 
Aby zapewnić użytkownikom bezproblemowe logowanie jednokrotne, musisz dodać tylko jeden adres URL usługi Azure AD do ustawień strefy intranetowej użytkowników przy użyciu zasad grupy w Active Directory: `https://autologon.microsoftazuread-sso.com`. Wcześniej Klienci musieli dodać dwa adresy URL.

Aby uzyskać więcej informacji, zobacz [Azure Active Directory bezproblemowe logowanie jednokrotne](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso). 
 
---
 
### <a name="new-federated-apps-available-in-azure-ad-app-gallery"></a>Nowe aplikacje federacyjne dostępne w galerii aplikacji usługi Azure AD

**Typ:** Nowa funkcja  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwości produktu:** integracja innej firmy

W marcu 2018 dodaliśmy następujące 15 nowych aplikacji z obsługą Federacji do naszej galerii aplikacji:

[BoxCryptor](https://docs.microsoft.com/azure/active-directory/active-directory-saas-boxcryptor-tutorial), [CylancePROTECT](https://docs.microsoft.com/azure/active-directory/active-directory-saas-cylanceprotect-tutorial), Wrike, [SignalFx](https://docs.microsoft.com/azure/active-directory/active-directory-saas-signalfx-tutorial), Assistant przez FirstAgenda, [YardiOne](https://docs.microsoft.com/azure/active-directory/active-directory-saas-yardione-tutorial), vtiger CRM, unobrazkową, [amplituda](https://docs.microsoft.com/azure/active-directory/active-directory-saas-amplitude-tutorial), [Spacio](https://docs.microsoft.com/azure/active-directory/active-directory-saas-spacio-tutorial), [ContractWorks](https://docs.microsoft.com/azure/active-directory/active-directory-saas-contractworks-tutorial), [Bersin](https://docs.microsoft.com/azure/active-directory/active-directory-saas-bersin-tutorial), [Mercell](https://docs.microsoft.com/azure/active-directory/active-directory-saas-mercell-tutorial), [ Trisotech Digital Enterprise Server](https://docs.microsoft.com/azure/active-directory/active-directory-saas-trisotechdigitalenterpriseserver-tutorial), [Qumu Cloud](https://docs.microsoft.com/azure/active-directory/active-directory-saas-qumucloud-tutorial).
 
Aby uzyskać więcej informacji na temat aplikacji, zobacz [SaaS Application Integration with Azure Active Directory](https://aka.ms/appstutorial).

Aby uzyskać więcej informacji na temat tworzenia listy aplikacji w galerii aplikacji usługi Azure AD, zobacz [Wyświetlanie listy aplikacji w galerii aplikacji Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing). 

---
 
### <a name="pim-for-azure-resources-is-generally-available"></a>Usługa PIM dla zasobów platformy Azure jest ogólnie dostępna

**Typ:** Nowa funkcja  
**Kategoria usługi:** Privileged Identity Management  
**Możliwość produktu:** Privileged Identity Management
 
Jeśli używasz Azure AD Privileged Identity Management dla ról katalogu, możesz teraz korzystać z możliwości dostępu i przypisywania w usłudze PIM dla ról zasobów platformy Azure, takich jak subskrypcje, grupy zasobów, Virtual Machines i inne obsługiwane zasoby. przez Azure Resource Manager. Wymuś Multi-Factor Authentication podczas aktywacji ról just-in-Time i Zaplanuj aktywację w koordynacji z zatwierdzonymi zmianami systemu Windows. Ponadto w tej wersji dodano ulepszenia, które nie są dostępne w publicznej wersji zapoznawczej, w tym zaktualizowany interfejs użytkownika, przepływy pracy zatwierdzania i możliwość rozszerzenia ról, które wkrótce wygasną i odnawiania wygasłych ról.

Aby uzyskać więcej informacji, zobacz temat [PIM dla zasobów platformy Azure (wersja zapoznawcza)](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/azure-pim-resource-rbac)
 
---
 
### <a name="adding-optional-claims-to-your-apps-tokens-public-preview"></a>Dodawanie opcjonalnych oświadczeń do tokenów aplikacji (publiczna wersja zapoznawcza)

**Typ:** Nowa funkcja  
**Kategoria usługi:** Uwierzytelnienia (nazwy logowania)  
**Możliwość produktu:** Uwierzytelnianie użytkownika
 
Aplikacja usługi Azure AD może teraz żądać niestandardowych lub opcjonalnych oświadczeń w tokenach JWTs lub SAML.  Są to oświadczenia dotyczące użytkownika lub dzierżawy, które nie są uwzględnione domyślnie w tokenie z powodu ograniczeń rozmiaru lub zastosowania.  Jest to obecnie dostępna w publicznej wersji zapoznawczej dla aplikacji usługi Azure AD w punktach końcowych wersji 1.0 i 2.0.  Zapoznaj się z dokumentacją, aby uzyskać informacje na temat tego, jakie oświadczenia można dodać, oraz sposobu edytowania manifestu aplikacji w celu ich żądania.  

Aby uzyskać więcej informacji, zobacz [opcjonalne oświadczenia w usłudze Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-optional-claims).
 
---
 
### <a name="azure-ad-supports-pkce-for-more-secure-oauth-flows"></a>Usługa Azure AD obsługuje PKCE na potrzeby bardziej bezpiecznych przepływów OAuth

**Typ:** Nowa funkcja  
**Kategoria usługi:** Uwierzytelnienia (nazwy logowania)  
**Możliwość produktu:** Uwierzytelnianie użytkownika
 
Zaktualizowano dokumentację usługi Azure AD w celu zanotowania pomocy technicznej PKCE, która pozwala na bardziej bezpieczną komunikację w ramach przepływu przydzielenia kodu autoryzacji OAuth 2,0.  Dla punktów końcowych v 1.0 i v 2.0 są obsługiwane zarówno S256, jak i code_challenges. 

Aby uzyskać więcej informacji, zobacz [żądanie kodu autoryzacji](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-protocols-oauth-code#request-an-authorization-code). 
 
---
 
### <a name="support-for-provisioning-all-user-attribute-values-available-in-the-workday-get_workers-api"></a>Obsługa aprowizacji wszystkich wartości atrybutów użytkownika dostępnych w interfejsie API Get_Workers produktu Workday

**Typ:** Nowa funkcja  
**Kategoria usługi:** Inicjowanie obsługi aplikacji  
**Możliwości produktu:** integracja innej firmy
 
Publiczna wersja zapoznawcza udostępniania przychodzącego z produktu Workday do Active Directory i usługi Azure AD obsługuje teraz możliwość wyodrębnienia wszystkich wartości atrybutów dostępnych w INTERFEJSie Workday Get_Workers. To dodanie obsługuje setki dodatkowych atrybutów standardowych i niestandardowych poza tymi, które są dostarczane z początkową wersją łącznika inicjowania obsługi ruchu przychodzącego dla programu Workday.

Aby uzyskać więcej informacji, zobacz: [Dostosowywanie listy atrybutów użytkownika produktu Workday](https://docs.microsoft.com/azure/active-directory/active-directory-saas-workday-inbound-tutorial#customizing-the-list-of-workday-user-attributes)

---

### <a name="changing-group-membership-from-dynamic-to-static-and-vice-versa"></a>Zmiana przynależności do grupy z dynamicznego na static i na odwrót

**Typ:** Nowa funkcja  
**Kategoria usługi:** Zarządzanie grupami  
**Możliwość produktu:** Społeczności
 
Istnieje możliwość zmiany sposobu zarządzania członkostwem w grupie. Jest to przydatne, gdy chcesz zachować tę samą nazwę grupy i identyfikator w systemie, tak aby wszystkie istniejące odwołania do grupy były nadal ważne; utworzenie nowej grupy wymaga aktualizacji tych odwołań.
Zaktualizowaliśmy centrum administracyjne usługi Azure AD w celu zapewnienia obsługi tej funkcji. Teraz klienci mogą konwertować istniejące grupy z członkostwa dynamicznego na przypisane członkostwo i na odwrót. Istniejące polecenia cmdlet programu PowerShell są również nadal dostępne.

Aby uzyskać więcej informacji, zobacz [dynamiczne reguły członkostwa dla grup w Azure Active Directory](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership)

---

### <a name="improved-sign-out-behavior-with-seamless-sso"></a>Ulepszone zachowanie podczas logowania za pomocą bezproblemowego logowania jednokrotnego

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Uwierzytelnienia (nazwy logowania)  
**Możliwość produktu:** Uwierzytelnianie użytkownika
 
Wcześniej, nawet jeśli użytkownicy jawnie wylogowani z aplikacji zabezpieczonej przez usługę Azure AD, zostaną automatycznie zarejestrowani z użyciem bezproblemowego logowania jednokrotnego, jeśli próbujesz uzyskać dostęp do aplikacji usługi Azure AD w sieci firmowej ze swoich urządzeń przyłączonych do domeny. Ta zmiana powoduje, że wylogowanie jest obsługiwane.  Umożliwia to użytkownikom wybranie tego samego lub innego konta usługi Azure AD w celu zalogowania się za pomocą programu, a nie automatyczne logowanie za pomocą bezproblemowego logowania jednokrotnego.

Aby uzyskać więcej informacji, zobacz [Azure Active Directory bezproblemowe logowanie jednokrotne](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso)
 
---
 
### <a name="application-proxy-connector-version-154020-released"></a>Wydano wersję łącznika serwera proxy aplikacji 1.5.402.0

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Serwer proxy aplikacji  
**Możliwość produktu:** Ochrona tożsamości & zabezpieczenia
 
Wersja tego łącznika jest stopniowo wprowadzana do listopada. Ta nowa wersja łącznika obejmuje następujące zmiany:

- Łącznik teraz ustawia pliki cookie na poziomie domeny zamiast na poziomie domeny podrzędnej. Zapewnia to bezproblemowe środowisko logowania jednokrotnego i pozwala uniknąć nadmiarowych wierszy uwierzytelniania.
- Obsługa fragmentarycznych żądań kodowania
- Ulepszone monitorowanie kondycji łącznika 
- Kilka poprawek usterek i ulepszeń stabilności

Aby uzyskać więcej informacji, zobacz [Omówienie łączników usługi Azure serwer proxy aplikacji usługi Azure AD](https://docs.microsoft.com/azure/active-directory/application-proxy-understand-connectors).
 
---

## <a name="february-2018"></a>Luty 2018 r.
 
### <a name="improved-navigation-for-managing-users-and-groups"></a>Ulepszona nawigacja w celu zarządzania użytkownikami i grupami

**Typ:** Planowanie zmiany  
**Kategoria usługi:** Zarządzanie katalogami  
**Możliwość produktu:** Katalogi

Środowisko nawigacji dla zarządzania użytkownikami i grupami zostało udoskonalone. Teraz można nawigować z poziomu omówienia katalogów bezpośrednio do listy wszystkich użytkowników, z łatwiejszym dostępem do listy usuniętych użytkowników. Możesz również przejść z omówienia katalogów bezpośrednio do listy wszystkich grup z ułatwieniami dostępu do ustawień zarządzania grupami. Ponadto na stronie Przegląd katalogów można wyszukiwać użytkowników, grupy, aplikacje dla przedsiębiorstw lub rejestrację aplikacji. 

---

### <a name="availability-of-sign-ins-and-audit-reports-in-microsoft-azure-operated-by-21vianet-azure-china-21vianet"></a>Dostępność raportów logowania i inspekcji w Microsoft Azure obsługiwane przez firmę 21Vianet (Azure Chiny 21Vianet)

**Typ:** Nowa funkcja  
**Kategoria usługi:** Azure Stack  
**Możliwość produktu:** Monitorowanie & raportowania

Raporty dzienników aktywności usługi Azure AD są teraz dostępne w Microsoft Azure obsługiwane przez firmę 21Vianet (Azure Chiny 21Vianet). Uwzględniono następujące dzienniki:

- **Dzienniki aktywności logowania** — obejmuje wszystkie dzienniki logowania skojarzone z dzierżawcą.

- **Dzienniki inspekcji hasła samoobsługowego** — obejmuje wszystkie dzienniki inspekcji SSPR.

- **Dzienniki inspekcji zarządzania katalogami** — obejmuje wszystkie dzienniki inspekcji związane z zarządzaniem katalogiem, takie jak zarządzanie użytkownikami, zarządzanie aplikacjami i inne.

Korzystając z tych dzienników, można uzyskać wgląd w działanie środowiska. Na podstawie udostępnionych danych można:

- Określ, w jaki sposób aplikacje i usługi są wykorzystywane przez użytkowników.

- Rozwiązywanie problemów uniemożliwiających użytkownikom wykonywanie pracy.

Aby uzyskać więcej informacji na temat korzystania z tych raportów, zobacz [Azure Active Directory raportowanie](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-azure-portal).

---

### <a name="use-report-reader-role-non-admin-role-to-view-azure-ad-activity-reports"></a>Aby wyświetlić raporty aktywności usługi Azure AD, użyj roli "czytelnik raportu" (rola niebędąca administratorem)

**Typ:** Nowa funkcja  
**Kategoria usługi:** Reporting  
**Możliwość produktu:** Monitorowanie & raportowania

Aby uzyskać dostęp do dzienników aktywności usługi Azure AD w ramach klientów, skontaktuj się z tą funkcją, aby umożliwić użytkownikom należącym do roli "czytelnika raportu" dostęp do operacji logowania i inspekcji w ramach Azure Portal oraz korzystanie z naszych interfejsów API grafów. 

Aby uzyskać więcej informacji, jak korzystać z tych raportów, zobacz [Azure Active Directory raportowanie](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-azure-portal). 

---

### <a name="employeeid-claim-available-as-user-attribute-and-user-identifier"></a>Rolę IDPracownika dostępną jako atrybut użytkownika i identyfikator użytkownika

**Typ:** Nowa funkcja  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwość produktu:** ZWRÓCIŁ
 
Wartość **IDPracownika** można skonfigurować jako identyfikator użytkownika i atrybut użytkownika dla użytkowników należących do członków i gościa B2B w aplikacjach logowania opartych na języku SAML w interfejsie użytkownika aplikacji przedsiębiorstwa.

Aby uzyskać więcej informacji, zobacz [Dostosowywanie oświadczeń wystawionych w tokenie SAML dla aplikacji dla przedsiębiorstw w Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization).

---

### <a name="simplified-application-management-using-wildcards-in-azure-ad-application-proxy"></a>Uproszczone zarządzanie aplikacjami przy użyciu symboli wieloznacznych na platformie Azure serwer proxy aplikacji usługi Azure AD

**Typ:** Nowa funkcja  
**Kategoria usługi:** Serwer proxy aplikacji  
**Możliwość produktu:** Uwierzytelnianie użytkownika
 
Aby ułatwić wdrażanie aplikacji i zmniejszyć koszty administracyjne, Obsługujemy teraz możliwość publikowania aplikacji przy użyciu symboli wieloznacznych. Aby opublikować aplikację z symbolami wieloznacznymi, możesz użyć standardowego przepływu publikowania aplikacji, ale w wewnętrznych i zewnętrznych adresach URL Użyj symbolu wieloznacznego.

Aby uzyskać więcej informacji, zobacz [symbole wieloznaczne w Azure Active Directory serwerze proxy aplikacji](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-wildcard)

---

### <a name="new-cmdlets-to-support-configuration-of-application-proxy"></a>Nowe polecenia cmdlet do obsługi konfiguracji serwera proxy aplikacji

**Typ:** Nowa funkcja  
**Kategoria usługi:** Serwer proxy aplikacji  
**Możliwość produktu:** Platformach

Najnowsza wersja modułu AzureAD PowerShell w wersji zapoznawczej zawiera nowe polecenia cmdlet, które umożliwiają klientom Konfigurowanie aplikacji serwera proxy aplikacji przy użyciu programu PowerShell.

Nowe polecenia cmdlet są następujące: 

- Get-AzureADApplicationProxyApplication
- Get-AzureADApplicationProxyApplicationConnectorGroup
- Get-AzureADApplicationProxyConnector
- Get-AzureADApplicationProxyConnectorGroup
- Get-AzureADApplicationProxyConnectorGroupMembers
- Get-AzureADApplicationProxyConnectorMemberOf
- New-AzureADApplicationProxyApplication
- New-AzureADApplicationProxyConnectorGroup
- Remove-AzureADApplicationProxyApplication
- Remove-AzureADApplicationProxyApplicationConnectorGroup
- Remove-AzureADApplicationProxyConnectorGroup
- Set-AzureADApplicationProxyApplication
- Set-AzureADApplicationProxyApplicationConnectorGroup
- Set-AzureADApplicationProxyApplicationCustomDomainCertificate
- Set-AzureADApplicationProxyApplicationSingleSignOn
- Set-AzureADApplicationProxyConnector
- Set-AzureADApplicationProxyConnectorGroup

---
 
### <a name="new-cmdlets-to-support-configuration-of-groups"></a>Nowe polecenia cmdlet do obsługi konfiguracji grup

**Typ:** Nowa funkcja  
**Kategoria usługi:** Serwer proxy aplikacji  
**Możliwość produktu:** Platformach

Najnowsza wersja modułu AzureAD PowerShell zawiera polecenia cmdlet służące do zarządzania grupami w usłudze Azure AD. Te polecenia cmdlet były wcześniej dostępne w module AzureADPreview i są teraz dodawane do modułu AzureAD

Polecenia cmdlet grupy, które są teraz dostępne do ogólnej dostępności, to: 

- Get-AzureADMSGroup
- New-AzureADMSGroup
- Remove-AzureADMSGroup
- Set-AzureADMSGroup
- Get-AzureADMSGroupLifecyclePolicy
- New-AzureADMSGroupLifecyclePolicy
- Remove-AzureADMSGroupLifecyclePolicy
- Add-AzureADMSLifecyclePolicyGroup
- Remove-AzureADMSLifecyclePolicyGroup
- Reset-AzureADMSLifeCycleGroup   
- Get-AzureADMSLifecyclePolicyGroup

---
 
### <a name="a-new-release-of-azure-ad-connect-is-available"></a>Dostępna jest nowa wersja Azure AD Connect

**Typ:** Nowa funkcja  
**Kategoria usługi:** AD Sync  
**Możliwość produktu:** Platformach
 
Azure AD Connect jest preferowanym narzędziem do synchronizowania danych między usługą Azure AD i lokalnymi źródłami danych, w tym z systemem Windows Server Active Directory i protokołem LDAP.

>[!Important]
>Ta kompilacja wprowadza zmiany reguł schematu i synchronizacji. Usługa synchronizacji Azure AD Connect wyzwala pełne kroki importowania i pełnej synchronizacji po uaktualnieniu. Aby uzyskać informacje na temat zmiany tego zachowania, zobacz [jak odroczyć pełną synchronizację po uaktualnieniu](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-upgrade-previous-version#how-to-defer-full-synchronization-after-upgrade).

Ta wersja obejmuje następujące aktualizacje i zmiany:

**Rozwiązano problemy**

- Naprawianie okna chronometrażu zadań w tle dla strony filtrowania partycji podczas przełączania na następną stronę.

- Rozwiązano błąd, który spowodował naruszenie zasad dostępu podczas akcji niestandardowej ConfigDB.

- Rozwiązano usterkę do odzyskania z limitu czasu połączenia SQL.

- Rozwiązano problem polegający na tym, że certyfikaty z symbolami wieloznacznymi sieci SAN kończą się niepowodzeniem.

- Naprawiono usterkę powodującą awarię MIIServer. exe podczas eksportowania łącznika usługi AAD.

- Naprawiono usterkę polegającą na tym, że podczas uruchamiania w kontrolerze domeny zostało zarejestrowane złe hasło przy użyciu Kreatora połączenia usługi AAD, aby zmienić konfigurację

**Nowe funkcje i ulepszenia**
 
- Telemetria aplikacji — Administratorzy mogą przełączać tę klasę danych na włączony/wyłączony.

- Dane dotyczące kondycji usługi Azure AD — Administratorzy muszą odwiedzić portal kondycji, aby kontrolować ich ustawienia kondycji. Po zmianie zasad usługi Agenty będą je odczytywać i wymuszać.

- Dodano akcje konfiguracji zapisywania zwrotnego urządzeń i pasek postępu dla inicjowania strony.

- Ulepszona ogólna Diagnostyka z raportem HTML i pełną kolekcją danych w raporcie ZIP-Text/HTML.

- Ulepszona niezawodność autouaktualnienia i dodano dodatkową telemetrię, aby zapewnić możliwość określenia kondycji serwera.

- Ogranicz uprawnienia dostępne dla kont uprzywilejowanych na koncie łącznika usługi AD. W przypadku nowych instalacji Kreator ogranicza uprawnienia, które konta uprzywilejowane znajdują się na koncie MSOL po utworzeniu konta MSOL. Zmiany dotyczą instalacji ekspresowych i instalacji niestandardowych przy użyciu konta AutoCreate.

- Zmieniono Instalatora, aby nie wymagał uprawnień SA po czystej instalacji programu AADConnect.

- Nowe narzędzie do rozwiązywania problemów z synchronizacją dla określonego obiektu. Obecnie narzędzie sprawdza następujące kwestie:

    - Niezgodność UserPrincipalName między zsynchronizowanym obiektem użytkownika a kontem użytkownika w dzierżawie usługi Azure AD.
  
    - Jeśli obiekt jest filtrowany z synchronizacji z powodu filtrowania domeny
  
    - Jeśli obiekt jest filtrowany z synchronizacji z powodu filtrowania jednostki organizacyjnej (OU)

- Nowe narzędzie do synchronizowania bieżącego skrótu hasła przechowywanego w Active Directory lokalnym dla określonego konta użytkownika. Narzędzie nie wymaga zmiany hasła. 

---
 
### <a name="applications-supporting-intune-app-protection-policies-added-for-use-with-azure-ad-application-based-conditional-access"></a>Aplikacje obsługujące zasady Intune App Protection dodane do użycia z dostępem warunkowym opartym na aplikacji usługi Azure AD

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Dostęp warunkowy  
**Możliwość produktu:** Ochrona tożsamości & zabezpieczenia

Dodaliśmy więcej aplikacji obsługujących dostęp warunkowy oparty na aplikacji. Teraz możesz uzyskać dostęp do pakietu Office 365 i innych aplikacji w chmurze połączonych z usługą Azure AD przy użyciu tych zatwierdzonych aplikacji klienckich.

Następujące aplikacje zostaną dodane do końca lutego:

- Microsoft Power BI

- Microsoft Launcher

- Microsoft Invoicing

Aby uzyskać więcej informacji, zobacz:

- [Wymagania dotyczące zatwierdzonej aplikacji klienckiej](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#approved-client-app-requirement)
- [Dostęp warunkowy oparty na aplikacji usługi Azure AD](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)

---

### <a name="terms-of-use-update-to-mobile-experience"></a>Warunki użytkowania aktualizacji do środowiska mobilnego 

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Warunki użytkowania  
**Możliwość produktu:** Przepisów

Kiedy są wyświetlane warunki użytkowania, możesz teraz kliknąć problemy z **wyświetlaniem? Kliknij tutaj**. Kliknięcie tego linku otwiera warunki użytkowania natywnie na urządzeniu. Niezależnie od rozmiaru czcionki w dokumencie lub rozmiaru ekranu urządzenia, możesz powiększyć i odczytać dokument zgodnie z wymaganiami. 

---
 
## <a name="january-2018"></a>Styczeń 2018 r.
 
### <a name="new-federated-apps-available-in-azure-ad-app-gallery"></a>Nowe aplikacje federacyjne dostępne w galerii aplikacji usługi Azure AD 

**Typ:** Nowa funkcja  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwości produktu:** integracja innej firmy

W styczniu 2018 dodano następujące nowe aplikacje z obsługą Federacji w galerii aplikacji:

[IBM OpenPages](https://go.microsoft.com/fwlink/?linkid=864698), [OneTrust Management Software](https://go.microsoft.com/fwlink/?linkid=861660), [Dealpath](https://go.microsoft.com/fwlink/?linkid=863526), [IriusRisk federacyjnego Directory i [wierność zalety](https://go.microsoft.com/fwlink/?linkid=864701).

Aby uzyskać więcej informacji na temat aplikacji, zobacz [SaaS Application Integration with Azure Active Directory](https://aka.ms/appstutorial).

Aby uzyskać więcej informacji na temat tworzenia listy aplikacji w galerii aplikacji usługi Azure AD, zobacz [Wyświetlanie listy aplikacji w galerii aplikacji Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing). 

---
 
### <a name="sign-in-with-additional-risk-detected"></a>Zalogować się przy wykryciu dodatkowego ryzyka

**Typ:** Nowa funkcja  
**Kategoria usługi:** Ochrona tożsamości  
**Możliwość produktu:** Ochrona tożsamości & zabezpieczenia

Szczegółowe informacje na temat wykrytego wykrywania ryzyka są powiązane z subskrypcją usługi Azure AD. Za pomocą Azure AD — wersja Premium P2 można uzyskać najbardziej szczegółowe informacje na temat wszystkich podstawowych wykryć.

W przypadku wersji Azure AD — wersja Premium P1 wykryte wykrywania, które nie są objęte licencją, są wyświetlane w przypadku wykrycia ryzyka podczas logowania przy użyciu dodatkowego ryzyka.

Aby uzyskać więcej informacji, zobacz [Azure Active Directory wykrywania zagrożeń](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-risk-events).
 
---

### <a name="hide-office-365-applications-from-end-users-access-panels"></a>Ukryj aplikacje pakietu Office 365 z poziomu paneli dostępu użytkownika końcowego

**Typ:** Nowa funkcja  
**Kategoria usługi:** Moje aplikacje  
**Możliwość produktu:** ZWRÓCIŁ

Teraz można lepiej zarządzać sposobem wyświetlania aplikacji pakietu Office 365 w panelach dostępu użytkownika za pomocą nowego ustawienia użytkownika. Ta opcja jest pomocna w przypadku zmniejszenia liczby aplikacji w panelu dostępu użytkownika, jeśli wolisz wyświetlać tylko aplikacje pakietu Office w portalu usługi Office. Ustawienie znajduje się w **ustawieniach użytkownika** i ma etykietę, **Użytkownicy mogą widzieć tylko aplikacje pakietu Office 365 w portalu pakietu Office 365**.

Aby uzyskać więcej informacji, zobacz [ukrywanie aplikacji ze środowiska użytkownika w Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-hide-third-party-app).

---
 
### <a name="seamless-sign-into-apps-enabled-for-password-sso-directly-from-apps-url"></a>Bezproblemowe logowanie do aplikacji z włączonym logowaniem jednokrotnym hasła bezpośrednio z adresu URL aplikacji 

**Typ:** Nowa funkcja  
**Kategoria usługi:** Moje aplikacje  
**Możliwość produktu:** ZWRÓCIŁ

Rozszerzenie przeglądarki Moje aplikacje jest teraz dostępne za pośrednictwem wygodnego narzędzia zapewniającego możliwość logowania jednokrotnego na stronie Moje aplikacje jako skrót w przeglądarce. Po zainstalowaniu użytkownik zobaczy ikonę Gofr w swojej przeglądarce, która zapewni im szybki dostęp do aplikacji. Użytkownicy mogą teraz korzystać z programu:

- Możliwość bezpośredniej rejestracji w aplikacjach opartych na rejestracji jednokrotnej na stronie logowania aplikacji
- Uruchamianie dowolnej aplikacji przy użyciu funkcji szybkiego wyszukiwania
- Skróty do ostatnio używanych aplikacji z rozszerzenia
- Rozszerzenie jest dostępne dla przeglądarki Microsoft Edge, Chrome i Firefox.
 
Aby uzyskać więcej informacji, zobacz [Moje aplikacje bezpieczne logowanie](../user-help/my-apps-portal-end-user-access.md#download-and-install-the-my-apps-secure-sign-in-extension).

---

### <a name="azure-ad-administration-experience-in-azure-classic-portal-has-been-retired"></a>Środowisko administrowania usługą Azure AD w klasyczny portal Azure zostało wycofane

**Typ:** Przestarzałe   
**Kategoria usługi:** Usługa Azure AD  
**Możliwość produktu:** Katalogi

Od 8 stycznia 2018 środowisko administracyjne Azure AD w klasycznym portalu Azure zostało wycofane. Miało to miejsce w połączeniu z wycofywaniem z klasycznego portalu Azure. W przyszłości należy użyć [Centrum administracyjnego usługi Azure AD](https://aad.portal.azure.com) dla całej administracji opartej na portalu w usłudze Azure AD.
 
---

### <a name="the-phonefactor-web-portal-has-been-retired"></a>Portal sieci Web PhoneFactor został wycofany

**Typ:** Przestarzałe  
**Kategoria usługi:** Usługa Azure AD  
**Możliwość produktu:** Katalogi
 
Od 8 stycznia 2018 Portal sieci Web PhoneFactor został wycofany. Ten portal był używany do administrowania serwerem usługi MFA, ale te funkcje zostały przeniesione do Azure Portal w portal.azure.com. 

Konfiguracja usługi MFA znajduje się w lokalizacji: **Azure Active Directory \> serwera usługi MFA**
 
---
 
### <a name="deprecate-azure-ad-reports"></a>Przestarzałe raporty usługi Azure AD

**Typ:** Przestarzałe  
**Kategoria usługi:** Reporting  
**Możliwość produktu:** Zarządzanie cyklem życia tożsamości  


Dzięki ogólnej dostępności nowej konsoli administracyjnej Azure Active Directory i nowych interfejsów API dla obu raportów o działaniach i zabezpieczeniach interfejsy API raportów w punkcie końcowym "/Reports" zostały wycofane z 31 grudnia 2017.

**Co jest dostępne?**

W ramach przejścia do nowej konsoli administracyjnej udostępniono 2 nowe interfejsy API do pobierania dzienników aktywności usługi Azure AD. Nowy zestaw interfejsów API zapewnia bogatsze funkcje filtrowania i sortowania, a także zapewnia bogatsze działania związane z inspekcją i logowaniem. Dostęp do danych dostępnych wcześniej za pomocą raportów zabezpieczeń można teraz uzyskać za pomocą interfejsu API wykrywania ryzyka ochrony tożsamości w Microsoft Graph.

Aby uzyskać więcej informacji, zobacz:

- [Wprowadzenie do interfejsu API raportowania Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-getting-started-azure-portal)

- [Wprowadzenie do Azure Active Directory Identity Protection i Microsoft Graph](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection-graph-getting-started)

---

## <a name="december-2017"></a>Grudzień 2017

### <a name="terms-of-use-in-the-access-panel"></a>Warunki użytkowania w panelu dostępu

**Typ:** Nowa funkcja  
**Kategoria usługi:** Warunki użytkowania  
**Możliwość produktu:** Przepisów
 
Teraz możesz przejść do panelu dostępu i wyświetlić warunki użytkowania, które zostały wcześniej zaakceptowane.

Wykonaj następujące kroki:

1. Przejdź do [portalu Moje aplikacje](https://myapps.microsoft.com)i zaloguj się.

2. W prawym górnym rogu wybierz swoją nazwę, a następnie z listy wybierz pozycję **profil** . 

3. W **profilu**wybierz pozycję **Przejrzyj warunki użytkowania**. 

4. Teraz możesz przejrzeć zaakceptowane warunki użytkowania. 

Aby uzyskać więcej informacji, zobacz [Funkcja warunki użytkowania usługi Azure AD (wersja zapoznawcza)](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use).
 
---
 
### <a name="new-azure-ad-sign-in-experience"></a>Nowe środowisko logowania do usługi Azure AD

**Typ:** Nowa funkcja  
**Kategoria usługi:** Usługa Azure AD  
**Możliwość produktu:** Uwierzytelnianie użytkownika
 
Interfejsów użytkownika systemu tożsamości usługi Azure AD i konto Microsoft zostały zaprojektowane tak, aby miały spójny wygląd i działanie. Ponadto strona logowania usługi Azure AD najpierw zbiera nazwę użytkownika, a następnie poświadczenie na drugim ekranie.

Aby uzyskać więcej informacji, zobacz [nowe środowisko logowania do usługi Azure AD jest teraz dostępne w publicznej wersji zapoznawczej](https://cloudblogs.microsoft.com/enterprisemobility/2017/08/02/the-new-azure-ad-signin-experience-is-now-in-public-preview/).
 
---
 
### <a name="fewer-sign-in-prompts-a-new-keep-me-signed-in-experience-for-azure-ad-sign-in"></a>Mniej monitów logowania: nowe środowisko "nie wylogowuj mnie" dla logowania za pomocą usługi Azure AD

**Typ:** Nowa funkcja  
**Kategoria usługi:** Usługa Azure AD  
**Możliwość produktu:** Uwierzytelnianie użytkownika
 
Pole wyboru nie wylogowuj **mnie** na stronie logowania usługi Azure AD zostało zastąpione nowym monitem, który jest wyświetlany po pomyślnym uwierzytelnieniu. 

Jeśli **odpowie na** ten monit, usługa daje trwały token odświeżania. Takie zachowanie jest takie samo, jak w przypadku wybrania pola wyboru nie **wylogowuj mnie** w starym środowisku. W przypadku dzierżawców federacyjnych ten monit jest wyświetlany po pomyślnym uwierzytelnieniu w usłudze federacyjnej.

Aby uzyskać więcej informacji, zobacz [mniej monitów logowania: nowe środowisko "nie wylogowuj mnie" w usłudze Azure AD jest w wersji zapoznawczej](https://cloudblogs.microsoft.com/enterprisemobility/2017/09/19/fewer-login-prompts-the-new-keep-me-signed-in-experience-for-azure-ad-is-in-preview/). 

---

### <a name="add-configuration-to-require-the-terms-of-use-to-be-expanded-prior-to-accepting"></a>Dodaj konfigurację, aby wymagać, aby warunki użytkowania były rozwinięte przed zaakceptowaniem

**Typ:** Nowa funkcja  
**Kategoria usługi:** Warunki użytkowania  
**Możliwość produktu:** Przepisów
 
Opcja dla administratorów wymaga, aby użytkownicy mogli rozwijać warunki użytkowania przed zaakceptowaniem warunków.

Wybierz opcję **Włącz** lub **Wyłącz** , aby wymagać od użytkowników rozwinięcia warunków użytkowania. Ustawienie **on** wymaga od użytkowników wyświetlenia warunków użytkowania przed ich zaakceptowaniem.

Aby uzyskać więcej informacji, zobacz [Funkcja warunki użytkowania usługi Azure AD (wersja zapoznawcza)](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use).
 
---

### <a name="scoped-activation-for-eligible-role-assignments"></a>Aktywacja z zakresem dla kwalifikujących się przypisań ról

**Typ:** Nowa funkcja  
**Kategoria usługi:** Privileged Identity Management  
**Możliwość produktu:** Privileged Identity Management
 
Aktywacji z zakresem można użyć do aktywowania kwalifikujących się ról zasobów platformy Azure z mniejszą autonomią niż oryginalne wartości domyślne przypisania. Przykładem jest to, że użytkownik jest przypisywany jako właściciel subskrypcji w dzierżawie. W przypadku aktywacji z zakresem można aktywować rolę właściciela dla maksymalnie pięciu zasobów zawartych w ramach subskrypcji (takich jak grupy zasobów i maszyny wirtualne). Określanie zakresu aktywacji może ograniczyć możliwość wykonywania niepożądanych zmian w krytycznych zasobach platformy Azure.

Aby uzyskać więcej informacji, zobacz [co to jest Azure AD Privileged Identity Management?](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure).
 
---
 
### <a name="new-federated-apps-in-the-azure-ad-app-gallery"></a>Nowe aplikacje federacyjne w galerii aplikacji usługi Azure AD

**Typ:** Nowa funkcja  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwości produktu:** integracja innej firmy

W grudniu 2017 dodaliśmy te nowe aplikacje z obsługą Federacji do naszej galerii aplikacji:

[Accredible](https://go.microsoft.com/fwlink/?linkid=863523), Adobe Experience Manager, [Digital](https://go.microsoft.com/fwlink/?linkid=861685)websklepie, [Communifire](https://go.microsoft.com/fwlink/?linkid=861676) CybSafe, [FactSet](https://go.microsoft.com/fwlink/?linkid=863525), [Image działa](https://go.microsoft.com/fwlink/?linkid=863517), [mobi](https://go.microsoft.com/fwlink/?linkid=863521), [MobileIron Azure AD Integration](https://go.microsoft.com/fwlink/?linkid=858027), [Reflektive](https://go.microsoft.com/fwlink/?linkid=863518), [SAML SSO Bamboo przez rozwiązanie GmbH](https://go.microsoft.com/fwlink/?linkid=863520), [Logowanie jednokrotne SAML dla BitBucket przez rozwiązanie GmbH](https://go.microsoft.com/fwlink/?linkid=863519), [Vodeclic](https://go.microsoft.com/fwlink/?linkid=863522), WebHR, Zenegy integrację z usługą Azure AD.

Aby uzyskać więcej informacji na temat aplikacji, zobacz [SaaS Application Integration with Azure Active Directory](https://aka.ms/appstutorial).

Aby uzyskać więcej informacji na temat tworzenia listy aplikacji w galerii aplikacji usługi Azure AD, zobacz [Wyświetlanie listy aplikacji w galerii aplikacji Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing). 
 
---

### <a name="approval-workflows-for-azure-ad-directory-roles"></a>Przepływy pracy zatwierdzania dla ról w katalogu usługi Azure AD

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Privileged Identity Management  
**Możliwość produktu:** Privileged Identity Management
 
Przepływ pracy zatwierdzania dla ról w katalogu usługi Azure AD jest ogólnie dostępny.

Za pomocą przepływu pracy zatwierdzania Administratorzy ról uprzywilejowanych mogą wymagać od członków ról uprawnionych do żądania aktywacji roli, zanim będą mogli korzystać z roli uprzywilejowanej. Wiele użytkowników i grup może być delegowana odpowiedzialność za zatwierdzenie. Uprawnieni członkowie roli otrzymują powiadomienia po zakończeniu zatwierdzania, a ich rola jest aktywna.

---
 
### <a name="pass-through-authentication-skype-for-business-support"></a>Uwierzytelnianie przekazywane: obsługa usługi Skype dla firm

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Uwierzytelnienia (nazwy logowania)  
**Możliwość produktu:** Uwierzytelnianie użytkownika

Uwierzytelnianie przekazywane teraz obsługuje Logowanie użytkowników do aplikacji klienckich programu Skype dla firm, które obsługują nowoczesne uwierzytelnianie, w tym topologie online i hybrydowe. 

Aby uzyskać więcej informacji, zobacz temat [topologie programu Skype dla firm obsługujące nowoczesne uwierzytelnianie](https://technet.microsoft.com/library/mt803262.aspx).
 
---

### <a name="updates-to-azure-ad-privileged-identity-management-for-azure-rbac-preview"></a>Aktualizacje Azure AD Privileged Identity Management dla usługi Azure RBAC (wersja zapoznawcza)

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Privileged Identity Management  
**Możliwość produktu:** Privileged Identity Management
 
Korzystając z publicznej wersji zapoznawczej programu Azure AD Privileged Identity Management (PIM) dla Access Control opartej na rolach (RBAC) na platformie Azure, możesz teraz:

* Używaj wystarczającej ilości miejsca do administrowania.
* Wymagaj zatwierdzenia w celu aktywowania ról zasobów.
* Zaplanuj przyszłą aktywację roli wymagającą zatwierdzenia dla ról usługi Azure AD i kontroli RBAC platformy Azure.
 
Aby uzyskać więcej informacji, zobacz [Privileged Identity Management dla zasobów platformy Azure (wersja zapoznawcza)](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/azure-pim-resource-rbac).

---
 
## <a name="november-2017"></a>Listopad 2017
 
### <a name="access-control-service-retirement"></a>Wycofanie usługi Access Control

**Typ:** Planowanie zmiany  
**Kategoria usługi:** Usługa Access Control  
**Możliwość produktu:** Usługa Access Control 

Azure Active Directory Access Control (znana także jako usługa Access Control) zostanie wycofana z opóźnieniem 2018. Więcej informacji obejmujących szczegółowy harmonogram i wskazówki dotyczące migracji wysokiego poziomu zostaną podane w ciągu następnych kilku tygodni. Możesz pozostawić Komentarze na tej stronie z wszelkimi pytaniami dotyczącymi usługi Access Control, a członek zespołu otrzyma odpowiedzi na nie.

---

### <a name="restrict-browser-access-to-the-intune-managed-browser"></a>Ogranicz dostęp do przeglądarki do Intune Managed Browser 

**Typ:** Planowanie zmiany  
**Kategoria usługi:** Dostęp warunkowy  
**Możliwość produktu:** Bezpieczeństwo i Ochrona tożsamości

Możesz ograniczyć dostęp do przeglądarki do pakietu Office 365 i innych aplikacji w chmurze połączonych z usługą Azure AD przy użyciu Intune Managed Browser jako zatwierdzonej aplikacji. 

Teraz można skonfigurować następujący warunek dla dostępu warunkowego opartego na aplikacji:

**Aplikacje klienckie:** Przeglądarka

**Jaki jest wpływ zmiany?**

Obecnie dostęp jest blokowany w przypadku korzystania z tego warunku. Gdy wersja zapoznawcza jest dostępna, wszystkie uprawnienia dostępu będą wymagały używania aplikacji Managed Browser. 

Zapoznaj się z tą możliwością i więcej informacji w nadchodzących blogach i informacjach o wersji. 

Aby uzyskać więcej informacji, zobacz [dostęp warunkowy w usłudze Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal).
 
---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Nowe zatwierdzone aplikacje klienckie dla dostępu warunkowego opartego na aplikacji usługi Azure AD

**Typ:** Planowanie zmiany  
**Kategoria usługi:** Dostęp warunkowy  
**Możliwość produktu:** Bezpieczeństwo i Ochrona tożsamości

Następujące aplikacje znajdują się na liście [zatwierdzonych aplikacji klienckich](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#approved-client-app-requirement):

- [Usługi kaizala firmy Microsoft](https://www.microsoft.com/garage/profiles/kaizala/)
- Microsoft StaffHub

Aby uzyskać więcej informacji, zobacz:

- [Wymagania dotyczące zatwierdzonej aplikacji klienckiej](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#approved-client-app-requirement)
- [Dostęp warunkowy oparty na aplikacji usługi Azure AD](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)

---

### <a name="terms-of-use-support-for-multiple-languages"></a>Obsługa warunków użytkowania w wielu językach

**Typ:** Nowa funkcja    
**Kategoria usługi:** Warunki użytkowania  
**Możliwość produktu:** Przepisów

Administratorzy mogą teraz tworzyć nowe warunki użytkowania zawierające wiele dokumentów PDF. Można oznaczyć te dokumenty PDF przy użyciu odpowiedniego języka. Użytkownicy są wyświetlani w formacie PDF przy użyciu odpowiedniego języka w oparciu o ich preferencje. Jeśli nie ma dopasowania, wyświetlany jest język domyślny.

---
 
### <a name="real-time-password-writeback-client-status"></a>Stan klienta zapisywania zwrotnego hasła w czasie rzeczywistym

**Typ:** Nowa funkcja  
**Kategoria usługi:** Samoobsługowe resetowanie hasła  
**Możliwość produktu:** Uwierzytelnianie użytkownika

Teraz można sprawdzić stan lokalnego klienta zapisywania zwrotnego haseł. Ta opcja jest dostępna w sekcji **integracja lokalna na** stronie [resetowania hasła](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/PasswordReset) . 

Jeśli występują problemy z połączeniem z lokalnym klientem zapisywania zwrotnego, zobaczysz komunikat o błędzie, który oferuje następujące informacje:

- Informacje o tym, dlaczego nie można nawiązać połączenia z lokalnym klientem zapisywania zwrotnego.
- Link do dokumentacji, która pomaga w rozwiązaniu problemu. 

Aby uzyskać więcej informacji, zobacz [integracja lokalna](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-how-it-works#on-premises-integration).

---

### <a name="azure-ad-app-based-conditional-access"></a>Dostęp warunkowy oparty na aplikacji usługi Azure AD 
 
**Typ:** Nowa funkcja  
**Kategoria usługi:** Usługa Azure AD  
**Możliwość produktu:** Bezpieczeństwo i Ochrona tożsamości

Teraz możesz ograniczyć dostęp do pakietu Office 365 i innych aplikacji w chmurze połączonych z usługą Azure AD do [zatwierdzonych aplikacji klienckich](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#approved-client-app-requirement) , które obsługują zasady ochrony aplikacji usługi Intune przy użyciu [dostępu warunkowego opartego na aplikacji w usłudze Azure AD](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access). Zasady ochrony aplikacji usługi Intune służą do konfigurowania i ochrony danych firmowych w tych aplikacjach klienckich.

Łącząc [aplikacje oparte](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access) na urządzeniach z zasadami dostępu warunkowego [opartymi na urządzeniu](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-policy-connected-applications) , można zapewnić ochronę danych na urządzeniach osobistych i firmowych.

Następujące warunki i kontrolki są teraz dostępne do użycia z dostępem warunkowym opartym na aplikacji:

**Obsługiwany warunek platformy**

- iOS
- Android

**Warunek aplikacji klienckich**

- Aplikacje mobilne i klienci stacjonarni

**Kontrola dostępu**

- Wymagaj zatwierdzonej aplikacji klienckiej

Aby uzyskać więcej informacji, zobacz [dostęp warunkowy oparty na aplikacji usługi Azure AD](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access).
 
---

### <a name="manage-azure-ad-devices-in-the-azure-portal"></a>Zarządzanie urządzeniami usługi Azure AD w Azure Portal

**Typ:** Nowa funkcja  
**Kategoria usługi:** Rejestrowanie urządzeń i zarządzanie nimi  
**Możliwość produktu:** Bezpieczeństwo i Ochrona tożsamości

Teraz można znaleźć wszystkie urządzenia połączone z usługą Azure AD oraz działania związane z urządzeniami w jednym miejscu. Istnieje nowe środowisko administracyjne do zarządzania wszystkimi tożsamościami i ustawieniami urządzeń w Azure Portal. W tej wersji można:

- Wyświetl wszystkie urządzenia, które są dostępne dla dostępu warunkowego w usłudze Azure AD.
- Wyświetl właściwości, w tym urządzenia dołączone do hybrydowej usługi Azure AD.
- Znajdź klucze funkcji BitLocker dla urządzeń przyłączonych do usługi Azure AD, Zarządzaj urządzeniem w usłudze Intune i nie tylko.
- Zarządzanie ustawieniami związanymi z urządzeniami usługi Azure AD.

Aby uzyskać więcej informacji, zobacz [Zarządzanie urządzeniami przy użyciu Azure Portal](https://docs.microsoft.com/azure/active-directory/device-management-azure-portal).

---

### <a name="support-for-macos-as-a-device-platform-for-azure-ad-conditional-access"></a>Obsługa macOS jako platformy urządzeń dla dostępu warunkowego usługi Azure AD 

**Typ:** Nowa funkcja    
**Kategoria usługi:** Dostęp warunkowy  
**Możliwość produktu:** Bezpieczeństwo i Ochrona tożsamości 

Teraz można uwzględnić (lub wykluczyć) macOS jako warunek platformy urządzenia w zasadach dostępu warunkowego usługi Azure AD. Dodanie macOS do obsługiwanych platform urządzeń pozwala wykonać następujące działania:

- **Rejestrowanie urządzeń macOS i zarządzanie nimi za pomocą usługi Intune.** Podobnie jak w przypadku innych platform, takich jak iOS i Android, aplikacja Portal firmy jest dostępna do macOS na potrzeby ujednoliconych rejestracji. Możesz użyć nowej aplikacji Portal firmy dla usługi macOS, aby zarejestrować urządzenie w usłudze Intune i zarejestrować je w usłudze Azure AD.
- **Upewnij się, że urządzenia macOS są zgodne z zasadami zgodności organizacji zdefiniowanymi w usłudze Intune.** W usłudze Intune na Azure Portal można teraz skonfigurować zasady zgodności dla urządzeń macOS. 
- **Ogranicz dostęp do aplikacji w usłudze Azure AD tylko do zgodnych urządzeń macOS.** Tworzenie zasad dostępu warunkowego ma macOS jako osobną opcję platformy urządzeń. Teraz można tworzyć zasady dostępu warunkowego dla macOS dla zestawu aplikacji dla konkretnych platform na platformie Azure.

Aby uzyskać więcej informacji, zobacz:

- [Tworzenie zasad zgodności urządzeń dla urządzeń z systemem macOS w usłudze Intune](https://aka.ms/macoscompliancepolicy)
- [Dostęp warunkowy w usłudze Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal)
 
---

### <a name="network-policy-server-extension-for-azure-multi-factor-authentication"></a>Rozszerzenie serwera zasad sieciowych dla usługi Azure Multi-Factor Authentication 

**Typ:** Nowa funkcja    
**Kategoria usługi:**  Uwierzytelnianie wieloskładnikowe  
**Możliwość produktu:** Uwierzytelnianie użytkownika

Rozszerzenie serwera zasad sieciowych dla platformy Azure Multi-Factor Authentication dodaje do infrastruktury uwierzytelniania możliwości Multi-Factor Authentication oparte na chmurze, używając istniejących serwerów. Za pomocą rozszerzenia serwera zasad sieciowych możesz dodać połączenie telefoniczne, wiadomość SMS lub weryfikację aplikacji telefonicznej do istniejącego przepływu uwierzytelniania. Nie trzeba instalować, konfigurować i obsługiwać nowych serwerów. 

To rozszerzenie zostało utworzone dla organizacji, które chcą chronić połączenia wirtualnej sieci prywatnej bez wdrażania Serwer Multi-Factor Authentication platformy Azure. Rozszerzenie serwera zasad sieciowych pełni rolę karty między usługą RADIUS i opartymi na chmurze Multi-Factor Authentication Azure, aby zapewnić drugi czynnik uwierzytelniania dla federacyjnych lub synchronizowanych użytkowników.

Aby uzyskać więcej informacji, zobacz [integrowanie istniejącej infrastruktury serwera zasad sieciowych z usługą Azure Multi-Factor Authentication](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-nps-extension).
 
---

### <a name="restore-or-permanently-remove-deleted-users"></a>Przywracanie lub trwałe usuwanie usuniętych użytkowników

**Typ:** Nowa funkcja    
**Kategoria usługi:** Zarządzanie użytkownikami  
**Możliwość produktu:** Katalogi 

W centrum administracyjnym usługi Azure AD możesz teraz:

- Przywracanie usuniętego użytkownika. 
- Trwale Usuń użytkownika.

**Aby wypróbować ten element:**

1. W centrum administracyjnym usługi Azure AD wybierz pozycję [Wszyscy użytkownicy](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/All) w sekcji **Zarządzanie** . 

2. Z listy **Pokaż** wybierz pozycję **ostatnio usunięci użytkownicy**. 

3. Wybierz co najmniej jednego z ostatnio usuniętych użytkowników, a następnie Przywróć je lub trwale Usuń.
 
---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Nowe zatwierdzone aplikacje klienckie dla dostępu warunkowego opartego na aplikacji usługi Azure AD
 
**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Dostęp warunkowy  
**Możliwość produktu:** Bezpieczeństwo i Ochrona tożsamości

Następujące aplikacje zostały dodane do listy [zatwierdzonych aplikacji klienckich](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#approved-client-app-requirement):

- Microsoft Planner
- Azure Information Protection 

Aby uzyskać więcej informacji, zobacz:

- [Wymagania dotyczące zatwierdzonej aplikacji klienckiej](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#approved-client-app-requirement)
- [Dostęp warunkowy oparty na aplikacji usługi Azure AD](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)

---

### <a name="use-or-between-controls-in-a-conditional-access-policy"></a>Używanie "OR" między kontrolkami w zasadach dostępu warunkowego 

**Typ:** Zmieniono funkcję    
**Kategoria usługi:** Dostęp warunkowy  
**Możliwość produktu:** Bezpieczeństwo i Ochrona tożsamości
 
Teraz można użyć opcji "OR" (wymaga jednej z wybranych kontrolek) do kontroli dostępu warunkowego. Za pomocą tej funkcji można tworzyć zasady z "lub" między kontrolkami dostępu. Można na przykład użyć tej funkcji w celu utworzenia zasad, które wymagają od użytkownika zalogowania się za pomocą Multi-Factor Authentication "lub" jako na zgodnym urządzeniu.

Aby uzyskać więcej informacji, zobacz [mechanizmy kontroli dostępu warunkowego usługi Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-controls).
 
---

### <a name="aggregation-of-real-time-risk-detections"></a>Agregacja wykrycia ryzyka w czasie rzeczywistym

**Typ:** Zmieniono funkcję    
**Kategoria usługi:** Ochrona tożsamości  
**Możliwość produktu:** Bezpieczeństwo i Ochrona tożsamości

W Azure AD Identity Protection wszystkie wykrycia ryzyka w czasie rzeczywistym pochodzące z tego samego adresu IP w danym dniu są teraz agregowane dla każdego typu wykrywania ryzyka. Ta zmiana ogranicza ilość wykrytych wykryć ryzyka bez zmian w zabezpieczeniach użytkownika.

Podstawowe wykrywanie w czasie rzeczywistym działa po każdym zalogowaniu się użytkownika. W przypadku skonfigurowania zasad zabezpieczeń dotyczących ryzyka związanego z logowaniem do Multi-Factor Authentication lub blokowania dostępu jest on nadal wyzwalany podczas każdego ryzykownego logowania.
 
---
 
## <a name="october-2017"></a>Październik 2017

### <a name="deprecate-azure-ad-reports"></a>Przestarzałe raporty usługi Azure AD

**Typ:** Planowanie zmiany  
**Kategoria usługi:** Reporting  
**Możliwość produktu:** Zarządzanie cyklem życia tożsamości  

Azure Portal oferuje następujące informacje:

- Nowa konsola administracyjna usługi Azure AD.
- Nowe interfejsy API dla raportów działań i zabezpieczeń.
 
Ze względu na te nowe możliwości zostały wycofane interfejsy API raportów w punkcie końcowym/Reports w dniu 10 grudnia 2017. 

---

### <a name="automatic-sign-in-field-detection"></a>Automatyczne wykrywanie pól logowania

**Typ:** FIXED   
**Kategoria usługi:** Moje aplikacje  
**Możliwość produktu:** Logowanie jednokrotne  

Usługa Azure AD obsługuje automatyczne wykrywanie pól logowania dla aplikacji, które renderują pole Nazwa użytkownika i hasło w formacie HTML. Te kroki są udokumentowane w [sposób automatycznego przechwytywania pól logowania dla aplikacji](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-password-single-sign-on-non-gallery-applications-problems#manually-capture-sign-in-fields-for-an-app). Tę możliwość można znaleźć, dodając aplikację *spoza galerii* na stronie aplikacje dla **przedsiębiorstw** w [Azure Portal](https://aad.portal.azure.com). Ponadto można skonfigurować tryb **logowania** jednokrotnego w tej nowej aplikacji do **logowania jednokrotnego opartego na hasłach**, wprowadzić adres URL sieci Web, a następnie zapisać stronę.
 
Ze względu na problem z usługą ta funkcja została tymczasowo wyłączona. Problem został rozwiązany, a automatyczne wykrywanie pól logowania jest dostępne ponownie.

---

### <a name="new-multi-factor-authentication-features"></a>Nowe funkcje Multi-Factor Authentication

**Typ:** Nowa funkcja  
**Kategoria usługi:** Uwierzytelnianie wieloskładnikowe  
**Możliwość produktu:** Bezpieczeństwo i Ochrona tożsamości  

Uwierzytelnianie wieloskładnikowe (MFA) to istotna część ochrony organizacji. Aby poświadczenia były bardziej adaptacyjne i bardziej bezproblemowe, dodano następujące funkcje: 

- Wyniki testu wieloskładnikowego są bezpośrednio zintegrowane z raportem logowania do usługi Azure AD, który obejmuje dostęp programistyczny do wyników usługi MFA.
- Konfiguracja usługi MFA jest bardziej głęboko zintegrowana z konfiguracją środowiska Azure AD w Azure Portal.

Dzięki tej publicznej wersji zapoznawczej zarządzanie i raportowanie usługi MFA jest zintegrowaną częścią podstawowego środowiska konfiguracji usługi Azure AD. Teraz możesz zarządzać funkcjami portalu zarządzania usługą MFA w ramach środowiska usługi Azure AD.

Aby uzyskać więcej informacji, zobacz temat [Informacje o raportowaniu usługi MFA w Azure Portal](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-sign-ins-mfa). 

---

### <a name="terms-of-use"></a>Warunki użytkowania

**Typ:** Nowa funkcja  
**Kategoria usługi:** Warunki użytkowania  
**Możliwość produktu:** Przepisów  

Warunki użytkowania usługi Azure AD umożliwiają prezentowanie informacji, takich jak odpowiednie odrzuty do wymagań prawnych lub zgodności dla użytkowników.

Warunków użytkowania usługi Azure AD można używać w następujących scenariuszach:

- Ogólne warunki użytkowania dla wszystkich użytkowników w organizacji
- Określone warunki użytkowania oparte na atrybutach użytkownika (na przykład lekarze lub pracownicy krajowi w porównaniu z pracownikami lokalnymi i międzynarodowymi, wykonane przez grupy dynamiczne)
- Specyficzne warunki użytkowania dotyczące uzyskiwania dostępu do aplikacji firmowych o dużym wpływie, takich jak Salesforce

Aby uzyskać więcej informacji, zobacz [warunki użytkowania usługi Azure AD](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use).

---

### <a name="enhancements-to-privileged-identity-management"></a>Ulepszenia Privileged Identity Management

**Typ:** Nowa funkcja  
**Kategoria usługi:** Privileged Identity Management  
**Możliwość produktu:** Privileged Identity Management  

Za pomocą Azure AD Privileged Identity Management możesz zarządzać dostępem do zasobów platformy Azure (wersja zapoznawcza) i kontrolować je oraz monitorować, aby:

- Subskrypcje
- Grupy zasobów
- Maszyny wirtualne 

Wszystkie zasoby w Azure Portal, które korzystają z funkcji RBAC platformy Azure, mogą korzystać ze wszystkich możliwości zarządzania zabezpieczeniami i cyklem życia, które Azure AD Privileged Identity Management oferty.

Aby uzyskać więcej informacji, zobacz [Privileged Identity Management dla zasobów platformy Azure](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/azure-pim-resource-rbac).

---

### <a name="access-reviews"></a>Przeglądy dostępu

**Typ:** Nowa funkcja  
**Kategoria usługi:** Przeglądy dostępu  
**Możliwość produktu:** Przepisów  

Organizacje mogą korzystać z przeglądów dostępu (wersja zapoznawcza) w celu wydajnego zarządzania członkostwem w grupach i dostępem do aplikacji dla przedsiębiorstw: 

- Przeprowadzając przeglądy dostępu, można ponownie certyfikować dostęp użytkowników-gości do aplikacji i ich członkostwo w grupach. Recenzenci mogą efektywnie decydować, czy zezwolić Gościom na kontynuowanie dostępu na podstawie szczegółowych informacji dostępnych w ramach przeglądów dostępu.
- Przeprowadzając przeglądy dostępu, można ponownie certyfikować dostęp do aplikacji i członkostwo w grupach pracowników.

Kontrole z przeglądem dostępu można łączyć w programy odpowiednie dla danej organizacji, aby śledzić przeglądy dla aplikacji dotyczących zgodności lub aplikacji narażonych na ryzyko.

Aby uzyskać więcej informacji, zobacz [przeglądy dostępu w usłudze Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-azure-ad-controls-access-reviews-overview).

---

### <a name="hide-third-party-applications-from-my-apps-and-the-office-365-app-launcher"></a>Ukryj aplikacje innych firm od moich aplikacji i uruchamiania aplikacji pakietu Office 365

**Typ:** Nowa funkcja  
**Kategoria usługi:** Moje aplikacje  
**Możliwość produktu:** Logowanie jednokrotne  

Teraz można lepiej zarządzać aplikacjami, które są wyświetlane w portalach użytkowników, za pomocą nowej właściwości **Ukryj aplikację** . Możesz ukryć aplikacje, aby pomóc w wykorzystaniu kafelków aplikacji na potrzeby usług zaplecza lub zduplikowanych kafelków oraz nieczytelnych uruchomień aplikacji użytkowników. Przełącznik znajduje się w sekcji **Właściwości** aplikacji innej firmy i jest oznaczony jako **widoczny dla użytkownika?** Możesz również programowo ukryć aplikację za pomocą programu PowerShell. 

Aby uzyskać więcej informacji, zobacz [ukrywanie aplikacji innych firm ze środowiska użytkownika w usłudze Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-hide-third-party-app). 


**Co jest dostępne?**

 W ramach przejścia do nowej konsoli administracyjnej dostępne są dwa nowe interfejsy API służące do pobierania dzienników aktywności usługi Azure AD. Nowy zestaw interfejsów API zapewnia bogatsze funkcje filtrowania i sortowania, a także zapewnia bogatsze działania związane z inspekcją i logowaniem. Dane, które wcześniej były dostępne za pomocą raportów zabezpieczeń, są teraz dostępne za pomocą interfejsu API wykrywania ryzyka ochrony tożsamości w Microsoft Graph.


## <a name="september-2017"></a>2017 września

### <a name="hotfix-for-identity-manager"></a>Poprawka dla programu Identity Manager

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Menedżer tożsamości  
**Możliwość produktu:** Zarządzanie cyklem życia tożsamości  

Pakiet zbiorczy poprawek (build 4.4.1642.0) jest dostępny od 25 września 2017 dla programu Identity Manager 2016 z dodatkiem Service Pack 1. Ten pakiet zbiorczy:

- Rozwiązuje problemy i dodaje ulepszenia.
- Jest aktualizacją zbiorczą, która zastępuje wszystkie aktualizacje programu Identity Manager 2016 z dodatkiem Service Pack 1 do kompilacji 4.4.1459.0 dla programu Identity Manager 2016. 
- Wymaga posiadania 4.4.1302.0 kompilacji programu Identity Manager 2016. 

Aby uzyskać więcej informacji, zobacz [pakiet zbiorczy poprawek (build 4.4.1642.0) jest dostępny dla programu Identity Manager 2016 z dodatkiem Service Pack 1](https://support.microsoft.com/help/4021562). 

---
