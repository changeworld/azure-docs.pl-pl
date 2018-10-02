---
title: Użyj Data Lake Store z usługą Hadoop w usłudze Azure HDInsight
description: Dowiedz się, jak wykonywać zapytania o dane z usługi Azure Data Lake Store i zapisywać wyniki analiz.
services: hdinsight,storage
author: jasonwhowell
ms.author: jasonh
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 07/23/2018
ms.openlocfilehash: e1c76f65df13811a3dc3028e627b7c19c1437fb5
ms.sourcegitcommit: 5843352f71f756458ba84c31f4b66b6a082e53df
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/01/2018
ms.locfileid: "47585547"
---
# <a name="use-data-lake-store-with-azure-hdinsight-clusters"></a>Korzystanie z usługi Data Lake Store w połączeniu z klastrami usługi Azure HDInsight

Aby analizować dane w klastrze usługi HDInsight, możesz je zapisać w usłudze [Microsoft Azure Storage](../storage/common/storage-introduction.md) lub [Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md) albo obu tych usługach. Obie opcje magazynowania pozwalają bezpiecznie usuwać klastry usługi HDInsight używane do obliczeń bez utraty danych użytkownika.

W tym artykule omówiono współdziałanie usługi Data Lake Store z klastrami usługi HDInsight. Aby dowiedzieć się, jak usługa Microsoft Azure Storage współdziała z klastrami usługi HDInsight, zobacz [Use Azure Storage with Azure HDInsight clusters (Używanie usługi Microsoft Azure Storage z klastrami usługi Azure HDInsight)](hdinsight-hadoop-use-blob-storage.md). Więcej informacji dotyczących tworzenia klastra usługi HDInsight można znaleźć w temacie [Create Hadoop clusters in HDInsight (Tworzenie klastrów platformy Hadoop w usłudze HDInsight)](hdinsight-hadoop-provision-linux-clusters.md).

> [!NOTE]
> Usługa Data Lake Store jest dostępna wyłącznie przez zabezpieczony kanał, dlatego nazwa schematu systemu plików `adls` nie jest używana. Zawsze używaj nazwy `adl`.
> 


## <a name="availability-for-hdinsight-clusters"></a>Dostępność klastrów HDInsight

Platforma Hadoop obsługuje pojęcie domyślnego systemu plików. Domyślny system plików wyznacza domyślny schemat i element authority. Może również służyć do rozpoznawania ścieżek względnych. W trakcie procesu tworzenia klastra usługi HDInsight jako domyślny system plików można wskazać kontener obiektów blob w usłudze Azure Storage. W przypadku wersji 3.5 lub nowszych wersji usługi HDInsight jako domyślny system plików można wybrać usługę Microsoft Azure Storage lub Azure Data Lake Store (z nielicznymi wyjątkami). 

Klastry usługi HDInsight mogą używać usługi Data Lake Store na dwa sposoby:

* Jako magazyn domyślny
* Jako magazyn dodatkowy z rozszerzeniem Azure Storage Blob jako magazynem domyślnym

Aktualnie tylko niektóre typy/wersje klastra usługi HDInsight obsługują używanie kont usługi Data Lake Store jako magazynu domyślnego i magazynu dodatkowego:

| Typ klastra usługi HDInsight | Usługa Data Lake Store jako magazyn domyślny | Usługa Data Lake Store jako magazyn dodatkowy| Uwagi |
|------------------------|------------------------------------|---------------------------------------|------|
| HDInsight w wersji 3.6 | Yes | Yes | |
| HDInsight w wersji 3.5 | Yes | Yes | Z wyjątkiem bazy danych HBase|
| HDInsight w wersji 3.4 | Nie | Yes | |
| HDInsight w wersji 3.3 | Nie | Nie | |
| HDInsight w wersji 3.2 | Nie | Yes | |
| Storm | | |Usługa Data Lake Store umożliwia zapisanie danych pochodzących z topologii Storm. Usługi Data Lake Store można również używać do obsługi danych referencyjnych, które następnie mogą być odczytywane przez topologię Storm.|

Używanie usługi Data Lake Store jako konta magazynu dodatkowego nie ma wpływu na wydajność ani możliwość odczytywania danych z klastra lub zapisywania ich w usłudze Azure Storage.


