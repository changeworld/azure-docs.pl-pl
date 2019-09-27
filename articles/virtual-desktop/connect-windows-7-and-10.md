---
title: Nawiązywanie połączenia z programem Windows Virtual Desktop Preview z systemu Windows 10 lub Windows 7 — Azure
description: Jak nawiązać połączenie z pulpitem wirtualnym systemu Windows przy użyciu klienta pulpitu systemu Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 09/24/2019
ms.author: helohr
ms.openlocfilehash: 49431f75fb801c683a167e1809724eeeef208561
ms.sourcegitcommit: e1b6a40a9c9341b33df384aa607ae359e4ab0f53
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/27/2019
ms.locfileid: "71338451"
---
# <a name="connect-with-the-windows-desktop-client"></a>Nawiązywanie połączenia z klientem klasycznym systemu Windows

> Dotyczy: Windows 7 i Windows 10

Możesz uzyskać dostęp do zasobów pulpitu wirtualnego systemu Windows na urządzeniach z systemem Windows 7 lub Windows 10 przy użyciu klienta pulpitu systemu Windows.

> [!IMPORTANT]
> Pulpit wirtualny systemu Windows nie obsługuje klienta połączenia programów RemoteApp i pulpitu (RADC) ani klienta Podłączanie pulpitu zdalnego (MSTSC).

## <a name="install-the-windows-desktop-client"></a>Instalowanie klienta klasycznego systemu Windows

Poniżej przedstawiono klientów, których można obecnie pobrać:

- [Windows 64-bit](https://go.microsoft.com/fwlink/?linkid=2068602)

Będziemy aktualizować tę listę, ponieważ klient stanie się dostępny dla większej liczby wersji systemu Windows.

Klienta programu można zainstalować dla bieżącego użytkownika, który nie wymaga uprawnień administratora, lub administrator może zainstalować i skonfigurować klienta tak, aby wszyscy użytkownicy na urządzeniu mogli uzyskać do niego dostęp.

Po zainstalowaniu klienta programu można uruchomić z menu Start, wyszukując **pulpit zdalny**.

## <a name="subscribe-to-a-feed"></a>Subskrybowanie kanału informacyjnego

Uzyskaj listę dostępnych zasobów zarządzanych przez zasubskrybowanie kanału informacyjnego dostarczonego przez administratora. Subskrybowanie sprawia, że zasoby są dostępne na komputerze lokalnym.

Aby subskrybować źródło danych:

1. Otwórz klienta pulpitu systemu Windows.
2. Wybierz pozycję **Subskrybuj** na stronie głównej, aby nawiązać połączenie z usługą i pobrać zasoby.
3. Po wyświetleniu monitu zaloguj się przy użyciu konta użytkownika.

Po pomyślnym zalogowaniu powinna zostać wyświetlona lista zasobów, do których można uzyskać dostęp.

Zasoby można uruchamiać przy użyciu jednej z dwóch metod.

- Na stronie głównej klienta kliknij dwukrotnie zasób, aby go uruchomić.
- Uruchom zasób, ponieważ inne aplikacje są zwykle z menu Start.
  - Możesz również wyszukać aplikacje na pasku wyszukiwania.

Po zasubskrybowaniu kanału informacyjnego zawartość kanału informacyjnego jest regularnie aktualizowana automatycznie. Zasoby mogą zostać dodane, zmienione lub usunięte na podstawie zmian wprowadzonych przez administratora.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat korzystania z klienta klasycznego systemu Windows, zapoznaj się z tematem [wprowadzenie do klienta klasycznego systemu Windows](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/windowsdesktop).
