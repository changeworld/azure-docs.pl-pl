---
title: Tworzenie strategii zarządzania elastyczną kontrolą dostępu — Usługa Azure AD
description: Niniejszy dokument zawiera wytyczne dotyczące strategii, które organizacja powinna przyjąć w celu zapewnienia odporności w celu zmniejszenia ryzyka blokady podczas nieprzewidzianych zakłóceń
services: active-directory
author: martincoetzer
manager: daveba
tags: azuread
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.workload: identity
ms.date: 01/29/2020
ms.author: martinco
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0ca5817e744ff81efcd549bc328d7ce5eeedb2d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76908738"
---
# <a name="create-a-resilient-access-control-management-strategy-with-azure-active-directory"></a>Tworzenie strategii zarządzania elastyczną kontrolą dostępu za pomocą usługi Azure Active Directory

>[!NOTE]
> The information contained in this document represents the current view of Microsoft Corporation on the issues discussed as of the date of publication. Ponieważ firma Microsoft musi reagować na zmieniające się warunki rynkowe, nie powinna być interpretowana jako zobowiązanie ze strony firmy Microsoft, a firma Microsoft nie może zagwarantować dokładności jakichkolwiek informacji przedstawionych po dacie publikacji.

Organizacje, które korzystają z jednej kontroli dostępu, takiej jak uwierzytelnianie wieloskładnikowe (MFA) lub pojedyncza lokalizacja sieciowa w celu zabezpieczenia swoich systemów informatycznych, są podatne na błędy dostępu do swoich aplikacji i zasobów, jeśli ta pojedyncza kontrola dostępu stanie się niedostępna lub nieprawidłowo skonfigurowane. Na przykład klęska żywiołowa może spowodować niedostępność dużych segmentów infrastruktury telekomunikacyjnej lub sieci firmowych. Takie zakłócenie może uniemożliwić użytkownikom końcowym i administratorom zalogowanie się.

Ten dokument zawiera wskazówki dotyczące strategii, które organizacja powinna przyjąć w celu zapewnienia odporności w celu zmniejszenia ryzyka blokady podczas nieprzewidzianych zakłóceń w następujących scenariuszach:

 1. Organizacje mogą zwiększyć swoją odporność, aby zmniejszyć ryzyko blokady **przed zakłóceniem,** implementując strategie łagodzenia zmiany klimatu lub plany awaryjne.
 2. Organizacje mogą nadal uzyskiwać dostęp do aplikacji i zasobów, które wybierają **podczas przerw,** dzięki wprowadzonym strategiom łagodzenia zmiany klimatu i planom awaryjnym.
 3. Organizacje powinny upewnić się, że zachowują informacje, takie jak dzienniki, **po zakłóceniu** i przed wycofaniem wszelkich zaimplementowanych przez nieprzewidzianych sytuacji.
 4. Organizacje, które nie wdrożyły strategii zapobiegania lub alternatywnych planów, mogą być w stanie wdrożyć **opcje awaryjne,** aby poradzić sobie z zakłóceniami.

## <a name="key-guidance"></a>Najważniejsze wskazówki

W tym dokumencie znajdują się cztery kluczowe dania na wynos:

* Unikaj blokady administratora, korzystając z kont dostępu awaryjnego.
* Implementowanie usługi MFA przy użyciu dostępu warunkowego (CA), a nie usługi MFA dla użytkownika.
* Ograniczanie blokady użytkownika przy użyciu wielu formantów dostępu warunkowego (CA).
* Załagodzić blokadę użytkownika przez inicjowanie obsługi administracyjnej wiele metod uwierzytelniania lub ich odpowiedników dla każdego użytkownika.

## <a name="before-a-disruption"></a>Przed zakłóceniem

Łagodzenie rzeczywistych zakłóceń musi być głównym celem organizacji w radzeniu sobie z problemami kontroli dostępu, które mogą wystąpić. Łagodzenie obejmuje planowanie rzeczywistego zdarzenia oraz strategie wdrażania, aby upewnić się, że kontrola dostępu i operacje nie są naruszone podczas przerw.

