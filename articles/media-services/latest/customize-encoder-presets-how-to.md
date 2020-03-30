---
title: Kodowanie transformacji niestandardowej przy użyciu usługi Media Services w wersji 3 .NET — Azure | Dokumenty firmy Microsoft
description: W tym temacie pokazano, jak używać usługi Azure Media Services w wersji 3 do kodowania transformacji niestandardowej przy użyciu platformy .NET.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 05/03/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: ebe701032e6416b3e007a28db62f5a8235bb1bb1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80068045"
---
# <a name="how-to-encode-with-a-custom-transform---net"></a>Jak zakodować za pomocą niestandardowego przekształcenia - .NET

Podczas kodowania za pomocą usługi Azure Media Services można szybko rozpocząć pracę z jednym z zalecanych wbudowanych ustawień wstępnych na podstawie najlepszych rozwiązań branżowych, jak pokazano w samouczku [pliki przesyłania strumieniowego.](stream-files-tutorial-with-api.md) Można również utworzyć niestandardowe ustawienia predefiniowane, aby kierować określone wymagania dotyczące scenariusza lub urządzenia.

## <a name="considerations"></a>Zagadnienia do rozważenia

Podczas tworzenia niestandardowych ustawień predefiniowanych stosuje się następujące zagadnienia:

* Wszystkie wartości wysokości i szerokości zawartości AVC muszą być wielokrotnością 4.
* W usłudze Azure Media Services w wersji 3 wszystkie kodowanie bitów są w bitach na sekundę. Różni się to od ustawień wstępnych w naszych interfejsach API v2, które używały kilobitów na sekundę jako jednostki. Na przykład jeśli szybkość transmisji bitów w wersji 2 została określona jako 128 (kilobitów/sekundę), w wersji 3 zostanie ustawiona na 128000 (bity/sekundę).

## <a name="prerequisites"></a>Wymagania wstępne 

[Tworzenie konta usługi Media Services](create-account-cli-how-to.md)

## <a name="download-the-sample"></a>Pobierz przykład

Sklonuj repozytorium GitHub zawierające pełne próbki rdzenia .NET na komputerze za pomocą następującego polecenia:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials.git
 ```
 
Próbka niestandardowego ustawienia wstępnego znajduje się w folderze [EncodeCustomTransform.](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/EncodeCustomTransform/)

## <a name="create-a-transform-with-a-custom-preset"></a>Tworzenie transformacji przy niestandardowych ustawieniach predefiniowanych 

Podczas tworzenia nowego [przekształcenia](https://docs.microsoft.com/rest/api/media/transforms)należy określić, co ma być wytwarzane jako dane wyjściowe. Wymagany parametr to obiekt [TransformOutput](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#transformoutput), jak pokazano w poniższym kodzie. Każdy obiekt **TransformOutput** zawiera element **Preset**. **Predefiniowane opisuje** instrukcje krok po kroku operacji przetwarzania wideo i/lub audio, które mają być używane do generowania żądanego **TransformOutput**. Następujące **TransformOutput** tworzy niestandardowe ustawienia kodeka i danych wyjściowych warstwy.

Podczas tworzenia obiektu [Transform](https://docs.microsoft.com/rest/api/media/transforms) należy najpierw sprawdzić, czy taki obiekt już istnieje, używając metody **Get**, tak jak przedstawiono w poniższym kodzie. W umiań Media Services w wersji 3 **metody Get** dla jednostek zwracają **wartość null,** jeśli jednostka nie istnieje (sprawdzanie nazwy bez uwzględniania wielkości liter).

### <a name="example"></a>Przykład

Poniższy przykład definiuje zestaw wyjść, które mają być generowane, gdy to Transform jest używany. Najpierw dodajemy warstwę AacAudio do kodowania audio i dwie warstwy H264Video do kodowania wideo. W warstwach wideo przypisujemy etykiety, aby mogły być używane w nazwach plików wyjściowych. Następnie chcemy, aby dane wyjściowe zawierały również miniatury. W poniższym przykładzie określamy obrazy w formacie PNG, generowane w 50% rozdzielczości wejściowego wideo i przy trzech sygnaturach czasowych — {25%, 50%, 75} długości wejściowego wideo. Na koniec określamy format plików wyjściowych - jeden dla wideo + audio, a drugi dla miniatur. Ponieważ mamy wiele H264Layers, musimy użyć makr, które produkują unikatowe nazwy na warstwę. Możemy użyć `{Label}` lub `{Bitrate}` makro, przykład pokazuje pierwszy.

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/EncodeCustomTransform/MediaV3ConsoleApp/Program.cs#EnsureTransformExists)]

## <a name="next-steps"></a>Następne kroki

[Przesyłanie strumieniowe plików](stream-files-tutorial-with-api.md) 
