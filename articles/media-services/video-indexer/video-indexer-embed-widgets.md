---
title: Osadzanie widżetów indeksatora wideo w aplikacjach
titlesuffix: Azure Media Services
description: Dowiedz się, jak osadzić widżety usługi Video Indexer w aplikacji.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: juliako
ms.openlocfilehash: 5acd9b68368f56000a0a32d1ade310cf30143950
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2019
ms.locfileid: "65799379"
---
# <a name="embed-video-indexer-widgets-into-your-applications"></a>Osadzanie widżetów indeksatora wideo w aplikacjach

W tym artykule wyjaśniono, jak osadzać widżety usługi Video Indexer w aplikacjach. Indeksator wideo obsługuje dwa typy osadzanie widżetów w aplikacji: **Cognitive Insights** i **Player**. 

Począwszy od wersji 2, podstawowy adres URL elementu widget zawiera region konta. Na przykład generuje konto w regionie zachodnie stany USA: `https://wus2.videoindexer.ai/embed/insights/...`.

## <a name="widget-types"></a>Typy widżetów

### <a name="cognitive-insights-widget"></a>Widżet Cognitive Insights

Widżet **Cognitive Insights** (Szczegółowe informacje) zawiera wszystkie szczegóły wizualne wyodrębnione z filmu wideo w procesie indeksowania. Widżet szczegółowych informacji obsługuje następujące parametry opcjonalne adresu URL:

|Name (Nazwa)|Definicja|Opis|
|---|---|---|
|widgets|Ciągi rozdzielone przecinkami|Umożliwia kontrolowanie szczegółowych informacji do renderowania. <br/>Przykład: parametr `https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?widgets=people,search` spowoduje wyrenderowanie tylko szczegółowych informacji interfejsu użytkownika na temat ludzi i marek<br/>Dostępne opcje: people, keywords, annotations, brands, sentiments, transcript, search.<br/>Brak obsługi przez adres URL w przypadku parametru version=2<br/><br/>**Uwaga:** Elementy widget param adres URL nie jest obsługiwane w wersji 2. |

### <a name="player-widget"></a>Widżet Player

Widżet **Player** (Odtwarzacz) umożliwia przesyłanie strumieniowe wideo z adaptacyjną szybkością transmisji. Widżet Player obsługuje następujące parametry opcjonalne adresu URL:

|Name (Nazwa)|Definicja|Opis|
|---|---|---|
|t|Sekundy od początku|Sprawia, że odtwarzanie rozpoczyna się od podanego punktu w czasie.<br/>Przykład: t=60|
|captions|Kod języka|Pobiera napisy w danym języku podczas ładowania widżetu w celu udostępnienia ich w menu napisów.<br/>Przykład: captions=en-US|
|showCaptions|Wartość logiczna|Powoduje załadowanie już włączonych napisów.<br/>Przykład: showCaptions=true|
|type||Aktywuje skórkę odtwarzacza audio (część wideo jest usuwana).<br/>Przykład: type=audio|
|autoplay|Wartość logiczna|Wskazuje, czy należy rozpocząć odtwarzanie po załadowaniu pliku wideo (wartość domyślna to true).<br/>Przykład: autoplay=false|
|language|Kod języka|Określa język widżetu Player (wartość domyślna to en-US)<br/>Przykład: language=de-DE|

## <a name="embedding-public-content"></a>Osadzanie zawartości publicznej