### <a name="why-do-you-need-resilient-access-control"></a>Dlaczego potrzebna jest odporna kontrola dostępu?

 Tożsamość to płaszczyzna kontrolna użytkowników uzyskujących dostęp do aplikacji i zasobów. System tożsamości kontroluje użytkowników i na jakich warunkach, takich jak kontrola dostępu lub wymagania uwierzytelniania, użytkownicy uzyskują dostęp do aplikacji. Jeśli jedno lub więcej wymagań dotyczących uwierzytelniania lub kontroli dostępu nie jest dostępnych do uwierzytelniania przez użytkowników z powodu nieprzewidzianych okoliczności, organizacje mogą wystąpić z jednym lub jednym z następujących problemów:

* **Blokada administratora:** Administratorzy nie mogą zarządzać dzierżawą ani usługami.
* **Blokada użytkownika:** Użytkownicy nie mogą uzyskiwać dostępu do aplikacji ani zasobów.

### <a name="administrator-lockout-contingency"></a>Nieprzewidziane blokady administratora

Aby odblokować dostęp administratora do dzierżawy, należy utworzyć konta dostępu awaryjnego. Te konta dostępu awaryjnego, znane również jako konta *typu break glass,* umożliwiają dostęp do zarządzania konfiguracją usługi Azure AD, gdy normalne procedury dostępu do konta uprzywilejowanego nie są dostępne. Zgodnie z [zaleceniami dotyczącymi dostępu awaryjnego]( https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-emergency-access)należy utworzyć co najmniej dwa konta dostępu awaryjnego.

### <a name="mitigating-user-lockout"></a>Ograniczanie blokady użytkownika

 Aby zmniejszyć ryzyko blokady użytkownika, należy użyć zasad dostępu warunkowego z wieloma formantami, aby dać użytkownikom możliwość wyboru sposobu uzyskiwania dostępu do aplikacji i zasobów. Dając użytkownikowi wybór między, na przykład, logowanie się za pomocą usługi MFA **lub** logowanie się z zarządzanego urządzenia **lub** logowanie się z sieci firmowej, jeśli jeden z formantów dostępu jest niedostępny, użytkownik ma inne opcje, aby kontynuować pracę.

#### <a name="microsoft-recommendations"></a>Zalecenia firmy Microsoft

Uwzględnij następujące kontrolki dostępu w istniejących zasadach dostępu warunkowego dla organizacji:

