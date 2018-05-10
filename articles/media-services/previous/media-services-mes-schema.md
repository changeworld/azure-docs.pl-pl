---
title: Schemat Media Encoder Standard | Dokumentacja firmy Microsoft
description: Artykuł zawiera omówienie Media Encoder Standard schematu.
author: Juliako
manager: cfowler
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: 4c060062-8ef2-41d9-834e-e81e8eafcf2e
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2017
ms.author: juliako
ms.openlocfilehash: 346d7aecb6a4295f8ceb64bc1b5c6494b7b41bfd
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
---
# <a name="media-encoder-standard-schema"></a>Schemat Media Encoder Standard
W tym artykule opisano niektóre elementy i typy schematu XML, na którym [ustawienia standardu Media Encoder Standard](media-services-mes-presets-overview.md) opierają się. Artykuł zawiera szczegółowy opis elementów i ich prawidłowe wartości.  

## <a name="Preset"></a> Ustawienia domyślne (element główny)
Definiuje ustawienie kodowania.  

### <a name="elements"></a>Elementy
| Name (Nazwa) | Typ | Opis |
| --- | --- | --- |
| **Kodowanie** |[Kodowanie](media-services-mes-schema.md#Encoding) |Element główny wskazuje, czy źródeł danych wejściowych do zakodowania. |
| **Dane wyjściowe** |[Dane wyjściowe](media-services-mes-schema.md#Output) |Kolekcja plików żądanego wyniku. |

### <a name="attributes"></a>Atrybuty
| Name (Nazwa) | Typ | Opis |
| --- | --- | --- |
| **Wersja**<br/><br/> Wymagane |**xs: dziesiętne** |Wersja wstępnie zdefiniowane. Występują następujące ograniczenia: wartość xs:fractionDigits = wartość "1" i xs:minInclusive = "1" na przykład **wersja = "1.0"**. |

## <a name="Encoding"></a> Kodowanie
Zawiera sekwencję następujące elementy:  

### <a name="elements"></a>Elementy
| Name (Nazwa) | Typ | Opis |
| --- | --- | --- |
| **H264Video** |[H264Video](media-services-mes-schema.md#H264Video) |Ustawienia dla kodowanie wideo H.264. |
| **AACAudio** |[AACAudio](media-services-mes-schema.md#AACAudio) |Ustawienia kodowania audio AAC. |
| **BmpImage** |[BmpImage](media-services-mes-schema.md#BmpImage) |Ustawienia dla obrazu w formacie Bmp. |
| **PngImage** |[PngImage](media-services-mes-schema.md#PngImage) |Ustawienia dla obrazów Png. |
| **JpgImage** |[JpgImage](media-services-mes-schema.md#JpgImage) |Ustawienia dla obrazów Jpg. |

## <a name="H264Video"></a> H264Video
### <a name="elements"></a>Elementy
| Name (Nazwa) | Typ | Opis |
| --- | --- | --- |
| **TwoPass**<br/><br/> minOccurs="0" |**xs:Boolean** |Aktualnie obsługiwana jest tylko jeden przebieg kodowania. |
| **KeyFrameInterval**<br/><br/> minOccurs="0"<br/><br/> **default="00:00:02"** |**xs: Time** |Określa stały odstęp między ramki IDR w jednostkach czasu w sekundach. Również nazywane GOP czas trwania. Zobacz **SceneChangeDetection** do kontrolowania, czy koder można różni się od tej wartości. |
| **SceneChangeDetection**<br/><br/> minOccurs="0"<br/><br/> domyślne = "false" |**xs: wartość logiczna** |Jeśli ustawiono wartość true, koder podejmuje próbę wykrycia zmianę sceny w wideo i wstawia ramkę IDR. |
| **Złożoność**<br/><br/> minOccurs="0"<br/><br/> default="Balanced" |**xs:String** |Formanty równowagę między kodowania jakości szybkość i wideo. Może być jedną z następujących wartości: **szybkości**, **zrównoważony**, lub **jakości**<br/><br/> Wartość domyślna: **zrównoważonym** |
| **SyncMode**<br/><br/> minOccurs="0" | |Funkcja mają być widoczne w przyszłej wersji. |
| **H264Layers**<br/><br/> minOccurs="0" |[H264Layers](media-services-mes-schema.md#H264Layers) |Kolekcja warstw wideo danych wyjściowych. |

### <a name="attributes"></a>Atrybuty
| Name (Nazwa) | Typ | Opis |
| --- | --- | --- |
| **Warunek** |**xs:String** | Dane wejściowe nie ma karty wideo, można wymusić kodera, aby wstawić monochromatyczny Ścieżka wideo. W tym celu należy użyć warunku = "InsertBlackIfNoVideoBottomLayerOnly" (Aby wstawić wideo na wyłącznie najniższa szybkość transmisji bitów) lub warunek = "InsertBlackIfNoVideo" (Aby wstawić wideo output szybkości transmisji bitów). Więcej informacji znajduje się w [tym](media-services-advanced-encoding-with-mes.md#no_video) artykule.|

## <a name="H264Layers"></a> H264Layers

Domyślnie w przypadku wysłania danych wejściowych do kodera, który zawiera tylko audio i wideo nie elementu zawartości wyjściowej zawiera pliki tylko danych audio. Niektóre odtwarzacze nie można obsłużyć tych strumieni wyjściowych. Można użyć H264Video **InsertBlackIfNoVideo** atrybutu ustawienie, aby wymusić kodera, aby dodać ścieżki wideo do danych wyjściowych w tym scenariuszu. Więcej informacji znajduje się w [tym](media-services-advanced-encoding-with-mes.md#no_video) artykule.
              
### <a name="elements"></a>Elementy
| Name (Nazwa) | Typ | Opis |
| --- | --- | --- |
| **H264Layer**<br/><br/> minOccurs="0" maxOccurs="unbounded" |[H264Layer](media-services-mes-schema.md#H264Layer) |Kolekcja H264 warstwy. |

## <a name="H264Layer"></a> H264Layer
> [!NOTE]
> Limity wideo są oparte na wartości opisanych w [poziomy H264](https://en.wikipedia.org/wiki/H.264/MPEG-4_AVC#Levels) tabeli.  
> 
> 

### <a name="elements"></a>Elementy
| Name (Nazwa) | Typ | Opis |
| --- | --- | --- |
| **Profil**<br/><br/> minOccurs="0"<br/><br/> default=”Auto” |**xs: ciąg** |Może być jedną z następujących **xs: ciąg** wartości: **automatycznie**, **linii bazowej**, **Main**, **wysokiej**. |
| **Poziom**<br/><br/> minOccurs="0"<br/><br/> default=”Auto” |**xs: ciąg** | |
| **Szybkość transmisji bitów**<br/><br/> minOccurs="0" |**xs:int** |Szybkość transmisji bitów używany dla tej warstwy wideo określonej w KB/s. |
| **MaxBitrate**<br/><br/> minOccurs="0" |**xs: int** |Maksymalna szybkość transmisji bitów używany dla tej warstwy wideo określonej w KB/s. |
| **BufferWindow**<br/><br/> minOccurs="0"<br/><br/> default="00:00:05" |**xs: czas** |Długość buforu wideo. |
| **Szerokość**<br/><br/> minOccurs="0" |**xs: int** |Szerokość ramki wyjście wideo w pikselach.<br/><br/> Obecnie należy określić zarówno szerokość i wysokość. Szerokość i wysokość muszą być liczby parzyste. |
| **Wysokość**<br/><br/> minOccurs="0" |**xs:int** |Wysokość ramki wyjście wideo w pikselach.<br/><br/> Obecnie należy określić zarówno szerokość i wysokość. Szerokość i wysokość muszą być liczby parzyste.|
| **BFrames**<br/><br/> minOccurs="0" |**xs: int** |Liczba ramek B między ramki odwołania. |
| **ReferenceFrames**<br/><br/> minOccurs="0"<br/><br/> domyślne = "3" |**xs:int** |Liczba ramek odwołania w GOP. |
| **EntropyMode**<br/><br/> minOccurs="0"<br/><br/> default=”Cabac” |**xs: ciąg** |Może być jedną z następujących wartości: **Cabac** i **Cavlc**. |
| **Szybkość klatek**<br/><br/> minOccurs="0" |Liczba wymierna |Określa szybkość odtwarzania wideo danych wyjściowych. Użyj domyślnej "0/1", aby poinformować kodera, użyj tego samego szybkość klatek jako wejściowego pliku wideo. Dozwolone wartości powinny być wspólne szybkości odtwarzania wideo. Jednak wszystkie prawidłowe wymierna jest dozwolone. Na przykład 1/1 będą 1 kl. / s i jest prawidłowy.<br/><br/> -12/1 (12 kl. / s)<br/><br/> -15/1 (15 kl. / s)<br/><br/> -24/1 (24 kl. / s)<br/><br/> 24000/1001 (23.976 kl. / s)<br/><br/> -25/1 (25 kl. / s)<br/><br/>  -30/1 (30 kl. / s)<br/><br/> 30000/1001 (29,97 kl. / s) <br/> <br/>**Uwaga** w przypadku tworzenia niestandardowych ustawienie wstępne kodowania wielu szybkości transmisji bitów, następnie wszystkie warstwy ustawienie **musi** używać tej samej wartości szybkość klatek.|
| **AdaptiveBFrame**<br/><br/> minOccurs="0" |**xs: wartość logiczna** |Kopiowanie z kodera multimediów Azure |
| **Wycinków**<br/><br/> minOccurs="0"<br/><br/> default="0" |**xs:int** |Określa liczbę wycinków ramki jest podzielony na. Zaleca się używanie domyślnego. |

## <a name="AACAudio"></a> AACAudio
 Zawiera sekwencję następujące elementy i grupy.  

 Aby uzyskać więcej informacji na temat AAC, zobacz [AAC](https://en.wikipedia.org/wiki/Advanced_Audio_Coding).  

### <a name="elements"></a>Elementy
| Name (Nazwa) | Typ | Opis |
| --- | --- | --- |
| **Profil**<br/><br/> minOccurs = "0"<br/><br/> default="AACLC" |**xs: ciąg** |Może być jedną z następujących wartości: **AACLC**, **HEAACV1**, lub **HEAACV2**. |

### <a name="attributes"></a>Atrybuty
| Name (Nazwa) | Typ | Opis |
| --- | --- | --- |
| **Warunek** |**xs: ciąg** |Aby wymusić kodera, aby utworzyć element zawartości zawierający dyskretnej ścieżki audio, gdy dane wejściowe nie zawierają żadnych audio, określ wartość "InsertSilenceIfNoAudio".<br/><br/> Domyślnie w przypadku wysłania danych wejściowych do kodera, który zawiera tylko wideo i audio nie następnie elementu zawartości wyjściowej zawiera pliki, które zawierają dane tylko wideo. Niektóre odtwarzacze nie można obsłużyć tych strumieni wyjściowych. To ustawienie umożliwia wymuszenie kodera, aby dodać dyskretnej ścieżki audio danych wyjściowych w tym scenariuszu. |

### <a name="groups"></a>Grupy
| Informacje ogólne | Opis |
| --- | --- |
| [AudioGroup](media-services-mes-schema.md#AudioGroup)<br/><br/> minOccurs="0" |Zobacz opis [AudioGroup](media-services-mes-schema.md#AudioGroup) wiedzieć odpowiednią liczbę kanałów, częstotliwość próbkowania i szybkość transmisji bitów, które można ustawić dla każdego profilu. |

## <a name="AudioGroup"></a> AudioGroup
Aby uzyskać szczegóły, jakie wartości są prawidłowe dla każdego profilu zobacz poniższą tabelą "Szczegóły kodera-dekodera Audio".  

### <a name="elements"></a>Elementy
| Name (Nazwa) | Typ | Opis |
| --- | --- | --- |
| **Kanały**<br/><br/> minOccurs="0" |**xs: int** |Liczba kanałów audio zakodowany. Poniżej przedstawiono prawidłowe opcje: 1, 2, 5, 6, 8.<br/><br/> Domyślne: 2. |
| **SamplingRate**<br/><br/> minOccurs="0" |**xs: int** |Częstotliwość próbkowania audio, określona w Hz. |
| **Szybkość transmisji bitów**<br/><br/> minOccurs="0" |**xs: int** |Szybkość transmisji bitów używany podczas kodowania audio, określonej w KB/s. |

### <a name="audio-codec-details"></a>Szczegóły kodera-dekodera audio
Kodera-dekodera audio|Szczegóły  
-----------------|---  
**AACLC**|1:<br/><br/> -11025: 8 &lt;= szybkości transmisji bitów &lt; 16<br/><br/> -12000: 8 &lt;= szybkości transmisji bitów &lt; 16<br/><br/> -16000: 8 &lt;= szybkości transmisji bitów &lt;32<br/><br/>-22050: 24 &lt;= szybkości transmisji bitów &lt; 32<br/><br/> -24000: 24 &lt;= szybkości transmisji bitów &lt; 32<br/><br/> -32000: 32 &lt;= szybkości transmisji bitów &lt;= 192<br/><br/> -44100: 56 &lt;= szybkości transmisji bitów &lt;= 288<br/><br/> -48000: 56 &lt;= szybkości transmisji bitów &lt;= 288<br/><br/> -88200: 128 &lt;= szybkości transmisji bitów &lt;= 288<br/><br/> -96000: 128 &lt;= szybkości transmisji bitów &lt;= 288<br/><br/> 2:<br/><br/> -11025: 16 &lt;= szybkości transmisji bitów &lt; 24<br/><br/> -12000: 16 &lt;= szybkości transmisji bitów &lt; 24<br/><br/> -16000: 16 &lt;= szybkości transmisji bitów &lt; 40<br/><br/> -22050: 32 &lt;= szybkości transmisji bitów &lt; 40<br/><br/> -24000: 32 &lt;= szybkości transmisji bitów &lt; 40<br/><br/> -32000: 40 &lt;= szybkości transmisji bitów &lt;= 384<br/><br/> -44100: 96 &lt;= szybkości transmisji bitów &lt;= 576<br/><br/> -48000: 96 &lt;= szybkości transmisji bitów &lt;= 576<br/><br/> -88200: 256 &lt;= szybkości transmisji bitów &lt;= 576<br/><br/> -96000: 256 &lt;= szybkości transmisji bitów &lt;= 576<br/><br/> 5/6:<br/><br/> -32000: 160 &lt;= szybkości transmisji bitów &lt;= 896<br/><br/> -44100: 240 &lt;= szybkości transmisji bitów &lt;= 1024<br/><br/> -48000: 240 &lt;= szybkości transmisji bitów &lt;= 1024<br/><br/> -88200: 640 &lt;= szybkości transmisji bitów &lt;= 1024<br/><br/> -96000: 640 &lt;= szybkości transmisji bitów &lt;= 1024<br/><br/> 8:<br/><br/> -32000: 224 &lt;= szybkości transmisji bitów &lt;= 1024<br/><br/> -44100: 384 &lt;= szybkości transmisji bitów &lt;= 1024<br/><br/> -48000: 384 &lt;= szybkości transmisji bitów &lt;= 1024<br/><br/> -88200: 896 &lt;= szybkości transmisji bitów &lt;= 1024<br/><br/> -96000: 896 &lt;= szybkości transmisji bitów &lt;= 1024  
**HEAACV1**|1:<br/><br/> -22050 b: szybkości transmisji bitów = 8<br/><br/> -24000: 8 &lt;= szybkości transmisji bitów &lt;= 10<br/><br/> -32000: 12 &lt;= szybkości transmisji bitów &lt;= 64<br/><br/> -44100: 20 &lt;= szybkości transmisji bitów &lt;= 64<br/><br/> -48000: 20 &lt;= szybkości transmisji bitów &lt;= 64<br/><br/> -88200 b: szybkości transmisji bitów = 64<br/><br/> 2:<br/><br/> -32000: 16 &lt;= szybkości transmisji bitów &lt;= 128<br/><br/> -44100: 16 &lt;= szybkości transmisji bitów &lt;= 128<br/><br/> -48000: 16 &lt;= szybkości transmisji bitów &lt;= 128<br/><br/> -88200: 96 &lt;= szybkości transmisji bitów &lt;= 128<br/><br/> -96000: 96 &lt;= szybkości transmisji bitów &lt;= 128<br/><br/> 5/6:<br/><br/> -32000: 64 &lt;= szybkości transmisji bitów &lt;= 320<br/><br/> -44100: 64 &lt;= szybkości transmisji bitów &lt;= 320<br/><br/> -48000: 64 &lt;= szybkości transmisji bitów &lt;= 320<br/><br/> -88200: 256 &lt;= szybkości transmisji bitów &lt;= 320<br/><br/> -96000: 256 &lt;= szybkości transmisji bitów &lt;= 320<br/><br/> 8:<br/><br/> -32000: 96 &lt;= szybkości transmisji bitów &lt;= 448<br/><br/> -44100: 96 &lt;= szybkości transmisji bitów &lt;= 448<br/><br/> -48000: 96 &lt;= szybkości transmisji bitów &lt;= 448<br/><br/> -88200: 384 &lt;= szybkości transmisji bitów &lt;= 448<br/><br/> -96000: 384 &lt;= szybkości transmisji bitów &lt;= 448  
**HEAACV2**|2:<br/><br/> -22050: 8 &lt;= szybkości transmisji bitów &lt;= 10<br/><br/> -24000: 8 &lt;= szybkości transmisji bitów &lt;= 10<br/><br/> -32000: 12 &lt;= szybkości transmisji bitów &lt;= 64<br/><br/> -44100: 20 &lt;= szybkości transmisji bitów &lt;= 64<br/><br/> -48000: 20 &lt;= szybkości transmisji bitów &lt;= 64<br/><br/> -88200: 64 &lt;= szybkości transmisji bitów &lt;= 64  
  

## <a name="Clip"></a> Clip
### <a name="attributes"></a>Atrybuty
| Name (Nazwa) | Typ | Opis |
| --- | --- | --- |
| **Czas rozpoczęcia** |**DURATION** |Określa czas rozpoczęcia prezentacji. Wartość StartTime musi być zgodna bezwzględną znacznikami czasu wejściowy plik wideo. Na przykład, jeśli pierwszej ramki wejściowy plik wideo ma sygnaturę 12:00:10.000, następnie wartość StartTime powinna być co najmniej 12:00:10.000 lub nowszej. |
| **Czas trwania** |**DURATION** |Określa czas prezentacji (na przykład wygląd nakładka wideo). |

## <a name="Output"></a> Dane wyjściowe
### <a name="attributes"></a>Atrybuty
| Name (Nazwa) | Typ | Opis |
| --- | --- | --- |
| **FileName** |**xs:String** |Nazwa pliku wyjściowego.<br/><br/> Makra opisane w poniższej tabeli można użyć do tworzenia nazwy pliku wyjściowego. Na przykład:<br/><br/> **"Wyjście": [{"FileName": "{nazwę bazową}*{rozpoznawania}* plik MP4 {szybkości transmisji bitów}", "Format": {"Type": "MP4Format"}}]** |

### <a name="macros"></a>Makra
| Macro | Opis |
| --- | --- |
| **{Basename}** |Jeśli przeprowadzasz kodowanie VoD {nazwę bazową} jest pierwsze 32 znaki właściwości AssetFile.Name podstawowego pliku zasobów wejściowych.<br/><br/> Jeśli zasób wejściowy jest archiwum na żywo, {nazwę bazową} jest pobierany z atrybutów trackName w manifeście serwera. Jeśli przesyłasz subclip zadania przy użyciu TopBitrate, podobnie jak w: "< VideoStream\>TopBitrate < / VideoStream\>", plik wyjściowy zawiera wideo, a następnie nazwę {bazową} jest pierwsze 32 znaki trackName warstwy wideo z najwyższą szybkości transmisji bitów.<br/><br/> Jeśli zamiast tego są przesyłania zadania subclip przy użyciu wszystkich wejściowych szybkości transmisji bitów, takich jak "< VideoStream\>* < / VideoStream\>", plik wyjściowy zawiera wideo, a następnie {nazwę bazową} jest pierwsze 32 znaki trackName programu odpowiednia warstwa wideo. |
| **{Codec}** |Mapowany do "H264" wideo i "AAC" dla audio. |
| **{Bitrate}** |Docelowy wideo szybkość transmisji bitów, jeśli plik wyjściowy zawiera wideo i audio lub szybkości transmisji bitów audio docelowych, jeśli plik wyjściowy zawiera tylko audio. Wartość używana jest szybkości transmisji bitów w KB/s. |
| **{Channel}** |Liczba kanałów audio, jeśli plik zawiera audio. |
| **{Szerokość}** |Szerokość wideo, w pikselach, w pliku wyjściowym, jeśli plik zawiera wideo. |
| **{Wysokość}** |Wysokość wideo, w pikselach, w pliku wyjściowym, jeśli plik zawiera wideo. |
| **{Rozszerzenia}** |Dziedziczy właściwości "Type" dla pliku wyjściowego. Nazwa pliku wyjściowego ma rozszerzenie, które jest jednym z: "mp4", "ts", "jpg", "png" lub "bmp". |
| **{Index}** |Obowiązkowe dla miniatur. Powinien mieć tylko występuje jeden raz. |

## <a name="Video"></a> Wideo (typ złożony dziedziczy koder-dekoder)
### <a name="attributes"></a>Atrybuty
| Name (Nazwa) | Typ | Opis |
| --- | --- | --- |
| **Rozpocznij** |**xs:String** | |
| **Krok** |**xs:String** | |
| **Range** |**xs:String** | |
| **PreserveResolutionAfterRotation** |**xs:Boolean** |Aby uzyskać szczegółowe informacje, zobacz następującą sekcję: [PreserveResolutionAfterRotation](media-services-mes-schema.md#PreserveResolutionAfterRotation) |

### <a name="PreserveResolutionAfterRotation"></a> PreserveResolutionAfterRotation
Zalecane jest użycie **PreserveResolutionAfterRotation** flagi w połączeniu z wartościami rozpoznawania wyrażony w procentach (Width = "100%", wysokość = "100%").  

Domyślnie ustawienia (szerokość, wysokość) rozdzielczości Koduj w predefiniowane Media Encoder Standard (rynkowej) są przeznaczone do plików wideo za pomocą 0 stopni. Na przykład jeśli wejściowy plik wideo jest 1280 x 720 z zero stopni, następnie ustawień domyślnych Sprawdź, czy dane wyjściowe ma taką samą rozdzielczość.  

![MESRoation1](./media/media-services-shemas/media-services-mes-roation1.png) 

Jeśli została przechwycona wejściowy plik wideo z obrotu różna od zera (na przykład smartphone lub tablet przechowywanych w pionie), następnie rynkowej domyślnie stosuje ustawienia rozpoznawania Koduj (szerokość, wysokość) wejściowego pliku wideo, a następnie kompensacji obrót. Na przykład zobacz obraz, który jest zgodny. Ustawienie używa Width = "100%", wysokość = "100%", który rynkowej interpretowane jako wymagające dane wyjściowe do 1280 pikseli szerokości i wysokości 720 pikseli. Po obracanie wideo, ją następnie zmniejsza obraz pasuje do tego okna, co może prowadzić do obszarów pillar-box po lewej i prawej stronie.  

![MESRoation2](./media/media-services-shemas/media-services-mes-roation2.png) 

Alternatywnie możesz wprowadzić użycie **PreserveResolutionAfterRotation** Flaga i ustaw ją na wartość "prawda" (wartość domyślna to "false"). Dlatego jeśli ustawienia ma szerokość = "100%", wysokość = "100%" i PreserveResolutionAfterRotation ustawioną wartość "prawda", wejściowy plik wideo, czyli 1280 pikseli szerokości i wysokości o 90 stopni 720 pikseli generuje dane wyjściowe z zero stopni, ale 720 pikseli szerokości i 1280 wysokości pikseli. Znajduje się poniżej:  

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
| **Jakość**<br/><br/> minOccurs="0" |**xs:int** |Prawidłowe wartości: 1(worst)-100(best) |

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

## <a name="BmpImage"></a> BmpImage (typ złożony dziedziczy wideo)
### <a name="elements"></a>Elementy
| Name (Nazwa) | Typ | Opis |
| --- | --- | --- |
| **PngLayers**<br/><br/> minOccurs="0" |[PngLayers](media-services-mes-schema.md#PngLayers) |Warstwy PNG |

## <a name="JpgImage"></a> JpgImage (typ złożony dziedziczy wideo)
### <a name="elements"></a>Elementy
| Name (Nazwa) | Typ | Opis |
| --- | --- | --- |
| **PngLayers**<br/><br/> minOccurs="0" |[PngLayers](media-services-mes-schema.md#PngLayers) |Warstwy PNG |

## <a name="PngImage"></a> PngImage (typ złożony dziedziczy wideo)
### <a name="elements"></a>Elementy
| Name (Nazwa) | Typ | Opis |
| --- | --- | --- |
| **PngLayers**<br/><br/> minOccurs="0" |[PngLayers](media-services-mes-schema.md#PngLayers) |Warstwy PNG |

## <a name="examples"></a>Przykłady
Zobacz przykłady XML ustawień, które są tworzone na podstawie tego schematu, zobacz [ustawienia zadania rynkowej (Media Encoder Standard)](media-services-mes-presets-overview.md).

## <a name="next-steps"></a>Kolejne kroki
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

