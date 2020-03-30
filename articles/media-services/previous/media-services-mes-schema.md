---
title: Schemat Standardowy kodera multimediów | Dokumenty firmy Microsoft
description: W tym artykule opisano niektóre elementy i typy schematu XML, na których są oparte ustawienia predefiniowane media encoder standard.
author: Juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: 622f14beabb1f2f109dff5d28c1591ffdd5aa000
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74901450"
---
# <a name="media-encoder-standard-schema"></a>Schemat usługi Media Encoder Standard
W tym artykule opisano niektóre elementy i typy schematu XML, na których są oparte [ustawienia predefiniowane media encoder standard.](media-services-mes-presets-overview.md) Artykuł zawiera wyjaśnienie elementów i ich prawidłowych wartości.  

## <a name="preset-root-element"></a><a name="Preset"></a>Predefiniowane (element główny)
Definiuje predefiniowane ustawienie kodowania.  

### <a name="elements"></a>Elementy

| Nazwa | Typ | Opis |
| --- | --- | --- |
| **Kodowanie** |[Kodowanie](media-services-mes-schema.md#Encoding) |Element główny, wskazuje, że źródła wejściowe mają być zakodowane. |
| **Dane wyjściowe** |[Dane wyjściowe](media-services-mes-schema.md#Output) |Kolekcja żądanych plików wyjściowych. |
| **Tryb rozciągnięcia**<br/>minOccurs="0"<br/>default="AutoSize|xs:ciąg znaków|Steruj wyjściowym rozmiarem klatki wideo, dopełnieniem, pikselem lub współczynnikiem proporcji ekranu. **StretchMode** może być jedną z następujących wartości: **Brak**, **AutoSize** (domyślnie) lub **AutoDopasowanie**.<br/><br/>**Brak:** Ściśle przestrzegać rozdzielczości wyjściowej (na przykład **szerokość** i **wysokość** w ustawieniach predefiniowanych) bez uwzględnienia proporcji pikseli lub współczynnika proporcji obrazu wejściowego wideo. Zalecane w scenariuszach, takich jak [przycinanie](media-services-crop-video.md), gdzie wyjściowy obraz wideo ma inny współczynnik proporcji w porównaniu do danych wejściowych. <br/><br/>**Autosize:** Rozdzielczość wyjściowa zmieści się wewnątrz okna (Szerokość * Wysokość) określona przez predefiniowane. Jednak koder tworzy wideo wyjściowe o kwadratowym (1:1) współczynniku proporcji pikseli. W związku z tym wyjście Szerokość lub wyjście Wysokość może zostać zastąpiona w celu dopasowania współczynnika proporcji wyświetlania danych wejściowych, bez dopełnienia. Na przykład jeśli dane wejściowe jest 1920x1080 i predefiniowane kodowanie prosi o 1280x1280, a następnie Height wartość w predefiniowane jest zastępowane, a dane wyjściowe będą na 1280x720, który utrzymuje współczynnik proporcji wejściowych 16:9. <br/><br/>**AutoFit:** W razie potrzeby, pad wideo wyjściowe (z letterbox lub pillarbox), aby uhonorować żądaną rozdzielczość wyjściową, zapewniając jednocześnie, że aktywny obszar wideo na wyjściu ma taki sam współczynnik proporcji jak wejście. Załóżmy na przykład, że dane wejściowe to 1920x1080, a ustawienie kodujące wymaga 1280x1280. Następnie wyjście wideo będzie na 1280x1280, ale będzie zawierać wewnętrzny prostokąt 1280x720 "aktywnego wideo" o współczynniku proporcji 16:9 i regionach skrzynki na listy 280 pikseli wysoko u góry i na dole. Na inny przykład, jeśli dane wejściowe jest 1440x1080 i preset kodowania prosi o 1280x720, a następnie wyjście będzie na 1280x720, który zawiera wewnętrzny prostokąt 960x720 w proporcjach 4:3 i obszarów pola filaru 160 pikseli szerokości po lewej i prawej stronie. 

### <a name="attributes"></a>Atrybuty

| Nazwa | Typ | Opis |
| --- | --- | --- |
| **Wersja**<br/><br/> Wymagany |**xs: dziesiętne** |Wersja predefiniowana. Obowiązują następujące ograniczenia: xs:fractionDigits value="1" i xs:minInclusive value="1" Na przykład **version="1.0"**. |

## <a name="encoding"></a><a name="Encoding"></a>Kodowania
Zawiera sekwencję następujących elementów:  

### <a name="elements"></a>Elementy

| Nazwa | Typ | Opis |
| --- | --- | --- |
| **H264Film** |[H264Film](media-services-mes-schema.md#H264Video) |Ustawienia kodowania wideo H.264. |
| **AACAudio ( AACAudio )** |[AACAudio ( AACAudio )](media-services-mes-schema.md#AACAudio) |Ustawienia kodowania audio AAC. |
| **BmpImage ( BmpImage )** |[BmpImage ( BmpImage )](media-services-mes-schema.md#BmpImage) |Ustawienia obrazu Bmp. |
| **PngImage (Obraz)** |[PngImage (Obraz)](media-services-mes-schema.md#PngImage) |Ustawienia obrazu Png. |
| **JpgImage (Obraz)** |[JpgImage (Obraz)](media-services-mes-schema.md#JpgImage) |Ustawienia obrazu jpg. |

## <a name="h264video"></a><a name="H264Video"></a>H264Film
### <a name="elements"></a>Elementy

| Nazwa | Typ | Opis |
| --- | --- | --- |
| **Dwapasek**<br/><br/> minOccurs="0" |**xs:wartość logiczna** |Obecnie obsługiwane jest tylko kodowanie jednoprzebiegowe. |
| **Element ceframe klucza**<br/><br/> minOccurs="0"<br/><br/> **default="00:00:02"** |**xs:czas** |Określa stałe odstępy między ramkami IDR w jednostkach sekund. Określany również jako czas trwania GOP. Zobacz **SceneChangeDetection** do kontrolowania, czy koder może odbiegać od tej wartości. |
| **SceneChangeDetection (Zmiana sceny)**<br/><br/> minOccurs="0"<br/><br/> default="false" |**xs: logiczny** |Jeśli ustawiona wartość true, koder próbuje wykryć zmianę sceny w filmie i wstawia ramkę IDR. |
| **Złożoności**<br/><br/> minOccurs="0"<br/><br/> default="Zrównoważony" |**xs:ciąg znaków** |Steruje kompromisem między szybkością kodowania a jakością wideo. Może to być jedna z następujących wartości: **Prędkość,** **Zrównoważony**lub **Jakość**<br/><br/> Domyślnie: **Zrównoważony** |
| **Tryb synchronizacji**<br/><br/> minOccurs="0" | |Funkcja zostanie udostępniona w przyszłej wersji. |
| **H264Warstwy**<br/><br/> minOccurs="0" |[H264Warstwy](media-services-mes-schema.md#H264Layers) |Kolekcja wyjściowych warstw wideo. |

### <a name="attributes"></a>Atrybuty

| Nazwa | Typ | Opis |
| --- | --- | --- |
| **Warunek** |**xs:ciąg znaków** | Jeśli wejście nie ma wideo, można wymusić na koderze wstawianie monochromatycznej ścieżki wideo. Aby to zrobić, należy użyć Condition="InsertBlackIfNoVideoBottomLayerOnly" (aby wstawić wideo tylko przy najniższej szybkości transmisji bitów) lub Condition="InsertBlackIfNoVideo" (aby wstawić wideo przy wszystkich szybkościach transmisji bitów wyjściowych). Więcej informacji znajduje się w [tym](media-services-advanced-encoding-with-mes.md#no_video) artykule.|

## <a name="h264layers"></a><a name="H264Layers"></a>H264Warstwy

Domyślnie jeśli wysyłasz dane wejściowe do kodera, który zawiera tylko dźwięk, a nie wideo, zasób wyjściowy zawiera pliki tylko z danymi audio. Niektórzy gracze mogą nie być w stanie obsłużyć takich strumieni wyjściowych. Można użyć ustawienia atrybutu **Wstawnik** H264Video, aby wymusić na koderze dodanie ścieżki wideo do danych wyjściowych w tym scenariuszu. Więcej informacji znajduje się w [tym](media-services-advanced-encoding-with-mes.md#no_video) artykule.
              
### <a name="elements"></a>Elementy

| Nazwa | Typ | Opis |
| --- | --- | --- |
| **H264Warstwa**<br/><br/> minOccurs="0" maxOccurs="bez ograniczeń" |[H264Warstwa](media-services-mes-schema.md#H264Layer) |Kolekcja warstw H264. |

## <a name="h264layer"></a><a name="H264Layer"></a>H264Warstwa
> [!NOTE]
> Limity wideo są oparte na wartościach opisanych w tabeli [Poziomy H264.](https://en.wikipedia.org/wiki/H.264/MPEG-4_AVC#Levels)  
> 
> 

### <a name="elements"></a>Elementy

| Nazwa | Typ | Opis |
| --- | --- | --- |
| **Profil**<br/><br/> minOccurs="0"<br/><br/> default="Auto" |**xs: ciąg znaków** |Może być jednym z następujących **xs:** wartości ciągu: **Auto**, **Linia bazowa**, **Główny**, **Wysoki**. |
| **Poziom**<br/><br/> minOccurs="0"<br/><br/> default="Auto" |**xs: ciąg znaków** | |
| **Bitrate**<br/><br/> minOccurs="0" |**xs:int** |Szybkość transmisji bitów używana dla tej warstwy wideo, określona w kbps. |
| **Maksymalna liczba bitów**<br/><br/> minOccurs="0" |**xs: int** |Maksymalna szybkość transmisji bitów używana dla tej warstwy wideo, określona w kb/s. |
| **BuforWindow**<br/><br/> minOccurs="0"<br/><br/> default="00:00:05" |**xs: czas** |Długość buforu wideo. |
| **impulsów**<br/><br/> minOccurs="0" |**xs: int** |Szerokość wyjściowej klatki wideo w pikselach.<br/><br/> Obecnie należy określić zarówno szerokość, jak i wysokość. Szerokość i wysokość muszą być liczbami parzyste. |
| **Wysokość**<br/><br/> minOccurs="0" |**xs:int** |Wysokość wyjściowej klatki wideo w pikselach.<br/><br/> Obecnie należy określić zarówno szerokość, jak i wysokość. Szerokość i wysokość muszą być liczbami parzyste.|
| **Ramki B**<br/><br/> minOccurs="0" |**xs: int** |Liczba klatek B między ramkami odniesienia. |
| **Klatki odniesienia**<br/><br/> minOccurs="0"<br/><br/> default="3" |**xs:int** |Liczba ramek referencyjnych w gop. |
| **Tryb EntropyMode**<br/><br/> minOccurs="0"<br/><br/> default="Cabac" |**xs: ciąg znaków** |Może to być jedna z następujących wartości: **Cabac** i **Cavlc**. |
| **Ilość klatek**<br/><br/> minOccurs="0" |liczba racjonalna |Określa liczbę klatek na sekundę wyjściowego wideo. Użyj domyślnej wartości "0/1", aby koder używał tej samej liczby klatek na sekundę, co wejściowy film wideo. Oczekuje się, że dozwolone wartości będą częstymi liczbami klatek na wideo. Jednak wszelkie ważne racjonalne jest dozwolone. Na przykład 1/1 będzie 1 fps i jest prawidłowy.<br/><br/> - 12/1 (12 kl./s)<br/><br/> - 15/1 (15 kl./s)<br/><br/> - 24/1 (24 kl./s)<br/><br/> - 24000/1001 (23.976 kl./s)<br/><br/> - 25/1 (25 kl./s)<br/><br/>  - 30/1 (30 kl./s)<br/><br/> - 30000/1001 (29,97 kl./s) <br/> <br/>**UWAGA** Jeśli tworzysz niestandardowe ustawienie predefiniowane dla kodowania z wieloma bitami, wszystkie warstwy predefiniowanych **ustawień muszą** używać tej samej wartości FrameRate.|
| **AdaptiveBFrame**<br/><br/> minOccurs="0" |**xs: logiczny** |Kopiowanie z kodera multimediów platformy Azure |
| **Wycinki**<br/><br/> minOccurs="0"<br/><br/> default="0" |**xs:int** |Określa, na ile plasterków jest podzielona ramka. Zaleca się używanie ustawień domyślnych. |

## <a name="aacaudio"></a><a name="AACAudio"></a>AACAudio ( AACAudio )
 Zawiera sekwencję następujących elementów i grup.  

 Aby uzyskać więcej informacji na temat aac, zobacz [AAC](https://en.wikipedia.org/wiki/Advanced_Audio_Coding).  

### <a name="elements"></a>Elementy

| Nazwa | Typ | Opis |
| --- | --- | --- |
| **Profil**<br/><br/> minOccurs="0 "<br/><br/> default="AACLC" |**xs: ciąg znaków** |Może to być jedna z następujących wartości: **AACLC**, **HEAACV1**lub **HEAACV2**. |

### <a name="attributes"></a>Atrybuty

| Nazwa | Typ | Opis |
| --- | --- | --- |
| **Warunek** |**xs: ciąg znaków** |Aby wymusić koder do wytworzenia zasobu, który zawiera cichą ścieżkę audio, gdy wejście nie ma dźwięku, należy określić wartość "InsertSilenceIfNoAudio".<br/><br/> Domyślnie jeśli wysyłasz dane wejściowe do kodera, który zawiera tylko wideo i nie audio, a następnie zasób wyjściowy zawiera pliki, które zawierają tylko dane wideo. Niektórzy gracze mogą nie być w stanie obsłużyć takich strumieni wyjściowych. To ustawienie służy do wymuszania kodera, aby dodać cichą ścieżkę audio do danych wyjściowych w tym scenariuszu. |

### <a name="groups"></a>Grupy

| Tematy pomocy | Opis |
| --- | --- |
| [Grupa audio](media-services-mes-schema.md#AudioGroup)<br/><br/> minOccurs="0" |Zobacz opis [AudioGroup,](media-services-mes-schema.md#AudioGroup) aby poznać odpowiednią liczbę kanałów, częstotliwość próbkowania i szybkość transmisji bitów, które można ustawić dla każdego profilu. |

## <a name="audiogroup"></a><a name="AudioGroup"></a>Grupa audio
Aby uzyskać szczegółowe informacje o tym, jakie wartości są prawidłowe dla każdego profilu, zobacz tabelę "Szczegóły kodeka audio", która znajduje się poniżej.  

### <a name="elements"></a>Elementy

| Nazwa | Typ | Opis |
| --- | --- | --- |
| **Kanały**<br/><br/> minOccurs="0" |**xs: int** |Liczba zakodowanych kanałów audio. Ważne są następujące opcje: 1, 2, 5, 6, 8.<br/><br/> Domyślnie: 2. |
| **SamplingRate (Szybkość próbkowania)**<br/><br/> minOccurs="0" |**xs: int** |Częstotliwość próbkowania dźwięku, określona w Hz. |
| **Bitrate**<br/><br/> minOccurs="0" |**xs: int** |Szybkość transmisji bitów używana podczas kodowania dźwięku, określona w kbps. |

### <a name="audio-codec-details"></a>Szczegóły kodeka audio

Kodek audio|Szczegóły  
-----------------|---  
**AACLC ( AACLC )** |1.<br/><br/> - 11025: &lt;8 &lt; = szybkość transmisji bitów 16<br/><br/> - 12000: &lt;8 &lt; = szybkość transmisji bitów 16<br/><br/> - 16000: &lt;8 &lt;= bitrate 32<br/><br/>- 22050: 24 &lt; &lt; = bitrate 32<br/><br/> - 24000: 24 &lt; &lt; = bitrate 32<br/><br/> - 32000: 32 &lt; &lt;= szybkość transmisji bitów = 192<br/><br/> - 44100: 56 &lt; &lt;= szybkość transmisji bitów = 288<br/><br/> - 48000: 56 &lt; &lt;= szybkość transmisji bitów = 288<br/><br/> - 88200 : &lt;128 &lt;= bitrate = 288<br/><br/> - 96000 : &lt;128 &lt;= bitrate = 288<br/><br/> 2.<br/><br/> - 11025: 16 &lt; &lt; = szybkość transmisji bitów 24<br/><br/> - 12000: 16 &lt; &lt; = szybkość transmisji bitów 24<br/><br/> - 16000: 16 &lt; &lt; = szybkość transmisji bitów 40<br/><br/> - 22050: 32 &lt; &lt; = szybkość transmisji bitów 40<br/><br/> - 24000 : &lt;32 &lt; = bitrate 40<br/><br/> - 32000: 40 &lt; &lt;= szybkość transmisji bitów = 384<br/><br/> - 44100: 96 &lt; &lt;= szybkość transmisji bitów = 576<br/><br/> - 48000 : &lt;96 &lt;= szybkość transmisji bitów = 576<br/><br/> - 88200: 256 &lt; &lt;= szybkość transmisji bitów = 576<br/><br/> - 96000: 256 &lt; &lt;= szybkość transmisji bitów = 576<br/><br/> 5/6:<br/><br/> - 32000: 160 &lt; &lt;= szybkość transmisji bitów = 896<br/><br/> - 44100: 240 &lt; &lt;= bitrate = 1024<br/><br/> - 48000: 240 &lt; &lt;= bitrate = 1024<br/><br/> - 88200: 640 &lt; &lt;= bitrate = 1024<br/><br/> - 96000: 640 &lt; &lt;= bitrate = 1024<br/><br/> 8.<br/><br/> - 32000 : &lt;224 &lt;= bitrate = 1024<br/><br/> - 44100 : &lt;384 &lt;= bitrate = 1024<br/><br/> - 48000: 384 &lt; &lt;= bitrate = 1024<br/><br/> - 88200: 896 &lt; &lt;= szybkość transmisji bitów = 1024<br/><br/> - 96000: 896 &lt; &lt;= bitrate = 1024  
**HEAACV1 ( HEAACV1 )** |1.<br/><br/> - 22050: szybkość transmisji bitów = 8<br/><br/> - 24000: &lt;8 &lt;= bitrate = 10<br/><br/> - 32000: 12 &lt; &lt;= szybkość transmisji bitów = 64<br/><br/> - 44100: 20 &lt; &lt;= szybkość transmisji bitów = 64<br/><br/> - 48000: 20 &lt; &lt;= szybkość transmisji bitów = 64<br/><br/> - 88200: szybkość transmisji bitów = 64<br/><br/> 2.<br/><br/> - 32000: 16 &lt; &lt;= szybkość transmisji bitów = 128<br/><br/> - 44100: 16 &lt; &lt;= szybkość transmisji bitów = 128<br/><br/> - 48000: 16 &lt; &lt;= szybkość transmisji bitów = 128<br/><br/> - 88200 : &lt;96 &lt;= bitrate = 128<br/><br/> - 96000: 96 &lt; &lt;= szybkość transmisji bitów = 128<br/><br/> 5/6:<br/><br/> - 32000 : &lt;64 &lt;= szybkość transmisji bitów = 320<br/><br/> - 44100: 64 &lt; &lt;= bitrate = 320<br/><br/> - 48000: 64 &lt; &lt;= bitrate = 320<br/><br/> - 88200 : &lt;256 &lt;= bitrate = 320<br/><br/> - 96000: 256 &lt; &lt;= bitrate = 320<br/><br/> 8.<br/><br/> - 32000: 96 &lt; &lt;= szybkość transmisji bitów = 448<br/><br/> - 44100: 96 &lt; &lt;= bitrate = 448<br/><br/> - 48000: 96 &lt; &lt;= szybkość transmisji bitów = 448<br/><br/> - 88200: 384 &lt; &lt;= bitrate = 448<br/><br/> - 96000: 384 &lt; &lt;= bitrate = 448  
**HEAACV2 (HEAACV2)** |2.<br/><br/> - 22050: &lt;8 &lt;= szybkość transmisji bitów = 10<br/><br/> - 24000: &lt;8 &lt;= bitrate = 10<br/><br/> - 32000: 12 &lt; &lt;= szybkość transmisji bitów = 64<br/><br/> - 44100: 20 &lt; &lt;= szybkość transmisji bitów = 64<br/><br/> - 48000: 20 &lt; &lt;= szybkość transmisji bitów = 64<br/><br/> - 88200: 64 &lt; &lt;= szybkość transmisji bitów = 64  
  
## <a name="clip"></a><a name="Clip"></a>Klip
### <a name="attributes"></a>Atrybuty

| Nazwa | Typ | Opis |
| --- | --- | --- |
| **Starttime** |**xs:czas trwania** |Określa godzinę rozpoczęcia prezentacji. Wartość StartTime musi odpowiadać bezwzględnym sygnaturom czasowym wejściowego wideo. Na przykład jeśli pierwsza klatka wejściowego wideo ma sygnaturę czasową 12:00:10.000, wówczas czas starttime powinien mieć co najmniej 12:00:10.000 lub więcej. |
| **Czas trwania** |**xs:czas trwania** |Określa czas trwania prezentacji (na przykład wygląd nakładki w filmie). |

## <a name="output"></a><a name="Output"></a>Wyjście
### <a name="attributes"></a>Atrybuty

| Nazwa | Typ | Opis |
| --- | --- | --- |
| **Pod nazwą** |**xs:ciąg znaków** |Nazwa pliku wyjściowego.<br/><br/> Do tworzenia nazw plików wyjściowych można użyć makr opisanych w poniższej tabeli. Przykład:<br/><br/> **"Wyjścia": [ { "Nazwa pliku": "{Basename}*{Rozdzielczość}*{Bitrate}.mp4", "Format": { "Type": "MP4Format" } } ]** |

### <a name="macros"></a>Makra

| Makro | Opis |
| --- | --- |
| **{Basename}** |Jeśli kodujesz VoD, {Basename} jest pierwszym 32 znakami właściwości AssetFile.Name pliku podstawowego w zasobie wejściowym.<br/><br/> Jeśli zasób wejściowy jest archiwum na żywo, {Basename} jest pochodną atrybutów trackName w manifeście serwera. Jeśli przesyłasz zadanie podpoślizgu przy użyciu TopBitrate, jak w:\>"<VideoStream TopBitrate\></VideoStream ", a plik wyjściowy zawiera wideo, to {Basename} jest pierwszym 32 znaków trackName warstwy wideo o najwyższej szybkości transmisji bitów.<br/><br/> Jeśli zamiast tego przesyłasz zadanie podpoślizgowe przy użyciu wszystkich wejściowych szybkości transmisji bitów, takich jak "<VideoStream\>*</VideoStream\>", a plik wyjściowy zawiera wideo, {Basename} jest pierwszym 32 znakami trackName odpowiedniej warstwy wideo. |
| **{Kodek}** |Mapy do "H264" dla wideo i "AAC" dla audio. |
| **{Szybkość transmisji bitów}** |Docelowa szybkość transmisji bitów wideo, jeśli plik wyjściowy zawiera wideo i audio, lub docelowa szybkość transmisji bitów audio, jeśli plik wyjściowy zawiera tylko dźwięk. Używana wartość to szybkość transmisji bitów w kb/s. |
| **{Kanał}** |Liczba kanałów audio, jeśli plik zawiera dźwięk. |
| **{Szerokość}** |Szerokość wideo, w pikselach, w pliku wyjściowym, jeśli plik zawiera wideo. |
| **{Wysokość}** |Wysokość wideo, w pikselach, w pliku wyjściowym, jeśli plik zawiera wideo. |
| **{Rozszerzenie}** |Dziedziczy z właściwości "Typ" dla pliku wyjściowego. Nazwa pliku wyjściowego ma rozszerzenie, które jest jednym z: "mp4", "ts", "jpg", "png" lub "bmp". |
| **{Indeks}** |Obowiązkowe dla miniatury. Powinien być obecny tylko raz. |

## <a name="video-complex-type-inherits-from-codec"></a><a name="Video"></a>Wideo (typ złożony dziedziczy z kodeka)
### <a name="attributes"></a>Atrybuty

| Nazwa | Typ | Opis |
| --- | --- | --- |
| **Początek** |**xs:ciąg znaków** | |
| **Krok** |**xs:ciąg znaków** | |
| **Zakres** |**xs:ciąg znaków** | |
| **PreserveResolutionAfterRotation** |**xs:wartość logiczna** |Aby uzyskać szczegółowe informacje, zobacz następującą sekcję: [PreserveResolutionAfterRotation](media-services-mes-schema.md#PreserveResolutionAfterRotation) |

### <a name="preserveresolutionafterrotation"></a><a name="PreserveResolutionAfterRotation"></a>PreserveResolutionAfterRotation
Zaleca się użycie flagi **PreserveResolutionAfterRotation** w połączeniu z wartościami rozdzielczości wyrażonymi w procentach (Width="100%" , Height="100%").  

Domyślnie ustawienia rozdzielczości kodowania (Szerokość, Wysokość) w ustawieniach predefiniowanych media encoder standard (MES) są przeznaczone dla filmów z obrotem 0 stopni. Na przykład jeśli wejściowy film wideo jest 1280x720 z obrotem zerowego stopnia, domyślne ustawienia predefiniowane upewnij się, że dane wyjściowe mają taką samą rozdzielczość.  

![MesRoation1](./media/media-services-shemas/media-services-mes-roation1.png) 

Jeśli wejściowy film wideo został przechwycony z obrotem niezerowym (na przykład smartfon lub tablet trzymany w pionie), mes domyślnie stosuje ustawienia rozdzielczości kodowania (Szerokość, Wysokość) do wejściowego wideo, a następnie kompensuje obrót. Na przykład zobacz poniższy obraz. Predefiniowane użycie Width = "100%", Height = "100%", które MES interpretuje jako wymagające danych wyjściowych o szerokości 1280 pikseli i wysokości 720 pikseli. Po obróceniu wideo, a następnie zmniejsza obraz, aby zmieścić się w tym oknie, prowadząc do obszarów filar-box po lewej i prawej stronie.  

![MesRoation2 (właso)](./media/media-services-shemas/media-services-mes-roation2.png) 

Alternatywnie można skorzystać z **PreserveResolutionAfterRotation** flagi i ustawić go na "true" (domyślnie jest "false"). Więc jeśli preset ma Szerokość = "100%", Wysokość = "100%" i PreserveResolutionAfterRotation ustawiona na "true", wejście wideo, które jest 1280 pikseli szerokości i 720 pikseli wysokości z 90-stopniowym obrotem tworzy wyjście z obrotem zerowym stopniem, ale 720 pikseli szerokości i 1280 pikseli. Zobacz poniższy rysunek:  

![MesRoation3 (WYROZW.](./media/media-services-shemas/media-services-mes-roation3.png) 

## <a name="formatgroup-group"></a><a name="FormatGroup"></a>Grupa formatu (grupa)
### <a name="elements"></a>Elementy

| Nazwa | Typ | Opis |
| --- | --- | --- |
| **BmpFormat** |**BmpFormat** | |
| **PngFormat** |**PngFormat** | |
| **JpgFormat (sformat jpg)** |**JpgFormat (sformat jpg)** | |

## <a name="bmplayer"></a><a name="BmpLayer"></a>Warstwa Bmp
### <a name="element"></a>Element

| Nazwa | Typ | Opis |
| --- | --- | --- |
| **impulsów**<br/><br/> minOccurs="0" |**xs:int** | |
| **Wysokość**<br/><br/> minOccurs="0" |**xs:int** | |

### <a name="attributes"></a>Atrybuty

| Nazwa | Typ | Opis |
| --- | --- | --- |
| **Warunek** |**xs:ciąg znaków** | |

## <a name="pnglayer"></a><a name="PngLayer"></a>Warstwa Png
### <a name="element"></a>Element

| Nazwa | Typ | Opis |
| --- | --- | --- |
| **impulsów**<br/><br/> minOccurs="0" |**xs:int** | |
| **Wysokość**<br/><br/> minOccurs="0" |**xs:int** | |

### <a name="attributes"></a>Atrybuty

| Nazwa | Typ | Opis |
| --- | --- | --- |
| **Warunek** |**xs:ciąg znaków** | |

## <a name="jpglayer"></a><a name="JpgLayer"></a>Warstwa jpg
### <a name="element"></a>Element

| Nazwa | Typ | Opis |
| --- | --- | --- |
| **impulsów**<br/><br/> minOccurs="0" |**xs:int** | |
| **Wysokość**<br/><br/> minOccurs="0" |**xs:int** | |
| **Jakość**<br/><br/> minOccurs="0" |**xs:int** |Prawidłowe wartości: 1(najgorszy)-100 (najlepiej) |

### <a name="attributes"></a>Atrybuty

| Nazwa | Typ | Opis |
| --- | --- | --- |
| **Warunek** |**xs:ciąg znaków** | |

## <a name="pnglayers"></a><a name="PngLayers"></a>PngWarstwy
### <a name="elements"></a>Elementy

| Nazwa | Typ | Opis |
| --- | --- | --- |
| **Warstwa Png**<br/><br/> minOccurs="0" maxOccurs="bez ograniczeń" |[Warstwa Png](media-services-mes-schema.md#PngLayer) | |

## <a name="bmplayers"></a><a name="BmpLayers"></a>Warstwy Bmp
### <a name="elements"></a>Elementy

| Nazwa | Typ | Opis |
| --- | --- | --- |
| **Warstwa Bmp**<br/><br/> minOccurs="0" maxOccurs="bez ograniczeń" |[Warstwa Bmp](media-services-mes-schema.md#BmpLayer) | |

## <a name="jpglayers"></a><a name="JpgLayers"></a>JpgLayers (Warstwa jpg)
### <a name="elements"></a>Elementy

| Nazwa | Typ | Opis |
| --- | --- | --- |
| **Warstwa jpg**<br/><br/> minOccurs="0" maxOccurs="bez ograniczeń" |[Warstwa jpg](media-services-mes-schema.md#JpgLayer) | |

## <a name="bmpimage-complex-type-inherits-from-video"></a><a name="BmpImage"></a>BmpImage (typ złożony dziedziczy z wideo)
### <a name="elements"></a>Elementy

| Nazwa | Typ | Opis |
| --- | --- | --- |
| **PngWarstwy**<br/><br/> minOccurs="0" |[PngWarstwy](media-services-mes-schema.md#PngLayers) |Warstwy Png |

## <a name="jpgimage-complex-type-inherits-from-video"></a><a name="JpgImage"></a>JpgImage (typ złożony dziedziczy z wideo)
### <a name="elements"></a>Elementy

| Nazwa | Typ | Opis |
| --- | --- | --- |
| **PngWarstwy**<br/><br/> minOccurs="0" |[PngWarstwy](media-services-mes-schema.md#PngLayers) |Warstwy Png |

## <a name="pngimage-complex-type-inherits-from-video"></a><a name="PngImage"></a>PngImage (typ złożony dziedziczy z wideo)
### <a name="elements"></a>Elementy

| Nazwa | Typ | Opis |
| --- | --- | --- |
| **PngWarstwy**<br/><br/> minOccurs="0" |[PngWarstwy](media-services-mes-schema.md#PngLayers) |Warstwy Png |

## <a name="examples"></a>Przykłady
Zobacz przykłady ustawień predefiniowanych XML, które są zbudowane na podstawie tego schematu, zobacz [Ustawienia predefiniowane zadań dla MES (Media Encoder Standard)](media-services-mes-presets-overview.md).

## <a name="next-steps"></a>Następne kroki
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

