---
title: Użyj szablonów platformy Azure, aby utworzyć HDInsight i usługi Data Lake Store | Dokumentacja firmy Microsoft
description: Korzystanie z szablonów usługi Azure Resource Manager do tworzenia i klastry usługi HDInsight za pomocą usługi Azure Data Lake Store
services: data-lake-store,hdinsight
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 8ef8152f-2121-461e-956c-51c55144919d
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: nitinme
ms.openlocfilehash: 7f49b643550b1888e1b0a4a6a57bbe4d428c65ac
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34625784"
---
# <a name="create-an-hdinsight-cluster-with-data-lake-store-using-azure-resource-manager-template"></a>Tworzenie klastra usługi HDInsight z Data Lake Store za pomocą szablonu usługi Azure Resource Manager
> [!div class="op_single_selector"]
> * [Korzystanie z portalu](data-lake-store-hdinsight-hadoop-use-portal.md)
> * [Przy użyciu programu PowerShell (do magazynu domyślnego)](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
> * [Przy użyciu programu PowerShell (dla dodatkowego magazynu)](data-lake-store-hdinsight-hadoop-use-powershell.md)
> * [Za pomocą Menedżera zasobów](data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)
>
>

Dowiedz się, jak skonfigurować klaster usługi HDInsight z usługi Azure Data Lake Store, przy użyciu programu Azure PowerShell **jako dodatkowego magazynu**.

Dla typów obsługiwanych klastra usługi Data Lake Store można użyć jako domyślnego magazynu lub konto dodatkowego miejsca do magazynowania. W przypadku usługi Data Lake Store jako dodatkowego magazynu domyślne konto magazynu dla klastrów nadal będzie Azure blob Storage (WASB) i plików związanych z klastrem (na przykład dzienników itp.) nadal są zapisywane do magazynu domyślnego, gdy mogą być przechowywane dane, które ma być przetwarzane w ramach konta usługi Data Lake Store. Za pomocą usługi Data Lake Store jako dodatkowego magazynu konta nie wpływa na wydajność lub możliwości odczytu i zapisu do magazynu z klastra.

## <a name="using-data-lake-store-for-hdinsight-cluster-storage"></a>W magazynie klastra usługi HDInsight przy użyciu usługi Data Lake Store

Poniżej przedstawiono kilka istotnych kwestii dotyczących z usługą Data Lake Store za pomocą usługi HDInsight:

* Możliwość tworzenia klastrów usługi HDInsight z dostępem do usługi Data Lake Store jako domyślny magazyn jest dostępny dla usługi HDInsight w wersji 3.5 i 3,6.

* Możliwość tworzenia klastrów usługi HDInsight z dostępem do usługi Data Lake Store jako dodatkowego magazynu jest dostępna dla usługi HDInsight w wersji 3.2, 3.4, 3.5 i 3,6.

W tym artykule możemy zainicjować klastra usługi Hadoop z usługą Data Lake Store jako dodatkowego magazynu. Aby uzyskać instrukcje dotyczące sposobu tworzenia klastra usługi Hadoop z usługi Data Lake Store jako domyślnego magazynu, zobacz [tworzenia klastra usługi HDInsight z Data Lake Store przy użyciu portalu Azure](data-lake-store-hdinsight-hadoop-use-portal.md).

## <a name="prerequisites"></a>Wymagania wstępne
Przed przystąpieniem do wykonania kroków opisanych w tym samouczku należy dysponować następującymi elementami:

* **Subskrypcja platformy Azure**. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Program Azure PowerShell 1.0 lub nowszy**. Zobacz artykuł [Instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azure/overview).
* **Nazwy głównej usługi Azure Active Directory usługi**. Kroki opisane w tym samouczku zawierają instrukcje dotyczące sposobu tworzenia nazwy głównej usługi w usłudze Azure AD. Jednak musi być administrator usługi Azure AD, aby można było utworzyć nazwy głównej usługi. Jeśli jesteś administratorem usługi Azure AD, możesz pominąć te wymagania wstępne i kontynuować samouczka.

    **Jeśli nie jesteś administratorem usługi Azure AD**, nie można wykonać kroki wymagane do utworzenia nazwy głównej usługi. W takim przypadku administrator usługi Azure AD należy najpierw utworzyć nazwy głównej usługi przed utworzeniem klastra usługi HDInsight z usługą Data Lake Store. Ponadto nazwy głównej usługi musi być utworzony przy użyciu certyfikatu, zgodnie z opisem w [Tworzenie nazwy głównej usługi o certyfikat](../azure-resource-manager/resource-group-authenticate-service-principal.md#create-service-principal-with-certificate-from-certificate-authority).

## <a name="create-an-hdinsight-cluster-with-azure-data-lake-store"></a>Tworzenie klastra usługi HDInsight z usługi Azure Data Lake Store
Szablon usługi Resource Manager i wymagań wstępnych dotyczących używania szablonu, są dostępne w witrynie GitHub pod [wdrażanie klastra usługi HDInsight Linux z nowej usługi Data Lake Store](https://github.com/Azure/azure-quickstart-templates/tree/master/201-hdinsight-datalake-store-azure-storage). Postępuj zgodnie z instrukcjami w to łącze, aby utworzyć klaster usługi HDInsight za pomocą usługi Azure Data Lake Store jako dodatkowego magazynu.

Instrukcje w wymienionych powyżej łącze wymagają programu PowerShell. Przed rozpoczęciem pracy z tych instrukcji upewnij się, że logujesz się do konta platformy Azure. Na pulpicie otwórz nowe okno programu Azure PowerShell, a następnie wprowadź poniższe fragmenty kodu. Po wyświetleniu monitu zaloguj się jako jeden z administratorów/właścicieli subskrypcji:

```
# Log in to your Azure account
Connect-AzureRmAccount

# List all the subscriptions associated to your account
Get-AzureRmSubscription

# Select a subscription
Set-AzureRmContext -SubscriptionId <subscription ID>
```

## <a name="upload-sample-data-to-the-azure-data-lake-store"></a>Przekaż przykładowe dane do usługi Azure Data Lake Store
Szablon usługi Resource Manager tworzy nowe konto usługi Data Lake Store i kojarzy ją z klastrem usługi HDInsight. Teraz należy przesłać przykładowych danych do usługi Data Lake Store. Te dane potrzebne później w samouczku do uruchomienia zadań z klastra usługi HDInsight, że dostęp do danych w usłudze Data Lake Store. Aby uzyskać instrukcje na temat przekazywania danych, zobacz [przekazania pliku do usługi Data Lake Store](data-lake-store-get-started-portal.md#uploaddata). Jeśli szukasz przykładowych danych do przekazania, możesz pobrać folder **Ambulance Data** z [repozytorium Git usługi Azure Data Lake](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData).

## <a name="set-relevant-acls-on-the-sample-data"></a>Ustawić odpowiednich list ACL na przykładowych danych
Aby upewnić się, że jest dostępny z klastra usługi HDInsight przykładowe dane, które zostaną przesłane, upewnij się, że jest używany do ustanawiania tożsamości między klastrem usługi HDInsight i usługi Data Lake Store miał dostęp do pliku/folderu, który próbujesz uzyskać dostęp do aplikacji usługi Azure AD. Aby to zrobić, wykonaj następujące czynności.

1. Znajdź nazwę aplikacji usługi Azure AD, który jest skojarzony z klastrem usługi HDInsight i usługi Data Lake Store. Jednym ze sposobów Szukaj nazwy jest, aby otworzyć blok klastra usługi HDInsight utworzonego przy użyciu szablonu usługi Resource Manager, kliknij przycisk **tożsamość usługi AAD klastra** , a następnie wyszukaj wartość **nazwę wyświetlaną nazwę główną usługi**.
2. Teraz zapewniają dostęp do tej aplikacji usługi Azure AD na plik lub folder, który chcesz uzyskać dostęp z klastrem usługi HDInsight. Aby ustawić prawo listy ACL na plik lub folder, w usłudze Data Lake Store, zobacz [Zabezpieczanie danych w usłudze Data Lake Store](data-lake-store-secure-data.md#filepermissions).

## <a name="run-test-jobs-on-the-hdinsight-cluster-to-use-the-data-lake-store"></a>Uruchom zadania testowe w klastrze usługi HDInsight do użycia usługi Data Lake Store
Po skonfigurowaniu klastra usługi HDInsight można uruchamiać zadania testowego w klastrze, aby sprawdzić, czy klastra usługi HDInsight można uzyskać dostępu do usługi Data Lake Store. Aby to zrobić, zostanie uruchomiony przykładowe zadania Hive, która tworzy tabelę przy użyciu przykładowych danych, który został wcześniej przekazany do usługi Data Lake Store.

W tej sekcji zostanie SSH do klastra usługi HDInsight w systemie Linux i uruchomić przykładowe zapytanie Hive. Jeśli używasz klienta z systemem Windows, firma Microsoft zaleca używanie **PuTTY**, który można pobrać z [ http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html ](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

Aby uzyskać więcej informacji na temat używania programu PuTTY, zobacz [używanie SSH z opartą na systemie Linux platformą Hadoop w usłudze HDInsight z systemu Windows ](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md).

1. Po nawiązaniu połączenia, należy uruchomić interfejsu wiersza polecenia Hive za pomocą następującego polecenia:

   ```
   hive
   ```
2. Przy użyciu interfejsu wiersza polecenia, wpisz poniższe instrukcje, aby utworzyć nową tabelę o nazwie **pojazdów** przy użyciu przykładowych danych w usłudze Data Lake Store:

   ```
   DROP TABLE vehicles;
   CREATE EXTERNAL TABLE vehicles (str string) LOCATION 'adl://<mydatalakestore>.azuredatalakestore.net:443/';
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


## <a name="access-data-lake-store-using-hdfs-commands"></a>Dostęp Data Lake Store za pomocą poleceń systemu plików HDFS
Po skonfigurowaniu klastra usługi HDInsight do użycia usługi Data Lake Store służy poleceń powłoki systemu plików HDFS można uzyskać dostępu do magazynu.

W tej sekcji zostanie SSH w systemie Linux usługi HDInsight klastra, a następnie uruchom polecenia systemu plików HDFS. Jeśli używasz klienta z systemem Windows, firma Microsoft zaleca używanie **PuTTY**, który można pobrać z [ http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html ](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

Aby uzyskać więcej informacji na temat używania programu PuTTY, zobacz [używanie SSH z opartą na systemie Linux platformą Hadoop w usłudze HDInsight z systemu Windows ](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md).

Po nawiązaniu połączenia użyj następującego polecenia systemu plików HDFS, aby wyświetlić listę plików w usłudze Data Lake Store.

```
hdfs dfs -ls adl://<Data Lake Store account name>.azuredatalakestore.net:443/
```

Powinny pojawić się plik, który został wcześniej przekazany do usługi Data Lake Store.

```
15/09/17 21:41:15 INFO web.CaboWebHdfsFileSystem: Replacing original urlConnectionFactory with org.apache.hadoop.hdfs.web.URLConnectionFactory@21a728d6
Found 1 items
-rwxrwxrwx   0 NotSupportYet NotSupportYet     671388 2015-09-16 22:16 adl://mydatalakestore.azuredatalakestore.net:443/mynewfolder
```

Można również użyć `hdfs dfs -put` polecenie, aby przekazać pliki do usługi Data Lake Store, a następnie użyj `hdfs dfs -ls` Aby sprawdzić, czy pliki zostały pomyślnie przekazane.


## <a name="next-steps"></a>Kolejne kroki
* [Kopiowanie danych z obiektów blob magazynu Azure do usługi Data Lake Store](data-lake-store-copy-data-wasb-distcp.md)
* [Użyj Data Lake Store z klastrami Azure HDInsight](../hdinsight/hdinsight-hadoop-use-data-lake-store.md)
