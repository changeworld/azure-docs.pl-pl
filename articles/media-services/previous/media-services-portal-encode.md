---
title: Kodowanie elementu zawartości za pomocą usługi Media Encoder Standard w witrynie Azure portal | Dokumentacja firmy Microsoft
description: Ten samouczek przeprowadzi Cię przez kroki kodowania zasobów za pomocą usługi Media Encoder Standard w witrynie Azure portal.
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
ms.openlocfilehash: 90190f426419e65bd580b9004ae76a2c6b0c12e2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61463166"
---
# <a name="encode-an-asset-by-using-media-encoder-standard-in-the-azure-portal"></a>Kodowanie elementu zawartości za pomocą usługi Media Encoder Standard w witrynie Azure portal

> [!NOTE]
> Do ukończenia tego samouczka jest potrzebne konto platformy Azure. Aby uzyskać szczegółowe informacje, zobacz [Bezpłatna wersja próbna systemu Azure](https://azure.microsoft.com/pricing/free-trial/). 
> 
> 

Jednym z najbardziej typowych scenariuszy, w pracy z usługą Azure Media Services jest dostarczanie adaptacyjną szybkością transmisji bitów klientom usługi przesyłania strumieniowego. Usługa Media Services obsługuje następujące technologie przesyłania strumieniowego adaptacyjną szybkością transmisji bitów: Apple HTTP Live Streaming (HLS), Microsoft Smooth Streaming i Dynamic Adaptive Streaming za pośrednictwem protokołu HTTP (DASH, również o nazwie MPEG-DASH). Do przesyłania strumieniowego z adaptacyjną szybkością transmisji bitów, należy przygotować pliki wideo, najpierw należy zakodować źródłowy plik wideo jako pliki o różnych szybkościach transmisji bitów. Usługi Azure Media Encoder Standard umożliwia kodowanie filmów wideo.  

Usługa Media Services udostępnia funkcję dynamicznego tworzenia pakietów. Za pomocą funkcji dynamicznego tworzenia pakietów można dostarczać usługi każdego pliku MP4 o różnych szybkościach transmisji bitów w HLS, Smooth Streaming i MPEG-DASH, bez ponownego pakowania w tych formatach. Korzystając z funkcji dynamicznego tworzenia pakietów, można przechowywać i opłacać pliki w formacie jednego magazynu. Usługa Media Services skompiluje oraz udostępni właściwą odpowiedź na podstawie żądania klienta.

Aby skorzystać z dynamicznego tworzenia pakietów, musisz zakodować swoje pliki źródłowe jako zestaw plików MP4 o różnych szybkościach transmisji bitów. Kroki kodowania przedstawiono w dalszej części tego artykułu.

Aby dowiedzieć się, jak skalowanie przetwarzania multimediów, zobacz [skalowanie przetwarzania za pomocą witryny Azure portal multimediów](media-services-portal-scale-media-processing.md).

## <a name="encode-in-the-azure-portal"></a>Kodowanie w witrynie Azure portal

Aby zakodować zawartość przy użyciu standardowego kodera multimediów:

1. W witrynie [Azure Portal](https://portal.azure.com/) wybierz swoje konto usługi Azure Media Services.
2. Wybierz kolejno pozycje **Ustawienia** > **Elementy zawartości**. Wybierz element zawartości, który chcesz zakodować.
3. Wybierz przycisk **Koduj**.
4. W okienku **Kodowanie elementu zawartości** wybierz procesor **Media Encoder Standard** i ustawienia wstępne. Aby uzyskać informacje o ustawieniach wstępnych, zobacz [Auto-generate a bitrate ladder](media-services-autogen-bitrate-ladder-with-mes.md) (Automatyczne generowanie drabiny szybkości transmisji bitów) i [Task presets for Media Encoder Standard](media-services-mes-presets-overview.md) (Ustawienia wstępne zadań procesora Media Encoder Standard). Ważne jest, aby wybrać ustawienia wstępne, które będą najlepiej działać dla wejściowego pliku wideo. Na przykład: jeśli wejściowy plik wideo ma rozdzielczość 1920 &#215; 1080 pikseli, można użyć ustawienia wstępnego **Wielokrotna szybkość transmisji bitów H264 1080p**. Jeśli masz wideo w niskiej rozdzielczości (640 &#215; 360), ustawienie wstępne **Wielokrotna szybkość transmisji bitów H264 1080p** nie powinno być używane.
   
   Aby ułatwić sobie zarządzanie zasobami, możesz poddać edycji nazwę wyjściowego elementu zawartości i nazwę zadania.
   
   ![Kodowanie elementów zawartości](./media/media-services-portal-vod-get-started/media-services-encode1.png)
5. Wybierz pozycję **Utwórz**.

## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Kolejne kroki
* [Monitoruj postęp zadania kodowania](media-services-portal-check-job-progress.md) w witrynie Azure portal.  

