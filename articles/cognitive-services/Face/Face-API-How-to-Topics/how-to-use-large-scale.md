---
title: 'Przykład: Użyj funkcji Na dużą skalę - Ściana'
titleSuffix: Azure Cognitive Services
description: Ten przewodnik jest artykułem na temat skalowania w górę z istniejących persongroup i FaceList obiektów do LargePersonGroup i LargeFaceList obiektów.
services: cognitive-services
author: SteveMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: sample
ms.date: 05/01/2019
ms.author: sbowles
ms.openlocfilehash: dc0964e40e9214e414d865c06006f1d36e97eeb2
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76169780"
---
# <a name="example-use-the-large-scale-feature"></a>Przykład: Użyj funkcji na dużą skalę

Ten przewodnik jest zaawansowanym artykułem na temat skalowania w górę z istniejących persongroup i FaceList obiektów do LargePersonGroup i LargeFaceList obiektów, odpowiednio. W tym przewodniku przedstawiono proces migracji. Zakłada podstawową znajomość persongroup i FaceList obiektów, [Train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599ae2d16ac60f11b48b5aa4) operacji i funkcji rozpoznawania twarzy. Aby dowiedzieć się więcej o tych tematach, zobacz przewodnik koncepcyjny [rozpoznawania twarzy.](../concepts/face-recognition.md)

LargePersonGroup i LargeFaceList są zbiorczo określane jako operacje na dużą skalę. LargePersonGroup może zawierać do 1 miliona osób, z których każda ma maksymalnie 248 twarzy. LargeFaceList może zawierać do 1 miliona twarzy. Operacje na dużą skalę są podobne do konwencjonalnych PersonGroup i FaceList, ale mają pewne różnice ze względu na nową architekturę. 

Przykłady są zapisywane w języku C# przy użyciu biblioteki klienta usługi Azure Cognitive Services Face.

> [!NOTE]
> Aby włączyć wydajność wyszukiwania twarzy dla identyfikacji i findsimilar w dużej skali, wprowadzenie train operacji wstępnie przetworzyć LargeFaceList i LargePersonGroup. Czas szkolenia waha się od sekund do około pół godziny w zależności od rzeczywistej pojemności. W okresie szkolenia możliwe jest wykonanie identyfikacji i findsimilar jeśli udane szkolenie operacyjne zostało wykonane wcześniej. Wadą jest to, że nowe dodane osoby i twarze nie pojawiają się w wyniku, dopóki nie zostanie zakończona nowa migracja po migracji do szkolenia na dużą skalę.

## <a name="step-1-initialize-the-client-object"></a>Krok 1: Inicjowanie obiektu klienta

Korzystając z biblioteki klienta Face klucz subskrypcji i punkt końcowy subskrypcji są przekazywane za pośrednictwem konstruktora FaceClient klasy. Przykład:

```csharp
string SubscriptionKey = "<Subscription Key>";
// Use your own subscription endpoint corresponding to the subscription key.
string SubscriptionEndpoint = "https://westus.api.cognitive.microsoft.com";
private readonly IFaceClient faceClient = new FaceClient(
            new ApiKeyServiceClientCredentials(subscriptionKey),
            new System.Net.Http.DelegatingHandler[] { });
faceClient.Endpoint = SubscriptionEndpoint
```

