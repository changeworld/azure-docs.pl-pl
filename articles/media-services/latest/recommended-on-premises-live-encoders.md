---
title: Kodery przesyłania strumieniowego na żywo zalecane przez Media Services — Azure | Microsoft Docs
description: Dowiedz się więcej o koderach lokalnych dotyczących przesyłania strumieniowego na żywo zalecanych przez Media Services
services: media-services
keywords: encoding;encoders;media
author: johndeu
manager: johndeu
ms.author: johndeu
ms.date: 02/10/2020
ms.topic: article
ms.service: media-services
ms.openlocfilehash: aa9cd3f642e3d8047e8b64afb023fffb7bd2c4f6
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/20/2020
ms.locfileid: "77484914"
---
# <a name="recommended-live-streaming-encoders"></a>Zalecane kodery przesyłania strumieniowego na żywo

W Azure Media Services wydarzenie na [żywo](https://docs.microsoft.com/rest/api/media/liveevents) (kanał) reprezentuje potok do przetwarzania zawartości przesyłania strumieniowego na żywo. Zdarzenie na żywo odbiera strumienie wejściowe na żywo na jeden z dwóch sposobów.

* Lokalny koder na żywo wysyła strumień RTMP o dużej szybkości transmisji bitów lub Smooth Streaming (pofragmentowany plik MP4) do zdarzenia na żywo, które nie ma włączonej obsługi kodowania na żywo z Media Services. Pozyskiwane strumienie przechodzą przez zdarzenia na żywo bez dalszej obróbki. Ta metoda jest nazywana **przekazywaniem**. Zalecamy, aby koder na żywo wysyłał strumienie o szybkości transmisji bitów zamiast strumienia o pojedynczej szybkości transmisji bitów do zdarzenia przekazywania na żywo, aby umożliwić przesyłanie strumieniowe transmisji bitów do klienta. 

    Jeśli używasz strumieni o różnej szybkości transmisji bitów dla zdarzenia przekazywania na żywo, rozmiar grupę GOP wideo i fragmenty wideo na różnych szybkościach transmisji bitów muszą być zsynchronizowane, aby uniknąć nieoczekiwanych zachowań po stronie odtwarzania.

  > [!NOTE]
  > Użycie metody przekazującej to najbardziej ekonomiczny sposób na przesyłanie strumieniowe na żywo.
 
* Lokalny koder na żywo wysyła strumień o pojedynczej szybkości transmisji bitów do zdarzenia na żywo, które umożliwia wykonywanie kodowania na żywo za pomocą Media Services w jednym z następujących formatów: RTMP lub Smooth Streaming (fragmentacja MP4). Zdarzenie na żywo następnie wykonuje kodowanie na żywo przychodzącego strumienia o pojedynczej szybkości transmisji bitów do strumienia wideo o wysokiej szybkości transmisji bitów (adaptacyjnej).

Aby uzyskać szczegółowe informacje na temat kodowania na żywo z Media Services, zobacz [przesyłanie strumieniowe na żywo z Media Services v3](live-streaming-overview.md).

## <a name="encoder-requirements"></a>Wymagania dotyczące kodera

Kodery muszą obsługiwać protokół TLS 1,2 w przypadku używania protokołów HTTPS lub RTMP.

## <a name="live-encoders-that-output-rtmp"></a>Kodery dynamiczne, które wyprowadzają dane RTMP

Usługa Media Services zaleca używanie jednego z następujących koderów na żywo, które jako dane wyjściowe mają pliki RTMP. Obsługiwane schematy adresów URL to `rtmp://` lub `rtmps://`.

Podczas przesyłania strumieniowego za pośrednictwem protokołu RTMP sprawdź ustawienia zapory i/lub serwera proxy, aby upewnić się, że porty TCP ruchu wychodzącego 1935 i 1936 są otwarte.<br/><br/>
Podczas przesyłania strumieniowego za pośrednictwem protokołu RTMPS sprawdź ustawienia zapory i/lub serwera proxy, aby upewnić się, że porty TCP ruchu wychodzącego 2935 i 2936 są otwarte.

> [!NOTE]
> Kodery muszą obsługiwać protokół TLS 1,2 w przypadku używania protokołów RTMP.

- Adobe Flash Media Live Encoder 3.2
- [Cambria na żywo 4,3](https://www.capellasystems.net/products/cambria-live/)
- Element aktywny (wersja 2.14.15 i nowsze)
- Haivision KB
- Haivision Makito X HEVC
- OBS Studio
- Switcher Studio (iOS)
- Wirecast Telestream (wersja 13.0.2 lub nowsza ze względu na wymaganie TLS 1,2)
- Wirecast telestrumienia S (obsługiwane są tylko RTMP)
- Teradek Slice 756
- TriCaster 8000
- Tricaster Mini HD-4
- VMIX
- xStream
- [Narzędzia FFmpeg](https://www.ffmpeg.org)
- [GoPro](https://gopro.com/help/articles/block/getting-started-with-live-streaming) Hero 7 i Hero 8
- [Restream.io](https://restream.io/)

## <a name="live-encoders-that-output-fragmented-mp4"></a>Kodery dynamiczne, które wyprowadzają pofragmentowany plik MP4

Media Services zaleca użycie jednego z następujących koderów na żywo, które mają Smooth Streaming o wielu szybkościach transmisji bitów (fragmentacja MP4) jako dane wyjściowe. Obsługiwane schematy adresów URL to `http://` lub `https://`.

> [!NOTE]
> Kodery muszą obsługiwać protokół TLS 1,2 w przypadku używania protokołów HTTPS.

- Ateme TITAN Live
- Cisco Digital Media Encoder 2200
- Element aktywny (wersja 2.14.15 i nowsza ze względu na wymaganie TLS 1,2)
- Envivio 4Caster C4 Gen III 
- Wyobraź sobie komunikację Selenio MCP3
- Media Excel Hero Live i Hero 4K (UHD/HEVC)
- [Narzędzia FFmpeg](https://www.ffmpeg.org)

> [!TIP]
>  Jeśli przesyłasz strumieniowo wydarzenia na żywo w wielu językach (na przykład jedną angielską ścieżką audio i jedną Hiszpańska ścieżką audio), możesz to zrobić za pomocą kodera programu Excel Live Encoder skonfigurowanego do wysyłania kanału informacyjnego na żywo do zdarzenia przekazywania na żywo.

## <a name="configuring-on-premises-live-encoder-settings"></a>Konfigurowanie ustawień lokalnego kodera na żywo

Aby uzyskać informacje o ustawieniach, które są prawidłowe dla typu zdarzenia na żywo, zobacz [Porównanie typów zdarzeń na żywo](live-event-types-comparison.md).

### <a name="playback-requirements"></a>Wymagania dotyczące odtwarzania

Aby odtworzyć zawartość, musi być obecny strumień audio i wideo. Odtwarzanie strumienia tylko wideo nie jest obsługiwane.

### <a name="configuration-tips"></a>Wskazówki dotyczące konfiguracji

- Jeśli to możliwe, przy użyciu połączenia internetowego hardwired.
- Podczas określania wymagań dotyczących przepustowości należy zwiększyć szybkość transmisji strumieniowej. Chociaż nie jest to wymagane, prosta reguła pomaga ograniczyć wpływ przeciążenia sieci.
- Podczas korzystania z koderów opartych na oprogramowaniu, zamknąć wszystkie zbędne programy.
- Zmiana konfiguracji kodera po rozpoczęciu wypychania ma negatywny wpływ na zdarzenie. Zmiany konfiguracji mogą spowodować niestabilność zdarzenia. 
- Upewnij się, że podajesz sobie dużo czasu, aby skonfigurować wydarzenie. W przypadku zdarzeń o dużej skali zalecamy uruchomienie Instalatora godzinę przed wydarzeniem.

## <a name="becoming-an-on-premises-encoder-partner"></a>Zostań partnerem z lokalnym koderem

Jako Azure Media Services lokalnego partnera kodera Media Services promować produkt, zalecając koder do klientów korporacyjnych. Aby móc pełnić rolę lokalnego partnera kodera, należy sprawdzić zgodność kodera lokalnego z Media Services. Aby to zrobić, wykonaj następujące weryfikacje.

### <a name="pass-through-live-event-verification"></a>Weryfikacja zdarzenia przekazującego na żywo

1. Na koncie Media Services upewnij się, że **punkt końcowy przesyłania strumieniowego** jest uruchomiony. 
2. Utwórz i uruchom wydarzenie **Pass-through** Live. <br/> Aby uzyskać więcej informacji, zobacz [Live Event states and billing](live-event-states-billing.md) (Stany i rozliczenia dotyczące wydarzenia na żywo).
3. Pobierz adresy URL pozyskiwania i skonfiguruj koder lokalny, aby używać adresu URL do wysyłania strumienia na żywo o wysokiej szybkości transmisji bitów do Media Services.
4. Uzyskaj adres URL wersji zapoznawczej i użyj go do sprawdzenia, czy dane wejściowe kodera są faktycznie odbierane.
5. Utwórz nowy obiekt **zasobów** .
6. Utwórz na **żywo wyjście** i użyj utworzonej nazwy zasobu.
7. Utwórz **lokalizator przesyłania strumieniowego** z wbudowanymi typami **zasad przesyłania strumieniowego** .
8. Wyświetl listę ścieżek w **lokalizatorze przesyłania strumieniowego** , aby odzyskać adresy URL do użycia.
9. Pobierz nazwę hosta dla **punktu końcowego przesyłania strumieniowego** , z którego chcesz przesyłać strumieniowo.
10. Połącz adres URL z kroku 8 z nazwą hosta w kroku 9, aby uzyskać pełny adres URL.
11. Uruchom koder na żywo przez około 10 minut.
12. Zatrzymaj wydarzenie na żywo. 
13. Użyj odtwarzacza, takiego jak [Azure Media Player](https://aka.ms/azuremediaplayer) , aby obejrzeć zarchiwizowany element zawartości, aby upewnić się, że odtwarzanie nie będzie widoczne na wszystkich poziomach jakości. Lub Obejrzyj i sprawdź poprawność przy użyciu adresu URL wersji zapoznawczej w trakcie sesji na żywo.
14. Zapisz identyfikator zasobu, opublikowany adres URL przesyłania strumieniowego dla archiwum na żywo oraz ustawienia i wersję używane z kodera na żywo.
15. Zresetuj stan zdarzenia na żywo po utworzeniu każdego przykładu.
16. Powtórz kroki od 5 do 15 dla wszystkich konfiguracji obsługiwanych przez koder (z niemniemi, napisami lub innymi szybkościami kodowania, a także bez nich).

### <a name="live-encoding-live-event-verification"></a>Weryfikacja zdarzeń na żywo w kodowaniu na żywo

1. Na koncie Media Services upewnij się, że **punkt końcowy przesyłania strumieniowego** jest uruchomiony. 
2. Utwórz i uruchom wydarzenie Live **Encoding** na żywo. <br/> Aby uzyskać więcej informacji, zobacz [Live Event states and billing](live-event-states-billing.md) (Stany i rozliczenia dotyczące wydarzenia na żywo).
3. Pobierz adresy URL pozyskiwania i skonfiguruj koder, aby wypchnąć strumień na żywo o pojedynczej szybkości transmisji bitów do Media Services.
4. Uzyskaj adres URL wersji zapoznawczej i użyj go do sprawdzenia, czy dane wejściowe kodera są faktycznie odbierane.
5. Utwórz nowy obiekt **zasobów** .
6. Utwórz na **żywo wyjście** i użyj utworzonej nazwy zasobu.
7. Utwórz **lokalizator przesyłania strumieniowego** z wbudowanymi typami **zasad przesyłania strumieniowego** .
8. Wyświetl listę ścieżek w **lokalizatorze przesyłania strumieniowego** , aby odzyskać adresy URL do użycia.
9. Pobierz nazwę hosta dla **punktu końcowego przesyłania strumieniowego** , z którego chcesz przesyłać strumieniowo.
10. Połącz adres URL z kroku 8 z nazwą hosta w kroku 9, aby uzyskać pełny adres URL.
11. Uruchom koder na żywo przez około 10 minut.
12. Zatrzymaj wydarzenie na żywo.
13. Użyj odtwarzacza, takiego jak [Azure Media Player](https://aka.ms/azuremediaplayer) , aby obejrzeć zarchiwizowany element zawartości, aby upewnić się, że odtwarzanie nie ma widocznych żadnych błędów dla wszystkich poziomów jakości. Lub Obejrzyj i sprawdź poprawność przy użyciu adresu URL wersji zapoznawczej w trakcie sesji na żywo.
14. Zapisz identyfikator zasobu, opublikowany adres URL przesyłania strumieniowego dla archiwum na żywo oraz ustawienia i wersję używane z kodera na żywo.
15. Zresetuj stan zdarzenia na żywo po utworzeniu każdego przykładu.
16. Powtórz kroki od 5 do 15 dla wszystkich konfiguracji obsługiwanych przez koder (z niemniemi, napisami lub innymi szybkościami kodowania, a także bez nich).

### <a name="longevity-verification"></a>Weryfikacja eksploatacji

Wykonaj te same czynności co w przypadku [weryfikacji zdarzenia](#pass-through-live-event-verification) dotyczącego przekazywania na żywo z wyjątkiem kroku 11. <br/>Zamiast 10 minut Uruchom koder na żywo przez jeden tydzień lub dłużej. Użyj odtwarzacza, takiego jak [Azure Media Player](https://aka.ms/azuremediaplayer) , aby obserwować przesyłanie strumieniowe na żywo od czasu do czasu (lub zarchiwizowanego zasobu), aby upewnić się, że odtwarzanie nie ma widocznych błędów.

### <a name="email-your-recorded-settings"></a>Wyślij zapisane ustawienia pocztą e-mail

Na koniec Wyślij zapisane ustawienia i parametry Archiwum na żywo, aby Azure Media Services w amshelp@microsoft.com jako powiadomienie, że wszystkie testy samoweryfikacji zostały zakończone pomyślnie. Podaj również informacje kontaktowe dla każdej z nich. Możesz skontaktować się z zespołem Azure Media Services, podając wszelkie pytania dotyczące tego procesu.

## <a name="next-steps"></a>Następne kroki

[Przesyłanie strumieniowe na żywo z Media Services v3](live-streaming-overview.md)
