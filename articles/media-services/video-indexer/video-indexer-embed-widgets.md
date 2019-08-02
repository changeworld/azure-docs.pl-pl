---
title: Osadź Video Indexer widżety w aplikacjach
titlesuffix: Azure Media Services
description: Dowiedz się, jak osadzić widżety usługi Video Indexer w aplikacji.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 07/29/2019
ms.author: juliako
ms.openlocfilehash: ec3c7379c8c7f28765fbc4396d3e9804a6c127f6
ms.sourcegitcommit: e3b0fb00b27e6d2696acf0b73c6ba05b74efcd85
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/30/2019
ms.locfileid: "68663747"
---
# <a name="embed-video-indexer-widgets-into-your-applications"></a>Osadź Video Indexer widżety w aplikacjach

W tym artykule wyjaśniono, jak osadzać widżety usługi Video Indexer w aplikacjach. Video Indexer obsługuje osadzanie trzech typów elementów widget w aplikacji: **Szczegółowe informacje**, odtwarzaczei **Edytor**. 

Począwszy od wersji 2, podstawowy adres URL widżetu zawiera region określonego konta. Na przykład konto w regionie zachodnie stany USA generuje: `https://wus2.videoindexer.ai/embed/insights/...`.

## <a name="widget-types"></a>Typy widżetów

### <a name="cognitive-insights-widget"></a>Widżet Cognitive Insights

Widżet **Cognitive Insights** (Szczegółowe informacje) zawiera wszystkie szczegóły wizualne wyodrębnione z filmu wideo w procesie indeksowania. Widżet szczegółowych informacji obsługuje następujące parametry opcjonalne adresu URL:

|Name (Nazwa)|Definicja|Opis|
|---|---|---|
|`widgets`|Ciągi rozdzielone przecinkami|Umożliwia kontrolowanie szczegółowych informacji do renderowania. <br/>Przykład: parametr `https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?widgets=people,search` spowoduje wyrenderowanie tylko szczegółowych informacji interfejsu użytkownika na temat ludzi i marek<br/>Dostępne opcje: people, keywords, annotations, brands, sentiments, transcript, search.<br/>Brak obsługi przez adres URL w przypadku parametru version=2<br/><br/>**Uwaga:** Parametr adresu URL widżetów nie jest obsługiwany w wersji 2. |
|`locale`|Kod w języku krótkim|Steruje językiem usługi Insights. Wartość domyślna to `en`. Na przykład: `language=de`.|
|`tab`|Domyślna wybrana karta|Kontroluje kartę szczegółowe dane, która jest renderowana domyślnie. `tab=timeline`renderuje szczegółowe informacje z wybraną kartą oś czasu.|

### <a name="player-widget"></a>Widżet Player

Widżet **Player** (Odtwarzacz) umożliwia przesyłanie strumieniowe wideo z adaptacyjną szybkością transmisji. Widżet Player obsługuje następujące parametry opcjonalne adresu URL:

|Name (Nazwa)|Definicja|Opis|
|---|---|---|
|`t`|Sekund od początku|Sprawia, że odtwarzanie rozpoczyna się od podanego punktu w czasie.<br/>Przykład: `t=60`.|
|`captions`|Kod języka|Pobiera napisy w danym języku podczas ładowania widżetu w celu udostępnienia ich w menu napisów.<br/>Przykład: `captions=en-US`.|
|`showCaptions`|Wartość logiczna|Powoduje załadowanie już włączonych napisów.<br/>Przykład: `showCaptions=true`.|
|`type`||Aktywuje skórkę odtwarzacza audio (część wideo jest usuwana).<br/>Przykład: `type=audio`.|
|`autoplay`|Wartość logiczna|Wskazuje, czy należy rozpocząć odtwarzanie po załadowaniu pliku wideo (wartość domyślna to true).<br/>Przykład: `autoplay=false`.|
|`language`|Kod języka|Określa język widżetu Player (wartość domyślna to en-US)<br/>Przykład: `language=de-DE`.|

### <a name="editor-widget"></a>Widżet edytora 

Widżet **edytora** pozwala tworzyć nowe projekty i zarządzać szczegółowymi informacjami wideo.

|Name (Nazwa)|Definicja|Opis|
|---|---|---|
|`accessToken`<sup>*</sup>|String|`accessToken` Parametr jest wymagany w przypadku korzystania z widżetu edytora.<br/>Token dostępu zapewnia dostęp do wideo tylko w ramach konta, które jest używane do osadzenia widżetu. |
|`language`|Kod języka|Określa język widżetu Player (wartość domyślna to en-US)<br/>Przykład: `language=de-DE`.|
|`locale`|Kod w języku krótkim|Steruje językiem usługi Insights. Wartość domyślna to `en`. Na przykład: `language=de`.|

<sup>*</sup>Właściciel powinien `accessToken` zachować ostrożność. 

## <a name="embedding-public-content"></a>Osadzanie zawartości publicznej

