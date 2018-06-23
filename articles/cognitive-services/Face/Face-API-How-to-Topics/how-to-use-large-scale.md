---
title: Funkcja na dużą skalę w powierzchni interfejsu API | Dokumentacja firmy Microsoft
titleSuffix: Microsoft Cognitive Services
description: Funkcja na dużą skalę w powierzchni interfejsu API z kognitywnych usług.
services: cognitive-services
author: SteveMSFT
manager: corncar
ms.service: cognitive-services
ms.component: face-api
ms.topic: article
ms.date: 03/01/2018
ms.author: sbowles
ms.openlocfilehash: f7b3ac57cf6b24c8a90b4ea59757d3a2cfafd781
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347192"
---
# <a name="how-to-use-the-large-scale-feature"></a>Jak używać funkcji na dużą skalę

Ten przewodnik jest zaawansowane artykuł na temat migracji kodu do skalowanie w górę od istniejących PersonGroup i FaceList LargePersonGroup i LargeFaceList odpowiednio.
Ten przewodnik przedstawia proces migracji z założenia wiedzy podstawowe sposoby użycia PersonGroup i FaceList.
Pobieranie znasz podstawowe operacje, można znaleźć innych samouczków, takich jak [sposób identyfikacji kroje w obrazach](HowtoIdentifyFacesinImage.md),

Interfejs API krój Microsoft wydał ostatnio dwie funkcje na potrzeby scenariuszy na dużą skalę, LargePersonGroup i LargeFaceList zbiorczo określany jako operacji na dużą skalę.
LargePersonGroup może zawierać maksymalnie 1 000 000 osób z maksymalnie 248 kroje, a LargeFaceList może zawierać maksymalnie do 1 000 000 powierzchni.

Operacje na dużą skalę są podobne z konwencjonalnej PersonGroup i FaceList, ale ma kilka istotnych różnic z powodu nowej architektury.
Ten przewodnik przedstawia proces migracji z założenia wiedzy podstawowe sposoby użycia PersonGroup i FaceList.
Przykłady są zapisywane w języku C# za pomocą biblioteki klienta powierzchni interfejsu API.

Umożliwia wykonywanie wyszukiwania krój identyfikacji i FindSimilar w dużej skali, należy wprowadzić operację Train wstępnie przetworzyć LargeFaceList i LargePersonGroup.
Czas szkolenie może się różnić od sekund do około pół godziny w zależności od rzeczywistej pojemności.
W okresie szkolenia jest nadal możliwe do wykonania identyfikacji i FindSimilar, jeśli pomyślnie szkolenia odbywa się przed.
Jednak wadą jest to, że nowe dodane osób/kroje nie będą widoczne w wynik ukończenie nowych migracji post do szkolenia na dużą skalę.

## <a name="concepts"></a> Pojęcia

Jeśli nie masz doświadczenia w obsłudze następujące pojęcia w tym przewodniku, definicje znajdują się w [słownik](../Glossary.md):

- LargePersonGroup: Kolekcja osób o pojemności do 1 000 000.
- LargeFaceList: Kolekcja kroje o pojemności do 1 000 000.
- Pociągu: Wstępne proces zapewnienia identyfikacji/FindSimilar wydajności.
- Identyfikacja: Zidentyfikować kroje co najmniej jeden z PersonGroup lub LargePersonGroup.
- FindSimilar: Wyszukiwanie podobne krojów z FaceList lub LargeFaceList.

## <a name="initialization"></a> Krok 1: Autoryzowanie wywołania interfejsu API

W przypadku za pomocą biblioteki klienta interfejsu API krój, klucz subskrypcji i punktu końcowego subskrypcji są przekazywane w konstruktora klasy FaceServiceClient. Na przykład:

```CSharp
string SubscriptionKey = "<Subscription Key>";
// Use your own subscription endpoint corresponding to the subscription key.
string SubscriptionRegion = "https://westcentralus.api.cognitive.microsoft.com/face/v1.0/";
FaceServiceClient FaceServiceClient = new FaceServiceClient(SubscriptionKey, SubscriptionRegion);
```

