---
title: 'Przykład: Identyfikowanie twarzy na obrazach — interfejs API rozpoznawania twarzy'
titleSuffix: Azure Cognitive Services
description: Identyfikowanie twarzy na obrazach za pomocą interfejsu API rozpoznawania twarzy.
services: cognitive-services
author: SteveMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: sample
ms.date: 04/10/2019
ms.author: sbowles
ms.openlocfilehash: 7da146cafaf9af5c91bbbb2a3a23d8a90d49d8cd
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64699344"
---
# <a name="example-identify-faces-in-images"></a>Przykład: Identyfikowanie twarzy na obrazach

Ten przewodnik pokazuje sposób identyfikowania nieznanych twarzy przy użyciu elementów PersonGroup, które należy utworzyć wcześniej na podstawie zdjęć znanych osób. Przykłady są zapisywane w języku C# przy użyciu biblioteki klienta interfejsu API rozpoznawania twarzy.

## <a name="preparation"></a>Przygotowanie

W tym przykładzie pokażemy:

- Jak utworzyć element PersonGroup, który zawiera listę znanych osób.
- Jak przypisać do poszczególnych osób twarze, na podstawie których te osoby będą identyfikowane. Zalecane jest używanie wyraźnych, frontalnych zdjęć twarzy — takich jak w dowodzie osobistym. Dobry zbiór zdjęć powinien zawierać zdjęcia twarzy jednej osoby w różnych pozach, fryzurach i ubraniach o różnych kolorach.

Aby zademonstrować ten przykład, musisz przygotować zdjęcia:

- Kilka zdjęć twarzy danej osoby. [Pobierz przykładowy zdjęcia](https://github.com/Microsoft/Cognitive-Face-Windows/tree/master/Data) Anna, rachunku i Clare.
- Seria używanych do testowania identyfikacji zdjęć testowych, na których znajdują lub nie znajdują się twarze Anny, Billa lub Clare. Przykładowe zdjęcia można też pobrać, używając powyższego łącza.

## <a name="step-1-authorize-the-api-call"></a>Krok 1: Autoryzowanie wywołania interfejsu API

Każde wywołanie do interfejsu API rozpoznawania twarzy wymaga klucza subskrypcji. Ten klucz można przekazywać jako parametr ciągu zapytania albo umieszczać w nagłówku żądania. Aby przekazać klucz subskrypcji w ciągu zapytania, użyj jako przykładu adresu URL żądania [Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236):
```
https://westus.api.cognitive.microsoft.com/face/v1.0/detect[?returnFaceId][&returnFaceLandmarks][&returnFaceAttributes]
&subscription-key=<Subscription key>
```

Klucz subskrypcji możesz też określić w nagłówku żądania HTTP: **ocp-apim-subscription-key: &lt;klucz subskrypcji&gt;** Jeśli korzystasz z biblioteki klienta, klucz subskrypcji jest przekazywany w konstruktorze klasy FaceServiceClient. Na przykład:
 
```CSharp 
faceServiceClient = new FaceServiceClient("<Subscription Key>");
```
 
Klucz subskrypcji można uzyskać na stronie Marketplace witryny Azure Portal. Zobacz [Subskrypcje](https://azure.microsoft.com/try/cognitive-services/).

## <a name="step-2-create-the-persongroup"></a>Krok 2: Tworzenie elementu PersonGroup

W tym kroku tworzymy element PersonGroup o nazwie „MyFriends” zawierający trzy osoby: Annę, Billa i Clare. Dla każdej osoby zarejestrowano kilka twarzy. Twarze muszą zostać wykryte na zdjęciach. Po wykonaniu tych czynności powinien istnieć element PersonGroup podobny do przedstawionego na ilustracji:

![JakIdentyfikować1](../Images/group.image.1.jpg)

### <a name="21-define-people-for-the-persongroup"></a>2.1. Definiowanie osób w PersonGroup
Osoba jest podstawową jednostką mającą tożsamość. Dla osoby można zarejestrować jedną lub wiele twarzy. PersonGroup to zbiór osób, a każda osoba jest definiowana w określonym elemencie PersonGroup. Identyfikacja jest wykonywana względem PersonGroup. Naszym zadaniem jest utworzenie elementu PersonGroup, a następnie wewnątrz niego osób, takich jak Anna, Bill i Clare.

Najpierw należy utworzyć nowy element PersonGroup. Służy do tego interfejs API [PersonGroup - Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244). W bibliotece klienta API odpowiada mu metoda CreatePersonGroupAsync klasy FaceServiceClient. Identyfikator grupy podany podczas tworzenia grupy jest unikalny w obrębie subskrypcji — elementy PersonGroup można również pobierać, modyfikować i usuwać za pomocą innych interfejsów API. Po utworzeniu grupy można w niej zdefiniować osoby za pomocą interfejsu API [PersonGroup Person - Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c). Jego odpowiednikiem w bibliotece klienta jest metoda CreatePersonAsync. Po utworzeniu osoby można dodać jej twarze.

```CSharp 
// Create an empty PersonGroup
string personGroupId = "myfriends";
await faceServiceClient.CreatePersonGroupAsync(personGroupId, "My Friends");
 
// Define Anna
CreatePersonResult friend1 = await faceServiceClient.CreatePersonAsync(
    // Id of the PersonGroup that the person belonged to
    personGroupId,    
    // Name of the person
    "Anna"            
);
 
// Define Bill and Clare in the same way
```
### <a name="step2-2"></a> 2.2 Wykrywanie twarzy i przypisywanie ich do właściwych osób
Wykrywanie odbywa się przez wysłanie do interfejsu API [Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) żądania HTTP „POST” zawierającego w treści plik obrazu. W przypadku używania biblioteki klienta służy do tego metoda DetectAsync klasy FaceServiceClient.

Dla każdej wykrytej twarzy można wywołać interfejs API [PersonGroup Person – Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b), aby dodać ją do odpowiedniej osoby.

Poniższy kod pokazuje sposób wykrywania twarzy na obrazie i dodawania jej do osoby:
```CSharp 
// Directory contains image files of Anna
const string friend1ImageDir = @"D:\Pictures\MyFriends\Anna\";
 
foreach (string imagePath in Directory.GetFiles(friend1ImageDir, "*.jpg"))
{
    using (Stream s = File.OpenRead(imagePath))
    {
        // Detect faces in the image and add to Anna
        await faceServiceClient.AddPersonFaceAsync(
            personGroupId, friend1.PersonId, s);
    }
}
// Do the same for Bill and Clare
``` 
Pamiętaj, że jeśli obraz zawiera wiele twarzy, dodana zostanie tylko największa. Do osoby można dodać inne twarze, przekazując ciąg o formacie „targetFace = lewo, góra, szerokość, wysokość” do parametru zapytania targetFace interfejsu API [PersonGroup Person - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b), albo używając opcjonalnego parametru targetFace metody AddPersonFaceAsync. Każda twarz dodana do osoby otrzyma stały identyfikator, którego można używać w wywołaniach [PersonGroup Person – Delete Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523e) i [Face – Identify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239).

## <a name="step-3-train-the-persongroup"></a>Krok 3: Szkolenie elementu PersonGroup

Element PersonGroup musi najpierw zostać wyszkolony, zanim będzie można go używać do identyfikowania. Ponadto szkolenie trzeba powtórzyć po dodaniu lub usunięciu osoby albo edytowaniu twarzy którejś z osób. Do szkolenia służy interfejs API [PersonGroup – Train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249). W przypadku używania biblioteki klienta należy wywołać metodę TrainPersonGroupAsync:
 
```CSharp 
await faceServiceClient.TrainPersonGroupAsync(personGroupId);
```
 
Szkolenie jest procesem asynchronicznym. Zakończenie wykonywania metody TrainPersonGroupAsync nie oznacza zakończenia szkolenia. Konieczne może być sprawdzenie stanu szkolenia przez wywołanie interfejsu API [PersonGroup - Get Training Status](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395247) lub metody GetPersonGroupTrainingStatusAsync z biblioteki klienta. Poniższy kod prezentuje prostą logikę oczekiwania na zakończenie szkolenia elementu PersonGroup:
 
```CSharp 
TrainingStatus trainingStatus = null;
while(true)
{
    trainingStatus = await faceServiceClient.GetPersonGroupTrainingStatusAsync(personGroupId);
 
    if (trainingStatus.Status != Status.Running)
    {
        break;
    }
 
    await Task.Delay(1000);
} 
``` 

## <a name="step-4-identify-a-face-against-a-defined-persongroup"></a>Krok 4: Identyfikowanie twarzy przy użyciu zdefiniowanego elementu PersonGroup

Podczas identyfikowania interfejs API rozpoznawania twarzy może obliczyć podobieństwo sprawdzanej twarzy do wszystkich twarzy w grupie i zwrócić najbardziej podobną osobę (lub kilka osób). Służy do tego interfejs API [Face - Identify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) lub metoda IdentifyAsync z biblioteki klienta.

Najpierw należy wykryć sprawdzaną twarz w sposób opisany w poprzednich krokach, a następnie przekazać jej identyfikator do interfejsu API identyfikowania twarzy jako drugi argument. Można identyfikować jednocześnie wiele identyfikatorów twarzy — zwrócone zostaną wszystkie wyniki identyfikacji. Domyślnie identyfikowanie zwraca tylko jedną osobę, która najbardziej pasuje do sprawdzanej twarzy. Można jednak podać opcjonalny parametr maxNumOfCandidatesReturned, aby uzyskać w odpowiedzi więcej kandydatów.

Poniższy kod pokazuje proces identyfikowania:

```CSharp 
string testImageFile = @"D:\Pictures\test_img1.jpg";

using (Stream s = File.OpenRead(testImageFile))
{
    var faces = await faceServiceClient.DetectAsync(s);
    var faceIds = faces.Select(face => face.FaceId).ToArray();
 
    var results = await faceServiceClient.IdentifyAsync(personGroupId, faceIds);
    foreach (var identifyResult in results)
    {
        Console.WriteLine("Result of face: {0}", identifyResult.FaceId);
        if (identifyResult.Candidates.Length == 0)
        {
            Console.WriteLine("No one identified");
        }
        else
        {
            // Get top 1 among all candidates returned
            var candidateId = identifyResult.Candidates[0].PersonId;
            var person = await faceServiceClient.GetPersonAsync(personGroupId, candidateId);
            Console.WriteLine("Identified as {0}", person.Name);
        }
    }
}
``` 

Po wykonaniu tych kroków możesz spróbować zidentyfikować różne twarze i sprawdzić, czy twarze Anny, Billa i Clare można poprawnie zidentyfikować na podstawie zdjęć przesłanych do interfejsu wykrywania twarzy. Zobacz poniższe przykłady:

![JakIdentyfikować2](../Images/identificationResult.1.jpg )

## <a name="step-5-request-for-large-scale"></a>Krok 5. Dostęp wersji działającej na większych zbiorach

Element PersonGroup może zawierać maksymalnie 10 000 osób z powodu ograniczeń wcześniejszego projektu usługi.
Aby uzyskać informacje o zastosowaniach obejmujących nawet miliony osób, zobacz [Jak używać funkcji na dużą skalę](how-to-use-large-scale.md).

## <a name="summary"></a>Podsumowanie

W tym przewodniku przedstawiono proces tworzenia elementu PersonGroup i identyfikowania osoby. Oto krótkie przypomnienie funkcji wcześniej opisanych i użytych w przykładach:

- Wykrywanie twarzy przy użyciu interfejsu API [Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d)
- Tworzenie elementów PersonGroup za pomocą interfejsu API [PersonGroup - Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244)
- Tworzenie osób za pomocą interfejsu API [PersonGroup Person - Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c)
- Trenowanie elementu PersonGroup za pomocą interfejsu API [PersonGroup - Train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249)
- Identyfikowanie nieznanych twarzy z użyciem elementu PersonGroup za pomocą interfejsu API [Face - Identify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239)

## <a name="related-topics"></a>Tematy pokrewne

- [Pojęcia rozpoznawania twarzy](../concepts/face-recognition.md)
- [Wykrywanie twarzy na obrazie](HowtoDetectFacesinImage.md)
- [Jak dodawać twarze](how-to-add-faces.md)
- [Jak używać funkcji na dużą skalę](how-to-use-large-scale.md)
