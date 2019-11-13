---
title: Łączenie z pulpitem wirtualnym systemu Windows 10 lub 7 — Azure
description: Jak nawiązać połączenie z pulpitem wirtualnym systemu Windows przy użyciu klienta pulpitu systemu Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: helohr
ms.openlocfilehash: 420978e79da99847f16e5c85b2d2510083ca97a7
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/13/2019
ms.locfileid: "74006273"
---
# <a name="connect-with-the-windows-desktop-client"></a>Łączenie się z klientem klasycznym systemu Windows

> Dotyczy: Windows 7 i Windows 10

Możesz uzyskać dostęp do zasobów pulpitu wirtualnego systemu Windows na urządzeniach z systemem Windows 7 lub Windows 10 przy użyciu klienta pulpitu systemu Windows.

> [!IMPORTANT]
> Pulpit wirtualny systemu Windows nie obsługuje klienta połączenia programów RemoteApp i pulpitu (RADC) ani klienta Podłączanie pulpitu zdalnego (MSTSC).

## <a name="install-the-windows-desktop-client"></a>Instalowanie klienta klasycznego systemu Windows

Wybierz klienta zgodnego z Twoją wersją systemu Windows:

- [Windows 64-bit](https://go.microsoft.com/fwlink/?linkid=2068602)
- [Windows 32-bit](https://go.microsoft.com/fwlink/?linkid=2098960)
- [ARM64 systemu Windows](https://go.microsoft.com/fwlink/?linkid=2098961)

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
