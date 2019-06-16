---
title: Ograniczanie dostępu przy użyciu sygnatury dostępu współdzielonego — Azure HDInsight
description: Dowiedz się, jak ograniczyć HDInsight dostęp do danych przechowywanych w obiektach blob usługi Azure storage za pomocą sygnatur dostępu współdzielonego.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/29/2019
ms.author: hrasheed
ms.openlocfilehash: 7f7f6fe31afe35d9ccfd6ee33617bd7e4fbe46b7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65409563"
---
# <a name="use-azure-storage-shared-access-signatures-to-restrict-access-to-data-in-hdinsight"></a>Ograniczania dostępu do danych w HDInsight za pomocą sygnatur dostępu współdzielonego magazynu platformy Azure

HDInsight ma pełny dostęp do danych w ramach kont usługi Azure Storage skojarzonym z klastrem. Sygnatur dostępu współdzielonego dla kontenera obiektów blob umożliwia ograniczanie dostępu do danych. Dostęp do sygnatur Współdzielonego są funkcji konta usługi Azure storage, która pozwala na ograniczanie dostępu do danych. Na przykład zapewniając dostęp do danych tylko do odczytu.

> [!IMPORTANT]  
> Rozwiązanie przy użyciu struktury Apache Ranger należy rozważyć użycie HDInsight przyłączone do domeny. Aby uzyskać więcej informacji, zobacz [HDInsight przyłączone do domeny skonfiguruj](./domain-joined/apache-domain-joined-configure.md) dokumentu.

> [!WARNING]  
> HDInsight musi mieć pełny dostęp do domyślnego magazynu klastra.

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure.

* Klient SSH. Aby uzyskać więcej informacji, zobacz [Łączenie się z usługą HDInsight (Apache Hadoop) przy użyciu protokołu SSH](./hdinsight-hadoop-linux-use-ssh-unix.md).

* Istniejące [kontenera magazynu](../storage/blobs/storage-quickstart-blobs-portal.md).  

