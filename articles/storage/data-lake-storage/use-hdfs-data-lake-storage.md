---
title: Przy użyciu interfejsu wiersza polecenia systemu plików HDFS z usługi Azure Data Lake Storage Gen2 w wersji zapoznawczej
description: Wprowadzenie do systemu plików HDFS interfejsu wiersza polecenia dla wersji zapoznawczej usługi Data Lake Storage Gen2
services: storage
documentationcenter: ''
author: artemuwka
manager: jahogg
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2018
ms.author: artek
ms.component: data-lake-storage-gen2
ms.openlocfilehash: 75fb07120c78c45d422ee5017eac0afcf0e80859
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37060822"
---
# <a name="using-the-hdfs-cli-with-data-lake-storage-gen2"></a>Przy użyciu interfejsu wiersza polecenia systemu plików HDFS w usłudze Data Lake Storage Gen2

Azure Data Lake magazynu Gen2 podglądu służy do zarządzania i dostęp do danych, tak samo jak w przypadku [systemu Distributed plików Hadoop (HDFS)](http://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html). Czy masz klastra usługi HDInsight dołączony lub uruchom zadanie Apache Spark przy użyciu Azure Databricks do wykonywania analizy na dane przechowywane w usłudze Azure Data Lake magazynu Gen2 służy interfejsu wiersza polecenia (CLI) do pobrania i modyfikowania załadowanych danych. Pozostała część tego artykułu opisano opcje ma podczas [zespołu usługi Magazyn Azure działa na obsługę Eksploratora usługi Storage platformy Azure i portalu Azure](https://azure.microsoft.com/roadmap/) — jedyne!

## <a name="hdfs-cli-with-hdinsight"></a>System plików HDFS interfejsu wiersza polecenia z usługą HDInsight

Usługa HDInsight zapewnia dostęp do rozproszonego systemu plików, który jest lokalnie dołączony do węzłów obliczeniowych. W tym systemie plików jest możliwy przy użyciu powłoki, który bezpośrednio prowadzi interakcję z systemem plików HDFS i innych systemów plików, które obsługuje usługi Hadoop. Poniżej przedstawiono często używanych poleceń i linki do przydatne zasoby.

>[!IMPORTANT]
>Naliczanie opłat rozpoczyna się w momencie utworzenia klastra usługi HDInsight i kończy się wraz z jego usunięciem. Są naliczane proporcjonalnie za minutę, więc zawsze należy usunąć klaster, gdy nie jest już w użyciu (Dowiedz się, jak [usunąć klaster](../../hdinsight/hdinsight-delete-cluster.md)). Jednak dane przechowywane w usłudze Azure Data Lake magazynu Gen2 utrzymuje nawet po usunięciu klastra usługi HDInsight.

Aby uzyskać listę plików lub katalogów:

    hdfs dfs -ls <args>
Do tworzenia katalogu:

    hdfs dfs -mkdir [-p] <paths>
Aby usunąć pliku lub katalogu:

    hdfs dfs -rm [-skipTrash] URI [URI ...]


Teraz wytłumaczone klastra usługi HDInsight Hadoop w systemie Linux jako przykład. Aby korzystać z interfejsu wiersza polecenia systemu plików HDFS, najpierw należy ustanowić [zdalny dostęp do usług](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-hadoop-linux-information#remote-access-to-services). W przypadku wybrania [SSH](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix) przykładowy kod programu PowerShell będzie wyglądać następująco:
```PowerShell
#Connect to the cluster via SSH.
ssh sshuser@clustername-ssh.azurehdinsight.net
#Execute basic HDFS commands. Display the hierarchy.
hdfs dfs -ls /
#Create a sample directory.
hdfs dfs -mkdir /samplefolder
```

Parametry połączenia można znaleźć w folderze "SSH + klastra logowania" części bloku klastra usługi HDInsight w portalu Azure. SSH poświadczenia zostały określone podczas tworzenia klastra.

Aby uzyskać więcej informacji o systemie plików HDFS interfejsu wiersza polecenia, zobacz [oficjalnej dokumentacji](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html) i [przewodnik uprawnienia systemu plików HDFS](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html).

## <a name="hdfs-cli-with-azure-databricks"></a>System plików HDFS interfejsu wiersza polecenia z Azure Databricks

Databricks zapewnia CLI łatwy w użyciu, rozszerzający Databricks interfejsu API REST. Projekt open source znajduje się na [GitHub](https://github.com/databricks/databricks-cli). Poniżej przedstawiono często używanych poleceń.

Aby uzyskać listę plików lub katalogów:

    dbfs ls [-l]
Do tworzenia katalogu:

    dbfs mkdirs
Aby usunąć plik:

    dbfs rm [-r]

Inny sposób interakcji z Databricks są notesów. Podczas Notes ma podstawowy język, można mieszać języków, określając język % magic polecenia języka na początku komórki. W szczególności % sh umożliwia wykonanie kodu powłoki w notesie podobne jak w przykładzie HDInsight wcześniej w tym artykule.

Aby uzyskać listę plików lub katalogów:

    %sh ls <args>
Do tworzenia katalogu:

    %sh mkdir [-p] <paths>
Aby usunąć pliku lub katalogu:

    %sh rm [-skipTrash] URI [URI ...]

Po uruchomieniu klastra Spark w usłudze Azure Databricks, zostanie utworzony nowy notes. Przykładowy skrypt notesu będzie wyglądać w następujący sposób:

    #Execute basic HDFS commands invoking the shell. Display the hierarchy.
    %sh ls /
    #Create a sample directory.
    %sh mkdir /samplefolder

Aby uzyskać więcej informacji o Databricks interfejsu wiersza polecenia, zobacz [oficjalnej dokumentacji](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html). Aby uzyskać więcej informacji na notesów, zobacz [notesów](https://docs.azuredatabricks.net/user-guide/notebooks/index.html) sekcji dokumentacji.

## <a name="next-steps"></a>Kolejne kroki

- [Tworzenie klastra usługi HDInsight z usługi Azure Data Lake magazynu Gen2](./quickstart-create-connect-hdi-cluster.md)
- [Użycie konta usługi Azure Data Lake magazynu Gen2 funkcją w Azure Databricks](./quickstart-create-databricks-account.md) 