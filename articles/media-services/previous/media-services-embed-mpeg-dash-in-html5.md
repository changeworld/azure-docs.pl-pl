---
title: Osadzanie wideo adaptacyjnego przesyłania strumieniowego MPEG-DASH w aplikacji HTML5 z implementacją DASH.js | Dokumentacja firmy Microsoft
description: W tym temacie przedstawiono sposób osadzania MPEG-DASH adaptacyjnego przesyłania strumieniowego wideo w aplikacji HTML5 z implementacją DASH.js.
author: Juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: 5aa0e7b6-f5c3-4cc1-aa33-ed16ea4780c2
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: f521fd11a2053cf8cf1ea0f9f91667fe475f0eee
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61464202"
---
# <a name="embedding-an-mpeg-dash-adaptive-streaming-video-in-an-html5-application-with-dashjs"></a>Osadzanie MPEG-DASH adaptacyjne przesyłanie strumieniowe filmów wideo w aplikacji HTML5 z implementacją DASH.js  

## <a name="overview"></a>Omówienie
MPEG-DASH jest standardem ISO do adaptacyjnego przesyłania strumieniowego zawartości wideo, która oferuje istotne korzyści dla deweloperów, które chcą dostarczanie wideo wysokiej jakości, adaptacyjne przesyłanie strumieniowe danych wyjściowych. Przy użyciu standardu MPEG-DASH strumienia wideo dopasowuje automatycznie niższych definicji podczas staje się przeciążenia sieci. Zmniejsza to prawdopodobieństwo podglądu oglądanie wideo "wstrzymane", gdy gracz pliki do pobrania następnej kilka sekund, aby odtworzyć (zwane również buforowania). Jak zmniejsza przeciążenie sieci, odtwarzacza wideo z kolei powróci do strumienia wyższej jakości. Ta możliwość dostosowania przepustowość wymaganą również powoduje szybsze uruchamianie filmu wideo. Oznacza to, że pierwsze kilka sekund, mogą być odtwarzane w segmencie niższa jakość fast do pobierania, a następnie została buforowana krok do wyższej jakości zawartości po wystarczające.

Implementacją Dash.js jest typu open-source MPEG-DASH odtwarzacza wideo napisanych w języku JavaScript. Jego celem jest zapewnienie odtwarzacz niezawodne, dla wielu platform, który mogą być swobodnie ponownie użyte w aplikacjach, które wymagają odtwarzania wideo. Zapewnia odtwarzanie MPEG-DASH w dowolnej przeglądarce, który obsługuje obecnie W3C nośnika źródłowego rozszerzenia (MSE) dla programu Chrome, Microsoft Edge i IE11 (innych przeglądarek ma wskazuje zamiar obsługuje MSE). Aby uzyskać więcej informacji na temat implementacją DASH.js js, zobacz repozytorium implementacją dash.js w witrynie GitHub.

## <a name="creating-a-browser-based-streaming-video-player"></a>Tworzenie oparte na przeglądarce przesyłania strumieniowego odtwarzacza wideo
Aby utworzyć prostą stronę sieci web wyświetla odtwarzacza wideo za pomocą oczekiwanej kontroluje takich play, Wstrzymaj, przewiń itp., należy:

1. Utwórz stronę HTML
2. Dodaj tag wideo
3. Dodaj player implementacją dash.js
4. Inicjowanie odtwarzacza
5. Dodaj niektóre style CSS
6. Wyświetlanie wyników w przeglądarce, który implementuje MSE

Inicjowanie odtwarzacz trwa tylko kilka wierszy kodu JavaScript. Korzystając z implementacją dash.js, naprawdę jest proste do osadzania filmu wideo MPEG-DASH w swoich aplikacjach opartych na przeglądarce.

## <a name="creating-the-html-page"></a>Tworzenie strony HTML
Pierwszym krokiem jest utworzenie, standard zawierający HTML strony **wideo** elementu, Zapisz ten plik jako basicPlayer.html w poniższym przykładzie pokazano:

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

