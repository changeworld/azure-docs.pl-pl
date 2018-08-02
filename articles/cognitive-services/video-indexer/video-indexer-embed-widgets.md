---
title: Osadzanie widżetów indeksatora wideo platformy Azure w aplikacjach | Dokumentacja firmy Microsoft
description: ''
services: cognitive services
documentationcenter: ''
author: juliako
manager: erikre
ms.service: cognitive-services
ms.topic: article
ms.date: 07/31/2018
ms.author: juliako
ms.openlocfilehash: 4ce1eedac69b06f491510be93fcfbdfbc879ff7e
ms.sourcegitcommit: d4c076beea3a8d9e09c9d2f4a63428dc72dd9806
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/01/2018
ms.locfileid: "39398136"
---
# <a name="embed-video-indexer-widgets-into-your-applications"></a>Osadzanie widżetów indeksatora wideo w aplikacjach

Indeksator wideo obsługuje dwa typy osadzanie widżetów w aplikacji: **Cognitive Insights** i **Player**. 

* A **Cognitive Insights** widżet obejmuje wszystkie wizualizację wyników analizy danych, które zostały wyodrębnione z filmu wideo, proces indeksowania. 
    Element widget insights obsługuje następujące opcjonalne parametry adresu URL:

    |Name (Nazwa)|Definicja|Opis|
    |---|---|---|
    |elementy widget|Ciągów rozdzielonych przecinkami|Umożliwia kontrolowanie szczegółowe informacje, które ma być renderowany. <br/>Przykład: **elementy widget = osób, marek** będą renderowane tylko osoby i marek insights interfejsu użytkownika<br/>Dostępne opcje: osób, słów kluczowych, adnotacje, marek, opinie, zapis, wyszukiwanie | 
* A **Player** widżet umożliwia przesyłanie strumieniowe wideo za pomocą adaptacyjnej szybkości transmisji.

    Element widget player obsługuje następujące opcjonalne parametry adresu URL:

    |Name (Nazwa)|Definicja|Opis|
    |---|---|---|
    |t|Sekundy od początku|Sprawia, że start player odtwarzanie w danym momencie punktu.<br/>Przykład: t = 60|
    |podpisy|Kod języka|Pobiera podpis w danym języku podczas widżet ładowania były dostępne w menu transkrypcji.<br/>Przykład: podpisy = en-Us|
    |showCaptions|Wartość logiczna|Sprawia, że player załadować dane przy użyciu transkrypcji już włączone.<br/>Przykład: showCaptions = true|
    |type||Aktywuje skórki odtwarzacz audio (część wideo jest usunięty).<br/>Przykład: wpisz = audio|
    |autoodtwarzania|Wartość logiczna|Zdecyduj, jeśli gracz powinien rozpocząć odtwarzanie wideo po załadowaniu (wartość domyślna to true).<br/>Przykład: autoodtwarzania = false|
    |Język|Kod języka|Kontrolki odtwarzacza kontroluje lokalizacji (wartość domyślna to en US)<br/>Przykład: język = de-DE.|

## <a name="embedding-public-content"></a>Osadzanie zawartości publicznej

