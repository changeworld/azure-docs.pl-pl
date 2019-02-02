---
title: Dowiedz się więcej o koderów na żywo transmisji strumieniowej w środowisku lokalnym zalecane przez usługę Media Services — Azure | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o zalecanych przez usługę Media Services na żywo transmisji strumieniowej kodery lokalne
services: media-services
keywords: encoding;encoders;media
author: johndeu
manager: johndeu
ms.author: johndeu
ms.date: 01/17/2019
ms.topic: article
ms.service: media-services
ms.openlocfilehash: c3e42ba9fe84ded8c60fc71f19de785945852116
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/02/2019
ms.locfileid: "55656672"
---
# <a name="recommended-live-streaming-encoders"></a>Zalecane kodery transmisji strumieniowej na żywo

W usłudze Media Services [wydarzenie na żywo](https://docs.microsoft.com/rest/api/media/liveevents) (kanał) reprezentuje potok przetwarzania zawartości transmisji strumieniowej na żywo. Wydarzenie na żywo odbiera strumieni danych wejściowych w jeden z dwóch sposobów:

* Koder na żywo w środowisku lokalnym wysyła różnych szybkościach transmisji bitów RTMP lub Smooth Streaming (pofragmentowany MP4) strumienia do wydarzenie na żywo, która nie jest włączona kodowanie na żywo za pomocą usługi Media Services. Pozyskiwane strumienie są przekazywane za pośrednictwem zdarzenia na żywo bez dalszego przetwarzania. Ta metoda jest wywoływana **przekazywanego**. Koder na żywo może wysyłać strumień z jedną szybkością transmisji bitów do przebiegu za pośrednictwem kanału, ale ta konfiguracja nie jest zalecane, ponieważ nie zezwala na adaptacyjną szybkością transmisji bitów, przesyłanie strumieniowe do klienta.

  > [!NOTE]
  > Metoda przekazywania to najbardziej ekonomiczny sposób transmisja strumieniowa na żywo.

* Lokalny koder na żywo wysyła strumień o pojedynczej szybkości bitów do wydarzenie na żywo, który jest skonfigurowany do przeprowadzania kodowania na żywo z usługą Media Services w jednym z następujących formatów: RTMP lub Smooth Streaming (pofragmentowany MP4). Wydarzenie na żywo wykonuje następnie kodowanie na żywo przychodzącego strumienia pojedynczej do wielokrotnej szybkości transmisji bitów (adaptacyjnej) strumienia wideo.

Aby uzyskać szczegółowe informacje na temat kodowania na żywo za pomocą usługi Media Services, zobacz [na żywo, przesyłanie strumieniowe przy użyciu usługi Media Services v3](live-streaming-overview.md).

## <a name="live-encoders-that-output-rtmp"></a>Dane wyjściowe RTMP koderów na żywo

Usługa Media Services zaleca się przy użyciu jednej z następujących koderów na żywo, które mają RTMP jako dane wyjściowe. Obsługiwane schematy adresów URL są `rtmp://` lub `rtmps://`.

> [!NOTE]
 > Podczas przesyłania strumieniowego za pośrednictwem protokołu RTMP, sprawdź ustawienia zapory i/lub serwer proxy, aby upewnić się, że porty TCP ruchu wychodzącego 1935 i 1936 są otwarte.<br/>
 Podczas przesyłania strumieniowego za pośrednictwem RTMPS, sprawdź ustawienia zapory i/lub serwer proxy, aby upewnić się, że porty TCP ruchu wychodzącego 2935 i 2936 są otwarte.

- Adobe Flash Media Live Encoder 3.2
- Haivision KB
- Haivision Makito X HEVC
- OBS Studio
- Przełącznik Studio (iOS)
- Telestream Wirecast 8.1 +
- Telestream Wirecast S
- Teradek Slice 756
- TriCaster 8000
- HD Tricaster Mini-4
- VMIX
- xStream

## <a name="live-encoders-that-output-fragmented-mp4"></a>Dane wyjściowe pofragmentowany plik MP4 koderów na żywo

Usługa Media Services zaleca się przy użyciu jednej z następujących koderów na żywo, które mają różnych szybkościach transmisji bitów Smooth Streaming (pofragmentowany plik MP4) jako dane wyjściowe. Obsługiwane schematy adresów URL są `http://` lub `https://`.

- Ateme TITAN Live
- Kodera multimediów cyfrowych Cisco 2200
- Elemental Live
- Envivio 4Caster C4 ogólnego III
- Imagine Communications Selenio MCP3
- Nośnik w programie Excel Hero na żywo i Hero 4K (UHD / — HEVC)

## <a name="configuring-on-premises-live-encoder-settings"></a>Konfigurowanie lokalnych ustawień kodera na żywo

Aby uzyskać informacje o jakie ustawienia są prawidłowe dla danego typu zdarzenia na żywo, zobacz [wydarzenie na żywo typy porównania](live-event-types-comparison.md).

### <a name="playback-requirements"></a>Wymagania odtwarzania

Zarówno strumień audio i wideo musi być obecny w kolejności do odtwarzania zawartości. Odtwarzanie strumienia tylko do filmu wideo nie jest obsługiwane.

### <a name="configuration-tips"></a>Porady dotyczące konfiguracji

- Jeśli to możliwe, przy użyciu połączenia internetowego hardwired.
- Jest regułą podczas określania wymagań dotyczących przepustowości dwukrotnie różnych przesyłania strumieniowego. Chociaż nie jest to wymagane, może to pomóc, łagodzenia skutków przeciążenie sieci.
- Gdy za pomocą oprogramowania na podstawie koderów, zamknąć wszystkie zbędne programy.
- Nie należy zmieniać konfiguracji kodera, po rozpoczęciu wypychania. Ma negatywny wpływ na zdarzenia i może spowodować, że zdarzenie, aby być niestabilny. 
- Upewnij się, aby przyznać sobie wystarczającą ilość czasu, aby skonfigurować zdarzenia. W przypadku zdarzeń dużej skali, jego zalecane jest, aby uruchomić Instalatora na godzinę przed zdarzenia.

## <a name="how-to-become-an-on-premises-encoder-partner"></a>Jak zostać partnerem kodera w środowisku lokalnym

Jako partner usługi Azure Media Services Encoder w warstwie lokalnej usługi Media Services wspiera produktu przez polecanie koder klientom korporacyjnym. Aby zostać partnerem kodera w środowisku lokalnym, należy sprawdzić zgodność koder w środowisku lokalnym za pomocą usługi Media Services. Aby to zrobić, wykonaj następujące weryfikacji:

### <a name="pass-through-live-event-verification"></a>Weryfikacja wydarzenie na żywo przekazywania

1. Upewnij się, w ramach konta usługi Media Services **punkt końcowy przesyłania strumieniowego** jest uruchomiona. 
2. Tworzenie i uruchamianie **przekazywanego** wydarzenie na żywo. <br/> Aby uzyskać więcej informacji, zobacz [stany wydarzenie na żywo i rozliczenia](live-event-states-billing.md).
3. Uzyskaj adresy URL pozyskiwania i skonfigurować koder środowiska lokalnego do używania adresu URL do wysyłania strumienia na żywo o różnych szybkościach transmisji bitów do usługi Media Services.
4. Adres URL (wersja zapoznawcza) i weryfikować, czy rzeczywiście są odbierane dane wejściowe z kodera.
5. Utwórz nową **zasobów** obiektu.
6. Tworzenie **na żywo dane wyjściowe** i użyj nazwy zasobu, który został utworzony.
7. Tworzenie **lokalizatora przesyłania strumieniowego** dzięki wbudowanej **przesyłania strumieniowego zasad** typów.
8. Wyświetlanie listy ścieżek na **lokalizatora przesyłania strumieniowego** odzyskać adresy URL do użycia.
9. Pobieranie nazwy hosta dla **punkt końcowy przesyłania strumieniowego** chcesz przesyłać strumieniowo z.
10. Adres URL w kroku 8 należy połączyć z nazwą hosta w kroku 9, aby uzyskać pełny adres URL.
11. Uruchom koder na żywo przez około 10 minut.
12. Zatrzymaj wydarzenie na żywo. 
13. Używanie odtwarzacz, takiej jak [usługi Azure Media Player](https://ampdemo.azureedge.net/azuremediaplayer.html) Obejrzyj zarchiwizowanego elementu zawartości to upewnić się, że odtwarzanie nie występującymi widoczne dla wszystkich poziomów jakości (lub też oglądanie i zweryfikować za pośrednictwem adresu URL w wersji zapoznawczej podczas sesji na żywo).
14. Zapisz identyfikator zasobu adresu URL przesyłania strumieniowego na żywo archiwum i ustawień i wersja używanego z koder na żywo opublikowane.
15. Resetowanie stanu wydarzenie na żywo po utworzeniu każdej próbki.
16. Powtórz kroki od 5 do 15 w przypadku wszystkich konfiguracji obsługiwanych przez koder (z lub bez ad sygnalizowanie/napisów/inną kodowanie szybkości).

### <a name="live-encoding-live-event-verification"></a>Na żywo kodowania weryfikacji wydarzenie na żywo

1. Upewnij się, w ramach konta usługi Media Services **punkt końcowy przesyłania strumieniowego** jest uruchomiona. 
2. Tworzenie i uruchamianie **kodowanie na żywo** wydarzenie na żywo. <br/> Aby uzyskać więcej informacji, zobacz [stany wydarzenie na żywo i rozliczenia](live-event-states-billing.md).
3. Uzyskaj adresy URL pozyskiwania i skonfigurować koder wypychanie strumienia na żywo jedną szybkością transmisji bitów do usługi Media Services.
4. Adres URL (wersja zapoznawcza) i weryfikować, czy rzeczywiście są odbierane dane wejściowe z kodera.
5. Utwórz nową **zasobów** obiektu.
6. Tworzenie **na żywo dane wyjściowe** i użyj nazwy zasobu, który został utworzony.
7. Tworzenie **lokalizatora przesyłania strumieniowego** dzięki wbudowanej **przesyłania strumieniowego zasad** typów.
8. Wyświetlanie listy ścieżek na **lokalizatora przesyłania strumieniowego** odzyskać adresy URL do użycia.
9. Pobieranie nazwy hosta dla **punkt końcowy przesyłania strumieniowego** chcesz przesyłać strumieniowo z.
10. Adres URL w kroku 8 należy połączyć z nazwą hosta w kroku 9, aby uzyskać pełny adres URL.
11. Uruchom koder na żywo przez około 10 minut.
12. Zatrzymaj wydarzenie na żywo.
13. Używanie odtwarzacz, takiej jak [usługi Azure Media Player](https://ampdemo.azureedge.net/azuremediaplayer.html) Obejrzyj zarchiwizowanego elementu zawartości to upewnić się, że odtwarzanie nie występującymi widoczne dla wszystkich poziomów jakości (lub też oglądanie i zweryfikować za pośrednictwem adresu URL w wersji zapoznawczej podczas sesji na żywo).
14. Zapisz identyfikator zasobu adresu URL przesyłania strumieniowego na żywo archiwum i ustawień i wersja używanego z koder na żywo opublikowane.
15. Resetowanie stanu wydarzenie na żywo po utworzeniu każdej próbki.
16. Powtórz kroki od 5 do 15 w przypadku wszystkich konfiguracji obsługiwanych przez koder (z lub bez ad sygnalizowanie/napisów/inną kodowanie szybkości).

### <a name="longevity-verification"></a>Weryfikacja trwałość

Takie same kroki co [weryfikacji wydarzenie na żywo przekazywanego](#pass-through-live-event-verification) z wyjątkiem krok 11. <br/>Zamiast 10 minut Uruchom koder na żywo przez jednego tygodnia lub dłużej. Używanie odtwarzacz, takiej jak [usługi Azure Media Player](https://ampdemo.azureedge.net/azuremediaplayer.html) obejrzeć transmisji strumieniowych na żywo od czasu do czasu (lub zarchiwizowanego elementu zawartości) to upewnić się, że odtwarzanie nie występującymi widoczne.

### <a name="email-your-recorded-settings"></a>Swoje zarejestrowane ustawienia poczty e-mail

Na koniec swoje zarejestrowane ustawienia poczty e-mail i na żywo parametry archiwum usługi Azure Media Services na amsstreaming@microsoft.com jako powiadomienie, które przeszły wszystkie testy weryfikacyjne opisane samooceny. Także informacji kontaktowych w dowolnym ups postępuj zgodnie z. Możesz skontaktować się z zespołu usługi Azure Media Services za pomocą jakieś pytania dotyczące tego procesu.

## <a name="next-steps"></a>Kolejne kroki

[Transmisja strumieniowa za pomocą usługi Media Services v3 na żywo](live-streaming-overview.md)
