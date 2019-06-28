---
title: 'Przykład: Funkcja na dużą skalę — interfejs API rozpoznawania twarzy'
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
ms.openlocfilehash: dcbec817f771324219a68de96eb5dd262a887fc1
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2019
ms.locfileid: "67449047"
---
# <a name="example-use-the-large-scale-feature"></a>Przykład: Korzystanie z funkcji na dużą skalę

Ten przewodnik jest zaawansowane artykułu na temat sposobu skalowanie w górę z istniejących obiektów w grupie i FaceList obiektów LargePersonGroup i LargeFaceList, odpowiednio. Ten przewodnik przedstawia proces migracji. Przyjęto założenie, podstawowe znajomość obiekty grupie i FaceList [Train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599ae2d16ac60f11b48b5aa4) operacji i funkcji rozpoznawania twarzy. Aby dowiedzieć się więcej na temat tych tematów, zobacz [rozpoznawania twarzy](../concepts/face-recognition.md) przewodnik koncepcyjny.

LargePersonGroup i LargeFaceList są nazywane zbiorczo operacji na dużą skalę. LargePersonGroup może zawierać maksymalnie 1 milion osób, każdy o maksymalnym 248 twarzy. LargeFaceList może zawierać maksymalnie 1 milion twarzy. Operacje na dużą skalę są podobne do poziomu konwencjonalnego grupie i FaceList, ale ma pewne różnice ze względu na nowej architekturze. 

Przykłady są napisane w C# za pomocą biblioteki klienta usługi Azure Cognitive Services Face API.

> [!NOTE]
> Aby włączyć twarzy wydajność wyszukiwania dla identyfikacji i FindSimilar w dużej skali, należy wprowadzić operację szkolenie w celu wstępnego przetworzenia LargeFaceList i LargePersonGroup. Podczas szkolenia różni się od sekund do około pół godziny na podstawie rzeczywistej wydajności. W okresie szkolenia jest możliwe przeprowadzenie identyfikacji i FindSimilar, jeśli działające pomyślnie szkolenia zostało zrobione przed. Wadą jest to, czy nowe dodano osoby i twarzy nie pojawiają się w wyniku aż do zakończenia nowego po migracji do szkoleń o dużej skali.

## <a name="step-1-initialize-the-client-object"></a>Krok 1: Inicjowanie obiektu klienta

Korzystając z biblioteki klienta interfejsu API rozpoznawania twarzy, klucz subskrypcji i punkt końcowy subskrypcji są przekazywane w konstruktorze klasy FaceClient. Na przykład:

```CSharp
string SubscriptionKey = "<Subscription Key>";
// Use your own subscription endpoint corresponding to the subscription key.
string SubscriptionEndpoint = "https://westus.api.cognitive.microsoft.com";
private readonly IFaceClient faceClient = new FaceClient(
            new ApiKeyServiceClientCredentials(subscriptionKey),
            new System.Net.Http.DelegatingHandler[] { });
faceClient.Endpoint = SubscriptionEndpoint
```

