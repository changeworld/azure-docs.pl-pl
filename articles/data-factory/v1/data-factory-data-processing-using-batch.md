---
title: Przetwarzania dużych zestawów danych przy użyciu fabryki danych i partii | Dokumentacja firmy Microsoft
description: Zawiera opis sposobu przetwarzania dużych ilości danych w potoku fabryki danych Azure przy użyciu równoległego przetwarzania możliwości partii zadań Azure.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: 688b964b-51d0-4faa-91a7-26c7e3150868
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: 8f0cd8aad2d5c5142fc66c78393b57ff210a7b83
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2018
---
# <a name="process-large-scale-datasets-by-using-data-factory-and-batch"></a>Proces dużych zestawów danych przy użyciu fabryki danych i usługi partia zadań
> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Azure Data Factory, która jest ogólnie dostępna. Jeśli używasz wersji 2 usługi fabryka danych, która jest w wersji zapoznawczej, zobacz [działań niestandardowych z fabryki danych w wersji 2](../transform-data-using-dotnet-custom-activity.md).

W tym artykule opisano architekturę rozwiązania próbki, które przenosi i przetwarzania dużych zestawów danych w sposób automatycznego i zaplanowane. Umożliwia także wskazówki end-to-end, aby zaimplementować to rozwiązanie przy użyciu fabryki danych i partii zadań Azure.

