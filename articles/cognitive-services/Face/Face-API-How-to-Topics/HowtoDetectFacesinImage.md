---
title: Wykrywanie kroje w obrazów za pomocą interfejsu API krój | Dokumentacja firmy Microsoft
titleSuffix: Microsoft Cognitive Services
description: Wykrywanie kroje obrazów za pomocą interfejsu API krój w usługach kognitywnych.
services: cognitive-services
author: SteveMSFT
manager: corncar
ms.service: cognitive-services
ms.component: face-api
ms.topic: article
ms.date: 03/01/2018
ms.author: sbowles
ms.openlocfilehash: 57cd0915450428399fd680638aa4fae2cdbe17c9
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347104"
---
# <a name="how-to-detect-faces-in-image"></a>Jak wykryć skierowany w obrazie

W tym przewodniku przedstawiono sposób wykrywania krojów z obrazu z powierzchni atrybutów, takich jak płci, wieku lub ułożenia wyodrębnione. Przykłady są zapisywane w języku C# za pomocą biblioteki klienta powierzchni interfejsu API. 

## <a name="concepts"></a> Pojęcia

Jeśli nie znasz za pomocą dowolnego z następujących koncepcji, w tym przewodniku, należy odwoływać się do definicji w naszym [słownik](../Glossary.md) w dowolnym momencie: 

- Wykrywanie twarzy
- Punkty orientacyjne krój
- Stanowić HEAD
- Atrybuty krój

## <a name="preparation"></a> Przygotowanie

W tym przykładzie zostaną przedstawione następujące funkcje: 

- Wykrywanie krojów z obrazu i oznaczysz je przy użyciu prostokątne ramek
- Analizowanie lokalizacje uczniów, nos lub ujścia i oznaczysz je w obrazie
- Analizowanie ułożenia head, płeć i wiek powierzchni

Aby wykonać te funkcje, należy przygotować obraz z co najmniej jeden krój Wyczyść. 

## <a name="step1"></a> Krok 1: Autoryzowanie wywołania interfejsu API

Każdego wywołania funkcji API krój wymaga klucza subskrypcji. Ten klucz musi być przekazywane przy użyciu parametru ciągu zapytania albo określonym w nagłówku żądania. Aby przekazać klucz subskrypcji przy użyciu ciągu zapytania, zapoznaj się adres URL żądania [stają przed — wykrywanie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) na przykład:

```
https://westus.api.cognitive.microsoft.com/face/v1.0/detect[?returnFaceId][&returnFaceLandmarks][&returnFaceAttributes]
&subscription-key=<Subscription Key>
```

Alternatywnie, klucz subskrypcji można również określić w nagłówku żądania HTTP: **ocp-apim subskrypcji — klucz: &lt;klucza subskrypcji&gt;**  podczas korzystania z biblioteki klienta, klucz Subskrypcja jest przekazywany w za pomocą konstruktora klasy FaceServiceClient. Na przykład:
```CSharp
faceServiceClient = new FaceServiceClient("<Subscription Key>");
```

## <a name="step2"></a> Krok 2: Przekaż obraz do usługi i wykonać wykrywania twarzy na obrazie

Najprostszym sposobem wykonania wykrywania twarzy na obrazie jest bezpośrednio przekazywanie obrazu. Jest to realizowane przez wysłanie żądania "POST" z typem zawartości application/octet-stream, z danymi do odczytu z obrazu JPEG. Maksymalny rozmiar obrazu jest 4 MB.

Za pomocą biblioteki klienta, wykrywania twarzy na obrazie za pomocą przekazywanie odbywa się przez przekazywanie obiektu strumienia. Zobacz poniższy przykład:
```CSharp
using (Stream s = File.OpenRead(@"D:\MyPictures\image1.jpg"))
{
    var faces = await faceServiceClient.DetectAsync(s, true, true);
 
    foreach (var face in faces)
    {
        var rect = face.FaceRectangle;
        var landmarks = face.FaceLandmarks;
    }
}
```

Należy pamiętać, że metoda DetectAsync FaceServiceClient asynchronicznego. Wywołanie metody powinien być oznaczony jako async również, aby można było używać klauzuli await.
Jeśli obraz jest już w sieci web i adres URL, podając również adres URL można wykonać wykrywania twarzy na obrazie. W tym przykładzie treści żądania będzie ciągu JSON, który zawiera adres URL.
Za pomocą biblioteki klienta, wykrywania twarzy na obrazie za pomocą adresu URL mogą być wykonywane za pomocą innego przeciążenia metody DetectAsync.
```CSharp
string imageUrl = "http://news.microsoft.com/ceo/assets/photos/06_web.jpg";
var faces = await faceServiceClient.DetectAsync(imageUrl, true, true);
 
foreach (var face in faces)
{
    var rect = face.FaceRectangle;
    var landmarks = face.FaceLandmarks;
}
``` 