Klucz subskrypcji z odpowiedniego punktu końcowego można uzyskać ze strony użytkownika portalu Azure Marketplace.
Zobacz [subskrypcje](https://azure.microsoft.com/services/cognitive-services/directory/vision/).

## <a name="migrate"></a> Krok 2: Migracja kodu w akcji

W tej sekcji tylko koncentruje się na migrowanie wykonania PersonGroup/FaceList LargePersonGroup/LargeFaceList.
Mimo że LargePersonGroup/LargeFaceList różni się od PersonGroup/FaceList w projektowania i implementacji wewnętrznego, interfejsy API są podobne dla zgodności wstecz.

Migracja danych nie jest obsługiwana, należy zamiast tego utwórz ponownie LargePersonGroup/LargeFaceList.

## <a name="largepersongroup"></a> Krok 2.1: Migrowanie PersonGroup do LargePersonGroup

Migracja z PersonGroup do LargePersonGroup jest smooth, ponieważ mają dokładnie te same operacje poziom grupowania.

PersonGroup/osoby związane z implementacją, jest tylko zmienić ścieżek interfejsu API lub moduł klasy zestawu SDK LargePersonGroup i LargePersonGroup osobie.

Pod względem migracji danych, zobacz [jak dodać skierowany](how-to-add-faces.md) odwołania.

## <a name="largefacelist"></a> Krok 2.2: Migrowanie FaceList do LargeFaceList

| Interfejsy API FaceList | Interfejsy API LargeFaceList |
|:---:|:---:|
| Przycisk Utwórz | Przycisk Utwórz |
| Usuwanie | Usuwanie |
| Get | Get |
| List | List |
| Aktualizacja | Aktualizacja |
| - | Uczenie |
| - | Pobierz stan szkolenia |

Zgodnie z poprzednią tabelą znajduje się porównanie operacje poziom listy między FaceList i LargeFaceList.
Jak to pokazano LargeFaceList jest dostarczany z nowych operacji pociągu i pobierania stanu szkolenia w porównaniu z FaceList.
Pobieranie LargeFaceList uczony jest warunkiem wstępnym z [FindSimilar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) operacji w chwili, gdy jest nie Train wymagane dla FaceList.
Poniższy fragment kodu jest funkcją pomocnika oczekiwania szkolenia LargeFaceList.

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

Wcześniej będą typowy sposób FaceList dodawania powierzchni i FindSimilar

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

Podczas migrowania go do LargeFaceList, powinien być

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

Jak to pokazano powyżej, zarządzanie danymi i części FindSimilar są prawie takie same.
Jedynym wyjątkiem jest to, że świeże przetwarzania wstępnego operacji pociągu należy wykonać w LargeFaceList przed FindSimilar działa.

## <a name="train"></a>Krok 3: Sugestie Train

Mimo że operacji Train przyspiesza [FindSimilar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) i [identyfikacji](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239), czasu szkoleń wystąpi, zwłaszcza gdy pochodzi na dużą skalę.
W poniższej tabeli znajduje się czas szacowany szkolenia w różnej skali:

| Skala (kroje lub osoby) | Szacowany czas szkolenia |
|:---:|:---:|
| 1000 | s 1 i 2 |
| 10 000 | 5 – 10 s |
| 100,000 | 1 – 2 minuty |
| 1 000 000 | 10 – 30 min. |

Na lepsze wykorzystanie funkcji na dużą skalę, zaleca się wziąć pod uwagę kilka strategii.

## <a name="interval"></a>Krok 3.1: Interwał czasu dostosować

Jak to pokazano w `TrainLargeFaceList()`, istnieje `timeIntervalInMilliseconds` opóźnienia proces sprawdzania stanu nieskończone szkolenia.
Dla LargeFaceList z powierzchni więcej przy użyciu większy interwał zmniejsza liczba wywołań i kosztów.
Przedział czasu, należy dostosować zgodnie z oczekiwanym pojemność LargeFaceList.

Tej samej strategii dotyczą również LargePersonGroup.
Na przykład podczas uczenia LargePersonGroup 1 000 000 osób `timeIntervalInMilliseconds` może być 60 000 () 1-minutowych interwałach).

