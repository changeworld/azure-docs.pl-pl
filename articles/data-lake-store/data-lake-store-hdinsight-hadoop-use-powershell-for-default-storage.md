---
title: Tworzenie klastrów usługi HDInsight z usługą Data Lake Store jako domyślny magazyn przy użyciu programu PowerShell | Dokumentacja firmy Microsoft
description: Tworzenie i korzystanie z usługi Azure Data Lake Store klastrów usługi HDInsight przy użyciu programu Azure PowerShell
services: data-lake-store,hdinsight
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 8917af15-8e37-46cf-87ad-4e6d5d67ecdb
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.date: 11/28/2017
ms.author: nitinme
ms.openlocfilehash: 2a9f2e9c2634173cf2feb9ded2e87e49f9c8d1e4
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
---
# <a name="create-hdinsight-clusters-with-data-lake-store-as-default-storage-by-using-powershell"></a>Tworzenie klastrów usługi HDInsight z usługą Data Lake Store jako domyślny magazyn przy użyciu programu PowerShell

> [!div class="op_single_selector"]
> * [Korzystanie z witryny Azure Portal](data-lake-store-hdinsight-hadoop-use-portal.md)
> * [Przy użyciu programu PowerShell (do magazynu domyślnego)](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
> * [Przy użyciu programu PowerShell (dla dodatkowego magazynu)](data-lake-store-hdinsight-hadoop-use-powershell.md)
> * [Użyj Menedżera zasobów](data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)

Dowiedz się, jak skonfigurować klastry usługi HDInsight Azure z usługi Azure Data Lake Store jako domyślny magazyn przy użyciu programu Azure PowerShell. Aby uzyskać instrukcje dotyczące tworzenia klastra usługi HDInsight z usługą Data Lake Store jako dodatkowego miejsca do magazynowania, zobacz [tworzenia klastra usługi HDInsight z usługą Data Lake Store jako dodatkowego magazynu](data-lake-store-hdinsight-hadoop-use-powershell.md).

Poniżej przedstawiono kilka istotnych kwestii dotyczących z usługą Data Lake Store za pomocą usługi HDInsight:

* Możliwość tworzenia klastrów usługi HDInsight z dostępem do usługi Data Lake Store jako domyślnego magazynu jest dostępna dla usługi HDInsight w wersji 3.5 i 3,6.

* Opcja tworzenia HDInsight klastrów z dostępem do usługi Data Lake Store jako domyślnego magazynu jest *nie jest dostępny* klastrów HDInsight Premium.

Aby skonfigurować HDInsight do pracy z usługą Data Lake Store za pomocą programu PowerShell, postępuj zgodnie z instrukcjami w sekcji następnych pięciu.

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem tego samouczka, upewnij się, czy zostały spełnione następujące wymagania:

* **Subskrypcja platformy Azure**: Przejdź do [Azure Pobierz bezpłatną wersję próbną](https://azure.microsoft.com/pricing/free-trial/).
* **Program Azure PowerShell 1.0 lub nowszego**: zobacz [jak instalowanie i konfigurowanie programu PowerShell](/powershell/azure/overview).
* **Windows Software Development Kit (SDK)**: Aby zainstalować zestaw Windows SDK, przejdź do [pliki do pobrania i narzędzi dla systemu Windows 10](https://dev.windows.com/downloads). Zestaw SDK służy do tworzenia certyfikatu zabezpieczeń.
* **Nazwy głównej usługi Azure Active Directory**: w tym samouczku opisano sposób tworzenia nazwy głównej usługi w usłudze Azure Active Directory (Azure AD). Jednak można utworzyć nazwy głównej usługi, musi być administratorem usługi Azure AD. Jeśli jesteś administratorem, możesz pominąć te wymagania wstępne i kontynuować samouczka.

    >[!NOTE]
    >Usługi można utworzyć podmiot, tylko wtedy, gdy administrator usługi Azure AD. Administrator usługi Azure AD należy utworzyć usługę podmiotu zabezpieczeń przed utworzeniem klastra usługi HDInsight z usługą Data Lake Store. Nazwy głównej usługi należy utworzyć przy użyciu certyfikatu zgodnie z opisem w [Tworzenie nazwy głównej usługi o certyfikat](../azure-resource-manager/resource-group-authenticate-service-principal.md#create-service-principal-with-certificate-from-certificate-authority).
    >

## <a name="create-a-data-lake-store-account"></a>Tworzenie konta usługi Data Lake Store

Aby utworzyć konto usługi Data Lake Store, wykonaj następujące czynności:

1. Na pulpicie otwórz okno programu PowerShell, a następnie wprowadź poniższe fragmenty kodu. Po wyświetleniu monitu zaloguj się, zaloguj się jako jeden z administratorów subskrypcji lub właścicieli. 

        # Sign in to your Azure account
        Connect-AzureRmAccount

        # List all the subscriptions associated to your account
        Get-AzureRmSubscription

        # Select a subscription
        Set-AzureRmContext -SubscriptionId <subscription ID>

        # Register for Data Lake Store
        Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.DataLakeStore"

    > [!NOTE]
    > Jeśli rejestrowanie dostawcy zasobów usługi Data Lake Store i komunikat o błędzie podobny do `Register-AzureRmResourceProvider : InvalidResourceNamespace: The resource namespace 'Microsoft.DataLakeStore' is invalid`, subskrypcja może nie być białej dla usługi Data Lake Store. Aby włączyć Twojej subskrypcji platformy Azure dla publicznej wersji zapoznawczej usługi Data Lake Store, postępuj zgodnie z instrukcjami [wprowadzenie do usługi Azure Data Lake Store za pomocą portalu Azure](data-lake-store-get-started-portal.md).
    >

2. Konto usługi Data Lake Store jest skojarzona z grupą zasobów platformy Azure. Rozpocznij od utworzenia grupy zasobów.

        $resourceGroupName = "<your new resource group name>"
        New-AzureRmResourceGroup -Name $resourceGroupName -Location "East US 2"

    Powinny pojawić się dane wyjściowe podobne to:

        ResourceGroupName : hdiadlgrp
        Location          : eastus2
        ProvisioningState : Succeeded
        Tags              :
        ResourceId        : /subscriptions/<subscription-id>/resourceGroups/hdiadlgrp

3. Utwórz konto usługi Data Lake Store. Nazwa konta, które określisz musi zawierać tylko małe litery i cyfry.

        $dataLakeStoreName = "<your new Data Lake Store name>"
        New-AzureRmDataLakeStoreAccount -ResourceGroupName $resourceGroupName -Name $dataLakeStoreName -Location "East US 2"

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

4. Za pomocą usługi Data Lake Store jako domyślny magazyn wymaga określenia ścieżki katalogu głównego, do którego kopiowania plików specyficznych dla klastra podczas tworzenia klastra. Aby utworzyć ścieżki katalogu głównego, który jest **/klastrów/hdiadlcluster** we fragmencie, użyj następujących poleceń cmdlet:

        $myrootdir = "/"
        New-AzureRmDataLakeStoreItem -Folder -AccountName $dataLakeStoreName -Path $myrootdir/clusters/hdiadlcluster


## <a name="set-up-authentication-for-role-based-access-to-data-lake-store"></a>Konfigurowanie uwierzytelniania opartego na rolach dostępu do usługi Data Lake Store
Każda subskrypcja platformy Azure jest skojarzony z jednostką usługi Azure AD. Użytkowników i usług, które uzyskują dostęp do zasobów subskrypcji przy użyciu portalu Azure lub interfejsu API Azure Resource Manager muszą najpierw uwierzytelnić w usłudze Azure AD. Dostęp do usług i subskrypcji platformy Azure przez przypisywanie ich odpowiedniej roli na zasobów platformy Azure. W przypadku usług nazwy głównej usługi identyfikuje usługę w usłudze Azure AD.

W tej części przedstawiono sposób przyznania usługi aplikacji, takie jak usługi HDInsight, dostęp do zasobów platformy Azure (konta Data Lake Store, który został utworzony wcześniej). Można to zrobić przez utworzenie usługi głównej aplikacji i przypisywanie ról do niego za pośrednictwem programu PowerShell.

Aby skonfigurować uwierzytelnianie usługi Active Directory dla usługi Azure Data Lake, należy wykonać zadania w następujących sekcjach.

### <a name="create-a-self-signed-certificate"></a>Utwórz certyfikat z podpisem własnym
Upewnij się, że masz [zestaw Windows SDK](https://dev.windows.com/en-us/downloads) zainstalowany, przed wykonaniem czynności w tej sekcji. Należy także utworzyć katalogu, takie jak *C:\mycertdir*, w którym można utworzyć certyfikatu.

1. W oknie programu PowerShell przejdź do lokalizacji, w której zainstalowany zestaw Windows SDK (zazwyczaj *\Windows Kits\10\bin\x86 C:\Program Files (x86)*) i używać [MakeCert] [ makecert] narzędzie do utworzenia certyfikatu z podpisem własnym oraz klucza prywatnego. Użyj następujących poleceń:

        $certificateFileDir = "<my certificate directory>"
        cd $certificateFileDir

        makecert -sv mykey.pvk -n "cn=HDI-ADL-SP" CertFile.cer -r -len 2048

    Pojawi się monit o wprowadzenie hasło klucza prywatnego. Po polecenie zostanie wykonane pomyślnie, powinien zostać wyświetlony **CertFile.cer** i **mykey.pvk** w katalogu określonego certyfikatu.
2. Użyj [Pvk2Pfx] [ pvk2pfx] narzędzie do konwersji plików .pvk i .cer, które MakeCert utworzone do pliku .pfx. Uruchom następujące polecenie:

        pvk2pfx -pvk mykey.pvk -spc CertFile.cer -pfx CertFile.pfx -po <password>

    Po wyświetleniu monitu wprowadź określone wcześniej hasło klucza prywatnego. Wartość określona dla **— po** parametr jest hasło skojarzone z pliku pfx. Po polecenie zostało zakończone pomyślnie, powinien również zostać wyświetlony **CertFile.pfx** w katalogu określonego certyfikatu.

### <a name="create-an-azure-ad-and-a-service-principal"></a>Tworzenie usługi Azure AD i nazwy głównej usługi
W tej sekcji Tworzenie nazwy głównej usługi dla aplikacji usługi Azure AD, przypisać rolę do nazwy głównej usługi i Uwierzytelnij się jako nazwy głównej usługi zapewniając certyfikatu. Aby utworzyć aplikację w usłudze Azure AD, uruchom następujące polecenia:

1. Wklej następujące polecenia cmdlet w oknie konsoli programu PowerShell. Upewnij się, że wartości określonej dla **— Nazwa wyświetlana** właściwości jest unikatowa. Wartości **— strona główna** i **- IdentiferUris** są symbole zastępcze i nie są weryfikowane.

        $certificateFilePath = "$certificateFileDir\CertFile.pfx"

        $password = Read-Host -Prompt "Enter the password" # This is the password you specified for the .pfx file

        $certificatePFX = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($certificateFilePath, $password)

        $rawCertificateData = $certificatePFX.GetRawCertData()

        $credential = [System.Convert]::ToBase64String($rawCertificateData)

        $application = New-AzureRmADApplication `
            -DisplayName "HDIADL" `
            -HomePage "https://contoso.com" `
            -IdentifierUris "https://mycontoso.com" `
            -CertValue $credential  `
            -StartDate $certificatePFX.NotBefore  `
            -EndDate $certificatePFX.NotAfter

        $applicationId = $application.ApplicationId
2. Tworzenie nazwy głównej usługi za pomocą identyfikatora aplikacji.

        $servicePrincipal = New-AzureRmADServicePrincipal -ApplicationId $applicationId

        $objectId = $servicePrincipal.Id
3. Udziel dostępu główną usługi do katalogu głównego usługi Data Lake Store i wszystkie foldery w ścieżce katalogu głównego, określony wcześniej. Użyj następujących poleceń cmdlet:

        Set-AzureRmDataLakeStoreItemAclEntry -AccountName $dataLakeStoreName -Path / -AceType User -Id $objectId -Permissions All
        Set-AzureRmDataLakeStoreItemAclEntry -AccountName $dataLakeStoreName -Path /clusters -AceType User -Id $objectId -Permissions All
        Set-AzureRmDataLakeStoreItemAclEntry -AccountName $dataLakeStoreName -Path /clusters/hdiadlcluster -AceType User -Id $objectId -Permissions All

## <a name="create-an-hdinsight-linux-cluster-with-data-lake-store-as-the-default-storage"></a>Tworzenie klastra usługi HDInsight Linux z usługą Data Lake Store jako domyślnego magazynu

W tej sekcji utworzysz klaster usługi HDInsight Hadoop Linux z usługą Data Lake Store jako domyślnego magazynu. W tej wersji klaster usługi HDInsight i Data Lake — Magazyn musi być w tej samej lokalizacji.

1. Pobierz identyfikator subskrypcji dzierżawcy i zapisać go do użycia w przyszłości.

        $tenantID = (Get-AzureRmContext).Tenant.TenantId

2. Tworzenie klastra usługi HDInsight przy użyciu następujących poleceń cmdlet:

        # Set these variables

        $location = "East US 2"
        $storageAccountName = $dataLakeStoreName                       # Data Lake Store account name
        $storageRootPath = "<Storage root path you specified earlier>" # E.g. /clusters/hdiadlcluster
        $clusterName = "<unique cluster name>"
        $clusterNodes = <ClusterSizeInNodes>            # The number of nodes in the HDInsight cluster
        $httpCredentials = Get-Credential
        $sshCredentials = Get-Credential

        New-AzureRmHDInsightCluster `
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

    Po pomyślnym zakończeniu polecenia cmdlet powinny być widoczne dane wyjściowe, która wyświetla szczegóły klastra.

## <a name="run-test-jobs-on-the-hdinsight-cluster-to-use-data-lake-store"></a>Uruchom zadania testowe w klastrze usługi HDInsight do użycia usługi Data Lake Store
Po skonfigurowaniu klastra usługi HDInsight można uruchomić zadania testowe w celu zapewnienia, że można uzyskać dostępu do usługi Data Lake Store. Aby to zrobić, uruchom przykładowe zadania Hive, aby utworzyć tabelę, która używa przykładowych danych, który jest już dostępne w usłudze Data Lake Store w  *<cluster root>/example/data/sample.log*.

W tej sekcji należy utworzyć połączenie Secure Shell (SSH) do utworzonego klastra usługi HDInsight w systemie Linux, a następnie uruchom przykładowe zapytanie Hive.

* Jeśli używasz klienta systemu Windows do nawiązania połączenia SSH do klastra, zobacz [używanie SSH z opartą na systemie Linux platformą Hadoop w usłudze HDInsight z systemu Windows](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md).
* Jeśli połączenie SSH w klastrze za pomocą klienta systemu Linux, zobacz [używanie SSH z opartą na systemie Linux platformą Hadoop w usłudze HDInsight w systemie Linux](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).

1. Po dokonaniu połączenie, uruchomić Hive interfejsu wiersza polecenia (CLI) za pomocą następującego polecenia:

        hive
2. Użyj interfejsu wiersza polecenia, aby wprowadzić poniższe instrukcje, aby utworzyć nową tabelę o nazwie **pojazdów** przy użyciu przykładowych danych w usłudze Data Lake Store:

        DROP TABLE log4jLogs;
        CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
        STORED AS TEXTFILE LOCATION 'adl:///example/data/';
        SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;

    Wynik kwerendy powinna zostać wyświetlona w konsoli programu SSH.

    >[!NOTE]
    >Ścieżka do przykładowych danych w poprzednim poleceniu CREATE TABLE jest `adl:///example/data/`, gdzie `adl:///` jest głównym klastra. Poniższy przykład głównego klastra, określonego w tym samouczku, to polecenie jest `adl://hdiadlstore.azuredatalakestore.net/clusters/hdiadlcluster`. Możesz użyć alternatywnej krótszy lub podaj pełną ścieżkę do katalogu głównego klastra.
    >

## <a name="access-data-lake-store-by-using-hdfs-commands"></a>Dostęp do usługi Data Lake Store za pomocą poleceń systemu plików HDFS
Po skonfigurowaniu klastra usługi HDInsight do użycia usługi Data Lake Store możesz używać poleceń powłoki systemu Distributed plików Hadoop (HDFS), dostęp do sklepu z.

W tej sekcji należy połączenie SSH do utworzonego klastra usługi HDInsight w systemie Linux, a następnie uruchom polecenia systemu plików HDFS.

* Jeśli używasz klienta systemu Windows do nawiązania połączenia SSH do klastra, zobacz [używanie SSH z opartą na systemie Linux platformą Hadoop w usłudze HDInsight z systemu Windows](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md).
* Jeśli połączenie SSH w klastrze za pomocą klienta systemu Linux, zobacz [używanie SSH z opartą na systemie Linux platformą Hadoop w usłudze HDInsight w systemie Linux](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).

Po utworzeniu połączenia, listę plików w usłudze Data Lake Store za pomocą następującego polecenia systemu plików HDFS.

    hdfs dfs -ls adl:///

Można również użyć `hdfs dfs -put` polecenie, aby przekazać pliki do usługi Data Lake Store, a następnie użyj `hdfs dfs -ls` Aby sprawdzić, czy pliki zostały pomyślnie przekazane.

## <a name="see-also"></a>Zobacz także
* [Użyj Data Lake Store z klastrami Azure HDInsight](../hdinsight/hdinsight-hadoop-use-data-lake-store.md)
* [Portalu Azure: Tworzenie klastra usługi HDInsight do użycia usługi Data Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md)

[makecert]: https://msdn.microsoft.com/library/windows/desktop/ff548309(v=vs.85).aspx
[pvk2pfx]: https://msdn.microsoft.com/library/windows/desktop/ff550672(v=vs.85).aspx
