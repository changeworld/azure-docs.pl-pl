---
title: Utrwalanie i zadań dane wyjściowe do magazynu Azure przy użyciu interfejsu API usługi partia zadań Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utrwalić partii zadań i zadania dane wyjściowe do usługi Azure Storage za pomocą interfejsu API usługi partii.
services: batch
author: dlepow
manager: jeconnoc
editor: ''
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 06/16/2017
ms.author: danlep
ms.openlocfilehash: ee8622525adcc698bf920b0c3379cc3065798a19
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2018
ms.locfileid: "30315596"
---
# <a name="persist-task-data-to-azure-storage-with-the-batch-service-api"></a>Utrwalenia danych zadań do magazynu Azure przy użyciu interfejsu API usługi partii

[!INCLUDE [batch-task-output-include](../../includes/batch-task-output-include.md)]

Począwszy od wersji 2017-05-01, interfejsu API usługi partii obsługuje trwałych danych wyjściowych do magazynu Azure do wykonywania zadań i zadania Menedżer zadania, które będą uruchamiane w pulach w konfiguracji maszyny wirtualnej. Podczas dodawania zadania jako miejsca docelowego dla danych wyjściowych zadania można określić kontener w usłudze Azure Storage. Po zakończeniu zadania usługa partia zadań następnie zapisuje wszelkie dane wyjściowe do tego kontenera.

Zaletą używania interfejsu API usługi partii do utrwalenia danych wyjściowych zadania jest, że nie ma potrzeby modyfikowania aplikacji, która działa zadanie. Zamiast tego z kilka prostych zmian do aplikacji klienta, można ją utrwalić dane wyjściowe zadania z kodem, który tworzy zadanie.   

## <a name="when-do-i-use-the-batch-service-api-to-persist-task-output"></a>Kiedy używać interfejsu API usługi partii do utrwalenia danych wyjściowych zadania?

Partia zadań Azure zawiera więcej niż jeden sposób, aby zachować dane wyjściowe zadania. Przy użyciu interfejsu API usługi partii jest to wygodny podejście, które najlepiej nadaje się do tych scenariuszy:

