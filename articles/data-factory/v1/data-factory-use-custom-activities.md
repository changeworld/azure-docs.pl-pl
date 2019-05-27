---
title: Korzystanie z działań niestandardowych w potoku usługi Azure Data Factory
description: Dowiedz się, jak tworzyć niestandardowe działania i używać ich w potoku usługi Azure Data Factory.
services: data-factory
documentationcenter: ''
ms.assetid: 8dd7ba14-15d2-4fd9-9ada-0b2c684327e9
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
author: nabhishek
ms.author: abnarain
manager: craigg
robots: noindex
ms.openlocfilehash: f7e3b1496890a4b97fc435b49ab9bf282134d1a6
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2019
ms.locfileid: "65910818"
---
# <a name="use-custom-activities-in-an-azure-data-factory-pipeline"></a>Korzystanie z działań niestandardowych w potoku usługi Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Wersja 1](data-factory-use-custom-activities.md)
> * [Wersja 2 (bieżąca wersja)](../transform-data-using-dotnet-custom-activity.md)

> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory. Jeśli używasz bieżącą wersję usługi Data Factory, zobacz [niestandardowych działań w wersji 2](../transform-data-using-dotnet-custom-activity.md).

Istnieją dwa typy działań, które można używać w potoku usługi Azure Data Factory.

