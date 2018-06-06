---
title: Słownik ochrony tożsamości usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Słownik ochrony tożsamości usługi Azure Active Directory
services: active-directory
keywords: ochronę tożsamości usługi Azure active directory, usługa cloud app discovery, zarządzanie aplikacjami, zabezpieczeń, ryzyka, poziom ryzyka, luki w zabezpieczeniach, zasady zabezpieczeń, słownik
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: 833119a5-33d6-4482-adda-fa35218c72c3
ms.service: active-directory
ms.component: protection
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2018
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: a9cc4a914091c91f4b2ae1c152644fe8e01747e8
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2018
ms.locfileid: "34713395"
---
# <a name="azure-active-directory-identity-protection-glossary"></a>Słownik ochrony tożsamości usługi Azure Active Directory
### <a name="at-risk-user"></a>Zagrożone (użytkownika)
Użytkownik z jednego lub wielu zdarzeń active ryzyka. 

### <a name="atypical-sign-in-location"></a>Nietypowe logowania w lokalizacji
Logowania z lokalizacji geograficznej, które nie są typowe dla określonego użytkownika, podobnych użytkowników lub dzierżawcy.

### <a name="azure-ad-identity-protection"></a>Usługa Azure AD Identity Protection
Moduł zabezpieczeń usługi Azure Active Directory, która udostępnia skonsolidowany wgląd w zdarzenia o podwyższonym ryzyku i potencjalnych luk w zabezpieczeniach wpływających na tożsamości organizacji.

### <a name="conditional-access"></a>Dostęp warunkowy
Zasady zabezpieczenia dostępu do zasobów. Zasady dostępu warunkowego są przechowywane w usłudze Azure Active Directory i są oceniane przez usługę Azure AD przed udzieleniem im dostępu do zasobu.  Przykład reguły obejmują, ograniczanie dostępu na podstawie lokalizacji użytkownika, kondycja urządzenia lub metoda uwierzytelniania użytkownika.

### <a name="credentials"></a>Poświadczenia
Informacje, która zawiera identyfikator i potwierdzenie identyfikatora, który jest używany do uzyskania dostępu do lokalnego i zasobów sieciowych. Przykładami poświadczeń są nazwy użytkownika i hasła, karty inteligentne i certyfikaty.

### <a name="event"></a>Wydarzenie
Rekord działania w usłudze Azure Active Directory.

### <a name="false-positive-risk-event"></a>Fałszywie dodatnich (ryzyka zdarzenie)
Stan zdarzenia ryzyka ustawionych ręcznie przez użytkownika ochronę tożsamości, informujący, że zdarzenie ryzyka została zbadana i został niepoprawnie oznaczone jako zdarzenie ryzyka.

### <a name="identity"></a>Tożsamość
Osoba lub podmiot, należy sprawdzić za pomocą uwierzytelniania na podstawie kryteriów, takie jak hasła lub certyfikatu.

### <a name="identity-risk-event"></a>Zdarzenie ryzyka tożsamości
Zdarzenie usługi AAD został oznaczony jako nietypowych przez ochronę tożsamości, która może wskazywać, że naruszono tożsamości.

### <a name="ignored-risk-event"></a>Ignorowane (ryzyka zdarzenie)
Stan zdarzenia ryzyka ustawionych ręcznie przez użytkownika ochronę tożsamości, wskazujący, że zdarzenia ryzyko jest zamknięte bez podejmowania działań korygujących.

### <a name="impossible-travel-from-atypical-locations"></a>Niemożliwa podróż z nietypowych lokalizacji
Zdarzenie ryzyka, wyzwalane po wykryciu dwukrotnego zalogowania do tego samego użytkownika, gdzie jest co najmniej jeden z nich z nietypowych lokalizacji logowania, a czas między sesje logowania jest krótszy niż minimalny czas, jaki zajmuje się fizycznie przesyłane między tymi lokacjami.  

### <a name="investigation"></a>Badanie
Proces oceny działania, dzienników i inne istotne informacje powiązane ze zdarzeniem ryzyka zdecydować, czy korygowania lub ograniczenie kroki są niezbędne, określić, czy i jak tożsamość zostało naruszone i zrozumieć korzystania ze złamanymi zabezpieczeniami tożsamości.

### <a name="leaked-credentials"></a>Ujawnione poświadczenia
Zdarzenie ryzyka, wyzwalane, gdy bieżące poświadczenia użytkownika (nazwy użytkownika i hasło) znajdują się publicznie opublikowane w sieci web ciemny, przez naszych pracowników naukowo-badawczych.

