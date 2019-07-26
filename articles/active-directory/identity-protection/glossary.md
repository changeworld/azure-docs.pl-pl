---
title: Słownik Azure Active Directory Identity Protection | Microsoft Docs
description: Azure Active Directory Identity Protection słownik
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: reference
ms.date: 01/15/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6751fe74bfd9b8a07aec2263582d9f1a4bc0b2ac
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/19/2019
ms.locfileid: "68333983"
---
# <a name="azure-active-directory-identity-protection-glossary"></a>Azure Active Directory Identity Protection słownik

### <a name="at-risk-user"></a>Zagrożone (użytkownik)
Użytkownik z co najmniej jednym aktywnym zdarzeniem związanym z ryzykiem. 

### <a name="atypical-sign-in-location"></a>Nietypowa lokalizacja logowania
Logowanie z lokalizacji geograficznej, która nie jest typowa dla określonego użytkownika, podobnych użytkowników lub dzierżawcy.

### <a name="azure-ad-identity-protection"></a>Usługa Azure AD Identity Protection
Moduł zabezpieczeń Azure Active Directory, który zapewnia skonsolidowany wgląd w zdarzenia o podwyższonym ryzyku i potencjalne luki w zabezpieczeniach wpływających na tożsamości organizacji.

### <a name="conditional-access"></a>Dostęp warunkowy
Zasady zabezpieczające dostęp do zasobów. Reguły dostępu warunkowego są przechowywane w Azure Active Directory i są oceniane przez usługę Azure AD przed przyznaniem dostępu do zasobu.  Przykładowe reguły obejmują ograniczanie dostępu na podstawie lokalizacji użytkownika, kondycji urządzenia lub metody uwierzytelniania użytkownika.

### <a name="credentials"></a>Poświadczenia
Informacje, które obejmują identyfikację i weryfikację tożsamości, która jest używana do uzyskiwania dostępu do zasobów lokalnych i sieciowych. Przykładami poświadczeń są nazwy i hasła użytkowników, karty inteligentne i certyfikaty.

### <a name="event"></a>Wydarzenie
Rekord działania w Azure Active Directory.

### <a name="false-positive-risk-event"></a>False-pozytywne (zdarzenie ryzyka)
Stan zdarzenia ryzyka ustawiany ręcznie przez użytkownika ochrony tożsamości, wskazujący, że zdarzenie ryzyka zostało zbadane i zostało nieprawidłowo oflagowane jako zdarzenie ryzyka.

### <a name="identity"></a>Tożsamość
Osoba lub jednostka, która musi być zweryfikowana przy użyciu uwierzytelniania, na podstawie kryteriów, takich jak hasło lub certyfikat.

### <a name="identity-risk-event"></a>Zdarzenie związane z ryzykiem tożsamości
Zdarzenie usługi AAD oflagowane jako nietypowe przez program Identity Protection i może wskazywać, że zabezpieczenia tożsamości zostały naruszone.

### <a name="ignored-risk-event"></a>Zignorowane (zdarzenie ryzyka)
Stan zdarzenia ryzyka ustawiany ręcznie przez użytkownika ochrony tożsamości, wskazujący, że zdarzenie ryzyka jest zamknięte bez podejmowania akcji korygowania.

### <a name="impossible-travel-from-atypical-locations"></a>Niemożliwa podróż z nietypowych lokalizacji
Zdarzenie o podwyższonym ryzyku wyzwalane w przypadku wykrycia dwóch logowań dla tego samego użytkownika, jeśli co najmniej jeden z nich pochodzi z nietypowej lokalizacji logowania, a czas między logowaniami jest krótszy niż minimalny czas, który będzie fizycznie przejechać między tymi lokalizacjami.  

### <a name="investigation"></a>Badanie
Proces przeglądania działań, dzienników i innych istotnych informacji związanych ze zdarzeniem ryzyka w celu podjęcia decyzji o tym, czy czynności zaradcze lub zaradcze są niezbędne, Dowiedz się, czy i w jaki sposób została naruszona tożsamość, a także zrozumieć, jak złamano tożsamość została użyta.

### <a name="leaked-credentials"></a>Ujawnione poświadczenia
Zdarzenie o podwyższonym ryzyku wyzwalane, gdy bieżące poświadczenia użytkownika (nazwa użytkownika i hasło) zostały ogłoszone publicznie w ciemnej sieci Web przez naszych pracowników.

### <a name="mitigation"></a>Środki zaradcze
Akcja ograniczająca lub eliminująca zdolność osoby atakującej do korzystania ze złamanej tożsamości lub urządzenia bez przywracania tożsamości lub urządzenia do stanu bezpiecznego. Środki zaradcze nie rozwiązują poprzednich zdarzeń o podwyższonym ryzyku skojarzonych z tożsamością lub urządzeniem.

