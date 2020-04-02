---
title: Przetwarzanie dużych zestawów danych przy użyciu usługi Data Factory i Batch
description: W tym artykule opisano sposób przetwarzania ogromnych ilości danych w potoku usługi Azure Data Factory przy użyciu możliwości przetwarzania równoległego usługi Azure Batch.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: 7a6540b5784a76acfc248fb15feb1aaf39420845
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80546945"
---
# <a name="process-large-scale-datasets-by-using-data-factory-and-batch"></a>Przetwarzanie dużych zestawów danych przy użyciu usługi Data Factory i Batch
> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Azure Data Factory, która jest ogólnie dostępna. Jeśli używasz bieżącej wersji usługi Data Factory, zobacz [Działania niestandardowe w fabryce danych](../transform-data-using-dotnet-custom-activity.md).

W tym artykule opisano architekturę przykładowego rozwiązania, które przenosi i przetwarza zestawy danych na dużą skalę w sposób automatyczny i zaplanowany. Zawiera również kompleksowe wskazówki do zaimplementowania rozwiązania przy użyciu usługi Data Factory i usługi Azure Batch.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Ten artykuł jest dłuższy niż typowy artykuł, ponieważ zawiera instruktaż całego przykładowego rozwiązania. Jeśli jesteś nowym użytkownikem usługi Batch i Data Factory, możesz dowiedzieć się więcej o tych usługach i o tym, jak ze sobą współpracują. Jeśli wiesz coś o usługach i projektujesz/projektujesz rozwiązanie, możesz skupić się na sekcji architektury w artykule. Jeśli tworzysz prototyp lub rozwiązanie, możesz wypróbować instrukcje krok po kroku w instruktażu. Zapraszamy do komentarzy na temat tej zawartości i sposobu ich wykorzystania.

Najpierw przyjrzyjmy się, jak fabryka danych i usługi wsadowe mogą pomóc w przetwarzaniu dużych zestawów danych w chmurze.     


## <a name="why-azure-batch"></a>Dlaczego usługa Azure Batch?
 Za pomocą usługi Batch można wydajnie uruchamiać aplikacje do pracy na dużą skalę w zakresie komputerów równoległych i wysokowydajnych (HPC) w chmurze. Jest to usługa platformy, która planuje pracy intensywnie korzystających z obliczeń do uruchomienia na zarządzanej kolekcji maszyn wirtualnych (maszyn wirtualnych). Może automatycznie skalować zasoby obliczeniowe, aby zaspokoić potrzeby zadań.

W usłudze Batch definiuje się zasoby obliczeniowe Azure do wykonywania aplikacji równolegle i na dużą skalę. Zadania na żądanie lub zaplanowane można uruchamiać. Nie trzeba ręcznie tworzyć, konfigurować i zarządzać klastrem HPC, poszczególnymi maszynami wirtualnymi, sieciami wirtualnymi ani złożoną infrastrukturą zadania i planowania zadań.

 Jeśli nie jesteś zaznajomiony z batch, następujące artykuły pomagają zrozumieć architekturę/implementację rozwiązania opisanego w tym artykule:   

* [Podstawy partii](../../batch/batch-technical-overview.md)
* [Omówienie funkcji usługi Batch](../../batch/batch-api-basics.md)

