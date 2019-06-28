---
title: Skonfiguruj swoje konto w trybie offline do przesyłania strumieniowego zawartości PlayReady chronionych - Azure
description: W tym artykule przedstawiono sposób konfigurowania konta usługi Azure Media Services do przesyłania strumieniowego PlayReady dla systemu Windows 10 w trybie offline.
services: media-services
keywords: Android trybu Offline, ExoPlayer, Widevine DRM, myślnik
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
ms.openlocfilehash: ae5fdd51d9bc1a3e7e2521c6ca1ff64d884c96f8
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/24/2019
ms.locfileid: "67341778"
---
# <a name="offline-playready-streaming-for-windows-10"></a>PlayReady w trybie offline, przesyłania strumieniowego dla systemu Windows 10

Usługa Azure Media Services obsługuje pobieranie/odtwarzania w trybie offline dzięki ochronie DRM. W tym artykule omówiono obsługę trybu offline usługi Azure Media Services dla systemu Windows 10/PlayReady klientów. Temat pomocy technicznej w trybie offline dla systemu iOS/FairPlay i urządzeń Android/Widevine w następujących artykułach:

- [Przesyłanie strumieniowe w trybie offline przy użyciu technologii FairPlay na potrzeby systemu iOS](offline-fairplay-for-ios.md)
- [Widevine w trybie offline, przesyłania strumieniowego dla systemu Android](offline-widevine-for-android.md)

> [!NOTE]
> DRM w trybie offline jest rozliczane tylko składania pojedyncze żądanie licencji, podczas pobierania zawartości. Wszelkie błędy nie są już naliczane.

## <a name="overview"></a>Przegląd

W tej sekcji zawiera podstawowe informacje dotyczące odtwarzania w trybie offline, szczególnie Dlaczego:

* W niektórych krajach/regionach jest nadal ograniczona dostępność internetowych i/lub przepustowości. Użytkownicy mogą zdecydować się na pobieranie najpierw, aby móc obejrzeć zawartość w tyle wysokiej rozdzielczości dla środowiska oglądania zadowalające. W tym przypadku częściej, problem nie jest dostępność sieci, lecz jest ograniczona przepustowość sieci. Dostawców OTT/OVP prośbę o pomocy technicznej w trybie offline.
* Ujawnione podczas konferencji akcjonariuszy Netflix 2016 K3, pobierania zawartości jest "strony Żądana funkcja" i "się otwarty do niego" powiedział przez Reed Hastings, Dyrektor Generalny firmy Netflix.
* Niektórzy dostawcy zawartości może nie zezwalaj na dostarczanie licencji DRM poza krawędź kraj/region. Jeśli użytkownik musi przyjechać zagranicy i nadal chce obejrzeć zawartość, jest konieczne pobierania w trybie offline.
 
Wyzwaniem, któremu możemy twarzy we wdrażaniu w trybie offline jest następująca:

* MP4 jest obsługiwany przez wiele odtwarzaczy, narzędzia kodera, ale istnieje żadne wiązanie między kontenerów w formacie MP4 i technologii DRM;
* W dłuższej perspektywie CFF z CENC jest Zdajemy sobie. Obecnie ekosystem obsługę narzędzia/player nie jest jednak istnieje jeszcze. To rozwiązanie, musimy już dziś.
 
