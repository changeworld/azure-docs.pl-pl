---
title: Linux Support pulpitu wirtualnego systemu Windows — Azure
description: Krótki przegląd obsługi systemu Linux dla pulpitu wirtualnego Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: helohr
ms.openlocfilehash: f7d62487605c26a68e7c71b6d95d7814da0a07c9
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/28/2020
ms.locfileid: "76776393"
---
# <a name="linux-support"></a>Linux Support

Możesz użyć zestawu Linux SDK dla pulpitu wirtualnego systemu Windows, aby skompilować autonomiczny Klient pulpitu wirtualnego systemu Windows. Można go również użyć do włączenia obsługi pulpitu wirtualnego systemu Windows w aplikacji klienckiej. Ten krótki przewodnik wyjaśnia, co to jest zestaw Linux SDK i jak zacząć z niego korzystać.

## <a name="what-is-the-linux-sdk"></a>Co to jest zestaw SDK systemu Linux?

Za pomocą interfejsów API zestawu SDK można pobierać źródła zasobów, łączyć się z sesjami pulpitu lub aplikacji zdalnych oraz korzystać z wielu przekierowań, które obsługują Klienci pierwszej firmy.

### <a name="supported-linux-distributions"></a>Obsługiwane dystrybucje systemu Linux

Zestaw SDK jest zgodny z większością systemów operacyjnych opartych na systemie Ubuntu 18,04 lub nowszym. Jeśli korzystasz z innej dystrybucji systemu Linux, możemy współpracować z nim, aby dowiedzieć się, jak najlepiej obsługiwać Twoje potrzeby.

### <a name="feature-support"></a>Obsługa funkcji

Zestaw SDK obsługuje wiele połączeń do sesji pulpitu i aplikacji zdalnych. Obsługiwane są następujące przekierowania:

| Przekierowania       | Obsługiwane |
| :---------------- | :-------: |
| Klawiatury          | &#10004;  |
| Wskaźnik             | &#10004;  |
| Dźwięk w          | &#10004;  |
| Wyjście audio         | &#10004;  |
| Schowek (tekst)  | &#10004;  |
| Schowek (obraz) | &#10004;  |
| Schowek (plik)  | &#10004;  |
| Kart         | &#10004;  |
| Dysk/folder      | &#10004;  |

Zestaw SDK obsługuje również wiele konfiguracji wyświetlania monitorów, o ile monitory wybrane dla sesji są ciągłe.

Ten dokument zostanie zaktualizowany w miarę dodawania obsługi nowych funkcji i przekierowań. Jeśli chcesz zasugerować nowe funkcje i inne ulepszenia, odwiedź naszą [stronę usługi UserVoice](https://go.microsoft.com/fwlink/?linkid=2116523).

## <a name="get-started-with-the-linux-sdk"></a>Wprowadzenie do zestawu SDK dla systemu Linux

Przed rozpoczęciem opracowywania klienta systemu Linux dla pulpitu wirtualnego z systemem Windows należy wykonać następujące czynności:

1. Kompiluj i wdrażaj środowisko pulpitu wirtualnego systemu Windows na potrzeby testowania lub użycia produkcyjnego.
2. Przetestuj dostępnych klientów pierwszej firmy, aby zapoznać się z interfejsem użytkownika pulpitu wirtualnego systemu Windows.

## <a name="next-steps"></a>Następne kroki

Możesz zażądać dostępu do zestawu Linux SDK na naszym [forum społeczności Tech](https://go.microsoft.com/fwlink/?linkid=2116541).

Zapoznaj się z naszą dokumentacją dla następujących klientów:

- [Klient klasyczny systemu Windows](connect-windows-7-and-10.md)
- [Klient sieci Web](connect-web.md)
- [Klient systemu Android](connect-android.md)
- [Klient macOS](connect-macos.md)
- [Klient systemu iOS](connect-ios.md)
