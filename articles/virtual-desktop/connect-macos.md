---
title: Nawiązywanie połączenia z pulpitem wirtualnym systemu Windows z macOS — Azure
description: Jak nawiązać połączenie z pulpitem wirtualnym systemu Windows z macOS.
services: virtual-desktop
author: heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: helohr
ms.openlocfilehash: bf6bfb52bd763eabd88f23701c3d1f240cdf4844
ms.sourcegitcommit: 0486aba120c284157dfebbdaf6e23e038c8a5a15
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/26/2019
ms.locfileid: "71310193"
---
# <a name="connect-from-macos"></a>Łączenie z macOS

> Dotyczy: macOS 10,12 lub nowszego

>[!NOTE]
> Możliwość dostępu do zasobów pulpitu wirtualnego systemu Windows z klienta macOS jest obecnie dostępna w wersji zapoznawczej.

Możesz uzyskać dostęp do zasobów pulpitu wirtualnego systemu Windows z urządzeń macOS za pomocą klienta do pobrania. Ten przewodnik zawiera informacje na temat konfigurowania klienta programu.

## <a name="install-the-client"></a>Instalowanie klienta programu

Aby rozpocząć, [Pobierz](https://apps.apple.com/app/microsoft-remote-desktop/id1295203466?mt=12) i zainstaluj klienta programu na urządzeniu macOS.

## <a name="subscribe-to-a-feed"></a>Subskrybowanie kanału informacyjnego

Zasubskrybuj kanał informacyjny Twojego administratora, aby uzyskać listę zasobów zarządzanych dostępnych dla Ciebie na urządzeniu macOS.

Aby subskrybować źródło danych:

1. Wybierz pozycję **Dodaj źródło danych** na stronie głównej, aby nawiązać połączenie z usługą i pobrać zasoby.
2. Wprowadź adres URL źródła danych. Może to być adres URL lub adres e-mail:
   - Jeśli używasz adresu URL, użyj tego administratora. Zwykle jest <https://rdweb.wvd.microsoft.com>to adres URL.
   - Aby użyć poczty e-mail, wprowadź swój adres e-mail. Oznacza to, że klient może wyszukać adres URL skojarzony z Twoim adresem e-mail, jeśli administrator skonfigurował serwer w taki sposób.
3. Wybierz pozycję **Subskrybuj**.
4. Po wyświetleniu monitu zaloguj się przy użyciu konta użytkownika.

Po zalogowaniu powinna zostać wyświetlona lista dostępnych zasobów.

Po zasubskrybowaniu kanału informacyjnego zawartość kanału informacyjnego będzie regularnie aktualizowana automatycznie. Zasoby mogą zostać dodane, zmienione lub usunięte na podstawie zmian wprowadzonych przez administratora.

## <a name="client-documentation"></a>Dokumentacja klienta

Aby dowiedzieć się więcej o kliencie macOS, zapoznaj się z dokumentacją dotyczącą rozpoczynania [pracy z klientem programu macOS](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-mac) .
