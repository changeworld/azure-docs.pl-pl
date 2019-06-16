---
title: Kodowanie niestandardowej transformacji za pomocą usługi Media Services v3 .NET — Azure | Dokumentacja firmy Microsoft
description: W tym temacie pokazano, jak kodować niestandardowe przekształcenia przy użyciu platformy .NET za pomocą usługi Azure Media Services v3.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.custom: seodec18
ms.date: 05/03/2019
ms.author: juliako
ms.openlocfilehash: 2167a74dc81bdbb2562211cf5c0195a755941d9d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65148336"
---
# <a name="how-to-encode-with-a-custom-transform---net"></a>Jak kodowanie za pomocą niestandardowej transformacji — .NET

Podczas kodowania za pomocą usługi Azure Media Services, należy można szybko rozpocząć pracę z jedną z zalecanych wbudowane ustawienia wstępne, oparte na najlepszych branżowych rozwiązań, jak pokazano w [strumieniowego przesyłania plików](stream-files-tutorial-with-api.md) samouczka. Możesz również tworzyć niestandardowe ustawienie wstępne pod kątem określonych wymagań scenariusza lub urządzenia.

## <a name="considerations"></a>Zagadnienia do rozważenia

Podczas tworzenia niestandardowych ustawień wstępnych należy uwzględnić następujące kwestie:

* Wszystkie wartości dla wysokość i szerokość AVC zawartości musi być wielokrotnością liczby 4.
* W usłudze Azure Media Services v3 są wszystkie kodowania szybkości transmisji w bitach na sekundę. To różni się od ustawień wstępnych przy użyciu interfejsów API w wersji 2, które używane kilobitów na sekundę jako jednostka. Na przykład jeśli szybkość transmisji bitów w wersji 2 została określona jako 128 (kilobity/sekundę), w wersji 3 go będzie miał ustawienie 128000 (bity/sekundę).

## <a name="prerequisites"></a>Wymagania wstępne 

[Utwórz konto usługi Media Services](create-account-cli-how-to.md)

## <a name="download-the-sample"></a>Pobierz przykład

Sklonuj repozytorium GitHub, który zawiera pełny przykład platformy .NET Core do komputera za pomocą następującego polecenia:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials.git
 ```
 
Niestandardowe ustawienia wstępnego przykładowe znajduje się w [EncodeCustomTransform](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/EncodeCustomTransform/) folderu.

## <a name="create-a-transform-with-a-custom-preset"></a>Tworzenie przekształcenia przy użyciu niestandardowego ustawienia wstępnego 

Podczas tworzenia nowego [Przekształcanie](https://docs.microsoft.com/rest/api/media/transforms), należy określić, co chcesz, aby wygenerować jako dane wyjściowe. Wymagany parametr to obiekt [TransformOutput](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#transformoutput), jak pokazano w poniższym kodzie. Każdy obiekt **TransformOutput** zawiera element **Preset**. **Ustawienie wstępne** w tym artykule opisano krok po kroku instrukcje operacji przetwarzania wideo lub audio, które mają być używane do generowania żądaną **TransformOutput**. Następujące **TransformOutput** tworzy niestandardowe ustawienia koderów-dekoderów i warstwy danych wyjściowych.

Podczas tworzenia obiektu [Transform](https://docs.microsoft.com/rest/api/media/transforms) należy najpierw sprawdzić, czy taki obiekt już istnieje, używając metody **Get**, tak jak przedstawiono w poniższym kodzie. W Media Services v3 **uzyskać** metody na jednostkach zwracają **null** Jeśli jednostka nie istnieje (bez uwzględniania wielkości liter Sprawdź nazwy).

### <a name="example"></a>Przykład

Poniższy przykład definiuje zestaw danych wyjściowych, które ma zostać wygenerowane podczas tej transformacji jest używany. Najpierw dodamy warstwy AacAudio audio kodowania i dwie warstwy H264Video dla kodowania wideo. W warstwach wideo firma Microsoft przypisać etykiety, dzięki czemu mogą być używane w nazwach plików danych wyjściowych. Następnie chcemy, aby dane wyjściowe, aby obejmować miniatury. W poniższym przykładzie określamy obrazy w formacie PNG, generowane w wysokości 50% rozdzielczość wejściowy plik wideo i w trzech sygnatury czasowe — {25%, 50%, 75} długości wejściowego filmu wideo. Ponadto firma Microsoft służy do określania pliki wyjściowe — jedno dla wideo i audio, a drugi dla miniatur. Ponieważ firma Microsoft ma wielu H264Layers, musimy użyć makra, które generują unikatowych nazw na warstwę. Możemy użyć `{Label}` lub `{Bitrate}` makra w przykładzie pokazano pierwszych.

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/EncodeCustomTransform/MediaV3ConsoleApp/Program.cs#EnsureTransformExists)]

## <a name="next-steps"></a>Kolejne kroki

[Przesyłanie strumieniowe plików](stream-files-tutorial-with-api.md) 
