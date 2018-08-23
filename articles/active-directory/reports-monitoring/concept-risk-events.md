---
title: Zdarzenia o podwyższonym ryzyku w usłudze Azure Active Directory | Dokumentacja firmy Microsoft
description: Ta artice zapewnia szczegółowe omówienie zdarzeń o podwyższonym ryzyku są.
services: active-directory
keywords: Usługa Azure active directory identity protection, zabezpieczenia, ryzyka, poziom ryzyka, luk w zabezpieczeniach, zasady zabezpieczeń
author: priyamohanram
manager: mtillman
ms.assetid: fa2c8b51-d43d-4349-8308-97e87665400b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.component: report-monitor
ms.date: 05/14/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: fe5bcce06ef02bae75bf9fdaf5702a99f485885c
ms.sourcegitcommit: 1af4bceb45a0b4edcdb1079fc279f9f2f448140b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/09/2018
ms.locfileid: "42054270"
---
# <a name="azure-active-directory-risk-events"></a>Zdarzenia o podwyższonym ryzyku w usłudze Azure Active Directory

Większość naruszenia zabezpieczeń ma miejsce, gdy osoby atakujące uzyskują dostęp do środowiska, kradzież tożsamości użytkownika. Wykrywanie tożsamości ze złamanymi zabezpieczeniami jest nie łatwym zadaniem. Usługa Azure Active Directory korzysta z algorytmów uczenia maszynowego adaptacyjne i algorytmy heurystyczne wykryć podejrzane akcje, które są powiązane z kontami użytkowników. Każdej wykrytej podejrzanych działań są przechowywane w rekord nazywany *zdarzenie o podwyższonym ryzyku*.

Obecnie usługa Azure Active Directory wykrywa sześć typów zdarzeń o podwyższonym ryzyku:

