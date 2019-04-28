---
title: Schemat usługi Media Encoder Standard | Dokumentacja firmy Microsoft
description: Artykuł zawiera omówienie usługi Media Encoder Standard schematu.
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
ms.openlocfilehash: 837235e04ce190a4481e1f19789d8e9ff9cb7578
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61131585"
---
# <a name="media-encoder-standard-schema"></a>Schemat usługi Media Encoder Standard
W tym artykule opisano niektóre elementy i typy schematu XML, na którym [ustawienia wstępne usługi Media Encoder Standard](media-services-mes-presets-overview.md) opierają się. Artykuł zawiera omówienie elementów i ich ważnych wartości.  

## <a name="Preset"></a> Ustawienie wstępne (element główny)
Definiuje ustawienie wstępne kodowania.  

### <a name="elements"></a>Elementy

| Name (Nazwa) | Typ | Opis |
| --- | --- | --- |
| **Kodowanie** |[Kodowanie](media-services-mes-schema.md#Encoding) |Element główny wskazuje, że źródeł danych wejściowych do zakodowania. |
| **Dane wyjściowe** |[Dane wyjściowe](media-services-mes-schema.md#Output) |Kolekcja plików żądaną produktu wyjściowego. |
| **StretchMode**<br/>minOccurs="0"<br/>default="AutoSize|xs:String|Kontrolować rozmiar wideo ramki danych wyjściowych, dopełnienie, pikseli lub wyświetlić współczynnik proporcji. **StretchMode** mogą mieć jedną z następujących wartości: **Brak**, **AutoSize** (ustawienie domyślne) lub **Autodopasowanie**.<br/><br/>**Brak**: Ścisłe rozdzielczość wyjściową (na przykład **szerokość** i **wysokość** predefiniowanymi) bez uwzględniania współczynnik proporcji pikseli lub współczynnik proporcji ekranu wejściowego filmu wideo. Zalecane w scenariuszach, takich jak [przycinanie](media-services-crop-video.md), gdzie wyjście wideo ma inny współczynnik proporcji, w porównaniu z danych wejściowych. <br/><br/>**AutoSize**: Rozdzielczość wyjściową zmieści się w oknie (szerokość * wysokość) określony przez ustawienie wstępne. Jednak kodera tworzy wideo danych wyjściowych, który ma współczynnik proporcji kwadratowy pikseli (1:1). W związku z tym, dane wyjściowe szerokość lub danych wyjściowych wysokość może zostać zastąpiona w celu dopasowania współczynnik proporcji ekranu danych wejściowych, bez uzupełnień. Na przykład jeśli dane wejściowe są 1920 x 1080 pikseli i ustawienia wstępne kodowania poprosi o podanie 1280 x 1280, następnie wartość wysokości predefiniowanymi zostanie zastąpiona, a dane wyjściowe będą miały 1280 x 720, który przechowuje wprowadzania proporcje 16:9. <br/><br/>**Autodopasowanie**: Jeśli to konieczne, konsoli danych wyjściowych filmu (letterbox lub pillarbox) przestrzegać rozwiązanie żądaną produktu wyjściowego przy jednoczesnym zapewnieniu, że aktywnym regionem wideo w danych wyjściowych ma takiego samego współczynnika proporcji jako dane wejściowe. Na przykład załóżmy, że dane wejściowe są 1920 x 1080 pikseli i ustawienia wstępne kodowania poprosi o podanie 1280 x 1280. Następnie wyjście wideo będą miały 1280 x 1280, ale będzie ona zawierać wewnętrzny 1280 x 720 prostokąt "active wideo" proporcje 16:9 i letterbox regionów 280 pikseli u góry i u dołu. Inny przykład jeśli dane wejściowe są 1440 x 1080 pikseli i ustawienia wstępne kodowania poprosi o podanie 1280 x 720 to dane wyjściowe będą na 1280 x 720, który zawiera wewnętrzny prostokąt 960 x 720 na współczynnik proporcji 4:3 i słupka pole regionów 160 pikseli w lewo i w prawo. 

### <a name="attributes"></a>Atrybuty

| Name (Nazwa) | Typ | Opis |
| --- | --- | --- |
| **Wersja**<br/><br/> Wymagane |**xs: dziesiętne** |Wersja wstępnie zdefiniowane. Obowiązują następujące ograniczenia: wartość xs:fractionDigits = wartość "1" i xs:minInclusive = "1", na przykład **wersji = "1.0"**. |

## <a name="Encoding"></a> Kodowanie
Zawiera sekwencję następujące elementy:  

### <a name="elements"></a>Elementy

| Name (Nazwa) | Typ | Opis |
| --- | --- | --- |
| **H264Video** |[H264Video](media-services-mes-schema.md#H264Video) |Ustawienia dla kodowania wideo H.264. |
| **AACAudio** |[AACAudio](media-services-mes-schema.md#AACAudio) |Ustawienia kodowania audio AAC. |
| **BmpImage** |[BmpImage](media-services-mes-schema.md#BmpImage) |Ustawienia dla obrazu w formacie Bmp. |
| **PngImage** |[PngImage](media-services-mes-schema.md#PngImage) |Ustawienia obrazu Png. |
| **JpgImage** |[JpgImage](media-services-mes-schema.md#JpgImage) |Ustawienia obrazu Jpg. |

## <a name="H264Video"></a> H264Video
### <a name="elements"></a>Elementy

| Name (Nazwa) | Typ | Opis |
| --- | --- | --- |
| **TwoPass**<br/><br/> minOccurs="0" |**xs:Boolean** |Aktualnie obsługiwana jest tylko jeden przebieg kodowania. |
| **KeyFrameInterval**<br/><br/> minOccurs="0"<br/><br/> **default="00:00:02"** |**xs: Time** |Określa stały odstęp między IDR ramki w jednostkach czasu w sekundach. Również określany jako czas trwania GOP. Zobacz **SceneChangeDetection** do kontrolowania, czy kodera można różni się od tej wartości. |
| **SceneChangeDetection**<br/><br/> minOccurs="0"<br/><br/> default=”false” |**xs: wartość logiczna** |Jeśli ustawiona na wartość true, koder próbuje wykryć zmianę sceny w filmie wideo i wstawia IDR ramkę. |
| **Złożoność**<br/><br/> minOccurs="0"<br/><br/> default="Balanced" |**xs:String** |Kontroluje kompromis między kodowanie szybkość i wideo w jakości. Może być jedną z następujących wartości: **Szybkość**, **równoważenia**, lub **jakości**<br/><br/> Domyślne: **Zrównoważone** |
| **SyncMode**<br/><br/> minOccurs="0" | |Funkcja zostaną ujawnione w przyszłej wersji. |
| **H264Layers**<br/><br/> minOccurs="0" |[H264Layers](media-services-mes-schema.md#H264Layers) |Kolekcja warstw wideo w danych wyjściowych. |

### <a name="attributes"></a>Atrybuty

| Name (Nazwa) | Typ | Opis |
| --- | --- | --- |
| **Warunek** |**xs:String** | Dane wejściowe nie ma karty wideo, możesz wymusić kodera do wstawienia monochromatycznych Ścieżka wideo. Aby to zrobić, należy użyć warunku = "InsertBlackIfNoVideoBottomLayerOnly" (tak, aby wstawić wideo na tylko najniższej szybkości transmisji bitów) lub warunek = "InsertBlackIfNoVideo" (Aby wstawić wideo dane wyjściowe różnych). Więcej informacji znajduje się w [tym](media-services-advanced-encoding-with-mes.md#no_video) artykule.|

## <a name="H264Layers"></a> H264Layers

Domyślnie Jeśli wyślesz dane wejściowe do kodera, który zawiera tylko audio i wideo nie elementu zawartości wyjściowej zawiera pliki z tylko dane audio. Niektóre odtwarzacze nie można obsłużyć takich strumieni danych wyjściowych. Możesz użyć H264Video **InsertBlackIfNoVideo** atrybutu ustawienie, aby wymusić kodera można dodać ścieżki wideo, danych wyjściowych w tym scenariuszu. Więcej informacji znajduje się w [tym](media-services-advanced-encoding-with-mes.md#no_video) artykule.
              
### <a name="elements"></a>Elementy

| Name (Nazwa) | Typ | Opis |
| --- | --- | --- |
| **H264Layer**<br/><br/> minOccurs="0" maxOccurs="unbounded" |[H264Layer](media-services-mes-schema.md#H264Layer) |Kolekcja warstw H264. |

## <a name="H264Layer"></a> H264Layer
> [!NOTE]
> Limity filmów wideo są oparte na wartości opisanych w [poziomy H264](https://en.wikipedia.org/wiki/H.264/MPEG-4_AVC#Levels) tabeli.  
> 
> 

### <a name="elements"></a>Elementy

| Name (Nazwa) | Typ | Opis |
| --- | --- | --- |
| **Profil**<br/><br/> minOccurs="0"<br/><br/> default=”Auto” |**xs: ciąg** |Może być jedną z następujących **xs: ciąg** wartości: **Automatyczne**, **linii bazowej**, **Main**, **wysokiej**. |
| **Poziom**<br/><br/> minOccurs="0"<br/><br/> default=”Auto” |**xs: ciąg** | |
| **Szybkość transmisji bitów**<br/><br/> minOccurs="0" |**xs:int** |Szybkość transmisji bitów używany dla tej warstwy wideo określonej w KB/s. |
| **MaxBitrate**<br/><br/> minOccurs="0" |**xs: int** |Maksymalna szybkość transmisji bitów używany dla tej warstwy wideo określonej w KB/s. |
| **BufferWindow**<br/><br/> minOccurs="0"<br/><br/> default="00:00:05" |**xs: czas** |Długość buforu wideo. |
| **Szerokość**<br/><br/> minOccurs="0" |**xs: int** |Szerokość danych wyjściowych klatki wideo, w pikselach.<br/><br/> Obecnie należy określić wysokość i szerokość. Szerokość i wysokość musi być liczby parzyste z zakresu. |
| **Wysokość**<br/><br/> minOccurs="0" |**xs:int** |Wysokość dane wyjściowe klatki wideo, w pikselach.<br/><br/> Obecnie należy określić wysokość i szerokość. Szerokość i wysokość musi być liczby parzyste z zakresu.|
| **BFrames**<br/><br/> minOccurs="0" |**xs: int** |Liczba ramek B pomiędzy klatkami odwołania. |
| **ReferenceFrames**<br/><br/> minOccurs="0"<br/><br/> domyślne = "3" |**xs:int** |Liczba ramek odwołania w GOP. |
| **EntropyMode**<br/><br/> minOccurs="0"<br/><br/> default=”Cabac” |**xs: ciąg** |Może być jedną z następujących wartości: **Cabac** i **Cavlc**. |
| **Szybkość klatek**<br/><br/> minOccurs="0" |Liczba wymierna |Określa szybkość odtwarzania wideo w danych wyjściowych. Użyj domyślnej wartości "0/1", aby poinformować kodera, użyj tego samego szybkość klatek jako wejściowego pliku wideo. Dozwolone wartości powinny być typowe szybkości odtwarzania wideo. Jednakże dowolne, prawidłowe wymierne jest dozwolone. Na przykład 1/1 będzie 1 kl. / s i jest prawidłowy.<br/><br/> -12/1 (12 kl. / s)<br/><br/> -15/1 (15 kl. / s)<br/><br/> -24/1 (24 kl. / s)<br/><br/> 24000/1001 (23.976 kl. / s)<br/><br/> -25/1 (25 kl. / s)<br/><br/>  -30/1 (30 kl. / s)<br/><br/> 30000/1001 (29,97 kl. / s) <br/> <br/>**Uwaga** tworząc niestandardowe ustawienie wstępne kodowania wieloma szybkościami transmisji bitów, następnie wszystkie warstwy ustawienia wstępnego **musi** używać tej samej wartości szybkości klatek.|
| **AdaptiveBFrame**<br/><br/> minOccurs="0" |**xs: wartość logiczna** |Kopiowanie z usługi Azure media encoder |
| **Wycinki**<br/><br/> minOccurs="0"<br/><br/> default="0" |**xs:int** |Określa, ile wycinki ramki jest podzielony na. Zaleca się korzystanie z domyślnego. |

## <a name="AACAudio"></a> AACAudio
 Zawiera sekwencję następujące elementy i grup.  

 Aby uzyskać więcej informacji na temat adaptacyjnych kontrolek aplikacji, zobacz [AAC](https://en.wikipedia.org/wiki/Advanced_Audio_Coding).  

### <a name="elements"></a>Elementy

| Name (Nazwa) | Typ | Opis |
| --- | --- | --- |
| **Profil**<br/><br/> minOccurs="0 "<br/><br/> default="AACLC" |**xs: ciąg** |Może być jedną z następujących wartości: **AACLC**, **HEAACV1**, lub **HEAACV2**. |

### <a name="attributes"></a>Atrybuty

| Name (Nazwa) | Typ | Opis |
| --- | --- | --- |
| **Warunek** |**xs: ciąg** |Aby wymusić kodera, aby utworzyć element zawartości zawierający dyskretnej ścieżki audio, gdy dane wejściowe zawierają bez dźwięku, określ wartość "InsertSilenceIfNoAudio".<br/><br/> Domyślnie Jeśli wyślesz dane wejściowe do kodera, który zawiera tylko, audio i wideo nie, następnie elementu zawartości wyjściowej zawiera pliki, które zawierają dane tylko wideo. Niektóre odtwarzacze nie można obsłużyć takich strumieni danych wyjściowych. To ustawienie umożliwia wymuszenie kodera można dodać dyskretnej ścieżki audio w danych wyjściowych w tym scenariuszu. |

### <a name="groups"></a>Grupy

| Informacje ogólne | Opis |
| --- | --- |
| [AudioGroup](media-services-mes-schema.md#AudioGroup)<br/><br/> minOccurs="0" |Zobacz opis [AudioGroup](media-services-mes-schema.md#AudioGroup) wiedzieć odpowiednią liczbę kanałów, częstotliwość próbkowania i szybkość transmisji bitów, które można ustawić dla każdego profilu. |

## <a name="AudioGroup"></a> AudioGroup
Aby uzyskać szczegółowe informacje o jakie wartości są prawidłowe dla każdego profilu zobacz tabelę "Szczegóły kodera-dekodera Audio", która jest zgodna.  

### <a name="elements"></a>Elementy

| Name (Nazwa) | Typ | Opis |
| --- | --- | --- |
| **Channels**<br/><br/> minOccurs="0" |**xs: int** |Liczba kanałów audio zakodowany. Poniżej przedstawiono prawidłowe opcje: 1, 2, 5, 6, 8.<br/><br/> Domyślne: 2. |
| **SamplingRate**<br/><br/> minOccurs="0" |**xs: int** |Częstotliwość próbkowania audio określone w Hz. |
| **Szybkość transmisji bitów**<br/><br/> minOccurs="0" |**xs: int** |Szybkość transmisji bitów związanych z kodowaniem audio, określonej w KB/s. |

### <a name="audio-codec-details"></a>Szczegóły kodera-dekodera audio

Audio Codec|Szczegóły  
-----------------|---  
**AACLC** |1.<br/><br/> - 11025: 8 &lt;= szybkości transmisji bitów &lt; 16<br/><br/> - 12000: 8 &lt;= szybkości transmisji bitów &lt; 16<br/><br/> - 16000: 8 &lt;= szybkości transmisji bitów &lt;32<br/><br/>- 22050: 24 &lt;= szybkości transmisji bitów &lt; 32<br/><br/> - 24000: 24 &lt;= szybkości transmisji bitów &lt; 32<br/><br/> - 32000: 32 &lt;= szybkości transmisji bitów &lt;= 192<br/><br/> - 44100: 56 &lt;= szybkości transmisji bitów &lt;= 288<br/><br/> - 48000: 56 &lt;= szybkości transmisji bitów &lt;= 288<br/><br/> - 88200 : 128 &lt;= szybkości transmisji bitów &lt;= 288<br/><br/> - 96000 : 128 &lt;= szybkości transmisji bitów &lt;= 288<br/><br/> 2.<br/><br/> - 11025: 16 &lt;= szybkości transmisji bitów &lt; 24<br/><br/> - 12000: 16 &lt;= szybkości transmisji bitów &lt; 24<br/><br/> - 16000: 16 &lt;= szybkości transmisji bitów &lt; 40<br/><br/> - 22050: 32 &lt;= szybkości transmisji bitów &lt; 40<br/><br/> - 24000 : 32 &lt;= szybkości transmisji bitów &lt; 40<br/><br/> - 32000:  40 &lt;= szybkości transmisji bitów &lt;= 384<br/><br/> - 44100: 96 &lt;= szybkości transmisji bitów &lt;= 576<br/><br/> - 48000 : 96 &lt;= szybkości transmisji bitów &lt;= 576<br/><br/> - 88200: 256 &lt;= szybkości transmisji bitów &lt;= 576<br/><br/> - 96000: 256 &lt;= szybkości transmisji bitów &lt;= 576<br/><br/> 5/6:<br/><br/> - 32000: 160 &lt;= szybkości transmisji bitów &lt;= 896<br/><br/> - 44100: 240 &lt;= szybkości transmisji bitów &lt;= 1024<br/><br/> - 48000: 240 &lt;= szybkości transmisji bitów &lt;= 1024<br/><br/> - 88200: 640 &lt;= szybkości transmisji bitów &lt;= 1024<br/><br/> - 96000: 640 &lt;= szybkości transmisji bitów &lt;= 1024<br/><br/> 8:<br/><br/> - 32000 : 224 &lt;= szybkości transmisji bitów &lt;= 1024<br/><br/> - 44100 : 384 &lt;= szybkości transmisji bitów &lt;= 1024<br/><br/> - 48000: 384 &lt;= szybkości transmisji bitów &lt;= 1024<br/><br/> - 88200: 896 &lt;= szybkości transmisji bitów &lt;= 1024<br/><br/> - 96000: 896 &lt;= szybkości transmisji bitów &lt;= 1024  
**HEAACV1** |1.<br/><br/> -22050 b: szybkość transmisji bitów = 8<br/><br/> - 24000: 8 &lt;= szybkości transmisji bitów &lt;= 10<br/><br/> - 32000: 12 &lt;= szybkości transmisji bitów &lt;= 64<br/><br/> - 44100: 20 &lt;= szybkości transmisji bitów &lt;= 64<br/><br/> - 48000: 20 &lt;= szybkości transmisji bitów &lt;= 64<br/><br/> -88200 b: szybkość transmisji bitów = 64<br/><br/> 2.<br/><br/> - 32000: 16 &lt;= szybkości transmisji bitów &lt;= 128<br/><br/> - 44100: 16 &lt;= szybkości transmisji bitów &lt;= 128<br/><br/> - 48000: 16 &lt;= szybkości transmisji bitów &lt;= 128<br/><br/> - 88200 : 96 &lt;= szybkości transmisji bitów &lt;= 128<br/><br/> - 96000: 96 &lt;= szybkości transmisji bitów &lt;= 128<br/><br/> 5/6:<br/><br/> - 32000 : 64 &lt;= szybkości transmisji bitów &lt;= 320<br/><br/> - 44100: 64 &lt;= szybkości transmisji bitów &lt;= 320<br/><br/> - 48000: 64 &lt;= szybkości transmisji bitów &lt;= 320<br/><br/> - 88200 : 256 &lt;= szybkości transmisji bitów &lt;= 320<br/><br/> - 96000: 256 &lt;= szybkości transmisji bitów &lt;= 320<br/><br/> 8:<br/><br/> - 32000: 96 &lt;= szybkości transmisji bitów &lt;= 448<br/><br/> - 44100: 96 &lt;= szybkości transmisji bitów &lt;= 448<br/><br/> - 48000: 96 &lt;= szybkości transmisji bitów &lt;= 448<br/><br/> - 88200: 384 &lt;= szybkości transmisji bitów &lt;= 448<br/><br/> - 96000: 384 &lt;= szybkości transmisji bitów &lt;= 448  
**HEAACV2** |2.<br/><br/> - 22050: 8 &lt;= szybkości transmisji bitów &lt;= 10<br/><br/> - 24000: 8 &lt;= szybkości transmisji bitów &lt;= 10<br/><br/> - 32000: 12 &lt;= szybkości transmisji bitów &lt;= 64<br/><br/> - 44100: 20 &lt;= szybkości transmisji bitów &lt;= 64<br/><br/> - 48000: 20 &lt;= szybkości transmisji bitów &lt;= 64<br/><br/> - 88200: 64 &lt;= szybkości transmisji bitów &lt;= 64  
  
## <a name="Clip"></a> Klipu
### <a name="attributes"></a>Atrybuty

| Name (Nazwa) | Typ | Opis |
| --- | --- | --- |
| **Godzina rozpoczęcia** |**DURATION** |Określa czas rozpoczęcia prezentacji. Wartość StartTime musi być zgodna bezwzględne sygnatury czasowe wejściowego filmu wideo. Na przykład, jeśli pierwszej ramki wejściowy plik wideo ma sygnaturę czasową 12:00:10.000, następnie wartość StartTime powinna być co najmniej 12:00:10.000 lub nowszej. |
| **Czas trwania** |**DURATION** |Określa czas prezentacji (na przykład wygląd nakładki w filmie wideo). |

## <a name="Output"></a> Dane wyjściowe
### <a name="attributes"></a>Atrybuty

| Name (Nazwa) | Typ | Opis |
| --- | --- | --- |
| **FileName** |**xs:String** |Nazwa pliku wyjściowego.<br/><br/> Makra, które opisano w poniższej tabeli można użyć do tworzenia nazw plików wyjściowych. Na przykład:<br/><br/> **"Wyjście": [{"Nazwa_pliku": "{Basename}*{rozpoznawania}* MP4 {szybkości transmisji bitów}", "Format": {"Type": "MP4Format"}}]** |

### <a name="macros"></a>Makra

| Macro | Opis |
| --- | --- |
| **{Basename}** |Jeśli przeprowadzasz kodowania wideo na żądanie {Basename} jest pierwsze 32 znaki właściwość AssetFile.Name plik podstawowy w danych wejściowych zasobu.<br/><br/> Jeśli wejściowego elementu jest dynamiczne archiwum, następnie {Basename} jest tworzony na podstawie atrybutów trackName w manifeście serwera. Jeśli przesyłasz zadanie klipu podrzędnego, używając TopBitrate, podobnie jak: "< VideoStream\>TopBitrate < / VideoStream\>", plik wyjściowy zawiera film wideo, a następnie {Basename} jest pierwsze 32 znaki trackName warstwy wideo przy użyciu najwyższej szybkości transmisji bitów.<br/><br/> Jeśli zamiast tego użytkownik przesyła zadanie klipu podrzędnego, za pomocą wszystkich różnych danych wejściowych, takich jak "< VideoStream\>* < / VideoStream\>", plik wyjściowy zawiera film wideo, a następnie {Basename} jest pierwsze 32 znaki trackName programu odpowiednia warstwa wideo. |
| **{Codec}** |Mapowany do "H264" dla filmów wideo i "AAC" dla audio. |
| **{Bitrate}** |Docelowy wideo szybkości transmisji bitów, jeśli plik wyjściowy zawiera wideo i audio lub docelowej audio szybkość transmisji bitów, jeśli plik wyjściowy zawiera tylko audio. Wartość używana jest szybkości transmisji bitów w KB/s. |
| **{Channel}** |Liczba kanałów audio, jeśli plik zawiera audio. |
| **{Szerokość}** |Szerokość wideo, w pikselach, w pliku danych wyjściowych, jeśli plik zawiera wideo. |
| **{Wysokość}** |Wysokość filmu wideo, w pikselach, w pliku danych wyjściowych, jeśli plik zawiera wideo. |
| **{Extension}** |Dziedziczy właściwości "Type" dla pliku wyjściowego. Nazwa pliku wyjściowego ma rozszerzenie, które jest jednym z: "mp4", "ts", "jpg", "png" lub "bmp". |
| **{Index}** |Obowiązkowe dla miniatur. Powinny tylko znajdować się jeden raz. |

## <a name="Video"></a> Wideo (typ złożony dziedziczy z kodera-dekodera)
### <a name="attributes"></a>Atrybuty

| Name (Nazwa) | Typ | Opis |
| --- | --- | --- |
| **Rozpocznij** |**xs:String** | |
| **Step** |**xs:String** | |
| **Range** |**xs:String** | |
| **PreserveResolutionAfterRotation** |**xs:Boolean** |Aby uzyskać szczegółowe informacje na ten temat zobacz następującą sekcję: [PreserveResolutionAfterRotation](media-services-mes-schema.md#PreserveResolutionAfterRotation) |

### <a name="PreserveResolutionAfterRotation"></a> PreserveResolutionAfterRotation
Zalecane jest użycie **PreserveResolutionAfterRotation** flagi w połączeniu z wartości rozdzielczości wyrażony w procentach (Width = "100%" Height = "100%").  

Domyślnie ustawienia (szerokość, wysokość) rozdzielczości Koduj w ustawieniach wstępnych Media Encoder Standard (MES) są przeznaczone dla filmów wideo o 0 stopni. Na przykład jeśli wejściowy plik wideo jest 1280 x 720 o 0 stopni, następnie ustawień domyślnych upewnij się, że dane wyjściowe mają tego samego rozwiązania.  

![MESRoation1](./media/media-services-shemas/media-services-mes-roation1.png) 

Jeśli wejściowy plik wideo zostanie przechwycony z rotacji różna od zera (na przykład smartfonie lub tablecie przechowywanych w pionie), następnie MES domyślnie stosuje ustawienia rozdzielczości Koduj (szerokość, wysokość) wejściowego pliku wideo, a następnie kompensuje obrót. Na przykład zobacz obraz, który jest zgodna. Ustawienie wstępne używa Width = "100%" Height = "100%", który MES interpretuje jako wymagające dane wyjściowe były 1280 pikseli szerokości i wysokości 720 pikseli. Po obracanie wideo, następnie zmniejsza obraz, który pasuje do tego okna, prowadzące do obszarów pillar-box po lewej i prawej stronie.  

![MESRoation2](./media/media-services-shemas/media-services-mes-roation2.png) 

Alternatywnie można wprowadzać użytkowania **PreserveResolutionAfterRotation** Flaga i ustaw ją na "true" (wartość domyślna to "false"). Więc jeśli predefiniowane ma szerokość = "100%" Height = "100%", a PreserveResolutionAfterRotation wartość "true", wejściowy plik wideo, który jest 1280 pikseli szerokości i wysokości o 90 stopni 720 pikseli wyjściowe o 0 stopni, ale 720 pikseli szerokości i 1280 pikseli wysokości. Zobacz poniższy obraz:  

![MESRoation3](./media/media-services-shemas/media-services-mes-roation3.png) 

## <a name="FormatGroup"></a> FormatGroup (grupa)
### <a name="elements"></a>Elementy

| Name (Nazwa) | Typ | Opis |
| --- | --- | --- |
| **BmpFormat** |**BmpFormat** | |
| **PngFormat** |**PngFormat** | |
| **JpgFormat** |**JpgFormat** | |

## <a name="BmpLayer"></a> BmpLayer
### <a name="element"></a>Element

| Name (Nazwa) | Typ | Opis |
| --- | --- | --- |
| **Szerokość**<br/><br/> minOccurs="0" |**xs:int** | |
| **Wysokość**<br/><br/> minOccurs="0" |**xs:int** | |

### <a name="attributes"></a>Atrybuty

| Name (Nazwa) | Typ | Opis |
| --- | --- | --- |
| **Warunek** |**xs:String** | |

## <a name="PngLayer"></a> PngLayer
### <a name="element"></a>Element

| Name (Nazwa) | Typ | Opis |
| --- | --- | --- |
| **Szerokość**<br/><br/> minOccurs="0" |**xs:int** | |
| **Wysokość**<br/><br/> minOccurs="0" |**xs:int** | |

### <a name="attributes"></a>Atrybuty

| Name (Nazwa) | Typ | Opis |
| --- | --- | --- |
| **Warunek** |**xs:String** | |

## <a name="JpgLayer"></a> JpgLayer
### <a name="element"></a>Element

| Name (Nazwa) | Typ | Opis |
| --- | --- | --- |
| **Szerokość**<br/><br/> minOccurs="0" |**xs:int** | |
| **Wysokość**<br/><br/> minOccurs="0" |**xs:int** | |
| **Jakość**<br/><br/> minOccurs="0" |**xs:int** |Prawidłowe wartości: 1(worst)-100(Best) |

### <a name="attributes"></a>Atrybuty

| Name (Nazwa) | Typ | Opis |
| --- | --- | --- |
| **Warunek** |**xs:String** | |

## <a name="PngLayers"></a> PngLayers
### <a name="elements"></a>Elementy

| Name (Nazwa) | Typ | Opis |
| --- | --- | --- |
| **PngLayer**<br/><br/> minOccurs="0" maxOccurs="unbounded" |[PngLayer](media-services-mes-schema.md#PngLayer) | |

## <a name="BmpLayers"></a> BmpLayers
### <a name="elements"></a>Elementy

| Name (Nazwa) | Typ | Opis |
| --- | --- | --- |
| **BmpLayer**<br/><br/> minOccurs="0" maxOccurs="unbounded" |[BmpLayer](media-services-mes-schema.md#BmpLayer) | |

## <a name="JpgLayers"></a> JpgLayers
### <a name="elements"></a>Elementy

| Name (Nazwa) | Typ | Opis |
| --- | --- | --- |
| **JpgLayer**<br/><br/> minOccurs="0" maxOccurs="unbounded" |[JpgLayer](media-services-mes-schema.md#JpgLayer) | |

## <a name="BmpImage"></a> BmpImage (typ złożony dziedziczy z wideo)
### <a name="elements"></a>Elementy

| Name (Nazwa) | Typ | Opis |
| --- | --- | --- |
| **PngLayers**<br/><br/> minOccurs="0" |[PngLayers](media-services-mes-schema.md#PngLayers) |Warstwy PNG |

## <a name="JpgImage"></a> JpgImage (typ złożony dziedziczy z wideo)
### <a name="elements"></a>Elementy

| Name (Nazwa) | Typ | Opis |
| --- | --- | --- |
| **PngLayers**<br/><br/> minOccurs="0" |[PngLayers](media-services-mes-schema.md#PngLayers) |Warstwy PNG |

## <a name="PngImage"></a> PngImage (typ złożony dziedziczy z wideo)
### <a name="elements"></a>Elementy

| Name (Nazwa) | Typ | Opis |
| --- | --- | --- |
| **PngLayers**<br/><br/> minOccurs="0" |[PngLayers](media-services-mes-schema.md#PngLayers) |Warstwy PNG |

## <a name="examples"></a>Przykłady
Zobacz przykłady XML ustawienia wstępne, które są tworzone na podstawie tego schematu, zobacz [ustawienia wstępne zadań usługi MES (Media Encoder Standard)](media-services-mes-presets-overview.md).

## <a name="next-steps"></a>Kolejne kroki
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