1. Aprowizuj wiele metod uwierzytelniania dla każdego użytkownika, który polega na różnych kanałach komunikacji, na przykład aplikacji Microsoft Authenticator (internetowej), tokenie OATH (generowanym na urządzeniu) i SMS (telefonicznym).
2. Wdrażanie funkcji Windows Hello dla firm na urządzeniach z systemem Windows 10 w celu spełnienia wymagań usługi MFA bezpośrednio z logowania się na urządzeniu.
3. Korzystanie z zaufanych urządzeń za pośrednictwem urządzeń [przyłączu hybrydowego usługi Azure AD](https://docs.microsoft.com/azure/active-directory/devices/overview) lub [zarządzanych usług Microsoft Intune](https://docs.microsoft.com/intune/planning-guide). Zaufane urządzenia poprawią środowisko użytkownika, ponieważ samo zaufane urządzenie może spełnić wymagania dotyczące silnego uwierzytelniania zasad bez wyzwania usługi MFA dla użytkownika. Usługa MFA będzie następnie wymagana podczas rejestrowania nowego urządzenia i uzyskiwania dostępu do aplikacji lub zasobów z niezaufanych urządzeń.
4. Użyj zasad opartych na ryzyku ochrony tożsamości usługi Azure AD, które uniemożliwiają dostęp, gdy użytkownik lub logowanie jest zagrożone zamiast stałych zasad usługi MFA.

>[!NOTE]
> Zasady oparte na ryzyku wymagają licencji [usługi Azure AD Premium P2.](https://azure.microsoft.com/pricing/details/active-directory/)

W poniższym przykładzie opisano zasady, które należy utworzyć, aby zapewnić elastyczną kontrolę dostępu dla użytkownika, aby uzyskać dostęp do swoich aplikacji i zasobów. W tym przykładzie będzie wymagać grupy zabezpieczeń **AppUsers** z użytkowników docelowych, które mają być dostępne, grupa o nazwie **CoreAdmins** z podstawowych administratorów i grupy o nazwie **EmergencyAccess** z kont dostępu awaryjnego.
Ten przykładowy zestaw zasad zapewni wybranym użytkownikom w **użyczeń aplikacji**dostęp do wybranych aplikacji, jeśli łączą się z zaufanego urządzenia lub zapewniają silne uwierzytelnianie, na przykład uwierzytelnianie wieloskładnikowe. Nie obejmuje kont awaryjnych i podstawowych administratorów.

**Zasady ograniczania ryzyka urzędu certyfikacji ustawione:**

* Zasady 1: Blokowanie dostępu do osób spoza grup docelowych
  * Użytkownicy i grupy: uwzględnij wszystkich użytkowników. Wyklucz AppUsers, CoreAdmins i EmergencyAccess
  * Aplikacje w chmurze: uwzględnij wszystkie aplikacje
  * Warunki: (Brak)
  * Kontrola nad dotacjami: blok
* Zasady 2: Udziel dostępu do aplikacji Użytkownicy, którzy wymagają usługi MFA lub zaufanego urządzenia.
  * Użytkownicy i grupy: uwzględnij użytkowników aplikacji. Wyklucz CoreAdmins i EmergencyAccess
  * Aplikacje w chmurze: uwzględnij wszystkie aplikacje
  * Warunki: (Brak)
  * Kontrola nad przyznaniem dotacji: Udziel dostępu, wymagaj uwierzytelniania wieloskładnikowego, wymagaj zgodności urządzenia. W przypadku wielu formantów: wymagaj jednego z wybranych formantów.

### <a name="contingencies-for-user-lockout"></a>Nieprzewidziane sytuacje blokady użytkownika

Alternatywnie organizacja może również tworzyć zasady awaryjne. Aby utworzyć zasady awaryjne, należy zdefiniować kryteria kompromisu między ciągłością działania, kosztem operacyjnym, kosztami finansowymi i zagrożeniami bezpieczeństwa. Na przykład zasady awaryjne można aktywować tylko dla podzbioru użytkowników, dla podzbioru aplikacji, dla podzbioru klientów lub z podzbioru lokalizacji. Zasady awaryjne dadzą administratorom i użytkownikom końcowym dostęp do aplikacji i zasobów podczas przerwy, gdy nie zaimplementowano metody ograniczania ryzyka.
Zrozumienie ekspozycji podczas zakłóceń pomaga zmniejszyć ryzyko i jest kluczową częścią procesu planowania. Aby utworzyć plan awaryjny, należy najpierw określić następujące wymagania biznesowe organizacji:

1. Określ swoje aplikacje o znaczeniu krytycznym z wyprzedzeniem: Jakie są aplikacje, do których musisz udzielić dostępu, nawet przy niższym ryzyku/bezpieczeństwie? Stwórz listę tych aplikacji i upewnij się, że inni interesariusze (firma, bezpieczeństwo, prawnicy, przywództwo) zgadzają się, że jeśli cała kontrola dostępu zniknie, te aplikacje nadal muszą być uruchamiane. Prawdopodobnie skończysz z kategoriami:
   * **Aplikacje o znaczeniu krytycznym kategorii 1,** które nie mogą być niedostępne dłużej niż kilka minut, na przykład aplikacje, które bezpośrednio wpływają na przychody organizacji.
   * **Kategoria 2 Ważne aplikacje,** które firma musi być dostępna w ciągu kilku godzin.
   * **Aplikacje o niskim priorytecie kategorii 3,** które mogą wytrzymać kilkudniowe zakłócenia.
2. W przypadku aplikacji w kategorii 1 i 2 firma Microsoft zaleca wstępnie zaplanować, jakiego typu poziom dostępu ma zostać dozwolony:
   * Czy chcesz zezwolić na pełny dostęp lub sesję z ograniczeniami, na przykład ograniczanie pobierania?
   * Czy chcesz zezwolić na dostęp do części aplikacji, ale nie do całej aplikacji?
   * Czy chcesz zezwolić pracownikowi na dostęp do informacji i zablokować dostęp administratora do czasu przywrócenia kontroli dostępu?
3. W przypadku tych aplikacji firma Microsoft zaleca również zaplanowanie, które drogi dostępu zostaną celowo otwarte, a które zostaną zamknięte:
   * Czy chcesz zezwolić przeglądarce na dostęp i blokowanie klientów bogatych, którzy mogą zapisywać dane w trybie offline?
   * Czy chcesz zezwolić na dostęp tylko użytkownikom w sieci firmowej i zablokować zewnętrznych użytkowników?
   * Czy chcesz zezwolić na dostęp z niektórych krajów lub regionów tylko podczas zakłóceń?
   * Czy chcesz, aby zasady dotyczące zasad awaryjnych, zwłaszcza w przypadku aplikacji o znaczeniu krytycznym, zakończyły się niepowodzeniem lub zakończyły się powodzeniem, jeśli alternatywna kontrola dostępu nie jest dostępna?

#### <a name="microsoft-recommendations"></a>Zalecenia firmy Microsoft

Zasady dostępu warunkowego awaryjnego to **wyłączone zasady,** które pomijają usługi Azure MFA, usługi MFA innych firm, oparte na ryzyku lub na urządzeniach. Następnie, gdy organizacja zdecyduje się aktywować plan awaryjny, administratorzy mogą włączyć zasady i wyłączyć regularne zasady oparte na kontroli.

>[!IMPORTANT]
> Wyłączenie zasad, które wymuszają bezpieczeństwo użytkowników, nawet tymczasowo, zmniejszy postawę zabezpieczeń, gdy plan awaryjny jest w miejscu.

* Skonfiguruj zestaw zasad rezerwowych, jeśli zakłócenie w jednym typie poświadczeń lub jeden mechanizm kontroli dostępu wpływa na dostęp do aplikacji. Skonfiguruj zasady w stanie wyłączonym, który wymaga sprzężenia domeny jako formantu, jako kopii zapasowej dla aktywnej zasady, która wymaga dostawcy usługi MFA innej firmy.
* Zmniejsz ryzyko zgadywania haseł przez złych aktorów, gdy usługa MFA nie jest wymagana, postępując zgodnie z praktykami zawartymi w opracowaniu zawierającym [wskazówki dotyczące haseł.](https://aka.ms/passwordguidance)
* Wdrażanie [samoobsługowego resetowania haseł usługi Azure AD (SSPR)](https://docs.microsoft.com/azure/active-directory/authentication/quickstart-sspr) i [ochrony hasłem usługi Azure AD,](https://docs.microsoft.com/azure/active-directory/authentication/howto-password-ban-bad-on-premises-deploy) aby upewnić się, że użytkownicy nie używają wspólnego hasła i terminów, które chcesz zablokować.
* Użyj zasad, które ograniczają dostęp w aplikacjach, jeśli określony poziom uwierzytelniania nie zostanie osiągnięty, zamiast po prostu wrócić do pełnego dostępu. Przykład:
  * Konfigurowanie zasad tworzenia kopii zapasowych, które wysyłają oświadczenie o ograniczonej sesji do programów Exchange i SharePoint.
  * Jeśli twoja organizacja korzysta z programu Microsoft Cloud App Security, należy rozważyć powrót do zasad, które angażują MCAS, a następnie MCAS zezwala na dostęp tylko do odczytu, ale nie jest przekazywany.
* Nazwij swoje zasady, aby upewnić się, że łatwo je znaleźć podczas zakłóceń. Podaj następujące elementy w nazwie zasad:
  * *Numer etykiety* dla zasad.
  * Tekst do pokazania, ta zasada dotyczy tylko sytuacji awaryjnych. Na przykład: **WŁĄCZ W NAGŁYCH WYPADKACH**
  * *Zakłócenia,* które stosuje się do. Na przykład: **Podczas zakłóceń usługi MFA**
  * *Numer sekwencyjny,* aby wyświetlić kolejność, w której należy aktywować zasady.
  * *Aplikacje,* do które się odnosi.
  * *Formanty,* które będą stosowane.
  * *Warunki, jakich* to wymaga.
  
Ten standard nazewnictwa dla zasad awaryjnych będzie następujący: 

```
EMnnn - ENABLE IN EMERGENCY: [Disruption][i/n] - [Apps] - [Controls] [Conditions]
```

Poniższy przykład: **Przykład A — zasady urzędu certyfikacji awaryjnego w celu przywrócenia dostępu do aplikacji współpracy o znaczeniu krytycznym**— jest typowym awaryjnym przypadkiem korporacyjnym. W tym scenariuszu organizacja zazwyczaj wymaga usługi MFA dla wszystkich usług Exchange Online i usługi SharePoint Dostęp online, a zakłócenia w tym przypadku jest dostawcą usługi MFA dla klienta ma awarię (czy usługi Azure MFA, dostawcy usługi MFA w środowisku lokalnym lub usługi MFA innych firm). Ta zasada zmniejsza tę awarię, zezwalając określonym użytkownikom docelowym na dostęp do tych aplikacji z zaufanych urządzeń z systemem Windows tylko wtedy, gdy uzyskują dostęp do aplikacji z ich zaufanej sieci firmowej. Będzie również wykluczyć konta awaryjne i podstawowych administratorów z tych ograniczeń. Użytkownicy kierowani uzyskają następnie dostęp do usługi Exchange Online i usługi SharePoint Online, podczas gdy inni użytkownicy nadal nie będą mieli dostępu do aplikacji z powodu awarii. W tym przykładzie będzie wymagać nazwanej lokalizacji **sieciowej CorpNetwork** i grupy zabezpieczeń **ContingencyAccess** z użytkownikami docelowymi, grupy o nazwie **CoreAdmins** z podstawowymi administratorami oraz grupy o nazwie **EmergencyAccess** z kontami dostępu awaryjnego. Nieprzewidziane wymaga czterech zasad, aby zapewnić pożądany dostęp. 

**Przykład A — zasady urzędu certyfikacji awaryjnego w celu przywrócenia dostępu do aplikacji współpracy o znaczeniu krytycznym:**

* Zasady 1: Wymagaj urządzeń przyłączonych do domeny dla programu Exchange i sharepoint
  * Nazwa: EM001 - WŁĄCZ W NAGŁYCH WYPADKACH: Zakłócenia usługi MFA[1/4] - Program Exchange SharePoint — wymaga hybrydowego dołączania do usługi Azure AD
  * Użytkownicy i grupy: uwzględnij awaryjneaccess. Wyklucz CoreAdmins i EmergencyAccess
  * Aplikacje w chmurze: exchange online i sharepoint online
  * Warunki: Dowolny
  * Kontrola nad przyznaniem dotacji: wymagaj przyłączanych do domeny
  * Stan: Wyłączone
* Zasady 2: Blokowanie platform innych niż Windows
  * Nazwa: EM002 - WŁĄCZ W NAGŁYCH WYPADKACH: Zakłócenia MFA[2/4] - Exchange SharePoint - Blok dostępu z wyjątkiem systemu Windows
  * Użytkownicy i grupy: uwzględnij wszystkich użytkowników. Wyklucz CoreAdmins i EmergencyAccess
  * Aplikacje w chmurze: exchange online i sharepoint online
  * Warunki: Platforma urządzeń obejmuje wszystkie platformy, wyklucz windowsa
  * Kontrola nad dotacjami: blok
  * Stan: Wyłączone
* Zasady 3: Blokowanie sieci innych niż CorpNetwork
  * Nazwa: EM003 - WŁĄCZ W NAGŁYCH WYPADKACH: Zakłócenia MFA[3/4] - Exchange SharePoint - Blok dostępu z wyjątkiem sieci firmowej
  * Użytkownicy i grupy: uwzględnij wszystkich użytkowników. Wyklucz CoreAdmins i EmergencyAccess
  * Aplikacje w chmurze: exchange online i sharepoint online
  * Warunki: Lokalizacje Obejmują dowolną lokalizację, wyklucz corpnetwork
  * Kontrola nad dotacjami: blok
  * Stan: Wyłączone
* Polityka 4: Blokowanie EAS jawnie
  * Nazwa: EM004 - WŁĄCZ W NAGŁYCH WYPADKACH: Zakłócenia MFA[4/4] - Exchange - Block EAS dla wszystkich użytkowników
  * Użytkownicy i grupy: uwzględnij wszystkich użytkowników
  * Aplikacje w chmurze: uwzględnij usługę Exchange Online
  * Warunki: Aplikacje klienckie: Exchange Active Sync
  * Kontrola nad dotacjami: blok
  * Stan: Wyłączone

Kolejność aktywacji:

1. Wyklucz awaryjneaccess, CoreAdmins i EmergencyAccess z istniejących zasad usługi MFA. Sprawdź, czy użytkownik w programie ContingencyAccess może uzyskać dostęp do usługi SharePoint Online i usługi Exchange Online.
2. Włącz zasady 1: Sprawdź, czy użytkownicy na urządzeniach przyłączonych do domeny, którzy nie znajdują się w grupach wykluczeń, mogą uzyskiwać dostęp do usług Exchange Online i usługi SharePoint Online. Sprawdź, czy użytkownicy w grupie Wyklucz mogą uzyskiwać dostęp do usługi SharePoint Online i Exchange z dowolnego urządzenia.
3. Włącz zasady 2: Sprawdź, czy użytkownicy, którzy nie należą do grupy wykluczeń, nie mogą uzyskać dostępu do usługi SharePoint Online i usługi Exchange Online ze swoich urządzeń przenośnych. Sprawdź, czy użytkownicy w grupie Wyklucz mogą uzyskiwać dostęp do programu SharePoint i programu Exchange z dowolnego urządzenia (Windows/iOS/Android).
4. Włącz zasady 3: Sprawdź, czy użytkownicy, którzy nie należą do grup wykluczeń, nie mogą uzyskać dostępu do programu SharePoint i programu Exchange poza siecią firmową, nawet na komputerze przyłączanym do domeny. Sprawdź, czy użytkownicy w grupie Wyklucz mogą uzyskiwać dostęp do programu SharePoint i programu Exchange z dowolnej sieci.
5. Włącz zasady 4: Sprawdź, czy wszyscy użytkownicy nie mogą uzyskać usługi Exchange Online z natywnych aplikacji pocztowych na urządzeniach przenośnych.
6. Wyłącz istniejące zasady usługi MFA dla usług SharePoint Online i Exchange Online.

W tym następnym przykładzie **przykład B — awaryjne zasady urzędu certyfikacji, aby umożliwić mobilny dostęp do Salesforce**, dostęp aplikacji firmy jest przywracany. W tym scenariuszu klient zazwyczaj wymaga, aby ich pracownicy sprzedaży dostęp do Salesforce (skonfigurowany do logowania jednokrotnego za pomocą usługi Azure AD) z urządzeń przenośnych, aby były dozwolone tylko ze zgodnych urządzeń. Zakłócenie w tym przypadku jest to, że istnieje problem z oceną zgodności urządzenia i awaria dzieje się w newralgicznym czasie, gdy zespół sprzedaży potrzebuje dostępu do Salesforce, aby zamknąć transakcje. Te zasady awaryjne zapewnią krytycznym użytkownikom dostęp do Salesforce z urządzenia mobilnego, dzięki czemu będą mogli nadal zamykać transakcje i nie zakłócać działalności. W tym przykładzie **SalesforceContingency** zawiera wszystkich pracowników sprzedaży, którzy muszą zachować dostęp i **SalesAdmins** zawiera niezbędnych administratorów Salesforce.

**Przykład B — zasady urzędu certyfikacji awaryjnego:**

* Zasady 1: Blokowanie wszystkich osób nie należy do zespołu SalesContingency
  * Nazwa: EM001 - WŁĄCZ W NAGŁYCH WYPADKACH: Zakłócenia zgodności z urządzeniami[1/2] - Salesforce - Blokuj wszystkich użytkowników z wyjątkiem SalesforceContingency
  * Użytkownicy i grupy: uwzględnij wszystkich użytkowników. Wyklucz SalesAdmins i SalesforceContingency
  * Aplikacje w chmurze: Salesforce.
  * Warunki: Brak
  * Kontrola nad dotacjami: blok
  * Stan: Wyłączone
* Polityka 2: Blokowanie zespołu sprzedaży z dowolnej platformy innej niż mobilna (w celu zmniejszenia powierzchni ataku)
  * Nazwa: EM002 - ENABLE IN EMERGENCY: Device Compliance Disruption[2/2] - Salesforce - Blokuj wszystkie platformy z wyjątkiem iOS i Android
  * Użytkownicy i grupy: uwzględnij SalesforceContingency. Wyklucz SalesAdmins
  * Aplikacje w chmurze: Salesforce
  * Warunki: Platforma urządzeń obejmuje wszystkie platformy, wyklucz iOS i Android
  * Kontrola nad dotacjami: blok
  * Stan: Wyłączone

Kolejność aktywacji:

1. Wyklucz SalesAdmins i SalesforceContingency z istniejących zasad zgodności urządzeń dla Salesforce. Sprawdź, czy użytkownik w grupie SalesforceContingency może uzyskać dostęp do salesforce.
2. Włącz zasady 1: Sprawdź, czy użytkownicy spoza SalesContingency nie mogą uzyskać dostępu do Salesforce. Sprawdź, czy użytkownicy w SalesAdmins i SalesforceContingency mogą uzyskać dostęp do Salesforce.
3. Włącz zasady 2: Sprawdź, czy użytkownicy w grupie SalesContingency nie mogą uzyskiwać dostępu do salesforce z ich laptopów z systemem Windows/Mac, ale nadal mogą uzyskiwać dostęp z urządzeń przenośnych. Sprawdź SalesAdmin nadal można uzyskać dostęp Salesforce z dowolnego urządzenia.
4. Wyłącz istniejące zasady zgodności urządzeń dla Salesforce.

### <a name="deploy-password-hash-sync-even-if-you-are-federated-or-use-pass-through-authentication"></a>Wdrażanie synchronizacji skrótów haseł, nawet jeśli jesteś sfederowany lub korzystasz z uwierzytelniania przekazywanego

Blokada użytkownika może również wystąpić, jeśli spełnione są następujące warunki:

- Organizacja używa rozwiązania tożsamości hybrydowej z uwierzytelnianiem przekazu lub federacją.
- Lokalne systemy tożsamości (takie jak usługa Active Directory, usługi AD FS lub składnik zależny) są niedostępne. 
 
Aby być bardziej odporne, organizacja powinna [włączyć synchronizację skrótów haseł,](https://docs.microsoft.com/azure/security/fundamentals/choose-ad-authn)ponieważ umożliwia [przełączanie się do przy użyciu synchronizacji skrótów haseł,](https://docs.microsoft.com/azure/active-directory/hybrid/plan-connect-user-signin) jeśli lokalne systemy tożsamości są wyłączone.

#### <a name="microsoft-recommendations"></a>Zalecenia firmy Microsoft
 Włącz synchronizację skrótów haseł za pomocą kreatora usługi Azure AD Connect, niezależnie od tego, czy organizacja używa uwierzytelniania federacyjnego, czy uwierzytelniania przekazywanego.

>[!IMPORTANT]
> Nie jest wymagane, aby przekonwertować użytkowników z uwierzytelniań sfederowanych do uwierzytelniania zarządzanego do korzystania z synchronizacji skrótów haseł.

## <a name="during-a-disruption"></a>Podczas zakłóceń

Jeśli zdecydujesz się na wdrożenie planu łagodzenia, będzie można automatycznie przetrwać pojedyncze zakłócenia kontroli dostępu. Jeśli jednak zdecydujesz się utworzyć plan awaryjny, będziesz mógł aktywować zasady awaryjne podczas zakłóceń kontroli dostępu:

1. Włącz zasady awaryjne, które zapewniają użytkownikom docelowym dostęp do określonych aplikacji z określonych sieci.
2. Wyłącz regularne zasady oparte na kontroli.

### <a name="microsoft-recommendations"></a>Zalecenia firmy Microsoft

W zależności od tego, które środki zaradcze lub nieprzewidziane są używane podczas zakłóceń, organizacja może udzielać dostępu tylko za pomocą haseł. Żadne zabezpieczenie nie stanowi znacznego zagrożenia dla bezpieczeństwa, które należy starannie rozważyć. Organizacje muszą:

1. W ramach strategii kontroli zmian dokumentuj każdą zmianę i poprzedni stan, aby móc wycofać wszelkie nieprzewidziane rozwiązania, gdy tylko kontrole dostępu będą w pełni operacyjne.
2. Załóżmy, że złośliwi aktorzy będą próbować zbierać hasła za pomocą ataków typu spray has lub phishing podczas wyłączania usługi MFA. Ponadto złe podmioty mogą już mieć hasła, które wcześniej nie udzieliły dostępu do żadnego zasobu, który można podjąć podczas tego okna. W przypadku użytkowników krytycznych, takich jak kadra kierownicza, można częściowo ograniczyć to ryzyko, resetując ich hasła przed wyłączeniem usługi MFA dla nich.
3. Zarchiwizuj wszystkie działania logowania, aby zidentyfikować, kto uzyskuje dostęp do tego, co w czasie, gdy usługa MFA została wyłączona.
4. [Klasyfikowanie wszystkich przypadków wykrywania ryzyka zgłoszonych](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-sign-ins) w tym oknie.

## <a name="after-a-disruption"></a>Po zakłóceniu

Cofnij zmiany wprowadzone w ramach aktywowanego planu awaryjnego po przywróceniu usługi, która spowodowała zakłócenia. 

1. Włącz regularne zasady
2. Wyłącz zasady awaryjne. 
3. Wycofaj wszelkie inne zmiany wprowadzone i udokumentowane podczas zakłóceń.
4. Jeśli użyto konta dostępu awaryjnego, pamiętaj, aby ponownie wygenerować poświadczenia i fizycznie zabezpieczyć szczegóły nowych poświadczeń w ramach procedur konta dostępu awaryjnego.
5. Kontynuuj [klasyfikowanie wszystkich wykrywania ryzyka zgłoszonych](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-sign-ins) po zakłóceniu podejrzanej aktywności.
6. Odwołaj wszystkie tokeny odświeżania, które zostały wydane [przy użyciu programu PowerShell,](https://docs.microsoft.com/powershell/module/azuread/revoke-azureaduserallrefreshtoken?view=azureadps-2.0) aby kierować reklamy na zestaw użytkowników. Odwoływanie wszystkich tokenów odświeżania jest ważne dla uprzywilejowanych kont używanych podczas zakłóceń i wykonanie tego spowoduje, że zmusi je do ponownego uwierzytelnienia i spełnienia kontroli przywróconych zasad.

## <a name="emergency-options"></a>Opcje awaryjne

 W przypadku sytuacji awaryjnej i organizacji nie wcześniej zaimplementować środki zaradcze lub plan awaryjny, a następnie postępuj zgodnie z zaleceniami w [sekcji Awaryjne dla blokady użytkownika,](#contingencies-for-user-lockout) jeśli już używają zasad dostępu warunkowego do wymuszania usługi MFA.
Jeśli organizacja korzysta ze starszych zasad usługi MFA dla użytkowników, można rozważyć następującą alternatywę:

1. Jeśli masz wychodzący adres IP sieci firmowej, możesz dodać je jako zaufane adresy IP, aby włączyć uwierzytelnianie tylko w sieci firmowej.
   1. Jeśli nie masz spisu wychodzących adresów IP lub wymagane jest włączenie dostępu wewnątrz i na zewnątrz sieci firmowej, możesz dodać całą przestrzeń adresową IPv4 jako zaufane adresy IP, określając 0.0.0.0/1 i 128.0.0.0/1.

>[!IMPORTANT]
 > Jeśli poszerzysz zaufane adresy IP, aby odblokować dostęp, nie zostaną wygenerowane wykrywanie ryzyka związane z adresami IP (na przykład niemożliwe podróżowanie lub nieznane lokalizacje).

>[!NOTE]
 > Konfigurowanie [zaufanych usług IP](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-mfasettings) dla usługi Azure MFA jest dostępne tylko z [licencjami usługi Azure AD Premium.](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-licensing)

## <a name="learn-more"></a>Dowiedz się więcej

* [Dokumentacja uwierzytelniania usługi Azure AD](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfaserver-iis)
* [Zarządzanie kontami administracyjnymi dostępu awaryjnego w usłudze Azure AD](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-emergency-access)
* [konfigurowanie nazwanych lokalizacji w usłudze Azure Active Directory](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations)
  * [Set-MsolDomainFederationSettings](https://docs.microsoft.com/powershell/module/msonline/set-msoldomainfederationsettings?view=azureadps-1.0)
* [Jak skonfigurować urządzenia połączone z hybrydową usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/devices/hybrid-azuread-join-plan)
* [Przewodnik wdrażania funkcji Windows Hello dla firm](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-deployment-guide)
  * [Wskazówki dotyczące haseł — Microsoft Research](https://research.microsoft.com/pubs/265143/microsoft_password_guidance.pdf)
* [Jakie są warunki w usłudze Azure Active Directory Dostęp warunkowy?](https://docs.microsoft.com/azure/active-directory/conditional-access/conditions)
* [Co to są formanty dostępu w usłudze Azure Active Directory Dostęp warunkowy?](https://docs.microsoft.com/azure/active-directory/conditional-access/controls)
