---
title: Łączenie się z pulpitem wirtualnym systemu Windows z systemu macOS — Azure
description: Jak połączyć się z pulpitem wirtualnym systemu Windows za pomocą klienta systemu macOS.
services: virtual-desktop
author: heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 12/13/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 673c20355e9ad4abf2c8318b3eb2ebbcc36ed8fd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79128230"
---
# <a name="connect-with-the-macos-client"></a>Nawiązywanie połączenia z klientem systemu macOS

> Dotyczy: macOS 10.12 lub nowszych

Dostęp do zasobów pulpitu wirtualnego systemu Windows można uzyskać z urządzeń z systemem macOS za pomocą naszego klienta do pobrania. W tym przewodniku dowiesz się, jak skonfigurować klienta.

## <a name="install-the-client"></a>Instalacja klienta

Aby rozpocząć, [pobierz](https://apps.apple.com/app/microsoft-remote-desktop/id1295203466?mt=12) i zainstaluj klienta na urządzeniu z systemem macOS.

## <a name="subscribe-to-a-feed"></a>Subskrybowanie kanału informacyjnego

Subskrybuj kanał, który podał twój administrator, aby uzyskać listę zarządzanych zasobów dostępnych na urządzeniu z systemem macOS.

Aby zasubskrybować kanał:

1. Wybierz **pozycję Dodaj kanał informacyjny** na stronie głównej, aby połączyć się z usługą i pobrać zasoby.
2. Wprowadź adres URL kanału informacyjnego. Może to być adres URL lub adres e-mail:
   - Jeśli używasz adresu URL, użyj adresu URL, który podał Ci administrator. Zwykle adres URL <https://rdweb.wvd.microsoft.com>to .
   - Aby korzystać z poczty e-mail, wpisz swój adres e-mail. To nakazuje klientowi wyszukać adres URL skojarzony z twoim adresem e-mail, jeśli administrator skonfigurował serwer w ten sposób.
3. Wybierz **pozycję Subskrybuj**.
4. Zaloguj się przy za pomocą konta użytkownika po wyświetleniu monitu.

Po zalogowaniu się powinna zostać wyświetlona lista dostępnych zasobów.

Po zasubskrybowaniu pliku danych zawartość pliku danych będzie regularnie aktualizowana automatycznie. Zasoby mogą być dodawane, zmieniane lub usuwane na podstawie zmian wprowadzonych przez administratora.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o kliencie systemu macOS, zapoznaj się [z dokumentacją klienta systemu macOS.](/windows-server/remote/remote-desktop-services/clients/remote-desktop-mac/)
