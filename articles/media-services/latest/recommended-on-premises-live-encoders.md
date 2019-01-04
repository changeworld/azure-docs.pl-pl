---
title: Dowiedz się więcej o koderów na żywo transmisji strumieniowej w środowisku lokalnym zalecane przez usługę Media Services — Azure | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o zalecanych przez usługę Media Services na żywo transmisji strumieniowej kodery lokalne
services: media-services
keywords: kodowanie koderów; multimediów
author: johndeu
manager: johndeu
ms.author: johndeu
ms.date: 12/14/2018
ms.topic: article
ms.service: media-services
ms.openlocfilehash: d1110669bd0ca8c0ba0caf34ef41861c500bdd33
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/27/2018
ms.locfileid: "53790032"
---
# <a name="recommended-live-streaming-encoders"></a>Zalecane kodery transmisji strumieniowej na żywo

W usłudze Media Services [element LiveEvent](https://docs.microsoft.com/rest/api/media/liveevents) (kanał) reprezentuje potok przetwarzania zawartości transmisji strumieniowej na żywo. Element LiveEvent odbiera strumieni danych wejściowych w jeden z dwóch sposobów:

* Koder na żywo w środowisku lokalnym wysyła różnych szybkościach transmisji bitów RTMP lub Smooth Streaming (pofragmentowany MP4) strumienia na element LiveEvent, która nie jest włączona kodowanie na żywo za pomocą usługi Media Services. Pozyskiwane strumienie są przekazywane za pośrednictwem LiveEvents bez dalszego przetwarzania. Ta metoda jest wywoływana **przekazywanego**. Koder na żywo może wysyłać strumień z jedną szybkością transmisji bitów do przebiegu za pośrednictwem kanału, ale ta konfiguracja nie jest zalecane, ponieważ nie zezwala na adaptacyjną szybkością transmisji bitów, przesyłanie strumieniowe do klienta.

  > [!NOTE]
  > Metoda przekazywania to najbardziej ekonomiczny sposób transmisja strumieniowa na żywo.

* Lokalny koder na żywo wysyła strumień o pojedynczej szybkości bitów na element LiveEvent, który jest skonfigurowany do przeprowadzania kodowania na żywo z usługą Media Services w jednym z następujących formatów: RTMP lub Smooth Streaming (pofragmentowany MP4). Element LiveEvent wykonuje następnie kodowanie na żywo przychodzącego strumienia pojedynczej do wielokrotnej szybkości transmisji bitów (adaptacyjnej) strumienia wideo.

Aby uzyskać szczegółowe informacje na temat kodowania na żywo za pomocą usługi Media Services, zobacz [na żywo, przesyłanie strumieniowe przy użyciu usługi Media Services v3](live-streaming-overview.md).

## <a name="live-encoders-that-output-rtmp"></a>Dane wyjściowe RTMP koderów na żywo

Usługa Media Services zaleca się przy użyciu jednej z następujących koderów na żywo, które mają RTMP jako dane wyjściowe. Obsługiwane schematy adresów URL są `rtmp://` lub `rtmps://`.

> [!NOTE]
 > Podczas przesyłania strumieniowego za pośrednictwem protokołu RTMP, sprawdź ustawienia zapory i/lub serwer proxy, aby upewnić się, że porty TCP ruchu wychodzącego 1935 i 1936 są otwarte.<br/>
 Podczas przesyłania strumieniowego za pośrednictwem RTMPS, sprawdź ustawienia zapory i/lub serwer proxy, aby upewnić się, że porty TCP ruchu wychodzącego 2935 i 2936 są otwarte.

- Adobe Flash Media Live Encoder 3.2
- Haivision KB
- X Makito Haivision — HEVC
- Studio systemu bankowości Internetowej
- Przełącznik Studio (iOS)
- Telestream Wirecast 8.1 +
- Telestream Wirecast S
- Wycinek Teradek 756
- TriCaster 8000
- HD Tricaster Mini-4
- VMIX
- xStream

## <a name="live-encoders-that-output-fragmented-mp4"></a>Dane wyjściowe pofragmentowany plik MP4 koderów na żywo

Usługa Media Services zaleca się przy użyciu jednej z następujących koderów na żywo, które mają różnych szybkościach transmisji bitów Smooth Streaming (pofragmentowany plik MP4) jako dane wyjściowe. Obsługiwane schematy adresów URL są `rtmp://` lub `rtmps://`.

- Ateme TITAN na żywo
- Kodera multimediów cyfrowych Cisco 2200
- Elemental Live
- Envivio 4Caster C4 ogólnego III
- Wyobraź sobie Selenio MCP3 komunikacji
- Nośnik w programie Excel Hero na żywo i Hero 4K (UHD / — HEVC)

## <a name="how-to-become-an-on-premises-encoder-partner"></a>Jak zostać partnerem kodera w środowisku lokalnym

Jako partner usługi Azure Media Services Encoder w warstwie lokalnej usługi Media Services wspiera produktu przez polecanie koder klientom korporacyjnym. Aby zostać partnerem kodera w środowisku lokalnym, należy sprawdzić zgodność koder w środowisku lokalnym za pomocą usługi Media Services. Aby to zrobić, wykonaj następujące weryfikacji:

### <a name="pass-through-liveevent-verification"></a>Weryfikacja element LiveEvent przekazywania

1. Utwórz lub przejdź do konta usługi Azure Media Services.
2. Tworzenie i uruchamianie **przekazywanego** element LiveEvent.
3. Konfiguruj koder wypychanie strumienia na żywo o różnych szybkościach transmisji bitów.
4. Utwórz zdarzenia na żywo opublikowane.
5. Uruchom koder na żywo przez około 10 minut.
6. Zatrzymaj wydarzenie na żywo.
7. Tworzenie, uruchamianie punktu końcowego przesyłania strumieniowego, używać odtwarzacza, takich jak [usługi Azure Media Player](https://ampdemo.azureedge.net/azuremediaplayer.html) Obejrzyj zarchiwizowanego elementu zawartości to upewnić się, że odtwarzanie nie występującymi widoczne dla wszystkich poziomów jakości (lub też oglądanie i zweryfikować za pośrednictwem adresu URL (wersja zapoznawcza) podczas sesji na żywo przed wykonaniem kroku 6).
8. Zapisz identyfikator zasobu adresu URL przesyłania strumieniowego na żywo archiwum i ustawień i wersja używanego z koder na żywo opublikowane.
9. Resetowanie stanu element LiveEvent po utworzeniu każdej próbki.
10. Powtórz kroki od 3 do 9, w przypadku wszystkich konfiguracji obsługiwanych przez koder (z lub bez ad sygnalizowanie/napisów/inną kodowanie szybkości).

### <a name="live-encoding-liveevent-verification"></a>Element LiveEvent weryfikacji kodowanie na żywo

1. Utwórz lub przejdź do konta usługi Azure Media Services.
2. Tworzenie i uruchamianie **kodowanie na żywo** element LiveEvent.
3. Konfiguruj koder wypychanie strumienia na żywo jedną szybkością transmisji bitów.
4. Utwórz zdarzenia na żywo opublikowane.
5. Uruchom koder na żywo przez około 10 minut.
6. Zatrzymaj wydarzenie na żywo.
7. Tworzenie, uruchamianie punktu końcowego przesyłania strumieniowego, używać odtwarzacza, takich jak [usługi Azure Media Player](https://ampdemo.azureedge.net/azuremediaplayer.html) Obejrzyj zarchiwizowanego elementu zawartości to upewnić się, że odtwarzanie nie występującymi widoczne dla wszystkich poziomów jakości (lub też oglądanie i zweryfikować za pośrednictwem adresu URL (wersja zapoznawcza) podczas sesji na żywo przed wykonaniem kroku 6).
8. Zapisz identyfikator zasobu adresu URL przesyłania strumieniowego na żywo archiwum i ustawień i wersja używanego z koder na żywo opublikowane.
9. Resetowanie stanu element LiveEvent po utworzeniu każdej próbki.
10. Powtórz kroki od 3 do 9, w przypadku wszystkich konfiguracji obsługiwanych przez koder (z lub bez ad sygnalizowanie/napisów/różne kodowania szybkości).

### <a name="longevity-verification"></a>Weryfikacja trwałość

1. Utwórz lub przejdź do konta usługi Azure Media Services.
2. Tworzenie i uruchamianie **przekazywanego** kanału.
3. Konfiguruj koder wypychanie strumienia na żywo o różnych szybkościach transmisji bitów.
4. Utwórz zdarzenia na żywo opublikowane.
5. Uruchom koder na żywo przez jednego tygodnia lub dłużej.
6. Używanie odtwarzacz, takiej jak [usługi Azure Media Player](https://ampdemo.azureedge.net/azuremediaplayer.html) obejrzeć transmisji strumieniowych na żywo od czasu do czasu (lub zarchiwizowanego elementu zawartości) to upewnić się, że odtwarzanie nie występującymi widoczne.
7. Zatrzymaj wydarzenie na żywo.
8. Zapisz identyfikator zasobu adresu URL przesyłania strumieniowego na żywo archiwum i ustawień i wersja używanego z koder na żywo opublikowane.

Na koniec swoje zarejestrowane ustawienia poczty e-mail i na żywo parametry archiwum usługi Azure Media Services na amsstreaming@microsoft.com jako powiadomienie, które przeszły wszystkie testy weryfikacyjne opisane samooceny. Także informacji kontaktowych w dowolnym ups postępuj zgodnie z. Możesz skontaktować się z zespołu usługi Azure Media Services za pomocą jakieś pytania dotyczące tego procesu.

## <a name="next-steps"></a>Kolejne kroki

[Transmisja strumieniowa za pomocą usługi Media Services v3 na żywo](live-streaming-overview.md)