Aby uzyskać klucz subskrypcji z odpowiednim punktem końcowym, przejdź do portalu Azure Marketplace z witryny Azure portal.
Aby uzyskać więcej informacji, zobacz [Subskrypcje](https://azure.microsoft.com/services/cognitive-services/directory/vision/).

## <a name="step-2-code-migration"></a>Krok 2: Migracja kodu

W tej sekcji opisano sposób migracji implementacji persongroup lub FaceList do LargePersonGroup lub LargeFaceList. Chociaż LargePersonGroup lub LargeFaceList różni się od PersonGroup lub FaceList w projektowaniu i implementacji wewnętrznej, interfejsy interfejsu API są podobne dla zgodności z powrotem.

Migracja danych nie jest obsługiwana. Zamiast tego ponownie utwórz LargePersonGroup lub LargeFaceList.

### <a name="migrate-a-persongroup-to-a-largepersongroup"></a>Migrowanie grupy osób do grupy dużych osób

Migracja z grupy osób do grupy dużej osoby jest prosta. Mają one dokładnie te same operacje na poziomie grupy.

W przypadku implementacji persongroup- lub osoby związane z, jest konieczne, aby zmienić tylko ścieżki interfejsu API lub SDK klasy/modułu largepersongroup i largepersongroup osoby.

Dodaj wszystkie twarze i osoby z grupy osób do nowej Grupy Dużej Liczby Osób. Aby uzyskać więcej informacji, zobacz [Dodawanie ścian](how-to-add-faces.md).

### <a name="migrate-a-facelist-to-a-largefacelist"></a>Migrowanie listy facelist do listy largeface

| Interfejsy API listy FaceList | Interfejsy API listy LargeFaceList |
|:---:|:---:|
| Utwórz | Utwórz |
| Usuń | Usuń |
| Get | Get |
| List | List |
| Aktualizacja | Aktualizacja |
| - | Szkolenie |
| - | Pobieranie stanu szkolenia |

W powyższej tabeli znajduje się porównanie z operacji na poziomie listy dla kolekcji FaceList i LargeFaceList. Jak pokazano, LargeFaceList jest wyposażony w nowe operacje, Train i Get Training Status, w porównaniu z FaceList. Szkolenie LargeFaceList jest warunkiem wstępnym [FindSimilar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) operacji. Szkolenie nie jest wymagane dla FaceList. Poniższy urywek jest funkcją pomocnika, aby czekać na szkolenie LargeFaceList:

```csharp
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
    await FaceClient.LargeTrainLargeFaceListAsync(largeFaceListId);

    // Wait for training finish.
    while (true)
    {
        Task.Delay(timeIntervalInMilliseconds).Wait();
        var status = await faceClient.LargeFaceList.TrainAsync(largeFaceListId);

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

Wcześniej typowe użycie FaceList z dodanymi twarzami i FindSimilar wyglądało następująco:

```csharp
// Create a FaceList.
const string FaceListId = "myfacelistid_001";
const string FaceListName = "MyFaceListDisplayName";
const string ImageDir = @"/path/to/FaceList/images";
faceClient.FaceList.CreateAsync(FaceListId, FaceListName).Wait();

// Add Faces to the FaceList.
Parallel.ForEach(
    Directory.GetFiles(ImageDir, "*.jpg"),
    async imagePath =>
        {
            using (Stream stream = File.OpenRead(imagePath))
            {
                await faceClient.FaceList.AddFaceFromStreamAsync(FaceListId, stream);
            }
        });

// Perform FindSimilar.
const string QueryImagePath = @"/path/to/query/image";
var results = new List<SimilarPersistedFace[]>();
using (Stream stream = File.OpenRead(QueryImagePath))
{
    var faces = faceClient.Face.DetectWithStreamAsync(stream).Result;
    foreach (var face in faces)
    {
        results.Add(await faceClient.Face.FindSimilarAsync(face.FaceId, FaceListId, 20));
    }
}
```

Podczas migracji do LargeFaceList staje się następujące:

```csharp
// Create a LargeFaceList.
const string LargeFaceListId = "mylargefacelistid_001";
const string LargeFaceListName = "MyLargeFaceListDisplayName";
const string ImageDir = @"/path/to/FaceList/images";
faceClient.LargeFaceList.CreateAsync(LargeFaceListId, LargeFaceListName).Wait();

// Add Faces to the LargeFaceList.
Parallel.ForEach(
    Directory.GetFiles(ImageDir, "*.jpg"),
    async imagePath =>
        {
            using (Stream stream = File.OpenRead(imagePath))
            {
                await faceClient.LargeFaceList.AddFaceFromStreamAsync(LargeFaceListId, stream);
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
    var faces = faceClient.Face.DetectWithStreamAsync(stream).Result;
    foreach (var face in faces)
    {
        results.Add(await faceClient.Face.FindSimilarAsync(face.FaceId, largeFaceListId: LargeFaceListId));
    }
}
```

Jak wcześniej pokazano, zarządzanie danymi i część FindSimilar są prawie takie same. Jedynym wyjątkiem jest to, że nowe preprocesing train operacji musi zakończyć się w LargeFaceList przed FindSimilar działa.

## <a name="step-3-train-suggestions"></a>Krok 3: Sugestie pociągów

Mimo, że operacja pociągu przyspiesza [FindSimilar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) i [identyfikacji,](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239)czas szkolenia cierpi, zwłaszcza gdy zbliża się na dużą skalę. Szacowany czas szkolenia w różnych skalach jest wymieniony w poniższej tabeli.

| Skala dla twarzy lub osób | Szacowany czas szkolenia |
|:---:|:---:|
| 1000 | 1-2 sek. |
| 10 000 | 5-10 sek. |
| 100 000 | 1-2 min. |
| 1 000 000 | 10-30 min. |

Aby lepiej wykorzystać funkcję na dużą skalę, zalecamy następujące strategie.

### <a name="step-31-customize-time-interval"></a>Krok 3.1: Dostosowywanie przedziału czasu

Jak pokazano `TrainLargeFaceList()`w , istnieje przedział czasu w milisekundach, aby opóźnić proces sprawdzania stanu nieskończonego treningu. W przypadku listy LargeFaceList z większą liczbą twarzy użycie dłuższego interwału zmniejsza liczby wywołań i koszt. Dostosuj przedział czasu zgodnie z oczekiwaną pojemnością LargeFaceList.

Ta sama strategia dotyczy również LargePersonGroup. Na przykład podczas szkolenia LargePersonGroup z 1 `timeIntervalInMilliseconds` mln osób, może być 60.000, co jest interwał 1-minutowy.

### <a name="step-32-small-scale-buffer"></a>Krok 3.2: Bufor na małą skalę

Osoby lub twarze w LargePersonGroup lub LargeFaceList można przeszukiwać tylko po przeszkoleniu. W dynamicznym scenariuszu nowe osoby lub twarze są stale dodawane i muszą być natychmiast przeszukiwalne, ale szkolenie może trwać dłużej niż oczekiwano. 

Aby rozwiązać ten problem, należy użyć bardzo małej skali LargePersonGroup lub LargeFaceList jako bufor tylko dla nowo dodanych wpisów. Ten bufor zajmuje krótszy czas, aby trenować ze względu na mniejszy rozmiar. Natychmiastowe wyszukiwanie w tym buforze tymczasowym powinno działać. Użyj tego buforu w połączeniu z szkolenia na master LargePersonGroup lub LargeFaceList, uruchamiając szkolenie główne w przedziale sparser. Przykłady są w środku nocy i codziennie.

Przykładowy przepływ pracy:

1. Utwórz wzorzec LargePersonGroup lub LargeFaceList, który jest kolekcją wzorcową. Utwórz bufor LargePersonGroup lub LargeFaceList, który jest kolekcją buforu. Kolekcja buforu jest tylko dla nowo dodanych osób lub twarzy.
1. Dodaj nowe osoby lub ściany do kolekcji głównej i kolekcji buforu.
1. Tylko trenować kolekcji buforu z krótkim przedziale czasu, aby upewnić się, że nowo dodane wpisy zaczynają obowiązywać.
1. Wywołanie identyfikacji lub FindSimilar względem kolekcji głównej i kolekcji buforu. Scalanie wyników.
1. Gdy rozmiar kolekcji buforu zwiększa się do progu lub w czasie bezczynnością systemu, utwórz nową kolekcję buforu. Wyzwalanie Train operacji w kolekcji wzorca.
1. Usuń kolekcję starego buforu po operacji Train kończy się w kolekcji master.

### <a name="step-33-standalone-training"></a>Krok 3.3: Samodzielne szkolenie

Jeśli stosunkowo długie opóźnienie jest dopuszczalne, nie jest konieczne wyzwolenie Train operacji zaraz po dodaniu nowych danych. W zamian operację szkolenia można oddzielić od logiki głównej i wyzwalać ją regularnie. Ta strategia jest odpowiednia dla scenariuszy dynamicznych z dopuszczalnym opóźnieniem. Może być stosowany do scenariuszy statycznych w celu dalszego zmniejszenia częstotliwości Train.

Załóżmy, `TrainLargePersonGroup` że istnieje `TrainLargeFaceList`funkcja podobna do . Typowa implementacja autonomicznego szkolenia na LargePersonGroup przez [`Timer`](https://msdn.microsoft.com/library/system.timers.timer(v=vs.110).aspx) wywołanie klasy w `System.Timers` jest:

```csharp
private static void Main()
{
    // Create a LargePersonGroup.
    const string LargePersonGroupId = "mylargepersongroupid_001";
    const string LargePersonGroupName = "MyLargePersonGroupDisplayName";
    faceClient.LargePersonGroup.CreateAsync(LargePersonGroupId, LargePersonGroupName).Wait();

    // Set up standalone training at regular intervals.
    const int TimeIntervalForStatus = 1000 * 60; // 1-minute interval for getting training status.
    const double TimeIntervalForTrain = 1000 * 60 * 60; // 1-hour interval for training.
    var trainTimer = new Timer(TimeIntervalForTrain);
    trainTimer.Elapsed += (sender, args) => TrainTimerOnElapsed(LargePersonGroupId, TimeIntervalForStatus);
    trainTimer.AutoReset = true;
    trainTimer.Enabled = true;

    // Other operations like creating persons, adding faces, and identification, except for Train.
    // ...
}

private static void TrainTimerOnElapsed(string largePersonGroupId, int timeIntervalInMilliseconds)
{
    TrainLargePersonGroup(largePersonGroupId, timeIntervalInMilliseconds).Wait();
}
```

Aby uzyskać więcej informacji na temat zarządzania danymi i implementacji związanych z identyfikacją, zobacz [Dodawanie ścian](how-to-add-faces.md) i [identyfikowanie twarzy na obrazie](HowtoIdentifyFacesinImage.md).

## <a name="summary"></a>Podsumowanie

W tym przewodniku opisano, jak przeprowadzić migrację istniejącego kodu PersonGroup lub FaceList, a nie danych, do LargePersonGroup lub LargeFaceList:

- LargePersonGroup i LargeFaceList działają podobnie do PersonGroup lub FaceList, z tą różnicą, że operacja Train jest wymagana przez LargeFaceList.
- Weź właściwą strategię train do dynamicznej aktualizacji danych dla dużych zestawów danych.

## <a name="next-steps"></a>Następne kroki

Postępuj zgodnie z instrukcjami, aby dowiedzieć się, jak dodać twarze do grupy osób lub wykonać operację Identyfikowanie w grupie osób.

- [Dodawanie twarzy](how-to-add-faces.md)
- [Identyfikowanie twarzy na obrazie](HowtoIdentifyFacesinImage.md)
