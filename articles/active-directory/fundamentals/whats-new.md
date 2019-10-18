---
title: Co nowego? Informacje o wersji — Azure Active Directory | Microsoft Docs
description: Dowiedz się, co nowego w Azure Active Directory, takich jak Najnowsze informacje o wersji, znane problemy, poprawki błędów, przestarzałe funkcje i nadchodzące zmiany.
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
ms.date: 10/17/2019
ms.author: lizross
ms.reviewer: dhanyahk
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: c475bfc6179f3604ed6b83cafc75d34f4359513f
ms.sourcegitcommit: 6eecb9a71f8d69851bc962e2751971fccf29557f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/17/2019
ms.locfileid: "72532839"
---
# <a name="whats-new-in-azure-active-directory"></a>Co nowego w Azure Active Directory?

>Otrzymuj powiadomienia o tym, kiedy należy ponownie odwiedzić Tę stronę pod kątem aktualizacji przez skopiowanie i wklejenie tego adresu URL: `https://docs.microsoft.com/api/search/rss?search=%22release+notes+for+azure+AD%22&locale=en-us` do ikony czytnika ![RSS kanału informacyjnego ](./media/whats-new/feed-icon-16x16.png) czytnika kanału informacyjnego.

Usługa Azure AD otrzymuje ulepszenia na bieżąco. Aby zachować aktualność najnowszych zmian, ten artykuł zawiera informacje na temat:

- Najnowsze wersje
- Znane problemy
- Poprawki błędów
- Przestarzałe funkcje
- Plany zmian

Ta strona jest aktualizowana co miesiąc, dlatego należy ją regularnie odwiedzać. Jeśli szukasz elementów, które są starsze niż sześć miesięcy, możesz je znaleźć w archiwum, aby poznać nowości [w Azure Active Directory](whats-new-archive.md).

---

## <a name="october-2019"></a>Październik 2019

### <a name="deprecation-of-the-identityriskevent-api-for-azure-ad-identity-protection-risk-detections"></a>Wycofanie interfejsu API identityRiskEvent na potrzeby wykrywania ryzyka Azure AD Identity Protection  

**Typ:** Planowanie zmiany  
**Kategoria usługi:** Ochrona tożsamości  
**Możliwość produktu:** Ochrona tożsamości & zabezpieczenia

