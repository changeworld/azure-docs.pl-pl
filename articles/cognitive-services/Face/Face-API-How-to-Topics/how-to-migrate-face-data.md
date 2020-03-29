---
title: Migrowanie danych twarzy w ramach subskrypcji — Face
titleSuffix: Azure Cognitive Services
description: W tym przewodniku pokazano, jak przeprowadzić migrację przechowywanych danych twarzy z jednej subskrypcji Face do innej.
services: cognitive-services
author: lewlu
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: lewlu
ms.openlocfilehash: e5ca51da7322e4eab4ea364ec5da086a1068fa9a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "76169807"
---
# <a name="migrate-your-face-data-to-a-different-face-subscription"></a>Migrowanie danych twarzy do innej subskrypcji Face

W tym przewodniku pokazano, jak przenieść dane twarzy, takie jak zapisany obiekt PersonGroup z twarzami, do innej subskrypcji usługi Azure Cognitive Services Face. Aby przenieść dane, należy użyć funkcji Migawka. W ten sposób można uniknąć konieczności wielokrotnego tworzenia i trenowania PersonGroup lub FaceList obiektu podczas przenoszenia lub rozwijania operacji. Na przykład być może utworzono obiekt PersonGroup przy użyciu bezpłatnej subskrypcji próbnej, a teraz chcesz przeprowadzić migrację do płatnej subskrypcji. Lub może być konieczne zsynchronizowane dane twarzy w ramach subskrypcji w różnych regionach dla operacji dużego przedsiębiorstwa.

Ta sama strategia migracji ma również zastosowanie do LargePersonGroup i LargeFaceList obiektów. Jeśli nie znasz pojęć w tym przewodniku, zobacz ich definicje w przewodniku po [pojęciach rozpoznawania twarzy.](../concepts/face-recognition.md) W tym przewodniku użyto biblioteki klienta Face .NET z c#.

## <a name="prerequisites"></a>Wymagania wstępne

Potrzebne są następujące elementy:

