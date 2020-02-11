---
title: 'Szybki Start: niestandardowy asystenta C# głosowego (platformy UWP) — usługa mowy'
titleSuffix: Azure Cognitive Services
description: W tym artykule opisano tworzenie aplikacji C# platforma uniwersalna systemu Windows (platformy UWP) za pomocą zestawu Cognitive Services Speech Software Development Kit (SDK). Możesz połączyć aplikację kliencką z wcześniej utworzonym bot Framework bot skonfigurowanym do korzystania z kanału bezpośredniej linii mowy. Aplikacja została skompilowana z pakietem NuGet zestawu mowy SDK i Microsoft Visual Studio 2019.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 02/10/2020
ms.author: dapine
ms.openlocfilehash: af9395b8f75a9097d4449571fd54be6259a7b9f3
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/11/2020
ms.locfileid: "77119784"
---
# <a name="quickstart-create-a-voice-assistant-with-the-speech-sdk-uwp"></a>Szybki Start: Tworzenie asystenta głosowego za pomocą zestawu Speech SDK, platformy UWP

Przewodniki Szybki Start są również dostępne dla [funkcji rozpoznawania mowy](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=uwp), [syntezy mowy](~/articles/cognitive-services/Speech-Service/quickstarts/text-to-speech.md?pivots=programming-language-csharp&tabs=uwp)i [tłumaczenia mowy](~/articles/cognitive-services/Speech-Service/quickstarts/translate-speech-to-text.md?pivots=programming-language-csharp&tabs=uwp).

W tym artykule opisano tworzenie aplikacji C# platforma uniwersalna systemu Windows (platformy UWP) przy użyciu [zestawu Speech SDK](speech-sdk.md). Program zostanie połączony z wcześniej utworzonym i skonfigurowanym bot, aby umożliwić korzystanie z asystenta głosowego z aplikacji klienckiej. Aplikacja jest zbudowana z [pakietem NuGet zestawu Speech SDK](https://aka.ms/csspeech/nuget) i Microsoft Visual Studio 2019 (w dowolnej wersji).

> [!NOTE]
> Platforma uniwersalna systemu Windows pozwala tworzyć aplikacje, które działają na dowolnym urządzeniu z systemem Windows 10, m.in. na komputerach PC, konsolach Xbox, urządzeniach Surface Hub itp.

## <a name="prerequisites"></a>Wymagania wstępne

Ten przewodnik Szybki start wymaga następujących elementów:

