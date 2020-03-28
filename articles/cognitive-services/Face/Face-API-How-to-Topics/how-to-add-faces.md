---
title: 'Przykład: Dodawanie ścian do grupy osób — ściana'
titleSuffix: Azure Cognitive Services
description: W tym przewodniku pokazano, jak dodać dużą liczbę osób i twarzy do obiektu PersonGroup za pomocą usługi Azure Cognitive Services Face.
services: cognitive-services
author: SteveMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: sample
ms.date: 04/10/2019
ms.author: sbowles
ms.openlocfilehash: 240905d538afc5c0f4b7f0e0bf400fac23c3183f
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76169826"
---
# <a name="add-faces-to-a-persongroup"></a>Dodawanie ścian do grupy osób

W tym przewodniku pokazano, jak dodać dużą liczbę osób i twarzy do obiektu PersonGroup. Ta sama strategia dotyczy również LargePersonGroup, FaceList i LargeFaceList obiektów. Ten przykład jest napisany w języku C# przy użyciu biblioteki klienta usługi Azure Cognitive Services Face .NET.

## <a name="step-1-initialization"></a>Krok 1: Inicjowanie

Poniższy kod deklaruje kilka zmiennych i implementuje funkcję pomocnika, aby zaplanować żądania dodawania twarzy:

- `PersonCount` jest łączną liczbą osób.
- `CallLimitPerSecond` jest maksymalną liczbą wywołań na sekundę, zgodnie z warstwą subskrypcji.
- `_timeStampQueue` jest kolejką do rejestrowania znaczników czasu żądań.
- `await WaitCallLimitPerSecondAsync()`czeka, aż będzie prawidłowy, aby wysłać następne żądanie.

```csharp
const int PersonCount = 10000;
const int CallLimitPerSecond = 10;
static Queue<DateTime> _timeStampQueue = new Queue<DateTime>(CallLimitPerSecond);

static async Task WaitCallLimitPerSecondAsync()
{
    Monitor.Enter(_timeStampQueue);
    try
    {
        if (_timeStampQueue.Count >= CallLimitPerSecond)
        {
            TimeSpan timeInterval = DateTime.UtcNow - _timeStampQueue.Peek();
            if (timeInterval < TimeSpan.FromSeconds(1))
            {
                await Task.Delay(TimeSpan.FromSeconds(1) - timeInterval);
            }
            _timeStampQueue.Dequeue();
        }
        _timeStampQueue.Enqueue(DateTime.UtcNow);
    }
    finally
    {
        Monitor.Exit(_timeStampQueue);
    }
}
```

## <a name="step-2-authorize-the-api-call"></a>Krok 2: Autoryzowanie wywołania interfejsu API

Korzystając z biblioteki klienta, należy przekazać klucz subskrypcji do konstruktora **FaceClient** klasy. Przykład:

```csharp
private readonly IFaceClient faceClient = new FaceClient(
    new ApiKeyServiceClientCredentials("<SubscriptionKey>"),
    new System.Net.Http.DelegatingHandler[] { });
```

Aby uzyskać klucz subskrypcji, przejdź do portalu Azure Marketplace z witryny Azure. Aby uzyskać więcej informacji, zobacz [Subskrypcje](https://www.microsoft.com/cognitive-services/sign-up).

## <a name="step-3-create-the-persongroup"></a>Krok 3: Tworzenie obiektu PersonGroup

Tworzony jest obiekt PersonGroup o nazwie „MyPersonGroup” w celu zapisania osób.
Czas żądania jest umieszczany w kolejce `_timeStampQueue` w celu zapewnienia ogólnej walidacji.

```csharp
const string personGroupId = "mypersongroupid";
const string personGroupName = "MyPersonGroup";
_timeStampQueue.Enqueue(DateTime.UtcNow);
await faceClient.LargePersonGroup.CreateAsync(personGroupId, personGroupName);
```

## <a name="step-4-create-the-persons-for-the-persongroup"></a>Krok 4: Tworzenie osób dla grupy osób

Osoby są tworzone jednocześnie i `await WaitCallLimitPerSecondAsync()` jest również stosowany w celu uniknięcia przekroczenia limitu wywołania.

```csharp
Person[] persons = new Person[PersonCount];
Parallel.For(0, PersonCount, async i =>
{
    await WaitCallLimitPerSecondAsync();

    string personName = $"PersonName#{i}";
    persons[i] = await faceClient.PersonGroupPerson.CreateAsync(personGroupId, personName);
});
```

## <a name="step-5-add-faces-to-the-persons"></a>Krok 5: Dodawanie twarzy do osób

Twarze dodane do różnych osób są przetwarzane jednocześnie. Ściany dodane dla jednej konkretnej osoby są przetwarzane sekwencyjnie.
Ponownie `await WaitCallLimitPerSecondAsync()` jest wywoływana w celu zapewnienia, że częstotliwość żądania jest w zakresie ograniczenia.

```csharp
Parallel.For(0, PersonCount, async i =>
{
    Guid personId = persons[i].PersonId;
    string personImageDir = @"/path/to/person/i/images";

    foreach (string imagePath in Directory.GetFiles(personImageDir, "*.jpg"))
    {
        await WaitCallLimitPerSecondAsync();

        using (Stream stream = File.OpenRead(imagePath))
        {
            await faceClient.PersonGroupPerson.AddFaceFromStreamAsync(personGroupId, personId, stream);
        }
    }
});
```

## <a name="summary"></a>Podsumowanie

W tym przewodniku poznaliście proces tworzenia grupy osób z ogromną liczbą osób i twarzy. Kilka przypomnień:

- Ta strategia dotyczy również FaceLists i LargePersonGroups.
- Dodawanie lub usuwanie twarzy do różnych list twarzy lub osób w LargePersonGroups są przetwarzane jednocześnie.
- Dodawanie lub usuwanie twarzy do jednej konkretnej listy facelist lub osoby w LargePersonGroup są wykonywane sekwencyjnie.
- Dla uproszczenia sposób obsługi potencjalnego wyjątku jest pominięty w tym przewodniku. Jeśli chcesz zwiększyć niezawodność, zastosuj odpowiednie zasady ponawiania.

Wyjaśniono i wykazano następujące cechy:

- Tworzenie grup osób przy użyciu [grupy osób — tworzenie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244) interfejsu API.
- Tworzenie osób przy użyciu [persongroup persongroup - Tworzenie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c) interfejsu API.
- Dodawanie twarzy do osób za pomocą interfejsu API [osoby — dodawanie twarzy.](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b)

## <a name="related-topics"></a>Powiązane tematy

- [Identyfikowanie twarzy na obrazie](HowtoIdentifyFacesinImage.md)
- [Wykrywanie twarzy na obrazie](HowtoDetectFacesinImage.md)
- [Korzystanie z funkcji na dużą skalę](how-to-use-large-scale.md)
