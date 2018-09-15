---
title: Dowiedz się więcej o koderów zalecane przez usługę Azure Media Services | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o koderów zalecane przez usługę media services
services: media-services
keywords: kodowanie koderów; multimediów
author: dbgeorge
manager: johndeu
ms.author: johndeu
ms.date: 09/13/2018
ms.topic: article
ms.service: media-services
ms.openlocfilehash: c90d6a5784fe9d80df4fab304b6122d3fa24d0b5
ms.sourcegitcommit: 616e63d6258f036a2863acd96b73770e35ff54f8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/14/2018
ms.locfileid: "45605168"
---
# <a name="recommended-on-premises-encoders"></a>Zalecane kodery lokalne
Gdy strumieniowo na żywo za pomocą usługi Azure Media Services, można określić, jak chcesz kanał w taki sposób, aby otrzymywać strumień wejściowy. Jeśli zdecydujesz się koder lokalnej za pomocą kanału kodowania na żywo, koder ma wypychać strumienia wysokiej jakości jedną szybkością transmisji bitów jako dane wyjściowe. Jeśli zdecydujesz się koder lokalnej za pomocą przebiegu za pośrednictwem kanału, koder ma wypychać strumień o wielokrotnej szybkości transmisji bitów jako dane wyjściowe z wszystkich klas żądaną produktu wyjściowego. Aby uzyskać więcej informacji, zobacz [przesyłania strumieniowego przy użyciu lokalnych koderów na żywo](media-services-live-streaming-with-onprem-encoders.md).

Usługa Azure Media Services zaleca się przy użyciu jednej z poniższych koderów na żywo, które mają RTMP jako dane wyjściowe:
- Adobe Flash Media Live Encoder 3.2
- X Makito Haivision — HEVC
- Haivision KB
- Telestream Wirecast 8.1 +
- Telestream Wirecast S
- Wycinek Teradek 756
- TriCaster 8000
- HD Tricaster Mini-4
- Studio systemu bankowości Internetowej
- VMIX
- xStream
- Przełącznik Studio (iOS)

Usługa Azure Media Services zaleca się przy użyciu jednej z następujących koderów na żywo, które mają różnych szybkościach transmisji bitów podzielonej zawartości — w formacie MP4 (Smooth Streaming) jako dane wyjściowe:
- Nośnik w programie Excel Hero na żywo i Hero 4K (UHD / — HEVC)
- Ateme TITAN na żywo
- Kodera multimediów cyfrowych Cisco 2200
- Elemental Live
- Envivio 4Caster C4 ogólnego III
- Wyobraź sobie Selenio MCP3 komunikacji

> [!NOTE]
> Koder na żywo może wysyłać strumień z jedną szybkością transmisji bitów do przebiegu za pośrednictwem kanału, ale ta konfiguracja nie jest zalecane, ponieważ nie zezwala na adaptacyjną szybkością transmisji bitów, przesyłanie strumieniowe do klienta.

## <a name="how-to-become-an-on-prem-encoder-partner"></a>Jak zostać partnerem usługi Encoder w warstwie lokalnej
Jako partner usługi Azure Media Services Encoder w warstwie lokalnej usługi Media Services wspiera produktu przez polecanie koder klientom korporacyjnym. Aby zostać partnerem encoder w warstwie Premium, należy sprawdzić zgodność koder w warstwie Premium z usługą Media Services. Aby to zrobić, wykonaj następujące weryfikacji:

Przekaż za pośrednictwem weryfikacji kanału
1. Tworzenie lub przejdź do konta usługi Azure Media Services
2. Tworzenie i uruchamianie **przekazywanego** kanału
3. Konfiguruj koder wypychanie strumienia na żywo o różnych szybkościach transmisji bitów.
4. Utwórz opublikowane wydarzenie na żywo
5. Uruchom koder na żywo przez około 10 minut
6. Zatrzymaj wydarzenie na żywo
7. Tworzenie, uruchamianie punktu końcowego przesyłania strumieniowego, używać odtwarzacza, takich jak [usługi Azure Media Player](https://ampdemo.azureedge.net/azuremediaplayer.html) Obejrzyj zarchiwizowanego elementu zawartości to upewnić się, że odtwarzanie nie występującymi widoczne dla wszystkich poziomów jakości (lub też oglądanie i zweryfikować za pośrednictwem adresu URL (wersja zapoznawcza) podczas sesji na żywo, przed wykonaniem kroku 6)
8. Zapisz identyfikator zasobu adresu URL przesyłania strumieniowego na żywo archiwum i ustawień i wersja używanego z koder na żywo opublikowane
9. Zresetuj stan kanału po utworzeniu każdej próbki
10. Powtórz kroki od 3 do 9, w przypadku wszystkich konfiguracji obsługiwanych przez koder (z lub bez ad sygnalizowanie/napisów/inną kodowanie szybkości)

Kodowanie weryfikacji kanału na żywo
1. Tworzenie lub przejdź do konta usługi Azure Media Services
2. Tworzenie i uruchamianie **kodowanie na żywo** kanału
3. Konfiguruj koder wypychanie strumienia na żywo jedną szybkością transmisji bitów.
4. Utwórz opublikowane wydarzenie na żywo
5. Uruchom koder na żywo przez około 10 minut
6. Zatrzymaj wydarzenie na żywo
7. Tworzenie, uruchamianie punktu końcowego przesyłania strumieniowego, używać odtwarzacza, takich jak [usługi Azure Media Player](https://ampdemo.azureedge.net/azuremediaplayer.html) Obejrzyj zarchiwizowanego elementu zawartości to upewnić się, że odtwarzanie nie występującymi widoczne dla wszystkich poziomów jakości (lub też oglądanie i zweryfikować za pośrednictwem adresu URL (wersja zapoznawcza) podczas sesji na żywo, przed wykonaniem kroku 6)
8. Zapisz identyfikator zasobu adresu URL przesyłania strumieniowego na żywo archiwum i ustawień i wersja używanego z koder na żywo opublikowane
9. Zresetuj stan kanału po utworzeniu każdej próbki
10. Powtórz kroki od 3 do 9, w przypadku wszystkich konfiguracji obsługiwanych przez koder (z lub bez ad sygnalizowanie/napisów/różne kodowania szybkość)

Weryfikacja trwałość
1. Tworzenie lub przejdź do konta usługi Azure Media Services
2. Tworzenie i uruchamianie **przekazywanego** kanału
3. Konfiguruj koder wypychanie strumienia na żywo o różnych szybkościach transmisji bitów.
4. Utwórz opublikowane wydarzenie na żywo
5. Uruchom koder na żywo przez jednego tygodnia lub dłużej
6. Używanie odtwarzacz, takiej jak [usługi Azure Media Player](https://ampdemo.azureedge.net/azuremediaplayer.html) obejrzeć transmisji strumieniowych na żywo od czasu do czasu (lub zarchiwizowanego elementu zawartości) to upewnić się, że odtwarzanie nie występującymi widoczne
7. Zatrzymaj wydarzenie na żywo
8. Zapisz identyfikator zasobu adresu URL przesyłania strumieniowego na żywo archiwum i ustawień i wersja używanego z koder na żywo opublikowane

Na koniec wysyłania zarejestrowane ustawienia i parametry archiwum do usługi Media Services na żywo przez wysyłanie wiadomości e-mail amsstreaming@microsoft.com. Po otrzymaniu Media Services wykonuje testy weryfikacji na próbkach z koder na żywo. Możesz skontaktować się ze usługi Media Services za pomocą jakieś pytania dotyczące tego procesu.