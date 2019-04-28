---
title: Język znaczników synteza mowy (SSML) — usługi mowy
titleSuffix: Azure Cognitive Services
description: W celu kontrolowania Wymowa i prosody w zamiany tekstu na mowę, przy użyciu Markup Language synteza mowy.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/13/2018
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: 57fc7e699d88dbe777750e3acdb7f96794b66fc0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61460290"
---
# <a name="speech-synthesis-markup-language-ssml"></a>Język znaczników syntezy mowy (SSML)

Mowy syntezy Markup Language (SSML) to język znaczników oparty na formacie XML, który umożliwia kontrolowanie Wymowa i *prosody* z zamiany tekstu na mowę. Prosody odwołuje się do rytm wydawania i wysokość mowy — muzyka, jeśli chcesz. Możesz fonetycznie Podaj słowa, zapewniają wskazówki do interpretacji liczb, wstawianie wstrzymuje, wysokość formantu, wolumin i szybkość i więcej. Aby uzyskać więcej informacji, zobacz [mowy syntezy Markup Language (SSML) w wersji 1.0](https://www.w3.org/TR/2009/REC-speech-synthesis-20090303/).

Aby uzyskać pełną listę obsługiwanych języków, ustawień regionalnych i głosów (neuronowych i standardowa), zobacz [języki](language-support.md#text-to-speech).

Poniższe sekcje zawierają przykłady typowych rozpoznawania mowy, syntezy zadania.

## <a name="add-a-break"></a>Dodaj podział
```xml
<speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
<voice  name='Microsoft Server Speech Text to Speech Voice (en-US, Jessa24kRUS)'>
    Welcome to Microsoft Cognitive Services <break time="100ms" /> Text-to-Speech API.
</voice> </speak>
```

## <a name="change-speaking-rate"></a>Zmień częstotliwość wypowiedzi

Wypowiedzi współczynnik można zastosować do standardowego głosów na poziomie zdania lub word. Wypowiedzi współczynnik mogą być stosowane tylko do neuronowych głosów na poziomie pojedynczych zdań.

```xml
<speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
<voice  name='Microsoft Server Speech Text to Speech Voice (en-US, Guy24kRUS)'>
<prosody rate="+30.00%">
    Welcome to Microsoft Cognitive Services Text-to-Speech API.
</prosody></voice> </speak>
```

## <a name="pronunciation"></a>Wymowa
```xml
<speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
<voice  name='Microsoft Server Speech Text to Speech Voice (en-US, Jessa24kRUS)'>
    <phoneme alphabet="ipa" ph="t&#x259;mei&#x325;&#x27E;ou&#x325;"> tomato </phoneme>
</voice> </speak>
```

## <a name="change-volume"></a>Zmiany woluminu

Można zastosować zmian woluminu do standardowego głosów na poziomie zdania lub word. Zmiany głośności mogą być stosowane tylko do neuronowych głosów na poziomie pojedynczych zdań.

```xml
<speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
<voice  name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'>
<prosody volume="+20.00%">
    Welcome to Microsoft Cognitive Services Text-to-Speech API.
</prosody></voice> </speak>
```

## <a name="change-pitch"></a>Zmień wysokość

Można zastosować zmian pomysłu do standardowego głosów na poziomie zdania lub word. Gęstość zmiany mogą być stosowane tylko do neuronowych głosów na poziomie pojedynczych zdań.

```xml
<speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
    <voice  name='Microsoft Server Speech Text to Speech Voice (en-US, Guy24kRUS)'>
    Welcome to <prosody pitch="high">Microsoft Cognitive Services Text-to-Speech API.</prosody>
</voice> </speak>
```

## <a name="change-pitch-contour"></a>Zmiana wysokości konturu

> [!IMPORTANT]
> Gęstość konturu zmiany nie są obsługiwane przy użyciu głosów neuronowych.

```xml
<speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
<voice  name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'>
<prosody contour="(80%,+20%) (90%,+30%)" >
    Good morning.
</prosody></voice> </speak>
```

## <a name="use-multiple-voices"></a>Użyj wielu głosów
```xml
<speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
<voice  name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'>
    Good morning!
</voice>
<voice  name='Microsoft Server Speech Text to Speech Voice (en-US, Guy24kRUS)'>
    Good morning to you too Jessa!
</voice> </speak>
```

## <a name="next-steps"></a>Kolejne kroki

* [Obsługa języków: głosów, ustawień regionalnych, języków](language-support.md)
