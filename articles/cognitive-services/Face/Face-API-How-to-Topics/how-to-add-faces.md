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
ms.openlocfilehash: 04fe9251ba124ed5d218daf915339c7f84efdeb6
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64704178"
---
# <a name="how-to-add-faces-to-a-persongroup"></a>Jak dodać twarze na grupie

Ten przewodnik przedstawia najlepsze rozwiązania dotyczące dodawania dużej liczby osoby i twarzy na obiekt w grupie. Tej samej strategii dotyczy także LargePersonGroup FaceList i LargeFaceList. W tym przykładzie są zapisywane C# przy użyciu biblioteki klienta .NET interfejsu API rozpoznawania twarzy.

## <a name="step-1-initialization"></a>Krok 1: Inicjowanie

Poniższy kod deklaruje kilka zmiennych i implementuje dodawania funkcji pomocnika, aby zaplanować twarz żądań.

- `PersonCount` jest łączną liczbą osób.
- `CallLimitPerSecond` jest maksymalną liczbą wywołań na sekundę, zgodnie z warstwą subskrypcji.
- `_timeStampQueue` jest kolejką do rejestrowania znaczników czasu żądań.
- `await WaitCallLimitPerSecondAsync()` powoduje oczekiwanie na moment, kiedy można prawidłowo wysłać kolejne żądanie.

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

Klucz subskrypcji można uzyskać na stronie Marketplace witryny Azure Portal. Zobacz [Subskrypcje](https://www.microsoft.com/cognitive-services/sign-up).

## <a name="step-3-create-the-persongroup"></a>Krok 3: Tworzenie elementu PersonGroup

Tworzony jest obiekt PersonGroup o nazwie „MyPersonGroup” w celu zapisania osób.
Czas żądania jest umieszczany w kolejce `_timeStampQueue` w celu zapewnienia ogólnej walidacji.

```csharp
const string personGroupId = "mypersongroupid";
const string personGroupName = "MyPersonGroup";
_timeStampQueue.Enqueue(DateTime.UtcNow);
await faceServiceClient.CreatePersonGroupAsync(personGroupId, personGroupName);
```

## <a name="step-4-create-the-persons-to-the-persongroup"></a>Krok 4: Tworzenie osób w obiekcie PersonGroup

Osoby są tworzone jednocześnie i stosowana jest także funkcja `await WaitCallLimitPerSecondAsync()` w celu uniknięcia przekroczenia limitu wywołań.

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

Operacje dodawania twarzy do różnych osób są przetwarzane współbieżnie, natomiast w przypadku jednej określonej osoby jest to operacja sekwencyjna.
Ponownie wywoływana jest funkcja `await WaitCallLimitPerSecondAsync()` w celu zapewnienia, że częstotliwość żądań znajduje się w zakresie ograniczenia.

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

W tym przewodniku przedstawiono proces tworzenia obiektu PersonGroup z dużą liczbą osób i twarzy. Kilka przypomnień:

- Ta strategia dotyczy także obiektów FaceList i LargePersonGroup.
- Dodawanie/usuwanie twarzy dla różnych obiektów FaceLists lub Persons w LargePersonGroup może być przetwarzane współbieżnie.
- Te same operacje dla jednego określonego obiektu FaceList lub Person w LargePersonGroup powinny być wykonywane sekwencyjnie.
- Aby zachować prostotę, obsługa potencjalnych wyjątków została pominięta w tym przewodniku. Jeśli chcesz zwiększyć niezawodność, należy zastosować właściwe zasady ponawiania.

Poniżej podano krótkie przypomnienie wcześniej opisanych i przedstawionych funkcji:

- Tworzenie grup osób za pomocą interfejsu API [PersonGroup - Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244)
- Tworzenie osób za pomocą interfejsu API [PersonGroup Person - Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c)
- Dodawanie twarzy do osób za pomocą interfejsu API [PersonGroup Person - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b)

## <a name="related-topics"></a>Tematy pokrewne

- [Identyfikowanie twarzy na obrazie](HowtoIdentifyFacesinImage.md)
- [Wykrywanie twarzy na obrazie](HowtoDetectFacesinImage.md)
- [Jak używać funkcji na dużą skalę](how-to-use-large-scale.md)
