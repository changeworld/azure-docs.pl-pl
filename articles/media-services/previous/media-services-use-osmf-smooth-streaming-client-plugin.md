---
title: Płynnego przesyłania strumieniowego dodatek plug-in dla typu Open Source Media Framework
description: Dowiedz się, jak używać usługi Azure Media Services Smooth Streaming wtyczki dla otwartej struktury nośnika źródłowego programu Adobe.
services: media-services
documentationcenter: ''
author: juliako
manager: cfowler
editor: ''
ms.assetid: 6068151f-b6b0-4507-9346-f03416d3d572
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/26/2016
ms.author: juliako
ms.openlocfilehash: 6a4ae0697e59eb00e216fc29b154408e4d7a99b8
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
---
# <a name="how-to-use-the-microsoft-smooth-streaming-plugin-for-the-adobe-open-source-media-framework"></a>Jak używać Smooth Microsoft przesyłania strumieniowego dodatek dla programu Adobe typu Open Source Media Framework
## <a name="overview"></a>Przegląd
Dodatek Microsoft Smooth Streaming dla Otwórz źródła Media Framework w wersji 2.0 (SS dla OSMF) rozszerza możliwości domyślne OSMF i dodaje Microsoft Smooth Streaming odtwarzanie zawartości dla nowych i istniejących odtwarzaczy OSMF. Wtyczka dodaje również funkcje Smooth Streaming odtwarzania do odtwarzania multimediów światła (SMP).

SS dla OSMF obejmuje dwie wersje dodatku:

* Statyczne Smooth Streaming dodatek plug-in dla OSMF (SWC)
* Dynamiczne Smooth Streaming dodatek plug-in dla OSMF (SWF)

