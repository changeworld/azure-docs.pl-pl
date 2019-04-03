---
title: Szablony platformy Azure umożliwiają tworzenie HDInsight przy użyciu usługi Azure Data Lake Storage Gen1 | Dokumentacja firmy Microsoft
description: Szablony usługi Azure Resource Manager umożliwiają tworzenie i używanie klastrów HDInsight za pomocą usługi Azure Data Lake Storage Gen1
services: data-lake-store,hdinsight
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.assetid: 8ef8152f-2121-461e-956c-51c55144919d
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: b09ca2cc358107c5f95fe3426351d380380db3c2
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2019
ms.locfileid: "58880138"
---
# <a name="create-an-hdinsight-cluster-with-azure-data-lake-storage-gen1-using-azure-resource-manager-template"></a>Tworzenie klastra usługi HDInsight przy użyciu usługi Azure Data Lake Storage Gen1 przy użyciu szablonu usługi Azure Resource Manager
> [!div class="op_single_selector"]
> * [Korzystanie z portalu](data-lake-store-hdinsight-hadoop-use-portal.md)
> * [Przy użyciu programu PowerShell (do magazynu domyślnego)](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
> * [Przy użyciu programu PowerShell (w przypadku dodatkowego magazynu)](data-lake-store-hdinsight-hadoop-use-powershell.md)
> * [Używanie Menedżera zasobów](data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)
>
>

Dowiedz się, jak za pomocą programu Azure PowerShell Konfigurowanie klastra usługi HDInsight przy użyciu usługi Azure Data Lake Storage Gen1 **jako magazynu dodatkowego**.

W przypadku klastra obsługiwanych typów Data Lake Storage Gen1 może służyć jako magazynem domyślnym lub dodatkowe konto magazynu. Gdy Data Lake Storage Gen1 jest używany jako magazyn dodatkowy, domyślne konto magazynu dla klastrów będzie nadal usługi Azure blob Storage (WASB) i domyślny magazyn danych, który chcesz nadal są zapisywane pliki dotyczące klastra (na przykład dzienników itp.) proces mogą być przechowywane na koncie usługi Data Lake Storage Gen1. Za pomocą programu Data Lake Storage Gen1 jako dodatkowe konto magazynu nie ma wpływu na wydajność ani możliwość odczytu/zapisu do magazynu z klastra.

## <a name="using-data-lake-storage-gen1-for-hdinsight-cluster-storage"></a>Za pomocą programu Data Lake Storage Gen1 dla magazynu klastra HDInsight

Poniżej przedstawiono kilka istotnych kwestii dotyczących HDInsight przy użyciu Data Lake Storage Gen1:

* Opcja umożliwiająca tworzenie klastrów HDInsight z dostępem do programu Data Lake Storage Gen1, jako magazyn domyślny jest dostępna dla HDInsight w wersji 3.5 i 3.6.

* Opcja umożliwiająca tworzenie klastrów HDInsight z dostępem do programu Data Lake Storage Gen1, jako magazyn dodatkowy jest dostępny dla HDInsight w wersji 3.2, 3.4, 3.5 i 3.6.

W tym artykule będziemy Zainicjuj obsługę klastra Hadoop, za pomocą programu Data Lake Storage Gen1 jako dodatkowego magazynu. Aby uzyskać instrukcje, jak utworzyć klaster usługi Hadoop za pomocą Data Lake Storage Gen1 jako magazynem domyślnym, zobacz [tworzenia klastra usługi HDInsight przy użyciu Data Lake Storage Gen1 przy użyciu witryny Azure Portal](data-lake-store-hdinsight-hadoop-use-portal.md).

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Przed przystąpieniem do wykonania kroków opisanych w tym samouczku należy dysponować następującymi elementami:

* **Subskrypcja platformy Azure**. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Program Azure PowerShell 1.0 lub nowszy**. Zobacz artykuł [Instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azure/overview).
* **Azure Active Directory Service Principal**. Kroki opisane w tym samouczku zawierają instrukcje dotyczące sposobu tworzenia jednostki usługi w usłudze Azure AD. Jednakże musi być administratorem usługi Azure AD, aby można było utworzyć nazwę główną usługi. Jeśli jesteś administratorem usługi Azure AD, można pominąć to wymaganie wstępne, a następnie przejść do samouczka.

    **Jeśli nie jesteś administratorem usługi Azure AD**, nie będziesz w stanie wykonać kroki wymagane do utworzenia jednostki usługi. W takim przypadku administrator usługi Azure AD należy najpierw utworzyć nazwę główną usługi można było utworzyć klaster usługi HDInsight za pomocą programu Data Lake Storage Gen1. Także nazwy głównej usługi muszą zostać utworzone za pomocą certyfikatu, zgodnie z opisem w [utworzyć nazwę główną usługi za pomocą certyfikatu](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-certificate-from-certificate-authority).

## <a name="create-an-hdinsight-cluster-with-data-lake-storage-gen1"></a>Tworzenie klastra HDInsight z usługą Data Lake Storage Gen1
Szablon usługi Resource Manager i wymagania wstępne dotyczące korzystania z szablonu, są dostępne w usłudze GitHub w [wdrażanie klastra HDInsight w systemie Linux przy użyciu nowego Data Lake Storage Gen1](https://github.com/Azure/azure-quickstart-templates/tree/master/201-hdinsight-datalake-store-azure-storage). Wykonaj instrukcje podane w tym link Tworzenie klastra HDInsight z usługą Data Lake Storage Gen1 jako dodatkowego magazynu.

Zgodnie z instrukcjami na link wymienionych powyżej wymagają programu PowerShell. Przed rozpoczęciem pracy z tymi instrukcjami, upewnij się, że logujesz się do konta platformy Azure. Na pulpicie otwórz nowe okno programu Azure PowerShell, a następnie wprowadź poniższe fragmenty kodu. Po wyświetleniu monitu, aby zalogować się, upewnij się, że logujesz się jako jeden z administratorów/właściciel subskrypcji:

```
# Log in to your Azure account
Connect-AzAccount

# List all the subscriptions associated to your account
Get-AzSubscription

# Select a subscription
Set-AzContext -SubscriptionId <subscription ID>
```

Szablon umożliwia wdrożenie tych typów zasobów:

* [Microsoft.DataLakeStore/accounts](/azure/templates/microsoft.datalakestore/accounts)
* [Microsoft.Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts)
* [Microsoft.HDInsight/clusters](/azure/templates/microsoft.hdinsight/clusters)

## <a name="upload-sample-data-to-data-lake-storage-gen1"></a>Przekazywanie przykładowych danych do programu Data Lake Storage Gen1
Szablon usługi Resource Manager powoduje utworzenie nowego konta Data Lake Storage Gen1 i kojarzy ją z klastrem HDInsight. Teraz należy przekazać przykładowe dane do usługi Data Lake Storage Gen1. Te dane potrzebne w dalszej części samouczka, aby uruchamiać zadania z klastra usługi HDInsight, że dostęp do danych w ramach konta Data Lake Storage Gen1. Aby uzyskać instrukcje na temat przekazywania danych, zobacz [przekazać plik do konta usługi Data Lake Storage Gen1](data-lake-store-get-started-portal.md#uploaddata). Jeśli szukasz przykładowych danych do przekazania, możesz pobrać folder **Ambulance Data** z [repozytorium Git usługi Azure Data Lake](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData).

## <a name="set-relevant-acls-on-the-sample-data"></a>Ustawić odpowiednich list ACL dla przykładowych danych
Aby upewnić się, że przykładowe dane, które zostaną przesłane jest dostępny z klastra HDInsight, musisz zapewnić, że aplikacji usługi Azure AD, który jest używany do ustanawiania tożsamości między klastrem HDInsight i Data Lake Storage Gen1 miał dostęp do pliku/folderu, w którym próbujesz się dostęp. Aby to zrobić, wykonaj następujące czynności.

1. Znajdź nazwę aplikacji usługi Azure AD, który jest skojarzony z klastrem HDInsight i konta Data Lake Storage Gen1. Jednym ze sposobów poszukaj nazwa jest, aby otworzyć blok klastra HDInsight, które zostały utworzone za pomocą szablonu usługi Resource Manager, kliknij przycisk **tożsamości usługi AAD klastra** kartę i wyszukaj wartość **nazwę wyświetlaną nazwę główną usługi**.
2. Teraz zapewniają dostęp do tej aplikacji usługi Azure AD w pliku/folderu, który chcesz uzyskać dostęp z klastra HDInsight. Aby ustawić odpowiednie listy ACL dla pliku/folderu Data Lake Storage Gen1, zobacz [Zabezpieczanie danych w Data Lake Storage Gen1](data-lake-store-secure-data.md#filepermissions).

## <a name="run-test-jobs-on-the-hdinsight-cluster-to-use-data-lake-storage-gen1"></a>Uruchamianie testów zadań w klastrze HDInsight, aby użyć Data Lake Storage Gen1
Po skonfigurowaniu klastra usługi HDInsight, zadania testowe można uruchomić w klastrze, aby sprawdzić, czy klaster HDInsight mogą uzyskiwać dostęp do programu Data Lake Storage Gen1. Aby to zrobić, dokonamy przykładowe zadania Hive, która tworzy tabelę przy użyciu przykładowych danych, który został wcześniej przekazany do konta usługi Data Lake Storage Gen1.

W tej sekcji zostanie SSH do klastra HDInsight w systemie Linux a następnie uruchom przykładowe zapytanie programu Hive. Jeśli używasz klienta Windows, zalecamy użycie **PuTTY**, który można pobrać z [ https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html ](https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

Aby uzyskać więcej informacji na temat korzystania z programu PuTTY, zobacz [używanie protokołu SSH z opartą na systemie Linux z platformą Hadoop w HDInsight z Windows](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md).

1. Po nawiązaniu połączenia, należy uruchomić interfejs wiersza polecenia programu Hive przy użyciu następującego polecenia:

   ```
   hive
   ```
2. Przy użyciu interfejsu wiersza polecenia, wprowadź poniższe instrukcje, aby utworzyć nową tabelę o nazwie **pojazdów** przy użyciu przykładowych danych w Data Lake Storage Gen1:

   ```
   DROP TABLE vehicles;
   CREATE EXTERNAL TABLE vehicles (str string) LOCATION 'adl://<mydatalakestoragegen1>.azuredatalakestore.net:443/';
   SELECT * FROM vehicles LIMIT 10;
   ```

   Powinny pojawić się dane wyjściowe podobne do następujących:

   ```
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
   ```


## <a name="access-data-lake-storage-gen1-using-hdfs-commands"></a>Data Lake Storage Gen1 dostęp przy użyciu poleceń systemu plików HDFS
Po skonfigurowaniu klastra HDInsight w Data Lake Storage Gen1 służy poleceń powłoki systemu plików HDFS dostępu do sklepu.

W tej sekcji zostanie SSH w systemie HDInsight Linux klastra, a następnie uruchom polecenia systemu plików HDFS. Jeśli używasz klienta Windows, zalecamy użycie **PuTTY**, który można pobrać z [ https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html ](https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

Aby uzyskać więcej informacji na temat korzystania z programu PuTTY, zobacz [używanie protokołu SSH z opartą na systemie Linux z platformą Hadoop w HDInsight z Windows](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md).

Po nawiązaniu połączenia użyj następującego polecenia systemu plików HDFS, aby wyświetlić listę plików w ramach konta Data Lake Storage Gen1.

```
hdfs dfs -ls adl://<Data Lake Storage Gen1 account name>.azuredatalakestore.net:443/
```

Powinny pojawić się plik który został wcześniej przekazany do Data Lake Storage Gen1.

```
15/09/17 21:41:15 INFO web.CaboWebHdfsFileSystem: Replacing original urlConnectionFactory with org.apache.hadoop.hdfs.web.URLConnectionFactory@21a728d6
Found 1 items
-rwxrwxrwx   0 NotSupportYet NotSupportYet     671388 2015-09-16 22:16 adl://mydatalakestoragegen1.azuredatalakestore.net:443/mynewfolder
```

Można również użyć `hdfs dfs -put` polecenie, aby przekazać pliki do Data Lake Storage Gen1, a następnie użyj `hdfs dfs -ls` Aby sprawdzić, czy pliki zostały pomyślnie przekazane.


## <a name="next-steps"></a>Kolejne kroki
* [Kopiowanie danych z obiektów blob usługi Azure Storage do Data Lake Storage Gen1](data-lake-store-copy-data-wasb-distcp.md)
* [Za pomocą usług Data Lake Storage Gen1 klastrów Azure HDInsight](../hdinsight/hdinsight-hadoop-use-data-lake-store.md)
