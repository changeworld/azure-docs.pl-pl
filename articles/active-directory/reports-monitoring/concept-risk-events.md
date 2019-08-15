---
title: Zdarzenia o podwyższonym ryzyku Azure Active Directory | Microsoft Docs
description: Ten artice zawiera szczegółowy przegląd zdarzeń związanych z ryzykiem.
services: active-directory
keywords: Azure Active Directory Identity Protection, bezpieczeństwo, ryzyko, poziom ryzyka, usterka, zasady zabezpieczeń
author: cawrites
manager: daveba
ms.assetid: fa2c8b51-d43d-4349-8308-97e87665400b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: chadam
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5711d900653ae7786899ce1c53f22cf181f5b8bf
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/13/2019
ms.locfileid: "68988271"
---
# <a name="azure-active-directory-risk-events"></a>Zdarzenia ryzyka Azure Active Directory

Większość naruszeń zabezpieczeń odbywa się, gdy osoby atakujące uzyskują dostęp do środowiska przez kradzież tożsamości użytkownika. Odnajdywanie zagrożonych tożsamości nie jest łatwe. Azure Active Directory używa adaptacyjnych algorytmów uczenia maszynowego i heurystyki do wykrywania podejrzanych działań, które są związane z kontami użytkowników. Każda wykryta podejrzana akcja jest przechowywana w rekordzie o nazwie **zdarzenie**o podwyższonym ryzyku.

Istnieją dwa miejsca, w których można przeglądać zgłoszone zdarzenia dotyczące ryzyka:

 - **Raportowanie usługi Azure AD** — zdarzenia ryzyka są częścią raportów zabezpieczeń usługi Azure AD. Aby uzyskać więcej informacji, zapoznaj się z raportem o zabezpieczeniach dotyczące [użytkowników](concept-user-at-risk.md) i [ryzykownych](concept-risky-sign-ins.md)logowań.

 - Zdarzenia o podwyższonym ryzyku **Azure AD Identity Protection** są również częścią funkcji raportowania [Azure Active Directory Identity Protection](../active-directory-identityprotection.md).

Ponadto można użyć [interfejsu API zdarzeń dotyczących ryzyka ochrony tożsamości](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/identityriskevent) , aby uzyskać programowy dostęp do wykrywania zabezpieczeń przy użyciu Microsoft Graph. Aby uzyskać więcej informacji, zobacz [wprowadzenie do Azure Active Directory Identity Protection i Microsoft Graph](../identity-protection/graph-get-started.md). 

Obecnie Azure Active Directory wykrywa sześć typów zdarzeń o podwyższonym ryzyku:

