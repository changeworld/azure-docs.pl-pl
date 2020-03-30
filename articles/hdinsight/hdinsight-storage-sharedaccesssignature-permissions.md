---
title: Ograniczanie dostępu przy użyciu sygnatur dostępu współdzielonego — usługa Azure HDInsight
description: Dowiedz się, jak ograniczyć dostęp usługi HDInsight do danych przechowywanych w obiektach blob magazynu platformy Azure za pomocą sygnatur dostępu współdzielonego.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/13/2019
ms.openlocfilehash: 1a4ae0701174278203023c156a86aad8feb1ca4c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240621"
---
# <a name="use-azure-storage-shared-access-signatures-to-restrict-access-to-data-in-hdinsight"></a>Używanie sygnatur dostępu współdzielonego do usługi Azure Storage w celu ograniczenia dostępu do danych w usłudze HDInsight

Usługa HDInsight ma pełny dostęp do danych na kontach usługi Azure Storage skojarzonych z klastrem. Za pomocą podpisów dostępu współdzielonego w kontenerze obiektów blob można ograniczyć dostęp do danych. Sygnatury dostępu współdzielonego (SAS) to funkcja kont magazynu platformy Azure, która umożliwia ograniczenie dostępu do danych. Na przykład zapewnienie dostępu tylko do odczytu do danych.

> [!IMPORTANT]  
> Aby uzyskać rozwiązanie przy użyciu Apache Ranger, należy rozważyć użycie usługi HDInsight przyłączone do domeny. Aby uzyskać więcej informacji, zobacz konfigurowanie dokumentu [HDInsight przyłączony do domeny.](./domain-joined/apache-domain-joined-configure.md)

> [!WARNING]  
> Usługa HDInsight musi mieć pełny dostęp do domyślnego magazynu dla klastra.

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure.

* Klient SSH. Aby uzyskać więcej informacji, zobacz [Łączenie się z usługą HDInsight (Apache Hadoop) przy użyciu protokołu SSH](./hdinsight-hadoop-linux-use-ssh-unix.md).

* Istniejący [kontener magazynu](../storage/blobs/storage-quickstart-blobs-portal.md).  

