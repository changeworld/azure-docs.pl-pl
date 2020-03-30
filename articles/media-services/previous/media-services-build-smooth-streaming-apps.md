---
title: Płynne przesyłanie strumieniowe aplikacji ze Sklepu Windows – poradnik | Dokumenty firmy Microsoft
description: Dowiedz się, jak używać usługi Azure Media Services do tworzenia aplikacji ze Sklepu Windows w języku C# za pomocą formantu XML MediaElement do odtwarzania zawartości płynnego strumienia.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: 0fa5d8c5-3d5f-4886-ae55-fb6de4f5256d
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2019
ms.author: juliako
ms.openlocfilehash: 9ff961638aa170948d51793a21e86d18dd7e1d80
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "69016788"
---
# <a name="how-to-build-a-smooth-streaming-windows-store-application"></a>Jak utworzyć płynną aplikację ze Sklepu Windows  

Pakiet SDK klienta płynnego przesyłania strumieniowego dla systemu Windows 8 umożliwia deweloperom tworzenie aplikacji ze Sklepu Windows, które mogą odtwarzać zawartość płynną transmisję strumieniową na żądanie i na żywo. Oprócz podstawowego odtwarzania zawartości Smooth Streaming, zestaw SDK oferuje również zaawansowane funkcje, takie jak ochrona Microsoft PlayReady, ograniczenie poziomu jakości, nagrywanie strumieniowe na żywo, przełączanie strumienia audio, nasłuchiwanie aktualizacji stanu (takie jak zmiany poziomu jakości) i zdarzeń błędów itd. Aby uzyskać więcej informacji na temat obsługiwanych funkcji, zobacz [informacje o wersji](https://www.iis.net/learn/media/smooth-streaming/smooth-streaming-client-sdk-for-windows-8-release-notes). Aby uzyskać więcej informacji, zobacz [Struktura programu Player dla systemu Windows 8](https://playerframework.codeplex.com/). 

Ten samouczek zawiera cztery lekcje:

1. Tworzenie podstawowej aplikacji magazynu smooth streaming
2. Dodawanie paska suwaka w celu sterowania postępem nośnika
3. Wybierz opcję Płynne strumieniowanie strumieniowe
4. Wybierz płynne strumieniowanie ścieżek

## <a name="prerequisites"></a>Wymagania wstępne
> [!NOTE]
> Projekty ze Sklepu Windows w wersji 8.1 lub wcześniejszych nie są obsługiwane w programie Visual Studio 2017.  Aby uzyskać więcej informacji, zobacz [Obsługiwane platformy i zgodność programu Visual Studio 2017](https://www.visualstudio.com/en-us/productinfo/vs2017-compatibility-vs).

* Windows 8 32-bitowy lub 64-bitowy.
* Visual Studio w wersjach od 2012 do 2015.
* [Microsoft Smooth Streaming Client SDK dla Systemu Windows 8](https://visualstudiogallery.msdn.microsoft.com/04423d13-3b3e-4741-a01c-1ae29e84fea6?SRC=Home).

Ukończone rozwiązanie dla każdej lekcji można pobrać z przykładów kodu dewelopera MSDN (Galeria kodu): 

* [Lekcja 1](https://code.msdn.microsoft.com/Smooth-Streaming-Client-0bb1471f) - Prosty Windows 8 Smooth Streaming Media Player, 
* [Lekcja 2](https://code.msdn.microsoft.com/A-simple-Windows-8-Smooth-ee98f63a) - Prosty Windows 8 Smooth Streaming Media Player z suwakiem Bar Control, 
* [Lekcja 3](https://code.msdn.microsoft.com/A-Windows-8-Smooth-883c3b44) - Windows 8 Smooth Streaming Media Player z wybór strumienia,  
* [Lekcja 4](https://code.msdn.microsoft.com/A-Windows-8-Smooth-aa9e4907) — odtwarzacz multimediów z systemem Windows 8 Smooth Streaming Media Player z wyborem ścieżki.

## <a name="lesson-1-create-a-basic-smooth-streaming-store-application"></a>Lekcja 1: Tworzenie podstawowej aplikacji smooth streaming Store

W tej lekcji utworzysz aplikację ze Sklepu Windows z formantem MediaElement, aby odtworzyć zawartość płynnego strumienia.  Uruchomiona aplikacja wygląda następująco:

![Przykład aplikacji Smooth Streaming ze Sklepu Windows][PlayerApplication]

Aby uzyskać więcej informacji na temat tworzenia aplikacji ze Sklepu Windows, zobacz [Tworzenie wspaniałych aplikacji dla systemu Windows 8](https://msdn.microsoft.com/windows/apps/br229512.aspx). Ta lekcja zawiera następujące procedury:

1. Tworzenie projektu ze Sklepu Windows
2. Projektowanie interfejsu użytkownika (XAML)
3. Modyfikowanie kodu znajdującego się za plikiem
4. Kompilowanie i testowanie aplikacji

### <a name="to-create-a-windows-store-project"></a>Aby utworzyć projekt ze Sklepu Windows

1. Uruchom program Visual Studio; w latach 2012–2015.
1. W menu **PLIK** kliknij pozycję **Nowy**, a następnie kliknij pozycję **Projekt**.
1. W oknie dialogowym Nowy projekt wpisz lub wybierz następujące wartości:

    | Nazwa | Wartość |
    | --- | --- |
    | Grupa szablonów |Zainstalowane/Szablony/Visual C#/Windows Store |
    | Szablon |Pusta aplikacja (XAML) |
    | Nazwa |Odtwarzacz SSPlayer |
    | Lokalizacja |C:\SSTutorials |
    | Nazwa rozwiązania |Odtwarzacz SSPlayer |
    | Utwórz katalog dla rozwiązania
 |(wybrano) |

1. Kliknij przycisk **OK**.

### <a name="to-add-a-reference-to-the-smooth-streaming-client-sdk"></a>Aby dodać odwołanie do sdk klienta płynnego przesyłania strumieniowego

1. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy pozycję **SSPlayer**, a następnie kliknij polecenie **Dodaj odwołanie**.
1. Wpisz lub wybierz poniższe wartości:

    | Nazwa | Wartość |
    | --- | --- |
    | Grupa odwołań |Windows/rozszerzenia |
    | Tematy pomocy |Wybierz pakiet Microsoft Smooth Streaming Client SDK dla systemu Windows 8 i pakietu środowiska wykonawczego Microsoft Visual C++ |

1. Kliknij przycisk **OK**. 

Po dodaniu odwołań należy wybrać platformę docelową (x64 lub x86), dodanie odwołań nie będzie działać dla dowolnej konfiguracji platformy procesora CPU.  W Eksploratorze rozwiązań zostanie wyświetlony żółty znak ostrzegawczy dla tych dodanych odwołań.

### <a name="to-design-the-player-user-interface"></a>Aby zaprojektować interfejs użytkownika odtwarzacza

1. W Eksploratorze rozwiązań kliknij dwukrotnie pozycję **MainPage.xaml,** aby otworzyć go w widoku projektu.
2. Znajdź ** &lt;siatkę&gt; ** i ** &lt;/Grid&gt; ** tagi pliku XAML i wklej następujący kod między dwoma tagami:

   ```xml
         <Grid.RowDefinitions>

            <RowDefinition Height="20"/>    <!-- spacer -->
            <RowDefinition Height="50"/>    <!-- media controls -->
            <RowDefinition Height="100*"/>  <!-- media element -->
            <RowDefinition Height="80*"/>   <!-- media stream and track selection -->
            <RowDefinition Height="50"/>    <!-- status bar -->
         </Grid.RowDefinitions>

         <StackPanel Name="spMediaControl" Grid.Row="1" Orientation="Horizontal">
            <TextBlock x:Name="tbSource" Text="Source :  " FontSize="16" FontWeight="Bold" VerticalAlignment="Center" />
            <TextBox x:Name="txtMediaSource" Text="https://ecn.channel9.msdn.com/o9/content/smf/smoothcontent/elephantsdream/Elephants_Dream_1024-h264-st-aac.ism/manifest" FontSize="10" Width="700" Margin="0,4,0,10" />
            <Button x:Name="btnSetSource" Content="Set Source" Width="111" Height="43" Click="btnSetSource_Click"/>
            <Button x:Name="btnPlay" Content="Play" Width="111" Height="43" Click="btnPlay_Click"/>
            <Button x:Name="btnPause" Content="Pause"  Width="111" Height="43" Click="btnPause_Click"/>
            <Button x:Name="btnStop" Content="Stop"  Width="111" Height="43" Click="btnStop_Click"/>
            <CheckBox x:Name="chkAutoPlay" Content="Auto Play" Height="55" Width="Auto" IsChecked="{Binding AutoPlay, ElementName=mediaElement, Mode=TwoWay}"/>
            <CheckBox x:Name="chkMute" Content="Mute" Height="55" Width="67" IsChecked="{Binding IsMuted, ElementName=mediaElement, Mode=TwoWay}"/>
         </StackPanel>

         <StackPanel Name="spMediaElement" Grid.Row="2" Height="435" Width="1072"
                    HorizontalAlignment="Center" VerticalAlignment="Center">
            <MediaElement x:Name="mediaElement" Height="356" Width="924" MinHeight="225"
                          HorizontalAlignment="Center" VerticalAlignment="Center" 
                          AudioCategory="BackgroundCapableMedia" />
            <StackPanel Orientation="Horizontal">
                <Slider x:Name="sliderProgress" Width="924" Height="44"
                        HorizontalAlignment="Center" VerticalAlignment="Center"
                        PointerPressed="sliderProgress_PointerPressed"/>
                <Slider x:Name="sliderVolume" 
                        HorizontalAlignment="Right" VerticalAlignment="Center" Orientation="Vertical" 
                        Height="79" Width="148" Minimum="0" Maximum="1" StepFrequency="0.1" 
                        Value="{Binding Volume, ElementName=mediaElement, Mode=TwoWay}" 
                        ToolTipService.ToolTip="{Binding Value, RelativeSource={RelativeSource Mode=Self}}"/>
            </StackPanel>
         </StackPanel>

         <StackPanel Name="spStatus" Grid.Row="4" Orientation="Horizontal">
            <TextBlock x:Name="tbStatus" Text="Status :  " 
               FontSize="16" FontWeight="Bold" VerticalAlignment="Center" HorizontalAlignment="Center" />
            <TextBox x:Name="txtStatus" FontSize="10" Width="700" VerticalAlignment="Center"/>
         </StackPanel>
   ```
   Formant MediaElement służy do odtwarzania multimediów. Formant suwaka o nazwie sliderProgress będzie używany w następnej lekcji do kontrolowania postępu nośnika.
3. Naciśnij **klawisze CTRL+S,** aby zapisać plik.

Formant MediaElement nie obsługuje zawartości płynne przesyłanie strumieniowe po wyjęciu z pudełka. Aby włączyć obsługę płynnego przesyłania strumieniowego, należy zarejestrować program obsługi strumienia bajtów płynne przez rozszerzenie nazwy pliku i typ MIME.  Aby się zarejestrować, należy użyć metody MediaExtensionManager.RegisterByteStreamHandler obszaru nazw windows.media.

W tym pliku XAML niektóre programy obsługi zdarzeń są skojarzone z formantami.  Należy zdefiniować te programy obsługi zdarzeń.

### <a name="to-modify-the-code-behind-file"></a>Aby zmodyfikować kod za plikiem

1. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy pozycję **MainPage.xaml**, a następnie kliknij polecenie **Wyświetl kod**.
2. W górnej części pliku dodaj następującą instrukcję:
   
        using Windows.Media;
3. Na początku klasy **MainPage** dodaj następujący element członkowski danych:
   
         private MediaExtensionManager extensions = new MediaExtensionManager();
4. Na końcu konstruktora **MainPage** dodaj następujące dwa wiersze:
   
        extensions.RegisterByteStreamHandler("Microsoft.Media.AdaptiveStreaming.SmoothByteStreamHandler", ".ism", "text/xml");
        extensions.RegisterByteStreamHandler("Microsoft.Media.AdaptiveStreaming.SmoothByteStreamHandler", ".ism", "application/vnd.ms-sstr+xml");
5. Na końcu **mainpage** klasy wklej następujący kod:
   ```csharp
         # region UI Button Click Events
         private void btnPlay_Click(object sender, RoutedEventArgs e)
         {

         mediaElement.Play();
         txtStatus.Text = "MediaElement is playing ...";
         }
         private void btnPause_Click(object sender, RoutedEventArgs e)
         {

         mediaElement.Pause();
         txtStatus.Text = "MediaElement is paused";
         }
         private void btnSetSource_Click(object sender, RoutedEventArgs e)
         {

         sliderProgress.Value = 0;
         mediaElement.Source = new Uri(txtMediaSource.Text);

         if (chkAutoPlay.IsChecked == true)
         {
             txtStatus.Text = "MediaElement is playing ...";
         }
         else
         {
             txtStatus.Text = "Click the Play button to play the media source.";
         }
         }
         private void btnStop_Click(object sender, RoutedEventArgs e)
         {

         mediaElement.Stop();
         txtStatus.Text = "MediaElement is stopped";
         }
         private void sliderProgress_PointerPressed(object sender, PointerRoutedEventArgs e)
         {

         txtStatus.Text = "Seek to position " + sliderProgress.Value;
         mediaElement.Position = new TimeSpan(0, 0, (int)(sliderProgress.Value));
         }
         # endregion
   ```
   Program obsługi zdarzeń sliderProgress_PointerPressed jest zdefiniowany w tym miejscu.  Istnieje więcej prac do zrobienia, aby to działało, które zostaną omówione w następnej lekcji tego samouczka.
6. Naciśnij **klawisze CTRL+S,** aby zapisać plik.

Gotowy kod za plikiem wygląda następująco:

![Codeview w programie Visual Studio aplikacji Smooth Streaming ze Sklepu Windows][CodeViewPic]

### <a name="to-compile-and-test-the-application"></a>Aby skompilować i przetestować aplikację

1. W menu **KOMPILACJA** kliknij polecenie **Menedżer konfiguracji**.
2. Zmień **platformę rozwiązania Active,** aby dopasować swoją platformę programisty.
3. Naciśnij **klawisz F6,** aby skompilować projekt. 
4. Naciśnij **klawisz F5,** aby uruchomić aplikację.
5. W górnej części aplikacji można użyć domyślnego adresu URL płynnego przesyłania strumieniowego lub wprowadzić inny adres URL. 
6. Kliknij **pozycję Ustaw źródło**. Ponieważ **autoodtłańcają** się domyślnie, nośnik będzie odtwarzany automatycznie.  Multimedia można sterować za pomocą przycisków **Odtwórz,** **Wstrzymaj** i **Zatrzymaj.**  Głośność nośnika można sterować za pomocą suwaka pionowego.  Jednak poziomy suwak do kontrolowania postępu nośnika nie jest jeszcze w pełni zaimplementowany. 

Ukończyłeś lekcję1.  W tej lekcji używasz MediaElement formant do odtwarzania płynnej transmisji strumieniowej zawartości.  W następnej lekcji dodasz suwak, aby kontrolować postęp zawartości Płynne przesyłanie strumieniowe.

## <a name="lesson-2-add-a-slider-bar-to-control-the-media-progress"></a>Lekcja 2: Dodaj pasek suwaka, aby kontrolować postęp multimediów

W lekcji 1 utworzono aplikację Ze Sklepu Windows z formancie MediaElement XAML do odtwarzania płynnej zawartości multimediów.  Chodzi o kilka podstawowych funkcji multimedialnych, takich jak start, stop i pauza.  W tej lekcji dodasz do aplikacji kontrolkę paska suwaka.

W tym samouczku użyjemy czasomierza, aby zaktualizować położenie suwaka na podstawie bieżącej pozycji formantu MediaElement.  Czas rozpoczęcia i zakończenia suwaka również musi zostać zaktualizowany w przypadku zawartości na żywo.  Można to lepiej obsłużyć w przypadku aktualizacji źródła adaptacyjnego.

Źródła multimediów to obiekty, które generują dane multimedialne.  Źródłowy program rozpoznawania nazw przyjmuje adres URL lub strumień bajtów i tworzy odpowiednie źródło nośnika dla tej zawartości.  Program rozpoznawania nazw źródłowych jest standardowym sposobem tworzenia źródeł multimediów przez aplikacje. 

Ta lekcja zawiera następujące procedury:

1. Zarejestruj program obsługi płynnego przesyłania strumieniowego 
2. Dodawanie programów obsługi zdarzeń na poziomie adaptacyjnego menedżera źródeł
3. Dodawanie programów obsługi zdarzeń adaptacyjnego poziomu źródła
4. Dodawanie programów obsługi zdarzeń MediaElement
5. Dodawanie kodu powiązanego z paska suwaka
6. Kompilowanie i testowanie aplikacji

### <a name="to-register-the-smooth-streaming-byte-stream-handler-and-pass-the-propertyset"></a>Aby zarejestrować program obsługi strumienia bajtów płynne przesyłanie strumieniowe i przekazać zestaw właściwości

1. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy pozycję **MainPage.xaml**, a następnie kliknij polecenie **Wyświetl kod**.
2. Na początku pliku dodaj następującą instrukcję using:

   ```csharp
        using Microsoft.Media.AdaptiveStreaming;
   ```
3. Na początku klasy MainPage dodaj następujące elementy członkowskie danych:

   ```csharp
         private Windows.Foundation.Collections.PropertySet propertySet = new Windows.Foundation.Collections.PropertySet();             
         private IAdaptiveSourceManager adaptiveSourceManager;
   ```
4. Wewnątrz **konstruktora MainPage** dodaj następujący kod po **tym. Inicjuj składniki();** wiersz i wiersze kodu rejestracyjnego napisane w poprzedniej lekcji:

   ```csharp
        // Gets the default instance of AdaptiveSourceManager which manages Smooth 
        //Streaming media sources.
        adaptiveSourceManager = AdaptiveSourceManager.GetDefault();
        // Sets property key value to AdaptiveSourceManager default instance.
        // {A5CE1DE8-1D00-427B-ACEF-FB9A3C93DE2D}" must be hardcoded.
        propertySet["{A5CE1DE8-1D00-427B-ACEF-FB9A3C93DE2D}"] = adaptiveSourceManager;
   ```
5. Wewnątrz konstruktora **MainPage** zmodyfikuj dwie metody RegisterByteStreamHandler, aby dodać następujące parametry:

   ```csharp
         // Registers Smooth Streaming byte-stream handler for ".ism" extension and, 
         // "text/xml" and "application/vnd.ms-ss" mime-types and pass the propertyset. 
         // http://*.ism/manifest URI resources will be resolved by Byte-stream handler.
         extensions.RegisterByteStreamHandler(

            "Microsoft.Media.AdaptiveStreaming.SmoothByteStreamHandler", 
            ".ism", 
            "text/xml", 
            propertySet );
         extensions.RegisterByteStreamHandler(

            "Microsoft.Media.AdaptiveStreaming.SmoothByteStreamHandler", 
            ".ism", 
            "application/vnd.ms-sstr+xml", 
         propertySet);
   ```
6. Naciśnij **klawisze CTRL+S,** aby zapisać plik.

### <a name="to-add-the-adaptive-source-manager-level-event-handler"></a>Aby dodać program obsługi zdarzeń na poziomie adaptacyjnego menedżera źródeł

1. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy pozycję **MainPage.xaml**, a następnie kliknij polecenie **Wyświetl kod**.
2. Wewnątrz klasy **MainPage** dodaj następujący element członkowski danych:

   ```csharp
     private AdaptiveSource adaptiveSource = null;
   ```
3. Na końcu **mainpage** klasy, dodaj następujące obsługi zdarzeń:

   ```csharp
         # region Adaptive Source Manager Level Events
         private void mediaElement_AdaptiveSourceOpened(AdaptiveSource sender, AdaptiveSourceOpenedEventArgs args)
         {

            adaptiveSource = args.AdaptiveSource;
         }

         # endregion Adaptive Source Manager Level Events
   ```
4. Na końcu konstruktora **MainPage** dodaj następujący wiersz, aby subskrybować zdarzenie open adaptacyjne źródło:

   ```csharp
         adaptiveSourceManager.AdaptiveSourceOpenedEvent += 
           new AdaptiveSourceOpenedEventHandler(mediaElement_AdaptiveSourceOpened);
   ```
5. Naciśnij **klawisze CTRL+S,** aby zapisać plik.

### <a name="to-add-adaptive-source-level-event-handlers"></a>Aby dodać adaptacyjne programy obsługi zdarzeń na poziomie źródła

1. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy pozycję **MainPage.xaml**, a następnie kliknij polecenie **Wyświetl kod**.
2. Wewnątrz klasy **MainPage** dodaj następujący element członkowski danych:

   ```csharp
     private AdaptiveSourceStatusUpdatedEventArgs adaptiveSourceStatusUpdate; 
     private Manifest manifestObject;
   ```
3. Na końcu **mainpage** klasy, dodaj następujące programy obsługi zdarzeń:

   ```csharp
         # region Adaptive Source Level Events
         private void mediaElement_ManifestReady(AdaptiveSource sender, ManifestReadyEventArgs args)
         {

            adaptiveSource = args.AdaptiveSource;
            manifestObject = args.AdaptiveSource.Manifest;
         }

         private void mediaElement_AdaptiveSourceStatusUpdated(AdaptiveSource sender, AdaptiveSourceStatusUpdatedEventArgs args)
         {

            adaptiveSourceStatusUpdate = args;
         }

         private void mediaElement_AdaptiveSourceFailed(AdaptiveSource sender, AdaptiveSourceFailedEventArgs args)
         {

            txtStatus.Text = "Error: " + args.HttpResponse;
         }

         # endregion Adaptive Source Level Events
   ```
4. Na końcu **mediaElement AdaptiveSourceOpened** metody, dodaj następujący kod, aby subskrybować zdarzenia:

   ```csharp
         adaptiveSource.ManifestReadyEvent +=

                    mediaElement_ManifestReady;
         adaptiveSource.AdaptiveSourceStatusUpdatedEvent += 

            mediaElement_AdaptiveSourceStatusUpdated;
         adaptiveSource.AdaptiveSourceFailedEvent += 

            mediaElement_AdaptiveSourceFailed;
   ```
5. Naciśnij **klawisze CTRL+S,** aby zapisać plik.

Te same zdarzenia są dostępne na poziomie adaptacyjnego źródła żłobka, jak również, który może służyć do obsługi funkcji wspólnych dla wszystkich elementów multimedialnych w aplikacji. Każdy AdaptiveSource zawiera własne zdarzenia i wszystkie zdarzenia AdaptiveSource będą kaskadowo w obszarze AdaptiveSourceManager.

### <a name="to-add-media-element-event-handlers"></a>Aby dodać programy obsługi zdarzeń elementu multimedialnego

1. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy pozycję **MainPage.xaml**, a następnie kliknij polecenie **Wyświetl kod**.
2. Na końcu **mainpage** klasy, dodaj następujące programy obsługi zdarzeń:

   ```csharp
         # region Media Element Event Handlers
         private void MediaOpened(object sender, RoutedEventArgs e)
         {

            txtStatus.Text = "MediaElement opened";
         }

         private void MediaFailed(object sender, ExceptionRoutedEventArgs e)
         {

            txtStatus.Text= "MediaElement failed: " + e.ErrorMessage;
         }

         private void MediaEnded(object sender, RoutedEventArgs e)
         {

            txtStatus.Text ="MediaElement ended.";
         }

         # endregion Media Element Event Handlers
   ```
3. Na końcu konstruktora **MainPage** dodaj następujący kod do indeksu dolnego do zdarzeń:

   ```csharp
         mediaElement.MediaOpened += MediaOpened;
         mediaElement.MediaEnded += MediaEnded;
         mediaElement.MediaFailed += MediaFailed;
   ```
4. Naciśnij **klawisze CTRL+S,** aby zapisać plik.

### <a name="to-add-slider-bar-related-code"></a>Aby dodać kod powiązany z pasemka suwaka

1. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy pozycję **MainPage.xaml**, a następnie kliknij polecenie **Wyświetl kod**.
2. Na początku pliku dodaj następującą instrukcję using:

   ```csharp
        using Windows.UI.Core;
   ```
3. Wewnątrz klasy **MainPage** dodaj następujące elementy członkowskie danych:

   ```csharp
         public static CoreDispatcher _dispatcher;
         private DispatcherTimer sliderPositionUpdateDispatcher;
   ```
4. Na końcu konstruktora **MainPage** dodaj następujący kod:

   ```csharp
         _dispatcher = Window.Current.Dispatcher;
         PointerEventHandler pointerpressedhandler = new PointerEventHandler(sliderProgress_PointerPressed);
         sliderProgress.AddHandler(Control.PointerPressedEvent, pointerpressedhandler, true);    
   ```
5. Na końcu klasy **MainPage** dodaj następujący kod:

   ```csharp
         # region sliderMediaPlayer
         private double SliderFrequency(TimeSpan timevalue)
         {

            long absvalue = 0;
            double stepfrequency = -1;

            if (manifestObject != null)
            {
                absvalue = manifestObject.Duration - (long)manifestObject.StartTime;
            }
            else
            {
                absvalue = mediaElement.NaturalDuration.TimeSpan.Ticks;
            }

            TimeSpan totalDVRDuration = new TimeSpan(absvalue);

            if (totalDVRDuration.TotalMinutes >= 10 && totalDVRDuration.TotalMinutes < 30)
            {
               stepfrequency = 10;
            }
            else if (totalDVRDuration.TotalMinutes >= 30 
                     && totalDVRDuration.TotalMinutes < 60)
            {
                stepfrequency = 30;
            }
            else if (totalDVRDuration.TotalHours >= 1)
            {
                stepfrequency = 60;
            }

            return stepfrequency;
         }

         void updateSliderPositionoNTicks(object sender, object e)
         {

            sliderProgress.Value = mediaElement.Position.TotalSeconds;
         }

         public void setupTimer()
         {

            sliderPositionUpdateDispatcher = new DispatcherTimer();
            sliderPositionUpdateDispatcher.Interval = new TimeSpan(0, 0, 0, 0, 300);
            startTimer();
         }

         public void startTimer()
         {

            sliderPositionUpdateDispatcher.Tick += updateSliderPositionoNTicks;
            sliderPositionUpdateDispatcher.Start();
         }

         // Slider start and end time must be updated in case of live content
         public async void setSliderStartTime(long startTime)
         {

            await _dispatcher.RunAsync(CoreDispatcherPriority.Normal, () =>
            {
                TimeSpan timespan = new TimeSpan(adaptiveSourceStatusUpdate.StartTime);
                double absvalue = (int)Math.Round(timespan.TotalSeconds, MidpointRounding.AwayFromZero);
                sliderProgress.Minimum = absvalue;
            });
         }

         // Slider start and end time must be updated in case of live content
         public async void setSliderEndTime(long startTime)
         {

            await _dispatcher.RunAsync(CoreDispatcherPriority.Normal, () =>
            {
                TimeSpan timespan = new TimeSpan(adaptiveSourceStatusUpdate.EndTime);
                double absvalue = (int)Math.Round(timespan.TotalSeconds, MidpointRounding.AwayFromZero);
                sliderProgress.Maximum = absvalue;
            });
         }

         # endregion sliderMediaPlayer
   ```

   > [!NOTE]
   > CoreDispatcher jest używany do wprowadzania zmian w wątku interfejsu użytkownika z wątku interfejsu użytkownika. W przypadku wąskiego gardła w wątku dyspozytora deweloper może użyć dyspozytora dostarczonego przez element interfejsu użytkownika, który zamierza zaktualizować.  Przykład:

   ```csharp
         await sliderProgress.Dispatcher.RunAsync(CoreDispatcherPriority.Normal, () => { TimeSpan 

         timespan = new TimeSpan(adaptiveSourceStatusUpdate.EndTime); 
         double absvalue  = (int)Math.Round(timespan.TotalSeconds, MidpointRounding.AwayFromZero); 

         sliderProgress.Maximum = absvalue; }); 
   ```
6. Na końcu **metody mediaElement_AdaptiveSourceStatusUpdated** dodaj następujący kod:

   ```csharp
         setSliderStartTime(args.StartTime);
         setSliderEndTime(args.EndTime);
   ```
7. Na końcu **MediaOpened** metody, dodać następujący kod:

   ```csharp
         sliderProgress.StepFrequency = SliderFrequency(mediaElement.NaturalDuration.TimeSpan);
         sliderProgress.Width = mediaElement.Width;
         setupTimer();
   ```
8. Naciśnij **klawisze CTRL+S,** aby zapisać plik.

### <a name="to-compile-and-test-the-application"></a>Aby skompilować i przetestować aplikację

1. Naciśnij **klawisz F6,** aby skompilować projekt. 
2. Naciśnij **klawisz F5,** aby uruchomić aplikację.
3. W górnej części aplikacji można użyć domyślnego adresu URL płynnego przesyłania strumieniowego lub wprowadzić inny adres URL. 
4. Kliknij **pozycję Ustaw źródło**. 
5. Przetestuj suwak.

Ukończyłeś lekcję 2.  W tej lekcji dodano suwak do aplikacji. 

## <a name="lesson-3-select-smooth-streaming-streams"></a>Lekcja 3: Wybierz płynne strumienie strumieniowe
Płynne przesyłanie strumieniowe umożliwia strumieniowe przesyłanie zawartości za pomocą wielu ścieżek dźwiękowych w języku, które są wybierane przez widzów.  W tej lekcji umożliwisz widzom wybranie strumieni. Ta lekcja zawiera następujące procedury:

1. Modyfikowanie pliku XAML
2. Modyfikowanie kodu znajdującego się za plikiem
3. Kompilowanie i testowanie aplikacji

### <a name="to-modify-the-xaml-file"></a>Aby zmodyfikować plik XAML

1. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy pozycję **MainPage.xaml**, a następnie kliknij polecenie **Widok Projektanta**.
2. Zlokalizuj &lt;grid.RowDefinitions&gt;i zmodyfikuj zdefiniowanie wierszy, aby wyglądały następująco:

   ```xml
         <Grid.RowDefinitions>            
            <RowDefinition Height="20"/>
            <RowDefinition Height="50"/>
            <RowDefinition Height="100"/>
            <RowDefinition Height="80"/>
            <RowDefinition Height="50"/>
         </Grid.RowDefinitions>
   ```
3. Wewnątrz &lt;tagów Grid&gt;&lt;/Grid&gt; dodaj następujący kod, aby zdefiniować kontrolkę pola listy, aby użytkownicy mogli zobaczyć listę dostępnych strumieni i wybrać strumienie:

   ```xml
         <Grid Name="gridStreamAndBitrateSelection" Grid.Row="3">
            <Grid.RowDefinitions>
                <RowDefinition Height="300"/>
            </Grid.RowDefinitions>
            <Grid.ColumnDefinitions>
                <ColumnDefinition Width="250*"/>
                <ColumnDefinition Width="250*"/>
            </Grid.ColumnDefinitions>
            <StackPanel Name="spStreamSelection" Grid.Row="1" Grid.Column="0">
                <StackPanel Orientation="Horizontal">
                    <TextBlock Name="tbAvailableStreams" Text="Available Streams:" FontSize="16" VerticalAlignment="Center"></TextBlock>
                    <Button Name="btnChangeStreams" Content="Submit" Click="btnChangeStream_Click"/>
                </StackPanel>
                <ListBox x:Name="lbAvailableStreams" Height="200" Width="200" Background="Transparent" HorizontalAlignment="Left" 
                    ScrollViewer.VerticalScrollMode="Enabled" ScrollViewer.VerticalScrollBarVisibility="Visible">
                    <ListBox.ItemTemplate>
                        <DataTemplate>
                            <CheckBox Content="{Binding Name}" IsChecked="{Binding isChecked, Mode=TwoWay}" />
                        </DataTemplate>
                    </ListBox.ItemTemplate>
                </ListBox>
            </StackPanel>
         </Grid>
   ```
4. Naciśnij **klawisze CTRL+S,** aby zapisać zmiany.

### <a name="to-modify-the-code-behind-file"></a>Aby zmodyfikować kod za plikiem

1. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy pozycję **MainPage.xaml**, a następnie kliknij polecenie **Wyświetl kod**.
2. Wewnątrz obszaru nazw SSPlayer dodaj nową klasę:

   ```csharp
        #region class Stream
   
        public class Stream
        {
            private IManifestStream stream;
            public bool isCheckedValue;
            public string name;
   
            public string Name
            {
                get { return name; }
                set { name = value; }
            }
   
            public IManifestStream ManifestStream
            {
                get { return stream; }
                set { stream = value; }
            }
   
            public bool isChecked
            {
                get { return isCheckedValue; }
                set
                {
                    // mMke the video stream always checked.
                    if (stream.Type == MediaStreamType.Video)
                    {
                        isCheckedValue = true;
                    }
                    else
                    {
                        isCheckedValue = value;
                    }
                }
            }
   
            public Stream(IManifestStream streamIn)
            {
                stream = streamIn;
                name = stream.Name;
            }
        }
        #endregion class Stream
   ```
3. Na początku mainpage klasy dodaj następujące definicje zmiennych:

   ```csharp
         private List<Stream> availableStreams;
         private List<Stream> availableAudioStreams;
         private List<Stream> availableTextStreams;
         private List<Stream> availableVideoStreams;
   ```
4. Wewnątrz mainpage klasy, dodaj następujący region:
   ```csharp
        #region stream selection
        ///<summary>
        ///Functionality to select streams from IManifestStream available streams
        /// </summary>
   
        // This function is called from the mediaElement_ManifestReady event handler 
        // to retrieve the streams and populate them to the local data members.
        public void getStreams(Manifest manifestObject)
        {
            availableStreams = new List<Stream>();
            availableVideoStreams = new List<Stream>();
            availableAudioStreams = new List<Stream>();
            availableTextStreams = new List<Stream>();
   
            try
            {
                for (int i = 0; i<manifestObject.AvailableStreams.Count; i++)
                {
                    Stream newStream = new Stream(manifestObject.AvailableStreams[i]);
                    newStream.isChecked = false;
   
                    //populate the stream lists based on the types
                    availableStreams.Add(newStream);
   
                    switch (newStream.ManifestStream.Type)
                    {
                        case MediaStreamType.Video:
                            availableVideoStreams.Add(newStream);
                            break;
                        case MediaStreamType.Audio:
                            availableAudioStreams.Add(newStream);
                            break;
                        case MediaStreamType.Text:
                            availableTextStreams.Add(newStream);
                            break;
                    }
   
                    // Select the default selected streams from the manifest.
                    for (int j = 0; j<manifestObject.SelectedStreams.Count; j++)
                    {
                        string selectedStreamName = manifestObject.SelectedStreams[j].Name;
                        if (selectedStreamName.Equals(newStream.Name))
                        {
                            newStream.isChecked = true;
                            break;
                        }
                    }
                }
            }
            catch (Exception e)
            {
                txtStatus.Text = "Error: " + e.Message;
            }
        }
   
        // This function set the list box ItemSource
        private async void refreshAvailableStreamsListBoxItemSource()
        {
            try
            {
                //update the stream check box list on the UI
                await _dispatcher.RunAsync(CoreDispatcherPriority.Normal, ()
                    => { lbAvailableStreams.ItemsSource = availableStreams; });
            }
            catch (Exception e)
            {
                txtStatus.Text = "Error: " + e.Message;
            }
        }
   
        // This function creates a selected streams list
        private void createSelectedStreamsList(List<IManifestStream> selectedStreams)
        {
            bool isOneVideoSelected = false;
            bool isOneAudioSelected = false;
   
            // Only one video stream can be selected
            for (int j = 0; j<availableVideoStreams.Count; j++)
            {
                if (availableVideoStreams[j].isChecked && (!isOneVideoSelected))
                {
                    selectedStreams.Add(availableVideoStreams[j].ManifestStream);
                    isOneVideoSelected = true;
                }
            }
   
            // Select the first video stream from the list if no video stream is selected
            if (!isOneVideoSelected)
            {
                availableVideoStreams[0].isChecked = true;
                selectedStreams.Add(availableVideoStreams[0].ManifestStream);
            }
   
            // Only one audio stream can be selected
            for (int j = 0; j<availableAudioStreams.Count; j++)
            {
                if (availableAudioStreams[j].isChecked && (!isOneAudioSelected))
                {
                    selectedStreams.Add(availableAudioStreams[j].ManifestStream);
                    isOneAudioSelected = true;
                    txtStatus.Text = "The audio stream is changed to " + availableAudioStreams[j].ManifestStream.Name;
                }
            }
   
            // Select the first audio stream from the list if no audio steam is selected.
            if (!isOneAudioSelected)
            {
                availableAudioStreams[0].isChecked = true;
                selectedStreams.Add(availableAudioStreams[0].ManifestStream);
            }
   
            // Multiple text streams are supported.
            for (int j = 0; j < availableTextStreams.Count; j++)
            {
                if (availableTextStreams[j].isChecked)
                {
                    selectedStreams.Add(availableTextStreams[j].ManifestStream);
                }
            }
        }
   
        // Change streams on a smooth streaming presentation with multiple video streams.
        private async void changeStreams(List<IManifestStream> selectStreams)
        {
            try
            {
                IReadOnlyList<IStreamChangedResult> returnArgs =
                    await manifestObject.SelectStreamsAsync(selectStreams);
            }
            catch (Exception e)
            {
                txtStatus.Text = "Error: " + e.Message;
            }
        }
        #endregion stream selection
   ```
5. Zlokalizuj metodę mediaElement_ManifestReady, dołącz następujący kod na końcu funkcji:
   ```csharp
        getStreams(manifestObject);
        refreshAvailableStreamsListBoxItemSource();
   ```
    Tak więc, gdy manifest MediaElement jest gotowy, kod pobiera listę dostępnych strumieni i wypełnia pole listy interfejsu użytkownika z listą.
6. Wewnątrz mainpage klasy, znajdź przyciski interfejsu użytkownika kliknij region zdarzeń, a następnie dodać następującą definicję funkcji:
   ```csharp
        private void btnChangeStream_Click(object sender, RoutedEventArgs e)
        {
            List<IManifestStream> selectedStreams = new List<IManifestStream>();
   
            // Create a list of the selected streams
            createSelectedStreamsList(selectedStreams);
   
            // Change streams on the presentation
            changeStreams(selectedStreams);
        }
   ```

### <a name="to-compile-and-test-the-application"></a>Aby skompilować i przetestować aplikację

1. Naciśnij **klawisz F6,** aby skompilować projekt. 
2. Naciśnij **klawisz F5,** aby uruchomić aplikację.
3. W górnej części aplikacji można użyć domyślnego adresu URL płynnego przesyłania strumieniowego lub wprowadzić inny adres URL. 
4. Kliknij **pozycję Ustaw źródło**. 
5. Domyślnym językiem jest audio_eng. Spróbuj przełączać się między audio_eng a audio_es. Za każdym razem, gdy wybierasz nowy strumień, musisz kliknąć przycisk Prześlij.

Ukończyłeś lekcję 3.  W tej lekcji należy dodać funkcje, aby wybrać strumienie.

## <a name="lesson-4-select-smooth-streaming-tracks"></a>Lekcja 4: Wybierz płynne strumieniowanie utworów

Prezentacja Smooth Streaming może zawierać wiele plików wideo zakodowanych z różnymi poziomami jakości (szybkości transmisji bitów) i rozdzielczościami. W tej lekcji umożliwisz użytkownikom wybieranie ścieżek. Ta lekcja zawiera następujące procedury:

1. Modyfikowanie pliku XAML
2. Modyfikowanie kodu znajdującego się za plikiem
3. Kompilowanie i testowanie aplikacji

### <a name="to-modify-the-xaml-file"></a>Aby zmodyfikować plik XAML

1. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy pozycję **MainPage.xaml**, a następnie kliknij polecenie **Widok Projektanta**.
2. Znajdź &lt;grid&gt; tag z nazwą **gridStreamAndBitrateSelection**, dołącz następujący kod na końcu tagu:
   ```xml
         <StackPanel Name="spBitRateSelection" Grid.Row="1" Grid.Column="1">
         <StackPanel Orientation="Horizontal">
             <TextBlock Name="tbBitRate" Text="Available Bitrates:" FontSize="16" VerticalAlignment="Center"/>
             <Button Name="btnChangeTracks" Content="Submit" Click="btnChangeTrack_Click" />
         </StackPanel>
         <ListBox x:Name="lbAvailableVideoTracks" Height="200" Width="200" Background="Transparent" HorizontalAlignment="Left" 
                  ScrollViewer.VerticalScrollMode="Enabled" ScrollViewer.VerticalScrollBarVisibility="Visible">
             <ListBox.ItemTemplate>
                 <DataTemplate>
                     <CheckBox Content="{Binding Bitrate}" IsChecked="{Binding isChecked, Mode=TwoWay}"/>
                 </DataTemplate>
             </ListBox.ItemTemplate>
         </ListBox>
         </StackPanel>
   ```
3. Naciśnij **klawisze CTRL+S,** aby zapisać zmiany

### <a name="to-modify-the-code-behind-file"></a>Aby zmodyfikować kod za plikiem

1. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy pozycję **MainPage.xaml**, a następnie kliknij polecenie **Wyświetl kod**.
2. Wewnątrz obszaru nazw SSPlayer dodaj nową klasę:
   ```csharp
        #region class Track
        public class Track
        {
            private IManifestTrack trackInfo;
            public string _bitrate;
            public bool isCheckedValue;
   
            public IManifestTrack TrackInfo
            {
                get { return trackInfo; }
                set { trackInfo = value; }
            }
   
            public string Bitrate
            {
                get { return _bitrate; }
                set { _bitrate = value; }
            }
   
            public bool isChecked
            {
                get { return isCheckedValue; }
                set
                {
                    isCheckedValue = value;
                }
            }
   
            public Track(IManifestTrack trackInfoIn)
            {
                trackInfo = trackInfoIn;
                _bitrate = trackInfoIn.Bitrate.ToString();
            }
            //public Track() { }
        }
        #endregion class Track
   ```
3. Na początku mainpage klasy dodaj następujące definicje zmiennych:
   ```csharp
        private List<Track> availableTracks;
   ```
4. Wewnątrz mainpage klasy, dodaj następujący region:
   ```csharp
        #region track selection
        /// <summary>
        /// Functionality to select video streams
        /// </summary>
   
        /// This Function gets the tracks for the selected video stream
        public void getTracks(Manifest manifestObject)
        {
            availableTracks = new List<Track>();
   
            IManifestStream videoStream = getVideoStream();
            IReadOnlyList<IManifestTrack> availableTracksLocal = videoStream.AvailableTracks;
            IReadOnlyList<IManifestTrack> selectedTracksLocal = videoStream.SelectedTracks;
   
            try
            {
                for (int i = 0; i < availableTracksLocal.Count; i++)
                {
                    Track thisTrack = new Track(availableTracksLocal[i]);
                    thisTrack.isChecked = true;
   
                    for (int j = 0; j < selectedTracksLocal.Count; j++)
                    {
                        string selectedTrackName = selectedTracksLocal[j].Bitrate.ToString();
                        if (selectedTrackName.Equals(thisTrack.Bitrate))
                        {
                            thisTrack.isChecked = true;
                            break;
                        }
                    }
                    availableTracks.Add(thisTrack);
                }
            }
            catch (Exception e)
            {
                txtStatus.Text = e.Message;
            }
        }
   
        // This function gets the video stream that is playing
        private IManifestStream getVideoStream()
        {
            IManifestStream videoStream = null;
            for (int i = 0; i < manifestObject.SelectedStreams.Count; i++)
            {
                if (manifestObject.SelectedStreams[i].Type == MediaStreamType.Video)
                {
                    videoStream = manifestObject.SelectedStreams[i];
                    break;
                }
            }
            return videoStream;
        }
   
        // This function set the UI list box control ItemSource
        private async void refreshAvailableTracksListBoxItemSource()
        {
            try
            {
                // Update the track check box list on the UI 
                await _dispatcher.RunAsync(CoreDispatcherPriority.Normal, ()
                    => { lbAvailableVideoTracks.ItemsSource = availableTracks; });
            }
            catch (Exception e)
            {
                txtStatus.Text = "Error: " + e.Message;
            }        
        }
   
        // This function creates a list of the selected tracks.
        private void createSelectedTracksList(List<IManifestTrack> selectedTracks)
        {
            // Create a list of selected tracks
            for (int j = 0; j < availableTracks.Count; j++)
            {
                if (availableTracks[j].isCheckedValue == true)
                {
                    selectedTracks.Add(availableTracks[j].TrackInfo);
                }
            }
        }
   
        // This function selects the tracks based on user selection 
        private void changeTracks(List<IManifestTrack> selectedTracks)
        {
            IManifestStream videoStream = getVideoStream();
            try
            {
                videoStream.SelectTracks(selectedTracks);
            }
            catch (Exception ex)
            {
                txtStatus.Text = ex.Message;
            }
        }
        #endregion track selection
   ```
5. Zlokalizuj metodę mediaElement_ManifestReady, dołącz następujący kod na końcu funkcji:
   ```csharp
         getTracks(manifestObject);
         refreshAvailableTracksListBoxItemSource();
   ```
6. Wewnątrz mainpage klasy, znajdź przyciski interfejsu użytkownika kliknij region zdarzeń, a następnie dodać następującą definicję funkcji:
   ```csharp
         private void btnChangeStream_Click(object sender, RoutedEventArgs e)
         {
            List<IManifestStream> selectedStreams = new List<IManifestStream>();

            // Create a list of the selected streams
            createSelectedStreamsList(selectedStreams);

            // Change streams on the presentation
            changeStreams(selectedStreams);
         }
   ```
   
### <a name="to-compile-and-test-the-application"></a>Aby skompilować i przetestować aplikację

1. Naciśnij **klawisz F6,** aby skompilować projekt. 
2. Naciśnij **klawisz F5,** aby uruchomić aplikację.
3. W górnej części aplikacji można użyć domyślnego adresu URL płynnego przesyłania strumieniowego lub wprowadzić inny adres URL. 
4. Kliknij **pozycję Ustaw źródło**. 
5. Domyślnie wybrane są wszystkie ścieżki strumienia wideo. Aby wyeksperować zmiany szybkości transmisji bitów, można wybrać najniższą dostępną szybkość transmisji bitów, a następnie wybrać najwyższą dostępną szybkość transmisji bitów. Po każdej zmianie należy kliknąć przycisk Prześlij.  Możesz zobaczyć zmiany jakości wideo.

Ukończyłeś lekcję 4.  W tej lekcji należy dodać funkcje, aby wybrać utwory.

## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="other-resources"></a>Inne zasoby:
* [Jak zbudować aplikację Smooth Streaming Windows 8 JavaScript z zaawansowanymi funkcjami](https://blogs.iis.net/cenkd/archive/2012/08/10/how-to-build-a-smooth-streaming-windows-8-javascript-application-with-advanced-features.aspx)
* [Przegląd techniczny płynnego przesyłania strumieniowego](https://www.iis.net/learn/media/on-demand-smooth-streaming/smooth-streaming-technical-overview)

[PlayerApplication]: ./media/media-services-build-smooth-streaming-apps/SSClientWin8-1.png
[CodeViewPic]: ./media/media-services-build-smooth-streaming-apps/SSClientWin8-2.png

