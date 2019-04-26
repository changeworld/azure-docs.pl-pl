---
title: Usługa Azure Active Directory Identity Protection słownik | Dokumentacja firmy Microsoft
description: Usługa Azure Active Directory Identity Protection słownik
services: active-directory
keywords: Usługa Azure active directory identity protection odnajdywania aplikacji w chmurze, zarządzanie aplikacji, zabezpieczenia, ryzyka, poziom ryzyka, luk w zabezpieczeniach, zasady zabezpieczeń, słownik
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: daveba
ms.assetid: 833119a5-33d6-4482-adda-fa35218c72c3
ms.service: active-directory
ms.subservice: identity-protection
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2018
ms.author: joflore
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5def46b6c12dc08c273ea7334516b63b7606477f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60452827"
---
# <a name="azure-active-directory-identity-protection-glossary"></a>Usługa Azure Active Directory Identity Protection słownik
### <a name="at-risk-user"></a>Zagrożone (użytkownik)
Użytkownik z jednego lub wielu zdarzeń aktywnego ryzyka. 

### <a name="atypical-sign-in-location"></a>Lokalizacja nietypowe logowania
Zaloguj się za pomocą lokalizacji geograficznej, który nie jest typowe dla określonego użytkownika, podobnych użytkowników lub dzierżawcy.

### <a name="azure-ad-identity-protection"></a>Usługa Azure AD Identity Protection
Moduł zabezpieczeń usługi Azure Active Directory, która zapewnia skonsolidowany wgląd w zdarzenia o podwyższonym ryzyku i potencjalnych luk w zabezpieczeniach mające wpływ na tożsamości w organizacji.

### <a name="conditional-access"></a>Dostęp warunkowy
Zasady dotyczące zabezpieczania dostępu do zasobów. Zasady dostępu warunkowego są przechowywane w usłudze Azure Active Directory i są oceniane przez usługę Azure AD przed udzieleniem im dostępu do zasobu.  Przykład reguły obejmują, ograniczanie dostępu na podstawie lokalizacji użytkownika, kondycja urządzenia lub metoda uwierzytelniania użytkowników.

### <a name="credentials"></a>Poświadczenia
Informacje, które obejmują identyfikator i potwierdzenie, które jest używane do uzyskiwania dostępu do lokalnych i zasobów sieciowych. Przykładami poświadczeń są nazwy użytkownika i hasła, karty inteligentne i certyfikaty.

### <a name="event"></a>Wydarzenie
Rekord działania w usłudze Azure Active Directory.

### <a name="false-positive-risk-event"></a>Fałszywie dodatnie (zdarzenie o podwyższonym ryzyku)
Stan zdarzenia o podwyższonym ryzyku, Ustaw ręcznie przez użytkownika Identity Protection, informujący, że zdarzenie o podwyższonym ryzyku zostało zbadane i został niepoprawnie oflagowana jako zdarzenie o podwyższonym ryzyku.

### <a name="identity"></a>Tożsamość
Osoba lub podmiot, który musi zostać zweryfikowany za pomocą uwierzytelniania, na podstawie kryteriów, takich jak hasła lub certyfikatu.

### <a name="identity-risk-event"></a>Tożsamość, zdarzenia o podwyższonym ryzyku
Zdarzenie usługi AAD została oflagowana jako nietypowe przez ochronę tożsamości, która może wskazywać, że tożsamość bezpieczeństwo zostało naruszone.

### <a name="ignored-risk-event"></a>Ignorowane (zdarzenie o podwyższonym ryzyku)
Stan zdarzenia o podwyższonym ryzyku, Ustaw ręcznie przez użytkownika Identity Protection, wskazujący, że zdarzenie o podwyższonym ryzyku jest zamknięty bez podejmowania działań korygujących.

### <a name="impossible-travel-from-atypical-locations"></a>Więc podróż jest wykluczona z nietypowych lokalizacji
Zdarzenia o podwyższonym ryzyku, wyzwalane, gdy dwa logowania dla tego samego użytkownika zostaną wykryte, gdzie co najmniej jeden z nich jest z nietypowej lokalizacji logowania, a czas między operacji logowania jest krótszy niż minimalny czas, jaki zajęłoby fizycznie podróży między tymi lokacjami.  

### <a name="investigation"></a>Badanie
Proces oceny działania, dzienniki i inne istotne informacje dotyczące zdarzenia o podwyższonym ryzyku, można zdecydować, czy kroki korygowania i ograniczania ryzyka są niezbędne, zrozumieć, jeśli i jak tożsamość zostało naruszone i zrozumieć sposób, w jaki naruszenia zabezpieczeń tożsamości została użyta.

### <a name="leaked-credentials"></a>Ujawnione poświadczenia
Zdarzenia o podwyższonym ryzyku, wyzwalane, gdy bieżące poświadczenia użytkownika (nazwy użytkownika i hasło) zostaną znalezione, odpowiadając publicznie w Internecie ciemny naszych pracowników naukowo-badawczych.

