---
title: Tworzenie klastrów usługi Apache Hadoop za pomocą klasycznego wiersza polecenia platformy Azure — Azure HDInsight
description: Dowiedz się, jak tworzyć klastry HDInsight przy użyciu dla wielu platform klasycznego wiersza polecenia platformy Azure.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/27/2018
ms.author: hrasheed
ms.openlocfilehash: 93ee51c8a00e5cfcbffd56f96b627b68dd124aea
ms.sourcegitcommit: f0c2758fb8ccfaba76ce0b17833ca019a8a09d46
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/06/2018
ms.locfileid: "51034697"
---
# <a name="create-hdinsight-clusters-using-the-azure-classic-cli"></a>Tworzenie klastrów HDInsight za pomocą klasycznego wiersza polecenia platformy Azure

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Kroki opisane w tym przewodniku dokumentu, tworzenie klastra HDInsight 3.5 przy użyciu klasycznego wiersza polecenia platformy Azure.

[!INCLUDE [classic-cli-warning](../../includes/requires-classic-cli.md)]

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

* **Subskrypcja platformy Azure**. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

* **Klasyczny interfejs wiersza polecenia Azure**. Kroki opisane w tym dokumencie ostatnio zostały przetestowane za pomocą klasycznego wiersza polecenia platformy Azure w wersji 0.10.14.

## <a name="log-in-to-your-azure-subscription"></a>Logowanie się do subskrypcji platformy Azure

Wykonaj kroki opisane w temacie [nawiązywanie połączenia z subskrypcją platformy Azure z interfejsu wiersza polecenia platformy Azure](/cli/azure/authenticate-azure-cli) i nawiąż połączenie z subskrypcją za pomocą **logowania** metody.

## <a name="create-a-cluster"></a>Tworzenie klastra

Poniższe kroki należy wykonać z poziomu wiersza polecenia, takiego jak program PowerShell lub Bash.

1. Do uwierzytelniania w Twojej subskrypcji platformy Azure, użyj następującego polecenia:

        azure login

    Monit o podanie swojej nazwy i hasła. Jeśli masz wiele subskrypcji platformy Azure, użyj `azure account set <subscriptionname>` można ustawić subskrypcję, która używać klasycznych poleceń interfejsu wiersza polecenia.

2. Włącz tryb usługi Azure Resource Manager za pomocą następującego polecenia:

        azure config mode arm

3. Utwórz grupę zasobów. Ta grupa zasobów zawiera klaster HDInsight i skojarzone konto magazynu.

        azure group create groupname location

    * Zastąp `groupname` unikatową nazwą grupy.

    * Zastąp `location` z regionu geograficznego, który chcesz utworzyć grupę w.

       Aby uzyskać listę prawidłowych lokalizacji, należy użyć `azure location list` polecenia, a następnie użyj jednej z lokalizacji z `Name` kolumny.

4. Tworzenie konta magazynu To konto magazynu jest używana jako magazyn domyślny dla klastra HDInsight.

        azure storage account create -g groupname --sku-name RAGRS -l location --kind Storage storagename

    * Zastąp `groupname` o nazwie grupy utworzone w poprzednim kroku.

    * Zastąp `location` przy użyciu tej samej lokalizacji, które są używane w poprzednim kroku.

    * Zastąp `storagename` unikatową nazwą konta magazynu.

        > [!NOTE]
        > Aby uzyskać więcej informacji na temat parametrów użytych w tym poleceniu, należy użyć `azure storage account create -h` Aby wyświetlić Pomoc dla tego polecenia.

5. Pobierz klucz używany do uzyskania dostępu do konta magazynu.

        azure storage account keys list -g groupname storagename

    * Zastąp `groupname` nazwą grupy zasobów.
    * Zastąp `storagename` nazwą konta magazynu.

     W danych, który jest zwracany, Zapisz `key` wartość `key1`.

