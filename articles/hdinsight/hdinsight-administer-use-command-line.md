---
title: Zarządzanie klastrami usługi Hadoop za pomocą wiersza polecenia platformy Azure — Azure HDInsight
description: Dowiedz się, jak zarządzać klastrami Hadoop w usłudze Azure HDInsight przy użyciu interfejsu wiersza polecenia platformy Azure. Interfejs wiersza polecenia platformy Azure działa w Windows, Mac i Linux.
services: hdinsight
editor: jasonwhowell
author: jasonwhowell
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 05/14/2018
ms.author: jasonh
ms.openlocfilehash: dea0f004c4283bf594e46097092a52dedabb9f4b
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/07/2018
ms.locfileid: "39599024"
---
# <a name="manage-hadoop-clusters-in-hdinsight-using-the-azure-cli"></a>Zarządzanie klastrami Hadoop w HDInsight przy użyciu wiersza polecenia platformy Azure
[!INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

Dowiedz się, jak używać [interfejsu wiersza polecenia platformy Azure](../cli-install-nodejs.md) do zarządzania klastrami Hadoop w usłudze Azure HDInsight. Interfejs wiersza polecenia platformy Azure został zaimplementowany w środowisku Node.js. Można go używać na dowolnej platformie, która obsługuje środowisko Node.js, w tym w systemie Windows, Mac i Linux. Obecnie nie obsługuje HDInsight [interfejsu wiersza polecenia platformy Azure w wersji 2.0](https://docs.microsoft.com/cli/azure).

W tym artykule opisano tylko przy użyciu wiersza polecenia platformy Azure przy użyciu HDInsight. Aby uzyskać ogólne wskazówki dotyczące sposobu używania wiersza polecenia platformy Azure, zobacz [Instalowanie i Konfigurowanie interfejsu wiersza polecenia platformy Azure][azure-command-line-tools].

## <a name="prerequisites"></a>Wymagania wstępne
Przed rozpoczęciem korzystania z informacji zawartych w tym artykule należy dysponować następującymi elementami:

* **Subskrypcja platformy Azure**. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* **Interfejs wiersza polecenia platformy Azure** — informacje na temat instalacji i konfiguracji można znaleźć w temacie [Instalowanie i konfigurowanie interfejsu wiersza polecenia platformy Azure](../cli-install-nodejs.md).
* **Łączenie z platformą Azure**, używając następującego polecenia:

    ```cli
    azure login
    ```
  
    Więcej informacji na temat uwierzytelniania za pomocą konta służbowego lub szkolnego znajdziesz w temacie [Połączenie z subskrypcją platformy Azure z poziomu interfejsu wiersza polecenia platformy Azure](/cli/azure/authenticate-azure-cli).
* **Przełączenie w tryb usługi Azure Resource Manager** przy użyciu następującego polecenia:
  
    ```cli
    azure config mode arm
    ```

Aby uzyskać pomoc, należy użyć **-h** przełącznika.  Na przykład:

```cli
azure hdinsight cluster create -h
```

## <a name="create-clusters-with-the-cli"></a>Tworzenie klastrów przy użyciu interfejsu wiersza polecenia
Zobacz [Tworzenie klastrów w HDInsight przy użyciu wiersza polecenia platformy Azure](hdinsight-hadoop-create-linux-clusters-azure-cli.md).

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
Aby zmienić rozmiar klastra usługi Hadoop:

```cli
azure hdinsight cluster resize [options] <clusterName> <Target Instance Count>
```


## <a name="enabledisable-http-access-for-a-cluster"></a>Włącz/Wyłącz dostęp HTTP dla klastra

```cli
azure hdinsight cluster enable-http-access [options] <Cluster Name> <userName> <password>
azure hdinsight cluster disable-http-access [options] <Cluster Name>
```

## <a name="enabledisable-rdp-access-for-a-cluster"></a>Włącz/Wyłącz dostęp RDP dla klastra

```cli
azure hdinsight cluster enable-rdp-access [options] <Cluster Name> <rdpUserName> <rdpPassword> <rdpExpiryDate>
azure hdinsight cluster disable-rdp-access [options] <Cluster Name>
```

## <a name="next-steps"></a>Kolejne kroki
W tym artykule mają przedstawiono sposób wykonywania różnych zadań administracyjnych klastra HDInsight. Aby dowiedzieć się więcej, zobacz następujące artykuły:

* [Administrowanie HDInsight za pomocą witryny Azure Portal][hdinsight-admin-portal]
* [Administrowanie HDInsight za pomocą programu Azure PowerShell][hdinsight-admin-powershell]
* [Wprowadzenie do usługi Azure HDInsight][hdinsight-get-started]
* [Jak używać interfejsu wiersza polecenia platformy Azure][azure-command-line-tools]

[azure-command-line-tools]: ../cli-install-nodejs.md
[azure-create-storageaccount]:../storage/common/storage-create-storage-account.md
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/


[hdinsight-admin-portal]: hdinsight-administer-use-management-portal.md
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-get-started]:hadoop/apache-hadoop-linux-tutorial-get-started.md

[image-cli-account-download-import]: ./media/hdinsight-administer-use-command-line/HDI.CLIAccountDownloadImport.png
[image-cli-clustercreation]: ./media/hdinsight-administer-use-command-line/HDI.CLIClusterCreation.png
[image-cli-clustercreation-config]: ./media/hdinsight-administer-use-command-line/HDI.CLIClusterCreationConfig.png
[image-cli-clusterlisting]: ./media/hdinsight-administer-use-command-line/command-line-list-of-clusters.png "Listy i wyświetlaniu klastrów"
