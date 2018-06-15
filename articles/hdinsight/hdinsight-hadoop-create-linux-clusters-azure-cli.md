---
title: Tworzenie klastrów Hadoop przy użyciu wiersza polecenia-Azure HDInsight | Dokumentacja firmy Microsoft
description: Informacje o sposobie tworzenia klastrów usługi HDInsight przy użyciu 1.0 interfejsu wiersza polecenia platformy Azure i platform.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 50b01483-455c-4d87-b754-2229005a8ab9
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 02/27/2018
ms.author: larryfr
ms.openlocfilehash: e56829c771ae47933f79c519920a20c1308873fe
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
ms.locfileid: "31397590"
---
# <a name="create-hdinsight-clusters-using-the-azure-cli"></a>Tworzenie klastrów usługi HDInsight przy użyciu wiersza polecenia platformy Azure

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Kroki opisane w tym przewodniku dokumentu tworzenia klastra usługi HDInsight 3.5 przy użyciu 1.0 interfejsu wiersza polecenia platformy Azure.

> [!IMPORTANT]
> W tym temacie opisano, jak używać 1.0 interfejsu wiersza polecenia platformy Azure do tworzenia klastra usługi HDInsight. Ta wersja interfejsu wiersza polecenia jest przestarzały i nie dodano obsługę tworzenia klastrów usługi HDInsight 2.0 interfejsu wiersza polecenia platformy Azure.
>
> Tworzenie i zarządzanie nimi w usłudze hdinsight umożliwia także programu Azure PowerShell. Aby uzyskać więcej informacji, zobacz [Tworzenie klastrów usługi HDInsight przy użyciu programu Azure PowerShell](hdinsight-hadoop-create-linux-clusters-azure-powershell.md) dokumentu.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

