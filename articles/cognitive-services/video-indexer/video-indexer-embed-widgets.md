---
title: Osadzanie Azure wideo indeksatora elementów widget w aplikacji | Dokumentacja firmy Microsoft
description: ''
services: cognitive services
documentationcenter: ''
author: juliako
manager: erikre
ms.service: cognitive-services
ms.topic: article
ms.date: 04/04/2018
ms.author: juliako
ms.openlocfilehash: 8da06253c58a2de474e879f18013fa1e57f5668d
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35349709"
---
# <a name="embed-video-indexer-widgets-into-your-applications"></a>Osadzanie wideo indeksatora elementów widget w aplikacji

Indeksator wideo obsługuje osadzania dwa typy elementy widget do aplikacji: **kognitywnych Insights** i **Player**. 

* A **kognitywnych Insights** widżet obejmuje wszystkie visual szczegółowe informacje, które zostały wyodrębnione wideo indeksowania procesu. 
    Widżet insights obsługuje następujące opcjonalne parametry adresu URL:

    |Name (Nazwa)|Definicja|Opis|
    |---|---|---|
    |elementy widget|Ciągów rozdzielone przecinkami|Umożliwia określenie szczegółowych danych, który ma być renderowany. <br/>Przykład: **elementy widget = osób, marek** będą zawierały tylko osoby i marek insights interfejsu użytkownika<br/>Dostępne opcje: osób, słowa kluczowe, adnotacje, marek, opinie, zapis, wyszukiwanie | 
* A **Player** elementu widget umożliwia strumienia wideo przy użyciu adaptacyjnej szybkości transmisji.

    Widżet player obsługuje następujące opcjonalne parametry adresu URL:

    |Name (Nazwa)|Definicja|Opis|
    |---|---|---|
    |t|Sekund od rozpoczęcia|Sprawia, że początkowy player odtwarzanie w danym momencie punktu.<br/>Przykład: t = 60|
    |podpisy|Kod języka|Pobiera podpisu w danym języku podczas ładowania mają być dostępne w menu podpisy elementu widget.<br/>Przykład: etykiety = en-Us|
    |showCaptions|Wartość logiczna|Sprawia, że player załadować z napisami już włączone.<br/>Przykład: showCaptions = true|
    |type||Aktywuje karnacji odtwarzacz audio (części wideo jest usunięte).<br/>Przykład: wpisz = audio|
    |Autoodtwarzanie|Wartość logiczna|Zdecyduj, czy gracz ma się rozpoczynać odtwarzanie wideo po załadowaniu (wartość domyślna to true).<br/>Przykład: Autoodtwarzanie = false|
    |język|Kod języka|Kontroli odtwarzacz steruje lokalizacji (wartość domyślna to en US)<br/>Przykład: język = de-DE|

## <a name="embedding-public-content"></a>Osadzanie zawartości publicznej

