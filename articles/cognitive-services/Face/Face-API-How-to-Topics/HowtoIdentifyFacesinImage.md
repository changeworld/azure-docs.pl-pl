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
ms.openlocfilehash: c22230545ccbe1ef1b4bfa35a33f0302197463b1
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/23/2019
ms.locfileid: "66124523"
---
# <a name="example-identify-faces-in-images"></a>Przykład: Identyfikowanie twarzy na obrazach

Ten przewodnik pokazuje, jak do identyfikowania nieznanych twarze za pomocą obiektów w grupie, które są tworzone z wyprzedzeniem od znanych osób. Przykłady są napisane w C# za pomocą biblioteki klienta usługi Azure Cognitive Services Face API.

## <a name="preparation"></a>Przygotowanie

W tym przykładzie przedstawiono:

- Jak utworzyć grupie. Tej grupie zawiera listę znanych osób.
- Jak przypisać twarzy do każdej osoby. Te powierzchnie służą jako punkt odniesienia do identyfikowania osób. Firma Microsoft zaleca używanie wyczyść czołowego widoków twarze. Przykładem jest identyfikator zdjęcia Dobry zestaw zdjęcia zawiera twarzy w tej samej osoby w różnych stwarza, kolory odzieży lub hairstyles.

Do przeprowadzenia pokazem tego przykładu, należy przygotować:

- Kilka zdjęć twarzy danej osoby. [Pobierz przykładowy zdjęcia](https://github.com/Microsoft/Cognitive-Face-Windows/tree/master/Data) Anna, rachunku i Clare.
- Seria testów zdjęcia. Zdjęcia, ale nie mogą zawierać twarzy Anna, rachunku lub Clare. Służą one do identyfikacji testu. Niektóre przykładowe obrazy można również wybrać z poprzednich łącza.

## <a name="step-1-authorize-the-api-call"></a>Krok 1: Autoryzowanie wywołania interfejsu API

Każde wywołanie do interfejsu API rozpoznawania twarzy wymaga klucza subskrypcji. Ten klucz można przekazany przez parametr ciągu zapytania lub określony w nagłówku żądania. Aby przekazać klucz subskrypcji za pomocą ciągu zapytania, zobacz adres URL żądania dla [twarzy — wykrywanie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) jako przykładu:
```
https://westus.api.cognitive.microsoft.com/face/v1.0/detect[?returnFaceId][&returnFaceLandmarks][&returnFaceAttributes]
&subscription-key=<Subscription key>
```

Alternatywnie, określ klucz subskrypcji w nagłówku żądania HTTP **ocp-apim-subscription-key: &lt;Klucz subskrypcji&gt;**.
Korzystając z biblioteki klienta, klucz subskrypcji jest przekazywane w konstruktorze klasy FaceServiceClient. Na przykład:
 
```CSharp 
faceServiceClient = new FaceServiceClient("<Subscription Key>");
```
 
Aby uzyskać klucz subskrypcji, przejdź do portalu Azure Marketplace w witrynie Azure portal. Aby uzyskać więcej informacji, zobacz [subskrypcje](https://azure.microsoft.com/try/cognitive-services/).

## <a name="step-2-create-the-persongroup"></a>Krok 2: Tworzenie elementu PersonGroup

W tym kroku grupie o nazwie "MyFriends" zawiera Anna, rachunku i Clare. Dla każdej osoby zarejestrowano kilka twarzy. Powierzchnie muszą zostać wykryte przy użyciu obrazów. Po wykonaniu tych czynności powinien istnieć element PersonGroup podobny do przedstawionego na ilustracji:

![MyFriends](../Images/group.image.1.jpg)

### <a name="step-21-define-people-for-the-persongroup"></a>Krok 2.1. Zdefiniuj osób na grupie
Osoba jest podstawową jednostką mającą tożsamość. Dla osoby można zarejestrować jedną lub wiele twarzy. Grupie to zbiór osób. Każda osoba jest zdefiniowana w określonej grupie. Identyfikacja odbywa się przed grupie. Zadanie jest utworzenie grupie i następnie utworzyć osób, takich jak Anna rachunku i Clare.

Najpierw utwórz nowe grupie za pomocą [grupie — Tworzenie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244) interfejsu API. W bibliotece klienta API odpowiada mu metoda CreatePersonGroupAsync klasy FaceServiceClient. Identyfikator grupy, który jest określony, aby utworzyć grupę jest unikatowa dla każdej subskrypcji. Możesz również można uzyskać, zaktualizować lub usunąć grup osób przy użyciu innych interfejsów API w grupie. 

Po zdefiniowaniu grupy, można zdefiniować osoby w niej za pomocą [osoba grupie - utworzyć](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c) interfejsu API. Jego odpowiednikiem w bibliotece klienta jest metoda CreatePersonAsync. Twarzy można dodać do każdej osoby, po ich utworzeniu.

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
### <a name="step2-2"></a> Krok 2.2: Wykrywanie twarzy i zarejestruj je do osoby, poprawny
Wykrywanie odbywa się przez wysłanie do interfejsu API [Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) żądania HTTP „POST” zawierającego w treści plik obrazu. Korzystając z biblioteki klienta, wykrywanie twarzy odbywa się za pośrednictwem metody DetectAsync dla klasy FaceServiceClient.

Dla każdej twarzy, która jest wykrywana wywołania [grupie osoby — Dodaj twarzy](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) Aby dodać ją do osoby poprawne.

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
Jeśli obraz zawiera więcej niż jeden twarzy, jest dodawana tylko największych twarzy. Możesz dodać inne twarzy do osoby. Przekaż ciąg w formacie "targetFace = po lewej stronie, top, szerokość, wysokość" do [grupie osoba — Dodaj twarzy](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) parametr zapytania targetFace interfejsów API. Możesz również użyć targetFace opcjonalnego parametru metody AddPersonFaceAsync można dodać inne twarzy. Każdej twarzy dodane do osoby, otrzymuje identyfikator unikatowy utrwalonych twarzy Możesz użyć tego Identyfikatora w [grupie osoby — usuwać Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523e) i [twarzy — zidentyfikować](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239).

## <a name="step-3-train-the-persongroup"></a>Krok 3: Szkolenie elementu PersonGroup

Musi być uczony grupie, przed wykonaniem za pomocą jego identyfikatora. Musi być retrained grupie, po dodaniu lub usunięciu każda osoba, lub Jeśli edytujesz zarejestrowanych twarzy osoby. Do szkolenia służy interfejs API [PersonGroup – Train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249). Korzystając z biblioteki klienta jest wywołanie metody TrainPersonGroupAsync:
 
```CSharp 
await faceServiceClient.TrainPersonGroupAsync(personGroupId);
```
 
Szkolenie jest proces asynchroniczny. Nie może być zakończona nawet w przypadku, po powrocie z metody TrainPersonGroupAsync. Konieczne może wykonać zapytania o stan szkolenia. Użyj [grupie — pobieranie stanu w szkolenia](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395247) metody interfejsu API lub GetPersonGroupTrainingStatusAsync biblioteki klienta. Poniższy kod przedstawia prostą logikę czekać na grupie szkolenia na zakończenie:
 
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

Gdy interfejs API rozpoznawania twarzy wykonuje identyfikatory, oblicza podobieństwo twarzy testów wśród wszystkich powierzchni w obrębie grupy. Zwraca najbardziej porównywalne osób testowania twarzy. Ten proces odbywa się za pośrednictwem [twarzy — ustalenie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) interfejsu API lub metoda IdentifyAsync biblioteki klienta.

Testowanie rozpoznawania twarzy, muszą zostać wykryte za pomocą poprzedniej procedury. Następnie funkcji face ID jest przekazywany do interfejsu API identyfikacji jako drugi argument. Jednocześnie można zidentyfikować twarzy wiele identyfikatorów. Wynik zawiera wszystkie zidentyfikowane wyniki. Domyślnie proces identyfikacji zwraca tylko jedna osoba, która najlepiej odpowiada powierzchni testu. Jeśli wolisz, określ maxNumOfCandidatesReturned opcjonalnego parametru, aby umożliwić proces identyfikacji zwrócić więcej kandydatów.

Poniższy kod przedstawia proces identyfikacji:

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

Po zakończeniu kroków, spróbuj zidentyfikować różnych powierzchni. Zobacz, jeśli twarze Anna, rachunku lub Clare może poprawnie zidentyfikować zgodnie z obrazy przekazane do wykrywania twarzy. Zobacz poniższe przykłady:

![Identyfikowanie różnych twarzy](../Images/identificationResult.1.jpg )

## <a name="step-5-request-for-large-scale"></a>Krok 5. Żądanie na dużą skalę

Grupie może zawierać maksymalnie 10 000 osób, oparte na poprzednie ograniczenie projektu.
Aby uzyskać informacje o zastosowaniach obejmujących nawet miliony osób, zobacz [Jak używać funkcji na dużą skalę](how-to-use-large-scale.md).

## <a name="summary"></a>Podsumowanie

W tym przewodniku przedstawiono proces tworzenia grupie i identyfikacji osoby. Następujące funkcje zostały wyjaśnione i przedstawione w artykule:

- Wykrywanie twarzy przy użyciu [twarzy — wykrywanie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d) interfejsu API.
- Tworzenie grup osób przy użyciu [grupie — Tworzenie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244) interfejsu API.
- Utwórz osoby za pomocą [osoba grupie - utworzyć](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c) interfejsu API.
- Szkolenie grupie za pomocą [grupie – Train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249) interfejsu API.
- Identyfikowanie twarzy nieznany względem grupie za pomocą [twarzy — ustalenie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) interfejsu API.

## <a name="related-topics"></a>Tematy pokrewne

- [Pojęcia rozpoznawania twarzy](../concepts/face-recognition.md)
- [Wykrywanie twarzy na obrazie](HowtoDetectFacesinImage.md)
- [Dodaj twarzy](how-to-add-faces.md)
- [Użyj funkcji na dużą skalę](how-to-use-large-scale.md)
