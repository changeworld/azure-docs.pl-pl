---
title: Wykrywanie ryzyka usługi Azure Active Directory | Dokumenty firmy Microsoft
description: Ten artice daje szczegółowy przegląd tego, co wykrywanie ryzyka są.
services: active-directory
keywords: ochrona tożsamości usługi Azure Active Directory, bezpieczeństwo, ryzyko, poziom ryzyka, luka, zasady zabezpieczeń
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
ms.openlocfilehash: 4caa248f6972609ecb6bf71dd521c68d78cebd70
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2020
ms.locfileid: "80383960"
---
# <a name="azure-active-directory-risk-detections"></a>Wykrywanie ryzyka usługi Azure Active Directory

Zdecydowana większość naruszeń zabezpieczeń ma miejsce, gdy osoby atakujące uzyskują dostęp do środowiska, kradnąc tożsamość użytkownika. Odnajdowanie naruszonych tożsamości nie jest łatwym zadaniem. Usługa Azure Active Directory używa adaptacyjnych algorytmów uczenia maszynowego i heurystyki do wykrywania podejrzanych akcji, które są związane z kontami użytkowników. Każda wykryta podejrzana akcja jest przechowywana w rekordzie zwanym **wykrywaniem ryzyka.**

Istnieją dwa miejsca, w których można przejrzeć zgłoszone wykrywanie ryzyka:

 - **Raportowanie usługi Azure AD** — wykrywanie ryzyka jest częścią raportów zabezpieczeń usługi Azure AD. Aby uzyskać więcej informacji, zobacz [raport zabezpieczeń użytkowników zagrożonych](concept-user-at-risk.md) i [raport zabezpieczeń ryzykownych logów](concept-risky-sign-ins.md).

 - **Usługa Azure AD Identity Protection** — wykrywanie ryzyka jest również częścią możliwości raportowania usługi Azure Active [Directory Identity Protection](../active-directory-identityprotection.md).

Ponadto można użyć [interfejsu API wykrywania ryzyka ochrony tożsamości,](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/identityriskevent) aby uzyskać programowy dostęp do wykrywania zabezpieczeń za pomocą programu Microsoft Graph. Aby uzyskać więcej informacji, zobacz [Wprowadzenie do usługi Azure Active Directory Identity Protection i Microsoft Graph](../identity-protection/graph-get-started.md). 

Obecnie usługa Azure Active Directory wykrywa sześć typów wykrywania ryzyka:

- [Użytkownicy z ujawnionymi poświadczeniami](#leaked-credentials) 
- [Logowania z anonimowych adresów IP](#sign-ins-from-anonymous-ip-addresses) 
- [Niemożliwa podróż do nietypowych lokalizacji](#impossible-travel-to-atypical-locations) 
- [Logowania z zainfekowanych urządzeń](#sign-ins-from-infected-devices) 
- [Logowania z adresów IP związanych z podejrzanymi działaniami](#sign-ins-from-ip-addresses-with-suspicious-activity) 
- [Logowania z nieznanych lokalizacji](#sign-in-from-unfamiliar-locations) 

![Wykrywanie ryzyka](./media/concept-risk-events/91.png)

> [!IMPORTANT]
> Czasami wykrywanie ryzyka może być wykrywane bez odpowiedniego wpisu logowania w [raporcie logowania](concept-sign-ins.md). Jest tak, ponieważ ochrona tożsamości ocenia ryzyko zarówno **dla logowania interakcyjne,** jak i **nieinterakcyjne,** podczas gdy raport logowania pokazuje tylko interaktywne logowania.

Wgląd w wykryte wykrywanie ryzyka jest powiązany z subskrypcją usługi Azure AD. 

* Dzięki **wersji Azure AD Premium P2**otrzymujesz najbardziej szczegółowe informacje o wszystkich podstawowych wykrywaniach. 
* W **przypadku wersji Azure AD Premium P1**zaawansowane wykrywanie (takie jak nieznane właściwości logowania) nie są objęte licencją i będą wyświetlane pod nazwą **Zaloguj się z wykrytym dodatkowym ryzykiem.** Ponadto pola poziomu ryzyka i szczegółów ryzyka są ukryte.

Podczas gdy wykrywanie ryzyka stanowi już ważny aspekt ochrony tożsamości, masz również możliwość ręcznego rozwiązania ich lub zaimplementowania automatycznych odpowiedzi przez skonfigurowanie zasad dostępu warunkowego. Aby uzyskać więcej informacji, zobacz [Azure Active Directory Identity Protection (Ochrona tożsamości w usłudze Azure Active Directory)](../active-directory-identityprotection.md).

## <a name="risk-detection-types"></a>Typy wykrywania ryzyka

Właściwość **typu wykrywania ryzyka** jest identyfikatorem podejrzanej akcji, dla jakiej został utworzony rekord wykrywania ryzyka.

Ciągłe inwestycje firmy Microsoft w proces wykrywania prowadzą do:

- Poprawa dokładności wykrywania istniejących wykrywania ryzyka 
- Nowe typy wykrywania ryzyka, które zostaną dodane w przyszłości

### <a name="leaked-credentials"></a>Wyciekające poświadczenia

Gdy cyberprzestępcy naruszyli prawidłowe hasła legalnych użytkowników, często udostępniają te poświadczenia. Zwykle odbywa się to poprzez opublikowanie ich publicznie w ciemnej sieci lub wklej witryn lub poprzez handel lub sprzedaż poświadczeń na czarnym rynku. Usługa microsoft wyciekła poświadczeń przejmuje nazwy użytkownika / parę haseł poprzez monitorowanie publicznych i ciemnych stron internetowych i poprzez współpracę z:

- Naukowcy
- Ścigania
- Zespoły zabezpieczeń w firmie Microsoft
- Inne zaufane źródła 

Gdy usługa uzyskuje parę nazw użytkowników / haseł, są one sprawdzane pod kątem bieżących prawidłowych poświadczeń użytkowników usługi AAD. Po znalezieniu dopasowania oznacza to, że hasło użytkownika zostało naruszone i zostanie utworzone **wykrywanie ryzyka wyciekanych poświadczeń.**

### <a name="sign-ins-from-anonymous-ip-addresses"></a>Logowania z anonimowych adresów IP

Ten typ wykrywania ryzyka identyfikuje użytkowników, którzy pomyślnie zalogowali się z adresu IP, który został zidentyfikowany jako anonimowy adres IP serwera proxy. Te serwery proxy są używane przez osoby, które chcą ukryć adres IP urządzenia i mogą być używane do złośliwego zamiaru.

### <a name="impossible-travel-to-atypical-locations"></a>Niemożliwa podróż do nietypowych lokalizacji

Ten typ wykrywania ryzyka identyfikuje dwa logowania pochodzące z geograficznie odległych lokalizacji, gdzie co najmniej jedna z lokalizacji może być nietypowa dla użytkownika, biorąc pod uwagę zachowanie z przeszłości. Wśród kilku innych czynników ten algorytm uczenia maszynowego uwzględnia czas między dwoma logowaniami i czas potrzebny użytkownikowi na podróż z pierwszej lokalizacji do drugiej, wskazując, że inny użytkownik używa tego samego Poświadczenia.

Algorytm ignoruje oczywiste "fałszywe alarmy" przyczyniające się do niemożliwych warunków podróży, takich jak VPN i lokalizacje regularnie używane przez innych użytkowników w organizacji. System ma początkowy okres uczenia się 14 dni, podczas którego uczy się zachowania logowania nowego użytkownika. 

### <a name="sign-in-from-unfamiliar-locations"></a>Logowanie się z nieznanych lokalizacji

Ten typ wykrywania ryzyka uwzględnia wcześniejsze lokalizacje logowania (IP, Szerokość geograficzna / długość geograficzna i ASN) w celu określenia nowych / nieznanych lokalizacji. System przechowuje informacje o poprzednich lokalizacjach używanych przez użytkownika i uwzględnia te "znane" lokalizacje. Wykrywanie ryzyka jest wyzwalane, gdy logowanie występuje z lokalizacji, która nie znajduje się jeszcze na liście znanych lokalizacji. System ma początkowy okres nauki wynoszący 30 dni, podczas którego nie oznacza żadnych nowych lokalizacji jako nieznanych lokalizacji. System ignoruje również logowania ze znanych urządzeń i lokalizacji, które są geograficznie blisko znanej lokalizacji. 

Usługa Identity Protection wykrywa logowania z nieznanych lokalizacji również w przypadku uwierzytelniania podstawowego / starszych protokołów. Ponieważ te protokoły nie mają nowoczesnych znanych funkcji, takich jak identyfikator klienta, nie ma wystarczającej ilości danych telemetrycznych, aby zmniejszyć liczbę fałszywych alarmów. Aby zmniejszyć liczbę wykrytych wykrywania ryzyka, należy przejść do nowoczesnego uwierzytelniania.   

> [!NOTE]
> Jeśli nazwa użytkownika logowania i hasło nie są zgodne, logowanie zakończy się niepowodzeniem i wykrywanie ryzyka nie nastąpi. Logowanie z nieznanych lokalizacji wykrywania ryzyka tylko są wyzwalane na pomyślny logowania.

### <a name="sign-ins-from-infected-devices"></a>Logowania z zainfekowanych urządzeń

Ten typ wykrywania ryzyka identyfikuje logowania z urządzeń zainfekowanych złośliwym oprogramowaniem, o których wiadomo, że aktywnie komunikują się z serwerem botów. Jest to określane przez korelację adresów IP urządzenia użytkownika z adresami IP, które miały kontakt z serwerem botów. 

### <a name="sign-ins-from-ip-addresses-with-suspicious-activity"></a>Logowania z adresów IP związanych z podejrzanymi działaniami
Ten typ wykrywania ryzyka identyfikuje adresy IP, z których w krótkim czasie zaobserwowano dużą liczbę nieudanych prób logowania na wielu kontach użytkowników. To pasuje do wzorców ruchu adresów IP używanych przez osoby atakujące i jest silnym wskaźnikiem, że konta są już lub mają zostać naruszone. Jest to algorytm uczenia maszynowego, który ignoruje oczywiste fałszywych alarmów, takich jak adresy IP, które są regularnie używane przez innych użytkowników w organizacji.  System ma początkowy okres uczenia się 14 dni, w którym uczy się zachowania logowania nowego użytkownika i nowej dzierżawy.

## <a name="detection-type"></a>Typ wykrywania

Właściwość typu wykrywania jest wskaźnikiem **(w czasie rzeczywistym** lub **offline)** dla przedziałów czasowych wykrywania wykrywania ryzyka. Obecnie większość wykrywania ryzyka są wykrywane w trybie offline w operacji przetwarzania końcowego po wykryciu ryzyka.

W poniższej tabeli wymieniono czas potrzebny na pokazanie się typu wykrywania w powiązanym raporcie:

| Typ wykrywania | Opóźnienie raportowania |
| --- | --- |
| Przesyłanie w czasie rzeczywistym | 5 do 10 minut |
| W trybie offline | 2 do 4 godzin |


W przypadku typów wykrywania ryzyka wykrytych przez usługę Azure Active Directory typy wykrywania są następujące:

| Typ wykrywania ryzyka | Typ wykrywania |
| :-- | --- | 
| [Użytkownicy z ujawnionymi poświadczeniami](#leaked-credentials) | W trybie offline |
| [Logowania z anonimowych adresów IP](#sign-ins-from-anonymous-ip-addresses) | Przesyłanie w czasie rzeczywistym |
| [Niemożliwa podróż do nietypowych lokalizacji](#impossible-travel-to-atypical-locations) | W trybie offline |
| [Logowania z nieznanych lokalizacji](#sign-in-from-unfamiliar-locations) | Przesyłanie w czasie rzeczywistym |
| [Logowania z zainfekowanych urządzeń](#sign-ins-from-infected-devices) | W trybie offline |
| [Logowania z adresów IP związanych z podejrzanymi działaniami](#sign-ins-from-ip-addresses-with-suspicious-activity) | W trybie offline|


## <a name="risk-level"></a>Poziom ryzyka

Właściwość poziomu ryzyka wykrywania ryzyka jest wskaźnikiem **(Wysoki,** **Średni**lub **Niski)** dla dotkliwości i pewności wykrywania ryzyka. Ta właściwość pomaga ustalić priorytety działań, które należy podjąć. 

Nasilenie wykrywania ryzyka reprezentuje siłę sygnału jako predyktora naruszenia tożsamości. Zaufanie jest wskaźnikiem możliwości fałszywych alarmów. 

Na przykład: 

* **Wysoka:** Wysoka pewność i wykrywanie wysokiego ryzyka. Te zdarzenia są silne wskaźniki, że tożsamość użytkownika została naruszona, a wszystkie konta użytkowników wpływ powinny być natychmiast korygowane.

* **Medium**: Wysoka dotkliwość, ale niższe wykrywanie ryzyka ufności lub odwrotnie. Zdarzenia te są potencjalnie ryzykowne, a wszelkie konta użytkowników, których to dotyczy, powinny zostać naprawione.

* **Niski:** Niska pewność i wykrywanie niskiego ryzyka. To zdarzenie nie może wymagać natychmiastowego działania, ale w połączeniu z innymi wykrywania ryzyka, może stanowić silne wskazanie, że tożsamość jest zagrożona.

![Poziom ryzyka](./media/concept-risk-events/01.png)

### <a name="leaked-credentials"></a>Wyciekające poświadczenia

Wykrywanie ryzyka wyciekły poświadczenia są klasyfikowane jako **Wysoki**, ponieważ stanowią one wyraźne wskazanie, że nazwa użytkownika i hasło są dostępne dla osoby atakującej.

### <a name="sign-ins-from-anonymous-ip-addresses"></a>Logowania z anonimowych adresów IP

Poziom ryzyka dla tego typu wykrywania ryzyka jest **średni,** ponieważ anonimowy adres IP nie jest silnym wskaźnikiem naruszenia zabezpieczeń konta. Zalecamy natychmiastowe skontaktowanie się z użytkownikiem w celu sprawdzenia, czy używa on anonimowych adresów IP.


### <a name="impossible-travel-to-atypical-locations"></a>Niemożliwa podróż do nietypowych lokalizacji

Niemożliwe podróżowanie jest zwykle dobrym wskaźnikiem, że haker był w stanie pomyślnie się zalogować. Jednak fałszywe alarmy mogą wystąpić, gdy użytkownik podróżuje przy użyciu nowego urządzenia lub przy użyciu sieci VPN, która zazwyczaj nie jest używana przez innych użytkowników w organizacji. Innym źródłem fałszywych alarmów są aplikacje, które nieprawidłowo przekazują adresy IP serwera jako adresy IP klienta, co może nadawać wygląd logów odbywających się z centrum danych, w którym hostowany jest zaplecze tej aplikacji (często są to centra danych firmy Microsoft, które może nadawać wygląd logowania odbywających się z adresów IP należących do firmy Microsoft). W wyniku tych fałszywych alarmów poziom ryzyka dla tego wykrywania ryzyka jest **średni.**

> [!TIP]
> Można zmniejszyć liczbę zgłoszonych fałszywych alarmów dla tego typu wykrywania ryzyka, konfigurując [nazwane lokalizacje](../active-directory-named-locations.md). 

### <a name="sign-in-from-unfamiliar-locations"></a>Logowanie się z nieznanych lokalizacji

Nieznane lokalizacje mogą stanowić silny sygnał, że osoba atakująca może użyć skradzionej tożsamości. Fałszywe alarmy mogą wystąpić, gdy użytkownik podróżuje, wypróbowywa nowe urządzenie lub korzysta z nowej sieci VPN. W wyniku tych fałszywych alarmów poziom ryzyka dla tego typu zdarzenia jest **średni**.

### <a name="sign-ins-from-infected-devices"></a>Logowania z zainfekowanych urządzeń

To wykrywanie ryzyka identyfikuje adresy IP, a nie urządzenia użytkownika. Jeśli kilka urządzeń znajduje się za jednym adresem IP, a tylko niektóre są kontrolowane przez sieć botów, logowania z innych urządzeń niepotrzebnie wyzwalają to zdarzenie, dlatego to wykrywanie ryzyka jest klasyfikowane jako **niskie**.  

Zalecamy skontaktowanie się z użytkownikiem i zeskanowanie wszystkich urządzeń użytkownika. Możliwe jest również, że urządzenie osobiste użytkownika jest zainfekowane lub że ktoś inny używał zainfekowanego urządzenia z tego samego adresu IP co użytkownik. Zainfekowane urządzenia są często zainfekowane złośliwym oprogramowaniem, które nie zostało jeszcze zidentyfikowane przez oprogramowanie antywirusowe, a także mogą wskazywać na złe nawyki użytkownika, które mogły spowodować zainfekowanie urządzenia.

Aby uzyskać więcej informacji na temat rozwiązywania problemu infekcji złośliwym [oprogramowaniem, zobacz Centrum ochrony przed złośliwym oprogramowaniem](https://www.microsoft.com/en-us/security/portal/definitions/adl.aspx/).

### <a name="sign-ins-from-ip-addresses-with-suspicious-activity"></a>Logowania z adresów IP związanych z podejrzanymi działaniami

Zalecamy skontaktowanie się z użytkownikiem w celu sprawdzenia, czy rzeczywiście zalogował się z adresu IP, który został oznaczony jako podejrzany. Poziom ryzyka dla tego typu zdarzenia to "**Medium**", ponieważ kilka urządzeń może znajdować się za tym samym adresem IP, podczas gdy tylko niektóre mogą być odpowiedzialne za podejrzaną aktywność. 


## <a name="next-steps"></a>Następne kroki

* [Raport zabezpieczeń użytkowników zagrożonych](concept-user-at-risk.md)
* [Raport zabezpieczeń ryzykownych logów](concept-risky-sign-ins.md)
* [Usługa Azure AD Identity Protection](../active-directory-identityprotection.md).
