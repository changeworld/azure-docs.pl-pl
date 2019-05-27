---
title: Wykrywanie twarzy na obrazie — interfejs API rozpoznawania twarzy
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak używać różnych danych zwróconych przez funkcję wykrywania twarzy.
services: cognitive-services
author: SteveMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 04/18/2019
ms.author: sbowles
ms.openlocfilehash: 46bd1bdd55725878bc7b1bd55d5e24b78d82aada
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/23/2019
ms.locfileid: "66124548"
---
# <a name="get-face-detection-data"></a>Pobieranie danych wykrywania twarzy

Ten przewodnik pokazuje sposób użycia wykrywanie twarzy można wyodrębnić atrybutów, takich jak płci, wieku lub poza z danego obrazu. Fragmenty kodu, w tym przewodniku są zapisywane C# za pomocą biblioteki klienta usługi Azure Cognitive Services Face API. Taką samą funkcjonalność jest dostępna za pośrednictwem [interfejsu API REST](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

W tym przewodniku przedstawiono sposób do:

- Pobierz lokalizacje i wymiary twarzy na obrazie.
- Pobierz lokalizacje różnych to punktów charakterystycznych, takie jak uczniów, czołowych i ujścia w obrazie.
- Odgadnąć, płeć, wiek, emocje i innych atrybutów twarzy wykryte.

## <a name="setup"></a>Konfiguracja

W tym przewodniku założono, że możesz już zbudowany [FaceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient?view=azure-dotnet) obiektu o nazwie `faceClient`, przy użyciu twarzy subskrypcji key i punktu końcowego adresu URL. W tym miejscu, można użyć funkcji wykrywania twarzy, wywołując jedną [DetectWithUrlAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithurlasync?view=azure-dotnet), która zostanie użyta w tym przewodniku lub [DetectWithStreamAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithstreamasync?view=azure-dotnet). Aby uzyskać instrukcje na temat sposobu skonfigurowania tej funkcji, zobacz [wykrywania twarzy Przewodnik Szybki start dotyczący C# ](../quickstarts/csharp-detect-sdk.md).

Ten przewodnik koncentruje się na szczegółowe informacje na temat wywołania wykrywania, takie jak jakie argumenty można przekazać i co można zrobić za pomocą zwracanych danych. Zaleca się wyszukać tylko funkcje, których potrzebujesz. Każda operacja zajmuje dodatkowy czas ukończenia.

## <a name="get-basic-face-data"></a>Pobieranie danych podstawowych twarzy

Aby znaleźć twarzy i ich lokalizacji obrazu, należy wywołać metodę z _returnFaceId_ parametr **true**. Jest to ustawienie domyślne.

```csharp
IList<DetectedFace> faces = await faceClient.Face.DetectWithUrlAsync(imageUrl, true, false, null);
```

Można tworzyć zapytania zwracanego [DetectedFace](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.detectedface?view=azure-dotnet) obiektów dla ich unikatowych identyfikatorów i prostokąt, który zawiera współrzędne twarzy w pikselach.

```csharp
foreach (var face in faces)
{
    string id = face.FaceId.ToString();
    FaceRectangle rect = face.FaceRectangle;
}
```

Aby uzyskać informacje na temat sposobu analizowania lokalizacji i wymiary powierzchni, zobacz [FaceRectangle](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.facerectangle?view=azure-dotnet). Zazwyczaj prostokącie oczy rzęs, czołowych i ujścia. Początku head, uszy i chin nie są zawsze uwzględniane. Aby użyć obrys twarzy przyciąć pełną head lub uzyskać portret środku zrzut, być może obraz typ Identyfikatora zdjęcia, możesz rozwinąć prostokąt w każdym kierunku.

## <a name="get-face-landmarks"></a>Pobierz to punktów charakterystycznych

[Twarzy charakterystycznych elementów krajobrazu](../concepts/face-detection.md#face-landmarks) to zbiór łatwe do znalezienia punkty na powierzchni, takich jak uczniów lub porady czołowych. Aby uzyskać dane charakterystycznych elementów krajobrazu rozpoznawania twarzy, należy ustawić _returnFaceLandmarks_ parametr **true**.

```csharp
IList<DetectedFace> faces = await faceClient.Face.DetectWithUrlAsync(imageUrl, true, true, null);
```

Poniższy kod demonstruje, jak może pobrać lokalizacji czołowych i uczniów:

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

Możesz również użyć danych charakterystycznych elementów krajobrazu twarzy dokładnie obliczyć kierunek twarz. Na przykład można zdefiniować obrót twarz jako wektor z Centrum ujścia do środka oczu. Poniższy kod oblicza wektor to:

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

Gdy wiadomo, kierunku twarz, można też obrócić ramkę prostokątny twarzy, są bardziej prawidłowo wyrównane. Aby przyciąć twarzy na obrazie, można programowo obrócić obraz, dzięki czemu zawsze miejscami pojawiania się twarzy pionowo.

## <a name="get-face-attributes"></a>Pobieranie atrybutów twarzy

Oprócz prostokąty twarzy i charakterystycznych elementów krajobrazu interfejs API wykrywania twarzy można analizować kilka pojęć atrybutów twarzy. Aby uzyskać pełną listę, zobacz [atrybutów twarzy](../concepts/face-detection.md#attributes) koncepcyjny sekcji.

Aby analizować atrybutów twarzy, należy ustawić _returnFaceAttributes_ parametru do listy [wyliczenia FaceAttributeType](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.faceattributetype?view=azure-dotnet) wartości.

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

Następnie pobieranie odwołań do zwracanych danych i wykonać więcej operacji zgodnie z potrzebami.

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

Aby dowiedzieć się więcej na temat każdego z atrybutów, zobacz [wykrywanie twarzy i atrybuty](../concepts/face-detection.md) przewodnik koncepcyjny.

## <a name="next-steps"></a>Kolejne kroki

W tym przewodniku przedstawiono sposób użycia różnych funkcji wykrywania twarzy. Następnie zintegrować te funkcje aplikację wykonując szczegółowy samouczek.

- [Samouczek: Tworzenie aplikacji WPF, aby wyświetlić dane rozpoznawania twarzy na obrazie](../Tutorials/FaceAPIinCSharpTutorial.md)
- [Samouczek: Tworzenie aplikacji dla systemu Android, aby wykrywać i ramki twarzy na obrazie](../Tutorials/FaceAPIinJavaForAndroidTutorial.md)

## <a name="related-topics"></a>Tematy pokrewne

- [Dokumentacja referencyjna (REST)](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)
- [Dokumentacja referencyjna (.NET SDK)](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/face?view=azure-dotnet)