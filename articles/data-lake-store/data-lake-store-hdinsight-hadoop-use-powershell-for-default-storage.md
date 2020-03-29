---
title: Tworzenie klastrów usługi HDInsight przy użyciu usługi Azure Data Lake Storage Gen1 jako magazynu domyślnego przy użyciu programu PowerShell | Dokumenty firmy Microsoft
description: Tworzenie klastrów usługi HDInsight za pomocą usługi Azure Data Lake Storage Gen1 za pomocą programu Azure PowerShell
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "66161410"
---
# <a name="create-hdinsight-clusters-with-azure-data-lake-storage-gen1-as-default-storage-by-using-powershell"></a>Tworzenie klastrów usługi HDInsight przy użyciu usługi Azure Data Lake Storage Gen1 jako magazynu domyślnego przy użyciu programu PowerShell

> [!div class="op_single_selector"]
> * [Korzystanie z witryny Azure Portal](data-lake-store-hdinsight-hadoop-use-portal.md)
> * [Używanie programu PowerShell (do magazynu domyślnego)](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
> * [Użyj programu PowerShell (do dodatkowego magazynu)](data-lake-store-hdinsight-hadoop-use-powershell.md)
> * [Korzystanie z Menedżera zasobów](data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)

Dowiedz się, jak skonfigurować klastry usługi Azure HDInsight za pomocą usługi Azure Data Lake Storage Gen1 jako magazynu domyślnego. Aby uzyskać instrukcje dotyczące tworzenia klastra HDInsight z pamięcią masową Data Lake Storage Gen1 jako dodatkowego magazynu, zobacz [Tworzenie klastra HDInsight z pamięcią masową Data Lake Storage Gen1 jako dodatkowego magazynu.](data-lake-store-hdinsight-hadoop-use-powershell.md)

Oto kilka ważnych zagadnień dotyczących korzystania z usługi HDInsight z pamięcią masową Data Lake Gen1:

* Opcja tworzenia klastrów HDInsight z dostępem do usługi Data Lake Storage Gen1 jako domyślnego magazynu jest dostępna dla hdinsight w wersji 3.5 i 3.6.

* Opcja tworzenia klastrów HDInsight z dostępem do magazynu usługi Data Lake Gen1 jako domyślnego magazynu nie jest *dostępna* dla klastrów HDInsight Premium.

Aby skonfigurować program HDInsight do pracy z programem Data Lake Storage Gen1 przy użyciu programu PowerShell, postępuj zgodnie z instrukcjami w następnych pięciu sekcjach.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Przed rozpoczęciem tego samouczka upewnij się, że spełniasz następujące wymagania:

* **Subskrypcja platformy Azure:** Przejdź do [bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Program Azure PowerShell 1.0 lub większy:** Zobacz [Jak zainstalować i skonfigurować program PowerShell](/powershell/azure/overview).
* **Zestaw Windows Software Development Kit (SDK)**: Aby zainstalować zestaw Windows SDK, przejdź do [pozycji Pliki do pobrania i narzędzia dla systemu Windows 10](https://dev.windows.com/downloads). SDK służy do tworzenia certyfikatu zabezpieczeń.
* **Jednostki usługi Azure Active Directory:** W tym samouczku opisano sposób tworzenia jednostki usługi w usłudze Azure Active Directory (Azure AD). Jednak aby utworzyć jednostkę usługi, musisz być administratorem usługi Azure AD. Jeśli jesteś administratorem, możesz pominąć ten warunek wstępny i kontynuować samouczek.

    >[!NOTE]
    >Jednostkę usługi można utworzyć tylko wtedy, gdy jesteś administratorem usługi Azure AD. Administrator usługi Azure AD musi utworzyć jednostkę usługi, zanim będzie można utworzyć klaster HDInsight z usługą Data Lake Storage Gen1. Podmiot usługi musi zostać utworzony przy za pomocą certyfikatu, zgodnie z opisem w [pozycję Utwórz jednostkę usługi z certyfikatem.](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-certificate-from-certificate-authority)
    >

## <a name="create-a-data-lake-storage-gen1-account"></a>Tworzenie konta usługi Data Lake Storage Gen1

Aby utworzyć konto Data Lake Storage Gen1, wykonaj następujące czynności:

1. Na pulpicie otwórz okno programu PowerShell, a następnie wprowadź poniższe fragmenty kodu. Po wyświetleniu monitu o zalogowanie się zaloguj się jako jeden z administratorów lub właścicieli subskrypcji. 

        # Sign in to your Azure account
        Connect-AzAccount

        # List all the subscriptions associated to your account
        Get-AzSubscription

        # Select a subscription
        Set-AzContext -SubscriptionId <subscription ID>

        # Register for Data Lake Storage Gen1
        Register-AzResourceProvider -ProviderNamespace "Microsoft.DataLakeStore"

    > [!NOTE]
    > Jeśli zarejestrujesz dostawcę zasobów Data Lake Storage `Register-AzResourceProvider : InvalidResourceNamespace: The resource namespace 'Microsoft.DataLakeStore' is invalid`Gen1 i pojawi się błąd podobny do , twoja subskrypcja może nie być na białej liście dla usługi Data Lake Storage Gen1. Aby włączyć subskrypcję platformy Azure dla usługi Data Lake Storage Gen1, postępuj zgodnie z instrukcjami w [wprowadzenie do usługi Azure Data Lake Storage Gen1 przy użyciu witryny Azure portal.](data-lake-store-get-started-portal.md)
    >

2. Konto usługi Data Lake Storage Gen1 jest skojarzone z grupą zasobów platformy Azure. Zacznij od utworzenia grupy zasobów.

        $resourceGroupName = "<your new resource group name>"
        New-AzResourceGroup -Name $resourceGroupName -Location "East US 2"

    Powinny pojawić się następujące dane wyjściowe:

        ResourceGroupName : hdiadlgrp
        Location          : eastus2
        ProvisioningState : Succeeded
        Tags              :
        ResourceId        : /subscriptions/<subscription-id>/resourceGroups/hdiadlgrp

3. Utwórz konto Gen1 magazynu usługi Data Lake. Określona nazwa konta musi zawierać tylko małe litery i cyfry.

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

4. Użycie magazynu usługi Data Lake Storage Gen1 jako domyślnego magazynu wymaga określenia ścieżki głównej, do której pliki specyficzne dla klastra są kopiowane podczas tworzenia klastra. Aby utworzyć ścieżkę główną, która jest **/clusters/hdiadlcluster** we urywce, użyj następujących poleceń cmdlet:

        $myrootdir = "/"
        New-AzDataLakeStoreItem -Folder -AccountName $dataLakeStorageGen1Name -Path $myrootdir/clusters/hdiadlcluster


## <a name="set-up-authentication-for-role-based-access-to-data-lake-storage-gen1"></a>Konfigurowanie uwierzytelniania dla dostępu opartego na rolach do usługi Data Lake Storage Gen1
Każda subskrypcja platformy Azure jest skojarzona z jednostką usługi Azure AD. Użytkownicy i usługi, które uzyskują dostęp do zasobów subskrypcji przy użyciu witryny Azure portal lub interfejsu API usługi Azure Resource Manager, muszą najpierw uwierzytelnić się za pomocą usługi Azure AD. Dostęp jest przyznawany do subskrypcji platformy Azure i usług, przypisując im odpowiednią rolę w zasobie platformy Azure. W przypadku usług podmiotu usługi identyfikuje usługę w usłudze Azure AD.

W tej sekcji pokazano, jak udzielić usługi aplikacji, takich jak HDInsight, dostęp do zasobu platformy Azure (konto usługi Data Lake Storage Gen1, które zostały utworzone wcześniej). Należy to zrobić, tworząc jednostkę usługi dla aplikacji i przypisując role do niego za pośrednictwem programu PowerShell.

Aby skonfigurować uwierzytelnianie usługi Active Directory dla usługi Data Lake Storage Gen1, wykonaj zadania w następujących dwóch sekcjach.

### <a name="create-a-self-signed-certificate"></a>Tworzenie certyfikatu z podpisem własnym
Upewnij się, że masz zainstalowany [pakiet Windows SDK](https://dev.windows.com/en-us/downloads) przed przystąpieniem do czynności w tej sekcji. Musisz również utworzyć katalog, taki jak *C:\mycertdir*, w którym utworzono certyfikat.

1. W oknie programu PowerShell przejdź do lokalizacji, w której zainstalowano zestaw Windows SDK (zazwyczaj *C:\Program Files (x86)\Windows Kits\10\bin\x86*) i użyj narzędzia [MakeCert,][makecert] aby utworzyć certyfikat z podpisem własnym i klucz prywatny. Użyj następujących poleceń:

        $certificateFileDir = "<my certificate directory>"
        cd $certificateFileDir

        makecert -sv mykey.pvk -n "cn=HDI-ADL-SP" CertFile.cer -r -len 2048

    Zostanie wyświetlony monit o wprowadzenie hasła klucza prywatnego. Po pomyślnym wykonaniu polecenia w określonym katalogu certyfikatów powinien zostać wyświetlony plik **CertFile.cer** i **mykey.pvk.**
2. Użyj narzędzia [Pvk2Pfx,][pvk2pfx] aby przekonwertować pliki .pvk i .cer utworzone przez MakeCert na plik .pfx. Uruchom następujące polecenie:

        pvk2pfx -pvk mykey.pvk -spc CertFile.cer -pfx CertFile.pfx -po <password>

    Po wyświetleniu monitu wprowadź hasło klucza prywatnego określone wcześniej. Wartość określona dla parametru **-po** jest hasłem skojarzonym z plikiem .pfx. Po pomyślnym zakończeniu polecenia w określonym katalogu certyfikatów powinien zostać wyświetlony **plik CertFile.pfx.**

### <a name="create-an-azure-ad-and-a-service-principal"></a>Tworzenie usługi Azure AD i jednostki usługi
W tej sekcji utworzysz jednostkę usługi dla aplikacji usługi Azure AD, przypisz rolę do jednostki usługi i uwierzytelnij jako jednostkę usługi, zapewniając certyfikat. Aby utworzyć aplikację w usłudze Azure AD, uruchom następujące polecenia:

1. Wklej następujące polecenia cmdlet w oknie konsoli programu PowerShell. Upewnij się, że wartość określona dla właściwości **-DisplayName** jest unikatowa. Wartości **-HomePage** i **-IdentiferUris** są wartościami zastępczymi i nie są weryfikowane.

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
2. Utwórz jednostkę usługi przy użyciu identyfikatora aplikacji.

        $servicePrincipal = New-AzADServicePrincipal -ApplicationId $applicationId

        $objectId = $servicePrincipal.Id
3. Udziel jednostki usługi dostępu do katalogu głównego magazynu usługi Data Lake Gen1 i wszystkich folderów w ścieżce głównej, która została określona wcześniej. Użyj następujących poleceń cmdlet:

        Set-AzDataLakeStoreItemAclEntry -AccountName $dataLakeStorageGen1Name -Path / -AceType User -Id $objectId -Permissions All
        Set-AzDataLakeStoreItemAclEntry -AccountName $dataLakeStorageGen1Name -Path /clusters -AceType User -Id $objectId -Permissions All
        Set-AzDataLakeStoreItemAclEntry -AccountName $dataLakeStorageGen1Name -Path /clusters/hdiadlcluster -AceType User -Id $objectId -Permissions All

## <a name="create-an-hdinsight-linux-cluster-with-data-lake-storage-gen1-as-the-default-storage"></a>Tworzenie klastra systemu LINUX HDInsight z pamięcią Data Lake Storage Gen1 jako domyślną pamięcią masową

W tej sekcji utworzysz klaster SYSTEMU LINUX HDInsight Hadoop z pamięcią Gen1 magazynu usługi Data Lake jako domyślny magazyn. W tej wersji klaster HDInsight i data lake storage gen1 musi znajdować się w tej samej lokalizacji.

1. Pobierz identyfikator dzierżawy subskrypcji i przechowuj go do późniejszego użycia.

        $tenantID = (Get-AzContext).Tenant.TenantId

2. Utwórz klaster HDInsight przy użyciu następujących poleceń cmdlet:

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

    Po pomyślnym zakończeniu polecenia cmdlet powinien zostać wyświetlony dane wyjściowe z listą szczegółów klastra.

## <a name="run-test-jobs-on-the-hdinsight-cluster-to-use-data-lake-storage-gen1"></a>Uruchamianie zadań testowych w klastrze HDInsight w celu użycia usługi Data Lake Storage Gen1
Po skonfigurowaniu klastra HDInsight można uruchomić zadania testowe na nim, aby upewnić się, że może uzyskać dostęp do magazynu usługi Data Lake Gen1. Aby to zrobić, uruchom przykładowe zadanie gałęzi, aby utworzyć tabelę, która używa przykładowych danych, które są już dostępne w urozwoju danych Lake Storage Gen1 w * \<katalogu głównym klastra>/example/data/sample.log*.

W tej sekcji należy nawiązać połączenie Secure Shell (SSH) do utworzonego klastra SYSTEMU HDInsight Linux, a następnie uruchomić przykładową kwerendę hive.

* Jeśli używasz klienta systemu Windows do nawiązywać połączenie SSH do klastra, zobacz [Używanie funkcji SSH z systemem Linux w systemie HDInsight z systemu Windows](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md).
* Jeśli używasz klienta Systemu Linux do nawiązywać połączenie SSH do klastra, zobacz [Używanie SSH z systemem Linux Hadoop na HDInsight z Linuksa](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).

1. Po nawiązaniu połączenia uruchom interfejs wiersza polecenia hive (CLI) za pomocą następującego polecenia:

        hive
2. Użyj interfejsu wiersza polecenia, aby wprowadzić następujące instrukcje, aby utworzyć nową tabelę o nazwie **pojazdy** przy użyciu przykładowych danych w umiaźniach magazynu usługi Data Lake:

        DROP TABLE log4jLogs;
        CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
        STORED AS TEXTFILE LOCATION 'adl:///example/data/';
        SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;

    Dane wyjściowe kwerendy powinny być widoczne na konsoli SSH.

    >[!NOTE]
    >Ścieżka do przykładowych danych w poprzednim poleceniu CREATE TABLE znajduje `adl:///example/data/`się w obszarze , w którym `adl:///` znajduje się katalog główny klastra. Na przykład z katalogu głównego klastra, który jest `adl://hdiadlstore.azuredatalakestore.net/clusters/hdiadlcluster`określony w tym samouczku, polecenie jest . Można użyć krótszej alternatywy lub podać pełną ścieżkę do katalogu głównego klastra.
    >

## <a name="access-data-lake-storage-gen1-by-using-hdfs-commands"></a>Dostęp do pamięci masowej usługi Data Lake Gen1 przy użyciu poleceń HDFS
Po skonfigurowaniu klastra HDInsight do używania pamięci masowej usługi Data Lake Storage Gen1 można użyć poleceń powłoki Hadoop Distributed File System (HDFS), aby uzyskać dostęp do magazynu.

W tej sekcji nawiązasz połączenie SSH do utworzonego klastra SYSTEMU HDInsight Linux, a następnie uruchomisz polecenia HDFS.

* Jeśli używasz klienta systemu Windows do nawiązywać połączenie SSH do klastra, zobacz [Używanie funkcji SSH z systemem Linux w systemie HDInsight z systemu Windows](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md).
* Jeśli używasz klienta Systemu Linux do nawiązywać połączenie SSH do klastra, zobacz [Używanie SSH z systemem Linux Hadoop na HDInsight z Linuksa](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).

Po nawiązeniu połączenia wyświetl listę plików w układzie danych Lake Storage Gen1 za pomocą następującego polecenia systemu plików HDFS.

    hdfs dfs -ls adl:///

Można również użyć `hdfs dfs -put` polecenia, aby przekazać niektóre pliki do usługi `hdfs dfs -ls` Data Lake Storage Gen1, a następnie użyć do sprawdzenia, czy pliki zostały pomyślnie przekazane.

## <a name="see-also"></a>Zobacz też
* [Używanie usługi Data Lake Storage Gen1 w klastrach usługi Azure HDInsight](../hdinsight/hdinsight-hadoop-use-data-lake-store.md)
* [Witryna Azure portal: tworzenie klastra usługi HDInsight w celu użycia usługi Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)

[makecert]: https://msdn.microsoft.com/library/windows/desktop/ff548309(v=vs.85).aspx
[pvk2pfx]: https://msdn.microsoft.com/library/windows/desktop/ff550672(v=vs.85).aspx
