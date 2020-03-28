---
title: 'Przykład: Identyfikowanie twarzy na obrazach - Twarz'
titleSuffix: Azure Cognitive Services
description: W tym przewodniku pokazano, jak zidentyfikować nieznane twarze przy użyciu persongroup obiektów, które są tworzone z znanych osób z wyprzedzeniem.
services: cognitive-services
author: SteveMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: sample
ms.date: 04/10/2019
ms.author: sbowles
ms.openlocfilehash: 0b1cf99fe6e2aa4d7fcb12c3fb96b10b42c7c0b7
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76169914"
---
# <a name="example-identify-faces-in-images"></a>Przykład: Identyfikowanie twarzy na obrazach

W tym przewodniku pokazano, jak zidentyfikować nieznane twarze przy użyciu persongroup obiektów, które są tworzone z znanych osób z wyprzedzeniem. Przykłady są zapisywane w języku C# przy użyciu biblioteki klienta usługi Azure Cognitive Services Face.

## <a name="preparation"></a>Przygotowywanie

W tym przykładzie pokazano:

- Jak utworzyć grupę osób. Ta grupa person zawiera listę znanych osób.
- Jak przypisać twarze do każdej osoby. Te twarze są używane jako punkt odniesienia do identyfikacji osób. Zaleca się używanie wyraźnych widoków czołowych twarzy. Przykładem jest identyfikator zdjęcia. Dobry zestaw zdjęć zawiera twarze tej samej osoby w różnych pozach, kolorach odzieży lub fryzurach.

Aby przeprowadzić demonstrację tej próbki, przygotować:

- Kilka zdjęć twarzy danej osoby. [Pobierz przykładowe zdjęcia](https://github.com/Microsoft/Cognitive-Face-Windows/tree/master/Data) dla Anny, Billa i Klary.
- Seria zdjęć testowych. Zdjęcia mogą, ale nie, nie zawierają twarzy Anny, Billa lub Klary. Są one używane do testowania identyfikacji. Wybierz również przykładowe obrazy z poprzedniego łącza.

## <a name="step-1-authorize-the-api-call"></a>Krok 1: Autoryzowanie wywołania interfejsu API

Każde wywołanie do interfejsu API rozpoznawania twarzy wymaga klucza subskrypcji. Ten klucz może być przekazywany przez parametr ciągu zapytania lub określony w nagłówku żądania. Aby przekazać klucz subskrypcji za pośrednictwem ciągu zapytania, zobacz adres URL żądania dla [face — detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) jako przykład:
```
https://westus.api.cognitive.microsoft.com/face/v1.0/detect[?returnFaceId][&returnFaceLandmarks][&returnFaceAttributes]
&subscription-key=<Subscription key>
```

Alternatywnie należy określić klucz subskrypcji w nagłówku żądania HTTP **ocp-apim-subscription-key: &lt;Klucz&gt;subskrypcji**.
Korzystając z biblioteki klienta, klucz subskrypcji jest przekazywany za pośrednictwem konstruktora FaceClient klasy. Przykład:
 
```csharp 
private readonly IFaceClient faceClient = new FaceClient(
            new ApiKeyServiceClientCredentials("<subscription key>"),
            new System.Net.Http.DelegatingHandler[] { });
```
 
Aby uzyskać klucz subskrypcji, przejdź do portalu Azure Marketplace z witryny Azure. Aby uzyskać więcej informacji, zobacz [Subskrypcje](https://azure.microsoft.com/try/cognitive-services/).

## <a name="step-2-create-the-persongroup"></a>Krok 2: Tworzenie elementu PersonGroup

W tym kroku persongroup o nazwie "MyFriends" zawiera Anna, Bill i Clare. Dla każdej osoby zarejestrowano kilka twarzy. Twarze muszą być wykryte na podstawie obrazów. Po wykonaniu tych czynności powinien istnieć element PersonGroup podobny do przedstawionego na ilustracji:

![Znajomi MyFriends](../Images/group.image.1.jpg)

### <a name="step-21-define-people-for-the-persongroup"></a>Krok 2.1: Definiowanie osób dla grupy osób
Osoba jest podstawową jednostką mającą tożsamość. Dla osoby można zarejestrować jedną lub wiele twarzy. A PersonGroup to zbiór osób. Każda osoba jest zdefiniowana w ramach określonej grupy osób. Identyfikacja odbywa się względem grupy osób. Zadaniem jest utworzenie grupy osób, a następnie utworzenie osób w niej, takich jak Anna, Bill i Clare.

Najpierw utwórz nową grupę osób przy użyciu [persongroup - Tworzenie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244) interfejsu API. Odpowiedni interfejs API biblioteki klienta jest CreatePersonGroupAsync metody dla FaceClient klasy. Identyfikator grupy określony do utworzenia grupy jest unikatowy dla każdej subskrypcji. Można również uzyskać, zaktualizować lub usunąć persongroups przy użyciu innych interfejsów API persongroup. 

Po zdefiniowaniu grupy można zdefiniować osoby w jej obrębie za pomocą [persongroup person - Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c) API. Jego odpowiednikiem w bibliotece klienta jest metoda CreatePersonAsync. Po jej utworzeniu możesz dodać twarz do każdej osoby.

```csharp 
// Create an empty PersonGroup
string personGroupId = "myfriends";
await faceClient.PersonGroup.CreateAsync(personGroupId, "My Friends");
 
// Define Anna
CreatePersonResult friend1 = await faceClient.PersonGroupPerson.CreateAsync(
    // Id of the PersonGroup that the person belonged to
    personGroupId,    
    // Name of the person
    "Anna"            
);
 
// Define Bill and Clare in the same way
```
### <a name="step-22-detect-faces-and-register-them-to-the-correct-person"></a><a name="step2-2"></a>Krok 2.2: Wykrywanie twarzy i rejestrowanie ich właściwej osobie
Wykrywanie odbywa się przez wysłanie do interfejsu API [Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) żądania HTTP „POST” zawierającego w treści plik obrazu. Podczas korzystania z biblioteki klienta wykrywanie twarzy odbywa się za pośrednictwem jednego z Detect.. Metody asynchronizowe faceclient klasy.

Dla każdej wykrytej twarzy wywołaj osobę [persongroup persongroup — dodaj twarz,](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) aby dodać ją do właściwej osoby.

Poniższy kod pokazuje sposób wykrywania twarzy na obrazie i dodawania jej do osoby:

```csharp 
// Directory contains image files of Anna
const string friend1ImageDir = @"D:\Pictures\MyFriends\Anna\";
 
foreach (string imagePath in Directory.GetFiles(friend1ImageDir, "*.jpg"))
{
    using (Stream s = File.OpenRead(imagePath))
    {
        // Detect faces in the image and add to Anna
        await faceClient.PersonGroupPerson.AddFaceFromStreamAsync(
            personGroupId, friend1.PersonId, s);
    }
}
// Do the same for Bill and Clare
``` 
Jeśli obraz zawiera więcej niż jedną ścianę, dodawana jest tylko największa ściana. Do osoby można dodać inne twarze. Przekaż ciąg w formacie "targetFace = left, top, width, height" do [PersonGroup Person Person - Dodaj](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) parametr zapytania targetFace interfejsu API twarzy. Można również użyć targetFace opcjonalny parametr dla AddPersonFaceAsync metody, aby dodać inne ściany. Każda twarz dodana do danej osoby otrzymuje unikalny identyfikator twarzy. Tego identyfikatora można użyć w [persongroup persongroup persongroup – Delete Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523e) and [Face – Identify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239).

## <a name="step-3-train-the-persongroup"></a>Krok 3: Szkolenie elementu PersonGroup

PersonGroup musi być przeszkolony, zanim można wykonać identyfikację za pomocą niego. PersonGroup musi zostać przeszkolony po dodaniu lub usunięciu dowolnej osoby lub w przypadku edytowania zarejestrowanej twarzy danej osoby. Do szkolenia służy interfejs API [PersonGroup – Train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249). Korzystając z biblioteki klienta, jest to wywołanie TrainPersonGroupAsync metody:
 
```csharp 
await faceClient.PersonGroup.TrainAsync(personGroupId);
```
 
Szkolenie jest procesem asynchronizacyjnym. Może nie być zakończona nawet po TrainPersonGroupAsync metoda zwraca. Może być konieczne zapytanie o stan szkolenia. Użyj [persongroup — Pobierz stan szkolenia](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395247) interfejsu API lub GetPersonGroupTrainingStatusAsync metody biblioteki klienta. Poniższy kod demonstruje prostą logikę oczekiwania na zakończenie szkolenia persongroup:
 
```csharp 
TrainingStatus trainingStatus = null;
while(true)
{
    trainingStatus = await faceClient.PersonGroup.GetTrainingStatusAsync(personGroupId);
 
    if (trainingStatus.Status != TrainingStatusType.Running)
    {
        break;
    }
 
    await Task.Delay(1000);
} 
``` 

## <a name="step-4-identify-a-face-against-a-defined-persongroup"></a>Krok 4: Identyfikowanie twarzy przy użyciu zdefiniowanego elementu PersonGroup

Gdy usługa Face wykonuje identyfikatory, oblicza podobieństwo ściany testowej między wszystkimi ścianami w grupie. Zwraca najbardziej porównywalne osoby do badania twarzy. Ten proces odbywa się za pośrednictwem [Face — Identyfikowanie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) interfejsu API lub IdentifyAsync metody biblioteki klienta.

Ściany testowej należy wykryć przy użyciu poprzednich kroków. Następnie identyfikator twarzy jest przekazywany do interfejsu API identyfikacji jako drugi argument. Można jednocześnie zidentyfikować wiele identyfikatorów twarzy. Wynik zawiera wszystkie zidentyfikowane wyniki. Domyślnie proces identyfikacji zwraca tylko jedną osobę, która najlepiej pasuje do ściany testowej. Jeśli wolisz, określ parametr opcjonalny maxNumOfCandidatesPowwrócony, aby umożliwić procesowi identyfikacji zwracanie większej liczby kandydatów.

Poniższy kod demonstruje proces identyfikacji:

```csharp 
string testImageFile = @"D:\Pictures\test_img1.jpg";

using (Stream s = File.OpenRead(testImageFile))
{
    var faces = await faceClient.Face.DetectWithStreamAsync(s);
    var faceIds = faces.Select(face => face.FaceId.Value).ToArray();
 
    var results = await faceClient.Face.IdentifyAsync(faceIds, personGroupId);
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
            var person = await faceClient.PersonGroupPerson.GetAsync(personGroupId, candidateId);
            Console.WriteLine("Identified as {0}", person.Name);
        }
    }
}
``` 

Po zakończeniu kroków spróbuj zidentyfikować różne twarze. Sprawdź, czy twarze Anny, Billa lub Klary mogą być poprawnie zidentyfikowane zgodnie z obrazami przesłanym do wykrywania twarzy. Zobacz poniższe przykłady:

![Identyfikowanie różnych twarzy](../Images/identificationResult.1.jpg )

## <a name="step-5-request-for-large-scale"></a>Krok 5: Wniosek o dużą skalę

Grupa osób może pomieścić do 10 000 osób na podstawie poprzedniego ograniczenia projektu.
Aby uzyskać informacje o zastosowaniach obejmujących nawet miliony osób, zobacz [Jak używać funkcji na dużą skalę](how-to-use-large-scale.md).

## <a name="summary"></a>Podsumowanie

W tym przewodniku poznaliście proces tworzenia grupy osób i identyfikowania osoby. Wyjaśniono i wykazano następujące cechy:

- Wykrywanie twarzy za pomocą [interfejsu API face — wykrywania.](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d)
- Tworzenie grup osób przy użyciu [grupy osób — tworzenie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244) interfejsu API.
- Tworzenie osób przy użyciu [persongroup persongroup - Tworzenie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c) interfejsu API.
- Trenuj grupę osób za pomocą [interfejsu API persongroup — train.](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249)
- Identyfikowanie nieznanych twarzy względem grupy osób za pomocą [interfejsu API Face — identyfikowanie.](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239)

## <a name="related-topics"></a>Powiązane tematy

- [Pojęcia rozpoznawania twarzy](../concepts/face-recognition.md)
- [Wykrywanie twarzy na obrazie](HowtoDetectFacesinImage.md)
- [Dodawanie twarzy](how-to-add-faces.md)
- [Korzystanie z funkcji na dużą skalę](how-to-use-large-scale.md)
