---
title: Dodaj kroje przy użyciu interfejsu API krój | Dokumentacja firmy Microsoft
titleSuffix: Microsoft Cognitive Services
description: Dodaj kroje obrazów za pomocą interfejsu API krój w usługach kognitywnych.
services: cognitive-services
author: SteveMSFT
manager: corncar
ms.service: cognitive-services
ms.component: face-api
ms.topic: article
ms.date: 03/01/2018
ms.author: sbowles
ms.openlocfilehash: 3306c13d6c3d231ddbda38cfcbc5419fcdbd30db
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347000"
---
# <a name="how-to-add-faces"></a>Jak dodać powierzchni

W tym przewodniku przedstawiono najlepsze rozwiązanie, aby dodać bardzo dużej liczby osób i kroje PersonGroup.
Tej samej strategii dotyczą również FaceList i LargePersonGroup.
Przykłady są zapisywane w języku C# za pomocą biblioteki klienta powierzchni interfejsu API.

## <a name="step1"></a> Krok 1: inicjowanie

Kilka zadeklarować i zaimplementowano funkcji pomocnika można zaplanować żądania.

- `PersonCount` jest to całkowita liczba osób.
- `CallLimitPerSecond` jest maksymalną wywołania na sekundę, zgodnie z poziomu subskrypcji.
- `_timeStampQueue` jest kolejką do rejestrowania sygnatury czasowe żądania.
- `await WaitCallLimitPerSecondAsync()` czeka na zakończenie jest ważny do wysłania następnego żądania.

```CSharp
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

## <a name="step2"></a> Krok 2: Autoryzowanie wywołania interfejsu API

Podczas korzystania z biblioteki klienta, klucz subskrypcji jest przekazywana w konstruktorze klasy FaceServiceClient. Na przykład:

```CSharp
FaceServiceClient faceServiceClient = new FaceServiceClient("<Subscription Key>");
```

Klucz subskrypcji można uzyskać ze strony użytkownika portalu Azure Marketplace. Zobacz [subskrypcje](https://www.microsoft.com/cognitive-services/en-us/sign-up).

## <a name="step3"></a> Krok 3: Tworzenie PersonGroup

PersonGroup o nazwie "MyPersonGroup" jest tworzony zapisać osób.
Czas żądania jest umieszczonych w kolejce do `_timeStampQueue` zapewnienie ogólnej sprawdzania poprawności.

```CSharp
const string personGroupId = "mypersongroupid";
const string personGroupName = "MyPersonGroup";
_timeStampQueue.Enqueue(DateTime.UtcNow);
await faceServiceClient.CreatePersonGroupAsync(personGroupId, personGroupName);
```

## <a name="step4"></a> Krok 4: Tworzenie osobom PersonGroup

Osoby są tworzone jednocześnie i `await WaitCallLimitPerSecondAsync()` jest również stosowane w celu uniknięcia przekroczenia limitu wywołania.

```CSharp
CreatePersonResult[] persons = new CreatePersonResult[PersonCount];
Parallel.For(0, PersonCount, async i =>
{
    await WaitCallLimitPerSecondAsync();

    string personName = $"PersonName#{i}";
    persons[i] = await faceServiceClient.CreatePersonAsync(personGroupId, personName);
});
```

## <a name="step5"></a> Krok 5: Dodawanie kroje osobom

Dodawanie kroje do innej osoby są przetwarzane współbieżnie, natomiast w przypadku jednego określonej osoby jest Sekwencyjna.
Ponownie `await WaitCallLimitPerSecondAsync()` wywoływana w celu zapewnienia częstotliwość żądanie znajduje się w zakresie ograniczenia.

```CSharp
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

## <a name="summary"></a> Podsumowanie

W tym przewodniku uzyskanych w procesie tworzenia PersonGroup bardzo dużej liczby osób i powierzchni. Kilka przypomnienia:

- Ta strategia dotyczą również FaceList i LargePersonGroup.
- Dodawanie/usuwanie kroje różnych FaceLists lub osób pełniących LargePersonGroup mogą być przetwarzane współbieżnie.
- Te same operacje dla jednego określonego FaceList lub osoby w LargePersonGroup powinno być wykonywane sekwencyjnie.
- Aby zachować prostotę, obsługa wyjątków potencjalnych pominięto w tym przewodniku. Jeśli chcesz zwiększyć niezawodność więcej, powinny być stosowane zasady ponawiania właściwe.

Bieżące informacje o funkcji wcześniej wyjaśniono i sprawdzono są następujące:

- Tworzenie za pomocą PersonGroups [PersonGroup — tworzenie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244) interfejsu API
- Tworzenie osoby za pomocą [osoba PersonGroup - utworzyć](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c) interfejsu API
- Dodawanie kroje do osoby za pomocą [PersonGroup osoba — Dodaj krój](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) interfejsu API

## <a name="related"></a> Tematy pokrewne
- [Jak zidentyfikować skierowany w obrazie](HowtoIdentifyFacesinImage.md)
- [Jak wykryć skierowany w obrazie](HowtoDetectFacesinImage.md)
- [Jak używać funkcji na dużą skalę](how-to-use-large-scale.md)
