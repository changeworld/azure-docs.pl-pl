---
title: Zarządzanie klastrami Hadoop przy użyciu interfejsu wiersza polecenia Azure - Azure HDInsight | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zarządzać klastrów platformy Hadoop w usłudze Azure HDInsight za pomocą interfejsu wiersza polecenia platformy Azure. Interfejsu wiersza polecenia Azure działa w systemie Windows, Mac i Linux.
services: hdinsight
editor: cgronlun
manager: jhubbard
author: mumian
tags: azure-portal
documentationcenter: ''
ms.assetid: 4f26c79f-8540-44bd-a470-84722a9e4eca
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.devlang: na
ms.topic: conceptual
ms.date: 12/15/2017
ms.author: jgao
ms.openlocfilehash: d8e5d0f09fc5149134370fe8ba48faf256d3688e
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="manage-hadoop-clusters-in-hdinsight-using-the-azure-cli"></a>Zarządzanie klastrami Hadoop w HDInsight przy użyciu wiersza polecenia platformy Azure
[!INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

Dowiedz się, jak używać [interfejsu wiersza polecenia platformy Azure](../cli-install-nodejs.md) do zarządzania klastrami Hadoop w usłudze Azure HDInsight. Interfejs wiersza polecenia platformy Azure został zaimplementowany w środowisku Node.js. Można go używać na dowolnej platformie, która obsługuje środowisko Node.js, w tym w systemie Windows, Mac i Linux. Obecnie nie obsługuje HDInsight [Azure CLI 2.0](https://docs.microsoft.com/cli/azure).

W tym artykule opisano tylko przy użyciu wiersza polecenia platformy Azure z usługą HDInsight. Aby uzyskać ogólne wskazówki dotyczące sposobu używania interfejsu wiersza polecenia Azure, zobacz [Instalowanie i Konfigurowanie interfejsu wiersza polecenia Azure][azure-command-line-tools].

## <a name="prerequisites"></a>Wymagania wstępne
Przed rozpoczęciem korzystania z informacji zawartych w tym artykule należy dysponować następującymi elementami:

* **Subskrypcja platformy Azure**. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* **Interfejs wiersza polecenia platformy Azure** — informacje na temat instalacji i konfiguracji można znaleźć w temacie [Instalowanie i konfigurowanie interfejsu wiersza polecenia platformy Azure](../cli-install-nodejs.md).
* **Połączenia z platformą Azure**, za pomocą następującego polecenia:

    ```cli
    azure login
    ```
  
    Więcej informacji na temat uwierzytelniania za pomocą konta służbowego lub szkolnego znajdziesz w temacie [Połączenie z subskrypcją platformy Azure z poziomu interfejsu wiersza polecenia platformy Azure](/cli/azure/authenticate-azure-cli).
* **Przełączenie w tryb usługi Azure Resource Manager** przy użyciu następującego polecenia:
  
    ```cli
    azure config mode arm
    ```

Aby uzyskać pomoc, użyj **-h** przełącznika.  Na przykład:

```cli
azure hdinsight cluster create -h
```

## <a name="create-clusters-with-the-cli"></a>Tworzenie klastrów z poziomu interfejsu wiersza polecenia
Zobacz [Tworzenie klastrów w usłudze HDInsight przy użyciu interfejsu wiersza polecenia Azure](hdinsight-hadoop-create-linux-clusters-azure-cli.md).

## <a name="list-and-show-cluster-details"></a>Listy i pokazać szczegółów klastra
Użyj następujących poleceń do listy w celu wyświetlenia szczegółów klastra:

```cli
azure hdinsight cluster list
azure hdinsight cluster show <Cluster Name>
```

![Widok wiersza polecenia klastra listy][image-cli-clusterlisting]

## <a name="delete-clusters"></a>Usuwanie klastrów
Aby usunąć klaster, użyj następującego polecenia:

```cli
azure hdinsight cluster delete <Cluster Name>
```

Można również usunąć klaster przez usunięcie grupy zasobów zawierającej klaster. Należy pamiętać, spowoduje to usunięcie wszystkich zasobów w grupie, w tym domyślne konto magazynu.

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

## <a name="enabledisable-rdp-access-for-a-cluster"></a>Włączanie/wyłączanie dostępu RDP dla klastra

```cli
azure hdinsight cluster enable-rdp-access [options] <Cluster Name> <rdpUserName> <rdpPassword> <rdpExpiryDate>
azure hdinsight cluster disable-rdp-access [options] <Cluster Name>
```

## <a name="next-steps"></a>Kolejne kroki
W tym artykule zapoznaniu do wykonywania różnych zadań administracyjnych klastra usługi HDInsight. Aby dowiedzieć się więcej, zobacz następujące artykuły:

* [Administrowanie HDInsight przy użyciu portalu Azure][hdinsight-admin-portal]
* [Administrowanie HDInsight przy użyciu programu Azure PowerShell][hdinsight-admin-powershell]
* [Wprowadzenie do usługi Azure HDInsight][hdinsight-get-started]
* [Sposób użycia interfejsu wiersza polecenia Azure][azure-command-line-tools]

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
[image-cli-clusterlisting]: ./media/hdinsight-administer-use-command-line/command-line-list-of-clusters.png "Lista i Pokaż klastrów"
