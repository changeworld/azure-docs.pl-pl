---
title: 'Szybki start: łączenie się z aplikacją Polecenia niestandardowe za pomocą sdk mowy — usługa mowy'
titleSuffix: Azure Cognitive Services
description: W tym artykule utworzysz aplikację kliencką SDK mowy za pomocą poleceń niestandardowych.
services: cognitive-services
author: don-d-kim
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/09/2019
ms.author: donkim
ms.openlocfilehash: 9e324af0b90f595b5b7af2a417a562efb193d854
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "76156781"
---
# <a name="quickstart-connect-to-a-custom-commands-application-with-the-speech-sdk-preview"></a>Szybki start: łączenie się z aplikacją Polecenia niestandardowe za pomocą sdk mowy (wersja zapoznawcza)

Po utworzeniu hosta aplikacji polecenia niestandardowe, można rozpocząć rozmowy z nim z urządzenia klienckiego.

W tym artykule:

- Publikowanie aplikacji polecenia niestandardowe i uzyskanie identyfikatora aplikacji (identyfikator aplikacji)
- Tworzenie aplikacji klienckiej przy użyciu SDK mowy, aby umożliwić rozmawianie z aplikacją Polecenia niestandardowe

## <a name="prerequisites"></a>Wymagania wstępne

Aplikacja Polecenia niestandardowe jest wymagana do ukończenia tego artykułu. Jeśli aplikacja Polecenia niestandardowe nie została jeszcze utworzona, można to zrobić w poprzednich przewodnikach Szybki start:

- [Szybki start: tworzenie polecenia niestandardowego (wersja zapoznawcza)](./quickstart-custom-speech-commands-create-new.md)
- [Szybki start: tworzenie niestandardowego polecenia z parametrami (wersja zapoznawcza)](./quickstart-custom-speech-commands-create-parameters.md)

Potrzebne będą również:

- [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)
- Klucz subskrypcji platformy Azure dla usług mowy. [Pobierz go za darmo](get-started.md) lub utwórz go w [witrynie Azure portal](https://portal.azure.com)

## <a name="optional-get-started-fast"></a>Opcjonalnie: szybkie rozpoczęcie pracy

W tym przewodniku Szybki start opisano krok po kroku, jak zrobić aplikację kliencką, aby połączyć się z aplikacją polecenia niestandardowe. Jeśli wolisz nurkować bezpośrednio w, kompletny, gotowy do kompilacji kod źródłowy używany w tym przewodniku `quickstart` Szybki start jest dostępny w [przykładach zestawów SDK mowy](https://aka.ms/csspeech/samples) w folderze.

## <a name="step-1-publish-custom-commands-application"></a>Krok 1: Publikowanie aplikacji Polecenia niestandardowe

1. Otwórz [wcześniej utworzoną aplikację Polecenia niestandardowe](./quickstart-custom-speech-commands-create-new.md) i wybierz pozycję **Publikuj**

   > [!div class="mx-imgBorder"]
   > ![Publikowanie aplikacji](media/custom-speech-commands/fulfill-sdk-publish-application.png)

1. Skopiuj identyfikator aplikacji z powiadomienia o publikacji w celu późniejszego użycia

## <a name="step-2-create-a-visual-studio-project"></a>Krok 2: Tworzenie projektu programu Visual Studio

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-uwp-create-proj.md)]

## <a name="step-3-add-sample-code"></a>Krok 3: Dodaj przykładowy kod

W tym kroku dodajemy kod XAML, który definiuje interfejs użytkownika aplikacji i dodaj implementację w języku C# bez kodu.

### <a name="xaml-code"></a>Kod XAML

Utwórz interfejs użytkownika aplikacji, dodając kod XAML.

1. W **Eksploratorze rozwiązań**otwórz`MainPage.xaml`

1. W widoku XAML projektanta zastąp całą zawartość następującym fragmentem kodu:

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
               <Button x:Name="ListenButton" Content="Talk"
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

Widok Projekt jest aktualizowany w celu wyświetlenia interfejsu użytkownika aplikacji.

### <a name="c-code-behind-source"></a>Źródło kodu języka C#

Dodaj źródło związanye z kodem, tak aby aplikacja działała zgodnie z oczekiwaniami. Źródło oparte na kodzie obejmuje:

- Wymagane `using` instrukcje `Speech` dla `Speech.Dialog` i obszarów nazw
- Prosta implementacja zapewniająca dostęp do mikrofonu, przewodowa do obsługi przycisków
- Podstawowe pomocnicy interfejsu użytkownika do prezentowania komunikatów i błędów w aplikacji
- Punkt docelowy ścieżki kodu inicjowania, który zostanie wypełniony później
- Pomocnik do odtwarzania tekstu na mowę (bez obsługi przesyłania strumieniowego)
- Obsługa pustych przycisków, aby rozpocząć nasłuchiwanie, które zostanie wypełnione później

Dodaj źródło oparte na kodzie w następujący sposób:

1. W **Eksploratorze rozwiązań**otwórz `MainPage.xaml.cs` plik źródłowy związany z kodem (zgrupowany w obszarze) `MainPage.xaml`

1. Zastąp zawartość pliku następującym kodem:

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

1. Dodaj następujący kod do treści metody`InitializeDialogServiceConnector`

   ```csharp
   // This code creates the `DialogServiceConnector` with your subscription information.
   // create a DialogServiceConfig by providing a Custom Commands application id and Cognitive Services subscription key
   // the RecoLanguage property is optional (default en-US); note that only en-US is supported in Preview
   const string speechCommandsApplicationId = "YourApplicationId"; // Your application id
   const string speechSubscriptionKey = "YourSpeechSubscriptionKey"; // Your subscription key
   const string region = "YourServiceRegion"; // The subscription service region. Note: only 'westus2' is currently supported

   var speechCommandsConfig = CustomCommandsConfig.FromSubscription(speechCommandsApplicationId, speechSubscriptionKey, region);
   speechCommandsConfig.SetProperty(PropertyId.SpeechServiceConnection_RecoLanguage, "en-us");
   connector = new DialogServiceConnector(speechCommandsConfig);
   ```

1. Zastąp `YourApplicationId` `YourSpeechSubscriptionKey`ciągi `YourServiceRegion` , i własne wartości dla aplikacji, subskrypcji mowy i [regionu](regions.md)

1. Dołącz poniższy fragment kodu na końcu treści metody`InitializeDialogServiceConnector`

   ```csharp
   //
   // This code sets up handlers for events relied on by `DialogServiceConnector` to communicate its activities,
   // speech recognition results, and other information.
   //
   // ActivityReceived is the main way your client will receive messages, audio, and events
   connector.ActivityReceived += async (sender, activityReceivedEventArgs) =>
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

1. Dodaj następujący fragment kodu do treści `ListenButton_ButtonClicked` metody w `MainPage` klasie

   ```csharp
   // This code sets up `DialogServiceConnector` to listen, since you already established the configuration and
   // registered the event handlers.
   if (connector == null)
   {
       InitializeDialogServiceConnector();
       // Optional step to speed up first interaction: if not called,
       // connection happens automatically on first use
       var connectTask = connector.ConnectAsync();
   }

   try
   {
       // Start sending audio
       await connector.ListenOnceAsync();
   }
   catch (Exception ex)
   {
       NotifyUser($"Exception: {ex.ToString()}", NotifyType.ErrorMessage);
   }
   ```

1. Na pasku menu wybierz **pozycję Zapisz** > **wszystko,** aby zapisać zmiany

## <a name="build-and-run-the-application"></a>Kompilowanie i uruchamianie aplikacji

1. Na pasku menu wybierz pozycję **Build** > **Build Solution** to build the application. Kod powinien zostać skompilowany bez błędów.

1. Aby uruchomić aplikację, wybierz **opcję Debugowanie** > **rozpocznij debugowanie** (lub naciśnij **klawisz F5).** Zostanie **wyświetlene okno helloworld.**

   ![Przykładowa aplikacja wirtualnego asystenta platformy uniwersalnej systemu w języku C# — szybki start](media/sdk/qs-voice-assistant-uwp-helloworld-window.png)

1. Wybierz **włącz mikrofon**. Jeśli pojawi się żądanie uprawnień dostępu, wybierz pozycję **Tak**.

   ![Żądanie uprawnień dostępu do mikrofonu](media/sdk/qs-csharp-uwp-10-access-prompt.png)

1. Wybierz **opcję Porozmawiaj**i mów w języku angielskim do mikrofonu urządzenia. Mowa jest przesyłana do kanału Direct Line Speech i transkrybowana na tekst, który pojawia się w oknie.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Jak: Wypełnianie poleceń na kliencie za pomocą zestawu SDK mowy (wersja zapoznawcza)](./how-to-custom-speech-commands-fulfill-sdk.md)
> [Jak: Dodawanie weryfikacji do parametrów polecenia niestandardowego (wersja zapoznawcza)](./how-to-custom-speech-commands-validations.md)
