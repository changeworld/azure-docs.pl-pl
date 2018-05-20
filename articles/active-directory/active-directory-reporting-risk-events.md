---
title: Zdarzenia o podwyższonym ryzyku Azure Active Directory | Dokumentacja firmy Microsoft
description: Ten artice udostępnia szczegółowy przegląd zdarzeń o podwyższonym ryzyku są.
services: active-directory
keywords: ochronę tożsamości usługi Azure active directory, zabezpieczeń, ryzyka, poziom ryzyka, luki w zabezpieczeniach, zasady zabezpieczeń
author: MarkusVi
manager: mtillman
ms.assetid: fa2c8b51-d43d-4349-8308-97e87665400b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/14/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: e883caa63bde26e13234dde949ce4517b328e3a5
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
---
# <a name="azure-active-directory-risk-events"></a>Zdarzenia o podwyższonym ryzyku Azure Active Directory

Większość naruszeń zabezpieczeń ma miejsce, gdy osoby atakujące uzyskania dostępu do środowiska kradzieży tożsamości użytkownika. Odnajdywanie złamany tożsamości jest bez łatwe zadania. Usługi Azure Active Directory korzysta z algorytmów uczenia maszynowego adaptacyjną i heurystyki do wykrycia podejrzanych działań, które są związane z kontami użytkowników. Każdy wykryto podejrzane działania są przechowywane w rekordzie o nazwie *zdarzenia ryzyka*.

Obecnie usługa Azure Active Directory wykrywa sześć typów zdarzeń o podwyższonym ryzyku:

