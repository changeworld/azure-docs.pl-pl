---
title: Łączenie się z pulpitem wirtualnym systemu Windows Windows 10 lub 7 — Platforma Azure
description: Jak połączyć się z pulpitem wirtualnym systemu Windows za pomocą klienta pulpitu systemu Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/19/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: b6436059cc8b3637edfc2a146d0aab3e2beae6a8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80154342"
---
# <a name="connect-with-the-windows-desktop-client"></a>Łączenie się z klientem klasycznym systemu Windows

> Dotyczy: Windows 7, Windows 10 i Windows 10 IoT Enterprise

Dostęp do zasobów pulpitu wirtualnego systemu Windows można uzyskać na urządzeniach z systemami Windows 7, Windows 10 i Windows 10 IoT Enterprise za pomocą klienta pulpitu systemu Windows.

> [!IMPORTANT]
> Pulpit wirtualny systemu Windows nie obsługuje klienta usługi RemoteApp i połączenia pulpitu (RADC) ani klienta MSTSC (Remote Desktop Connection).

> [!IMPORTANT]
> Pulpit wirtualny systemu Windows obecnie nie obsługuje klienta pulpitu zdalnego ze Sklepu Windows. Obsługa tego klienta zostanie dodana w przyszłej wersji.

## <a name="install-the-windows-desktop-client"></a>Instalowanie klienta pulpitu systemu Windows

Wybierz klienta, który pasuje do Twojej wersji systemu Windows:

- [Windows 64-bitowy](https://go.microsoft.com/fwlink/?linkid=2068602)
- [Windows 32-bitowy](https://go.microsoft.com/fwlink/?linkid=2098960)
- [Windows ARM64](https://go.microsoft.com/fwlink/?linkid=2098961)

Możesz zainstalować klienta dla bieżącego użytkownika, który nie wymaga praw administratora, lub administrator może zainstalować i skonfigurować klienta, tak aby wszyscy użytkownicy na urządzeniu mieli do niego dostęp.

Po zainstalowaniu klient może zostać uruchomiony z menu Start, wyszukując **pulpit zdalny**.

## <a name="subscribe-to-a-feed"></a>Subskrybowanie kanału informacyjnego

Uzyskaj listę zarządzanych zasobów dostępnych, subskrybując kanał informacyjny dostarczony przez administratora. Subskrybowanie udostępnia zasoby na komputerze lokalnym.

Aby zasubskrybować kanał:

1. Otwórz klienta pulpitu systemu Windows.
2. Wybierz **pozycję Subskrybuj** na stronie głównej, aby połączyć się z usługą i pobrać zasoby.
3. Zaloguj się przy za pomocą konta użytkownika po wyświetleniu monitu.

Po pomyślnym zalogowaniu powinna zostać wyświetlona lista zasobów, do których można uzyskać dostęp.

Zasoby można uruchamiać za pomocą jednej z dwóch metod.

- Na stronie głównej klienta kliknij dwukrotnie zasób, aby go uruchomić.
- Uruchom zasób tak, jak zwykle inne aplikacje z menu Start.
  - Możesz też wyszukać aplikacje na pasku wyszukiwania.

Po zasubskrybowaniu pliku danych zawartość pliku danych jest regularnie aktualizowana automatycznie. Zasoby mogą być dodawane, zmieniane lub usuwane na podstawie zmian wprowadzonych przez administratora.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o korzystaniu z klienta pulpitu systemu Windows, zapoznaj się [z wprowadzeniem do klienta pulpitu systemu Windows](/windows-server/remote/remote-desktop-services/clients/windowsdesktop/).