1. Przejdź do witryny internetowej [Video Indexer](https://www.videoindexer.ai/) i zaloguj się.
2. Kliknij film wideo, z którym chcesz pracować.
3. Kliknij przycisk „embed” (osadź) znajdujący się poniżej pliku wideo.

    ![Widżet](./media/video-indexer-embed-widgets/video-indexer-widget01.png)

    Po kliknięciu przycisku na ekranie pojawi się okno modalne osadzania, w którym możesz wybrać widżet do osadzenia w aplikacji.
    Wybranie widżetu (**poznawcze informacje**, **odtwarzacz**lub **Edytor**) powoduje wygenerowanie kodu osadzonego do wklejenia w aplikacji.
 
4. Wybierz odpowiedni typ widżetu (**poznawcze informacje**, **odtwarzacz**lub **Edytor**).
5. Skopiuj kod osadzania i dodaj go do swojej aplikacji. 

    ![Widżet](./media/video-indexer-embed-widgets/video-indexer-widget02.png)

> [!NOTE]
> Jeśli masz problemy z udostępnianiem adresów URL wideo, spróbuj dodać parametr "Location" do linku. Parametr powinien być ustawiony na [regiony platformy Azure, w których istnieje Video Indexer](regions.md). Na przykład `https://www.videoindexer.ai/accounts/00000000-0000-0000-0000-000000000000/videos/b2b2c74b8e/?location=trial`.

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

### <a name="embed-widgets-in-your-application--blog-recommended"></a>Osadź widżety w aplikacji/blogu (zalecane) 

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
3. Zaimplementuj komunikację dla odtwarzacza z nasłuchiwaniem w oczekiwaniu na zdarzenie „message”. Przykład:

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

Jeśli osadzisz Video Indexer Insights przy użyciu własnych [Azure Media Player](https://aka.ms/azuremediaplayer), możesz użyć metody **GetVttUrl** , aby uzyskać napisy (napisy). Możesz również wywołać metodę JavaScript **getSubtitlesUrl** z wtyczki AMP usługi Video Indexer (jak pokazano wcześniej). 

## <a name="customizing-embeddable-widgets"></a>Dostosowywanie osadzalnych widżetów

### <a name="cognitive-insights-widget"></a>Widżet Cognitive Insights

Możesz wybrać typy szczegółowych informacji, określając je jako wartość następującego parametru adresu URL dodanego do kodu osadzania, który otrzymujesz (z interfejsu API lub z aplikacji sieci Web): `&widgets=<list of wanted widgets>`.

Możliwe wartości: people, keywords, sentiments, transcript, search.

Na przykład jeśli chcesz osadzić widżet zawierający tylko osoby i przeszukać szczegółowe informacje, adres URL osadzania elementu iframe będzie wyglądać następująco:

`https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?widgets=people,search`

Tytuł okna elementu IFRAME można również dostosować, podając `&title=<YourTitle>` adres URL elementu iframe. (Spowoduje to dostosowanie wartości \<title> elementu html).
    
Na przykład jeśli chcesz nadać polu elementu IFRAME tytuł "MyInsights", adres URL będzie wyglądać następująco:

`https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?title=MyInsights`

Ta opcja ma zastosowanie tylko w przypadkach, gdy trzeba otworzyć szczegółowe informacje w nowym oknie.

### <a name="player-widget"></a>Widżet Player

W przypadku osadzania widżetu Player usługi Video Indexer można wybrać rozmiar odtwarzacza, określając rozmiar elementu iframe.

Przykład:

`<iframe width="640" height="360" src="https://www.videoindexer.ai/embed/player/<accountId>/<videoId>/" frameborder="0" allowfullscreen />`

Domyślnie widżet Player usługi Video Indexer będzie mieć automatycznie generowane napisy oparte na transkrypcji nagrania wideo wyodrębnionego z pliku wideo w języku źródłowym, który został wybrany podczas przekazywania pliku wideo.

Jeśli chcesz osadzić przy użyciu innego języka, możesz dodać `&captions=< Language | ”all” | “false” >` do adresu URL odtwarzacza osadzania lub "wszystkie" jako wartość, jeśli chcesz mieć wszystkie dostępne etykiety.
Jeśli chcesz, aby podpisy były wyświetlane domyślnie, możesz przekazać `&showCaptions=true`.

Adres URL osadzania będzie wyglądać następująco: 

`https://www.videoindexer.ai/embed/player/<accountId>/<videoId>/?captions=italian`

Jeśli chcesz wyłączyć napisy, jako wartość parametru „captions” możesz przekazać wartość „false”.

Automatyczne odtwarzanie — domyślnie odtwarzacz rozpocznie odtwarzanie pliku wideo. Możesz to zmienić, przekazując w powyższym adresie URL osadzania parametr &autoplay=false.

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać informacje o sposobie wyświetlania i edytowania szczegółowych informacji usługi Video Indexer, zobacz [ten](video-indexer-view-edit.md) artykuł.

Zapoznaj się również z [CodePen indeksatora wideo](https://codepen.io/videoindexer/pen/eGxebZ).