Tym dokumencie przyjęto założenie, że czytelnik ma ogólne praktyczną znajomość OSMF i OSMF dodatków plug-in. Aby uzyskać więcej informacji na temat OSMF można znaleźć w dokumentacji na [oficjalna witryna OSMF](http://osmf.org/).

### <a name="smooth-streaming-plugin-for-osmf-20"></a>Wtyczka Smooth Streaming OSMF 2.0
Wtyczka obsługuje ładowania i odtwarzania zawartości Smooth Streaming na żądanie przy użyciu następujących funkcji:

* Odtwarzanie Smooth Streaming na żądanie (Wstrzymaj, wyszukiwania, Zatrzymaj odtwarzanie)
* Odtwarzanie Live Smooth Streaming (Odtwórz)
* Na żywo funkcji DVR (Wstrzymaj, wyszukiwania, odtwarzania DVR, Go Live)
* Obsługa wideo koderów-dekoderów - H.264
* Obsługę Audio koderów-dekoderów - AAC
* Wiele wersji językowych audio przełączenie OSMF wbudowanych interfejsów API
* Maksymalna liczba odtwarzania jakości wybór z interfejsami API wbudowanych OSMF
* Podpisy z wtyczki podpisy OSMF kodowane boczną
* Adobe&reg; Flash&reg; Player 11.4 lub nowszej.
* Ta wersja obsługuje tylko OSMF 2.0.

## <a name="supported-features-and-known-issues"></a>Obsługiwane funkcje i znane problemy
Aby uzyskać pełną listę obsługiwanych funkcji, nieobsługiwanych funkcji oraz znanych problemów, zapoznaj się [tego dokumentu](http://download.microsoft.com/download/3/1/B/31B63D97-574E-4A8D-BF8D-170744181724/Smooth_Streaming_Plugin_for_OSMF.pdf).

## <a name="loading-the-plugin"></a>Podczas ładowania dodatku plug-in
Można załadować wtyczki OSMF statycznie (w czasie kompilacji) lub dynamicznie (w czasie wykonywania). Dodatek Smooth Streaming do pobrania OSMF obejmuje zarówno dynamicznej i statycznej wersji.

* Ładowanie statycznych: Aby załadować statycznie, jest wymagany plik biblioteki statycznej (SWC). Statyczne wtyczek są dodawane jako odwołania do projektów i dokonać scalania w pliku wyjściowego w czasie kompilacji.
* Dynamiczne ładowanie: Aby załadować dynamicznie, wymagany jest prekompilowany plik (SWF). Dynamiczne dodatków plug-in są załadowane w czasie wykonywania i nie są uwzględnione w danych wyjściowych projektu. (Skompilowanych danych wyjściowych) Wtyczki dynamicznej mogą być ładowane przy użyciu protokołów HTTP i plików.

Aby uzyskać więcej informacji na ładowanie statycznych i dynamicznych, można znaleźć w oficjalnej [strona wtyczek OSMF](http://osmf.org/dev/osmf/OtherPDFs/osmf_plugin_dev_guide.pdf).

### <a name="ss-for-osmf-static-loading"></a>SS do ładowania statycznych OSMF
Poniższy fragment kodu przedstawia sposób załadować wtyczki SS statycznie dla OSMF i odtwarzanie wideo podstawowe za pomocą klasy OSMF MediaFactory. Przed dołączeniem SS OSMF kodu, upewnij się, że odwołania projektu zawiera wtyczki statyczny "MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swc".

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


### <a name="ss-for-osmf-dynamic-loading"></a>SS dla OSMF dynamiczne ładowanie
Poniższy fragment kodu przedstawia sposób załadować wtyczki SS dla OSMF dynamicznie i odtwarzać podstawowego wideo przy użyciu klasy OSMF MediaFactory. Przed dołączeniem SS OSMF kodu, skopiuj do folderu projektu wtyczki dynamiczne "MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swf", aby załadować przy użyciu protokołu pliku, lub skopiuj na serwerze sieci web, obciążenia HTTP. Nie istnieje potrzeba do dołączenia "MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swc" odwołania do projektu.

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

## <a name="strobe-media--playback-with-the-ss-odmf-dynamic-plugin"></a>Odtwarzanie multimediów światła z wtyczki dynamiczne SS ODMF
Smooth Streaming wtyczki dynamiczne OSMF jest zgodny z [odtwarzania multimediów światła (SMP)](http://osmf.org/strobe_mediaplayback.html). SS wtyczki OSMF służy do dodawania Smooth Streaming odtwarzanie zawartości do punktu SMP. Aby to zrobić, należy skopiować "MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swf" na serwerze sieci web, obciążenia HTTP, wykonując następujące czynności:

1. Przeglądaj [strony Instalatora odtwarzanie multimediów światła](http://osmf.org/dev/2.0gm/setup.html). 
2. Ustaw src ze źródłem Smooth Streaming (np. http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest) 
3. Wprowadź żądane zmiany w konfiguracji, a następnie kliknij polecenie Podgląd i aktualizacji.
   
   **Uwaga** prawidłowy crossdomain.xml wymaga serwera zawartości sieci web. 
4. Skopiuj i Wklej kod, aby to prosta strona HTML za pomocą edytora tekstu, takich jak w poniższym przykładzie:

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



1. Dodaj wtyczkę Smooth Streaming OSMF kod osadzania i Zapisz.
   
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
2. Zapisz strony HTML i opublikować na serwerze sieci web. Przejdź do strony sieci web opublikowanych przy użyciu Twoje ulubione Flash&reg; Player włączone przeglądarki internetowej (program Internet Explorer, Chrome, Firefox, itp).
3. Korzystanie z funkcji Smooth Streaming zawartości wewnątrz Adobe&reg; Flash&reg; Player.

Aby uzyskać więcej informacji na ogólne programowanie OSMF, zobacz urzędnika [OSMF programowanie strony](http://osmf.org/resources.html).

## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Zobacz też
[Adaptacyjne przesyłanie strumieniowe wtyczki dla aktualizacji OSMF firmy Microsoft](https://azure.microsoft.com/blog/2014/10/27/microsoft-adaptive-streaming-plugin-for-osmf-update/) 

