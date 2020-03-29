---
title: Łączenie się z pulpitem wirtualnym systemu Windows z systemu Android — Azure
description: Jak połączyć się z pulpitem wirtualnym systemu Windows za pomocą klienta systemu Android.
services: virtual-desktop
author: heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: d2990c82efbcdac7d453f920301787b8c83db1e9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295377"
---
# <a name="connect-with-the-android-client"></a>Łączenie się z klientem systemu Android

> Dotyczy: Android 4.1 lub nowszych, Chromebooków z ChromeOS 53 lub nowszymi.

>[!NOTE]
> Możliwość dostępu do zasobów pulpitu wirtualnego systemu Windows z klienta systemu Android jest obecnie dostępna w wersji zapoznawczej.

Dostęp do zasobów pulpitu wirtualnego systemu Windows można uzyskać z urządzenia z systemem Android za pomocą naszego klienta do pobrania. Możesz też korzystać z klienta Androida na urządzeniach Chromebooka obsługujących Sklep Google Play. W tym przewodniku dowiesz się, jak skonfigurować klienta androida.

## <a name="install-the-android-client"></a>Zainstaluj klienta Androida

Aby rozpocząć, [pobierz](https://play.google.com/store/apps/details?id=com.microsoft.rdc.androidx) i zainstaluj klienta na urządzeniu z Androidem.

## <a name="subscribe-to-a-feed"></a>Subskrybowanie kanału informacyjnego

Subskrybuj kanał informacyjny dostarczony przez administratora, aby uzyskać listę zarządzanych zasobów, do których możesz uzyskać dostęp na urządzeniu z Androidem.

Aby zasubskrybować kanał:

1. W Centrum połączeń **+** naciśnij pozycję , a następnie naciśnij pozycję **Zdalny kanał informacyjny zasobów**.
2. Wprowadź adres URL pliku danych w polu **Adres URL kanału informacyjnego.** Adres URL pliku danych może być adresem URL lub adresem e-mail.
   - Jeśli używasz adresu URL, użyj adresu URL, <https://rdweb.wvd.microsoft.com>który dał Ci administrator, normalnie .
   - Aby korzystać z poczty e-mail, wpisz swój adres e-mail. Klient wyszuka adres URL skojarzony z Twoim adresem e-mail, jeśli administrator skonfigurował serwer w ten sposób.
3. Naciśnij przycisk **DALEJ**.
4. Po wyświetleniu monitu podaj poświadczenia.
   - W przypadku **nazwy użytkownika**należy podać nazwę użytkownika z uprawnieniami dostępu do zasobów.
   - W przypadku **hasła**podaj hasło skojarzone z nazwą użytkownika.
   - Może również zostać wyświetlony monit o podanie dodatkowych czynników, jeśli administrator skonfigurował uwierzytelnianie w ten sposób.

Po subskrybowaniu Centrum połączeń powinno wyświetlać zasoby zdalne.

Po zasubskrybowaniu pliku danych zawartość pliku danych będzie regularnie aktualizowana automatycznie. Zasoby mogą być dodawane, zmieniane lub usuwane na podstawie zmian wprowadzonych przez administratora.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o korzystaniu z klienta Androida, zapoznaj się [z Wprowadzenie do klienta Android](/windows-server/remote/remote-desktop-services/clients/remote-desktop-android/).