### <a name="mitigation"></a>Środki zaradcze
Akcja celu ograniczenie lub wyeliminowanie możliwość osobie atakującej wykorzystanie tożsamości ze złamanymi zabezpieczeniami lub urządzenia bez przywracania do tożsamości lub urządzenia do stanu bezpiecznego. Ograniczenia nie można rozpoznać poprzednie zdarzenia o podwyższonym ryzyku skojarzone z tożsamością lub urządzenia.

### <a name="multi-factor-authentication"></a>Uwierzytelnianie wieloskładnikowe
Metoda uwierzytelniania, która wymaga co najmniej dwóch metod uwierzytelniania, które mogą obejmować coś, co użytkownik ma taki certyfikat; coś, co użytkownik wie, takie jak nazwy użytkowników, hasła lub wyrażenia — dostęp próbny; fizyczne atrybuty, takie jak thumbprint; i osobistych atrybutów, takich jak osobisty podpis.

### <a name="offline-detection"></a>Wykrywanie w trybie offline
Wykrywanie anomalii i oceny ryzyka zdarzenie, takie jak próby logowania w późniejszym czasie dla zdarzenia, które zostało już przeprowadzone.

### <a name="policy-condition"></a>Warunek zasady
Część zasady zabezpieczeń, który definiuje jednostki (grup, użytkownicy, aplikacje, platformy urządzeń, Stany urządzeń, zakresy adresów IP, typy klientów) uwzględnionych w zasadach lub wykluczone z niego.

### <a name="policy-rule"></a>Reguła zasad
Część zasady zabezpieczeń, który opisuje okoliczności, które będą wyzwalać zasad i akcji wykonanych po wyzwoleniu zasad.

### <a name="prevention"></a>Zapobieganie
Działanie, aby zapobiec uszkodzeniu organizacji za pośrednictwem nadużycie tożsamości lub urządzenia podejrzewa lub wiedzieć, aby być narażone na ataki. Akcja zapobiegania nie zabezpieczyć urządzenia lub tożsamości, a nie zostanie rozpoznana poprzednie zdarzenia o podwyższonym ryzyku.

### <a name="privileged-user"></a>Uprzywilejowany (użytkownik)
Użytkownik, który w czasie zdarzenia o podwyższonym ryzyku, ma uprawnienia administratora stałych lub tymczasowych do jednego lub kilku zasobów usługi Azure Active Directory, takie jak Administrator globalny, Administrator rozliczeń, Administrator usługi, administrator użytkownika i hasło administratora. 

### <a name="real-time"></a>Czas rzeczywisty
Zobacz wykrywania w czasie rzeczywistym.

### <a name="real-time-detection"></a>Wykrywanie w czasie rzeczywistym
Wykrywanie anomalii i oceny ryzyka zdarzenia, takiego jak prób logowania przed zdarzeniem może kontynuować.

### <a name="remediated-risk-event"></a>Skorygowane (zdarzenie o podwyższonym ryzyku)
Stan zdarzenia o podwyższonym ryzyku, ustawiane przez ochronę tożsamości, wskazującą, czy zdarzenie o podwyższonym ryzyku korygowania przy użyciu akcji korygowania standardowego dla tego typu zdarzenia o podwyższonym ryzyku. Na przykład po zresetowaniu hasła użytkownika wiele zdarzeń o podwyższonym ryzyku, które wskazują, że poprzednie hasło zostało naruszone są rozwiązywane automatycznie.

### <a name="remediation"></a>Korygowanie
Akcja, aby zabezpieczyć tożsamość lub urządzeń, które zostały wcześniej podejrzenie lub znane naruszenia. Akcja korygowania przywraca tożsamości lub urządzenia do stanu bezpiecznego i jest rozpoznawana jako poprzednie zdarzenia o podwyższonym ryzyku skojarzone z tożsamością lub urządzenia.

### <a name="resolved-risk-event"></a>Rozwiązany (zdarzenie o podwyższonym ryzyku)
Stan zdarzenia o podwyższonym ryzyku, Ustaw ręcznie przez użytkownika w usłudze Identity Protection, wskazującą, czy użytkownik wykonał akcji korygowania odpowiednie poza Identity Protection i zdarzenia o podwyższonym ryzyku należy uwzględnić zamknięte.

### <a name="risk-event-status"></a>Stan zdarzenia o podwyższonym ryzyku
Właściwości zdarzenia o podwyższonym ryzyku, wskazującą, czy zdarzenie jest aktywne, a jeśli zamknięty, przyczyna jej zamknięciem.

### <a name="risk-event-type"></a>Typ zdarzenia o podwyższonym ryzyku
Kategoria zdarzenia o podwyższonym ryzyku, wskazujący typ anomalii, która wywołała zdarzenie zostały uznane za ryzykowne.

### <a name="risk-level-risk-event"></a>Poziom ryzyka (zdarzenie o podwyższonym ryzyku)
Wskazanie (wysoki, średni lub niski) określające ważność zdarzenia o podwyższonym ryzyku, aby ułatwić użytkownikom ochronę tożsamości priorytety działania podjęte w celu zmniejszenia ryzyka dla organizacji. 