Chodzi o to: smooth streaming ([PIFF](https://go.microsoft.com/?linkid=9682897)) format pliku przy użyciu H264/AAC ma powiązanie o technologii PlayReady (AES-128 kont.). Poszczególne smooth przesyłania strumieniowego ismv plik (przy założeniu, że audio jest multipleksowego w filmie wideo) jest fMP4 i może służyć do odtwarzania. Bezproblemowe zawartości transmisji strumieniowej odbywa się przez szyfrowanie PlayReady, każdy plik ismv staje się PlayReady, chronione pofragmentowany plik MP4. Możemy wybrać plik ismv z preferowanych szybkości transmisji bitów i zmień jego nazwę jako MP4 do pobrania.

Dla hostingu PlayReady chronione pobierania progresywnego plików MP4, dostępne są dwie opcje:

* Jeden jest umieszczenie tego MP4 w ten sam zasób usługi kontenera/media i korzystać z usługi Azure Media Services, punkt końcowy do pobierania progresywnego; przesyłania strumieniowego
* Jeden lokalizatora SAS następuje służy do pobierania progresywnego bezpośrednio z usługi Azure Storage, z pominięciem usługi Azure Media Services.
 
Można używać dwóch typów dostarczaniem licencji PlayReady:

* Usługi dostarczania licencji PlayReady w usłudze Azure Media Services.
* Serwery licencji PlayReady hostowanych w dowolnym miejscu.

Poniżej przedstawiono dwa zestawy zasobów testowych, pierwszy z nich przy użyciu dostarczaniem licencji PlayReady w AMS podczas drugiej za pomocą Mój serwer licencji PlayReady hostowanych na Maszynie wirtualnej platformy Azure:

Zasób #1:

* Adres URL pobierania progresywnego: [https://willzhanmswest.streaming.mediaservices.windows.net/8d078cf8-d621-406c-84ca-88e6b9454acc/20150807-bridges-2500_H264_1644kbps_AAC_und_ch2_256kbps.mp4](https://willzhanmswest.streaming.mediaservices.windows.net/8d078cf8-d621-406c-84ca-88e6b9454acc/20150807-bridges-2500_H264_1644kbps_AAC_und_ch2_256kbps.mp4")
* PlayReady LA_URL (AMS): [https://willzhanmswest.keydelivery.mediaservices.windows.net/PlayReady/](https://willzhanmswest.keydelivery.mediaservices.windows.net/PlayReady/)

Zasób #2:

* Adres URL pobierania progresywnego: [https://willzhanmswest.streaming.mediaservices.windows.net/7c085a59-ae9a-411e-842c-ef10f96c3f89/20150807-bridges-2500_H264_1644kbps_AAC_und_ch2_256kbps.mp4](https://willzhanmswest.streaming.mediaservices.windows.net/7c085a59-ae9a-411e-842c-ef10f96c3f89/20150807-bridges-2500_H264_1644kbps_AAC_und_ch2_256kbps.mp4)
* PlayReady LA_URL (lokalnie): [https://willzhan12.cloudapp.net/playready/rightsmanager.asmx](https://willzhan12.cloudapp.net/playready/rightsmanager.asmx)

W przypadku testowania odtwarzania użyliśmy uniwersalnej aplikacji Windows w systemie Windows 10. W [przykłady aplikacji uniwersalnych systemu Windows 10](https://github.com/Microsoft/Windows-universal-samples), znajduje się przykład podstawowego odtwarzacza o nazwie [adaptacyjnego przesyłania strumieniowego przykładowe](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/AdaptiveStreaming). Wszystko, czego musimy to zrobić, jest dodanie kodu w firmie Microsoft w celu pobrania pobrany wideo i używać go jako źródła, zamiast adaptacyjnego przesyłania strumieniowego źródła. Zmiany są przycisku program obsługi zdarzeń kliknięcia:

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

![Odtwarzanie PlayReady w trybie offline chronione fMP4](./media/offline-playready-for-windows/offline-playready1.jpg)


Ponieważ wideo jest objęte ochroną PlayReady, zrzut ekranu nie będzie uwzględnienie filmu wideo.

Podsumowując firma Microsoft otrzymano trybu offline w usłudze Azure Media Services:

* Transkodowanie zawartości i szyfrowanie PlayReady, możesz to zrobić w usłudze Azure Media Services lub innych narzędzi;
* Zawartość może być hostowana w usłudze Azure Media Services lub usługi Azure Storage do pobierania progresywnego;
* Może być dostarczaniem licencji PlayReady, usługi Azure Media Services lub w innym miejscu;
* Przygotowanej zawartości transmisji strumieniowej smooth nadal można używać do przesyłania strumieniowego online za pośrednictwem DASH lub smooth za pomocą usług PlayReady jako DRM.

## <a name="next-steps"></a>Kolejne kroki

[Projektowanie systemu ochrony zawartości przy użyciu technologii multi-DRM z kontrolą dostępu](design-multi-drm-system-with-access-control.md)
