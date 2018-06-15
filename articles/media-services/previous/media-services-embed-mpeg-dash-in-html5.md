---
title: Osadzania wideo adaptacyjnego przesyłania strumieniowego MPEG-DASH w aplikacji HTML5 z DASH.js | Dokumentacja firmy Microsoft
description: W tym temacie przedstawiono sposób osadzić w aplikacji HTML5 z DASH.js MPEG-DASH adaptacyjne przesyłanie strumieniowe wideo.
author: Juliako
manager: cfowler
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: 5aa0e7b6-f5c3-4cc1-aa33-ed16ea4780c2
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/26/2016
ms.author: juliako
ms.openlocfilehash: 2b0e6bf643f55e1809b29def7766c58b59f4bb50
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
ms.locfileid: "33788634"
---
# <a name="embedding-an-mpeg-dash-adaptive-streaming-video-in-an-html5-application-with-dashjs"></a>Osadzanie adaptacyjną wideo przesyłania strumieniowego MPEG-DASH aplikacji HTML5 z DASH.js
## <a name="overview"></a>Przegląd
MPEG-DASH jest standardem ISO do adaptacyjnego przesyłania strumieniowego zawartości wideo, która oferuje istotne korzyści dla deweloperów, która pragnie dostarczania wideo wysokiej jakości, adaptacyjne przesyłanie strumieniowe danych wyjściowych. Z MPEG-DASH strumienia wideo spowoduje automatyczne dopasowanie do dolnej definicji gdy sieć staje się przeciążona. Zmniejsza to prawdopodobieństwo podglądu wyświetlanie "Wstrzymano" wideo, gdy odtwarzacz pobiera dalej kilka sekund, aby odtworzyć (alias buforowania). Ponieważ zmniejsza przeciążenie sieci, odtwarzacza wideo z kolei powróci do strumienia wyższej jakości. Możliwość dostosowania przepustowość wymagana powoduje również skrócić czas rozpoczęcia wideo. Oznacza to pierwsze sekundy można odtworzyć w segmencie jakości niższe fast do pobierania, a następnie krok do wyższej jakości raz wystarczającej zawartości została buforowana.

Dash.js jest open source MPEG-DASH odtwarzacza wideo napisane w języku JavaScript. Jego celem jest zapewnienie odtwarzacz niezawodne, między platformami, który może nastąpić za darmo w aplikacjach, które wymagają odtwarzania wideo. Zapewnia odtwarzanie MPEG-DASH w dowolnej przeglądarce, która obsługuje obecnie W3C nośnika źródłowego rozszerzenia (MSE) Chrome, Microsoft Edge i IE11 (innych przeglądarek wskazał zamiaru obsługuje MSE). Aby uzyskać więcej informacji na temat DASH.js js Zobacz dash.js repozytorium GitHub.

## <a name="creating-a-browser-based-streaming-video-player"></a>Tworzenie przeglądarki przesyłania strumieniowego odtwarzacza wideo
Aby utworzyć prostą stronę sieci web, który wyświetla odtwarzacza wideo z oczekiwanym formanty takie play, Wstrzymaj, przewiń itp., musisz:

1. Tworzenie strony HTML
2. Dodaj tag wideo
3. Dodaj dash.js player
4. Inicjowanie Windows Media player
5. Dodaj niektórych stylu CSS
6. Przejrzyj wyniki w przeglądarce, która implementuje MSE

Inicjowanie odtwarzacz mogą być wykonywane w kilku wierszach kodu JavaScript. Przy użyciu dash.js, naprawdę jest proste do osadzania wideo MPEG-DASH w aplikacjach opartych na przeglądarce.

## <a name="creating-the-html-page"></a>Tworzenie strony HTML
Pierwszym krokiem jest utworzenie standardowego zawierający HTML strony **wideo** elementu, Zapisz ten plik jako basicPlayer.html, jak w poniższym przykładzie przedstawiono:

```html
    <!DOCTYPE html>
    <html>
      <head><title>Adaptive Streaming in HTML5</title></head>
      <body>
        <h1>Adaptive Streaming with HTML5</h1>
        <video id="videoplayer" controls></video>
      </body>
    </html>
```