- [Użytkownicy z ujawnionymi poświadczeniami](#leaked-credentials) 
- [Logowania z anonimowych adresów IP](#sign-ins-from-anonymous-ip-addresses) 
- [Niemożliwa podróż do nietypowych lokalizacji](#impossible-travel-to-atypical-locations) 
- [Logowania z zainfekowanych urządzeń](#sign-ins-from-infected-devices) 
- [Logowania z adresów IP z podejrzaną aktywnością](#sign-ins-from-ip-addresses-with-suspicious-activity) 
- [Logowania z nieznanych lokalizacji](#sign-in-from-unfamiliar-locations) 


![Zdarzenie o podwyższonym ryzyku](./media/concept-risk-events/91.png)

Szczegółowe informacje, uzyskasz na zdarzenie wykryte zagrożenie jest powiązany ze swoją subskrypcją usługi Azure AD. Za pomocą wersji Azure AD Premium P2 otrzymasz najbardziej szczegółowe informacje na temat wszystkie wykrycia bazowego. Za pomocą wersji Azure AD Premium P1 wykrywania, które nie są objęte licencja są wyświetlane jako zdarzenie o podwyższonym ryzyku **logowania z dodatkowym ryzykiem wykryto**.


Ten artykuł zawiera możesz szczegółowo omówiono zdarzeń o podwyższonym ryzyku, jakie są i jak ich używać do ochrony Twojej tożsamości usługi Azure AD.


## <a name="risk-event-types"></a>Rodzaje ryzykownych zdarzeń

Właściwości typu zdarzenia o podwyższonym ryzyku jest dla został utworzony identyfikator podejrzanego działania rekord zdarzenia o podwyższonym ryzyku.

Ciągłe inwestycje firmy Microsoft w procesie wykrywania prowadzić do:

- Ulepszenia dokładności wykrywania istniejących zdarzeń o podwyższonym ryzyku 
- Nowe typy zdarzeń o podwyższonym ryzyku, które zostaną dodane w przyszłości

### <a name="leaked-credentials"></a>Ujawnione poświadczenia

Przestępców przestępcami cybernetycznymi naruszenia zabezpieczeń prawidłowe haseł uprawnionych użytkowników, często współużytkować tych poświadczeń. Zazwyczaj jest to zrobić, publikując je publicznie na ciemny witryn sieci web lub wklej lub handlem lub sprzedaży poświadczeń na czarnym rynku. Microsoft wyciek poświadczeń usługi uzyskuje nazwę użytkownika / hasło pary przez monitorowanie publicznych i ciemny witryn sieci web i Praca z:

- Badacze
- Organom ścigania
- Zespoły zabezpieczeń w firmie Microsoft
- Innych zaufanych źródeł 

Gdy usługa uzyskuje nazwę użytkownika / hasło pary one są sprawdzane w odniesieniu bieżącego prawidłowe poświadczenia użytkowników usługi AAD. Gdy zostanie znalezione dopasowanie, oznacza to, że został złamany hasła użytkownika, a *wyciek poświadczeń zdarzenie o podwyższonym ryzyku* zostanie utworzony.

### <a name="sign-ins-from-anonymous-ip-addresses"></a>Logowania z anonimowych adresów IP

Ten typ zdarzenia o podwyższonym ryzyku identyfikuje użytkowników, którzy zalogowali się pomyślnie się z adresu IP, który został zidentyfikowany jako adres IP anonimowego serwera proxy. Te serwery proxy są wykorzystywane przez osoby, które chcą ukryć adres IP swojego urządzenia i mogą być używane do złośliwego działania.


### <a name="impossible-travel-to-atypical-locations"></a>Niemożliwa podróż do nietypowych lokalizacji

Ten typ zdarzenia o podwyższonym ryzyku identyfikuje pochodzące z komputera odległym geograficznie lokalizacji, w którym co najmniej jednej z lokalizacji może być również nietypowa dla użytkownika, biorąc pod uwagę ostatnie działanie dwukrotnym zalogowaniu. Między kilka innych czynników tego algorytmu uczenia maszynowego uwzględnia czas między dwa logowania i czas zajęłoby dla użytkownika na komunikację z pierwszej lokalizacji do drugiego, wskazujący, że inny użytkownik korzysta z takie same poświadczenia.

Algorytm ignoruje oczywiste "wyniki fałszywie dodatnie" Współtworzenie warunki niemożliwych podróży, takich jak sieci VPN i lokalizacje, które regularnie używane przez innych użytkowników w organizacji. System ma wstępny okres uczenia wynoszący 14 dni, podczas których uczy się zachowanie logowania nowego użytkownika. 

### <a name="sign-in-from-unfamiliar-locations"></a>Logowanie z nieznanych lokalizacji

Tego typu zdarzenia o podwyższonym ryzyku uwzględnia ostatnie logowania w lokalizacji (IP, szerokości / długości geograficznej i jego numer ASN) aby określić nowy / nieznanych lokalizacjach. System przechowuje informacje o powyższych lokalizacjach, używane przez użytkownika i uwzględnia te lokalizacje "znanych". Zdarzenia o podwyższonym ryzyku jest wyzwalany, gdy logowania wystąpi z lokalizacji, która nie jest już na liście zaznajomiony lokalizacji. System ma wstępny okres uczenia wynoszący 30 dni, w których nie Flaga jakichkolwiek nowych lokalizacji jako nieznanych lokalizacji. System ignoruje także logowania z dobrze znanych urządzeń i lokalizacji, które geograficznie znajdują się blisko znanej lokalizacji. 

Identity Protection wykrywa logowania z nieznanych lokalizacji również na uwierzytelnianie podstawowe / starszych protokołów. Ponieważ te protokoły nie ma nowoczesny znane funkcje, takie jak identyfikator klienta, nie ma taką ilość telemetrii, aby zmniejszyć liczbę fałszywych alarmów. Aby zmniejszyć liczbę zdarzeń o podwyższonym ryzyku wykrytych, należy przenieść do nowoczesnego uwierzytelniania.   

### <a name="sign-ins-from-infected-devices"></a>Logowania z zainfekowanych urządzeń

Ten typ zdarzenia o podwyższonym ryzyku identyfikuje logowania z urządzeń zainfekowany złośliwym oprogramowaniem, które są znane z aktywnej komunikacji z serwerem bot. Jest to określane przez skorelowanie adresów IP na urządzeniu użytkownika względem adresów IP, które zostały kontaktu z usługą serwera bota. 

### <a name="sign-ins-from-ip-addresses-with-suspicious-activity"></a>Logowania z adresów IP związanych z podejrzanymi działaniami
Ten typ zdarzenia o podwyższonym ryzyku identyfikuje adresów IP, z których dużą liczbę nieudanych prób logowania były widoczne, na wielu kontach użytkowników w krótkim okresie. Wzorzec ruchu jest charakterystyczny dla adresów IP używanych przez osoby atakujące i jest silne wskazuje, czy konta są już lub mają być narażone na ataki. Jest to algorytmu uczenia maszynowego, które ignoruje oczywiste "*fałszywych alarmów*", takie jak adresy IP są regularnie używane przez innych użytkowników w organizacji.  System ma wstępny okres uczenia wynoszący 14 dni, w którym uczy się zachowanie logowania nowego użytkownika i nowej dzierżawy.


## <a name="detection-type"></a>Typ wykrywania

Właściwość type wykrywania jest wskaźnika (w czasie rzeczywistym lub w trybie Offline) dla przedziału czasu wykrycia zdarzenia o podwyższonym ryzyku. Obecnie większość zdarzeń o podwyższonym ryzyku są wykrywane w trybie offline w ramach operacji przetwarzania końcowego po wystąpieniu zdarzenia o podwyższonym ryzyku.

W poniższej tabeli wymieniono ilość czasu potrzebnego dla typu wykrywania do powiązanego raportu:

| Typ wykrywania | Opóźnienie raportowania |
| --- | --- |
| Czas rzeczywisty | 5 – 10 minut |
| Offline | 2 – 4 godziny |


Typy zdarzeń o podwyższonym ryzyku, który wykrywa usługi Azure Active Directory dostępne są następujące typy wykrywania:

| Typ zdarzenia o podwyższonym ryzyku | Typ wykrywania |
| :-- | --- | 
| [Użytkownicy z ujawnionymi poświadczeniami](#leaked-credentials) | Offline |
| [Logowania z anonimowych adresów IP](#sign-ins-from-anonymous-ip-addresses) | Czas rzeczywisty |
| [Niemożliwa podróż do nietypowych lokalizacji](#impossible-travel-to-atypical-locations) | Offline |
| [Logowania z nieznanych lokalizacji](#sign-in-from-unfamiliar-locations) | Czas rzeczywisty |
| [Logowania z zainfekowanych urządzeń](#sign-ins-from-infected-devices) | Offline |
| [Logowania z adresów IP z podejrzaną aktywnością](#sign-ins-from-ip-addresses-with-suspicious-activity) | Offline|


## <a name="risk-level"></a>Poziom ryzyka

Poziom ryzyka własności zdarzenie o podwyższonym ryzyku jest wskaźnikiem (wysoki, średni lub niski) na poziomie ważności i zaufania zdarzenie o podwyższonym ryzyku. Ta właściwość pomaga określić priorytety akcje, które należy podjąć. 

Ważność zdarzenia o podwyższonym ryzyku reprezentuje siłę sygnału jako predykcyjne naruszenia tożsamości. Zaufania oznacza możliwość wyników fałszywie dodatnich. 

Na przykład: 

* **Wysoka**: o dużej pewności i zdarzenia o podwyższonym ryzyku o wysokiej ważności. Te zdarzenia są silne wskaźników, które tożsamość użytkownika zostało naruszone, i kont użytkowników, wpływ na powinny zostać skorygowane natychmiast.

* **Średnia**: o wysokiej ważności, ale niższe zdarzenie o podwyższonym ryzyku zaufania, lub na odwrót. Te zdarzenia są potencjalnie ryzykownymi i kont użytkowników, wpływ na należy skorygować.

* **Niska**: Niski, niezawodne i zdarzenia o podwyższonym ryzyku o niskiej ważności. To zdarzenie może nie wymagać natychmiastowego działania, ale w połączeniu z innych zdarzeń o podwyższonym ryzyku może dostarczyć silne wskazanie, że tożsamość zostanie naruszony.

![Poziom ryzyka](./media/concept-risk-events/01.png)

### <a name="leaked-credentials"></a>Ujawnione poświadczenia

Ujawnione poświadczenia zdarzeń o podwyższonym ryzyku są klasyfikowane jako **wysokiej**, ponieważ zapewniają one jednoznacznie zidentyfikować, że nazwa użytkownika i hasło są dostępne dla osoby atakującej.

### <a name="sign-ins-from-anonymous-ip-addresses"></a>Logowania z anonimowych adresów IP

Poziom ryzyka dla tego typu zdarzenia o podwyższonym ryzyku jest **średni** ponieważ z anonimowego adresu IP nie jest silne oznaczenie naruszenia zabezpieczeń konta. Firma Microsoft zaleca, natychmiast skontaktuj się użytkownika, aby sprawdzić, jeśli za pomocą anonimowych adresów IP.


### <a name="impossible-travel-to-atypical-locations"></a>Niemożliwa podróż do nietypowych lokalizacji

Niemożliwa zmiana lokalizacji jest zwykle dobry wskaźnik, który haker był w stanie pomyślnie zalogować. Jednak fałszywych alarmów może wystąpić, gdy użytkownik podróżuje przy użyciu nowego urządzenia lub sieci VPN, które zwykle nie są używane przez innych użytkowników w organizacji. Innym źródłem fałszywych alarmów jest aplikacje przekazujące niepoprawnie serwera adresów IP jako klient adresy IP, które mogą spowodować wygląd logowań znajduje się miejsce do centrum danych, w których ta aplikacja firmy zaplecza (często są to centrów danych firmy Microsoft, które może nadać wygląd logowań odbywa się przez firmę Microsoft należące do adresów IP). W wyniku tych fałszywych alarmów jest poziom ryzyka dla tego zdarzenia o podwyższonym ryzyku **średni**.

> [!TIP]
> Można zmniejszyć liczbę zgłaszanych fałszywych alarmów dla tego typu zdarzenia o podwyższonym ryzyku, konfigurując [lokalizacje z nazwą](../active-directory-named-locations.md). 

### <a name="sign-in-from-unfamiliar-locations"></a>Logowanie z nieznanych lokalizacji

Nieznanych lokalizacji można podać silne wskazanie, czy osoba atakująca ma możliwość korzystania z kradzieży tożsamości. Fałszywych alarmów może wystąpić, gdy użytkownik podróżuje, próbuje się nowego urządzenia lub jest przy użyciu nowej sieci VPN. W wyniku tych wyników fałszywie dodatnich, jest poziom ryzyka dla tego typu zdarzenia **średni**.

### <a name="sign-ins-from-infected-devices"></a>Logowania z zainfekowanych urządzeń

To zdarzenie o podwyższonym ryzyku identyfikuje adresów IP, nie urządzeń użytkowników. Jeśli kilka urządzeń znajdują się za pojedynczy adres IP, a tylko niektóre są kontrolowane przez sieć bot, logowania z innymi urządzeniami Moje wyzwalacz to zdarzenie niepotrzebnie, dlatego do tego zdarzenia o podwyższonym ryzyku klasyfikowania jako **niski**.  

Firma Microsoft zaleca się z nim, a skanowanie wszystkich urządzeń użytkownika. Jest również możliwe, że jest zainfekowany urządzeń osobistych użytkownika lub jak wspomniano wcześniej, że ktoś inny używał zainfekowanego urządzenia z tego samego adresu IP jako użytkownik. Zainfekowanych urządzeń są często zainfekowanych przez złośliwe oprogramowanie, które nie zostały zidentyfikowane przez oprogramowanie antywirusowe i może również oznaczać jako płynność nawyków, które mogą być przyczyną infekują urządzenia.

Aby uzyskać więcej informacji na temat adresów przed infekcjami złośliwym oprogramowaniem, zobacz [Malware Protection Center](http://go.microsoft.com/fwlink/?linkid=335773&clcid=0x409).


### <a name="sign-ins-from-ip-addresses-with-suspicious-activity"></a>Logowania z adresów IP związanych z podejrzanymi działaniami

Firma Microsoft zaleca, skontaktuj się z użytkownika, aby sprawdzić, czy rzeczywiście podpisane z adresu IP, która została oznaczona jako podejrzanej. Poziom ryzyka dla tego typu zdarzenia to "**średni**" kilka urządzeń może być za ten sam adres IP, podczas gdy tylko niektóre mogą być odpowiedzialne za podejrzanych działań. 


 
## <a name="next-steps"></a>Kolejne kroki

Zdarzenia o podwyższonym ryzyku są podstawą ochrony tożsamości usługi Azure AD. Usługa Azure AD obecnie może wykryć sześć zdarzeń o podwyższonym ryzyku: 


| Typ zdarzenia o podwyższonym ryzyku | Poziom ryzyka | Typ wykrywania |
| :-- | --- | --- |
| [Użytkownicy z ujawnionymi poświadczeniami](#leaked-credentials) | Wysoka | Offline |
| [Logowania z anonimowych adresów IP](#sign-ins-from-anonymous-ip-addresses) | Medium | Czas rzeczywisty |
| [Niemożliwa podróż do nietypowych lokalizacji](#impossible-travel-to-atypical-locations) | Medium | Offline |
| [Logowania z nieznanych lokalizacji](#sign-in-from-unfamiliar-locations) | Medium | Czas rzeczywisty |
| [Logowania z zainfekowanych urządzeń](#sign-ins-from-infected-devices) | Małe | Offline |
| [Logowania z adresów IP z podejrzaną aktywnością](#sign-ins-from-ip-addresses-with-suspicious-activity) | Medium | Offline|

Gdzie można znaleźć zdarzenia o podwyższonym ryzyku, które zostały wykryte w danym środowisku
Istnieją dwa miejsca, w którym przejrzeć zdarzenia ryzyka zgłaszanej:

 - **Raportowanie usługi Azure AD** -zdarzeń o podwyższonym ryzyku są częścią zabezpieczeń usługi Azure AD raportów. Aby uzyskać więcej informacji, zobacz [raport zabezpieczeń dotyczący narażonych użytkowników](concept-user-at-risk.md) i [raport zabezpieczeń dotyczący ryzykownych logowań](concept-risky-sign-ins.md).

 - **Usługa Azure AD Identity Protection** -zdarzeń o podwyższonym ryzyku są również częścią [usługi Azure Active Directory Identity Protection przez](../active-directory-identityprotection.md) możliwości raportowania.
    

Natomiast wykrywanie zdarzeń o podwyższonym ryzyku już reprezentuje istotnym elementem ochrony tożsamości, masz również opcję, aby ręcznie rozwiązać je albo nawet zaimplementować automatycznych odpowiedzi przez skonfigurowanie zasad dostępu warunkowego. Aby uzyskać więcej informacji, zobacz z [usługi Azure Active Directory Identity Protection przez](../active-directory-identityprotection.md).
 