6. Tworzenie klastra usługi HDInsight.

        azure hdinsight cluster create -g groupname -l location -y Linux --clusterType Hadoop --defaultStorageAccountName storagename.blob.core.windows.net --defaultStorageAccountKey storagekey --defaultStorageContainer clustername --workerNodeCount 3 --userName admin --password httppassword --sshUserName sshuser --sshPassword sshuserpassword clustername

    * Zastąp `groupname` nazwą grupy zasobów.

    * Zastąp `Hadoop` z typem klastra, który chcesz utworzyć. Na przykład `Hadoop`, `HBase`, `Kafka`, `Spark`, lub `Storm`.

     > [!IMPORTANT]
     > HDInsight mogą mieć różne typy, które odnoszą się do obciążenia lub technologii, że klaster jest ona dostrojona dla klastrów. Nie istnieje obsługiwana metoda do tworzenia klastra, który łączy wiele typów, takich jak Storm i bazy danych HBase w jednym klastrze.

    * Zastąp `location` przy użyciu tej samej lokalizacji, które są używane w poprzednich krokach.

    * Zastąp `storagename` przy użyciu nazwy konta magazynu.

    * Zastąp `storagekey` przy użyciu klucza uzyskanego w poprzednim kroku.

    * Aby uzyskać `--defaultStorageContainer` parametr, użyj takiej samej nazwie jak korzystania z klastra.

    * Zastąp `admin` i `httppassword` przy użyciu nazwy i hasła, które mają być używane podczas uzyskiwania dostępu do klastra za pośrednictwem protokołu HTTPS.

    * Zastąp `sshuser` i `sshuserpassword` przy użyciu nazwy użytkownika i hasło, które mają być używane podczas uzyskiwania dostępu do klastra przy użyciu protokołu SSH

    > [!IMPORTANT]
    > W tym przykładzie tworzy klaster z dwoma węzłami procesu roboczego. Po utworzeniu klastra można również zmienić liczbę węzłów procesu roboczego przez wykonanie operacji skalowania. Jeśli planujesz użycie więcej niż 32 węzły procesu roboczego, musisz wybrać rozmiar węzła głównego z co najmniej 8 rdzeniami i 14 GB pamięci RAM. Należy określić rozmiar węzła głównego przy użyciu `--headNodeSize` parametru podczas tworzenia klastra.
    >
    > Aby uzyskać więcej informacji o rozmiarach węzła i powiązanych kosztach, zobacz [Cennik usługi HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

    Może upłynąć kilka minut na zakończenie procesu tworzenia klastra. Zwykle około 15.

## <a name="troubleshoot"></a>Rozwiązywanie problemów

W razie problemów podczas tworzenia klastrów usługi HDInsight zapoznaj się z [wymaganiami dotyczącymi kontroli dostępu](hdinsight-administer-use-portal-linux.md#create-clusters).

## <a name="next-steps"></a>Kolejne kroki

Teraz, że udało Ci się utworzyć klaster usługi HDInsight przy użyciu klasyczny interfejs wiersza polecenia, należy użyć następującego, aby dowiedzieć się, jak pracować z klastrem:

### <a name="hadoop-clusters"></a>Klastry usługi Hadoop

* [Korzystanie z programu Hive z usługą HDInsight](hadoop/hdinsight-use-hive.md)
* [Korzystanie z języka Pig z usługą HDInsight](hadoop/hdinsight-use-pig.md)
* [Używanie technologii MapReduce z HDInsight](hadoop/hdinsight-use-mapreduce.md)

### <a name="hbase-clusters"></a>Klastry baz danych HBase

* [Wprowadzenie do usługi HBase w usłudze HDInsight](hbase/apache-hbase-tutorial-get-started-linux.md)
* [Opracowywanie aplikacji w języku Java dla bazy danych HBase na HDInsight](hbase/apache-hbase-build-java-maven-linux.md)

### <a name="storm-clusters"></a>Klastry STORM

* [Opracowywanie topologii języka Java dla systemu Storm w HDInsight](storm/apache-storm-develop-java-topology.md)
* [Użyj języka Python składników systemu Storm w HDInsight](storm/apache-storm-develop-python-topology.md)
* [Wdrażanie i monitorowanie topologii z systemu Storm w HDInsight](storm/apache-storm-deploy-monitor-topology-linux.md)
