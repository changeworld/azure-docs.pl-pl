---
title: Rezydencja danych uwierzytelniania wieloskładnikowego usługi Azure
description: Dowiedz się, jakie dane osobiste i organizacyjne usługi Azure Multi-Factor Authentication przechowuje na temat Ciebie i Twoich użytkowników oraz jakie dane pozostają w kraju pochodzenia.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 12/16/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sasubram
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4c4556ece7faaacb8657a1012344f2263ee84214
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75480140"
---
# <a name="data-residency-and-customer-data-for-azure-multi-factor-authentication"></a>Dane dotyczące rezydencji i danych klientów w przypadku uwierzytelniania wieloskładnikowego platformy Azure

Dane klienta są przechowywane przez usługę Azure AD w lokalizacji geograficznej na podstawie adresu podanego przez organizację podczas subskrybowania usługi Microsoft Online, takiej jak Office 365 i Azure. Aby uzyskać informacje o tym, gdzie są przechowywane dane klienta, można użyć sekcji [Gdzie znajdują się dane?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located)

Oparte na chmurze uwierzytelnianie wieloskładnikowe platformy Azure i serwer uwierzytelniania wieloskładnikowego platformy Azure przetwarzają i przechowują pewną ilość danych osobowych i danych organizacji. W tym artykule opisano, co i gdzie dane są przechowywane.

Następujące działania uwierzytelniania wieloskładnikowego pochodzą obecnie z amerykańskich centrów danych, z wyjątkiem przypadków, gdy zaznaczono inaczej:

* Uwierzytelnianie dwuskładnikowe przy użyciu połączeń telefonicznych lub wiadomości SMS zazwyczaj pochodzi z amerykańskich centrów danych i jest kierowane przez dostawców globalnych.
    * Żądania uwierzytelniania użytkowników ogólnego przeznaczenia z innych regionów, takich jak Europa lub Australia, są obecnie przetwarzane przez centra danych w tym regionie. Inne zdarzenia, takie jak samoobsługowe resetowanie hasła, zdarzenia usługi Azure B2C lub scenariusze hybrydowe przy użyciu rozszerzenia serwera NPS lub karty usług AD FS, są obecnie przetwarzane przez amerykańskie centra danych.
* Powiadomienia wypychane przy użyciu aplikacji Microsoft Authenticator pochodzą z amerykańskich centrów danych. Ponadto usługi specyficzne dla dostawcy urządzeń mogą również wchodzić w grę z różnych regionów.
* Kody OATH są zazwyczaj obecnie weryfikowane w Stanach Zjednoczonych.
    * Ponownie zdarzenia uwierzytelniania użytkowników ogólnego przeznaczenia, które pochodzą z innych regionów, takich jak Europa lub Australia, są przetwarzane przez centra danych w tym regionie. Dodatkowe zdarzenia są obecnie przetwarzane przez amerykańskie centra danych.

## <a name="personal-data-stored-by-azure-multi-factor-authentication"></a>Dane osobowe przechowywane przez uwierzytelnianie wieloskładnikowe platformy Azure

Dane osobowe to informacje na poziomie użytkownika powiązane z konkretną osobą. Następujące dane zawierają dane osobowe:

* Zablokowani użytkownicy
* Ominięty użytkowników
* Żądania zmiany tokenu urządzenia microsoft authenticator
* Raporty aktywności uwierzytelniania wieloskładnikowego
* Aktywacje programu Microsoft Authenticator

Informacje te są przechowywane przez 90 dni.

Uwierzytelnianie wieloskładnikowe platformy Azure nie rejestruje danych osobowych, takich jak nazwa użytkownika, numer telefonu lub adres IP, ale istnieje *Identyfikator Użytkownika,* który identyfikuje próby uwierzytelniania wieloskładnikowego dla użytkowników. Dane dziennika są przechowywane przez 30 dni.

