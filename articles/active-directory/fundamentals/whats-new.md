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
ms.component: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 12/10/2018
ms.author: lizross
ms.reviewer: dhanyahk
ms.custom: it-pro
ms.openlocfilehash: 23fff8fee9e6fd289944da4e946a2a28369ecdd2
ms.sourcegitcommit: 9b6492fdcac18aa872ed771192a420d1d9551a33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/22/2019
ms.locfileid: "54449507"
---
# <a name="whats-new-in-azure-active-directory"></a>What's new in Azure Active Directory?

>Otrzymaj powiadomienie o tym, kiedy Odwiedzaj tę stronę w celu aktualizacji przez kopiowanie i wklejanie tego adresu URL: `https://docs.microsoft.com/api/search/rss?search=%22release+notes+for+azure+AD%22&locale=en-us` do Twojej ![ikonę RSS Widoczną](./media/whats-new/feed-icon-16x16.png) czytnik źródła.

Usługa Azure AD odbiera ulepszenia w sposób ciągły. Aby uzyskać najnowsze informacje o najnowszych zmianach, w tym artykule przedstawiono informacje o:

- Zainstalowane najnowsze wersje
- Znane problemy
- Poprawki błędów
- Przestarzałe funkcje
- Plany dotyczące zmian

Ta strona jest aktualizowany co miesiąc, więc ponownie regularnie. Jeśli szukasz dla elementów, które są starsze niż 6 miesięcy, możesz je znaleźć w [archiwizowania na potrzeby What's new in Azure Active Directory](whats-new-archive.md).

---
## <a name="novemberdecember-2018"></a>Listopad/grudnia 2018 r.

### <a name="users-removed-from-synchronization-scope-no-longer-switch-to-cloud-only-accounts"></a>Użytkownicy usunięci z zakresu synchronizacji nie dłużej przełącznika kontom tylko w chmurze

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
|Kategoria|To była **nazwa usługi** pola. Jest teraz **kategorie inspekcji** pola. **Nazwa usługi** została zmieniona na **loggedByService** pola.|<ul><li>Aprowizacja kont</li><li>Katalog podstawowy</li><li>Samoobsługowe Resetowanie hasła</li></ul>|<ul><li>Zarządzanie użytkownikami</li><li>Zarządzanie grupami</li><li>Zarządzanie aplikacjami</li></ul>|
|targetResources|Obejmuje **TargetResourceType** na najwyższym poziomie.|&nbsp;|<ul><li>Zasady</li><li>aplikacja</li><li>Użytkownik</li><li>Grupa</li></ul>|
|loggedByService|Zawiera nazwę usługi, która wygenerowała dziennika inspekcji.|Null|<ul><li>Aprowizacja kont</li><li>Katalog podstawowy</li><li>Samoobsługowe resetowanie haseł</li></ul>|
|Wynik|Zwraca wynik funkcji dzienników inspekcji. Wcześniej był to wyliczenia, ale teraz pokazujemy wartości rzeczywistej.|<ul><li>0</li><li>1</li></ul>|<ul><li>Powodzenie</li><li>Niepowodzenie</li></ul>|

#### <a name="changed-fields-in-the-sign-in-schema"></a>Zmienione pola w schemacie logowania
Następujące pola są zmiany w schemacie logowania:

|Nazwa pola|Co się zmieniło|Stare wartości|Nowe wartości|
|----------|------------|----------|----------|
|appliedConditionalAccessPolicies|To była **conditionalaccessPolicies** pola. Jest teraz **appliedConditionalAccessPolicies** pola.|Bez zmian|Bez zmian|
|conditionalAccessStatus|Zwraca wynik funkcji o stanie zasad dostępu warunkowego przy logowaniu. Wcześniej był to wyliczenia, ale teraz pokazujemy wartości rzeczywistej.|<ul><li>0</li><li>1</li><li>2</li><li>3</li></ul>|<ul><li>Powodzenie</li><li>Niepowodzenie</li><li>Nie zastosowano</li><li>Disabled (Wyłączony)</li></ul>|
|appliedConditionalAccessPolicies: result|Zwraca wynik funkcji poszczególnych stan zasady warunkowego dostępu w podczas logowania. Wcześniej był to wyliczenia, ale teraz pokazujemy wartości rzeczywistej.|<ul><li>0</li><li>1</li><li>2</li><li>3</li></ul>|<ul><li>Powodzenie</li><li>Niepowodzenie</li><li>Nie zastosowano</li><li>Disabled (Wyłączony)</li></ul>|

Aby uzyskać więcej informacji na temat schematu, zobacz [inspekcji usługi Azure AD interpretację rejestruje schematu w usłudze Azure Monitor (wersja zapoznawcza)](https://docs.microsoft.com/azure/active-directory/reports-monitoring/reference-azure-monitor-audit-log-schema)

---

### <a name="identity-protection-improvements-to-the-supervised-machine-learning-model-and-the-risk-score-engine"></a>Ulepszenia ochrony tożsamości nadzorowanych modelu uczenia maszynowego i aparat oceny ryzyka

**Typ:** Zmieniona funkcja  
**Kategoria usług:** Identity Protection  
**Możliwości produktu:** Oceny ryzyka

Ulepszenia dotyczące ochrony tożsamości użytkownika i zaloguj się ryzyko oceny aparat może pomóc zwiększyć dokładność ryzyka użytkownika i pokrycia. Administratorzy mogą Zwróć uwagę, że poziom ryzyka użytkownika zostało już bezpośrednio połączone z poziomu ryzyka wykrywania określonych i czy jest wzrost liczba i poziom ryzykownych zdarzeń logowania.

Wykrywanie ryzyka, teraz są oceniane przez nadzorowanych modelu, który oblicza ryzyko związane z użytkownikiem za pomocą dodatkowych funkcji logowania użytkownika i wzorzec wykrywania uczenia maszynowego. Na podstawie tego modelu, administrator może okazać się użytkownikom ocen wysokiego ryzyka, nawet jeśli wykryć w skojarzonym z tym użytkownikiem zagrożenia na poziomie niskim lub średnim. 

---

### <a name="administrators-can-reset-their-own-password-using-the-microsoft-authenticator-app-public-preview"></a>Administratorzy mogą resetować swoje hasła przy użyciu aplikacji Microsoft Authenticator (publiczna wersja zapoznawcza)

**Typ:** Zmieniona funkcja  
**Kategoria usług:** Samodzielne resetowanie hasła  
**Możliwości produktu:** Uwierzytelnianie użytkowników

Administratorzy usługi Azure AD, mogą teraz zresetować swoje hasła przy użyciu powiadomienia w aplikacji Microsoft Authenticator lub kod z aplikacji mobilnej wystawcy uwierzytelnienia lub sprzęt tokenu. Aby zresetować własne hasło, Administratorzy teraz będą mogli korzystać z dwóch z następujących metod:

- Powiadomienie w aplikacji Microsoft Authenticator

- Innych aplikacji mobilnej wystawcy uwierzytelnienia / sprzętu token kodu

- Email

- Połączenie telefoniczne

- Wiadomość SMS

Aby uzyskać więcej informacji na temat korzystania z aplikacji Microsoft Authenticator resetowania haseł, zobacz [usługi Azure AD samoobsługowego resetowania haseł — aplikacji mobilnych i funkcji samoobsługowego resetowania HASEŁ (wersja zapoznawcza)](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-howitworks#mobile-app-and-sspr-preview)

---

### <a name="new-azure-ad-cloud-device-administrator-role-public-preview"></a>Nowa rola Administrator urządzenia w chmurze Azure AD (publiczna wersja zapoznawcza)

**Typ:** Nowa funkcja  
**Kategoria usług:** Rejestracja urządzeń i zarządzanie nimi  
**Możliwości produktu:** Kontrola dostępu

Administratorzy mogą przypisywać użytkowników do nowej roli Administrator urządzenia w chmurze do wykonywania zadań administratora urządzenia w chmurze. Użytkownicy przypisani do roli Administratorzy urządzenia w chmurze można włączać, wyłączać i usuwać urządzenia w usłudze Azure AD oraz możliwość odczytu klucze funkcji BitLocker 10 systemu Windows (jeśli istnieje) w witrynie Azure portal.

Aby uzyskać więcej informacji na temat ról i uprawnień, zobacz [przypisywanie ról administratorów w usłudze Azure Active Directory](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)

---

### <a name="manage-your-devices-using-the-new-activity-timestamp-in-azure-ad-public-preview"></a>Zarządzanie urządzeniami za pomocą nowa sygnatura czasowa działania w usłudze Azure AD (publiczna wersja zapoznawcza)

**Typ:** Nowa funkcja  
**Kategoria usług:** Rejestracja urządzeń i zarządzanie nimi  
**Możliwości produktu:** Zarządzanie cyklem życia urządzenia

Zdajemy sobie sprawę, że wraz z upływem czasu należy odświeżyć i wycofywania urządzeń w swojej organizacji w usłudze Azure AD, aby uniknąć nieaktualne urządzenia zawiesza się w danym środowisku. Aby ułatwić ten proces, teraz usługi Azure AD aktualizuje urządzeń za pomocą nowa sygnatura czasowa działania pomaga zarządzać cyklu życia urządzenia.

Aby uzyskać więcej informacji na temat i korzystaj z tą sygnaturą czasową zobacz [How to: Zarządzanie starych urządzeniami w usłudze Azure AD](https://docs.microsoft.com/azure/active-directory/devices/manage-stale-devices)

---

### <a name="administrators-can-require-users-to-accept-a-terms-of-use-on-each-device"></a>Administratorzy mogą wymagać od użytkowników o zaakceptowanie warunków użytkowania na każdym urządzeniu

**Typ:** Nowa funkcja  
**Kategoria usług:** Warunki użytkowania  
**Możliwości produktu:** Nadzór
 
Administratorzy mogą włączyć **wymagać od użytkowników akceptacji na każdym urządzeniu** opcję użytkownikom zaakceptować warunki użytkowania na każdym urządzeniu używanych w dzierżawie.

Aby uzyskać więcej informacji, zobacz [warunków na poziomie urządzenia użyj postanowień dotyczących usługi Azure Active Directory użyj funkcji](https://docs.microsoft.com/azure/active-directory/governance/active-directory-tou#per-device-terms-of-use).

---

### <a name="administrators-can-configure-a-terms-of-use-to-expire-based-on-a-recurring-schedule"></a>Administratorzy mogą skonfigurować warunki użytkowania wygaśnie w oparciu o Harmonogram cykliczny

**Typ:** Nowa funkcja  
**Kategoria usług:** Warunki użytkowania  
**Możliwości produktu:** Nadzór
 

Administratorzy mogą włączyć **wygaśnie zgody** opcję warunki użytkowania wygaśnie dla wszystkich użytkowników na podstawie zgodnie z określonym harmonogramem cyklicznym. Harmonogram może być rocznie, bi rocznie, co kwartał i co miesiąc. Po wygaśnięciu warunków użytkowania musi akceptować użytkowników.

Aby uzyskać więcej informacji, zobacz [warunki Użyj postanowień dotyczących usługi Azure Active Directory użyj funkcji Dodaj](https://docs.microsoft.com/azure/active-directory/governance/active-directory-tou#add-terms-of-use).

---

### <a name="administrators-can-configure-a-terms-of-use-to-expire-based-on-each-users-schedule"></a>Administratorzy mogą skonfigurować warunki użytkowania do wygaśnięcia na podstawie harmonogramu każdego użytkownika

**Typ:** Nowa funkcja  
**Kategoria usług:** Warunki użytkowania  
**Możliwości produktu:** Nadzór

Administratorzy mogą teraz określić czas trwania ten użytkownik musi ponownie zaakceptowali warunki użytkowania. Na przykład Administratorzy mogą określić, czy użytkownicy muszą ponownie zaakceptowali warunki użytkowania co 90 dni.

Aby uzyskać więcej informacji, zobacz [warunki Użyj postanowień dotyczących usługi Azure Active Directory użyj funkcji Dodaj](https://docs.microsoft.com/azure/active-directory/governance/active-directory-tou#add-terms-of-use).
 
---

### <a name="new-azure-ad-privileged-identity-management-pim-emails-for-azure-active-directory-roles"></a>Nowe usługi Azure AD Privileged Identity Management (PIM) wysłany w wiadomości e-mail dla ról usługi Azure Active Directory

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

### <a name="group-based-licensing-is-now-generally-available"></a>Licencjonowanie na podstawie grupy jest teraz ogólnie dostępna

**Typ:** Zmieniona funkcja  
**Kategoria usług:** Inne  
**Możliwości produktu:** Katalog

Licencjonowanie na podstawie grupy jest poza publicznej wersji zapoznawczej i jest teraz ogólnie dostępna. W ramach tej wersji głównej ta funkcja została wprowadziliśmy bardziej skalowalna i dodano możliwość ponownego przetworzenia oparte na grupach przypisania licencji dla pojedynczego użytkownika i możliwość używania oparte na grupach licencji z licencjami pakietu Office 365 E3/A3.

Aby uzyskać więcej informacji na temat licencjonowania opartego na grupach, zobacz [co to jest oparte na grupach Licencjonowanie w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-licensing-whatis-azure-portal)

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---november-2018"></a>Nowe aplikacje federacyjne dostępne w galerii aplikacji usługi Azure AD — listopada 2018 r.

**Typ:** Nowa funkcja  
**Kategoria usług:** Aplikacje dla przedsiębiorstw  
**Możliwości produktu:** Integracje innych firm
 
Na listopada 2018 Dodaliśmy obsługę 26 nowych aplikacjach z Federacją w galerii aplikacji:

[CoreStack](https://cloud.corestack.io/site/login), [HubSpot](https://docs.microsoft.com/azure/active-directory/saas-apps/HubSpot-tutorial), [GetThere](https://docs.microsoft.com/azure/active-directory/saas-apps/getthere-tutorial), [Pe gramatyczne](https://docs.microsoft.com/azure/active-directory/saas-apps/grape-tutorial), [eHour](https://getehour.com/try-now), [Consent2Go](https://docs.microsoft.com/azure/active-directory/saas-apps/Consent2Go-tutorial), [Appinux](https://docs.microsoft.com/azure/active-directory/saas-apps/appinux-tutorial), [DriveDollar](https://www.drivedollar.com/Account/Login), [Useall](https://docs.microsoft.com/azure/active-directory/saas-apps/useall-tutorial), [Campus nieskończonej](https://docs.microsoft.com/azure/active-directory/saas-apps/infinitecampus-tutorial), [Alaya](https://alayagood.com/en/demo/), [ HeyBuddy](https://docs.microsoft.com/azure/active-directory/saas-apps/heybuddy-tutorial), [Wrike SAML](https://docs.microsoft.com/azure/active-directory/saas-apps/wrike-tutorial), [dryfu](https://docs.microsoft.com/azure/active-directory/saas-apps/drift-tutorial), [Zenegy dla firm centralnej 365](https://accounting.zenegy.com/), [Everbridge elementu członkowskiego portalu](https://docs.microsoft.com/azure/active-directory/saas-apps/everbridge-tutorial), [IDEO](https://profile.ideo.com/users/sign_up), [Ivanti programu Service Manager (ISM)](https://docs.microsoft.com/azure/active-directory/saas-apps/ivanti-service-manager-tutorial), [Peakon](https://docs.microsoft.com/azure/active-directory/saas-apps/peakon-tutorial), [logowania jednokrotnego Allbound](https://docs.microsoft.com/azure/active-directory/saas-apps/allbound-sso-tutorial), [obiekt typu Plex aplikacje — Test klasycznego](https://test.plexonline.com/signon), [obiekt typu Plex aplikacji — Model Klasyczny](https://www.plexonline.com/signon), [obiekt typu Plex aplikacje — testowego UX](https://test.cloud.plex.com/sso), [obiekt typu Plex aplikacji — UX](https://cloud.plex.com/sso), [obiekt typu Plex aplikacje zarządzania tożsamościami i Dostępem](https://accounts.plex.com/), [RZEMIEŚLNICZYCH - uczestnictwa, System śledzenia danych finansowych i pielęgnacji rekordów](https://getcrafts.ca/craftsregistration) 

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
**Kategoria usług:** Zarządzanie katalogami  
**Możliwości produktu:** Katalog

Mamy przyjemność ogłosić, że teraz można użyć domeny ForceDelete interfejsu API można usunąć nazwy domeny niestandardowej, asynchronicznie zmieniając nazwę odwołania, takich jak użytkownicy, grupy i aplikacje z niestandardowej nazwy domeny (contoso.com) do (nazwa domeny początkowej domyślnej contoso.onmicrosoft.com).

Ta zmiana pomaga szybciej usunąć nazwy domeny niestandardowej, czy Twoja organizacja już używa nazwy, czy należy użyć nazwy domeny za pomocą innej usługi Azure AD.

Aby uzyskać więcej informacji, zobacz [usuwanie nazwy domeny niestandardowej](https://docs.microsoft.com/azure/active-directory/users-groups-roles/domains-manage#delete-a-custom-domain-name).

---

## <a name="september-2018"></a>Wrzesień 2018 r.
 
### <a name="updated-administrator-role-permissions-for-dynamic-groups"></a>Zaktualizowane uprawnienia roli administratora dla grup dynamicznych

**Typ:** Ustalone  
**Kategoria usług:** Zarządzanie grupami  
**Możliwości produktu:** Współpraca

Rozwiązaliśmy problem, więc teraz tworzyć i aktualizować reguły członkostwa dynamicznego, bez potrzeby bycia właściciela grupy ról określonego administratora.

Dostępne są następujące role:

- Administrator globalny lub zapisywania firmy

- Administrator usługi Intune

- Administrator kont użytkowników

Aby uzyskać więcej informacji, zobacz [utworzyć grupę dynamiczną i sprawdzić stan](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-create-rule)

---

### <a name="simplified-single-sign-on-sso-configuration-settings-for-some-third-party-apps"></a>Uproszczone ustawienia konfiguracji logowania jednokrotnego dla niektórych aplikacji innej firmy

**Typ:** Nowa funkcja  
**Kategoria usług:** Aplikacje dla przedsiębiorstw  
**Możliwości produktu:** Logowanie jednokrotne

Zdajemy sobie sprawę, że ustawienia zapasowej pojedynczego logowania jednokrotnego (SSO) dla oprogramowania jako usługi (SaaS) aplikacji może być trudne, ze względu na unikatowy charakter każdej konfiguracji aplikacji. Mamy skompilowane środowisko uproszczona konfiguracja, aby automatycznie wypełnić ustawień konfiguracji logowania jednokrotnego dla następujących aplikacji SaaS innych firm:

- Zendesk

- Komponent ArcGis Online

- Narzędzie Jamf Pro

Aby rozpocząć korzystanie z tego środowiska jednym kliknięciem, przejdź do **witryny Azure portal** > **konfiguracji logowania jednokrotnego** strony aplikacji. Aby uzyskać więcej informacji, zobacz [integracji aplikacji SaaS w usłudze Azure Active Directory](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list)

---

### <a name="azure-active-directory---where-is-your-data-located-page"></a>Azure Active Directory — strona Gdzie znajdują się dane?

**Typ:** Nowa funkcja  
**Kategoria usług:** Inne  
**Możliwości produktu:** GoLocal

Wybierz region, firmy z **usługi Azure Active Directory — Twoje dane lokalizacji** strony do widoku, w którym Centrum danych Azure przechowuje dane usługi Azure AD w spoczynku dla wszystkich usług Azure AD. Informacje można filtrować według określonej usługi Azure AD dla regionu w firmie.

Aby skorzystać z tej funkcji i uzyskać więcej informacji, zobacz [usługi Azure Active Directory — Twoje dane lokalizacji](https://aka.ms/AADDataMap).

---

### <a name="new-deployment-plan-available-for-the-my-apps-access-panel"></a>Nowy plan wdrożenia dostępny dla panelu Dostęp do moich aplikacji

**Typ:** Nowa funkcja  
**Kategoria usług:** Moje aplikacje  
**Możliwości produktu:** Logowanie jednokrotne

Sprawdź nowy plan wdrożenia, dostępnej w panelu Moje aplikacjom dostępu (https://aka.ms/deploymentplans).
Panel Moje dostępu aplikacji zapewnia użytkownikom z jednego miejsca można znaleźć i uzyskać dostęp do swoich aplikacji. Ten portal zapewnia również użytkownikom możliwości samoobsługi, takie jak żądania dostępu do aplikacji i grup oraz zarządzanie dostępem do tych zasobów w imieniu innych użytkowników.

Aby uzyskać więcej informacji, zobacz [co to jest portal Moje aplikacje?](https://docs.microsoft.com/azure/active-directory/user-help/active-directory-saas-access-panel-introduction)

---

### <a name="new-troubleshooting-and-support-tab-on-the-sign-ins-logs-page-of-the-azure-portal"></a>Nowa karta Rozwiązywanie problemów i pomoc techniczna na stronie dzienników logowań witryny Azure Portal

**Typ:** Nowa funkcja  
**Kategoria usług:** Raportowanie  
**Możliwości produktu:** Monitorowanie i raportowanie

Nowy **Rozwiązywanie problemów i pomoc techniczna** karcie **logowania** strony Azure portal, mają na celu ułatwienie administratorom i pracowników działu pomocy technicznej, rozwiązywania problemów związanych z logowania usługi Azure AD. Ta nowa karta zawiera kod błędu, komunikat o błędzie i zaleceniami związanymi z korygowaniem (jeśli istnieje) Aby rozwiązać ten problem. Jeśli nie uda się rozwiązać ten problem, również udostępniamy nowy sposób tworzenia biletu pomocy technicznej przy użyciu **Kopiuj do Schowka** środowisko, które wypełnia **identyfikator żądania** i **Data (UTC)** pól pliku dziennika w biletu pomocy technicznej.  

![Dzienniki logowania z nowej karty](media/whats-new/troubleshooting-and-support.png)

---

### <a name="enhanced-support-for-custom-extension-properties-used-to-create-dynamic-membership-rules"></a>Ulepszona obsługa niestandardowych właściwości rozszerzenia używanych do tworzenia reguł członkostwa dynamicznego

**Typ:** Zmieniona funkcja  
**Kategoria usług:** Zarządzanie grupami  
**Możliwości produktu:** Współpraca

Dzięki tej aktualizacji można teraz kliknąć **pobieranie właściwości niestandardowego rozszerzenia** link z konstruktora reguły grupy dynamiczne użytkownika, wprowadź swój identyfikator aplikacji unikatowymi i otrzymywać pełną listę właściwości niestandardowego rozszerzenia do użycia podczas tworzenia dynamiczny reguły członkostwa dla użytkowników. Tej listy można odświeżać w taki sposób, aby uzyskać nowe właściwości rozszerzenia niestandardowego dla danej aplikacji.

Aby uzyskać więcej informacji o używaniu właściwości rozszerzenia niestandardowe reguły członkostwa dynamicznego, zobacz [rozszerzenia i właściwości niestandardowego rozszerzenia](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership#extension-properties-and-custom-extension-properties)

---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Nowe zatwierdzone aplikacje klienckie na potrzeby opartego na aplikacjach dostępu warunkowego w usłudze Azure AD

**Typ:** Plan zmiany  
**Kategoria usług:** Dostęp warunkowy  
**Możliwości produktu:** Tożsamość, zabezpieczenia i ochrona

Następujące aplikacje są na liście [zatwierdzonych aplikacji klienckich](https://docs.microsoft.com/azure/active-directory/conditional-access/technical-reference#approved-client-app-requirement):

- Microsoft To-Do

- Microsoft Stream

Aby uzyskać więcej informacji, zobacz:

- [Usługa Azure AD oparty na aplikacji dostępu warunkowego](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)

---

### <a name="new-support-for-self-service-password-reset-from-the-windows-7881-lock-screen"></a>Nowa funkcja obsługi samoobsługowego resetowania hasła z ekranu blokady systemu Windows 7/8/8.1

**Typ:** Nowa funkcja  
**Kategoria usług:** SSPR  
**Możliwości produktu:** Uwierzytelnianie użytkowników

Po skonfigurowaniu tej nowej funkcji użytkownikom zostanie wyświetlony link do zresetowania swojego hasła z **blokady** ekranu urządzenia z systemem Windows 7, Windows 8 lub Windows 8.1. Klikając ten link, użytkownik jest przeprowadzany przez sam przepływ resetowania hasła jako za pośrednictwem przeglądarki sieci web.

Aby uzyskać więcej informacji, zobacz [jak włączyć funkcję resetowania z Windows 7, 8 i 8.1 hasła](https://aka.ms/ssprforwindows78)

---

### <a name="change-notice-authorization-codes-will-no-longer-be-available-for-reuse"></a>Powiadomienie o zmianie: Kody autoryzacji nie będzie już dostępny do ponownego wykorzystania 

**Typ:** Plan zmiany  
**Kategoria usług:** Uwierzytelnienia (logowania)  
**Możliwości produktu:** Uwierzytelnianie użytkowników

15 listopada 2018 r. usługi Azure AD będzie akceptować kody poprzednio używanych uwierzytelniania dla aplikacji. Ta zmiana zabezpieczeń pomaga do usługi Azure AD zgodnie ze specyfikacją protokołu OAuth i będzie wymuszany w punktach końcowych v1 i v2.

Jeśli aplikacja używa kody autoryzacji uzyskiwanie tokenów dla wielu zasobów, zalecamy Użyj kodu, aby uzyskać token odświeżania, a następnie używać tego tokenu odświeżania w celu pobrania dodatkowe tokeny dla innych zasobów. Kody autoryzacji należy używać tylko raz, ale tokenów odświeżania mogą być wielokrotnie używane w wielu zasobach. Aplikacja, która podejmuje próbę ponownego użycia kodu uwierzytelniania podczas przepływu kodu OAuth otrzymają komunikat o błędzie invalid_grant.

Ta i inne zmiany dotyczące protokołów, zobacz [pełną listę Nowości dla uwierzytelniania](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---september-2018"></a>Nowe aplikacje federacyjne dostępne w galerii aplikacji usługi Azure AD — wrzesień 2018

**Typ:** Nowa funkcja  
**Kategoria usług:** Aplikacje dla przedsiębiorstw  
**Możliwości produktu:** Integracje innych firm
 
W września 2018 Dodaliśmy obsługę 16 nowych aplikacjach z Federacją w galerii aplikacji:

[Uberflip](https://docs.microsoft.com/azure/active-directory/saas-apps/uberflip-tutorial), [Comeet rekrutacji oprogramowania](https://docs.microsoft.com/azure/active-directory/saas-apps/comeetrecruitingsoftware-tutorial), [Workteam](https://docs.microsoft.com/azure/active-directory/saas-apps/workteam-tutorial), [ArcGIS Enterprise](https://docs.microsoft.com/azure/active-directory/saas-apps/arcgisenterprise-tutorial), [Nuclino](https://docs.microsoft.com/azure/active-directory/saas-apps/nuclino-tutorial), [ Chmura JDA](https://docs.microsoft.com/azure/active-directory/saas-apps/jdacloud-tutorial), [Snowflake](https://docs.microsoft.com/azure/active-directory/saas-apps/snowflake-tutorial), NavigoCloud, [Figma](https://docs.microsoft.com/azure/active-directory/saas-apps/figma-tutorial), join.me, [ZephyrSSO](https://docs.microsoft.com/azure/active-directory/saas-apps/zephyrsso-tutorial), [Silverback](https://docs.microsoft.com/azure/active-directory/saas-apps/silverback-tutorial), Riverbed Xirrus EasyPass, [logowania jednokrotnego Rackspace](https://docs.microsoft.com/azure/active-directory/saas-apps/rackspacesso-tutorial), Enlyft Usługa rejestracji Jednokrotnej dla platformy Azure, usługę SurveyMonkey na [Convene](https://docs.microsoft.com/azure/active-directory/saas-apps/convene-tutorial), [dmarcian](https://docs.microsoft.com/azure/active-directory/saas-apps/dmarcian-tutorial)

Aby uzyskać więcej informacji o aplikacjach, zobacz [integracji aplikacji SaaS w usłudze Azure Active Directory](https://aka.ms/appstutorial). Aby uzyskać więcej informacji o ofercie swoją aplikację w galerii aplikacji Azure AD, zobacz [umieść swoją aplikację w galerii aplikacji usługi Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="support-for-additional-claims-transformations-methods"></a>Obsługa dodatkowych metod przekształceń oświadczeń

**Typ:** Nowa funkcja  
**Kategoria usług:** Aplikacje dla przedsiębiorstw  
**Możliwości produktu:** Logowanie jednokrotne

Dodaliśmy nowe metody przekształcania oświadczeń ToLower() i ToUpper(), które mogą być stosowane na tokeny SAML z opartej na SAML **konfiguracji rejestracji jednokrotnej** strony.

Aby uzyskać więcej informacji, zobacz [Dostosowywanie oświadczeń wystawionych w tokenie języka SAML dla aplikacji dla przedsiębiorstw w usłudze Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization)

---

### <a name="updated-saml-based-app-configuration-ui-preview"></a>Zaktualizowany interfejs użytkownika konfiguracji aplikacji opartej na języku SAML (wersja zapoznawcza)

**Typ:** Zmieniona funkcja  
**Kategoria usług:** Aplikacje dla przedsiębiorstw  
**Możliwości produktu:** Logowanie jednokrotne

W ramach naszego interfejsu użytkownika konfiguracji zaktualizowanej aplikacji opartej na SAML uzyskasz:

- Środowisko zaktualizowano wskazówki dotyczące konfigurowania aplikacji opartej na SAML.

- Widoczność o nowościach brakujące lub nieprawidłowe w konfiguracji.

- Możliwość dodawania wielu adresów e-mail, aby uzyskać powiadomienie o wygaśnięciu certyfikatu.

- Nowe metody przekształcania oświadczeń, ToLower() i ToUpper() i inne.

- Sposób przekazać własny token podpisywania certyfikatu dla aplikacji przedsiębiorstwa.

- Sposób, aby ustawić Format identyfikatora NameID dla aplikacji SAML i sposób można ustawić wartości identyfikatora NameID jako rozszerzenia katalogów.

Aby włączyć ten zaktualizowany widok, kliknij przycisk **wypróbować nasze nowe środowisko** link u góry **logowania jednokrotnego** strony. Aby uzyskać więcej informacji, zobacz [Samouczek: Konfigurowanie opartej na SAML logowania jednokrotnego dla aplikacji w usłudze Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-single-sign-on-portal).

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

Aby uzyskać więcej informacji, zobacz [Azure Active Directory — warunki dotyczące użycia funkcji](https://docs.microsoft.com/azure/active-directory/active-directory-tou).

---

### <a name="new-custom-conditional-access-policy-template-created-during-terms-of-use-tou-creation"></a>Nowy szablon zasad dostępu warunkowego "niestandardowy", podczas tworzenia warunków użytkowania (ToU)

**Typ:** Nowa funkcja  
**Kategoria usług:** Warunki użytkowania  
**Możliwości produktu:** Nadzór

Podczas tworzenia warunków użytkowania (ToU) również tworzony jest nowy szablon zasad dostępu warunkowego "niestandardowe". Ten nowy szablon zasad umożliwia tworzenie warunków użytkowania, a następnie od razu przejść do bloku tworzenia zasad dostępu warunkowego, bez konieczności ręcznego przejdź za pośrednictwem portalu.

Aby uzyskać więcej informacji, zobacz [Azure Active Directory — warunki dotyczące użycia funkcji](https://docs.microsoft.com/azure/active-directory/active-directory-tou).

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