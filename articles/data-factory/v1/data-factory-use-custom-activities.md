---
title: Korzystanie z działań niestandardowych w potoku usługi Azure Data Factory
description: Dowiedz się, jak tworzyć niestandardowe działania i używać ich w potoku usługi Azure Data Factory.
services: data-factory
documentationcenter: ''
ms.assetid: 8dd7ba14-15d2-4fd9-9ada-0b2c684327e9
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
author: nabhishek
ms.author: abnarain
manager: anandsub
robots: noindex
ms.openlocfilehash: 54cb06f1c77ab68818d8531b57d6eb936deda8d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79265729"
---
# <a name="use-custom-activities-in-an-azure-data-factory-pipeline"></a>Korzystanie z działań niestandardowych w potoku usługi Azure Data Factory
> [!div class="op_single_selector" title1="Wybierz wersję używanej usługi Data Factory:"]
> * [Wersja 1](data-factory-use-custom-activities.md)
> * [Wersja 2 (bieżąca wersja)](../transform-data-using-dotnet-custom-activity.md)

> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory. Jeśli używasz bieżącej wersji usługi Data Factory, zobacz [Działania niestandardowe w wersji 2](../transform-data-using-dotnet-custom-activity.md).

Istnieją dwa typy działań, których można użyć w potoku usługi Azure Data Factory.

