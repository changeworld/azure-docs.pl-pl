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
ms.date: 02/22/2019
ms.author: sbowles
ms.openlocfilehash: bf3af8f5d1d2f063199a8275c2f49c70140e8732
ms.sourcegitcommit: 89b5e63945d0c325c1bf9e70ba3d9be6888da681
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/08/2019
ms.locfileid: "57588775"
---
# <a name="get-face-detection-data"></a>Pobieranie danych wykrywania twarzy

Ten przewodnik pokazują, jak wyodrębnić atrybutów, takich jak płci, wieku lub poza z danego obrazu za pomocą wykrywania twarzy. Fragmenty kodu, w tym przewodniku są zapisywane C# przy użyciu biblioteki klienta interfejsu API rozpoznawania twarzy, ale te same funkcje są dostępne za pośrednictwem [interfejsu API REST](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

Ten przewodnik przedstawia sposób do:

- Pobierz lokalizacje i wymiary twarzy na obrazie.
- Pobierz lokalizacje różnych to punktów charakterystycznych (uczniów, czołowych, ujścia i tak dalej) do obrazu.
- Odgadnąć, płeć, wiek i emocji i innych atrybutów twarzy wykryte.

## <a name="setup"></a>Konfigurowanie

W tym przewodniku założono, zostały już wykonane **[FaceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient?view=azure-dotnet)** obiektu o nazwie `faceClient`, przy użyciu twarzy subskrypcji key i punktu końcowego adresu URL. W tym miejscu, można użyć funkcji wykrywania twarzy, wywołując jedną **[DetectWithUrlAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithurlasync?view=azure-dotnet)** (używanych w tym przewodniku) lub **[DetectWithStreamAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithstreamasync?view=azure-dotnet)**. Zobacz [wykrywanie twarzy Przewodnik Szybki start dotyczący C# ](../quickstarts/csharp-detect-sdk.md) instrukcje na temat konfigurowania tego.

Ten przewodnik koncentruje się na szczegółowe informacje na temat wywołania Wykryj&mdash;argumenty, które można przekazać i co można zrobić za pomocą zwracanych danych. Firma Microsoft zaleca tylko podczas badania funkcje, których potrzebujesz, ponieważ każda operacja zajmuje dodatkowy czas do ukończenia.

## <a name="get-basic-face-data"></a>Pobieranie danych podstawowych twarzy

Aby znaleźć twarzy i ich lokalizacji obrazu, należy wywołać metodę z _returnFaceId_ parametr **true** (ustawienie domyślne).

```csharp
IList<DetectedFace> faces = await faceClient.Face.DetectWithUrlAsync(imageUrl, true, false, null);
```

Zwrócony **[DetectedFace](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.detectedface?view=azure-dotnet)** obiekty mogą być wyszukiwane ich unikatowych identyfikatorów i prostokąt, co daje współrzędne twarzy w pikselach.

```csharp
foreach (var face in faces)
{
    string id = face.FaceId.ToString();
    FaceRectangle rect = face.FaceRectangle;
}
```

Zobacz **[FaceRectangle](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.facerectangle?view=azure-dotnet)** informacji na temat sposobu analizowania lokalizacji i wymiary powierzchni. Zazwyczaj prostokąta zawiera oczy rzęs, czołowych i ujścia; początku head, uszy i chin nie uwzględniono niekoniecznie. Jeśli zamierzasz używać obrys twarzy, aby przyciąć head pełną lub średniej zrzut pionowa (identyfikator typu obraz zdjęcia), można rozwinąć prostokąt, jest pewna w każdym kierunku.

## <a name="get-face-landmarks"></a>Pobierz to punktów charakterystycznych

To punktów charakterystycznych to zbiór łatwe do znalezienia punkty na powierzchni, takich jak uczniów lub porady czołowych. Dane charakterystycznych elementów krajobrazu twarzy można uzyskać przez ustawienie _returnFaceLandmarks_ parametr **true**.

```csharp
IList<DetectedFace> faces = await faceClient.Face.DetectWithUrlAsync(imageUrl, true, true, null);
```

Domyślnie istnieje 27 wstępnie zdefiniowanych punktów charakterystycznych. Na poniższej ilustracji przedstawiono wszystkie punkty 27:

![Diagramu twarzy ze wszystkich 27 charakterystycznych punktów oznakowane jako](../Images/landmarks.1.jpg)

Punktów zwrócił znajdują się w jednostkach pikseli, podobnie jak ramki prostokąt twarzy. Poniższy kod demonstruje, jak może pobrać lokalizacji czołowych i uczniów:

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

Danych charakterystycznych elementów krajobrazu twarzy można również dokładnie obliczyć kierunek powierzchni. Na przykład możemy zdefiniować obrót twarz jako wektor z Centrum ujścia do środka oczu. Poniższy kod oblicza wektor to:

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

Kierunek twarz, wiedząc, następnie można obracać ramkę prostokątny twarzy, są bardziej prawidłowo wyrównane. Jeśli chcesz przyciąć twarzy na obrazie, można programowo obrócić obraz, aby zawsze miejscami pojawiania się twarzy pionowo.

## <a name="get-face-attributes"></a>Pobieranie atrybutów twarzy

Oprócz prostokąty twarzy i charakterystycznych elementów krajobrazu interfejs API wykrywania twarzy można analizować kilka pojęć atrybutów twarzy. Należą do nich:

- Wiek
- Płeć
- Intensywność uśmiechu
- Zarost
- Okulary
- 3D poza siedzibę
- Rozpoznawanie emocji

> [!IMPORTANT]
> Te atrybuty są przewidzieć przy użyciu algorytmów statystycznych, a nie zawsze są dokładne. Należy zachować ostrożność podczas podejmowania decyzji na podstawie danych atrybutu.
>

Aby analizować atrybutów twarzy, należy ustawić _returnFaceAttributes_ parametru do listy **[wyliczenia FaceAttributeType](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.faceattributetype?view=azure-dotnet)** wartości.

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

Następnie pobieranie odwołań do zwracanych danych i wykonać dalsze czynności zgodnie z potrzebami.

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

Aby dowiedzieć się więcej na temat każdego z atrybutów, zobacz [słownik](../Glossary.md).

## <a name="next-steps"></a>Kolejne kroki

W tym przewodniku przedstawiono sposób użycia różnych funkcji wykrywania twarzy. Następnie możesz zapoznać się [słownik](../Glossary.md) uzyskać bardziej szczegółowy widok danych twarzy zostały pobrane.

## <a name="related-topics"></a>Tematy pokrewne

- [Dokumentacja referencyjna (REST)](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)
- [Dokumentacja referencyjna (.NET SDK)](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/face?view=azure-dotnet)
