---
title: Nawiązywanie połączenia z pulpitem wirtualnym systemu Windows w systemie Android — Azure
description: Jak nawiązać połączenie z pulpitem wirtualnym systemu Windows przy użyciu klienta systemu Android.
services: virtual-desktop
author: heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 09/26/2019
ms.author: helohr
ms.openlocfilehash: 41b0c1ced9e66bd58d73683865b2c40afc16c5d3
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/05/2019
ms.locfileid: "73605846"
---
# <a name="connect-with-the-android-client"></a>Łączenie się z klientem systemu Android

> Dotyczy: system Android 4,1 i nowsze, Chromebooks z ChromeOS 53 i nowszymi.

>[!NOTE]
> Możliwość dostępu do zasobów pulpitu wirtualnego systemu Windows z klienta systemu Android jest obecnie dostępna w wersji zapoznawczej.

Możesz uzyskać dostęp do zasobów pulpitu wirtualnego systemu Windows z urządzenia z systemem Android za pomocą klienta do pobrania. Klienta systemu Android można także używać na urządzeniach Chromebook, które obsługują Sklep Google Play. W tym przewodniku przedstawiono sposób konfigurowania klienta systemu Android.

## <a name="install-the-android-client"></a>Instalowanie klienta systemu Android

Aby rozpocząć, [Pobierz](https://play.google.com/store/apps/details?id=com.microsoft.rdc.android) i zainstaluj klienta na urządzeniu z systemem Android.

## <a name="subscribe-to-a-feed"></a>Subskrybowanie kanału informacyjnego

Zasubskrybuj źródło danych dostarczone przez administratora, aby uzyskać listę zasobów zarządzanych, do których możesz uzyskać dostęp na urządzeniu z systemem Android.

Aby subskrybować źródło danych:

1. W centrum połączenia naciśnij pozycję **+** , a następnie naciśnij pozycję **zdalne źródło danych**.
2. Wprowadź adres URL źródła danych w polu **adres URL źródła danych** . Adres URL źródła danych może być adresem URL lub adresem e-mail.
   - Jeśli używasz adresu URL, użyj swojego administratora, który został przez Ciebie podany, zwykle <https://rdweb.wvd.microsoft.com>.
   - Aby użyć poczty e-mail, wprowadź swój adres e-mail. Klient będzie szukać adresu URL skojarzonego z Twoim adresem e-mail, jeśli administrator skonfigurował serwer w taki sposób.
3. Naciśnij przycisk **dalej**.
4. Po wyświetleniu monitu podaj swoje poświadczenia.
   - W polu **Nazwa użytkownika**Nadaj nazwę użytkownikowi uprawnienia dostępu do zasobów.
   - W polu **hasło**Podaj hasło skojarzone z nazwą użytkownika.
   - Może być również wyświetlony monit o podanie dodatkowych czynników, jeśli administrator skonfigurował uwierzytelnianie w taki sposób.

Po zasubskrybowaniu centrum połączeń powinno wyświetlić zasoby zdalne.

Po zasubskrybowaniu kanału informacyjnego zawartość kanału informacyjnego będzie regularnie aktualizowana automatycznie. Zasoby mogą zostać dodane, zmienione lub usunięte na podstawie zmian wprowadzonych przez administratora.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat korzystania z klienta systemu Android, zapoznaj się z tematem Rozpoczynanie [pracy z klientem systemu Android](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-android).