## <a name="buffer"></a>Krok 3.2 buforu na małą skalę

Osoby/kroje w LargePersonGroup/LargeFaceList są wyszukiwanie tylko po zakończenia uczenia.
W przypadku dynamicznych osób/powierzchnie są stale dodawane i muszą być natychmiast wyszukiwanie jeszcze szkolenie może trwać dłużej niż wymagany.
Aby uniknąć tego problemu, można użyć bardzo niewielkie LargePersonGroup/LargeFaceList jako bufor tylko dla nowo dodanych wpisów.
Bufor przyjmuje krótszy czas do uczenia z powodu znacznie mniejszy rozmiar i natychmiastowe wyszukiwania w tym buforu tymczasowego powinny działać.
Użyj tego buforu w połączeniu z szkoleń w głównym LargePersonGroup/LargeFaceList, wykonując wzorca szkolenia w bardziej rozrzedzonych odstępach czasu, na przykład w nocy pośredniej i codziennie.

Przykładowy przepływ pracy:
1. Tworzenie głównego LargePersonGroup/LargeFaceList (Kolekcja główna) i buforu LargePersonGroup/LargeFaceList (buforu kolekcji). Kolekcja bufor jest tylko w przypadku nowo dodanego osób/powierzchni.
1. Dodaj nowe osób/kroje do wzorca kolekcji i kolekcji buforu.
1. Tylko uczenia kolekcji buforu z interwałem krótki czas, aby upewnić się, działają nowo dodanych wpisów.
1. Wywołaj identyfikacji/FindSimilar przed zarówno wzorca kolekcji i kolekcji buforu oraz scalenie wyniki.
1. Rozmiar buforu kolekcji zwiększa próg lub w czasie bezczynności systemu, Utwórz nową kolekcję buforu, a wyzwolenia pociąg w głównym zbiorze.
1. Usuń stare kolekcję bufor po zakończeniu szkolenia w głównym zbiorze.

## <a name="standalone"></a>Krok 3.3 autonomiczny szkolenia

Czy dopuszczalny jest stosunkowo długi czas oczekiwania, nie jest wymagane do wyzwalania operacji Train bezpośrednio po dodaniu nowych danych.
Zamiast tego operację Train można podzielić na podstawie logiki główny i wyzwalane regularnie.
Ta strategia jest odpowiedni dla scenariuszy dynamicznej z dopuszczalnym czasem oczekiwania i mogą być stosowane do statycznego scenariuszami, aby jeszcze bardziej ograniczyć częstotliwość Train.

Załóżmy, że istnieje `TrainLargePersonGroup` funkcję zbliżoną do `TrainLargeFaceList`.
Typowa implementacja autonomicznej szkolenia w LargePersonGroup wywołując [ `Timer` ](https://msdn.microsoft.com/library/system.timers.timer(v=vs.110).aspx) klasy w `System.Timers` będzie:

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

Więcej informacji na temat zarządzania danymi i powiązane identyfikacji implementacji, zobacz [jak dodać skierowany](how-to-add-faces.md) i [sposób identyfikowania skierowany w obrazie](HowtoIdentifyFacesinImage.md).

## <a name="summary"></a> Podsumowanie

W tym przewodniku zapoznaniu migrację istniejącego kodu PersonGroup/FaceList (a nie dane) do LargePersonGroup/LargeFaceList:

- LargePersonGroup i LargeFaceList działa podobnie do PersonGroup/FaceList, z wyjątkiem Train operacji jest wymagany przez LargeFaceList.
- Strategii właściwej train należy wykonać w celu aktualizacji danych dynamicznych dla zestawu danych na dużą skalę.

## <a name="related"></a> Tematy pokrewne

- [Jak zidentyfikować skierowany w obrazie](HowtoIdentifyFacesinImage.md)
- [Jak dodać powierzchni](how-to-add-faces.md)