## <a name="use-data-lake-store-as-default-storage"></a>Używanie usługi Data Lake Store jako magazynu domyślnego

Po wdrożeniu usługi HDInsight z usługą Data Lake Store jako magazynem domyślnym pliki klastra są zapisywane w usłudze Data Lake Store w następującej lokalizacji:

    adl://mydatalakestore/<cluster_root_path>/

gdzie `<cluster_root_path>` to nazwa folderu utworzonego w usłudze Data Lake Store. Określając ścieżkę główną dla każdego klastra, możesz użyć tego samego konta usługi Data Lake Store dla wielu klastrów. Dlatego jest możliwa następująca konfiguracja:

* Klaster1 może używać ścieżki `adl://mydatalakestore/cluster1storage`
* Klaster2 może używać ścieżki `adl://mydatalakestore/cluster2storage`

Zwróć uwagę, że oba klastry używają tego samego konta usługi Data Lake Store — **mydatalakestore**. Każdy klaster ma dostęp do własnego głównego systemu plików w usłudze Data Lake Store. W środowisku wdrażania witryny Azure Portal zostanie wyświetlony monit o użycie nazwy folderu, takiej jak **/clusters/\<nazwa_klastra>**, dla ścieżki głównej.

Aby używanie usługi Data Lake Store jako magazynu domyślnego było możliwe, jednostka usługi musi mieć dostęp do następujących ścieżek:

- Katalog główny konta usługi Data Lake Store,  na przykład: adl://mydatalakestore/.
- Folder zawierający wszystkie foldery klastra,  na przykład: adl://mydatalakestore/clusters.
- Folder klastra,  na przykład: adl://mydatalakestore/clusters/cluster1storage.

