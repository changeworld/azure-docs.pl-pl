---
title: 'Szybki start: Niestandardowy głos — pierwszy asystent wirtualny (wersja zapoznawcza), C# (platformy UWP) — usługa mowy'
titleSuffix: Azure Cognitive Services
description: W tym artykule opisano tworzenie aplikacji C# platforma uniwersalna systemu Windows (platformy UWP) za pomocą zestawu Cognitive Services Speech Software Development Kit (SDK). Możesz połączyć aplikację kliencką z wcześniej utworzonym bot Framework bot skonfigurowanym do korzystania z kanału bezpośredniej linii mowy. Aplikacja będzie kompilowana przy użyciu pakietu NuGet zestawu Speech SDK i programu Microsoft Visual Studio 2017.
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: travisw
ms.openlocfilehash: 6d88bcc6d9d2c2e46ef3a3cb841c9268a544e37c
ms.sourcegitcommit: 6cff17b02b65388ac90ef3757bf04c6d8ed3db03
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/29/2019
ms.locfileid: "68609693"
---
# <a name="quickstart-create-a-voice-first-virtual-assistant-with-the-speech-sdk-uwp"></a>Szybki start: Tworzenie wirtualnego asystenta głosowego przy użyciu zestawu Speech SDK, platformy UWP

Przewodniki Szybki Start są również dostępne w przypadku [zamiany mowy na tekst](quickstart-csharp-uwp.md), zamiany [tekstu na mowę](quickstart-text-to-speech-csharp-uwp.md) i zamiany [mowy](quickstart-translate-speech-uwp.md)na mowę.

W tym artykule opisano tworzenie aplikacji C# platforma uniwersalna systemu Windows (platformy UWP) przy użyciu [zestawu Speech SDK](speech-sdk.md). Program nawiąże połączenie z wcześniej utworzonym i skonfigurowanym bot, aby umożliwić wirtualne środowisko asystenta wirtualnego z aplikacji klienckiej. Aplikacja została skompilowana z pakietem [NuGet zestawu Speech SDK](https://aka.ms/csspeech/nuget) i Microsoft Visual Studio 2017 lub nowszą (dowolna wersja).

> [!NOTE]
> Platforma uniwersalna systemu Windows pozwala tworzyć aplikacje, które działają na dowolnym urządzeniu z systemem Windows 10, m.in. na komputerach PC, konsolach Xbox, urządzeniach Surface Hub itp.

## <a name="prerequisites"></a>Wymagania wstępne

Ten przewodnik Szybki start wymaga następujących elementów:

* [Program Visual Studio 2017](https://visualstudio.microsoft.com/downloads/) lub nowszy
* Klucz subskrypcji platformy Azure dla usługi Speech Services. [Uzyskaj jeden bezpłatnie](get-started.md) lub utwórz go na [Azure Portal](https://portal.azure.com).
* Wcześniej utworzono bot skonfigurowany za pomocą [kanału Direct line](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech)

    > [!NOTE]
    > Bezpośredni wiersz mowy (wersja zapoznawcza) jest obecnie dostępny w podzestawie regionów usługi Speech Services. Zapoznaj się z [listą obsługiwanych regionów dla wirtualnych asystentów głosowych](regions.md#voice-first-virtual-assistants) i upewnij się, że zasoby są wdrożone w jednym z tych regionów.

## <a name="optional-get-started-fast"></a>Opcjonalnie: Szybkie rozpoczynanie pracy

Ten przewodnik Szybki Start opisuje krok po kroku, jak utworzyć prostą aplikację kliencką do łączenia się z botem z obsługą mowy. Jeśli wolisz szczegółowe z prawej strony, kompletny, gotowy do skompilowania kod źródłowy używany w tym przewodniku Szybki Start jest dostępny `quickstart` w [przykładach zestawu Speech SDK](https://aka.ms/csspeech/samples) w folderze.

## <a name="create-a-visual-studio-project"></a>Tworzenie projektu programu Visual Studio

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-uwp-create-proj.md)]

## <a name="add-sample-code"></a>Dodaj kod przykładowy

1. Interfejs użytkownika aplikacji jest definiowany przy użyciu języka XAML. Otwórz `MainPage.xaml` w Eksploratorze rozwiązań. W widoku XAML projektanta Zastąp całą zawartość poniższymi.

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
            <StackPanel Orientation="Vertical" HorizontalAlignment="Center"  Margin="20,50,0,0" VerticalAlignment="Center" Width="800">
                <Button x:Name="EnableMicrophoneButton" Content="Enable Microphone"  Margin="0,0,10,0" Click="EnableMicrophone_ButtonClicked" Height="35"/>
                <Button x:Name="ListenButton" Content="Talk to your bot" Margin="0,10,10,0" Click="ListenButton_ButtonClicked" Height="35"/>
                <StackPanel x:Name="StatusPanel" Orientation="Vertical" RelativePanel.AlignBottomWithPanel="True" RelativePanel.AlignRightWithPanel="True" RelativePanel.AlignLeftWithPanel="True">
                    <TextBlock x:Name="StatusLabel" Margin="0,10,10,0" TextWrapping="Wrap" Text="Status:" FontSize="20"/>
                    <Border x:Name="StatusBorder" Margin="0,0,0,0">
                        <ScrollViewer VerticalScrollMode="Auto"  VerticalScrollBarVisibility="Auto" MaxHeight="200">
                            <!-- Use LiveSetting to enable screen readers to announce the status update. -->
                            <TextBlock x:Name="StatusBlock" FontWeight="Bold" AutomationProperties.LiveSetting="Assertive"
                    MaxWidth="{Binding ElementName=Splitter, Path=ActualWidth}" Margin="10,10,10,20" TextWrapping="Wrap"  />
                        </ScrollViewer>
                    </Border>
                </StackPanel>
            </StackPanel>
            <MediaElement x:Name="mediaElement"/>
        </Grid>
    </Page>
    ```

1. Otwórz plik `MainPage.xaml.cs`źródłowy związany z kodem. Znajdziesz go w sekcji `MainPage.xaml`. Zastąp zawartość następującym kodem. Oto, co obejmuje ten przykład:

    * Używanie instrukcji dla przestrzeni nazw mowy i mowy. okna dialogowego
    * Prosta implementacja zapewniająca dostęp do mikrofonu przy użyciu sieci przewodowej do procedury obsługi przycisku
    * Podstawowe pomocnicy interfejsu użytkownika do prezentowania komunikatów i błędów w aplikacji
    * Punkt wyładunkowy dla ścieżki kodu inicjującego, który zostanie zapełniony później
    * Pomocnik odtwarzania zamiany tekstu na mowę (bez obsługi przesyłania strumieniowego)
    * Pusta procedura obsługi przycisku do rozpoczęcia nasłuchiwania, która zostanie wypełniona później

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

            private async void EnableMicrophone_ButtonClicked(object sender, RoutedEventArgs e)
            {
                bool isMicAvailable = true;
                try
                {
                    var mediaCapture = new Windows.Media.Capture.MediaCapture();
                    var settings = new Windows.Media.Capture.MediaCaptureInitializationSettings();
                    settings.StreamingCaptureMode = Windows.Media.Capture.StreamingCaptureMode.Audio;
                    await mediaCapture.InitializeAsync(settings);
                }
                catch (Exception)
                {
                    isMicAvailable = false;
                }
                if (!isMicAvailable)
                {
                    await Windows.System.Launcher.LaunchUriAsync(new Uri("ms-settings:privacy-microphone"));
                }
                else
                {
                    NotifyUser("Microphone was enabled", NotifyType.StatusMessage);
                }
            }

            private void NotifyUser(string strMessage, NotifyType type = NotifyType.StatusMessage)
            {
                // If called from the UI thread, then update immediately.
                // Otherwise, schedule a task on the UI thread to perform the update.
                if (Dispatcher.HasThreadAccess)
                {
                    UpdateStatus(strMessage, type);
                }
                else
                {
                    var task = Dispatcher.RunAsync(Windows.UI.Core.CoreDispatcherPriority.Normal, () => UpdateStatus(strMessage, type));
                }
            }

            private void UpdateStatus(string strMessage, NotifyType type)
            {
                switch (type)
                {
                    case NotifyType.StatusMessage:
                        StatusBorder.Background = new SolidColorBrush(Windows.UI.Colors.Green);
                        break;
                    case NotifyType.ErrorMessage:
                        StatusBorder.Background = new SolidColorBrush(Windows.UI.Colors.Red);
                        break;
                }
                StatusBlock.Text += string.IsNullOrEmpty(StatusBlock.Text) ? strMessage : "\n" + strMessage;

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
                // Raise an event if necessary to enable a screen reader to announce the status update.
                var peer = Windows.UI.Xaml.Automation.Peers.FrameworkElementAutomationPeer.FromElement(StatusBlock);
                if (peer != null)
                {
                    peer.RaiseAutomationEvent(Windows.UI.Xaml.Automation.Peers.AutomationEvents.LiveRegionChanged);
                }
            }

            // Waits for accumulates all audio associated with a given PullAudioOutputStream and then plays it to the
            // MediaElement. Long spoken audio will create extra latency and a streaming playback solution (that plays
            // audio while it continues to be received) should be used -- see the samples for examples of this.
            private void SynchronouslyPlayActivityAudio(PullAudioOutputStream activityAudio)
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

                var task = Dispatcher.RunAsync(Windows.UI.Core.CoreDispatcherPriority.Normal, () =>
                {
                    mediaElement.SetSource(playbackStreamWithHeader.AsRandomAccessStream(), "audio/wav");
                    mediaElement.Play();
                });
            }

            private void InitializeDialogServiceConnector()
            {
                // New code will go here
            }

            private async void ListenButton_ButtonClicked(object sender, RoutedEventArgs e)
            {
                // New code will go here
            }
        }
    }
    ```

1. Następnie utworzysz `DialogServiceConnector` wraz z informacjami o subskrypcji. Dodaj następujące `InitializeDialogServiceConnector`elementy do treści metody, zastępując ciągi `YourChannelSecret`, `YourSpeechSubscriptionKey`i `YourServiceRegion` własnymi wartościami dla bot, subskrypcji mowy i [regionu](regions.md).

    > [!NOTE]
    > Bezpośredni wiersz mowy (wersja zapoznawcza) jest obecnie dostępny w podzestawie regionów usługi Speech Services. Zapoznaj się z [listą obsługiwanych regionów dla wirtualnych asystentów głosowych](regions.md#voice-first-virtual-assistants) i upewnij się, że zasoby są wdrożone w jednym z tych regionów.

    > [!NOTE]
    > Aby uzyskać informacje na temat konfigurowania bot i pobierania wpisu tajnego kanału, zobacz dokumentację struktury bot dla [kanału Direct Speech line](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech).

    ```csharp
    // create a DialogServiceConfig by providing a bot secret key and Cognitive Services subscription key
    // the RecoLanguage property is optional (default en-US); note that only en-US is supported in Preview
    const string channelSecret = "YourChannelSecret"; // Your channel secret
    const string speechSubscriptionKey = "YourSpeechSubscriptionKey"; // Your subscription key
    const string region = "YourServiceRegion"; // Your subscription service region. Note: only a subset of regions are currently supported

    var botConfig = DialogServiceConfig.FromBotSecret(channelSecret, speechSubscriptionKey, region);
    botConfig.SetProperty(PropertyId.SpeechServiceConnection_RecoLanguage, "en-US");
    connector = new DialogServiceConnector(botConfig);
    ```

1. `DialogServiceConnector`opiera się na kilku zdarzeniach, aby komunikować swoje działania bot, wyniki rozpoznawania mowy i inne informacje. Dodaj programy obsługi dla tych zdarzeń, dołączając następujące polecenie na końcu treści `InitializeDialogServiceConnector`metody.

    ```csharp
    // ActivityReceived is the main way your bot will communicate with the client and uses bot framework activities
    connector.ActivityReceived += async (sender, activityReceivedEventArgs) =>
    {
        NotifyUser($"Activity received, hasAudio={activityReceivedEventArgs.HasAudio} activity={activityReceivedEventArgs.Activity}");

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
            NotifyUser($"Error: code={canceledEventArgs.ErrorCode}, details={canceledEventArgs.ErrorDetails}");
        }
    };
    // Recognizing (not 'Recognized') will provide the intermediate recognized text while an audio stream is being processed
    connector.Recognizing += (sender, recognitionEventArgs) =>
    {
        NotifyUser($"Recognizing! in-progress text={recognitionEventArgs.Result.Text}");
    };
    // Recognized (not 'Recognizing') will provide the final recognized text once audio capture is completed
    connector.Recognized += (sender, recognitionEventArgs) =>
    {
        NotifyUser($"Final speech-to-text result: '{recognitionEventArgs.Result.Text}'");
    };
    // SessionStarted will notify when audio begins flowing to the service for a turn
    connector.SessionStarted += (sender, sessionEventArgs) =>
    {
        NotifyUser($"Now Listening! Session started, id={sessionEventArgs.SessionId}");
    };
    // SessionStopped will notify when a turn is complete and it's safe to begin listening again
    connector.SessionStopped += (sender, sessionEventArgs) =>
    {
        NotifyUser($"Listening complete. Session ended, id={sessionEventArgs.SessionId}");
    };
    ```

1. Po nawiązaniu konfiguracji i zarejestrowaniu obsługi zdarzeń, `DialogServiceConnector` teraz wystarczy nasłuchiwać. Dodaj następujący tekst do treści `ListenButton_ButtonClicked` metody `MainPage` w klasie.

    ```csharp
    private async void ListenButton_ButtonClicked(object sender, RoutedEventArgs e)
    {
        if (connector == null)
        {
            InitializeDialogServiceConnector();
            // Optional step to speed up first interaction: if not called, connection happens automatically on first use
            var connectTask = connector.ConnectAsync();
        }

        try
        {
            // Start sending audio to your speech-enabled bot
            var listenTask = connector.ListenOnceAsync();

            // You can also send activities to your bot as JSON strings -- Microsoft.Bot.Schema can simplify this
            string speakActivity = @"{""type"":""message"",""text"":""Greeting Message"", ""speak"":""Hello there!""}";
            await connector.SendActivityAsync(speakActivity);

        }
        catch (Exception ex)
        {
            NotifyUser($"Exception: {ex.ToString()}", NotifyType.ErrorMessage);
        }
    }
    ```

1. Zapisz wszystkie zmiany w projekcie.

## <a name="build-and-run-the-app"></a>Kompilowanie i uruchamianie aplikacji

1. Skompiluj aplikację. Na pasku menu programu Visual Studio wybierz opcję **Kompiluj** > **kompilację rozwiązania**. Kod powinien teraz zostać skompilowany bez błędów.

    ![Zrzut ekranu aplikacji Visual Studio z wyróżnioną opcją Kompiluj rozwiązanie](media/sdk/qs-csharp-uwp-08-build.png "Kompilacja ukończona pomyślnie")

1. Uruchom aplikację. Na pasku menu programu Visual Studio wybierz kolejno opcje **Debuguj** > **Rozpocznij debugowanie**lub naciśnij klawisz **F5**.

    ![Zrzut ekranu aplikacji Visual Studio z wyróżnioną opcją Rozpocznij debugowanie](media/sdk/qs-csharp-uwp-09-start-debugging.png "Uruchamianie aplikacji do debugowania")

1. Zostanie wyświetlone okno podręczne. W aplikacji wybierz pozycję **Włącz mikrofon**i potwierdź żądanie uprawnienia, które wyskakujące.

    ![Zrzut ekranu żądania uprawnień](media/sdk/qs-csharp-uwp-10-access-prompt.png "Uruchom aplikację do debugowania")

1. Wybierz opcję **Rozmawiaj z Twoim botem**i zacznij mówić do angielskiej frazy lub zdania do mikrofonu Twojego urządzenia. Twoja Zamiana jest przesyłana do kanału mowy Direct line i uzyskanego do tekstu, który pojawia się w oknie.

    ![Zrzut ekranu przedstawiający pomyślne włączenie bot](media/voice-first-virtual-assistants/quickstart-cs-uwp-bot-successful-turn.png "Pomyślne włączenie bot")

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Tworzenie i wdrażanie podstawowego bota](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-basic-deploy?view=azure-bot-service-4.0)

## <a name="see-also"></a>Zobacz także

- [Informacje o Asystencie wirtualnym w pierwszej kolejności głosowej](voice-first-virtual-assistants.md)
- [Uzyskaj bezpłatnie klucz subskrypcji usługi Speech Services](get-started.md)
- [Niestandardowe słowa wznawiania](speech-devices-sdk-create-kws.md)
- [Połącz bezpośrednią mowę wiersza z bot](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech)
- [Zapoznaj się z przykładami dla języka C# w usłudze GitHub](https://aka.ms/csspeech/samples)
