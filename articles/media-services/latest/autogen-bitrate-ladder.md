---
title: Użyj standardowego kodera w usłudze Azure Media Services do kodowania plików wideo przy użyciu drabinę automatycznie generowanej szybkości transmisji bitów | Dokumentacja firmy Microsoft
description: W tym temacie pokazano, jak użyć standardowego kodera w usłudze Media Services do kodowania danych wejściowych wideo z drabinę automatycznie generowanej szybkości transmisji bitów, na podstawie rozdzielczość i szybkość transmisji bitów. Rozdzielczość i szybkość transmisji bitów nigdy nie zostanie przekroczony. Na przykład jeśli dane wejściowe są 720p w 3 MB/s, dane wyjściowe będą pozostawać w najlepszym 720p i rozpocznie stawkami niższa niż 3 MB/s.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2018
ms.author: juliako
ms.openlocfilehash: 6e447c04f4a94f2fb534ecb0605595a90816431e
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34638300"
---
#  <a name="encode-with-an-auto-generated-bitrate-ladder"></a>Kodowanie z drabinę automatycznie generowanej szybkości transmisji bitów

## <a name="overview"></a>Przegląd

W tym artykule opisano sposób używania standardowego kodera w usłudze Media Services do kodowania wejściowy plik wideo do drabinę automatycznie generowanej szybkości transmisji bitów (pary rozpoznawania szybkości transmisji bitów), na podstawie rozdzielczość i szybkość transmisji bitów. Ten koder wbudowanych ustawienie lub ustawienie wstępne, nigdy nie przekroczy rozdzielczość i szybkość transmisji bitów. Na przykład jeśli dane wejściowe są 720p 3 MB/s, dane wyjściowe w najlepszym pozostaje 720p i rozpocznie stawkami niższa niż 3 MB/s.

### <a name="encoding-for-streaming"></a>Kodowanie używane do przesyłania strumieniowego

Jeśli używasz **AdaptiveStreaming** wstępnie zdefiniowane w **przekształcenie**, otrzymasz dane wyjściowe, które jest odpowiednie dla dostawy za pośrednictwem przesyłania strumieniowego protokołów, takich jak HLS i KRESKI. Korzystając z tego ustawienia wstępnego, usługa inteligentnie określa liczbę warstw wideo, aby wygenerować i w jaki szybkości transmisji bitów i rozdzielczości. Zawartość danych wyjściowych zawiera pliki MP4, gdzie kodowany w formacie AAC audio i wideo H.264 algorytmem Base64 nie jest przeplatana.

Aby wyświetlić przykład sposobu używania tego ustawienia wstępnego, zobacz [strumienia pliku](stream-files-dotnet-quickstart.md).

## <a name="output"></a>Dane wyjściowe

W tej sekcji przedstawiono trzy przykłady warstw wideo dane wyjściowe generowane przez koder Media Services wyniku z kodowaniem **AdaptiveStreaming** wstępnie zdefiniowane. We wszystkich przypadkach dane wyjściowe zawierają tylko dźwięk plików MP4 z stereo audio zakodowane w 128 Kb/s.

### <a name="example-1"></a>Przykład 1
Źródło o wysokość "1080" i "29.970" szybkość klatek tworzy 6 warstwy wideo:

|Warstwa|Wysokość|Szerokość|Szybkość transmisji bitów (KB/s)|
|---|---|---|---|
|1|1080|1920|6780|
|2|720|1280|3520|
|3|540|960|2210|
|4|360|640|1150|
|5|270|480|720|
|6|180|320|380|

### <a name="example-2"></a>Przykład 2
Źródło o wysokość "720" i "23.970" szybkość klatek tworzy 5 warstwy wideo:

|Warstwa|Wysokość|Szerokość|Szybkość transmisji bitów (KB/s)|
|---|---|---|---|
|1|720|1280|2940|
|2|540|960|1850|
|3|360|640|960|
|4|270|480|600|
|5|180|320|320|

### <a name="example-3"></a>Przykład 3
Źródło o wysokość "360" i "29.970" szybkość klatek tworzy 3 warstwy wideo:

|Warstwa|Wysokość|Szerokość|Szybkość transmisji bitów (KB/s)|
|---|---|---|---|
|1|360|640|700|
|2|270|480|440|
|3|180|320|230|

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Strumieniowe przesyłanie pliku](stream-files-dotnet-quickstart.md)