- [Użytkownicy z ujawnionymi poświadczeniami](#leaked-credentials) 
- [Logowania z anonimowych adresów IP](#sign-ins-from-anonymous-ip-addresses) 
- [Niemożliwa podróż do nietypowych lokalizacji](#impossible-travel-to-atypical-locations) 
- [Logowania z zainfekowanych urządzeń](#sign-ins-from-infected-devices) 
- [Logowania z adresów IP z podejrzaną aktywnością](#sign-ins-from-ip-addresses-with-suspicious-activity) 
- [Logowania z nieznanych lokalizacji](#sign-in-from-unfamiliar-locations) 

![Zdarzenie o podwyższonym ryzyku](./media/concept-risk-events/91.png)

> [!IMPORTANT]
> Czasami może się pojawić zdarzenie o podwyższonym ryzyku bez odpowiedniego wpisu logowania w [raporcie logowania](concept-sign-ins.md). Wynika to z faktu, że usługa Identity Protection szacuje ryzyko dla logowania interaktywnego i nieinterakcyjnego, podczas gdy raport logowania zawiera tylko interakcyjne logowania.

Szczegółowe informacje, uzyskasz na zdarzenie wykryte zagrożenie jest powiązany ze swoją subskrypcją usługi Azure AD. 

* Za pomocą **Azure AD — wersja Premium P2**można uzyskać najbardziej szczegółowe informacje na temat wszystkich podstawowych wykryć. 
* W **wersji Azure AD — wersja Premium P1**wykrycie zaawansowane (takie jak nieznane właściwości logowania) nie jest objęte licencją i zostanie wyświetlone pod nazwą **logowania z wykrytym dodatkowym ryzykiem**. Ponadto pola szczegóły poziomu ryzyka i ryzyka są ukryte.

Chociaż wykrycie zdarzeń o podwyższonym ryzyku reprezentuje już istotny aspekt ochrony tożsamości, można także ręcznie rozwiązać te problemy lub zaimplementować automatyczne odpowiedzi przez skonfigurowanie zasad dostępu warunkowego. Aby uzyskać więcej informacji, zobacz [Azure Active Directory Identity Protection](../active-directory-identityprotection.md).

## <a name="risk-event-types"></a>Rodzaje ryzykownych zdarzeń

Właściwość **Typ zdarzenia ryzyka** jest identyfikatorem podejrzanej akcji, dla którego utworzono rekord zdarzenia o podwyższonym ryzyku.

Ciągłe inwestycje firmy Microsoft w proces wykrywania prowadzą do:

- Ulepszenia dokładności wykrywania istniejących zdarzeń o podwyższonym ryzyku 
- Nowe typy zdarzeń ryzyka, które zostaną dodane w przyszłości

### <a name="leaked-credentials"></a>Ujawnione poświadczenia

Gdy cybernetycznymi naruszają prawidłowe hasła dla uprawnionych użytkowników, często udostępniają te poświadczenia. Jest to zwykle realizowane przez zaksięgowanie ich publicznie w ciemnych witrynach sieci Web lub wklejanie lub przez handel lub sprzedawanie poświadczeń na czarnym rynku. Usługa Microsoft wycieka poświadczeń uzyskuje pary nazw użytkowników i haseł, monitorując publiczne i ciemne witryny sieci Web i wykonując następujące działania:

- Badacze
- Przepisy prawne
- Zespoły ds. zabezpieczeń w firmie Microsoft
- Inne zaufane źródła 

Gdy usługa uzyskuje pary username i Password, są sprawdzane pod kątem bieżących prawidłowych poświadczeń przez użytkowników usługi AAD. Po znalezieniu dopasowania oznacza to, że złamano hasło użytkownika i jest tworzone zdarzenie powodujące **zagrożenie związane** z poświadczeniami.

### <a name="sign-ins-from-anonymous-ip-addresses"></a>Logowania z anonimowych adresów IP

To zdarzenie ryzyka określa użytkowników, którzy pomyślnie logowali się z adresu IP, który został zidentyfikowany jako anonimowy adres IP serwera proxy. Te serwery proxy są wykorzystywane przez osoby, które chcą ukryć adres IP swojego urządzenia i mogą być używane do złośliwego działania.

### <a name="impossible-travel-to-atypical-locations"></a>Niemożliwa podróż do nietypowych lokalizacji

Ten typ zdarzenia pozwala zidentyfikować dwa logowania pochodzące z lokalizacji geograficznie odległych, gdzie co najmniej jedna z lokalizacji może być nietypowa dla użytkownika, pod kątem zachowania poprzedniego. Ten algorytm uczenia maszynowego uwzględnia między innymi różne czynniki czas między dwoma logowaniami i czas, który miał zostać przesłany przez użytkownika z pierwszej lokalizacji do drugiego, co oznacza, że inny użytkownik korzysta z tego samego uwierzytelniające.

Algorytm ignoruje oczywiste "fałszywie dodatnie" przyczyniające się do niemożliwych warunków podróży, takich jak sieci VPN i lokalizacje regularnie używane przez innych użytkowników w organizacji. System ma początkowy okres uczenia wynoszący 14 dni, podczas którego nauczy się zachowanie logowania nowego użytkownika. 

### <a name="sign-in-from-unfamiliar-locations"></a>Logowanie z nieznanych lokalizacji

Ten typ zdarzenia ryzyka uwzględnia lokalizacje logowania (IP, szerokości/długości geograficznej i ASN), aby określić nowe/nieznane lokalizacje. System przechowuje informacje o poprzednich lokalizacjach używanych przez użytkownika i uwzględnia te "znane" lokalizacje. Zdarzenie ryzyka jest wyzwalane, gdy logowanie następuje z lokalizacji, która nie znajduje się na liście znanych lokalizacji. System ma początkowy okres uczenia 30 dni, podczas którego nie flaguje żadnych nowych lokalizacji jako nieznanych lokalizacji. System ignoruje logowania ze znanych urządzeń i lokalizacje, które znajdują się geograficznie blisko znanej lokalizacji. 

Program Identity Protection wykrywa logowania z nieznanych lokalizacji również dla uwierzytelniania podstawowego/starszych protokołów. Ponieważ te protokoły nie mają nowoczesnych znanych funkcji, takich jak identyfikator klienta, nie ma wystarczającej ilości danych telemetrycznych, aby zmniejszyć liczbę fałszywie dodatnich. Aby zmniejszyć liczbę zdarzeń wykrytych zagrożeń, należy przejść do nowoczesnego uwierzytelniania.   

### <a name="sign-ins-from-infected-devices"></a>Logowania z zainfekowanych urządzeń

Ten typ zdarzenia pozwala zidentyfikować logowania z urządzeń zainfekowanych złośliwym oprogramowaniem, które są znane, aby aktywnie komunikować się z serwerem bot. Jest to określane przez skorelowanie adresów IP urządzenia użytkownika z adresami IP, które były w kontakcie z serwerem bot. 

### <a name="sign-ins-from-ip-addresses-with-suspicious-activity"></a>Logowania z adresów IP z podejrzaną aktywnością
To zdarzenie ryzyka określa adresy IP, z których były widoczne duże liczby nieudanych prób logowania na wielu kontach użytkowników w krótkim czasie. Dopasowuje to wzorce ruchu adresów IP używanych przez osoby atakujące i jest silnym wskaźnikiem, że konta są już lub zostały naruszone. Jest to algorytm uczenia maszynowego, który ignoruje oczywiste fałszywe-pozytywne, takie jak adresy IP, które są regularnie używane przez innych użytkowników w organizacji.  System ma początkowy okres uczenia wynoszący 14 dni, w którym nauczysz zachowanie logowania nowego użytkownika i nowej dzierżawy.

## <a name="detection-type"></a>Typ wykrywania

Właściwość typ wykrywania jest wskaźnikiem (w**czasie rzeczywistym** lub **w trybie offline**) dla interwału wykrycia zdarzenia o podwyższonym ryzyku. Obecnie przed wystąpieniem ryzyka wykryto w trybie offline zdarzenia z największą ilością ryzyka po wystąpieniu zdarzenia.

W poniższej tabeli wymieniono czas, jaki jest potrzebny dla typu wykrywania do wyświetlenia w powiązanym raporcie:

| Typ wykrywania | Raportowanie opóźnienia |
| --- | --- |
| Czas rzeczywisty | od 5 do 10 minut |
| W trybie offline | od 2 do 4 godzin |


W przypadku typów zdarzeń o podwyższonym ryzyku Azure Active Directory wykrywane są następujące typy wykrywania:

| Typ zdarzenia ryzyka | Typ wykrywania |
| :-- | --- | 
| [Użytkownicy z ujawnionymi poświadczeniami](#leaked-credentials) | W trybie offline |
| [Logowania z anonimowych adresów IP](#sign-ins-from-anonymous-ip-addresses) | Czas rzeczywisty |
| [Niemożliwa podróż do nietypowych lokalizacji](#impossible-travel-to-atypical-locations) | W trybie offline |
| [Logowania z nieznanych lokalizacji](#sign-in-from-unfamiliar-locations) | Czas rzeczywisty |
| [Logowania z zainfekowanych urządzeń](#sign-ins-from-infected-devices) | W trybie offline |
| [Logowania z adresów IP z podejrzaną aktywnością](#sign-ins-from-ip-addresses-with-suspicious-activity) | W trybie offline|


## <a name="risk-level"></a>Poziom ryzyka

Właściwość poziomu ryzyka dla zdarzenia o podwyższonym ryzyku to wskaźnik (**wysoki**, **Średni**lub **niski**) dotyczący ważności i zaufania zdarzenia ryzyka. Ta właściwość pomaga określić priorytety działań, które należy wykonać. 

Ważność zdarzenia ryzyka reprezentuje siłę sygnału jako predykcyjny kompromis dotyczący naruszenia tożsamości. Wiarygodność jest wskaźnikiem dla możliwości fałszywych wartości dodatnich. 

Na przykład 

* **Wysoka**: Zdarzenie o wysokim poziomie pewności i o wysokim stopniu ważności. Te zdarzenia są silnymi wskaźnikami, w przypadku których zabezpieczenia tożsamości zostały naruszone, a wszystkie konta użytkowników, których to dotyczy, powinny być natychmiast korygowane.

* **Średni**: Wysoka ważność, ale niższe ryzyko związane z zaufaniem lub na odwrót. Zdarzenia te są potencjalnie ryzykowne i należy skorygować wszystkie konta użytkowników, których dotyczy problem.

* **Niska**: Zdarzenie niskiej pewności i niskiego poziomu ważności. To zdarzenie może nie wymagać natychmiastowego działania, ale w połączeniu z innymi zdarzeniami o podwyższonym ryzyku może zapewnić silne wskazanie naruszenia bezpieczeństwa tożsamości.

![Poziom ryzyka](./media/concept-risk-events/01.png)

### <a name="leaked-credentials"></a>Ujawnione poświadczenia

Zdarzenia ryzyka dotyczącego przecieków poświadczeń są klasyfikowane jako **wysokie**, ponieważ zapewniają jasne wskazanie, że nazwa użytkownika i hasło są dostępne dla osoby atakującej.

### <a name="sign-ins-from-anonymous-ip-addresses"></a>Logowania z anonimowych adresów IP

Poziom ryzyka dla tego typu zdarzenia ryzyka jest **Średni** , ponieważ anonimowy adres IP nie jest silnym wskaźnikiem naruszenia zabezpieczeń konta. Zalecamy natychmiastowe skontaktowanie się z użytkownikiem w celu sprawdzenia, czy korzystali z anonimowych adresów IP.


### <a name="impossible-travel-to-atypical-locations"></a>Niemożliwa podróż do nietypowych lokalizacji

Niemożliwa podróż jest zwykle dobrym wskaźnikiem, że haker mógł pomyślnie się zalogować. Jednak fałszywe-pozytywne mogą wystąpić, gdy użytkownik podróżuje przy użyciu nowego urządzenia lub sieci VPN, które zwykle nie są używane przez innych użytkowników w organizacji. Innym źródłem fałszywych wartości fałszywych są aplikacje, które niepoprawnie przechodzą adresy IP serwera jako adresy IP klienta, co może spowodować pojawienie się miejsca logowania z centrum danych, w którym jest hostowana wewnętrzna aplikacja. może nadawać wygląd logowania z adresów IP należących do firmy Microsoft). W wyniku tych fałszywych, poziom ryzyka dla tego zdarzenia o podwyższonym ryzyku to **Średni**.

> [!TIP]
> Można zmniejszyć liczbę raportowanych fałszywych wartości false dla tego typu zdarzenia ryzyka przez skonfigurowanie [nazwanych lokalizacji](../active-directory-named-locations.md). 

### <a name="sign-in-from-unfamiliar-locations"></a>Logowanie z nieznanych lokalizacji

Nieznane lokalizacje mogą zapewnić mocną wskazówkę, że osoba atakująca może korzystać z skradzionej tożsamości. Fałszywe-pozytywne mogą wystąpić, gdy użytkownik podróżuje, próbuje nowe urządzenie lub korzysta z nowej sieci VPN. W wyniku tych fałszywych wartości dodatnich poziom ryzyka dla tego typu zdarzenia to **Średni**.

### <a name="sign-ins-from-infected-devices"></a>Logowania z zainfekowanych urządzeń

To zdarzenie ryzyka identyfikuje adresy IP, a nie urządzenia użytkowników. Jeśli kilka urządzeń znajduje się za pojedynczym adresem IP, a tylko niektóre z nich są kontrolowane przez sieć bot, logowania z innych urządzeń wyzwalają to zdarzenie niekoniecznie, co oznacza, że to zdarzenie ryzyka jest klasyfikowane jako **niskie**.  

Zalecamy skontaktowanie się z użytkownikiem i przeskanowanie wszystkich urządzeń użytkownika. Istnieje również możliwość, że urządzenie osobiste użytkownika jest zainfekowane lub że ktoś inny używa zainfekowanego urządzenia z tego samego adresu IP, który jest używany przez użytkownika. Zainfekowane urządzenia są często Zainfekowane złośliwym oprogramowaniem, które nie zostało jeszcze zidentyfikowane przez oprogramowanie antywirusowe, i mogą także wskazywać wszelkie złe nawyki użytkowników, które mogą spowodować zainfekowanie urządzenia.

Więcej informacji o sposobie rozwiązywania infekcji złośliwym oprogramowaniem znajduje się w [Centrum ochrony przed złośliwym](https://www.microsoft.com/en-us/security/portal/definitions/adl.aspx/)oprogramowaniem.

### <a name="sign-ins-from-ip-addresses-with-suspicious-activity"></a>Logowania z adresów IP z podejrzaną aktywnością

Zalecamy skontaktowanie się z użytkownikiem w celu sprawdzenia, czy rzeczywiście zalogowano się z adresu IP, który został oznaczony jako podejrzany. Poziomem ryzyka dla tego typu zdarzenia jest "**średnia**", ponieważ niektóre urządzenia mogą znajdować się za tym samym adresem IP, podczas gdy tylko niektóre mogą być odpowiedzialne za podejrzane działania. 


## <a name="next-steps"></a>Następne kroki

* [Raport o zabezpieczeniach użytkowników z ryzykiem](concept-user-at-risk.md)
* [Raport zabezpieczeń dotyczący ryzykownych logowań](concept-risky-sign-ins.md)
* [Azure AD Identity Protection](../active-directory-identityprotection.md).
