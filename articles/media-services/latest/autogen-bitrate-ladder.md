---
title: Kodowanie klipów wideo za pomocą standardowego kodera w usłudze Media Services — Azure | Dokumenty firmy Microsoft
description: W tym temacie pokazano, jak używać kodera standardowego w udziale usługi Media Services do kodowania wejściowego wideo za pomocą automatycznie generowanej szybkości transmisji bitów, na podstawie rozdzielczości wejściowej i szybkości transmisji bitów.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60733321"
---
#  <a name="encode-with-an-auto-generated-bitrate-ladder"></a>Kodowanie za pomocą automatycznie generowanej bitrate drabiny

## <a name="overview"></a>Omówienie

W tym artykule wyjaśniono, jak używać kodera standardowego w udziale programu Media Services do kodowania wejściowego wideo do automatycznie generowanej szybkości transmisji bitów (par bitrate-resolution) na podstawie rozdzielczości wejściowej i szybkości transmisji bitów. To wbudowane ustawienie cewaru lub predefiniowane ustawienie nigdy nie przekroczy rozdzielczości wejściowej i szybkości transmisji bitów. Na przykład jeśli wejście jest 720p przy 3 Mbps, wyjście pozostaje 720p w najlepszym i rozpocznie się od szybkości niższej niż 3 Mbps.

### <a name="encoding-for-streaming"></a>Kodowanie do przesyłania strumieniowego

Podczas korzystania z **adaptiveStreaming** preset w **Transform**, otrzymasz dane wyjściowe, które nadaje się do dostarczania za pośrednictwem protokołów przesyłania strumieniowego, takich jak HLS i DASH. Podczas korzystania z tego ustawienia wstępnego usługa inteligentnie określa, ile warstw wideo do wygenerowania i w jakim tempie transmisji bitów i rozdzielczości. Zawartość wyjściowa zawiera pliki MP4, w których dźwięk zakodowany w aac i wideo zakodowane w h.264 nie są przeplotem.

Aby zobaczyć przykład użycia tego ustawienia predefiniowane, zobacz [Przesyłanie strumieniowe pliku](stream-files-dotnet-quickstart.md).

## <a name="output"></a>Dane wyjściowe

W tej sekcji przedstawiono trzy przykłady wyjściowych warstw wideo opracowanych przez koder usługi Media Services w wyniku kodowania z predefiniowaniem **adaptivestreaming.** We wszystkich przypadkach wyjście zawiera tylko audio plik MP4 z stereo audio zakodowane na 128 kbps.

### <a name="example-1"></a>Przykład 1
Źródło o wysokości "1080" i szybkości klatek na sekundę "29.970" tworzy 6 warstw wideo:

|Warstwa|Właściwość Height|impulsów|Szybkość transmisji bitów (kb/s)|
|---|---|---|---|
|1|1080|1920|6780|
|2|720|1280|3520|
|3|540|960|2210|
|4|360|640|1150|
|5|270|480|720|
|6|180|320|380|

### <a name="example-2"></a>Przykład 2
Źródło o wysokości "720" i szybkości klatek na sekundę "23.970" tworzy 5 warstw wideo:

|Warstwa|Właściwość Height|impulsów|Szybkość transmisji bitów (kb/s)|
|---|---|---|---|
|1|720|1280|2940|
|2|540|960|1850|
|3|360|640|960|
|4|270|480|600|
|5|180|320|320|

### <a name="example-3"></a>Przykład 3
Źródło o wysokości "360" i szybkości klatek na sekundę "29.970" tworzy 3 warstwy wideo:

|Warstwa|Właściwość Height|impulsów|Szybkość transmisji bitów (kb/s)|
|---|---|---|---|
|1|360|640|700|
|2|270|480|440|
|3|180|320|230|

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Strumieniowe przesyłanie pliku](stream-files-dotnet-quickstart.md)
