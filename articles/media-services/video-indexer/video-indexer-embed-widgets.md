---
title: Osadzanie widżetów indeksatora wideo w aplikacjach
titleSuffix: Azure Media Services
description: Dowiedz się, jak osadzać widżety indeksatora wideo w aplikacjach.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 03/26/2020
ms.author: juliako
ms.openlocfilehash: 4d92bd3709c5f56db0095ca1be2caa0e9c78b42f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80336831"
---
# <a name="embed-video-indexer-widgets-in-your-apps"></a>Osadzanie widżetów indeksatora wideo w aplikacjach

Z tego artykułu dowiesz się, jak osadzać widżety indeksatora wideo w aplikacjach. Indeksator wideo obsługuje osadzanie trzech typów widżetów w aplikacjach: *Cognitive Insights*, *Player*i *Editor*.

Począwszy od wersji 2, podstawowy adres URL widżetu obejmuje region określonego konta. Na przykład konto w regionie Zachodnie stany `https://wus2.videoindexer.ai/embed/insights/...`USA generuje: .

## <a name="widget-types"></a>Typy widżetów

### <a name="cognitive-insights-widget"></a>Widżet Cognitive Insights

Widżet Cognitive Insights (Szczegółowe informacje) zawiera wszystkie szczegóły wizualne wyodrębnione z filmu wideo w procesie indeksowania. Widżet Usługi Cognitive Insights obsługuje następujące opcjonalne parametry adresu URL:

|Nazwa|Definicja|Opis|
|---|---|---|
|`widgets` | Ciągi rozdzielone przecinkami | Umożliwia kontrolowanie szczegółowych informacji, które mają być renderowane.<br/>Przykład: `https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?widgets=people,keywords` renderuje tylko osoby i słowa kluczowe Statystyki interfejsu użytkownika.<br/>Dostępne opcje: ludzie, animowaneSchary, słowa kluczowe, etykiety, uczucia, emocje, tematy, klatki kluczowe, transkrypcja, ocr, głośniki, sceny i namedEntities.|
|`controls`|Ciągi rozdzielone przecinkami|Umożliwia sterowanie formantami, które mają być renderowane.<br/>Przykład: `https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?controls=search,download` renderuje tylko opcję wyszukiwania i przycisk pobierania.<br/>Dostępne opcje: wyszukiwanie, pobieranie, ustawienia wstępne, język.|
|`language`|Krótki kod języka (nazwa języka)|Steruje językiem szczegółowych informacji.<br/>Przykład: `https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?language=es-es` <br/>Lub`https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?language=spanish`|
|`locale` | Krótki kod języka | Steruje językiem interfejsu użytkownika. Wartością domyślną jest `en`. <br/>Przykład: `locale=de`.|
|`tab` | Domyślna wybrana karta | Steruje kartą **Statystyka,** która jest renderowana domyślnie. <br/>Przykład: `tab=timeline` renderuje statystyki z wybraną kartą **Oś czasu.**|

### <a name="player-widget"></a>Widżet Player

Za pomocą widżetu Odtwarzacz można przesyłać strumieniowo wideo za pomocą adaptacyjnej szybkości transmisji bitów. Widżet Player obsługuje następujące opcjonalne parametry adresu URL.

|Nazwa|Definicja|Opis|
|---|---|---|
|`t` | Sekundy od początku | Sprawia, że gracz zaczyna grać od określonego punktu czasu.<br/> Przykład: `t=60`. |
|`captions` | Kod języka | Pobiera podpis w określonym języku podczas ładowania widżetu, aby był dostępny w menu **Podpisy.**<br/> Przykład: `captions=en-US`. |
|`showCaptions` | Wartość logiczna | Powoduje załadowanie już włączonych napisów.<br/> Przykład: `showCaptions=true`. |
|`type`| | Aktywuje skórkę odtwarzacza audio (część wideo zostanie usunięta).<br/> Przykład: `type=audio`. |
|`autoplay` | Wartość logiczna | Wskazuje, czy odtwarzacz powinien rozpocząć odtwarzanie wideo po załadowaniu. Wartością domyślną jest `true`.<br/> Przykład: `autoplay=false`. |
|`language` | Kod języka | Steruje językiem gracza. Wartością domyślną jest `en-US`.<br/>Przykład: `language=de-DE`.|

### <a name="editor-widget"></a>Widżet Edytor

