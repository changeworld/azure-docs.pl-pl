---
title: 'Samouczek: Klastry na żądanie w usłudze Azure HDInsight z fabryką danych'
description: Samouczek — dowiedz się, jak utworzyć klastry Apache Hadoop na żądanie w programie HDInsight przy użyciu usługi Azure Data Factory.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: tutorial
ms.date: 03/18/2020
ms.openlocfilehash: b184a42c52384440445181ac44c616c3139e064f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "80130688"
---
# <a name="tutorial-create-on-demand-apache-hadoop-clusters-in-hdinsight-using-azure-data-factory"></a>Samouczek: Tworzenie klastrów Apache Hadoop na żądanie w programie HDInsight przy użyciu usługi Azure Data Factory

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

W tym samouczku dowiesz się, jak utworzyć klaster [Apache Hadoop](./hadoop/apache-hadoop-introduction.md) na żądanie w usłudze Azure HDInsight przy użyciu usługi Azure Data Factory. Następnie użyj potoków danych w usłudze Azure Data Factory, aby uruchomić zadania hive i usunąć klaster. Pod koniec tego samouczka dowiesz się, jak operacjonalizacji zadania dużych zbiorów danych, w którym tworzenie klastra, uruchamianie zadań i usuwanie klastra są wykonywane zgodnie z harmonogramem.

Ten samouczek obejmuje następujące zadania:

> [!div class="checklist"]
> * Tworzenie konta usługi Azure Storage
> * Opis działania usługi Azure Data Factory
> * Tworzenie fabryki danych przy użyciu portalu Azure
> * Tworzenie połączonych usług
> * Tworzenie potoku
> * Wyzwalanie potoku
> * Monitorowanie potoku
> * Sprawdzanie danych wyjściowych

Jeśli nie masz subskrypcji platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed rozpoczęciem.

## <a name="prerequisites"></a>Wymagania wstępne

