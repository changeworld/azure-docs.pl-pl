---
title: 'PowerShell: klaster usługi Azure HDInsight z usługą Azure Data Lake Storage Gen1 jako magazyn dodatków | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować klaster usługi HDInsight za pomocą usługi Azure Data Lake Storage Gen1 jako dodatkowego magazynu.
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
ms.openlocfilehash: 4cd61619e0417ab1db8d8413872b2dff1c904fc1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78970140"
---
# <a name="use-azure-powershell-to-create-an-hdinsight-cluster-with-azure-data-lake-storage-gen1-as-additional-storage"></a>Tworzenie klastra usługi HDInsight za pomocą usługi Azure Data Lake Storage Gen1 (jako dodatkowego magazynu) za pomocą programu Azure PowerShell.

> [!div class="op_single_selector"]
> * [Korzystanie z portalu](data-lake-store-hdinsight-hadoop-use-portal.md)
> * [Korzystanie z programu PowerShell (do magazynu domyślnego)](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
> * [Korzystanie z programu PowerShell (do dodatkowego magazynu)](data-lake-store-hdinsight-hadoop-use-powershell.md)
> * [Korzystanie z Menedżera zasobów](data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)
>
>

Dowiedz się, jak skonfigurować klaster usługi HDInsight za pomocą usługi Azure Data Lake Storage Gen1 **jako dodatkowego magazynu.** Aby uzyskać instrukcje dotyczące tworzenia klastra HDInsight z pamięcią masową Data Lake Storage Gen1 jako magazynu [domyślnego, zobacz Tworzenie klastra HDInsight z pamięcią masową Data Lake Storage Gen1 jako magazynu domyślnego](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md).

> [!NOTE]
> Jeśli zamierzasz używać usługi Data Lake Storage Gen1 jako dodatkowego magazynu dla klastra HDInsight, zdecydowanie zaleca się to zrobić podczas tworzenia klastra, jak opisano w tym artykule. Dodawanie data lake storage gen1 jako dodatkowego magazynu do istniejącego klastra HDInsight jest skomplikowanym procesem i podatne na błędy.
>

W przypadku obsługiwanych typów klastrów usługa Data Lake Storage Gen1 może służyć jako domyślne konto magazynu lub dodatkowe konto magazynu. Gdy usługa Data Lake Storage Gen1 jest używana jako dodatkowy magazyn, domyślnym kontem magazynu dla klastrów nadal będą obiekty Blobs usługi Azure Storage (WASB), a pliki związane z klastrem (takie jak dzienniki itp.) są nadal zapisywane w magazynie domyślnym, podczas gdy dane, które chcesz proces może być przechowywany na koncie Data Lake Storage Gen1. Używanie usługi Data Lake Storage Gen1 jako dodatkowego konta magazynu nie wpływa na wydajność ani na możliwość odczytu/zapisu do magazynu z klastra.

## <a name="using-data-lake-storage-gen1-for-hdinsight-cluster-storage"></a>Korzystanie z usługi Data Lake Storage Gen1 dla pamięci masowej klastra HDInsight

Oto kilka ważnych zagadnień dotyczących korzystania z usługi HDInsight z pamięcią masową Data Lake Gen1:

* Opcja tworzenia klastrów HDInsight z dostępem do pamięci masowej Data Lake Gen1 jako dodatkowego magazynu jest dostępna dla hdinsight w wersjach 3.2, 3.4, 3.5 i 3.6.

Konfigurowanie usługi HDInsight do pracy z pamięcią Data Lake Storage Gen1 przy użyciu programu PowerShell obejmuje następujące kroki:

* Tworzenie konta usługi Data Lake Storage Gen1
* Konfigurowanie uwierzytelniania dla dostępu opartego na rolach do usługi Data Lake Storage Gen1
* Tworzenie klastra USŁUGI HDInsight z uwierzytelnianiem w umiań magazynu usługi Data Lake
* Uruchamianie zadania testowego w klastrze

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Przed przystąpieniem do wykonania kroków opisanych w tym samouczku należy dysponować następującymi elementami:

* **Subskrypcja platformy Azure**. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Program Azure PowerShell 1.0 lub nowszy**. Zobacz artykuł [Instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azure/overview).
* **Windows SDK**. Możesz je zainstalować, klikając [tutaj](https://dev.windows.com/en-us/downloads). Służy do tworzenia certyfikatu zabezpieczeń.
* **Podmiot zabezpieczeń usługi Active Directory platformy Azure**. Kroki opisane w tym samouczku zawierają instrukcje dotyczące tworzenia jednostki usługi w usłudze Azure AD. Jednak musisz być administratorem usługi Azure AD, aby móc utworzyć jednostkę usługi. Jeśli jesteś administratorem usługi Azure AD, możesz pominąć to wymaganie wstępne i przejść do samouczka.

    **Jeśli nie jesteś administratorem usługi Azure AD,** nie będzie można wykonać kroki wymagane do utworzenia jednostki usługi. W takim przypadku administrator usługi Azure AD musi najpierw utworzyć jednostkę usługi, zanim będzie można utworzyć klaster HDInsight z gen1 magazynu usługi Data Lake. Ponadto podmiot usługi musi zostać utworzony przy użyciu certyfikatu, zgodnie z opisem w [pozycję Utwórz jednostkę usługi z certyfikatem.](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-certificate-from-certificate-authority)

## <a name="create-a-data-lake-storage-gen1-account"></a>Tworzenie konta usługi Data Lake Storage Gen1
Wykonaj następujące kroki, aby utworzyć konto Usługi Data Lake Storage Gen1.

1. Na pulpicie otwórz nowe okno programu Azure PowerShell i wprowadź następujący fragment kodu. Po wyświetleniu monitu o zalogowanie się upewnij się, że logujesz się jako jeden z administratorów/właścicieli subskrypcji:

        # Log in to your Azure account
        Connect-AzAccount

        # List all the subscriptions associated to your account
        Get-AzSubscription

        # Select a subscription
        Set-AzContext -SubscriptionId <subscription ID>

        # Register for Data Lake Storage Gen1
        Register-AzResourceProvider -ProviderNamespace "Microsoft.DataLakeStore"

   > [!NOTE]
   > Jeśli zostanie wyświetlony błąd `Register-AzResourceProvider : InvalidResourceNamespace: The resource namespace 'Microsoft.DataLakeStore' is invalid` podobny do podczas rejestrowania dostawcy zasobów Usługi Data Lake Storage Gen1, możliwe jest, że subskrypcja nie jest na białej liście dla usługi Data Lake Storage Gen1. Upewnij się, że włączysz subskrypcję platformy Azure dla usługi Data Lake Storage Gen1, postępując zgodnie z tymi [instrukcjami.](data-lake-store-get-started-portal.md)
   >
   >
2. Konto usługi Data Lake Storage Gen1 jest skojarzone z grupą zasobów platformy Azure. Rozpocznij od utworzenia grupy zasobów platformy Azure.

        $resourceGroupName = "<your new resource group name>"
        New-AzResourceGroup -Name $resourceGroupName -Location "East US 2"

    Powinny pojawić się następujące dane wyjściowe:

        ResourceGroupName : hdiadlgrp
        Location          : eastus2
        ProvisioningState : Succeeded
        Tags              :
        ResourceId        : /subscriptions/<subscription-id>/resourceGroups/hdiadlgrp

3. Utwórz konto Gen1 magazynu usługi Data Lake. Określona nazwa konta musi zawierać tylko małe litery i cyfry.

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

5. Przekaż przykładowe dane do usługi Data Lake Storage Gen1. Użyjemy tego w dalszej części tego artykułu, aby sprawdzić, czy dane są dostępne z klastra HDInsight. Jeśli szukasz przykładowych danych do przekazania, możesz pobrać folder **Ambulance Data** z [repozytorium Git usługi Azure Data Lake](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData).

        $myrootdir = "/"
        Import-AzDataLakeStoreItem -AccountName $dataLakeStorageGen1Name -Path "C:\<path to data>\vehicle1_09142014.csv" -Destination $myrootdir\vehicle1_09142014.csv


## <a name="set-up-authentication-for-role-based-access-to-data-lake-storage-gen1"></a>Konfigurowanie uwierzytelniania dla dostępu opartego na rolach do usługi Data Lake Storage Gen1

Każda subskrypcja platformy Azure jest skojarzona z usługą Azure Active Directory. Użytkownicy i usługi, które uzyskują dostęp do zasobów subskrypcji przy użyciu witryny Azure portal lub interfejsu API usługi Azure Resource Manager, muszą najpierw uwierzytelnić się przy użyciu tej usługi Azure Active Directory. Dostęp jest przyznawany do subskrypcji platformy Azure i usług, przypisując im odpowiednią rolę w zasobie platformy Azure.  W przypadku usług podmiotu zabezpieczeń usługi identyfikuje usługę w usłudze Azure Active Directory (AAD). W tej sekcji pokazano, jak udzielić usługi aplikacji, takich jak HDInsight, dostęp do zasobu platformy Azure (konto Usługi Data Lake Storage Gen1 utworzone wcześniej) przez utworzenie jednostki usługi dla aplikacji i przypisywanie ról do tego za pośrednictwem programu Azure PowerShell.

Aby skonfigurować uwierzytelnianie usługi Active Directory dla usługi Data Lake Storage Gen1, należy wykonać następujące zadania.

* Tworzenie certyfikatu z podpisem własnym
* Tworzenie aplikacji w usłudze Azure Active Directory i jednostki usługi

### <a name="create-a-self-signed-certificate"></a>Tworzenie certyfikatu z podpisem własnym

Upewnij się, że masz zainstalowany [pakiet Windows SDK](https://dev.windows.com/en-us/downloads) przed przystąpieniem do czynności w tej sekcji. Musisz również utworzyć katalog, taki jak **C:\mycertdir**, w którym zostanie utworzony certyfikat.

1. W oknie programu PowerShell przejdź do lokalizacji, w której zainstalowano pakiet Windows SDK (zazwyczaj i użyj narzędzia `C:\Program Files (x86)\Windows Kits\10\bin\x86` [MakeCert,][makecert] aby utworzyć certyfikat z podpisem własnym i klucz prywatny. Użyj następujących poleceń.

        $certificateFileDir = "<my certificate directory>"
        cd $certificateFileDir

        makecert -sv mykey.pvk -n "cn=HDI-ADL-SP" CertFile.cer -r -len 2048

    Zostanie wyświetlony monit o wprowadzenie hasła klucza prywatnego. Po pomyślnym wykonaniu polecenia w określonym katalogu certyfikatów powinien zostać wyświetlony **plik CertFile.cer** i **mykey.pvk.**
2. Użyj narzędzia [Pvk2Pfx,][pvk2pfx] aby przekonwertować pliki .pvk i .cer utworzone przez MakeCert na plik .pfx. Uruchom następujące polecenie.

        pvk2pfx -pvk mykey.pvk -spc CertFile.cer -pfx CertFile.pfx -po <password>

    Po wyświetleniu monitu wprowadź hasło klucza prywatnego określone wcześniej. Wartość określona dla parametru **-po** jest hasłem skojarzonym z plikiem .pfx. Po pomyślnym zakończeniu polecenia w określonym katalogu certyfikatów powinien zostać wyświetlony plik CertFile.pfx.

### <a name="create-an-azure-active-directory-and-a-service-principal"></a>Tworzenie usługi Azure Active Directory i jednostki usługi

W tej sekcji należy wykonać kroki, aby utworzyć jednostkę usługi dla aplikacji usługi Azure Active Directory, przypisać rolę do jednostki usługi i uwierzytelnić jako jednostki usługi, dostarczając certyfikat. Uruchom następujące polecenia, aby utworzyć aplikację w usłudze Azure Active Directory.

1. Wklej następujące polecenia cmdlet w oknie konsoli programu PowerShell. Upewnij się, że wartość określona dla właściwości **-DisplayName** jest unikatowa. Ponadto wartości **-HomePage** i **-IdentiferUris** są wartościami zastępczymi i nie są weryfikowane.

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
3. Udziel jednostki usługi dostępu do folderu Data Lake Storage Gen1 i pliku, do którego będzie można uzyskać dostęp z klastra HDInsight. Poniższy fragment kodu zapewnia dostęp do katalogu głównego konta Data Lake Storage Gen1 (gdzie skopiowano przykładowy plik danych) i samego pliku.

        Set-AzDataLakeStoreItemAclEntry -AccountName $dataLakeStorageGen1Name -Path / -AceType User -Id $objectId -Permissions All
        Set-AzDataLakeStoreItemAclEntry -AccountName $dataLakeStorageGen1Name -Path /vehicle1_09142014.csv -AceType User -Id $objectId -Permissions All

## <a name="create-an-hdinsight-linux-cluster-with-data-lake-storage-gen1-as-additional-storage"></a>Tworzenie klastra SYSTEMU LINUX HDInsight z pamięcią masową Data Lake Storage Gen1 jako dodatkową pamięcią masową

W tej sekcji tworzymy klaster SYSTEMU LINUX HDInsight Hadoop z pamięcią masową Data Lake Storage Gen1 jako dodatkową pamięć masową. W tej wersji klaster HDInsight i konto Data Lake Storage Gen1 muszą znajdować się w tej samej lokalizacji.

1. Zacznij od pobrania identyfikatora dzierżawy subskrypcji. Będziesz tego potrzebować później.

        $tenantID = (Get-AzContext).Tenant.TenantId
2. W tej wersji dla klastra Hadoop data lake storage Gen1 może służyć tylko jako dodatkowy magazyn dla klastra. Domyślnym magazynem nadal będą obiekty BLOB magazynu platformy Azure (WASB). Dlatego najpierw utworzymy konto magazynu i kontenery magazynu wymagane dla klastra.

        # Create an Azure storage account
        $location = "East US 2"
        $storageAccountName = "<StorageAccountName>"   # Provide a Storage account name

        New-AzStorageAccount -ResourceGroupName $resourceGroupName -StorageAccountName $storageAccountName -Location $location -Type Standard_GRS

        # Create an Azure Blob Storage container
        $containerName = "<ContainerName>"              # Provide a container name
        $storageAccountKey = (Get-AzStorageAccountKey -Name $storageAccountName -ResourceGroupName $resourceGroupName)[0].Value
        $destContext = New-AzStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
        New-AzStorageContainer -Name $containerName -Context $destContext
3. Utwórz klaster HDInsight. Użyj następujących poleceń cmdlet.

        # Set these variables
        $clusterName = $containerName                   # As a best practice, have the same name for the cluster and container
        $clusterNodes = <ClusterSizeInNodes>            # The number of nodes in the HDInsight cluster
        $httpCredentials = Get-Credential
        $sshCredentials = Get-Credential

        New-AzHDInsightCluster -ClusterName $clusterName -ResourceGroupName $resourceGroupName -HttpCredential $httpCredentials -Location $location -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" -DefaultStorageAccountKey $storageAccountKey -DefaultStorageContainer $containerName  -ClusterSizeInNodes $clusterNodes -ClusterType Hadoop -Version "3.4" -OSType Linux -SshCredential $sshCredentials -ObjectID $objectId -AadTenantId $tenantID -CertificateFilePath $certificateFilePath -CertificatePassword $password

    Po pomyślnym zakończeniu polecenia cmdlet powinien zostać wyświetlony dane wyjściowe z listą szczegółów klastra.


## <a name="run-test-jobs-on-the-hdinsight-cluster-to-use-the-data-lake-storage-gen1-account"></a>Uruchamianie zadań testowych w klastrze HDInsight w celu użycia konta Data Lake Storage Gen1
Po skonfigurowaniu klastra HDInsight można uruchomić zadania testowe w klastrze, aby sprawdzić, czy klaster HDInsight może uzyskać dostęp do magazynu usługi Data Lake Gen1. W tym celu uruchomimy przykładowe zadanie gałęzi, które tworzy tabelę przy użyciu przykładowych danych przekazanych wcześniej do konta Data Lake Storage Gen1.

W tej sekcji będzie SSH do klastra SYSTEMU LINUX HDInsight utworzony i uruchomić przykładowe zapytanie hive.

* Jeśli używasz klienta systemu Windows do SSH do klastra, zobacz [Korzystanie z SSH z systemem Linux hadoop na HDInsight z systemu Windows](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md).
* Jeśli używasz klienta Linuksa do SSH do klastra, zobacz [Użyj SSH z Systemem Linux Hadoop na HDInsight z Linuksa](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md)

1. Po nawiązaniu połączenia uruchom wiersz wiersza polecenia gałęzi za pomocą następującego polecenia:

        hive
2. Korzystając z interfejsu wiersza polecenia, wprowadź następujące instrukcje, aby utworzyć nową tabelę o nazwie **pojazdy** przy użyciu przykładowych danych w umiaźniach magazynu usługi Data Lake:

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

## <a name="access-data-lake-storage-gen1-using-hdfs-commands"></a>Dostęp do pamięci masowej usługi Data Lake Gen1 przy użyciu poleceń HDFS
Po skonfigurowaniu klastra HDInsight do używania pamięci masowej usługi Data Lake Gen1 można użyć poleceń powłoki HDFS, aby uzyskać dostęp do magazynu.

W tej sekcji będzie SSH do klastra HDInsight Linux utworzony i uruchomić polecenia HDFS.

* Jeśli używasz klienta systemu Windows do SSH do klastra, zobacz [Korzystanie z SSH z systemem Linux hadoop na HDInsight z systemu Windows](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md).
* Jeśli używasz klienta Linuksa do SSH do klastra, zobacz [Użyj SSH z Systemem Linux Hadoop na HDInsight z Linuksa](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md)

Po podłączeniu użyj następującego polecenia systemu plików HDFS, aby wyświetlić listę plików na koncie Data Lake Storage Gen1.

    hdfs dfs -ls adl://<Data Lake Storage Gen1 account name>.azuredatalakestore.net:443/

Należy wyświetlić listę pliku, który został przekazany wcześniej do usługi Data Lake Storage Gen1.

    15/09/17 21:41:15 INFO web.CaboWebHdfsFileSystem: Replacing original urlConnectionFactory with org.apache.hadoop.hdfs.web.URLConnectionFactory@21a728d6
    Found 1 items
    -rwxrwxrwx   0 NotSupportYet NotSupportYet     671388 2015-09-16 22:16 adl://mydatalakestoragegen1.azuredatalakestore.net:443/mynewfolder

Można również użyć `hdfs dfs -put` polecenia, aby przekazać niektóre pliki do usługi `hdfs dfs -ls` Data Lake Storage Gen1, a następnie użyć do sprawdzenia, czy pliki zostały pomyślnie przekazane.

## <a name="see-also"></a>Zobacz też
* [Używanie usługi Data Lake Storage Gen1 w klastrach usługi Azure HDInsight](../hdinsight/hdinsight-hadoop-use-data-lake-store.md)
* [Portal: tworzenie klastra usługi HDInsight w celu użycia usługi Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)

[makecert]: https://msdn.microsoft.com/library/windows/desktop/ff548309(v=vs.85).aspx
[pvk2pfx]: https://msdn.microsoft.com/library/windows/desktop/ff550672(v=vs.85).aspx
