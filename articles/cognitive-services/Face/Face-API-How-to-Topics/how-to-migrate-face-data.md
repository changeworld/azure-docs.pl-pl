---
title: Migruj dane rozpoznawania twarzy w subskrypcjach — interfejs API rozpoznawania twarzy
titleSuffix: Azure Cognitive Services
description: Ten przewodnik pokazuje, jak przeprowadzić migrację danych przechowywanych twarzy z jedną subskrypcją interfejsu API rozpoznawania twarzy do innego.
services: cognitive-services
author: lewlu
manager: cgronlun
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: lewlu
ms.openlocfilehash: 02e9b64c89eda1471d644e0116bbf8c1c061ccc3
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64682532"
---
# <a name="migrate-your-face-data-to-a-different-face-subscription"></a>Migruj dane rozpoznawania twarzy do innej subskrypcji twarzy

Ten przewodnik pokazuje, jak przenosić dane rozpoznawania twarzy (takich jak zapisane **grupie** twarzy) na inną subskrypcję interfejsu API rozpoznawania twarzy, za pomocą funkcji migawki. Dzięki temu można uniknąć konieczności wielokrotnie tworzenie i uczenie **grupie** lub **FaceList** podczas przenoszenia lub rozszerzając operacji. Na przykład, które mogły zostać utworzone **grupie** przy użyciu bezpłatnej subskrypcji próbnej, a teraz chcesz przeprowadzić jej migrację do płatnej subskrypcji lub może być konieczne na synchronizowanie danych z twarzy w wielu regionach dla operacji dużych przedsiębiorstw.

Tej samej strategii migracji ma również zastosowanie do **LargePersonGroup** i **LargeFaceList** obiektów. Jeśli nie znasz koncepcji, w tym przewodniku, zobacz ich definicje [pojęcia rozpoznawania twarzy](../concepts/face-recognition.md) przewodnik. Ten przewodnik korzysta z biblioteki klienta .NET interfejsu API rozpoznawania twarzy, za pomocą C#.

## <a name="prerequisites"></a>Wymagania wstępne

