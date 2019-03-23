---
title: Data Lake Storage Gen1 za pomocą usługi Hadoop w usłudze Azure HDInsight
description: Dowiedz się, jak wykonywać zapytania o dane z usługi Azure Data Lake Storage Gen1 i zapisywać wyniki analiz.
services: hdinsight,storage
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 11/06/2018
ms.openlocfilehash: b567f5e74737c6020a3dd08484354383d45ecb7d
ms.sourcegitcommit: 223604d8b6ef20a8c115ff877981ce22ada6155a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/22/2019
ms.locfileid: "58361893"
---
# <a name="use-data-lake-storage-gen1-with-azure-hdinsight-clusters"></a>Za pomocą usług Data Lake Storage Gen1 klastrów Azure HDInsight

> [!Note] 
> Wdrażanie nowych klastrów HDInsight za pomocą [usługi Azure Data Lake Storage Gen2](hdinsight-hadoop-use-data-lake-storage-gen2.md) większą wydajność i nowe funkcje.

Aby analizować dane w klastrze HDInsight, można przechowywać danych albo w [usługi Azure Storage](../storage/common/storage-introduction.md), [usługi Azure Data Lake Storage Gen 1](../data-lake-store/data-lake-store-overview.md) lub [usługi Azure Data Lake Storage Gen 2](../storage/blobs/data-lake-storage-introduction.md). Wszystkie opcje magazynowania pozwalają bezpiecznie usuwać klastry HDInsight, które są używane do obliczeń bez utraty danych użytkownika.

