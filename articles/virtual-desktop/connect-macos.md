---
title: Nawiązywanie połączenia z pulpitem wirtualnym systemu Windows z macOS — Azure
description: Jak nawiązać połączenie z pulpitem wirtualnym systemu Windows przy użyciu klienta macOS.
services: virtual-desktop
author: heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: helohr
ms.openlocfilehash: dc980d60c1db684a47c38b3b8efceb08dd618838
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/05/2019
ms.locfileid: "73605803"
---
# <a name="connect-with-the-macos-client"></a>Nawiązywanie połączenia z klientem systemu macOS

> Dotyczy: macOS 10,12 lub nowszego

>[!NOTE]
> Możliwość dostępu do zasobów pulpitu wirtualnego systemu Windows z klienta macOS jest obecnie dostępna w wersji zapoznawczej.

Możesz uzyskać dostęp do zasobów pulpitu wirtualnego systemu Windows z urządzeń macOS za pomocą klienta do pobrania. Ten przewodnik zawiera informacje na temat konfigurowania klienta programu.

## <a name="install-the-client"></a>Instalowanie klienta programu

Aby rozpocząć, [pobierz](https://apps.apple.com/app/microsoft-remote-desktop/id1295203466?mt=12) i zainstaluj klienta programu na urządzeniu macOS.

## <a name="subscribe-to-a-feed"></a>Subskrybowanie kanału informacyjnego

Zasubskrybuj kanał informacyjny Twojego administratora, aby uzyskać listę zasobów zarządzanych dostępnych dla Ciebie na urządzeniu macOS.

Aby subskrybować źródło danych:

1. Wybierz pozycję **Dodaj źródło danych** na stronie głównej, aby nawiązać połączenie z usługą i pobrać zasoby.
2. Wprowadź adres URL źródła danych. Może to być adres URL lub adres e-mail:
   - Jeśli używasz adresu URL, użyj tego administratora. Zwykle adres URL jest <https://rdweb.wvd.microsoft.com>.
   - Aby użyć poczty e-mail, wprowadź swój adres e-mail. Oznacza to, że klient może wyszukać adres URL skojarzony z Twoim adresem e-mail, jeśli administrator skonfigurował serwer w taki sposób.
3. Wybierz pozycję **Subskrybuj**.
4. Po wyświetleniu monitu zaloguj się przy użyciu konta użytkownika.

Po zalogowaniu powinna zostać wyświetlona lista dostępnych zasobów.

Po zasubskrybowaniu kanału informacyjnego zawartość kanału informacyjnego będzie regularnie aktualizowana automatycznie. Zasoby mogą zostać dodane, zmienione lub usunięte na podstawie zmian wprowadzonych przez administratora.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o kliencie macOS, zapoznaj się z dokumentacją dotyczącą rozpoczynania [pracy z klientem programu macOS](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-mac) .