### <a name="multi-factor-authentication"></a>Uwierzytelnianie wieloskładnikowe
Metoda uwierzytelniania, która wymaga co najmniej dwóch metod uwierzytelniania, które mogą obejmować coś, co użytkownik ma, takiego certyfikatu; coś znanego przez użytkownika, takie jak nazwy użytkowników, hasła lub frazy przekazywania; atrybuty fizyczne, takie jak odcisk palca; i atrybutami osobistymi, takimi jak podpisy osobiste.

### <a name="offline-detection"></a>Wykrywanie w trybie offline
Wykrywanie anomalii i oceny ryzyka zdarzenia, takiego jak próba logowania po fakcie, dla zdarzenia, które już się zakończyło.

### <a name="policy-condition"></a>Warunek zasad
Część zasad zabezpieczeń, która definiuje jednostki (grupy, użytkownicy, aplikacje, platformy urządzeń, Stany urządzeń, zakresy adresów IP, typy klientów) zawarte w zasadach lub z nich wykluczone.

### <a name="policy-rule"></a>Reguła zasad
Część zasad zabezpieczeń opisująca warunki, które spowodują wyzwolenie zasad, oraz akcje podejmowane w momencie wyzwolenia zasad.

### <a name="prevention"></a>Zapobieganie
Akcja zapobiegająca uszkodzeniu organizacji za pomocą nadużycia tożsamości lub urządzenia podejrzanych lub świadomych naruszenia. Akcja zapobiegania nie zabezpiecza urządzenia lub tożsamości i nie rozwiązuje poprzednich zdarzeń związanych z ryzykiem.

### <a name="privileged-user"></a>Uprzywilejowany (użytkownik)
Użytkownik, który w momencie wystąpienia ryzyka ma trwałe lub tymczasowe uprawnienia administratora do co najmniej jednego zasobu w Azure Active Directory, takich jak Administrator globalny, administrator rozliczeń, administrator usługi, administrator użytkownika i hasło. 

### <a name="real-time"></a>Czas rzeczywisty
Zobacz wykrywanie w czasie rzeczywistym.

### <a name="real-time-detection"></a>Wykrywanie w czasie rzeczywistym
Wykrywanie anomalii i oceny ryzyka zdarzenia, takiego jak próba logowania, zanim będzie możliwe kontynuowanie zdarzenia.

### <a name="remediated-risk-event"></a>Skorygowane (zdarzenie ryzyka)
Stan zdarzenia ryzyka ustawiany automatycznie przez ochronę tożsamości, wskazując, że zdarzenie ryzyka zostało skorygowane przy użyciu standardowej akcji korygowania dla tego typu zdarzenia o podwyższonym ryzyku. Na przykład, gdy hasło użytkownika zostanie zresetowane, wiele zdarzeń ryzyka wskazujących na naruszenie bezpieczeństwa poprzedniego hasła jest automatycznie korygowane.

### <a name="remediation"></a>Korygowanie
Akcja zabezpieczania tożsamości lub urządzenia, które zostały wcześniej podejrzane lub uznane za zagrożone. Akcja korygowania przywraca tożsamość lub urządzenie do stanu bezpiecznego i rozwiązuje poprzednie zdarzenia dotyczące ryzyka związane z tożsamością lub urządzeniem.

### <a name="resolved-risk-event"></a>Rozwiązano (zdarzenie ryzyka)
Stan zdarzenia o podwyższonym ryzyku ustawiany ręcznie przez użytkownika ochrony tożsamości, wskazujący, że użytkownik wykonał odpowiednią akcję korygowania poza usługą Identity Protection i że zdarzenie ryzyka powinno być uznawane za zamknięte.

### <a name="risk-event-status"></a>Stan zdarzenia ryzyka
Właściwość zdarzenia o podwyższonym ryzyku wskazująca, czy zdarzenie jest aktywne, a jeśli jest zamknięte, powód jego zamknięcia.

### <a name="risk-event-type"></a>Typ zdarzenia o podwyższonym ryzyku
Kategoria dla zdarzenia o podwyższonym ryzyku wskazująca typ anomalii, który spowodował, że zdarzenie jest uważane za ryzykowne.

### <a name="risk-level-risk-event"></a>Poziom ryzyka (zdarzenie ryzyka)
Wskazanie (wysoka, średnia lub niska) ważności zdarzenia ryzyka, które ułatwiają użytkownikom ochrony tożsamości określanie priorytetów działań podejmowanych w celu zmniejszenia ryzyka dla organizacji. 