- Dwa klucze subskrypcji Face, jeden z istniejącymi danymi i jeden do migracji. Aby zasubskrybować usługę Face i uzyskać klucz, postępuj zgodnie z instrukcjami w aplikacji [Utwórz konto usług Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account).
- Ciąg identyfikatora subskrypcji face, który odpowiada subskrypcji docelowej. Aby go znaleźć, wybierz **omówienie** w witrynie Azure portal. 
- Dowolna wersja [programu Visual Studio 2015 lub 2017](https://www.visualstudio.com/downloads/).

## <a name="create-the-visual-studio-project"></a>Tworzenie projektu programu Visual Studio

Ten przewodnik używa prostej aplikacji konsoli do uruchamiania migracji danych twarzy. Aby uzyskać pełną implementację, zobacz [przykład migawki twarzy](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/FaceApiSnapshotSample/FaceApiSnapshotSample) w usłudze GitHub.

1. W programie Visual Studio utwórz nowy projekt programu Console .NET Framework. Nazwij go **FaceApiSnapshotSample**.
1. Pobierz wymagane pakiety NuGet. Kliknij prawym przyciskiem myszy projekt w Eksploratorze rozwiązań i wybierz pozycję **Zarządzaj pakietami NuGet**. Wybierz kartę **Przeglądaj** i wybierz pozycję **Dołącz wydanie wstępne**. Znajdź i zainstaluj następujący pakiet:
    - [Microsoft.Azure.CognitiveServices.Vision.Face 2.3.0-preview](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.Face/2.2.0-preview)

## <a name="create-face-clients"></a>Tworzenie klientów twarzy

W **Main** metody w *Program.cs*, utworzyć dwa [FaceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient?view=azure-dotnet) wystąpień dla źródła i subskrypcji docelowych. W tym przykładzie użyto subskrypcji face w regionie Azji Wschodniej jako źródło i zachodnia subskrypcja USA jako miejsce docelowe. W tym przykładzie pokazano, jak migrować dane z jednego regionu platformy Azure do innego. 

[!INCLUDE [subdomains-note](../../../../includes/cognitive-services-custom-subdomains-note.md)]

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

Wypełnij wartości klucza subskrypcji i adresy URL punktów końcowych dla subskrypcji źródłowych i docelowych.


## <a name="prepare-a-persongroup-for-migration"></a>Przygotowanie grupy osób do migracji

Potrzebujesz identyfikatora persongroup w subskrypcji źródłowej, aby przeprowadzić migrację do subskrypcji docelowej. Użyj [metody PersonGroupOperationsExtensions.ListAsync,](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.persongroupoperationsextensions.listasync?view=azure-dotnet) aby pobrać listę obiektów persongroup. Następnie pobierz [właściwość PersonGroup.PersonGroupId.](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.persongroup.persongroupid?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Vision_Face_Models_PersonGroup_PersonGroupId) Ten proces wygląda inaczej w zależności od tego, jakie obiekty PersonGroup masz. W tym przewodniku źródłowy identyfikator grupy `personGroupId`persongroup jest przechowywany w pliku .

> [!NOTE]
> [Przykładowy kod](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/FaceApiSnapshotSample/FaceApiSnapshotSample) tworzy i szkoli nową grupę osób do migracji. W większości przypadków powinieneś już mieć persongroup do użycia.

## <a name="take-a-snapshot-of-a-persongroup"></a>Tworzenie migawki grupy osób

Migawka jest tymczasowym magazynem zdalnym dla niektórych typów danych twarzy. Działa jako rodzaj schowka do kopiowania danych z jednej subskrypcji do drugiej. Najpierw należy zrobić migawkę danych w subskrypcji źródłowej. Następnie należy zastosować go do nowego obiektu danych w subskrypcji docelowej.

Użyj wystąpienia FaceClient subskrypcji źródłowej, aby zrobić migawkę persongroup. Użyj [TakeAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.snapshotoperationsextensions.takeasync?view=azure-dotnet) z identyfikatorem osobygroup i identyfikatorem subskrypcji docelowej. Jeśli masz wiele subskrypcji docelowych, dodaj je jako wpisy tablicy w trzecim parametrze.

```csharp
var takeSnapshotResult = await FaceClientEastAsia.Snapshot.TakeAsync(
    SnapshotObjectType.PersonGroup,
    personGroupId,
    new[] { "<Azure West US Subscription ID>" /* Put other IDs here, if multiple target subscriptions wanted */ });
```

> [!NOTE]
> Proces robienia i stosowania migawek nie zakłóca żadnych regularnych wywołań do źródła lub docelowych persongroups lub FaceLists. Nie należy wykonywać jednoczesne wywołania, które zmieniają obiekt źródłowy, takich jak [FaceList połączeń zarządzania](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.facelistoperations?view=azure-dotnet) lub [persongroup train wywołanie,](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.persongroupoperations?view=azure-dotnet) na przykład. Operacja migawki może działać przed lub po tych operacjach lub może wystąpić błędy.

## <a name="retrieve-the-snapshot-id"></a>Pobieranie identyfikatora migawki

Metoda używana do robienia migawek jest asynchroniczne, więc należy poczekać na jego zakończenie. Nie można anulować operacji migawki. W tym kodzie `WaitForOperation` metoda monitoruje wywołanie asynchroniczne. Sprawdza stan co 100 ms. Po zakończeniu operacji pobierz identyfikator operacji, analizując `OperationLocation` pole. 

```csharp
var takeOperationId = Guid.Parse(takeSnapshotResult.OperationLocation.Split('/')[2]);
var operationStatus = await WaitForOperation(FaceClientEastAsia, takeOperationId);
```

Typowa `OperationLocation` wartość wygląda następująco:

```csharp
"/operations/a63a3bdd-a1db-4d05-87b8-dbad6850062a"
```

Metoda `WaitForOperation` pomocnika jest tutaj:

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

Po po wyświetleniu `Succeeded`stanu operacji pobierz identyfikator migawki, analizując `ResourceLocation` pole zwróconego wystąpienia OperationStatus.

```csharp
var snapshotId = Guid.Parse(operationStatus.ResourceLocation.Split('/')[2]);
```

Typowa `resourceLocation` wartość wygląda następująco:

```csharp
"/snapshots/e58b3f08-1e8b-4165-81df-aa9858f233dc"
```

## <a name="apply-a-snapshot-to-a-target-subscription"></a>Stosowanie migawki do subskrypcji docelowej

Następnie utwórz nową grupę persongroup w subskrypcji docelowej przy użyciu losowo wygenerowanego identyfikatora. Następnie użyj wystąpienia FaceClient subskrypcji docelowej, aby zastosować migawkę do tej grupy osób. Przekaż identyfikator migawki i nowy identyfikator grupy persongroup.

```csharp
var newPersonGroupId = Guid.NewGuid().ToString();
var applySnapshotResult = await FaceClientWestUS.Snapshot.ApplyAsync(snapshotId, newPersonGroupId);
```


> [!NOTE]
> Obiekt Migawka jest prawidłowy tylko przez 48 godzin. Zrób migawkę tylko wtedy, gdy zamierzasz użyć jej do migracji danych wkrótce potem.

Żądanie zastosowania migawki zwraca inny identyfikator operacji. Aby uzyskać ten identyfikator, należy `OperationLocation` przeanalizować pole zwracanego wystąpienia applySnapshotResult. 

```csharp
var applyOperationId = Guid.Parse(applySnapshotResult.OperationLocation.Split('/')[2]);
```

Proces aplikacji migawki jest również asynchroniczne, więc ponownie użyć `WaitForOperation` czekać na zakończenie.

```csharp
operationStatus = await WaitForOperation(FaceClientWestUS, applyOperationId);
```

## <a name="test-the-data-migration"></a>Testowanie migracji danych

Po zastosowaniu migawki, nowy PersonGroup w subskrypcji docelowej wypełnia z oryginalnych danych twarzy. Domyślnie wyniki szkolenia są również kopiowane. Nowa grupa persongroup jest gotowa do wywołania identyfikacji twarzy bez konieczności ponownego szkolenia.

Aby przetestować migrację danych, uruchom następujące operacje i porównaj wyniki, które drukują na konsoli:

```csharp
await DisplayPersonGroup(FaceClientEastAsia, personGroupId);
await IdentifyInPersonGroup(FaceClientEastAsia, personGroupId);

await DisplayPersonGroup(FaceClientWestUS, newPersonGroupId);
// No need to retrain the person group before identification,
// training results are copied by snapshot as well.
await IdentifyInPersonGroup(FaceClientWestUS, newPersonGroupId);
```

Użyj następujących metod pomocnika:

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

Teraz możesz użyć nowej grupy persongroup w subskrypcji docelowej. 

Aby ponownie zaktualizować docelową grupę osób w przyszłości, utwórz nową grupę osób, aby otrzymać migawkę. Aby to zrobić, wykonaj kroki opisane w tym przewodniku. Pojedynczy obiekt PersonGroup może mieć migawkę zastosowaną do niego tylko jeden raz.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Po zakończeniu migracji danych twarzy ręcznie usuń obiekt migawki.

```csharp
await FaceClientEastAsia.Snapshot.DeleteAsync(snapshotId);
```

## <a name="next-steps"></a>Następne kroki

Następnie zapoznaj się z odpowiednią dokumentacją referencyjną interfejsu API, zapoznaj się z przykładową aplikacją, która korzysta z funkcji Migawka, lub postępuj zgodnie z instrukcjami, aby rozpocząć korzystanie z innych operacji interfejsu API wymienionych w tym miejscu:

- [Dokumentacja odwołania do migawek (SDK.NET)](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.snapshotoperations?view=azure-dotnet)
- [Próbka migawki ściany](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/FaceApiSnapshotSample/FaceApiSnapshotSample)
- [Dodawanie twarzy](how-to-add-faces.md)
- [Wykrywanie twarzy na obrazie](HowtoDetectFacesinImage.md)
- [Identyfikowanie twarzy na obrazie](HowtoIdentifyFacesinImage.md)
