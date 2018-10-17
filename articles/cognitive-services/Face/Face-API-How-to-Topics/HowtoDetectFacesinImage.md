---
title: 'Przykład: wykrywanie twarzy na obrazach — interfejs API rozpoznawania twarzy'
titleSuffix: Azure Cognitive Services
description: Interfejs API rozpoznawania twarzy umożliwia wykrywanie twarzy na obrazach.
services: cognitive-services
author: SteveMSFT
manager: cgronlun
ms.service: cognitive-services
ms.component: face-api
ms.topic: sample
ms.date: 03/01/2018
ms.author: sbowles
ms.openlocfilehash: a4c74ff70a4426abf97562bf997479a91afbf17a
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/18/2018
ms.locfileid: "46124052"
---
# <a name="example-how-to-detect-faces-in-image"></a>Przykład: jak wykrywać twarze na obrazie

Ten przewodnik pokazuje, jak wykrywać twarze na obrazie przy użyciu atrybutów twarzy, takich jak płeć, wiek lub przyjęta postawa. Przykłady są zapisywane w języku C# przy użyciu biblioteki klienta interfejsu API rozpoznawania twarzy. 

## <a name="concepts"></a>Pojęcia

Jeśli nie znasz poniższych pojęć z tego przewodnika, w dowolnym momencie zapoznaj się z definicjami dostępnymi w naszym [słowniku](../Glossary.md): 

- Wykrywanie twarzy
- Punkty charakterystyczne twarzy
- Ułożenie głowy
- Atrybuty twarzy

## <a name="preparation"></a>Przygotowanie

W tym przykładzie zostaną przedstawione następujące funkcje: 

- Wykrywanie twarzy na obrazie i oznaczanie ich przy użyciu prostokątnych ramek
- Analizowanie lokalizacji źrenic, nosa lub ust i oznaczanie ich na obrazie
- Analizowanie ułożenia głowy, płci i wieku twarzy

Aby użyć tych funkcji, należy przygotować obraz z co najmniej jedną czytelną twarzą. 

## <a name="step-1-authorize-the-api-call"></a>Krok 1. Autoryzowanie wywołania interfejsu API

Każde wywołanie do interfejsu API rozpoznawania twarzy wymaga klucza subskrypcji. Ten klucz musi zostać albo przekazany przez parametr ciągu zapytania, albo określony w nagłówku żądania. Aby przekazać klucz subskrypcji w ciągu zapytania, użyj jako przykładu adresu URL funkcji [Twarz — wykrywanie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236):

```
https://westus.api.cognitive.microsoft.com/face/v1.0/detect[?returnFaceId][&returnFaceLandmarks][&returnFaceAttributes]
&subscription-key=<Subscription Key>
```

Alternatywnie klucz subskrypcji można również określić w nagłówku żądania HTTP: **ocp-apim-subscription-key: &lt;klucz subskrypcji&gt;**. W przypadku korzystania z biblioteki klienckiej klucz subskrypcji jest przekazywany klucz za pomocą konstruktora klasy FaceServiceClient. Na przykład:
```CSharp
faceServiceClient = new FaceServiceClient("<Subscription Key>");
```

## <a name="step-2-upload-an-image-to-the-service-and-execute-face-detection"></a>Krok 2. Przekazywanie obrazu do usługi i wykonywanie wykrywania twarzy

Najprostszym sposobem wykrywania twarzy jest bezpośrednie przekazywanie obrazu. Odbywa się to przez wysłanie żądania „POST” z typem zawartości application/octet-stream z danymi odczytanymi z obrazu JPEG. Maksymalny rozmiar obrazu to 4 MB.

Gdy używana jest biblioteka kliencka, wykrywanie twarzy za pomocą przekazywania jest realizowane przez przekazanie obiektu strumienia. Zapoznaj się z poniższym przykładem:

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

Pamiętaj, że metoda DetectAsync klasy FaceServiceClient jest asynchroniczna. Także metoda wywołująca powinna być oznaczona jako asynchroniczna, aby można było użyć klauzuli await.
Jeśli obraz znajduje się już w Internecie i ma adres URL, wykrywanie twarzy można również wykonać, podając adres URL. W tym przykładzie treścią żądania będzie ciąg JSON, który zawiera adres URL.
Gdy używana jest biblioteka kliencka, wykrywanie twarzy za pomocą adresu URL można łatwo wykonać, używając innego obciążenia metody DetectAsync.

