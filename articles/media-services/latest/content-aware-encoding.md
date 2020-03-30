---
title: Predefiniowane kodowanie z uwzględnieniem zawartości — Usługa Azure Media Services
description: W tym artykule omówiono kodowanie uwzględniające zawartość w usłudze Microsoft Azure Media Services w wersji 3.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 01/24/2020
ms.author: juliako
ms.custom: ''
ms.openlocfilehash: 3ea6c4226a59ba020a477cc5811033ff3dc3c2e9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76772082"
---
# <a name="use-the-content-aware-encoding-preset-to-find-the-optimal-bitrate-value-for-a-given-resolution"></a>Użyj ustawienia wstępnego kodowania uwzględniającego zawartość, aby znaleźć optymalną wartość szybkości transmisji bitów dla danej rozdzielczości

Aby przygotować zawartość do dostarczenia za pomocą [adaptacyjnego przesyłania strumieniowego bitrate,](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming)wideo musi być kodowane z wieloma szybkościami transmisji bitów (od najwyższej do najniższej). Zapewnia to wdzięku degradacji jakości, jak bitrate jest obniżona, więc jest rozdzielczość wideo. Takie wielokrotne kodowanie szybkości transmisji bitów korzysta z tak zwanej drabiny kodowania – tabeli rozdzielczości i szybkości transmisji bitów, zobacz [wbudowane ustawienia kodowania](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#encodernamedpreset)media services.

Należy pamiętać o zawartości, którą przetwarzasz i dostosować / dostroić drabinę kodowania do złożoności poszczególnych wideo. W każdej rozdzielczości występuje szybkość transmisji bitów, po przekroczeniu której jakikolwiek wzrost jakości nie jest spostrzegawczy – koder działa przy tej optymalnej wartości bitrate. Kolejnym poziomem optymalizacji jest wybranie rozdzielczości na podstawie zawartości — na przykład wideo przedstawiające prezentację programu PowerPoint nie korzysta z przechodzenia poniżej 720p. Idąc dalej, koder może być zadaniem optymalizacji ustawień dla każdego zdjęcia w filmie. 

Predefiniowane ustawienia [adaptacyjnego przesyłania strumieniowego](autogen-bitrate-ladder.md) firmy Microsoft częściowo rozwiązują problem zmienności jakości i rozdzielczości źródłowych filmów wideo. Nasi klienci mają zróżnicowaną mieszankę treści, niektóre w rozdzielczości 1080p, inne w rozdzielczości 720p, a kilka w SD i niższych rozdzielczościach. Co więcej, nie wszystkie treści źródłowe to wysokiej jakości antresole ze studiów filmowych lub telewizyjnych. Predefiniowane ustawienia adaptacyjnego przesyłania strumieniowego rozstrzygną te problemy, zapewniając, że drabina szybkości transmisji bitów nigdy nie przekracza rozdzielczości ani średniej szybkości transmisji bitów antresoli wejściowej. Jednak to ustawienie predefiniowane nie sprawdza właściwości źródłowych innych niż rozdzielczość i szybkość transmisji bitów.

## <a name="the-content-aware-encoding"></a>Kodowanie uwzględniające zawartość 

Predefiniowane kodowanie z uwzględnieniem zawartości rozszerza mechanizm "adaptacyjnego przesyłania strumieniowego szybkości transmisji bitów", wprowadzając niestandardową logikę, która umożliwia koderowi uzyskanie optymalnej wartości bitrate dla danej rozdzielczości, ale bez konieczności szczegółowej analizy obliczeniowej. To ustawienie wstępne tworzy zestaw plików MP4 wyrównanych do GOP. Biorąc pod uwagę wszelkie zawartości wejściowej, usługa wykonuje wstępną lekką analizę zawartości wejściowej i używa wyników do określenia optymalnej liczby warstw, odpowiedniej szybkości transmisji bitów i ustawień rozdzielczości dla dostarczania przez adaptacyjne przesyłanie strumieniowe. To ustawienie predefiniowane jest szczególnie skuteczne w przypadku filmów o niskiej i średniej złożoności, gdzie pliki wyjściowe będą miały niższą szybkość transmisji bitów niż predefiniowane ustawienia Adaptive Streaming, ale w jakości, która nadal zapewnia widzom dobre wrażenia. Wyjście będzie zawierać pliki MP4 z wideo i audio przeplatane

Poniższe przykładowe wykresy przedstawiają porównanie przy użyciu wskaźników jakości, takich jak [PSNR](https://en.wikipedia.org/wiki/Peak_signal-to-noise_ratio) i [VMAF](https://en.wikipedia.org/wiki/Video_Multimethod_Assessment_Fusion). Źródło zostało stworzone przez łączenie krótkich klipów o dużej złożoności zdjęć z filmów i programów telewizyjnych, mających na celu podkreślenie kodera. Z definicji to ustawienie wstępne daje wyniki, które różnią się od zawartości do zawartości — oznacza to również, że dla niektórych treści, nie może być znaczne zmniejszenie szybkości transmisji bitów lub poprawy jakości.

![Krzywa zniekształceń szybkości (RD) za pomocą PSNR](media/content-aware-encoding/msrv1.png)

**Rysunek 1: Krzywa zniekształceń szybkości (RD) przy użyciu metryki PSNR dla źródła o wysokiej złożoności**

![Krzywa zniekształceń szybkości (RD) przy użyciu vmaf](media/content-aware-encoding/msrv2.png)

**Rysunek 2: Krzywa zniekształceń szybkości (RD) przy użyciu metryki VMAF dla źródła wysokiej złożoności**

Poniżej znajdują się wyniki dla innej kategorii zawartości źródłowej, gdzie koder był w stanie ustalić, że dane wejściowe były niskiej jakości (wiele artefaktów kompresji ze względu na niską szybkość transmisji bitów). Należy zauważyć, że z ustawieniami predefiniowanych obsługujących zawartość, koder postanowił wyprodukować tylko jedną warstwę wyjściową — przy wystarczająco niskiej szybkości transmisji bitów, aby większość klientów mogła odtwarzać strumień bez blokowania.

![Krzywa rd przy użyciu PSNR](media/content-aware-encoding/msrv3.png)

**Rysunek 3: Krzywa RD wykorzystująca PSNR dla wejścia niskiej jakości (w 1080p)**

![Krzywa pulpitu zdalnego przy użyciu vmaf](media/content-aware-encoding/msrv4.png)

**Rysunek 4: Krzywa rd przy użyciu VMAF dla niskiej jakości wejścia (w 1080p)**

## <a name="how-to-use-the-content-aware-encoding-preset"></a>Jak korzystać z ustawień predefiniowanych kodowania obsługujących zawartość 

Można tworzyć przekształcenia, które używają tego ustawienia predefiniowane w następujący sposób. 

> [!TIP]
> Zobacz [Sekcję Następne kroki,](#next-steps) aby uzyskać samouczki, które używają tranform outputs. Zasób wyjściowy może być dostarczany z punktów końcowych przesyłania strumieniowego usługi Media Services w protokołach takich jak MPEG-DASH i HLS (jak pokazano w samouczkach).


```csharp
TransformOutput[] output = new TransformOutput[]
{
   new TransformOutput
   {
      // The preset for the Transform is set to one of Media Services built-in sample presets.
      // You can customize the encoding settings by changing this to use "StandardEncoderPreset" class.
      Preset = new BuiltInStandardEncoderPreset()
      {
         // This sample uses the new preset for content-aware encoding
         PresetName = EncoderNamedPreset.ContentAwareEncoding
      }
   }
};
```

> [!NOTE]
> Zadania kodowania `ContentAwareEncoding` przy użyciu ustawień predefiniowanych są rozliczane na podstawie minut wyjściowych. 

## <a name="next-steps"></a>Następne kroki

* [Samouczek: Przesyłanie, kodowanie i przesyłanie strumieniowe filmów za pomocą usługi Media Services w wersji 3](stream-files-tutorial-with-api.md)
* [Samouczek: Kodowanie zdalnego pliku na podstawie adresu URL i przesyłanie strumieniowe wideo - REST](stream-files-tutorial-with-rest.md)
* [Samouczek: Kodowanie zdalnego pliku na podstawie adresu URL i przesyłanie strumieniowe wideo - CLI](stream-files-cli-quickstart.md)
* [Samouczek: Kodowanie zdalnego pliku na podstawie adresu URL i przesyłanie strumieniowe wideo - .NET](stream-files-dotnet-quickstart.md)
* [Samouczek: Kodowanie zdalnego pliku na podstawie adresu URL i przesyłanie strumieniowe wideo - Node.js](stream-files-nodejs-quickstart.md)
