---
title: 'Przykład: używanie funkcji na dużą skalę — interfejs API rozpoznawania twarzy'
titleSuffix: Azure Cognitive Services
description: Używanie funkcji na dużą skalę w interfejsie API rozpoznawania twarzy.
services: cognitive-services
author: SteveMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: sample
ms.date: 05/01/2019
ms.author: sbowles
ms.openlocfilehash: 35ab2d36a5d6c9977398fdbc16ba22eb1d9656a4
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2019
ms.locfileid: "65229834"
---
# <a name="example-how-to-use-the-large-scale-feature"></a>Przykład: jak używać funkcji na dużą skalę

Ten przewodnik jest zaawansowane artykułu na temat sposobu skalowanie w górę od istniejącego **grupie** i **FaceList** do **LargePersonGroup** i **LargeFaceList**odpowiednio. Ten przewodnik przedstawia proces migracji i założono podstawowe znajomość **grupie**, **FaceList**, [Train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599ae2d16ac60f11b48b5aa4) operacji i rozpoznawanie twarzy funkcje. Zobacz [rozpoznawania twarzy](../concepts/face-recognition.md) koncepcyjny przewodnika, aby dowiedzieć się więcej na temat tych.

LargePersonGroup i LargeFaceList są nazywane zbiorczo operacji na dużą skalę. LargePersonGroup może zawierać maksymalnie 1 000 000 osób, z których każdy o maksymalnym 248 twarze i LargeFaceList może zawierać maksymalnie 1 000 000 twarzy. Operacje na dużą skalę są podobne do poziomu konwencjonalnego grupie i FaceList, ale ma pewne istotne różnice ze względu na nowej architekturze. 

Przykłady są zapisywane w języku C# przy użyciu biblioteki klienta interfejsu API rozpoznawania twarzy.

> [!NOTE]
> Aby umożliwić wydajne wyszukiwanie twarzy na potrzeby użycia funkcji Identification i FindSimilar w dużej skali, należy wprowadzić operację szkolenia w celu wstępnego przetwarzania obiektów LargeFaceList i LargePersonGroup. Czas szkolenia różni się od kilku sekund do około pół godziny w zależności od rzeczywistej pojemności. W okresie szkolenia można nadal przeprowadzać operacje Identification i FindSimilar, jeśli wcześniej odbyło się szkolenie zakończone powodzeniem. Jednak wadą jest to, że nowo dodane osoby/twarze nie będą pojawiać się w wyniku aż do zakończenia nowego szkolenia na dużą skalę po migracji.

## <a name="step-1-initialize-the-client-object"></a>Krok 1: Inicjowanie obiektu klienta

Jeśli korzystasz z biblioteki klienta interfejsu API rozpoznawania twarzy, klucz subskrypcji i punkt końcowy subskrypcji są przekazywane w konstruktorze klasy FaceServiceClient. Na przykład:

```CSharp
string SubscriptionKey = "<Subscription Key>";
// Use your own subscription endpoint corresponding to the subscription key.
string SubscriptionRegion = "https://westcentralus.api.cognitive.microsoft.com/face/v1.0/";
FaceServiceClient FaceServiceClient = new FaceServiceClient(SubscriptionKey, SubscriptionRegion);
```