```CSharp
string imageUrl = "http://news.microsoft.com/ceo/assets/photos/06_web.jpg";
var faces = await faceServiceClient.DetectAsync(imageUrl, true, true);
 
foreach (var face in faces)
{
    var rect = face.FaceRectangle;
    var landmarks = face.FaceLandmarks;
}
``` 

Właściwość FaceRectangle zwracana z wykrytymi twarzami to w zasadzie lokalizacje na twarzy w pikselach. Zazwyczaj ten prostokąt zawiera oczy, brwi, nos i usta — górna część głowy, uszy i podbródek nie są uwzględniane. Jeśli przycinasz cały portret głowy lub portret od pasa w górę (obraz typu zdjęcie do dokumentu tożsamości), możesz rozszerzyć obszar prostokątnej ramki twarzy, ponieważ obszar twarzy może być za mały dla niektórych aplikacji. Aby dokładniej zlokalizować twarz, warto użyć jej punktów charakterystycznych (mechanizmów lokalizowania rysów twarzy lub kierunku twarzy) w sposób opisany w następnej sekcji.

## <a name="step-3-understanding-and-using-face-landmarks"></a>Krok 3. Opis punktów charakterystycznych twarzy i korzystanie z nich

Punkty charakterystyczne twarzy to zbiór szczegółowych punktów na twarzy. Są to zazwyczaj elementy, takie jak źrenice, kąciki oczu i nos. Punkty charakterystyczne to opcjonalne atrybuty, które można analizować podczas wykrywania twarzy. Można przekazać wartość „true” jako wartość logiczną do parametru zapytania returnFaceLandmarks podczas wywoływania elementu [Twarz — wykrywanie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) lub użyć opcjonalnego parametru returnFaceLandmarks dla metody DetectAsync klasy FaceServiceClient, aby uwzględnić punkty charakterystyczne w wynikach wykrywania.

Domyślnie istnieje 27 wstępnie zdefiniowanych punktów charakterystycznych. Na poniższej ilustracji przedstawiono sposób definiowania wszystkich 27 punktów:

![HowToDetectFace](../Images/landmarks.1.jpg)

Punkty są zwracane przy użyciu jednostek pikseli, podobnie jak prostokątna ramka twarzy. Ułatwia to oznaczanie określonych punktów orientacyjnych na obrazie. Poniższy kod przedstawia sposób pobierania lokalizacji nosa i źrenic:

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

Oprócz oznaczania rysów twarzy na obrazie, punkty charakterystyczne pozwalają również na dokładne obliczanie kierunku twarzy. Możemy na przykład zdefiniować kierunek twarzy od środka ust do środka oczu. Poniższy kod opisuje to szczegółowo:

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

Po zidentyfikowaniu kierunku twarzy można obrócić prostokątną ramkę twarzy, aby wyrównać ją z twarzą. Jest oczywiste, że korzystanie z punktów charakterystycznych twarzy oferuje większą szczegółowość i użyteczność.

## <a name="step-4-using-other-face-attributes"></a>Krok 4. Korzystanie z innych atrybutów twarzy

Poza punktami charakterystycznymi interfejs API wykrywania twarzy może również analizować inne atrybuty twarzy. Są to następujące elementy:

- Wiek
- Płeć
- Intensywność uśmiechu
- Zarost
- Ułożenie głowy w 3D

Te atrybuty są przewidywane przy użyciu algorytmów statystycznych i nie zawsze są dokładne w 100%. Są jednak one nadal pomocne w przypadku klasyfikowania twarzy przy użyciu tych atrybutów. Więcej informacji na temat każdego z atrybutów można znaleźć w [słowniku](../Glossary.md).

Poniżej przedstawiono prosty przykład wyodrębniania atrybutów twarzy podczas wykrywania twarzy:

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

## <a name="summary"></a>Podsumowanie

W tym przewodniku przedstawiliśmy funkcje interfejsu API [Twarz — wykrywanie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236); sposób wykrywania twarzy na obrazach przekazanych lokalnie lub przy użyciu adresów URL obrazów w Internecie; sposób wykrywania twarzy przez zwracanie prostokątnych ramek twarzy oraz sposób analizowania punktów charakterystycznych twarzy, ułożenia głowy w 3D i innych atrybutów twarzy.

Więcej informacji na temat szczegółów interfejsu API można znaleźć w podręczniku interfejsu API funkcji [Twarz — wykrywanie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

## <a name="related-topics"></a>Tematy pokrewne

[How to Identify Faces in Image](HowtoIdentifyFacesinImage.md) (Jak identyfikować twarze na obrazie)
