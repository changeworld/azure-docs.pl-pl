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
ms.openlocfilehash: 702aed12860c090e83b997e6b56d56e06b416568
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2019
ms.locfileid: "65913788"
---
# <a name="migrate-your-face-data-to-a-different-face-subscription"></a>Migruj dane rozpoznawania twarzy do innej subskrypcji twarzy

Ten przewodnik pokazuje, jak przenieść dane rozpoznawania twarzy, takie jak obiekt grupie zapisane z twarzy, do innej subskrypcji platformy Azure Cognitive Services Face API. Aby przenieść dane, funkcja migawki. W ten sposób można uniknąć konieczności wielokrotnie tworzenie i uczenie grupie lub FaceList obiektu podczas przenoszenia lub rozwiń operacji. Na przykład prawdopodobnie możesz utworzyć obiekt grupie za pomocą bezpłatnej subskrypcji próbnej i teraz chcemy przeprowadzić jej migrację do płatnej subskrypcji. Lub może być konieczne na synchronizowanie danych z twarzy w wielu regionach dla operacji dużych przedsiębiorstw.

Tej samej strategii migracji dotyczy także LargePersonGroup i LargeFaceList obiektów. Jeśli nie znasz koncepcji, w tym przewodniku, zobacz ich definicje [pojęcia rozpoznawania twarzy](../concepts/face-recognition.md) przewodnik. Ten przewodnik korzysta z biblioteki klienta .NET interfejsu API rozpoznawania twarzy, za pomocą C#.

## <a name="prerequisites"></a>Wymagania wstępne

Potrzebne są następujące elementy:

- Dwa interfejsu API rozpoznawania twarzy klucze subskrypcji, jeden z istniejących danych i jedną migrację do. Aby subskrybować usługę interfejsu API rozpoznawania twarzy i Uzyskaj klucz, postępuj zgodnie z instrukcjami [Tworzenie konta usług Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account).
- Interfejs API rozpoznawania twarzy ciąg Identyfikatora subskrypcji, która odnosi się do subskrypcji docelowej. Aby ją znaleźć, wybierz **Przegląd** w witrynie Azure portal. 
- Dowolna wersja programu [Visual Studio 2015 lub 2017](https://www.visualstudio.com/downloads/).

## <a name="create-the-visual-studio-project"></a>Tworzenie projektu programu Visual Studio

Ten przewodnik używa aplikacja konsolowa proste przeprowadzić migrację danych twarzy. Aby uzyskać pełną implementację, zobacz [próbki migawek interfejsu API rozpoznawania twarzy](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/FaceApiSnapshotSample/FaceApiSnapshotSample) w witrynie GitHub.

1. W programie Visual Studio Utwórz nowy projekt konsoli aplikacji .NET Framework. Nadaj mu nazwę **FaceApiSnapshotSample**.
1. Pobierz wymagane pakiety NuGet. Kliknij prawym przyciskiem myszy projekt w Eksploratorze rozwiązań, a następnie wybierz pozycję **Zarządzaj pakietami NuGet**. Wybierz **Przeglądaj** , a następnie wybierz pozycję **Uwzględnij wersję wstępną**. Znajdź i zainstaluj następującego pakietu:
    - [Microsoft.Azure.CognitiveServices.Vision.Face 2.3.0-preview](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.Face/2.2.0-preview)

## <a name="create-face-clients"></a>Tworzenie klientów do rozpoznawania twarzy

W **Main** method in Class metoda *Program.cs*, utworzysz dwa [FaceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient?view=azure-dotnet) wystąpień dla subskrypcji źródłowej i docelowej. W tym przykładzie używa subskrypcji twarzy w regionie Azja Wschodnia jako źródła i subskrypcji zachodnie stany USA, jako element docelowy. W tym przykładzie pokazano, jak migrować dane z jednego regionu platformy Azure do innego. W przypadku subskrypcji w różnych regionach, należy zmienić `Endpoint` ciągów.

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

Wypełnienie wartości kluczy w subskrypcji i adresy URL punktów końcowych dla subskrypcji źródłowej i docelowej.


## <a name="prepare-a-persongroup-for-migration"></a>Przygotuj grupie do migracji

Identyfikator grupie są potrzebne w Twojej subskrypcji źródła w celu migrowania go do subskrypcji docelowej. Użyj [PersonGroupOperationsExtensions.ListAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.persongroupoperationsextensions.listasync?view=azure-dotnet) metodę, aby pobrać listę obiektów grupie. Następnie Uzyskaj [PersonGroup.PersonGroupId](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.persongroup.persongroupid?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Vision_Face_Models_PersonGroup_PersonGroupId) właściwości. Proces ten wygląda inaczej w oparciu o jakie grupie obiektów, masz. W tym przewodniku źródłowej grupie identyfikator znajduje się w `personGroupId`.

> [!NOTE]
> [Przykładowego kodu](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/FaceApiSnapshotSample/FaceApiSnapshotSample) tworzy i przygotowuje nowy grupie, aby przeprowadzić migrację. W większości przypadków powinno mieć już grupie do użycia.

## <a name="take-a-snapshot-of-a-persongroup"></a>Utwórz migawkę grupie

Migawka jest tymczasowego magazynu zdalnego dla niektórych typów danych twarzy. Działa ona jako rodzaj Schowka do kopiowania danych z jednej subskrypcji do innego. Najpierw należy utworzyć migawkę danych w subskrypcji źródłowej. Następnie możesz zastosować go do nowego obiektu danych w subskrypcji docelowej.

Aby utworzyć migawkę grupie, należy użyć wystąpienia FaceClient subskrypcji źródłowej. Użyj [TakeAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.snapshotoperationsextensions.takeasync?view=azure-dotnet) o identyfikatorze grupie i identyfikator subskrypcji docelowej. Jeśli masz wiele subskrypcji docelowej, należy dodać je jako tablicę wpisów w trzeci parametr.

```csharp
var takeSnapshotResult = await FaceClientEastAsia.Snapshot.TakeAsync(
    SnapshotObjectType.PersonGroup,
    personGroupId,
    new[] { "<Azure West US Subscription ID>" /* Put other IDs here, if multiple target subscriptions wanted */ });
```

> [!NOTE]
> Proces pobierania i stosowanie migawek nie zakłócać wszelkie regularne wywołania do źródła i docelowych grup osób lub na liście twarzy. Nie wprowadzaj jednoczesnych wywołań, które zmieniają obiektu źródłowego, takich jak [wywołania zarządzania FaceList](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.facelistoperations?view=azure-dotnet) lub [Train grupie](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.persongroupoperations?view=azure-dotnet) na przykład wywoływać. Operacja migawki mogą zostać uruchomione przed lub po tych operacji lub mogą wystąpić błędy.

## <a name="retrieve-the-snapshot-id"></a>Pobierz identyfikator migawki

Metoda używany do robienia migawek jest asynchroniczna, więc musi czekać na jego zakończenie. Nie można anulować operacji tworzenia migawek. W tym kodzie `WaitForOperation` metoda monitoruje wywołania asynchronicznego. Sprawdza stan każdego 100 ms. Po zakończeniu operacji, należy pobrać identyfikator operacji, analizując `OperationLocation` pola. 

```csharp
var takeOperationId = Guid.Parse(takeSnapshotResult.OperationLocation.Split('/')[2]);
var operationStatus = await WaitForOperation(FaceClientEastAsia, takeOperationId);
```

Typowa `OperationLocation` wartość wygląda następująco:

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

Po stan operacji pokazuje `Succeeded`, Uzyskaj identyfikator migawki, analizując `ResourceLocation` pola wystąpienia OperationStatus zwrócone.

```csharp
var snapshotId = Guid.Parse(operationStatus.ResourceLocation.Split('/')[2]);
```

Typowa `resourceLocation` wartość wygląda następująco:

```csharp
"/snapshots/e58b3f08-1e8b-4165-81df-aa9858f233dc"
```

## <a name="apply-a-snapshot-to-a-target-subscription"></a>Stosowanie migawki do subskrypcji docelowej

Następnie należy utworzyć nowej grupie w subskrypcji docelowej przy użyciu losowo wygenerowanego identyfikatora. Następnie użyj wystąpienia FaceClient subskrypcji docelowej w celu zastosowania migawki na tej grupie. Przekaż w migawce, identyfikator i nowy identyfikator grupie.

```csharp
var newPersonGroupId = Guid.NewGuid().ToString();
var applySnapshotResult = await FaceClientWestUS.Snapshot.ApplyAsync(snapshotId, newPersonGroupId);
```


> [!NOTE]
> Obiekt migawki jest prawidłowy tylko przez 48 godzin. Tylko, jeśli zamierzasz używać go w ramach migracji danych, Utwórz migawkę wkrótce po.

Zastosuj żądanie migawki zwraca inny identyfikator operacji. Aby uzyskać ten identyfikator, przeanalizować `OperationLocation` pola wystąpienia applySnapshotResult zwrócone. 

```csharp
var applyOperationId = Guid.Parse(applySnapshotResult.OperationLocation.Split('/')[2]);
```

Proces aplikacji migawki jest asynchroniczne, więc ponownie użyć `WaitForOperation` oczekiwania na zakończenie jego działania.

```csharp
operationStatus = await WaitForOperation(FaceClientWestUS, applyOperationId);
```

## <a name="test-the-data-migration"></a>Testowanie migracji danych

Po zastosowaniu migawki nowej grupie w subskrypcji docelowej zostaną wyświetlone wszystkie oryginalne dane rozpoznawania twarzy. Domyślnie wyniki szkolenia również są kopiowane. Nowej grupie jest gotowa do wywołania Identyfikacja twarzy bez konieczności ponownego trenowania.

Aby przetestować migracji danych, należy uruchomić następujące operacje i porównać wyniki, drukowania do konsoli:

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

Teraz można użyć nowej grupie w subskrypcji docelowej. 

Aby zaktualizować docelowej grupie w przyszłości, należy utworzyć nowej grupie, aby otrzymywać migawki. Aby to zrobić, wykonaj kroki opisane w tym przewodniku. Pojedynczy obiekt grupie może mieć migawki zastosować tylko jeden raz.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Po zakończeniu migracji danych twarzy, ręcznie usuń obiekt migawki.

```csharp
await FaceClientEastAsia.Snapshot.DeleteAsync(snapshotId);
```

## <a name="next-steps"></a>Kolejne kroki

Następnie zobacz odpowiednie dokumentacji interfejsu API, zapoznaj się z przykładową aplikację, która używa funkcji migawki lub wykonaj przewodnik, aby rozpocząć korzystanie z innych operacji interfejsu API wymienionych poniżej:

- [Dokumentacja referencyjna migawki (.NET SDK)](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.snapshotoperations?view=azure-dotnet)
- [Interfejs API rozpoznawania twarzy migawki próbki](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/FaceApiSnapshotSample/FaceApiSnapshotSample)
- [Dodaj twarzy](how-to-add-faces.md)
- [Wykrywanie twarzy na obrazie](HowtoDetectFacesinImage.md)
- [Identyfikowanie twarzy na obrazie](HowtoIdentifyFacesinImage.md)
