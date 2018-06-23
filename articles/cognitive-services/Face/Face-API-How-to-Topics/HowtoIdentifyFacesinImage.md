---
title: Identyfikowanie powierzchni obrazów przy użyciu interfejsu API krój | Dokumentacja firmy Microsoft
titleSuffix: Microsoft Cognitive Services
description: Za pomocą interfejsu API krój w usługach kognitywnych zidentyfikować powierzchni obrazów.
services: cognitive-services
author: SteveMSFT
manager: corncar
ms.service: cognitive-services
ms.component: face-api
ms.topic: article
ms.date: 03/01/2018
ms.author: sbowles
ms.openlocfilehash: 3f75db176055d9f784ec978497d7cae077ff629f
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35349544"
---
# <a name="how-to-identify-faces-in-images"></a>Jak zidentyfikować powierzchni obrazów

W tym przewodniku pokazano, jak zidentyfikować nieznany kroje przy użyciu PersonGroups, które są tworzone z wyprzedzeniem od znanych osób. Przykłady są zapisywane w języku C# za pomocą biblioteki klienta powierzchni interfejsu API.

## <a name="concepts"></a> Pojęcia

Jeśli nie masz doświadczenia w obsłudze następujące pojęcia w tym przewodniku, należy wyszukać definicje w naszym [słownik](../Glossary.md) w dowolnym momencie:

- Stają przed — wykrywania
- Stają przed — ustalenie
- PersonGroup

## <a name="preparation"></a> Przygotowanie

W tym przykładzie przedstawiony poniżej:

- Jak utworzyć PersonGroup - PersonGroup ten zawiera listę znanych osób.
- Jak przypisać kroje każda osoba - kroje te są używane jako linii bazowej do identyfikowania osób. Zalecane jest użycie kroje wyczyść frontonu, podobnie jak identyfikatora zdjęcia Dobry zestaw zdjęć powinna zawierać krojów z tej samej osoby w różnych stanowi, kolory odzieży lub włosów style.

Przeprowadzenie pokazu tej próbki, należy przygotować licznych obrazów:

- Kilka zdjęć z powierzchni danej osoby. [Kliknij tutaj, aby pobrać przykładowy zdjęć](https://github.com/Microsoft/Cognitive-Face-Windows/tree/master/Data) Anna, faktury i Clare.
- Szereg zdjęć testów, które mogą lub nie mogą zawierać kroje Anna, rachunku lub Clare wykorzystywane do testowania identyfikacji. Można również wybrać niektóre przykładowe obrazy z poprzedniego łącza.

## <a name="step1"></a> Krok 1: Autoryzowanie wywołania interfejsu API

Każdego wywołania funkcji API krój wymaga klucza subskrypcji. Ten klucz może być przekazywane parametr ciągu zapytania albo określonym w nagłówku żądania. Aby przekazać klucz subskrypcji przy użyciu ciągu zapytania, zapoznaj się adres URL żądania [stają przed — wykrywanie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) na przykład:
```
https://westus.api.cognitive.microsoft.com/face/v1.0/detect[?returnFaceId][&returnFaceLandmarks][&returnFaceAttributes]
&subscription-key=<Subscription key>
```

Alternatywnie, klucz subskrypcji można również określić w nagłówku żądania HTTP: **ocp-apim subskrypcji — klucz: &lt;klucza subskrypcji&gt;**  podczas korzystania z biblioteki klienta, klucz Subskrypcja jest przekazywany w za pomocą konstruktora klasy FaceServiceClient. Na przykład:
 
```CSharp 
faceServiceClient = new FaceServiceClient("<Subscription Key>");
```
 
Klucz subskrypcji można uzyskać ze strony użytkownika portalu Azure Marketplace. Zobacz [subskrypcje](https://azure.microsoft.com/try/cognitive-services/).

## <a name="step2"></a> Krok 2: Tworzenie PersonGroup

W tym kroku utworzono PersonGroup o nazwie "MyFriends", który zawiera trzy osoby: Anna, faktury i Clare. Każda osoba ma kilka kroje zarejestrowany. Powierzchni muszą zostać wykryte z obrazami. Po wszystkich tych kroków, masz PersonGroup, podobnie jak na poniższej ilustracji:

![HowToIdentify1](../Images/group.image.1.jpg)

### <a name="step2-1"></a> 2.1 zdefiniuj osób dla PersonGroup
Osoba jest podstawową jednostką identyfikowanie. Osoba może mieć co najmniej jeden kroje znane zarejestrowany. Jednak PersonGroup jest kolekcją osób, a każda osoba jest zdefiniowany w szczególności PersonGroup. Identyfikacja odbywa się przed PersonGroup. Tak Utwórz PersonGroup, a następnie utwórz osoby, takich jak Anna, faktury i Clare ma zadania.

Najpierw należy utworzyć nowy PersonGroup. Jest to wykonywane przy użyciu [PersonGroup — tworzenie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244) interfejsu API. Odpowiednie biblioteki klienta interfejsu API jest metoda CreatePersonGroupAsync dla klasy FaceServiceClient. Określony identyfikator grupy można utworzyć grupy jest unikatowa dla każdej subskrypcji — mogą również uzyskać, aktualizować lub usuwać PersonGroups przy użyciu innych interfejsów API PersonGroup. Grupy jest zdefiniowane, osoby mogą być następnie definiowane w nim za pomocą [osoba PersonGroup - utworzyć](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c) interfejsu API. Metoda biblioteki klienta jest CreatePersonAsync. Po są tworzone, możesz dodać krój do poszczególnych osób.

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
### <a name="step2-2"></a> 2.2 wykrywania kroje i zarejestruj je do osoby poprawne
Wykrywanie odbywa się przez wysłanie do żądania sieci web "POST" [stają przed — wykrywanie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) interfejsu API z pliku obrazu w treści żądania HTTP. Podczas korzystania z biblioteki klienckiej, wykrywania twarzy na obrazie jest wykonywana za pomocą metody DetectAsync dla klasy FaceServiceClient.

Każda powierzchnia wykrytych można wywołać [PersonGroup osoby — Dodaj krój](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) Aby dodać je do osoby poprawne.

Poniższy kod ilustruje proces jak wykryć krój z obrazu i dodaj go do osoby:
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
Zwróć uwagę, że jeśli obraz zawiera więcej niż jeden krój, największy kroju został dodany. Można dodać inne kroje osobie, przekazując ciąg w formacie "targetFace = po lewej, top, szerokość, wysokość" do [PersonGroup osoba — Dodaj krój](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) interfejsu API targetFace parametru zapytania lub przy użyciu targetFace opcjonalny parametr dla Metody AddPersonFaceAsync w celu dodania innych powierzchni. Każdą powierzchnię dodane do osoby będą mieć identyfikator unikatowy utrwalonego krój, którego można użyć w [PersonGroup osoby — Usuń krój](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523e) i [stają przed — zidentyfikować](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239).
## <a name="step3"></a> Krok 3: Uczenia PersonGroup

Musi być uczony PersonGroup, zanim możliwe będzie wykonanie identyfikacji za jego pomocą. Ponadto musi on być retrained po dodaniu lub usunięciu każda osoba, lub jeśli osoba ma ich zarejestrowany krój edytować. Szkolenie jest realizowane przez [PersonGroup — Train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249) interfejsu API. Podczas korzystania z biblioteki klienckiej, to po prostu wywołanie do metody TrainPersonGroupAsync:
 
```CSharp 
await faceServiceClient.TrainPersonGroupAsync(personGroupId);
```
 
Szkolenie jest proces asynchroniczny. Może zostać zakończona, nie nawet po metoda TrainPersonGroupAsync zwróciła. Należy zbadać stan szkolenia przez [PersonGroup - pobierania stanu szkolenia](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395247) metody interfejsu API lub GetPersonGroupTrainingStatusAsync biblioteki klienta. Poniższy kod przedstawia proste logiki oczekiwanie PersonGroup szkolenia na zakończenie:
 
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

## <a name="step4"></a> Krok 4: Zidentyfikować krój przed zdefiniowanych PersonGroup
Podczas identyfikacji, powierzchni interfejsu API można obliczyć podobieństwa twarzy testów wśród wszystkich kroje w obrębie grupy i zwraca najbardziej porównywalne osób na tej stronie testowych. Jest to zrobić za pomocą [stają przed — ustalenie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) interfejsu API lub metody IdentifyAsync biblioteki klienta.

Kroju testowych musi zostać wykryte przy użyciu poprzednich kroków, a następnie identyfikator krój jest przekazywana do Identyfikuj interfejsu API jako drugi argument. Krój wiele identyfikatorów, które mogą zostać zidentyfikowane na raz, a wynik będzie zawierać wszystkich wyników identyfikowanie. Domyślnie określić zwraca tylko jedna osoba, najlepiej odpowiadający kroju testu. Jeśli wolisz, możesz określić maxNumOfCandidatesReturned opcjonalny parametr, aby umożliwić Identyfikuj zwracać więcej kandydatów.

Poniższy kod ilustruje proces określ:
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

Po zakończeniu czynności możesz zidentyfikować różnych powierzchni i zobacz, czy kroje Anna, rachunku lub Clare można poprawnie zidentyfikować, zgodnie z obrazy przekazany do wykrywania twarzy na obrazie. Poniżej przedstawiono przykłady:

![HowToIdentify2](../Images/identificationResult.1.jpg )

## <a name="step5"></a> Krok 5: Żądanie na dużą skalę

Ponieważ jest znana PersonGroup może zawierać maksymalnie 10 000 osób z powodu ograniczenia projektu w poprzedniej.
Aby uzyskać więcej informacji na temat do milionów skali scenariuszy, zobacz [używania funkcji na dużą skalę](how-to-use-large-scale.md).

## <a name="summary"></a> Podsumowanie

W tym przewodniku uzyskanych proces tworzenia PersonGroup i identyfikacji osoby. Bieżące informacje o funkcji wcześniej wyjaśniono i sprawdzono są następujące:

- Wykrywanie skierowany przy użyciu [stają przed — wykrywanie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d) interfejsu API
- Tworzenie za pomocą PersonGroups [PersonGroup — tworzenie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244) interfejsu API
- Tworzenie osoby za pomocą [osoba PersonGroup - utworzyć](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c) interfejsu API
- Szkolenie PersonGroup przy użyciu [PersonGroup — Train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249) interfejsu API
- Identyfikowanie nieznanej kroje przed potencjalnym użyciem PersonGroup [stają przed — ustalenie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) interfejsu API

## <a name="related"></a> Tematy pokrewne

- [Jak wykryć skierowany w obrazie](HowtoDetectFacesinImage.md)
- [Jak dodać powierzchni](how-to-add-faces.md)
- [Jak używać funkcji na dużą skalę](how-to-use-large-scale.md)