### <a name="mitigation"></a>Środki zaradcze
Akcja celu ograniczenie lub wyeliminowanie osoba atakująca możliwość wykorzystania złamany tożsamości lub urządzenia bez przywrócenia tożsamości lub urządzenie to bezpieczne. Środki zaradcze nie można rozpoznać poprzednie zdarzenia ryzyko związane z tożsamości lub urządzenia.

### <a name="multi-factor-authentication"></a>Uwierzytelnianie wieloskładnikowe
Metoda uwierzytelniania, która wymaga dwóch lub więcej metod uwierzytelniania, które mogą obejmować coś użytkownik ma taki certyfikat; coś użytkownik wie, takie jak nazwy użytkowników, hasła lub wyrażenia przebiegu; Atrybuty fizyczne, takie jak odcisk palca; i atrybuty osobistych, takich jak osobiste podpisu.

### <a name="offline-detection"></a>Wykrywanie w trybie offline
Wykrywanie anomalii i oceny ryzyka zdarzenia, takie jak próba logowania po fakcie, dla każdego zdarzenia, która została już wykonana.

### <a name="policy-condition"></a>Stan zasad
Część zasad zabezpieczeń, który definiuje jednostek (grup, użytkownicy, aplikacje, platformy urządzeń, Stany urządzeń, zakresy adresów IP, typy klientów) uwzględnionych w zasadach lub wykluczone z niego.

### <a name="policy-rule"></a>Reguła zasad
Część zasad zabezpieczeń, który opisuje okoliczności, które spowoduje wywołanie zasad i akcje wykonywane po wyzwoleniu zasady.

### <a name="prevention"></a>Zapobieganie
Działania w celu zapobieżenia szkody w organizacji za pomocą nadużycia tożsamości lub urządzenia podejrzanych lub znać naruszenia. Akcji związanych z zapobieganiem nie zabezpieczyć urządzenia lub tożsamości, a nie rozwiązuje poprzednie zdarzenia ryzyka.

### <a name="privileged-user"></a>Uprzywilejowane (użytkownika)
Użytkownik, który w czasie zdarzenia ryzyka, ma uprawnienia administratora stałych lub tymczasowych do co najmniej jeden zasób w usłudze Active Directory, takie jak Administrator globalny, Administrator rozliczeń, Administrator usługi, administrator użytkownika i hasło administratora. 

### <a name="real-time"></a>Czas rzeczywisty
Zobacz wykrywanie w czasie rzeczywistym.

### <a name="real-time-detection"></a>Wykrywanie w czasie rzeczywistym
Wykrywanie anomalii i oceny ryzyka zdarzenia, takie jak prób logowania przed zdarzeniem można kontynuować.

### <a name="remediated-risk-event"></a>Skorygowane (ryzyka zdarzenie)
Stan zdarzenia ryzyka ustawione automatycznie ochrony tożsamości i wskazujący, że zdarzenia ryzyka korygowania przy użyciu akcji korygowania standardowe dla tego typu zdarzenia ryzyka. Na przykład po zresetowaniu hasła użytkownika wielu zdarzeń ryzyka, które wskazują, że zostało naruszone poprzednich haseł są rozwiązywane automatycznie.

### <a name="remediation"></a>Korygowanie
Akcja do zabezpieczania tożsamości lub urządzeń, które zostały wcześniej podejrzanych lub znane naruszenia. Akcja korygowania przywraca tożsamości lub urządzenie to bezpieczne i usuwa poprzednie zdarzenia ryzyko związane z tożsamości lub urządzenia.

### <a name="resolved-risk-event"></a>Rozwiązany (ryzyka zdarzenie)
Stan zdarzenia ryzyka ustawionych ręcznie przez użytkownika ochronę tożsamości, wskazujący, że użytkownik miał akcji korygowania odpowiednie poza ochrony tożsamości i że zdarzenia ryzyka należy traktować jako zamknięte.

### <a name="risk-event-status"></a>Stan zdarzenia ryzyka
Właściwości zdarzenia ryzyka, wskazującą, czy zdarzenie jest aktywne, a jeśli zamknięte, przyczynę zamknięcia go.

### <a name="risk-event-type"></a>Typ zdarzenia o podwyższonym ryzyku
Kategoria zdarzenia ryzyka, wskazujący typ anomalii, który spowodował zdarzenie uważane za ryzykowne.

### <a name="risk-level-risk-event"></a>Poziom ryzyka (ryzyka zdarzenie)
Wskazanie (wysoki, średni lub niski) priorytetu zdarzenia ryzyka, aby ułatwić użytkownikom ochronę tożsamości priorytetów działania podjęte w celu zmniejszenia ryzyka do organizacji. 