1. Zaloguj się do Twojego [indeksatora wideo](https://api-portal.videoindexer.ai/) konta. 
2. Kliknij przycisk "Osadź", która pojawia się poniżej wideo.

    ![Element widget](./media/video-indexer-embed-widgets/video-indexer-widget01.png)

    Po kliknięciu przycisku modalne osadzania będą wyświetlane na ekranie, którego można określić, jakie widget chcesz osadzania w aplikacji.
    Wybranie elementu widget (**Player** lub **kognitywnych Insights**), generuje osadzonego kodu można wkleić do aplikacji.
 
3. Wybierz typ elementu widget ma (**kognitywnych Insights** lub **Player**).
4. Skopiuj kod osadzania, a następnie dodać do aplikacji. 

    ![Element widget](./media/video-indexer-embed-widgets/video-indexer-widget02.png)

## <a name="embedding-private-content"></a>Osadzanie prywatną zawartość

Można pobrać osadzić kody z osadzić wyskakujące okienka (jak pokazano w poprzedniej sekcji) dla **publicznego** tylko wideo. 

Jeśli chcesz osadzić **prywatnej** wideo, trzeba przekazać token dostępu w **iframe**w **src** atrybutu:

     https://www.videoindexer.ai/embed/[insights | player]/<accountId>/<VideoId>/?accessToken=<accessToken>
    
Użyj [ **Get Insights Widget** ](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-insights-widget?) interfejsu API można pobrać zawartości elementu widget kognitywnych szczegółowych informacji, lub użyj [ **uzyskać Token dostępu wideo** ](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Video-Access-Token?) i dodać go jako Zapytanie param do adresu url, jak pokazano powyżej. Określ adres URL jako **iframe**w **src** wartość.

Jeśli chcesz podać insights funkcje edycji (posiadamy w naszej aplikacji sieci web) w Twojej osadzonych widget, należy przekazać token dostępu z uprawnieniami do edycji. Użyj [ **Get Insights Widget** ](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-insights-widget?) lub [ **uzyskać Token dostępu wideo** ](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Video-Access-Token?) z **& allowEdit = true**. 

## <a name="widgets-interaction"></a>Elementy widget interakcji

**Kognitywnych Insights** widżetu mogą prowadzić interakcję z wideo w swojej aplikacji. W tej sekcji przedstawiono sposób osiągnięcia interakcji.

![Element widget](./media/video-indexer-embed-widgets/video-indexer-widget03.png)

### <a name="cross-origin-communications"></a>Komunikacja między źródłami

Można pobrać elementy widget indeksatora wideo do komunikowania się z innymi składnikami, usługę indeksatora wideo wykonuje następujące czynności:

- Używa metody HTML5 komunikacji między źródłami **funkcji postMessage** i 
- Weryfikuje wiadomości między VideoIndexer.ai pochodzenia. 

Jeśli zdecydujesz się wdrożyć własny kod player i do integracji z **kognitywnych Insights** elementy widget, odpowiada sieci Sprawdź poprawność źródła komunikat, który pochodzi z VideoIndexer.ai.

### <a name="embed-both-types-of-widgets-in-your-application--blog-recommended"></a>Osadź oba rodzaje elementów widget w aplikacji / blog (zalecane) 

W tej sekcji pokazano, jak osiągnięcia interakcji między dwa widgety indeksatora wideo, użytkownik klika formant wgląd w aplikacji, odtwarzacza przechodzi do odpowiednich chwilę.

    <script src="https://breakdown.blob.core.windows.net/public/vb.widgets.mediator.js"></script> 

1. Kopiuj **Player** kod osadzania elementu widget.
2. Kopiuj **kognitywnych Insights** kod osadzania.
3. Dodaj [ **pliku mediatora** ](https://breakdown.blob.core.windows.net/public/vb.widgets.mediator.js) do obsługi komunikacji między dwa elementy widget:

    <script src="https://breakdown.blob.core.windows.net/public/vb.widgets.mediator.js"></script>

Teraz po kliknięciu formantu wgląd w swojej aplikacji odtwarzacza przechodzi do odpowiednich chwilę.

Aby uzyskać więcej informacji, zobacz [ten pokaz](https://api-portal.videoindexer.ai/demo-all-widgets).

### <a name="embed-the-cognitive-insights-widget-and-use-azure-media-player-to-play-the-content"></a>Osadź widget kognitywnych Insights i odtwarzania zawartości za pomocą usługi Azure Media Player

W tej sekcji przedstawiono sposób osiągnięcia interakcji między **kognitywnych Insights** elementu widget i przy użyciu wystąpienia usługi Azure Media Player [wtyczki AMP](https://breakdown.blob.core.windows.net/public/amp-vb.plugin.js).
 
1. Dodawanie wtyczki indeksatora wideo odtwarzacza AMP.

        <script src="https://breakdown.blob.core.windows.net/public/amp-vb.plugin.js"></script>


2. Wystąpienia usługi Azure Media Player z dodatku indeksatora wideo.

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

3. Kopiuj **kognitywnych Insights** kod osadzania.

Można teraz nawiązać połączenia z określonym odtwarzaczu multimedialnym Azure.

Aby uzyskać więcej informacji, zobacz [ten pokaz](https://api-portal.videoindexer.ai/demo-your-amp).

### <a name="embed-video-indexer-cognitive-insights-widget-and-use-your-own-player-could-be-any-player"></a>Osadź widget Insights kognitywnych indeksatora wideo i użyć własnych odtwarzacza (może być dowolny odtwarzacz)

Użycie własnego player, masz manipulowanie odtwarzacza zajmie się samodzielnie, aby osiągnąć komunikacji. 

1. Wstaw odtwarzacza wideo.

    Na przykład standardowego odtwarzacza HTML5

        <video id="vid1" width="640" height="360" controls autoplay preload>
           <source src="//breakdown.blob.core.windows.net/public/Microsoft%20HoloLens-%20RoboRaid.mp4" type="video/mp4" /> 
           Your browser does not support the video tag.
        </video>    

2. Osadź widget kognitywnych szczegółowych informacji.
3. Implementowanie komunikacji dla odtwarzacza przez nasłuchiwanie w zdarzeniu "komunikat". Na przykład:

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


Aby uzyskać więcej informacji, zobacz [ten pokaz](https://api-portal.videoindexer.ai/demo-your-player).

## <a name="adding-subtitles"></a>Dodawanie podpisów

Po osadzeniu insights indeksatora wideo z odtwarzacza AMP służy **GetVttUrl** metodę, aby pobrać napisy (napisów). Możesz także wywołać metodę javascript z wtyczki wideo indeksatora AMP **getSubtitlesUrl** (jak pokazano wcześniej). 

## <a name="customizing-embeddable-widgets"></a>Dostosowywanie osadzenia widżetów

### <a name="cognitive-insights-widget"></a>Widżet kognitywnych insights
Można wybrać typy insights ma przez określenie ich jako wartość do następującego parametru URL dodawane do kodu osadzania uzyskać (z interfejsu API lub aplikacji sieci web):

**& elementy widget =** \<listy widżetów odpowiedniej >

Możliwe wartości to: osoby, słowa kluczowe, opinie, zapis, wyszukiwanie.

Na przykład jeśli chcesz osadzić element widget zawierający tylko osoby i wyszukiwania insights elementu iframe osadzić adres URL będzie wyglądać następująco: https://www.videoindexer.ai/embed/insights/c4c1ad4c9a/?widgets=people, wyszukiwania

Można również dostosować tytuł okna iframe zapewniając **& Tytuł =** <YourTitle> do adresu url elementu iframe. (Będzie dostosować html \<title > wartości).
Na przykład, jeśli chcesz nadać okna iframe tytuł "MyInsights", adres url będzie wyglądać następująco: https://www.videoindexer.ai/embed/insights/c4c1ad4c9a/?title=MyInsights. Należy zauważyć, że ta opcja jest ważna tylko w przypadkach, gdy trzeba otworzyć szczegółowych danych w nowym oknie.

### <a name="player-widget"></a>Widżet Player
Po osadzeniu odtwarzacza wideo indeksatora można wybrać rozmiar odtwarzacza, określając rozmiar elementu iframe.

Na przykład:

    <iframe width="640" height="360" src="https://www.videoindexer.ai/embed/player/{id}” frameborder="0" allowfullscreen />

Przez domyślnego odtwarzacza wideo indeksator ma automatycznie generowanych napisy oparte na zapis wideo, który został wyodrębniony z wideo z językiem źródła, którego wybrano po wideo został przekazany.

Jeśli chcesz osadzić z inną wersją językową można dodać **& podpisy = < język | "wszystkie" | "false" >** URL player osadzania lub umieść "all" jako wartość, jeśli chcesz, aby wszystkie podpisy dostępne języki.
Jeśli podpisy mają być wyświetlane domyślnie można przekazać **& showCaptions = true**

Adres URL osadzania następnie będzie wyglądać następująco: https://www.videoindexer.ai/embed/player/9a296c6ec3/?captions=italian. Jeśli chcesz wyłączyć podpisy można przekazać jako wartość parametru podpisy "false".

Automatyczne odtwarzanie — domyślnie odtwarzacz rozpocznie się odtwarzanie wideo. Nie można przekazywanie & autoodtwarzania = false, aby adres URL osadzania powyżej.

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się, jak wyświetlić i edytować indeksator wideo szczegółowe informacje, zobacz [to](video-indexer-view-edit.md) artykułu.

## <a name="see-also"></a>Zobacz także

[Film poglądowy dotyczący indeksatora](video-indexer-overview.md)