- [Działania związane z przenoszeniem danych](data-factory-data-movement-activities.md) w celu przenoszenia danych między [obsługiwanymi magazynami danych źródłowych i magazynów danych ujścia.](data-factory-data-movement-activities.md#supported-data-stores-and-formats)
- [Działania transformacji danych do](data-factory-data-transformation-activities.md) przekształcania danych przy użyciu usług obliczeniowych, takich jak Usługi Azure HDInsight, Usługa Azure Batch i Azure Machine Learning.

Aby przenieść dane do/z magazynu danych, który usługa Data Factory nie obsługuje, utwórz **działanie niestandardowe** z własną logiką przenoszenia danych i użyj działania w potoku. Podobnie, aby przekształcić/przetworzyć dane w sposób, który nie jest obsługiwany przez fabrykę danych, utwórz działanie niestandardowe z własną logiką transformacji danych i użyj działania w potoku.

Można skonfigurować działanie niestandardowe do uruchamiania na puli **usług Azure Batch** maszyn wirtualnych. Korzystając z usługi Azure Batch, można użyć tylko istniejącej puli usługi Azure Batch.

Poniższy instruktaż zawiera instrukcje krok po kroku dotyczące tworzenia niestandardowego działania platformy .NET i używania działania niestandardowego w potoku. Instruktaż używa usługi połączonej **usługi Azure Batch.**

> [!IMPORTANT]
> - Nie można użyć bramy zarządzania danymi z działania niestandardowego, aby uzyskać dostęp do lokalnych źródeł danych. Obecnie [brama zarządzania danymi](data-factory-data-management-gateway.md) obsługuje tylko działanie kopiowania i działanie procedury składowanej w fabryce danych.

## <a name="walkthrough-create-a-custom-activity"></a>Instruktaż: tworzenie działania niestandardowego
### <a name="prerequisites"></a>Wymagania wstępne
* Visual Studio 2012/2013/2015/2017
* Pobieranie i instalowanie [pakietu Azure .NET SDK](https://azure.microsoft.com/downloads/)

### <a name="azure-batch-prerequisites"></a>Wymagania wstępne usługi Azure Batch
W instruktażu można uruchomić niestandardowe działania platformy .NET przy użyciu usługi Azure Batch jako zasobu obliczeniowego. **Usługa Azure Batch** to usługa platformy do wydajnego uruchamiania aplikacji komputerowych na dużą skalę w chmurze. Usługa Azure Batch planuje pracę intensywnie korzystającą z obliczeń do uruchomienia na zarządzanej **kolekcji maszyn wirtualnych**i może automatycznie skalować zasoby obliczeniowe w celu zaspokojenia potrzeb zadań. Zobacz artykuł [o podstawach usługi Azure Batch,][batch-technical-overview] aby uzyskać szczegółowe omówienie usługi Azure Batch.

W samouczku utwórz konto usługi Azure Batch z pulą maszyn wirtualnych. Oto konkretne kroki:

1. Utwórz **konto usługi Azure Batch** przy użyciu portalu [Azure.](https://portal.azure.com) Aby uzyskać instrukcje, zobacz [Tworzenie konta usługi Azure Batch i zarządzanie nim.][batch-create-account]
2. Zanotuj nazwę konta usługi Azure Batch, klucz konta, identyfikator URI i nazwę puli. Potrzebne są do utworzenia usługi połączonej usługi Azure Batch.
    1. Na stronie głównej konta usługi Azure Batch zostanie wyświetlony `https://myaccount.westus.batch.azure.com`adres **URL** w następującym formacie: . W tym przykładzie **myaccount** to nazwa konta usługi Azure Batch. Identyfikator URI używany w definicji usługi połączonej jest adresem URL bez nazwy konta. Na przykład: `https://<region>.batch.azure.com`.
    2. Kliknij polecenie **Klawisze** w menu po lewej stronie i skopiuj **podstawowy klucz dostępu**.
    3. Aby użyć istniejącej puli, kliknij w menu pozycję **Pule** i zanotuj **identyfikator** puli. Jeśli nie masz istniejącej puli, przejdź do następnego kroku.
2. Utwórz **pulę partii azure**.

   1. W [witrynie Azure portal](https://portal.azure.com)kliknij pozycję **Przeglądaj** w menu po lewej stronie, a następnie kliknij polecenie **Konta wsadowe**.
   2. Wybierz swoje konto usługi Azure Batch, aby otworzyć blok **konto wsadowe.**
   3. Kliknij **kafelek Baseny.**
   4. W bloku **Baseny** kliknij przycisk Dodaj na pasku narzędzi, aby dodać pulę.
      1. Wprowadź identyfikator puli (identyfikator puli). Zwróć uwagę na **identyfikator puli;** jest potrzebny podczas tworzenia rozwiązania Data Factory.
      2. Określ **system Windows Server 2012 R2** dla ustawienia Rodzina systemów operacyjnych.
      3. Wybierz **warstwę cenową węzła**.
      4. Wprowadź **2** jako wartość dla ustawienia **Dedykowane dla celu.**
      5. Wprowadź **2** jako wartość dla ustawienia **Maksymalna liczba zadań na węzeł.**
   5. Kliknij przycisk **OK**, aby utworzyć pulę.
   6. Zanotuj **identyfikator** puli.

### <a name="high-level-steps"></a>Kroki ogólne
Oto dwa kroki wysokiego poziomu, które można wykonać w ramach tego przewodnika:

1. Utwórz działanie niestandardowe, które zawiera prostą logikę przekształcania/przetwarzania danych.
2. Utwórz fabrykę danych platformy Azure za pomocą potoku, który używa działania niestandardowego.

### <a name="create-a-custom-activity"></a>Tworzenie działania niestandardowego
Aby utworzyć działanie niestandardowe platformy .NET, utwórz projekt **biblioteki klas platformy .NET** z klasą implementuują ten interfejs **IDotNetActivity.** Ten interfejs ma tylko jedną metodę: [Execute](https://msdn.microsoft.com/library/azure/mt603945.aspx) i jego podpis jest:

```csharp
public IDictionary<string, string> Execute(
    IEnumerable<LinkedService> linkedServices,
    IEnumerable<Dataset> datasets,
    Activity activity,
    IActivityLogger logger)
```

Metoda przyjmuje cztery parametry:

- **linkedServices**. Ta właściwość jest wyliczającą listę usług połączonych magazynu danych, do których odwołują się zestawy danych wejściowych/wyjściowych dla działania.
- **zbiorów danych**. Ta właściwość jest wyliczona lista zestawów danych wejściowych/wyjściowych dla działania. Ten parametr służy do uzyskania lokalizacji i schematów zdefiniowanych przez wejściowe i wyjściowe zestawy danych.
- **aktywności .** Ta właściwość reprezentuje bieżące działanie. Może służyć do uzyskiwania dostępu do właściwości rozszerzonych skojarzonych z działaniem niestandardowym. Szczegółowe informacje można znaleźć w programie [Access extended properties.](#access-extended-properties)
- **rejestratora**. Ten obiekt umożliwia pisanie komentarzy debugowania, które powierzchni w dzienniku użytkownika dla potoku.

Metoda zwraca słownik, który może służyć do łańcucha działań niestandardowych razem w przyszłości. Ta funkcja nie jest jeszcze zaimplementowana, więc zwróć pusty słownik z metody.

### <a name="procedure"></a>Procedura
1. Utwórz projekt **biblioteki klas .NET.**
   <ol type="a">
     <li>Uruchom program Visual Studio.</li>
     <li>Kliknij pozycję <b>Plik</b>, wskaż polecenie <b>Nowy</b> i kliknij pozycję <b>Projekt</b>.</li>
     <li>Rozwiń węzeł <b>Szablony</b> i wybierz opcję <b>Visual C#</b>. W tym instruktażu można użyć języka C#, ale można użyć dowolnego języka .NET do opracowania działania niestandardowego.</li>
     <li>Wybierz <b>bibliotekę klas</b> z listy typów projektów po prawej stronie. W programie Visual Studio wybierz pozycję <b>Biblioteka klas (.NET Framework)</b> </li>
     <li>Wprowadź <b>MyDotNetActivity</b> dla <b>nazwy</b>.</li>
     <li>Wybierz <b>C:\ADFGetStarted</b> dla <b>lokalizacji</b>.</li>
     <li>Kliknij przycisk <b>OK</b>, aby utworzyć projekt.</li>
   </ol>

2. Kliknij pozycję **Narzędzia**, wskaż pozycję **Menedżer pakietów NuGet**, a następnie kliknij pozycję **Konsola menedżera pakietów**.

3. W konsoli Menedżera pakietów wykonaj następujące polecenie, aby zaimportować **microsoft.Azure.Management.DataFactories**.

    ```powershell
    Install-Package Microsoft.Azure.Management.DataFactories
    ```
4. Zaimportuj pakiet Azure **Storage** NuGet do projektu.

    ```powershell
    Install-Package WindowsAzure.Storage -Version 4.3.0
    ```

    > [!IMPORTANT]
    > Program data factory service launcher wymaga wersji 4.3 systemu WindowsAzure.Storage. Jeśli dodasz odwołanie do nowszej wersji zestawu usługi Azure Storage w projekcie działania niestandardowego, zostanie wyświetlony błąd podczas wykonywania działania. Aby rozwiązać ten problem, zobacz [sekcję izolacji domeny aplikacji.](#appdomain-isolation)
5. Dodaj następujące **instrukcje do** pliku źródłowego w projekcie.

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
6. Zmień nazwę **obszaru nazw** na **MyDotNetActivityNS**.

    ```csharp
    namespace MyDotNetActivityNS
    ```
7. Zmień nazwę klasy na **MyDotNetActivity** i wyjdź ją z interfejsu **IDotNetActivity,** jak pokazano w poniższym fragmentie kodu:

    ```csharp
    public class MyDotNetActivity : IDotNetActivity
    ```
8. Zaimplementuj (Dodaj) **Execute** metody interfejsu **IDotNetActivity** do **MyDotNetActivity** klasy i skopiować następujący przykładowy kod do metody.

    Poniższy przykład zlicza liczbę wystąpień wyszukiwanego terminu ("Microsoft") w każdym obiekcie blob skojarzonym z plasterkiem danych.

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
9. Dodaj następujące metody pomocnicze:

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

    Metoda GetFolderPath zwraca ścieżkę do folderu, do który wskazuje zestaw danych, a metoda GetFileName zwraca nazwę obiektu blob/pliku, na który wskazuje zestaw danych. Jeśli masz folderPath definiuje przy użyciu zmiennych, takich jak {Rok}, {Miesiąc}, {Dzień} itp., metoda zwraca ciąg, jak to jest bez zastępowania ich wartościami środowiska uruchomieniowego. Zobacz [sekcję Właściwości rozszerzone programu Access,](#access-extended-properties) aby uzyskać szczegółowe informacje na temat uzyskiwania dostępu do slicestart, SliceEnd itp.

    ```JSON
    "name": "InputDataset",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "fileName": "file.txt",
            "folderPath": "adftutorial/inputfolder/",
    ```

    Metoda Oblicz oblicza liczbę wystąpień słowa kluczowego Microsoft w plikach wejściowych (obiektach blob w folderze). Wyszukiwany termin ("Microsoft") jest zakodowany na miejscu w kodzie.
10. Skompiluj projekt. Kliknij **polecenie Zbuduj** z menu i kliknij polecenie **Buduj rozwiązanie**.

    > [!IMPORTANT]
    > Ustaw wersję 4.5.2 programu .NET Framework jako platformę docelową dla projektu: kliknij prawym przyciskiem myszy projekt i kliknij polecenie **Właściwości,** aby ustawić platformę docelową. Usługa Data Factory nie obsługuje działań niestandardowych skompilowanych z wersjami programu .NET Framework późniejszymi niż 4.5.2.

11. Uruchom **Eksploratora Windows**i przejdź do **folderu bin\debug** lub **bin\release** w zależności od typu kompilacji.
12. Utwórz plik zip **MyDotNetActivity.zip** zawierający wszystkie \<pliki\>binarne w folderze projektu \bin\Debug folderu. Dołącz plik **MyDotNetActivity.pdb,** aby uzyskać dodatkowe szczegóły, takie jak numer wiersza w kodzie źródłowym, który spowodował problem, jeśli wystąpił błąd.

    > [!IMPORTANT]
    > Wszystkie pliki w archiwum ZIP działania niestandardowego muszą znajdować się na **najwyższym poziomie**, bez podfolderów.

    ![Wyjściowe pliki binarne](./media/data-factory-use-custom-activities/Binaries.png)
14. Utwórz kontener obiektów blob o nazwie **customactivitycontainer,** jeśli jeszcze nie istnieje.
15. Przekaż MyDotNetActivity.zip jako obiekt blob do customactivitycontainer w magazynie obiektów blob **ogólnego przeznaczenia** platformy Azure (nie hot/cool magazynu obiektów blob), który jest określany przez AzureStorageLinkedService.

> [!IMPORTANT]
> Jeśli dodasz ten projekt działania platformy .NET do rozwiązania w programie Visual Studio, które zawiera projekt data factory i dodasz odwołanie do projektu działania .NET z projektu aplikacji Data Factory, nie trzeba wykonywać dwóch ostatnich kroków ręcznego tworzenia pliku błyskawicznego i przekazywanie go do magazynu obiektów blob ogólnego przeznaczenia platformy Azure. Podczas publikowania jednostek usługi Data Factory przy użyciu programu Visual Studio te kroki są automatycznie wykonywane przez proces publikowania. Aby uzyskać więcej informacji, zobacz [projekt fabryki danych w](#data-factory-project-in-visual-studio) programie Visual Studio sekcji.

## <a name="create-a-pipeline-with-custom-activity"></a>Tworzenie potoku z działaniem niestandardowym
Utworzono działanie niestandardowe i prześliłeś plik zip z plikami binarnymi do kontenera obiektów blob na koncie usługi Azure Storage **ogólnego przeznaczenia.** W tej sekcji utworzysz fabrykę danych platformy Azure z potokiem, który używa działania niestandardowego.

Wejściowy zestaw danych dla działania niestandardowego reprezentuje obiekty blob (pliki) w folderze customactivityinput kontenera adftutorial w magazynie obiektów blob. Wyjściowy zestaw danych dla działania reprezentuje wyjściowe obiekty blob w folderze customactivityoutput kontenera adftutorial w magazynie obiektów blob.

Utwórz plik **file.txt** z następującą zawartością i przekaż go do folderu **customactivityinput** **kontenera adftutorial.** Utwórz kontener adftutorial, jeśli jeszcze nie istnieje.

```
test custom activity Microsoft test custom activity Microsoft
```

Folder wejściowy odpowiada wycinku w usłudze Azure Data Factory, nawet jeśli folder ma dwa lub więcej plików. Gdy każdy plasterek jest przetwarzany przez potok, działanie niestandardowe iteruje za pośrednictwem wszystkich obiektów blob w folderze wejściowym dla tego plasterka.

Widzisz jeden plik wyjściowy z w folderze adftutorial\customactivityoutput z co najmniej jednym wierszem (tak samo jak liczba obiektów blob w folderze wejściowym):

```
2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2016-11-16-00/file.txt.
```


Wykonaj następujące czynności opisane w tej sekcji:

1. Tworzenie **fabryki danych**.
2. Tworzenie **połączonych usług** dla puli partii Azure maszyn wirtualnych, na których jest uruchamiane działanie niestandardowe i usługi Azure Storage, która przechowuje obiekty blob wejścia/wyjścia.
3. Tworzenie danych wejściowych i **wyjściowych,** które reprezentują dane wejściowe i wyjściowe działania niestandardowego.
4. Utwórz **potok,** który używa działania niestandardowego.

> [!NOTE]
> Utwórz **plik.txt** i przekaż go do kontenera obiektów blob, jeśli jeszcze tego nie zrobiono. Zobacz instrukcje w powyższej sekcji.

### <a name="step-1-create-the-data-factory"></a>Krok 1: Tworzenie fabryki danych
1. Po zalogowaniu się do witryny Azure portal wykonaj następujące czynności:
   1. Kliknij **pozycję Utwórz zasób** w menu po lewej stronie.
   2. Kliknij **pozycję Dane + Analiza** w **nowym** bloku.
   3. Kliknij przycisk **Fabryka danych** w bloku **Analiza danych**.

      ![Nowe menu Fabryka danych platformy Azure](media/data-factory-use-custom-activities/new-azure-data-factory-menu.png)
2. W **bloku Nowa fabryka danych** wprowadź **CustomActivityFactory** dla name. Nazwa fabryki danych Azure musi być globalnie unikatowa. Jeśli zostanie wyświetlony błąd: **Nazwa fabryki danych "CustomActivityFactory" nie jest dostępna,** zmień nazwę fabryki danych (na przykład **yournameCustomActivityFactory)** i spróbuj utworzyć ponownie.

    ![Nowy blok usługi Azure Data Factory](media/data-factory-use-custom-activities/new-azure-data-factory-blade.png)
3. Kliknij **pozycję NAZWA GRUPY ZASOBÓW**i wybierz istniejącą grupę zasobów lub grupę zasobów.
4. Sprawdź, czy używasz poprawnej **subskrypcji** i **regionu,** w którym ma zostać utworzona fabryka danych.
5. Kliknij przycisk **Utwórz** w bloku **Nowa fabryka danych**.
6. Zobaczysz, że fabryka danych jest tworzona na **pulpicie nawigacyjnym** witryny Azure portal.
7. Po pomyślnym utworzeniu fabryki danych zobaczysz bloku Fabryka danych, który pokazuje zawartość fabryki danych.

    ![Blok Fabryka danych](media/data-factory-use-custom-activities/data-factory-blade.png)

### <a name="step-2-create-linked-services"></a>Krok 2: Tworzenie połączonych usług
Połączone usługi łączą magazyny danych lub usługi obliczeniowe z fabryką danych Azure. W tym kroku należy połączyć swoje konto usługi Azure Storage i konto usługi Azure Batch z fabryką danych.

#### <a name="create-azure-storage-linked-service"></a>Tworzenie połączonej usługi Azure Storage
1. Kliknij **przycisk Autor i rozmieścij** kafelek na bloku **DATA FACTORY** dla **customactivityFactory**. Pojawi się Edytor fabryki danych.
2. Kliknij **pozycję Nowy magazyn danych** na pasku poleceń i wybierz pozycję Magazyn platformy **Azure**. Powinien zostać wyświetlony skrypt JSON do tworzenia połączonej usługi Azure Storage w edytorze.

    ![Nowy magazyn danych — usługa Azure Storage](media/data-factory-use-custom-activities/new-data-store-menu.png)
3. Zastąp `<accountname>` nazwą swojego `<accountkey>` konta magazynu platformy Azure i kluczem dostępu konta magazynu platformy Azure. Aby dowiedzieć się, jak uzyskać klucz dostępu do magazynu, zobacz [Zarządzanie kluczami dostępu do konta magazynu](../../storage/common/storage-account-keys-manage.md).

    ![Usługa polubiona usługą Azure Storage](media/data-factory-use-custom-activities/azure-storage-linked-service.png)
4. Kliknij przycisk **Wdróż** na pasku poleceń, aby wdrożyć połączoną usługę.

#### <a name="create-azure-batch-linked-service"></a>Tworzenie połączonej usługi Azure Batch
1. W Edytorze fabryki danych kliknij **... Więcej** informacji na pasku poleceń kliknij pozycję **Nowe obliczenia**, a następnie wybierz z menu pozycję **Azure Batch.**

    ![Nowe obliczenia — usługa Azure Batch](media/data-factory-use-custom-activities/new-azure-compute-batch.png)
2. W skrypcie JSON wprowadzono następujące zmiany:

   1. Określ nazwę konta usługi Azure Batch dla właściwości **accountName.** Adres **URL** z **bloku konta usługi Azure** `http://accountname.region.batch.azure.com`Batch jest w następującym formacie: . Dla **batchUri** właściwości w JSON, należy `accountname.` usunąć z adresu `accountname` URL `accountName` i użyć dla właściwości JSON.
   2. Określ klucz konta usługi Azure Batch dla właściwości **accessKey.**
   3. Określ nazwę puli utworzonej jako część wymagań wstępnych dla **właściwości poolName.** Można również określić identyfikator puli zamiast nazwy puli.
   4. Określ identyfikator URI partii azure dla **właściwości batchUri.** Przykład: `https://westus.batch.azure.com`.
   5. Określ **azurestoragelinkedservice** dla **właściwości linkedServiceName.**

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

       Dla **właściwości poolName** można również określić identyfikator puli zamiast nazwy puli.

### <a name="step-3-create-datasets"></a>Krok 3: Tworzenie zestawów danych
W tym kroku należy utworzyć zestawy danych do reprezentowania danych wejściowych i wyjściowych.

#### <a name="create-input-dataset"></a>Tworzenie wejściowego zestawu danych
1. W **edytorze** dla fabryki danych kliknij **... Więcej** informacji na pasku poleceń kliknij pozycję **Nowy zestaw danych**, a następnie wybierz z menu rozwijanego pozycję Magazyn obiektów **Blob platformy Azure.**
2. Zastąp JSON w prawym okienku następującym fragmentem kodu JSON:

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

   Potok można utworzyć w dalszej części tego przewodnika z czasem rozpoczęcia: 2016-11-16T00:00:00Z i czas zakończenia: 2016-11-16T05:00:00Z. Dane są planowane do produkcji danych co godzinę, więc istnieje pięć wycinków wejściowych/wyjściowych (między 00:00:00 -> **00** **05:00:00).**

   **Częstotliwość** i **interwał** dla wejściowego zestawu danych jest ustawiona na **Godzina** i **1**, co oznacza, że plasterek wejściowy jest dostępny co godzinę. W tym przykładzie jest to ten sam plik (file.txt) w pliku intputfolder.

   Oto godziny rozpoczęcia dla każdego plasterka, który jest reprezentowany przez SliceStart zmiennej systemowej w powyższym fragmentie JSON.
3. Kliknij **pozycję Wdrażanie** na pasku narzędzi, aby utworzyć i **wdrożyć zestaw danych wejściowych**. Upewnij się, że na pasku tytułu w edytorze wyświetlany jest komunikat **TABELA ZOSTAŁA UTWORZONA POMYŚLNIE**.

#### <a name="create-an-output-dataset"></a>Tworzenie wyjściowego zestawu danych
1. W **edytorze Data Factory**kliknij **... Więcej** informacji na pasku poleceń kliknij pozycję **Nowy zestaw danych**, a następnie wybierz pozycję Magazyn obiektów **Blob platformy Azure**.
2. Zastąp skrypt JSON w prawym okienku następującym skryptem JSON:

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

     Lokalizacja wyjściowa jest **adftutorial / customactivityoutput /** i nazwa pliku wyjściowego jest yyyy-MM-dd-HH.txt gdzie yyyy-MM-dd-HH jest rok, miesiąc, data i godzina plasterka produkowane. Zobacz [odwołanie dewelopera, aby][adf-developer-reference] uzyskać szczegółowe informacje.

    Dla każdego wycinka wejściowego jest generowany wyjściowy obiekt blob/file. Oto jak plik wyjściowy jest nazwany dla każdego plasterka. Wszystkie pliki wyjściowe są generowane w jednym folderze **wyjściowym: adftutorial\customactivityoutput**.

   | Plasterek | Godzina rozpoczęcia | Plik wyjściowy |
   |:--- |:--- |:--- |
   | 1 |2016-11-16T00:00:00 |2016-11-16-00.txt |
   | 2 |2016-11-16T01:00:00 |2016-11-16-01.txt |
   | 3 |2016-11-16T02:00:00 |2016-11-16-02.txt |
   | 4 |2016-11-16T03:00:00 |2016-11-16-03.txt |
   | 5 |2016-11-16T04:00:00 |2016-11-16-04.txt |

    Pamiętaj, że wszystkie pliki w folderze wejściowym są częścią plasterka z wyżej wymienionymi godzinami rozpoczęcia. Podczas przetwarzania tego wycinka działanie niestandardowe skanuje każdy plik i tworzy wiersz w pliku wyjściowym z liczbą wystąpień wyszukiwanego hasła ("Microsoft"). Jeśli w folderze wejściowym znajdują się trzy pliki, w pliku wyjściowym znajdują się trzy wiersze dla każdego plasterka godzinowego: 2016-11-16-00.txt, 2016-11-16:01:00:00.txt itp.
3. Aby wdrożyć **zestaw danych wyjściowych,** kliknij przycisk **Wdrażanie** na pasku poleceń.

### <a name="create-and-run-a-pipeline-that-uses-the-custom-activity"></a>Tworzenie i uruchamianie potoku, który używa działania niestandardowego
1. W Edytorze fabryki danych kliknij **... Więcej**, a następnie wybierz **pozycję Nowy potok** na pasku poleceń.
2. Zastąp JSON w prawym okienku następującym skryptem JSON:

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

   * **Współbieżność** jest ustawiona na **2,** dzięki czemu dwa plasterki są przetwarzane równolegle przez 2 maszyny wirtualne w puli usługi Azure Batch.
   * Istnieje jedno działanie w sekcji działania i jest typu: **DotNetActivity**.
   * **Nazwa zestawu** jest ustawiona na nazwę biblioteki DLL: **MyDotnetActivity.dll**.
   * **EntryPoint** jest ustawiony na **MyDotNetActivityNS.MyDotNetActivity**.
   * **PackageLinkedService** jest ustawiona na **AzureStorageLinkedService,** który wskazuje magazyn obiektów blob, który zawiera plik zip działania niestandardowego. Jeśli używasz różnych kont usługi Azure Storage dla plików wejściowych/wyjściowych i pliku zip działania niestandardowego, należy utworzyć inną usługę połączoną usługi Azure Storage. W tym artykule przyjęto założenie, że używasz tego samego konta usługi Azure Storage.
   * **PackageFile** jest ustawiony na **customactivitycontainer/MyDotNetActivity.zip**. Jest w formacie: containerforthezip/nameofthezip.zip.
   * Działanie niestandardowe przyjmuje **InputDataset** jako dane wejściowe i **OutputDataset** jako dane wyjściowe.
   * Właściwość linkedServiceName działania niestandardowego wskazuje na **usługę AzureBatchLinkedService**, która informuje usługę Azure Data Factory, że działanie niestandardowe musi być uruchamiane na maszynach wirtualnych usługi Azure Batch.
   * **isPaused** właściwość jest ustawiona na **false** domyślnie. Potok działa natychmiast w tym przykładzie, ponieważ wycinki rozpoczynają się w przeszłości. Można ustawić tę właściwość true, aby wstrzymać potok i ustawić go z powrotem do false, aby ponownie uruchomić.
   * Czas **rozpoczęcia** i **zakończenia** są **pięć** godzin od siebie i plasterki są produkowane co godzinę, więc pięć plasterków są produkowane przez potoku.
3. Aby wdrożyć potok, kliknij przycisk **Wdrażanie** na pasku poleceń.

### <a name="monitor-the-pipeline"></a>Monitorowanie potoku
1. W bloku Fabryka danych w witrynie Azure portal kliknij pozycję **Diagram**.

    ![Kafelek Diagram](./media/data-factory-use-custom-activities/DataFactoryBlade.png)
2. W widoku diagramu kliknij teraz zestaw danych wyjściowych.

    ![Widok diagramu](./media/data-factory-use-custom-activities/diagram.png)
3. Powinieneś zobaczyć, że pięć wycinków danych wyjściowych są w stanie Gotowy. Jeśli nie są one w stanie gotowości, nie zostały jeszcze wyprodukowane.

   ![Plasterki wyjściowe](./media/data-factory-use-custom-activities/OutputSlices.png)
4. Sprawdź, czy pliki wyjściowe są generowane w magazynie obiektów blob w kontenerze **adftutorial.**

   ![dane wyjściowe z działania niestandardowego][image-data-factory-output-from-custom-activity]
5. Po otwarciu pliku wyjściowego, powinien być widoczny dane wyjściowe podobne do następujących danych wyjściowych:

    ```
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2016-11-16-00/file.txt.
    ```
6. Użyj [poleceń][azure-preview-portal] cmdlet azure portal lub Azure PowerShell do monitorowania fabryki danych, potoków i zestawów danych. Wiadomości z **ActivityLogger** w kodzie dla działania niestandardowego w dziennikach (w szczególności user-0.log), które można pobrać z portalu lub przy użyciu poleceń cmdlet.

   ![pobieranie dzienników z aktywności niestandardowej][image-data-factory-download-logs-from-custom-activity]

Zobacz [Monitorowanie i zarządzanie potokami, aby](data-factory-monitor-manage-pipelines.md) uzyskać szczegółowe kroki monitorowania zestawów danych i potoków.

## <a name="data-factory-project-in-visual-studio"></a>Projekt data factory w programie Visual Studio
Można tworzyć i publikować jednostki usługi Data Factory przy użyciu programu Visual Studio zamiast korzystania z witryny Azure portal. Aby uzyskać szczegółowe informacje na temat tworzenia i publikowania jednostek usługi Data Factory przy użyciu programu Visual Studio, zobacz [Tworzenie pierwszego potoku przy użyciu programu Visual Studio](data-factory-build-your-first-pipeline-using-vs.md) i [kopiowanie danych z obiektu blob platformy Azure do](data-factory-copy-activity-tutorial-using-visual-studio.md) artykułów SQL platformy Azure.

Wykonaj następujące dodatkowe kroki, jeśli tworzysz projekt data factory w programie Visual Studio:

1. Dodaj projekt fabryka danych do rozwiązania programu Visual Studio, który zawiera projekt działania niestandardowego.
2. Dodaj odwołanie do projektu działania platformy .NET z projektu Fabryka danych. Kliknij prawym przyciskiem myszy pozycję Projekt fabryki danych, wskaż polecenie **Dodaj**, a następnie kliknij polecenie **Odwołanie**.
3. W oknie dialogowym **Dodawanie odwołania** wybierz projekt **MyDotNetActivity** i kliknij przycisk **OK**.
4. Skompiluj i opublikuj rozwiązanie.

    > [!IMPORTANT]
    > Podczas publikowania jednostek usługi Data Factory plik zip jest tworzony automatycznie dla Ciebie i jest przekazywany do kontenera obiektów blob: customactivitycontainer. Jeśli kontener obiektów blob nie istnieje, jest również tworzony automatycznie.

## <a name="data-factory-and-batch-integration"></a>Integracja z fabryką danych i wsadem
Usługa Data Factory tworzy zadanie w usłudze Azure Batch o nazwie: **adf-poolname: job-xxx**. Kliknij polecenie **Zadania** z lewego menu.

![Usługa Azure Data Factory — zadania wsadowe](media/data-factory-use-custom-activities/data-factory-batch-jobs.png)

Zadanie jest tworzone dla każdego uruchomienia działania wycinka. Jeśli istnieje pięć wycinków gotowych do przetworzenia, w tym zadaniu zostanie utworzonych pięć zadań. Jeśli w puli Batch znajduje się wiele węzłów obliczeniowych, dwa lub więcej wycinków można uruchomić równolegle. Jeśli maksymalna liczba zadań na węzeł obliczeniowy jest ustawiona na > 1, można również mieć więcej niż jeden plasterek uruchomiony na tym samym obliczaniu.

![Usługa Azure Data Factory — zadania wsadowe](media/data-factory-use-custom-activities/data-factory-batch-job-tasks.png)

Na poniższym diagramie przedstawiono relację między zadaniami usługi Azure Data Factory i Batch.

![Usługa & danych](./media/data-factory-use-custom-activities/DataFactoryAndBatch.png)

## <a name="troubleshoot-failures"></a>Rozwiązywanie problemów z błędami
Rozwiązywanie problemów składa się z kilku podstawowych technik:

1. Jeśli widzisz następujący błąd, może być przy użyciu magazynu obiektów blob Hot/Cool zamiast używania magazynu obiektów blob ogólnego przeznaczenia platformy Azure. Przekaż plik zip do **ogólnego przeznaczenia konta usługi Azure Storage**.

    ```
    Error in Activity: Job encountered scheduling error. Code: BlobDownloadMiscError Category: ServerError Message: Miscellaneous error encountered while downloading one of the specified Azure Blob(s).
    ```
2. Jeśli zostanie wyświetlony następujący błąd, upewnij się, że nazwa klasy w pliku CS jest zgodna z nazwą określoną dla właściwości **EntryPoint** w potoku JSON. W instruktażu nazwa klasy to: MyDotNetActivity, a EntryPoint w JSON to: MyDotNetActivityNS. **MyDotNetActivity**.

    ```
    MyDotNetActivity assembly does not exist or doesn't implement the type Microsoft.DataFactories.Runtime.IDotNetActivity properly
    ```

   Jeśli nazwy są zgodne, upewnij się, że wszystkie pliki binarne znajdują się w **folderze głównym** pliku zip. Oznacza to, że po otwarciu pliku zip, powinieneś zobaczyć wszystkie pliki w folderze głównym, a nie w żadnych podfolderach.
3. Jeśli plasterek wejściowy nie jest ustawiony na **Gotowe,** upewnij się, że struktura folderów wejściowych jest poprawna, a **plik.txt** istnieje w folderach wejściowych.
3. W **Execute** metody działania niestandardowego, użyj **IActivityLogger** obiektu do rejestrowania informacji, które ułatwia rozwiązywanie problemów. Zarejestrowane komunikaty pojawiają się w plikach dziennika użytkownika (jeden lub więcej plików o nazwie: user-0.log, user-1.log, user-2.log itp.).

   W **pliku Wyjściowo-dataaset** bloku, kliknij plasterek, aby wyświetlić **blok PLASTEREK DANYCH** dla tego plasterka. Widzisz **działanie uruchamia dla** tego plasterka. Powinien zostać wyświetlony jeden bieg działania dla plasterka. Po kliknięciu przycisku Uruchom na pasku poleceń można uruchomić inną aktywność dla tego samego plasterka.

   Po kliknięciu uruchomienia działania, zobaczysz **ACTIVITY RUN SZCZEGÓŁY** bloku z listą plików dziennika. W pliku user_0.log są widoczne rejestrowane komunikaty. Po wystąpieniu błędu zostanie wyświetlonych trzy uruchomienia działania, ponieważ liczba ponownych prób jest ustawiona na 3 w potoku/działania JSON. Po kliknięciu uruchomienia działania, zobaczysz pliki dziennika, które można przejrzeć, aby rozwiązać ten błąd.

   Na liście plików dziennika kliknij **user-0.log**. W prawym panelu są wyniki korzystania z **IActivityLogger.Write** metody. Jeśli nie widzisz wszystkich wiadomości, sprawdź, czy masz więcej plików dziennika o nazwie: user_1.log, user_2.log itp. W przeciwnym razie kod może zakończyć się niepowodzeniem po ostatniej zarejestrowanej wiadomości.

   Ponadto sprawdź **system-0.log** dla wszystkich komunikatów o błędach systemu i wyjątków.
4. Dołącz plik **PDB** do pliku zip, aby szczegóły błędu zawierały informacje, takie jak **stos wywołań,** gdy wystąpi błąd.
5. Wszystkie pliki w archiwum ZIP działania niestandardowego muszą znajdować się na **najwyższym poziomie**, bez podfolderów.
6. Upewnij się, że **assemblyName** (MyDotNetActivity.dll), **entryPoint**(MyDotNetActivityNS.MyDotNetActivity), **packageFile** (customactivitycontainer/MyDotNetActivity.zip) i **packageLinkedService** (należy wskazać magazyn obiektów blob **ogólnego przeznaczenia**platformy Azure, który zawiera plik zip) są ustawione na poprawne wartości.
7. Jeśli naprawiono błąd i chcesz przetworzyć wycinek ponownie, kliknij prawym przyciskiem wycinek w bloku **OutputDataset** i kliknij polecenie **Uruchom**.
8. Jeśli widzisz następujący błąd, używasz pakietu usługi Azure Storage w wersji > 4.3.0. Program data factory service launcher wymaga wersji 4.3 systemu WindowsAzure.Storage. Zobacz sekcji [izolacji domeny aplikacji](#appdomain-isolation) dla obejść, jeśli należy użyć nowszej wersji zestawu usługi Azure Storage.

    ```
    Error in Activity: Unknown error in module: System.Reflection.TargetInvocationException: Exception has been thrown by the target of an invocation. ---> System.TypeLoadException: Could not load type 'Microsoft.WindowsAzure.Storage.Blob.CloudBlob' from assembly 'Microsoft.WindowsAzure.Storage, Version=4.3.0.0, Culture=neutral,
    ```

    Jeśli można użyć wersji 4.3.0 pakietu usługi Azure Storage, usuń istniejące odwołanie do pakietu usługi Azure Storage w wersji > 4.3.0. Następnie uruchom następujące polecenie z konsoli Menedżera pakietów NuGet.

    ```powershell
    Install-Package WindowsAzure.Storage -Version 4.3.0
    ```

    Skompiluj projekt. Usuń zestaw usługi Azure.Storage w wersji > 4.3.0 z folderu bin\Debug. Utwórz plik zip z plikami binarnymi i plikiem PDB. Zastąp stary plik zip na ten w kontenerze obiektów blob (customactivitycontainer). Uruchom ponownie plasterki, które nie powiodły się (kliknij prawym przyciskiem myszy plasterek, a następnie kliknij polecenie Uruchom).
8. Działanie niestandardowe nie używa pliku **app.config** z pakietu. W związku z tym jeśli kod odczytuje żadnych ciągów połączeń z pliku konfiguracji, nie działa w czasie wykonywania. Najlepszym rozwiązaniem podczas korzystania z usługi Azure Batch jest do przechowywania wszelkich wpisów tajnych w **usłudze Azure KeyVault**, użyj jednostki usługi opartej na certyfikatach, aby chronić **keyvault**i dystrybuować certyfikat do puli usługi Azure Batch. Niestandardowe działanie .NET będzie miało w takiej sytuacji dostęp do danych poufnych z magazynu KeyVault w czasie uruchomienia. To rozwiązanie jest rozwiązaniem ogólnym i można skalować do dowolnego typu klucz tajny, a nie tylko parametry połączenia.

   Istnieje łatwiejsze obejście (ale nie jest to najlepsze rozwiązanie): można utworzyć **usługę linked Azure SQL** z ustawieniami ciągu połączenia, utworzyć zestaw danych, który używa połączonej usługi, i łańcuch zestawu danych jako fikcyjnego zestawu danych wejściowych do niestandardowego działania platformy .NET. Następnie można uzyskać dostęp do ciągu połączenia usługi połączonej w kodzie działania niestandardowego.

## <a name="update-custom-activity"></a>Aktualizowanie działania niestandardowego
Jeśli zaktualizujesz kod dla działania niestandardowego, skompiluj go i przekaż plik zip, który zawiera nowe pliki binarne do magazynu obiektów blob.

## <a name="appdomain-isolation"></a>Izolacja domeny aplikacji
Zobacz [Przykład międzynastawowy,](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/CrossAppDomainDotNetActivitySample) który pokazuje, jak utworzyć działanie niestandardowe, które nie jest powiązane z wersjami zestawu używanymi przez program uruchamiający fabryka danych (na przykład: WindowsAzure.Storage v4.3.0, Newtonsoft.Json v6.0.x itp.).

## <a name="access-extended-properties"></a>Dostęp do właściwości rozszerzonych
Można zadeklarować rozszerzone właściwości w działaniu JSON, jak pokazano w poniższym przykładzie:

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

W tym przykładzie istnieją dwie właściwości rozszerzone: **SliceStart** i **DataFactoryName**. Wartość SliceStart jest oparta na zmiennej systemowej SliceStart. Zobacz [Zmienne systemowe,](data-factory-functions-variables.md) aby uzyskać listę obsługiwanych zmiennych systemowych. Wartość DataFactoryName jest zakodowany na czas CustomActivityFactory.

Aby uzyskać dostęp do tych właściwości rozszerzonych w **Execute** metody, należy użyć kodu podobnego do następującego kodu:

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
Można również utworzyć pulę usługi Azure Batch z funkcją **skalowania automatycznego.** Na przykład można utworzyć pulę wsadową platformy Azure z 0 dedykowanymi maszynami wirtualnymi i formułą skalowania automatycznego na podstawie liczby oczekujących zadań.

Przykładowa formuła w tym miejscu osiąga następujące zachowanie: Gdy pula jest początkowo tworzona, zaczyna się od 1 maszyny Wirtualnej. $PendingTasks metryka definiuje liczbę zadań w stanie uruchomione + aktywne (w kolejce).  Formuła znajduje średnią liczbę oczekujących zadań w ciągu ostatnich 180 sekund i odpowiednio ustawia targetDedicated. Zapewnia, że TargetDedicated nigdy nie wykracza poza 25 maszyn wirtualnych. Tak jak nowe zadania są przesyłane, puli automatycznie rośnie i jak zadania ukończone, maszyny wirtualne stają się wolne jeden po drugim i skalowanie automatyczne zmniejsza te maszyny wirtualne. uruchamianieNumberOfVMs i maxNumberofVMs mogą być dostosowane do Twoich potrzeb.

Formuła skalowania automatycznego:

```
startingNumberOfVMs = 1;
maxNumberofVMs = 25;
pendingTaskSamplePercent = $PendingTasks.GetSamplePercent(180 * TimeInterval_Second);
pendingTaskSamples = pendingTaskSamplePercent < 70 ? startingNumberOfVMs : avg($PendingTasks.GetSample(180 * TimeInterval_Second));
$TargetDedicated=min(maxNumberofVMs,pendingTaskSamples);
```

Zobacz [Automatyczne skalowanie węzłów obliczeniowych w puli usługi Azure Batch, aby](../../batch/batch-automatic-scaling.md) uzyskać szczegółowe informacje.

Jeśli pula używa domyślnej [funkcji autoScaleEvaluationInterval,](https://msdn.microsoft.com/library/azure/dn820173.aspx)przygotowanie maszyny wirtualnej może potrwać 15-30 minut.  Jeśli pula używa innego autoScaleEvaluationInterval, usługa Batch może potrwać autoScaleEvaluationInterval + 10 minut.


## <a name="create-a-custom-activity-by-using-net-sdk"></a>Tworzenie działania niestandardowego przy użyciu sdk .NET
W przewodniku w tym artykule utworzysz fabrykę danych z potokiem, który używa działania niestandardowego przy użyciu witryny Azure portal. Poniższy kod pokazuje, jak utworzyć fabrykę danych przy użyciu .NET SDK zamiast. Można znaleźć więcej szczegółów na temat używania SDK do programowego tworzenia potoków w [potoku tworzenia z działaniem kopiowania przy użyciu .NET API](data-factory-copy-activity-tutorial-using-dotnet-api.md) artykułu.

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

## <a name="debug-custom-activity-in-visual-studio"></a>Debugowanie działania niestandardowego w programie Visual Studio
Usługa Azure Data Factory — przykład [środowiska lokalnego](https://github.com/gbrueckl/Azure.DataFactory.LocalEnvironment) w usłudze GitHub zawiera narzędzie, które umożliwia debugowanie niestandardowych działań platformy .NET w programie Visual Studio.

## <a name="sample-custom-activities-on-github"></a>Przykładowe działania niestandardowe w usłudze GitHub
| Sample | Co robi działanie niestandardowe |
| --- | --- |
| [Http Data Downloader](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/HttpDataDownloaderSample). |Pobiera dane z punktu końcowego HTTP do usługi Azure Blob Storage przy użyciu niestandardowej aktywności języka C# w fabryce danych. |
| [Przykład analizy nastrojów na Twitterze](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/TwitterAnalysisSample-CustomC%23Activity) |Wywołuje model studio usługi Azure Machine Learning i zrobić analizy tonacji, oceniania, przewidywania itp. |
| [Uruchom skrypt R](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/RunRScriptUsingADFSample). |Wywołuje skrypt R, uruchamiając program RScript.exe w klastrze HDInsight, w który jest już zainstalowany program R. |
| [Działanie Cross AppDomain .NET](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/CrossAppDomainDotNetActivitySample) |Używa różnych wersji montażowych od tych używanych przez wyrzutnię data factory |
| [Ponowne przetwarzanie modelu w usługach Azure Analysis Services](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/AzureAnalysisServicesProcessSample) |  Ponowne przetwarzanie modelu w usługach Azure Analysis Services. |

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
