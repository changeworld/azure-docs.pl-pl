---
title: Korzystanie z działań niestandardowych w potoku usługi Azure Data Factory
description: Informacje o sposobie tworzenia niestandardowych działań i używać ich w potoku fabryki danych Azure.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.assetid: 8dd7ba14-15d2-4fd9-9ada-0b2c684327e9
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: douglasl
robots: noindex
ms.openlocfilehash: d12038daf5d74889ca99ed2c7d35009f4cb7add7
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34622817"
---
# <a name="use-custom-activities-in-an-azure-data-factory-pipeline"></a>Korzystanie z działań niestandardowych w potoku usługi Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Wersja 1 — ogólnie dostępna](data-factory-use-custom-activities.md)
> * [Wersja 2 — wersja zapoznawcza](../transform-data-using-dotnet-custom-activity.md)

> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory, która jest ogólnie dostępna (GA). Jeśli używasz wersji 2 usługi fabryka danych, która jest w wersji zapoznawczej, zobacz [działań niestandardowych w wersji 2](../transform-data-using-dotnet-custom-activity.md).

Istnieją dwa typy działań, które można używać w potoku fabryki danych Azure.

- [Działania przepływu danych](data-factory-data-movement-activities.md) do przenoszenia danych między [obsługiwane magazyny danych źródłowy i odbiorczy](data-factory-data-movement-activities.md#supported-data-stores-and-formats).
- [Działania przekształcania danych](data-factory-data-transformation-activities.md) do przekształcania danych przy użyciu usług, takich jak Azure HDInsight, partii zadań Azure i usługi Azure Machine Learning obliczeniowe. 

Aby przenieść dane do/z magazynem danych, który nie obsługuje fabryki danych, należy utworzyć **działania niestandardowego** z własnych logiki przenoszenia danych i użyj działania w potoku. Podobnie aby proces/transformacji danych w taki sposób, który nie jest obsługiwany przez fabrykę danych, tworzenie niestandardowego działania na własną logikę przekształcania danych i użyj działania w potoku. 

Można skonfigurować niestandardowe działania do uruchamiania na **partii zadań Azure** puli maszyn wirtualnych. Korzystając z partii zadań Azure, można użyć tylko istniejącej puli partii zadań Azure.

Następujący przewodnik zawiera instrukcje krok po kroku dotyczące tworzenia działań niestandardowych .NET i używania działań niestandardowych w potoku. Użyto **partii zadań Azure** połączonej usługi. 

> [!IMPORTANT]
> - Nie jest możliwe użycie bramę zarządzania danymi z działań niestandardowych dostęp do lokalnych źródeł danych. Obecnie [brama zarządzania danymi](data-factory-data-management-gateway.md) obsługuje tylko działania kopiowania i działania procedury składowanej w fabryce danych.   

## <a name="walkthrough-create-a-custom-activity"></a>Wskazówki: Tworzenie niestandardowego działania
### <a name="prerequisites"></a>Wymagania wstępne
* Visual Studio 2012/2013/2015
* Pobierz i zainstaluj zestaw [SDK .NET Azure](https://azure.microsoft.com/downloads/).

### <a name="azure-batch-prerequisites"></a>Wymagania wstępne partii platformy Azure
W tym przewodnikiem możesz uruchomić własnych działań platformy .NET przy użyciu partii zadań Azure jako zasoby obliczeniowe. **Partia zadań Azure** Trwa obliczanie platformy usług do uruchomienia równoległego na dużą skalę i wysokiej wydajności (HPC) aplikacji wydajnie w chmurze. Partia zadań Azure Planowanie zadań obliczeniowych do uruchamiania na zarządzanego **kolekcji maszyn wirtualnych**, i może automatycznie skali zasoby obliczeniowe do potrzeb zadań. Zobacz [podstawowe informacje o partii zadań Azure] [ batch-technical-overview] artykułu szczegółowe omówienie usługi partia zadań Azure.

Samouczek należy utworzyć konto partii zadań Azure przy użyciu puli maszyn wirtualnych. Oto konkretne kroki:

1. Utwórz **konto partii zadań Azure** przy użyciu [portalu Azure](http://portal.azure.com). Zobacz [tworzenie i zarządzanie nimi konto partii zadań Azure] [ batch-create-account] artykułu, aby uzyskać instrukcje.
2. Zanotuj nazwę konta usługi partia zadań Azure, klucz konta, identyfikator URI i nazwa puli. Należy je w celu utworzenia usługi partia zadań Azure połączone.
    1. Na stronie głównej dla konta usługi partia zadań Azure, zobacz **adres URL** w następującym formacie: `https://myaccount.westus.batch.azure.com`. W tym przykładzie **myaccount** jest nazwa konta partii zadań Azure. Identyfikator URI używanych w definicji połączonej usługi jest adresem URL bez nazwy konta. Na przykład: `https://<region>.batch.azure.com`.
    2. Kliknij przycisk **klucze** w menu po lewej stronie, a następnie skopiuj **podstawowy klucz dostępu**.
    3. Aby użyć istniejącej puli, kliknij **pule** na pasku menu, a następnie zanotuj **identyfikator** puli. Jeśli nie masz istniejącej puli, przejdź do następnego kroku.     
2. Utwórz **puli partii zadań Azure**.

   1. W [portalu Azure](https://portal.azure.com), kliknij przycisk **Przeglądaj** w menu po lewej stronie, a następnie kliknij przycisk **konta usługi partia zadań**.
   2. Wybierz konto partii zadań Azure, aby otworzyć **konta usługi partia zadań** bloku.
   3. Kliknij przycisk **pule** kafelka.
   4. W **pule** bloku, kliknij przycisk Dodaj, aby dodać pulę na pasku narzędzi.
      1. Wpisz identyfikator puli (identyfikator puli). Uwaga **identyfikator puli**; potrzebne podczas tworzenia rozwiązania fabryki danych.
      2. Określ **systemu Windows Server 2012 R2** ustawienia rodziny systemów operacyjnych.
      3. Wybierz **warstwę cenową węzła**.
      4. Wprowadź **2** wartości dla **docelowego w wersji dedykowanej** ustawienie.
      5. Wprowadź **2** wartości dla **maksymalna liczba zadań na węzeł** ustawienie.
   5. Kliknij przycisk **OK**, aby utworzyć pulę.
   6. Należy zanotować **identyfikator** puli. 



### <a name="high-level-steps"></a>Ogólne kroki
Poniżej przedstawiono dwa ogólne kroki wykonywane w ramach tego przewodnika: 

1. Tworzenie niestandardowego działania, który zawiera proste danych przekształcania/przetwarzania logiki.
2. Tworzenie fabryki danych Azure z potok, który korzysta z działań niestandardowych.

### <a name="create-a-custom-activity"></a>Tworzenie niestandardowego działania
Aby utworzyć niestandardowe działanie .NET, należy utworzyć **Biblioteka klas programu .NET** projektu z klasy, która implementuje, który **IDotNetActivity** interfejsu. Ten interfejs jest tylko jedna metoda: [Execute](https://msdn.microsoft.com/library/azure/mt603945.aspx) i podpisie:

```csharp
public IDictionary<string, string> Execute(
        IEnumerable<LinkedService> linkedServices,
        IEnumerable<Dataset> datasets,
        Activity activity,
        IActivityLogger logger)
```


Metoda przyjmuje cztery parametry:

- **linkedServices**. Ta właściwość jest wyliczalny listę usług połączonych magazyn danych odwołuje się zestaw danych wejścia/wyjścia dla działania.   
- **zestawy danych**. Ta właściwość jest wyliczalny listę zestawów danych wejścia/wyjścia dla działania. Ten parametr umożliwia pobieranie lokalizacji i schematy wynika z zestawów danych wejściowych i wyjściowych.
- **działanie**. Ta właściwość reprezentuje bieżącego działania. Umożliwia dostęp rozszerzone właściwości skojarzone z działań niestandardowych do. Zobacz [dostępu właściwości rozszerzone](#access-extended-properties) szczegółowe informacje.
- **Rejestrator**. Ten obiekt umożliwia zapisanie komentarze debugowania tej powierzchni w dzienniku użytkownika dla potoku.

Metoda zwraca słownik, który może służyć do łańcuch niestandardowych działań w przyszłości. Ta funkcja nie jest jeszcze zaimplementowana, więc Zwróć pusty słownik z metody.  

### <a name="procedure"></a>Procedura
1. Utwórz **Biblioteka klas programu .NET** projektu.
   <ol type="a">
     <li>Uruchom <b>programu Visual Studio 2017</b> lub <b>programu Visual Studio 2015</b> lub <b>programu Visual Studio 2013</b> lub <b>programu Visual Studio 2012</b>.</li>
     <li>Kliknij pozycję <b>Plik</b>, wskaż polecenie <b>Nowy</b> i kliknij pozycję <b>Projekt</b>.</li>
     <li>Rozwiń węzeł <b>Szablony</b> i wybierz opcję <b>Visual C#</b>. W tym przewodniku używasz C#, ale można używać dowolnego języka .NET do opracowywania działań niestandardowych.</li>
     <li>Wybierz <b>biblioteki klas</b> z listy typów projektów po prawej stronie. VS 2017 wybierz <b>biblioteki klas (.NET Framework)</b> </li>
     <li>Wprowadź <b>MyDotNetActivity</b> dla <b>nazwa</b>.</li>
     <li>Wybierz <b>C:\ADFGetStarted</b> dla <b>lokalizacji</b>.</li>
     <li>Kliknij przycisk <b>OK</b>, aby utworzyć projekt.</li>
   </ol>
   
2. Kliknij pozycję **Narzędzia**, wskaż pozycję **Menedżer pakietów NuGet**, a następnie kliknij pozycję **Konsola menedżera pakietów**.

3. W konsoli Menedżera pakietów, wykonaj następujące polecenie, aby zaimportować **Microsoft.Azure.Management.DataFactories**.

    ```PowerShell
    Install-Package Microsoft.Azure.Management.DataFactories
    ```
4. Importuj **usługi Azure Storage** pakiet NuGet do projektu.

    ```PowerShell
    Install-Package WindowsAzure.Storage -Version 4.3.0
    ```

    > [!IMPORTANT]
    > Uruchamianie usługi fabryka danych wymaga wersji 4.3 WindowsAzure.Storage. Jeśli dodasz odwołanie do nowszej wersji zestawu Azure Storage w projekcie działań niestandardowych, zostanie wyświetlony błąd, gdy działanie wykonuje. Aby rozwiązać problem, zobacz [izolacji elementu Appdomain](#appdomain-isolation) sekcji. 
5. Dodaj następujące **przy użyciu** instrukcje do pliku źródłowego w projekcie.

    ```csharp

    // Comment these lines if using VS 2017
    using System.IO;
    using System.Globalization;
    using System.Diagnostics;
    using System.Linq;
    // --------------------

    // Comment these lines if using <= VS 2015
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Text;
    using System.Threading.Tasks;
    // ---------------------

    using Microsoft.Azure.Management.DataFactories.Models;
    using Microsoft.Azure.Management.DataFactories.Runtime;

    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    ```
6. Zmień nazwę **przestrzeni nazw** do **MyDotNetActivityNS**.

    ```csharp
    namespace MyDotNetActivityNS
    ```
7. Zmień nazwę klasy, która ma **MyDotNetActivity** i pochodzi ona z **IDotNetActivity** interfejsu pokazane na poniższy fragment kodu:

    ```csharp
    public class MyDotNetActivity : IDotNetActivity
    ```
8. Implementowanie (Dodaj) **Execute** metody **IDotNetActivity** interfejsu do **MyDotNetActivity** klasy i skopiuj następujący kod do metody.

    Poniższy przykład zlicza wystąpienia terminu wyszukiwania ("Microsoft") w każdym obiekcie blob skojarzony z wycinka danych.

    ```csharp
    /// <summary>
    /// Execute method is the only method of IDotNetActivity interface you must implement.
    /// In this sample, the method invokes the Calculate method to perform the core logic.  
    /// </summary>
    
    public IDictionary<string, string> Execute(
        IEnumerable<LinkedService> linkedServices,
        IEnumerable<Dataset> datasets,
        Activity activity,
        IActivityLogger logger)
    {
        // get extended properties defined in activity JSON definition
        // (for example: SliceStart)
        DotNetActivity dotNetActivity = (DotNetActivity)activity.TypeProperties;
        string sliceStartString = dotNetActivity.ExtendedProperties["SliceStart"];
    
        // to log information, use the logger object
        // log all extended properties            
        IDictionary<string, string> extendedProperties = dotNetActivity.ExtendedProperties;
        logger.Write("Logging extended properties if any...");
        foreach (KeyValuePair<string, string> entry in extendedProperties)
        {
            logger.Write("<key:{0}> <value:{1}>", entry.Key, entry.Value);
        }
    
        // linked service for input and output data stores
        // in this example, same storage is used for both input/output
        AzureStorageLinkedService inputLinkedService;

        // get the input dataset
        Dataset inputDataset = datasets.Single(dataset => dataset.Name == activity.Inputs.Single().Name);
    
        // declare variables to hold type properties of input/output datasets
        AzureBlobDataset inputTypeProperties, outputTypeProperties;
        
        // get type properties from the dataset object
        inputTypeProperties = inputDataset.Properties.TypeProperties as AzureBlobDataset;
    
        // log linked services passed in linkedServices parameter
        // you will see two linked services of type: AzureStorage
        // one for input dataset and the other for output dataset 
        foreach (LinkedService ls in linkedServices)
            logger.Write("linkedService.Name {0}", ls.Name);
    
        // get the first Azure Storate linked service from linkedServices object
        // using First method instead of Single since we are using the same
        // Azure Storage linked service for input and output.
        inputLinkedService = linkedServices.First(
            linkedService =>
            linkedService.Name ==
            inputDataset.Properties.LinkedServiceName).Properties.TypeProperties
            as AzureStorageLinkedService;
    
        // get the connection string in the linked service
        string connectionString = inputLinkedService.ConnectionString;
    
        // get the folder path from the input dataset definition
        string folderPath = GetFolderPath(inputDataset);
        string output = string.Empty; // for use later.
    
        // create storage client for input. Pass the connection string.
        CloudStorageAccount inputStorageAccount = CloudStorageAccount.Parse(connectionString);
        CloudBlobClient inputClient = inputStorageAccount.CreateCloudBlobClient();
    
        // initialize the continuation token before using it in the do-while loop.
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
    
            // Calculate method returns the number of occurrences of
            // the search term (“Microsoft”) in each blob associated
               // with the data slice. definition of the method is shown in the next step.
    
            output = Calculate(blobList, logger, folderPath, ref continuationToken, "Microsoft");
    
        } while (continuationToken != null);
    
        // get the output dataset using the name of the dataset matched to a name in the Activity output collection.
        Dataset outputDataset = datasets.Single(dataset => dataset.Name == activity.Outputs.Single().Name);

        // get type properties for the output dataset
        outputTypeProperties = outputDataset.Properties.TypeProperties as AzureBlobDataset;
    
        // get the folder path from the output dataset definition
        folderPath = GetFolderPath(outputDataset);

        // log the output folder path   
        logger.Write("Writing blob to the folder: {0}", folderPath);
    
        // create a storage object for the output blob.
        CloudStorageAccount outputStorageAccount = CloudStorageAccount.Parse(connectionString);
        // write the name of the file.
        Uri outputBlobUri = new Uri(outputStorageAccount.BlobEndpoint, folderPath + "/" + GetFileName(outputDataset));
    
        // log the output file name
        logger.Write("output blob URI: {0}", outputBlobUri.ToString());

        // create a blob and upload the output text.
        CloudBlockBlob outputBlob = new CloudBlockBlob(outputBlobUri, outputStorageAccount.Credentials);
        logger.Write("Writing {0} to the output blob", output);
        outputBlob.UploadText(output);
    
        // The dictionary can be used to chain custom activities together in the future.
        // This feature is not implemented yet, so just return an empty dictionary.  
    
        return new Dictionary<string, string>();
    }
    ```
9. Dodaj następujące metody pomocnika: 

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

        // get type properties of the dataset   
        AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;
        if (blobDataset == null)
        {
            return null;
        }
    
        // return the folder path found in the type properties
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
    
        // get type properties of the dataset
        AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;
        if (blobDataset == null)
        {
            return null;
        }
    
        // return the blob/file name in the type properties
        return blobDataset.FileName;
    }
    
    /// <summary>
    /// Iterates through each blob (file) in the folder, counts the number of instances of search term in the file,
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

    Metoda GetFolderPath zwraca ścieżkę do folderu, który wskazuje element dataset i metoda GetFileName zwraca nazwę obiektu blob/pliku, który wskazuje element dataset. Jeśli użytkownik havefolderPath definiuje, korzystając ze zmiennych, takich jak {Year}, {Month}, {Day} itd., metoda zwraca ciąg w postaci, w jakiej jest bez zamianę wartości środowiska uruchomieniowego. Zobacz [dostępu właściwości rozszerzone](#access-extended-properties) sekcji, aby uzyskać szczegółowe informacje dotyczące uzyskiwania dostępu do SliceStart, SliceEnd itp.    

    ```JSON
    "name": "InputDataset",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "fileName": "file.txt",
            "folderPath": "adftutorial/inputfolder/",
    ```

    Metoda Calculate oblicza liczbę wystąpień — słowo kluczowe pliki wejściowe (obiekty BLOB w folderze) firmy Microsoft. Wyszukiwany termin ("Microsoft") jest ustalony w kodzie.
10. Skompiluj projekt. Kliknij przycisk **kompilacji** w menu, kliknij **Kompiluj rozwiązanie**.

    > [!IMPORTANT]
    > Ustaw 4.5.2 wersję programu .NET Framework jako platforma docelowa projektu: kliknij prawym przyciskiem myszy projekt, a następnie kliknij przycisk **właściwości** można ustawić platformę docelową. Fabryki danych nie obsługuje niestandardowych działań skompilowany .NET Framework w wersji nowszej niż 4.5.2.

11. Uruchom **Eksploratora Windows**, a następnie przejdź do **bin\debug** lub **bin\release** folder, w zależności od typu kompilacji.
12. Utwórz plik zip **MyDotNetActivity.zip** zawierający wszystkie pliki binarne w <project folder>\bin\Debug folderu. Obejmują **MyDotNetActivity.pdb** pliku, tak aby uzyskać dodatkowe szczegóły, takie jak numer wiersza w kodzie źródłowym, która spowodowała problem, jeśli wystąpił błąd. 

    > [!IMPORTANT]
    > Wszystkie pliki w archiwum ZIP działania niestandardowego muszą znajdować się na **najwyższym poziomie**, bez podfolderów.

    ![Binarne pliki wyjściowe](./media/data-factory-use-custom-activities/Binaries.png)
14. Tworzenie kontenera obiektów blob o nazwie **customactivitycontainer** Jeśli jeszcze nie istnieje. 
15. Przekaż MyDotNetActivity.zip jako obiekt blob do customactivitycontainer w **ogólnego przeznaczenia** magazynu obiektów blob platformy Azure (gorącą nie/chłodnej magazynu obiektów Blob), który odwołuje się do AzureStorageLinkedService.  

> [!IMPORTANT]
> Jeśli dodać ten projekt działania .NET do rozwiązania w programie Visual Studio, zawierający projekt fabryki danych i Dodaj odwołanie do projektu działania .NET z projektu aplikacji fabryki danych nie należy przeprowadzić ostatnie dwa kroki ręcznego tworzenia pliku zip i przekazać go do magazynu obiektów blob platformy Azure ogólnego przeznaczenia. Podczas publikowania jednostek fabryki danych przy użyciu programu Visual Studio, te kroki automatycznie są wykonywane przez proces publikowania. Aby uzyskać więcej informacji, zobacz [fabryki danych projektu programu Visual Studio](#data-factory-project-in-visual-studio) sekcji.

## <a name="create-a-pipeline-with-custom-activity"></a>Utworzyć potok z działań niestandardowych
Utworzono niestandardowe działania i przekazać do kontenera obiektów blob w pliku zip z plików binarnych **ogólnego przeznaczenia** konta magazynu Azure. W tej sekcji utworzysz fabryki danych Azure z potok, który korzysta z działań niestandardowych.

Wejściowy zestaw danych dla działań niestandardowych reprezentuje obiektów blob (pliki) w folderze customactivityinput adftutorial kontenera w magazynie obiektów blob. Zestaw danych wyjściowych dla działania reprezentuje obiekty BLOB danych wyjściowych w folderze customactivityoutput adftutorial kontenera w magazynie obiektów blob.

Utwórz **plik.txt** pliku o następującej zawartości i przekaż go do **customactivityinput** folderu **adftutorial** kontenera. Tworzenie kontenera adftutorial, jeśli już istnieje. 

```
test custom activity Microsoft test custom activity Microsoft
```

Folder wejściowy odpowiada wycinka w fabryce danych Azure, nawet jeśli folder zawiera dwa lub więcej plików. Każdy wycinek jest przetwarzany przez potok, działania niestandardowego iterację wszystkich obiektów blob w folderze wejściowych dla tego wycinka.

Zobaczysz, że jeden wyjściowy plik z folderu adftutorial\customactivityoutput z jednego lub więcej wierszy (tak samo jak liczbę obiektów blob w folderze wprowadzania):

```
2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2016-11-16-00/file.txt.
```


Poniżej przedstawiono kroki, które należy wykonać w tej sekcji:

1. Utwórz **fabryki danych**.
2. Utwórz **połączone usługi** dla maszyn wirtualnych w puli partii zadań Azure uruchamianego działań niestandardowych i usługi Azure Storage, który zawiera obiekty BLOB wejścia/wyjścia.
3. Utwórz dane wejściowe i wyjściowe **zestawów danych** reprezentujące dane wejściowe i wyjściowe działania niestandardowego.
4. Utwórz **potoku** używający działania niestandardowego.

> [!NOTE]
> Utwórz **plik.txt** i przekaż go do kontenera obiektów blob, jeśli jeszcze tego nie zrobiono. Zobacz instrukcje w poprzedniej sekcji.   

### <a name="step-1-create-the-data-factory"></a>Krok 1: Tworzenie fabryki danych
1. Po zalogowaniu się do portalu Azure, wykonaj następujące czynności:
   1. Kliknij przycisk **Utwórz zasób** w menu po lewej stronie.
   2. Kliknij przycisk **dane i analiza** w **nowy** bloku.
   3. Kliknij przycisk **Fabryka danych** w bloku **Analiza danych**.
   
    ![Nowe menu fabryki danych Azure](media/data-factory-use-custom-activities/new-azure-data-factory-menu.png)
2. W **nowa fabryka danych** bloku, wprowadź **CustomActivityFactory** dla nazwy. Nazwa fabryki danych Azure musi być globalnie unikatowa. Jeśli zostanie wyświetlony błąd: **nazwa fabryki danych "CustomActivityFactory" nie jest dostępna**, Zmień nazwę fabryki danych (na przykład **yournameCustomActivityFactory**), a następnie spróbuj ponownie utworzyć.

    ![Nowy blok fabryki danych Azure](media/data-factory-use-custom-activities/new-azure-data-factory-blade.png)
3. Kliknij przycisk **Nazwa grupy zasobów**i wybierz istniejącą grupę zasobów lub Utwórz grupę zasobów.
4. Sprawdź, czy używasz właściwego **subskrypcji** i **region** miejscu fabryki danych ma zostać utworzony.
5. Kliknij przycisk **Utwórz** w bloku **Nowa fabryka danych**.
6. Zobacz tworzony w fabryce danych **pulpitu nawigacyjnego** portalu Azure.
7. Po fabryki danych został utworzony pomyślnie, wyświetlona bloku fabryki danych przedstawiona zawartość fabryki danych.
    
    ![Blok Fabryka danych](media/data-factory-use-custom-activities/data-factory-blade.png)

### <a name="step-2-create-linked-services"></a>Krok 2: Tworzenie usługi połączonej
Połączone usługi łączą magazyny danych lub usługi obliczeniowe z fabryką danych Azure. W tym kroku możesz połączyć Twoje konto usługi Azure Storage i konta usługi partia zadań Azure z fabryką danych.

#### <a name="create-azure-storage-linked-service"></a>Tworzenie połączonej usługi Azure Storage
1. Kliknij przycisk **tworzenie i wdrażanie** Kafelek na **FABRYKI danych** bloku **CustomActivityFactory**. Pojawi się Edytor fabryki danych.
2. Kliknij przycisk **nowy magazyn danych** polecenie Pasek i wybierz polecenie **magazynu Azure**. Powinien zostać wyświetlony skrypt JSON do tworzenia połączonej usługi Azure Storage w edytorze.
    
    ![Nowy magazyn danych - Azure Storage](media/data-factory-use-custom-activities/new-data-store-menu.png)
3. Zastąp `<accountname>` z nazwą konta magazynu Azure i `<accountkey>` z kluczem dostępu konta magazynu platformy Azure. Informacje na temat pobierania klucza dostępu do magazynu znajdują się w artykule [Wyświetlanie, kopiowanie i ponowne generowanie kluczy dostępu do magazynu](../../storage/common/storage-create-storage-account.md#manage-your-storage-account).

    ![Usługa Azure Storage zbędne usługi](media/data-factory-use-custom-activities/azure-storage-linked-service.png)
4. Kliknij przycisk **Wdróż** na pasku poleceń, aby wdrożyć połączoną usługę.

#### <a name="create-azure-batch-linked-service"></a>Tworzenie usługi partia zadań Azure połączone
1. W edytorze fabryki danych, kliknij polecenie **... Więcej** na pasku poleceń, kliknij przycisk **nowych obliczeń**, a następnie wybierz **partii zadań Azure** z menu.

    ![Nowe obliczeń - partii zadań Azure](media/data-factory-use-custom-activities/new-azure-compute-batch.png)
2. Do skryptu JSON, należy wprowadzić następujące zmiany:

   1. Określ nazwę konta partii zadań Azure **accountName** właściwości. **Adres URL** z **bloku konta usługi partia zadań Azure** znajduje się w następującym formacie: `http://accountname.region.batch.azure.com`. Dla **batchUri** właściwości w formacie JSON, musisz usunąć `accountname.` z adresu URL i użycia `accountname` dla `accountName` właściwości JSON.
   2. Określ klucz konta partii zadań Azure **accessKey** właściwości.
   3. Określ nazwę puli został utworzony jako część wymagania wstępne dotyczące **poolName** właściwości. Można również określić identyfikator puli zamiast nazwy puli.
   4. Określ identyfikator URI usługi partia zadań Azure dla **batchUri** właściwości. Przykład: `https://westus.batch.azure.com`.  
   5. Określ **AzureStorageLinkedService** dla **linkedServiceName** właściwości.

        ```json
        {
         "name": "AzureBatchLinkedService",
         "properties": {
           "type": "AzureBatch",
           "typeProperties": {
             "accountName": "myazurebatchaccount",
             "batchUri": "https://westus.batch.azure.com",
             "accessKey": "<yourbatchaccountkey>",
             "poolName": "myazurebatchpool",
             "linkedServiceName": "AzureStorageLinkedService"
           }
         }
        }
        ```

       Aby uzyskać **poolName** właściwości, można również określić identyfikator puli zamiast nazwy puli.

    

### <a name="step-3-create-datasets"></a>Krok 3: Tworzenie zestawów danych
W tym kroku możesz utworzyć zestawy danych do reprezentowania danych wejściowych i wyjściowych.

#### <a name="create-input-dataset"></a>Tworzenie wejściowego zestawu danych
1. W **edytorze** fabryki danych kliknij kolejno polecenia  **Więcej** na pasku poleceń, kliknij przycisk **nowy zestaw danych**, a następnie wybierz **magazynu obiektów Blob Azure** z menu rozwijanego.
2. Zastąp dane JSON w okienku po prawej stronie następujący fragment kodu JSON:

    ```json
    {
     "name": "InputDataset",
     "properties": {
         "type": "AzureBlob",
         "linkedServiceName": "AzureStorageLinkedService",
         "typeProperties": {
             "folderPath": "adftutorial/customactivityinput/",
             "format": {
                 "type": "TextFormat"
             }
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

   Utworzyć potok w dalszej części tego przewodnika, czas rozpoczęcia: 2016-11-16T00:00:00Z i na końcu czasu: 2016-11-16T05:00:00Z. Jest to zaplanowane co godzinę, wyprodukować danych co pięć wycinków wejścia/wyjścia (między **00**: 00:00 -> **05**: 00:00).

   **Częstotliwość** i **interwał** dla zestawu danych wejściowych jest ustawiony na **godzina** i **1**, co oznacza, że wejściowy wycinek jest dostępny co godzinę. W tym przykładzie jest tego samego pliku (plik.txt) w intputfolder.

   Poniżej przedstawiono godziny rozpoczęcia dla każdego wycinka, która jest reprezentowana przez SliceStart zmienna w powyższym fragment kodu JSON.
3. Kliknij przycisk **Wdróż** na pasku narzędzi, aby utworzyć i wdrożyć **InputDataset**. Upewnij się, że na pasku tytułu w edytorze wyświetlany jest komunikat **TABELA ZOSTAŁA UTWORZONA POMYŚLNIE**.

#### <a name="create-an-output-dataset"></a>Tworzenie wyjściowego zestawu danych
1. W **Edytor fabryki danych**, kliknij przycisk **... Więcej** na pasku poleceń, kliknij przycisk **nowy zestaw danych**, a następnie wybierz **magazynu obiektów Blob Azure**.
2. Zastąp skryptu JSON, w okienku po prawej stronie poniższy skrypt JSON:

    ```JSON
    {
        "name": "OutputDataset",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
            "typeProperties": {
                "fileName": "{slice}.txt",
                "folderPath": "adftutorial/customactivityoutput/",
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

     Lokalizacja danych wyjściowych jest **adftutorial/customactivityoutput/** i nazwa pliku wyjściowego jest RRRR MM-dd-HH.txt w przypadku RRRR MM-dd gg rok, miesiąc, Data i godzina tworzonym wycinka. Zobacz [dokumentacja dla deweloperów] [ adf-developer-reference] szczegółowe informacje.

    Obiekt blob/plik wyjściowy jest generowany dla każdego wejściowego wycinka. Oto, jak dla każdego wycinka nosi nazwę pliku wyjściowego. Wszystkie pliki wyjściowe są generowane w jednym folderze wyjściowym: **adftutorial\customactivityoutput**.

   | Wycinek | Godzina rozpoczęcia | Plik wyjściowy |
   |:--- |:--- |:--- |
   | 1 |2016-11-16T00:00:00 |2016-11-16-00.txt |
   | 2 |2016-11-16T01:00:00 |2016-11-16-01.txt |
   | 3 |2016-11-16T02:00:00 |2016-11-16-02.txt |
   | 4 |2016-11-16T03:00:00 |2016-11-16-03.txt |
   | 5 |2016-11-16T04:00:00 |2016-11-16-04.txt |

    Należy pamiętać, że wszystkie pliki w folderze wejściowych część wycinek z godziny rozpoczęcia wymienionych powyżej. Podczas przetwarzania tego wycinka działania niestandardowego skanowania za pomocą każdego pliku i tworzy wiersz w pliku danych wyjściowych z liczbą wystąpień terminu wyszukiwania ("Microsoft"). Jeśli istnieją trzy pliki w inputfolder, istnieją trzy wiersze w pliku wyjściowym dla każdego wycinka co godzinę: 2016-11-16-00.txt 2016-11-16:01:00:00.txt itp.
3. Aby wdrożyć **OutputDataset**, kliknij przycisk **Wdróż** na pasku poleceń.

### <a name="create-and-run-a-pipeline-that-uses-the-custom-activity"></a>Tworzenie i uruchamianie potok, który korzysta z działań niestandardowych
1. W edytorze fabryki danych, kliknij polecenie **... Więcej**, a następnie wybierz **nowy potok** na pasku poleceń. 
2. Zastąp dane JSON w okienku po prawej stronie poniższy skrypt JSON:

    ```JSON
    {
      "name": "ADFTutorialPipelineCustom",
      "properties": {
        "description": "Use custom activity",
        "activities": [
          {
            "Name": "MyDotNetActivity",
            "Type": "DotNetActivity",
            "Inputs": [
              {
                "Name": "InputDataset"
              }
            ],
            "Outputs": [
              {
                "Name": "OutputDataset"
              }
            ],
            "LinkedServiceName": "AzureBatchLinkedService",
            "typeProperties": {
              "AssemblyName": "MyDotNetActivity.dll",
              "EntryPoint": "MyDotNetActivityNS.MyDotNetActivity",
              "PackageLinkedService": "AzureStorageLinkedService",
              "PackageFile": "customactivitycontainer/MyDotNetActivity.zip",
              "extendedProperties": {
                "SliceStart": "$$Text.Format('{0:yyyyMMddHH-mm}', Time.AddMinutes(SliceStart, 0))"
              }
            },
            "Policy": {
              "Concurrency": 2,
              "ExecutionPriorityOrder": "OldestFirst",
              "Retry": 3,
              "Timeout": "00:30:00",
              "Delay": "00:00:00"
            }
          }
        ],
        "start": "2016-11-16T00:00:00Z",
        "end": "2016-11-16T05:00:00Z",
        "isPaused": false
      }
    }
    ```

    Pamiętaj o następujących kwestiach:

   * **Współbieżność** ustawiono **2** tak, aby dwa wycinków są przetwarzane równolegle przez 2 maszyny wirtualne w puli partii zadań Azure.
   * Brak jednego działania w sekcji działania i jest typu: **DotNetActivity**.
   * **AssemblyName** ma ustawioną nazwę biblioteki DLL: **MyDotnetActivity.dll**.
   * **Punkt wejścia** ustawiono **MyDotNetActivityNS.MyDotNetActivity**.
   * **PackageLinkedService** ustawiono **AzureStorageLinkedService** wskazującego do magazynu obiektów blob, który zawiera plik zip działania niestandardowego. Jeśli używasz różnych kont usługi Azure Storage dla wejścia/wyjścia plików i działania niestandardowego pliku zip, utworzysz innego połączoną usługą magazynu Azure. W tym artykule przyjęto założenie, że używasz tego samego konta magazynu Azure.
   * **PackageFile** ustawiono **customactivitycontainer/MyDotNetActivity.zip**. Jest w formacie: containerforthezip/nameofthezip.zip.
   * Trwa to niestandardowe działanie **InputDataset** jako dane wejściowe i **OutputDataset** jako dane wyjściowe.
   * Wskazuje właściwość linkedServiceName działania niestandardowego **AzureBatchLinkedService**, który informuje fabryki danych Azure, wymagającym działań niestandardowych do uruchamiania na maszynach wirtualnych Azure partii.
   * **isPaused** właściwość jest ustawiona na **false** domyślnie. Potok uruchamia natychmiast w tym przykładzie, ponieważ wycinków rozpoczęcia w przeszłości. Tę właściwość można ustawić wartość true, aby wstrzymać potoku i go ustawić ponownie na wartość false, aby uruchomić ponownie.
   * **Start** czasu i **zakończenia** czasy są **pięć** godziny od siebie i wycinki są tworzone co godzinę, więc pięć wycinków są produkowane przez potok.
3. Aby wdrożyć potok, kliknij przycisk **Wdróż** na pasku poleceń.

### <a name="monitor-the-pipeline"></a>Monitorowanie potoku
1. W bloku fabryki danych w portalu Azure, kliknij **Diagram**.

    ![Kafelek Diagram](./media/data-factory-use-custom-activities/DataFactoryBlade.png)
2. W widoku diagramu kliknij przycisk OutputDataset.

    ![Widok diagramu](./media/data-factory-use-custom-activities/diagram.png)
3. Powinny być widoczne czy wycinków pięć dane wyjściowe są w stanie gotowe. Jeśli nie są w stanie gotowe, nie została opracowana jeszcze. 

   ![Wycinki danych wyjściowych](./media/data-factory-use-custom-activities/OutputSlices.png)
4. Sprawdź, czy pliki wyjściowe są generowane w magazynie obiektów blob w **adftutorial** kontenera.

   ![dane wyjściowe z działań niestandardowych][image-data-factory-ouput-from-custom-activity]
5. Po otwarciu pliku wyjściowego, powinny być widoczne dane wyjściowe podobne do następujących danych wyjściowych:

    ```
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2016-11-16-00/file.txt.
    ```
6. Użyj [portalu Azure] [ azure-preview-portal] lub poleceń cmdlet programu Azure PowerShell do monitorowania z fabryki danych, potoki i zestawów danych. Można wyświetlić wiadomości z **ActivityLogger** w kodzie niestandardowe działania w dziennikach (w szczególności 0.log użytkownika), które można pobrać z portalu lub przy użyciu poleceń cmdlet.

   ![Pobierz dzienniki z działań niestandardowych][image-data-factory-download-logs-from-custom-activity]

Zobacz [monitorować i zarządzać potoki](data-factory-monitor-manage-pipelines.md) szczegółowy opis kroków monitorowania zestawy danych i potoki.      

## <a name="data-factory-project-in-visual-studio"></a>Projekt fabryki danych w programie Visual Studio  
Można tworzyć i publikować jednostek fabryki danych przy użyciu programu Visual Studio, a za pomocą portalu Azure. Szczegółowe informacje na temat tworzenia i publikowania jednostek fabryki danych przy użyciu programu Visual Studio, zobacz [kompilacji swój pierwszy potok, za pomocą programu Visual Studio](data-factory-build-your-first-pipeline-using-vs.md) i [skopiować dane z obiektu Blob Azure do usługi Azure SQL](data-factory-copy-activity-tutorial-using-visual-studio.md) artykułów.

Jeśli tworzysz projekt fabryki danych w programie Visual Studio, należy wykonać następujące dodatkowe czynności:
 
1. Dodaj fabryki danych projektu do rozwiązania Visual Studio, które zawiera projekt działania niestandardowego. 
2. Dodaj odwołanie do projektu działania .NET z projektu fabryki danych. Kliknij prawym przyciskiem myszy projekt w fabryce danych, wskaż pozycję **Dodaj**, a następnie kliknij przycisk **odwołania**. 
3. W **Dodaj odwołanie** okno dialogowe, wybierz opcję **MyDotNetActivity** projektu, a następnie kliknij przycisk **OK**.
4. Tworzenie i publikowanie rozwiązania.

    > [!IMPORTANT]
    > Podczas publikowania jednostek fabryki danych pliku zip jest automatycznie tworzony i jest przekazywany do kontenera obiektów blob: customactivitycontainer. Jeśli kontenera obiektów blob nie istnieje, jest on automatycznie tworzony zbyt.  


## <a name="data-factory-and-batch-integration"></a>Integracja z fabryki danych i usługi partia zadań
Usługi fabryka danych tworzy zadanie w partii zadań Azure o nazwie: **adf poolname: xxx zadania**. Kliknij przycisk **zadania** z menu po lewej stronie. 

![Fabryka danych Azure - zadań wsadowych](media/data-factory-use-custom-activities/data-factory-batch-jobs.png)

Zadanie jest tworzone przy każdym uruchomieniu działania wycinek. W przypadku pięć wycinków gotowy do przetwarzania pięć zadań są tworzone w ramach tego zadania. Jeśli istnieje wiele węzłów obliczeniowych w puli partii, co najmniej dwa wycinków można uruchomić równolegle. Jeśli maksymalny zadań na węzeł obliczeń jest ustawiona na > 1, może istnieć więcej niż jeden wycinek uruchomionych na tej samej mocy obliczeniowej.

![Fabryka danych Azure - zadań zadania wsadowego](media/data-factory-use-custom-activities/data-factory-batch-job-tasks.png)

Na poniższym diagramie przedstawiono związek między fabryki danych Azure i partii zadań.

![Fabryka danych & partii](./media/data-factory-use-custom-activities/DataFactoryAndBatch.png)

## <a name="troubleshoot-failures"></a>Rozwiązywanie problemów z błędami
Rozwiązywanie problemów z składa się z kilku podstawowych techniki:

1. Jeśli zostanie wyświetlony następujący błąd, używasz magazynu obiektów blob aktywny/chłodnej zamiast przy użyciu magazynu obiektów blob platformy Azure ogólnego przeznaczenia. Przekaż plik zip do **ogólnego przeznaczenia konta magazynu Azure**. 
 
    ```
    Error in Activity: Job encountered scheduling error. Code: BlobDownloadMiscError Category: ServerError Message: Miscellaneous error encountered while downloading one of the specified Azure Blob(s).
    ``` 
2. Jeśli zostanie wyświetlony następujący błąd, upewnij się, że nazwa klasy w pliku CS odpowiada nazwa określona dla **punktu wejścia** właściwości w potoku JSON. W tym przewodnikiem jest nazwa klasy: MyDotNetActivity i punktu wejścia w formacie JSON: MyDotNetActivityNS. **MyDotNetActivity**.

    ```
    MyDotNetActivity assembly does not exist or doesn't implement the type Microsoft.DataFactories.Runtime.IDotNetActivity properly
    ```

   Jeśli nazwy są zgodne, upewnij się, że wszystkie pliki binarne znajdują się w **folderu głównego** pliku zip. Oznacza to podczas otwierania pliku zip, powinny być widoczne wszystkie pliki w folderze głównym, a nie wszystkie podfoldery.   
3. Jeśli wycinek wejściowy nie jest ustawiony na **gotowe**, potwierdź poprawność struktury folderów wejściowych i **plik.txt** istnieje w folderze wejściowego.
3. W **Execute** metody działania niestandardowego, użyj **IActivityLogger** obiektu do logowania się informacje ułatwiające rozwiązywanie problemów. Zarejestrowane komunikaty, wyświetlani w plikach dziennika użytkownika (co najmniej jeden plik o nazwie: 0.log użytkownika, 1.log użytkownika, użytkownik 2.log itp.).

   W **OutputDataset** bloku, kliknij wycinek, aby wyświetlić **WYCINKA danych** bloku dla tego wycinka. Zostanie wyświetlony **uruchomień działania** dla tego wycinka. Powinny pojawić się jeden uruchamiania dla wycinka działania. Jeśli klikniesz przycisk uruchamiania na pasku poleceń, można uruchomić inne działanie Uruchom dla tej samej wycinka.

   Po kliknięciu przycisku uruchamiania działania, zobacz **szczegóły uruchomienia działania** bloku zawierającego listę plików dziennika. Zobaczysz zarejestrowane komunikaty, w pliku user_0.log. Gdy wystąpi błąd, zostanie wyświetlony trzech uruchomień działania, ponieważ liczby ponownych prób jest ustawiona na 3 w potoku/aktywność JSON. Po kliknięciu przycisku Uruchom działania, zobaczysz plików dziennika, które można przejrzeć, aby rozwiązać problem.

   Na liście plików dziennika, kliknij **0.log użytkownika**. W prawym okienku są wyniki za pomocą **IActivityLogger.Write** metody. Jeśli nie widzisz wszystkich wiadomości, sprawdź, czy istnieje więcej plików dziennika o nazwie: user_1.log, user_2.log itp. W przeciwnym razie kod mogła zakończyć się niepowodzeniem po ostatniej rejestrowany komunikat.

   Ponadto sprawdź **0.log systemu** za wszelkie komunikaty o błędach systemu i wyjątki.
4. Obejmują **PDB** plików w pliku zip, aby szczegóły błędu mają informacje takie jak **stosu wywołań** po wystąpieniu błędu.
5. Wszystkie pliki w archiwum ZIP działania niestandardowego muszą znajdować się na **najwyższym poziomie**, bez podfolderów.
6. Upewnij się, że **assemblyName** (MyDotNetActivity.dll), **punktu wejścia**(MyDotNetActivityNS.MyDotNetActivity), **packageFile** (customactivitycontainer/MyDotNetActivity.zip) i **packageLinkedService** (powinny wskazywać **ogólnego przeznaczenia**magazynu obiektów blob platformy Azure, który zawiera plik zip) są ustawione prawidłowe wartości.
7. Jeśli naprawiono błąd i chcesz przetworzyć wycinek ponownie, kliknij prawym przyciskiem wycinek w bloku **OutputDataset** i kliknij polecenie **Uruchom**.
8. Jeśli zostanie wyświetlony następujący błąd, są za pomocą pakietu magazynu Azure w wersji > 4.3.0. Uruchamianie usługi fabryka danych wymaga wersji 4.3 WindowsAzure.Storage. Zobacz [izolacji elementu Appdomain](#appdomain-isolation) sekcji OBEJŚCIE Jeśli należy użyć nowszej wersji zestawu Azure Storage. 

    ```
    Error in Activity: Unknown error in module: System.Reflection.TargetInvocationException: Exception has been thrown by the target of an invocation. ---> System.TypeLoadException: Could not load type 'Microsoft.WindowsAzure.Storage.Blob.CloudBlob' from assembly 'Microsoft.WindowsAzure.Storage, Version=4.3.0.0, Culture=neutral, 
    ```

    Jeśli używasz 4.3.0 wersji pakietu usługi Azure Storage, Usuń istniejące odwołanie do pakietu wersji > 4.3.0 usługi Azure Storage. Następnie uruchom następujące polecenie w konsoli Menedżera pakietów NuGet. 

    ```PowerShell
    Install-Package WindowsAzure.Storage -Version 4.3.0
    ```

    Skompiluj projekt. Usuń zespół Azure.Storage wersji > 4.3.0 z folderu bin\Debug. Utwórz plik zip zawierający pliki binarne i pliku PDB. Zastąpić starego pliku zip to w kontenerze obiektów blob (customactivitycontainer). Uruchom ponownie wycinków, których nie powiodła się (kliknij prawym przyciskiem myszy wycinek i kliknij przycisk Uruchom).   
8. Działania niestandardowe nie używa **app.config** plików z pakietu. W związku z tym jeśli kod odczytuje wszelkie parametry połączenia z pliku konfiguracji, działa w czasie wykonywania. Najlepszym rozwiązaniem w przypadku przy użyciu partii zadań Azure do przechowywania wszelkich kluczy tajnych w **Azure KeyVault**, użyj nazwy głównej usługi oparte na certyfikatach, aby chronić **keyvault**i rozpowszechniania certyfikatów do puli partii zadań Azure. Niestandardowe działanie .NET będzie miało w takiej sytuacji dostęp do danych poufnych z magazynu KeyVault w czasie uruchomienia. To rozwiązanie jest ogólny rozwiązania i można skalować do dowolnego typu klucza tajnego, nie tylko w parametrach połączenia.

   Istnieje obejście łatwiejsze (ale nie najlepiej): można utworzyć **Azure SQL połączona usługa** z ustawień parametrów połączenia, utworzyć zestawu danych, który używa połączonej usługi, a powiązane zestawu danych jako fikcyjny wejściowy zestaw danych do działania niestandardowego .NET. Następnie dostępne parametry połączenia połączonej usługi w kodzie działania niestandardowego.  

## <a name="update-custom-activity"></a>Aktualizowanie działań niestandardowych
Po zaktualizowaniu kod dla działań niestandardowych skompiluj go i przekazać plik zip, który zawiera nowe pliki binarne do magazynu obiektów blob.

## <a name="appdomain-isolation"></a>Izolacja domeny aplikacji
Zobacz [Cross próbki AppDomain](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/CrossAppDomainDotNetActivitySample) który pokazuje, jak utworzyć niestandardowe działanie, które nie jest ograniczona do wersji zestawu używanych przez uruchamianie fabryki danych (przykład: WindowsAzure.Storage v4.3.0 Newtonsoft.Json v6.0.x, itp.).

## <a name="access-extended-properties"></a>Dostęp do właściwości rozszerzone
Można zadeklarować właściwości rozszerzone w działaniu JSON, jak pokazano w poniższym przykładzie:

```JSON
"typeProperties": {
  "AssemblyName": "MyDotNetActivity.dll",
  "EntryPoint": "MyDotNetActivityNS.MyDotNetActivity",
  "PackageLinkedService": "AzureStorageLinkedService",
  "PackageFile": "customactivitycontainer/MyDotNetActivity.zip",
  "extendedProperties": {
    "SliceStart": "$$Text.Format('{0:yyyyMMddHH-mm}', Time.AddMinutes(SliceStart, 0))",
    "DataFactoryName": "CustomActivityFactory"
  }
},
```


W tym przykładzie są dwie właściwości rozszerzone: **SliceStart** i **DataFactoryName**. Wartość SliceStart opiera się na SliceStart zmiennej systemowej. Zobacz [zmienne systemowe](data-factory-functions-variables.md) listę zmiennych obsługiwany system. Wartość DataFactoryName jest ustalony na CustomActivityFactory.

Aby przejść do nich rozszerzone właściwości w **Execute** metody, użyj kod podobny do następującego kodu:

```csharp
// to get extended properties (for example: SliceStart)
DotNetActivity dotNetActivity = (DotNetActivity)activity.TypeProperties;
string sliceStartString = dotNetActivity.ExtendedProperties["SliceStart"];

// to log all extended properties                               
IDictionary<string, string> extendedProperties = dotNetActivity.ExtendedProperties;
logger.Write("Logging extended properties if any...");
foreach (KeyValuePair<string, string> entry in extendedProperties)
{
    logger.Write("<key:{0}> <value:{1}>", entry.Key, entry.Value);
}
```

## <a name="auto-scaling-of-azure-batch"></a>Automatyczne skalowanie partii zadań Azure
Można również utworzyć puli partii zadań Azure z **skalowania automatycznego** funkcji. Na przykład można utworzyć puli partii zadań azure 0 dedykowanych maszyn wirtualnych i formuły skalowania automatycznego na podstawie liczby oczekujących zadań. 

Przykładowa formuła tutaj realizuje następujące zachowanie: podczas tworzenia puli, rozpoczyna się od 1 maszyny Wirtualnej. Metryka $PendingTasks definiuje liczbę zadań uruchomiona + aktywny (w kolejce) stanu.  Formuła znajduje średnią liczbę oczekujących zadań w ciągu ostatnich 180 sekund i odpowiednio ustawia TargetDedicated. Gwarantuje, że TargetDedicated nigdy nie wykracza poza 25 maszyn wirtualnych. Tak, jak nowe zadania są przesyłane, automatycznie zwiększa rozmiar puli i jako zakończenie zadania, maszyn wirtualnych stają się wolnego jeden po drugim i skalowanie automatyczne zmniejsza tych maszyn wirtualnych. startingNumberOfVMs i maxNumberofVMs można dostosować do własnych potrzeb.

Formuła skalowania automatycznego:

``` 
startingNumberOfVMs = 1;
maxNumberofVMs = 25;
pendingTaskSamplePercent = $PendingTasks.GetSamplePercent(180 * TimeInterval_Second);
pendingTaskSamples = pendingTaskSamplePercent < 70 ? startingNumberOfVMs : avg($PendingTasks.GetSample(180 * TimeInterval_Second));
$TargetDedicated=min(maxNumberofVMs,pendingTaskSamples);
```

Zobacz [automatycznie skali obliczeniowe węzłów w puli partii zadań Azure](../../batch/batch-automatic-scaling.md) szczegółowe informacje.

Jeśli w puli jest przy użyciu domyślnego [autoScaleEvaluationInterval](https://msdn.microsoft.com/library/azure/dn820173.aspx), usługa partia zadań może zająć 15 do 30 minut, aby przygotować maszyny Wirtualnej przed uruchomieniem działania niestandardowego.  Jeśli pula używa innego autoScaleEvaluationInterval, usługa partia zadań może zająć autoScaleEvaluationInterval + 10 minut.


## <a name="create-a-custom-activity-by-using-net-sdk"></a>Tworzenie niestandardowego działania przy użyciu zestawu .NET SDK
W przewodniku, w tym artykule Tworzenie fabryki danych z potok, który używa działań niestandardowych za pomocą portalu Azure. Poniższy kod przedstawia sposób tworzenia fabryki danych przy użyciu zestawu .NET SDK zamiast tego. Można znaleźć więcej szczegółów na temat przy użyciu zestawu SDK do programowego tworzenia potoków w [utworzyć potok z działania kopiowania przy użyciu interfejsu API platformy .NET](data-factory-copy-activity-tutorial-using-dotnet-api.md) artykułu. 

```csharp
using System;
using System.Configuration;
using System.Collections.ObjectModel;
using System.Threading;
using System.Threading.Tasks;

using Microsoft.Azure;
using Microsoft.Azure.Management.DataFactories;
using Microsoft.Azure.Management.DataFactories.Models;
using Microsoft.Azure.Management.DataFactories.Common.Models;

using Microsoft.IdentityModel.Clients.ActiveDirectory;
using System.Collections.Generic;

namespace DataFactoryAPITestApp
{
    class Program
    {
        static void Main(string[] args)
        {
            // create data factory management client

            // TODO: replace ADFTutorialResourceGroup with the name of your resource group.
            string resourceGroupName = "ADFTutorialResourceGroup";

            // TODO: replace APITutorialFactory with a name that is globally unique. For example: APITutorialFactory04212017
            string dataFactoryName = "APITutorialFactory";

            TokenCloudCredentials aadTokenCredentials = new TokenCloudCredentials(
                ConfigurationManager.AppSettings["SubscriptionId"],
                GetAuthorizationHeader().Result);

            Uri resourceManagerUri = new Uri(ConfigurationManager.AppSettings["ResourceManagerEndpoint"]);

            DataFactoryManagementClient client = new DataFactoryManagementClient(aadTokenCredentials, resourceManagerUri);

            Console.WriteLine("Creating a data factory");
            client.DataFactories.CreateOrUpdate(resourceGroupName,
                new DataFactoryCreateOrUpdateParameters()
                {
                    DataFactory = new DataFactory()
                    {
                        Name = dataFactoryName,
                        Location = "westus",
                        Properties = new DataFactoryProperties()
                    }
                }
            );

            // create a linked service for input data store: Azure Storage
            Console.WriteLine("Creating Azure Storage linked service");
            client.LinkedServices.CreateOrUpdate(resourceGroupName, dataFactoryName,
                new LinkedServiceCreateOrUpdateParameters()
                {
                    LinkedService = new LinkedService()
                    {
                        Name = "AzureStorageLinkedService",
                        Properties = new LinkedServiceProperties
                        (
                            // TODO: Replace <accountname> and <accountkey> with name and key of your Azure Storage account.
                            new AzureStorageLinkedService("DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>")
                        )
                    }
                }
            );

            // create a linked service for output data store: Azure SQL Database
            Console.WriteLine("Creating Azure Batch linked service");
            client.LinkedServices.CreateOrUpdate(resourceGroupName, dataFactoryName,
                new LinkedServiceCreateOrUpdateParameters()
                {
                    LinkedService = new LinkedService()
                    {
                        Name = "AzureBatchLinkedService",
                        Properties = new LinkedServiceProperties
                        (
                            // TODO: replace <batchaccountname> and <yourbatchaccountkey> with name and key of your Azure Batch account
                            new AzureBatchLinkedService("<batchaccountname>", "https://westus.batch.azure.com", "<yourbatchaccountkey>", "myazurebatchpool", "AzureStorageLinkedService")
                        )
                    }
                }
            );

            // create input and output datasets
            Console.WriteLine("Creating input and output datasets");
            string Dataset_Source = "InputDataset";
            string Dataset_Destination = "OutputDataset";

            Console.WriteLine("Creating input dataset of type: Azure Blob");
            client.Datasets.CreateOrUpdate(resourceGroupName, dataFactoryName,

                new DatasetCreateOrUpdateParameters()
                {
                    Dataset = new Dataset()
                    {
                        Name = Dataset_Source,
                        Properties = new DatasetProperties()
                        {
                            LinkedServiceName = "AzureStorageLinkedService",
                            TypeProperties = new AzureBlobDataset()
                            {
                                FolderPath = "adftutorial/customactivityinput/",
                                Format = new TextFormat()
                            },
                            External = true,
                            Availability = new Availability()
                            {
                                Frequency = SchedulePeriod.Hour,
                                Interval = 1,
                            },

                            Policy = new Policy() { }
                        }
                    }
                });

            Console.WriteLine("Creating output dataset of type: Azure Blob");
            client.Datasets.CreateOrUpdate(resourceGroupName, dataFactoryName,
                new DatasetCreateOrUpdateParameters()
                {
                    Dataset = new Dataset()
                    {
                        Name = Dataset_Destination,
                        Properties = new DatasetProperties()
                        {
                            LinkedServiceName = "AzureStorageLinkedService",
                            TypeProperties = new AzureBlobDataset()
                            {
                                FileName = "{slice}.txt",
                                FolderPath = "adftutorial/customactivityoutput/",
                                PartitionedBy = new List<Partition>()
                                {
                                    new Partition()
                                    {
                                        Name = "slice",
                                        Value = new DateTimePartitionValue()
                                        {
                                            Date = "SliceStart",
                                            Format = "yyyy-MM-dd-HH"
                                        }
                                    }
                                }
                            },
                            Availability = new Availability()
                            {
                                Frequency = SchedulePeriod.Hour,
                                Interval = 1,
                            },
                        }
                    }
                });

            Console.WriteLine("Creating a custom activity pipeline");
            DateTime PipelineActivePeriodStartTime = new DateTime(2017, 3, 9, 0, 0, 0, 0, DateTimeKind.Utc);
            DateTime PipelineActivePeriodEndTime = PipelineActivePeriodStartTime.AddMinutes(60);
            string PipelineName = "ADFTutorialPipelineCustom";

            client.Pipelines.CreateOrUpdate(resourceGroupName, dataFactoryName,
                new PipelineCreateOrUpdateParameters()
                {
                    Pipeline = new Pipeline()
                    {
                        Name = PipelineName,
                        Properties = new PipelineProperties()
                        {
                            Description = "Use custom activity",

                            // Initial value for pipeline's active period. With this, you won't need to set slice status
                            Start = PipelineActivePeriodStartTime,
                            End = PipelineActivePeriodEndTime,
                            IsPaused = false,

                            Activities = new List<Activity>()
                            {
                                new Activity()
                                {
                                    Name = "MyDotNetActivity",
                                    Inputs = new List<ActivityInput>()
                                    {
                                        new ActivityInput() {
                                            Name = Dataset_Source
                                        }
                                    },
                                    Outputs = new List<ActivityOutput>()
                                    {
                                        new ActivityOutput()
                                        {
                                            Name = Dataset_Destination
                                        }
                                    },
                                    LinkedServiceName = "AzureBatchLinkedService",
                                    TypeProperties = new DotNetActivity()
                                    {
                                        AssemblyName = "MyDotNetActivity.dll",
                                        EntryPoint = "MyDotNetActivityNS.MyDotNetActivity",
                                        PackageLinkedService = "AzureStorageLinkedService",
                                        PackageFile = "customactivitycontainer/MyDotNetActivity.zip",
                                        ExtendedProperties = new Dictionary<string, string>()
                                        {
                                            { "SliceStart", "$$Text.Format('{0:yyyyMMddHH-mm}', Time.AddMinutes(SliceStart, 0))"}
                                        }
                                    },
                                    Policy = new ActivityPolicy()
                                    {
                                        Concurrency = 2,
                                        ExecutionPriorityOrder = "OldestFirst",
                                        Retry = 3,
                                        Timeout = new TimeSpan(0,0,30,0),
                                        Delay = new TimeSpan()
                                    }
                                }
                            }
                        }
                    }
                });
        }

        public static async Task<string> GetAuthorizationHeader()
        {
            AuthenticationContext context = new AuthenticationContext(ConfigurationManager.AppSettings["ActiveDirectoryEndpoint"] + ConfigurationManager.AppSettings["ActiveDirectoryTenantId"]);
            ClientCredential credential = new ClientCredential(
                ConfigurationManager.AppSettings["ApplicationId"],
                ConfigurationManager.AppSettings["Password"]);
            AuthenticationResult result = await context.AcquireTokenAsync(
                resource: ConfigurationManager.AppSettings["WindowsManagementUri"],
                clientCredential: credential);

            if (result != null)
                return result.AccessToken;

            throw new InvalidOperationException("Failed to acquire token");
        }
    }
}
```

## <a name="debug-custom-activity-in-visual-studio"></a>Debugowanie działań niestandardowych w programie Visual Studio
[Fabryki danych Azure - lokalnego środowiska](https://github.com/gbrueckl/Azure.DataFactory.LocalEnvironment) przykładem w witrynie GitHub zawiera narzędzia, która umożliwia debugowanie niestandardowych działań platformy .NET w programie Visual Studio.  


## <a name="sample-custom-activities-on-github"></a>Przykład niestandardowych działań w witrynie GitHub
| Sample | Jakie niestandardowe działanie robi |
| --- | --- |
| [Narzędzie do pobierania danych HTTP](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/HttpDataDownloaderSample). |Pobiera dane z punktu końcowego HTTP do magazynu obiektów Blob Azure przy użyciu działań niestandardowych C# w fabryce danych. |
| [Przykładowe wskaźniki nastrojów klientów analizy w usłudze Twitter](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/TwitterAnalysisSample-CustomC%23Activity) |Wywołuje model usługi uczenie Maszynowe Azure i analizy wskaźniki nastrojów klientów oceniania, prognozowania itp. |
| [Uruchom skrypt języka R](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample). |Wywołuje skrypt języka R, uruchamiając RScript.exe w klastrze usługi HDInsight, na którym jest już zainstalowany R na nim. |
| [Krzyżowe AppDomain .NET działania](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/CrossAppDomainDotNetActivitySample) |Używa innego zestawu wersji z takich, które jest używane przez uruchamianie fabryki danych |
| [Ponowne przetworzenie modelu w usług Azure Analysis Services](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/AzureAnalysisServicesProcessSample) |  Ponownego przetwarzania modelu w usług Azure Analysis Services. |

[batch-net-library]: ../../batch/batch-dotnet-get-started.md
[batch-create-account]: ../../batch/batch-account-create-portal.md
[batch-technical-overview]: ../../batch/batch-technical-overview.md
[batch-get-started]: ../../batch/batch-dotnet-get-started.md
[use-custom-activities]: data-factory-use-custom-activities.md
[troubleshoot]: data-factory-troubleshoot.md
[data-factory-introduction]: data-factory-introduction.md
[azure-powershell-install]: https://github.com/Azure/azure-sdk-tools/releases


[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456

[new-azure-batch-account]: https://msdn.microsoft.com/library/mt125880.aspx
[new-azure-batch-pool]: https://msdn.microsoft.com/library/mt125936.aspx
[azure-batch-blog]: http://blogs.technet.com/b/windowshpc/archive/2014/10/28/using-azure-powershell-to-manage-azure-batch-account.aspx

[nuget-package]: http://go.microsoft.com/fwlink/?LinkId=517478
[adf-developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[azure-preview-portal]: https://portal.azure.com/

[adfgetstarted]: data-factory-copy-data-from-azure-blob-storage-to-sql-database.md
[hivewalkthrough]: data-factory-data-transformation-activities.md

[image-data-factory-ouput-from-custom-activity]: ./media/data-factory-use-custom-activities/OutputFilesFromCustomActivity.png

[image-data-factory-download-logs-from-custom-activity]: ./media/data-factory-use-custom-activities/DownloadLogsFromCustomActivity.png
