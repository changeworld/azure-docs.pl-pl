---
title: Strumieniowe na żywo koderów zalecane przez usługę Media Services — Azure | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o zalecanych przez usługę Media Services na żywo transmisji strumieniowej kodery lokalne
services: media-services
keywords: encoding;encoders;media
author: johndeu
manager: johndeu
ms.author: johndeu
ms.date: 06/12/2019
ms.topic: article
ms.service: media-services
ms.openlocfilehash: 1bede8ff7c33988a4e11a84d3a51ca098a608f33
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67055279"
---
# <a name="recommended-live-streaming-encoders"></a>Zalecane kodery transmisji strumieniowej na żywo

W usłudze Azure Media Services [wydarzenie na żywo](https://docs.microsoft.com/rest/api/media/liveevents) (kanał) reprezentuje potok przetwarzania zawartości transmisji strumieniowej na żywo. Wydarzenie na żywo odbiera strumieni danych wejściowych w jeden z dwóch sposobów.

* Koder na żywo w środowisku lokalnym wysyła różnych szybkościach transmisji bitów RTMP lub Smooth Streaming (pofragmentowany MP4) strumienia do wydarzenie na żywo, która nie jest włączona kodowanie na żywo za pomocą usługi Media Services. Pozyskiwane strumienie są przekazywane za pośrednictwem zdarzenia na żywo bez dalszego przetwarzania. Ta metoda jest wywoływana **przekazywanego**. Koder na żywo może wysyłać strumień z jedną szybkością transmisji bitów do kanału przekazującego. Nie zaleca tej konfiguracji, ponieważ nie dopuszcza do adaptacyjną szybkością transmisji bitów, przesyłanie strumieniowe do klienta

  > [!NOTE]
  > Metoda przekazywania to najbardziej ekonomiczny sposób transmisja strumieniowa na żywo.
 
* Lokalny koder na żywo wysyła strumień o pojedynczej szybkości bitów do wydarzenie na żywo, który jest skonfigurowany do przeprowadzania kodowania na żywo z usługą Media Services w jednym z następujących formatów: RTMP lub Smooth Streaming (pofragmentowany MP4). Wydarzenie na żywo wykonuje następnie kodowanie na żywo przychodzącego strumienia pojedynczej do wielokrotnej szybkości transmisji bitów (adaptacyjnej) strumienia wideo.

Aby uzyskać szczegółowe informacje na temat kodowania na żywo za pomocą usługi Media Services, zobacz [na żywo, przesyłanie strumieniowe przy użyciu usługi Media Services v3](live-streaming-overview.md).

## <a name="live-encoders-that-output-rtmp"></a>Dane wyjściowe RTMP koderów na żywo

Usługa Media Services zaleca używanie jednego z następujących koderów na żywo, które jako dane wyjściowe mają pliki RTMP. Obsługiwane schematy adresów URL są `rtmp://` lub `rtmps://`.

> [!NOTE]
> Podczas przesyłania strumieniowego za pośrednictwem protokołu RTMP sprawdź ustawienia zapory i/lub serwera proxy, aby upewnić się, że porty TCP ruchu wychodzącego 1935 i 1936 są otwarte.

- Adobe Flash Media Live Encoder 3.2
- Haivision KB
- Haivision Makito X HEVC
- OBS Studio
- Switcher Studio (iOS)
- Telestream Wirecast 8.1+
- Telestream Wirecast S
- Teradek Slice 756
- TriCaster 8000
- Tricaster Mini HD-4
- VMIX
- xStream

## <a name="live-encoders-that-output-fragmented-mp4"></a>Koderów na żywo podzielonej zawartości w formacie MP4

Usługa Media Services zaleca się przy użyciu jednej z następujących koderów na żywo, które mają różnych szybkościach transmisji bitów Smooth Streaming (pofragmentowany MP4) jako dane wyjściowe. Obsługiwane schematy adresów URL są `http://` lub `https://`.

- Ateme TITAN Live
- Cisco Digital Media Encoder 2200
- Elemental Live
- Envivio 4Caster C4 Gen III
- Imagine Communications Selenio MCP3
- Media Excel Hero Live i Hero 4K (UHD/HEVC)

> [!TIP]
>  Jeśli są przesyłania strumieniowego wydarzeń na żywo w wielu językach, (na przykład jedną ścieżkę audio angielskiej i jedną ścieżkę audio hiszpańskim), można to zrobić za pomocą nośników w programie Excel koder na żywo, skonfigurować do wysyłania Kanał informacyjny na żywo do przekazywania zdarzenia na żywo.

## <a name="configuring-on-premises-live-encoder-settings"></a>Konfigurowanie lokalnych ustawień kodera na żywo

Aby uzyskać informacje o jakie ustawienia są prawidłowe dla danego typu zdarzenia na żywo, zobacz [wydarzenie na żywo typy porównania](live-event-types-comparison.md).

### <a name="playback-requirements"></a>Wymagania odtwarzania

Do odtwarzania zawartości, musi być obecny zarówno strumień audio i wideo. Odtwarzanie strumienia tylko do filmu wideo nie jest obsługiwane.

### <a name="configuration-tips"></a>Porady dotyczące konfiguracji

- Jeśli to możliwe, przy użyciu połączenia internetowego hardwired.
- Podczas określania, wymagania dotyczące przepustowości, double szybkości transmisji strumieniowych. Chociaż nie jest to konieczne, tę prostą zasadę pomaga złagodzić skutki przeciążenie sieci.
- Podczas korzystania z koderów opartych na oprogramowaniu, zamknąć wszystkie zbędne programy.
- Zmiana konfiguracji kodera, po jego uruchomieniu, wypychanie ma negatywny wpływ na zdarzenie. Zmiany w konfiguracji może powodować zdarzenie stanie się niestabilna. 
- Upewnij się, że należy przyznać sobie wystarczającą ilość czasu, aby skonfigurować zdarzenia. W przypadku zdarzeń o dużej skali zaleca się uruchamiania Instalatora, godzina, zanim zdarzenia.

## <a name="becoming-an-on-premises-encoder-partner"></a>Zostać partnerem kodera w środowisku lokalnym

Jako partner usługi Azure Media Services Encoder w warstwie lokalnej usługi Media Services wspiera produktu przez polecanie koder klientom korporacyjnym. Aby zostać partnerem kodera w środowisku lokalnym, należy sprawdzić zgodność koder w środowisku lokalnym za pomocą usługi Media Services. Aby to zrobić, wykonaj następujące weryfikacji.

### <a name="pass-through-live-event-verification"></a>Weryfikacja wydarzenie na żywo przekazywania

1. Upewnij się, że na Twoim koncie usługi Media Services **punkt końcowy przesyłania strumieniowego** jest uruchomiona. 
2. Tworzenie i uruchamianie **przekazywanego** wydarzenie na żywo. <br/> Aby uzyskać więcej informacji, zobacz [Live Event states and billing](live-event-states-billing.md) (Stany i rozliczenia dotyczące wydarzenia na żywo).
3. Uzyskiwanie adresów URL pozyskiwania i skonfiguruj swoje lokalny koder wysyłać strumień na żywo o różnych szybkościach transmisji bitów do usługi Media Services przy użyciu adresu URL.
4. Adres URL (wersja zapoznawcza) i weryfikować, czy rzeczywiście są odbierane dane wejściowe z kodera.
5. Utwórz nową **zasobów** obiektu.
6. Tworzenie **na żywo dane wyjściowe** i użyj nazwy zasobu, który został utworzony.
7. Tworzenie **lokalizatora przesyłania strumieniowego** dzięki wbudowanej **przesyłania strumieniowego zasad** typów.
8. Wyświetlanie listy ścieżek na **lokalizatora przesyłania strumieniowego** odzyskać adresy URL do użycia.
9. Pobierz nazwę hosta dla **punkt końcowy przesyłania strumieniowego** , którą chcesz strumieniowo przesyłać z.
10. Adres URL w kroku 8 należy połączyć z nazwy hosta określonej w kroku 9, aby uzyskać pełny adres URL.
11. Uruchom koder na żywo przez około 10 minut.
12. Zatrzymaj wydarzenie na żywo. 
13. Używanie odtwarzacz, takiej jak [usługi Azure Media Player](https://ampdemo.azureedge.net/azuremediaplayer.html) obejrzeć zarchiwizowanego elementu zawartości to upewnić się, że odtwarzanie nie widoczne występującymi na wszystkich poziomach jakości. Można także obejrzeć i zweryfikować za pośrednictwem adresu URL w wersji zapoznawczej podczas sesji na żywo.
14. Zapisz identyfikator zasobu opublikowanego adresu URL przesyłania strumieniowego na żywo archiwum i ustawień i wersja używanego z koder na żywo.
15. Resetowanie stanu wydarzenie na żywo po utworzeniu każdej próbki.
16. Powtórz kroki od 5 do 15 w przypadku wszystkich konfiguracji obsługiwanych przez koder (z lub bez sygnalizowanie ad, podpisów lub inną szybkość kodowania).

### <a name="live-encoding-live-event-verification"></a>Na żywo kodowania weryfikacji wydarzenie na żywo

1. Upewnij się, że na Twoim koncie usługi Media Services **punkt końcowy przesyłania strumieniowego** jest uruchomiona. 
2. Tworzenie i uruchamianie **kodowanie na żywo** wydarzenie na żywo. <br/> Aby uzyskać więcej informacji, zobacz [Live Event states and billing](live-event-states-billing.md) (Stany i rozliczenia dotyczące wydarzenia na żywo).
3. Uzyskiwanie adresów URL pozyskiwania i skonfigurować koder wypychanie strumienia na żywo jedną szybkością transmisji bitów do usługi Media Services.
4. Adres URL (wersja zapoznawcza) i weryfikować, czy rzeczywiście są odbierane dane wejściowe z kodera.
5. Utwórz nową **zasobów** obiektu.
6. Tworzenie **na żywo dane wyjściowe** i użyj nazwy zasobu, który został utworzony.
7. Tworzenie **lokalizatora przesyłania strumieniowego** dzięki wbudowanej **przesyłania strumieniowego zasad** typów.
8. Wyświetlanie listy ścieżek na **lokalizatora przesyłania strumieniowego** odzyskać adresy URL do użycia.
9. Pobierz nazwę hosta dla **punkt końcowy przesyłania strumieniowego** , którą chcesz strumieniowo przesyłać z.
10. Adres URL w kroku 8 należy połączyć z nazwy hosta określonej w kroku 9, aby uzyskać pełny adres URL.
11. Uruchom koder na żywo przez około 10 minut.
12. Zatrzymaj wydarzenie na żywo.
13. Używanie odtwarzacz, takiej jak [usługi Azure Media Player](https://ampdemo.azureedge.net/azuremediaplayer.html) obejrzeć zarchiwizowanego elementu zawartości to upewnić się, że odtwarzanie nie występującymi widoczne dla wszystkich poziomów jakości. Można także obejrzeć i zweryfikować za pośrednictwem adresu URL w wersji zapoznawczej podczas sesji na żywo.
14. Zapisz identyfikator zasobu opublikowanego adresu URL przesyłania strumieniowego na żywo archiwum i ustawień i wersja używanego z koder na żywo.
15. Resetowanie stanu wydarzenie na żywo po utworzeniu każdej próbki.
16. Powtórz kroki od 5 do 15 w przypadku wszystkich konfiguracji obsługiwanych przez koder (z lub bez sygnalizowanie ad, podpisów lub inną szybkość kodowania).

### <a name="longevity-verification"></a>Weryfikacja trwałość

Te same czynności co w [weryfikacji wydarzenie na żywo przekazywanego](#pass-through-live-event-verification) z wyjątkiem krok 11. <br/>Zamiast 10 minut Uruchom koder na żywo przez jednego tygodnia lub dłużej. Używanie odtwarzacz, takiej jak [usługi Azure Media Player](https://ampdemo.azureedge.net/azuremediaplayer.html) obejrzeć transmisji strumieniowych na żywo od czasu do czasu (lub zarchiwizowanego elementu zawartości) to upewnić się, że odtwarzanie nie występującymi widoczne.

### <a name="email-your-recorded-settings"></a>Swoje zarejestrowane ustawienia poczty e-mail

Na koniec swoje zarejestrowane ustawienia poczty e-mail i parametry archiwum usługi Azure Media Services na żywo amslived@microsoft.com jako powiadomienie, które przeszły wszystkie testy weryfikacyjne opisane samooceny. Także informacji kontaktowych w dowolnym przegapisz odpowiedzi członka. Możesz skontaktować się z zespołu usługi Azure Media Services za pomocą jakieś pytania dotyczące tego procesu.

## <a name="next-steps"></a>Kolejne kroki

[Transmisja strumieniowa za pomocą usługi Media Services v3 na żywo](live-streaming-overview.md)