* Jeśli przy użyciu programu PowerShell, konieczne będzie [modułu Az](https://docs.microsoft.com/powershell/azure/overview).

* Jeśli chcą używać wiersza polecenia platformy Azure i nie został jeszcze zainstalowany go, zobacz [zainstalować interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

* Jeśli przy użyciu [Python](https://www.python.org/downloads/), w wersji 2.7 lub nowszej.

* Jeśli przy użyciu C#, Visual Studio musi być w wersji 2013 lub nowszy.

* [Schemat identyfikatora URI](./hdinsight-hadoop-linux-information.md#URI-and-scheme) konta magazynu. Takie rozwiązanie byłoby `wasb://` dla usługi Azure Storage `abfs://` dla usługi Azure Data Lake Storage Gen2 lub `adl://` dla usługi Azure Data Lake Storage Gen1. Bezpieczny transfer jest włączona dla usługi Azure Storage lub Data Lake Storage Gen2, identyfikator URI będzie mieć `wasbs://` lub `abfss://`odpowiednio Zobacz też [bezpieczny transfer](../storage/common/storage-require-secure-transfer.md).

* Istniejący klaster HDInsight do dodania do podpisu dostępu współdzielonego. Jeśli nie użyjesz programu Azure PowerShell do utworzenia klastra i Dodaj sygnaturę dostępu współdzielonego podczas tworzenia klastra.

* Przykład plików ze [ https://github.com/Azure-Samples/hdinsight-dotnet-python-azure-storage-shared-access-signature ](https://github.com/Azure-Samples/hdinsight-dotnet-python-azure-storage-shared-access-signature). To repozytorium zawiera następujące elementy:

  * Projekt programu Visual Studio, który można utworzyć kontenera magazynu, zapisane zasady i interfejsie SAS do użycia z usługą HDInsight
  * Skrypt w języku Python, który można utworzyć kontenera magazynu, zapisane zasady i interfejsie SAS do użycia z usługą HDInsight
  * Skrypt programu PowerShell, który można utworzyć klaster HDInsight i skonfigurować go do korzystania z sygnatury dostępu Współdzielonego. Zaktualizowana wersja jest używana poniżej.
  * Przykładowy plik: `hdinsight-dotnet-python-azure-storage-shared-access-signature-master\sampledata\sample.log`

## <a name="shared-access-signatures"></a>Sygnatury dostępu współdzielonego

Istnieją dwa rodzaje sygnatur dostępu współdzielonego:

* Ad hoc: Czas rozpoczęcia, czas wygaśnięcia i uprawnienia dla sygnatury dostępu Współdzielonego są określone w identyfikatorze URI sygnatury dostępu Współdzielonego.

* Przechowywanych zasad dostępu: Przechowywanych zasad dostępu jest zdefiniowany w kontenerze, zasobów, takich jak kontener obiektów blob. Zasady może służyć do zarządzania ograniczeniami dla jednego lub więcej sygnatur dostępu współdzielonego. Jeśli sygnatury dostępu Współdzielonego jest kojarzony z przechowywanych zasad dostępu, sygnatury dostępu Współdzielonego dziedziczy ograniczenia — godzina rozpoczęcia, czas wygaśnięcia i uprawnienia — zdefiniowane dla przechowywanych zasad dostępu.

Różnica między dwoma formami jest ważna dla jednego kluczowy scenariusz: odwołania. Sygnatury dostępu Współdzielonego jest adresem URL, dzięki czemu każdy, kto uzyskuje sygnaturę dostępu Współdzielonego, używać niezależnie od tego, kto zażądano rozpoczynać się. Sygnatury dostępu Współdzielonego zostanie opublikowany publicznie, może służyć przez dowolną osobę na całym świecie. Sygnatury dostępu Współdzielonego, który jest rozpowszechniany jest prawidłowy, dopóki jedna z następujących czterech zdarzeń sytuacji:

1. Czas wygaśnięcia, określone w sygnatury dostępu Współdzielonego zostanie osiągnięty.

2. Czas wygaśnięcia, określone w zasadach dostępu składowana odwołuje się sygnatury dostępu Współdzielonego zostanie osiągnięty. Następujące scenariusze spowodować, że czas wygaśnięcia osiągnąć:

    * Przedział czasu upłynął.
    * Zasady dostępu przechowywane zostanie zmodyfikowany na potrzeby ma czas wygaśnięcia w przeszłości. Zmiana czas wygaśnięcia jest jednym ze sposobów, aby można było odwołać sygnatury dostępu Współdzielonego.

3. Usunąć przechowywanych zasad dostępu odwołuje się sygnatury dostępu Współdzielonego, który jest inny sposób, aby można było odwołać sygnatury dostępu Współdzielonego. Jeśli ponownie przechowywane zasady dostępu o takiej samej nazwie, wszystkie tokeny sygnatur dostępu Współdzielonego dla poprzednich zasad są prawidłowe, (Jeśli nie minął czas wygaśnięcia na sygnatury dostępu Współdzielonego). Zamierzasz odwołać sygnatury dostępu Współdzielonego, należy użyć innej nazwy, jeśli ponownie zasady dostępu z czasem wygaśnięcia w przyszłości.

4. Klucz konta, który został użyty do utworzenia sygnatury dostępu Współdzielonego zostanie ponownie wygenerowany. Trwa ponowne generowanie klucza powoduje, że wszystkie aplikacje, które używają poprzedniej klucz niepowodzenia uwierzytelniania. Zaktualizuj wszystkie składniki do nowego klucza.

> [!IMPORTANT]  
> Identyfikator URI sygnatury dostępu współdzielonego jest skojarzony z kluczem konta używane do tworzenia podpisu, a skojarzone przechowywane zasady dostępu (jeśli istnieje). Jeśli nie przechowywanych zasad dostępu jest określona, jedynym sposobem, aby można było odwołać sygnatury dostępu współdzielonego jest należy zmienić wartość klucza konta.

Firma Microsoft zaleca, zawsze używaj przechowywanych zasad dostępu. Korzystając z zasady przechowywane, możesz odwołać podpisów lub przedłużyć datę wygaśnięcia, zgodnie z potrzebami. Kroki opisane w tym dokumencie używane przechowywane zasady dostępu do generowania sygnatury dostępu Współdzielonego.

Aby uzyskać więcej informacji na temat sygnatur dostępu współdzielonego, zobacz [opis modelu sygnatur dostępu Współdzielonego](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

## <a name="create-a-stored-policy-and-sas"></a>Tworzenie sygnatury dostępu Współdzielonego i przechowywanych zasad

Zapisz token sygnatury dostępu Współdzielonego, który jest generowany na końcu każdej metody. Token będzie wyglądać podobnie do następującego:

```output
?sv=2018-03-28&sr=c&si=myPolicyPS&sig=NAxefF%2BrR2ubjZtyUtuAvLQgt%2FJIN5aHJMj6OsDwyy4%3D
```

### <a name="using-powershell"></a>Korzystanie z programu PowerShell

Zastąp `RESOURCEGROUP`, `STORAGEACCOUNT`, i `STORAGECONTAINER` odpowiednimi wartościami dla istniejącego kontenera magazynu. Zmień katalog na `hdinsight-dotnet-python-azure-storage-shared-access-signature-master` lub poprawić `-File` parametr w celu uwzględnienia ścieżki bezwzględnej do `Set-AzStorageblobcontent`. Wprowadź następujące polecenie programu PowerShell:

```PowerShell
$resourceGroupName = "RESOURCEGROUP"
$storageAccountName = "STORAGEACCOUNT"
$containerName = "STORAGECONTAINER"
$policy = "myPolicyPS"

# Login to your Azure subscription
$sub = Get-AzSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Connect-AzAccount
}

# If you have multiple subscriptions, set the one to use
# Select-AzSubscription -SubscriptionId "<SUBSCRIPTIONID>"

# Get the access key for the Azure Storage account
$storageAccountKey = (Get-AzStorageAccountKey `
                                -ResourceGroupName $resourceGroupName `
                                -Name $storageAccountName)[0].Value

# Create an Azure Storage context
$storageContext = New-AzStorageContext `
                                -StorageAccountName $storageAccountName `
                                -StorageAccountKey $storageAccountKey

# Create a stored access policy for the Azure storage container
New-AzStorageContainerStoredAccessPolicy `
   -Container $containerName `
   -Policy $policy `
   -Permission "rl" `
   -ExpiryTime "12/31/2025 08:00:00" `
   -Context $storageContext

# Get the stored access policy or policies for the Azure storage container
Get-AzStorageContainerStoredAccessPolicy `
    -Container $containerName `
    -Context $storageContext

# Generates an SAS token for the Azure storage container
New-AzStorageContainerSASToken `
    -Name $containerName `
    -Policy $policy `
    -Context $storageContext

<# Removes a stored access policy from the Azure storage container
Remove-AzStorageContainerStoredAccessPolicy `
    -Container $containerName `
    -Policy $policy `
    -Context $storageContext
#>

# upload a file for a later example
Set-AzStorageblobcontent `
    -File "./sampledata/sample.log" `
    -Container $containerName `
    -Blob "samplePS.log" `
    -Context $storageContext
```

### <a name="using-azure-cli"></a>Korzystanie z interfejsu wiersza polecenia platformy Azure

Używanie zmiennych w tej sekcji jest oparty na środowisku Windows. Niewielkie zmiany będą potrzebne dla powłoki bash lub innych środowiskach.

1. Zastąp `STORAGEACCOUNT`, i `STORAGECONTAINER` odpowiednimi wartościami dla istniejącego kontenera magazynu.

    ```azurecli
    # set variables
    set AZURE_STORAGE_ACCOUNT=STORAGEACCOUNT
    set AZURE_STORAGE_CONTAINER=STORAGECONTAINER

    #Login
    az login

    # If you have multiple subscriptions, set the one to use
    # az account set --subscription SUBSCRIPTION

    # Retrieve the primary key for the storage account
    az storage account keys list --account-name %AZURE_STORAGE_ACCOUNT% --query "[0].{PrimaryKey:value}" --output table
    ```

2. Ustaw pobrane klucz podstawowy do zmiennej w celu późniejszego użycia. Zastąp `PRIMARYKEY` z pobraną wartość w poprzednim kroku, a następnie wprowadź poniższe polecenie:

    ```azurecli
    #set variable for primary key
    set AZURE_STORAGE_KEY=PRIMARYKEY
    ```

3. Zmień katalog na `hdinsight-dotnet-python-azure-storage-shared-access-signature-master` lub poprawić `--file` parametr w celu uwzględnienia ścieżki bezwzględnej do `az storage blob upload`. Wykonaj pozostałe polecenia:

    ```azurecli
    # Create stored access policy on the containing object
    az storage container policy create --container-name %AZURE_STORAGE_CONTAINER% --name myPolicyCLI --account-key %AZURE_STORAGE_KEY% --account-name %AZURE_STORAGE_ACCOUNT% --expiry 2025-12-31 --permissions rl

    # List stored access policies on a containing object
    az storage container policy list --container-name %AZURE_STORAGE_CONTAINER% --account-key %AZURE_STORAGE_KEY% --account-name %AZURE_STORAGE_ACCOUNT%

    # Generate a shared access signature for the container
    az storage container generate-sas --name myPolicyCLI --account-key %AZURE_STORAGE_KEY% --account-name %AZURE_STORAGE_ACCOUNT%

    # Reversal
    # az storage container policy delete --container-name %AZURE_STORAGE_CONTAINER% --name myPolicyCLI --account-key %AZURE_STORAGE_KEY% --account-name %AZURE_STORAGE_ACCOUNT%

    # upload a file for a later example
    az storage blob upload --container-name %AZURE_STORAGE_CONTAINER% --account-key %AZURE_STORAGE_KEY% --account-name %AZURE_STORAGE_ACCOUNT% --name sampleCLI.log --file "./sampledata/sample.log"
    ```

### <a name="using-python"></a>Korzystanie z języka Python

Otwórz `SASToken.py` i Zastęp `storage_account_name`, `storage_account_key`, i `storage_container_name` odpowiednimi wartościami dla istniejącego kontenera magazynu, a następnie uruchom skrypt.

Może być konieczne wykonanie `pip install --upgrade azure-storage` Jeśli zostanie wyświetlony komunikat o błędzie `ImportError: No module named azure.storage`.

### <a name="using-c"></a>Przy użyciu języka C#

1. Otwórz rozwiązanie w programie Visual Studio.

2. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy **SASExample** projektu, a następnie wybierz **właściwości**.

3. Wybierz **ustawienia** i Dodaj wartości dla następujących pozycji:

   * StorageConnectionString: Parametry połączenia dla konta magazynu, który chcesz utworzyć zasady przechowywane i sygnatury dostępu Współdzielonego dla. Powinna być w formacie `DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey` gdzie `myaccount` jest nazwą konta magazynu i `mykey` jest kluczem do konta magazynu.

   * ContainerName: Kontener na koncie magazynu, którą chcesz ograniczyć dostęp do.

   * SASPolicyName: Nazwa używać przechowywanych zasad w celu utworzenia.

   * FileToUpload: Ścieżka do pliku, który zostanie przekazany do kontenera.

4. Uruchom projekt. Zapisz zasady tokenu sygnatury dostępu Współdzielonego, nazwa konta magazynu i nazwa kontenera. Te wartości są używane podczas kojarzenia konta magazynu z klastrem usługi HDInsight.

## <a name="use-the-sas-with-hdinsight"></a>Sygnatura dostępu Współdzielonego za pomocą HDInsight

Podczas tworzenia klastra usługi HDInsight, należy określić konto magazynu podstawowego i opcjonalnie można określić dodatkowe konta magazynu. Obie te metody dodawania magazynu wymagają pełnego dostępu do konta magazynu i kontenerów, które są używane.

Aby użyć sygnaturę dostępu współdzielonego, aby ograniczyć dostęp do kontenera, Dodaj niestandardowy wpis, aby **lokacji podstawowej** konfigurację klastra. Podczas tworzenia klastra przy użyciu programu PowerShell lub po utworzeniu klastra przy użyciu narzędzia Ambari, możesz dodać wpis.

### <a name="create-a-cluster-that-uses-the-sas"></a>Tworzenie klastra, który korzysta z sygnatury dostępu Współdzielonego

Zastąp `CLUSTERNAME`, `RESOURCEGROUP`, `DEFAULTSTORAGEACCOUNT`, `STORAGECONTAINER`, `STORAGEACCOUNT`, i `TOKEN` odpowiednimi wartościami. Wprowadź poleceń programu PowerShell:

```powershell

$clusterName = 'CLUSTERNAME'
$resourceGroupName = 'RESOURCEGROUP'

# Replace with the Azure data center you want to the cluster to live in
$location = 'eastus'

# Replace with the name of the default storage account TO BE CREATED
$defaultStorageAccountName = 'DEFAULTSTORAGEACCOUNT'

# Replace with the name of the SAS container CREATED EARLIER
$SASContainerName = 'STORAGECONTAINER'

# Replace with the name of the SAS storage account CREATED EARLIER
$SASStorageAccountName = 'STORAGEACCOUNT'

# Replace with the SAS token generated earlier
$SASToken = 'TOKEN'

# Default cluster size (# of worker nodes), version, and type
$clusterSizeInNodes = "4"
$clusterVersion = "3.6"
$clusterType = "Hadoop"

# Login to your Azure subscription
$sub = Get-AzSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Connect-AzAccount
}

# If you have multiple subscriptions, set the one to use
# Select-AzSubscription -SubscriptionId "<SUBSCRIPTIONID>"

# Create an Azure Storage account and container
New-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $defaultStorageAccountName `
    -Location $location `
    -SkuName Standard_LRS `
    -Kind StorageV2 `
    -EnableHttpsTrafficOnly 1

$defaultStorageAccountKey = (Get-AzStorageAccountKey `
                                -ResourceGroupName $resourceGroupName `
                                -Name $defaultStorageAccountName)[0].Value

$defaultStorageContext = New-AzStorageContext `
                                -StorageAccountName $defaultStorageAccountName `
                                -StorageAccountKey $defaultStorageAccountKey


# Create a blob container. This holds the default data store for the cluster.
New-AzStorageContainer `
    -Name $clusterName `
    -Context $defaultStorageContext 

# Cluster login is used to secure HTTPS services hosted on the cluster
$httpCredential = Get-Credential `
    -Message "Enter Cluster login credentials" `
    -UserName "admin"

# SSH user is used to remotely connect to the cluster using SSH clients
$sshCredential = Get-Credential `
    -Message "Enter SSH user credentials" `
    -UserName "sshuser"

# Create the configuration for the cluster
$config = New-AzHDInsightClusterConfig 

$config = $config | Add-AzHDInsightConfigValues `
    -Spark2Defaults @{} `
    -Core @{"fs.azure.sas.$SASContainerName.$SASStorageAccountName.blob.core.windows.net"=$SASToken}

# Create the HDInsight cluster
New-AzHDInsightCluster `
    -Config $config `
    -ResourceGroupName $resourceGroupName `
    -ClusterName $clusterName `
    -Location $location `
    -ClusterSizeInNodes $clusterSizeInNodes `
    -ClusterType $clusterType `
    -OSType Linux `
    -Version $clusterVersion `
    -HttpCredential $httpCredential `
    -SshCredential $sshCredential `
    -DefaultStorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
    -DefaultStorageAccountKey $defaultStorageAccountKey `
    -DefaultStorageContainer $clusterName

<# REVERSAL
Remove-AzHDInsightCluster `
    -ResourceGroupName $resourceGroupName `
    -ClusterName $clusterName

Remove-AzStorageContainer `
    -Name $clusterName `
    -Context $defaultStorageContext

Remove-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $defaultStorageAccountName

Remove-AzResourceGroup `
    -Name $resourceGroupName
#>
```

> [!IMPORTANT]  
> Po wyświetleniu monitu o HTTP/HTTPS lub nazwa użytkownika SSH i hasło, należy podać hasło, które spełnia następujące kryteria:
>
> * Musi być co najmniej 10 znaków.
> * Musi zawierać co najmniej jedną cyfrę.
> * Musi zawierać co najmniej jeden znak inny niż alfanumeryczny.
> * Musi zawierać co najmniej jedną wielką lub małą literę.

Zajmuje trochę czasu ten skrypt, aby ukończyć, zwykle około 15 minut. Po zakończeniu działania skryptu bez żadnych błędów został utworzony klaster.

### <a name="use-the-sas-with-an-existing-cluster"></a>Sygnatura dostępu Współdzielonego za pomocą istniejącego klastra

Jeśli masz istniejący klaster, można dodać na sygnatur dostępu Współdzielonego **lokacji podstawowej** konfiguracji wykonując następujące kroki:

1. Otwórz interfejs webowy Ambari dla klastra. Adres na tej stronie jest `https://YOURCLUSTERNAME.azurehdinsight.net`. Po wyświetleniu monitu uwierzytelniania w klastrze przy użyciu nazwy administratora (Administrator) i hasło używane podczas tworzenia klastra.

2. Z lewej strony interfejsu użytkownika sieci web Ambari, wybierz **HDFS** , a następnie wybierz **Configs** kartę pośrodku strony.

3. Wybierz **zaawansowane** kartę, a następnie przewiń do momentu znalezienia **lokacji podstawowej niestandardowe** sekcji.

4. Rozwiń **lokacji podstawowej niestandardowe** sekcji, a następnie przewiń do zakończenia, a następnie wybierz pozycję **Dodaj właściwość...**  łącza. Użyj następujących wartości dla **klucz** i **wartość** pola:

   * **Klucz**: `fs.azure.sas.CONTAINERNAME.STORAGEACCOUNTNAME.blob.core.windows.net`
   * **Wartość**: Sygnatura dostępu Współdzielonego zwrócony przez jedną z metod wykonane wcześniej.

     Zastąp `CONTAINERNAME` z kontenerem nazwę, jak używać z C# lub aplikacji sygnatury dostępu Współdzielonego. Zastąp `STORAGEACCOUNTNAME` nazwą konta magazynu została użyta.

5. Kliknij przycisk **Dodaj** przycisk, aby zapisać ten klucz i wartość, a następnie kliknij przycisk **Zapisz** przycisk, aby zapisać zmiany konfiguracji. Po wyświetleniu monitu, Dodaj opis zmiany ("Dodawanie dostępu do magazynu sygnatur dostępu Współdzielonego" na przykład), a następnie kliknij przycisk **Zapisz**.

    Kliknij przycisk **OK** kiedy zmiany zostały ukończone.

   > [!IMPORTANT]  
   > Aby zmiany zaczęły obowiązywać, należy ponownie uruchomić kilka usług.

6. W sieci web Ambari interfejsu użytkownika, wybierz **systemu plików HDFS** z listy po lewej stronie, a następnie wybierz **ponowne uruchomienie wszystkich wpływ** z **działania usługi** listę rozwijaną listę po prawej stronie. Po wyświetleniu monitu wybierz __upewnij się, uruchom ponownie wszystkie__.

    Powtórz ten proces dla MapReduce2 i YARN.

7. Po ponownym uruchomieniu usługi, wybierz każdego z nich i Wyłącz tryb konserwacji z **działania usługi** listy rozwijanej.

## <a name="test-restricted-access"></a>Test z ograniczonym dostępem

Wykonaj następujące kroki, aby zweryfikować, możliwe jest tylko odczyt i listy elementów na koncie magazynu sygnatur dostępu Współdzielonego.

1. Łączenie z klastrem. Zastąp `CLUSTERNAME` o nazwie klastra i wprowadź następujące polecenie:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. Aby wyświetlić listę zawartości kontenera, użyj następującego polecenia w wierszu polecenia:

    ```bash
    hdfs dfs -ls wasbs://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/
    ```

    Zastąp `SASCONTAINER` o nazwie kontenera utworzone dla sygnatury dostępu Współdzielonego konta magazynu. Zastąp `SASACCOUNTNAME` nazwą konta magazynu używanego dla sygnatury dostępu Współdzielonego.

    Lista zawiera plik przekazane, gdy utworzono kontener i sygnatury dostępu Współdzielonego.

3. Użyj następującego polecenia, aby sprawdzić, czy możesz odczytać zawartość pliku. Zastąp `SASCONTAINER` i `SASACCOUNTNAME` jak w poprzednim kroku. Zastąp `sample.log` o nazwie pliku, wyświetlane w poprzednim poleceniu:

    ```bash
    hdfs dfs -text wasb://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/sample.log
    ```

    To polecenie wyświetla zawartość pliku.

4. Aby pobrać plik do lokalnego systemu plików, użyj następującego polecenia:

    ```bash
    hdfs dfs -get wasbs://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/sample.log testfile.txt
    ```

    To polecenie pobiera plik w lokalnym pliku o nazwie **testfile.txt**.

5. Użyj następującego polecenia do przekazania pliku lokalnego do nowego pliku o nazwie **testupload.txt** magazynu sygnatur dostępu Współdzielonego:

    ```bash
    hdfs dfs -put testfile.txt wasbs://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/testupload.txt
    ```

    Pojawi się komunikat podobny do następującego tekstu:

        put: java.io.IOException

    Ten błąd występuje, ponieważ lokalizacja magazynu jest odczytu + tylko z listy. Aby umieścić dane na domyślny magazyn dla klastra, który jest zapisywalny, użyj następującego polecenia:

    ```bash
    hdfs dfs -put testfile.txt wasbs:///testupload.txt
    ```

    Tym razem operacja powinna zakończyć się pomyślnie.

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się inne sposoby pracy z danymi w klastrze, skoro wiesz jak dodać ograniczony dostęp do magazynu w klastrze usługi HDInsight:

* [Use Apache Hive z HDInsight](hadoop/hdinsight-use-hive.md)
* [Apache Pig za pomocą HDInsight](hadoop/hdinsight-use-pig.md)
* [Używanie technologii MapReduce z HDInsight](hadoop/hdinsight-use-mapreduce.md)