Za pomocą widżetu Edytor można tworzyć nowe projekty i zarządzać statystykami wideo. Widżet Edytor obsługuje następujące opcjonalne parametry adresu URL.

|Nazwa|Definicja|Opis|
|---|---|---|
|`accessToken`<sup>*</sup> | Ciąg | Zapewnia dostęp do filmów, które znajdują się tylko na koncie, które służy do osadzania widżetu.<br> Widżet `accessToken` Edytor wymaga parametru. |
|`language` | Kod języka | Steruje językiem gracza. Wartością domyślną jest `en-US`.<br/>Przykład: `language=de-DE`. |
|`locale` | Krótki kod języka | Steruje językiem szczegółowych informacji. Wartością domyślną jest `en`.<br/>Przykład: `language=de`. |

<sup>*</sup>Właściciel powinien `accessToken` zachować ostrożność.

## <a name="embedding-public-content"></a>Osadzanie zawartości publicznej

1. Zaloguj się w witrynie [indeksatora wideo.](https://www.videoindexer.ai/)
2. Wybierz film, z którego chcesz pracować.
3. Wybierz przycisk **Osadź,** który pojawi się pod filmem.

    ![Przycisk Osadzaj w indeksatorze wideo](./media/video-indexer-embed-widgets/video-indexer-widget01.png)

    Po wybraniu przycisku **Osadzanie** możesz wybrać widżet, który chcesz osadzić w aplikacji.
4. Wybierz odpowiedni typ widżetu **(Cognitive Insights,** **Player**lub **Editor**).
 
5. Skopiuj kod osadzania, a następnie dodaj go do aplikacji.

    ![Kod osadzania dla aplikacji — indeksator wideo](./media/video-indexer-embed-widgets/video-indexer-widget02.png)

> [!NOTE]
> Jeśli masz problemy z udostępnianiem adresów URL wideo, dodaj ten `location` parametr do łącza. Parametr powinien być ustawiony na [regiony platformy Azure, w których istnieje indeksator wideo.](regions.md) Na przykład: `https://www.videoindexer.ai/accounts/00000000-0000-0000-0000-000000000000/videos/b2b2c74b8e/?location=trial`.

## <a name="embedding-private-content"></a>Osadzanie zawartości prywatnej

Aby osadzić prywatny film wideo, musisz przekazać `src` token dostępu w atrybucie elementu iframe:

`https://www.videoindexer.ai/embed/[insights | player]/<accountId>/<videoId>/?accessToken=<accessToken>`
    
Aby uzyskać zawartość widżetu Usługi Cognitive Insights, użyj jednej z następujących metod:

- Interfejs API [widżetów Pobierz statystyki.](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Video-Insights-Widget?&pattern=widget)<br/>
- [Token Pobierz dostęp do wideo](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Get-Video-Access-Token?). Dodaj go jako parametr kwerendy do adresu URL. Określ ten `src` adres URL jako wartość elementu iframe, jak pokazano wcześniej.

Aby zapewnić funkcje wglądu do edycji w osadzonym widżecie, należy przekazać token dostępu zawierający uprawnienia do edycji. Użyj [widżetu Pobierz statystyki](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Video-Insights-Widget?&pattern=widget) lub pobierz token dostępu do [wideo](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Video-Access-Token?) za pomocą programu `&allowEdit=true`.

## <a name="widgets-interaction"></a>Interakcje z widżetami

Widżet Usługi Cognitive Insights może wchodzić w interakcje z klipem wideo w aplikacji. W tej sekcji pokazano, jak zrealizować taką interakcję.

![Indeksator wideo widżetów usługi Cognitive Insights](./media/video-indexer-embed-widgets/video-indexer-widget03.png)

### <a name="cross-origin-communications"></a>Komunikacja między źródłami

Aby uzyskać widżety indeksatora wideo do komunikowania się z innymi składnikami, usługa indeksatora wideo:

- Używa metody `postMessage`komunikacji krzyżowej HTML5 .
- weryfikuje komunikaty w źródle videoIndexer.ai.

Jeśli zaimplementujesz własny kod odtwarzacza i integrujesz się z widżetami usługi Cognitive Insights, twoim obowiązkiem jest sprawdzenie źródła wiadomości, która pochodzi z VideoIndexer.ai.

### <a name="embed-widgets-in-your-app-or-blog-recommended"></a>Osadzanie widżetów w aplikacji lub blogu (zalecane)

W tej sekcji pokazano, jak osiągnąć interakcję między dwoma widżetami indeksatora wideo, aby po wybraniu przez użytkownika kontroli wglądu w aplikacji odtwarzacz przeskakiwać do odpowiedniego momentu.

1. Skopiuj kod osadzania widżetu Player.
2. Skopiuj kod osadzania widżetu Cognitive Insights.
3. Dodaj [plik mediatora](https://breakdown.blob.core.windows.net/public/vb.widgets.mediator.js) do obsługi komunikacji między dwoma widżetami:<br/> 
`<script src="https://breakdown.blob.core.windows.net/public/vb.widgets.mediator.js"></script>`

Teraz, gdy użytkownik wybierze kontrolę wglądu w aplikacji, gracz przeskakuje do odpowiedniego momentu.

Aby uzyskać więcej informacji, zobacz [indeksator wideo - Osadź oba widżety demo](https://codepen.io/videoindexer/pen/NzJeOb).

### <a name="embed-the-cognitive-insights-widget-and-use-azure-media-player-to-play-the-content"></a>Osadzanie widżetu Cognitive Insights i odtwarzanie zawartości za pomocą usługi Azure Media Player

W tej sekcji pokazano, jak osiągnąć interakcję między widżetem usługi Cognitive Insights a wystąpieniem programu Azure Media Player przy użyciu [wtyczki AMP.](https://breakdown.blob.core.windows.net/public/amp-vb.plugin.js)

1. Dodaj wtyczkę indeksatora wideo dla odtwarzacza AMP:<br/> `<script src="https://breakdown.blob.core.windows.net/public/amp-vb.plugin.js"></script>`
2. Tworzenie wystąpienia programu Azure Media Player za pomocą wtyczki indeksatora wideo.

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
            syncLanguage: true,
            location: "trial" /* location option for paid accounts (default is trial) */
            });

            // Set the source dynamically.
            initSource.call(this);
        });

