---
title: Korzystanie z usługi Data Lake Storage Gen1 z usługą Hadoop w usłudze Azure HDInsight
description: Dowiedz się, jak wyszukiwać dane z usługi Azure Data Lake Storage Gen1 i przechowywać wyniki analizy.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 03/01/2020
ms.openlocfilehash: 3e7e5919a3f862f5cad243654972683d1879c4ba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78251085"
---
# <a name="use-data-lake-storage-gen1-with-azure-hdinsight-clusters"></a>Używanie usługi Data Lake Storage Gen1 w klastrach usługi Azure HDInsight

> [!Note]
> Wdrażanie nowych klastrów HDInsight przy użyciu [usługi Azure Data Lake Storage Gen2](hdinsight-hadoop-use-data-lake-storage-gen2.md) w celu zwiększenia wydajności i nowych funkcji.

Aby analizować dane w klastrze HDInsight, można przechowywać dane w [usłudze Azure Storage,](../storage/common/storage-introduction.md) [usłudze Azure Data Lake Storage Gen 1](../data-lake-store/data-lake-store-overview.md)lub [Azure Data Lake Storage Gen 2](../storage/blobs/data-lake-storage-introduction.md). Wszystkie opcje magazynu umożliwiają bezpieczne usuwanie klastrów HDInsight, które są używane do obliczeń bez utraty danych użytkownika.