1. Przejdź do witryny internetowej [Video Indexer](https://www.videoindexer.ai/) i zaloguj się.
2. Kliknij film wideo, z którym chcesz pracować.
3. Kliknij przycisk „embed” (osadź) znajdujący się poniżej pliku wideo.

    ![Widżet](./media/video-indexer-embed-widgets/video-indexer-widget01.png)

    Po kliknięciu przycisku na ekranie pojawi się okno modalne osadzania, w którym możesz wybrać widżet do osadzenia w aplikacji.
    Wybranie widżetu (**Player** lub **Cognitive Insights**) spowoduje wygenerowanie kodu osadzania do wklejenia w aplikacji.
 
4. Wybierz typ widżetu (**Cognitive Insights** lub **Player**).
5. Skopiuj kod osadzania i dodaj go do swojej aplikacji. 

    ![Widżet](./media/video-indexer-embed-widgets/video-indexer-widget02.png)

## <a name="embedding-private-content"></a>Osadzanie zawartości prywatnej

Tylko w przypadku **publicznych** plików wideo można uzyskać kody osadzania z okien podręcznych osadzania (jak pokazano w poprzedniej sekcji). 

Jeśli chcesz osadzić **prywatny** plik wideo, musisz przekazać token dostępu w atrybucie **src** elementu **iframe**:

`https://www.videoindexer.ai/embed/[insights | player]/<accountId>/<videoId>/?accessToken=<accessToken>`
    
Skorzystaj z interfejsu API [**pobierania widżetu szczegółowych informacji**](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Video-Insights-Widget?&pattern=widget), aby pobrać zawartość elementu widżetu Cognitive Insights, lub z funkcji [**pobierania tokenu dostępu wideo**](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Video-Access-Token?) i dodaj ten token jako parametr zapytania do adresu URL, jak pokazano powyżej. Określ ten adres URL jako wartość **src** elementu **iframe**.

Jeśli chcesz zapewnić możliwość edycji szczegółowych informacji w osadzonym widżecie (tak jak w naszej aplikacji internetowej), musisz przekazać token dostępu z uprawnieniami do edycji. Użyj funkcji [**pobierania widżetu szczegółowych informacji**](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Video-Insights-Widget?&pattern=widget) lub [**pobierania tokenu dostępu wideo**](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Video-Access-Token?) z parametrem **&allowEdit=true**. 

## <a name="widgets-interaction"></a>Interakcje z widżetami

Widżet **Cognitive Insights** może wchodzić w interakcje z plikiem wideo w aplikacji. W tej sekcji pokazano, jak zrealizować taką interakcję.

![Widżet](./media/video-indexer-embed-widgets/video-indexer-widget03.png)

### <a name="cross-origin-communications"></a>Komunikacja między źródłami

Aby umożliwić widżetom usługi Video Indexer komunikowanie się z innymi składnikami, usługa ta:

- używa metody HTML5 do komunikacji między źródłami — **postMessage**; 
- weryfikuje komunikaty w źródle videoIndexer.ai. 

Jeśli zdecydujesz się na wdrożenie własnego kodu odtwarzacza i realizację integracji z widżetami **Cognitive Insights**, odpowiadasz za zweryfikowanie źródła komunikatu pochodzącego z VideoIndexer.ai.

### <a name="embed-both-types-of-widgets-in-your-application--blog-recommended"></a>Osadzanie obu typów widżetów w aplikacji/blogu (zalecane) 

W tej sekcji pokazano, jak zrealizować interakcję między dwoma widżetami usługi Video Indexer: gdy użytkownik kliknie kontrolkę szczegółowych informacji w aplikacji, odtwarzacz przeskoczy do odpowiedniego punktu w nagraniu wideo.

`<script src="https://breakdown.blob.core.windows.net/public/vb.widgets.mediator.js"></script>`

1. Skopiuj kod osadzania widżetu **Player**.
2. Skopiuj kod osadzania widżetu **Cognitive Insights**.
3. Dodaj [**plik mediatora**](https://breakdown.blob.core.windows.net/public/vb.widgets.mediator.js) do obsługi komunikacji między dwoma widżetami:

`<script src="https://breakdown.blob.core.windows.net/public/vb.widgets.mediator.js"></script>`

Teraz gdy użytkownik kliknie kontrolkę szczegółowych informacji w aplikacji, odtwarzacz przeskoczy do odpowiedniego punktu w nagraniu wideo.

Aby uzyskać więcej informacji, zobacz [ten pokaz](https://codepen.io/videoindexer/pen/NzJeOb).

### <a name="embed-the-cognitive-insights-widget-and-use-azure-media-player-to-play-the-content"></a>Osadzanie widżetu Cognitive Insights i odtwarzanie zawartości za pomocą usługi Azure Media Player

W tej sekcji pokazano, jak zrealizować interakcję między widżetem **Cognitive Insights** i wystąpieniem usługi Azure Media Player za pomocą [wtyczki AMP](https://breakdown.blob.core.windows.net/public/amp-vb.plugin.js).
 
1. Dodaj wtyczkę usługi Video Indexer dla odtwarzacza AMP.<br/> `<script src="https://breakdown.blob.core.windows.net/public/amp-vb.plugin.js"></script>`
2. Utwórz wystąpienie usługi Azure Media Player za pomocą wtyczki usługi Video Indexer.

        // Init Source
        function initSource() {
            var tracks = [{
            kind: 'captions',
            // Here is how to load vtt from VI, you can replace it with your vtt url.
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

        // Init your AMP instance
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
            // Activate the plugin
            this.videobreakdown({
            videoId: "c4c1ad4c9a",
            syncTranscript: true,
            syncLanguage: true
            });

            // Set the source dynamically
            initSource.call(this);
        });

3. Skopiuj kod osadzania widżetu **Cognitive Insights**.

Teraz powinna być możliwa komunikacja z usługą Azure Media Player.

Aby uzyskać więcej informacji, zobacz [ten pokaz](https://codepen.io/videoindexer/pen/rYONrO).

### <a name="embed-video-indexer-cognitive-insights-widget-and-use-your-own-player-could-be-any-player"></a>Osadzanie widżetu Cognitive Insights usługi Video Indexer i używanie własnego odtwarzacza (dowolnego)

W przypadku używania własnego odtwarzacza trzeba samodzielnie zadbać o manipulowanie odtwarzaczem w celu zapewnienia komunikacji. 

1. Wstaw odtwarzacz wideo.

    Na przykład standardowy odtwarzacz HTML5

        <video id="vid1" width="640" height="360" controls autoplay preload>
           <source src="//breakdown.blob.core.windows.net/public/Microsoft%20HoloLens-%20RoboRaid.mp4" type="video/mp4" /> 
           Your browser does not support the video tag.
        </video>    

2. Osadź widżet Cognitive Insights.
3. Zaimplementuj komunikację dla odtwarzacza z nasłuchiwaniem w oczekiwaniu na zdarzenie „message”. Na przykład:

        <script>
    
            (function(){
            // Reference your player instance
            var playerInstance = document.getElementById('vid1');
        
            function jumpTo(evt) {
              var origin = evt.origin || evt.originalEvent.origin;
        
              // Validate that event comes from the videobreakdown domain.
              if ((origin === "https://www.videobreakdown.com") && evt.data.time !== undefined){
                
                // Here you need to call your player "jumpTo" implementation
                playerInstance.currentTime = evt.data.time;
               
                // Confirm arrival to us
                if ('postMessage' in window) {
                  evt.source.postMessage({confirm: true, time: evt.data.time}, origin);
                }
              }
            }
        
            // Listen to message event
            window.addEventListener("message", jumpTo, false);
          
            }())    
        
        </script>

Aby uzyskać więcej informacji, zobacz [ten pokaz](https://codepen.io/videoindexer/pen/YEyPLd).

## <a name="adding-subtitles"></a>Dodawanie napisów

Jeśli osadzasz szczegółowe informacje usługi Video Indexer z własnym odtwarzaczem AMP, możesz pobrać napisy (podpisy) za pomocą metody **GetVttUrl**. Możesz również wywołać metodę JavaScript **getSubtitlesUrl** z wtyczki AMP usługi Video Indexer (jak pokazano wcześniej). 

## <a name="customizing-embeddable-widgets"></a>Dostosowywanie osadzalnych widżetów

### <a name="cognitive-insights-widget"></a>Widżet Cognitive Insights

Możesz wybrać typy wglądu w szczegółowe dane mają, określając je jako wartość do następującego parametru URL dodawane do kodu osadzania otrzymasz (z interfejsu API lub aplikacji sieci web): `&widgets=<list of wanted widgets>`.

Możliwe wartości: people, keywords, sentiments, transcript, search.

Na przykład jeśli chcesz osadzić elementu widget zawierający tylko insights osób i Wyszukiwanie elementu iframe adres URL osadzania będzie wyglądać następująco:

`https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?widgets=people,search`

Można również dostosować tytuł okna elementu iframe, zapewniając `&title=<YourTitle>` do adresu URL elementu iframe. (Spowoduje to dostosowanie wartości \<title> elementu html).
    
Na przykład jeśli chcesz nadać okna w elemencie iframe tytuł "MyInsights", adres URL będzie wyglądać następująco:

`https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?title=MyInsights`

Ta opcja ma zastosowanie tylko w przypadkach, gdy trzeba otworzyć szczegółowe informacje w nowym oknie.

### <a name="player-widget"></a>Widżet Player

W przypadku osadzania widżetu Player usługi Video Indexer można wybrać rozmiar odtwarzacza, określając rozmiar elementu iframe.

Na przykład:

`<iframe width="640" height="360" src="https://www.videoindexer.ai/embed/player/<accountId>/<videoId>/" frameborder="0" allowfullscreen />`

Domyślnie widżet Player usługi Video Indexer będzie mieć automatycznie generowane napisy oparte na transkrypcji nagrania wideo wyodrębnionego z pliku wideo w języku źródłowym, który został wybrany podczas przekazywania pliku wideo.

Jeśli chcesz osadzanie za pomocą innego języka, można dodać `&captions=< Language | ”all” | “false” >` adres URL odtwarzacza osadzania lub put "all" jako wartość, jeśli chcesz mieć wszystkie dostępne języki podpisów.
Chcąc podpisów, które mają być wyświetlane domyślnie można przekazać `&showCaptions=true`.

Adres URL osadzania następnie będzie wyglądać następująco: 

`https://www.videoindexer.ai/embed/player/<accountId>/<videoId>/?captions=italian`

Jeśli chcesz wyłączyć napisy, jako wartość parametru „captions” możesz przekazać wartość „false”.

Automatyczne odtwarzanie — domyślnie odtwarzacz rozpocznie odtwarzanie pliku wideo. Możesz to zmienić, przekazując w powyższym adresie URL osadzania parametr &autoplay=false.

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać informacje o sposobie wyświetlania i edytowania szczegółowych informacji usługi Video Indexer, zobacz [ten](video-indexer-view-edit.md) artykuł.

Ponadto zapoznaj się z [Video indexer funkcji codepen można](https://codepen.io/videoindexer/pen/eGxebZ).
