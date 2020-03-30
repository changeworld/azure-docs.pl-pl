---
title: Osadzanie adaptacyjnego przesyłania wideo MPEG-DASH w aplikacji HTML5 za pomocą dash.js | Dokumenty firmy Microsoft
description: W tym temacie pokazano, jak osadzić MPEG-DASH Adaptive Streaming Video w aplikacji HTML5 z DASH.js.
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
ms.openlocfilehash: 6c1df14ba5a9f233f42750d4e6dea68a7d6ddc0e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77564860"
---
# <a name="embedding-an-mpeg-dash-adaptive-streaming-video-in-an-html5-application-with-dashjs"></a>Osadzanie plików wideo adaptacyjnego przesyłania strumieniowego MPEG-DASH w aplikacji HTML5 z implementacją DASH.js  

## <a name="overview"></a>Omówienie
MPEG-DASH to standard ISO do adaptacyjnego przesyłania strumieniowego treści wideo, który oferuje znaczące korzyści dla programistów, którzy chcą dostarczać wysokiej jakości, adaptacyjne wyjście strumieniowe wideo. Dzięki technologii MPEG-DASH strumień wideo automatycznie dostosowuje się do niższej rozdzielczości, gdy sieć staje się przeciążona. Zmniejsza to prawdopodobieństwo, że widz zobaczy "wstrzymany" film, podczas gdy odtwarzacz pobierze kolejne kilka sekund, aby odtworzyć (to oznacza, że zmniejsza prawdopodobieństwo buforowania). W miarę zmniejszania się przeciążenia sieci odtwarzacz wideo powróci do strumienia wyższej jakości. Ta możliwość dostosowania wymaganej przepustowości powoduje również krótszy czas rozpoczęcia wideo. Oznacza to, że pierwsze kilka sekund można odtworzyć w segmencie niższej jakości, a następnie przejść do wyższej jakości po buforowanie wystarczającej ilości zawartości.

Dash.js jest odtwarzaczem wideo MPEG-DASH typu open source napisanym w języku JavaScript. Jego celem jest zapewnienie solidnego, wieloplatformowego odtwarzacza, który może być swobodnie ponownie odtwarzany w aplikacjach wymagających odtwarzania wideo. Zapewnia odtwarzanie MPEG-DASH w dowolnej przeglądarce obsługującej rozszerzenia W3C Media Source Extensions (MSE), dziś czyli Chrome, Microsoft Edge i IE11 (inne przeglądarki wskazały zamiar obsługi MSE). Aby uzyskać więcej informacji na temat DASH.js, js zobacz repozytorium GitHub dash.js.

## <a name="creating-a-browser-based-streaming-video-player"></a>Tworzenie odtwarzacza strumieniowego przesyłania wideo opartego na przeglądarce
Aby utworzyć prostą stronę internetową, która wyświetla odtwarzacz wideo z oczekiwanymi elementami sterującymi, takimi jak odtwarzanie, pauza, przewijanie do tyłu itp., należy:

1. Tworzenie strony HTML
2. Dodawanie tagu wideo
3. Dodaj odtwarzacz dash.js
4. Inicjowanie odtwarzacza
5. Dodaj styl CSS
6. Wyświetlanie wyników w przeglądarce, która implementuje MSE

Inicjowanie odtwarzacza można ukończyć w zaledwie kilku wierszach kodu JavaScript. Korzystanie dash.js, to naprawdę jest to, że proste do osadzenia MPEG-DASH wideo w aplikacji opartych na przeglądarce.

## <a name="creating-the-html-page"></a>Tworzenie strony HTML
Pierwszym krokiem jest utworzenie standardowej strony HTML zawierającej element **wideo,** zapisz ten plik jako basicPlayer.html, jak pokazano w poniższym przykładzie:

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

## <a name="adding-the-dashjs-player"></a>Dodawanie odtwarzacza DASH.js
Aby dodać dash.js odwołania implementacji do aplikacji, należy pobrać dash.all.js plik z najnowszej wersji projektu dash.js. Należy to zapisać w folderze JavaScript aplikacji. Ten plik jest plik wygody, który ściąga wszystkie niezbędne dash.js kod do jednego pliku. Jeśli masz rozejrzyć się po repozytorium dash.js, znajdziesz poszczególne pliki, kod testowy i wiele więcej, ale jeśli wszystko, co chcesz zrobić, to użyć dash.js, to plik dash.all.js jest tym, czego potrzebujesz.

Aby dodać odtwarzacz dash.js do aplikacji, dodaj tag skryptu do sekcji head basicPlayer.html:

```html
    <!-- DASH-AVC/265 reference implementation -->
    < script src="js/dash.all.js"></script>
```

Następnie utwórz funkcję, aby zainicjować odtwarzacz po załadowaniu strony. Dodaj następujący skrypt po wierszu, w którym ładujesz dash.all.js:

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

Ta funkcja najpierw tworzy DashContext. Służy do konfigurowania aplikacji dla określonego środowiska wykonawczego. Z technicznego punktu widzenia definiuje klasy, które należy użyć struktury iniekcji zależności podczas konstruowania aplikacji. W większości przypadków używasz Dash.di.DashContext.

Następnie wystąpienia podstawowej klasy dash.js framework, MediaPlayer. Ta klasa zawiera podstawowe metody potrzebne, takie jak odtwarzanie i pauza, zarządza relacją z elementem wideo, a także zarządza interpretacją pliku opis prezentacji multimediów (MPD), który opisuje wideo do odtworzonego.

Funkcja startup() klasy MediaPlayer jest wywoływana, aby upewnić się, że odtwarzacz jest gotowy do odtwarzania wideo. Między innymi funkcja zapewnia, że wszystkie niezbędne klasy (zgodnie z definicją kontekstu) zostały załadowane. Gdy odtwarzacz będzie gotowy, możesz dołączyć do niego element wideo za pomocą funkcji attachView(). Funkcja uruchamiania umożliwia MediaPlayer wstrzyknąć strumień wideo do elementu, a także kontrolować odtwarzanie w razie potrzeby.

Przekaż adres URL pliku MPD do MediaPlayer, aby wiedział o filmie, który ma zostać odtworzony. Utworzona właśnie funkcja setupVideo() będzie musiała zostać wykonana po pełnym załadowaniu strony. W tym celu za pomocą zdarzenia onload elementu ciała. Zmień `<body>` swój element na:

```html
    <body onload="setupVideo()">
```

Na koniec ustaw rozmiar elementu wideo za pomocą CSS. W adaptacyjnym środowisku przesyłania strumieniowego jest to szczególnie ważne, ponieważ rozmiar odtwarzanego wideo może ulec zmianie w miarę dostosowywania odtwarzania do zmieniających się warunków sieciowych. W tym prostym demo po prostu wymusić element wideo do 80% dostępnego okna przeglądarki, dodając następujące CSS do sekcji head strony:

```html
    <style>
    video {
      width: 80%;
      height: 80%;
    }
    </style>
```

## <a name="playing-a-video"></a>Odtwarzanie filmu
Aby odtworzyć film, wskaż przeglądarkę na podstawowym pliku Playback.html i kliknij przycisk odtwórz wyświetlony odtwarzacz wideo.

## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Zobacz też

[Repozytorium GitHub dash.js](https://github.com/Dash-Industry-Forum/dash.js) 

