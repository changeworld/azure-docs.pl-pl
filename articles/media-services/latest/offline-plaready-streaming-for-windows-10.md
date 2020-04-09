---
title: Konfigurowanie przesyłania strumieniowego PlayReady w trybie offline za pomocą usługi Azure Media Services w wersji 3
description: W tym artykule pokazano, jak skonfigurować konto usługi Azure Media Services do przesyłania strumieniowego PlayReady dla systemu Windows 10 w trybie offline.
services: media-services
keywords: DASH, DRM, Widevine Offline Mode, ExoPlayer, Android
documentationcenter: ''
author: willzhan
manager: steveng
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/01/2019
ms.author: willzhan
ms.openlocfilehash: 151aadadb5674f7f144d42b1f9d5115501ed381d
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80887235"
---
# <a name="offline-playready-streaming-for-windows-10-with-media-services-v3"></a>Przesyłanie strumieniowe playready w trybie offline dla systemu Windows 10 z usługą Media Services w wersji 3

Usługa Azure Media Services obsługuje pobieranie/odtwarzanie w trybie offline z ochroną DRM. W tym artykule omówiono obsługę usługi Azure Media Services dla klientów systemu Windows 10/PlayReady w trybie offline. O obsłudze trybu offline dla urządzeń z systemem iOS/FairPlay i Android/Widevine można przeczytać w następujących artykułach:

- [Przesyłanie strumieniowe w trybie offline przy użyciu technologii FairPlay na potrzeby systemu iOS](offline-fairplay-for-ios.md)
- [Przesyłanie strumieniowe widevine w trybie offline dla systemu Android](offline-widevine-for-android.md)

> [!NOTE]
> Usługa DRM w trybie offline jest naliczona tylko za złożenie jednego żądania licencji podczas pobierania zawartości. Wszelkie błędy nie są rozliczane.

## <a name="overview"></a>Omówienie

W tej sekcji przedstawiono pewne tło podczas odtwarzania w trybie offline, zwłaszcza dlaczego:

* W niektórych krajach/regionach dostępność Internetu i/lub przepustowość jest nadal ograniczona.Użytkownicy mogą najpierw pobrać, aby móc oglądać zawartość w wystarczająco wysokiej rozdzielczości, aby zapewnić zadowalające wrażenia wizualne. W tym przypadku częściej problemem nie jest dostępność sieci, a raczej ograniczona przepustowość sieci. Dostawcy OTT/OVP proszą o obsługę trybu offline.
* Jak ujawniono na konferencji akcjonariuszy Netflix 2016 Q3, pobieranie treści jest "funkcją typu ofich.
* Niektórzy dostawcy treści mogą nie zezwalać na dostarczanie licencji DRM poza granicami kraju/regionu. Jeśli użytkownik musi podróżować za granicę i nadal chce oglądać zawartość, wymagane jest pobieranie w trybie offline.
 
Wyzwanie, przed jakim stoimy podczas wdrażania trybu offline, jest następujące:

* MP4 jest obsługiwany przez wielu graczy, narzędzia kodera, ale nie ma powiązania między kontenerem MP4 i DRM;
* W dłuższej perspektywie, CFF z CENC jest do zrobienia. Jednak dzisiaj, narzędzia / ekosystem wsparcia gracza nie ma jeszcze. Dziś potrzebujemy rozwiązania.
 