- Należy napisać kod, aby zachować dane wyjściowe zadania z Twojej aplikacji klienckiej bez modyfikowania aplikacji, która działa zadania.
- Chcesz zachować dane wyjściowe z zadania wsadowego i zadania Menedżer zadania w pulach utworzone za pomocą konfiguracji maszyny wirtualnej.
- Chcesz zachować dane wyjściowe do kontenera magazynu Azure za pomocą dowolnego nazwy.
- Aby zachować dane wyjściowe do kontenera usługi Magazyn Azure o nazwie zgodnie z [standard konwencje pliku wsadowego](https://github.com/Azure/azure-sdk-for-net/tree/vs17Dev/src/SDKs/Batch/Support/FileConventions#conventions). 

Jeśli scenariusz różni się od wymienione powyżej, może być konieczne należy wziąć pod uwagę różne podejścia. Na przykład usługi interfejsu API partii nie obsługuje obecnie przesyłania strumieniowego dane wyjściowe do magazynu Azure podczas wykonywania zadania. Do strumienia wyjściowego, należy wziąć pod uwagę przy użyciu biblioteki konwencje plików wsadowych, dostępna dla platformy .NET. Dla innych języków musisz zaimplementować własne rozwiązania. Aby uzyskać więcej informacji dotyczących innych opcji trwałych danych wyjściowych zadania, zobacz [utrwalić danych wyjściowych i zadań do magazynu Azure](batch-task-output.md). 

## <a name="create-a-container-in-azure-storage"></a>Tworzenie kontenera w usłudze Azure Storage

Aby zachować dane wyjściowe zadania do magazynu Azure, należy utworzyć kontener, który służy jako lokalizację docelową dla plików wyjściowych. Kontener należy utworzyć przed uruchomieniem zadania, najlepiej przed przesłać zadanie. Aby utworzyć kontener, użyj odpowiedniej biblioteki klienta magazynu Azure lub zestawu SDK. Aby uzyskać więcej informacji na temat interfejsy API usługi Azure Storage, zobacz [dokumentację usługi Azure Storage](https://docs.microsoft.com/azure/storage/).

Na przykład użyć podczas pisania aplikacji w języku C#, [biblioteki klienta usługi Azure Storage dla platformy .NET](https://www.nuget.org/packages/WindowsAzure.Storage/). Poniższy przykład przedstawia sposób tworzenia kontenera:

```csharp
CloudBlobContainer container = storageAccount.CreateCloudBlobClient().GetContainerReference(containerName);
await conainer.CreateIfNotExists();
```

## <a name="get-a-shared-access-signature-for-the-container"></a>Uzyskiwanie sygnatury dostępu współdzielonego dla kontenera

Po utworzeniu kontenera, Pobierz sygnatury dostępu współdzielonego (SAS) z prawem do zapisu do kontenera. Sygnatury dostępu Współdzielonego umożliwiają dostęp delegowany do kontenera. Sygnatury dostępu Współdzielonego udziela dostępu z określonego zestawu uprawnień i w określonym przedziale czasu. Usługa partia zadań musi sygnatury dostępu Współdzielonego z uprawnieniami do zapisu do zapisywania danych wyjściowych zadania do kontenera. Aby uzyskać więcej informacji na temat sygnatury dostępu Współdzielonego, zobacz [używanie sygnatury dostępu współdzielonego \(SAS\) w usłudze Azure Storage](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

Po wyświetleniu okna sygnatury dostępu Współdzielonego przy użyciu interfejsów API usługi magazynu Azure, interfejsu API zwraca ciąg tokenu sygnatury dostępu Współdzielonego. Ten ciąg tokenu zawiera wszystkie parametry SAS, w tym uprawnienia i interwał, w którym sygnatury dostępu Współdzielonego jest prawidłowy. Aby uzyskać dostępu do kontenera w magazynie Azure przy użyciu sygnatury dostępu Współdzielonego, należy dołączyć ciąg tokenu sygnatury dostępu Współdzielonego do identyfikatora URI zasobu. Identyfikator URI, zasobu wraz z dołączonym tokenu sygnatury dostępu Współdzielonego, zapewnia uwierzytelniony dostęp do magazynu Azure.

Poniższy przykład pokazuje, jak pobrać ciąg tokenu sygnatury dostępu Współdzielonego tylko do zapisu dla kontenera, a następnie dołącza do kontenera identyfikatora URI sygnatury dostępu Współdzielonego:

```csharp
string containerSasToken = container.GetSharedAccessSignature(new SharedAccessBlobPolicy()
{
    SharedAccessExpiryTime = DateTimeOffset.UtcNow.AddDays(1),
    Permissions = SharedAccessBlobPermissions.Write
});

string containerSasUrl = container.Uri.AbsoluteUri + containerSasToken; 
```

## <a name="specify-output-files-for-task-output"></a>Określ plików wyjściowych dla danych wyjściowych zadania

Aby określić plików wyjściowych dla zadania, należy utworzyć kolekcję [OutputFile](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.outputfile) obiekty i przypisz go do [CloudTask.OutputFiles](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudtask.outputfiles#Microsoft_Azure_Batch_CloudTask_OutputFiles) właściwości po utworzeniu zadania. 

Poniższy przykład kodu .NET tworzy zadanie, które zapisuje liczb losowych w pliku o nazwie `output.txt`. W przykładzie jest tworzony w pliku wyjściowym `output.txt` do zapisania do kontenera. W przykładzie jest tworzony także plików wyjściowych dla wszystkie pliki dziennika, które jest zgodny z wzorcem pliku `std*.txt` (_np._, `stdout.txt` i `stderr.txt`). Adresu URL kontenera wymaga sygnatury dostępu Współdzielonego, który został utworzony wcześniej dla kontenera. Usługa partia zadań używa sygnatury dostępu Współdzielonego w celu uwierzytelniania dostępu do kontenera: 

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

### <a name="specify-a-file-pattern-for-matching"></a>Określ wzorzec pliku do dopasowania

Po określeniu pliku wyjściowego, można użyć [OutputFile.FilePattern](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.outputfile.filepattern#Microsoft_Azure_Batch_OutputFile_FilePattern) właściwości w celu określenia wzorzec pliku do dopasowania. Wzorzec pliku zgadzają zero pliki, pojedynczego pliku lub zestawu plików, które są tworzone przez zadania.

**FilePattern** właściwość obsługuje standardowe plików symboli wieloznacznych `*` (dla innych niż cykliczne zgodna) i `**` (dla cyklicznego zgodna). Na przykład powyżej przykładowy kod określa wzorzec pliku do dopasowania `std*.txt` lokalizacji: 

`filePattern: @"..\std*.txt"`

Aby przekazać jeden plik, określ wzorzec pliku bez symboli wieloznacznych. Na przykład powyżej przykładowy kod określa wzorzec pliku do dopasowania `output.txt`:

`filePattern: @"output.txt"`

### <a name="specify-an-upload-condition"></a>Określ warunek przekazywania

[OutputFileUploadOptions.UploadCondition](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.outputfileuploadoptions.uploadcondition#Microsoft_Azure_Batch_OutputFileUploadOptions_UploadCondition) właściwości zezwala na przekazywanie warunkowego plików wyjściowych. Typowy scenariusz obejmuje można przekazać jeden zestaw plików w przypadku pomyślnego zakończenia zadania, a inny zestaw plików, jeśli działanie nie powiodło się. Na przykład możesz przekazać plików pełnego dziennika tylko wtedy, gdy zadanie nie powiedzie się i kończy działanie z kodem zakończenia różną od zera. Podobnie można przekazać plików wyników, tylko w przypadku pomyślnego zakończenia zadania, ponieważ te pliki mogą być brakujące lub niekompletne, jeśli zadanie nie powiedzie się.

Przykładowy kod powyżej zestawy **UploadCondition** właściwości **TaskCompletion**. To ustawienie określa, że plik ma zostać przekazana po ukończeniu zadania, niezależnie od wartości Kod wyjścia. 

`uploadCondition: OutputFileUploadCondition.TaskCompletion`

W przypadku innych ustawień, zobacz [OutputFileUploadCondition](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.common.outputfileuploadcondition) wyliczenia.

### <a name="disambiguate-files-with-the-same-name"></a>Odróżniania plików o takiej samej nazwie

Zadania w ramach zadania może tworzyć pliki, które mają taką samą nazwę. Na przykład `stdout.txt` i `stderr.txt` są tworzone dla każdego zadania w ramach zadania. Ponieważ każde zadanie jest uruchamiane w kontekście jego własnej, pliki te nie powodują konflikt w systemie plików węzła. Jednak podczas przekazywania plików z wielu zadań do udostępnionego kontenera należy do odróżniania plików o takiej samej nazwie.

[OutputFileBlobContainerDestination.Path](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.outputfileblobcontainerdestination.path#Microsoft_Azure_Batch_OutputFileBlobContainerDestination_Path) właściwość określa docelowego obiektu blob lub katalog wirtualny dla plików wyjściowych. Można użyć **ścieżki** właściwość nazwy obiektów blob lub katalogu wirtualnego w taki sposób, że pliki wyjściowe o takiej samej nazwie jednoznacznie noszą nazwy w usłudze Azure Storage. Za pomocą Identyfikatora zadania w ścieżce jest dobrym sposobem zapewnienia unikatowe nazwy i łatwo zidentyfikować plików.

Jeśli **FilePattern** właściwość jest ustawiona na wyrażenie z symbolem wieloznacznym, a następnie wszystkie pliki, które jest zgodna z wzorcem są przekazywane do katalogu wirtualnego, określony przez **ścieżki** właściwości. Na przykład, jeśli kontener jest `mycontainer`, identyfikator zadania `mytask`, i jest wzorzec pliku `..\std*.txt`, bezwzględny identyfikator URI do plików wyjściowych w usłudze Azure Storage będzie podobny do:

```
https://myaccount.blob.core.windows.net/mycontainer/mytask/stderr.txt
https://myaccount.blob.core.windows.net/mycontainer/mytask/stdout.txt
```

Jeśli **FilePattern** właściwości ustawiono być zgodna z nazwą jednego pliku, co oznacza nie zawiera żadnych symboli wieloznacznych, a następnie wartość **ścieżki** właściwość określa nazwę FQDN obiektu blob. Jeśli przewidujesz konflikty nazw z jednego pliku z wielu zadań, następnie wprowadzić nazwę katalogu wirtualnego jako część nazwy pliku do odróżniania tych plików. Na przykład ustawić **ścieżki** właściwość Identyfikatora zadania, znak ogranicznika (zazwyczaj ukośnikiem) i nazwa pliku:

`path: taskId + @"/output.txt"`

Bezwzględna identyfikatorów URI do plików wyjściowych dla zestawu zadań będą wyglądać podobnie do:

```
https://myaccount.blob.core.windows.net/mycontainer/task1/output.txt
https://myaccount.blob.core.windows.net/mycontainer/task2/output.txt
```

Aby uzyskać więcej informacji na temat katalogów wirtualnych w usłudze Azure Storage, zobacz [listę obiektów blob w kontenerze](../storage/blobs/storage-quickstart-blobs-dotnet.md#list-the-blobs-in-a-container).


## <a name="diagnose-file-upload-errors"></a>Diagnozowanie błędów przekazywania plików

Przekazywanie plików wyjściowych do magazynu Azure nie powiedzie się, a następnie przenosi zadania **Ukończono** stanu i [TaskExecutionInformation.FailureInformation](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.taskexecutioninformation.failureinformation#Microsoft_Azure_Batch_TaskExecutionInformation_FailureInformation) właściwość jest ustawiona. Sprawdź **FailureInformation** właściwości w celu określenia, jakie błąd. Na przykład w tym miejscu jest błąd występujący podczas przekazywania pliku, jeśli nie można odnaleźć kontenera: 

```
Category: UserError
Code: FileUploadContainerNotFound
Message: One of the specified Azure container(s) was not found while attempting to upload an output file
```

Na każdym przekazywania pliku wsadowego zapisuje dwa pliki dziennika na węzeł obliczeń `fileuploadout.txt` i `fileuploaderr.txt`. Możesz przejrzeć te pliki dziennika, aby dowiedzieć się więcej na temat określonego błędu. W przypadkach, gdy przekazywania pliku nigdy nie podjęto próbę, np. ponieważ nie można uruchomić samo zadanie następnie te pliki dziennika nie istnieje.

## <a name="diagnose-file-upload-performance"></a>Diagnozowanie wydajność przekazywania pliku

`fileuploadout.txt` Plików dzienników postępu przekazywania. Możesz przejrzeć ten plik, aby dowiedzieć się więcej na temat jak długo trwa przesyłania plików. Należy pamiętać, że istnieje wiele czynników do przekazania wydajności, takich jak rozmiar węzła innych działań w węźle w czasie przekazywania, czy kontener docelowy znajduje się w tym samym regionie co puli partii przekazywany liczbę węzłów do konta magazynu w tym samym czasie i tak dalej.

## <a name="use-the-batch-service-api-with-the-batch-file-conventions-standard"></a>Usługi interfejsu API partii za pomocą standardowej konwencji pliku wsadowego

Można utrwalić danych wyjściowych zadania przy użyciu interfejsu API usługi partii, należy określić nazwę użytkownika docelowy kontener i obiektów blob, jednak chcesz. Można także je według nazwy [standard konwencje pliku wsadowego](https://github.com/Azure/azure-sdk-for-net/tree/vs17Dev/src/SDKs/Batch/Support/FileConventions#conventions). Standard konwencje plików określa nazwy kontenera docelowego i obiektów blob w usłudze Azure Storage dla pliku wyjściowego danego na podstawie nazw zadań i zadań. Jeśli używasz standardowego pliku konwencje nazewnictwa plików wyjściowych, a następnie Twoje pliki wyjściowe są dostępne do wyświetlenia w [portalu Azure](https://portal.azure.com).

Jeśli tworzysz w języku C#, możesz użyć metod wbudowanych w [biblioteki konwencje pliku wsadowego dla platformy .NET](https://www.nuget.org/packages/Microsoft.Azure.Batch.Conventions.Files). Ta biblioteka utworzy prawidłowo nazwanych kontenerów i obiektów blob ścieżki. Na przykład można wywołać interfejsu API, aby uzyskać prawidłową nazwę kontenera, na podstawie nazwy zadania:

```csharp
string containerName = job.OutputStorageContainerName();
```

Można użyć [CloudJobExtensions.GetOutputStorageContainerUrl](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.conventions.files.cloudjobextensions.getoutputstoragecontainerurl) metodę adres zwrotny URL sygnatury dostępu Współdzielonego dostępu współdzielonego, która jest używana podczas zapisu do kontenera. Następnie można przekazać tego dostępu Współdzielonego w celu [OutputFileBlobContainerDestination](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.outputfileblobcontainerdestination) konstruktora.

Jeśli tworzysz w języku innym niż C#, należy zaimplementować standardowej konwencji pliku samodzielnie.

## <a name="code-sample"></a>Przykład kodu

[PersistOutputs] [ github_persistoutputs] przykładowy projekt jest jednym z [przykłady kodu partii zadań Azure] [ github_samples] w witrynie GitHub. To rozwiązanie Visual Studio pokazano, jak używać biblioteki klienta usługi partia zadań dla platformy .NET do utrwalenia danych wyjściowych zadania do magazynu trwałego. Aby uruchomić przykład, wykonaj następujące kroki:

1. Otwórz projekt w **programu Visual Studio 2015 lub nowsza**.
2. Dodaj wsadowego i magazynowania **poświadczenia konta** do **AccountSettings.settings** w projekcie Microsoft.Azure.Batch.Samples.Common.
3. **Tworzenie** (ale nie jest uruchomiony) rozwiązania. Jeśli zostanie wyświetlony monit, należy przywrócić wszystkie pakiety NuGet.
4. Użyj portalu Azure, aby przekazać [pakiet aplikacji](batch-application-packages.md) dla **PersistOutputsTask**. Obejmują `PersistOutputsTask.exe` i jego zestawów zależnych pakietu ZIP, Ustaw identyfikator aplikacji na "PersistOutputsTask" i "1.0" wersja pakietu aplikacji.
5. **Uruchom** (Uruchom) **PersistOutputs** projektu.
6. Po wyświetleniu monitu wybierz technologię trwałości tak, aby użyć do uruchomienia przykładu, wprowadź **2** Aby uruchomić przykład, aby zachować dane wyjściowe zadania przy użyciu interfejsu API usługi partii.
7. W razie potrzeby można uruchomić przykładowy ponownie, wprowadzając **3** do utrwalenia danych wyjściowych z interfejsem API usługi partii, a także nazwę ścieżki docelowej i kontener obiektów blob według standardu konwencje plików.

## <a name="next-steps"></a>Kolejne kroki

- Aby uzyskać więcej informacji na trwałych danych wyjściowych zadania z biblioteką konwencje plików dla platformy .NET, zobacz [utrwalenia danych i zadań do magazynu Azure z biblioteką konwencje pliku wsadowego dla platformy .NET utrwalić ](batch-task-output-file-conventions.md).
- Aby uzyskać informacje na inne podejścia trwałych danych wyjściowych w partii zadań Azure, zobacz [utrwalić danych wyjściowych i zadań do magazynu Azure](batch-task-output.md).

[github_persistoutputs]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/PersistOutputs
[github_samples]: https://github.com/Azure/azure-batch-samples
