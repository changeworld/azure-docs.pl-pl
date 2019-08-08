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
ms.date: 07/31/2019
ms.author: lizross
ms.reviewer: dhanyahk
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: b338ad90c01c109cc9b2e222f1d7bcaa09f20346
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/06/2019
ms.locfileid: "68812953"
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

## <a name="january-2019"></a>Styczeń 2019

### <a name="active-directory-b2b-collaboration-using-one-time-passcode-authentication-public-preview"></a>Współpraca B2B w usłudze Active Directory przy użyciu uwierzytelniania za pomocą jednorazowego kodu dostępu (publiczna wersja zapoznawcza)

**Wprowadź** Nowa funkcja  
**Kategoria usługi:** B2B  
**Możliwość produktu:** B2B/B2C

Wprowadziliśmy jednorazowe uwierzytelnianie kodu dostępu (OTP) dla użytkowników Gości B2B, którzy nie mogą być uwierzytelniani przy użyciu innych metod, takich jak Azure AD, konto Microsoft (MSA) lub Google Federation. Ta nowa metoda uwierzytelniania oznacza, że użytkownicy-Goście nie muszą tworzyć nowych konto Microsoft. Zamiast tego podczas realizowania zaproszenia lub uzyskiwania dostępu do zasobu udostępnionego użytkownik-Gość może zażądać, aby kod tymczasowy został wysłany na adres e-mail. Przy użyciu tego kodu tymczasowego użytkownik-Gość może nadal logować się.