* Jeśli korzystasz z programu PowerShell, potrzebny jest [moduł Az](https://docs.microsoft.com/powershell/azure/overview).

* Jeśli chcesz korzystać z interfejsu wiersza polecenia platformy Azure i nie zainstalowano go jeszcze, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

* W przypadku korzystania z [języka Python](https://www.python.org/downloads/)w wersji 2.7 lub nowszej.

* Jeśli używa się języka C#, visual studio musi być w wersji 2013 lub nowszej.

* [Schemat URI](./hdinsight-hadoop-linux-information.md#URI-and-scheme) dla twojego konta magazynu. Dotyczy to `wasb://` usługi Azure `abfs://` Storage, usługi Azure `adl://` Data Lake Storage Gen2 lub usługi Azure Data Lake Storage Gen1. Jeśli bezpieczny transfer jest włączony dla usługi Azure `wasbs://`Storage, identyfikator URI będzie . Zobacz też [bezpieczny transfer](../storage/common/storage-require-secure-transfer.md).

* Istniejący klaster HDInsight, do którym można dodać sygnaturę dostępu współdzielonego. Jeśli nie, można użyć programu Azure PowerShell, aby utworzyć klaster i dodać podpis dostępu współdzielonego podczas tworzenia klastra.

* Przykładowe pliki [https://github.com/Azure-Samples/hdinsight-dotnet-python-azure-storage-shared-access-signature](https://github.com/Azure-Samples/hdinsight-dotnet-python-azure-storage-shared-access-signature)z pliku . To repozytorium zawiera następujące elementy:

  * Projekt programu Visual Studio, który może utworzyć kontener magazynu, przechowywane zasady i sygnatury dostępu Współdzielonego do użytku z programem HDInsight
  * Skrypt języka Python, który może utworzyć kontener magazynu, przechowywane zasady i sygnatury dostępu Współdzielonego do użytku z hdinsight
  * Skrypt programu PowerShell, który można utworzyć klaster HDInsight i skonfigurować go do używania sygnatury dostępu Współdzielonego. Zaktualizowana wersja jest używana dalej poniżej.
  * Przykładowy plik:`hdinsight-dotnet-python-azure-storage-shared-access-signature-master\sampledata\sample.log`

## <a name="shared-access-signatures"></a>Sygnatury dostępu współdzielonego

Istnieją dwie formy podpisów dostępu współdzielonego:

* Ad hoc: Czas rozpoczęcia, czas wygaśnięcia i uprawnienia dla sygnatury dostępu Współdzielonego są określone w identyfikatorze URI sygnatury dostępu Współdzielonego.

* Zasady dostępu przechowywane: zasady dostępu przechowywane jest zdefiniowany w kontenerze zasobów, takich jak kontener obiektów blob. Zasady mogą służyć do zarządzania ograniczeniami dla jednego lub więcej podpisów dostępu współdzielonego. Po skojarzeniu sygnatury dostępu Współdzielonego z zasadami dostępu przechowywanego sygnatura dostępu współdzielonego dziedziczy ograniczenia — czas rozpoczęcia, czas wygaśnięcia i uprawnienia — zdefiniowane dla zasad dostępu przechowywanego.

Różnica między tymi dwoma formularzami jest ważna dla jednego kluczowego scenariusza: odwołania. Sygnatury dostępu Współdzielonego jest adresem URL, więc każdy, kto uzyskuje sygnatury dostępu Współdzielonego, może go używać, niezależnie od tego, kto o to poprosi. Jeśli sas jest publikowany publicznie, może być używany przez każdego na świecie. Sydamy współdzielone jest prawidłowe, dopóki nie nastąpi jedna z czterech rzeczy:

1. Zostanie osiągnięty czas wygaśnięcia określony w sygnaturze dostępu Współdzielonego.

2. Zostanie osiągnięty czas wygaśnięcia określony w zasadach dostępu przechowywanego, do których odwołuje się sygnatura dostępu Współdzielonego. Następujące scenariusze powodują, że czas wygaśnięcia ma zostać osiągnięty:

    * Upłynął przedział czasu.
    * Przechowywane zasady dostępu jest modyfikowany, aby mieć czas wygaśnięcia w przeszłości. Zmiana czasu wygaśnięcia jest jednym ze sposobów odwołania sygnatury dostępu Współdzielonego.

3. Zasady dostępu przechowywanego, do których odwołuje się sygnatura dostępu Współdzielonego, są usuwane, co jest innym sposobem odwołania sygnatury dostępu Współdzielonego. Jeśli ponownie utworzyć zasady dostępu przechowywane o tej samej nazwie, wszystkie tokeny sygnatury dostępu Współdzielonego dla poprzednich zasad są prawidłowe (jeśli czas wygaśnięcia na sygnatury dostępu współdzielonego nie minęło). Jeśli zamierzasz odwołać sygnatury dostępu Współdzielonego, należy użyć innej nazwy, jeśli ponownie utworzyć zasady dostępu z czasem wygaśnięcia w przyszłości.

4. Klucz konta, który został użyty do utworzenia sygnatury dostępu Współdzielonego jest generowany ponownie. Ponowne generowanie klucza powoduje, że wszystkie aplikacje, które używają poprzedniego klucza, nie powodują niepowodzenia uwierzytelniania. Zaktualizuj wszystkie składniki do nowego klucza.

> [!IMPORTANT]  
> Identyfikator URI podpisu dostępu współdzielonego jest skojarzony z kluczem konta używanym do tworzenia podpisu i skojarzonymi z nimi zasadami dostępu przechowywanego (jeśli istnieje). Jeśli nie określono zasad dostępu przechowywanego, jedynym sposobem odwołania podpisu dostępu współdzielonego jest zmiana klucza konta.

Zaleca się, aby zawsze używać zasad dostępu przechowywanego. Korzystając z przechowywanych zasad, można odwołać podpisy lub przedłużyć datę wygaśnięcia w razie potrzeby. Kroki opisane w tym dokumencie używają zasad dostępu przechowywanego do generowania sygnatury dostępu Współdzielonego.

Aby uzyskać więcej informacji na temat podpisów dostępu współdzielonego, zobacz [Opis modelu sygnatury dostępu współdzielonego](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

## <a name="create-a-stored-policy-and-sas"></a>Tworzenie przechowywanych zasad i sygnatury dostępu Współdzielonego

Zapisz token sygnatury dostępu Współdzielonego, który jest produkowany na końcu każdej metody. Token będzie wyglądać podobnie do następującego:

```output
?sv=2018-03-28&sr=c&si=myPolicyPS&sig=NAxefF%2BrR2ubjZtyUtuAvLQgt%2FJIN5aHJMj6OsDwyy4%3D
```

### <a name="using-powershell"></a>Korzystanie z programu PowerShell

Zamień `RESOURCEGROUP` `STORAGECONTAINER` i `STORAGEACCOUNT`odpowiednie wartości dla istniejącego kontenera magazynu. Zmień katalog `hdinsight-dotnet-python-azure-storage-shared-access-signature-master` na lub `-File` popraw parametr, aby `Set-AzStorageblobcontent`zawierał ścieżkę bezwzględną dla . Wprowadź następujące polecenie programu PowerShell:

```powershell
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

Użycie zmiennych w tej sekcji jest oparte na środowisku systemu Windows. Niewielkie różnice będą potrzebne do bash lub innych środowisk.

1. `STORAGEACCOUNT`Zamień `STORAGECONTAINER` i odpowiednie wartości dla istniejącego kontenera magazynu.

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

2. Ustaw pobrany klucz podstawowy na zmienną do późniejszego użycia. Zamień `PRIMARYKEY` pobraną wartość w poprzednim kroku, a następnie wprowadź poniższe polecenie:

    ```console
    #set variable for primary key
    set AZURE_STORAGE_KEY=PRIMARYKEY
    ```

3. Zmień katalog `hdinsight-dotnet-python-azure-storage-shared-access-signature-master` na lub `--file` popraw parametr, aby `az storage blob upload`zawierał ścieżkę bezwzględną dla . Wykonaj pozostałe polecenia:

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

Otwórz `SASToken.py` plik i `storage_account_name` `storage_account_key`zastąp , i `storage_container_name` z odpowiednimi wartościami dla istniejącego kontenera magazynu, a następnie uruchom skrypt.

Może być konieczne `pip install --upgrade azure-storage` wykonanie, jeśli `ImportError: No module named azure.storage`zostanie wyświetlony komunikat o błędzie .

### <a name="using-c"></a>Przy użyciu języka C#

1. Otwórz rozwiązanie w programie Visual Studio.

2. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy projekt **SASExample** i wybierz polecenie **Właściwości**.

3. Wybierz **ustawienia** i dodaj wartości dla następujących wpisów:

   * StorageConnectionString: Parametry połączenia dla konta magazynu, dla którego chcesz utworzyć przechowywane zasady i sygnatury dostępu Współdzielonego. Format powinien `DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey` znajdować `myaccount` się w miejscu, `mykey` w którym znajduje się nazwa konta magazynu i jest kluczem dla konta magazynu.

   * ContainerName: kontener na koncie magazynu, do którego chcesz ograniczyć dostęp.

   * SASPolicyName: Nazwa używana do tworzenia przechowywanych zasad.

   * FileToUpload: ścieżka do pliku, który jest przekazytywał do kontenera.

4. Uruchom projekt. Zapisz token zasad sygnatury dostępu Współdzielonego, nazwę konta magazynu i nazwę kontenera. Te wartości są używane podczas kojarzenia konta magazynu z klastrem HDInsight.

## <a name="use-the-sas-with-hdinsight"></a>Używanie sygnatury dostępu Współdzielonego z hdinsight

Podczas tworzenia klastra HDInsight należy określić podstawowe konto magazynu i opcjonalnie określić dodatkowe konta magazynu. Obie te metody dodawania magazynu wymagają pełnego dostępu do kont magazynu i kontenerów, które są używane.

Aby ograniczyć dostęp do kontenera za pomocą podpisu dostępu współdzielonego, dodaj wpis niestandardowy do konfiguracji **lokacji podstawowej** klastra. Wpis można dodać podczas tworzenia klastra za pomocą programu PowerShell lub po utworzeniu klastra przy użyciu programu Ambari.

### <a name="create-a-cluster-that-uses-the-sas"></a>Tworzenie klastra korzystającego z sygnatury dostępu Współdzielonego

`CLUSTERNAME`Zastąp `STORAGECONTAINER` `STORAGEACCOUNT`, `RESOURCEGROUP` `DEFAULTSTORAGEACCOUNT` `TOKEN` , , i odpowiednimi wartościami. Wprowadź polecenia programu PowerShell:

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

$config = $config | Add-AzHDInsightConfigValue `
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
> Po wyświetleniu monitu o podanie nazwy użytkownika i hasła http/s lub SSH należy podać hasło spełniające następujące kryteria:
>
> * Musi mieć co najmniej 10 znaków długości.
> * Musi zawierać co najmniej jedną cyfrę.
> * Musi zawierać co najmniej jeden znak niealnumeryczny.
> * Musi zawierać co najmniej jedną wielką lub dolną literę.

To zajmuje trochę czasu dla tego skryptu, aby zakończyć, zwykle około 15 minut. Po zakończeniu skryptu bez żadnych błędów, klaster został utworzony.

### <a name="use-the-sas-with-an-existing-cluster"></a>Używanie sygnatury dostępu Współdzielonego z istniejącym klastrem

Jeśli masz istniejący klaster, możesz dodać sygnaturę dostępu Współdzielonego do konfiguracji **lokacji podstawowej,** wykonując następujące kroki:

1. Otwórz internetowy interfejs użytkownika ambari dla swojego klastra. Adres tej strony `https://YOURCLUSTERNAME.azurehdinsight.net`to . Po wyświetleniu monitu uwierzytelnij się w klastrze przy użyciu nazwy administratora (administratora) i hasła użytego podczas tworzenia klastra.

1. Przejdź do **witryny HDFS** > **Configs** > **Advanced** > Custom**core-site**.

1. Rozwiń sekcję **Niestandardowa lokacja rdzenia,** przewiń do końca, a następnie wybierz pozycję **Dodaj właściwość...**. Użyj następujących wartości dla **klucza** i **wartości:**

    * **Klucz:**`fs.azure.sas.CONTAINERNAME.STORAGEACCOUNTNAME.blob.core.windows.net`
    * **Wartość:** Sygnatury dostępu Współdzielonego zwróconego przez jedną z metod wcześniej wykonanych.

    Zamień `CONTAINERNAME` na nazwę kontenera używaną z aplikacją C# lub SAS. Zastąp `STORAGEACCOUNTNAME` nazwą użytego konta magazynu.

    Wybierz **dodaj,** aby zapisać ten klucz i wartość

1. Wybierz przycisk **Zapisz,** aby zapisać zmiany konfiguracji. Po wyświetleniu monitu dodaj opis zmiany (na przykład"dodawanie dostępu do magazynu sygnatury dostępu współdzielonego), a następnie wybierz pozycję **Zapisz**.

    Po zakończeniu zmian wybierz **przycisk OK.**

   > [!IMPORTANT]  
   > Przed wejściem w życie zmiany należy ponownie uruchomić kilka usług.

1. Zostanie wyświetlona lista rozwijana **Uruchom ponownie.** Z listy rozwijanej **wybierz pozycję Uruchom ponownie wszystkie, których dotyczy problem,** a następnie pozycję __Potwierdź ponownie wszystkie__.

    Powtórz ten proces dla **MapReduce2** i **YARN**.

1. Po ponownym uruchomieniu usług wybierz każdy z nich i wyłącz tryb konserwacji z listy rozwijanej **Akcje usługi.**

## <a name="test-restricted-access"></a>Test ograniczony dostęp

Poniższe kroki można wykonać, aby sprawdzić, czy na koncie magazynu sygnatury dostępu Współdzielonego można odczytywać i wystawiać tylko elementy.

1. Połącz się z klastrem. Zamień `CLUSTERNAME` na nazwę klastra i wprowadź następujące polecenie:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. Aby wyświetlić listę zawartości kontenera, użyj następującego polecenia z wiersza polecenia:

    ```bash
    hdfs dfs -ls wasbs://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/
    ```

    Zamień `SASCONTAINER` na nazwę kontenera utworzonego dla konta magazynu sygnatury dostępu Współdzielonego. Zastąp `SASACCOUNTNAME` nazwą konta magazynu używanego dla sygnatury dostępu Współdzielonego.

    Lista zawiera plik przekazany podczas tworzenia kontenera i sygnatury dostępu Współdzielonego.

3. Użyj następującego polecenia, aby sprawdzić, czy można odczytać zawartość pliku. `SASCONTAINER` Wymień `SASACCOUNTNAME` i tak jak w poprzednim kroku. Zastąp `sample.log` nazwą pliku wyświetlanego w poprzednim poleceniu:

    ```bash
    hdfs dfs -text wasbs://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/sample.log
    ```

    To polecenie zawiera listę zawartości pliku.

4. Użyj następującego polecenia, aby pobrać plik do lokalnego systemu plików:

    ```bash
    hdfs dfs -get wasbs://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/sample.log testfile.txt
    ```

    To polecenie pobiera plik do lokalnego pliku o nazwie **testfile.txt**.

5. Użyj następującego polecenia, aby przekazać plik lokalny do nowego pliku o nazwie **testupload.txt** w magazynie sygnatury dostępu Współdzielonego:

    ```bash
    hdfs dfs -put testfile.txt wasbs://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/testupload.txt
    ```

    Zostanie wyświetlony komunikat podobny do następującego tekstu:

        put: java.io.IOException

    Ten błąd występuje, ponieważ lokalizacja magazynu jest tylko do odczytu +lista. Użyj następującego polecenia, aby umieścić dane w domyślnym magazynie dla klastra, który jest zapisywalny:

    ```bash
    hdfs dfs -put testfile.txt wasbs:///testupload.txt
    ```

    Tym razem operacja powinna zakończyć się pomyślnie.

## <a name="next-steps"></a>Następne kroki

Teraz, gdy już wiesz, jak dodać magazyn o ograniczonym dostępie do klastra HDInsight, poznaj inne sposoby pracy z danymi w klastrze:

* [Korzystanie z programu Apache Hive z usługą HDInsight](hadoop/hdinsight-use-hive.md)
* [Korzystanie z mapReduce z HDInsight](hadoop/hdinsight-use-mapreduce.md)