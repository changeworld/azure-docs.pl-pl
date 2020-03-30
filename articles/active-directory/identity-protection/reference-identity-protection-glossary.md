---
title: Słownik ochrony tożsamości usługi Azure Active Directory
description: Słownik ochrony tożsamości usługi Azure Active Directory
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: reference
ms.date: 10/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9a3e2df956aaa4f9fd0af83dd2a18e04d731c714
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74232342"
---
# <a name="azure-active-directory-identity-protection-glossary"></a>Słownik ochrony tożsamości usługi Azure Active Directory

### <a name="at-risk-user"></a>Zagrożone (użytkownik)
Użytkownik z co najmniej jednym aktywnym wykrywaniem ryzyka. 

### <a name="atypical-sign-in-location"></a>Nietypowe miejsce logowania
Logowanie z lokalizacji geograficznej, która nie jest typowa dla określonego użytkownika, podobnych użytkowników lub dzierżawy.

### <a name="azure-ad-identity-protection"></a>Usługa Azure AD Identity Protection
Moduł zabezpieczeń usługi Azure Active Directory, który zapewnia skonsolidowany widok wykrywania ryzyka i potencjalnych luk w zabezpieczeniach mających wpływ na tożsamość organizacji.

### <a name="conditional-access"></a>Dostęp warunkowy
Zasady zabezpieczania dostępu do zasobów. Reguły dostępu warunkowego są przechowywane w usłudze Azure Active Directory i są oceniane przez usługę Azure AD przed udzieleniem dostępu do zasobu.  Przykładowe reguły obejmują ograniczanie dostępu na podstawie lokalizacji użytkownika, kondycji urządzenia lub metody uwierzytelniania użytkownika.

### <a name="credentials"></a>Poświadczenia
Informacje, które obejmują identyfikację i dowód tożsamości, który jest używany do uzyskania dostępu do zasobów lokalnych i sieciowych. Przykładami poświadczeń są nazwy użytkowników i hasła, karty inteligentne i certyfikaty.

### <a name="event"></a>Wydarzenie
Rekord działania w usłudze Azure Active Directory.

### <a name="false-positive-risk-detection"></a>Fałszywie dodatni (wykrywanie ryzyka)
Stan wykrywania ryzyka ustawiony ręcznie przez użytkownika ochrony tożsamości, wskazujący, że wykrywanie ryzyka zostało zbadane i zostało niepoprawnie oznaczone jako wykrywanie ryzyka.

### <a name="identity"></a>Tożsamość
Osoba lub jednostka, które muszą być zweryfikowane za pomocą uwierzytelniania, na podstawie kryteriów, takich jak hasło lub certyfikat.

### <a name="identity-risk-detection"></a>Wykrywanie ryzyka tożsamości
Zdarzenie usługi Azure AD, które zostało oflagowane jako nietypowe przez ochronę tożsamości i może wskazywać, że tożsamość została naruszona.

### <a name="ignored-risk-detection"></a>Ignorowane (wykrywanie ryzyka)
Stan wykrywania ryzyka ustawiony ręcznie przez użytkownika ochrony tożsamości, wskazując, że wykrywanie ryzyka jest zamknięte bez podejmowania działań korygujących.

### <a name="impossible-travel-from-atypical-locations"></a>Niemożliwe podróże z nietypowych miejsc
Wykrywanie ryzyka wyzwalane po wykryciu dwóch logów dla tego samego użytkownika, gdzie co najmniej jeden z nich pochodzi z nietypowej lokalizacji logowania i gdy czas między logowaniami jest krótszy niż minimalny czas fizycznego podróżowania między nimi Lokalizacje.  

### <a name="investigation"></a>Badanie
Proces przeglądania działań, dzienników i innych istotnych informacji związanych z wykrywaniem ryzyka w celu podjęcia decyzji, czy konieczne są kroki naprawcze lub ograniczające działania, zrozumienia, czy i w jaki sposób tożsamość została naruszona, oraz zrozumienia, w jaki sposób naruszono zagrożenie. tożsamości.

### <a name="leaked-credentials"></a>Wyciekające poświadczenia
Wykrywanie ryzyka wyzwalane, gdy bieżące poświadczenia użytkownika (nazwa użytkownika i hasło) są publikowane publicznie w ciemnej sieci przez naszych badaczy.

### <a name="mitigation"></a>Środki zaradcze
Akcja mająca na celu ograniczenie lub wyeliminowanie możliwości wykorzystania tożsamości lub urządzenia, której bezpieczeństwo zostało naruszone, bez przywracania tożsamości lub urządzenia do stanu bezpiecznego. Ograniczenie nie rozwiązuje poprzednich wykrywania ryzyka skojarzonych z tożsamością lub urządzeniem.

