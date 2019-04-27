---
title: Kodowanie wideo za pomocą kodera w warstwie standardowa w usłudze Media Services — Azure | Dokumentacja firmy Microsoft
description: W tym temacie pokazano, jak używać kodera w warstwie standardowa w usłudze Media Services do kodowania danych wejściowych wystąpili drabiny szybkości transmisji bitów z wygenerowany automatycznie, na podstawie rozdzielczości i szybkości transmisji bitów.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/10/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: c25c32f35adc1c017f0f4c012c82bd7e0af8d452
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60733321"
---
#  <a name="encode-with-an-auto-generated-bitrate-ladder"></a>Kodowanie za pomocą drabiny szybkości transmisji bitów z wygenerowany automatycznie

## <a name="overview"></a>Przegląd

W tym artykule wyjaśniono, jak używać kodera w warstwie standardowa w usłudze Media Services do zakodowania wejściowy plik wideo w drabiny szybkości transmisji bitów z automatycznego generowania (pary rozpoznawania szybkości transmisji bitów), na podstawie rozdzielczości i szybkości transmisji bitów. Koder wbudowanego ustawiania lub ustawienie wstępne, nigdy nie przekroczy rozdzielczości i szybkości transmisji bitów. Na przykład jeśli dane wejściowe są 720p 3 MB/s, dane wyjściowe w najlepszym pozostaje 720p i rozpocznie się stawek niższy niż 3 MB/s.

### <a name="encoding-for-streaming"></a>Kodowanie przesyłania strumieniowego

Kiedy używasz **AdaptiveStreaming** wstępnie zdefiniowanych w **Przekształcanie**, otrzymasz dane wyjściowe, który jest odpowiedni do dostarczania za pośrednictwem protokołów, takich jak HLS i DASH przesyłania strumieniowego. Korzystając z tego ustawienia wstępnego, usługa inteligentnie określa liczbę warstw wideo, aby wygenerować i w jakich szybkości transmisji bitów i rozdzielczości. Zawartość danych wyjściowych zawiera pliki MP4, gdzie zakodowane w formacie AAC audio i wideo H.264 algorytmem Base64 nie ma przeplotu.

Aby zapoznać się z przykładem użycia tego ustawienia wstępnego, zobacz [Stream pliku](stream-files-dotnet-quickstart.md).

## <a name="output"></a>Dane wyjściowe

W tej sekcji przedstawiono trzy przykłady warstw wideo dane wyjściowe generowane przez usługi Media Services Encoder w warstwie wyniku kodowania z **AdaptiveStreaming** wstępnie zdefiniowane. We wszystkich przypadkach dane wyjściowe zawierają tylko dane audio plik MP4 z dźwiękiem stereo zakodowane w 128 Kb/s.

### <a name="example-1"></a>Przykład 1
Źródło o wysokości "1080" i "29.970" szybkość klatek generuje 6 warstwy wideo:

|Warstwa|Wysokość|Szerokość|Szybkość transmisji bitów (KB/s)|
|---|---|---|---|
|1|1080|1920|6780|
|2|720|1280|3520|
|3|540|960|2210|
|4|360|640|1150|
|5|270|480|720|
|6|180|320|380|

### <a name="example-2"></a>Przykład 2
Źródło o wysokości "720" i "23.970" szybkość klatek generuje 5 warstwy wideo:

|Warstwa|Wysokość|Szerokość|Szybkość transmisji bitów (KB/s)|
|---|---|---|---|
|1|720|1280|2940|
|2|540|960|1850|
|3|360|640|960|
|4|270|480|600|
|5|180|320|320|

### <a name="example-3"></a>Przykład 3
Źródło o wysokości "360" i "29.970" szybkość klatek generuje 3 warstwy wideo:

|Warstwa|Wysokość|Szerokość|Szybkość transmisji bitów (KB/s)|
|---|---|---|---|
|1|360|640|700|
|2|270|480|440|
|3|180|320|230|

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Strumieniowe przesyłanie pliku](stream-files-dotnet-quickstart.md)
