---
title: Wtyczka Smooth Streaming dla typu Open Source Media Framework
description: Dowiedz się, jak używać usługi Azure Media Services wtyczki Smooth Streaming dla firmy Adobe Open Source Media Framework.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: 6068151f-b6b0-4507-9346-f03416d3d572
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: c40d8b93a7487619cc94586c7e6b4cdc550435cd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60825595"
---
# <a name="how-to-use-the-microsoft-smooth-streaming-plugin-for-the-adobe-open-source-media-framework"></a>Jak używać zestawu Microsoft Smooth Streaming dodatek dla programu Adobe typu Open Source Media Framework  
## <a name="overview"></a>Omówienie
Dodatek Microsoft Smooth Streaming dla Otwórz źródła Media Framework w wersji 2.0 (SS dla platformy OSMF) rozszerza możliwości domyślne OSMF i dodaje Microsoft Smooth Streaming odtwarzanie zawartości dla nowych i istniejących odtwarzaczy OSMF. Wtyczka dodaje również możliwości odtwarzania Smooth Streaming do Strobe Media Playback (SMP).

SS dla platformy OSMF zawiera dwie wersje wtyczki:

* Statyczne wtyczki Smooth Streaming dla OSMF (SWC)
* Dynamiczne wtyczki Smooth Streaming dla OSMF (SWF)

