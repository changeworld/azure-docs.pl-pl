---
title: Tworzenie strategii zarządzania kontroli dostępu odporne na błędy — usługi Azure Active Directory
description: Niniejszy dokument zawiera wskazówki dotyczące strategii wiadomość organizacji powinna przyjąć zapewnienie odporności, aby zmniejszyć ryzyko blokady podczas przerw w działaniu nieprzewidziane
services: active-directory
author: martincoetzer
manager: daveba
tags: azuread
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.date: 12/19/2018
ms.author: martincoetzer
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6e1fa72f8c7edf76ec46663fd62ee40a3a16e8cd
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "58886084"
---
# <a name="create-a-resilient-access-control-management-strategy-with-azure-active-directory"></a>Tworzenie strategii zarządzania kontroli dostępu odporne na błędy przy użyciu usługi Azure Active Directory

>[!NOTE]
> Informacje zawarte w tym dokumencie reprezentuje bieżący widok firmy Microsoft Corporation na problemy omówione w dniu publikacji. Ponieważ firma Microsoft musi reagować na zmieniające się warunki na rynku, nie powinny być rozumiane jako zobowiązanie ze strony firmy Microsoft, a firma Microsoft nie gwarantuje dokładności informacji po opublikowaniu.

Organizacje, które polegają na kontroli dostępu jednego, takie jak uwierzytelnianie wieloskładnikowe (MFA) lub jedną lokalizację sieciową, aby zabezpieczyć swoje systemy IT są podatne na błędy dostępu do swoich aplikacji i zasobów, jeśli kontroli dostępu jednego staje się niedostępny lub zostały nieprawidłowo skonfigurowane. Na przykład klęski żywiołowe, może spowodować niedostępność dużych segmentów infrastruktury telekomunikacyjnej lub sieci firmowej. Zakłócenia te można uniemożliwić użytkownikom końcowym i administratorom możliwości zarejestrowania się.

Niniejszy dokument zawiera wskazówki dotyczące strategii wiadomość organizacji powinna przyjąć zapewnienie odporności, aby zmniejszyć ryzyko blokady podczas przerw w działaniu nieprzewidziane z następującymi scenariuszami:

 1. Organizacje mogą zwiększyć ich odporności, aby zmniejszyć ryzyko blokady **przed zakłócenia** implementując strategii ograniczania ryzyka lub plany awaryjne.
 2. Organizacje mogą w dalszym ciągu dostęp do aplikacji i zasobów zdecydują **podczas przerw w działaniu** przez umieszczenie strategii ograniczania ryzyka i plany awaryjne w miejscu.
 3. Upewnij się, przechowują informacje, takie jak dzienniki, organizacje **po zakłócenia** i przed przywracanie ich warunkowych, wszystkie one wdrożone.
 4. Organizacje, które nie zaimplementował strategii zapobiegania lub alternatywne plany mogą mieć możliwość wdrożenia **awaryjnego opcje** radzenia sobie z zakłócenie.

## <a name="key-guidance"></a>Wskazówki dotyczące kluczy

Istnieją cztery najważniejsze wnioski, w tym dokumencie:

* Należy unikać blokowania administratora za pomocą kont dostępu awaryjnego.
* Implementowanie uwierzytelniania Wieloskładnikowego przy użyciu dostępu warunkowego (CA), zamiast usługi MFA na użytkownika.
* Eliminowanie blokady użytkownika za pomocą wielu kontrolek dostępu warunkowego (CA).
* Eliminowanie blokady użytkownika, aprowizując wielu metod uwierzytelniania lub odpowiedniki dla każdego użytkownika.

## <a name="before-a-disruption"></a>Przed przerw w działaniu

Łagodzenia przerw w działaniu rzeczywiste musi być infrastrukturalnej organizacji w dotyczącej problemów dotyczących kontroli dostępu, które mogą wystąpić podczas. Łagodzenia obejmuje planowanie dotyczące rzeczywistego zdarzenia, oraz implementowanie strategie, aby upewnić się, że mechanizmy kontroli dostępu i operacje są bez zmian podczas przerw w działaniu.

