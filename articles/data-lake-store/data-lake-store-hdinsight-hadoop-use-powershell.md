---
title: 'Program PowerShell: Klaster usługi Azure HDInsight przy użyciu usługi Azure Data Lake Storage Gen1 jako dodatkowego magazynu | Dokumentacja firmy Microsoft'
services: data-lake-store,hdinsight
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.assetid: 164ada5a-222e-4be2-bd32-e51dbe993bc0
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: f78ad8d58bb1bc760a31b792b44a4a39ed25e1f3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "66161395"
---
# <a name="use-azure-powershell-to-create-an-hdinsight-cluster-with-azure-data-lake-storage-gen1-as-additional-storage"></a>Tworzenie klastra usługi HDInsight przy użyciu usługi Azure Data Lake Storage Gen1 (jako magazyn dodatkowy) za pomocą programu Azure PowerShell

> [!div class="op_single_selector"]
> * [Korzystanie z portalu](data-lake-store-hdinsight-hadoop-use-portal.md)
> * [Przy użyciu programu PowerShell (do magazynu domyślnego)](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
> * [Przy użyciu programu PowerShell (w przypadku dodatkowego magazynu)](data-lake-store-hdinsight-hadoop-use-powershell.md)
> * [Przy użyciu usługi Resource Manager](data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)
>
>

Dowiedz się, jak za pomocą programu Azure PowerShell Konfigurowanie klastra usługi HDInsight przy użyciu usługi Azure Data Lake Storage Gen1 **jako magazynu dodatkowego**. Aby uzyskać instrukcje na temat sposobu Tworzenie klastra HDInsight z usługą Data Lake Storage Gen1 jako magazynem domyślnym, zobacz [Tworzenie klastra HDInsight z usługą Data Lake Storage Gen1 jako magazynem domyślnym](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md).

> [!NOTE]
> Jeśli zamierzasz użyć Data Lake Storage Gen1 jako dodatkowego magazynu dla klastrów HDInsight, zdecydowanie zaleca się to zrobić podczas tworzenia klastra zgodnie z opisem w tym artykule. Dodawanie Data Lake Storage Gen1 jako dodatkowego magazynu do istniejącego HDInsight klastra jest skomplikowane i podatne na błędy.
>

W przypadku klastra obsługiwanych typów Data Lake Storage Gen1 może służyć jako magazynem domyślnym lub dodatkowe konto magazynu. Gdy Data Lake Storage Gen1 jest używany jako magazyn dodatkowy, domyślne konto magazynu dla klastrów będzie nadal usługi Azure blob Storage (WASB) i domyślny magazyn danych, który chcesz nadal są zapisywane pliki dotyczące klastra (na przykład dzienników itp.) proces mogą być przechowywane na koncie usługi Data Lake Storage Gen1. Za pomocą programu Data Lake Storage Gen1 jako dodatkowe konto magazynu nie ma wpływu na wydajność ani możliwość odczytu/zapisu do magazynu z klastra.

## <a name="using-data-lake-storage-gen1-for-hdinsight-cluster-storage"></a>Za pomocą programu Data Lake Storage Gen1 dla magazynu klastra HDInsight

Poniżej przedstawiono kilka istotnych kwestii dotyczących HDInsight przy użyciu Data Lake Storage Gen1:

* Opcja umożliwiająca tworzenie klastrów HDInsight z dostępem do programu Data Lake Storage Gen1, jako magazyn dodatkowy jest dostępny dla HDInsight w wersji 3.2, 3.4, 3.5 i 3.6.

Konfigurowanie HDInsight do pracy z Data Lake Storage Gen1 przy użyciu programu PowerShell obejmuje następujące czynności:

* Tworzenie konta Data Lake Storage Gen1
* Konfigurowanie uwierzytelniania opartego na rolach dostęp do programu Data Lake Storage Gen1
* Tworzenie klastra HDInsight uwierzytelniania w usłudze Data Lake Storage Gen1
* Uruchom zadanie testowe w klastrze

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Przed przystąpieniem do wykonania kroków opisanych w tym samouczku należy dysponować następującymi elementami:

* **Subskrypcja platformy Azure**. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Program Azure PowerShell 1.0 lub nowszy**. Zobacz artykuł [Instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azure/overview).
* **Windows SDK**. Możesz je zainstalować, klikając [tutaj](https://dev.windows.com/en-us/downloads). Możesz użyć tego można utworzyć certyfikatu zabezpieczeń.
* **Azure Active Directory Service Principal**. Kroki opisane w tym samouczku zawierają instrukcje dotyczące sposobu tworzenia jednostki usługi w usłudze Azure AD. Jednakże musi być administratorem usługi Azure AD, aby można było utworzyć nazwę główną usługi. Jeśli jesteś administratorem usługi Azure AD, można pominąć to wymaganie wstępne, a następnie przejść do samouczka.

    **Jeśli nie jesteś administratorem usługi Azure AD**, nie będziesz w stanie wykonać kroki wymagane do utworzenia jednostki usługi. W takim przypadku administrator usługi Azure AD należy najpierw utworzyć nazwę główną usługi można było utworzyć klaster usługi HDInsight za pomocą programu Data Lake Storage Gen1. Także nazwy głównej usługi muszą zostać utworzone za pomocą certyfikatu, zgodnie z opisem w [utworzyć nazwę główną usługi za pomocą certyfikatu](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-certificate-from-certificate-authority).

## <a name="create-a-data-lake-storage-gen1-account"></a>Tworzenie konta Data Lake Storage Gen1
Wykonaj następujące kroki, aby utworzyć konto Data Lake Storage Gen1.

1. Na pulpicie otwórz nowe okno programu Azure PowerShell, a następnie wprowadź poniższy fragment kodu. Po wyświetleniu monitu, aby zalogować się, upewnij się, że logujesz się jako jeden z administratora/właściciela subskrypcji:

        # Log in to your Azure account
        Connect-AzAccount

        # List all the subscriptions associated to your account
        Get-AzSubscription

        # Select a subscription
        Set-AzContext -SubscriptionId <subscription ID>

        # Register for Data Lake Storage Gen1
        Register-AzResourceProvider -ProviderNamespace "Microsoft.DataLakeStore"

   > [!NOTE]
   > Jeśli otrzymasz komunikat o błędzie podobny do `Register-AzResourceProvider : InvalidResourceNamespace: The resource namespace 'Microsoft.DataLakeStore' is invalid` podczas rejestrowania dostawcy zasobów Data Lake Storage Gen1, jest możliwe, że Twoja subskrypcja nie jest na liście dozwolonych elementów dla programu Data Lake Storage Gen1. Upewnij się, Włącz Twojej subskrypcji platformy Azure Data Lake Storage Gen1 wykonując te [instrukcje](data-lake-store-get-started-portal.md).
   >
   >
2. Konta Data Lake Storage Gen1 jest skojarzona z grupą zasobów platformy Azure. Rozpocznij od utworzenia grupy zasobów platformy Azure.

        $resourceGroupName = "<your new resource group name>"
        New-AzResourceGroup -Name $resourceGroupName -Location "East US 2"

    Powinny pojawić się dane wyjściowe podobne do tego:

        ResourceGroupName : hdiadlgrp
        Location          : eastus2
        ProvisioningState : Succeeded
        Tags              :
        ResourceId        : /subscriptions/<subscription-id>/resourceGroups/hdiadlgrp

3. Tworzenie konta Data Lake Storage Gen1. Nazwa konta, które określisz musi zawierać tylko małe litery i cyfry.

        $dataLakeStorageGen1Name = "<your new Data Lake Storage Gen1 account name>"
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

5. Przekaż przykładowe dane do programu Data Lake Storage Gen1. Użyjemy w dalszej części tego artykułu, aby sprawdzić, czy dane są dostępne z klastra usługi HDInsight. Jeśli szukasz przykładowych danych do przekazania, możesz pobrać folder **Ambulance Data** z [repozytorium Git usługi Azure Data Lake](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData).

        $myrootdir = "/"
        Import-AzDataLakeStoreItem -AccountName $dataLakeStorageGen1Name -Path "C:\<path to data>\vehicle1_09142014.csv" -Destination $myrootdir\vehicle1_09142014.csv


## <a name="set-up-authentication-for-role-based-access-to-data-lake-storage-gen1"></a>Konfigurowanie uwierzytelniania opartego na rolach dostęp do programu Data Lake Storage Gen1

Każda subskrypcja platformy Azure jest skojarzony z usługą Azure Active Directory. Za pomocą tej usługi Azure Active Directory należy najpierw uwierzytelniania użytkowników i usług, wymagające dostępu do zasobów w subskrypcji przy użyciu witryny Azure portal lub interfejsu API usługi Azure Resource Manager. Udzielić dostępu do subskrypcji platformy Azure i usług, przypisując im odpowiednią rolę w obrębie zasobu platformy Azure.  W przypadku usług, nazwy głównej usługi określa usługi w usłudze Azure Active Directory (AAD). W tej sekcji pokazano, jak udzielić usługi aplikacji, takich jak HDInsight, dostęp do zasobów platformy Azure (utworzone wcześniej konta Data Lake Storage Gen1) przez utworzenie nazwy głównej usługi dla aplikacji i przypisywanie ról do tego za pomocą programu Azure PowerShell.

Aby skonfigurować uwierzytelnianie usługi Active Directory dla programu Data Lake Storage Gen1, należy wykonać poniższe zadania.

* Tworzenie certyfikatu z podpisem własnym
* Tworzenie aplikacji w usłudze Azure Active Directory i jednostki usługi

### <a name="create-a-self-signed-certificate"></a>Tworzenie certyfikatu z podpisem własnym

Upewnij się, że masz [zestawu Windows SDK](https://dev.windows.com/en-us/downloads) zainstalowane przed wykonaniem kroków w tej sekcji. Należy także utworzyć katalogu, takie jak **C:\mycertdir**, w której zostanie utworzony certyfikat.

1. W oknie programu PowerShell, przejdź do lokalizacji, w której zainstalowany zestaw Windows SDK (zazwyczaj `C:\Program Files (x86)\Windows Kits\10\bin\x86` i użyj [MakeCert] [ makecert] do utworzenia certyfikatu z podpisem własnym i klucz prywatny. Użyj następujących poleceń.

        $certificateFileDir = "<my certificate directory>"
        cd $certificateFileDir

        makecert -sv mykey.pvk -n "cn=HDI-ADL-SP" CertFile.cer -r -len 2048

    Zostanie wyświetlony monit wpisz hasło klucza prywatnego. Po polecenie zostało wykonane pomyślnie, powinien zostać wyświetlony **CertFile.cer** i **mykey.pvk** w katalogu certyfikat określony.
2. Użyj [Pvk2Pfx] [ pvk2pfx] narzędzie do konwersji plików .pvk i cer, utworzone przez narzędzie MakeCert do pliku .pfx. Uruchom następujące polecenie.

        pvk2pfx -pvk mykey.pvk -spc CertFile.cer -pfx CertFile.pfx -po <password>

    Po wyświetleniu monitu wprowadź określone wcześniej hasło klucza prywatnego. Wartość określona dla **— po** parametr jest hasło, które jest skojarzone z plikiem pfx. Po pomyślnym wykonaniu polecenia powinien być też widoczny CertFile.pfx w katalogu certyfikat, który określiłeś.

### <a name="create-an-azure-active-directory-and-a-service-principal"></a>Tworzenie usługi Azure Active Directory i jednostki usługi

W tej sekcji należy wykonać kroki, aby utworzyć jednostkę usługi Azure Active Directory aplikacji usługi, przypisywanie roli do jednostki usługi i Uwierzytelnij się jako nazwa główna usługi, zapewniając certyfikatu. Uruchom następujące polecenia, aby utworzyć aplikację w usłudze Azure Active Directory.

1. Wklej następujące polecenia cmdlet w oknie konsoli programu PowerShell. Upewnij się, że wartość określona dla **- DisplayName** właściwość jest unikatowa. Ponadto wartości **— strona główna** i **- IdentiferUris** są wartości symboli zastępczych i nie są weryfikowane.

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
3. Udzielanie dostępu do jednostki usługi Data Lake Storage Gen1 folderu i pliku, który będzie miał dostęp z klastra HDInsight. Poniższy fragment kodu umożliwia dostęp do głównego konta Data Lake Storage Gen1 (której skopiowany przykładowy plik danych) i sam plik.

        Set-AzDataLakeStoreItemAclEntry -AccountName $dataLakeStorageGen1Name -Path / -AceType User -Id $objectId -Permissions All
        Set-AzDataLakeStoreItemAclEntry -AccountName $dataLakeStorageGen1Name -Path /vehicle1_09142014.csv -AceType User -Id $objectId -Permissions All

## <a name="create-an-hdinsight-linux-cluster-with-data-lake-storage-gen1-as-additional-storage"></a>Utwórz klaster usługi HDInsight w systemie Linux za pomocą programu Data Lake Storage Gen1 jako dodatkowego miejsca do magazynowania

W tej sekcji utworzymy klastra usługi HDInsight Hadoop Linux za pomocą programu Data Lake Storage Gen1 jako dodatkowego magazynu. W tej wersji klastra HDInsight i Data Lake Storage Gen1 konta muszą być w tej samej lokalizacji.

1. Zaczynać się podczas pobierania identyfikatora subskrypcji dzierżawcy. Który będzie potrzebny później.

        $tenantID = (Get-AzContext).Tenant.TenantId
2. W tej wersji w przypadku klastra Hadoop Data Lake Storage Gen1 należy używać tylko jako dodatkowy magazyn dla klastra. Domyślny magazyn będą nadal podlegać obiektów blob usługi Azure storage (WASB). Dlatego najpierw utworzymy konto magazynu i kontenery magazynu wymaganych dla klastra.

        # Create an Azure storage account
        $location = "East US 2"
        $storageAccountName = "<StorageAccountName>"   # Provide a Storage account name

        New-AzStorageAccount -ResourceGroupName $resourceGroupName -StorageAccountName $storageAccountName -Location $location -Type Standard_GRS

        # Create an Azure Blob Storage container
        $containerName = "<ContainerName>"              # Provide a container name
        $storageAccountKey = (Get-AzStorageAccountKey -Name $storageAccountName -ResourceGroupName $resourceGroupName)[0].Value
        $destContext = New-AzStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
        New-AzStorageContainer -Name $containerName -Context $destContext
3. Tworzenie klastra HDInsight. Użyj następujących poleceń cmdlet.

        # Set these variables
        $clusterName = $containerName                   # As a best practice, have the same name for the cluster and container
        $clusterNodes = <ClusterSizeInNodes>            # The number of nodes in the HDInsight cluster
        $httpCredentials = Get-Credential
        $sshCredentials = Get-Credential

        New-AzHDInsightCluster -ClusterName $clusterName -ResourceGroupName $resourceGroupName -HttpCredential $httpCredentials -Location $location -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" -DefaultStorageAccountKey $storageAccountKey -DefaultStorageContainer $containerName  -ClusterSizeInNodes $clusterNodes -ClusterType Hadoop -Version "3.4" -OSType Linux -SshCredential $sshCredentials -ObjectID $objectId -AadTenantId $tenantID -CertificateFilePath $certificateFilePath -CertificatePassword $password

    Po pomyślnym ukończeniu polecenia cmdlet powinny pojawić się dane wyjściowe, wyświetlanie szczegółów klastra.


## <a name="run-test-jobs-on-the-hdinsight-cluster-to-use-the-data-lake-storage-gen1-account"></a>Uruchamianie testów zadań w klastrze HDInsight, aby użyć konta Data Lake Storage Gen1
Po skonfigurowaniu klastra usługi HDInsight, zadania testowe można uruchomić w klastrze, aby sprawdzić, czy klaster HDInsight mogą uzyskiwać dostęp do programu Data Lake Storage Gen1. Aby to zrobić, dokonamy przykładowe zadania Hive, która tworzy tabelę przy użyciu przykładowych danych, który został wcześniej przekazany do konta usługi Data Lake Storage Gen1.

W tej sekcji zostanie SSH w klastrze HDInsight w systemie Linux, możesz utworzyć i uruchomić przykładowe zapytanie programu Hive.

* Jeśli używasz klienta Windows SSH do klastra, zobacz [używanie protokołu SSH z opartą na systemie Linux z platformą Hadoop w HDInsight z Windows](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md).
* Jeśli używasz systemu Linux klienta SSH do klastra, zobacz [używanie protokołu SSH z opartą na systemie Linux platformą Hadoop w HDInsight z systemu Linux](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md)

1. Po nawiązaniu połączenia, należy uruchomić interfejs wiersza polecenia programu Hive przy użyciu następującego polecenia:

        hive
2. Przy użyciu interfejsu wiersza polecenia, wprowadź poniższe instrukcje, aby utworzyć nową tabelę o nazwie **pojazdów** przy użyciu przykładowych danych w Data Lake Storage Gen1:

        DROP TABLE vehicles;
        CREATE EXTERNAL TABLE vehicles (str string) LOCATION 'adl://<mydatalakestoragegen1>.azuredatalakestore.net:443/';
        SELECT * FROM vehicles LIMIT 10;

    Powinny pojawić się dane wyjściowe podobne do następujących:

        1,1,2014-09-14 00:00:03,46.81006,-92.08174,51,S,1
        1,2,2014-09-14 00:00:06,46.81006,-92.08174,13,NE,1
        1,3,2014-09-14 00:00:09,46.81006,-92.08174,48,NE,1
        1,4,2014-09-14 00:00:12,46.81006,-92.08174,30,W,1
        1,5,2014-09-14 00:00:15,46.81006,-92.08174,47,S,1
        1,6,2014-09-14 00:00:18,46.81006,-92.08174,9,S,1
        1,7,2014-09-14 00:00:21,46.81006,-92.08174,53,N,1
        1,8,2014-09-14 00:00:24,46.81006,-92.08174,63,SW,1
        1,9,2014-09-14 00:00:27,46.81006,-92.08174,4,NE,1
        1,10,2014-09-14 00:00:30,46.81006,-92.08174,31,N,1

## <a name="access-data-lake-storage-gen1-using-hdfs-commands"></a>Data Lake Storage Gen1 dostęp przy użyciu poleceń systemu plików HDFS
Po skonfigurowaniu klastra HDInsight w Data Lake Storage Gen1 służy poleceń powłoki systemu plików HDFS dostępu do sklepu.

W tej sekcji wykonasz SSH w klastrze HDInsight w systemie Linux, możesz utworzyć i uruchomić poleceń systemu plików HDFS.

* Jeśli używasz klienta Windows SSH do klastra, zobacz [używanie protokołu SSH z opartą na systemie Linux z platformą Hadoop w HDInsight z Windows](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md).
* Jeśli używasz systemu Linux klienta SSH do klastra, zobacz [używanie protokołu SSH z opartą na systemie Linux platformą Hadoop w HDInsight z systemu Linux](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md)

Po nawiązaniu połączenia użyj następującego polecenia systemu plików HDFS, aby wyświetlić listę plików w ramach konta Data Lake Storage Gen1.

    hdfs dfs -ls adl://<Data Lake Storage Gen1 account name>.azuredatalakestore.net:443/

Powinny pojawić się plik który został wcześniej przekazany do Data Lake Storage Gen1.

    15/09/17 21:41:15 INFO web.CaboWebHdfsFileSystem: Replacing original urlConnectionFactory with org.apache.hadoop.hdfs.web.URLConnectionFactory@21a728d6
    Found 1 items
    -rwxrwxrwx   0 NotSupportYet NotSupportYet     671388 2015-09-16 22:16 adl://mydatalakestoragegen1.azuredatalakestore.net:443/mynewfolder

Można również użyć `hdfs dfs -put` polecenie, aby przekazać pliki do Data Lake Storage Gen1, a następnie użyj `hdfs dfs -ls` Aby sprawdzić, czy pliki zostały pomyślnie przekazane.

## <a name="see-also"></a>Zobacz też
* [Za pomocą usług Data Lake Storage Gen1 klastrów Azure HDInsight](../hdinsight/hdinsight-hadoop-use-data-lake-store.md)
* [Portal: Tworzenie klastra usługi HDInsight, aby użyć Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)

[makecert]: https://msdn.microsoft.com/library/windows/desktop/ff548309(v=vs.85).aspx
[pvk2pfx]: https://msdn.microsoft.com/library/windows/desktop/ff550672(v=vs.85).aspx
