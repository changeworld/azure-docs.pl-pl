---
title: Wykrywanie twarzy na obrazie - Twarz
titleSuffix: Azure Cognitive Services
description: W tym przewodniku pokazano, jak używać wykrywania twarzy do wyodrębniania atrybutów, takich jak płeć, wiek lub pozowanie z danego obrazu.
services: cognitive-services
author: SteveMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 04/18/2019
ms.author: sbowles
ms.openlocfilehash: 7070cb3bcd1b519828a750cf4ba6caf7ecb34bbb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "76169874"
---
# <a name="get-face-detection-data"></a>Uzyskaj dane dotyczące wykrywania twarzy

W tym przewodniku pokazano, jak używać wykrywania twarzy do wyodrębniania atrybutów, takich jak płeć, wiek lub pozowanie z danego obrazu. Fragmenty kodu w tym przewodniku są zapisywane w języku C# przy użyciu biblioteki klienta usługi Azure Cognitive Services Face. Ta sama funkcja jest dostępna za pośrednictwem [interfejsu API REST](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

W tym przewodniku dowiesz się, jak:

- Pobierz lokalizacje i wymiary twarzy na obrazie.
- Pobierz lokalizacje różnych punktów orientacyjnych twarzy, takich jak źrenice, nos i usta, na obrazie.
- Odgadnij płeć, wiek, emocje i inne atrybuty wykrytej twarzy.

## <a name="setup"></a>Konfiguracja

W tym przewodniku przyjęto założenie, że obiekt `faceClient` [FaceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient?view=azure-dotnet) o nazwie , z kluczem subskrypcji face i adresem URL punktu końcowego. W tym miejscu można użyć funkcji wykrywania twarzy, wywołując [detectwithurlAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithurlasync?view=azure-dotnet), który jest używany w tym [przewodniku, lub DetectWithStreamAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithstreamasync?view=azure-dotnet). Aby uzyskać instrukcje dotyczące konfigurowania tej funkcji, postępuj zgodnie z jednym z przewodników Szybki start.

Ten przewodnik koncentruje się na szczegóły wykryć wywołanie, takie jak jakie argumenty można przekazać i co można zrobić z zwróconych danych. Zaleca się zapytanie tylko o funkcje, których potrzebujesz. Każda operacja zajmuje dodatkowy czas, aby zakończyć.

## <a name="get-basic-face-data"></a>Uzyskaj podstawowe dane twarzy

Aby znaleźć twarze i uzyskać ich lokalizacje na obrazie, należy wywołać metodę z _parametrem returnFaceId_ ustawionym na **true**. Jest to ustawienie domyślne.

```csharp
IList<DetectedFace> faces = await faceClient.Face.DetectWithUrlAsync(imageUrl, true, false, null);
```

Można zbadać [zwrócone DetectedFace](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.detectedface?view=azure-dotnet) obiektów ich unikatowych identyfikatorów i prostokąt, który daje współrzędne pikseli twarzy.

```csharp
foreach (var face in faces)
{
    string id = face.FaceId.ToString();
    FaceRectangle rect = face.FaceRectangle;
}
```

Aby uzyskać informacje na temat analizowania lokalizacji i wymiarów ściany, zobacz [FaceRectangle](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.facerectangle?view=azure-dotnet). Zwykle ten prostokąt zawiera oczy, brwi, nos i usta. Czubek głowy, uszu i podbródek niekoniecznie są wliczone w cenę. Aby użyć prostokąta twarzy do przycięcia pełnej głowy lub uzyskania portretu w połowie zdjęcia, być może w przypadku obrazu typu identyfikator zdjęcia, można rozwinąć prostokąt w każdym kierunku.

## <a name="get-face-landmarks"></a>Uzyskaj punkty orientacyjne twarzy