### <a name="azure-multi-factor-authentication"></a>Azure Multi-Factor Authentication

W przypadku chmur publicznych platformy Azure, z wyłączeniem uwierzytelniania B2C platformy Azure, rozszerzenia serwera NPS i karty usług AD FS systemu Windows Server 2016 lub 2019, są przechowywane następujące dane osobowe:

| Typ zdarzenia                           | Typ magazynu danych |
|--------------------------------------|-----------------|
| Token OATH                           | W dziennikach uwierzytelniania wieloskładnikowego     |
| Jednostronny SMS                          | W dziennikach uwierzytelniania wieloskładnikowego     |
| Połączenie głosowe                           | W dziennikach uwierzytelniania wieloskładnikowego<br />Magazyn danych raportu aktywności uwierzytelniania wieloskładnikowego<br />Zablokowani użytkownicy w przypadku zgłoszenia oszustwa |
| Powiadomienie o programie Microsoft Authenticator | W dziennikach uwierzytelniania wieloskładnikowego<br />Magazyn danych raportu aktywności uwierzytelniania wieloskładnikowego<br />Zablokowani użytkownicy w przypadku zgłoszenia oszustwa<br />Zmienianie żądań po zmianie tokenu urządzenia microsoft authenticator |

> [!NOTE]
> Magazyn danych raportu aktywności uwierzytelniania wieloskładnikowego jest przechowywany w Stanach Zjednoczonych dla wszystkich chmur, niezależnie od regionu, który przetwarza żądanie uwierzytelniania. Microsoft Azure Niemcy, Microsoft Azure obsługiwane przez 21Vianet i Microsoft Government Cloud mają własne niezależne magazyny danych oddzielnie od magazynów danych regionu chmury publicznej, jednak te dane są zawsze przechowywane w Stanach Zjednoczonych.

W przypadku platformy Microsoft Azure Dla Instytucji Rządowych, Platformy Microsoft Azure w Niemczech, platformy Microsoft Azure obsługiwanej przez 21Vianet, uwierzytelniania B2C platformy Azure, rozszerzenia serwera NPS oraz karty usług AD FS systemu Windows Server 2016 lub 2019 przechowywane są następujące dane osobowe:

| Typ zdarzenia                           | Typ magazynu danych |
|--------------------------------------|-----------------|
| Token OATH                           | W dziennikach uwierzytelniania wieloskładnikowego<br />Magazyn danych raportu aktywności uwierzytelniania wieloskładnikowego |
| Jednostronny SMS                          | W dziennikach uwierzytelniania wieloskładnikowego<br />Magazyn danych raportu aktywności uwierzytelniania wieloskładnikowego |
| Połączenie głosowe                           | W dziennikach uwierzytelniania wieloskładnikowego<br />Magazyn danych raportu aktywności uwierzytelniania wieloskładnikowego<br />Zablokowani użytkownicy w przypadku zgłoszenia oszustwa |
| Powiadomienie o programie Microsoft Authenticator | W dziennikach uwierzytelniania wieloskładnikowego<br />Magazyn danych raportu aktywności uwierzytelniania wieloskładnikowego<br />Zablokowani użytkownicy w przypadku zgłoszenia oszustwa<br />Zmienianie żądań po zmianie tokenu urządzenia microsoft authenticator |

### <a name="multi-factor-authentication-server"></a>Serwer Multi-Factor Authentication

Jeśli wdrożysz i uruchomisz serwer uwierzytelniania wieloskładnikowego platformy Azure, przechowywane są następujące dane osobowe:

> [!IMPORTANT]
> Od 1 lipca 2019 r. firma Microsoft nie będzie już oferować serwera uwierzytelniania wieloskładnikowego dla nowych wdrożeń. Nowi klienci, którzy chcieliby wymagać uwierzytelniania wieloskładnikowego od swoich użytkowników, powinni korzystać z uwierzytelniania wieloskładnikowego platformy Azure w chmurze. Obecni klienci, którzy aktywowali serwer uwierzytelniania wieloskładnikowego przed 1 lipca, będą mogli pobrać najnowszą wersję, przyszłe aktualizacje i wygenerować poświadczenia aktywacji w zwykły sposób.