Właściwość FaceRectangle, która jest zwracana z wykrytym kroje jest zasadniczo lokalizacji na powierzchni w pikselach. Zazwyczaj prostokąta zawiera oczu, rzęs nos i ujścia — początku head, uszy i chin nie są uwzględniane. Przycinanie head pełną lub zrzut pośredniej pionowa (obraz zdjęcie identyfikator typu), można rozwiń obszar ramki krój prostokątny obszar powierzchni może być za mała dla niektórych aplikacji. Do zlokalizowania krój dokładnie przy użyciu punkty orientacyjne krój (lokalizowanie funkcji twarzy na obrazie lub stają przed mechanizmów kierunek) opisane w następnej sekcji zostanie okazać się przydatne.

## <a name="step3"></a> Krok 3: Opisu i użytkowania punkty orientacyjne krój

Punkty orientacyjne krój są szereg szczegółowe punkty na powierzchni; zwykle punkty krój składników, takich jak uczniów, canthus lub nos. Punkty orientacyjne krój są opcjonalne atrybuty, które można analizować podczas wykrywania twarzy na obrazie. Można albo przebiegu 'true' jako wartości logicznej do parametru zapytania returnFaceLandmarks podczas wywoływania metody [stają przed — wykrywanie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), lub użyj returnFaceLandmarks opcjonalny parametr dla klasy FaceServiceClient metody DetectAsync w kolejności Aby uwzględnić punkty orientacyjne krój w wynikach wykrywania.

Domyślnie są 27 punktów dzielnicę wstępnie zdefiniowane. Poniższy rysunek przedstawia sposób wszystkich punktów 27 są zdefiniowane:

![HowToDetectFace](../Images/landmarks.1.jpg)

Punktów zwrócona są w jednostkach pikseli, podobnie jak ramki prostokątne krój. W związku z tym ułatwiając oznaczyć określonych punktów zainteresowanie obrazu. Poniższy kod ilustruje, pobieranie lokalizacji nos i uczniów:
```CSharp
var faces = await faceServiceClient.DetectAsync(imageUrl, returnFaceLandmarks:true);
 
foreach (var face in faces)
{
    var rect = face.FaceRectangle;
    var landmarks = face.FaceLandmarks;
 
    double noseX = landmarks.NoseTip.X;
    double noseY = landmarks.NoseTip.Y;
 
    double leftPupilX = landmarks.PupilLeft.X;
    double leftPupilY = landmarks.PupilLeft.Y;
 
    double rightPupilX = landmarks.PupilRight.X;
    double rightPupilY = landmarks.PupilRight.Y;
}
``` 

Oprócz oznaczenie funkcji powierzchni w obrazie, punkty orientacyjne krój można również dokładnie obliczyć kierunek powierzchni. Na przykład możemy zdefiniować kierunek kroju jako wektor z Centrum ujścia do Centrum oczu. Poniższy kod to szczegółowo opisano:

```CSharp
var landmarks = face.FaceLandmarks;
 
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

Znając kroju znajduje się w kierunku można obracać ramki prostokątne krój wyrównać kroju. Jest jasne, czy przy użyciu punkty orientacyjne krój zapewnić bardziej szczegółowe informacje i narzędzia.

## <a name="step4"></a> Krok 4: Przy użyciu innych atrybutów krój

Oprócz krój punkty orientacyjne, krój — wykryć interfejsu API można analizować również kilka atrybutów na powierzchnię. Te atrybuty obejmują:

- Wiek
- Płeć
- Intensywność uśmiechu
- Twarzy włosów
- Ułożenie head 3D

Te atrybuty są przewidzieć przy użyciu statystyczne algorytmów i mogą nie być dokładne 100%. Jednak są nadal przydatne umożliwia klasyfikowanie kroje przez te atrybuty. Aby uzyskać więcej informacji na temat każdego z atrybutów, zapoznaj się [słownik](../Glossary.md).

Poniżej przedstawiono prosty przykład wyodrębniania atrybuty krój podczas wykrywania twarzy na obrazie:
```CSharp
var requiredFaceAttributes = new FaceAttributeType[] {
                FaceAttributeType.Age,
                FaceAttributeType.Gender,
                FaceAttributeType.Smile,
                FaceAttributeType.FacialHair,
                FaceAttributeType.HeadPose,
                FaceAttributeType.Glasses
            };
var faces = await faceServiceClient.DetectAsync(imageUrl,
    returnFaceLandmarks: true,
    returnFaceAttributes: requiredFaceAttributes);

foreach (var face in faces)
{
    var id = face.FaceId;
    var attributes = face.FaceAttributes;
    var age = attributes.Age;
    var gender = attributes.Gender;
    var smile = attributes.Smile;
    var facialHair = attributes.FacialHair;
    var headPose = attributes.HeadPose;
    var glasses = attributes.Glasses;
}
``` 
## <a name="summary"></a> Podsumowanie

W tym przewodniku uzyskanych funkcje związane z [stają przed — wykrywanie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) interfejsu API, jak dla lokalnych przekazać obrazy lub adresy URL obrazów w sieci web może wykryć kroje; jak może wykryć kroje zwracając ramki prostokątne krój; i jak również mogą analizować punkty orientacyjne krój, 3D stanowi head i inne atrybuty twarzy na obrazie.

Aby uzyskać więcej informacji na temat interfejsu API szczegóły można znaleźć Podręcznik interfejsu API dla [stają przed — wykrywanie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

## <a name="related"></a> Tematy pokrewne

[Jak zidentyfikować skierowany w obrazie](HowtoIdentifyFacesinImage.md)
