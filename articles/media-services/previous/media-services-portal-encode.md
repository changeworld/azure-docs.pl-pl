---
title: Kodowanie elementu zawartości przy użyciu standardu Media Encoder Standard w portalu Azure | Dokumentacja firmy Microsoft
description: Ten samouczek przedstawia kroki kodowania zawartości przy użyciu standardu Media Encoder Standard w portalu Azure.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.assetid: 107d9e9a-71e9-43e5-b17c-6e00983aceab
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/07/2017
ms.author: juliako
ms.openlocfilehash: 470eb8613416f441c1becee628acf3c898591c84
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
ms.locfileid: "33790223"
---
# <a name="encode-an-asset-by-using-media-encoder-standard-in-the-azure-portal"></a>Kodowanie elementu zawartości przy użyciu standardu Media Encoder Standard w portalu Azure

> [!NOTE]
> Do ukończenia tego samouczka jest potrzebne konto platformy Azure. Aby uzyskać szczegółowe informacje, zobacz [Bezpłatna wersja próbna systemu Azure](https://azure.microsoft.com/pricing/free-trial/). 
> 
> 

Jednym z najbardziej typowych scenariuszy w pracy z usługą Azure Media Services jest dostarczanie klientom usługi przesyłania strumieniowego adaptacyjną szybkością transmisji bitów. Usługa Media Services obsługuje następujące technologie przesyłania strumieniowego adaptacyjnej szybkości bitowej: Apple HTTP Live Streaming (HLS), Smooth Streaming firmy Microsoft i dynamicznych adaptacyjne przesyłanie strumieniowe za pośrednictwem protokołu HTTP (kreska, nazywany również MPEG-DASH). Aby przygotować pliki wideo do przesyłania strumieniowego o adaptacyjnej szybkości bitowej, najpierw zakodować źródłowy plik wideo jako pliki wielokrotnej szybkości transmisji bitów. Azure Media Encoder Standard umożliwia kodowania plików wideo.  

Usługa Media Services udostępnia funkcję dynamicznego tworzenia pakietów. Dzięki funkcji dynamicznego tworzenia pakietów, można dostarczać MP4s Twojego wielokrotnej szybkości transmisji bitów w HLS, Smooth Streaming, MPEG-DASH, bez ponowne utworzenie pakietu w tych formatach. Korzystając z dynamicznego tworzenia pakietów, można przechowywać i opłacać pliki w formacie jednego magazynu. Usługa Media Services kompilacje i służy właściwą odpowiedź na podstawie żądania klienta.

Aby skorzystać z dynamicznego tworzenia pakietów, musisz zakodować swoje pliki źródłowe jako zestaw plików MP4 o różnych szybkościach transmisji bitów. Kroki kodowania przedstawiono w dalszej części tego artykułu.

Informacje na temat skalowania przetwarzania nośnika, zobacz [skalowanie przetwarzania przy użyciu portalu Azure media](media-services-portal-scale-media-processing.md).

## <a name="encode-in-the-azure-portal"></a>Kodowanie w portalu Azure

W celu zakodowania zawartości przy użyciu standardu Media Encoder Standard:

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
* [Monitorować postęp zadania kodowania](media-services-portal-check-job-progress.md) w portalu Azure.  

