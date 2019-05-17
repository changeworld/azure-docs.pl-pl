---
title: Utrwalanie zadań i zadań danych wyjściowych do usługi Azure Storage z usługą Batch interfejs API — Azure Batch | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utrwalanie partii zadań i zadań danych wyjściowych do usługi Azure Storage za pomocą interfejsu API usługi Batch.
services: batch
author: laurenhughes
manager: jeconnoc
editor: ''
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 03/05/2019
ms.author: lahugh
ms.custom: seodec18
ms.openlocfilehash: 982fe5cea633d9fd1bbbe7dc862b69d89f5f1c1c
ms.sourcegitcommit: 1fbc75b822d7fe8d766329f443506b830e101a5e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/14/2019
ms.locfileid: "65595279"
---
# <a name="persist-task-data-to-azure-storage-with-the-batch-service-api"></a>Utrwalanie danych zadań do usługi Azure Storage za pomocą interfejsu API usługi Batch

[!INCLUDE [batch-task-output-include](../../includes/batch-task-output-include.md)]

Interfejs API usługi Batch obsługuje utrwalanie danych wyjściowych do usługi Azure Storage dla zadań i Menedżera zadań uruchamianych w pulach w konfiguracji maszyny wirtualnej. Po dodaniu zadania, można wskazać kontener w usłudze Azure Storage jako lokalizacja docelowa dla danych wyjściowych zadania podrzędnego. Po zakończeniu zadania usługi Batch następnie zapisuje wszelkie dane wyjściowe do tego kontenera.

Zaletą korzystania z interfejsu API usługi Batch można utrwalić dane wyjściowe zadania jest, że nie ma potrzeby modyfikowania aplikacji, w którym zadanie jest uruchomione. Zamiast tego za pomocą kilku zmian do aplikacji klienta, umożliwia utrwalanie danych wyjściowych zadania podrzędnego z w ramach tego samego kodu, który tworzy zadanie.

## <a name="when-do-i-use-the-batch-service-api-to-persist-task-output"></a>Kiedy używać interfejsu API usługi Batch można utrwalić dane wyjściowe zadania?

Usługa Azure Batch udostępnia więcej niż jeden sposób, aby zachować dane wyjściowe zadania. Korzystanie z interfejsu API usługi Batch jest wygodne podejście, które najlepiej nadaje się do tych scenariuszy:

- Chcesz napisać kod, aby utrwalić dane wyjściowe zadania z poziomu aplikacji klienta, bez modyfikowania aplikacji, w którym działa zadanie.
- Chcesz zachować dane wyjściowe z zadania usługi Batch i zadanie Menedżer zadania, w przypadku pul utworzonych za pomocą konfiguracji maszyny wirtualnej.
- Chcesz zachować dane wyjściowe do kontenera usługi Azure Storage przy użyciu dowolnej nazwie.
- Aby zachować dane wyjściowe do kontenera usługi Azure Storage, o nazwie zgodnie z opisem w [standardowe konwencje pliku wsadowego](https://github.com/Azure/azure-sdk-for-net/tree/psSdkJson6/src/SDKs/Batch/Support/FileConventions#conventions). 

Jeśli scenariusz różni się od przedstawionych powyżej, może być konieczne należy wziąć pod uwagę innego podejścia. Na przykład interfejs API usługi Batch obecnie nie obsługuje przesyłania strumieniowego danych wyjściowych do usługi Azure Storage, podczas gdy zadanie jest uruchomione. Do strumienia wyjściowego, należy wziąć pod uwagę za pomocą biblioteki Konwencji plików usługi Batch, dostępna dla platformy .NET. W przypadku języków należy zaimplementować własnego rozwiązania. Aby uzyskać więcej informacji na temat innych opcji utrwalanie danych wyjściowych zadania, zobacz [utrwalanie danych wyjściowych zadań i zadań w usłudze Azure Storage](batch-task-output.md).

## <a name="create-a-container-in-azure-storage"></a>Tworzenie kontenera w usłudze Azure Storage

Aby zachować dane wyjściowe zadania do usługi Azure Storage, należy utworzyć kontener, który służy jako lokalizację docelową dla plików danych wyjściowych. Kontener należy utworzyć przed uruchomieniem zadania, najlepiej przed przesłaniem zadania. Aby utworzyć kontener, użyj odpowiedniej biblioteki klienta usługi Azure Storage lub zestawu SDK. Aby uzyskać więcej informacji na temat interfejsów API usługi Azure Storage, zobacz [dokumentację usługi Azure Storage](https://docs.microsoft.com/azure/storage/).

Na przykład jeśli piszesz aplikację w języku C#, użyj [biblioteki klienta usługi Azure Storage dla platformy .NET](https://www.nuget.org/packages/WindowsAzure.Storage/). Poniższy przykład pokazuje, jak utworzyć kontener:

```csharp
CloudBlobContainer container = storageAccount.CreateCloudBlobClient().GetContainerReference(containerName);
await container.CreateIfNotExists();
```

## <a name="get-a-shared-access-signature-for-the-container"></a>Uzyskiwanie sygnatury dostępu współdzielonego dla kontenera

Po utworzeniu kontenera, pobrać sygnatury dostępu współdzielonego (SAS) z uprawnieniami do zapisu do kontenera. Sygnatury dostępu Współdzielonego zapewnia delegowany dostęp do kontenera. Sygnatura dostępu Współdzielonego udziela dostępu z określonym zestawem uprawnień i w określonym przedziale czasu. Usługa Batch wymaga sygnatury dostępu Współdzielonego z uprawnieniami do zapisu do zapisywania danych wyjściowych zadań podrzędnych do kontenera. Aby uzyskać więcej informacji na temat sygnatury dostępu Współdzielonego, zobacz [używanie sygnatury dostępu współdzielonego \(sygnatury dostępu Współdzielonego\) w usłudze Azure Storage](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

Po otrzymaniu sygnatury dostępu Współdzielonego przy użyciu interfejsów API usługi Azure Storage, interfejs API zwraca ciąg tokenu sygnatury dostępu Współdzielonego. Ten ciąg tokenu obejmuje wszystkie parametry SAS, w tym uprawnienia i interwał, przez który sygnatura dostępu Współdzielonego jest prawidłowy. Aby korzystać z sygnatury dostępu Współdzielonego, dostępu do kontenera w usłudze Azure Storage, należy dołączyć ciąg tokenu sygnatury dostępu Współdzielonego do identyfikator URI zasobu. Identyfikator URI, zasobu wraz z dołączonym tokenu sygnatury dostępu Współdzielonego zapewnia uwierzytelniony dostęp do usługi Azure Storage.

Poniższy przykład pokazuje, jak uzyskać ciąg tokenu sygnatury dostępu Współdzielonego tylko do zapisu w kontenerze, a następnie dołącza sygnatury dostępu Współdzielonego kontenera identyfikatora URI:

```csharp
string containerSasToken = container.GetSharedAccessSignature(new SharedAccessBlobPolicy()
{
    SharedAccessExpiryTime = DateTimeOffset.UtcNow.AddDays(1),
    Permissions = SharedAccessBlobPermissions.Write
});

string containerSasUrl = container.Uri.AbsoluteUri + containerSasToken;
```

## <a name="specify-output-files-for-task-output"></a>Określ pliki wyjściowe dla danych wyjściowych zadania

Aby określić pliki wyjściowe dla zadania, należy utworzyć kolekcję [Plik_wyjściowy](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.outputfile) obiektów i przypisz ją do [CloudTask.OutputFiles](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudtask.outputfiles#Microsoft_Azure_Batch_CloudTask_OutputFiles) właściwości po utworzeniu zadania.

Następujące C# przykładowy kod tworzy zadanie, które zapisuje liczb losowych do pliku o nazwie `output.txt`. W przykładzie jest tworzony plik wyjściowy dla `output.txt` są zapisywane w kontenerze. W przykładzie jest tworzony również pliki wyjściowe dla wszystkich plików dziennika, pasujących do wzorca plik `std*.txt` (_np._, `stdout.txt` i `stderr.txt`). Adres URL kontenera wymaga sygnatury dostępu Współdzielonego, który został utworzony wcześniej w kontenerze. Usługa Batch używa sygnatury dostępu Współdzielonego w celu uwierzytelnienia dostępu do kontenera:

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

Kiedy określasz plik wyjściowy, możesz użyć [OutputFile.FilePattern](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.outputfile.filepattern#Microsoft_Azure_Batch_OutputFile_FilePattern) właściwości w celu określenia wzorzec pliku do dopasowania. Wzorzec pliku może odpowiadać, zerowego pliki, pojedynczy plik lub zestaw plików, które są tworzone przez zadanie.

**FilePattern** właściwość obsługuje standardowy system plików symboli wieloznacznych `*` (dla wersji niecyklicznego odpowiada) i `**` (dla wersji odpowiada cyklicznego). Na przykład na powyższym przykładowym kodzie jest Określa wzorzec pliku do dopasowania `std*.txt` niecyklicznie:

`filePattern: @"..\std*.txt"`

Aby przekazać jeden plik, określ wzorzec pliku z bez symboli wieloznacznych. Na przykład na powyższym przykładowym kodzie jest Określa wzorzec pliku do dopasowania `output.txt`:

`filePattern: @"output.txt"`

### <a name="specify-an-upload-condition"></a>Określ warunek przekazywania

[OutputFileUploadOptions.UploadCondition](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.outputfileuploadoptions.uploadcondition#Microsoft_Azure_Batch_OutputFileUploadOptions_UploadCondition) właściwości zezwala warunkowego przekazywania plików wyjściowych. Jest to typowy scenariusz można przekazać jeden zestaw plików, jeśli zadanie zakończy się powodzeniem, a inny zbiór plików, jeśli zakończy się niepowodzeniem. Na przykład można przekazać plików pełnego dziennika tylko wtedy, gdy zadanie nie powiedzie się i kończy pracę z kodem zakończenia różny od zera. Podobnie można przekazać pliki wyników, tylko wtedy, gdy zadanie zakończy się powodzeniem, ponieważ te pliki mogą być brakujące lub niekompletne, w razie niepowodzenia zadania.

Przykładowy kod powyżej zestawy **UploadCondition** właściwości **TaskCompletion**. To ustawienie określa, że plik można przekazać po zakończeniu zadania, niezależnie od wartości Kod zakończenia.

`uploadCondition: OutputFileUploadCondition.TaskCompletion`

Aby uzyskać inne ustawienia, zobacz [OutputFileUploadCondition](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.common.outputfileuploadcondition) wyliczenia.

### <a name="disambiguate-files-with-the-same-name"></a>Odróżnienia plików o takiej samej nazwie

Zadania w ramach zadania może generować pliki, które mają taką samą nazwę. Na przykład `stdout.txt` i `stderr.txt` są tworzone dla każdego zadania, które jest uruchamiane w ramach zadania. Ponieważ każde zadanie podrzędne jest uruchamiany w kontekście swój własny, pliki te nie wystąpił konflikt w systemie plików węzła. Jednak podczas przekazywania plików z wielu zadań do udostępnionych kontenerów, konieczne będzie odróżnić pliki o takiej samej nazwie.

[OutputFileBlobContainerDestination.Path](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.outputfileblobcontainerdestination.path#Microsoft_Azure_Batch_OutputFileBlobContainerDestination_Path) właściwość określa docelowego obiektu blob lub katalog wirtualny dla plików wyjściowych. Możesz użyć **ścieżki** właściwości nazwy obiektu blob lub katalogu wirtualnego w taki sposób, że pliki wyjściowe o takiej samej nazwie jednoznacznie są nazywane w usłudze Azure Storage. Przy użyciu Identyfikatora zadania w ścieżce jest dobrym sposobem zapewnienia unikatowe nazwy i łatwo zidentyfikować pliki.

Jeśli **FilePattern** właściwość jest ustawiona na wyrażenie z symbolem wieloznacznym, a następnie wszystkie pliki, które pasuje do wzorca są przekazywane do katalogu wirtualnego, określony przez **ścieżki** właściwości. Na przykład, jeśli kontener ma `mycontainer`, zadanie jest identyfikator `mytask`, i jest wzorzec pliku `..\std*.txt`, bezwzględne identyfikatory URI w celu plików wyjściowych w usłudze Azure Storage będą podobne do:

```
https://myaccount.blob.core.windows.net/mycontainer/mytask/stderr.txt
https://myaccount.blob.core.windows.net/mycontainer/mytask/stdout.txt
```

Jeśli **FilePattern** właściwość jest ustawiona, aby dopasować nazwę pojedynczego pliku, co oznacza, nie zawiera żadnych symboli wieloznacznych, a następnie wartość **ścieżki** właściwość określa nazwę FQDN obiektu blob. Jeśli przewidujesz konflikty nazw z pojedynczy plik z wielu zadań, następnie uwzględnić nazwę katalogu wirtualnego, jako część nazwy pliku, aby odróżnić tych plików. Na przykład ustawić **ścieżki** właściwości do uwzględnienia, identyfikator zadania, znak ograniczający (zazwyczaj ukośnikiem) i nazwa pliku:

`path: taskId + @"/output.txt"`

Bezwzględna identyfikatorów URI do plików wyjściowych dla zestawu zadań będzie wyglądać podobnie do:

```
https://myaccount.blob.core.windows.net/mycontainer/task1/output.txt
https://myaccount.blob.core.windows.net/mycontainer/task2/output.txt
```

Aby uzyskać więcej informacji na temat katalogów wirtualnych w usłudze Azure Storage, zobacz [wyświetlanie listy obiektów blob w kontenerze](../storage/blobs/storage-quickstart-blobs-dotnet.md#list-the-blobs-in-a-container).

## <a name="diagnose-file-upload-errors"></a>Diagnozuj błędy przekazywania plików

Jeśli przekazywanie plików wyjściowych do usługi Azure Storage kończy się niepowodzeniem, a następnie zadanie przechodzi do **Ukończono** stanu i [TaskExecutionInformation.FailureInformation](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.taskexecutioninformation.failureinformation#Microsoft_Azure_Batch_TaskExecutionInformation_FailureInformation) właściwość jest ustawiona. Sprawdź **FailureInformation** właściwości w celu określenia, jakie błędu. Na przykład w tym miejscu jest błąd występujący podczas przekazywania pliku, jeśli nie można odnaleźć kontenera:

```
Category: UserError
Code: FileUploadContainerNotFound
Message: One of the specified Azure container(s) was not found while attempting to upload an output file
```

Na każdym przekazywanie pliku wsadowego zapisuje dwa pliki dzienników do węzła obliczeniowego `fileuploadout.txt` i `fileuploaderr.txt`. Można sprawdzić te pliki dziennika, aby dowiedzieć się więcej na temat określonego błędu. W przypadkach, w którym przekazanie pliku nigdy nie podjęto, na przykład ponieważ samo zadanie nie można uruchomić następnie te pliki dziennika nie istnieje.

## <a name="diagnose-file-upload-performance"></a>Diagnozowanie wydajności przekazywania plików

`fileuploadout.txt` Plików dzienników postęp przekazywania. Można sprawdzić ten plik, aby dowiedzieć się więcej, o tym, ile Trwa przekazywanie plików. Należy pamiętać, że istnieje wiele czynników mającym swój wkład do przekazania wydajności, takich jak rozmiar węzła, inne działania w węźle podczas przekazywania, czy kontener docelowy znajduje się w tym samym regionie, co puli usługi Batch, ilu węzłów przekazywane są do stora GE konto na tym samym czasie i tak dalej.

## <a name="use-the-batch-service-api-with-the-batch-file-conventions-standard"></a>Interfejs API usługi Batch za pomocą standardowej konwencji plików usługi Batch

Gdy będzie się powtarzać dane wyjściowe zadania przy użyciu interfejsu API usługi Batch, można nadać nazwę kontenera docelowego i obiektów blob w dowolny sposób. Możesz również nazwij je zgodnie z opisem w [standardowe konwencje pliku wsadowego](https://github.com/Azure/azure-sdk-for-net/tree/psSdkJson6/src/SDKs/Batch/Support/FileConventions#conventions). Standardowe konwencje plików określa nazwy kontenera docelowego i obiektów blob w usłudze Azure Storage dla pliku wyjściowego danego na podstawie nazw zadanie i zadania. Jeśli należy używać standardowy plik konwencje nazewnictwa plików wyjściowych, a następnie swoje pliki wyjściowe są dostępne pod kątem wyświetlania w [witryny Azure portal](https://portal.azure.com).

Jeśli tworzysz w języku C#, można użyć metod wbudowanych [biblioteki Konwencji plików usługi Batch dla platformy .NET](https://www.nuget.org/packages/Microsoft.Azure.Batch.Conventions.Files). Ta biblioteka tworzy poprawnie nazwanych kontenerów i ścieżki obiektu blob. Na przykład można wywołać interfejs API, aby pobrać poprawną nazwę kontenera, na podstawie nazwy zadania:

```csharp
string containerName = job.OutputStorageContainerName();
```

Możesz użyć [CloudJobExtensions.GetOutputStorageContainerUrl](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.conventions.files.cloudjobextensions.getoutputstoragecontainerurl) metodę, aby zwrócić adres URL sygnatury (SAS) dostępu współdzielonego, który jest używany do zapisu do kontenera. Możesz następnie przekazać ten sygnatury dostępu Współdzielonego do [OutputFileBlobContainerDestination](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.outputfileblobcontainerdestination) konstruktora.

Jeśli tworzysz w języku innym niż C#, należy zaimplementować standardowej konwencji plików samodzielnie.

## <a name="code-sample"></a>Przykład kodu

[PersistOutputs] [ github_persistoutputs] przykładowy projekt jest jednym z [przykładów kodu usługi Azure Batch] [ github_samples] w witrynie GitHub. To rozwiązanie programu Visual Studio pokazuje, jak zachować dane wyjściowe zadania do trwałego magazynu za pomocą biblioteki klienta usługi Batch dla platformy .NET. Do uruchomienia przykładu, wykonaj następujące kroki:

1. Otwórz projekt w **Visual Studio 2019**.
2. Dodawanie usługi Batch i Storage **poświadczenia konta** do **AccountSettings.settings** w projekcie Microsoft.Azure.Batch.Samples.Common.
3. **Tworzenie** (ale nie należy uruchamiać) to rozwiązanie. Jeśli zostanie wyświetlony monit, należy przywrócić wszystkie pakiety NuGet.
4. Użyj witryny Azure portal, aby przekazać [pakiet aplikacji](batch-application-packages.md) dla **PersistOutputsTask**. Obejmują `PersistOutputsTask.exe` i ustaw jego zestawów zależnych w pakiecie .zip, identyfikator aplikacji, aby "PersistOutputsTask" i "1.0" wersja pakietu aplikacji.
5. **Rozpocznij** (Uruchom) **PersistOutputs** projektu.
6. Po wyświetleniu monitu wybierz technologię stanów trwałych, można użyć do uruchamiania przykładu, należy wprowadzić **2** do uruchomienia przykładu, aby zachować dane wyjściowe zadania przy użyciu interfejsu API usługi Batch.
7. Jeśli to konieczne, ponownie uruchom przykład, wprowadzając **3** utrwalanie danych wyjściowych za pomocą interfejsu API usługi Batch, a także nazwy kontenera i obiektów blob ścieżce docelowej zgodnie ze standardem Konwencji plików.

## <a name="next-steps"></a>Kolejne kroki

- Aby uzyskać więcej informacji na temat utrwalanie danych wyjściowych zadania za pomocą biblioteki Konwencji plików dla platformy .NET, zobacz [utrwalanie danych i zadań do usługi Azure Storage za pomocą biblioteki Konwencji plików usługi Batch dla platformy .NET](batch-task-output-file-conventions.md).
- Aby uzyskać informacji na temat innych metod utrwalanie danych wyjściowych w usłudze Azure Batch, zobacz [utrwalanie danych wyjściowych zadań i zadań w usłudze Azure Storage](batch-task-output.md).

[github_persistoutputs]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/PersistOutputs
[github_samples]: https://github.com/Azure/azure-batch-samples