| Typ zdarzenia                           | Typ magazynu danych |
|--------------------------------------|-----------------|
| Token OATH                           | W dziennikach uwierzytelniania wieloskładnikowego<br />Magazyn danych raportu aktywności uwierzytelniania wieloskładnikowego |
| Jednostronny SMS                          | W dziennikach uwierzytelniania wieloskładnikowego<br />Magazyn danych raportu aktywności uwierzytelniania wieloskładnikowego |
| Połączenie głosowe                           | W dziennikach uwierzytelniania wieloskładnikowego<br />Magazyn danych raportu aktywności uwierzytelniania wieloskładnikowego<br />Zablokowani użytkownicy w przypadku zgłoszenia oszustwa |
| Powiadomienie o programie Microsoft Authenticator | W dziennikach uwierzytelniania wieloskładnikowego<br />Magazyn danych raportu aktywności uwierzytelniania wieloskładnikowego<br />Zablokowani użytkownicy w przypadku zgłoszenia oszustwa<br />Zmienianie żądań po zmianie tokenu urządzenia microsoft authenticator |

## <a name="organizational-data-stored-by-azure-multi-factor-authentication"></a>Dane organizacyjne przechowywane przez uwierzytelnianie wieloskładnikowe platformy Azure

Dane organizacyjne to informacje na poziomie dzierżawy, które mogą udostępniać konfigurację lub konfigurację środowiska. Ustawienia dzierżawy na następujących stronach uwierzytelniania wieloskładnikowego portalu Azure mogą przechowywać dane organizacji, takie jak progi blokady lub informacje o identyfikatorze dzwoniącego dla przychodzących żądań uwierzytelniania telefonu:

* Blokada konta
* Alert dotyczący wykrycia oszustwa
* Powiadomienia
* Ustawienia połączenia telefonicznego

W przypadku serwera uwierzytelniania wieloskładnikowego platformy Azure następujące strony portalu Platformy Azure mogą zawierać dane organizacyjne:

* Ustawienia serwera
* Jednorazowa obwodnica
* Zasady buforowania
* Stan serwera uwierzytelniania wieloskładnikowego

## <a name="log-data-location"></a>Rejestrowanie lokalizacji danych

Gdzie informacje dziennika są przechowywane zależy od regionu, w którym są przetwarzane w. Większość regionów geograficznych ma natywne możliwości uwierzytelniania wieloskładnikowego platformy Azure, więc dane dziennika są przechowywane w tym samym regionie, który przetwarza żądanie uwierzytelniania wieloskładnikowego. W regionach geograficznych bez natywnej obsługi uwierzytelniania wieloskładnikowego platformy Azure są one obsługiwane przez lokalizacje geograficzne Stanów Zjednoczonych lub Europy, a dane dziennika są przechowywane w tym samym regionie, w który przetwarza żądanie uwierzytelniania wieloskładnikowego.

Niektóre podstawowe dane dziennika uwierzytelniania są przechowywane tylko w Stanach Zjednoczonych. Microsoft Azure Niemcy i Microsoft Azure obsługiwane przez 21Vianet są zawsze przechowywane w odpowiedniej chmurze. Dane dziennika usługi Microsoft Government Cloud są zawsze przechowywane w Stanach Zjednoczonych.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji o tym, jakie informacje o użytkowniku są zbierane przez oparte na chmurze uwierzytelnianie wieloskładnikowe azure i serwer uwierzytelniania wieloskładnikowego platformy Azure, zobacz [Zbieranie danych użytkowników uwierzytelniania wieloskładnikowego azure](howto-mfa-reporting-datacollection.md).
