---
title: Informacje dotyczące wykrywania ryzyka Azure Active Directory Identity Protection | Microsoft Docs
description: Informacje dotyczące wykrywania ryzyka Azure Active Directory Identity Protection.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: reference
ms.date: 01/25/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: a1005ac1853568222dea83a0f12293945825cf6d
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70127574"
---
# <a name="azure-active-directory-identity-protection-risk-detections-reference"></a>Informacje dotyczące wykrywania ryzyka Azure Active Directory Identity Protection

Większość naruszeń zabezpieczeń odbywa się, gdy osoby atakujące uzyskują dostęp do środowiska przez kradzież tożsamości użytkownika. Odnajdywanie zagrożonych tożsamości nie jest łatwe. Azure Active Directory używa adaptacyjnych algorytmów uczenia maszynowego i heurystyki do wykrywania podejrzanych działań, które są związane z kontami użytkowników. Każda wykryta podejrzana akcja jest przechowywana w rekordzie o nazwie wykrywanie ryzyka.

## <a name="anonymous-ip-address"></a>Anonimowy adres IP

**Typ wykrywania:** W czasie rzeczywistym  
**Stara nazwa:** Logowania z anonimowego adresu IP

Ten typ wykrywania ryzyka oznacza logowanie z anonimowego adresu IP (na przykład przeglądarki tor, sieci VPN Anonymizer).
Te adresy IP są zwykle używane przez aktorów, którzy chcą ukryć swoje dane telemetryczne logowania (adres IP, lokalizacja, urządzenie itp.) dla potencjalnie złośliwego celu.

## <a name="atypical-travel"></a>Nietypowa podróż

**Typ wykrywania:** W trybie offline  
**Stara nazwa:** Niemożliwa podróż do nietypowych lokalizacji

Ten typ wykrywania ryzyka identyfikuje dwa logowania pochodzące z lokalizacji geograficznie odległych, gdzie co najmniej jedna z tych lokalizacji może być nietypowa dla użytkownika, pod kątem wcześniejszego zachowania. Ten algorytm uczenia maszynowego uwzględnia między innymi różne czynniki czas między dwoma logowaniami i czas, który miał zostać przesłany przez użytkownika z pierwszej lokalizacji do drugiego, co oznacza, że inny użytkownik korzysta z tego samego uwierzytelniające.

Algorytm ignoruje oczywiste "fałszywie dodatnie" przyczyniające się do niemożliwych warunków podróży, takich jak sieci VPN i lokalizacje regularnie używane przez innych użytkowników w organizacji. System ma początkowy okres uczenia z najwcześniej 14 dni lub 10 logowań, podczas którego uczy się o zachowanie logowania nowego użytkownika.

## <a name="leaked-credentials"></a>Nieujawnione poświadczenia

**Typ wykrywania:** W trybie offline  
**Stara nazwa:** Użytkownicy z ujawnionymi poświadczeniami

Ten typ wykrywania zagrożeń wskazuje, że wykryto przeciek prawidłowych poświadczeń użytkownika.
Gdy cybernetycznymi naruszają prawidłowe hasła dla uprawnionych użytkowników, przestępcy często udostępniają te poświadczenia. Jest to zazwyczaj realizowane przez zaksięgowanie ich publicznie w ciemnych witrynach sieci Web lub wklejanie lub przez handel lub sprzedawanie poświadczeń na czarnym rynku. Usługa Microsoft wycieka poświadczeń uzyskuje pary nazw użytkowników i haseł, monitorując publiczne i ciemne witryny sieci Web i wykonując następujące działania:

- Badacze
- Przepisy prawne
- Zespoły ds. zabezpieczeń w firmie Microsoft
- Inne zaufane źródła

Gdy usługa uzyskuje poświadczenia użytkownika z ciemnej sieci Web, wkleja witryny lub powyższe źródła, są one sprawdzane względem bieżących prawidłowych poświadczeń użytkowników usługi Azure AD w celu znalezienia prawidłowych dopasowań.

## <a name="malware-linked-ip-address"></a>Adres IP połączony ze złośliwym oprogramowaniem

**Typ wykrywania:** W trybie offline  
**Stara nazwa:** Logowania z zainfekowanych urządzeń

