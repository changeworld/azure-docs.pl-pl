---
title: Eksperymentalne ustawienie wstępne dla kodowania z obsługą zawartości — Azure | Microsoft Docs
description: W tym artykule omówiono kodowanie oparte na zawartości w Microsoft Azure Media Services v3.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 04/05/2019
ms.author: juliako
ms.custom: ''
ms.openlocfilehash: 9389466b6291542563c068706479bf981c5880da
ms.sourcegitcommit: 2f8ff235b1456ccfd527e07d55149e0c0f0647cc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/07/2020
ms.locfileid: "75692760"
---
# <a name="experimental-preset-for-content-aware-encoding"></a>Eksperymentalne ustawienie wstępne dla kodowania obsługującego zawartość

Aby przygotować zawartość do dostarczenia przez [szybkość przesyłania strumieniowego z adaptacyjną szybkością transmisji bitów](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming), należy zakodować wideo przy użyciu wielu szybkości transmisji bitów (wysokiej lub niskiej). W celu zapewnienia płynnej obniżenia jakości szybkość transmisji bitów jest niższa, ponieważ jest to rozdzielczość wideo. W wyniku tego jest to nazywane drabinem kodowania — tabelę rozdzielczości i szybkości transmisji bitów; Zobacz [wbudowane ustawienia wstępne kodowania](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#encodernamedpreset)Media Services.

## <a name="overview"></a>Przegląd

Zainteresowanie przenoszonym poza wstępnie ustawionym podejściem do wszystkich filmów wideo wzrosło po Netflix opublikowaniu [blogu](https://medium.com/netflix-techblog/per-title-encode-optimization-7e99442b62a2) w grudniu 2015. Od tego momentu w portalu Marketplace wydano wiele rozwiązań dla kodowania obsługującego zawartość. Zobacz [ten artykuł](https://www.streamingmedia.com/Articles/Editorial/Featured-Articles/Buyers-Guide-to-Per-Title-Encoding-130676.aspx) , aby zapoznać się z omówieniem. Pomysłem jest uwzględnienie zawartości — Aby dostosować lub dopasować drabinkę kodowania do złożoności poszczególnych filmów wideo. W każdej rozdzielczości istnieje szybkość transmisji bitów, która nie jest Perceptive — koder działa z optymalną szybkością transmisji bitów. Następnym poziomem optymalizacji jest wybranie rozwiązań opartych na zawartości — na przykład wideo prezentacji programu PowerPoint nie jest korzystne w przypadku przechodzenia poniżej 720. Następnie można wykonać zadanie kodera w celu zoptymalizowania ustawień dla każdego zrzutu w filmie wideo. Netflix to [podejście](https://medium.com/netflix-techblog/optimized-shot-based-encodes-now-streaming-4b9464204830) w 2018.

Na początku 2017 firma Microsoft udostępniła [adaptacyjne ustawienie wstępne przesyłania strumieniowego](autogen-bitrate-ladder.md) w celu rozwiązania problemu zmienności jakości i rozdzielczości źródłowych filmów wideo. Nasi klienci mają zróżnicowaną zawartość, kilka o godzinie 1080p, inne w firmie 720, a kilka w SD i niższych rozdzielczościach. Ponadto nie cała zawartość źródłowa była wysoka jakość Mezzanine z filmu lub Studios TV. Adaptacyjne ustawienie wstępne przesyłania strumieniowego eliminuje te problemy, upewniając się, że Drabinka szybkości transmisji nigdy nie przekracza rozdzielczości ani średniej szybkości transmisji bitów danych wejściowych Mezzanine.

Eksperymentalne ustawienia wstępne kodowania obsługujące zawartość rozszerzają ten mechanizm przez włączenie logiki niestandardowej, która umożliwia koderowi wyszukiwanie optymalnej szybkości transmisji bitów dla danego rozwiązania, ale bez konieczności przeprowadzania obszernej analizy obliczeniowej. Wynikiem tego jest to, że nowe ustawienie wstępne generuje dane wyjściowe, które mają mniejszą szybkość transmisji bitów niż adaptacyjne ustawienie wstępne przesyłania strumieniowego, ale przy wyższej jakości. Zapoznaj się z poniższymi przykładowymi wykresami pokazującymi porównanie przy użyciu metryk jakości, takich jak [PSNR](https://en.wikipedia.org/wiki/Peak_signal-to-noise_ratio) i [VMAF](https://en.wikipedia.org/wiki/Video_Multimethod_Assessment_Fusion). Źródło zostało utworzone przez połączenie krótkich klipów o wysokiej złożoności z filmów i programów telewizyjnych, które są przeznaczone do podkreślania kodera. Zgodnie z definicją, to ustawienie wstępne daje wyniki, które różnią się w zależności od zawartości — oznacza to również, że w przypadku niektórych zawartości może nie być znacząca Redukcja szybkości transmisji bitów lub poprawy jakości.

![Krzywa stopnia zniekształcenia (RD) przy użyciu PSNR](media/cae-experimental/msrv1.png)

**Rysunek 1: Krzywa stopnia zniekształcenia (RD) używająca metryki PSNR dla źródła o dużej złożoności**

![Krzywa stopnia zniekształcenia (RD) przy użyciu VMAF](media/cae-experimental/msrv2.png)

**Rysunek 2: Krzywa stopnia zniekształcenia (RD) używająca metryki VMAF dla źródła o dużej złożoności**

Ustawienie wstępne jest obecnie dostrojone w celu uzyskania wysokiej złożoności wideo ze źródła wysokiej jakości (filmy, programy telewizyjne). Pracuj w toku, aby dostosowywać do zawartości o niskiej złożoności (na przykład prezentacji programu PowerPoint), a także słabych filmów wideo. To ustawienie wstępne używa tego samego zestawu rozwiązań co ustawienia wstępnego przesyłania strumieniowego. Firma Microsoft pracuje nad metodami, aby wybrać minimalny zestaw rozwiązań opartych na zawartości. Poniżej przedstawiono wyniki dla innej kategorii zawartości źródłowej, w której koder był w stanie stwierdzić, że dane wejściowe były niskiej jakości (wiele artefaktów kompresji z powodu niskiej szybkości transmisji bitów). Należy zauważyć, że w przypadku eksperymentalnego ustawienia wstępnego koder zdecydował się utworzyć tylko jedną warstwę wyjściową — z niską szybkością transmisji bitów, dzięki czemu większość klientów będzie mogła odtworzyć strumień bez parkingów.

![Krzywa usług pulpitu zdalnego przy użyciu PSNR](media/cae-experimental/msrv3.png)

**Rysunek 3: Krzywa usług pulpitu zdalnego wykorzystująca PSNR dla danych wejściowych o niskiej jakości (o godzinie 1080p)**

![Krzywa usług pulpitu zdalnego przy użyciu VMAF](media/cae-experimental/msrv4.png)

**Rysunek 4: Krzywa usług pulpitu zdalnego wykorzystująca VMAF dla danych wejściowych o niskiej jakości (o godzinie 1080p)**

## <a name="use-the-experimental-preset"></a>Użyj eksperymentalnego ustawienia wstępnego

Można tworzyć przekształcenia, które używają tego ustawienia wstępnego w następujący sposób. W przypadku korzystania z [tego](stream-files-tutorial-with-api.md)samouczka można zaktualizować kod w następujący sposób:

```csharp
TransformOutput[] output = new TransformOutput[]
{
   new TransformOutput
   {
      // The preset for the Transform is set to one of Media Services built-in sample presets.
      // You can customize the encoding settings by changing this to use "StandardEncoderPreset" class.
      Preset = new BuiltInStandardEncoderPreset()
      {
         // This sample uses the new experimental preset for content-aware encoding
         PresetName = EncoderNamedPreset.ContentAwareEncodingExperimental
      }
   }
};
```

> [!NOTE]
> Prefiks "eksperymentalny" jest używany w tym miejscu do sygnalizowania, że nadal trwa rozwój podstawowych algorytmów. W miarę upływu czasu do logiki używanej do generowania drabin szybkości transmisji bitów można wprowadzać zmiany, a celem jest zbieżność algorytmu, który jest niezawodny i który dostosowuje się do szerokiej gamy warunków wprowadzania. Zadania kodowania korzystające z tego ustawienia wstępnego będą nadal rozliczane na podstawie minut danych wyjściowych, a element zawartości wyjściowej można dostarczyć z naszych punktów końcowych przesyłania strumieniowego w protokołach, takich jak ŁĄCZNIKi i HLS.

## <a name="next-steps"></a>Następne kroki

Po poznaniu tej nowej opcji optymalizacji filmów wideo Zapraszamy Cię do wypróbowania. Możesz wysłać nam swoją opinię przy użyciu linków na końcu tego artykułu lub zaangażować nas bezpośrednio w <amsved@microsoft.com>.