[Punkty orientacyjne twarzy](../concepts/face-detection.md#face-landmarks) to zestaw łatwych do znalezienia punktów na twarzy, takich jak źrenice lub czubek nosa. Aby uzyskać dane punktu orientacyjnego twarzy, ustaw parametr _returnFaceLandmarks_ na **true**.

```csharp
IList<DetectedFace> faces = await faceClient.Face.DetectWithUrlAsync(imageUrl, true, true, null);
```

Poniższy kod pokazuje, jak można pobrać lokalizacje nosa i uczniów:

```csharp
foreach (var face in faces)
{
    var landmarks = face.FaceLandmarks;

    double noseX = landmarks.NoseTip.X;
    double noseY = landmarks.NoseTip.Y;

    double leftPupilX = landmarks.PupilLeft.X;
    double leftPupilY = landmarks.PupilLeft.Y;

    double rightPupilX = landmarks.PupilRight.X;
    double rightPupilY = landmarks.PupilRight.Y;
}
```

Można również użyć danych punktów orientacyjnych ścian, aby dokładnie obliczyć kierunek ściany. Na przykład można zdefiniować obrót ściany jako wektor od środka jamy ustnej do środka oczu. Następujący kod oblicza ten wektor:

```csharp
var upperLipBottom = landmarks.UpperLipBottom;
var underLipTop = landmarks.UnderLipTop;

var centerOfMouth = new Point(
    (upperLipBottom.X + underLipTop.X) / 2,
    (upperLipBottom.Y + underLipTop.Y) / 2);

var eyeLeftInner = landmarks.EyeLeftInner;
var eyeRightInner = landmarks.EyeRightInner;

var centerOfTwoEyes = new Point(
    (eyeLeftInner.X + eyeRightInner.X) / 2,
    (eyeLeftInner.Y + eyeRightInner.Y) / 2);

Vector faceDirection = new Vector(
    centerOfTwoEyes.X - centerOfMouth.X,
    centerOfTwoEyes.Y - centerOfMouth.Y);
```

Gdy znasz kierunek ściany, możesz obrócić prostokątną ramkę czołową, aby wyrównać ją bardziej poprawnie. Aby przyciąć ściany obrazu, można programowo obrócić obraz, tak aby twarze zawsze były wyświetlane pionowo.

## <a name="get-face-attributes"></a>Uzyskaj atrybuty twarzy

Oprócz prostokątów twarzy i punktów orientacyjnych interfejs API wykrywania twarzy może analizować kilka atrybutów koncepcyjnych twarzy. Aby uzyskać pełną listę, zobacz sekcję koncepcyjne [Atrybuty twarzy.](../concepts/face-detection.md#attributes)

Aby analizować atrybuty twarzy, ustaw parametr _returnFaceAttributes_ na listę wartości [Wyliczenia Typu FaceAttributeType.](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.faceattributetype?view=azure-dotnet)

```csharp
var requiredFaceAttributes = new FaceAttributeType[] {
    FaceAttributeType.Age,
    FaceAttributeType.Gender,
    FaceAttributeType.Smile,
    FaceAttributeType.FacialHair,
    FaceAttributeType.HeadPose,
    FaceAttributeType.Glasses,
    FaceAttributeType.Emotion
};
var faces = await faceClient.DetectWithUrlAsync(imageUrl, true, false, requiredFaceAttributes);
```

Następnie uzyskać odwołania do zwróconych danych i wykonać więcej operacji zgodnie z potrzebami.

```csharp
foreach (var face in faces)
{
    var attributes = face.FaceAttributes;
    var age = attributes.Age;
    var gender = attributes.Gender;
    var smile = attributes.Smile;
    var facialHair = attributes.FacialHair;
    var headPose = attributes.HeadPose;
    var glasses = attributes.Glasses;
    var emotion = attributes.Emotion;
}
```

Aby dowiedzieć się więcej o poszczególnych atrybutach, zobacz Przewodnik koncepcyjny [wykrywania twarzy i atrybuty.](../concepts/face-detection.md)

## <a name="next-steps"></a>Następne kroki

W tym przewodniku przedstawiono, jak korzystać z różnych funkcji wykrywania twarzy. Następnie zintegruj te funkcje z aplikacją, wykonując szczegółowy samouczek.

- [Samouczek: tworzenie aplikacji WPF do wyświetlania danych twarzy na obrazie](../Tutorials/FaceAPIinCSharpTutorial.md)
- [Samouczek: tworzenie aplikacji dla systemu Android wykrywającej i oznaczającej ramką twarze na obrazie](../Tutorials/FaceAPIinJavaForAndroidTutorial.md)

## <a name="related-topics"></a>Powiązane tematy

- [Dokumentacja referencyjna (REST)](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)
- [Dokumentacja referencyjna (.NET SDK)](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/faceapi?view=azure-dotnet)