- Dwa interfejsu API rozpoznawania twarzy klucze subskrypcji (z istniejących danych i jedną migrację do). Postępuj zgodnie z instrukcjami w [Tworzenie konta usług Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) do subskrybowania usługi interfejsu API rozpoznawania twarzy i Uzyskaj klucz.
- Interfejs API rozpoznawania twarzy ciąg Identyfikatora subskrypcji, odpowiadający subskrypcja docelowa (znaleziony w **Przegląd** bloku w witrynie Azure portal). 
- Dowolna wersja programu [Visual Studio 2015 lub 2017](https://www.visualstudio.com/downloads/).

## <a name="create-the-visual-studio-project"></a>Tworzenie projektu programu Visual Studio

Ten przewodnik użyje aplikacja konsolowa prosty do wykonania migracji danych twarzy. Aby uzyskać pełną implementację, zobacz [przykład migawki interfejsu API rozpoznawania twarzy](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/FaceApiSnapshotSample/FaceApiSnapshotSample) w witrynie GitHub.

1. W programie Visual Studio Utwórz nowy **Aplikacja konsoli (.NET Framework)** projektu i nadaj mu nazwę **FaceApiSnapshotSample**.
1. Pobierz wymagane pakiety NuGet. Kliknij prawym przyciskiem myszy projekt w Eksploratorze rozwiązań, a następnie wybierz pozycję **Zarządzaj pakietami NuGet**. Kliknij kartę **Przeglądaj**, wybierz pozycję **Uwzględnij wersję wstępną**, a następnie znajdź i zainstaluj następujący pakiet:
    - [Microsoft.Azure.CognitiveServices.Vision.Face 2.3.0-preview](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.Face/2.2.0-preview)

## <a name="create-face-clients"></a>Tworzenie klientów do rozpoznawania twarzy

W **Main** method in Class metoda *Program.cs*, utworzysz dwa **[FaceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient?view=azure-dotnet)** wystąpień dla subskrypcji źródłowej i docelowej. W tym przykładzie użyjemy subskrypcji twarzy w regionie Azja Wschodnia jako źródła i subskrypcji zachodnie stany USA jako element docelowy. To będzie pokazują, jak migrować dane z jednego regionu platformy Azure do innego. W przypadku subskrypcji w różnych regionach, należy zmienić `Endpoint` ciągów.

```csharp
var FaceClientEastAsia = new FaceClient(new ApiKeyServiceClientCredentials("<East Asia Subscription Key>"))
    {
        Endpoint = "https://southeastasia.api.cognitive.microsoft.com/>"
    };

var FaceClientWestUS = new FaceClient(new ApiKeyServiceClientCredentials("<West US Subscription Key>"))
    {
        Endpoint = "https://westus.api.cognitive.microsoft.com/"
    };
```

Należy podać wartości kluczy w subskrypcji i adresy URL punktów końcowych dla subskrypcji źródłowej i docelowej.


## <a name="prepare-a-persongroup-for-migration"></a>Przygotuj grupie do migracji

Potrzebujesz Identyfikatora **grupie** w ramach subskrypcji źródła w celu migrowania go do subskrypcji docelowej. Użyj **[PersonGroupOperationsExtensions.ListAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.persongroupoperationsextensions.listasync?view=azure-dotnet)** metodę, aby pobrać listę z **grupie** obiekty; następnie **[ PersonGroup.PersonGroupId](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.persongroup.persongroupid?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Vision_Face_Models_PersonGroup_PersonGroupId)** właściwości. Ten proces będzie wyglądać różne w zależności od tego, co **grupie** obiektów, masz. W tym przewodnik źródła **grupie** identyfikator jest przechowywany w `personGroupId`.

> [!NOTE]
> [Przykładowego kodu](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/FaceApiSnapshotSample/FaceApiSnapshotSample) tworzy i przygotowuje nowy **grupie** do migracji, ale w większości przypadków należy **grupie** do użycia.

## <a name="take-snapshot-of-persongroup"></a>Utwórz migawkę grupie

Migawka jest tymczasowego magazynu zdalnego dla niektórych typów danych twarzy. Działa ona jako rodzaj Schowka do kopiowania danych z jednej subskrypcji do innego. Najpierw użytkownik "tworzy" migawkę danych w subskrypcji źródłowej, a następnie "odnoszą się" go do nowego obiektu danych w subskrypcji docelowej.

Użyj subskrypcji źródłowej **FaceClient** wystąpienia, aby utworzyć migawkę **grupie**przy użyciu **[TakeAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.snapshotoperationsextensions.takeasync?view=azure-dotnet)** z  **Grupie** ID i identyfikator subskrypcji docelowej. Jeśli masz wiele subskrypcji docelowej, należy dodać ich jako wpisy tablicy w trzeci parametr.

```csharp
var takeSnapshotResult = await FaceClientEastAsia.Snapshot.TakeAsync(
    SnapshotObjectType.PersonGroup,
    personGroupId,
    new[] { "<Azure West US Subscription ID>" /* Put other IDs here, if multiple target subscriptions wanted */ });
```

> [!NOTE]
> Proces pobierania i stosowanie migawek nie będzie zakłócać działanie wszelkie wywołania regularne źródłowa lub docelowa **grupie**s (lub **FaceList**s). Jednak nie zaleca się dokonywania jednoczesnych wywołań, które zmieniają się z obiektem źródłowym ([wywołania zarządzania FaceList](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.facelistoperations?view=azure-dotnet) lub [Train grupie](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.persongroupoperations?view=azure-dotnet) na przykład wywoływać), ponieważ może wykonać operacji migawki Wykonaj przed lub po tych operacji lub mogą wystąpić błędy.

## <a name="retrieve-the-snapshot-id"></a>Pobierz identyfikator migawki

Migawki wykonywania metody jest asynchroniczna, więc musisz czekać na jego zakończenie (migawka, których nie można anulować operacji). W tym kodzie `WaitForOperation` metoda monitoruje wywołania asynchronicznego, sprawdzania stanu każdego 100 MS. Po zakończeniu operacji, można pobrać identyfikatora operacji. Możesz uzyskać je, analizując `OperationLocation` pola. 

```csharp
var takeOperationId = Guid.Parse(takeSnapshotResult.OperationLocation.Split('/')[2]);
var operationStatus = await WaitForOperation(FaceClientEastAsia, takeOperationId);
```

Typowa `OperationLocation` wartość będzie wyglądać następująco:

```csharp
"/operations/a63a3bdd-a1db-4d05-87b8-dbad6850062a"
```

`WaitForOperation` Metody pomocnika jest tutaj:

```csharp
/// <summary>
/// Waits for the take/apply operation to complete and returns the final operation status.
/// </summary>
/// <returns>The final operation status.</returns>
private static async Task<OperationStatus> WaitForOperation(IFaceClient client, Guid operationId)
{
    OperationStatus operationStatus = null;
    do
    {
        if (operationStatus != null)
        {
            Thread.Sleep(TimeSpan.FromMilliseconds(100));
        }

        // Get the status of the operation.
        operationStatus = await client.Snapshot.GetOperationStatusAsync(operationId);

        Console.WriteLine($"Operation Status: {operationStatus.Status}");
    }
    while (operationStatus.Status != OperationStatusType.Succeeded
            && operationStatus.Status != OperationStatusType.Failed);

    return operationStatus;
}
```

Gdy stan operacji jest oznaczony jako `Succeeded`, identyfikator migawki można następnie uzyskać, analizując `ResourceLocation` pole zwracanego **OperationStatus** wystąpienia.

```csharp
var snapshotId = Guid.Parse(operationStatus.ResourceLocation.Split('/')[2]);
```

Typowa `resourceLocation` wartość będzie wyglądać następująco:

```csharp
"/snapshots/e58b3f08-1e8b-4165-81df-aa9858f233dc"
```

## <a name="apply-snapshot-to-target-subscription"></a>Stosowanie migawki do subskrypcji docelowej

Następnie należy utworzyć nowy **grupie** w subskrypcji docelowej przy użyciu losowo wygenerowanego identyfikatora. Następnie użyj subskrypcji docelowej **FaceClient** wystąpienia zakończone powodzeniem zastosować migawki tej grupie, identyfikator migawki i nowe **grupie** identyfikatora. 

```csharp
var newPersonGroupId = Guid.NewGuid().ToString();
var applySnapshotResult = await FaceClientWestUS.Snapshot.ApplyAsync(snapshotId, newPersonGroupId);
```


> [!NOTE]
> Obiekt migawki jest prawidłowy tylko przez 48 godzin. Tylko należy utworzyć migawkę, jeśli zamierzasz używać go w ramach migracji danych wkrótce po.

Zastosuj żądanie migawki zwróci inny identyfikator operacji. Ten identyfikator można uzyskać, analizując `OperationLocation` pole zwracanego **applySnapshotResult** wystąpienia. 

```csharp
var applyOperationId = Guid.Parse(applySnapshotResult.OperationLocation.Split('/')[2]);
```

Proces aplikacji migawki jest asynchroniczne, więc ponownie użyć `WaitForOperation` czekać na jego zakończenie.

```csharp
operationStatus = await WaitForOperation(FaceClientWestUS, applyOperationId);
```

## <a name="test-the-data-migration"></a>Testowanie migracji danych

Po zastosowaniu migawki, nowe **grupie** w docelowej subskrypcji powinny zostać wypełnione oryginalne dane rozpoznawania twarzy. Domyślnie wyniki szkolenia również są kopiowane, więc nowe **grupie** będzie gotowa do wywołania Identyfikacja twarzy bez konieczności ponownego trenowania.

Aby przetestować migracji danych, można uruchomić następujące operacje i należy porównać wyniki, drukowania do konsoli.

```csharp
await DisplayPersonGroup(FaceClientEastAsia, personGroupId);
await IdentifyInPersonGroup(FaceClientEastAsia, personGroupId);

await DisplayPersonGroup(FaceClientWestUS, newPersonGroupId);
// No need to retrain the person group before identification,
// training results are copied by snapshot as well.
await IdentifyInPersonGroup(FaceClientWestUS, newPersonGroupId);
```

Należy użyć następujących metod pomocy:

```csharp
private static async Task DisplayPersonGroup(IFaceClient client, string personGroupId)
{
    var personGroup = await client.PersonGroup.GetAsync(personGroupId);
    Console.WriteLine("Person Group:");
    Console.WriteLine(JsonConvert.SerializeObject(personGroup));

    // List persons.
    var persons = await client.PersonGroupPerson.ListAsync(personGroupId);

    foreach (var person in persons)
    {
        Console.WriteLine(JsonConvert.SerializeObject(person));
    }

    Console.WriteLine();
}
```

```csharp
private static async Task IdentifyInPersonGroup(IFaceClient client, string personGroupId)
{
    using (var fileStream = new FileStream("data\\PersonGroup\\Daughter\\Daughter1.jpg", FileMode.Open, FileAccess.Read))
    {
        var detectedFaces = await client.Face.DetectWithStreamAsync(fileStream);

        var result = await client.Face.IdentifyAsync(detectedFaces.Select(face => face.FaceId.Value).ToList(), personGroupId);
        Console.WriteLine("Test identify against PersonGroup");
        Console.WriteLine(JsonConvert.SerializeObject(result));
        Console.WriteLine();
    }
}
```

Teraz możesz rozpocząć korzystanie z nowej **grupie** w subskrypcji docelowej. 

Jeśli chcesz zaktualizować element docelowy **grupie** ponownie w przyszłości, należy utworzyć nową **grupie** (zgodnie z krokami w tym przewodniku) do odbierania migawki. Pojedynczy **grupie** obiekt może mieć tylko migawki zastosowano jeden raz.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Po zakończeniu migracji danych twarzy zalecamy ręcznie usuń obiekt migawki.

```csharp
await FaceClientEastAsia.Snapshot.DeleteAsync(snapshotId);
```

## <a name="next-steps"></a>Kolejne kroki

Następnie zobacz odpowiednie dokumentacji interfejsu API, zapoznaj się z przykładową aplikację, która używa funkcji migawki lub wykonaj przewodnik, aby rozpocząć korzystanie z innych operacji interfejsu API, wymienione w tym miejscu.

- [Dokumentacja referencyjna migawki (.NET SDK)](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.snapshotoperations?view=azure-dotnet)
- [Przykładowe migawki interfejsu API rozpoznawania twarzy](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/FaceApiSnapshotSample/FaceApiSnapshotSample)
- [Jak dodać twarzy](how-to-add-faces.md)
- [Wykrywanie twarzy na obrazie](HowtoDetectFacesinImage.md)
- [Sposób identyfikacji twarzy na obrazie](HowtoIdentifyFacesinImage.md)
