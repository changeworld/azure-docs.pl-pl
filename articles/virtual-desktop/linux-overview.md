---
title: Obsługa systemu Windows Virtual Desktop Linux — platforma Azure
description: Krótki przegląd linux wsparcie dla Windows Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 967fd1fa182b7c8e581fd74cc287c5a6ba0e4038
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127827"
---
# <a name="linux-support"></a>Obsługa systemu Linux

Za pomocą pakietu Linux SDK dla pulpitu wirtualnego systemu Windows można utworzyć samodzielny klient pulpitu wirtualnego systemu Windows. Można go również użyć, aby włączyć obsługę pulpitu wirtualnego systemu Windows w aplikacji klienckiej. Ten krótki przewodnik wyjaśni, czym jest SDK Linuksa i jak zacząć go używać.

## <a name="what-is-the-linux-sdk"></a>Co to jest SDK Systemu Linux?

Interfejsy API sdk można używać do pobierania źródeł danych źródłowych zasobów, łączenia się z sesjami aplikacji pulpitu lub zdalnego i używania wielu przekierowań obsługiwanych przez naszych klientów zewnętrznych.

> [!NOTE]
> SDK jest obecnie w fazie rozwoju. Zaktualizujemy ten dokument z instrukcjami dostępu do sdk, gdy jest on dostępny.

### <a name="supported-linux-distributions"></a>Obsługiwane dystrybucje linuksa

SDK jest kompatybilny z większością systemów operacyjnych opartych na Ubuntu 18.04 lub nowszym. Jeśli masz inną dystrybucję Linuksa, możemy współpracować z Tobą, aby dowiedzieć się, jak najlepiej wspierać Twoje potrzeby.

### <a name="feature-support"></a>Obsługa funkcji

Zestaw SDK obsługuje wiele połączeń z sesjami aplikacji stacjonarnych i zdalnych. Obsługiwane są następujące przekierowania:

| Przekierowania       | Obsługiwane |
| :---------------- | :-------: |
| Klawiatura          | &#10004;  |
| Mysz             | &#10004;  |
| Dźwięk w          | &#10004;  |
| Wyjście audio         | &#10004;  |
| Schowek (tekst)  | &#10004;  |
| Schowek (zdjęcie) | &#10004;  |
| Schowek (plik)  | &#10004;  |
| Smartcard         | &#10004;  |
| Dysk/folder      | &#10004;  |

Zestaw SDK obsługuje również wiele konfiguracji wyświetlania monitorów, o ile monitory wybrane dla sesji są ciągłe.

Zaktualizujemy ten dokument, gdy dodamy obsługę nowych funkcji i przekierowań. Jeśli chcesz zaproponować nowe funkcje i inne ulepszenia, odwiedź naszą [stronę UserVoice](https://go.microsoft.com/fwlink/?linkid=2116523).

## <a name="get-started-with-the-linux-sdk"></a>Wprowadzenie do sdk linuksa

Przed opracowaniem klienta systemu Linux dla pulpitu wirtualnego systemu Windows należy wykonać następujące czynności:

1. Tworzenie i wdrażanie środowiska pulpitu wirtualnego systemu Windows do testowania lub użytku produkcyjnego.
2. Przetestuj dostępnych klientów innych firm, aby zapoznać się z środowiskiem użytkownika pulpitu wirtualnego systemu Windows.

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z naszą dokumentacją dla następujących klientów:

- [Klient pulpitu systemu Windows](connect-windows-7-and-10.md)
- [Klient sieci Web](connect-web.md)
- [Klient Androida](connect-android.md)
- [Klient systemu macOS](connect-macos.md)
- [Klient systemu iOS](connect-ios.md)
