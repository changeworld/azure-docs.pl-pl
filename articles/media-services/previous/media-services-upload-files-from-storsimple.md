---
title: Przekazywanie plików na konto usługi Azure Media Services z rozwiązania Azure StorSimple | Microsoft Docs
description: Ten artykuł zawiera krótkie omówienie usługi Azure StorSimple Data Manager. Artykuł zawiera również linki do samouczków, które przedstawiają sposób wyodrębniania danych z rozwiązania StorSimple i przekazywania ich jako elementów zawartości na konto usługi Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 1dd09328-262b-43ef-8099-73241b49a925
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: 8a2972b8fbc31a3f945f40949128b3f37f2f4b77
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60544953"
---
# <a name="upload-files-into-an-azure-media-services-account-from-azure-storsimple"></a>Przekazywanie plików na konto usługi Azure Media Services z rozwiązania Azure StorSimple  

Ten artykuł zawiera krótkie omówienie usługi Azure StorSimple Data Manager. Artykuł zawiera również linki do samouczków, które przedstawiają sposób wyodrębniania danych z rozwiązania StorSimple i przekazywania ich jako elementów zawartości na konto usługi Azure Media Services (AMS).

> 
> [!NOTE]
> Usługa Azure StorSimple Data Manager jest obecnie dostępna w prywatnej wersji zapoznawczej. 
> 

## <a name="overview"></a>Omówienie

Za pomocą usługi Media Services można przekazać pliki cyfrowe do elementu zawartości. Element zawartości może zawierać pliki wideo, audio, obrazy, kolekcje miniatur, ścieżki tekstowe i pliki napisów (oraz metadane dotyczące tych plików). Po przekazaniu plików zawartość jest bezpiecznie przechowywana w chmurze, na potrzeby dalszego przetwarzania i przesyłania strumieniowego.

Usługa [Azure StorSimple](https://docs.microsoft.com/azure/storsimple/) używa magazynu w chmurze jako rozszerzenia rozwiązania lokalnego i automatycznie tworzy warstwy danych w magazynie lokalnym oraz magazynie w chmurze. Urządzenie StorSimple usuwa duplikaty i kompresuje dane przed wysłaniem ich do chmury, dzięki czemu wysyłanie dużych plików do chmury staje się bardzo wydajne. Usługa [StorSimple Data Manager](../../storsimple/storsimple-data-manager-overview.md) oferuje interfejsy API, które umożliwiają wyodrębnianie danych z rozwiązania StorSimple i przedstawianie ich w postaci elementów zawartości usługi AMS.

## <a name="get-started"></a>Rozpoczęcie pracy

1. [Utwórz konto usługi Media Services](media-services-portal-create-account.md) , do którego chcesz przenieść elementy zawartości.
2. Utwórz nowe konto wersji zapoznawczej usługi Data Manager, zgodnie z opisem w artykule [StorSimple Data Manager](../../storsimple/storsimple-data-manager-overview.md).
3. Utwórz konto usługi StorSimple Data Manager.
4. Utwórz zadanie przekształcania danych, które po uruchomieniu wyodrębnia dane z urządzenia StorSimple i przekazuje je na konto usługi AMS jako elementy zawartości. 

    Gdy zadanie zacznie działać, zostanie utworzona kolejka magazynu. Jest ona wypełniana przy użyciu komunikatów dotyczących przekształconych obiektów blob, gdy będą gotowe. Nazwa tej kolejki jest taka sama jak nazwa definicji zadania. Za pomocą tej kolejki można określić, czy element zawartości jest gotowy i wywołać żądaną operację usługi Media Services do uruchomienia w jego obrębie. Tej kolejki można na przykład użyć do wyzwalania funkcji Azure Function, która zawiera niezbędny kod usługi Media Services.

## <a name="see-also"></a>Zobacz także

[Za pomocą zestawu .NET SDK wyzwalanie zadań menedżera danych](../../storsimple/storsimple-data-manager-dotnet-jobs.md)

## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Kolejne kroki

Teraz możesz zakodować przekazane elementy zawartości. Więcej informacji znajduje się na stronie [Kodowanie elementów zawartości](media-services-portal-encode.md).