Aby uzyskać klucz subskrypcji z jego odpowiedni punkt końcowy, przejdź do portalu Azure Marketplace w witrynie Azure portal.
Aby uzyskać więcej informacji, zobacz [subskrypcje](https://azure.microsoft.com/services/cognitive-services/directory/vision/).

## <a name="step-2-code-migration"></a>Krok 2: Migracja kodu

W tej sekcji skupiono się na temat migrowania wdrożenia w grupie lub FaceList LargePersonGroup lub LargeFaceList. Mimo że LargePersonGroup lub LargeFaceList różni się od grupie lub FaceList projektowania i implementacji wewnętrznych, interfejsy API są podobne dla zgodności z poprzednimi wersjami.

Migracja danych nie jest obsługiwane. Możesz ponownie utworzyć LargePersonGroup lub LargeFaceList zamiast tego.

### <a name="migrate-a-persongroup-to-a-largepersongroup"></a>Migrowanie grupie do LargePersonGroup

Migracja z grupie LargePersonGroup jest proste. Współużytkują one dokładnie tych samych operacji poziomu grupy.

Do wykonania powiązane grupie lub osoby zachodzi konieczność zmiany tylko ścieżek interfejsu API lub moduł klasy zestawu SDK LargePersonGroup i LargePersonGroup osobie.

Dodaj wszystkie twarzy oraz osoby z grupie do nowych LargePersonGroup. Aby uzyskać więcej informacji, zobacz [Dodaj twarzy](how-to-add-faces.md).

### <a name="migrate-a-facelist-to-a-largefacelist"></a>Migrowanie FaceList do LargeFaceList

| Interfejsy API listy FaceList | Interfejsy API listy LargeFaceList |
|:---:|:---:|
| Przycisk Utwórz | Przycisk Utwórz |
| Usuwanie | Usuwanie |
| Get | Get |
| List | List |
| Aktualizacja | Aktualizacja |
| - | Szkolenie |
| - | Pobieranie stanu szkolenia |

W powyższej tabeli znajduje się porównanie z operacji na poziomie listy dla kolekcji FaceList i LargeFaceList. Zostanie wyświetlone, LargeFaceList jest powiązana z nowej operacji, szkolenie i pobieranie stanu szkolenia w porównaniu z FaceList. Szkolenie LargeFaceList jest warunkiem wstępnym [FindSimilar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) operacji. Szkolenie nie jest wymagana dla FaceList. Poniższy fragment kodu jest funkcją pomocnika czekać na szkolenie LargeFaceList:

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

Typowym zastosowaniem FaceList przy użyciu dodanych powierzchni i FindSimilar oglądałem się wcześniej, jak pokazano poniżej:

```CSharp
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

Migrowanie go do LargeFaceList, staje się następujące czynności:

```CSharp
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

Jak pokazano wcześniej zarządzanie danymi i część FindSimilar są prawie takie same. Jedynym wyjątkiem jest, że świeże wstępnego przetwarzania operacji Train należy ukończyć w LargeFaceList FindSimilar działa.

## <a name="step-3-train-suggestions"></a>Krok 3: Szkolenie sugestie

Mimo że procesu pociągu przyspiesza [FindSimilar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) i [identyfikacji](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239), niska czasu szkoleń, szczególnie w przypadku, gdy na dużą skalę. Czas szacowany szkolenia w różnych skalach znajduje się w poniższej tabeli.

| Skala dla powierzchni lub osoby | Szkolenie szacowany czas |
|:---:|:---:|
| 1000 | 1-2 sek. |
| 10 000 | 5 – 10 s |
| 100,000 | 1-2 min |
| 1 000 000 | 10-30 min |

Aby lepiej korzystać z funkcji na dużą skalę, zalecamy następujące strategie.

### <a name="step-31-customize-time-interval"></a>Krok 3.1. Dostosowywanie przedział czasu

Jak pokazano w `TrainLargeFaceList()`, ma przedział czasu (w milisekundach), aby opóźnić proces sprawdzania stanu nieskończonej szkolenia. W przypadku listy LargeFaceList z większą liczbą twarzy użycie dłuższego interwału zmniejsza liczby wywołań i koszt. Dostosuj przedział czasu, zgodnie z oczekiwanym pojemność LargeFaceList.

Tej samej strategii dotyczy także LargePersonGroup. Na przykład, gdy LargePersonGroup jest szkolenie przy użyciu 1 milionie osób `timeIntervalInMilliseconds` może być 60 000, która jest 1-minutowych interwałach.

### <a name="step-32-small-scale-buffer"></a>Krok 3.2 Niewielkie buforu

Osoby lub powierzchnie LargePersonGroup lub LargeFaceList są można wyszukiwać tylko od czasu zakończenia uczenia. W przypadku dynamicznych nowe osoby lub powierzchnie są stale dodawane i musi być od razu można wyszukiwać jeszcze szkolenie może trwać dłużej niż wymagany. 

Aby rozwiązać ten problem, użyj bardzo niewielkie LargePersonGroup lub LargeFaceList rolę buforu tylko dla nowo dodanych wpisów. Ten bufor trwa krótszy czas na nauczenie się ze względu na mniejszy rozmiar. Możliwość natychmiastowego wyszukiwania w ramach tego buforu tymczasowego powinny działać. Użyj tego buforu w połączeniu z szkolenie dotyczące wzorca LargePersonGroup lub LargeFaceList, uruchamiając wzorca szkolenia sparser określony interwał. Przykładami są środku nocy i codziennie.

Przykładowy przepływ pracy:

1. Główny LargePersonGroup lub utworzyć LargeFaceList, czyli kolekcji głównej. Utwórz buforu LargePersonGroup lub LargeFaceList, czyli kolekcji buforu. Kolekcja bufor jest tylko dla nowo dodanego osób lub powierzchnie.
1. Dodaj nowe osoby lub powierzchnie do wzorca kolekcji i kolekcji buforu.
1. Tylko szkolenie kolekcji buforu z interwałem krótki czas, aby upewnić się, że nowo dodanych wpisów zaczęły obowiązywać.
1. Identyfikacja wywołania lub FindSimilar względem wzorca kolekcji i kolekcji buforu. Scal wyniki.
1. Gdy rozmiar kolekcji buforu zwiększa się do progu, lub w czasie bezczynności systemu, należy utworzyć nową kolekcję buforu. Wyzwalanie operacji szkolenie dla wzorca kolekcji.
1. Usuń stare kolekcję buforu, po zakończeniu operacji szkolenie dla wzorca kolekcji.

### <a name="step-33-standalone-training"></a>Krok 3.3. Szkolenie autonomiczny

Jeśli stosunkowo długi czas oczekiwania, nie trzeba wyzwalacza operacji Train prawo po dodaniu nowych danych. W zamian operację szkolenia można oddzielić od logiki głównej i wyzwalać ją regularnie. Ta strategia jest odpowiednia dla scenariuszy dynamicznej z dopuszczalnym czasem oczekiwania. Można zastosować do scenariuszy statyczne, aby jeszcze bardziej ograniczyć częstotliwość Train.

Załóżmy, że istnieje `TrainLargePersonGroup` podobne do funkcji `TrainLargeFaceList`. Typowa implementacja metody szkolenia autonomicznego na LargePersonGroup wywołując [ `Timer` ](https://msdn.microsoft.com/library/system.timers.timer(v=vs.110).aspx) klasy w `System.Timers` jest:

```CSharp
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

Aby uzyskać więcej informacji dotyczących zarządzania danymi i powiązane identyfikacji implementacji, zobacz [Dodaj twarzy](how-to-add-faces.md) i [identyfikowanie twarzy na obrazie](HowtoIdentifyFacesinImage.md).

## <a name="summary"></a>Podsumowanie

W tym przewodniku przedstawiono sposób migracji istniejącego kodu w grupie lub FaceList, nie dane LargePersonGroup lub LargeFaceList:

- LargePersonGroup i LargeFaceList działają podobnie jak grupie lub FaceList, z tą różnicą, że procesu pociągu jest wymagana przez LargeFaceList.
- Właściwej strategii Train należy wykonać w celu aktualizacji danych dynamicznych dla dużych zestawów danych.

## <a name="next-steps"></a>Kolejne kroki

Postępuj zgodnie z przewodnik, aby dowiedzieć się, jak dodać twarze na grupie lub wykonaj operację identyfikowanie w grupie.

- [Dodaj twarzy](how-to-add-faces.md)
- [Identyfikowanie twarzy na obrazie](HowtoIdentifyFacesinImage.md)