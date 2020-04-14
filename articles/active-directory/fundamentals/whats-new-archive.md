---
title: Archiwum dla Co nowego w usłudze Azure Active Directory? | Microsoft Docs
description: Informacje o nowościach w sekcji Przegląd tego zestawu zawartości zawiera 6 miesięcy aktywności. Po 6 miesiącach elementy są usuwane z głównego artykułu i umieszczane w tym artykule archiwum.
services: active-directory
author: msmimart
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: mimart
ms.reviewer: dhanyahk
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 25191951472e30492606ad97c440a13359c8ef24
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81253173"
---
# <a name="archive-for-whats-new-in-azure-active-directory"></a>Archiwum dla Co nowego w usłudze Azure Active Directory?

Podstawowy [Co nowego w usłudze Azure Active Directory?](whats-new.md) artykuł informacje o wersji zawiera aktualizacje z ostatnich sześciu miesięcy, podczas gdy ten artykuł zawiera wszystkie starsze informacje.

Co nowego w usłudze Azure Active Directory? informacje o wersji zawierają informacje na temat:

- Najnowsze wydania
- Znane problemy
- Poprawki błędów
- Funkcje uznane za przestarzałe
- Plany zmian

---

## <a name="september-2019"></a>Wrzesień 2019 r.

### <a name="plan-for-change-deprecation-of-the-power-bi-content-packs"></a>Planowanie zmian: wycofanie pakietów zawartości usługi Power BI

**Typ:** Plan zmian  
**Kategoria usługi:** Reporting  
**Możliwości produktu:** Monitorowanie & raportowania

Począwszy od 1 października 2019 r. usługa Power BI rozpocznie przestarzałe odtwarzanie wszystkich pakietów zawartości, w tym pakietu zawartości usługi Azure AD Power BI. Jako alternatywę dla tego pakietu zawartości można użyć skoroszytów usługi Azure AD, aby uzyskać wgląd w usługi związane z usługą Azure AD. Nadchodzą dodatkowe skoroszyty, w tym skoroszyty dotyczące zasad dostępu warunkowego w trybie tylko do raportów, szczegółowe informacje oparte na zgodzie aplikacji i inne.