W tym artykule dowiesz się, jak Data Lake Storage Gen1 działa z klastrami HDInsight. Aby dowiedzieć się, jak usługa Microsoft Azure Storage współdziała z klastrami usługi HDInsight, zobacz [Use Azure Storage with Azure HDInsight clusters (Używanie usługi Microsoft Azure Storage z klastrami usługi Azure HDInsight)](hdinsight-hadoop-use-blob-storage.md). Aby uzyskać więcej informacji na temat tworzenia klastra usługi HDInsight, zobacz [klastrów utworzyć Apache Hadoop w HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

> [!NOTE]  
> Data Lake Storage Gen1 jest dostępna wyłącznie przez zabezpieczony kanał, więc ma nie `adls` Nazwa schematu systemu plików. Zawsze używaj nazwy `adl`.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="availability-for-hdinsight-clusters"></a>Dostępność klastrów HDInsight

Apache Hadoop obsługuje pojęcie domyślnego systemu plików. Domyślny system plików wyznacza domyślny schemat i element authority. Może również służyć do rozpoznawania ścieżek względnych. W trakcie procesu tworzenia klastra HDInsight w usłudze Azure Storage jako domyślny system plików można wskazać kontener obiektów blob lub HDInsight 3.5 i nowsze wersje, można wybrać usługę Azure Storage lub Azure Data Lake Storage Gen1 jako domyślny system plików za pomocą jednak istnieje kilka wyjątków. 

Klastry HDInsight mogą używać Data Lake Storage Gen1 na dwa sposoby:

* Jako magazyn domyślny
* Jako magazyn dodatkowy z rozszerzeniem Azure Storage Blob jako magazynem domyślnym

Aktualnie tylko niektóre HDInsight klastra typy/wersje pomocy technicznej za pomocą programu Data Lake Storage Gen1 jako magazynu domyślnego i dodatkowych kont magazynu:

| Typ klastra usługi HDInsight | Data Lake Storage Gen1 jako magazynem domyślnym | Data Lake Storage Gen1 jako magazyn dodatkowy| Uwagi |
|------------------------|------------------------------------|---------------------------------------|------|
| HDInsight w wersji 3.6 | Yes | Yes | Z wyjątkiem bazy danych HBase|
| HDInsight w wersji 3.5 | Yes | Yes | Z wyjątkiem bazy danych HBase|
| HDInsight w wersji 3.4 | Nie | Yes | |
| HDInsight w wersji 3.3 | Nie | Nie | |
| HDInsight w wersji 3.2 | Nie | Yes | |
| Storm | | |Data Lake Storage Gen1 służy do zapisywania danych pochodzących z topologii Storm. Można również użyć usługi Data Lake Storage dla danych referencyjnych, które następnie mogą być odczytywane przez topologię Storm.|

> [!WARNING]  
> HDInsight HBase nie jest obsługiwana przy użyciu usługi Azure Data Lake Storage Gen1

Za pomocą programu Data Lake Storage Gen1 jako dodatkowe konto magazynu nie wpływa na wydajność ani możliwość odczytu lub zapisu do usługi Azure storage z klastra.

## <a name="use-data-lake-storage-gen1-as-default-storage"></a>Użyj Data Lake Storage Gen1 jako magazynem domyślnym

Po wdrożeniu HDInsight przy użyciu Data Lake Storage Gen1 jako magazynem domyślnym pliki dotyczące klastra są przechowywane w `adl://mydatalakestore/<cluster_root_path>/`, gdzie `<cluster_root_path>` to nazwa folderu utworzonego w usługi Data Lake Storage. Określając ścieżkę główną dla każdego klastra, można użyć tego samego konta usługi Data Lake Storage dla więcej niż jednego klastra. Dlatego jest możliwa następująca konfiguracja:

* Klaster1 może używać ścieżki `adl://mydatalakestore/cluster1storage`
* Klaster2 może używać ścieżki `adl://mydatalakestore/cluster2storage`

Zwróć uwagę, że oba klastry używają tego samego konta Data Lake Storage Gen1 **mydatalakestore**. Każdy klaster ma dostęp do własnego głównego systemu plików w usługi Data Lake Storage. W środowisku wdrażania witryny Azure Portal zostanie wyświetlony monit o użycie nazwy folderu, takiej jak **/clusters/\<nazwa_klastra>**, dla ścieżki głównej.

Aby można było używać Data Lake Storage Gen1 jako magazynem domyślnym, należy przyznać jednostce usługi dostęp do następujących ścieżek:

- Katalog główny konta Data Lake Storage Gen1.  na przykład: adl://mydatalakestore/.
- Folder zawierający wszystkie foldery klastra,  na przykład: adl://mydatalakestore/clusters.
- Folder klastra,  na przykład: adl://mydatalakestore/clusters/cluster1storage.

Aby uzyskać więcej informacji do tworzenia usługi podmiot zabezpieczeń i Udziel dostępu, zobacz skonfigurować magazyn usługi Data Lake dostępu.

### <a name="extracting-a-certificate-from-azure-keyvault-for-use-in-cluster-creation"></a>Wyodrębnianie certyfikatu z magazynu kluczy Azure do użycia w procesie tworzenia klastra

Jeśli chcesz skonfigurować usługi Azure Data Lake Storage Gen1 jako magazynu domyślnego dla nowego klastra, a certyfikat dla jednostki usługi są przechowywane w usłudze Azure Key Vault, istnieje kilka dodatkowych kroków wymaganych do przekonwertowania certyfikatów poprawny format. Poniższe fragmenty kodu przedstawiają sposób wykonania konwersji.

Najpierw Pobierz certyfikat z magazynu Key Vault i Wyodrębnij `SecretValueText`.

```powershell
$certPassword = Read-Host "Enter Certificate Password"
$cert = (Get-AzureKeyVaultSecret -VaultName 'MY-KEY-VAULT' -Name 'MY-SECRET-NAME')
$certValue = [System.Convert]::FromBase64String($cert.SecretValueText)
```

Następnie przekonwertować `SecretValueText` z certyfikatem.

```powershell
$certObject = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList $certValue,$null,"Exportable, PersistKeySet"
$certBytes = $certObject.Export([System.Security.Cryptography.X509Certificates.X509ContentType]::Pkcs12, $certPassword.SecretValueText);
$identityCertificate = [System.Convert]::ToBase64String($certBytes)
```

Następnie można użyć `$identityCertificate` do wdrożenia nowego klastra tak jak w poniższym fragmencie kodu:

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

## <a name="use-data-lake-storage-gen1-as-additional-storage"></a>Użyj Data Lake Storage Gen1 jako magazyn dodatkowy

Data Lake Storage Gen1 można użyć jako dodatkowego magazynu dla klastra, jak również. W takich przypadkach domyślnym magazynem klastra może być konto usługi Azure Blob Storage lub konta usługi Data Lake Storage. Jeśli używasz zadań HDInsight dane przechowywane w usługi Data Lake Storage jako magazynu dodatkowego, musi być w pełni kwalifikowanej ścieżki plików. Na przykład:

    adl://mydatalakestore.azuredatalakestore.net/<file_path>

Zwróć uwagę, że teraz w adresie URL nie ma elementu **cluster_root_path**. To, ponieważ usługi Data Lake Storage nie jest magazynem domyślnym w tym przypadku więc wszystko, co należy zrobić to, podaj ścieżkę do plików.

Aby można było używać Gen1 Data Lake Storage jako magazynu dodatkowego, wystarczy przyznać jednostce usługi dostępu do ścieżki, w którym są przechowywane pliki.  Na przykład:

    adl://mydatalakestore.azuredatalakestore.net/<file_path>

Aby uzyskać więcej informacji do tworzenia usługi podmiot zabezpieczeń i Udziel dostępu, zobacz skonfigurować magazyn usługi Data Lake dostępu.


## <a name="use-more-than-one-data-lake-storage-accounts"></a>Używanie wielu kont usługi Data Lake Storage

Dodawania konta usługi Data Lake Storage jako dodatkowe i więcej niż jeden magazyn usługi Data Lake kont są realizowane przez nadanie uprawnień klastra HDInsight na danych w ramach jednej lub wielu kont usługi Data Lake Storage. Zobacz Konfigurowanie Data Lake dostępu do magazynu.

## <a name="configure-data-lake-storage-access"></a>Konfigurowanie dostępu do usługi Data Lake Storage

Aby skonfigurować dostęp usługi Data Lake Storage z klastra usługi HDInsight, musi mieć usługi Azure Active directory (Azure AD) nazwy głównej usługi. Tylko administrator usługi Azure AD może utworzyć jednostkę usługi. Jednostkę usługi należy utworzyć przy użyciu certyfikatu. Aby uzyskać więcej informacji, zobacz temat [Szybki start: Konfigurowanie klastrów w HDInsight](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md), i [Tworzenie jednostki usługi przy użyciu samoobsługowego Self-signed-certificate](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-self-signed-certificate).

> [!NOTE]  
> Jeśli zamierzasz używać usługi Azure Data Lake Storage Gen1 jako dodatkowego magazynu dla klastra HDInsight, zdecydowanie zaleca się to zrobić podczas tworzenia klastra zgodnie z opisem w tym artykule. Dodawanie usługi Azure Data Lake magazynu Gen1 jako dodatkowego magazynu do istniejącego klastra HDInsight nie jest obsługiwanym scenariuszem.
>

## <a name="access-files-from-the-cluster"></a>Dostęp do plików z klastra

Istnieje kilka sposobów, które są dostępne pliki z usługi Data Lake Storage klastra usługi HDInsight.

* **Przy użyciu w pełni kwalifikowanej nazwy**. W przypadku tej metody należy podać pełną ścieżkę do pliku, do którego chcesz uzyskać dostęp.

        adl://mydatalakestore.azuredatalakestore.net/<cluster_root_path>/<file_path>

* **Przy użyciu skróconego formatu ścieżki**. W przypadku tej metody należy zastąpić ścieżkę do katalogu głównego klastra prefiksem adl:///. W powyższym przykładzie można zastąpić ścieżkę `adl://mydatalakestore.azuredatalakestore.net/<cluster_root_path>/` prefiksem `adl:///`.

        adl:///<file path>

* **Przy użyciu ścieżki względnej**. W przypadku tej metody należy podać tylko względną ścieżkę do pliku, do którego chcesz uzyskać dostęp. Na przykład jeśli pełna ścieżka do pliku to:

        adl://mydatalakestore.azuredatalakestore.net/<cluster_root_path>/example/data/sample.log

    Dostęp do tego samego pliku sample.log można uzyskać zamiast tego za pomocą tej ścieżki względnej.

        /example/data/sample.log

## <a name="create-hdinsight-clusters-with-access-to-data-lake-storage-gen1"></a>Tworzenie klastrów HDInsight z dostępem do programu Data Lake Storage Gen1

Użyj poniższych linków, aby uzyskać szczegółowe instrukcje dotyczące sposobu tworzenia klastrów HDInsight z dostępem do programu Data Lake Storage Gen1.

* [Korzystanie z portalu](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md)
* [Przy użyciu programu PowerShell (za pomocą Data Lake Storage Gen1 jako magazynu domyślnego)](../data-lake-store/data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
* [Przy użyciu programu PowerShell (za pomocą Data Lake Gen1 magazynu jako magazyn dodatkowy)](../data-lake-store/data-lake-store-hdinsight-hadoop-use-powershell.md)
* [Korzystanie z szablonów platformy Azure](../data-lake-store/data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)

## <a name="refresh-the-hdinsight-certificate-for-data-lake-storage-gen1-access"></a>Odśwież certyfikatu HDInsight Data Lake Storage Gen1 dostępu

Poniższy przykład kodu programu PowerShell odczytuje certyfikat z pliku lokalnego lub usługi Azure Key Vault, a aktualizacje klastra usługi HDInsight przy użyciu nowego certyfikatu do dostępu do usługi Azure Data Lake Storage Gen1. Podaj własną nazwę klastra HDInsight, nazwę grupy zasobów, identyfikator subskrypcji, identyfikator aplikacji, ścieżka lokalna do certyfikatu. Wpisz hasło po wyświetleniu monitu.

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

Login-AzAccount
Select-AzSubscription -SubscriptionId $subscriptionId

if($addNewCertKeyCredential)
{
    Write-Host "Creating new KeyCredential for the app"
    $keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())
    New-AzADAppCredential -ApplicationId $appId -CertValue $keyValue -EndDate $cert.NotAfter -StartDate $cert.NotBefore
    Write-Host "Waiting for 30 seconds for the permissions to get propagated"
    Start-Sleep -s 30
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

## <a name="next-steps"></a>Kolejne kroki
W tym artykule przedstawiono sposób użycia zgodnego systemem plików HDFS usługa Azure Data Lake Storage Gen1 z HDInsight. Podane tu informacje umożliwiają tworzenie skalowalnych, długoterminowych rozwiązań do pozyskiwania danych archiwalnych i używanie usługi HDInsight w celu efektywnego wykorzystywania informacji przechowywanych w postaci danych ze strukturą i bez niej.

Aby uzyskać więcej informacji, zobacz:

* [Wprowadzenie do usługi Azure HDInsight][hdinsight-get-started]
* [Szybki start: konfigurowanie klastrów w usłudze HDInsight](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md).
* [Tworzenie klastra usługi HDInsight, aby użyć Data Lake Storage Gen1 przy użyciu programu Azure PowerShell](../data-lake-store/data-lake-store-hdinsight-hadoop-use-powershell.md)
* [Przekazywanie danych do usługi HDInsight][hdinsight-upload-data]
* [Korzystanie z programu Apache Hive z usługą HDInsight][hdinsight-use-hive]
* [Korzystanie z programu Apache Pig z usługą HDInsight][hdinsight-use-pig]
* [Use Azure Storage Shared Access Signatures to restrict access to data with HDInsight][hdinsight-use-sas] (Używanie sygnatur dostępu współdzielonego do usługi Azure Storage, aby ograniczyć dostęp do danych za pomocą usługi HDInsight)

[hdinsight-use-sas]: hdinsight-storage-sharedaccesssignature-permissions.md
[powershell-install]: /powershell/azureps-cmdlets-docs
[hdinsight-creation]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-get-started]:hadoop/apache-hadoop-linux-tutorial-get-started.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-hive]:hadoop/hdinsight-use-hive.md
[hdinsight-use-pig]:hadoop/hdinsight-use-pig.md

[blob-storage-restAPI]: https://msdn.microsoft.com/library/windowsazure/dd135733.aspx
[azure-storage-create]:../storage/common/storage-create-storage-account.md

[img-hdi-powershell-blobcommands]: ./media/hdinsight-hadoop-use-blob-storage/HDI.PowerShell.BlobCommands.png
[img-hdi-quick-create]: ./media/hdinsight-hadoop-use-blob-storage/HDI.QuickCreateCluster.png
[img-hdi-custom-create-storage-account]: ./media/hdinsight-hadoop-use-blob-storage/HDI.CustomCreateStorageAccount.png  