3. Skopiuj kod osadzania widżetu Cognitive Insights.

Teraz możesz komunikować się z programem Azure Media Player.

Aby uzyskać więcej informacji, zobacz [prezentację usługi Azure Media Player + VI Insights](https://codepen.io/videoindexer/pen/rYONrO).

### <a name="embed-the-video-indexer-cognitive-insights-widget-and-use-a-different-video-player"></a>Osadzanie widżetu Aplikacji Cognitive Insights indeksatora wideo i używanie innego odtwarzacza wideo

Jeśli używasz odtwarzacza wideo innego niż Azure Media Player, należy ręcznie manipulować odtwarzacz wideo, aby osiągnąć komunikację.

1. Wstaw odtwarzacz wideo.

    Na przykład standardowy odtwarzacz HTML5:

        <video id="vid1" width="640" height="360" controls autoplay preload>
           <source src="//breakdown.blob.core.windows.net/public/Microsoft%20HoloLens-%20RoboRaid.mp4" type="video/mp4" /> 
           Your browser does not support the video tag.
        </video>    

2. Osadź widżet Cognitive Insights.
3. Zaimplementuj komunikację dla odtwarzacza z nasłuchiwaniem w oczekiwaniu na zdarzenie „message”. Przykład:

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

Aby uzyskać więcej informacji, zobacz [prezentację usługi Azure Media Player + VI Insights](https://codepen.io/videoindexer/pen/YEyPLd).

## <a name="adding-subtitles"></a>Dodawanie napisów

Jeśli osadzasz szczegółowe informacje indeksatora wideo za pomocą `GetVttUrl` własnego programu Azure Media [Player,](https://aka.ms/azuremediaplayer)możesz użyć tej metody, aby uzyskać podpisy kodowane (napisy). Można również wywołać metodę JavaScript z wtyczki `getSubtitlesUrl` Video Indexer AMP (jak pokazano wcześniej).

## <a name="customizing-embeddable-widgets"></a>Dostosowywanie osadzalnych widżetów

### <a name="cognitive-insights-widget"></a>Widżet Cognitive Insights

Można wybrać typy szczegółowych informacji, które chcesz. Aby to zrobić, należy określić je jako wartość do następującego parametru adresu URL, który jest dodawany `&widgets=<list of wanted widgets>`do kodu osadzania, który otrzymujesz (z interfejsu API lub z aplikacji sieci web): .

Możliwe wartości to: **ludzie**, **słowa kluczowe**, **uczucia**, **transkrypcja**i **wyszukiwanie**.

Jeśli na przykład chcesz osadzić widżet zawierający tylko osoby i statystyki wyszukiwania, adres URL osadzania elementu iframe będzie wyglądał następująco:

`https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?widgets=people,search`

Tytuł okna elementu iframe można również dostosować, podając `&title=<YourTitle>` adres URL elementu iframe. (Dostosowuje tytuł HTML \<> wartość).

Jeśli na przykład chcesz nadać okno iframe tytuł "MyInsights", adres URL będzie wyglądał następująco:

`https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?title=MyInsights`

Ta opcja ma zastosowanie tylko w przypadkach, gdy trzeba otworzyć szczegółowe informacje w nowym oknie.

### <a name="player-widget"></a>Widżet Player

W przypadku osadzania widżetu Player usługi Video Indexer można wybrać rozmiar odtwarzacza, określając rozmiar elementu iframe.

Przykład:

`<iframe width="640" height="360" src="https://www.videoindexer.ai/embed/player/<accountId>/<videoId>/" frameborder="0" allowfullscreen />`

Domyślnie odtwarzacz indeksatora wideo ma automatycznie generowane napisy, które są oparte na transkrypcji wideo. Transkrypcja jest wyodrębniona z filmu z językiem źródłowym wybranym podczas przesyłania filmu.

Jeśli chcesz osadzić w innym języku, `&captions=< Language | "all" | "false" >` możesz dodać go do adresu URL osadzania gracza. Jeśli chcesz podpisy we wszystkich dostępnych językach, użyj wartości `all`. Jeśli chcesz, aby napisy były wyświetlane domyślnie, możesz przekazać program `&showCaptions=true`.

Adres URL osadzania będzie wyglądał następująco:

`https://www.videoindexer.ai/embed/player/<accountId>/<videoId>/?captions=italian`

Jeśli chcesz wyłączyć podpisy, możesz `captions` przekazać wartość `false`parametru jako .

#### <a name="autoplay"></a>Autoodtwarzanie
Domyślnie odtwarzacz rozpocznie odtwarzanie wideo. Możesz nie przechodzić `&autoplay=false` do poprzedniego adresu URL osadzania.

## <a name="code-samples"></a>Przykłady kodu

Zobacz [przykładowe repozytorium kodu,](https://github.com/Azure-Samples/media-services-video-indexer/tree/master/Widgets) które zawiera przykłady interfejsu API i widżetów indeksatora wideo:

| Plik/folder                       | Opis                                |
|-----------------------------------|--------------------------------------------|
| `azure-media-player`              | Ładowanie wideo indeksatora wideo w niestandardowym programie Azure Media Player.                        |
| `azure-media-player-vi-insights`  | Osadź usługę VI Insights za pomocą niestandardowego programu Azure Media Player.                             |
| `control-vi-embedded-player`      | Osadź odtwarzacz VI i kontroluj go z zewnątrz.                                    |
| `custom-index-location`           | Osadzanie usługi VI Insights z niestandardowej lokalizacji zewnętrznej (może być klientem obiektu blob).     |
| `embed-both-insights`             | Podstawowe wykorzystanie vi insights zarówno odtwarzacza i spostrzeżeń.                            |
| `embed-insights-with-AMP`         | Osadź widżet USŁUGI VI Insights za pomocą niestandardowego programu Azure Media Player.                      |
| `customize-the-widgets`           | Osadź widżety VI z niestandardowymi opcjami.                                     |
| `embed-both-widgets`              | Osadź ODTWARZACZ VI i Insights i komunikuj się między nimi.                      |
| `url-generator`                   | Generuje niestandardowy adres URL osadzania widżetów na podstawie opcji określonych przez użytkownika.             |
| `html5-player`                    | Osadź vi insights z domyślnym odtwarzaczem wideo HTML5.                           |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać informacje dotyczące wyświetlania i edytowania statystyk indeksatora wideo, zobacz [Wyświetlanie i edytowanie statystyk indeksatora wideo](video-indexer-view-edit.md).

Ponadto, sprawdź [Video indexer CodePen](https://codepen.io/videoindexer/pen/eGxebZ).