Ten typ wykrywania zagrożeń wskazuje logowania z adresów IP zainfekowanych złośliwym oprogramowaniem, które jest znane, aby aktywnie komunikować się z serwerem bot. Jest to określane przez skorelowanie adresów IP urządzenia użytkownika z adresami IP, które były w kontakcie z serwerem bot, gdy serwer bot był aktywny.

## <a name="unfamiliar-sign-in-properties"></a>Nieznane właściwości logowania

**Typ wykrywania:** W czasie rzeczywistym  
**Stara nazwa:** Logowania z nieznanych lokalizacji

Ten typ wykrywania ryzyka uwzględnia wcześniejszą historię logowania (IP, szerokości geograficznej i ASN), aby wyszukać nietypowe logowania. System przechowuje informacje o poprzednich lokalizacjach używanych przez użytkownika i uwzględnia te "znane" lokalizacje. Wykrywanie ryzyka jest wyzwalane, gdy logowanie następuje z lokalizacji, która nie znajduje się na liście znanych lokalizacji. Nowo utworzeni użytkownicy będą w trybie uczenia się przez okres, w którym nieznane wykrycia ryzyka związanego z logowaniem zostaną wyłączone w czasie, gdy algorytmy wiedzą o zachowaniu użytkownika. Czas trwania trybu uczenia jest dynamiczny i zależy od tego, ile czasu zajmuje algorytm zbierania wystarczającej ilości informacji o wzorcach logowania użytkownika. Minimalny czas trwania wynosi pięć dni. Użytkownik może wrócić do trybu uczenia po długim czasie braku aktywności. System ignoruje logowania ze znanych urządzeń i lokalizacje, które znajdują się geograficznie blisko znanej lokalizacji. 

Uruchamiamy również to wykrywanie uwierzytelniania podstawowego (lub starszych protokołów). Ponieważ te protokoły nie mają nowoczesnych właściwości, takich jak identyfikator klienta, jest ograniczona liczba danych telemetrycznych, aby zmniejszyć liczbę fałszywie dodatnich. Zalecamy, aby nasi klienci mogli przejść do nowoczesnego uwierzytelniania.

## <a name="azure-ad-threat-intelligence"></a>Analiza zagrożeń usługi Azure AD

**Typ wykrywania:** W trybie offline <br>
**Stara nazwa:** To wykrywanie będzie wyświetlane w starszych raportach Azure AD Identity Protection (Użytkownicy oflagowani w celu ryzyka, wykrywania ryzyka) jako "Użytkownicy z nieujawnionymi poświadczeniami"

Ten typ wykrywania zagrożeń wskazuje aktywność użytkownika nietypową dla danego użytkownika lub jest zgodna ze znanymi wzorcami ataków na podstawie wewnętrznych i zewnętrznych źródeł analizy zagrożeń firmy Microsoft.

## <a name="admin-confirmed-user-compromised"></a>Administrator potwierdził naruszenie zabezpieczeń użytkownika

**Typ wykrywania:** W trybie offline <br>
To wykrywanie wskazuje, że administrator zaznaczył "Potwierdzanie naruszenia przez użytkownika" w interfejsie użytkownika ryzykownych użytkowników lub przy użyciu interfejsu API riskyUsers. Aby sprawdzić, który administrator został naruszony, należy sprawdzić historię ryzyka użytkownika (za pośrednictwem interfejsu użytkownika lub interfejsu API).

## <a name="malicious-ip-address"></a>Złośliwy adres IP

**Typ wykrywania:** W trybie offline <br>
To wykrywanie wskazuje, że logowanie jest ze złośliwego adresu IP. Adres IP jest uznawany za złośliwy w oparciu o następujące kwestie:
-   Wskaźniki wysokiego błędu (z powodu nieprawidłowych poświadczeń odebranych z adresu IP)
-   Inne źródła reputacji adresów IP

## <a name="additional-risk-detected"></a>Wykryto dodatkowe ryzyko

**Typ wykrywania:** W czasie rzeczywistym lub w trybie offline <br>
To wykrywanie wskazuje, że wykryto jedno z powyższych wykryć w warstwie Premium. Ponieważ wykrycia warstwy Premium są widoczne tylko dla Azure AD — wersja Premium klientów P2, są zatytułowane "dodatkowe ryzyko wykryte" dla klientów innych niż P2.
