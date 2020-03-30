---
title: Utrwalanie danych wyjściowych w usłudze Azure Storage za pomocą interfejsu API usługi wsadowej — usługa Azure Batch
description: Dowiedz się, jak używać interfejsu API usługi batch do utrwalania danych wyjściowych zadań i zadań usługi Batch do usługi Azure Storage.
services: batch
author: LauraBrenner
manager: evansma
editor: ''
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 03/05/2019
ms.author: labrenne
ms.custom: seodec18
ms.openlocfilehash: 11bd8bc427dd3da35ec5aa0f728f6b04b7d4527d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77022855"
---
# <a name="persist-task-data-to-azure-storage-with-the-batch-service-api"></a>Utrwalanie danych zadań w usłudze Azure Storage za pomocą interfejsu API usługi wsadowej

[!INCLUDE [batch-task-output-include](../../includes/batch-task-output-include.md)]

Interfejs API usługi wsadowej obsługuje utrwalanie danych wyjściowych do usługi Azure Storage dla zadań i zadań menedżera zadań, które są uruchamiane w pulach z konfiguracją maszyny wirtualnej. Po dodaniu zadania można określić kontener w usłudze Azure Storage jako miejsce docelowe dla danych wyjściowych zadania. Usługa Batch następnie zapisuje wszystkie dane wyjściowe do tego kontenera po zakończeniu zadania.

Zaletą przy użyciu interfejsu API usługi batch do utrwalania danych wyjściowych zadań jest to, że nie trzeba modyfikować aplikacji, która jest uruchomiona zadanie. Zamiast tego z kilku modyfikacji aplikacji klienckiej, można utrwalić dane wyjściowe zadania z tego samego kodu, który tworzy zadanie.

## <a name="when-do-i-use-the-batch-service-api-to-persist-task-output"></a>Kiedy używać interfejsu API usługi batch do utrwalania danych wyjściowych zadań?

Usługa Azure Batch udostępnia więcej niż jeden sposób utrwalania danych wyjściowych zadań. Korzystanie z interfejsu API usługi batch jest wygodne podejście, które najlepiej nadaje się do tych scenariuszy:

- Chcesz napisać kod, aby utrwalić dane wyjściowe zadania z poziomu aplikacji klienckiej, bez modyfikowania aplikacji, która jest uruchomiona.
- Chcesz utrwalić dane wyjściowe z zadań usługi Batch i zadań menedżera zadań w pulach utworzonych za pomocą konfiguracji maszyny wirtualnej.
- Chcesz utrwalić dane wyjściowe do kontenera usługi Azure Storage o dowolnej nazwie.
- Chcesz utrwalić dane wyjściowe do kontenera usługi Azure Storage o nazwie zgodnie ze [standardem Konwencje plików wsadowych.](https://github.com/Azure/azure-sdk-for-net/tree/psSdkJson6/src/SDKs/Batch/Support/FileConventions#conventions) 

Jeśli scenariusz różni się od wymienionych powyżej, może być konieczne rozważenie innego podejścia. Na przykład interfejs API usługi batch nie obsługuje obecnie przesyłania strumieniowego danych wyjściowych do usługi Azure Storage, gdy zadanie jest uruchomione. Aby przesyłać strumieniowo dane wyjściowe, należy rozważyć użycie biblioteki Konwencje plików wsadowych, dostępnej dla platformy .NET. W przypadku innych języków należy zaimplementować własne rozwiązanie. Aby uzyskać więcej informacji na temat innych opcji utrwalania danych wyjściowych zadań, zobacz [Utrwalanie zadania i danych wyjściowych zadań w usłudze Azure Storage.](batch-task-output.md)

## <a name="create-a-container-in-azure-storage"></a>Tworzenie kontenera w usłudze Azure Storage

Aby utrwalić dane wyjściowe zadania w usłudze Azure Storage, należy utworzyć kontener, który służy jako miejsce docelowe dla plików wyjściowych. Utwórz kontener przed uruchomieniem zadania, najlepiej przed przesłaniem zadania. Aby utworzyć kontener, należy użyć odpowiedniej biblioteki klienta usługi Azure Storage lub zestawu SDK. Aby uzyskać więcej informacji na temat interfejsów API usługi Azure Storage, zobacz [dokumentację usługi Azure Storage.](https://docs.microsoft.com/azure/storage/)

Na przykład jeśli piszesz aplikację w języku C#, użyj [biblioteki klienta usługi Azure Storage dla platformy .NET](https://www.nuget.org/packages/WindowsAzure.Storage/). W poniższym przykładzie pokazano, jak utworzyć kontener:

```csharp
CloudBlobContainer container = storageAccount.CreateCloudBlobClient().GetContainerReference(containerName);
await container.CreateIfNotExists();
```

## <a name="get-a-shared-access-signature-for-the-container"></a>Uzyskiwanie podpisu dostępu współdzielonego dla kontenera

Po utworzeniu kontenera, pobierz podpis dostępu współdzielonego (SAS) z dostępem do zapisu do kontenera. Sygnatura dostępu współdzielonego zapewnia delegowany dostęp do kontenera. Sygnatura dostępu zapewnia dostęp z określonym zestawem uprawnień i w określonym przedziale czasu. Usługa Batch potrzebuje sygnatury dostępu Współdzielonego z uprawnieniami zapisu do zapisu danych wyjściowych zadania do kontenera. Aby uzyskać więcej informacji na temat sygnatury dostępu [współdzielonego, zobacz Używanie współdzielonego sygnatur dostępu współdzielonego \(\) w usłudze Azure Storage](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

Po otrzymaniu sygnatury dostępu Współdzielonego przy użyciu interfejsów API usługi Azure Storage interfejs API zwraca ciąg tokenu sygnatury dostępu Współdzielonego. Ten ciąg tokenu zawiera wszystkie parametry sygnatury dostępu Współdzielonego, w tym uprawnienia i interwał, w którym sygnatury dostępu Współdzielonego jest prawidłowy. Aby użyć sygnatury dostępu współdzielonego do uzyskiwania dostępu do kontenera w usłudze Azure Storage, należy dołączyć ciąg tokenu sygnatury dostępu współdzielonego do identyfikatora URI zasobu. Identyfikator URI zasobu wraz z dołączonego tokenu Sygnatury dostępu współdzielonego zapewnia uwierzytelniony dostęp do usługi Azure Storage.

W poniższym przykładzie pokazano, jak uzyskać ciąg tokenu tylko do zapisu sygnatury dostępu Współdzielonego dla kontenera, a następnie dołącza sygnatury dostępu Współdzielonego do identyfikatora URI kontenera:

```csharp
string containerSasToken = container.GetSharedAccessSignature(new SharedAccessBlobPolicy()
{
    SharedAccessExpiryTime = DateTimeOffset.UtcNow.AddDays(1),
    Permissions = SharedAccessBlobPermissions.Write
});

string containerSasUrl = container.Uri.AbsoluteUri + containerSasToken;
```

## <a name="specify-output-files-for-task-output"></a>Określanie plików wyjściowych dla danych wyjściowych zadań

Aby określić pliki wyjściowe dla zadania, należy utworzyć kolekcję obiektów [OutputFile](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.outputfile) i przypisać go do [cloudtask.OutputFiles](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudtask.outputfiles#Microsoft_Azure_Batch_CloudTask_OutputFiles) właściwości podczas tworzenia zadania.

Poniższy przykład kodu Języka C# tworzy zadanie, które `output.txt`zapisuje liczby losowe do pliku o nazwie . Przykład tworzy plik wyjściowy `output.txt` do zapisania w kontenerze. W przykładzie tworzone są również pliki wyjściowe `std*.txt` dla wszystkich plików `stdout.txt` dziennika, które pasują do wzorca pliku ( `stderr.txt`_np._ Adres URL kontenera wymaga sygnatury dostępu Współdzielonego utworzonego wcześniej dla kontenera. Usługa Batch używa sygnatury dostępu Współdzielonego do uwierzytelniania dostępu do kontenera:

```csharp
new CloudTask(taskId, "cmd /v:ON /c \"echo off && set && (FOR /L %i IN (1,1,100000) DO (ECHO !RANDOM!)) > output.txt\"")
{
    OutputFiles = new List<OutputFile>
    {
        new OutputFile(
            filePattern: @"..\std*.txt",
            destination: new OutputFileDestination(
         new OutputFileBlobContainerDestination(
                    containerUrl: containerSasUrl,
                    path: taskId)),
            uploadOptions: new OutputFileUploadOptions(
            uploadCondition: OutputFileUploadCondition.TaskCompletion)),
        new OutputFile(
            filePattern: @"output.txt",
            destination: 
         new OutputFileDestination(new OutputFileBlobContainerDestination(
                    containerUrl: containerSasUrl,
                    path: taskId + @"\output.txt")),
            uploadOptions: new OutputFileUploadOptions(
            uploadCondition: OutputFileUploadCondition.TaskCompletion)),
}
```

### <a name="specify-a-file-pattern-for-matching"></a>Określanie wzorca pliku do dopasowania

Po określeniu pliku wyjściowego można użyć właściwości [OutputFile.FilePattern,](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.outputfile.filepattern#Microsoft_Azure_Batch_OutputFile_FilePattern) aby określić wzorzec pliku do dopasowania. Wzorzec pliku może odpowiadać zero plików, pojedynczy plik lub zestaw plików, które są tworzone przez zadanie.

**Właściwość FilePattern** obsługuje standardowe symbole `*` wieloznaczne systemu plików, takie `**` jak (dla dopasowań niecyklicznych) i (dla dopasowań cyklicznych). Na przykład powyższy przykład kodu określa wzorzec pliku, który ma być zgodny `std*.txt` z niecyklicznym:

`filePattern: @"..\std*.txt"`

Aby przekazać pojedynczy plik, określ wzorzec pliku bez symboli wieloznacznych. Na przykład powyższy przykład kodu określa wzorzec pliku, który ma być zgodny: `output.txt`

`filePattern: @"output.txt"`

### <a name="specify-an-upload-condition"></a>Określanie warunku przekazywania

[Właściwość OutputFileUploadOptions.UploadCondition](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.outputfileuploadoptions.uploadcondition#Microsoft_Azure_Batch_OutputFileUploadOptions_UploadCondition) umożliwia warunkowe przekazywanie plików wyjściowych. Typowym scenariuszem jest przekazanie jednego zestawu plików, jeśli zadanie zakończy się pomyślnie, i innego zestawu plików, jeśli zakończy się niepowodzeniem. Na przykład można przekazać pełne pliki dziennika tylko wtedy, gdy zadanie zakończy się niepowodzeniem i kończy pracę z kodem zakończenia niezerowego. Podobnie można przekazać pliki wyników tylko wtedy, gdy zadanie zakończy się pomyślnie, ponieważ te pliki mogą brakować lub niekompletne, jeśli zadanie nie powiedzie się.

Powyższy przykład kodu ustawia właściwość **UploadCondition** na **TaskCompletion**. To ustawienie określa, że plik ma zostać przekazany po zakończeniu zadań, niezależnie od wartości kodu zakończenia.

`uploadCondition: OutputFileUploadCondition.TaskCompletion`

Aby uzyskać inne ustawienia, zobacz [OutputFileUploadCondition wyliczenia.](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.common.outputfileuploadcondition)

### <a name="disambiguate-files-with-the-same-name"></a>Rozróżnianie plików o tej samej nazwie

Zadania w zadaniu mogą tworzyć pliki o tej samej nazwie. Na przykład `stdout.txt` `stderr.txt` i są tworzone dla każdego zadania, które jest uruchamiane w zadaniu. Ponieważ każde zadanie jest uruchamiane we własnym kontekście, pliki te nie są w konflikcie w systemie plików węzła. Jednak podczas przekazywania plików z wielu zadań do udostępnionego kontenera, należy odróżnić pliki o tej samej nazwie.

[Właściwość OutputFileBlcontainerDestination.Path](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.outputfileblobcontainerdestination.path#Microsoft_Azure_Batch_OutputFileBlobContainerDestination_Path) określa docelowy obiekt blob lub katalog wirtualny dla plików wyjściowych. Właściwości **Path** można użyć do nazwania obiektu blob lub katalogu wirtualnego w taki sposób, aby pliki wyjściowe o tej samej nazwie były unikatowo nazwane w usłudze Azure Storage. Używanie identyfikatora zadania w ścieżce jest dobrym sposobem zapewnienia unikatowych nazw i łatwej identyfikacji plików.

Jeśli **Właściwość FilePattern** jest ustawiona na wyrażenie symboli wieloznacznych, wszystkie pliki pasujące do wzorca są przekazywane do katalogu wirtualnego określonego przez **path** właściwości. Na przykład jeśli kontener `mycontainer`jest , identyfikator `mytask`zadania jest , `..\std*.txt`a wzorzec pliku jest , a bezwzględne identyfikatory URI do plików wyjściowych w usłudze Azure Storage będzie podobny do:

```
https://myaccount.blob.core.windows.net/mycontainer/mytask/stderr.txt
https://myaccount.blob.core.windows.net/mycontainer/mytask/stdout.txt
```

Jeśli **Właściwość FilePattern** jest ustawiona tak, aby odpowiadała nazwie pojedynczego pliku, co oznacza, że nie zawiera żadnych symboli wieloznacznych, wartość **właściwości Path** określa w pełni kwalifikowaną nazwę obiektu blob. Jeśli przewidujesz konflikty nazw z pojedynczym plikiem z wielu zadań, należy dołączyć nazwę katalogu wirtualnego jako część nazwy pliku, aby odróżnić te pliki. Na przykład ustaw **właściwość Path,** aby uwzględnić identyfikator zadania, znak ogranicznika (zazwyczaj ukośnik do przodu) i nazwę pliku:

`path: taskId + @"/output.txt"`

Bezwzględne identyfikatory URI do plików wyjściowych dla zestawu zadań będą podobne do:

```
https://myaccount.blob.core.windows.net/mycontainer/task1/output.txt
https://myaccount.blob.core.windows.net/mycontainer/task2/output.txt
```

Aby uzyskać więcej informacji na temat katalogów wirtualnych w usłudze Azure Storage, zobacz [Lista obiektów blob w kontenerze](../storage/blobs/storage-quickstart-blobs-dotnet.md#list-the-blobs-in-a-container).

## <a name="diagnose-file-upload-errors"></a>Diagnozowanie błędów przekazywania plików

Jeśli przekazywanie plików wyjściowych do usługi Azure Storage nie powiedzie się, zadanie zostanie przesunięcia do stanu **Ukończone** i [TaskExecutionInformation.FailureInformation](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.taskexecutioninformation.failureinformation#Microsoft_Azure_Batch_TaskExecutionInformation_FailureInformation) właściwość jest ustawiona. Sprawdź **FailureInformation właściwość,** aby ustalić, jaki błąd wystąpił. Na przykład w tym miejscu występuje błąd, który występuje podczas przekazywania plików, jeśli nie można odnaleźć kontenera:

```
Category: UserError
Code: FileUploadContainerNotFound
Message: One of the specified Azure container(s) was not found while attempting to upload an output file
```

Przy każdym przekazywaniu pliku partia zapisuje dwa pliki `fileuploadout.txt` `fileuploaderr.txt`dziennika do węzła obliczeniowego i . Można sprawdzić te pliki dziennika, aby dowiedzieć się więcej o określonej awarii. W przypadkach, gdy nigdy nie podjęto próby przekazania pliku, na przykład dlatego, że samo zadanie nie może działać, te pliki dziennika nie będą istnieć.

## <a name="diagnose-file-upload-performance"></a>Diagnozowanie wydajności przekazywania plików

Postęp `fileuploadout.txt` przekazywania dzienników plików. Możesz sprawdzić ten plik, aby dowiedzieć się więcej o tym, jak długo trwa przesyłanie plików. Należy pamiętać, że istnieje wiele czynników przyczyniających się do przekazywania wydajności, w tym rozmiar węzła, inne działanie w węźle w momencie przekazywania, czy kontener docelowy znajduje się w tym samym regionie co pula usługi Batch, ile węzłów jest przekazywania do przechowywania w tym samym czasie i tak dalej.

## <a name="use-the-batch-service-api-with-the-batch-file-conventions-standard"></a>Używanie interfejsu API usługi wsadowej ze standardem Konwencje plików wsadowych

Po utrwaleniu danych wyjściowych zadania z interfejsem API usługi batch, można nazwać kontenera docelowego i obiektów blob, jak chcesz. Można również nazwać je zgodnie [ze standardem Konwencje plików wsadowych](https://github.com/Azure/azure-sdk-for-net/tree/psSdkJson6/src/SDKs/Batch/Support/FileConventions#conventions). Standard Konwencje plików określa nazwy kontenera docelowego i obiektu blob w usłudze Azure Storage dla danego pliku wyjściowego na podstawie nazw zadania i zadania. Jeśli używasz standardu Konwencje plików do nazewnictwa plików wyjściowych, pliki wyjściowe są dostępne do przeglądania w [witrynie Azure portal](https://portal.azure.com).

W przypadku tworzenia w języku C#można użyć metod wbudowanych w [bibliotekę Konwencje plików wsadowych dla platformy .NET](https://www.nuget.org/packages/Microsoft.Azure.Batch.Conventions.Files). Ta biblioteka tworzy poprawnie nazwane kontenery i ścieżki obiektów blob dla Ciebie. Na przykład można wywołać interfejsu API, aby uzyskać poprawną nazwę kontenera, na podstawie nazwy zadania:

```csharp
string containerName = job.OutputStorageContainerName();
```

Za pomocą metody [CloudJobExtensions.GetOutputStorageContainerUrl](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.conventions.files.cloudjobextensions.getoutputstoragecontainerurl) można użyć do zwrócenia adresu URL podpisu dostępu udostępnionego (SAS), który jest używany do zapisu w kontenerze. Następnie można przekazać ten sygnatury dostępu Współdzielonego do [OutputFileBlContainerDestination konstruktora.](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.outputfileblobcontainerdestination)

Jeśli tworzysz w języku innym niż C#, należy samodzielnie zaimplementować standard konwencje plików.

## <a name="code-sample"></a>Przykład kodu

Przykładowy projekt [PersistOutputs][github_persistoutputs] jest jednym z [przykładowych przykładów kodu usługi Azure Batch][github_samples] w usłudze GitHub. To rozwiązanie programu Visual Studio pokazuje, jak używać biblioteki klienta usługi Batch dla platformy .NET do utrwalania danych wyjściowych zadań do trwałego magazynu. Aby uruchomić próbkę, wykonaj następujące kroki:

1. Otwórz projekt w **programie Visual Studio 2019**.
2. Dodaj **poświadczenia konta** usługi Batch i Storage do **accountSettings.settings** w projekcie Microsoft.Azure.Batch.Samples.Common.
3. **Kompilacja** (ale nie uruchamiaj) rozwiązania. Przywróć wszystkie pakiety NuGet, jeśli zostanie wyświetlony monit.
4. Użyj witryny Azure portal, aby przekazać [pakiet aplikacji](batch-application-packages.md) dla **PersistOutputsTask**. Dołącz `PersistOutputsTask.exe` i jego zestawy zależne w pakiecie .zip, ustaw identyfikator aplikacji na "PersistOutputsTask", a wersję pakietu aplikacji na "1.0".
5. **Uruchom** (uruchom) **persistoutputs** projektu.
6. Po wyświetleniu monitu o wybranie technologii trwałości do uruchomienia próbki wprowadź **2,** aby uruchomić próbkę przy użyciu interfejsu API usługi batch do utrwalania danych wyjściowych zadań.
7. W razie potrzeby uruchom próbkę ponownie, wprowadzając **3,** aby utrwalić dane wyjściowe za pomocą interfejsu API usługi Batch, a także nazwać docelową ścieżkę kontenera i obiektu blob zgodnie ze standardem Konwencje plików.

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać więcej informacji na temat utrwalania danych wyjściowych zadań za pomocą biblioteki Konwencje plików dla platformy .NET, zobacz [Utrwalanie danych zadania i zadania w usłudze Azure Storage za pomocą biblioteki Konwencje plików wsadowych dla platformy .NET](batch-task-output-file-conventions.md).
- Aby uzyskać informacje na temat innych metod utrwalania danych wyjściowych w usłudze Azure Batch, zobacz [Utrwalanie zadania i danych wyjściowych zadań do usługi Azure Storage.](batch-task-output.md)

[github_persistoutputs]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/PersistOutputs
[github_samples]: https://github.com/Azure/azure-batch-samples