### <a name="why-do-you-need-resilient-access-control"></a>Dlaczego jest wymagana kontrola dostępu odporne na błędy?

 Tożsamość jest płaszczyznę kontroli użytkowników uzyskujących dostęp do aplikacji i zasobów. System tożsamości kontroluje użytkowników, którzy i pod jakimi warunkami, takich jak funkcje kontroli dostępu lub wymagania dotyczące uwierzytelniania, użytkownicy mogą uzyskiwać dostęp do aplikacji. Co najmniej jeden uwierzytelniania lub dostępu wymaganiami dotyczącymi kontroli nie są dostępne dla użytkowników do uwierzytelniania z powodu nieprzewidzianego okoliczności, organizacje mogą wystąpić jedno lub oba z następujących problemów:

* **Administrator blokady:** Administratorzy nie mogą zarządzać dzierżawy lub usługi.
* **Funkcja blokady użytkownika:** Użytkownicy nie mogą uzyskiwać dostęp do aplikacji lub zasobów.

### <a name="administrator-lockout-contingency"></a>Administrator blokady awaryjny

Aby odblokować dostęp administracyjny do swojej dzierżawy, należy tworzyć kont dostępu awaryjnego. Tych kont dostępu awaryjnego, nazywana również *awaryjne* kont, Zezwalaj na dostęp do zarządzania konfiguracją usługi Azure AD, podczas normalnego konta uprzywilejowanego dostępu procedury nie są dostępne. Co najmniej dwóch kont dostępu awaryjnego powinny zostać utworzone następujące [zaleceń dotyczących kont dostępu awaryjnego]( https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-emergency-access).

### <a name="mitigating-user-lockout"></a>Ograniczanie ryzyka blokady użytkownika

 Aby zmniejszyć ryzyko blokady użytkownika, użyj zasad dostępu warunkowego z wieloma formantami pozwala użytkownikom wybór sposobu uzyskiwania przez nich dostępu aplikacji i zasobów. Zapewniając użytkownikowi na wybór między, na przykład logujesz się przy użyciu usługi MFA **lub** logowanie z zarządzanego urządzenia **lub** ma użytkownika logowania się z sieci firmowej, jeśli jeden z kontroli dostępu jest niedostępna inne opcje, aby kontynuować pracę.

#### <a name="microsoft-recommendations"></a>Zalecenia firmy Microsoft

Należy zastosować następujące ustawienia kontroli dostępu w istniejących zasad dostępu warunkowego dla organizacji:

