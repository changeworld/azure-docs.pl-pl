---
title: Archiwum nowości? — Usługa azure Active Directory | Dokumentacja firmy Microsoft
description: Co to jest nowe informacje o wersji w ramach przeglądu sekcji tego zestawu zawartości zawiera 6 miesięcy działania. Po upływie 6 miesięcy elementy są usuwane z głównego artykułu i umieścić w tym artykule archiwum.
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 02/28/2019
ms.author: lizross
ms.reviewer: dhanyahk
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: f6fe7b2cefdd29f0497bbc18e969a77ac295ba42
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2019
ms.locfileid: "59051958"
---
# <a name="archive-for-whats-new-in-azure-active-directory"></a>Archiwum nowości? w usłudze Azure Active Directory

Podstawowy [co to jest nowe informacje o wersji](whats-new.md) artykuł zawiera najnowsze 6 miesięcy informacji, chociaż ten artykuł zawiera starsze dane.

Co to jest nowe informacje o wersji uzyskać informacje na temat:

- Zainstalowane najnowsze wersje
- Znane problemy
- Poprawki błędów
- Przestarzałe funkcje
- Plany dotyczące zmian

---

## <a name="august-2018"></a>Sierpień 2018 r.

### <a name="changes-to-azure-active-directory-ip-address-ranges"></a>Zmiany zakresów adresów IP usługi Azure Active Directory

**Typ:** Plan zmiany  
**Kategoria usług:** Inne  
**Możliwości produktu:** Platforma

Wprowadzamy większych zakresów adresów IP do usługi Azure AD, co oznacza, że jeśli skonfigurowano zakresów adresów IP usługi AD platformy Azure dla zapory, routery lub grupy zabezpieczeń sieci, należy je zaktualizować. Wprowadzamy aktualizację, dzięki czemu nie trzeba ponownie zmienić zapory, router lub konfiguracje zakresu adresów IP z grup zabezpieczeń sieci, gdy usługa Azure AD dodaje nowe punkty końcowe. 

Ruch sieciowy przechodzi do tych nowych zakresów w ciągu następnych dwóch miesięcy. Aby kontynuować nieprzerwane działanie usług, należy dodać te zaktualizowane wartości do adresów IP przed 10 września 2018 r.:

- 20.190.128.0/18 

- 40.126.0.0/18 

Zdecydowanie zaleca się nie usuwa starych zakresów adresów IP, dopóki wszystkie ruchu sieciowego została przeniesiona do nowego zakresu. Aktualizacje dotyczące przenoszenia i dowiedzieć się, usunięcie starych zakresów, zobacz [URL usługi Office 365 i zakresy adresów IP](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2).

---

### <a name="change-notice-authorization-codes-will-no-longer-be-available-for-reuse"></a>Powiadomienie o zmianie: Kody autoryzacji nie będzie już dostępny do ponownego wykorzystania 

**Typ:** Plan zmiany  
**Kategoria usług:** Uwierzytelnienia (logowania)  
**Możliwości produktu:** Uwierzytelnianie użytkowników

15 listopada 2018 r. usługi Azure AD będzie akceptować kody poprzednio używanych uwierzytelniania dla aplikacji. Ta zmiana zabezpieczeń pomaga do usługi Azure AD zgodnie ze specyfikacją protokołu OAuth i będzie wymuszany w punktach końcowych v1 i v2.

Jeśli aplikacja używa kody autoryzacji uzyskiwanie tokenów dla wielu zasobów, zalecamy Użyj kodu, aby uzyskać token odświeżania, a następnie używać tego tokenu odświeżania w celu pobrania dodatkowe tokeny dla innych zasobów. Kody autoryzacji należy używać tylko raz, ale tokenów odświeżania mogą być wielokrotnie używane w wielu zasobach. Aplikacja, która podejmuje próbę ponownego użycia kodu uwierzytelniania podczas przepływu kodu OAuth otrzymają komunikat o błędzie invalid_grant.

Ta i inne zmiany dotyczące protokołów, zobacz [pełną listę Nowości dla uwierzytelniania](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes).
 
---

### <a name="converged-security-info-management-for-self-service-password-sspr-and-multi-factor-authentication-mfa"></a>Zarządzanie zbieżnymi informacjami zabezpieczającymi na potrzeby samoobsługowego resetowania hasła i usługi Multi-Factor Authentication (MFA)

**Typ:** Nowa funkcja  
**Kategoria usług:** SSPR  
**Możliwości produktu:** Uwierzytelnianie użytkowników
 
Ta nowa funkcja ułatwia osób Zarządzanie swoich informacji zabezpieczających (na przykład, numer telefonu, aplikacja mobilna i tak dalej) dla funkcji samoobsługowego resetowania HASEŁ i uwierzytelniania MFA w jednej lokalizacji i doświadczenie; w porównaniu z wcześniej, gdzie to zostało zrobione w dwóch różnych lokalizacjach.

To środowisko konwergentnej działa także dla osób korzystających z funkcji samoobsługowego resetowania HASEŁ lub uwierzytelniania MFA. Ponadto jeśli Twoja organizacja nie wymusić rejestracji uwierzytelniania Wieloskładnikowego lub samoobsługowego resetowania HASEŁ, osoby nadal można zarejestrować żadnych uwierzytelniania Wieloskładnikowego lub samoobsługowego resetowania HASEŁ zabezpieczeń informacji metod dozwolona przez Twoją organizację, z poziomu portalu Moje aplikacje.