W tym artykule jest dłuższa niż typowe artykułu, ponieważ zawiera ona wskazówki przykładowe całego rozwiązania. Jeśli masz zamiar partii i fabryki danych, informacje na temat tych usług i jak one współdziałają ze sobą. Jeśli znasz coś o usługach i są projektowanie/zaprojektowanie rozwiązania, można skupić się na [sekcji architektura](#architecture-of-sample-solution) artykułu. Jeśli projektujesz prototypu lub rozwiązania, można wypróbować instrukcje krok po kroku w [wskazówki](#implementation-of-sample-solution). Zapraszamy komentarze dotyczące tej zawartości i sposobie używania jej.

Po pierwsze Przyjrzyjmy się jak usługi fabryki danych i partii może pomóc procesu dużych zestawów danych w chmurze.     

## <a name="why-azure-batch"></a>Dlaczego usługa partia zadań Azure?
 Wsadowe umożliwia sprawne działanie aplikacji na dużą skalę równoległych i wysokiej wydajności obliczeniowej (HPC) w chmurze. Jest to usługa platformy, która planuje pracę obliczeniowych do uruchamiania na zbiór zarządzanych maszynach wirtualnych (VM). Może automatycznie skalować zasoby obliczeniowe do potrzeb zadań.

W usłudze Batch definiuje się zasoby obliczeniowe Azure do wykonywania aplikacji równolegle i na dużą skalę. Można uruchomić na żądanie lub według harmonogramu zadań. Nie trzeba ręcznie tworzenie, konfigurowanie i zarządzanie klastra HPC, poszczególnych maszyn wirtualnych, sieci wirtualne lub złożone zadania i planowanie zadań infrastruktury.

 Jeśli nie znasz z partii, następujące artykuły ułatwić zrozumienie architektury/implementacji rozwiązania opisane w tym artykule:   

* [Podstawowe informacje o partii](../../batch/batch-technical-overview.md)
* [Przegląd funkcji partii](../../batch/batch-api-basics.md)

Opcjonalnie, aby dowiedzieć się więcej na temat usługi partia zadań, zobacz [ścieżka szkoleniowa dotycząca partii](https://azure.microsoft.com/documentation/learning-paths/batch/).

## <a name="why-azure-data-factory"></a>Dlaczego warto wybrać usługę Azure Data Factory?
Fabryka danych jest usługą integracji danych w chmurze, która służy do aranżacji i automatyzacji przenoszenia i przekształcania danych. Fabryka danych służy do tworzenia potoki zarządzanych danych, które przenoszenia danych z lokalnego i w chmurze magazyny danych w magazynie danych scentralizowane. Przykładem jest magazynu obiektów Blob platformy Azure. Fabryka danych służy do procesu/Przekształcanie danych za pomocą usług, takich jak Azure HDInsight i uczenia maszynowego Azure. Można również zaplanować potoki danych do uruchamiania w zaplanowanym czasie (na przykład co godzinę, codziennie i co tydzień). Można monitorować i zarządzać nimi potoki jeden rzut oka do identyfikowania problemów i podejmij akcję.

  Jeśli nie znasz z fabryką danych, następujące artykuły ułatwić zrozumienie architektury/implementacji rozwiązania opisane w tym artykule:  

* [Wprowadzenie do fabryki danych](data-factory-introduction.md)
* [Tworzenie swój pierwszy potok danych](data-factory-build-your-first-pipeline.md)   

Opcjonalnie, aby dowiedzieć się więcej na temat fabryki danych, zobacz [ścieżka szkoleniowa dotycząca fabryki danych](https://azure.microsoft.com/documentation/learning-paths/data-factory/).

## <a name="data-factory-and-batch-together"></a>Fabryki danych i partii razem
Fabryka danych zawiera wbudowane działania. Na przykład działanie kopiowania jest używany do skopiowania/przeniesienia danych z magazynu danych źródłowych w magazynie danych docelowym. Działanie Hive jest używane do przetwarzania danych przy użyciu klastrów platformy Hadoop (HDInsight) na platformie Azure. Aby uzyskać listę obsługiwanych transformacji działania, zobacz [działań przekształcania danych](data-factory-data-transformation-activities.md).

Można również tworzyć niestandardowe działania .NET przenosić i przetwarzania danych na własną logikę. Te działania można uruchomić w klastrze usługi HDInsight lub w puli partii maszyn wirtualnych. Korzystając z partii, można skonfigurować pulę, aby stosować automatyczne skalowanie (Dodawanie lub usuwanie maszyn wirtualnych, w zależności od obciążenia) na podstawie formuły, musisz podać.     

## <a name="architecture-of-a-sample-solution"></a>Architektura przykładowe rozwiązanie
  Architektura opisane w tym artykule jest prostym rozwiązaniem. Ma również zastosowanie w złożonych scenariuszach, takich jak modelowania branży usług finansowych, przetwarzania obrazów i renderowania i genomiczne analizy ryzyka.

Na diagramie przedstawiono, jak Data Factory organizuje przenoszenia danych i przetwarzania. Pokazuje też, jak partii przetwarza dane w sposób równoległy. Pobrać i wydrukować diagram dla ułatwienia (11 x 17 cali lub rozmiaru A3). Aby uzyskać dostęp na diagramie, dzięki czemu można go wydrukować, zobacz [aranżacji HPC i danych przy użyciu partii i fabryki danych](http://go.microsoft.com/fwlink/?LinkId=717686).

[![Diagram przetwarzania danych na dużą skalę](./media/data-factory-data-processing-using-batch/image1.png)](http://go.microsoft.com/fwlink/?LinkId=717686)

Poniższa lista zawiera podstawowe kroki procesu. Rozwiązanie zawiera kod i wyjaśnienia dotyczące rozwiązania end-to-end.

* **Skonfiguruj partii z pulą węzłów obliczeniowych (VM).** Można określić liczbę węzłów i rozmiar każdego węzła.

* **Utwórz wystąpienie fabryki danych** skonfigurowany z jednostkami, które reprezentują magazynu obiektów blob, usługa partia zadań obliczeń danych wejścia/wyjścia i przepływu pracy/potoku z działaniami, które przenoszenia i przekształcanie danych.

* **Tworzenie niestandardowego działania .NET w potoku fabryki danych.** Działanie jest wykonywana w puli partii kodu użytkownika.

* **Do przechowywania dużych ilości danych wejściowych jako obiekty BLOB w usłudze Azure Storage.** Danych jest podzielona na wycinków logiczne (zazwyczaj za czas).

* **Fabryka danych kopiuje dane są przetwarzane równolegle** do lokalizacji dodatkowej.

* **Fabryka danych uruchamia to niestandardowe działanie przy użyciu puli przydzielone przez partię.** Fabryka danych można uruchomić jednocześnie działań. Każde działanie przetwarza wycinka danych. Wyniki są przechowywane w magazynie.

* **Fabryka danych przenosi wyniki końcowe do lokalizacji trzeci** dystrybucji za pośrednictwem aplikacji lub dla dalszego przetwarzania przez inne narzędzia.

## <a name="implementation-of-the-sample-solution"></a>Implementacja przykładowe rozwiązanie
Przykładowe rozwiązanie jest celowo proste. Został zaprojektowany tak, aby opisano, jak używać razem do zestawów danych proces fabryki danych i partii. Rozwiązanie zlicza liczbę wystąpień wyszukiwany termin "Microsoft" w plików wejściowych, które są zorganizowane w szeregów czasowych. Następnie danych wyjściowych liczbę do plików wyjściowych.

**Czas:** Jeśli znasz już podstawy usługi Azure, fabryki danych i partii i zostały wykonane następujące wymagania wstępne, to rozwiązanie ma jednej do dwóch godzin.

### <a name="prerequisites"></a>Wymagania wstępne
#### <a name="azure-subscription"></a>Subskrypcja platformy Azure
Jeśli nie masz subskrypcji platformy Azure, można szybko utworzyć bezpłatne konto próbne. Aby uzyskać więcej informacji, zobacz [bezpłatnej wersji próbnej](https://azure.microsoft.com/pricing/free-trial/).

#### <a name="azure-storage-account"></a>Konto magazynu Azure
Używane konto magazynu do przechowywania danych w tym samouczku. Jeśli nie masz konta magazynu, zobacz [Utwórz konto magazynu](../../storage/common/storage-create-storage-account.md#create-a-storage-account). Przykładowe rozwiązanie korzysta z magazynu obiektów blob.

#### <a name="azure-batch-account"></a>Konto usługi partia zadań Azure
Tworzenie konta usługi partia zadań za pomocą [portalu Azure](http://portal.azure.com/). Aby uzyskać więcej informacji, zobacz [Utwórz i Zarządzaj kontem usługi partia zadań](../../batch/batch-account-create-portal.md). Zanotuj klucz nazwy i konta konta usługi partia zadań. Można też użyć [AzureRmBatchAccount nowy](https://msdn.microsoft.com/library/mt603749.aspx) , aby utworzyć konto usługi partia zadań. Aby uzyskać instrukcje dotyczące sposobu używania tego polecenia cmdlet, zobacz [wprowadzenie do poleceń cmdlet programu PowerShell partii](../../batch/batch-powershell-cmdlets-get-started.md).

Przykładowe rozwiązanie używa partii (pośrednio za pośrednictwem potoku fabryki danych) do przetwarzania danych w sposób równoległy w puli węzłów obliczeniowych (zarządzanej kolekcji maszyn wirtualnych).

#### <a name="azure-batch-pool-of-virtual-machines"></a>Azure puli partii maszyn wirtualnych
Tworzenie puli partii z co najmniej dwóch węzłów obliczeniowych.

1. W [portalu Azure](https://portal.azure.com), wybierz pozycję **Przeglądaj** w menu po lewej stronie, a następnie wybierz **konta usługi partia zadań**.

2. Wybierz konta partii zadań, aby otworzyć **konta usługi partia zadań** bloku.

3. Wybierz **pule** kafelka.

4. Na **pule** bloku, wybierz opcję **Dodaj** przycisk na pasku narzędzi, aby dodać pulę.

   a. Wpisz identyfikator puli (**identyfikator puli**). Należy pamiętać, identyfikator puli. Należy go podczas tworzenia rozwiązania fabryki danych.

   b. Określ **systemu Windows Server 2012 R2** dla **rodziny systemów operacyjnych** ustawienie.

   c. Wybierz **warstwę cenową węzła**.

   d. Wprowadź **2** jako wartość **docelowego w wersji dedykowanej** ustawienie.

   e. Wprowadź **2** jako wartość **maksymalna liczba zadań na węzeł** ustawienie.

   f. Wybierz **OK** Aby utworzyć pulę.

#### <a name="azure-storage-explorer"></a>Eksplorator usługi Azure Storage
Możesz użyć [6 Eksploratora magazynu Azure](https://azurestorageexplorer.codeplex.com/) lub [CloudXplorer](http://clumsyleaf.com/products/cloudxplorer) (z ClumsyLeaf oprogramowanie) do przeglądania i zmiany danych w projektach magazynu. Możesz również sprawdzić i zmienić dane w dzienniku aplikacji hostowanych w chmurze.

1. Utworzyć kontener o nazwie **mojkontener** prywatny dostęp (Brak dostępu anonimowego).

2. Jeśli używasz CloudXplorer, należy utworzyć foldery i podfoldery o następującej strukturze:

   ![Struktura folderów i podfolderów](./media/data-factory-data-processing-using-batch/image3.png)

   `Inputfolder` i `outputfolder` są folderów najwyższego poziomu w `mycontainer`. `inputfolder` Folder zawiera podfoldery z sygnaturami daty i godziny (RRRR-MM-DD-HH).

   Użycie Eksploratora usługi Storage, w następnym kroku, możesz przekazać pliki z następującymi nazwami: `inputfolder/2015-11-16-00/file.txt`, `inputfolder/2015-11-16-01/file.txt`i tak dalej. Ten krok automatycznie tworzy foldery.

3. Utwórz plik tekstowy **plik.txt** na komputerze z zawartością, która ma kluczowego **Microsoft**. Przykładem jest "test niestandardowe aktywności testów Microsoft działania niestandardowego Microsoft".

4. Przekaż plik do następujących folderów wejściowego w magazynie obiektów blob:

   ![Wejściowy folderów](./media/data-factory-data-processing-using-batch/image4.png)

   Użycie Eksploratora usługi Storage, Przekaż **plik.txt** pliku **mojkontener**. Wybierz **kopiowania** na pasku narzędzi, aby utworzyć kopię obiektu blob. W **kopiowania obiektu Blob** okno dialogowe, zmień **Nazwa docelowego obiektu blob** do `inputfolder/2015-11-16-00/file.txt`. Powtórz ten krok, aby utworzyć `inputfolder/2015-11-16-01/file.txt`, `inputfolder/2015-11-16-02/file.txt`, `inputfolder/2015-11-16-03/file.txt`, `inputfolder/2015-11-16-04/file.txt`i tak dalej. Ta akcja powoduje automatyczne utworzenie foldery.

5. Tworzenie kontenera o nazwie `customactivitycontainer`. Przekaż plik zip działań niestandardowych do tego kontenera.

#### <a name="visual-studio"></a>Visual Studio
Zainstaluj program Visual Studio 2012 lub nowszym można utworzyć niestandardowego działania dotyczącego partii usługi do użycia w rozwiązaniu fabryki danych.

### <a name="high-level-steps-to-create-the-solution"></a>Ogólne kroki umożliwiające utworzenie rozwiązania
1. Tworzenie niestandardowego działania, który zawiera logikę przetwarzania danych.

2. Tworzenie fabryki danych, który korzysta z działań niestandardowych.

### <a name="create-the-custom-activity"></a>Tworzenie niestandardowego działania
Działania niestandardowe fabryki danych jest centralnym to przykładowe rozwiązanie. Przykładowe rozwiązanie używa usługi partia zadań do uruchomienia działania niestandardowego. Aby uzyskać informacje o tworzeniu niestandardowych działań i używać ich w potokach fabryki danych, zobacz [skorzystać z działań niestandardowych w potoku fabryki danych](data-factory-use-custom-activities.md).

Aby utworzyć .NET działań niestandardowych, którego można używać w potoku fabryki danych, należy utworzyć projektu biblioteki klas .NET z klasy, która implementuje interfejs IDotNetActivity. Ten interfejs jest tylko jedna metoda: wykonanie. Podpis metody jest następujący:

```csharp
public IDictionary<string, string> Execute(
            IEnumerable<LinkedService> linkedServices,
            IEnumerable<Dataset> datasets,
            Activity activity,
            IActivityLogger logger)
```

Metoda ma kilka kluczowych składników, które należy zrozumieć:

* Metoda przyjmuje cztery parametry:

  * **linkedServices**. Ten parametr jest wyliczalny lista połączonych usług połączonych źródeł danych wejścia/wyjścia (na przykład magazynu obiektów blob) z fabryką danych. W tym przykładzie istnieje tylko jeden połączonej usługi typu usługi Azure Storage używane dla danych wejściowych i wyjściowych.
  * **zestawy danych**. Ten parametr jest wyliczalny listę zestawów danych. Ten parametr umożliwia pobieranie lokalizacji i schematy wynika z zestawów danych wejściowych i wyjściowych.
  * **działanie**. Tego parametru reprezentuje bieżącego obiektu obliczeń. W takim przypadku jest usługa partia zadań.
  * **Rejestrator**. Rejestrator służy do zapisania tej powierzchni komentarze debugowania jako dziennika "Użytkownika" dla potoku.
* Metoda zwraca słownik, który może służyć do łańcuch niestandardowych działań w przyszłości. Ta funkcja nie jest jeszcze zaimplementowana, więc po prostu zwraca pusty słownik z metody.

#### <a name="procedure-create-the-custom-activity"></a>Procedura: Tworzenie niestandardowego działania
1. Tworzenie projektu biblioteki klas .NET w programie Visual Studio.

   a. Uruchom program Visual Studio 2012/2013/2015.

   b. Wybierz kolejno pozycje **Plik** > **Nowy** > **Projekt**.

   c. Rozwiń węzeł **szablony**i wybierz **Visual C\#**. W tym przewodniku, należy użyć serwera C\#, ale żadnego języka .NET umożliwia tworzenie działań niestandardowych.

   d. Wybierz **biblioteki klas** z listy typów projektów po prawej stronie.

   e. Wprowadź **MyDotNetActivity** dla **nazwa**.

   f. Wybierz **C:\\ADF** dla **lokalizacji**. Utwórz folder **ADF** Jeśli nie istnieje.

   g. Wybierz **OK** Aby utworzyć projekt.

2. Wybierz **narzędzia** > **Menedżera pakietów NuGet** > **Konsola Menedżera pakietów**.

3. W konsoli Menedżera pakietów wykonaj następujące polecenie, aby zaimportować Microsoft.Azure.Management.DataFactories:

    ```powershell
    Install-Package Microsoft.Azure.Management.DataFactories
    ```
4. Importuj **usługi Azure Storage** pakiet NuGet do projektu. Ten pakiet jest konieczne, ponieważ używają interfejsu API magazynu obiektów Blob w tym przykładzie:

    ```powershell
    Install-Package Azure.Storage
    ```
5. Dodaj następujący kod do pliku źródłowego w projekcie przy użyciu dyrektyw:

    ```csharp
    using System.IO;
    using System.Globalization;
    using System.Diagnostics;
    using System.Linq;
    
    using Microsoft.Azure.Management.DataFactories.Models;
    using Microsoft.Azure.Management.DataFactories.Runtime;
    
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    ```
6. Zmień nazwę przestrzeni nazw do **MyDotNetActivityNS**.

    ```csharp
    namespace MyDotNetActivityNS
    ```
7. Zmień nazwę klasy, która ma **MyDotNetActivity**i pochodzi z **IDotNetActivity** interfejsu pokazane:

    ```csharp
    public class MyDotNetActivity : IDotNetActivity
    ```
8. Implementowanie (Dodaj) **Execute** metody **IDotNetActivity** interfejsu do **MyDotNetActivity** klasy. Skopiuj następujący kod do metody. Aby uzyskać informacje o przez logikę używaną w ramach tej metody, zobacz [wykonania metody](#execute-method) sekcji.

    ```csharp
    /// <summary>
    /// The Execute method is the only method of IDotNetActivity interface you must implement.
    /// In this sample, the method invokes the Calculate method to perform the core logic.  
    /// </summary>
    public IDictionary<string, string> Execute(
       IEnumerable<LinkedService> linkedServices,
       IEnumerable<Dataset> datasets,
       Activity activity,
       IActivityLogger logger)
    {
    
       // Declare types for the input and output data stores.
       AzureStorageLinkedService inputLinkedService;
    
       Dataset inputDataset = datasets.Single(dataset => dataset.Name == activity.Inputs.Single().Name);
    
       foreach (LinkedService ls in linkedServices)
           logger.Write("linkedService.Name {0}", ls.Name);
    
       // Use the First method instead of Single because we are using the same
       // Azure Storage linked service for input and output.
       inputLinkedService = linkedServices.First(
           linkedService =>
           linkedService.Name ==
           inputDataset.Properties.LinkedServiceName).Properties.TypeProperties
           as AzureStorageLinkedService;
    
       string connectionString = inputLinkedService.ConnectionString; // To create an input storage client.
       string folderPath = GetFolderPath(inputDataset);
       string output = string.Empty; // for use later.
    
       // Create the storage client for input. Pass the connection string.
       CloudStorageAccount inputStorageAccount = CloudStorageAccount.Parse(connectionString);
       CloudBlobClient inputClient = inputStorageAccount.CreateCloudBlobClient();
    
       // Initialize the continuation token before using it in the do-while loop.
       BlobContinuationToken continuationToken = null;
       do
       {   // get the list of input blobs from the input storage client object.
           BlobResultSegment blobList = inputClient.ListBlobsSegmented(folderPath,
                                    true,
                                    BlobListingDetails.Metadata,
                                    null,
                                    continuationToken,
                                    null,
                                    null);
    
           // The Calculate method returns the number of occurrences of
           // the search term "Microsoft" in each blob associated
           // with the data slice.
           //
           // The definition of the method is shown in the next step.
           output = Calculate(blobList, logger, folderPath, ref continuationToken, "Microsoft");
    
       } while (continuationToken != null);
    
       // Get the output dataset by using the name of the dataset matched to a name in the Activity output collection.
       Dataset outputDataset = datasets.Single(dataset => dataset.Name == activity.Outputs.Single().Name);
    
       folderPath = GetFolderPath(outputDataset);
    
       logger.Write("Writing blob to the folder: {0}", folderPath);
    
       // Create a storage object for the output blob.
       CloudStorageAccount outputStorageAccount = CloudStorageAccount.Parse(connectionString);
       // Write the name of the file.
       Uri outputBlobUri = new Uri(outputStorageAccount.BlobEndpoint, folderPath + "/" + GetFileName(outputDataset));
    
       logger.Write("output blob URI: {0}", outputBlobUri.ToString());
       // Create a blob and upload the output text.
       CloudBlockBlob outputBlob = new CloudBlockBlob(outputBlobUri, outputStorageAccount.Credentials);
       logger.Write("Writing {0} to the output blob", output);
       outputBlob.UploadText(output);
    
       // The dictionary can be used to chain custom activities together in the future.
       // This feature is not implemented yet, so just return an empty dictionary.
       return new Dictionary<string, string>();
    }
    ```
9. Dodaj następujące metody pomocnika do klasy. Te metody są wywoływane przez **Execute** metody. Najważniejszą **Calculate** metody izoluje kodu, który iteruje po każdy obiekt blob.

    ```csharp
    /// <summary>
    /// Gets the folderPath value from the input/output dataset.
    /// </summary>
    private static string GetFolderPath(Dataset dataArtifact)
    {
       if (dataArtifact == null || dataArtifact.Properties == null)
       {
           return null;
       }
    
       AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;
       if (blobDataset == null)
       {
           return null;
       }
    
       return blobDataset.FolderPath;
    }
    
    /// <summary>
    /// Gets the fileName value from the input/output dataset.
    /// </summary>
    
    private static string GetFileName(Dataset dataArtifact)
    {
       if (dataArtifact == null || dataArtifact.Properties == null)
       {
           return null;
       }
    
       AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;
       if (blobDataset == null)
       {
           return null;
       }
    
       return blobDataset.FileName;
    }
    
    /// <summary>
    /// Iterates through each blob (file) in the folder, counts the number of instances of the search term in the file,
    /// and prepares the output text that is written to the output blob.
    /// </summary>
    
    public static string Calculate(BlobResultSegment Bresult, IActivityLogger logger, string folderPath, ref BlobContinuationToken token, string searchTerm)
    {
       string output = string.Empty;
       logger.Write("number of blobs found: {0}", Bresult.Results.Count<IListBlobItem>());
       foreach (IListBlobItem listBlobItem in Bresult.Results)
       {
           CloudBlockBlob inputBlob = listBlobItem as CloudBlockBlob;
           if ((inputBlob != null) && (inputBlob.Name.IndexOf("$$$.$$$") == -1))
           {
               string blobText = inputBlob.DownloadText(Encoding.ASCII, null, null, null);
               logger.Write("input blob text: {0}", blobText);
               string[] source = blobText.Split(new char[] { '.', '?', '!', ' ', ';', ':', ',' }, StringSplitOptions.RemoveEmptyEntries);
               var matchQuery = from word in source
                                where word.ToLowerInvariant() == searchTerm.ToLowerInvariant()
                                select word;
               int wordCount = matchQuery.Count();
               output += string.Format("{0} occurrences(s) of the search term \"{1}\" were found in the file {2}.\r\n", wordCount, searchTerm, inputBlob.Name);
           }
       }
       return output;
    }
    ```
    Metoda GetFolderPath zwraca ścieżkę do folderu, który wskazuje element dataset i metoda GetFileName zwraca nazwę obiektu blob/pliku, który wskazuje element dataset.

    ```csharp

    "name": "InputDataset",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "fileName": "file.txt",
            "folderPath": "mycontainer/inputfolder/{Year}-{Month}-{Day}-{Hour}",
    ```

    Metoda Calculate oblicza liczbę wystąpień słowa kluczowego "Microsoft" w pliki wejściowe (obiekty BLOB w folderze). Wyszukiwany termin "Microsoft" jest ustalony w kodzie.

10. Skompiluj projekt. Wybierz **kompilacji** z menu, a następnie wybierz **Kompiluj rozwiązanie**.

11. Uruchom Eksploratora Windows i przejdź do **bin\\debugowania** lub **bin\\wersji** folderu. Wybór folderu zależy od typu kompilacji.

12. Utwórz plik zip **MyDotNetActivity.zip** zawierający wszystkie pliki binarne w  **\\bin\\debugowania** folderu. Możesz dołączyć MyDotNetActivity. **pdb** pliku, tak aby uzyskać dodatkowe szczegóły, takie jak numer wiersza w kodzie źródłowym, która spowodowała problem, gdy wystąpi błąd.

   ![Bin\Debug listy folderów](./media/data-factory-data-processing-using-batch/image5.png)

13. Przekaż **MyDotNetActivity.zip** jako obiekt blob do kontenera obiektów blob `customactivitycontainer` w magazynie obiektów blob, że StorageLinkedService połączonej usługi w zastosowaniach ADFTutorialDataFactory. Tworzenie kontenera obiektów blob `customactivitycontainer` Jeśli jeszcze nie istnieje.

#### <a name="execute-method"></a>Execute — Metoda
Ta sekcja zawiera szczegółowe informacje o kod w metodzie Execute.

1. Elementy iteracji w kolekcji wejściowych znajdują się w [Microsoft.WindowsAzure.Storage.Blob](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.aspx) przestrzeni nazw. Do iterowania po kolekcji obiektów blob, musisz użyć **BlobContinuationToken** klasy. W zasadzie, należy użyć-pętli z tokenem jako mechanizm wyjścia z pętli while. Aby uzyskać więcej informacji, zobacz [magazynu obiektów Blob Użyj ze środowiska .NET](../../storage/blobs/storage-dotnet-how-to-use-blobs.md). Podstawowe pętli jest następujący:

    ```csharp
    // Initialize the continuation token.
    BlobContinuationToken continuationToken = null;
    do
    {
    // Get the list of input blobs from the input storage client object.
    BlobResultSegment blobList = inputClient.ListBlobsSegmented(folderPath,
    
                         true,
                                   BlobListingDetails.Metadata,
                                   null,
                                   continuationToken,
                                   null,
                                   null);
    // Return a string derived from parsing each blob.
    
     output = Calculate(blobList, logger, folderPath, ref continuationToken, "Microsoft");
    
    } while (continuationToken != null);

    ```
   Aby uzyskać więcej informacji, zobacz dokumentację [ListBlobsSegmented](https://msdn.microsoft.com/library/jj717596.aspx) metody.

2. Kod do pracy za pomocą zestawu obiektów blob logicznie przechodzi w ramach nie-pętli while. W **Execute** metody, nie-podczas pętli przekazuje listę obiektów blob do metodę o nazwie **Calculate**. Metoda zwraca ciąg zmiennej o nazwie **dane wyjściowe** będący wynikiem o iterowane przez wszystkie obiekty BLOB w segmencie.

   Zwraca liczbę wystąpień wyszukiwany termin "Microsoft" w obiekcie blob przekazany do **Calculate** metody.

    ```csharp
    output += string.Format("{0} occurrences of the search term \"{1}\" were found in the file {2}.\r\n", wordCount, searchTerm, inputBlob.Name);
    ```
3. Po **Calculate** zakończeniu metody go musi być przystosowana do nowego obiektu blob. Dla każdego zestawu obiektów blob przetwarzane nowego obiektu blob można pisać z wynikami. Można zapisać do nowego obiektu blob, najpierw należy znaleźć wyjściowego zestawu danych.

    ```csharp
    // Get the output dataset by using the name of the dataset matched to a name in the Activity output collection.
    Dataset outputDataset = datasets.Single(dataset => dataset.Name == activity.Outputs.Single().Name);
    ```
4. Kod również wywołuje metodę Pomocnika **GetFolderPath** pobierania ścieżki folderu (nazwa kontenera magazynu).

    ```csharp
    folderPath = GetFolderPath(outputDataset);
    ```
   Metoda GetFolderPath rzutuje na AzureBlobDataSet, który ma właściwość o nazwie FolderPath obiektu DataSet.

    ```csharp
    AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;
    
    return blobDataset.FolderPath;
    ```
5. Kod wywołuje **GetFileName** metodę, aby pobrać nazwę pliku (obiektów blob). Kod jest podobny do poprzedniego kodu, które zostało użyte do pobierania ścieżki folderu.

    ```csharp
    AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;
    
    return blobDataset.FileName;
    ```
6. Nazwa pliku są zapisywane przez utworzenie obiekt URI. Identyfikator URI Konstruktor korzysta **BlobEndpoint** właściwość, aby zwrócić nazwę kontenera. Nazwa i ścieżka pliku folderu są dodawane do skonstruowania identyfikator URI obiektu blob danych wyjściowych.  

    ```csharp
    // Write the name of the file.
    Uri outputBlobUri = new Uri(outputStorageAccount.BlobEndpoint, folderPath + "/" + GetFileName(outputDataset));
    ```
7. Po zapisaniu nazwy pliku może zapisać ciąg dane wyjściowe z **Calculate** metody do nowego obiektu blob:

    ```csharp
    // Create a blob and upload the output text.
    CloudBlockBlob outputBlob = new CloudBlockBlob(outputBlobUri, outputStorageAccount.Credentials);
    logger.Write("Writing {0} to the output blob", output);
    outputBlob.UploadText(output);
    ```

### <a name="create-the-data-factory"></a>Tworzenie fabryki danych
W [tworzenia działań niestandardowych](#create-the-custom-activity) sekcji utworzone niestandardowe działania i przekazać plik zip z plikami binarnymi i pliku PDB do kontenera obiektów blob. W tej sekcji Tworzenie fabryki danych z potok, który korzysta z działań niestandardowych.

Wejściowy zestaw danych dla działań niestandardowych reprezentuje obiektów blob (pliki) w folderze wejściowych (`mycontainer\\inputfolder`) w magazynie obiektów blob. Zestaw danych wyjściowych dla działania reprezentuje obiekty BLOB danych wyjściowych w folderze wyjściowym (`mycontainer\\outputfolder`) w magazynie obiektów blob.

Upuść jeden lub więcej plików w folderach wejściowych:

```
mycontainer -\> inputfolder
    2015-11-16-00
    2015-11-16-01
    2015-11-16-02
    2015-11-16-03
    2015-11-16-04
```

Na przykład usunąć jeden plik (plik.txt) o następującej zawartości do poszczególnych folderów:

```
test custom activity Microsoft test custom activity Microsoft
```

Każdego folderu wejściowych odpowiada wycinka w fabryce danych, nawet wtedy, gdy folder zawiera dwa lub więcej plików. Każdy wycinek jest przetwarzany przez potok, działania niestandardowego iterację wszystkich obiektów blob w folderze wejściowych dla tego wycinka.

Zobaczysz pięć plików wyjściowych o tej samej zawartości. Na przykład plik wyjściowy przetwarzanie pliku w folderze 2015-11-16-00 ma następującą zawartość:

```
2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file.txt.
```

Jeśli zgubisz wiele plików (plik.txt, Plik2.txt file3.txt) o tej samej zawartości do folderu wejściowych zobaczysz następującą zawartość w pliku wyjściowym. Każdego folderu (2015-11-16-00 itp.) odpowiada wycinka w tym przykładzie, mimo że znajduje się w nim wiele plików wejściowych.

```csharp
2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file.txt.
2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file2.txt.
2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file3.txt.
```

Plik wyjściowy ma trzy wiersze teraz, po jednej dla każdego pliku wejściowego (blob) w folderze skojarzone z slice (2015-11-16-00).

Zadanie jest tworzone dla każdej uruchamiania działania. W tym przykładzie istnieje tylko jedno działanie w potoku. Gdy wycinek jest przetwarzany przez potok, działania niestandardowego działa w partii przetwarzania wycinka. Ponieważ istnieje pięć wycinków (każdy wycinek może mieć wielu obiektów blob lub pliku), pięć zadań są tworzone w partii. Po uruchomieniu zadania w partii, jest działań niestandardowych, który jest uruchomiony.

Poniższe wskazówki zawiera dodatkowe szczegóły.

#### <a name="step-1-create-the-data-factory"></a>Krok 1: Tworzenie fabryki danych
1. Po zalogowaniu się do [portalu Azure](https://portal.azure.com/), wykonaj następujące czynności:

   a. Wybierz **nowy** w menu po lewej stronie.

   b. Wybierz **dane i analiza** na **nowy** bloku.

   c. Wybierz **fabryki danych** na **analizy danych** bloku.

2. Na **nowa fabryka danych** bloku, wprowadź **CustomActivityFactory** dla nazwy. Nazwa fabryki danych musi być globalnie unikatowa. Jeśli wystąpi błąd "Nazwa fabryki danych CustomActivityFactory nie jest dostępna", należy zmienić nazwy fabryki danych. Na przykład użyć yournameCustomActivityFactory i ponownie utworzyć fabryki danych.

3. Wybierz **Nazwa grupy zasobów**i wybierz istniejącą grupę zasobów lub Utwórz grupę zasobów.

4. Sprawdź poprawność subskrypcji i regionu, w którym ma fabryki danych ma zostać utworzony.

5. Wybierz **Utwórz** na **nowa fabryka danych** bloku.

6. Na pulpicie nawigacyjnym portalu jest tworzenie fabryki danych.

7. Po pomyślnym utworzeniu fabryki danych zobacz **fabryki danych** strony, która pokazuje zawartość fabryki danych.

   ![Strona fabryki danych](./media/data-factory-data-processing-using-batch/image6.png)

#### <a name="step-2-create-linked-services"></a>Krok 2: Tworzenie usługi połączonej
Połączone usługi łączenie magazyny danych lub obliczeniowe usług z fabryką danych. W tym kroku możesz połączyć Twoje konto magazynu i konto usługi partia zadań z fabryką danych.

#### <a name="create-an-azure-storage-linked-service"></a>Tworzenie połączonej usługi Azure Storage
1. Wybierz **tworzenie i wdrażanie** Kafelek na **fabryki danych** bloku **CustomActivityFactory**. Zostanie wyświetlony Edytor fabryki danych.

2. Wybierz **nowy magazyn danych** na pasku poleceń i wybierz polecenie **magazynu Azure.** Skryptu JSON, który jest używany do utworzenia magazynu, pojawi się połączonej usługi w edytorze.

   ![Nowy magazyn danych](./media/data-factory-data-processing-using-batch/image7.png)

3. Zastąp **nazwę konta** nazwą konta magazynu. Zastąp **klucz konta** kluczem dostępu do konta magazynu. Aby dowiedzieć się, jak uzyskać klucz dostępu do magazynu, zobacz [wyświetlanie, kopiowanie i regenerate magazynu, klucze dostępu](../../storage/common/storage-create-storage-account.md#manage-your-storage-account).

4. Wybierz przycisk **Wdróż** na pasku poleceń, aby wdrożyć połączoną usługę.

   ![Wdrażanie](./media/data-factory-data-processing-using-batch/image8.png)

#### <a name="create-an-azure-batch-linked-service"></a>Tworzenie usługi partia zadań Azure połączone
W tym kroku utworzysz połączonej usługi dla konta partii zadań, które jest używane do uruchamiania działań niestandardowych fabryki danych.

1. Wybierz **nowych obliczeń** na pasku poleceń i wybierz polecenie **partii zadań Azure.** Skryptu JSON, który służy do tworzenia partii pojawia się połączonej usługi w edytorze.

2. W skrypcie JSON:

   a. Zastąp **nazwa konta** z nazwą konta usługi partia zadań.

   b. Zastąp **klucz dostępu** z kluczem dostępu do konta usługi partia zadań.

   c. Wprowadź identyfikator puli dla **poolName** właściwości. Dla tej właściwości można określić nazwę puli lub identyfikator puli.

   d. Wprowadź identyfikator URI usługi partia zadań dla **batchUri** właściwości JSON.

      > [!IMPORTANT]
      > Adres URL z **konta usługi partia zadań** bloku znajduje się w następującym formacie: \<accountname\>.\< region\>. batch.azure.com. Aby uzyskać **batchUri** właściwości skryptu JSON, musisz usunąć a88 "accountname." ** z adresu URL. Może to być na przykład `"batchUri": "https://eastus.batch.azure.com"`.
      >
      >

      ![Bloku konta usługi partia zadań](./media/data-factory-data-processing-using-batch/image9.png)

      Aby uzyskać **poolName** właściwości, można również określić identyfikator puli zamiast nazwy puli.

      > [!NOTE]
      > Usługi fabryka danych nie obsługuje opcji na żądanie dla partii, w przeciwieństwie do usługi HDInsight. Można użyć własnych puli partii w fabryce danych.
      >
      >
   
   e. Określ **StorageLinkedService** dla **linkedServiceName** właściwości. Tej połączonej usługi jest utworzony w poprzednim kroku. Ten magazyn jest używany jako obszaru przemieszczania dla plików i dzienników.

3. Wybierz przycisk **Wdróż** na pasku poleceń, aby wdrożyć połączoną usługę.

#### <a name="step-3-create-datasets"></a>Krok 3: Tworzenie zestawów danych
W tym kroku możesz utworzyć zestawy danych do reprezentowania danych wejściowych i wyjściowych.

#### <a name="create-the-input-dataset"></a>Tworzenie wejściowego zestawu danych
1. W edytorze fabryki danych, wybierz **nowy zestaw danych** przycisk na pasku narzędzi. Wybierz **magazynu obiektów Blob Azure** z listy rozwijanej.

2. Zastąp skryptu JSON, w okienku po prawej stronie następujący fragment kodu JSON:

    ```json
    {
       "name": "InputDataset",
       "properties": {
           "type": "AzureBlob",
           "linkedServiceName": "AzureStorageLinkedService",
           "typeProperties": {
               "folderPath": "mycontainer/inputfolder/{Year}-{Month}-{Day}-{Hour}",
               "format": {
                   "type": "TextFormat"
               },
               "partitionedBy": [
                   {
                       "name": "Year",
                       "value": {
                           "type": "DateTime",
                           "date": "SliceStart",
                           "format": "yyyy"
                       }
                   },
                   {
                       "name": "Month",
                       "value": {
                           "type": "DateTime",
                           "date": "SliceStart",
                           "format": "MM"
                       }
                   },
                   {
                       "name": "Day",
                       "value": {
                           "type": "DateTime",
                           "date": "SliceStart",
                           "format": "dd"
                       }
                   },
                   {
                       "name": "Hour",
                       "value": {
                           "type": "DateTime",
                           "date": "SliceStart",
                           "format": "HH"
                       }
                   }
               ]
           },
           "availability": {
               "frequency": "Hour",
               "interval": 1
           },
           "external": true,
           "policy": {}
       }
    }
    ```

    Utworzyć potok w dalszej części tego przewodnika, czas rozpoczęcia 2015-11-16T00:00:00Z i na końcu czasu 2015-11-16T05:00:00Z. Zostało zaplanowane co godzinę, wygenerowało danych co pięć wycinków wejścia/wyjścia (między **00**: 00:00 -\> **05**: 00:00).

    **Częstotliwość** i **interwał** dla zestawu danych wejściowych są ustawione na **godzina** i **1**, co oznacza, że wejściowy wycinek jest dostępny co godzinę.

    Czas rozpoczęcia każdy wycinek jest reprezentowana przez **SliceStart** zmiennej systemowej w poprzednim fragment kodu JSON. Poniżej przedstawiono godziny rozpoczęcia dla każdego wycinka.

    | **Wycinek** | **Godzina rozpoczęcia**          |
    |-----------|-------------------------|
    | 1         | 2015-11-16T**00**:00:00 |
    | 2         | 2015-11-16T**01**:00:00 |
    | 3         | 2015-11-16T**02**:00:00 |
    | 4         | 2015-11-16T**03**:00:00 |
    | 5         | 2015-11-16T**04**:00:00 |

    **FolderPath** jest obliczana przy użyciu rok, miesiąc, dzień i godzinę część wycinek czas rozpoczęcia (**SliceStart**). Oto, jak folder wejściowy jest mapowany na wycinek.

    | **Wycinek** | **Godzina rozpoczęcia**          | **Folder wejściowy**  |
    |-----------|-------------------------|-------------------|
    | 1         | 2015-11-16T**00**:00:00 | 2015-11-16-**00** |
    | 2         | 2015-11-16T**01**:00:00 | 2015-11-16-**01** |
    | 3         | 2015-11-16T**02**:00:00 | 2015-11-16-**02** |
    | 4         | 2015-11-16T**03**:00:00 | 2015-11-16-**03** |
    | 5         | 2015-11-16T**04**:00:00 | 2015-11-16-**04** |

3. Wybierz **Wdróż** na pasku narzędzi, aby utworzyć i wdrożyć **InputDataset** tabeli.

#### <a name="create-the-output-dataset"></a>Tworzenie wyjściowego zestawu danych
W tym kroku utworzysz innego elementu dataset typu AzureBlob do reprezentowania danych wyjściowych.

1. W edytorze fabryki danych, wybierz **nowy zestaw danych** przycisk na pasku narzędzi. Wybierz **magazynu obiektów Blob Azure** z listy rozwijanej.

2. Zastąp skryptu JSON, w okienku po prawej stronie następujący fragment kodu JSON:

    ```json
    {
       "name": "OutputDataset",
       "properties": {
           "type": "AzureBlob",
           "linkedServiceName": "AzureStorageLinkedService",
           "typeProperties": {
               "fileName": "{slice}.txt",
               "folderPath": "mycontainer/outputfolder",
               "partitionedBy": [
                   {
                       "name": "slice",
                       "value": {
                           "type": "DateTime",
                           "date": "SliceStart",
                           "format": "yyyy-MM-dd-HH"
                       }
                   }
               ]
           },
           "availability": {
               "frequency": "Hour",
               "interval": 1
           }
       }
    }
    ```

    Obiekt blob/plik wyjściowy jest generowany dla każdego wejściowego wycinka. Oto, jak dla każdego wycinka nosi nazwę pliku wyjściowego. Wszystkie pliki wyjściowe są generowane w jednym folderze wyjściowym, `mycontainer\\outputfolder`.

    | **Wycinek** | **Godzina rozpoczęcia**          | **Plik wyjściowy**       |
    |-----------|-------------------------|-----------------------|
    | 1         | 2015-11-16T**00**:00:00 | 2015-11-16-**00.txt** |
    | 2         | 2015-11-16T**01**:00:00 | 2015-11-16-**01.txt** |
    | 3         | 2015-11-16T**02**:00:00 | 2015-11-16-**02.txt** |
    | 4         | 2015-11-16T**03**:00:00 | 2015-11-16-**03.txt** |
    | 5         | 2015-11-16T**04**:00:00 | 2015-11-16-**04.txt** |

    Należy pamiętać, że wszystkie pliki w folderze wejściowych (na przykład 2015-11-16-00) są częścią wycinek czas rozpoczęcia 2015-11-16-00. Podczas przetwarzania tego wycinka działania niestandardowego skanowania za pomocą każdego pliku i tworzy wiersz w pliku danych wyjściowych z liczbą wystąpień wyszukiwany termin "Microsoft". Jeśli istnieją trzy pliki w folderze 2015-11-16-00, istnieją trzy wiersze w danych wyjściowych pliku 2015-11-16-00.txt.

3. Wybierz **Wdróż** na pasku narzędzi, aby utworzyć i wdrożyć **OutputDataset**.

#### <a name="step-4-create-and-run-the-pipeline-with-a-custom-activity"></a>Krok 4: Tworzenie i uruchamianie potoku z działań niestandardowych
W tym kroku możesz utworzyć potok z jednego działania, niestandardowego działania utworzonego wcześniej.

> [!IMPORTANT]
> Jeśli nie zostały jeszcze przekazane **plik.txt** jako danych wejściowych folderów w kontenerze obiektów blob, zrobić przed utworzeniem potoku. **IsPaused** właściwość ma wartość false w potoku JSON, więc potoku zostanie uruchomiona natychmiast, ponieważ **start** przypada w przeszłości.
>
>

1. W edytorze fabryki danych, wybierz **nowy potok** na pasku poleceń. Jeśli nie widzisz polecenia, wybierz symbol wielokropka, aby go wyświetlić.

2. Zastąp skryptu JSON, w okienku po prawej stronie następujący fragment kodu JSON:

    ```json
    {
       "name": "PipelineCustom",
       "properties": {
           "description": "Use custom activity",
           "activities": [
               {
                   "type": "DotNetActivity",
                   "typeProperties": {
                       "assemblyName": "MyDotNetActivity.dll",
                       "entryPoint": "MyDotNetActivityNS.MyDotNetActivity",
                       "packageLinkedService": "AzureStorageLinkedService",
                       "packageFile": "customactivitycontainer/MyDotNetActivity.zip"
                   },
                   "inputs": [
                       {
                           "name": "InputDataset"
                       }
                   ],
                   "outputs": [
                       {
                           "name": "OutputDataset"
                       }
                   ],
                   "policy": {
                       "timeout": "00:30:00",
                       "concurrency": 5,
                       "retry": 3
                   },
                   "scheduler": {
                       "frequency": "Hour",
                       "interval": 1
                   },
                   "name": "MyDotNetActivity",
                   "linkedServiceName": "AzureBatchLinkedService"
               }
           ],
           "start": "2015-11-16T00:00:00Z",
           "end": "2015-11-16T05:00:00Z",
           "isPaused": false
      }
    }
    ```
   Pamiętaj o następujących kwestiach:

   * Jest tylko jedno działanie w potoku i jest typu **DotNetActivity**.
   * **AssemblyName** jest ustawiona na nazwę pliku dll **MyDotNetActivity.dll**.
   * **Punkt wejścia** ustawiono **MyDotNetActivityNS.MyDotNetActivity**. Zasadniczo jest \<przestrzeni nazw\>.\< ClassName\> w kodzie.
   * **PackageLinkedService** ustawiono **StorageLinkedService**, który wskazuje do magazynu obiektów blob, który zawiera plik zip działania niestandardowego. Jeśli używasz różnych kont magazynu dla plików wejścia/wyjścia i działania niestandardowego pliku zip, należy utworzyć inny połączoną usługą magazynu. W tym artykule przyjęto założenie, że używasz tego samego konta magazynu.
   * **PackageFile** ustawiono **customactivitycontainer/MyDotNetActivity.zip**. Jest w formacie \<containerforthezip\>/\<nameofthezip.zip\>.
   * Trwa to niestandardowe działanie **InputDataset** jako dane wejściowe i **OutputDataset** jako dane wyjściowe.
   * **LinkedServiceName** wskazuje właściwości niestandardowe działania **AzureBatchLinkedService**, który informuje fabryki danych, która to niestandardowe działanie musi być uruchamiane w partii.
   * **Współbieżności** ustawienie jest ważne. Jeśli używasz wartość domyślna to 1, nawet jeśli użytkownik ma dwa lub więcej obliczeniowe węzłów w puli partii, wycinki są przetwarzane po kolei. W związku z tym nie mogły skorzystać z równoległego przetwarzania możliwości wsadu. Jeśli ustawisz **współbieżności** na wartość większą powiedz 2, oznacza to dwa wycinków (odpowiada dwa zadania w partii) mogą być przetwarzane w tym samym czasie. W takim przypadku obie maszyny wirtualne w puli partii są wykorzystywane. Ustaw odpowiednio właściwość współbieżności.
   * Tylko jedno zadanie (wycinek) jest wykonywana na maszynie Wirtualnej w dowolnym momencie domyślnie. Domyślnie **maksymalna zadania dla maszyny Wirtualnej** ma ustawioną wartość 1 dla puli partii. W ramach wymagań wstępnych utworzono pulę z tą właściwością, wartość 2. W związku z tym wycinków fabryki dwóch danych można uruchomić na maszynie Wirtualnej w tym samym czasie.
    - **IsPaused** właściwość jest domyślnie ustawiona na wartość false. Potok uruchamia natychmiast w tym przykładzie, ponieważ wycinków rozpoczęcia w przeszłości. Tę właściwość można ustawić, **true** wstrzymania potoku i ustaw ją z powrotem do **false** ponowne uruchomienie.
    -   **Start** i **zakończenia** czasy są od siebie pięć godzin. Wycinki są tworzone co godzinę, więc pięć wycinków są produkowane przez potok.

3. Wybierz przycisk **Wdróż** na pasku poleceń, aby wdrożyć potok.

#### <a name="step-5-test-the-pipeline"></a>Krok 5: Testowanie potoku
W tym kroku należy przetestować potoku upuszczanie plików w folderach wejściowego. Rozpocznij od testowania potoku za pomocą jednego pliku dla każdego folderu wejściowego.

1. Na **fabryki danych** bloku w portalu Azure, wybierz opcję **Diagram**.

   ![Diagram](./media/data-factory-data-processing-using-batch/image10.png)

2. W **Diagram** wyświetlić, kliknij dwukrotnie plik wejściowy zestaw danych **InputDataset**.

   ![InputDataset](./media/data-factory-data-processing-using-batch/image11.png)

3. **InputDataset** zostanie wyświetlony blok z wszystkich pięciu wycinków gotowe. Powiadomienie **czas rozpoczęcia WYCINEK** i **czas zakończenia WYCINEK** dla każdego wycinka.

   ![Wejściowy wycinek rozpoczęcia i zakończenia godzin](./media/data-factory-data-processing-using-batch/image12.png)

4. W **Diagram** widok, wybierz opcję **OutputDataset**.

5. Wycinki pięć dane wyjściowe są wyświetlane w **gotowe** stan, jeśli zostały utworzone.

   ![OUTPUT wycinek rozpoczęcia i zakończenia razy](./media/data-factory-data-processing-using-batch/image13.png)

6. Aby wyświetlić zadania związane z wycinków za pomocą portalu i zobacz maszyny Wirtualnej, jakie każdy wycinek uruchomionego na. Aby uzyskać więcej informacji, zobacz [integracji fabryki danych i partii](#data-factory-and-batch-integration) sekcji.

7. Pliki wyjściowe są wyświetlane w obszarze `mycontainer` w `outputfolder` w magazynie obiektów blob.

   ![Pliki wyjściowe w magazynie](./media/data-factory-data-processing-using-batch/image15.png)

   Pięć pliki wyjściowe są wyświetlane, jednego dla każdego wejściowego wycinka. Każdy plik wyjściowy ma zawartość podobne do następujących danych wyjściowych:

    ```
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file.txt.
    ```
   Na poniższym diagramie przedstawiono sposób mapowania wycinków fabryki danych do zadań w partii. W tym przykładzie wycinek ma tylko jeden przebieg.

   ![Diagram mapowania wycinek](./media/data-factory-data-processing-using-batch/image16.png)

8. Teraz spróbuj z wieloma plikami w folderze. Tworzenie plików **Plik2.txt**, **file3.txt**, **file4.txt**, i **file5.txt** o tej samej zawartości, tak jak plik.txt w folderze **2015-11-06-01**.

9. W folderze wyjściowym, usuń plik wyjściowy **2015-11-16-01.txt**.

10. Na **OutputDataset** bloku, kliknij prawym przyciskiem myszy wycinek z **czas rozpoczęcia WYCINEK** ustawioną **2015-11-16 01:00:00 AM**. Wybierz **Uruchom** do Uruchom ponownie/ponowić wycinka. Wycinek ma teraz pięć plików zamiast jeden plik.

    ![Uruchom polecenie](./media/data-factory-data-processing-using-batch/image17.png)

11. Po uruchomieniu wycinka i jego stan jest **gotowe**, sprawdź zawartość pliku wyjściowego dla tego wycinka (**2015-11-16-01.txt**). Plik wyjściowy jest wyświetlany w obszarze `mycontainer` w `outputfolder` w magazynie obiektów blob. Powinien istnieć jeden wiersz dla każdego pliku wycinka.

    ```
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file.txt.
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file2.txt.
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file3.txt.
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file4.txt.
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file5.txt.
    ```

> [!NOTE]
> Jeśli nie usuniesz danych wyjściowych pliku 2015-11-16-01.txt przed z pięć plików wejściowych, zobacz jeden wiersz z poprzedniego uruchomienia wycinka i pięciu wierszach z bieżącego uruchomienia wycinka. Domyślnie zawartość jest dołączany do pliku wyjściowego, jeśli już istnieje.
>
>

#### <a name="data-factory-and-batch-integration"></a>Integracja z fabryki danych i usługi partia zadań
Usługi fabryka danych tworzy zadanie w partii o nazwie `adf-poolname:job-xxx`.

![Zadania wsadowe](media/data-factory-data-processing-using-batch/data-factory-batch-jobs.png)

Zadania w zadaniu jest tworzony przy każdym uruchomieniu działania wycinek. Jeśli wycinków 10 jest gotowe do przetworzenia, 10 zadań są tworzone w zadaniu. Może mieć więcej niż jeden wycinek typu uruchamiania równolegle, jeśli masz wiele węzłów obliczeniowych w puli. Jeśli maksymalna liczba zadań na węzeł obliczeń ustawiono więcej niż jeden, więcej niż jeden wycinek typu można uruchomić na tej samej mocy obliczeniowej.

W tym przykładzie istnieje pięć wycinków co pięć zadań w partii. Z **współbieżności** ustawioną **5** w potoku JSON w fabryce danych i **maksymalna zadania dla maszyny Wirtualnej** ustawioną **2** w puli partii z **2** maszyn wirtualnych, zadania uruchomione szybkie. (Sprawdź godziny rozpoczęcia i zakończenia zadania).

Aby wyświetlić zadania wsadowego i jego zadań, które są skojarzone z wycinków za pomocą portalu i zobacz maszyny Wirtualnej, jakie każdy wycinek uruchomionego na.

![Zadania wsadowego](media/data-factory-data-processing-using-batch/data-factory-batch-job-tasks.png)

### <a name="debug-the-pipeline"></a>Debugowanie potoku
Debugowanie składa się z kilku podstawowych technik.

1. Jeśli wejściowy wycinek nie został skonfigurowany do **gotowe**, potwierdzić, że struktury folderów wejściowe są poprawne i że plik.txt istnieje w folderach wejściowych.

   ![Struktura folderów wejściowych](./media/data-factory-data-processing-using-batch/image3.png)

2. W **Execute** metody działania niestandardowego, użyj **IActivityLogger** obiektu do logowania się informacje ułatwiające rozwiązywanie problemów. Zarejestrowane komunikaty wyświetlane w użytkownika\_plików dziennika 0.

   Na **OutputDataset** bloku, zaznacz go, aby wyświetlić **wycinka danych** bloku dla tego wycinka. W obszarze **odbywa się działanie**, zobaczysz jednego działania Uruchom wycinka. W przypadku wybrania **Uruchom** na pasku poleceń, można uruchomić inne działanie Uruchom dla tej samej wycinka.

   Po wybraniu uruchamiania działania, zobacz **szczegóły uruchomienia działania** bloku zawierającego listę plików dziennika. Zobacz zarejestrowane komunikaty w użytkownika\_plików dziennika 0. Gdy wystąpi błąd, zostanie wyświetlony trzech uruchomień działania, ponieważ liczby ponownych prób jest ustawiona na 3 w potoku/aktywność JSON. Po wybraniu uruchomienia działania, zobaczysz plików dziennika, które można przejrzeć, aby rozwiązać problem.

   ![Bloki wycinek OutputDataset i danych](./media/data-factory-data-processing-using-batch/image18.png)

   Na liście plików dzienników, wybierz **0.log użytkownika**. W prawym okienku wyniki za pomocą **IActivityLogger.Write** metody są wyświetlane.

   ![Szczegóły blok uruchamiania działania](./media/data-factory-data-processing-using-batch/image19.png)

   Sprawdź system-0.log za wszelkie komunikaty o błędach systemu i wyjątki.

    ```
    Trace\_T\_D\_12/6/2015 1:43:35 AM\_T\_D\_\_T\_D\_Verbose\_T\_D\_0\_T\_D\_Loading assembly file MyDotNetActivity...
    
    Trace\_T\_D\_12/6/2015 1:43:35 AM\_T\_D\_\_T\_D\_Verbose\_T\_D\_0\_T\_D\_Creating an instance of MyDotNetActivityNS.MyDotNetActivity from assembly file MyDotNetActivity...
    
    Trace\_T\_D\_12/6/2015 1:43:35 AM\_T\_D\_\_T\_D\_Verbose\_T\_D\_0\_T\_D\_Executing Module
    
    Trace\_T\_D\_12/6/2015 1:43:38 AM\_T\_D\_\_T\_D\_Information\_T\_D\_0\_T\_D\_Activity e3817da0-d843-4c5c-85c6-40ba7424dce2 finished successfully
    ```
3. Obejmują **PDB** plików w pliku zip, dzięki czemu szczegóły błędu mają informacje, takie jak stos wywołań, gdy wystąpi błąd.

4. Wszystkie pliki w pliku zip niestandardowe działania musi być na najwyższym poziomie z bez podfolderów.

   ![Lista plików zip działań niestandardowych](./media/data-factory-data-processing-using-batch/image20.png)

5. Upewnij się, że **assemblyName** (MyDotNetActivity.dll), **punktu wejścia** (MyDotNetActivityNS.MyDotNetActivity), **packageFile** (customactivitycontainer / MyDotNetActivity.zip) i **packageLinkedService** (powinny wskazywać do magazynu obiektów blob, który zawiera plik zip) są ustawione prawidłowe wartości.

6. Jeśli zostanie rozwiązany, błędu, aby ponownie przetworzyć wycinka, kliknij prawym przyciskiem myszy wycinek w **OutputDataset** bloku, a następnie wybierz **Uruchom**.

   ![Blok OutputDataset opcję Uruchom](./media/data-factory-data-processing-using-batch/image21.png)

   > [!NOTE]
   > Kontener znajduje się w magazynie obiektów blob o nazwie `adfjobs`. Ten kontener nie jest automatycznie usuwana, lecz można je bezpiecznie usunąć po zakończeniu testowania rozwiązania. Podobnie, w zakresie fabryki danych tworzy zadanie wsadowe o nazwie `adf-\<pool ID/name\>:job-0000000001`. Po przetestowaniu rozwiązania Jeśli chcesz, możesz usunąć tego zadania.
   >
   >
7. Działania niestandardowe nie są używane **app.config** plików z pakietu. W związku z tym jeśli kod odczytuje wszelkie parametry połączenia z pliku konfiguracji, nie działa w czasie wykonywania. Najlepszym rozwiązaniem, gdy używasz partii jest do przechowywania wszelkich kluczy tajnych w magazynie kluczy Azure. Następnie użyj nazwy głównej usługi na podstawie certyfikatu, aby chronić magazynu kluczy i rozpowszechniania certyfikatów do puli partii. Działania niestandardowe .NET mogą uzyskiwać dostęp do kluczy tajnych z magazynu kluczy w czasie wykonywania. To rozwiązanie ogólnego można skalować do dowolnego typu klucza tajnego, nie tylko ciąg połączenia.

    Jest dostępne obejście łatwiejsze, ale nie jest najlepszym rozwiązaniem. Można utworzyć usługi połączone bazy danych SQL z połączeniem ustawień parametrów. Następnie można utworzyć zestawu danych, który używa połączonej usługi i łańcucha zestawu danych jako fikcyjny wejściowy zestaw danych do działania niestandardowego .NET. Następnie dostępne parametry połączenia połączonej usługi w kodzie działania niestandardowego. Powinien działać prawidłowo w czasie wykonywania.  

#### <a name="extend-the-sample"></a>Rozszerzanie próbki
Można rozszerzyć tego przykładu, aby dowiedzieć się więcej o funkcjach fabryki danych i partii. Na przykład aby przetwarzać wycinki inny zakres czasu, należy wykonać następujące czynności:

1. Dodaj następujące podfoldery `inputfolder`: 2015-11-16-05, 2015-11-16-06 201-11-16-07 2011-11-16-08 i 2015-11-16-09. Umieścić pliki wejściowe w tych folderach. Zmień godzinę zakończenia dla potoku z `2015-11-16T05:00:00Z` do `2015-11-16T10:00:00Z`. W **Diagram** wyświetlić, kliknij dwukrotnie **InputDataset** i Potwierdź, że wejściowy wycinków są gotowe. Kliknij dwukrotnie **OutputDataset** aby zobaczyć stan wycinki danych wyjściowych. Jeśli w **gotowe** stanu, sprawdź folder wyjściowy dla plików wyjściowych.

2. Zwiększanie lub zmniejszanie **współbieżności** ustawienie, aby zrozumieć, jak wpływają na wydajność rozwiązania, szczególnie przetwarzania, który występuje w partii. Aby uzyskać więcej informacji na temat **współbieżności** ustawień, zobacz "krok 4: tworzenie i uruchamianie potoku z działań niestandardowych."

3. Tworzenie puli za pomocą/małe **maksymalna zadania dla maszyny Wirtualnej**. Aby używać nowego zestawu utworzonego, zaktualizuj partii połączonej usługi w rozwiązaniu fabryki danych. Aby uzyskać więcej informacji na temat **maksymalna zadania dla maszyny Wirtualnej** ustawień, zobacz "krok 4: tworzenie i uruchamianie potoku z działań niestandardowych."

4. Tworzenie puli partii z **skalowania automatycznego** funkcji. Automatyczne skalowanie węzłów obliczeniowych w puli partii jest dynamiczne Dostosowywanie przetwarzania zasilania używanych przez aplikację. 

    Przykładowa formuła tutaj osiąga następujące działania. Podczas tworzenia puli, rozpoczyna się od jednej maszyny Wirtualnej. Metryka $PendingTasks definiuje liczbę zadań uruchomione i Stany aktywny (w kolejce). Formuła znajduje średnią liczbę oczekujących zadań w ciągu ostatnich 180 sekund i odpowiednio ustawia TargetDedicated. Gwarantuje, że TargetDedicated nigdy nie wykracza poza 25 maszyn wirtualnych. Nowe zadania zostały przesłane, automatycznie zwiększa rozmiar puli. Jako zakończenie zadania wolnego jeden po drugim stają się maszyn wirtualnych i skalowanie automatyczne zmniejsza tych maszyn wirtualnych. StartingNumberOfVMs i maxNumberofVMs można dostosować do własnych potrzeb.
 
    Formuła skalowania automatycznego:

    ``` 
    startingNumberOfVMs = 1;
    maxNumberofVMs = 25;
    pendingTaskSamplePercent = $PendingTasks.GetSamplePercent(180 * TimeInterval_Second);
    pendingTaskSamples = pendingTaskSamplePercent < 70 ? startingNumberOfVMs : avg($PendingTasks.GetSample(180 * TimeInterval_Second));
    $TargetDedicated=min(maxNumberofVMs,pendingTaskSamples);
    ```

   Aby uzyskać więcej informacji, zobacz [automatycznie skali obliczeniowe węzłów w puli partii](../../batch/batch-automatic-scaling.md).

   Jeśli pula używa domyślnej [autoScaleEvaluationInterval](https://msdn.microsoft.com/library/azure/dn820173.aspx), usługa partia zadań może zająć 15 do 30 minut, aby przygotować maszyny Wirtualnej przed uruchomieniem działania niestandardowego. Jeśli inny autoScaleEvaluationInterval korzysta z puli, usługa partia zadań może zająć autoScaleEvaluationInterval plus 10 minut.

5. W rozwiązaniu próbki **Execute** wywołuje metodę **Calculate** metody, która przetwarza wycinek danych wejściowych do produkcji wycinek danych wyjściowych. Napisania własnej metody do przetwarzania danych wejściowych i Zastąp **Calculate** wywołanie metody **Execute** metody przez wywołanie do metody.

### <a name="next-steps-consume-the-data"></a>Następne kroki: danych
Po przetwarzania danych, będzie można korzystać z narzędzia online, takie jak usługi Power BI. Oto łącza, aby ułatwić zrozumienie usługi Power BI i jak z niego korzystać na platformie Azure:

* [Eksploruj zestawu danych w usłudze Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-get-data/)
* [Wprowadzenie do programu Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-getting-started/)
* [Odświeżanie danych w usłudze Power BI](https://powerbi.microsoft.com/documentation/powerbi-refresh-data/)
* [Azure i usługi Power BI: omówienie](https://powerbi.microsoft.com/documentation/powerbi-azure-and-power-bi/)

## <a name="references"></a>Dokumentacja
* [Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/)

  * [Wprowadzenie do usługi fabryka danych](data-factory-introduction.md)
  * [Rozpoczynanie pracy z fabryką danych](data-factory-build-your-first-pipeline.md)
  * [Użyj niestandardowych działań w potoku fabryki danych](data-factory-use-custom-activities.md)
* [Azure Batch](https://azure.microsoft.com/documentation/services/batch/)

  * [Podstawowe informacje o partii](../../batch/batch-technical-overview.md)
  * [Przegląd funkcji partii](../../batch/batch-api-basics.md)
  * [Utwórz i Zarządzaj kontem usługi partia zadań w portalu Azure](../../batch/batch-account-create-portal.md)
  * [Rozpoczynanie pracy z biblioteki klienta usługi partia zadań dla platformy .NET](../../batch/batch-dotnet-get-started.md)

[batch-explorer]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[batch-explorer-walkthrough]: http://blogs.technet.com/b/windowshpc/archive/2015/01/20/azure-batch-explorer-sample-walkthrough.aspx