### <a name="multi-factor-authentication"></a>Uwierzytelnianie wieloskładnikowe
Metoda uwierzytelniania, która wymaga dwóch lub więcej metod uwierzytelniania, które mogą zawierać coś, co użytkownik ma, taki certyfikat; coś, co użytkownik wie, takie jak nazwy użytkowników, hasła lub hasła; atrybuty fizyczne, takie jak odcisk palca; i osobistych atrybutów, takich jak podpis osobisty.

### <a name="offline-detection"></a>Wykrywanie w trybie offline
Wykrywanie anomalii i ocena ryzyka zdarzenia, takie jak próba logowania po fakcie, dla zdarzenia, które już się stało.

### <a name="policy-condition"></a>Warunek zasad
Część zasad zabezpieczeń, która definiuje jednostki (grupy, użytkowników, aplikacje, platformy urządzeń, stany urządzenia, zakresy adresów IP, typy klientów) zawarte w zasadach lub wykluczone z niej.

### <a name="policy-rule"></a>Reguła zasad
Część zasad zabezpieczeń, która opisuje okoliczności, które mogłyby wywołać zasady i akcje podjęte po uruchomieniu zasad.

### <a name="prevention"></a>Zapobieganie
Działanie mające na celu zapobieganie uszkodzeniom organizacji poprzez nadużywanie tożsamości lub urządzenia, co do których podejrzewa się lub wie, że zostały naruszone. Akcja zapobiegawczego nie zabezpiecza urządzenia ani tożsamości i nie rozwiązuje wcześniejszych wykrywania ryzyka.

### <a name="privileged-user"></a>Uprzywilejowany (użytkownik)
Użytkownik, który w momencie wykrywania ryzyka miał stałe lub tymczasowe uprawnienia administratora do co najmniej jednego zasobu w usłudze Azure Active Directory, takiego jak administrator globalny, administrator rozliczeń, administrator usługi, administrator użytkownika i hasło Administratora. 

### <a name="real-time"></a>Przesyłanie w czasie rzeczywistym
Zobacz Wykrywanie w czasie rzeczywistym.

### <a name="real-time-detection"></a>Wykrywanie w czasie rzeczywistym
Wykrywanie anomalii i ocena ryzyka zdarzenia, takie jak próba logowania przed zdarzeniem jest dozwolone kontynuowanie.

### <a name="remediated-risk-detection"></a>Skorygowane (wykrywanie ryzyka)
Stan wykrywania ryzyka jest automatycznie ustawiany przez ochronę tożsamości, co oznacza, że wykrywanie ryzyka zostało naprawione przy użyciu standardowej akcji korygującej dla tego typu wykrywania ryzyka. Na przykład po zresetowaniu hasła użytkownika wiele wykrywania ryzyka, które wskazują, że poprzednie hasło zostało naruszone, zostało automatycznie skorygowane.

### <a name="remediation"></a>Korekty
Akcja mająca na celu zabezpieczenie tożsamości lub urządzenia, które wcześniej podejrzewano lub wiadomo, że zostały naruszone. Akcja korygowania przywraca tożsamość lub urządzenie do stanu bezpiecznego i rozwiązuje poprzednie wykrywanie ryzyka skojarzone z tożsamością lub urządzeniem.

### <a name="resolved-risk-detection"></a>Rozwiązany (wykrywanie ryzyka)
Stan wykrywania ryzyka ustawiony ręcznie przez użytkownika ochrony tożsamości, wskazując, że użytkownik podjął odpowiednie działania korygujące poza ochroną tożsamości i że wykrywanie ryzyka należy uznać za zamknięte.

### <a name="risk-detection-status"></a>Stan wykrywania ryzyka
Właściwość wykrywania ryzyka, wskazując, czy zdarzenie jest aktywne, a jeśli zamknięte, powodem jego zamknięcia.

### <a name="risk-detection-type"></a>Typ wykrywania ryzyka
Kategoria wykrywania ryzyka, wskazująca typ anomalii, która spowodowała zdarzenie, które należy uznać za ryzykowne.

### <a name="risk-level-risk-detection"></a>Poziom ryzyka (wykrywanie ryzyka)
Wskazanie (Wysoki, Średni lub Niski) ważności wykrywania ryzyka, aby pomóc użytkownikom ochrony tożsamości priorytety akcji, które podejmują w celu zmniejszenia ryzyka dla ich organizacji. 