- [Użytkownicy z ujawnione poświadczenia](#leaked-credentials) 
- [Logowania z anonimowych adresów IP](#sign-ins-from-anonymous-ip-addresses) 
- [Niemożliwa podróż do nietypowych lokalizacji](#impossible-travel-to-atypical-locations) 
- [Logowania z zainfekowanych urządzeń](#sign-ins-from-infected-devices) 
- [Logowania z adresów IP związanych z podejrzanymi działaniami](#sign-ins-from-ip-addresses-with-suspicious-activity) 
- [Logowania z nieznanych lokalizacji](#sign-in-from-unfamiliar-locations) 


![Zdarzenia ryzyka](./media/active-directory-reporting-risk-events/91.png)

Wgląd w informacje o uzyskać zdarzenie wykryte zagrożenie jest powiązany z subskrypcją usługi Azure AD. Możesz uzyskać bardziej szczegółowe informacje o wszystkich wykryć podstawowej wersji Azure AD Premium P2. Z wersji Azure AD Premium P1 wykryć, które nie są objęte licencję są wyświetlane jako zdarzenie ryzyka **logowanie z dodatkowych ryzyka wykryto**.


Ten artykuł zawiera możesz są szczegółowe omówienie jakie zdarzenia o podwyższonym ryzyku i jak ich używać do ochrony Twojej tożsamości usługi Azure AD.


## <a name="risk-event-types"></a>Rodzaje ryzykownych zdarzeń

Właściwość typu zdarzenia ryzyko jest identyfikator podejrzanego działania rekordu zdarzenia ryzyka została utworzona dla.

Ciągłe inwestycje firmy Microsoft w procesie wykrywania prowadzić do:

- Ulepszenia dokładność wykrywania istniejących zdarzeń ryzyka 
- Nowe typy zdarzeń ryzyka, które zostaną dodane w przyszłości

### <a name="leaked-credentials"></a>Ujawnione poświadczenia

Przestępców komputerowym przestępcom złamania hasła prawidłowy autoryzowanych użytkowników, często udostępnić tych poświadczeń. Zwykle odbywa się przez firmę ich publicznie ciemny witryn sieci web i wklejania lub handlem lub sprzedaży poświadczeń na czarnym rynku. Microsoft ujawnione poświadczenia usługi uzyskuje nazwę użytkownika / hasło pary przez monitorowanie publicznego i ciemny witryn sieci web i Praca z:

- Badacze
- Organom ścigania
- Zespoły zabezpieczeń w firmie Microsoft
- Innych zaufanych źródeł. 

Gdy usługa wymaga nazwy użytkownika / pary hasła one są porównywane z bieżącym prawidłowe poświadczenia użytkowników usługi AAD. Po znalezieniu dopasowania, oznacza to, że naruszono bezpieczeństwo hasła użytkownika, a *ujawnione poświadczenia ryzyka zdarzeń* jest tworzony.

### <a name="sign-ins-from-anonymous-ip-addresses"></a>Logowania z anonimowych adresów IP

Ten typ zdarzenia ryzyka identyfikuje użytkowników, którzy zalogowali się pomyślnie z adresu IP, który został zidentyfikowany jako adres IP anonimowy serwer proxy. Te serwery proxy są używane przez różne osoby, aby ukryć adres IP urządzenia, które mogą być używane do złośliwymi działaniami.


### <a name="impossible-travel-to-atypical-locations"></a>Niemożliwa podróż do nietypowych lokalizacji

Ten typ zdarzenia ryzyka identyfikuje dwa logowania pochodzące z odległymi geograficznie lokalizacji, w którym co najmniej jednej z lokalizacji może być również nietypowe dla użytkownika, podane poza zachowanie. Wśród wielu innych czynników tego algorytmu uczenia maszynowego bierze pod uwagę czas między dwoma logowania i czasu, jaki zajęłyby do przesyłane z lokalizacji pierwszy na sekundę, wskazującą, czy inny użytkownik jest korzystającej z tego samego użytkownika poświadczenia.

Algorytm ignoruje oczywiste "fałszywych alarmów" Współtworzenie niemożliwa podróż warunki, takie jak sieci VPN i lokalizacje regularnie używane przez innych użytkowników w organizacji. System ma okres learning początkowej 14 dni, w których uczy się nowego użytkownika logowania zachowanie. 

### <a name="sign-in-from-unfamiliar-locations"></a>Logowanie z nieznanych lokalizacji

Ten typ zdarzenia ryzyka uwzględnia poza logowania w lokalizacji (IP, zakres / geograficzne i ASN) do określenia lokalizacji nowego / doświadczenia w pracy. System przechowuje informacje dotyczące powyższych lokalizacjach, używane przez użytkownika i traktuje te lokalizacje "znanych". Zdarzenie ryzyko jest wyzwalane, gdy logowanie odbywa się od lokalizacji, która nie jest jeszcze listę znanych lokalizacji. System ma początkowej learning okres 30 dni, w których nie Flaga wszelkie nowe lokalizacje jako nieznanych lokalizacji. System zignoruje również logowania z urządzeń znanych i lokalizacje, które są od siebie lokalizacjach geograficznych znanych lokalizacji. 

Identity Protection wykrywa logowania z nieznanych lokalizacji również dla uwierzytelniania podstawowego / starszych protokołów. Ponieważ te protokoły nie ma nowoczesnych znanych funkcji, takich jak identyfikator klienta, nie ma wystarczającej ilości danych telemetrycznych do redukować liczbę fałszywych alarmów. Aby zmniejszyć liczbę zdarzeń wykryte zagrożenie, należy przenieść nowoczesnego uwierzytelniania.   

### <a name="sign-ins-from-infected-devices"></a>Logowania z zainfekowanych urządzeń

Ten typ zdarzenia ryzyka identyfikuje logowania z urządzeń zainfekowanych złośliwym oprogramowaniem, które nie są aktywnie komunikować się z serwerem botów. Jest to określane przez skorelowanie adresów IP urządzenia użytkownika z adresami IP, które były kontaktu z serwerem botów. 

### <a name="sign-ins-from-ip-addresses-with-suspicious-activity"></a>Logowania z adresów IP związanych z podejrzanymi działaniami
Ten typ zdarzenia ryzyka identyfikuje adresów IP, z których dużej liczby nieudanych prób logowania były widoczne, na wielu kontach użytkowników w krótkim przedziale czasu. Ruchu jest charakterystyczny dla adresów IP używanych przez osoby atakujące i silne wskaźnikiem, czy konta są już lub mają zostać naruszone. Jest to algorytmu uczenia maszynowego, który ignoruje oczywiste "*alarmów false*", takie jak adresy IP są regularnie używane przez innych użytkowników w organizacji.  System ma okres learning początkowej 14 dni, gdzie uczy się zachowanie logowania nowego użytkownika i nowej dzierżawy.


## <a name="detection-type"></a>Typ wykrywania

Właściwość type wykrywania są wskaźnikami (w czasie rzeczywistym lub w trybie Offline) dla przedziału czasu wykrywania zdarzenia ryzyka. Obecnie większość zdarzeń o podwyższonym ryzyku są wykrywane w trybie offline w ramach operacji przetwarzania końcowego po wystąpieniu zdarzenia ryzyka.

W poniższej tabeli wymieniono ilość czasu potrzebnego dla typu wykrywania wyświetlani w raporcie pokrewne:

| Typ wykrywania | Opóźnienie raportowania |
| --- | --- |
| Czas rzeczywisty | 5 – 10 minut |
| Offline | 2-4 godziny |


Typy zdarzeń ryzyka, które wykrywa usługi Azure Active Directory dostępne są następujące typy wykrywania:

| Typ zdarzenia ryzyka | Typ wykrywania |
| :-- | --- | 
| [Użytkownicy z ujawnione poświadczenia](#leaked-credentials) | Offline |
| [Logowania z anonimowych adresów IP](#sign-ins-from-anonymous-ip-addresses) | Czas rzeczywisty |
| [Niemożliwa podróż do nietypowych lokalizacji](#impossible-travel-to-atypical-locations) | Offline |
| [Logowania z nieznanych lokalizacji](#sign-in-from-unfamiliar-locations) | Czas rzeczywisty |
| [Logowania z zainfekowanych urządzeń](#sign-ins-from-infected-devices) | Offline |
| [Logowania z adresów IP związanych z podejrzanymi działaniami](#sign-ins-from-ip-addresses-with-suspicious-activity) | Offline|


## <a name="risk-level"></a>Poziom ryzyka

Właściwość poziomu ryzyka zdarzenia ryzyko jest wskaźnikiem (wysoki, średni lub niski) ważność i zaufania zdarzeń ryzyka. Ta właściwość pomaga priorytetów działania, które należy wykonać. 

Ważność zdarzenia ryzyka reprezentuje siła sygnału jako predykcyjne naruszenia tożsamości. Zaufanie jest wskaźnikiem możliwość fałszywych alarmów. 

Na przykład: 

* **Wysoka**: wysokiego zaufania i zdarzenia ryzyka o wysokim znaczeniu. Te zdarzenia są silne wskaźników, które naruszono tożsamości użytkownika i kont użytkowników, wpływ na powinny zostać skorygowane natychmiast.

* **Średnia liczba godzin**: o wysokim znaczeniu, ale niższe zdarzeń ryzyka zaufania, albo na odwrót. Te zdarzenia są potencjalnie ryzykowne i kont użytkowników w pełni funkcjonalne należy skorygować.

* **Niski**: małych zaufania i zdarzenia ryzyka o niskim znaczeniu. To zdarzenie może nie wymagać natychmiastowego działania, ale w połączeniu z innymi zdarzeniami ryzyka może udostępnić oznaczenie silne złamane tożsamości.

![Poziom ryzyka](./media/active-directory-reporting-risk-events/01.png)

### <a name="leaked-credentials"></a>Ujawnione poświadczenia

Ujawnione poświadczenia zdarzenia o podwyższonym ryzyku są sklasyfikowane jako **wysokiej**, ponieważ udostępniają one jednoznacznie zidentyfikować, że podana nazwa użytkownika i hasło są dostępne do osoby atakującej.

### <a name="sign-ins-from-anonymous-ip-addresses"></a>Logowania z anonimowych adresów IP

Poziom ryzyka dla tego typu zdarzenia ryzyko jest **średni** ponieważ anonimowego adresu IP nie jest oznaczenie silne naruszenia konta. Firma Microsoft zaleca, natychmiast skontaktuj się użytkownika, aby sprawdzić, czy za pomocą anonimowych adresów IP.


### <a name="impossible-travel-to-atypical-locations"></a>Niemożliwa podróż do nietypowych lokalizacji

Niemożliwa podróż jest zwykle dobry wskaźnik, że haker został pomyślnie logować się. Jednak alarmów false może wystąpić, gdy użytkownik podróżuje przy użyciu nowego urządzenia lub sieci VPN, który zazwyczaj nie jest używany przez innych użytkowników w organizacji. Aplikacje, które niepoprawnie przekazywania adresów IP serwera jako klient adresów IP, które mogą spowodować wygląd jest inne źródło alarmów false rejestrowania znajduje się miejsce do centrum danych, gdzie tej aplikacji do wewnętrznego (często są to Microsoft centrów danych nadających wygląd logowania biorąc umieść firmy Microsoft do adresów IP). W wyniku tych alarmów false jest poziom ryzyka dla tego zdarzenia ryzyka **średni**.

> [!TIP]
> Można zmniejszyć liczbę zgłoszonych alarmów false dla tego typu zdarzenia ryzyko przez skonfigurowanie [o nazwie lokalizacje](active-directory-named-locations.md). 

### <a name="sign-in-from-unfamiliar-locations"></a>Logowanie z nieznanych lokalizacji

Nieznanych lokalizacji można podać silne wskazanie, że atakujący jest w stanie używać kradzieży tożsamości. FALSE alarmów może wystąpić, gdy użytkownik podróżuje, próbuje się nowego urządzenia lub jest za pomocą nowej sieci VPN. W wyniku tych fałszywych alarmów, poziom ryzyka dla tego typu zdarzenia jest **średni**.

### <a name="sign-ins-from-infected-devices"></a>Logowania z zainfekowanych urządzeń

To zdarzenie ryzyka identyfikuje adresy IP, nie urządzeń użytkownika. Jeśli kilka urządzeń znajdują się za jeden adres IP, a tylko niektóre są kontrolowane przez sieć bot logowania z innymi urządzeniami Moje wyzwalacza to zdarzenie niepotrzebnie, czyli Przyczyna klasyfikowania to zdarzenie ryzyka jako **małej**.  

Zaleca się z nim i skanowanie wszystkich urządzeń użytkownika. Istnieje również możliwość, że jest zainfekowany urządzenia osobiste użytkownika lub jak wspomniano wcześniej, że ktoś inny używał zainfekowanego urządzenia z tego samego adresu IP w imieniu użytkownika. Zainfekowanych urządzeń są często zainfekowanych przez złośliwe oprogramowanie, nie została zidentyfikowana przez oprogramowanie antywirusowe, którą może również oznaczać jako płynność zwyczaje, które mogą być przyczyną zainfekowania urządzenia.

Aby uzyskać więcej informacji na temat adresów infekcji złośliwym oprogramowaniem, zobacz [Centrum ochrony przed złośliwym oprogramowaniem](http://go.microsoft.com/fwlink/?linkid=335773&clcid=0x409).


### <a name="sign-ins-from-ip-addresses-with-suspicious-activity"></a>Logowania z adresów IP związanych z podejrzanymi działaniami

Firma Microsoft zaleca skontaktowanie się użytkownika, aby sprawdzić, czy rzeczywiście podpisane z adresu IP, która została oznaczona jako podejrzana. Poziom ryzyka dla tego typu zdarzenia to "**średni**" kilka urządzeń może być za ten sam adres IP, podczas gdy tylko niektóre mogą być odpowiedzialne za podejrzane działania. 


 
## <a name="next-steps"></a>Kolejne kroki

Zdarzenia o podwyższonym ryzyku są foundation ochrony tożsamości usługi Azure AD. Usługi Azure AD można obecnie wykrywa sześciu zdarzenia ryzyka: 


| Typ zdarzenia ryzyka | Poziom ryzyka | Typ wykrywania |
| :-- | --- | --- |
| [Użytkownicy z ujawnione poświadczenia](#leaked-credentials) | Wysoka | Offline |
| [Logowania z anonimowych adresów IP](#sign-ins-from-anonymous-ip-addresses) | Medium | Czas rzeczywisty |
| [Niemożliwa podróż do nietypowych lokalizacji](#impossible-travel-to-atypical-locations) | Medium | Offline |
| [Logowania z nieznanych lokalizacji](#sign-in-from-unfamiliar-locations) | Medium | Czas rzeczywisty |
| [Logowania z zainfekowanych urządzeń](#sign-ins-from-infected-devices) | Małe | Offline |
| [Logowania z adresów IP związanych z podejrzanymi działaniami](#sign-ins-from-ip-addresses-with-suspicious-activity) | Medium | Offline|

Gdzie można znaleźć zdarzenia ryzyka, które zostały wykryte w środowisku
Istnieją dwa miejsca, w którym przejrzeć zdarzenia zgłoszone ryzyka:

 - **Raportowanie na platformie Azure AD** -zdarzenia o podwyższonym ryzyku są częścią zabezpieczeń usługi Azure AD raportów. Aby uzyskać więcej informacji, zobacz [użytkowników na zagrożenia bezpieczeństwa raport](active-directory-reporting-security-user-at-risk.md) i [raport zabezpieczeń ryzykowne logowania](active-directory-reporting-security-risky-sign-ins.md).

 - **Azure AD Identity Protection** -zdarzenia o podwyższonym ryzyku są również częścią [Azure Active Directory Identity Protection](active-directory-identityprotection.md) funkcje raportowania.
    

Podczas wykrywania zdarzenia o podwyższonym ryzyku już reprezentuje istotnym elementem ochrony Twojej tożsamości, masz również opcję, aby ręcznie rozwiązać je albo nawet wdrożenie automatyczne odpowiedzi przez skonfigurowanie zasad dostępu warunkowego. Aby uzyskać więcej informacji, zobacz z [Azure Active Directory Identity Protection](active-directory-identityprotection.md).
 