### <a name="risk-level-sign-in"></a>Poziom ryzyka (logowanie)
Wskazanie (wysoki, średni lub niski) prawdopodobieństwo, że dla określonych logowania, ktoś próbuje użyć tożsamość użytkownika.

### <a name="risk-level-user-compromise"></a>Poziom ryzyka (naruszenia zabezpieczeń użytkownika)
Wskazanie (wysoki, średni lub niski) prawdopodobieństwo, że naruszono tożsamości.

### <a name="risk-level-vulnerability"></a>Poziom ryzyka (luki w zabezpieczeniach)
Wskazanie (wysoki, średni lub niski) ważności luki w zabezpieczeniach, aby ułatwić użytkownikom ochronę tożsamości priorytetów działania podjęte w celu zmniejszenia ryzyka do organizacji.

### <a name="secure-identity"></a>Secure (tożsamości)
Należy podjąć akcję naprawczą, takie jak zmiany hasła lub ponownej instalacji systemu do przywrócenia tożsamości potencjalnie złamany do stanu, który nie został naruszony maszyny.

### <a name="security-policy"></a>Zasady zabezpieczeń
Kolekcja reguł zasad i warunek. Zasady można zastosować do jednostek, takich jak użytkownicy, grupy aplikacji, urządzeń, platformy urządzeń, Stany urządzeń, zakresy adresów IP i Auth2.0 typy klientów. Gdy jest włączona, zostanie ona potraktowana zawsze, gdy jednostka uwzględnionych w zasadach jest wystawiony token dla zasobu.

### <a name="sign-in-v"></a>Zaloguj się (v)
Do uwierzytelniania tożsamości w usłudze Azure Active Directory.

### <a name="sign-in-n"></a>Logowania (n)
Proces lub akcji uwierzytelniania tożsamości w usłudze Azure Active Directory i zdarzenia, który przechwytuje tej operacji.

### <a name="sign-in-from-anonymous-ip-address"></a>Zaloguj się z anonimowego adresu IP
Zdarzenie ryzyka wyzwalane po pomyślnym zalogowaniu z adresu IP, który został zidentyfikowany jako adres IP anonimowy serwer proxy.

### <a name="sign-in-from-infected-device"></a>Zaloguj się z zainfekowanych urządzeń
Zdarzenie ryzyka, wyzwalane, gdy logowania pochodzi z adresu IP, który jest znany, który będzie używany przez co najmniej jedno urządzenie ze złamanymi zabezpieczeniami, które aktywnie próby komunikacji z serwerem botów.

### <a name="sign-in-from-ip-address-with-suspicious-activity"></a>Zaloguj się z adresu IP z podejrzanymi działaniami
Zdarzenie ryzyka wyzwalane po pomyślnym logowanie z adresu IP adresów z dużej liczby nieudanych prób logowania na wielu kontach użytkowników w krótkim przedziale czasu.

### <a name="sign-in-from-unfamiliar-location"></a>Zaloguj się z nieznanych lokalizacji
Zdarzenie ryzyka, wyzwalane, gdy użytkownik pomyślnie loguje się z nowej lokalizacji (IP szerokości geograficznej/geograficzne i ASN).

### <a name="sign-in-risk"></a>Ryzyko logowania
Zobacz ryzyka poziom (logowanie)

### <a name="sign-in-risk-policy"></a>Zasady dotyczące ryzyka związanego z logowaniem
Zasady dostępu warunkowego, która ocenia ryzyko dla określonych logowanie i stosuje środki zaradcze, na podstawie wstępnie zdefiniowane warunki i zasady.

### <a name="user-compromise-risk"></a>Ryzyko naruszenia zabezpieczeń użytkownika
Zobacz ryzyka poziom (naruszenia zabezpieczeń użytkownika)

### <a name="user-risk"></a>Ryzyko związane z użytkownikiem
Zobacz ryzyka poziom (naruszenia zabezpieczeń użytkownika).

### <a name="user-risk-policy"></a>Zasady dotyczące ryzyka związanego z użytkownikiem
Zasady dostępu warunkowego, która uwzględnia przy logowaniu i stosuje środki zaradcze, na podstawie wstępnie zdefiniowane warunki i zasady.

### <a name="users-flagged-for-risk"></a>Użytkownicy oflagowani w związku z ryzykiem
Użytkownicy, którzy mają zdarzenia ryzyka, które są aktywne lub skorygowanych

### <a name="vulnerability"></a>Luka w zabezpieczeniach
Konfiguracja lub warunku w usłudze Azure Active Directory, co czyni katalogu podatna na luki w zabezpieczeniach i zagrożeń.

## <a name="see-also"></a>Zobacz także
* [Ochronę tożsamości usługi Azure Active Directory](active-directory-identityprotection.md)