### <a name="risk-level-sign-in"></a>Poziom ryzyka (logowanie)
Wskazanie (wysoki, średni lub niski) prawdopodobieństwa dotyczącego określonego logowania, ktoś inny próbuje użyć tożsamości użytkownika.

### <a name="risk-level-user-compromise"></a>Poziom ryzyka (naruszenie użytkownika)
Wskazanie (wysoki, średni lub niski) prawdopodobieństwa naruszenia zabezpieczeń tożsamości.

### <a name="risk-level-vulnerability"></a>Poziom ryzyka (Luka w zabezpieczeniach)
Wskazanie (wysoka, średnia lub niska) ważności luki w zabezpieczeniach w celu ułatwienia użytkownikom ochrony tożsamości określanie priorytetów działań podejmowanych w celu zmniejszenia ryzyka dla organizacji.

### <a name="secure-identity"></a>Bezpieczne (tożsamość)
Wykonaj akcje korygowania, takie jak zmiana hasła lub odtwarzanie maszynowe, aby przywrócić potencjalnie naruszoną tożsamość w stanie nienaruszonym.

### <a name="security-policy"></a>Zasady zabezpieczeń
Kolekcja reguł i warunków zasad. Zasady mogą być stosowane do jednostek, takich jak użytkownicy, grupy, aplikacje, urządzenia, platformy urządzeń, Stany urządzeń, zakresy adresów IP i typy klientów uwierzytelniania 2.0. Gdy zasady są włączone, są oceniane za każdym razem, gdy jednostka uwzględniona w zasadach wystawia token dla zasobu.

### <a name="sign-in-v"></a>Logowanie (v)
Aby uwierzytelnić się w tożsamości w Azure Active Directory.

### <a name="sign-in-n"></a>Logowanie (n)
Proces lub akcja uwierzytelniania tożsamości w Azure Active Directory i zdarzenia, które przechwytuje tę operację.

### <a name="sign-in-from-anonymous-ip-address"></a>Logowanie z anonimowego adresu IP
Zdarzenie o podwyższonym ryzyku wyzwalane po pomyślnym zalogowaniu z adresu IP, który został zidentyfikowany jako anonimowy adres IP serwera proxy.

### <a name="sign-in-from-infected-device"></a>Logowanie z zainfekowanego urządzenia
Zdarzenie ryzyka wyzwalane, gdy logowanie pochodzi z adresu IP, który jest znany jako używany przez co najmniej jedno zagrożone urządzenie, które aktywnie podejmuje próbę nawiązania połączenia z serwerem bot.

### <a name="sign-in-from-ip-address-with-suspicious-activity"></a>Zaloguj się przy użyciu adresu IP z podejrzaną aktywnością
Zdarzenie o podwyższonym ryzyku wyzwalane po pomyślnym zalogowaniu się z adresu IP o dużej liczbie nieudanych prób zalogowania dla wielu kont użytkowników w krótkim czasie.

### <a name="sign-in-from-unfamiliar-location"></a>Zaloguj się z nieznanej lokalizacji
Zdarzenie o podwyższonym ryzyku wyzwalane, gdy użytkownik pomyślnie zaloguje się z nowej lokalizacji (adres IP, Szerokość geograficzna/długość i numer ASN).

### <a name="sign-in-risk"></a>Ryzyko logowania
Zobacz poziom ryzyka (logowanie)

### <a name="sign-in-risk-policy"></a>Zasady dotyczące ryzyka związanego z logowaniem
Zasady dostępu warunkowego, które szacują ryzyko związane z określonym logowaniem i stosują środki zaradcze na podstawie wstępnie zdefiniowanych warunków i reguł.

### <a name="user-compromise-risk"></a>Zagrożenie naruszenia użytkownika
Zobacz poziom ryzyka (naruszenie użytkownika)

### <a name="user-risk"></a>Ryzyko związane z użytkownikiem
Zobacz poziom ryzyka (naruszenie użytkowników).

### <a name="user-risk-policy"></a>Zasady dotyczące ryzyka związanego z użytkownikiem
Zasady dostępu warunkowego, które traktują logowanie i stosują środki zaradcze na podstawie wstępnie zdefiniowanych warunków i reguł.

### <a name="users-flagged-for-risk"></a>Użytkownicy oflagowani w związku z ryzykiem
Użytkownicy, którzy mają zdarzenia o podwyższonym ryzyku, które są aktywne lub skorygowane

### <a name="vulnerability"></a>Luka w zabezpieczeniach
Konfiguracja lub warunek w Azure Active Directory, który sprawia, że katalog jest podatny na luki w zabezpieczeniach lub zagrożeniach.

## <a name="see-also"></a>Zobacz także
* [Ochrona tożsamości w usłudze Azure Active Directory](../active-directory-identityprotection.md)