Aby uzyskać więcej informacji na temat tworzenia jednostki usługi i przyznawania dostępu, zobacz [Konfigurowanie dostępu do usługi Data Lake Store](#configure-data-lake-store-access).


## <a name="use-data-lake-store-as-additional-storage"></a>Używanie usługi Data Lake Store jako magazynu dodatkowego

Jako dodatkowego magazynu dla klastra można również użyć usługi Data Lake Store. Domyślnym magazynem klastra może wtedy być konto usługi Azure Storage Blob lub Data Lake Store. Jeśli do obsługi danych przechowywanych w usłudze Data Lake Store jako magazynie dodatkowym używasz zadań usługi HDInsight, musisz podać w pełni kwalifikowaną ścieżkę do plików. Na przykład:

    adl://mydatalakestore.azuredatalakestore.net/<file_path>

Zwróć uwagę, że teraz w adresie URL nie ma elementu **cluster_root_path**. Przyczyną jest to, że w tym przypadku usługa Data Lake Store nie jest magazynem domyślnym, więc należy tylko podać ścieżkę do plików.

Aby używać usługi Data Lake Store jako magazynu dodatkowego, wystarczy przyznać jednostce usługi dostęp do ścieżek przechowywania plików.  Na przykład:

    adl://mydatalakestore.azuredatalakestore.net/<file_path>

Aby uzyskać więcej informacji na temat tworzenia jednostki usługi i przyznawania dostępu, zobacz [Konfigurowanie dostępu do usługi Data Lake Store](#configure-data-lake-store-access).


## <a name="use-more-than-one-data-lake-store-accounts"></a>Używanie wielu kont usługi Data Lake Store

Aby dodać kilka kont usługi Data Lake Store, należy przyznać klastrowi usługi HDInsight uprawnienia do danych na kontach usługi Data Lake Store. Zobacz [Konfigurowanie dostępu do usługi Data Lake Store](#configure-data-lake-store-access).

## <a name="configure-data-lake-store-access"></a>Konfigurowanie dostępu do usługi Data Lake Store

Aby skonfigurować dostęp do usługi Data Lake Store z klastra usługi HDInsight, musisz mieć jednostkę usługi Azure Active Directory (Azure AD). Tylko administrator usługi Azure AD może utworzyć jednostkę usługi. Jednostkę usługi należy utworzyć przy użyciu certyfikatu. Aby uzyskać więcej informacji, zobacz [Szybki start: konfigurowanie klastrów w usłudze HDInsight](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md) i [Tworzenie jednostki usługi przy użyciu certyfikatu z podpisem własnym](../azure-resource-manager/resource-group-authenticate-service-principal.md#create-service-principal-with-self-signed-certificate).

> [!NOTE]
> Jeśli zamierzasz używać usługi Azure Data Lake Store jako dodatkowego magazynu klastra usługi HDInsight, zdecydowanie zalecamy wykonanie tej czynności podczas tworzenia klastra zgodnie z opisem w tym artykule. Scenariusz obejmujący dodawanie usługi Azure Data Lake Store jako dodatkowego magazynu do istniejącego klastra usługi HDInsight nie jest obsługiwany.
>

## <a name="access-files-from-the-cluster"></a>Dostęp do plików z klastra

Istnieją różne sposoby uzyskiwania dostępu do plików w usłudze Data Lake Store z klastra usługi HDInsight.

* **Przy użyciu w pełni kwalifikowanej nazwy**. W przypadku tej metody należy podać pełną ścieżkę do pliku, do którego chcesz uzyskać dostęp.

        adl://mydatalakestore.azuredatalakestore.net/<cluster_root_path>/<file_path>

* **Przy użyciu skróconego formatu ścieżki**. W przypadku tej metody należy zastąpić ścieżkę do katalogu głównego klastra prefiksem adl:///. W powyższym przykładzie można zastąpić ścieżkę `adl://mydatalakestore.azuredatalakestore.net/<cluster_root_path>/` prefiksem `adl:///`.

        adl:///<file path>

* **Przy użyciu ścieżki względnej**. W przypadku tej metody należy podać tylko względną ścieżkę do pliku, do którego chcesz uzyskać dostęp. Na przykład jeśli pełna ścieżka do pliku to:

        adl://mydatalakestore.azuredatalakestore.net/<cluster_root_path>/example/data/sample.log

    Dostęp do tego samego pliku sample.log można uzyskać zamiast tego za pomocą tej ścieżki względnej.

        /example/data/sample.log

## <a name="create-hdinsight-clusters-with-access-to-data-lake-store"></a>Tworzenie klastrów usługi HDInsight z dostępem do usługi Data Lake Store

Poniższe linki pozwalają uzyskać szczegółowe instrukcje dotyczące tworzenia klastrów usługi HDInsight z dostępem do usługi Data Lake Store.

* [Korzystanie z portalu](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md)
* [HDInsight with Data Lake Store as default storage - PowerShell](../data-lake-store/data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md) (Usługa HDInsight z usługą Data Lake Store jako magazynem domyślnym — PowerShell)
* [Using PowerShell (with Data Lake Store as additional storage)](../data-lake-store/data-lake-store-hdinsight-hadoop-use-powershell.md) (Korzystanie z programu PowerShell z usługą Data Lake Store jako magazynem dodatkowym)
* [Korzystanie z szablonów platformy Azure](../data-lake-store/data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)

## <a name="refresh-the-hdinsight-certificate-for-data-lake-store-access"></a>Odśwież certyfikatu HDInsight dostępu Data Lake Store

Poniższy przykład kodu programu PowerShell odczytuje lokalnego pliku certyfikatu i aktualizuje klastra usługi HDInsight przy użyciu nowego certyfikatu, aby dostęp do usługi Azure Data Lake Store. Podaj własną nazwę klastra HDInsight, nazwę grupy zasobów, identyfikator subskrypcji, identyfikator aplikacji, ścieżka lokalna do certyfikatu. Wpisz hasło po wyświetleniu monitu.

```powershell-interactive
$clusterName = '<clustername>'
$resourceGroupName = '<resourcegroupname>'
$subscriptionId = '01234567-8a6c-43bc-83d3-6b318c6c7305'
$appId = '01234567-e100-4118-8ba6-c25834f4e938'
$generateSelfSignedCert = $false
$addNewCertKeyCredential = $true
$certFilePath = 'C:\localfolder\adls.pfx'
$certPassword = Read-Host "Enter Certificate Password"

if($generateSelfSignedCert)
{
    Write-Host "Generating new SelfSigned certificate"
    
    $cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=hdinsightAdlsCert" -KeySpec KeyExchange
    $certBytes = $cert.Export([System.Security.Cryptography.X509Certificates.X509ContentType]::Pkcs12, $certPassword);
    $certString = [System.Convert]::ToBase64String($certBytes)
}
else
{

    Write-Host "Reading the cert file from path $certFilePath"

    $cert = new-object System.Security.Cryptography.X509Certificates.X509Certificate2($certFilePath, $certPassword)
    $certString = [System.Convert]::ToBase64String([System.IO.File]::ReadAllBytes($certFilePath))
}

Login-AzureRmAccount
Select-AzureRmSubscription -SubscriptionId $subscriptionId

if($addNewCertKeyCredential)
{
    Write-Host "Creating new KeyCredential for the app"
    $keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())
    New-AzureRmADAppCredential -ApplicationId $appId -CertValue $keyValue -EndDate $cert.NotAfter -StartDate $cert.NotBefore
    Write-Host "Waiting for 30 seconds for the permissions to get propagated"
    Start-Sleep -s 30
}

Write-Host "Updating the certificate on HDInsight cluster..."

Invoke-AzureRmResourceAction `
    -ResourceGroupName $resourceGroupName `
    -ResourceType 'Microsoft.HDInsight/clusters' `
    -ResourceName $clusterName `
    -ApiVersion '2015-03-01-preview' `
    -Action 'updateclusteridentitycertificate' `
    -Parameters @{ ApplicationId = $appId; Certificate = $certString; CertificatePassword = $certPassword.ToString() } `
    -Force
```

## <a name="next-steps"></a>Kolejne kroki
W tym artykule przedstawiono sposób korzystania z usługi Azure Data Lake Store zgodnej z systemem plików HDFS w połączeniu z usługą HDInsight. Podane tu informacje umożliwiają tworzenie skalowalnych, długoterminowych rozwiązań do pozyskiwania danych archiwalnych i używanie usługi HDInsight w celu efektywnego wykorzystywania informacji przechowywanych w postaci danych ze strukturą i bez niej.

Aby uzyskać więcej informacji, zobacz:

* [Wprowadzenie do usługi Azure HDInsight][hdinsight-get-started]
* [Szybki start: konfigurowanie klastrów w usłudze HDInsight](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md)
* [Create an HDInsight cluster to use Data Lake Store using the Azure PowerShell](../data-lake-store/data-lake-store-hdinsight-hadoop-use-powershell.md) (Tworzenie klastra usługi HDInsight z usługą Data Lake Store za pomocą programu Azure PowerShell)
* [Przekazywanie danych do usługi HDInsight][hdinsight-upload-data]
* [Korzystanie z programu Hive z usługą HDInsight][hdinsight-use-hive]
* [Korzystanie z języka Pig z usługą HDInsight][hdinsight-use-pig]
* [Use Azure Storage Shared Access Signatures to restrict access to data with HDInsight][hdinsight-use-sas] (Używanie sygnatur dostępu współdzielonego do usługi Azure Storage, aby ograniczyć dostęp do danych za pomocą usługi HDInsight)

[hdinsight-use-sas]: hdinsight-storage-sharedaccesssignature-permissions.md
[powershell-install]: /powershell/azureps-cmdlets-docs
[hdinsight-creation]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-get-started]:hadoop/apache-hadoop-linux-tutorial-get-started.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-hive]:hadoop/hdinsight-use-hive.md
[hdinsight-use-pig]:hadoop/hdinsight-use-pig.md

[blob-storage-restAPI]: http://msdn.microsoft.com/library/windowsazure/dd135733.aspx
[azure-storage-create]:../storage/common/storage-create-storage-account.md

[img-hdi-powershell-blobcommands]: ./media/hdinsight-hadoop-use-blob-storage/HDI.PowerShell.BlobCommands.png
[img-hdi-quick-create]: ./media/hdinsight-hadoop-use-blob-storage/HDI.QuickCreateCluster.png
[img-hdi-custom-create-storage-account]: ./media/hdinsight-hadoop-use-blob-storage/HDI.CustomCreateStorageAccount.png  
