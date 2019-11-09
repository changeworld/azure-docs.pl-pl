---
title: Osadź Video Indexer widżety w aplikacjach
titleSuffix: Azure Media Services
description: Dowiedz się, jak osadzić Video Indexer widżety w aplikacji.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 07/29/2019
ms.author: juliako
ms.openlocfilehash: 99d6647ab5e7fa8f35cef883dd00ae9fea866370
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73839120"
---
# <a name="embed-video-indexer-widgets-in-your-applications"></a>Osadź Video Indexer widżety w aplikacjach

W tym artykule przedstawiono sposób osadzania Video Indexer widżetów w aplikacjach. Video Indexer obsługuje osadzanie trzech typów elementów widget w aplikacji: *szczegółowe informacje*, *odtwarzacze*i *Edytor*. 

Począwszy od wersji 2, podstawowy adres URL widżetu zawiera region określonego konta. Na przykład konto w regionie zachodnie stany USA generuje: `https://wus2.videoindexer.ai/embed/insights/...`.

## <a name="widget-types"></a>Typy widżetów

### <a name="cognitive-insights-widget"></a>Widżet Cognitive Insights

Widżet analizy poznawczej zawiera wszystkie informacje wizualne, które zostały wyodrębnione z procesu indeksowania wideo. Widżet szczegółowe informacje obsługuje następujące opcjonalne parametry adresu URL.

|Nazwa|Definicja|Opis|
|---|---|---|
|`widgets`|Ciągi rozdzielone przecinkami|Umożliwia kontrolowanie szczegółowych informacji, które mają być renderowane. <br/> Przykład: `https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?widgets=people,search` renderuje tylko osoby i informacje o interfejsie użytkownika marki.<br/>Dostępne opcje: people, keywords, annotations, brands, sentiments, transcript, search.<br/>Należy pamiętać, że parametr adresu URL `widgets` nie jest obsługiwany w wersji 2.<br/>|
|`locale`|Kod w języku krótkim|Steruje językiem usługi Insights. Wartość domyślna to `en`. <br/> Przykład: `locale=de`.|
|`tab`|Domyślna wybrana karta|Steruje kartą usługi **Insights** , która jest renderowana domyślnie. <br/> Przykład: `tab=timeline` renderuje szczegółowe informacje z wybraną kartą **oś czasu** .|

### <a name="player-widget"></a>Widżet Player

Możesz użyć widżetu odtwarzacza do przesyłania strumieniowego wideo przy użyciu adaptacyjnej szybkości transmisji bitów. Widżet odtwarzacza obsługuje następujące opcjonalne parametry adresu URL.

|Nazwa|Definicja|Opis|
|---|---|---|
|`t`|Sekund od początku|Sprawia, że gracz rozpocznie odtwarzanie od określonego momentu.<br/> Przykład: `t=60`.|
|`captions`|Kod języka|Pobiera podpis w określonym języku podczas ładowania elementu widget, aby był dostępny w menu **podpisy** .<br/> Przykład: `captions=en-US`.|
|`showCaptions`|Wartość logiczna|Powoduje załadowanie już włączonych napisów.<br/> Przykład: `showCaptions=true`.|
|`type`||Aktywuje karnację odtwarzacza audio (część wideo jest usuwana).<br/> Przykład: `type=audio`.|
|`autoplay`|Wartość logiczna|Wskazuje, czy odtwarzacz powinien rozpocząć odtwarzanie wideo po załadowaniu. Wartość domyślna to `true`.<br/> Przykład: `autoplay=false`.|
|`language`|Kod języka|Kontroluje język odtwarzacza. Wartość domyślna to `en-US`.<br/>Przykład: `language=de-DE`.|

### <a name="editor-widget"></a>Widżet edytora

Za pomocą widżetu edytora można tworzyć nowe projekty i zarządzać szczegółowymi informacjami wideo. Widżet edytora obsługuje następujące opcjonalne parametry adresu URL.

