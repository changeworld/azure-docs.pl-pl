---
title: Co nowego w Azure Active Directory? | Microsoft Docs
description: Co to jest nowe informacje o wersji w ramach przeglądu sekcji tego zestawu zawartości zawiera 6 miesięcy działania. Po upływie 6 miesięcy elementy są usuwane z głównego artykułu i umieścić w tym artykule archiwum.
services: active-directory
author: msmimart
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 02/27/2020
ms.author: mimart
ms.reviewer: dhanyahk
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: cd376474da2116837278cd4886465c53a99e5455
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/29/2020
ms.locfileid: "78190623"
---
# <a name="archive-for-whats-new-in-azure-active-directory"></a>Co nowego w Azure Active Directory?

Podstawowe informacje o [nowościach w Azure Active Directory?](whats-new.md) artykuł dotyczący wersji zawiera aktualizacje dla ostatnich sześciu miesięcy, a ten artykuł zawiera wszystkie starsze dane.

Co nowego w Azure Active Directory? Informacje o wersji zawierają:

- Zainstalowane najnowsze wersje
- Znane problemy
- Poprawki błędów
- Przestarzałe funkcje
- Plany dotyczące zmian

---

## <a name="august-2019"></a>Sierpień 2019 r.

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

Aby uzyskać więcej informacji, zobacz [ryzykowni użytkownicy](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-investigate-risk#risky-users), [ryzykowne logowania](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-investigate-risk#risky-sign-ins)i [wykrywanie ryzyka](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-investigate-risk#risk-detections).

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

Począwszy od biblioteki Azure AD Authentication Library (ADAL.NET) w wersji 5.0.0 — wersja zapoznawcza, deweloperzy aplikacji muszą [serializować jedną pamięć podręczną na konto dla aplikacji sieci Web i interfejsów API sieci Web](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Token-cache-serialization#custom-token-cache-serialization-in-web-applications--web-api). W przeciwnym razie niektóre scenariusze korzystające z [przepływu w imieniu](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-api-call-api-app-configuration#on-behalf-of-flow)i konkretne przypadki użycia `UserAssertion`mogą spowodować podniesienie uprawnień. Aby uniknąć tej luki w zabezpieczeniach, ADAL.NET teraz ignoruje bibliotekę uwierzytelniania Microsoft dla udostępnionej pamięci podręcznej usługi dotnet (MSAL.NET) dla scenariuszy w imieniu użytkownika.

Aby uzyskać więcej informacji o tym problemie, zobacz temat [Luka w zabezpieczeniach Azure Active Directory podniesienia uprawnień w bibliotece uwierzytelniania](https://portal.msrc.microsoft.com/security-guidance/advisory/CVE-2019-1258).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---august-2019"></a>Nowe aplikacje federacyjne dostępne w galerii aplikacja usługi Azure AD — sierpień 2019

**Typ:** Nowa funkcja  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwości produktu:** integracja innej firmy

W sierpniu 2019 dodaliśmy te 26 nowych aplikacji z obsługą Federacji do galerii aplikacji:

[Projektowi Civic Innovation platform](https://docs.microsoft.com/azure/active-directory/saas-apps/civic-platform-tutorial), [Amazon Business](https://docs.microsoft.com/azure/active-directory/saas-apps/amazon-business-tutorial), [ProNovos](https://docs.microsoft.com/azure/active-directory/saas-apps/pronovos-ops-manager-tutorial)Operations Manager, [Cognidox](https://docs.microsoft.com/azure/active-directory/saas-apps/cognidox-tutorial), [Viareport (Europa)](https://docs.microsoft.com/azure/active-directory/saas-apps/viareports-inativ-portal-europe-tutorial), [Azure Databricks](https://azure.microsoft.com/services/databricks), [Robin](https://docs.microsoft.com/azure/active-directory/saas-apps/robin-tutorial), w usłudze [Academy](https://docs.microsoft.com/azure/active-directory/saas-apps/academy-attendance-tutorial), [macierze priorytetowe](https://sync.appfluence.com/pmwebng/), [Inativ Cousto](https://cousto.platformers.be/account/login), [MySpace](https://uploadcare.com/accounts/signup/), [Uploadcare Endpoint Backup](https://docs.microsoft.com/azure/active-directory/saas-apps/carbonite-endpoint-backup-tutorial), [Carbonite, CPQSync](https://docs.microsoft.com/azure/active-directory/saas-apps/cpqsync-by-cincom-tutorial), [Cincom](https://docs.microsoft.com/azure/active-directory/saas-apps/chargebee-tutorial), [dostarczanie. Media™ Portal](https://portal.deliver.media), [Chargebee Education](https://docs.microsoft.com/azure/active-directory/saas-apps/frontline-education-tutorial), [F5](https://www.f5.com/products/security/access-policy-manager), [teraźniejszości AD Łączenie](https://www.stashcat.com), [migotanie](https://docs.microsoft.com/azure/active-directory/saas-apps/blink-tutorial), [Vocoli](https://docs.microsoft.com/azure/active-directory/saas-apps/vocoli-tutorial), [ProNovos Analytics](https://docs.microsoft.com/azure/active-directory/saas-apps/pronovos-analytics-tutorial), [Sigstr](https://docs.microsoft.com/azure/active-directory/saas-apps/sigstr-tutorial), [Darwinbox](https://docs.microsoft.com/azure/active-directory/saas-apps/darwinbox-tutorial), [Watch według kolorów](https://docs.microsoft.com/azure/active-directory/saas-apps/watch-by-colors-tutorial), [zespół](https://docs.microsoft.com/azure/active-directory/saas-apps/harness-tutorial), [EAB, opieka strategiczna](https://docs.microsoft.com/azure/active-directory/saas-apps/eab-navigate-strategic-care-tutorial)

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

Aby uzyskać więcej informacji, zobacz [Zarządzanie dynamicznymi regułami członkostwa](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership).

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

Aby uzyskać więcej informacji na temat udoskonalonego środowiska informacji o zabezpieczeniach, zapoznaj się z [dokumentacją administratora](https://aka.ms/securityinfodocs) i [dokumentacją użytkownika](https://aka.ms/securityinfoguide).

#### <a name="to-turn-on-this-new-experience-you-must"></a>Aby włączyć to nowe środowisko, musisz:

1. Zaloguj się do Azure Portal jako Administrator globalny lub administrator użytkowników.

2. Przejdź do pozycji **Azure Active Directory > Ustawienia użytkownika > zarządzanie ustawieniami funkcji Podgląd panelu dostępu**.

3. **Użytkownicy mogą korzystać z funkcji w wersji zapoznawczej na potrzeby rejestrowania obszaru informacje zabezpieczające i zarządzania nim** , wybrać opcję **wybrane**, a następnie wybrać grupę użytkowników lub wybrać opcję **wszystkie** , aby włączyć tę funkcję dla wszystkich użytkowników w dzierżawie.

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

Usuwamy adres IP serwera usługi MFA z [usługi sieci Web adresu IP pakietu Office 365 i adresu URL](https://docs.microsoft.com/office365/enterprise/office-365-ip-web-service). Jeśli obecnie korzystasz z tych stron do aktualizowania ustawień zapory, musisz się upewnić, że zawarto również listę adresów IP udokumentowaną w sekcji **wymagania dotyczące zapory serwer Multi-Factor Authentication systemu Azure** w artykule [wprowadzenie do usługi Azure serwer Multi-Factor Authentication](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfaserver-deploy#azure-multi-factor-authentication-server-firewall-requirements) .

---

### <a name="app-only-tokens-now-require-the-client-app-to-exist-in-the-resource-tenant"></a>Tokeny tylko dla aplikacji wymagają teraz, aby aplikacja kliencka istniała w dzierżawie zasobów

**Typ:** FIXED  
**Kategoria usługi:** Uwierzytelnienia (nazwy logowania)  
**Możliwość produktu:** Uwierzytelnianie użytkownika

26 lipca 2019 zmieniono sposób dostarczania tokenów tylko aplikacji za pomocą [uprawnień do przydzielenia poświadczeń klienta](https://docs.microsoft.com/azure/active-directory/develop/v1-oauth2-client-creds-grant-flow). Wcześniej aplikacje mogły uzyskać tokeny do wywoływania innych aplikacji, niezależnie od tego, czy aplikacja kliencka była w dzierżawie. Zaktualizowaliśmy to zachowanie, więc zasoby o pojedynczej dzierżawie nazywane czasami interfejsami API sieci Web mogą być wywoływane tylko przez aplikacje klienckie, które znajdują się w dzierżawie zasobów.

Jeśli aplikacja nie znajduje się w dzierżawie zasobów, zostanie wyświetlony komunikat o błędzie z informacją o tym, `The service principal named <app_name> was not found in the tenant named <tenant_name>. This can happen if the application has not been installed by the administrator of the tenant.` aby rozwiązać ten problem, należy utworzyć nazwę główną usługi App Service w dzierżawie przy użyciu [punktu końcowego zgody użytkownika](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent#using-the-admin-consent-endpoint) lub [za pośrednictwem programu PowerShell](https://docs.microsoft.com/azure/active-directory/develop/howto-authenticate-service-principal-powershell), co gwarantuje, że dzierżawca uzyska uprawnienia aplikacji do działania w ramach dzierżawy.

Aby uzyskać więcej informacji, zobacz [co nowego w uwierzytelnianiu?](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#app-only-tokens-for-single-tenant-applications-are-only-issued-if-the-client-app-exists-in-the-resource-tenant).

> [!NOTE]
> Istniejąca zgoda między klientem i interfejsem API nadal nie jest wymagana. Aplikacje powinny nadal przeprowadzać własne testy autoryzacji.

---

### <a name="new-passwordless-sign-in-to-azure-ad-using-fido2-security-keys"></a>Nowe logowanie za pomocą hasła do usługi Azure AD przy użyciu kluczy zabezpieczeń FIDO2

**Typ:** Nowa funkcja  
**Kategoria usługi:** Uwierzytelnienia (nazwy logowania)  
**Możliwość produktu:** Uwierzytelnianie użytkownika

Klienci usługi Azure AD mogą teraz ustawiać zasady zarządzania kluczami zabezpieczeń FIDO2 dla użytkowników i grup organizacji. Użytkownicy końcowi mogą również rejestrować swoje klucze zabezpieczeń, używać tych kluczy do logowania się do swoich kont Microsoft w witrynach sieci Web na urządzeniach z systemem FIDO, a także do logowania się do urządzeń systemu Windows 10 przyłączonych do usługi Azure AD.

Aby uzyskać więcej informacji, zobacz [Włączanie logowania bezhasła w usłudze Azure AD (wersja zapoznawcza)](/azure/active-directory/authentication/concept-authentication-passwordless) w celu uzyskania informacji związanych z administratorem i [Konfigurowanie informacji zabezpieczających do korzystania z klucza zabezpieczeń (wersja zapoznawcza)](https://docs.microsoft.com/azure/active-directory/user-help/security-info-setup-security-key) dla informacji związanych z użytkownikiem końcowym.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---july-2019"></a>Nowe aplikacje federacyjne dostępne w galerii aplikacja usługi Azure AD — lipiec 2019

**Typ:** Nowa funkcja  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwości produktu:** integracja innej firmy

W lipcu 2019 dodaliśmy te 18 nowych aplikacji z obsługą Federacji do galerii aplikacji:

[Ungerboeck Software](https://docs.microsoft.com/azure/active-directory/saas-apps/ungerboeck-software-tutorial), [jasnozielony wzorzec omnichannel Center contacting](https://docs.microsoft.com/azure/active-directory/saas-apps/bright-pattern-omnichannel-contact-center-tutorial), [sprytne Nelly](https://docs.microsoft.com/azure/active-directory/saas-apps/clever-nelly-tutorial), [AcquireIO](https://docs.microsoft.com/azure/active-directory/saas-apps/acquireio-tutorial), [Looop](https://www.looop.co/schedule-a-demo/), [productboard](https://docs.microsoft.com/azure/active-directory/saas-apps/productboard-tutorial), [MS Azure SSO Access for Ethidex zgodność Office™](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on#password-based-sso), [pogłoskami narosłymi](https://docs.microsoft.com/azure/active-directory/saas-apps/hype-tutorial), [abstract](https://docs.microsoft.com/azure/active-directory/saas-apps/abstract-tutorial), [wzniesienieer](https://docs.microsoft.com/azure/active-directory/saas-apps/ascentis-tutorial), [Flipsnack](https://www.flipsnack.com/accounts/sign-in-sso.html), [Wandera](https://docs.microsoft.com/azure/active-directory/saas-apps/wandera-tutorial), [TwineSocial](https://twinesocial.com/), [Kallidus](https://docs.microsoft.com/azure/active-directory/saas-apps/kallidus-tutorial), [HyperAnna](https://docs.microsoft.com/azure/active-directory/saas-apps/hyperanna-tutorial), [PharmID WasteWitness](https://www.pharmid.com/), [i2B Connect](https://www.i2b-online.com/sign-up-to-use-i2b-connect-here-sso-access/), [JFrog Artifactory](https://docs.microsoft.com/azure/active-directory/saas-apps/jfrog-artifactory-tutorial)

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

Jeśli nie masz już możliwości zarządzania długimi listami adresów IP i zakresów, możesz użyć nowego tagu usługi sieciowej **AzureActiveDirectoryDomainServices** w grupie zabezpieczeń sieci platformy Azure, aby zabezpieczyć ruch przychodzący do podsieci sieci wirtualnej Azure AD Domain Services.

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

Wszyscy Administratorzy usługi Azure AD mogą teraz wybrać transparent w górnej części istniejących raportów zabezpieczeń, takich jak Raport **o podwyższonym ryzyku** , aby rozpocząć korzystanie z nowego środowiska zabezpieczeń, jak pokazano w raportach **ryzykownych użytkowników** i **ryzykownych** logowań. Wraz z upływem czasu wszystkie raporty zabezpieczeń będą przenoszone ze starszych wersji do nowych wersji, a nowe raporty udostępniają następujące dodatkowe możliwości:

- Zaawansowane filtrowanie i sortowanie

- Akcje zbiorcze, takie jak odrzucanie ryzyka użytkownika

- Potwierdzenie ochrony jednostek naruszonych lub bezpiecznych

- Stan ryzyka, obejmujący: zagrożone, odrzucone, korygowane i potwierdzone naruszone

Aby uzyskać więcej informacji, zobacz Raport [dotyczący ryzykownych użytkowników](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-investigate-risk#risky-users) i [raport dotyczący ryzykownych](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-investigate-risk#risky-sign-ins)logowań.

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

[Azure AD SAML Toolkit](https://docs.microsoft.com/azure/active-directory/saas-apps/saml-toolkit-tutorial), [Otsuka Shokai (大塚商会)](https://docs.microsoft.com/azure/active-directory/saas-apps/otsuka-shokai-tutorial), [ANAQUA](https://docs.microsoft.com/azure/active-directory/saas-apps/anaqua-tutorial), [Klient sieci VPN platformy Azure](https://portal.azure.com/), [wydatki](https://docs.microsoft.com/azure/active-directory/saas-apps/expensein-tutorial), [pomocnik](https://docs.microsoft.com/azure/active-directory/saas-apps/helper-helper-tutorial)pomocnika, [Costpoint](https://docs.microsoft.com/azure/active-directory/saas-apps/costpoint-tutorial), [GlobalOne](https://docs.microsoft.com/azure/active-directory/saas-apps/globalone-tutorial), [Mercedes-Benz w biurze](https://me.secure.mercedes-benz.com/), [skore](https://app.justskore.it/), [konsola infrastruktury Oracle Cloud Infrastructure](https://docs.microsoft.com/azure/active-directory/saas-apps/oracle-cloud-tutorial), [CyberArk — uwierzytelnianie SAML](https://docs.microsoft.com/azure/active-directory/saas-apps/cyberark-saml-authentication-tutorial), Scrible [edu](https://www.scrible.com/sign-in/#/create-account), [PandaDoc](https://docs.microsoft.com/azure/active-directory/saas-apps/pandadoc-tutorial), [Perceptyx](https://apexdata.azurewebsites.net/docs.microsoft.com/azure/active-directory/saas-apps/perceptyx-tutorial), Proptimise [OS](https://proptimise.co.uk/software/), [vtiger CRM (SAML)](https://docs.microsoft.com/azure/active-directory/saas-apps/vtiger-crm-saml-tutorial), Oracle Access Manager dla programu Oracle Handel detaliczny, program Oracle Access Manager dla Oracle E-Business Suite, Oracle IDCS for E-Business Suite, Oracle IDCS for PeopleSoft, Oracle IDCS for JD Edwards

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

[Freedcamp](https://docs.microsoft.com/azure/active-directory/saas-apps/freedcamp-tutorial), [prawdziwe linki](https://docs.microsoft.com/azure/active-directory/saas-apps/real-links-tutorial), [Kianda](https://app.kianda.com/sso/OpenID/AzureAD/), [Simple Signer](https://docs.microsoft.com/azure/active-directory/saas-apps/simple-sign-tutorial), [Braze](https://docs.microsoft.com/azure/active-directory/saas-apps/braze-tutorial), [Display](https://docs.microsoft.com/azure/active-directory/saas-apps/displayr-tutorial), [TEMPLAFY](https://docs.microsoft.com/azure/active-directory/saas-apps/templafy-tutorial), [Marketo](https://toutapp.com/login),, [ACLP](https://docs.microsoft.com/azure/active-directory/saas-apps/aclp-tutorial), [systemy](https://docs.microsoft.com/azure/active-directory/saas-apps/outsystems-tutorial),, META4 [Global HR](https://docs.microsoft.com/azure/active-directory/saas-apps/meta4-global-hr-tutorial), [miejsce pracy](https://docs.microsoft.com/azure/active-directory/saas-apps/quantum-workplace-tutorial)w usłudze Quantum, [, interfejsy](https://docs.microsoft.com/azure/active-directory/saas-apps/cobalt-tutorial) [API usługi webMethods](https://docs.microsoft.com/azure/active-directory/saas-apps/webmethods-integration-cloud-tutorial), [RedFlag](https://pocketstop.com/redflag/), [Whatfix](https://docs.microsoft.com/azure/active-directory/saas-apps/whatfix-tutorial), [Control](https://docs.microsoft.com/azure/active-directory/saas-apps/control-tutorial), [JOBHUB](https://docs.microsoft.com/azure/active-directory/saas-apps/jobhub-tutorial), [NEOGOV](https://docs.microsoft.com/azure/active-directory/saas-apps/neogov-tutorial), [Foodee](https://docs.microsoft.com/azure/active-directory/saas-apps/foodee-tutorial), [MyVR](https://docs.microsoft.com/azure/active-directory/saas-apps/myvr-tutorial)

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

Aby uzyskać więcej informacji na temat odświeżonej wersji Azure AD Identity Protection, zobacz [cztery główne Azure AD Identity Protection ulepszenia znajdują się teraz w publicznym blogu w wersji zapoznawczej](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Four-major-Azure-AD-Identity-Protection-enhancements-are-now-in/ba-p/326935) , a [co to jest Azure Active Directory Identity Protection (odświeżane)?](https://docs.microsoft.com/azure/active-directory/identity-protection/overview-v2) art. Aby uzyskać więcej informacji o wykrywaniu analizy zagrożeń w usłudze Azure AD, zobacz artykuł dotyczący [wykrywania zagrożeń Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/identity-protection/concept-identity-protection-risks) .

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

[SAP Fiori](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-fiori-tutorial), [HRworks logowania](https://docs.microsoft.com/azure/active-directory/saas-apps/hrworks-single-sign-on-tutorial)jednokrotnego, [Percolate](https://docs.microsoft.com/azure/active-directory/saas-apps/percolate-tutorial), [MobiControl](https://docs.microsoft.com/azure/active-directory/saas-apps/mobicontrol-tutorial), [Citrix webskaler](https://docs.microsoft.com/azure/active-directory/saas-apps/citrix-netscaler-tutorial), [Shibumi](https://docs.microsoft.com/azure/active-directory/saas-apps/shibumi-tutorial), [kanaper](https://docs.microsoft.com/azure/active-directory/saas-apps/benchling-tutorial), [MileIQ](https://mileiq.onelink.me/991934284/7e980085), [PageDNA](https://docs.microsoft.com/azure/active-directory/saas-apps/pagedna-tutorial), [EduBrite](https://docs.microsoft.com/azure/active-directory/saas-apps/edubrite-lms-tutorial), RStudio [,](https://docs.microsoft.com/azure/active-directory/saas-apps/rstudio-connect-tutorial), [AMMS](https://docs.microsoft.com/azure/active-directory/saas-apps/amms-tutorial), [e,](https://docs.microsoft.com/azure/active-directory/saas-apps/mitel-connect-tutorial) [Cloud (Logowanie jednokrotne oparte na rolach)](https://docs.microsoft.com/azure/active-directory/saas-apps/alibaba-cloud-service-role-based-sso-tutorial), [Alibaba Management](https://docs.microsoft.com/azure/active-directory/saas-apps/certent-equity-management-tutorial), Certent [Certificate Manager](https://docs.microsoft.com/azure/active-directory/saas-apps/sectigo-certificate-manager-tutorial), [Sectigo](https://docs.microsoft.com/azure/active-directory/saas-apps/greenorbit-tutorial), [GreenOrbit](https://docs.microsoft.com/azure/active-directory/saas-apps/workgrid-tutorial), [Workgrid](https://docs.microsoft.com/azure/active-directory/saas-apps/mondaycom-tutorial), [ SurveyMonkey Enterprise](https://docs.microsoft.com/azure/active-directory/saas-apps/surveymonkey-enterprise-tutorial), [Indiggo](https://indiggolead.com/)

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

Azure AD Connect jest w trakcie przechodzenia do naszych systemów alertów e-mail, co może spowodować, że niektórzy klienci są nowym nadawcą poczty e-mail. Aby rozwiązać ten problemy, należy dodać `azure-noreply@microsoft.com` do listy dozwolonych organizacji lub nie będzie można kontynuować otrzymywania ważnych alertów z pakietu Office 365, Azure lub usług synchronizacji.

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

Aby uzyskać więcej informacji na temat konfigurowania urządzeń i aplikacji oraz zarządzania nimi przy użyciu dostępu warunkowego, zobacz [Wymagaj zarządzanych urządzeń dla dostępu do aplikacji w chmurze z dostępem warunkowym](https://docs.microsoft.com/azure/active-directory/conditional-access/require-managed-devices) i [Wymagaj zatwierdzonych aplikacji klienckich do dostępu do aplikacji w chmurze przy użyciu dostępu warunkowego](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access). Aby uzyskać więcej informacji o sposobach zarządzania dostępem przy użyciu programu Microsoft Edge z zasadami Microsoft Intune, zobacz [Zarządzanie dostępem do Internetu za pomocą przeglądarki Microsoft Intune chronionej przez zasady](https://docs.microsoft.com/intune/app-configuration-managed-browser).

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

[ISEC7 Mobile Exchange Delegate](https://www.isec7.com/english/), [MediusFlow](https://office365.cloudapp.mediusflow.com/), [ePlatform](https://docs.microsoft.com/azure/active-directory/saas-apps/eplatform-tutorial), [Fulcrum](https://docs.microsoft.com/azure/active-directory/saas-apps/fulcrum-tutorial), [ExcelityGlobal](https://docs.microsoft.com/azure/active-directory/saas-apps/excelityglobal-tutorial), [system inspekcji oparty na wyjaśnieniu](https://docs.microsoft.com/azure/active-directory/saas-apps/explanation-based-auditing-system-tutorial), [Leanion](https://docs.microsoft.com/azure/active-directory/saas-apps/lean-tutorial), [Powerschool — zagadnienia dotyczące wydajności](https://docs.microsoft.com/azure/active-directory/saas-apps/powerschool-performance-matters-tutorial), [Cinode](https://cinode.com/), [Sieć intranetowa Iris](https://docs.microsoft.com/azure/active-directory/saas-apps/iris-intranet-tutorial), [Empactis](https://docs.microsoft.com/azure/active-directory/saas-apps/empactis-tutorial), [SmartDraw](https://docs.microsoft.com/azure/active-directory/saas-apps/smartdraw-tutorial), [Confirmit Horizons](https://docs.microsoft.com/azure/active-directory/saas-apps/confirmit-horizons-tutorial) [, zadanie](https://docs.microsoft.com/azure/active-directory/saas-apps/tas-tutorial)

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

[Euromonitor paszport](https://docs.microsoft.com/azure/active-directory/saas-apps/euromonitor-passport-tutorial), [MindTickle](https://docs.microsoft.com/azure/active-directory/saas-apps/mindtickle-tutorial), [odcisk palca](https://seeforgetest-exxon.azurewebsites.net/Account/create?Length=7), [STOSy](https://docs.microsoft.com/azure/active-directory/saas-apps/airstack-tutorial)w [bazie danych Oracle, ERP](https://docs.microsoft.com/azure/active-directory/saas-apps/oracle-fusion-erp-tutorial), [iDrive](https://docs.microsoft.com/azure/active-directory/saas-apps/idrive-tutorial), [skyward Qmlativ](https://docs.microsoft.com/azure/active-directory/saas-apps/skyward-qmlativ-tutorial), [Brightidea](https://docs.microsoft.com/azure/active-directory/saas-apps/brightidea-tutorial), [AlertOps](https://docs.microsoft.com/azure/active-directory/saas-apps/alertops-tutorial), [Soloinsight-CloudGate rejestracji jednokrotnej](https://docs.microsoft.com/azure/active-directory/saas-apps/soloinsight-cloudgate-sso-tutorial), uprawnienia do kliknięcia, [Brandfolder](https://docs.microsoft.com/azure/active-directory/saas-apps/brandfolder-tutorial) [, StoregateSmartFile,](https://docs.microsoft.com/azure/active-directory/saas-apps/smartfile-tutorial) [Pexip](https://docs.microsoft.com/azure/active-directory/saas-apps/pexip-tutorial), [Stormboard](https://docs.microsoft.com/azure/active-directory/saas-apps/stormboard-tutorial), [sejsmiczne](https://docs.microsoft.com/azure/active-directory/saas-apps/seismic-tutorial), [udostępnianie marzenie](https://www.shareadream.org/how-it-works), [Bugsnag](https://docs.microsoft.com/azure/active-directory/saas-apps/bugsnag-tutorial), [webMethods Integration Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/webmethods-integration-cloud-tutorial), [wiedza System LMS z dowolnego miejsca](https://docs.microsoft.com/azure/active-directory/saas-apps/knowledge-anywhere-lms-tutorial), [kampus w jednostce organizacyjnej](https://docs.microsoft.com/azure/active-directory/saas-apps/ou-campus-tutorial), [dane Periscope](https://docs.microsoft.com/azure/active-directory/saas-apps/periscope-data-tutorial), [Portal NetOp](https://docs.microsoft.com/azure/active-directory/saas-apps/netop-portal-tutorial), [smartvid.IO](https://docs.microsoft.com/azure/active-directory/saas-apps/smartvid.io-tutorial), [PureCloud przez Genesys](https://docs.microsoft.com/azure/active-directory/saas-apps/purecloud-by-genesys-tutorial), [platforma produktywności ClickUp](https://docs.microsoft.com/azure/active-directory/saas-apps/clickup-productivity-platform-tutorial)

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

## <a name="january-2019"></a>Styczeń 2019 r.

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

[Firstbird](https://docs.microsoft.com/azure/active-directory/saas-apps/firstbird-tutorial), [Folloze](https://docs.microsoft.com/azure/active-directory/saas-apps/folloze-tutorial), [paleta talent](https://docs.microsoft.com/azure/active-directory/saas-apps/talent-palette-tutorial), [infor CloudSuite](https://docs.microsoft.com/azure/active-directory/saas-apps/infor-cloud-suite-tutorial), [Cisco parasol](https://docs.microsoft.com/azure/active-directory/saas-apps/cisco-umbrella-tutorial), [rozwiązania Zscaler dostęp do Internetu](https://docs.microsoft.com/azure/active-directory/saas-apps/zscaler-internet-access-administrator-tutorial), [przypomnienia o wygaśnięciu](https://docs.microsoft.com/azure/active-directory/saas-apps/expiration-reminder-tutorial), [InstaVR Viewer](https://docs.microsoft.com/azure/active-directory/saas-apps/instavr-viewer-tutorial), [CORPTAX](https://docs.microsoft.com/azure/active-directory/saas-apps/corptax-tutorial), [czasownik](https://app.verb.net/login), [OpenLattice](https://openlattice.com/agora), [TheOrgWiki](https://www.theorgwiki.com/signup), [Pavaso Digital Close](https://docs.microsoft.com/azure/active-directory/saas-apps/pavaso-digital-close-tutorial), [GoodPractice Toolkit](https://docs.microsoft.com/azure/active-directory/saas-apps/goodpractice-toolkit-tutorial), [Cloud Service Picco](https://docs.microsoft.com/azure/active-directory/saas-apps/cloud-service-picco-tutorial), [AuditBoard](https://docs.microsoft.com/azure/active-directory/saas-apps/auditboard-tutorial), [iProva](https://docs.microsoft.com/azure/active-directory/saas-apps/iprova-tutorial), on- [CallPlease](https://webapp.callplease.com/create-account/create-account.html) [,](https://docs.microsoft.com/azure/active-directory/saas-apps/workable-tutorial) [GTNexus system rejestracji jednokrotnej](https://docs.microsoft.com/azure/active-directory/saas-apps/gtnexus-sso-module-tutorial), [CBRE ServiceInsight](https://docs.microsoft.com/azure/active-directory/saas-apps/cbre-serviceinsight-tutorial), [Deskradar](https://docs.microsoft.com/azure/active-directory/saas-apps/deskradar-tutorial), [Coralogixv](https://docs.microsoft.com/azure/active-directory/saas-apps/coralogix-tutorial), [Signagelive](https://docs.microsoft.com/azure/active-directory/saas-apps/signagelive-tutorial), [arach dla przedsiębiorstw](https://docs.microsoft.com/azure/active-directory/saas-apps/ares-for-enterprise-tutorial), [K2 dla pakietu Office 365](https://www.k2.com/O365), [Xledger](https://www.xledger.net/), [iDiD Manager](https://docs.microsoft.com/azure/active-directory/saas-apps/idid-manager-tutorial), [HighGear](https://docs.microsoft.com/azure/active-directory/saas-apps/highgear-tutorial), [wiedza](https://docs.microsoft.com/azure/active-directory/saas-apps/visitly-tutorial), [prom Alp](https://docs.microsoft.com/azure/active-directory/saas-apps/korn-ferry-alp-tutorial) [,](https://docs.microsoft.com/azure/active-directory/saas-apps/adoddle-csaas-platform-tutorial) [Acadia](https://docs.microsoft.com/azure/active-directory/saas-apps/acadia-tutorial)<!-- , [CaféX Portal (Meetings)](https://docs.microsoft.com/azure/active-directory/saas-apps/cafexportal-meetings-tutorial), [MazeMap Link](https://docs.microsoft.com/azure/active-directory/saas-apps/mazemaplink-tutorial)-->  

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

Rozwiązano problem polegający na tym, że flaga DirSyncEnabled użytkownika zostanie błędnie przełączona na **Fałsz** , gdy obiekt Active Directory Domain Services (AD DS) został wykluczony z zakresu synchronizacji, a następnie przeniesiony do kosza w usłudze Azure AD w następującym cyklu synchronizacji. W wyniku tej poprawki Jeśli użytkownik zostanie wykluczony z zakresu synchronizacji, a następnie przywrócono z Kosza usługi Azure AD, konto użytkownika pozostanie jako zsynchronizowane z lokalnej usługi AD zgodnie z oczekiwaniami i nie będzie możliwe do zarządzania w chmurze, ponieważ jego źródło (SoA) pozostaje jako lokalna usługi AD.

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
|targetResources|Obejmuje **TargetResourceType** na najwyższego poziomu.|&nbsp;|<ul><li>Zasady</li><li>Aplikacja</li><li>Użytkownik</li><li>Grupa</li></ul>|
|loggedByService|Zawiera nazwę usługi, która wygenerowała dziennik inspekcji.|Null|<ul><li>Aprowizacja kont</li><li>Katalog podstawowy</li><li>Samoobsługowe resetowanie haseł</li></ul>|
|Wynik|Zawiera wyniki dzienników inspekcji. Wcześniej było to wyliczone, ale teraz pokazywane są rzeczywiste wartości.|<ul><li>0</li><li>1</li></ul>|<ul><li>Powodzenie</li><li>Niepowodzenie</li></ul>|

#### <a name="changed-fields-in-the-sign-in-schema"></a>Zmieniono pola w schemacie logowania
Następujące pola są zmieniane w schemacie logowania:

|Nazwa pola|Co zostało zmienione|Stare wartości|Nowe wartości|
|----------|------------|----------|----------|
|appliedConditionalAccessPolicies|To było pole **conditionalaccessPolicies** . To teraz pole **appliedConditionalAccessPolicies** .|Nie wprowadzono zmian|Nie wprowadzono zmian|
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

- Email

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

[CoreStack](https://cloud.corestack.io/site/login), [HubSpot](https://docs.microsoft.com/azure/active-directory/saas-apps/HubSpot-tutorial), [](https://docs.microsoft.com/azure/active-directory/saas-apps/getthere-tutorial)Getin, [gra-PE](https://docs.microsoft.com/azure/active-directory/saas-apps/grape-tutorial), [eHour](https://getehour.com/try-now), [Consent2Go](https://docs.microsoft.com/azure/active-directory/saas-apps/Consent2Go-tutorial), [Appinux](https://docs.microsoft.com/azure/active-directory/saas-apps/appinux-tutorial), [DriveDollar](https://azuremarketplace.microsoft.com/marketplace/apps/savitas.drivedollar-azuread?tab=Overview), [Useall](https://docs.microsoft.com/azure/active-directory/saas-apps/useall-tutorial), [infinite](https://docs.microsoft.com/azure/active-directory/saas-apps/infinitecampus-tutorial), [Alaya](https://alayagood.com/en/demo/), [HeyBuddy](https://docs.microsoft.com/azure/active-directory/saas-apps/heybuddy-tutorial), [Wrike SAML](https://docs.microsoft.com/azure/active-directory/saas-apps/wrike-tutorial), [dryf](https://docs.microsoft.com/azure/active-directory/saas-apps/drift-tutorial), [Zenegy for Business Central 365](https://accounting.zenegy.com/), [EverBridge — Portal elementów członkowskich](https://docs.microsoft.com/azure/active-directory/saas-apps/everbridge-tutorial), [ideo](https://profile.ideo.com/users/sign_up), [Ivanti Service Manager (ISM)](https://docs.microsoft.com/azure/active-directory/saas-apps/ivanti-service-manager-tutorial), [Peakon](https://docs.microsoft.com/azure/active-directory/saas-apps/peakon-tutorial), [Allbound SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/allbound-sso-tutorial), [aplikacje typu plex — test klasyczny ](https://test.plexonline.com/signon), [Aplikacje typu plex — klasyczne](https://www.plexonline.com/signon), [aplikacje typu plex — test środowiska](https://test.cloud.plex.com/sso)użytkownika, [aplikacje obiektów typu plex — UX](https://cloud.plex.com/sso), [aplikacje typu plex — IAM](https://accounts.plex.com/), [łodzie, zapisy, frekwencja, & finansowy system śledzenia](https://getcrafts.ca/craftsregistration) 

Aby uzyskać więcej informacji na temat aplikacji, zobacz [SaaS Application Integration with Azure Active Directory](https://aka.ms/appstutorial). Aby uzyskać więcej informacji na temat tworzenia listy aplikacji w galerii aplikacji usługi Azure AD, zobacz [Wyświetlanie listy aplikacji w galerii aplikacji Azure Active Directory](https://aka.ms/azureadapprequest).

---

## <a name="october-2018"></a>Październik 2018 r.

### <a name="azure-ad-logs-now-work-with-azure-log-analytics-public-preview"></a>Dzienniki usługi Azure AD obecnie pracować z usługą Azure Log Analytics (publiczna wersja zapoznawcza)

**Typ:** Nowa funkcja  
**Kategoria usługi:** Reporting  
**Możliwość produktu:** Monitorowanie & raportowania

Cieszymy się, mogąc mogą obecnie przekazywać dzienników usługi Azure AD do usługi Azure Log Analytics! Najczęściej żądanych pomaga umożliwiają jeszcze lepsze dostęp do analizy dla Twojej firmy, operacji i zabezpieczeń, a także sposób, aby wspomóc monitorowanie infrastruktury. Aby uzyskać więcej informacji, zobacz [dzienniki aktywności Azure Active Directory w usłudze Azure log Analytics teraz dostępny](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-Active-Directory-Activity-logs-in-Azure-Log-Analytics-now/ba-p/274843) blog.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---october-2018"></a>Nowe aplikacje federacyjne dostępne w galerii aplikacji usługi Azure AD — października 2018 r.

**Typ:** Nowa funkcja  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwości produktu:** integracja innej firmy

Na października 2018 Dodaliśmy obsługę 14 nowych aplikacjach z Federacją w galerii aplikacji:

[Moje punkty przyznawania](https://docs.microsoft.com/azure/active-directory/saas-apps/myawardpoints-tutorial), [Vibe HCM](https://docs.microsoft.com/azure/active-directory/saas-apps/vibehcm-tutorial), ambyint, [MyWorkDrive](https://docs.microsoft.com/azure/active-directory/saas-apps/myworkdrive-tutorial), [BorrowBox](https://docs.microsoft.com/azure/active-directory/saas-apps/borrowbox-tutorial), Dialpad [, ON24 środowisko wirtualne](https://docs.microsoft.com/azure/active-directory/saas-apps/on24-tutorial), [RingCentral](https://docs.microsoft.com/azure/active-directory/saas-apps/ringcentral-tutorial), [rozwiązania Zscaler trzy](https://docs.microsoft.com/azure/active-directory/saas-apps/zscaler-three-tutorial), [Phraseanet](https://docs.microsoft.com/azure/active-directory/saas-apps/phraseanet-tutorial), [Appraisd](https://docs.microsoft.com/azure/active-directory/saas-apps/appraisd-tutorial), [Workspot](https://docs.microsoft.com/azure/active-directory/saas-apps/workspotcontrol-tutorial), [Shuccho Navi](https://docs.microsoft.com/azure/active-directory/saas-apps/shucchonavi-tutorial), [Glassfrog](https://docs.microsoft.com/azure/active-directory/saas-apps/glassfrog-tutorial)

Aby uzyskać więcej informacji na temat aplikacji, zobacz [SaaS Application Integration with Azure Active Directory](https://aka.ms/appstutorial). Aby uzyskać więcej informacji na temat tworzenia listy aplikacji w galerii aplikacji usługi Azure AD, zobacz [Wyświetlanie listy aplikacji w galerii aplikacji Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="azure-ad-domain-services-email-notifications"></a>Powiadomienia E-mail usług domenowych Azure AD

**Typ:** Nowa funkcja  
**Kategoria usługi:** Azure AD Domain Services  
**Możliwość produktu:** Azure AD Domain Services

Azure AD Domain Services udostępnia alerty w portalu Azure dotyczące błędów konfiguracji lub problemów z Twoją domeną zarządzaną. Te alerty zawierają przewodniki krok po kroku, dzięki czemu można spróbować rozwiązać problemy bez konieczności kontaktowania się z pomocą techniczną.

Począwszy od października, będzie można dostosować ustawienia powiadomień dla domeny zarządzanej, gdy nowe alerty występują, do wyznaczonej grupy osób, eliminując konieczność stale Sprawdź portal aktualizacji zostanie wysłana wiadomość e-mail.

Aby uzyskać więcej informacji, zobacz [Ustawienia powiadomień w Azure AD Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-notifications).

---

### <a name="azure-ad-portal-supports-using-the-forcedelete-domain-api-to-delete-custom-domains"></a>Platforma Azure obsługuje portalu AD przy użyciu domeny ForceDelete interfejsu API, można usunąć domeny niestandardowej 

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Zarządzanie katalogami  
**Możliwość produktu:** Katalogi

Mamy przyjemność ogłosić, że teraz można użyć domeny ForceDelete interfejsu API można usunąć nazwy domeny niestandardowej, asynchronicznie zmieniając nazwę odwołania, takich jak użytkownicy, grupy i aplikacje z niestandardowej nazwy domeny (contoso.com) do (nazwa domeny początkowej domyślnej contoso.onmicrosoft.com).

Ta zmiana pomaga szybciej usunąć nazwy domeny niestandardowej, czy Twoja organizacja już używa nazwy, czy należy użyć nazwy domeny za pomocą innej usługi Azure AD.

Aby uzyskać więcej informacji, zobacz [usuwanie niestandardowej nazwy domeny](https://docs.microsoft.com/azure/active-directory/users-groups-roles/domains-manage#delete-a-custom-domain-name).

---

## <a name="september-2018"></a>Wrzesień 2018 r.
 
### <a name="updated-administrator-role-permissions-for-dynamic-groups"></a>Uprawnienia roli administrator zaktualizowane grup dynamicznych

**Typ:** FIXED  
**Kategoria usługi:** Zarządzanie grupami  
**Możliwość produktu:** Społeczności

Rozwiązaliśmy problem, więc teraz tworzyć i aktualizować reguły członkostwa dynamicznego, bez potrzeby bycia właściciela grupy ról określonego administratora.

Dostępne są następujące role:

- Administrator globalny

- Administrator usługi Intune

- Administrator użytkowników

Aby uzyskać więcej informacji, zobacz [Tworzenie grupy dynamicznej i sprawdzanie stanu](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-create-rule)

---

### <a name="simplified-single-sign-on-sso-configuration-settings-for-some-third-party-apps"></a>Uproszczone ustawienia konfiguracji pojedynczego logowania jednokrotnego (SSO) w przypadku niektórych aplikacji innych firm

**Typ:** Nowa funkcja  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwość produktu:** ZWRÓCIŁ

Zdajemy sobie sprawę, że ustawienia zapasowej pojedynczego logowania jednokrotnego (SSO) dla oprogramowania jako usługi (SaaS) aplikacji może być trudne, ze względu na unikatowy charakter każdej konfiguracji aplikacji. Mamy skompilowane środowisko uproszczona konfiguracja, aby automatycznie wypełnić ustawień konfiguracji logowania jednokrotnego dla następujących aplikacji SaaS innych firm:

- Zendesk

- Komponent ArcGis Online

- Jamf Pro

Aby rozpocząć korzystanie z tego samego kliknięcia, przejdź do strony **Konfiguracja logowania jednokrotnego** w **Azure Portal** > dla aplikacji. Aby uzyskać więcej informacji, zobacz [SaaS Application Integration with Azure Active Directory](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list)

---

### <a name="azure-active-directory---where-is-your-data-located-page"></a>Usługa Azure Active Directory — Twoje dane lokalizacji? Strona

**Typ:** Nowa funkcja  
**Kategoria usługi:** Różnych  
**Możliwość produktu:** GoLocal

Wybierz region firmy na stronie **Azure Active Directory — gdzie znajdują się Twoje dane** , aby zobaczyć, które centrum danych platformy Azure przechowuje dane usługi Azure AD w spoczynku dla wszystkich usług Azure AD. Informacje można filtrować według określonej usługi Azure AD dla regionu w firmie.

Aby uzyskać dostęp do tej funkcji i uzyskać więcej informacji, zobacz [Azure Active Directory — gdzie znajdują się Twoje dane](https://aka.ms/AADDataMap).

---

### <a name="new-deployment-plan-available-for-the-my-apps-access-panel"></a>Nowy plan wdrażania dostępne do panelu dostępu usługi Moje aplikacje

**Typ:** Nowa funkcja  
**Kategoria usługi:** Moje aplikacje  
**Możliwość produktu:** ZWRÓCIŁ

Zapoznaj się z nowym planem wdrożenia dostępnym dla panelu dostępu moje aplikacje (https://aka.ms/deploymentplans).
Panel Moje dostępu aplikacji zapewnia użytkownikom z jednego miejsca można znaleźć i uzyskać dostęp do swoich aplikacji. Ten portal zapewnia również użytkownikom możliwości samoobsługi, takie jak żądania dostępu do aplikacji i grup oraz zarządzanie dostępem do tych zasobów w imieniu innych użytkowników.

Aby uzyskać więcej informacji, zobacz artykuł [co to jest portal moje aplikacje?](https://docs.microsoft.com/azure/active-directory/user-help/active-directory-saas-access-panel-introduction)

---

### <a name="new-troubleshooting-and-support-tab-on-the-sign-ins-logs-page-of-the-azure-portal"></a>Karta nowe Rozwiązywanie problemów i pomocy technicznej na stronie dzienniki operacji logowania w witrynie Azure Portal

**Typ:** Nowa funkcja  
**Kategoria usługi:** Reporting  
**Możliwość produktu:** Monitorowanie & raportowania

Nowa karta **Rozwiązywanie problemów i pomoc techniczna** na stronie **logowania** Azure Portal ma pomóc administratorom i inżynierom pomocy technicznej rozwiązywać problemy związane z logowaniem do usługi Azure AD. Ta nowa karta zawiera kod błędu, komunikat o błędzie i zalecenia dotyczące korygowania (jeśli istnieją), aby pomóc w rozwiązaniu problemu. Jeśli nie możesz rozwiązać tego problemu, udostępnimy również nowy sposób tworzenia biletu pomocy technicznej przy użyciu funkcji **kopiowania do schowka** , która wypełnia pola **Identyfikator żądania** i **Data (UTC)** dla pliku dziennika w biletu pomocy technicznej.  

![Dzienniki logowania pokazujące nową kartę](media/whats-new/troubleshooting-and-support.png)

---

### <a name="enhanced-support-for-custom-extension-properties-used-to-create-dynamic-membership-rules"></a>Rozszerzona obsługa właściwości niestandardowe rozszerzenie użyte do utworzenia reguły członkostwa dynamicznego

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Zarządzanie grupami  
**Możliwość produktu:** Społeczności

Dzięki tej aktualizacji można teraz kliknąć link **Pobierz niestandardowe właściwości rozszerzenia** z konstruktora reguły dynamicznej grupy użytkowników, wprowadzić unikatowy identyfikator aplikacji i otrzymać pełną listę niestandardowych właściwości rozszerzenia, które będą używane podczas tworzenia reguły członkostwa dynamicznego dla użytkowników. Tej listy można odświeżać w taki sposób, aby uzyskać nowe właściwości rozszerzenia niestandardowego dla danej aplikacji.

Aby uzyskać więcej informacji o używaniu niestandardowych właściwości rozszerzenia dla reguł członkostwa dynamicznego, zobacz [Właściwości rozszerzenia i niestandardowe właściwości rozszerzenia](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership#extension-properties-and-custom-extension-properties) .

---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Nowe zatwierdzone aplikacje klienckie dla dostępu warunkowego opartego na aplikacji usługi Azure AD

**Typ:** Planowanie zmiany  
**Kategoria usługi:** Dostęp warunkowy  
**Możliwość produktu:** Bezpieczeństwo i Ochrona tożsamości

Następujące aplikacje znajdują się na liście [zatwierdzonych aplikacji klienckich](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-conditions#client-apps-preview):

- Microsoft To-Do

- Microsoft Stream

Aby uzyskać więcej informacji, zobacz:

- [Dostęp warunkowy oparty na aplikacji usługi Azure AD](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)

---

### <a name="new-support-for-self-service-password-reset-from-the-windows-7881-lock-screen"></a>Nowa funkcja obsługi samoobsługowego resetowania hasła na ekranie blokady 7/8/8.1 Windows

**Typ:** Nowa funkcja  
**Kategoria usługi:** SSPR  
**Możliwość produktu:** Uwierzytelnianie użytkownika

Po skonfigurowaniu tej nowej funkcji użytkownicy zobaczą link do resetowania hasła na ekranie **blokady** urządzenia z systemem Windows 7, Windows 8 lub Windows 8.1. Klikając ten link, użytkownik jest przeprowadzany przez sam przepływ resetowania hasła jako za pośrednictwem przeglądarki sieci web.

Aby uzyskać więcej informacji, zobacz [jak włączyć resetowanie haseł w systemach Windows 7, 8 i 8,1](https://aka.ms/ssprforwindows78)

---

### <a name="change-notice-authorization-codes-will-no-longer-be-available-for-reuse"></a>Zmień powiadomienie: kody autoryzacji nie będzie już dostępny do ponownego wykorzystania 

**Typ:** Planowanie zmiany  
**Kategoria usługi:** Uwierzytelnienia (nazwy logowania)  
**Możliwość produktu:** Uwierzytelnianie użytkownika

15 listopada 2018 r. usługi Azure AD będzie akceptować kody poprzednio używanych uwierzytelniania dla aplikacji. Ta zmiana zabezpieczeń pomaga do usługi Azure AD zgodnie ze specyfikacją protokołu OAuth i będzie wymuszany w punktach końcowych v1 i v2.

Jeśli aplikacja używa kody autoryzacji uzyskiwanie tokenów dla wielu zasobów, zalecamy Użyj kodu, aby uzyskać token odświeżania, a następnie używać tego tokenu odświeżania w celu pobrania dodatkowe tokeny dla innych zasobów. Kody autoryzacji należy używać tylko raz, ale tokenów odświeżania mogą być wielokrotnie używane w wielu zasobach. Aplikacja, która podejmuje próbę ponownego użycia kodu uwierzytelniania podczas przepływu kodu OAuth otrzymają komunikat o błędzie invalid_grant.

Aby zapoznać się z tymi i innymi zmianami dotyczącymi protokołów, zapoznaj [się z pełną listą nowości związanych z uwierzytelnianiem](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---september-2018"></a>Nowe aplikacje federacyjne dostępne w galerii aplikacji usługi Azure AD — września 2018 r.

**Typ:** Nowa funkcja  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwości produktu:** integracja innej firmy
 
W września 2018 Dodaliśmy obsługę 16 nowych aplikacjach z Federacją w galerii aplikacji:

[Uberflip](https://docs.microsoft.com/azure/active-directory/saas-apps/uberflip-tutorial), [Współspotykać oprogramowanie do rekrutacji](https://docs.microsoft.com/azure/active-directory/saas-apps/comeetrecruitingsoftware-tutorial), [Workteam](https://docs.microsoft.com/azure/active-directory/saas-apps/workteam-tutorial), [ArcGIS Enterprise](https://docs.microsoft.com/azure/active-directory/saas-apps/arcgisenterprise-tutorial), [Nuclino](https://docs.microsoft.com/azure/active-directory/saas-apps/nuclino-tutorial), [JDA Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/jdacloud-tutorial), [Płatne](https://docs.microsoft.com/azure/active-directory/saas-apps/snowflake-tutorial), [NAVIGOCLOUD, Figma](https://docs.microsoft.com/azure/active-directory/saas-apps/figma-tutorial), join.me, [ZephyrSSO](https://docs.microsoft.com/azure/active-directory/saas-apps/zephyrsso-tutorial), [Silverback](https://docs.microsoft.com/azure/active-directory/saas-apps/silverback-tutorial), Riverbed Xirrus EasyPass, [Rackspace SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/rackspacesso-tutorial), Enlyft SSO dla platformy Azure, SurveyMonkey [](https://docs.microsoft.com/azure/active-directory/saas-apps/dmarcian-tutorial) , [zwoływanie](https://docs.microsoft.com/azure/active-directory/saas-apps/convene-tutorial)dmarcian

Aby uzyskać więcej informacji na temat aplikacji, zobacz [SaaS Application Integration with Azure Active Directory](https://aka.ms/appstutorial). Aby uzyskać więcej informacji na temat tworzenia listy aplikacji w galerii aplikacji usługi Azure AD, zobacz [Wyświetlanie listy aplikacji w galerii aplikacji Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="support-for-additional-claims-transformations-methods"></a>Obsługa dodatkowych oświadczeń przekształcenia metod

**Typ:** Nowa funkcja  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwość produktu:** ZWRÓCIŁ

Wprowadziliśmy nowe metody przekształcania roszczeń, ToLower () i ToUpper (), które można zastosować do tokenów SAML na stronie **konfiguracji logowania** jednokrotnego opartego na protokole SAML.

Aby uzyskać więcej informacji, zobacz [How to Dostosowywanie oświadczeń wystawionych w tokenie SAML dla aplikacji dla przedsiębiorstw w usłudze Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization)

---

### <a name="updated-saml-based-app-configuration-ui-preview"></a>Zaktualizowano konfigurację aplikacji opartej na SAML interfejsu użytkownika (wersja zapoznawcza)

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwość produktu:** ZWRÓCIŁ

W ramach naszego interfejsu użytkownika konfiguracji zaktualizowanej aplikacji opartej na SAML uzyskasz:

- Środowisko zaktualizowano wskazówki dotyczące konfigurowania aplikacji opartej na SAML.

- Widoczność o nowościach brakujące lub nieprawidłowe w konfiguracji.

- Możliwość dodawania wielu adresów e-mail, aby uzyskać powiadomienie o wygaśnięciu certyfikatu.

- Nowe metody przekształcania oświadczeń, ToLower() i ToUpper() i inne.

- Sposób przekazać własny token podpisywania certyfikatu dla aplikacji przedsiębiorstwa.

- Sposób, aby ustawić Format identyfikatora NameID dla aplikacji SAML i sposób można ustawić wartości identyfikatora NameID jako rozszerzenia katalogów.

Aby włączyć ten zaktualizowany widok, kliknij link **Wypróbuj nowe środowisko** w górnej części strony **logowania** jednokrotnego. Aby uzyskać więcej informacji, zobacz [Samouczek: Konfigurowanie logowania jednokrotnego opartego na protokole SAML dla aplikacji przy użyciu Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-single-sign-on-non-gallery-applications).

---

## <a name="august-2018"></a>Sierpień 2018 r.

### <a name="changes-to-azure-active-directory-ip-address-ranges"></a>Zmiany do zakresów adresów adresów IP platformy Azure Active Directory

**Typ:** Planowanie zmiany  
**Kategoria usługi:** Różnych  
**Możliwość produktu:** Platformach

Wprowadzamy większych zakresów adresów IP do usługi Azure AD, co oznacza, że jeśli skonfigurowano zakresów adresów IP usługi AD platformy Azure dla zapory, routery lub grupy zabezpieczeń sieci, należy je zaktualizować. Wprowadzamy aktualizację, dzięki czemu nie trzeba ponownie zmienić zapory, router lub konfiguracje zakresu adresów IP z grup zabezpieczeń sieci, gdy usługa Azure AD dodaje nowe punkty końcowe. 

Ruch sieciowy przechodzi do tych nowych zakresów w ciągu następnych dwóch miesięcy. Aby kontynuować nieprzerwane działanie usług, należy dodać te zaktualizowane wartości do adresów IP przed 10 września 2018 r.:

- 20.190.128.0/18 

- 40.126.0.0/18 

Zdecydowanie zaleca się nie usuwa starych zakresów adresów IP, dopóki wszystkie ruchu sieciowego została przeniesiona do nowego zakresu. Aby uzyskać aktualizacje dotyczące przenoszenia i dowiedzieć się, kiedy można usunąć stare zakresy, zobacz [adresy URL i zakresy adresów IP usługi Office 365](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2).

---

### <a name="change-notice-authorization-codes-will-no-longer-be-available-for-reuse"></a>Zmień powiadomienie: kody autoryzacji nie będzie już dostępny do ponownego wykorzystania 

**Typ:** Planowanie zmiany  
**Kategoria usługi:** Uwierzytelnienia (nazwy logowania)  
**Możliwość produktu:** Uwierzytelnianie użytkownika

15 listopada 2018 r. usługi Azure AD będzie akceptować kody poprzednio używanych uwierzytelniania dla aplikacji. Ta zmiana zabezpieczeń pomaga do usługi Azure AD zgodnie ze specyfikacją protokołu OAuth i będzie wymuszany w punktach końcowych v1 i v2.

Jeśli aplikacja używa kody autoryzacji uzyskiwanie tokenów dla wielu zasobów, zalecamy Użyj kodu, aby uzyskać token odświeżania, a następnie używać tego tokenu odświeżania w celu pobrania dodatkowe tokeny dla innych zasobów. Kody autoryzacji należy używać tylko raz, ale tokenów odświeżania mogą być wielokrotnie używane w wielu zasobach. Aplikacja, która podejmuje próbę ponownego użycia kodu uwierzytelniania podczas przepływu kodu OAuth otrzymają komunikat o błędzie invalid_grant.

Aby zapoznać się z tymi i innymi zmianami dotyczącymi protokołów, zapoznaj [się z pełną listą nowości związanych z uwierzytelnianiem](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes).
 
---

### <a name="converged-security-info-management-for-self-service-password-sspr-and-multi-factor-authentication-mfa"></a>Zarządzanie informacjami konwergentnej zabezpieczeń haseł (SSPR) i usługi Multi-Factor Authentication (MFA)

**Typ:** Nowa funkcja  
**Kategoria usługi:** SSPR  
**Możliwość produktu:** Uwierzytelnianie użytkownika
 
Ta nowa funkcja ułatwia osób Zarządzanie swoich informacji zabezpieczających (na przykład, numer telefonu, aplikacja mobilna i tak dalej) dla funkcji samoobsługowego resetowania HASEŁ i uwierzytelniania MFA w jednej lokalizacji i doświadczenie; w porównaniu z wcześniej, gdzie to zostało zrobione w dwóch różnych lokalizacjach.

To środowisko konwergentnej działa także dla osób korzystających z funkcji samoobsługowego resetowania HASEŁ lub uwierzytelniania MFA. Ponadto jeśli Twoja organizacja nie wymusić rejestracji uwierzytelniania Wieloskładnikowego lub samoobsługowego resetowania HASEŁ, osoby nadal można zarejestrować żadnych uwierzytelniania Wieloskładnikowego lub samoobsługowego resetowania HASEŁ zabezpieczeń informacji metod dozwolona przez Twoją organizację, z poziomu portalu Moje aplikacje.

Jest to zgłoszenie zgody na uczestnictwo w publicznej wersji zapoznawczej. Administratorzy mogą włączyć nowe środowisko (w razie potrzeby) dla wybranej grupy lub dla wszystkich użytkowników w dzierżawie. Aby uzyskać więcej informacji na temat zbieżności środowiska, zobacz [Blog dotyczący zbieżności](https://cloudblogs.microsoft.com/enterprisemobility/2018/08/06/mfa-and-sspr-updates-now-in-public-preview/)

---

### <a name="new-http-only-cookies-setting-in-azure-ad-application-proxy-apps"></a>Nowe tylko HTTP ustawienia dotyczącego plików cookie w aplikacje serwera proxy aplikacji usługi Azure AD

**Typ:** Nowa funkcja  
**Kategoria usługi:** Serwer proxy aplikacji  
**Możliwość produktu:** Access Control

Istnieje nowe ustawienie o nazwie, czyli **pliki cookie** w aplikacjach serwera proxy aplikacji. To ustawienie zapewnia dodatkowe zabezpieczenia, dołączając flagi HTTPOnly w nagłówku odpowiedzi HTTP, zarówno serwer Proxy aplikacji dostępu i sesji plików cookie, zatrzymanie dostępu do pliku cookie. pochodzące ze skryptu po stronie klienta i uniemożliwia dalsze akcje, takie jak kopiowanie lub Modyfikowanie pliku cookie. Chociaż ta flaga nie został wcześniej użyty, pliki cookie były zawsze szyfrowane, a następnie przesyłane przy użyciu połączenia SSL w celu zabezpieczenia przed Niepoprawne modyfikacje.

To ustawienie nie jest zgodna z aplikacjami korzystającymi z kontrolki ActiveX, takich jak pulpitu zdalnego. Jeśli jesteś w takiej sytuacji firma Microsoft zaleca, wyłącz to ustawienie.

Aby uzyskać więcej informacji na temat ustawienia plików cookie tylko HTTP, zobacz [publikowanie aplikacji przy użyciu usługi Azure serwer proxy aplikacji usługi Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-publish-azure-portal).

---

### <a name="privileged-identity-management-pim-for-azure-resources-supports-management-group-resource-types"></a>Privileged Identity Management (PIM) dla zasobów platformy Azure obsługuje typy zasobów w grupie zarządzania

**Typ:** Nowa funkcja  
**Kategoria usługi:** Privileged Identity Management  
**Możliwość produktu:** Privileged Identity Management
 
Ustawienia aktywacji i przypisania Just-In-Time można teraz stosować do typów zasobów grupy zarządzania, tak samo, jak już jak w przypadku subskrypcji, grupy zasobów i zasoby (takie jak maszyny wirtualne, App Services i inne). Ponadto każda osoba z rolą, która zapewnia dostęp administratora dla grupy zarządzania może odnajdywać i zarządzać tym zasobem w usłudze PIM.

Aby uzyskać więcej informacji na temat usług PIM i zasobów platformy Azure, zobacz [odnajdywanie zasobów platformy Azure i zarządzanie nimi przy użyciu Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-resource-roles-discover-resources)
 
---

### <a name="application-access-preview-provides-faster-access-to-the-azure-ad-portal"></a>Dostęp do aplikacji (wersja zapoznawcza) umożliwia szybszy dostęp do portalu usługi Azure AD

**Typ:** Nowa funkcja  
**Kategoria usługi:** Privileged Identity Management  
**Możliwość produktu:** Privileged Identity Management
 
Obecnie podczas aktywacji roli przy użyciu usługi PIM, może potrwać ponad 10 minut uprawnień zastosować zmiany. Jeśli zdecydujesz się używać dostępu do aplikacji, która jest obecnie dostępna w publicznej wersji zapoznawczej, Administratorzy mogą uzyskiwać dostęp do portalu usługi Azure AD zaraz po zakończeniu żądania aktywacji.

Obecnie dostęp do aplikacji obsługuje tylko środowisko portalu usługi Azure AD i zasobów platformy Azure. Aby uzyskać więcej informacji na temat usługi PIM i dostępu do aplikacji, zobacz [co to jest Azure AD Privileged Identity Management?](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure)
 
---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---august-2018"></a>Nowe aplikacje federacyjne dostępne w galerii aplikacji usługi Azure AD — sierpnia 2018

**Typ:** Nowa funkcja  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwości produktu:** integracja innej firmy
 
W sierpnia 2018 Dodaliśmy obsługę 16 nowych aplikacjach z Federacją w galerii aplikacji:

[Hornbill](https://docs.microsoft.com/azure/active-directory/saas-apps/hornbill-tutorial), [bridgeline Unbound](https://docs.microsoft.com/azure/active-directory/saas-apps/bridgelineunbound-tutorial), [sosy Labs — testowanie dla urządzeń przenośnych i sieci Web](https://docs.microsoft.com/azure/active-directory/saas-apps/saucelabs-mobileandwebtesting-tutorial), [Łączniki meta Networks](https://docs.microsoft.com/azure/active-directory/saas-apps/metanetworksconnector-tutorial), [jak to zrobisz](https://docs.microsoft.com/azure/active-directory/saas-apps/waywedo-tutorial), [Spotinst](https://docs.microsoft.com/azure/active-directory/saas-apps/spotinst-tutorial), [premaster (Inlogik)](https://docs.microsoft.com/azure/active-directory/saas-apps/promaster-tutorial) [](https://docs.microsoft.com/azure/active-directory/saas-apps/eluminate-tutorial), [](https://docs.microsoft.com/azure/active-directory/saas-apps/dovetale-tutorial) [SchoolBooking,](https://docs.microsoft.com/azure/active-directory/saas-apps/safeconnect-tutorial) [4me](https://docs.microsoft.com/azure/active-directory/saas-apps/4me-tutorial) [,](https://docs.microsoft.com/azure/active-directory/saas-apps/zenqms-tutorial) [Dokumentacja](https://docs.microsoft.com/azure/active-directory/saas-apps/DOSSIER-tutorial), [N2F](https://docs.microsoft.com/azure/active-directory/saas-apps/n2f-expensereports-tutorial) [](https://docs.microsoft.com/azure/active-directory/saas-apps/comm100livechat-tutorial)

Aby uzyskać więcej informacji na temat aplikacji, zobacz [SaaS Application Integration with Azure Active Directory](https://aka.ms/appstutorial). Aby uzyskać więcej informacji na temat tworzenia listy aplikacji w galerii aplikacji usługi Azure AD, zobacz [Wyświetlanie listy aplikacji w galerii aplikacji Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="native-tableau-support-is-now-available-in-azure-ad-application-proxy"></a>Natywna obsługa Tableau jest teraz dostępna na serwerze Proxy aplikacji usługi Azure AD

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Serwer proxy aplikacji  
**Możliwość produktu:** Access Control

Za pomocą naszej aktualizacji z OpenID Connect protokołu OAuth 2.0 Code Grant dla naszych protokołu uwierzytelniania wstępnego, masz już konieczności dodatkowego konfigurowania Tableau za pomocą serwera Proxy aplikacji. Ta zmiana protokołu pomaga również lepszą obsługę bardziej nowoczesnych aplikacji przy użyciu tylko przekierowania HTTP, które często są obsługiwane w znacznikach języka JavaScript i HTML serwera Proxy aplikacji.

Aby uzyskać więcej informacji na temat naszego natywnej obsługi Tableau, zobacz [Azure serwer proxy aplikacji usługi Azure AD teraz z natywną obsługą Tableau](https://blogs.technet.microsoft.com/applicationproxyblog/2018/08/14/azure-ad-application-proxy-now-with-native-tableau-support).

---

### <a name="new-support-to-add-google-as-an-identity-provider-for-b2b-guest-users-in-azure-active-directory-preview"></a>Obsługa nowych dodać Google jako dostawcy tożsamości dla użytkowników-gości B2B w usłudze Azure Active Directory (wersja zapoznawcza)

**Typ:** Nowa funkcja  
**Kategoria usługi:** B2B  
**Możliwość produktu:** B2B/B2C

Przez skonfigurowanie Federacji za pomocą usługi Google w Twojej organizacji, można pozwolić zaproszonego logowania użytkowników usługi Gmail udostępnionych aplikacji i zasobów przy użyciu istniejącego konta Google, bez konieczności tworzenia osobistych Account Microsoft (MSA) lub konta usługi Azure AD.

Jest to zgłoszenie zgody na uczestnictwo w publicznej wersji zapoznawczej. Aby uzyskać więcej informacji na temat usługi Google Federation, zobacz [Dodawanie usługi Google jako dostawcy tożsamości dla użytkowników-Gości B2B](https://docs.microsoft.com/azure/active-directory/b2b/google-federation).

---

## <a name="july-2018"></a>Lipiec 2018 r.

### <a name="improvements-to-azure-active-directory-email-notifications"></a>Ulepszenia powiadomień e-mail w usłudze Azure Active Directory

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Różnych  
**Możliwość produktu:** Zarządzanie cyklem życia tożsamości
 
Usługa Azure Active Directory (Azure AD) wiadomości e-mail teraz funkcji zaktualizowanego projektu, a także zmiany adres e-mail nadawcy i nazwa wyświetlana nadawcy, gdy wysyłane z następującymi usługami:
 
- Przeglądy dostępu w usłudze Azure AD
- Azure AD Connect Health 
- Usługa Azure AD Identity Protection 
- Azure AD Privileged Identity Management
- Powiadomienia certyfikat wygasa aplikacji przedsiębiorstwa
- Powiadomienia usługi inicjowania obsługi administracyjnej aplikacji przedsiębiorstwa
 
Powiadomienia e-mail będą wysyłane na następujący adres e-mail nadawcy i nazwa wyświetlana:

- Adres e-mail: azure-noreply@microsoft.com
- Nazwa wyświetlana: Microsoft Azure
 
Aby zapoznać się z przykładem niektórych nowych projektów poczty e-mail i więcej informacji, zobacz [powiadomienia e-mail w usłudze Azure AD PIM](https://go.microsoft.com/fwlink/?linkid=2005832).

---

### <a name="azure-ad-activity-logs-are-now-available-through-azure-monitor"></a>Dzienniki aktywności w usłudze Azure AD są teraz dostępne za pośrednictwem usługi Azure Monitor

**Typ:** Nowa funkcja  
**Kategoria usługi:** Reporting  
**Możliwość produktu:** Monitorowanie & raportowania

Dzienniki aktywności usługi Azure AD są teraz dostępne w publicznej wersji zapoznawczej dla usługi Azure Monitor (Usługa monitorowania całej platformy Azure). Usługa Azure Monitor udostępnia długoterminowego przechowywania i bezproblemową integrację, oprócz tych usprawnień:

- Długoterminowe przechowywanie kierując pliki dzienników z kontem usługi Azure storage.

- Bezproblemowa integracja rozwiązania SIEM, bez konieczności pisania lub obsługa skryptów niestandardowych.

- Bezproblemowa integracja z własnych niestandardowych rozwiązań, narzędzia do analizy lub rozwiązania do zarządzania zdarzeniami.

Aby uzyskać więcej informacji na temat tych nowych funkcji, zobacz [dzienniki aktywności usługi Azure AD w blogu w Azure monitor Diagnostyka jest teraz w publicznej wersji zapoznawczej](https://cloudblogs.microsoft.com/enterprisemobility/2018/07/26/azure-ad-activity-logs-in-azure-monitor-diagnostics-now-in-public-preview/) i naszej dokumentacji, [Azure Active Directory dzienników aktywności w Azure monitor (wersja zapoznawcza)](https://docs.microsoft.com/azure/active-directory/reporting-azure-monitor-diagnostics-overview).

---

### <a name="conditional-access-information-added-to-the-azure-ad-sign-ins-report"></a>Informacje o dostępie warunkowym dodane do raportu logowania do usługi Azure AD

**Typ:** Nowa funkcja  
**Kategoria usługi:** Reporting  
**Możliwość produktu:** Ochrona tożsamości & zabezpieczenia
 
Ta aktualizacja pozwala zobaczyć, które zasady są oceniane, gdy użytkownik loguje się wraz z wyników zasad. Ponadto raport zawiera teraz typ aplikacji klienckiej używane przez użytkownika, dzięki czemu można zidentyfikować ruch w protokole starszej wersji. Wpisy raportu mogą teraz również być wyszukiwane identyfikator korelacji, który można znaleźć w komunikacie o błędzie widocznych dla użytkownika i może służyć do identyfikowania i rozwiązywania problemów z pasujące żądanie logowania.

---

### <a name="view-legacy-authentications-through-sign-ins-activity-logs"></a>Widok starszych uwierzytelnienia za pomocą dzienników aktywności logowania

**Typ:** Nowa funkcja  
**Kategoria usługi:** Reporting  
**Możliwość produktu:** Monitorowanie & raportowania
 
Po wprowadzeniu pola **aplikacji klienta** w dziennikach aktywności logowania klienci mogą teraz zobaczyć użytkowników korzystających ze starszych uwierzytelnień. Klienci będą mogli uzyskać dostęp do tych informacji przy użyciu interfejsu API logowania Microsoft Graph lub za pośrednictwem dzienników aktywności logowania w portalu usługi Azure AD, gdzie można użyć kontrolki **App Client** do filtrowania starszych uwierzytelnień. Zapoznaj się z dokumentacją, aby uzyskać więcej informacji.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---july-2018"></a>Nowe aplikacje federacyjne dostępne w galerii aplikacji usługi Azure AD — lipca 2018 r.

**Typ:** Nowa funkcja  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwości produktu:** integracja innej firmy
 
W lipca 2018 Dodaliśmy obsługę 16 nowych aplikacjach z Federacją w galerii aplikacji:

[Centra innowacji](https://docs.microsoft.com/azure/active-directory/saas-apps/innovationhub-tutorial), [Leapsome](https://docs.microsoft.com/azure/active-directory/saas-apps/leapsome-tutorial), [niektóre administratora logowania jednokrotnego](https://docs.microsoft.com/azure/active-directory/saas-apps/certainadminsso-tutorial), PSUC, [IPass SmartConnect](https://docs.microsoft.com/azure/active-directory/saas-apps/ipasssmartconnect-tutorial), [](https://review.docs.microsoft.com/azure/active-directory/saas-apps/smartlpa-tutorial) [](https://review.docs.microsoft.com/azure/active-directory/saas-apps/kanbanize-tutorial) [zrzut ekranu przedstawiający-O-Matic](https://docs.microsoft.com/azure/active-directory/saas-apps/screencast-tutorial), PowerSchool ujednoliconej klasy, [eli](https://docs.microsoft.com/azure/active-directory/saas-apps/elionboarding-tutorial), [Bomgar, NimbleX](https://docs.microsoft.com/azure/active-directory/saas-apps/bomgarremotesupport-tutorial), [Insight4GRC,](https://docs.microsoft.com/azure/active-directory/saas-apps/nimblex-tutorial) [](https://docs.microsoft.com/azure/active-directory/saas-apps/imagineerwebvision-tutorial) [](https://docs.microsoft.com/azure/active-directory/saas-apps/insight4grc-tutorial) [](https://docs.microsoft.com/azure/active-directory/saas-apps/securejoinnow-tutorial) [](https://docs.microsoft.com/azure/active-directory/saas-apps/skillsbase-tutorial)

Aby uzyskać więcej informacji na temat aplikacji, zobacz [SaaS Application Integration with Azure Active Directory](https://aka.ms/appstutorial). Aby uzyskać więcej informacji na temat tworzenia listy aplikacji w galerii aplikacji usługi Azure AD, zobacz [Wyświetlanie listy aplikacji w galerii aplikacji Azure Active Directory](https://aka.ms/azureadapprequest).

---
 
### <a name="new-user-provisioning-saas-app-integrations---july-2018"></a>Nowy użytkownik inicjowania obsługi administracyjnej integracji aplikacji SaaS — lipca 2018 r.

**Typ:** Nowa funkcja  
**Kategoria usługi:** Inicjowanie obsługi aplikacji  
**Możliwości produktu:** integracja innej firmy
 
Usługa Azure AD pozwala zautomatyzować procesy tworzenia, obsługi i usuwania tożsamości użytkowników w aplikacjach SaaS, takich jak Dropbox, Salesforce, ServiceNow i nie tylko. Do lipca 2018 dodano obsługę następujących aplikacji w galerii aplikacji Azure AD aprowizacji użytkowników:

- [Cisco WebEx](https://docs.microsoft.com/azure/active-directory/saas-apps/cisco-webex-provisioning-tutorial)

- [Dodatkowe](https://docs.microsoft.com/azure/active-directory/saas-apps/bonusly-provisioning-tutorial)

Aby uzyskać listę wszystkich aplikacji, które obsługują Inicjowanie obsługi użytkowników w galerii usługi Azure AD, zobacz [SaaS Application Integration with Azure Active Directory](https://aka.ms/appstutorial).

---

### <a name="connect-health-for-sync---an-easier-way-to-fix-orphaned-and-duplicate-attribute-sync-errors"></a>Connect Health do celów synchronizacji — łatwiejszy sposób naprawić błędy synchronizacji oddzielone i zduplikowanego atrybutu

**Typ:** Nowa funkcja  
**Kategoria usługi:** Połączenie AD  
**Możliwość produktu:** Monitorowanie & raportowania
 
Program Azure AD Connect Health wprowadza samodzielne rozwiązanie problemu, ułatwiające wyróżnić i naprawić błędy synchronizacji. Ta funkcja rozwiązuje błędy synchronizacji zduplikowanym atrybutem i poprawki obiektów, które są oddzielone od usługi Azure AD. Diagnostyka to ma następujące zalety:

- Zawęża zduplikowanym atrybutem błędy synchronizacji, zapewniając określonej poprawki

- Stosuje poprawki dla scenariuszy usługi Azure AD, naprawianie błędów w jednym kroku w wersji dedykowanej

- Nie uaktualnienia lub konfiguracja jest wymagana do włączenia i korzystać z tej funkcji

Aby uzyskać więcej informacji, zobacz [diagnozowanie i korygowanie zduplikowanych błędów synchronizacji atrybutów](https://docs.microsoft.com/azure/active-directory/connect-health/active-directory-aadconnect-health-diagnose-sync-errors)

---

### <a name="visual-updates-to-the-azure-ad-and-msa-sign-in-experiences"></a>Visual aktualizacji do usługi Azure AD i logowania konta Microsoft napotyka

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Usługa Azure AD  
**Możliwość produktu:** Uwierzytelnianie użytkownika

Zaktualizowaliśmy interfejs użytkownika dla usług online firmy Microsoft środowiska logowania takich jak dla usługi Office 365 i Azure. Ta zmiana sprawia, że ekrany mniej zatłoczoną i bardziej bezpośredni. Aby uzyskać więcej informacji na temat tej zmiany, zobacz [nadchodzące ulepszenia blogu logowania do usługi Azure AD](https://cloudblogs.microsoft.com/enterprisemobility/2018/04/04/upcoming-improvements-to-the-azure-ad-sign-in-experience/) .

---

### <a name="new-release-of-azure-ad-connect---july-2018"></a>Nowa wersja programu Azure AD Connect — lipca 2018 r.

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Inicjowanie obsługi aplikacji  
**Możliwość produktu:** Zarządzanie cyklem życia tożsamości

Najnowsza wersja programu Azure AD Connect obejmuje: 

- Poprawki i aktualizacje obsługi 

- Ogólne udostępnienie usług integracji Federację Ping

- Aktualizacje do najnowszego klienta SQL 2012 

Aby uzyskać więcej informacji na temat tej aktualizacji, zobacz [Azure AD Connect: historia](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-version-history) wersji

---

### <a name="updates-to-the-terms-of-use-end-user-ui"></a>Aktualizacje warunków użytkowania użytkownika końcowego

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Warunki użytkowania  
**Możliwość produktu:** Dobrego

Trwa aktualizowanie ciągu akceptacji w Interfejsie użytkownika końcowego warunków użytkowania.

**Bieżący tekst.** Aby uzyskać dostęp do zasobów [tenantName], musisz zaakceptować warunki użytkowania.<br>**Nowy tekst.** Aby uzyskać dostęp do zasobów [tenantName], możesz przeczytać warunki użytkowania.

**Bieżący tekst:** Wybranie opcji Akceptuj oznacza, że akceptujesz wszystkie powyższe warunki użytkowania.<br>**Nowy tekst:** Kliknij przycisk Akceptuj, aby potwierdzić, że znasz i rozumiesz warunki użytkowania.

---
 
### <a name="pass-through-authentication-supports-legacy-protocols-and-applications"></a>Uwierzytelnianie przekazywane obsługuje starsze protokoły i aplikacje

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Uwierzytelnienia (nazwy logowania)  
**Możliwość produktu:** Uwierzytelnianie użytkownika
 
Uwierzytelnianie przekazywane teraz obsługuje starszych protokołów i aplikacji. Następujące ograniczenia są teraz w pełni obsługiwane:

- Logowania użytkowników do starszych aplikacji klienckich pakietu Office, pakietu Office 2010 i Office 2013 bez nowoczesnego uwierzytelniania.

- Dostęp do udostępnianie kalendarza i bezpłatne zajęty informacji w programie Exchange środowisk o hybrydowych w pakiecie Office 2010 tylko.

- Logowania użytkowników do usługi Skype dla firm aplikacji klienckich bez nowoczesnego uwierzytelniania.

- Użytkownik logowania do programu PowerShell w wersji 1.0.

- Programu Apple Device Enrollment Program (DEP) firmy Apple, przy użyciu Asystenta ustawień systemu iOS. 

---
 
### <a name="converged-security-info-management-for-self-service-password-reset-and-multi-factor-authentication"></a>Zarządzanie zabezpieczeniami konwergentnej informacje samoobsługowego resetowania haseł i uwierzytelniania wieloskładnikowego

**Typ:** Nowa funkcja  
**Kategoria usługi:** SSPR  
**Możliwość produktu:** Uwierzytelnianie użytkownika

Ta nowa funkcja umożliwia użytkownikom zarządzanie zabezpieczające (na przykład, numer telefonu, adres e-mail, aplikacji mobilnej i tak dalej) do samoobsługowego resetowania haseł (SSPR) i usługi Multi-Factor Authentication (MFA) w jednym środowisku. Użytkownicy mają już nie można zarejestrować tego samego jako informacje zabezpieczające dla funkcji samoobsługowego resetowania HASEŁ i uwierzytelniania Wieloskładnikowego w dwóch różnych środowiskach. Nowe środowisko dotyczy również użytkowników, którzy mają funkcji samoobsługowego resetowania HASEŁ lub uwierzytelniania MFA.

Jeśli organizacja nie wymusza rejestracji MFA lub SSPR, użytkownicy mogą zarejestrować swoje informacje zabezpieczające za pomocą portalu **My Apps** . W tym miejscu użytkownicy mogą rejestrować żadnych metod włączone dla uwierzytelniania Wieloskładnikowego lub samoobsługowego resetowania HASEŁ. 

Jest to zgłoszenie zgody na uczestnictwo w publicznej wersji zapoznawczej. Administratorzy mogą włączyć nowe środowisko (w razie potrzeby) dla wybranej grupy użytkowników lub dla wszystkich użytkowników w dzierżawie.

---
 
### <a name="use-the-microsoft-authenticator-app-to-verify-your-identity-when-you-reset-your-password"></a>Korzystanie z aplikacji Microsoft Authenticator do zweryfikowania Twojej tożsamości podczas resetowania hasła

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** SSPR  
**Możliwość produktu:** Uwierzytelnianie użytkownika

Ta funkcja pozwala użytkowników innych niż administratorzy zweryfikować swoją tożsamość podczas resetowania hasła przy użyciu powiadomienia lub kodu z Microsoft Authenticator (lub dowolną inną aplikację). Po Administratorzy, Włącz ten samoobsługowego resetowania haseł — metoda, użytkownicy, którzy zarejestrowali się do aplikacji mobilnej za pośrednictwem aka.ms/mfasetup lub aka.ms/setupsecurityinfo można użyć aplikacji mobilnej jako metody weryfikacji podczas resetowania hasła.

Tylko powiadomienia aplikacji mobilnej może zostać włączona w ramach zasad, które wymagają dwóch metod, aby zresetować hasło.

---

## <a name="june-2018"></a>Czerwiec 2018 r.

### <a name="change-notice-security-fix-to-the-delegated-authorization-flow-for-apps-using-azure-ad-activity-logs-api"></a>Zmień powiadomienie: poprawki zabezpieczeń, aby przepływ delegowanej autoryzacji dla aplikacji przy użyciu interfejsu API usługi Azure AD działania dzienniki

**Typ:** Planowanie zmiany  
**Kategoria usługi:** Reporting  
**Możliwość produktu:** Monitorowanie & raportowania

Ze względu na nasze silniejsze Wymuszanie zabezpieczeń musiałeś wprowadzić zmianę uprawnień dla aplikacji, które korzystają z delegowanego przepływu autoryzacji w celu uzyskiwania dostępu do [interfejsów API dzienników aktywności usługi Azure AD](https://aka.ms/aadreportsapi). Ta zmiana będzie miała miejsce do **26 czerwca 2018**.

Wszelkie aplikacje użycie dziennika aktywności platformy Azure AD interfejsów API, wykonaj następujące kroki, aby upewnić się, że aplikacja nie zostanie przerwane po operacji zmiany.

**Aby zaktualizować uprawnienia aplikacji**

1. Zaloguj się do Azure Portal, wybierz pozycję **Azure Active Directory**, a następnie wybierz pozycję **rejestracje aplikacji**.
2. Wybierz aplikację korzystającą z interfejsu API dzienników aktywności usługi Azure AD, wybierz pozycję **Ustawienia**, wybierz pozycję **wymagane uprawnienia**, a następnie wybierz pozycję **Windows Azure Active Directory** API.
3. W obszarze **delegowane uprawnienia** w bloku **Włącz dostęp** zaznacz pole wyboru obok pozycji **Odczytaj dane katalogu** , a następnie wybierz pozycję **Zapisz**.
4. Wybierz pozycję **Udziel uprawnień**, a następnie wybierz pozycję **tak**.
    
    >[!Note]
    >Musi być administratorem globalnym, aby udzielić uprawnień do aplikacji.

Aby uzyskać więcej informacji, zobacz temat [udzielanie uprawnień](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-prerequisites-azure-portal#grant-permissions) w obszarze wymagania wstępne, aby uzyskać dostęp do artykułu interfejsu API raportowania usługi Azure AD.

---

### <a name="configure-tls-settings-to-connect-to-azure-ad-services-for-pci-dss-compliance"></a>Skonfiguruj ustawienia protokołu TLS, aby połączyć się z usługami Azure AD pod kątem zgodności ze standardami PCI DSS

**Typ:** Nowa funkcja  
**Kategoria usługi:** NIE DOTYCZY  
**Możliwość produktu:** Platformach

Zabezpieczeń TLS (Transport Layer) to protokół, który zapewnia integralność danych i poufności informacji między dwiema aplikacjami komunikujące się i protokół najczęściej wdrożonej zabezpieczeń używanych obecnie.

[Rada standardu zabezpieczeń PCI](https://www.pcisecuritystandards.org/) ustaliła, że wczesne wersje protokołu TLS i SSL (SSL) muszą zostać wyłączone na korzyść, aby włączyć nowe i bezpieczniejsze protokoły aplikacji, z zachowaniem zgodności rozpoczynającym się **30 czerwca 2018**. Ta zmiana oznacza, że jeśli łączenie się z usługami Azure AD i wymagają zgodności ze standardem PCI DSS, należy wyłączyć protokół TLS 1.0. Wiele wersji protokołu TLS są dostępne, ale protokołu TLS 1.2 jest najnowszą wersją dostępną dla usług Azure Active Directory. Firma Microsoft zdecydowanie zaleca się przejście bezpośrednio do protokołu TLS 1.2 dla kombinacji zarówno klient/serwer, jak i przeglądarką a serwerem.

Przestarzałe przeglądarki może nie obsługiwać nowszych wersji protokołu TLS, np. protokół TLS 1.2. Aby sprawdzić, które wersje protokołu TLS są obsługiwane przez przeglądarkę, przejdź do witryny [Qualyse SSL Labs](https://www.ssllabs.com/) i kliknij pozycję **Testuj przeglądarkę**. Firma Microsoft zaleca się uaktualnienie do najnowszej wersji przeglądarki sieci web i najlepiej włączyć tylko protokołu TLS 1.2.

**Aby włączyć protokół TLS 1,2 w przeglądarce**

- **Microsoft Edge i Internet Explorer (oba są ustawiane przy użyciu programu Internet Explorer)**

    1. Otwórz program Internet Explorer, wybierz pozycję **narzędzia** > **Opcje internetowe** > **Zaawansowane**.
    2. W obszarze **zabezpieczenia** wybierz opcję **użyj protokołu TLS 1,2**, a następnie wybierz przycisk **OK**.
    3. Zamknij wszystkie okna przeglądarki, a następnie ponownie uruchom program Internet Explorer. 

- **Google Chrome**

    1. Otwórz program Google Chrome, wpisz *Chrome://Settings/* na pasku adresu i naciśnij klawisz **Enter**.
    2. Rozwiń opcje **Zaawansowane** , przejdź do obszaru **system** , a następnie wybierz pozycję **Otwórz ustawienia serwera proxy**.
    3. W oknie **Właściwości internetowe** wybierz kartę **Zaawansowane** , przejdź do obszaru **zabezpieczenia** , wybierz opcję **Użyj protokołu TLS 1,2**, a następnie wybierz przycisk **OK**.
    4. Zamknij wszystkie okna przeglądarki i ponownie Google Chrome.

- **Mozilla Firefox**

    1. Otwórz przeglądarkę Firefox, wpisz *informacje: config* na pasku adresu, a następnie naciśnij klawisz **Enter**.
    2. Wyszukaj termin, *TLS*, a następnie wybierz wpis **Security. TLS. Version. Max** .
    3. Ustaw wartość na **3** , aby wymusić, aby przeglądarka korzystała z do wersji TLS 1,2, a następnie wybierz **przycisk OK**.

        >[!NOTE]
        >Firefox w wersji 60,0 obsługuje protokół TLS 1,3, więc można także ustawić wartość Security. TLS. Version. max na **4**.

    4. Zamknij wszystkie okna przeglądarki i ponownie Mozilla Firefox.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---june-2018"></a>Nowe aplikacje federacyjne dostępne w galerii aplikacji usługi Azure AD — czerwca 2018 r.

**Typ:** Nowa funkcja  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwości produktu:** integracja innej firmy
 
W czerwcu 2018 Dodaliśmy obsługę tych 15 nowe aplikacje z Federacją w galerii aplikacji:

[Skytap](https://docs.microsoft.com/azure/active-directory/active-directory-saas-skytap-tutorial), [rozliczanie muzyki](https://docs.microsoft.com/azure/active-directory/active-directory-saas-settlingmusic-tutorial), [obsługa tokenów SAML 1,1 — aplikacja biznesowa](https://docs.microsoft.com/azure/active-directory/active-directory-saas-saml-tutorial), [Nadstrój](https://docs.microsoft.com/azure/active-directory/active-directory-saas-supermood-tutorial), [autozadanie](https://docs.microsoft.com/azure/active-directory/active-directory-saas-autotaskendpointbackup-tutorial), [kopia zapasowa punktu końcowego](https://docs.microsoft.com/azure/active-directory/active-directory-saas-autotaskendpointbackup-tutorial), [sieci SkyHigh](https://docs.microsoft.com/azure/active-directory/active-directory-saas-skyhighnetworks-tutorial), Smartway2, [TONICDM](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tonicdm-tutorial), [Moconavi](https://docs.microsoft.com/azure/active-directory/active-directory-saas-moconavi-tutorial), [Zoho one](https://docs.microsoft.com/azure/active-directory/active-directory-saas-zohoone-tutorial), [lokalne, program SharePoint](https://docs.microsoft.com/azure/active-directory/active-directory-saas-sharepoint-on-premises-tutorial), [przewidzieć pakiet CX](https://docs.microsoft.com/azure/active-directory/active-directory-saas-foreseecxsuite-tutorial), [Vidyard](https://docs.microsoft.com/azure/active-directory/active-directory-saas-vidyard-tutorial), [ChronicX](https://docs.microsoft.com/azure/active-directory/active-directory-saas-chronicx-tutorial)

Aby uzyskać więcej informacji na temat aplikacji, zobacz [SaaS Application Integration with Azure Active Directory](https://aka.ms/appstutorial). Aby uzyskać więcej informacji na temat tworzenia listy aplikacji w galerii aplikacji usługi Azure AD, zobacz [Wyświetlanie listy aplikacji w galerii aplikacji Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing). 

---

### <a name="azure-ad-password-protection-is-available-in-public-preview"></a>Ochrony hasłem w usłudze Azure AD jest dostępna w publicznej wersji zapoznawczej

**Typ:** Nowa funkcja  
**Kategoria usługi:** Ochrona tożsamości  
**Możliwość produktu:** Uwierzytelnianie użytkownika

Użyj hasła usługi Azure AD ochrony, aby wyeliminować łatwe do odgadnięcia hasła ze środowiska. Eliminowanie haseł pomaga zmniejszyć ryzyko naruszenia zabezpieczeń z atak typu osłony hasła.

W szczególności ochrony haseł usługi Azure AD umożliwia:

- Ochronę kont w organizacji w usługi Azure AD i systemu Windows Server Active Directory (AD). 
- Zatrzymuje użytkownikom korzystanie z haseł na liście więcej niż 500 najczęściej używanych haseł, a ponad 1 milion znaków podstawienia odmiany tych haseł.
- Administrowanie ochrony haseł usługi Azure AD z jednej lokalizacji w portalu usługi Azure AD dla usługi Azure AD i AD systemu Windows Server dla lokalnego.

Aby uzyskać więcej informacji na temat ochrony hasłem w usłudze Azure AD, zobacz [eliminowanie nieprawidłowych haseł w organizacji](https://aka.ms/aadpasswordprotectiondocs).

---

### <a name="new-all-guests-conditional-access-policy-template-created-during-terms-of-use-creation"></a>Nowy szablon zasad dostępu warunkowego "Wszyscy Goście" utworzony podczas tworzenia warunków użytkowania

**Typ:** Nowa funkcja  
**Kategoria usługi:** Warunki użytkowania  
**Możliwość produktu:** Dobrego

Podczas tworzenia warunków użytkowania nowy szablon zasad dostępu warunkowego jest również tworzony dla "Wszyscy Goście" i "wszystkie aplikacje". Ten nowy szablon zasad mają zastosowanie nowo utworzony warunków użytkowania, co usprawnia tworzenie i wymuszanie procesu dla gości.

Aby uzyskać więcej informacji, zobacz [Azure Active Directory warunki użytkowania funkcji](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use).

---

### <a name="new-custom-conditional-access-policy-template-created-during-terms-of-use-creation"></a>Nowy "niestandardowy" szablon zasad dostępu warunkowego utworzony podczas tworzenia warunków użytkowania

**Typ:** Nowa funkcja  
**Kategoria usługi:** Warunki użytkowania  
**Możliwość produktu:** Dobrego

Podczas tworzenia warunków użytkowania tworzony jest również nowy "niestandardowy" szablon zasad dostępu warunkowego. Ten nowy szablon zasad umożliwia utworzenie warunków użytkowania, a następnie natychmiastowe przejście do bloku tworzenia zasad dostępu warunkowego, bez konieczności ręcznego nawigowania po portalu.

Aby uzyskać więcej informacji, zobacz [Azure Active Directory warunki użytkowania funkcji](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use).

---

### <a name="new-and-comprehensive-guidance-about-deploying-azure-multi-factor-authentication"></a>Nowe i wyczerpujące wskazówki dotyczące wdrażania usługi Azure Multi-Factor Authentication

**Typ:** Nowa funkcja  
**Kategoria usługi:** Różnych  
**Możliwość produktu:** Ochrona tożsamości & zabezpieczenia
 
Udostępniliśmy nowe wskazówki krok po kroku dotyczące wdrażania usługi Azure Multi-Factor Authentication (MFA) w Twojej organizacji.

Aby wyświetlić przewodnik wdrażania usługi MFA, przejdź do repozytorium [przewodniki wdrażania tożsamości](https://aka.ms/DeploymentPlans) w witrynie GitHub. Aby przekazać opinię na temat przewodników wdrażania, użyj [formularza Opinia dotycząca planu wdrożenia](https://aka.ms/deploymentplanfeedback). Jeśli masz jakieś pytania dotyczące przewodników wdrażania, skontaktuj się z nami pod adresem [IDGitDeploy](mailto:idgitdeploy@microsoft.com).

---

### <a name="azure-ad-delegated-app-management-roles-are-in-public-preview"></a>Usługa Azure AD delegowane Zarządzanie aplikacjami, które role są w publicznej wersji zapoznawczej

**Typ:** Nowa funkcja  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwość produktu:** Access Control

Administratorzy mogą teraz delegować zadania zarządzania aplikacją bez przypisywania roli administratora globalnego. Nowe role i funkcje są:

- **Nowe role administratorów usługi Azure AD:**

    - **Administrator aplikacji.** Daje możliwość zarządzać wszystkimi aspektami wszystkie aplikacje, w tym rejestracji, ustawień logowania jednokrotnego, przypisań aplikacji i licencjonowania, ustawienia serwera proxy aplikacji i zgody (z wyjątkiem sytuacji określonych zasobów usługi Azure AD).

    - **Administrator aplikacji w chmurze.** Przyznaje wszystkie możliwości administratora aplikacji, z wyjątkiem serwera proxy aplikacji, ponieważ nie zapewnia dostępu do lokalnego.

    - **Deweloper aplikacji.** Przyznaje możliwość tworzenia rejestracji aplikacji, nawet jeśli opcja **Zezwalaj użytkownikom na rejestrowanie aplikacji** jest wyłączona.

- **Własność (skonfiguruj rejestrację dla aplikacji i aplikację dla przedsiębiorstw, podobnie jak w przypadku procesu własności grupy:**
 
    - **Właściciel rejestracji aplikacji.** Daje możliwość zarządzać wszystkimi aspektami należących do rejestracji aplikacji, w tym manifest aplikacji i dodawanie dodatkowych właścicieli.

    - **Właściciel aplikacji przedsiębiorstwa.** Daje możliwość zarządzać wieloma aspektami aplikacji należących do przedsiębiorstwa, w tym ustawień logowania jednokrotnego, przypisań aplikacji i zgody (z wyjątkiem sytuacji określonych zasobów usługi Azure AD).

Aby uzyskać więcej informacji na temat publicznej wersji zapoznawczej, zobacz [role zarządzania aplikacjami delegowanymi usługi Azure AD są w publicznej wersji zapoznawczej](https://cloudblogs.microsoft.com/enterprisemobility/2018/06/13/hallelujah-azure-ad-delegated-application-management-roles-are-in-public-preview/) . blog. Aby uzyskać więcej informacji o rolach i uprawnieniach, zobacz [Przypisywanie ról administratorów w Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal).

---

## <a name="may-2018"></a>Maj 2018 r.

### <a name="expressroute-support-changes"></a>Zmiany pomocy technicznej usługi ExpressRoute

**Typ:** Planowanie zmiany  
**Kategoria usługi:** Uwierzytelnienia (nazwy logowania)  
**Możliwość produktu:** Platformach  

Oprogramowanie jako usługa, takich jak Azure Active Directory (Azure AD) są przeznaczone do najlepszej metody, przechodząc bezpośrednio przez Internet, bez korzystania z usługi ExpressRoute lub innych prywatnej tuneli sieci VPN. W związku z tym w dniu **1 sierpnia 2018**ExpressRoutemy obsługę usługi Azure AD przy użyciu publicznej komunikacji równorzędnej Azure i społeczności platformy Azure w komunikacji równorzędnej firmy Microsoft. Wszystkie usługi, wpływ tej zmiany może się okazać, Azure AD ruchu stopniowo przesunięcie z usługi ExpressRoute z Internetem.

Zmieniamy się obsłudze, Wiemy także, są nadal sytuacji, w których konieczne może być użyć to dedykowany zestaw obwodów dla ruchu uwierzytelniania. W związku z tym usługa Azure AD będzie obsługiwać ograniczenia zakresu adresów IP dla dzierżawcy przy użyciu usługi ExpressRoute i usług już na komunikacji równorzędnej firmy Microsoft ze społecznością "Innych usług Online pakietu Office 365". Jeśli dotyczy usług, ale wymagają usługi ExpressRoute, należy wykonać następujące czynności:

- **Jeśli korzystasz z publicznej komunikacji równorzędnej Azure.** Przejdź do komunikacji równorzędnej firmy Microsoft i zarejestruj się, aby skorzystać z **innych społeczności usługi Office 365 Online Services (12076:5100)** . Aby uzyskać więcej informacji na temat przenoszenia z publicznej komunikacji równorzędnej Azure do komunikacji równorzędnej firmy Microsoft, zobacz artykuł [przenoszenie publicznej komunikacji równorzędnej do firmy Microsoft](https://docs.microsoft.com/azure/expressroute/how-to-move-peering) .

- **Jeśli używasz komunikacji równorzędnej firmy Microsoft.** Utwórz konto w **innej społeczności usługi Office 365 Online Service (12076:5100)** . Więcej informacji o wymaganiach dotyczących routingu znajduje się w [sekcji Obsługa Wspólnot protokołu BGP](https://docs.microsoft.com/azure/expressroute/expressroute-routing#bgp) w artykule wymagania dotyczące routingu ExpressRoute.

Jeśli musisz nadal używać dedykowanych obwodów, musisz skontaktować się z zespołem ds. kont Microsoft, aby uzyskać autoryzację do korzystania z **innej społeczności usługi Office 365 Online Service (12076:5100)** . Tablica zarządzana Office przeglądu MS sprawdzi, czy potrzebujesz tych obwodów i upewnij się, że rozumiesz implikacje techniczne ich zachowania. Subskrypcje nieautoryzowane próby utworzenia filtrów tras dla usługi Office 365 będą otrzymywać komunikat o błędzie. 
 
---

### <a name="microsoft-graph-apis-for-administrative-scenarios-for-tou"></a>Interfejsy API programu Graph firmy Microsoft dla scenariuszy administracyjnych dla warunków użytkowania

**Typ:** Nowa funkcja  
**Kategoria usługi:** Warunki użytkowania  
**Możliwość produktu:** Środowisko deweloperskie
 
Dodaliśmy Microsoft Graph interfejsów API dla operacji administrowania warunkami użytkowania usługi Azure AD. Możesz tworzyć, aktualizować i usuwać obiekty warunków użytkowania.

---

### <a name="add-azure-ad-multi-tenant-endpoint-as-an-identity-provider-in-azure-ad-b2c"></a>Dodaj punkt końcowy z wieloma dzierżawami usługi Azure AD jako dostawcy tożsamości w usłudze Azure AD B2C

**Typ:** Nowa funkcja  
**Kategoria usługi:** B2C — Zarządzanie tożsamościami konsumentów  
**Możliwość produktu:** B2B/B2C
 
Za pomocą zasad niestandardowych, możesz teraz dodać wspólnego punktu końcowego usługi Azure AD jako dostawcy tożsamości w usłudze Azure AD B2C. Umożliwia to pojedynczy punkt wejścia dla wszystkich użytkowników usługi Azure AD, którzy logują się do aplikacji. Aby uzyskać więcej informacji, zobacz [Azure Active Directory B2C: Zezwalanie użytkownikom na logowanie się do dostawcy tożsamości usługi Azure AD z wieloma dzierżawcami przy użyciu zasad niestandardowych](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-setup-commonaad-custom).

---

### <a name="use-internal-urls-to-access-apps-from-anywhere-with-our-my-apps-sign-in-extension-and-the-azure-ad-application-proxy"></a>Wewnętrzne adresy URL umożliwia dostęp do aplikacji z dowolnego miejsca za pomocą naszego rozszerzenia Moje aplikacje logowania i serwera Proxy aplikacji usługi AD systemu Azure

**Typ:** Nowa funkcja  
**Kategoria usługi:** Moje aplikacje  
**Możliwość produktu:** ZWRÓCIŁ
 
Użytkownicy mogą teraz uzyskiwać dostęp do aplikacji za pomocą wewnętrznych adresów URL nawet spoza sieci firmowej przy użyciu bezpiecznego Moje aplikacje logowania rozszerzenia dla usługi Azure AD. To będzie działać z dowolnej aplikacji, która została opublikowana przy użyciu serwera Proxy aplikacji usługi Azure AD, w dowolnej przeglądarce, która także ma zainstalowane rozszerzenie przeglądarki panelu dostępu. Funkcji Przekierowywanie adresu URL jest automatycznie włączone, gdy użytkownik loguje się do rozszerzenia. Rozszerzenie jest dostępne do pobrania w przeglądarkach [Microsoft Edge](https://go.microsoft.com/fwlink/?linkid=845176), [Chrome](https://go.microsoft.com/fwlink/?linkid=866367)i [Firefox](https://go.microsoft.com/fwlink/?linkid=866366).

---
 
### <a name="azure-active-directory---data-in-europe-for-europe-customers"></a>Usługa Azure Active Directory — dane w Europie klienci Europa

**Typ:** Nowa funkcja  
**Kategoria usługi:** Różnych  
**Możliwość produktu:** GoLocal

Klienci w Europie wymagają swoich danych, aby pozostać w Europie i nie są replikowane poza Europejskiego centrów danych na potrzeby spotkania i poufności informacji w europejskich przepisów. Ten [artykuł](https://go.microsoft.com/fwlink/?linkid=872328) zawiera szczegółowe informacje dotyczące informacji o tożsamościach, które będą przechowywane w Europie, a także informacje o informacjach, które będą przechowywane poza europejskimi centrami danych. 

---
 
### <a name="new-user-provisioning-saas-app-integrations---may-2018"></a>Nowy użytkownik inicjowania obsługi administracyjnej integracji aplikacji SaaS — maj 2018 r.

**Typ:** Nowa funkcja  
**Kategoria usługi:** Inicjowanie obsługi aplikacji  
**Możliwości produktu:** integracja innej firmy
 
Usługa Azure AD pozwala zautomatyzować procesy tworzenia, obsługi i usuwania tożsamości użytkowników w aplikacjach SaaS, takich jak Dropbox, Salesforce, ServiceNow i nie tylko. W maju 2018 r Dodaliśmy obsługę następujących aplikacji w galerii aplikacji Azure AD aprowizacji użytkowników:

- [BlueJeans](https://docs.microsoft.com/azure/active-directory/active-directory-saas-bluejeans-provisioning-tutorial)

- [OnDemand kamień](https://docs.microsoft.com/azure/active-directory/active-directory-saas-cornerstone-ondemand-provisioning-tutorial)

- [Systemu Zendesk](https://docs.microsoft.com/azure/active-directory/active-directory-saas-zendesk-provisioning-tutorial)

Aby uzyskać listę wszystkich aplikacji, które obsługują Inicjowanie obsługi użytkowników w galerii usługi Azure AD, zobacz [https://aka.ms/appstutorial](https://aka.ms/appstutorial).

---
 
### <a name="azure-ad-access-reviews-of-groups-and-app-access-now-provides-recurring-reviews"></a>Przeglądy dostępu usługi Azure AD, grup i przeglądy cyklicznego udostępnia teraz dostęp do aplikacji

**Typ:** Nowa funkcja  
**Kategoria usługi:** Przeglądy dostępu  
**Możliwość produktu:** Dobrego
 
Przegląd dostępu grup i aplikacji jest teraz ogólnie dostępna w ramach usługi Azure AD Premium P2.  Administratorzy, będzie można skonfigurować przeglądów dostępu członkostwa w grupach i przypisania aplikacji, aby automatycznie powtarzać w regularnych odstępach czasu, takich jak co miesiąc lub kwartalną.

---

### <a name="azure-ad-activity-logs-sign-ins-and-audit-are-now-available-through-ms-graph"></a>Dzienniki aktywności usługi AD platformy Azure (logowania i inspekcji) są teraz dostępne za pośrednictwem MS Graph

**Typ:** Nowa funkcja  
**Kategoria usługi:** Reporting  
**Możliwość produktu:** Monitorowanie & raportowania
 
Dzienniki aktywności usługi Azure AD, które obejmują logowania i dzienniki inspekcji, są teraz dostępne za pomocą interfejsu API Microsoft Graph. Dodaliśmy dwa punkty końcowe za pomocą interfejsu API Microsoft Graph, aby uzyskać dostęp do tych dzienników. Zapoznaj się z naszymi [dokumentami](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-getting-started-azure-portal) , aby uzyskać programistyczny dostęp do interfejsów API raportowania usługi Azure AD, aby rozpocząć pracę. 

---
 
### <a name="improvements-to-the-b2b-redemption-experience-and-leave-an-org"></a>Ulepszenia realizacji B2B środowisko i opuścić organizacji

**Typ:** Nowa funkcja  
**Kategoria usługi:** B2B  
**Możliwość produktu:** B2B/B2C

**Wykupu just in Time:** Po udostępnieniu zasobu użytkownikowi gość przy użyciu interfejsu API B2B — nie musisz wysyłać specjalnej wiadomości e-mail z zaproszeniem. W większości przypadków użytkownik-Gość można uzyskać dostępu do zasobu i zostaną podjęte przez środowisko realizacji dokładnie na czas. Nie wpływa na z powodu brakujących wiadomości e-mail. Nie ma więcej poproszenie użytkowników gości "Czy możesz kliknąć łącze realizacji wysłania systemu, możesz?". Oznacza to, gdy SPO korzysta z Menedżera zaproszenie — mętna załączniki mogą mieć ten sam Kanoniczny adres URL dla wszystkich użytkowników — wewnętrzne i zewnętrzne — w dowolnym stanie realizacji.

**Nowoczesne środowisko wykupu:** Nie ma więcej informacji na stronie początkowej dzielenia ekranu. Użytkownicy będą widzieć nowoczesny zgoda doświadczenia z organizacji zapraszającej zasady zachowania poufności informacji, podobnie jak w przypadku aplikacji innych firm.

**Użytkownicy-Goście mogą opuścić organizacji:** Gdy relacja użytkownika z organizacją zostanie przełączona, może ona samoobsługowo wychodzić z organizacji. Nie ma więcej wywoływania administratora organizacji zapraszającej o "usunięte", nie ma więcej wywoływanie biletami pomocy technicznej.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---may-2018"></a>Nowe aplikacje federacyjne dostępne w galerii aplikacji usługi Azure AD — maj 2018 r.

**Typ:** Nowa funkcja  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwości produktu:** integracja innej firmy
 
W maju 2018 Dodaliśmy obsługę tych 18 nowe aplikacje z Federacją do galerii aplikacji:

[AwardSpring](https://docs.microsoft.com/azure/active-directory/active-directory-saas-awardspring-tutorial), Infogix Data3Sixty, [Yodeck](https://docs.microsoft.com/azure/active-directory/active-directory-saas-infogix-tutorial), [Jamf Pro](https://docs.microsoft.com/azure/active-directory/active-directory-saas-jamfprosamlconnector-tutorial), [KnowledgeOwl](https://docs.microsoft.com/azure/active-directory/active-directory-saas-knowledgeowl-tutorial), [ENVI MMIS](https://docs.microsoft.com/azure/active-directory/active-directory-saas-envimmis-tutorial), [LaunchDarkly](https://docs.microsoft.com/azure/active-directory/active-directory-saas-launchdarkly-tutorial), [Adobe Captivateal](https://docs.microsoft.com/azure/active-directory/active-directory-saas-adobecaptivateprime-tutorial), [Montage online](https://docs.microsoft.com/azure/active-directory/active-directory-saas-montageonline-tutorial),[まなびポケット](https://docs.microsoft.com/azure/active-directory/active-directory-saas-manabipocket-tutorial), OpenReel, [Arc Publishing-SSO](https://docs.microsoft.com/azure/active-directory/active-directory-saas-arc-tutorial), [PlanGrid](https://docs.microsoft.com/azure/active-directory/active-directory-saas-plangrid-tutorial), [iWellnessNow](https://docs.microsoft.com/azure/active-directory/active-directory-saas-iwellnessnow-tutorial), [Proxyclick](https://docs.microsoft.com/azure/active-directory/active-directory-saas-proxyclick-tutorial), [potencjalnie szkodliwe oprogramowanie](https://docs.microsoft.com/azure/active-directory/active-directory-saas-riskware-tutorial), [stado](https://docs.microsoft.com/azure/active-directory/active-directory-saas-flock-tutorial), [ReviewSnap](https://docs.microsoft.com/azure/active-directory/active-directory-saas-reviewsnap-tutorial)

Aby uzyskać więcej informacji na temat aplikacji, zobacz [SaaS Application Integration with Azure Active Directory](https://aka.ms/appstutorial).

Aby uzyskać więcej informacji na temat tworzenia listy aplikacji w galerii aplikacji usługi Azure AD, zobacz [Wyświetlanie listy aplikacji w galerii aplikacji Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing).

---
 
### <a name="new-step-by-step-deployment-guides-for-azure-active-directory"></a>Nowe przewodniki krok po kroku wdrożenia usługi Azure Active Directory

**Typ:** Nowa funkcja  
**Kategoria usługi:** Różnych  
**Możliwość produktu:** Katalogi
 
Nowe wskazówki krok po kroku dotyczące sposobu wdrażania Azure Active Directory (Azure AD), w tym samoobsługowego resetowania hasła (SSPR), logowania jednokrotnego (SSO), dostępu warunkowego (CA), serwera proxy aplikacji, aprowizacji użytkowników, Active Directory Federation Services (ADFS) do programu Uwierzytelnianie przekazywane (PTA) i usługi ADFS do synchronizacji skrótów haseł (PHS).

Aby wyświetlić przewodniki wdrażania, przejdź do repozytorium [przewodniki wdrażania tożsamości](https://aka.ms/DeploymentPlans) w witrynie GitHub. Aby przekazać opinię na temat przewodników wdrażania, użyj [formularza Opinia dotycząca planu wdrożenia](https://aka.ms/deploymentplanfeedback). Jeśli masz jakieś pytania dotyczące przewodników wdrażania, skontaktuj się z nami pod adresem [IDGitDeploy](mailto:idgitdeploy@microsoft.com).

---

### <a name="enterprise-applications-search---load-more-apps"></a>Aplikacje dla przedsiębiorstw wyszukiwanie - ładowanie większej liczby aplikacji

**Typ:** Nowa funkcja  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwość produktu:** ZWRÓCIŁ
 
Problem ze znalezieniem informacji aplikacji / jednostki usług? Dodaliśmy możliwość ładowania większej liczby aplikacji w aplikacjach firmowych listę wszystkich aplikacji. Domyślnie pokazujemy 20 aplikacji. Teraz możesz kliknąć przycisk **Załaduj więcej** , aby wyświetlić dodatkowe aplikacje. 

---
 
### <a name="the-may-release-of-aadconnect-contains-a-public-preview-of-the-integration-with-pingfederate-important-security-updates-many-bug-fixes-and-new-great-new-troubleshooting-tools"></a>May wersji AADConnect zawiera publiczną wersję zapoznawczą integracji z serwerem PingFederate, ważne aktualizacje zabezpieczeń, wiele poprawek usterek i nowe great nowych narzędzi do rozwiązywania problemów. 

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Połączenie AD  
**Możliwość produktu:** Zarządzanie cyklem życia tożsamości
 
May wersji AADConnect zawiera publiczną wersję zapoznawczą integracji z serwerem PingFederate, ważne aktualizacje zabezpieczeń, wiele poprawek usterek i nowe great nowych narzędzi do rozwiązywania problemów. Informacje o wersji można znaleźć [tutaj](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-version-history#118190).

---

### <a name="azure-ad-access-reviews-auto-apply"></a>Przeglądy dostępu w usłudze Azure AD: automatycznie Zastosuj

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Przeglądy dostępu  
**Możliwość produktu:** Dobrego

Przeglądy dostępu dotyczące dostępu grupy i aplikacje są teraz ogólnie dostępne w ramach usługi Azure AD Premium P2. Administrator może skonfigurować, aby automatycznie zastosować zmiany wprowadzone przez recenzenta do tej grupy lub aplikacji, ponieważ zakończeniu przeglądu dostępu. Administrator może również określić, co się dzieje z przedłużenia dostępu użytkownika, jeśli recenzenci nie odpowie, spowodować usunięcie dostępu, móc chronić dostęp do lub rekomendacje systemu. 

---

### <a name="id-tokens-can-no-longer-be-returned-using-the-query-response_mode-for-new-apps"></a>Tokeny Identyfikatora już nie mogą być zwracane w przypadku nowych aplikacji przy użyciu response_mode zapytania. 

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Uwierzytelnienia (nazwy logowania)  
**Możliwość produktu:** Uwierzytelnianie użytkownika
 
Aplikacje utworzone w dniu lub po 25 kwietnia 2018 nie będą już mogły żądać **id_token** przy użyciu response_mode **zapytań** .  To umożliwia wbudowanej usługi Azure AD ze specyfikacjami OIDC, a także pomaga zmniejszyć obszar ataków aplikacji.  Aplikacje utworzone przed 25 kwietnia 2018 nie są blokowane przed użyciem **zapytania** response_mode z response_type **id_token**.  Zwrócony błąd podczas żądania id_token z usługi AAD jest **AADSTS70007: "Query" nie jest obsługiwaną wartością "response_mode" podczas żądania tokenu**.

**Fragmenty** i **form_post** response_modes nadal działały — podczas tworzenia nowych obiektów aplikacji (na przykład w przypadku użycia serwera proxy aplikacji) przed utworzeniem nowej aplikacji upewnij się, że używasz jednej z tych response_modes.  

---
 
## <a name="april-2018"></a>Kwiecień 2018 r. 

### <a name="azure-ad-b2c-access-token-are-ga"></a>Usługa Azure AD B2C Token dostępu są ogólnie dostępne

**Typ:** Nowa funkcja  
**Kategoria usługi:** B2C — Zarządzanie tożsamościami konsumentów  
**Możliwość produktu:** B2B/B2C 

Teraz możesz uzyskać dostęp do interfejsów API sieci Web zabezpieczony za pomocą usługi Azure AD B2C przy użyciu tokenów dostępu. Ta funkcja jest przenoszona z publicznej wersji zapoznawczej do GA. Udoskonalono środowisko interfejsu użytkownika do konfigurowania aplikacji usługi Azure AD B2C i interfejsów API sieci web i wprowadzono drobne ulepszenia.
 
Aby uzyskać więcej informacji, zobacz [Azure AD B2C: żądanie tokenów dostępu](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-access-tokens).

---

### <a name="test-single-sign-on-configuration-for-saml-based-applications"></a>Testowanie konfiguracji pojedynczego logowania jednokrotnego dla aplikacji opartej na SAML

**Typ:** Nowa funkcja  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwość produktu:** ZWRÓCIŁ

Konfigurowanie aplikacji opartej na SAML logowania jednokrotnego, możesz przetestować integrację na stronie konfiguracji. Jeśli wystąpi błąd podczas logowania, możesz dostarczyć błąd w środowisku testowania, a usługa Azure AD zapewnia czynności rozwiązujących problem do rozwiązania konkretnego problemu.

Aby uzyskać więcej informacji, zobacz:

- [Konfigurowanie logowania jednokrotnego do aplikacji, które nie znajdują się w galerii aplikacji Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-custom-apps)
- [Jak debugować Logowanie jednokrotne oparte na protokole SAML do aplikacji w Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-debugging)

---
 
### <a name="azure-ad-terms-of-use-now-has-per-user-reporting"></a>Warunki użytkowania usługi Azure AD mają teraz raportowanie na użytkownika

**Typ:** Nowa funkcja  
**Kategoria usługi:** Warunki użytkowania  
**Możliwość produktu:** Przepisów
 
Administratorzy mogą teraz wybrać danego warunków użytkowania i wyświetlić wszystkich użytkowników, które wyrażono zgodę na czy warunki użytkowania i co daty/godziny go miało miejsce.

Aby uzyskać więcej informacji, zobacz [Funkcja warunki użytkowania usługi Azure AD](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use).

---
 
### <a name="azure-ad-connect-health-risky-ip-for-ad-fs-extranet-lockout-protection"></a>Program Azure AD Connect Health: Ryzykownych adresów IP do ochrony blokowanie ekstranetu w usługach AD FS 

**Typ:** Nowa funkcja  
**Kategoria usługi:** Różnych  
**Możliwość produktu:** Monitorowanie & raportowania

Teraz obsługuje możliwość wykrywania adresów IP adresy, która przekracza próg nieudanych logowań U/P na podstawie godzinową lub dzienną programu Connect Health. Dostępne są następujące funkcje oferowane przez tę funkcję:

- Kompleksowy raport przedstawiający adres IP i liczbę nieudanych prób logowania generowane na podstawie godziny/dnia z progiem można dostosowywać.
- Alerty bazujące na poczcie e-mail, gdy określony adres IP został przekroczony próg nieudanych logowań U/P na podstawie godziny/dnia.
- Opcja pobierania w celu przeprowadzenia szczegółowej analizy danych

Aby uzyskać więcej informacji, zobacz [raport dotyczący ryzykownych adresów IP](https://aka.ms/aadchriskyip).

---
 
### <a name="easy-app-config-with-metadata-file-or-url"></a>Konfiguracja aplikacji łatwo z pliku metadanych lub adres URL

**Typ:** Nowa funkcja  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwość produktu:** ZWRÓCIŁ

Na stronie aplikacje przedsiębiorstwa administratorzy przekazać plik metadanych SAML do skonfigurowania logowania jednokrotnego SAML na podstawie aplikacji usługi AAD i spoza galerii.

Ponadto umożliwia adres URL metadanych Federacji aplikacji usługi Azure AD Konfigurowanie logowania jednokrotnego przy użyciu aplikacji docelowej.

Aby uzyskać więcej informacji, zobacz [Konfigurowanie logowania jednokrotnego do aplikacji, które nie znajdują się w galerii aplikacji Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-custom-apps).

---

### <a name="azure-ad-terms-of-use-now-generally-available"></a>Usługa Azure AD z warunkami użytkowania jest teraz ogólnie dostępna

**Typ:** Nowa funkcja  
**Kategoria usługi:** Warunki użytkowania  
**Możliwość produktu:** Przepisów
 

Warunki użytkowania usługi Azure AD zostały przeniesione z publicznej wersji zapoznawczej do ogólnie dostępnej.

Aby uzyskać więcej informacji, zobacz [Funkcja warunki użytkowania usługi Azure AD](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use).

---

### <a name="allow-or-block-invitations-to-b2b-users-from-specific-organizations"></a>Zezwalaj na zaproszenia lub blokowanie ich dla użytkowników B2B z określonym organizacjom

**Typ:** Nowa funkcja  
**Kategoria usługi:** B2B  
**Możliwość produktu:** B2B/B2C
 

Teraz można określić organizacji partnerskich, który ma być udostępnianie i współpraca przy użyciu we współpracy B2B usługi Azure AD. Aby to zrobić, możesz utworzyć listę konkretnych dozwolonych lub zablokowanych domen. Gdy domeny jest zablokowane, za pomocą tych funkcji, pracownikom nie może wysyłać zaproszenia do osób w danej domenie.

Dzięki temu można kontrolować dostęp do zasobów, umożliwiając bezproblemowe środowisko dla zatwierdzonych użytkowników jednocześnie.

Ta funkcja współpracy B2B jest dostępna dla wszystkich Azure Active Directory klientów i może być używana w połączeniu z funkcjami Azure AD — wersja Premium, takimi jak dostęp warunkowy i Ochrona tożsamości, aby uzyskać bardziej szczegółową kontrolę nad tym, kiedy i jak zewnętrznym użytkownikom biznesowym jest znak w programie i uzyskać dostęp.

Aby uzyskać więcej informacji, zobacz [Zezwalanie lub blokowanie zaproszeń użytkownikom B2B z określonych organizacji](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-allow-deny-list).

---
 
### <a name="new-federated-apps-available-in-azure-ad-app-gallery"></a>Nowe aplikacje federacyjne dostępne w galerii aplikacji usługi Azure AD

**Typ:** Nowa funkcja  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwości produktu:** integracja innej firmy

W kwietniu 2018 Dodaliśmy obsługę tych 13 nowe aplikacje z Federacją do galerii aplikacji:

Kryterium HCM, [FiscalNote](https://docs.microsoft.com/azure/active-directory/active-directory-saas-fiscalnote-tutorial), [Secret Server (on-premises)](https://docs.microsoft.com/azure/active-directory/active-directory-saas-secretserver-on-premises-tutorial), [sygnał dynamiczny](https://docs.microsoft.com/azure/active-directory/active-directory-saas-dynamicsignal-tutorial), [mindWireless](https://docs.microsoft.com/azure/active-directory/active-directory-saas-mindwireless-tutorial), [schemat organizacyjny teraz](https://docs.microsoft.com/azure/active-directory/active-directory-saas-orgchartnow-tutorial), [Ziflow](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ziflow-tutorial), [AppNeta Performance Monitor](https://docs.microsoft.com/azure/active-directory/active-directory-saas-appneta-tutorial), [Elium](https://docs.microsoft.com/azure/active-directory/active-directory-saas-elium-tutorial) , [FluxX Labs](https://docs.microsoft.com/azure/active-directory/active-directory-saas-fluxxlabs-tutorial), [Cisco Cloud](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ciscocloud-tutorial), półka i [SafetyNET](https://docs.microsoft.com/azure/active-directory/active-directory-saas-safetynet-tutorial)

Aby uzyskać więcej informacji na temat aplikacji, zobacz [SaaS Application Integration with Azure Active Directory](https://aka.ms/appstutorial).

Aby uzyskać więcej informacji na temat tworzenia listy aplikacji w galerii aplikacji usługi Azure AD, zobacz [Wyświetlanie listy aplikacji w galerii aplikacji Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing).

---
 
### <a name="grant-b2b-users-in-azure-ad-access-to-your-on-premises-applications-public-preview"></a>Użytkowników B2B przydział w usłudze Azure AD dostęp do aplikacji w środowisku lokalnym (publiczna wersja zapoznawcza)

**Typ:** Nowa funkcja  
**Kategoria usługi:** B2B  
**Możliwość produktu:** B2B/B2C

Jako organizacja, która używa funkcji współpracy B2B usługi Azure Active Directory (Azure AD), aby zaprosić użytkowników-gości z organizacji partnerskich do usługi Azure AD teraz możesz podać tych użytkowników B2B dostępu do aplikacji lokalnych. Te aplikacje w środowisku lokalnym za pomocą uwierzytelnianie oparte na protokole SAML lub zintegrowanego Windows Authentication (Zintegrowane) ograniczonego delegowania protokołu Kerberos (KCD).

Aby uzyskać więcej informacji, zobacz [udzielanie użytkownikom B2B w usłudze Azure AD dostępu do aplikacji lokalnych](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-hybrid-cloud-to-on-premises).

---
 
### <a name="get-sso-integration-tutorials-from-the-azure-marketplace"></a>Pobierz samouczki integracji logowania jednokrotnego w witrynie Azure Marketplace

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Różnych  
**Możliwości produktu:** integracja innej firmy

Jeśli aplikacja, która jest wymieniona w [portalu Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps?page=1) obsługuje logowanie jednokrotne oparte na protokole SAML, kliknij przycisk **Pobierz teraz** udostępnia samouczek integracji skojarzony z tą aplikacją. 

---

### <a name="faster-performance-of-azure-ad-automatic-user-provisioning-to-saas-applications"></a>Szybsza wydajność usługi Azure AD automatyczna aprowizacja użytkowników do aplikacji SaaS

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Inicjowanie obsługi aplikacji  
**Możliwości produktu:** integracja innej firmy
 
Wcześniej klienci korzystający z przypisywania łączników aplikacji SaaS (na przykład usługi Salesforce, ServiceNow i pole) użytkowników usługi Azure Active Directory mogą wystąpić spadek wydajności w przypadku ich dzierżawami usługi Azure AD zawiera ponad 100 000 użytkowników połączonych i grupy i ich były używane przypisań użytkowników i grup użytkowników, którzy powinny być aprowizowany.

2 kwietnia 2018 r. istotnie poprawiającą wydajność ulepszenia zostały wdrożone do inicjowania obsługi usługi Azure AD, która znacznie skrócić czas potrzebny do wykonania początkowej synchronizacji od usługi Azure Active Directory i aplikacji SaaS docelowej.

W rezultacie wielu klientów, miały synchronizacji początkowej do aplikacji, które miały liczbę dni lub nigdy nie zakończona, teraz są kończone w ciągu kilku minut lub godzin.

Aby uzyskać więcej informacji, zobacz [co się stanie w trakcie aprowizacji?](/azure//active-directory/app-provisioning/how-provisioning-works)

---

### <a name="self-service-password-reset-from-windows-10-lock-screen-for-hybrid-azure-ad-joined-machines"></a>Samodzielnie resetując hasło z ekranu blokady systemu Windows 10 do hybrydowej usługi Azure AD przyłączone maszyn

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Samoobsługowe resetowanie hasła  
**Możliwość produktu:** Uwierzytelnianie użytkownika
 
Zaktualizowaliśmy obejmuje pomocy technicznej dla maszyn, które są dołączone do usługi Azure AD hybrydowej funkcji samoobsługowego resetowania HASEŁ systemu Windows 10. Ta funkcja jest dostępna w systemie Windows 10 RS4 umożliwia użytkownikom do zresetowania swojego hasła na ekranie blokady komputera systemu Windows 10. Użytkownicy, którzy są włączone i zarejestrowany do samoobsługowego resetowania haseł mogą korzystać z tej funkcji.

Aby uzyskać więcej informacji, zobacz [Resetowanie hasła usługi Azure AD na ekranie logowania](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-sspr-windows).

---

## <a name="march-2018"></a>Marzec 2018 r.
 
### <a name="certificate-expire-notification"></a>Certyfikat wygasa powiadomień

**Typ:** FIXED  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwość produktu:** ZWRÓCIŁ
 
Usługa Azure AD wysyła powiadomienie, gdy certyfikat dla galerii lub aplikacji spoza galerii, wkrótce wygaśnie. 

Niektórzy użytkownicy nie otrzymał powiadomienia aplikacji dla przedsiębiorstw skonfigurowane dla opartej na SAML logowania jednokrotnego. Ten problem został rozwiązany. Usługa Azure AD wysyła powiadomienie w przypadku certyfikatów wygaśnie za 7, 30 i 60 dni. Jesteś w stanie wyświetlić to zdarzenie w dziennikach inspekcji. 

Aby uzyskać więcej informacji, zobacz:

- [Zarządzanie certyfikatami federacyjnego logowania jednokrotnego w Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-sso-certs)
- [Raporty dotyczące inspekcji w portalu Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-audit-logs)
 
---
 
### <a name="twitter-and-github-identity-providers-in-azure-ad-b2c"></a>Usługi Twitter i GitHub dostawców tożsamości w usłudze Azure AD B2C

**Typ:** Nowa funkcja  
**Kategoria usługi:** B2C — Zarządzanie tożsamościami konsumentów  
**Możliwość produktu:** B2B/B2C
 
Możesz teraz dodać Twitter lub GitHub jako dostawcy tożsamości w usłudze Azure AD B2C. Twitter jest przenoszona z publicznej wersji zapoznawczej do ogólnego udostępnienia. GitHub został wydany w publicznej wersji zapoznawczej.

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
 
### <a name="office-365-native-clients-are-supported-by-seamless-sso-using-a-non-interactive-protocol"></a>Natywne klienci usługi Office 365 są obsługiwane przez bezproblemowe logowanie Jednokrotne przy użyciu protokołu nieinterakcyjnym

**Typ:** Nowa funkcja  
**Kategoria usługi:** Uwierzytelnienia (nazwy logowania)  
**Możliwość produktu:** Uwierzytelnianie użytkownika
 
Użytkownika przy użyciu natywnych klientów usługi Office 365 (wersja 16.0.8730.xxxx i nowsze wersje) Pobierz dyskretnej środowisko logowania jednokrotnego przy użyciu bezproblemowego logowania jednokrotnego. Te są obsługiwane przez dodanie nieinterakcyjnych protokołu (WS-Trust) do usługi Azure AD.

Aby uzyskać więcej informacji, zobacz [Jak zalogować się na natywnym kliencie z bezproblemowym logowaniem JEDNOkrotnym?](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso-how-it-works#how-does-sign-in-on-a-native-client-with-seamless-sso-work)
 
---

### <a name="users-get-a-silent-sign-on-experience-with-seamless-sso-if-an-application-sends-sign-in-requests-to-azure-ads-tenant-endpoints"></a>Użytkownicy uzyskują dyskretnej logowania jednokrotnego, za pomocą bezproblemowego logowania jednokrotnego, jeśli aplikacja przesyła żądania rejestrowania do punktów końcowych dzierżawy usługi Azure AD

**Typ:** Nowa funkcja  
**Kategoria usługi:** Uwierzytelnienia (nazwy logowania)  
**Możliwość produktu:** Uwierzytelnianie użytkownika
 
Użytkownicy uzyskują ciche środowisko logowania za pomocą bezproblemowego logowania jednokrotnego, jeśli aplikacja (na przykład `https://contoso.sharepoint.com`) wysyła żądania logowania do punktów końcowych dzierżawy usługi Azure AD, czyli `https://login.microsoftonline.com/contoso.com/<..>` lub `https://login.microsoftonline.com/<tenant_ID>/<..>`-zamiast wspólnego punktu końcowego usługi Azure AD (`https://login.microsoftonline.com/common/<...>`).

Aby uzyskać więcej informacji, zobacz [Azure Active Directory bezproblemowe logowanie jednokrotne](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso). 

---
 
### <a name="need-to-add-only-one-azure-ad-url-instead-of-two-urls-previously-to-users-intranet-zone-settings-to-roll-out-seamless-sso"></a>Należy dodać tylko jeden adres URL usługi Azure AD, zamiast wcześniej dwa adresy URL do ustawienia strefy Intranet użytkowników to dystrybucję bezproblemowe logowanie Jednokrotne

**Typ:** Nowa funkcja  
**Kategoria usługi:** Uwierzytelnienia (nazwy logowania)  
**Możliwość produktu:** Uwierzytelnianie użytkownika
 
Aby zapewnić użytkownikom bezproblemowe logowanie jednokrotne, musisz dodać tylko jeden adres URL usługi Azure AD do ustawień strefy intranetowej użytkowników przy użyciu zasad grupy w Active Directory: `https://autologon.microsoftazuread-sso.com`. Wcześniej klienci musieli dodać dwa adresy URL.

Aby uzyskać więcej informacji, zobacz [Azure Active Directory bezproblemowe logowanie jednokrotne](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso). 
 
---
 
### <a name="new-federated-apps-available-in-azure-ad-app-gallery"></a>Nowe aplikacje federacyjne dostępne w galerii aplikacji usługi Azure AD

**Typ:** Nowa funkcja  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwości produktu:** integracja innej firmy

W marcu 2018 r. Dodaliśmy obsługę tych 15 nowe aplikacje z Federacją do galerii aplikacji:

[BoxCryptor](https://docs.microsoft.com/azure/active-directory/active-directory-saas-boxcryptor-tutorial), [CylancePROTECT](https://docs.microsoft.com/azure/active-directory/active-directory-saas-cylanceprotect-tutorial), Wrike, [SignalFx](https://docs.microsoft.com/azure/active-directory/active-directory-saas-signalfx-tutorial), Assistant przez FirstAgenda, [YardiOne](https://docs.microsoft.com/azure/active-directory/active-directory-saas-yardione-tutorial), vtiger CRM, inobrazkowe, [amplituda](https://docs.microsoft.com/azure/active-directory/active-directory-saas-amplitude-tutorial), [Spacio](https://docs.microsoft.com/azure/active-directory/active-directory-saas-spacio-tutorial), [ContractWorks](https://docs.microsoft.com/azure/active-directory/active-directory-saas-contractworks-tutorial), [Bersin](https://docs.microsoft.com/azure/active-directory/active-directory-saas-bersin-tutorial), [Mercell](https://docs.microsoft.com/azure/active-directory/active-directory-saas-mercell-tutorial), [Trisotech Digital Enterprise Server](https://docs.microsoft.com/azure/active-directory/active-directory-saas-trisotechdigitalenterpriseserver-tutorial), [Qumu Cloud](https://docs.microsoft.com/azure/active-directory/active-directory-saas-qumucloud-tutorial).
 
Aby uzyskać więcej informacji na temat aplikacji, zobacz [SaaS Application Integration with Azure Active Directory](https://aka.ms/appstutorial).

Aby uzyskać więcej informacji na temat tworzenia listy aplikacji w galerii aplikacji usługi Azure AD, zobacz [Wyświetlanie listy aplikacji w galerii aplikacji Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing). 

---
 
### <a name="pim-for-azure-resources-is-generally-available"></a>Usługa PIM dla zasobów platformy Azure jest ogólnie dostępna

**Typ:** Nowa funkcja  
**Kategoria usługi:** Privileged Identity Management  
**Możliwość produktu:** Privileged Identity Management
 
Korzystania z usługi Azure AD Privileged Identity Management dla ról w katalogu teraz umożliwia czasowo dostęp i możliwości przypisania w usłudze PIM dla ról zasobów platformy Azure, takich jak subskrypcji, grupy zasobów, maszyny wirtualne i innych zasobów, obsługiwane przez usługi Azure Resource Manager. Wymuszanie uwierzytelniania wieloskładnikowego podczas aktywacji ról Just-In-Time i Planuj aktywacje w połączeniu z systemem windows zatwierdzone zmiany. Ponadto w tej wersji dodano ulepszenia nie jest dostępna w publicznej wersji zapoznawczej, w tym zaktualizowanego interfejsu użytkownika, przepływy pracy zatwierdzania i możliwość rozszerzania role, które wkrótce wygasną i Odnów wygasłe role.

Aby uzyskać więcej informacji, zobacz temat [PIM dla zasobów platformy Azure (wersja zapoznawcza)](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/azure-pim-resource-rbac)
 
---
 
### <a name="adding-optional-claims-to-your-apps-tokens-public-preview"></a>Dodawanie opcjonalnych oświadczeń do tokenów aplikacji (publiczna wersja zapoznawcza)

**Typ:** Nowa funkcja  
**Kategoria usługi:** Uwierzytelnienia (nazwy logowania)  
**Możliwość produktu:** Uwierzytelnianie użytkownika
 
Aplikacja usługi Azure AD mogą teraz oświadczenia żądań, niestandardowych lub opcjonalnie tokenów Jwt lub SAML tokenów.  To są oświadczenia dotyczące użytkownika lub dzierżawy, które nie są uwzględnione w tokenie, z powodu ograniczeń rozmiaru lub stosowania domyślnie.  Ta funkcja jest obecnie w publicznej wersji zapoznawczej dla aplikacji usługi Azure AD w wersji 1.0 i 2.0 punktach końcowych.  Zobacz dokumentację, aby uzyskać informacje na oświadczeniach, jakie można dodać i jak edytować manifest aplikacji do ich zażądać.  

Aby uzyskać więcej informacji, zobacz [opcjonalne oświadczenia w usłudze Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-optional-claims).
 
---
 
### <a name="azure-ad-supports-pkce-for-more-secure-oauth-flows"></a>Usługa Azure AD obsługuje PKCE bardziej bezpieczne przepływy OAuth

**Typ:** Nowa funkcja  
**Kategoria usługi:** Uwierzytelnienia (nazwy logowania)  
**Możliwość produktu:** Uwierzytelnianie użytkownika
 
Dokumentacja usługi Azure AD zostały zaktualizowane należy pamiętać, obsługa PKCE, który pozwala na bardziej bezpiecznej komunikacji w przepływie przyznawania kodu autoryzacji usługi uwierzytelniania OAuth 2.0.  Code_challenges S256 i w postaci zwykłego tekstu są obsługiwane dla punktów końcowych w wersji 1.0 i 2.0. 

Aby uzyskać więcej informacji, zobacz [żądanie kodu autoryzacji](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-protocols-oauth-code#request-an-authorization-code). 
 
---
 
### <a name="support-for-provisioning-all-user-attribute-values-available-in-the-workday-get_workers-api"></a>Obsługa funkcji obsługi administracyjnej wszystkie wartości atrybutów użytkownika dostępny w interfejsie API Get_Workers Workday

**Typ:** Nowa funkcja  
**Kategoria usługi:** Inicjowanie obsługi aplikacji  
**Możliwości produktu:** integracja innej firmy
 
Publiczna wersja zapoznawcza aprowizacji dla ruchu przychodzącego z produktu Workday do usługi Active Directory i usługi Azure AD obecnie obsługuje możliwość wyodrębniania i aprowizacji w interfejsie API Get_Workers Workday wszystkie wartości atrybutów. Spowoduje to dodanie obsługuje setek dodatkowe standardowe i atrybutów niestandardowych, które są dostarczane z wstępną wersję produktu Workday wykraczających dla ruchu przychodzącego inicjowania obsługi administracyjnej łącznika.

Aby uzyskać więcej informacji, zobacz: [Dostosowywanie listy atrybutów użytkownika produktu Workday](https://docs.microsoft.com/azure/active-directory/active-directory-saas-workday-inbound-tutorial#customizing-the-list-of-workday-user-attributes)

---

### <a name="changing-group-membership-from-dynamic-to-static-and-vice-versa"></a>Zmiana członkostwa w grupie z dynamicznej na statyczną i na odwrót

**Typ:** Nowa funkcja  
**Kategoria usługi:** Zarządzanie grupami  
**Możliwość produktu:** Społeczności
 
Istnieje możliwość zmienić sposób zarządzania członkostwa w grupie. Jest to przydatne, gdy chcesz zachować taką samą nazwę grupy i identyfikator w systemie, dzięki czemu wszystkie istniejące odwołania do tej grupy są nadal ważne. Tworzenie nowej grupy wymaga aktualizacji te odwołania.
Zaktualizowaliśmy Centrum administracyjnego usługi AD Azure, aby obsługiwać tę funkcję. Teraz klienci mogą przekształcać istniejące grupy dynamiczne członkostwo przypisane członkostwo i na odwrót. Nadal dostępne są również istniejące polecenia cmdlet programu PowerShell.

Aby uzyskać więcej informacji, zobacz [dynamiczne reguły członkostwa dla grup w Azure Active Directory](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership)

---

### <a name="improved-sign-out-behavior-with-seamless-sso"></a>Ulepszone zachowanie wylogowania z bezproblemowe logowanie Jednokrotne

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Uwierzytelnienia (nazwy logowania)  
**Możliwość produktu:** Uwierzytelnianie użytkownika
 
Wcześniej nawet wtedy, gdy użytkownicy wylogowanie jawnie aplikacji zabezpieczonej przez usługi Azure AD, ich będzie automatycznie zalogować się ponownie przy użyciu bezproblemowe logowanie Jednokrotne gdy one próbował uzyskać dostęp do aplikacji usługi Azure AD, ponownie w ramach ich sieci corpnet za pomocą ich urządzeń przyłączonych do domeny. Dzięki tej zmianie Wyloguj jest obsługiwane.  Dzięki temu użytkownicy mogą wybrać tej samej lub innej usługi Azure AD konto do zalogowania Wstecz, a nie są automatycznie zalogować się przy użyciu bezproblemowe logowanie Jednokrotne.

Aby uzyskać więcej informacji, zobacz [Azure Active Directory bezproblemowe logowanie jednokrotne](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso)
 
---
 
### <a name="application-proxy-connector-version-154020-released"></a>Wersja łącznika serwera Proxy aplikacji 1.5.402.0 wydania

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Serwer proxy aplikacji  
**Możliwość produktu:** Ochrona tożsamości & zabezpieczenia
 
Ta wersja łącznika jest stopniowo wdrażana za pośrednictwem listopada. Nowa wersja łącznika obejmuje następujące zmiany:

- Łącznik teraz ustawia pliki cookie z poziomu domeny zamiast tego poziomu domeny podrzędnej. To zapewnia płynne logowania jednokrotnego i pozwala uniknąć monitów uwierzytelniania nadmiarowe.
- Obsługa żądań Kodowanie fragmentaryczne
- Monitorowanie kondycji łącznika ulepszone 
- Kilka poprawek błędów i ulepszenia w zakresie stabilności

Aby uzyskać więcej informacji, zobacz [Omówienie łączników usługi Azure serwer proxy aplikacji usługi Azure AD](https://docs.microsoft.com/azure/active-directory/application-proxy-understand-connectors).
 
---

## <a name="february-2018"></a>Luty 2018 r.
 
### <a name="improved-navigation-for-managing-users-and-groups"></a>Ulepszona nawigacja do zarządzania użytkownikami i grupami

**Typ:** Planowanie zmiany  
**Kategoria usługi:** Zarządzanie katalogami  
**Możliwość produktu:** Katalogi

Usprawniono środowisko nawigacji do zarządzania użytkownikami i grupami. Możesz teraz przechodzić od omówienia directory bezpośrednio do listy wszystkich użytkowników, których łatwiejszy dostęp do listy usuniętych użytkowników. Możesz także przejść od omówienia directory bezpośrednio do listy wszystkich grup, łatwiejszy dostęp do grupy zarządzania. A także na stronie Przegląd w katalogu, możesz wyszukać użytkowników, grupy, aplikacja dla przedsiębiorstw lub rejestracja aplikacji. 

---

### <a name="availability-of-sign-ins-and-audit-reports-in-microsoft-azure-operated-by-21vianet-azure-china-21vianet"></a>Dostępność logowania i inspekcji raporty w systemie Microsoft Azure, obsługiwana przez firmę 21Vianet (Azure China 21Vianet)

**Typ:** Nowa funkcja  
**Kategoria usługi:** Azure Stack  
**Możliwość produktu:** Monitorowanie & raportowania

Raporty dziennika usługi Azure AD Activity są teraz dostępne w systemie Microsoft Azure, świadczona przez firmę 21Vianet (Azure China 21Vianet) wystąpień. Uwzględnione są następujące dzienniki:

- **Dzienniki aktywności logowania** — obejmuje wszystkie dzienniki logowania skojarzone z dzierżawcą.

- **Dzienniki inspekcji hasła samoobsługowego** — obejmuje wszystkie dzienniki inspekcji SSPR.

- **Dzienniki inspekcji zarządzania katalogami** — obejmuje wszystkie dzienniki inspekcji związane z zarządzaniem katalogiem, takie jak zarządzanie użytkownikami, zarządzanie aplikacjami i inne.

Za pomocą tych dzienników możesz uzyskać szczegółowe informacje dotyczące sposobu działania środowiska. Na podstawie udostępnionych danych można:

- Określ, jak Twoje aplikacje i usługi są wykorzystywane przez użytkowników.

- Rozwiązywanie problemów, uniemożliwiając użytkownikom Rozpoczynanie pracy.

Aby uzyskać więcej informacji na temat korzystania z tych raportów, zobacz [Azure Active Directory raportowanie](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-azure-portal).

---

### <a name="use-report-reader-role-non-admin-role-to-view-azure-ad-activity-reports"></a>Użyj roli "Czytnik raportów" (rola nieadministracyjna), aby wyświetlić raporty aktywności usługi Azure AD

**Typ:** Nowa funkcja  
**Kategoria usługi:** Reporting  
**Możliwość produktu:** Monitorowanie & raportowania

Aby uzyskać dostęp do dzienników aktywności usługi Azure AD w ramach klientów, należy umożliwić użytkownikom należącym do roli "czytelnika raportu" dostęp do operacji logowania i inspekcji w ramach Azure Portal, jak również przy użyciu Microsoft Graph Interfejsu API. 

Aby uzyskać więcej informacji, jak korzystać z tych raportów, zobacz [Azure Active Directory raportowanie](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-azure-portal). 

---

### <a name="employeeid-claim-available-as-user-attribute-and-user-identifier"></a>Oświadczenie elementu EmployeeID dostępne jako atrybut użytkownika i identyfikator użytkownika

**Typ:** Nowa funkcja  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwość produktu:** ZWRÓCIŁ
 
Wartość **IDPracownika** można skonfigurować jako identyfikator użytkownika i atrybut użytkownika dla użytkowników należących do członków i gościa B2B w aplikacjach logowania opartych na języku SAML w interfejsie użytkownika aplikacji przedsiębiorstwa.

Aby uzyskać więcej informacji, zobacz [Dostosowywanie oświadczeń wystawionych w tokenie SAML dla aplikacji dla przedsiębiorstw w Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization).

---

### <a name="simplified-application-management-using-wildcards-in-azure-ad-application-proxy"></a>Uproszczone zarządzanie aplikacjami przy użyciu symboli wieloznacznych na serwerze Proxy aplikacji usługi Azure AD

**Typ:** Nowa funkcja  
**Kategoria usługi:** Serwer proxy aplikacji  
**Możliwość produktu:** Uwierzytelnianie użytkownika
 
Aby ułatwić wdrożenie aplikacji i zmniejszyć swoje koszty administracyjne, możemy teraz obsługują możliwość publikowania aplikacji przy użyciu symboli wieloznacznych. Aby opublikować aplikację symboli wieloznacznych, postępuj zgodnie z przepływem publikowania aplikacji standardowej ale użyć symbolu wieloznacznego w adresach URL wewnętrznych i zewnętrznych.

Aby uzyskać więcej informacji, zobacz [symbole wieloznaczne w Azure Active Directory serwerze proxy aplikacji](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-wildcard)

---

### <a name="new-cmdlets-to-support-configuration-of-application-proxy"></a>Nowe polecenia cmdlet obsługujące konfigurację serwera Proxy aplikacji

**Typ:** Nowa funkcja  
**Kategoria usługi:** Serwer proxy aplikacji  
**Możliwość produktu:** Platformach

Najnowszą wersję modułu usługi Azure AD PowerShell w wersji zapoznawczej zawiera nowe polecenia cmdlet, które umożliwiają klientom Konfigurowanie aplikacji serwera Proxy aplikacji za pomocą programu PowerShell.

Dostępne są następujące nowe polecenia cmdlet: 

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
 
### <a name="new-cmdlets-to-support-configuration-of-groups"></a>Nowe polecenia cmdlet obsługujące konfigurację grup

**Typ:** Nowa funkcja  
**Kategoria usługi:** Serwer proxy aplikacji  
**Możliwość produktu:** Platformach

Najnowsza wersja modułu AzureAD PowerShell zawiera polecenia cmdlet do zarządzania grupami w usłudze Azure AD. Te polecenia cmdlet były wcześniej dostępne w AzureADPreview module i zostały dodane do modułu usługi Azure AD

Dostępne są następujące polecenia cmdlet grupy, które są teraz udostępniony do powszechnego użytku: 

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
 
### <a name="a-new-release-of-azure-ad-connect-is-available"></a>Dostępna jest nowa wersja programu Azure AD Connect

**Typ:** Nowa funkcja  
**Kategoria usługi:** AD Sync  
**Możliwość produktu:** Platformach
 
Program Azure AD Connect jest preferowanym narzędziem do synchronizowania danych między usługami Azure AD i lokalnych źródeł danych, w tym usługi Active Directory systemu Windows Server i LDAP.

>[!Important]
>Ta kompilacja wprowadza schematu i synchronizacja zmian reguły. Usługa Azure AD Connect synchronizacji wyzwala pełny Import i pełną synchronizację kroków po uaktualnieniu. Aby uzyskać informacje na temat zmiany tego zachowania, zobacz [jak odroczyć pełną synchronizację po uaktualnieniu](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-upgrade-previous-version#how-to-defer-full-synchronization-after-upgrade).

Ta wersja ma następujące aktualizacje i zmiany:

**Rozwiązano problemy**

- Usuń okna czasowego na zadania w tle dla strony filtrowanie partycji, podczas przełączania do następnej strony.

- Naprawiono usterkę, który spowodował naruszenie zasad dostępu podczas ConfigDB akcji niestandardowej.

- Usunięto usterkę do odzyskania z limit czasu połączenia sql.

- Usunięto usterkę awarię certyfikatów z symbolami wieloznacznymi SAN kontrola wymagań wstępnych.

- Naprawiono usterkę, która powoduje awarię miiserver.exe podczas eksportowania do łącznika usługi AAD.

- Usunięto usterkę, w której próba nieprawidłowe hasło, rejestrowane na kontrolerze domeny, gdy działa spowodowane usługi AAD connect kreatora, aby zmienić konfigurację

**Nowe funkcje i ulepszenia**
 
- Telemetria usługi Application - administratorów można przełączyć tej klasy danych wł. / wył.

- Danych kondycji usługi AD platformy Azure — Administratorzy, należy odwiedzić portal kondycji do kontrolowania ustawień ich kondycji. Po zmianie zasad usługi agentami odczytu, a jej wymusić.

- Dodawany akcje konfiguracji funkcji zapisywania zwrotnego urządzeń, a pasek postępu inicjowania strony.

- Ulepszona diagnostyka ogólnego z raportu w formacie HTML i zbierania pełnych danych w pliku ZIP tekstu / raport w formacie HTML.

- Poprawiono niezawodność automatycznego uaktualniania i dodać dodatkową telemetrię, aby upewnić się, że można ustalić kondycję serwera.

- Uprawnienia dostępne do kont uprzywilejowanych dla konta łącznika AD. W przypadku nowych instalacji Kreator ogranicza uprawnienia, które konta uprzywilejowane ma konta MSOL po utworzeniu konta MSOL. Zmiany dotyczą instalacji ekspresowej i instalacje niestandardowe przy użyciu automatycznego tworzenia konta.

- Zmienione przez Instalatora nie wymagają uprawnień administratora systemu w czysta instalacja programu AADConnect.

- Nowe narzędzie do rozwiązywania problemów z synchronizacją dla konkretnego obiektu. Obecnie narzędzie sprawdza, czy następujące czynności:

    - UserPrincipalName niezgodność między obiektów synchronizowanych użytkowników i konta użytkownika w dzierżawy usługi Azure AD.
  
    - Jeśli obiekt został odfiltrowany z synchronizacji z powodu filtrowanie domeny
  
    - Jeśli obiekt został odfiltrowany z synchronizacji z powodu jednostki organizacyjnej (OU), filtrowanie

- Nowe narzędzie do synchronizacji bieżąca wartość skrótu hasła przechowywane w lokalnej usłudze Active Directory dla określonego konta użytkownika. Narzędzie nie wymaga zmiany hasła. 

---
 
### <a name="applications-supporting-intune-app-protection-policies-added-for-use-with-azure-ad-application-based-conditional-access"></a>Aplikacje obsługujące zasady Intune App Protection dodane do użycia z dostępem warunkowym opartym na aplikacji usługi Azure AD

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Dostęp warunkowy  
**Możliwość produktu:** Ochrona tożsamości & zabezpieczenia

Dodaliśmy więcej aplikacji obsługujących dostęp warunkowy oparty na aplikacji. Teraz można uzyskać dostęp do usługi Office 365 i inne aplikacje podłączone usługi AD platformy Azure w chmurze przy użyciu tych zatwierdzonych aplikacji klienckich.

Następujące aplikacje zostaną dodane do końca lutego:

- Microsoft Power BI

- Microsoft Launcher

- Microsoft Invoicing

Aby uzyskać więcej informacji, zobacz:

- [Wymagania dotyczące zatwierdzonej aplikacji klienckiej](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-conditions#client-apps-preview)
- [Dostęp warunkowy oparty na aplikacji usługi Azure AD](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)

---

### <a name="terms-of-use-update-to-mobile-experience"></a>Warunki użytkowania aktualizacji do środowiska mobilnego 

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Warunki użytkowania  
**Możliwość produktu:** Przepisów

Kiedy są wyświetlane warunki użytkowania, możesz teraz kliknąć problemy z **wyświetlaniem? Kliknij tutaj**. Kliknięcie tego linku spowoduje otworzenie warunków użytkowania natywnie na urządzeniu. Niezależnie od tego, czy rozmiar czcionki w dokumencie lub rozmiar ekranu urządzenia możesz powiększać i czytać dokument zgodnie z potrzebami. 

---
 
## <a name="january-2018"></a>Styczeń 2018 r.
 
### <a name="new-federated-apps-available-in-azure-ad-app-gallery"></a>Nowe aplikacje federacyjne dostępne w galerii aplikacji usługi Azure AD 

**Typ:** Nowa funkcja  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwości produktu:** integracja innej firmy

Stycznia 2018 r. zostały dodane następujące nowe aplikacje z obsługą federacji, w galerii aplikacji:

[IBM OpenPages](https://go.microsoft.com/fwlink/?linkid=864698), [OneTrust Management Software](https://go.microsoft.com/fwlink/?linkid=861660), [Dealpath](https://go.microsoft.com/fwlink/?linkid=863526), [IriusRisk federacyjnego Directory i [wierność zalety](https://go.microsoft.com/fwlink/?linkid=864701).

Aby uzyskać więcej informacji na temat aplikacji, zobacz [SaaS Application Integration with Azure Active Directory](https://aka.ms/appstutorial).

Aby uzyskać więcej informacji na temat tworzenia listy aplikacji w galerii aplikacji usługi Azure AD, zobacz [Wyświetlanie listy aplikacji w galerii aplikacji Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing). 

---
 
### <a name="sign-in-with-additional-risk-detected"></a>Zaloguj się przy użyciu dodatkowych wykryto ryzyko

**Typ:** Nowa funkcja  
**Kategoria usługi:** Ochrona tożsamości  
**Możliwość produktu:** Ochrona tożsamości & zabezpieczenia

Szczegółowe informacje na temat wykrytego wykrywania ryzyka są powiązane z subskrypcją usługi Azure AD. Za pomocą wersji Azure AD Premium P2 otrzymasz najbardziej szczegółowe informacje na temat wszystkie wykrycia bazowego.

W przypadku wersji Azure AD — wersja Premium P1 wykryte wykrywania, które nie są objęte licencją, są wyświetlane w przypadku wykrycia ryzyka podczas logowania przy użyciu dodatkowego ryzyka.

Aby uzyskać więcej informacji, zobacz [Azure Active Directory wykrywania zagrożeń](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-risk-events).
 
---

### <a name="hide-office-365-applications-from-end-users-access-panels"></a>Ukryj aplikacje usługi Office 365 w panelach dostępu użytkownika końcowego

**Typ:** Nowa funkcja  
**Kategoria usługi:** Moje aplikacje  
**Możliwość produktu:** ZWRÓCIŁ

Możesz teraz lepiej zarządzać jak aplikacje usługi Office 365 widoczne na panelach dostępu użytkowników za pomocą nowego ustawienia użytkownika. Ta opcja jest przydatne w przypadku zmniejszenie liczby aplikacji w panelach dostępu użytkownika, jeśli chcesz tylko wyświetlić aplikacje pakietu Office w portalu usługi Office. Ustawienie znajduje się w **ustawieniach użytkownika** i ma etykietę, **Użytkownicy mogą widzieć tylko aplikacje pakietu Office 365 w portalu pakietu Office 365**.

Aby uzyskać więcej informacji, zobacz [ukrywanie aplikacji ze środowiska użytkownika w Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-hide-third-party-app).

---
 
### <a name="seamless-sign-into-apps-enabled-for-password-sso-directly-from-apps-url"></a>Bezproblemowe logowania do aplikacji objętych hasło logowania jednokrotnego, bezpośrednio z adres URL aplikacji 

**Typ:** Nowa funkcja  
**Kategoria usługi:** Moje aplikacje  
**Możliwość produktu:** ZWRÓCIŁ

Moje aplikacje rozszerzenia przeglądarki jest teraz dostępna za pośrednictwem wygodne narzędzie, które zapewnia pojedynczy znak Moje aplikacje na możliwość jako skrót w przeglądarce. Po zainstalowaniu przez użytkownika zostanie wyświetlona ikona typu "gofr" w przeglądarce, który zapewnia im szybki dostęp do aplikacji. Użytkownicy mogą korzystać z zalet:

- Możliwość bezpośrednio zalogować się do logowania jednokrotnego hasła opartego na aplikacji z poziomu strony logowania w aplikacji
- Uruchamianie dowolnej aplikacji przy użyciu funkcji szybkiego wyszukiwania
- Skróty do ostatnio używane aplikacje z rozszerzenia
- Rozszerzenie jest dostępne dla przeglądarki Microsoft Edge, Chrome i Firefox.
 
Aby uzyskać więcej informacji, zobacz [Moje aplikacje bezpieczne logowanie](../user-help/my-apps-portal-end-user-access.md#download-and-install-the-my-apps-secure-sign-in-extension).

---

### <a name="azure-ad-administration-experience-in-azure-classic-portal-has-been-retired"></a>Administrowanie usługami AD systemu Azure środowisko w klasycznym portalu Azure został wycofany.

**Typ:** Przestarzałe   
**Kategoria usługi:** Usługa Azure AD  
**Możliwość produktu:** Katalogi

Od 8 stycznia 2018 r. administracji usługi Azure AD został wycofany doświadczenie w klasycznej witrynie Azure portal. Odbyło się w połączeniu z procedury wycofywania w klasycznym portalu Azure, sam. W przyszłości należy użyć [Centrum administracyjnego usługi Azure AD](https://aad.portal.azure.com) dla całej administracji opartej na portalu w usłudze Azure AD.
 
---

### <a name="the-phonefactor-web-portal-has-been-retired"></a>W portalu sieci web PhoneFactor został wycofany.

**Typ:** Przestarzałe  
**Kategoria usługi:** Usługa Azure AD  
**Możliwość produktu:** Katalogi
 
Od 8 stycznia 2018 r. został wycofany z portalu sieci web PhoneFactor. Ten portal został użyty do administrowania serwerem MFA, ale te funkcje zostały przeniesione do witryny Azure portal pod adresem portal.azure.com. 

Konfiguracja usługi MFA znajduje się w lokalizacji: **Azure Active Directory \> serwera usługi MFA**
 
---
 
### <a name="deprecate-azure-ad-reports"></a>Wycofana raporty usługi Azure AD

**Typ:** Przestarzałe  
**Kategoria usługi:** Reporting  
**Możliwość produktu:** Zarządzanie cyklem życia tożsamości  


Ogólne udostępnienie nowej konsoli administrowanie usługą Azure Active Directory i nowe interfejsy API, które są teraz dostępne dla działań i zabezpieczeń, raportów, raportów interfejsów API w obszarze "/ reports" punktu końcowego została wycofana od końca 31 grudnia 2017 r.

**Co jest dostępne?**

W ramach przejścia do nowej konsoli administracyjnej udostępniliśmy 2 nowe interfejsy API do pobierania dzienników aktywności usługi Azure AD. Nowy zestaw interfejsów API zapewnia bardziej zaawansowane filtrowanie i sortowanie funkcje oprócz bardziej rozbudowane kontroli i działań logowania. Dostęp do danych dostępnych wcześniej za pomocą raportów zabezpieczeń można teraz uzyskać za pomocą interfejsu API wykrywania ryzyka ochrony tożsamości w Microsoft Graph.

Aby uzyskać więcej informacji, zobacz:

- [Wprowadzenie do interfejsu API raportowania Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-getting-started-azure-portal)

- [Wprowadzenie do Azure Active Directory Identity Protection i Microsoft Graph](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection-graph-getting-started)

---

## <a name="december-2017"></a>Grudnia 2017 r.

### <a name="terms-of-use-in-the-access-panel"></a>Warunki użytkowania w panelu dostępu

**Typ:** Nowa funkcja  
**Kategoria usługi:** Warunki użytkowania  
**Możliwość produktu:** Przepisów
 
Możesz teraz przejść do panelu dostępu i wyświetlanie warunków użytkowania, które zostały wcześniej zaakceptowane.

Wykonaj następujące kroki:

1. Przejdź do [portalu Moje aplikacje](https://myapps.microsoft.com)i zaloguj się.

2. W prawym górnym rogu wybierz swoją nazwę, a następnie z listy wybierz pozycję **profil** . 

3. W **profilu**wybierz pozycję **Przejrzyj warunki użytkowania**. 

4. Teraz można przejrzeć warunki użytkowania akceptowane. 

Aby uzyskać więcej informacji, zobacz [Funkcja warunki użytkowania usługi Azure AD (wersja zapoznawcza)](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use).
 
---
 
### <a name="new-azure-ad-sign-in-experience"></a>Nowe środowisko logowania dla usługi Azure AD

**Typ:** Nowa funkcja  
**Kategoria usługi:** Usługa Azure AD  
**Możliwość produktu:** Uwierzytelnianie użytkownika
 
Usługi Azure AD i systemu tożsamości konta Microsoft UI zostały zaprojektowane od nowa, aby udzielić użytkownikom spójny wygląd i zachowanie. Ponadto strony logowania usługi Azure AD umożliwia zbieranie informacji o nazwę użytkownika najpierw następuje poświadczeń na drugim ekranie.

Aby uzyskać więcej informacji, zobacz [nowe środowisko logowania do usługi Azure AD jest teraz dostępne w publicznej wersji zapoznawczej](https://cloudblogs.microsoft.com/enterprisemobility/2017/08/02/the-new-azure-ad-signin-experience-is-now-in-public-preview/).
 
---
 
### <a name="fewer-sign-in-prompts-a-new-keep-me-signed-in-experience-for-azure-ad-sign-in"></a>Mniejszą liczbę monitów logowania: nowe "nie wylogowuj mnie" Środowisko logowania w usłudze Azure AD

**Typ:** Nowa funkcja  
**Kategoria usługi:** Usługa Azure AD  
**Możliwość produktu:** Uwierzytelnianie użytkownika
 
Pole wyboru nie wylogowuj **mnie** na stronie logowania usługi Azure AD zostało zastąpione nowym monitem, który jest wyświetlany po pomyślnym uwierzytelnieniu. 

Jeśli **odpowie na** ten monit, usługa daje trwały token odświeżania. Takie zachowanie jest takie samo, jak w przypadku wybrania pola wyboru nie **wylogowuj mnie** w starym środowisku. W przypadku dzierżaw federacyjnych tego monitu pokazuje po pomyślnie przeprowadzić uwierzytelnienie przy użyciu usługi federacyjnej.

Aby uzyskać więcej informacji, zobacz [mniej monitów logowania: nowe środowisko "nie wylogowuj mnie" w usłudze Azure AD jest w wersji zapoznawczej](https://cloudblogs.microsoft.com/enterprisemobility/2017/09/19/fewer-login-prompts-the-new-keep-me-signed-in-experience-for-azure-ad-is-in-preview/). 

---

### <a name="add-configuration-to-require-the-terms-of-use-to-be-expanded-prior-to-accepting"></a>Dodaj konfigurację, aby wymagać warunków użytkowania przed akceptacją rozszerzona

**Typ:** Nowa funkcja  
**Kategoria usługi:** Warunki użytkowania  
**Możliwość produktu:** Przepisów
 
Opcja dla administratorów wymaga użytkowników rozwinięcia warunków użytkowania przed akceptacją postanowień.

Wybierz opcję **Włącz** lub **Wyłącz** , aby wymagać od użytkowników rozwinięcia warunków użytkowania. Ustawienie **on** wymaga od użytkowników wyświetlenia warunków użytkowania przed ich zaakceptowaniem.

Aby uzyskać więcej informacji, zobacz [Funkcja warunki użytkowania usługi Azure AD (wersja zapoznawcza)](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use).
 
---

### <a name="scoped-activation-for-eligible-role-assignments"></a>O określonym zakresie aktywacji dla kwalifikujących się przypisań ról

**Typ:** Nowa funkcja  
**Kategoria usługi:** Privileged Identity Management  
**Możliwość produktu:** Privileged Identity Management
 
Przypisania ról uprawnionych zasobów platformy Azure z mniej autonomię w zakresie od oryginalnej wartości domyślne przypisania aktywować, można użyć aktywacji o określonym zakresie. Przykładem jest, jeśli masz przypisaną jako właściciela subskrypcji w ramach dzierżawy. O określonym zakresie aktywacji można aktywować roli właściciela dla maksymalnie 5 zasobów znajdujących się w ramach subskrypcji (np. grupy zasobów i maszyn wirtualnych). Wyznaczanie zakresu proces aktywacji może zmniejszyć prawdopodobieństwo wykonywanie niepożądanych zmian do krytycznych zasobów platformy Azure.

Aby uzyskać więcej informacji, zobacz [co to jest Azure AD Privileged Identity Management?](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure).
 
---
 
### <a name="new-federated-apps-in-the-azure-ad-app-gallery"></a>Nowe aplikacje federacyjne w galerii aplikacji usługi Azure AD

**Typ:** Nowa funkcja  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwości produktu:** integracja innej firmy

W grudniu 2017 r. Dodaliśmy obsługę tych aplikacji z Federacją do galerii aplikacji:

[Accredible](https://go.microsoft.com/fwlink/?linkid=863523), Adobe Experience Manager, [Digital](https://go.microsoft.com/fwlink/?linkid=861685)websklepie, [Communifire](https://go.microsoft.com/fwlink/?linkid=861676) CybSafe, [FactSet](https://go.microsoft.com/fwlink/?linkid=863525), [Image działa](https://go.microsoft.com/fwlink/?linkid=863517), [mobi](https://go.microsoft.com/fwlink/?linkid=863521), [MobileIron Azure AD Integration](https://go.microsoft.com/fwlink/?linkid=858027), [Reflektive](https://go.microsoft.com/fwlink/?linkid=863518), [SAML SSO dla Bamboo, rezolucja GmbH](https://go.microsoft.com/fwlink/?linkid=863520), [Logowanie jednokrotne SAML dla BitBucket, rezolucja GmbH](https://go.microsoft.com/fwlink/?linkid=863519), [Vodeclic](https://go.microsoft.com/fwlink/?linkid=863522), WebHR, Zenegy Azure AD Integration.

Aby uzyskać więcej informacji na temat aplikacji, zobacz [SaaS Application Integration with Azure Active Directory](https://aka.ms/appstutorial).

Aby uzyskać więcej informacji na temat tworzenia listy aplikacji w galerii aplikacji usługi Azure AD, zobacz [Wyświetlanie listy aplikacji w galerii aplikacji Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing). 
 
---

### <a name="approval-workflows-for-azure-ad-directory-roles"></a>Przepływy pracy zatwierdzania dla ról katalogu usługi Azure AD

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Privileged Identity Management  
**Możliwość produktu:** Privileged Identity Management
 
Przepływ pracy zatwierdzania dla ról katalogu usługi Azure AD jest ogólnie dostępna.

Za pomocą przepływu pracy zatwierdzania Administratorzy ról uprzywilejowanych może wymagać członków roli kwalifikujących się do żądania aktywacji roli, zanim rozpoczną korzystanie z ról uprzywilejowanych. Wielu użytkowników i grup mogą być delegowane zatwierdzenia obowiązki. Członkowie roli kwalifikujących się otrzymywanie powiadomień o zakończeniu zatwierdzenia i ich rolą jest aktywny.

---
 
### <a name="pass-through-authentication-skype-for-business-support"></a>Uwierzytelnianie przekazywane: Skype dla firm pomocy technicznej

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Uwierzytelnienia (nazwy logowania)  
**Możliwość produktu:** Uwierzytelnianie użytkownika

Teraz uwierzytelniania przekazywanego obsługuje logowania użytkowników do usługi Skype dla firm aplikacji klienckich, które obsługują nowoczesnego uwierzytelniania, w tym w trybie online i hybrydowe topologie. 

Aby uzyskać więcej informacji, zobacz temat [topologie programu Skype dla firm obsługujące nowoczesne uwierzytelnianie](https://technet.microsoft.com/library/mt803262.aspx).
 
---

### <a name="updates-to-azure-ad-privileged-identity-management-for-azure-rbac-preview"></a>Aktualizacje usługi Azure AD Privileged Identity Management dla RBAC platformy Azure (wersja zapoznawcza)

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Privileged Identity Management  
**Możliwość produktu:** Privileged Identity Management
 
Za pomocą odświeżanie w publicznej wersji zapoznawczej usługi Azure AD Privileged Identity Management (PIM) based kontroli dostępu (RBAC) można teraz:

* Użyj wystarczający zakres administracji.
* Wymagaj zatwierdzenia do aktywowania role zasobów.
* Zaplanuj przyszłe aktywacji roli, która wymaga zatwierdzenia dla usługi Azure AD i ról RBAC platformy Azure.
 
Aby uzyskać więcej informacji, zobacz [Privileged Identity Management dla zasobów platformy Azure (wersja zapoznawcza)](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/azure-pim-resource-rbac).

---
 
## <a name="november-2017"></a>Listopada 2017 r.
 
### <a name="access-control-service-retirement"></a>Wycofanie usługi kontroli dostępu

**Typ:** Planowanie zmiany  
**Kategoria usługi:** Usługa Access Control  
**Możliwość produktu:** Usługa Access Control 

Usługi Azure Active Directory Access Control (znany także jako usługa Access Control) zostanie wycofana w późnym 2018 r. Więcej informacji, który zawiera szczegółowy harmonogram i wskazówek dotyczących migracji wysokiego poziomu będą dostępne w ciągu kilku następnych tygodni. Na tej stronie można zamieszczać komentarze, pytania dotyczące usługi kontroli dostępu, a członek zespołu będzie odbierać je.

---

### <a name="restrict-browser-access-to-the-intune-managed-browser"></a>Ogranicz dostęp za pomocą przeglądarki do usługi Intune Managed Browser 

**Typ:** Planowanie zmiany  
**Kategoria usługi:** Dostęp warunkowy  
**Możliwość produktu:** Bezpieczeństwo i Ochrona tożsamości

Możesz ograniczyć dostęp przeglądarki do usługi Office 365 i innych aplikacji w chmurze połączone usługi AD systemu Azure przy użyciu aplikacji Intune Managed Browser jako zatwierdzonej aplikacji. 

Teraz można skonfigurować następujący warunek dla dostępu warunkowego opartego na aplikacji:

**Aplikacje klienckie:** Przeglądarka

**Jaki jest wpływ zmiany?**

Już dziś dostęp jest zablokowany, korzystając z tego warunku. Po udostępnieniu wersji zapoznawczej dostęp będzie wymagać korzystanie z aplikacji programu managed browser. 

Poszukaj tej funkcji i dodatkowe informacje w nadchodzących o naszych blogach i wersji. 

Aby uzyskać więcej informacji, zobacz [dostęp warunkowy w usłudze Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal).
 
---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Nowe zatwierdzone aplikacje klienckie dla dostępu warunkowego opartego na aplikacji usługi Azure AD

**Typ:** Planowanie zmiany  
**Kategoria usługi:** Dostęp warunkowy  
**Możliwość produktu:** Bezpieczeństwo i Ochrona tożsamości

Następujące aplikacje znajdują się na liście [zatwierdzonych aplikacji klienckich](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-conditions#client-apps-preview):

- [Usługi kaizala firmy Microsoft](https://www.microsoft.com/garage/profiles/kaizala/)
- Microsoft StaffHub

Aby uzyskać więcej informacji, zobacz:

- [Wymagania dotyczące zatwierdzonej aplikacji klienckiej](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-conditions#client-apps-preview)
- [Dostęp warunkowy oparty na aplikacji usługi Azure AD](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)

---

### <a name="terms-of-use-support-for-multiple-languages"></a>Warunki użytkowania obsługują wiele języków

**Typ:** Nowa funkcja    
**Kategoria usługi:** Warunki użytkowania  
**Możliwość produktu:** Przepisów

Administratorzy mogą teraz utworzyć nowe warunki użytkowania, które zawierają wiele dokumentów PDF. Można oznaczyć tych dokumentów PDF z odpowiedniego języka. Użytkownicy otrzymują plik PDF z zgodny język, w oparciu o ich preferencje. W przypadku niezgodności, jest wyświetlany domyślny język.

---
 
### <a name="real-time-password-writeback-client-status"></a>Stan klienta zapisywania zwrotnego haseł w czasie rzeczywistym

**Typ:** Nowa funkcja  
**Kategoria usługi:** Samoobsługowe resetowanie hasła  
**Możliwość produktu:** Uwierzytelnianie użytkownika

Teraz można sprawdzić stan swojego klienta zapisywania zwrotnego haseł w środowisku lokalnym. Ta opcja jest dostępna w sekcji **integracja lokalna na** stronie [resetowania hasła](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/PasswordReset) . 

Jeśli występują problemy z połączeniem do środowiska lokalnego klienta zapisywania zwrotnego, zobaczysz komunikat o błędzie, który zapewnia:

- Informacje na temat Dlaczego nie można nawiązać połączenia z klientem zapisywania zwrotnego w środowisku lokalnym.
- Link do dokumentacji, która pomaga w rozwiązywaniu problemu. 

Aby uzyskać więcej informacji, zobacz [integracja lokalna](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-how-it-works#on-premises-integration).

---

### <a name="azure-ad-app-based-conditional-access"></a>Dostęp warunkowy oparty na aplikacji usługi Azure AD 
 
**Typ:** Nowa funkcja  
**Kategoria usługi:** Usługa Azure AD  
**Możliwość produktu:** Bezpieczeństwo i Ochrona tożsamości

Teraz możesz ograniczyć dostęp do pakietu Office 365 i innych aplikacji w chmurze połączonych z usługą Azure AD do [zatwierdzonych aplikacji klienckich](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-conditions#client-apps-preview) , które obsługują zasady ochrony aplikacji usługi Intune przy użyciu [dostępu warunkowego opartego na aplikacji w usłudze Azure AD](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access). Zasady ochrony aplikacji usługi Intune służą do konfigurowania i ochrony danych firmy na tych aplikacji klienckich.

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

### <a name="manage-azure-ad-devices-in-the-azure-portal"></a>Zarządzanie urządzeniami usługi Azure AD w witrynie Azure portal

**Typ:** Nowa funkcja  
**Kategoria usługi:** Rejestrowanie urządzeń i zarządzanie nimi  
**Możliwość produktu:** Bezpieczeństwo i Ochrona tożsamości

Teraz można znaleźć wszystkie swoje urządzenia podłączone do usługi Azure AD i działań związanych z urządzenia w jednym miejscu. Istnieje nowe środowisko Administracja i Zarządzanie tożsamościami urządzeń i ustawienia w witrynie Azure portal. W tej wersji można wykonywać następujące czynności:

- Wyświetl wszystkie urządzenia, które są dostępne dla dostępu warunkowego w usłudze Azure AD.
- Wyświetlanie właściwości, które zawierają hybrydowe Azure urządzeń dołączonych do usługi AD.
- Znajdź klucze funkcji BitLocker dla urządzeń przyłączonych do usługi AD systemu Azure, zarządzanie urządzeniem za pomocą usługi Intune i nie tylko.
- Zarządzaj ustawienia dotyczące urządzeń usługi Azure AD.

Aby uzyskać więcej informacji, zobacz [Zarządzanie urządzeniami przy użyciu Azure Portal](https://docs.microsoft.com/azure/active-directory/device-management-azure-portal).

---

### <a name="support-for-macos-as-a-device-platform-for-azure-ad-conditional-access"></a>Obsługa macOS jako platformy urządzeń dla dostępu warunkowego usługi Azure AD 

**Typ:** Nowa funkcja    
**Kategoria usługi:** Dostęp warunkowy  
**Możliwość produktu:** Bezpieczeństwo i Ochrona tożsamości 

Teraz można uwzględnić (lub wykluczyć) macOS jako warunek platformy urządzenia w zasadach dostępu warunkowego usługi Azure AD. Z dodatkiem z systemem macOS do obsługiwanych platform urządzeń możesz wykonywać następujące czynności:

- **Rejestrowanie urządzeń macOS i zarządzanie nimi za pomocą usługi Intune.** Podobnie jak w innych platform, takich jak systemy iOS i Android, aplikację portalu firmy jest dostępna dla systemu macOS ujednoliconego rejestracji. Nowa aplikacja portal firmy dla systemu macOS można użyć do rejestrowania urządzenia w usłudze Intune i zarejestrowanie go za pomocą usługi Azure AD.
- **Upewnij się, że urządzenia macOS są zgodne z zasadami zgodności organizacji zdefiniowanymi w usłudze Intune.** W usłudze Intune w witrynie Azure portal możesz teraz skonfigurować zasady zgodności dla urządzeń z systemem macOS. 
- **Ogranicz dostęp do aplikacji w usłudze Azure AD tylko do zgodnych urządzeń macOS.** Tworzenie zasad dostępu warunkowego ma macOS jako osobną opcję platformy urządzeń. Teraz można tworzyć zasady dostępu warunkowego dla macOS dla zestawu aplikacji dla konkretnych platform na platformie Azure.

Aby uzyskać więcej informacji, zobacz:

- [Tworzenie zasad zgodności urządzeń dla urządzeń z systemem macOS w usłudze Intune](https://aka.ms/macoscompliancepolicy)
- [Dostęp warunkowy w usłudze Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal)
 
---

### <a name="network-policy-server-extension-for-azure-multi-factor-authentication"></a>Rozszerzenie serwera zasady sieciowe dla usługi Azure Multi-Factor Authentication 

**Typ:** Nowa funkcja    
**Kategoria usługi:**  Uwierzytelnianie wieloskładnikowe  
**Możliwość produktu:** Uwierzytelnianie użytkownika

Rozszerzenie serwera zasad sieciowych dla usługi Azure Multi-Factor Authentication dodaje funkcje oparte na chmurze Multi-Factor Authentication do infrastruktury uwierzytelniania przy użyciu istniejących serwerów. Z rozszerzeniem serwera zasad sieciowych można dodać połączenie telefoniczne, wiadomość SMS lub weryfikacji aplikacji telefonicznej do istniejącego przepływu uwierzytelniania. Nie trzeba instalować, konfigurować i obsługiwać nowe serwery. 

To rozszerzenie został utworzony dla organizacji, które chcesz chronić połączenia sieci VPN bez wdrażania serwera usługi Azure Multi-Factor Authentication. Serwer zasad sieciowych, których rozszerzenie działa jako karty między RADIUS i oparte na chmurze usługi Azure Multi-Factor Authentication, aby zapewnić drugi składnik uwierzytelniania federacyjnego lub zsynchronizowanych użytkowników.

Aby uzyskać więcej informacji, zobacz [integrowanie istniejącej infrastruktury serwera zasad sieciowych z usługą Azure Multi-Factor Authentication](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-nps-extension).
 
---

### <a name="restore-or-permanently-remove-deleted-users"></a>Przywrócić lub usunąć trwale usunięto użytkowników

**Typ:** Nowa funkcja    
**Kategoria usługi:** Zarządzanie użytkownikami  
**Możliwość produktu:** Katalogi 

W Centrum administracyjnym usługi Azure AD możesz teraz:

- Przywracanie usuniętego użytkownika. 
- Trwałe usuwanie użytkownika.

**Aby wypróbować ten element:**

1. W centrum administracyjnym usługi Azure AD wybierz pozycję [Wszyscy użytkownicy](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/All) w sekcji **Zarządzanie** . 

2. Z listy **Pokaż** wybierz pozycję **ostatnio usunięci użytkownicy**. 

3. Wybierz co najmniej jeden niedawno usuniętych użytkowników i następnie przywrócić je lub trwale usunąć je.
 
---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Nowe zatwierdzone aplikacje klienckie dla dostępu warunkowego opartego na aplikacji usługi Azure AD
 
**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Dostęp warunkowy  
**Możliwość produktu:** Bezpieczeństwo i Ochrona tożsamości

Następujące aplikacje zostały dodane do listy [zatwierdzonych aplikacji klienckich](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-conditions#client-apps-preview):

- Microsoft Planner
- Azure Information Protection 

Aby uzyskać więcej informacji, zobacz:

- [Wymagania dotyczące zatwierdzonej aplikacji klienckiej](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-conditions#client-apps-preview)
- [Dostęp warunkowy oparty na aplikacji usługi Azure AD](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)

---

### <a name="use-or-between-controls-in-a-conditional-access-policy"></a>Używanie "OR" między kontrolkami w zasadach dostępu warunkowego 

**Typ:** Zmieniono funkcję    
**Kategoria usługi:** Dostęp warunkowy  
**Możliwość produktu:** Bezpieczeństwo i Ochrona tożsamości
 
Teraz można użyć opcji "OR" (wymaga jednej z wybranych kontrolek) do kontroli dostępu warunkowego. Ta funkcja służy do tworzenia zasad za pomocą "OR" między kontroli dostępu. Na przykład można użyć tej funkcji, aby utworzyć zasadę, która wymaga od użytkownika, zaloguj się przy użyciu usługi Multi-Factor Authentication "OR" włączenia zgodnego urządzenia.

Aby uzyskać więcej informacji, zobacz [mechanizmy kontroli dostępu warunkowego usługi Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-controls).
 
---

### <a name="aggregation-of-real-time-risk-detections"></a>Agregacja wykrycia ryzyka w czasie rzeczywistym

**Typ:** Zmieniono funkcję    
**Kategoria usługi:** Ochrona tożsamości  
**Możliwość produktu:** Bezpieczeństwo i Ochrona tożsamości

W Azure AD Identity Protection wszystkie wykrycia ryzyka w czasie rzeczywistym pochodzące z tego samego adresu IP w danym dniu są teraz agregowane dla każdego typu wykrywania ryzyka. Ta zmiana ogranicza ilość wykrytych wykryć ryzyka bez zmian w zabezpieczeniach użytkownika.

Podstawowe wykrywanie w czasie rzeczywistym działa w każdym razem, gdy użytkownik loguje się. Jeśli masz zasady zabezpieczeń ryzyka logowania równa uwierzytelniania wieloskładnikowego lub blokowanie dostępu, nadal jest wyzwalany podczas każdego ryzykowne logowania.
 
---
 
## <a name="october-2017"></a>Października 2017 r.

### <a name="deprecate-azure-ad-reports"></a>Wycofana raporty usługi Azure AD

**Typ:** Planowanie zmiany  
**Kategoria usługi:** Reporting  
**Możliwość produktu:** Zarządzanie cyklem życia tożsamości  

Azure portal udostępnia Ci:

- Nową konsolę administracyjną usługi Azure AD.
- Nowe interfejsy API dla raportów aktywności i zabezpieczeń.
 
Ze względu na te nowe możliwości raportu interfejsów API w ramach punktu końcowego/Reports zostały wycofane 10 grudnia 2017 r. 

---

### <a name="automatic-sign-in-field-detection"></a>Wykrywanie automatyczne pól logowania

**Typ:** FIXED   
**Kategoria usługi:** Moje aplikacje  
**Możliwość produktu:** Logowanie jednokrotne  

Usługa Azure AD obsługuje wykrywania automatycznego pól logowania dla aplikacji, które są renderowane pola nazwy i hasła użytkownika HTML. Te kroki są udokumentowane w [sposób automatycznego przechwytywania pól logowania dla aplikacji](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-password-single-sign-on-non-gallery-applications-problems#manually-capture-sign-in-fields-for-an-app). Tę możliwość można znaleźć, dodając aplikację *spoza galerii* na stronie aplikacje dla **przedsiębiorstw** w [Azure Portal](https://aad.portal.azure.com). Ponadto można skonfigurować tryb **logowania** jednokrotnego w tej nowej aplikacji do **logowania jednokrotnego opartego na hasłach**, wprowadzić adres URL sieci Web, a następnie zapisać stronę.
 
Ze względu na problem z usługą zostało tymczasowo wyłączyć tej funkcji. Problem został rozwiązany i wykrywania automatycznego pól logowania będą ponownie dostępne.

---

### <a name="new-multi-factor-authentication-features"></a>Nowe funkcje usługi Multi-Factor Authentication

**Typ:** Nowa funkcja  
**Kategoria usługi:** Uwierzytelnianie wieloskładnikowe  
**Możliwość produktu:** Bezpieczeństwo i Ochrona tożsamości  

Uwierzytelnianie wieloskładnikowe (MFA) jest integralną część ochrony Twojej organizacji. Aby bardziej adaptacyjnego poświadczenia i doświadczenia Bezproblemowa, zostały dodane następujące funkcje: 

- Wyniki żądania usługi Multi-Factor Authentication są bezpośrednio zintegrowane usługi Azure AD logowania w raporcie, który obejmuje programowy dostęp do usługi MFA wyników.
- Konfiguracja uwierzytelniania Wieloskładnikowego głębiej jest zintegrowana w konfiguracji usługi Azure AD środowisko w witrynie Azure portal.

W tej publicznej wersji zapoznawczej zarządzania usługi MFA i raportowania są zintegrowaną częścią środowisko konfiguracji podstawowej usługi Azure AD. Możesz teraz zarządzać funkcji portalu zarządzania usługi MFA w środowisku usługi Azure AD.

Aby uzyskać więcej informacji, zobacz temat [Informacje o raportowaniu usługi MFA w Azure Portal](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-sign-ins-mfa). 

---

### <a name="terms-of-use"></a>Warunki użytkowania

**Typ:** Nowa funkcja  
**Kategoria usługi:** Warunki użytkowania  
**Możliwość produktu:** Przepisów  

Możesz użyć usługi Azure AD warunki użytkowania przedstawiają informacje, takie jak zastrzeżenia istotne dla prawnych lub wymagań dotyczących zgodności dla użytkowników.

Warunki użytkowania usługi Azure AD można użyć w następujących scenariuszach:

- Ogólne warunki użytkowania dla wszystkich użytkowników w Twojej organizacji
- Specyficzne warunki użytkowania na podstawie atrybutów użytkownika (na przykład lekarzy a pielęgniarek) lub domowych, a pracownicy krajowi przez grupy dynamiczne
- Specyficzne warunki użytkowania do uzyskiwania dostępu do aplikacji biznesowych o dużym znaczeniu, takich jak Salesforce

Aby uzyskać więcej informacji, zobacz [warunki użytkowania usługi Azure AD](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use).

---

### <a name="enhancements-to-privileged-identity-management"></a>Ulepszenia Privileged Identity Management

**Typ:** Nowa funkcja  
**Kategoria usługi:** Privileged Identity Management  
**Możliwość produktu:** Privileged Identity Management  

Za pomocą usługi Azure AD Privileged Identity Management możesz zarządzanie, sterowanie i monitorowanie dostępu do zasobów platformy Azure (wersja zapoznawcza) w Twojej organizacji:

- Subskrypcje
- Grupy zasobów
- Maszyny wirtualne 

Wszystkie zasoby w witrynie Azure portal, korzystających z funkcji Azure RBAC korzystać z zalet zabezpieczeń i funkcji zarządzania cyklem życia, które usługi Azure AD Privileged Identity Management ma do zaoferowania.

Aby uzyskać więcej informacji, zobacz [Privileged Identity Management dla zasobów platformy Azure](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/azure-pim-resource-rbac).

---

### <a name="access-reviews"></a>Przeglądy dostępu

**Typ:** Nowa funkcja  
**Kategoria usługi:** Przeglądy dostępu  
**Możliwość produktu:** Przepisów  

Organizacje mogą używać przeglądów dostępu (wersja zapoznawcza) w celu efektywnego zarządzania członkostwa w grupach i dostępem do aplikacji dla przedsiębiorstw: 

- Przeprowadzając przeglądy dostępu, można ponownie certyfikować dostęp użytkowników-gości do aplikacji i ich członkostwo w grupach. Recenzenci mogą efektywnie decydować, czy gościom przedłużony dostępu na podstawie uzyskanych informacji dostarczonych przez przeglądów dostępu.
- Przeprowadzając przeglądy dostępu, można ponownie certyfikować dostęp do aplikacji i członkostwo w grupach pracowników.

Kontrole z przeglądem dostępu można łączyć w programy odpowiednie dla danej organizacji, aby śledzić przeglądy dla aplikacji dotyczących zgodności lub aplikacji narażonych na ryzyko.

Aby uzyskać więcej informacji, zobacz [przeglądy dostępu w usłudze Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-azure-ad-controls-access-reviews-overview).

---

### <a name="hide-third-party-applications-from-my-apps-and-the-office-365-app-launcher"></a>Ukrywanie aplikacji innych firm, które z moich aplikacji i uruchamianie aplikacji usługi Office 365

**Typ:** Nowa funkcja  
**Kategoria usługi:** Moje aplikacje  
**Możliwość produktu:** Logowanie jednokrotne  

Teraz można lepiej zarządzać aplikacjami, które są wyświetlane w portalach użytkowników, za pomocą nowej właściwości **Ukryj aplikację** . Można ukryć aplikacji, aby ułatwić w przypadkach, w którym kafelków aplikacji widoczne dla usług zaplecza lub zduplikowanych fragmentów i przyciski użytkowników zaśmiecania aplikacji Uruchom. Przełącznik znajduje się w sekcji **Właściwości** aplikacji innej firmy i jest oznaczony jako **widoczny dla użytkownika?** Można również ukryć aplikację programistycznie za pomocą programu PowerShell. 

Aby uzyskać więcej informacji, zobacz [ukrywanie aplikacji innych firm ze środowiska użytkownika w usłudze Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-hide-third-party-app). 


**Co jest dostępne?**

 W ramach przejścia na Nowa Konsola administracyjna dwa nowe interfejsy API do pobierania działania usługi Azure AD dostępnych dzienników. Nowy zestaw interfejsów API zapewnia bardziej zaawansowane filtrowanie i sortowanie funkcje oprócz bardziej rozbudowane kontroli i działań logowania. Dane, które wcześniej były dostępne za pomocą raportów zabezpieczeń, są teraz dostępne za pomocą interfejsu API wykrywania ryzyka ochrony tożsamości w Microsoft Graph.


## <a name="september-2017"></a>Września 2017 r.

### <a name="hotfix-for-identity-manager"></a>Poprawka programu Identity Manager

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Menedżer tożsamości  
**Możliwość produktu:** Zarządzanie cyklem życia tożsamości  

Pakiet zbiorczy poprawek (kompilacja 4.4.1642.0) jest dostępny od 25 września 2017 r. dla dodatku Service Pack 1 dla programu Identity Manager 2016. Ten pakiet zbiorczy:

- Rozwiązuje problemy, a następnie dodaje ulepszenia.
- Jest zbiorcza aktualizacja, która zastępuje wszystkie aktualizacje programu Identity Manager 2016 Service Pack 1 do kompilacji 4.4.1459.0 programu Identity Manager 2016. 
- Wymagane jest posiadanie programu Identity Manager 2016 4.4.1302.0 kompilacji. 

Aby uzyskać więcej informacji, zobacz [pakiet zbiorczy poprawek (build 4.4.1642.0) jest dostępny dla programu Identity Manager 2016 z dodatkiem Service Pack 1](https://support.microsoft.com/help/4021562). 

---