Klucz subskrypcji z odpowiadającym mu punktem końcowym można uzyskać na stronie portalu Marketplace witryny Azure Portal.
Zobacz [Subskrypcje](https://azure.microsoft.com/services/cognitive-services/directory/vision/).

## <a name="step-2-code-migration"></a>Krok 2: Migracja kodu

W tej sekcji skupiono się tylko na migrowaniu implementacji kolekcji PersonGroup/FaceList do kolekcji LargePersonGroup/LargeFaceList. Mimo że LargePersonGroup/LargeFaceList różni się od grupie/FaceList projektowania i implementacji wewnętrznych, interfejsy API są podobne dla zgodności z poprzednimi wersjami.

Migracja danych nie jest obsługiwana — musisz w zamian odtworzyć kolekcję LargePersonGroup/LargeFaceList.

### <a name="migrate-persongroup-to-largepersongroup"></a>Migrowanie kolekcji PersonGroup do kolekcji LargePersonGroup

Migracji grupie LargePersonGroup jest proste, dzielą się dokładnie tych samych operacji poziomu grupy.

W przypadku implementacji związanej z elementem PersonGroup/osoba trzeba tylko zmienić ścieżki interfejsu API lub moduł/klasę zestawu SDK na grupę LargePersonGroup i osobę w grupie LargePersonGroup.

Należy dodać wszystkie twarzy oraz osoby w grupie, do nowego LargePersonGroup. Zobacz [jak dodać twarzy](how-to-add-faces.md) dla odwołania.

### <a name="migrate-facelist-to-largefacelist"></a>Migrowanie listy FaceList do listy LargeFaceList

| Interfejsy API listy FaceList | Interfejsy API listy LargeFaceList |
|:---:|:---:|
| Przycisk Utwórz | Przycisk Utwórz |
| Usuń | Usuń |
| Pobierz | Pobierz |
| List | List |
| Aktualizacja | Aktualizacja |
| - | Szkol |
| - | Pobieranie stanu szkolenia |

W powyższej tabeli znajduje się porównanie z operacji na poziomie listy dla kolekcji FaceList i LargeFaceList. Jak pokazano, do kolekcji LargeFaceList dołączono nowe operacje, szkolenie i pobieranie stanu szkolenia, których nie oferuje kolekcja FaceList. Przeszkolenie listy LargeFaceList jest warunkiem wstępnym operacji [FindSimilar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237), a w przypadku listy FaceList szkolenie nie jest wymagane. Poniższy fragment kodu to funkcja pomocnika na potrzeby oczekiwania na szkolenie listy LargeFaceList.

```CSharp
/// <summary>
/// Helper function to train LargeFaceList and wait for finish.
/// </summary>
/// <remarks>
/// The time interval can be adjusted considering the following factors:
/// - The training time which depends on the capacity of the LargeFaceList.
/// - The acceptable latency for getting the training status.
/// - The call frequency and cost.
///
/// Estimated training time for LargeFaceList in different scale:
/// -     1,000 faces cost about  1 to  2 seconds.
/// -    10,000 faces cost about  5 to 10 seconds.
/// -   100,000 faces cost about  1 to  2 minutes.
/// - 1,000,000 faces cost about 10 to 30 minutes.
/// </remarks>
/// <param name="largeFaceListId">The Id of the LargeFaceList for training.</param>
/// <param name="timeIntervalInMilliseconds">The time interval for getting training status in milliseconds.</param>
/// <returns>A task of waiting for LargeFaceList training finish.</returns>
private static async Task TrainLargeFaceList(
    string largeFaceListId,
    int timeIntervalInMilliseconds = 1000)
{
    // Trigger a train call.
    await FaceServiceClient.TrainLargeFaceListAsync(largeFaceListId);

    // Wait for training finish.
    while (true)
    {
        Task.Delay(timeIntervalInMilliseconds).Wait();
        var status = await FaceServiceClient.GetLargeFaceListTrainingStatusAsync(largeFaceListId);

        if (status.Status == Status.Running)
        {
            continue;
        }
        else if (status.Status == Status.Succeeded)
        {
            break;
        }
        else
        {
            throw new Exception("The train operation is failed!");
        }
    }
}
```

Wcześniej typowy FaceList dodawania twarzy i FindSimilar powinien wyglądać następująco:

```CSharp
// Create a FaceList.
const string FaceListId = "myfacelistid_001";
const string FaceListName = "MyFaceListDisplayName";
const string ImageDir = @"/path/to/FaceList/images";
FaceServiceClient.CreateFaceListAsync(FaceListId, FaceListName).Wait();

// Add Faces to the FaceList.
Parallel.ForEach(
    Directory.GetFiles(ImageDir, "*.jpg"),
    async imagePath =>
        {
            using (Stream stream = File.OpenRead(imagePath))
            {
                await FaceServiceClient.AddFaceToFaceListAsync(FaceListId, stream);
            }
        });

// Perform FindSimilar.
const string QueryImagePath = @"/path/to/query/image";
var results = new List<SimilarPersistedFace[]>();
using (Stream stream = File.OpenRead(QueryImagePath))
{
    var faces = FaceServiceClient.DetectAsync(stream).Result;
    foreach (var face in faces)
    {
        results.Add(await FaceServiceClient.FindSimilarAsync(face.FaceId, FaceListId, 20));
    }
}
```

Podczas migrowania go do LargeFaceList, powinny stać się następujące czynności:

```CSharp
// Create a LargeFaceList.
const string LargeFaceListId = "mylargefacelistid_001";
const string LargeFaceListName = "MyLargeFaceListDisplayName";
const string ImageDir = @"/path/to/FaceList/images";
FaceServiceClient.CreateLargeFaceListAsync(LargeFaceListId, LargeFaceListName).Wait();

// Add Faces to the LargeFaceList.
Parallel.ForEach(
    Directory.GetFiles(ImageDir, "*.jpg"),
    async imagePath =>
        {
            using (Stream stream = File.OpenRead(imagePath))
            {
                await FaceServiceClient.AddFaceToLargeFaceListAsync(LargeFaceListId, stream);
            }
        });

// Train() is newly added operation for LargeFaceList.
// Must call it before FindSimilarAsync() to ensure the newly added faces searchable.
await TrainLargeFaceList(LargeFaceListId);

// Perform FindSimilar.
const string QueryImagePath = @"/path/to/query/image";
var results = new List<SimilarPersistedFace[]>();
using (Stream stream = File.OpenRead(QueryImagePath))
{
    var faces = FaceServiceClient.DetectAsync(stream).Result;
    foreach (var face in faces)
    {
        results.Add(await FaceServiceClient.FindSimilarAsync(face.FaceId, largeFaceListId: LargeFaceListId));
    }
}
```

Jak pokazano powyżej, zarządzanie danymi i część FindSimilar są prawie takie same. Jedynym wyjątkiem jest to, że nową operację przetwarzania wstępnego Szkolenie należy ukończyć w kolekcji LargeFaceList, aby funkcja FindSimilar mogła działać.

## <a name="step-3-train-suggestions"></a>Krok 3: Szkolenie sugestie

Mimo że operacja szkolenia przyspiesza działanie funkcji [FindSimilar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) i [Identification](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239), czas szkolenia wydłuża się, szczególnie w przypadku pracy na dużą skalę. Szacowany czas szkolenia w różnych skalach został przedstawiony w poniższej tabeli:

| Skala (twarze lub osoby) | Szacowany czas szkolenia |
|:---:|:---:|
| 1000 | 1–2 s |
| 10 000 | 5–10 s |
| 100,000 | 1–2 min |
| 1 000 000 | 10–30 min |

Aby lepiej wykorzystywać funkcje na dużą skalę, warto wziąć pod uwagę niektóre strategie.

## <a name="step-31-customize-time-interval"></a>Krok 3.1. Dostosowywanie przedział czasu

Jak pokazano, obiekt `TrainLargeFaceList()` zawiera element `timeIntervalInMilliseconds` umożliwiający nieskończone opóźnianie procesu sprawdzania stanu szkolenia. W przypadku listy LargeFaceList z większą liczbą twarzy użycie dłuższego interwału zmniejsza liczby wywołań i koszt. Interwał czasu należy dostosować zgodnie z oczekiwaną pojemnością obiektu LargeFaceList.

Tej samej strategii dotyczy także LargePersonGroup. Na przykład podczas szkolenia LargePersonGroup 1 000 000 osób `timeIntervalInMilliseconds` może być 60 000 (1-minutowych interwałach).

## <a name="step-32-small-scale-buffer"></a>Krok 3.2. Bufor na małą skalę

Osoby/twarze można wyszukiwać w kolekcjach LargePersonGroup/LargeFaceList tylko po ukończeniu ich szkolenia. W scenariuszu dynamicznym nowe osoby/twarze są stale dodawane i muszą być natychmiast możliwe do wyszukania, ale szkolenie może potrwać dłużej niż jest to wymagane. Aby rozwiązać ten problem, można użyć dodatkowej kolekcji LargePersonGroup/LargeFaceList w małej skali jako buforu tylko dla nowo dodanych wpisów. Czas szkolenia tego buforu jest krótszy, ponieważ jego rozmiar jest znacznie mniejszy, i natychmiastowe wyszukiwanie w tym buforze tymczasowym powinno działać. Tego buforu należy używać w połączeniu ze szkoleniem dotyczącym głównej kolekcji LargePersonGroup/LargeFaceList, przeprowadzając szkolenie główne w dłuższym interwale, na przykład w nocy, i codziennie.

Przykładowy przepływ pracy:
1. Utwórz główny obiekt LargePersonGroup/LargeFaceList (kolekcja główna) i bufor LargePersonGroup/LargeFaceList (kolekcja buforu). Kolekcja buforu jest przeznaczona tylko dla nowo dodanych osób/twarzy.
1. Dodaj nowe osoby/twarze do kolekcji głównej i kolekcji buforu.
1. Przeprowadź tylko szkolenie kolekcji buforu w krótkim przedziale czasu, aby zagwarantować działanie nowo dodanych wpisów.
1. Wywołaj operacje Identification/FindSimilar względem kolekcji głównej i kolekcji buforu, a następnie scal wyniki.
1. Gdy rozmiar kolekcji buforu zwiększy się do wartości progowej lub w czasie bezczynności systemu, utwórz nową kolekcję buforu i wyzwól szkolenie kolekcji głównej.
1. Usuń starą kolekcję buforu po zakończeniu szkolenia kolekcji głównej.

## <a name="step-33-standalone-training"></a>Krok 3.3 szkolenia autonomiczny

Jeśli stosunkowo długie opóźnienie jest dopuszczalne, nie trzeba wyzwalać operacji szkolenia od razu po dodaniu nowych danych. W zamian operację szkolenia można oddzielić od logiki głównej i wyzwalać ją regularnie. Ta strategia jest odpowiednia w przypadku scenariuszy dynamicznych z dopuszczalnym opóźnieniem. Można ją stosować do scenariuszy statycznych, aby jeszcze bardziej zmniejszyć częstotliwość szkolenia.

Załóżmy, że istnieje funkcja `TrainLargePersonGroup` podobna do obiektu `TrainLargeFaceList`. Typowa implementacja metody autonomicznego szkoleń dotyczących LargePersonGroup wywołując [ `Timer` ](https://msdn.microsoft.com/library/system.timers.timer(v=vs.110).aspx) klasy w `System.Timers` będzie:

```CSharp
private static void Main()
{
    // Create a LargePersonGroup.
    const string LargePersonGroupId = "mylargepersongroupid_001";
    const string LargePersonGroupName = "MyLargePersonGroupDisplayName";
    FaceServiceClient.CreateLargePersonGroupAsync(LargePersonGroupId, LargePersonGroupName).Wait();

    // Setup a standalone training at regular intervals.
    const int TimeIntervalForStatus = 1000 * 60; // 1 minute interval for getting training status.
    const double TimeIntervalForTrain = 1000 * 60 * 60; // 1 hour interval for training.
    var trainTimer = new Timer(TimeIntervalForTrain);
    trainTimer.Elapsed += (sender, args) => TrainTimerOnElapsed(LargePersonGroupId, TimeIntervalForStatus);
    trainTimer.AutoReset = true;
    trainTimer.Enabled = true;

    // Other operations like creating persons, adding faces and Identification except for Train.
    // ...
}

private static void TrainTimerOnElapsed(string largePersonGroupId, int timeIntervalInMilliseconds)
{
    TrainLargePersonGroup(largePersonGroupId, timeIntervalInMilliseconds).Wait();
}
```

Więcej informacji na temat zarządzania danymi i implementacji powiązanych z identyfikacją można znaleźć w tematach [Jak dodawać twarze](how-to-add-faces.md) i [How to Identify Faces in Image](HowtoIdentifyFacesinImage.md) (Jak identyfikować twarze na obrazie).

## <a name="summary"></a>Podsumowanie

W tym przewodniku pokazano, jak migrować istniejący kod kolekcji PersonGroup/FaceList (nie dane) do kolekcji LargePersonGroup/LargeFaceList:

- Kolekcje LargePersonGroup i LargeFaceList działają podobnie do kolekcji PersonGroup/FaceList, ale dla listy LargeFaceList jest wymagana operacja szkolenia.
- W przypadku zestawów danych na dużą skalę należy zastosować odpowiednią strategię szkolenia w celu aktualizacji danych dynamicznych.

## <a name="next-steps"></a>Kolejne kroki

Postępuj zgodnie z przewodnik, aby dowiedzieć się, jak dodać twarze na grupie lub wykonaj operację identyfikowanie w grupie.

- [Jak dodawać twarze](how-to-add-faces.md)
- [Identyfikowanie twarzy na obrazie](HowtoIdentifyFacesinImage.md)