---
title: Eksperymentalne ustawienie wstępne kodowania zawartości — Azure | Dokumentacja firmy Microsoft
description: W tym artykule omówiono kodowanie zawartości w usłudze Azure Media Services
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
ms.openlocfilehash: ddb7bfd2437af806c8db75068c50545e69867ea0
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2019
ms.locfileid: "65151019"
---
# <a name="experimental-preset-for-content-aware-encoding"></a>Eksperymentalne ustawienie wstępne kodowania zawartości

Aby przygotować zawartość do przekazania przez [streaming z adaptacyjną szybkością transmisji bitów](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming), wideo, musi być zakodowany na wielu — szybkości transmisji bitów (wysoka, niski). Aby zapewnić bezpieczne pogorszenia się jakości, ponieważ szybkość transmisji bitów jest obniżony więc rozdzielczości wideo. Skutkuje to tak zwane drabiny kodowania — spis rozdzielczości i szybkości transmisji; Zapoznaj się z usługami Media [wbudowane ustawienia wstępne kodowania](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#encodernamedpreset).

## <a name="overview"></a>Omówienie

Zwiększyć zainteresowanie więcej niż podejście jeden — ustawienie wstępne — uniwersalna, odpowiednia wszelkie-filmów wideo, po opublikowaniu Netflix ich [blogu](https://medium.com/netflix-techblog/per-title-encode-optimization-7e99442b62a2) w grudniu 2015. Od tamtej pory wiele rozwiązań do kodowania zawartości opublikowane w portalu marketplace; zobacz [w tym artykule](https://www.streamingmedia.com/Articles/Editorial/Featured-Articles/Buyers-Guide-to-Per-Title-Encoding-130676.aspx) omówienie. Chodzi o to pod uwagę zawartości — Aby dostosować lub dostosowywanie kodowania drabiny do złożoności poszczególnych wideo. W każdej rozdzielczości Brak transmisji bitów, po przekroczeniu których wzrost jakości nie jest perceptive — koder operuje na tę wartość optymalne szybkości transmisji bitów. Następny poziom optymalizacji jest wybranie rozwiązania na podstawie zawartości — na przykład wideo z prezentacją programu PowerPoint nie korzysta z przejściem poniżej 720p. Idąc dalej, kodera można nadzorowania zoptymalizować ustawienia dla każdego zrzut w wideo. Netflix opisem [takie podejście](https://medium.com/netflix-techblog/optimized-shot-based-encodes-now-streaming-4b9464204830) w 2018 roku.

Na początku 2017 roku, firma Microsoft opublikowała program [adaptacyjnego przesyłania strumieniowego](autogen-bitrate-ladder.md) wstępnie zdefiniowanych, aby rozwiązać problem zmienności jakości i rozdzielczości źródłowe pliki wideo. Naszych klientów było różnych kombinacji zawartości, niektóre 1080 p, inne osoby w 720p i kilka w jakości SD i stosowania niższych rozdzielczości. Ponadto nie wszystkie zawartość źródłowa była mezzanines wysokiej jakości z filmów lub telewizji studios. Adaptacyjna, przesyłanie strumieniowe wstępnie zdefiniowane adresy te problemy, zapewniając, że drabiny szybkości transmisji bitów nigdy nie przekracza rozwiązania lub Średnia szybkość transmisji bitów z plik wejściowy.

Eksperymentalne ustawienie wstępne kodowania zawartości rozszerza ten mechanizm, zawierających logikę niestandardową, która umożliwia kodera wyszukiwanie wartość optymalne szybkości transmisji bitów dla danego rozwiązania, ale bez konieczności rozbudowaną analizę obliczeniową. Wynikiem jest, że to nowe ustawienie wstępne generuje danych wyjściowych, który ma niższe szybkości transmisji bitów niż ustawienie wstępne adaptacyjnego przesyłania strumieniowego, ale wyższej jakości. Następujące przykładowe wykresy, przedstawiających porównanie przy użyciu metryk jakości, takie jak [PSNR](https://en.wikipedia.org/wiki/Peak_signal-to-noise_ratio) i [VMAF](https://en.wikipedia.org/wiki/Video_Multimethod_Assessment_Fusion). Źródła został utworzony przez złączenie krótkich klipów zrzuty wysokiego stopnia złożoności z filmów i programów telewizyjnych, przeznaczony podkreślanie kodera. Zgodnie z definicją, wyniki tego generuje wstępnie zdefiniowanych, które różnią się od zawartości do zawartości — oznacza to również, że niektóre zawartości może nie istnieć znaczące zmniejszenie szybkości transmisji bitów lub poprawę jakości.

![Krzywa współczynnik jego zniekształcenia (RD) przy użyciu PSNR](media/cae-experimental/msrv1.png)

**Rysunek 1: Krzywa współczynnik jego zniekształcenia (usług pulpitu zdalnego) za pomocą metryk PSNR wysokiego stopnia złożoności źródła**

![Krzywa współczynnik jego zniekształcenia (RD) przy użyciu VMAF](media/cae-experimental/msrv2.png)

**Rysunek 2: Krzywa współczynnik jego zniekształcenia (usług pulpitu zdalnego) za pomocą metryk VMAF wysokiego stopnia złożoności źródła**

Ustawienie wstępne obecnie jest ona dostrojona dla wysokiego stopnia złożoności, wysokiej jakości źródłowe pliki wideo (filmy, programy telewizyjne). Praca jest w toku, aby dostosować je do zawartości, zmniejszenie złożoności (na przykład prezentacji programu PowerPoint), a także mniejszą jakości materiały wideo. To ustawienie wstępne również używa tego samego zestawu rozwiązania adaptacyjnego przesyłania strumieniowego ustawienia wstępnego. Firma Microsoft pracuje dla metod, aby wybrać minimalny zestaw rozwiązania na podstawie zawartości. W następujący sposób wyniki są na inną kategorię źródła zawartości, gdzie kodera był w stanie określić, że wprowadzono złej jakości (wiele kompresji artefaktów z powodu małej szybkości transmisji bitów). Pamiętaj, że o eksperymentalnym ustawienie wstępne, kodera zdecydowała się utworzyć tylko jedną warstwę danych wyjściowych — na tyle niskie, szybkości transmisji bitów, aby większość klientów można odtworzyć strumień bez wstrzymujących działanie.

![Krzywa usług pulpitu zdalnego przy użyciu PSNR](media/cae-experimental/msrv3.png)

**Rysunek 3: Krzywa usług pulpitu zdalnego przy użyciu PSNR wprowadzania niskiej jakości (na 1080p)**

![Krzywa usług pulpitu zdalnego przy użyciu VMAF](media/cae-experimental/msrv4.png)

**Rysunek 4: Krzywa usług pulpitu zdalnego przy użyciu VMAF wprowadzania niskiej jakości (na 1080p)**

## <a name="use-the-experimental-preset"></a>Użycia ustawienia wstępnego eksperymentalne

Można utworzyć przekształceń, korzystających z tego ustawienia wstępnego w następujący sposób. W przypadku korzystania z samouczka [takich](stream-files-tutorial-with-api.md), należy zaktualizować kod w następujący sposób:

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
> Prefiks "eksperymentalne" jest używany w tym miejscu celu sygnalizowania, że nadal opracowujemy bazowego algorytmów. Można zostanie ona zmiany w czasie, przez logikę używaną do generowania drabiny szybkości transmisji bitów, z celem zbieżności na algorytm, który jest niezawodny i dostosowuje się do szerokiej gamy wejściowych warunkami. Kodowanie zadań przy użyciu spowoduje wstępnie nadal być minut rozliczane na podstawie danych wyjściowych i elementu zawartości wyjściowej mogą być dostarczane z naszych punktów końcowych przesyłania strumieniowego w protokołów, takich jak KRESKI i HLS.

## <a name="next-steps"></a>Kolejne kroki

Skoro wiesz już o tej nowej opcji optymalizacji filmów wideo, zachęcamy do wypróbować jej możliwości. Prześlij nam swoją opinię, korzystając z linków na końcu tego artykułu lub skontaktuj się z bardziej bezpośrednio do nas <amsved@microsoft.com>.
