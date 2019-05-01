---
title: Zarządzanie klastrami Apache Hadoop za pomocą klasycznego wiersza polecenia platformy Azure — Azure HDInsight
description: Dowiedz się, jak używać platformy Azure klasyczny interfejs wiersza polecenia do zarządzania klastrami Apache Hadoop w usłudze Azure HDInsight.
ms.reviewer: jasonh
author: tylerfox
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 11/06/2018
ms.author: tyfox
ms.openlocfilehash: 94ef5a60ecc5d943d78b16a386660049cc52d82e
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64694459"
---
# <a name="manage-apache-hadoop-clusters-in-hdinsight-using-the-azure-classic-cli"></a>Zarządzanie klastrami Apache Hadoop w HDInsight przy użyciu klasycznego wiersza polecenia platformy Azure
[!INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

Dowiedz się, jak używać [klasyczny interfejs wiersza polecenia platformy Azure](../cli-install-nodejs.md) zarządzanie [Apache Hadoop](https://hadoop.apache.org/) klastrów w usłudze Azure HDInsight. Klasyczny interfejs wiersza polecenia jest zaimplementowana w środowisku Node.js. Można go używać na dowolnej platformie, która obsługuje środowisko Node.js, w tym w systemie Windows, Mac i Linux.

[!INCLUDE [classic-cli-warning](../../includes/requires-classic-cli.md)]

## <a name="prerequisites"></a>Wymagania wstępne
Przed rozpoczęciem korzystania z informacji zawartych w tym artykule należy dysponować następującymi elementami:

* **Subskrypcja platformy Azure**. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* **Klasyczny interfejs wiersza polecenia Azure** — zobacz [Instalowanie i Konfigurowanie interfejsu wiersza polecenia platformy Azure Classic](../cli-install-nodejs.md) informacji o instalacji i konfiguracji.
* **Łączenie z platformą Azure**, używając następującego polecenia:

    ```cli
    azure login
    ```
  
    Aby uzyskać więcej informacji na temat uwierzytelniania, przy użyciu konta służbowego lub szkolnego, zobacz [Połącz z subskrypcją platformy Azure z wiersza polecenia platformy Azure Classic](/cli/azure/authenticate-azure-cli).
* **Przełączenie w tryb usługi Azure Resource Manager** przy użyciu następującego polecenia:
  
    ```cli
    azure config mode arm
    ```

Aby uzyskać pomoc, należy użyć **-h** przełącznika.  Na przykład:

```cli
azure hdinsight cluster create -h
```

## <a name="create-clusters-with-the-cli"></a>Tworzenie klastrów przy użyciu interfejsu wiersza polecenia
Zobacz [Tworzenie klastrów w HDInsight przy użyciu wiersza polecenia platformy Azure Classic](hdinsight-hadoop-create-linux-clusters-azure-cli.md).

## <a name="list-and-show-cluster-details"></a>Listy i wyświetlaniu szczegółów klastra
Aby wyświetlić listę i szczegóły klastra, użyj następujących poleceń:

```cli
azure hdinsight cluster list
azure hdinsight cluster show <Cluster Name>
```

![Widok listy klastrów w wiersza polecenia][image-cli-clusterlisting]

## <a name="delete-clusters"></a>Usuwanie klastrów
Aby usunąć klaster, użyj następującego polecenia:

```cli
azure hdinsight cluster delete <Cluster Name>
```

Możesz także usunąć klaster, usuwając grupę zasobów zawierającą klaster. Należy pamiętać, że spowoduje to usunięcie wszystkich zasobów w grupie, w tym domyślne konto magazynu.

```cli
azure group delete <Resource Group Name>
```

## <a name="scale-clusters"></a>Skalowanie klastrów
Aby zmienić rozmiar klastra Apache Hadoop:

```cli
azure hdinsight cluster resize [options] <clusterName> <Target Instance Count>
```


## <a name="enabledisable-http-access-for-a-cluster"></a>Włącz/Wyłącz dostęp HTTP dla klastra

```cli
azure hdinsight cluster enable-http-access [options] <Cluster Name> <userName> <password>
azure hdinsight cluster disable-http-access [options] <Cluster Name>
```

## <a name="next-steps"></a>Kolejne kroki
W tym artykule mają przedstawiono sposób wykonywania różnych zadań administracyjnych klastra HDInsight. Aby dowiedzieć się więcej, zobacz następujące artykuły:

* [Zarządzanie klastrami Apache Hadoop w HDInsight przy użyciu witryny Azure portal](hdinsight-administer-use-portal-linux.md)
* [Administrowanie HDInsight za pomocą programu Azure PowerShell][hdinsight-admin-powershell]
* [Wprowadzenie do usługi Azure HDInsight][hdinsight-get-started]
* [Jak używać klasycznego wiersza polecenia platformy Azure][azure-command-line-tools]

[azure-command-line-tools]: ../cli-install-nodejs.md
[azure-create-storageaccount]:../storage/common/storage-create-storage-account.md
[azure-purchase-options]: https://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: https://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: https://azure.microsoft.com/pricing/free-trial/

[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-get-started]:hadoop/apache-hadoop-linux-tutorial-get-started.md

[image-cli-account-download-import]: ./media/hdinsight-administer-use-command-line/HDI.CLIAccountDownloadImport.png
[image-cli-clustercreation]: ./media/hdinsight-administer-use-command-line/HDI.CLIClusterCreation.png
[image-cli-clustercreation-config]: ./media/hdinsight-administer-use-command-line/HDI.CLIClusterCreationConfig.png
[image-cli-clusterlisting]: ./media/hdinsight-administer-use-command-line/command-line-list-of-clusters.png "Listy i wyświetlaniu klastrów"
