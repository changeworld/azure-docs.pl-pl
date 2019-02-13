---
title: Dokumentacja zdarzeń o podwyższonym ryzyku w usłudze Azure Active Directory Identity Protection | Dokumentacja firmy Microsoft
description: Dokumentacja zdarzeń o podwyższonym ryzyku Azure Active Directory Identity Protection.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: 14f7fc83-f4bb-41bf-b6f1-a9bb97717c34
ms.service: active-directory
ms.subservice: identity-protection
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/25/2018
ms.author: markvi
ms.reviewer: raluthra
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1509f40b88e3dc9c51bd00ed379c5b0130230a99
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/13/2019
ms.locfileid: "56178842"
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

Tego typu zdarzenia o podwyższonym ryzyku uwzględnia ostatnie logowania właściwości (na przykład urządzenia, lokalizacji sieciowej) do określenia logowania z nieznanych właściwości. System przechowuje właściwości powyższych lokalizacjach, używane przez użytkownika i traktuje te "znanych". Zdarzenia o podwyższonym ryzyku jest wyzwalany, gdy logowania wystąpi z właściwościami, które nie są już na liście znanych właściwości. System ma wstępny okres uczenia wynoszący 30 dni, w których nie Flaga rozwiązaniami do wykrywania wszelkich nowych.
Przeprowadzamy również wykrywanie dla uwierzytelniania podstawowego (lub starszych protokołów). Ponieważ te protokoły nie ma właściwości modern, takie jak identyfikator klienta, jest ograniczona telemetrię, aby redukować liczbę fałszywych alarmów. Zalecamy naszym klientom migrację do nowoczesnego uwierzytelniania.

