---
title: 'Środowiska PowerShell: Usługa Azure HDInsight klaster z usługi Data Lake Store jako magazyn dodatków | Dokumentacja firmy Microsoft'
services: data-lake-store,hdinsight
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 164ada5a-222e-4be2-bd32-e51dbe993bc0
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 01/30/2018
ms.author: nitinme
ms.openlocfilehash: 4c08dac95a2d2b52f1a1d28f6933b94ad4db10b7
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
---
# <a name="use-azure-powershell-to-create-an-hdinsight-cluster-with-data-lake-store-as-additional-storage"></a>Tworzenie klastra usługi HDInsight z usługą Data Lake Store (jako dodatkowej pamięci masowej) przy użyciu programu Azure PowerShell

> [!div class="op_single_selector"]
> * [Korzystanie z portalu](data-lake-store-hdinsight-hadoop-use-portal.md)
> * [Przy użyciu programu PowerShell (do magazynu domyślnego)](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
> * [Przy użyciu programu PowerShell (dla dodatkowego magazynu)](data-lake-store-hdinsight-hadoop-use-powershell.md)
> * [Za pomocą Menedżera zasobów](data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)
>
>

Dowiedz się, jak skonfigurować klaster usługi HDInsight z usługi Azure Data Lake Store, przy użyciu programu Azure PowerShell **jako dodatkowego magazynu**. Aby uzyskać instrukcje dotyczące sposobu tworzenia klastra usługi HDInsight z usługi Azure Data Lake Store jako domyślnego magazynu, zobacz [tworzenia klastra usługi HDInsight z usługą Data Lake Store jako domyślny magazyn](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md).

> [!NOTE]
> Jeśli zamierzasz używać usługi Azure Data Lake Store jako dodatkowego magazynu klastra usługi HDInsight, zdecydowanie zalecamy wykonanie tej czynności podczas tworzenia klastra zgodnie z opisem w tym artykule. Dodawanie usługi Azure Data Lake Store jako dodatkowego magazynu do istniejącego klastra usługi HDInsight jest skomplikowane i podatne na błędy.
>

Dla typów obsługiwanych klastra usługi Data Lake Store może służyć jako domyślnego magazynu lub konto dodatkowego miejsca do magazynowania. W przypadku usługi Data Lake Store jako dodatkowego magazynu domyślne konto magazynu dla klastrów nadal będzie Azure blob Storage (WASB) i plików związanych z klastrem (na przykład dzienników itp.) nadal są zapisywane do magazynu domyślnego, gdy mogą być przechowywane dane, które ma być przetwarzane w ramach konta usługi Data Lake Store. Za pomocą usługi Data Lake Store jako dodatkowego magazynu konta nie wpływa na wydajność lub możliwości odczytu i zapisu do magazynu z klastra.

## <a name="using-data-lake-store-for-hdinsight-cluster-storage"></a>W magazynie klastra usługi HDInsight przy użyciu usługi Data Lake Store

Poniżej przedstawiono kilka istotnych kwestii dotyczących z usługą Data Lake Store za pomocą usługi HDInsight:

* Możliwość tworzenia klastrów usługi HDInsight z dostępem do usługi Data Lake Store jako dodatkowego magazynu jest dostępna dla usługi HDInsight w wersji 3.2, 3.4, 3.5 i 3,6.

Konfigurowanie usługi HDInsight do pracy z usługą Data Lake Store za pomocą programu PowerShell obejmuje następujące kroki:

* Tworzenie usługi Azure Data Lake Store
* Konfigurowanie uwierzytelniania opartego na rolach dostępu do usługi Data Lake Store
* Tworzenie klastra usługi HDInsight przy użyciu uwierzytelniania do usługi Data Lake Store
* Uruchom zadanie testowe w klastrze

## <a name="prerequisites"></a>Wymagania wstępne
Przed przystąpieniem do wykonania kroków opisanych w tym samouczku należy dysponować następującymi elementami:

* **Subskrypcja platformy Azure**. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Program Azure PowerShell 1.0 lub nowszy**. Zobacz artykuł [Instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azure/overview).
* **Zestaw Windows SDK**. Możesz je zainstalować, klikając [tutaj](https://dev.windows.com/en-us/downloads). Umożliwia to utworzenie certyfikatu bezpieczeństwa.
* **Nazwy głównej usługi Azure Active Directory usługi**. Kroki opisane w tym samouczku zawierają instrukcje dotyczące sposobu tworzenia nazwy głównej usługi w usłudze Azure AD. Jednak musi być administrator usługi Azure AD, aby można było utworzyć nazwy głównej usługi. Jeśli jesteś administratorem usługi Azure AD, możesz pominąć te wymagania wstępne i kontynuować samouczka.

    **Jeśli nie jesteś administratorem usługi Azure AD**, nie można wykonać kroki wymagane do utworzenia nazwy głównej usługi. W takim przypadku administrator usługi Azure AD należy najpierw utworzyć nazwy głównej usługi przed utworzeniem klastra usługi HDInsight z usługą Data Lake Store. Ponadto nazwy głównej usługi musi być utworzony przy użyciu certyfikatu, zgodnie z opisem w [Tworzenie nazwy głównej usługi o certyfikat](../azure-resource-manager/resource-group-authenticate-service-principal.md#create-service-principal-with-certificate-from-certificate-authority).

## <a name="create-an-azure-data-lake-store"></a>Tworzenie usługi Azure Data Lake Store
Wykonaj następujące kroki, aby utworzyć usługi Data Lake Store.

1. Na pulpicie otwórz nowe okno programu Azure PowerShell, a następnie wprowadź poniższy fragment kodu. Po wyświetleniu monitu, aby się zalogować, upewnij się, że logujesz się jako jeden właściciel administratora subskrypcji:

        # Log in to your Azure account
        Connect-AzureRmAccount

        # List all the subscriptions associated to your account
        Get-AzureRmSubscription

        # Select a subscription
        Set-AzureRmContext -SubscriptionId <subscription ID>

        # Register for Data Lake Store
        Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.DataLakeStore"

   > [!NOTE]
   > Jeśli zostanie wyświetlony błąd podobny do `Register-AzureRmResourceProvider : InvalidResourceNamespace: The resource namespace 'Microsoft.DataLakeStore' is invalid` podczas rejestrowania dostawcy zasobów usługi Data Lake Store, istnieje możliwość, że Twoja subskrypcja nie jest białej dla usługi Azure Data Lake Store. Upewnij się, że Włącz subskrypcji platformy Azure dla publicznej wersji zapoznawczej usługi Data Lake Store wykonując te [instrukcje](data-lake-store-get-started-portal.md).
   >
   >
2. Konto usługi Azure Data Lake Store jest skojarzone z grupą zasobów platformy Azure. Rozpocznij od utworzenia grupy zasobów platformy Azure.

        $resourceGroupName = "<your new resource group name>"
        New-AzureRmResourceGroup -Name $resourceGroupName -Location "East US 2"

    Powinny pojawić się dane wyjściowe podobne to:

        ResourceGroupName : hdiadlgrp
        Location          : eastus2
        ProvisioningState : Succeeded
        Tags              :
        ResourceId        : /subscriptions/<subscription-id>/resourceGroups/hdiadlgrp

3. Utwórz konto usługi Azure Data Lake Store. Nazwa konta, które określisz musi zawierać tylko małe litery i cyfry.

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

5. Przekazywania przykładowych danych do usługi Azure Data Lake. Użyjemy w dalszej części tego artykułu można zweryfikować, że dane są dostępne z klastra usługi HDInsight. Jeśli szukasz przykładowych danych do przekazania, możesz pobrać folder **Ambulance Data** z [repozytorium Git usługi Azure Data Lake](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData).

        $myrootdir = "/"
        Import-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Path "C:\<path to data>\vehicle1_09142014.csv" -Destination $myrootdir\vehicle1_09142014.csv


## <a name="set-up-authentication-for-role-based-access-to-data-lake-store"></a>Konfigurowanie uwierzytelniania opartego na rolach dostępu do usługi Data Lake Store

Każda subskrypcja platformy Azure jest skojarzony z usługą Azure Active Directory. Z tej usługi Azure Active Directory muszą najpierw uwierzytelnić użytkowników i usług, które uzyskują dostęp do zasobów przy użyciu portalu Azure lub interfejsu API usługi Azure Resource Manager subskrypcji. Dostęp do usług i subskrypcji platformy Azure przez przypisywanie ich odpowiedniej roli na zasobów platformy Azure.  W przypadku usług nazwy głównej usługi identyfikuje usługi w usłudze Azure Active Directory (AAD). W tej części przedstawiono sposób przyznania usługi aplikacji, takich jak usługa HDInsight, dostęp do zasobów platformy Azure (utworzone wcześniej konto usługi Azure Data Lake Store) przez tworzenie nazwy głównej usługi dla aplikacji i przypisywanie ról w tym za pomocą programu Azure PowerShell.

Aby skonfigurować uwierzytelnianie usługi Active Directory dla usługi Azure Data Lake, należy wykonać poniższe zadania.

* Utwórz certyfikat z podpisem własnym
* Tworzenie aplikacji w usłudze Azure Active Directory i nazwy głównej usługi

### <a name="create-a-self-signed-certificate"></a>Utwórz certyfikat z podpisem własnym

Upewnij się, że masz [zestaw Windows SDK](https://dev.windows.com/en-us/downloads) zainstalowany, przed wykonaniem czynności w tej sekcji. Należy także utworzyć katalogu, takie jak **C:\mycertdir**, gdzie można utworzyć certyfikatu.

1. W oknie programu PowerShell przejdź do lokalizacji, w której zainstalowany zestaw Windows SDK (zazwyczaj `C:\Program Files (x86)\Windows Kits\10\bin\x86` i użyj [MakeCert] [ makecert] narzędzie do utworzenia certyfikatu z podpisem własnym oraz klucza prywatnego. Użyj następujących poleceń.

        $certificateFileDir = "<my certificate directory>"
        cd $certificateFileDir

        makecert -sv mykey.pvk -n "cn=HDI-ADL-SP" CertFile.cer -r -len 2048

    Pojawi się monit o wprowadzenie hasło klucza prywatnego. Po polecenie zostało wykonane pomyślnie, powinien zostać wyświetlony **CertFile.cer** i **mykey.pvk** w katalogu certyfikatu zostanie określony.
2. Użyj [Pvk2Pfx] [ pvk2pfx] narzędzie do konwersji plików .pvk i .cer, które MakeCert utworzone do pliku .pfx. Uruchom następujące polecenie.

        pvk2pfx -pvk mykey.pvk -spc CertFile.cer -pfx CertFile.pfx -po <password>

    Po wyświetleniu monitu wprowadź określone wcześniej hasło klucza prywatnego. Wartość określona dla **— po** parametr jest hasło, które jest skojarzone z plikiem pfx. Po pomyślnym zakończeniu działania polecenia, zobacz też CertFile.pfx w katalogu certyfikat określony.

### <a name="create-an-azure-active-directory-and-a-service-principal"></a>Tworzenie usługi Azure Active Directory i nazwy głównej usługi

W tej sekcji możesz wykonać kroki, aby utworzyć usługę podmiotu zabezpieczeń dla aplikacji usługi Azure Active Directory, przypisać rolę do nazwy głównej usługi i Uwierzytelnij się jako nazwy głównej usługi zapewniając certyfikatu. Uruchom następujące polecenia, aby utworzyć aplikację w usłudze Azure Active Directory.

1. Wklej następujące polecenia cmdlet w oknie konsoli programu PowerShell. Upewnij się, że wartość określona dla **— Nazwa wyświetlana** właściwości jest unikatowa. Ponadto wartości **— strona główna** i **- IdentiferUris** są symbole zastępcze i nie są weryfikowane.

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
2. Tworzenie nazwy głównej usługi przy użyciu identyfikatora aplikacji.

        $servicePrincipal = New-AzureRmADServicePrincipal -ApplicationId $applicationId

        $objectId = $servicePrincipal.Id
3. Udzielać dostępu głównej usługi w folderze usługi Data Lake Store i plik, którego będzie korzystać z klastrem usługi HDInsight. Poniższy fragment zapewnia dostęp do katalogu głównego konta usługi Data Lake Store (gdzie możesz skopiować przykładowy plik danych), a w samym pliku.

        Set-AzureRmDataLakeStoreItemAclEntry -AccountName $dataLakeStoreName -Path / -AceType User -Id $objectId -Permissions All
        Set-AzureRmDataLakeStoreItemAclEntry -AccountName $dataLakeStoreName -Path /vehicle1_09142014.csv -AceType User -Id $objectId -Permissions All

## <a name="create-an-hdinsight-linux-cluster-with-data-lake-store-as-additional-storage"></a>Tworzenie klastra usługi HDInsight Linux z usługą Data Lake Store jako dodatkowego miejsca do magazynowania

W tej sekcji utworzymy klastra usługi HDInsight Hadoop Linux z usługą Data Lake Store jako dodatkowego magazynu. W tej wersji klastra usługi HDInsight i usługi Data Lake Store muszą być w tej samej lokalizacji.

1. Zaczynać się podczas pobierania identyfikatora subskrypcji dzierżawcy. Który będzie potrzebny później.

        $tenantID = (Get-AzureRmContext).Tenant.TenantId
2. W tej wersji dla klastra usługi Hadoop, usługi Data Lake Store można użyć tylko jako dodatkowy magazyn dla klastra. Magazyn domyślne będą nadal obiekty BLOB magazynu Azure (WASB). Tak najpierw utworzymy konto magazynu i kontenery magazynu wymaganych dla klastra.

        # Create an Azure storage account
        $location = "East US 2"
        $storageAccountName = "<StorageAcccountName>"   # Provide a Storage account name

        New-AzureRmStorageAccount -ResourceGroupName $resourceGroupName -StorageAccountName $storageAccountName -Location $location -Type Standard_GRS

        # Create an Azure Blob Storage container
        $containerName = "<ContainerName>"              # Provide a container name
        $storageAccountKey = (Get-AzureRmStorageAccountKey -Name $storageAccountName -ResourceGroupName $resourceGroupName)[0].Value
        $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
        New-AzureStorageContainer -Name $containerName -Context $destContext
3. Tworzenie klastra usługi HDInsight. Użyj następujących poleceń cmdlet.

        # Set these variables
        $clusterName = $containerName                   # As a best practice, have the same name for the cluster and container
        $clusterNodes = <ClusterSizeInNodes>            # The number of nodes in the HDInsight cluster
        $httpCredentials = Get-Credential
        $sshCredentials = Get-Credential

        New-AzureRmHDInsightCluster -ClusterName $clusterName -ResourceGroupName $resourceGroupName -HttpCredential $httpCredentials -Location $location -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" -DefaultStorageAccountKey $storageAccountKey -DefaultStorageContainer $containerName  -ClusterSizeInNodes $clusterNodes -ClusterType Hadoop -Version "3.4" -OSType Linux -SshCredential $sshCredentials -ObjectID $objectId -AadTenantId $tenantID -CertificateFilePath $certificateFilePath -CertificatePassword $password

    Po pomyślnym zakończeniu polecenia cmdlet, powinien pojawić się dane wyjściowe, wyświetlania szczegółów klastra.


## <a name="run-test-jobs-on-the-hdinsight-cluster-to-use-the-data-lake-store"></a>Uruchom zadania testowe w klastrze usługi HDInsight do użycia usługi Data Lake Store
Po skonfigurowaniu klastra usługi HDInsight można uruchamiać zadania testowego w klastrze, aby sprawdzić, czy klastra usługi HDInsight można uzyskać dostępu do usługi Data Lake Store. Aby to zrobić, zostanie uruchomiony przykładowe zadania Hive, która tworzy tabelę przy użyciu przykładowych danych, który został wcześniej przekazany do usługi Data Lake Store.

W tej sekcji zostanie SSH do klastra usługi HDInsight w systemie Linux, możesz utworzyć i uruchomić przykładowe zapytanie Hive.

* Jeśli korzystasz z systemu Windows klienta SSH do klastra, zobacz [używanie SSH z opartą na systemie Linux platformą Hadoop w usłudze HDInsight z systemu Windows](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md).
* Jeśli korzystasz z systemu Linux klientowi SSH do klastra, zobacz [używanie SSH z opartą na systemie Linux platformą Hadoop w usłudze HDInsight z systemu Linux](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md)

1. Po nawiązaniu połączenia, należy uruchomić interfejsu wiersza polecenia Hive za pomocą następującego polecenia:

        hive
2. Przy użyciu interfejsu wiersza polecenia, wpisz poniższe instrukcje, aby utworzyć nową tabelę o nazwie **pojazdów** przy użyciu przykładowych danych w usłudze Data Lake Store:

        DROP TABLE vehicles;
        CREATE EXTERNAL TABLE vehicles (str string) LOCATION 'adl://<mydatalakestore>.azuredatalakestore.net:443/';
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

## <a name="access-data-lake-store-using-hdfs-commands"></a>Dostęp Data Lake Store za pomocą poleceń systemu plików HDFS
Po skonfigurowaniu klastra usługi HDInsight do użycia usługi Data Lake Store służy poleceń powłoki systemu plików HDFS można uzyskać dostępu do magazynu.

W tej sekcji należy SSH do klastra usługi HDInsight Linux utworzone i uruchamiania poleceń systemu plików HDFS.

* Jeśli korzystasz z systemu Windows klienta SSH do klastra, zobacz [używanie SSH z opartą na systemie Linux platformą Hadoop w usłudze HDInsight z systemu Windows](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md).
* Jeśli korzystasz z systemu Linux klientowi SSH do klastra, zobacz [używanie SSH z opartą na systemie Linux platformą Hadoop w usłudze HDInsight z systemu Linux](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md)

Po nawiązaniu połączenia użyj następującego polecenia systemu plików HDFS, aby wyświetlić listę plików w usłudze Data Lake Store.

    hdfs dfs -ls adl://<Data Lake Store account name>.azuredatalakestore.net:443/

Powinny pojawić się plik, który został wcześniej przekazany do usługi Data Lake Store.

    15/09/17 21:41:15 INFO web.CaboWebHdfsFileSystem: Replacing original urlConnectionFactory with org.apache.hadoop.hdfs.web.URLConnectionFactory@21a728d6
    Found 1 items
    -rwxrwxrwx   0 NotSupportYet NotSupportYet     671388 2015-09-16 22:16 adl://mydatalakestore.azuredatalakestore.net:443/mynewfolder

Można również użyć `hdfs dfs -put` polecenie, aby przekazać pliki do usługi Data Lake Store, a następnie użyj `hdfs dfs -ls` Aby sprawdzić, czy pliki zostały pomyślnie przekazane.

## <a name="see-also"></a>Zobacz też
* [Użyj Data Lake Store z klastrami Azure HDInsight](../hdinsight/hdinsight-hadoop-use-data-lake-store.md)
* [Portal: Tworzenie klastra usługi HDInsight do użycia usługi Data Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md)

[makecert]: https://msdn.microsoft.com/library/windows/desktop/ff548309(v=vs.85).aspx
[pvk2pfx]: https://msdn.microsoft.com/library/windows/desktop/ff550672(v=vs.85).aspx