Chodzi o to: płynne przesyłanie strumieniowe[(PIFF)](https://docs.microsoft.com/iis/media/smooth-streaming/protected-interoperable-file-format)format pliku z H264/AAC ma wiązanie z PlayReady (AES-128 CTR). Indywidualny płynny plik .ismv (zakładając, że dźwięk jest muxed w wideo) sam jest fMP4 i może być używany do odtwarzania. Jeśli płynna zawartość przesyłania strumieniowego przechodzi przez szyfrowanie PlayReady, każdy plik .ismv staje się chronionym fragmentarycznym plikiem MP4. Możemy wybrać plik .ismv z preferowaną szybkością transmisji bitów i zmienić jego nazwę na .mp4 do pobrania.

Istnieją dwie opcje hostingu chronionego MP4 PlayReady do progresywnego pobierania:

* Można umieścić ten plik MP4 w tym samym zasobie kontenera/usługi multimedialnej i wykorzystać punkt końcowy przesyłania strumieniowego usługi Azure Media Services do pobierania progresywnego;
* Można użyć lokalizatora sygnatury dostępu Współdzielonego do pobierania progresywnego bezpośrednio z usługi Azure Storage, z pominięciem usługi Azure Media Services.
 
Można użyć dwóch typów dostarczania licencji PlayReady:

* Usługa dostarczania licencji PlayReady w usłudze Azure Media Services;
* Serwery licencji PlayReady hostowane w dowolnym miejscu.

Poniżej znajdują się dwa zestawy zasobów testowych, pierwszy przy użyciu dostarczania licencji PlayReady w systemie AMS, podczas gdy drugi przy użyciu serwera licencji PlayReady hostowane na maszynie Wirtualnej platformy Azure:

#1 aktywów:

* Progresywny adres URL pobierania:[https://willzhanmswest.streaming.mediaservices.windows.net/8d078cf8-d621-406c-84ca-88e6b9454acc/20150807-bridges-2500_H264_1644kbps_AAC_und_ch2_256kbps.mp4](https://willzhanmswest.streaming.mediaservices.windows.net/8d078cf8-d621-406c-84ca-88e6b9454acc/20150807-bridges-2500_H264_1644kbps_AAC_und_ch2_256kbps.mp4")
* LA_URL PlayReady (AMS):[https://willzhanmswest.keydelivery.mediaservices.windows.net/PlayReady/](https://willzhanmswest.keydelivery.mediaservices.windows.net/PlayReady/)

#2 aktywów:

* Progresywny adres URL pobierania:[https://willzhanmswest.streaming.mediaservices.windows.net/7c085a59-ae9a-411e-842c-ef10f96c3f89/20150807-bridges-2500_H264_1644kbps_AAC_und_ch2_256kbps.mp4](https://willzhanmswest.streaming.mediaservices.windows.net/7c085a59-ae9a-411e-842c-ef10f96c3f89/20150807-bridges-2500_H264_1644kbps_AAC_und_ch2_256kbps.mp4)
* PlayReady LA_URL (lokalnie):[https://willzhan12.cloudapp.net/playready/rightsmanager.asmx](https://willzhan12.cloudapp.net/playready/rightsmanager.asmx)

Do testowania odtwarzania użyliśmy uniwersalnej aplikacji systemu Windows w systemie Windows 10. W [windows 10 Uniwersalne próbki,](https://github.com/Microsoft/Windows-universal-samples)istnieje podstawowy przykład odtwarzacza o nazwie [Adaptive Streaming Sample](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/AdaptiveStreaming). Wszystko, co musimy zrobić, to dodać kod dla nas, aby wybrać pobrany film i używać go jako źródła, zamiast adaptacyjnego źródła strumieniowego. Zmiany są w programie obsługi zdarzeń kliknięcia przycisku:

```csharp
private async void LoadUri_Click(object sender, RoutedEventArgs e)
{
    //Uri uri;
    //if (!Uri.TryCreate(UriBox.Text, UriKind.Absolute, out uri))
    //{
    // Log("Malformed Uri in Load text box.");
    // return;
    //}
    //LoadSourceFromUriTask = LoadSourceFromUriAsync(uri);
    //await LoadSourceFromUriTask;

    //willzhan change start
    // Create and open the file picker
    FileOpenPicker openPicker = new FileOpenPicker();
    openPicker.ViewMode = PickerViewMode.Thumbnail;
    openPicker.SuggestedStartLocation = PickerLocationId.ComputerFolder;
    openPicker.FileTypeFilter.Add(".mp4");
    openPicker.FileTypeFilter.Add(".ismv");
    //openPicker.FileTypeFilter.Add(".mkv");
    //openPicker.FileTypeFilter.Add(".avi");

    StorageFile file = await openPicker.PickSingleFileAsync();

    if (file != null)
    {
       //rootPage.NotifyUser("Picked video: " + file.Name, NotifyType.StatusMessage);
       this.mediaPlayerElement.MediaPlayer.Source = MediaSource.CreateFromStorageFile(file);
       this.mediaPlayerElement.MediaPlayer.Play();
       UriBox.Text = file.Path;
    }
    else
    {
       // rootPage.NotifyUser("Operation cancelled.", NotifyType.ErrorMessage);
    }

    // On small screens, hide the description text to make room for the video.
    DescriptionText.Visibility = (ActualHeight < 500) ? Visibility.Collapsed : Visibility.Visible;
}
```

![Odtwarzanie w trybie offline chronionego fMP4 w trybie offline](./media/offline-playready-for-windows/offline-playready1.jpg)

Ponieważ film jest w obszarze ochrony PlayReady, zrzut ekranu nie będzie mógł dołączyć wideo.

Podsumowując, osiągnęliśmy tryb offline w usłudze Azure Media Services:

* Transkodowanie zawartości i szyfrowanie PlayReady można wykonać w usłudze Azure Media Services lub innych narzędziach;
* Zawartość może być hostowana w usłudze Azure Media Services lub usłudze Azure Storage do pobierania progresywnego;
* Dostarczanie licencji PlayReady może pochodzić z usługi Azure Media Services lub w innym miejscu;
* Przygotowana płynna zawartość strumieniowa może być nadal używana do przesyłania strumieniowego online za pośrednictwem DASH lub płynnego z PlayReady jako DRM.

## <a name="next-steps"></a>Następne kroki

[Projektowanie systemu ochrony zawartości przy użyciu technologii multi-DRM z kontrolą dostępu](design-multi-drm-system-with-access-control.md)