1. Zaloguj się do Twojej [Video Indexer](https://api-portal.videoindexer.ai/) konta. 
2. Kliknij przycisk "osadzić", który pojawia się poniżej filmu wideo.

    ![Element widget](./media/video-indexer-embed-widgets/video-indexer-widget01.png)

    Po kliknięciu przycisku, modalne osadzania będą wyświetlane na ekranie, w którym można wybrać jakie widżet chcesz osadzania w aplikacji.
    Wybieranie elementu widget (**Player** lub **Cognitive Insights**), generuje osadzony kod można wkleić do aplikacji.
 
3. Wybierz typ elementu widget ma (**Cognitive Insights** lub **Player**).
4. Skopiuj kod osadzania i dodać do swojej aplikacji. 

    ![Element widget](./media/video-indexer-embed-widgets/video-indexer-widget02.png)

## <a name="embedding-private-content"></a>Osadzanie zawartości prywatnych

W przypadku uzyskać osadzenia kodów z osadzić wyskakujące okienka (jak pokazano w poprzedniej sekcji) dla **publicznych** tylko filmów wideo. 

Jeśli chcesz osadzić **prywatnej** wideo, trzeba przekazać token dostępu w **iframe**firmy **src** atrybutu:

     https://www.videoindexer.ai/embed/[insights | player]/<accountId>/<VideoId>/?accessToken=<accessToken>
    
Użyj [ **widżet uzyskiwanie szczegółowych informacji** ](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-insights-widget?) interfejsu API, aby pobrać zawartość elementu widget Cognitive szczegółowych informacji, lub użyj [ **Uzyskaj Token dostępu wideo** ](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Video-Access-Token?) i dodaj ją jako zapytań wysyłanych na adres url, jak pokazano powyżej. Określ adres URL jako **iframe**firmy **src** wartość.

Jeśli chcesz możliwości edycji szczegółowych informacji (takich jak mamy się w naszej aplikacji sieci web) w swojej osadzonego elementu widget, trzeba będzie przekazać token dostępu z uprawnieniami do edycji. Użyj [ **widżet uzyskiwanie szczegółowych informacji** ](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-insights-widget?) lub [ **Uzyskaj Token dostępu wideo** ](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Video-Access-Token?) z **& allowEdit = true**. 

## <a name="widgets-interaction"></a>Elementy widget interakcji

**Cognitive Insights** widżetu mogą wchodzić w interakcje z wideo w swojej aplikacji. W tej sekcji pokazano, jak osiągnąć ta interakcja.

![Element widget](./media/video-indexer-embed-widgets/video-indexer-widget03.png)

### <a name="cross-origin-communications"></a>Komunikacja między źródłami

Aby uzyskać Video Indexer elementy widget służące do komunikowania się z innymi składnikami, usługi Video Indexer wykonuje następujące czynności:

- Używa komunikacji między źródłami metoda HTML5 **funkcji postMessage** i 
- Sprawdza poprawność wiadomości między VideoIndexer.ai pochodzenia. 

Jeśli zdecydujesz się wdrożyć własny kod player i czy integracji z **Cognitive Insights** elementy widget, jest odpowiedzialny za sprawdzanie poprawności pochodzenia komunikat, który pochodzi z VideoIndexer.ai.

### <a name="embed-both-types-of-widgets-in-your-application--blog-recommended"></a>Osadzanie oba rodzaje elementów widget w aplikacji / blogu (zalecane) 

W tej sekcji pokazano, jak osiągnąć interakcji między dwa widgety Video Indexer więc gdy użytkownik kliknie kontrolkę wgląd w aplikacji, gracz przechodzi do momentu, istotne.

    <script src="https://breakdown.blob.core.windows.net/public/vb.widgets.mediator.js"></script> 

1. Kopiuj **Player** widżet kodu osadzania.
2. Kopiuj **Cognitive Insights** kod osadzania.
3. Dodaj [ **pliku mediatora** ](https://breakdown.blob.core.windows.net/public/vb.widgets.mediator.js) do obsługi komunikacji między dwa elementy widget:

    <script src="https://breakdown.blob.core.windows.net/public/vb.widgets.mediator.js"></script>

Teraz po kliknięciu formantu wgląd w swojej aplikacji odtwarzacza przechodzi do momentu, istotne.

Aby uzyskać więcej informacji, zobacz [tej wersji demonstracyjnej](https://api-portal.videoindexer.ai/demo-all-widgets).

### <a name="embed-the-cognitive-insights-widget-and-use-azure-media-player-to-play-the-content"></a>Osadzanie widżetów Cognitive szczegółowych informacji i usługi Azure Media Player umożliwia odtwarzanie zawartości

W tej sekcji pokazano, jak osiągnąć interakcji między **Cognitive Insights** widżet i wystąpienie usługi Azure Media Player za pomocą [wtyczki AMP](https://breakdown.blob.core.windows.net/public/amp-vb.plugin.js).
 
1. Dodaj wtyczkę Video Indexer odtwarzacza AMP.

        <script src="https://breakdown.blob.core.windows.net/public/amp-vb.plugin.js"></script>


2. Utwórz wystąpienie usługi Azure Media Player za pomocą wtyczki Video Indexer.

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

3. Kopiuj **Cognitive Insights** kod osadzania.

Można teraz nawiązać połączenia z usługi Azure Media Player.

Aby uzyskać więcej informacji, zobacz [tej wersji demonstracyjnej](https://api-portal.videoindexer.ai/demo-your-amp).

### <a name="embed-video-indexer-cognitive-insights-widget-and-use-your-own-player-could-be-any-player"></a>Osadzanie widżetów Cognitive informacji szczegółowych indeksatora wideo i używać własnych player (może to być dowolny odtwarzacz)

Jeśli używasz własnych odtwarzacza, trzeba zadbać o manipulowanie odtwarzacza samodzielnie, aby osiągnąć komunikacji. 

1. Wstaw odtwarzacza wideo.

    Na przykład standardowy odtwarzaczu HTML5

        <video id="vid1" width="640" height="360" controls autoplay preload>
           <source src="//breakdown.blob.core.windows.net/public/Microsoft%20HoloLens-%20RoboRaid.mp4" type="video/mp4" /> 
           Your browser does not support the video tag.
        </video>    

2. Osadzanie widżetów Cognitive szczegółowych informacji.
3. Implementuje komunikację za pomocą odtwarzacza przez nasłuchiwanie w zdarzeniu "message". Na przykład:

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


Aby uzyskać więcej informacji, zobacz [tej wersji demonstracyjnej](https://api-portal.videoindexer.ai/demo-your-player).

## <a name="adding-subtitles"></a>Dodawanie napisów

Jeśli osadzasz informacji szczegółowych indeksatora wideo z odtwarzaczem AMP, można użyć **GetVttUrl** metodę, aby uzyskać napisów (napisy). Można również wywołać metody javascript z wtyczki AMP indeksatora wideo **getSubtitlesUrl** (jak pokazano wcześniej). 

## <a name="customizing-embeddable-widgets"></a>Dostosowywanie elementów widget możliwego do osadzenia

### <a name="cognitive-insights-widget"></a>Element widget cognitive szczegółowych informacji
Możesz wybrać typy wglądu w szczegółowe dane mają, określając je jako wartość do następującego parametru URL dodawane do kodu osadzania otrzymasz (z interfejsu API lub aplikacji sieci web):

**& elementy widget =** \<listy widżetów odpowiedniej >

Możliwe wartości to: osób, słów kluczowych, opinie, zapis, wyszukiwanie.

Na przykład, jeśli chcesz osadzić elementu widget zawierający tylko insights osób i Wyszukiwanie elementu iframe adres URL osadzania będzie wyglądać następująco: https://www.videoindexer.ai/embed/insights/c4c1ad4c9a/?widgets=people, wyszukiwanie

Można również dostosować tytuł okna elementu iframe, zapewniając **& Tytuł =** <YourTitle> do adresu url elementu iframe. (Umożliwia dostosowywanie, html \<title > wartość).
Na przykład, jeśli chcesz nadać okna w elemencie iframe tytuł "MyInsights", adres url będzie wyglądać następująco: https://www.videoindexer.ai/embed/insights/c4c1ad4c9a/?title=MyInsights. Zwróć uwagę, czy ta opcja ma zastosowanie tylko w przypadkach, gdy potrzebujesz otwarcie szczegółowych danych w nowym oknie.

### <a name="player-widget"></a>Widgetu odtwarzania
Jeśli osadzania odtwarzacz Video Indexer można wybrać rozmiar odtwarzacza, określając rozmiar elementu iframe.

Na przykład:

    <iframe width="640" height="360" src="https://www.videoindexer.ai/embed/player/{id}” frameborder="0" allowfullscreen />

Domyślna usługa Video Indexer Player będą mieć automatycznie generowane napisy oparte na transkrypcjach film wideo, który został wyodrębniony z wideo za pomocą języka źródłowego, który został wybrany, gdy film wideo został przekazany.

Jeśli chcesz osadzić przy użyciu innego języka można dodać **& podpisy = < język | "wszystkie" | "false" >** adres URL odtwarzacza osadzania lub put "all" jako wartość, jeśli chcesz mieć wszystkie dostępne języki podpisów.
Jeśli chcesz, aby podpisów, które mają być wyświetlane domyślnie można przekazać **& showCaptions = true**

Adres URL osadzania następnie będzie wyglądać następująco: https://www.videoindexer.ai/embed/player/9a296c6ec3/?captions=italian. Jeśli chcesz wyłączyć transkrypcje można przekazać "false", jako wartość parametru transkrypcji.

Automatyczne odtwarzanie — domyślnie odtwarzacz zostanie uruchomiony, odtwarzanie filmu wideo. nie możesz przez przekazanie & autoodtwarzania = false, aby adres URL osadzania powyżej.

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać informacje o sposobie wyświetlania i edytowania informacji szczegółowych indeksatora wideo, zobacz [to](video-indexer-view-edit.md) artykułu.

## <a name="see-also"></a>Zobacz także

[Omówienie indeksatora wideo](video-indexer-overview.md)