Aby uzyskać więcej informacji na temat skoroszytów, zobacz [Jak używać skoroszytów usługi Azure Monitor dla raportów usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-use-azure-monitor-workbooks). Aby uzyskać więcej informacji na temat usuwania pakietów zawartości, zobacz wpis w blogu [o ogólnej dostępności aplikacji szablonu usługi Power BI z ogłoszeniem.](https://powerbi.microsoft.com/blog/announcing-power-bi-template-apps-general-availability/)

---

### <a name="my-profile-is-renaming-and-integrating-with-the-microsoft-office-account-page"></a>Nazwa mojego profilu zmienia nazwę i integruje się ze stroną konta pakietu Microsoft Office

**Typ:** Plan zmian  
**Kategoria usługi:** Mój profil/konto  
**Możliwości produktu:** Współpracy

Od października środowisko Mój profil stanie się moim kontem. W ramach tej zmiany, wszędzie tam, gdzie obecnie mówi, **Mój profil** zmieni się na **Moje konto**. Oprócz zmiany nazewnictwa i niektórych ulepszeń projektu zaktualizowane środowisko zaoferuje dodatkową integrację ze stroną konta pakietu Microsoft Office. W szczególności będziesz mieć dostęp do instalacji i subskrypcji pakietu Office na stronie **Konto przeglądowe,** a także do preferencji kontaktowych związanych z pakietem Office na stronie **Prywatność.**

Aby uzyskać więcej informacji na temat środowiska Mój profil (wersja zapoznawcza), zobacz [Omówienie portalu Mój profil (wersja zapoznawcza).](https://docs.microsoft.com/azure/active-directory/user-help/myprofile-portal-overview)

---

### <a name="bulk-manage-groups-and-members-using-csv-files-in-the-azure-ad-portal-public-preview"></a>Zbiorcze zarządzanie grupami i członkami przy użyciu plików CSV w portalu usługi Azure AD (public preview)

**Typ:** Nowa funkcja  
**Kategoria usługi:** Zarządzanie grupą  
**Możliwości produktu:** Współpracy

Mamy przyjemność ogłosić dostępność w wersji publicznej w wersji zapoznawczej środowiska zarządzania grupami zbiorczymi w portalu usługi Azure AD. Teraz można używać pliku CSV i portalu usługi Azure AD do zarządzania grupami i listami członków, w tym:

- Dodawanie lub usuwanie członków z grupy.

- Pobieranie listy grup z katalogu.

- Pobieranie listy członków grupy dla określonej grupy.

Aby uzyskać więcej informacji, zobacz [Zbiorcze dodawanie członków](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-import-members), [Zbiorcze usuwanie członków](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-remove-members), Lista członków pobierania [zbiorczego](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-download-members)i [Lista grup pobierania zbiorczego](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-download).

---

### <a name="dynamic-consent-is-now-supported-through-a-new-admin-consent-endpoint"></a>Zgoda dynamiczna jest teraz obsługiwana za pośrednictwem nowego punktu końcowego zgody administratora

**Typ:** Nowa funkcja  
**Kategoria usługi:** Uwierzytelnianie (loginy)  
**Możliwości produktu:** Uwierzytelnianie użytkownika

Utworzyliśmy nowy punkt końcowy zgody administratora do obsługi dynamicznej zgody, co jest przydatne dla aplikacji, które chcą używać modelu zgody dynamicznej na platformie Microsoft Identity.

Aby uzyskać więcej informacji na temat używania tego nowego punktu końcowego, zobacz [Korzystanie z punktu końcowego zgody administratora](https://docs.microsoft.com/azure/active-directory/develop/v2-admin-consent).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---september-2019"></a>Nowe aplikacje federacyjne dostępne w galerii aplikacji usługi Azure AD — wrzesień 2019 r.

**Typ:** Nowa funkcja  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwości produktu:** Integracja stron trzecich

We wrześniu 2019 r. dodaliśmy do galerii aplikacji 29 nowych aplikacji z obsługą federacji:

[ScheduleLook](https://schedulelook.bbsonlineservices.net/), [MS Azure SSO Access&trade; for Ethidex Compliance Office - Logowanie jednokrotne](https://docs.microsoft.com/azure/active-directory/saas-apps/ms-azure-sso-access-for-ethidex-compliance-office-tutorial), [iServer Portal](https://docs.microsoft.com/azure/active-directory/saas-apps/iserver-portal-tutorial), [SKYSITE](https://docs.microsoft.com/azure/active-directory/saas-apps/skysite-tutorial), [Concur Travel and Expense](https://docs.microsoft.com/azure/active-directory/saas-apps/concur-travel-and-expense-tutorial), [WorkBoard](https://docs.microsoft.com/azure/active-directory/saas-apps/workboard-tutorial), `https://apps.yeeflow.com/`, ARC [Udogodnienia](https://docs.microsoft.com/azure/active-directory/saas-apps/arc-facilities-tutorial), [Luware Stratus Team](https://stratus.emea.luware.cloud/login), Szerokie [pomysły](https://wideideas.online/wideideas/), [Prisma Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/prisma-cloud-tutorial), [JDLT Client Hub](https://clients.jdlt.co.uk/login), REN [RAKU](https://docs.microsoft.com/azure/active-directory/saas-apps/renraku-tutorial), [SealPath Secure Browser](https://protection.sealpath.com/SealPathInterceptorWopiSaas/Open/InstallSealPathEditorOneDrive), [Prisma Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/prisma-cloud-tutorial), `https://app.penneo.com/`, `https://app.testhtm.com/settings/email-integration`, [Cintoo Cloud](https://aec.cintoo.com/login), [Whitesource](https://docs.microsoft.com/azure/active-directory/saas-apps/whitesource-tutorial), [Hosted Heritage Online SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/hosted-heritage-online-sso-tutorial), [IDC](https://docs.microsoft.com/azure/active-directory/saas-apps/idc-tutorial), [CakeHR](https://docs.microsoft.com/azure/active-directory/saas-apps/cakehr-tutorial), [BIS](https://docs.microsoft.com/azure/active-directory/saas-apps/bis-tutorial), [Coo Kai Team Build](https://ms-contacts.coo-kai.jp/), [Sonarqube](https://docs.microsoft.com/azure/active-directory/saas-apps/sonarqube-tutorial), [Adobe Identity Management](https://docs.microsoft.com/azure/active-directory/saas-apps/adobe-identity-management-tutorial), Discovery Korzyści [SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/discovery-benefits-sso-tutorial), [Amelio](https://app.amelio.co/),`https://itask.yipinapp.com/`

Aby uzyskać więcej informacji na temat aplikacji, zobacz [Integracja aplikacji SaaS z usługą Azure Active Directory](https://aka.ms/appstutorial). Aby uzyskać więcej informacji na temat wyświetlania aplikacji w galerii aplikacji usługi Azure AD, zobacz [Lista aplikacji w galerii aplikacji usługi Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="new-azure-ad-global-reader-role"></a>Nowa rola czytnika globalnego usługi Azure AD

**Typ:** Nowa funkcja  
**Kategoria usługi:** Rbac  
**Możliwości produktu:** Kontrola dostępu

Począwszy od 24 września 2019 r. rozpoczniemy wdrażanie nowej roli usługi Azure Active Directory (AD) o nazwie Global Reader. To wdrożenie rozpocznie się od produkcji i globalnych klientów chmury (GCC), kończąc na całym świecie w październiku.

Rola czytnika globalnego jest tylko do odczytu odpowiednikiem administratora globalnego. Użytkownicy w tej roli mogą odczytywać ustawienia i informacje administracyjne w usługach Microsoft 365, ale nie mogą podejmować działań zarządzania. Stworzyliśmy rolę Czytnika globalnego, aby zmniejszyć liczbę administratorów globalnych w organizacji. Ponieważ konta administratora globalnego są zaawansowane i narażone na ataki, zalecamy, aby mieć mniej niż pięciu administratorów globalnych. Zalecamy używanie roli czytnika globalnego do planowania, inspekcji lub dochodzeń. Zalecamy również użycie roli czytnika globalnego w połączeniu z innymi rolami administratora ograniczonego, takimi jak Administrator programu Exchange, aby ułatwić wykonanie pracy bez konieczności pełnienia roli administratora globalnego.

Rola Czytnik globalny współpracuje z nowym Centrum administracyjnym usługi Microsoft 365, Centrum administracyjnym programu Exchange, Centrum administracyjne zespołów, Centrum zabezpieczeń, Centrum zgodności, Centrum administracyjne usługi Azure AD i Centrum administracyjne zarządzania urządzeniami.

>[!NOTE]
> Na początku publicznej wersji zapoznawczej rola czytnika globalnego nie będzie działać z: programem SharePoint, zarządzanie dostępem uprzywilejowanym, skrytka klienta, etykiety czułości, cykl życia zespołów, raportowanie zespołów & analizy połączeń, zarządzanie urządzeniami ip zespołów i katalog aplikacji Teams. 

Aby uzyskać więcej informacji, zobacz [Uprawnienia roli administratora w usłudze Azure Active Directory](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles).

---

### <a name="access-an-on-premises-report-server-from-your-power-bi-mobile-app-using-azure-active-directory-application-proxy"></a>Uzyskiwanie dostępu do lokalnego serwera raportów z aplikacji Usługi Power BI Mobile przy użyciu serwera proxy aplikacji usługi Azure Active Directory

**Typ:** Nowa funkcja  
**Kategoria usługi:** Serwer proxy aplikacji  
**Możliwości produktu:** Kontrola dostępu

Nowa integracja między aplikacją mobilną Usługi Power BI a serwerem proxy aplikacji usługi Azure AD umożliwia bezpieczne logowanie się do aplikacji mobilnej Usługi Power BI i wyświetlanie dowolnych raportów organizacji hostowanych na lokalnym serwerze raportów usługi Power BI.

Aby uzyskać informacje o aplikacji Power BI Mobile, w tym o tym, gdzie można ją pobrać, zobacz [witrynę usługi Power BI](https://powerbi.microsoft.com/mobile/). Aby uzyskać więcej informacji na temat konfigurowania aplikacji mobilnej Usługi Power BI za pomocą serwera proxy aplikacji usługi Azure AD, zobacz [Włączanie dostępu zdalnego do usługi Power BI Mobile za pomocą serwera proxy aplikacji usługi Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-integrate-with-power-bi).

---

### <a name="new-version-of-the-azureadpreview-powershell-module-is-available"></a>Dostępna jest nowa wersja modułu AzureADPreview PowerShell

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Innych  
**Możliwości produktu:** Katalogu

Nowe polecenia cmdlet zostały dodane do modułu AzureADPreview, aby ułatwić definiowanie i przypisywanie ról niestandardowych w usłudze Azure AD, w tym:

- `Add-AzureADMSFeatureRolloutPolicyDirectoryObject`
- `Get-AzureADMSFeatureRolloutPolicy`
- `New-AzureADMSFeatureRolloutPolicy`
- `Remove-AzureADMSFeatureRolloutPolicy`
- `Remove-AzureADMSFeatureRolloutPolicyDirectoryObject`
- `Set-AzureADMSFeatureRolloutPolicy`

---

### <a name="new-version-of-azure-ad-connect"></a>Nowa wersja usługi Azure AD Connect

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Innych  
**Możliwości produktu:** Katalogu

Firma Microsoft wydała zaktualizowaną wersję usługi Azure AD Connect dla klientów automatycznego uaktualniania. Ta nowa wersja zawiera kilka nowych funkcji, ulepszeń i poprawek błędów. Aby uzyskać więcej informacji na temat tej nowej wersji, zobacz [Historia wydania usługi Azure AD Connect: Version](https://docs.microsoft.com/azure/active-directory/hybrid/reference-connect-version-history#14250).

---

### <a name="azure-multi-factor-authentication-mfa-server-version-802-is-now-available"></a>Usługa Azure Multi-Factor Authentication (MFA) Server, wersja 8.0.2 jest już dostępna

**Typ:** Stałe  
**Kategoria usługi:** Mfa  
**Możliwości produktu:** Ochrona & zabezpieczeń tożsamości

Jeśli jesteś istniejącym klientem, który aktywował serwer usługi MFA przed 1 lipca 2019 r., możesz teraz pobrać najnowszą wersję serwera usługi MFA (wersja 8.0.2). W tej nowej wersji:

- Rozwiązano problem, który powodował, że gdy synchronizacja usługi Azure AD zmienia użytkownika z Wyłączone na Włączone, wiadomość e-mail jest wysyłana do użytkownika.

- Rozwiązano problem, który powodował, że klienci mogli pomyślnie uaktualnić, kontynuując korzystanie z funkcji Tagi.

- Dodano kod kraju Kosowa (+383).

- Dodano jednorazowe rejestrowanie inspekcji obejścia do pliku MultiFactorAuthSvc.log.

- Poprawiono wydajność sdk usług sieci Web.

- Naprawiono inne drobne błędy.

Od 1 lipca 2019 r. firma Microsoft przestała oferować serwer usługi MFA dla nowych wdrożeń. Nowi klienci, którzy wymagają uwierzytelniania wieloskładnikowego, powinni używać uwierzytelniania wieloskładnikowego platformy Azure w chmurze. Aby uzyskać więcej informacji, zobacz [Planowanie wdrożenia uwierzytelniania wieloskładnikowego platformy Azure opartego na chmurze.](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

---

## <a name="august-2019"></a>Sierpień 2019 r.

### <a name="enhanced-search-filtering-and-sorting-for-groups-is-available-in-the-azure-ad-portal-public-preview"></a>Rozszerzone wyszukiwanie, filtrowanie i sortowanie grup jest dostępne w portalu usługi Azure AD (public preview)

**Typ:** Nowa funkcja  
**Kategoria usługi:** Zarządzanie grupą  
**Możliwości produktu:** Współpracy

Mamy przyjemność ogłosić dostępność w wersji publicznej w wersji zapoznawczej ulepszonych środowisk związanych z grupami w portalu usługi Azure AD. Te ulepszenia ułatwiają lepsze zarządzanie grupami i listami członków, udostępniając:

- Zaawansowane funkcje wyszukiwania, takie jak wyszukiwanie podciągów na listach grup.
- Zaawansowane opcje filtrowania i sortowania na listach członków i właścicieli.
- Nowe możliwości wyszukiwania list członków i właścicieli.
- Dokładniejsze liczby grup dla dużych grup.

Aby uzyskać więcej informacji, zobacz [Zarządzanie grupami w witrynie Azure portal](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-members-azure-portal?context=azure/active-directory/users-groups-roles/context/ugr-context).

---

### <a name="new-custom-roles-are-available-for-app-registration-management-public-preview"></a>Nowe role niestandardowe są dostępne do zarządzania rejestracją aplikacji (publiczna wersja zapoznawcza)

**Typ:** Nowa funkcja  
**Kategoria usługi:** Rbac  
**Możliwości produktu:** Kontrola dostępu

Role niestandardowe (dostępne z subskrypcją usługi Azure AD P1 lub P2) mogą teraz pomóc w zapewnieniu dostępu szczegółowego, umożliwiając tworzenie definicji ról z określonymi uprawnieniami, a następnie przypisywanie tych ról do określonych zasobów. Obecnie można utworzyć role niestandardowe przy użyciu uprawnień do zarządzania rejestracjami aplikacji, a następnie przypisywanie roli do określonej aplikacji. Aby uzyskać więcej informacji na temat ról niestandardowych, zobacz [Niestandardowe role administratora w usłudze Azure Active Directory (wersja zapoznawcza)](https://docs.microsoft.com/azure/active-directory/users-groups-roles/roles-custom-overview).

Jeśli potrzebujesz dodatkowych uprawnień lub zasobów obsługiwanych, których obecnie nie widzisz, możesz wysłać opinię do naszej [witryny opinii platformy Azure,](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032) a my dodamy twoje żądanie do naszej mapy drogowej aktualizacji.

---

### <a name="new-provisioning-logs-can-help-you-monitor-and-troubleshoot-your-app-provisioning-deployment-public-preview"></a>Nowe dzienniki inicjowania obsługi administracyjnej mogą pomóc w monitorowaniu i rozwiązywaniu problemów z wdrażaniem inicjowania obsługi administracyjnej aplikacji (publiczna wersja zapoznawcza)

**Typ:** Nowa funkcja  
**Kategoria usługi:** Inicjowanie obsługi administracyjnej aplikacji  
**Możliwości produktu:** Zarządzanie cyklem życia tożsamości

Dostępne są nowe dzienniki inicjowania obsługi administracyjnej ułatwiające monitorowanie i rozwiązywanie problemów z wdrażaniem inicjowania obsługi administracyjnej użytkowników i grup. Te nowe pliki dziennika zawierają informacje na temat:

- Jakie grupy zostały pomyślnie utworzone w [serwisie ServiceNow](https://docs.microsoft.com/azure/active-directory/saas-apps/servicenow-provisioning-tutorial)
- Jakie role zostały zaimportowane z [Amazon Web Services (AWS)](https://docs.microsoft.com/azure/active-directory/saas-apps/amazon-web-service-tutorial#configure-and-test-azure-ad-single-sign-on-for-amazon-web-services-aws)
- Pracownicy, którzy nie zostali zaimportowani z [Workday](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial)

Aby uzyskać więcej informacji, zobacz [Inicjowanie obsługi administracyjnej raportów w portalu usługi Azure Active Directory (wersja zapoznawcza)](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs).

---

### <a name="new-security-reports-for-all-azure-ad-administrators-general-availability"></a>Nowe raporty zabezpieczeń dla wszystkich administratorów usługi Azure AD (ogólna dostępność)

**Typ:** Nowa funkcja  
**Kategoria usługi:** Ochrona tożsamości  
**Możliwości produktu:** Ochrona & zabezpieczeń tożsamości

Domyślnie wszyscy administratorzy usługi Azure AD wkrótce będą mogli uzyskać dostęp do nowoczesnych raportów zabezpieczeń w usłudze Azure AD. Do końca września będzie można użyć banera na górze nowoczesnych raportów bezpieczeństwa, aby powrócić do starych raportów.

Nowoczesne raporty zabezpieczeń zapewnią dodatkowe możliwości ze starszych wersji, w tym:

- Zaawansowane filtrowanie i sortowanie
- Akcje zbiorcze, takie jak odrzucanie ryzyka użytkownika
- Potwierdzenie zagrożonych lub bezpiecznych podmiotów
- Stan ryzyka, obejmujący: Zagrożony, Odrzucony, Naprawiony i Potwierdzony
- Nowe wykrywanie związane z ryzykiem (dostępne dla subskrybentów usługi Azure AD Premium)

Aby uzyskać więcej informacji, zobacz [Użytkownicy ryzykowne](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-investigate-risk#risky-users), [Ryzykowne logowania](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-investigate-risk#risky-sign-ins)i [Wykrywanie ryzyka](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-investigate-risk#risk-detections).

---

### <a name="user-assigned-managed-identity-is-available-for-virtual-machines-and-virtual-machine-scale-sets-general-availability"></a>Tożsamość zarządzana przypisana przez użytkownika jest dostępna dla maszyn wirtualnych i zestawów skalowania maszyn wirtualnych (dostępność ogólna)

**Typ:** Nowa funkcja  
**Kategoria usługi:** Tożsamości zarządzane dla zasobów platformy Azure  
**Możliwości produktu:** Środowisko deweloperskie

Tożsamości zarządzane przypisane przez użytkownika są teraz ogólnie dostępne dla maszyn wirtualnych i zestawów skalowania maszyn wirtualnych. W ramach tej platformy Azure można utworzyć tożsamość w dzierżawie usługi Azure AD, który jest zaufany przez subskrypcję w użyciu i można przypisać do jednego lub więcej wystąpień usługi platformy Azure. Aby uzyskać więcej informacji na temat tożsamości zarządzanych przypisanych przez użytkownika, zobacz [Co to są tożsamości zarządzane dla zasobów platformy Azure?](https://aka.ms/azuremanagedidentity).

---

### <a name="users-can-reset-their-passwords-using-a-mobile-app-or-hardware-token-general-availability"></a>Użytkownicy mogą resetować hasła za pomocą aplikacji mobilnej lub tokenu sprzętowego (ogólna dostępność)

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Samoobsługowe resetowanie hasła  
**Możliwości produktu:** Uwierzytelnianie użytkownika

Użytkownicy, którzy zarejestrowali aplikację mobilną w organizacji, mogą teraz zresetować własne hasło, zatwierdzając powiadomienie z aplikacji Microsoft Authenticator lub wprowadzając kod z aplikacji mobilnej lub tokenu sprzętowego.

Aby uzyskać więcej informacji, zobacz [Jak to działa: Samoobsługowe resetowanie hasła usługi Azure AD](https://aka.ms/authappsspr). Aby uzyskać więcej informacji na temat środowiska użytkownika, zobacz [Resetowanie własnego hasła służbowego lub szkolnego .](https://docs.microsoft.com/azure/active-directory/user-help/user-help-password-reset-overview)

---

### <a name="adalnet-ignores-the-msalnet-shared-cache-for-on-behalf-of-scenarios"></a>ADAL.NET ignoruje MSAL.NET udostępnionej pamięci podręcznej dla scenariuszy w imieniu

**Typ:** Stałe  
**Kategoria usługi:** Uwierzytelnianie (loginy)  
**Możliwości produktu:** Uwierzytelnianie użytkownika

Począwszy od biblioteki uwierzytelniania usługi Azure AD (ADAL.NET) w wersji 5.0.0-preview, deweloperzy aplikacji muszą [serializować jedną pamięć podręczną na konto dla aplikacji sieci Web i internetowych interfejsów API.](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Token-cache-serialization#custom-token-cache-serialization-in-web-applications--web-api) W przeciwnym razie niektóre scenariusze przy użyciu [przepływu w imieniu,](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-api-call-api-app-configuration#on-behalf-of-flow)wraz z niektórymi konkretnymi `UserAssertion`przypadkami użycia , może spowodować podniesienie uprawnień. Aby uniknąć tej luki, ADAL.NET teraz ignoruje bibliotekę uwierzytelniania firmy Microsoft dla udostępnionej pamięci podręcznej dotnet (MSAL.NET) dla scenariuszy w imieniu.

Aby uzyskać więcej informacji na temat tego problemu, zobacz [Luka w zabezpieczeniach poziomu uprawnień biblioteki uwierzytelniania usługi Azure Active Directory](https://portal.msrc.microsoft.com/security-guidance/advisory/CVE-2019-1258).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---august-2019"></a>Nowe aplikacje federacyjne dostępne w galerii aplikacji usługi Azure AD — sierpień 2019 r.

**Typ:** Nowa funkcja  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwości produktu:** Integracja stron trzecich

W sierpniu 2019 r. dodaliśmy do galerii aplikacji 26 nowych aplikacji z obsługą federacji:

[Platforma Obywatelska](https://docs.microsoft.com/azure/active-directory/saas-apps/civic-platform-tutorial), [Amazon Business](https://docs.microsoft.com/azure/active-directory/saas-apps/amazon-business-tutorial), [ProNovos Ops Manager](https://docs.microsoft.com/azure/active-directory/saas-apps/pronovos-ops-manager-tutorial), [Cognidox](https://docs.microsoft.com/azure/active-directory/saas-apps/cognidox-tutorial), [Viareport's Inativ Portal (Europa)](https://docs.microsoft.com/azure/active-directory/saas-apps/viareports-inativ-portal-europe-tutorial), [Azure Databricks](https://azure.microsoft.com/services/databricks), [Robin](https://docs.microsoft.com/azure/active-directory/saas-apps/robin-tutorial), [Academy Attendance](https://docs.microsoft.com/azure/active-directory/saas-apps/academy-attendance-tutorial), [Matryca priorytetowa](https://sync.appfluence.com/pmwebng/), [Cousto MySpace](https://cousto.platformers.be/account/login), [Uploadcare](https://uploadcare.com/accounts/signup/), [Carbonite Endpoint Backup](https://docs.microsoft.com/azure/active-directory/saas-apps/carbonite-endpoint-backup-tutorial), [CPQSync przez Cincom](https://docs.microsoft.com/azure/active-directory/saas-apps/cpqsync-by-cincom-tutorial), [Chargebee](https://docs.microsoft.com/azure/active-directory/saas-apps/chargebee-tutorial), [deliver.media&trade; Portal](https://portal.deliver.media), [Frontline Education](https://docs.microsoft.com/azure/active-directory/saas-apps/frontline-education-tutorial), [F5](https://www.f5.com/products/security/access-policy-manager), [stashcat AD connect](https://www.stashcat.com), [Blink](https://docs.microsoft.com/azure/active-directory/saas-apps/blink-tutorial), [Vocoli](https://docs.microsoft.com/azure/active-directory/saas-apps/vocoli-tutorial), [ProNovos Analytics](https://docs.microsoft.com/azure/active-directory/saas-apps/pronovos-analytics-tutorial), [Sigstr](https://docs.microsoft.com/azure/active-directory/saas-apps/sigstr-tutorial), [Darwinbox](https://docs.microsoft.com/azure/active-directory/saas-apps/darwinbox-tutorial), [Watch by Colors](https://docs.microsoft.com/azure/active-directory/saas-apps/watch-by-colors-tutorial), [Uprząż](https://docs.microsoft.com/azure/active-directory/saas-apps/harness-tutorial), [EAB Nawigacja Strategic Care](https://docs.microsoft.com/azure/active-directory/saas-apps/eab-navigate-strategic-care-tutorial)

Aby uzyskać więcej informacji na temat aplikacji, zobacz [Integracja aplikacji SaaS z usługą Azure Active Directory](https://aka.ms/appstutorial). Aby uzyskać więcej informacji na temat wyświetlania aplikacji w galerii aplikacji usługi Azure AD, zobacz [Lista aplikacji w galerii aplikacji usługi Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="new-versions-of-the-azuread-powershell-and-azureadpreview-powershell-modules-are-available"></a>Dostępne są nowe wersje modułów azuread powershell i azureadpreview powershell

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Innych  
**Możliwości produktu:** Katalogu

Dostępne są nowe aktualizacje modułów azuread i azuread preview programu PowerShell:

- Nowy `-Filter` parametr został dodany `Get-AzureADDirectoryRole` do parametru w module AzureAD. Ten parametr ułatwia filtrowanie ról katalogu zwracanych przez polecenie cmdlet.
- Nowe polecenia cmdlet zostały dodane do modułu AzureADPreview, aby ułatwić definiowanie i przypisywanie ról niestandardowych w usłudze Azure AD, w tym:

    - `Get-AzureADMSRoleAssignment`
    - `Get-AzureADMSRoleDefinition`
    - `New-AzureADMSRoleAssignment`
    - `New-AzureADMSRoleDefinition`
    - `Remove-AzureADMSRoleAssignment`
    - `Remove-AzureADMSRoleDefinition`
    - `Set-AzureADMSRoleDefinition`

---

### <a name="improvements-to-the-ui-of-the-dynamic-group-rule-builder-in-the-azure-portal"></a>Ulepszenia interfejsu użytkownika konstruktora reguł grupy dynamicznej w witrynie Azure portal

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Zarządzanie grupą  
**Możliwości produktu:** Współpracy

Wprowadzono pewne ulepszenia interfejsu użytkownika do konstruktora reguł grupy dynamicznej, dostępne w witrynie Azure portal, aby ułatwić konfigurowanie nowej reguły lub zmiany istniejących reguł. To ulepszenie projektu umożliwia tworzenie reguł z maksymalnie pięcioma wyrażeniami, a nie tylko jednym. Zaktualizowaliśmy również listę właściwości urządzenia, aby usunąć przestarzałe właściwości urządzenia.

Aby uzyskać więcej informacji, zobacz [Zarządzanie dynamicznymi regułami członkostwa](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership).

---

### <a name="new-microsoft-graph-app-permission-available-for-use-with-access-reviews"></a>Nowe uprawnienie aplikacji Microsoft Graph dostępne do użycia z recenzjami dostępu

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Recenzje programu Access  
**Możliwości produktu:** Zarządzanie tożsamościami

Wprowadziliśmy nowe uprawnienie aplikacji Microsoft `AccessReview.ReadWrite.Membership`Graph, które umożliwia aplikacjom automatyczne tworzenie i pobieranie opinii o dostępie do członkostwa w grupach i przydziałów aplikacji. To uprawnienie może być używane przez zaplanowane zadania lub jako część automatyzacji, bez konieczności zalogowanego kontekstu użytkownika.

Aby uzyskać więcej informacji, zobacz [przykładowy sposób tworzenia przeglądów dostępu usługi Azure AD przy użyciu uprawnień aplikacji programu Microsoft Graph w blogu programu PowerShell](https://techcommunity.microsoft.com/t5/Azure-Active-Directory/Example-how-to-create-Azure-AD-access-reviews-using-Microsoft/m-p/807241).

---

### <a name="azure-ad-activity-logs-are-now-available-for-government-cloud-instances-in-azure-monitor"></a>Dzienniki aktywności usługi Azure AD są teraz dostępne dla wystąpień chmury rządowej w usłudze Azure Monitor

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Reporting  
**Możliwości produktu:** Monitorowanie & raportowania

Z przyjemnością informujemy, że dzienniki aktywności usługi Azure AD są teraz dostępne dla wystąpień chmury rządowej w usłudze Azure Monitor. Teraz można wysyłać dzienniki usługi Azure AD do konta magazynu lub do centrum zdarzeń w celu zintegrowania z narzędziami SIEM, takimi jak [Sumologic](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-sumologic), [Splunk](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-splunk)i [ArcSight](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-arcsight). 

Aby uzyskać więcej informacji na temat konfigurowania usługi Azure Monitor, zobacz [Dzienniki aktywności usługi Azure AD w usłudze Azure Monitor](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-activity-logs-azure-monitor#cost-considerations).

---

### <a name="update-your-users-to-the-new-enhanced-security-info-experience"></a>Aktualizowanie użytkowników do nowych, ulepszonych informacji zabezpieczających

**Typ:** Zmieniono funkcję  
**Kategoria usługi:**  Uwierzytelnianie (loginy)   
**Możliwości produktu:** Uwierzytelnianie użytkownika

25 września 2019 r. wyłączymy stare, nieudoskonalone informacje o zabezpieczeniach do rejestrowania informacji zabezpieczających użytkownika i zarządzania nimi oraz włączamy tylko nową, [ulepszoną wersję](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Cool-enhancements-to-the-Azure-AD-combined-MFA-and-password/ba-p/354271). Oznacza to, że użytkownicy nie będą już mogli korzystać ze starego środowiska.

Aby uzyskać więcej informacji na temat rozszerzonych informacji o zabezpieczeniach, zapoznaj się z [dokumentacją administratora](https://aka.ms/securityinfodocs) i [dokumentacją użytkownika.](https://aka.ms/securityinfoguide)

#### <a name="to-turn-on-this-new-experience-you-must"></a>Aby włączyć to nowe środowisko, musisz:

1. Zaloguj się do witryny Azure portal jako administrator globalny lub administrator użytkownika.

2. Przejdź do **witryny Azure Active Directory > ustawienia użytkownika > Zarządzanie ustawieniami funkcji podglądu panelu dostępu**.

3. W **obszarze Użytkownicy mogą używać funkcji podglądu do rejestrowania informacji zabezpieczających i zarządzania nimi — rozszerzony** obszar, **wybieranie wybranych**, a następnie wybieranie grupy użytkowników lub **wybranie** opcji Wszystkie, aby włączyć tę funkcję dla wszystkich użytkowników w dzierżawie.

4. W obszarze **Użytkownicy mogą używać funkcji podglądu do rejestrowania zabezpieczeń i zarządzania nimi **info**** wybierz **opcję Brak**.

5. Zapisz ustawienia.

    Po zapisaniu ustawień nie będziesz już mieć dostępu do starych informacji zabezpieczających.

>[!Important]
>Jeśli te kroki nie zostaną ukończone przed 25 września 2019 r., dzierżawa usługi Azure Active Directory zostanie automatycznie włączona dla rozszerzonego środowiska. Jeśli masz pytania, skontaktuj registrationpreview@microsoft.comsię z nami pod adresem .

---

### <a name="authentication-requests-using-post-logins-will-be-more-strictly-validated"></a>Żądania uwierzytelniania przy użyciu logowania POST będą bardziej rygorystycznie sprawdzane

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Uwierzytelnianie (loginy)  
**Możliwości produktu:** Standardów

Począwszy od 2 września 2019 r., żądania uwierzytelniania przy użyciu metody POST będą bardziej rygorystycznie sprawdzane w stosunku do standardów HTTP. W szczególności spacje i cudzysłowy (") nie będą już usuwane z wartości formularza żądania. Te zmiany nie powinny przerywać istniejących klientów i pomoże upewnić się, że żądania wysyłane do usługi Azure AD są niezawodnie obsługiwane za każdym razem.

Aby uzyskać więcej informacji, zobacz [powiadomienia o zmianach dotyczących łamania usługi Azure AD.](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#post-form-semantics-will-be-enforced-more-strictly---spaces-and-quotes-will-be-ignored)

---

## <a name="july-2019"></a>Lipiec 2019 r.

### <a name="plan-for-change-application-proxy-service-update-to-support-only-tls-12"></a>Planowanie zmian: aktualizacja usługi proxy aplikacji do obsługi tylko protokołu TLS 1.2

**Typ:** Plan zmian  
**Kategoria usługi:** Serwer proxy aplikacji  
**Możliwości produktu:** Kontrola dostępu

Aby zapewnić Ci nasze najsilniejsze szyfrowanie, zaczniemy ograniczać dostęp usługi Proxy aplikacji tylko do protokołów TLS 1.2. To ograniczenie zostanie początkowo wprowadzone do klientów, którzy już używają protokołów TLS 1.2, więc nie zobaczysz wpływu. Całkowite wycofanie protokołów TLS 1.0 i TLS 1.1 zostanie zakończone 31 sierpnia 2019 r. Klienci nadal korzystający z protokołów TLS 1.0 i TLS 1.1 otrzymają zaawansowane powiadomienie, aby przygotować się do tej zmiany.

Aby zachować połączenie z usługą proxy aplikacji przez całą tę zmianę, zaleca się, aby upewnić się, że kombinacje klient-serwer i serwer przeglądarki są aktualizowane w celu używania protokołu TLS 1.2. Zaleca się również, aby uwzględnić wszystkie systemy klienckie używane przez pracowników do uzyskiwania dostępu do aplikacji opublikowanych za pośrednictwem usługi Proxy aplikacji.

Aby uzyskać więcej informacji, zobacz [Dodawanie aplikacji lokalnej do zdalnego dostępu za pośrednictwem serwera proxy aplikacji w usłudze Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application).

---

### <a name="plan-for-change-design-updates-are-coming-for-the-application-gallery"></a>Planowanie zmian: nadchodzą aktualizacje projektu dla Galerii aplikacji

**Typ:** Plan zmian  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwości produktu:** Usługi rejestracji jednokrotnej

Nowe zmiany interfejsu użytkownika nadchodzą do projektu Add z obszaru **galerii** **Dodaj bloku aplikacji.** Te zmiany ułatwią znajdowanie aplikacji obsługujących automatyczne inicjowanie obsługi administracyjnej, openid connect, język znaczników oświadczeń zabezpieczeń (SAML) i logowanie jednokrotne hasła.These changes will help you more easily find your apps that support automatic provisioning, OpenID Connect, Security Assertion Markup Language (SAML) and Password single sign-on (SSO).

---

### <a name="plan-for-change-removal-of-the-mfa-server-ip-address-from-the-office-365-ip-address"></a>Planowanie zmian: usuwanie adresu IP serwera usługi MFA z adresu IP usługi Office 365

**Typ:** Plan zmian  
**Kategoria usługi:** Mfa  
**Możliwości produktu:** Ochrona & zabezpieczeń tożsamości

Usuwamy adres IP serwera usługi MFA z [usługi Adres IP usługi Office 365 i adres URL usługi sieci Web](https://docs.microsoft.com/office365/enterprise/office-365-ip-web-service). Jeśli obecnie polegasz na tych stronach, aby zaktualizować ustawienia zapory, należy się upewnić, że zawiera również listę adresów IP udokumentowanych w sekcji **Wymagania zapory serwera uwierzytelniania wieloskładnikowego platformy Azure** w [artykule Wprowadzenie do serwera uwierzytelniania wieloskładnikowego azure.](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfaserver-deploy#azure-multi-factor-authentication-server-firewall-requirements)

---

### <a name="app-only-tokens-now-require-the-client-app-to-exist-in-the-resource-tenant"></a>Tokeny tylko do aplikacji wymagają teraz istnienia aplikacji klienckiej w dzierżawie zasobów

**Typ:** Stałe  
**Kategoria usługi:** Uwierzytelnianie (loginy)  
**Możliwości produktu:** Uwierzytelnianie użytkownika

W dniu 26 lipca 2019 r. zmieniliśmy sposób dostarczania tokenów tylko do aplikacji za pośrednictwem [dotacji poświadczeń klienta.](https://docs.microsoft.com/azure/active-directory/develop/v1-oauth2-client-creds-grant-flow) Wcześniej aplikacje mogą uzyskać tokeny do wywoływania innych aplikacji, niezależnie od tego, czy aplikacja kliencka znajdowała się w dzierżawie. Zaktualizowaliśmy to zachowanie, aby zasoby z jedną dzierżawą, czasami nazywane interfejsami API sieci Web, mogły być wywoływane tylko przez aplikacje klienckie istniejące w dzierżawie zasobów.

Jeśli aplikacja nie znajduje się w dzierżawie zasobów, zostanie wyświetlony `The service principal named <app_name> was not found in the tenant named <tenant_name>. This can happen if the application has not been installed by the administrator of the tenant.` komunikat o błędzie, który mówi, Aby rozwiązać ten problem, należy utworzyć jednostkę usługi aplikacji klienta w dzierżawie, przy użyciu [punktu końcowego zgody administratora](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent#using-the-admin-consent-endpoint) lub [za pośrednictwem programu PowerShell](https://docs.microsoft.com/azure/active-directory/develop/howto-authenticate-service-principal-powershell), który zapewnia dzierżawy dał uprawnienia aplikacji do działania w dzierżawie.

Aby uzyskać więcej informacji, zobacz [Co nowego w uwierzytelnianiu?](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#app-only-tokens-for-single-tenant-applications-are-only-issued-if-the-client-app-exists-in-the-resource-tenant).

> [!NOTE]
> Istniejąca zgoda między klientem a interfejsem API nadal nie jest wymagana. Aplikacje powinny nadal wykonywać własne kontrole autoryzacji.

---

### <a name="new-passwordless-sign-in-to-azure-ad-using-fido2-security-keys"></a>Nowe logowanie bez hasła do usługi Azure AD przy użyciu kluczy zabezpieczeń FIDO2

**Typ:** Nowa funkcja  
**Kategoria usługi:** Uwierzytelnianie (loginy)  
**Możliwości produktu:** Uwierzytelnianie użytkownika

Klienci usługi Azure AD mogą teraz ustawiać zasady do zarządzania kluczami zabezpieczeń FIDO2 dla użytkowników i grup organizacji. Użytkownicy końcowi mogą również samodzielnie rejestrować swoje klucze zabezpieczeń, używać kluczy do logowania się do swoich kont Microsoft w witrynach sieci Web na urządzeniach obsługujących fido, a także logować się do swoich urządzeń z systemem Windows 10 przyłączonych do usługi Azure AD.

Aby uzyskać więcej informacji, zobacz [Włączanie logowania bez hasła dla usługi Azure AD (wersja zapoznawcza)](/azure/active-directory/authentication/concept-authentication-passwordless) dla informacji związanych z administratorem i [Konfigurowanie informacji zabezpieczających w celu używania klucza zabezpieczeń (Wersja zapoznawcza)](https://docs.microsoft.com/azure/active-directory/user-help/security-info-setup-security-key) dla informacji związanych z użytkownikiem końcowym.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---july-2019"></a>Nowe aplikacje federacyjne dostępne w galerii aplikacji usługi Azure AD — lipiec 2019 r.

**Typ:** Nowa funkcja  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwości produktu:** Integracja stron trzecich

W lipcu 2019 r. dodaliśmy do galerii aplikacji 18 nowych aplikacji z obsługą federacji:

[Ungerboeck Software](https://docs.microsoft.com/azure/active-directory/saas-apps/ungerboeck-software-tutorial), [Bright Pattern Omnichannel Contact Center](https://docs.microsoft.com/azure/active-directory/saas-apps/bright-pattern-omnichannel-contact-center-tutorial), Clever [Nelly](https://docs.microsoft.com/azure/active-directory/saas-apps/clever-nelly-tutorial), [AcquireIO](https://docs.microsoft.com/azure/active-directory/saas-apps/acquireio-tutorial), [Looop](https://www.looop.co/schedule-a-demo/), [productboard](https://docs.microsoft.com/azure/active-directory/saas-apps/productboard-tutorial), [MS Azure SSO Access for Ethidex Compliance Office&trade;](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on#password-based-sso), [Hype](https://docs.microsoft.com/azure/active-directory/saas-apps/hype-tutorial), [Abstract](https://docs.microsoft.com/azure/active-directory/saas-apps/abstract-tutorial), [Ascentis](https://docs.microsoft.com/azure/active-directory/saas-apps/ascentis-tutorial), [Flipsnack](https://www.flipsnack.com/accounts/sign-in-sso.html), [Wandera](https://docs.microsoft.com/azure/active-directory/saas-apps/wandera-tutorial), [TwineSocial](https://twinesocial.com/), [Kallidus](https://docs.microsoft.com/azure/active-directory/saas-apps/kallidus-tutorial), [HyperAnna](https://docs.microsoft.com/azure/active-directory/saas-apps/hyperanna-tutorial), [PharmID WasteWitness](https://pharmid.com/), [i2B Connect](https://www.i2b-online.com/sign-up-to-use-i2b-connect-here-sso-access/), [JFrog Artifactory](https://docs.microsoft.com/azure/active-directory/saas-apps/jfrog-artifactory-tutorial)

Aby uzyskać więcej informacji na temat aplikacji, zobacz [Integracja aplikacji SaaS z usługą Azure Active Directory](https://aka.ms/appstutorial). Aby uzyskać więcej informacji na temat wyświetlania aplikacji w galerii aplikacji usługi Azure AD, zobacz [Lista aplikacji w galerii aplikacji usługi Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>Automatyzacja inicjowania obsługi administracyjnej konta użytkownika dla tych nowo obsługiwanych aplikacji SaaS

**Typ:** Nowa funkcja  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwości produktu:** Monitorowanie & raportowania

Teraz można zautomatyzować tworzenie, aktualizowanie i usuwanie kont użytkowników dla tych nowo zintegrowanych aplikacji:

- [Dialpad](https://docs.microsoft.com/azure/active-directory/saas-apps/dialpad-provisioning-tutorial)

- [Federated Directory](https://docs.microsoft.com/azure/active-directory/saas-apps/federated-directory-provisioning-tutorial)

- [Figma](https://docs.microsoft.com/azure/active-directory/saas-apps/figma-provisioning-tutorial)

- [Leapsome](https://docs.microsoft.com/azure/active-directory/saas-apps/leapsome-provisioning-tutorial)

- [Peakon](https://docs.microsoft.com/azure/active-directory/saas-apps/peakon-provisioning-tutorial)

- [Smartsheet](https://docs.microsoft.com/azure/active-directory/saas-apps/smartsheet-provisioning-tutorial)

Aby uzyskać więcej informacji na temat lepszego zabezpieczania organizacji przy użyciu automatycznego inicjowania obsługi administracyjnej konta użytkownika, zobacz [Automatyzacja inicjowania obsługi administracyjnej aplikacji SaaS przy użyciu usługi Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)

---

### <a name="new-azure-ad-domain-services-service-tag-for-network-security-group"></a>Nowy tag usługi usług domenowych usługi Azure AD dla sieciowej grupy zabezpieczeń

**Typ:** Nowa funkcja  
**Kategoria usługi:** Usługi domenowe usługi Azure AD  
**Możliwości produktu:** Usługi domenowe usługi Azure AD

Jeśli masz dość zarządzania długimi listami adresów IP i zakresów, możesz użyć nowego tagu usługi **sieciowej AzureActiveDirectoryDomainServices** w sieciowej grupie zabezpieczeń platformy Azure, aby ułatwić zabezpieczanie ruchu przychodzącego do sieci wirtualnej usług ad-usług azure.

Aby uzyskać więcej informacji na temat tego nowego tagu usługi, zobacz [Sieciowe grupy zabezpieczeń usług domenowych usługi Azure AD](../../active-directory-domain-services/network-considerations.md#network-security-groups-and-required-ports).

---

### <a name="new-security-audits-for-azure-ad-domain-services-public-preview"></a>Nowe inspekcje zabezpieczeń dla usług domenowych usługi Azure AD (publiczna wersja zapoznawcza)

**Typ:** Nowa funkcja  
**Kategoria usługi:** Usługi domenowe usługi Azure AD  
**Możliwości produktu:** Usługi domenowe usługi Azure AD

Mamy przyjemność ogłosić wydanie inspekcji zabezpieczeń usługi Azure AD Domain Service w publicznej wersji zapoznawczej. Inspekcja zabezpieczeń pomaga zapewnić krytyczny wgląd w usługi uwierzytelniania przez przesyłanie strumieniowe zdarzeń inspekcji zabezpieczeń do zasobów docelowych, w tym usługi Azure Storage, obszarów roboczych usługi Azure Log Analytics i usługi Azure Event Hub, przy użyciu portalu usługi domen usługi Azure AD.

Aby uzyskać więcej informacji, zobacz [Włączanie inspekcji zabezpieczeń dla usług domenowych usługi Azure AD (Preview)](https://docs.microsoft.com/azure/active-directory-domain-services/security-audit-events).

---

### <a name="new-authentication-methods-usage--insights-public-preview"></a>Użycie nowych metod uwierzytelniania & szczegółowych informacji (publiczna wersja zapoznawcza)

**Typ:** Nowa funkcja  
**Kategoria usługi:** Samoobsługowe resetowanie hasła  
**Możliwości produktu:** Monitorowanie & raportowania

Nowe metody uwierzytelniania & raportów szczegółowych mogą pomóc w zrozumieniu, w jaki sposób funkcje, takie jak uwierzytelnianie wieloskładnikowe platformy Azure i samoobsługowe resetowanie hasła są rejestrowane i używane w organizacji, w tym liczba zarejestrowanych użytkowników dla każdej funkcji, jak często samoobsługowy resetowanie hasła jest używany do resetowania haseł i za pomocą której odbywa się resetowanie.

Aby uzyskać więcej informacji, zobacz [Użycie metod uwierzytelniania & szczegółowych informacji (wersja zapoznawcza)](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-methods-usage-insights).

---

### <a name="new-security-reports-are-available-for-all-azure-ad-administrators-public-preview"></a>Nowe raporty zabezpieczeń są dostępne dla wszystkich administratorów usługi Azure AD (public preview)

**Typ:** Nowa funkcja  
**Kategoria usługi:** Ochrona tożsamości  
**Możliwości produktu:** Ochrona & zabezpieczeń tożsamości

Wszyscy administratorzy usługi Azure AD mogą teraz wybrać baner u góry istniejących raportów zabezpieczeń, takich jak **Użytkownicy oflagowani raportem o ryzyku,** aby rozpocząć korzystanie z nowego środowiska zabezpieczeń, jak pokazano w raportach **Ryzykownych użytkowników** i **ryzykownych logowaniach.** Z biegiem czasu wszystkie raporty zabezpieczeń zostaną przeniesione ze starszych wersji do nowych wersji, a nowe raporty zapewniają następujące dodatkowe możliwości:

- Zaawansowane filtrowanie i sortowanie

- Akcje zbiorcze, takie jak odrzucanie ryzyka użytkownika

- Potwierdzenie zagrożonych lub bezpiecznych podmiotów

- Stan ryzyka, obejmujący: Zagrożony, Odrzucony, Naprawiony i Potwierdzony

Aby uzyskać więcej informacji, zobacz [Raport ryzykownych użytkowników](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-investigate-risk#risky-users) i [raport ryzykowne logowania](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-investigate-risk#risky-sign-ins).

---

### <a name="new-security-audits-for-azure-ad-domain-services-public-preview"></a>Nowe inspekcje zabezpieczeń dla usług domenowych usługi Azure AD (publiczna wersja zapoznawcza)

**Typ:** Nowa funkcja  
**Kategoria usługi:** Usługi domenowe usługi Azure AD  
**Możliwości produktu:** Usługi domenowe usługi Azure AD

Mamy przyjemność ogłosić wydanie inspekcji zabezpieczeń usługi Azure AD Domain Service w publicznej wersji zapoznawczej. Inspekcja zabezpieczeń pomaga zapewnić krytyczny wgląd w usługi uwierzytelniania przez przesyłanie strumieniowe zdarzeń inspekcji zabezpieczeń do zasobów docelowych, w tym usługi Azure Storage, obszarów roboczych usługi Azure Log Analytics i usługi Azure Event Hub, przy użyciu portalu usługi domen usługi Azure AD.

Aby uzyskać więcej informacji, zobacz [Włączanie inspekcji zabezpieczeń dla usług domenowych usługi Azure AD (Preview)](https://docs.microsoft.com/azure/active-directory-domain-services/security-audit-events).

---

### <a name="new-b2b-direct-federation-using-samlws-fed-public-preview"></a>Nowa bezpośrednia federacja B2B przy użyciu SAML/WS-Fed (publiczna wersja zapoznawcza)

**Typ:** Nowa funkcja  
**Kategoria usługi:** B2b  
**Możliwości produktu:** B2B/B2C

Bezpośrednia federacja ułatwia pracę z partnerami, których rozwiązanie tożsamości zarządzane przez IT nie jest usługą Azure AD, dzięki pracy z systemami tożsamości obsługującymi standardy SAML lub WS-Fed. Po skonfigurowaniu bezpośredniej relacji federacyjnej z partnerem każdy nowy użytkownik-gość zaproszony z tej domeny może współpracować z Tobą przy użyciu istniejącego konta organizacyjnego, dzięki czemu korzystanie z usług gości jest bardziej płynne.

Aby uzyskać więcej informacji, zobacz [Federacja bezpośrednia z usługą AD FS i zewnętrznymi dostawcami dla użytkowników-gości (wersja zapoznawcza)](https://docs.microsoft.com/azure/active-directory/b2b/direct-federation).

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>Automatyzacja inicjowania obsługi administracyjnej konta użytkownika dla tych nowo obsługiwanych aplikacji SaaS

**Typ:** Nowa funkcja  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwości produktu:** Monitorowanie & raportowania

Teraz można zautomatyzować tworzenie, aktualizowanie i usuwanie kont użytkowników dla tych nowo zintegrowanych aplikacji:

- [Dialpad](https://docs.microsoft.com/azure/active-directory/saas-apps/dialpad-provisioning-tutorial)

- [Federated Directory](https://docs.microsoft.com/azure/active-directory/saas-apps/federated-directory-provisioning-tutorial)

- [Figma](https://docs.microsoft.com/azure/active-directory/saas-apps/figma-provisioning-tutorial)

- [Leapsome](https://docs.microsoft.com/azure/active-directory/saas-apps/leapsome-provisioning-tutorial)

- [Peakon](https://docs.microsoft.com/azure/active-directory/saas-apps/peakon-provisioning-tutorial)

- [Smartsheet](https://docs.microsoft.com/azure/active-directory/saas-apps/smartsheet-provisioning-tutorial)

Aby uzyskać więcej informacji na temat lepszego zabezpieczania organizacji przy użyciu automatycznego inicjowania obsługi administracyjnej konta użytkownika, zobacz [Automatyzacja inicjowania obsługi administracyjnej aplikacji SaaS za pomocą usługi Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).

---

### <a name="new-check-for-duplicate-group-names-in-the-azure-ad-portal"></a>Nowe sprawdzanie zduplikowanych nazw grup w portalu usługi Azure AD

**Typ:** Nowa funkcja  
**Kategoria usługi:** Zarządzanie grupą  
**Możliwości produktu:** Współpracy

Teraz podczas tworzenia lub aktualizowania nazwy grupy z portalu usługi Azure AD, firma Microsoft przeprowadzi sprawdzenie, czy duplikujesz istniejącą nazwę grupy w zasobie. Jeśli stwierdzimy, że nazwa jest już używana przez inną grupę, zostaniesz poproszony o zmodyfikowanie swojego imienia i nazwiska.

Aby uzyskać więcej informacji, zobacz [Zarządzanie grupami w portalu usługi Azure AD](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal?context=azure/active-directory/users-groups-roles/context/ugr-context).

---

### <a name="azure-ad-now-supports-static-query-parameters-in-reply-redirect-uris"></a>Usługa Azure AD obsługuje teraz statyczne parametry zapytań w identyfikatorach URI odpowiedzi (przekierowanie)

**Typ:** Nowa funkcja  
**Kategoria usługi:** Uwierzytelnianie (loginy)  
**Możliwości produktu:** Uwierzytelnianie użytkownika

Aplikacje usługi Azure AD można teraz zarejestrować i używać identyfikatorów URI odpowiedzi (przekierowanie) z parametrami zapytania statycznego (na `https://contoso.com/oauth2?idp=microsoft`przykład) dla żądań OAuth 2.0. Parametr zapytania statycznego podlega dopasowywaniu ciągów dla identyfikatorów URI odpowiedzi, podobnie jak każda inna część identyfikatora URI odpowiedzi. Jeśli nie ma zarejestrowanego ciągu, który pasuje do adresu URL dekodowane redirect-uri, żądanie zostanie odrzucone. Jeśli identyfikator URI odpowiedzi zostanie znaleziony, cały ciąg jest używany do przekierowania użytkownika, łącznie z parametrem zapytania statycznego.

Identyfikatory identyfikatorów URI odpowiedzi dynamicznej są nadal zabronione, ponieważ stanowią zagrożenie bezpieczeństwa i nie mogą być używane do przechowywania informacji o stanie w żądaniu uwierzytelniania. W tym celu `state` należy użyć parametru.

Obecnie ekrany rejestracji aplikacji w witrynie Azure portal nadal blokują parametry kwerendy. Można jednak ręcznie edytować manifest aplikacji, aby dodać i przetestować parametry zapytania w aplikacji. Aby uzyskać więcej informacji, zobacz [Co nowego w uwierzytelnianiu?](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#redirect-uris-can-now-contain-query-string-parameters).

---

### <a name="activity-logs-ms-graph-apis-for-azure-ad-are-now-available-through-powershell-cmdlets"></a>Dzienniki aktywności (interfejsy API programu MS Graph) dla usługi Azure AD są teraz dostępne za pośrednictwem poleceń cmdlet programu PowerShell

**Typ:** Nowa funkcja  
**Kategoria usługi:** Reporting  
**Możliwości produktu:** Monitorowanie & raportowania

Z przyjemnością informujemy, że dzienniki aktywności usługi Azure AD (raporty inspekcji i logowania) są teraz dostępne za pośrednictwem modułu Azure AD PowerShell. Wcześniej można było tworzyć własne skrypty przy użyciu punktów końcowych interfejsu API programu MS Graph, a teraz rozszerzyliśmy tę możliwość na polecenia cmdlet programu PowerShell.

Aby uzyskać więcej informacji na temat używania tych poleceń cmdlet, zobacz [polecenia cmdlet programu Azure AD PowerShell do raportowania](https://docs.microsoft.com/azure/active-directory/reports-monitoring/reference-powershell-reporting).

---

### <a name="updated-filter-controls-for-audit-and-sign-in-logs-in-azure-ad"></a>Zaktualizowane formanty filtrów dla dzienników inspekcji i logowania w usłudze Azure AD

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Reporting  
**Możliwości produktu:** Monitorowanie & raportowania

Zaktualizowaliśmy raporty dziennika inspekcji i logowania, dzięki czemu można teraz stosować różne filtry bez konieczności dodawania ich jako kolumn na ekranach raportu. Ponadto możesz teraz zdecydować, ile filtrów chcesz wyświetlić na ekranie. Wszystkie te aktualizacje współpracują ze sobą, aby raporty były łatwiejsze do odczytania i bardziej ograniczone do twoich potrzeb.

Aby uzyskać więcej informacji na temat tych aktualizacji, zobacz [Filtrowanie dzienników inspekcji](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-audit-logs#filtering-audit-logs) i [działania logowania filtru](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-sign-ins#filter-sign-in-activities).

---

## <a name="june-2019"></a>Czerwiec 2019 r.

### <a name="new-riskdetections-api-for-microsoft-graph-public-preview"></a>Nowy interfejs API ds. ryzykadetections dla programu Microsoft Graph (publiczna wersja zapoznawcza)

**Typ:** Nowa funkcja  
**Kategoria usługi:** Ochrona tożsamości  
**Możliwości produktu:** Ochrona & zabezpieczeń tożsamości

Mamy przyjemność ogłosić nowe ryzykoDetections API dla programu Microsoft Graph jest teraz w publicznej wersji zapoznawczej. Za pomocą tego nowego interfejsu API można wyświetlić listę użytkowników związanych z ochroną tożsamości organizacji i wykrywanie ryzyka logowania. Ten interfejs API służy również do bardziej efektywnego wykonywania zapytań o wykrywanie ryzyka, w tym szczegółowe informacje o typie wykrywania, stanie, poziomie i innych.

Aby uzyskać więcej informacji, zobacz [dokumentację referencyjną interfejsu API wykrywania ryzyka](https://docs.microsoft.com/graph/api/resources/riskdetection?view=graph-rest-beta).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---june-2019"></a>Nowe aplikacje federacyjne dostępne w galerii aplikacji usługi Azure AD — czerwiec 2019 r.

**Typ:** Nowa funkcja  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwości produktu:** Integracja stron trzecich

W czerwcu 2019 r. dodaliśmy do galerii aplikacji 22 nowe aplikacje z obsługą federacji:

[Azure AD SAML Toolkit](https://docs.microsoft.com/azure/active-directory/saas-apps/saml-toolkit-tutorial), [Otsuka Shokai (jap.)](https://docs.microsoft.com/azure/active-directory/saas-apps/otsuka-shokai-tutorial), [ANAQUA](https://docs.microsoft.com/azure/active-directory/saas-apps/anaqua-tutorial), [Azure VPN Client](https://portal.azure.com/), [ExpenseIn](https://docs.microsoft.com/azure/active-directory/saas-apps/expensein-tutorial), [Helper Helper](https://docs.microsoft.com/azure/active-directory/saas-apps/helper-helper-tutorial), [Costpoint](https://docs.microsoft.com/azure/active-directory/saas-apps/costpoint-tutorial), [GlobalOne](https://docs.microsoft.com/azure/active-directory/saas-apps/globalone-tutorial), [Mercedes-Benz In-Car Office](https://me.secure.mercedes-benz.com/), [Skore](https://app.justskore.it/), [Oracle Cloud Infrastructure Console](https://docs.microsoft.com/azure/active-directory/saas-apps/oracle-cloud-tutorial), [CyberArk SAML Authentication](https://docs.microsoft.com/azure/active-directory/saas-apps/cyberark-saml-authentication-tutorial), [Scrible Edu](https://www.scrible.com/sign-in/#/create-account), [PandaDoc](https://docs.microsoft.com/azure/active-directory/saas-apps/pandadoc-tutorial), [Perceptyx](https://apexdata.azurewebsites.net/docs.microsoft.com/azure/active-directory/saas-apps/perceptyx-tutorial), [Proptimise OS](https://proptimise.co.uk/software/), [Vtiger CRM (SAML)](https://docs.microsoft.com/azure/active-directory/saas-apps/vtiger-crm-saml-tutorial), Oracle Access Manager dla Oracle Retail Merchandising, Oracle Access Manager dla Oracle E-Business Suite, Oracle IDCS dla pakietu E-Business Suite, Oracle IDCS dla PeopleSoft, Oracle IDCS dla JD Edwards

Aby uzyskać więcej informacji na temat aplikacji, zobacz [Integracja aplikacji SaaS z usługą Azure Active Directory](https://aka.ms/appstutorial). Aby uzyskać więcej informacji na temat wyświetlania aplikacji w galerii aplikacji usługi Azure AD, zobacz [Lista aplikacji w galerii aplikacji usługi Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>Automatyzacja inicjowania obsługi administracyjnej konta użytkownika dla tych nowo obsługiwanych aplikacji SaaS

**Typ:** Nowa funkcja  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwości produktu:** Monitorowanie & raportowania

Teraz można zautomatyzować tworzenie, aktualizowanie i usuwanie kont użytkowników dla tych nowo zintegrowanych aplikacji:

- [Zoom](https://docs.microsoft.com/azure/active-directory/saas-apps/zoom-provisioning-tutorial)

- [Envoy](https://docs.microsoft.com/azure/active-directory/saas-apps/envoy-provisioning-tutorial)

- [Proxyclick](https://docs.microsoft.com/azure/active-directory/saas-apps/proxyclick-provisioning-tutorial)

- [4me](https://docs.microsoft.com/azure/active-directory/saas-apps/4me-provisioning-tutorial)

Aby uzyskać więcej informacji na temat lepszego zabezpieczania organizacji przy użyciu automatycznego inicjowania obsługi administracyjnej konta użytkownika, zobacz [Automatyzacja inicjowania obsługi administracyjnej aplikacji SaaS przy użyciu usługi Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)

---

### <a name="view-the-real-time-progress-of-the-azure-ad-provisioning-service"></a>Wyświetlanie postępu usługi inicjowania obsługi administracyjnej usługi Azure AD w czasie rzeczywistym

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Inicjowanie obsługi administracyjnej aplikacji  
**Możliwości produktu:** Zarządzanie cyklem życia tożsamości

Zaktualizowaliśmy środowisko inicjowania obsługi administracyjnej usługi Azure AD, aby uwzględnić nowy pasek postępu, który pokazuje, jak daleko znajdujesz się w procesie inicjowania obsługi administracyjnej użytkowników. To zaktualizowane środowisko zawiera również informacje o liczbie użytkowników aprowied podczas bieżącego cyklu, a także ilu użytkowników zostały aprowidzone do tej pory.

Aby uzyskać więcej informacji, zobacz [Sprawdzanie stanu inicjowania obsługi administracyjnej przez użytkowników](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user).

---

### <a name="company-branding-now-appears-on-sign-out-and-error-screens"></a>Branding firmy pojawia się teraz na ekranach wylogowywania i błędów

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Uwierzytelnianie (loginy)  
**Możliwości produktu:** Uwierzytelnianie użytkownika

Zaktualizowaliśmy usługę Azure AD, aby znakowanie twojej firmy było teraz wyświetlane na ekranach wylogowywania i błędów, a także na stronie logowania. Nie musisz nic robić, aby włączyć tę funkcję, usługa Azure AD po prostu używa zasobów, które zostały już skonfigurowane w obszarze **znakowania firmy** w witrynie Azure portal.

Aby uzyskać więcej informacji na temat konfigurowania znakowania firm, zobacz [Dodawanie znakowania do stron usługi Azure Active Directory w organizacji](https://docs.microsoft.com/azure/active-directory/fundamentals/customize-branding).

---

### <a name="azure-multi-factor-authentication-mfa-server-is-no-longer-available-for-new-deployments"></a>Serwer uwierzytelniania wieloskładnikowego (MFA) platformy Azure nie jest już dostępny dla nowych wdrożeń

**Typ:** Przestarzałe  
**Kategoria usługi:** Mfa  
**Możliwości produktu:** Ochrona & zabezpieczeń tożsamości

Od 1 lipca 2019 r. firma Microsoft nie będzie już oferować serwera usługi MFA dla nowych wdrożeń. Nowi klienci, którzy chcą wymagać uwierzytelniania wieloskładnikowego w swojej organizacji, muszą teraz korzystać z uwierzytelniania wieloskładnikowego platformy Azure w chmurze. Klienci, którzy aktywowali serwer usługi MFA przed 1 lipca, nie zobaczą zmiany. Nadal będzie można pobrać najnowszą wersję, pobrać przyszłe aktualizacje i wygenerować poświadczenia aktywacji.

Aby uzyskać więcej informacji, zobacz [Wprowadzenie do serwera uwierzytelniania wieloskładnikowego platformy Azure](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfaserver-deploy). Aby uzyskać więcej informacji na temat wieloskładnikowego uwierzytelniania azure w chmurze, zobacz [Planowanie wdrożenia uwierzytelniania wieloskładnikowego platformy Azure w chmurze.](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

---

## <a name="may-2019"></a>Maj 2019 r.

### <a name="service-change-future-support-for-only-tls-12-protocols-on-the-application-proxy-service"></a>Zmiana usługi: Przyszła obsługa tylko protokołów TLS 1.2 w usłudze proxy aplikacji

**Typ:** Plan zmian  
**Kategoria usługi:** Serwer proxy aplikacji  
**Możliwości produktu:** Kontrola dostępu

Aby zapewnić najlepsze w swojej klasie szyfrowanie dla naszych klientów, ograniczamy dostęp tylko do protokołów TLS 1.2 w usłudze proxy aplikacji. Ta zmiana jest stopniowo wdrażana dla klientów, którzy już używają tylko protokołów TLS 1.2, więc nie powinno się widzieć żadnych zmian.

Wycofanie protokołów TLS 1.0 i TLS 1.1 nastąpi 31 sierpnia 2019 r., ale powiadomimy o tym z wyprzedzeniem, więc będziesz mieć czas na przygotowanie się do tej zmiany. Aby przygotować się na tę zmianę, upewnij się, że kombinacje klient-serwer i przeglądarka- serwer, w tym wszyscy klienci używane przez użytkowników do uzyskiwania dostępu do aplikacji opublikowanych za pośrednictwem serwera proxy aplikacji, są aktualizowane w celu używania protokołu TLS 1.2 do obsługi połączenia z usługą Proxy aplikacji. Aby uzyskać więcej informacji, zobacz [Dodawanie aplikacji lokalnej do zdalnego dostępu za pośrednictwem serwera proxy aplikacji w usłudze Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application#before-you-begin).

---

### <a name="use-the-usage-and-insights-report-to-view-your-app-related-sign-in-data"></a>Wyświetlanie danych logowania związanych z aplikacją za pomocą raportu użycia i szczegółowych informacji

**Typ:** Nowa funkcja  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwości produktu:** Monitorowanie & raportowania

Teraz można użyć raportu użycia i analizy, znajdującego się w obszarze **aplikacje dla przedsiębiorstw** w witrynie Azure portal, aby uzyskać widok zorientowany na aplikację danych logowania, w tym informacje o:

- Najpopularniejsze używane aplikacje dla Twojej organizacji

- Aplikacje z najbardziej nieudanymi logowaniami

- Najważniejsze błędy logowania dla każdej aplikacji

Aby uzyskać więcej informacji na temat tej funkcji, zobacz [Raport Użycia i szczegółowych informacji w portalu usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-usage-insights-report)

---

### <a name="automate-your-user-provisioning-to-cloud-apps-using-azure-ad"></a>Automatyzacja inicjowania obsługi administracyjnej aplikacji w chmurze przy użyciu usługi Azure AD

**Typ:** Nowa funkcja  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwości produktu:** Monitorowanie & raportowania

Wykonaj te nowe samouczki, aby użyć usługi inicjowania obsługi administracyjnej usługi Azure AD do automatyzacji tworzenia, usuwania i aktualizowania kont użytkowników dla następujących aplikacji opartych na chmurze:

- [Kometa](https://docs.microsoft.com/azure/active-directory/saas-apps/comeet-recruiting-software-provisioning-tutorial)

- [Sygnał dynamiczny](https://docs.microsoft.com/azure/active-directory/saas-apps/dynamic-signal-provisioning-tutorial)

- [KeeperBezpieczeństwo](https://docs.microsoft.com/azure/active-directory/saas-apps/keeper-password-manager-digitalvault-provisioning-tutorial)

Możesz również wykonać ten nowy [samouczek Dropbox,](https://docs.microsoft.com/azure/active-directory/saas-apps/dropboxforbusiness-provisioning-tutorial)który zawiera informacje o tym, jak aprowizować obiekty grupy.

Aby uzyskać więcej informacji na temat lepszego zabezpieczania organizacji za pomocą automatycznego inicjowania obsługi administracyjnej konta użytkownika, zobacz [Automatyzacja inicjowania obsługi administracyjnej aplikacji SaaS za pomocą usługi Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).

---

### <a name="identity-secure-score-is-now-available-in-azure-ad-general-availability"></a>Bezpieczny wynik tożsamości jest teraz dostępny w usłudze Azure AD (ogólna dostępność)

**Typ:** Nowa funkcja  
**Kategoria usługi:** N/a  
**Możliwości produktu:** Ochrona & zabezpieczeń tożsamości

Teraz można monitorować i poprawić swoją postawę zabezpieczeń tożsamości przy użyciu funkcji bezpiecznego wyniku tożsamości w usłudze Azure AD. Funkcja bezpiecznego wyniku tożsamości korzysta z jednego pulpitu nawigacyjnego, aby pomóc:

- Obiektywnie zmierz swoją postawę bezpieczeństwa tożsamości, na podstawie wyniku od 1 do 223.

- Planowanie ulepszeń zabezpieczeń tożsamości

- Sprawdź sukces ulepszeń zabezpieczeń

Aby uzyskać więcej informacji na temat funkcji oceny zabezpieczeń tożsamości, zobacz [Jaki jest wynik bezpiecznego tożsamości w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/fundamentals/identity-secure-score).

---

### <a name="new-app-registrations-experience-is-now-available-general-availability"></a>Nowe środowisko rejestracji aplikacji jest już dostępne (Ogólna dostępność)

**Typ:** Nowa funkcja  
**Kategoria usługi:** Uwierzytelnianie (loginy)  
**Możliwości produktu:** Środowisko deweloperskie

Nowe środowisko [rejestracji aplikacji](https://aka.ms/appregistrations) jest teraz w ogólnej dostępności. To nowe środowisko obejmuje wszystkie kluczowe funkcje, które znasz z witryny Azure portal i portal rejestracji aplikacji i ulepsza je za pośrednictwem:

- **Lepsze zarządzanie aplikacjami.** Zamiast wyświetlać aplikacje w różnych portalach, możesz teraz zobaczyć wszystkie aplikacje w jednej lokalizacji.

- **Uproszczona rejestracja aplikacji.** Od ulepszonego środowiska nawigacji po odświeżone środowisko wyboru uprawnień — teraz łatwiej jest zarejestrować aplikacje i nimi zarządzać.

- **Bardziej szczegółowe informacje.** Więcej informacji na temat aplikacji można znaleźć, w tym przewodniki szybki start i inne informacje.

Aby uzyskać więcej informacji, zobacz [Microsoft identity platform](https://docs.microsoft.com/azure/active-directory/develop/) i środowisko rejestracji aplikacji jest teraz ogólnie [dostępne!](https://developer.microsoft.com/identity/blogs/new-app-registrations-experience-is-now-generally-available/) ogłoszenie bloga.

---

### <a name="new-capabilities-available-in-the-risky-users-api-for-identity-protection"></a>Nowe możliwości dostępne w interfejsie API ryzykownych użytkowników dla ochrony tożsamości

**Typ:** Nowa funkcja  
**Kategoria usługi:** Ochrona tożsamości  
**Możliwości produktu:** Ochrona & zabezpieczeń tożsamości

Z przyjemnością informujemy, że możesz teraz użyć interfejsu API ryzykownych użytkowników, aby pobrać historię ryzyka użytkowników, odrzucić ryzykownych użytkowników i potwierdzić użytkowników jako zagrożonych. Ta zmiana pomaga bardziej efektywnie aktualizować stan ryzyka użytkowników i zrozumieć ich historię ryzyka.

Aby uzyskać więcej informacji, zobacz [dokumentację referencyjną interfejsu API ryzykownych użytkowników](https://docs.microsoft.com/graph/api/resources/riskyuser?view=graph-rest-beta).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---may-2019"></a>Nowe aplikacje federacyjne dostępne w galerii aplikacji usługi Azure AD — maj 2019 r.

**Typ:** Nowa funkcja  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwości produktu:** Integracja stron trzecich

W maju 2019 r. dodaliśmy do galerii aplikacji 21 nowych aplikacji z obsługą federacji:

[Freedcamp](https://docs.microsoft.com/azure/active-directory/saas-apps/freedcamp-tutorial), [Prawdziwe linki](https://docs.microsoft.com/azure/active-directory/saas-apps/real-links-tutorial), [Kianda](https://app.kianda.com/sso/OpenID/AzureAD/), [Prosty znak](https://docs.microsoft.com/azure/active-directory/saas-apps/simple-sign-tutorial), [Braze](https://docs.microsoft.com/azure/active-directory/saas-apps/braze-tutorial), [Displayr](https://docs.microsoft.com/azure/active-directory/saas-apps/displayr-tutorial), [Templafy](https://docs.microsoft.com/azure/active-directory/saas-apps/templafy-tutorial), [Marketo Sales Engage](https://toutapp.com/login), [ACLP](https://docs.microsoft.com/azure/active-directory/saas-apps/aclp-tutorial), [OutSystems](https://docs.microsoft.com/azure/active-directory/saas-apps/outsystems-tutorial), [Meta4 Global HR](https://docs.microsoft.com/azure/active-directory/saas-apps/meta4-global-hr-tutorial), Quantum [Workplace](https://docs.microsoft.com/azure/active-directory/saas-apps/quantum-workplace-tutorial), [Kobalt](https://docs.microsoft.com/azure/active-directory/saas-apps/cobalt-tutorial), [webMethods API Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/webmethods-integration-cloud-tutorial), [RedFlag](https://pocketstop.com/redflag/), [Whatfix](https://docs.microsoft.com/azure/active-directory/saas-apps/whatfix-tutorial), [Control](https://docs.microsoft.com/azure/active-directory/saas-apps/control-tutorial), [JOBHUB](https://docs.microsoft.com/azure/active-directory/saas-apps/jobhub-tutorial), [NEOGOV](https://docs.microsoft.com/azure/active-directory/saas-apps/neogov-tutorial), [Foodee](https://docs.microsoft.com/azure/active-directory/saas-apps/foodee-tutorial), [MyVR](https://docs.microsoft.com/azure/active-directory/saas-apps/myvr-tutorial)

Aby uzyskać więcej informacji na temat aplikacji, zobacz [Integracja aplikacji SaaS z usługą Azure Active Directory](https://aka.ms/appstutorial). Aby uzyskać więcej informacji na temat wyświetlania aplikacji w galerii aplikacji usługi Azure AD, zobacz [Lista aplikacji w galerii aplikacji usługi Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="improved-groups-creation-and-management-experiences-in-the-azure-ad-portal"></a>Ulepszone środowisko tworzenia i zarządzania grupami w portalu usługi Azure AD

**Typ:** Nowa funkcja  
**Kategoria usługi:** Zarządzanie grupą  
**Możliwości produktu:** Współpracy

Wprowadzono ulepszenia do środowisk związanych z grupami w portalu usługi Azure AD. Te ulepszenia umożliwiają administratorom lepsze zarządzanie listami grup, listami członków i dostarczanie dodatkowych opcji tworzenia.

Ulepszenia obejmują:

- Podstawowe filtrowanie według typu członkostwa i typu grupy.

- Dodawanie nowych kolumn, takich jak Źródło i Adres e-mail.

- Możliwość wielokrotnego wybierania grup, członków i list właścicieli w celu łatwego usunięcia.

- Możliwość wyboru adresu e-mail i dodawania właścicieli podczas tworzenia grupy.

Aby uzyskać więcej informacji, zobacz [Tworzenie podstawowej grupy i dodawanie członków w usłudze Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal).

---

### <a name="configure-a-naming-policy-for-office-365-groups-in-azure-ad-portal-general-availability"></a>Konfigurowanie zasad nazewnictwa dla grup usługi Office 365 w portalu usługi Azure AD (dostępność ogólna)

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Zarządzanie grupą  
**Możliwości produktu:** Współpracy

Administratorzy mogą teraz konfigurować zasady nazewnictwa dla grup usługi Office 365 przy użyciu portalu usługi Azure AD. Ta zmiana pomaga wymusić spójne konwencje nazewnictwa dla grup usługi Office 365 utworzonych lub edytowanych przez użytkowników w organizacji.

Zasady nazewnictwa dla grup usługi Office 365 można skonfigurować na dwa różne sposoby:

- Zdefiniuj prefiksy lub sufiksy, które są automatycznie dodawane do nazwy grupy.

- Prześlij dostosowany zestaw zablokowanych słów dla twojej organizacji, które nie są dozwolone w nazwach grup (na przykład "Dyrektor generalny, lista płac, kadr").

Aby uzyskać więcej informacji, zobacz [Wymuszanie zasad nazewnictwa dla grup usługi Office 365](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-naming-policy).

---

### <a name="microsoft-graph-api-endpoints-are-now-available-for-azure-ad-activity-logs-general-availability"></a>Punkty końcowe interfejsu API programu Microsoft Graph są teraz dostępne dla dzienników aktywności usługi Azure AD (dostępność ogólna)

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Reporting  
**Możliwości produktu:** Monitorowanie & raportowania

Z przyjemnością ogłaszamy ogólną dostępność punktów końcowych interfejsu API programu Microsoft Graph dla dzienników aktywności usługi Azure AD. W tej wersji można teraz używać wersji 1.0 dzienników inspekcji usługi Azure AD, a także logów logowania interfejsów API.

Aby uzyskać więcej informacji, zobacz [omówienie interfejsu API dziennika inspekcji usługi Azure AD](https://docs.microsoft.com/graph/api/resources/azure-ad-auditlog-overview?view=graph-rest-1.0).

---

### <a name="administrators-can-now-use-conditional-access-for-the-combined-registration-process-public-preview"></a>Administratorzy mogą teraz używać dostępu warunkowego do procesu rejestracji połączonej (publiczna wersja zapoznawcza)

**Typ:** Nowa funkcja  
**Kategoria usługi:** Dostęp warunkowy  
**Możliwości produktu:** Ochrona & zabezpieczeń tożsamości  

Administratorzy mogą teraz tworzyć zasady dostępu warunkowego do użycia na połączonej stronie rejestracji. Obejmuje to stosowanie zasad umożliwiających rejestrację, jeśli:

- Użytkownicy są w zaufanej sieci.

- Użytkownicy są niskie ryzyko logowania.

- Użytkownicy są na zarządzanym urządzeniu.

- Użytkownicy zgadzają się na warunki użytkowania organizacji (WU).

Aby uzyskać więcej informacji na temat dostępu warunkowego i resetowania hasła, można zobaczyć [dostęp warunkowy dla usługi Azure AD połączone mfa i resetowanie hasła środowiska rejestracji wpis w blogu](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Conditional-access-for-the-Azure-AD-combined-MFA-and-password/ba-p/566348). Aby uzyskać więcej informacji na temat zasad dostępu warunkowego dla procesu rejestracji połączonej, zobacz [Zasady dostępu warunkowego dla połączonej rejestracji](https://docs.microsoft.com/azure/active-directory/authentication/howto-registration-mfa-sspr-combined#conditional-access-policies-for-combined-registration). Aby uzyskać więcej informacji na temat funkcji warunków użytkowania usługi Azure AD, zobacz [Funkcja warunków użytkowania usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use).

---

## <a name="april-2019"></a>Kwiecień 2019 r.

### <a name="new-azure-ad-threat-intelligence-detection-is-now-available-as-part-of-azure-ad-identity-protection"></a>Nowe wykrywanie analizy zagrożeń usługi Azure AD jest teraz dostępne w ramach usługi Azure AD Identity Protection

**Typ:** Nowa funkcja  
**Kategoria usługi:** Ochrona tożsamości usługi Azure AD  
**Możliwości produktu:** Ochrona & zabezpieczeń tożsamości

Wykrywanie analizy zagrożeń usługi Azure AD jest teraz dostępne jako część zaktualizowanej funkcji usługi Azure AD Identity Protection. Ta nowa funkcja pomaga wskazać nietypową aktywność użytkownika dla określonego użytkownika lub działania, które jest zgodne ze znanymi wzorcami ataków opartymi na wewnętrznych i zewnętrznych źródłach analizy zagrożeń firmy Microsoft.

Aby uzyskać więcej informacji na temat odświeżona wersja usługi Azure AD Identity Protection, zobacz [cztery główne ulepszenia usługi Azure AD Identity Protection są teraz w blogu publicznej wersji zapoznawczej](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Four-major-Azure-AD-Identity-Protection-enhancements-are-now-in/ba-p/326935) i [Co to jest usługa Azure Active Directory Identity Protection (odświeżony)?](https://docs.microsoft.com/azure/active-directory/identity-protection/overview-v2) (Jak działa usługa Azure RMS). Aby uzyskać więcej informacji na temat wykrywania analizy zagrożeń usługi Azure AD, zobacz artykuł [wykrywania zagrożeń usługi Azure Active Directory Identity Protection.](https://docs.microsoft.com/azure/active-directory/identity-protection/concept-identity-protection-risks)

---

### <a name="azure-ad-entitlement-management-is-now-available-public-preview"></a>Zarządzanie uprawnieniami usługi Azure AD jest teraz dostępne (publiczna wersja zapoznawcza)

**Typ:** Nowa funkcja  
**Kategoria usługi:** Zarządzanie tożsamościami  
**Możliwości produktu:** Zarządzanie tożsamościami

Zarządzanie uprawnieniami usługi Azure AD, teraz w publicznej wersji zapoznawczej, pomaga klientom delegować zarządzanie pakietami dostępu, który określa, jak pracownicy i partnerzy biznesowi mogą żądać dostępu, kto musi zatwierdzić i jak długo mają dostęp. Pakiety programu Access mogą zarządzać członkostwem w grupach usługi Azure AD i Office 365, przypisaniami ról w aplikacjach dla przedsiębiorstw oraz przypisaniami ról w witrynach usługi SharePoint Online. Więcej informacji na temat zarządzania uprawnieniami można przeczytać w [ychowy sposób zarządzania uprawnieniami usługi Azure AD](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-overview). Aby dowiedzieć się więcej na temat zakresu funkcji zarządzania tożsamościami usługi Azure AD, w tym zarządzania tożsamościami uprzywilejowanymi, przeglądów dostępu i warunków użytkowania, zobacz [Co to jest zarządzanie tożsamościami usługi Azure AD?](../governance/identity-governance-overview.md)

---

### <a name="configure-a-naming-policy-for-office-365-groups-in-azure-ad-portal-public-preview"></a>Konfigurowanie zasad nazewnictwa dla grup usługi Office 365 w portalu usługi Azure AD (publiczna wersja zapoznawcza)

**Typ:** Nowa funkcja  
**Kategoria usługi:** Zarządzanie grupą  
**Możliwości produktu:** Współpracy

Administratorzy mogą teraz konfigurować zasady nazewnictwa dla grup usługi Office 365 przy użyciu portalu usługi Azure AD. Ta zmiana pomaga wymusić spójne konwencje nazewnictwa dla grup usługi Office 365 utworzonych lub edytowanych przez użytkowników w organizacji.

Zasady nazewnictwa dla grup usługi Office 365 można skonfigurować na dwa różne sposoby:

- Zdefiniuj prefiksy lub sufiksy, które są automatycznie dodawane do nazwy grupy.

- Prześlij dostosowany zestaw zablokowanych słów dla twojej organizacji, które nie są dozwolone w nazwach grup (na przykład "Dyrektor generalny, lista płac, kadr").

Aby uzyskać więcej informacji, zobacz [Wymuszanie zasad nazewnictwa dla grup usługi Office 365](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-naming-policy).

---

### <a name="azure-ad-activity-logs-are-now-available-in-azure-monitor-general-availability"></a>Dzienniki aktywności usługi Azure AD są teraz dostępne w usłudze Azure Monitor (dostępność ogólna)

**Typ:** Nowa funkcja  
**Kategoria usługi:** Reporting  
**Możliwości produktu:** Monitorowanie & raportowania

Aby pomóc w adresie opinii na temat wizualizacji za pomocą dzienników aktywności usługi Azure AD, wprowadzamy nową funkcję insights w usłudze Log Analytics. Ta funkcja ułatwia uzyskiwanie szczegółowych informacji na temat zasobów usługi Azure AD przy użyciu naszych szablonów interaktywnych, o nazwie Skoroszyty. Te wstępnie utworzone skoroszyty mogą zawierać szczegółowe informacje o aplikacjach lub użytkownikach i obejmują:

- **Logowania.** Zawiera szczegółowe informacje dotyczące aplikacji i użytkowników, w tym lokalizację logowania, używany system operacyjny lub klienta przeglądarki i wersję oraz liczbę pomyślnych lub nieudanych logów.

- **Uwierzytelnianie starsze i dostęp warunkowy.** Zawiera szczegółowe informacje dla aplikacji i użytkowników korzystających ze starszego uwierzytelniania, w tym użycie uwierzytelniania wieloskładnikowego wyzwalane przez zasady dostępu warunkowego, aplikacje korzystające z zasad dostępu warunkowego itd.

- **Analiza błędów logowania.** Pomaga ustalić, czy błędy logowania występują z powodu akcji użytkownika, problemów z zasadami lub infrastruktury.

- **Raporty niestandardowe.** Można tworzyć nowe lub edytować istniejące skoroszyty, aby dostosować funkcję Insights dla organizacji.

Aby uzyskać więcej informacji, zobacz [Jak używać skoroszytów usługi Azure Monitor dla raportów usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-use-azure-monitor-workbooks).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---april-2019"></a>Nowe aplikacje federacyjne dostępne w galerii aplikacji usługi Azure AD — kwiecień 2019 r.

**Typ:** Nowa funkcja  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwości produktu:** Integracja stron trzecich

W kwietniu 2019 r. dodaliśmy do galerii aplikacji 21 nowych aplikacji z obsługą federacji:

[SAP Fiori](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-fiori-tutorial), [HRworks Single Sign-On](https://docs.microsoft.com/azure/active-directory/saas-apps/hrworks-single-sign-on-tutorial), [Percolate](https://docs.microsoft.com/azure/active-directory/saas-apps/percolate-tutorial), [MobiControl](https://docs.microsoft.com/azure/active-directory/saas-apps/mobicontrol-tutorial), [Citrix NetScaler](https://docs.microsoft.com/azure/active-directory/saas-apps/citrix-netscaler-tutorial), [Shibumi](https://docs.microsoft.com/azure/active-directory/saas-apps/shibumi-tutorial), [Benchling](https://docs.microsoft.com/azure/active-directory/saas-apps/benchling-tutorial), [MileIQ](https://mileiq.onelink.me/991934284/7e980085), [PageDNA](https://docs.microsoft.com/azure/active-directory/saas-apps/pagedna-tutorial), [EduBrite LMS](https://docs.microsoft.com/azure/active-directory/saas-apps/edubrite-lms-tutorial), [RStudio Connect](https://docs.microsoft.com/azure/active-directory/saas-apps/rstudio-connect-tutorial), [AMMS](https://docs.microsoft.com/azure/active-directory/saas-apps/amms-tutorial), [Mitel Connect](https://docs.microsoft.com/azure/active-directory/saas-apps/mitel-connect-tutorial), [Alibaba Cloud (SSO oparte na rolach)](https://docs.microsoft.com/azure/active-directory/saas-apps/alibaba-cloud-service-role-based-sso-tutorial), [Certent Equity Management](https://docs.microsoft.com/azure/active-directory/saas-apps/certent-equity-management-tutorial), [Sectigo Certificate Manager](https://docs.microsoft.com/azure/active-directory/saas-apps/sectigo-certificate-manager-tutorial), [GreenOrbit](https://docs.microsoft.com/azure/active-directory/saas-apps/greenorbit-tutorial), [Workgrid](https://docs.microsoft.com/azure/active-directory/saas-apps/workgrid-tutorial), [monday.com](https://docs.microsoft.com/azure/active-directory/saas-apps/mondaycom-tutorial), [SurveyMonkey Enterprise](https://docs.microsoft.com/azure/active-directory/saas-apps/surveymonkey-enterprise-tutorial), [Indiggo](https://indiggolead.com/)

Aby uzyskać więcej informacji na temat aplikacji, zobacz [Integracja aplikacji SaaS z usługą Azure Active Directory](https://aka.ms/appstutorial). Aby uzyskać więcej informacji na temat wyświetlania aplikacji w galerii aplikacji usługi Azure AD, zobacz [Lista aplikacji w galerii aplikacji usługi Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="new-access-reviews-frequency-option-and-multiple-role-selection"></a>Nowy dostęp przegląda opcję częstotliwości i wybór wielu ról

**Typ:** Nowa funkcja  
**Kategoria usługi:** Recenzje programu Access  
**Możliwości produktu:** Zarządzanie tożsamościami

Nowe aktualizacje w przeglądach dostępu usługi Azure AD umożliwiają:

- Zmień częstotliwość recenzji dostępu na **półrocznie,** oprócz wcześniej istniejących opcji tygodniowych, miesięcznych, kwartalnych i rocznych.

- Wybierz wiele ról zasobów usługi Azure AD i platformy Azure podczas tworzenia pojedynczej recenzji dostępu. W tej sytuacji wszystkie role są skonfigurowane z tymi samymi ustawieniami i wszyscy recenzenci są powiadamiani w tym samym czasie.

Aby uzyskać więcej informacji na temat tworzenia przeglądu dostępu, zobacz [Tworzenie przeglądu dostępu grup lub aplikacji w recenzjach dostępu usługi Azure AD](https://docs.microsoft.com/azure/active-directory/governance/create-access-review).

---

### <a name="azure-ad-connect-email-alert-systems-are-transitioning-sending-new-email-sender-information-for-some-customers"></a>Systemy alertów e-mail usługi Azure AD Connect zmieniają się, wysyłając nowe informacje o nadawcy wiadomości e-mail dla niektórych klientów

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Synchronizacja usługi AD  
**Możliwości produktu:** Platformy

Usługa Azure AD Connect jest w trakcie przenoszenia naszych systemów alertów poczty e-mail, potencjalnie pokazując niektórym klientom nowego nadawcę wiadomości e-mail. Aby rozwiązać ten problem, należy dodać `azure-noreply@microsoft.com` do listy dozwolonych organizacji lub nie będzie można nadal otrzymywać ważnych alertów z usługi Office 365, platformy Azure lub usług synchronizacji.

---

### <a name="upn-suffix-changes-are-now-successful-between-federated-domains-in-azure-ad-connect"></a>Zmiany sufiksu nazwy UPN są teraz skuteczne między domenami federacyjnie w usłudze Azure AD Connect

**Typ:** Stałe  
**Kategoria usługi:** Synchronizacja usługi AD  
**Możliwości produktu:** Platformy

Teraz możesz z powodzeniem zmienić sufiks nazwy UPN użytkownika z jednej domeny federacyjnej na inną domenę federacyjnej w usłudze Azure AD Connect. Ta poprawka oznacza, że nie powinieneś już doświadczać komunikatu o błędzie FederatedDomainChangeError podczas cyklu synchronizacji lub odbierania wiadomości e-mail z powiadomieniem z informacją: "Nie można zaktualizować tego obiektu w usłudze Azure Active Directory, ponieważ atrybut [FederatedUser.UserPrincipalName] jest nieprawidłowy. Zaktualizuj wartość w lokalnych usługach katalogowych".

Aby uzyskać więcej informacji, zobacz [Rozwiązywanie problemów z błędami podczas synchronizacji](https://docs.microsoft.com/azure/active-directory/hybrid/tshoot-connect-sync-errors#federateddomainchangeerror).

---

### <a name="increased-security-using-the-app-protection-based-conditional-access-policy-in-azure-ad-public-preview"></a>Zwiększone bezpieczeństwo przy użyciu zasad dostępu warunkowego opartego na ochronie aplikacji w usłudze Azure AD (publiczna wersja zapoznawcza)

**Typ:** Nowa funkcja  
**Kategoria usługi:** Dostęp warunkowy  
**Możliwości produktu:** Ochrona & zabezpieczeń tożsamości

Dostęp warunkowy oparty na ochronie aplikacji jest teraz dostępny przy użyciu zasad **ochrony aplikacji Wymagaj.** Ta nowa zasada pomaga zwiększyć bezpieczeństwo organizacji, pomagając zapobiegać:

- Użytkownicy uzyskujący dostęp do aplikacji bez licencji usługi Microsoft Intune.

- Użytkownicy nie mogą uzyskać zasad ochrony aplikacji usługi Microsoft Intune.

- Użytkownicy uzyskujący dostęp do aplikacji bez skonfigurowanych zasad ochrony aplikacji usługi Microsoft Intune.

Aby uzyskać więcej informacji, zobacz [Jak wymagać zasad ochrony aplikacji dla dostępu do aplikacji w chmurze z dostępem warunkowym](https://docs.microsoft.com/azure/active-directory/conditional-access/app-protection-based-conditional-access).

---

### <a name="new-support-for-azure-ad-single-sign-on-and-conditional-access-in-microsoft-edge-public-preview"></a>Nowa obsługa logowania jednokrotnego usługi Azure AD i dostępu warunkowego w przeglądarce Microsoft Edge (publiczna wersja zapoznawcza)

**Typ:** Nowa funkcja  
**Kategoria usługi:** Dostęp warunkowy  
**Możliwości produktu:** Ochrona & zabezpieczeń tożsamości

Ulepszyliśmy naszą obsługę usługi Azure AD dla usługi Microsoft Edge, w tym zapewnienie nowej obsługi logowania jednokrotnego usługi Azure AD i dostępu warunkowego. Jeśli wcześniej używano przeglądarki zarządzanej usługi Microsoft Intune, możesz teraz użyć przeglądarki Microsoft Edge.

Aby uzyskać więcej informacji na temat konfigurowania urządzeń i aplikacji przy użyciu dostępu warunkowego i zarządzania nimi przy użyciu dostępu warunkowego, zobacz [Wymaganie zarządzanych urządzeń do dostępu do aplikacji w chmurze za pomocą dostępu do aplikacji warunkowej](https://docs.microsoft.com/azure/active-directory/conditional-access/require-managed-devices) i [wymaganie zatwierdzonych aplikacji klienckich do uzyskiwania dostępu do aplikacji w chmurze za pomocą programu Dostęp warunkowy](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access). Aby uzyskać więcej informacji na temat zarządzania dostępem przy użyciu przeglądarki Microsoft Edge za pomocą zasad usługi Microsoft Intune, zobacz [Zarządzanie dostępem do Internetu przy użyciu przeglądarki chronionej zasadami usługi Microsoft Intune](https://docs.microsoft.com/intune/app-configuration-managed-browser).

---

## <a name="march-2019"></a>Marzec 2019 r.

### <a name="identity-experience-framework-and-custom-policy-support-in-azure-active-directory-b2c-is-now-available-ga"></a>Struktura środowiska tożsamości i obsługa zasad niestandardowych w usłudze Azure Active Directory B2C jest teraz dostępna (GA)

**Typ:** Nowa funkcja  
**Kategoria usługi:** B2C - Zarządzanie tożsamością konsumenta  
**Możliwości produktu:** B2B/B2C

Teraz można tworzyć niestandardowe zasady w usłudze Azure AD B2C, w tym następujące zadania, które są obsługiwane na dużą skalę i w ramach naszej umowy SLA platformy Azure:

- Tworzenie i przekazywanie podróży użytkowników uwierzytelniania niestandardowego przy użyciu zasad niestandardowych.

- Opis podróży użytkownika krok po kroku jako wymiany między dostawcami oświadczeń.

- Zdefiniuj rozgałęzienie warunkowe w podróżach użytkowników.

- Przekształcanie i mapowanie oświadczeń do wykorzystania w decyzjach i komunikacji w czasie rzeczywistym.

- Użyj usług obsługujących interfejs API REST w środowiskach pozyskiwania użytkowników uwierzytelniania niestandardowego. Na przykład z dostawcami poczty e-mail, CRM i zastrzeżonymi systemami autoryzacji.

- Zgodność z usługami dostawców tożsamości zgodnych z protokołem OpenIDConnect. Na przykład z wieloma dzierżawcami usługi Azure AD, dostawców kont społecznościowych lub dostawców weryfikacji dwuskładnikowej.

Aby uzyskać więcej informacji na temat tworzenia zasad niestandardowych, zobacz [Uwagi dewelopera dotyczące zasad niestandardowych w usłudze Azure Active Directory B2C](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-developer-notes-custom) i przeczytaj [wpis w blogu Alex Simon, w tym studia przypadków](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-custom-policies-to-build-your-own-identity-journeys/ba-p/382791).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---march-2019"></a>Nowe aplikacje federacyjne dostępne w galerii aplikacji usługi Azure AD — marzec 2019 r.

**Typ:** Nowa funkcja  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwości produktu:** Integracja stron trzecich

W marcu 2019 r. dodaliśmy do galerii aplikacji 14 nowych aplikacji z obsługą federacji:

[ISEC7 Mobile Exchange Delegate](https://www.isec7.com/english/), [MediusFlow](https://office365.cloudapp.mediusflow.com/), [ePlatform](https://docs.microsoft.com/azure/active-directory/saas-apps/eplatform-tutorial), [Fulcrum](https://docs.microsoft.com/azure/active-directory/saas-apps/fulcrum-tutorial), [ExcelityGlobal](https://docs.microsoft.com/azure/active-directory/saas-apps/excelityglobal-tutorial), [Wyjaśnienie oparte system audytu](https://docs.microsoft.com/azure/active-directory/saas-apps/explanation-based-auditing-system-tutorial), [Lean](https://docs.microsoft.com/azure/active-directory/saas-apps/lean-tutorial), [Powerschool Performance Matters](https://docs.microsoft.com/azure/active-directory/saas-apps/powerschool-performance-matters-tutorial), [Cinode](https://cinode.com/), [Iris Intranet](https://docs.microsoft.com/azure/active-directory/saas-apps/iris-intranet-tutorial), [Empactis](https://docs.microsoft.com/azure/active-directory/saas-apps/empactis-tutorial), [SmartDraw](https://docs.microsoft.com/azure/active-directory/saas-apps/smartdraw-tutorial), [Confirmit Horizons](https://docs.microsoft.com/azure/active-directory/saas-apps/confirmit-horizons-tutorial), [TAS](https://docs.microsoft.com/azure/active-directory/saas-apps/tas-tutorial)

Aby uzyskać więcej informacji na temat aplikacji, zobacz [Integracja aplikacji SaaS z usługą Azure Active Directory](https://aka.ms/appstutorial). Aby uzyskać więcej informacji na temat wyświetlania aplikacji w galerii aplikacji usługi Azure AD, zobacz [Lista aplikacji w galerii aplikacji usługi Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="new-zscaler-and-atlassian-provisioning-connectors-in-the-azure-ad-gallery---march-2019"></a>Nowe łączniki inicjowania obsługi administracyjnej zscaler i atlassian w galerii usługi Azure AD — marzec 2019 r.

**Typ:** Nowa funkcja  
**Kategoria usługi:** Inicjowanie obsługi administracyjnej aplikacji  
**Możliwości produktu:** Integracja stron trzecich

Automatyzacja tworzenia, aktualizowania i usuwania kont użytkowników dla następujących aplikacji:

[Zscaler](https://aka.ms/ZscalerProvisioning), [Zscaler Beta](https://aka.ms/ZscalerBetaProvisioning), [Zscaler One](https://aka.ms/ZscalerOneProvisioning), [Zscaler Dwa](https://aka.ms/ZscalerTwoProvisioning), [Zscaler Trzy](https://aka.ms/ZscalerThreeProvisioning), [Zscaler ZSCloud](https://aka.ms/ZscalerZSCloudProvisioning), [Atlassian Cloud](https://aka.ms/atlassianCloudProvisioning)

Aby uzyskać więcej informacji na temat lepszego zabezpieczania organizacji za pomocą automatycznego inicjowania obsługi administracyjnej konta użytkownika, zobacz [Automatyzacja inicjowania obsługi administracyjnej aplikacji SaaS za pomocą usługi Azure AD](https://aka.ms/ProvisioningDocumentation).

---

### <a name="restore-and-manage-your-deleted-office-365-groups-in-the-azure-ad-portal"></a>Przywracanie usuniętych grup usługi Office 365 i zarządzanie nimi w portalu usługi Azure AD

**Typ:** Nowa funkcja  
**Kategoria usługi:** Zarządzanie grupą  
**Możliwości produktu:** Współpracy

Teraz możesz wyświetlać usunięte grupy usługi Office 365 i zarządzać nimi z portalu usługi Azure AD. Ta zmiana pomaga sprawdzić, które grupy są dostępne do przywrócenia, wraz z umożliwieniem trwałego usunięcia wszystkich grup, które nie są potrzebne w organizacji.

Aby uzyskać więcej informacji, zobacz [Przywracanie wygasłych lub usuniętych grup](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-restore-deleted#view-and-manage-the-deleted-office-365-groups-that-are-available-to-restore).

---

### <a name="single-sign-on-is-now-available-for-azure-ad-saml-secured-on-premises-apps-through-application-proxy-public-preview"></a>Logowanie jednokrotne jest teraz dostępne dla aplikacji lokalnych zabezpieczonych przez usługę Azure AD SAML za pośrednictwem serwera proxy aplikacji (publiczna wersja zapoznawcza)

**Typ:** Nowa funkcja  
**Kategoria usługi:** Serwer proxy aplikacji  
**Możliwości produktu:** Kontrola dostępu

Teraz można zapewnić środowisko logowania jednokrotnego (SSO) dla lokalnych aplikacji uwierzytelnionych przez SAML, wraz ze zdalnym dostępem do tych aplikacji za pośrednictwem serwera proxy aplikacji. Aby uzyskać więcej informacji na temat konfigurowania logowania jednokrotnego SAML w aplikacjach lokalnych, zobacz [Logowanie jednokrotne SAML dla aplikacji lokalnych z serwerem proxy aplikacji (Wersja zapoznawcza).](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-single-sign-on-on-premises-apps)

---

### <a name="client-apps-in-request-loops-will-be-interrupted-to-improve-reliability-and-user-experience"></a>Aplikacje klienckie w pętlach żądań zostaną przerwane, aby poprawić niezawodność i komfort użytkowania

**Typ:** Nowa funkcja  
**Kategoria usługi:** Uwierzytelnianie (loginy)  
**Możliwości produktu:** Uwierzytelnianie użytkownika

Aplikacje klienckie mogą niepoprawnie wystawiać setki tych samych żądań logowania w krótkim okresie czasu. Te żądania, niezależnie od tego, czy się powiodły, czy nie, przyczyniają się do słabego środowiska użytkownika i zwiększonych obciążeń dla dostawców tożsamości, zwiększając opóźnienia dla wszystkich użytkowników i zmniejszając dostępność usługi IDP.

Ta aktualizacja `invalid_grant` wysyła `AADSTS50196: The server terminated an operation because it encountered a loop while processing a request` błąd: do aplikacji klienckich, które wystawiają zduplikowane żądania wiele razy w krótkim okresie czasu, poza zakresem normalnej pracy. Aplikacje klienckie, które napotykają ten problem, powinny wyświetlać interaktywny monit, który wymaga od użytkownika ponownego zalogowania się. Aby uzyskać więcej informacji na temat tej zmiany i jak naprawić aplikację, jeśli napotka ten błąd, zobacz [Co nowego w przypadku uwierzytelniania?](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#looping-clients-will-be-interrupted).

---

### <a name="new-audit-logs-user-experience-now-available"></a>Nowe środowisko użytkownika dzienników inspekcji jest już dostępne

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Reporting  
**Możliwości produktu:** Monitorowanie & raportowania

Utworzyliśmy nową stronę **dzienników inspekcji** usługi Azure AD, aby poprawić zarówno czytelność, jak i sposób wyszukiwania informacji. Aby wyświetlić nową stronę **Dzienniki inspekcji,** wybierz **pozycję Dzienniki inspekcji** w sekcji **Działania** usługi Azure AD.

![Nowa strona Dzienniki inspekcji z przykładowymi informacjami](media/whats-new/audit-logs-page.png)

Aby uzyskać więcej informacji na temat nowej strony **Dzienniki inspekcji,** zobacz [Inspekcja raportów aktywności w portalu usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-audit-logs#audit-logs).

---

### <a name="new-warnings-and-guidance-to-help-prevent-accidental-administrator-lockout-from-misconfigured-conditional-access-policies"></a>Nowe ostrzeżenia i wskazówki ułatwiające zapobieganie przypadkowej blokadzie administratora z nieprawidłowo skonfigurowanych zasad dostępu warunkowego

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Dostęp warunkowy  
**Możliwości produktu:** Ochrona & zabezpieczeń tożsamości

Aby zapobiec przypadkowemu zablokowaniu przez administratorów własnych dzierżaw za pomocą nieprawidłowo skonfigurowanych zasad dostępu warunkowego, utworzyliśmy nowe ostrzeżenia i zaktualizowane wskazówki w witrynie Azure portal. Aby uzyskać więcej informacji na temat nowych wskazówek, zobacz [Jakie są zależności usług w usłudze Azure Active Directory Dostęp warunkowy](https://docs.microsoft.com/azure/active-directory/conditional-access/service-dependencies).

---

### <a name="improved-end-user-terms-of-use-experiences-on-mobile-devices"></a>Ulepszone warunki użytkowania użytkowników końcowych na urządzeniach mobilnych

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Warunki użytkowania  
**Możliwości produktu:** Zarządzania

Zaktualizowaliśmy nasze istniejące warunki użytkowania, aby poprawić sposób przeglądania i wyrażania zgody na warunki użytkowania na urządzeniu mobilnym. Teraz możesz powiększać i pomniejszać, wracać, pobierać informacje i wybierać hiperłącza. Aby uzyskać więcej informacji na temat zaktualizowanych warunków użytkowania, zobacz [Funkcja warunków użytkowania usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use#what-terms-of-use-looks-like-for-users).

---

### <a name="new-azure-ad-activity-logs-download-experience-available"></a>Nowe środowisko pobierania dzienników aktywności usługi Azure AD

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Reporting  
**Możliwości produktu:** Monitorowanie & raportowania

Teraz można pobrać duże ilości dzienników aktywności bezpośrednio z witryny Azure portal. Ta aktualizacja umożliwia:

- Pobierz do 250 000 wierszy.

- Otrzymuuj powiadomienia po zakończeniu pobierania.

- Dostosuj nazwę pliku.

- Określ format wyjściowy, JSON lub CSV.

Aby uzyskać więcej informacji na temat tej funkcji, zobacz [Szybki start: Pobieranie raportu inspekcji przy użyciu witryny Azure portal](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-download-audit-report)

---

### <a name="breaking-change-updates-to-condition-evaluation-by-exchange-activesync-eas"></a>Przerywanie zmiany: aktualizacje oceny stanu przez program Exchange ActiveSync (EAS)

**Typ:** Plan zmian  
**Kategoria usługi:** Dostęp warunkowy  
**Możliwości produktu:** Kontrola dostępu

Jesteśmy w trakcie aktualizowania sposobu oceny następujących warunków przez program Exchange ActiveSync (EAS):

- Lokalizacja użytkownika na podstawie kraju, regionu lub adresu IP

- Ryzyko logowania

- Platforma urządzeń

Jeśli wcześniej używano tych warunków w zasadach dostępu warunkowego, należy pamiętać, że zachowanie warunku może ulec zmianie. Na przykład jeśli wcześniej używany warunek lokalizacji użytkownika w zasadach, może się okazać, że zasady są teraz pomijane na podstawie lokalizacji użytkownika.

---

## <a name="february-2019"></a>Luty 2019 r.

### <a name="configurable-azure-ad-saml-token-encryption-public-preview"></a>Konfigurowalne szyfrowanie tokenów SAML usługi Azure AD (publiczna wersja zapoznawcza) 

**Typ:** Nowa funkcja  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwości produktu:** Usługi rejestracji jednokrotnej

Teraz można skonfigurować dowolną obsługiwana aplikację SAML do odbierania zaszyfrowanych tokenów SAML. Po skonfigurowaniu i użyciu z aplikacją usługa Azure AD szyfruje emitowane potwierdzenia SAML przy użyciu klucza publicznego uzyskanego z certyfikatu przechowywanego w usłudze Azure AD.

Aby uzyskać więcej informacji na temat konfigurowania szyfrowania tokenu SAML, zobacz [Konfigurowanie szyfrowania tokenów saml usługi Azure AD.](https://docs.microsoft.com/azure/active-directory/manage-apps/howto-saml-token-encryption)

---

### <a name="create-an-access-review-for-groups-or-apps-using-azure-ad-access-reviews"></a>Tworzenie przeglądu dostępu dla grup lub aplikacji przy użyciu przeglądów usługi Azure AD Access

**Typ:** Nowa funkcja  
**Kategoria usługi:** Recenzje programu Access  
**Możliwości produktu:** Zarządzania

Teraz można dołączyć wiele grup lub aplikacji w jednej recenzji dostępu usługi Azure AD do członkostwa w grupie lub przypisania aplikacji. Przeglądy dostępu z wieloma grupami lub aplikacjami są łączone przy użyciu tych samych ustawień, a wszyscy uwzględnieni recenzenci są powiadamiani w tym samym czasie.

Aby uzyskać więcej informacji na temat tworzenia przeglądu dostępu przy użyciu przeglądów usługi Azure AD Access, zobacz [Tworzenie przeglądu dostępu grup lub aplikacji w przeglądach usługi Azure AD Access](https://docs.microsoft.com/azure/active-directory/governance/create-access-review)

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---february-2019"></a>Nowe aplikacje federacyjne dostępne w galerii aplikacji usługi Azure AD — luty 2019 r.

**Typ:** Nowa funkcja  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwości produktu:** Integracja stron trzecich
 
W lutym 2019 r. dodaliśmy do galerii aplikacji 27 nowych aplikacji z obsługą federacji:

[Paszport Euromonitora](https://docs.microsoft.com/azure/active-directory/saas-apps/euromonitor-passport-tutorial), [MindTickle](https://docs.microsoft.com/azure/active-directory/saas-apps/mindtickle-tutorial), [FAT FINGER](https://seeforgetest-exxon.azurewebsites.net/Account/create?Length=7), [AirStack](https://docs.microsoft.com/azure/active-directory/saas-apps/airstack-tutorial), [Oracle Fusion ERP](https://docs.microsoft.com/azure/active-directory/saas-apps/oracle-fusion-erp-tutorial), [IDrive](https://docs.microsoft.com/azure/active-directory/saas-apps/idrive-tutorial), [Skyward Qmlativ](https://docs.microsoft.com/azure/active-directory/saas-apps/skyward-qmlativ-tutorial), [Brightidea](https://docs.microsoft.com/azure/active-directory/saas-apps/brightidea-tutorial), [AlertOps](https://docs.microsoft.com/azure/active-directory/saas-apps/alertops-tutorial), [Soloinsight-CloudGate SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/soloinsight-cloudgate-sso-tutorial), Kliknij uprawnienie, [Brandfolder](https://docs.microsoft.com/azure/active-directory/saas-apps/brandfolder-tutorial), [StoregateSmartFile](https://docs.microsoft.com/azure/active-directory/saas-apps/smartfile-tutorial), [Pexip](https://docs.microsoft.com/azure/active-directory/saas-apps/pexip-tutorial), [Stormboard](https://docs.microsoft.com/azure/active-directory/saas-apps/stormboard-tutorial), [Sejsmiczne](https://docs.microsoft.com/azure/active-directory/saas-apps/seismic-tutorial), [Share A Dream](https://www.shareadream.org/how-it-works), [Bugsnag](https://docs.microsoft.com/azure/active-directory/saas-apps/bugsnag-tutorial), [webMethods Integration Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/webmethods-integration-cloud-tutorial), Wiedza Wszędzie [LMS](https://docs.microsoft.com/azure/active-directory/saas-apps/knowledge-anywhere-lms-tutorial), [OU Campus](https://docs.microsoft.com/azure/active-directory/saas-apps/ou-campus-tutorial), Dane [peryskopowe](https://docs.microsoft.com/azure/active-directory/saas-apps/periscope-data-tutorial), [Netop Portal](https://docs.microsoft.com/azure/active-directory/saas-apps/netop-portal-tutorial), [smartvid.io](https://docs.microsoft.com/azure/active-directory/saas-apps/smartvid.io-tutorial), [PureCloud przez Genesys](https://docs.microsoft.com/azure/active-directory/saas-apps/purecloud-by-genesys-tutorial), [ClickUp Productivity Platform](https://docs.microsoft.com/azure/active-directory/saas-apps/clickup-productivity-platform-tutorial)

Aby uzyskać więcej informacji na temat aplikacji, zobacz [Integracja aplikacji SaaS z usługą Azure Active Directory](https://aka.ms/appstutorial). Aby uzyskać więcej informacji na temat wyświetlania aplikacji w galerii aplikacji usługi Azure AD, zobacz [Lista aplikacji w galerii aplikacji usługi Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="enhanced-combined-mfasspr-registration"></a>Ulepszona łączna rejestracja usługi MFA/SSPR

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Samoobsługowe resetowanie hasła  
**Możliwości produktu:** Uwierzytelnianie użytkownika

W odpowiedzi na opinie klientów ulepszyliśmy połączone środowisko podglądu rejestracji usługi MFA/SSPR, pomagając użytkownikom szybciej rejestrować informacje zabezpieczające zarówno dla usługi MFA, jak i sspr. 

**Aby włączyć ulepszone środowisko dla użytkowników, wykonaj następujące kroki:**

1. Jako administrator globalny lub administrator użytkownika zaloguj się do witryny Azure portal i przejdź do **usługi Azure Active Directory > ustawieniami użytkownika > Zarządzanie ustawieniami funkcji w wersji zapoznawczej panelu dostępu**. 

2. W **przypadku użytkowników, którzy mogą korzystać z funkcji podglądu do rejestrowania informacji zabezpieczających i zarządzania nimi — opcja odświeżanie,** wybierz opcję włączenia funkcji dla **wybranej grupy użytkowników** lub dla wszystkich **użytkowników**.

W ciągu najbliższych kilku tygodni będziemy usuwać możliwość włączania starego połączonego środowiska podglądu rejestracji UWIERZYTELNIANIA/SSPR dla dzierżawców, którzy jeszcze go nie mają włączoną.

**Aby sprawdzić, czy formant zostanie usunięty dla dzierżawy, wykonaj następujące kroki:**

1. Jako administrator globalny lub administrator użytkownika zaloguj się do witryny Azure portal i przejdź do **usługi Azure Active Directory > ustawieniami użytkownika > Zarządzanie ustawieniami funkcji w wersji zapoznawczej panelu dostępu**.  

2. Jeśli **opcja Użytkownicy, którzy mogą korzystać z funkcji podglądu do rejestrowania informacji zabezpieczających i zarządzania nimi,** jest ustawiona na **Brak,** opcja zostanie usunięta z dzierżawy.

Niezależnie od tego, czy wcześniej włączyłeś stare połączone środowisko podglądu rejestracji usługi MFA/SSPR dla użytkowników, stare środowisko zostanie wyłączone w przyszłości. Z tego powodu zdecydowanie sugerujemy, aby jak najszybciej przejść do nowego, ulepszonego doświadczenia.

Aby uzyskać więcej informacji na temat rozszerzonej rejestracji, zobacz [chłodne ulepszenia połączonego programu MFA usługi Azure AD i procesu rejestracji resetowania hasła.](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Cool-enhancements-to-the-Azure-AD-combined-MFA-and-password/ba-p/354271)

---

### <a name="updated-policy-management-experience-for-user-flows"></a>Zaktualizowano środowisko zarządzania zasadami dla przepływów użytkowników

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** B2C - Zarządzanie tożsamością konsumenta  
**Możliwości produktu:** B2B/B2C

Zaktualizowaliśmy proces tworzenia zasad i zarządzania przepływami użytkowników (wcześniej nazywanymi wbudowanymi zasadami). To nowe środowisko jest teraz domyślne dla wszystkich dzierżaw usługi Azure AD.

Dodatkowe opinie i sugestie można przekazać za pomocą ikon uśmiechu lub marszczenia w obszarze **Wyślij nam opinię** u góry ekranu portalu.

Aby uzyskać więcej informacji na temat nowego środowiska zarządzania zasadami, zobacz [usługi Azure AD B2C ma teraz dostosowanie javascript i wiele innych nowych funkcji](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-now-has-JavaScript-customization-and-many-more-new/ba-p/353595) blogu.

---

### <a name="choose-specific-page-element-versions-provided-by-azure-ad-b2c"></a>Wybieranie określonych wersji elementów strony dostarczanych przez usługę Azure AD B2C

**Typ:** Nowa funkcja  
**Kategoria usługi:** B2C - Zarządzanie tożsamością konsumenta  
**Możliwości produktu:** B2B/B2C

Teraz można wybrać określoną wersję elementów strony dostarczonych przez usługę Azure AD B2C. Wybierając określoną wersję, można przetestować aktualizacje, zanim pojawią się na stronie i można uzyskać przewidywalne zachowanie. Ponadto możesz teraz zdecydować się na wymuszanie określonych wersji stron, aby zezwolić na dostosowania javascript. Aby włączyć tę funkcję, przejdź do strony Właściwości w **przepływach** użytkownika.

Aby uzyskać więcej informacji na temat wybierania określonych wersji elementów strony, zobacz [usługi Azure AD B2C ma teraz javascript dostosowywania i wiele innych nowych funkcji](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-now-has-JavaScript-customization-and-many-more-new/ba-p/353595) blogu.

---

### <a name="configurable-end-user-password-requirements-for-b2c-ga"></a>Konfigurowalne wymagania dotyczące haseł użytkowników końcowych dla B2C (GA)

**Typ:** Nowa funkcja  
**Kategoria usługi:** B2C - Zarządzanie tożsamością konsumenta  
**Możliwości produktu:** B2B/B2C

Teraz można skonfigurować złożoność haseł organizacji dla użytkowników końcowych, zamiast używać natywnej zasady haseł usługi Azure AD. Z właściwości bloku **przepływów** użytkownika (wcześniej znany jako wbudowane zasady), można wybrać złożoność hasła **proste** lub **silne**, lub można utworzyć **niestandardowy** zestaw wymagań.

Aby uzyskać więcej informacji na temat konfiguracji wymagań dotyczących złożoności hasła, zobacz [Konfigurowanie wymagań dotyczących złożoności haseł w usłudze Azure Active Directory B2C](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-password-complexity).

---

### <a name="new-default-templates-for-custom-branded-authentication-experiences"></a>Nowe szablony domyślne dla niestandardowych środowisk uwierzytelniania uwierzytelniania uwierzytelniania uwierzytelniania

**Typ:** Nowa funkcja  
**Kategoria usługi:** B2C - Zarządzanie tożsamością konsumenta  
**Możliwości produktu:** B2B/B2C

Możesz użyć naszych nowych szablonów domyślnych, znajdujących się w bloku **Układy stron przepływów** użytkownika (wcześniej nazywanych wbudowanymi zasadami), aby utworzyć niestandardowe środowisko uwierzytelniania markowego dla użytkowników.

Aby uzyskać więcej informacji na temat korzystania z szablonów, zobacz [Usługa Azure AD B2C ma teraz dostosowanie javascript i wiele innych nowych funkcji](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-now-has-JavaScript-customization-and-many-more-new/ba-p/353595).

---

## <a name="january-2019"></a>Styczeń 2019 r.

### <a name="active-directory-b2b-collaboration-using-one-time-passcode-authentication-public-preview"></a>Współpraca b2b usługi Active Directory przy użyciu jednorazowego uwierzytelniania kodem dostępu (publiczna wersja zapoznawcza)

**Typ:** Nowa funkcja  
**Kategoria usługi:** B2b  
**Możliwości produktu:** B2B/B2C

Wprowadziliśmy jednorazowe uwierzytelnianie kodów dostępu (OTP) dla użytkowników-gości B2B, którzy nie mogą być uwierzytelnieni za pomocą innych środków, takich jak usługa Azure AD, konto Microsoft (MSA) lub federacja Google. Ta nowa metoda uwierzytelniania oznacza, że użytkownicy-goście nie muszą tworzyć nowego konta Microsoft. Zamiast tego podczas realizacji zaproszenia lub uzyskiwania dostępu do zasobu udostępnionego użytkownik-gość może zażądać wysłania kodu tymczasowego na adres e-mail. Za pomocą tego kodu tymczasowego, użytkownik-gość może nadal się logować.

Aby uzyskać więcej informacji, zobacz [Uwierzytelnianie jednorazowym kodem dostępu (wersja zapoznawcza) poczty e-mail](https://docs.microsoft.com/azure/active-directory/b2b/one-time-passcode) i blog usługi [Azure AD sprawia, że udostępnianie i współpraca są bezproblemowe dla każdego użytkownika z dowolnym kontem.](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-makes-sharing-and-collaboration-seamless-for-any-user/ba-p/325949)

### <a name="new-azure-ad-application-proxy-cookie-settings"></a>Nowe ustawienia plików cookie serwera proxy aplikacji usługi Azure AD

**Typ:** Nowa funkcja  
**Kategoria usługi:** Serwer proxy aplikacji  
**Możliwości produktu:** Kontrola dostępu

Wprowadziliśmy trzy nowe ustawienia plików cookie, dostępne dla aplikacji publikowanych za pośrednictwem serwera proxy aplikacji:

- **Użyj pliku cookie tylko http.** Ustawia flagę **HTTPOnly** w plikach cookie dostępu serwera proxy aplikacji i sesji. Włączenie tego ustawienia zapewnia dodatkowe korzyści w zakresie bezpieczeństwa, takie jak zapobieganie kopiowaniu lub modyfikowaniu plików cookie za pomocą skryptów po stronie klienta. Zalecamy włączenie tej flagi (wybierz **tak)** dla dodatkowych korzyści.

- **Użyj bezpiecznego pliku cookie.** Ustawia flagę **Bezpieczne** pliki cookie dostępu serwera proxy aplikacji i sesji. Włączenie tego ustawienia zapewnia dodatkowe korzyści w zakresie bezpieczeństwa, upewniając się, że pliki cookie są przesyłane tylko za pośrednictwem kanałów bezpiecznych TLS, takich jak HTTPS. Zalecamy włączenie tej flagi (wybierz **tak)** dla dodatkowych korzyści.

- **Użyj trwałego pliku cookie.** Zapobiega wygasaniu plików cookie dostępu po zamknięciu przeglądarki internetowej. Te pliki cookie trwają przez cały okres istnienia tokenu dostępu. Jednak pliki cookie są resetowane po osiągnięciu czasu wygaśnięcia lub jeśli użytkownik ręcznie usuwa plik cookie. Zalecamy zachowanie domyślnego ustawienia **Nie**, włączanie tylko ustawienia dla starszych aplikacji, które nie udostępniają plików cookie między procesami.

Aby uzyskać więcej informacji na temat nowych plików cookie, zobacz [Ustawienia plików cookie dotyczące uzyskiwania dostępu do aplikacji lokalnych w usłudze Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-cookie-settings).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---january-2019"></a>Nowe aplikacje federacyjne dostępne w galerii aplikacji usługi Azure AD — styczeń 2019 r.

**Typ:** Nowa funkcja  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwości produktu:** Integracja stron trzecich
 
W styczniu 2019 r. dodaliśmy do galerii aplikacji 35 nowych aplikacji z obsługą federacji:

[Firstbird](https://docs.microsoft.com/azure/active-directory/saas-apps/firstbird-tutorial), [Folloze](https://docs.microsoft.com/azure/active-directory/saas-apps/folloze-tutorial), [Talent Palette](https://docs.microsoft.com/azure/active-directory/saas-apps/talent-palette-tutorial), [Infor CloudSuite](https://docs.microsoft.com/azure/active-directory/saas-apps/infor-cloud-suite-tutorial), [Cisco Umbrella](https://docs.microsoft.com/azure/active-directory/saas-apps/cisco-umbrella-tutorial), [Zscaler Internet Access Administrator](https://docs.microsoft.com/azure/active-directory/saas-apps/zscaler-internet-access-administrator-tutorial), Przypomnienie [wygaśnięcia](https://docs.microsoft.com/azure/active-directory/saas-apps/expiration-reminder-tutorial), [InstaVR Viewer](https://docs.microsoft.com/azure/active-directory/saas-apps/instavr-viewer-tutorial), [CorpTax](https://docs.microsoft.com/azure/active-directory/saas-apps/corptax-tutorial), [Czasownik](https://app.verb.net/login), [OpenLattice](https://openlattice.com/agora), [TheOrgWiki](https://www.theorgwiki.com/signup), [Pavaso Digital Close](https://docs.microsoft.com/azure/active-directory/saas-apps/pavaso-digital-close-tutorial), [GoodPractice Toolkit](https://docs.microsoft.com/azure/active-directory/saas-apps/goodpractice-toolkit-tutorial), Cloud Service [PICCO](https://docs.microsoft.com/azure/active-directory/saas-apps/cloud-service-picco-tutorial), [AuditBoard](https://docs.microsoft.com/azure/active-directory/saas-apps/auditboard-tutorial), [iProva](https://docs.microsoft.com/azure/active-directory/saas-apps/iprova-tutorial), [wykonalne](https://docs.microsoft.com/azure/active-directory/saas-apps/workable-tutorial) [, CallPlease](https://webapp.callplease.com/create-account/create-account.html), [GTNexus SSO System](https://docs.microsoft.com/azure/active-directory/saas-apps/gtnexus-sso-module-tutorial), [CBRE ServiceInsight](https://docs.microsoft.com/azure/active-directory/saas-apps/cbre-serviceinsight-tutorial), [Deskradar](https://docs.microsoft.com/azure/active-directory/saas-apps/deskradar-tutorial), [Coralogixv](https://docs.microsoft.com/azure/active-directory/saas-apps/coralogix-tutorial), [Signagelive](https://docs.microsoft.com/azure/active-directory/saas-apps/signagelive-tutorial), [ARES dla przedsiębiorstw](https://docs.microsoft.com/azure/active-directory/saas-apps/ares-for-enterprise-tutorial), [K2 dla Office 365](https://www.k2.com/O365), [Xledger](https://www.xledger.net/), [iDiD Manager](https://docs.microsoft.com/azure/active-directory/saas-apps/idid-manager-tutorial), [HighGear](https://docs.microsoft.com/azure/active-directory/saas-apps/highgear-tutorial), [Visitly](https://docs.microsoft.com/azure/active-directory/saas-apps/visitly-tutorial), [Korn Ferry ALP](https://docs.microsoft.com/azure/active-directory/saas-apps/korn-ferry-alp-tutorial), [Acadia](https://docs.microsoft.com/azure/active-directory/saas-apps/acadia-tutorial), [Adoddle cSaas Platform](https://docs.microsoft.com/azure/active-directory/saas-apps/adoddle-csaas-platform-tutorial)<!-- , [CaféX Portal (Meetings)](https://docs.microsoft.com/azure/active-directory/saas-apps/cafexportal-meetings-tutorial), [MazeMap Link](https://docs.microsoft.com/azure/active-directory/saas-apps/mazemaplink-tutorial)-->  

Aby uzyskać więcej informacji na temat aplikacji, zobacz [Integracja aplikacji SaaS z usługą Azure Active Directory](https://aka.ms/appstutorial). Aby uzyskać więcej informacji na temat wyświetlania aplikacji w galerii aplikacji usługi Azure AD, zobacz [Lista aplikacji w galerii aplikacji usługi Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="new-azure-ad-identity-protection-enhancements-public-preview"></a>Nowe ulepszenia usługi Azure AD Identity Protection (publiczna wersja zapoznawcza)

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Ochrona tożsamości  
**Możliwości produktu:** Ochrona & zabezpieczeń tożsamości

Z przyjemnością informujemy, że dodaliśmy następujące ulepszenia do publicznej oferty w wersji zapoznawczej usługi Azure AD Identity Protection, w tym:

- Zaktualizowany i bardziej zintegrowany interfejs użytkownika

- Dodatkowe interfejsy API

- Ulepszona ocena ryzyka dzięki uczeniu maszynowemu

- Dostosowanie całego produktu do ryzykownych użytkowników i ryzykownych logów

Aby uzyskać więcej informacji na temat ulepszeń, zobacz [Co to jest ochrona tożsamości usługi Azure Active Directory (odświeżona)?](https://aka.ms/IdentityProtectionDocs) aby dowiedzieć się więcej i podzielić się swoimi przemyśleniami za pośrednictwem monitów w produkcie.

---

### <a name="new-app-lock-feature-for-the-microsoft-authenticator-app-on-ios-and-android-devices"></a>Nowa funkcja blokady aplikacji dla aplikacji Microsoft Authenticator na urządzeniach z systemem iOS i Android

**Typ:** Nowa funkcja  
**Kategoria usługi:** Aplikacja Microsoft Authenticator  
**Możliwości produktu:** Ochrona & zabezpieczeń tożsamości

Aby zapewnić bezpieczeństwo jednorazowych kodów dostępu, informacji o aplikacji i ustawień aplikacji, możesz włączyć funkcję Blokada aplikacji w aplikacji Microsoft Authenticator. Włączenie blokady aplikacji oznacza, że podczas otwierania aplikacji Microsoft Authenticator zostaniesz poproszony o uwierzytelnienie przy użyciu kodu PIN lub danych biometrycznych.

Aby uzyskać więcej informacji, zobacz często zadawane [pytania dotyczące aplikacji Microsoft Authenticator](https://docs.microsoft.com/azure/active-directory/user-help/microsoft-authenticator-app-faq).

---

### <a name="enhanced-azure-ad-privileged-identity-management-pim-export-capabilities"></a>Rozszerzone możliwości eksportowania usługi Azure AD Privileged Identity Management (PIM)

**Typ:** Nowa funkcja  
**Kategoria usługi:** Zarządzanie tożsamościami uprzywilejowanymi  
**Możliwości produktu:** Zarządzanie tożsamościami uprzywilejowanymi

Administratorzy zarządzania tożsamościami uprzywilejowanymi (PIM) mogą teraz eksportować wszystkie aktywne i kwalifikujące się przypisania ról dla określonego zasobu, który obejmuje przypisania ról dla wszystkich zasobów podrzędnych. Wcześniej administratorom trudno było uzyskać pełną listę przypisań ról dla subskrypcji i musieli eksportować przypisania ról dla każdego określonego zasobu.

Aby uzyskać więcej informacji, zobacz [Wyświetlanie historii działań i inspekcji ról zasobów platformy Azure w usłudze PIM](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/azure-pim-resource-rbac).

---

## <a name="novemberdecember-2018"></a>Listopad/grudzień 2018 r.

### <a name="users-removed-from-synchronization-scope-no-longer-switch-to-cloud-only-accounts"></a>Użytkownicy usunięci z zakresu synchronizacji nie przełączają się już na konta tylko w chmurze

**Typ:** Stałe  
**Kategoria usługi:** Zarządzanie użytkownikami  
**Możliwości produktu:** Katalogu

>[!Important]
>Słyszeliśmy i rozumiemy twoją frustrację z powodu tej poprawki. W związku z tym firma You've cofnięte tej zmiany do czasu, że możemy ułatwić poprawkę do wdrożenia w organizacji.

Naprawiliśmy błąd, w którym flaga DirSyncEnabled użytkownika zostanie błędnie przełączona na **False,** gdy obiekt Usług domenowych Active Directory (AD DS) został wykluczony z zakresu synchronizacji, a następnie przeniesiony do Kosza w usłudze Azure AD w następnym cyklu synchronizacji. W wyniku tej poprawki, jeśli użytkownik jest wykluczony z zakresu synchronizacji, a następnie przywrócone z Kosza usługi Azure AD, konto użytkownika pozostaje zsynchronizowany z lokalnej usługi AD, zgodnie z oczekiwaniami i nie można zarządzać w chmurze, ponieważ jego źródło urzędu (SoA) pozostaje jako lokalna usługa AD.

Przed tą poprawką wystąpił problem, gdy flaga DirSyncEnabled została przełączona na False. To dało błędne wrażenie, że te konta zostały przekonwertowane na obiekty tylko w chmurze i że konta mogą być zarządzane w chmurze. Jednak konta nadal przechowywane ich SoA jako lokalnie i wszystkie zsynchronizowane właściwości (atrybuty cienia) pochodzących z lokalnej usługi AD. Ten warunek spowodował wiele problemów w usłudze Azure AD i innych obciążeń w chmurze (takich jak Exchange Online), które powinny traktować te konta jako zsynchronizowane z usługi AD, ale teraz zachowują się jak konta tylko w chmurze.

W tej chwili jedynym sposobem, aby prawdziwie przekonwertować zsynchronizowane z konta usługi AD do konta tylko w chmurze jest wyłączenie DirSync na poziomie dzierżawy, który wyzwala operację wewnętrznej bazy danych do transferu SoA. Ten typ zmiany SoA wymaga (ale nie ogranicza się do) czyszczenie wszystkich lokalnych atrybutów powiązanych (takich jak LastDirSyncTime i atrybuty cienia) i wysyłania sygnału do innych obciążeń w chmurze, aby jego odpowiedni obiekt został przekonwertowany na konto tylko w chmurze.

Ta poprawka w związku z tym zapobiega bezpośrednie aktualizacje na ImmutableID atrybut użytkownika zsynchronizowane z usługi AD, które w niektórych scenariuszach w przeszłości były wymagane. Zgodnie z projektem ImmutableID obiektu w usłudze Azure AD, jak sama nazwa wskazuje, ma być niezmienne. Nowe funkcje zaimplementowane w usłudze Azure AD Connect Health i klient synchronizacji usługi Azure AD Connect są dostępne w celu rozwiązania takich scenariuszy:

- **Aktualizacja ImmutableID na dużą skalę dla wielu użytkowników w podejściu etapowym**
  
  Na przykład należy wykonać długą migrację między lasami usług AD DS. Rozwiązanie: Użyj usługi Azure AD Connect, aby **skonfigurować kotwicę źródłową** i podczas migracji użytkownika skopiuj istniejące wartości ImmutableID z usługi Azure AD do atrybutu ms-DS-Consistency-Guid lokalnego użytkownika usług AD DS nowego lasu. Aby uzyskać więcej informacji, zobacz [Korzystanie z ms-DS-ConsistencyGuid jako sourceAnchor](/azure/active-directory/hybrid/plan-connect-design-concepts#using-ms-ds-consistencyguid-as-sourceanchor).

- **Aktualizacje OnemutableID na dużą skalę dla wielu użytkowników w jednym ujęciu**

  Na przykład podczas implementowania usługi Azure AD Connect popełnisz błąd, a teraz musisz zmienić atrybut SourceAnchor. Rozwiązanie: Wyłącz dirsync na poziomie dzierżawy i wyczyść wszystkie nieprawidłowe wartości ImmutableID. Aby uzyskać więcej informacji, zobacz [Wyłączanie synchronizacji katalogów dla usługi Office 365](/office365/enterprise/turn-off-directory-synchronization).

- **Rewanż użytkownika lokalnego z istniejącym użytkownikiem w usłudze Azure AD** Na przykład użytkownik, który został ponownie utworzony w usługach AD DS generuje duplikat na koncie usługi Azure AD zamiast reneuzyzowania go z istniejącym kontem usługi Azure AD (obiekt oddzielony). Rozwiązanie: Użyj usługi Azure AD Connect Health w witrynie Azure portal, aby ponownie zamapować źródło Anchor/ImmutableID. Aby uzyskać więcej informacji, zobacz [Scenariusz osieroconego obiektu](/azure/active-directory/hybrid/how-to-connect-health-diagnose-sync-errors#orphaned-object-scenario).

### <a name="breaking-change-updates-to-the-audit-and-sign-in-logs-schema-through-azure-monitor"></a>Breaking Change: Aktualizacje schematu inspekcji i logowania dzienników za pośrednictwem usługi Azure Monitor

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Reporting  
**Możliwości produktu:** Monitorowanie & raportowania

Obecnie publikujemy strumienie dziennika inspekcji i logowania za pośrednictwem usługi Azure Monitor, dzięki czemu można bezproblemowo zintegrować pliki dziennika z narzędziami SIEM lub usługą Log Analytics. Na podstawie opinii i w ramach przygotowań do tego funkcji ogólne ogłoszenie o dostępności, wprowadzamy następujące zmiany w naszym schemacie. Te zmiany schematu i związane z nimi aktualizacje dokumentacji nastąpią w pierwszym tygodniu stycznia.

#### <a name="new-fields-in-the-audit-schema"></a>Nowe pola w schemacie inspekcji
Dodajemy nowe pole **Typ operacji,** aby zapewnić typ operacji wykonywanej na zasobie. Na przykład **Dodaj**, **Aktualizuj**lub **Usuń**.

#### <a name="changed-fields-in-the-audit-schema"></a>Zmienione pola w schemacie inspekcji
W schemacie inspekcji zmieniają się następujące pola:

|Nazwa pola|Co się zmieniło|Stare wartości|Nowe wartości|
|----------|------------|----------|----------|
|Kategoria|Było to pole **Nazwa usługi.** Teraz jest to pole **Kategorie inspekcji.** **Nazwa usługi** została zmieniona na pole **loggedByService.**|<ul><li>Aprowizacja kont</li><li>Katalog podstawowy</li><li>Samoobsługowe resetowanie hasła</li></ul>|<ul><li>Zarządzanie użytkownikami</li><li>Zarządzanie grupami</li><li>Zarządzanie aplikacjami</li></ul>|
|targetResources|Zawiera **TargetResourceType** na najwyższym poziomie.|&nbsp;|<ul><li>Zasady</li><li>Aplikacja</li><li>Użytkownik</li><li>Grupa</li></ul>|
|usługaloggedByService|Zawiera nazwę usługi, która wygenerowała dziennik inspekcji.|Null|<ul><li>Aprowizacja kont</li><li>Katalog podstawowy</li><li>Samoobsługowe resetowanie haseł</li></ul>|
|Wynik|Zawiera wyniki dzienników inspekcji. Wcześniej zostało to wyliczone, ale teraz pokazujemy rzeczywistą wartość.|<ul><li>0</li><li>1</li></ul>|<ul><li>Powodzenie</li><li>Niepowodzenie</li></ul>|

#### <a name="changed-fields-in-the-sign-in-schema"></a>Zmienione pola w schemacie logowania
W schemacie logowania zmieniają się następujące pola:

|Nazwa pola|Co się zmieniło|Stare wartości|Nowe wartości|
|----------|------------|----------|----------|
|appliedConditionalAccessPolicies|To było **warunkowealeksalekspolityka.** Teraz jest **to pole AppliedConditionalAccessPolicies.**|Bez zmian|Bez zmian|
|warunkowy StatusAccess|Zapewnia wynik stanu zasad dostępu warunkowego podczas logowania. Wcześniej zostało to wyliczone, ale teraz pokazujemy rzeczywistą wartość.|<ul><li>0</li><li>1</li><li>2</li><li>3</li></ul>|<ul><li>Powodzenie</li><li>Niepowodzenie</li><li>Nieuwzłosz, nie jest stosowany</li><li>Disabled (Wyłączony)</li></ul>|
|appliedConditionalAccessPolicies: result|Zapewnia wynik indywidualnego stanu zasad dostępu warunkowego podczas logowania. Wcześniej zostało to wyliczone, ale teraz pokazujemy rzeczywistą wartość.|<ul><li>0</li><li>1</li><li>2</li><li>3</li></ul>|<ul><li>Powodzenie</li><li>Niepowodzenie</li><li>Nieuwzłosz, nie jest stosowany</li><li>Disabled (Wyłączony)</li></ul>|

Aby uzyskać więcej informacji na temat schematu, zobacz [Interpretowanie schematu dzienników inspekcji usługi Azure AD w usłudze Azure Monitor (wersja zapoznawcza)](https://docs.microsoft.com/azure/active-directory/reports-monitoring/reference-azure-monitor-audit-log-schema)

---

### <a name="identity-protection-improvements-to-the-supervised-machine-learning-model-and-the-risk-score-engine"></a>Ulepszenia ochrony tożsamości w nadzorowanym modelu uczenia maszynowego i silniku oceny ryzyka

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Ochrona tożsamości  
**Możliwości produktu:** Oceny ryzyka

Ulepszenia aparatu oceny ryzyka związanego z ochroną tożsamości i logowania mogą przyczynić się do zwiększenia dokładności i zasięgu ryzyka użytkownika. Administratorzy mogą zauważyć, że poziom ryzyka użytkownika nie jest już bezpośrednio związany z poziomem ryzyka określonych wykrywania i że wzrasta liczba i poziom ryzykownych zdarzeń logowania.

Wykrywanie ryzyka są teraz oceniane przez nadzorowany model uczenia maszynowego, który oblicza ryzyko użytkownika przy użyciu dodatkowych funkcji logowania użytkownika i wzorca wykrywania. Na podstawie tego modelu administrator może znaleźć użytkowników z wysokimi wynikami ryzyka, nawet jeśli wykrywania skojarzone z tym użytkownikiem są niskiego lub średniego ryzyka. 

---

### <a name="administrators-can-reset-their-own-password-using-the-microsoft-authenticator-app-public-preview"></a>Administratorzy mogą resetować własne hasło za pomocą aplikacji Microsoft Authenticator (publiczna wersja zapoznawcza)

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Samoobsługowe resetowanie hasła  
**Możliwości produktu:** Uwierzytelnianie użytkownika

Administratorzy usługi Azure AD mogą teraz resetować własne hasło przy użyciu powiadomień aplikacji Microsoft Authenticator lub kodu z dowolnej aplikacji uwierzytelniającego urządzenia przenośnego lub tokenu sprzętowego. Aby zresetować własne hasło, administratorzy będą mogli korzystać z dwóch z następujących metod:

- Powiadomienie o aplikacji Microsoft Authenticator

- Inna mobilna aplikacja uwierzytelniacza / kod tokenu sprzętowego

- Adres e-mail

- Połączenie telefoniczne

- Wiadomość SMS

Aby uzyskać więcej informacji na temat resetowania haseł przy użyciu aplikacji Microsoft Authenticator, zobacz [samoobsługowe resetowanie haseł usługi Azure AD — aplikacja mobilna i samoobsługowe (w wersji zapoznawczej)](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-howitworks#mobile-app-and-sspr)

---

### <a name="new-azure-ad-cloud-device-administrator-role-public-preview"></a>Nowa rola administratora urządzeń w chmurze usługi Azure AD (publiczna wersja zapoznawcza)

**Typ:** Nowa funkcja  
**Kategoria usługi:** Rejestracja i zarządzanie urządzeniami  
**Możliwości produktu:** Kontrola dostępu

Administratorzy mogą przypisywać użytkowników do nowej roli Administratora urządzeń w chmurze w celu wykonywania zadań administratora urządzeń w chmurze. Użytkownicy, do jakiej przypisano rolę Administratorzy urządzeń w chmurze, mogą włączać, wyłączać i usuwać urządzenia w usłudze Azure AD, a także mogą odczytywać klucze funkcji BitLocker systemu Windows 10 (jeśli są obecne) w witrynie Azure portal.

Aby uzyskać więcej informacji o rolach i uprawnieniach, zobacz [Przypisywanie ról administratora w usłudze Azure Active Directory](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)

---

### <a name="manage-your-devices-using-the-new-activity-timestamp-in-azure-ad-public-preview"></a>Zarządzanie urządzeniami przy użyciu nowego sygnatury czasowej aktywności w usłudze Azure AD (publiczna wersja zapoznawcza)

**Typ:** Nowa funkcja  
**Kategoria usługi:** Rejestracja i zarządzanie urządzeniami  
**Możliwości produktu:** Zarządzanie cyklem życia urządzenia

Zdajemy sobie sprawę, że z czasem należy odświeżyć i wycofać urządzenia organizacji w usłudze Azure AD, aby uniknąć starych urządzeń w twoim środowisku. Aby ułatwić ten proces, usługa Azure AD aktualizuje teraz urządzenia za pomocą nowego sygnatury czasowej aktywności, co ułatwia zarządzanie cyklem życia urządzenia.

Aby uzyskać więcej informacji na temat sposobu uzyskania i używania tej sygnatury czasowej, zobacz [Jak: Zarządzanie przestarzałymi urządzeniami w usłudze Azure AD](https://docs.microsoft.com/azure/active-directory/devices/manage-stale-devices)

---

### <a name="administrators-can-require-users-to-accept-a-terms-of-use-on-each-device"></a>Administratorzy mogą wymagać od użytkowników zaakceptowania warunków użytkowania na każdym urządzeniu

**Typ:** Nowa funkcja  
**Kategoria usługi:** Warunki użytkowania  
**Możliwości produktu:** Zarządzania
 
Administratorzy mogą teraz włączyć opcję **Wymagaj od użytkowników zgody na każde urządzenie,** aby wymagać od użytkowników zaakceptowania warunków użytkowania na każdym urządzeniu, którego używają w dzierżawie.

Aby uzyskać więcej informacji, zobacz [sekcję Warunki użytkowania na urządzeniu funkcji warunków użytkowania usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use#per-device-terms-of-use).

---

### <a name="administrators-can-configure-a-terms-of-use-to-expire-based-on-a-recurring-schedule"></a>Administratorzy mogą skonfigurować warunki użytkowania, które wygasają na podstawie harmonogramu cyklicznego

**Typ:** Nowa funkcja  
**Kategoria usługi:** Warunki użytkowania  
**Możliwości produktu:** Zarządzania
 

Administratorzy mogą teraz włączyć opcję **Wygasaj zgody,** aby warunki użytkowania wygasły dla wszystkich użytkowników na podstawie określonego harmonogramu cyklicznego. Harmonogram może być co roku, dwa razy w roku, kwartalnie lub co miesiąc. Po wygaśnięciu warunków użytkowania użytkownicy muszą ponownie się z tym pogodzić.

Aby uzyskać więcej informacji, zobacz [sekcję Dodawanie warunków użytkowania funkcji warunków użytkowania usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use#add-terms-of-use).

---

### <a name="administrators-can-configure-a-terms-of-use-to-expire-based-on-each-users-schedule"></a>Administratorzy mogą skonfigurować warunki użytkowania, które wygasają zgodnie z harmonogramem każdego użytkownika

**Typ:** Nowa funkcja  
**Kategoria usługi:** Warunki użytkowania  
**Możliwości produktu:** Zarządzania

Administratorzy mogą teraz określić czas trwania, który użytkownik musi ponownie przyśpić warunki użytkowania. Na przykład administratorzy mogą określić, że użytkownicy muszą ponownie przywracać warunki użytkowania co 90 dni.

Aby uzyskać więcej informacji, zobacz [sekcję Dodawanie warunków użytkowania funkcji warunków użytkowania usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use#add-terms-of-use).
 
---

### <a name="new-azure-ad-privileged-identity-management-pim-emails-for-azure-active-directory-roles"></a>Nowe wiadomości e-mail dotyczące zarządzania tożsamościami uprzywilejowanymi usługą Azure AD (PIM) dla ról usługi Azure Active Directory

**Typ:** Nowa funkcja  
**Kategoria usługi:** Zarządzanie tożsamościami uprzywilejowanymi  
**Możliwości produktu:** Zarządzanie tożsamościami uprzywilejowanymi
 
Klienci korzystający z usługi Azure AD Privileged Identity Management (PIM) mogą teraz otrzymywać cotygodniową wiadomość e-mail o skrótach, w tym następujące informacje dotyczące ostatnich siedmiu dni:

- Przegląd najważniejszych kwalifikujących się i stałych przypisań ról

- Liczba użytkowników aktywujących role

- Liczba użytkowników przypisanych do ról w ułowionej funkcji

- Liczba użytkowników przypisanych do ról poza pim

- Liczba użytkowników "na stałe" w PIM

Aby uzyskać więcej informacji na temat usługi PIM i dostępnych powiadomień e-mail, zobacz [Powiadomienia e-mail w pim](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-email-notifications).

---

### <a name="group-based-licensing-is-now-generally-available"></a>Licencjonowanie grupowe jest teraz ogólnie dostępne

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Innych  
**Możliwości produktu:** Katalogu

Licencjonowanie oparte na grupach jest poza publiczną wersję zapoznawczą i jest teraz ogólnie dostępne. W ramach tej ogólnej wersji uczyniliśmy tę funkcję bardziej skalowalną i dodaliśmy możliwość ponownego przetwarzania przypisań licencjonowania opartych na grupach dla pojedynczego użytkownika oraz możliwość korzystania z licencjonowania grupowego z licencjami usługi Office 365 E3/A3.

Aby uzyskać więcej informacji na temat licencjonowania opartego na grupach, zobacz [Co to jest licencjonowanie oparte na grupach w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-licensing-whatis-azure-portal)

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---november-2018"></a>Nowe aplikacje federacyjne dostępne w galerii aplikacji usługi Azure AD — listopad 2018 r.

**Typ:** Nowa funkcja  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwości produktu:** Integracja stron trzecich
 
W listopadzie 2018 r. dodaliśmy do galerii aplikacji 26 nowych aplikacji z obsługą federacji:

[CoreStack](https://cloud.corestack.io/site/login), [HubSpot](https://docs.microsoft.com/azure/active-directory/saas-apps/HubSpot-tutorial), [GetThere](https://docs.microsoft.com/azure/active-directory/saas-apps/getthere-tutorial), [Gra-Pe](https://docs.microsoft.com/azure/active-directory/saas-apps/grape-tutorial), [eHour](https://getehour.com/try-now), [Consent2Go](https://docs.microsoft.com/azure/active-directory/saas-apps/Consent2Go-tutorial), [Appinux](https://docs.microsoft.com/azure/active-directory/saas-apps/appinux-tutorial), [DriveDollar](https://azuremarketplace.microsoft.com/marketplace/apps/savitas.drivedollar-azuread?tab=Overview), [Useall](https://docs.microsoft.com/azure/active-directory/saas-apps/useall-tutorial), [Infinite Campus](https://docs.microsoft.com/azure/active-directory/saas-apps/infinitecampus-tutorial), [Alaya](https://alayagood.com/en/demo/), [HeyBuddy](https://docs.microsoft.com/azure/active-directory/saas-apps/heybuddy-tutorial), [Wrike SAML](https://docs.microsoft.com/azure/active-directory/saas-apps/wrike-tutorial), [Drift](https://docs.microsoft.com/azure/active-directory/saas-apps/drift-tutorial), [Zenegy for Business Central 365](https://accounting.zenegy.com/), [Everbridge Portal członkowski](https://docs.microsoft.com/azure/active-directory/saas-apps/everbridge-tutorial), [IDEO](https://profile.ideo.com/users/sign_up), [Ivanti Service Manager (ISM)](https://docs.microsoft.com/azure/active-directory/saas-apps/ivanti-service-manager-tutorial), [Peakon](https://docs.microsoft.com/azure/active-directory/saas-apps/peakon-tutorial), [Allbound SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/allbound-sso-tutorial), [Plex Apps - Classic Test](https://test.plexonline.com/signon), [Aplikacje Plex - Classic](https://www.plexonline.com/signon), [Aplikacje Plex - UX Test](https://test.cloud.plex.com/sso), [Plex Apps - UX](https://cloud.plex.com/sso), [Plex Apps - IAM](https://accounts.plex.com/), [CRAFTS - rekordy opieki nad dziećmi, frekwencja, & system śledzenia finansowego](https://getcrafts.ca/craftsregistration) 

Aby uzyskać więcej informacji na temat aplikacji, zobacz [Integracja aplikacji SaaS z usługą Azure Active Directory](https://aka.ms/appstutorial). Aby uzyskać więcej informacji na temat wyświetlania aplikacji w galerii aplikacji usługi Azure AD, zobacz [Lista aplikacji w galerii aplikacji usługi Azure Active Directory](https://aka.ms/azureadapprequest).

---

## <a name="october-2018"></a>Październik 2018 r.

### <a name="azure-ad-logs-now-work-with-azure-log-analytics-public-preview"></a>Dzienniki usługi Azure AD współpracują teraz z usługą Azure Log Analytics (publiczna wersja zapoznawcza)

**Typ:** Nowa funkcja  
**Kategoria usługi:** Reporting  
**Możliwości produktu:** Monitorowanie & raportowania

Z przyjemnością informujemy, że możesz teraz przesyłać dalej swoje dzienniki usługi Azure AD do usługi Azure Log Analytics! Ta najchętniej żądana funkcja zapewnia jeszcze lepszy dostęp do analiz dla twojej firmy, operacji i zabezpieczeń, a także sposób na monitorowanie infrastruktury. Aby uzyskać więcej informacji, zobacz [dzienniki aktywności usługi Azure Active Directory w usłudze Azure Log Analytics teraz dostępne](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-Active-Directory-Activity-logs-in-Azure-Log-Analytics-now/ba-p/274843) w blogu.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---october-2018"></a>Nowe aplikacje federacyjne dostępne w galerii aplikacji usługi Azure AD — październik 2018 r.

**Typ:** Nowa funkcja  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwości produktu:** Integracja stron trzecich

W październiku 2018 r. dodaliśmy do galerii aplikacji 14 nowych aplikacji z obsługą federacji:

[My Award Points](https://docs.microsoft.com/azure/active-directory/saas-apps/myawardpoints-tutorial), [Vibe HCM](https://docs.microsoft.com/azure/active-directory/saas-apps/vibehcm-tutorial), ambyint , [MyWorkDrive](https://docs.microsoft.com/azure/active-directory/saas-apps/myworkdrive-tutorial), [BorrowBox](https://docs.microsoft.com/azure/active-directory/saas-apps/borrowbox-tutorial), Dialpad , [ON24 Virtual Environment](https://docs.microsoft.com/azure/active-directory/saas-apps/on24-tutorial), [RingCentral](https://docs.microsoft.com/azure/active-directory/saas-apps/ringcentral-tutorial), [Zscaler Trzy](https://docs.microsoft.com/azure/active-directory/saas-apps/zscaler-three-tutorial), [Phraseanet](https://docs.microsoft.com/azure/active-directory/saas-apps/phraseanet-tutorial), [Ocena](https://docs.microsoft.com/azure/active-directory/saas-apps/appraisd-tutorial), [Workspot Control](https://docs.microsoft.com/azure/active-directory/saas-apps/workspotcontrol-tutorial), [Shuccho Navi](https://docs.microsoft.com/azure/active-directory/saas-apps/shucchonavi-tutorial), [Glassfrog](https://docs.microsoft.com/azure/active-directory/saas-apps/glassfrog-tutorial)

Aby uzyskać więcej informacji na temat aplikacji, zobacz [Integracja aplikacji SaaS z usługą Azure Active Directory](https://aka.ms/appstutorial). Aby uzyskać więcej informacji na temat wyświetlania aplikacji w galerii aplikacji usługi Azure AD, zobacz [Lista aplikacji w galerii aplikacji usługi Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="azure-ad-domain-services-email-notifications"></a>Powiadomienia e-mail usług domenowych usługi Azure AD

**Typ:** Nowa funkcja  
**Kategoria usługi:** Usługi domenowe usługi Azure AD  
**Możliwości produktu:** Usługi domenowe usługi Azure AD

Usługi domenowe usługi Azure AD udostępnia alerty w portalu Platformy Azure o błędnych konfiguracjach lub problemach z domeną zarządzana. Alerty te obejmują przewodniki krok po kroku, dzięki czemu można spróbować rozwiązać problemy bez konieczności kontaktowania się z pomocą techniczną.

Od października możesz dostosować ustawienia powiadomień dla domeny zarządzanej, aby w przypadku wystąpienia nowych alertów wiadomość e-mail była wysyłana do wyznaczonej grupy osób, eliminując konieczność ciągłego sprawdzania portalu pod kątem aktualizacji.

Aby uzyskać więcej informacji, zobacz [Ustawienia powiadomień w usługach domenowych usługi Azure AD](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-notifications).

---

### <a name="azure-ad-portal-supports-using-the-forcedelete-domain-api-to-delete-custom-domains"></a>Portal usługi Azure AD obsługuje usuwanie domen niestandardowych przy użyciu interfejsu API domeny ForceDelete 

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Zarządzanie katalogami  
**Możliwości produktu:** Katalogu

Z przyjemnością informujemy, że możesz teraz użyć interfejsu API domeny ForceDelete do usuwania niestandardowych nazw domen przez asynchronicznie zmianę nazwy odwołań, takich jak użytkownicy, grupy i aplikacje z niestandardowej nazwy domeny (contoso.com) z powrotem do początkowej domyślnej nazwy domeny (contoso.onmicrosoft.com).

Ta zmiana pomaga szybciej usunąć niestandardowe nazwy domen, jeśli organizacja nie używa już nazwy lub jeśli trzeba użyć nazwy domeny z inną usługą Azure AD.

Aby uzyskać więcej informacji, zobacz [Usuwanie niestandardowej nazwy domeny](https://docs.microsoft.com/azure/active-directory/users-groups-roles/domains-manage#delete-a-custom-domain-name).

---

## <a name="september-2018"></a>Wrzesień 2018 r.
 
### <a name="updated-administrator-role-permissions-for-dynamic-groups"></a>Zaktualizowane uprawnienia roli administratora dla grup dynamicznych

**Typ:** Stałe  
**Kategoria usługi:** Zarządzanie grupą  
**Możliwości produktu:** Współpracy

Naprawiliśmy problem, aby określone role administratorów mogły teraz tworzyć i aktualizować dynamiczne reguły członkostwa, bez konieczności bycia właścicielem grupy.

Role są następujące:

- Administrator globalny

- Administrator usługi Intune

- Administrator użytkownika

Aby uzyskać więcej informacji, zobacz [Tworzenie grupy dynamicznej i sprawdzanie stanu](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-create-rule)

---

### <a name="simplified-single-sign-on-sso-configuration-settings-for-some-third-party-apps"></a>Uproszczone ustawienia konfiguracji logowania jednokrotnego (SSO) dla niektórych aplikacji innych firm

**Typ:** Nowa funkcja  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwości produktu:** Usługi rejestracji jednokrotnej

Zdajemy sobie sprawę, że konfigurowanie aplikacji logujących jednokrotne (SSO) dla aplikacji typu Oprogramowanie jako usługa (SaaS) może być trudne ze względu na unikatowy charakter każdej konfiguracji aplikacji. Stworzyliśmy uproszczoną konfigurację, aby automatycznie wypełniać ustawienia konfiguracji logowania sytuacyjnego dla następujących aplikacji SaaS innych firm:

- Zendesk

- ArcGis Online

- Jamf Pro

Aby rozpocząć korzystanie z tego środowiska jednym kliknięciem, przejdź do strony**konfiguracji SSO usługi** **Azure portal** > dla aplikacji. Aby uzyskać więcej informacji, zobacz [Integracja aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list)

---

### <a name="azure-active-directory---where-is-your-data-located-page"></a>Usługa Azure Active Directory — gdzie znajdują się Twoje dane? Strona

**Typ:** Nowa funkcja  
**Kategoria usługi:** Innych  
**Możliwości produktu:** GoLocal (GoLocal)

Wybierz region firmy z **usługi Azure Active Directory — gdzie znajduje się strona danych,** aby wyświetlić, które centrum danych platformy Azure zawiera dane usługi Azure AD w spoczynku dla wszystkich usług Azure AD. Informacje można filtrować według określonych usług Azure AD dla regionu firmy.

Aby uzyskać dostęp do tej funkcji i uzyskać więcej informacji, zobacz [Usługa Azure Active Directory — gdzie znajdują się dane](https://aka.ms/AADDataMap).

---

### <a name="new-deployment-plan-available-for-the-my-apps-access-panel"></a>Nowy plan wdrażania dostępny dla panelu Mój dostęp do aplikacji

**Typ:** Nowa funkcja  
**Kategoria usługi:** Moje aplikacje  
**Możliwości produktu:** Usługi rejestracji jednokrotnej

Zapoznaj się z nowym planem wdrażania dostępnym dlahttps://aka.ms/deploymentplans)panelu Mój dostęp do aplikacji ( .
Panel Mój dostęp do aplikacji zapewnia użytkownikom jedno miejsce do znajdowania aplikacji i uzyskiwania do nich dostępu. Ten portal zapewnia również użytkownikom możliwości samoobsługi, takie jak żądanie dostępu do aplikacji i grup lub zarządzanie dostępem do tych zasobów w imieniu innych osób.

Aby uzyskać więcej informacji, zobacz [Co to jest portal Moje aplikacje?](https://docs.microsoft.com/azure/active-directory/user-help/active-directory-saas-access-panel-introduction)

---

### <a name="new-troubleshooting-and-support-tab-on-the-sign-ins-logs-page-of-the-azure-portal"></a>Nowa karta Rozwiązywanie problemów i pomoc techniczna na stronie Dzienniki logowania w witrynie Azure portal

**Typ:** Nowa funkcja  
**Kategoria usługi:** Reporting  
**Możliwości produktu:** Monitorowanie & raportowania

Nowa karta **Rozwiązywanie problemów i pomoc techniczna** na stronie **logowania w** witrynie Azure portal ma na celu pomoc administratorom i inżynierom pomocy technicznej w rozwiązywaniu problemów związanych z logowaniem usługi Azure AD. Ta nowa karta zawiera kod błędu, komunikat o błędzie i zalecenia dotyczące korygowania (jeśli istnieją), aby pomóc w rozwiązaniu problemu. Jeśli nie możesz rozwiązać problemu, dajemy ci również nowy sposób tworzenia biletu pomocy technicznej przy użyciu środowiska **Kopiuj do schowka,** który wypełnia pola **Identyfikator żądania** i **Data (UTC)** dla pliku dziennika w bilecie pomocy technicznej.  

![Dzienniki logowania z nową kartą](media/whats-new/troubleshooting-and-support.png)

---

### <a name="enhanced-support-for-custom-extension-properties-used-to-create-dynamic-membership-rules"></a>Ulepszona obsługa niestandardowych właściwości rozszerzeń używanych do tworzenia dynamicznych reguł członkostwa

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Zarządzanie grupą  
**Możliwości produktu:** Współpracy

Dzięki tej aktualizacji można teraz kliknąć **łącze Pobierz niestandardowe właściwości rozszerzenia** z konstruktora reguł dynamicznej grupy użytkowników, wprowadzić unikatowy identyfikator aplikacji i otrzymać pełną listę niestandardowych właściwości rozszerzenia, które będą używane podczas tworzenia dynamicznej reguły członkostwa dla użytkowników. Tę listę można również odświeżyć, aby uzyskać nowe niestandardowe właściwości rozszerzenia dla tej aplikacji.

Aby uzyskać więcej informacji na temat używania niestandardowych właściwości rozszerzeń dla dynamicznych reguł członkostwa, zobacz [Właściwości rozszerzenia i niestandardowe właściwości rozszerzeń](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership#extension-properties-and-custom-extension-properties)

---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Nowe zatwierdzone aplikacje klienckie dla dostępu warunkowego opartego na usłudze Azure AD

**Typ:** Plan zmian  
**Kategoria usługi:** Dostęp warunkowy  
**Możliwości produktu:** Bezpieczeństwo tożsamości i ochrona tożsamości

Na liście [zatwierdzonych aplikacji klienckich](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-conditions#client-apps-preview)znajdują się następujące aplikacje:

- Microsoft To-Do

- Usługa Microsoft Stream

Aby uzyskać więcej informacji, zobacz:

- [Dostęp warunkowy oparty na aplikacji usługi Azure AD](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)

---

### <a name="new-support-for-self-service-password-reset-from-the-windows-7881-lock-screen"></a>Nowa obsługa samoobsługowego resetowania hasła z ekranu blokady systemu Windows 7/8/8.1

**Typ:** Nowa funkcja  
**Kategoria usługi:** Wiele z tych  
**Możliwości produktu:** Uwierzytelnianie użytkownika

Po skonfigurowaniu tej nowej funkcji użytkownicy zobaczą łącze umożliwiające zresetowanie hasła z ekranu **blokady** urządzenia z systemem Windows 7, Windows 8 lub Windows 8.1. Klikając ten link, użytkownik jest prowadzony przez ten sam przepływ resetowania hasła, jak za pośrednictwem przeglądarki internetowej.

Aby uzyskać więcej informacji, zobacz [Jak włączyć resetowanie hasła z systemu Windows 7, 8 i 8.1](https://aka.ms/ssprforwindows78)

---

### <a name="change-notice-authorization-codes-will-no-longer-be-available-for-reuse"></a>Powiadomienie o zmianie: Kody autoryzacji nie będą już dostępne do ponownego użycia 

**Typ:** Plan zmian  
**Kategoria usługi:** Uwierzytelnianie (loginy)  
**Możliwości produktu:** Uwierzytelnianie użytkownika

Począwszy od 15 listopada 2018 r. usługa Azure AD przestanie akceptować wcześniej używane kody uwierzytelniania dla aplikacji. Ta zmiana zabezpieczeń pomaga dostosować usługę Azure AD do specyfikacji OAuth i będzie wymuszana zarówno w punktach końcowych w wersji 1, jak i w wersji 2.

Jeśli aplikacja ponownie używa kodów autoryzacji, aby uzyskać tokeny dla wielu zasobów, zaleca się użycie kodu, aby uzyskać token odświeżania, a następnie użyć tego tokenu odświeżania, aby uzyskać dodatkowe tokeny dla innych zasobów. Kody autoryzacji można używać tylko raz, ale tokeny odświeżania mogą być używane wiele razy w wielu zasobach. Aplikacja, która próbuje ponownie użyć kodu uwierzytelniania podczas przepływu kodu OAuth, otrzyma błąd invalid_grant.

W tym celu i inne zmiany związane z protokołami można znaleźć [na pełnej liście nowości w zakresie uwierzytelniania](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---september-2018"></a>Nowe aplikacje federacyjne dostępne w galerii aplikacji usługi Azure AD — wrzesień 2018 r.

**Typ:** Nowa funkcja  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwości produktu:** Integracja stron trzecich
 
We wrześniu 2018 r. dodaliśmy do galerii aplikacji 16 nowych aplikacji z obsługą federacji:

[Uberflip](https://docs.microsoft.com/azure/active-directory/saas-apps/uberflip-tutorial), [Comeet Recruiting Software](https://docs.microsoft.com/azure/active-directory/saas-apps/comeetrecruitingsoftware-tutorial), [Workteam](https://docs.microsoft.com/azure/active-directory/saas-apps/workteam-tutorial), [ArcGIS Enterprise](https://docs.microsoft.com/azure/active-directory/saas-apps/arcgisenterprise-tutorial), [Nuclino](https://docs.microsoft.com/azure/active-directory/saas-apps/nuclino-tutorial), [JDA Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/jdacloud-tutorial), [Snowflake](https://docs.microsoft.com/azure/active-directory/saas-apps/snowflake-tutorial), NavigoCloud, [Figma](https://docs.microsoft.com/azure/active-directory/saas-apps/figma-tutorial), join.me, [ZephyrSSO](https://docs.microsoft.com/azure/active-directory/saas-apps/zephyrsso-tutorial), [Silverback](https://docs.microsoft.com/azure/active-directory/saas-apps/silverback-tutorial), Riverbed Xirrus EasyPass, [Rackspace SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/rackspacesso-tutorial), Enlyft SSO for Azure, SurveyMonkey, [Convene](https://docs.microsoft.com/azure/active-directory/saas-apps/convene-tutorial), [dmarcian](https://docs.microsoft.com/azure/active-directory/saas-apps/dmarcian-tutorial)

Aby uzyskać więcej informacji na temat aplikacji, zobacz [Integracja aplikacji SaaS z usługą Azure Active Directory](https://aka.ms/appstutorial). Aby uzyskać więcej informacji na temat wyświetlania aplikacji w galerii aplikacji usługi Azure AD, zobacz [Lista aplikacji w galerii aplikacji usługi Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="support-for-additional-claims-transformations-methods"></a>Obsługa dodatkowych metod przekształceń oświadczeń

**Typ:** Nowa funkcja  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwości produktu:** Usługi rejestracji jednokrotnej

Wprowadziliśmy nowe metody przekształcania oświadczeń, ToLower() i ToUpper(), które można zastosować do tokenów SAML na stronie **konfiguracji logowania jednokrotnego oparte na** SAML.

Aby uzyskać więcej informacji, zobacz [Jak dostosować oświadczenia wystawione w tokenie SAML dla aplikacji dla przedsiębiorstw w usłudze Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization)

---

### <a name="updated-saml-based-app-configuration-ui-preview"></a>Zaktualizowany interfejs użytkownika konfiguracji aplikacji oparty na saml (wersja zapoznawcza)

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwości produktu:** Usługi rejestracji jednokrotnej

W ramach naszego zaktualizowanego interfejsu użytkownika konfiguracji aplikacji opartej na SAML otrzymasz:

- Zaktualizowane środowisko instruktażowe do konfigurowania aplikacji opartych na SAML.

- Większa widoczność tego, czego brakuje lub jest nieprawidłowa w konfiguracji.

- Możliwość dodawania wielu adresów e-mail do powiadamiania o certyfikatie wygaśnięcia.

- Nowe metody przekształcania oświadczeń, ToLower() i ToUpper() i inne.

- Sposób przekazywania własnego certyfikatu podpisywania tokenów dla aplikacji korporacyjnych.

- Sposób ustawienia formatu NameID dla aplikacji SAML oraz sposób ustawienia wartości NameID jako rozszerzeń katalogów.

Aby włączyć ten zaktualizowany widok, kliknij łącze **Wypróbuj nasze nowe środowisko** u góry strony logowania **jednokrotnego.** Aby uzyskać więcej informacji, zobacz [Samouczek: Konfigurowanie logowania jednokrotnego opartego na saml dla aplikacji za pomocą usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-single-sign-on-non-gallery-applications).

---

## <a name="august-2018"></a>Sierpień 2018 r.

### <a name="changes-to-azure-active-directory-ip-address-ranges"></a>Zmiany w zakresach adresów IP usługi Azure Active Directory

**Typ:** Plan zmian  
**Kategoria usługi:** Innych  
**Możliwości produktu:** Platformy

Wprowadzamy większe zakresy adresów IP do usługi Azure AD, co oznacza, że jeśli skonfigurowano zakresy adresów IP usługi Azure AD dla zapór, routerów lub grup zabezpieczeń sieciowych, musisz je zaktualizować. Wprowadzamy tę aktualizację, dzięki czemu nie trzeba ponownie zmieniać konfiguracji zakresu adresów IP zapory, routera ani sieciowych grup zabezpieczeń, gdy usługa Azure AD doda nowe punkty końcowe. 

Ruch sieciowy przenosi się do tych nowych zakresów w ciągu najbliższych dwóch miesięcy. Aby kontynuować nieprzerwaną usługę, należy dodać te zaktualizowane wartości do adresów IP przed 10 września 2018 r.:

- 20.190.128.0/18 

- 40.126.0.0/18 

Zdecydowanie zaleca się nie usuwanie starych zakresów adresów IP, dopóki cały ruch sieciowy nie przeniesie się do nowych zakresów. Aby uzyskać informacje o przeprowadzce i dowiedzieć się, kiedy można usunąć stare zakresy, zobacz [Adresy URL usługi Office 365 i zakresy adresów IP](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2).

---

### <a name="change-notice-authorization-codes-will-no-longer-be-available-for-reuse"></a>Powiadomienie o zmianie: Kody autoryzacji nie będą już dostępne do ponownego użycia 

**Typ:** Plan zmian  
**Kategoria usługi:** Uwierzytelnianie (loginy)  
**Możliwości produktu:** Uwierzytelnianie użytkownika

Począwszy od 15 listopada 2018 r. usługa Azure AD przestanie akceptować wcześniej używane kody uwierzytelniania dla aplikacji. Ta zmiana zabezpieczeń pomaga dostosować usługę Azure AD do specyfikacji OAuth i będzie wymuszana zarówno w punktach końcowych w wersji 1, jak i w wersji 2.

Jeśli aplikacja ponownie używa kodów autoryzacji, aby uzyskać tokeny dla wielu zasobów, zaleca się użycie kodu, aby uzyskać token odświeżania, a następnie użyć tego tokenu odświeżania, aby uzyskać dodatkowe tokeny dla innych zasobów. Kody autoryzacji można używać tylko raz, ale tokeny odświeżania mogą być używane wiele razy w wielu zasobach. Aplikacja, która próbuje ponownie użyć kodu uwierzytelniania podczas przepływu kodu OAuth, otrzyma błąd invalid_grant.

W tym celu i inne zmiany związane z protokołami można znaleźć [na pełnej liście nowości w zakresie uwierzytelniania](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes).
 
---

### <a name="converged-security-info-management-for-self-service-password-sspr-and-multi-factor-authentication-mfa"></a>Konwergentne zarządzanie informacjami zabezpieczającymi dla haseł samoobsługowych (SSPR) i uwierzytelniania wieloskładnikowego (MFA)

**Typ:** Nowa funkcja  
**Kategoria usługi:** Wiele z tych  
**Możliwości produktu:** Uwierzytelnianie użytkownika
 
Ta nowa funkcja pomaga użytkownikom zarządzać informacjami zabezpieczającymi (takimi jak numer telefonu, aplikacja mobilna itd.) dla samooka i usługi MFA w jednej lokalizacji i doświadczeniu; w porównaniu z poprzednimi, gdzie odbywało się to w dwóch różnych miejscach.

To konwergentne doświadczenie działa również dla osób korzystających z funkcji SSPR lub usługi MFA. Ponadto jeśli organizacja nie wymusza rejestracji usługi MFA lub SSPR, użytkownicy mogą nadal rejestrować wszystkie metody zabezpieczeń usługi MFA lub SSPR dozwolone przez organizację z portalu Moje aplikacje.

Jest to publiczna wersja zapoznawcza opt-in. Administratorzy mogą włączyć nowe środowisko (w razie potrzeby) dla wybranej grupy lub dla wszystkich użytkowników w dzierżawie. Aby uzyskać więcej informacji na temat środowiska konwergentnego, zobacz [blog z konwergentnymi środowiskami](https://cloudblogs.microsoft.com/enterprisemobility/2018/08/06/mfa-and-sspr-updates-now-in-public-preview/)

---

### <a name="new-http-only-cookies-setting-in-azure-ad-application-proxy-apps"></a>Nowe ustawienie plików cookie tylko do protokołu HTTP w aplikacjach proxy aplikacji usługi Azure AD

**Typ:** Nowa funkcja  
**Kategoria usługi:** Serwer proxy aplikacji  
**Możliwości produktu:** Kontrola dostępu

W aplikacjach proxy aplikacji pojawiło się nowe ustawienie o nazwie **Pliki cookie tylko http.** To ustawienie pomaga zapewnić dodatkowe zabezpieczenia, dołączając flagę HTTPOnly w nagłówku odpowiedzi HTTP zarówno dla plików cookie dostępu serwera proxy aplikacji, jak i plików cookie sesji, zatrzymując dostęp do pliku cookie ze skryptu po stronie klienta i zapobiegając dalszym działaniom, takim jak kopiowanie lub modyfikowanie pliku cookie. Chociaż ta flaga nie była używana wcześniej, pliki cookie zawsze były szyfrowane i przesyłane za pomocą połączenia TLS w celu ochrony przed niewłaściwymi modyfikacjami.

To ustawienie nie jest zgodne z aplikacjami korzystającymi z formantów ActiveX, takich jak Pulpit zdalny. Jeśli jesteś w takiej sytuacji, zalecamy wyłączenie tego ustawienia.

Aby uzyskać więcej informacji na temat ustawienia Plików cookie tylko http, zobacz [Publikowanie aplikacji przy użyciu serwera proxy aplikacji usługi Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-publish-azure-portal).

---

### <a name="privileged-identity-management-pim-for-azure-resources-supports-management-group-resource-types"></a>Zarządzanie tożsamościami uprzywilejowanymi (PIM) dla zasobów platformy Azure obsługuje typy zasobów grupy zarządzania

**Typ:** Nowa funkcja  
**Kategoria usługi:** Zarządzanie tożsamościami uprzywilejowanymi  
**Możliwości produktu:** Zarządzanie tożsamościami uprzywilejowanymi
 
Ustawienia aktywacji i przydziału just-in-Time można teraz stosować do typów zasobów grupy zarządzania, podobnie jak w przypadku subskrypcji, grup zasobów i zasobów (takich jak maszyny wirtualne, usługi aplikacji i inne). Ponadto każda osoba, która pełni rolę zapewniającą administratorowi dostęp do grupy zarządzania, może odnajdować ten zasób i zarządzać nim w ułowieniu.

Aby uzyskać więcej informacji na temat zasobów usługi PIM i platformy Azure, zobacz [Odnajdywanie zasobów platformy Azure i zarządzanie nimi przy użyciu zarządzania tożsamościami uprzywilejowanymi](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-resource-roles-discover-resources)
 
---

### <a name="application-access-preview-provides-faster-access-to-the-azure-ad-portal"></a>Dostęp do aplikacji (wersja zapoznawcza) zapewnia szybszy dostęp do portalu usługi Azure AD

**Typ:** Nowa funkcja  
**Kategoria usługi:** Zarządzanie tożsamościami uprzywilejowanymi  
**Możliwości produktu:** Zarządzanie tożsamościami uprzywilejowanymi
 
Dzisiaj podczas aktywacji roli przy użyciu usługi PIM, może upłynąć ponad 10 minut dla uprawnień, aby wejść w życie. Jeśli zdecydujesz się użyć dostępu do aplikacji, który jest obecnie w publicznej wersji zapoznawczej, administratorzy mogą uzyskać dostęp do portalu usługi Azure AD, gdy tylko żądanie aktywacji zostanie zakończone.

Obecnie dostęp do aplikacji obsługuje tylko środowisko portalu usługi Azure AD i zasoby platformy Azure. Aby uzyskać więcej informacji na temat usługi PIM i dostępu do aplikacji, zobacz [Co to jest zarządzanie tożsamościami uprzywilejowanymi usługi Azure AD?](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure)
 
---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---august-2018"></a>Nowe aplikacje federacyjne dostępne w galerii aplikacji usługi Azure AD — sierpień 2018 r.

**Typ:** Nowa funkcja  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwości produktu:** Integracja stron trzecich
 
W sierpniu 2018 r. dodaliśmy do galerii aplikacji 16 nowych aplikacji z obsługą federacji:

[Hornbill](https://docs.microsoft.com/azure/active-directory/saas-apps/hornbill-tutorial), [Bridgeline Unbound](https://docs.microsoft.com/azure/active-directory/saas-apps/bridgelineunbound-tutorial), [Sauce Labs - Mobile and Web Testing](https://docs.microsoft.com/azure/active-directory/saas-apps/saucelabs-mobileandwebtesting-tutorial), Meta [Networks Connector](https://docs.microsoft.com/azure/active-directory/saas-apps/metanetworksconnector-tutorial), Way We [Do](https://docs.microsoft.com/azure/active-directory/saas-apps/waywedo-tutorial), [Spotinst](https://docs.microsoft.com/azure/active-directory/saas-apps/spotinst-tutorial), [ProMaster (przez Inlogik)](https://docs.microsoft.com/azure/active-directory/saas-apps/promaster-tutorial), SchoolBooking, [4me](https://docs.microsoft.com/azure/active-directory/saas-apps/4me-tutorial), [Dossier](https://docs.microsoft.com/azure/active-directory/saas-apps/DOSSIER-tutorial), [N2F - Raporty wydatków](https://docs.microsoft.com/azure/active-directory/saas-apps/n2f-expensereports-tutorial), [Comm100 Live Chat](https://docs.microsoft.com/azure/active-directory/saas-apps/comm100livechat-tutorial), [SafeConnect](https://docs.microsoft.com/azure/active-directory/saas-apps/safeconnect-tutorial), [ZenQMS](https://docs.microsoft.com/azure/active-directory/saas-apps/zenqms-tutorial), [eLuminate](https://docs.microsoft.com/azure/active-directory/saas-apps/eluminate-tutorial), [Dovetale](https://docs.microsoft.com/azure/active-directory/saas-apps/dovetale-tutorial).

Aby uzyskać więcej informacji na temat aplikacji, zobacz [Integracja aplikacji SaaS z usługą Azure Active Directory](https://aka.ms/appstutorial). Aby uzyskać więcej informacji na temat wyświetlania aplikacji w galerii aplikacji usługi Azure AD, zobacz [Lista aplikacji w galerii aplikacji usługi Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="native-tableau-support-is-now-available-in-azure-ad-application-proxy"></a>Natywna obsługa tableau jest teraz dostępna w serwerze proxy aplikacji usługi Azure AD

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Serwer proxy aplikacji  
**Możliwości produktu:** Kontrola dostępu

Dzięki naszej aktualizacji z OpenID Connect do protokołu OAuth 2.0 Code Grant dla naszego protokołu preuwierzytelniania, nie musisz już wykonywać żadnej dodatkowej konfiguracji, aby używać Tableau z serwerem proxy aplikacji. Ta zmiana protokołu pomaga również serwerowi proxy aplikacji lepiej obsługiwać bardziej nowoczesne aplikacje przy użyciu tylko przekierowań HTTP, które są powszechnie obsługiwane w tagach JavaScript i HTML.

Aby uzyskać więcej informacji na temat naszej natywnej pomocy technicznej dla Tableau, zobacz [Usługi Azure AD Application Proxy teraz z natywnej obsługi Tableau](https://blogs.technet.microsoft.com/applicationproxyblog/2018/08/14/azure-ad-application-proxy-now-with-native-tableau-support).

---

### <a name="new-support-to-add-google-as-an-identity-provider-for-b2b-guest-users-in-azure-active-directory-preview"></a>Nowa obsługa dodawania Google jako dostawcy tożsamości dla użytkowników-gości B2B w usłudze Azure Active Directory (wersja zapoznawcza)

**Typ:** Nowa funkcja  
**Kategoria usługi:** B2b  
**Możliwości produktu:** B2B/B2C

Konfiguruj federację z Google w organizacji, możesz pozwolić zaproszonym użytkownikom Gmaila zalogować się do udostępnionych aplikacji i zasobów przy użyciu istniejącego konta Google bez konieczności tworzenia osobistego konta Microsoft (MSA) lub konta usługi Azure AD.

Jest to publiczna wersja zapoznawcza opt-in. Aby uzyskać więcej informacji o federacji Google, zobacz [Dodawanie Google jako dostawcy tożsamości dla użytkowników-gości B2B](https://docs.microsoft.com/azure/active-directory/b2b/google-federation).

---

## <a name="july-2018"></a>Lipiec 2018 r.

### <a name="improvements-to-azure-active-directory-email-notifications"></a>Ulepszenia powiadomień e-mail usługi Azure Active Directory

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Innych  
**Możliwości produktu:** Zarządzanie cyklem życia tożsamości
 
Wiadomości e-mail usługi Azure Active Directory (Azure AD) zawierają teraz zaktualizowany projekt, a także zmiany adresu e-mail nadawcy i nazwy wyświetlanej nadawcy, gdy są wysyłane z następujących usług:
 
- Recenzje programu Azure AD Access
- Azure AD Connect Health 
- Usługa Azure AD Identity Protection 
- Azure AD Privileged Identity Management
- Powiadomienia o certyfikatach wygasających aplikacji przedsiębiorstwa
- Powiadomienia o usłudze inicjowania obsługi administracyjnej aplikacji przedsiębiorstwa
 
Powiadomienia e-mail będą wysyłane z następującego adresu e-mail i nazwy wyświetlanej:

- Adres e-mail:azure-noreply@microsoft.com
- Nazwa wyświetlana: Microsoft Azure
 
Na przykład niektórych nowych projektów wiadomości e-mail i więcej informacji, zobacz [Powiadomienia e-mail w usłudze Azure AD PIM](https://go.microsoft.com/fwlink/?linkid=2005832).

---

### <a name="azure-ad-activity-logs-are-now-available-through-azure-monitor"></a>Dzienniki aktywności usługi Azure AD są teraz dostępne za pośrednictwem usługi Azure Monitor

**Typ:** Nowa funkcja  
**Kategoria usługi:** Reporting  
**Możliwości produktu:** Monitorowanie & raportowania

Dzienniki aktywności usługi Azure AD są teraz dostępne w publicznej wersji zapoznawczej dla usługi Azure Monitor (usługa monitorowania platformy Azure w całej platformie). Usługa Azure Monitor oferuje długoterminowe przechowywanie i bezproblemową integrację, oprócz tych ulepszeń:

- Długoterminowe przechowywanie przez routing plików dziennika do własnego konta magazynu platformy Azure.

- Bezproblemowa integracja SIEM bez konieczności pisania lub obsługi skryptów niestandardowych.

- Bezproblemowa integracja z własnymi rozwiązaniami niestandardowymi, narzędziami analitycznymi lub rozwiązaniami do zarządzania incydentami.

Aby uzyskać więcej informacji na temat tych nowych funkcji, zobacz nasze dzienniki aktywności usługi Azure AD w blogu [Usługi Azure Monitor diagnostyki jest teraz w publicznej wersji zapoznawczej](https://cloudblogs.microsoft.com/enterprisemobility/2018/07/26/azure-ad-activity-logs-in-azure-monitor-diagnostics-now-in-public-preview/) i naszej dokumentacji, [Dzienniki aktywności usługi Azure Active Directory w usłudze Azure Monitor (wersja zapoznawcza)](https://docs.microsoft.com/azure/active-directory/reporting-azure-monitor-diagnostics-overview).

---

### <a name="conditional-access-information-added-to-the-azure-ad-sign-ins-report"></a>Informacje o dostępie warunkowym dodane do raportu logowania usługi Azure AD

**Typ:** Nowa funkcja  
**Kategoria usługi:** Reporting  
**Możliwości produktu:** Ochrona & zabezpieczeń tożsamości
 
Ta aktualizacja pozwala zobaczyć, które zasady są oceniane, gdy użytkownik loguje się wraz z wynikiem zasad. Ponadto raport zawiera teraz typ aplikacji klienckiej używanej przez użytkownika, dzięki czemu można zidentyfikować ruch starszego protokołu. Wpisy raportu można teraz również wyszukiwać w poszukiwaniu identyfikatora korelacji, który można znaleźć w komunikacie o błędzie skierowanym do użytkownika i może służyć do identyfikowania i rozwiązywania problemów z pasującym żądaniem logowania.

---

### <a name="view-legacy-authentications-through-sign-ins-activity-logs"></a>Wyświetlanie starszych uwierzytelniania za pośrednictwem dzienników aktywności logowania

**Typ:** Nowa funkcja  
**Kategoria usługi:** Reporting  
**Możliwości produktu:** Monitorowanie & raportowania
 
Wraz z wprowadzeniem pola **Aplikacja kliencka** w dziennikach aktywności logowania klienci mogą teraz zobaczyć użytkowników, którzy używają starszych uwierzytelniania. Klienci będą mogli uzyskać dostęp do tych informacji przy użyciu interfejsu API logowania microsoft graph lub za pośrednictwem dzienników aktywności logowania w portalu usługi Azure AD, gdzie można użyć formantu **aplikacji klienta** do filtrowania starszych uwierzytelniania. Zapoznaj się z dokumentacją, aby uzyskać więcej informacji.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---july-2018"></a>Nowe aplikacje federacyjne dostępne w galerii aplikacji usługi Azure AD — lipiec 2018 r.

**Typ:** Nowa funkcja  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwości produktu:** Integracja stron trzecich
 
W lipcu 2018 r. dodaliśmy do galerii aplikacji 16 nowych aplikacji z obsługą federacji:

[Centrum Innowacji](https://docs.microsoft.com/azure/active-directory/saas-apps/innovationhub-tutorial), [Leapsome](https://docs.microsoft.com/azure/active-directory/saas-apps/leapsome-tutorial), [Niektóre Admin SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/certainadminsso-tutorial), PSUC Staging, [iPass SmartConnect](https://docs.microsoft.com/azure/active-directory/saas-apps/ipasssmartconnect-tutorial), [Screencast-O-Matic](https://docs.microsoft.com/azure/active-directory/saas-apps/screencast-tutorial), PowerSchool Unified Classroom , [Eli Onboarding](https://docs.microsoft.com/azure/active-directory/saas-apps/elionboarding-tutorial), [Bomgar Remote Support](https://docs.microsoft.com/azure/active-directory/saas-apps/bomgarremotesupport-tutorial), [Nimblex](https://docs.microsoft.com/azure/active-directory/saas-apps/nimblex-tutorial), [Imagineer WebVision](https://docs.microsoft.com/azure/active-directory/saas-apps/imagineerwebvision-tutorial), [Insight4GRC](https://docs.microsoft.com/azure/active-directory/saas-apps/insight4grc-tutorial), [SecureW2 JoinNow Connector](https://docs.microsoft.com/azure/active-directory/saas-apps/securejoinnow-tutorial), [Kanbanize](../saas-apps/kanbanize-tutorial.md), [SmartLPA](../saas-apps/smartlpa-tutorial.md), [Skills Base](https://docs.microsoft.com/azure/active-directory/saas-apps/skillsbase-tutorial)

Aby uzyskać więcej informacji na temat aplikacji, zobacz [Integracja aplikacji SaaS z usługą Azure Active Directory](https://aka.ms/appstutorial). Aby uzyskać więcej informacji na temat wyświetlania aplikacji w galerii aplikacji usługi Azure AD, zobacz [Lista aplikacji w galerii aplikacji usługi Azure Active Directory](https://aka.ms/azureadapprequest).

---
 
### <a name="new-user-provisioning-saas-app-integrations---july-2018"></a>Integracje aplikacji saaS nowych użytkowników — lipiec 2018 r.

**Typ:** Nowa funkcja  
**Kategoria usługi:** Inicjowanie obsługi administracyjnej aplikacji  
**Możliwości produktu:** Integracja stron trzecich
 
Usługa Azure AD umożliwia automatyzację tworzenia, konserwacji i usuwania tożsamości użytkowników w aplikacjach SaaS, takich jak Dropbox, Salesforce, ServiceNow i innych. W lipcu 2018 r. dodaliśmy obsługę inicjowania obsługi administracyjnej dla następujących aplikacji w galerii aplikacji usługi Azure AD:

- [Cisco WebEx](https://docs.microsoft.com/azure/active-directory/saas-apps/cisco-webex-provisioning-tutorial)

- [Bonusly](https://docs.microsoft.com/azure/active-directory/saas-apps/bonusly-provisioning-tutorial)

Aby uzyskać listę wszystkich aplikacji obsługujących inicjowanie obsługi administracyjnej użytkowników w galerii usługi Azure AD, zobacz [Integracja aplikacji SaaS z usługą Azure Active Directory](https://aka.ms/appstutorial).

---

### <a name="connect-health-for-sync---an-easier-way-to-fix-orphaned-and-duplicate-attribute-sync-errors"></a>Połącz kondycję dla synchronizacji — łatwiejszy sposób naprawiania błędów synchronizacji osieroconych i zduplikowanych atrybutów

**Typ:** Nowa funkcja  
**Kategoria usługi:** Połączenie usługi AD  
**Możliwości produktu:** Monitorowanie & raportowania
 
Usługa Azure AD Connect Health wprowadza samoobsługowe korygowanie, które ułatwia wyróżnianie i naprawianie błędów synchronizacji. Ta funkcja rozwiązuje błędy synchronizacji zduplikowanych atrybutów i naprawia obiekty oddzielone od usługi Azure AD. Diagnoza ta ma następujące korzyści:

- Zawęża błędy synchronizacji zduplikowanych atrybutów, zapewniając określone poprawki

- Stosuje poprawkę dla dedykowanych scenariuszy usługi Azure AD, rozwiązując błędy w jednym kroku

- Do włączenia i korzystania z tej funkcji nie jest wymagane uaktualnienie ani konfiguracja.

Aby uzyskać więcej informacji, zobacz [Diagnozowanie i korygowanie błędów synchronizacji zduplikowanych atrybutów](https://docs.microsoft.com/azure/active-directory/connect-health/active-directory-aadconnect-health-diagnose-sync-errors)

---

### <a name="visual-updates-to-the-azure-ad-and-msa-sign-in-experiences"></a>Aktualizacje wizualne środowiska logowania usługi Azure AD i MSA

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Azure AD  
**Możliwości produktu:** Uwierzytelnianie użytkownika

Zaktualizowaliśmy interfejs użytkownika dla środowiska logowania usług online firmy Microsoft, na przykład dla usługi Office 365 i platformy Azure. Ta zmiana sprawia, że ekrany są mniej zaśmiecone i prostsze. Aby uzyskać więcej informacji na temat tej zmiany, zobacz [nadchodzące ulepszenia bloga środowiska logowania usługi Azure AD.](https://cloudblogs.microsoft.com/enterprisemobility/2018/04/04/upcoming-improvements-to-the-azure-ad-sign-in-experience/)

---

### <a name="new-release-of-azure-ad-connect---july-2018"></a>Nowa wersja usługi Azure AD Connect — lipiec 2018 r.

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Inicjowanie obsługi administracyjnej aplikacji  
**Możliwości produktu:** Zarządzanie cyklem życia tożsamości

Najnowsza wersja usługi Azure AD Connect zawiera: 

- Poprawki błędów i aktualizacje obsługi 

- Ogólna dostępność integracji Ping-Federrate

- Aktualizacje najnowszego klienta programu SQL 2012 

Aby uzyskać więcej informacji na temat tej aktualizacji, zobacz [Usługa Azure AD Connect: Historia wersji](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-version-history)

---

### <a name="updates-to-the-terms-of-use-end-user-ui"></a>Aktualizacje warunków użytkowania interfejsu użytkownika końcowego

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Warunki użytkowania  
**Możliwości produktu:** Zarządzania

Aktualizujemy ciąg akceptacji w interfejsie użytkownika końcowego WYTU.

**Bieżący tekst.** Aby uzyskać dostęp do zasobów [tenantName], należy zaakceptować warunki użytkowania.<br>**Nowy tekst.** Aby uzyskać dostęp do zasobu [tenantName], należy zapoznać się z warunkami użytkowania.

**Bieżący tekst:** Wyrażenie zgody oznacza, że zgadzasz się na wszystkie powyższe warunki użytkowania.<br>**Nowy tekst:** Kliknij przycisk Zaakceptuj, aby potwierdzić, że przeczytałeś i zrozumiałeś warunki użytkowania.

---
 
### <a name="pass-through-authentication-supports-legacy-protocols-and-applications"></a>Uwierzytelnianie przekazywane obsługuje starsze protokoły i aplikacje

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Uwierzytelnianie (loginy)  
**Możliwości produktu:** Uwierzytelnianie użytkownika
 
Uwierzytelnianie przekazywane obsługuje teraz starsze protokoły i aplikacje. Następujące ograniczenia są teraz w pełni obsługiwane:

- Logowania użytkowników do starszych aplikacji klienckich pakietu Office, pakietu Office 2010 i pakietu Office 2013 bez konieczności nowoczesnego uwierzytelniania.

- Dostęp do udostępniania kalendarza oraz informacji wolny/zajęty w środowiskach hybrydowych programu Exchange tylko w pakiecie Office 2010.

- Logowania użytkowników do aplikacji klienckich programu Skype dla firm bez konieczności nowoczesnego uwierzytelniania.

- Logowania użytkowników do programu PowerShell w wersji 1.0.

- Program rejestracji urządzeń firmy Apple (Apple DEP) za pomocą Asystenta konfiguracji systemu iOS. 

---
 
### <a name="converged-security-info-management-for-self-service-password-reset-and-multi-factor-authentication"></a>Konwergentne zarządzanie informacjami zabezpieczającymi do samodzielnego resetowania haseł i uwierzytelniania wieloskładnikowego

**Typ:** Nowa funkcja  
**Kategoria usługi:** Wiele z tych  
**Możliwości produktu:** Uwierzytelnianie użytkownika

Ta nowa funkcja umożliwia użytkownikom zarządzanie informacjami zabezpieczającymi (na przykład numerem telefonu, adresem e-mail, aplikacją mobilną itd.) w celu samodzielnego resetowania hasła (SSPR) i uwierzytelniania wieloskładnikowego (MFA) w jednym experience środowisko. Użytkownicy nie będą już musieli rejestrować tych samych informacji zabezpieczających dla samowłasnych i wieloskładnikowych w dwóch różnych środowiskach. To nowe środowisko dotyczy również użytkowników, którzy mają jednookie i duże wymagania.

Jeśli organizacja nie wymusza rejestracji usługi MFA lub SSPR, użytkownicy mogą rejestrować swoje informacje zabezpieczające za pośrednictwem portalu **Moje aplikacje.** Stamtąd użytkownicy mogą rejestrować wszystkie metody włączone dla usługi MFA lub sspr. 

Jest to publiczna wersja zapoznawcza opt-in. Administratorzy mogą włączyć nowe środowisko (w razie potrzeby) dla wybranej grupy użytkowników lub wszystkich użytkowników w dzierżawie.

---
 
### <a name="use-the-microsoft-authenticator-app-to-verify-your-identity-when-you-reset-your-password"></a>Weryfikacja hasła za pomocą aplikacji Microsoft Authenticator

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Wiele z tych  
**Możliwości produktu:** Uwierzytelnianie użytkownika

Ta funkcja umożliwia osobom niebędącym administratorami weryfikowanie tożsamości podczas resetowania hasła przy użyciu powiadomienia lub kodu z programu Microsoft Authenticator (lub dowolnej innej aplikacji uwierzytelniającego). Po włączeniu przez administratorów tej samoobsługowej metody resetowania hasła użytkownicy, którzy zarejestrowali aplikację mobilną za pośrednictwem aka.ms/mfasetup lub aka.ms/setupsecurityinfo mogą korzystać z aplikacji mobilnej jako metody weryfikacji podczas resetowania hasła.

Powiadomienie o aplikacji mobilnej można włączyć tylko w ramach zasad, które wymagają dwóch metod resetowania hasła.

---

## <a name="june-2018"></a>Czerwiec 2018 r.

### <a name="change-notice-security-fix-to-the-delegated-authorization-flow-for-apps-using-azure-ad-activity-logs-api"></a>Powiadomienie o zmianie: poprawka zabezpieczeń do przepływu autoryzacji delegowanej dla aplikacji korzystających z interfejsu API dzienników aktywności usługi Azure AD

**Typ:** Plan zmian  
**Kategoria usługi:** Reporting  
**Możliwości produktu:** Monitorowanie & raportowania

Ze względu na nasze silniejsze wymuszanie zabezpieczeń musieliśmy wprowadzić zmiany w uprawnieniach dla aplikacji korzystających z delegowanego przepływu autoryzacji w celu uzyskania dostępu do [interfejsów API dzienników aktywności usługi Azure AD.](https://aka.ms/aadreportsapi) Zmiana ta nastąpi do **26 czerwca 2018 r.**.

Jeśli którakolwiek z aplikacji korzysta z interfejsów API dziennika aktywności usługi Azure AD, wykonaj następujące kroki, aby upewnić się, że aplikacja nie zostanie przerwana po zmianie.

**Aby zaktualizować uprawnienia aplikacji**

1. Zaloguj się do witryny Azure Portal, wybierz pozycję **Azure Active Directory**, a następnie wybierz pozycję **Rejestracje aplikacji**.
2. Wybierz aplikację, która korzysta z interfejsu API dzienników aktywności usługi Azure AD, wybierz **pozycję Ustawienia**, wybierz pozycję **Wymagane uprawnienia**, a następnie wybierz interfejs API usługi **Active Directory systemu Windows Azure.**
3. W obszarze **Uprawnienia delegowane** bloku **Włącz dostęp** zaznacz pole obok pozycji Odczyt danych **katalogu,** a następnie wybierz pozycję **Zapisz**.
4. Wybierz **pozycję Udziel uprawnień**, a następnie wybierz pozycję **Tak**.
    
    >[!Note]
    >Aby udzielić uprawnień do aplikacji, musisz być administratorem globalnym.

Aby uzyskać więcej informacji, zobacz obszar [Udzielanie uprawnień](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-prerequisites-azure-portal#grant-permissions) wymagań wstępnych, aby uzyskać dostęp do interfejsu API raportowania usługi Azure AD.

---

### <a name="configure-tls-settings-to-connect-to-azure-ad-services-for-pci-dss-compliance"></a>Konfigurowanie ustawień protokołu TLS w celu łączenia się z usługami usługi Azure AD w celu zapewnienia zgodności z usługami PCI DSS

**Typ:** Nowa funkcja  
**Kategoria usługi:** N/a  
**Możliwości produktu:** Platformy

Transport Layer Security (TLS) to protokół, który zapewnia prywatność i integralność danych między dwiema aplikacjami komunikującymi się i jest najczęściej wdrażanym protokołem zabezpieczeń używanym obecnie.

[Rada Standardów Bezpieczeństwa PCI](https://www.pcisecuritystandards.org/) ustaliła, że wczesne wersje protokołu TLS i Secure Sockets Layer (SSL) muszą być wyłączone na rzecz włączania nowych i bezpieczniejszych protokołów aplikacji, z przestrzeganiem go od **30 czerwca 2018**r. Ta zmiana oznacza, że jeśli łączysz się z usługami Usługi Azure AD i wymagasz zgodności z USŁUGĄ PCI DSS, należy wyłączyć usługę TLS 1.0. Dostępnych jest wiele wersji protokołu TLS, ale tls 1.2 to najnowsza wersja dostępna dla usług Azure Active Directory Services. Zdecydowanie zalecamy przejście bezpośrednio do protokołu TLS 1.2 zarówno dla kombinacji klient/serwer, jak i przeglądarka/serwer.

Przestarzałe przeglądarki mogą nie obsługiwać nowszych wersji protokołu TLS, takich jak TLS 1.2. Aby zobaczyć, które wersje protokołu TLS są obsługiwane przez przeglądarkę, przejdź do [witryny Qualys SSL Labs](https://www.ssllabs.com/) i kliknij **przycisk Przetestuj przeglądarkę**. Zalecamy uaktualnienie do najnowszej wersji przeglądarki internetowej i najlepiej włączyć tylko TLS 1.2.

**Aby włączyć TLS 1.2, przez przeglądarkę**

- **Microsoft Edge i Internet Explorer (oba są ustawione przy użyciu programu Internet Explorer)**

    1. Otwórz program Internet Explorer, wybierz pozycję **Narzędzia** > **Opcje** > internetowe**Zaawansowane**.
    2. W obszarze **Zabezpieczenia** wybierz **opcję Użyj protokołu TLS 1.2**, a następnie wybierz przycisk **OK**.
    3. Zamknij wszystkie okna przeglądarki i uruchom ponownie program Internet Explorer. 

- **Google Chrome**

    1. Otwórz Przeglądarkę Google Chrome, wpisz *chrome://settings/* na pasku adresu i naciśnij klawisz **Enter**.
    2. Rozwiń opcje **zaawansowane,** przejdź do obszaru **System** i wybierz pozycję **Otwórz ustawienia serwera proxy**.
    3. W polu **Właściwości internetowe** wybierz kartę **Zaawansowane,** przejdź do obszaru **Zabezpieczenia,** wybierz **pozycję Użyj protokołu TLS 1.2**, a następnie wybierz przycisk **OK**.
    4. Zamknij wszystkie okna przeglądarki i uruchom ponownie Google Chrome.

- **Mozilla Firefox**

    1. Otwórz Firefoksa, wpisz *about:config* na pasku adresu, a następnie naciśnij **klawisz Enter**.
    2. Wyszukaj termin *TLS,* a następnie wybierz wpis **security.tls.version.max.**
    3. Ustaw wartość na **3,** aby wymusić użycie przeglądarki do wersji TLS 1.2, a następnie wybierz **przycisk OK**.

        >[!NOTE]
        >Firefox w wersji 60.0 obsługuje TLS 1.3, dzięki czemu można również ustawić wartość security.tls.version.max na **4**.

    4. Zamknij wszystkie okna przeglądarki i uruchom ponownie Mozilla Firefox.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---june-2018"></a>Nowe aplikacje federacyjne dostępne w galerii aplikacji usługi Azure AD — czerwiec 2018 r.

**Typ:** Nowa funkcja  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwości produktu:** Integracja stron trzecich
 
W czerwcu 2018 r. dodaliśmy do galerii aplikacji 15 nowych aplikacji z obsługą federacji:

[Skytap](https://docs.microsoft.com/azure/active-directory/active-directory-saas-skytap-tutorial), [Rozliczanie muzyki](https://docs.microsoft.com/azure/active-directory/active-directory-saas-settlingmusic-tutorial), [SAML 1.1 Token włączone LOB App](https://docs.microsoft.com/azure/active-directory/active-directory-saas-saml-tutorial), [Supermood](https://docs.microsoft.com/azure/active-directory/active-directory-saas-supermood-tutorial), [Autotask](https://docs.microsoft.com/azure/active-directory/active-directory-saas-autotaskendpointbackup-tutorial), [Endpoint Backup](https://docs.microsoft.com/azure/active-directory/active-directory-saas-autotaskendpointbackup-tutorial), [Skyhigh Networks](https://docs.microsoft.com/azure/active-directory/active-directory-saas-skyhighnetworks-tutorial), Smartway2, [TonicDM](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tonicdm-tutorial), [Moconavi](https://docs.microsoft.com/azure/active-directory/active-directory-saas-moconavi-tutorial), [Zoho One](https://docs.microsoft.com/azure/active-directory/active-directory-saas-zohoone-tutorial), [SharePoint na miejscu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-sharepoint-on-premises-tutorial), [ForeSee CX Suite](https://docs.microsoft.com/azure/active-directory/active-directory-saas-foreseecxsuite-tutorial), [Vidyard](https://docs.microsoft.com/azure/active-directory/active-directory-saas-vidyard-tutorial), [ChronicX](https://docs.microsoft.com/azure/active-directory/active-directory-saas-chronicx-tutorial)

Aby uzyskać więcej informacji na temat aplikacji, zobacz [Integracja aplikacji SaaS z usługą Azure Active Directory](https://aka.ms/appstutorial). Aby uzyskać więcej informacji na temat wyświetlania aplikacji w galerii aplikacji usługi Azure AD, zobacz [Lista aplikacji w galerii aplikacji usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing). 

---

### <a name="azure-ad-password-protection-is-available-in-public-preview"></a>Ochrona hasłem usługi Azure AD jest dostępna w publicznej wersji zapoznawczej

**Typ:** Nowa funkcja  
**Kategoria usługi:** Ochrona tożsamości  
**Możliwości produktu:** Uwierzytelnianie użytkownika

Ochrona hasłem usługi Azure AD pomaga wyeliminować łatwo odgadowane hasła ze środowiska. Wyeliminowanie tych haseł pomaga zmniejszyć ryzyko naruszenia bezpieczeństwa z typu spray hasła ataku.

W szczególności usługa Azure AD Password Protection pomaga:

- Ochrona kont organizacji zarówno w usłudze Azure AD, jak i w usłudze Active Directory (AD) systemu Windows Server. 
- Uniemożliwia użytkownikom używanie haseł na liście ponad 500 najczęściej używanych haseł i ponad milionowi odmian tych haseł.
- Administrowanie ochroną hasłem usługi Azure AD z jednej lokalizacji w portalu usługi Azure AD, zarówno dla usługi Azure AD, jak i lokalnego systemu Windows Server AD.

Aby uzyskać więcej informacji na temat ochrony hasłem usługi Azure AD, zobacz [Eliminowanie złych haseł w organizacji](https://aka.ms/aadpasswordprotectiondocs).

---

### <a name="new-all-guests-conditional-access-policy-template-created-during-terms-of-use-creation"></a>Nowy szablon zasad "wszyscy goście" dostęp warunkowy utworzony podczas tworzenia warunków użytkowania

**Typ:** Nowa funkcja  
**Kategoria usługi:** Warunki użytkowania  
**Możliwości produktu:** Zarządzania

Podczas tworzenia warunków użytkowania tworzony jest również nowy szablon zasad dostępu warunkowego dla "wszystkich gości" i "wszystkich aplikacji". Ten nowy szablon zasad stosuje nowo utworzone Y, usprawniając proces tworzenia i wymuszania dla gości.

Aby uzyskać więcej informacji, zobacz [Warunki użytkowania usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use).

---

### <a name="new-custom-conditional-access-policy-template-created-during-terms-of-use-creation"></a>Nowy szablon zasad "niestandardowego" dostępu warunkowego utworzony podczas tworzenia warunków użytkowania

**Typ:** Nowa funkcja  
**Kategoria usługi:** Warunki użytkowania  
**Możliwości produktu:** Zarządzania

Podczas tworzenia warunków użytkowania tworzony jest również nowy szablon zasad "niestandardowy" dostęp warunkowy. Ten nowy szablon zasad umożliwia utworzenie WY, a następnie natychmiast przejdź do bloku tworzenia zasad dostępu warunkowego, bez konieczności ręcznego przechodzenia przez portal.

Aby uzyskać więcej informacji, zobacz [Warunki użytkowania usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use).

---

### <a name="new-and-comprehensive-guidance-about-deploying-azure-multi-factor-authentication"></a>Nowe i kompleksowe wskazówki dotyczące wdrażania uwierzytelniania wieloskładnikowego platformy Azure

**Typ:** Nowa funkcja  
**Kategoria usługi:** Innych  
**Możliwości produktu:** Ochrona & zabezpieczeń tożsamości
 
Wydaliśmy nowe wskazówki krok po kroku dotyczące wdrażania usługi Azure Multi-Factor Authentication (MFA) w organizacji.

Aby wyświetlić przewodnik wdrażania usługi MFA, przejdź do [repozytorium przewodników wdrażania tożsamości](https://aka.ms/DeploymentPlans) w usłudze GitHub. Aby przekazać opinię na temat przewodników wdrażania, użyj [formularza Opinie o planie wdrożenia](https://aka.ms/deploymentplanfeedback). Jeśli masz jakieś pytania dotyczące przewodników wdrażania, skontaktuj się z nami pod adresem [IDGitDeploy](mailto:idgitdeploy@microsoft.com).

---

### <a name="azure-ad-delegated-app-management-roles-are-in-public-preview"></a>Role zarządzania aplikacjami delegowanymi usługą Azure AD są dostępne w publicznej wersji zapoznawczej

**Typ:** Nowa funkcja  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwości produktu:** Kontrola dostępu

Administratorzy mogą teraz delegować zadania zarządzania aplikacjami bez przypisywania roli Administratora globalnego. Nowe role i możliwości to:

- **Nowe standardowe role administratora usługi Azure AD:**

    - **Administrator aplikacji.** Umożliwia zarządzanie wszystkimi aspektami wszystkich aplikacji, w tym rejestracją, ustawieniami logowania przyuszeń, przypisaniami aplikacji i licencjonowaniem, ustawieniami serwera proxy aplikacji i zgodą (z wyjątkiem zasobów usługi Azure AD).

    - **Administrator aplikacji w chmurze.** Udziela wszystkich możliwości administratora aplikacji, z wyjątkiem serwera proxy aplikacji, ponieważ nie zapewnia dostępu lokalnego.

    - **Deweloper aplikacji.** Daje możliwość tworzenia rejestracji aplikacji, nawet jeśli **zezwalaj użytkownikom na rejestrowanie aplikacji** opcja jest wyłączona.

- **Własność (skonfigurowana rejestracja na aplikację i aplikacja dla przedsiębiorstwa, podobnie jak proces własności grupy:**
 
    - **Właściciel rejestracji aplikacji.** Daje możliwość zarządzania wszystkimi aspektami rejestracji aplikacji, w tym manifestu aplikacji i dodawanie dodatkowych właścicieli.

    - **Właściciel aplikacji dla przedsiębiorstw.** Umożliwia zarządzanie wieloma aspektami używanych aplikacji dla przedsiębiorstw, w tym ustawieniami logowania przyuszeń, przypisaniami aplikacji i zgodą (z wyjątkiem zasobów usługi Azure AD).

Aby uzyskać więcej informacji na temat publicznej wersji zapoznawczej, zobacz [role zarządzania delegowanymi aplikacjami usługi Azure AD są w publicznej wersji zapoznawczej!](https://cloudblogs.microsoft.com/enterprisemobility/2018/06/13/hallelujah-azure-ad-delegated-application-management-roles-are-in-public-preview/) Blog. Aby uzyskać więcej informacji o rolach i uprawnieniach, zobacz [Przypisywanie ról administratora w usłudze Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal).

---

## <a name="may-2018"></a>Maj 2018 r.

### <a name="expressroute-support-changes"></a>Zmiany obsługi usługi ExpressRoute

**Typ:** Plan zmian  
**Kategoria usługi:** Uwierzytelnianie (loginy)  
**Możliwości produktu:** Platformy  

Oprogramowanie jako usługa, taka jak usługa Azure Active Directory (Azure AD) są zaprojektowane do pracy najlepiej, przechodząc bezpośrednio przez Internet, bez konieczności expressroute lub innych prywatnych tuneli sieci VPN. W związku z tym **1 sierpnia 2018**r. przestaniemy obsługiwać usługi Usługi ExpressRoute dla usługi Azure AD przy użyciu publicznej komunikacji równorzędnej platformy Azure i społeczności platformy Azure w komunikacji równorzędnej firmy Microsoft. Wszystkie usługi, których dotyczy ta zmiana, mogą zauważyć, że ruch usługi Azure AD stopniowo przechodzi z usługi ExpressRoute do Internetu.

Podczas gdy zmieniamy naszą obsługę, wiemy również, że nadal istnieją sytuacje, w których może być konieczne użycie dedykowanego zestawu obwodów dla ruchu uwierzytelniania. Z tego powodu usługa Azure AD będzie nadal obsługiwać ograniczenia zakresu adresów IP dla dzierżawy przy użyciu usługi ExpressRoute i usług już w komunikacji równorzędnej firmy Microsoft ze społecznością "Inne usługi office 365 online". Jeśli twoje usługi są dotknięte, ale potrzebujesz usługi ExpressRoute, należy wykonać następujące czynności:

- **Jeśli korzystasz z komunikacji równorzędnej na platformie Azure.** Przejdź do komunikacji równorzędnej firmy Microsoft i zarejestruj się w społeczności **innych usług Office 365 Online (12076:5100).** Aby uzyskać więcej informacji na temat przenoszenia z publicznej komunikacji równorzędnej platformy Azure do komunikacji równorzędnej firmy Microsoft, zobacz artykuł [Przenoszenie publicznej komunikacji równorzędnej do komunikacji równorzędnej firmy Microsoft.](https://docs.microsoft.com/azure/expressroute/how-to-move-peering)

- **Jeśli korzystasz z komunikacji równorzędnej firmy Microsoft.** Zarejestruj się w społeczności **innej usługi Office 365 Online (12076:5100).** Aby uzyskać więcej informacji na temat wymagań dotyczących routingu, zobacz [sekcję Obsługa społeczności BGP](https://docs.microsoft.com/azure/expressroute/expressroute-routing#bgp) w artykule Wymagania dotyczące routingu usługi ExpressRoute.

Jeśli musisz nadal korzystać z dedykowanych obwodów, musisz porozmawiać z zespołem konta Microsoft o tym, jak uzyskać autoryzację korzystania ze społeczności **innej usługi Office 365 Online (12076:5100).** Komisja rewizyjna zarządzana przez MS Office sprawdzi, czy potrzebujesz tych obwodów i upewni się, że rozumiesz techniczne implikacje ich przechowywania. Nieautoryzowane subskrypcje próbujące utworzyć filtry tras dla usługi Office 365 otrzymają komunikat o błędzie. 
 
---

### <a name="microsoft-graph-apis-for-administrative-scenarios-for-tou"></a>Interfejsy API programu Microsoft Graph dla scenariuszy administracyjnych dla wyobrażeń

**Typ:** Nowa funkcja  
**Kategoria usługi:** Warunki użytkowania  
**Możliwości produktu:** Środowisko deweloperskie
 
Dodaliśmy interfejsy API programu Microsoft Graph do administrowania warunkami użytkowania usługi Azure AD. Można tworzyć, aktualizować, usuwać warunki użytkowania obiektu.

---

### <a name="add-azure-ad-multi-tenant-endpoint-as-an-identity-provider-in-azure-ad-b2c"></a>Dodawanie punktu końcowego usługi Azure AD dla wielu dzierżaw jako dostawcy tożsamości w usłudze Azure AD B2C

**Typ:** Nowa funkcja  
**Kategoria usługi:** B2C - Zarządzanie tożsamością konsumenta  
**Możliwości produktu:** B2B/B2C
 
Korzystając z zasad niestandardowych, można teraz dodać wspólny punkt końcowy usługi Azure AD jako dostawcę tożsamości w usłudze Azure AD B2C. Dzięki temu można mieć jeden punkt wejścia dla wszystkich użytkowników usługi Azure AD, którzy logują się do aplikacji. Aby uzyskać więcej informacji, zobacz [Usługa Azure Active Directory B2C: Zezwalaj użytkownikom na logowanie się do wielodostępnego dostawcy tożsamości usługi Azure AD przy użyciu zasad niestandardowych.](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-setup-commonaad-custom)

---

### <a name="use-internal-urls-to-access-apps-from-anywhere-with-our-my-apps-sign-in-extension-and-the-azure-ad-application-proxy"></a>Używanie wewnętrznych adresów URL w celu uzyskiwania dostępu do aplikacji z dowolnego miejsca za pomocą rozszerzenia logowania moje aplikacje i serwera proxy aplikacji usługi Azure AD

**Typ:** Nowa funkcja  
**Kategoria usługi:** Moje aplikacje  
**Możliwości produktu:** Usługi rejestracji jednokrotnej
 
Użytkownicy mogą teraz uzyskiwać dostęp do aplikacji za pośrednictwem wewnętrznych adresów URL, nawet gdy są poza siecią firmową przy użyciu rozszerzenia Bezpieczne logowanie my apps dla usługi Azure AD. Będzie to działać z dowolną aplikacją, która została opublikowana przy użyciu usługi Azure AD Application Proxy, w dowolnej przeglądarce, która ma również zainstalowane rozszerzenie przeglądarki Panelu dostępu. Funkcja przekierowania adresu URL jest automatycznie włączona, gdy użytkownik zaloguje się do rozszerzenia. Rozszerzenie jest dostępne do pobrania na [Microsoft Edge,](https://go.microsoft.com/fwlink/?linkid=845176) [Chrome](https://go.microsoft.com/fwlink/?linkid=866367)i [Firefox](https://go.microsoft.com/fwlink/?linkid=866366).

---
 
### <a name="azure-active-directory---data-in-europe-for-europe-customers"></a>Usługa Azure Active Directory — dane w Europie dla klientów w Europie

**Typ:** Nowa funkcja  
**Kategoria usługi:** Innych  
**Możliwości produktu:** GoLocal (GoLocal)

Klienci w Europie wymagają, aby ich dane pozostały w Europie i nie były powielane poza europejskimi centrami danych w celu ochrony prywatności i prawa europejskiego. Ten [artykuł](https://go.microsoft.com/fwlink/?linkid=872328) zawiera szczegółowe informacje na temat tego, jakie informacje o tożsamości będą przechowywane w Europie, a także zawiera szczegółowe informacje na temat informacji, które będą przechowywane poza europejskimi centrami danych. 

---
 
### <a name="new-user-provisioning-saas-app-integrations---may-2018"></a>Integracje aplikacji saaS nowych użytkowników — maj 2018 r.

**Typ:** Nowa funkcja  
**Kategoria usługi:** Inicjowanie obsługi administracyjnej aplikacji  
**Możliwości produktu:** Integracja stron trzecich
 
Usługa Azure AD umożliwia automatyzację tworzenia, konserwacji i usuwania tożsamości użytkowników w aplikacjach SaaS, takich jak Dropbox, Salesforce, ServiceNow i innych. W maju 2018 r. dodaliśmy obsługę inicjowania obsługi administracyjnej dla następujących aplikacji w galerii aplikacji usługi Azure AD:

- [BlueJeans](https://docs.microsoft.com/azure/active-directory/active-directory-saas-bluejeans-provisioning-tutorial)

- [Cornerstone OnDemand](https://docs.microsoft.com/azure/active-directory/active-directory-saas-cornerstone-ondemand-provisioning-tutorial)

- [Zendesk](https://docs.microsoft.com/azure/active-directory/active-directory-saas-zendesk-provisioning-tutorial)

Aby uzyskać listę wszystkich aplikacji obsługujących inicjowanie [https://aka.ms/appstutorial](https://aka.ms/appstutorial)obsługi administracyjnej użytkowników w galerii usługi Azure AD, zobacz .

---
 
### <a name="azure-ad-access-reviews-of-groups-and-app-access-now-provides-recurring-reviews"></a>Przeglądy dostępu usługi Azure AD grup i dostępu do aplikacji zapewniają teraz cykliczne przeglądy

**Typ:** Nowa funkcja  
**Kategoria usługi:** Recenzje programu Access  
**Możliwości produktu:** Zarządzania
 
Przegląd dostępu grup i aplikacji jest teraz ogólnie dostępny w ramach usługi Azure AD Premium P2.  Administratorzy będą mogli skonfigurować przeglądy dostępu członkostwa w grupach i przydziałów aplikacji, aby automatycznie powtarzać się w regularnych odstępach czasu, takich jak miesięczne lub kwartalne.

---

### <a name="azure-ad-activity-logs-sign-ins-and-audit-are-now-available-through-ms-graph"></a>Dzienniki aktywności usługi Azure AD (logowania i inspekcje) są teraz dostępne za pośrednictwem programu MS Graph

**Typ:** Nowa funkcja  
**Kategoria usługi:** Reporting  
**Możliwości produktu:** Monitorowanie & raportowania
 
Dzienniki aktywności usługi Azure AD, które obejmują logowania i dzienniki inspekcji, są teraz dostępne za pośrednictwem interfejsu API programu Microsoft Graph. Firma Microsoft ujawnił dwa punkty końcowe za pośrednictwem interfejsu API programu Microsoft Graph, aby uzyskać dostęp do tych dzienników. Zapoznaj się z naszymi [dokumentami,](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-getting-started-azure-portal) aby uzyskać programowy dostęp do interfejsów API raportowania usługi Azure AD, aby rozpocząć. 

---
 
### <a name="improvements-to-the-b2b-redemption-experience-and-leave-an-org"></a>Ulepszenia doświadczenia w zakresie realizacji B2B i pozostawienie

**Typ:** Nowa funkcja  
**Kategoria usługi:** B2b  
**Możliwości produktu:** B2B/B2C

**W sam raz odkupienie:** Po udostępnieniu zasobu użytkownikowi-gościowi korzystającemu z interfejsu API B2B — nie trzeba wysyłać specjalnej wiadomości e-mail z zaproszeniem. W większości przypadków użytkownik-gość może uzyskać dostęp do zasobu i zostanie przejęty przez środowisko realizacji w samą porę. Koniec z wpływem z powodu nieodebranych wiadomości e-mail. Koniec z pytaniem użytkowników-gości "Czy kliknąłeś na ten link realizacji, który wysłał Ci system?". Oznacza to, że gdy SPO używa menedżera zaproszeń - mętne załączniki mogą mieć ten sam kanoniczny adres URL dla wszystkich użytkowników - wewnętrznych i zewnętrznych - w dowolnym stanie realizacji.

**Nowoczesne doświadczenie odkupienia:** Koniec ze stroną docelową realizacji podzielonego ekranu. Użytkownicy zobaczą nowoczesne środowisko zgody z zasadami zachowania poufności informacji organizacji zapraszania, podobnie jak w przypadku aplikacji innych firm.

**Użytkownicy-goście mogą opuścić oręt:** Po zakończeniu relacji użytkownika z organizacją może on samowystarczają się odejść z organizacji. Koniec z wzywaniem administratora organizacji zapraszającej do "usunięcia", nie więcej podnoszenia biletów pomocy technicznej.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---may-2018"></a>Nowe aplikacje federacyjne dostępne w galerii aplikacji usługi Azure AD — maj 2018 r.

**Typ:** Nowa funkcja  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwości produktu:** Integracja stron trzecich
 
W maju 2018 r. dodaliśmy te 18 nowych aplikacji z obsługą federacji do naszej galerii aplikacji:

[AwardSpring](https://docs.microsoft.com/azure/active-directory/active-directory-saas-awardspring-tutorial), Infogix Data3Sixty rządzić, [iWellnessNow](https://docs.microsoft.com/azure/active-directory/active-directory-saas-iwellnessnow-tutorial) [Yodeck](https://docs.microsoft.com/azure/active-directory/active-directory-saas-infogix-tutorial), [PlanGrid](https://docs.microsoft.com/azure/active-directory/active-directory-saas-plangrid-tutorial) [Jamf Pro](https://docs.microsoft.com/azure/active-directory/active-directory-saas-jamfprosamlconnector-tutorial), [KnowledgeOwl](https://docs.microsoft.com/azure/active-directory/active-directory-saas-knowledgeowl-tutorial), [Envi MMIS](https://docs.microsoft.com/azure/active-directory/active-directory-saas-envimmis-tutorial) [Proxyclick](https://docs.microsoft.com/azure/active-directory/active-directory-saas-proxyclick-tutorial), [LaunchDarkly](https://docs.microsoft.com/azure/active-directory/active-directory-saas-launchdarkly-tutorial), [Adobe Captivate Prime](https://docs.microsoft.com/azure/active-directory/active-directory-saas-adobecaptivateprime-tutorial), [Montage Online](https://docs.microsoft.com/azure/active-directory/active-directory-saas-montageonline-tutorial),[中田田田田田](https://docs.microsoft.com/azure/active-directory/active-directory-saas-manabipocket-tutorial)田田, [Riskware](https://docs.microsoft.com/azure/active-directory/active-directory-saas-riskware-tutorial), [Flock](https://docs.microsoft.com/azure/active-directory/active-directory-saas-flock-tutorial) [Arc Publishing - SSO](https://docs.microsoft.com/azure/active-directory/active-directory-saas-arc-tutorial), [Recenzje](https://docs.microsoft.com/azure/active-directory/active-directory-saas-reviewsnap-tutorial)

Aby uzyskać więcej informacji na temat aplikacji, zobacz [Integracja aplikacji SaaS z usługą Azure Active Directory](https://aka.ms/appstutorial).

Aby uzyskać więcej informacji na temat wyświetlania aplikacji w galerii aplikacji usługi Azure AD, zobacz [Lista aplikacji w galerii aplikacji usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing).

---
 
### <a name="new-step-by-step-deployment-guides-for-azure-active-directory"></a>Nowe przewodniki po wdrażaniu krok po kroku dla usługi Azure Active Directory

**Typ:** Nowa funkcja  
**Kategoria usługi:** Innych  
**Możliwości produktu:** Katalogu
 
Nowe wskazówki krok po kroku dotyczące wdrażania usługi Azure Active Directory (Azure AD), w tym samoobsługowego resetowania haseł (SSPR), logowania jednokrotnego (SSO), dostępu warunkowego (CA), serwera proxy aplikacji, inicjowania obsługi administracyjnej użytkownika, usług federacyjnych Active Directory (ADFS) do uwierzytelniania przekazywanego (PTA) i synchronizacji skrótu usługi ADFS (PHS).

Aby wyświetlić przewodniki wdrażania, przejdź do repozytorium [przewodników wdrażania tożsamości](https://aka.ms/DeploymentPlans) w usłudze GitHub. Aby przekazać opinię na temat przewodników wdrażania, użyj [formularza Opinie o planie wdrożenia](https://aka.ms/deploymentplanfeedback). Jeśli masz jakieś pytania dotyczące przewodników wdrażania, skontaktuj się z nami pod adresem [IDGitDeploy](mailto:idgitdeploy@microsoft.com).

---

### <a name="enterprise-applications-search---load-more-apps"></a>Wyszukiwanie aplikacji dla przedsiębiorstw — ładowanie większej liczby aplikacji

**Typ:** Nowa funkcja  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwości produktu:** Usługi rejestracji jednokrotnej
 
Masz problemy ze znalezieniem aplikacji / zleceniodawców usług? Dodaliśmy możliwość ładowania większej liczby aplikacji w aplikacjach korporacyjnych na liście wszystkich aplikacji. Domyślnie pokazujemy 20 aplikacji. Teraz możesz kliknąć, **Załaduj więcej,** aby wyświetlić dodatkowe aplikacje. 

---
 
### <a name="the-may-release-of-aadconnect-contains-a-public-preview-of-the-integration-with-pingfederate-important-security-updates-many-bug-fixes-and-new-great-new-troubleshooting-tools"></a>Majowa wersja AADConnect zawiera publiczną wersję zapoznawczą integracji z PingFederate, ważne aktualizacje zabezpieczeń, wiele poprawek błędów i nowe nowe, świetne narzędzia do rozwiązywania problemów. 

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Połączenie usługi AD  
**Możliwości produktu:** Zarządzanie cyklem życia tożsamości
 
Majowa wersja AADConnect zawiera publiczną wersję zapoznawczą integracji z PingFederate, ważne aktualizacje zabezpieczeń, wiele poprawek błędów i nowe nowe, świetne narzędzia do rozwiązywania problemów. Informacje o wersji można znaleźć [tutaj](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-version-history#118190).

---

### <a name="azure-ad-access-reviews-auto-apply"></a>Przeglądy dostępu usługi Azure AD: automatyczne stosowanie

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Recenzje programu Access  
**Możliwości produktu:** Zarządzania

Przeglądy dostępu grup i aplikacji są teraz ogólnie dostępne w ramach usługi Azure AD Premium P2. Administrator może skonfigurować automatyczne stosowanie zmian recenzenta do tej grupy lub aplikacji po zakończeniu przeglądu dostępu. Administrator może również określić, co stanie się z ciągłym dostępem użytkownika, jeśli recenzenci nie odpowiedzieli, nie usunęła dostępu, zachowaj dostęp lub zapoznała się z zaleceniami systemowymi. 

---

### <a name="id-tokens-can-no-longer-be-returned-using-the-query-response_mode-for-new-apps"></a>Tokeny identyfikatorów nie mogą być już zwracane przy użyciu response_mode zapytania dla nowych aplikacji. 

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Uwierzytelnianie (loginy)  
**Możliwości produktu:** Uwierzytelnianie użytkownika
 
Aplikacje utworzone w dniu lub po 25 kwietnia 2018 r. nie będą już mogły żądać **id_token** przy użyciu **response_mode kwerendy.**  Spowoduje to wyświetlenie narzędzia usługi Azure AD ze specyfikacjami OIDC i pomaga zmniejszyć obszar ataku aplikacji.  Aplikacje utworzone przed 25 kwietnia 2018 r. nie mogą korzystać z **zapytania** response_mode z response_type **id_token**.  Zwracany błąd podczas żądania id_token od usługi AAD jest **AADSTS70007: "query" nie jest obsługiwaną wartością "response_mode" podczas żądania tokenu.**

**Fragment** i **form_post** response_modes kontynuować pracę — podczas tworzenia nowych obiektów aplikacji (na przykład dla użycia serwera proxy aplikacji), upewnij się, że korzystanie z jednego z tych response_modes przed utworzeniem nowej aplikacji.  

---
 
## <a name="april-2018"></a>Kwiecień 2018 r. 

### <a name="azure-ad-b2c-access-token-are-ga"></a>Token dostępu usługi Azure AD B2C to ga

**Typ:** Nowa funkcja  
**Kategoria usługi:** B2C - Zarządzanie tożsamością konsumenta  
**Możliwości produktu:** B2B/B2C 

Teraz można uzyskać dostęp do interfejsów API sieci Web zabezpieczonych przez usługę Azure AD B2C przy użyciu tokenów dostępu. Funkcja przechodzi z publicznego podglądu do ga. UI środowiska konfigurowania aplikacji usługi Azure AD B2C i interfejsów API sieci Web została ulepszona i wprowadzono inne drobne ulepszenia.
 
Aby uzyskać więcej informacji, zobacz [Usługa Azure AD B2C: Żądanie tokenów dostępu](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-access-tokens).

---

### <a name="test-single-sign-on-configuration-for-saml-based-applications"></a>Testowanie konfiguracji logowania jednokrotnego dla aplikacji opartych na saml

**Typ:** Nowa funkcja  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwości produktu:** Usługi rejestracji jednokrotnej

Podczas konfigurowania aplikacji SSO opartych na SAML można przetestować integrację na stronie konfiguracji. Jeśli wystąpi błąd podczas logowania, można podać błąd w doświadczeniu testowania i usługi Azure AD zapewnia kroki rozwiązywania problemów, aby rozwiązać określony problem.

Aby uzyskać więcej informacji, zobacz:

- [Konfigurowanie logowania jednokrotnego do aplikacji, które nie znajdują się w galerii aplikacji Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-custom-apps)
- [Jak debugować aplikacje logowania jednokrotnego oparte na technologii SAML w usłudze Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-debugging)

---
 
### <a name="azure-ad-terms-of-use-now-has-per-user-reporting"></a>Warunki użytkowania usługi Azure AD mają teraz raportowanie dla użytkowników

**Typ:** Nowa funkcja  
**Kategoria usługi:** Warunki użytkowania  
**Możliwości produktu:** Zgodności
 
Administratorzy mogą teraz wybrać daną WY i zobaczyć wszystkich użytkowników, którzy wyrazili zgodę na to Wcześniej i datę /godzinę.

Aby uzyskać więcej informacji, zobacz [funkcję warunków użytkowania usługi Azure AD](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use).

---
 
### <a name="azure-ad-connect-health-risky-ip-for-ad-fs-extranet-lockout-protection"></a>Kondycja połączenia usługi Azure AD: ryzykowny adres IP dla ochrony blokady pozasieci usług AD FS 

**Typ:** Nowa funkcja  
**Kategoria usługi:** Innych  
**Możliwości produktu:** Monitorowanie & raportowania

Connect Health obsługuje teraz możliwość wykrywania adresów IP, które przekraczają próg nieudanych logowania U/P co godzinę lub codziennie. Możliwości oferowane przez tę funkcję to:

- Kompleksowy raport pokazujący adres IP i liczbę nieudanych logowań generowanych co godzinę/ codziennie z konfigurowalnym progiem.
- Alerty oparte na wiadomościach e-mail pokazujące, że określony adres IP przekroczył próg nieudanych logowania U/P co godzinę/codziennie.
- Opcja pobierania w celu szczegółowej analizy danych

Aby uzyskać więcej informacji, zobacz [Raport ryzykownych adresów IP](https://aka.ms/aadchriskyip).

---
 
### <a name="easy-app-config-with-metadata-file-or-url"></a>Łatwa konfigura aplikacji z plikiem metadanych lub adresem URL

**Typ:** Nowa funkcja  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwości produktu:** Usługi rejestracji jednokrotnej

Na stronie Aplikacje enterprise administratorzy mogą przekazać plik metadanych SAML w celu skonfigurowania logowania opartego na SAML dla aplikacji AAD Gallery i Non-Gallery.

Ponadto można użyć adresu URL metadanych federacji aplikacji usługi Azure AD, aby skonfigurować sytuowanie sytuacyjne za pomocą aplikacji docelowej.

Aby uzyskać więcej informacji, zobacz [Konfigurowanie logowania jednokrotnego do aplikacji, które nie znajdują się w galerii aplikacji usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-custom-apps).

---

### <a name="azure-ad-terms-of-use-now-generally-available"></a>Warunki użytkowania usługi Azure AD są teraz ogólnie dostępne

**Typ:** Nowa funkcja  
**Kategoria usługi:** Warunki użytkowania  
**Możliwości produktu:** Zgodności
 

Warunki użytkowania usługi Azure AD zostały przeniesione z publicznej wersji zapoznawczej do ogólnie dostępne.

Aby uzyskać więcej informacji, zobacz [funkcję warunków użytkowania usługi Azure AD](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use).

---

### <a name="allow-or-block-invitations-to-b2b-users-from-specific-organizations"></a>Zezwalanie na zaproszenia lub ich blokowanie dla użytkowników B2B z określonych organizacji

**Typ:** Nowa funkcja  
**Kategoria usługi:** B2b  
**Możliwości produktu:** B2B/B2C
 

Teraz można określić, które organizacje partnerskie, które chcesz udostępnić i współpracować z w usłudze Azure AD B2B Collaboration. Aby to zrobić, można utworzyć listę określonych domen zezwalania lub odrzucania. Gdy domena jest zablokowana przy użyciu tych funkcji, pracownicy nie mogą już wysyłać zaproszeń do osób w tej domenie.

Pomaga to kontrolować dostęp do zasobów, umożliwiając jednocześnie płynne środowisko dla zatwierdzonych użytkowników.

Ta funkcja współpracy B2B jest dostępna dla wszystkich klientów usługi Azure Active Directory i może być używana w połączeniu z funkcjami usługi Azure AD Premium, takimi jak dostęp warunkowy i ochrona tożsamości, aby uzyskać bardziej szczegółową kontrolę nad tym, kiedy i jak zewnętrzni użytkownicy biznesowi logują się i uzyskują dostęp.

Aby uzyskać więcej informacji, zobacz [Zezwalanie lub blokowanie zaproszeń do użytkowników B2B z określonych organizacji](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-allow-deny-list).

---
 
### <a name="new-federated-apps-available-in-azure-ad-app-gallery"></a>Nowe aplikacje federacyjne dostępne w galerii aplikacji usługi Azure AD

**Typ:** Nowa funkcja  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwości produktu:** Integracja stron trzecich

W kwietniu 2018 r. dodaliśmy te 13 nowych aplikacji z obsługą federacji do naszej galerii aplikacji:

Kryterium HCM, [FiscalNote](https://docs.microsoft.com/azure/active-directory/active-directory-saas-fiscalnote-tutorial), [Secret Server (on-premises),](https://docs.microsoft.com/azure/active-directory/active-directory-saas-secretserver-on-premises-tutorial) [Sygnał dynamiczny](https://docs.microsoft.com/azure/active-directory/active-directory-saas-dynamicsignal-tutorial), [mindWireless](https://docs.microsoft.com/azure/active-directory/active-directory-saas-mindwireless-tutorial), [OrgChart Now](https://docs.microsoft.com/azure/active-directory/active-directory-saas-orgchartnow-tutorial), [Ziflow](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ziflow-tutorial), [AppNeta Performance Monitor](https://docs.microsoft.com/azure/active-directory/active-directory-saas-appneta-tutorial), [Elium](https://docs.microsoft.com/azure/active-directory/active-directory-saas-elium-tutorial) , [Fluxx Labs](https://docs.microsoft.com/azure/active-directory/active-directory-saas-fluxxlabs-tutorial), [Cisco Cloud](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ciscocloud-tutorial), półka, [SafetyNet](https://docs.microsoft.com/azure/active-directory/active-directory-saas-safetynet-tutorial)

Aby uzyskać więcej informacji na temat aplikacji, zobacz [Integracja aplikacji SaaS z usługą Azure Active Directory](https://aka.ms/appstutorial).

Aby uzyskać więcej informacji na temat wyświetlania aplikacji w galerii aplikacji usługi Azure AD, zobacz [Lista aplikacji w galerii aplikacji usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing).

---
 
### <a name="grant-b2b-users-in-azure-ad-access-to-your-on-premises-applications-public-preview"></a>Udzielanie użytkownikom B2B w usłudze Azure AD dostępu do aplikacji lokalnych (publiczna wersja zapoznawcza)

**Typ:** Nowa funkcja  
**Kategoria usługi:** B2b  
**Możliwości produktu:** B2B/B2C

Jako organizacja korzystająca z funkcji współpracy B2B usługi Azure Active Directory (Azure AD) do zapraszania użytkowników-gości z organizacji partnerskich do usługi Azure AD, możesz teraz zapewnić tym użytkownikom B2B dostęp do aplikacji lokalnych. Te aplikacje lokalne mogą używać uwierzytelniania opartego na SAML lub zintegrowanego uwierzytelniania systemu Windows (IWA) z delegowaniem z ograniczeniami protokołu Kerberos (KCD).

Aby uzyskać więcej informacji, zobacz [Udzielanie użytkownikom B2B w usłudze Azure AD dostępu do aplikacji lokalnych.](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-hybrid-cloud-to-on-premises)

---
 
### <a name="get-sso-integration-tutorials-from-the-azure-marketplace"></a>Pobierz samouczki dotyczące integracji z sytuacyjami z portalu Azure Marketplace

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Innych  
**Możliwości produktu:** Integracja stron trzecich

Jeśli aplikacja, która jest wymieniona w [portalu Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps?page=1) obsługuje saml oparte logowania jednokrotnego, kliknięcie **przycisku Pobierz teraz** zapewnia samouczek integracji skojarzone z tej aplikacji. 

---

### <a name="faster-performance-of-azure-ad-automatic-user-provisioning-to-saas-applications"></a>Większa wydajność automatycznego inicjowania obsługi administracyjnej usługi Azure AD w aplikacjach SaaS

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Inicjowanie obsługi administracyjnej aplikacji  
**Możliwości produktu:** Integracja stron trzecich
 
Wcześniej klienci korzystający z łączników inicjowania obsługi administracyjnej dla aplikacji SaaS (na przykład Salesforce, ServiceNow i Box) mogą uzyskać powolną wydajność, jeśli ich dzierżawy usługi Azure AD zawierały ponad 100 000 połączonych użytkowników i grup, a oni używali przypisań użytkowników i grup do określenia, którzy użytkownicy powinni być aprowowani.

W dniu 2 kwietnia 2018 r. wdrożono znaczące ulepszenia wydajności w usłudze inicjowania obsługi administracyjnej usługi Azure AD, które znacznie skracają czas potrzebny do wykonywania początkowych synchronizacji między usługą Azure Active Directory a docelowymi aplikacjami SaaS.

W rezultacie wielu klientów, którzy mieli początkowe synchronizacje z aplikacjami, które trwały wiele dni lub nigdy nie zostały ukończone, kończy się teraz w ciągu kilku minut lub godzin.

Aby uzyskać więcej informacji, zobacz [Co się dzieje podczas inicjowania obsługi administracyjnej?](/azure//active-directory/app-provisioning/how-provisioning-works)

---

### <a name="self-service-password-reset-from-windows-10-lock-screen-for-hybrid-azure-ad-joined-machines"></a>Samoobsługowe resetowanie hasła z ekranu blokady systemu Windows 10 dla hybrydowych maszyn przyłączonych do usługi Azure AD

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Samoobsługowe resetowanie hasła  
**Możliwości produktu:** Uwierzytelnianie użytkownika
 
Zaktualizowaliśmy funkcję SSPR systemu Windows 10, aby uwzględnić obsługę maszyn, które są połączone z hybrydową usługą Azure AD. Ta funkcja jest dostępna w systemie Windows 10 RS4 umożliwia użytkownikom resetowanie hasła z ekranu blokady komputera z systemem Windows 10. Użytkownicy, którzy są włączeni i zarejestrowani do samodzielnego resetowania hasła, mogą korzystać z tej funkcji.

Aby uzyskać więcej informacji, zobacz [Resetowanie hasła usługi Azure AD z ekranu logowania](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-sspr-windows).

---

## <a name="march-2018"></a>Marzec 2018 r.
 
### <a name="certificate-expire-notification"></a>Powiadomienie o wygaśnięciu certyfikatu

**Typ:** Stałe  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwości produktu:** Usługi rejestracji jednokrotnej
 
Usługa Azure AD wysyła powiadomienie, gdy certyfikat dla aplikacji galerii lub spoza galerii wkrótce wygaśnie. 

Niektórzy użytkownicy nie otrzymywali powiadomień dla aplikacji dla przedsiębiorstw skonfigurowanych dla logowania jednokrotnego opartego na saml. Ten problem został rozwiązany. Usługa Azure AD wysyła powiadomienie o certyfikatach wygasających w ciągu 7, 30 i 60 dni. To zdarzenie można zobaczyć w dziennikach inspekcji. 

Aby uzyskać więcej informacji, zobacz:

- [Zarządzanie certyfikatami dla federacyjnego logowania jednokrotnego w usłudze Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-sso-certs)
- [Raporty dotyczące inspekcji w portalu usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-audit-logs)
 
---
 
### <a name="twitter-and-github-identity-providers-in-azure-ad-b2c"></a>Dostawcy tożsamości twittera i gitu w usłudze Azure AD B2C

**Typ:** Nowa funkcja  
**Kategoria usługi:** B2C - Zarządzanie tożsamością konsumenta  
**Możliwości produktu:** B2B/B2C
 
Teraz można dodać Twitter lub GitHub jako dostawcy tożsamości w usłudze Azure AD B2C. Twitter przechodzi z publicznego podglądu do GA. GitHub jest wydawany w publicznej wersji zapoznawczej.

Aby uzyskać więcej informacji, zobacz [Co to jest współpraca usługi Azure AD B2B?](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b).
 
---

### <a name="restrict-browser-access-using-intune-managed-browser-with-azure-ad-application-based-conditional-access-for-ios-and-android"></a>Ograniczanie dostępu do przeglądarki przy użyciu zarządzanej przeglądarki usługi Intune za pomocą dostępu warunkowego opartego na usłudze Azure AD dla systemów iOS i Android

**Typ:** Nowa funkcja  
**Kategoria usługi:** Dostęp warunkowy  
**Możliwości produktu:** Ochrona & zabezpieczeń tożsamości
 
**Teraz w publicznej wersji zapoznawczej!**

**SSO sytuaj zarządzania przeglądarką usługi Intune:** Pracownicy mogą używać logowania jednokrotnego na klientach natywnych (takich jak Microsoft Outlook) i przeglądarki zarządzanej usługi Intune dla wszystkich aplikacji połączonych z usługą Azure AD.

**Obsługa dostępu warunkowego zarządzanego w usłudze Intune:** Teraz można wymagać od pracowników korzystania z zarządzanej przeglądarki usługi Intune przy użyciu zasad dostępu warunkowego opartego na aplikacjach.

Przeczytaj więcej na ten temat w naszym [blogu](https://cloudblogs.microsoft.com/enterprisemobility/2018/03/15/the-intune-managed-browser-now-supports-azure-ad-sso-and-conditional-access/).

Aby uzyskać więcej informacji, zobacz:

- [Konfigurowanie dostępu warunkowego opartego na aplikacji](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)

- [Konfigurowanie zasad przeglądarki zarządzanej](https://aka.ms/managedbrowser)  

---
 
### <a name="app-proxy-cmdlets-in-powershell-ga-module"></a>Polecenia cmdlet serwera proxy aplikacji w module GA programu PowerShell

**Typ:** Nowa funkcja  
**Kategoria usługi:** Serwer proxy aplikacji  
**Możliwości produktu:** Kontrola dostępu
 
Obsługa poleceń cmdlet serwera proxy aplikacji jest teraz dostępna w module GA programu PowerShell! Wymaga to, aby być na bieżąco z modułami programu PowerShell — jeśli masz więcej niż rok w tyle, niektóre polecenia cmdlet mogą przestać działać. 

Aby uzyskać więcej informacji, zobacz [AzureAD](https://docs.microsoft.com/powershell/module/Azuread/?view=azureadps-2.0).
 
---
 
### <a name="office-365-native-clients-are-supported-by-seamless-sso-using-a-non-interactive-protocol"></a>Klienci natywni usługi Office 365 są obsługiwani przez bezproblemowe logowanie jednokrotne przy użyciu protokołu nieinterakcyjnego

**Typ:** Nowa funkcja  
**Kategoria usługi:** Uwierzytelnianie (loginy)  
**Możliwości produktu:** Uwierzytelnianie użytkownika
 
Użytkownik korzystający z klientów natywnych usługi Office 365 (wersja 16.0.8730.xxxx i powyżej) uzyskuje ciche logowanie przy użyciu bezproblemowego logowania jednokrotnego. Ta obsługa jest dostarczana przez dodanie protokołu nieinterakcyjnego (WS-Trust) do usługi Azure AD.

Aby uzyskać więcej informacji, zobacz [Jak działa logowanie na kliencie macierzystym przy bezproblemowym logowanie jednokrotne?](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso-how-it-works#how-does-sign-in-on-a-native-client-with-seamless-sso-work)
 
---

### <a name="users-get-a-silent-sign-on-experience-with-seamless-sso-if-an-application-sends-sign-in-requests-to-azure-ads-tenant-endpoints"></a>Użytkownicy otrzymują ciche środowisko logowania z bezproblemowym logowaniem jednokrotnym, jeśli aplikacja wysyła żądania logowania do punktów końcowych dzierżawy usługi Azure AD

**Typ:** Nowa funkcja  
**Kategoria usługi:** Uwierzytelnianie (loginy)  
**Możliwości produktu:** Uwierzytelnianie użytkownika
 
Użytkownicy otrzymują ciche środowisko logowania z bezproblemowym logowaniem jednokrotnym, jeśli aplikacja `https://contoso.sharepoint.com`(na przykład) wysyła żądania logowania `https://login.microsoftonline.com/contoso.com/<..>` `https://login.microsoftonline.com/<tenant_ID>/<..>` do punktów końcowych dzierżawy usługi Azure`https://login.microsoftonline.com/common/<...>`AD — czyli lub — zamiast wspólnego punktu końcowego usługi Azure AD ( ).

Aby uzyskać więcej informacji, zobacz [Azure Active Directory Bezproblemowe logowanie jednokrotne](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso). 

---
 
### <a name="need-to-add-only-one-azure-ad-url-instead-of-two-urls-previously-to-users-intranet-zone-settings-to-roll-out-seamless-sso"></a>Aby wdrożyć bezproblemowe logowanie typu SSO, należy dodać tylko jeden adres URL usługi Azure AD, a nie dwa adresy URL wcześniej.

**Typ:** Nowa funkcja  
**Kategoria usługi:** Uwierzytelnianie (loginy)  
**Możliwości produktu:** Uwierzytelnianie użytkownika
 
Aby wdrożyć bezproblemowe logowanie jednokrotne dla użytkowników, musisz dodać tylko jeden adres URL usługi Azure AD `https://autologon.microsoftazuread-sso.com`do ustawień strefy intranetu użytkowników przy użyciu zasad grupy w usłudze Active Directory: . Wcześniej klienci musieli dodać dwa adresy URL.

Aby uzyskać więcej informacji, zobacz [Azure Active Directory Bezproblemowe logowanie jednokrotne](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso). 
 
---
 
### <a name="new-federated-apps-available-in-azure-ad-app-gallery"></a>Nowe aplikacje federacyjne dostępne w galerii aplikacji usługi Azure AD

**Typ:** Nowa funkcja  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwości produktu:** Integracja stron trzecich

W marcu 2018 r. dodaliśmy te 15 nowych aplikacji z obsługą federacji do naszej galerii aplikacji:

[Boxcryptor](https://docs.microsoft.com/azure/active-directory/active-directory-saas-boxcryptor-tutorial), [CylancePROTECT](https://docs.microsoft.com/azure/active-directory/active-directory-saas-cylanceprotect-tutorial), Wrike, [SignalFx](https://docs.microsoft.com/azure/active-directory/active-directory-saas-signalfx-tutorial), Asystent przez FirstAgenda, [YardiOne](https://docs.microsoft.com/azure/active-directory/active-directory-saas-yardione-tutorial), Vtiger CRM, inwink, [Amplituda](https://docs.microsoft.com/azure/active-directory/active-directory-saas-amplitude-tutorial), [Spacio](https://docs.microsoft.com/azure/active-directory/active-directory-saas-spacio-tutorial), [ContractWorks](https://docs.microsoft.com/azure/active-directory/active-directory-saas-contractworks-tutorial), [Bersin](https://docs.microsoft.com/azure/active-directory/active-directory-saas-bersin-tutorial), [Mercell](https://docs.microsoft.com/azure/active-directory/active-directory-saas-mercell-tutorial), [Trisotech Digital Enterprise Server](https://docs.microsoft.com/azure/active-directory/active-directory-saas-trisotechdigitalenterpriseserver-tutorial), [Qumu Cloud](https://docs.microsoft.com/azure/active-directory/active-directory-saas-qumucloud-tutorial).
 
Aby uzyskać więcej informacji na temat aplikacji, zobacz [Integracja aplikacji SaaS z usługą Azure Active Directory](https://aka.ms/appstutorial).

Aby uzyskać więcej informacji na temat wyświetlania aplikacji w galerii aplikacji usługi Azure AD, zobacz [Lista aplikacji w galerii aplikacji usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing). 

---
 
### <a name="pim-for-azure-resources-is-generally-available"></a>Usługa PIM dla zasobów platformy Azure jest ogólnie dostępna

**Typ:** Nowa funkcja  
**Kategoria usługi:** Zarządzanie tożsamościami uprzywilejowanymi  
**Możliwości produktu:** Zarządzanie tożsamościami uprzywilejowanymi
 
Jeśli używasz usługi Azure AD Privileged Identity Management dla ról katalogów, można teraz użyć funkcji dostępu i przydziału powiązanego z czasem usługi PIM dla ról zasobów platformy Azure, takich jak subskrypcje, grupy zasobów, maszyny wirtualne i inne zasoby obsługiwane przez usługę Azure Resource Manager. Wymuszaj uwierzytelnianie wieloskładnikowe podczas aktywowania ról Just-In-Time i planuj aktywacje w koordynacji z zatwierdzonymi oknami zmian. Ponadto w tej wersji dodano ulepszenia niedostępne podczas publicznej wersji zapoznawczej, w tym zaktualizowany interfejs użytkownika, przepływy pracy zatwierdzania i możliwość rozszerzenia role wygasające wkrótce i odnowić wygasłe role.

Aby uzyskać więcej informacji, zobacz [PIM dla zasobów platformy Azure (Wersja zapoznawcza)](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/azure-pim-resource-rbac)
 
---
 
### <a name="adding-optional-claims-to-your-apps-tokens-public-preview"></a>Dodawanie oświadczeń opcjonalnych do tokenów aplikacji (publiczna wersja zapoznawcza)

**Typ:** Nowa funkcja  
**Kategoria usługi:** Uwierzytelnianie (loginy)  
**Możliwości produktu:** Uwierzytelnianie użytkownika
 
Aplikacja usługi Azure AD może teraz żądać oświadczeń niestandardowych lub opcjonalnych w znakach JWTs lub SAML.  Są to oświadczenia dotyczące użytkownika lub dzierżawy, które nie są domyślnie uwzględnione w tokenie ze względu na ograniczenia rozmiaru lub stosowania.  Jest to obecnie w publicznej wersji zapoznawczej dla aplikacji usługi Azure AD w punktach końcowych w wersji 1.0 i 2.0.  Zobacz dokumentację, aby uzyskać informacje na temat oświadczeń, które można dodać i jak edytować manifest aplikacji, aby je zażądać.  

Aby uzyskać więcej informacji, zobacz [Oświadczenia opcjonalne w usłudze Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-optional-claims).
 
---
 
### <a name="azure-ad-supports-pkce-for-more-secure-oauth-flows"></a>Usługa Azure AD obsługuje protokół PKCE w celu zapewnienia bezpieczniejszych przepływów OAuth

**Typ:** Nowa funkcja  
**Kategoria usługi:** Uwierzytelnianie (loginy)  
**Możliwości produktu:** Uwierzytelnianie użytkownika
 
Dokumenty usługi Azure AD zostały zaktualizowane w celu zanotować obsługę PKCE, co pozwala na bezpieczniejszą komunikację podczas przepływu udzielania dotacji kodu autoryzacji OAuth 2.0.  Zarówno S256, jak i code_challenges w postaci zwykłego tekstu są obsługiwane w punktach końcowych w wersji 1.0 i v2.0. 

Aby uzyskać więcej informacji, zobacz [Żądanie kodu autoryzacji](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-protocols-oauth-code#request-an-authorization-code). 
 
---
 
### <a name="support-for-provisioning-all-user-attribute-values-available-in-the-workday-get_workers-api"></a>Obsługa inicjowania obsługi administracyjnej wszystkich wartości atrybutów użytkownika dostępnych w interfejsie API Get_Workers dnia roboczego

**Typ:** Nowa funkcja  
**Kategoria usługi:** Inicjowanie obsługi administracyjnej aplikacji  
**Możliwości produktu:** Integracja stron trzecich
 
Publiczny podgląd inicjowania obsługi administracyjnej ruchu przychodzącego z usługi Workday do usługi Active Directory i usługi Azure AD obsługuje teraz możliwość wyodrębniania i inicjowania obsługi administracyjnej wszystkich wartości atrybutów dostępnych w interfejsie API Get_Workers Workday. Spowoduje to dodanie obsługuje setki dodatkowych atrybutów standardowych i niestandardowych poza te dostarczane z początkową wersją łącznika inicjowania obsługi administracyjnej ruchu przychodzącego Workday.

Aby uzyskać więcej informacji, zobacz: [Dostosowywanie listy atrybutów użytkownika dnia roboczego](https://docs.microsoft.com/azure/active-directory/active-directory-saas-workday-inbound-tutorial#customizing-the-list-of-workday-user-attributes)

---

### <a name="changing-group-membership-from-dynamic-to-static-and-vice-versa"></a>Zmiana członkostwa w grupach z dynamicznego na statyczny i odwrotnie

**Typ:** Nowa funkcja  
**Kategoria usługi:** Zarządzanie grupą  
**Możliwości produktu:** Współpracy
 
Można zmienić sposób zarządzania członkostwem w grupie. Jest to przydatne, gdy chcesz zachować tę samą nazwę grupy i identyfikator w systemie, więc wszystkie istniejące odwołania do grupy są nadal ważne; utworzenie nowej grupy wymagałoby aktualizacji tych odwołań.
Zaktualizowaliśmy centrum administracyjne usługi Azure AD w celu obsługi tej funkcji. Teraz klienci mogą konwertować istniejące grupy z członkostwa dynamicznego na przypisane członkostwo i odwrotnie. Istniejące polecenia cmdlet programu PowerShell są nadal dostępne.

Aby uzyskać więcej informacji, zobacz [Dynamiczne reguły członkostwa dla grup w usłudze Azure Active Directory](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership)

---

### <a name="improved-sign-out-behavior-with-seamless-sso"></a>Poprawiono zachowanie wylogowywania za pomocą bezproblemowego logowania jednokrotnego

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Uwierzytelnianie (loginy)  
**Możliwości produktu:** Uwierzytelnianie użytkownika
 
Wcześniej, nawet jeśli użytkownicy jawnie wylogowali się z aplikacji zabezpieczonej przez usługę Azure AD, zostaną automatycznie zalogowani z powrotem przy użyciu bezproblemowego logowania jednokrotnego, jeśli próbują ponownie uzyskać dostęp do aplikacji usługi Azure AD w ramach ich panelu z urządzeń przyłączonych do domeny. Dzięki tej zmianie wylogowanie jest obsługiwane.  Dzięki temu użytkownicy mogą wybrać to samo lub inne konto usługi Azure AD, aby zalogować się z powrotem, zamiast automatycznie zalogować się przy użyciu bezproblemowego logowania jednokrotnego.

Aby uzyskać więcej informacji, zobacz [Bezproblemowe logowanie jednoobjęte usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso)
 
---
 
### <a name="application-proxy-connector-version-154020-released"></a>Opublikowano złącze serwera proxy aplikacji w wersji 1.5.402.0

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Serwer proxy aplikacji  
**Możliwości produktu:** Ochrona & zabezpieczeń tożsamości
 
Ta wersja łącznika jest stopniowo wdrażana do listopada. Ta nowa wersja łącznika zawiera następujące zmiany:

- Łącznik ustawia teraz pliki cookie na poziomie domeny, a nie poziom poddomeny. Zapewnia to płynniejsze środowisko przyszywnienia i pozwala uniknąć monitów o uwierzytelniania nadmiarowego.
- Obsługa żądań kodowania fragmentowanego
- Ulepszone monitorowanie stanu złącza 
- Kilka poprawek błędów i ulepszenia stabilności

Aby uzyskać więcej informacji, zobacz [Opis łączników serwera proxy aplikacji usługi Azure AD](https://docs.microsoft.com/azure/active-directory/application-proxy-understand-connectors).
 
---

## <a name="february-2018"></a>Luty 2018 r.
 
### <a name="improved-navigation-for-managing-users-and-groups"></a>Ulepszona nawigacja do zarządzania użytkownikami i grupami

**Typ:** Plan zmian  
**Kategoria usługi:** Zarządzanie katalogami  
**Możliwości produktu:** Katalogu

Usprawniono nawigację do zarządzania użytkownikami i grupami. Teraz można przejść z przeglądu katalogu bezpośrednio do listy wszystkich użytkowników, z łatwiejszym dostępem do listy usuniętych użytkowników. Można również przejść z przeglądu katalogu bezpośrednio do listy wszystkich grup, z łatwiejszym dostępem do ustawień zarządzania grupami. A także na stronie przeglądu katalogu można wyszukać użytkownika, grupę, aplikację przedsiębiorstwa lub rejestrację aplikacji. 

---

### <a name="availability-of-sign-ins-and-audit-reports-in-microsoft-azure-operated-by-21vianet-azure-china-21vianet"></a>Dostępność logów i raportów inspekcji na platformie Microsoft Azure obsługiwanych przez firmę 21Vianet (Azure China 21Vianet)

**Typ:** Nowa funkcja  
**Kategoria usługi:** Stos platformy Azure  
**Możliwości produktu:** Monitorowanie & raportowania

Raporty dziennika aktywności usługi Azure AD są teraz dostępne na platformie Microsoft Azure obsługiwane przez wystąpienia 21Vianet (Azure China 21Vianet). Następujące dzienniki są zawarte:

- **Dzienniki aktywności logowania** — zawiera wszystkie dzienniki logowania skojarzone z dzierżawą.

- **Samoobsługowe dzienniki inspekcji haseł** — zawiera wszystkie dzienniki inspekcji SSPR.

- **Dzienniki inspekcji zarządzania katalogami** — zawiera wszystkie dzienniki inspekcji związane z zarządzaniem katalogami, takie jak zarządzanie użytkownikami, zarządzanie aplikacjami i inne.

Dzięki tym dziennikom można uzyskać wgląd w sposób działania środowiska. Na podstawie udostępnionych danych można:

- Określ sposób korzystania z aplikacji i usług przez użytkowników.

- Rozwiązywanie problemów uniemożliwiających użytkownikom wykonanie pracy.

Aby uzyskać więcej informacji na temat korzystania z tych raportów, zobacz [Raportowanie usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-azure-portal).

---

### <a name="use-report-reader-role-non-admin-role-to-view-azure-ad-activity-reports"></a>Użyj roli "Czytnik raportów" (rola niebędąca administratorem) w celu wyświetlania raportów aktywności usługi Azure AD

**Typ:** Nowa funkcja  
**Kategoria usługi:** Reporting  
**Możliwości produktu:** Monitorowanie & raportowania

W ramach opinii klientów, aby umożliwić role niebędące administratorami, aby mieć dostęp do dzienników aktywności usługi Azure AD, włączyliśmy możliwość dostępu do logowania i inspekcji w witrynie Microsoft Graph użytkowników, a także do korzystania z interfejsu API programu Microsoft Graph. 

Aby uzyskać więcej informacji, jak korzystać z tych raportów, zobacz [Raportowanie usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-azure-portal). 

---

### <a name="employeeid-claim-available-as-user-attribute-and-user-identifier"></a>Oświadczenie EmployeeID dostępne jako atrybut użytkownika i identyfikator użytkownika

**Typ:** Nowa funkcja  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwości produktu:** Usługi rejestracji jednokrotnej
 
**Identyfikator pracownika** można skonfigurować jako identyfikator użytkownika i atrybut Użytkownika dla użytkowników członkowskich i gości B2B w aplikacjach logowania opartych na SAML z interfejsu użytkownika aplikacji Enterprise.

Aby uzyskać więcej informacji, zobacz [Dostosowywanie oświadczeń wystawionych w tokenie SAML dla aplikacji dla przedsiębiorstw w usłudze Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization).

---

### <a name="simplified-application-management-using-wildcards-in-azure-ad-application-proxy"></a>Uproszczone zarządzanie aplikacjami przy użyciu symboli wieloznacznych w serwerze proxy aplikacji usługi Azure AD

**Typ:** Nowa funkcja  
**Kategoria usługi:** Serwer proxy aplikacji  
**Możliwości produktu:** Uwierzytelnianie użytkownika
 
Aby ułatwić wdrażanie aplikacji i zmniejszyć obciążenie administracyjne, obsługujemy teraz możliwość publikowania aplikacji przy użyciu symboli wieloznacznych. Aby opublikować aplikację wieloznaczną, można wykonać standardowy przepływ publikowania aplikacji, ale użyj symbolu wieloznacznego w wewnętrznych i zewnętrznych adresach URL.

Aby uzyskać więcej informacji, zobacz [Aplikacje wieloznaczne w serwerze proxy aplikacji usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-wildcard)

---

### <a name="new-cmdlets-to-support-configuration-of-application-proxy"></a>Nowe polecenia cmdlet do obsługi konfiguracji serwera proxy aplikacji

**Typ:** Nowa funkcja  
**Kategoria usługi:** Serwer proxy aplikacji  
**Możliwości produktu:** Platformy

Najnowsza wersja modułu AzureAD PowerShell Preview zawiera nowe polecenia cmdlet, które umożliwiają klientom konfigurowanie aplikacji proxy aplikacji przy użyciu programu PowerShell.

Nowe polecenia cmdlet to: 

- Get-AzureADApplicationProxyAplikacja
- Grupa Get-AzureADApplicationProxyApplicationConnectorGroup
- Get-AzureADApplicationProxyConnector
- Grupa Get-AzureADApplicationProxyConnectorGroup
- Członkowie grupy Grupy Get-AzureADApplicationProxyConnectorGroupmembers
- Get-AzureADApplicationProxyConnectorMemberOf
- Nowa AzureADApplicationProxyApplication
- Nowa azureadapplicationProxyConnectorGroup
- Usuń AzureADApplicationProxyApplication
- Usuń—AzureADApplicationProxyApplicationConnectorGroup
- Usuń azureadapplicationProxyConnectorGroup
- Set-AzureADApplicationProxyApplication Set-AzureADApplication
- Set-AzureADApplicationProxyApplicationConnectorGroup
- Set-AzureADApplicationProxyApplicationCustomDomainCertificate
- Set-AzureADApplicationProxyApplicationSingleSignOn
- Zestaw AzureADApplicationProxyConnector
- Set-AzureADApplicationProxyConnectorGroup

---
 
### <a name="new-cmdlets-to-support-configuration-of-groups"></a>Nowe polecenia cmdlet do obsługi konfiguracji grup

**Typ:** Nowa funkcja  
**Kategoria usługi:** Serwer proxy aplikacji  
**Możliwości produktu:** Platformy

Najnowsza wersja modułu AzureAD PowerShell zawiera polecenia cmdlet do zarządzania grupami w usłudze Azure AD. Te polecenia cmdlet były wcześniej dostępne w module AzureADPreview i są teraz dodawane do modułu AzureAD

Polecenia cmdlet grupy, które są teraz dostępne dla ogólnej dostępności są następujące: 

- Grupa Get-AzureADMS
- Nowa grupa AzureADMS
- Grupa Usuń azureadms
- Set-AzureADMSGroup
- Get-AzureADMSGroupLifecyclePolicy
- Nowa AzureADMSGroupLifecyclePolicy
- Usuń-AzureADMSGroupLifecyclePolicy
- Grupa dodawania AzureADMSLifecyclePolicyGroup
- Usuń-AzureAdmSLifecyclePolicyGroup
- Grupa Reset-AzureADMSLifeCycleGroup   
- Grupa polityk get-AzureAdmsLifecyclePolicyGroup

---
 
### <a name="a-new-release-of-azure-ad-connect-is-available"></a>Dostępna jest nowa wersja usługi Azure AD Connect

**Typ:** Nowa funkcja  
**Kategoria usługi:** Synchronizacja usługi AD  
**Możliwości produktu:** Platformy
 
Usługa Azure AD Connect jest preferowanym narzędziem do synchronizowania danych między usługą Azure AD i lokalnymi źródłami danych, w tym usługą Windows Server Active Directory i LDAP.

>[!Important]
>Ta kompilacja wprowadza zmiany w regułach schematu i synchronizacji. Usługa synchronizacji usługi Azure AD Connect wyzwala kroki pełnego importu i pełnej synchronizacji po uaktualnieniu. Aby uzyskać informacje na temat zmiany tego zachowania, zobacz [Jak odroczyć pełną synchronizację po uaktualnieniu](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-upgrade-previous-version#how-to-defer-full-synchronization-after-upgrade).

Ta wersja ma następujące aktualizacje i zmiany:

**Rozwiązane problemy**

- Napraw okno chronometrażu w zadaniach w tle dla strony filtrowania partycji podczas przełączania do następnej strony.

- Naprawiono błąd, który powodował naruszenie zasad dostępu podczas akcji niestandardowej configdb.

- Naprawiono błąd, aby odzyskać z sql limit czasu połączenia.

- Naprawiono błąd, który powodował, że certyfikaty z symbolami wieloznaczymi SAN nie sprawdzały się przed reqem.

- Naprawiono błąd, który powodował awarię programu miiserver.exe podczas eksportowania złącza AAD.

- Naprawiono błąd, który powodował, że zła próba zalogowania się na kontrolerze domeny podczas pracy powodowała zmianę konfiguracji przez kreatora połączenia usługi AAD

**Nowe funkcje i ulepszenia**
 
- Telemetria aplikacji — administratorzy mogą włączać/wyłączać tę klasę danych.

- Dane usługi Azure AD Health — administratorzy muszą odwiedzić portal kondycji, aby kontrolować swoje ustawienia kondycji. Po zmianie zasad usługi agenci będą je odczytywać i wymuszać.

- Dodano akcje konfiguracji stornia zwrotnego urządzenia i pasek postępu do inicjowania strony.

- Ulepszona ogólna diagnostyka dzięki raportowi HTML i pełnemu zbieraniu danych w raporcie ZIP-Text / HTML.

- Zwiększona niezawodność automatycznego uaktualniania i dodano dodatkowe dane telemetryczne, aby zapewnić kondycję serwera.

- Ograniczanie uprawnień dostępnych dla kont uprzywilejowanych na koncie programu AD Connector. W przypadku nowych instalacji kreator ogranicza uprawnienia, które konta uprzywilejowane mają na koncie MSOL po utworzeniu konta MSOL. Zmiany mają wpływ na instalacje ekspresowe i instalacje niestandardowe za pomocą konta Auto-Create.

- Zmieniono instalator, aby nie wymagał uprawnień SA podczas czystej instalacji usługi AADConnect.

- Nowe narzędzie do rozwiązywania problemów z synchronizacją dla określonego obiektu. Obecnie narzędzie sprawdza następujące rzeczy:

    - Niezgodność userPrincipalName między zsynchronizowanym obiektem użytkownika a kontem użytkownika w dzierżawie usługi Azure AD.
  
    - Jeśli obiekt jest filtrowany z synchronizacji z powodu filtrowania domeny
  
    - Jeśli obiekt jest filtrowany z synchronizacji z powodu filtrowania jednostki organizacyjnej

- Nowe narzędzie do synchronizowania bieżącego skrótu hasła przechowywanego w lokalnej usłudze Active Directory dla określonego konta użytkownika. Narzędzie nie wymaga zmiany hasła. 

---
 
### <a name="applications-supporting-intune-app-protection-policies-added-for-use-with-azure-ad-application-based-conditional-access"></a>Aplikacje obsługujące zasady ochrony aplikacji usługi Intune dodane do użytku z dostępem warunkowym opartym na aplikacji usługi Azure AD

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Dostęp warunkowy  
**Możliwości produktu:** Ochrona & zabezpieczeń tożsamości

Dodaliśmy więcej aplikacji, które obsługują dostęp warunkowy oparty na aplikacjach. Teraz możesz uzyskać dostęp do usługi Office 365 i innych aplikacji w chmurze połączonych z usługą Azure AD przy użyciu tych zatwierdzonych aplikacji klienckich.

Do końca lutego zostaną dodane następujące wnioski:

- Microsoft Power BI

- Microsoft Launcher

- Microsoft Invoicing

Aby uzyskać więcej informacji, zobacz:

- [Wymagane zatwierdzenie aplikacji klienckiej](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-conditions#client-apps-preview)
- [Dostęp warunkowy oparty na aplikacji usługi Azure AD](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)

---

### <a name="terms-of-use-update-to-mobile-experience"></a>Warunki użytkowania aktualizacji do obsługi urządzeń mobilnych 

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Warunki użytkowania  
**Możliwości produktu:** Zgodności

Po wyświetleniu warunków użytkowania można teraz kliknąć opcję **Masz problemy z wyświetlaniem? Kliknij tutaj**. Kliknięcie tego linku powoduje otwarcie warunków użytkowania natywnie na urządzeniu. Niezależnie od rozmiaru czcionki w dokumencie lub rozmiaru ekranu urządzenia można powiększać i odczytywać dokument w razie potrzeby. 

---
 
## <a name="january-2018"></a>Styczeń 2018 r.
 
### <a name="new-federated-apps-available-in-azure-ad-app-gallery"></a>Nowe aplikacje federacyjne dostępne w galerii aplikacji usługi Azure AD 

**Typ:** Nowa funkcja  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwości produktu:** Integracja stron trzecich

W styczniu 2018 r. w galerii aplikacji dodano następujące nowe aplikacje z obsługą federacji:

[IBM OpenPages](https://go.microsoft.com/fwlink/?linkid=864698), [OneTrust Privacy Management Software](https://go.microsoft.com/fwlink/?linkid=861660), [Dealpath](https://go.microsoft.com/fwlink/?linkid=863526), [IriusRisk Federated Directory i [Fidelity NetBenefits](https://go.microsoft.com/fwlink/?linkid=864701).

Aby uzyskać więcej informacji na temat aplikacji, zobacz [Integracja aplikacji SaaS z usługą Azure Active Directory](https://aka.ms/appstutorial).

Aby uzyskać więcej informacji na temat wyświetlania aplikacji w galerii aplikacji usługi Azure AD, zobacz [Lista aplikacji w galerii aplikacji usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing). 

---
 
### <a name="sign-in-with-additional-risk-detected"></a>Zaloguj się przy wykryciu dodatkowego ryzyka

**Typ:** Nowa funkcja  
**Kategoria usługi:** Ochrona tożsamości  
**Możliwości produktu:** Ochrona & zabezpieczeń tożsamości

Wgląd w wykryte wykrywanie ryzyka jest powiązany z subskrypcją usługi Azure AD. W wersji Azure AD Premium P2 można uzyskać najbardziej szczegółowe informacje o wszystkich podstawowych wykrywania.

W wersji Azure AD Premium P1 wykrywania, które nie są objęte licencją są wyświetlane jako wykrywania ryzyka Zaloguj się z dodatkowym ryzykiem wykryte.

Aby uzyskać więcej informacji, zobacz [Wykrywanie ryzyka usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-risk-events).
 
---

### <a name="hide-office-365-applications-from-end-users-access-panels"></a>Ukrywanie aplikacji usługi Office 365 z paneli dostępu użytkownika końcowego

**Typ:** Nowa funkcja  
**Kategoria usługi:** Moje aplikacje  
**Możliwości produktu:** Usługi rejestracji jednokrotnej

Teraz możesz lepiej zarządzać tym, jak aplikacje usługi Office 365 są wyświetlane w panelach dostępu użytkownika za pomocą nowego ustawienia użytkownika. Ta opcja jest przydatna w celu zmniejszenia liczby aplikacji w panelach dostępu użytkownika, jeśli wolisz wyświetlać tylko aplikacje pakietu Office w portalu pakietu Office. To ustawienie znajduje się w **ustawieniach użytkownika** i jest oznaczone **etykietą Użytkownicy mogą wyświetlać tylko aplikacje usługi Office 365 w portalu usługi Office 365**.

Aby uzyskać więcej informacji, zobacz [Ukrywanie aplikacji przed korzystaniem z usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-hide-third-party-app).

---
 
### <a name="seamless-sign-into-apps-enabled-for-password-sso-directly-from-apps-url"></a>Bezproblemowe logowanie do aplikacji włączonych dla logowania jednokrotnego hasła bezpośrednio z adresu URL aplikacji 

**Typ:** Nowa funkcja  
**Kategoria usługi:** Moje aplikacje  
**Możliwości produktu:** Usługi rejestracji jednokrotnej

Rozszerzenie przeglądarki Moje aplikacje jest teraz dostępne za pomocą wygodnego narzędzia, które zapewnia możliwość logowania my apps jako skrót w przeglądarce. Po zainstalowaniu, użytkownik zobaczy ikonę wafel w przeglądarce, która zapewnia im szybki dostęp do aplikacji. Użytkownicy mogą teraz korzystać z:

- Możliwość bezpośredniego logowania się do aplikacji opartych na logowanie do logowania z hasłem na stronie logowania aplikacji
- Uruchamianie dowolnej aplikacji przy użyciu funkcji szybkiego wyszukiwania
- Skróty do ostatnio używanych aplikacji z rozszerzenia
- Rozszerzenie jest dostępne dla Microsoft Edge, Chrome i Firefox.
 
Aby uzyskać więcej informacji, zobacz [Rozszerzenie bezpiecznego logowania moje aplikacje](../user-help/my-apps-portal-end-user-access.md#download-and-install-the-my-apps-secure-sign-in-extension).

---

### <a name="azure-ad-administration-experience-in-azure-classic-portal-has-been-retired"></a>Środowisko administrowania usługą Azure AD w portalu Azure Classic Portal zostało wycofane

**Typ:** Przestarzałe   
**Kategoria usługi:** Azure AD  
**Możliwości produktu:** Katalogu

Od 8 stycznia 2018 r. środowisko administrowania usługą Azure AD w klasycznym portalu platformy Azure zostało wycofane. Miało to miejsce w połączeniu z wycofaniem samego klasycznego portalu azure. W przyszłości należy użyć [centrum administracyjnego usługi Azure AD](https://aad.portal.azure.com) dla wszystkich administracji usługi Azure AD opartej na portalu.
 
---

### <a name="the-phonefactor-web-portal-has-been-retired"></a>Portal internetowy PhoneFactor został wycofany

**Typ:** Przestarzałe  
**Kategoria usługi:** Azure AD  
**Możliwości produktu:** Katalogu
 
Od 8 stycznia 2018 r. portal internetowy PhoneFactor został wycofany. Ten portal był używany do administrowania serwerem usługi MFA, ale te funkcje zostały przeniesione do witryny Azure portal w portal.azure.com. 

Konfiguracja usługi MFA znajduje się pod adresem: **Serwer usługi Azure Active Directory \> MFA**
 
---
 
### <a name="deprecate-azure-ad-reports"></a>Przestarzałe raporty usługi Ad platformy Azure

**Typ:** Przestarzałe  
**Kategoria usługi:** Reporting  
**Możliwości produktu:** Zarządzanie cyklem życia tożsamości  


Dzięki ogólnej dostępności nowej konsoli administracji usługi Azure Active Directory i nowych interfejsów API dostępnych teraz zarówno dla raportów aktywności, jak i raportów zabezpieczeń, interfejsy API raportu w punkcie końcowym "/reports" zostały wycofane z końcem grudnia 31, 2017.

**Co jest dostępne?**

W ramach przejścia do nowej konsoli administracyjnej udostępniliśmy 2 nowe interfejsy API do pobierania dzienników aktywności usługi Azure AD. Nowy zestaw interfejsów API zapewnia bogatsze funkcje filtrowania i sortowania, a także zapewnia bogatsze działania inspekcji i logowania. Dane wcześniej dostępne za pośrednictwem raportów zabezpieczeń można teraz uzyskać za pośrednictwem interfejsu API wykrywania ryzyka ochrony tożsamości w programie Microsoft Graph.

Aby uzyskać więcej informacji, zobacz:

- [Wprowadzenie do interfejsu API raportowania usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-getting-started-azure-portal)

- [Wprowadzenie do usługi Azure Active Directory Identity Protection i Microsoft Graph](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection-graph-getting-started)

---

## <a name="december-2017"></a>Grudzień 2017

### <a name="terms-of-use-in-the-access-panel"></a>Warunki użytkowania w Panelu dostępu

**Typ:** Nowa funkcja  
**Kategoria usługi:** Warunki użytkowania  
**Możliwości produktu:** Zgodności
 
Teraz możesz przejść do Panelu dostępu i wyświetlić wcześniej zaakceptowane warunki użytkowania.

Wykonaj następujące kroki:

1. Przejdź do [portalu MyApps](https://myapps.microsoft.com)i zaloguj się.

2. W prawym górnym rogu wybierz swoje imię i nazwisko, a następnie wybierz **pozycję Profil** z listy. 

3. W swoim **profilu**wybierz **pozycję Przejrzyj warunki użytkowania**. 

4. Teraz możesz zapoznać się z zaakceptowanymi warunkami użytkowania. 

Aby uzyskać więcej informacji, zobacz [funkcję warunków użytkowania usługi Azure AD (wersja zapoznawcza)](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use).
 
---
 
### <a name="new-azure-ad-sign-in-experience"></a>Nowe środowisko logowania usługi Azure AD

**Typ:** Nowa funkcja  
**Kategoria usługi:** Azure AD  
**Możliwości produktu:** Uwierzytelnianie użytkownika
 
Interfejsy użytkownika systemu tożsamości usługi Azure AD i microsoft zostały przeprojektowane, aby mieć spójny wygląd i działanie. Ponadto strona logowania usługi Azure AD zbiera najpierw nazwę użytkownika, a następnie poświadczenia na drugim ekranie.

Aby uzyskać więcej informacji, zobacz [Nowe środowisko logowania usługi Azure AD jest teraz w publicznej wersji zapoznawczej](https://cloudblogs.microsoft.com/enterprisemobility/2017/08/02/the-new-azure-ad-signin-experience-is-now-in-public-preview/).
 
---
 
### <a name="fewer-sign-in-prompts-a-new-keep-me-signed-in-experience-for-azure-ad-sign-in"></a>Mniej monitów logowania: nowe środowisko "keep me signed in" dla logowania usługi Azure AD

**Typ:** Nowa funkcja  
**Kategoria usługi:** Azure AD  
**Możliwości produktu:** Uwierzytelnianie użytkownika
 
Pole wyboru **Zachowaj mnie zalogowanego** na stronie logowania usługi Azure AD zostało zastąpione nowym monitem, który pojawia się po pomyślnym uwierzytelnienia. 

Jeśli odpowiesz **tak** na ten monit, usługa zapewnia trwały token odświeżania. To zachowanie jest takie samo, jak po wybraniu pola wyboru **Zachowaj mnie zalogowany w** starym doświadczeniu. W przypadku dzierżaw sfederowanych ten monit jest wyświetlany po pomyślnym uwierzytelnienia za pomocą usługi federacyjnej.

Aby uzyskać więcej informacji, zobacz [Mniej monitów logowania: nowe środowisko "keep me signed in" dla usługi Azure AD znajduje się w wersji zapoznawczej.](https://cloudblogs.microsoft.com/enterprisemobility/2017/09/19/fewer-login-prompts-the-new-keep-me-signed-in-experience-for-azure-ad-is-in-preview/) 

---

### <a name="add-configuration-to-require-the-terms-of-use-to-be-expanded-prior-to-accepting"></a>Dodaj konfigurację, aby wymagać rozszerzenia warunków użytkowania przed zaakceptowaniem

**Typ:** Nowa funkcja  
**Kategoria usługi:** Warunki użytkowania  
**Możliwości produktu:** Zgodności
 
Opcja dla administratorów wymaga od użytkowników rozszerzenia warunków użytkowania przed zaakceptowaniem warunków.

Wybierz **włącz** lub **wyłącz,** aby wymagać od użytkowników rozszerzenia warunków użytkowania. **Ustawienie Włączone** wymaga od użytkowników wyświetlania warunków użytkowania przed ich zaakceptowaniem.

Aby uzyskać więcej informacji, zobacz [funkcję warunków użytkowania usługi Azure AD (wersja zapoznawcza)](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use).
 
---

### <a name="scoped-activation-for-eligible-role-assignments"></a>Aktywacja o określonym zakresie dla kwalifikujących się przypisań ról

**Typ:** Nowa funkcja  
**Kategoria usługi:** Zarządzanie tożsamościami uprzywilejowanymi  
**Możliwości produktu:** Zarządzanie tożsamościami uprzywilejowanymi
 
Aktywacja o określonym zakresie umożliwia aktywowanie kwalifikujących się przypisań ról zasobów platformy Azure z mniejszą autonomią niż domyślne przypisanie oryginalnego. Przykładem jest, jeśli jesteś przypisany jako właściciel subskrypcji w dzierżawie. Dzięki aktywacji o określonym zakresie można aktywować rolę właściciela dla maksymalnie pięciu zasobów zawartych w ramach subskrypcji (takich jak grupy zasobów i maszyny wirtualne). Określanie zakresu aktywacji może zmniejszyć możliwość wykonywania niepożądanych zmian w krytycznych zasobach platformy Azure.

Aby uzyskać więcej informacji, zobacz [Co to jest zarządzanie tożsamościami uprzywilejowanymi usługi Azure AD?](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure).
 
---
 
### <a name="new-federated-apps-in-the-azure-ad-app-gallery"></a>Nowe aplikacje federacyjne w galerii aplikacji usługi Azure AD

**Typ:** Nowa funkcja  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwości produktu:** Integracja stron trzecich

W grudniu 2017 r. dodaliśmy te nowe aplikacje z obsługą federacji do naszej galerii aplikacji:

[Accredible](https://go.microsoft.com/fwlink/?linkid=863523), Adobe Experience Manager, [EFI Digital StoreFront](https://go.microsoft.com/fwlink/?linkid=861685), [Communifire](https://go.microsoft.com/fwlink/?linkid=861676) CybSafe, [FactSet](https://go.microsoft.com/fwlink/?linkid=863525), [IMAGE WORKS](https://go.microsoft.com/fwlink/?linkid=863517), [MOBI](https://go.microsoft.com/fwlink/?linkid=863521), [MobileIron Azure AD integracji](https://go.microsoft.com/fwlink/?linkid=858027), [Reflektive](https://go.microsoft.com/fwlink/?linkid=863518), [SAML SSO dla Bamboo przez rozdzielczość GmbH](https://go.microsoft.com/fwlink/?linkid=863520), [SAML SSO dla Bitbucket przez rozdzielczość GmbH](https://go.microsoft.com/fwlink/?linkid=863519), [Vodeclic](https://go.microsoft.com/fwlink/?linkid=863522), WebHR, Zenegy Azure AD Integracja.

Aby uzyskać więcej informacji na temat aplikacji, zobacz [Integracja aplikacji SaaS z usługą Azure Active Directory](https://aka.ms/appstutorial).

Aby uzyskać więcej informacji na temat wyświetlania aplikacji w galerii aplikacji usługi Azure AD, zobacz [Lista aplikacji w galerii aplikacji usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing). 
 
---

### <a name="approval-workflows-for-azure-ad-directory-roles"></a>Przepływy pracy zatwierdzania ról katalogów usługi Azure AD

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Zarządzanie tożsamościami uprzywilejowanymi  
**Możliwości produktu:** Zarządzanie tożsamościami uprzywilejowanymi
 
Przepływ pracy zatwierdzania ról katalogów usługi Azure AD jest ogólnie dostępny.

Dzięki przepływowi pracy zatwierdzania administratorzy ról uprzywilejowanych mogą wymagać od kwalifikujących się członków roli żądania aktywacji roli, zanim będą mogli użyć roli uprzywilejowanej. Wielu użytkownikom i grupom można delegować obowiązki zatwierdzania. Kwalifikujący się członkowie roli otrzymują powiadomienia po zakończeniu zatwierdzania i ich rola jest aktywna.

---
 
### <a name="pass-through-authentication-skype-for-business-support"></a>Uwierzytelnianie przekazywane: obsługa programu Skype dla firm

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Uwierzytelnianie (loginy)  
**Możliwości produktu:** Uwierzytelnianie użytkownika

Uwierzytelnianie przekazywane obsługuje teraz logowania użytkowników do aplikacji klienckich programu Skype dla firm, które obsługują nowoczesne uwierzytelnianie, które obejmuje topologie online i hybrydowe. 

Aby uzyskać więcej informacji, zobacz [Topologie programu Skype dla firm obsługiwane przy użyciu nowoczesnego uwierzytelniania](https://technet.microsoft.com/library/mt803262.aspx).
 
---

### <a name="updates-to-azure-ad-privileged-identity-management-for-azure-rbac-preview"></a>Aktualizacje zarządzania tożsamościami uprzywilejowanymi usługi Azure AD dla usługi Azure RBAC (wersja zapoznawcza)

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Zarządzanie tożsamościami uprzywilejowanymi  
**Możliwości produktu:** Zarządzanie tożsamościami uprzywilejowanymi
 
Dzięki publicznemu odświeżeniu w wersji zapoznawczej usługi Azure AD Privileged Identity Management (PIM) dla kontroli dostępu opartej na rolach platformy Azure można teraz:

* Użyj tylko tyle administracji.
* Wymagaj zatwierdzenia, aby aktywować role zasobów.
* Zaplanuj przyszłą aktywację roli, która wymaga zatwierdzenia zarówno ról usługi Azure AD, jak i usługi Azure RBAC.
 
Aby uzyskać więcej informacji, zobacz [Zarządzanie tożsamościami uprzywilejowanymi dla zasobów platformy Azure (wersja zapoznawcza)](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/azure-pim-resource-rbac).

---
 
## <a name="november-2017"></a>Listopad 2017
 
### <a name="access-control-service-retirement"></a>Wycofanie usługi Kontrola dostępu

**Typ:** Plan zmian  
**Kategoria usługi:** Usługa kontroli dostępu  
**Możliwości produktu:** Usługa kontroli dostępu 

Kontrola dostępu usługi Azure Active Directory (znana również jako usługa kontroli dostępu) zostanie wycofana pod koniec 2018 r. Więcej informacji, które zawierają szczegółowy harmonogram i wytyczne dotyczące migracji wysokiego poziomu, zostaną dostarczone w ciągu najbliższych kilku tygodni. Możesz zostawić komentarze na tej stronie z wszelkimi pytaniami dotyczącymi usługi Kontroli dostępu, a członek zespołu odpowie na nie.

---

### <a name="restrict-browser-access-to-the-intune-managed-browser"></a>Ograniczanie dostępu do przeglądarki do zarządzanej przeglądarki usługi Intune 

**Typ:** Plan zmian  
**Kategoria usługi:** Dostęp warunkowy  
**Możliwości produktu:** Bezpieczeństwo tożsamości i ochrona tożsamości

Możesz ograniczyć dostęp do przeglądarki do usługi Office 365 i innych aplikacji w chmurze połączonych z usługą Azure AD, używając zarządzanej przeglądarki usługi Intune jako zatwierdzonej aplikacji. 

Teraz można skonfigurować następujący warunek dla dostępu warunkowego opartego na aplikacji:

**Aplikacje klienckie:** Przeglądarka

**Jaki jest efekt tej zmiany?**

Dzisiaj dostęp jest zablokowany podczas korzystania z tego warunku. Gdy podgląd jest dostępny, cały dostęp będzie wymagał użycia aplikacji przeglądarki zarządzanej. 

Poszukaj tej możliwości i więcej informacji w nadchodzących blogach i informacjach o wersji. 

Aby uzyskać więcej informacji, zobacz [Dostęp warunkowy w usłudze Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal).
 
---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Nowe zatwierdzone aplikacje klienckie dla dostępu warunkowego opartego na usłudze Azure AD

**Typ:** Plan zmian  
**Kategoria usługi:** Dostęp warunkowy  
**Możliwości produktu:** Bezpieczeństwo tożsamości i ochrona tożsamości

Na liście [zatwierdzonych aplikacji klienckich](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-conditions#client-apps-preview)znajdują się następujące aplikacje:

- [Microsoft Kaizala](https://www.microsoft.com/garage/profiles/kaizala/)
- Microsoft StaffHub

Aby uzyskać więcej informacji, zobacz:

- [Wymagane zatwierdzenie aplikacji klienckiej](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-conditions#client-apps-preview)
- [Dostęp warunkowy oparty na aplikacji usługi Azure AD](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)

---

### <a name="terms-of-use-support-for-multiple-languages"></a>Obsługa warunków użytkowania w wielu językach

**Typ:** Nowa funkcja    
**Kategoria usługi:** Warunki użytkowania  
**Możliwości produktu:** Zgodności

Administratorzy mogą teraz tworzyć nowe warunki użytkowania, które zawierają wiele dokumentów PDF. Te dokumenty PDF można oznaczyć odpowiednim językiem. Użytkownicy są wyświetlane PDF z pasującym językiem na podstawie ich preferencji. Jeśli nie ma dopasowania, wyświetlany jest domyślny język.

---
 
### <a name="real-time-password-writeback-client-status"></a>Stan klienta stortuje hasło w czasie rzeczywistym

**Typ:** Nowa funkcja  
**Kategoria usługi:** Samoobsługowe resetowanie hasła  
**Możliwości produktu:** Uwierzytelnianie użytkownika

Teraz można przejrzeć stan klienta przywtym hasłoback lokalne. Ta opcja jest dostępna w sekcji **Integracja lokalna** na stronie [Resetowanie hasła.](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/PasswordReset) 

Jeśli występują problemy z połączeniem z lokalnym klientem stornowania, zostanie wyświetlony komunikat o błędzie, który zawiera:

- Informacje o tym, dlaczego nie można połączyć się z lokalnym klientem stornuj.
- Łącze do dokumentacji, która pomaga w rozwiązaniu problemu. 

Aby uzyskać więcej informacji, zobacz [integracja lokalna](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-how-it-works#on-premises-integration).

---

### <a name="azure-ad-app-based-conditional-access"></a>Dostęp warunkowy oparty na aplikacji usługi Azure AD 
 
**Typ:** Nowa funkcja  
**Kategoria usługi:** Azure AD  
**Możliwości produktu:** Bezpieczeństwo tożsamości i ochrona tożsamości

Teraz możesz ograniczyć dostęp do usługi Office 365 i innych aplikacji w chmurze połączonych z usługą Azure AD do [zatwierdzonych aplikacji klienckich obsługujących](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-conditions#client-apps-preview) zasady ochrony aplikacji usługi Intune przy użyciu [dostępu warunkowego opartego na aplikacjach usługi Azure AD](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access). Zasady ochrony aplikacji usługi Intune służą do konfigurowania i ochrony danych firmy w tych aplikacjach klienckich.

Łącząc [oparte na aplikacjach](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access) z zasadami dostępu [warunkowego opartego na urządzeniach,](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-policy-connected-applications) masz możliwość ochrony danych dla urządzeń osobistych i firmowych.

Następujące warunki i kontrolki są teraz dostępne do użycia z dostępem warunkowym opartym na aplikacji:

**Obsługiwany warunek platformy**

- iOS
- Android

**Warunek aplikacji klienckich**

- Aplikacje mobilne i klienci komputerowi

**Kontrola dostępu**

- Wymaganie zatwierdzonej aplikacji klienckiej

Aby uzyskać więcej informacji, zobacz [Dostęp warunkowy oparty na aplikacji usługi Azure AD](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access).
 
---

### <a name="manage-azure-ad-devices-in-the-azure-portal"></a>Zarządzanie urządzeniami usługi Azure AD w witrynie Azure portal

**Typ:** Nowa funkcja  
**Kategoria usługi:** Rejestracja i zarządzanie urządzeniami  
**Możliwości produktu:** Bezpieczeństwo tożsamości i ochrona tożsamości

Teraz możesz znaleźć wszystkie urządzenia połączone z usługą Azure AD i działania związane z urządzeniem w jednym miejscu. Istnieje nowe środowisko administracyjne do zarządzania wszystkimi tożsamościami i ustawieniami urządzeń w witrynie Azure portal. W tej wersji można:

- Wyświetl wszystkie urządzenia, które są dostępne dla dostępu warunkowego w usłudze Azure AD.
- Wyświetl właściwości, które obejmują hybrydowe urządzenia przyłączone do usługi Azure AD.
- Znajdź klucze funkcji BitLocker dla urządzeń przyłączonych do usługi Azure AD, zarządzaj urządzeniem za pomocą usługi Intune i nie tylko.
- Zarządzanie ustawieniami związanymi z urządzeniami usługi Azure AD.

Aby uzyskać więcej informacji, zobacz [Zarządzanie urządzeniami przy użyciu witryny Azure portal](https://docs.microsoft.com/azure/active-directory/device-management-azure-portal).

---

### <a name="support-for-macos-as-a-device-platform-for-azure-ad-conditional-access"></a>Obsługa systemu macOS jako platformy urządzeń dla dostępu warunkowego usługi Azure AD 

**Typ:** Nowa funkcja    
**Kategoria usługi:** Dostęp warunkowy  
**Możliwości produktu:** Bezpieczeństwo tożsamości i ochrona tożsamości 

Teraz można uwzględnić (lub wykluczyć) macOS jako warunek platformy urządzeń w zasadach dostępu warunkowego usługi Azure AD. Dzięki dodaniu systemu macOS do obsługiwanych platform urządzeń można:

- **Rejestrowanie urządzeń z systemem macOS i zarządzanie nimi przy użyciu usługi Intune.** Podobnie jak inne platformy, takie jak iOS i Android, aplikacja portalu firmy jest dostępna dla systemu macOS do ujednoliconych rejestracji. Za pomocą nowej aplikacji portalu firmy dla systemu macOS można zarejestrować urządzenie w usłudze Intune i zarejestrować je w usłudze Azure AD.
- **Upewnij się, że urządzenia z systemem macOS są zgodne z zasadami zgodności organizacji zdefiniowanymi w usłudze Intune.** W usłudze Intune w witrynie Azure portal można teraz skonfigurować zasady zgodności dla urządzeń z systemem macOS. 
- **Ogranicz dostęp do aplikacji w usłudze Azure AD tylko do zgodnych urządzeń z systemem macOS.** Tworzenie zasad dostępu warunkowego ma system macOS jako osobną opcję platformy urządzenia. Teraz możesz tworzyć zasady dostępu warunkowego specyficzne dla systemu macOS dla docelowego zestawu aplikacji na platformie Azure.

Aby uzyskać więcej informacji, zobacz:

- [Tworzenie zasad zgodności urządzeń dla urządzeń z systemem macOS w usłudze Intune](https://aka.ms/macoscompliancepolicy)
- [Dostęp warunkowy w usłudze Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal)
 
---

### <a name="network-policy-server-extension-for-azure-multi-factor-authentication"></a>Rozszerzenie serwera zasad sieciowych dla uwierzytelniania wieloskładnikowego platformy Azure 

**Typ:** Nowa funkcja    
**Kategoria usługi:**  Uwierzytelnianie wieloskładnikowe  
**Możliwości produktu:** Uwierzytelnianie użytkownika

Rozszerzenie serwera zasad sieciowych dla uwierzytelniania wieloskładnikowego platformy Azure dodaje do infrastruktury uwierzytelniania wieloskładnikowego oparte na chmurze funkcje uwierzytelniania przy użyciu istniejących serwerów. Dzięki rozszerzeniu Serwer zasad sieciowych możesz dodać połączenie telefoniczne, wiadomość TEKSTOWĄ lub weryfikację aplikacji telefonicznej do istniejącego przepływu uwierzytelniania. Nie trzeba instalować, konfigurować i obsługiwać nowych serwerów. 

To rozszerzenie zostało utworzone dla organizacji, które chcą chronić połączenia wirtualnej sieci prywatnej bez wdrażania serwera uwierzytelniania wieloskładnikowego platformy Azure. Rozszerzenie serwera zasad sieciowych działa jako karta między radius i cloud-based Azure Multi-Factor Authentication, aby zapewnić drugi współczynnik uwierzytelniania dla użytkowników federowanych lub zsynchronizowanych.

Aby uzyskać więcej informacji, zobacz [Integrowanie istniejącej infrastruktury serwera zasad sieciowych z uwierzytelnianiem wieloskładnikowym platformy Azure](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-nps-extension).
 
---

### <a name="restore-or-permanently-remove-deleted-users"></a>Przywracanie lub trwałe usuwanie usuniętych użytkowników

**Typ:** Nowa funkcja    
**Kategoria usługi:** Zarządzanie użytkownikami  
**Możliwości produktu:** Katalogu 

W centrum administracyjnym usługi Azure AD możesz teraz:

- Przywracanie usuniętego użytkownika. 
- Trwałe usunięcie użytkownika.

**Aby go wypróbować:**

1. W centrum administracyjnym usługi Azure AD wybierz pozycję [Wszyscy użytkownicy](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/All) w sekcji **Zarządzanie.** 

2. Z listy **Pokaż** wybierz pozycję **Ostatnio usunięci użytkownicy**. 

3. Wybierz jednego lub więcej ostatnio usuniętych użytkowników, a następnie przywróć lub trwale usuń.
 
---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Nowe zatwierdzone aplikacje klienckie dla dostępu warunkowego opartego na usłudze Azure AD
 
**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Dostęp warunkowy  
**Możliwości produktu:** Bezpieczeństwo tożsamości i ochrona tożsamości

Do listy [zatwierdzonych aplikacji klienckich](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-conditions#client-apps-preview)dodano następujące aplikacje:

- Planista firmy Microsoft
- Azure Information Protection 

Aby uzyskać więcej informacji, zobacz:

- [Wymagane zatwierdzenie aplikacji klienckiej](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-conditions#client-apps-preview)
- [Dostęp warunkowy oparty na aplikacji usługi Azure AD](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)

---

### <a name="use-or-between-controls-in-a-conditional-access-policy"></a>Używanie funkcji "OR" między formantami w zasadach dostępu warunkowego 

**Typ:** Zmieniono funkcję    
**Kategoria usługi:** Dostęp warunkowy  
**Możliwości produktu:** Bezpieczeństwo tożsamości i ochrona tożsamości
 
Teraz można użyć "OR" (wymagać jednego z wybranych formantów) dla formantów dostępu warunkowego. Za pomocą tej funkcji można tworzyć zasady z "OR" między formantami dostępu. Na przykład można użyć tej funkcji, aby utworzyć zasadę, która wymaga od użytkownika zalogować się przy użyciu uwierzytelniania wieloskładnikowego "OR", aby być na zgodnym urządzeniu.

Aby uzyskać więcej informacji, zobacz [Formanty w usłudze Azure AD Dostęp warunkowy](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-controls).
 
---

### <a name="aggregation-of-real-time-risk-detections"></a>Agregacja wykrywania ryzyka w czasie rzeczywistym

**Typ:** Zmieniono funkcję    
**Kategoria usługi:** Ochrona tożsamości  
**Możliwości produktu:** Bezpieczeństwo tożsamości i ochrona tożsamości

W usłudze Azure AD Identity Protection wszystkie wykrywanie ryzyka w czasie rzeczywistym, które pochodzą z tego samego adresu IP w danym dniu, są teraz agregowane dla każdego typu wykrywania ryzyka. Ta zmiana ogranicza liczbę wykrywania ryzyka wyświetlanych bez żadnych zmian w zabezpieczeniach użytkownika.

Podstawowe wykrywanie w czasie rzeczywistym działa za każdym razem, gdy użytkownik się zaloguje. Jeśli masz zasady zabezpieczeń ryzyka logowania skonfigurowane do uwierzytelniania wieloskładnikowego lub dostępu blokowego, jest on nadal wyzwalany podczas każdego ryzykownego logowania.
 
---
 
## <a name="october-2017"></a>Październik 2017

### <a name="deprecate-azure-ad-reports"></a>Przestarzałe raporty usługi Ad platformy Azure

**Typ:** Plan zmian  
**Kategoria usługi:** Reporting  
**Możliwości produktu:** Zarządzanie cyklem życia tożsamości  

Portal Azure zapewnia:

- Nowa konsola administracyjna usługi Azure AD.
- Nowe interfejsy API dla raportów aktywności i zabezpieczeń.
 
Ze względu na te nowe możliwości interfejsy API raportu w obszarze /reports endpoint zostały wycofane w dniu 10 grudnia 2017. 

---

### <a name="automatic-sign-in-field-detection"></a>Automatyczne wykrywanie pól logowania

**Typ:** Stałe   
**Kategoria usługi:** Moje aplikacje  
**Możliwości produktu:** Logowanie jednokrotne  

Usługa Azure AD obsługuje automatyczne wykrywanie pól logowania dla aplikacji, które renderują nazwę użytkownika HTML i pole hasła. Te kroki są opisane w [polu Jak automatycznie przechwytywać pola logowania dla aplikacji](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-password-single-sign-on-non-gallery-applications-problems#manually-capture-sign-in-fields-for-an-app). Tę funkcję można znaleźć, dodając aplikację *spoza galerii* na stronie **Aplikacje przedsiębiorstwa** w [witrynie Azure portal](https://aad.portal.azure.com). Ponadto można skonfigurować tryb **logowania jednokrotnego w** tej nowej aplikacji do logowania **jednokrotnego opartego na hasłach,** wprowadź internetowy adres URL, a następnie zapisać stronę.
 
Z powodu problemu z usługą ta funkcja została tymczasowo wyłączona. Problem został rozwiązany, a automatyczne wykrywanie pola logowania jest ponownie dostępne.

---

### <a name="new-multi-factor-authentication-features"></a>Nowe funkcje uwierzytelniania wieloskładnikowego

**Typ:** Nowa funkcja  
**Kategoria usługi:** Uwierzytelnianie wieloskładnikowe  
**Możliwości produktu:** Bezpieczeństwo tożsamości i ochrona tożsamości  

Uwierzytelnianie wieloskładnikowe (MFA) jest istotną częścią ochrony organizacji. Aby poświadczenia były bardziej adaptacyjne, a środowisko bardziej płynne, dodano następujące funkcje: 

- Wieloczynnikowe wyniki wyzwania są bezpośrednio zintegrowane z raportem logowania usługi Azure AD, który zawiera programowy dostęp do wyników usługi MFA.
- Konfiguracja usługi MFA jest bardziej zintegrowana z usługą Azure AD w witrynie Azure portal.

Dzięki tej publicznej wersji zapoznawczej zarządzanie usługi MFA i raportowanie są zintegrowaną częścią podstawowego środowiska konfiguracji usługi Azure AD. Teraz możesz zarządzać funkcjami portalu zarządzania usługi MFA w środowisku usługi Azure AD.

Aby uzyskać więcej informacji, zobacz [Odwołanie do raportowania usługi MFA w witrynie Azure portal](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-sign-ins-mfa). 

---

### <a name="terms-of-use"></a>Warunki użytkowania

**Typ:** Nowa funkcja  
**Kategoria usługi:** Warunki użytkowania  
**Możliwości produktu:** Zgodności  

Za pomocą warunków korzystania z usługi Azure AD można przedstawić użytkownikom informacje, takie jak odpowiednie zastrzeżenia dotyczące wymagań prawnych lub zgodności.

Można użyć warunków korzystania z usługi Azure AD w następujących scenariuszach:

- Ogólne warunki użytkowania dla wszystkich użytkowników w organizacji
- Szczególne warunki użytkowania oparte na atrybutach użytkownika (na przykład lekarze kontra pielęgniarki lub pracownicy domowi i międzynarodowi, wykonywane przez grupy dynamiczne)
- Szczegółowe warunki korzystania z aplikacji biznesowych o dużym wpływie, takich jak Salesforce

Aby uzyskać więcej informacji, zobacz [warunki użytkowania usługi Azure AD](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use).

---

### <a name="enhancements-to-privileged-identity-management"></a>Ulepszenia zarządzania tożsamościami uprzywilejowanymi

**Typ:** Nowa funkcja  
**Kategoria usługi:** Zarządzanie tożsamościami uprzywilejowanymi  
**Możliwości produktu:** Zarządzanie tożsamościami uprzywilejowanymi  

Za pomocą usługi Azure AD Privileged Identity Management można zarządzać, kontrolować i monitorować dostęp do zasobów platformy Azure (w wersji zapoznawczej) w organizacji, aby:

- Subskrypcje
- Grupy zasobów
- Maszyny wirtualne 

Wszystkie zasoby w witrynie Azure portal, które korzystają z funkcji usługi Azure RBAC można skorzystać ze wszystkich funkcji zarządzania zabezpieczeniami i cyklem życia, które usługa Azure AD Privileged Identity Management ma do zaoferowania.

Aby uzyskać więcej informacji, zobacz [Zarządzanie tożsamościami uprzywilejowanymi dla zasobów platformy Azure](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/azure-pim-resource-rbac).

---

### <a name="access-reviews"></a>Przeglądy dostępu

**Typ:** Nowa funkcja  
**Kategoria usługi:** Dostęp do recenzji  
**Możliwości produktu:** Zgodności  

Organizacje mogą używać przeglądów dostępu (wersja zapoznawcza) do efektywnego zarządzania członkostwem w grupach i dostępem do aplikacji korporacyjnych: 

- Przeprowadzając przeglądy dostępu, można ponownie certyfikować dostęp użytkowników-gości do aplikacji i ich członkostwo w grupach. Recenzenci mogą skutecznie zdecydować, czy zezwolić gościom na stały dostęp na podstawie informacji dostarczonych przez opinie o dostępie.
- Przeprowadzając przeglądy dostępu, można ponownie certyfikować dostęp do aplikacji i członkostwo w grupach pracowników.

Kontrole z przeglądem dostępu można łączyć w programy odpowiednie dla danej organizacji, aby śledzić przeglądy dla aplikacji dotyczących zgodności lub aplikacji narażonych na ryzyko.

Aby uzyskać więcej informacji, zobacz [Przeglądy dostępu usługi Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-azure-ad-controls-access-reviews-overview).

---

### <a name="hide-third-party-applications-from-my-apps-and-the-office-365-app-launcher"></a>Ukrywanie aplikacji innych firm w witrynie Moje aplikacje i uruchamianie aplikacji usługi Office 365

**Typ:** Nowa funkcja  
**Kategoria usługi:** Moje aplikacje  
**Możliwości produktu:** Logowanie jednokrotne  

Teraz możesz lepiej zarządzać aplikacjami, które pojawiają się w portalach użytkowników za pośrednictwem nowej właściwości **ukryć aplikację.** Możesz ukryć aplikacje, aby pomóc w przypadkach, gdy kafelki aplikacji są wyświetlane w usługach zaplecza lub zduplikowane kafelki i zaśmiecanie uruchamiania aplikacji użytkowników. Przełącznik znajduje się w sekcji **Właściwości** aplikacji innej firmy i jest oznaczony jako **Widoczny dla użytkownika?** Aplikację można również programowo ukryć za pośrednictwem programu PowerShell. 

Aby uzyskać więcej informacji, zobacz [Ukrywanie aplikacji innej firmy przed korzystaniem z usługi Azure AD.](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-hide-third-party-app) 


**Co jest dostępne?**

 W ramach przejścia do nowej konsoli administracyjnej dostępne są dwa nowe interfejsy API do pobierania dzienników aktywności usługi Azure AD. Nowy zestaw interfejsów API zapewnia bogatsze funkcje filtrowania i sortowania, a także zapewnia bogatsze działania inspekcji i logowania. Dane dostępne wcześniej za pośrednictwem raportów zabezpieczeń są teraz dostępne za pośrednictwem interfejsu API wykrywania ryzyka ochrony tożsamości w programie Microsoft Graph.


## <a name="september-2017"></a>Wrzesień 2017

### <a name="hotfix-for-identity-manager"></a>Poprawka dla Menedżera tożsamości

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Menedżer tożsamości  
**Możliwości produktu:** Zarządzanie cyklem życia tożsamości  

Pakiet zbiorczy poprawek (kompilacja 4.4.1642.0) jest dostępny od 25 września 2017 r. dla dodatku Service Pack 1 dla programu Identity Manager 2016. Ten pakiet zbiorczy:

- Rozwiązuje problemy i dodaje ulepszenia.
- Jest zbiorczą aktualizacją, która zastępuje wszystkie aktualizacje dodatku Service Pack 1 programu Identity Manager 2016 do kompilacji 4.4.1459.0 dla Menedżera tożsamości 2016. 
- Wymaga, aby mieć Identity Manager 2016 kompilacji 4.4.1302.0. 

Aby uzyskać więcej informacji, zobacz [Pakiet zestawienia poprawek (kompilacja 4.4.1642.0) jest dostępny dla programu Identity Manager 2016 z dodatkiem Service Pack 1](https://support.microsoft.com/help/4021562). 

---