## <a name="adding-the-dashjs-player"></a>Dodawanie DASH.js Player
Aby dodać dash.js implementacji odwołania do aplikacji, należy do pobrania pliku dash.all.js z wersji 1.0 dash.js projektu. To ma zostać zapisany w folderze JavaScript aplikacji. Ten plik jest plikiem wygody, ściągający kod dash.js niezbędne do jednego pliku w razem. Jeśli masz wyszukiwania wokół repozytorium dash.js można znaleźć pojedyncze pliki, testowania kodu i wiele innych, ale jeśli wszystkie chcesz zrobić jest użyć dash.js, plik dash.all.js jest, co jest potrzebne.

Aby dodać dash.js player do aplikacji, należy dodać tag skryptu do sekcji head basicPlayer.html:

```html
    <!-- DASH-AVC/265 reference implementation -->
    < script src="js/dash.all.js"></script>
```

Następnie Utwórz funkcję, aby zainicjować odtwarzacz podczas ładowania strony. Dodaj poniższy skrypt po wierszu załadować dash.all.js:

```html
    <script>
    // setup the video element and attach it to the Dash player
    function setupVideo() {
      var url = "http://wams.edgesuite.net/media/MPTExpressionData02/BigBuckBunny_1080p24_IYUV_2ch.ism/manifest(format=mpd-time-csf)";
      var context = new Dash.di.DashContext();
      var player = new MediaPlayer(context);
                      player.startup();
                      player.attachView(document.querySelector("#videoplayer"));
                      player.attachSource(url);
    }
    </script>
```

Ta funkcja najpierw tworzy DashContext. Służy do konfigurowania aplikacji dla środowiska wykonawczego specyficzne. Z technicznego punktu widzenia definiuje klasy, które framework iniekcji zależności należy używać podczas tworzenia aplikacji. W większości przypadków należy użyć Dash.di.DashContext.

Następnie można utworzyć wystąpienia klasy podstawowej struktury dash.js MediaPlayer. Ta klasa zawiera podstawowe metody takie jak odtwarzanie i wstrzymywanie, zarządza relacji z elementem wideo i zarządza także interpretacji pliku opisu prezentacji nośnika (MPD), który opisuje wideo do odtworzenia.

Aby upewnić się, że odtwarzacz jest gotowy do odtwarzania wideo wywołania funkcji startup() klasy MediaPlayer. Między innymi funkcji gwarantuje, że wszystkie niezbędne klasy (zgodnie z definicją w kontekście) zostały załadowane. Gdy odtwarzacz jest gotowy, można dołączyć elementu wideo do niej przy użyciu funkcji attachView(). Funkcja startowa umożliwia MediaPlayer iniekcję strumienia wideo do elementu i również sterowanie odtwarzania zgodnie z potrzebami.

Przekazać adres URL pliku MPD do MediaPlayer, dzięki czemu wie o wideo, że oczekuje się, aby odtworzyć. Funkcja setupVideo() właśnie utworzony musi być wykonywane po stronie pełni został załadowany. W tym celu za pomocą tego zdarzenia onload elementu body. Zmień Twoje <body> elementu:

```html
    <body onload="setupVideo()">
```

Wreszcie Ustaw rozmiar elementu wideo, używając CSS. W środowisku adaptacyjnego przesyłania strumieniowego jest to szczególnie ważne, ponieważ rozmiar odtwarzania wideo może zmienić odtwarzania dostosowuje się do zmieniających się warunków sieciowych. W tym prosty pokaz po prostu wymusić wideo elementu 80% okna przeglądarki dostępne przez dodanie następujących CSS do sekcji head strony:

```html
    <style>
    video {
      width: 80%;
      height: 80%;
    }
    </style>
```

## <a name="playing-a-video"></a>Odtwarzanie wideo
Odtwarzanie wideo, wskazać w przeglądarce z plikiem basicPlayback.html i kliknij przycisk play na odtwarzacza wideo wyświetlane.

## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Zobacz też
[Opracowywanie aplikacji odtwarzacza wideo](media-services-develop-video-players.md)

[Repozytorium GitHub dash.js](https://github.com/Dash-Industry-Forum/dash.js) 