- [Działania przenoszenia danych](data-factory-data-movement-activities.md) do przenoszenia danych między [obsługiwane magazyny danych źródła i ujścia](data-factory-data-movement-activities.md#supported-data-stores-and-formats).
- [Działania przekształcania danych](data-factory-data-transformation-activities.md) do przekształcania danych za pomocą usług obliczeniowych, takich jak Azure HDInsight, Azure Batch i Azure Machine Learning.

Aby przenieść dane do/z magazynu danych, który nie obsługuje usługi Data Factory, należy utworzyć **niestandardowe działanie** z własną logiką przepływu danych i używania działania w potoku. Podobnie celu przekształcenie/przetworzenie danych w sposób, który nie jest obsługiwany przez usługę Data Factory, Utwórz niestandardowe działanie za pomocą własnej logiki przekształcania danych i użyć działania w potoku.

Można skonfigurować niestandardowe działanie, aby uruchomić na **usługi Azure Batch** puli maszyn wirtualnych. Korzystając z usługi Azure Batch, można użyć tylko istniejącej puli Azure Batch.

Następujące instruktaż zawiera szczegółowe instrukcje dotyczące tworzenia niestandardowe działanie platformy .NET i używania niestandardowego działania w potoku. Przewodnik używa **usługi Azure Batch** połączoną usługę.

> [!IMPORTANT]
> - Nie jest możliwe użycie bramy zarządzania danymi z działań niestandardowych dostęp do lokalnych źródeł danych. Obecnie [bramy zarządzania danymi](data-factory-data-management-gateway.md) obsługuje tylko działania kopiowania i działanie procedury składowanej w usłudze Data Factory.

## <a name="walkthrough-create-a-custom-activity"></a>Przewodnik: Tworzenie niestandardowego działania
### <a name="prerequisites"></a>Wymagania wstępne
* Visual Studio 2012/2013/2015/2017
* Pobierz i zainstaluj zestaw [SDK .NET Azure](https://azure.microsoft.com/downloads/).

### <a name="azure-batch-prerequisites"></a>Wymagania wstępne usługi Azure Batch
W instruktażu możesz uruchamiać swoje niestandardowe działania programu .NET przy użyciu usługi Azure Batch jako zasobów obliczeniowych. Usługa **Azure Batch** to usługa platformy służąca do wydajnego uruchamiania w chmurze aplikacji równoległych oraz aplikacji do obliczeń o wysokiej wydajności w wielkiej skali. Usługa Azure Batch planuje pracę intensywnych obliczeń do uruchamiania na zarządzanej **kolekcji maszyn wirtualnych**, i może automatycznie skalować zasoby obliczeniowe do potrzeb Twojego zadania. Zobacz [podstawy usługi Azure Batch] [ batch-technical-overview] artykułu szczegółowe omówienie usługi Azure Batch.

Samouczek należy utworzyć konto usługi Azure Batch przy użyciu puli maszyn wirtualnych. Oto konkretne kroki:

1. Tworzenie **konta usługi Azure Batch** przy użyciu [witryny Azure portal](https://portal.azure.com). Zobacz [Tworzenie konta usługi Azure Batch i zarządzanie nim] [ batch-create-account] artykuł, aby uzyskać instrukcje.
2. Zanotuj nazwę konta usługi Azure Batch, klucza konta, identyfikator URI i nazwy puli. Będą one potrzebne do tworzenia usługi Azure Batch połączone.
    1. Na stronie głównej konta usługi Azure Batch, zobacz **adresu URL** w następującym formacie: `https://myaccount.westus.batch.azure.com`. W tym przykładzie **myaccount** jest nazwą konta usługi Azure Batch. Identyfikator URI, których używasz w definicji połączonej usługi jest adres URL bez nazwy konta. Na przykład: `https://<region>.batch.azure.com`.
    2. Kliknij przycisk **klucze** w menu po lewej stronie, a następnie skopiuj **podstawowy klucz dostępu**.
    3. Aby użyć istniejącej puli, kliknij **pule** na pasku menu, a następnie zanotuj **identyfikator** puli. Jeśli nie masz istniejącej puli, przejdź do następnego kroku.
2. Tworzenie **puli Azure Batch**.

   1. W [witryny Azure portal](https://portal.azure.com), kliknij przycisk **Przeglądaj** w menu po lewej stronie, a następnie kliknij przycisk **konta usługi Batch**.
   2. Wybierz swoje konto usługi Azure Batch, aby otworzyć **konta usługi Batch** bloku.
   3. Kliknij przycisk **pule** kafelka.
   4. W **pule** bloku, kliknij przycisk Dodaj na pasku narzędzi, aby dodać pulę.
      1. Wpisz identyfikator puli (identyfikator puli). Uwaga **identyfikator puli**; będzie on potrzebny podczas tworzenia rozwiązania usługi Data Factory.
      2. Określ **systemu Windows Server 2012 R2** ustawienia Rodzina systemów operacyjnych.
      3. Wybierz **warstwie cenowej węzła**.
      4. Wprowadź **2** jako wartość **docelowego w wersji dedykowanej** ustawienie.
      5. Wprowadź **2** jako wartość **maksymalna liczba zadań na węzeł** ustawienie.
   5. Kliknij przycisk **OK**, aby utworzyć pulę.
   6. Zanotuj **identyfikator** puli.

### <a name="high-level-steps"></a>Ogólne kroki
Poniżej przedstawiono dwa ogólne kroki, które wykonujesz w ramach tego przewodnika:

1. Utwórz niestandardowe działanie, które zawiera logikę przetworzenia/przekształcenia danych proste.
2. Tworzenie usługi Azure data factory z potokiem, który wykorzystuje działania niestandardowe.

### <a name="create-a-custom-activity"></a>Tworzenie niestandardowego działania
Aby utworzyć niestandardowe działanie platformy .NET, Utwórz **Biblioteka klas programu .NET** projektu za pomocą klasy, która implementuje, **IDotNetActivity** interfejsu. Ten interfejs ma tylko jedną metodę: [Wykonaj](https://msdn.microsoft.com/library/azure/mt603945.aspx) i podpisie:

```csharp
public IDictionary<string, string> Execute(
    IEnumerable<LinkedService> linkedServices,
    IEnumerable<Dataset> datasets,
    Activity activity,
    IActivityLogger logger)
```

Ta metoda przyjmuje cztery parametry:

- **linkedServices**. Ta właściwość jest listę wyliczalną Store danych, połączonej usługi odwołuje się zestawy danych wejściowych/wyjściowych działania.
- **zestawy danych**. Ta właściwość jest lista numerowana wejściowych/wyjściowych zestawów danych przeznaczonych do działania. Ten parametr umożliwia uzyskiwanie lokalizacji i schematy definiowane przez danych wejściowych i wyjściowych zestawów danych.
- **działanie**. Ta właściwość reprezentuje bieżące działanie. Można je uzyskać dostęp do rozszerzonych właściwości skojarzone z działań niestandardowych. Zobacz [dostęp do właściwości rozszerzone](#access-extended-properties) Aby uzyskać szczegółowe informacje.
- **Rejestrator**. Ten obiekt umożliwia pisać komentarze debugowania tej powierzchni w dzienniku użytkownika dla potoku.

Metoda zwraca słownik, który może służyć do łańcuch niestandardowych działań w przyszłości. Ta funkcja nie jest jeszcze zaimplementowana, więc Zwróć pusty słownik z metody.

### <a name="procedure"></a>Procedura
1. Tworzenie **Biblioteka klas programu .NET** projektu.
   <ol type="a">
     <li>Uruchom program Visual Studio.</li>
     <li>Kliknij pozycję <b>Plik</b>, wskaż polecenie <b>Nowy</b> i kliknij pozycję <b>Projekt</b>.</li>
     <li>Rozwiń węzeł <b>Szablony</b> i wybierz opcję <b>Visual C#</b>. W tym przewodniku możesz używać języka C#, ale można używać dowolnego języka platformy .NET do tworzenia działań niestandardowych.</li>
     <li>Wybierz <b>biblioteki klas</b> z listy typów projektów po prawej stronie. W programie Visual Studio, wybierz <b>biblioteki klas (.NET Framework)</b> </li>
     <li>Wprowadź <b>MyDotNetActivity</b> dla <b>nazwa</b>.</li>
     <li>Wybierz <b>C:\ADFGetStarted</b> dla <b>lokalizacji</b>.</li>
     <li>Kliknij przycisk <b>OK</b>, aby utworzyć projekt.</li>
   </ol>

2. Kliknij pozycję **Narzędzia**, wskaż pozycję **Menedżer pakietów NuGet**, a następnie kliknij pozycję **Konsola menedżera pakietów**.

3. W konsoli Menedżera pakietów, wykonaj następujące polecenie, aby zaimportować **Microsoft.Azure.Management.DataFactories**.

    ```powershell
    Install-Package Microsoft.Azure.Management.DataFactories
    ```
4. Importuj **usługi Azure Storage** pakietu NuGet w projekcie.

    ```powershell
    Install-Package WindowsAzure.Storage -Version 4.3.0
    ```

    > [!IMPORTANT]
    > Uruchamianie usługi fabryka danych wymaga wersji 4.3 WindowsAzure.Storage. Jeśli dodasz odwołanie do nowszej wersji zestawu Azure Storage w projekcie niestandardowe działanie, zostanie wyświetlony błąd, gdy działanie wykonuje. Aby naprawić błąd, zobacz [izolacji elementu Appdomain](#appdomain-isolation) sekcji.
5. Dodaj następujący kod **przy użyciu** instrukcje pliku źródłowego w projekcie.

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
7. Zmień nazwę klasy, która ma **MyDotNetActivity** i pochodzić z **IDotNetActivity** interfejsu, jak pokazano w poniższym fragmencie kodu:

    ```csharp
    public class MyDotNetActivity : IDotNetActivity
    ```
8. Implementowanie (Dodaj) **Execute** metody **IDotNetActivity** współpracować w celu **MyDotNetActivity** klasy, a następnie skopiuj następujący przykładowy kod do metody.

    Poniższy przykład zlicza wystąpienia wyszukiwany termin ("Microsoft") w każdy obiekt blob skojarzony z wycinka danych.

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

        // get the first Azure Storage linked service from linkedServices object
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
9. Dodaj następujące metody pomocy:

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

    Metoda GetFolderPath zwraca ścieżkę do folderu, który wskazuje zestaw danych, a metoda GetFileName zwraca nazwę pliku obiektu blob/wskazujący zestaw danych. Jeśli masz folderPath definiuje, korzystając ze zmiennych, takich jak {Year}, {Month} {dzień} itp., metoda zwraca ciąg w postaci, w jakiej jest bez zastępowania ich wartości środowiska uruchomieniowego. Zobacz [dostęp do właściwości rozszerzone](#access-extended-properties) sekcji, aby uzyskać szczegółowe informacje na temat uzyskiwania dostępu do parametru SliceStart, SliceEnd itp.

    ```JSON
    "name": "InputDataset",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "fileName": "file.txt",
            "folderPath": "adftutorial/inputfolder/",
    ```

    Metoda Calculate oblicza liczbę wystąpień — słowo kluczowe plików wejściowych (obiekty BLOB w folderze) firmy Microsoft. Wyszukiwany termin ("Microsoft") jest ustalony w kodzie.
10. Skompiluj projekt. Kliknij przycisk **kompilacji** menu, a następnie kliknij **Kompiluj rozwiązanie**.

    > [!IMPORTANT]
    > Ustaw 4.5.2 wersję programu .NET Framework jako platformę docelową dla projektu: kliknij prawym przyciskiem myszy projekt, a następnie kliknij przycisk **właściwości** można ustawić platformę docelową. Fabryki danych nie obsługuje niestandardowych działań skompilowany .NET Framework w wersji nowszej niż 4.5.2.

11. Uruchom **Eksplorator Windows**, a następnie przejdź do **bin\debug** lub **bin\release** folder, w zależności od typu kompilacji.
12. Tworzenie pliku zip **MyDotNetActivity.zip** zawierający wszystkie pliki binarne w \<folderu projektu\>folderze \bin\Debug. Obejmują **MyDotNetActivity.pdb** plików tak, aby uzyskać dodatkowe szczegóły, takie jak numer linii w kodzie źródłowym, która spowodowała problem, jeśli wystąpił błąd.

    > [!IMPORTANT]
    > Wszystkie pliki w archiwum ZIP działania niestandardowego muszą znajdować się na **najwyższym poziomie**, bez podfolderów.

    ![Binarne pliki wyjściowe](./media/data-factory-use-custom-activities/Binaries.png)
14. Utwórz kontener obiektów blob o nazwie **customactivitycontainer** Jeśli jeszcze nie istnieje.
15. Przekaż MyDotNetActivity.zip jako obiekt blob do customactivitycontainer w **ogólnego przeznaczenia** usługi Azure blob storage (nie gorący/chłodny magazyn obiektów Blob), który jest określany przez AzureStorageLinkedService.

> [!IMPORTANT]
> Jeśli dodawanie tego projektu działanie platformy .NET do rozwiązania w programie Visual Studio, która zawiera projekt usługi fabryka danych, a następnie dodaj odwołanie do projektu działanie platformy .NET z projektu aplikacji usługi Data Factory, nie należy wykonać dwa ostatnie kroki ręcznego tworzenia pliku zip plik i przekazać go do ogólnego przeznaczenia Azure blob storage. Podczas publikowania jednostek usługi Data Factory przy użyciu programu Visual Studio, te kroki są wykonywane automatycznie przez proces publikowania. Aby uzyskać więcej informacji, zobacz [projekt usługi fabryka danych w programie Visual Studio](#data-factory-project-in-visual-studio) sekcji.

## <a name="create-a-pipeline-with-custom-activity"></a>Tworzenie potoku za pomocą niestandardowego działania
Utworzono niestandardowe działanie i przekazany plik zip z plikami binarnymi do kontenera obiektów blob w **ogólnego przeznaczenia** konta usługi Azure Storage. W tej sekcji utworzysz usługi Azure data factory z potokiem, który wykorzystuje działania niestandardowe.

Wejściowy zestaw danych działania niestandardowego reprezentuje obiektów blob (pliki) w folderze customactivityinput kontenera adftutorial w magazynie obiektów blob. Wyjściowy zestaw danych działania reprezentuje wyjściowych obiektów blob w folderze customactivityoutput kontenera adftutorial w magazynie obiektów blob.

Tworzenie **plik.txt** pliku o następującej zawartości i przekaż go do **customactivityinput** folderu **adftutorial** kontenera. Tworzenie kontenera adftutorial, jeśli go jeszcze nie istnieje.

```
test custom activity Microsoft test custom activity Microsoft
```

Folderu danych wejściowych odpowiada że wycinek jest w usłudze Azure Data Factory, nawet wtedy, gdy folder ma dwa lub więcej plików. Każdy wycinek jest przetwarzany przez potok, niestandardowe działanie iteruje wszystkich obiektów blob w folderze wejściowym dla tego wycinka.

Zobaczysz, że jeden wyjściowy plik z folderu adftutorial\customactivityoutput z jednego lub więcej wierszy (tak jak liczbę obiektów blob w folderze wejściowym):

```
2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2016-11-16-00/file.txt.
```


Poniżej przedstawiono kroki, które należy wykonać w tej sekcji:

1. Tworzenie **usługi data factory**.
2. Tworzenie **połączonych usług** puli Azure Batch maszyn wirtualnych na uruchamiająca niestandardowe działanie i magazynu platformy Azure, w którym przechowywane są obiekty BLOB we/wy.
3. Utworzenie wejściowych i wyjściowych **zestawów danych** reprezentujące dane wejściowe i wyjściowe działania niestandardowego.
4. Tworzenie **potoku** używającej niestandardowego działania.

> [!NOTE]
> Tworzenie **plik.txt** i przekaż go do kontenera obiektów blob, jeśli jeszcze tego nie zrobiłeś. Zobacz instrukcje w poprzedniej sekcji.

### <a name="step-1-create-the-data-factory"></a>Krok 1: Tworzenie fabryki danych
1. Po zalogowaniu się do witryny Azure portal, wykonaj następujące czynności:
   1. Kliknij przycisk **Utwórz zasób** w menu po lewej stronie.
   2. Kliknij przycisk **dane + analiza** w **New** bloku.
   3. Kliknij przycisk **Fabryka danych** w bloku **Analiza danych**.

      ![Nowe menu usługi Azure Data Factory](media/data-factory-use-custom-activities/new-azure-data-factory-menu.png)
2. W **nowa fabryka danych** bloku wprowadź **CustomActivityFactory** dla nazwy. Nazwa fabryki danych Azure musi być globalnie unikatowa. Jeśli zostanie wyświetlony błąd: **Nazwa fabryki danych "CustomActivityFactory" nie jest dostępna**, Zmień nazwę fabryki danych (na przykład **yournameCustomActivityFactory**), a następnie spróbuj utworzyć ją ponownie.

    ![Nowy blok usługi Azure Data Factory](media/data-factory-use-custom-activities/new-azure-data-factory-blade.png)
3. Kliknij przycisk **nazwy grupy zasobów**i wybierz istniejącą grupę zasobów lub Utwórz grupę zasobów.
4. Sprawdź, że używasz prawidłowych **subskrypcji** i **region** , w której chcesz utworzyć fabrykę danych.
5. Kliknij przycisk **Utwórz** w bloku **Nowa fabryka danych**.
6. Zostanie wyświetlony w fabryce danych tworzony w **pulpit nawigacyjny** w witrynie Azure Portal.
7. Po pomyślnym utworzeniu fabryki danych, zobaczysz bloku Data Factory zawartością fabryki danych.

    ![Blok Fabryka danych](media/data-factory-use-custom-activities/data-factory-blade.png)

### <a name="step-2-create-linked-services"></a>Krok 2: Tworzenie połączonych usług
Połączone usługi łączą magazyny danych lub usługi obliczeniowe z fabryką danych Azure. W tym kroku połączysz swoje konto usługi Azure Storage i konta usługi Azure Batch z fabryką danych.

#### <a name="create-azure-storage-linked-service"></a>Tworzenie połączonej usługi Azure Storage
1. Kliknij przycisk **tworzenie i wdrażanie** kafelków na **usługi DATA FACTORY** bloku **CustomActivityFactory**. Pojawi się Edytor fabryki danych.
2. Kliknij przycisk **nowy magazyn danych** na polecenia paska, a następnie wybierz **usługi Azure storage**. Powinien zostać wyświetlony skrypt JSON do tworzenia połączonej usługi Azure Storage w edytorze.

    ![Nowy magazyn danych — Azure Storage](media/data-factory-use-custom-activities/new-data-store-menu.png)
3. Zastąp `<accountname>` z nazwą konta usługi Azure storage i `<accountkey>` kluczem dostępu konta usługi Azure storage. Informacje na temat pobierania klucza dostępu do magazynu znajdują się w artykule [Wyświetlanie, kopiowanie i ponowne generowanie kluczy dostępu do magazynu](../../storage/common/storage-account-manage.md#access-keys).

    ![Usługa Azure Storage zbędne usługi](media/data-factory-use-custom-activities/azure-storage-linked-service.png)
4. Kliknij przycisk **Wdróż** na pasku poleceń, aby wdrożyć połączoną usługę.

#### <a name="create-azure-batch-linked-service"></a>Tworzenie usługi Azure Batch połączone
1. W edytorze fabryki danych, kliknij przycisk **... Więcej** na pasku poleceń kliknij **nowe obliczenie**, a następnie wybierz pozycję **usługi Azure Batch** z menu.

    ![Nowe obliczenie — Azure Batch](media/data-factory-use-custom-activities/new-azure-compute-batch.png)
2. Skrypt JSON, należy wprowadzić następujące zmiany:

   1. Określ nazwę konta usługi Azure Batch **accountName** właściwości. **Adresu URL** z **bloku konta usługi Azure Batch** znajduje się w następującym formacie: `http://accountname.region.batch.azure.com`. Aby uzyskać **batchUri** właściwości w kodzie JSON, którą chcesz usunąć `accountname.` z adresu URL i użycia `accountname` dla `accountName` właściwość JSON.
   2. Określ klucz konta usługi Azure Batch **accessKey** właściwości.
   3. Określ nazwę puli został utworzony jako część wymagań wstępnych dotyczących **poolName** właściwości. Można również określić identyfikator puli zamiast nazwy puli.
   4. Określ identyfikator URI usługi Azure Batch dla **batchUri** właściwości. Przykład: `https://westus.batch.azure.com`.
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
W tym kroku utworzysz zestawy danych do reprezentowania danych wejściowych i wyjściowych.

#### <a name="create-input-dataset"></a>Tworzenie wejściowego zestawu danych
1. W **edytorze** fabryki danych kliknij kolejno polecenia  **Więcej** na pasku poleceń kliknij **nowy zestaw danych**, a następnie wybierz pozycję **usługi Azure Blob storage** z menu rozwijanego.
2. Zastąp kod JSON w prawym okienku następującym fragmentem kodu JSON:

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

   Utworzysz potok w dalszej części tego przewodnika, czas rozpoczęcia: 2016-11-16T00:00:00Z i godzinę zakończenia: 2016-11-16T05:00:00Z. Zostaje zaplanowane do wygenerowania danych co godzinę, co pięć wycinki wejścia/wyjścia (między **00**: -> 00:00 **05**: 00:00).

   **Częstotliwość** i **interwał** dla wejściowego zestawu danych jest ustawiony na **godzinę** i **1**, co oznacza, że wycinek danych wejściowych jest dostępny co godzinę. W tym przykładzie jest tego samego pliku (plik.txt) w intputfolder.

   Poniżej przedstawiono godziny rozpoczęcia dla każdego wycinka jest reprezentowany przez SliceStart zmiennej systemowej w powyższym fragmencie kodu JSON.
3. Kliknij przycisk **Wdróż** na pasku narzędzi, aby utworzyć i wdrożyć **InputDataset**. Upewnij się, że na pasku tytułu w edytorze wyświetlany jest komunikat **TABELA ZOSTAŁA UTWORZONA POMYŚLNIE**.

#### <a name="create-an-output-dataset"></a>Tworzenie wyjściowego zestawu danych
1. W **edytora fabryki danych**, kliknij przycisk **... Więcej** na pasku poleceń kliknij **nowy zestaw danych**, a następnie wybierz pozycję **usługi Azure Blob storage**.
2. Zastąp skrypt JSON w okienku po prawej stronie poniższy skrypt JSON:

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

     Lokalizacja danych wyjściowych jest **adftutorial/customactivityoutput/** i nazwa pliku wyjściowego jest RRRR MM-dd-HH.txt, gdy RRRR MM-dd HH roku, miesiąca, daty i godziny wycinka generowany. Zobacz [dokumentacja dla deweloperów] [ adf-developer-reference] Aby uzyskać szczegółowe informacje.

    Obiekt blob/plik wyjściowy jest generowany dla każdego wycinek danych wejściowych. Oto, jak plik wyjściowy nosi nazwę każdego wycinka. Wszystkie pliki wyjściowe są generowane w jednym folderze danych wyjściowych: **adftutorial\customactivityoutput**.

   | Wycinek | Godzina rozpoczęcia | Plik wyjściowy |
   |:--- |:--- |:--- |
   | 1 |2016-11-16T00:00:00 |2016-11-16-00.txt |
   | 2 |2016-11-16T01:00:00 |2016-11-16-01.txt |
   | 3 |2016-11-16T02:00:00 |2016-11-16-02.txt |
   | 4 |2016-11-16T03:00:00 |2016-11-16-03.txt |
   | 5 |2016-11-16T04:00:00 |2016-11-16-04.txt |

    Należy pamiętać, że wszystkie pliki w folderze wejściowym są częścią wycinek z czasem rozpoczęcia wymienionych powyżej. Podczas przetwarzania tego wycinka niestandardowe działanie skanowania za pomocą każdego pliku i tworzy linię w pliku danych wyjściowych z liczbą wystąpień wyszukiwany termin ("Microsoft"). Jeśli istnieją trzy pliki w inputfolder, istnieją trzy wiersze w pliku danych wyjściowych dla każdego wycinka co godzinę: 2016-11-16-00.txt 2016-11-16:01:00:00.txt itp.
3. Aby wdrożyć **OutputDataset**, kliknij przycisk **Wdróż** na pasku poleceń.

### <a name="create-and-run-a-pipeline-that-uses-the-custom-activity"></a>Tworzenie i uruchamianie potoku, który używa niestandardowego działania
1. W edytorze fabryki danych, kliknij przycisk **... Więcej**, a następnie wybierz pozycję **nowy potok** na pasku poleceń.
2. Zastąp kod JSON w okienku po prawej stronie poniższy skrypt JSON:

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

   * **Współbieżność** ustawiono **2** tak, że dwa wycinki są przetwarzane równolegle przez 2 maszyny wirtualne w puli Azure Batch.
   * W sekcji działań jest jedno działanie i jest on typu: **DotNetActivity**.
   * **AssemblyName** jest ustawiona na nazwę biblioteki DLL: **MyDotnetActivity.dll**.
   * **Punkt wejścia** ustawiono **MyDotNetActivityNS.MyDotNetActivity**.
   * **PackageLinkedService** ustawiono **AzureStorageLinkedService** wskazującego na magazynu obiektów blob, który zawiera plik zip działania niestandardowego. Jeśli używasz różnych kont usługi Azure Storage dla plików wejściowych/wyjściowych i plik zip działania niestandardowego, utworzysz inną połączoną usługę Azure Storage. W tym artykule założono, że używasz tego samego konta usługi Azure Storage.
   * **PackageFile** ustawiono **customactivitycontainer/MyDotNetActivity.zip**. Jest w formacie: containerforthezip/nameofthezip.zip.
   * Niestandardowe działanie przyjmuje **InputDataset** jako dane wejściowe i **OutputDataset** jako dane wyjściowe.
   * Wskazuje właściwość linkedServiceName działania niestandardowego **AzureBatchLinkedService**, który informuje usługi Azure Data Factory działanie niestandardowe muszą zostać uruchomione na maszynach wirtualnych platformy Azure Batch.
   * **isPaused** właściwość jest ustawiona na **false** domyślnie. Potok uruchamia natychmiast w tym przykładzie, ponieważ wycinki można uruchomić w przeszłości. Tę właściwość można ustawić na wartość true, aby wstrzymać potoku i go ustawić ponownie na wartość false, aby ponownie uruchomić.
   * **Start** czasu i **zakończenia** godziny są **pięć** godziny od siebie i wycinki są generowane co godzinę, więc pięć wycinki są produkowane przez potok.
3. Aby wdrożyć potok, kliknij przycisk **Wdróż** na pasku poleceń.

### <a name="monitor-the-pipeline"></a>Monitorowanie potoku
1. W bloku Data Factory w witrynie Azure portal, kliknij **Diagram**.

    ![Kafelek Diagram](./media/data-factory-use-custom-activities/DataFactoryBlade.png)
2. Na stronie widok diagramu kliknij przycisk zestawu OutputDataset.

    ![Widok diagramu](./media/data-factory-use-custom-activities/diagram.png)
3. Powinieneś zobaczyć, że wycinki pięć dane wyjściowe są w stanie gotowe. Jeśli nie są w stanie gotowe, jeszcze nie została opracowana jeszcze.

   ![Wycinki danych wyjściowych](./media/data-factory-use-custom-activities/OutputSlices.png)
4. Sprawdź, czy pliki wyjściowe są generowane w magazynie obiektów blob w **adftutorial** kontenera.

   ![dane wyjściowe z działań niestandardowych][image-data-factory-output-from-custom-activity]
5. Jeśli otworzysz plik wyjściowy, powinny pojawić się dane wyjściowe są podobne do następujących danych wyjściowych:

    ```
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2016-11-16-00/file.txt.
    ```
6. Użyj [witryny Azure portal] [ azure-preview-portal] lub poleceń cmdlet programu Azure PowerShell, aby monitorować fabryki danych, potoki i zestawów danych. Można wyświetlić wiadomości z **ActivityLogger** w kodzie niestandardowe działania w dzienniki (w szczególności nazwach user-0.log), które można pobrać z portalu lub przy użyciu poleceń cmdlet.

   ![Pobieranie dzienników z działań niestandardowych][image-data-factory-download-logs-from-custom-activity]

Zobacz [monitorowanie potoków i zarządzanie nimi](data-factory-monitor-manage-pipelines.md) Aby uzyskać szczegółowe instrukcje dotyczące monitorowania potoków i zestawów danych.

## <a name="data-factory-project-in-visual-studio"></a>Projekt usługi fabryka danych w programie Visual Studio
Można tworzyć i publikować jednostki fabryki danych przy użyciu programu Visual Studio zamiast przy użyciu witryny Azure portal. Szczegółowe informacje na temat tworzenia i publikowania jednostek usługi Data Factory przy użyciu programu Visual Studio, zobacz [Tworzenie pierwszego potoku za pomocą programu Visual Studio](data-factory-build-your-first-pipeline-using-vs.md) i [kopiowanie danych z obiektów Blob platformy Azure do bazy danych SQL Azure](data-factory-copy-activity-tutorial-using-visual-studio.md) artykuły.

Jeśli tworzysz projekt usługi fabryka danych w programie Visual Studio, należy wykonać następujące dodatkowe czynności:

1. Dodaj projekt usługi fabryka danych do rozwiązania programu Visual Studio, które zawiera projekt działań niestandardowych.
2. Dodaj odwołanie do projektu działanie platformy .NET z projekt usługi fabryka danych. Kliknij prawym przyciskiem myszy projekt usługi fabryka danych, wskaż opcję **Dodaj**, a następnie kliknij przycisk **odwołania**.
3. W **Dodaj odwołanie** okno dialogowe, wybierz opcję **MyDotNetActivity** projektu, a następnie kliknij przycisk **OK**.
4. Tworzenie i publikowanie rozwiązania.

    > [!IMPORTANT]
    > Podczas publikowania jednostek usługi Data Factory pliku zip jest automatycznie tworzony i zostanie przekazany do kontenera obiektów blob: customactivitycontainer. Jeśli kontener obiektów blob nie istnieje, zostanie automatycznie utworzony zbyt.

## <a name="data-factory-and-batch-integration"></a>Integracja usługi Data Factory i Batch
Usługa Data Factory tworzy zadanie w usłudze Azure Batch o nazwie: **adf poolname: xxx zadania**. Kliknij przycisk **zadań** menu po lewej stronie.

![Usługa Azure Data Factory — zadania usługi Batch](media/data-factory-use-custom-activities/data-factory-batch-jobs.png)

Zadanie jest tworzone dla każdego uruchomienia działania wycinka. W przypadku pięciu wycinki gotowe do przetworzenia pięć zadań są tworzone w ramach tego zadania. W przypadku wielu węzłach obliczeniowych w puli usługi Batch, co najmniej dwa wycinki mogą uruchamiać równolegle. Jeśli maksymalny zadań na węzeł obliczeniowy jest ustawiona na > 1, możesz mieć więcej niż jeden wycinek uruchomionych na tych samych obliczeń.

![Usługa Azure Data Factory — zadania usługi Batch](media/data-factory-use-custom-activities/data-factory-batch-job-tasks.png)

Na poniższym diagramie przedstawiono relację między zadaniami w usłudze Azure Data Factory i Batch.

![Data Factory i Batch](./media/data-factory-use-custom-activities/DataFactoryAndBatch.png)

## <a name="troubleshoot-failures"></a>Rozwiązywanie problemów z błędami
Rozwiązywanie problemów z obejmuje kilka podstawowych technik:

1. Jeśli zostanie wyświetlony następujący błąd, używasz gorąca/chłodny magazyn obiektów blob zamiast ogólnego przeznaczenia usługi Azure blob storage. Przekaż plik zip do **konto magazynu ogólnego przeznaczenia Azure**.

    ```
    Error in Activity: Job encountered scheduling error. Code: BlobDownloadMiscError Category: ServerError Message: Miscellaneous error encountered while downloading one of the specified Azure Blob(s).
    ```
2. Jeśli zostanie wyświetlony następujący błąd, upewnij się, że nazwa klasy w pliku CS odpowiada nazwa określona dla **punktu wejścia** właściwości w kodzie JSON potoku. W instruktażu Nazwa klasy jest: MyDotNetActivity i punktu wejścia w kodzie JSON to: MyDotNetActivityNS.**MyDotNetActivity**.

    ```
    MyDotNetActivity assembly does not exist or doesn't implement the type Microsoft.DataFactories.Runtime.IDotNetActivity properly
    ```

   Jeśli nazwy są zgodne, upewnij się, że wszystkie pliki binarne znajdują się w **folder główny** pliku zip. Oznacza to gdy otworzysz plik zip, powinien zostać wyświetlony wszystkie pliki w folderze głównym, a nie w żadnych podfolderów.
3. Jeśli wycinek danych wejściowych nie jest ustawiony na **gotowe**, upewnij się, że struktura folderów wejściowych jest poprawna i **plik.txt** istnieje w folderach wejściowych.
3. W **Execute** metody działania niestandardowego użyj **IActivityLogger** obiektu, aby rejestrować informacje, które pomagają w rozwiązywaniu problemów. Zarejestrowane komunikaty pojawiają się w plikach dzienników użytkownika (co najmniej jeden plik o nazwie: nazwach user-0.log, user-1.log, user-2.log itp.).

   W **OutputDataset** bloku kliknij wycinek, aby wyświetlić **WYCINKA danych** blok dla tego wycinka. Zostanie wyświetlony **uruchomienia działania** dla tego wycinka. Powinien zostać wyświetlony jeden przebiegu działania dla wycinka. Na pasku poleceń kliknij przycisk Uruchom, po uruchomieniu kolejnego działania, uruchom dla tej samej wycinka.

   Po kliknięciu przycisku uruchomienia działania, zobacz **szczegóły URUCHAMIANIA działania** bloku zawierającego listę plików dziennika. Zarejestrowane komunikaty, w pliku user_0.log zostanie wyświetlony. Wystąpi błąd, zostanie wyświetlony trzy uruchomienia działania, ponieważ liczba ponownych prób jest równa 3 w kodzie JSON potoku/działania. Po kliknięciu przycisku uruchomienia działania zostanie wyświetlony pliki dziennika, które można przeglądać, aby rozwiązać ten błąd.

   Na liście plików dziennika, kliknij **nazwach user-0.log**. W prawym okienku są wyniki użycia **IActivityLogger.Write** metody. Jeśli nie widzisz wszystkich wiadomości, sprawdź, czy istnieją więcej plików dziennika o nazwie: user_1.log, user_2.log itp. W przeciwnym razie kod może nie powiodło się po ostatniej rejestrowany komunikat.

   Ponadto należy sprawdzić **0.log systemu** za wszelkie komunikaty o błędach systemu i wyjątki.
4. Obejmują **PDB** plików w pliku zip, aby szczegóły błędu zawierać informacje, takie jak **stos wywołań** po wystąpieniu błędu.
5. Wszystkie pliki w archiwum ZIP działania niestandardowego muszą znajdować się na **najwyższym poziomie**, bez podfolderów.
6. Upewnij się, że **assemblyName** (MyDotNetActivity.dll), **punktu wejścia**(MyDotNetActivityNS.MyDotNetActivity), **packageFile** (customactivitycontainer / MyDotNetActivity.zip) i **packageLinkedService** (powinien wskazywać **ogólnego przeznaczenia**magazynu obiektów blob platformy Azure, który zawiera plik zip) są ustawione na poprawne wartości.
7. Jeśli naprawiono błąd i chcesz przetworzyć wycinek ponownie, kliknij prawym przyciskiem wycinek w bloku **OutputDataset** i kliknij polecenie **Uruchom**.
8. Jeśli zostanie wyświetlony następujący błąd, używasz pakietu usługi Azure Storage w wersji > 4.3.0. Uruchamianie usługi fabryka danych wymaga wersji 4.3 WindowsAzure.Storage. Zobacz [izolacji elementu Appdomain](#appdomain-isolation) sekcji OBEJŚCIE Jeśli musisz użyć nowszej wersji zestawu Azure Storage.

    ```
    Error in Activity: Unknown error in module: System.Reflection.TargetInvocationException: Exception has been thrown by the target of an invocation. ---> System.TypeLoadException: Could not load type 'Microsoft.WindowsAzure.Storage.Blob.CloudBlob' from assembly 'Microsoft.WindowsAzure.Storage, Version=4.3.0.0, Culture=neutral,
    ```

    Jeśli używasz 4.3.0 wersję pakietu usługi Azure Storage, Usuń istniejące odwołanie do pakietu wersji > 4.3.0 usługi Azure Storage. Następnie uruchom następujące polecenie z poziomu konsoli Menedżera pakietów NuGet.

    ```powershell
    Install-Package WindowsAzure.Storage -Version 4.3.0
    ```

    Skompiluj projekt. Usuń Azure.Storage zestawu w wersji > 4.3.0 z folderu bin\Debug. Utwórz plik zip, pliki binarne i pliku PDB. Zastąp starego pliku zip wskazanego w kontenerze obiektów blob (customactivitycontainer). Uruchom ponownie wycinków, które nie powiodło się (kliknij prawym przyciskiem myszy wycinka i kliknij przycisk Uruchom).
8. Działanie niestandardowe nie korzysta z **app.config** plik z pakietu. W związku z tym jeśli kod odczyta dowolne parametry połączenia z pliku konfiguracji, działa w czasie wykonywania. Najlepszym rozwiązaniem, gdy za pomocą usługi Azure Batch jest przechowywanie wszystkich danych poufnych w **Azure KeyVault**, użyć jednostki usługi oparte na certyfikatach, aby chronić **keyvault**i dystrybucja certyfikatu do usługi Azure Batch Pula. Niestandardowe działanie .NET będzie miało w takiej sytuacji dostęp do danych poufnych z magazynu KeyVault w czasie uruchomienia. To rozwiązanie jest to rozwiązanie ogólne i mogą być skalowane do dowolnego typu danych poufnych, nie tylko parametrów połączenia.

   Ma ułatwić obejście problemu (ale nie najlepszym rozwiązaniem jest): można utworzyć **połączonej usługi Azure SQL** ustawienia parametrów połączenia, tworzenie zestawu danych, który korzysta z połączonej usługi i połączyć w łańcuch zestawu danych jako fikcyjnego wejściowego zestawu danych do niestandardowe działanie platformy .NET. Mogą uzyskiwać dostęp do parametrów połączenia połączonej usługi w kodzie działań niestandardowych.

## <a name="update-custom-activity"></a>Niestandardowe działanie aktualizacji
Po zaktualizowaniu kodu niestandardowego działania, skompiluj go, a następnie przekaż plik zip, który zawiera nowe pliki binarne do magazynu obiektów blob.

## <a name="appdomain-isolation"></a>Izolacja domeny aplikacji
Zobacz [obejmujące wiele przykładowych AppDomain](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/CrossAppDomainDotNetActivitySample) , dowiesz się, jak utworzyć niestandardowe działanie, które nie jest ograniczony do wersji zestawu, używanych przez uruchamianie usługi Data Factory (przykład: WindowsAzure.Storage v4.3.0, Newtonsoft.Json v6.0.x, etc.).

## <a name="access-extended-properties"></a>Dostęp do właściwości rozszerzone
Można zadeklarować właściwości rozszerzone w działaniu, w formacie JSON, jak pokazano w następującym przykładzie:

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

W tym przykładzie istnieją dwie właściwości rozszerzone: **SliceStart** i **DataFactoryName**. Wartość parametru SliceStart opiera się na zmiennej systemowej SliceStart. Zobacz [zmiennych systemowych](data-factory-functions-variables.md) listę zmiennych obsługiwany system. Wartość DataFactoryName jest ustalony na CustomActivityFactory.

Aby przejść do nich rozszerzone właściwości w **Execute** metody, użyj kodu podobne do następującego kodu:

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

## <a name="auto-scaling-of-azure-batch"></a>Automatyczne skalowanie usługi Azure Batch
Możesz również utworzyć puli usługi Azure Batch przy użyciu **skalowania automatycznego** funkcji. Na przykład można utworzyć puli usługi azure batch przy użyciu 0 dedykowanych maszyn wirtualnych i formułę skalowania automatycznego na podstawie liczby oczekujących zadań.

W tym miejscu wartość przykładowa formuła realizuje następujące zachowanie: Podczas tworzenia puli, rozpoczynają się one od 1 maszyna wirtualna. Metryki $PendingTasks definiuje liczbę zadań uruchamiania + aktywny (kolejki) stanu.  Formuła wyszukuje średnią liczbę zadań oczekujących w ostatnich 180 sekund i ustawia odpowiednio TargetDedicated. Zapewnia, że TargetDedicated nigdy nie trafiają ponad 25 maszyn wirtualnych. Tak, ponieważ nowe zadania są przesyłane, puli automatycznie rozszerza się w i jako zadania, maszyn wirtualnych stają się bezpłatne pojedynczo, i automatyczne skalowanie zmniejsza tych maszyn wirtualnych. startingNumberOfVMs i maxNumberofVMs mogą być dostosowane do potrzeb.

Formułę skalowania automatycznego:

```
startingNumberOfVMs = 1;
maxNumberofVMs = 25;
pendingTaskSamplePercent = $PendingTasks.GetSamplePercent(180 * TimeInterval_Second);
pendingTaskSamples = pendingTaskSamplePercent < 70 ? startingNumberOfVMs : avg($PendingTasks.GetSample(180 * TimeInterval_Second));
$TargetDedicated=min(maxNumberofVMs,pendingTaskSamples);
```

Zobacz [automatyczne skalowanie węzłów obliczeniowych w puli usługi Azure Batch](../../batch/batch-automatic-scaling.md) Aby uzyskać szczegółowe informacje.

Jeśli pula używa domyślnie [autoScaleEvaluationInterval](https://msdn.microsoft.com/library/azure/dn820173.aspx), usługa Batch może potrwać 15 – 30 minut, aby przygotować maszynę Wirtualną przed uruchomieniem działania niestandardowego.  Jeśli pula używa różnych autoScaleEvaluationInterval, usługa Batch może potrwać autoScaleEvaluationInterval + 10 minut.


## <a name="create-a-custom-activity-by-using-net-sdk"></a>Utwórz niestandardowe działanie przy użyciu zestawu .NET SDK
W instruktażu, w tym artykule Tworzenie fabryki danych obejmującej potok, który używa niestandardowego działania przy użyciu witryny Azure portal. Poniższy kod przedstawia sposób tworzenia fabryki danych przy użyciu zestawu SDK .NET zamiast tego. Można znaleźć więcej szczegółów na temat przy użyciu zestawu SDK do programowego tworzenia potoków w [tworzenie potoku za pomocą działania kopiowania przy użyciu interfejsu API platformy .NET](data-factory-copy-activity-tutorial-using-dotnet-api.md) artykułu.

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
[Usługi Azure Data Factory — środowisko lokalne](https://github.com/gbrueckl/Azure.DataFactory.LocalEnvironment) przykład w witrynie GitHub zawiera narzędzia, która pozwala na debugowanie niestandardowe działania programu .NET w programie Visual Studio.

## <a name="sample-custom-activities-on-github"></a>Przykładowe niestandardowe działania w witrynie GitHub
| Sample | Jakie niestandardowe działanie robi |
| --- | --- |
| [Narzędzie do pobierania danych HTTP](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/HttpDataDownloaderSample). |Pobiera dane z punktu końcowego HTTP do usługi Azure Blob Storage przy użyciu niestandardowych C# działania w usłudze Data Factory. |
| [Próbka analizy tonacji w usłudze Twitter](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/TwitterAnalysisSample-CustomC%23Activity) |Wywołuje się, Azure Machine Learning studio modelu i analiza tonacji oceniania, prognozowania itp. |
| [Uruchom skrypt języka R](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample). |Wywołuje skrypt języka R, uruchamiając Rscript.exe.{0}nie w klastrze usługi HDInsight, w którym jest już zainstalowany R na nim. |
| [Krzyżowe elementu AppDomain działanie platformy .NET](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/CrossAppDomainDotNetActivitySample) |Używa innego zestawu w wersjach od takich, które jest używane przez uruchamianie usługi Data Factory |
| [Ponowne przetwarzanie modeli w usługach Azure Analysis Services](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/AzureAnalysisServicesProcessSample) |  Ponownego przetwarzania modelu w usłudze Azure Analysis Services. |

[batch-net-library]: ../../batch/batch-dotnet-get-started.md
[batch-create-account]: ../../batch/batch-account-create-portal.md
[batch-technical-overview]: ../../batch/batch-technical-overview.md
[batch-get-started]: ../../batch/batch-dotnet-get-started.md
[use-custom-activities]: data-factory-use-custom-activities.md
[troubleshoot]: data-factory-troubleshoot.md
[data-factory-introduction]: data-factory-introduction.md
[azure-powershell-install]: https://github.com/Azure/azure-sdk-tools/releases


[developer-reference]: https://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: https://go.microsoft.com/fwlink/?LinkId=517456

[new-azure-batch-account]: https://msdn.microsoft.com/library/mt125880.aspx
[new-azure-batch-pool]: https://msdn.microsoft.com/library/mt125936.aspx
[azure-batch-blog]: https://blogs.technet.com/b/windowshpc/archive/2014/10/28/using-azure-powershell-to-manage-azure-batch-account.aspx

[nuget-package]: https://go.microsoft.com/fwlink/?LinkId=517478
[adf-developer-reference]: https://go.microsoft.com/fwlink/?LinkId=516908
[azure-preview-portal]: https://portal.azure.com/

[adfgetstarted]: data-factory-copy-data-from-azure-blob-storage-to-sql-database.md
[hivewalkthrough]: data-factory-data-transformation-activities.md

[image-data-factory-output-from-custom-activity]: ./media/data-factory-use-custom-activities/OutputFilesFromCustomActivity.png

[image-data-factory-download-logs-from-custom-activity]: ./media/data-factory-use-custom-activities/DownloadLogsFromCustomActivity.png