### <a name="risk-level-sign-in"></a>Poziom ryzyka (logowanie)
Wskazanie (Wysoki, Średni lub Niski) prawdopodobieństwa, że w przypadku określonego logowania ktoś inny próbuje użyć tożsamości użytkownika.

### <a name="risk-level-user-compromise"></a>Poziom ryzyka (naruszenie zabezpieczeń użytkownika)
Wskazanie (Wysoki, Średni lub Niski) prawdopodobieństwa naruszenia tożsamości.

### <a name="risk-level-vulnerability"></a>Poziom ryzyka (luka w zabezpieczeniach)
Wskazanie (wysokiego, średniego lub niskiego) stopnia ważności usterki, aby pomóc użytkownikom ochrony tożsamości ustalić priorytety akcji, które podejmują w celu zmniejszenia ryzyka dla ich organizacji.

### <a name="secure-identity"></a>Bezpieczeństwo (tożsamość)
Podejmij działania korygujące, takie jak zmiana hasła lub ponowne zagosnowanie komputera, aby przywrócić potencjalnie zagrożoną tożsamość do stanu bez zabezpieczeń.

### <a name="security-policy"></a>Zasady zabezpieczeń
Kolekcja reguł i warunków zasad. Zasady mogą być stosowane do jednostek, takich jak użytkownicy, grupy, aplikacje, urządzenia, platformy urządzeń, stany urządzeń, zakresy adresów IP i typy klientów Auth2.0. Gdy zasada jest włączona, jest oceniana za każdym razem, gdy jednostka uwzględniona w zasadach jest wystawiana token dla zasobu.

### <a name="sign-in-v"></a>Zaloguj się (v)
Aby uwierzytelnić się do tożsamości w usłudze Azure Active Directory.

### <a name="sign-in-n"></a>Logowanie (n)
Proces lub akcja uwierzytelniania tożsamości w usłudze Azure Active Directory i zdarzenie, które przechwytuje tę operację.

### <a name="sign-in-from-anonymous-ip-address"></a>Logowanie się z anonimowego adresu IP
Wykrywanie ryzyka wyzwalane po pomyślnym zalogowaniu się z adresu IP, który został zidentyfikowany jako anonimowy adres IP serwera proxy.

### <a name="sign-in-from-infected-device"></a>Logowanie się z zainfekowanego urządzenia
Wykrywanie ryzyka wyzwalane, gdy logowanie pochodzi z adresu IP, który jest znany jako używany przez co najmniej jedno lub więcej urządzeń, które aktywnie próbują komunikować się z serwerem botów.

### <a name="sign-in-from-ip-address-with-suspicious-activity"></a>Logowanie się z adresu IP za pomocą podejrzanej aktywności
Wykrywanie ryzyka wyzwalane po pomyślnym zalogowaniu się z adresu IP z dużą liczbą nieudanych prób logowania na wielu kontach użytkowników w krótkim okresie czasu.

### <a name="sign-in-from-unfamiliar-location"></a>Zaloguj się z nieznanej lokalizacji
Wykrywanie ryzyka wyzwalane, gdy użytkownik pomyślnie loguje się z nowej lokalizacji (IP, Szerokość/długość geograficzna i ASN).

### <a name="sign-in-risk"></a>Ryzyko logowania
Zobacz Poziom ryzyka (logowanie)

### <a name="sign-in-risk-policy"></a>Zasady dotyczące ryzyka logowania
Zasady dostępu warunkowego, który ocenia ryzyko do określonego logowania i stosuje środki zaradcze na podstawie wstępnie zdefiniowanych warunków i reguł.

### <a name="user-compromise-risk"></a>Ryzyko naruszenia bezpieczeństwa przez użytkownika
Zobacz Poziom ryzyka (naruszenie zabezpieczeń użytkownika)

### <a name="user-risk"></a>Ryzyko użytkownika
Zobacz Poziom ryzyka (naruszenie zabezpieczeń użytkownika).

### <a name="user-risk-policy"></a>Zasady dotyczące ryzyka użytkownika
Zasady dostępu warunkowego, który uwzględnia logowania i stosuje środki zaradcze na podstawie wstępnie zdefiniowanych warunków i reguł.

### <a name="users-flagged-for-risk"></a>Użytkownicy oflagowani w związku z ryzykiem
Użytkownicy, którzy mają wykrywanie ryzyka, które są aktywne lub korygowane

### <a name="vulnerability"></a>Problemy
Konfiguracja lub warunek w usłudze Azure Active Directory, co sprawia, że katalog jest podatny na exploity lub zagrożenia.

## <a name="see-also"></a>Zobacz też

- [Ochrona tożsamości w usłudze Azure Active Directory](../active-directory-identityprotection.md)
