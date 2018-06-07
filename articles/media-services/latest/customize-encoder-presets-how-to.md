---
title: Kodowanie niestandardowej transformacji przy użyciu usługi Azure Media Services w wersji 3 | Dokumentacja firmy Microsoft
description: W tym temacie pokazano, jak używać usługi Azure Media Services w wersji 3 do kodowania przekształcenie niestandardowe.
services: media-services
documentationcenter: ''
author: Juliako
manager: cflower
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.custom: ''
ms.date: 05/17/2018
ms.author: juliako
ms.openlocfilehash: d298070877a366d04b2df1ef8ac63b08f8771de0
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34655672"
---
# <a name="how-to-encode-with-a-custom-transform"></a>Sposób kodowania z przekształcenie niestandardowe

Podczas kodowania w usłudze Azure Media Services, musisz szybkie rozpoczęcie pracy z jednym zalecanych ustawień wbudowanych oparte na najlepsze rozwiązania w branży, jak pokazano w [strumieniowego przesyłania plików](stream-files-tutorial-with-api.md) samouczek lub można wybrać do utworzenia niestandardowego Ustawienie wstępne pod kątem określonych wymagań scenariusza lub urządzenia. 

> [!Note]
> W usłudze Azure Media Services w wersji 3 są wszystkie kodowania szybkości transmisji bitów w bitów na sekundę. To jest inny niż v2 REST, które ustawienia standardu Media Encoder Standard. Na przykład szybkości transmisji bitów w wersji 2 zostałaby określona jako 128, ale w wersji 3 byłoby 128000.

## <a name="download-the-sample"></a>Pobierz przykład

Klonowanie repozytorium GitHub, który zawiera pełny przykład .NET Core do komputera za pomocą następującego polecenia:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials.git
 ```
 
Niestandardowe próbki predefiniowanych znajduje się w [EncodeCustomTransform](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/EncodeCustomTransform/) folderu.

## <a name="create-a-transform-with-a-custom-preset"></a>Utworzyć transformację z animacji niestandardowej 

Podczas tworzenia nowego [przekształcenie](https://docs.microsoft.com/rest/api/media/transforms), należy określić, co ma być jako dane wyjściowe. Wymagany parametr to obiekt **TransformOutput**, jak pokazano w poniższym kodzie. Każdy obiekt **TransformOutput** zawiera element **Preset**. Element **Preset** zawiera szczegółowe instrukcje operacji przetwarzania wideo i/lub dźwięku używanych do wygenerowania wymaganego obiektu **TransformOutput**. Następujące **TransformOutput** tworzy niestandardowe ustawienia wyjściowej koderów-dekoderów i warstwy.

Podczas tworzenia obiektu [Transform](https://docs.microsoft.com/rest/api/media/transforms) należy najpierw sprawdzić, czy taki obiekt już istnieje, używając metody **Get**, tak jak przedstawiono w poniższym kodzie.  W usługi Media Services w wersji 3 **uzyskać** metody na jednostkach zwracają **null** Jeśli jednostka nie istnieje (bez uwzględniania wielkości liter Sprawdź nazwy).

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/EncodeCustomTransform/MediaV3ConsoleApp/Program.cs#EnsureTransformExists)]

## <a name="next-steps"></a>Kolejne kroki

[Przesyłanie strumieniowe plików](stream-files-tutorial-with-api.md) 
