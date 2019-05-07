---
title: Przetwarzanie dużych ilości danych przy użyciu usługi Data Factory i Batch | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób przetwarzania ogromnej ilości danych w potoku usługi Azure Data Factory przy użyciu równoległego przetwarzania możliwości usługi Azure Batch.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: 688b964b-51d0-4faa-91a7-26c7e3150868
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: e95f167cf6dcfe90fff1c2be174ca197cb2aa004
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2019
ms.locfileid: "65204036"
---
# <a name="process-large-scale-datasets-by-using-data-factory-and-batch"></a>Przetwarzania dużych ilości danych przy użyciu usługi Data Factory i Batch
> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Azure Data Factory, która jest ogólnie dostępna. Jeśli używasz bieżącą wersję usługi Data Factory, zobacz [niestandardowych działań w usłudze Data Factory](../transform-data-using-dotnet-custom-activity.md).

W tym artykule opisano architekturę przykładowe rozwiązanie, która przenosi i przetwarza dużych ilości danych w sposób automatycznego i zaplanowane. Zawiera także wskazówki end-to-end, aby zaimplementować to rozwiązanie przy użyciu usługi Data Factory i Azure Batch.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

W tym artykule jest dłuższa niż typowy artykułu, ponieważ zawiera ona wskazówki dotyczące całej przykładowe rozwiązanie. Jeśli jesteś nowym użytkownikiem usługi Batch i Data Factory, informacje na temat tych usług i w jaki sposób działają razem. Jeśli znasz coś o usługach i są projektowanie/Projektowanie rozwiązania, możesz skoncentrować się na architekturze części tego artykułu. Jeżeli projektujesz prototyp lub rozwiązania, można wypróbować instrukcjami krok po kroku w instruktażu. Zapraszamy komentarze dotyczące tej zawartości i sposobu ich używania.

Najpierw Przyjrzyjmy się jak usługi Data Factory i Batch mogą pomóc Ci przetwarzania dużych zestawów danych w chmurze.     


## <a name="why-azure-batch"></a>Dlaczego usługa Azure Batch?
 Batch umożliwia wydajne uruchamianie dużych równoległych i o wysokiej wydajności obliczeń (HPC) aplikacji w chmurze. Jest to usługa platformy, która planuje pracę intensywnych obliczeń do uruchamiania na zarządzanej kolekcji maszyn wirtualnych (VM). Umożliwia też automatyczne skalowanie zasobów obliczeniowych w celu spełnienia potrzeb Twoich zadań.

W usłudze Batch definiuje się zasoby obliczeniowe Azure do wykonywania aplikacji równolegle i na dużą skalę. Można uruchomić na żądanie lub zaplanowane zadania. Nie trzeba ręcznie tworzenie, konfigurowanie i zarządzanie, klaster HPC, poszczególnych maszyn wirtualnych, sieci wirtualnych lub złożone zadania i infrastruktury planowania zadań podrzędnych.

 Jeśli nie znasz usługi Batch, następujące artykuły ułatwić zrozumienie architektury/implementacja rozwiązania opisane w tym artykule:   

* [Podstawy usługi Batch](../../batch/batch-technical-overview.md)
* [Omówienie funkcji usługi Batch](../../batch/batch-api-basics.md)