|Nazwa|Definicja|Opis|
|---|---|---|
|`accessToken`<sup>*</sup>|Ciąg|Zapewnia dostęp do filmów wideo tylko na koncie, które jest używane do osadzenia widżetu.<br> Widżet edytora wymaga parametru `accessToken`.|
|`language`|Kod języka|Kontroluje język odtwarzacza. Wartość domyślna to `en-US`.<br/>Przykład: `language=de-DE`.|
|`locale`|Kod w języku krótkim|Steruje językiem usługi Insights. Wartość domyślna to `en`.<br/>Przykład: `language=de`.|

<sup>*</sup> Właściciel powinien zapewnić `accessToken` z zachowaniem ostrożności.

## <a name="embedding-public-content"></a>Osadzanie zawartości publicznej

1. Zaloguj się do witryny sieci Web [Video Indexer](https://www.videoindexer.ai/) .
2. Wybierz wideo, z którym chcesz współpracować.
3. Wybierz przycisk **Osadź** , który pojawia się pod klipem wideo.

    ![Widżet](./media/video-indexer-embed-widgets/video-indexer-widget01.png)

    Po wybraniu przycisku **Osadź** można wybrać widżet, który ma zostać osadzony w aplikacji. 
4. Wybierz odpowiedni typ widżetu (**poznawcze informacje**, **odtwarzacz**lub **Edytor**).
 
5. Skopiuj kod osadzania, a następnie dodaj go do aplikacji. 

    ![Widżet](./media/video-indexer-embed-widgets/video-indexer-widget02.png)

> [!NOTE]
> Jeśli masz problemy z udostępnianiem adresów URL wideo, Dodaj do łącza parametr `location`. Parametr powinien być ustawiony na [regiony platformy Azure, w których istnieje Video Indexer](regions.md). Na przykład: `https://www.videoindexer.ai/accounts/00000000-0000-0000-0000-000000000000/videos/b2b2c74b8e/?location=trial`.

## <a name="embedding-private-content"></a>Osadzanie zawartości prywatnej

Aby osadzić prywatny film wideo, należy przekazać token dostępu w atrybucie **src** elementu iframe:

`https://www.videoindexer.ai/embed/[insights | player]/<accountId>/<videoId>/?accessToken=<accessToken>`
    
Aby uzyskać zawartość widżetu wglądu w szczegółowe dane, użyj jednego z następujących elementów:<br/>
- Interfejs API usługi [Get Insights](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Video-Insights-Widget?&pattern=widget) .<br/>
- [Token uzyskiwania dostępu do wideo](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Video-Access-Token?). Dodaj go jako parametr zapytania do adresu URL. Określ ten adres URL jako wartość **src** dla elementu iframe, jak pokazano wcześniej.

Aby zapewnić możliwości edytowania szczegółowych informacji w osadzonym elemencie widget, należy przekazać token dostępu, który obejmuje uprawnienia do edycji. Użyj [widżetu Get Insights](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Video-Insights-Widget?&pattern=widget) lub [Uzyskaj token dostępu wideo](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Video-Access-Token?) z `&allowEdit=true`. 

## <a name="widgets-interaction"></a>Interakcje z widżetami

Widżet wglądu w szczegółowe dane może korzystać z wideo w aplikacji. W tej sekcji pokazano, jak zrealizować taką interakcję.

![Widżet](./media/video-indexer-embed-widgets/video-indexer-widget03.png)

### <a name="cross-origin-communications"></a>Komunikacja między źródłami

Aby uzyskać Video Indexer widżety do komunikowania się z innymi składnikami, usługa Video Indexer:

- Używa metody HTML5 komunikacji między źródłami **PostMessage**. 
- weryfikuje komunikaty w źródle videoIndexer.ai. 

Jeśli zaimplementowasz własny kod odtwarzacza i zintegrujesz go z widżetami poznawcze spostrzeżenia, odpowiadasz za potwierdzenie pochodzenia wiadomości, która pochodzi z VideoIndexer.ai.

### <a name="embed-widgets-in-your-application-or-blog-recommended"></a>Osadź widżety w aplikacji lub blogu (zalecane) 

W tej sekcji przedstawiono sposób osiągnięcia interakcji między dwoma Video Indexer widżetami, dzięki czemu gdy użytkownik wybierze kontrolę wglądu w aplikację, gracz przejdzie do odpowiedniego momentu.

1. Skopiuj kod osadzania widżetu odtwarzacza.
2. Skopiuj kod osadzania poznawczego wglądu w szczegółowe dane.
3. Dodaj [plik mediator](https://breakdown.blob.core.windows.net/public/vb.widgets.mediator.js) , aby obsłużyć komunikację między dwoma widżetami:<br/> 
`<script src="https://breakdown.blob.core.windows.net/public/vb.widgets.mediator.js"></script>`

Teraz, gdy użytkownik wybierze kontrolę wglądu w aplikację, gracz przechodzi do odpowiedniego momentu.

Aby uzyskać więcej informacji, zobacz [Video Indexer — osadzanie obu elementów widget](https://codepen.io/videoindexer/pen/NzJeOb).

### <a name="embed-the-cognitive-insights-widget-and-use-azure-media-player-to-play-the-content"></a>Osadzanie widżetu Cognitive Insights i odtwarzanie zawartości za pomocą usługi Azure Media Player

W tej sekcji przedstawiono sposób osiągnięcia interakcji między widżetem analizy poznawczej a wystąpieniem Azure Media Player za pomocą [wtyczki amp](https://breakdown.blob.core.windows.net/public/amp-vb.plugin.js).
 
1. Dodaj wtyczkę Video Indexer dla odtwarzacza AMP:<br/> `<script src="https://breakdown.blob.core.windows.net/public/amp-vb.plugin.js"></script>`
2. Tworzenie wystąpienia Azure Media Player za pomocą wtyczki Video Indexer.

        // Init the source.
        function initSource() {
            var tracks = [{
            kind: 'captions',
            // To load vtt from VI, replace it with your vtt URL.
            src: this.getSubtitlesUrl("c4c1ad4c9a", "English"),
            srclang: 'en',
            label: 'English'
            }];

            myPlayer.src([
            {
                "src": "//amssamples.streaming.mediaservices.windows.net/91492735-c523-432b-ba01-faba6c2206a2/AzureMediaServicesPromo.ism/manifest",
                "type": "application/vnd.ms-sstr+xml"
            }
            ], tracks);
        }

        // Init your AMP instance.
        var myPlayer = amp('vid1', { /* Options */
            "nativeControlsForTouch": false,
            autoplay: true,
            controls: true,
            width: "640",
            height: "400",
            poster: "",
            plugins: {
            videobreakedown: {}
            }
        }, function () {
            // Activate the plug-in.
            this.videobreakdown({
            videoId: "c4c1ad4c9a",
            syncTranscript: true,
            syncLanguage: true
            });

            // Set the source dynamically.
            initSource.call(this);
        });

3. Skopiuj kod osadzania poznawczego wglądu w szczegółowe dane.

Teraz powinno być możliwe komunikowanie się z Azure Media Player.

Aby uzyskać więcej informacji, zobacz [demonstracja Azure Media Player + VI Insights](https://codepen.io/videoindexer/pen/rYONrO).

### <a name="embed-the-video-indexer-cognitive-insights-widget-and-use-a-different-video-player"></a>Osadź widżet Video Indexer poznawcze spostrzeżenia i użyj innego odtwarzacza wideo

Jeśli używasz odtwarzacza wideo innego niż Azure Media Player, musisz ręcznie manipulować odtwarzaczem wideo w celu osiągnięcia komunikacji. 

1. Wstaw odtwarzacz wideo.

    Na przykład standardowy odtwarzacz HTML5:

        <video id="vid1" width="640" height="360" controls autoplay preload>
           <source src="//breakdown.blob.core.windows.net/public/Microsoft%20HoloLens-%20RoboRaid.mp4" type="video/mp4" /> 
           Your browser does not support the video tag.
        </video>    

2. Osadź widżet Cognitive Insights.
3. Zaimplementuj komunikację dla odtwarzacza z nasłuchiwaniem w oczekiwaniu na zdarzenie „message”. Na przykład:

        <script>
    
            (function(){
            // Reference your player instance.
            var playerInstance = document.getElementById('vid1');
        
            function jumpTo(evt) {
              var origin = evt.origin || evt.originalEvent.origin;
        
              // Validate that the event comes from the videobreakdown domain.
              if ((origin === "https://www.videobreakdown.com") && evt.data.time !== undefined){
                
                // Call your player's "jumpTo" implementation.
                playerInstance.currentTime = evt.data.time;
               
                // Confirm the arrival to us.
                if ('postMessage' in window) {
                  evt.source.postMessage({confirm: true, time: evt.data.time}, origin);
                }
              }
            }
        
            // Listen to the message event.
            window.addEventListener("message", jumpTo, false);
          
            }())    
        
        </script>

Aby uzyskać więcej informacji, zobacz [demonstracja Azure Media Player + VI Insights](https://codepen.io/videoindexer/pen/YEyPLd).

## <a name="adding-subtitles"></a>Dodawanie napisów

Jeśli osadzisz Video Indexer Insights przy użyciu własnych [Azure Media Player](https://aka.ms/azuremediaplayer), możesz użyć metody **GetVttUrl** , aby uzyskać napisy (napisy). Możesz również wywołać metodę JavaScript z Video Indexer AMP **getSubtitlesUrl** (jak pokazano wcześniej). 

## <a name="customizing-embeddable-widgets"></a>Dostosowywanie osadzalnych widżetów

### <a name="cognitive-insights-widget"></a>Widżet Cognitive Insights

Możesz wybrać typy szczegółowych informacji. W tym celu należy określić jako wartość następujący parametr adresu URL, który jest dodawany do kodu osadzania, który otrzymujesz (z interfejsu API lub z aplikacji sieci Web): `&widgets=<list of wanted widgets>`.

Możliwe wartości to: **ludzie**, **Keywords**, **mową**, **transkrypcja**i **Search**.

Na przykład jeśli chcesz osadzić widżet zawierający tylko osoby i szczegółowe dane wyszukiwania, adres URL osadzania elementu iframe będzie wyglądać następująco:

`https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?widgets=people,search`

Tytuł okna elementu IFRAME można również dostosować, dostarczając `&title=<YourTitle>` do adresu URL elementu iframe. (Dostosowuje tytuł \<HTML > wartość).
    
Na przykład jeśli chcesz nadać polu elementu IFRAME tytuł "MyInsights", adres URL będzie wyglądać następująco:

`https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?title=MyInsights`

Ta opcja ma zastosowanie tylko w przypadkach, gdy trzeba otworzyć szczegółowe informacje w nowym oknie.

### <a name="player-widget"></a>Widżet Player

W przypadku osadzania widżetu Player usługi Video Indexer można wybrać rozmiar odtwarzacza, określając rozmiar elementu iframe.

Na przykład:

`<iframe width="640" height="360" src="https://www.videoindexer.ai/embed/player/<accountId>/<videoId>/" frameborder="0" allowfullscreen />`

Domyślnie program Video Indexer Player ma automatycznie generowane napisy, które są oparte na transkrypcji filmu wideo. Transkrypcja jest wyodrębniana z filmu wideo z językiem źródłowym, który został wybrany podczas przekazywania wideo.

Jeśli chcesz osadzić w innym języku, możesz dodać `&captions=< Language | "all" | "false" >` do adresu URL osadzania odtwarzacza. Jeśli chcesz mieć podpisy we wszystkich dostępnych językach, użyj wartości `all`. Jeśli chcesz, aby podpisy były wyświetlane domyślnie, możesz przekazać `&showCaptions=true`.

Adres URL osadzania będzie wyglądać następująco: 

`https://www.videoindexer.ai/embed/player/<accountId>/<videoId>/?captions=italian`

Aby wyłączyć napisy, można przekazać `captions` wartość parametru jako `false`.

#### <a name="autoplay"></a>Autoodtwarzania
Domyślnie gracz rozpocznie odtwarzanie filmu wideo. Możesz zrezygnować z przekazywania `&autoplay=false` do poprzedniego adresu URL osadzania.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać informacje na temat sposobu wyświetlania i edytowania Video Indexer szczegółowych informacji, zobacz [Wyświetlanie i edytowanie Video Indexer szczegółowych](video-indexer-view-edit.md)informacji.

Zapoznaj się również z [CodePen indeksatora wideo](https://codepen.io/videoindexer/pen/eGxebZ).
