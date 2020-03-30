---
title: Łączenie się z pulpitem wirtualnym systemu Windows z systemu iOS — Azure
description: Jak połączyć się z pulpitem wirtualnym systemu Windows przy użyciu klienta systemu iOS.
services: virtual-desktop
author: heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 02/08/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 3bf9ea6c68e4cbbe721705639e6c6416c0e93c9d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79128256"
---
# <a name="connect-with-the-ios-client"></a>Nawiązywanie połączenia z klientem systemu iOS

> Dotyczy: iOS 13.0 lub nowszych. Kompatybilny z telefonami iPhone, iPadem i iPodem touch.

Dostęp do zasobów pulpitu wirtualnego systemu Windows można uzyskać z urządzenia z systemem iOS za pomocą naszego klienta do pobrania. W tym przewodniku dowiesz się, jak skonfigurować klienta systemu iOS.

## <a name="install-the-ios-client"></a>Instalowanie klienta systemu iOS

Aby rozpocząć, [pobierz](https://aka.ms/rdios) i zainstaluj klienta na urządzeniu z systemem iOS.

## <a name="subscribe-to-a-feed"></a>Subskrybowanie kanału informacyjnego

Subskrybuj kanał informacyjny dostarczony przez administratora, aby uzyskać listę zarządzanych zasobów, do których możesz uzyskać dostęp na urządzeniu z systemem iOS.

Aby zasubskrybować kanał:

1. W Centrum połączeń **+** naciśnij pozycję , a następnie naciśnij pozycję **Dodaj obszar roboczy**.
2. Wprowadź adres URL pliku danych w polu **Adres URL kanału informacyjnego.** Adres URL pliku danych może być adresem URL lub adresem e-mail.
   - Jeśli używasz adresu URL, użyj adresu URL, który podał Ci administrator. Zwykle adres URL <https://rdweb.wvd.microsoft.com>to .
   - Aby korzystać z poczty e-mail, wpisz swój adres e-mail. To nakazuje klientowi wyszukać adres URL skojarzony z twoim adresem e-mail, jeśli administrator skonfigurował serwer w ten sposób.
3. Naciśnij pozycję **Dalej**.
4. Po wyświetleniu monitu podaj poświadczenia.
   - W przypadku **nazwy użytkownika**należy podać nazwę użytkownika z uprawnieniami dostępu do zasobów.
   - W przypadku **hasła**podaj hasło skojarzone z nazwą użytkownika.
   - Może również zostać wyświetlony monit o podanie dodatkowych czynników, jeśli administrator skonfigurował uwierzytelnianie w ten sposób.
5. Naciśnij pozycję **Zapisz**.

Następnie Centrum połączeń powinno wyświetlać zasoby zdalne.

Po zasubskrybowaniu pliku danych zawartość pliku danych będzie regularnie aktualizowana automatycznie. Zasoby mogą być dodawane, zmieniane lub usuwane na podstawie zmian wprowadzonych przez administratora.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat korzystania z klienta systemu iOS, zapoznaj się z dokumentacją [klienta systemu iOS](/windows-server/remote/remote-desktop-services/clients/remote-desktop-ios/) wprowadzenie.