* [Program Visual Studio 2019](https://visualstudio.microsoft.com/downloads/).
* Klucz subskrypcji platformy Azure dla usługi Mowa. [Uzyskaj jeden bezpłatnie](get-started.md) lub utwórz go na [Azure Portal](https://portal.azure.com).
* Wcześniej utworzono bot skonfigurowany za pomocą [kanału bezpośredniej linii mowy](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech).

  > [!NOTE]
  > Zapoznaj się z [listą obsługiwanych regionów dla asystentów głosowych](regions.md#voice-assistants) i upewnij się, że zasoby zostały wdrożone w jednym z tych regionów.

## <a name="optional-get-started-fast"></a>Opcjonalne: szybkie rozpoczynanie pracy

Ten przewodnik Szybki Start opisuje krok po kroku, jak umożliwić aplikacji klienckiej łączenie się z usługą bot z obsługą mowy. Jeśli wolisz szczegółowe z prawej strony, kompletny, gotowy do skompilowania kod źródłowy użyty w tym przewodniku Szybki Start jest dostępny w [przykładach zestawu Speech SDK](https://aka.ms/csspeech/samples) w folderze `quickstart`.

## <a name="create-a-visual-studio-project"></a>Tworzenie projektu programu Visual Studio

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-uwp-create-proj.md)]

## <a name="add-sample-code"></a>Dodawanie przykładowego kodu

Teraz Dodaj kod XAML, który definiuje interfejs użytkownika aplikacji, a następnie Dodaj implementację z C# kodem.

### <a name="xaml-code"></a>Kod XAML

Najpierw utworzysz interfejs użytkownika aplikacji, dodając kod XAML:

1. W **Eksplorator rozwiązań**Otwórz `MainPage.xaml`.

1. W widoku XAML projektanta Zastąp całą zawartość następującym fragmentem kodu:

    ```xml
    <Page
        x:Class="helloworld.MainPage"
        xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        xmlns:local="using:helloworld"
        xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
        xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
        mc:Ignorable="d"
        Background="{ThemeResource ApplicationPageBackgroundThemeBrush}">

        <Grid>
            <StackPanel Orientation="Vertical" HorizontalAlignment="Center"  
                        Margin="20,50,0,0" VerticalAlignment="Center" Width="800">
                <Button x:Name="EnableMicrophoneButton" Content="Enable Microphone"  
                        Margin="0,0,10,0" Click="EnableMicrophone_ButtonClicked" 
                        Height="35"/>
                <Button x:Name="ListenButton" Content="Talk to your bot" 
                        Margin="0,10,10,0" Click="ListenButton_ButtonClicked" 
                        Height="35"/>
                <StackPanel x:Name="StatusPanel" Orientation="Vertical" 
                            RelativePanel.AlignBottomWithPanel="True" 
                            RelativePanel.AlignRightWithPanel="True" 
                            RelativePanel.AlignLeftWithPanel="True">
                    <TextBlock x:Name="StatusLabel" Margin="0,10,10,0" 
                               TextWrapping="Wrap" Text="Status:" FontSize="20"/>
                    <Border x:Name="StatusBorder" Margin="0,0,0,0">
                        <ScrollViewer VerticalScrollMode="Auto"  
                                      VerticalScrollBarVisibility="Auto" MaxHeight="200">
                            <!-- Use LiveSetting to enable screen readers to announce 
                                 the status update. -->
                            <TextBlock 
                                x:Name="StatusBlock" FontWeight="Bold" 
                                AutomationProperties.LiveSetting="Assertive"
                                MaxWidth="{Binding ElementName=Splitter, Path=ActualWidth}" 
                                Margin="10,10,10,20" TextWrapping="Wrap"  />
                        </ScrollViewer>
                    </Border>
                </StackPanel>
            </StackPanel>
            <MediaElement x:Name="mediaElement"/>
        </Grid>
    </Page>
    ```

Widok Projekt został zaktualizowany, aby pokazać interfejs użytkownika aplikacji.

### <a name="c-code-behind-source"></a>C#Źródło związane z kodem

Następnie należy dodać Źródło związane z kodem, aby aplikacja działała zgodnie z oczekiwaniami. Źródło związane z kodem obejmuje:

- instrukcje `using` dla przestrzeni nazw `Speech` i `Speech.Dialog`
- Prosta implementacja zapewniająca dostęp do mikrofonu przy użyciu sieci przewodowej do procedury obsługi przycisku
- Podstawowe pomocnicy interfejsu użytkownika do prezentowania komunikatów i błędów w aplikacji
- Punkt wyładunkowy dla ścieżki kodu inicjującego, który zostanie zapełniony później
- Pomocnik odtwarzania zamiany tekstu na mowę (bez obsługi przesyłania strumieniowego)
- Pusta procedura obsługi przycisku do rozpoczęcia nasłuchiwania, która zostanie wypełniona później

Aby dodać Źródło związane z kodem, wykonaj następujące kroki:

1. W **Eksplorator rozwiązań**Otwórz plik źródłowy związany z kodem `MainPage.xaml.cs`. (Jest ono pogrupowane w obszarze `MainPage.xaml`).

1. Zastąp zawartość pliku następującym fragmentem kodu:

    ```csharp
    using Microsoft.CognitiveServices.Speech;
    using Microsoft.CognitiveServices.Speech.Audio;
    using Microsoft.CognitiveServices.Speech.Dialog;
    using System;
    using System.Diagnostics;
    using System.IO;
    using System.Text;
    using Windows.Foundation;
    using Windows.Storage.Streams;
    using Windows.UI.Xaml;
    using Windows.UI.Xaml.Controls;
    using Windows.UI.Xaml.Media;

    namespace helloworld
    {
        public sealed partial class MainPage : Page
        {
            private DialogServiceConnector connector;

            private enum NotifyType
            {
                StatusMessage,
                ErrorMessage
            };

            public MainPage()
            {
                this.InitializeComponent();
            }

            private async void EnableMicrophone_ButtonClicked(
                object sender, RoutedEventArgs e)
            {
                bool isMicAvailable = true;
                try
                {
                    var mediaCapture = new Windows.Media.Capture.MediaCapture();
                    var settings = 
                        new Windows.Media.Capture.MediaCaptureInitializationSettings();
                    settings.StreamingCaptureMode = 
                        Windows.Media.Capture.StreamingCaptureMode.Audio;
                    await mediaCapture.InitializeAsync(settings);
                }
                catch (Exception)
                {
                    isMicAvailable = false;
                }
                if (!isMicAvailable)
                {
                    await Windows.System.Launcher.LaunchUriAsync(
                        new Uri("ms-settings:privacy-microphone"));
                }
                else
                {
                    NotifyUser("Microphone was enabled", NotifyType.StatusMessage);
                }
            }

            private void NotifyUser(
                string strMessage, NotifyType type = NotifyType.StatusMessage)
            {
                // If called from the UI thread, then update immediately.
                // Otherwise, schedule a task on the UI thread to perform the update.
                if (Dispatcher.HasThreadAccess)
                {
                    UpdateStatus(strMessage, type);
                }
                else
                {
                    var task = Dispatcher.RunAsync(
                        Windows.UI.Core.CoreDispatcherPriority.Normal, 
                        () => UpdateStatus(strMessage, type));
                }
            }

            private void UpdateStatus(string strMessage, NotifyType type)
            {
                switch (type)
                {
                    case NotifyType.StatusMessage:
                        StatusBorder.Background = new SolidColorBrush(
                            Windows.UI.Colors.Green);
                        break;
                    case NotifyType.ErrorMessage:
                        StatusBorder.Background = new SolidColorBrush(
                            Windows.UI.Colors.Red);
                        break;
                }
                StatusBlock.Text += string.IsNullOrEmpty(StatusBlock.Text) 
                    ? strMessage : "\n" + strMessage;

                if (!string.IsNullOrEmpty(StatusBlock.Text))
                {
                    StatusBorder.Visibility = Visibility.Visible;
                    StatusPanel.Visibility = Visibility.Visible;
                }
                else
                {
                    StatusBorder.Visibility = Visibility.Collapsed;
                    StatusPanel.Visibility = Visibility.Collapsed;
                }
                // Raise an event if necessary to enable a screen reader 
                // to announce the status update.
                var peer = Windows.UI.Xaml.Automation.Peers.FrameworkElementAutomationPeer.FromElement(StatusBlock);
                if (peer != null)
                {
                    peer.RaiseAutomationEvent(
                        Windows.UI.Xaml.Automation.Peers.AutomationEvents.LiveRegionChanged);
                }
            }

            // Waits for and accumulates all audio associated with a given 
            // PullAudioOutputStream and then plays it to the MediaElement. Long spoken 
            // audio will create extra latency and a streaming playback solution 
            // (that plays audio while it continues to be received) should be used -- 
            // see the samples for examples of this.
            private void SynchronouslyPlayActivityAudio(
                PullAudioOutputStream activityAudio)
            {
                var playbackStreamWithHeader = new MemoryStream();
                playbackStreamWithHeader.Write(Encoding.ASCII.GetBytes("RIFF"), 0, 4); // ChunkID
                playbackStreamWithHeader.Write(BitConverter.GetBytes(UInt32.MaxValue), 0, 4); // ChunkSize: max
                playbackStreamWithHeader.Write(Encoding.ASCII.GetBytes("WAVE"), 0, 4); // Format
                playbackStreamWithHeader.Write(Encoding.ASCII.GetBytes("fmt "), 0, 4); // Subchunk1ID
                playbackStreamWithHeader.Write(BitConverter.GetBytes(16), 0, 4); // Subchunk1Size: PCM
                playbackStreamWithHeader.Write(BitConverter.GetBytes(1), 0, 2); // AudioFormat: PCM
                playbackStreamWithHeader.Write(BitConverter.GetBytes(1), 0, 2); // NumChannels: mono
                playbackStreamWithHeader.Write(BitConverter.GetBytes(16000), 0, 4); // SampleRate: 16kHz
                playbackStreamWithHeader.Write(BitConverter.GetBytes(32000), 0, 4); // ByteRate
                playbackStreamWithHeader.Write(BitConverter.GetBytes(2), 0, 2); // BlockAlign
                playbackStreamWithHeader.Write(BitConverter.GetBytes(16), 0, 2); // BitsPerSample: 16-bit
                playbackStreamWithHeader.Write(Encoding.ASCII.GetBytes("data"), 0, 4); // Subchunk2ID
                playbackStreamWithHeader.Write(BitConverter.GetBytes(UInt32.MaxValue), 0, 4); // Subchunk2Size

                byte[] pullBuffer = new byte[2056];

                uint lastRead = 0;
                do
                {
                    lastRead = activityAudio.Read(pullBuffer);
                    playbackStreamWithHeader.Write(pullBuffer, 0, (int)lastRead);
                }
                while (lastRead == pullBuffer.Length);

                var task = Dispatcher.RunAsync(
                    Windows.UI.Core.CoreDispatcherPriority.Normal, () =>
                {
                    mediaElement.SetSource(
                        playbackStreamWithHeader.AsRandomAccessStream(), "audio/wav");
                    mediaElement.Play();
                });
            }

            private void InitializeDialogServiceConnector()
            {
                // New code will go here
            }

            private async void ListenButton_ButtonClicked(
                object sender, RoutedEventArgs e)
            {
                // New code will go here
            }
        }
    }
    ```

1. Dodaj następujący fragment kodu do treści metody `InitializeDialogServiceConnector`. Ten kod tworzy `DialogServiceConnector` z informacjami o subskrypcji.

    ```csharp
    // Create a BotFrameworkConfig by providing a Speech service subscription key
    // the RecoLanguage property is optional (default en-US)
    const string speechSubscriptionKey = "YourSpeechSubscriptionKey"; // Your subscription key
    const string region = "YourServiceRegion"; // Your subscription service region.

    var botConfig = BotFrameworkConfig.FromSubscription(speechSubscriptionKey, region);
    botConfig.SetProperty(PropertyId.SpeechServiceConnection_RecoLanguage, "en-US");
    connector = new DialogServiceConnector(botConfig);
    ```

   > [!NOTE]
   > Zapoznaj się z [listą obsługiwanych regionów dla asystentów głosowych](regions.md#voice-assistants) i upewnij się, że zasoby zostały wdrożone w jednym z tych regionów.

   > [!NOTE]
   > Aby uzyskać informacje na temat konfigurowania bot i pobierania wpisu tajnego kanału, zobacz dokumentację struktury bot dla [kanału Direct Speech line](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech).

1. Zastąp ciągi `YourChannelSecret`, `YourSpeechSubscriptionKey`i `YourServiceRegion` własnymi wartościami dla bot, subskrypcji mowy i [regionu](regions.md).

1. Dołącz Poniższy fragment kodu do końca treści metody `InitializeDialogServiceConnector`. Ten kod konfiguruje programy obsługi dla zdarzeń, na których opiera się `DialogServiceConnector` do przekazywania działań bot, wyników rozpoznawania mowy i innych informacji.

    ```csharp
    // ActivityReceived is the main way your bot will communicate with the client 
    // and uses bot framework activities
    connector.ActivityReceived += (sender, activityReceivedEventArgs) =>
    {
        NotifyUser(
            $"Activity received, hasAudio={activityReceivedEventArgs.HasAudio} activity={activityReceivedEventArgs.Activity}");

        if (activityReceivedEventArgs.HasAudio)
        {
            SynchronouslyPlayActivityAudio(activityReceivedEventArgs.Audio);
        }
    };

    // Canceled will be signaled when a turn is aborted or experiences an error condition
    connector.Canceled += (sender, canceledEventArgs) =>
    {
        NotifyUser($"Canceled, reason={canceledEventArgs.Reason}");
        if (canceledEventArgs.Reason == CancellationReason.Error)
        {
            NotifyUser(
                $"Error: code={canceledEventArgs.ErrorCode}, details={canceledEventArgs.ErrorDetails}");
        }
    };

    // Recognizing (not 'Recognized') will provide the intermediate recognized text 
    // while an audio stream is being processed
    connector.Recognizing += (sender, recognitionEventArgs) =>
    {
        NotifyUser($"Recognizing! in-progress text={recognitionEventArgs.Result.Text}");
    };

    // Recognized (not 'Recognizing') will provide the final recognized text 
    // once audio capture is completed
    connector.Recognized += (sender, recognitionEventArgs) =>
    {
        NotifyUser($"Final speech-to-text result: '{recognitionEventArgs.Result.Text}'");
    };

    // SessionStarted will notify when audio begins flowing to the service for a turn
    connector.SessionStarted += (sender, sessionEventArgs) =>
    {
        NotifyUser($"Now Listening! Session started, id={sessionEventArgs.SessionId}");
    };

    // SessionStopped will notify when a turn is complete and 
    // it's safe to begin listening again
    connector.SessionStopped += (sender, sessionEventArgs) =>
    {
        NotifyUser($"Listening complete. Session ended, id={sessionEventArgs.SessionId}");
    };
    ```

1. Dodaj następujący fragment kodu do treści metody `ListenButton_ButtonClicked` w klasie `MainPage`. Ten kod konfiguruje `DialogServiceConnector` do nasłuchiwania, ponieważ konfiguracja została już zarejestrowana i zarejestrowano procedury obsługi zdarzeń.

    ```csharp
    if (connector == null)
    {
        InitializeDialogServiceConnector();
        // Optional step to speed up first interaction: if not called, 
        // connection happens automatically on first use
        var connectTask = connector.ConnectAsync();
    }

    try
    {
        // Start sending audio to your speech-enabled bot
        var listenTask = connector.ListenOnceAsync();

        // You can also send activities to your bot as JSON strings -- 
        // Microsoft.Bot.Schema can simplify this
        string speakActivity = 
            @"{""type"":""message"",""text"":""Greeting Message"", ""speak"":""Hello there!""}";
        await connector.SendActivityAsync(speakActivity);

    }
    catch (Exception ex)
    {
        NotifyUser($"Exception: {ex.ToString()}", NotifyType.ErrorMessage);
    }
    ```

1. Na pasku menu wybierz kolejno opcje **plik** > **Zapisz wszystko** , aby zapisać zmiany.

## <a name="build-and-run-the-application"></a>Kompilowanie i uruchamianie aplikacji

Teraz możesz przystąpić do kompilowania i testowania aplikacji.

1. Na pasku menu wybierz kolejno opcje **kompiluj** > **Kompiluj rozwiązanie** , aby skompilować aplikację. Kod powinien teraz zostać skompilowany bez błędów.

1. Wybierz **debuguj** > **Rozpocznij debugowanie** (lub naciśnij klawisz **F5**), aby uruchomić aplikację. Zostanie wyświetlone okno **HelloWorld** .

   ![Przykładowa aplikacja asystenta głosowego platformy UWP C# — Szybki Start](media/sdk/qs-voice-assistant-uwp-helloworld-window.png)

1. Wybierz pozycję **Włącz mikrofon**i po włączeniu żądania uprawnienia dostępu wybierz pozycję **tak**.

   ![Żądanie uprawnienia dostępu do mikrofonu](media/sdk/qs-csharp-uwp-10-access-prompt.png)

1. Wybierz opcję **Rozmawiaj z Twoim botem**i zacznij mówić do angielskiej frazy lub zdania do mikrofonu Twojego urządzenia. Twoja Zamiana jest przesyłana do kanału mowy Direct line i uzyskanego do tekstu, który pojawia się w oknie.
<!--
    ![Successful bot response](media/voice-assistants/quickstart-cs-uwp-bot-successful-turn.png)
-->
## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Tworzenie i wdrażanie podstawowego bota](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-basic-deploy?view=azure-bot-service-4.0)

## <a name="see-also"></a>Zobacz też

- [Asystenci głosu — informacje](voice-assistants.md)
- [Uzyskaj bezpłatnie klucz subskrypcji usługi mowy](get-started.md)
- [Niestandardowe słowa kluczowe](speech-devices-sdk-create-kws.md)
- [Połącz bezpośrednią mowę wiersza z bot](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech)
- [Zapoznaj się z przykładami dla języka C# w usłudze GitHub](https://aka.ms/csspeech/samples)