* Zainstalowano [moduł Az](https://docs.microsoft.com/powershell/azure/overview) programu PowerShell.

* Podmiot usługi Azure Active Directory. Po utworzeniu jednostki usługi należy pobrać **identyfikator aplikacji** i **klucz uwierzytelniania** przy użyciu instrukcji w połączonym artykule. Te wartości są potrzebne w dalszej części tego samouczka. Ponadto upewnij się, że podmiot zabezpieczeń usługi jest członkiem *roli współautora* subskrypcji lub grupy zasobów, w której jest tworzony klaster. Aby uzyskać instrukcje pobierania wymaganych wartości i przypisywania odpowiednich ról, zobacz [Tworzenie jednostki usługi Azure Active Directory](../active-directory/develop/howto-create-service-principal-portal.md).

## <a name="create-preliminary-azure-objects"></a>Tworzenie wstępnych obiektów platformy Azure

W tej sekcji utworzysz różne obiekty, które będą używane dla klastra HDInsight, który tworzysz na żądanie. Utworzone konto magazynu będzie zawierać przykładowy skrypt `partitionweblogs.hql` [HiveQL,](https://cwiki.apache.org/confluence/display/Hive/LanguageManual) który służy do symulowania przykładowego zadania gałęzi Apache, które jest uruchamiane w klastrze.

W tej sekcji używa skryptu programu Azure PowerShell do utworzenia konta magazynu i skopiowania wymaganych plików na koncie magazynu. Przykładowy skrypt programu Azure PowerShell w tej sekcji wykonuje następujące zadania:

1. Loguje się na platformę Azure.
2. Tworzy grupę zasobów platformy Azure.
3. Tworzy konto usługi Azure Storage.
4. Tworzy kontener obiektu Blob na koncie magazynu
5. Kopiuje przykładowy skrypt HiveQL (**partitionweblogs.hql**) kontenera obiektów blob. Skrypt jest dostępny [https://hditutorialdata.blob.core.windows.net/adfhiveactivity/script/partitionweblogs.hql](https://hditutorialdata.blob.core.windows.net/adfhiveactivity/script/partitionweblogs.hql)pod adresem . Przykładowy skrypt jest już dostępny w innym publicznym kontenerze obiektów Blob. Poniższy skrypt programu PowerShell tworzy kopię tych plików na konto usługi Azure Storage, które tworzy.

### <a name="create-storage-account-and-copy-files"></a>Tworzenie konta magazynu i kopiowanie plików

> [!IMPORTANT]  
> Określ nazwy grupy zasobów platformy Azure i konta magazynu platformy Azure, które zostaną utworzone przez skrypt.
> Zapisz **nazwę grupy zasobów,** **nazwę konta magazynu**i klucz konta **magazynu** wysuwający przez skrypt. Potrzebujesz ich w następnej sekcji.

```powershell
$resourceGroupName = "<Azure Resource Group Name>"
$storageAccountName = "<Azure Storage Account Name>"
$location = "East US"

$sourceStorageAccountName = "hditutorialdata"  
$sourceContainerName = "adfv2hiveactivity"

$destStorageAccountName = $storageAccountName
$destContainerName = "adfgetstarted" # don't change this value.

####################################
# Connect to Azure
####################################
#region - Connect to Azure subscription
Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
$sub = Get-AzSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Connect-AzAccount
}

# If you have multiple subscriptions, set the one to use
# Select-AzSubscription -SubscriptionId "<SUBSCRIPTIONID>"

#endregion

####################################
# Create a resource group, storage, and container
####################################

#region - create Azure resources
Write-Host "`nCreating resource group, storage account and blob container ..." -ForegroundColor Green

New-AzResourceGroup `
    -Name $resourceGroupName `
    -Location $location

New-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $destStorageAccountName `
    -Kind StorageV2 `
    -Location $location `
    -SkuName Standard_LRS `
    -EnableHttpsTrafficOnly 1

$destStorageAccountKey = (Get-AzStorageAccountKey `
    -ResourceGroupName $resourceGroupName `
    -Name $destStorageAccountName)[0].Value

$sourceContext = New-AzStorageContext `
    -StorageAccountName $sourceStorageAccountName `
    -Anonymous

$destContext = New-AzStorageContext `
    -StorageAccountName $destStorageAccountName `
    -StorageAccountKey $destStorageAccountKey

New-AzStorageContainer `
    -Name $destContainerName `
    -Context $destContext
#endregion

####################################
# Copy files
####################################
#region - copy files
Write-Host "`nCopying files ..." -ForegroundColor Green

$blobs = Get-AzStorageBlob `
    -Context $sourceContext `
    -Container $sourceContainerName `
    -Blob "hivescripts\hivescript.hql"

$blobs|Start-AzStorageBlobCopy `
    -DestContext $destContext `
    -DestContainer $destContainerName `
    -DestBlob "hivescripts\partitionweblogs.hql"

Write-Host "`nCopied files ..." -ForegroundColor Green
Get-AzStorageBlob `
    -Context $destContext `
    -Container $destContainerName
#endregion

Write-host "`nYou will use the following values:" -ForegroundColor Green
write-host "`nResource group name: $resourceGroupName"
Write-host "Storage Account Name: $destStorageAccountName"
write-host "Storage Account Key: $destStorageAccountKey"

Write-host "`nScript completed" -ForegroundColor Green
```

### <a name="verify-storage-account"></a>Weryfikowanie konta magazynu

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Od lewej przejdź do pozycji Wszystkie**grupy zasobów****ogólnych** >  **usług** > .
1. Wybierz nazwę grupy zasobów utworzoną w skrypcie programu PowerShell. Użyj filtru, jeśli na liście znajduje się zbyt wiele grup zasobów.
1. W widoku **Przegląd** zostanie wyświetlony jeden zasób na liście, chyba że grupa zasobów zostanie udostępniona innym projektom. Ten zasób jest kontem magazynu o nazwie określonej wcześniej. Wybierz nazwę konta magazynu.
1. Wybierz **kafelek Kontenery.**
1. Wybierz kontener **adfgetstarted.** Zostanie wyświetlony folder o nazwie **hivescripts**.
1. Otwórz folder i upewnij się, że zawiera przykładowy plik skryptu, **partitionweblogs.hql**.

## <a name="understand-the-azure-data-factory-activity"></a>Opis działania usługi Azure Data Factory

[Usługa Azure Data Factory](../data-factory/introduction.md) organizuje i automatyzuje przenoszenie i przekształcanie danych. Usługa Azure Data Factory może utworzyć klaster usługi HDInsight Hadoop just-in-time, aby przetworzyć wycinek danych wejściowych i usunąć klaster po zakończeniu przetwarzania.

W usłudze Azure Data Factory fabryka danych może mieć jeden lub więcej potoków danych. Potok danych ma co najmniej jedno działania. Istnieją dwa rodzaje działań:

* [Działania związane z przenoszeniem danych](../data-factory/copy-activity-overview.md). Działania związane z przenoszeniem danych umożliwiają przenoszenie danych ze źródłowego magazynu danych do docelowego magazynu danych.
* [Działania związane z transformacją danych](../data-factory/transform-data.md). Działania przekształcania danych są używane do przekształcania/przetwarzania danych. Działanie hive hdinsight jest jednym z działań transformacji obsługiwanych przez fabrykę danych. Użyj działania transformacji gałęzi w tym samouczku.

W tym artykule skonfigurujesz działanie Hive, aby utworzyć klaster programu HDInsight na żądanie. Gdy działanie jest uruchamiane w celu przetworzenia danych, oto, co się dzieje:

1. Klaster HDInsight Hadoop jest tworzony automatycznie, aby przetworzyć plasterek tylko w czasie.

2. Dane wejściowe są przetwarzane przez uruchomienie skryptu HiveQL w klastrze. W tym samouczku skrypt HiveQL skojarzony z działaniem gałęzi wykonuje następujące akcje:

    * Używa istniejącej tabeli (*ulesampletable*) do utworzenia innej tabeli **HiveSampleOut**.
    * Wypełnia tabelę **HiveSampleOut** tylko określonymi kolumnami z oryginalnego *pliku hivesampletable*.

3. Klaster programu HDInsight Hadoop jest usuwany po zakończeniu przetwarzania, a klaster jest bezczynny dla skonfigurowanej ilości czasu (ustawienie timeToLive). Jeśli następny plasterek danych jest dostępny do przetwarzania w tym czasieToLive czas bezczynny, ten sam klaster jest używany do przetwarzania plasterka.  

## <a name="create-a-data-factory"></a>Tworzenie fabryki danych

1. Zaloguj się do [Portalu Azure](https://portal.azure.com/).

2. W menu po lewej stronie przejdź do **+ Tworzenie zasobu** > **Analytics** > **Data Factory**.

    ![Usługa Azure Data Factory w portalu](./media/hdinsight-hadoop-create-linux-clusters-adf/data-factory-azure-portal.png "Usługa Azure Data Factory w portalu")

3. Wprowadź lub wybierz następujące wartości dla kafelka **Nowa fabryka danych:**

    |Właściwość  |Wartość  |
    |---------|---------|
    |Nazwa | Wprowadź nazwę fabryki danych. Ta nazwa musi być unikatowa w skali globalnej.|
    |Wersja | Wyjdź na **V2**. |
    |Subskrypcja | Wybierz swoją subskrypcję platformy Azure. |
    |Grupa zasobów | Wybierz grupę zasobów utworzoną za pomocą skryptu programu PowerShell. |
    |Lokalizacja | Lokalizacja jest automatycznie ustawiana na lokalizację określoną podczas wcześniejszego tworzenia grupy zasobów. W tym samouczku lokalizacja jest ustawiona na **wschodnie stany USA**. |
    |Włącz GIT|Odznacz to pole.|

    ![Tworzenie usługi Azure Data Factory przy użyciu portalu Azure](./media/hdinsight-hadoop-create-linux-clusters-adf/azure-portal-create-data-factory.png "Tworzenie usługi Azure Data Factory przy użyciu portalu Azure")

4. Wybierz **pozycję Utwórz**. Tworzenie fabryki danych może potrwać od 2 do 4 minut.

5. Po utworzeniu fabryki danych otrzymasz powiadomienie **o pomyślnym wdrożeniu** za pomocą przycisku **Przejdź do zasobu.**  Wybierz **pozycję Przejdź do zasobu,** aby otworzyć widok domyślny fabryki danych.

6. Wybierz **author & Monitor,** aby uruchomić portal tworzenia i monitorowania usługi Azure Data Factory.

    ![Omówienie portalu usługi Azure Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/data-factory-portal-overview.png "Omówienie usługi Azure Data Factory")

## <a name="create-linked-services"></a>Tworzenie połączonych usług

W tej sekcji można tworzyć dwie połączone usługi w fabryce danych.

* **Usługa połączona usługi Azure Storage,** która łączy konto magazynu platformy Azure z fabryką danych. Ten magazyn jest używany przez klaster usługi HDInsight na żądanie. Zawiera również skrypt hive, który jest uruchamiany w klastrze.
* **Usługa połączona z HDInsight na żądanie**. Usługa Azure Data Factory automatycznie tworzy klaster HDInsight i uruchamia skrypt hive. Następnie usuwa klaster usługi HDInsight, gdy jest on bezczynny przez wstępnie skonfigurowany czas.

### <a name="create-an-azure-storage-linked-service"></a>Tworzenie połączonej usługi Azure Storage

1. Z lewego okienka strony **Rozpocznijmy pracę** wybierz ikonę **Autor.**

    ![Tworzenie połączonej usługi usługi Azure Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/data-factory-edit-tab.png "Tworzenie połączonej usługi usługi Azure Data Factory")

2. Wybierz **pozycję Połączenia** z lewego dolnego rogu okna, a następnie wybierz pozycję **+Nowy**.

    ![Tworzenie połączeń w fabryce danych platformy Azure](./media/hdinsight-hadoop-create-linux-clusters-adf/data-factory-create-new-connection.png "Tworzenie połączeń w fabryce danych platformy Azure")

3. W oknie dialogowym **Nowa usługa połączona** wybierz pozycję **Usługa Azure Blob Storage,** a następnie wybierz pozycję **Kontynuuj**.

    ![Tworzenie połączonej usługi usługi Azure Storage dla fabryki danych](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-storage-linked-service.png "Tworzenie połączonej usługi usługi Azure Storage dla fabryki danych")

4. Podaj następujące wartości dla połączonej usługi magazynu:

    |Właściwość |Wartość |
    |---|---|
    |Nazwa |Wprowadź polecenie `HDIStorageLinkedService`.|
    |Subskrypcja platformy Azure |Wybierz subskrypcję z listy rozwijanej.|
    |Nazwa konta magazynu |Wybierz konto usługi Azure Storage utworzone jako część skryptu programu PowerShell.|

    Wybierz **opcję Testuj połączenie,** a jeśli zakończy się pomyślnie, wybierz pozycję **Utwórz**.

    ![Podaj nazwę usługi połączonej usługi Azure Storage](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-storage-linked-service-details.png "Podaj nazwę usługi połączonej usługi Azure Storage")

### <a name="create-an-on-demand-hdinsight-linked-service"></a>Tworzenie połączonej usługi HDInsight na żądanie

1. Wybierz ponownie przycisk **+ Nowy**, aby utworzyć kolejną połączoną usługę.

2. W oknie **Nowa usługa połączona** wybierz kartę **Obliczanie.**

3. Wybierz **pozycję Azure HDInsight**, a następnie wybierz pozycję **Kontynuuj**.

    ![Tworzenie połączonej usługi HDInsight dla usługi Azure Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-linked-service.png "Tworzenie połączonej usługi HDInsight dla usługi Azure Data Factory")

4. W oknie **Nowa usługa połączona** wprowadź następujące wartości, a pozostałe domyślnie pozostaw:

    | Właściwość | Wartość |
    | --- | --- |
    | Nazwa | Wprowadź polecenie `HDInsightLinkedService`.|
    | Typ | Wybierz **opcję HdInsight na żądanie**. |
    | Połączona usługa Azure Storage | Wybierz pozycję `HDIStorageLinkedService`. |
    | Typ klastra | Wybierz **hadoop** |
    | Czas wygaśnięcia | Podaj czas trwania, dla którego ma być dostępny klaster HDInsight przed automatycznym usunięciem.|
    | Identyfikator jednostki usługi | Podaj identyfikator aplikacji jednostki usługi Azure Active Directory utworzonej w ramach wymagań wstępnych. |
    | Klucz główny usługi | Podaj klucz uwierzytelniania dla jednostki usługi Azure Active Directory. |
    | Prefiks nazwy klastra | Podaj wartość, która będzie poprzedzony do wszystkich typów klastra, które są tworzone przez fabrykę danych. |
    |Subskrypcja |Wybierz subskrypcję z listy rozwijanej.|
    | Wybieranie grupy zasobów | Wybierz grupę zasobów utworzoną jako część skryptu programu PowerShell, który był wcześniej używany.|
    | Typ systemu operacyjnego/nazwa użytkownika SSH klastra | Często należy wprowadzić nazwę użytkownika `sshuser`SSH. |
    | Typ systemu operacyjnego/hasło SSH klastra | Podaj hasło dla użytkownika SSH |
    | Typ systemu operacyjnego/nazwa użytkownika klastra | Często wprowadzaj nazwę użytkownika `admin`klastra . |
    | Typ systemu operacyjnego/hasło klastra | Podaj hasło dla użytkownika klastra. |

    Następnie wybierz pozycję **Utwórz**.

    ![Podaj wartości połączonej usługi HDInsight](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-linked-service-details.png "Podaj wartości połączonej usługi HDInsight")

## <a name="create-a-pipeline"></a>Tworzenie potoku

1. Wybierz **+** przycisk (plus), a następnie wybierz pozycję **Potok**.

    ![Tworzenie potoku w usłudze Azure Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-create-pipeline.png "Tworzenie potoku w usłudze Azure Data Factory")

1. W **przyborniku Działania** rozwiń pozycję **HDInsight**i przeciągnij działanie **Gałąź** na powierzchnię projektanta potoku. Na karcie **Ogólne** podaj nazwę działania.

    ![Dodawanie działań do potoku data factory](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-add-hive-pipeline.png "Dodawanie działań do potoku data factory")

1. Upewnij się, że wybrano aktywność Gałąź, wybierz kartę **Klaster HDI,** a z listy rozwijanej **Usługi połączonej HDInsight** wybierz utworzoną wcześniej usługę, **HDInsightLinkedService**dla usługi HDInsight.

    ![Podaj szczegóły klastra HDInsight dla potoku](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-hive-activity-select-hdinsight-linked-service.png "Podaj szczegóły klastra HDInsight dla potoku")

1. Wybierz kartę **Skrypt** i wykonaj następujące czynności:

    1. W przypadku **usługi połączonej ze skryptami**wybierz **HDIStorageLinkedService** z listy rozwijanej. Ta wartość jest usługą połączony magazyn utworzoną wcześniej.

    1. W polu **Ścieżka pliku**wybierz pozycję **Przeglądaj magazyn** i przejdź do lokalizacji, w której dostępny jest przykładowy skrypt gałęzi. Jeśli skrypt programu PowerShell został uruchomiony `adfgetstarted/hivescripts/partitionweblogs.hql`wcześniej, ta lokalizacja powinna być .

        ![Podaj szczegóły skryptu gałęzi dla potoku](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-provide-script-path.png "Podaj szczegóły skryptu gałęzi dla potoku")

    1. W obszarze**Parametry** **zaawansowane** > wybierz opcję **Automatyczne wypełnianie ze skryptu**. Ta opcja wyszukuje wszystkie parametry w skrypcie hive, które wymagają wartości w czasie wykonywania.

    1. W polu tekstowym **wartości** dodaj istniejący `wasbs://adfgetstarted@<StorageAccount>.blob.core.windows.net/outputfolder/`folder w formacie . W ścieżce jest rozróżniana wielkość liter. Jest to ścieżka, w której będą przechowywane dane wyjściowe skryptu. Schemat `wasbs` jest konieczne, ponieważ konta magazynu mają teraz bezpieczne transfer wymagane włączone domyślnie.

        ![Podaj parametry skryptu Hive](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-provide-script-parameters.png "Podaj parametry skryptu Hive")

1. Wybierz **sprawdź poprawność,** aby sprawdzić poprawność potoku. Wybierz **>>** przycisk (strzałka w prawo), aby zamknąć okno sprawdzania poprawności.

    ![Sprawdzanie poprawności potoku usługi Azure Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-validate-all.png "Sprawdzanie poprawności potoku usługi Azure Data Factory")

1. Na koniec wybierz **pozycję Opublikuj wszystko,** aby opublikować artefakty w usłudze Azure Data Factory.

    ![Publikowanie potoku usługi Azure Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-publish-pipeline.png "Publikowanie potoku usługi Azure Data Factory")

## <a name="trigger-a-pipeline"></a>Wyzwalanie potoku

1. Na pasku narzędzi na powierzchni projektanta wybierz pozycję **Dodaj wyzwalacz wyzwalacza** > **teraz**.

    ![Wyzwalanie potoku usługi Azure Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-trigger-pipeline.png "Wyzwalanie potoku usługi Azure Data Factory")

2. Wybierz **przycisk OK** na pasku bocznym wyskakującym okienku.

## <a name="monitor-a-pipeline"></a>Monitorowanie potoku

1. Przejdź do karty **Monitorowanie** po lewej stronie. Uruchomienie potoku zostanie wyświetlone na liście **Uruchomienia potoku**. Zwróć uwagę na stan uruchomienia w kolumnie **Stan.**

    ![Monitorowanie potoku usługi Azure Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-monitor-pipeline.png "Monitorowanie potoku usługi Azure Data Factory")

1. Wybierz pozycję **Odśwież**, aby odświeżyć stan.

1. Można również wybrać ikonę **Wyświetl przebiegi działania,** aby wyświetlić przebieg działania skojarzony z potokiem. Na poniższym zrzucie ekranu zobaczysz tylko jedno działanie uruchomione, ponieważ w potoku, który został utworzony, jest tylko jedno działanie. Aby przełączyć się z powrotem do poprzedniego widoku, wybierz **pipelines** w kierunku górnej części strony.

    ![Monitorowanie działania potoku usługi Azure Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-monitor-pipeline-activity.png "Monitorowanie działania potoku usługi Azure Data Factory")

## <a name="verify-the-output"></a>Sprawdzanie danych wyjściowych

1. Aby zweryfikować dane wyjściowe, w witrynie Azure Portal przejdź do konta magazynu, które zostały użyte w tym samouczku. Powinny zostać wyświetlonych następujące foldery lub kontenery:

    * Zobaczysz **adfgerstarted/outputfolder,** który zawiera dane wyjściowe skryptu hive, który został uruchomiony jako część potoku.

    * Zobaczysz **adfhdidatafactory-\<linked-service-name>-\<timestamp>** container. Ten kontener jest domyślną lokalizacją magazynu klastra HDInsight, który został utworzony jako część uruchomienia potoku.

    * Zobaczysz kontener **adfjobs,** który ma dzienniki zadań usługi Azure Data Factory.  

        ![Weryfikowanie danych wyjściowych potoku usługi Azure Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-verify-output.png "Weryfikowanie danych wyjściowych potoku usługi Azure Data Factory")

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

W tworzeniu klastra HDInsight na żądanie nie trzeba jawnie usuwać klastra HDInsight. Klaster jest usuwany na podstawie konfiguracji podanej podczas tworzenia potoku. Jednak nawet po usunięciu klastra konta magazynu skojarzone z klastrem nadal istnieją. To zachowanie jest zgodnie z projektem, dzięki czemu można zachować dane w stanie nienaruszonym. Jeśli jednak nie chcesz utrwalić danych, możesz usunąć utworzone konto magazynu.

Alternatywnie można usunąć całą grupę zasobów utworzoną dla tego samouczka. Spowoduje to usunięcie konta magazynu i utworzonej usługi Azure Data Factory.

### <a name="delete-the-resource-group"></a>Usuwanie grupy zasobów

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Wybierz **pozycję Grupy zasobów** w lewym okienku.
1. Wybierz nazwę grupy zasobów utworzoną w skrypcie programu PowerShell. Użyj filtru, jeśli na liście znajduje się zbyt wiele grup zasobów. Otwiera grupę zasobów.
1. Na kafelku **Zasoby** masz domyślne konto magazynu i fabrykę danych na liście, chyba że grupa zasobów zostanie udostępniona innym projektom.
1. Wybierz pozycję **Usuń grupę zasobów**. Spowoduje to usunięcie konta magazynu i danych przechowywanych na koncie magazynu.

    ![Grupa zasobów usuwania witryny Azure portal](./media/hdinsight-hadoop-create-linux-clusters-adf/delete-resource-group.png "Usuwanie grupy zasobów")

1. Wprowadź nazwę grupy zasobów, aby potwierdzić usunięcie, a następnie wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

W tym artykule dowiesz się, jak używać usługi Azure Data Factory do tworzenia klastra HDInsight na żądanie i uruchamiania zadań [hive Apache.](https://hive.apache.org/) Przejdź do następnego artykułu, aby dowiedzieć się, jak tworzyć klastry HDInsight z konfiguracją niestandardową.

> [!div class="nextstepaction"]
> [Tworzenie klastrów usługi Azure HDInsight przy niestandardowych konfiguracjach](hdinsight-hadoop-provision-linux-clusters.md)
