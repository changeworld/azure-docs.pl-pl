---
title: Funkcje Smooth Streaming samouczek aplikacji Sklepu Windows | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać usługi Azure Media Services do tworzenia aplikacji Sklepu Windows w języku C# za pomocą formantu XML MediaElement do strumienia Smooth odtwarzania zawartości.
services: media-services
documentationcenter: ''
author: juliako
manager: cfowler
editor: ''
ms.assetid: 0fa5d8c5-3d5f-4886-ae55-fb6de4f5256d
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/11/2017
ms.author: juliako
ms.openlocfilehash: f55e8aa652d65ea751a77742fa5823b09d9ee87b
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
ms.locfileid: "33788935"
---
# <a name="how-to-build-a-smooth-streaming-windows-store-application"></a>Jak tworzyć Smooth Streaming aplikację ze Sklepu Windows

Smooth Streaming klienta zestawu SDK dla systemu Windows 8 umożliwia deweloperom tworzenie aplikacji Sklepu Windows, które można odtwarzać zawartości na żądanie i na żywo Smooth Streaming. Oprócz podstawowych odtwarzania zawartości Smooth Streaming, zestaw SDK udostępnia zaawansowane funkcje, takie jak Microsoft PlayReady ochrony ograniczenie poziomu jakości, Live DVR, strumieniem audio przełączania, nasłuchiwanie aktualizacji stanu (np. zmiany poziomu jakości) i zdarzenia błędu i tak dalej. Aby uzyskać więcej informacji na obsługiwanych funkcji, zobacz [informacje o wersji](http://www.iis.net/learn/media/smooth-streaming/smooth-streaming-client-sdk-for-windows-8-release-notes). Aby uzyskać więcej informacji, zobacz [Framework Player dla systemu Windows 8](http://playerframework.codeplex.com/). 

Ten samouczek zawiera cztery lekcje:

1. Tworzenie podstawowej płynnego przesyłania strumieniowego aplikacji sklepu
2. Dodawanie paska suwaka do kontrolowania postępu nośnika
3. Wybierz Smooth Streaming strumieni
4. Wybierz Smooth Streaming ścieżki

## <a name="prerequisites"></a>Wymagania wstępne
> [!NOTE]
> Wersja projektów Sklepu Windows 8.1 i starsze nie są obsługiwane w programie Visual Studio 2017 r.  Aby uzyskać więcej informacji, zobacz [Obsługiwane platformy i zgodność programu Visual Studio 2017](https://www.visualstudio.com/en-us/productinfo/vs2017-compatibility-vs).

* Windows 8, 32-bitowy lub 64-bitowej.
* Visual Studio wersji 2012 za pomocą 2015.
* [Microsoft Smooth Streaming Client zestawu SDK dla systemu Windows 8](http://visualstudiogallery.msdn.microsoft.com/04423d13-3b3e-4741-a01c-1ae29e84fea6?SRC=Homehttp://visualstudiogallery.msdn.microsoft.com/04423d13-3b3e-4741-a01c-1ae29e84fea6?SRC=Home).

Gotowego rozwiązania dla każdej lekcji można pobrać z przykładów kodu deweloperów MSDN (Galeria kodu): 

* [Lekcja 1](http://code.msdn.microsoft.com/Smooth-Streaming-Client-0bb1471f) — sprawne prostego systemu Windows 8 przesyłania strumieniowego Media Player 
* [Lekcja 2](http://code.msdn.microsoft.com/A-simple-Windows-8-Smooth-ee98f63a) — sprawne prostego systemu Windows 8 przesyłania strumieniowego Media Player paska suwaka formantu 
* [Lekcja 3](http://code.msdn.microsoft.com/A-Windows-8-Smooth-883c3b44) — sprawne systemu Windows 8 przesyłania strumieniowego Media Player do wyboru strumienia  
* [Lekcja 4](http://code.msdn.microsoft.com/A-Windows-8-Smooth-aa9e4907) — sprawne systemu Windows 8 przesyłania strumieniowego Media Player śledzenie zaznaczenia.

## <a name="lesson-1-create-a-basic-smooth-streaming-store-application"></a>Lekcja 1: Tworzenie podstawowej płynnego przesyłania strumieniowego aplikacji sklepu

W tej lekcji utworzysz aplikację ze Sklepu Windows za pomocą formantu MediaElement do odtwarzania Smooth Stream zawartości.  Działającej aplikacji wygląda następująco:

![Przykładowa aplikacja Sklepu Windows Smooth Streaming][PlayerApplication]

Aby uzyskać więcej informacji na temat tworzenia aplikacji Sklepu Windows, temacie [tworzenie wspaniałych aplikacji dla systemu Windows 8](http://msdn.microsoft.com/windows/apps/br229512.aspx). W tej lekcji obejmuje następujące procedury:

1. Tworzenie projektu ze Sklepu Windows
2. Projektowanie interfejsu użytkownika (XAML)
3. Zmodyfikuj plik związany z kodem
4. Kompilowanie i testowanie aplikacji

**Aby utworzyć projekt Sklepu Windows**

1. Uruchom program Visual Studio; obsługiwane są wersje 2012 za pomocą 2015.
2. W menu **PLIK** kliknij pozycję **Nowy**, a następnie kliknij pozycję **Projekt**.
3. W oknie dialogowym Nowy projekt wpisz lub wybierz następujące wartości:

| Name (Nazwa) | Wartość |
| --- | --- |
| Grupa szablonów |Zainstalowane/szablony/Visual C# / ze Sklepu Windows |
| Szablon |Pusta aplikacja (XAML) |
| Name (Nazwa) |SSPlayer |
| Lokalizacja |C:\SSTutorials |
| Nazwa rozwiązania |SSPlayer |
| Utwórz katalog dla rozwiązania |(wybrano) |

1. Kliknij przycisk **OK**.

**Aby dodać odwołanie do Smooth Streaming SDK klienta**

1. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy **SSPlayer**, a następnie kliknij przycisk **Dodaj odwołanie**.
2. Wpisz lub wybierz poniższe wartości:

| Name (Nazwa) | Wartość |
| --- | --- |
| Grupy odwołania |Rozszerzenia/systemu Windows |
| Informacje ogólne |Wybierz Microsoft Smooth Streaming Client zestawu SDK dla systemu Windows 8 i Microsoft Visual C++ Runtime Package |

1. Kliknij przycisk **OK**. 

Po dodaniu odwołań, musisz wybrać platformę docelową (x64 lub x86), Dodawanie odwołania nie będą działać dla konfiguracji platformy dowolnego Procesora.  W Eksploratorze rozwiązań pojawi się żółty znak ostrzeżenie tych dodać odwołań.

**Projektowanie interfejsu użytkownika player**

1. W Eksploratorze rozwiązań kliknij dwukrotnie kliknij **MainPage.xaml** aby otworzyć go w widoku Projekt.
2. Zlokalizuj **&lt;siatki&gt;** i **&lt;/Grid&gt;** znaczniki pliku XAML i wklej następujący kod między tagami dwóch:

         <Grid.RowDefinitions>

            <RowDefinition Height="20"/>    <!-- spacer -->
            <RowDefinition Height="50"/>    <!-- media controls -->
            <RowDefinition Height="100*"/>  <!-- media element -->
            <RowDefinition Height="80*"/>   <!-- media stream and track selection -->
            <RowDefinition Height="50"/>    <!-- status bar -->
         </Grid.RowDefinitions>

         <StackPanel Name="spMediaControl" Grid.Row="1" Orientation="Horizontal">
            <TextBlock x:Name="tbSource" Text="Source :  " FontSize="16" FontWeight="Bold" VerticalAlignment="Center" />
            <TextBox x:Name="txtMediaSource" Text="http://ecn.channel9.msdn.com/o9/content/smf/smoothcontent/elephantsdream/Elephants_Dream_1024-h264-st-aac.ism/manifest" FontSize="10" Width="700" Margin="0,4,0,10" />
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
   
   Formant MediaElement służy do odtwarzania multimediów. Kontrolka suwaka o nazwie sliderProgress będzie można użyć w następnej lekcji do kontrolowania postępu nośnika.
3. Naciśnij klawisz **CTRL + S** można zapisać pliku.

Formant MediaElement nie obsługuje funkcji Smooth Streaming zawartości out-of-box. Aby włączyć obsługę Smooth Streaming, należy zarejestrować obsługi strumienia bajtów Smooth Streaming przez rozszerzenie nazwy pliku i typ MIME.  Aby zarejestrować, należy użyć metody MediaExtensionManager.RegisterByteStremHandler Windows.Media przestrzeni nazw.

W tym pliku XAML niektóre programy obsługi zdarzeń skojarzonych z formantami.  Należy zdefiniować te programy obsługi zdarzeń.

**Aby zmodyfikować kodzie pliku**

1. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy **MainPage.xaml**, a następnie kliknij przycisk **kod widoku**.
2. W górnej części pliku, dodaj następującą instrukcję using:
   
        using Windows.Media;
3. Na początku **MainPage** klasy, Dodaj następujący element członkowski danych:
   
         private MediaExtensionManager extensions = new MediaExtensionManager();
4. Na koniec **MainPage** konstruktora, Dodaj następujące dwa wiersze:
   
        extensions.RegisterByteStreamHandler("Microsoft.Media.AdaptiveStreaming.SmoothByteStreamHandler", ".ism", "text/xml");
        extensions.RegisterByteStreamHandler("Microsoft.Media.AdaptiveStreaming.SmoothByteStreamHandler", ".ism", "application/vnd.ms-sstr+xml");
5. Na koniec **MainPage** klasy, wklej następujący kod:
   
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

Program obsługi zdarzeń sliderProgress_PointerPressed jest zdefiniowany w tym miejscu.  Istnieje więcej działa zrobić, aby przygotować go do pracy, które będą objęte w następnej lekcji tego samouczka.
6. Naciśnij klawisz **CTRL + S** można zapisać pliku.

Zakończono kodzie pliku powinna wyglądać następująco:

![Codeview w aplikacji Visual Studio programu Smooth Streaming Sklepu Windows][CodeViewPic]

**Aby skompilować i testowanie aplikacji**

1. Z **kompilacji** menu, kliknij przycisk **programu Configuration Manager**.
2. Zmień **platformy aktywne rozwiązanie** odpowiadające rozwoju platformy.
3. Naciśnij klawisz **F6** skompilować projekt. 
4. Naciśnij klawisz **F5**, aby uruchomić aplikację.
5. W górnej części aplikacji możesz użyć domyślnej Smooth Streaming URL lub wprowadź inną. 
6. Kliknij przycisk **Ustaw źródło**. Ponieważ **Auto-odtwarzanie** jest włączona domyślnie nośnika jest odtwarzany automatycznie.  Można kontrolować przy użyciu nośnika **odtwarzanie**, **Wstrzymaj** i **zatrzymać** przycisków.  Można kontrolować woluminu nośnika przy użyciu pionowej kontrolce slider.  Jednak suwak poziomy kontroli postępu nośnika nie jest całkowicie jeszcze zaimplementowane. 

Lesson1 została ukończona.  W tej lekcji służy do odtwarzania zawartości Smooth Streaming Formant MediaElement.  W następnej lekcji doda suwak do kontrolowania postęp Smooth Streaming zawartości.

## <a name="lesson-2-add-a-slider-bar-to-control-the-media-progress"></a>Lekcja 2: Dodawanie paska suwaka do kontrolowania postępu nośnika

W Lekcja 1 utworzono aplikację ze Sklepu Windows za pomocą formantu MediaElement XAML do odtwarzania zawartości multimedialnej Smooth Streaming.  Pochodzi niektórych funkcji podstawowych nośników, takich jak start, stop i wstrzymania.  W tej lekcji formantu paska suwaka doda do aplikacji.

W tym samouczku używamy czasomierz do zaktualizowania pozycji suwaka na podstawie bieżącego położenia kontrolki MediaElement.  Suwak rozpoczęcia i zakończenia czasu również muszą zostać zaktualizowane w przypadku zawartości na żywo.  Mogą być lepiej obsłużone w zdarzeniu adaptacyjną źródła aktualizacji.

Źródeł multimediów to obiekty, które generować dane do nośnika.  Źródło programu rozpoznawania nazw przyjmujący element stream adres URL lub bajt i tworzy odpowiedni nośnik źródła dla tej zawartości.  Mechanizm rozpoznawania źródła jest standardowym sposobem dla aplikacji do tworzenia źródeł multimediów. 

W tej lekcji obejmuje następujące procedury:

1. Zarejestrować obsługi Smooth Streaming 
2. Dodawanie obsługi zdarzeń na poziomie menedżera adaptacyjną źródła
3. Dodawanie obsługi zdarzeń poziomu adaptacyjną źródła
4. Dodaj element MediaElement procedury obsługi zdarzeń
5. Dodaj suwaka kodu powiązanego
6. Kompilowanie i testowanie aplikacji

**Aby zarejestrować obsługi strumienia bajtów Smooth Streaming i przekaż propertyset**

1. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy **MainPage.xaml**, a następnie kliknij przycisk **kod widoku**.
2. Na początku pliku, dodaj następującą instrukcję using:

        using Microsoft.Media.AdaptiveStreaming;
3. Na początku klasy MainPage, Dodaj następujące elementy członkowskie danych:

         private Windows.Foundation.Collections.PropertySet propertySet = new Windows.Foundation.Collections.PropertySet();             
         private IAdaptiveSourceManager adaptiveSourceManager;
4. Wewnątrz **MainPage** konstruktora, Dodaj następujący kod po **to. Inicjowanie Components();**  wiersza i rejestracji kodu wierszach w poprzedniej lekcji:

        // Gets the default instance of AdaptiveSourceManager which manages Smooth 
        //Streaming media sources.
        adaptiveSourceManager = AdaptiveSourceManager.GetDefault();
        // Sets property key value to AdaptiveSourceManager default instance.
        // {A5CE1DE8-1D00-427B-ACEF-FB9A3C93DE2D}" must be hardcoded.
        propertySet["{A5CE1DE8-1D00-427B-ACEF-FB9A3C93DE2D}"] = adaptiveSourceManager;
5. Wewnątrz **MainPage** konstruktora, zmodyfikuj te dwie metody RegisterByteStreamHandler, aby dodać określonymi parametrami:

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
6. Naciśnij klawisz **CTRL + S** można zapisać pliku.

**Aby dodać obsługi zdarzeń na poziomie menedżera adaptacyjną źródła**

1. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy **MainPage.xaml**, a następnie kliknij przycisk **kod widoku**.
2. Wewnątrz **MainPage** klasy, Dodaj następujący element członkowski danych:
   
     prywatne adaptiveSource AdaptiveSource = null;
3. Na koniec **MainPage** klasy, Dodaj następujący program obsługi zdarzeń:
   
         # region Adaptive Source Manager Level Events
         private void mediaElement_AdaptiveSourceOpened(AdaptiveSource sender, AdaptiveSourceOpenedEventArgs args)
         {

            adaptiveSource = args.AdaptiveSource;
         }

         # endregion Adaptive Source Manager Level Events
4. Na koniec **MainPage** konstruktora, Dodaj następujący wiersz, aby subskrybować zdarzenia otwarte adaptacyjną źródłowego:
   
         adaptiveSourceManager.AdaptiveSourceOpenedEvent += 
           new AdaptiveSourceOpenedEventHandler(mediaElement_AdaptiveSourceOpened);
5. Naciśnij klawisz **CTRL + S** można zapisać pliku.

**Aby dodać obsługę zdarzeń poziomu adaptacyjną źródła**

1. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy **MainPage.xaml**, a następnie kliknij przycisk **kod widoku**.
2. Wewnątrz **MainPage** klasy, Dodaj następujący element członkowski danych:
   
     prywatne adaptiveSourceStatusUpdate AdaptiveSourceStatusUpdatedEventArgs;   prywatne manifestObject manifestu;
3. Na koniec **MainPage** klasy, Dodaj następujące programy obsługi zdarzeń:

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
4. Na koniec **mediaElement AdaptiveSourceOpened** metody, Dodaj następujący kod, aby subskrybować zdarzenia:
   
         adaptiveSource.ManifestReadyEvent +=

                    mediaElement_ManifestReady;
         adaptiveSource.AdaptiveSourceStatusUpdatedEvent += 

            mediaElement_AdaptiveSourceStatusUpdated;
         adaptiveSource.AdaptiveSourceFailedEvent += 

            mediaElement_AdaptiveSourceFailed;
5. Naciśnij klawisz **CTRL + S** można zapisać pliku.

Tego samego zdarzenia są dostępne na adaptacyjną Menedżer poziom źródła również, która może służyć do obsługi funkcji wspólne dla wszystkich elementów nośnika w aplikacji. Każdy AdaptiveSource zawiera własnych zdarzeń i wszystkie zdarzenia AdaptiveSource będzie kaskadowych w obszarze AdaptiveSourceManager.

**Aby dodać obsługę zdarzeń Element multimediów**

1. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy **MainPage.xaml**, a następnie kliknij przycisk **kod widoku**.
2. Na koniec **MainPage** klasy, Dodaj następujące programy obsługi zdarzeń:

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
3. Na koniec **MainPage** konstruktora, Dodaj następujący kod do indeks dolny do zdarzeń:

         mediaElement.MediaOpened += MediaOpened;
         mediaElement.MediaEnded += MediaEnded;
         mediaElement.MediaFailed += MediaFailed;
4. Naciśnij klawisz **CTRL + S** można zapisać pliku.

**Aby dodać paska suwaka związane z kodu**

1. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy **MainPage.xaml**, a następnie kliknij przycisk **kod widoku**.
2. Na początku pliku, dodaj następującą instrukcję using:
      
        using Windows.UI.Core;
3. Wewnątrz **MainPage** klasy, Dodaj następujące elementy członkowskie danych:
   
         public static CoreDispatcher _dispatcher;
         private DispatcherTimer sliderPositionUpdateDispatcher;
4. Na koniec **MainPage** konstruktora, Dodaj następujący kod:
   
         _dispatcher = Window.Current.Dispatcher;
         PointerEventHandler pointerpressedhandler = new PointerEventHandler(sliderProgress_PointerPressed);
         sliderProgress.AddHandler(Control.PointerPressedEvent, pointerpressedhandler, true);    
5. Na koniec **MainPage** klasy, Dodaj następujący kod:

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
      
>[!NOTE]
>CoreDispatcher jest używany do wprowadzania zmian w wątku interfejsu użytkownika z wątku interfejsu użytkownika innych niż. W przypadku "wąskie gardło" na Wątek dyspozytora developer mogą być używane dyspozytora dostarczonych przez jego zamierza zaktualizować elementu interfejsu użytkownika.  Na przykład:
   
         await sliderProgress.Dispatcher.RunAsync(CoreDispatcherPriority.Normal, () => { TimeSpan 

         timespan = new TimeSpan(adaptiveSourceStatusUpdate.EndTime); 
         double absvalue  = (int)Math.Round(timespan.TotalSeconds, MidpointRounding.AwayFromZero); 

         sliderProgress.Maximum = absvalue; }); 
6. Na koniec **mediaElement_AdaptiveSourceStatusUpdated** metody, Dodaj następujący kod:

         setSliderStartTime(args.StartTime);
         setSliderEndTime(args.EndTime);
7. Na koniec **MediaOpened** metody, Dodaj następujący kod:

         sliderProgress.StepFrequency = SliderFrequency(mediaElement.NaturalDuration.TimeSpan);
         sliderProgress.Width = mediaElement.Width;
         setupTimer();
8. Naciśnij klawisz **CTRL + S** można zapisać pliku.

**Aby skompilować i testowanie aplikacji**

1. Naciśnij klawisz **F6** skompilować projekt. 
2. Naciśnij klawisz **F5**, aby uruchomić aplikację.
3. W górnej części aplikacji możesz użyć domyślnej Smooth Streaming URL lub wprowadź inną. 
4. Kliknij przycisk **Ustaw źródło**. 
5. Przetestuj paska suwaka.

Lekcja 2 została ukończona.  W tej lekcji suwaka jest dodanych do aplikacji. 

## <a name="lesson-3-select-smooth-streaming-streams"></a>Lekcja 3: Wybierz Smooth Streaming strumieni
Smooth Streaming jest w stanie do strumieniowego przesyłania zawartości z wielu ścieżek audio języka, które można wybrać przez przeglądarki.  W tej lekcji spowoduje włączenie przeglądarki wybrać strumieni. W tej lekcji obejmuje następujące procedury:

1. Zmodyfikuj plik XAML
2. Modyfikowanie pliku behand kodu
3. Kompilowanie i testowanie aplikacji

**Aby zmodyfikować plik XAML**

1. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy **MainPage.xaml**, a następnie kliknij przycisk **Widok projektanta**.
2. Zlokalizuj &lt;Grid.RowDefinitions&gt;i zmodyfikować RowDefinitions, aby ich wygląda następująco:
   
         <Grid.RowDefinitions>            
            <RowDefinition Height="20"/>
            <RowDefinition Height="50"/>
            <RowDefinition Height="100"/>
            <RowDefinition Height="80"/>
            <RowDefinition Height="50"/>
         </Grid.RowDefinitions>
3. Wewnątrz &lt;siatki&gt;&lt;/Grid&gt; tagów, Dodaj następujący kod do zdefiniowania kontrolki listbox, dlatego użytkownicy mogą zobaczyć listę dostępnych strumieni i wybierz strumieni:

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
4. Naciśnij klawisz **CTRL + S** Aby zapisać zmiany.

**Aby zmodyfikować kodzie pliku**

1. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy **MainPage.xaml**, a następnie kliknij przycisk **kod widoku**.
2. W obszarze nazw SSPlayer Dodaj nową klasę:
   
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
3. Na początku klasy MainPage, Dodaj następujące definicje zmiennych:
   
         private List<Stream> availableStreams;
         private List<Stream> availableAudioStreams;
         private List<Stream> availableTextStreams;
         private List<Stream> availableVideoStreams;
4. Wewnątrz klasy MainPage Dodaj następujący region:
   
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
   
            // Select the frist video stream from the list if no video stream is selected
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
   
            // Select the frist audio stream from the list if no audio steam is selected.
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
5. Zlokalizuj metody mediaElement_ManifestReady, Dołącz następujący kod na końcu funkcji:
   
        getStreams(manifestObject);
        refreshAvailableStreamsListBoxItemSource();
   
    Element MediaElement manifestu jest gotowy, kod pobiera listę dostępnych strumieni i wypełnia pole listy interfejsu użytkownika z listy.
6. Wewnątrz klasy MainPage zlokalizować interfejsu użytkownika przycisków kliknij region zdarzenia, a następnie dodaj następującą definicję funkcji:
   
        private void btnChangeStream_Click(object sender, RoutedEventArgs e)
        {
            List<IManifestStream> selectedStreams = new List<IManifestStream>();
   
            // Create a list of the selected streams
            createSelectedStreamsList(selectedStreams);
   
            // Change streams on the presentation
            changeStreams(selectedStreams);
        }

**Aby skompilować i testowanie aplikacji**

1. Naciśnij klawisz **F6** skompilować projekt. 
2. Naciśnij klawisz **F5**, aby uruchomić aplikację.
3. W górnej części aplikacji możesz użyć domyślnej Smooth Streaming URL lub wprowadź inną. 
4. Kliknij przycisk **Ustaw źródło**. 
5. Język domyślny jest audio_eng. Spróbuj przełączać się między audio_eng i audio_es. Zawsze, gdy, wybierz nowego strumienia, należy kliknąć przycisk przesyłania.

Ukończono Lekcja 3.  W tej lekcji możesz dodać funkcje wybrać strumieni.

## <a name="lesson-4-select-smooth-streaming-tracks"></a>Lekcja 4: Wybierz Smooth Streaming ścieżki
Prezentacja Smooth Streaming może zawierać wiele plików wideo zakodowane za pomocą różne poziomy jakości (szybkości transmisji bitów) i rozwiązania. W tej lekcji umożliwi użytkownikom na wybór ścieżki. W tej lekcji obejmuje następujące procedury:

1. Zmodyfikuj plik XAML
2. Modyfikowanie pliku behand kodu
3. Kompilowanie i testowanie aplikacji

**Aby zmodyfikować plik XAML**

1. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy **MainPage.xaml**, a następnie kliknij przycisk **Widok projektanta**.
2. Zlokalizuj &lt;siatki&gt; tag o nazwie **gridStreamAndBitrateSelection**, Dołącz następujący kod na końcu tagu:
   
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
3. Naciśnij klawisz **CTRL + S** można zapisać zmian he

**Aby zmodyfikować kodzie pliku**

1. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy **MainPage.xaml**, a następnie kliknij przycisk **kod widoku**.
2. W obszarze nazw SSPlayer Dodaj nową klasę:
   
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
3. Na początku klasy MainPage, Dodaj następujące definicje zmiennych:
   
        private List<Track> availableTracks;
4. Wewnątrz klasy MainPage Dodaj następujący region:
   
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
5. Zlokalizuj metody mediaElement_ManifestReady, Dołącz następujący kod na końcu funkcji:
   
         getTracks(manifestObject);
         refreshAvailableTracksListBoxItemSource();
6. Wewnątrz klasy MainPage zlokalizować interfejsu użytkownika przycisków kliknij region zdarzenia, a następnie dodaj następującą definicję funkcji:
   
         private void btnChangeStream_Click(object sender, RoutedEventArgs e)
         {
            List<IManifestStream> selectedStreams = new List<IManifestStream>();

            // Create a list of the selected streams
            createSelectedStreamsList(selectedStreams);

            // Change streams on the presentation
            changeStreams(selectedStreams);
         }

**Aby skompilować i testowanie aplikacji**

1. Naciśnij klawisz **F6** skompilować projekt. 
2. Naciśnij klawisz **F5**, aby uruchomić aplikację.
3. W górnej części aplikacji możesz użyć domyślnej Smooth Streaming URL lub wprowadź inną. 
4. Kliknij przycisk **Ustaw źródło**. 
5. Domyślnie wszystkie ścieżki strumienia wideo wybrane. Wypróbować zmiany szybkości bitowej, można wybrać najniższa szybkość transmisji bitów, dostępne, a następnie wybierz najwyższy szybkość transmisji bitów, dostępne. Należy kliknąć przesyłania po każdej zmianie.  Zmiany jakości wideo jest widoczny.

Ukończono lekcji 4.  W tej lekcji możesz dodać funkcje, aby wybrać ścieżki.

## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="other-resources"></a>Inne zasoby:
* [Jak utworzyć aplikację z zaawansowanych funkcji Smooth JavaScript 8 Windows przesyłania strumieniowego](http://blogs.iis.net/cenkd/archive/2012/08/10/how-to-build-a-smooth-streaming-windows-8-javascript-application-with-advanced-features.aspx)
* [Płynnego przesyłania strumieniowego — omówienie](http://www.iis.net/learn/media/on-demand-smooth-streaming/smooth-streaming-technical-overview)

[PlayerApplication]: ./media/media-services-build-smooth-streaming-apps/SSClientWin8-1.png
[CodeViewPic]: ./media/media-services-build-smooth-streaming-apps/SSClientWin8-2.png

