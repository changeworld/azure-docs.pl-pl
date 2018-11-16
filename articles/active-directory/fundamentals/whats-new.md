---
title: Co nowego? Informacje o wersji dla usługi Azure AD | Dokumentacja firmy Microsoft
description: Poznaj nowości z usługą Azure Active Directory (Azure AD), takich jak najnowsze informacje o wersji — znane problemy, poprawki, przestarzałe funkcje i nadchodzących zmianach.
services: active-directory
author: eross-msft
manager: mtillman
featureFlags:
- clicktale
ms.assetid: 06a149f7-4aa1-4fb9-a8ec-ac2633b031fb
ms.service: active-directory
ms.component: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: lizross
ms.reviewer: dhanyahk
ms.openlocfilehash: 1377280cf3b7272d710b47322b9b27ff8cb36327
ms.sourcegitcommit: 275eb46107b16bfb9cf34c36cd1cfb000331fbff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/15/2018
ms.locfileid: "51707167"
---
# <a name="whats-new-in-azure-active-directory"></a>What's new in Azure Active Directory?

>Otrzymaj powiadomienie o tym, kiedy Odwiedzaj tę stronę w celu aktualizacji, dodając następujący kod [adresu URL](https://docs.microsoft.com/api/search/rss?search=%22whats%20new%20in%20azure%20active%20directory%22&locale=en-us/) do Twojej ![ikonę RSS Widoczną](./media/whats-new/feed-icon-16x16.png) czytnik źródła.

Usługa Azure AD odbiera ulepszenia w sposób ciągły. Aby uzyskać najnowsze informacje o najnowszych zmianach, w tym artykule przedstawiono informacje o:

- Zainstalowane najnowsze wersje
- Znane problemy
- Poprawki błędów
- Przestarzałe funkcje
- Plany dotyczące zmian

Ta strona jest aktualizowany co miesiąc, więc ponownie regularnie. Jeśli szukasz dla elementów, które są starsze niż 6 miesięcy, możesz je znaleźć w [archiwizowania na potrzeby What's new in Azure Active Directory](whats-new-archive.md).

---
## <a name="october-2018"></a>Październik 2018 r.

### <a name="azure-ad-logs-now-work-with-azure-log-analytics-public-preview"></a>Dzienniki usługi Azure AD obecnie pracować z usługą Azure Log Analytics (publiczna wersja zapoznawcza)

**Typ:** nowa funkcja  
**Kategoria usług:** raportowania  
**Możliwości produktu:** monitorowanie i raportowanie

Cieszymy się, mogąc mogą obecnie przekazywać dzienników usługi Azure AD do usługi Azure Log Analytics! Najczęściej żądanych pomaga umożliwiają jeszcze lepsze dostęp do analizy dla Twojej firmy, operacji i zabezpieczeń, a także sposób, aby wspomóc monitorowanie infrastruktury. Aby uzyskać więcej informacji, zobacz [działanie usługi Azure Active Directory rejestruje się w usłudze Azure Log Analytics jest teraz dostępna](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-Active-Directory-Activity-logs-in-Azure-Log-Analytics-now/ba-p/274843) blogu.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---october-2018"></a>Nowe aplikacje federacyjne dostępne w galerii aplikacji usługi Azure AD — października 2018 r.

**Typ:** nowa funkcja  
**Kategoria usług:** aplikacje dla przedsiębiorstw  
**Możliwości produktu:** 3rd integracji innych firm
 
Na października 2018 Dodaliśmy obsługę 14 nowych aplikacjach z Federacją w galerii aplikacji:

[Moje punkty Award](https://docs.microsoft.com/azure/active-directory/saas-apps/myawardpoints-tutorial), [Vibe HCM](https://docs.microsoft.com/azure/active-directory/saas-apps/vibehcm-tutorial), ambyint, [MyWorkDrive](https://docs.microsoft.com/azure/active-directory/saas-apps/myworkdrive-tutorial), [BorrowBox](https://docs.microsoft.com/azure/active-directory/saas-apps/borrowbox-tutorial), Dialpad, [środowiska wirtualnego ON24](https://docs.microsoft.com/azure/active-directory/saas-apps/on24-tutorial), [RingCentral](https://docs.microsoft.com/azure/active-directory/saas-apps/ringcentral-tutorial), [rozwiązania Zscaler trzech](https://docs.microsoft.com/azure/active-directory/saas-apps/zscaler-three-tutorial), [Phraseanet](https://docs.microsoft.com/azure/active-directory/saas-apps/phraseanet-tutorial), [Appraisd](https://docs.microsoft.com/azure/active-directory/saas-apps/appraisd-tutorial), [kontroli Workspot](https://docs.microsoft.com/azure/active-directory/saas-apps/workspotcontrol-tutorial), [Shuccho Navi](https://docs.microsoft.com/azure/active-directory/saas-apps/shucchonavi-tutorial), [Glassfrog](https://docs.microsoft.com/azure/active-directory/saas-apps/glassfrog-tutorial)

Aby uzyskać więcej informacji o aplikacjach, zobacz [integracji aplikacji SaaS w usłudze Azure Active Directory](https://aka.ms/appstutorial). Aby uzyskać więcej informacji o ofercie swoją aplikację w galerii aplikacji Azure AD, zobacz [umieść swoją aplikację w galerii aplikacji usługi Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="azure-ad-domain-services-email-notifications"></a>Powiadomienia E-mail usług domenowych Azure AD

**Typ:** nowa funkcja  
**Kategoria usług:** usług domenowych Azure AD  
**Możliwości produktu:** usług domenowych Azure AD

Azure AD Domain Services udostępnia alerty w portalu Azure dotyczące błędów konfiguracji lub problemów z Twoją domeną zarządzaną. Te alerty zawierają przewodniki krok po kroku, dzięki czemu można spróbować rozwiązać problemy bez konieczności kontaktowania się z pomocą techniczną.

Począwszy od października, będzie można dostosować ustawienia powiadomień dla domeny zarządzanej, gdy nowe alerty występują, do wyznaczonej grupy osób, eliminując konieczność stale Sprawdź portal aktualizacji zostanie wysłana wiadomość e-mail.

Aby uzyskać więcej informacji, zobacz [ustawienia powiadomień w usługach domenowych Azure AD](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-notifications).

---

### <a name="azure-ad-portal-supports-using-the-forcedelete-domain-api-to-delete-custom-domains"></a>Platforma Azure obsługuje portalu AD przy użyciu domeny ForceDelete interfejsu API, można usunąć domeny niestandardowej 

**Typ:** zmieniono funkcji  
**Kategoria usług:** Directory Management  
**Możliwości produktu:** katalogu

Mamy przyjemność ogłosić, że teraz można użyć domeny ForceDelete interfejsu API można usunąć nazwy domeny niestandardowej, asynchronicznie zmieniając nazwę odwołania, takich jak użytkownicy, grupy i aplikacje z niestandardowej nazwy domeny (contoso.com) do (nazwa domeny początkowej domyślnej contoso.onmicrosoft.com).

Ta zmiana pomaga szybciej usunąć nazwy domeny niestandardowej, czy Twoja organizacja już używa nazwy, czy należy użyć nazwy domeny za pomocą innej usługi Azure AD.

Aby uzyskać więcej informacji, zobacz [usuwanie nazwy domeny niestandardowej](https://docs.microsoft.com/azure/active-directory/users-groups-roles/domains-manage#delete-a-custom-domain-name).

---

## <a name="september-2018"></a>Wrzesień 2018 r.
 
### <a name="updated-administrator-role-permissions-for-dynamic-groups"></a>Zaktualizowane uprawnienia roli administratora dla grup dynamicznych

**Typ:** stałej  
**Kategoria usług:** Zarządzanie grupami  
**Możliwości produktu:** współpracy

Rozwiązaliśmy problem, więc teraz tworzyć i aktualizować reguły członkostwa dynamicznego, bez potrzeby bycia właściciela grupy ról określonego administratora.

Dostępne są następujące role:

- Administrator globalny lub zapisywania firmy

- Administrator usługi Intune

- Administrator kont użytkowników

Aby uzyskać więcej informacji, zobacz [utworzyć grupę dynamiczną i sprawdzić stan](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-create-rule)

---

### <a name="simplified-single-sign-on-sso-configuration-settings-for-some-third-party-apps"></a>Uproszczone ustawienia konfiguracji logowania jednokrotnego dla niektórych aplikacji innej firmy

**Typ:** nowa funkcja  
**Kategoria usług:** aplikacje dla przedsiębiorstw  
**Możliwości produktu:** logowania jednokrotnego

Zdajemy sobie sprawę, że ustawienia zapasowej pojedynczego logowania jednokrotnego (SSO) dla oprogramowania jako usługi (SaaS) aplikacji może być trudne, ze względu na unikatowy charakter każdej konfiguracji aplikacji. Mamy skompilowane środowisko uproszczona konfiguracja, aby automatycznie wypełnić ustawień konfiguracji logowania jednokrotnego dla następujących aplikacji SaaS innych firm:

- Zendesk

- Komponent ArcGis Online

- Narzędzie Jamf Pro

Aby rozpocząć korzystanie z tego środowiska jednym kliknięciem, przejdź do **witryny Azure portal** > **konfiguracji logowania jednokrotnego** strony aplikacji. Aby uzyskać więcej informacji, zobacz [integracji aplikacji SaaS w usłudze Azure Active Directory](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list)

---

### <a name="azure-active-directory---where-is-your-data-located-page"></a>Usługa Azure Active Directory — Twoje dane lokalizacji? Strona

**Typ:** nowa funkcja  
**Kategoria usług:** innych  
**Możliwości produktu:** GoLocal

Wybierz region, firmy z **usługi Azure Active Directory — Twoje dane lokalizacji** strony do widoku, w którym Centrum danych Azure przechowuje dane usługi Azure AD w spoczynku dla wszystkich usług Azure AD. Informacje można filtrować według określonej usługi Azure AD dla regionu w firmie.

Aby skorzystać z tej funkcji i uzyskać więcej informacji, zobacz [usługi Azure Active Directory — Twoje dane lokalizacji](https://aka.ms/AADDataMap).

---

### <a name="new-deployment-plan-available-for-the-my-apps-access-panel"></a>Nowy plan wdrożenia dostępny dla panelu Dostęp do moich aplikacji

**Typ:** nowa funkcja  
**Kategoria usług:** Moje aplikacje  
**Możliwości produktu:** logowania jednokrotnego

Sprawdź nowy plan wdrożenia, dostępnej w panelu Moje aplikacjom dostępu (http://aka.ms/deploymentplans).
Panel Moje dostępu aplikacji zapewnia użytkownikom z jednego miejsca można znaleźć i uzyskać dostęp do swoich aplikacji. Ten portal zapewnia również użytkownikom możliwości samoobsługi, takie jak żądania dostępu do aplikacji i grup oraz zarządzanie dostępem do tych zasobów w imieniu innych użytkowników.

Aby uzyskać więcej informacji, zobacz [co to jest portal Moje aplikacje?](https://docs.microsoft.com/azure/active-directory/user-help/active-directory-saas-access-panel-introduction)

---

### <a name="new-troubleshooting-and-support-tab-on-the-sign-ins-logs-page-of-the-azure-portal"></a>Nowa karta Rozwiązywanie problemów i pomoc techniczna na stronie dzienników logowań witryny Azure Portal

**Typ:** nowa funkcja  
**Kategoria usług:** raportowania  
**Możliwości produktu:** monitorowanie i raportowanie

Nowy **Rozwiązywanie problemów i pomoc techniczna** karcie **logowania** strony Azure portal, mają na celu ułatwienie administratorom i pracowników działu pomocy technicznej, rozwiązywania problemów związanych z logowania usługi Azure AD. Ta nowa karta zawiera kod błędu, komunikat o błędzie i zaleceniami związanymi z korygowaniem (jeśli istnieje) Aby rozwiązać ten problem. Jeśli nie uda się rozwiązać ten problem, również udostępniamy nowy sposób tworzenia biletu pomocy technicznej przy użyciu **Kopiuj do Schowka** środowisko, które wypełnia **identyfikator żądania** i **Data (UTC)** pól pliku dziennika w biletu pomocy technicznej.  

![Dzienniki logowania z nowej karty](media/whats-new/troubleshooting-and-support.png)

---

### <a name="enhanced-support-for-custom-extension-properties-used-to-create-dynamic-membership-rules"></a>Ulepszona obsługa niestandardowych właściwości rozszerzenia używanych do tworzenia reguł członkostwa dynamicznego

**Typ:** zmieniono funkcji  
**Kategoria usług:** Zarządzanie grupami  
**Możliwości produktu:** współpracy

Dzięki tej aktualizacji można teraz kliknąć **pobieranie właściwości niestandardowego rozszerzenia** link z konstruktora reguły grupy dynamiczne użytkownika, wprowadź swój identyfikator aplikacji unikatowymi i otrzymywać pełną listę właściwości niestandardowego rozszerzenia do użycia podczas tworzenia dynamiczny reguły członkostwa dla użytkowników. Tej listy można odświeżać w taki sposób, aby uzyskać nowe właściwości rozszerzenia niestandardowego dla danej aplikacji.

Aby uzyskać więcej informacji o używaniu właściwości rozszerzenia niestandardowe reguły członkostwa dynamicznego, zobacz [rozszerzenia i właściwości niestandardowego rozszerzenia](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership#extension-properties-and-custom-extension-properties)

---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Nowe zatwierdzone aplikacje klienckie na potrzeby opartego na aplikacjach dostępu warunkowego w usłudze Azure AD

**Typ:** Planowanie zmian  
**Kategoria usług:** dostępu warunkowego  
**Możliwości produktu:** tożsamość, zabezpieczenia i ochrona

Następujące aplikacje są na liście [zatwierdzonych aplikacji klienckich](https://docs.microsoft.com/azure/active-directory/conditional-access/technical-reference#approved-client-app-requirement):

- Microsoft To-Do

- Microsoft Stream

Aby uzyskać więcej informacji, zobacz:

- [Usługa Azure AD oparty na aplikacji dostępu warunkowego](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)

---

### <a name="new-support-for-self-service-password-reset-from-the-windows-7881-lock-screen"></a>Nowa funkcja obsługi samoobsługowego resetowania hasła z ekranu blokady systemu Windows 7/8/8.1

**Typ:** nowa funkcja  
**Kategoria usług:** samoobsługowego resetowania HASEŁ  
**Możliwości produktu:** uwierzytelnianie użytkownika

Po skonfigurowaniu tej nowej funkcji użytkownikom zostanie wyświetlony link do zresetowania swojego hasła z **blokady** ekranu urządzenia z systemem Windows 7, Windows 8 lub Windows 8.1. Klikając ten link, użytkownik jest przeprowadzany przez sam przepływ resetowania hasła jako za pośrednictwem przeglądarki sieci web.

Aby uzyskać więcej informacji, zobacz [jak włączyć funkcję resetowania z Windows 7, 8 i 8.1 hasła](https://aka.ms/ssprforwindows78)

---

### <a name="change-notice-authorization-codes-will-no-longer-be-available-for-reuse"></a>Powiadomienie o zmianie: kody autoryzacji nie będą już dostępne do ponownego użycia 

**Typ:** Planowanie zmian  
**Kategoria usług:** uwierzytelnienia (logowania)  
**Możliwości produktu:** uwierzytelnianie użytkownika

15 listopada 2018 r. usługi Azure AD będzie akceptować kody poprzednio używanych uwierzytelniania dla aplikacji. Ta zmiana zabezpieczeń pomaga do usługi Azure AD zgodnie ze specyfikacją protokołu OAuth i będzie wymuszany w punktach końcowych v1 i v2.

Jeśli aplikacja używa kody autoryzacji uzyskiwanie tokenów dla wielu zasobów, zalecamy Użyj kodu, aby uzyskać token odświeżania, a następnie używać tego tokenu odświeżania w celu pobrania dodatkowe tokeny dla innych zasobów. Kody autoryzacji należy używać tylko raz, ale tokenów odświeżania mogą być wielokrotnie używane w wielu zasobach. Aplikacja, która podejmuje próbę ponownego użycia kodu uwierzytelniania podczas przepływu kodu OAuth otrzymają komunikat o błędzie invalid_grant.

Ta i inne zmiany dotyczące protokołów, zobacz [pełną listę Nowości dla uwierzytelniania](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---september-2018"></a>Nowe aplikacje federacyjne dostępne w galerii aplikacji usługi Azure AD — wrzesień 2018

**Typ:** nowa funkcja  
**Kategoria usług:** aplikacje dla przedsiębiorstw  
**Możliwości produktu:** 3rd integracji innych firm
 
W września 2018 Dodaliśmy obsługę 16 nowych aplikacjach z Federacją w galerii aplikacji:

[Uberflip](https://docs.microsoft.com/azure/active-directory/saas-apps/uberflip-tutorial), [Comeet rekrutacji oprogramowania](https://docs.microsoft.com/azure/active-directory/saas-apps/comeetrecruitingsoftware-tutorial), [Workteam](https://docs.microsoft.com/azure/active-directory/saas-apps/workteam-tutorial), [ArcGIS Enterprise](https://docs.microsoft.com/azure/active-directory/saas-apps/arcgisenterprise-tutorial), [Nuclino](https://docs.microsoft.com/azure/active-directory/saas-apps/nuclino-tutorial), [ Chmura JDA](https://docs.microsoft.com/azure/active-directory/saas-apps/jdacloud-tutorial), [Snowflake](https://docs.microsoft.com/azure/active-directory/saas-apps/snowflake-tutorial), NavigoCloud, [Figma](https://docs.microsoft.com/azure/active-directory/saas-apps/figma-tutorial), join.me, [ZephyrSSO](https://docs.microsoft.com/azure/active-directory/saas-apps/zephyrsso-tutorial), [Silverback](https://docs.microsoft.com/azure/active-directory/saas-apps/silverback-tutorial), Riverbed Xirrus EasyPass, [logowania jednokrotnego Rackspace](https://docs.microsoft.com/azure/active-directory/saas-apps/rackspacesso-tutorial), Enlyft Usługa rejestracji Jednokrotnej dla platformy Azure, usługę SurveyMonkey na [Convene](https://docs.microsoft.com/azure/active-directory/saas-apps/convene-tutorial), [dmarcian](https://docs.microsoft.com/azure/active-directory/saas-apps/dmarcian-tutorial)

Aby uzyskać więcej informacji o aplikacjach, zobacz [integracji aplikacji SaaS w usłudze Azure Active Directory](https://aka.ms/appstutorial). Aby uzyskać więcej informacji o ofercie swoją aplikację w galerii aplikacji Azure AD, zobacz [umieść swoją aplikację w galerii aplikacji usługi Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="support-for-additional-claims-transformations-methods"></a>Obsługa dodatkowych metod przekształceń oświadczeń

**Typ:** nowa funkcja  
**Kategoria usług:** aplikacje dla przedsiębiorstw  
**Możliwości produktu:** logowania jednokrotnego

Dodaliśmy nowe metody przekształcania oświadczeń ToLower() i ToUpper(), które mogą być stosowane na tokeny SAML z opartej na SAML **konfiguracji rejestracji jednokrotnej** strony.

Aby uzyskać więcej informacji, zobacz [Dostosowywanie oświadczeń wystawionych w tokenie języka SAML dla aplikacji dla przedsiębiorstw w usłudze Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization)

---

### <a name="updated-saml-based-app-configuration-ui-preview"></a>Zaktualizowany interfejs użytkownika konfiguracji aplikacji opartej na języku SAML (wersja zapoznawcza)

**Typ:** zmieniono funkcji  
**Kategoria usług:** aplikacje dla przedsiębiorstw  
**Możliwości produktu:** logowania jednokrotnego

W ramach naszego interfejsu użytkownika konfiguracji zaktualizowanej aplikacji opartej na SAML uzyskasz:

- Środowisko zaktualizowano wskazówki dotyczące konfigurowania aplikacji opartej na SAML.

- Widoczność o nowościach brakujące lub nieprawidłowe w konfiguracji.

- Możliwość dodawania wielu adresów e-mail, aby uzyskać powiadomienie o wygaśnięciu certyfikatu.

- Nowe metody przekształcania oświadczeń, ToLower() i ToUpper() i inne.

- Sposób przekazać własny token podpisywania certyfikatu dla aplikacji przedsiębiorstwa.

- Sposób, aby ustawić Format identyfikatora NameID dla aplikacji SAML i sposób można ustawić wartości identyfikatora NameID jako rozszerzenia katalogów.

Aby włączyć ten zaktualizowany widok, kliknij przycisk **wypróbować nasze nowe środowisko** link u góry **logowania jednokrotnego** strony. Aby uzyskać więcej informacji, zobacz [samouczek: opartej na SAML skonfigurować logowanie jednokrotne dla aplikacji w usłudze Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-single-sign-on-portal).

---

## <a name="august-2018"></a>Sierpień 2018 r.

### <a name="changes-to-azure-active-directory-ip-address-ranges"></a>Zmiany zakresów adresów IP usługi Azure Active Directory

**Typ:** Planowanie zmian  
**Kategoria usług:** innych  
**Możliwości produktu:** platformy

Wprowadzamy większych zakresów adresów IP do usługi Azure AD, co oznacza, że jeśli skonfigurowano zakresów adresów IP usługi AD platformy Azure dla zapory, routery lub grupy zabezpieczeń sieci, należy je zaktualizować. Wprowadzamy aktualizację, dzięki czemu nie trzeba ponownie zmienić zapory, router lub konfiguracje zakresu adresów IP z grup zabezpieczeń sieci, gdy usługa Azure AD dodaje nowe punkty końcowe. 

Ruch sieciowy przechodzi do tych nowych zakresów w ciągu następnych dwóch miesięcy. Aby kontynuować nieprzerwane działanie usług, należy dodać te zaktualizowane wartości do adresów IP przed 10 września 2018 r.:

- 20.190.128.0/18 

- 40.126.0.0/18 

Zdecydowanie zaleca się nie usuwa starych zakresów adresów IP, dopóki wszystkie ruchu sieciowego została przeniesiona do nowego zakresu. Aktualizacje dotyczące przenoszenia i dowiedzieć się, usunięcie starych zakresów, zobacz [URL usługi Office 365 i zakresy adresów IP](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2).

---

### <a name="change-notice-authorization-codes-will-no-longer-be-available-for-reuse"></a>Powiadomienie o zmianie: kody autoryzacji nie będą już dostępne do ponownego użycia 

**Typ:** Planowanie zmian  
**Kategoria usług:** uwierzytelnienia (logowania)  
**Możliwości produktu:** uwierzytelnianie użytkownika

15 listopada 2018 r. usługi Azure AD będzie akceptować kody poprzednio używanych uwierzytelniania dla aplikacji. Ta zmiana zabezpieczeń pomaga do usługi Azure AD zgodnie ze specyfikacją protokołu OAuth i będzie wymuszany w punktach końcowych v1 i v2.

Jeśli aplikacja używa kody autoryzacji uzyskiwanie tokenów dla wielu zasobów, zalecamy Użyj kodu, aby uzyskać token odświeżania, a następnie używać tego tokenu odświeżania w celu pobrania dodatkowe tokeny dla innych zasobów. Kody autoryzacji należy używać tylko raz, ale tokenów odświeżania mogą być wielokrotnie używane w wielu zasobach. Aplikacja, która podejmuje próbę ponownego użycia kodu uwierzytelniania podczas przepływu kodu OAuth otrzymają komunikat o błędzie invalid_grant.

Ta i inne zmiany dotyczące protokołów, zobacz [pełną listę Nowości dla uwierzytelniania](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes).
 
---

### <a name="converged-security-info-management-for-self-service-password-sspr-and-multi-factor-authentication-mfa"></a>Zarządzanie zbieżnymi informacjami zabezpieczającymi na potrzeby samoobsługowego resetowania hasła i usługi Multi-Factor Authentication (MFA)

**Typ:** nowa funkcja  
**Kategoria usług:** samoobsługowego resetowania HASEŁ  
**Możliwości produktu:** uwierzytelnianie użytkownika
 
Ta nowa funkcja ułatwia osób Zarządzanie swoich informacji zabezpieczających (na przykład, numer telefonu, aplikacja mobilna i tak dalej) dla funkcji samoobsługowego resetowania HASEŁ i uwierzytelniania MFA w jednej lokalizacji i doświadczenie; w porównaniu z wcześniej, gdzie to zostało zrobione w dwóch różnych lokalizacjach.

To środowisko konwergentnej działa także dla osób korzystających z funkcji samoobsługowego resetowania HASEŁ lub uwierzytelniania MFA. Ponadto jeśli Twoja organizacja nie wymusić rejestracji uwierzytelniania Wieloskładnikowego lub samoobsługowego resetowania HASEŁ, osoby nadal można zarejestrować żadnych uwierzytelniania Wieloskładnikowego lub samoobsługowego resetowania HASEŁ zabezpieczeń informacji metod dozwolona przez Twoją organizację, z poziomu portalu Moje aplikacje.

Jest to zgłoszenie zgody na uczestnictwo w publicznej wersji zapoznawczej. Administratorzy mogą włączyć nowe środowisko (w razie potrzeby) dla wybranej grupy lub dla wszystkich użytkowników w dzierżawie. Aby uzyskać więcej informacji na temat doświadczeń z osiągnięcia zbieżności zobacz [Konwergentne środowisko blog](https://cloudblogs.microsoft.com/enterprisemobility/2018/08/06/mfa-and-sspr-updates-now-in-public-preview/)

---

### <a name="new-http-only-cookies-setting-in-azure-ad-application-proxy-apps"></a>Nowe ustawienie Pliki cookie HTTP-Only w aplikacjach serwera proxy aplikacji usługi Azure AD

**Typ:** nowa funkcja  
**Kategoria usług:** serwera Proxy aplikacji  
**Możliwości produktu:** kontroli dostępu

Dostępne jest nowe ustawienie o nazwie, **plików cookie HTTP-Only** w aplikacjach serwer Proxy aplikacji. To ustawienie zapewnia dodatkowe zabezpieczenia, dołączając flagi HTTPOnly w nagłówku odpowiedzi HTTP, zarówno serwer Proxy aplikacji dostępu i sesji plików cookie, zatrzymanie dostępu do pliku cookie. pochodzące ze skryptu po stronie klienta i uniemożliwia dalsze akcje, takie jak kopiowanie lub Modyfikowanie pliku cookie. Chociaż ta flaga nie został wcześniej użyty, pliki cookie były zawsze szyfrowane, a następnie przesyłane przy użyciu połączenia SSL w celu zabezpieczenia przed Niepoprawne modyfikacje.

To ustawienie nie jest zgodna z aplikacjami korzystającymi z kontrolki ActiveX, takich jak pulpitu zdalnego. Jeśli jesteś w takiej sytuacji firma Microsoft zaleca, wyłącz to ustawienie.

Aby uzyskać więcej informacji na temat ustawień plików cookie HTTP-Only zobacz [publikowania aplikacji przy użyciu serwera Proxy aplikacji usługi Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-publish-azure-portal).

---

### <a name="privileged-identity-management-pim-for-azure-resources-supports-management-group-resource-types"></a>Usługa Privileged Identity Management (PIM) dla zasobów platformy Azure obsługuje typy zasobów grupy zarządzania

**Typ:** nowa funkcja  
**Kategoria usług:** Privileged Identity Management  
**Możliwości produktu:** Privileged Identity Management
 
Ustawienia aktywacji i przypisania Just-In-Time można teraz stosować do typów zasobów grupy zarządzania, tak samo, jak już jak w przypadku subskrypcji, grupy zasobów i zasoby (takie jak maszyny wirtualne, App Services i inne). Ponadto każda osoba z rolą, która zapewnia dostęp administratora dla grupy zarządzania może odnajdywać i zarządzać tym zasobem w usłudze PIM.

Aby uzyskać więcej informacji dotyczących usługi PIM i zasobów platformy Azure, zobacz [odnajdywanie i zarządzanie zasobami platformy Azure przy użyciu Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-resource-roles-discover-resources)
 
---

### <a name="application-access-preview-provides-faster-access-to-the-azure-ad-portal"></a>Dostęp do aplikacji (wersja zapoznawcza) umożliwia szybszy dostęp do portalu usługi Azure AD

**Typ:** nowa funkcja  
**Kategoria usług:** Privileged Identity Management  
**Możliwości produktu:** Privileged Identity Management
 
Obecnie podczas aktywacji roli przy użyciu usługi PIM, może potrwać ponad 10 minut uprawnień zastosować zmiany. Jeśli zdecydujesz się używać dostępu do aplikacji, która jest obecnie dostępna w publicznej wersji zapoznawczej, Administratorzy mogą uzyskiwać dostęp do portalu usługi Azure AD zaraz po zakończeniu żądania aktywacji.

Obecnie dostęp do aplikacji obsługuje tylko środowisko portalu usługi Azure AD i zasobów platformy Azure. Aby uzyskać więcej informacji na temat usługi PIM i dostęp do aplikacji, zobacz [co to jest usługa Azure AD Privileged Identity Management?](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure)
 
---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---august-2018"></a>Nowe aplikacje federacyjne dostępne w galerii aplikacji usługi Azure AD — sierpień 2018

**Typ:** nowa funkcja  
**Kategoria usług:** aplikacje dla przedsiębiorstw  
**Możliwości produktu:** 3rd integracji innych firm
 
W sierpnia 2018 Dodaliśmy obsługę 16 nowych aplikacjach z Federacją w galerii aplikacji:

[Hornbill](https://docs.microsoft.com/azure/active-directory/saas-apps/hornbill-tutorial), [Bridgeline niepowiązanych](https://docs.microsoft.com/azure/active-directory/saas-apps/bridgelineunbound-tutorial), [sos Labs — Mobile i testowanie sieci Web](https://docs.microsoft.com/azure/active-directory/saas-apps/saucelabs-mobileandwebtesting-tutorial), [Meta sieci łącznika](https://docs.microsoft.com/azure/active-directory/saas-apps/metanetworksconnector-tutorial), [sposobów](https://docs.microsoft.com/azure/active-directory/saas-apps/waywedo-tutorial), [Spotinst](https://docs.microsoft.com/azure/active-directory/saas-apps/spotinst-tutorial), [ProMaster (przez Inlogik)](https://docs.microsoft.com/azure/active-directory/saas-apps/promaster-tutorial), SchoolBooking, [4me](https://docs.microsoft.com/azure/active-directory/saas-apps/4me-tutorial), [dokumentacji](https://docs.microsoft.com/azure/active-directory/saas-apps/DOSSIER-tutorial), [N2F - wydatków Raporty](https://docs.microsoft.com/azure/active-directory/saas-apps/n2f-expensereports-tutorial), [Czat na żywo Comm100](https://docs.microsoft.com/azure/active-directory/saas-apps/comm100livechat-tutorial), [SafeConnect](https://docs.microsoft.com/azure/active-directory/saas-apps/safeconnect-tutorial), [ZenQMS](https://docs.microsoft.com/azure/active-directory/saas-apps/zenqms-tutorial), [eLuminate](https://docs.microsoft.com/azure/active-directory/saas-apps/eluminate-tutorial), [ Dovetale](https://docs.microsoft.com/azure/active-directory/saas-apps/dovetale-tutorial).

Aby uzyskać więcej informacji o aplikacjach, zobacz [integracji aplikacji SaaS w usłudze Azure Active Directory](https://aka.ms/appstutorial). Aby uzyskać więcej informacji o ofercie swoją aplikację w galerii aplikacji Azure AD, zobacz [umieść swoją aplikację w galerii aplikacji usługi Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="native-tableau-support-is-now-available-in-azure-ad-application-proxy"></a>Natywna obsługa usługi Tableau jest obecnie dostępna na serwerze proxy aplikacji usługi Azure AD

**Typ:** zmieniono funkcji  
**Kategoria usług:** serwera Proxy aplikacji  
**Możliwości produktu:** kontroli dostępu

Za pomocą naszej aktualizacji z OpenID Connect protokołu OAuth 2.0 Code Grant dla naszych protokołu uwierzytelniania wstępnego, masz już konieczności dodatkowego konfigurowania Tableau za pomocą serwera Proxy aplikacji. Ta zmiana protokołu pomaga również lepszą obsługę bardziej nowoczesnych aplikacji przy użyciu tylko przekierowania HTTP, które często są obsługiwane w znacznikach języka JavaScript i HTML serwera Proxy aplikacji.

Aby uzyskać więcej informacji na temat naszych natywną obsługę Tableau, zobacz [aplikacji serwera Proxy Azure AD obecnie z natywną obsługą Tableau](https://blogs.technet.microsoft.com/applicationproxyblog/2018/08/14/azure-ad-application-proxy-now-with-native-tableau-support).

---

### <a name="new-support-to-add-google-as-an-identity-provider-for-b2b-guest-users-in-azure-active-directory-preview"></a>Nowa funkcja obsługi dodawania usługi Google jako dostawcy tożsamości dla użytkowników gości B2B w usłudze Azure Active Directory (wersja zapoznawcza)

**Typ:** nowa funkcja  
**Kategoria usług:** B2B  
**Możliwości produktu:** B2B/B2C

Przez skonfigurowanie Federacji za pomocą usługi Google w Twojej organizacji, można pozwolić zaproszonego logowania użytkowników usługi Gmail udostępnionych aplikacji i zasobów przy użyciu istniejącego konta Google, bez konieczności tworzenia osobistych Account Microsoft (MSA) lub konta usługi Azure AD.

Jest to zgłoszenie zgody na uczestnictwo w publicznej wersji zapoznawczej. Aby uzyskać więcej informacji na temat Federacji Google zobacz [Dodaj Google jako dostawcy tożsamości dla użytkowników-gości B2B](https://docs.microsoft.com/azure/active-directory/b2b/google-federation).

---

## <a name="july-2018"></a>Lipiec 2018 r.

### <a name="improvements-to-azure-active-directory-email-notifications"></a>Udoskonalenia powiadomień e-mail w usłudze Azure Active Directory

**Typ:** zmieniono funkcji  
**Kategoria usług:** innych  
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

### <a name="azure-ad-activity-logs-are-now-available-through-azure-monitor"></a>Dzienniki aktywności usługi Azure AD są teraz dostępne za pośrednictwem usługi Azure Monitor

**Typ:** nowa funkcja  
**Kategoria usług:** raportowania  
**Możliwości produktu:** monitorowanie i raportowanie

Dzienniki aktywności usługi Azure AD są teraz dostępne w publicznej wersji zapoznawczej dla usługi Azure Monitor (Usługa monitorowania całej platformy Azure). Usługa Azure Monitor udostępnia długoterminowego przechowywania i bezproblemową integrację, oprócz tych usprawnień:

- Długoterminowe przechowywanie kierując pliki dzienników z kontem usługi Azure storage.

- Bezproblemowa integracja rozwiązania SIEM, bez konieczności pisania lub obsługa skryptów niestandardowych.

- Bezproblemowa integracja z własnych niestandardowych rozwiązań, narzędzia do analizy lub rozwiązania do zarządzania zdarzeniami.

Aby uzyskać więcej informacji o tych nowych funkcjach, zobacz nasz blog [Dzienniki aktywności usługi Azure AD w usłudze Azure Monitor, diagnostykę znajduje się teraz w publicznej wersji zapoznawczej](https://cloudblogs.microsoft.com/enterprisemobility/2018/07/26/azure-ad-activity-logs-in-azure-monitor-diagnostics-now-in-public-preview/) i naszą dokumentacją [Dzienniki aktywności usługi Azure Active Directory na platformie Azure Monitor (wersja zapoznawcza)](https://docs.microsoft.com/azure/active-directory/reporting-azure-monitor-diagnostics-overview).

---

### <a name="conditional-access-information-added-to-the-azure-ad-sign-ins-report"></a>Informacje o dostępie warunkowym dodane do raportu operacji logowania usługi Azure AD

**Typ:** nowa funkcja  
**Kategoria usług:** raportowania  
**Możliwości produktu:** ochrona i zabezpieczanie tożsamości
 
Ta aktualizacja pozwala zobaczyć, które zasady są oceniane, gdy użytkownik loguje się wraz z wyników zasad. Ponadto raport zawiera teraz typ aplikacji klienckiej używane przez użytkownika, dzięki czemu można zidentyfikować ruch w protokole starszej wersji. Wpisy raportu mogą teraz również być wyszukiwane identyfikator korelacji, który można znaleźć w komunikacie o błędzie widocznych dla użytkownika i może służyć do identyfikowania i rozwiązywania problemów z pasujące żądanie logowania.

---

### <a name="view-legacy-authentications-through-sign-ins-activity-logs"></a>Wyświetlanie starszych uwierzytelnień za pomocą dzienników aktywności logowań

**Typ:** nowa funkcja  
**Kategoria usług:** raportowania  
**Możliwości produktu:** monitorowanie i raportowanie
 
Wraz z wprowadzeniem **aplikację kliencką** dzienniki pola w aktywności logowania, klienci mogą teraz Zobacz użytkowników, którzy używają starszej wersji uwierzytelnienia. Klienci będą mogli korzystać z tych informacji przy użyciu interfejsu API programu Graph w MS logowania lub za pośrednictwem logowania Dzienniki aktywności w portalu usługi Azure AD, w którym można korzystać z **aplikację kliencką** formantu, aby odfiltrować uwierzytelnień starszej wersji. Zapoznaj się z dokumentacją, aby uzyskać więcej informacji.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---july-2018"></a>Nowe aplikacje federacyjne dostępne w galerii aplikacji usługi Azure AD — lipiec 2018

**Typ:** nowa funkcja  
**Kategoria usług:** aplikacje dla przedsiębiorstw  
**Możliwości produktu:** 3rd integracji innych firm
 
W lipca 2018 Dodaliśmy obsługę 16 nowych aplikacjach z Federacją w galerii aplikacji:

[Centrum innowacji](https://docs.microsoft.com/azure/active-directory/saas-apps/innovationhub-tutorial), [Leapsome](https://docs.microsoft.com/azure/active-directory/saas-apps/leapsome-tutorial), [niektóre administrator rejestracji Jednokrotnej](https://docs.microsoft.com/azure/active-directory/saas-apps/certainadminsso-tutorial), przemieszczania PSUC [iPass SmartConnect](https://docs.microsoft.com/azure/active-directory/saas-apps/ipasssmartconnect-tutorial), [zrzut ekranu-O-obie](https://docs.microsoft.com/azure/active-directory/saas-apps/screencast-tutorial) , PowerSchool Unified zajęć, [dołączania Eli](https://docs.microsoft.com/azure/active-directory/saas-apps/elionboarding-tutorial), [obsługę zdalnego Bomgar](https://docs.microsoft.com/azure/active-directory/saas-apps/bomgarremotesupport-tutorial), [Nimblex](https://docs.microsoft.com/azure/active-directory/saas-apps/nimblex-tutorial), [Imagineer WebVision](https://docs.microsoft.com/azure/active-directory/saas-apps/imagineerwebvision-tutorial) , [Insight4GRC](https://docs.microsoft.com/azure/active-directory/saas-apps/insight4grc-tutorial), [łącznika JoinNow SecureW2](https://docs.microsoft.com/azure/active-directory/saas-apps/securejoinnow-tutorial), [Kanbanize](https://review.docs.microsoft.com/azure/active-directory/saas-apps/kanbanize-tutorial), [SmartLPA](https://review.docs.microsoft.com/azure/active-directory/saas-apps/smartlpa-tutorial), [Base umiejętności](https://docs.microsoft.com/azure/active-directory/saas-apps/skillsbase-tutorial)

Aby uzyskać więcej informacji o aplikacjach, zobacz [integracji aplikacji SaaS w usłudze Azure Active Directory](https://aka.ms/appstutorial). Aby uzyskać więcej informacji o ofercie swoją aplikację w galerii aplikacji Azure AD, zobacz [umieść swoją aplikację w galerii aplikacji usługi Azure Active Directory](https://aka.ms/azureadapprequest).

---
 
### <a name="new-user-provisioning-saas-app-integrations---july-2018"></a>Integracje aplikacji SaaS służące do aprowizacji nowych użytkowników — lipiec 2018

**Typ:** nowa funkcja  
**Kategoria usług:** Aprowizację aplikacji  
**Możliwości produktu:** 3rd integracji innych firm
 
Usługa Azure AD pozwala zautomatyzować procesy tworzenia, obsługi i usuwania tożsamości użytkowników w aplikacjach SaaS, takich jak Dropbox, Salesforce, ServiceNow i nie tylko. Do lipca 2018 dodano obsługę następujących aplikacji w galerii aplikacji Azure AD aprowizacji użytkowników:

- [Cisco Spark](https://docs.microsoft.com/azure/active-directory/saas-apps/cisco-spark-provisioning-tutorial)

- [Cisco WebEx](https://docs.microsoft.com/azure/active-directory/saas-apps/cisco-webex-provisioning-tutorial)

- [Bonusly](https://docs.microsoft.com/azure/active-directory/saas-apps/bonusly-provisioning-tutorial)

Aby uzyskać listę wszystkich aplikacji, które obsługują aprowizowania użytkowników w galerii usługi Azure AD, zobacz [integracji aplikacji SaaS w usłudze Azure Active Directory](https://aka.ms/appstutorial).

---

### <a name="connect-health-for-sync---an-easier-way-to-fix-orphaned-and-duplicate-attribute-sync-errors"></a>Connect Health for Sync — prostszy sposób naprawiania błędów synchronizacji związanych z osieroconymi lub zduplikowanymi atrybutami

**Typ:** nowa funkcja  
**Kategoria usług:** AD Connect  
**Możliwości produktu:** monitorowanie i raportowanie
 
Program Azure AD Connect Health wprowadza samodzielne rozwiązanie problemu, ułatwiające wyróżnić i naprawić błędy synchronizacji. Ta funkcja rozwiązuje błędy synchronizacji zduplikowanym atrybutem i poprawki obiektów, które są oddzielone od usługi Azure AD. Diagnostyka to ma następujące zalety:

- Zawęża zduplikowanym atrybutem błędy synchronizacji, zapewniając określonej poprawki

- Stosuje poprawki dla scenariuszy usługi Azure AD, naprawianie błędów w jednym kroku w wersji dedykowanej

- Nie uaktualnienia lub konfiguracja jest wymagana do włączenia i korzystać z tej funkcji

Aby uzyskać więcej informacji, zobacz [diagnozowanie i rozwiązywanie błędów synchronizacji zduplikowanym atrybutem](https://docs.microsoft.com/azure/active-directory/connect-health/active-directory-aadconnect-health-diagnose-sync-errors)

---

### <a name="visual-updates-to-the-azure-ad-and-msa-sign-in-experiences"></a>Aktualizacje wizualne w środowiskach logowania usługi Azure AD i MSA.

**Typ:** zmieniono funkcji  
**Kategoria usług:** usługi Azure AD  
**Możliwości produktu:** uwierzytelnianie użytkownika

Zaktualizowaliśmy interfejs użytkownika dla usług online firmy Microsoft środowiska logowania takich jak dla usługi Office 365 i Azure. Ta zmiana sprawia, że ekrany mniej zatłoczoną i bardziej bezpośredni. Aby uzyskać więcej informacji na temat tej zmiany, zobacz [nadchodzące ulepszenia procesu logowania w usłudze Azure AD](https://cloudblogs.microsoft.com/enterprisemobility/2018/04/04/upcoming-improvements-to-the-azure-ad-sign-in-experience/) blogu.

---

### <a name="new-release-of-azure-ad-connect---july-2018"></a>Nowa wersja programu Azure AD Connect — lipiec 2018

**Typ:** zmieniono funkcji  
**Kategoria usług:** Aprowizację aplikacji  
**Możliwości produktu:** Zarządzanie cyklem życia tożsamości

Najnowsza wersja programu Azure AD Connect obejmuje: 

- Poprawki i aktualizacje obsługi 

- Ogólne udostępnienie usług integracji Federację Ping

- Aktualizacje do najnowszego klienta SQL 2012 

Aby uzyskać więcej informacji na temat tej aktualizacji, zobacz [program Azure AD Connect: Historia wersji](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-version-history)

---

### <a name="updates-to-the-terms-of-use-tou-end-user-ui"></a>Aktualizacje interfejsu użytkownika końcowego warunków użytkowania

**Typ:** zmieniono funkcji  
**Kategoria usług:** warunki użytkowania  
**Możliwości produktu:** nadzoru

Trwa aktualizowanie ciągu akceptacji w Interfejsie użytkownika końcowego warunków użytkowania.

**Aktualny tekst.** Aby uzyskać dostęp do zasobów [tenantName], musisz zaakceptować warunki użytkowania.<br>**Nowy tekst.** Aby uzyskać dostęp do zasobów [tenantName], możesz przeczytać warunki użytkowania.

**Aktualny tekst:** wybierając do akceptowania oznacza, że zgadzasz się na wszystkie powyższe warunki użytkowania.<br>**Nowy tekst:** , kliknij przycisk Zaakceptuj, aby upewnić się, że użytkownik przeczytał i zrozumiał warunki użytkowania.

---
 
### <a name="pass-through-authentication-supports-legacy-protocols-and-applications"></a>Uwierzytelnianie przekazywane obsługuje starsze protokoły i aplikacje

**Typ:** zmieniono funkcji  
**Kategoria usług:** uwierzytelnienia (logowania)  
**Możliwości produktu:** uwierzytelnianie użytkownika
 
Uwierzytelnianie przekazywane teraz obsługuje starszych protokołów i aplikacji. Następujące ograniczenia są teraz w pełni obsługiwane:

- Logowania użytkowników do starszych aplikacji klienckich pakietu Office, pakietu Office 2010 i Office 2013 bez nowoczesnego uwierzytelniania.

- Dostęp do udostępnianie kalendarza i bezpłatne zajęty informacji w programie Exchange środowisk o hybrydowych w pakiecie Office 2010 tylko.

- Logowania użytkowników do usługi Skype dla firm aplikacji klienckich bez nowoczesnego uwierzytelniania.

- Użytkownik logowania do programu PowerShell w wersji 1.0.

- Programu Apple Device Enrollment Program (DEP) firmy Apple, przy użyciu Asystenta ustawień systemu iOS. 

---
 
### <a name="converged-security-info-management-for-self-service-password-reset-and-multi-factor-authentication"></a>Zarządzanie zbieżnymi informacjami zabezpieczającymi na potrzeby samoobsługowego resetowania hasła i uwierzytelniania wieloskładnikowego

**Typ:** nowa funkcja  
**Kategoria usług:** samoobsługowego resetowania HASEŁ  
**Możliwości produktu:** uwierzytelnianie użytkownika

Ta nowa funkcja umożliwia użytkownikom zarządzanie zabezpieczające (na przykład, numer telefonu, adres e-mail, aplikacji mobilnej i tak dalej) do samoobsługowego resetowania haseł (SSPR) i usługi Multi-Factor Authentication (MFA) w jednym środowisku. Użytkownicy mają już nie można zarejestrować tego samego jako informacje zabezpieczające dla funkcji samoobsługowego resetowania HASEŁ i uwierzytelniania Wieloskładnikowego w dwóch różnych środowiskach. Nowe środowisko dotyczy również użytkowników, którzy mają funkcji samoobsługowego resetowania HASEŁ lub uwierzytelniania MFA.

Jeśli organizacja nie ma wymuszanie rejestracji uwierzytelniania Wieloskładnikowego lub samoobsługowego resetowania HASEŁ, użytkownicy mogą rejestrować swoje informacje zabezpieczające za pośrednictwem **Moje aplikacje** portalu. W tym miejscu użytkownicy mogą rejestrować żadnych metod włączone dla uwierzytelniania Wieloskładnikowego lub samoobsługowego resetowania HASEŁ. 

Jest to zgłoszenie zgody na uczestnictwo w publicznej wersji zapoznawczej. Administratorzy mogą włączyć nowe środowisko (w razie potrzeby) dla wybranej grupy użytkowników lub dla wszystkich użytkowników w dzierżawie.

---
 
### <a name="use-the-microsoft-authenticator-app-to-verify-your-identity-when-you-reset-your-password"></a>Korzystanie z aplikacji Microsoft Authenticator w celu weryfikowania swojej tożsamości podczas resetowania hasła

**Typ:** zmieniono funkcji  
**Kategoria usług:** samoobsługowego resetowania HASEŁ  
**Możliwości produktu:** uwierzytelnianie użytkownika

Ta funkcja pozwala użytkowników innych niż administratorzy zweryfikować swoją tożsamość podczas resetowania hasła przy użyciu powiadomienia lub kodu z Microsoft Authenticator (lub dowolną inną aplikację). Po Administratorzy, Włącz ten samoobsługowego resetowania haseł — metoda, użytkownicy, którzy zarejestrowali się do aplikacji mobilnej za pośrednictwem aka.ms/mfasetup lub aka.ms/setupsecurityinfo można użyć aplikacji mobilnej jako metody weryfikacji podczas resetowania hasła.

Tylko powiadomienia aplikacji mobilnej może zostać włączona w ramach zasad, które wymagają dwóch metod, aby zresetować hasło.

---

## <a name="june-2018"></a>Czerwiec 2018 r.

### <a name="change-notice-security-fix-to-the-delegated-authorization-flow-for-apps-using-azure-ad-activity-logs-api"></a>Zmień powiadomienie: poprawki zabezpieczeń, aby przepływ delegowanej autoryzacji dla aplikacji przy użyciu interfejsu API usługi Azure AD działania dzienniki

**Typ:** Planowanie zmian  
**Kategoria usług:** raportowania  
**Możliwości produktu:** monitorowanie i raportowanie

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

**Typ:** nowa funkcja  
**Kategoria usług:** n/d  
**Możliwości produktu:** platformy

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

**Typ:** nowa funkcja  
**Kategoria usług:** aplikacje dla przedsiębiorstw  
**Możliwości produktu:** 3rd integracji innych firm
 
W czerwcu 2018 Dodaliśmy obsługę tych 15 nowe aplikacje z Federacją w galerii aplikacji:

[Skytap](https://docs.microsoft.com/azure/active-directory/active-directory-saas-skytap-tutorial), [rozliczania utworów muzycznych](https://docs.microsoft.com/azure/active-directory/active-directory-saas-settlingmusic-tutorial), [aplikacji LOB z obsługą tokenu języka SAML 1.1](https://docs.microsoft.com/azure/active-directory/active-directory-saas-saml-tutorial), [Supermood](https://docs.microsoft.com/azure/active-directory/active-directory-saas-supermood-tutorial), [Autotask](https://docs.microsoft.com/azure/active-directory/active-directory-saas-autotaskendpointbackup-tutorial), [ Kopia zapasowa punktu końcowego](https://docs.microsoft.com/azure/active-directory/active-directory-saas-autotaskendpointbackup-tutorial), [sieci Skyhigh](https://docs.microsoft.com/azure/active-directory/active-directory-saas-skyhighnetworks-tutorial), Smartway2, [TonicDM](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tonicdm-tutorial), [Moconavi](https://docs.microsoft.com/azure/active-directory/active-directory-saas-moconavi-tutorial), [Zoho jeden](https://docs.microsoft.com/azure/active-directory/active-directory-saas-zohoone-tutorial), [ Lokalny program SharePoint](https://docs.microsoft.com/azure/active-directory/active-directory-saas-sharepoint-on-premises-tutorial), [przewiduje CX Suite](https://docs.microsoft.com/azure/active-directory/active-directory-saas-foreseecxsuite-tutorial), [Vidyard](https://docs.microsoft.com/azure/active-directory/active-directory-saas-vidyard-tutorial), [ChronicX](https://docs.microsoft.com/azure/active-directory/active-directory-saas-chronicx-tutorial)

Aby uzyskać więcej informacji o aplikacjach, zobacz [integracji aplikacji SaaS w usłudze Azure Active Directory](https://aka.ms/appstutorial). Aby uzyskać więcej informacji o ofercie swoją aplikację w galerii aplikacji Azure AD, zobacz [umieść swoją aplikację w galerii aplikacji usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing). 

---

### <a name="azure-ad-password-protection-is-available-in-public-preview"></a>Ochrony hasłem w usłudze Azure AD jest dostępna w publicznej wersji zapoznawczej

**Typ:** nowa funkcja  
**Kategoria usług:** Identity Protection  
**Możliwości produktu:** uwierzytelnianie użytkownika

Użyj hasła usługi Azure AD ochrony, aby wyeliminować łatwe do odgadnięcia hasła ze środowiska. Eliminowanie haseł pomaga zmniejszyć ryzyko naruszenia zabezpieczeń z atak typu osłony hasła.

W szczególności ochrony haseł usługi Azure AD umożliwia:

- Ochronę kont w organizacji w usługi Azure AD i systemu Windows Server Active Directory (AD). 
- Zatrzymuje użytkownikom korzystanie z haseł na liście więcej niż 500 najczęściej używanych haseł, a ponad 1 milion znaków podstawienia odmiany tych haseł.
- Administrowanie ochrony haseł usługi Azure AD z jednej lokalizacji w portalu usługi Azure AD dla usługi Azure AD i AD systemu Windows Server dla lokalnego.

Aby uzyskać więcej informacji na temat ochrony haseł usługi Azure AD, zobacz [eliminowanie nieprawidłowych haseł w organizacji](https://aka.ms/aadpasswordprotectiondocs).

---

### <a name="new-all-guests-conditional-access-policy-template-created-during-terms-of-use-tou-creation"></a>Nowych "wszystkich gości" dostępu warunkowego szablonu zasad podczas tworzenia warunków użytkowania (ToU)

**Typ:** nowa funkcja  
**Kategoria usług:** warunki użytkowania  
**Możliwości produktu:** nadzoru

Podczas tworzenia warunków użytkowania (ToU) tworzony jest też nowy szablon zasad dostępu warunkowego dla "wszystkich gości" i "wszystkie aplikacje". Ten nowy szablon zasad mają zastosowanie nowo utworzony warunków użytkowania, co usprawnia tworzenie i wymuszanie procesu dla gości.

Aby uzyskać więcej informacji, zobacz [Azure Active Directory — warunki dotyczące użycia funkcji](https://docs.microsoft.com/azure/active-directory/active-directory-tou).

---

### <a name="new-custom-conditional-access-policy-template-created-during-terms-of-use-tou-creation"></a>Nowy szablon zasad dostępu warunkowego "niestandardowy", podczas tworzenia warunków użytkowania (ToU)

**Typ:** nowa funkcja  
**Kategoria usług:** warunki użytkowania  
**Możliwości produktu:** nadzoru

Podczas tworzenia warunków użytkowania (ToU) również tworzony jest nowy szablon zasad dostępu warunkowego "niestandardowe". Ten nowy szablon zasad umożliwia tworzenie warunków użytkowania, a następnie od razu przejść do bloku tworzenia zasad dostępu warunkowego, bez konieczności ręcznego przejdź za pośrednictwem portalu.

Aby uzyskać więcej informacji, zobacz [Azure Active Directory — warunki dotyczące użycia funkcji](https://docs.microsoft.com/azure/active-directory/active-directory-tou).

---

### <a name="new-and-comprehensive-guidance-about-deploying-azure-multi-factor-authentication"></a>Nowe i wyczerpujące wskazówki dotyczące wdrażania usługi Azure Multi-Factor Authentication

**Typ:** nowa funkcja  
**Kategoria usług:** innych  
**Możliwości produktu:** ochrona i zabezpieczanie tożsamości
 
Udostępniliśmy nowe wskazówki krok po kroku dotyczące wdrażania usługi Azure Multi-Factor Authentication (MFA) w Twojej organizacji.

Aby wyświetlić w przewodniku wdrażania uwierzytelniania Wieloskładnikowego, przejdź do [wskazówki dotyczące wdrażania tożsamości](https://aka.ms/DeploymentPlans) repozytorium w witrynie GitHub. Aby przekazać opinię na temat wskazówki dotyczące wdrażania, użyj [wdrożenia planu forum z opiniami](https://aka.ms/deploymentplanfeedback). Jeśli masz jakieś pytania dotyczące wskazówki dotyczące wdrażania, skontaktuj się z nami pod adresem [IDGitDeploy](mailto:idgitdeploy@microsoft.com).

---

### <a name="azure-ad-delegated-app-management-roles-are-in-public-preview"></a>Usługa Azure AD delegowane Zarządzanie aplikacjami, które role są w publicznej wersji zapoznawczej

**Typ:** nowa funkcja  
**Kategoria usług:** aplikacje dla przedsiębiorstw  
**Możliwości produktu:** kontroli dostępu

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

**Typ:** Planowanie zmian  
**Kategoria usług:** uwierzytelnienia (logowania)  
**Możliwości produktu:** platformy  

Oprogramowanie jako usługa, takich jak Azure Active Directory (Azure AD) są przeznaczone do najlepszej metody, przechodząc bezpośrednio przez Internet, bez korzystania z usługi ExpressRoute lub innych prywatnej tuneli sieci VPN. W związku z tym na **1 sierpnia 2018**, firma Microsoft nie będzie obsługiwać usługi ExpressRoute dla usługi Azure AD przy użyciu publicznej komunikacji równorzędnej Azure i społeczności platformy Azure w komunikacji równorzędnej firmy Microsoft. Wszystkie usługi, wpływ tej zmiany może się okazać, Azure AD ruchu stopniowo przesunięcie z usługi ExpressRoute z Internetem.

Zmieniamy się obsłudze, Wiemy także, są nadal sytuacji, w których konieczne może być użyć to dedykowany zestaw obwodów dla ruchu uwierzytelniania. W związku z tym usługa Azure AD będzie obsługiwać ograniczenia zakresu adresów IP dla dzierżawcy przy użyciu usługi ExpressRoute i usług już na komunikacji równorzędnej firmy Microsoft ze społecznością "Innych usług Online pakietu Office 365". Jeśli dotyczy usług, ale wymagają usługi ExpressRoute, należy wykonać następujące czynności:

- **Jeśli komputer jest w publicznej komunikacji równorzędnej Azure.** Przenieś do komunikacji równorzędnej firmy Microsoft i zarejestrować się w celu **inne usługi Online Office 365 (12076:5100)** społeczności. Aby uzyskać więcej informacji o tym, jak można przenieść z publicznej komunikacji równorzędnej Azure do komunikacji równorzędnej firmy Microsoft, zobacz [Przenieś publicznej komunikacji równorzędnej do komunikacji równorzędnej firmy Microsoft](https://docs.microsoft.com/azure/expressroute/how-to-move-peering) artykułu.

- **Jeśli jesteś w komunikacji równorzędnej firmy Microsoft.** Zaloguj się w celu **inne usługi Office 365 Online service (12076:5100)** społeczności. Aby uzyskać więcej informacji na temat wymagania dotyczące routingu, zobacz [obsługę protokołu BGP społeczności sekcji](https://docs.microsoft.com/azure/expressroute/expressroute-routing#bgp) artykułu wymagania dotyczące routingu usługi ExpressRoute.

Jeśli musisz nadal używać dedykowanego obwodów, konieczne będzie komunikować się zespołem Account firmy Microsoft o tym, jak uzyskać pozwolenie na korzystanie **inne usługi Office 365 Online service (12076:5100)** społeczności. Tablica zarządzana Office przeglądu MS sprawdzi, czy potrzebujesz tych obwodów i upewnij się, że rozumiesz implikacje techniczne ich zachowania. Subskrypcje nieautoryzowane próby utworzenia filtrów tras dla usługi Office 365 będą otrzymywać komunikat o błędzie. 
 
---

### <a name="microsoft-graph-apis-for-administrative-scenarios-for-tou"></a>Interfejsy API programu Graph firmy Microsoft dla scenariuszy administracyjnych dla warunków użytkowania

**Typ:** nowa funkcja  
**Kategoria usług:** warunki użytkowania  
**Możliwości produktu:** środowisko programistyczne
 
Dodaliśmy interfejsy API programu Microsoft Graph dla operacji administracyjnych programu Azure AD warunki użytkowania. Jesteś w stanie tworzenie, aktualizowanie i usuwanie obiektu warunki użytkowania.

---

### <a name="add-azure-ad-multi-tenant-endpoint-as-an-identity-provider-in-azure-ad-b2c"></a>Dodaj punkt końcowy z wieloma dzierżawami usługi Azure AD jako dostawcy tożsamości w usłudze Azure AD B2C

**Typ:** nowa funkcja  
**Kategoria usług:** B2C - zarządzania tożsamościami klientów  
**Możliwości produktu:** B2B/B2C
 
Za pomocą zasad niestandardowych, możesz teraz dodać wspólnego punktu końcowego usługi Azure AD jako dostawcy tożsamości w usłudze Azure AD B2C. Umożliwia to pojedynczy punkt wejścia dla wszystkich użytkowników usługi Azure AD, którzy logują się do aplikacji. Aby uzyskać więcej informacji, zobacz [usługi Azure Active Directory B2C: Zezwalaj użytkownikom na logowanie się do dostawcy tożsamości wielu dzierżaw usługi Azure AD za pomocą zasad niestandardowych](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-setup-commonaad-custom).

---

### <a name="use-internal-urls-to-access-apps-from-anywhere-with-our-my-apps-sign-in-extension-and-the-azure-ad-application-proxy"></a>Wewnętrzne adresy URL umożliwia dostęp do aplikacji z dowolnego miejsca za pomocą naszego rozszerzenia Moje aplikacje logowania i serwera Proxy aplikacji usługi AD systemu Azure

**Typ:** nowa funkcja  
**Kategoria usług:** Moje aplikacje  
**Możliwości produktu:** logowania jednokrotnego
 
Użytkownicy mogą teraz uzyskiwać dostęp do aplikacji za pomocą wewnętrznych adresów URL nawet spoza sieci firmowej przy użyciu bezpiecznego Moje aplikacje logowania rozszerzenia dla usługi Azure AD. To będzie działać z dowolnej aplikacji, która została opublikowana przy użyciu serwera Proxy aplikacji usługi Azure AD, w dowolnej przeglądarce, która także ma zainstalowane rozszerzenie przeglądarki panelu dostępu. Funkcji Przekierowywanie adresu URL jest automatycznie włączone, gdy użytkownik loguje się do rozszerzenia. Rozszerzenie jest dostępne do pobrania na [Microsoft Edge](https://go.microsoft.com/fwlink/?linkid=845176), [Chrome](https://go.microsoft.com/fwlink/?linkid=866367), i [Firefox](https://go.microsoft.com/fwlink/?linkid=866366).

---
 
### <a name="azure-active-directory---data-in-europe-for-europe-customers"></a>Usługa Azure Active Directory — dane w Europie klienci Europa

**Typ:** nowa funkcja  
**Kategoria usług:** innych  
**Możliwości produktu:** GoLocal

Klienci w Europie wymagają swoich danych, aby pozostać w Europie i nie są replikowane poza Europejskiego centrów danych na potrzeby spotkania i poufności informacji w europejskich przepisów. To [artykułu](https://go.microsoft.com/fwlink/?linkid=872328) zawiera szczegółowe informacje, jakie informacje tożsamości będą przechowywane w Europie i udostępniają szczegółowe informacje na temat informacji, który ma być przechowywana poza Europejskich centrach danych. 

---
 
### <a name="new-user-provisioning-saas-app-integrations---may-2018"></a>Nowy użytkownik inicjowania obsługi administracyjnej integracji aplikacji SaaS — maj 2018 r.

**Typ:** nowa funkcja  
**Kategoria usług:** Aprowizację aplikacji  
**Możliwości produktu:** 3rd integracji innych firm
 
Usługa Azure AD pozwala zautomatyzować procesy tworzenia, obsługi i usuwania tożsamości użytkowników w aplikacjach SaaS, takich jak Dropbox, Salesforce, ServiceNow i nie tylko. W maju 2018 r Dodaliśmy obsługę następujących aplikacji w galerii aplikacji Azure AD aprowizacji użytkowników:

- [BlueJeans](https://docs.microsoft.com/azure/active-directory/active-directory-saas-bluejeans-provisioning-tutorial)

- [OnDemand podstawy](https://docs.microsoft.com/azure/active-directory/active-directory-saas-cornerstone-ondemand-provisioning-tutorial)

- [Zendesk](https://docs.microsoft.com/azure/active-directory/active-directory-saas-zendesk-provisioning-tutorial)

Aby uzyskać listę wszystkich aplikacji, które obsługują aprowizowania użytkowników w galerii usługi Azure AD, zobacz [ https://aka.ms/appstutorial ](https://aka.ms/appstutorial).

---
 
### <a name="azure-ad-access-reviews-of-groups-and-app-access-now-provides-recurring-reviews"></a>Przeglądy dostępu usługi Azure AD, grup i przeglądy cyklicznego udostępnia teraz dostęp do aplikacji

**Typ:** nowa funkcja  
**Kategoria usług:** przeglądów dostępu  
**Możliwości produktu:** nadzoru
 
Przegląd dostępu grup i aplikacji jest teraz ogólnie dostępna w ramach usługi Azure AD Premium P2.  Administratorzy, będzie można skonfigurować przeglądów dostępu członkostwa w grupach i przypisania aplikacji, aby automatycznie powtarzać w regularnych odstępach czasu, takich jak co miesiąc lub kwartalną.

---

### <a name="azure-ad-activity-logs-sign-ins-and-audit-are-now-available-through-ms-graph"></a>Dzienniki aktywności usługi AD platformy Azure (logowania i inspekcji) są teraz dostępne za pośrednictwem MS Graph

**Typ:** nowa funkcja  
**Kategoria usług:** raportowania  
**Możliwości produktu:** monitorowanie i raportowanie
 
Dzienniki aktywności usługi AD platformy Azure, co obejmuje dzienniki inspekcji i logowania, są teraz dostępne za pośrednictwem MS Graph. Udostępniliśmy dwie punktów końcowych za pośrednictwem MS Graph dostępu do tych dzienników. Zapoznaj się z naszym [dokumenty](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-getting-started-azure-portal) uzyskać programowy dostęp do usługi Azure AD interfejsy API raportowania na rozpoczęcie pracy. 

---
 
### <a name="improvements-to-the-b2b-redemption-experience-and-leave-an-org"></a>Ulepszenia realizacji B2B środowisko i opuścić organizacji

**Typ:** nowa funkcja  
**Kategoria usług:** B2B  
**Możliwości produktu:** B2B/B2C

**Dokładnie na czas realizacji:** po udostępniać zasobu z użytkownikiem-gościem, przy użyciu interfejsu API B2B — nie jest potrzebny do wysłania wiadomości e-mail z zaproszeniem specjalne. W większości przypadków użytkownik-Gość można uzyskać dostępu do zasobu i zostaną podjęte przez środowisko realizacji dokładnie na czas. Nie wpływa na z powodu brakujących wiadomości e-mail. Nie ma więcej poproszenie użytkowników gości "Czy możesz kliknąć łącze realizacji wysłania systemu, możesz?". Oznacza to, gdy SPO korzysta z Menedżera zaproszenie — mętna załączniki mogą mieć ten sam Kanoniczny adres URL dla wszystkich użytkowników — wewnętrzne i zewnętrzne — w dowolnym stanie realizacji.

**Środowisko realizacji nowoczesnych:** ma więcej podziału ekranu realizacji strona docelowa. Użytkownicy będą widzieć nowoczesny zgoda doświadczenia z organizacji zapraszającej zasady zachowania poufności informacji, podobnie jak w przypadku aplikacji innych firm.

**Użytkownicy-goście, można pozostawić organizacji:** po relację użytkownika w organizacji mogą one własnym służyć opuszczania organizacji. Nie ma więcej wywoływania administratora organizacji zapraszającej o "usunięte", nie ma więcej wywoływanie biletami pomocy technicznej.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---may-2018"></a>Nowe aplikacje federacyjne dostępne w galerii aplikacji usługi Azure AD — maj 2018 r.

**Typ:** nowa funkcja  
**Kategoria usług:** aplikacje dla przedsiębiorstw  
**Możliwości produktu:** 3rd integracji innych firm
 
W maju 2018 Dodaliśmy obsługę tych 18 nowe aplikacje z Federacją do galerii aplikacji:

[AwardSpring](https://docs.microsoft.com/azure/active-directory/active-directory-saas-awardspring-tutorial), określają sposób Infogix Data3Sixty, [Yodeck](https://docs.microsoft.com/azure/active-directory/active-directory-saas-infogix-tutorial), [narzędzia Jamf Pro](https://docs.microsoft.com/azure/active-directory/active-directory-saas-jamfprosamlconnector-tutorial), [KnowledgeOwl](https://docs.microsoft.com/azure/active-directory/active-directory-saas-knowledgeowl-tutorial), [Envi MMIS](https://docs.microsoft.com/azure/active-directory/active-directory-saas-envimmis-tutorial), [LaunchDarkly](https://docs.microsoft.com/azure/active-directory/active-directory-saas-launchdarkly-tutorial), [Adobe Captivate Prime](https://docs.microsoft.com/azure/active-directory/active-directory-saas-adobecaptivateprime-tutorial), [Online montażowy](https://docs.microsoft.com/azure/active-directory/active-directory-saas-montageonline-tutorial),[まなびポケット](https://docs.microsoft.com/azure/active-directory/active-directory-saas-manabipocket-tutorial), OpenReel, [łuk publikowania — Usługa rejestracji Jednokrotnej ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-arc-tutorial), [PlanGrid](https://docs.microsoft.com/azure/active-directory/active-directory-saas-plangrid-tutorial), [iWellnessNow](https://docs.microsoft.com/azure/active-directory/active-directory-saas-iwellnessnow-tutorial), [Proxyclick](https://docs.microsoft.com/azure/active-directory/active-directory-saas-proxyclick-tutorial), [Riskware](https://docs.microsoft.com/azure/active-directory/active-directory-saas-riskware-tutorial), [stada](https://docs.microsoft.com/azure/active-directory/active-directory-saas-flock-tutorial), [Reviewsnap](https://docs.microsoft.com/azure/active-directory/active-directory-saas-reviewsnap-tutorial)

Aby uzyskać więcej informacji o aplikacjach, zobacz [integracji aplikacji SaaS w usłudze Azure Active Directory](https://aka.ms/appstutorial).

Aby uzyskać więcej informacji o ofercie swoją aplikację w galerii aplikacji Azure AD, zobacz [umieść swoją aplikację w galerii aplikacji usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing).

---
 
### <a name="new-step-by-step-deployment-guides-for-azure-active-directory"></a>Nowe przewodniki krok po kroku wdrożenia usługi Azure Active Directory

**Typ:** nowa funkcja  
**Kategoria usług:** innych  
**Możliwości produktu:** katalogu
 
Nowy, krok po kroku wskazówki dotyczące wdrażania usługi Azure Active Directory (Azure AD), takie jak Samoobsługowe resetowanie haseł (SSPR), pojedynczego logowania jednokrotnego (SSO), dostępu warunkowego (CA), serwer proxy aplikacji, aprowizacja Active Directory Federation Services (ADFS) do użytkowników Uwierzytelnianie przekazywane (PTA) i usług AD FS do synchronizacji skrótów haseł (wersji).

Aby wyświetlić wskazówki dotyczące wdrażania, przejdź do [wskazówki dotyczące wdrażania tożsamości](https://aka.ms/DeploymentPlans) repozytorium w witrynie GitHub. Aby przekazać opinię na temat wskazówki dotyczące wdrażania, użyj [wdrożenia planu forum z opiniami](https://aka.ms/deploymentplanfeedback). Jeśli masz jakieś pytania dotyczące wskazówki dotyczące wdrażania, skontaktuj się z nami pod adresem [IDGitDeploy](mailto:idgitdeploy@microsoft.com).

---

### <a name="enterprise-applications-search---load-more-apps"></a>Aplikacje dla przedsiębiorstw wyszukiwanie - ładowanie większej liczby aplikacji

**Typ:** nowa funkcja  
**Kategoria usług:** aplikacje dla przedsiębiorstw  
**Możliwości produktu:** logowania jednokrotnego
 
Problem ze znalezieniem informacji aplikacji / jednostki usług? Dodaliśmy możliwość ładowania większej liczby aplikacji w aplikacjach firmowych listę wszystkich aplikacji. Domyślnie pokazujemy 20 aplikacji. Możesz teraz kliknąć Link, **Załaduj więcej** Aby wyświetlić dodatkowe aplikacje. 

---
 
### <a name="the-may-release-of-aadconnect-contains-a-public-preview-of-the-integration-with-pingfederate-important-security-updates-many-bug-fixes-and-new-great-new-troubleshooting-tools"></a>May wersji AADConnect zawiera publiczną wersję zapoznawczą integracji z serwerem PingFederate, ważne aktualizacje zabezpieczeń, wiele poprawek usterek i nowe great nowych narzędzi do rozwiązywania problemów. 

**Typ:** zmieniono funkcji  
**Kategoria usług:** AD Connect  
**Możliwości produktu:** Zarządzanie cyklem życia tożsamości
 
May wersji AADConnect zawiera publiczną wersję zapoznawczą integracji z serwerem PingFederate, ważne aktualizacje zabezpieczeń, wiele poprawek usterek i nowe great nowych narzędzi do rozwiązywania problemów. Można znaleźć informacje o wersji [tutaj](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-version-history#118190).

---

### <a name="azure-ad-access-reviews-auto-apply"></a>Przeglądy dostępu w usłudze Azure AD: automatycznie Zastosuj

**Typ:** zmieniono funkcji  
**Kategoria usług:** przeglądów dostępu  
**Możliwości produktu:** nadzoru

Przeglądy dostępu dotyczące dostępu grupy i aplikacje są teraz ogólnie dostępne w ramach usługi Azure AD Premium P2. Administrator może skonfigurować, aby automatycznie zastosować zmiany wprowadzone przez recenzenta do tej grupy lub aplikacji, ponieważ zakończeniu przeglądu dostępu. Administrator może również określić, co się dzieje z przedłużenia dostępu użytkownika, jeśli recenzenci nie odpowie, spowodować usunięcie dostępu, móc chronić dostęp do lub rekomendacje systemu. 

---

### <a name="id-tokens-can-no-longer-be-returned-using-the-query-responsemode-for-new-apps"></a>Tokeny Identyfikatora już nie mogą być zwracane w przypadku nowych aplikacji przy użyciu response_mode zapytania. 

**Typ:** zmieniono funkcji  
**Kategoria usług:** uwierzytelnienia (logowania)  
**Możliwości produktu:** uwierzytelnianie użytkownika
 
Aplikacje utworzone na lub po 25 kwietnia 2018 r. nie będzie już mógł zażądać **id_token** przy użyciu **zapytania** response_mode.  To umożliwia wbudowanej usługi Azure AD ze specyfikacjami OIDC, a także pomaga zmniejszyć obszar ataków aplikacji.  Aplikacje utworzone przed 25 kwietnia 2018 r. nie jest zablokowany za pomocą **zapytania** response_mode przy użyciu elementu response_type z **id_token**.  Błąd zwrócony podczas żądania elementu id_token z usługi AAD, **AADSTS70007: "query" nie jest obsługiwaną wartością "response_mode" podczas żądania tokenu**.

**Fragmentu** i **form_post** response_modes w dalszym ciągu działać — podczas tworzenia nowych obiektów aplikacji (na przykład w przypadku użycia serwera Proxy aplikacji), upewnij się użycie jednej z tych response_modes, przed utworzeniem Nowa aplikacja.  

---