Aby uzyskać więcej informacji, zobacz [jednorazowe uwierzytelnianie kodu dostępu za pomocą poczty e-mail (wersja zapoznawcza)](https://docs.microsoft.com/azure/active-directory/b2b/one-time-passcode) i blog, [usługa Azure AD zapewnia bezproblemowe udostępnianie i współpracę dla dowolnego użytkownika z dowolnym kontem](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-makes-sharing-and-collaboration-seamless-for-any-user/ba-p/325949).

### <a name="new-azure-ad-application-proxy-cookie-settings"></a>Nowe ustawienia pliku cookie usługi Azure serwer proxy aplikacji usługi Azure AD

**Wprowadź** Nowa funkcja  
**Kategoria usługi:** Serwer proxy aplikacji  
**Możliwość produktu:** Access Control

Wprowadziliśmy trzy nowe ustawienia plików cookie dostępne dla aplikacji publikowanych za poorednictwem serwera proxy aplikacji:

- **Używaj tylko protokołu HTTP.** Ustawia flagę **HTTPOnly** na serwerze proxy aplikacji i plikach cookie sesji. Włączenie tego ustawienia zapewnia dodatkowe korzyści z zabezpieczeń, takie jak uniemożliwienie kopiowania lub modyfikowania plików cookie za pomocą skryptów po stronie klienta. Zalecamy włączenie tej flagi (wybierz opcję **tak**), aby uzyskać dodatkowe korzyści.

- **Użyj bezpiecznego pliku cookie.** Ustawia **bezpieczną** flagę na serwerze proxy aplikacji i plikach cookie sesji. Włączenie tego ustawienia zapewnia dodatkowe korzyści z zabezpieczeń, zapewniając, że pliki cookie są przesyłane tylko za pośrednictwem bezpiecznych kanałów protokołu TLS, takich jak HTTPS. Zalecamy włączenie tej flagi (wybierz opcję **tak**), aby uzyskać dodatkowe korzyści.

- **Użyj trwałego pliku cookie.** Zapobiega utracie dostępu plików cookie po zamknięciu przeglądarki sieci Web. Te pliki cookie są ostatnie przez okres istnienia tokenu dostępu. Pliki cookie są jednak resetowane po osiągnięciu czasu wygaśnięcia lub ręcznym usunięciu pliku cookie przez użytkownika. Zalecamy zachowanie ustawienia domyślnego **nie**, włączając ustawienie dla starszych aplikacji, które nie udostępniają plików cookie między procesami.

Aby uzyskać więcej informacji na temat nowych plików cookie, zobacz [Ustawienia plików cookie dotyczące uzyskiwania dostępu do aplikacji lokalnych w Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-cookie-settings).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---january-2019"></a>Nowe aplikacje federacyjne dostępne w galerii aplikacji Azure AD — styczeń 2019

**Wprowadź** Nowa funkcja  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwość produktu:** Integracje innych firm
 
W styczniu 2019 dodaliśmy do galerii aplikacji 35 te nowe aplikacje z obsługą Federacji:

[Firstbird](https://docs.microsoft.com/azure/active-directory/saas-apps/firstbird-tutorial), [Folloze](https://docs.microsoft.com/azure/active-directory/saas-apps/folloze-tutorial), [paleta talent](https://docs.microsoft.com/azure/active-directory/saas-apps/talent-palette-tutorial), [infor CloudSuite](https://docs.microsoft.com/azure/active-directory/saas-apps/infor-cloud-suite-tutorial), [Cisco parasol](https://docs.microsoft.com/azure/active-directory/saas-apps/cisco-umbrella-tutorial), [rozwiązania Zscaler dostęp do Internetu](https://docs.microsoft.com/azure/active-directory/saas-apps/zscaler-internet-access-administrator-tutorial), [przypomnienia](https://docs.microsoft.com/azure/active-directory/saas-apps/expiration-reminder-tutorial)o wygaśnięciu, [przeglądarka InstaVR](https://docs.microsoft.com/azure/active-directory/saas-apps/instavr-viewer-tutorial), [CorpTax](https://docs.microsoft.com/azure/active-directory/saas-apps/corptax-tutorial), [ Verb](https://app.verb.net/login), [OpenLattice](https://openlattice.com/agora), [TheOrgWiki](https://www.theorgwiki.com/signup), [Pavaso Digital Close](https://docs.microsoft.com/azure/active-directory/saas-apps/pavaso-digital-close-tutorial), [GoodPractice Toolkit](https://docs.microsoft.com/azure/active-directory/saas-apps/goodpractice-toolkit-tutorial), [Cloud Service Picco](https://docs.microsoft.com/azure/active-directory/saas-apps/cloud-service-picco-tutorial), [AuditBoard](https://docs.microsoft.com/azure/active-directory/saas-apps/auditboard-tutorial), [iProva](https://docs.microsoft.com/azure/active-directory/saas-apps/iprova-tutorial), [](https://docs.microsoft.com/azure/active-directory/saas-apps/workable-tutorial)in, [CallPlease ](https://webapp.callplease.com/create-account/create-account.html) [GTNexus system rejestracji](https://docs.microsoft.com/azure/active-directory/saas-apps/gtnexus-sso-module-tutorial)jednokrotnej, [CBRE ServiceInsight](https://docs.microsoft.com/azure/active-directory/saas-apps/cbre-serviceinsight-tutorial), [Deskradar](https://docs.microsoft.com/azure/active-directory/saas-apps/deskradar-tutorial), [Coralogixv](https://docs.microsoft.com/azure/active-directory/saas-apps/coralogix-tutorial), [Signagelive](https://docs.microsoft.com/azure/active-directory/saas-apps/signagelive-tutorial), [arach dla przedsiębiorstw](https://docs.microsoft.com/azure/active-directory/saas-apps/ares-for-enterprise-tutorial), [K2 dla Office 365](https://www.k2.com/O365), [Xledger](https://www.xledger.net/), [iDiD Manager ](https://docs.microsoft.com/azure/active-directory/saas-apps/idid-manager-tutorial), [HighGear](https://docs.microsoft.com/azure/active-directory/saas-apps/highgear-tutorial), [wiedza](https://docs.microsoft.com/azure/active-directory/saas-apps/visitly-tutorial), [Alp prom](https://docs.microsoft.com/azure/active-directory/saas-apps/korn-ferry-alp-tutorial), [Acadia](https://docs.microsoft.com/azure/active-directory/saas-apps/acadia-tutorial), [Adoddle cSaas platform](https://docs.microsoft.com/azure/active-directory/saas-apps/adoddle-csaas-platform-tutorial)<!-- , [CaféX Portal (Meetings)](https://docs.microsoft.com/azure/active-directory/saas-apps/cafexportal-meetings-tutorial), [MazeMap Link](https://docs.microsoft.com/azure/active-directory/saas-apps/mazemaplink-tutorial)-->  

Aby uzyskać więcej informacji o aplikacjach, zobacz [integracji aplikacji SaaS w usłudze Azure Active Directory](https://aka.ms/appstutorial). Aby uzyskać więcej informacji o ofercie swoją aplikację w galerii aplikacji Azure AD, zobacz [umieść swoją aplikację w galerii aplikacji usługi Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="new-azure-ad-identity-protection-enhancements-public-preview"></a>Nowe ulepszenia usługi Azure AD Identity Protection (publicznego wersja zapoznawcza)

**Wprowadź** Zmieniona funkcja  
**Kategoria usługi:** Identity Protection  
**Możliwość produktu:** Ochrona i zabezpieczanie tożsamości

Przyjemnością się, że dodaliśmy następujące usprawnienia do oferty Azure AD Identity Protection publicznej wersji zapoznawczej, w tym:

- Zaktualizowany i bardziej zintegrowany interfejs użytkownika

- Dodatkowe interfejsy API

- Ulepszona Ocena ryzyka za poorednictwem uczenia maszynowego

- Wyrównywanie całego produktu wśród ryzykownych użytkowników i ryzykowne logowania

Aby uzyskać więcej informacji na temat ulepszeń, zobacz [co to jest Azure Active Directory Identity Protection (odświeżone)?](https://aka.ms/IdentityProtectionDocs) Aby dowiedzieć się więcej i podzielić się swoimi pomysłami, zapoznaj się z instrukcjami w ramach produktu.

---

### <a name="new-app-lock-feature-for-the-microsoft-authenticator-app-on-ios-and-android-devices"></a>Nowa funkcja blokady aplikacji dla aplikacji Microsoft Authenticator na urządzeniach z systemami iOS i Android

**Wprowadź** Nowa funkcja  
**Kategoria usługi:** Aplikacja Microsoft Authenticator  
**Możliwość produktu:** Ochrona i zabezpieczanie tożsamości

Aby zapewnić bezpieczeństwo jednorazowych kodów dostępu, informacji o aplikacji i ustawień aplikacji, możesz włączyć funkcję blokowania aplikacji w aplikacji Microsoft Authenticator. Włączenie blokady aplikacji oznacza, że użytkownik zostanie poproszony o uwierzytelnienie przy użyciu kodu PIN lub biometrycznego za każdym razem, gdy otworzysz aplikację Microsoft Authenticator.

Aby uzyskać więcej informacji, zobacz [często zadawane pytania dotyczące aplikacji Microsoft Authenticator](https://docs.microsoft.com/azure/active-directory/user-help/microsoft-authenticator-app-faq).

---

### <a name="enhanced-azure-ad-privileged-identity-management-pim-export-capabilities"></a>Rozszerzone funkcje eksportowania w usłudze Azure AD Privileged Identity Management (PIM)

**Wprowadź** Nowa funkcja  
**Kategoria usługi:** Privileged Identity Management  
**Możliwość produktu:** Privileged Identity Management

Administratorzy usługi Privileged Identity Management (PIM) mogą teraz wyeksportować wszystkie aktywne i kwalifikujące się przypisania ról dla określonego zasobu, w tym przypisania ról dla wszystkich zasobów podrzędnych. Wcześniej było trudne, aby administratorzy mogli uzyskać pełną listę przypisań ról dla subskrypcji i musiały wyeksportować przypisania ról dla każdego określonego zasobu.

Aby uzyskać więcej informacji, zobacz [Wyświetlanie historii aktywności i inspekcji dla ról zasobów platformy Azure w programie PIM](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/azure-pim-resource-rbac).

---

## <a name="novemberdecember-2018"></a>Listopad/grudzień 2018

### <a name="users-removed-from-synchronization-scope-no-longer-switch-to-cloud-only-accounts"></a>Użytkownicy usunięci z zakresu synchronizacji nie są już przełączani na konta tylko w chmurze

**Wprowadź** Ustalone  
**Kategoria usługi:** Zarządzanie użytkownikami  
**Możliwość produktu:** Katalog

>[!Important]
>W związku z tym nasłuchuje i rozumiemy frustrację. Z tego powodu przywrócono tę zmianę do momentu, w którym można ułatwić wdrożenie w organizacji.

Rozwiązano problem polegający na tym, że flaga DirSyncEnabled użytkownika powinna zostać błędnie przełączona na **Fałsz** , gdy obiekt Active Directory Domain Services (AD DS) został wykluczony z zakresu synchronizacji, a następnie przeniesiony do kosza w usłudze Azure AD w następujący sposób cykl synchronizacji. W wyniku tej poprawki Jeśli użytkownik zostanie wykluczony z zakresu synchronizacji, a następnie przywrócono z Kosza usługi Azure AD, konto użytkownika pozostanie jako zsynchronizowane z lokalnej usługi AD zgodnie z oczekiwaniami i nie będzie możliwe do zarządzania w chmurze, ponieważ jego źródło (SoA) pozostaje jako lokalna usługi AD.

Przed tą poprawką wystąpił problem podczas przełączania flagi DirSyncEnabled na wartość false. Nastąpiło niewłaściwe wrażenie, że te konta zostały przekonwertowane na obiekty tylko w chmurze, a konta mogą być zarządzane w chmurze. Jednak konta nadal przechowują swoje SoA jako lokalne i wszystkie zsynchronizowane właściwości (atrybuty cienia) pochodzące z lokalnej usługi AD. Ten stan powodował wiele problemów dotyczących usługi Azure AD i innych obciążeń w chmurze (na przykład usługi Exchange Online), które oczekują traktowania tych kont jako zsynchronizowanych z usługi AD, ale teraz zachowują się one jak konta tylko w chmurze.

W tym momencie jedynym sposobem na prawdziwą konwersję konta synchronizacji z usługi AD na konto tylko w chmurze jest wyłączenie narzędzia DirSync na poziomie dzierżawy, który wyzwala operację zaplecza do transferu SoA. Ten typ zmiany SoA wymaga (ale nie jest ograniczony do) czyszczenia wszystkich lokalnych atrybutów powiązanych (takich jak atrybuty LastDirSyncTime i Shadow) i wysyłania sygnału do innych obciążeń w chmurze, aby miało on odpowiedni obiekt przekonwertowany na konto tylko w chmurze .

W związku z tym ta poprawka uniemożliwia bezpośrednie aktualizacje atrybutu ImmutableID użytkownika synchronizowanego z usługi AD, co w niektórych scenariuszach w przeszłości było wymagane. Zgodnie z projektem, ImmutableID obiektu w usłudze Azure AD, jak nazwa oznacza, ma być niezmienny. Nowe funkcje zaimplementowane w Azure AD Connect Health i Azure AD Connect klienta synchronizacji są dostępne do rozwiązania takich scenariuszy:

- **Aktualizacja ImmutableID o dużej skali dla wielu użytkowników w podejściu przygotowanym**
  
  Na przykład należy wykonać długość AD DS migracji między lasami. Rozwiązanie: Użyj Azure AD Connect, aby **skonfigurować kotwicę źródłową** i, w miarę migrowania użytkownika, skopiować istniejące wartości ImmutableID z usługi Azure AD do AD DS lokalnego atrybutu "MS-ds-Consistency-GUID" w nowym lesie. Aby uzyskać więcej informacji, zobacz [Używanie MS-ds-ConsistencyGuid jako sourceAnchor](/azure/active-directory/hybrid/plan-connect-design-concepts#using-ms-ds-consistencyguid-as-sourceanchor).

- **Aktualizacje ImmutableID na dużą skalę dla wielu użytkowników w jednym zrzucie**

  Na przykład podczas wdrażania Azure AD Connect pomyłkę i teraz trzeba zmienić atrybut SourceAnchor. Rozwiązanie: Wyłącz narzędzie DirSync na poziomie dzierżawy i wyczyść wszystkie nieprawidłowe wartości ImmutableID. Aby uzyskać więcej informacji, zobacz Wyłączanie [synchronizacji katalogów dla pakietu Office 365](/office365/enterprise/turn-off-directory-synchronization).

- **Dopasowanie użytkownika lokalnego z istniejącym użytkownikiem w usłudze Azure AD** Na przykład użytkownik, który został utworzony w AD DS, generuje duplikat na koncie usługi Azure AD, a nie dopasowuje go przy użyciu istniejącego konta usługi Azure AD (obiektu oddzielonego). Rozwiązanie: Użyj Azure AD Connect Health w Azure Portal, aby ponownie zmapować kotwicę źródłową/ImmutableID. Aby uzyskać więcej informacji, zobacz [scenariusz obiektu](/azure/active-directory/hybrid/how-to-connect-health-diagnose-sync-errors#orphaned-object-scenario)oddzielonego.

### <a name="breaking-change-updates-to-the-audit-and-sign-in-logs-schema-through-azure-monitor"></a>Nieprzerwana zmiana: Aktualizacje schematu inspekcji i rejestrowania dzienników za pomocą Azure Monitor

**Wprowadź** Zmieniona funkcja  
**Kategoria usługi:** Raportowanie  
**Możliwość produktu:** Monitorowanie i raportowanie

Obecnie publikujemy strumienie inspekcji i logowania za pomocą Azure Monitor, dzięki czemu możesz bezproblemowo zintegrować pliki dziennika z narzędziami SIEM lub z Log Analytics. Na podstawie opinii i przygotowania do ogólnej dostępności tej funkcji wprowadzamy następujące zmiany w naszym schemacie. Te zmiany schematu i powiązane z nimi aktualizacje dokumentacji będą wykonywane przez pierwszy tydzień stycznia.

#### <a name="new-fields-in-the-audit-schema"></a>Nowe pola w schemacie inspekcji
Dodajemy nowe pole **typu operacji** , aby zapewnić typ operacji wykonanej na zasobie. Na przykład **Dodaj**, **zaktualizuj**lub **Usuń**.

#### <a name="changed-fields-in-the-audit-schema"></a>Zmieniono pola w schemacie inspekcji
Następujące pola są zmieniane w schemacie inspekcji:

|Nazwa pola|Co zostało zmienione|Stare wartości|Nowe wartości|
|----------|------------|----------|----------|
|Kategoria|To jest pole **nazwa usługi** . Jest teraz polem **kategorie inspekcji** . **Nazwa usługi** została zmieniona na pole **loggedByService** .|<ul><li>Aprowizacja kont</li><li>Katalog podstawowy</li><li>Samoobsługowe resetowanie hasła</li></ul>|<ul><li>Zarządzanie użytkownikami</li><li>Zarządzanie grupami</li><li>Zarządzanie aplikacjami</li></ul>|
|targetResources|Obejmuje **TargetResourceType** na najwyższego poziomu.|&nbsp;|<ul><li>Zasady</li><li>Aplikacja</li><li>Użytkownik</li><li>Grupa</li></ul>|
|loggedByService|Zawiera nazwę usługi, która wygenerowała dziennik inspekcji.|Null|<ul><li>Aprowizacja kont</li><li>Katalog podstawowy</li><li>Samoobsługowe resetowanie hasła</li></ul>|
|Wynik|Zawiera wyniki dzienników inspekcji. Wcześniej było to wyliczone, ale teraz pokazywane są rzeczywiste wartości.|<ul><li>0</li><li>1</li></ul>|<ul><li>Powodzenie</li><li>Niepowodzenie</li></ul>|

#### <a name="changed-fields-in-the-sign-in-schema"></a>Zmieniono pola w schemacie logowania
Następujące pola są zmieniane w schemacie logowania:

|Nazwa pola|Co zostało zmienione|Stare wartości|Nowe wartości|
|----------|------------|----------|----------|
|appliedConditionalAccessPolicies|To było pole **conditionalaccessPolicies** . To teraz pole **appliedConditionalAccessPolicies** .|Nie wprowadzono zmian|Nie wprowadzono zmian|
|conditionalAccessStatus|Zapewnia wynik stanu zasad dostępu warunkowego podczas logowania. Wcześniej było to wyliczone, ale teraz pokazywane są rzeczywiste wartości.|<ul><li>0</li><li>1</li><li>2</li><li>3</li></ul>|<ul><li>Powodzenie</li><li>Niepowodzenie</li><li>Nie zastosowano</li><li>Wyłączone</li></ul>|
|appliedConditionalAccessPolicies: wynik|Zapewnia wynik poszczególnych stanów zasad dostępu warunkowego podczas logowania. Wcześniej było to wyliczone, ale teraz pokazywane są rzeczywiste wartości.|<ul><li>0</li><li>1</li><li>2</li><li>3</li></ul>|<ul><li>Powodzenie</li><li>Niepowodzenie</li><li>Nie zastosowano</li><li>Wyłączone</li></ul>|

Aby uzyskać więcej informacji na temat schematu, zobacz [interpretowanie schematu dzienników inspekcji usługi Azure AD w Azure monitor (wersja zapoznawcza)](https://docs.microsoft.com/azure/active-directory/reports-monitoring/reference-azure-monitor-audit-log-schema)

---

### <a name="identity-protection-improvements-to-the-supervised-machine-learning-model-and-the-risk-score-engine"></a>Ulepszenia usługi Identity Protection dotyczące nadzorowanego modelu uczenia maszynowego i aparatu oceny ryzyka

**Wprowadź** Zmieniona funkcja  
**Kategoria usługi:** Identity Protection  
**Możliwość produktu:** Oceny ryzyka

Ulepszenia aparatu do oceny ryzyka związanego z ochroną tożsamości oraz możliwości logowania mogą pomóc w zwiększeniu dokładności i zakresu ryzyka dla użytkowników. Administratorzy mogą zauważyć, że poziom ryzyka użytkownika nie jest już bezpośrednio połączony z poziomem ryzyka specyficznych wykryć i że występuje wzrost liczby i poziomu ryzykownych zdarzeń związanych z logowaniem.

Wykrywanie ryzyka jest teraz oceniane przez nadzorowany model uczenia maszynowego, który oblicza ryzyko użytkownika przy użyciu dodatkowych funkcji logowania użytkownika i wzorca wykrywania. W oparciu o ten model administrator może znaleźć użytkowników z wynikami wysokiego ryzyka, nawet jeśli wykrycia związane z tym użytkownikiem mają niski lub średni ryzyko. 

---

### <a name="administrators-can-reset-their-own-password-using-the-microsoft-authenticator-app-public-preview"></a>Administratorzy mogą resetować swoje hasła przy użyciu aplikacji Microsoft Authenticator (publiczna wersja zapoznawcza)

**Wprowadź** Zmieniona funkcja  
**Kategoria usługi:** Samodzielne resetowanie hasła  
**Możliwość produktu:** Uwierzytelnianie użytkownika

Administratorzy usługi Azure AD mogą teraz resetować swoje hasła przy użyciu powiadomień aplikacji Microsoft Authenticator lub kodu z dowolnej aplikacji lub tokenu sprzętowego usług uwierzytelniania mobilnego. Aby zresetować własne hasło, Administratorzy mogą teraz korzystać z dwóch następujących metod:

- Powiadomienie Microsoft Authenticator aplikacji

- Inna aplikacja/token sprzętowy do uwierzytelniania mobilnego

- Email

- Połączenie telefoniczne

- Wiadomość SMS

Aby uzyskać więcej informacji na temat używania aplikacji Microsoft Authenticator do resetowania haseł, zobacz Samoobsługowe resetowanie haseł w [usłudze Azure AD — aplikacja mobilna i SSPR (wersja zapoznawcza)](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-howitworks#mobile-app-and-sspr-preview)

---

### <a name="new-azure-ad-cloud-device-administrator-role-public-preview"></a>Nowa rola Administrator urządzeń w chmurze w usłudze Azure AD (publiczna wersja zapoznawcza)

**Wprowadź** Nowa funkcja  
**Kategoria usługi:** Rejestracja urządzeń i zarządzanie nimi  
**Możliwość produktu:** Kontrola dostępu

Administratorzy mogą przypisywać użytkowników do nowej roli administratora urządzenia w chmurze w celu wykonywania zadań administratora urządzenia w chmurze. Użytkownicy z przypisaną rolą Administratorzy urządzeń w chmurze mogą włączać, wyłączać i usuwać urządzenia w usłudze Azure AD oraz mogą odczytywać klucze funkcji BitLocker systemu Windows 10 (jeśli istnieją) w Azure Portal.

Aby uzyskać więcej informacji o rolach i uprawnieniach, zobacz [Przypisywanie ról administratorów w Azure Active Directory](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)

---

### <a name="manage-your-devices-using-the-new-activity-timestamp-in-azure-ad-public-preview"></a>Zarządzanie urządzeniami przy użyciu nowego znacznika czasu aktywności w usłudze Azure AD (publiczna wersja zapoznawcza)

**Wprowadź** Nowa funkcja  
**Kategoria usługi:** Rejestracja urządzeń i zarządzanie nimi  
**Możliwość produktu:** Zarządzanie cyklem życia urządzenia

Zdajemy sobie sprawę, że w miarę upływu czasu musisz odświeżyć i wycofać urządzenia organizacji w usłudze Azure AD, aby uniknąć używania starych urządzeń w środowisku. Aby pomóc w tym procesie, usługa Azure AD teraz aktualizuje urządzenia przy użyciu nowej sygnatury czasowej działania, co ułatwia zarządzanie cyklem życia urządzenia.

Aby uzyskać więcej informacji na temat uzyskiwania i używania tej sygnatury czasowej, zobacz [How to: Zarządzanie nieodświeżonymi urządzeniami w usłudze Azure AD](https://docs.microsoft.com/azure/active-directory/devices/manage-stale-devices)

---

### <a name="administrators-can-require-users-to-accept-a-terms-of-use-on-each-device"></a>Administratorzy mogą wymagać od użytkowników zaakceptowania warunków użytkowania na poszczególnych urządzeniach

**Wprowadź** Nowa funkcja  
**Kategoria usługi:** Warunki użytkowania  
**Możliwość produktu:** Ład
 
Administratorzy mogą teraz włączyć opcję **Wymagaj od użytkowników zgody na każde urządzenie** , aby wymagać od użytkowników akceptacji warunków użytkowania na każdym urządzeniu, na którym są używane w dzierżawie.

Aby uzyskać więcej informacji, zobacz [sekcję warunki użytkowania poszczególnych urządzeń w funkcji Azure Active Directory warunki użytkowania](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use#per-device-terms-of-use).

---

### <a name="administrators-can-configure-a-terms-of-use-to-expire-based-on-a-recurring-schedule"></a>Administratorzy mogą skonfigurować warunki użytkowania, aby wygasnąć na podstawie harmonogramu cyklicznego

**Wprowadź** Nowa funkcja  
**Kategoria usługi:** Warunki użytkowania  
**Możliwość produktu:** Ład
 

Administratorzy mogą teraz włączyć opcję **wygasania** , aby wygaśnie warunki użytkowania dla wszystkich użytkowników na podstawie określonego harmonogramu cyklicznego. Harmonogram może być roczny, dwuroczny, kwartalny lub miesięczny. Po wygaśnięciu warunków użytkowania użytkownicy muszą ponownie zaakceptować.

Aby uzyskać więcej informacji, zobacz [sekcję Dodawanie warunków użytkowania w artykule Azure Active Directory warunki użytkowania](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use#add-terms-of-use).

---

### <a name="administrators-can-configure-a-terms-of-use-to-expire-based-on-each-users-schedule"></a>Administratorzy mogą skonfigurować warunki użytkowania, aby wygasnąć na podstawie harmonogramu każdego użytkownika

**Wprowadź** Nowa funkcja  
**Kategoria usługi:** Warunki użytkowania  
**Możliwość produktu:** Ład

Administratorzy mogą teraz określić czas trwania, przez który użytkownik musi ponownie zaakceptować warunki użytkowania. Administratorzy mogą na przykład określić, że użytkownicy muszą ponownie akceptować warunki użytkowania co 90 dni.

Aby uzyskać więcej informacji, zobacz [sekcję Dodawanie warunków użytkowania w artykule Azure Active Directory warunki użytkowania](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use#add-terms-of-use).
 
---

### <a name="new-azure-ad-privileged-identity-management-pim-emails-for-azure-active-directory-roles"></a>Nowe wiadomości e-mail usługi Azure AD Privileged Identity Management (PIM) dla ról usługi Azure Active Directory

**Wprowadź** Nowa funkcja  
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

**Wprowadź** Zmieniona funkcja  
**Kategoria usługi:** Inne  
**Możliwość produktu:** Katalog

Licencjonowanie oparte na grupach jest w publicznej wersji zapoznawczej i jest teraz ogólnie dostępne. W ramach tej ogólnej wersji ta funkcja jest bardziej skalowalna i dodaliśmy możliwość przetworzenia przypisań licencjonowania opartego na grupach dla jednego użytkownika i możliwość korzystania z licencjonowania opartego na grupach z licencjami pakietu Office 365 E3/a3.

Aby uzyskać więcej informacji na temat licencjonowania opartego na grupach, zobacz [co to jest Licencjonowanie oparte na grupach w Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-licensing-whatis-azure-portal)

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---november-2018"></a>Nowe aplikacje federacyjne dostępne w galerii aplikacji usługi Azure AD — listopad 2018

**Wprowadź** Nowa funkcja  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwość produktu:** Integracje innych firm
 
W listopadzie 2018 dodaliśmy następujące 26 nowych aplikacji z obsługą Federacji do galerii aplikacji:

[CoreStack](https://cloud.corestack.io/site/login), [HubSpot](https://docs.microsoft.com/azure/active-directory/saas-apps/HubSpot-tutorial), [](https://docs.microsoft.com/azure/active-directory/saas-apps/getthere-tutorial)Getin, [gra-PE](https://docs.microsoft.com/azure/active-directory/saas-apps/grape-tutorial), [eHour](https://getehour.com/try-now), [Consent2Go](https://docs.microsoft.com/azure/active-directory/saas-apps/Consent2Go-tutorial), [Appinux](https://docs.microsoft.com/azure/active-directory/saas-apps/appinux-tutorial), [DriveDollar](https://azuremarketplace.microsoft.com/marketplace/apps/savitas.drivedollar-azuread?tab=Overview), [Useall](https://docs.microsoft.com/azure/active-directory/saas-apps/useall-tutorial), [nieograniczone kampus](https://docs.microsoft.com/azure/active-directory/saas-apps/infinitecampus-tutorial), [Alaya](https://alayagood.com/en/demo/), [HeyBuddy](https://docs.microsoft.com/azure/active-directory/saas-apps/heybuddy-tutorial), [ Wrike SAML](https://docs.microsoft.com/azure/active-directory/saas-apps/wrike-tutorial), [dryf](https://docs.microsoft.com/azure/active-directory/saas-apps/drift-tutorial), [Zenegy for Business Central 365](https://accounting.zenegy.com/), [EverBridge Portal członków](https://docs.microsoft.com/azure/active-directory/saas-apps/everbridge-tutorial), [ideo](https://profile.ideo.com/users/sign_up), [Ivanti Service Manager (ISM)](https://docs.microsoft.com/azure/active-directory/saas-apps/ivanti-service-manager-tutorial), [Peakon](https://docs.microsoft.com/azure/active-directory/saas-apps/peakon-tutorial), [Allbound SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/allbound-sso-tutorial), [aplikacje typu plex — test klasyczny ](https://test.plexonline.com/signon), [Aplikacje typu plex — klasyczne](https://www.plexonline.com/signon), [aplikacje typu plex — test środowiska](https://test.cloud.plex.com/sso)użytkownika, [aplikacje obiektów typu plex — UX](https://cloud.plex.com/sso), [aplikacje typu plex — IAM](https://accounts.plex.com/), łodzie, [zapisy, frekwencja, & finansowy system śledzenia](https://getcrafts.ca/craftsregistration) 

Aby uzyskać więcej informacji o aplikacjach, zobacz [integracji aplikacji SaaS w usłudze Azure Active Directory](https://aka.ms/appstutorial). Aby uzyskać więcej informacji o ofercie swoją aplikację w galerii aplikacji Azure AD, zobacz [umieść swoją aplikację w galerii aplikacji usługi Azure Active Directory](https://aka.ms/azureadapprequest).

---

## <a name="october-2018"></a>Październik 2018 r.

### <a name="azure-ad-logs-now-work-with-azure-log-analytics-public-preview"></a>Dzienniki usługi Azure AD obecnie pracować z usługą Azure Log Analytics (publiczna wersja zapoznawcza)

**Wprowadź** Nowa funkcja  
**Kategoria usługi:** Raportowanie  
**Możliwość produktu:** Monitorowanie i raportowanie

Cieszymy się, mogąc mogą obecnie przekazywać dzienników usługi Azure AD do usługi Azure Log Analytics! Najczęściej żądanych pomaga umożliwiają jeszcze lepsze dostęp do analizy dla Twojej firmy, operacji i zabezpieczeń, a także sposób, aby wspomóc monitorowanie infrastruktury. Aby uzyskać więcej informacji, zobacz [działanie usługi Azure Active Directory rejestruje się w usłudze Azure Log Analytics jest teraz dostępna](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-Active-Directory-Activity-logs-in-Azure-Log-Analytics-now/ba-p/274843) blogu.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---october-2018"></a>Nowe aplikacje federacyjne dostępne w galerii aplikacji usługi Azure AD — października 2018 r.

**Wprowadź** Nowa funkcja  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwość produktu:** Integracje innych firm

Na października 2018 Dodaliśmy obsługę 14 nowych aplikacjach z Federacją w galerii aplikacji:

[Moje punkty Award](https://docs.microsoft.com/azure/active-directory/saas-apps/myawardpoints-tutorial), [Vibe HCM](https://docs.microsoft.com/azure/active-directory/saas-apps/vibehcm-tutorial), ambyint, [MyWorkDrive](https://docs.microsoft.com/azure/active-directory/saas-apps/myworkdrive-tutorial), [BorrowBox](https://docs.microsoft.com/azure/active-directory/saas-apps/borrowbox-tutorial), Dialpad, [środowiska wirtualnego ON24](https://docs.microsoft.com/azure/active-directory/saas-apps/on24-tutorial), [RingCentral](https://docs.microsoft.com/azure/active-directory/saas-apps/ringcentral-tutorial), [rozwiązania Zscaler trzech](https://docs.microsoft.com/azure/active-directory/saas-apps/zscaler-three-tutorial), [Phraseanet](https://docs.microsoft.com/azure/active-directory/saas-apps/phraseanet-tutorial), [Appraisd](https://docs.microsoft.com/azure/active-directory/saas-apps/appraisd-tutorial), [kontroli Workspot](https://docs.microsoft.com/azure/active-directory/saas-apps/workspotcontrol-tutorial), [Shuccho Navi](https://docs.microsoft.com/azure/active-directory/saas-apps/shucchonavi-tutorial), [Glassfrog](https://docs.microsoft.com/azure/active-directory/saas-apps/glassfrog-tutorial)

Aby uzyskać więcej informacji o aplikacjach, zobacz [integracji aplikacji SaaS w usłudze Azure Active Directory](https://aka.ms/appstutorial). Aby uzyskać więcej informacji o ofercie swoją aplikację w galerii aplikacji Azure AD, zobacz [umieść swoją aplikację w galerii aplikacji usługi Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="azure-ad-domain-services-email-notifications"></a>Powiadomienia E-mail usług domenowych Azure AD

**Wprowadź** Nowa funkcja  
**Kategoria usługi:** Azure AD Domain Services  
**Możliwość produktu:** Azure AD Domain Services

Azure AD Domain Services udostępnia alerty w portalu Azure dotyczące błędów konfiguracji lub problemów z Twoją domeną zarządzaną. Te alerty zawierają przewodniki krok po kroku, dzięki czemu można spróbować rozwiązać problemy bez konieczności kontaktowania się z pomocą techniczną.

Począwszy od października, będzie można dostosować ustawienia powiadomień dla domeny zarządzanej, gdy nowe alerty występują, do wyznaczonej grupy osób, eliminując konieczność stale Sprawdź portal aktualizacji zostanie wysłana wiadomość e-mail.

Aby uzyskać więcej informacji, zobacz [ustawienia powiadomień w usługach domenowych Azure AD](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-notifications).

---

### <a name="azure-ad-portal-supports-using-the-forcedelete-domain-api-to-delete-custom-domains"></a>Platforma Azure obsługuje portalu AD przy użyciu domeny ForceDelete interfejsu API, można usunąć domeny niestandardowej 

**Wprowadź** Zmieniona funkcja  
**Kategoria usługi:** Zarządzanie katalogiem  
**Możliwość produktu:** Katalog

Mamy przyjemność ogłosić, że teraz można użyć domeny ForceDelete interfejsu API można usunąć nazwy domeny niestandardowej, asynchronicznie zmieniając nazwę odwołania, takich jak użytkownicy, grupy i aplikacje z niestandardowej nazwy domeny (contoso.com) do (nazwa domeny początkowej domyślnej contoso.onmicrosoft.com).

Ta zmiana pomaga szybciej usunąć nazwy domeny niestandardowej, czy Twoja organizacja już używa nazwy, czy należy użyć nazwy domeny za pomocą innej usługi Azure AD.

Aby uzyskać więcej informacji, zobacz [usuwanie nazwy domeny niestandardowej](https://docs.microsoft.com/azure/active-directory/users-groups-roles/domains-manage#delete-a-custom-domain-name).

---

## <a name="september-2018"></a>Wrzesień 2018 r.
 
### <a name="updated-administrator-role-permissions-for-dynamic-groups"></a>Uprawnienia roli administrator zaktualizowane grup dynamicznych

**Wprowadź** Ustalone  
**Kategoria usługi:** Zarządzanie grupami  
**Możliwość produktu:** Współpraca

Rozwiązaliśmy problem, więc teraz tworzyć i aktualizować reguły członkostwa dynamicznego, bez potrzeby bycia właściciela grupy ról określonego administratora.

Dostępne są następujące role:

- Administrator globalny

- Administrator usługi Intune

- Administrator użytkownika

Aby uzyskać więcej informacji, zobacz [utworzyć grupę dynamiczną i sprawdzić stan](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-create-rule)

---

### <a name="simplified-single-sign-on-sso-configuration-settings-for-some-third-party-apps"></a>Uproszczone ustawienia konfiguracji pojedynczego logowania jednokrotnego (SSO) w przypadku niektórych aplikacji innych firm

**Wprowadź** Nowa funkcja  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwość produktu:** SSO

Zdajemy sobie sprawę, że ustawienia zapasowej pojedynczego logowania jednokrotnego (SSO) dla oprogramowania jako usługi (SaaS) aplikacji może być trudne, ze względu na unikatowy charakter każdej konfiguracji aplikacji. Mamy skompilowane środowisko uproszczona konfiguracja, aby automatycznie wypełnić ustawień konfiguracji logowania jednokrotnego dla następujących aplikacji SaaS innych firm:

- Zendesk

- Komponent ArcGis Online

- Narzędzie Jamf Pro

Aby rozpocząć korzystanie z tego środowiska jednym kliknięciem, przejdź do **witryny Azure portal** > **konfiguracji logowania jednokrotnego** strony aplikacji. Aby uzyskać więcej informacji, zobacz [integracji aplikacji SaaS w usłudze Azure Active Directory](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list)

---

### <a name="azure-active-directory---where-is-your-data-located-page"></a>Usługa Azure Active Directory — Twoje dane lokalizacji? Strona

**Wprowadź** Nowa funkcja  
**Kategoria usługi:** Inne  
**Możliwość produktu:** GoLocal

Wybierz region, firmy z **usługi Azure Active Directory — Twoje dane lokalizacji** strony do widoku, w którym Centrum danych Azure przechowuje dane usługi Azure AD w spoczynku dla wszystkich usług Azure AD. Informacje można filtrować według określonej usługi Azure AD dla regionu w firmie.

Aby skorzystać z tej funkcji i uzyskać więcej informacji, zobacz [usługi Azure Active Directory — Twoje dane lokalizacji](https://aka.ms/AADDataMap).

---

### <a name="new-deployment-plan-available-for-the-my-apps-access-panel"></a>Nowy plan wdrażania dostępne do panelu dostępu usługi Moje aplikacje

**Wprowadź** Nowa funkcja  
**Kategoria usługi:** Moje aplikacje  
**Możliwość produktu:** SSO

Sprawdź nowy plan wdrożenia, dostępnej w panelu Moje aplikacjom dostępu (https://aka.ms/deploymentplans).
Panel Moje dostępu aplikacji zapewnia użytkownikom z jednego miejsca można znaleźć i uzyskać dostęp do swoich aplikacji. Ten portal zapewnia również użytkownikom możliwości samoobsługi, takie jak żądania dostępu do aplikacji i grup oraz zarządzanie dostępem do tych zasobów w imieniu innych użytkowników.

Aby uzyskać więcej informacji, zobacz [co to jest portal Moje aplikacje?](https://docs.microsoft.com/azure/active-directory/user-help/active-directory-saas-access-panel-introduction)

---

### <a name="new-troubleshooting-and-support-tab-on-the-sign-ins-logs-page-of-the-azure-portal"></a>Karta nowe Rozwiązywanie problemów i pomocy technicznej na stronie dzienniki operacji logowania w witrynie Azure Portal

**Wprowadź** Nowa funkcja  
**Kategoria usługi:** Raportowanie  
**Możliwość produktu:** Monitorowanie i raportowanie

Nowy **Rozwiązywanie problemów i pomoc techniczna** karcie **logowania** strony Azure portal, mają na celu ułatwienie administratorom i pracowników działu pomocy technicznej, rozwiązywania problemów związanych z logowania usługi Azure AD. Ta nowa karta zawiera kod błędu, komunikat o błędzie i zaleceniami związanymi z korygowaniem (jeśli istnieje) Aby rozwiązać ten problem. Jeśli nie uda się rozwiązać ten problem, również udostępniamy nowy sposób tworzenia biletu pomocy technicznej przy użyciu **Kopiuj do Schowka** środowisko, które wypełnia **identyfikator żądania** i **Data (UTC)** pól pliku dziennika w biletu pomocy technicznej.  

![Dzienniki logowania pokazujące nową kartę](media/whats-new/troubleshooting-and-support.png)

---

### <a name="enhanced-support-for-custom-extension-properties-used-to-create-dynamic-membership-rules"></a>Rozszerzona obsługa właściwości niestandardowe rozszerzenie użyte do utworzenia reguły członkostwa dynamicznego

**Wprowadź** Zmieniona funkcja  
**Kategoria usługi:** Zarządzanie grupami  
**Możliwość produktu:** Współpraca

Dzięki tej aktualizacji można teraz kliknąć **pobieranie właściwości niestandardowego rozszerzenia** link z konstruktora reguły grupy dynamiczne użytkownika, wprowadź swój identyfikator aplikacji unikatowymi i otrzymywać pełną listę właściwości niestandardowego rozszerzenia do użycia podczas tworzenia dynamiczny reguły członkostwa dla użytkowników. Tej listy można odświeżać w taki sposób, aby uzyskać nowe właściwości rozszerzenia niestandardowego dla danej aplikacji.

Aby uzyskać więcej informacji o używaniu właściwości rozszerzenia niestandardowe reguły członkostwa dynamicznego, zobacz [rozszerzenia i właściwości niestandardowego rozszerzenia](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership#extension-properties-and-custom-extension-properties)

---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Nowe zatwierdzone aplikacje klienckie dla dostępu warunkowego opartego na aplikacji usługi Azure AD

**Wprowadź** Plan zmiany  
**Kategoria usługi:** Dostęp warunkowy  
**Możliwość produktu:** Bezpieczeństwo i Ochrona tożsamości

Następujące aplikacje są na liście [zatwierdzonych aplikacji klienckich](https://docs.microsoft.com/azure/active-directory/conditional-access/technical-reference#approved-client-app-requirement):

- Microsoft To-Do

- Microsoft Stream

Aby uzyskać więcej informacji, zobacz:

- [Dostęp warunkowy oparty na aplikacji usługi Azure AD](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)

---

### <a name="new-support-for-self-service-password-reset-from-the-windows-7881-lock-screen"></a>Nowa funkcja obsługi samoobsługowego resetowania hasła na ekranie blokady 7/8/8.1 Windows

**Wprowadź** Nowa funkcja  
**Kategoria usługi:** SSPR  
**Możliwość produktu:** Uwierzytelnianie użytkownika

Po skonfigurowaniu tej nowej funkcji użytkownikom zostanie wyświetlony link do zresetowania swojego hasła z **blokady** ekranu urządzenia z systemem Windows 7, Windows 8 lub Windows 8.1. Klikając ten link, użytkownik jest przeprowadzany przez sam przepływ resetowania hasła jako za pośrednictwem przeglądarki sieci web.

Aby uzyskać więcej informacji, zobacz [jak włączyć funkcję resetowania z Windows 7, 8 i 8.1 hasła](https://aka.ms/ssprforwindows78)

---

### <a name="change-notice-authorization-codes-will-no-longer-be-available-for-reuse"></a>Zmień powiadomienie: Kody autoryzacji nie będą już dostępne do ponownego użycia 

**Wprowadź** Plan zmiany  
**Kategoria usługi:** Uwierzytelnienia (logowania)  
**Możliwość produktu:** Uwierzytelnianie użytkownika

15 listopada 2018 r. usługi Azure AD będzie akceptować kody poprzednio używanych uwierzytelniania dla aplikacji. Ta zmiana zabezpieczeń pomaga do usługi Azure AD zgodnie ze specyfikacją protokołu OAuth i będzie wymuszany w punktach końcowych v1 i v2.

Jeśli aplikacja używa kody autoryzacji uzyskiwanie tokenów dla wielu zasobów, zalecamy Użyj kodu, aby uzyskać token odświeżania, a następnie używać tego tokenu odświeżania w celu pobrania dodatkowe tokeny dla innych zasobów. Kody autoryzacji należy używać tylko raz, ale tokenów odświeżania mogą być wielokrotnie używane w wielu zasobach. Aplikacja, która podejmuje próbę ponownego użycia kodu uwierzytelniania podczas przepływu kodu OAuth otrzymają komunikat o błędzie invalid_grant.

Ta i inne zmiany dotyczące protokołów, zobacz [pełną listę Nowości dla uwierzytelniania](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---september-2018"></a>Nowe aplikacje federacyjne dostępne w galerii aplikacji usługi Azure AD — września 2018 r.

**Wprowadź** Nowa funkcja  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwość produktu:** Integracje innych firm
 
W września 2018 Dodaliśmy obsługę 16 nowych aplikacjach z Federacją w galerii aplikacji:

[Uberflip](https://docs.microsoft.com/azure/active-directory/saas-apps/uberflip-tutorial), [Comeet rekrutacji oprogramowania](https://docs.microsoft.com/azure/active-directory/saas-apps/comeetrecruitingsoftware-tutorial), [Workteam](https://docs.microsoft.com/azure/active-directory/saas-apps/workteam-tutorial), [ArcGIS Enterprise](https://docs.microsoft.com/azure/active-directory/saas-apps/arcgisenterprise-tutorial), [Nuclino](https://docs.microsoft.com/azure/active-directory/saas-apps/nuclino-tutorial), [ Chmura JDA](https://docs.microsoft.com/azure/active-directory/saas-apps/jdacloud-tutorial), [Snowflake](https://docs.microsoft.com/azure/active-directory/saas-apps/snowflake-tutorial), NavigoCloud, [Figma](https://docs.microsoft.com/azure/active-directory/saas-apps/figma-tutorial), join.me, [ZephyrSSO](https://docs.microsoft.com/azure/active-directory/saas-apps/zephyrsso-tutorial), [Silverback](https://docs.microsoft.com/azure/active-directory/saas-apps/silverback-tutorial), Riverbed Xirrus EasyPass, [logowania jednokrotnego Rackspace](https://docs.microsoft.com/azure/active-directory/saas-apps/rackspacesso-tutorial), Enlyft Usługa rejestracji Jednokrotnej dla platformy Azure, usługę SurveyMonkey na [Convene](https://docs.microsoft.com/azure/active-directory/saas-apps/convene-tutorial), [dmarcian](https://docs.microsoft.com/azure/active-directory/saas-apps/dmarcian-tutorial)

Aby uzyskać więcej informacji o aplikacjach, zobacz [integracji aplikacji SaaS w usłudze Azure Active Directory](https://aka.ms/appstutorial). Aby uzyskać więcej informacji o ofercie swoją aplikację w galerii aplikacji Azure AD, zobacz [umieść swoją aplikację w galerii aplikacji usługi Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="support-for-additional-claims-transformations-methods"></a>Obsługa dodatkowych oświadczeń przekształcenia metod

**Wprowadź** Nowa funkcja  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwość produktu:** SSO

Dodaliśmy nowe metody przekształcania oświadczeń ToLower() i ToUpper(), które mogą być stosowane na tokeny SAML z opartej na SAML **konfiguracji rejestracji jednokrotnej** strony.

Aby uzyskać więcej informacji, zobacz [Dostosowywanie oświadczeń wystawionych w tokenie języka SAML dla aplikacji dla przedsiębiorstw w usłudze Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization)

---

### <a name="updated-saml-based-app-configuration-ui-preview"></a>Zaktualizowano konfigurację aplikacji opartej na SAML interfejsu użytkownika (wersja zapoznawcza)

**Wprowadź** Zmieniona funkcja  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwość produktu:** SSO

W ramach naszego interfejsu użytkownika konfiguracji zaktualizowanej aplikacji opartej na SAML uzyskasz:

- Środowisko zaktualizowano wskazówki dotyczące konfigurowania aplikacji opartej na SAML.

- Widoczność o nowościach brakujące lub nieprawidłowe w konfiguracji.

- Możliwość dodawania wielu adresów e-mail, aby uzyskać powiadomienie o wygaśnięciu certyfikatu.

- Nowe metody przekształcania oświadczeń, ToLower() i ToUpper() i inne.

- Sposób przekazać własny token podpisywania certyfikatu dla aplikacji przedsiębiorstwa.

- Sposób, aby ustawić Format identyfikatora NameID dla aplikacji SAML i sposób można ustawić wartości identyfikatora NameID jako rozszerzenia katalogów.

Aby włączyć ten zaktualizowany widok, kliknij przycisk **wypróbować nasze nowe środowisko** link u góry **logowania jednokrotnego** strony. Aby uzyskać więcej informacji, zobacz [Samouczek: Konfigurowanie logowania jednokrotnego opartego na protokole SAML dla aplikacji](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-single-sign-on-portal)przy użyciu Azure Active Directory.

---

## <a name="august-2018"></a>Sierpień 2018 r.

### <a name="changes-to-azure-active-directory-ip-address-ranges"></a>Zmiany do zakresów adresów adresów IP platformy Azure Active Directory

**Wprowadź** Plan zmiany  
**Kategoria usługi:** Inne  
**Możliwość produktu:** Platforma

Wprowadzamy większych zakresów adresów IP do usługi Azure AD, co oznacza, że jeśli skonfigurowano zakresów adresów IP usługi AD platformy Azure dla zapory, routery lub grupy zabezpieczeń sieci, należy je zaktualizować. Wprowadzamy aktualizację, dzięki czemu nie trzeba ponownie zmienić zapory, router lub konfiguracje zakresu adresów IP z grup zabezpieczeń sieci, gdy usługa Azure AD dodaje nowe punkty końcowe. 

Ruch sieciowy przechodzi do tych nowych zakresów w ciągu następnych dwóch miesięcy. Aby kontynuować nieprzerwane działanie usług, należy dodać te zaktualizowane wartości do adresów IP przed 10 września 2018 r.:

- 20.190.128.0/18 

- 40.126.0.0/18 

Zdecydowanie zaleca się nie usuwa starych zakresów adresów IP, dopóki wszystkie ruchu sieciowego została przeniesiona do nowego zakresu. Aktualizacje dotyczące przenoszenia i dowiedzieć się, usunięcie starych zakresów, zobacz [URL usługi Office 365 i zakresy adresów IP](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2).

---

### <a name="change-notice-authorization-codes-will-no-longer-be-available-for-reuse"></a>Zmień powiadomienie: Kody autoryzacji nie będą już dostępne do ponownego użycia 

**Wprowadź** Plan zmiany  
**Kategoria usługi:** Uwierzytelnienia (logowania)  
**Możliwość produktu:** Uwierzytelnianie użytkownika

15 listopada 2018 r. usługi Azure AD będzie akceptować kody poprzednio używanych uwierzytelniania dla aplikacji. Ta zmiana zabezpieczeń pomaga do usługi Azure AD zgodnie ze specyfikacją protokołu OAuth i będzie wymuszany w punktach końcowych v1 i v2.

Jeśli aplikacja używa kody autoryzacji uzyskiwanie tokenów dla wielu zasobów, zalecamy Użyj kodu, aby uzyskać token odświeżania, a następnie używać tego tokenu odświeżania w celu pobrania dodatkowe tokeny dla innych zasobów. Kody autoryzacji należy używać tylko raz, ale tokenów odświeżania mogą być wielokrotnie używane w wielu zasobach. Aplikacja, która podejmuje próbę ponownego użycia kodu uwierzytelniania podczas przepływu kodu OAuth otrzymają komunikat o błędzie invalid_grant.

Ta i inne zmiany dotyczące protokołów, zobacz [pełną listę Nowości dla uwierzytelniania](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes).
 
---

### <a name="converged-security-info-management-for-self-service-password-sspr-and-multi-factor-authentication-mfa"></a>Zarządzanie informacjami konwergentnej zabezpieczeń haseł (SSPR) i usługi Multi-Factor Authentication (MFA)

**Wprowadź** Nowa funkcja  
**Kategoria usługi:** SSPR  
**Możliwość produktu:** Uwierzytelnianie użytkownika
 
Ta nowa funkcja ułatwia osób Zarządzanie swoich informacji zabezpieczających (na przykład, numer telefonu, aplikacja mobilna i tak dalej) dla funkcji samoobsługowego resetowania HASEŁ i uwierzytelniania MFA w jednej lokalizacji i doświadczenie; w porównaniu z wcześniej, gdzie to zostało zrobione w dwóch różnych lokalizacjach.

To środowisko konwergentnej działa także dla osób korzystających z funkcji samoobsługowego resetowania HASEŁ lub uwierzytelniania MFA. Ponadto jeśli Twoja organizacja nie wymusić rejestracji uwierzytelniania Wieloskładnikowego lub samoobsługowego resetowania HASEŁ, osoby nadal można zarejestrować żadnych uwierzytelniania Wieloskładnikowego lub samoobsługowego resetowania HASEŁ zabezpieczeń informacji metod dozwolona przez Twoją organizację, z poziomu portalu Moje aplikacje.

Jest to zgłoszenie zgody na uczestnictwo w publicznej wersji zapoznawczej. Administratorzy mogą włączyć nowe środowisko (w razie potrzeby) dla wybranej grupy lub dla wszystkich użytkowników w dzierżawie. Aby uzyskać więcej informacji na temat doświadczeń z osiągnięcia zbieżności zobacz [Konwergentne środowisko blog](https://cloudblogs.microsoft.com/enterprisemobility/2018/08/06/mfa-and-sspr-updates-now-in-public-preview/)

---

### <a name="new-http-only-cookies-setting-in-azure-ad-application-proxy-apps"></a>Nowe tylko HTTP ustawienia dotyczącego plików cookie w aplikacje serwera proxy aplikacji usługi Azure AD

**Wprowadź** Nowa funkcja  
**Kategoria usługi:** Serwer proxy aplikacji  
**Możliwość produktu:** Access Control

Dostępne jest nowe ustawienie o nazwie, **plików cookie HTTP-Only** w aplikacjach serwer Proxy aplikacji. To ustawienie zapewnia dodatkowe zabezpieczenia, dołączając flagi HTTPOnly w nagłówku odpowiedzi HTTP, zarówno serwer Proxy aplikacji dostępu i sesji plików cookie, zatrzymanie dostępu do pliku cookie. pochodzące ze skryptu po stronie klienta i uniemożliwia dalsze akcje, takie jak kopiowanie lub Modyfikowanie pliku cookie. Chociaż ta flaga nie został wcześniej użyty, pliki cookie były zawsze szyfrowane, a następnie przesyłane przy użyciu połączenia SSL w celu zabezpieczenia przed Niepoprawne modyfikacje.

To ustawienie nie jest zgodna z aplikacjami korzystającymi z kontrolki ActiveX, takich jak pulpitu zdalnego. Jeśli jesteś w takiej sytuacji firma Microsoft zaleca, wyłącz to ustawienie.

Aby uzyskać więcej informacji na temat ustawień plików cookie HTTP-Only zobacz [publikowania aplikacji przy użyciu serwera Proxy aplikacji usługi Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-publish-azure-portal).

---

### <a name="privileged-identity-management-pim-for-azure-resources-supports-management-group-resource-types"></a>Privileged Identity Management (PIM) dla zasobów platformy Azure obsługuje typy zasobów w grupie zarządzania

**Wprowadź** Nowa funkcja  
**Kategoria usługi:** Privileged Identity Management  
**Możliwość produktu:** Privileged Identity Management
 
Ustawienia aktywacji i przypisania Just-In-Time można teraz stosować do typów zasobów grupy zarządzania, tak samo, jak już jak w przypadku subskrypcji, grupy zasobów i zasoby (takie jak maszyny wirtualne, App Services i inne). Ponadto każda osoba z rolą, która zapewnia dostęp administratora dla grupy zarządzania może odnajdywać i zarządzać tym zasobem w usłudze PIM.

Aby uzyskać więcej informacji dotyczących usługi PIM i zasobów platformy Azure, zobacz [odnajdywanie i zarządzanie zasobami platformy Azure przy użyciu Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-resource-roles-discover-resources)
 
---

### <a name="application-access-preview-provides-faster-access-to-the-azure-ad-portal"></a>Dostęp do aplikacji (wersja zapoznawcza) umożliwia szybszy dostęp do portalu usługi Azure AD

**Wprowadź** Nowa funkcja  
**Kategoria usługi:** Privileged Identity Management  
**Możliwość produktu:** Privileged Identity Management
 
Obecnie podczas aktywacji roli przy użyciu usługi PIM, może potrwać ponad 10 minut uprawnień zastosować zmiany. Jeśli zdecydujesz się używać dostępu do aplikacji, która jest obecnie dostępna w publicznej wersji zapoznawczej, Administratorzy mogą uzyskiwać dostęp do portalu usługi Azure AD zaraz po zakończeniu żądania aktywacji.

Obecnie dostęp do aplikacji obsługuje tylko środowisko portalu usługi Azure AD i zasobów platformy Azure. Aby uzyskać więcej informacji na temat usługi PIM i dostęp do aplikacji, zobacz [co to jest usługa Azure AD Privileged Identity Management?](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure)
 
---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---august-2018"></a>Nowe aplikacje federacyjne dostępne w galerii aplikacji usługi Azure AD — sierpnia 2018

**Wprowadź** Nowa funkcja  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwość produktu:** Integracje innych firm
 
W sierpnia 2018 Dodaliśmy obsługę 16 nowych aplikacjach z Federacją w galerii aplikacji:

[Hornbill](https://docs.microsoft.com/azure/active-directory/saas-apps/hornbill-tutorial), [Bridgeline niepowiązanych](https://docs.microsoft.com/azure/active-directory/saas-apps/bridgelineunbound-tutorial), [sos Labs — Mobile i testowanie sieci Web](https://docs.microsoft.com/azure/active-directory/saas-apps/saucelabs-mobileandwebtesting-tutorial), [Meta sieci łącznika](https://docs.microsoft.com/azure/active-directory/saas-apps/metanetworksconnector-tutorial), [sposobów](https://docs.microsoft.com/azure/active-directory/saas-apps/waywedo-tutorial), [Spotinst](https://docs.microsoft.com/azure/active-directory/saas-apps/spotinst-tutorial), [ProMaster (przez Inlogik)](https://docs.microsoft.com/azure/active-directory/saas-apps/promaster-tutorial), SchoolBooking, [4me](https://docs.microsoft.com/azure/active-directory/saas-apps/4me-tutorial), [dokumentacji](https://docs.microsoft.com/azure/active-directory/saas-apps/DOSSIER-tutorial), [N2F - wydatków Raporty](https://docs.microsoft.com/azure/active-directory/saas-apps/n2f-expensereports-tutorial), [Czat na żywo Comm100](https://docs.microsoft.com/azure/active-directory/saas-apps/comm100livechat-tutorial), [SafeConnect](https://docs.microsoft.com/azure/active-directory/saas-apps/safeconnect-tutorial), [ZenQMS](https://docs.microsoft.com/azure/active-directory/saas-apps/zenqms-tutorial), [eLuminate](https://docs.microsoft.com/azure/active-directory/saas-apps/eluminate-tutorial), [ Dovetale](https://docs.microsoft.com/azure/active-directory/saas-apps/dovetale-tutorial).

Aby uzyskać więcej informacji o aplikacjach, zobacz [integracji aplikacji SaaS w usłudze Azure Active Directory](https://aka.ms/appstutorial). Aby uzyskać więcej informacji o ofercie swoją aplikację w galerii aplikacji Azure AD, zobacz [umieść swoją aplikację w galerii aplikacji usługi Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="native-tableau-support-is-now-available-in-azure-ad-application-proxy"></a>Natywna obsługa Tableau jest teraz dostępna na serwerze Proxy aplikacji usługi Azure AD

**Wprowadź** Zmieniona funkcja  
**Kategoria usługi:** Serwer proxy aplikacji  
**Możliwość produktu:** Access Control

Za pomocą naszej aktualizacji z OpenID Connect protokołu OAuth 2.0 Code Grant dla naszych protokołu uwierzytelniania wstępnego, masz już konieczności dodatkowego konfigurowania Tableau za pomocą serwera Proxy aplikacji. Ta zmiana protokołu pomaga również lepszą obsługę bardziej nowoczesnych aplikacji przy użyciu tylko przekierowania HTTP, które często są obsługiwane w znacznikach języka JavaScript i HTML serwera Proxy aplikacji.

Aby uzyskać więcej informacji na temat naszych natywną obsługę Tableau, zobacz [aplikacji serwera Proxy Azure AD obecnie z natywną obsługą Tableau](https://blogs.technet.microsoft.com/applicationproxyblog/2018/08/14/azure-ad-application-proxy-now-with-native-tableau-support).

---

### <a name="new-support-to-add-google-as-an-identity-provider-for-b2b-guest-users-in-azure-active-directory-preview"></a>Obsługa nowych dodać Google jako dostawcy tożsamości dla użytkowników-gości B2B w usłudze Azure Active Directory (wersja zapoznawcza)

**Wprowadź** Nowa funkcja  
**Kategoria usługi:** B2B  
**Możliwość produktu:** B2B/B2C

Przez skonfigurowanie Federacji za pomocą usługi Google w Twojej organizacji, można pozwolić zaproszonego logowania użytkowników usługi Gmail udostępnionych aplikacji i zasobów przy użyciu istniejącego konta Google, bez konieczności tworzenia osobistych Account Microsoft (MSA) lub konta usługi Azure AD.

Jest to zgłoszenie zgody na uczestnictwo w publicznej wersji zapoznawczej. Aby uzyskać więcej informacji na temat Federacji Google zobacz [Dodaj Google jako dostawcy tożsamości dla użytkowników-gości B2B](https://docs.microsoft.com/azure/active-directory/b2b/google-federation).

---

## <a name="july-2018"></a>Lipiec 2018 r.

### <a name="improvements-to-azure-active-directory-email-notifications"></a>Ulepszenia powiadomień e-mail w usłudze Azure Active Directory

**Wprowadź** Zmieniona funkcja  
**Kategoria usługi:** Inne  
**Możliwość produktu:** Zarządzanie cyklem życia tożsamości
 
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

**Wprowadź** Nowa funkcja  
**Kategoria usługi:** Raportowanie  
**Możliwość produktu:** Monitorowanie i raportowanie

Dzienniki aktywności usługi Azure AD są teraz dostępne w publicznej wersji zapoznawczej dla usługi Azure Monitor (Usługa monitorowania całej platformy Azure). Usługa Azure Monitor udostępnia długoterminowego przechowywania i bezproblemową integrację, oprócz tych usprawnień:

- Długoterminowe przechowywanie kierując pliki dzienników z kontem usługi Azure storage.

- Bezproblemowa integracja rozwiązania SIEM, bez konieczności pisania lub obsługa skryptów niestandardowych.

- Bezproblemowa integracja z własnych niestandardowych rozwiązań, narzędzia do analizy lub rozwiązania do zarządzania zdarzeniami.

Aby uzyskać więcej informacji o tych nowych funkcjach, zobacz nasz blog [Dzienniki aktywności usługi Azure AD w usłudze Azure Monitor, diagnostykę znajduje się teraz w publicznej wersji zapoznawczej](https://cloudblogs.microsoft.com/enterprisemobility/2018/07/26/azure-ad-activity-logs-in-azure-monitor-diagnostics-now-in-public-preview/) i naszą dokumentacją [Dzienniki aktywności usługi Azure Active Directory na platformie Azure Monitor (wersja zapoznawcza)](https://docs.microsoft.com/azure/active-directory/reporting-azure-monitor-diagnostics-overview).

---

### <a name="conditional-access-information-added-to-the-azure-ad-sign-ins-report"></a>Informacje o dostępie warunkowym dodane do raportu logowania do usługi Azure AD

**Wprowadź** Nowa funkcja  
**Kategoria usługi:** Raportowanie  
**Możliwość produktu:** Ochrona i zabezpieczanie tożsamości
 
Ta aktualizacja pozwala zobaczyć, które zasady są oceniane, gdy użytkownik loguje się wraz z wyników zasad. Ponadto raport zawiera teraz typ aplikacji klienckiej używane przez użytkownika, dzięki czemu można zidentyfikować ruch w protokole starszej wersji. Wpisy raportu mogą teraz również być wyszukiwane identyfikator korelacji, który można znaleźć w komunikacie o błędzie widocznych dla użytkownika i może służyć do identyfikowania i rozwiązywania problemów z pasujące żądanie logowania.

---

### <a name="view-legacy-authentications-through-sign-ins-activity-logs"></a>Widok starszych uwierzytelnienia za pomocą dzienników aktywności logowania

**Wprowadź** Nowa funkcja  
**Kategoria usługi:** Raportowanie  
**Możliwość produktu:** Monitorowanie i raportowanie
 
Wraz z wprowadzeniem **aplikację kliencką** dzienniki pola w aktywności logowania, klienci mogą teraz Zobacz użytkowników, którzy używają starszej wersji uwierzytelnienia. Klienci będą mogli korzystać z tych informacji przy użyciu interfejsu API programu Graph w MS logowania lub za pośrednictwem logowania Dzienniki aktywności w portalu usługi Azure AD, w którym można korzystać z **aplikację kliencką** formantu, aby odfiltrować uwierzytelnień starszej wersji. Zapoznaj się z dokumentacją, aby uzyskać więcej informacji.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---july-2018"></a>Nowe aplikacje federacyjne dostępne w galerii aplikacji usługi Azure AD — lipca 2018 r.

**Wprowadź** Nowa funkcja  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwość produktu:** Integracje innych firm
 
W lipca 2018 Dodaliśmy obsługę 16 nowych aplikacjach z Federacją w galerii aplikacji:

[Centrum innowacji](https://docs.microsoft.com/azure/active-directory/saas-apps/innovationhub-tutorial), [Leapsome](https://docs.microsoft.com/azure/active-directory/saas-apps/leapsome-tutorial), [niektóre administrator rejestracji Jednokrotnej](https://docs.microsoft.com/azure/active-directory/saas-apps/certainadminsso-tutorial), przemieszczania PSUC [iPass SmartConnect](https://docs.microsoft.com/azure/active-directory/saas-apps/ipasssmartconnect-tutorial), [zrzut ekranu-O-obie](https://docs.microsoft.com/azure/active-directory/saas-apps/screencast-tutorial) , PowerSchool Unified zajęć, [dołączania Eli](https://docs.microsoft.com/azure/active-directory/saas-apps/elionboarding-tutorial), [obsługę zdalnego Bomgar](https://docs.microsoft.com/azure/active-directory/saas-apps/bomgarremotesupport-tutorial), [Nimblex](https://docs.microsoft.com/azure/active-directory/saas-apps/nimblex-tutorial), [Imagineer WebVision](https://docs.microsoft.com/azure/active-directory/saas-apps/imagineerwebvision-tutorial) , [Insight4GRC](https://docs.microsoft.com/azure/active-directory/saas-apps/insight4grc-tutorial), [łącznika JoinNow SecureW2](https://docs.microsoft.com/azure/active-directory/saas-apps/securejoinnow-tutorial), [Kanbanize](https://review.docs.microsoft.com/azure/active-directory/saas-apps/kanbanize-tutorial), [SmartLPA](https://review.docs.microsoft.com/azure/active-directory/saas-apps/smartlpa-tutorial), [Base umiejętności](https://docs.microsoft.com/azure/active-directory/saas-apps/skillsbase-tutorial)

Aby uzyskać więcej informacji o aplikacjach, zobacz [integracji aplikacji SaaS w usłudze Azure Active Directory](https://aka.ms/appstutorial). Aby uzyskać więcej informacji o ofercie swoją aplikację w galerii aplikacji Azure AD, zobacz [umieść swoją aplikację w galerii aplikacji usługi Azure Active Directory](https://aka.ms/azureadapprequest).

---
 
### <a name="new-user-provisioning-saas-app-integrations---july-2018"></a>Nowy użytkownik inicjowania obsługi administracyjnej integracji aplikacji SaaS — lipca 2018 r.

**Wprowadź** Nowa funkcja  
**Kategoria usługi:** Aprowizowanie aplikacji  
**Możliwość produktu:** Integracje innych firm
 
Usługa Azure AD pozwala zautomatyzować procesy tworzenia, obsługi i usuwania tożsamości użytkowników w aplikacjach SaaS, takich jak Dropbox, Salesforce, ServiceNow i nie tylko. Do lipca 2018 dodano obsługę następujących aplikacji w galerii aplikacji Azure AD aprowizacji użytkowników:

- [Cisco WebEx](https://docs.microsoft.com/azure/active-directory/saas-apps/cisco-webex-provisioning-tutorial)

- [Bonusly](https://docs.microsoft.com/azure/active-directory/saas-apps/bonusly-provisioning-tutorial)

Aby uzyskać listę wszystkich aplikacji, które obsługują aprowizowania użytkowników w galerii usługi Azure AD, zobacz [integracji aplikacji SaaS w usłudze Azure Active Directory](https://aka.ms/appstutorial).

---

### <a name="connect-health-for-sync---an-easier-way-to-fix-orphaned-and-duplicate-attribute-sync-errors"></a>Connect Health do celów synchronizacji — łatwiejszy sposób naprawić błędy synchronizacji oddzielone i zduplikowanego atrybutu

**Wprowadź** Nowa funkcja  
**Kategoria usługi:** AD Connect  
**Możliwość produktu:** Monitorowanie i raportowanie
 
Program Azure AD Connect Health wprowadza samodzielne rozwiązanie problemu, ułatwiające wyróżnić i naprawić błędy synchronizacji. Ta funkcja rozwiązuje błędy synchronizacji zduplikowanym atrybutem i poprawki obiektów, które są oddzielone od usługi Azure AD. Diagnostyka to ma następujące zalety:

- Zawęża zduplikowanym atrybutem błędy synchronizacji, zapewniając określonej poprawki

- Stosuje poprawki dla scenariuszy usługi Azure AD, naprawianie błędów w jednym kroku w wersji dedykowanej

- Nie uaktualnienia lub konfiguracja jest wymagana do włączenia i korzystać z tej funkcji

Aby uzyskać więcej informacji, zobacz [diagnozowanie i rozwiązywanie błędów synchronizacji zduplikowanym atrybutem](https://docs.microsoft.com/azure/active-directory/connect-health/active-directory-aadconnect-health-diagnose-sync-errors)

---

### <a name="visual-updates-to-the-azure-ad-and-msa-sign-in-experiences"></a>Visual aktualizacji do usługi Azure AD i logowania konta Microsoft napotyka

**Wprowadź** Zmieniona funkcja  
**Kategoria usługi:** Azure AD  
**Możliwość produktu:** Uwierzytelnianie użytkownika

Zaktualizowaliśmy interfejs użytkownika dla usług online firmy Microsoft środowiska logowania takich jak dla usługi Office 365 i Azure. Ta zmiana sprawia, że ekrany mniej zatłoczoną i bardziej bezpośredni. Aby uzyskać więcej informacji na temat tej zmiany, zobacz [nadchodzące ulepszenia procesu logowania w usłudze Azure AD](https://cloudblogs.microsoft.com/enterprisemobility/2018/04/04/upcoming-improvements-to-the-azure-ad-sign-in-experience/) blogu.

---

### <a name="new-release-of-azure-ad-connect---july-2018"></a>Nowa wersja programu Azure AD Connect — lipca 2018 r.

**Wprowadź** Zmieniona funkcja  
**Kategoria usługi:** Aprowizowanie aplikacji  
**Możliwość produktu:** Zarządzanie cyklem życia tożsamości

Najnowsza wersja programu Azure AD Connect obejmuje: 

- Poprawki i aktualizacje obsługi 

- Ogólne udostępnienie usług integracji Federację Ping

- Aktualizacje do najnowszego klienta SQL 2012 

Aby uzyskać więcej informacji na temat tej aktualizacji [, zobacz Azure AD Connect: Historia wersji](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-version-history)

---

### <a name="updates-to-the-terms-of-use-end-user-ui"></a>Aktualizacje warunków użytkowania użytkownika końcowego

**Wprowadź** Zmieniona funkcja  
**Kategoria usługi:** Warunki użytkowania  
**Możliwość produktu:** Ład

Trwa aktualizowanie ciągu akceptacji w Interfejsie użytkownika końcowego warunków użytkowania.

**Aktualny tekst.** Aby uzyskać dostęp do zasobów [tenantName], musisz zaakceptować warunki użytkowania.<br>**Nowy tekst.** Aby uzyskać dostęp do zasobów [tenantName], możesz przeczytać warunki użytkowania.

**Bieżący tekst:** Wybranie opcji Akceptuj oznacza, że akceptujesz wszystkie powyższe warunki użytkowania.<br>**Nowy tekst:** Kliknij przycisk Akceptuj, aby potwierdzić, że znasz i rozumiesz warunki użytkowania.

---
 
### <a name="pass-through-authentication-supports-legacy-protocols-and-applications"></a>Uwierzytelnianie przekazywane obsługuje starsze protokoły i aplikacje

**Wprowadź** Zmieniona funkcja  
**Kategoria usługi:** Uwierzytelnienia (logowania)  
**Możliwość produktu:** Uwierzytelnianie użytkownika
 
Uwierzytelnianie przekazywane teraz obsługuje starszych protokołów i aplikacji. Następujące ograniczenia są teraz w pełni obsługiwane:

- Logowania użytkowników do starszych aplikacji klienckich pakietu Office, pakietu Office 2010 i Office 2013 bez nowoczesnego uwierzytelniania.

- Dostęp do udostępnianie kalendarza i bezpłatne zajęty informacji w programie Exchange środowisk o hybrydowych w pakiecie Office 2010 tylko.

- Logowania użytkowników do usługi Skype dla firm aplikacji klienckich bez nowoczesnego uwierzytelniania.

- Użytkownik logowania do programu PowerShell w wersji 1.0.

- Programu Apple Device Enrollment Program (DEP) firmy Apple, przy użyciu Asystenta ustawień systemu iOS. 

---
 
### <a name="converged-security-info-management-for-self-service-password-reset-and-multi-factor-authentication"></a>Zarządzanie zabezpieczeniami konwergentnej informacje samoobsługowego resetowania haseł i uwierzytelniania wieloskładnikowego

**Wprowadź** Nowa funkcja  
**Kategoria usługi:** SSPR  
**Możliwość produktu:** Uwierzytelnianie użytkownika

Ta nowa funkcja umożliwia użytkownikom zarządzanie zabezpieczające (na przykład, numer telefonu, adres e-mail, aplikacji mobilnej i tak dalej) do samoobsługowego resetowania haseł (SSPR) i usługi Multi-Factor Authentication (MFA) w jednym środowisku. Użytkownicy mają już nie można zarejestrować tego samego jako informacje zabezpieczające dla funkcji samoobsługowego resetowania HASEŁ i uwierzytelniania Wieloskładnikowego w dwóch różnych środowiskach. Nowe środowisko dotyczy również użytkowników, którzy mają funkcji samoobsługowego resetowania HASEŁ lub uwierzytelniania MFA.

Jeśli organizacja nie ma wymuszanie rejestracji uwierzytelniania Wieloskładnikowego lub samoobsługowego resetowania HASEŁ, użytkownicy mogą rejestrować swoje informacje zabezpieczające za pośrednictwem **Moje aplikacje** portalu. W tym miejscu użytkownicy mogą rejestrować żadnych metod włączone dla uwierzytelniania Wieloskładnikowego lub samoobsługowego resetowania HASEŁ. 

Jest to zgłoszenie zgody na uczestnictwo w publicznej wersji zapoznawczej. Administratorzy mogą włączyć nowe środowisko (w razie potrzeby) dla wybranej grupy użytkowników lub dla wszystkich użytkowników w dzierżawie.

---
 
### <a name="use-the-microsoft-authenticator-app-to-verify-your-identity-when-you-reset-your-password"></a>Korzystanie z aplikacji Microsoft Authenticator do zweryfikowania Twojej tożsamości podczas resetowania hasła

**Wprowadź** Zmieniona funkcja  
**Kategoria usługi:** SSPR  
**Możliwość produktu:** Uwierzytelnianie użytkownika

Ta funkcja pozwala użytkowników innych niż administratorzy zweryfikować swoją tożsamość podczas resetowania hasła przy użyciu powiadomienia lub kodu z Microsoft Authenticator (lub dowolną inną aplikację). Po Administratorzy, Włącz ten samoobsługowego resetowania haseł — metoda, użytkownicy, którzy zarejestrowali się do aplikacji mobilnej za pośrednictwem aka.ms/mfasetup lub aka.ms/setupsecurityinfo można użyć aplikacji mobilnej jako metody weryfikacji podczas resetowania hasła.

Tylko powiadomienia aplikacji mobilnej może zostać włączona w ramach zasad, które wymagają dwóch metod, aby zresetować hasło.

---

## <a name="june-2018"></a>Czerwiec 2018 r.

### <a name="change-notice-security-fix-to-the-delegated-authorization-flow-for-apps-using-azure-ad-activity-logs-api"></a>Zmień powiadomienie: Poprawka zabezpieczeń do delegowanego przepływu autoryzacji dla aplikacji korzystających z interfejsu API dzienników aktywności usługi Azure AD

**Wprowadź** Plan zmiany  
**Kategoria usługi:** Raportowanie  
**Możliwość produktu:** Monitorowanie i raportowanie

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

**Wprowadź** Nowa funkcja  
**Kategoria usługi:** ND  
**Możliwość produktu:** Platforma

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

**Wprowadź** Nowa funkcja  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwość produktu:** Integracje innych firm
 
W czerwcu 2018 Dodaliśmy obsługę tych 15 nowe aplikacje z Federacją w galerii aplikacji:

[Skytap](https://docs.microsoft.com/azure/active-directory/active-directory-saas-skytap-tutorial), [rozliczania utworów muzycznych](https://docs.microsoft.com/azure/active-directory/active-directory-saas-settlingmusic-tutorial), [aplikacji LOB z obsługą tokenu języka SAML 1.1](https://docs.microsoft.com/azure/active-directory/active-directory-saas-saml-tutorial), [Supermood](https://docs.microsoft.com/azure/active-directory/active-directory-saas-supermood-tutorial), [Autotask](https://docs.microsoft.com/azure/active-directory/active-directory-saas-autotaskendpointbackup-tutorial), [ Kopia zapasowa punktu końcowego](https://docs.microsoft.com/azure/active-directory/active-directory-saas-autotaskendpointbackup-tutorial), [sieci Skyhigh](https://docs.microsoft.com/azure/active-directory/active-directory-saas-skyhighnetworks-tutorial), Smartway2, [TonicDM](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tonicdm-tutorial), [Moconavi](https://docs.microsoft.com/azure/active-directory/active-directory-saas-moconavi-tutorial), [Zoho jeden](https://docs.microsoft.com/azure/active-directory/active-directory-saas-zohoone-tutorial), [ Lokalny program SharePoint](https://docs.microsoft.com/azure/active-directory/active-directory-saas-sharepoint-on-premises-tutorial), [przewiduje CX Suite](https://docs.microsoft.com/azure/active-directory/active-directory-saas-foreseecxsuite-tutorial), [Vidyard](https://docs.microsoft.com/azure/active-directory/active-directory-saas-vidyard-tutorial), [ChronicX](https://docs.microsoft.com/azure/active-directory/active-directory-saas-chronicx-tutorial)

Aby uzyskać więcej informacji o aplikacjach, zobacz [integracji aplikacji SaaS w usłudze Azure Active Directory](https://aka.ms/appstutorial). Aby uzyskać więcej informacji o ofercie swoją aplikację w galerii aplikacji Azure AD, zobacz [umieść swoją aplikację w galerii aplikacji usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing). 

---

### <a name="azure-ad-password-protection-is-available-in-public-preview"></a>Ochrony hasłem w usłudze Azure AD jest dostępna w publicznej wersji zapoznawczej

**Wprowadź** Nowa funkcja  
**Kategoria usługi:** Identity Protection  
**Możliwość produktu:** Uwierzytelnianie użytkownika

Użyj hasła usługi Azure AD ochrony, aby wyeliminować łatwe do odgadnięcia hasła ze środowiska. Eliminowanie haseł pomaga zmniejszyć ryzyko naruszenia zabezpieczeń z atak typu osłony hasła.

W szczególności ochrony haseł usługi Azure AD umożliwia:

- Ochronę kont w organizacji w usługi Azure AD i systemu Windows Server Active Directory (AD). 
- Zatrzymuje użytkownikom korzystanie z haseł na liście więcej niż 500 najczęściej używanych haseł, a ponad 1 milion znaków podstawienia odmiany tych haseł.
- Administrowanie ochrony haseł usługi Azure AD z jednej lokalizacji w portalu usługi Azure AD dla usługi Azure AD i AD systemu Windows Server dla lokalnego.

Aby uzyskać więcej informacji na temat ochrony haseł usługi Azure AD, zobacz [eliminowanie nieprawidłowych haseł w organizacji](https://aka.ms/aadpasswordprotectiondocs).

---

### <a name="new-all-guests-conditional-access-policy-template-created-during-terms-of-use-creation"></a>Nowy szablon zasad dostępu warunkowego "Wszyscy Goście" utworzony podczas tworzenia warunków użytkowania

**Wprowadź** Nowa funkcja  
**Kategoria usługi:** Warunki użytkowania  
**Możliwość produktu:** Ład

Podczas tworzenia warunków użytkowania nowy szablon zasad dostępu warunkowego jest również tworzony dla "Wszyscy Goście" i "wszystkie aplikacje". Ten nowy szablon zasad mają zastosowanie nowo utworzony warunków użytkowania, co usprawnia tworzenie i wymuszanie procesu dla gości.

Aby uzyskać więcej informacji, zobacz [Azure Active Directory — warunki dotyczące użycia funkcji](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use).

---

### <a name="new-custom-conditional-access-policy-template-created-during-terms-of-use-creation"></a>Nowy "niestandardowy" szablon zasad dostępu warunkowego utworzony podczas tworzenia warunków użytkowania

**Wprowadź** Nowa funkcja  
**Kategoria usługi:** Warunki użytkowania  
**Możliwość produktu:** Ład

Podczas tworzenia warunków użytkowania tworzony jest również nowy "niestandardowy" szablon zasad dostępu warunkowego. Ten nowy szablon zasad umożliwia utworzenie warunków użytkowania, a następnie natychmiastowe przejście do bloku tworzenia zasad dostępu warunkowego, bez konieczności ręcznego nawigowania po portalu.

Aby uzyskać więcej informacji, zobacz [Azure Active Directory — warunki dotyczące użycia funkcji](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use).

---

### <a name="new-and-comprehensive-guidance-about-deploying-azure-multi-factor-authentication"></a>Nowe i wyczerpujące wskazówki dotyczące wdrażania usługi Azure Multi-Factor Authentication

**Wprowadź** Nowa funkcja  
**Kategoria usługi:** Inne  
**Możliwość produktu:** Ochrona i zabezpieczanie tożsamości
 
Udostępniliśmy nowe wskazówki krok po kroku dotyczące wdrażania usługi Azure Multi-Factor Authentication (MFA) w Twojej organizacji.

Aby wyświetlić w przewodniku wdrażania uwierzytelniania Wieloskładnikowego, przejdź do [wskazówki dotyczące wdrażania tożsamości](https://aka.ms/DeploymentPlans) repozytorium w witrynie GitHub. Aby przekazać opinię na temat wskazówki dotyczące wdrażania, użyj [wdrożenia planu forum z opiniami](https://aka.ms/deploymentplanfeedback). Jeśli masz jakieś pytania dotyczące wskazówki dotyczące wdrażania, skontaktuj się z nami pod adresem [IDGitDeploy](mailto:idgitdeploy@microsoft.com).

---

### <a name="azure-ad-delegated-app-management-roles-are-in-public-preview"></a>Usługa Azure AD delegowane Zarządzanie aplikacjami, które role są w publicznej wersji zapoznawczej

**Wprowadź** Nowa funkcja  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwość produktu:** Access Control

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

**Wprowadź** Plan zmiany  
**Kategoria usługi:** Uwierzytelnienia (logowania)  
**Możliwość produktu:** Platforma  

Oprogramowanie jako usługa, takich jak Azure Active Directory (Azure AD) są przeznaczone do najlepszej metody, przechodząc bezpośrednio przez Internet, bez korzystania z usługi ExpressRoute lub innych prywatnej tuneli sieci VPN. W związku z tym na **1 sierpnia 2018**, firma Microsoft nie będzie obsługiwać usługi ExpressRoute dla usługi Azure AD przy użyciu publicznej komunikacji równorzędnej Azure i społeczności platformy Azure w komunikacji równorzędnej firmy Microsoft. Wszystkie usługi, wpływ tej zmiany może się okazać, Azure AD ruchu stopniowo przesunięcie z usługi ExpressRoute z Internetem.

Zmieniamy się obsłudze, Wiemy także, są nadal sytuacji, w których konieczne może być użyć to dedykowany zestaw obwodów dla ruchu uwierzytelniania. W związku z tym usługa Azure AD będzie obsługiwać ograniczenia zakresu adresów IP dla dzierżawcy przy użyciu usługi ExpressRoute i usług już na komunikacji równorzędnej firmy Microsoft ze społecznością "Innych usług Online pakietu Office 365". Jeśli dotyczy usług, ale wymagają usługi ExpressRoute, należy wykonać następujące czynności:

- **Jeśli komputer jest w publicznej komunikacji równorzędnej Azure.** Przenieś do komunikacji równorzędnej firmy Microsoft i zarejestrować się w celu **inne usługi Online Office 365 (12076:5100)** społeczności. Aby uzyskać więcej informacji o tym, jak można przenieść z publicznej komunikacji równorzędnej Azure do komunikacji równorzędnej firmy Microsoft, zobacz [Przenieś publicznej komunikacji równorzędnej do komunikacji równorzędnej firmy Microsoft](https://docs.microsoft.com/azure/expressroute/how-to-move-peering) artykułu.

- **Jeśli jesteś w komunikacji równorzędnej firmy Microsoft.** Zaloguj się w celu **inne usługi Office 365 Online service (12076:5100)** społeczności. Aby uzyskać więcej informacji na temat wymagania dotyczące routingu, zobacz [obsługę protokołu BGP społeczności sekcji](https://docs.microsoft.com/azure/expressroute/expressroute-routing#bgp) artykułu wymagania dotyczące routingu usługi ExpressRoute.

Jeśli musisz nadal używać dedykowanego obwodów, konieczne będzie komunikować się zespołem Account firmy Microsoft o tym, jak uzyskać pozwolenie na korzystanie **inne usługi Office 365 Online service (12076:5100)** społeczności. Tablica zarządzana Office przeglądu MS sprawdzi, czy potrzebujesz tych obwodów i upewnij się, że rozumiesz implikacje techniczne ich zachowania. Subskrypcje nieautoryzowane próby utworzenia filtrów tras dla usługi Office 365 będą otrzymywać komunikat o błędzie. 
 
---

### <a name="microsoft-graph-apis-for-administrative-scenarios-for-tou"></a>Interfejsy API programu Graph firmy Microsoft dla scenariuszy administracyjnych dla warunków użytkowania

**Wprowadź** Nowa funkcja  
**Kategoria usługi:** Warunki użytkowania  
**Możliwość produktu:** Środowisko deweloperskie
 
Dodaliśmy Microsoft Graph interfejsów API dla operacji administrowania warunkami użytkowania usługi Azure AD. Możesz tworzyć, aktualizować i usuwać obiekty warunków użytkowania.

---

### <a name="add-azure-ad-multi-tenant-endpoint-as-an-identity-provider-in-azure-ad-b2c"></a>Dodaj punkt końcowy z wieloma dzierżawami usługi Azure AD jako dostawcy tożsamości w usłudze Azure AD B2C

**Wprowadź** Nowa funkcja  
**Kategoria usługi:** B2C — Consumer Identity Management  
**Możliwość produktu:** B2B/B2C
 
Za pomocą zasad niestandardowych, możesz teraz dodać wspólnego punktu końcowego usługi Azure AD jako dostawcy tożsamości w usłudze Azure AD B2C. Umożliwia to pojedynczy punkt wejścia dla wszystkich użytkowników usługi Azure AD, którzy logują się do aplikacji. Aby uzyskać więcej informacji, [Zobacz Azure Active Directory B2C: Zezwól użytkownikom na logowanie się do wielodostępnego dostawcy tożsamości usługi Azure AD przy użyciu zasad](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-setup-commonaad-custom)niestandardowych.

---

### <a name="use-internal-urls-to-access-apps-from-anywhere-with-our-my-apps-sign-in-extension-and-the-azure-ad-application-proxy"></a>Wewnętrzne adresy URL umożliwia dostęp do aplikacji z dowolnego miejsca za pomocą naszego rozszerzenia Moje aplikacje logowania i serwera Proxy aplikacji usługi AD systemu Azure

**Wprowadź** Nowa funkcja  
**Kategoria usługi:** Moje aplikacje  
**Możliwość produktu:** SSO
 
Użytkownicy mogą teraz uzyskiwać dostęp do aplikacji za pomocą wewnętrznych adresów URL nawet spoza sieci firmowej przy użyciu bezpiecznego Moje aplikacje logowania rozszerzenia dla usługi Azure AD. To będzie działać z dowolnej aplikacji, która została opublikowana przy użyciu serwera Proxy aplikacji usługi Azure AD, w dowolnej przeglądarce, która także ma zainstalowane rozszerzenie przeglądarki panelu dostępu. Funkcji Przekierowywanie adresu URL jest automatycznie włączone, gdy użytkownik loguje się do rozszerzenia. Rozszerzenie jest dostępne do pobrania na [Microsoft Edge](https://go.microsoft.com/fwlink/?linkid=845176), [Chrome](https://go.microsoft.com/fwlink/?linkid=866367), i [Firefox](https://go.microsoft.com/fwlink/?linkid=866366).

---
 
### <a name="azure-active-directory---data-in-europe-for-europe-customers"></a>Usługa Azure Active Directory — dane w Europie klienci Europa

**Wprowadź** Nowa funkcja  
**Kategoria usługi:** Inne  
**Możliwość produktu:** GoLocal

Klienci w Europie wymagają swoich danych, aby pozostać w Europie i nie są replikowane poza Europejskiego centrów danych na potrzeby spotkania i poufności informacji w europejskich przepisów. To [artykułu](https://go.microsoft.com/fwlink/?linkid=872328) zawiera szczegółowe informacje, jakie informacje tożsamości będą przechowywane w Europie i udostępniają szczegółowe informacje na temat informacji, który ma być przechowywana poza Europejskich centrach danych. 

---
 
### <a name="new-user-provisioning-saas-app-integrations---may-2018"></a>Nowy użytkownik inicjowania obsługi administracyjnej integracji aplikacji SaaS — maj 2018 r.

**Wprowadź** Nowa funkcja  
**Kategoria usługi:** Aprowizowanie aplikacji  
**Możliwość produktu:** Integracje innych firm
 
Usługa Azure AD pozwala zautomatyzować procesy tworzenia, obsługi i usuwania tożsamości użytkowników w aplikacjach SaaS, takich jak Dropbox, Salesforce, ServiceNow i nie tylko. W maju 2018 r Dodaliśmy obsługę następujących aplikacji w galerii aplikacji Azure AD aprowizacji użytkowników:

- [BlueJeans](https://docs.microsoft.com/azure/active-directory/active-directory-saas-bluejeans-provisioning-tutorial)

- [OnDemand podstawy](https://docs.microsoft.com/azure/active-directory/active-directory-saas-cornerstone-ondemand-provisioning-tutorial)

- [Zendesk](https://docs.microsoft.com/azure/active-directory/active-directory-saas-zendesk-provisioning-tutorial)

Aby uzyskać listę wszystkich aplikacji, które obsługują aprowizowania użytkowników w galerii usługi Azure AD, zobacz [ https://aka.ms/appstutorial ](https://aka.ms/appstutorial).

---
 
### <a name="azure-ad-access-reviews-of-groups-and-app-access-now-provides-recurring-reviews"></a>Przeglądy dostępu usługi Azure AD, grup i przeglądy cyklicznego udostępnia teraz dostęp do aplikacji

**Wprowadź** Nowa funkcja  
**Kategoria usługi:** Kontrole uprawnień dostępu  
**Możliwość produktu:** Ład
 
Przegląd dostępu grup i aplikacji jest teraz ogólnie dostępna w ramach usługi Azure AD Premium P2.  Administratorzy, będzie można skonfigurować przeglądów dostępu członkostwa w grupach i przypisania aplikacji, aby automatycznie powtarzać w regularnych odstępach czasu, takich jak co miesiąc lub kwartalną.

---

### <a name="azure-ad-activity-logs-sign-ins-and-audit-are-now-available-through-ms-graph"></a>Dzienniki aktywności usługi AD platformy Azure (logowania i inspekcji) są teraz dostępne za pośrednictwem MS Graph

**Wprowadź** Nowa funkcja  
**Kategoria usługi:** Raportowanie  
**Możliwość produktu:** Monitorowanie i raportowanie
 
Dzienniki aktywności usługi AD platformy Azure, co obejmuje dzienniki inspekcji i logowania, są teraz dostępne za pośrednictwem MS Graph. Udostępniliśmy dwie punktów końcowych za pośrednictwem MS Graph dostępu do tych dzienników. Zapoznaj się z naszym [dokumenty](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-getting-started-azure-portal) uzyskać programowy dostęp do usługi Azure AD interfejsy API raportowania na rozpoczęcie pracy. 

---
 
### <a name="improvements-to-the-b2b-redemption-experience-and-leave-an-org"></a>Ulepszenia realizacji B2B środowisko i opuścić organizacji

**Wprowadź** Nowa funkcja  
**Kategoria usługi:** B2B  
**Możliwość produktu:** B2B/B2C

**Wykupu just in Time:** Po udostępnieniu zasobu użytkownikowi gość przy użyciu interfejsu API B2B — nie musisz wysyłać specjalnej wiadomości e-mail z zaproszeniem. W większości przypadków użytkownik-Gość można uzyskać dostępu do zasobu i zostaną podjęte przez środowisko realizacji dokładnie na czas. Nie wpływa na z powodu brakujących wiadomości e-mail. Nie ma więcej poproszenie użytkowników gości "Czy możesz kliknąć łącze realizacji wysłania systemu, możesz?". Oznacza to, gdy SPO korzysta z Menedżera zaproszenie — mętna załączniki mogą mieć ten sam Kanoniczny adres URL dla wszystkich użytkowników — wewnętrzne i zewnętrzne — w dowolnym stanie realizacji.

**Nowoczesne środowisko wykupu:** Nie ma więcej informacji na stronie początkowej dzielenia ekranu. Użytkownicy będą widzieć nowoczesny zgoda doświadczenia z organizacji zapraszającej zasady zachowania poufności informacji, podobnie jak w przypadku aplikacji innych firm.

**Użytkownicy-Goście mogą opuścić organizacji:** Gdy relacja użytkownika z organizacją zostanie przełączona, może ona samoobsługowo wychodzić z organizacji. Nie ma więcej wywoływania administratora organizacji zapraszającej o "usunięte", nie ma więcej wywoływanie biletami pomocy technicznej.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---may-2018"></a>Nowe aplikacje federacyjne dostępne w galerii aplikacji usługi Azure AD — maj 2018 r.

**Wprowadź** Nowa funkcja  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwość produktu:** Integracje innych firm
 
W maju 2018 Dodaliśmy obsługę tych 18 nowe aplikacje z Federacją do galerii aplikacji:

[AwardSpring](https://docs.microsoft.com/azure/active-directory/active-directory-saas-awardspring-tutorial), określają sposób Infogix Data3Sixty, [Yodeck](https://docs.microsoft.com/azure/active-directory/active-directory-saas-infogix-tutorial), [narzędzia Jamf Pro](https://docs.microsoft.com/azure/active-directory/active-directory-saas-jamfprosamlconnector-tutorial), [KnowledgeOwl](https://docs.microsoft.com/azure/active-directory/active-directory-saas-knowledgeowl-tutorial), [Envi MMIS](https://docs.microsoft.com/azure/active-directory/active-directory-saas-envimmis-tutorial), [LaunchDarkly](https://docs.microsoft.com/azure/active-directory/active-directory-saas-launchdarkly-tutorial), [Adobe Captivate Prime](https://docs.microsoft.com/azure/active-directory/active-directory-saas-adobecaptivateprime-tutorial), [Online montażowy](https://docs.microsoft.com/azure/active-directory/active-directory-saas-montageonline-tutorial),[まなびポケット](https://docs.microsoft.com/azure/active-directory/active-directory-saas-manabipocket-tutorial), OpenReel, [łuk publikowania — Usługa rejestracji Jednokrotnej ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-arc-tutorial), [PlanGrid](https://docs.microsoft.com/azure/active-directory/active-directory-saas-plangrid-tutorial), [iWellnessNow](https://docs.microsoft.com/azure/active-directory/active-directory-saas-iwellnessnow-tutorial), [Proxyclick](https://docs.microsoft.com/azure/active-directory/active-directory-saas-proxyclick-tutorial), [Riskware](https://docs.microsoft.com/azure/active-directory/active-directory-saas-riskware-tutorial), [stada](https://docs.microsoft.com/azure/active-directory/active-directory-saas-flock-tutorial), [Reviewsnap](https://docs.microsoft.com/azure/active-directory/active-directory-saas-reviewsnap-tutorial)

Aby uzyskać więcej informacji o aplikacjach, zobacz [integracji aplikacji SaaS w usłudze Azure Active Directory](https://aka.ms/appstutorial).

Aby uzyskać więcej informacji o ofercie swoją aplikację w galerii aplikacji Azure AD, zobacz [umieść swoją aplikację w galerii aplikacji usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing).

---
 
### <a name="new-step-by-step-deployment-guides-for-azure-active-directory"></a>Nowe przewodniki krok po kroku wdrożenia usługi Azure Active Directory

**Wprowadź** Nowa funkcja  
**Kategoria usługi:** Inne  
**Możliwość produktu:** Katalog
 
Nowe wskazówki krok po kroku dotyczące sposobu wdrażania Azure Active Directory (Azure AD), w tym samoobsługowego resetowania hasła (SSPR), logowania jednokrotnego (SSO), dostępu warunkowego (CA), serwera proxy aplikacji, aprowizacji użytkowników, Active Directory Federation Services (ADFS) do programu Uwierzytelnianie przekazywane (PTA) i usługi ADFS do synchronizacji skrótów haseł (PHS).

Aby wyświetlić wskazówki dotyczące wdrażania, przejdź do [wskazówki dotyczące wdrażania tożsamości](https://aka.ms/DeploymentPlans) repozytorium w witrynie GitHub. Aby przekazać opinię na temat wskazówki dotyczące wdrażania, użyj [wdrożenia planu forum z opiniami](https://aka.ms/deploymentplanfeedback). Jeśli masz jakieś pytania dotyczące wskazówki dotyczące wdrażania, skontaktuj się z nami pod adresem [IDGitDeploy](mailto:idgitdeploy@microsoft.com).

---

### <a name="enterprise-applications-search---load-more-apps"></a>Aplikacje dla przedsiębiorstw wyszukiwanie - ładowanie większej liczby aplikacji

**Wprowadź** Nowa funkcja  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwość produktu:** SSO
 
Problem ze znalezieniem informacji aplikacji / jednostki usług? Dodaliśmy możliwość ładowania większej liczby aplikacji w aplikacjach firmowych listę wszystkich aplikacji. Domyślnie pokazujemy 20 aplikacji. Możesz teraz kliknąć Link, **Załaduj więcej** Aby wyświetlić dodatkowe aplikacje. 

---
 
### <a name="the-may-release-of-aadconnect-contains-a-public-preview-of-the-integration-with-pingfederate-important-security-updates-many-bug-fixes-and-new-great-new-troubleshooting-tools"></a>May wersji AADConnect zawiera publiczną wersję zapoznawczą integracji z serwerem PingFederate, ważne aktualizacje zabezpieczeń, wiele poprawek usterek i nowe great nowych narzędzi do rozwiązywania problemów. 

**Wprowadź** Zmieniona funkcja  
**Kategoria usługi:** AD Connect  
**Możliwość produktu:** Zarządzanie cyklem życia tożsamości
 
May wersji AADConnect zawiera publiczną wersję zapoznawczą integracji z serwerem PingFederate, ważne aktualizacje zabezpieczeń, wiele poprawek usterek i nowe great nowych narzędzi do rozwiązywania problemów. Można znaleźć informacje o wersji [tutaj](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-version-history#118190).

---

### <a name="azure-ad-access-reviews-auto-apply"></a>Przeglądy dostępu w usłudze Azure AD: automatycznie Zastosuj

**Wprowadź** Zmieniona funkcja  
**Kategoria usługi:** Kontrole uprawnień dostępu  
**Możliwość produktu:** Ład

Przeglądy dostępu dotyczące dostępu grupy i aplikacje są teraz ogólnie dostępne w ramach usługi Azure AD Premium P2. Administrator może skonfigurować, aby automatycznie zastosować zmiany wprowadzone przez recenzenta do tej grupy lub aplikacji, ponieważ zakończeniu przeglądu dostępu. Administrator może również określić, co się dzieje z przedłużenia dostępu użytkownika, jeśli recenzenci nie odpowie, spowodować usunięcie dostępu, móc chronić dostęp do lub rekomendacje systemu. 

---

### <a name="id-tokens-can-no-longer-be-returned-using-the-query-response_mode-for-new-apps"></a>Tokeny Identyfikatora już nie mogą być zwracane w przypadku nowych aplikacji przy użyciu response_mode zapytania. 

**Wprowadź** Zmieniona funkcja  
**Kategoria usługi:** Uwierzytelnienia (logowania)  
**Możliwość produktu:** Uwierzytelnianie użytkownika
 
Aplikacje utworzone na lub po 25 kwietnia 2018 r. nie będzie już mógł zażądać **id_token** przy użyciu **zapytania** response_mode.  To umożliwia wbudowanej usługi Azure AD ze specyfikacjami OIDC, a także pomaga zmniejszyć obszar ataków aplikacji.  Aplikacje utworzone przed 25 kwietnia 2018 r. nie jest zablokowany za pomocą **zapytania** response_mode przy użyciu elementu response_type z **id_token**.  Błąd zwrócony podczas żądania elementu id_token z usługi AAD, **AADSTS70007: "query" nie jest obsługiwaną wartością "response_mode" podczas żądania tokenu**.

**Fragmentu** i **form_post** response_modes w dalszym ciągu działać — podczas tworzenia nowych obiektów aplikacji (na przykład w przypadku użycia serwera Proxy aplikacji), upewnij się użycie jednej z tych response_modes, przed utworzeniem Nowa aplikacja.  

---
 
## <a name="april-2018"></a>Kwiecień 2018 r. 

### <a name="azure-ad-b2c-access-token-are-ga"></a>Usługa Azure AD B2C Token dostępu są ogólnie dostępne

**Wprowadź** Nowa funkcja  
**Kategoria usługi:** B2C — Consumer Identity Management  
**Możliwość produktu:** B2B/B2C 

Teraz możesz uzyskać dostęp do interfejsów API sieci Web zabezpieczony za pomocą usługi Azure AD B2C przy użyciu tokenów dostępu. Ta funkcja jest przenoszona z publicznej wersji zapoznawczej do GA. Udoskonalono środowisko interfejsu użytkownika do konfigurowania aplikacji usługi Azure AD B2C i interfejsów API sieci web i wprowadzono drobne ulepszenia.
 
Aby uzyskać więcej informacji, [Zobacz Azure AD B2C: Żądanie tokenów](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-access-tokens)dostępu.

---

### <a name="test-single-sign-on-configuration-for-saml-based-applications"></a>Testowanie konfiguracji pojedynczego logowania jednokrotnego dla aplikacji opartej na SAML

**Wprowadź** Nowa funkcja  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwość produktu:** SSO

Konfigurowanie aplikacji opartej na SAML logowania jednokrotnego, możesz przetestować integrację na stronie konfiguracji. Jeśli wystąpi błąd podczas logowania, możesz dostarczyć błąd w środowisku testowania, a usługa Azure AD zapewnia czynności rozwiązujących problem do rozwiązania konkretnego problemu.

Aby uzyskać więcej informacji, zobacz:

- [Konfigurowanie logowania jednokrotnego do aplikacji, które nie znajdują się w galerii aplikacji Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-custom-apps)
- [Jak debugować opartej na SAML logowania jednokrotnego do aplikacji w usłudze Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-debugging)

---
 
### <a name="azure-ad-terms-of-use-now-has-per-user-reporting"></a>Warunki użytkowania usługi Azure AD mają teraz raportowanie na użytkownika

**Wprowadź** Nowa funkcja  
**Kategoria usługi:** Warunki użytkowania  
**Możliwość produktu:** Zgodność
 
Administratorzy mogą teraz wybrać danego warunków użytkowania i wyświetlić wszystkich użytkowników, które wyrażono zgodę na czy warunki użytkowania i co daty/godziny go miało miejsce.

Aby uzyskać więcej informacji, zobacz [są wyposażone w usłudze Azure AD z warunkami użytkowania](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use).

---
 
### <a name="azure-ad-connect-health-risky-ip-for-ad-fs-extranet-lockout-protection"></a>Azure AD Connect Health: Ryzykowny adres IP dla AD FS ochrony blokad w sieci ekstranet 

**Wprowadź** Nowa funkcja  
**Kategoria usługi:** Inne  
**Możliwość produktu:** Monitorowanie i raportowanie

Teraz obsługuje możliwość wykrywania adresów IP adresy, która przekracza próg nieudanych logowań U/P na podstawie godzinową lub dzienną programu Connect Health. Dostępne są następujące funkcje oferowane przez tę funkcję:

- Kompleksowy raport przedstawiający adres IP i liczbę nieudanych prób logowania generowane na podstawie godziny/dnia z progiem można dostosowywać.
- Alerty bazujące na poczcie e-mail, gdy określony adres IP został przekroczony próg nieudanych logowań U/P na podstawie godziny/dnia.
- Opcja pobierania w celu przeprowadzenia szczegółowej analizy danych

Aby uzyskać więcej informacji, zobacz [raport ryzykownych adresów IP](https://aka.ms/aadchriskyip).

---
 
### <a name="easy-app-config-with-metadata-file-or-url"></a>Konfiguracja aplikacji łatwo z pliku metadanych lub adres URL

**Wprowadź** Nowa funkcja  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwość produktu:** SSO

Na stronie aplikacje przedsiębiorstwa administratorzy przekazać plik metadanych SAML do skonfigurowania logowania jednokrotnego SAML na podstawie aplikacji usługi AAD i spoza galerii.

Ponadto umożliwia adres URL metadanych Federacji aplikacji usługi Azure AD Konfigurowanie logowania jednokrotnego przy użyciu aplikacji docelowej.

Aby uzyskać więcej informacji, zobacz [Konfigurowanie logowania jednokrotnego do aplikacji, które nie znajdują się w galerii aplikacji usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-custom-apps).

---

### <a name="azure-ad-terms-of-use-now-generally-available"></a>Usługa Azure AD z warunkami użytkowania jest teraz ogólnie dostępna

**Wprowadź** Nowa funkcja  
**Kategoria usługi:** Warunki użytkowania  
**Możliwość produktu:** Zgodność
 

Warunki użytkowania usługi Azure AD zostały przeniesione z publicznej wersji zapoznawczej do ogólnie dostępnej.

Aby uzyskać więcej informacji, zobacz [są wyposażone w usłudze Azure AD z warunkami użytkowania](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use).

---

### <a name="allow-or-block-invitations-to-b2b-users-from-specific-organizations"></a>Zezwalaj na zaproszenia lub blokowanie ich dla użytkowników B2B z określonym organizacjom

**Wprowadź** Nowa funkcja  
**Kategoria usługi:** B2B  
**Możliwość produktu:** B2B/B2C
 

Teraz można określić organizacji partnerskich, który ma być udostępnianie i współpraca przy użyciu we współpracy B2B usługi Azure AD. Aby to zrobić, możesz utworzyć listę konkretnych dozwolonych lub zablokowanych domen. Gdy domeny jest zablokowane, za pomocą tych funkcji, pracownikom nie może wysyłać zaproszenia do osób w danej domenie.

Dzięki temu można kontrolować dostęp do zasobów, umożliwiając bezproblemowe środowisko dla zatwierdzonych użytkowników jednocześnie.

Ta funkcja współpracy B2B jest dostępna dla wszystkich Azure Active Directory klientów i może być używana w połączeniu z funkcjami Azure AD — wersja Premium, takimi jak dostęp warunkowy i Ochrona tożsamości, aby uzyskać bardziej szczegółową kontrolę nad tym, kiedy i jak zewnętrznym użytkownikom biznesowym jest znak w programie i uzyskać dostęp.

Aby uzyskać więcej informacji, zobacz [dozwolonych lub zablokowanych zaproszeń do użytkowników B2B z określonym organizacjom](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-allow-deny-list).

---
 
### <a name="new-federated-apps-available-in-azure-ad-app-gallery"></a>Nowe aplikacje federacyjne dostępne w galerii aplikacji usługi Azure AD

**Wprowadź** Nowa funkcja  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwość produktu:** Integracje innych firm

W kwietniu 2018 Dodaliśmy obsługę tych 13 nowe aplikacje z Federacją do galerii aplikacji:

Kryterium HCM, [FiscalNote](https://docs.microsoft.com/azure/active-directory/active-directory-saas-fiscalnote-tutorial), [wpis tajny serwera (lokalnie)](https://docs.microsoft.com/azure/active-directory/active-directory-saas-secretserver-on-premises-tutorial), [sygnałów dynamicznych](https://docs.microsoft.com/azure/active-directory/active-directory-saas-dynamicsignal-tutorial), [mindWireless](https://docs.microsoft.com/azure/active-directory/active-directory-saas-mindwireless-tutorial), [schematu organizacyjnego Teraz](https://docs.microsoft.com/azure/active-directory/active-directory-saas-orgchartnow-tutorial), [Ziflow](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ziflow-tutorial), [monitora wydajności AppNeta](https://docs.microsoft.com/azure/active-directory/active-directory-saas-appneta-tutorial), [Elium](https://docs.microsoft.com/azure/active-directory/active-directory-saas-elium-tutorial) , [Fluxx Labs](https://docs.microsoft.com/azure/active-directory/active-directory-saas-fluxxlabs-tutorial), [ Cisco, chmura](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ciscocloud-tutorial), półki, [SafetyNet](https://docs.microsoft.com/azure/active-directory/active-directory-saas-safetynet-tutorial)

Aby uzyskać więcej informacji o aplikacjach, zobacz [integracji aplikacji SaaS w usłudze Azure Active Directory](https://aka.ms/appstutorial).

Aby uzyskać więcej informacji o ofercie swoją aplikację w galerii aplikacji Azure AD, zobacz [umieść swoją aplikację w galerii aplikacji usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing).

---
 
### <a name="grant-b2b-users-in-azure-ad-access-to-your-on-premises-applications-public-preview"></a>Użytkowników B2B przydział w usłudze Azure AD dostęp do aplikacji w środowisku lokalnym (publiczna wersja zapoznawcza)

**Wprowadź** Nowa funkcja  
**Kategoria usługi:** B2B  
**Możliwość produktu:** B2B/B2C

Jako organizacja, która używa funkcji współpracy B2B usługi Azure Active Directory (Azure AD), aby zaprosić użytkowników-gości z organizacji partnerskich do usługi Azure AD teraz możesz podać tych użytkowników B2B dostępu do aplikacji lokalnych. Te aplikacje w środowisku lokalnym za pomocą uwierzytelnianie oparte na protokole SAML lub zintegrowanego Windows Authentication (Zintegrowane) ograniczonego delegowania protokołu Kerberos (KCD).

Aby uzyskać więcej informacji, zobacz [użytkowników B2B przydział w usłudze Azure AD dostęp do aplikacji w środowisku lokalnym](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-hybrid-cloud-to-on-premises).

---
 
### <a name="get-sso-integration-tutorials-from-the-azure-marketplace"></a>Pobierz samouczki integracji logowania jednokrotnego w witrynie Azure Marketplace

**Wprowadź** Zmieniona funkcja  
**Kategoria usługi:** Inne  
**Możliwość produktu:** Integracje innych firm

Jeśli aplikacja, która znajduje się w [portalu Azure marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps?page=1) obsługuje logowanie jednokrotne, klikając na podstawie protokołu SAML **Pobierz teraz** zapewnia samouczkiem integracji skojarzone z tej aplikacji. 

---

### <a name="faster-performance-of-azure-ad-automatic-user-provisioning-to-saas-applications"></a>Szybsza wydajność usługi Azure AD automatyczna aprowizacja użytkowników do aplikacji SaaS

**Wprowadź** Zmieniona funkcja  
**Kategoria usługi:** Aprowizowanie aplikacji  
**Możliwość produktu:** Integracje innych firm
 
Wcześniej klienci korzystający z przypisywania łączników aplikacji SaaS (na przykład usługi Salesforce, ServiceNow i pole) użytkowników usługi Azure Active Directory mogą wystąpić spadek wydajności w przypadku ich dzierżawami usługi Azure AD zawiera ponad 100 000 użytkowników połączonych i grupy i ich były używane przypisań użytkowników i grup użytkowników, którzy powinny być aprowizowany.

2 kwietnia 2018 r. istotnie poprawiającą wydajność ulepszenia zostały wdrożone do inicjowania obsługi usługi Azure AD, która znacznie skrócić czas potrzebny do wykonania początkowej synchronizacji od usługi Azure Active Directory i aplikacji SaaS docelowej.

W rezultacie wielu klientów, miały synchronizacji początkowej do aplikacji, które miały liczbę dni lub nigdy nie zakończona, teraz są kończone w ciągu kilku minut lub godzin.

Aby uzyskać więcej informacji, zobacz [co się dzieje podczas inicjowania obsługi administracyjnej?](https://docs.microsoft.com/azure/active-directory/active-directory-saas-app-provisioning#what-happens-during-provisioning)

---

### <a name="self-service-password-reset-from-windows-10-lock-screen-for-hybrid-azure-ad-joined-machines"></a>Samodzielnie resetując hasło z ekranu blokady systemu Windows 10 do hybrydowej usługi Azure AD przyłączone maszyn

**Wprowadź** Zmieniona funkcja  
**Kategoria usługi:** Samodzielne resetowanie hasła  
**Możliwość produktu:** Uwierzytelnianie użytkownika
 
Zaktualizowaliśmy obejmuje pomocy technicznej dla maszyn, które są dołączone do usługi Azure AD hybrydowej funkcji samoobsługowego resetowania HASEŁ systemu Windows 10. Ta funkcja jest dostępna w systemie Windows 10 RS4 umożliwia użytkownikom do zresetowania swojego hasła na ekranie blokady komputera systemu Windows 10. Użytkownicy, którzy są włączone i zarejestrowany do samoobsługowego resetowania haseł mogą korzystać z tej funkcji.

Aby uzyskać więcej informacji, zobacz [haseł usługi Azure AD z ekranu logowania](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-sspr-windows).

---

## <a name="march-2018"></a>Marca 2018 r.
 
### <a name="certificate-expire-notification"></a>Certyfikat wygasa powiadomień

**Wprowadź** Ustalone  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwość produktu:** SSO
 
Usługa Azure AD wysyła powiadomienie, gdy certyfikat dla galerii lub aplikacji spoza galerii, wkrótce wygaśnie. 

Niektórzy użytkownicy nie otrzymał powiadomienia aplikacji dla przedsiębiorstw skonfigurowane dla opartej na SAML logowania jednokrotnego. Ten problem został rozwiązany. Usługa Azure AD wysyła powiadomienie w przypadku certyfikatów wygaśnie za 7, 30 i 60 dni. Jesteś w stanie wyświetlić to zdarzenie w dziennikach inspekcji. 

Aby uzyskać więcej informacji, zobacz:

- [Zarządzanie certyfikatami dla federacyjnego logowania jednokrotnego w usłudze Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-sso-certs)
- [Raporty dotyczące inspekcji w portalu Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-audit-logs)
 
---
 
### <a name="twitter-and-github-identity-providers-in-azure-ad-b2c"></a>Usługi Twitter i GitHub dostawców tożsamości w usłudze Azure AD B2C

**Wprowadź** Nowa funkcja  
**Kategoria usługi:** B2C — Consumer Identity Management  
**Możliwość produktu:** B2B/B2C
 
Możesz teraz dodać Twitter lub GitHub jako dostawcy tożsamości w usłudze Azure AD B2C. Twitter jest przenoszona z publicznej wersji zapoznawczej do ogólnego udostępnienia. GitHub został wydany w publicznej wersji zapoznawczej.

Aby uzyskać więcej informacji, zobacz [czym jest współpraca B2B w usłudze Azure AD?](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b).
 
---

### <a name="restrict-browser-access-using-intune-managed-browser-with-azure-ad-application-based-conditional-access-for-ios-and-android"></a>Ograniczanie dostępu do przeglądarki przy użyciu Intune Managed Browser z dostępem warunkowym opartym na aplikacji w usłudze Azure AD dla systemów iOS i Android

**Wprowadź** Nowa funkcja  
**Kategoria usługi:** Dostęp warunkowy  
**Możliwość produktu:** Ochrona i zabezpieczanie tożsamości
 
**Teraz w publicznej wersji zapoznawczej!**

**Intune Managed Browser Logowanie jednokrotne:** Pracownicy mogą korzystać z logowania jednokrotnego na klientach natywnych (takich jak program Microsoft Outlook) i Intune Managed Browser dla wszystkich aplikacji połączonych z usługą Azure AD.

**Intune Managed Browser obsługa dostępu warunkowego:** Teraz można wymagać od pracowników korzystania z zarządzanej przeglądarki Intune przy użyciu zasad dostępu warunkowego opartego na aplikacji.

Dowiedz się więcej w naszym [wpis w blogu](https://cloudblogs.microsoft.com/enterprisemobility/2018/03/15/the-intune-managed-browser-now-supports-azure-ad-sso-and-conditional-access/).

Aby uzyskać więcej informacji, zobacz:

- [Konfigurowanie dostępu warunkowego opartego na aplikacji](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)

- [Konfigurowanie zasad programu managed browser](https://aka.ms/managedbrowser)  

---
 
### <a name="app-proxy-cmdlets-in-powershell-ga-module"></a>Polecenia cmdlet serwera Proxy aplikacji, w Module programu Powershell w wersji Ogólnodostępnej

**Wprowadź** Nowa funkcja  
**Kategoria usługi:** Serwer proxy aplikacji  
**Możliwość produktu:** Access Control
 
Obsługa polecenia cmdlet serwera Proxy aplikacji jest teraz moduł programu Powershell w wersji ogólnie dostępnej. Wymagane jest aktualne informacje o programie Powershell modułów — Jeżeli zostanie więcej niż roku za zaporą, niektóre polecenia cmdlet mogą przestać działać. 

Aby uzyskać więcej informacji, zobacz [AzureAD](https://docs.microsoft.com/powershell/module/Azuread/?view=azureadps-2.0).
 
---
 
### <a name="office-365-native-clients-are-supported-by-seamless-sso-using-a-non-interactive-protocol"></a>Natywne klienci usługi Office 365 są obsługiwane przez bezproblemowe logowanie Jednokrotne przy użyciu protokołu nieinterakcyjnym

**Wprowadź** Nowa funkcja  
**Kategoria usługi:** Uwierzytelnienia (logowania)  
**Możliwość produktu:** Uwierzytelnianie użytkownika
 
Użytkownika przy użyciu natywnych klientów usługi Office 365 (wersja 16.0.8730.xxxx i nowsze wersje) Pobierz dyskretnej środowisko logowania jednokrotnego przy użyciu bezproblemowego logowania jednokrotnego. Te są obsługiwane przez dodanie nieinterakcyjnych protokołu (WS-Trust) do usługi Azure AD.

Aby uzyskać więcej informacji, zobacz [sposób logowania na komputerze klienckim natywne z pracą bezproblemowe logowanie Jednokrotne?](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso-how-it-works#how-does-sign-in-on-a-native-client-with-seamless-sso-work)
 
---

### <a name="users-get-a-silent-sign-on-experience-with-seamless-sso-if-an-application-sends-sign-in-requests-to-azure-ads-tenant-endpoints"></a>Użytkownicy uzyskują dyskretnej logowania jednokrotnego, za pomocą bezproblemowego logowania jednokrotnego, jeśli aplikacja przesyła żądania rejestrowania do punktów końcowych dzierżawy usługi Azure AD

**Wprowadź** Nowa funkcja  
**Kategoria usługi:** Uwierzytelnienia (logowania)  
**Możliwość produktu:** Uwierzytelnianie użytkownika
 
Użytkownicy uzyskują dyskretnej logowania jednokrotnego, za pomocą bezproblemowego logowania jednokrotnego, jeśli aplikacja (na przykład `https://contoso.sharepoint.com`) wysyła żądania rejestrowania do punktów końcowych z dzierżawy usługi Azure AD — oznacza to, `https://login.microsoftonline.com/contoso.com/<..>` lub `https://login.microsoftonline.com/<tenant_ID>/<..>` — zamiast wspólnego punktu końcowego usługi Azure AD (`https://login.microsoftonline.com/common/<...>`).

Aby uzyskać więcej informacji, zobacz [usługi Azure Active Directory bezproblemowe logowanie jednokrotne](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso). 

---
 
### <a name="need-to-add-only-one-azure-ad-url-instead-of-two-urls-previously-to-users-intranet-zone-settings-to-roll-out-seamless-sso"></a>Należy dodać tylko jeden adres URL usługi Azure AD, zamiast wcześniej dwa adresy URL do ustawienia strefy Intranet użytkowników to dystrybucję bezproblemowe logowanie Jednokrotne

**Wprowadź** Nowa funkcja  
**Kategoria usługi:** Uwierzytelnienia (logowania)  
**Możliwość produktu:** Uwierzytelnianie użytkownika
 
Aby rozwinąć bezproblemowe logowanie Jednokrotne dla użytkowników, należy dodać tylko jeden usługi Azure AD adres URL użytkowników w sieci Intranet ustawienia strefy za pomocą zasad grupy w usłudze Active Directory: `https://autologon.microsoftazuread-sso.com`. Wcześniej klienci musieli dodać dwa adresy URL.

Aby uzyskać więcej informacji, zobacz [usługi Azure Active Directory bezproblemowe logowanie jednokrotne](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso). 
 
---
 
### <a name="new-federated-apps-available-in-azure-ad-app-gallery"></a>Nowe aplikacje federacyjne dostępne w galerii aplikacji usługi Azure AD

**Wprowadź** Nowa funkcja  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwość produktu:** Integracje innych firm

W marcu 2018 r. Dodaliśmy obsługę tych 15 nowe aplikacje z Federacją do galerii aplikacji:

[Boxcryptor](https://docs.microsoft.com/azure/active-directory/active-directory-saas-boxcryptor-tutorial), [CylancePROTECT](https://docs.microsoft.com/azure/active-directory/active-directory-saas-cylanceprotect-tutorial), Wrike, [SignalFx](https://docs.microsoft.com/azure/active-directory/active-directory-saas-signalfx-tutorial), Asystenta przez FirstAgenda, [YardiOne](https://docs.microsoft.com/azure/active-directory/active-directory-saas-yardione-tutorial), Vtiger CRM, inwink, [amplitudy](https://docs.microsoft.com/azure/active-directory/active-directory-saas-amplitude-tutorial), [Spacio](https://docs.microsoft.com/azure/active-directory/active-directory-saas-spacio-tutorial), [ContractWorks](https://docs.microsoft.com/azure/active-directory/active-directory-saas-contractworks-tutorial), [Bersin](https://docs.microsoft.com/azure/active-directory/active-directory-saas-bersin-tutorial), [Mercell](https://docs.microsoft.com/azure/active-directory/active-directory-saas-mercell-tutorial), [Trisotech cyfrowy Enterprise Server](https://docs.microsoft.com/azure/active-directory/active-directory-saas-trisotechdigitalenterpriseserver-tutorial), [chmury Qumu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-qumucloud-tutorial).
 
Aby uzyskać więcej informacji o aplikacjach, zobacz [integracji aplikacji SaaS w usłudze Azure Active Directory](https://aka.ms/appstutorial).

Aby uzyskać więcej informacji o ofercie swoją aplikację w galerii aplikacji Azure AD, zobacz [umieść swoją aplikację w galerii aplikacji usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing). 

---
 
### <a name="pim-for-azure-resources-is-generally-available"></a>Usługa PIM dla zasobów platformy Azure jest ogólnie dostępna

**Wprowadź** Nowa funkcja  
**Kategoria usługi:** Privileged Identity Management  
**Możliwość produktu:** Privileged Identity Management
 
Korzystania z usługi Azure AD Privileged Identity Management dla ról w katalogu teraz umożliwia czasowo dostęp i możliwości przypisania w usłudze PIM dla ról zasobów platformy Azure, takich jak subskrypcji, grupy zasobów, maszyny wirtualne i innych zasobów, obsługiwane przez usługi Azure Resource Manager. Wymuszanie uwierzytelniania wieloskładnikowego podczas aktywacji ról Just-In-Time i Planuj aktywacje w połączeniu z systemem windows zatwierdzone zmiany. Ponadto w tej wersji dodano ulepszenia nie jest dostępna w publicznej wersji zapoznawczej, w tym zaktualizowanego interfejsu użytkownika, przepływy pracy zatwierdzania i możliwość rozszerzania role, które wkrótce wygasną i Odnów wygasłe role.

Aby uzyskać więcej informacji, zobacz [PIM dla zasobów platformy Azure (wersja zapoznawcza)](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/azure-pim-resource-rbac)
 
---
 
### <a name="adding-optional-claims-to-your-apps-tokens-public-preview"></a>Dodawanie opcjonalnych oświadczeń do tokenów aplikacji (publiczna wersja zapoznawcza)

**Wprowadź** Nowa funkcja  
**Kategoria usługi:** Uwierzytelnienia (logowania)  
**Możliwość produktu:** Uwierzytelnianie użytkownika
 
Aplikacja usługi Azure AD mogą teraz oświadczenia żądań, niestandardowych lub opcjonalnie tokenów Jwt lub SAML tokenów.  To są oświadczenia dotyczące użytkownika lub dzierżawy, które nie są uwzględnione w tokenie, z powodu ograniczeń rozmiaru lub stosowania domyślnie.  Ta funkcja jest obecnie w publicznej wersji zapoznawczej dla aplikacji usługi Azure AD w wersji 1.0 i 2.0 punktach końcowych.  Zobacz dokumentację, aby uzyskać informacje na oświadczeniach, jakie można dodać i jak edytować manifest aplikacji do ich zażądać.  

Aby uzyskać więcej informacji, zobacz [opcjonalnie oświadczeń w usłudze Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-optional-claims).
 
---
 
### <a name="azure-ad-supports-pkce-for-more-secure-oauth-flows"></a>Usługa Azure AD obsługuje PKCE bardziej bezpieczne przepływy OAuth

**Wprowadź** Nowa funkcja  
**Kategoria usługi:** Uwierzytelnienia (logowania)  
**Możliwość produktu:** Uwierzytelnianie użytkownika
 
Dokumentacja usługi Azure AD zostały zaktualizowane należy pamiętać, obsługa PKCE, który pozwala na bardziej bezpiecznej komunikacji w przepływie przyznawania kodu autoryzacji usługi uwierzytelniania OAuth 2.0.  Code_challenges S256 i w postaci zwykłego tekstu są obsługiwane dla punktów końcowych w wersji 1.0 i 2.0. 

Aby uzyskać więcej informacji, zobacz [żądania kodu autoryzacji](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-protocols-oauth-code#request-an-authorization-code). 
 
---
 
### <a name="support-for-provisioning-all-user-attribute-values-available-in-the-workday-get_workers-api"></a>Obsługa funkcji obsługi administracyjnej wszystkie wartości atrybutów użytkownika dostępny w interfejsie API Get_Workers Workday

**Wprowadź** Nowa funkcja  
**Kategoria usługi:** Aprowizowanie aplikacji  
**Możliwość produktu:** Integracje innych firm
 
Publiczna wersja zapoznawcza aprowizacji dla ruchu przychodzącego z produktu Workday do usługi Active Directory i usługi Azure AD obecnie obsługuje możliwość wyodrębniania i aprowizacji w interfejsie API Get_Workers Workday wszystkie wartości atrybutów. Spowoduje to dodanie obsługuje setek dodatkowe standardowe i atrybutów niestandardowych, które są dostarczane z wstępną wersję produktu Workday wykraczających dla ruchu przychodzącego inicjowania obsługi administracyjnej łącznika.

Aby uzyskać więcej informacji, zobacz: [Dostosowywanie listy atrybutów użytkownika produktu Workday](https://docs.microsoft.com/azure/active-directory/active-directory-saas-workday-inbound-tutorial#customizing-the-list-of-workday-user-attributes)

---

### <a name="changing-group-membership-from-dynamic-to-static-and-vice-versa"></a>Zmiana członkostwa w grupie z dynamicznej na statyczną i na odwrót

**Wprowadź** Nowa funkcja  
**Kategoria usługi:** Zarządzanie grupami  
**Możliwość produktu:** Współpraca
 
Istnieje możliwość zmienić sposób zarządzania członkostwa w grupie. Jest to przydatne, gdy chcesz zachować taką samą nazwę grupy i identyfikator w systemie, dzięki czemu wszystkie istniejące odwołania do tej grupy są nadal ważne. Tworzenie nowej grupy wymaga aktualizacji te odwołania.
Zaktualizowaliśmy Centrum administracyjnego usługi AD Azure, aby obsługiwać tę funkcję. Teraz klienci mogą przekształcać istniejące grupy dynamiczne członkostwo przypisane członkostwo i na odwrót. Nadal dostępne są również istniejące polecenia cmdlet programu PowerShell.

Aby uzyskać więcej informacji, zobacz [dynamiczne reguły członkostwa dla grup w Azure Active Directory](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership)

---

### <a name="improved-sign-out-behavior-with-seamless-sso"></a>Ulepszone zachowanie wylogowania z bezproblemowe logowanie Jednokrotne

**Wprowadź** Zmieniona funkcja  
**Kategoria usługi:** Uwierzytelnienia (logowania)  
**Możliwość produktu:** Uwierzytelnianie użytkownika
 
Wcześniej nawet wtedy, gdy użytkownicy wylogowanie jawnie aplikacji zabezpieczonej przez usługi Azure AD, ich będzie automatycznie zalogować się ponownie przy użyciu bezproblemowe logowanie Jednokrotne gdy one próbował uzyskać dostęp do aplikacji usługi Azure AD, ponownie w ramach ich sieci corpnet za pomocą ich urządzeń przyłączonych do domeny. Dzięki tej zmianie Wyloguj jest obsługiwane.  Dzięki temu użytkownicy mogą wybrać tej samej lub innej usługi Azure AD konto do zalogowania Wstecz, a nie są automatycznie zalogować się przy użyciu bezproblemowe logowanie Jednokrotne.

Aby uzyskać więcej informacji, zobacz [usługi Azure Active Directory bezproblemowe logowanie jednokrotne](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso)
 
---
 
### <a name="application-proxy-connector-version-154020-released"></a>Wersja łącznika serwera Proxy aplikacji 1.5.402.0 wydania

**Wprowadź** Zmieniona funkcja  
**Kategoria usługi:** Serwer proxy aplikacji  
**Możliwość produktu:** Ochrona i zabezpieczanie tożsamości
 
Ta wersja łącznika jest stopniowo wdrażana za pośrednictwem listopada. Nowa wersja łącznika obejmuje następujące zmiany:

- Łącznik teraz ustawia pliki cookie z poziomu domeny zamiast tego poziomu domeny podrzędnej. To zapewnia płynne logowania jednokrotnego i pozwala uniknąć monitów uwierzytelniania nadmiarowe.
- Obsługa żądań Kodowanie fragmentaryczne
- Monitorowanie kondycji łącznika ulepszone 
- Kilka poprawek błędów i ulepszenia w zakresie stabilności

Aby uzyskać więcej informacji, zobacz [łączników serwera Proxy aplikacji usługi AD Azure zrozumienie](https://docs.microsoft.com/azure/active-directory/application-proxy-understand-connectors).
 
---

## <a name="february-2018"></a>Lutego 2018 r.
 
### <a name="improved-navigation-for-managing-users-and-groups"></a>Ulepszona nawigacja do zarządzania użytkownikami i grupami

**Wprowadź** Plan zmiany  
**Kategoria usługi:** Zarządzanie katalogiem  
**Możliwość produktu:** Katalog

Usprawniono środowisko nawigacji do zarządzania użytkownikami i grupami. Możesz teraz przechodzić od omówienia directory bezpośrednio do listy wszystkich użytkowników, których łatwiejszy dostęp do listy usuniętych użytkowników. Możesz także przejść od omówienia directory bezpośrednio do listy wszystkich grup, łatwiejszy dostęp do grupy zarządzania. A także na stronie Przegląd w katalogu, możesz wyszukać użytkowników, grupy, aplikacja dla przedsiębiorstw lub rejestracja aplikacji. 

---

### <a name="availability-of-sign-ins-and-audit-reports-in-microsoft-azure-operated-by-21vianet-azure-china-21vianet"></a>Dostępność logowania i inspekcji raporty w systemie Microsoft Azure, obsługiwana przez firmę 21Vianet (Azure China 21Vianet)

**Wprowadź** Nowa funkcja  
**Kategoria usługi:** Azure Stack  
**Możliwość produktu:** Monitorowanie i raportowanie

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

**Wprowadź** Nowa funkcja  
**Kategoria usługi:** Raportowanie  
**Możliwość produktu:** Monitorowanie i raportowanie

Jako część opinie klientów, aby włączyć role bez uprawnień administratora na dostęp do działania usługi Azure AD dzienników, udostępniliśmy możliwość dla użytkowników, którzy znajdują się w roli "Czytnik raportów" dostęp do logowania i działania inspekcji w witrynie Azure portal, jak i za pomocą naszych interfejsów API programu Graph. 

Aby uzyskać więcej informacji jak używać tych raportów, zobacz [raportowania usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-azure-portal). 

---

### <a name="employeeid-claim-available-as-user-attribute-and-user-identifier"></a>Oświadczenie elementu EmployeeID dostępne jako atrybut użytkownika i identyfikator użytkownika

**Wprowadź** Nowa funkcja  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwość produktu:** SSO
 
Można skonfigurować **EmployeeID** jako identyfikator użytkownika i atrybut użytkownika dla użytkowników i gości B2B w SAML logowania jednokrotnego aplikacji w interfejsie użytkownika aplikacji przedsiębiorstwa.

Aby uzyskać więcej informacji, zobacz [Dostosowywanie oświadczeń wystawionych w tokenie SAML dla aplikacji dla przedsiębiorstw w usłudze Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization).

---

### <a name="simplified-application-management-using-wildcards-in-azure-ad-application-proxy"></a>Uproszczone zarządzanie aplikacjami przy użyciu symboli wieloznacznych na serwerze Proxy aplikacji usługi Azure AD

**Wprowadź** Nowa funkcja  
**Kategoria usługi:** Serwer proxy aplikacji  
**Możliwość produktu:** Uwierzytelnianie użytkownika
 
Aby ułatwić wdrożenie aplikacji i zmniejszyć swoje koszty administracyjne, możemy teraz obsługują możliwość publikowania aplikacji przy użyciu symboli wieloznacznych. Aby opublikować aplikację symboli wieloznacznych, postępuj zgodnie z przepływem publikowania aplikacji standardowej ale użyć symbolu wieloznacznego w adresach URL wewnętrznych i zewnętrznych.

Aby uzyskać więcej informacji, zobacz [aplikacje z symbolami wieloznacznymi serwera proxy aplikacji usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-wildcard)

---

### <a name="new-cmdlets-to-support-configuration-of-application-proxy"></a>Nowe polecenia cmdlet obsługujące konfigurację serwera Proxy aplikacji

**Wprowadź** Nowa funkcja  
**Kategoria usługi:** Serwer proxy aplikacji  
**Możliwość produktu:** Platforma

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

**Wprowadź** Nowa funkcja  
**Kategoria usługi:** Serwer proxy aplikacji  
**Możliwość produktu:** Platforma

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

**Wprowadź** Nowa funkcja  
**Kategoria usługi:** AD Sync  
**Możliwość produktu:** Platforma
 
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
 
### <a name="applications-supporting-intune-app-protection-policies-added-for-use-with-azure-ad-application-based-conditional-access"></a>Aplikacje obsługujące zasady Intune App Protection dodane do użycia z dostępem warunkowym opartym na aplikacji usługi Azure AD

**Wprowadź** Zmieniona funkcja  
**Kategoria usługi:** Dostęp warunkowy  
**Możliwość produktu:** Ochrona i zabezpieczanie tożsamości

Dodaliśmy więcej aplikacji obsługujących dostęp warunkowy oparty na aplikacji. Teraz można uzyskać dostęp do usługi Office 365 i inne aplikacje podłączone usługi AD platformy Azure w chmurze przy użyciu tych zatwierdzonych aplikacji klienckich.

Następujące aplikacje zostaną dodane do końca lutego:

- Microsoft Power BI

- Microsoft Launcher

- Microsoft Invoicing

Aby uzyskać więcej informacji, zobacz:

- [Wymaganie aplikacji zatwierdzone klienta](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#approved-client-app-requirement)
- [Dostęp warunkowy oparty na aplikacji usługi Azure AD](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)

---

### <a name="terms-of-use-update-to-mobile-experience"></a>Warunki użytkowania aktualizacji do środowiska mobilnego 

**Wprowadź** Zmieniona funkcja  
**Kategoria usługi:** Warunki użytkowania  
**Możliwość produktu:** Zgodność

Po wyświetleniu warunków użytkowania, możesz teraz kliknąć **masz problemy z wyświetlaniem? Kliknij tutaj,** . Kliknięcie tego linku spowoduje otworzenie warunków użytkowania natywnie na urządzeniu. Niezależnie od tego, czy rozmiar czcionki w dokumencie lub rozmiar ekranu urządzenia możesz powiększać i czytać dokument zgodnie z potrzebami. 

---
 
## <a name="january-2018"></a>Stycznia 2018 r.
 
### <a name="new-federated-apps-available-in-azure-ad-app-gallery"></a>Nowe aplikacje federacyjne dostępne w galerii aplikacji usługi Azure AD 

**Wprowadź** Nowa funkcja  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwość produktu:** Integracje innych firm

Stycznia 2018 r. zostały dodane następujące nowe aplikacje z obsługą federacji, w galerii aplikacji:

[IBM OpenPages](https://go.microsoft.com/fwlink/?linkid=864698), [oprogramowanie do zarządzania zachowania OneTrust](https://go.microsoft.com/fwlink/?linkid=861660), [Dealpath](https://go.microsoft.com/fwlink/?linkid=863526), [IriusRisk federacyjnych katalogu i [NetBenefits wierności](https://go.microsoft.com/fwlink/?linkid=864701).

Aby uzyskać więcej informacji o aplikacjach, zobacz [integracji aplikacji SaaS w usłudze Azure Active Directory](https://aka.ms/appstutorial).

Aby uzyskać więcej informacji o ofercie swoją aplikację w galerii aplikacji Azure AD, zobacz [umieść swoją aplikację w galerii aplikacji usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing). 

---
 
### <a name="sign-in-with-additional-risk-detected"></a>Zaloguj się przy użyciu dodatkowych wykryto ryzyko

**Wprowadź** Nowa funkcja  
**Kategoria usługi:** Identity Protection  
**Możliwość produktu:** Ochrona i zabezpieczanie tożsamości

Szczegółowe informacje, uzyskasz na zdarzenie wykryte zagrożenie jest powiązany ze swoją subskrypcją usługi Azure AD. Za pomocą wersji Azure AD Premium P2 otrzymasz najbardziej szczegółowe informacje na temat wszystkie wykrycia bazowego.

Z wersją programu Azure AD Premium P1 wykrywania, które nie są objęte licencja są wyświetlane jako zdarzenie o podwyższonym ryzyku logowania z dodatkowym ryzykiem wykryto.

Aby uzyskać więcej informacji, zobacz [Zdarzenia o podwyższonym ryzyku w usłudze Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-risk-events).
 
---

### <a name="hide-office-365-applications-from-end-users-access-panels"></a>Ukryj aplikacje usługi Office 365 w panelach dostępu użytkownika końcowego

**Wprowadź** Nowa funkcja  
**Kategoria usługi:** Moje aplikacje  
**Możliwość produktu:** SSO

Możesz teraz lepiej zarządzać jak aplikacje usługi Office 365 widoczne na panelach dostępu użytkowników za pomocą nowego ustawienia użytkownika. Ta opcja jest przydatne w przypadku zmniejszenie liczby aplikacji w panelach dostępu użytkownika, jeśli chcesz tylko wyświetlić aplikacje pakietu Office w portalu usługi Office. To ustawienie znajduje się w **ustawienia użytkownika** i jest oznaczona, **użytkownicy widzą tylko aplikacje usługi Office 365 w portalu usługi Office 365**.

Aby uzyskać więcej informacji, zobacz [ukrywanie aplikacji w środowisku użytkownika usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-hide-third-party-app).

---
 
### <a name="seamless-sign-into-apps-enabled-for-password-sso-directly-from-apps-url"></a>Bezproblemowe logowania do aplikacji objętych hasło logowania jednokrotnego, bezpośrednio z adres URL aplikacji 

**Wprowadź** Nowa funkcja  
**Kategoria usługi:** Moje aplikacje  
**Możliwość produktu:** SSO

Moje aplikacje rozszerzenia przeglądarki jest teraz dostępna za pośrednictwem wygodne narzędzie, które zapewnia pojedynczy znak Moje aplikacje na możliwość jako skrót w przeglądarce. Po zainstalowaniu przez użytkownika zostanie wyświetlona ikona typu "gofr" w przeglądarce, który zapewnia im szybki dostęp do aplikacji. Użytkownicy mogą korzystać z zalet:

- Możliwość bezpośrednio zalogować się do logowania jednokrotnego hasła opartego na aplikacji z poziomu strony logowania w aplikacji
- Uruchamianie dowolnej aplikacji przy użyciu funkcji szybkiego wyszukiwania
- Skróty do ostatnio używane aplikacje z rozszerzenia
- Rozszerzenie jest dostępne dla przeglądarki Microsoft Edge, Chrome i Firefox.
 
Aby uzyskać więcej informacji, zobacz [Moje zabezpieczenia aplikacji logowania rozszerzenia](../user-help/my-apps-portal-end-user-access.md#download-and-install-the-my-apps-secure-sign-in-extension).

---

### <a name="azure-ad-administration-experience-in-azure-classic-portal-has-been-retired"></a>Administrowanie usługami AD systemu Azure środowisko w klasycznym portalu Azure został wycofany.

**Wprowadź** Przestarzałe   
**Kategoria usługi:** Azure AD  
**Możliwość produktu:** Katalog

Od 8 stycznia 2018 r. administracji usługi Azure AD został wycofany doświadczenie w klasycznej witrynie Azure portal. Odbyło się w połączeniu z procedury wycofywania w klasycznym portalu Azure, sam. W przyszłości, należy użyć [Centrum administracyjnego usługi Azure AD](https://aad.portal.azure.com) wszystkie swoje portal administracji opartej na usługi Azure AD.
 
---

### <a name="the-phonefactor-web-portal-has-been-retired"></a>W portalu sieci web PhoneFactor został wycofany.

**Wprowadź** Przestarzałe  
**Kategoria usługi:** Azure AD  
**Możliwość produktu:** Katalog
 
Od 8 stycznia 2018 r. został wycofany z portalu sieci web PhoneFactor. Ten portal został użyty do administrowania serwerem MFA, ale te funkcje zostały przeniesione do witryny Azure portal pod adresem portal.azure.com. 

Konfiguracja usługi MFA znajduje się w lokalizacji: **Serwer \> usługi MFA Azure Active Directory**
 
---
 
### <a name="deprecate-azure-ad-reports"></a>Wycofana raporty usługi Azure AD

**Wprowadź** Przestarzałe  
**Kategoria usługi:** Raportowanie  
**Możliwość produktu:** Zarządzanie cyklem życia tożsamości  


Ogólne udostępnienie nowej konsoli administrowanie usługą Azure Active Directory i nowe interfejsy API, które są teraz dostępne dla działań i zabezpieczeń, raportów, raportów interfejsów API w obszarze "/ reports" punktu końcowego została wycofana od końca 31 grudnia 2017 r.

**Co to jest dostępne?**

W ramach przejścia do nowej konsoli administracyjnej udostępniliśmy 2 nowe interfejsy API do pobierania dzienników aktywności usługi Azure AD. Nowy zestaw interfejsów API zapewnia bardziej zaawansowane filtrowanie i sortowanie funkcje oprócz bardziej rozbudowane kontroli i działań logowania. Wcześniej dostępne w raportach zabezpieczeń danych teraz są dostępne za pośrednictwem zdarzeń o podwyższonym ryzyku Identity Protection interfejsu API programu Microsoft Graph.

Aby uzyskać więcej informacji, zobacz:

- [Rozpoczynanie pracy z usługą Azure Active Directory, interfejsu API raportowania](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-getting-started-azure-portal)

- [Rozpoczynanie pracy z usługą Azure Active Directory Identity Protection i Microsoft Graph](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection-graph-getting-started)

---

## <a name="december-2017"></a>Grudnia 2017 r.

### <a name="terms-of-use-in-the-access-panel"></a>Warunki użytkowania w panelu dostępu

**Wprowadź** Nowa funkcja  
**Kategoria usługi:** Warunki użytkowania  
**Możliwość produktu:** Zgodność
 
Możesz teraz przejść do panelu dostępu i wyświetlanie warunków użytkowania, które zostały wcześniej zaakceptowane.

Wykonaj następujące kroki:

1. Przejdź do [portalu MyApps](https://myapps.microsoft.com)i zaloguj się.

2. W prawym górnym rogu wybierz swoją nazwę, a następnie wybierz pozycję **profilu** z listy. 

3. Na Twojej **profilu**, wybierz opcję **Przejrzyj warunki użytkowania**. 

4. Teraz można przejrzeć warunki użytkowania akceptowane. 

Aby uzyskać więcej informacji, zobacz [warunki usługi Azure AD, użyj funkcji (wersja zapoznawcza)](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use).
 
---
 
### <a name="new-azure-ad-sign-in-experience"></a>Nowe środowisko logowania dla usługi Azure AD

**Wprowadź** Nowa funkcja  
**Kategoria usługi:** Azure AD  
**Możliwość produktu:** Uwierzytelnianie użytkowników
 
Usługi Azure AD i systemu tożsamości konta Microsoft UI zostały zaprojektowane od nowa, aby udzielić użytkownikom spójny wygląd i zachowanie. Ponadto strony logowania usługi Azure AD umożliwia zbieranie informacji o nazwę użytkownika najpierw następuje poświadczeń na drugim ekranie.

Aby uzyskać więcej informacji, zobacz [nowe środowisko logowania dla usługi Azure AD jest teraz w publicznej wersji zapoznawczej](https://cloudblogs.microsoft.com/enterprisemobility/2017/08/02/the-new-azure-ad-signin-experience-is-now-in-public-preview/).
 
---
 
### <a name="fewer-sign-in-prompts-a-new-keep-me-signed-in-experience-for-azure-ad-sign-in"></a>Mniejsza liczba wyświetlanych z logowaniem: Nowe środowisko "nie wylogowuj mnie" dla logowania za pomocą usługi Azure AD

**Wprowadź** Nowa funkcja  
**Kategoria usługi:** Azure AD  
**Możliwość produktu:** Uwierzytelnianie użytkowników
 
**Nie wylogowuj mnie** pole wyboru na stronie logowania w usłudze Azure AD został zastąpiony nowym wierszu, który pojawia się po pomyślnym uwierzytelnieniu. 

Jeśli odpowiesz **tak** do tego wiersza, usługa zapewnia token odświeżania trwałe. To zachowanie jest takie same jak w przypadku wybrania **nie wylogowuj mnie** pole wyboru w stare środowisko. W przypadku dzierżaw federacyjnych tego monitu pokazuje po pomyślnie przeprowadzić uwierzytelnienie przy użyciu usługi federacyjnej.

Aby uzyskać więcej informacji, [Zobacz artykuł mniejsze zalogowanie się: Nowe środowisko "nie wylogowuj mnie" usługi Azure AD jest w wersji zapoznawczej](https://cloudblogs.microsoft.com/enterprisemobility/2017/09/19/fewer-login-prompts-the-new-keep-me-signed-in-experience-for-azure-ad-is-in-preview/). 

---

### <a name="add-configuration-to-require-the-terms-of-use-to-be-expanded-prior-to-accepting"></a>Dodaj konfigurację, aby wymagać warunków użytkowania przed akceptacją rozszerzona

**Wprowadź** Nowa funkcja  
**Kategoria usługi:** Warunki użytkowania  
**Możliwość produktu:** Zgodność
 
Opcja dla administratorów wymaga użytkowników rozwinięcia warunków użytkowania przed akceptacją postanowień.

Wybierz opcję **na** lub **poza** aby wymagać od użytkowników rozwinięcia warunków użytkowania. **Na** ustawienie wymaga użytkownikom na wyświetlanie warunków użytkowania przed ich akceptacją.

Aby uzyskać więcej informacji, zobacz [warunki usługi Azure AD, użyj funkcji (wersja zapoznawcza)](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use).
 
---

### <a name="scoped-activation-for-eligible-role-assignments"></a>O określonym zakresie aktywacji dla kwalifikujących się przypisań ról

**Wprowadź** Nowa funkcja  
**Kategoria usługi:** Privileged Identity Management  
**Możliwość produktu:** Privileged Identity Management
 
Przypisania ról uprawnionych zasobów platformy Azure z mniej autonomię w zakresie od oryginalnej wartości domyślne przypisania aktywować, można użyć aktywacji o określonym zakresie. Przykładem jest, jeśli masz przypisaną jako właściciela subskrypcji w ramach dzierżawy. O określonym zakresie aktywacji można aktywować roli właściciela dla maksymalnie 5 zasobów znajdujących się w ramach subskrypcji (np. grupy zasobów i maszyn wirtualnych). Wyznaczanie zakresu proces aktywacji może zmniejszyć prawdopodobieństwo wykonywanie niepożądanych zmian do krytycznych zasobów platformy Azure.

Aby uzyskać więcej informacji, zobacz [co to jest usługa Azure AD Privileged Identity Management?](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure).
 
---
 
### <a name="new-federated-apps-in-the-azure-ad-app-gallery"></a>Nowe aplikacje federacyjne w galerii aplikacji usługi Azure AD

**Wprowadź** Nowa funkcja  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwość produktu:** Integracje innych firm

W grudniu 2017 r. Dodaliśmy obsługę tych aplikacji z Federacją do galerii aplikacji:

[Accredible](https://go.microsoft.com/fwlink/?linkid=863523), Adobe Experience Manager, [sklepu cyfrowego EFI](https://go.microsoft.com/fwlink/?linkid=861685), [Communifire](https://go.microsoft.com/fwlink/?linkid=861676) CybSafe, [FactSet](https://go.microsoft.com/fwlink/?linkid=863525), [OBRAZIE działa](https://go.microsoft.com/fwlink/?linkid=863517), [MOBI](https://go.microsoft.com/fwlink/?linkid=863521), [integracji usługi Azure AD MobileIron](https://go.microsoft.com/fwlink/?linkid=858027), [Reflektive](https://go.microsoft.com/fwlink/?linkid=863518), [logowania jednokrotnego SAML dla Bambus przez rozwiązania GmbH](https://go.microsoft.com/fwlink/?linkid=863520), [Logowania jednokrotnego SAML dla Bitbucket, rozpoznawanie GmbH](https://go.microsoft.com/fwlink/?linkid=863519), [Vodeclic](https://go.microsoft.com/fwlink/?linkid=863522), WebHR, integracji z usługą AD Zenegy platformy Azure.

Aby uzyskać więcej informacji o aplikacjach, zobacz [integracji aplikacji SaaS w usłudze Azure Active Directory](https://aka.ms/appstutorial).

Aby uzyskać więcej informacji o ofercie swoją aplikację w galerii aplikacji Azure AD, zobacz [umieść swoją aplikację w galerii aplikacji usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing). 
 
---

### <a name="approval-workflows-for-azure-ad-directory-roles"></a>Przepływy pracy zatwierdzania dla ról katalogu usługi Azure AD

**Wprowadź** Zmieniona funkcja  
**Kategoria usługi:** Privileged Identity Management  
**Możliwość produktu:** Privileged Identity Management
 
Przepływ pracy zatwierdzania dla ról katalogu usługi Azure AD jest ogólnie dostępna.

Za pomocą przepływu pracy zatwierdzania Administratorzy ról uprzywilejowanych może wymagać członków roli kwalifikujących się do żądania aktywacji roli, zanim rozpoczną korzystanie z ról uprzywilejowanych. Wielu użytkowników i grup mogą być delegowane zatwierdzenia obowiązki. Członkowie roli kwalifikujących się otrzymywanie powiadomień o zakończeniu zatwierdzenia i ich rolą jest aktywny.

---
 
### <a name="pass-through-authentication-skype-for-business-support"></a>Uwierzytelnianie przekazywane: Obsługa usługi Skype dla firm

**Wprowadź** Zmieniona funkcja  
**Kategoria usługi:** Uwierzytelnienia (logowania)  
**Możliwość produktu:** Uwierzytelnianie użytkowników

Teraz uwierzytelniania przekazywanego obsługuje logowania użytkowników do usługi Skype dla firm aplikacji klienckich, które obsługują nowoczesnego uwierzytelniania, w tym w trybie online i hybrydowe topologie. 

Aby uzyskać więcej informacji, zobacz [Skype dla firm topologie obsługiwane z nowoczesnym uwierzytelnianiem](https://technet.microsoft.com/library/mt803262.aspx).
 
---

### <a name="updates-to-azure-ad-privileged-identity-management-for-azure-rbac-preview"></a>Aktualizacje usługi Azure AD Privileged Identity Management dla RBAC platformy Azure (wersja zapoznawcza)

**Wprowadź** Zmieniona funkcja  
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

**Wprowadź** Plan zmiany  
**Kategoria usługi:** Usługa Access Control  
**Możliwość produktu:** Usługa Access Control 

Usługi Azure Active Directory Access Control (znany także jako usługa Access Control) zostanie wycofana w późnym 2018 r. Więcej informacji, który zawiera szczegółowy harmonogram i wskazówek dotyczących migracji wysokiego poziomu będą dostępne w ciągu kilku następnych tygodni. Na tej stronie można zamieszczać komentarze, pytania dotyczące usługi kontroli dostępu, a członek zespołu będzie odbierać je.

---

### <a name="restrict-browser-access-to-the-intune-managed-browser"></a>Ogranicz dostęp za pomocą przeglądarki do usługi Intune Managed Browser 

**Wprowadź** Plan zmiany  
**Kategoria usługi:** Dostęp warunkowy  
**Możliwość produktu:** Bezpieczeństwo i Ochrona tożsamości

Możesz ograniczyć dostęp przeglądarki do usługi Office 365 i innych aplikacji w chmurze połączone usługi AD systemu Azure przy użyciu aplikacji Intune Managed Browser jako zatwierdzonej aplikacji. 

Teraz można skonfigurować następujący warunek dla dostępu warunkowego opartego na aplikacji:

**Aplikacje klienckie:** Browser

**Jaki jest wpływ tej zmiany?**

Już dziś dostęp jest zablokowany, korzystając z tego warunku. Po udostępnieniu wersji zapoznawczej dostęp będzie wymagać korzystanie z aplikacji programu managed browser. 

Poszukaj tej funkcji i dodatkowe informacje w nadchodzących o naszych blogach i wersji. 

Aby uzyskać więcej informacji, zobacz [dostęp warunkowy w usłudze Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal).
 
---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Nowe zatwierdzone aplikacje klienckie dla dostępu warunkowego opartego na aplikacji usługi Azure AD

**Wprowadź** Plan zmiany  
**Kategoria usługi:** Dostęp warunkowy  
**Możliwość produktu:** Bezpieczeństwo i Ochrona tożsamości

Następujące aplikacje są na liście [zatwierdzonych aplikacji klienckich](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#approved-client-app-requirement):

- [Microsoft Kaizala](https://www.microsoft.com/garage/profiles/kaizala/)
- Microsoft StaffHub

Aby uzyskać więcej informacji, zobacz:

- [Wymaganie aplikacji zatwierdzone klienta](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#approved-client-app-requirement)
- [Dostęp warunkowy oparty na aplikacji usługi Azure AD](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)

---

### <a name="terms-of-use-support-for-multiple-languages"></a>Warunki użytkowania obsługują wiele języków

**Wprowadź** Nowa funkcja    
**Kategoria usługi:** Warunki użytkowania  
**Możliwość produktu:** Zgodność

Administratorzy mogą teraz utworzyć nowe warunki użytkowania, które zawierają wiele dokumentów PDF. Można oznaczyć tych dokumentów PDF z odpowiedniego języka. Użytkownicy otrzymują plik PDF z zgodny język, w oparciu o ich preferencje. W przypadku niezgodności, jest wyświetlany domyślny język.

---
 
### <a name="real-time-password-writeback-client-status"></a>Stan klienta zapisywania zwrotnego haseł w czasie rzeczywistym

**Wprowadź** Nowa funkcja  
**Kategoria usługi:** Samoobsługowe resetowanie hasła  
**Możliwość produktu:** Uwierzytelnianie użytkowników

Teraz można sprawdzić stan swojego klienta zapisywania zwrotnego haseł w środowisku lokalnym. Ta opcja jest dostępna w **integracji lokalnego** części [resetowania hasła](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/PasswordReset) strony. 

Jeśli występują problemy z połączeniem do środowiska lokalnego klienta zapisywania zwrotnego, zobaczysz komunikat o błędzie, który zapewnia:

- Informacje na temat Dlaczego nie można nawiązać połączenia z klientem zapisywania zwrotnego w środowisku lokalnym.
- Link do dokumentacji, która pomaga w rozwiązywaniu problemu. 

Aby uzyskać więcej informacji, zobacz [integracji lokalnego](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-how-it-works#on-premises-integration).

---

### <a name="azure-ad-app-based-conditional-access"></a>Dostęp warunkowy oparty na aplikacji usługi Azure AD 
 
**Wprowadź** Nowa funkcja  
**Kategoria usługi:** Azure AD  
**Możliwość produktu:** Bezpieczeństwo i Ochrona tożsamości

Teraz możesz ograniczyć dostęp do pakietu Office 365 i innych aplikacji w chmurze połączonych z usługą Azure AD do [zatwierdzonych aplikacji klienckich](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#approved-client-app-requirement) , które obsługują zasady ochrony aplikacji usługi Intune przy użyciu [dostępu warunkowego opartego na aplikacji w usłudze Azure AD](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access). Zasady ochrony aplikacji usługi Intune służą do konfigurowania i ochrony danych firmy na tych aplikacji klienckich.

Łącząc [aplikacje oparte](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access) na urządzeniach z zasadami dostępu warunkowego [opartymi na urządzeniu](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-policy-connected-applications) , można zapewnić ochronę danych na urządzeniach osobistych i firmowych.

Następujące warunki i kontrolki są teraz dostępne do użycia z dostępem warunkowym opartym na aplikacji:

**Warunek z obsługiwanych platform**

- iOS
- Android

**Stan aplikacji klienta**

- Aplikacje mobilne i klienci stacjonarni

**Kontrola dostępu**

- Wymagaj zatwierdzonej aplikacji klienckiej

Aby uzyskać więcej informacji, zobacz [dostęp warunkowy oparty na aplikacji usługi Azure AD](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access).
 
---

### <a name="manage-azure-ad-devices-in-the-azure-portal"></a>Zarządzanie urządzeniami usługi Azure AD w witrynie Azure portal

**Wprowadź** Nowa funkcja  
**Kategoria usługi:** Rejestrowanie urządzeń i zarządzanie nimi  
**Możliwość produktu:** Bezpieczeństwo i Ochrona tożsamości

Teraz można znaleźć wszystkie swoje urządzenia podłączone do usługi Azure AD i działań związanych z urządzenia w jednym miejscu. Istnieje nowe środowisko Administracja i Zarządzanie tożsamościami urządzeń i ustawienia w witrynie Azure portal. W tej wersji można wykonywać następujące czynności:

- Wyświetl wszystkie urządzenia, które są dostępne dla dostępu warunkowego w usłudze Azure AD.
- Wyświetlanie właściwości, które zawierają hybrydowe Azure urządzeń dołączonych do usługi AD.
- Znajdź klucze funkcji BitLocker dla urządzeń przyłączonych do usługi AD systemu Azure, zarządzanie urządzeniem za pomocą usługi Intune i nie tylko.
- Zarządzaj ustawienia dotyczące urządzeń usługi Azure AD.

Aby uzyskać więcej informacji, zobacz [zarządzania urządzeniami za pomocą witryny Azure portal](https://docs.microsoft.com/azure/active-directory/device-management-azure-portal).

---

### <a name="support-for-macos-as-a-device-platform-for-azure-ad-conditional-access"></a>Obsługa macOS jako platformy urządzeń dla dostępu warunkowego usługi Azure AD 

**Wprowadź** Nowa funkcja    
**Kategoria usługi:** Dostęp warunkowy  
**Możliwość produktu:** Bezpieczeństwo i Ochrona tożsamości 

Teraz można uwzględnić (lub wykluczyć) macOS jako warunek platformy urządzenia w zasadach dostępu warunkowego usługi Azure AD. Z dodatkiem z systemem macOS do obsługiwanych platform urządzeń możesz wykonywać następujące czynności:

- **Rejestrowanie i zarządzanie urządzeniami z systemem macOS przy użyciu usługi Intune.** Podobnie jak w innych platform, takich jak systemy iOS i Android, aplikację portalu firmy jest dostępna dla systemu macOS ujednoliconego rejestracji. Nowa aplikacja portal firmy dla systemu macOS można użyć do rejestrowania urządzenia w usłudze Intune i zarejestrowanie go za pomocą usługi Azure AD.
- **Upewnij się, że urządzenia z systemem macOS stosować się do zasad zgodności w organizacji, określone w usłudze Intune.** W usłudze Intune w witrynie Azure portal możesz teraz skonfigurować zasady zgodności dla urządzeń z systemem macOS. 
- **Ogranicz dostęp do aplikacji w usłudze Azure AD tylko urządzeń zgodnych z systemem macOS.** Tworzenie zasad dostępu warunkowego ma macOS jako osobną opcję platformy urządzeń. Teraz można tworzyć zasady dostępu warunkowego dla macOS dla zestawu aplikacji dla konkretnych platform na platformie Azure.

Aby uzyskać więcej informacji, zobacz:

- [Tworzenie zasad zgodności urządzeń dla urządzeń z systemem macOS w usłudze Intune](https://aka.ms/macoscompliancepolicy)
- [Dostęp warunkowy w usłudze Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal)
 
---

### <a name="network-policy-server-extension-for-azure-multi-factor-authentication"></a>Rozszerzenie serwera zasady sieciowe dla usługi Azure Multi-Factor Authentication 

**Wprowadź** Nowa funkcja    
**Kategoria usługi:**  Uwierzytelnianie wieloskładnikowe  
**Możliwość produktu:** Uwierzytelnianie użytkowników

Rozszerzenie serwera zasad sieciowych dla usługi Azure Multi-Factor Authentication dodaje funkcje oparte na chmurze Multi-Factor Authentication do infrastruktury uwierzytelniania przy użyciu istniejących serwerów. Z rozszerzeniem serwera zasad sieciowych można dodać połączenie telefoniczne, wiadomość SMS lub weryfikacji aplikacji telefonicznej do istniejącego przepływu uwierzytelniania. Nie trzeba instalować, konfigurować i obsługiwać nowe serwery. 

To rozszerzenie został utworzony dla organizacji, które chcesz chronić połączenia sieci VPN bez wdrażania serwera usługi Azure Multi-Factor Authentication. Serwer zasad sieciowych, których rozszerzenie działa jako karty między RADIUS i oparte na chmurze usługi Azure Multi-Factor Authentication, aby zapewnić drugi składnik uwierzytelniania federacyjnego lub zsynchronizowanych użytkowników.

Aby uzyskać więcej informacji, zobacz [integrowanie istniejącej infrastruktury serwera zasad sieciowych przy użyciu usługi Azure Multi-Factor Authentication](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-nps-extension).
 
---

### <a name="restore-or-permanently-remove-deleted-users"></a>Przywrócić lub usunąć trwale usunięto użytkowników

**Wprowadź** Nowa funkcja    
**Kategoria usługi:** Zarządzanie użytkownikami  
**Możliwość produktu:** Katalog 

W Centrum administracyjnym usługi Azure AD możesz teraz:

- Przywracanie usuniętego użytkownika. 
- Trwałe usuwanie użytkownika.

**Aby wypróbować tę funkcję:**

1. W Centrum administracyjnym usługi Azure AD, wybierz [wszyscy użytkownicy](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/All) w **Zarządzaj** sekcji. 

2. Z **Pokaż** listy wybierz **niedawno usuniętych użytkowników**. 

3. Wybierz co najmniej jeden niedawno usuniętych użytkowników i następnie przywrócić je lub trwale usunąć je.
 
---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Nowe zatwierdzone aplikacje klienckie dla dostępu warunkowego opartego na aplikacji usługi Azure AD
 
**Wprowadź** Zmieniona funkcja  
**Kategoria usługi:** Dostęp warunkowy  
**Możliwość produktu:** Bezpieczeństwo i Ochrona tożsamości

Następujące aplikacje zostały dodane do listy [zatwierdzonych aplikacji klienckich](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#approved-client-app-requirement):

- Microsoft Planner
- Azure Information Protection 

Aby uzyskać więcej informacji, zobacz:

- [Wymaganie aplikacji zatwierdzone klienta](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#approved-client-app-requirement)
- [Dostęp warunkowy oparty na aplikacji usługi Azure AD](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)

---

### <a name="use-or-between-controls-in-a-conditional-access-policy"></a>Używanie "OR" między kontrolkami w zasadach dostępu warunkowego 

**Wprowadź** Zmieniona funkcja    
**Kategoria usługi:** Dostęp warunkowy  
**Możliwość produktu:** Bezpieczeństwo i Ochrona tożsamości
 
Teraz można użyć opcji "OR" (wymaga jednej z wybranych kontrolek) do kontroli dostępu warunkowego. Ta funkcja służy do tworzenia zasad za pomocą "OR" między kontroli dostępu. Na przykład można użyć tej funkcji, aby utworzyć zasadę, która wymaga od użytkownika, zaloguj się przy użyciu usługi Multi-Factor Authentication "OR" włączenia zgodnego urządzenia.

Aby uzyskać więcej informacji, zobacz [mechanizmy kontroli dostępu warunkowego usługi Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-controls).
 
---

### <a name="aggregation-of-real-time-risk-events"></a>Agregacja zdarzeń o podwyższonym ryzyku w czasie rzeczywistym

**Wprowadź** Zmieniona funkcja    
**Kategoria usługi:** Ochrona tożsamości  
**Możliwość produktu:** Bezpieczeństwo i Ochrona tożsamości

W usłudze Azure AD Identity Protection wszystkie zdarzenia ryzyka w czasie rzeczywistym, które pochodzą z tego samego adresu IP w danym dniu teraz są agregowane dla każdego typu zdarzenia o podwyższonym ryzyku. Ta zmiana ogranicza ilość zdarzenia o podwyższonym ryzyku są wyświetlane bez zmiany zabezpieczeń użytkownika.

Podstawowe wykrywanie w czasie rzeczywistym działa w każdym razem, gdy użytkownik loguje się. Jeśli masz zasady zabezpieczeń ryzyka logowania równa uwierzytelniania wieloskładnikowego lub blokowanie dostępu, nadal jest wyzwalany podczas każdego ryzykowne logowania.
 
---
 
## <a name="october-2017"></a>Października 2017 r.

### <a name="deprecate-azure-ad-reports"></a>Wycofana raporty usługi Azure AD

**Wprowadź** Plan zmiany  
**Kategoria usługi:** Raportowanie  
**Możliwość produktu:** Zarządzanie cyklem życia tożsamości  

Azure portal udostępnia Ci:

- Nową konsolę administracyjną usługi Azure AD.
- Nowe interfejsy API dla raportów aktywności i zabezpieczeń.
 
Ze względu na te nowe możliwości raportu interfejsów API w ramach punktu końcowego/Reports zostały wycofane 10 grudnia 2017 r. 

---

### <a name="automatic-sign-in-field-detection"></a>Wykrywanie automatyczne pól logowania

**Wprowadź** Ustalone   
**Kategoria usługi:** Moje aplikacje  
**Możliwość produktu:** Logowanie jednokrotne  

Usługa Azure AD obsługuje wykrywania automatycznego pól logowania dla aplikacji, które są renderowane pola nazwy i hasła użytkownika HTML. Te kroki są udokumentowane w artykule [jak automatycznie Przechwyć pola logowania dla aplikacji](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-password-single-sign-on-non-gallery-applications-problems#manually-capture-sign-in-fields-for-an-app). Tej funkcji można znaleźć, dodając *spoza galerii* aplikacji **aplikacje dla przedsiębiorstw** strony w [witryny Azure portal](https://aad.portal.azure.com). Ponadto można skonfigurować **logowania jednokrotnego** tryb, w tym nową aplikację do **opartego na hasłach logowania jednokrotnego**, wprowadź adres URL sieci web, a następnie Zapisz strony.
 
Ze względu na problem z usługą zostało tymczasowo wyłączyć tej funkcji. Problem został rozwiązany i wykrywania automatycznego pól logowania będą ponownie dostępne.

---

### <a name="new-multi-factor-authentication-features"></a>Nowe funkcje usługi Multi-Factor Authentication

**Wprowadź** Nowa funkcja  
**Kategoria usługi:** Uwierzytelnianie wieloskładnikowe  
**Możliwość produktu:** Bezpieczeństwo i Ochrona tożsamości  

Uwierzytelnianie wieloskładnikowe (MFA) jest integralną część ochrony Twojej organizacji. Aby bardziej adaptacyjnego poświadczenia i doświadczenia Bezproblemowa, zostały dodane następujące funkcje: 

- Wyniki żądania usługi Multi-Factor Authentication są bezpośrednio zintegrowane usługi Azure AD logowania w raporcie, który obejmuje programowy dostęp do usługi MFA wyników.
- Konfiguracja uwierzytelniania Wieloskładnikowego głębiej jest zintegrowana w konfiguracji usługi Azure AD środowisko w witrynie Azure portal.

W tej publicznej wersji zapoznawczej zarządzania usługi MFA i raportowania są zintegrowaną częścią środowisko konfiguracji podstawowej usługi Azure AD. Możesz teraz zarządzać funkcji portalu zarządzania usługi MFA w środowisku usługi Azure AD.

Aby uzyskać więcej informacji, zobacz [dokumentacja dotycząca usługi MFA raportowania w witrynie Azure portal](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-sign-ins-mfa). 

---

### <a name="terms-of-use"></a>Warunki użytkowania

**Wprowadź** Nowa funkcja  
**Kategoria usługi:** Warunki użytkowania  
**Możliwość produktu:** Zgodność  

Możesz użyć usługi Azure AD warunki użytkowania przedstawiają informacje, takie jak zastrzeżenia istotne dla prawnych lub wymagań dotyczących zgodności dla użytkowników.

Warunki użytkowania usługi Azure AD można użyć w następujących scenariuszach:

- Ogólne warunki użytkowania dla wszystkich użytkowników w Twojej organizacji
- Specyficzne warunki użytkowania na podstawie atrybutów użytkownika (na przykład lekarzy a pielęgniarek) lub domowych, a pracownicy krajowi przez grupy dynamiczne
- Specyficzne warunki użytkowania do uzyskiwania dostępu do aplikacji biznesowych o dużym znaczeniu, takich jak Salesforce

Aby uzyskać więcej informacji, zobacz [użytkowania usługi Azure AD](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use).

---

### <a name="enhancements-to-privileged-identity-management"></a>Ulepszenia Privileged Identity Management

**Wprowadź** Nowa funkcja  
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

**Wprowadź** Nowa funkcja  
**Kategoria usługi:** Przeglądy dostępu  
**Możliwość produktu:** Zgodność  

Organizacje mogą używać przeglądów dostępu (wersja zapoznawcza) w celu efektywnego zarządzania członkostwa w grupach i dostępem do aplikacji dla przedsiębiorstw: 

- Przeprowadzając przeglądy dostępu, można ponownie certyfikować dostęp użytkowników-gości do aplikacji i ich członkostwo w grupach. Recenzenci mogą efektywnie decydować, czy gościom przedłużony dostępu na podstawie uzyskanych informacji dostarczonych przez przeglądów dostępu.
- Przeprowadzając przeglądy dostępu, można ponownie certyfikować dostęp do aplikacji i członkostwo w grupach pracowników.

Kontrole z przeglądem dostępu można łączyć w programy odpowiednie dla danej organizacji, aby śledzić przeglądy dla aplikacji dotyczących zgodności lub aplikacji narażonych na ryzyko.

Aby uzyskać więcej informacji, zobacz [przeglądy dostępu w usłudze Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-azure-ad-controls-access-reviews-overview).

---

### <a name="hide-third-party-applications-from-my-apps-and-the-office-365-app-launcher"></a>Ukrywanie aplikacji innych firm, które z moich aplikacji i uruchamianie aplikacji usługi Office 365

**Wprowadź** Nowa funkcja  
**Kategoria usługi:** Moje aplikacje  
**Możliwość produktu:** Logowanie jednokrotne  

Możesz teraz lepiej zarządzać aplikacje, które pojawiają się w portalach użytkowników za pośrednictwem nowego **Ukryj aplikacji** właściwości. Można ukryć aplikacji, aby ułatwić w przypadkach, w którym kafelków aplikacji widoczne dla usług zaplecza lub zduplikowanych fragmentów i przyciski użytkowników zaśmiecania aplikacji Uruchom. Przełącznik jest w **właściwości** części aplikacji innych firm i jest oznaczona etykietą **widoczne dla użytkownika?** Można również ukryć aplikację programistycznie za pomocą programu PowerShell. 

Aby uzyskać więcej informacji, zobacz [ukrywanie aplikacji innych firm, ze środowiska użytkownika w usłudze Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-hide-third-party-app). 


**Co to jest dostępne?**

 W ramach przejścia na Nowa Konsola administracyjna dwa nowe interfejsy API do pobierania działania usługi Azure AD dostępnych dzienników. Nowy zestaw interfejsów API zapewnia bardziej zaawansowane filtrowanie i sortowanie funkcje oprócz bardziej rozbudowane kontroli i działań logowania. API zdarzeń o podwyższonym ryzyku ochrony tożsamości w programie Microsoft Graph można uzyskać dostępu do danych, które były dostępne w raportach zabezpieczeń.


## <a name="september-2017"></a>Września 2017 r.

### <a name="hotfix-for-identity-manager"></a>Poprawka programu Identity Manager

**Wprowadź** Zmieniona funkcja  
**Kategoria usługi:** Menedżer tożsamości  
**Możliwość produktu:** Zarządzanie cyklem życia tożsamości  

Pakiet zbiorczy poprawek (kompilacja 4.4.1642.0) jest dostępny od 25 września 2017 r. dla dodatku Service Pack 1 dla programu Identity Manager 2016. Ten pakiet zbiorczy:

- Rozwiązuje problemy, a następnie dodaje ulepszenia.
- Jest zbiorcza aktualizacja, która zastępuje wszystkie aktualizacje programu Identity Manager 2016 Service Pack 1 do kompilacji 4.4.1459.0 programu Identity Manager 2016. 
- Wymagane jest posiadanie programu Identity Manager 2016 4.4.1302.0 kompilacji. 

Aby uzyskać więcej informacji, zobacz [pakiet zbiorczy poprawek (kompilacja 4.4.1642.0) jest dostępna dla dodatku Service Pack 1 dla programu Identity Manager 2016](https://support.microsoft.com/help/4021562). 

---
