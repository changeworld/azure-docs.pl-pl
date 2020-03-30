---
title: Co to jest ryzyko? Usługa Azure AD Identity Protection
description: Objaśnienie ryzyka w usłudze Azure AD Identity Protection
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 10/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 775ff6b3ba003bed22ccd5a42cb4da005c4dbb69
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79253691"
---
# <a name="what-is-risk"></a>Co to jest ryzyko?

Wykrywanie ryzyka w usłudze Azure AD Identity Protection obejmują wszelkie zidentyfikowane podejrzane akcje związane z kontami użytkowników w katalogu.

Usługa Identity Protection zapewnia organizacjom dostęp do zaawansowanych zasobów, aby szybko wyświetlać te podejrzane akcje i reagować na nie. 

![Omówienie zabezpieczeń przedstawiające ryzykownych użytkowników i logowania](./media/concept-identity-protection-risks/identity-protection-security-overview.png)

## <a name="risk-types-and-detection"></a>Rodzaje ryzyka i wykrywanie

Istnieją dwa rodzaje ryzyka **Użytkownik** i **zaloguj się** oraz dwa rodzaje wykrywania lub obliczania w czasie **rzeczywistym** i **w trybie offline.**

### <a name="user-risk"></a>Ryzyko użytkownika

Ryzyko użytkownika reprezentuje prawdopodobieństwo, że dana tożsamość lub konto zostanie naruszone. 

Zagrożenia te są obliczane w trybie offline przy użyciu wewnętrznych i zewnętrznych źródeł analizy zagrożeń firmy Microsoft, w tym badaczy zabezpieczeń, specjalistów od egzekwowania prawa, zespołów zabezpieczeń firmy Microsoft i innych zaufanych źródeł.

| Wykrywanie ryzyka | Opis |
| --- | --- |
| Wyciekające poświadczenia | Ten typ wykrywania ryzyka wskazuje, że wyciekły prawidłowe poświadczenia użytkownika. Gdy cyberprzestępcy naruszyli prawidłowe hasła legalnych użytkowników, często udostępniają te poświadczenia. Ta udostępnianie odbywa się zazwyczaj poprzez publiczne publikowanie w ciemnej sieci, wklejania witryn lub poprzez handel i sprzedawanie poświadczeń na czarnym rynku. Gdy usługa poświadczeń firmy Microsoft wyciekła uzyskuje poświadczenia użytkownika z ciemnej sieci Web, wklej witryny lub innych źródeł, są one sprawdzane względem bieżących prawidłowych poświadczeń użytkowników usługi Azure AD w celu znalezienia prawidłowych dopasowań. |
| Analiza zagrożeń usługi Azure AD | Ten typ wykrywania ryzyka wskazuje aktywność użytkownika, która jest nietypowa dla danego użytkownika lub jest zgodna ze znanymi wzorcami ataków opartymi na wewnętrznych i zewnętrznych źródłach analizy zagrożeń firmy Microsoft. |

### <a name="sign-in-risk"></a>Ryzyko logowania

Ryzyko logowania reprezentuje prawdopodobieństwo, że dane żądanie uwierzytelnienia nie jest autoryzowane przez właściciela tożsamości. 

Ryzyka te można obliczyć w czasie rzeczywistym lub obliczyć w trybie offline przy użyciu wewnętrznych i zewnętrznych źródeł analizy zagrożeń firmy Microsoft, w tym badaczy zabezpieczeń, specjalistów od egzekwowania prawa, zespołów zabezpieczeń firmy Microsoft i innych zaufanych źródeł.

