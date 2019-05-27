---
title: Zestaw Smooth Streaming samouczek dotyczący aplikacji Windows Store | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać usługi Azure Media Services do tworzenia C# aplikacji Windows Store przy użyciu kontrolki XML MediaElement do odtwarzania zawartości Smooth Stream.
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
ms.openlocfilehash: b8c1513838fb848388946e18698a0410aa7a0332
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2019
ms.locfileid: "65949630"
---
# <a name="how-to-build-a-smooth-streaming-windows-store-application"></a>Jak tworzyć umożliwiającej płynne przesyłanie strumieniowe aplikacji Windows Store  

Smooth Streaming klienta zestawu SDK dla systemu Windows 8 umożliwia deweloperom tworzenie aplikacji Windows Store, które można odtwarzać na żądanie i na żywo zawartości Smooth Streaming. Oprócz podstawowych odtwarzanie zawartości Smooth Streaming, zestaw SDK udostępnia również podpowiedzi funkcji zaawansowanych, takich jak Microsoft objętych ochroną PlayReady, ograniczenie poziomu jakości, na żywo DVR, strumień audio przełączania, nasłuchuje aktualizacji stanu (np. zmiany poziomu jakości) i zdarzenia błędu i tak dalej. Aby uzyskać więcej informacji o obsługiwanych funkcji, zobacz [informacje o wersji](https://www.iis.net/learn/media/smooth-streaming/smooth-streaming-client-sdk-for-windows-8-release-notes). Aby uzyskać więcej informacji, zobacz [Player Framework dla systemu Windows 8](https://playerframework.codeplex.com/). 

Ten samouczek zawiera cztery lekcje:

1. Tworzenie podstawowego Smooth przesyłania strumieniowego aplikacji Store
2. Dodawanie paska suwaka do kontrolowania postępu multimediów
3. Wybierz strumieni Smooth Streaming
4. Wybierz ścieżki Smooth Streaming

## <a name="prerequisites"></a>Wymagania wstępne
> [!NOTE]
> Projekty Windows Store w wersji 8.1 i starsze nie są obsługiwane w programie Visual Studio 2017.  Aby uzyskać więcej informacji, zobacz [Obsługiwane platformy i zgodność programu Visual Studio 2017](https://www.visualstudio.com/en-us/productinfo/vs2017-compatibility-vs).

* Windows 8, 32-bitową lub 64-bitowych.
* Visual Studio wersji 2012 2015.
* [Microsoft Smooth Streaming Client SDK dla systemu Windows 8](https://visualstudiogallery.msdn.microsoft.com/04423d13-3b3e-4741-a01c-1ae29e84fea6?SRC=Home https://visualstudiogallery.msdn.microsoft.com/04423d13-3b3e-4741-a01c-1ae29e84fea6?SRC=Home).

Ukończone rozwiązanie dla każdej lekcji można pobrać z subskrypcją MSDN Developer Code Samples (Galeria kodu): 

* [Lekcja 1](https://code.msdn.microsoft.com/Smooth-Streaming-Client-0bb1471f) — proste systemu Windows 8 umożliwiającej płynne przesyłanie strumieniowe Media Player 
* [Lekcja 2](https://code.msdn.microsoft.com/A-simple-Windows-8-Smooth-ee98f63a) — prostego systemu Windows 8 umożliwiającej płynne przesyłanie strumieniowe Media Player do paska suwaka kontroli 
* [Lekcja 3](https://code.msdn.microsoft.com/A-Windows-8-Smooth-883c3b44) — systemu Windows 8 umożliwiającej płynne przesyłanie strumieniowe Media Player do wyboru Stream  
* [Lekcja 4](https://code.msdn.microsoft.com/A-Windows-8-Smooth-aa9e4907) — systemu Windows 8 umożliwiającej płynne przesyłanie strumieniowe Media Player do śledzenie zaznaczenia.

## <a name="lesson-1-create-a-basic-smooth-streaming-store-application"></a>Lekcja 1: Tworzenie podstawowego Smooth przesyłania strumieniowego aplikacji Store

W tej lekcji utworzysz aplikację Windows Store przy użyciu kontrolki MediaElement grać Smooth Stream zawartości.  Uruchamianie aplikacji wygląda następująco:

![Przykładowa aplikacja Smooth Streaming Windows Store][PlayerApplication]

Aby uzyskać więcej informacji na temat tworzenia aplikacji Windows Store, zobacz [Twórz wspaniałe aplikacje dla systemu Windows 8](https://msdn.microsoft.com/windows/apps/br229512.aspx). W tej lekcji obejmuje następujące procedury:

1. Utwórz projekt Windows Store
2. Projektowanie interfejsu użytkownika (XAML)
3. Modyfikowanie pliku CodeBehind
4. Kompilowanie i testowanie aplikacji

### <a name="to-create-a-windows-store-project"></a>Aby utworzyć projekt Windows Store

1. Uruchom program Visual Studio; obsługiwane są wersje 2012 2015.
1. W menu **PLIK** kliknij pozycję **Nowy**, a następnie kliknij pozycję **Projekt**.
1. W oknie dialogowym Nowy projekt wpisz lub wybierz następujące wartości:

    | Name (Nazwa) | Wartość |
    | --- | --- |
    | Grupa szablonów |Zainstalowana/szablony/Visual C#Windows Store |
    | Szablon |Pusta aplikacja (XAML) |
    | Name (Nazwa) |SSPlayer |
    | Lokalizacja |C:\SSTutorials |
    | Nazwa rozwiązania |SSPlayer |
    | Utwórz katalog dla rozwiązania |(wybrane) |

1. Kliknij przycisk **OK**.

### <a name="to-add-a-reference-to-the-smooth-streaming-client-sdk"></a>Aby dodać odwołanie do Smooth Streaming Client SDK

1. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy **SSPlayer**, a następnie kliknij przycisk **Dodaj odwołanie**.
1. Wpisz lub wybierz poniższe wartości:

    | Name (Nazwa) | Wartość |
    | --- | --- |
    | Grupa odwołań |Windows/Extensions |
    | Odwołanie |Wybierz pozycję Microsoft Smooth Streaming Client SDK dla systemu Windows 8 i pakiet programu Microsoft Visual C++ Runtime |

1. Kliknij przycisk **OK**. 

Po dodaniu odwołania, należy wybrać platforma docelowa (x64 lub x86), dodawanie odwołań nie będzie działać dla konfiguracji platformy dowolny Procesor.  W Eksploratorze rozwiązań zobaczysz, żółtego znaku ostrzeżenia dla tych dodać odwołania.

### <a name="to-design-the-player-user-interface"></a>Projektować interfejs użytkownika odtwarzacza

1. W Eksploratorze rozwiązań kliknij dwukrotnie **MainPage.xaml** aby otworzyć go w widoku Projekt.
2. Znajdź **&lt;siatki&gt;** i **&lt;/Grid&gt;** tagi plików XAML i wklej następujący kod między dwoma tagami:

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
   Formant MediaElement umożliwia odtwarzanie multimediów. Kontrolka suwaka, o nazwie sliderProgress będzie służyć w następnej lekcji do kontrolowania postępu nośnika.
3. Naciśnij klawisz **CTRL + S** można zapisać pliku.

Formant MediaElement nie obsługuje funkcji Smooth Streaming zawartości out-of-box. Aby włączyć obsługę funkcji Smooth Streaming, należy zarejestrować program obsługi strumienia bajtów Smooth Streaming przez rozszerzenie nazwy pliku i typu MIME.  Aby się zarejestrować, używana jest metoda MediaExtensionManager.RegisterByteStreamHandler Windows.Media przestrzeni nazw.

W tym pliku XAML niektóre procedury obsługi zdarzeń są skojarzone z kontrolki.  Należy zdefiniować te programy obsługi zdarzeń.

### <a name="to-modify-the-code-behind-file"></a>Aby zmodyfikować pliku CodeBehind

1. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy **MainPage.xaml**, a następnie kliknij przycisk **Wyświetl kod**.
2. W górnej części pliku Dodaj następującą instrukcję using:
   
        using Windows.Media;
3. Na początku **MainPage** klasy, Dodaj następujący element członkowski danych:
   
         private MediaExtensionManager extensions = new MediaExtensionManager();
4. Na koniec **MainPage** konstruktora, Dodaj następujące dwa wiersze:
   
        extensions.RegisterByteStreamHandler("Microsoft.Media.AdaptiveStreaming.SmoothByteStreamHandler", ".ism", "text/xml");
        extensions.RegisterByteStreamHandler("Microsoft.Media.AdaptiveStreaming.SmoothByteStreamHandler", ".ism", "application/vnd.ms-sstr+xml");
5. Na koniec **MainPage** klasy, wklej następujący kod:
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
   Program obsługi zdarzeń sliderProgress_PointerPressed jest zdefiniowane w tym miejscu.  Ma więcej działa zrobić, aby przygotować go do pracy, omówiona w następnej lekcji w tym samouczku.
6. Naciśnij klawisz **CTRL + S** można zapisać pliku.

Zakończono pliku CodeBehind powinna wyglądać następująco:

![Codeview w aplikacji programu Visual Studio programu Smooth Streaming Windows Store][CodeViewPic]

### <a name="to-compile-and-test-the-application"></a>Aby skompilować i przetestować aplikację

1. Z **kompilacji** menu, kliknij przycisk **programu Configuration Manager**.
2. Zmiana **aktywną platformą rozwiązania** odpowiadający danej platformy programowania.
3. Naciśnij klawisz **F6** do skompilowania projektu. 
4. Naciśnij klawisz **F5**, aby uruchomić aplikację.
5. W górnej części aplikacji możesz użyć domyślnej Smooth Streaming URL lub wprowadź inną nazwę. 
6. Kliknij przycisk **Ustaw źródło**. Ponieważ **autoodtwarzania** jest włączona domyślnie nośnika jest automatycznie odtworzyć.  Można kontrolować przy użyciu nośnika **Odtwórz**, **Wstrzymaj** i **zatrzymać** przycisków.  Można kontrolować woluminu nośnika przy użyciu pionowej kontrolce slider.  Jednak suwak poziomy kontrolowania postępu nośnika nie jest w pełni jeszcze zaimplementowana. 

Lesson1 została ukończona.  W tej lekcji użyjesz Formant MediaElement do odtwarzania zawartości Smooth Streaming.  W następnej lekcji zostaną dodane suwak, aby określić postęp zawartości Smooth Streaming.

## <a name="lesson-2-add-a-slider-bar-to-control-the-media-progress"></a>Lekcja 2: Dodawanie paska suwaka do kontrolowania postępu multimediów

W lekcji 1 utworzono aplikację Windows Store przy użyciu kontrolki XAML MediaElement do odtwarzania zawartości multimedialnej Smooth Streaming.  Chodzi niektóre funkcje podstawowe media, takie jak uruchamianie, zatrzymywanie i wstrzymania.  W tej lekcji dodasz formant paska suwaka do aplikacji.

W tym samouczku użyjemy czasomierz można zaktualizować położenie suwaka, w oparciu o bieżące położenie kontrolki MediaElement.  Suwak początek i koniec czasu również muszą zostać zaktualizowane w przypadku zawartości na żywo.  Mogą być lepiej obsługiwane w przypadku aktualizacji źródła adaptacyjne.

Źródeł multimediów są obiekty, które generują dane multimedialne.  Źródło programu rozpoznawania nazw przyjmuje adres URL lub bajt strumienia i tworzy źródło nośnika odpowiednie dla tej zawartości.  Mechanizm rozpoznawania źródła jest standardowym sposobem dla aplikacji, aby utworzyć źródeł multimediów. 

W tej lekcji obejmuje następujące procedury:

1. Zarejestruj procedury obsługi Smooth Streaming 
2. Dodawanie obsługi zdarzeń na poziomie menedżera adaptacyjne źródła
3. Dodawanie obsługi zdarzeń na poziomie źródła adaptacyjne
4. Dodawanie obsługi zdarzeń MediaElement
5. Dodaj suwak powiązane kodu kreskowego
6. Kompilowanie i testowanie aplikacji

### <a name="to-register-the-smooth-streaming-byte-stream-handler-and-pass-the-propertyset"></a>Aby zarejestrować obsługi strumienia bajtów Smooth Streaming i przekazać propertyset

1. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy **MainPage.xaml**, a następnie kliknij przycisk **Wyświetl kod**.
2. Na początku pliku Dodaj następującą instrukcję using:

   ```csharp
        using Microsoft.Media.AdaptiveStreaming;
   ```
3. Na początku klasy MainPage, Dodaj następujące elementy członkowskie danych:

   ```csharp
         private Windows.Foundation.Collections.PropertySet propertySet = new Windows.Foundation.Collections.PropertySet();             
         private IAdaptiveSourceManager adaptiveSourceManager;
   ```
4. Wewnątrz **MainPage** konstruktora, Dodaj następujący kod po **to. Inicjowanie Components();**  wiersza i rejestracji kodu wierszach w poprzedniej lekcji:

   ```csharp
        // Gets the default instance of AdaptiveSourceManager which manages Smooth 
        //Streaming media sources.
        adaptiveSourceManager = AdaptiveSourceManager.GetDefault();
        // Sets property key value to AdaptiveSourceManager default instance.
        // {A5CE1DE8-1D00-427B-ACEF-FB9A3C93DE2D}" must be hardcoded.
        propertySet["{A5CE1DE8-1D00-427B-ACEF-FB9A3C93DE2D}"] = adaptiveSourceManager;
   ```
5. Wewnątrz **MainPage** konstruktora, zmodyfikować te dwie metody RegisterByteStreamHandler, aby dodać określone parametry:

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
6. Naciśnij klawisz **CTRL + S** można zapisać pliku.

### <a name="to-add-the-adaptive-source-manager-level-event-handler"></a>Aby dodać obsługi zdarzeń na poziomie menedżera adaptacyjne źródła

1. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy **MainPage.xaml**, a następnie kliknij przycisk **Wyświetl kod**.
2. Wewnątrz **MainPage** klasy, Dodaj następujący element członkowski danych:

   ```csharp
     private AdaptiveSource adaptiveSource = null;
   ```
3. Na koniec **MainPage** klasy, dodaj następującą obsługę zdarzeń:

   ```csharp
         # region Adaptive Source Manager Level Events
         private void mediaElement_AdaptiveSourceOpened(AdaptiveSource sender, AdaptiveSourceOpenedEventArgs args)
         {

            adaptiveSource = args.AdaptiveSource;
         }

         # endregion Adaptive Source Manager Level Events
   ```
4. Na koniec **MainPage** konstruktora, Dodaj następujący wiersz do subskrybowania zdarzenia otwarte adaptacyjne źródłowego:

   ```csharp
         adaptiveSourceManager.AdaptiveSourceOpenedEvent += 
           new AdaptiveSourceOpenedEventHandler(mediaElement_AdaptiveSourceOpened);
   ```
5. Naciśnij klawisz **CTRL + S** można zapisać pliku.

### <a name="to-add-adaptive-source-level-event-handlers"></a>Aby dodać procedury obsługi zdarzeń na poziomie adaptacyjne źródła

1. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy **MainPage.xaml**, a następnie kliknij przycisk **Wyświetl kod**.
2. Wewnątrz **MainPage** klasy, Dodaj następujący element członkowski danych:

   ```csharp
     private AdaptiveSourceStatusUpdatedEventArgs adaptiveSourceStatusUpdate; 
     private Manifest manifestObject;
   ```
3. Na koniec **MainPage** klasy, Dodaj następujące procedury obsługi zdarzeń:

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
4. Na koniec **mediaElement AdaptiveSourceOpened** metody, Dodaj następujący kod do subskrybowania zdarzenia:

   ```csharp
         adaptiveSource.ManifestReadyEvent +=

                    mediaElement_ManifestReady;
         adaptiveSource.AdaptiveSourceStatusUpdatedEvent += 

            mediaElement_AdaptiveSourceStatusUpdated;
         adaptiveSource.AdaptiveSourceFailedEvent += 

            mediaElement_AdaptiveSourceFailed;
   ```
5. Naciśnij klawisz **CTRL + S** można zapisać pliku.

Te same zdarzenia są dostępne na adaptacyjne Menedżer poziom źródła, która może służyć do obsługi funkcji wspólnych dla wszystkich elementów multimedialnych w aplikacji. Każdy AdaptiveSource obejmuje swoje własne zdarzenia, a wszystkie zdarzenia AdaptiveSource będzie kaskadowych w obszarze AdaptiveSourceManager.

### <a name="to-add-media-element-event-handlers"></a>Aby dodać obsługę zdarzeń Element multimedialny

1. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy **MainPage.xaml**, a następnie kliknij przycisk **Wyświetl kod**.
2. Na koniec **MainPage** klasy, Dodaj następujące procedury obsługi zdarzeń:

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
3. Na koniec **MainPage** konstruktora, Dodaj następujący kod do indeksu dolnego zdarzeń:

   ```csharp
         mediaElement.MediaOpened += MediaOpened;
         mediaElement.MediaEnded += MediaEnded;
         mediaElement.MediaFailed += MediaFailed;
   ```
4. Naciśnij klawisz **CTRL + S** można zapisać pliku.

### <a name="to-add-slider-bar-related-code"></a>Aby dodać paska suwaka powiązanego kodu

1. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy **MainPage.xaml**, a następnie kliknij przycisk **Wyświetl kod**.
2. Na początku pliku Dodaj następującą instrukcję using:

   ```csharp
        using Windows.UI.Core;
   ```
3. Wewnątrz **MainPage** klasy, Dodaj następujące elementy członkowskie danych:

   ```csharp
         public static CoreDispatcher _dispatcher;
         private DispatcherTimer sliderPositionUpdateDispatcher;
   ```
4. Na koniec **MainPage** konstruktora, Dodaj następujący kod:

   ```csharp
         _dispatcher = Window.Current.Dispatcher;
         PointerEventHandler pointerpressedhandler = new PointerEventHandler(sliderProgress_PointerPressed);
         sliderProgress.AddHandler(Control.PointerPressedEvent, pointerpressedhandler, true);    
   ```
5. Na koniec **MainPage** klasy, Dodaj następujący kod:

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
   > CoreDispatcher służy do wprowadzania zmian w wątku interfejsu użytkownika z innego niż wątek interfejsu użytkownika. W przypadku wąskiego gardła na Wątek dyspozytora dla deweloperów można używać dyspozytora dostarczone przez element interfejsu użytkownika, które zamierzają aktualizacji.  Na przykład:

   ```csharp
         await sliderProgress.Dispatcher.RunAsync(CoreDispatcherPriority.Normal, () => { TimeSpan 

         timespan = new TimeSpan(adaptiveSourceStatusUpdate.EndTime); 
         double absvalue  = (int)Math.Round(timespan.TotalSeconds, MidpointRounding.AwayFromZero); 

         sliderProgress.Maximum = absvalue; }); 
   ```
6. Na koniec **mediaElement_AdaptiveSourceStatusUpdated** metody, Dodaj następujący kod:

   ```csharp
         setSliderStartTime(args.StartTime);
         setSliderEndTime(args.EndTime);
   ```
7. Na koniec **MediaOpened** metody, Dodaj następujący kod:

   ```csharp
         sliderProgress.StepFrequency = SliderFrequency(mediaElement.NaturalDuration.TimeSpan);
         sliderProgress.Width = mediaElement.Width;
         setupTimer();
   ```
8. Naciśnij klawisz **CTRL + S** można zapisać pliku.

### <a name="to-compile-and-test-the-application"></a>Aby skompilować i przetestować aplikację

1. Naciśnij klawisz **F6** do skompilowania projektu. 
2. Naciśnij klawisz **F5**, aby uruchomić aplikację.
3. W górnej części aplikacji możesz użyć domyślnej Smooth Streaming URL lub wprowadź inną nazwę. 
4. Kliknij przycisk **Ustaw źródło**. 
5. Test paska suwaka.

Lekcja 2 została ukończona.  W tej lekcji dodano suwaka do aplikacji. 

## <a name="lesson-3-select-smooth-streaming-streams"></a>Lekcja 3: Wybierz strumieni Smooth Streaming
Smooth Streaming jest w stanie do strumieniowego przesyłania zawartości z wielu ścieżki audio języka, które można wybrać przez przeglądarki.  W tej lekcji spowoduje włączenie przeglądarki wybrać strumieni. W tej lekcji obejmuje następujące procedury:

1. Zmodyfikuj plik XAML
2. Modyfikowanie pliku CodeBehind
3. Kompilowanie i testowanie aplikacji

### <a name="to-modify-the-xaml-file"></a>Aby zmodyfikować plik XAML

1. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy **MainPage.xaml**, a następnie kliknij przycisk **Projektant widoków**.
2. Znajdź &lt;Grid.RowDefinitions&gt;i zmodyfikuj RowDefinitions, tak jak wygląda:

   ```xml
         <Grid.RowDefinitions>            
            <RowDefinition Height="20"/>
            <RowDefinition Height="50"/>
            <RowDefinition Height="100"/>
            <RowDefinition Height="80"/>
            <RowDefinition Height="50"/>
         </Grid.RowDefinitions>
   ```
3. Wewnątrz &lt;siatki&gt;&lt;/Grid&gt; tagi, Dodaj następujący kod do zdefiniowania kontrolki listbox, dzięki czemu użytkownicy mogą zapoznać się z listą dostępnych strumieni i wybierz strumieni:

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
4. Naciśnij klawisz **CTRL + S** Aby zapisać zmiany.

### <a name="to-modify-the-code-behind-file"></a>Aby zmodyfikować pliku CodeBehind

1. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy **MainPage.xaml**, a następnie kliknij przycisk **Wyświetl kod**.
2. W obszarze nazw SSPlayer Dodaj nową klasę:

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
3. Na początku klasy MainPage, Dodaj następujące definicje zmiennych:

   ```csharp
         private List<Stream> availableStreams;
         private List<Stream> availableAudioStreams;
         private List<Stream> availableTextStreams;
         private List<Stream> availableVideoStreams;
   ```
4. Wewnątrz klasy MainPage Dodaj następujący region:
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
5. Znajdź metodę mediaElement_ManifestReady, Dołącz następujący kod na końcu funkcji:
   ```csharp
        getStreams(manifestObject);
        refreshAvailableStreamsListBoxItemSource();
   ```
    Dlatego MediaElement manifest jest gotowy, kod pobiera listę dostępnych strumieni i wypełnia pola listy interfejsu użytkownika z listy.
6. Wewnątrz klasy MainPage zlokalizować interfejsu użytkownika przyciski kliknij region zdarzenia, a następnie dodaj następującą definicję funkcji:
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

1. Naciśnij klawisz **F6** do skompilowania projektu. 
2. Naciśnij klawisz **F5**, aby uruchomić aplikację.
3. W górnej części aplikacji możesz użyć domyślnej Smooth Streaming URL lub wprowadź inną nazwę. 
4. Kliknij przycisk **Ustaw źródło**. 
5. Domyślny język jest audio_eng. Spróbuj przełączyć się między audio_eng i audio_es. Za każdym razem, gdy, możesz wybrać nowy strumień, należy kliknąć przycisk Prześlij.

Lekcja 3 została ukończona.  W tej lekcji należy dodać funkcjonalność do wyboru strumieni.

## <a name="lesson-4-select-smooth-streaming-tracks"></a>Lekcja 4 Wybierz ścieżki Smooth Streaming

Smooth Streaming prezentacji może zawierać wiele plików wideo zakodowane za pomocą różne poziomy jakości (szybkości transmisji bitów) i rozwiązania. W tej lekcji umożliwi użytkownikom na wybór ścieżek. W tej lekcji obejmuje następujące procedury:

1. Zmodyfikuj plik XAML
2. Modyfikowanie pliku CodeBehind
3. Kompilowanie i testowanie aplikacji

### <a name="to-modify-the-xaml-file"></a>Aby zmodyfikować plik XAML

1. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy **MainPage.xaml**, a następnie kliknij przycisk **Projektant widoków**.
2. Znajdź &lt;siatki&gt; tag o nazwie **gridStreamAndBitrateSelection**, Dołącz następujący kod na końcu tagu:
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
3. Naciśnij klawisz **CTRL + S** Aby zapisać zmiany

### <a name="to-modify-the-code-behind-file"></a>Aby zmodyfikować pliku CodeBehind

1. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy **MainPage.xaml**, a następnie kliknij przycisk **Wyświetl kod**.
2. W obszarze nazw SSPlayer Dodaj nową klasę:
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
3. Na początku klasy MainPage, Dodaj następujące definicje zmiennych:
   ```csharp
        private List<Track> availableTracks;
   ```
4. Wewnątrz klasy MainPage Dodaj następujący region:
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
5. Znajdź metodę mediaElement_ManifestReady, Dołącz następujący kod na końcu funkcji:
   ```csharp
         getTracks(manifestObject);
         refreshAvailableTracksListBoxItemSource();
   ```
6. Wewnątrz klasy MainPage zlokalizować interfejsu użytkownika przyciski kliknij region zdarzenia, a następnie dodaj następującą definicję funkcji:
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

1. Naciśnij klawisz **F6** do skompilowania projektu. 
2. Naciśnij klawisz **F5**, aby uruchomić aplikację.
3. W górnej części aplikacji możesz użyć domyślnej Smooth Streaming URL lub wprowadź inną nazwę. 
4. Kliknij przycisk **Ustaw źródło**. 
5. Domyślnie wybrane są wszystkie ścieżki strumienia wideo. Eksperymentowanie zmiany szybkości bitowej, możesz wybrać najniższy szybkość transmisji bitów, dostępne, a następnie wybierz najwyższy szybkość transmisji bitów, dostępne. Po każdej zmianie musi kliknij przycisk Prześlij.  Możesz zobaczyć zmiany jakości wideo.

Lekcja 4 została ukończona.  W tej lekcji należy dodać funkcje, aby wybrać ścieżki.

## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przesyłanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="other-resources"></a>Inne zasoby dotyczące oprogramowania:
* [Jak utworzyć aplikację Smooth JavaScript 8 Windows przesyłania strumieniowego, za pomocą zaawansowanych funkcji](https://blogs.iis.net/cenkd/archive/2012/08/10/how-to-build-a-smooth-streaming-windows-8-javascript-application-with-advanced-features.aspx)
* [Bezproblemowe przesyłania strumieniowego — opis techniczny](https://www.iis.net/learn/media/on-demand-smooth-streaming/smooth-streaming-technical-overview)

[PlayerApplication]: ./media/media-services-build-smooth-streaming-apps/SSClientWin8-1.png
[CodeViewPic]: ./media/media-services-build-smooth-streaming-apps/SSClientWin8-2.png

