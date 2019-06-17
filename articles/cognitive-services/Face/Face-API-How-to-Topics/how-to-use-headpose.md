---
title: Użyj atrybutu HeadPose
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak za pomocą atrybutu HeadPose automatyczna rotacja obrys twarzy, a także wykrywanie głównego gestów w filmie wideo kanału informacyjnego.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: sample
ms.date: 05/29/2019
ms.author: pafarley
ms.openlocfilehash: 168b4fce873206e39a32a83da3dc5509b431d6a1
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67058575"
---
# <a name="use-the-headpose-attribute"></a>Użyj atrybutu HeadPose

W tym przewodniku zobaczysz, jak można użyć atrybutu HeadPose wykryte twarzy umożliwiające niektóre najważniejsze scenariusze.

## <a name="rotate-the-face-rectangle"></a>Obróć obrys twarzy

Prostokąt twarzy, zwrócono co wykryte twarze, oznacza lokalizację i rozmiar twarzy na obrazie. Domyślnie prostokąta jest zawsze powiązana z obrazu (boków są poziome i pionowe). może to być mało wydajne dla ramek pod kątem twarzy. W sytuacjach, w którym chcesz przyciąć programowo twarzy na obrazie lepiej jest można było obrócić prostokąt można przycięcia.

[Cognitive Services Face WPF](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/Cognitive-Services-Face-WPF) Przykładowa aplikacja używa atrybutu HeadPose wymienić jego prostokąty wykryte twarzy.

### <a name="explore-the-sample-code"></a>Poznawanie przykładowego kodu

Za pomocą atrybutu HeadPose, można programowo obrócić prostokąt twarzy. Jeśli ten atrybut jest określony podczas wykrywania twarzy (zobacz [jak wykrywać twarze](HowtoDetectFacesinImage.md)), będzie można badać je później. Następujące metody z [Cognitive Services Face WPF](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/Cognitive-Services-Face-WPF) aplikacji przyjmuje listę **DetectedFace** obiekty i zwraca listę **[twarzy](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/app-samples/Cognitive-Services-Face-WPF/Sample-WPF/Controls/Face.cs)** obiekty. **Twarzy** Oto klasę niestandardową, że magazyny twarzy dane, w tym współrzędnych prostokąta zaktualizowane. Nowe wartości są obliczane dla **górnej**, **po lewej stronie**, **szerokość**, i **wysokość**oraz nowego pola **FaceAngle**określa obrót.

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

### <a name="display-the-updated-rectangle"></a>Wyświetl zaktualizowane prostokąt

W tym miejscu możesz użyć zwracanego **twarzy** obiektów w ekranu. Następujące wiersze z [FaceDetectionPage.xaml](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/app-samples/Cognitive-Services-Face-WPF/Sample-WPF/Controls/FaceDetectionPage.xaml) pokazują, jak nowy prostokąt jest renderowany na podstawie tych danych:

```xaml
 <DataTemplate>
    <Rectangle Width="{Binding Width}" Height="{Binding Height}" Stroke="#FF26B8F4" StrokeThickness="1">
        <Rectangle.LayoutTransform>
            <RotateTransform Angle="{Binding FaceAngle}"/>
        </Rectangle.LayoutTransform>
    </Rectangle>
</DataTemplate>
```

## <a name="detect-head-gestures"></a>Wykrywanie gestów głównego

Może wykryć główny gestów takich jak kiwającą i head potrząsając śledzenie zmian HeadPose w czasie rzeczywistym. Ta funkcja służy jako wykrywacz żywotności niestandardowych.

Wykrywanie żywotności jest zadanie określenia, czy podmiot jest prawdziwa osoba i nie obraz lub film wideo reprezentację w postaci. Wykrywanie gestu głównego może służyć jako jednym ze sposobów umożliwiających weryfikację żywotności, szczególnie w przeciwieństwie do reprezentację w postaci obrazu osoby.

> [!CAUTION]
> Aby wykryć główny gestów w czasie rzeczywistym, musisz wywołać interfejs API rozpoznawania twarzy z dużą szybkością (więcej niż jeden raz na sekundę). Jeśli masz subskrypcję bezpłatnej warstwy (f0), nie będzie to możliwe. Jeśli masz subskrypcję płatną warstwę, upewnij się, została obliczona kosztów wprowadzania szybkiego interfejsu API połączeń head gestu wykrywania.

Zobacz [przykład HeadPose interfejsu API rozpoznawania twarzy](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/FaceAPIHeadPoseSample) w serwisie GitHub, aby uzyskać przykład pracy head gestu wykrywania.

## <a name="next-steps"></a>Kolejne kroki

Zobacz [Cognitive Services Face WPF](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/Cognitive-Services-Face-WPF) aplikacji w witrynie GitHub dla przykładu pracy prostokątów obrócony twarzy. Zobacz też [przykład HeadPose interfejsu API rozpoznawania twarzy](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples) aplikację, która śledzi atrybut HeadPose w czasie rzeczywistym w celu wykrycia głównego przepływu.