W tym dokumencie przyjęto założenie, że czytelnik ma ogólne praktyczną wiedzę na temat technologii OSMF i OSMF wtyczek. Aby uzyskać więcej informacji na temat technologii OSMF można znaleźć w dokumentacji na [oficjalna witryna OSMF](http://osmf.org/).

### <a name="smooth-streaming-plugin-for-osmf-20"></a>Zestaw Smooth Streaming wtyczki dla OSMF w wersji 2.0
Wtyczka obsługuje ładowanie i odtwarzanie zawartości Smooth Streaming na żądanie dzięki następującym funkcjom:

* Odtwarzanie Smooth Streaming na żądanie (Odtwórz, Wstrzymaj, wyszukiwanie, Zatrzymaj)
* Odtwarzanie Live Smooth Streaming (odtwarzanie)
* Na żywo funkcji DVR (Wstrzymaj, wyszukiwanie, odtwarzania DVR Go-to-Live)
* Obsługa koderów-dekoderów wideo — H.264
* Obsługa dźwięku kodery-dekodery - adaptacyjnych kontrolek aplikacji
* Przełączanie przy użyciu technologii OSMF wbudowanym interfejsom API wielu języka audio
* Maksymalna liczba odtwarzania jakości zaznaczenie z wbudowanym interfejsom API OSMF
* Przyczepki napisy, za pomocą wtyczki podpisy OSMF
* Adobe&reg; Flash&reg; Player 11.4 lub nowszej.
* Ta wersja obsługuje tylko OSMF w wersji 2.0.

## <a name="supported-features-and-known-issues"></a>Obsługiwane funkcje i znane problemy
Aby uzyskać pełną listę obsługiwanych funkcji, nieobsługiwanych funkcji oraz znanych problemów, zobacz [w tym dokumencie](https://download.microsoft.com/download/3/1/B/31B63D97-574E-4A8D-BF8D-170744181724/Smooth_Streaming_Plugin_for_OSMF.pdf).

## <a name="loading-the-plugin"></a>Ładowanie wtyczki
Statycznie (w czasie kompilacji) można załadować wtyczki OSMF lub dynamicznie (w czasie wykonywania). Wtyczka Smooth Streaming dla OSMF pobierania zawiera wersje statycznych i dynamicznych.

* Ładowanie statyczne: Aby załadować statycznie, wymagany jest plik biblioteki statycznej (SWC). Statyczne wtyczki są dodawane jako odwołania do projektów i scalanie wewnątrz pliku wyjściowego w czasie kompilacji.
* Dynamiczne ładowanie: Aby załadować dynamicznie, wymagany jest prekompilowany plik (SWF). Dynamiczne dodatków plug-in są ładowane w czasie wykonywania i nie są uwzględnione w danych wyjściowych projektu. (Skompilowanych danych wyjściowych) Dynamiczne wtyczki mogą być ładowane przy użyciu protokołów HTTP i plików.

Aby uzyskać więcej informacji na temat ładowania statycznych i dynamicznych, można znaleźć w oficjalnej [strona wtyczek OSMF](http://osmf.org/dev/osmf/OtherPDFs/osmf_plugin_dev_guide.pdf).

### <a name="ss-for-osmf-static-loading"></a>SS dotyczące ładowania statyczne OSMF
Poniższy fragment kodu przedstawia sposób ładowania dodatku plug-in SS dla platformy OSMF statycznie i odtwarzanie wideo podstawowa przy użyciu technologii OSMF MediaFactory klasy. Przed dołączeniem SS dla OSMF kodu, upewnij się, że odwołania projektu zawiera wtyczki statyczne "MSAdaptiveStreamingPlugin-v1.0.3 osmf2.0.swc".

```
package 
{

    import com.microsoft.azure.media.AdaptiveStreamingPluginInfo;

    import flash.display.*;
    import org.osmf.media.*;
    import org.osmf.containers.MediaContainer;
    import org.osmf.events.MediaErrorEvent;
    import org.osmf.events.MediaFactoryEvent;
    import org.osmf.events.MediaPlayerStateChangeEvent;
    import org.osmf.layout.*;



    [SWF(width="1024", height="768", backgroundColor='#405050', frameRate="25")]
    public class TestPlayer extends Sprite
    {        
        public var _container:MediaContainer;
        public var _mediaFactory:DefaultMediaFactory;
        private var _mediaPlayerSprite:MediaPlayerSprite;


        public function TestPlayer( )
        {
            stage.quality = StageQuality.HIGH;

            initMediaPlayer();

        }

        private function initMediaPlayer():void
        {

            // Create the container (sprite) for managing display and layout
            _mediaPlayerSprite = new MediaPlayerSprite();    
            _mediaPlayerSprite.addEventListener(MediaErrorEvent.MEDIA_ERROR, onPlayerFailed);
            _mediaPlayerSprite.addEventListener(MediaPlayerStateChangeEvent.MEDIA_PLAYER_STATE_CHANGE, onPlayerStateChange);
            _mediaPlayerSprite.scaleMode = ScaleMode.NONE;
            _mediaPlayerSprite.width = stage.stageWidth;
            _mediaPlayerSprite.height = stage.stageHeight;
            //Adds the container to the stage
            addChild(_mediaPlayerSprite);

            // Create a mediafactory instance
            _mediaFactory = new DefaultMediaFactory();

            // Add the listeners for PLUGIN_LOADING
            _mediaFactory.addEventListener(MediaFactoryEvent.PLUGIN_LOAD,onPluginLoaded);
            _mediaFactory.addEventListener(MediaFactoryEvent.PLUGIN_LOAD_ERROR, onPluginLoadFailed );

            // Load the plugin class 
            loadAdaptiveStreamingPlugin( );  

        }

        private function loadAdaptiveStreamingPlugin( ):void
        {
            var pluginResource:MediaResourceBase;

            pluginResource = new PluginInfoResource(new AdaptiveStreamingPluginInfo( )); 
            _mediaFactory.loadPlugin( pluginResource ); 
        }

        private function onPluginLoaded( event:MediaFactoryEvent ):void
        {
            // The plugin is loaded successfully.
            // Your web server needs to host a valid crossdomain.xml file to allow plugin to download Smooth Streaming files.
        loadMediaSource("http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest")

        }

        private function onPluginLoadFailed( event:MediaFactoryEvent ):void
        {
            // The plugin is failed to load ...
        }


        private function onPlayerStateChange(event:MediaPlayerStateChangeEvent) : void
        {
            var state:String;

            state =  event.state;

            switch (state)
            {
                case MediaPlayerState.LOADING: 

                    // A new source is started to load.

                    break;

                case  MediaPlayerState.READY :   
                    // Add code to deal with Player Ready when it is hit the first load after a source is loaded. 

                    break;

                case MediaPlayerState.BUFFERING :

                    break;

                case  MediaPlayerState.PAUSED :
                    break;      
                // other states ...          
            }
        }

        private function onPlayerFailed(event:MediaErrorEvent) : void
        {
            // Media Player is failed .           
        }

        private function loadMediaSource(sourceURL : String):void 
        {
            // Take an URL of SmoothStreamingSource's manifest and add it to the page.

            var resource:URLResource= new URLResource( sourceURL );

            var element:MediaElement = _mediaFactory.createMediaElement( resource );
            _mediaPlayerSprite.scaleMode = ScaleMode.LETTERBOX;
            _mediaPlayerSprite.width = stage.stageWidth;
            _mediaPlayerSprite.height = stage.stageHeight;

            // Add the media element
            _mediaPlayerSprite.media = element;
        }     

    }
}
```


### <a name="ss-for-osmf-dynamic-loading"></a>SS dotyczące ładowania dynamiczne OSMF
Poniższy fragment kodu pokazuje, jak załadować wtyczki SS dla platformy OSMF dynamicznie i odtwarzać podstawową wideo za pomocą klasy OSMF MediaFactory. Przed dołączeniem SS dla OSMF kodu, skopiuj dodatek dynamicznej "MSAdaptiveStreamingPlugin-v1.0.3 osmf2.0.swf" do folderu projektu, jeśli chcesz załadować przy użyciu protokołu plików lub skopiuj w serwerze sieci web dla obciążenia HTTP. Nie ma potrzeby do dołączenia "MSAdaptiveStreamingPlugin-v1.0.3 osmf2.0.swc" odwołania do projektu.

{pakietu

    import flash.display.*;
    import org.osmf.media.*;
    import org.osmf.containers.MediaContainer;
    import org.osmf.events.MediaErrorEvent;
    import org.osmf.events.MediaFactoryEvent;
    import org.osmf.events.MediaPlayerStateChangeEvent;
    import org.osmf.layout.*;
    import flash.events.Event;
    import flash.system.Capabilities;


    //Sets the size of the SWF

    [SWF(width="1024", height="768", backgroundColor='#405050', frameRate="25")]
    public class TestPlayer extends Sprite
    {        
        public var _container:MediaContainer;
        public var _mediaFactory:DefaultMediaFactory;
        private var _mediaPlayerSprite:MediaPlayerSprite;


        public function TestPlayer( )
        {
            stage.quality = StageQuality.HIGH;
            initMediaPlayer();
        }

        private function initMediaPlayer():void
        {

            // Create the container (sprite) for managing display and layout
            _mediaPlayerSprite = new MediaPlayerSprite();    
            _mediaPlayerSprite.addEventListener(MediaErrorEvent.MEDIA_ERROR, onPlayerFailed);
            _mediaPlayerSprite.addEventListener(MediaPlayerStateChangeEvent.MEDIA_PLAYER_STATE_CHANGE, onPlayerStateChange);

            //Adds the container to the stage
            addChild(_mediaPlayerSprite);

            // Create a mediafactory instance
            _mediaFactory = new DefaultMediaFactory();

            // Add the listeners for PLUGIN_LOADING
            _mediaFactory.addEventListener(MediaFactoryEvent.PLUGIN_LOAD,onPluginLoaded);
            _mediaFactory.addEventListener(MediaFactoryEvent.PLUGIN_LOAD_ERROR, onPluginLoadFailed );

            // Load the plugin class 
            loadAdaptiveStreamingPlugin( );  

        }

        private function loadAdaptiveStreamingPlugin( ):void
        {
            var pluginResource:MediaResourceBase;
            var adaptiveStreamingPluginUrl:String;

            // Your dynamic plugin web server needs to host a valid crossdomain.xml file to allow loading plugins.

            adaptiveStreamingPluginUrl = "http://yourdomain/MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swf";
            pluginResource = new URLResource(adaptiveStreamingPluginUrl);
            _mediaFactory.loadPlugin( pluginResource ); 

        }

        private function onPluginLoaded( event:MediaFactoryEvent ):void
        {
            // The plugin is loaded successfully.

            // Your web server needs to host a valid crossdomain.xml file to allow plugin to download Smooth Streaming files.

    loadMediaSource("http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest")
        }

        private function onPluginLoadFailed( event:MediaFactoryEvent ):void
        {
            // The plugin is failed to load ...
        }


        private function onPlayerStateChange(event:MediaPlayerStateChangeEvent) : void
        {
            var state:String;

            state =  event.state;

            switch (state)
            {
                case MediaPlayerState.LOADING: 

                    // A new source is started to load.

                    break;

                case  MediaPlayerState.READY :   
                    // Add code to deal with Player Ready when it is hit the first load after a source is loaded. 

                    break;

                case MediaPlayerState.BUFFERING :

                    break;

                case  MediaPlayerState.PAUSED :
                    break;      
                // other states ...          
            }
        }

        private function onPlayerFailed(event:MediaErrorEvent) : void
        {
            // Media Player is failed .           
        }

        private function loadMediaSource(sourceURL : String):void 
        {
            // Take an URL of SmoothStreamingSource's manifest and add it to the page.

            var resource:URLResource= new URLResource( sourceURL );

            var element:MediaElement = _mediaFactory.createMediaElement( resource );
            _mediaPlayerSprite.scaleMode = ScaleMode.LETTERBOX;
            _mediaPlayerSprite.width = stage.stageWidth;
            _mediaPlayerSprite.height = stage.stageHeight;
            // Add the media element
            _mediaPlayerSprite.media = element;
        }     

    }
}

## <a name="strobe-media--playback-with-the-ss-odmf-dynamic-plugin"></a>Strobe odtwarzanie mediów za pomocą dodatek dynamicznej SS ODMF
Smooth Streaming dla OSMF wtyczki dynamicznych jest zgodny z [Strobe Media Playback (SMP)](http://osmf.org/strobe_mediaplayback.html). SS dla wtyczki OSMF służy do dodawania funkcji Smooth Streaming odtwarzanie zawartości do SMP. Aby to zrobić, należy skopiować "MSAdaptiveStreamingPlugin-v1.0.3 osmf2.0.swf" na serwerze sieci web, dla obciążenia HTTP wykonując następujące czynności:

1. Przeglądaj [strony Instalatora Strobe Media Playback](http://osmf.org/dev/2.0gm/setup.html). 
2. Ustaw src źródłem Smooth Streaming (np. http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest) 
3. Wprowadź żądane zmiany w konfiguracji, a następnie kliknij polecenie Podgląd i aktualizacja.
   
   **Uwaga** serwera zawartości sieci web wymaga crossdomain.xml prawidłowe. 
4. Skopiuj i Wklej kod do prostych strony HTML przy użyciu ulubionego edytora tekstu, takie jak w poniższym przykładzie:

        <html>
        <body>
        <object width="920" height="640"> 
        <param name="movie" value="http://osmf.org/dev/2.0gm/StrobeMediaPlayback.swf"></param>
        <param name="flashvars" value="src=http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest &autoPlay=true"></param>
        <param name="allowFullScreen" value="true"></param>
        <param name="allowscriptaccess" value="always"></param>
        <param name="wmode" value="direct"></param>
        <embed src="http://osmf.org/dev/2.0gm/StrobeMediaPlayback.swf" 
            type="application/x-shockwave-flash" 
            allowscriptaccess="always" 
            allowfullscreen="true" 
            wmode="direct" 
            width="920" 
            height="640" 
            flashvars=" src=http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest&autoPlay=true">
        </embed>
        </object>
        </body>
        </html>



1. Dodawanie wtyczki Smooth Streaming dla platformy OSMF kod osadzania i Zapisz.
   
        <html>
        <object width="920" height="640"> 
        <param name="movie" value="http://osmf.org/dev/2.0gm/StrobeMediaPlayback.swf"></param>
        <param name="flashvars" value="src=http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest&autoPlay=true&plugin_AdaptiveStreamingPlugin=http://yourdomain/MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swf&AdaptiveStreamingPlugin_retryLive=true&AdaptiveStreamingPlugin_retryInterval=10"></param>
        <param name="allowFullScreen" value="true"></param>
        <param name="allowscriptaccess" value="always"></param>
        <param name="wmode" value="direct"></param>
        <embed src="http://osmf.org/dev/2.0gm/StrobeMediaPlayback.swf" 
            type="application/x-shockwave-flash" 
            allowscriptaccess="always" 
            allowfullscreen="true" 
            wmode="direct" 
            width="920" 
            height="640" 
            flashvars="src=http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest&autoPlay=true&plugin_AdaptiveStreamingPlugin=http://yourdomain/MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swf&AdaptiveStreamingPlugin_retryLive=true&AdaptiveStreamingPlugin_retryInterval=10">
        </embed>
        </object>
        </html>
2. Zapisz stronę HTML i opublikować na serwerze sieci web. Przejdź na stronę sieci web opublikowanych przy użyciu Twojego ulubionego Flash&reg; Player włączone przeglądarka internetowa (Internet Explorer, Chrome, Firefox itd).
3. Korzystaj z funkcji Smooth Streaming zawartość wewnątrz Adobe&reg; Flash&reg; odtwarzacza.

Aby uzyskać więcej informacji na temat technologii OSMF ogólne ustawienia projektowania, zobacz official będzie przydatna [strony rozwoju technologii OSMF](http://osmf.org/resources.html).

## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Zobacz też
[Microsoft przesyłanej strumieniowo wtyczki dla aktualizacji OSMF](https://azure.microsoft.com/blog/2014/10/27/microsoft-adaptive-streaming-plugin-for-osmf-update/) 

