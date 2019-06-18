---
title: Za pomocą usługi Azure Data Lake Storage Gen2 przy użyciu interfejsu wiersza polecenia systemu plików HDFS
description: Wprowadzenie do systemu plików HDFS interfejs wiersza polecenia dla Gen2 Lake magazynu danych
services: storage
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: normesta
ms.subservice: data-lake-storage-gen2
ms.reviewer: artek
ms.openlocfilehash: 24123278ff353860ff2af59f4fd77645dfc189e3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64938856"
---
# <a name="using-the-hdfs-cli-with-data-lake-storage-gen2"></a>Przy użyciu interfejsu wiersza polecenia systemu plików HDFS z Gen2 Lake magazynu danych

Możesz uzyskać dostęp i zarządzania danymi na koncie magazynu przy użyciu interfejsu wiersza polecenia, tak samo, jak za pomocą [pliku System (HDFS, Hadoop Distributed)](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html). Ten artykuł zawiera kilka przykładów, które pomogą Ci rozpocząć pracę.

Usługa HDInsight zapewnia dostęp do rozproszonego systemu plików, który jest lokalnie dołączony do węzłów obliczeniowych. Możesz uzyskać dostęp w tym systemie plików, przy użyciu powłoki, który wchodzi w interakcję bezpośrednio z systemu plików HDFS i innych systemów plików które obsługuje platformy Hadoop.

Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia systemu plików HDFS, zobacz [oficjalnej dokumentacji](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html) i [Przewodnik po uprawnieniach systemu plików HDFS](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html)

>[!NOTE]
>Jeśli używasz usługi Azure Databricks zamiast HDInsight, a chcesz interakcję z danymi przy użyciu interfejsu wiersza polecenia, można użyć interfejsu wiersza polecenia Databricks wchodzić w interakcje w systemie plików usługi Databricks. Zobacz [interfejsu wiersza polecenia Databricks](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html).

## <a name="use-the-hdfs-cli-with-an-hdinsight-hadoop-cluster-on-linux"></a>Należy użyć interfejsu wiersza polecenia systemu plików HDFS z klastra usługi HDInsight Hadoop w systemie Linux

Najpierw należy ustanowić [zdalny dostęp do usług](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-information#remote-access-to-services). W przypadku wybrania [SSH](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix) przykładowego kodu programu PowerShell będzie wyglądać w następujący sposób:

```powershell
#Connect to the cluster via SSH.
ssh sshuser@clustername-ssh.azurehdinsight.net
#Execute basic HDFS commands. Display the hierarchy.
hdfs dfs -ls /
#Create a sample directory.
hdfs dfs -mkdir /samplefolder
```
Parametry połączenia znajduje się w temacie "klastra i protokołu SSH logowania" części bloku klastra HDInsight w witrynie Azure portal. Poświadczenia protokołu SSH zostały określone podczas tworzenia klastra.

>[!IMPORTANT]
>Klaster HDInsight rozliczeń rozpoczyna się po utworzeniu klastra i zatrzymuje, gdy klaster jest usuwany. Opłaty są naliczane za minutę, więc jeśli klaster nie jest używany, należy go usunąć. Aby dowiedzieć się, jak usunąć klaster, zobacz nasze [artykuł na temat](../../hdinsight/hdinsight-delete-cluster.md). Jednak dane przechowywane na koncie magazynu w usłudze Data Lake magazynu Gen2 — włączone będzie nadal występował mimo klastra usługi HDInsight zostanie usunięty.

## <a name="create-a-file-system"></a>Tworzenie systemu plików

    hdfs dfs -D "fs.azure.createRemoteFileSystemDuringInitialization=true" -ls abfs://<file-system-name>@<storage-account-name>.dfs.core.windows.net/

* Zamień symbol zastępczy `<file-system-name>` na nazwę, którą chcesz nadać systemowi plików.

* Zastąp symbol zastępczy `<storage-account-name>` nazwą konta magazynu.

## <a name="get-a-list-of-files-or-directories"></a>Pobierz listę plików lub katalogów

    hdfs dfs -ls <path>

Zastąp `<path>` zastępczego identyfikatorów URI systemu plików lub folderu systemu plików.

Na przykład: `hdfs dfs -ls abfs://my-file-system@mystorageaccount.dfs.core.windows.net/my-directory-name`

## <a name="create-a-directory"></a>Tworzenie katalogu

    hdfs dfs -mkdir [-p] <path>

Zastąp `<path>` symbol zastępczy przy użyciu głównej nazwy systemu plików lub folder w systemie plików.

Na przykład: `hdfs dfs -mkdir abfs://my-file-system@mystorageaccount.dfs.core.windows.net/`

## <a name="delete-a-file-or-directory"></a>Usuwanie pliku lub katalogu

    hdfs dfs -rm <path>

Zastąp `<path>` symbolu zastępczego z identyfikatorem URI pliku lub folderu, który chcesz usunąć.

Na przykład: `hdfs dfs -rmdir abfs://my-file-system@mystorageaccount.dfs.core.windows.net/my-directory-name/my-file-name`

## <a name="display-the-access-control-lists-acls-of-files-and-directories"></a>Wyświetlanie list kontroli dostępu (ACL) plików i katalogów

    hdfs dfs -getfacl [-R] <path>

Przykład:

`hdfs dfs -getfacl -R /dir`

Zobacz [getfacl](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#getfacl)

## <a name="set-acls-of-files-and-directories"></a>Ustaw listy ACL plików i katalogów

    hdfs dfs -setfacl [-R] [-b|-k -m|-x <acl_spec> <path>]|[--set <acl_spec> <path>]

Przykład:

`hdfs dfs -setfacl -m user:hadoop:rw- /file`

Zobacz [setfacl](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#setfacl)

## <a name="change-the-owner-of-files"></a>Zmień właściciela pliku

    hdfs dfs -chown [-R] <new_owner>:<users_group> <URI>

Zobacz [zmienić właściciela](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#chown)

## <a name="change-group-association-of-files"></a>Zmień powiązanie grupy plików

    hdfs dfs -chgrp [-R] <group> <URI>

Zobacz [chgrp](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#chgrp)

## <a name="change-the-permissions-of-files"></a>Zmienianie uprawnień pliku

    hdfs dfs -chmod [-R] <mode> <URI>

Zobacz [chmod](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#chmod)

Można wyświetlić pełną listę poleceń na [podręczniku powłoki System plików Apache Hadoop 2.4.1](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html) witryny sieci Web.

## <a name="next-steps"></a>Kolejne kroki

* [Użyj konta usługi Azure Data Lake Storage Gen2 możliwością w usłudze Azure Databricks](./data-lake-storage-quickstart-create-databricks-account.md)

* [Więcej informacji na temat list kontroli dostępu do plików i katalogów](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)
