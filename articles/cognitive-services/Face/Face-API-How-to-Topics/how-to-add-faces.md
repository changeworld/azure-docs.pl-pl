---
title: 'Przykład: Dodaj twarze na grupie — interfejs API rozpoznawania twarzy'
titleSuffix: Azure Cognitive Services
description: Dodawanie twarzy na obrazach za pomocą interfejsu API rozpoznawania twarzy.
services: cognitive-services
author: SteveMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: sample
ms.date: 04/10/2019
ms.author: sbowles
ms.openlocfilehash: 83aef90702e4a4cc4fd9bdfda486841f9b2a63a4
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/23/2019
ms.locfileid: "66124501"
---
# <a name="add-faces-to-a-persongroup"></a>Dodaj twarze na grupie

Ten przewodnik pokazuje, jak dodać dużą liczbę osoby i twarzy do obiektu grupie. Tej samej strategii dotyczy także LargePersonGroup FaceList i LargeFaceList obiektów. W tym przykładzie są zapisywane C# za pomocą biblioteki klienta usługi Azure Cognitive Services powierzchni interfejsu API platformy .NET.

## <a name="step-1-initialization"></a>Krok 1: Inicjalizacja

Poniższy kod deklaruje kilka zmiennych i implementuje dodawania funkcji pomocnika, aby zaplanować twarz żądań:

- `PersonCount` jest łączną liczbą osób.
- `CallLimitPerSecond` jest maksymalną liczbą wywołań na sekundę, zgodnie z warstwą subskrypcji.
- `_timeStampQueue` jest kolejką do rejestrowania znaczników czasu żądań.
- `await WaitCallLimitPerSecondAsync()` czeka, dopóki nie jest prawidłową wysłać następne żądanie.

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

Korzystając z biblioteki klienta, należy przekazać swój klucz subskrypcji do konstruktora klasy FaceServiceClient. Na przykład:

```csharp
FaceServiceClient faceServiceClient = new FaceServiceClient("<Subscription Key>");
```

Aby uzyskać klucz subskrypcji, przejdź do portalu Azure Marketplace w witrynie Azure portal. Aby uzyskać więcej informacji, zobacz [subskrypcje](https://www.microsoft.com/cognitive-services/sign-up).

## <a name="step-3-create-the-persongroup"></a>Krok 3: Tworzenie elementu PersonGroup

Tworzony jest obiekt PersonGroup o nazwie „MyPersonGroup” w celu zapisania osób.
Czas żądania jest umieszczany w kolejce `_timeStampQueue` w celu zapewnienia ogólnej walidacji.

```csharp
const string personGroupId = "mypersongroupid";
const string personGroupName = "MyPersonGroup";
_timeStampQueue.Enqueue(DateTime.UtcNow);
await faceServiceClient.CreatePersonGroupAsync(personGroupId, personGroupName);
```

## <a name="step-4-create-the-persons-for-the-persongroup"></a>Krok 4: Tworzenie osób w grupie

Osoby są tworzone jednocześnie, a `await WaitCallLimitPerSecondAsync()` jest również stosowane w celu uniknięcia przekroczenia limitu wywołania.

```csharp
CreatePersonResult[] persons = new CreatePersonResult[PersonCount];
Parallel.For(0, PersonCount, async i =>
{
    await WaitCallLimitPerSecondAsync();

    string personName = $"PersonName#{i}";
    persons[i] = await faceServiceClient.CreatePersonAsync(personGroupId, personName);
});
```

## <a name="step-5-add-faces-to-the-persons"></a>Krok 5. Dodawanie twarzy do osób

Możliwość współbieżnego przetwarzania powierzchnie, dodawane do różnych osób. Powierzchnie, dodawane do jednej osoby określone są przetwarzane sekwencyjnie.
Ponownie `await WaitCallLimitPerSecondAsync()` jest wywoływana, aby upewnić się, że częstotliwość żądanie znajduje się w zakresie ograniczenia.

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
            await faceServiceClient.AddPersonFaceAsync(personGroupId, personId, stream);
        }
    }
});
```

## <a name="summary"></a>Podsumowanie

W tym przewodniku przedstawiono proces tworzenia grupie z ogromną liczbą osoby i twarzy. Kilka przypomnień:

- Strategia ta dotyczy również na liście twarzy i LargePersonGroups.
- Dodawanie lub usuwanie twarzy do różnych liście twarzy lub osobom LargePersonGroups są przetwarzane równolegle.
- Dodawanie lub usuwanie twarzy do jednego określonego FaceList lub osobą w LargePersonGroup są wykonywane sekwencyjnie.
- Dla uproszczenia sposób obsługi wyjątków potencjalnych zostanie pominięty, w tym przewodniku. Jeśli chcesz zwiększyć niezawodność więcej, mają zastosowanie zasady ponawiania właściwe.

Następujące funkcje zostały wyjaśnione i przedstawione w artykule:

- Tworzenie grup osób przy użyciu [grupie — Tworzenie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244) interfejsu API.
- Utwórz osoby za pomocą [osoba grupie - utworzyć](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c) interfejsu API.
- Dodawanie twarzy do osoby za pomocą [grupie osoba — Dodaj twarzy](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) interfejsu API.

## <a name="related-topics"></a>Tematy pokrewne

- [Identyfikowanie twarzy na obrazie](HowtoIdentifyFacesinImage.md)
- [Wykrywanie twarzy na obrazie](HowtoDetectFacesinImage.md)
- [Użyj funkcji na dużą skalę](how-to-use-large-scale.md)
