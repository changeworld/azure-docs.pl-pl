---
title: Używanie atrybutu HeadPose
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak użyć atrybutu HeadPose do automatycznego obracania prostokąta twarzy lub wykrywania gestów głowy w kanale wideo.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: sample
ms.date: 05/29/2019
ms.author: pafarley
ms.openlocfilehash: 534846044770d66ec5171ad4f61de921d2d5d194
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76169791"
---
# <a name="use-the-headpose-attribute"></a>Używanie atrybutu HeadPose

W tym przewodniku zobaczysz, jak można użyć HeadPose atrybut wykrytej twarzy, aby włączyć niektóre kluczowe scenariusze.

## <a name="rotate-the-face-rectangle"></a>Obracanie prostokąta ściany

Prostokąt twarzy, zwracany z każdą wykrytą twarzą, oznacza położenie i rozmiar twarzy na obrazie. Domyślnie prostokąt jest zawsze wyrównany z obrazem (jego boki są pionowe i poziome); może to być nieefektywne dla kadrowania kątowych ścian. W sytuacjach, w których chcesz programowo przyciąć ściany obrazu, lepiej jest mieć możliwość obracania prostokąta w celu przycięcia.

Przykładowa aplikacja [WPF w ucho przykładu usług Cognitive Services](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/Cognitive-Services-Face-WPF) face używa atrybutu HeadPose do obracania wykrytych prostokątów twarzy.

### <a name="explore-the-sample-code"></a>Poznawanie przykładowego kodu

Prostokąt twarzy można programowo obracać za pomocą atrybutu HeadPose. Jeśli określisz ten atrybut podczas wykrywania twarzy (patrz [Jak wykryć twarze),](HowtoDetectFacesinImage.md)będzie można go później zbadać. Poniższa metoda z aplikacji [Cognitive Services Face WPF](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/Cognitive-Services-Face-WPF) pobiera listę **DetectedFace** obiektów i zwraca listę **[Face](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/app-samples/Cognitive-Services-Face-WPF/Sample-WPF/Controls/Face.cs)** obiektów. **Face** w tym miejscu jest klasą niestandardową, która przechowuje dane twarzy, w tym zaktualizowane współrzędne prostokąta. Nowe wartości są obliczane dla **górnej,** **lewej,** **szerokości**i **wysokości,** a nowe pole **FaceAngle** określa obrót.

```csharp
/// <summary>
/// Calculate the rendering face rectangle
/// </summary>
/// <param name="faces">Detected face from service</param>
/// <param name="maxSize">Image rendering size</param>
/// <param name="imageInfo">Image width and height</param>
/// <returns>Face structure for rendering</returns>
public static IEnumerable<Face> CalculateFaceRectangleForRendering(IList<DetectedFace> faces, int maxSize, Tuple<int, int> imageInfo)
{
    var imageWidth = imageInfo.Item1;
    var imageHeight = imageInfo.Item2;
    var ratio = (float)imageWidth / imageHeight;
    int uiWidth = 0;
    int uiHeight = 0;
    if (ratio > 1.0)
    {
        uiWidth = maxSize;
        uiHeight = (int)(maxSize / ratio);
    }
    else
    {
        uiHeight = maxSize;
        uiWidth = (int)(ratio * uiHeight);
    }

    var uiXOffset = (maxSize - uiWidth) / 2;
    var uiYOffset = (maxSize - uiHeight) / 2;
    var scale = (float)uiWidth / imageWidth;

    foreach (var face in faces)
    {
        var left = (int)(face.FaceRectangle.Left * scale + uiXOffset);
        var top = (int)(face.FaceRectangle.Top * scale + uiYOffset);

        // Angle of face rectangles, default value is 0 (not rotated).
        double faceAngle = 0;

        // If head pose attributes have been obtained, re-calculate the left & top (X & Y) positions.
        if (face.FaceAttributes?.HeadPose != null)
        {
            // Head pose's roll value acts directly as the face angle.
            faceAngle = face.FaceAttributes.HeadPose.Roll;
            var angleToPi = Math.Abs((faceAngle / 180) * Math.PI);

            // _____       | / \ |
            // |____|  =>  |/   /|
            //             | \ / |
            // Re-calculate the face rectangle's left & top (X & Y) positions.
            var newLeft = face.FaceRectangle.Left +
                face.FaceRectangle.Width / 2 -
                (face.FaceRectangle.Width * Math.Sin(angleToPi) + face.FaceRectangle.Height * Math.Cos(angleToPi)) / 2;

            var newTop = face.FaceRectangle.Top +
                face.FaceRectangle.Height / 2 -
                (face.FaceRectangle.Height * Math.Sin(angleToPi) + face.FaceRectangle.Width * Math.Cos(angleToPi)) / 2;

            left = (int)(newLeft * scale + uiXOffset);
            top = (int)(newTop * scale + uiYOffset);
        }

        yield return new Face()
        {
            FaceId = face.FaceId?.ToString(),
            Left = left,
            Top = top,
            OriginalLeft = (int)(face.FaceRectangle.Left * scale + uiXOffset),
            OriginalTop = (int)(face.FaceRectangle.Top * scale + uiYOffset),
            Height = (int)(face.FaceRectangle.Height * scale),
            Width = (int)(face.FaceRectangle.Width * scale),
            FaceAngle = faceAngle,
        };
    }
}
```