W odpowiedzi na opinie dla deweloperów Azure AD — wersja Premium w przypadku subskrybentów P2 mogą teraz wykonywać złożone zapytania dotyczące danych wykrywania ryzyka Azure AD Identity Protection przy użyciu nowego interfejsu API riskDetection na potrzeby Microsoft Graph. Istniejąca wersja beta interfejsu API [identityRiskEvent](https://docs.microsoft.com/en-us/graph/api/resources/identityriskevent?view=graph-rest-beta) przestanie zwracać dane **na około 10 stycznia 2020**. Jeśli Twoja organizacja korzysta z interfejsu API identityRiskEvent, należy przejść do nowego interfejsu API riskDetection.

Więcej informacji na temat nowego interfejsu API riskDetection można znaleźć w [dokumentacji dotyczącej interfejsu API wykrywania ryzyka](https://aka.ms/RiskDetectionsAPI).

---

## <a name="september-2019"></a>Wrzesień 2019 r.

### <a name="deprecation-of-the-power-bi-content-packs"></a>Przestarzałe pakiety zawartości Power BI

**Typ:** Przestarzałe  
**Kategoria usługi:** Reporting  
**Możliwość produktu:** Monitorowanie & raportowania

Power BI Przestarzałe pakiety zawartości z magazynu i od **1 października 2019**, nie obsługujemy pakietu zawartości Power BI usługi Azure AD. Klienci korzystający obecnie z pakietu zawartości mogą nadal z nich korzystać, nawet jeśli źródłowe interfejsy API są starszymi wersjami. Alternatywą dla tego pakietu zawartości jest użycie skoroszytów usługi Azure AD (opartych na Log Analytics) w celu uzyskania szczegółowych informacji dotyczących usług związanych z usługą Azure AD.

Aby uzyskać więcej informacji na temat skoroszytów, zobacz [jak używać skoroszytów Azure monitor dla Azure Active Directory raportów](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-use-azure-monitor-workbooks). Aby uzyskać więcej informacji na temat wycofania pakietów zawartości, zobacz wpis w blogu dotyczący [ogłaszania Power BI szablonów aplikacji ogólnie dostępna](https://powerbi.microsoft.com/blog/announcing-power-bi-template-apps-general-availability/) .

---

### <a name="my-profile-is-re-naming-and-integrating-with-the-microsoft-office-account-page"></a>Moje profile są zmieniane i integrowane za pomocą strony konta Microsoft Office

**Typ:** Planowanie zmiany  
**Kategoria usługi:** Mój profil/konto  
**Możliwość produktu:** Społeczności

Od października środowisko my profilu zostanie moje konto. W ramach tej zmiany wszędzie tam, gdzie się znajduje, **mój profil** zmieni się na **Moje konto**. W oparciu o zmiany nazw i niektóre ulepszenia projektowe zaktualizowane środowisko oferuje dodatkową integrację ze stroną konta Microsoft Office. W związku z tym będziesz mieć dostęp do instalacji i subskrypcji pakietu Office na stronie z **omówieniem konta** oraz z preferencjami kontaktu związanymi z pakietem Office na stronie **prywatność** .

Aby uzyskać więcej informacji na temat środowiska my profile (wersja zapoznawcza), zobacz temat [Omówienie portalu My profil (wersja zapoznawcza)](https://docs.microsoft.com/azure/active-directory/user-help/myprofile-portal-overview).

---

### <a name="bulk-manage-groups-and-members-using-csv-files-in-the-azure-ad-portal-public-preview"></a>Zbiorcze zarządzanie grupami i członkami przy użyciu plików CSV w portalu usługi Azure AD (publiczna wersja zapoznawcza)

**Typ:** Nowa funkcja  
**Kategoria usługi:** Zarządzanie grupami  
**Możliwość produktu:** Społeczności

Z przyjemnością ogłaszamy publiczną wersję zapoznawczą środowiska zarządzania grupami zbiorczymi w portalu usługi Azure AD. Można teraz używać pliku CSV i portalu usługi Azure AD do zarządzania grupami i listami elementów członkowskich, w tym:

- Dodawanie członków do grupy lub usuwanie ich z niej.

- Pobieranie listy grup z katalogu.

- Pobieranie listy członków grupy dla określonej grupy.

Aby uzyskać więcej informacji, zobacz [zbiorcze Dodawanie członków](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-import-members), [zbiorcze usuwanie](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-remove-members)członków, [listy członków pobierania zbiorczego](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-download-members)i [listę grup pobierania zbiorczego](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-download).

---

### <a name="dynamic-consent-is-now-supported-through-a-new-admin-consent-endpoint"></a>Zgoda dynamiczna jest teraz obsługiwana za pomocą nowego punktu końcowego zgody na administratora

**Typ:** Nowa funkcja  
**Kategoria usługi:** Uwierzytelnienia (nazwy logowania)  
**Możliwość produktu:** Uwierzytelnianie użytkownika

Utworzyliśmy nowy punkt końcowy zgody administratora, który będzie obsługiwał zgodę dynamiczną, co jest przydatne w przypadku aplikacji, które chcą korzystać z modelu zgody dynamicznej na platformie tożsamości firmy Microsoft.

Aby uzyskać więcej informacji o sposobach korzystania z tego nowego punktu końcowego, zobacz [Korzystanie z punktu końcowego zgody administratora](https://docs.microsoft.com/azure/active-directory/develop/v2-admin-consent).

---

### <a name="new-azure-ad-global-reader-role"></a>Nowa rola czytnika globalnego usługi Azure AD

**Typ:** Nowa funkcja  
**Kategoria usługi:** RBAC  
**Możliwość produktu:** Access Control

Od 24 września 2019 zostanie rozpoczęta nowa rola Azure Active Directory (AD) o nazwie Reader Global. To wdrożenie rozpocznie się wraz z produkcyjnym i globalnym klientom w chmurze (w zatoce), kończąc na całym świecie w październiku.

Globalna rola czytnika to odpowiedni dla administratora globalnego tylko do odczytu. Użytkownicy w tej roli mogą odczytywać ustawienia i informacje administracyjne w ramach usług Microsoft 365, ale nie mogą podejmować działań związanych z zarządzaniem. Utworzyliśmy globalną rolę czytelnika, która pomaga w zmniejszeniu liczby administratorów globalnych w organizacji. Ponieważ konta administratorów globalnych są zaawansowane i podatne na ataki, zalecamy użycie mniej niż pięciu administratorów globalnych. Zalecamy korzystanie z roli czytnika globalnego na potrzeby planowania, inspekcji lub badań. Zalecamy również korzystanie z roli czytnika globalnego w połączeniu z innymi ograniczonymi rolami administratora, takimi jak administrator programu Exchange, aby ułatwić wykonanie pracy bez konieczności posiadania roli administratora globalnego.

Globalna rola czytnika współpracuje z nowym centrum administracyjnym Microsoft 365, centrum administracyjnym programu Exchange, centrum administracyjnym zespołów, Security Center, centrum zgodności, centrum administracyjnym usługi Azure AD oraz centrum administracyjnym zarządzania urządzeniami.

>[!NOTE]
> Na początku publicznej wersji zapoznawczej rola czytnika globalnego nie będzie współpracująca z: SharePoint, Privileged Access Management, Skrytka klienta, etykietami czułości, cyklem życia zespołów, raportowaniem & wywołań analizy, zespoły IP zarządzanie urządzeniami telefonicznymi i zespołami Pełnotekstowy. Wszystkie te usługi są przeznaczone do pracy z rolą w przyszłości.

Aby uzyskać więcej informacji, zobacz [uprawnienia roli administrator w Azure Active Directory](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles).

---

### <a name="access-an-on-premises-report-server-from-your-power-bi-mobile-app-using-azure-active-directory-application-proxy"></a>Uzyskiwanie dostępu do lokalnego serwera raportów z aplikacji mobilnej Power BI przy użyciu serwer proxy aplikacji usługi Azure Active Directory

**Typ:** Nowa funkcja  
**Kategoria usługi:** Serwer proxy aplikacji  
**Możliwość produktu:** Access Control

Nowa integracja między aplikacją mobilną Power BI a usługą Azure serwer proxy aplikacji usługi Azure AD umożliwia bezpieczne logowanie do aplikacji mobilnej Power BI i wyświetlanie dowolnych raportów organizacji hostowanych w Serwer raportów usługi Power BI lokalnym.

Aby uzyskać informacje o aplikacji mobilnej Power BI, w tym o lokalizacji, w której należy pobrać aplikację, zapoznaj się z [witryną Power BI](https://powerbi.microsoft.com/mobile/). Aby uzyskać więcej informacji na temat sposobu konfigurowania aplikacji mobilnej Power BI przy użyciu usługi Azure serwer proxy aplikacji usługi Azure AD, zobacz [Włączanie dostępu zdalnego do Power BI Mobile przy użyciu usługi azure serwer proxy aplikacji usługi Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-integrate-with-power-bi).

---

### <a name="new-version-of-the-azureadpreview-powershell-module-is-available"></a>Dostępna jest nowa wersja modułu AzureADPreview PowerShell

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Różnych  
**Możliwość produktu:** Katalogi

Do modułu AzureADPreview dodano nowe polecenia cmdlet, które ułatwiają Definiowanie i przypisywanie ról niestandardowych w usłudze Azure AD, w tym:

- `Add-AzureADMSFeatureRolloutPolicyDirectoryObject`
- `Get-AzureADMSFeatureRolloutPolicy`
- `New-AzureADMSFeatureRolloutPolicy`
- `Remove-AzureADMSFeatureRolloutPolicy`
- `Remove-AzureADMSFeatureRolloutPolicyDirectoryObject`
- `Set-AzureADMSFeatureRolloutPolicy`

---

### <a name="new-version-of-azure-ad-connect"></a>Nowa wersja Azure AD Connect

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Różnych  
**Możliwość produktu:** Katalogi

Firma Microsoft udostępniła zaktualizowaną wersję Azure AD Connect dla klientów korzystających z aktualizacji. Ta nowa wersja zawiera kilka nowych funkcji, ulepszeń i poprawek błędów. Aby uzyskać więcej informacji o tej nowej wersji, zobacz [Azure AD Connect: historia](https://docs.microsoft.com/azure/active-directory/hybrid/reference-connect-version-history#14250)wersji.

---

### <a name="azure-multi-factor-authentication-mfa-server-version-802-is-now-available"></a>Serwer usługi Azure Multi-Factor Authentication (MFA) w wersji 8.0.2 jest teraz dostępny

**Typ:** FIXED  
**Kategoria usługi:** Funkcja  
**Możliwość produktu:** Ochrona tożsamości & zabezpieczenia

Jeśli jesteś istniejącym klientem, który aktywuje serwer usługi MFA przed 1 lipca 2019, możesz teraz pobrać najnowszą wersję serwera usługi MFA (wersja 8.0.2). W tej nowej wersji:

- Rozwiązano problem polegający na tym, że gdy usługa Azure AD Sync zmieni użytkownika z wyłączone na włączone, do użytkownika zostanie wysłana wiadomość e-mail.

- Rozwiązano problem, aby klienci mogli pomyślnie uaktualnić program, jednocześnie używając funkcji tagów.

- Dodano kod kraju Kosowa (+ 383).

- Dodano jednorazowe rejestrowanie inspekcji do dziennika MultiFactorAuthSvc. log.

- Ulepszona wydajność zestawu SDK usługi sieci Web.

- Rozwiązano inne drobne błędy.

Od 1 lipca 2019 firma Microsoft zatrzymała ofertę serwera usługi MFA dla nowych wdrożeń. Nowi klienci, którzy wymagają uwierzytelniania wieloskładnikowego, powinni korzystać z usługi Azure Multi-Factor Authentication opartej na chmurze. Aby uzyskać więcej informacji, zobacz [Planowanie wdrożenia usługi Azure Multi-Factor Authentication opartej na chmurze](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted).

---

## <a name="august-2019"></a>Sierpień 2019 r.

### <a name="plan-for-change-deprecation-of-the-power-bi-content-packs"></a>Zaplanuj zmianę: zaniechanie Power BI pakietów zawartości

**Typ:** Planowanie zmiany  
**Kategoria usługi:** Reporting  
**Możliwość produktu:** Monitorowanie & raportowania

Od 1 października 2019 Power BI rozpocznie się wycofanie wszystkich pakietów zawartości, w tym pakietu zawartości usługi Azure AD Power BI. Alternatywą dla tego pakietu zawartości jest użycie skoroszytów usługi Azure AD w celu uzyskania szczegółowych informacji dotyczących usług związanych z usługą Azure AD. Dostępne są dodatkowe skoroszyty, w tym skoroszyty dotyczące zasad dostępu warunkowego w trybie tylko raportowanie, informacje oparte na zgodzie aplikacji i inne.

Aby uzyskać więcej informacji na temat skoroszytów, zobacz [jak używać skoroszytów Azure monitor dla Azure Active Directory raportów](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-use-azure-monitor-workbooks). Aby uzyskać więcej informacji na temat wycofania pakietów zawartości, zobacz wpis w blogu dotyczący [ogłaszania Power BI szablonów aplikacji ogólnie dostępna](https://powerbi.microsoft.com/blog/announcing-power-bi-template-apps-general-availability/) .

---

### <a name="enhanced-search-filtering-and-sorting-for-groups-is-available-in-the-azure-ad-portal-public-preview"></a>Ulepszone wyszukiwanie, filtrowanie i sortowanie dla grup jest dostępne w portalu usługi Azure AD (publiczna wersja zapoznawcza)

**Typ:** Nowa funkcja  
**Kategoria usługi:** Zarządzanie grupami  
**Możliwość produktu:** Społeczności

Z przyjemnością ogłaszamy publiczną wersję zapoznawczą rozszerzonych środowisk związanych z grupami w portalu usługi Azure AD. Te ulepszenia ułatwiają lepsze zarządzanie grupami i listami składowymi, zapewniając:

- Zaawansowane możliwości wyszukiwania, takie jak wyszukiwanie podciągów na listach grup.
- Zaawansowane opcje filtrowania i sortowania na listach elementów członkowskich i właścicieli.
- Nowe możliwości wyszukiwania dla list elementów członkowskich i właścicieli.
- Dokładniejsze liczby grup dla dużych grup.

Aby uzyskać więcej informacji, zobacz [Zarządzanie grupami w Azure Portal](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-members-azure-portal?context=azure/active-directory/users-groups-roles/context/ugr-context).

---

### <a name="new-custom-roles-are-available-for-app-registration-management-public-preview"></a>Nowe role niestandardowe są dostępne dla zarządzania rejestracją aplikacji (publiczna wersja zapoznawcza)

**Typ:** Nowa funkcja  
**Kategoria usługi:** RBAC  
**Możliwość produktu:** Access Control

Role niestandardowe (dostępne w ramach subskrypcji usługi Azure AD P1 lub P2) mogą teraz pomóc zapewnić szczegółowy dostęp, umożliwiając tworzenie definicji ról z określonymi uprawnieniami, a następnie przypisywanie tych ról do określonych zasobów. Obecnie można tworzyć role niestandardowe przy użyciu uprawnień do zarządzania rejestracjami aplikacji, a następnie przypisywania roli do określonej aplikacji. Aby uzyskać więcej informacji na temat ról niestandardowych, zobacz [Niestandardowe role administratorów w Azure Active Directory (wersja zapoznawcza)](https://docs.microsoft.com/azure/active-directory/users-groups-roles/roles-custom-overview).

Jeśli potrzebujesz dodatkowych uprawnień lub zasobów, które nie są obecnie widoczne, możesz wysłać opinię do naszej [witryny opinii o platformie Azure](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032) i dodać Twoje żądanie do naszej mapy aktualizacji.

---

### <a name="new-provisioning-logs-can-help-you-monitor-and-troubleshoot-your-app-provisioning-deployment-public-preview"></a>Nowe dzienniki aprowizacji mogą pomóc w monitorowaniu i rozwiązywaniu problemów z wdrożeniem aprowizacji aplikacji (publiczna wersja zapoznawcza)

**Typ:** Nowa funkcja  
**Kategoria usługi:** Inicjowanie obsługi aplikacji  
**Możliwość produktu:** Zarządzanie cyklem życia tożsamości

Nowe dzienniki aprowizacji są dostępne, aby ułatwić monitorowanie i rozwiązywanie problemów z wdrożeniem aprowizacji użytkowników i grup. Te nowe pliki dziennika zawierają informacje o:

- Które grupy zostały pomyślnie utworzone w [usługi ServiceNow](https://docs.microsoft.com/azure/active-directory/saas-apps/servicenow-provisioning-tutorial)
- Jakie role zostały zaimportowane z [Amazon Web Services (AWS)](https://docs.microsoft.com/azure/active-directory/saas-apps/amazon-web-service-tutorial#configure-and-test-azure-ad-single-sign-on-for-amazon-web-services-aws)
- Którzy pracownicy nie zaimportowali z [produktu Workday](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial)

Aby uzyskać więcej informacji, zobacz artykuł [aprowizacji raportów w portalu Azure Active Directory (wersja zapoznawcza)](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs).

---

### <a name="new-security-reports-for-all-azure-ad-administrators-general-availability"></a>Nowe raporty zabezpieczeń dla wszystkich administratorów usługi Azure AD (ogólna dostępność)

**Typ:** Nowa funkcja  
**Kategoria usługi:** Ochrona tożsamości  
**Możliwość produktu:** Ochrona tożsamości & zabezpieczenia

Domyślnie wszyscy Administratorzy usługi Azure AD wkrótce będą mogli uzyskiwać dostęp do nowoczesnych raportów zabezpieczeń w ramach usługi Azure AD. Do końca września będzie można użyć transparentu w górnej części nowoczesnych raportów zabezpieczeń, aby powrócić do starych raportów.

Nowoczesne raporty zabezpieczeń zapewniają dodatkowe możliwości ze starszych wersji, w tym:

- Zaawansowane filtrowanie i sortowanie
- Akcje zbiorcze, takie jak odrzucanie ryzyka użytkownika
- Potwierdzenie ochrony jednostek naruszonych lub bezpiecznych
- Stan ryzyka, obejmujący: zagrożone, odrzucone, korygowane i potwierdzone naruszone
- Nowe wykrywania związane z ryzykiem (dostępne dla subskrybentów Azure AD — wersja Premium)

Aby uzyskać więcej informacji, zobacz [ryzykowni użytkownicy](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-investigate-risk#risky-users-report), [ryzykowne logowania](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-investigate-risk#risky-sign-ins-report)i [wykrywanie ryzyka](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-investigate-risk#risk-detections-report).

---

### <a name="user-assigned-managed-identity-is-available-for-virtual-machines-and-virtual-machine-scale-sets-general-availability"></a>Tożsamość zarządzana przypisana przez użytkownika jest dostępna dla Virtual Machines i Virtual Machine Scale Sets (ogólna dostępność)

**Typ:** Nowa funkcja  
**Kategoria usługi:** Zarządzane tożsamości dla zasobów platformy Azure  
**Możliwość produktu:** Środowisko deweloperskie

Tożsamości zarządzane przypisane przez użytkownika są teraz ogólnie dostępne dla Virtual Machines i Virtual Machine Scale Sets. W ramach tego działania platforma Azure może utworzyć tożsamość w dzierżawie usługi Azure AD, która jest zaufana dla używanej subskrypcji, i może być przypisana do co najmniej jednego wystąpienia usługi platformy Azure. Aby uzyskać więcej informacji na temat tożsamości zarządzanych przypisanych przez użytkownika, zobacz [co to jest tożsamość zarządzana dla zasobów platformy Azure?](https://aka.ms/azuremanagedidentity).

---

### <a name="users-can-reset-their-passwords-using-a-mobile-app-or-hardware-token-general-availability"></a>Użytkownicy mogą resetować swoje hasła przy użyciu aplikacji mobilnej lub tokenu sprzętowego (ogólna dostępność)

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Samoobsługowe resetowanie hasła  
**Możliwość produktu:** Uwierzytelnianie użytkownika

Użytkownicy, którzy zarejestrowali aplikację mobilną w organizacji, mogą teraz resetować swoje hasła, zatwierdzając powiadomienie w aplikacji Microsoft Authenticator lub wprowadzając kod z aplikacji mobilnej lub tokenu sprzętowego.

Aby uzyskać więcej informacji, zobacz [jak to działa: Samoobsługowe resetowanie hasła w usłudze Azure AD](https://aka.ms/authappsspr). Aby uzyskać więcej informacji na temat środowiska użytkownika, zobacz [Resetowanie własnego hasła służbowego — Omówienie](https://docs.microsoft.com/azure/active-directory/user-help/user-help-password-reset-overview).

---

### <a name="adalnet-ignores-the-msalnet-shared-cache-for-on-behalf-of-scenarios"></a>ADAL.NET ignoruje udostępnioną pamięć podręczną MSAL.NET dla scenariuszy w imieniu

**Typ:** FIXED  
**Kategoria usługi:** Uwierzytelnienia (nazwy logowania)  
**Możliwość produktu:** Uwierzytelnianie użytkownika

Począwszy od biblioteki Azure AD Authentication Library (ADAL.NET) w wersji 5.0.0 — wersja zapoznawcza, deweloperzy aplikacji muszą [serializować jedną pamięć podręczną na konto dla aplikacji sieci Web i interfejsów API sieci Web](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Token-cache-serialization#custom-token-cache-serialization-in-web-applications--web-api). W przeciwnym razie niektóre scenariusze korzystające z [przepływu w imieniu](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-api-call-api-app-configuration#on-behalf-of-flow), wraz z pewnymi przypadkami użycia `UserAssertion`, mogą spowodować podniesienie uprawnień. Aby uniknąć tej luki w zabezpieczeniach, ADAL.NET teraz ignoruje bibliotekę uwierzytelniania Microsoft dla udostępnionej pamięci podręcznej usługi dotnet (MSAL.NET) dla scenariuszy w imieniu użytkownika.

Aby uzyskać więcej informacji o tym problemie, zobacz temat [Luka w zabezpieczeniach Azure Active Directory podniesienia uprawnień w bibliotece uwierzytelniania](https://portal.msrc.microsoft.com/security-guidance/advisory/CVE-2019-1258).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---august-2019"></a>Nowe aplikacje federacyjne dostępne w galerii aplikacja usługi Azure AD — sierpień 2019

**Typ:** Nowa funkcja  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwości produktu:** integracja innej firmy

W sierpniu 2019 dodaliśmy te 26 nowych aplikacji z obsługą Federacji do galerii aplikacji:

[Projektowi Civic Innovation platform](https://docs.microsoft.com/azure/active-directory/saas-apps/civic-platform-tutorial), [Amazon Business](https://docs.microsoft.com/azure/active-directory/saas-apps/amazon-business-tutorial), [ProNovos Ops Manager](https://docs.microsoft.com/azure/active-directory/saas-apps/pronovos-ops-manager-tutorial), [Cognidox](https://docs.microsoft.com/azure/active-directory/saas-apps/cognidox-tutorial), [Viareport, Inativ Portal (Europa)](https://docs.microsoft.com/azure/active-directory/saas-apps/viareports-inativ-portal-europe-tutorial), [Azure Databricks](https://azure.microsoft.com/services/databricks) [](https://docs.microsoft.com/azure/active-directory/saas-apps/academy-attendance-tutorial), [Robin](https://docs.microsoft.com/azure/active-directory/saas-apps/robin-tutorial) [](https://sync.appfluence.com/pmwebng/) [Cousto MySpace](https://cousto.platformers.be/account/login), [Uploadcare](https://uploadcare.com/accounts/signup/), [Carbonite — kopia zapasowa punktu końcowego](https://docs.microsoft.com/azure/active-directory/saas-apps/carbonite-endpoint-backup-tutorial), [CPQSync, Cincom](https://docs.microsoft.com/azure/active-directory/saas-apps/cpqsync-by-cincom-tutorial), [Chargebee](https://docs.microsoft.com/azure/active-directory/saas-apps/chargebee-tutorial), [dostarczanie. Media™ Portal](https://portal.deliver.media), [teraźniejszości Education](https://docs.microsoft.com/azure/active-directory/saas-apps/frontline-education-tutorial), [F5](https://www.f5.com/products/security/access-policy-manager), [stashcat AD Łączenie](https://www.stashcat.com), [migotanie](https://docs.microsoft.com/azure/active-directory/saas-apps/blink-tutorial), [Vocoli](https://docs.microsoft.com/azure/active-directory/saas-apps/vocoli-tutorial), [ProNovos Analytics](https://docs.microsoft.com/azure/active-directory/saas-apps/pronovos-analytics-tutorial), [Sigstr](https://docs.microsoft.com/azure/active-directory/saas-apps/sigstr-tutorial), [Darwinbox](https://docs.microsoft.com/azure/active-directory/saas-apps/darwinbox-tutorial), [Watch według kolorów](https://docs.microsoft.com/azure/active-directory/saas-apps/watch-by-colors-tutorial), [zespół](https://docs.microsoft.com/azure/active-directory/saas-apps/harness-tutorial), [EAB, opieka strategiczna](https://docs.microsoft.com/azure/active-directory/saas-apps/eab-navigate-strategic-care-tutorial)

Aby uzyskać więcej informacji na temat aplikacji, zobacz [SaaS Application Integration with Azure Active Directory](https://aka.ms/appstutorial). Aby uzyskać więcej informacji na temat tworzenia listy aplikacji w galerii aplikacji usługi Azure AD, zobacz [Wyświetlanie listy aplikacji w galerii aplikacji Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="new-versions-of-the-azuread-powershell-and-azureadpreview-powershell-modules-are-available"></a>Dostępne są nowe wersje modułów AzureAD PowerShell i AzureADPreview PowerShell

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Różnych  
**Możliwość produktu:** Katalogi

Dostępne są nowe aktualizacje modułów programu PowerShell AzureAD i AzureAD w wersji zapoznawczej:

- Dodano nowy parametr `-Filter` do parametru `Get-AzureADDirectoryRole` w module AzureAD. Ten parametr ułatwia filtrowanie ról w katalogu zwracanych przez polecenie cmdlet.
- Do modułu AzureADPreview dodano nowe polecenia cmdlet, które ułatwiają Definiowanie i przypisywanie ról niestandardowych w usłudze Azure AD, w tym:

    - `Get-AzureADMSRoleAssignment`
    - `Get-AzureADMSRoleDefinition`
    - `New-AzureADMSRoleAssignment`
    - `New-AzureADMSRoleDefinition`
    - `Remove-AzureADMSRoleAssignment`
    - `Remove-AzureADMSRoleDefinition`
    - `Set-AzureADMSRoleDefinition`

---

### <a name="improvements-to-the-ui-of-the-dynamic-group-rule-builder-in-the-azure-portal"></a>Ulepszenia interfejsu użytkownika konstruktora reguł grupy dynamicznej w Azure Portal

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Zarządzanie grupami  
**Możliwość produktu:** Społeczności

Wprowadziliśmy pewne ulepszenia interfejsu użytkownika dla konstruktora dynamicznej reguły grupy, dostępne w Azure Portal, aby łatwiej skonfigurować nową regułę lub zmienić istniejące reguły. To ulepszenie projektu pozwala tworzyć reguły z maksymalnie pięcioma wyrażeniami zamiast tylko jeden. Zaktualizowaliśmy także listę właściwości urządzenia w celu usunięcia przestarzałych właściwości urządzenia.

Aby uzyskać więcej informacji, zobacz [Zarządzanie dynamicznymi regułami członkostwa](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-update-rule).

---

### <a name="new-microsoft-graph-app-permission-available-for-use-with-access-reviews"></a>Nowe uprawnienie Microsoft Graph aplikacji dostępne do przeglądu dostępu

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Przeglądy dostępu  
**Możliwość produktu:** Zarządzanie tożsamościami

Wprowadziliśmy nowe uprawnienie Microsoft Graph aplikacji `AccessReview.ReadWrite.Membership`, które umożliwia aplikacjom automatyczne tworzenie i pobieranie przeglądów dostępu dla członkostwa w grupach i przypisań aplikacji. To uprawnienie może być używane przez zaplanowane zadania lub jako część automatyzacji, bez konieczności logowania kontekstu użytkownika.

Aby uzyskać więcej informacji, zobacz [przykład jak utworzyć przeglądy dostępu do usługi Azure AD przy użyciu uprawnień aplikacji Microsoft Graph przy użyciu blogu programu PowerShell](https://techcommunity.microsoft.com/t5/Azure-Active-Directory/Example-how-to-create-Azure-AD-access-reviews-using-Microsoft/m-p/807241).

---

### <a name="azure-ad-activity-logs-are-now-available-for-government-cloud-instances-in-azure-monitor"></a>Dzienniki aktywności usługi Azure AD są teraz dostępne dla wystąpień chmury dla instytucji rządowych w Azure Monitor

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Reporting  
**Możliwość produktu:** Monitorowanie & raportowania

Przyjemnością się ogłaszamy, że dzienniki aktywności usługi Azure AD są teraz dostępne dla wystąpień chmury dla instytucji rządowych w Azure Monitor. Teraz możesz wysyłać dzienniki usługi Azure AD na konto magazynu lub do centrum zdarzeń, aby zintegrować je z narzędziami SIEM, takimi jak [SumoLogic](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-sumologic), [Splunk](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-splunk)i [ArcSight](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-arcsight). 

Aby uzyskać więcej informacji na temat konfigurowania Azure Monitor, zobacz [dzienniki aktywności usługi Azure AD w Azure monitor](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-activity-logs-azure-monitor#cost-considerations).

---

### <a name="update-your-users-to-the-new-enhanced-security-info-experience"></a>Aktualizowanie użytkowników do nowego, ulepszonego środowiska informacji o zabezpieczeniach

**Typ:** Zmieniono funkcję  
**Kategoria usługi:**  Uwierzytelnienia (nazwy logowania)   
**Możliwość produktu:** Uwierzytelnianie użytkownika

25 września 2019 zostanie wyłączone stare, nieulepszone środowisko informacyjne zabezpieczeń na potrzeby rejestrowania i zarządzania informacjami o zabezpieczeniach użytkownika i włączania tylko nowej, [ulepszonej wersji](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Cool-enhancements-to-the-Azure-AD-combined-MFA-and-password/ba-p/354271). Oznacza to, że użytkownicy nie będą już mogli używać starego środowiska.

Aby uzyskać więcej informacji na temat udoskonalonego środowiska informacji o zabezpieczeniach, zobacz [dokumentację administracyjną](https://aka.ms/securityinfodocs)  and naszej [dokumentacji użytkownika](https://aka.ms/securityinfoguide).

#### <a name="to-turn-on-this-new-experience-you-must"></a>Aby włączyć to nowe środowisko, musisz:

1. Zaloguj się do Azure Portal jako Administrator globalny lub administrator użytkowników.

2. Przejdź do pozycji **Azure Active Directory > Ustawienia użytkownika > zarządzanie ustawieniami funkcji Podgląd panelu dostępu**.

3. **Użytkownicy mogą korzystać z funkcji w wersji zapoznawczej w celu rejestrowania obszaru informacje zabezpieczające i zarządzania nim** , wybrać opcję **wybrane**, a następnie wybrać grupę użytkowników lub wybrać **wszystkie** , aby włączyć tę funkcję dla wszystkich użytkowników w dzierżawie.

4. W programie * * użytkownicy mogą używać funkcji w wersji zapoznawczej do rejestrowania i zarządzania zabezpieczeniami * * info * * * * — wybierz opcję **Brak**.

5. Zapisz ustawienia.

    Po zapisaniu ustawień nie będziesz mieć już dostępu do starych informacji zabezpieczających.

>[!Important]
>Jeśli te kroki nie zostaną wykonane przed 25 września 2019, dzierżawa Azure Active Directory zostanie automatycznie włączona dla udoskonalonego środowiska. Jeśli masz pytania, skontaktuj się z nami pod adresem registrationpreview@microsoft.com.

---

### <a name="authentication-requests-using-post-logins-will-be-more-strictly-validated"></a>Żądania uwierzytelniania przy użyciu wpisów logowania będą bardziej ścisłe.

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Uwierzytelnienia (nazwy logowania)  
**Możliwość produktu:** Norm

Od 2 września 2019 żądanie uwierzytelnienia przy użyciu metody POST będzie bardziej ściśle zweryfikowane względem standardów protokołu HTTP. W przypadku opcji spacje i podwójne cudzysłowy (") nie będą już usuwane z wartości formularza żądania. Te zmiany nie są oczekiwane na podział istniejących klientów i ułatwiają zapewnienie, że żądania wysyłane do usługi Azure AD są niezawodnie obsługiwane za każdym razem.

Aby uzyskać więcej informacji, zobacz [powiadomienia o zmianach w usłudze Azure AD](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#post-form-semantics-will-be-enforced-more-strictly---spaces-and-quotes-will-be-ignored).

---

## <a name="july-2019"></a>Lipiec 2019 r.

### <a name="plan-for-change-application-proxy-service-update-to-support-only-tls-12"></a>Zaplanuj zmianę: Aktualizacja usługi proxy aplikacji do obsługi tylko protokołu TLS 1,2

**Typ:** Planowanie zmiany  
**Kategoria usługi:** Serwer proxy aplikacji  
**Możliwość produktu:** Access Control

Aby zapewnić sobie Najmocniejsze szyfrowanie, zaczniemy ograniczać dostęp usługi serwera proxy aplikacji tylko do protokołów TLS 1,2. To ograniczenie zostanie początkowo wdrożone dla klientów, którzy już korzystają z protokołów TLS 1,2, więc nie zobaczysz wpływu. Zakończenie korzystania z protokołów TLS 1,0 i TLS 1,1 zostanie zakończone 31 sierpnia 2019. Klienci nadal korzystający z protokołów TLS 1,0 i TLS 1,1 otrzymają zaawansowaną informację na temat przygotowania do tej zmiany.

Aby zachować połączenie z usługą serwera proxy aplikacji w ramach tej zmiany, zalecamy, aby upewnić się, że kombinacje na serwerze klienta i przeglądarki zostały zaktualizowane pod kątem korzystania z protokołu TLS 1,2. Zalecamy również, aby dołączać do aplikacji publikowanych za pomocą usługi serwera proxy aplikacji wszystkie systemy klienckie używane przez pracowników.

Aby uzyskać więcej informacji, zobacz [Dodawanie aplikacji lokalnej dla dostępu zdalnego przy użyciu serwera proxy aplikacji w Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application).

---

### <a name="plan-for-change-design-updates-are-coming-for-the-application-gallery"></a>Planowanie zmian: Aktualizacje projektu są dostępne dla galerii aplikacji

**Typ:** Planowanie zmiany  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwość produktu:** ZWRÓCIŁ

Nowe zmiany w interfejsie użytkownika są nachodzące do projektu **dodawania z obszaru galerii** w bloku **Dodawanie aplikacji** . Te zmiany ułatwią łatwiejsze znajdowanie aplikacji obsługujących automatyczne Inicjowanie obsługi, OpenID Connect Connect, SAML (SAML) i logowanie jednokrotne (SSO).

---

### <a name="plan-for-change-removal-of-the-mfa-server-ip-address-from-the-office-365-ip-address"></a>Zaplanuj zmianę: Usuwanie adresu IP serwera usługi MFA z adresu IP pakietu Office 365

**Typ:** Planowanie zmiany  
**Kategoria usługi:** Funkcja  
**Możliwość produktu:** Ochrona tożsamości & zabezpieczenia

Usuwamy adres IP serwera usługi MFA z [usługi sieci Web adresu IP pakietu Office 365 i adresu URL](https://docs.microsoft.com/office365/enterprise/office-365-ip-web-service). Jeśli obecnie korzystasz z tych stron do aktualizowania ustawień zapory, musisz się upewnić, że zawarto również listę adresów IP udokumentowaną w sekcji **wymagania dotyczące zapory serwer Multi-Factor Authentication platformy Azure** . [ z artykułem Serwer Multi-Factor Authentication platformy Azure](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfaserver-deploy#azure-multi-factor-authentication-server-firewall-requirements) .

---

### <a name="app-only-tokens-now-require-the-client-app-to-exist-in-the-resource-tenant"></a>Tokeny tylko dla aplikacji wymagają teraz, aby aplikacja kliencka istniała w dzierżawie zasobów

**Typ:** FIXED  
**Kategoria usługi:** Uwierzytelnienia (nazwy logowania)  
**Możliwość produktu:** Uwierzytelnianie użytkownika

26 lipca 2019 zmieniono sposób dostarczania tokenów tylko aplikacji za pomocą [uprawnień do przydzielenia poświadczeń klienta](https://docs.microsoft.com/azure/active-directory/develop/v1-oauth2-client-creds-grant-flow). Wcześniej aplikacje mogły uzyskać tokeny do wywoływania innych aplikacji, niezależnie od tego, czy aplikacja kliencka była w dzierżawie. Zaktualizowaliśmy to zachowanie, więc zasoby o pojedynczej dzierżawie nazywane czasami interfejsami API sieci Web mogą być wywoływane tylko przez aplikacje klienckie, które znajdują się w dzierżawie zasobów.

Jeśli aplikacja nie znajduje się w dzierżawie zasobów, zostanie wyświetlony komunikat o błędzie z informacją o tym, `The service principal named <app_name> was not found in the tenant named <tenant_name>. This can happen if the application has not been installed by the administrator of the tenant.` aby rozwiązać ten problem, należy utworzyć nazwę główną usługi App Service w dzierżawie przy użyciu [punktu końcowego zgody użytkownika](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent#using-the-admin-consent-endpoint) lub [za pomocą programu PowerShell](https://docs.microsoft.com/azure/active-directory/develop/howto-authenticate-service-principal-powershell). dzięki czemu dzierżawca otrzymuje uprawnienia aplikacji do działania w ramach dzierżawy.

Aby uzyskać więcej informacji, zobacz [co nowego w uwierzytelnianiu?](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#app-only-tokens-for-single-tenant-applications-are-only-issued-if-the-client-app-exists-in-the-resource-tenant).

> [!NOTE]
> Istniejąca zgoda między klientem i interfejsem API nadal nie jest wymagana. Aplikacje powinny nadal przeprowadzać własne testy autoryzacji.

---

### <a name="new-passwordless-sign-in-to-azure-ad-using-fido2-security-keys"></a>Nowe logowanie za pomocą hasła do usługi Azure AD przy użyciu kluczy zabezpieczeń FIDO2

**Typ:** Nowa funkcja  
**Kategoria usługi:** Uwierzytelnienia (nazwy logowania)  
**Możliwość produktu:** Uwierzytelnianie użytkownika

Klienci usługi Azure AD mogą teraz ustawiać zasady zarządzania kluczami zabezpieczeń FIDO2 dla użytkowników i grup organizacji. Użytkownicy końcowi mogą również rejestrować swoje klucze zabezpieczeń, używając kluczy do logowania się do swoich kont Microsoft w witrynach sieci Web na urządzeniach z obsługą FIDO, a także logowania do urządzeń z systemem Windows 10 przyłączonych do usługi Azure AD.

Aby uzyskać więcej informacji, zobacz [Włączanie logowania bezhasła w usłudze Azure AD (wersja zapoznawcza)](/azure/active-directory/authentication/concept-authentication-passwordless) w celu uzyskania informacji związanych z administratorem i [Konfigurowanie informacji zabezpieczających do korzystania z klucza zabezpieczeń (wersja zapoznawcza)](https://docs.microsoft.com/azure/active-directory/user-help/security-info-setup-security-key) dla informacji związanych z użytkownikiem końcowym.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---july-2019"></a>Nowe aplikacje federacyjne dostępne w galerii aplikacja usługi Azure AD — lipiec 2019

**Typ:** Nowa funkcja  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwości produktu:** integracja innej firmy

W lipcu 2019 dodaliśmy te 18 nowych aplikacji z obsługą Federacji do galerii aplikacji:

[Ungerboeck Software](https://docs.microsoft.com/azure/active-directory/saas-apps/ungerboeck-software-tutorial), [jasnozielony wzorzec omnichannel Center contacting](https://docs.microsoft.com/azure/active-directory/saas-apps/bright-pattern-omnichannel-contact-center-tutorial), [sprytne Nelly](https://docs.microsoft.com/azure/active-directory/saas-apps/clever-nelly-tutorial), [AcquireIO](https://docs.microsoft.com/azure/active-directory/saas-apps/acquireio-tutorial), [Looop](https://www.looop.co/schedule-a-demo/), [productboard](https://docs.microsoft.com/azure/active-directory/saas-apps/productboard-tutorial), [MS Azure SSO Access for Ethidex zgodność z pakietem Office™](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on#password-based-sso), [pogłoskami narosłymi](https://docs.microsoft.com/azure/active-directory/saas-apps/hype-tutorial), [ Abstract](https://docs.microsoft.com/azure/active-directory/saas-apps/abstract-tutorial), [wzniesienie](https://docs.microsoft.com/azure/active-directory/saas-apps/ascentis-tutorial), [Flipsnack](https://www.flipsnack.com/accounts/sign-in-sso.html), [Wandera](https://docs.microsoft.com/azure/active-directory/saas-apps/wandera-tutorial), [TwineSocial](https://twinesocial.com/), [Kallidus](https://docs.microsoft.com/azure/active-directory/saas-apps/kallidus-tutorial), [HyperAnna](https://docs.microsoft.com/azure/active-directory/saas-apps/hyperanna-tutorial), [PharmID WasteWitness](https://www.pharmid.com/), [i2B Connect](https://www.i2b-online.com/sign-up-to-use-i2b-connect-here-sso-access/), [JFrog Artifactory](https://docs.microsoft.com/azure/active-directory/saas-apps/jfrog-artifactory-tutorial)

Aby uzyskać więcej informacji na temat aplikacji, zobacz [SaaS Application Integration with Azure Active Directory](https://aka.ms/appstutorial). Aby uzyskać więcej informacji na temat tworzenia listy aplikacji w galerii aplikacji usługi Azure AD, zobacz [Wyświetlanie listy aplikacji w galerii aplikacji Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>Automatyzowanie aprowizacji kont użytkowników dla tych nowo obsługiwanych aplikacji SaaS

**Typ:** Nowa funkcja  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwość produktu:** Monitorowanie & raportowania

Teraz można zautomatyzować tworzenie, aktualizowanie i usuwanie kont użytkowników dla nowo zintegrowanych aplikacji:

- [Dialpad](https://docs.microsoft.com/azure/active-directory/saas-apps/dialpad-provisioning-tutorial)

- [Katalog federacyjny](https://docs.microsoft.com/azure/active-directory/saas-apps/federated-directory-provisioning-tutorial)

- [Figma](https://docs.microsoft.com/azure/active-directory/saas-apps/figma-provisioning-tutorial)

- [Leapsome](https://docs.microsoft.com/azure/active-directory/saas-apps/leapsome-provisioning-tutorial)

- [Peakon](https://docs.microsoft.com/azure/active-directory/saas-apps/peakon-provisioning-tutorial)

- [Smartsheet](https://docs.microsoft.com/azure/active-directory/saas-apps/smartsheet-provisioning-tutorial)

Aby uzyskać więcej informacji o tym, jak lepiej zabezpieczyć organizację przy użyciu funkcji automatycznego inicjowania obsługi kont użytkowników, zobacz [Automatyzowanie aprowizacji użytkowników do aplikacji SaaS przy użyciu usługi Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)

---

### <a name="new-azure-ad-domain-services-service-tag-for-network-security-group"></a>Nowy tag usługi Azure AD Domain Services dla sieciowej grupy zabezpieczeń

**Typ:** Nowa funkcja  
**Kategoria usługi:** Azure AD Domain Services  
**Możliwość produktu:** Azure AD Domain Services

Jeśli masz już możliwość zarządzania długimi listami adresów IP i zakresów, możesz użyć nowego tagu usługi sieciowej **AzureActiveDirectoryDomainServices** w grupie zabezpieczeń sieci platformy Azure, aby zabezpieczyć ruch przychodzący do Azure AD Domain Services wirtualnej podsieć sieciowa.

Aby uzyskać więcej informacji na temat tego nowego tagu usługi, zobacz [sieciowe grupy zabezpieczeń dla Azure AD Domain Services](../../active-directory-domain-services/network-considerations.md#network-security-groups-and-required-ports).

---

### <a name="new-security-audits-for-azure-ad-domain-services-public-preview"></a>Nowe inspekcje zabezpieczeń dla Azure AD Domain Services (publiczna wersja zapoznawcza)

**Typ:** Nowa funkcja  
**Kategoria usługi:** Azure AD Domain Services  
**Możliwość produktu:** Azure AD Domain Services

Z przyjemnością ogłaszamy wydanie inspekcji zabezpieczeń usługi Azure AD Domain Service do publicznej wersji zapoznawczej. Inspekcja zabezpieczeń ułatwia zapewnienie krytycznego wglądu w usługi uwierzytelniania przez przesyłanie strumieniowe zdarzeń inspekcji zabezpieczeń do zasobów kierowanych, takich jak Azure Storage, obszary robocze platformy Azure Log Analytics i centrum zdarzeń Azure, przy użyciu usługi Azure AD Domain Service Portal.

Aby uzyskać więcej informacji, zobacz [Włączanie inspekcji zabezpieczeń dla Azure AD Domain Services (wersja zapoznawcza)](https://docs.microsoft.com/azure/active-directory-domain-services/security-audit-events).

---

### <a name="new-authentication-methods-usage--insights-public-preview"></a>Nowe metody uwierzytelniania użycie & Insights (publiczna wersja zapoznawcza)

**Typ:** Nowa funkcja  
**Kategoria usługi:** Samoobsługowe resetowanie hasła  
**Możliwość produktu:** Monitorowanie & raportowania

Nowe metody uwierzytelniania użycie & raporty usługi Insights mogą pomóc zrozumieć, jak funkcje, takie jak Azure Multi-Factor Authentication i Samoobsługowe resetowanie haseł, są rejestrowane i używane w organizacji, w tym liczbę zarejestrowanych Użytkownicy każdej funkcji, jak często funkcja samoobsługowego resetowania hasła jest używana do resetowania haseł i metody resetowania.

Aby uzyskać więcej informacji, zobacz [metody uwierzytelniania użycie & Insights (wersja zapoznawcza)](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-methods-usage-insights).

---

### <a name="new-security-reports-are-available-for-all-azure-ad-administrators-public-preview"></a>Nowe raporty zabezpieczeń są dostępne dla wszystkich administratorów usługi Azure AD (publiczna wersja zapoznawcza)

**Typ:** Nowa funkcja  
**Kategoria usługi:** Ochrona tożsamości  
**Możliwość produktu:** Ochrona tożsamości & zabezpieczenia

Wszyscy Administratorzy usługi Azure AD mogą teraz wybrać baner u góry istniejących raportów zabezpieczeń, na przykład raport dotyczący **użytkowników oflagowanych w związku z ryzykiem** , aby zacząć korzystać z nowego środowiska zabezpieczeń, jak pokazano w raportach **ryzykownych użytkowników** i **ryzykowne logowania** . Wraz z upływem czasu wszystkie raporty zabezpieczeń będą przenoszone ze starszych wersji do nowych wersji, a nowe raporty udostępniają następujące dodatkowe możliwości:

- Zaawansowane filtrowanie i sortowanie

- Akcje zbiorcze, takie jak odrzucanie ryzyka użytkownika

- Potwierdzenie ochrony jednostek naruszonych lub bezpiecznych

- Stan ryzyka, obejmujący: zagrożone, odrzucone, korygowane i potwierdzone naruszone

Aby uzyskać więcej informacji, zobacz Raport [dotyczący ryzykownych użytkowników](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-investigate-risk#risky-users-report) i [raport dotyczący ryzykownych](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-investigate-risk#risky-sign-ins-report)logowań.

---

### <a name="new-security-audits-for-azure-ad-domain-services-public-preview"></a>Nowe inspekcje zabezpieczeń dla Azure AD Domain Services (publiczna wersja zapoznawcza)

**Typ:** Nowa funkcja  
**Kategoria usługi:** Azure AD Domain Services  
**Możliwość produktu:** Azure AD Domain Services

Z przyjemnością ogłaszamy wydanie inspekcji zabezpieczeń usługi Azure AD Domain Service do publicznej wersji zapoznawczej. Inspekcja zabezpieczeń ułatwia zapewnienie krytycznego wglądu w usługi uwierzytelniania przez przesyłanie strumieniowe zdarzeń inspekcji zabezpieczeń do zasobów kierowanych, takich jak Azure Storage, obszary robocze platformy Azure Log Analytics i centrum zdarzeń Azure, przy użyciu usługi Azure AD Domain Service Portal.

Aby uzyskać więcej informacji, zobacz [Włączanie inspekcji zabezpieczeń dla Azure AD Domain Services (wersja zapoznawcza)](https://docs.microsoft.com/azure/active-directory-domain-services/security-audit-events).

---

### <a name="new-b2b-direct-federation-using-samlws-fed-public-preview"></a>Nowa Federacja usługi B2B Direct z użyciem protokołu SAML/WS-karmionego (publiczna wersja zapoznawcza)

**Typ:** Nowa funkcja  
**Kategoria usługi:** B2B  
**Możliwość produktu:** B2B/B2C

Federacja bezpośrednia pomaga uprościć pracę z partnerami, których rozwiązanie do zarządzania tożsamościami zarządzanymi przez dział IT nie jest usługą Azure AD, pracując z systemami tożsamości, które obsługują standardy SAML lub WS-workd. Po skonfigurowaniu bezpośredniej relacji Federacji z partnerem każdy nowy użytkownik-Gość, który Cię zaprosił z tej domeny, może współpracować z użytkownikiem przy użyciu istniejącego konta organizacji, dzięki czemu środowisko użytkownika dla Gości jest bardziej bezproblemowe.

Aby uzyskać więcej informacji, zobacz [bezpośrednia Federacja z AD FSami i dostawcami innych firm dla użytkowników-Gości (wersja zapoznawcza)](https://docs.microsoft.com/azure/active-directory/b2b/direct-federation).

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>Automatyzowanie aprowizacji kont użytkowników dla tych nowo obsługiwanych aplikacji SaaS

**Typ:** Nowa funkcja  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwość produktu:** Monitorowanie & raportowania

Teraz można zautomatyzować tworzenie, aktualizowanie i usuwanie kont użytkowników dla nowo zintegrowanych aplikacji:

- [Dialpad](https://docs.microsoft.com/azure/active-directory/saas-apps/dialpad-provisioning-tutorial)

- [Katalog federacyjny](https://docs.microsoft.com/azure/active-directory/saas-apps/federated-directory-provisioning-tutorial)

- [Figma](https://docs.microsoft.com/azure/active-directory/saas-apps/figma-provisioning-tutorial)

- [Leapsome](https://docs.microsoft.com/azure/active-directory/saas-apps/leapsome-provisioning-tutorial)

- [Peakon](https://docs.microsoft.com/azure/active-directory/saas-apps/peakon-provisioning-tutorial)

- [Smartsheet](https://docs.microsoft.com/azure/active-directory/saas-apps/smartsheet-provisioning-tutorial)

Aby uzyskać więcej informacji o tym, jak lepiej zabezpieczyć organizację przy użyciu funkcji automatycznego inicjowania obsługi kont użytkowników, zobacz [Automatyzacja aprowizacji użytkowników w aplikacjach SaaS za pomocą usługi Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).

---

### <a name="new-check-for-duplicate-group-names-in-the-azure-ad-portal"></a>Nowe sprawdzenie dla zduplikowanych nazw grup w portalu usługi Azure AD

**Typ:** Nowa funkcja  
**Kategoria usługi:** Zarządzanie grupami  
**Możliwość produktu:** Społeczności

Teraz po utworzeniu lub zaktualizowaniu nazwy grupy w portalu usługi Azure AD sprawdzimy, czy duplikat istniejącej nazwy grupy jest duplikowany w zasobie. Jeśli okaże się, że nazwa jest już używana przez inną grupę, zostanie wyświetlony monit o zmianę nazwy.

Aby uzyskać więcej informacji, zobacz [Zarządzanie grupami w portalu usługi Azure AD](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal?context=azure/active-directory/users-groups-roles/context/ugr-context).

---

### <a name="azure-ad-now-supports-static-query-parameters-in-reply-redirect-uris"></a>Usługa Azure AD obsługuje teraz statyczne parametry zapytań w identyfikatorach URI odpowiedzi (redirect)

**Typ:** Nowa funkcja  
**Kategoria usługi:** Uwierzytelnienia (nazwy logowania)  
**Możliwość produktu:** Uwierzytelnianie użytkownika

Aplikacje usługi Azure AD mogą teraz rejestrować i używać identyfikatorów URI odpowiedzi (redirect) przy użyciu statycznych parametrów zapytania (na przykład `https://contoso.com/oauth2?idp=microsoft`) dla żądań OAuth 2,0. Statyczny parametr zapytania jest uzależniony od ciągu dla identyfikatorów URI odpowiedzi, podobnie jak każda inna część identyfikatora URI odpowiedzi. Jeśli nie ma żadnego zarejestrowanego ciągu zgodnego z zdekodowanym adresem URL, żądanie zostanie odrzucone. Jeśli zostanie znaleziony identyfikator URI odpowiedzi, cały ciąg jest używany do przekierowania użytkownika, łącznie z parametrem zapytania statycznego.

Dynamiczne identyfikatory URI odpowiedzi są nadal zabronione, ponieważ stanowią zagrożenie bezpieczeństwa i nie mogą być używane do przechowywania informacji o stanie w ramach żądania uwierzytelnienia. W tym celu należy użyć parametru `state`.

Obecnie ekrany rejestracji aplikacji Azure Portal nadal blokują parametry zapytania. Można jednak ręcznie edytować manifest aplikacji, aby dodawać i testować parametry zapytania w aplikacji. Aby uzyskać więcej informacji, zobacz [co nowego w uwierzytelnianiu?](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#redirect-uris-can-now-contain-query-string-parameters).

---

### <a name="activity-logs-ms-graph-apis-for-azure-ad-are-now-available-through-powershell-cmdlets"></a>Dzienniki aktywności (interfejsy API MS Graph) dla usługi Azure AD są teraz dostępne za pomocą poleceń cmdlet programu PowerShell

**Typ:** Nowa funkcja  
**Kategoria usługi:** Reporting  
**Możliwość produktu:** Monitorowanie & raportowania

Przyjemnością ogłaszamy, że dzienniki aktywności usługi Azure AD (raporty inspekcji i logowania) są teraz dostępne za pomocą modułu Azure AD PowerShell. Wcześniej można było tworzyć własne skrypty za pomocą punktów końcowych MS interfejs API programu Graph i teraz rozszerzamy tę możliwość na polecenia cmdlet programu PowerShell.

Aby uzyskać więcej informacji na temat sposobu korzystania z tych poleceń cmdlet, zobacz [polecenia cmdlet programu PowerShell usługi Azure AD na potrzeby raportowania](https://docs.microsoft.com/azure/active-directory/reports-monitoring/reference-powershell-reporting).

---

### <a name="updated-filter-controls-for-audit-and-sign-in-logs-in-azure-ad"></a>Zaktualizowano kontrolki filtru dla dzienników inspekcji i logowania w usłudze Azure AD

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Reporting  
**Możliwość produktu:** Monitorowanie & raportowania

Zaktualizowaliśmy raporty inspekcji i logowania, dzięki czemu można teraz zastosować różne filtry bez konieczności dodawania ich jako kolumn na ekranach raportów. Ponadto możesz wybrać liczbę filtrów, które mają być wyświetlane na ekranie. Wszystkie te aktualizacje współpracują ze sobą, aby ułatwić ich odczytywanie i zwiększanie zakresu do Twoich potrzeb.

Aby uzyskać więcej informacji o tych aktualizacjach, zobacz [filtrowanie dzienników inspekcji](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-audit-logs#filtering-audit-logs) i [filtrowanie działań związanych z logowaniem](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-sign-ins#filter-sign-in-activities).

---

## <a name="june-2019"></a>Czerwiec 2019 r.

### <a name="new-riskdetections-api-for-microsoft-graph-public-preview"></a>Nowy interfejs API riskDetections dla Microsoft Graph (publiczna wersja zapoznawcza)

**Typ:** Nowa funkcja  
**Kategoria usługi:** Ochrona tożsamości  
**Możliwość produktu:** Ochrona tożsamości & zabezpieczenia

Z przyjemnością ogłaszamy nowy interfejs API riskDetections dla Microsoft Graph jest teraz dostępny w publicznej wersji zapoznawczej. Ten nowy interfejs API umożliwia wyświetlenie listy wykrytych użytkowników związanych z ochroną tożsamości w organizacji oraz ich wykrycia. Możesz również użyć tego interfejsu API, aby efektywniej wykonywać zapytania dotyczące wykrywania ryzyka, w tym szczegółowe informacje o typie wykrywania, stanie, poziomie i innych.

Aby uzyskać więcej informacji, zobacz [dokumentację dotyczącą interfejsu API wykrywania zagrożeń](https://docs.microsoft.com/graph/api/resources/riskdetection?view=graph-rest-beta).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---june-2019"></a>Nowe aplikacje federacyjne dostępne w galerii aplikacji usługi Azure AD — czerwiec 2019

**Typ:** Nowa funkcja  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwości produktu:** integracja innej firmy

W czerwcu 2019 dodaliśmy te 22 nowe aplikacje z obsługą Federacji do galerii aplikacji:

[Azure AD SAML Toolkit](https://docs.microsoft.com/azure/active-directory/saas-apps/saml-toolkit-tutorial), [Otsuka Shokai (大塚商会)](https://docs.microsoft.com/azure/active-directory/saas-apps/otsuka-shokai-tutorial), [ANAQUA](https://docs.microsoft.com/azure/active-directory/saas-apps/anaqua-tutorial), [Klient sieci VPN platformy Azure](https://portal.azure.com/), [wydatki](https://docs.microsoft.com/azure/active-directory/saas-apps/expensein-tutorial), [pomocnik pomocnika](https://docs.microsoft.com/azure/active-directory/saas-apps/helper-helper-tutorial), [Costpoint](https://docs.microsoft.com/azure/active-directory/saas-apps/costpoint-tutorial), [GlobalOne](https://docs.microsoft.com/azure/active-directory/saas-apps/globalone-tutorial), [Mercedes-Benz w biurze](https://me.secure.mercedes-benz.com/), [skore](https://app.justskore.it/), [Oracle Cloud Infrastructure Console](https://docs.microsoft.com/azure/active-directory/saas-apps/oracle-cloud-tutorial), [CyberArk Authentication SAML](https://docs.microsoft.com/azure/active-directory/saas-apps/cyberark-saml-authentication-tutorial), [Scrible edu](https://www.scrible.com/sign-in/#/create-account), [PandaDoc](https://docs.microsoft.com/azure/active-directory/saas-apps/pandadoc-tutorial), [Perceptyx](https://apexdata.azurewebsites.net/docs.microsoft.com/azure/active-directory/saas-apps/perceptyx-tutorial), [Proptimise OS](https://proptimise.co.uk/software/), [vtiger CRM (SAML)](https://docs.microsoft.com/azure/active-directory/saas-apps/vtiger-crm-saml-tutorial), Oracle Access Manager for Oracle Handel detaliczny, program Oracle Access Manager dla Oracle E-Business Suite, Oracle IDCS for E-Business Suite, Oracle IDCS for PeopleSoft, Oracle IDCS for JD Edwards

Aby uzyskać więcej informacji na temat aplikacji, zobacz [SaaS Application Integration with Azure Active Directory](https://aka.ms/appstutorial). Aby uzyskać więcej informacji na temat tworzenia listy aplikacji w galerii aplikacji usługi Azure AD, zobacz [Wyświetlanie listy aplikacji w galerii aplikacji Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>Automatyzowanie aprowizacji kont użytkowników dla tych nowo obsługiwanych aplikacji SaaS

**Typ:** Nowa funkcja  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwość produktu:** Monitorowanie & raportowania

Teraz można zautomatyzować tworzenie, aktualizowanie i usuwanie kont użytkowników dla nowo zintegrowanych aplikacji:

- [Zmieniać](https://docs.microsoft.com/azure/active-directory/saas-apps/zoom-provisioning-tutorial)

- [Wysłannika](https://docs.microsoft.com/azure/active-directory/saas-apps/envoy-provisioning-tutorial)

- [Proxyclick](https://docs.microsoft.com/azure/active-directory/saas-apps/proxyclick-provisioning-tutorial)

- [4me](https://docs.microsoft.com/azure/active-directory/saas-apps/4me-provisioning-tutorial)

Aby uzyskać więcej informacji o tym, jak lepiej zabezpieczyć organizację przy użyciu funkcji automatycznego inicjowania obsługi kont użytkowników, zobacz [Automatyzowanie aprowizacji użytkowników do aplikacji SaaS przy użyciu usługi Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)

---

### <a name="view-the-real-time-progress-of-the-azure-ad-provisioning-service"></a>Wyświetlanie postępu w czasie rzeczywistym usługi Azure AD Provisioning

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Inicjowanie obsługi aplikacji  
**Możliwość produktu:** Zarządzanie cyklem życia tożsamości

Zaktualizowaliśmy środowisko aprowizacji usługi Azure AD, aby uwzględnić nowy pasek postępu, który pokazuje, jak daleko jesteś w procesie aprowizacji użytkowników. To zaktualizowane środowisko zawiera również informacje o liczbie użytkowników, których zainicjowano w bieżącym cyklu, a także o tym, jak wiele użytkowników zostało zainicjowanych do tej pory.

Aby uzyskać więcej informacji, zobacz [Sprawdzanie stanu aprowizacji użytkowników](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user).

---

### <a name="company-branding-now-appears-on-sign-out-and-error-screens"></a>Znakowanie firmowe pojawia się teraz na ekranach Wyloguj się i błędy

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Uwierzytelnienia (nazwy logowania)  
**Możliwość produktu:** Uwierzytelnianie użytkownika

Zaktualizowaliśmy usługę Azure AD, dzięki czemu logo firmy pojawia się teraz na ekranach Wyloguj i błędy, a także na stronie logowania. Nie musisz nic robić, aby włączyć tę funkcję, usługa Azure AD po prostu korzysta z zasobów, które zostały już skonfigurowane w obszarze **znakowania firmowego** Azure Portal.

Aby uzyskać więcej informacji na temat konfigurowania znakowania firmowego, zobacz [Dodawanie znakowania do stron Azure Active Directory organizacji](https://docs.microsoft.com/azure/active-directory/fundamentals/customize-branding).

---

### <a name="azure-multi-factor-authentication-mfa-server-is-no-longer-available-for-new-deployments"></a>Serwer usługi Azure Multi-Factor Authentication (MFA) nie jest już dostępny dla nowych wdrożeń

**Typ:** Przestarzałe  
**Kategoria usługi:** Funkcja  
**Możliwość produktu:** Ochrona tożsamości & zabezpieczenia

Od 1 lipca 2019 firma Microsoft nie będzie już oferować serwera MFA dla nowych wdrożeń. Nowi klienci, którzy chcą wymagać uwierzytelniania wieloskładnikowego w organizacji, muszą teraz korzystać z usługi Azure Multi-Factor Authentication opartej na chmurze. Klienci, którzy aktywowali serwer usługi MFA przed 1 lipca, nie zobaczą zmiany. Nadal będzie można pobrać najnowszą wersję, pobrać przyszłe aktualizacje i wygenerować poświadczenia aktywacji.

Aby uzyskać więcej informacji, zobacz [Rozpoczynanie pracy z usługą Azure serwer Multi-Factor Authentication](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfaserver-deploy). Aby uzyskać więcej informacji na temat usługi Azure Multi-Factor Authentication opartej na chmurze, zobacz [Planowanie wdrożenia usługi azure Multi-Factor Authentication opartego na chmurze](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted).

---

## <a name="may-2019"></a>Maj 2019 r.

### <a name="service-change-future-support-for-only-tls-12-protocols-on-the-application-proxy-service"></a>Zmiana usługi: w przyszłości obsługuje tylko protokoły TLS 1,2 w usłudze serwera proxy aplikacji.

**Typ:** Planowanie zmiany  
**Kategoria usługi:** Serwer proxy aplikacji  
**Możliwość produktu:** Access Control

Aby pomóc w zapewnieniu najlepszej klasy szyfrowania dla naszych klientów, ograniczamy dostęp do protokołów TLS 1,2 w usłudze serwera proxy aplikacji. Ta zmiana jest stopniowo wprowadzana do klientów, którzy już korzystają z protokołów TLS 1,2, dlatego nie będzie można zobaczyć żadnych zmian.

Wycofanie protokołu TLS 1,0 i TLS 1,1 występuje 31 sierpnia 2019, ale postanowimy dodatkowe zaawansowane powiadomienie, dzięki czemu będziesz mieć czas na przygotowanie się do tej zmiany. Aby przygotować się do tej zmiany, upewnij się, że kombinacja klient-serwer i przeglądarka-serwer, łącznie z klientami używanymi przez użytkowników do uzyskiwania dostępu do aplikacji publikowanych za pomocą serwera proxy aplikacji, zostały zaktualizowane do korzystania z protokołu TLS 1,2 do obsługi połączenia z aplikacją Usługa serwera proxy. Aby uzyskać więcej informacji, zobacz [Dodawanie aplikacji lokalnej dla dostępu zdalnego przy użyciu serwera proxy aplikacji w Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application#before-you-begin).

---

### <a name="use-the-usage-and-insights-report-to-view-your-app-related-sign-in-data"></a>Użyj raportu Użycie i szczegółowe informacje, aby wyświetlić dane logowania związane z aplikacją

**Typ:** Nowa funkcja  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwość produktu:** Monitorowanie & raportowania

Możesz teraz użyć raportu Użycie i szczegółowe informacje znajdującego się w obszarze aplikacje dla **przedsiębiorstw** Azure Portal, aby uzyskać widok skoncentrowany na aplikacji, w tym informacje o:

- Najpopularniejsze używane aplikacje dla Twojej organizacji

- Aplikacje z największą liczbą nieudanych logowań

- Najważniejsze błędy logowania dla każdej aplikacji

Aby uzyskać więcej informacji na temat tej funkcji, zobacz [raport dotyczący użycia i usługi Insights w portalu Azure Active Directory](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-usage-insights-report)

---

### <a name="automate-your-user-provisioning-to-cloud-apps-using-azure-ad"></a>Automatyzowanie aprowizacji użytkowników w aplikacjach w chmurze przy użyciu usługi Azure AD

**Typ:** Nowa funkcja  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwość produktu:** Monitorowanie & raportowania

Postępuj zgodnie z tymi nowymi samouczkami, aby użyć usługi Azure AD Provisioning w celu zautomatyzowania tworzenia, usuwania i aktualizowania kont użytkowników dla następujących aplikacji opartych na chmurze:

- [Współspotykanie](https://docs.microsoft.com/azure/active-directory/saas-apps/comeet-recruiting-software-provisioning-tutorial)

- [DynamicSignal](https://docs.microsoft.com/azure/active-directory/saas-apps/dynamic-signal-provisioning-tutorial)

- [KeeperSecurity](https://docs.microsoft.com/azure/active-directory/saas-apps/keeper-password-manager-digitalvault-provisioning-tutorial)

Możesz również wykonać czynności opisane w [samouczku](https://docs.microsoft.com/azure/active-directory/saas-apps/dropboxforbusiness-provisioning-tutorial)dotyczącym usługi Dropbox, który zawiera informacje o sposobach aprowizacji obiektów grup.

Aby uzyskać więcej informacji o tym, jak lepiej zabezpieczyć organizację przez automatyczne Inicjowanie obsługi kont użytkowników, zobacz [Automatyzowanie aprowizacji użytkowników w aplikacjach SaaS za pomocą usługi Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).

---

### <a name="identity-secure-score-is-now-available-in-azure-ad-general-availability"></a>Ocena bezpiecznego tożsamości jest teraz dostępna w usłudze Azure AD (ogólna dostępność)

**Typ:** Nowa funkcja  
**Kategoria usługi:** NIE DOTYCZY  
**Możliwość produktu:** Ochrona tożsamości & zabezpieczenia

Teraz można monitorować i ulepszać stan zabezpieczeń tożsamości za pomocą funkcji "Funkcja bezpiecznego oceny" w usłudze Azure AD. Funkcja oceny zabezpieczeń tożsamości używa jednego pulpitu nawigacyjnego, aby ułatwić:

- Obiektywnie Zmierz stan zabezpieczeń tożsamości na podstawie wyniku z zakresu od 1 do 223.

- Planowanie ulepszeń w zakresie zabezpieczeń tożsamości

- Zapoznaj się z sukcesem ulepszeń zabezpieczeń

Aby uzyskać więcej informacji na temat funkcji oceny zabezpieczeń tożsamości, zobacz artykuł [co to jest bezpieczna ocena tożsamości w Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/fundamentals/identity-secure-score).

---

### <a name="new-app-registrations-experience-is-now-available-general-availability"></a>Nowe środowisko Rejestracje aplikacji jest teraz dostępne (ogólna dostępność)

**Typ:** Nowa funkcja  
**Kategoria usługi:** Uwierzytelnienia (nazwy logowania)  
**Możliwość produktu:** Środowisko deweloperskie

Nowe środowisko [rejestracje aplikacji](https://aka.ms/appregistrations) jest teraz ogólnie dostępne. To nowe środowisko obejmuje wszystkie kluczowe funkcje, które znasz z Azure Portal i portalu rejestracji aplikacji, i ulepszają je w następujący sposób:

- **Lepsza Zarządzanie aplikacjami.** Zamiast oglądać aplikacje w różnych portalach, możesz teraz zobaczyć wszystkie Twoje aplikacje w jednej lokalizacji.

- **Uproszczona rejestracja aplikacji.** W ulepszonym środowisku nawigacyjnym funkcji wyboru uprawnień odnowionych teraz łatwiej jest zarejestrować aplikacje i zarządzać nimi.

- **Bardziej szczegółowe informacje.** Możesz znaleźć więcej szczegółowych informacji o aplikacji, w tym przewodnikach Szybki Start i nie tylko.

Aby uzyskać więcej informacji, zobacz [Microsoft Identity platform](https://docs.microsoft.com/azure/active-directory/develop/) i [środowisko rejestracje aplikacji są teraz ogólnie dostępne!](https://developer.microsoft.com/identity/blogs/new-app-registrations-experience-is-now-generally-available/) ogłoszenie blogu.

---

### <a name="new-capabilities-available-in-the-risky-users-api-for-identity-protection"></a>Nowe funkcje dostępne w interfejsie API użytkowników ryzykownych usługi Identity Protection

**Typ:** Nowa funkcja  
**Kategoria usługi:** Ochrona tożsamości  
**Możliwość produktu:** Ochrona tożsamości & zabezpieczenia

Z przyjemnością ogłaszamy, że możesz teraz używać interfejsu API ryzykownych użytkowników, aby pobierać historię ryzyka użytkowników, odrzucać ryzykownych użytkowników i potwierdzać bezpieczeństwo użytkowników. Ta zmiana ułatwia efektywniejsze aktualizowanie stanu ryzyka dla użytkowników i zrozumienie ich historii ryzyka.

Aby uzyskać więcej informacji, zobacz [dokumentację dotyczącą interfejsu API ryzykownych użytkowników](https://docs.microsoft.com/graph/api/resources/riskyuser?view=graph-rest-beta).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---may-2019"></a>Nowe aplikacje federacyjne dostępne w galerii aplikacji usługi Azure AD — maj 2019

**Typ:** Nowa funkcja  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwości produktu:** integracja innej firmy

W maju 2019 dodaliśmy następujące 21 nowych aplikacji z obsługą Federacji do galerii aplikacji:

[Freedcamp](https://docs.microsoft.com/azure/active-directory/saas-apps/freedcamp-tutorial), [prawdziwe linki](https://docs.microsoft.com/azure/active-directory/saas-apps/real-links-tutorial), [Kianda](https://app.kianda.com/sso/OpenID/AzureAD/), [Simple Signer](https://docs.microsoft.com/azure/active-directory/saas-apps/simple-sign-tutorial), [Braze](https://docs.microsoft.com/azure/active-directory/saas-apps/braze-tutorial), [Display](https://docs.microsoft.com/azure/active-directory/saas-apps/displayr-tutorial), [TEMPLAFY](https://docs.microsoft.com/azure/active-directory/saas-apps/templafy-tutorial), [Marketo](https://toutapp.com/login), [ACLP](https://docs.microsoft.com/azure/active-directory/saas-apps/aclp-tutorial), [resystems](https://docs.microsoft.com/azure/active-directory/saas-apps/outsystems-tutorial), [META4 Global HR](https://docs.microsoft.com/azure/active-directory/saas-apps/meta4-global-hr-tutorial), [Quantum Miejsce pracy](https://docs.microsoft.com/azure/active-directory/saas-apps/quantum-workplace-tutorial), [kobalt](https://docs.microsoft.com/azure/active-directory/saas-apps/cobalt-tutorial), interfejsy [API usługi webMethods](https://docs.microsoft.com/azure/active-directory/saas-apps/webmethods-integration-cloud-tutorial), [RedFlag](https://pocketstop.com/redflag/), [Whatfix](https://docs.microsoft.com/azure/active-directory/saas-apps/whatfix-tutorial), [Control](https://docs.microsoft.com/azure/active-directory/saas-apps/control-tutorial), [JOBHUB](https://docs.microsoft.com/azure/active-directory/saas-apps/jobhub-tutorial), [NEOGOV](https://docs.microsoft.com/azure/active-directory/saas-apps/neogov-tutorial), [Foodee](https://docs.microsoft.com/azure/active-directory/saas-apps/foodee-tutorial), [MyVR](https://docs.microsoft.com/azure/active-directory/saas-apps/myvr-tutorial)

Aby uzyskać więcej informacji na temat aplikacji, zobacz [SaaS Application Integration with Azure Active Directory](https://aka.ms/appstutorial). Aby uzyskać więcej informacji na temat tworzenia listy aplikacji w galerii aplikacji usługi Azure AD, zobacz [Wyświetlanie listy aplikacji w galerii aplikacji Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="improved-groups-creation-and-management-experiences-in-the-azure-ad-portal"></a>Udoskonalone środowiska tworzenia i zarządzania grupami w portalu usługi Azure AD

**Typ:** Nowa funkcja  
**Kategoria usługi:** Zarządzanie grupami  
**Możliwość produktu:** Społeczności

Wprowadzono ulepszenia dotyczące środowisk związanych z grupami w portalu usługi Azure AD. Te udoskonalenia umożliwiają administratorom lepsze zarządzanie listami grup, listami członków i udostępnianiem dodatkowych opcji tworzenia.

Ulepszenia obejmują:

- Podstawowe filtrowanie według typu członkostwa i typu grupy.

- Dodanie nowych kolumn, takich jak źródło i adres E-mail.

- Możliwość łatwego usuwania grup, elementów członkowskich i list właścicieli.

- Możliwość wybrania adresu e-mail i dodania właścicieli podczas tworzenia grupy.

Aby uzyskać więcej informacji, zobacz [Tworzenie grupy podstawowej i Dodawanie członków przy użyciu Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal).

---

### <a name="configure-a-naming-policy-for-office-365-groups-in-azure-ad-portal-general-availability"></a>Konfigurowanie zasad nazewnictwa dla grup Office 365 w portalu usługi Azure AD (ogólna dostępność)

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Zarządzanie grupami  
**Możliwość produktu:** Społeczności

Administratorzy mogą teraz konfigurować zasady nazewnictwa dla grup pakietu Office 365 przy użyciu portalu usługi Azure AD. Ta zmiana pomaga wymusić spójne konwencje nazewnictwa dla grup pakietu Office 365 utworzonych lub edytowanych przez użytkowników w organizacji.

Zasady nazewnictwa dla grup pakietu Office 365 można skonfigurować na dwa różne sposoby:

- Zdefiniuj prefiksy lub sufiksy, które są automatycznie dodawane do nazwy grupy.

- Przekaż dostosowany zestaw zablokowanych słów dla swojej organizacji, które nie są dozwolone w nazwach grup (na przykład "dyrektor generalny, ListaPłac, HR").

Aby uzyskać więcej informacji, zobacz [wymuszanie zasad nazewnictwa dla grup pakietu Office 365](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-naming-policy).

---

### <a name="microsoft-graph-api-endpoints-are-now-available-for-azure-ad-activity-logs-general-availability"></a>Punkty końcowe interfejsu API Microsoft Graph są teraz dostępne dla dzienników aktywności usługi Azure AD (ogólna dostępność)

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Reporting  
**Możliwość produktu:** Monitorowanie & raportowania

Z przyjemnością ogłaszamy ogólną dostępność Microsoft Graph punktów końcowych interfejsu API dla dzienników aktywności usługi Azure AD. W tej wersji można teraz używać wersji 1,0 zarówno dzienników inspekcji usługi Azure AD, jak i interfejsów API logowania.

Aby uzyskać więcej informacji, zobacz [Omówienie interfejsu API dziennika inspekcji usługi Azure AD](https://docs.microsoft.com/graph/api/resources/azure-ad-auditlog-overview?view=graph-rest-1.0).

---

### <a name="administrators-can-now-use-conditional-access-for-the-combined-registration-process-public-preview"></a>Administratorzy mogą teraz korzystać z dostępu warunkowego dla połączonego procesu rejestracji (publiczna wersja zapoznawcza)

**Typ:** Nowa funkcja  
**Kategoria usługi:** Dostęp warunkowy  
**Możliwość produktu:** Ochrona tożsamości & zabezpieczenia  

Administratorzy mogą teraz tworzyć zasady dostępu warunkowego do użycia przez łączną stronę rejestracji. Obejmuje to zastosowanie zasad umożliwiających rejestrację, jeśli:

- Użytkownicy znajdują się w zaufanej sieci.

- Użytkownicy są niskim ryzykiem związanym z logowaniem.

- Użytkownicy znajdują się na zarządzanym urządzeniu.

- Użytkownicy zgadzają się na warunki użytkowania organizacji (warunków użytkowania).

Aby uzyskać więcej informacji na temat dostępu warunkowego i resetowania hasła, można zobaczyć [dostęp warunkowy do wpisu w blogu dotyczącego połączenia MFA i rejestracji resetowania haseł usługi Azure AD](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Conditional-access-for-the-Azure-AD-combined-MFA-and-password/ba-p/566348). Aby uzyskać więcej informacji na temat zasad dostępu warunkowego dla połączonego procesu rejestracji, zobacz [zasady dostępu warunkowego dla połączonej rejestracji](https://docs.microsoft.com/azure/active-directory/authentication/howto-registration-mfa-sspr-combined#conditional-access-policies-for-combined-registration). Aby uzyskać więcej informacji na temat funkcji warunków użytkowania usługi Azure AD, zobacz [Azure Active Directory warunki użytkowania](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use).

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