| Wykrywanie ryzyka | Typ wykrywania | Opis |
| --- | --- | --- |
| Anonimowy adres IP | Przesyłanie w czasie rzeczywistym | Ten typ wykrywania ryzyka wskazuje logowania z anonimowego adresu IP (na przykład przeglądarki Tora lub anonimowej sieci VPN). Te adresy IP są zazwyczaj używane przez podmioty, które chcą ukryć swoje dane telemetryczne logowania (adres IP, lokalizacja, urządzenie itp.) dla potencjalnie złośliwych intencji. |
| Nietypowe podróże | W trybie offline | Ten typ wykrywania ryzyka identyfikuje dwa logowania pochodzące z geograficznie odległych lokalizacji, gdzie co najmniej jedna z lokalizacji może być nietypowa dla użytkownika, biorąc pod uwagę zachowanie z przeszłości. Wśród kilku innych czynników ten algorytm uczenia maszynowego uwzględnia czas między dwoma logowaniami i czas potrzebny użytkownikowi na podróż z pierwszej lokalizacji do drugiej, wskazując, że inny użytkownik używa tego samego Poświadczenia. <br><br> Algorytm ignoruje oczywiste "fałszywe alarmy" przyczyniające się do niemożliwych warunków podróży, takich jak VPN i lokalizacje regularnie używane przez innych użytkowników w organizacji. System ma początkowy okres uczenia się najwcześniej 14 dni lub 10 logowania, podczas którego uczy się zachowania logowania nowego użytkownika. |
| Połączony adres IP ze złośliwym oprogramowaniem | W trybie offline | Ten typ wykrywania ryzyka wskazuje logowania z adresów IP zainfekowanych złośliwym oprogramowaniem, o których wiadomo, że aktywnie komunikują się z serwerem botów. Wykrywanie to jest określane przez korelację adresów IP urządzenia użytkownika z adresami IP, które miały kontakt z serwerem botów, gdy serwer bot był aktywny. |
| Nieznane właściwości logowania | Przesyłanie w czasie rzeczywistym | Ten typ wykrywania ryzyka uwzględnia historię logowania w przeszłości (IP, Szerokość geograficzna / długość geograficzna i ASN) w celu wyszukywania nietypowych logów. System przechowuje informacje o poprzednich lokalizacjach używanych przez użytkownika i uwzględnia te "znane" lokalizacje. Wykrywanie ryzyka jest wyzwalane, gdy logowanie występuje z lokalizacji, która nie znajduje się jeszcze na liście znanych lokalizacji. Nowo utworzone użytkownicy będą w "trybie uczenia się" przez okres czasu, w którym nieznane właściwości logowania wykrywania ryzyka zostaną wyłączone, podczas gdy nasze algorytmy uczą się zachowania użytkownika. Czas trwania trybu uczenia się jest dynamiczny i zależy od tego, ile czasu zajmuje algorytmowi zebranie wystarczającej ilości informacji o wzorcach logowania użytkownika. Minimalny czas trwania wynosi pięć dni. Użytkownik może wrócić do trybu uczenia się po długim okresie bezczynności. System ignoruje również logowania ze znanych urządzeń i lokalizacji, które są geograficznie blisko znanej lokalizacji. <br><br> Uruchamiamy również to wykrywanie dla uwierzytelniania podstawowego (lub starszych protokołów). Ponieważ te protokoły nie mają nowoczesnych właściwości, takich jak identyfikator klienta, istnieje ograniczona telemetria w celu zmniejszenia liczby fałszywych alarmów. Zalecamy naszym klientom przejście do nowoczesnego uwierzytelniania. |
| Administrator potwierdził, że użytkownik został naruszony | W trybie offline | To wykrywanie wskazuje, że administrator wybrał "Potwierdź, że użytkownik został naruszony" w interfejsie użytkownika risky lub za pomocą interfejsu API riskyUsers. Aby zobaczyć, który administrator potwierdził, że ten użytkownik został naruszony, sprawdź historię ryzyka użytkownika (za pośrednictwem interfejsu użytkownika lub interfejsu API). |
| Złośliwy adres IP | W trybie offline | To wykrywanie wskazuje logowanie ze złośliwego adresu IP. Adres IP jest uważany za złośliwy na podstawie wysokich wskaźników niepowodzeń z powodu nieprawidłowych poświadczeń otrzymanych z adresu IP lub innych źródeł reputacji ip. |
| Podejrzane reguły manipulowania skrzynką odbiorczą | W trybie offline | To wykrywanie jest wykrywane przez [program Microsoft Cloud App Security (MCAS).](/cloud-app-security/anomaly-detection-policy#suspicious-inbox-manipulation-rules) To wykrywanie profiluje środowisko i wyzwala alerty, gdy podejrzane reguły, które usuwają lub przenoszą wiadomości lub foldery, są ustawione w skrzynce odbiorczej użytkownika. Może to oznaczać, że konto użytkownika zostało naruszone, że wiadomości są celowo ukrywane i że skrzynka pocztowa jest używana do dystrybucji spamu lub złośliwego oprogramowania w organizacji. |
| Niemożliwa podróż | W trybie offline | To wykrywanie jest wykrywane przez [program Microsoft Cloud App Security (MCAS).](/cloud-app-security/anomaly-detection-policy#impossible-travel) Wykrywanie to identyfikuje dwie aktywności użytkownika (to pojedyncza lub wiele sesji) pochodzące z geograficznie odległych lokalizacji w okresie krótszym niż czas, w którym użytkownik musiałby podróżować z pierwszej lokalizacji do drugiej, co wskazuje, że że inny użytkownik używa tych samych poświadczeń. |

### <a name="other-risk-detections"></a>Inne wykrywanie ryzyka

| Wykrywanie ryzyka | Typ wykrywania | Opis |
| --- | --- | --- |
| Wykryto dodatkowe ryzyko | W czasie rzeczywistym lub w trybie offline | To wykrywanie wskazuje, że wykryto jedno z powyższych wykrywania premium. Ponieważ wykrywanie w usłudze Premium są widoczne tylko dla klientów usługi Azure AD Premium P2, są one zatytułowane "wykryte dodatkowe ryzyko" dla klientów bez licencji Usługi Azure AD Premium P2. |

## <a name="next-steps"></a>Następne kroki

- [Dostępne zasady ograniczania ryzyka](concept-identity-protection-policies.md)

- [Omówienie zabezpieczeń](concept-identity-protection-security-overview.md)