Opcjonalnie, aby dowiedzieć się więcej o usłudze Batch, zobacz [dokumentacji usługi Batch](https://docs.microsoft.com/azure/batch/).

## <a name="why-azure-data-factory"></a>Dlaczego warto wybrać usługę Azure Data Factory?
Fabryka danych jest usługą integracji danych w chmurze, która służy do aranżacji i automatyzacji przenoszenia i przekształcania danych. Data Factory służy do tworzenia potoków danych zarządzanych, służące do przenoszenia danych ze środowiska lokalnego i magazynów danych do scentralizowanego magazynu danych w chmurze. Przykładem są usługi Azure Blob storage. Data Factory służy do przetworzenia/przekształcenia danych za pomocą usług, takich jak Azure HDInsight i Azure Machine Learning. Można także zaplanować potoki danych, które będą uruchamiane w sposób zaplanowane, (na przykład co godzinę, codziennie, a co tydzień). Można monitorować i zarządzać potoków w celu zidentyfikowania problemów i podjąć działania.

  Jeśli nie znasz usługi Data Factory, następujące artykuły ułatwić zrozumienie architektury/implementacja rozwiązania opisane w tym artykule:  

* [Wprowadzenie do usługi Data Factory](data-factory-introduction.md)
* [Tworzenie pierwszego potoku danych](data-factory-build-your-first-pipeline.md)   

Opcjonalnie, aby dowiedzieć się więcej na temat usługi Data Factory, zobacz [dokumentację usługi Data Factory](https://docs.microsoft.com/rest/api/datafactory/v1/data-factory-data-factory).

## <a name="data-factory-and-batch-together"></a>Data Factory i Batch razem
Fabryka danych zawiera wbudowane działania. Na przykład działanie kopiowania służy do kopiowania/przenoszenia danych z magazynu danych źródłowych do docelowego magazynu danych. Działanie programu Hive jest używana do przetwarzania danych przy użyciu klastrów usługi Hadoop (HDInsight) na platformie Azure. Aby uzyskać listę działań przekształcania obsługiwane, zobacz [działania przekształcania danych](data-factory-data-transformation-activities.md).

Można również tworzyć niestandardowe działania programu .NET do przeniesienia lub przetwarzanie danych za pomocą własnej logiki. Te działania można uruchomić w klastrze usługi HDInsight lub w puli usługi Batch maszyn wirtualnych. Podczas korzystania z usługi Batch można skonfigurować pulę do automatycznego skalowania (Dodawanie lub usuwanie maszyn wirtualnych, w zależności od obciążenia) na podstawie formuły możesz podać.     

## <a name="architecture-of-a-sample-solution"></a>Architektura rozwiązania próbki
  Opisanej w tym artykule architektury jest to proste rozwiązanie. Ma również zastosowanie złożonych scenariuszy, takich jak modelowanie finansowe, przetwarzanie obrazu i renderowania i analiz dotyczących genomu ryzyka.

Na diagramie przedstawiono, jak Data Factory służy do aranżacji przenoszenia i przetwarzania danych. Pokazuje również, jak usługa Batch przetwarza dane w sposób równoległy. Pobrać i wydrukować diagram łatwiejszego odwoływania (11 x 17 cali lub rozmiaru A3). Aby uzyskać dostęp do diagramu, aby można go wydrukować, zobacz [HPC i organizowanie danych za pomocą usługi Batch i Data Factory](https://go.microsoft.com/fwlink/?LinkId=717686).

[![Diagram przetwarzania danych na dużą skalę](./media/data-factory-data-processing-using-batch/image1.png)](https://go.microsoft.com/fwlink/?LinkId=717686)

Poniższa lista zawiera podstawowe kroki procesu. Rozwiązanie zawiera kod i wyjaśnienia w celu skompilowania rozwiązania end-to-end.

* **Konfigurowanie usługi Batch z pulą węzłów obliczeniowych (maszyn wirtualnych).** Można określić liczbę węzłów, a rozmiar każdego węzła.

* **Tworzenie wystąpienia usługi Data Factory** skonfigurowanego przy użyciu jednostek, które reprezentują usługi blob storage, usługa partia zadań obliczeniowych, danych wejściowych/wyjściowych i przepływu pracy/potoku za pomocą działania przenoszenia i przekształcania danych, które.

* **Utwórz niestandardowe działanie platformy .NET w potoku usługi fabryka danych.** Działanie to Twój kod użytkownika, które jest uruchamiane na puli usługi Batch.

* **Store dużych ilości danych wejściowych w postaci obiektów blob w usłudze Azure Storage.** Dane są dzielone na wycinki logicznych w (zwykle według godziny).

* **Data Factory kopiuje dane, które są przetwarzane równolegle** do lokalizacji dodatkowej.

* **Data Factory uruchamia działania niestandardowego przy użyciu puli przydzielony przy użyciu usługi Batch.** Usługi Data Factory można jednocześnie uruchomić działania. Każde działanie przetwarza wycinka danych. Wyniki są przechowywane w magazynie.

* **Usługi Data Factory przenosi wyniki końcowe w trzecim lokalizacji** dystrybucji za pośrednictwem aplikacji lub do dalszego przetwarzania przez inne narzędzia.

## <a name="implementation-of-the-sample-solution"></a>Implementacja przykładowe rozwiązanie
Przykładowe rozwiązanie jest celowo proste. Został zaprojektowany tak, aby pokazać sposób używania usługi Data Factory i Batch razem z zestawami danych procesu. Rozwiązanie zlicza liczbę wystąpień wyszukiwany termin "Microsoft" w danych wejściowych plików, które są zorganizowane w szeregach czasowych. Wyświetla liczbę do plików wyjściowych.

**Czas:** Jeśli znasz już podstawy usługi Azure, Data Factory i Batch i zostały wykonane następujące wymagania wstępne, to rozwiązanie ma jedną do dwóch godzin.

### <a name="prerequisites"></a>Wymagania wstępne
#### <a name="azure-subscription"></a>Subskrypcja platformy Azure
Jeśli nie masz subskrypcji platformy Azure, można szybko utworzyć bezpłatne konto próbne. Aby uzyskać więcej informacji, zobacz [bezpłatna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

#### <a name="azure-storage-account"></a>Konto magazynu Azure
Konto usługi storage umożliwia przechowywanie danych w ramach tego samouczka. Jeśli nie masz konta magazynu, zobacz [Tworzenie konta magazynu](../../storage/common/storage-quickstart-create-account.md). Przykładowe rozwiązanie korzysta z magazynu obiektów blob.

#### <a name="azure-batch-account"></a>Konto usługi Azure Batch
Utwórz konto usługi Batch za pomocą [witryny Azure portal](https://portal.azure.com/). Aby uzyskać więcej informacji, zobacz [Tworzenie konta usługi Batch i zarządzanie nim](../../batch/batch-account-create-portal.md). Należy pamiętać, partii konta nazwy i klucza konta. Możesz również użyć [New AzBatchAccount](https://docs.microsoft.com/powershell/module/az.batch/new-azbatchaccount) polecenia cmdlet, aby utworzyć konto usługi Batch. Aby uzyskać instrukcje dotyczące sposobu używania tego polecenia cmdlet, zobacz [wprowadzenie do poleceń cmdlet programu PowerShell usługi Batch](../../batch/batch-powershell-cmdlets-get-started.md).

Przykładowe rozwiązanie używa usługi Batch (pośrednio za pośrednictwem potoku fabryki danych) do przetwarzania danych w sposób równoległy, w ramach puli węzłów obliczeniowych (zarządzanej kolekcji maszyn wirtualnych).

#### <a name="azure-batch-pool-of-virtual-machines"></a>Usługa Azure Batch pool maszyn wirtualnych
Tworzenie puli usługi Batch za pomocą co najmniej dwa węzły obliczeniowe.

1. W [witryny Azure portal](https://portal.azure.com), wybierz opcję **Przeglądaj** w menu po lewej stronie, a następnie wybierz **konta usługi Batch**.

1. Wybierz konto usługi Batch, aby otworzyć **konta usługi Batch** bloku.

1. Wybierz **pule** kafelka.

1. Na **pule** bloku wybierz **Dodaj** przycisk na pasku narzędzi, aby dodać pulę.

   a. Wpisz identyfikator puli (**identyfikator puli**). Należy pamiętać, identyfikator puli. Będzie on potrzebny podczas tworzenia rozwiązania fabryki danych.

   b. Określ **systemu Windows Server 2012 R2** dla **Rodzina systemów operacyjnych** ustawienie.

   c. Wybierz **warstwie cenowej węzła**.

   d. Wprowadź **2** jako wartość pozycji **docelowego w wersji dedykowanej** ustawienie.

   e. Wprowadź **2** jako wartość pozycji **maksymalna liczba zadań na węzeł** ustawienie.

   f. Wybierz **OK** do utworzenia puli.

#### <a name="azure-storage-explorer"></a>Eksplorator usługi Azure Storage
Możesz użyć [usługi Azure Storage Explorer 6](https://azurestorageexplorer.codeplex.com/) lub [CloudXplorer](http://clumsyleaf.com/products/cloudxplorer) (z oprogramowaniem ClumsyLeaf) sprawdzić i zmienić danych w projektach magazynu. Możesz również sprawdzić i zmienić danych w dziennikach aplikacji hostowanych w chmurze.

1. Utwórz kontener o nazwie **mycontainer** dostęp prywatny (bez dostępu anonimowego).

1. Jeśli używasz CloudXplorer, należy utworzyć foldery i podfoldery o następującej strukturze:

   ![Struktura folderów i podfolderów](./media/data-factory-data-processing-using-batch/image3.png)

   `Inputfolder` i `outputfolder` są foldery najwyższego poziomu w `mycontainer`. `inputfolder` Folder zawiera podfoldery z sygnatury daty i godziny (RRRR-MM-DD-HH).

   Jeśli używasz Eksploratora usługi Storage, w następnym kroku, możesz przekazać pliki o następujących nazwach: `inputfolder/2015-11-16-00/file.txt`, `inputfolder/2015-11-16-01/file.txt`i tak dalej. Ten krok jest automatycznie tworzy foldery.

1. Utwórz plik tekstowy **plik.txt** na komputerze z zawartością, która zawiera słowo kluczowe **Microsoft**. Na przykład gdy "testowa niestandardowe działanie programu Microsoft test działań niestandardowych firmy Microsoft."

1. Przekaż plik do następujących folderów wejściowych w magazynie obiektów blob:

   ![Folderach wejściowych](./media/data-factory-data-processing-using-batch/image4.png)

   Jeśli używasz Eksploratora usługi Storage, Przekaż **plik.txt** plik **mycontainer**. Wybierz **kopiowania** na pasku narzędzi, aby utworzyć kopię obiektu blob. W **obiektu Blob kopiowania** okno dialogowe, zmiana **nazwa obiektu blob docelowego** do `inputfolder/2015-11-16-00/file.txt`. Powtórz ten krok, aby utworzyć `inputfolder/2015-11-16-01/file.txt`, `inputfolder/2015-11-16-02/file.txt`, `inputfolder/2015-11-16-03/file.txt`, `inputfolder/2015-11-16-04/file.txt`i tak dalej. Ta akcja powoduje automatyczne utworzenie folderów.

1. Utwórz innego kontenera o nazwie `customactivitycontainer`. Przekaż plik zip działania niestandardowego do tego kontenera.

#### <a name="visual-studio"></a>Visual Studio
Zainstaluj program Visual Studio 2012 lub nowszy, aby utworzyć niestandardowe działanie usługi Batch do użycia w rozwiązania fabryki danych.

### <a name="high-level-steps-to-create-the-solution"></a>Ogólne kroki, aby utworzyć rozwiązanie
1. Utwórz niestandardowe działanie, które zawiera logikę przetwarzania danych.

1. Tworzenie fabryki danych, który używa działania niestandardowe.

### <a name="create-the-custom-activity"></a>Tworzenie niestandardowego działania
Niestandardowe działanie fabryki danych jest niezwykle to przykładowe rozwiązanie. Przykładowe rozwiązanie używa usługi Batch, aby uruchamiać działanie niestandardowe. Aby uzyskać informacje dotyczące opracowywania niestandardowych działań i używać ich w potoki fabryki danych, zobacz [korzystanie z działań niestandardowych w potoku usługi fabryka danych](data-factory-use-custom-activities.md).

Aby utworzyć niestandardowe działanie platformy .NET, którego można używać w potoku usługi fabryka danych, należy utworzyć projekt biblioteki klas .NET z klasą, która implementuje interfejsu IDotNetActivity. Ten interfejs ma tylko jedną metodę: Wykonywanie. Poniżej znajduje się sygnatura metody:

```csharp
public IDictionary<string, string> Execute(
            IEnumerable<LinkedService> linkedServices,
            IEnumerable<Dataset> datasets,
            Activity activity,
            IActivityLogger logger)
```

Metoda ma kilka kluczowych składników, które należy zrozumieć:

* Ta metoda przyjmuje cztery parametry:

  * **linkedServices**. Ten parametr jest lista numerowana połączonych usług połączonych źródeł danych wejściowych/wyjściowych (na przykład usługi blob storage) w usłudze data factory. W tym przykładzie istnieje tylko jeden połączonej usługi typu Azure Storage używane zarówno dane wejściowe i wyjściowe.
  * **zestawy danych**. Ten parametr jest lista numerowana zestawów danych. Ten parametr umożliwia uzyskiwanie lokalizacji i schematy definiowane przez danych wejściowych i wyjściowych zestawów danych.
  * **działanie**. Tego parametru reprezentuje bieżącej jednostki obliczeniowej. W tym przypadku jest to usługa Batch.
  * **Rejestrator**. Rejestrator umożliwia pisać komentarze debugowania tej powierzchni logowania "Użytkownik" w potoku.
* Metoda zwraca słownik, który może służyć do łańcuch niestandardowych działań w przyszłości. Ta funkcja nie jest jeszcze zaimplementowana, więc po prostu zwraca pusty słownik z metody.

#### <a name="procedure-create-the-custom-activity"></a>Procedura: Tworzenie niestandardowego działania
1. Utwórz projekt biblioteki klas .NET w programie Visual Studio.

   a. Start Visual Studio 2012/2013/2015.

   b. Wybierz kolejno pozycje **Plik** > **Nowy** > **Projekt**.

   c. Rozwiń **szablony**i wybierz **Visual C\#**. W tym przewodniku można użyć C\#, ale można użyć dowolnego języka platformy .NET do tworzenia działań niestandardowych.

   d. Wybierz **biblioteki klas** z listy typów projektów po prawej stronie.

   e. Wprowadź **MyDotNetActivity** dla **nazwa**.

   f. Wybierz **C:\\ADF** dla **lokalizacji**. Utwórz folder **ADF** Jeśli nie istnieje.

   g. Wybierz przycisk **OK**, aby utworzyć projekt.

1. Wybierz **narzędzia** > **Menedżera pakietów NuGet** > **Konsola Menedżera pakietów**.

1. W konsoli Menedżera pakietów wykonaj następujące polecenie, aby zaimportować Microsoft.Azure.Management.DataFactories:

    ```powershell
    Install-Package Microsoft.Azure.Management.DataFactories
    ```
1. Importuj **usługi Azure Storage** pakiet NuGet do projektu. Ten pakiet jest konieczne, ponieważ używają interfejsu API magazynu obiektów Blob w tym przykładzie:

    ```powershell
    Install-Package Az.Storage
    ```
1. Dodaj następujące dyrektywy using do pliku źródłowego w projekcie:

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
1. Zmień nazwę przestrzeni nazw do **MyDotNetActivityNS**.

    ```csharp
    namespace MyDotNetActivityNS
    ```
1. Zmień nazwę klasy, która ma **MyDotNetActivity**i pochodzić z **IDotNetActivity** interfejsu, jak pokazano:

    ```csharp
    public class MyDotNetActivity : IDotNetActivity
    ```
1. Implementowanie (Dodaj) **Execute** metody **IDotNetActivity** współpracować w celu **MyDotNetActivity** klasy. Skopiuj następujący kod do metody. Opis przez logikę używaną w ramach tej metody zawiera [metodę Execute](#execute-method) sekcji.

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
1. Dodaj następujące metody pomocnika do klasy. Te metody są wywoływane przez **Execute** metody. Najważniejszą **Calculate** metoda izoluje kod, który iteruje przez każdy obiekt blob.

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
    Metoda GetFolderPath zwraca ścieżkę do folderu, który wskazuje zestaw danych, a metoda GetFileName zwraca nazwę pliku obiektu blob/wskazujący zestaw danych.

    ```csharp

    "name": "InputDataset",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "fileName": "file.txt",
            "folderPath": "mycontainer/inputfolder/{Year}-{Month}-{Day}-{Hour}",
    ```

    Metoda Calculate oblicza liczbę wystąpień słowa kluczowego "Microsoft" w danych wejściowych plików (obiekty BLOB w folderze). Wyszukiwany termin "Microsoft" jest ustalony w kodzie.

1. Skompiluj projekt. Wybierz **kompilacji** menu, a następnie wybierz pozycję **Kompiluj rozwiązanie**.

1. Uruchom Eksploratora Windows i przejdź do **bin\\debugowania** lub **bin\\wersji** folderu. Wybór folderu zależy od rodzaju kompilacji.

1. Tworzenie pliku zip **MyDotNetActivity.zip** zawierający wszystkie pliki binarne w  **\\bin\\debugowania** folderu. Możesz chcieć dołączyć MyDotNetActivity. **pdb** plików tak, aby uzyskać dodatkowe szczegóły, takie jak numer linii w kodzie źródłowym, która spowodowała problem w przypadku wystąpienia błędu.

   ![Lista folderów bin\Debug](./media/data-factory-data-processing-using-batch/image5.png)

1. Przekaż **MyDotNetActivity.zip** jako obiekt blob do kontenera obiektów blob `customactivitycontainer` w magazynie obiektów blob, że usługa StorageLinkedService połączonej usługi w fabryce ADFTutorialDataFactory używa. Tworzenie kontenera obiektów blob `customactivitycontainer` Jeśli jeszcze nie istnieje.

#### <a name="execute-method"></a>Execute — Metoda
Ta sekcja zawiera szczegółowe informacje o kodzie w metodzie Execute.

1. Elementy członkowskie dla iteracji w kolekcji danych wejściowych znajdują się w [Microsoft.WindowsAzure.Storage.Blob](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob) przestrzeni nazw. Do iterowania po kolekcji obiektów blob, musisz użyć **BlobContinuationToken** klasy. W zasadzie, należy użyć wykonuj-pętli za pomocą tokenu w mechanizmie wyjścia z pętli while. Aby uzyskać więcej informacji, zobacz [Użyj usługi Blob storage z .NET](../../storage/blobs/storage-dotnet-how-to-use-blobs.md). Podstawowe pętli jest następujący:

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
   Aby uzyskać więcej informacji, zobacz dokumentację dla [ListBlobsSegmented](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._cloud_blob_container.listblobssegmented) metody.

1. Kod do pracy za pośrednictwem zestaw obiektów blob logicznie przechodzi w ramach nie-pętli while. W **Execute** metody, nie-a pętla przekazuje listę obiektów blob do metody o nazwie **Calculate**. Metoda zwraca wartość zmiennej ciągu o nazwie **dane wyjściowe** oznacza to wynik o postanowiliśmy za pośrednictwem wszystkich obiektów blob w segmencie.

   Zwraca liczbę wystąpień wyszukiwany termin "Microsoft" w obiekcie blob przekazany do **Calculate** metody.

    ```csharp
    output += string.Format("{0} occurrences of the search term \"{1}\" were found in the file {2}.\r\n", wordCount, searchTerm, inputBlob.Name);
    ```
1. Po **Calculate** metody jest zakończone, musi być napisana do nowego obiektu blob. Dla każdego zestawu przetwarzania obiektów blob można napisać nowy obiekt blob z wynikami. Aby zapisać nowy obiekt blob, najpierw Znajdź wyjściowy zestaw danych.

    ```csharp
    // Get the output dataset by using the name of the dataset matched to a name in the Activity output collection.
    Dataset outputDataset = datasets.Single(dataset => dataset.Name == activity.Outputs.Single().Name);
    ```
1. Kod wywołuje również metodę Pomocnika **GetFolderPath** Aby pobrać ścieżkę do folderu (nazwa kontenera magazynu).

    ```csharp
    folderPath = GetFolderPath(outputDataset);
    ```
   Metoda GetFolderPath rzutuje obiekt zestawu danych AzureBlobDataSet, która ma właściwość o nazwie FolderPath.

    ```csharp
    AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;
    
    return blobDataset.FolderPath;
    ```
1. Kod wywołuje **GetFileName** metodę, aby pobrać nazwę pliku (nazwa obiektu blob). Kod jest podobny do poprzedniego kodu, który został użyty do pobrania ścieżki folderu.

    ```csharp
    AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;
    
    return blobDataset.FileName;
    ```
1. Nazwa pliku jest zapisywany, tworząc w obiekcie URI. Konstruktor URI korzysta z **BlobEndpoint** właściwości, aby zwrócić nazwę kontenera. Folder ścieżka i nazwa pliku są dodawane do utworzenia identyfikatora URI obiektu blob danych wyjściowych.  

    ```csharp
    // Write the name of the file.
    Uri outputBlobUri = new Uri(outputStorageAccount.BlobEndpoint, folderPath + "/" + GetFileName(outputDataset));
    ```
1. Po zapisaniu nazwę pliku, można napisać ciągu danych wyjściowych z **Calculate** metody nowy obiekt blob:

    ```csharp
    // Create a blob and upload the output text.
    CloudBlockBlob outputBlob = new CloudBlockBlob(outputBlobUri, outputStorageAccount.Credentials);
    logger.Write("Writing {0} to the output blob", output);
    outputBlob.UploadText(output);
    ```

### <a name="create-the-data-factory"></a>Tworzenie fabryki danych
W [utworzyć niestandardowe działanie](#create-the-custom-activity) sekcji, możesz utworzyć niestandardowe działanie i przekazany plik zip z plikami binarnymi i pliku PDB do kontenera obiektów blob. W tej sekcji utworzysz fabrykę danych z potokiem, który wykorzystuje działania niestandardowe.

Wejściowy zestaw danych działania niestandardowego reprezentuje obiektów blob (pliki) w folderze wejściowym (`mycontainer\\inputfolder`) w magazynie obiektów blob. Wyjściowy zestaw danych działania reprezentuje wyjściowych obiektów blob w folderze danych wyjściowych (`mycontainer\\outputfolder`) w magazynie obiektów blob.

Upuść jeden lub więcej plików w folderach wejściowych:

```
mycontainer -\> inputfolder
    2015-11-16-00
    2015-11-16-01
    2015-11-16-02
    2015-11-16-03
    2015-11-16-04
```

Na przykład umieszczeniu jeden plik (plik.txt) o następującej zawartości w poszczególnych folderów:

```
test custom activity Microsoft test custom activity Microsoft
```

Każdego folderu danych wejściowych odpowiada wycinek w usłudze data factory, nawet wtedy, gdy folder ma dwa lub więcej plików. Każdy wycinek jest przetwarzany przez potok, niestandardowe działanie iteruje wszystkich obiektów blob w folderze wejściowym dla tego wycinka.

Zobaczysz pięć plików wyjściowych o tej samej zawartości. Na przykład plik wyjściowy z przetwarzania pliku w folderze 2015-11-16-00 ma następującą zawartość:

```
2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file.txt.
```

Jeśli usuniesz wiele plików (plik.txt, Plik2.txt i file3.txt) z taką samą zawartość do folderu danych wejściowych widać następującą zawartość w pliku wyjściowego. Każdy folder (2015-11-16-00 itp.) odpowiada wycinek w tym przykładzie, nawet jeśli folder ma wiele plików wejściowych.

```csharp
2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file.txt.
2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file2.txt.
2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file3.txt.
```

Plik wyjściowy ma trzy wiersze teraz, jeden dla każdego pliku wejściowego (obiekt blob) w folderze, który został skojarzony z wycinka (2015-11-16-00).

Zadanie jest tworzone dla każdego uruchomienia działania. W tym przykładzie jest tylko jedno działanie w potoku. Gdy wycinek jest przetwarzany przez potok, niestandardowe działanie uruchamia się w usłudze Batch do przetwarzania wycinka. Ponieważ istnieje pięć wycinków (każdy wycinek może mieć wiele obiektów blob lub pliku), pięć zadań są tworzone w usłudze Batch. Po uruchomieniu zadania w usłudze Batch to niestandardowe działanie, które jest uruchomiona.

Następujące instruktaż zawiera dodatkowe szczegóły.

#### <a name="step-1-create-the-data-factory"></a>Krok 1: Tworzenie fabryki danych
1. Po zalogowaniu się do [witryny Azure portal](https://portal.azure.com/), wykonaj następujące czynności:

   a. Wybierz **NEW** w menu po lewej stronie.

   b. Wybierz **dane + analiza** na **New** bloku.

   c. Wybierz **usługi Data Factory** na **analizy danych** bloku.

1. Na **nowa fabryka danych** bloku wprowadź **CustomActivityFactory** dla nazwy. Nazwa fabryki danych musi być globalnie unikatowa. Jeśli wystąpi błąd "Nazwa fabryki danych CustomActivityFactory jest niedostępna", Zmień nazwę fabryki danych. Na przykład użyć yournameCustomActivityFactory i ponownie utworzyć fabrykę danych.

1. Wybierz **nazwy grupy zasobów**i wybierz istniejącą grupę zasobów lub Utwórz grupę zasobów.

1. Sprawdź, czy subskrypcji oraz regionu, w której chcesz utworzyć fabrykę danych są poprawne.

1. Wybierz **Utwórz** na **nowa fabryka danych** bloku.

1. Fabryka danych jest tworzony na pulpicie nawigacyjnym portalu.

1. Po pomyślnym utworzeniu fabryki danych zostanie wyświetlona **usługi Data factory** strony, która pokazuje zawartość z fabryką danych.

   ![Strona fabryki danych](./media/data-factory-data-processing-using-batch/image6.png)

#### <a name="step-2-create-linked-services"></a>Krok 2: Tworzenie połączonych usług
Połączone usługi łączą magazyny danych lub usługi obliczeniowe z fabryką danych. W tym kroku połączysz swoje konto magazynu i konto usługi Batch z fabryką danych.

#### <a name="create-an-azure-storage-linked-service"></a>Tworzenie połączonej usługi Azure Storage
1. Wybierz **tworzenie i wdrażanie** kafelków na **usługi Data factory** bloku **CustomActivityFactory**. Pojawi się Edytor fabryki danych.

1. Wybierz **nowy magazyn danych** na pasku poleceń i wybierz polecenie **usługi Azure storage.** Skrypt JSON umożliwia utworzenie magazynu, pojawi się połączonej usługi w edytorze.

   ![Nowy magazyn danych](./media/data-factory-data-processing-using-batch/image7.png)

1. Zastąp **nazwę konta** nazwą konta magazynu. Zastąp **klucz konta** kluczem dostępu do konta magazynu. Aby dowiedzieć się, jak uzyskać klucz dostępu do magazynu, zobacz [wyświetlanie, kopiowanie i ponowne generowanie magazynu, klucze dostępu](../../storage/common/storage-account-manage.md#access-keys).

1. Wybierz przycisk **Wdróż** na pasku poleceń, aby wdrożyć połączoną usługę.

   ![Wdrażanie](./media/data-factory-data-processing-using-batch/image8.png)

#### <a name="create-an-azure-batch-linked-service"></a>Tworzenie usługi Azure Batch połączone
W tym kroku utworzysz połączoną usługę służącą do konta usługi Batch, które jest używane do uruchamiania działań niestandardowych fabryki danych.

1. Wybierz **nowe obliczenie** na pasku poleceń i wybierz polecenie **usługi Azure Batch.** Skrypt JSON, używanej do tworzenia instancji pojawi się połączonej usługi w edytorze.

1. W skrypcie JSON:

   a. Zastąp **nazwa konta** nazwą konta usługi Batch.

   b. Zastąp **klucz dostępu** kluczem dostępu konta usługi Batch.

   c. Wprowadź identyfikator puli dla **poolName** właściwości. Dla tej właściwości można określić nazwy puli lub identyfikatora puli.

   d. Wprowadź identyfikator URI usługi batch dla **batchUri** właściwość JSON.

      > [!IMPORTANT]
      > Adres URL z **konta usługi Batch** bloku znajduje się w następującym formacie: \<accountname\>.\< region\>. batch.azure.com. Aby uzyskać **batchUri** właściwość w skrypcie JSON, musisz usunąć a88 "accountname." ** z adresu URL. Może to być na przykład `"batchUri": "https://eastus.batch.azure.com"`.
      >
      >

      ![Blok konta usługi Batch](./media/data-factory-data-processing-using-batch/image9.png)

      Aby uzyskać **poolName** właściwości, można również określić identyfikator puli zamiast nazwy puli.

      > [!NOTE]
      > Usługa Data Factory nie obsługuje opcji na żądanie dla usługi Batch, co w przypadku HDInsight. Można użyć własnych puli usługi Batch w fabryce danych.
      >
      >
   
   e. Określ **StorageLinkedService** dla **linkedServiceName** właściwości. Ta połączona usługa została utworzona w poprzednim kroku. Ten magazyn jest używany jako obszaru przemieszczania dla plików i dzienników.

1. Wybierz przycisk **Wdróż** na pasku poleceń, aby wdrożyć połączoną usługę.

#### <a name="step-3-create-datasets"></a>Krok 3: Tworzenie zestawów danych
W tym kroku utworzysz zestawy danych do reprezentowania danych wejściowych i wyjściowych.

#### <a name="create-the-input-dataset"></a>Tworzenie wejściowego zestawu danych
1. W edytorze fabryki danych, wybierz **nowy zestaw danych** przycisk na pasku narzędzi. Wybierz **usługi Azure Blob storage** z listy rozwijanej.

1. Zastąp skrypt JSON w prawym okienku następującym fragmentem kodu JSON:

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

    Tworzenie potoku w dalszej części tego przewodnika, czas rozpoczęcia 2015-11-16T00:00:00Z i na końcu czasu 2015-11-16T05:00:00Z. Zostało zaplanowane w celu wygenerowania danych co godzinę, co pięć wycinki wejścia/wyjścia (między **00**: 00:00 -\> **05**: 00:00).

    **Częstotliwość** i **interwał** dla wejściowego zestawu danych są ustawione na **godzinę** i **1**, co oznacza, że wycinek danych wejściowych jest dostępny co godzinę.

    Czas rozpoczęcia dla każdego wycinka jest reprezentowany przez **SliceStart** zmiennej systemowej w poprzednim fragmencie kodu JSON. Poniżej przedstawiono godziny rozpoczęcia dla każdego wycinka.

    | **Slice** | **Godzina rozpoczęcia**          |
    |-----------|-------------------------|
    | 1         | 2015-11-16T**00**:00:00 |
    | 2         | 2015-11-16T**01**:00:00 |
    | 3         | 2015-11-16T**02**:00:00 |
    | 4         | 2015-11-16T**03**:00:00 |
    | 5         | 2015-11-16T**04**:00:00 |

    **FolderPath** jest obliczana przy użyciu rok, miesiąc, dzień i godzinę część czas rozpoczęcia wycinka (**SliceStart**). Oto, jak folderu danych wejściowych jest mapowany na wycinka.

    | **Slice** | **Godzina rozpoczęcia**          | **Folderu danych wejściowych**  |
    |-----------|-------------------------|-------------------|
    | 1         | 2015-11-16T**00**:00:00 | 2015-11-16-**00** |
    | 2         | 2015-11-16T**01**:00:00 | 2015-11-16-**01** |
    | 3         | 2015-11-16T**02**:00:00 | 2015-11-16-**02** |
    | 4         | 2015-11-16T**03**:00:00 | 2015-11-16-**03** |
    | 5         | 2015-11-16T**04**:00:00 | 2015-11-16-**04** |

1. Wybierz **Wdróż** na pasku narzędzi, aby utworzyć i wdrożyć **InputDataset** tabeli.

#### <a name="create-the-output-dataset"></a>Tworzenie wyjściowego zestawu danych
W tym kroku utworzysz inny zestaw danych typu obiektu blob platformy Azure do reprezentowania danych wyjściowych.

1. W edytorze fabryki danych, wybierz **nowy zestaw danych** przycisk na pasku narzędzi. Wybierz **usługi Azure Blob storage** z listy rozwijanej.

1. Zastąp skrypt JSON w prawym okienku następującym fragmentem kodu JSON:

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

    Obiekt blob/plik wyjściowy jest generowany dla każdego wycinek danych wejściowych. Oto, jak plik wyjściowy nosi nazwę każdego wycinka. Wszystkie pliki wyjściowe są generowane w jednym folderze danych wyjściowych, `mycontainer\\outputfolder`.

    | **Slice** | **Godzina rozpoczęcia**          | **Plik wyjściowy**       |
    |-----------|-------------------------|-----------------------|
    | 1         | 2015-11-16T**00**:00:00 | 2015-11-16-**00.txt** |
    | 2         | 2015-11-16T**01**:00:00 | 2015-11-16-**01.txt** |
    | 3         | 2015-11-16T**02**:00:00 | 2015-11-16-**02.txt** |
    | 4         | 2015-11-16T**03**:00:00 | 2015-11-16-**03.txt** |
    | 5         | 2015-11-16T**04**:00:00 | 2015-11-16-**04.txt** |

    Należy pamiętać, że wszystkie pliki w folderze wejściowym (na przykład 2015-11-16-00) są częścią wycinek czas rozpoczęcia 2015-11-16-00. Podczas przetwarzania tego wycinka niestandardowe działanie skanowania za pomocą każdego pliku i tworzy linię w pliku danych wyjściowych z liczbą wystąpień wyszukiwany termin "Microsoft". Jeśli istnieją trzy pliki w folderze 2015-11-16-00, istnieją trzy wiersze w danych wyjściowych pliku 2015-11-16-00.txt.

1. Wybierz **Wdróż** na pasku narzędzi, aby utworzyć i wdrożyć **OutputDataset**.

#### <a name="step-4-create-and-run-the-pipeline-with-a-custom-activity"></a>Krok 4: Tworzenie i uruchamianie potoku za pomocą niestandardowego działania
W tym kroku utworzysz potok z jednym działaniem, niestandardowe działanie, która została utworzona wcześniej.

> [!IMPORTANT]
> Jeśli nie został jeszcze przekazany **plik.txt** wprowadzanie foldery w kontenerze obiektów blob, to zrobić przed utworzeniem potoku. **IsPaused** właściwość jest ustawiona na wartość false w potoku JSON, dane, więc potok jest uruchamiany natychmiast, ponieważ **start** przypada w przeszłości.
>
>

1. W edytorze fabryki danych, wybierz **nowy potok** na pasku poleceń. Jeśli nie widzisz polecenia, wybierz symbol wielokropka, aby go wyświetlić.

1. Zastąp skrypt JSON w prawym okienku następującym fragmentem kodu JSON:

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

   * Potok jest tylko jedno działanie i jest typu **DotNetActivity**.
   * **AssemblyName** jest ustawiona na nazwę biblioteki DLL **MyDotNetActivity.dll**.
   * **Punkt wejścia** ustawiono **MyDotNetActivityNS.MyDotNetActivity**. Jest po prostu \<przestrzeni nazw\>.\< ClassName\> w kodzie.
   * **PackageLinkedService** ustawiono **StorageLinkedService**, który wskazuje na magazynu obiektów blob, który zawiera plik zip działania niestandardowego. Jeśli używasz różnych kont magazynu dla plików wejściowych/wyjściowych i plik zip działania niestandardowego należy utworzyć inny połączonej usługi Storage. W tym artykule założono, że używasz tego samego konta magazynu.
   * **PackageFile** ustawiono **customactivitycontainer/MyDotNetActivity.zip**. Jest w formacie \<containerforthezip\>/\<nameofthezip.zip\>.
   * Niestandardowe działanie przyjmuje **InputDataset** jako dane wejściowe i **OutputDataset** jako dane wyjściowe.
   * **LinkedServiceName** właściwość niestandardowe działanie odwołuje się do **AzureBatchLinkedService**, który informuje usługi Data Factory to niestandardowe działanie musi zostać uruchomiony w usłudze Batch.
   * **Współbieżności** ustawienie jest istotne. Jeśli używasz wartość domyślna wynosi 1, nawet jeśli masz dwa lub więcej węzłów obliczeniowych w puli usługi Batch, wycinki są przetwarzane jeden po drugim. W związku z tym nie korzystając z zalet równoległego przetwarzania możliwości usługi Batch. Jeśli ustawisz **współbieżności** na wartość większą Załóżmy, że 2 oznacza dwóch dzieli (odpowiada dwa zadania w usłudze Batch) mogą być przetwarzane w tym samym czasie. W takich przypadkach obie maszyny wirtualne w puli usługi Batch są wykorzystywane. Odpowiednio ustawić właściwości współbieżności.
   * Tylko jedno zadanie (wycinka) jest wykonywane na maszynie Wirtualnej w dowolnym momencie domyślnie. Domyślnie **maksymalna zadań na maszynę Wirtualną** jest ustawiona na 1 w przypadku puli usługi Batch. W ramach wymagań wstępnych utworzono pulę za pomocą tę właściwość ustawioną na 2. W związku z tym wycinki fabryki danych w dwóch można uruchomić na maszynie Wirtualnej w tym samym czasie.
     - **IsPaused** właściwość jest domyślnie ustawiona na wartość false. Potok uruchamia natychmiast w tym przykładzie, ponieważ wycinki można uruchomić w przeszłości. Można ustawić tę właściwość na **true** wstrzymać potoku i ustaw ją z powrotem na **false** ponownego uruchomienia.
     -   **Start** i **zakończenia** godziny są od siebie pięć godzin. Wycinki są generowane co godzinę, więc pięć wycinki są produkowane przez potok.

1. Wybierz przycisk **Wdróż** na pasku poleceń, aby wdrożyć potok.

#### <a name="step-5-test-the-pipeline"></a>Krok 5. Testowanie potoku
W tym kroku należy przetestować potoku przez umieszczenie plików w folderach wejściowych. Uruchom testowanie potoku za pomocą jednego pliku dla każdego folderu danych wejściowych.

1. Na **usługi Data factory** bloku w witrynie Azure portal, wybierz opcję **Diagram**.

   ![Diagram](./media/data-factory-data-processing-using-batch/image10.png)

1. W **Diagram** wyświetlić, kliknij dwukrotnie zestaw danych wejściowych **InputDataset**.

   ![InputDataset](./media/data-factory-data-processing-using-batch/image11.png)

1. **InputDataset** przy użyciu wszystkich pięciu wycinków gotowe, zostanie wyświetlony blok. Zwróć uwagę **czas rozpoczęcia WYCINKA** i **czas zakończenia WYCINKA** dla każdego wycinka.

   ![Wejściowe wycinek rozpoczęcia i godzinę zakończenia](./media/data-factory-data-processing-using-batch/image12.png)

1. W **Diagram** widoku, wybierz opcję **OutputDataset**.

1. Wycinki pięć dane wyjściowe są wyświetlane w **gotowe** stanie, jeśli zostały utworzone.

   ![Dane wyjściowe wycinek rozpoczęcia i godzinę zakończenia](./media/data-factory-data-processing-using-batch/image13.png)

1. Korzystanie z portalu, aby wyświetlić zadania związane z wycinków i zobacz jakie maszyny Wirtualnej, każdy wycinek, który został uruchomiony na. Aby uzyskać więcej informacji, zobacz [Integracja usługi Data Factory i Batch](#data-factory-and-batch-integration) sekcji.

1. Pliki wyjściowe są wyświetlane w obszarze `mycontainer` w `outputfolder` w usłudze blob storage.

   ![Pliki wyjściowe w magazynie](./media/data-factory-data-processing-using-batch/image15.png)

   Pięć pliki wyjściowe są wyświetlane, jednej dla każdego wycinek danych wejściowych. Każdy plik wyjściowy ma zawartość, podobne do następujących danych wyjściowych:

    ```
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file.txt.
    ```
   Na poniższym diagramie przedstawiono sposób mapowania wycinki fabryki danych do zadań w usłudze Batch. W tym przykładzie wycinek ma tylko jeden przebieg.

   ![Diagram mapowania wycinka](./media/data-factory-data-processing-using-batch/image16.png)

1. Wypróbuj teraz z wieloma plikami w folderze. Tworzenie plików **Plik2.txt**, **file3.txt**, **file4.txt**, i **file5.txt** o tej samej zawartości, tak jak plik.txt w folderze **2015-11-06-01**.

1. W folderze danych wyjściowych, usuń plik wyjściowy **2015-11-16-01.txt**.

1. Na **OutputDataset** bloku, kliknij prawym przyciskiem myszy wycinek z **czas rozpoczęcia WYCINKA** równa **2015-11-16 01:00:00 AM**. Wybierz **Uruchom** do Uruchom ponownie/ponowne przetwarzanie wycinka. Wycinek zawiera teraz pięć plików zamiast jednego pliku.

    ![Uruchom polecenie](./media/data-factory-data-processing-using-batch/image17.png)

1. Po uruchomieniu wycinka i jego stan to **gotowe**, sprawdź zawartość w pliku danych wyjściowych dla tego wycinka (**2015-11-16-01.txt**). Plik wyjściowy jest wyświetlany w obszarze `mycontainer` w `outputfolder` w usłudze blob storage. Powinna istnieć jeden wiersz dla każdego pliku wycinka.

    ```
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file.txt.
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file2.txt.
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file3.txt.
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file4.txt.
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file5.txt.
    ```

> [!NOTE]
> Jeśli nie usuniesz danych wyjściowych pliku 2015-11-16-01.txt przed z pięciu plików wejściowych, zostanie wyświetlony jeden wiersz z poprzedniego uruchomienia wycinka i pięć wierszy z bieżącego uruchomienia wycinka. Domyślnie zawartość jest dołączany do pliku wyjściowego, jeśli już istnieje.
>
>

#### <a name="data-factory-and-batch-integration"></a>Integracja usługi Data Factory i Batch
Usługa Data Factory tworzy zadanie w usłudze Batch o nazwie `adf-poolname:job-xxx`.

![Zadania usługi Batch](media/data-factory-data-processing-using-batch/data-factory-batch-jobs.png)

Zadanie w ramach zadania jest tworzone dla każdego uruchomienia działania wycinka. W przypadku gotowe do przetworzenia wycinki 10 10 zadań są tworzone w ramach zadania. Może mieć więcej niż jeden wycinek uruchamianych równolegle, jeśli masz wiele węzłów obliczeniowych w puli. Jeśli maksymalna liczba zadań na węzeł obliczeń ustawiono więcej niż jeden, uruchamiać więcej niż jeden wycinek na tych samych obliczeń.

W tym przykładzie występują pięć wycinki, dzięki czemu jest pięć zadań w usłudze Batch. Za pomocą **współbieżności** równa **5** w kodzie JSON potoku w fabryce danych i **maksymalna zadań na maszynę Wirtualną** równa **2** w puli usługi Batch za pomocą **2** maszyn wirtualnych, zadania działają szybko. (Sprawdź czas rozpoczęcia i zakończenia dla zadań).

Korzystanie z portalu, aby wyświetlić zadania usługi Batch i jego zadań podrzędnych, które są skojarzone z wycinków i zobacz jakie maszyny Wirtualnej, każdy wycinek, który został uruchomiony na.

![Zadania usługi Batch](media/data-factory-data-processing-using-batch/data-factory-batch-job-tasks.png)

### <a name="debug-the-pipeline"></a>Debugowanie potoku
Debugowanie obejmuje kilka podstawowych technik.

1. Jeśli wycinek danych wejściowych nie jest ustawiony na **gotowe**, upewnij się, że struktura folderów wejściowych jest poprawna i że plik.txt istnieje w folderach wejściowych.

   ![Struktura folderów wejściowych](./media/data-factory-data-processing-using-batch/image3.png)

1. W **Execute** metody działania niestandardowego użyj **IActivityLogger** obiektu, aby rejestrować informacje, które pomagają w rozwiązywaniu problemów. Zarejestrowane komunikaty wyświetlane użytkownika\_plików dziennika 0.

   Na **OutputDataset** bloku Wybierz wycinek, aby wyświetlić **wycinka danych** blok dla tego wycinka. W obszarze **uruchomienia działania**, zostanie wyświetlony w jednym przebiegu działania dla wycinka. Jeśli wybierzesz **Uruchom** na pasku poleceń można uruchomić kolejnego działania, uruchom dla tej samej wycinka.

   Po wybraniu uruchomienia działania, zobacz **szczegóły uruchamiania działania** bloku zawierającego listę plików dziennika. Zobacz zarejestrowane komunikaty użytkownika\_plików dziennika 0. Wystąpi błąd, zostanie wyświetlony trzy uruchomienia działania, ponieważ liczba ponownych prób jest równa 3 w kodzie JSON potoku/działania. Po wybraniu wykonywania działań, możesz przejrzeć pliki dziennika, które można przeglądać, aby rozwiązać ten błąd.

   ![Bloki wycinek OutputDataset i danych](./media/data-factory-data-processing-using-batch/image18.png)

   Na liście plików dziennika, wybierz **nazwach user-0.log**. W prawym okienku, a wyniki użycia **IActivityLogger.Write** metody są wyświetlane.

   ![Blok szczegóły uruchamiania działania](./media/data-factory-data-processing-using-batch/image19.png)

   Sprawdź system-0.log za wszelkie komunikaty o błędach systemu i wyjątki.

    ```
    Trace\_T\_D\_12/6/2015 1:43:35 AM\_T\_D\_\_T\_D\_Verbose\_T\_D\_0\_T\_D\_Loading assembly file MyDotNetActivity...
    
    Trace\_T\_D\_12/6/2015 1:43:35 AM\_T\_D\_\_T\_D\_Verbose\_T\_D\_0\_T\_D\_Creating an instance of MyDotNetActivityNS.MyDotNetActivity from assembly file MyDotNetActivity...
    
    Trace\_T\_D\_12/6/2015 1:43:35 AM\_T\_D\_\_T\_D\_Verbose\_T\_D\_0\_T\_D\_Executing Module
    
    Trace\_T\_D\_12/6/2015 1:43:38 AM\_T\_D\_\_T\_D\_Information\_T\_D\_0\_T\_D\_Activity e3817da0-d843-4c5c-85c6-40ba7424dce2 finished successfully
    ```
1. Obejmują **PDB** plików w pliku zip, dzięki czemu szczegóły błędu mieć informacje, takie jak stos wywołań, gdy wystąpi błąd.

1. Wszystkie pliki w pliku zip działania niestandardowego musi być na najwyższym poziomie przy użyciu bez podfolderów.

   ![Lista plików zip działania niestandardowego](./media/data-factory-data-processing-using-batch/image20.png)

1. Upewnij się, że **assemblyName** (MyDotNetActivity.dll), **punktu wejścia** (MyDotNetActivityNS.MyDotNetActivity), **packageFile** (customactivitycontainer / MyDotNetActivity.zip) i **packageLinkedService** (powinien wskazywać do magazynu obiektów blob, który zawiera plik zip) są ustawione na poprawne wartości.

1. Jeśli naprawiono błąd i chcesz przetworzyć wycinek ponownie, kliknij prawym przyciskiem wycinek w **OutputDataset** bloku, a następnie wybierz pozycję **Uruchom**.

   ![Blok OutputDataset opcji Uruchom](./media/data-factory-data-processing-using-batch/image21.png)

   > [!NOTE]
   > Kontener znajduje się w magazynie obiektów blob o nazwie `adfjobs`. Ten kontener nie jest automatycznie usuwany, ale można bezpiecznie usunąć go po zakończeniu testowania rozwiązania. Podobnie do rozwiązania fabryki danych tworzy zadanie usługi Batch o nazwie `adf-\<pool ID/name\>:job-0000000001`. Po przetestowaniu rozwiązania, jeśli chcesz, możesz usunąć to zadanie.
   >
   >
1. Działanie niestandardowe nie korzysta z **app.config** plik z pakietu. W związku z tym jeśli kod odczyta dowolne parametry połączenia z pliku konfiguracyjnego, nie działa w czasie wykonywania. Najlepszym rozwiązaniem podczas korzystania z usługi Batch jest przechowywanie wszystkich danych poufnych w usłudze Azure Key Vault. Następnie należy użyć nazwy głównej usługi oparte na certyfikatach do ochrony magazynu kluczy i dystrybucja certyfikatu do puli usługi Batch. Niestandardowe działanie platformy .NET dostęp do danych poufnych z magazynu kluczy w czasie wykonywania. To rozwiązanie ogólne można skalować do dowolnego typu danych poufnych, nie tylko parametrów połączenia.

    Nie jest dostępne obejście prostsze, ale nie jest najlepszym rozwiązaniem. Możesz utworzyć usługi połączonej bazy danych SQL z połączeniem ustawień parametrów. Następnie możesz utworzyć zestaw danych, który korzysta z połączonej usługi i połączony zestaw danych jako fikcyjnego wejściowy zestaw danych do niestandardowe działanie platformy .NET. Mogą uzyskiwać dostęp do parametrów połączenia połączonej usługi w kodzie działań niestandardowych. Powinien działać prawidłowo w czasie wykonywania.  

#### <a name="extend-the-sample"></a>Rozszerzanie przykładu
Możesz rozszerzyć ten przykład, aby dowiedzieć się więcej o funkcjach usługi Data Factory i Batch. Na przykład aby przetwarzać wycinki w zakresie innym czasie, wykonaj następujące czynności:

1. Dodaj następujące podfoldery w `inputfolder`: 2015-11-16-05, 2015-11-16-06 201-11-16-07 2011-11-16-08 i 2015-11-16-09. Umieść pliki wejściowe w tych folderach. Zmiana godziny zakończenia potoku z poziomu `2015-11-16T05:00:00Z` do `2015-11-16T10:00:00Z`. W **Diagram** wyświetlić, kliknij dwukrotnie **InputDataset** i upewnij się, że wycinki wejściowe są gotowe. Kliknij dwukrotnie **OutputDataset** sprawdzić stan wycinków danych wyjściowych. Jeśli są w **gotowe** stanie, sprawdź folder wyjściowy dla plików wyjściowych.

1. Zwiększanie lub zmniejszanie **współbieżności** ustawienie, aby zrozumieć, jak wpływa na wydajność rozwiązania, szczególnie przetwarzania, który występuje w usłudze Batch. Aby uzyskać więcej informacji na temat **współbieżności** ustawienie, zobacz "krok 4: Tworzenie i uruchamianie potoku za pomocą niestandardowego działania".

1. Tworzenie puli za pomocą wyższe/niższe **maksymalna zadań na maszynę Wirtualną**. Aby użyć nowej puli, który został utworzony, należy zaktualizować usługi połączone usługi Batch w rozwiązania fabryki danych. Aby uzyskać więcej informacji na temat **maksymalna zadań na maszynę Wirtualną** ustawienie, zobacz "krok 4: Tworzenie i uruchamianie potoku za pomocą niestandardowego działania".

1. Utwórz pulę usługi Batch przy użyciu **skalowania automatycznego** funkcji. Automatyczne skalowanie węzłów obliczeniowych w puli usługi Batch jest dynamicznego dostosowania mocy obliczeniowej, używanych przez aplikację. 

    W tym miejscu wartość przykładowa formuła realizuje następujące zachowanie. Podczas tworzenia puli, rozpoczyna się z jedną maszyną Wirtualną. Metryka $PendingTasks definiuje liczbę zadań, uruchomione i Stany aktywne (kolejki). Formuła wyszukuje średnią liczbę zadań oczekujących w ostatnich 180 sekund i ustawia odpowiednio TargetDedicated. Zapewnia, że TargetDedicated nigdy nie trafiają ponad 25 maszyn wirtualnych. Ponieważ nowe zadania są przesyłane automatycznie zwiększa rozmiar puli. Jako zadania maszyny wirtualne stają się wolne pojedynczo, a następnie wykorzystujące automatyczne skalowanie zmniejsza tych maszyn wirtualnych. StartingNumberOfVMs i maxNumberofVMs można dostosować do swoich potrzeb.
 
    Formułę skalowania automatycznego:

    ``` 
    startingNumberOfVMs = 1;
    maxNumberofVMs = 25;
    pendingTaskSamplePercent = $PendingTasks.GetSamplePercent(180 * TimeInterval_Second);
    pendingTaskSamples = pendingTaskSamplePercent < 70 ? startingNumberOfVMs : avg($PendingTasks.GetSample(180 * TimeInterval_Second));
    $TargetDedicated=min(maxNumberofVMs,pendingTaskSamples);
    ```

   Aby uzyskać więcej informacji, zobacz [automatyczne skalowanie węzłów obliczeniowych w puli usługi Batch](../../batch/batch-automatic-scaling.md).

   Jeśli pula używa domyślnego [autoScaleEvaluationInterval](https://msdn.microsoft.com/library/azure/dn820173.aspx), usługa Batch może zająć 15 do 30 minut, aby przygotować maszynę Wirtualną przed uruchomieniem działania niestandardowego. Jeśli pula używa różnych autoScaleEvaluationInterval, usługa Batch może zająć autoScaleEvaluationInterval plus 10 minut.

1. W przykładowym rozwiązaniu **Execute** wywołuje metodę **Calculate** metody, która przetwarza wycinek danych wejściowych w taki sposób, aby utworzyć wycinek danych wyjściowych. Można napisać metodę do przetwarzania danych wejściowych i Zastąp **Calculate** metody wywołania w **Execute** metody przy użyciu wywołania do metody.

### <a name="next-steps-consume-the-data"></a>Następne kroki: Korzystają z danych
Po użytkownik przetwarzania danych, będzie można korzystać z narzędziami online, takie jak Power BI. Oto łącza, aby lepiej zrozumieć, usługa Power BI i jak z niej korzystać na platformie Azure:

* [Zapoznaj się z zestawu danych w usłudze Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-get-data/)
* [Rozpoczynanie pracy z usługą Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-getting-started/)
* [Odświeżanie danych w usłudze Power BI](https://powerbi.microsoft.com/documentation/powerbi-refresh-data/)
* [Azure i usługi Power BI: Ogólne omówienie](https://powerbi.microsoft.com/documentation/powerbi-azure-and-power-bi/)

## <a name="references"></a>Dokumentacja
* [Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/)

  * [Wprowadzenie do usługi Data Factory](data-factory-introduction.md)
  * [Rozpoczynanie pracy z usługą Data Factory](data-factory-build-your-first-pipeline.md)
  * [Używanie niestandardowych działań w potoku usługi fabryka danych](data-factory-use-custom-activities.md)
* [Azure Batch](https://azure.microsoft.com/documentation/services/batch/)

  * [Podstawy usługi Batch](../../batch/batch-technical-overview.md)
  * [Omówienie funkcji usługi Batch](../../batch/batch-api-basics.md)
  * [Tworzenie i zarządzanie kontem usługi Batch w witrynie Azure portal](../../batch/batch-account-create-portal.md)
  * [Rozpoczynanie pracy z biblioteki klienta usługi Batch dla platformy .NET](../../batch/quick-run-dotnet.md)

[batch-explorer]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[batch-explorer-walkthrough]: https://blogs.technet.com/b/windowshpc/archive/2015/01/20/azure-batch-explorer-sample-walkthrough.aspx
