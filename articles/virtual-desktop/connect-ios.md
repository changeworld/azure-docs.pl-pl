---
title: Nawiązywanie połączenia z pulpitem wirtualnym systemu Windows w systemie iOS — Azure
description: Jak nawiązać połączenie z pulpitem wirtualnym systemu Windows z systemu iOS.
services: virtual-desktop
author: heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: helohr
ms.openlocfilehash: 3e77a77dfa2de003722419f861e957d4cb011b71
ms.sourcegitcommit: 0486aba120c284157dfebbdaf6e23e038c8a5a15
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/26/2019
ms.locfileid: "71310323"
---
# <a name="connect-from-ios"></a>Łączenie z systemu iOS

> Dotyczy: iOS 8,0 lub nowszy. Zgodne z iPhone, iPad i iPod touch.

>[!NOTE]
> Klient systemu iOS jest obecnie w wersji zapoznawczej.

Możesz uzyskać dostęp do zasobów pulpitu wirtualnego systemu Windows z urządzenia z systemem iOS za pomocą klienta do pobrania. W tym przewodniku przedstawiono sposób konfigurowania klienta systemu iOS.

## <a name="install-the-ios-beta-client"></a>Instalowanie klienta systemu iOS beta
Aby zainstalować klienta systemu iOS beta:

1. Zainstaluj aplikację [Apple usługi TestFlight](https://apps.apple.com/us/app/testflight/id899247664) na urządzeniu z systemem iOS.
2. Na urządzeniu z systemem iOS otwórz przeglądarkę i przejdź do [aka.MS/rdiosbeta](https://aka.ms/rdiosbeta).
3. Pod etykietą **krok 2: Dołącz do wersji**beta, wybierz pozycję **Rozpocznij testowanie**.
4. Po przekierowaniu do aplikacji usługi TestFlight wybierz pozycję **Akceptuj**, a następnie wybierz pozycję **Zainstaluj**.

## <a name="subscribe-to-a-feed"></a>Subskrybowanie kanału informacyjnego

Zasubskrybuj źródło danych dostarczone przez administratora, aby uzyskać listę zasobów zarządzanych, do których możesz uzyskać dostęp na urządzeniu z systemem iOS.

Aby subskrybować źródło danych:

1. W centrum połączenia naciśnij **+** pozycję, a następnie naciśnij pozycję **Dodaj obszar roboczy**.
2. Wprowadź adres URL źródła danych w polu **adres URL źródła danych** . Adres URL źródła danych może być adresem URL lub adresem e-mail.
   - Jeśli używasz adresu URL, użyj tego administratora. Zwykle jest <https://rdweb.wvd.microsoft.com>to adres URL.
   - Aby użyć poczty e-mail, wprowadź swój adres e-mail. Oznacza to, że klient może wyszukać adres URL skojarzony z Twoim adresem e-mail, jeśli administrator skonfigurował serwer w taki sposób.
3. Naciśnij przycisk **dalej**.
4. Po wyświetleniu monitu podaj swoje poświadczenia.
   - W polu **Nazwa użytkownika**Nadaj nazwę użytkownikowi uprawnienia dostępu do zasobów.
   - W polu **hasło**Podaj hasło skojarzone z nazwą użytkownika.
   - Może być również wyświetlony monit o podanie dodatkowych czynników, jeśli administrator skonfigurował uwierzytelnianie w taki sposób.
5. Naciśnij pozycję **Zapisz**.

Po wykonaniu tej czynności centrum połączenia powinno wyświetlić zasoby zdalne.

Po zasubskrybowaniu kanału informacyjnego zawartość kanału informacyjnego będzie regularnie aktualizowana automatycznie. Zasoby mogą zostać dodane, zmienione lub usunięte na podstawie zmian wprowadzonych przez administratora.

## <a name="client-documentation"></a>Dokumentacja klienta

Aby dowiedzieć się więcej na temat korzystania z klienta systemu iOS beta, zapoznaj się z dokumentacją dotyczącą rozpoczynania [pracy z klientem systemu iOS](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-ios) .
