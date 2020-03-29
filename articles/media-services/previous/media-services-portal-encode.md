---
title: Kodowanie zasobu przy użyciu standardu media encoder w witrynie Azure portal | Dokumenty firmy Microsoft
description: W tym samouczku otrzymasz od ciebie kroki kodowania zasobu przy użyciu standardu media encoder w witrynie Azure portal.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 107d9e9a-71e9-43e5-b17c-6e00983aceab
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: 7c147d99eaabee70316521d1f2bdc41933162ab1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "69542609"
---
# <a name="encode-an-asset-by-using-media-encoder-standard-in-the-azure-portal"></a>Kodowanie elementu zawartości za pomocą usługi Media Encoder Standard w witrynie Azure Portal

> [!NOTE]
> Do wykonania kroków tego samouczka potrzebne jest konto platformy Azure. Aby uzyskać szczegółowe informacje, zobacz [Bezpłatna wersja próbna platformy Azure](https://azure.microsoft.com/pricing/free-trial/). 
> 
> 

Jednym z najbardziej typowych scenariuszy pracy z usługą Azure Media Services jest dostarczanie adaptacyjnego przesyłania strumieniowego szybkości transmisji bitów do klientów. Usługa Media Services obsługuje następujące technologie adaptacyjnego przesyłania strumieniowego szybkości transmisji bitów: Przesyłanie strumieniowe na żywo (HLS) firmy Apple HTTP, płynne przesyłanie strumieniowe firmy Microsoft i dynamiczne adaptacyjne przesyłanie strumieniowe za pośrednictwem protokołu HTTP (DASH, zwanego również MPEG-DASH). Aby przygotować filmy do adaptacyjnego przesyłania strumieniowego szybkości transmisji bitów, najpierw zakoduj źródłowy film jako pliki o wielu szybkościach transmisji bitów. Kodowanie filmów umożliwia kodowanie filmów za pomocą programu Media Encoder Standard.  

Usługa Media Services udostępnia funkcję dynamicznego tworzenia pakietów. Dzięki dynamicznym pakowaniu możesz dostarczać wielospadowe formaty MP4 w HLS, Smooth Streaming i MPEG-DASH bez konieczności przepakowywania w tych formatach przesyłania strumieniowego. Korzystając z dynamicznego pakowania, można przechowywać i płacić za pliki w formacie pojedynczego przechowywania. Usługa Media Services tworzy i obsługuje odpowiednią odpowiedź na podstawie żądania klienta.

Aby skorzystać z dynamicznego tworzenia pakietów, musisz zakodować swoje pliki źródłowe jako zestaw plików MP4 o różnych szybkościach transmisji bitów. Kroki kodowania są przedstawione w dalszej części tego artykułu.

Aby dowiedzieć się, jak skalować przetwarzanie multimediów, zobacz [Skalowanie przetwarzania multimediów przy użyciu witryny Azure portal](media-services-portal-scale-media-processing.md).

## <a name="encode-in-the-azure-portal"></a>Kodowanie w witrynie Azure portal

Aby zakodować zawartość przy użyciu standardu Media Encoder Standard:

1. W witrynie [Azure Portal](https://portal.azure.com/) wybierz swoje konto usługi Azure Media Services.
2. Wybierz **pozycję Zasoby ustawień** > **Assets**. Wybierz element zawartości, który chcesz zakodować.
3. Wybierz przycisk **Koduj**.
4. W okienku **Kodowanie elementu zawartości** wybierz procesor **Media Encoder Standard** i ustawienia wstępne. Aby uzyskać informacje o ustawieniach wstępnych, zobacz [Auto-generate a bitrate ladder](media-services-autogen-bitrate-ladder-with-mes.md) (Automatyczne generowanie drabiny szybkości transmisji bitów) i [Task presets for Media Encoder Standard](media-services-mes-presets-overview.md) (Ustawienia wstępne zadań procesora Media Encoder Standard). Ważne jest, aby wybrać ustawienia wstępne, które będą najlepiej działać dla wejściowego pliku wideo. Na przykład: jeśli wejściowy plik wideo ma rozdzielczość 1920 &#215; 1080 pikseli, można użyć ustawienia wstępnego **Wielokrotna szybkość transmisji bitów H264 1080p**. Jeśli masz wideo w niskiej rozdzielczości (640 &#215; 360), ustawienie wstępne **Wielokrotna szybkość transmisji bitów H264 1080p** nie powinno być używane.
   
   Aby ułatwić sobie zarządzanie zasobami, możesz poddać edycji nazwę wyjściowego elementu zawartości i nazwę zadania.
   
   ![Kodowanie elementów zawartości](./media/media-services-portal-vod-get-started/media-services-encode1.png)
5. Wybierz **pozycję Utwórz**.

## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Następne kroki
* [Monitorowanie postępu zadania kodowania](media-services-portal-check-job-progress.md) w witrynie Azure portal.  

