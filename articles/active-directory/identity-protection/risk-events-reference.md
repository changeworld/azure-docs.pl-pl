---
title: Dokumentacja zdarzeń o podwyższonym ryzyku w usłudze Azure Active Directory Identity Protection | Dokumentacja firmy Microsoft
description: Dokumentacja zdarzeń o podwyższonym ryzyku Azure Active Directory Identity Protection.
services: active-directory
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: mtillman
ms.assetid: 14f7fc83-f4bb-41bf-b6f1-a9bb97717c34
ms.service: active-directory
ms.subservice: identity-protection
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/25/2018
ms.author: joflore
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3973a35acb4cb95d3392a8daa59e7fd9a8c56eb1
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2019
ms.locfileid: "65191521"
---
# <a name="azure-active-directory-identity-protection-risk-events-reference"></a>Dokumentacja zdarzeń o podwyższonym ryzyku w usłudze Azure Active Directory Identity Protection

Większość naruszenia zabezpieczeń ma miejsce, gdy osoby atakujące uzyskują dostęp do środowiska, kradzież tożsamości użytkownika. Wykrywanie tożsamości ze złamanymi zabezpieczeniami jest nie łatwym zadaniem. Usługa Azure Active Directory korzysta z algorytmów uczenia maszynowego adaptacyjne i algorytmy heurystyczne wykryć podejrzane akcje, które są powiązane z kontami użytkowników. Każdy wykryty podejrzanych działań są przechowywane w zdarzeniu wywoływanej ryzyko rekordu.


## <a name="anonymous-ip-address"></a>Anonimowy adres IP

**Typ wykrywania:** Czas rzeczywisty  
**Stara nazwa:** Logowania z anonimowych adresów IP


Ten typ zdarzenia o podwyższonym ryzyku wskazuje logowania z anonimowego adresu IP (na przykład przeglądarka Tor, anonymizer sieci VPN).
Te adresy IP są zwykle używane przez uczestników, którzy chcą ukryć ich telemetria logowania (adres IP, lokalizacji, urządzeń itp.) dla potencjalnie złośliwego działania.


## <a name="atypical-travel"></a>Nietypowa podróż

**Typ wykrywania:** Offline  
**Stara nazwa:** Niemożliwa podróż do nietypowych lokalizacji


Ten typ zdarzenia o podwyższonym ryzyku identyfikuje pochodzące z komputera odległym geograficznie lokalizacji, w którym co najmniej jednej z lokalizacji może być również nietypowa dla użytkownika, biorąc pod uwagę ostatnie działanie dwukrotnym zalogowaniu. Między kilka innych czynników tego algorytmu uczenia maszynowego uwzględnia czas między dwa logowania i czas zajęłoby dla użytkownika na komunikację z pierwszej lokalizacji do drugiego, wskazujący, że inny użytkownik korzysta z takie same poświadczenia.

Algorytm ignoruje oczywiste "wyniki fałszywie dodatnie" Współtworzenie warunki niemożliwych podróży, takich jak sieci VPN i lokalizacje, które regularnie używane przez innych użytkowników w organizacji. System ma wstępny okres uczenia wynoszący najwcześniejsza z 14 dni lub 10 nazw logowania, podczas którego dowie się, zachowanie logowania nowego użytkownika.


## <a name="leaked-credentials"></a>Ujawnione poświadczenia

**Typ wykrywania:** Offline  
**Stara nazwa:** Użytkownicy z ujawnionymi poświadczeniami


Ten typ zdarzenia o podwyższonym ryzyku wskazuje, że prawidłowe poświadczenia użytkownika zostały ujawnione.
Przestępców przestępcami cybernetycznymi naruszenia zabezpieczeń prawidłowe haseł uprawnionych użytkowników, często współużytkować tych poświadczeń. Zazwyczaj jest to wykonywane, publikując je publicznie na ciemny witryn sieci web lub wklej lub handlem lub sprzedaży poświadczeń na czarnym rynku. Microsoft wyciek poświadczeń usługi uzyskuje nazwę użytkownika / hasło pary przez monitorowanie publicznych i ciemny witryn sieci web i Praca z:

- Badacze

- Organom ścigania

- Zespoły zabezpieczeń w firmie Microsoft

- Innych zaufanych źródeł

Gdy usługa uzyskuje poświadczenia użytkownika z Internet, Wklej witryn lub powyżej źródeł, są porównywane użytkowników usługi Azure AD bieżącego prawidłowe poświadczenia, aby znaleźć dopasowania prawidłowe.


## <a name="malware-linked-ip-address"></a>Adres IP połączony ze złośliwym oprogramowaniem

**Typ wykrywania:** Offline  
**Stara nazwa:** Logowania z zainfekowanych urządzeń


Ten typ zdarzenia o podwyższonym ryzyku wskazuje logowania z adresów IP zainfekowany złośliwym oprogramowaniem, znanego aktywnie komunikować się z serwerem botów. Jest to określane przez skorelowanie adresów IP na urządzeniu użytkownika względem adresów IP, które zostały kontaktu z usługą serwera bot, podczas gdy serwer bot był aktywny.


## <a name="unfamiliar-sign-in-properties"></a>Nieznane właściwości logowania

**Typ wykrywania:** Czas rzeczywisty  
**Stara nazwa:** Logowania z nieznanych lokalizacji


Tego typu zdarzenia o podwyższonym ryzyku uwzględnia ostatnie historię logowania (IP, szerokości / długości geograficznej i jego numer ASN) do wyszukania nietypowe logowania. System przechowuje informacje o powyższych lokalizacjach, używane przez użytkownika i uwzględnia te lokalizacje "znanych". Zdarzenia o podwyższonym ryzyku jest wyzwalany, gdy logowania wystąpi z lokalizacji, która nie jest już na liście zaznajomiony lokalizacji. Nowo utworzony użytkownicy będą w "trybie uczenia" w okresie czasu w nieznanym logowania właściwości, które zdarzenia o podwyższonym ryzyku są wyłączone podczas nasze algorytmy Dowiedz się, zachowanie użytkowników. Szkoleniowe tryb czasu trwania jest dynamiczny, a także od tego, na ile czasu zajmuje algorytmu do zebrania wystarczającej ilości informacji na temat wzorców logowania użytkownika. Minimalny czas trwania wynosi pięć dni. Użytkownika można wrócić do trybu nauki po dłuższym okresie nieaktywności. System ignoruje także logowania z dobrze znanych urządzeń i lokalizacji, które geograficznie znajdują się blisko znanej lokalizacji. 

Przeprowadzamy również wykrywanie dla uwierzytelniania podstawowego (lub starszych protokołów). Ponieważ te protokoły nie ma właściwości modern, takie jak identyfikator klienta, jest ograniczona telemetrię, aby redukować liczbę fałszywych alarmów. Zalecamy naszym klientom migrację do nowoczesnego uwierzytelniania.


## <a name="azure-ad-threat-intelligence"></a>Analiza zagrożeń w usłudze Azure AD

**Typ wykrywania:** Offline <br>
**Stara nazwa:** Wykrywanie pojawią się w starszych raportach usługi Azure AD Identity Protection (użytkownicy oflagowani w związku z ryzykiem, zdarzeń o podwyższonym ryzyku) jako "Użytkownicy z ujawnionymi poświadczeniami"

Ten typ zdarzenia o podwyższonym ryzyku wskazuje aktywności użytkownika, który jest nietypowy dla danego użytkownika lub jest zgodne ze znanymi wzorcami ataków opartych na źródłach analizy zagrożeń wewnętrznych i zewnętrznych firmy Microsoft.