1. Aprowizuj wielu metod uwierzytelniania dla każdego użytkownika, korzystających z różnych kanałów komunikacji, na przykład aplikację Microsoft Authenticator (w oparciu o internet), token OATH (wygenerowany dla urządzeń) i (telephonic) programu SMS.
2. Wdróż Windows Hello dla firm na urządzeniach Windows 10 w celu spełnienia wymagań uwierzytelniania Wieloskładnikowego bezpośrednio z urządzenia logowania.
3. Korzystać z zaufanych urządzeń za pośrednictwem [dołączenie do hybrydowej usługi AD Azure](https://docs.microsoft.com/azure/active-directory/devices/overview) lub [urządzeń zarządzanych przez usługę Intune Microsoft](https://docs.microsoft.com/intune/planning-guide). Zaufane urządzenia poprawi środowisko użytkownika, ponieważ zaufanego urządzenia można spełniają wymagania silnego uwierzytelniania zasady bez żądania uwierzytelniania MFA dla użytkownika. Uwierzytelnianie wieloskładnikowe będzie wymagane podczas rejestrowania nowego urządzenia, a podczas uzyskiwania dostępu do aplikacji lub zasobów z niezaufanych urządzeń.
4. Korzystanie z usługi Azure AD tożsamości opartych na ryzykach zasad ochrony, które zapobiegają dostępowi, gdy użytkownik lub logowania są zagrożone zamiast stałych zasad MFA.

>[!NOTE]
> Zasady na podstawie ryzyka wymagają [usługi Azure AD Premium P2](https://azure.microsoft.com/pricing/details/active-directory/) licencji.

Na poniższym przykładzie opisano zasady, należy utworzyć w celu zapewnienia kontroli dostępu odporne na błędy dla użytkownika, dostęp do swoich aplikacji i zasobów. W tym przykładzie będzie potrzebna jest grupa zabezpieczeń **AppUsers** nazwę grupy użytkowników docelowych, którą chcesz nadać dostęp do, **CoreAdmins** Administratorzy podstawowych i utworzyć grupę o nazwie  **EmergencyAccess** za pomocą kont dostępu awaryjnego.
Ten przykładowy zestaw zasad spowoduje przyznanie wybranych użytkowników w **AppUsers**, dostęp do wybranych aplikacji, jeśli jest nawiązywane z zaufanego urządzenia lub zapewnia silne uwierzytelnianie, na przykład usługi MFA. Wyklucza konta awaryjne i Administratorzy core.

**Ustaw zasady ograniczania ryzyka dostępu Warunkowego:**

* Zasady 1: Blokuj dostęp do osób spoza grupy docelowe
  * Użytkownicy i grupy: Dotyczy wszystkich użytkowników. Wyklucz AppUsers CoreAdmins i EmergencyAccess
  * Aplikacje w chmurze: Obejmują wszystkie aplikacje
  * Warunki: (brak)
  * Grant Control: Blokuj
* Zasady 2: Udzielanie dostępu do AppUsers wymaganie uwierzytelniania Wieloskładnikowego lub zaufanego urządzenia.
  * Użytkownicy i grupy: Obejmują AppUsers. Wyklucz CoreAdmins i EmergencyAccess
  * Aplikacje w chmurze: Obejmują wszystkie aplikacje
  * Warunki: (brak)
  * Grant Control: Udzielanie dostępu, Wymagaj uwierzytelniania wieloskładnikowego, wymagać, aby było zgodne urządzenie. W przypadku wielu kontrolek: Wymagaj jednej z wybranych kontrolek.

### <a name="contingencies-for-user-lockout"></a>Warunkowych dla blokady użytkownika

Alternatywnie organizacji można też utworzyć zasady gotowości. Aby utworzyć zasady gotowości, należy zdefiniować kryteria zależnościami między ciągłości biznesowej, koszty operacyjne, koszty finansowe i zagrożenia dla bezpieczeństwa. Na przykład możesz aktywować awaryjny zasady tylko do podzbioru użytkowników dla podzbioru aplikacje dla podzbioru klientów, albo w podzbiorze lokalizacji. Zasady awaryjny zapewni dostęp administratorów i użytkowników końcowych do aplikacji i zasobów, podczas zakłóceń po zaimplementowaniu żadna metoda ograniczania ryzyka.
Opis usługi ekspozycji podczas przerw w działaniu pozwala zmniejszyć ryzyko i jest krytyczną częścią procesu planowania. Aby utworzyć plan awaryjny, należy najpierw określić następujące wymagania biznesowe organizacji:

1. Określ Twojej aplikacji o kluczowym znaczeniu wcześniej: Co to są aplikacje, podaj dostęp, nawet w przypadku niższy poziom ryzyka/zabezpieczeń? Tworzenie listy te aplikacje i upewnić się, że inne zainteresowane osoby (dla firm, kierownictwo prawne, zabezpieczenia,) są zgodne, jeśli wszystkie kontroli dostępu znika te aplikacje nadal musi być uruchomiony. Prawdopodobnie ma-to-end za pomocą kategorii:
   * **Aplikacji o krytycznym znaczeniu dla misji kategorii 1** , nie może być niedostępna dla więcej niż kilka minut, na przykład w przypadku aplikacji, które mają bezpośredni wpływ na przychody organizacji.
   * **Kategoria 2 ważnych aplikacji** firmy musi być dostępny w ciągu kilku godzin.
   * **Aplikacje o niskim priorytecie kategorii 3** , może wytrzymać zakłócenia przez kilka dni.
2. W przypadku aplikacji w kategorii 1 i 2 Firma Microsoft zaleca wcześniejszego zaplanowania jakiego rodzaju poziom dostępu chcesz zezwolić:
   * Czy chcesz zezwolić na pełny dostęp lub ograniczone sesji, takie jak ograniczanie pliki do pobrania?
   * Czy chcesz zezwolić na dostęp do części aplikacji, ale nie całej aplikacji?
   * Czy chcesz zezwolić na dostęp do informacji o procesu roboczego i zablokować dostęp administratora do momentu przywrócenia kontroli dostępu?
3. W przypadku tych aplikacji, firma Microsoft zaleca również planu, który drogi prowadzące dostępu celowo otworzysz i te, które zostanie zamknięte:
   * Czy chcesz zezwolić na przeglądarce tylko dostęp i blok sformatowanego klientów, które można zapisać danych w trybie offline?
   * Czy chcesz zezwolić na dostęp tylko dla użytkowników w sieci firmowej i zachować poza zablokowanych użytkowników?
   * Czy chcesz zezwolić na dostęp z określonych krajów lub regionów tylko podczas przerw w działaniu?
   * Czy chcesz, aby zasad w zasadach gotowości, szczególnie w przypadku aplikacji o kluczowym znaczeniu, aby udaje się powieść, jeśli formant dostępu alternatywnego nie jest dostępny?

#### <a name="microsoft-recommendations"></a>Zalecenia firmy Microsoft

Zasady dostępu warunkowego awaryjny **zasady wyłączone** , pomija formantów opartych na ryzykach lub na podstawie urządzeń usługi Azure MFA, MFA innych firm. Następnie gdy organizacja postanawia aktywować plan awaryjny, Administratorzy mogą włączyć zasady i wyłączyć regularne zasad opartych na kontroli.

>[!IMPORTANT]
> Wyłączanie zasad, które wymuszają zabezpieczeń użytkowników, nawet tymczasowo spowoduje obniżenie poziomu bezpieczeństwa gdy plan awaryjny są używane.

* Skonfiguruj zestaw zasad rezerwowego Jeśli zakłócenia w jeden typ poświadczeń lub jeden dostępu kontroli mechanizm wpływa na dostęp do aplikacji. Skonfiguruj zasady w stanie wyłączenia, która wymaga przyłączanie do domeny jako formant do przechowywania kopii zapasowych dla aktywne zasady, które wymagają dostawcy usługi MFA innej firmy.
* Zmniejszyć ryzyko złośliwych podmiotów odgadnięcie hasła, gdy usługi MFA nie jest wymagany, postępując zgodnie z rozwiązania w [wskazówki hasło](https://aka.ms/passwordguidance) oficjalny dokument.
* Wdrażanie [usługi Azure AD Self-Service hasło resetowania (SSPR)](https://docs.microsoft.com/azure/active-directory/authentication/quickstart-sspr) i [ochrony haseł usługi Azure AD](https://docs.microsoft.com/azure/active-directory/authentication/howto-password-ban-bad-on-premises-deploy) się upewnić, że użytkownicy nie należy używać wspólne hasło i warunki, które chcesz zablokować.
* Używaj zasad, które ograniczają dostępu w aplikacji, jeśli nie uzyskano określonego poziomu uwierzytelniania zamiast po prostu nastąpi powrót do pełnego dostępu. Na przykład:
  * Konfigurowanie zasad tworzenia kopii zapasowej, która wysyła oświadczenia sesji ograniczone do programu Exchange i SharePoint.
  * Jeśli Twoja organizacja korzysta z Microsoft Cloud App Security, należy wziąć pod uwagę nastąpi powrót do zasady, które angażuje MCAS, a następnie MCAS umożliwia dostęp tylko do odczytu, ale nie przekazuje.
* Nadaj nazwę swoich zasad, aby upewnij się, że można łatwo je znaleźć podczas przerw w działaniu. W nazwie zasad, należy uwzględnić następujące elementy:
  * A *numer etykiety* zasad.
  * Tekst do wyświetlenia, ta zasada dotyczy tylko w nagłych wypadkach. Na przykład: **WŁĄCZ W AWARYJNEGO**
  * *Przerw w działaniu* dotyczy. Na przykład: **Podczas przerw w działaniu usługi MFA**
  * A *numer sekwencyjny* wyświetlanych kolejność, musisz aktywować zasad.
  * *Aplikacje* dotyczy.
  * *Formantów* będzie stosowana.
  * *Warunki* wymaga.
  
Ten standard nazewnictwa dla zasad gotowości będzie następująca: 

```
EMnnn - ENABLE IN EMERGENCY: [Disruption][i/n] - [Apps] - [Controls] [Conditions]
```

Poniższy przykład: **Przykład A — zasady awaryjny urzędu certyfikacji do przywrócenia dostępu do aplikacji o kluczowym znaczeniu współpracy**, jest typowy awaryjny firmowych. W tym scenariuszu organizacja zazwyczaj wymaga uwierzytelniania Wieloskładnikowego wszelki dostęp do usługi Exchange Online i SharePoint Online, a zakłócenie w tym przypadku jest dostawca usługi MFA dla klienta wystąpiła awaria (czy usługi Azure MFA w środowisku lokalnym dostawcą uwierzytelnianie wieloskładnikowe lub MFA innych firm). Ta zasada zmniejsza tej awarii, zezwalając na określonych użytkowników docelowych dostęp do tych aplikacji z zaufanych urządzeń Windows tylko wtedy, gdy uzyskują dostęp do aplikacji z zaufanych sieci firmowej. Również będzie możliwe wykluczyć z tych ograniczeń konta awaryjne i Administratorzy core. Użytkowników docelowych zostaną następnie uzyskać dostęp do usługi Exchange Online i SharePoint Online, podczas gdy inni użytkownicy będą nadal ma dostęp do aplikacji z powodu awarii. W tym przykładzie będzie wymagać lokalizację sieciową o nazwie **CorpNetwork** i grupy zabezpieczeń **ContingencyAccess** nazwę grupy użytkowników docelowych **CoreAdmins** z Administratorzy podstawowych i utworzyć grupę o nazwie **EmergencyAccess** za pomocą kont dostępu awaryjnego. Awaryjny wymaga cztery zasady w celu zapewnienia żądanego dostępu. 

**Przykład A - awaryjny urząd certyfikacji zasad w celu przywrócenia dostępu do aplikacji o kluczowym znaczeniu współpracy:**

* Zasady 1: Wymagaj urządzenia przyłączonego do domeny dla programów Exchange i SharePoint
  * Nazwa: EM001 - WŁĄCZ W NAGŁYCH: Przerwy w działaniu usługi MFA [1/4] - wymiany SharePoint — wymagają dołączenie do hybrydowej usługi Azure AD
  * Użytkownicy i grupy: Obejmują ContingencyAccess. Wyklucz CoreAdmins i EmergencyAccess
  * Aplikacje w chmurze: Usługa Exchange Online i SharePoint Online
  * Warunki: Dowolne
  * Grant Control: Wymagają przyłączonych do domeny
  * Stan: Disabled (Wyłączony)
* Zasady 2: Blok platform innych niż Windows
  * Nazwa: EM002 - WŁĄCZ W NAGŁYCH: Przerwy w działaniu usługi MFA [2/4] - Exchange SharePoint — Blokuj dostęp z wyjątkiem Windows
  * Użytkownicy i grupy: Dotyczy wszystkich użytkowników. Wyklucz CoreAdmins i EmergencyAccess
  * Aplikacje w chmurze: Usługa Exchange Online i SharePoint Online
  * Warunki: Platforma obejmują wszystkie platformy urządzeń, Wyklucz Windows
  * Grant Control: Blokuj
  * Stan: Disabled (Wyłączony)
* Zasady 3: Blok z sieciami CorpNetwork
  * Nazwa: EM003 - WŁĄCZ W NAGŁYCH: Przerwy w działaniu usługi MFA [3/4] - Exchange SharePoint — Zablokuj dostęp poza siecią firmową
  * Użytkownicy i grupy: Dotyczy wszystkich użytkowników. Wyklucz CoreAdmins i EmergencyAccess
  * Aplikacje w chmurze: Usługa Exchange Online i SharePoint Online
  * Warunki: Lokalizacje obejmują dowolnej lokalizacji, Wyklucz CorpNetwork
  * Grant Control: Blokuj
  * Stan: Disabled (Wyłączony)
* Zasady 4: Jawnie zablokować EAS
  * Nazwa: EM004 - WŁĄCZ W NAGŁYCH: Przerwy w działaniu usługi MFA [4/4] - Exchange - Block EAS dla wszystkich użytkowników
  * Użytkownicy i grupy: Zawierała wszystkich użytkowników
  * Aplikacje w chmurze: Obejmują Exchange Online
  * Warunki: Aplikacje klienckie: Program Exchange Active Sync
  * Grant Control: Blokuj
  * Stan: Disabled (Wyłączony)

Kolejność aktywacji:

1. Wyklucz ContingencyAccess CoreAdmins i EmergencyAccess z istniejących zasad MFA. Sprawdź, czy użytkownik w ContingencyAccess dostęp do usługi SharePoint Online i Exchange Online.
2. Włącz zasady 1: Sprawdź, czy użytkownicy na urządzeniach przyłączonych do domeny, którzy nie znajdują się w wykluczenie grup mogą uzyskiwać dostęp do usługi Exchange Online i SharePoint Online. Sprawdź, czy użytkownicy w grupie wykluczenia może uzyskiwać dostęp do usługi SharePoint Online i Exchange z dowolnego urządzenia.
3. Włącz zasady 2: Sprawdź, czy użytkownicy, którzy nie znajdują się w grupie wykluczenia nie mogą przejść do usługi SharePoint Online i Exchange Online na swoich urządzeniach przenośnych. Sprawdź, czy użytkownicy w grupie wykluczenia można uzyskiwać dostęp do programu SharePoint i Exchange z dowolnego urządzenia (Windows/iOS/Android).
4. Włącz zasady 3: Sprawdź, użytkownicy, którzy nie znajdują się w wykluczenie grup nie można uzyskać dostępu programu SharePoint i Exchange poza siecią firmową, nawet w przypadku domeny połączonych maszyn. Sprawdź, czy użytkownicy w grupie wykluczenia można uzyskiwać dostęp do SharePoint i Exchange z żadną siecią.
5. Włącz zasady 4: Sprawdź, czy wszyscy użytkownicy, nie można pobrać usługi Exchange Online z poziomu aplikacji natywnego programu pocztowego na urządzeniach przenośnych.
6. Wyłącz istniejących zasad MFA dla usługi SharePoint Online i Exchange Online.

W następnym przykładzie **przykład B — zasady dostępu Warunkowego awaryjny, aby umożliwić mobilny dostęp do usługi Salesforce**, dostęp do aplikacji biznesowych jest przywracany. W tym scenariuszu klient zazwyczaj wymaga ich sprzedaży pracownikom dostęp do usługi Salesforce (skonfigurowany do logowania jednokrotnego w usłudze Azure AD) z urządzeń przenośnych, które mają być dozwolone tylko ze zgodnych urządzeń. Zakłócenie w tym przypadku jest że występuje problem z oceny zgodności urządzenia i awarii dzieje się w danym momencie poufnych gdzie sprzedaż zespołu potrzeby dostępu do usługi Salesforce, aby zamknąć transakcje. Te zasady awaryjny spowoduje przyznanie krytycznych użytkownicy dostępu do usługi Salesforce z urządzenia przenośnego, dzięki czemu może nadal Zamknij transakcji i nie przerywasz firmy. W tym przykładzie **SalesforceContingency** zawiera wszyscy pracownicy sprzedaży, którzy chcą zachować dostęp i **SalesAdmins** zawiera niezbędne administratorów usługi Salesforce.

**Przykład B — zasady awaryjny urzędu certyfikacji:**

* Zasady 1: Blokuj Wszyscy nie w zespole SalesContingency
  * Nazwa: EM001 - WŁĄCZ W NAGŁYCH: Przerwy w działaniu zgodności urządzenia [1/2] - Salesforce — blok wszystkich użytkowników z wyjątkiem SalesforceContingency
  * Użytkownicy i grupy: Dotyczy wszystkich użytkowników. Wyklucz SalesAdmins i SalesforceContingency
  * Aplikacje w chmurze: Salesforce.
  * Warunki: Brak
  * Grant Control: Blokuj
  * Stan: Disabled (Wyłączony)
* Zasady 2: Blokuj zespół ds. sprzedaży z dowolnej platformy inne niż mobile (w celu zmniejszenia obszaru powierzchni ataku)
  * Nazwa: EM002 - WŁĄCZ W NAGŁYCH: Przerwy w działaniu zgodności urządzenia [2/2] - Salesforce — Blokuj wszystkie platformy z wyjątkiem systemów iOS i Android
  * Użytkownicy i grupy: Obejmują SalesforceContingency. Wyklucz SalesAdmins
  * Aplikacje w chmurze: SalesForce
  * Warunki: Platforma obejmują wszystkie platformy urządzeń, Wyklucz systemów iOS i Android
  * Grant Control: Blokuj
  * Stan: Disabled (Wyłączony)

Kolejność aktywacji:

1. Wyklucz SalesAdmins i SalesforceContingency z istniejącej zasady zgodności urządzeń usługi Salesforce. Sprawdź, czy użytkownik w grupie SalesforceContingency mógł korzystać z usługi Salesforce.
2. Włącz zasady 1: Sprawdź, czy użytkownicy spoza SalesContingency nie może uzyskiwać dostęp do usługi Salesforce. Sprawdź użytkowników w SalesAdmins i SalesforceContingency mogą uzyskiwać dostęp do usługi Salesforce.
3. Włącz zasady 2: Upewnij się, użytkownicy w grupie SalesContingency nie może uzyskiwać dostęp do usługi Salesforce z ich komputerów przenośnych Windows lub Mac, ale uzyskiwać dostęp z urządzeń przenośnych. Sprawdź, czy SalesAdmin mogą nadal uzyskiwać dostęp do usługi Salesforce z dowolnego urządzenia.
4. Wyłącz istniejącą zasadę zgodności urządzeń dla usług Salesforce.

### <a name="deploy-password-hash-sync-even-if-you-are-federated-or-use-pass-through-authentication"></a>Wdrażanie synchronizacji skrótów haseł, nawet jeśli są Sfederowane lub przy użyciu uwierzytelniania przekazywanego

Funkcja blokady użytkownika może również wystąpić, jeśli są spełnione następujące warunki:

- Twoja organizacja korzysta z rozwiązania tożsamości hybrydowej przy użyciu uwierzytelniania przekazywanego i federacji.
- Lokalnych systemów tożsamości (np. usługi Active Directory, AD FS lub składnik zależny) są niedostępne. 
 
Aby były bardziej odporne, Twoja organizacja powinna [Włączanie synchronizacji skrótów haseł](https://docs.microsoft.com/azure/security/azure-ad-choose-authn), ponieważ umożliwia ona [rozpocząć korzystanie z synchronizacji skrótów haseł](https://docs.microsoft.com/azure/active-directory/hybrid/plan-connect-user-signin) w przypadku lokalnych systemów tożsamości w dół.

#### <a name="microsoft-recommendations"></a>Zalecenia firmy Microsoft
 Włączanie synchronizacji skrótów haseł za pomocą Kreatora Azure AD Connect, niezależnie od tego, czy Twoja organizacja korzysta z Federacji lub uwierzytelniania przekazywanego.

>[!IMPORTANT]
> Nie jest wymagane do przekonwertowania użytkowników federacyjnych na uwierzytelnianie zarządzane, aby użyć synchronizacji skrótów haseł.

## <a name="during-a-disruption"></a>Podczas przerw w działaniu

Jeśli została wybrana do implementowania plan ograniczenia będzie automatycznie przetrwać przerw w działaniu kontroli dostępu do jednego. Jednak jeśli została wybrana, aby utworzyć plan awaryjny, będzie można aktywować zasad gotowości podczas przerw w działaniu kontroli dostępu:

1. Włącz zasady gotowości, które mają przyznaną wybranych użytkowników, dostęp do określonych aplikacji z określonych sieci.
2. Wyłącz regularne zasady na podstawie kontroli.

### <a name="microsoft-recommendations"></a>Zalecenia firmy Microsoft

W zależności od tego, które środki zaradcze lub warunkowych, są używane podczas przerw w działaniu Twoja organizacja może udzielanie dostępu przy użyciu haseł po prostu. Ochrona nie jest znaczne zagrożenie dla bezpieczeństwa, należy dokładnie porównać. Organizacje muszą:

1. Jako część strategii kontroli zmian, każda zmiana i dokumentów poprzedniego stanu, aby można było konieczne przywrócenie początkowej wszelkie warunkowych, zaimplementowane zaraz po kontroli dostępu są w pełni funkcjonalne.
2. Załóżmy, że uczestników złośliwych działań będzie podejmować próby zbiorów hasła za pomocą osłony lub wyłudzeniach złamania hasła podczas wyłączania uwierzytelnianie wieloskładnikowe. Ponadto nieupoważnione osoby mogą już istnieć haseł, których wcześniej nie przyznała dostępu do dowolnego zasobu, który może nastąpić podczas tego okna. Krytyczne użytkowników na przykład dyrektorów firmy częściowo może zmniejszyć to zagrożenie poprzez zresetowanie swoich haseł przed wyłączeniem usługi MFA dla nich.
3. Archiwizowanie wszystkich logowań do identyfikowania, którzy uzyskują dostęp do co czasie, gdy uwierzytelnianie wieloskładnikowe zostało wyłączone.
4. [Klasyfikacja zgłaszane wszystkich zdarzeń o podwyższonym ryzyku](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-sign-ins) podczas tego okna.

## <a name="after-a-disruption"></a>Po przerwaniu

Cofnąć zmiany wprowadzone w ramach aktywowano plan awaryjny po przywróceniu usługi, które spowodowały zakłócenie. 

1. Włącz zasady regularne
2. Wyłącz zasady gotowości. 
3. Trwa wycofywanie wszelkie zmiany dokonane i udokumentowane podczas przerw w działaniu.
4. Jeśli używasz kont dostępu awaryjnego, pamiętaj, aby wygenerować ponownie poświadczenia, a następnie fizycznie Zabezpiecz nowe szczegóły poświadczeń w ramach procedur kont dostępu awaryjnego.
5. W dalszym ciągu [klasyfikacji, wszystkie zdarzenia o podwyższonym ryzyku zgłaszane](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-sign-ins) po przerwaniu dla podejrzanych działań.
6. Odwołaj wszystkie tokeny odświeżania, które zostały wystawione [przy użyciu programu PowerShell](https://docs.microsoft.com/powershell/module/azuread/revoke-azureaduserallrefreshtoken?view=azureadps-2.0) zbiór użytkowników docelowych. Odwoływanie wszystkie tokeny odświeżania jest ważne w przypadku uprzywilejowanych kont używane podczas przerw w działaniu i alternatywnych wymusi ich ponownego uwierzytelnienia i spełnić wymagania kontrolę nad przywróconej zasad.

## <a name="emergency-options"></a>Opcje awaryjnego

 W razie nagłych wypadków i Twoja organizacja wcześniej nie implementuje ograniczające ryzyko lub plan awaryjny, a następnie postępuj zgodnie z zaleceniami w [warunkowych dla blokady użytkownika](#contingencies-for-user-lockout) sekcji, jeśli już używasz dostępu warunkowego zasady można wymusić uwierzytelnianie wieloskładnikowe.
Jeśli Twoja organizacja korzysta z zasad starszej wersji usługi MFA na użytkownika, można rozważyć alternatywne:

1. Jeśli masz adres IP ruchu wychodzącego w sieci firmowej, możesz dodać ich jako zaufane adresy IP, aby włączyć uwierzytelnianie tylko do sieci firmowej.
   1. Jeśli nie masz spis wychodzące adresy IP lub należy włączyć dostęp i spoza sieci firmowej, można dodać całą przestrzeń adresów IPv4 jako zaufane adresy IP, określając 0.0.0.0/1 i 128.0.0.0/1.

>[!IMPORTANT]
 > Jeśli rozszerzenie jest zaufane adresy IP w celu odblokowania dostępu, nie można wygenerować zdarzeń o podwyższonym ryzyku skojarzone z adresami IP (na przykład, więc podróż jest wykluczona lub nieznanych lokalizacji).

>[!NOTE]
 > Konfigurowanie [zaufane adresy IP](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-mfasettings) dla usługi Azure MFA jest dostępna tylko z [licencji usługi Azure AD Premium](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-licensing).

## <a name="learn-more"></a>Dowiedz się więcej

* [Dokumentacja usługi Azure AD Authentication](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfaserver-iis)
* [Zarządzanie kontami administracyjnymi z dostępem awaryjnego w usłudze Azure AD](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-emergency-access)
* [Konfigurowanie nazwanych lokalizacji w usłudze Azure Active Directory](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations)
  * [Set-MsolDomainFederationSettings](https://docs.microsoft.com/powershell/module/msonline/set-msoldomainfederationsettings?view=azureadps-1.0)
* [Konfigurowanie urządzeń przyłączonych do usługi Azure Active Directory hybrydowe](https://docs.microsoft.com/azure/active-directory/devices/hybrid-azuread-join-plan)
* [Przewodnik wdrażania funkcji Windows Hello dla firm](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-deployment-guide)
  * [Wskazówki dotyczące haseł — badań firmy Microsoft](https://research.microsoft.com/pubs/265143/microsoft_password_guidance.pdf)
* [Jakie są warunki dostępu warunkowego usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/conditions)
* [Jakie są mechanizmy kontroli dostępu w funkcji dostępu warunkowego usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/controls)
