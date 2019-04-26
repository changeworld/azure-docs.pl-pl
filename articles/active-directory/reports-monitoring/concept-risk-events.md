---
title: Zdarzenia o podwyższonym ryzyku w usłudze Azure Active Directory | Dokumentacja firmy Microsoft
description: Ta artice zapewnia szczegółowe omówienie zdarzeń o podwyższonym ryzyku są.
services: active-directory
keywords: Usługa Azure active directory identity protection, zabezpieczenia, ryzyka, poziom ryzyka, luk w zabezpieczeniach, zasady zabezpieczeń
author: MarkusVi
manager: daveba
ms.assetid: fa2c8b51-d43d-4349-8308-97e87665400b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4afcbffa778c902c1b32979c69ca91ab0955f739
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60286864"
---
# <a name="azure-active-directory-risk-events"></a>Zdarzenia o podwyższonym ryzyku w usłudze Azure Active Directory

Większość naruszenia zabezpieczeń ma miejsce, gdy osoby atakujące uzyskują dostęp do środowiska, kradzież tożsamości użytkownika. Wykrywanie tożsamości ze złamanymi zabezpieczeniami jest nie łatwym zadaniem. Usługa Azure Active Directory korzysta z algorytmów uczenia maszynowego adaptacyjne i algorytmy heurystyczne wykryć podejrzane akcje, które są powiązane z kontami użytkowników. Każdej wykrytej podejrzanych działań są przechowywane w rekord nazywany **zdarzenie o podwyższonym ryzyku**.

Istnieją dwa miejsca, w którym przejrzeć zdarzenia ryzyka zgłaszanej:

 - **Raportowanie usługi Azure AD** -zdarzeń o podwyższonym ryzyku są częścią zabezpieczeń usługi Azure AD raportów. Aby uzyskać więcej informacji, zobacz [raport zabezpieczeń dotyczący narażonych użytkowników](concept-user-at-risk.md) i [raport zabezpieczeń dotyczący ryzykownych logowań](concept-risky-sign-ins.md).

 - **Usługa Azure AD Identity Protection** -zdarzeń o podwyższonym ryzyku są również częścią możliwości raportowania [usługi Azure Active Directory Identity Protection](../active-directory-identityprotection.md).

Ponadto można użyć [zdarzeń o podwyższonym ryzyku Identity Protection API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/identityriskevent) uzyskać programowy dostęp do wykrywania zabezpieczeń przy użyciu programu Microsoft Graph. Aby uzyskać więcej informacji, zobacz [Rozpoczynanie pracy z usługą Azure Active Directory Identity Protection i Microsoft Graph](../identity-protection/graph-get-started.md). 

Obecnie usługa Azure Active Directory wykrywa sześć typów zdarzeń o podwyższonym ryzyku:

- [Użytkownicy z ujawnionymi poświadczeniami](#leaked-credentials) 
- [Logowania z anonimowych adresów IP](#sign-ins-from-anonymous-ip-addresses) 
- [Niemożliwa podróż do nietypowych lokalizacji](#impossible-travel-to-atypical-locations) 
- [Logowania z zainfekowanych urządzeń](#sign-ins-from-infected-devices) 
- [Logowania z adresów IP z podejrzaną aktywnością](#sign-ins-from-ip-addresses-with-suspicious-activity) 
- [Logowania z nieznanych lokalizacji](#sign-in-from-unfamiliar-locations) 

![Zdarzenie o podwyższonym ryzyku](./media/concept-risk-events/91.png)

> [!IMPORTANT]
> Czasami może się okazać zdarzenie o podwyższonym ryzyku, bez odpowiadającego mu wpisu logowania w [raporcie logowań](concept-sign-ins.md). Jest to spowodowane Identity Protection ocenia ryzyko dla obu **interaktywne** i **nieinterakcyjnych** logowania, w raporcie logowań pokazuje tylko interakcyjne sesje logowania.

Szczegółowe informacje, uzyskasz na zdarzenie wykryte zagrożenie jest powiązany ze swoją subskrypcją usługi Azure AD. 

* Za pomocą **wersji usługi Azure AD Premium P2**, uzyskać najbardziej szczegółowe informacje na temat wszystkie wykrycia bazowego. 
* Za pomocą **wersji Azure AD Premium P1**, zaawansowane wykrywanie (np. nieznane logowania właściwości) nie są objęte licencji i pojawi się pod nazwą **logowania z dodatkowym ryzykiem wykryto** . Ponadto poziom ryzyka i pola szczegółów o podwyższonym ryzyku są ukryte.

Natomiast wykrywanie zdarzeń o podwyższonym ryzyku już reprezentuje istotnym elementem ochrony tożsamości, masz również opcję, aby ręcznie rozwiązać je lub zaimplementować automatycznych odpowiedzi przez skonfigurowanie zasad dostępu warunkowego. Aby uzyskać więcej informacji, zobacz [usługi Azure Active Directory Identity Protection](../active-directory-identityprotection.md).

## <a name="risk-event-types"></a>Rodzaje ryzykownych zdarzeń

**Typ zdarzenia ryzyka** właściwości jest identyfikatorem podejrzanych działań, które utworzono rekord zdarzenia o podwyższonym ryzyku.

Ciągłe inwestycje firmy Microsoft w procesie wykrywania prowadzić do:

- Ulepszenia dokładności wykrywania istniejących zdarzeń o podwyższonym ryzyku 
- Nowe typy zdarzeń o podwyższonym ryzyku, które zostaną dodane w przyszłości

### <a name="leaked-credentials"></a>Ujawnione poświadczenia

Gdy przestępcami cybernetycznymi naruszenia zabezpieczeń prawidłowe haseł uprawnionych użytkowników, często udostępniać tych poświadczeń. Zazwyczaj jest to zrobić, publikując je publicznie na ciemny witryn sieci web lub wklej lub handlem lub sprzedaży poświadczeń na czarnym rynku. Microsoft wyciek poświadczeń usługi uzyskuje nazwę użytkownika / hasło pary przez monitorowanie publicznych i ciemny witryn sieci web i Praca z:

- Badacze
- Organom ścigania
- Zespoły zabezpieczeń w firmie Microsoft
- Innych zaufanych źródeł 

Gdy usługa uzyskuje nazwę użytkownika / hasło pary one są sprawdzane w odniesieniu bieżącego prawidłowe poświadczenia użytkowników usługi AAD. Gdy zostanie znalezione dopasowanie, oznacza to, że został złamany hasła użytkownika, a **wyciek poświadczeń zdarzenie o podwyższonym ryzyku** zostanie utworzony.

### <a name="sign-ins-from-anonymous-ip-addresses"></a>Logowania z anonimowych adresów IP

Ten typ zdarzenia o podwyższonym ryzyku identyfikuje użytkowników, którzy zalogowali się pomyślnie się z adresu IP, który został zidentyfikowany jako adres IP anonimowego serwera proxy. Te serwery proxy są wykorzystywane przez osoby, które chcą ukryć adres IP swojego urządzenia i mogą być używane do złośliwego działania.

### <a name="impossible-travel-to-atypical-locations"></a>Niemożliwa podróż do nietypowych lokalizacji

Ten typ zdarzenia o podwyższonym ryzyku identyfikuje pochodzące z komputera odległym geograficznie lokalizacji, w którym co najmniej jednej z lokalizacji może być również nietypowa dla użytkownika, biorąc pod uwagę ostatnie działanie dwukrotnym zalogowaniu. Między kilka innych czynników tego algorytmu uczenia maszynowego uwzględnia czas między dwa logowania i czas zajęłoby dla użytkownika na komunikację z pierwszej lokalizacji do drugiego, wskazujący, że inny użytkownik korzysta z takie same poświadczenia.

Algorytm ignoruje oczywiste "wyniki fałszywie dodatnie" Współtworzenie warunki niemożliwych podróży, takich jak sieci VPN i lokalizacje, które regularnie używane przez innych użytkowników w organizacji. System ma wstępny okres uczenia wynoszący 14 dni, podczas których uczy się zachowanie logowania nowego użytkownika. 

### <a name="sign-in-from-unfamiliar-locations"></a>Logowania z nieznanych lokalizacji

Tego typu zdarzenia o podwyższonym ryzyku uwzględnia ostatnie logowania w lokalizacji (IP, szerokości / długości geograficznej i jego numer ASN) aby określić nowy / nieznanych lokalizacjach. System przechowuje informacje o powyższych lokalizacjach, używane przez użytkownika i uwzględnia te lokalizacje "znanych". Zdarzenia o podwyższonym ryzyku jest wyzwalany, gdy logowania wystąpi z lokalizacji, która nie jest już na liście zaznajomiony lokalizacji. System ma wstępny okres uczenia wynoszący 30 dni, w których nie Flaga jakichkolwiek nowych lokalizacji jako nieznanych lokalizacji. System ignoruje także logowania z dobrze znanych urządzeń i lokalizacji, które geograficznie znajdują się blisko znanej lokalizacji. 

Identity Protection wykrywa logowania z nieznanych lokalizacji również na uwierzytelnianie podstawowe / starszych protokołów. Ponieważ te protokoły nie ma nowoczesny znane funkcje, takie jak identyfikator klienta, nie ma taką ilość telemetrii, aby zmniejszyć liczbę fałszywych alarmów. Aby zmniejszyć liczbę zdarzeń o podwyższonym ryzyku wykrytych, należy przenieść do nowoczesnego uwierzytelniania.   

### <a name="sign-ins-from-infected-devices"></a>Logowania z zainfekowanych urządzeń

Ten typ zdarzenia o podwyższonym ryzyku identyfikuje logowania z urządzeń zainfekowany złośliwym oprogramowaniem, które są znane z aktywnej komunikacji z serwerem bot. Jest to określane przez skorelowanie adresów IP na urządzeniu użytkownika względem adresów IP, które zostały kontaktu z usługą serwera bota. 

### <a name="sign-ins-from-ip-addresses-with-suspicious-activity"></a>Logowania z adresów IP związanych z podejrzanymi działaniami
Ten typ zdarzenia o podwyższonym ryzyku identyfikuje adresów IP, z których dużą liczbę nieudanych prób logowania były widoczne, na wielu kontach użytkowników w krótkim okresie. Wzorzec ruchu jest charakterystyczny dla adresów IP używanych przez osoby atakujące i jest silne wskazuje, czy konta są już lub mają być narażone na ataki. To jest machine learning algorytmu, który ignoruje oczywiste fałszywych alarmów, takie jak adresy IP, które są regularnie używane przez innych użytkowników w organizacji.  System ma wstępny okres uczenia wynoszący 14 dni, w którym uczy się zachowanie logowania nowego użytkownika i nowej dzierżawy.

## <a name="detection-type"></a>Typ wykrywania

Wykrywanie właściwość type jest wskaźnikiem (**w czasie rzeczywistym** lub **Offline**) dla przedziału czasu wykrycia zdarzenia o podwyższonym ryzyku. Obecnie większość zdarzeń o podwyższonym ryzyku są wykrywane w trybie offline w ramach operacji przetwarzania końcowego po wystąpieniu zdarzenia o podwyższonym ryzyku.

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

Właściwość zdarzenia o podwyższonym ryzyku w systemach z poziomu ryzyka jest wskaźnik (**wysokiej**, **średni**, lub **niski**) na poziomie ważności i zaufania zdarzenie o podwyższonym ryzyku. Ta właściwość pomaga określić priorytety akcje, które należy podjąć. 

Ważność zdarzenia o podwyższonym ryzyku reprezentuje siłę sygnału jako predykcyjne naruszenia tożsamości. Zaufania oznacza możliwość wyników fałszywie dodatnich. 

Na przykład: 

* **Wysoka**: O dużej pewności i zdarzenia o podwyższonym ryzyku o wysokiej ważności. Te zdarzenia są silne wskaźników, które tożsamość użytkownika zostało naruszone, i kont użytkowników, wpływ na powinny zostać skorygowane natychmiast.

* **Średnia**: Wysoka ważność, ale niższe zdarzenie o podwyższonym ryzyku zaufania, lub na odwrót. Te zdarzenia są potencjalnie ryzykownymi i kont użytkowników, wpływ na należy skorygować.

* **Niska**: Niski zaufania i zdarzenia o podwyższonym ryzyku o niskiej ważności. To zdarzenie może nie wymagać natychmiastowego działania, ale w połączeniu z innych zdarzeń o podwyższonym ryzyku może dostarczyć silne wskazanie, że tożsamość zostanie naruszony.

![Poziom ryzyka](./media/concept-risk-events/01.png)

### <a name="leaked-credentials"></a>Ujawnione poświadczenia

Ujawnione poświadczenia zdarzeń o podwyższonym ryzyku są klasyfikowane jako **wysokiej**, ponieważ zapewniają one jednoznacznie zidentyfikować, że nazwa użytkownika i hasło są dostępne dla osoby atakującej.

### <a name="sign-ins-from-anonymous-ip-addresses"></a>Logowania z anonimowych adresów IP

Poziom ryzyka dla tego typu zdarzenia o podwyższonym ryzyku jest **średni** ponieważ z anonimowego adresu IP nie jest silne oznaczenie naruszenia zabezpieczeń konta. Firma Microsoft zaleca, natychmiast skontaktuj się użytkownika, aby sprawdzić, jeśli za pomocą anonimowych adresów IP.


### <a name="impossible-travel-to-atypical-locations"></a>Niemożliwa podróż do nietypowych lokalizacji

Niemożliwa zmiana lokalizacji jest zwykle dobry wskaźnik, który haker był w stanie pomyślnie zalogować. Jednak fałszywych alarmów może wystąpić, gdy użytkownik podróżuje przy użyciu nowego urządzenia lub sieci VPN, które zwykle nie są używane przez innych użytkowników w organizacji. Innym źródłem fałszywych alarmów jest aplikacje przekazujące niepoprawnie serwera adresów IP jako klient adresy IP, które mogą spowodować wygląd logowań znajduje się miejsce do centrum danych, w których ta aplikacja firmy zaplecza (często są to centrów danych firmy Microsoft, które może nadać wygląd logowań odbywa się przez firmę Microsoft należące do adresów IP). W wyniku tych fałszywych alarmów jest poziom ryzyka dla tego zdarzenia o podwyższonym ryzyku **średni**.

> [!TIP]
> Można zmniejszyć liczbę zgłaszanych fałszywych alarmów dla tego typu zdarzenia o podwyższonym ryzyku, konfigurując [lokalizacje z nazwą](../active-directory-named-locations.md). 

### <a name="sign-in-from-unfamiliar-locations"></a>Logowania z nieznanych lokalizacji

Nieznanych lokalizacji można podać silne wskazanie, czy osoba atakująca ma możliwość korzystania z kradzieży tożsamości. Fałszywych alarmów może wystąpić, gdy użytkownik podróżuje, próbuje się nowego urządzenia lub jest przy użyciu nowej sieci VPN. W wyniku tych wyników fałszywie dodatnich, jest poziom ryzyka dla tego typu zdarzenia **średni**.

### <a name="sign-ins-from-infected-devices"></a>Logowania z zainfekowanych urządzeń

To zdarzenie o podwyższonym ryzyku identyfikuje adresów IP, nie urządzeń użytkowników. Jeśli kilka urządzeń znajdują się za pojedynczy adres IP, a tylko niektóre są kontrolowane przez sieć bot, logowania z innymi urządzeniami Moje wyzwalacz to zdarzenie niepotrzebnie, dlatego to zdarzenie o podwyższonym ryzyku są klasyfikowane jako **niski**.  

Firma Microsoft zaleca się z nim, a skanowanie wszystkich urządzeń użytkownika. Jest również możliwe, że jest zainfekowany urządzenia osobiste użytkownika, lub czy ktoś inny używał zainfekowanego urządzenia z tego samego adresu IP jako użytkownik. Zainfekowanych urządzeń są często zainfekowanych przez złośliwe oprogramowanie, które nie zostały zidentyfikowane przez oprogramowanie antywirusowe, a także oznaczać wszelkie płynność nawyków, które mogą być przyczyną urządzenia infekują.

Aby uzyskać więcej informacji na temat adresów przed infekcjami złośliwym oprogramowaniem, zobacz [Malware Protection Center](https://www.microsoft.com/en-us/security/portal/definitions/adl.aspx/).

### <a name="sign-ins-from-ip-addresses-with-suspicious-activity"></a>Logowania z adresów IP związanych z podejrzanymi działaniami

Firma Microsoft zaleca, skontaktuj się z użytkownika, aby sprawdzić, czy rzeczywiście podpisane z adresu IP, która została oznaczona jako podejrzanej. Poziom ryzyka dla tego typu zdarzenia to "**średni**" kilka urządzeń może być za ten sam adres IP, podczas gdy tylko niektóre mogą być odpowiedzialne za podejrzanych działań. 


## <a name="next-steps"></a>Następne kroki

* [Raport zabezpieczeń dotyczący narażonych użytkowników](concept-user-at-risk.md)
* [Raport zabezpieczeń dotyczący ryzykownych logowań](concept-risky-sign-ins.md)
* [Azure AD Identity Protection](../active-directory-identityprotection.md).
