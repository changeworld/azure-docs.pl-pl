---
title: Tworzenie klastrów HDInsight za pomocą usługi Azure Data Lake Storage Gen1 jako magazynem domyślnym przy użyciu programu PowerShell | Dokumentacja firmy Microsoft
description: Tworzenie i używanie klastrów HDInsight za pomocą usługi Azure Data Lake Storage Gen1 za pomocą programu Azure PowerShell
services: data-lake-store,hdinsight
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.assetid: 8917af15-8e37-46cf-87ad-4e6d5d67ecdb
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: c57a45145d9abc43d0ca79839ea297dfc025db9b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "66161410"
---
# <a name="create-hdinsight-clusters-with-azure-data-lake-storage-gen1-as-default-storage-by-using-powershell"></a>Tworzenie klastrów HDInsight za pomocą usługi Azure Data Lake Storage Gen1 jako magazynem domyślnym przy użyciu programu PowerShell

> [!div class="op_single_selector"]
> * [Korzystanie z witryny Azure Portal](data-lake-store-hdinsight-hadoop-use-portal.md)
> * [Na użytek programu PowerShell (magazyn domyślny)](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
> * [Na użytek programu PowerShell (magazyn dodatkowy)](data-lake-store-hdinsight-hadoop-use-powershell.md)
> * [Użyj usługi Resource Manager](data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)

Dowiedz się, jak skonfigurować klastry usługi Azure HDInsight przy użyciu usługi Azure Data Lake Gen1 magazynu, jako magazynu domyślnego za pomocą programu Azure PowerShell. Aby uzyskać instrukcje dotyczące tworzenia klastra usługi HDInsight przy użyciu Data Lake Storage Gen1 jako dodatkowego magazynu, zobacz [Tworzenie klastra HDInsight z usługą Data Lake Storage Gen1 jako magazynu dodatkowego](data-lake-store-hdinsight-hadoop-use-powershell.md).

Poniżej przedstawiono kilka istotnych kwestii dotyczących HDInsight przy użyciu Data Lake Storage Gen1:

* Możliwość tworzenia klastrów HDInsight z dostępem do Data Lake Storage Gen1 jako magazynem domyślnym jest dostępna dla HDInsight w wersji 3.5 i 3.6.

* Opcja tworzenia HDInsight klastrów z dostępem do Data Lake Storage Gen1, jako magazyn domyślny jest *nie jest dostępna* klastrów HDInsight w warstwie Premium.

Aby skonfigurować HDInsight do pracy z Data Lake Storage Gen1 przy użyciu programu PowerShell, postępuj zgodnie z instrukcjami pięć następnych sekcjach.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Przed rozpoczęciem tego samouczka, upewnij się, że spełniasz następujące wymagania:

* **Subskrypcja platformy Azure**: Przejdź do [uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Program Azure PowerShell 1.0 lub nowszy**: Zobacz [jak zainstalować i skonfigurować program PowerShell](/powershell/azure/overview).
* **Windows Software Development Kit (SDK)**: Aby zainstalować zestaw Windows SDK, przejdź do [pliki do pobrania i narzędzia dla systemu Windows 10](https://dev.windows.com/downloads). Zestaw SDK jest używany do utworzenia certyfikatu zabezpieczeń.
* **Jednostki usługi w usłudze Azure Active Directory**: W tym samouczku opisano tworzenie usługi podmiotu zabezpieczeń w usłudze Azure Active Directory (Azure AD). Jednak aby utworzyć nazwę główną usługi, musi być administratorem usługi Azure AD. Jeśli jesteś administratorem, możesz pominąć to wymaganie wstępne i kontynuować z tego samouczka.

    >[!NOTE]
    >Usługi można utworzyć jednostki, tylko wtedy, gdy jesteś administratorem usługi Azure AD. Administrator usługi Azure AD należy utworzyć jednostkę usługi można było utworzyć klaster usługi HDInsight za pomocą programu Data Lake Storage Gen1. Musi być utworzona nazwa główna usługi za pomocą certyfikatu, zgodnie z opisem w [utworzyć nazwę główną usługi za pomocą certyfikatu](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-certificate-from-certificate-authority).
    >

## <a name="create-a-data-lake-storage-gen1-account"></a>Tworzenie konta Data Lake Storage Gen1

Tworzenie konta Data Lake Storage Gen1, wykonaj następujące czynności:

1. Na pulpicie otwórz okno programu PowerShell, a następnie wprowadź poniższe fragmenty kodu. Po wyświetleniu monitu zaloguj się, zaloguj się jako jeden z administratorów subskrypcji lub właścicieli. 

        # Sign in to your Azure account
        Connect-AzAccount

        # List all the subscriptions associated to your account
        Get-AzSubscription

        # Select a subscription
        Set-AzContext -SubscriptionId <subscription ID>

        # Register for Data Lake Storage Gen1
        Register-AzResourceProvider -ProviderNamespace "Microsoft.DataLakeStore"

    > [!NOTE]
    > Jeśli rejestrowanie dostawcy zasobów Data Lake Storage Gen1 i komunikat o błędzie podobny do `Register-AzResourceProvider : InvalidResourceNamespace: The resource namespace 'Microsoft.DataLakeStore' is invalid`, Twoja subskrypcja może nie być na liście dozwolonych elementów dla programu Data Lake Storage Gen1. Aby włączyć Twojej subskrypcji platformy Azure Data Lake Storage Gen1, postępuj zgodnie z instrukcjami [Rozpoczynanie pracy z usługą Azure Data Lake Storage Gen1 przy użyciu witryny Azure portal](data-lake-store-get-started-portal.md).
    >

2. Konta Data Lake Storage Gen1 jest skojarzona z grupą zasobów platformy Azure. Rozpocznij od utworzenia grupy zasobów.

        $resourceGroupName = "<your new resource group name>"
        New-AzResourceGroup -Name $resourceGroupName -Location "East US 2"

    Powinny pojawić się dane wyjściowe podobne do tego:

        ResourceGroupName : hdiadlgrp
        Location          : eastus2
        ProvisioningState : Succeeded
        Tags              :
        ResourceId        : /subscriptions/<subscription-id>/resourceGroups/hdiadlgrp

3. Tworzenie konta Data Lake Storage Gen1. Nazwa konta, które określisz musi zawierać tylko małe litery i cyfry.

        $dataLakeStorageGen1Name = "<your new Data Lake Storage Gen1 name>"
        New-AzDataLakeStoreAccount -ResourceGroupName $resourceGroupName -Name $dataLakeStorageGen1Name -Location "East US 2"

    Powinny pojawić się dane wyjściowe podobne do następujących:

        ...
        ProvisioningState           : Succeeded
        State                       : Active
        CreationTime                : 5/5/2017 10:53:56 PM
        EncryptionState             : Enabled
        ...
        LastModifiedTime            : 5/5/2017 10:53:56 PM
        Endpoint                    : hdiadlstore.azuredatalakestore.net
        DefaultGroup                :
        Id                          : /subscriptions/<subscription-id>/resourceGroups/hdiadlgrp/providers/Microsoft.DataLakeStore/accounts/hdiadlstore
        Name                        : hdiadlstore
        Type                        : Microsoft.DataLakeStore/accounts
        Location                    : East US 2
        Tags                        : {}

4. Za pomocą programu Data Lake Storage Gen1 jako magazynem domyślnym wymaga określenia ścieżka katalogu głównego, do którego pliki dotyczące klastra są kopiowane podczas tworzenia klastra. Aby utworzyć ścieżka katalogu głównego, który jest **/klastrów/hdiadlcluster** we fragmencie, użyj następujących poleceń cmdlet:

        $myrootdir = "/"
        New-AzDataLakeStoreItem -Folder -AccountName $dataLakeStorageGen1Name -Path $myrootdir/clusters/hdiadlcluster


## <a name="set-up-authentication-for-role-based-access-to-data-lake-storage-gen1"></a>Konfigurowanie uwierzytelniania opartego na rolach dostęp do programu Data Lake Storage Gen1
Każda subskrypcja platformy Azure jest skojarzony z jednostką usługi Azure AD. Użytkownicy i usługi, uzyskujących dostęp do zasobów subskrypcji przy użyciu witryny Azure portal lub interfejsu API usługi Azure Resource Manager, należy najpierw uwierzytelniania za pomocą usługi Azure AD. Udzielić dostępu do subskrypcji platformy Azure i usług, przypisując im odpowiednią rolę w obrębie zasobu platformy Azure. W przypadku usług, nazwy głównej usługi określa usługi w usłudze Azure AD.

W tej sekcji pokazano, jak udzielić usługi aplikacji, takich jak HDInsight, dostęp do zasobów platformy Azure (konta Data Lake Storage Gen1, który został utworzony wcześniej). Możesz to zrobić przez tworzenie jednostki dla aplikacji i przypisywanie ról do niego za pośrednictwem programu PowerShell usługi.

Aby skonfigurować uwierzytelnianie usługi Active Directory dla programu Data Lake Storage Gen1, należy wykonać zadania opisane w poniższych sekcjach.

### <a name="create-a-self-signed-certificate"></a>Tworzenie certyfikatu z podpisem własnym
Upewnij się, że masz [zestawu Windows SDK](https://dev.windows.com/en-us/downloads) zainstalowane przed wykonaniem kroków w tej sekcji. Należy także utworzyć katalogu, takie jak *C:\mycertdir*, w którym można utworzyć certyfikatu.

1. W oknie programu PowerShell, przejdź do lokalizacji, w której zainstalowany zestaw Windows SDK (zazwyczaj *\Windows Kits\10\bin\x86 C:\Program Files (x86)*) i używać [MakeCert] [ makecert] Narzędzie do tworzenia certyfikatu z podpisem własnym i klucza prywatnego. Użyj następujących poleceń:

        $certificateFileDir = "<my certificate directory>"
        cd $certificateFileDir

        makecert -sv mykey.pvk -n "cn=HDI-ADL-SP" CertFile.cer -r -len 2048

    Zostanie wyświetlony monit wpisz hasło klucza prywatnego. Po pomyślnym wykonaniu polecenia, powinien zostać wyświetlony **CertFile.cer** i **mykey.pvk** w katalogu certyfikat, który określono.
2. Użyj [Pvk2Pfx] [ pvk2pfx] narzędzie do konwersji plików .pvk i cer, utworzone przez narzędzie MakeCert do pliku .pfx. Uruchom następujące polecenie:

        pvk2pfx -pvk mykey.pvk -spc CertFile.cer -pfx CertFile.pfx -po <password>

    Po wyświetleniu monitu wprowadź określone wcześniej hasło klucza prywatnego. Wartość określona dla **— po** parametr jest hasło, która jest skojarzona z pliku pfx. Po polecenie zostało zakończone pomyślnie, powinno również zostać wyświetlone **CertFile.pfx** w katalogu certyfikat, który określono.

### <a name="create-an-azure-ad-and-a-service-principal"></a>Tworzenie usługi Azure AD i jednostkę usługi
W tej sekcji utworzyć jednostkę usługi dla aplikacji usługi Azure AD, przypisania roli do jednostki usługi i uwierzytelniać się jako nazwa główna usługi, zapewniając certyfikatu. Aby utworzyć aplikację w usłudze Azure AD, uruchom następujące polecenia:

1. Wklej następujące polecenia cmdlet w oknie konsoli programu PowerShell. Upewnij się, że wartości określonej dla **- DisplayName** właściwość jest unikatowa. Wartości **— strona główna** i **- IdentiferUris** są wartości symboli zastępczych i nie są weryfikowane.

        $certificateFilePath = "$certificateFileDir\CertFile.pfx"

        $password = Read-Host -Prompt "Enter the password" # This is the password you specified for the .pfx file

        $certificatePFX = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($certificateFilePath, $password)

        $rawCertificateData = $certificatePFX.GetRawCertData()

        $credential = [System.Convert]::ToBase64String($rawCertificateData)

        $application = New-AzADApplication `
            -DisplayName "HDIADL" `
            -HomePage "https://contoso.com" `
            -IdentifierUris "https://mycontoso.com" `
            -CertValue $credential  `
            -StartDate $certificatePFX.NotBefore  `
            -EndDate $certificatePFX.NotAfter

        $applicationId = $application.ApplicationId
2. Tworzenie jednostki usługi przy użyciu identyfikatora aplikacji.

        $servicePrincipal = New-AzADServicePrincipal -ApplicationId $applicationId

        $objectId = $servicePrincipal.Id
3. Udzielanie dostępu do jednostki usługi Data Lake Storage Gen1 głównego i wszystkich folderów w ścieżce katalogu głównego, określony wcześniej. Użyj następujących poleceń cmdlet:

        Set-AzDataLakeStoreItemAclEntry -AccountName $dataLakeStorageGen1Name -Path / -AceType User -Id $objectId -Permissions All
        Set-AzDataLakeStoreItemAclEntry -AccountName $dataLakeStorageGen1Name -Path /clusters -AceType User -Id $objectId -Permissions All
        Set-AzDataLakeStoreItemAclEntry -AccountName $dataLakeStorageGen1Name -Path /clusters/hdiadlcluster -AceType User -Id $objectId -Permissions All

## <a name="create-an-hdinsight-linux-cluster-with-data-lake-storage-gen1-as-the-default-storage"></a>Utwórz klaster usługi HDInsight w systemie Linux za pomocą Data Lake Storage Gen1 jako magazyn domyślny

W tej sekcji utworzysz klaster usługi HDInsight Hadoop Linux z Data Lake Storage Gen1 jako magazyn domyślny. W tej wersji klastra HDInsight i Data Lake Storage Gen1 muszą być w tej samej lokalizacji.

1. Pobieranie Identyfikatora dzierżawy subskrypcji i zapisz go do późniejszego użycia.

        $tenantID = (Get-AzContext).Tenant.TenantId

2. Tworzenie klastra HDInsight za pomocą następujących poleceń cmdlet:

        # Set these variables

        $location = "East US 2"
        $storageAccountName = $dataLakeStorageGen1Name                         # Data Lake Storage Gen1 account name
        $storageRootPath = "<Storage root path you specified earlier>" # E.g. /clusters/hdiadlcluster
        $clusterName = "<unique cluster name>"
        $clusterNodes = <ClusterSizeInNodes>            # The number of nodes in the HDInsight cluster
        $httpCredentials = Get-Credential
        $sshCredentials = Get-Credential

        New-AzHDInsightCluster `
               -ClusterType Hadoop `
               -OSType Linux `
               -ClusterSizeInNodes $clusterNodes `
               -ResourceGroupName $resourceGroupName `
               -ClusterName $clusterName `
               -HttpCredential $httpCredentials `
               -Location $location `
               -DefaultStorageAccountType AzureDataLakeStore `
               -DefaultStorageAccountName "$storageAccountName.azuredatalakestore.net" `
               -DefaultStorageRootPath $storageRootPath `
               -Version "3.6" `
               -SshCredential $sshCredentials `
               -AadTenantId $tenantId `
               -ObjectId $objectId `
               -CertificateFilePath $certificateFilePath `
               -CertificatePassword $password

    Po pomyślnym zakończeniu polecenia cmdlet powinny być widoczne dane wyjściowe, który wyświetla szczegóły klastra.

## <a name="run-test-jobs-on-the-hdinsight-cluster-to-use-data-lake-storage-gen1"></a>Uruchamianie testów zadań w klastrze HDInsight, aby użyć Data Lake Storage Gen1
Po skonfigurowaniu klastra usługi HDInsight można uruchomić zadania testowe na niej w celu zapewnienia, że mają dostęp do programu Data Lake Storage Gen1. Aby to zrobić, uruchamianie przykładowego zadania Hive można utworzyć tabeli, który używa przykładowych danych, który jest już dostępna w Data Lake Storage Gen1 w  *\<katalogu głównego klastra > /example/data/sample.log*.

W tej sekcji należy utworzyć połączenie Secure Shell (SSH) do klastra HDInsight w systemie Linux, który został utworzony, a następnie uruchom przykładowe zapytanie programu Hive.

* Jeśli używasz klienta Windows nawiązanie połączenia SSH do klastra, zobacz [używanie protokołu SSH z opartą na systemie Linux z platformą Hadoop w HDInsight z Windows](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md).
* Jeśli używasz klienta Linux nawiązanie połączenia SSH do klastra, zobacz [używanie protokołu SSH z opartą na systemie Linux platformą Hadoop w HDInsight z systemu Linux](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).

1. Po nawiązaniu połączenia, należy uruchomić Hive interfejsu wiersza polecenia (CLI) przy użyciu następującego polecenia:

        hive
2. Użyj interfejsu wiersza polecenia, aby wprowadzić poniższe instrukcje, aby utworzyć nową tabelę o nazwie **pojazdów** przy użyciu przykładowych danych w Data Lake Storage Gen1:

        DROP TABLE log4jLogs;
        CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
        STORED AS TEXTFILE LOCATION 'adl:///example/data/';
        SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;

    Wynik kwerendy powinien być widoczny w konsoli SSH.

    >[!NOTE]
    >Ścieżka do przykładowych danych w poprzednim poleceniu CREATE TABLE jest `adl:///example/data/`, gdzie `adl:///` jest głównym klastra. Poniższy przykład głównego klastra, który jest określony w tym samouczku polecenie jest `adl://hdiadlstore.azuredatalakestore.net/clusters/hdiadlcluster`. Możesz użyć alternatywnej krótszy lub podać pełną ścieżkę do katalogu głównego klastra.
    >

## <a name="access-data-lake-storage-gen1-by-using-hdfs-commands"></a>Dostęp Data Lake Storage Gen1 przy użyciu poleceń systemu plików HDFS
Po skonfigurowaniu klastra HDInsight w Data Lake Storage Gen1 służy poleceń powłoki pliku System (HDFS, Hadoop Distributed) dostęp do sklepu.

W tej sekcji wprowadzisz połączenie SSH w klastrze HDInsight w systemie Linux, który został utworzony, a następnie uruchomić poleceń systemu plików HDFS.

* Jeśli używasz klienta Windows nawiązanie połączenia SSH do klastra, zobacz [używanie protokołu SSH z opartą na systemie Linux z platformą Hadoop w HDInsight z Windows](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md).
* Jeśli używasz klienta Linux nawiązanie połączenia SSH do klastra, zobacz [używanie protokołu SSH z opartą na systemie Linux platformą Hadoop w HDInsight z systemu Linux](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).

Po utworzeniu połączenia listy plików w Data Lake Storage Gen1, za pomocą następującego polecenia systemu plików HDFS.

    hdfs dfs -ls adl:///

Można również użyć `hdfs dfs -put` polecenie, aby przekazać pliki do Data Lake Storage Gen1, a następnie użyj `hdfs dfs -ls` Aby sprawdzić, czy pliki zostały pomyślnie przekazane.

## <a name="see-also"></a>Zobacz także
* [Za pomocą usług Data Lake Storage Gen1 klastrów Azure HDInsight](../hdinsight/hdinsight-hadoop-use-data-lake-store.md)
* [Witryna Azure portal: Tworzenie klastra usługi HDInsight, aby użyć Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)

[makecert]: https://msdn.microsoft.com/library/windows/desktop/ff548309(v=vs.85).aspx
[pvk2pfx]: https://msdn.microsoft.com/library/windows/desktop/ff550672(v=vs.85).aspx
