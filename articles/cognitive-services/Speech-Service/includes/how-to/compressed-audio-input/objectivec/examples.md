---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: dapine
ms.openlocfilehash: 788903d802ca47c763734e7cf6ddbbf3b0032203
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/31/2020
ms.locfileid: "80409615"
---
Aby przesyłać strumieniowo w skompresowanym `SPXPullAudioInputStream` formacie `SPXPushAudioInputStream`audio do usługi Mowa, utwórz lub .

Poniższy fragment kodu pokazuje, jak `SPXAudioConfiguration` utworzyć z `SPXPushAudioInputStream`wystąpienia , określając MP3 jako format kompresji strumienia.

[!code-objectivec[Set up the input stream](~/samples-cognitive-services-speech-sdk/samples/objective-c/ios/compressed-streams/CompressedStreamsSample/CompressedStreamsSample/ViewController.m?range=67-76&highlight=2-11)]

Następny fragment kodu pokazuje, jak skompresowane dane audio mogą być `SPXPushAudioInputStream`odczytywane z pliku i pompowane do pliku .

[!code-objectivec[Push compressed audio data into the stream](~/samples-cognitive-services-speech-sdk/samples/objective-c/ios/compressed-streams/CompressedStreamsSample/CompressedStreamsSample/ViewController.m?range=106-150&highlight=19-44)]