Jest to zgłoszenie zgody na uczestnictwo w publicznej wersji zapoznawczej. Administratorzy mogą włączyć nowe środowisko (w razie potrzeby) dla wybranej grupy lub dla wszystkich użytkowników w dzierżawie. Aby uzyskać więcej informacji na temat doświadczeń z osiągnięcia zbieżności zobacz [Konwergentne środowisko blog](https://cloudblogs.microsoft.com/enterprisemobility/2018/08/06/mfa-and-sspr-updates-now-in-public-preview/)

---

### <a name="new-http-only-cookies-setting-in-azure-ad-application-proxy-apps"></a>Nowe ustawienie Pliki cookie HTTP-Only w aplikacjach serwera proxy aplikacji usługi Azure AD

**Typ:** Nowa funkcja  
**Kategoria usług:** Serwer proxy aplikacji  
**Możliwości produktu:** Kontrola dostępu

Dostępne jest nowe ustawienie o nazwie, **plików cookie HTTP-Only** w aplikacjach serwer Proxy aplikacji. To ustawienie zapewnia dodatkowe zabezpieczenia, dołączając flagi HTTPOnly w nagłówku odpowiedzi HTTP, zarówno serwer Proxy aplikacji dostępu i sesji plików cookie, zatrzymanie dostępu do pliku cookie. pochodzące ze skryptu po stronie klienta i uniemożliwia dalsze akcje, takie jak kopiowanie lub Modyfikowanie pliku cookie. Chociaż ta flaga nie został wcześniej użyty, pliki cookie były zawsze szyfrowane, a następnie przesyłane przy użyciu połączenia SSL w celu zabezpieczenia przed Niepoprawne modyfikacje.

To ustawienie nie jest zgodna z aplikacjami korzystającymi z kontrolki ActiveX, takich jak pulpitu zdalnego. Jeśli jesteś w takiej sytuacji firma Microsoft zaleca, wyłącz to ustawienie.

Aby uzyskać więcej informacji na temat ustawień plików cookie HTTP-Only zobacz [publikowania aplikacji przy użyciu serwera Proxy aplikacji usługi Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-publish-azure-portal).

---

### <a name="privileged-identity-management-pim-for-azure-resources-supports-management-group-resource-types"></a>Usługa Privileged Identity Management (PIM) dla zasobów platformy Azure obsługuje typy zasobów grupy zarządzania

**Typ:** Nowa funkcja  
**Kategoria usług:** Privileged Identity Management  
**Możliwości produktu:** Privileged Identity Management
 
Ustawienia aktywacji i przypisania Just-In-Time można teraz stosować do typów zasobów grupy zarządzania, tak samo, jak już jak w przypadku subskrypcji, grupy zasobów i zasoby (takie jak maszyny wirtualne, App Services i inne). Ponadto każda osoba z rolą, która zapewnia dostęp administratora dla grupy zarządzania może odnajdywać i zarządzać tym zasobem w usłudze PIM.

Aby uzyskać więcej informacji dotyczących usługi PIM i zasobów platformy Azure, zobacz [odnajdywanie i zarządzanie zasobami platformy Azure przy użyciu Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-resource-roles-discover-resources)
 
---

### <a name="application-access-preview-provides-faster-access-to-the-azure-ad-portal"></a>Dostęp do aplikacji (wersja zapoznawcza) umożliwia szybszy dostęp do portalu usługi Azure AD

**Typ:** Nowa funkcja  
**Kategoria usług:** Privileged Identity Management  
**Możliwości produktu:** Privileged Identity Management
 
Obecnie podczas aktywacji roli przy użyciu usługi PIM, może potrwać ponad 10 minut uprawnień zastosować zmiany. Jeśli zdecydujesz się używać dostępu do aplikacji, która jest obecnie dostępna w publicznej wersji zapoznawczej, Administratorzy mogą uzyskiwać dostęp do portalu usługi Azure AD zaraz po zakończeniu żądania aktywacji.

Obecnie dostęp do aplikacji obsługuje tylko środowisko portalu usługi Azure AD i zasobów platformy Azure. Aby uzyskać więcej informacji na temat usługi PIM i dostęp do aplikacji, zobacz [co to jest usługa Azure AD Privileged Identity Management?](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure)
 
---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---august-2018"></a>Nowe aplikacje federacyjne dostępne w galerii aplikacji usługi Azure AD — sierpień 2018

**Typ:** Nowa funkcja  
**Kategoria usług:** Aplikacje dla przedsiębiorstw  
**Możliwości produktu:** Integracje innych firm
 
W sierpnia 2018 Dodaliśmy obsługę 16 nowych aplikacjach z Federacją w galerii aplikacji:

[Hornbill](https://docs.microsoft.com/azure/active-directory/saas-apps/hornbill-tutorial), [Bridgeline niepowiązanych](https://docs.microsoft.com/azure/active-directory/saas-apps/bridgelineunbound-tutorial), [sos Labs — Mobile i testowanie sieci Web](https://docs.microsoft.com/azure/active-directory/saas-apps/saucelabs-mobileandwebtesting-tutorial), [Meta sieci łącznika](https://docs.microsoft.com/azure/active-directory/saas-apps/metanetworksconnector-tutorial), [sposobów](https://docs.microsoft.com/azure/active-directory/saas-apps/waywedo-tutorial), [Spotinst](https://docs.microsoft.com/azure/active-directory/saas-apps/spotinst-tutorial), [ProMaster (przez Inlogik)](https://docs.microsoft.com/azure/active-directory/saas-apps/promaster-tutorial), SchoolBooking, [4me](https://docs.microsoft.com/azure/active-directory/saas-apps/4me-tutorial), [dokumentacji](https://docs.microsoft.com/azure/active-directory/saas-apps/DOSSIER-tutorial), [N2F - wydatków Raporty](https://docs.microsoft.com/azure/active-directory/saas-apps/n2f-expensereports-tutorial), [Czat na żywo Comm100](https://docs.microsoft.com/azure/active-directory/saas-apps/comm100livechat-tutorial), [SafeConnect](https://docs.microsoft.com/azure/active-directory/saas-apps/safeconnect-tutorial), [ZenQMS](https://docs.microsoft.com/azure/active-directory/saas-apps/zenqms-tutorial), [eLuminate](https://docs.microsoft.com/azure/active-directory/saas-apps/eluminate-tutorial), [ Dovetale](https://docs.microsoft.com/azure/active-directory/saas-apps/dovetale-tutorial).

Aby uzyskać więcej informacji o aplikacjach, zobacz [integracji aplikacji SaaS w usłudze Azure Active Directory](https://aka.ms/appstutorial). Aby uzyskać więcej informacji o ofercie swoją aplikację w galerii aplikacji Azure AD, zobacz [umieść swoją aplikację w galerii aplikacji usługi Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="native-tableau-support-is-now-available-in-azure-ad-application-proxy"></a>Natywna obsługa usługi Tableau jest obecnie dostępna na serwerze proxy aplikacji usługi Azure AD

**Typ:** Zmieniona funkcja  
**Kategoria usług:** Serwer proxy aplikacji  
**Możliwości produktu:** Kontrola dostępu

Za pomocą naszej aktualizacji z OpenID Connect protokołu OAuth 2.0 Code Grant dla naszych protokołu uwierzytelniania wstępnego, masz już konieczności dodatkowego konfigurowania Tableau za pomocą serwera Proxy aplikacji. Ta zmiana protokołu pomaga również lepszą obsługę bardziej nowoczesnych aplikacji przy użyciu tylko przekierowania HTTP, które często są obsługiwane w znacznikach języka JavaScript i HTML serwera Proxy aplikacji.

Aby uzyskać więcej informacji na temat naszych natywną obsługę Tableau, zobacz [aplikacji serwera Proxy Azure AD obecnie z natywną obsługą Tableau](https://blogs.technet.microsoft.com/applicationproxyblog/2018/08/14/azure-ad-application-proxy-now-with-native-tableau-support).

---

### <a name="new-support-to-add-google-as-an-identity-provider-for-b2b-guest-users-in-azure-active-directory-preview"></a>Nowa funkcja obsługi dodawania usługi Google jako dostawcy tożsamości dla użytkowników gości B2B w usłudze Azure Active Directory (wersja zapoznawcza)

**Typ:** Nowa funkcja  
**Kategoria usług:** B2B  
**Możliwości produktu:** B2B/B2C

Przez skonfigurowanie Federacji za pomocą usługi Google w Twojej organizacji, można pozwolić zaproszonego logowania użytkowników usługi Gmail udostępnionych aplikacji i zasobów przy użyciu istniejącego konta Google, bez konieczności tworzenia osobistych Account Microsoft (MSA) lub konta usługi Azure AD.

Jest to zgłoszenie zgody na uczestnictwo w publicznej wersji zapoznawczej. Aby uzyskać więcej informacji na temat Federacji Google zobacz [Dodaj Google jako dostawcy tożsamości dla użytkowników-gości B2B](https://docs.microsoft.com/azure/active-directory/b2b/google-federation).

---

## <a name="july-2018"></a>Lipiec 2018 r.

### <a name="improvements-to-azure-active-directory-email-notifications"></a>Ulepszenia powiadomień e-mail w usłudze Azure Active Directory

**Typ:** Zmieniona funkcja  
**Kategoria usług:** Inne  
**Możliwości produktu:** Zarządzanie cyklem życia tożsamości
 
Usługa Azure Active Directory (Azure AD) wiadomości e-mail teraz funkcji zaktualizowanego projektu, a także zmiany adres e-mail nadawcy i nazwa wyświetlana nadawcy, gdy wysyłane z następującymi usługami:
 
- Przeglądy dostępu w usłudze Azure AD
- Azure AD Connect Health 
- Usługa Azure AD Identity Protection 
- Usługa Azure AD Privileged Identity Management
- Powiadomienia certyfikat wygasa aplikacji przedsiębiorstwa
- Powiadomienia usługi inicjowania obsługi administracyjnej aplikacji przedsiębiorstwa
 
Powiadomienia e-mail będą wysyłane na następujący adres e-mail nadawcy i nazwa wyświetlana:

- Adres e-mail: azure-noreply@microsoft.com
- Nazwa wyświetlana: Microsoft Azure
 
Na przykład niektóre nowe projekty poczty e-mail i więcej informacji, zobacz [wiadomości E-mail z powiadomieniami w usłudze Azure AD PIM](https://go.microsoft.com/fwlink/?linkid=2005832).

---

### <a name="azure-ad-activity-logs-are-now-available-through-azure-monitor"></a>Dzienniki aktywności w usłudze Azure AD są teraz dostępne za pośrednictwem usługi Azure Monitor

**Typ:** Nowa funkcja  
**Kategoria usług:** Raportowanie  
**Możliwości produktu:** Monitorowanie i raportowanie

Dzienniki aktywności usługi Azure AD są teraz dostępne w publicznej wersji zapoznawczej dla usługi Azure Monitor (Usługa monitorowania całej platformy Azure). Usługa Azure Monitor udostępnia długoterminowego przechowywania i bezproblemową integrację, oprócz tych usprawnień:

- Długoterminowe przechowywanie kierując pliki dzienników z kontem usługi Azure storage.

- Bezproblemowa integracja rozwiązania SIEM, bez konieczności pisania lub obsługa skryptów niestandardowych.

- Bezproblemowa integracja z własnych niestandardowych rozwiązań, narzędzia do analizy lub rozwiązania do zarządzania zdarzeniami.

Aby uzyskać więcej informacji o tych nowych funkcjach, zobacz nasz blog [Dzienniki aktywności usługi Azure AD w usłudze Azure Monitor, diagnostykę znajduje się teraz w publicznej wersji zapoznawczej](https://cloudblogs.microsoft.com/enterprisemobility/2018/07/26/azure-ad-activity-logs-in-azure-monitor-diagnostics-now-in-public-preview/) i naszą dokumentacją [Dzienniki aktywności usługi Azure Active Directory na platformie Azure Monitor (wersja zapoznawcza)](https://docs.microsoft.com/azure/active-directory/reporting-azure-monitor-diagnostics-overview).

---

### <a name="conditional-access-information-added-to-the-azure-ad-sign-ins-report"></a>Informacje o dostępie warunkowym dodany do raportów logowania usługi Azure AD

**Typ:** Nowa funkcja  
**Kategoria usług:** Raportowanie  
**Możliwości produktu:** Ochrona i zabezpieczanie tożsamości
 
Ta aktualizacja pozwala zobaczyć, które zasady są oceniane, gdy użytkownik loguje się wraz z wyników zasad. Ponadto raport zawiera teraz typ aplikacji klienckiej używane przez użytkownika, dzięki czemu można zidentyfikować ruch w protokole starszej wersji. Wpisy raportu mogą teraz również być wyszukiwane identyfikator korelacji, który można znaleźć w komunikacie o błędzie widocznych dla użytkownika i może służyć do identyfikowania i rozwiązywania problemów z pasujące żądanie logowania.

---

### <a name="view-legacy-authentications-through-sign-ins-activity-logs"></a>Widok starszych uwierzytelnienia za pomocą dzienników aktywności logowania

**Typ:** Nowa funkcja  
**Kategoria usług:** Raportowanie  
**Możliwości produktu:** Monitorowanie i raportowanie
 
Wraz z wprowadzeniem **aplikację kliencką** dzienniki pola w aktywności logowania, klienci mogą teraz Zobacz użytkowników, którzy używają starszej wersji uwierzytelnienia. Klienci będą mogli korzystać z tych informacji przy użyciu interfejsu API programu Graph w MS logowania lub za pośrednictwem logowania Dzienniki aktywności w portalu usługi Azure AD, w którym można korzystać z **aplikację kliencką** formantu, aby odfiltrować uwierzytelnień starszej wersji. Zapoznaj się z dokumentacją, aby uzyskać więcej informacji.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---july-2018"></a>Nowe aplikacje federacyjne dostępne w galerii aplikacji usługi Azure AD — lipca 2018 r.

**Typ:** Nowa funkcja  
**Kategoria usług:** Aplikacje dla przedsiębiorstw  
**Możliwości produktu:** Integracje innych firm
 
W lipca 2018 Dodaliśmy obsługę 16 nowych aplikacjach z Federacją w galerii aplikacji:

[Centrum innowacji](https://docs.microsoft.com/azure/active-directory/saas-apps/innovationhub-tutorial), [Leapsome](https://docs.microsoft.com/azure/active-directory/saas-apps/leapsome-tutorial), [niektóre administrator rejestracji Jednokrotnej](https://docs.microsoft.com/azure/active-directory/saas-apps/certainadminsso-tutorial), przemieszczania PSUC [iPass SmartConnect](https://docs.microsoft.com/azure/active-directory/saas-apps/ipasssmartconnect-tutorial), [zrzut ekranu-O-obie](https://docs.microsoft.com/azure/active-directory/saas-apps/screencast-tutorial) , PowerSchool Unified zajęć, [dołączania Eli](https://docs.microsoft.com/azure/active-directory/saas-apps/elionboarding-tutorial), [obsługę zdalnego Bomgar](https://docs.microsoft.com/azure/active-directory/saas-apps/bomgarremotesupport-tutorial), [Nimblex](https://docs.microsoft.com/azure/active-directory/saas-apps/nimblex-tutorial), [Imagineer WebVision](https://docs.microsoft.com/azure/active-directory/saas-apps/imagineerwebvision-tutorial) , [Insight4GRC](https://docs.microsoft.com/azure/active-directory/saas-apps/insight4grc-tutorial), [łącznika JoinNow SecureW2](https://docs.microsoft.com/azure/active-directory/saas-apps/securejoinnow-tutorial), [Kanbanize](https://review.docs.microsoft.com/azure/active-directory/saas-apps/kanbanize-tutorial), [SmartLPA](https://review.docs.microsoft.com/azure/active-directory/saas-apps/smartlpa-tutorial), [Base umiejętności](https://docs.microsoft.com/azure/active-directory/saas-apps/skillsbase-tutorial)

Aby uzyskać więcej informacji o aplikacjach, zobacz [integracji aplikacji SaaS w usłudze Azure Active Directory](https://aka.ms/appstutorial). Aby uzyskać więcej informacji o ofercie swoją aplikację w galerii aplikacji Azure AD, zobacz [umieść swoją aplikację w galerii aplikacji usługi Azure Active Directory](https://aka.ms/azureadapprequest).

---
 
### <a name="new-user-provisioning-saas-app-integrations---july-2018"></a>Nowy użytkownik inicjowania obsługi administracyjnej integracji aplikacji SaaS — lipca 2018 r.

**Typ:** Nowa funkcja  
**Kategoria usług:** Aprowizowanie aplikacji  
**Możliwości produktu:** Integracje innych firm
 
Usługa Azure AD pozwala zautomatyzować procesy tworzenia, obsługi i usuwania tożsamości użytkowników w aplikacjach SaaS, takich jak Dropbox, Salesforce, ServiceNow i nie tylko. Do lipca 2018 dodano obsługę następujących aplikacji w galerii aplikacji Azure AD aprowizacji użytkowników:

- [Cisco Spark](https://docs.microsoft.com/azure/active-directory/saas-apps/cisco-spark-provisioning-tutorial)

- [Cisco WebEx](https://docs.microsoft.com/azure/active-directory/saas-apps/cisco-webex-provisioning-tutorial)

- [Bonusly](https://docs.microsoft.com/azure/active-directory/saas-apps/bonusly-provisioning-tutorial)

Aby uzyskać listę wszystkich aplikacji, które obsługują aprowizowania użytkowników w galerii usługi Azure AD, zobacz [integracji aplikacji SaaS w usłudze Azure Active Directory](https://aka.ms/appstutorial).

---

### <a name="connect-health-for-sync---an-easier-way-to-fix-orphaned-and-duplicate-attribute-sync-errors"></a>Connect Health do celów synchronizacji — łatwiejszy sposób naprawić błędy synchronizacji oddzielone i zduplikowanego atrybutu

**Typ:** Nowa funkcja  
**Kategoria usług:** AD Connect  
**Możliwości produktu:** Monitorowanie i raportowanie
 
Program Azure AD Connect Health wprowadza samodzielne rozwiązanie problemu, ułatwiające wyróżnić i naprawić błędy synchronizacji. Ta funkcja rozwiązuje błędy synchronizacji zduplikowanym atrybutem i poprawki obiektów, które są oddzielone od usługi Azure AD. Diagnostyka to ma następujące zalety:

- Zawęża zduplikowanym atrybutem błędy synchronizacji, zapewniając określonej poprawki

- Stosuje poprawki dla scenariuszy usługi Azure AD, naprawianie błędów w jednym kroku w wersji dedykowanej

- Nie uaktualnienia lub konfiguracja jest wymagana do włączenia i korzystać z tej funkcji

Aby uzyskać więcej informacji, zobacz [diagnozowanie i rozwiązywanie błędów synchronizacji zduplikowanym atrybutem](https://docs.microsoft.com/azure/active-directory/connect-health/active-directory-aadconnect-health-diagnose-sync-errors)

---

### <a name="visual-updates-to-the-azure-ad-and-msa-sign-in-experiences"></a>Visual aktualizacji do usługi Azure AD i logowania konta Microsoft napotyka

**Typ:** Zmieniona funkcja  
**Kategoria usług:** Azure AD  
**Możliwości produktu:** Uwierzytelnianie użytkowników

Zaktualizowaliśmy interfejs użytkownika dla usług online firmy Microsoft środowiska logowania takich jak dla usługi Office 365 i Azure. Ta zmiana sprawia, że ekrany mniej zatłoczoną i bardziej bezpośredni. Aby uzyskać więcej informacji na temat tej zmiany, zobacz [nadchodzące ulepszenia procesu logowania w usłudze Azure AD](https://cloudblogs.microsoft.com/enterprisemobility/2018/04/04/upcoming-improvements-to-the-azure-ad-sign-in-experience/) blogu.

---

### <a name="new-release-of-azure-ad-connect---july-2018"></a>Nowa wersja programu Azure AD Connect — lipca 2018 r.

**Typ:** Zmieniona funkcja  
**Kategoria usług:** Aprowizowanie aplikacji  
**Możliwości produktu:** Zarządzanie cyklem życia tożsamości

Najnowsza wersja programu Azure AD Connect obejmuje: 

- Poprawki i aktualizacje obsługi 

- Ogólne udostępnienie usług integracji Federację Ping

- Aktualizacje do najnowszego klienta SQL 2012 

Aby uzyskać więcej informacji na temat tej aktualizacji, zobacz [program Azure AD Connect: Historia wersji](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-version-history)

---

### <a name="updates-to-the-terms-of-use-tou-end-user-ui"></a>Aktualizacje do Interfejsu użytkownika końcowego warunków użytkowania (ToU)

**Typ:** Zmieniona funkcja  
**Kategoria usług:** Warunki użytkowania  
**Możliwości produktu:** Nadzór

Trwa aktualizowanie ciągu akceptacji w Interfejsie użytkownika końcowego warunków użytkowania.

**Aktualny tekst.** Aby uzyskać dostęp do zasobów [tenantName], musisz zaakceptować warunki użytkowania.<br>**Nowy tekst.** Aby uzyskać dostęp do zasobów [tenantName], możesz przeczytać warunki użytkowania.

**Aktualny tekst:** Wybieranie zaakceptować oznacza, że zgadzasz się na wszystkie powyższe warunki użytkowania.<br>**Nowy tekst:** Kliknij przycisk Zaakceptuj, aby upewnić się, że użytkownik przeczytał i zrozumiał warunki użytkowania.

---
 
### <a name="pass-through-authentication-supports-legacy-protocols-and-applications"></a>Uwierzytelnianie przekazywane obsługuje starsze protokoły i aplikacje

**Typ:** Zmieniona funkcja  
**Kategoria usług:** Uwierzytelnienia (logowania)  
**Możliwości produktu:** Uwierzytelnianie użytkowników
 
Uwierzytelnianie przekazywane teraz obsługuje starszych protokołów i aplikacji. Następujące ograniczenia są teraz w pełni obsługiwane:

- Logowania użytkowników do starszych aplikacji klienckich pakietu Office, pakietu Office 2010 i Office 2013 bez nowoczesnego uwierzytelniania.

- Dostęp do udostępnianie kalendarza i bezpłatne zajęty informacji w programie Exchange środowisk o hybrydowych w pakiecie Office 2010 tylko.

- Logowania użytkowników do usługi Skype dla firm aplikacji klienckich bez nowoczesnego uwierzytelniania.

- Użytkownik logowania do programu PowerShell w wersji 1.0.

- Programu Apple Device Enrollment Program (DEP) firmy Apple, przy użyciu Asystenta ustawień systemu iOS. 

---
 
### <a name="converged-security-info-management-for-self-service-password-reset-and-multi-factor-authentication"></a>Zarządzanie zabezpieczeniami konwergentnej informacje samoobsługowego resetowania haseł i uwierzytelniania wieloskładnikowego

**Typ:** Nowa funkcja  
**Kategoria usług:** SSPR  
**Możliwości produktu:** Uwierzytelnianie użytkowników

Ta nowa funkcja umożliwia użytkownikom zarządzanie zabezpieczające (na przykład, numer telefonu, adres e-mail, aplikacji mobilnej i tak dalej) do samoobsługowego resetowania haseł (SSPR) i usługi Multi-Factor Authentication (MFA) w jednym środowisku. Użytkownicy mają już nie można zarejestrować tego samego jako informacje zabezpieczające dla funkcji samoobsługowego resetowania HASEŁ i uwierzytelniania Wieloskładnikowego w dwóch różnych środowiskach. Nowe środowisko dotyczy również użytkowników, którzy mają funkcji samoobsługowego resetowania HASEŁ lub uwierzytelniania MFA.

Jeśli organizacja nie ma wymuszanie rejestracji uwierzytelniania Wieloskładnikowego lub samoobsługowego resetowania HASEŁ, użytkownicy mogą rejestrować swoje informacje zabezpieczające za pośrednictwem **Moje aplikacje** portalu. W tym miejscu użytkownicy mogą rejestrować żadnych metod włączone dla uwierzytelniania Wieloskładnikowego lub samoobsługowego resetowania HASEŁ. 

Jest to zgłoszenie zgody na uczestnictwo w publicznej wersji zapoznawczej. Administratorzy mogą włączyć nowe środowisko (w razie potrzeby) dla wybranej grupy użytkowników lub dla wszystkich użytkowników w dzierżawie.

---
 
### <a name="use-the-microsoft-authenticator-app-to-verify-your-identity-when-you-reset-your-password"></a>Korzystanie z aplikacji Microsoft Authenticator do zweryfikowania Twojej tożsamości podczas resetowania hasła

**Typ:** Zmieniona funkcja  
**Kategoria usług:** SSPR  
**Możliwości produktu:** Uwierzytelnianie użytkowników

Ta funkcja pozwala użytkowników innych niż administratorzy zweryfikować swoją tożsamość podczas resetowania hasła przy użyciu powiadomienia lub kodu z Microsoft Authenticator (lub dowolną inną aplikację). Po Administratorzy, Włącz ten samoobsługowego resetowania haseł — metoda, użytkownicy, którzy zarejestrowali się do aplikacji mobilnej za pośrednictwem aka.ms/mfasetup lub aka.ms/setupsecurityinfo można użyć aplikacji mobilnej jako metody weryfikacji podczas resetowania hasła.

Tylko powiadomienia aplikacji mobilnej może zostać włączona w ramach zasad, które wymagają dwóch metod, aby zresetować hasło.

---

## <a name="june-2018"></a>Czerwiec 2018 r.

### <a name="change-notice-security-fix-to-the-delegated-authorization-flow-for-apps-using-azure-ad-activity-logs-api"></a>Powiadomienie o zmianie: Przepływ delegowanej autoryzacji dla aplikacji przy użyciu interfejsu API usługi Azure AD działania dzienniki poprawki zabezpieczeń

**Typ:** Plan zmiany  
**Kategoria usług:** Raportowanie  
**Możliwości produktu:** Monitorowanie i raportowanie

Ze względu na nasze silniejsze wymuszania zabezpieczeń Musieliśmy wprowadzić zmianę uprawnień dla aplikacji używających przepływ delegowanej autoryzacji dostępu do [interfejsy API usługi Azure AD działania dzienniki](https://aka.ms/aadreportsapi). Ta zmiana zostanie przeprowadzona przez **26 czerwca 2018 r**.

Wszelkie aplikacje użycie dziennika aktywności platformy Azure AD interfejsów API, wykonaj następujące kroki, aby upewnić się, że aplikacja nie zostanie przerwane po operacji zmiany.

**Aby zaktualizować uprawnienia aplikacji**

1. Zaloguj się do witryny Azure portal, wybierz opcję **usługi Azure Active Directory**, a następnie wybierz pozycję **rejestracje aplikacji**.
2. Wybierz aplikację, który używa działania dzienniki interfejsu API Azure AD, wybierz **ustawienia**, wybierz opcję **wymagane uprawnienia**, a następnie wybierz pozycję **Windows Azure Active Directory** interfejsu API.
3. W **delegowane uprawnienia** obszaru **Włącz dostęp** bloku, zaznacz pole obok pozycji **directory odczytu** danych, a następnie wybierz **Zapisz**.
4. Wybierz **udzielić uprawnień**, a następnie wybierz pozycję **tak**.
    
    >[!Note]
    >Musi być administratorem globalnym, aby udzielić uprawnień do aplikacji.

Aby uzyskać więcej informacji, zobacz [udzielić uprawnień](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-prerequisites-azure-portal#grant-permissions) obszaru warunków wstępnych, aby uzyskać dostęp do artykułu interfejsu API raportowania usługi Azure AD.

---

### <a name="configure-tls-settings-to-connect-to-azure-ad-services-for-pci-dss-compliance"></a>Skonfiguruj ustawienia protokołu TLS, aby połączyć się z usługami Azure AD pod kątem zgodności ze standardami PCI DSS

**Typ:** Nowa funkcja  
**Kategoria usług:** ND  
**Możliwości produktu:** Platforma

Zabezpieczeń TLS (Transport Layer) to protokół, który zapewnia integralność danych i poufności informacji między dwiema aplikacjami komunikujące się i protokół najczęściej wdrożonej zabezpieczeń używanych obecnie.

[Rady standardami bezpieczeństwa PCI](https://www.pcisecuritystandards.org/) stwierdził, że Wczesne wersje protokołu TLS i protokół Secure Sockets Layer (SSL) musi zostać wyłączona na rzecz włączana protokołów aplikacji nowego i bardziej bezpieczne, za pomocą od zgodności **30 czerwca 2018**. Ta zmiana oznacza, że jeśli łączenie się z usługami Azure AD i wymagają zgodności ze standardem PCI DSS, należy wyłączyć protokół TLS 1.0. Wiele wersji protokołu TLS są dostępne, ale protokołu TLS 1.2 jest najnowszą wersją dostępną dla usług Azure Active Directory. Firma Microsoft zdecydowanie zaleca się przejście bezpośrednio do protokołu TLS 1.2 dla kombinacji zarówno klient/serwer, jak i przeglądarką a serwerem.

Przestarzałe przeglądarki może nie obsługiwać nowszych wersji protokołu TLS, np. protokół TLS 1.2. Aby zobaczyć, które wersje protokołu TLS są obsługiwane przez przeglądarkę, przejdź do [Qualys SSL Labs](https://www.ssllabs.com/) lokacji, a następnie kliknij przycisk **testów przeglądarki**. Firma Microsoft zaleca się uaktualnienie do najnowszej wersji przeglądarki sieci web i najlepiej włączyć tylko protokołu TLS 1.2.

**Aby włączyć protokół TLS 1.2 przez przeglądarkę**

- **Microsoft Edge i przeglądarki Internet Explorer (oba są ustawiane przy użyciu programu Internet Explorer)**

    1. Otwórz program Internet Explorer, wybierz **narzędzia** > **Opcje internetowe** > **zaawansowane**.
    2. W **zabezpieczeń** wybierz opcję **użycia protokołu TLS 1.2**, a następnie wybierz pozycję **OK**.
    3. Zamknij wszystkie okna przeglądarki, a następnie ponownie uruchom program Internet Explorer. 

- **Google Chrome**

    1. Otwórz Google Chrome, typ *chrome://settings/* w pasku adresu i naciśnij klawisz **Enter**.
    2. Rozwiń **zaawansowane** opcji, przejdź do **systemu** obszaru, a następnie wybierz **otworzyć ustawienia serwera proxy**.
    3. W **właściwości internetowe** wybierz pozycję **zaawansowane** kartę, przejdź do **zabezpieczeń** wybierz opcję **użycia protokołu TLS 1.2**, a następnie wybierz pozycję  **OK**.
    4. Zamknij wszystkie okna przeglądarki i ponownie Google Chrome.

- **Mozilla Firefox**

    1. Otwórz przeglądarkę Firefox typu *o: config* do paska adresu, a następnie naciśnij klawisz **Enter**.
    2. Wyszukaj termin, *TLS*, a następnie wybierz pozycję **security.tls.version.max** wpisu.
    3. Ustaw wartość **3** wymusić przeglądarkę, aby użycie wersji protokołu TLS 1.2, a następnie wybierz **OK**.

        >[!NOTE]
        >Firefox wersja 60.0 obsługuje TLS 1.3, więc wartość security.tls.version.max można również ustawić, **4**.

    4. Zamknij wszystkie okna przeglądarki i ponownie Mozilla Firefox.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---june-2018"></a>Nowe aplikacje federacyjne dostępne w galerii aplikacji usługi Azure AD — czerwca 2018 r.

**Typ:** Nowa funkcja  
**Kategoria usług:** Aplikacje dla przedsiębiorstw  
**Możliwości produktu:** Integracje innych firm
 
W czerwcu 2018 Dodaliśmy obsługę tych 15 nowe aplikacje z Federacją w galerii aplikacji:

[Skytap](https://docs.microsoft.com/azure/active-directory/active-directory-saas-skytap-tutorial), [rozliczania utworów muzycznych](https://docs.microsoft.com/azure/active-directory/active-directory-saas-settlingmusic-tutorial), [aplikacji LOB z obsługą tokenu języka SAML 1.1](https://docs.microsoft.com/azure/active-directory/active-directory-saas-saml-tutorial), [Supermood](https://docs.microsoft.com/azure/active-directory/active-directory-saas-supermood-tutorial), [Autotask](https://docs.microsoft.com/azure/active-directory/active-directory-saas-autotaskendpointbackup-tutorial), [ Kopia zapasowa punktu końcowego](https://docs.microsoft.com/azure/active-directory/active-directory-saas-autotaskendpointbackup-tutorial), [sieci Skyhigh](https://docs.microsoft.com/azure/active-directory/active-directory-saas-skyhighnetworks-tutorial), Smartway2, [TonicDM](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tonicdm-tutorial), [Moconavi](https://docs.microsoft.com/azure/active-directory/active-directory-saas-moconavi-tutorial), [Zoho jeden](https://docs.microsoft.com/azure/active-directory/active-directory-saas-zohoone-tutorial), [ Lokalny program SharePoint](https://docs.microsoft.com/azure/active-directory/active-directory-saas-sharepoint-on-premises-tutorial), [przewiduje CX Suite](https://docs.microsoft.com/azure/active-directory/active-directory-saas-foreseecxsuite-tutorial), [Vidyard](https://docs.microsoft.com/azure/active-directory/active-directory-saas-vidyard-tutorial), [ChronicX](https://docs.microsoft.com/azure/active-directory/active-directory-saas-chronicx-tutorial)

Aby uzyskać więcej informacji o aplikacjach, zobacz [integracji aplikacji SaaS w usłudze Azure Active Directory](https://aka.ms/appstutorial). Aby uzyskać więcej informacji o ofercie swoją aplikację w galerii aplikacji Azure AD, zobacz [umieść swoją aplikację w galerii aplikacji usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing). 

---

### <a name="azure-ad-password-protection-is-available-in-public-preview"></a>Ochrony hasłem w usłudze Azure AD jest dostępna w publicznej wersji zapoznawczej

**Typ:** Nowa funkcja  
**Kategoria usług:** Identity Protection  
**Możliwości produktu:** Uwierzytelnianie użytkowników

Użyj hasła usługi Azure AD ochrony, aby wyeliminować łatwe do odgadnięcia hasła ze środowiska. Eliminowanie haseł pomaga zmniejszyć ryzyko naruszenia zabezpieczeń z atak typu osłony hasła.

W szczególności ochrony haseł usługi Azure AD umożliwia:

- Ochronę kont w organizacji w usługi Azure AD i systemu Windows Server Active Directory (AD). 
- Zatrzymuje użytkownikom korzystanie z haseł na liście więcej niż 500 najczęściej używanych haseł, a ponad 1 milion znaków podstawienia odmiany tych haseł.
- Administrowanie ochrony haseł usługi Azure AD z jednej lokalizacji w portalu usługi Azure AD dla usługi Azure AD i AD systemu Windows Server dla lokalnego.

Aby uzyskać więcej informacji na temat ochrony haseł usługi Azure AD, zobacz [eliminowanie nieprawidłowych haseł w organizacji](https://aka.ms/aadpasswordprotectiondocs).

---

### <a name="new-all-guests-conditional-access-policy-template-created-during-terms-of-use-tou-creation"></a>Nowych "wszystkich gości" dostępu warunkowego szablonu zasad podczas tworzenia warunków użytkowania (ToU)

**Typ:** Nowa funkcja  
**Kategoria usług:** Warunki użytkowania  
**Możliwości produktu:** Nadzór

Podczas tworzenia warunków użytkowania (ToU) tworzony jest też nowy szablon zasad dostępu warunkowego dla "wszystkich gości" i "wszystkie aplikacje". Ten nowy szablon zasad mają zastosowanie nowo utworzony warunków użytkowania, co usprawnia tworzenie i wymuszanie procesu dla gości.

Aby uzyskać więcej informacji, zobacz [Azure Active Directory — warunki dotyczące użycia funkcji](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use).

---

### <a name="new-custom-conditional-access-policy-template-created-during-terms-of-use-tou-creation"></a>Nowy szablon zasad dostępu warunkowego "niestandardowy", podczas tworzenia warunków użytkowania (ToU)

**Typ:** Nowa funkcja  
**Kategoria usług:** Warunki użytkowania  
**Możliwości produktu:** Nadzór

Podczas tworzenia warunków użytkowania (ToU) również tworzony jest nowy szablon zasad dostępu warunkowego "niestandardowe". Ten nowy szablon zasad umożliwia tworzenie warunków użytkowania, a następnie od razu przejść do bloku tworzenia zasad dostępu warunkowego, bez konieczności ręcznego przejdź za pośrednictwem portalu.

Aby uzyskać więcej informacji, zobacz [Azure Active Directory — warunki dotyczące użycia funkcji](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use).

---

### <a name="new-and-comprehensive-guidance-about-deploying-azure-multi-factor-authentication"></a>Nowe i wyczerpujące wskazówki dotyczące wdrażania usługi Azure Multi-Factor Authentication

**Typ:** Nowa funkcja  
**Kategoria usług:** Inne  
**Możliwości produktu:** Ochrona i zabezpieczanie tożsamości
 
Udostępniliśmy nowe wskazówki krok po kroku dotyczące wdrażania usługi Azure Multi-Factor Authentication (MFA) w Twojej organizacji.

Aby wyświetlić w przewodniku wdrażania uwierzytelniania Wieloskładnikowego, przejdź do [wskazówki dotyczące wdrażania tożsamości](https://aka.ms/DeploymentPlans) repozytorium w witrynie GitHub. Aby przekazać opinię na temat wskazówki dotyczące wdrażania, użyj [wdrożenia planu forum z opiniami](https://aka.ms/deploymentplanfeedback). Jeśli masz jakieś pytania dotyczące wskazówki dotyczące wdrażania, skontaktuj się z nami pod adresem [IDGitDeploy](mailto:idgitdeploy@microsoft.com).

---

### <a name="azure-ad-delegated-app-management-roles-are-in-public-preview"></a>Usługa Azure AD delegowane Zarządzanie aplikacjami, które role są w publicznej wersji zapoznawczej

**Typ:** Nowa funkcja  
**Kategoria usług:** Aplikacje dla przedsiębiorstw  
**Możliwości produktu:** Kontrola dostępu

Administratorzy mogą teraz delegować zadania zarządzania aplikacją bez przypisywania roli administratora globalnego. Nowe role i funkcje są:

- **Nowy standard role administratora usługi Azure AD:**

    - **Administrator aplikacji.** Daje możliwość zarządzać wszystkimi aspektami wszystkie aplikacje, w tym rejestracji, ustawień logowania jednokrotnego, przypisań aplikacji i licencjonowania, ustawienia serwera proxy aplikacji i zgody (z wyjątkiem sytuacji określonych zasobów usługi Azure AD).

    - **Administrator aplikacji w chmurze.** Przyznaje wszystkie możliwości administratora aplikacji, z wyjątkiem serwera proxy aplikacji, ponieważ nie zapewnia dostępu do lokalnego.

    - **Deweloper aplikacji.** Daje możliwość tworzenia rejestracje aplikacji, nawet jeśli **Zezwalaj użytkownikom na rejestrowanie aplikacji** opcja jest wyłączona.

- **Własność (Konfigurowanie aplikacji rejestracji i -enterprise aplikacji, podobny do procesu własność grupy:**
 
    - **Właściciel rejestracji aplikacji.** Daje możliwość zarządzać wszystkimi aspektami należących do rejestracji aplikacji, w tym manifest aplikacji i dodawanie dodatkowych właścicieli.

    - **Właściciel aplikacji przedsiębiorstwa.** Daje możliwość zarządzać wieloma aspektami aplikacji należących do przedsiębiorstwa, w tym ustawień logowania jednokrotnego, przypisań aplikacji i zgody (z wyjątkiem sytuacji określonych zasobów usługi Azure AD).

Aby uzyskać więcej informacji na temat publicznej wersji zapoznawczej, zobacz [usługi Azure AD delegowane Zarządzanie aplikacjami, które role są w publicznej wersji zapoznawczej!](https://cloudblogs.microsoft.com/enterprisemobility/2018/06/13/hallelujah-azure-ad-delegated-application-management-roles-are-in-public-preview/) blog. Aby uzyskać więcej informacji na temat ról i uprawnień, zobacz [przypisywanie ról administratorów w usłudze Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal).

---

## <a name="may-2018"></a>Maj 2018 r.

### <a name="expressroute-support-changes"></a>Zmiany pomocy technicznej usługi ExpressRoute

**Typ:** Plan zmiany  
**Kategoria usług:** Uwierzytelnienia (logowania)  
**Możliwości produktu:** Platforma  

Oprogramowanie jako usługa, takich jak Azure Active Directory (Azure AD) są przeznaczone do najlepszej metody, przechodząc bezpośrednio przez Internet, bez korzystania z usługi ExpressRoute lub innych prywatnej tuneli sieci VPN. W związku z tym na **1 sierpnia 2018**, firma Microsoft nie będzie obsługiwać usługi ExpressRoute dla usługi Azure AD przy użyciu publicznej komunikacji równorzędnej Azure i społeczności platformy Azure w komunikacji równorzędnej firmy Microsoft. Wszystkie usługi, wpływ tej zmiany może się okazać, Azure AD ruchu stopniowo przesunięcie z usługi ExpressRoute z Internetem.

Zmieniamy się obsłudze, Wiemy także, są nadal sytuacji, w których konieczne może być użyć to dedykowany zestaw obwodów dla ruchu uwierzytelniania. W związku z tym usługa Azure AD będzie obsługiwać ograniczenia zakresu adresów IP dla dzierżawcy przy użyciu usługi ExpressRoute i usług już na komunikacji równorzędnej firmy Microsoft ze społecznością "Innych usług Online pakietu Office 365". Jeśli dotyczy usług, ale wymagają usługi ExpressRoute, należy wykonać następujące czynności:

- **Jeśli komputer jest w publicznej komunikacji równorzędnej Azure.** Przenieś do komunikacji równorzędnej firmy Microsoft i zarejestrować się w celu **inne usługi Online Office 365 (12076:5100)** społeczności. Aby uzyskać więcej informacji o tym, jak można przenieść z publicznej komunikacji równorzędnej Azure do komunikacji równorzędnej firmy Microsoft, zobacz [Przenieś publicznej komunikacji równorzędnej do komunikacji równorzędnej firmy Microsoft](https://docs.microsoft.com/azure/expressroute/how-to-move-peering) artykułu.

- **Jeśli jesteś w komunikacji równorzędnej firmy Microsoft.** Zaloguj się w celu **inne usługi Office 365 Online service (12076:5100)** społeczności. Aby uzyskać więcej informacji na temat wymagania dotyczące routingu, zobacz [obsługę protokołu BGP społeczności sekcji](https://docs.microsoft.com/azure/expressroute/expressroute-routing#bgp) artykułu wymagania dotyczące routingu usługi ExpressRoute.

Jeśli musisz nadal używać dedykowanego obwodów, konieczne będzie komunikować się zespołem Account firmy Microsoft o tym, jak uzyskać pozwolenie na korzystanie **inne usługi Office 365 Online service (12076:5100)** społeczności. Tablica zarządzana Office przeglądu MS sprawdzi, czy potrzebujesz tych obwodów i upewnij się, że rozumiesz implikacje techniczne ich zachowania. Subskrypcje nieautoryzowane próby utworzenia filtrów tras dla usługi Office 365 będą otrzymywać komunikat o błędzie. 
 
---

### <a name="microsoft-graph-apis-for-administrative-scenarios-for-tou"></a>Interfejsy API programu Graph firmy Microsoft dla scenariuszy administracyjnych dla warunków użytkowania

**Typ:** Nowa funkcja  
**Kategoria usług:** Warunki użytkowania  
**Możliwości produktu:** Środowisko deweloperskie
 
Dodaliśmy interfejsy API programu Microsoft Graph dla operacji administracyjnych programu Azure AD warunki użytkowania. Jesteś w stanie tworzenie, aktualizowanie i usuwanie obiektu warunki użytkowania.

---

### <a name="add-azure-ad-multi-tenant-endpoint-as-an-identity-provider-in-azure-ad-b2c"></a>Dodaj punkt końcowy z wieloma dzierżawami usługi Azure AD jako dostawcy tożsamości w usłudze Azure AD B2C

**Typ:** Nowa funkcja  
**Kategoria usług:** B2C — Consumer Identity Management  
**Możliwości produktu:** B2B/B2C
 
Za pomocą zasad niestandardowych, możesz teraz dodać wspólnego punktu końcowego usługi Azure AD jako dostawcy tożsamości w usłudze Azure AD B2C. Umożliwia to pojedynczy punkt wejścia dla wszystkich użytkowników usługi Azure AD, którzy logują się do aplikacji. Aby uzyskać więcej informacji, zobacz [usługi Azure Active Directory B2C: Zezwalaj użytkownikom na logowanie się do dostawcy tożsamości wielu dzierżaw usługi Azure AD za pomocą zasad niestandardowych](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-setup-commonaad-custom).

---

### <a name="use-internal-urls-to-access-apps-from-anywhere-with-our-my-apps-sign-in-extension-and-the-azure-ad-application-proxy"></a>Wewnętrzne adresy URL umożliwia dostęp do aplikacji z dowolnego miejsca za pomocą naszego rozszerzenia Moje aplikacje logowania i serwera Proxy aplikacji usługi AD systemu Azure

**Typ:** Nowa funkcja  
**Kategoria usług:** Moje aplikacje  
**Możliwości produktu:** Logowanie jednokrotne
 
Użytkownicy mogą teraz uzyskiwać dostęp do aplikacji za pomocą wewnętrznych adresów URL nawet spoza sieci firmowej przy użyciu bezpiecznego Moje aplikacje logowania rozszerzenia dla usługi Azure AD. To będzie działać z dowolnej aplikacji, która została opublikowana przy użyciu serwera Proxy aplikacji usługi Azure AD, w dowolnej przeglądarce, która także ma zainstalowane rozszerzenie przeglądarki panelu dostępu. Funkcji Przekierowywanie adresu URL jest automatycznie włączone, gdy użytkownik loguje się do rozszerzenia. Rozszerzenie jest dostępne do pobrania na [Microsoft Edge](https://go.microsoft.com/fwlink/?linkid=845176), [Chrome](https://go.microsoft.com/fwlink/?linkid=866367), i [Firefox](https://go.microsoft.com/fwlink/?linkid=866366).

---
 
### <a name="azure-active-directory---data-in-europe-for-europe-customers"></a>Usługa Azure Active Directory — dane w Europie klienci Europa

**Typ:** Nowa funkcja  
**Kategoria usług:** Inne  
**Możliwości produktu:** GoLocal

Klienci w Europie wymagają swoich danych, aby pozostać w Europie i nie są replikowane poza Europejskiego centrów danych na potrzeby spotkania i poufności informacji w europejskich przepisów. To [artykułu](https://go.microsoft.com/fwlink/?linkid=872328) zawiera szczegółowe informacje, jakie informacje tożsamości będą przechowywane w Europie i udostępniają szczegółowe informacje na temat informacji, który ma być przechowywana poza Europejskich centrach danych. 

---
 
### <a name="new-user-provisioning-saas-app-integrations---may-2018"></a>Nowy użytkownik inicjowania obsługi administracyjnej integracji aplikacji SaaS — maj 2018 r.

**Typ:** Nowa funkcja  
**Kategoria usług:** Aprowizowanie aplikacji  
**Możliwości produktu:** Integracje innych firm
 
Usługa Azure AD pozwala zautomatyzować procesy tworzenia, obsługi i usuwania tożsamości użytkowników w aplikacjach SaaS, takich jak Dropbox, Salesforce, ServiceNow i nie tylko. W maju 2018 r Dodaliśmy obsługę następujących aplikacji w galerii aplikacji Azure AD aprowizacji użytkowników:

- [BlueJeans](https://docs.microsoft.com/azure/active-directory/active-directory-saas-bluejeans-provisioning-tutorial)

- [OnDemand podstawy](https://docs.microsoft.com/azure/active-directory/active-directory-saas-cornerstone-ondemand-provisioning-tutorial)

- [Zendesk](https://docs.microsoft.com/azure/active-directory/active-directory-saas-zendesk-provisioning-tutorial)

Aby uzyskać listę wszystkich aplikacji, które obsługują aprowizowania użytkowników w galerii usługi Azure AD, zobacz [ https://aka.ms/appstutorial ](https://aka.ms/appstutorial).

---
 
### <a name="azure-ad-access-reviews-of-groups-and-app-access-now-provides-recurring-reviews"></a>Przeglądy dostępu usługi Azure AD, grup i przeglądy cyklicznego udostępnia teraz dostęp do aplikacji

**Typ:** Nowa funkcja  
**Kategoria usług:** Przeglądy dostępu  
**Możliwości produktu:** Nadzór
 
Przegląd dostępu grup i aplikacji jest teraz ogólnie dostępna w ramach usługi Azure AD Premium P2.  Administratorzy, będzie można skonfigurować przeglądów dostępu członkostwa w grupach i przypisania aplikacji, aby automatycznie powtarzać w regularnych odstępach czasu, takich jak co miesiąc lub kwartalną.

---

### <a name="azure-ad-activity-logs-sign-ins-and-audit-are-now-available-through-ms-graph"></a>Dzienniki aktywności usługi AD platformy Azure (logowania i inspekcji) są teraz dostępne za pośrednictwem MS Graph

**Typ:** Nowa funkcja  
**Kategoria usług:** Raportowanie  
**Możliwości produktu:** Monitorowanie i raportowanie
 
Dzienniki aktywności usługi AD platformy Azure, co obejmuje dzienniki inspekcji i logowania, są teraz dostępne za pośrednictwem MS Graph. Udostępniliśmy dwie punktów końcowych za pośrednictwem MS Graph dostępu do tych dzienników. Zapoznaj się z naszym [dokumenty](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-getting-started-azure-portal) uzyskać programowy dostęp do usługi Azure AD interfejsy API raportowania na rozpoczęcie pracy. 

---
 
### <a name="improvements-to-the-b2b-redemption-experience-and-leave-an-org"></a>Ulepszenia realizacji B2B środowisko i opuścić organizacji

**Typ:** Nowa funkcja  
**Kategoria usług:** B2B  
**Możliwości produktu:** B2B/B2C

**Dokładnie na czas realizacji:** Gdy udostępniasz zasób użytkownika-gościa za pomocą interfejsu API B2B — nie jest potrzebny do wysłania wiadomości e-mail z zaproszeniem specjalne. W większości przypadków użytkownik-Gość można uzyskać dostępu do zasobu i zostaną podjęte przez środowisko realizacji dokładnie na czas. Nie wpływa na z powodu brakujących wiadomości e-mail. Nie ma więcej poproszenie użytkowników gości "Czy możesz kliknąć łącze realizacji wysłania systemu, możesz?". Oznacza to, gdy SPO korzysta z Menedżera zaproszenie — mętna załączniki mogą mieć ten sam Kanoniczny adres URL dla wszystkich użytkowników — wewnętrzne i zewnętrzne — w dowolnym stanie realizacji.

**Realizacji nowoczesne środowisko:** Nie ma więcej podziału ekranu realizacji strona docelowa. Użytkownicy będą widzieć nowoczesny zgoda doświadczenia z organizacji zapraszającej zasady zachowania poufności informacji, podobnie jak w przypadku aplikacji innych firm.

**Użytkownicy-goście, można pozostawić organizacji:** Po relację użytkownika w organizacji mogą samodzielnie służą one opuszczania organizacji. Nie ma więcej wywoływania administratora organizacji zapraszającej o "usunięte", nie ma więcej wywoływanie biletami pomocy technicznej.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---may-2018"></a>Nowe aplikacje federacyjne dostępne w galerii aplikacji usługi Azure AD — maj 2018 r.

**Typ:** Nowa funkcja  
**Kategoria usług:** Aplikacje dla przedsiębiorstw  
**Możliwości produktu:** Integracje innych firm
 
W maju 2018 Dodaliśmy obsługę tych 18 nowe aplikacje z Federacją do galerii aplikacji:

[AwardSpring](https://docs.microsoft.com/azure/active-directory/active-directory-saas-awardspring-tutorial), określają sposób Infogix Data3Sixty, [Yodeck](https://docs.microsoft.com/azure/active-directory/active-directory-saas-infogix-tutorial), [narzędzia Jamf Pro](https://docs.microsoft.com/azure/active-directory/active-directory-saas-jamfprosamlconnector-tutorial), [KnowledgeOwl](https://docs.microsoft.com/azure/active-directory/active-directory-saas-knowledgeowl-tutorial), [Envi MMIS](https://docs.microsoft.com/azure/active-directory/active-directory-saas-envimmis-tutorial), [LaunchDarkly](https://docs.microsoft.com/azure/active-directory/active-directory-saas-launchdarkly-tutorial), [Adobe Captivate Prime](https://docs.microsoft.com/azure/active-directory/active-directory-saas-adobecaptivateprime-tutorial), [Online montażowy](https://docs.microsoft.com/azure/active-directory/active-directory-saas-montageonline-tutorial),[まなびポケット](https://docs.microsoft.com/azure/active-directory/active-directory-saas-manabipocket-tutorial), OpenReel, [łuk publikowania — Usługa rejestracji Jednokrotnej ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-arc-tutorial), [PlanGrid](https://docs.microsoft.com/azure/active-directory/active-directory-saas-plangrid-tutorial), [iWellnessNow](https://docs.microsoft.com/azure/active-directory/active-directory-saas-iwellnessnow-tutorial), [Proxyclick](https://docs.microsoft.com/azure/active-directory/active-directory-saas-proxyclick-tutorial), [Riskware](https://docs.microsoft.com/azure/active-directory/active-directory-saas-riskware-tutorial), [stada](https://docs.microsoft.com/azure/active-directory/active-directory-saas-flock-tutorial), [Reviewsnap](https://docs.microsoft.com/azure/active-directory/active-directory-saas-reviewsnap-tutorial)

Aby uzyskać więcej informacji o aplikacjach, zobacz [integracji aplikacji SaaS w usłudze Azure Active Directory](https://aka.ms/appstutorial).

Aby uzyskać więcej informacji o ofercie swoją aplikację w galerii aplikacji Azure AD, zobacz [umieść swoją aplikację w galerii aplikacji usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing).

---
 
### <a name="new-step-by-step-deployment-guides-for-azure-active-directory"></a>Nowe przewodniki krok po kroku wdrożenia usługi Azure Active Directory

**Typ:** Nowa funkcja  
**Kategoria usług:** Inne  
**Możliwości produktu:** Katalog
 
Nowy, krok po kroku wskazówki dotyczące wdrażania usługi Azure Active Directory (Azure AD), takie jak Samoobsługowe resetowanie haseł (SSPR), pojedynczego logowania jednokrotnego (SSO), dostępu warunkowego (CA), serwer proxy aplikacji, aprowizacja Active Directory Federation Services (ADFS) do użytkowników Uwierzytelnianie przekazywane (PTA) i usług AD FS do synchronizacji skrótów haseł (wersji).

Aby wyświetlić wskazówki dotyczące wdrażania, przejdź do [wskazówki dotyczące wdrażania tożsamości](https://aka.ms/DeploymentPlans) repozytorium w witrynie GitHub. Aby przekazać opinię na temat wskazówki dotyczące wdrażania, użyj [wdrożenia planu forum z opiniami](https://aka.ms/deploymentplanfeedback). Jeśli masz jakieś pytania dotyczące wskazówki dotyczące wdrażania, skontaktuj się z nami pod adresem [IDGitDeploy](mailto:idgitdeploy@microsoft.com).

---

### <a name="enterprise-applications-search---load-more-apps"></a>Aplikacje dla przedsiębiorstw wyszukiwanie - ładowanie większej liczby aplikacji

**Typ:** Nowa funkcja  
**Kategoria usług:** Aplikacje dla przedsiębiorstw  
**Możliwości produktu:** Logowanie jednokrotne
 
Problem ze znalezieniem informacji aplikacji / jednostki usług? Dodaliśmy możliwość ładowania większej liczby aplikacji w aplikacjach firmowych listę wszystkich aplikacji. Domyślnie pokazujemy 20 aplikacji. Możesz teraz kliknąć Link, **Załaduj więcej** Aby wyświetlić dodatkowe aplikacje. 

---
 
### <a name="the-may-release-of-aadconnect-contains-a-public-preview-of-the-integration-with-pingfederate-important-security-updates-many-bug-fixes-and-new-great-new-troubleshooting-tools"></a>May wersji AADConnect zawiera publiczną wersję zapoznawczą integracji z serwerem PingFederate, ważne aktualizacje zabezpieczeń, wiele poprawek usterek i nowe great nowych narzędzi do rozwiązywania problemów. 

**Typ:** Zmieniona funkcja  
**Kategoria usług:** AD Connect  
**Możliwości produktu:** Zarządzanie cyklem życia tożsamości
 
May wersji AADConnect zawiera publiczną wersję zapoznawczą integracji z serwerem PingFederate, ważne aktualizacje zabezpieczeń, wiele poprawek usterek i nowe great nowych narzędzi do rozwiązywania problemów. Można znaleźć informacje o wersji [tutaj](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-version-history#118190).

---

### <a name="azure-ad-access-reviews-auto-apply"></a>Przeglądy dostępu w usłudze Azure AD: automatycznie Zastosuj

**Typ:** Zmieniona funkcja  
**Kategoria usług:** Przeglądy dostępu  
**Możliwości produktu:** Nadzór

Przeglądy dostępu dotyczące dostępu grupy i aplikacje są teraz ogólnie dostępne w ramach usługi Azure AD Premium P2. Administrator może skonfigurować, aby automatycznie zastosować zmiany wprowadzone przez recenzenta do tej grupy lub aplikacji, ponieważ zakończeniu przeglądu dostępu. Administrator może również określić, co się dzieje z przedłużenia dostępu użytkownika, jeśli recenzenci nie odpowie, spowodować usunięcie dostępu, móc chronić dostęp do lub rekomendacje systemu. 

---

### <a name="id-tokens-can-no-longer-be-returned-using-the-query-responsemode-for-new-apps"></a>Tokeny Identyfikatora już nie mogą być zwracane w przypadku nowych aplikacji przy użyciu response_mode zapytania. 

**Typ:** Zmieniona funkcja  
**Kategoria usług:** Uwierzytelnienia (logowania)  
**Możliwości produktu:** Uwierzytelnianie użytkowników
 
Aplikacje utworzone na lub po 25 kwietnia 2018 r. nie będzie już mógł zażądać **id_token** przy użyciu **zapytania** response_mode.  To umożliwia wbudowanej usługi Azure AD ze specyfikacjami OIDC, a także pomaga zmniejszyć obszar ataków aplikacji.  Aplikacje utworzone przed 25 kwietnia 2018 r. nie jest zablokowany za pomocą **zapytania** response_mode przy użyciu elementu response_type z **id_token**.  Błąd zwrócony podczas żądania elementu id_token z usługi AAD, **AADSTS70007: "query" nie jest obsługiwaną wartością "response_mode" podczas żądania tokenu**.

**Fragmentu** i **form_post** response_modes w dalszym ciągu działać — podczas tworzenia nowych obiektów aplikacji (na przykład w przypadku użycia serwera Proxy aplikacji), upewnij się użycie jednej z tych response_modes, przed utworzeniem Nowa aplikacja.  

---
 
## <a name="april-2018"></a>Kwiecień 2018 r. 

### <a name="azure-ad-b2c-access-token-are-ga"></a>Usługa Azure AD B2C Token dostępu są ogólnie dostępne

**Typ:** Nowa funkcja  
**Kategoria usług:** B2C — Consumer Identity Management  
**Możliwości produktu:** B2B/B2C 

Teraz możesz uzyskać dostęp do interfejsów API sieci Web zabezpieczony za pomocą usługi Azure AD B2C przy użyciu tokenów dostępu. Ta funkcja jest przenoszona z publicznej wersji zapoznawczej do GA. Udoskonalono środowisko interfejsu użytkownika do konfigurowania aplikacji usługi Azure AD B2C i interfejsów API sieci web i wprowadzono drobne ulepszenia.
 
Aby uzyskać więcej informacji, zobacz [usługi Azure AD B2C: Żądania tokenów dostępu](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-access-tokens).

---

### <a name="test-single-sign-on-configuration-for-saml-based-applications"></a>Testowanie konfiguracji pojedynczego logowania jednokrotnego dla aplikacji opartej na SAML

**Typ:** Nowa funkcja  
**Kategoria usług:** Aplikacje dla przedsiębiorstw  
**Możliwości produktu:** Logowanie jednokrotne

Konfigurowanie aplikacji opartej na SAML logowania jednokrotnego, możesz przetestować integrację na stronie konfiguracji. Jeśli wystąpi błąd podczas logowania, możesz dostarczyć błąd w środowisku testowania, a usługa Azure AD zapewnia czynności rozwiązujących problem do rozwiązania konkretnego problemu.

Aby uzyskać więcej informacji, zobacz:

- [Konfigurowanie logowania jednokrotnego do aplikacji, które nie znajdują się w galerii aplikacji Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-custom-apps)
- [Jak debugować logowanie jednokrotne SAML w aplikacjach w usłudze Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-debugging)

---
 
### <a name="azure-ad-terms-of-use-now-has-per-user-reporting"></a>Usługa Azure AD warunki użytkowania ma teraz na użytkownika raportowania

**Typ:** Nowa funkcja  
**Kategoria usług:** Warunki użytkowania  
**Możliwości produktu:** Zgodność
 
Administratorzy mogą teraz wybrać danego warunków użytkowania i wyświetlić wszystkich użytkowników, które wyrażono zgodę na czy warunki użytkowania i co daty/godziny go miało miejsce.

Aby uzyskać więcej informacji, zobacz [są wyposażone w usłudze Azure AD z warunkami użytkowania](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use).

---
 
### <a name="azure-ad-connect-health-risky-ip-for-ad-fs-extranet-lockout-protection"></a>Azure AD Connect Health: Ryzykowny adres IP do ochrony blokowanie ekstranetu w usługach AD FS 

**Typ:** Nowa funkcja  
**Kategoria usług:** Inne  
**Możliwości produktu:** Monitorowanie i raportowanie

Teraz obsługuje możliwość wykrywania adresów IP adresy, która przekracza próg nieudanych logowań U/P na podstawie godzinową lub dzienną programu Connect Health. Dostępne są następujące funkcje oferowane przez tę funkcję:

- Kompleksowy raport przedstawiający adres IP i liczbę nieudanych prób logowania generowane na podstawie godziny/dnia z progiem można dostosowywać.
- Alerty bazujące na poczcie e-mail, gdy określony adres IP został przekroczony próg nieudanych logowań U/P na podstawie godziny/dnia.
- Opcja pobierania w celu przeprowadzenia szczegółowej analizy danych

Aby uzyskać więcej informacji, zobacz [raport ryzykownych adresów IP](https://aka.ms/aadchriskyip).

---
 
### <a name="easy-app-config-with-metadata-file-or-url"></a>Konfiguracja aplikacji łatwo z pliku metadanych lub adres URL

**Typ:** Nowa funkcja  
**Kategoria usług:** Aplikacje dla przedsiębiorstw  
**Możliwości produktu:** Logowanie jednokrotne

Na stronie aplikacje przedsiębiorstwa administratorzy przekazać plik metadanych SAML do skonfigurowania logowania jednokrotnego SAML na podstawie aplikacji usługi AAD i spoza galerii.

Ponadto umożliwia adres URL metadanych Federacji aplikacji usługi Azure AD Konfigurowanie logowania jednokrotnego przy użyciu aplikacji docelowej.

Aby uzyskać więcej informacji, zobacz [Konfigurowanie logowania jednokrotnego do aplikacji, które nie znajdują się w galerii aplikacji usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-custom-apps).

---

### <a name="azure-ad-terms-of-use-now-generally-available"></a>Usługa Azure AD z warunkami użytkowania jest teraz ogólnie dostępna

**Typ:** Nowa funkcja  
**Kategoria usług:** Warunki użytkowania  
**Możliwości produktu:** Zgodność
 

Usługa Azure AD warunki użytkowania przeniósł z publicznej wersji zapoznawczej jest ogólnie dostępna.

Aby uzyskać więcej informacji, zobacz [są wyposażone w usłudze Azure AD z warunkami użytkowania](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use).

---

### <a name="allow-or-block-invitations-to-b2b-users-from-specific-organizations"></a>Zezwalaj na zaproszenia lub blokowanie ich dla użytkowników B2B z określonym organizacjom

**Typ:** Nowa funkcja  
**Kategoria usług:** B2B  
**Możliwości produktu:** B2B/B2C
 

Teraz można określić organizacji partnerskich, który ma być udostępnianie i współpraca przy użyciu we współpracy B2B usługi Azure AD. Aby to zrobić, możesz utworzyć listę konkretnych dozwolonych lub zablokowanych domen. Gdy domeny jest zablokowane, za pomocą tych funkcji, pracownikom nie może wysyłać zaproszenia do osób w danej domenie.

Dzięki temu można kontrolować dostęp do zasobów, umożliwiając bezproblemowe środowisko dla zatwierdzonych użytkowników jednocześnie.

Ta funkcja współpracy B2B jest dostępna dla wszystkich klientów usługi Azure Active Directory i może służyć w połączeniu z funkcji usługi Azure AD Premium, takich jak warunkowe ochrony dostępu i tożsamości uzyskać większą kontrolę nad kiedy i jak firm zewnętrznych użytkowników w i uzyskać dostęp.

Aby uzyskać więcej informacji, zobacz [dozwolonych lub zablokowanych zaproszeń do użytkowników B2B z określonym organizacjom](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-allow-deny-list).

---
 
### <a name="new-federated-apps-available-in-azure-ad-app-gallery"></a>Nowe aplikacje federacyjne dostępne w galerii aplikacji usługi Azure AD

**Typ:** Nowa funkcja  
**Kategoria usług:** Aplikacje dla przedsiębiorstw  
**Możliwości produktu:** Integracje innych firm

W kwietniu 2018 Dodaliśmy obsługę tych 13 nowe aplikacje z Federacją do galerii aplikacji:

Kryterium HCM, [FiscalNote](https://docs.microsoft.com/azure/active-directory/active-directory-saas-fiscalnote-tutorial), [wpis tajny serwera (lokalnie)](https://docs.microsoft.com/azure/active-directory/active-directory-saas-secretserver-on-premises-tutorial), [sygnałów dynamicznych](https://docs.microsoft.com/azure/active-directory/active-directory-saas-dynamicsignal-tutorial), [mindWireless](https://docs.microsoft.com/azure/active-directory/active-directory-saas-mindwireless-tutorial), [schematu organizacyjnego Teraz](https://docs.microsoft.com/azure/active-directory/active-directory-saas-orgchartnow-tutorial), [Ziflow](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ziflow-tutorial), [monitora wydajności AppNeta](https://docs.microsoft.com/azure/active-directory/active-directory-saas-appneta-tutorial), [Elium](https://docs.microsoft.com/azure/active-directory/active-directory-saas-elium-tutorial) , [Fluxx Labs](https://docs.microsoft.com/azure/active-directory/active-directory-saas-fluxxlabs-tutorial), [ Cisco, chmura](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ciscocloud-tutorial), półki, [SafetyNet](https://docs.microsoft.com/azure/active-directory/active-directory-saas-safetynet-tutorial)

Aby uzyskać więcej informacji o aplikacjach, zobacz [integracji aplikacji SaaS w usłudze Azure Active Directory](https://aka.ms/appstutorial).

Aby uzyskać więcej informacji o ofercie swoją aplikację w galerii aplikacji Azure AD, zobacz [umieść swoją aplikację w galerii aplikacji usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing).

---
 
### <a name="grant-b2b-users-in-azure-ad-access-to-your-on-premises-applications-public-preview"></a>Użytkowników B2B przydział w usłudze Azure AD dostęp do aplikacji w środowisku lokalnym (publiczna wersja zapoznawcza)

**Typ:** Nowa funkcja  
**Kategoria usług:** B2B  
**Możliwości produktu:** B2B/B2C

Jako organizacja, która używa funkcji współpracy B2B usługi Azure Active Directory (Azure AD), aby zaprosić użytkowników-gości z organizacji partnerskich do usługi Azure AD teraz możesz podać tych użytkowników B2B dostępu do aplikacji lokalnych. Te aplikacje w środowisku lokalnym za pomocą uwierzytelnianie oparte na protokole SAML lub zintegrowanego Windows Authentication (Zintegrowane) ograniczonego delegowania protokołu Kerberos (KCD).

Aby uzyskać więcej informacji, zobacz [użytkowników B2B przydział w usłudze Azure AD dostęp do aplikacji w środowisku lokalnym](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-hybrid-cloud-to-on-premises).

---
 
### <a name="get-sso-integration-tutorials-from-the-azure-marketplace"></a>Pobierz samouczki integracji logowania jednokrotnego w witrynie Azure Marketplace

**Typ:** Zmieniona funkcja  
**Kategoria usług:** Inne  
**Możliwości produktu:** Integracje innych firm

Jeśli aplikacja, która znajduje się w [portalu Azure marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps?page=1) obsługuje logowanie jednokrotne, klikając na podstawie protokołu SAML **Pobierz teraz** zapewnia samouczkiem integracji skojarzone z tej aplikacji. 

---

### <a name="faster-performance-of-azure-ad-automatic-user-provisioning-to-saas-applications"></a>Szybsza wydajność usługi Azure AD automatyczna aprowizacja użytkowników do aplikacji SaaS

**Typ:** Zmieniona funkcja  
**Kategoria usług:** Aprowizowanie aplikacji  
**Możliwości produktu:** Integracje innych firm
 
Wcześniej klienci korzystający z przypisywania łączników aplikacji SaaS (na przykład usługi Salesforce, ServiceNow i pole) użytkowników usługi Azure Active Directory mogą wystąpić spadek wydajności w przypadku ich dzierżawami usługi Azure AD zawiera ponad 100 000 użytkowników połączonych i grupy i ich były używane przypisań użytkowników i grup użytkowników, którzy powinny być aprowizowany.

2 kwietnia 2018 r. istotnie poprawiającą wydajność ulepszenia zostały wdrożone do inicjowania obsługi usługi Azure AD, która znacznie skrócić czas potrzebny do wykonania początkowej synchronizacji od usługi Azure Active Directory i aplikacji SaaS docelowej.

W rezultacie wielu klientów, miały synchronizacji początkowej do aplikacji, które miały liczbę dni lub nigdy nie zakończona, teraz są kończone w ciągu kilku minut lub godzin.

Aby uzyskać więcej informacji, zobacz [co się dzieje podczas inicjowania obsługi administracyjnej?](https://docs.microsoft.com/azure/active-directory/active-directory-saas-app-provisioning#what-happens-during-provisioning)

---

### <a name="self-service-password-reset-from-windows-10-lock-screen-for-hybrid-azure-ad-joined-machines"></a>Samodzielnie resetując hasło z ekranu blokady systemu Windows 10 do hybrydowej usługi Azure AD przyłączone maszyn

**Typ:** Zmieniona funkcja  
**Kategoria usług:** Samodzielne resetowanie hasła  
**Możliwości produktu:** Uwierzytelnianie użytkowników
 
Zaktualizowaliśmy obejmuje pomocy technicznej dla maszyn, które są dołączone do usługi Azure AD hybrydowej funkcji samoobsługowego resetowania HASEŁ systemu Windows 10. Ta funkcja jest dostępna w systemie Windows 10 RS4 umożliwia użytkownikom do zresetowania swojego hasła na ekranie blokady komputera systemu Windows 10. Użytkownicy, którzy są włączone i zarejestrowany do samoobsługowego resetowania haseł mogą korzystać z tej funkcji.

Aby uzyskać więcej informacji, zobacz [haseł usługi Azure AD z ekranu logowania](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-sspr-windows).

---

## <a name="march-2018"></a>Marca 2018 r.
 
### <a name="certificate-expire-notification"></a>Certyfikat wygasa powiadomień

**Typ:** Ustalone  
**Kategoria usług:** Aplikacje dla przedsiębiorstw  
**Możliwości produktu:** Logowanie jednokrotne
 
Usługa Azure AD wysyła powiadomienie, gdy certyfikat dla galerii lub aplikacji spoza galerii, wkrótce wygaśnie. 

Niektórzy użytkownicy nie otrzymał powiadomienia aplikacji dla przedsiębiorstw skonfigurowane dla opartej na SAML logowania jednokrotnego. Ten problem został rozwiązany. Usługa Azure AD wysyła powiadomienie w przypadku certyfikatów wygaśnie za 7, 30 i 60 dni. Jesteś w stanie wyświetlić to zdarzenie w dziennikach inspekcji. 

Aby uzyskać więcej informacji, zobacz:

- [Zarządzanie certyfikatami dla federacyjnego logowania jednokrotnego w usłudze Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-sso-certs)
- [Raporty dotyczące inspekcji w portalu usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-audit-logs)
 
---
 
### <a name="twitter-and-github-identity-providers-in-azure-ad-b2c"></a>Usługi Twitter i GitHub dostawców tożsamości w usłudze Azure AD B2C

**Typ:** Nowa funkcja  
**Kategoria usług:** B2C — Consumer Identity Management  
**Możliwości produktu:** B2B/B2C
 
Możesz teraz dodać Twitter lub GitHub jako dostawcy tożsamości w usłudze Azure AD B2C. Twitter jest przenoszona z publicznej wersji zapoznawczej do ogólnego udostępnienia. GitHub został wydany w publicznej wersji zapoznawczej.

Aby uzyskać więcej informacji, zobacz [czym jest współpraca B2B w usłudze Azure AD?](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b).
 
---

### <a name="restrict-browser-access-using-intune-managed-browser-with-azure-ad-application-based-conditional-access-for-ios-and-android"></a>Ogranicz dostęp za pomocą przeglądarki przy użyciu usługi Intune Managed Browser przy użyciu dostępu warunkowego opartego na aplikacji usługi Azure AD dla systemów iOS i Android

**Typ:** Nowa funkcja  
**Kategoria usług:** Dostęp warunkowy  
**Możliwości produktu:** Ochrona i zabezpieczanie tożsamości
 
**Teraz w publicznej wersji zapoznawczej!**

**Intune Managed Browser logowania jednokrotnego:** Pracownikom służy logowanie jednokrotne z klientów natywnych (takich jak Microsoft Outlook) i Intune Managed Browser dla wszystkich aplikacji platformy Azure podłączonej do usługi AD.

**Obsługa dostępu warunkowego w przeglądarce zarządzane przez usługę Intune:** Możesz teraz wymagać pracownikom korzystać z usługi Intune Managed browser, za pomocą zasad dostępu warunkowego opartego na aplikacji.

Dowiedz się więcej w naszym [wpis w blogu](https://cloudblogs.microsoft.com/enterprisemobility/2018/03/15/the-intune-managed-browser-now-supports-azure-ad-sso-and-conditional-access/).

Aby uzyskać więcej informacji, zobacz:

- [Konfigurowanie dostępu warunkowego opartego na aplikacji](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)

- [Konfigurowanie zasad programu managed browser](https://aka.ms/managedbrowser)  

---
 
### <a name="app-proxy-cmdlets-in-powershell-ga-module"></a>Polecenia cmdlet serwera Proxy aplikacji, w Module programu Powershell w wersji Ogólnodostępnej

**Typ:** Nowa funkcja  
**Kategoria usług:** Serwer proxy aplikacji  
**Możliwości produktu:** Kontrola dostępu
 
Obsługa polecenia cmdlet serwera Proxy aplikacji jest teraz moduł programu Powershell w wersji ogólnie dostępnej. Wymagane jest aktualne informacje o programie Powershell modułów — Jeżeli zostanie więcej niż roku za zaporą, niektóre polecenia cmdlet mogą przestać działać. 

Aby uzyskać więcej informacji, zobacz [AzureAD](https://docs.microsoft.com/powershell/module/Azuread/?view=azureadps-2.0).
 
---
 
### <a name="office-365-native-clients-are-supported-by-seamless-sso-using-a-non-interactive-protocol"></a>Natywne klienci usługi Office 365 są obsługiwane przez bezproblemowe logowanie Jednokrotne przy użyciu protokołu nieinterakcyjnym

**Typ:** Nowa funkcja  
**Kategoria usług:** Uwierzytelnienia (logowania)  
**Możliwości produktu:** Uwierzytelnianie użytkowników
 
Użytkownika przy użyciu natywnych klientów usługi Office 365 (wersja 16.0.8730.xxxx i nowsze wersje) Pobierz dyskretnej środowisko logowania jednokrotnego przy użyciu bezproblemowego logowania jednokrotnego. Te są obsługiwane przez dodanie nieinterakcyjnych protokołu (WS-Trust) do usługi Azure AD.

Aby uzyskać więcej informacji, zobacz [sposób logowania na komputerze klienckim natywne z pracą bezproblemowe logowanie Jednokrotne?](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso-how-it-works#how-does-sign-in-on-a-native-client-with-seamless-sso-work)
 
---

### <a name="users-get-a-silent-sign-on-experience-with-seamless-sso-if-an-application-sends-sign-in-requests-to-azure-ads-tenant-endpoints"></a>Użytkownicy uzyskują dyskretnej logowania jednokrotnego, za pomocą bezproblemowego logowania jednokrotnego, jeśli aplikacja przesyła żądania rejestrowania do punktów końcowych dzierżawy usługi Azure AD

**Typ:** Nowa funkcja  
**Kategoria usług:** Uwierzytelnienia (logowania)  
**Możliwości produktu:** Uwierzytelnianie użytkowników
 
Użytkownicy uzyskują dyskretnej logowania jednokrotnego, za pomocą bezproblemowego logowania jednokrotnego, jeśli aplikacja (na przykład `https://contoso.sharepoint.com`) wysyła żądania rejestrowania do punktów końcowych z dzierżawy usługi Azure AD — oznacza to, `https://login.microsoftonline.com/contoso.com/<..>` lub `https://login.microsoftonline.com/<tenant_ID>/<..>` — zamiast wspólnego punktu końcowego usługi Azure AD (`https://login.microsoftonline.com/common/<...>`).

Aby uzyskać więcej informacji, zobacz [usługi Azure Active Directory bezproblemowe logowanie jednokrotne](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso). 

---
 
### <a name="need-to-add-only-one-azure-ad-url-instead-of-two-urls-previously-to-users-intranet-zone-settings-to-roll-out-seamless-sso"></a>Należy dodać tylko jeden adres URL usługi Azure AD, zamiast wcześniej dwa adresy URL do ustawienia strefy Intranet użytkowników to dystrybucję bezproblemowe logowanie Jednokrotne

**Typ:** Nowa funkcja  
**Kategoria usług:** Uwierzytelnienia (logowania)  
**Możliwości produktu:** Uwierzytelnianie użytkowników
 
Aby rozwinąć bezproblemowe logowanie Jednokrotne dla użytkowników, należy dodać tylko jeden usługi Azure AD adres URL użytkowników w sieci Intranet ustawienia strefy za pomocą zasad grupy w usłudze Active Directory: `https://autologon.microsoftazuread-sso.com`. Wcześniej klienci musieli dodać dwa adresy URL.

Aby uzyskać więcej informacji, zobacz [usługi Azure Active Directory bezproblemowe logowanie jednokrotne](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso). 
 
---
 
### <a name="new-federated-apps-available-in-azure-ad-app-gallery"></a>Nowe aplikacje federacyjne dostępne w galerii aplikacji usługi Azure AD

**Typ:** Nowa funkcja  
**Kategoria usług:** Aplikacje dla przedsiębiorstw  
**Możliwości produktu:** Integracje innych firm

W marcu 2018 r. Dodaliśmy obsługę tych 15 nowe aplikacje z Federacją do galerii aplikacji:

[Boxcryptor](https://docs.microsoft.com/azure/active-directory/active-directory-saas-boxcryptor-tutorial), [CylancePROTECT](https://docs.microsoft.com/azure/active-directory/active-directory-saas-cylanceprotect-tutorial), Wrike, [SignalFx](https://docs.microsoft.com/azure/active-directory/active-directory-saas-signalfx-tutorial), Asystenta przez FirstAgenda, [YardiOne](https://docs.microsoft.com/azure/active-directory/active-directory-saas-yardione-tutorial), Vtiger CRM, inwink, [amplitudy](https://docs.microsoft.com/azure/active-directory/active-directory-saas-amplitude-tutorial), [Spacio](https://docs.microsoft.com/azure/active-directory/active-directory-saas-spacio-tutorial), [ContractWorks](https://docs.microsoft.com/azure/active-directory/active-directory-saas-contractworks-tutorial), [Bersin](https://docs.microsoft.com/azure/active-directory/active-directory-saas-bersin-tutorial), [Mercell](https://docs.microsoft.com/azure/active-directory/active-directory-saas-mercell-tutorial), [Trisotech cyfrowy Enterprise Server](https://docs.microsoft.com/azure/active-directory/active-directory-saas-trisotechdigitalenterpriseserver-tutorial), [chmury Qumu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-qumucloud-tutorial).
 
Aby uzyskać więcej informacji o aplikacjach, zobacz [integracji aplikacji SaaS w usłudze Azure Active Directory](https://aka.ms/appstutorial).

Aby uzyskać więcej informacji o ofercie swoją aplikację w galerii aplikacji Azure AD, zobacz [umieść swoją aplikację w galerii aplikacji usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing). 

---
 
### <a name="pim-for-azure-resources-is-generally-available"></a>Usługa PIM dla zasobów platformy Azure jest ogólnie dostępna

**Typ:** Nowa funkcja  
**Kategoria usług:** Privileged Identity Management  
**Możliwości produktu:** Privileged Identity Management
 
Korzystania z usługi Azure AD Privileged Identity Management dla ról w katalogu teraz umożliwia czasowo dostęp i możliwości przypisania w usłudze PIM dla ról zasobów platformy Azure, takich jak subskrypcji, grupy zasobów, maszyny wirtualne i innych zasobów, obsługiwane przez usługi Azure Resource Manager. Wymuszanie uwierzytelniania wieloskładnikowego podczas aktywacji ról Just-In-Time i Planuj aktywacje w połączeniu z systemem windows zatwierdzone zmiany. Ponadto w tej wersji dodano ulepszenia nie jest dostępna w publicznej wersji zapoznawczej, w tym zaktualizowanego interfejsu użytkownika, przepływy pracy zatwierdzania i możliwość rozszerzania role, które wkrótce wygasną i Odnów wygasłe role.

Aby uzyskać więcej informacji, zobacz [PIM dla zasobów platformy Azure (wersja zapoznawcza)](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/azure-pim-resource-rbac)
 
---
 
### <a name="adding-optional-claims-to-your-apps-tokens-public-preview"></a>Dodawanie opcjonalnych oświadczeń do tokenów aplikacji (publiczna wersja zapoznawcza)

**Typ:** Nowa funkcja  
**Kategoria usług:** Uwierzytelnienia (logowania)  
**Możliwości produktu:** Uwierzytelnianie użytkowników
 
Aplikacja usługi Azure AD mogą teraz oświadczenia żądań, niestandardowych lub opcjonalnie tokenów Jwt lub SAML tokenów.  To są oświadczenia dotyczące użytkownika lub dzierżawy, które nie są uwzględnione w tokenie, z powodu ograniczeń rozmiaru lub stosowania domyślnie.  Ta funkcja jest obecnie w publicznej wersji zapoznawczej dla aplikacji usługi Azure AD w wersji 1.0 i 2.0 punktach końcowych.  Zobacz dokumentację, aby uzyskać informacje na oświadczeniach, jakie można dodać i jak edytować manifest aplikacji do ich zażądać.  

Aby uzyskać więcej informacji, zobacz [opcjonalnie oświadczeń w usłudze Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-optional-claims).
 
---
 
### <a name="azure-ad-supports-pkce-for-more-secure-oauth-flows"></a>Usługa Azure AD obsługuje PKCE bardziej bezpieczne przepływy OAuth

**Typ:** Nowa funkcja  
**Kategoria usług:** Uwierzytelnienia (logowania)  
**Możliwości produktu:** Uwierzytelnianie użytkowników
 
Dokumentacja usługi Azure AD zostały zaktualizowane należy pamiętać, obsługa PKCE, który pozwala na bardziej bezpiecznej komunikacji w przepływie przyznawania kodu autoryzacji usługi uwierzytelniania OAuth 2.0.  Code_challenges S256 i w postaci zwykłego tekstu są obsługiwane dla punktów końcowych w wersji 1.0 i 2.0. 

Aby uzyskać więcej informacji, zobacz [żądania kodu autoryzacji](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-protocols-oauth-code#request-an-authorization-code). 
 
---
 
### <a name="support-for-provisioning-all-user-attribute-values-available-in-the-workday-getworkers-api"></a>Obsługa funkcji obsługi administracyjnej wszystkie wartości atrybutów użytkownika dostępny w interfejsie API Get_Workers Workday

**Typ:** Nowa funkcja  
**Kategoria usług:** Aprowizowanie aplikacji  
**Możliwości produktu:** Integracje innych firm
 
Publiczna wersja zapoznawcza aprowizacji dla ruchu przychodzącego z produktu Workday do usługi Active Directory i usługi Azure AD obecnie obsługuje możliwość wyodrębniania i aprowizacji w interfejsie API Get_Workers Workday wszystkie wartości atrybutów. Spowoduje to dodanie obsługuje setek dodatkowe standardowe i atrybutów niestandardowych, które są dostarczane z wstępną wersję produktu Workday wykraczających dla ruchu przychodzącego inicjowania obsługi administracyjnej łącznika.

Aby uzyskać więcej informacji, zobacz: [Dostosowywanie listy atrybutów użytkownika w produkcie Workday](https://docs.microsoft.com/azure/active-directory/active-directory-saas-workday-inbound-tutorial#customizing-the-list-of-workday-user-attributes)

---

### <a name="changing-group-membership-from-dynamic-to-static-and-vice-versa"></a>Zmiana członkostwa w grupie z dynamicznej na statyczną i na odwrót

**Typ:** Nowa funkcja  
**Kategoria usług:** Zarządzanie grupami  
**Możliwości produktu:** Współpraca
 
Istnieje możliwość zmienić sposób zarządzania członkostwa w grupie. Jest to przydatne, gdy chcesz zachować taką samą nazwę grupy i identyfikator w systemie, dzięki czemu wszystkie istniejące odwołania do tej grupy są nadal ważne. Tworzenie nowej grupy wymaga aktualizacji te odwołania.
Zaktualizowaliśmy Centrum administracyjnego usługi AD Azure, aby obsługiwać tę funkcję. Teraz klienci mogą przekształcać istniejące grupy dynamiczne członkostwo przypisane członkostwo i na odwrót. Nadal dostępne są również istniejące polecenia cmdlet programu PowerShell.

Aby uzyskać więcej informacji, zobacz [reguły członkostwa dynamicznego dla grup w usłudze Azure Active Directory](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership)

---

### <a name="improved-sign-out-behavior-with-seamless-sso"></a>Ulepszone zachowanie wylogowania z bezproblemowe logowanie Jednokrotne

**Typ:** Zmieniona funkcja  
**Kategoria usług:** Uwierzytelnienia (logowania)  
**Możliwości produktu:** Uwierzytelnianie użytkowników
 
Wcześniej nawet wtedy, gdy użytkownicy wylogowanie jawnie aplikacji zabezpieczonej przez usługi Azure AD, ich będzie automatycznie zalogować się ponownie przy użyciu bezproblemowe logowanie Jednokrotne gdy one próbował uzyskać dostęp do aplikacji usługi Azure AD, ponownie w ramach ich sieci corpnet za pomocą ich urządzeń przyłączonych do domeny. Dzięki tej zmianie Wyloguj jest obsługiwane.  Dzięki temu użytkownicy mogą wybrać tej samej lub innej usługi Azure AD konto do zalogowania Wstecz, a nie są automatycznie zalogować się przy użyciu bezproblemowe logowanie Jednokrotne.

Aby uzyskać więcej informacji, zobacz [usługi Azure Active Directory bezproblemowe logowanie jednokrotne](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso)
 
---
 
### <a name="application-proxy-connector-version-154020-released"></a>Wersja łącznika serwera Proxy aplikacji 1.5.402.0 wydania

**Typ:** Zmieniona funkcja  
**Kategoria usług:** Serwer proxy aplikacji  
**Możliwości produktu:** Ochrona i zabezpieczanie tożsamości
 
Ta wersja łącznika jest stopniowo wdrażana za pośrednictwem listopada. Nowa wersja łącznika obejmuje następujące zmiany:

- Łącznik teraz ustawia pliki cookie z poziomu domeny zamiast tego poziomu domeny podrzędnej. To zapewnia płynne logowania jednokrotnego i pozwala uniknąć monitów uwierzytelniania nadmiarowe.
- Obsługa żądań Kodowanie fragmentaryczne
- Monitorowanie kondycji łącznika ulepszone 
- Kilka poprawek błędów i ulepszenia w zakresie stabilności

Aby uzyskać więcej informacji, zobacz [łączników serwera Proxy aplikacji usługi AD Azure zrozumienie](https://docs.microsoft.com/azure/active-directory/application-proxy-understand-connectors).
 
---

## <a name="february-2018"></a>Lutego 2018 r.
 
### <a name="improved-navigation-for-managing-users-and-groups"></a>Ulepszona nawigacja do zarządzania użytkownikami i grupami

**Typ:** Plan zmiany  
**Kategoria usług:** Zarządzanie katalogami  
**Możliwości produktu:** Katalog

Usprawniono środowisko nawigacji do zarządzania użytkownikami i grupami. Możesz teraz przechodzić od omówienia directory bezpośrednio do listy wszystkich użytkowników, których łatwiejszy dostęp do listy usuniętych użytkowników. Możesz także przejść od omówienia directory bezpośrednio do listy wszystkich grup, łatwiejszy dostęp do grupy zarządzania. A także na stronie Przegląd w katalogu, możesz wyszukać użytkowników, grupy, aplikacja dla przedsiębiorstw lub rejestracja aplikacji. 

---

### <a name="availability-of-sign-ins-and-audit-reports-in-microsoft-azure-operated-by-21vianet-azure-china-21vianet"></a>Dostępność logowania i inspekcji raporty w systemie Microsoft Azure, obsługiwana przez firmę 21Vianet (Azure China 21Vianet)

**Typ:** Nowa funkcja  
**Kategoria usług:** Azure Stack  
**Możliwości produktu:** Monitorowanie i raportowanie

Raporty dziennika usługi Azure AD Activity są teraz dostępne w systemie Microsoft Azure, świadczona przez firmę 21Vianet (Azure China 21Vianet) wystąpień. Uwzględnione są następujące dzienniki:

- **Dzienniki aktywności logowania** — obejmuje wszystkie logowania dzienniki skojarzony z dzierżawą.

- **Samoobsługowe dzienników inspekcji haseł** — obejmuje wszystkie dzienniki inspekcji funkcji samoobsługowego resetowania HASEŁ.

- **Przeprowadź inspekcję zarządzania w katalogu dzienników** — obejmuje wszystkie dzienniki inspekcji związanych z zarządzaniem katalogu użytkownika zarządzania, zarządzanie aplikacjami i inne.

Za pomocą tych dzienników możesz uzyskać szczegółowe informacje dotyczące sposobu działania środowiska. Na podstawie udostępnionych danych można:

- Określ, jak Twoje aplikacje i usługi są wykorzystywane przez użytkowników.

- Rozwiązywanie problemów, uniemożliwiając użytkownikom Rozpoczynanie pracy.

Aby uzyskać więcej informacji na temat używania tych raportów, zobacz [raportowania usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-azure-portal).

---

### <a name="use-report-reader-role-non-admin-role-to-view-azure-ad-activity-reports"></a>Użyj roli "Czytnik raportów" (rola nieadministracyjna), aby wyświetlić raporty aktywności usługi Azure AD

**Typ:** Nowa funkcja  
**Kategoria usług:** Raportowanie  
**Możliwości produktu:** Monitorowanie i raportowanie

Jako część opinie klientów, aby włączyć role bez uprawnień administratora na dostęp do działania usługi Azure AD dzienników, udostępniliśmy możliwość dla użytkowników, którzy znajdują się w roli "Czytnik raportów" dostęp do logowania i działania inspekcji w witrynie Azure portal, jak i za pomocą naszych interfejsów API programu Graph. 

Aby uzyskać więcej informacji jak używać tych raportów, zobacz [raportowania usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-azure-portal). 

---

### <a name="employeeid-claim-available-as-user-attribute-and-user-identifier"></a>Oświadczenie elementu EmployeeID dostępne jako atrybut użytkownika i identyfikator użytkownika

**Typ:** Nowa funkcja  
**Kategoria usług:** Aplikacje dla przedsiębiorstw  
**Możliwości produktu:** Logowanie jednokrotne
 
Można skonfigurować **EmployeeID** jako identyfikator użytkownika i atrybut użytkownika dla użytkowników i gości B2B w SAML logowania jednokrotnego aplikacji w interfejsie użytkownika aplikacji przedsiębiorstwa.

Aby uzyskać więcej informacji, zobacz [Dostosowywanie oświadczeń wystawionych w tokenie SAML dla aplikacji dla przedsiębiorstw w usłudze Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization).

---

### <a name="simplified-application-management-using-wildcards-in-azure-ad-application-proxy"></a>Uproszczone zarządzanie aplikacjami przy użyciu symboli wieloznacznych na serwerze Proxy aplikacji usługi Azure AD

**Typ:** Nowa funkcja  
**Kategoria usług:** Serwer proxy aplikacji  
**Możliwości produktu:** Uwierzytelnianie użytkowników
 
Aby ułatwić wdrożenie aplikacji i zmniejszyć swoje koszty administracyjne, możemy teraz obsługują możliwość publikowania aplikacji przy użyciu symboli wieloznacznych. Aby opublikować aplikację symboli wieloznacznych, postępuj zgodnie z przepływem publikowania aplikacji standardowej ale użyć symbolu wieloznacznego w adresach URL wewnętrznych i zewnętrznych.

Aby uzyskać więcej informacji, zobacz [aplikacje z symbolami wieloznacznymi serwera proxy aplikacji usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-wildcard)

---

### <a name="new-cmdlets-to-support-configuration-of-application-proxy"></a>Nowe polecenia cmdlet obsługujące konfigurację serwera Proxy aplikacji

**Typ:** Nowa funkcja  
**Kategoria usług:** Serwer proxy aplikacji  
**Możliwości produktu:** Platforma

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
**Kategoria usług:** Serwer proxy aplikacji  
**Możliwości produktu:** Platforma

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
**Kategoria usług:** AD Sync  
**Możliwości produktu:** Platforma
 
Program Azure AD Connect jest preferowanym narzędziem do synchronizowania danych między usługami Azure AD i lokalnych źródeł danych, w tym usługi Active Directory systemu Windows Server i LDAP.

>[!Important]
>Ta kompilacja wprowadza schematu i synchronizacja zmian reguły. Usługa Azure AD Connect synchronizacji wyzwala pełny Import i pełną synchronizację kroków po uaktualnieniu. Aby uzyskać informacje na temat zmienić to zachowanie, zobacz [jak mają być odroczone pełnej synchronizacji po uaktualnieniu](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-upgrade-previous-version#how-to-defer-full-synchronization-after-upgrade).

Ta wersja ma następujące aktualizacje i zmiany:

**Rozwiązane problemy**

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
 
### <a name="applications-supporting-intune-app-protection-policies-added-for-use-with-azure-ad-application-based-conditional-access"></a>Aplikacje dodane obsługi zasad ochrony aplikacji usługi Intune za pomocą dostępu warunkowego opartego na aplikacji usługi Azure AD

**Typ:** Zmieniona funkcja  
**Kategoria usług:** Dostęp warunkowy  
**Możliwości produktu:** Ochrona i zabezpieczanie tożsamości

Dodaliśmy więcej aplikacji, które obsługują dostęp warunkowy oparty na aplikacji. Teraz można uzyskać dostęp do usługi Office 365 i inne aplikacje podłączone usługi AD platformy Azure w chmurze przy użyciu tych zatwierdzonych aplikacji klienckich.

Następujące aplikacje zostaną dodane do końca lutego:

- Microsoft Power BI

- Microsoft Launcher

- Microsoft Invoicing

Aby uzyskać więcej informacji, zobacz:

- [Wymaganie aplikacji zatwierdzone klienta](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#approved-client-app-requirement)
- [Usługa Azure AD oparty na aplikacji dostępu warunkowego](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)

---

### <a name="terms-of-use-update-to-mobile-experience"></a>Aktualizacja warunków użytkowania dla przenośnych 

**Typ:** Zmieniona funkcja  
**Kategoria usług:** Warunki użytkowania  
**Możliwości produktu:** Zgodność

Po wyświetleniu warunków użytkowania, możesz teraz kliknąć **masz problemy z wyświetlaniem? Kliknij tutaj,**. Kliknięcie tego linku spowoduje otworzenie warunków użytkowania natywnie na urządzeniu. Niezależnie od tego, czy rozmiar czcionki w dokumencie lub rozmiar ekranu urządzenia możesz powiększać i czytać dokument zgodnie z potrzebami. 

---
 
## <a name="january-2018"></a>Stycznia 2018 r.
 
### <a name="new-federated-apps-available-in-azure-ad-app-gallery"></a>Nowe aplikacje federacyjne dostępne w galerii aplikacji usługi Azure AD 

**Typ:** Nowa funkcja  
**Kategoria usług:** Aplikacje dla przedsiębiorstw  
**Możliwości produktu:** Integracje innych firm

Stycznia 2018 r. zostały dodane następujące nowe aplikacje z obsługą federacji, w galerii aplikacji:

[IBM OpenPages](https://go.microsoft.com/fwlink/?linkid=864698), [oprogramowanie do zarządzania zachowania OneTrust](https://go.microsoft.com/fwlink/?linkid=861660), [Dealpath](https://go.microsoft.com/fwlink/?linkid=863526), [IriusRisk federacyjnych katalogu i [NetBenefits wierności](https://go.microsoft.com/fwlink/?linkid=864701).

Aby uzyskać więcej informacji o aplikacjach, zobacz [integracji aplikacji SaaS w usłudze Azure Active Directory](https://aka.ms/appstutorial).

Aby uzyskać więcej informacji o ofercie swoją aplikację w galerii aplikacji Azure AD, zobacz [umieść swoją aplikację w galerii aplikacji usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing). 

---
 
### <a name="sign-in-with-additional-risk-detected"></a>Zaloguj się przy użyciu dodatkowych wykryto ryzyko

**Typ:** Nowa funkcja  
**Kategoria usług:** Identity Protection  
**Możliwości produktu:** Ochrona i zabezpieczanie tożsamości

Szczegółowe informacje, uzyskasz na zdarzenie wykryte zagrożenie jest powiązany ze swoją subskrypcją usługi Azure AD. Za pomocą wersji Azure AD Premium P2 otrzymasz najbardziej szczegółowe informacje na temat wszystkie wykrycia bazowego.

Z wersją programu Azure AD Premium P1 wykrywania, które nie są objęte licencja są wyświetlane jako zdarzenie o podwyższonym ryzyku logowania z dodatkowym ryzykiem wykryto.

Aby uzyskać więcej informacji, zobacz [Zdarzenia o podwyższonym ryzyku w usłudze Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-risk-events).
 
---

### <a name="hide-office-365-applications-from-end-users-access-panels"></a>Ukryj aplikacje usługi Office 365 w panelach dostępu użytkownika końcowego

**Typ:** Nowa funkcja  
**Kategoria usług:** Moje aplikacje  
**Możliwości produktu:** Logowanie jednokrotne

Możesz teraz lepiej zarządzać jak aplikacje usługi Office 365 widoczne na panelach dostępu użytkowników za pomocą nowego ustawienia użytkownika. Ta opcja jest przydatne w przypadku zmniejszenie liczby aplikacji w panelach dostępu użytkownika, jeśli chcesz tylko wyświetlić aplikacje pakietu Office w portalu usługi Office. To ustawienie znajduje się w **ustawienia użytkownika** i jest oznaczona, **użytkownicy widzą tylko aplikacje usługi Office 365 w portalu usługi Office 365**.

Aby uzyskać więcej informacji, zobacz [ukrywanie aplikacji w środowisku użytkownika usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-hide-third-party-app).

---
 
### <a name="seamless-sign-into-apps-enabled-for-password-sso-directly-from-apps-url"></a>Bezproblemowe logowania do aplikacji objętych hasło logowania jednokrotnego, bezpośrednio z adres URL aplikacji 

**Typ:** Nowa funkcja  
**Kategoria usług:** Moje aplikacje  
**Możliwości produktu:** Logowanie jednokrotne

Moje aplikacje rozszerzenia przeglądarki jest teraz dostępna za pośrednictwem wygodne narzędzie, które zapewnia pojedynczy znak Moje aplikacje na możliwość jako skrót w przeglądarce. Po zainstalowaniu przez użytkownika zostanie wyświetlona ikona typu "gofr" w przeglądarce, który zapewnia im szybki dostęp do aplikacji. Użytkownicy mogą korzystać z zalet:

- Możliwość bezpośrednio zalogować się do logowania jednokrotnego hasła opartego na aplikacji z poziomu strony logowania w aplikacji
- Uruchamianie dowolnej aplikacji przy użyciu funkcji szybkiego wyszukiwania
- Skróty do ostatnio używane aplikacje z rozszerzenia
- Rozszerzenie jest dostępne dla programu Microsoft Edge, Chrome i Firefox.
 
Aby uzyskać więcej informacji, zobacz [Moje zabezpieczenia aplikacji logowania rozszerzenia](../user-help/my-apps-portal-end-user-access.md#download-and-install-the-my-apps-secure-sign-in-extension).

---

### <a name="azure-ad-administration-experience-in-azure-classic-portal-has-been-retired"></a>Administrowanie usługami AD systemu Azure środowisko w klasycznym portalu Azure został wycofany.

**Typ:** Przestarzałe   
**Kategoria usług:** Azure AD  
**Możliwości produktu:** Katalog

Od 8 stycznia 2018 r. administracji usługi Azure AD został wycofany doświadczenie w klasycznej witrynie Azure portal. Odbyło się w połączeniu z procedury wycofywania w klasycznym portalu Azure, sam. W przyszłości, należy użyć [Centrum administracyjnego usługi Azure AD](https://aad.portal.azure.com) wszystkie swoje portal administracji opartej na usługi Azure AD.
 
---

### <a name="the-phonefactor-web-portal-has-been-retired"></a>W portalu sieci web PhoneFactor został wycofany.

**Typ:** Przestarzałe  
**Kategoria usług:** Azure AD  
**Możliwości produktu:** Katalog
 
Od 8 stycznia 2018 r. został wycofany z portalu sieci web PhoneFactor. Ten portal został użyty do administrowania serwerem MFA, ale te funkcje zostały przeniesione do witryny Azure portal pod adresem portal.azure.com. 

Konfiguracja uwierzytelniania Wieloskładnikowego znajduje się w: **Azure Active Directory \> MFA Server**
 
---
 
### <a name="deprecate-azure-ad-reports"></a>Wycofana raporty usługi Azure AD

**Typ:** Przestarzałe  
**Kategoria usług:** Raportowanie  
**Możliwości produktu:** Zarządzanie cyklem życia tożsamości  


Ogólne udostępnienie nowej konsoli administrowanie usługą Azure Active Directory i nowe interfejsy API, które są teraz dostępne dla działań i zabezpieczeń, raportów, raportów interfejsów API w obszarze "/ reports" punktu końcowego została wycofana od końca 31 grudnia 2017 r.

**Co to jest dostępne?**

W ramach przejścia do nowej konsoli administracyjnej udostępniliśmy 2 nowe interfejsy API do pobierania dzienników aktywności usługi Azure AD. Nowy zestaw interfejsów API zapewnia bardziej zaawansowane filtrowanie i sortowanie funkcje oprócz bardziej rozbudowane kontroli i działań logowania. Wcześniej dostępne w raportach zabezpieczeń danych teraz są dostępne za pośrednictwem zdarzeń o podwyższonym ryzyku Identity Protection interfejsu API programu Microsoft Graph.

Aby uzyskać więcej informacji, zobacz:

- [Rozpoczynanie pracy z usługą Azure Active Directory, interfejsu API raportowania](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-getting-started-azure-portal)

- [Rozpoczynanie pracy z usługą Azure Active Directory Identity Protection i Microsoft Graph](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection-graph-getting-started)

---

## <a name="december-2017"></a>Grudnia 2017 r.

### <a name="terms-of-use-in-the-access-panel"></a>Warunki użytkowania w panelu dostępu

**Typ:** Nowa funkcja  
**Kategoria usług:** Warunki użytkowania  
**Możliwości produktu:** Zgodność
 
Możesz teraz przejść do panelu dostępu i wyświetlanie warunków użytkowania, które zostały wcześniej zaakceptowane.

Wykonaj następujące kroki:

1. Przejdź do [portalu MyApps](https://myapps.microsoft.com)i zaloguj się.

2. W prawym górnym rogu wybierz swoją nazwę, a następnie wybierz pozycję **profilu** z listy. 

3. Na Twojej **profilu**, wybierz opcję **Przejrzyj warunki użytkowania**. 

4. Teraz można przejrzeć warunki użytkowania akceptowane. 

Aby uzyskać więcej informacji, zobacz [warunki usługi Azure AD, użyj funkcji (wersja zapoznawcza)](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use).
 
---
 
### <a name="new-azure-ad-sign-in-experience"></a>Nowe środowisko logowania dla usługi Azure AD

**Typ:** Nowa funkcja  
**Kategoria usług:** Azure AD  
**Możliwości produktu:** Uwierzytelnianie użytkowników
 
Usługi Azure AD i systemu tożsamości konta Microsoft UI zostały zaprojektowane od nowa, aby udzielić użytkownikom spójny wygląd i zachowanie. Ponadto strony logowania usługi Azure AD umożliwia zbieranie informacji o nazwę użytkownika najpierw następuje poświadczeń na drugim ekranie.

Aby uzyskać więcej informacji, zobacz [nowe środowisko logowania dla usługi Azure AD jest teraz w publicznej wersji zapoznawczej](https://cloudblogs.microsoft.com/enterprisemobility/2017/08/02/the-new-azure-ad-signin-experience-is-now-in-public-preview/).
 
---
 
### <a name="fewer-sign-in-prompts-a-new-keep-me-signed-in-experience-for-azure-ad-sign-in"></a>Mniej monity o logowanie: Nowych "nie wylogowuj mnie" Środowisko logowania w usłudze Azure AD

**Typ:** Nowa funkcja  
**Kategoria usług:** Azure AD  
**Możliwości produktu:** Uwierzytelnianie użytkowników
 
**Nie wylogowuj mnie** pole wyboru na stronie logowania w usłudze Azure AD został zastąpiony nowym wierszu, który pojawia się po pomyślnym uwierzytelnieniu. 

Jeśli odpowiesz **tak** do tego wiersza, usługa zapewnia token odświeżania trwałe. To zachowanie jest takie same jak w przypadku wybrania **nie wylogowuj mnie** pole wyboru w stare środowisko. W przypadku dzierżaw federacyjnych tego monitu pokazuje po pomyślnie przeprowadzić uwierzytelnienie przy użyciu usługi federacyjnej.

Aby uzyskać więcej informacji, zobacz [mniejszą liczbę monitów logowania: Nowe środowisko "nie wylogowuj mnie" dla usługi Azure AD jest dostępne w wersji zapoznawczej](https://cloudblogs.microsoft.com/enterprisemobility/2017/09/19/fewer-login-prompts-the-new-keep-me-signed-in-experience-for-azure-ad-is-in-preview/). 

---

### <a name="add-configuration-to-require-the-terms-of-use-to-be-expanded-prior-to-accepting"></a>Dodaj konfigurację, aby wymagać warunków użytkowania przed akceptacją rozszerzona

**Typ:** Nowa funkcja  
**Kategoria usług:** Warunki użytkowania  
**Możliwości produktu:** Zgodność
 
Opcja dla administratorów wymaga użytkowników rozwinięcia warunków użytkowania przed akceptacją postanowień.

Wybierz opcję **na** lub **poza** aby wymagać od użytkowników rozwinięcia warunków użytkowania. **Na** ustawienie wymaga użytkownikom na wyświetlanie warunków użytkowania przed ich akceptacją.

Aby uzyskać więcej informacji, zobacz [warunki usługi Azure AD, użyj funkcji (wersja zapoznawcza)](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use).
 
---

### <a name="scoped-activation-for-eligible-role-assignments"></a>O określonym zakresie aktywacji dla kwalifikujących się przypisań ról

**Typ:** Nowa funkcja  
**Kategoria usług:** Privileged Identity Management  
**Możliwości produktu:** Privileged Identity Management
 
Przypisania ról uprawnionych zasobów platformy Azure z mniej autonomię w zakresie od oryginalnej wartości domyślne przypisania aktywować, można użyć aktywacji o określonym zakresie. Przykładem jest, jeśli masz przypisaną jako właściciela subskrypcji w ramach dzierżawy. O określonym zakresie aktywacji można aktywować roli właściciela dla maksymalnie 5 zasobów znajdujących się w ramach subskrypcji (np. grupy zasobów i maszyn wirtualnych). Wyznaczanie zakresu proces aktywacji może zmniejszyć prawdopodobieństwo wykonywanie niepożądanych zmian do krytycznych zasobów platformy Azure.

Aby uzyskać więcej informacji, zobacz [co to jest usługa Azure AD Privileged Identity Management?](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure).
 
---
 
### <a name="new-federated-apps-in-the-azure-ad-app-gallery"></a>Nowe aplikacje federacyjne w galerii aplikacji usługi Azure AD

**Typ:** Nowa funkcja  
**Kategoria usług:** Aplikacje dla przedsiębiorstw  
**Możliwości produktu:** Integracje innych firm

W grudniu 2017 r. Dodaliśmy obsługę tych aplikacji z Federacją do galerii aplikacji:

[Accredible](https://go.microsoft.com/fwlink/?linkid=863523), Adobe Experience Manager, [sklepu cyfrowego EFI](https://go.microsoft.com/fwlink/?linkid=861685), [Communifire](https://go.microsoft.com/fwlink/?linkid=861676) CybSafe, [FactSet](https://go.microsoft.com/fwlink/?linkid=863525), [OBRAZIE działa](https://go.microsoft.com/fwlink/?linkid=863517), [MOBI](https://go.microsoft.com/fwlink/?linkid=863521), [integracji usługi Azure AD MobileIron](https://go.microsoft.com/fwlink/?linkid=858027), [Reflektive](https://go.microsoft.com/fwlink/?linkid=863518), [logowania jednokrotnego SAML dla Bambus przez rozwiązania GmbH](https://go.microsoft.com/fwlink/?linkid=863520), [Logowania jednokrotnego SAML dla Bitbucket, rozpoznawanie GmbH](https://go.microsoft.com/fwlink/?linkid=863519), [Vodeclic](https://go.microsoft.com/fwlink/?linkid=863522), WebHR, integracji z usługą AD Zenegy platformy Azure.

Aby uzyskać więcej informacji o aplikacjach, zobacz [integracji aplikacji SaaS w usłudze Azure Active Directory](https://aka.ms/appstutorial).

Aby uzyskać więcej informacji o ofercie swoją aplikację w galerii aplikacji Azure AD, zobacz [umieść swoją aplikację w galerii aplikacji usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing). 
 
---

### <a name="approval-workflows-for-azure-ad-directory-roles"></a>Przepływy pracy zatwierdzania dla ról katalogu usługi Azure AD

**Typ:** Zmieniona funkcja  
**Kategoria usług:** Privileged Identity Management  
**Możliwości produktu:** Privileged Identity Management
 
Przepływ pracy zatwierdzania dla ról katalogu usługi Azure AD jest ogólnie dostępna.

Za pomocą przepływu pracy zatwierdzania Administratorzy ról uprzywilejowanych może wymagać członków roli kwalifikujących się do żądania aktywacji roli, zanim rozpoczną korzystanie z ról uprzywilejowanych. Wielu użytkowników i grup mogą być delegowane zatwierdzenia obowiązki. Członkowie roli kwalifikujących się otrzymywanie powiadomień o zakończeniu zatwierdzenia i ich rolą jest aktywny.

---
 
### <a name="pass-through-authentication-skype-for-business-support"></a>Uwierzytelnianie przekazywane: Skype dla firm pomocy technicznej

**Typ:** Zmieniona funkcja  
**Kategoria usług:** Uwierzytelnienia (logowania)  
**Możliwości produktu:** Uwierzytelnianie użytkowników

Teraz uwierzytelniania przekazywanego obsługuje logowania użytkowników do usługi Skype dla firm aplikacji klienckich, które obsługują nowoczesnego uwierzytelniania, w tym w trybie online i hybrydowe topologie. 

Aby uzyskać więcej informacji, zobacz [Skype dla firm topologie obsługiwane z nowoczesnym uwierzytelnianiem](https://technet.microsoft.com/library/mt803262.aspx).
 
---

### <a name="updates-to-azure-ad-privileged-identity-management-for-azure-rbac-preview"></a>Aktualizacje usługi Azure AD Privileged Identity Management dla RBAC platformy Azure (wersja zapoznawcza)

**Typ:** Zmieniona funkcja  
**Kategoria usług:** Privileged Identity Management  
**Możliwości produktu:** Privileged Identity Management
 
Za pomocą odświeżanie w publicznej wersji zapoznawczej usługi Azure AD Privileged Identity Management (PIM) based kontroli dostępu (RBAC) można teraz:

* Użyj wystarczający zakres administracji.
* Wymagaj zatwierdzenia do aktywowania role zasobów.
* Zaplanuj przyszłe aktywacji roli, która wymaga zatwierdzenia dla usługi Azure AD i ról RBAC platformy Azure.
 
Aby uzyskać więcej informacji, zobacz [Privileged Identity Management dla zasobów platformy Azure (wersja zapoznawcza)](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/azure-pim-resource-rbac).

---
 
## <a name="november-2017"></a>Listopada 2017 r.
 
### <a name="access-control-service-retirement"></a>Wycofanie usługi kontroli dostępu

**Typ:** Plan zmiany  
**Kategoria usług:** Usługa Access Control service  
**Możliwości produktu:** Usługa Access Control service 

Usługi Azure Active Directory Access Control (znany także jako usługa Access Control) zostanie wycofana w późnym 2018 r. Więcej informacji, który zawiera szczegółowy harmonogram i wskazówek dotyczących migracji wysokiego poziomu będą dostępne w ciągu kilku następnych tygodni. Na tej stronie można zamieszczać komentarze, pytania dotyczące usługi kontroli dostępu, a członek zespołu będzie odbierać je.

---

### <a name="restrict-browser-access-to-the-intune-managed-browser"></a>Ogranicz dostęp za pomocą przeglądarki do usługi Intune Managed Browser 

**Typ:** Plan zmiany  
**Kategoria usług:** Dostęp warunkowy  
**Możliwości produktu:** Tożsamość, zabezpieczenia i ochrona

Możesz ograniczyć dostęp przeglądarki do usługi Office 365 i innych aplikacji w chmurze połączone usługi AD systemu Azure przy użyciu aplikacji Intune Managed Browser jako zatwierdzonej aplikacji. 

Teraz można skonfigurować następujący warunek dla dostępu warunkowego opartego na aplikacji:

**Aplikacje klienckie:** Przeglądarka

**Jaki jest wpływ tej zmiany?**

Już dziś dostęp jest zablokowany, korzystając z tego warunku. Po udostępnieniu wersji zapoznawczej dostęp będzie wymagać korzystanie z aplikacji programu managed browser. 

Poszukaj tej funkcji i dodatkowe informacje w nadchodzących o naszych blogach i wersji. 

Aby uzyskać więcej informacji, zobacz [dostęp warunkowy w usłudze Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal).
 
---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Nowe zatwierdzone aplikacje klienckie na potrzeby opartego na aplikacjach dostępu warunkowego w usłudze Azure AD

**Typ:** Plan zmiany  
**Kategoria usług:** Dostęp warunkowy  
**Możliwości produktu:** Tożsamość, zabezpieczenia i ochrona

Następujące aplikacje są na liście [zatwierdzonych aplikacji klienckich](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#approved-client-app-requirement):

- [Microsoft Kaizala](https://www.microsoft.com/garage/profiles/kaizala/)
- Microsoft StaffHub

Aby uzyskać więcej informacji, zobacz:

- [Wymaganie aplikacji zatwierdzone klienta](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#approved-client-app-requirement)
- [Usługa Azure AD oparty na aplikacji dostępu warunkowego](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)

---

### <a name="terms-of-use-support-for-multiple-languages"></a>Warunki użytkowania obsługują wiele języków

**Typ:** Nowa funkcja    
**Kategoria usług:** Warunki użytkowania  
**Możliwości produktu:** Zgodność

Administratorzy mogą teraz utworzyć nowe warunki użytkowania, które zawierają wiele dokumentów PDF. Można oznaczyć tych dokumentów PDF z odpowiedniego języka. Użytkownicy otrzymują plik PDF z zgodny język, w oparciu o ich preferencje. W przypadku niezgodności, jest wyświetlany domyślny język.

---
 
### <a name="real-time-password-writeback-client-status"></a>Stan klienta zapisywania zwrotnego haseł w czasie rzeczywistym

**Typ:** Nowa funkcja  
**Kategoria usług:** Samoobsługowe resetowanie haseł  
**Możliwości produktu:** Uwierzytelnianie użytkowników

Teraz można sprawdzić stan swojego klienta zapisywania zwrotnego haseł w środowisku lokalnym. Ta opcja jest dostępna w **integracji lokalnego** części [resetowania hasła](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/PasswordReset) strony. 

Jeśli występują problemy z połączeniem do środowiska lokalnego klienta zapisywania zwrotnego, zobaczysz komunikat o błędzie, który zapewnia:

- Informacje na temat Dlaczego nie można nawiązać połączenia z klientem zapisywania zwrotnego w środowisku lokalnym.
- Link do dokumentacji, która pomaga w rozwiązywaniu problemu. 

Aby uzyskać więcej informacji, zobacz [integracji lokalnego](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-how-it-works#on-premises-integration).

---

### <a name="azure-ad-app-based-conditional-access"></a>Usługa Azure AD oparty na aplikacji dostępu warunkowego 
 
**Typ:** Nowa funkcja  
**Kategoria usług:** Azure AD  
**Możliwości produktu:** Tożsamość, zabezpieczenia i ochrona

Teraz można ograniczyć dostęp do usługi Office 365 i innych aplikacji połączonych AD chmury platformy Azure do [zatwierdzonych aplikacji klienckich](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#approved-client-app-requirement) obsługujące zasady ochrony aplikacji usługi Intune przy użyciu [dostępu warunkowego opartego na aplikacji usługi Azure AD](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access). Zasady ochrony aplikacji usługi Intune służą do konfigurowania i ochrony danych firmy na tych aplikacji klienckich.

Łącząc [opartego na aplikacji](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access) z [opartego na urządzeniach](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-policy-connected-applications) zasady dostępu warunkowego, masz możliwość ochrony danych osobistych i urządzeniach firmy.

Następujące warunki i kontrolki są teraz dostępne do użycia przy użyciu dostępu warunkowego opartego na aplikacji:

**Warunek z obsługiwanych platform**

- iOS
- Android

**Stan aplikacji klienta**

- Aplikacje mobilne i klienci stacjonarni

**Kontrola dostępu**

- Wymagaj zatwierdzonej aplikacji klienckiej

Aby uzyskać więcej informacji, zobacz [dostępu warunkowego opartego na aplikacji usługi Azure AD](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access).
 
---

### <a name="manage-azure-ad-devices-in-the-azure-portal"></a>Zarządzanie urządzeniami usługi Azure AD w witrynie Azure portal

**Typ:** Nowa funkcja  
**Kategoria usług:** Rejestracja urządzeń i zarządzanie  
**Możliwości produktu:** Tożsamość, zabezpieczenia i ochrona

Teraz można znaleźć wszystkie swoje urządzenia podłączone do usługi Azure AD i działań związanych z urządzenia w jednym miejscu. Istnieje nowe środowisko Administracja i Zarządzanie tożsamościami urządzeń i ustawienia w witrynie Azure portal. W tej wersji można wykonywać następujące czynności:

- Wyświetl wszystkie swoje urządzenia, które są dostępne dla dostępu warunkowego w usłudze Azure AD.
- Wyświetlanie właściwości, które zawierają hybrydowe Azure urządzeń dołączonych do usługi AD.
- Znajdź klucze funkcji BitLocker dla urządzeń przyłączonych do usługi AD systemu Azure, zarządzanie urządzeniem za pomocą usługi Intune i nie tylko.
- Zarządzaj ustawienia dotyczące urządzeń usługi Azure AD.

Aby uzyskać więcej informacji, zobacz [zarządzania urządzeniami za pomocą witryny Azure portal](https://docs.microsoft.com/azure/active-directory/device-management-azure-portal).

---

### <a name="support-for-macos-as-a-device-platform-for-azure-ad-conditional-access"></a>Obsługa dla systemu macOS jako platformę urządzenia dla dostępu warunkowego usługi Azure AD 

**Typ:** Nowa funkcja    
**Kategoria usług:** Dostęp warunkowy  
**Możliwości produktu:** Tożsamość, zabezpieczenia i ochrona 

Można teraz włączyć (lub wyłączyć) z systemem macOS jako warunek platformy urządzenia w zasadach dostępu warunkowego usługi Azure AD. Z dodatkiem z systemem macOS do obsługiwanych platform urządzeń możesz wykonywać następujące czynności:

- **Rejestrowanie i zarządzanie urządzeniami z systemem macOS przy użyciu usługi Intune.** Podobnie jak w innych platform, takich jak systemy iOS i Android, aplikację portalu firmy jest dostępna dla systemu macOS ujednoliconego rejestracji. Nowa aplikacja portal firmy dla systemu macOS można użyć do rejestrowania urządzenia w usłudze Intune i zarejestrowanie go za pomocą usługi Azure AD.
- **Upewnij się, że urządzenia z systemem macOS stosować się do zasad zgodności w organizacji, określone w usłudze Intune.** W usłudze Intune w witrynie Azure portal możesz teraz skonfigurować zasady zgodności dla urządzeń z systemem macOS. 
- **Ogranicz dostęp do aplikacji w usłudze Azure AD tylko urządzeń zgodnych z systemem macOS.** Tworzenie zasad dostępu warunkowego ma z systemem macOS jako opcji platformy oddzielnym urządzeniu. Teraz można tworzyć zasady dostępu warunkowego specyficzne dla systemu macOS dla aplikacji docelowej, ustaw na platformie Azure.

Aby uzyskać więcej informacji, zobacz:

- [Tworzenie zasad zgodności dla urządzeń z systemem macOS w usłudze Intune](https://aka.ms/macoscompliancepolicy)
- [Dostęp warunkowy w usłudze Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal)
 
---

### <a name="network-policy-server-extension-for-azure-multi-factor-authentication"></a>Rozszerzenie serwera zasady sieciowe dla usługi Azure Multi-Factor Authentication 

**Typ:** Nowa funkcja    
**Kategoria usług:**  Uwierzytelnianie wieloskładnikowe  
**Możliwości produktu:** Uwierzytelnianie użytkowników

Rozszerzenie serwera zasad sieciowych dla usługi Azure Multi-Factor Authentication dodaje funkcje oparte na chmurze Multi-Factor Authentication do infrastruktury uwierzytelniania przy użyciu istniejących serwerów. Z rozszerzeniem serwera zasad sieciowych można dodać połączenie telefoniczne, wiadomość SMS lub weryfikacji aplikacji telefonicznej do istniejącego przepływu uwierzytelniania. Nie trzeba instalować, konfigurować i obsługiwać nowe serwery. 

To rozszerzenie został utworzony dla organizacji, które chcesz chronić połączenia sieci VPN bez wdrażania serwera usługi Azure Multi-Factor Authentication. Serwer zasad sieciowych, których rozszerzenie działa jako karty między RADIUS i oparte na chmurze usługi Azure Multi-Factor Authentication, aby zapewnić drugi składnik uwierzytelniania federacyjnego lub zsynchronizowanych użytkowników.

Aby uzyskać więcej informacji, zobacz [integrowanie istniejącej infrastruktury serwera zasad sieciowych przy użyciu usługi Azure Multi-Factor Authentication](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-nps-extension).
 
---

### <a name="restore-or-permanently-remove-deleted-users"></a>Przywrócić lub usunąć trwale usunięto użytkowników

**Typ:** Nowa funkcja    
**Kategoria usług:** Zarządzanie użytkownikami  
**Możliwości produktu:** Katalog 

W Centrum administracyjnym usługi Azure AD możesz teraz:

- Przywracanie usuniętego użytkownika. 
- Trwałe usuwanie użytkownika.

**Aby wypróbować tę funkcję:**

1. W Centrum administracyjnym usługi Azure AD, wybierz [wszyscy użytkownicy](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/All) w **Zarządzaj** sekcji. 

2. Z **Pokaż** listy wybierz **niedawno usuniętych użytkowników**. 

3. Wybierz co najmniej jeden niedawno usuniętych użytkowników i następnie przywrócić je lub trwale usunąć je.
 
---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Nowe zatwierdzone aplikacje klienckie na potrzeby opartego na aplikacjach dostępu warunkowego w usłudze Azure AD
 
**Typ:** Zmieniona funkcja  
**Kategoria usług:** Dostęp warunkowy  
**Możliwości produktu:** Tożsamość, zabezpieczenia i ochrona

Następujące aplikacje zostały dodane do listy [zatwierdzonych aplikacji klienckich](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#approved-client-app-requirement):

- Microsoft Planner
- Azure Information Protection 

Aby uzyskać więcej informacji, zobacz:

- [Wymaganie aplikacji zatwierdzone klienta](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#approved-client-app-requirement)
- [Usługa Azure AD oparty na aplikacji dostępu warunkowego](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)

---

### <a name="use-or-between-controls-in-a-conditional-access-policy"></a>Użyj "Lub" między kontrolkami w zasadach dostępu warunkowego 

**Typ:** Zmieniona funkcja    
**Kategoria usług:** Dostęp warunkowy  
**Możliwości produktu:** Tożsamość, zabezpieczenia i ochrona
 
Możesz teraz użyć "lub" (wymaga jednej z wybranych kontrolek) dla kontroli dostępu warunkowego. Ta funkcja służy do tworzenia zasad za pomocą "OR" między kontroli dostępu. Na przykład można użyć tej funkcji, aby utworzyć zasadę, która wymaga od użytkownika, zaloguj się przy użyciu usługi Multi-Factor Authentication "OR" włączenia zgodnego urządzenia.

Aby uzyskać więcej informacji, zobacz [kontroli dostępu warunkowego usługi Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-controls).
 
---

### <a name="aggregation-of-real-time-risk-events"></a>Agregacja zdarzeń o podwyższonym ryzyku w czasie rzeczywistym

**Typ:** Zmieniona funkcja    
**Kategoria usług:** Ochrona tożsamości  
**Możliwości produktu:** Tożsamość, zabezpieczenia i ochrona

W usłudze Azure AD Identity Protection wszystkie zdarzenia ryzyka w czasie rzeczywistym, które pochodzą z tego samego adresu IP w danym dniu teraz są agregowane dla każdego typu zdarzenia o podwyższonym ryzyku. Ta zmiana ogranicza ilość zdarzenia o podwyższonym ryzyku są wyświetlane bez zmiany zabezpieczeń użytkownika.

Podstawowe wykrywanie w czasie rzeczywistym działa w każdym razem, gdy użytkownik loguje się. Jeśli masz zasady zabezpieczeń ryzyka logowania równa uwierzytelniania wieloskładnikowego lub blokowanie dostępu, nadal jest wyzwalany podczas każdego ryzykowne logowania.
 
---
 
## <a name="october-2017"></a>Października 2017 r.

### <a name="deprecate-azure-ad-reports"></a>Wycofana raporty usługi Azure AD

**Typ:** Plan zmiany  
**Kategoria usług:** Raportowanie  
**Możliwości produktu:** Zarządzanie cyklem życia tożsamości  

Azure portal udostępnia Ci:

- Nową konsolę administracyjną usługi Azure AD.
- Nowe interfejsy API dla raportów aktywności i zabezpieczeń.
 
Ze względu na te nowe możliwości raportu interfejsów API w ramach punktu końcowego/Reports zostały wycofane 10 grudnia 2017 r. 

---

### <a name="automatic-sign-in-field-detection"></a>Wykrywanie automatyczne pól logowania

**Typ:** Ustalone   
**Kategoria usług:** Moje aplikacje  
**Możliwości produktu:** Logowanie jednokrotne  

Usługa Azure AD obsługuje wykrywania automatycznego pól logowania dla aplikacji, które są renderowane pola nazwy i hasła użytkownika HTML. Te kroki są udokumentowane w artykule [jak automatycznie Przechwyć pola logowania dla aplikacji](https://docs.microsoft.com/azure/active-directory/application-config-sso-problem-configure-password-sso-non-gallery#how-to-manually-capture-sign-in-fields-for-an-application). Tej funkcji można znaleźć, dodając *spoza galerii* aplikacji **aplikacje dla przedsiębiorstw** strony w [witryny Azure portal](https://aad.portal.azure.com). Ponadto można skonfigurować **logowania jednokrotnego** tryb, w tym nową aplikację do **opartego na hasłach logowania jednokrotnego**, wprowadź adres URL sieci web, a następnie Zapisz strony.
 
Ze względu na problem z usługą zostało tymczasowo wyłączyć tej funkcji. Problem został rozwiązany i wykrywania automatycznego pól logowania będą ponownie dostępne.

---

### <a name="new-multi-factor-authentication-features"></a>Nowe funkcje usługi Multi-Factor Authentication

**Typ:** Nowa funkcja  
**Kategoria usług:** Uwierzytelnianie wieloskładnikowe  
**Możliwości produktu:** Tożsamość, zabezpieczenia i ochrona  

Uwierzytelnianie wieloskładnikowe (MFA) jest integralną część ochrony Twojej organizacji. Aby bardziej adaptacyjnego poświadczenia i doświadczenia Bezproblemowa, zostały dodane następujące funkcje: 

- Wyniki żądania usługi Multi-Factor Authentication są bezpośrednio zintegrowane usługi Azure AD logowania w raporcie, który obejmuje programowy dostęp do usługi MFA wyników.
- Konfiguracja uwierzytelniania Wieloskładnikowego głębiej jest zintegrowana w konfiguracji usługi Azure AD środowisko w witrynie Azure portal.

W tej publicznej wersji zapoznawczej zarządzania usługi MFA i raportowania są zintegrowaną częścią środowisko konfiguracji podstawowej usługi Azure AD. Możesz teraz zarządzać funkcji portalu zarządzania usługi MFA w środowisku usługi Azure AD.

Aby uzyskać więcej informacji, zobacz [dokumentacja dotycząca usługi MFA raportowania w witrynie Azure portal](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-sign-ins-mfa). 

---

### <a name="terms-of-use"></a>Warunki użytkowania

**Typ:** Nowa funkcja  
**Kategoria usług:** Warunki użytkowania  
**Możliwości produktu:** Zgodność  

Możesz użyć usługi Azure AD warunki użytkowania przedstawiają informacje, takie jak zastrzeżenia istotne dla prawnych lub wymagań dotyczących zgodności dla użytkowników.

Warunki użytkowania usługi Azure AD można użyć w następujących scenariuszach:

- Ogólne warunki użytkowania dla wszystkich użytkowników w Twojej organizacji
- Specyficzne warunki użytkowania na podstawie atrybutów użytkownika (na przykład lekarzy a pielęgniarek) lub domowych, a pracownicy krajowi przez grupy dynamiczne
- Specyficzne warunki użytkowania do uzyskiwania dostępu do aplikacji biznesowych o dużym znaczeniu, takich jak Salesforce

Aby uzyskać więcej informacji, zobacz [użytkowania usługi Azure AD](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use).

---

### <a name="enhancements-to-privileged-identity-management"></a>Ulepszenia Privileged Identity Management

**Typ:** Nowa funkcja  
**Kategoria usług:** Privileged Identity Management  
**Możliwości produktu:** Privileged Identity Management  

Za pomocą usługi Azure AD Privileged Identity Management możesz zarządzanie, sterowanie i monitorowanie dostępu do zasobów platformy Azure (wersja zapoznawcza) w Twojej organizacji:

- Subskrypcje
- Grupy zasobów
- Maszyny wirtualne 

Wszystkie zasoby w witrynie Azure portal, korzystających z funkcji Azure RBAC korzystać z zalet zabezpieczeń i funkcji zarządzania cyklem życia, które usługi Azure AD Privileged Identity Management ma do zaoferowania.

Aby uzyskać więcej informacji, zobacz [Privileged Identity Management dla zasobów platformy Azure](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/azure-pim-resource-rbac).

---

### <a name="access-reviews"></a>Przeglądy dostępu

**Typ:** Nowa funkcja  
**Kategoria usług:** Przeglądy dostępu  
**Możliwości produktu:** Zgodność  

Organizacje mogą używać przeglądów dostępu (wersja zapoznawcza) w celu efektywnego zarządzania członkostwa w grupach i dostępem do aplikacji dla przedsiębiorstw: 

- Przeprowadzając przeglądy dostępu, można ponownie certyfikować dostęp użytkowników-gości do aplikacji i ich członkostwo w grupach. Recenzenci mogą efektywnie decydować, czy gościom przedłużony dostępu na podstawie uzyskanych informacji dostarczonych przez przeglądów dostępu.
- Przeprowadzając przeglądy dostępu, można ponownie certyfikować dostęp do aplikacji i członkostwo w grupach pracowników.

Kontrole z przeglądem dostępu można łączyć w programy odpowiednie dla danej organizacji, aby śledzić przeglądy dla aplikacji dotyczących zgodności lub aplikacji narażonych na ryzyko.

Aby uzyskać więcej informacji, zobacz [przeglądy dostępu w usłudze Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-azure-ad-controls-access-reviews-overview).

---

### <a name="hide-third-party-applications-from-my-apps-and-the-office-365-app-launcher"></a>Ukrywanie aplikacji innych firm, które z moich aplikacji i uruchamianie aplikacji usługi Office 365

**Typ:** Nowa funkcja  
**Kategoria usług:** Moje aplikacje  
**Możliwości produktu:** Logowanie jednokrotne  

Możesz teraz lepiej zarządzać aplikacje, które pojawiają się w portalach użytkowników za pośrednictwem nowego **Ukryj aplikacji** właściwości. Można ukryć aplikacji, aby ułatwić w przypadkach, w którym kafelków aplikacji widoczne dla usług zaplecza lub zduplikowanych fragmentów i przyciski użytkowników zaśmiecania aplikacji Uruchom. Przełącznik jest w **właściwości** części aplikacji innych firm i jest oznaczona etykietą **widoczne dla użytkownika?** Można również ukryć aplikację programistycznie za pomocą programu PowerShell. 

Aby uzyskać więcej informacji, zobacz [ukrywanie aplikacji innych firm, ze środowiska użytkownika w usłudze Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-hide-third-party-app). 


**Co to jest dostępne?**

 W ramach przejścia na Nowa Konsola administracyjna dwa nowe interfejsy API do pobierania działania usługi Azure AD dostępnych dzienników. Nowy zestaw interfejsów API zapewnia bardziej zaawansowane filtrowanie i sortowanie funkcje oprócz bardziej rozbudowane kontroli i działań logowania. API zdarzeń o podwyższonym ryzyku ochrony tożsamości w programie Microsoft Graph można uzyskać dostępu do danych, które były dostępne w raportach zabezpieczeń.


## <a name="september-2017"></a>Września 2017 r.

### <a name="hotfix-for-identity-manager"></a>Poprawka programu Identity Manager

**Typ:** Zmieniona funkcja  
**Kategoria usług:** Programu Identity Manager  
**Możliwości produktu:** Zarządzanie cyklem życia tożsamości  

Pakiet zbiorczy poprawek (kompilacja 4.4.1642.0) jest dostępny od 25 września 2017 r. dla dodatku Service Pack 1 dla programu Identity Manager 2016. Ten pakiet zbiorczy:

- Rozwiązuje problemy, a następnie dodaje ulepszenia.
- Jest zbiorcza aktualizacja, która zastępuje wszystkie aktualizacje programu Identity Manager 2016 Service Pack 1 do kompilacji 4.4.1459.0 programu Identity Manager 2016. 
- Wymagane jest posiadanie programu Identity Manager 2016 4.4.1302.0 kompilacji. 

Aby uzyskać więcej informacji, zobacz [pakiet zbiorczy poprawek (kompilacja 4.4.1642.0) jest dostępna dla dodatku Service Pack 1 dla programu Identity Manager 2016](https://support.microsoft.com/help/4021562). 

---