W tym artykule dowiesz się, jak Usługa Data Lake Storage Gen1 działa z klastrami HDInsight. Aby dowiedzieć się, jak usługa Microsoft Azure Storage współdziała z klastrami usługi HDInsight, zobacz [Use Azure Storage with Azure HDInsight clusters (Używanie usługi Microsoft Azure Storage z klastrami usługi Azure HDInsight)](hdinsight-hadoop-use-blob-storage.md). Aby uzyskać więcej informacji na temat tworzenia klastra HDInsight, zobacz [Tworzenie klastrów Apache Hadoop w pliku HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

> [!NOTE]  
> Data Lake Storage Gen1 jest zawsze dostępny za pośrednictwem `adls` bezpiecznego kanału, więc nie ma nazwy schematu systemu plików. Zawsze używaj nazwy `adl`.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="availability-for-hdinsight-clusters"></a>Dostępność klastrów HDInsight

Apache Hadoop obsługuje pojęcie domyślnego systemu plików. Domyślny system plików wyznacza domyślny schemat i element authority. Może również służyć do rozpoznawania ścieżek względnych. Podczas procesu tworzenia klastra HDInsight można określić kontener obiektów blob w usłudze Azure Storage jako domyślny system plików lub w przypadku systemu HDInsight 3.5 i nowszych wersji, można wybrać usługę Azure Storage lub Usługę Azure Data Lake Storage Gen1 jako domyślny system plików z systemem plików niewielu wyjątków. Należy zauważyć, że klaster i konto magazynu muszą być hostowane w tym samym regionie.

Klastry HDInsight mogą używać pamięci masowej usługi Data Lake Gen1 na dwa sposoby:

* Jako magazyn domyślny
* Jako magazyn dodatkowy z rozszerzeniem Azure Storage Blob jako magazynem domyślnym

Obecnie tylko niektóre typy/wersje klastra HDInsight obsługują używanie usługi Data Lake Storage Gen1 jako domyślnego magazynu i dodatkowych kont magazynu:

| Typ klastra usługi HDInsight | Data Lake Storage Gen1 jako domyślne przechowywanie | Data Lake Storage Gen1 jako dodatkowa pamięć masowa| Uwagi |
|------------------------|------------------------------------|---------------------------------------|------|
| HDInsight wersja 4.0 | Nie | Nie |ADLS Gen1 nie jest obsługiwany w formacie HDInsight 4.0 |
| HDInsight w wersji 3.6 | Tak | Tak | Z wyjątkiem bazy danych HBase|
| HDInsight w wersji 3.5 | Tak | Tak | Z wyjątkiem bazy danych HBase|
| HDInsight w wersji 3.4 | Nie | Tak | |
| HDInsight w wersji 3.3 | Nie | Nie | |
| HDInsight w wersji 3.2 | Nie | Tak | |
| Storm | | |Za pomocą usługi Data Lake Storage Gen1 można zapisywać dane z topologii burzy. Można również użyć magazynu usługi Data Lake dla danych referencyjnych, które następnie mogą być odczytywane przez topologię burzy.|

> [!WARNING]  
> Usługa HDInsight HBase nie jest obsługiwana w usłudze Azure Data Lake Storage Gen1

Używanie usługi Data Lake Storage Gen1 jako dodatkowego konta magazynu nie wpływa na wydajność ani na możliwość odczytu lub zapisu do magazynu platformy Azure z klastra.

## <a name="use-data-lake-storage-gen1-as-default-storage"></a>Używanie pamięci masowej usługi Data Lake Storage Gen1 jako domyślnego magazynu

Gdy usługa HDInsight jest wdrażana z pamięcią Flashd1 jako magazyn `adl://mydatalakestore/<cluster_root_path>/`domyślny, pliki związane z klastrem są przechowywane w obszarze , gdzie `<cluster_root_path>` jest nazwa folderu utworzonego w magazynie usługi Data Lake. Określając ścieżkę główną dla każdego klastra, można użyć tego samego konta Magazynu usługi Data Lake dla więcej niż jednego klastra. Dlatego jest możliwa następująca konfiguracja:

* Klaster1 może używać ścieżki `adl://mydatalakestore/cluster1storage`
* Klaster2 może używać ścieżki `adl://mydatalakestore/cluster2storage`

Należy zauważyć, że oba klastry używają tego samego konta Data Lake Storage Gen1 **mydatalakestore**. Każdy klaster ma dostęp do własnego głównego systemu plików w programie Data Lake Storage. W środowisku wdrażania witryny Azure Portal zostanie wyświetlony monit o użycie nazwy folderu, takiej jak **/clusters/\<nazwa_klastra>**, dla ścieżki głównej.

Aby móc używać usługi Data Lake Storage Gen1 jako magazynu domyślnego, należy udzielić podmiotowi usługi dostępu do następujących ścieżek:

* Katalog główny konta Data Lake Storage Gen1.  na przykład: adl://mydatalakestore/.
* Folder zawierający wszystkie foldery klastra,  na przykład: adl://mydatalakestore/clusters.
* Folder klastra,  na przykład: adl://mydatalakestore/clusters/cluster1storage.

Aby uzyskać więcej informacji dotyczących tworzenia jednostki usługi i udzielania dostępu, zobacz Konfigurowanie dostępu do magazynu usługi Data Lake.

### <a name="extracting-a-certificate-from-azure-keyvault-for-use-in-cluster-creation"></a>Wyodrębnianie certyfikatu z usługi Azure Keyvault do użycia w tworzeniu klastra

Jeśli chcesz skonfigurować usługę Azure Data Lake Storage Gen1 jako domyślny magazyn dla nowego klastra, a certyfikat dla jednostki usługi jest przechowywany w usłudze Azure Key Vault, należy wykonać kilka dodatkowych kroków, aby przekonwertować certyfikat na poprawny format. Poniższe fragmenty kodu pokazują, jak wykonać konwersję.

Najpierw pobierz certyfikat z Usługi Key `SecretValueText`Vault i wyodrębnij plik .

```powershell
$certPassword = Read-Host "Enter Certificate Password"
$cert = (Get-AzureKeyVaultSecret -VaultName 'MY-KEY-VAULT' -Name 'MY-SECRET-NAME')
$certValue = [System.Convert]::FromBase64String($cert.SecretValueText)
```

Następnie przekonwertuj `SecretValueText` certyfikat na certyfikat.

```powershell
$certObject = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList $certValue,$null,"Exportable, PersistKeySet"
$certBytes = $certObject.Export([System.Security.Cryptography.X509Certificates.X509ContentType]::Pkcs12, $certPassword.SecretValueText);
$identityCertificate = [System.Convert]::ToBase64String($certBytes)
```

Następnie można użyć `$identityCertificate` do wdrożenia nowego klastra, jak w następującym urywek:

```powershell
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $pathToArmTemplate `
    -identityCertificate $identityCertificate `
    -identityCertificatePassword $certPassword.SecretValueText `
    -clusterName  $clusterName `
    -clusterLoginPassword $SSHpassword `
    -sshPassword $SSHpassword `
    -servicePrincipalApplicationId $application.ApplicationId
```

## <a name="use-data-lake-storage-gen1-as-additional-storage"></a>Użyj usługi Data Lake Storage Gen1 jako dodatkowego magazynu

Można użyć Data Lake Storage Gen1 jako dodatkowego magazynu dla klastra, jak również. W takich przypadkach domyślnego magazynu klastra może być obiektem blob usługi Azure Storage lub kontem usługi Data Lake Storage. Jeśli używasz zadań HDInsight względem danych przechowywanych w magazynie usługi Data Lake jako dodatkowego magazynu, musisz użyć w pełni kwalifikowanej ścieżki do plików. Przykład:

    adl://mydatalakestore.azuredatalakestore.net/<file_path>

Zwróć uwagę, że teraz w adresie URL nie ma elementu **cluster_root_path**. To dlatego, że Usługa Data Lake Storage nie jest domyślnym magazynem w tym przypadku, więc wszystko, co musisz zrobić, to podać ścieżkę do plików.

Aby móc używać usługi Data Lake Storage Gen1 jako dodatkowego magazynu, wystarczy udzielić jednostce usługi dostępu do ścieżek, w których są przechowywane pliki.  Przykład:

    adl://mydatalakestore.azuredatalakestore.net/<file_path>

Aby uzyskać więcej informacji dotyczących tworzenia jednostki usługi i udzielania dostępu, zobacz Konfigurowanie dostępu do magazynu usługi Data Lake.

## <a name="use-more-than-one-data-lake-storage-accounts"></a>Używanie więcej niż jednego konta usługi Data Lake Storage

Dodawanie konta usługi Data Lake Storage jako dodatkowe i dodawanie więcej niż jednego konta usługi Data Lake Storage odbywa się przez nadanie uprawnienia klastra HDInsight na dane w jednej rudy więcej kont usługi Data Lake Storage. Zobacz Konfigurowanie dostępu do magazynu usługi Data Lake.

## <a name="configure-data-lake-storage-access"></a>Konfigurowanie dostępu do magazynu usługi Data Lake

Aby skonfigurować dostęp do magazynu usługi Data Lake z klastra USŁUGI HDInsight, musisz mieć jednostkę usługi Azure Active Directory (Azure AD). Tylko administrator usługi Azure AD może utworzyć jednostkę usługi. Jednostkę usługi należy utworzyć przy użyciu certyfikatu. Aby uzyskać więcej informacji, zobacz [Szybki start: konfigurowanie klastrów w usłudze HDInsight](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md) i [Tworzenie jednostki usługi przy użyciu certyfikatu z podpisem własnym](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-self-signed-certificate).

> [!NOTE]  
> Jeśli zamierzasz używać usługi Azure Data Lake Storage Gen1 jako dodatkowego magazynu dla klastra HDInsight, zdecydowanie zaleca się to zrobić podczas tworzenia klastra, jak opisano w tym artykule. Dodawanie usługi Azure Data Lake Storage Gen1 jako dodatkowego magazynu do istniejącego klastra HDInsight nie jest obsługiwanym scenariuszem.

Aby uzyskać więcej informacji na temat podstaw modelu kontroli dostępu dla usługi Data Lake Storage Gen1, zobacz [Kontrola dostępu w usłudze Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-access-control.md).

## <a name="access-files-from-the-cluster"></a>Dostęp do plików z klastra

Istnieje kilka sposobów uzyskiwania dostępu do plików w magazynie usługi Data Lake z klastra HDInsight.

* **Przy użyciu w pełni kwalifikowanej nazwy**. W przypadku tej metody należy podać pełną ścieżkę do pliku, do którego chcesz uzyskać dostęp.

    ```
    adl://<data_lake_account>.azuredatalakestore.net/<cluster_root_path>/<file_path>
    ```

* **Przy użyciu skróconego formatu ścieżki**. Za pomocą tego podejścia można zastąpić ścieżkę do katalogu głównego klastra:

    ```
    adl:///<file path>
    ```

* **Przy użyciu ścieżki względnej**. W przypadku tej metody należy podać tylko względną ścieżkę do pliku, do którego chcesz uzyskać dostęp.

    ```
    /<file.path>/
    ```

### <a name="data-access-examples"></a>Przykłady dostępu do danych

Przykłady są oparte na [połączeniu ssh](./hdinsight-hadoop-linux-use-ssh-unix.md) z węzłem głównym klastra. W przykładach użyto wszystkich trzech schematów URI. Zastąp `DATALAKEACCOUNT` i `CLUSTERNAME` odpowiednimi wartościami.

#### <a name="a-few-hdfs-commands"></a>Kilka poleceń hdfs

1. Utwórz prosty plik w magazynie lokalnym.

    ```bash
    touch testFile.txt
    ```

1. Tworzenie katalogów w magazynie klastra.

    ```bash
    hdfs dfs -mkdir adl://DATALAKEACCOUNT.azuredatalakestore.net/clusters/CLUSTERNAME/sampledata1/
    hdfs dfs -mkdir adl:///sampledata2/
    hdfs dfs -mkdir /sampledata3/
    ```

1. Kopiowanie danych z magazynu lokalnego do magazynu klastra.

    ```bash
    hdfs dfs -copyFromLocal testFile.txt adl://DATALAKEACCOUNT.azuredatalakestore.net/clusters/CLUSTERNAME/sampledata1/
    hdfs dfs -copyFromLocal testFile.txt adl:///sampledata2/
    hdfs dfs -copyFromLocal testFile.txt /sampledata3/
    ```

1. Lista zawartości katalogu w magazynie klastra.

    ```bash
    hdfs dfs -ls adl://DATALAKEACCOUNT.azuredatalakestore.net/clusters/CLUSTERNAME/sampledata1/
    hdfs dfs -ls adl:///sampledata2/
    hdfs dfs -ls /sampledata3/
    ```

#### <a name="creating-a-hive-table"></a>Tworzenie tabeli gałęzi

Trzy lokalizacje plików są wyświetlane w celach ilustracyjnych. W przypadku rzeczywistego wykonania należy `LOCATION` użyć tylko jednego z wpisów.

```hql
DROP TABLE myTable;
CREATE EXTERNAL TABLE myTable (
    t1 string,
    t2 string,
    t3 string,
    t4 string,
    t5 string,
    t6 string,
    t7 string)
ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
STORED AS TEXTFILE
LOCATION 'adl://DATALAKEACCOUNT.azuredatalakestore.net/clusters/CLUSTERNAME/example/data/';
LOCATION 'adl:///example/data/';
LOCATION '/example/data/';
```

## <a name="identify-storage-path-from-ambari"></a>Identyfikowanie ścieżki pamięci masowej z ambari

Aby zidentyfikować pełną ścieżkę do skonfigurowanych domyślnych magazynów, przejdź `fs.defaultFS` do**witryny KONfiguracyjne** **HDFS** > i wprowadź w polu wprowadzania filtru.

## <a name="create-hdinsight-clusters-with-access-to-data-lake-storage-gen1"></a>Tworzenie klastrów HDInsight z dostępem do pamięci masowej usługi Data Lake Gen1

Skorzystaj z poniższych łączy, aby uzyskać szczegółowe instrukcje dotyczące tworzenia klastrów HDInsight z dostępem do usługi Data Lake Storage Gen1.

* [Korzystanie z portalu](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md)
* [Korzystanie z programu PowerShell (z pamięcią gen1 magazynu usługi Data Lake jako domyślnego magazynu)](../data-lake-store/data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
* [Korzystanie z programu PowerShell (z pamięcią masową Data Lake Storage Gen1 jako dodatkową pamięcią masową)](../data-lake-store/data-lake-store-hdinsight-hadoop-use-powershell.md)
* [Korzystanie z szablonów platformy Azure](../data-lake-store/data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)

## <a name="refresh-the-hdinsight-certificate-for-data-lake-storage-gen1-access"></a>Odświeżanie certyfikatu usługi HDInsight na potrzeby dostępu usługi Data Lake Storage Gen1

Poniższy przykładowy kod programu PowerShell odczytuje certyfikat z pliku lokalnego lub usługi Azure Key Vault i aktualizuje klaster HDInsight o nowy certyfikat, aby uzyskać dostęp do usługi Azure Data Lake Storage Gen1. Podaj własną nazwę klastra HDInsight, nazwę grupy zasobów, identyfikator subskrypcji, identyfikator aplikacji, lokalną ścieżkę do certyfikatu. Wpisz hasło po wyświetleniu monitu.

```powershell-interactive
$clusterName = '<clustername>'
$resourceGroupName = '<resourcegroupname>'
$subscriptionId = '01234567-8a6c-43bc-83d3-6b318c6c7305'
$appId = '01234567-e100-4118-8ba6-c25834f4e938'
$addNewCertKeyCredential = $true
$certFilePath = 'C:\localfolder\adls.pfx'
$KeyVaultName = "my-key-vault-name"
$KeyVaultSecretName = "my-key-vault-secret-name"
$certPassword = Read-Host "Enter Certificate Password"
# certSource
# 0 - create self signed cert
# 1 - read cert from file path
# 2 - read cert from key vault
$certSource = 0

Login-AzAccount
Select-AzSubscription -SubscriptionId $subscriptionId

if($certSource -eq 0)
{
    Write-Host "Generating new SelfSigned certificate"

    $cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=hdinsightAdlsCert" -KeySpec KeyExchange
    $certBytes = $cert.Export([System.Security.Cryptography.X509Certificates.X509ContentType]::Pkcs12, $certPassword);
    $certString = [System.Convert]::ToBase64String($certBytes)
}
elseif($certSource -eq 1)
{

    Write-Host "Reading the cert file from path $certFilePath"

    $cert = new-object System.Security.Cryptography.X509Certificates.X509Certificate2($certFilePath, $certPassword)
    $certString = [System.Convert]::ToBase64String([System.IO.File]::ReadAllBytes($certFilePath))
}
elseif($certSource -eq 2)
{

    Write-Host "Reading the cert file from Azure Key Vault $KeyVaultName"

    $cert = (Get-AzureKeyVaultSecret -VaultName $KeyVaultName -Name $KeyVaultSecretName)
    $certValue = [System.Convert]::FromBase64String($cert.SecretValueText)
    $certObject = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList $certValue, $null,"Exportable, PersistKeySet"

    $certBytes = $certObject.Export([System.Security.Cryptography.X509Certificates.X509ContentType]::Pkcs12, $certPassword.SecretValueText);

    $certString =[System.Convert]::ToBase64String($certBytes)
}

if($addNewCertKeyCredential)
{
    Write-Host "Creating new KeyCredential for the app"
    $keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())
    New-AzADAppCredential -ApplicationId $appId -CertValue $keyValue -EndDate $cert.NotAfter -StartDate $cert.NotBefore
    Write-Host "Waiting for 7 minutes for the permissions to get propagated"
    Start-Sleep -s 420 #7 minutes
}

Write-Host "Updating the certificate on HDInsight cluster..."

Invoke-AzResourceAction `
    -ResourceGroupName $resourceGroupName `
    -ResourceType 'Microsoft.HDInsight/clusters' `
    -ResourceName $clusterName `
    -ApiVersion '2015-03-01-preview' `
    -Action 'updateclusteridentitycertificate' `
    -Parameters @{ ApplicationId = $appId; Certificate = $certString; CertificatePassword = $certPassword.ToString() } `
    -Force
```

## <a name="next-steps"></a>Następne kroki

W tym artykule dowiesz się, jak używać usługi Azure Lake Storage Gen1 zgodnej z usługą HDFS z usługą HDInsight. Podane tu informacje umożliwiają tworzenie skalowalnych, długoterminowych rozwiązań do pozyskiwania danych archiwalnych i używanie usługi HDInsight w celu efektywnego wykorzystywania informacji przechowywanych w postaci danych ze strukturą i bez niej.

Aby uzyskać więcej informacji, zobacz:

* [Wprowadzenie do usługi Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Szybki start: konfigurowanie klastrów w usłudze HDInsight](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md)
* [Tworzenie klastra usługi HDInsight w celu używania usługi Data Lake Storage Gen1 przy użyciu programu Azure PowerShell](../data-lake-store/data-lake-store-hdinsight-hadoop-use-powershell.md)
* [Przekazywanie danych do usługi HDInsight](hdinsight-upload-data.md)
* [Korzystanie z programu Apache Hive z usługą HDInsight](hadoop/hdinsight-use-hive.md)
* [Use Azure Storage Shared Access Signatures to restrict access to data with HDInsight](hdinsight-storage-sharedaccesssignature-permissions.md) (Używanie sygnatur dostępu współdzielonego do usługi Azure Storage, aby ograniczyć dostęp do danych za pomocą usługi HDInsight)
* [Samouczek: wyodrębnianie, przekształcanie i ładowanie danych przy użyciu interaktywnej kwerendy w usłudze Azure HDInsight](./interactive-query/interactive-query-tutorial-analyze-flight-data.md)