## <a name="adding-the-dashjs-player"></a>Dodawanie Player implementacją DASH.js
Aby dodać implementację referencyjną implementacją dash.js do aplikacji, należy do pobrania pliku dash.all.js z najnowszej wersji z implementacją dash.js projektu. To powinien zostać zapisany w folderze JavaScript w aplikacji. Ten plik jest plikiem wygody, który zbiera cały kod z implementacją dash.js niezbędne do pojedynczego pliku. Jeśli masz się wokół repozytorium implementacją dash.js możesz wyszukiwania poszczególnych plików, testowanie kodu i wiele innych, ale jeśli wszystko, czego chcemy jest implementacją dash.js, plik dash.all.js jest to, czego potrzebujesz.

Aby dodać player implementacją dash.js do aplikacji, należy dodać tag skryptu do sekcji head basicPlayer.html:

```html
    <!-- DASH-AVC/265 reference implementation -->
    < script src="js/dash.all.js"></script>
```

Następnie należy utworzyć funkcję, aby zainicjować odtwarzacza, jeśli strona ładuje się. Dodaj następujący skrypt po wierszu, w którym obciążenia dash.all.js:

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

Ta funkcja najpierw tworzy DashContext. Służy do konfigurowania aplikacji dla określonego środowiska. Z technicznego punktu widzenia definiuje klasy, które framework iniekcji zależności należy użyć podczas tworzenia aplikacji. W większości przypadków należy używać Dash.di.DashContext.

Następnie można utworzyć wystąpienia klasy podstawowej framework implementacją dash.js MediaPlayer. Ta klasa zawiera podstawowe metody takie jak Odtwórz wstrzymać, zarządza relacji z elementem wideo i zarządza także interpretacji pliku opisu prezentacji nośnika (MPD), który opisuje wideo do odtwarzania.

Funkcja startup() MediaPlayer — klasa jest wywoływana, aby upewnić się, że gracz jest gotowy do odtwarzania wideo. Między innymi funkcja gwarantuje, że wszystkie niezbędne klasy (zgodnie z definicją kontekście) zostały załadowane. Gdy gracz jest gotowy, możesz dołączyć do niego przy użyciu funkcji attachView() elementu wideo. Funkcja startowa umożliwia MediaPlayer wstawić strumienia wideo do elementu, a także sterować odtwarzaniem zgodnie z potrzebami.

Przekazać adres URL pliku MPD do MediaPlayer, tak aby wie o wideo, że oczekuje się, aby odtworzyć. Funkcja setupVideo() właśnie utworzony należy do wykonania po stronie pełni został załadowany. W tym za pomocą zdarzeń onload elementu body. Zmień swoje `<body>` elementu:

```html
    <body onload="setupVideo()">
```

Na koniec Ustaw rozmiar elementu wideo przy użyciu CSS. W adaptacyjne środowisko przesyłania strumieniowego jest to szczególnie ważne, ponieważ rozmiar odtwarzania filmu wideo może zmienić się odtwarzania dostosowuje do zmieniających się warunków sieciowych. W tym prosty pokaz po prostu wymusić elementu wideo do 80% okna przeglądarki dostępne, dodając następujące arkusze CSS do głównych części:

```html
    <style>
    video {
      width: 80%;
      height: 80%;
    }
    </style>
```

## <a name="playing-a-video"></a>Odtwarzanie filmu wideo
Aby odtworzyć film wideo, wskazać w przeglądarce w pliku basicPlayback.html, a następnie kliknij przycisk play na odtwarzacz wideo, wyświetlane.

## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Zobacz też
[Opracowywanie aplikacji odtwarzacza wideo](media-services-develop-video-players.md)

[Repozytorium w witrynie GitHub implementacją dash.js](https://github.com/Dash-Industry-Forum/dash.js) 