### <a name="risk-level-sign-in"></a>Poziom ryzyka (sign-in)
Wskazanie (wysoki, średni lub niski) prawdopodobieństwo, że dla określonych logowania, ktoś próbuje użyć tożsamość użytkownika.

### <a name="risk-level-user-compromise"></a>Poziom ryzyka (naruszenia zabezpieczeń użytkownika)
Wskazanie (wysoki, średni lub niski) prawdopodobieństwo, że tożsamość bezpieczeństwo zostało naruszone.

### <a name="risk-level-vulnerability"></a>Poziom ryzyka (usterki)
Wskazanie (wysoki, średni lub niski) określające ważność luk w zabezpieczeniach, aby ułatwić użytkownikom ochronę tożsamości priorytety działania podjęte w celu zmniejszenia ryzyka dla organizacji.

### <a name="secure-identity"></a>Zabezpieczenia (tożsamości)
Podjąć akcje naprawcze, takie jak zmiana hasła lub odtwarzanie z obrazu do przywrócenia tożsamości mogą mieć złamane zabezpieczenia Niezrównana stan maszyny.

### <a name="security-policy"></a>Zasady zabezpieczeń
Kolekcja reguł zasad i warunków. Zasady mogą dotyczyć jednostek, takich jak użytkownicy, grupy, aplikacje, urządzenia, platformy urządzeń, Stany urządzeń, zakresy adresów IP i typów klienta OAuth 2.0. Gdy jest włączona, zostanie ocenione, zawsze wtedy, gdy jednostki uwzględnionych w zasadach wystawiono tokenu do zasobu.

### <a name="sign-in-v"></a>Zaloguj się (v)
Do uwierzytelniania tożsamości w usłudze Azure Active Directory.

### <a name="sign-in-n"></a>Zaloguj (n)
Proces lub akcji uwierzytelniania tożsamości w usłudze Azure Active Directory i zdarzenia, które przechwytuje tej operacji.

### <a name="sign-in-from-anonymous-ip-address"></a>Zaloguj się za pomocą anonimowego adresu IP
Zdarzenia o podwyższonym ryzyku wyzwalane po pomyślnym zalogowaniu z adresu IP, który został zidentyfikowany jako adres IP anonimowego serwera proxy.

### <a name="sign-in-from-infected-device"></a>Zaloguj się za pomocą zainfekowanego urządzenia
Wyzwalane, gdy logowania pochodzi z adresu IP, który jest znany, który będzie używany przez co najmniej jedno urządzenie ze złamanymi zabezpieczeniami, które aktywnie próby komunikacji z serwerem bot zdarzenie o podwyższonym ryzyku.

### <a name="sign-in-from-ip-address-with-suspicious-activity"></a>Zaloguj się za pomocą adresu IP z podejrzaną aktywnością
Zdarzenia o podwyższonym ryzyku wyzwalane po pomyślne logowanie z adresu IP adresów z dużą liczbę nieudanych prób logowania na wielu kontach użytkowników w krótkim okresie.

### <a name="sign-in-from-unfamiliar-location"></a>Zaloguj się z nieznanej lokalizacji
Zdarzenia o podwyższonym ryzyku, wyzwalane, gdy użytkownik pomyślnie loguje się z nowej lokalizacji (IP, szerokości/długości geograficznej i jego numer ASN).

### <a name="sign-in-risk"></a>Ryzyko logowania
Zobacz ryzyka poziom (sign-in)

### <a name="sign-in-risk-policy"></a>Zasady dotyczące ryzyka związanego z logowaniem
Zasady dostępu warunkowego, która ocenia ryzyko dla określonych logowania i stosuje ograniczenia na podstawie wstępnie zdefiniowanych warunków i zasad.

### <a name="user-compromise-risk"></a>Ryzyko naruszenia zabezpieczeń użytkownika
Zobacz ryzyka poziom (naruszenia zabezpieczeń użytkownika)

### <a name="user-risk"></a>Ryzyko związane z użytkownikiem
Zobacz ryzyka poziom (naruszenia zabezpieczeń użytkownika).

### <a name="user-risk-policy"></a>Zasady dotyczące ryzyka związanego z użytkownikiem
Zasady dostępu warunkowego, które uwzględnia logowania i stosuje ograniczenia na podstawie wstępnie zdefiniowanych warunków i zasad.

### <a name="users-flagged-for-risk"></a>Użytkownicy oflagowani w związku z ryzykiem
Użytkownicy, którzy mają zdarzeń o podwyższonym ryzyku, które są aktywne lub wyeliminowane

### <a name="vulnerability"></a>Luka w zabezpieczeniach
Konfiguracja lub warunku w usłudze Azure Active Directory, co sprawia, że katalog podatna na luki w zabezpieczeniach i zagrożeń.

## <a name="see-also"></a>Zobacz także
* [Ochrona tożsamości w usłudze Azure Active Directory](../active-directory-identityprotection.md)

