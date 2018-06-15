---
title: 'Samouczek: Tworzenie na żądanie klastrów platformy Hadoop w usłudze Azure HDInsight przy użyciu fabryki danych | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak utworzyć na żądanie klastrów platformy Hadoop w usłudze HDInsight przy użyciu fabryki danych Azure.
services: hdinsight
documentationcenter: ''
tags: azure-portal
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 1f3b3a78-4d16-4d99-ba6e-06f7bb185d6a
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 05/07/2018
ms.author: nitinme
ms.openlocfilehash: 53ff14e00b88f6d182579ba0d9df630fae9b3d78
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
ms.locfileid: "33771137"
---
# <a name="tutorial-create-on-demand-hadoop-clusters-in-hdinsight-using-azure-data-factory"></a>Samouczek: Tworzenie na żądanie klastrów platformy Hadoop w usłudze HDInsight przy użyciu fabryki danych Azure
[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

W tym artykule należy Dowiedz się, jak utworzyć klaster usługi Hadoop na żądanie w usłudze Azure HDInsight przy użyciu fabryki danych Azure. Następnie użyj potoki danych w fabryce danych Azure do uruchamiania zadań Hive oraz usunięcie klastra. Na koniec tego samouczka zostanie przedstawiony sposób operacjonalizacji zadania danych big data, uruchamianie, gdzie tworzenia klastra, uruchom zadanie i usuwanie klastra są wykonywane zgodnie z harmonogramem.

Ten samouczek obejmuje następujące zadania: 

> [!div class="checklist"]
> * Tworzenie konta usługi Azure Storage
> * Zrozumienie działania fabryki danych Azure
> * Tworzenie fabryki danych przy użyciu portalu Azure
> * Tworzenie połączonych usług
> * Tworzenie potoku
> * Wyzwalacz potoku
> * Monitorowanie potoku
> * Sprawdzanie danych wyjściowych

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

* Azure PowerShell. Aby uzyskać instrukcje, zobacz [Instalowanie i konfigurowanie programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-5.7.0).

* Jednostki usługi Azure Active Directory. Po utworzeniu nazwy głównej usługi, należy pobrać **identyfikator aplikacji** i **klucz uwierzytelniania** korzystając z instrukcji w połączonych artykułu. Te wartości muszą się w dalszej części tego samouczka. Sprawdź także, czy nazwy głównej usługi jest członkiem *współautora* roli subskrypcji lub grupy zasobów, w której jest tworzony klaster. Aby uzyskać instrukcje dotyczące pobierania wymagane wartości i przypisz odpowiednich ról, zobacz [Tworzenie nazwy głównej usługi Azure Active Directory](../azure-resource-manager/resource-group-create-service-principal-portal.md).

## <a name="create-an-azure-storage-account"></a>Tworzenie konta usługi Azure Storage

W tej sekcji utworzysz konto magazynu, który będzie używany jako domyślny magazyn dla klastra usługi HDInsight, tworzonych na żądanie. To konto magazynu zawiera także przykładowy skrypt HiveQL (**hivescript.hql**) używanego do symulowania przykładowe zadania Hive, która działa w klastrze.

Ta sekcja używa skrypt programu Azure PowerShell można utworzyć konta magazynu i skopiuj pliki wymagane w ramach konta magazynu. Przykładowy skrypt programu PowerShell systemu Azure w tej sekcji wykonuje następujące zadania:

1. Dzienniki w na platformie Azure.
2. Tworzy grupę zasobów platformy Azure.
3. Tworzy konto usługi Azure Storage.
4. Tworzy kontener obiektów Blob na koncie magazynu
5. Kopiuje przykładowy skrypt HiveQL (**hivescript.hql**) kontenera obiektów Blob. Skrypt jest dostępny na [ https://hditutorialdata.blob.core.windows.net/adfv2hiveactivity/hivescripts/hivescript.hql ](https://hditutorialdata.blob.core.windows.net/adfhiveactivity/script/partitionweblogs.hql). Przykładowy skrypt jest już dostępny w innym publicznego kontenera obiektów Blob. Poniższy skrypt programu PowerShell tworzy kopię tych plików do utworzonego konta usługi Magazyn Azure.


**Aby utworzyć konto magazynu i skopiować pliki przy użyciu programu Azure PowerShell:**
> [!IMPORTANT]
> Określ nazwy grupy zasobów platformy Azure i konto magazynu Azure, który zostanie utworzony przez skrypt.
> Zapisz **Nazwa grupy zasobów**, **nazwy konta magazynu**, i **klucz konta magazynu** wyjściowych przez skrypt. Należy je w następnej sekcji.

```powershell
$resourceGroupName = "<Azure Resource Group Name>"
$storageAccountName = "<Azure Storage Account Name>"
$location = "East US 2"

$sourceStorageAccountName = "hditutorialdata"  
$sourceContainerName = "adfv2hiveactivity"

$destStorageAccountName = $storageAccountName
$destContainerName = "adfgetstarted" # don't change this value.

####################################
# Connect to Azure
####################################
#region - Connect to Azure subscription
Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
<<<<<<< HEAD
Login-AzureRmAccount
=======
try{Get-AzureRmContext}
catch{Connect-AzureRmAccount}
>>>>>>> refs/remotes/MicrosoftDocs/release-build-hdinsight-2018
#endregion

####################################
# Create a resource group, storage, and container
####################################

#region - create Azure resources
Write-Host "`nCreating resource group, storage account and blob container ..." -ForegroundColor Green

New-AzureRmResourceGroup -Name $resourceGroupName -Location $location
New-AzureRmStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $destStorageAccountName `
    -type Standard_LRS `
    -Location $location

$destStorageAccountKey = (Get-AzureRmStorageAccountKey `
    -ResourceGroupName $resourceGroupName `
    -Name $destStorageAccountName)[0].Value

$sourceContext = New-AzureStorageContext `
    -StorageAccountName $sourceStorageAccountName `
    -Anonymous
$destContext = New-AzureStorageContext `
    -StorageAccountName $destStorageAccountName `
    -StorageAccountKey $destStorageAccountKey

New-AzureStorageContainer -Name $destContainerName -Context $destContext
#endregion

####################################
# Copy files
####################################
#region - copy files
Write-Host "`nCopying files ..." -ForegroundColor Green

$blobs = Get-AzureStorageBlob `
    -Context $sourceContext `
    -Container $sourceContainerName

$blobs|Start-AzureStorageBlobCopy `
    -DestContext $destContext `
    -DestContainer $destContainerName

Write-Host "`nCopied files ..." -ForegroundColor Green
Get-AzureStorageBlob -Context $destContext -Container $destContainerName
#endregion

Write-host "`nYou will use the following values:" -ForegroundColor Green
write-host "`nResource group name: $resourceGroupName"
Write-host "Storage Account Name: $destStorageAccountName"
write-host "Storage Account Key: $destStorageAccountKey"

Write-host "`nScript completed" -ForegroundColor Green
```

**Aby sprawdzić, tworzenie konta magazynu**

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Wybierz **grup zasobów** w lewym okienku.
3. Kliknij dwukrotnie nazwę grupy zasobów utworzonej za pomocą skryptu programu PowerShell. Jeśli masz zbyt wiele grup zasobów na liście, użyj filtru.
4. Na **zasobów** kafelka, zobaczysz jednego zasobu, chyba że współużytkować grupy zasobów z innymi projektami na liście. Ten zasób jest konto magazynu o nazwie określone wcześniej. Wybierz nazwę konta magazynu.
5. Wybierz **obiekty BLOB** Kafelki.
6. Wybierz **adfgetstarted** kontenera. Zobacz folder o nazwie **hivescripts**.
7. Otwórz folder i upewnij się, że zawiera przykładowy plik skryptu **hivescript.hql**.

## <a name="understand-the-azure-data-factory-activity"></a>Zrozumienie działania fabryki danych Azure

[Fabryka danych Azure](../data-factory/introduction.md) organizuje i automatyzuje przepływu i przekształcania danych. Fabryka danych Azure można utworzyć usługi HDInsight Hadoop klastra just-in-time do przetwarzania wycinka danych wejściowych i usunąć klaster, po zakończeniu przetwarzania. 

W fabryce danych Azure fabryki danych może mieć co najmniej jeden potoki danych. Potoku danych ma co najmniej jednego działania. Istnieją dwa typy działań:

* [Działania przepływu danych](../data-factory/copy-activity-overview.md) -działań przepływu danych jest używane do przenoszenia danych z magazynu danych źródła w magazynie danych docelowego.
* [Działania przekształcania danych](../data-factory/transform-data.md). Działania przekształcania danych służy do procesu przekształcenia danych. Działanie Hive HDInsight jest jednym z działania przekształcania obsługiwane przez fabryki danych. Działanie przekształcania Hive jest służy w tym samouczku.

W tym artykule możesz skonfigurować działanie gałęzi do utworzenia klastra usługi Hadoop w HDInsight na żądanie. Po uruchomieniu działania do przetwarzania danych, Oto, co się stanie:

1. Klaster usługi HDInsight Hadoop jest tworzona automatycznie dla możesz just-in-time przetwarzania wycinka. 

2. Dane wejściowe są przetwarzane przez uruchomienie skryptu HiveQL w klastrze. W tym samouczku skrypt HiveQL skojarzone z działaniem hive wykonuje następujące czynności:

    * Korzysta z istniejącej tabeli (*hivesampletable*) można utworzyć innej tabeli **HiveSampleOut**.
    * Wypełnia **HiveSampleOut** tabelę zawierającą tylko określone kolumny od oryginału *hivesampletable*.

3. Klaster usługi HDInsight Hadoop jest usuwany po zakończeniu przetwarzania i klastra jest w stanie bezczynności skonfigurowanych ilość czasu (ustawienie timeToLive). Jeśli dalej wycinek danych jest dostępne do przetwarzania z tego czasu bezczynności timeToLive, tego samego klastra służy do przetwarzania wycinka.  

## <a name="create-a-data-factory"></a>Tworzenie fabryki danych

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com/).

2. W portalu Azure wybierz **Utwórz zasób** > **dane i analiza** > **fabryki danych**.

    ![Fabryka danych Azure w portalu](./media/hdinsight-hadoop-create-linux-clusters-adf/data-factory-azure-portal.png "fabryki danych Azure w portalu")

2. Wprowadź lub wybierz wartości, jak pokazano na poniższym zrzucie ekranu:

    ![Tworzenie fabryki danych Azure przy użyciu portalu Azure](./media/hdinsight-hadoop-create-linux-clusters-adf/create-data-factory-portal.png "tworzenie fabryki danych Azure przy użyciu portalu Azure")

    Wprowadź lub wybierz poniższe wartości:
    
    |Właściwość  |Opis  |
    |---------|---------|
    |**Nazwa** |  Wprowadź nazwę dla fabryki danych. Ta nazwa musi być globalnie unikatowa.|
    |**Subskrypcja**     |  Wybierz swoją subskrypcję platformy Azure. |
    |**Grupa zasobów**     | Wybierz **Użyj istniejącego** , a następnie wybierz grupę zasobów, które zostały utworzone za pomocą skryptu programu PowerShell. |
    |**Wersja**     | Wybierz **V2 (wersja zapoznawcza)** |
    |**Lokalizacja**     | Lokalizacja jest ustawiana automatycznie do lokalizacji, w której można określić podczas tworzenia grupy zasobów wcześniej. W tym samouczku, lokalizacja jest ustawiana **wschodnie stany USA 2**. |
    

3. Wybierz **Przypnij do pulpitu nawigacyjnego**, a następnie wybierz **Utwórz**. Powinien zostać wyświetlony nowy Kafelek zatytułowany **przesyłanie wdrożenia** na pulpicie nawigacyjnym portalu. Tworzenie fabryki danych może potrwać od 2 do 4 minut.

    ![Postęp wdrażania szablonu](./media/hdinsight-hadoop-create-linux-clusters-adf/deployment-progress-tile.png "postępu wdrożenia szablonu") 
 
4. Po utworzeniu fabryki danych portalu zawiera omówienie dla fabryki danych.

    ![Omówienie usługi fabryka danych Azure](./media/hdinsight-hadoop-create-linux-clusters-adf/data-factory-portal-overview.png "fabryki danych Azure — omówienie")

5. Wybierz **autora & Monitor** można uruchomić fabryki danych Azure, tworzenia i monitorowania portalu.

## <a name="create-linked-services"></a>Tworzenie połączonych usług

W tej sekcji można tworzyć dwa połączone usługi w fabryce danych.

- **Połączoną usługę Azure Storage**, która łączy konto usługi Azure Storage z fabryką danych. Ten magazyn jest używany przez klaster usługi HDInsight na żądanie. Zawiera ona także skryptu Hive, który jest uruchamiany w klastrze.
- **Połączoną usługę HDInsight dostępną na żądanie**. Fabryka danych Azure automatycznie tworzy klaster usługi HDInsight i uruchamia skrypt Hive. Następnie usuwa klaster usługi HDInsight, gdy jest on bezczynny przez wstępnie skonfigurowany czas.

###  <a name="create-an-azure-storage-linked-service"></a>Tworzenie połączonej usługi Azure Storage

1. W lewym okienku **Rozpocznijmy** wybierz pozycję **Edytuj** ikony.

    ![Tworzenie usługi fabryka danych Azure połączone](./media/hdinsight-hadoop-create-linux-clusters-adf/data-factory-edit-tab.png "tworzenie fabryki danych Azure połączone usługi")

2. Wybierz **połączeń** z lewym dolnym rogu okna, a następnie wybierz **+ nowy**.

    ![Utwórz połączenia w fabryce danych Azure](./media/hdinsight-hadoop-create-linux-clusters-adf/data-factory-create-new-connection.png "tworzenia połączeń z fabryki danych Azure")

3. W **nowej usługi połączonej** okno dialogowe, wybierz opcję **magazyn obiektów Blob Azure** , a następnie wybierz **Kontynuuj**.

    ![Tworzenie usługi Azure Storage połączonej usługi fabryki danych](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-storage-linked-service.png "połączonej usługi fabryki danych tworzenia magazynu Azure")

4. Podaj nazwę dla połączonej usługi magazynu, wybierz utworzone konto magazynu Azure w ramach skryptu programu PowerShell, a następnie wybierz **Zakończ**.

    ![Podaj nazwę dla usługi Azure Storage połączona usługa](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-storage-linked-service-details.png "Podaj nazwę dla usługi Azure Storage połączona usługa")

### <a name="create-an-on-demand-hdinsight-linked-service"></a>Tworzenie połączonej usługi HDInsight na żądanie

1. Wybierz ponownie przycisk **+ Nowy**, aby utworzyć kolejną połączoną usługę.

2. W oknie **Nowa połączona usługa** wybierz kolejno pozycje **Compute** > **Azure HDInsight**, a następnie kliknij pozycję **Kontynuuj**.

    ![HDInsight tworzenie połączonej usługi fabryki danych Azure](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-linked-service.png "HDInsight tworzenie połączonej usługi fabryki danych Azure")

3. W **nowej usługi połączonej** okna, podaj wartości wymagane.

    ![Podaj wartości dla usługi HDInsight połączona usługa](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-linked-service-details.png "Podaj wartości dla usługi HDInsight połączona usługa")

    Wprowadź następujące wartości i pozostawić rest jako domyślny.

    | Właściwość | Opis |
    | --- | --- |
    | Name (Nazwa) | Wprowadź nazwę dla usługi HDInsight połączone |
    | Typ | Wybierz **HDInsight na żądanie** |
    | Połączona usługa Azure Storage | Wybierz połączoną usługą magazynu utworzony wcześniej. |
    | Typ klastra | Wybierz **hadoop** |
    | Czas wygaśnięcia | Podaj czas trwania, dla której ma zostać klastra usługi HDInsight, które mają być dostępne, zanim zostaną automatycznie usunięte.|
    | Identyfikator jednostki usługi | Podaj identyfikator aplikacji utworzonej w ramach wymagań wstępnych nazwy głównej usługi Azure Active Directory |
    | Klucz główny usługi | Wprowadź klucz uwierzytelniania dla nazwy głównej usługi Azure Active Directory |
    | Prefiks nazwy klastra | Podaj wartość, która będzie ona poprzedzona dla wszystkich typów klastra, które zostały utworzone przy użyciu fabryki danych |
    | Grupa zasobów | Wybierz jako część skrypt programu PowerShell używanego wcześniej utworzoną grupę zasobów| 
    | Nazwa użytkownika SSH klastra | Wprowadź nazwę użytkownika SSH |
    | Hasło SSH klastra | Podaj hasło dla użytkownika SSH |

    Wybierz pozycję **Finish** (Zakończ).

## <a name="create-a-pipeline"></a>Tworzenie potoku

1. Wybierz przycisk **+** (znak plus), a następnie wybierz pozycję **Potok**.

    ![Utworzyć potok w fabryce danych Azure](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-create-pipeline.png "utworzyć potok w fabryce danych Azure")

2. W **działania** przybornika, rozwiń węzeł **HDInsight**i przeciągnij **Hive** działania na powierzchnię projektanta potoku. W **ogólne** karcie, podaj nazwę działania.

    ![Dodawanie działań do potoku fabryki danych](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-add-hive-pipeline.png "dodawanie działań do potoku fabryki danych")

3. Upewnij się, że wybrane działanie Hive, wybierz pozycję **klastra HDI** kartę i z **połączoną usługą usługi HDInsight** listy rozwijanej, wybierz połączonej usługi utworzony wcześniej dla usługi HDInsight.

    ![Podaj szczegóły klastra usługi HDInsight dla potoku](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-hive-activity-select-hdinsight-linked-service.png "szczegółów klastra HDInsight zapewniają dla potoku")

4. Wybierz **skryptu** karcie i wykonaj następujące czynności:

    a. Aby uzyskać **skryptu połączonej usługi**, wybierz pozycję **HDIStorageLinkedService**. Ta wartość jest połączoną usługą magazynu utworzony wcześniej.

    b. Dla **ścieżka pliku**, wybierz pozycję **Przeglądaj magazynu** i przejdź do lokalizacji, w którym znajduje się przykładowy skrypt Hive. Jeśli skrypt programu PowerShell został przeprowadzony wcześniej, to lokalizacja powinna być `adfgetstarted/hivescripts/hivescript.hql`.

    ![Podaj szczegóły skryptu Hive dla potoku](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-provide-script-path.png "Hive Podaj szczegóły skryptu dla potoku")

    c. W obszarze **zaawansowane** > **parametry**, wybierz pozycję **automatyczne wypełnianie ze skryptu**. Ta opcja jest szuka żadnych parametrów skryptu Hive, które wymagają wartości w czasie wykonywania. Możesz użyć skryptu (**hivescript.hql**) ma **dane wyjściowe** parametru. Podaj wartość w formacie `wasb://<Container>@<StorageAccount>.blob.core.windows.net/outputfolder/` wskaż istniejący folder w magazynie Azure. W ścieżce jest rozróżniana wielkość liter. Jest to ścieżka, w którym będą przechowywane dane wyjściowe skryptu.

    ![Podaj parametry skryptu Hive](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-provide-script-parameters.png "podać parametry skryptu Hive")

5. Wybierz **weryfikacji** do sprawdzania poprawności potoku. Wybierz przycisk **>>** (strzałka w prawo), aby zamknąć okno weryfikacji.

    ![Sprawdź poprawność potoku fabryki danych Azure](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-validate-all.png "zweryfikować potoku fabryki danych Azure")

5. Na koniec wybierz **opublikować wszystkie** do publikowania artefaktów z fabryką danych Azure.

    ![Publikowanie potoku fabryki danych Azure](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-publish-pipeline.png "publikowania potoku fabryki danych Azure")

## <a name="trigger-a-pipeline"></a>Wyzwalacz potoku

1. Na pasku narzędzi na powierzchni projektowej wybierz **wyzwalacza** > **wyzwalacza teraz**.

    ![Wyzwalanie potoku fabryki danych Azure](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-trigger-pipeline.png "wyzwolenia potoku fabryki danych Azure")

2. Wybierz **Zakończ** w paska bocznego wyskakujących.

## <a name="monitor-a-pipeline"></a>Monitorowanie potoku

1. Przejdź do karty **Monitorowanie** po lewej stronie. Uruchomienie potoku zostanie wyświetlone na liście **Uruchomienia potoku**. Zwróć uwagę, stan potrzebne do uruchomienia **stan** kolumny.

    ![Monitorowanie potoku fabryki danych Azure](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-monitor-pipeline.png "monitorować potoku fabryki danych Azure")

2. Wybierz pozycję **Odśwież**, aby odświeżyć stan.

3. Możesz też wybrać **uruchomień działania widoku** ikonę, aby zobaczyć uruchamiania działania związane z potoku. Na poniższym zrzucie ekranu Zobacz temat tylko jedno działanie uruchamiać, ponieważ istnieje tylko jedno działanie w potoku, który został utworzony. Aby powrócić do poprzedniego widoku, wybierz **potoki** kierunku górnej części strony.

    ![Monitorowanie aktywności potoku fabryki danych Azure](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-monitor-pipeline-activity.png "monitorowania aktywności potoku fabryki danych Azure")


## <a name="verify-the-output"></a>Sprawdzanie danych wyjściowych

1. Aby sprawdzić, dane wyjściowe, w portalu Azure przejdź do konta magazynu, który został użyty w tym samouczku. Powinny być widoczne następujące foldery lub kontenerów:

    - Zostanie wyświetlony **adfgerstarted/outputfolder** zawierający dane wyjściowe skryptu Hive, który został uruchomiony jako część potoku.

    - Zostanie wyświetlony **adfhdidatafactory -\<połączone service-name >-\<sygnatury czasowej >** kontenera. Ten kontener jest domyślną lokalizację przechowywania klastra usługi HDInsight, który został utworzony jako część uruchomienia procesu.

    - Zostanie wyświetlony **adfjobs** dzienniki kontener, który ma zadania fabryki danych Azure.  

        ![Sprawdź dane wyjściowe potoku fabryki danych Azure](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-verify-output.png "Sprawdź dane wyjściowe potoku fabryki danych Azure")


## <a name="clean-up-the-tutorial"></a>Czyszczenie na koniec samouczka

Z na deman tworzenia klastra usługi HDInsight, ale nie trzeba jawnie usunąć klaster usługi HDInsight. Usunąć klastra na podstawie konfiguracji podane podczas tworzenia potoku. Jednak nawet po usunięciu klastra konta magazynu skojarzone z klastrem nadal istnieje. To zachowanie jest zgodne z założeniami, dzięki czemu można zachować dane bez zmian. Jednak, jeśli nie chcesz zachować dane, można usunąć utworzone konto magazynu.

Alternatywnie można usunąć grupę zasobów całej, utworzony na potrzeby tego samouczka. Spowoduje to usunięcie konta magazynu i fabryki danych Azure, który został utworzony.

### <a name="delete-the-resource-group"></a>Usuń grupę zasobów

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Wybierz **grup zasobów** w lewym okienku.
3. Wybierz nazwę grupy zasobów utworzonej za pomocą skryptu programu PowerShell. Jeśli masz zbyt wiele grup zasobów na liście, użyj filtru. Spowoduje to otwarcie grupy zasobów.
4. Na **zasobów** kafelka, użytkownik ma domyślne konto magazynu i fabryki danych, chyba że współużytkować grupy zasobów z innymi projektami na liście.
5. Wybierz pozycję **Usuń grupę zasobów**. To spowoduje usunięcie konta magazynu i dane przechowywane na koncie magazynu.

    ![Usuń grupę zasobów](./media/hdinsight-hadoop-create-linux-clusters-adf/delete-resource-group.png "Usuń grupę zasobów")

6. Wprowadź nazwę grupy zasobów, aby potwierdzić usunięcie, a następnie wybierz **usunąć**.


## <a name="next-steps"></a>Kolejne kroki
W tym artykule przedstawiono sposób tworzenia klastra usługi HDInsight na żądanie i uruchamiania zadań Hive za pomocą fabryki danych Azure. Przejdź do następnego artciel informacje na temat tworzenia klastrów usługi HDInsight z konfiguracji niestandardowej.

> [!div class="nextstepaction"]
>[Tworzenie klastrów usługi HDInsight Azure z konfiguracji niestandardowej](hdinsight-hadoop-provision-linux-clusters.md)