Opcjonalnie, aby dowiedzieć się więcej o partii, zobacz [dokumentację usługi Batch](https://docs.microsoft.com/azure/batch/).

## <a name="why-azure-data-factory"></a>Dlaczego usługa Azure Data Factory?
Fabryka danych jest usługą integracji danych w chmurze, która służy do aranżacji i automatyzacji przenoszenia i przekształcania danych. Za pomocą usługi Data Factory można tworzyć potoki danych zarządzanych, które przenoszą dane z lokalnych i chmurowych magazynów danych do scentralizowanego magazynu danych. Przykładem jest magazyn obiektów Blob platformy Azure. Za pomocą usługi Data Factory można przetwarzać/przekształcać dane przy użyciu usług, takich jak Usługi Azure HDInsight i azure machine learning. Można również zaplanować potoki danych do uruchomienia w sposób zaplanowany (na przykład co godzinę, codziennie i co tydzień). Można monitorować i zarządzać potoki na pierwszy rzut oka, aby zidentyfikować problemy i podjąć działania.

  Jeśli nie jesteś zaznajomiony z fabryką danych, następujące artykuły pomagają zrozumieć architekturę/implementację rozwiązania opisanego w tym artykule:  

* [Wprowadzenie do usługi Data Factory](data-factory-introduction.md)
* [Tworzenie pierwszego potoku danych](data-factory-build-your-first-pipeline.md)   

Opcjonalnie, aby dowiedzieć się więcej o fabryce danych, zapoznaj się [z dokumentacją fabryki danych](https://docs.microsoft.com/rest/api/datafactory/v1/data-factory-data-factory).

## <a name="data-factory-and-batch-together"></a>Fabryka danych i partia razem
Usługa Data Factory zawiera wbudowane działania. Na przykład działanie Kopiowanie służy do kopiowania/przenoszenia danych z magazynu danych źródłowych do docelowego magazynu danych. Działanie gałęzi jest używany do przetwarzania danych przy użyciu klastrów Hadoop (HDInsight) na platformie Azure. Aby uzyskać listę obsługiwanych działań transformacji, zobacz [Działania przekształcania danych](data-factory-data-transformation-activities.md).

Można również utworzyć niestandardowe działania platformy .NET, aby przenosić lub przetwarzać dane za pomocą własnej logiki. Działania te można uruchamiać w klastrze HDInsight lub na puli partii maszyn wirtualnych. Korzystając z usługi Batch, można skonfigurować pulę do skalowania automatycznego (dodawanie lub usuwanie maszyn wirtualnych na podstawie obciążenia) na podstawie formuły, którą podasz.     

## <a name="architecture-of-a-sample-solution"></a>Architektura przykładowego rozwiązania
  Architektura opisana w tym artykule jest dla prostego rozwiązania. Jest to również istotne dla złożonych scenariuszy, takich jak modelowanie ryzyka przez usługi finansowe, przetwarzanie i renderowanie obrazu oraz analiza genomiczna.

Diagram ilustruje, jak fabryka danych organizuje przenoszenie i przetwarzanie danych. Pokazuje również, jak Batch przetwarza dane w sposób równoległy. Pobierz i wydrukuj diagram w celu łatwego odniesienia (11 x 17 cali lub format A3). Aby uzyskać dostęp do diagramu, aby można go było wydrukować, zobacz [HPC i aranżacja danych przy użyciu usługi Batch i Data Factory](https://go.microsoft.com/fwlink/?LinkId=717686).

[![Diagram przetwarzania danych na dużą skalę](./media/data-factory-data-processing-using-batch/image1.png)](https://go.microsoft.com/fwlink/?LinkId=717686)

Poniższa lista zawiera podstawowe kroki procesu. Rozwiązanie zawiera kod i objaśnienia do tworzenia rozwiązania end-to-end.

* **Konfigurowanie usługi Batch z pulą węzłów obliczeniowych (maszyn wirtualnych).** Można określić liczbę węzłów i rozmiar każdego węzła.

* **Utwórz wystąpienie fabryki danych,** które jest skonfigurowane z jednostkami reprezentującymi magazyn obiektów blob, usługę obliczeniową Batch, dane wejściowe/wyjściowe oraz przepływ pracy/potok z działaniami, które przenoszą i przekształcają dane.

* **Utwórz niestandardowe działanie platformy .NET w potoku Fabryka danych.** Działanie jest kod użytkownika, który działa w puli batch.

* **Przechowuj duże ilości danych wejściowych jako obiekty blob w usłudze Azure Storage.** Dane są podzielone na logiczne wycinki (zwykle według czasu).

* **Fabryka danych kopiuje dane, które są przetwarzane równolegle** do lokalizacji dodatkowej.

* **Usługa Data Factory uruchamia działanie niestandardowe przy użyciu puli przydzielonej przez partię.** Fabryka danych może uruchamiać działania jednocześnie. Każde działanie przetwarza wycinek danych. Wyniki są przechowywane w magazynie.

* **Fabryka danych przenosi wyniki końcowe do trzeciej lokalizacji,** w celu dystrybucji za pośrednictwem aplikacji lub dalszego przetwarzania za pomocą innych narzędzi.

## <a name="implementation-of-the-sample-solution"></a>Wdrożenie przykładowego rozwiązania
Przykładowe rozwiązanie jest celowo proste. Został zaprojektowany, aby pokazać, jak używać fabryki danych i partii razem do przetwarzania zestawów danych. Rozwiązanie zlicza liczbę wystąpień wyszukiwanego terminu "Microsoft" w plikach wejściowych, które są zorganizowane w szeregach czasowych. Następnie wyprowadza liczbę do plików wyjściowych.

**Czas:** Jeśli znasz podstawy platformy Azure, fabryki danych i usługi Batch i zostały ukończone następujące wymagania wstępne, to rozwiązanie trwa od jednej do dwóch godzin, aby zakończyć.

### <a name="prerequisites"></a>Wymagania wstępne
#### <a name="azure-subscription"></a>Subskrypcja platformy Azure
Jeśli nie masz subskrypcji platformy Azure, możesz szybko utworzyć bezpłatne konto próbne. Aby uzyskać więcej informacji, zobacz [Bezpłatna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

#### <a name="azure-storage-account"></a>Konto magazynu Azure
Konto magazynu służy do przechowywania danych w tym samouczku. Jeśli nie masz konta magazynu, zobacz [Tworzenie konta magazynu](../../storage/common/storage-account-create.md). Przykładowe rozwiązanie używa magazynu obiektów blob.

#### <a name="azure-batch-account"></a>Konto usługi Azure Batch
Utwórz konto usługi Batch przy użyciu [portalu Azure](https://portal.azure.com/). Aby uzyskać więcej informacji, zobacz [Tworzenie konta usługi Batch i zarządzanie nim](../../batch/batch-account-create-portal.md). Zanotuj nazwę konta usługi Batch i klucz konta. Można również użyć polecenia cmdlet [New-AzBatchAccount](https://docs.microsoft.com/powershell/module/az.batch/new-azbatchaccount) do utworzenia konta usługi Batch. Aby uzyskać instrukcje dotyczące używania tego polecenia cmdlet, zobacz [Wprowadzenie do poleceń cmdlet programu Batch PowerShell](../../batch/batch-powershell-cmdlets-get-started.md).

Przykładowe rozwiązanie używa Batch (pośrednio za pośrednictwem potoku fabryki danych) do przetwarzania danych w sposób równoległy na puli węzłów obliczeniowych (zarządzana kolekcja maszyn wirtualnych).

#### <a name="azure-batch-pool-of-virtual-machines"></a>Pula maszyn wirtualnych usługi Azure Batch
Utwórz pulę wsadową z co najmniej dwoma węzłami obliczeniowymi.

1. W [witrynie Azure portal](https://portal.azure.com)wybierz pozycję **Przeglądaj** w menu po lewej stronie i wybierz pozycję **Konta wsadowe**.

1. Wybierz konto usługi Batch, aby otworzyć blok **Konto wsadowe.**

1. Wybierz **kafelek Baseny.**

1. Na **pulach** bloku wybierz przycisk **Dodaj** na pasku narzędzi, aby dodać pulę.

   a. Wprowadź identyfikator puli (**Identyfikator puli**). Zwróć uwagę na identyfikator puli. Jest to potrzebne podczas tworzenia rozwiązania fabryki danych.

   b. Określ **system Windows Server 2012 R2** dla ustawienia **Rodzina systemów operacyjnych.**

   d. Wybierz **warstwę cenową węzła**.

   d. Wprowadź **2** jako wartość ustawienia **Dedykowane dla celu.**

   e. Wprowadź **2** jako wartość ustawienia **Maksymalna liczba zadań na węzeł.**

   f. Wybierz **przycisk OK,** aby utworzyć pulę.

#### <a name="azure-storage-explorer"></a>Eksplorator usługi Azure Storage
Za pomocą [usługi Azure Storage Explorer 6](https://azurestorageexplorer.codeplex.com/) lub [CloudXplorer](https://clumsyleaf.com/products/cloudxplorer) (z oprogramowania ClumsyLeaf) służy do sprawdzania i zmieniania danych w projektach magazynu. Można również sprawdzić i zmienić dane w dziennikach aplikacji hostowanych w chmurze.

1. Utwórz kontener o nazwie **mycontainer** z dostępem prywatnym (bez dostępu anonimowego).

1. Jeśli używasz CloudXplorer, utwórz foldery i podfoldery z następującą strukturą:

   ![Struktura folderów i podfolderów](./media/data-factory-data-processing-using-batch/image3.png)

   `Inputfolder`i `outputfolder` są folderami najwyższego poziomu w `mycontainer`pliku . Folder `inputfolder` zawiera podfoldery ze sygnaturami daty i godziny (YYYY-MM-DD-HH).

   Jeśli używasz Eksploratora magazynu, w następnym kroku `inputfolder/2015-11-16-00/file.txt`przekazujesz pliki o następujących nazwach: , `inputfolder/2015-11-16-01/file.txt`i tak dalej. Ten krok automatycznie tworzy foldery.

1. Utwórz plik tekstowy **file.txt** na komputerze z zawartością zawierającą słowo kluczowe **Microsoft**. Przykładem jest "test działania niestandardowego Microsoft test działania niestandardowego Microsoft."

1. Przekaż plik do następujących folderów wejściowych w magazynie obiektów blob:

   ![Foldery wejściowe](./media/data-factory-data-processing-using-batch/image4.png)

   Jeśli używasz Eksploratora magazynu, przekaż plik **file.txt** do **mycontainer**. Wybierz **pozycję Kopiuj** na pasku narzędzi, aby utworzyć kopię obiektu blob. W oknie dialogowym **Kopiowanie obiektu blob** `inputfolder/2015-11-16-00/file.txt`zmień **nazwę docelowego obiektu blob** na . Powtórz ten krok, `inputfolder/2015-11-16-02/file.txt` `inputfolder/2015-11-16-03/file.txt`aby `inputfolder/2015-11-16-04/file.txt`utworzyć `inputfolder/2015-11-16-01/file.txt`, , , i tak dalej. Ta akcja automatycznie tworzy foldery.

1. Utwórz inny `customactivitycontainer`kontener o nazwie . Przekaż plik zip działania niestandardowego do tego kontenera.

#### <a name="visual-studio"></a>Visual Studio
Zainstaluj program Visual Studio 2012 lub nowszą, aby utworzyć niestandardowe działanie usługi Batch, które ma być używane w rozwiązaniu fabrycznym danych.

### <a name="high-level-steps-to-create-the-solution"></a>Kroki wysokiego poziomu w celu stworzenia rozwiązania
1. Utwórz działanie niestandardowe, które zawiera logikę przetwarzania danych.

1. Utwórz fabrykę danych, która używa działania niestandardowego.

### <a name="create-the-custom-activity"></a>Tworzenie działania niestandardowego
Działanie niestandardowe fabryki danych jest sercem tego przykładowego rozwiązania. Przykładowe rozwiązanie używa batch do uruchomienia działania niestandardowego. Aby uzyskać informacje dotyczące tworzenia działań niestandardowych i używania ich w potokach fabryki danych, zobacz [Używanie działań niestandardowych w potoku fabryki danych](data-factory-use-custom-activities.md).

Aby utworzyć działanie niestandardowe platformy .NET, którego można użyć w potoku fabryki danych, należy utworzyć projekt biblioteki klas .NET z klasą implementuują interfejs IDotNetActivity. Ten interfejs ma tylko jedną metodę: Execute. Oto podpis metody:

```csharp
public IDictionary<string, string> Execute(
            IEnumerable<LinkedService> linkedServices,
            IEnumerable<Dataset> datasets,
            Activity activity,
            IActivityLogger logger)
```

Metoda ma kilka kluczowych składników, które należy zrozumieć:

* Metoda przyjmuje cztery parametry:

  * **linkedServices**. Ten parametr jest wyliczoną listą połączonych usług, które łączą źródła danych wejściowych/wyjściowych (na przykład magazynu obiektów blob) z fabryką danych. W tym przykładzie istnieje tylko jedna połączona usługa typu Azure Storage używana zarówno dla danych wejściowych, jak i wyjściowych.
  * **zbiorów danych**. Ten parametr jest wyliczalną listą zestawów danych. Ten parametr służy do uzyskania lokalizacji i schematów zdefiniowanych przez wejściowe i wyjściowe zestawy danych.
  * **aktywności .** Ten parametr reprezentuje bieżącą jednostkę obliczeniową. W takim przypadku jest to usługa wsadowa.
  * **rejestratora**. Rejestratora można użyć do pisania komentarzy debugowania, które powierzchni jako dziennik "Użytkownik" dla potoku.
* Metoda zwraca słownik, który może służyć do łańcucha działań niestandardowych razem w przyszłości. Ta funkcja nie jest jeszcze zaimplementowana, więc po prostu zwróć pusty słownik z metody.

#### <a name="procedure-create-the-custom-activity"></a>Procedura: Tworzenie działania niestandardowego
1. Utwórz projekt biblioteki klas .NET w programie Visual Studio.

   a. Uruchom program Visual Studio 2012/2013/2015.

   b. Wybierz **pozycję Plik** > **nowy** > **projekt**.

   d. Rozwiń **pozycję Szablony**i wybierz pozycję **Visual C\#**. W tym instruktażu\#można użyć języka C , ale można użyć dowolnego języka .NET do opracowania działania niestandardowego.

   d. Wybierz **bibliotekę klas** z listy typów projektów po prawej stronie.

   e. Wprowadź **MyDotNetActivity** dla **nazwy**.

   f. Wybierz **C:\\ADF** dla **lokalizacji**. Utwórz folder **ADF,** jeśli nie istnieje.

   g. Wybierz przycisk **OK**, aby utworzyć projekt.

1. Wybierz **opcję Konsola** > Menedżera**pakietów Menedżera** > **pakietów**Narzędzia NuGet .

1. W konsoli Menedżera pakietów wykonaj następujące polecenie, aby zaimportować microsoft.Azure.Management.DataFactories:

    ```powershell
    Install-Package Microsoft.Azure.Management.DataFactories
    ```
1. Zaimportuj pakiet Azure **Storage** NuGet do projektu. Ten pakiet jest potrzebny, ponieważ w tym przykładzie używasz interfejsu API magazynu obiektów blob:

    ```powershell
    Install-Package Az.Storage
    ```
1. Dodaj następujące za pomocą dyrektyw do pliku źródłowego w projekcie:

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
1. Zmień nazwę obszaru nazw na **MyDotNetActivityNS**.

    ```csharp
    namespace MyDotNetActivityNS
    ```
1. Zmień nazwę klasy na **MyDotNetActivity**i wyjdź ją z interfejsu **IDotNetActivity,** jak pokazano na rysunku:

    ```csharp
    public class MyDotNetActivity : IDotNetActivity
    ```
1. Zaimplementuj (dodaj) **Execute** metody interfejsu **IDotNetActivity** do **myDotNetActivity** klasy. Skopiuj poniższy przykładowy kod do metody. Aby uzyskać wyjaśnienie logiki używanej w tej metodzie, zobacz [Execute metody](#execute-method) sekcji.

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
1. Dodaj następujące metody pomocnika do klasy. Te metody są wywoływane przez **Execute** metody. Najważniejsze, **Calculate** metoda izoluje kod, który iteruje za pośrednictwem każdego obiektu blob.

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
    Metoda GetFolderPath zwraca ścieżkę do folderu, do który wskazuje zestaw danych, a metoda GetFileName zwraca nazwę obiektu blob/pliku, na który wskazuje zestaw danych.

    ```csharp

    "name": "InputDataset",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "fileName": "file.txt",
            "folderPath": "mycontainer/inputfolder/{Year}-{Month}-{Day}-{Hour}",
    ```

    Metoda Oblicz oblicza liczbę wystąpień słowa kluczowego "Microsoft" w plikach wejściowych (obiektach blob w folderze). Wyszukiwane hasło "Microsoft" jest zakodowane na miejscu w kodzie.

1. Skompiluj projekt. Z menu **wybierz polecenie Zbuduj,** a następnie wybierz polecenie **Build Solution**.

1. Uruchom Eksploratora Windows i przejdź do folderu **debugowania lub\\** wydania **pojemnika.\\** Wybór folderu zależy od typu kompilacji.

1. Utwórz plik zip **MyDotNetActivity.zip,** który zawiera wszystkie pliki binarne w folderze ** \\debugowania pojemnika.\\** Można dołączyć MyDotNetActivity. **plik pdb,** aby uzyskać dodatkowe szczegóły, takie jak numer wiersza w kodzie źródłowym, który spowodował problem w przypadku wystąpienia błędu.

   ![Lista folderów bin\Debug](./media/data-factory-data-processing-using-batch/image5.png)

1. Przekaż **plik MyDotNetActivity.zip** jako obiekt blob do kontenera `customactivitycontainer` obiektów blob w magazynie obiektów blob, którego używa usługa połączona z usługą StorageLinkedService w usłudze ADFTutorialDataFactory. Utwórz kontener `customactivitycontainer` obiektów blob, jeśli jeszcze nie istnieje.

#### <a name="execute-method"></a>Metoda wykonywania
Ta sekcja zawiera więcej szczegółów na temat kodu w Execute metody.

1. Elementy członkowskie do iteracji za pośrednictwem kolekcji danych wejściowych znajdują się w obszarze nazw [Microsoft.WindowsAzure.Storage.Blob.](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob) Aby iterować za pośrednictwem kolekcji obiektów blob, należy użyć **klasy BlobContinuationToken.** W istocie należy użyć pętli do-while z tokenem jako mechanizm do zamykania pętli. Aby uzyskać więcej informacji, zobacz [Używanie magazynu obiektów Blob z platformy .NET](../../storage/blobs/storage-dotnet-how-to-use-blobs.md). Podstawowa pętla jest pokazana tutaj:

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
   Aby uzyskać więcej informacji, zobacz dokumentację [metody ListBlobsSegmented.](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob.cloudblobcontainer.listblobssegmented)

1. Kod do pracy za pośrednictwem zestawu obiektów blob logicznie przechodzi w pętli do-while. W **Execute metody,** pętla do-while przekazuje listę obiektów blob do metody o nazwie **Oblicz**. Metoda zwraca zmienną ciągu o nazwie **output,** która jest wynikiem iteracji przez wszystkie obiekty blob w segmencie.

   Zwraca liczbę wystąpień wyszukiwanego terminu "Microsoft" w obiekcie blob przekazany **do Calculate** metody.

    ```csharp
    output += string.Format("{0} occurrences of the search term \"{1}\" were found in the file {2}.\r\n", wordCount, searchTerm, inputBlob.Name);
    ```
1. Po **obliczać** metoda jest zakończona, musi być zapisywany w nowym obiekcie blob. Dla każdego zestawu przetworzonych obiektów blob nowy obiekt blob mogą być zapisywane z wynikami. Aby zapisać do nowego obiektu blob, najpierw znajdź wyjściowy zestaw danych.

    ```csharp
    // Get the output dataset by using the name of the dataset matched to a name in the Activity output collection.
    Dataset outputDataset = datasets.Single(dataset => dataset.Name == activity.Outputs.Single().Name);
    ```
1. Kod wywołuje również metodę pomocniczą **GetFolderPath,** aby pobrać ścieżkę folderu (nazwę kontenera magazynu).

    ```csharp
    folderPath = GetFolderPath(outputDataset);
    ```
   Metoda GetFolderPath rzutuje obiekt DataSet na zestaw azureblobdataset, który ma właściwość o nazwie FolderPath.

    ```csharp
    AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;

    return blobDataset.FolderPath;
    ```
1. Kod wywołuje **Metodę GetFileName,** aby pobrać nazwę pliku (nazwa obiektu blob). Kod jest podobny do poprzedniego kodu, który został użyty do uzyskania ścieżki folderu.

    ```csharp
    AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;

    return blobDataset.FileName;
    ```
1. Nazwa pliku jest zapisywana przez utworzenie obiektu URI. Konstruktor URI używa **właściwości BlobEndpoint** do zwrócenia nazwy kontenera. Ścieżka folderu i nazwa pliku są dodawane do konstruowania wyjściowego identyfikatora URI obiektu blob.  

    ```csharp
    // Write the name of the file.
    Uri outputBlobUri = new Uri(outputStorageAccount.BlobEndpoint, folderPath + "/" + GetFileName(outputDataset));
    ```
1. Po zapisaniu nazwy pliku można zapisać ciąg wyjściowy z **Metody Oblicz** do nowego obiektu blob:

    ```csharp
    // Create a blob and upload the output text.
    CloudBlockBlob outputBlob = new CloudBlockBlob(outputBlobUri, outputStorageAccount.Credentials);
    logger.Write("Writing {0} to the output blob", output);
    outputBlob.UploadText(output);
    ```

### <a name="create-the-data-factory"></a>Tworzenie fabryki danych
W sekcji [Tworzenie działania niestandardowego](#create-the-custom-activity) utworzono działanie niestandardowe i przesłano plik zip z plikami binarnymi i plik PDB do kontenera obiektów blob. W tej sekcji utworzysz fabrykę danych z potokiem, który używa działania niestandardowego.

Wejściowy zestaw danych dla działania niestandardowego reprezentuje obiekty blob`mycontainer\\inputfolder`(pliki) w folderze wejściowym ( ) w magazynie obiektów blob. Wyjściowy zestaw danych dla działania reprezentuje wyjściowe obiekty`mycontainer\\outputfolder`blob w folderze wyjściowym ( ) w magazynie obiektów blob.

Upuść jeden lub więcej plików do folderów wejściowych:

```
mycontainer -\> inputfolder
    2015-11-16-00
    2015-11-16-01
    2015-11-16-02
    2015-11-16-03
    2015-11-16-04
```

Na przykład upuść jeden plik (plik.txt) z następującą zawartością do każdego z folderów:

```
test custom activity Microsoft test custom activity Microsoft
```

Każdy folder wejściowy odpowiada wycinku w fabryce danych, nawet jeśli folder ma dwa lub więcej plików. Gdy każdy plasterek jest przetwarzany przez potok, działanie niestandardowe iteruje za pośrednictwem wszystkich obiektów blob w folderze wejściowym dla tego plasterka.

Widzisz pięć plików wyjściowych o tej samej zawartości. Na przykład plik wyjściowy z przetwarzania pliku w folderze 2015-11-16-00 ma następującą zawartość:

```
2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file.txt.
```

Jeśli upuszczasz wiele plików (file.txt, file2.txt, file3.txt) o tej samej zawartości do folderu wejściowego, w pliku wyjściowym zostanie wyświetlonej następującą zawartość. Każdy folder (2015-11-16-00 itp.) odpowiada wycinku w tym przykładzie, nawet jeśli folder zawiera wiele plików wejściowych.

```csharp
2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file.txt.
2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file2.txt.
2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file3.txt.
```

Plik wyjściowy ma teraz trzy wiersze, po jednym dla każdego pliku wejściowego (obiektu blob) w folderze skojarzonym z plasterkiem (2015-11-16-00).

Zadanie jest tworzone dla każdego uruchomienia działania. W tym przykładzie istnieje tylko jedno działanie w potoku. Gdy plasterek jest przetwarzany przez potok, działanie niestandardowe jest uruchamiane na partii, aby przetworzyć plasterek. Ponieważ istnieje pięć plasterków (każdy plasterek może mieć wiele obiektów blob lub plik), pięć zadań są tworzone w usłudze Batch. Gdy zadanie jest uruchamiane w uerce, jest to działanie niestandardowe, które jest uruchomione.

Poniższy instruktaż zawiera dodatkowe szczegóły.

#### <a name="step-1-create-the-data-factory"></a>Krok 1: Tworzenie fabryki danych
1. Po zalogowaniu się do [witryny Azure portal](https://portal.azure.com/)należy wykonać następujące czynności:

   a. Wybierz **NOWY** w menu po lewej stronie.

   b. Wybierz **dane + Analytics** na **nowym** bloku.

   d. Wybierz **fabrykę danych** w bloku **analizy danych.**

1. W bloku **Nowa fabryka danych** wprowadź **customactivityFactory** dla nazwy. Nazwa fabryki danych musi być globalnie unikatowa. Jeśli zostanie wyświetlony błąd "Nazwa fabryki danych CustomActivityFactory nie jest dostępna", zmień nazwę fabryki danych. Na przykład użyj yournameCustomActivityFactory i ponownie utwórz fabrykę danych.

1. Wybierz **nazwę grupy zasobów**i wybierz istniejącą grupę zasobów lub grupę zasobów.

1. Sprawdź, czy subskrypcja i region, w którym ma zostać utworzona fabryka danych, są poprawne.

1. Wybierz **pozycję Utwórz** w bloku **Nowa fabryka danych.**

1. Fabryka danych jest tworzona na pulpicie nawigacyjnym portalu.

1. Po pomyślnym utworzeniu fabryki danych zostanie wyświetlona strona **Fabryka danych,** na której jest wyświetlana zawartość fabryki danych.

   ![Strona fabryki danych](./media/data-factory-data-processing-using-batch/image6.png)

#### <a name="step-2-create-linked-services"></a>Krok 2: Tworzenie połączonych usług
Połączone usługi łączą magazyny danych lub usługi obliczeniowe z fabryką danych. W tym kroku należy połączyć konto magazynu i konto usługi Batch z fabryką danych.

#### <a name="create-an-azure-storage-linked-service"></a>Tworzenie połączonej usługi Azure Storage
1. Wybierz **autora i wdrożyć** kafelek na **bloku Fabryka danych** dla **CustomActivityFactory**. Pojawi się Edytor fabryki danych.

1. Wybierz **nowy magazyn danych** na pasku poleceń i wybierz pozycję Magazyn platformy **Azure.** Pojawi się skrypt JSON używany do tworzenia usługi połączonej magazynu w edytorze.

   ![Nowy magazyn danych](./media/data-factory-data-processing-using-batch/image7.png)

1. Zastąp **nazwę konta** nazwą konta magazynu. Zastąp **klucz konta** kluczem dostępu do konta magazynu. Aby dowiedzieć się, jak uzyskać klucz dostępu do magazynu, zobacz [Zarządzanie kluczami dostępu do konta magazynu](../../storage/common/storage-account-keys-manage.md).

1. Wybierz przycisk **Wdróż** na pasku poleceń, aby wdrożyć połączoną usługę.

   ![Wdrażanie](./media/data-factory-data-processing-using-batch/image8.png)

#### <a name="create-an-azure-batch-linked-service"></a>Tworzenie połączonej usługi Azure Batch
W tym kroku utworzysz połączony serwis dla konta usługi Batch, który jest używany do uruchamiania działania niestandardowego fabryki danych.

1. Wybierz **pozycję Nowe obliczenia** na pasku poleceń i wybierz pozycję Azure **Batch.** Pojawi się skrypt JSON używany do utworzenia usługi połączonej partią w edytorze.

1. W skrypcie JSON:

   a. Zamień **nazwę konta** na nazwę konta usługi Batch.

   b. Zastąp **klucz dostępu** kluczem dostępu konta usługi Batch.

   d. Wprowadź identyfikator puli dla **właściwości poolName.** Dla tej właściwości można określić nazwę puli lub identyfikator puli.

   d. Wprowadź identyfikator URI partii dla **właściwości batchUri** JSON.

      > [!IMPORTANT]
      > Adres URL z bloku **Konto wsadowe** jest w następującym formacie: \<accountname\>. \<regionu\>.batch.azure.com. Dla **batchUri** właściwości w skrypcie JSON, należy usunąć a88 "accountname". ** z adresu URL. Może to być na przykład `"batchUri": "https://eastus.batch.azure.com"`.
      >
      >

      ![Blok Konta wsadowego](./media/data-factory-data-processing-using-batch/image9.png)

      Dla **właściwości poolName** można również określić identyfikator puli zamiast nazwy puli.

      > [!NOTE]
      > Usługa Data Factory nie obsługuje opcji na żądanie dla usługi Batch, tak jak w przypadku usługi HDInsight. W fabryce danych można używać tylko własnej puli partii.
      >
      >

   e. Określ **StorageLinkedService** dla **właściwości linkedServiceName.** Ta połączona usługa została utworzona w poprzednim kroku. Ten magazyn jest używany jako obszar przemieszczania dla plików i dzienników.

1. Wybierz przycisk **Wdróż** na pasku poleceń, aby wdrożyć połączoną usługę.

#### <a name="step-3-create-datasets"></a>Krok 3: Tworzenie zestawów danych
W tym kroku należy utworzyć zestawy danych do reprezentowania danych wejściowych i wyjściowych.

#### <a name="create-the-input-dataset"></a>Tworzenie wejściowego zestawu danych
1. W Edytorze fabryki danych wybierz przycisk **Nowy zestaw danych** na pasku narzędzi. Wybierz **magazyn obiektów blob platformy Azure** z listy rozwijanej.

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

    Potoku można utworzyć w dalszej części tego przewodnika z czasem rozpoczęcia 2015-11-16T00:00:00Z i czas zakończenia 2015-11-16T05:00:00Z. Zaplanowano produkcję danych co godzinę, więc istnieje pięć wycinków wejściowych/wyjściowych (między\> 00:00:00 - **00** **05:00:00).**

    **Częstotliwość** i **interwał** dla wejściowego zestawu danych są ustawione na **Godzina** i **1**, co oznacza, że plasterek wejściowy jest dostępny co godzinę.

    Czas rozpoczęcia dla każdego plasterka jest reprezentowany przez zmienną systemową **SliceStart** w poprzednim urywek JSON. Oto godziny rozpoczęcia każdego plasterka.

    | **Plasterek** | **Godzina rozpoczęcia**          |
    |-----------|-------------------------|
    | 1         | 2015-11-16T**00**:00:00 |
    | 2         | 2015-11-16T**01**:00:00 |
    | 3         | 2015-11-16T**02**:00:00 |
    | 4         | 2015-11-16T**03**:00:00 |
    | 5         | 2015-11-16T**04**:00:00 |

    Ścieżka **folderu** jest obliczana przy użyciu części roku, miesiąca, dnia i godziny czasu rozpoczęcia plasterka (**SliceStart**). Oto jak folder wejściowy jest mapowany na plasterek.

    | **Plasterek** | **Godzina rozpoczęcia**          | **Folder wejściowy**  |
    |-----------|-------------------------|-------------------|
    | 1         | 2015-11-16T**00**:00:00 | 2015-11-16-**00** |
    | 2         | 2015-11-16T**01**:00:00 | 2015-11-16-**01** |
    | 3         | 2015-11-16T**02**:00:00 | 2015-11-16-**02** |
    | 4         | 2015-11-16T**03**:00:00 | 2015-11-16-**03** |
    | 5         | 2015-11-16T**04**:00:00 | 2015-11-16-**04** |

1. Wybierz **pozycję Wdrażanie** na pasku narzędzi, aby utworzyć i wdrożyć tabelę **InputDataset.**

#### <a name="create-the-output-dataset"></a>Tworzenie wyjściowego zestawu danych
W tym kroku należy utworzyć inny zestaw danych typu AzureBlob do reprezentowania danych wyjściowych.

1. W Edytorze fabryki danych wybierz przycisk **Nowy zestaw danych** na pasku narzędzi. Wybierz **magazyn obiektów blob platformy Azure** z listy rozwijanej.

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

    Dla każdego wycinka wejściowego jest generowany wyjściowy obiekt blob/file. Oto jak plik wyjściowy jest nazwany dla każdego plasterka. Wszystkie pliki wyjściowe są generowane `mycontainer\\outputfolder`w jednym folderze wyjściowym, .

    | **Plasterek** | **Godzina rozpoczęcia**          | **Plik wyjściowy**       |
    |-----------|-------------------------|-----------------------|
    | 1         | 2015-11-16T**00**:00:00 | 2015-11-16-**00.txt** |
    | 2         | 2015-11-16T**01**:00:00 | 2015-11-16-**01.txt** |
    | 3         | 2015-11-16T**02**:00:00 | 2015-11-16-**02.txt** |
    | 4         | 2015-11-16T**03**:00:00 | 2015-11-16-**03.txt** |
    | 5         | 2015-11-16T**04**:00:00 | 2015-11-16-**04.txt** |

    Należy pamiętać, że wszystkie pliki w folderze wejściowym (na przykład 2015-11-16-00) są częścią plasterka z czasem rozpoczęcia 2015-11-16-00. Podczas przetwarzania tego plasterka działanie niestandardowe skanuje każdy plik i tworzy wiersz w pliku wyjściowym z liczbą wystąpień wyszukiwanego terminu "Microsoft". Jeśli w folderze znajdują się trzy pliki 2015-11-16-00, w pliku wyjściowym 2015-11-16-00.txt znajdują się trzy wiersze.

1. Wybierz **pozycję Wdrażanie** na pasku narzędzi, aby utworzyć i wdrożyć **zestaw danych wyjściowych**.

#### <a name="step-4-create-and-run-the-pipeline-with-a-custom-activity"></a>Krok 4: Tworzenie i uruchamianie potoku za pomocą działania niestandardowego
W tym kroku utworzysz potok z jednym działaniem, działanie niestandardowe utworzone wcześniej.

> [!IMPORTANT]
> Jeśli plik **file.txt** nie został przekazany do folderów wejściowych w kontenerze obiektów blob, zrób to przed utworzeniem potoku. Właściwość **isPaused** jest ustawiona na false w potoku JSON, więc potok działa natychmiast, ponieważ data **rozpoczęcia** jest w przeszłości.
>
>

1. W Edytorze fabryki danych wybierz pozycję **Nowy potok** na pasku poleceń. Jeśli nie widzisz polecenia, wybierz symbol wielokropka, aby go wyświetlić.

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

   * Tylko jedno działanie jest w potoku i jest typu **DotNetActivity**.
   * **Nazwa zestawu** jest ustawiona na nazwę pliku **DLL MyDotNetActivity.dll**.
   * **EntryPoint** jest ustawiony na **MyDotNetActivityNS.MyDotNetActivity**. To w zasadzie \<namespace\>. \<classname\> w kodzie.
   * **PackageLinkedService** jest **ustawiona na StorageLinkedService**, który wskazuje na magazyn obiektu blob, który zawiera plik zip działania niestandardowego. Jeśli używasz różnych kont magazynu dla plików wejściowych/wyjściowych i niestandardowego pliku zip działania, musisz utworzyć inną usługę połączoną z magazynem. W tym artykule przyjęto założenie, że używasz tego samego konta magazynu.
   * **PackageFile** jest ustawiony na **customactivitycontainer/MyDotNetActivity.zip**. Jest w \<formacie containerforthezip\>/\<nameofthezip.zip\>.
   * Działanie niestandardowe przyjmuje **InputDataset** jako dane wejściowe i **OutputDataset** jako dane wyjściowe.
   * Właściwość **linkedServiceName** działania niestandardowego wskazuje na **platformę AzureBatchLinkedService**, która informuje usługę Data Factory, że działanie niestandardowe musi być uruchamiane w usłudze Batch.
   * Ustawienie **współbieżności** jest ważne. Jeśli używasz wartości domyślnej, która jest 1, nawet jeśli masz dwa lub więcej węzłów obliczeniowych w puli Batch, plasterki są przetwarzane jeden po drugim. W związku z tym nie są przy korzystaniu z możliwości przetwarzania równoległego usługi Batch. Jeśli **ustawisz współbieżność** do wyższej wartości, powiedzmy 2, oznacza to, że dwa plasterki (odpowiada dwóm zadaniom w partii) mogą być przetwarzane w tym samym czasie. W takim przypadku używane są maszyny wirtualne w puli batch. Ustaw właściwość współbieżności odpowiednio.
   * Domyślnie tylko jedno zadanie (plasterek) jest wykonywane na maszynie wirtualnej w dowolnym punkcie. Domyślnie **maksymalna liczba zadań na maszynę wirtualną** jest ustawiona na 1 dla puli wsadowej. W ramach wymagań wstępnych utworzono pulę z tą właściwością ustawioną na 2. W związku z tym dwa wycinki fabryki danych można uruchomić na maszynie Wirtualnej w tym samym czasie.
     - Właściwość **isPaused** jest domyślnie ustawiona na false. Potok działa natychmiast w tym przykładzie, ponieważ wycinki rozpoczynają się w przeszłości. Można ustawić tę właściwość **true,** aby wstrzymać potok i ustawić go z powrotem do **false,** aby ponownie uruchomić.
     -   Czas **rozpoczęcia** i **zakończenia** jest oddalony od siebie o pięć godzin. Plasterki są produkowane co godzinę, więc pięć plasterków są produkowane przez potok.

1. Wybierz przycisk **Wdróż** na pasku poleceń, aby wdrożyć potok.

#### <a name="step-5-test-the-pipeline"></a>Krok 5: Testowanie rurociągu
W tym kroku należy przetestować potoku przez upuszczanie plików do folderów wejściowych. Rozpocznij od przetestowania potoku z jednym plikiem dla każdego folderu wejściowego.

1. W bloku **Fabryka danych** w witrynie Azure portal wybierz pozycję **Diagram**.

   ![Diagram](./media/data-factory-data-processing-using-batch/image10.png)

1. W widoku **Diagram** kliknij dwukrotnie zestaw danych wejściowych **InputDataset**.

   ![Zestaw danych wejściowych](./media/data-factory-data-processing-using-batch/image11.png)

1. **InputDataset bloku** pojawia się z wszystkich pięciu plasterków gotowych. Zwróć uwagę na **czas rozpoczęcia plasterka** i **czas zakończenia plasterka** dla każdego plasterka.

   ![Godziny rozpoczęcia i zakończenia plasterka wejściowego](./media/data-factory-data-processing-using-batch/image12.png)

1. W widoku **Diagram** wybierz pozycję **OutputDataset**.

1. Pięć wycinków danych wyjściowych są wyświetlane w stanie **Gotowy,** jeśli zostały wyprodukowane.

   ![Czas rozpoczęcia i zakończenia plasterka wyjściowego](./media/data-factory-data-processing-using-batch/image13.png)

1. Użyj portalu, aby wyświetlić zadania skojarzone z plasterkami i zobaczyć, na jakiej maszynie wirtualnej działał każdy plasterek. Aby uzyskać więcej informacji, zobacz sekcję [integracji fabryka danych i partia.](#data-factory-and-batch-integration)

1. Pliki wyjściowe `mycontainer` są `outputfolder` wyświetlane w obszarze w magazynie obiektów blob.

   ![Pliki wyjściowe w magazynie](./media/data-factory-data-processing-using-batch/image15.png)

   Na liście znajduje się pięć plików wyjściowych, po jednym dla każdego wycinka wejściowego. Każdy z plików wyjściowych ma zawartość podobną do następujących danych wyjściowych:

    ```
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file.txt.
    ```
   Na poniższym diagramie przedstawiono sposób, w jaki wycina fabryka danych mapuje zadania w układzie partii. W tym przykładzie plasterek ma tylko jeden bieg.

   ![Diagram mapowania plasterków](./media/data-factory-data-processing-using-batch/image16.png)

1. Teraz spróbuj z wieloma plikami w folderze. Tworzenie plików **file2.txt**, **file3.txt**, **file4.txt**i **file5.txt** o takiej samej zawartości jak w pliku file.txt w folderze **2015-11-06-01**.

1. W folderze wyjściowym usuń plik wyjściowy **2015-11-16-01.txt**.

1. Na **bloku OutputDataset** kliknij prawym przyciskiem myszy plasterek z czasem **rozpoczęcia plasterka** ustawionym na **11/16/2015 01:00:00 AM**. Wybierz **pozycję Uruchom,** aby ponownie uruchomić/ponownie przetworzyć plasterek. Plasterek ma teraz pięć plików zamiast jednego pliku.

    ![Run](./media/data-factory-data-processing-using-batch/image17.png)

1. Po uruchomieniu plasterka i jego stanie jest **gotowy**, sprawdź zawartość w pliku wyjściowym dla tego plasterka (**2015-11-16-01.txt**). Plik wyjściowy `mycontainer` pojawia `outputfolder` się w obszarze w magazynie obiektów blob. Powinien istnieć wiersz dla każdego pliku plasterka.

    ```
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file.txt.
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file2.txt.
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file3.txt.
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file4.txt.
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file5.txt.
    ```

> [!NOTE]
> Jeśli plik wyjściowy 2015-11-16-01.txt nie został usunięty przed próbą z pięcioma plikami wejściowymi, zostanie wyświetlony jeden wiersz z poprzedniego przebiegu plasterka i pięć wierszy z bieżącego przebiegu plasterka. Domyślnie zawartość jest dołączana do pliku wyjściowego, jeśli już istnieje.
>
>

#### <a name="data-factory-and-batch-integration"></a>Integracja z fabryką danych i wsadem
Usługa Data Factory tworzy zadanie w `adf-poolname:job-xxx`usłudze Batch o nazwie .

![Zadania wsadowe](media/data-factory-data-processing-using-batch/data-factory-batch-jobs.png)

Zadanie w zadaniu jest tworzone dla każdego uruchomienia działania wycinka. Jeśli 10 wycinków jest gotowych do przetworzenia, w zadaniu zostanie utworzonych 10 zadań. Jeśli w puli jest uruchomionych więcej niż jeden plasterek, jeśli w puli znajduje się wiele węzłów obliczeniowych. Jeśli maksymalna liczba zadań na węzeł obliczeniowy jest ustawiona na większą niż jeden, więcej niż jeden plasterek może działać na tych samych obliczeniach.

W tym przykładzie istnieje pięć wycinków, więc istnieje pięć zadań w uziemienia. Z **współbieżności ustawioną** na **5** w potoku JSON w fabryce danych i **Maksymalna liczba zadań na maszynę wirtualną** ustawiona na **2** w puli partii z **2** maszynami wirtualnymi, zadania działają szybko. (Sprawdź godziny rozpoczęcia i zakończenia zadań).

Użyj portalu, aby wyświetlić zadanie Batch i jego zadania, które są skojarzone z plasterkami i zobacz, na jakiej maszynie wirtualnej uruchomiony jest każdy plasterek.

![Zadania zadania wsadowego](media/data-factory-data-processing-using-batch/data-factory-batch-job-tasks.png)

### <a name="debug-the-pipeline"></a>Debugowanie potoku
Debugowanie składa się z kilku podstawowych technik.

1. Jeśli plasterek wejściowy nie jest ustawiony na **Gotowe,** upewnij się, że struktura folderów wejściowych jest poprawna i że plik file.txt istnieje w folderach wejściowych.

   ![Struktura folderów wejściowych](./media/data-factory-data-processing-using-batch/image3.png)

1. W **Execute** metody działania niestandardowego, użyj **IActivityLogger** obiektu do rejestrowania informacji, które ułatwia rozwiązywanie problemów. Zarejestrowane komunikaty są wyświetlane\_w pliku 0.log użytkownika.

   W **bloku OutputDataset** wybierz plasterek, aby wyświetlić blok **Plasterek danych** dla tego plasterka. W obszarze **Działanie jest uruchamiane**, zobaczysz jedno działanie uruchomione dla plasterka. Jeśli wybierzesz **pozycję Uruchom** na pasku poleceń, możesz uruchomić inną aktywność dla tego samego plasterka.

   Po wybraniu uruchomienia działania zostanie wyświetlony **blok Szczegóły uruchamiania działania** z listą plików dziennika. W pliku 0.log\_użytkownika są widoczne rejestrowane komunikaty. Po wystąpieniu błędu zostanie wyświetlonych trzy uruchomienia działania, ponieważ liczba ponownych prób jest ustawiona na 3 w potoku/działania JSON. Po wybraniu uruchomienia działania, zobaczysz pliki dziennika, które można przejrzeć, aby rozwiązać ten błąd.

   ![Dane wyjścioweStawy danych i blokki plasterka danych](./media/data-factory-data-processing-using-batch/image18.png)

   Na liście plików dziennika wybierz **user-0.log**. W prawym panelu zostaną wyświetlone wyniki korzystania z **metody IActivityLogger.Write.**

   ![Blok szczegółów przebiegu działania](./media/data-factory-data-processing-using-batch/image19.png)

   Sprawdź system-0.log dla wszystkich komunikatów o błędach systemu i wyjątków.

    ```
    Trace\_T\_D\_12/6/2015 1:43:35 AM\_T\_D\_\_T\_D\_Verbose\_T\_D\_0\_T\_D\_Loading assembly file MyDotNetActivity...

    Trace\_T\_D\_12/6/2015 1:43:35 AM\_T\_D\_\_T\_D\_Verbose\_T\_D\_0\_T\_D\_Creating an instance of MyDotNetActivityNS.MyDotNetActivity from assembly file MyDotNetActivity...

    Trace\_T\_D\_12/6/2015 1:43:35 AM\_T\_D\_\_T\_D\_Verbose\_T\_D\_0\_T\_D\_Executing Module

    Trace\_T\_D\_12/6/2015 1:43:38 AM\_T\_D\_\_T\_D\_Information\_T\_D\_0\_T\_D\_Activity e3817da0-d843-4c5c-85c6-40ba7424dce2 finished successfully
    ```
1. Dołącz plik **PDB** do pliku zip, aby szczegóły błędu zawierały informacje, takie jak stos wywołań, gdy wystąpi błąd.

1. Wszystkie pliki w pliku zip dla działania niestandardowego muszą znajdować się na najwyższym poziomie bez podfolderów.

   ![Lista plików zip działania niestandardowego](./media/data-factory-data-processing-using-batch/image20.png)

1. Upewnij się, że **nazwa zestawu** (MyDotNetActivity.dll), **entryPoint** (MyDotNetActivityNS.MyDotNetActivity), **packageFile** (customactivitycontainer/MyDotNetActivity.zip) i **packageLinkedService** (powinna wskazywać magazyn obiektów blob zawierający plik zip) są ustawione na poprawne wartości.

1. Jeśli naprawiono błąd i chcesz ponownie przetworzyć plasterek, kliknij plasterek prawym przyciskiem myszy w bloku **OutputDataset** i wybierz polecenie **Uruchom**.

   ![Opcja uruchamiania bloku OutputDataset](./media/data-factory-data-processing-using-batch/image21.png)

   > [!NOTE]
   > Kontener znajduje się w magazynie `adfjobs`obiektów blob o nazwie . Ten kontener nie jest automatycznie usuwany, ale można go bezpiecznie usunąć po zakończeniu testowania rozwiązania. Podobnie rozwiązanie fabryki danych tworzy zadanie `adf-\<pool ID/name\>:job-0000000001`batch o nazwie . Możesz usunąć to zadanie po przetestowaniu rozwiązania, jeśli chcesz.
   >
   >
1. Działanie niestandardowe nie używa pliku **app.config** z pakietu. W związku z tym jeśli kod odczytuje żadnych ciągów połączeń z pliku konfiguracji, nie działa w czasie wykonywania. Najlepszym rozwiązaniem podczas korzystania z usługi Batch jest do przechowywania wszelkich wpisów tajnych w usłudze Azure Key Vault. Następnie użyj jednostki usługi opartej na certyfikatach, aby chronić magazyn kluczy i dystrybuować certyfikat do puli usługi Batch. Działanie niestandardowe platformy .NET może uzyskiwać dostęp do wpisów tajnych z magazynu kluczy w czasie wykonywania. To ogólne rozwiązanie można skalować do dowolnego typu klucza tajnego, a nie tylko ciągu połączenia.

    Istnieje łatwiejsze obejście, ale nie jest to najlepsze rozwiązanie. Usługę połączeniową bazy danych SQL można utworzyć z ustawieniami ciągu połączenia. Następnie można utworzyć zestaw danych, który używa połączonej usługi i łańcuch zestawu danych jako fikcyjnego zestawu danych wejściowych do niestandardowego działania .NET. Następnie można uzyskać dostęp do ciągu połączenia usługi połączonej w kodzie działania niestandardowego. Powinien działać poprawnie w czasie wykonywania.  

#### <a name="extend-the-sample"></a>Rozszerz próbkę
Można rozszerzyć ten przykład, aby dowiedzieć się więcej o fabryce danych i funkcji partii. Na przykład, aby przetworzyć plasterki w innym zakresie czasu, należy wykonać następujące kroki:

1. Dodaj następujące podfoldery `inputfolder`w: 2015-11-16-05, 2015-11-16-06, 201-11-16-07, 2011-11-16-08 i 2015-11-16-09. Umieść pliki wejściowe w tych folderach. Zmień czas zakończenia potoku `2015-11-16T05:00:00Z` `2015-11-16T10:00:00Z`z na . W widoku **Diagram** kliknij dwukrotnie **pozycję InputDataset** i upewnij się, że wycinki wejściowe są gotowe. Kliknij dwukrotnie **pozycję OutputDataset,** aby zobaczyć stan plasterków danych wyjściowych. Jeśli są one w stanie **Gotowy,** sprawdź folder wyjściowy dla plików wyjściowych.

1. Zwiększ lub zmniejsz ustawienie **współbieżności,** aby zrozumieć, jak wpływa na wydajność rozwiązania, zwłaszcza przetwarzania, które występuje w u partii. Aby uzyskać więcej informacji na temat ustawienia **współbieżności,** zobacz "Krok 4: Tworzenie i uruchamianie potoku z działaniem niestandardowym."

1. Utwórz pulę z wyższymi/niższymi **zadaniami Maksymalna na maszynę wirtualną**. Aby użyć nowej puli, która została utworzona, zaktualizuj usługę połączony przez partię w rozwiązaniu fabryki danych. Aby uzyskać więcej informacji na temat **ustawienia Maksymalna liczba zadań na maszynę wirtualną,** zobacz "Krok 4: Tworzenie i uruchamianie potoku za pomocą działania niestandardowego".

1. Utwórz pulę wsadową za pomocą funkcji **skalowania automatycznego.** Automatyczne skalowanie węzłów obliczeniowych w puli partii jest dynamiczną regulacją mocy obliczeniowej używanej przez aplikację.

    Przykładowa formuła w tym miejscu osiąga następujące zachowanie. Gdy pula jest początkowo tworzony, rozpoczyna się od jednej maszyny Wirtualnej. Metryka $PendingTasks definiuje liczbę zadań w stanach uruchomionych i aktywnych (w kolejce). Formuła znajduje średnią liczbę oczekujących zadań w ciągu ostatnich 180 sekund i odpowiednio ustawia targetDedicated. Zapewnia, że TargetDedicated nigdy nie wykracza poza 25 maszyn wirtualnych. W miarę przesyłania nowych zadań pula automatycznie rośnie. Po zakończeniu zadań maszyny wirtualne stają się wolne jeden po drugim, a skalowanie automatyczne zmniejsza te maszyny wirtualne. Można dostosować startingNumberOfVMs i maxNumberofVMs do swoich potrzeb.

    Formuła skalowania automatycznego:

    ```
    startingNumberOfVMs = 1;
    maxNumberofVMs = 25;
    pendingTaskSamplePercent = $PendingTasks.GetSamplePercent(180 * TimeInterval_Second);
    pendingTaskSamples = pendingTaskSamplePercent < 70 ? startingNumberOfVMs : avg($PendingTasks.GetSample(180 * TimeInterval_Second));
    $TargetDedicated=min(maxNumberofVMs,pendingTaskSamples);
    ```

   Aby uzyskać więcej informacji, zobacz [Automatyczne skalowanie węzłów obliczeniowych w puli partii](../../batch/batch-automatic-scaling.md).

   Jeśli pula używa domyślnej [automatycznejaleniewycenacjeInterwalne,](https://msdn.microsoft.com/library/azure/dn820173.aspx)usługa Batch może potrwać od 15 do 30 minut, aby przygotować maszynę wirtualną przed uruchomieniem działania niestandardowego. Jeśli pula używa innego autoScaleEvaluationInterval, usługa Batch może potrwać autoScaleEvaluationInterval plus 10 minut.

1. W przykładowym rozwiązaniu **Execute** Metoda wywołuje **Calculate** metody, która przetwarza plasterek danych wejściowych do produkcji fragment danych wyjściowych. Można napisać własną metodę do przetwarzania danych wejściowych i zastąpić **Wywołać Calculate** metody w **Execute** metody wywołanie metody.

### <a name="next-steps-consume-the-data"></a>Następne kroki: Korzystanie z danych
Po przetworzeniu danych można je używać za pomocą narzędzi online, takich jak usługa Power BI. Oto łącza ułatwiające zrozumienie usługi Power BI i używanie jej na platformie Azure:

* [Eksplorowanie zestawu danych w usłudze Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-get-data/)
* [Wprowadzenie do programu Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-getting-started/)
* [Odświeżanie danych w usłudze Power BI](https://powerbi.microsoft.com/documentation/powerbi-refresh-data/)
* [Platforma Azure i usługa Power BI: podstawowe omówienie](https://powerbi.microsoft.com/documentation/powerbi-azure-and-power-bi/)

## <a name="references"></a>Dokumentacja
* [Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/)

  * [Wprowadzenie do usługi Data Factory](data-factory-introduction.md)
  * [Wprowadzenie do usługi Data Factory](data-factory-build-your-first-pipeline.md)
  * [Używanie działań niestandardowych w potoku usługi Data Factory](data-factory-use-custom-activities.md)
* [Azure Batch](https://azure.microsoft.com/documentation/services/batch/)

  * [Podstawy partii](../../batch/batch-technical-overview.md)
  * [Omówienie funkcji usługi Batch](../../batch/batch-api-basics.md)
  * [Tworzenie konta usługi Batch i zarządzanie nim w witrynie Azure portal](../../batch/batch-account-create-portal.md)
  * [Wprowadzenie do biblioteki klienta usługi Batch dla platformy .NET](../../batch/quick-run-dotnet.md)

[batch-explorer]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[batch-explorer-walkthrough]: https://blogs.technet.com/b/windowshpc/archive/2015/01/20/azure-batch-explorer-sample-walkthrough.aspx