### <a name="display-the-updated-rectangle"></a>Wyświetlanie zaktualizowanego prostokąta

W tym miejscu można użyć zwróconych obiektów **Ściana** na ekranie. Następujące wiersze z [pliku FaceDetectionPage.xaml](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/app-samples/Cognitive-Services-Face-WPF/Sample-WPF/Controls/FaceDetectionPage.xaml) pokazują, jak nowy prostokąt jest renderowany na podstawie tych danych:

```xaml
 <DataTemplate>
    <Rectangle Width="{Binding Width}" Height="{Binding Height}" Stroke="#FF26B8F4" StrokeThickness="1">
        <Rectangle.LayoutTransform>
            <RotateTransform Angle="{Binding FaceAngle}"/>
        </Rectangle.LayoutTransform>
    </Rectangle>
</DataTemplate>
```

## <a name="detect-head-gestures"></a>Wykrywanie gestów głowy

Możesz wykryć gesty głowy, takie jak kiwanie głową i potrząsanie głową, śledząc zmiany HeadPose w czasie rzeczywistym. Możesz użyć tej funkcji jako niestandardowego detektora żywotności.

Wykrywanie żywotności jest zadaniem określenia, że podmiot jest prawdziwą osobą, a nie reprezentacją obrazu lub wideo. Detektor gestów głowy może służyć jako jeden ze sposobów na weryfikację żywotności, zwłaszcza w przeciwieństwie do reprezentacji obrazu osoby.

> [!CAUTION]
> Aby wykrywać gesty głowy w czasie rzeczywistym, musisz wywołać interfejs API twarzy z dużą szybkością (więcej niż raz na sekundę). Jeśli masz subskrypcję bezpłatną (f0), nie będzie to możliwe. Jeśli masz subskrypcję płatnej warstwy, upewnij się, że obliczono koszty wykonywania szybkich wywołań interfejsu API w celu wykrywania gestów head.

Zobacz [przykład headpose twarzy](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/FaceAPIHeadPoseSample) w usłudze GitHub, aby uzyskać działający przykład wykrywania gestów głowy.

## <a name="next-steps"></a>Następne kroki

Zobacz [aplikacji WPF WPF w usłudze Cognitive Services w](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/Cognitive-Services-Face-WPF) usłudze GitHub, aby uzyskać działający przykład prostokątów obróconej ściany. Możesz też zapoznać się z przykładową aplikacją [Face HeadPose,](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples) która śledzi atrybut HeadPose w czasie rzeczywistym w celu wykrycia ruchów głowy.