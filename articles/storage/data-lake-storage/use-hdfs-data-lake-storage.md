---
title: Przy użyciu interfejsu wiersza polecenia systemu plików HDFS za pomocą usługi Azure Data Lake Storage Gen2 (wersja zapoznawcza)
description: Wprowadzenie do systemu plików HDFS interfejs wiersza polecenia dla Data Lake Gen2 — wersja zapoznawcza
services: storage
author: artemuwka
ms.service: storage
ms.topic: article
ms.date: 06/27/2018
ms.author: artek
ms.component: data-lake-storage-gen2
ms.openlocfilehash: c5f11cbb12b727f5f308d7a71c51706fa8ec373f
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/08/2018
ms.locfileid: "51277090"
---
# <a name="using-the-hdfs-cli-with-data-lake-storage-gen2"></a>Przy użyciu interfejsu wiersza polecenia systemu plików HDFS z Gen2 Lake magazynu danych

Usługa Azure Data Lake Gen2 — wersja zapoznawcza pozwala zarządzać i uzyskiwać dostęp do danych, tak samo, jak za pomocą [pliku System (HDFS, Hadoop Distributed)](http://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html). Czy istnieje klastra usługi HDInsight, dołączony, lub uruchomić zadanie platformy Apache Spark przy użyciu usługi Azure Databricks w celu przeprowadzenia analizy danych przechowywanych na koncie usługi Azure Storage, można użyć interfejsu wiersza polecenia (CLI) do pobrania i manipulowania nimi załadowanych danych.

## <a name="hdfs-cli-with-hdinsight"></a>System plików HDFS interfejsu wiersza polecenia przy użyciu HDInsight

Usługa HDInsight zapewnia dostęp do rozproszonego systemu plików, który jest lokalnie dołączony do węzłów obliczeniowych. W tym systemie plików jest możliwy za pomocą powłoki, który wchodzi w interakcję bezpośrednio z systemu plików HDFS i innych systemów plików, które obsługuje platformy Hadoop. Poniżej przedstawiono typowe polecenia i linki do przydatnych zasobów.

>[!IMPORTANT]
>Klaster HDInsight rozliczeń rozpoczyna się po utworzeniu klastra i zatrzymuje, gdy klaster jest usuwany. Opłaty są naliczane za minutę, więc jeśli klaster nie jest używany, należy go usunąć. Aby dowiedzieć się, jak usunąć klaster, zobacz nasze [artykuł na temat](../../hdinsight/hdinsight-delete-cluster.md). Jednak dane przechowywane na koncie magazynu w usłudze Data Lake magazynu Gen2 — włączone będzie nadal występował mimo klastra usługi HDInsight zostanie usunięty.

Aby uzyskać listę plików lub katalogów:

    hdfs dfs -ls <args>
Aby utworzyć katalog:

    hdfs dfs -mkdir [-p] <paths>
Aby usunąć pliku lub katalogu:

    hdfs dfs -rm [-skipTrash] URI [URI ...]


Poświęćmy teraz klastra usługi HDInsight Hadoop w systemie Linux, na przykład. Aby korzystać z interfejsu wiersza polecenia systemu plików HDFS, najpierw należy ustanowić [zdalny dostęp do usług](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-information#remote-access-to-services). W przypadku wybrania [SSH](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix) przykładowego kodu programu PowerShell będzie wyglądać w następujący sposób:
```PowerShell
#Connect to the cluster via SSH.
ssh sshuser@clustername-ssh.azurehdinsight.net
#Execute basic HDFS commands. Display the hierarchy.
hdfs dfs -ls /
#Create a sample directory.
hdfs dfs -mkdir /samplefolder
```

Parametry połączenia znajduje się w temacie "klastra i protokołu SSH logowania" części bloku klastra HDInsight w witrynie Azure portal. Poświadczenia protokołu SSH zostały określone podczas tworzenia klastra.

Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia systemu plików HDFS, zobacz [oficjalnej dokumentacji](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html) i [Przewodnik po uprawnieniach systemu plików HDFS](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html).

## <a name="hdfs-cli-with-azure-databricks"></a>System plików HDFS interfejsu wiersza polecenia za pomocą usługi Azure Databricks

Databricks zapewnia łatwy w użyciu interfejsu wiersza polecenia platformy korzystających z interfejsu API REST usługi Databricks. Projekt typu open-source znajduje się na [GitHub](https://github.com/databricks/databricks-cli). Poniżej przedstawiono często używanych poleceń.

Aby uzyskać listę plików lub katalogów:

    dbfs ls [-l]
Aby utworzyć katalog:

    dbfs mkdirs
Aby usunąć plik:

    dbfs rm [-r]

Inny sposób interakcji z usługi Databricks są notesy. Chociaż notesu ma podstawowy język, określając język % magiczne polecenie języka na początku komórka można łączyć różne języki. W szczególności % sh umożliwia wykonanie kodu powłoki w notesie podobne w przykładzie HDInsight we wcześniejszej części tego artykułu.

Aby uzyskać listę plików lub katalogów:

    %sh ls <args>
Aby utworzyć katalog:

    %sh mkdir [-p] <paths>
Aby usunąć pliku lub katalogu:

    %sh rm [-skipTrash] URI [URI ...]

Po uruchomieniu klastra Spark w usłudze Azure Databricks, utworzysz nowy notes. Przykładowy skrypt notesu będzie wyglądać w następujący sposób:

    #Execute basic HDFS commands invoking the shell. Display the hierarchy.
    %sh ls /
    #Create a sample directory.
    %sh mkdir /samplefolder

Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia Databricks, zobacz [oficjalną dokumentacją](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html). Aby uzyskać więcej informacji na temat notesów, zobacz [notesów](https://docs.azuredatabricks.net/user-guide/notebooks/index.html) sekcji dokumentacji.

## <a name="next-steps"></a>Kolejne kroki

- [Tworzenie klastra usługi HDInsight przy użyciu usługi Azure Data Lake Storage Gen2](./quickstart-create-connect-hdi-cluster.md)
- [Użyj konta usługi Azure Data Lake Storage Gen2 możliwością w usłudze Azure Databricks](./quickstart-create-databricks-account.md) 