* **Subskrypcja platformy Azure**. Zobacz artykuł [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

* **Interfejs wiersza polecenia platformy Azure**. Z wiersza polecenia platformy Azure w wersji 0.10.14 ostatnio przetestowane kroki opisane w tym dokumencie.

    > [!IMPORTANT]
    > Azure CLI 1.0 jest przestarzały i nie dodano obsługę tworzenia klastrów usługi HDInsight 2.0 interfejsu wiersza polecenia platformy Azure.

## <a name="log-in-to-your-azure-subscription"></a>Logowanie się do subskrypcji platformy Azure

Wykonaj kroki opisane w temacie [Nawiązywanie połączenia z subskrypcją platformy Azure za pomocą interfejsu wiersza polecenia platformy Azure (Azure CLI)](/cli/azure/authenticate-azure-cli) i nawiąż połączenie z subskrypcją za pomocą metody **logowania**.

## <a name="create-a-cluster"></a>Tworzenie klastra

Poniższe kroki należy wykonać z poziomu wiersza polecenia, takich jak programu PowerShell lub Bash.

1. Do uwierzytelniania do subskrypcji platformy Azure, użyj następującego polecenia:

        azure login

    Zostanie wyświetlony monit podaj swoją nazwę i hasło. Jeśli masz wiele subskrypcji Azure, użyj `azure account set <subscriptionname>` można ustawić subskrypcję, która używać polecenia interfejsu wiersza polecenia Azure.

2. Włącz tryb usługi Azure Resource Manager za pomocą następującego polecenia:

        azure config mode arm

3. Utwórz grupę zasobów. Ta grupa zasobów zawiera klastra usługi HDInsight i skojarzone konto magazynu.

        azure group create groupname location

    * Zastąp `groupname` z unikatową nazwę grupy.

    * Zastąp `location` z regionu geograficznego, który chcesz utworzyć grupę w.

       Aby uzyskać listę prawidłowych lokalizacji, należy użyć `azure location list` polecenia, a następnie użyj jednej z lokalizacji z `Name` kolumny.

4. Tworzenie konta magazynu To konto magazynu jest używany jako domyślny magazyn dla klastra usługi HDInsight.

        azure storage account create -g groupname --sku-name RAGRS -l location --kind Storage storagename

    * Zastąp `groupname` o nazwie grupy utworzony w poprzednim kroku.

    * Zastąp `location` z tej samej lokalizacji, które są używane w poprzednim kroku.

    * Zastąp `storagename` o unikatowej nazwie dla konta magazynu.

        > [!NOTE]
        > Aby uzyskać więcej informacji na parametry używane w tym poleceniu, użyj `azure storage account create -h` Aby wyświetlić Pomoc dla tego polecenia.

5. Pobierz klucz używany do uzyskania dostępu do konta magazynu.

        azure storage account keys list -g groupname storagename

    * Zastąp `groupname` z nazwą grupy zasobów.
    * Zastąp `storagename` z nazwą konta magazynu.

     W danych, która jest zwracana, zapisywać `key` wartość `key1`.

6. Tworzenie klastra usługi HDInsight.

        azure hdinsight cluster create -g groupname -l location -y Linux --clusterType Hadoop --defaultStorageAccountName storagename.blob.core.windows.net --defaultStorageAccountKey storagekey --defaultStorageContainer clustername --workerNodeCount 3 --userName admin --password httppassword --sshUserName sshuser --sshPassword sshuserpassword clustername

    * Zastąp `groupname` z nazwą grupy zasobów.

    * Zastąp `Hadoop` z typem klastra, który chcesz utworzyć. Na przykład `Hadoop`, `HBase`, `Kafka`, `Spark`, lub `Storm`.

     > [!IMPORTANT]
     > HDInsight mogą mieć różnych typów, które odpowiadają obciążenia lub technologii, która jest dostosowana klastra na potrzeby klastrów. Nie istnieje metoda obsługiwanych do utworzenia klastra, który łączy wiele typów, takie jak Storm i bazy danych HBase w jednym klastrze.

    * Zastąp `location` z tej samej lokalizacji, które są używane w poprzednich krokach.

    * Zastąp `storagename` nazwy konta magazynu.

    * Zastąp `storagekey` z kluczem uzyskanych w poprzednim kroku.

    * Aby uzyskać `--defaultStorageContainer` parametru, użyj takiej samej nazwie, jak jest używany dla klastra.

    * Zastąp `admin` i `httppassword` przy użyciu nazwy i hasła ma zostać użyty podczas uzyskiwania dostępu do klastra za pośrednictwem protokołu HTTPS.

    * Zastąp `sshuser` i `sshuserpassword` przy użyciu nazwy użytkownika i hasła, które mają być używane podczas uzyskiwania dostępu do klastra przy użyciu protokołu SSH

    > [!IMPORTANT]
    > W tym przykładzie jest tworzony klaster z dwoma węzłami procesu roboczego. Liczba węzłów procesu roboczego po utworzeniu klastra można również zmienić przez wykonanie operacji skalowania. Jeśli planujesz używanie więcej niż 32 węzłami procesów roboczych, następnie należy wybrać rozmiar węzła głównego z co najmniej 8 rdzeni i 14 GB pamięci RAM. Należy określić rozmiaru węzła głównego przy użyciu `--headNodeSize` parametru podczas tworzenia klastra.
    >
    > Aby uzyskać więcej informacji na węzeł rozmiary i koszty, zobacz [cennik usługi HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

    Może upłynąć kilka minut na zakończenie procesu tworzenia klastra. Zazwyczaj około 15.

## <a name="troubleshoot"></a>Rozwiązywanie problemów

W razie problemów podczas tworzenia klastrów usługi HDInsight zapoznaj się z [wymaganiami dotyczącymi kontroli dostępu](hdinsight-administer-use-portal-linux.md#create-clusters).

## <a name="next-steps"></a>Kolejne kroki

Teraz, że pomyślnie utworzono klaster usługi HDInsight przy użyciu wiersza polecenia platformy Azure, aby dowiedzieć się, jak pracować z klastra należy użyć następującego:

### <a name="hadoop-clusters"></a>Klastry Hadoop

* [Korzystanie z programu Hive z usługą HDInsight](hadoop/hdinsight-use-hive.md)
* [Korzystanie z języka Pig z usługą HDInsight](hadoop/hdinsight-use-pig.md)
* [Korzystać z usługi MapReduce z usługą HDInsight](hadoop/hdinsight-use-mapreduce.md)

### <a name="hbase-clusters"></a>Klastrów HBase

* [Wprowadzenie do usługi HBase w usłudze HDInsight](hbase/apache-hbase-tutorial-get-started-linux.md)
* [Tworzenie aplikacji Java bazy danych hbase w usłudze HDInsight](hbase/apache-hbase-build-java-maven-linux.md)

### <a name="storm-clusters"></a>Klastry STORM

* [Tworzenie topologii Java dla Storm w usłudze HDInsight](storm/apache-storm-develop-java-topology.md)
* [Użyj składników języka Python w Storm w usłudze HDInsight](storm/apache-storm-develop-python-topology.md)
* [Wdrażanie i monitorowanie topologii z systemu Storm w usłudze HDInsight](storm/apache-storm-deploy-monitor-topology-linux.md)
