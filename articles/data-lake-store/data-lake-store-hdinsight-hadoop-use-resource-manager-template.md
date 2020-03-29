---
title: Tworzenie usługi HDInsight za pomocą usługi Azure Data Lake Storage Gen1 za pomocą szablonów platformy Azure | Dokumenty firmy Microsoft
description: Tworzenie i używanie klastrów usługi HDInsight za pomocą usługi Azure Data Lake Storage Gen1 za pomocą szablonów usługi Azure Resource Manager
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "66161387"
---
# <a name="create-an-hdinsight-cluster-with-azure-data-lake-storage-gen1-using-azure-resource-manager-template"></a>Tworzenie klastra usługi HDInsight przy użyciu szablonu usługi Azure Data Lake Storage Gen1 przy użyciu szablonu usługi Azure Resource Manager
> [!div class="op_single_selector"]
> * [Korzystanie z portalu](data-lake-store-hdinsight-hadoop-use-portal.md)
> * [Korzystanie z programu PowerShell (do magazynu domyślnego)](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
> * [Korzystanie z programu PowerShell (do dodatkowego magazynu)](data-lake-store-hdinsight-hadoop-use-powershell.md)
> * [Korzystanie z Menedżera zasobów](data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)
>
>

Dowiedz się, jak skonfigurować klaster usługi HDInsight za pomocą usługi Azure Data Lake Storage Gen1 **jako dodatkowego magazynu.**

W przypadku obsługiwanych typów klastrów usługa Data Lake Storage Gen1 może służyć jako domyślne konto magazynu lub dodatkowe konto magazynu. Gdy usługa Data Lake Storage Gen1 jest używana jako dodatkowy magazyn, domyślnym kontem magazynu dla klastrów nadal będą obiekty Blobs usługi Azure Storage (WASB), a pliki związane z klastrem (takie jak dzienniki itp.) są nadal zapisywane w magazynie domyślnym, podczas gdy dane, które chcesz proces może być przechowywany na koncie Data Lake Storage Gen1. Używanie usługi Data Lake Storage Gen1 jako dodatkowego konta magazynu nie wpływa na wydajność ani na możliwość odczytu/zapisu do magazynu z klastra.

## <a name="using-data-lake-storage-gen1-for-hdinsight-cluster-storage"></a>Korzystanie z usługi Data Lake Storage Gen1 dla pamięci masowej klastra HDInsight

Oto kilka ważnych zagadnień dotyczących korzystania z usługi HDInsight z pamięcią masową Data Lake Gen1:

* Opcja tworzenia klastrów HDInsight z dostępem do pamięci masowej Data Lake Gen1 jako domyślnej pamięci masowej jest dostępna dla hdinsight w wersji 3.5 i 3.6.

* Opcja tworzenia klastrów HDInsight z dostępem do pamięci masowej Data Lake Gen1 jako dodatkowego magazynu jest dostępna dla hdinsight w wersjach 3.2, 3.4, 3.5 i 3.6.

W tym artykule aprowizować klaster Hadoop z Data Lake Storage Gen1 jako dodatkowego magazynu. Aby uzyskać instrukcje dotyczące tworzenia klastra Hadoop z magazynem data lake Gen1 jako magazynu [domyślnego, zobacz Tworzenie klastra HDInsight z magazynem data lake gen1 przy użyciu usługi Azure Portal](data-lake-store-hdinsight-hadoop-use-portal.md).

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Przed przystąpieniem do wykonania kroków opisanych w tym samouczku należy dysponować następującymi elementami:

* **Subskrypcja platformy Azure**. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Program Azure PowerShell 1.0 lub nowszy**. Zobacz artykuł [Instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azure/overview).
* **Podmiot zabezpieczeń usługi Active Directory platformy Azure**. Kroki opisane w tym samouczku zawierają instrukcje dotyczące tworzenia jednostki usługi w usłudze Azure AD. Jednak musisz być administratorem usługi Azure AD, aby móc utworzyć jednostkę usługi. Jeśli jesteś administratorem usługi Azure AD, możesz pominąć to wymaganie wstępne i przejść do samouczka.

    **Jeśli nie jesteś administratorem usługi Azure AD,** nie będzie można wykonać kroki wymagane do utworzenia jednostki usługi. W takim przypadku administrator usługi Azure AD musi najpierw utworzyć jednostkę usługi, zanim będzie można utworzyć klaster HDInsight z gen1 magazynu usługi Data Lake. Ponadto podmiot usługi musi zostać utworzony przy użyciu certyfikatu, zgodnie z opisem w [pozycję Utwórz jednostkę usługi z certyfikatem.](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-certificate-from-certificate-authority)

## <a name="create-an-hdinsight-cluster-with-data-lake-storage-gen1"></a>Tworzenie klastra USŁUGI HDInsight z pamięcią magazynową usługi Data Lake Gen1
Szablon Menedżera zasobów i wymagania wstępne dotyczące korzystania z szablonu są dostępne w usłudze GitHub w [usłudze Wdrażanie klastra systemu LINUX HDInsight z nową pamięcią Data Lake Storage Gen1](https://github.com/Azure/azure-quickstart-templates/tree/master/201-hdinsight-datalake-store-azure-storage). Postępuj zgodnie z instrukcjami podanymi w tym łączu, aby utworzyć klaster HDInsight z magazynem data lake gen1 jako dodatkowego magazynu.

Instrukcje pod łączem, o którym mowa powyżej, wymagają programu PowerShell. Zanim zaczniesz od tych instrukcji, upewnij się, że logujesz się do konta platformy Azure. Na pulpicie otwórz nowe okno programu Azure PowerShell i wprowadź następujące fragmenty kodu. Po wyświetleniu monitu o zalogowanie się upewnij się, że logujesz się jako jeden z administratorów/właściciela subskrypcji:

```
# Log in to your Azure account
Connect-AzAccount

# List all the subscriptions associated to your account
Get-AzSubscription

# Select a subscription
Set-AzContext -SubscriptionId <subscription ID>
```

Szablon wdraża następujące typy zasobów:

* [Microsoft.DataLakeStore/konta](/azure/templates/microsoft.datalakestore/accounts)
* [Microsoft.Storage/storageKondyje](/azure/templates/microsoft.storage/storageaccounts)
* [Microsoft.HDInsight/klastry](/azure/templates/microsoft.hdinsight/clusters)

## <a name="upload-sample-data-to-data-lake-storage-gen1"></a>Przekazywanie przykładowych danych do usługi Data Lake Storage Gen1
Szablon Menedżera zasobów tworzy nowe konto Data Lake Storage Gen1 i kojarzy je z klastrem HDInsight. Teraz należy przekazać niektóre przykładowe dane do usługi Data Lake Storage Gen1. Te dane będą potrzebne w dalszej części samouczka do uruchamiania zadań z klastra HDInsight, które uzyskują dostęp do danych na koncie Data Lake Storage Gen1. Aby uzyskać instrukcje dotyczące przekazywania danych, zobacz [Przekazywanie pliku do konta Data Lake Storage Gen1](data-lake-store-get-started-portal.md#uploaddata). Jeśli szukasz przykładowych danych do przekazania, możesz pobrać folder **Ambulance Data** z [repozytorium Git usługi Azure Data Lake](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData).

## <a name="set-relevant-acls-on-the-sample-data"></a>Ustawianie odpowiednich list ACL na przykładowych danych
Aby upewnić się, że przykładowe dane, które zostały przekazane, są dostępne z klastra HDInsight, należy upewnić się, że aplikacja usługi Azure AD używana do ustanawiania tożsamości między klastrem HDInsight a magazynem danych Gen1 ma dostęp do pliku/folderu, do którego próbujesz uzyskać dostęp. Aby to zrobić, wykonaj następujące kroki.

1. Znajdź nazwę aplikacji usługi Azure AD skojarzonej z klastrem HDInsight i kontem Data Lake Storage Gen1. Jednym ze sposobów wyszukiwania nazwy jest otwarcie bloku klastra USŁUGI HDInsight utworzonego przy użyciu szablonu Menedżera zasobów, kliknięcie karty **Tożsamość usługi AAD klastra** i wyszukanie wartości **głównej nazwy wyświetlanej usługi**.
2. Teraz zapewnij dostęp do tej aplikacji usługi Azure AD w pliku/folderze, do którego chcesz uzyskać dostęp z klastra HDInsight. Aby ustawić odpowiednie listy ACL w pliku/folderze w programie Data Lake Storage Gen1, zobacz [Zabezpieczanie danych w programie Data Lake Storage Gen1](data-lake-store-secure-data.md#filepermissions).

## <a name="run-test-jobs-on-the-hdinsight-cluster-to-use-data-lake-storage-gen1"></a>Uruchamianie zadań testowych w klastrze HDInsight w celu użycia usługi Data Lake Storage Gen1
Po skonfigurowaniu klastra HDInsight można uruchomić zadania testowe w klastrze, aby sprawdzić, czy klaster HDInsight może uzyskać dostęp do magazynu usługi Data Lake Gen1. W tym celu uruchomimy przykładowe zadanie gałęzi, które tworzy tabelę przy użyciu przykładowych danych przekazanych wcześniej do konta Data Lake Storage Gen1.

W tej sekcji będzie SSH do klastra SYSTEMU LINUX HDInsight i uruchomić przykładowe zapytanie hive. Jeśli używasz klienta systemu Windows, zalecamy użycie **PuTTY** [https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html), które można pobrać z .

Aby uzyskać więcej informacji na temat korzystania z PuTTY, zobacz [Korzystanie z SSH z systemem Linux Hadoop na HDInsight z systemu Windows](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md).

1. Po nawiązaniu połączenia uruchom wiersz wiersza polecenia gałęzi za pomocą następującego polecenia:

   ```
   hive
   ```
2. Korzystając z interfejsu wiersza polecenia, wprowadź następujące instrukcje, aby utworzyć nową tabelę o nazwie **pojazdy** przy użyciu przykładowych danych w umiaźniach magazynu usługi Data Lake:

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


## <a name="access-data-lake-storage-gen1-using-hdfs-commands"></a>Dostęp do pamięci masowej usługi Data Lake Gen1 przy użyciu poleceń HDFS
Po skonfigurowaniu klastra HDInsight do używania pamięci masowej usługi Data Lake Gen1 można użyć poleceń powłoki HDFS, aby uzyskać dostęp do magazynu.

W tej sekcji będziesz SSH do klastra HDInsight Linux i uruchomić polecenia HDFS. Jeśli używasz klienta systemu Windows, zalecamy użycie **PuTTY** [https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html), które można pobrać z .

Aby uzyskać więcej informacji na temat korzystania z PuTTY, zobacz [Korzystanie z SSH z systemem Linux Hadoop na HDInsight z systemu Windows](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md).

Po podłączeniu użyj następującego polecenia systemu plików HDFS, aby wyświetlić listę plików na koncie Data Lake Storage Gen1.

```
hdfs dfs -ls adl://<Data Lake Storage Gen1 account name>.azuredatalakestore.net:443/
```

Należy wyświetlić listę pliku, który został przekazany wcześniej do usługi Data Lake Storage Gen1.

```
15/09/17 21:41:15 INFO web.CaboWebHdfsFileSystem: Replacing original urlConnectionFactory with org.apache.hadoop.hdfs.web.URLConnectionFactory@21a728d6
Found 1 items
-rwxrwxrwx   0 NotSupportYet NotSupportYet     671388 2015-09-16 22:16 adl://mydatalakestoragegen1.azuredatalakestore.net:443/mynewfolder
```

Można również użyć `hdfs dfs -put` polecenia, aby przekazać niektóre pliki do usługi `hdfs dfs -ls` Data Lake Storage Gen1, a następnie użyć do sprawdzenia, czy pliki zostały pomyślnie przekazane.


## <a name="next-steps"></a>Następne kroki
* [Kopiowanie danych z obiektów blob usługi Azure Storage do usługi Data Lake Storage Gen1](data-lake-store-copy-data-wasb-distcp.md)
* [Używanie usługi Data Lake Storage Gen1 w klastrach usługi Azure HDInsight](../hdinsight/hdinsight-hadoop-use-data-lake-store.md)
