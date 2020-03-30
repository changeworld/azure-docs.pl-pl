---
title: Korzystanie z interfejsu wiersza polecenia HDFS z usługą Azure Data Lake Storage Gen2
description: Wprowadzenie do interfejsu wiersza interfejsu wiersza interfejsu plików HDFS dla pamięci masowej w jeziorze datam Gen2
services: storage
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: normesta
ms.subservice: data-lake-storage-gen2
ms.reviewer: artek
ms.openlocfilehash: 1d5313f3f0fff128dd09f9c9857b7dd9921ea4f8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "69992219"
---
# <a name="using-the-hdfs-cli-with-data-lake-storage-gen2"></a>Korzystanie z interfejsu wiersza interfejsu interfejsu twardego HDFS z pamięcią masową Data Lake Gen2

Dostęp do danych na koncie magazynu i zarządzanie nimi można uzyskać za pomocą interfejsu wiersza polecenia, tak jak w przypadku [systemu plików HDFS (Hadoop Distributed File System).](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html) W tym artykule przedstawiono kilka przykładów, które pomogą Ci rozpocząć pracę.

HdInsight zapewnia dostęp do kontenera rozproszonego, który jest lokalnie dołączony do węzłów obliczeniowych. Dostęp do tego kontenera można uzyskać przy użyciu powłoki, która bezpośrednio współdziała z hdfs i innych systemów plików, które obsługuje Hadoop.

Więcej informacji na temat interfejsu interfejsu wiersza interfejsu twardego można znaleźć w [oficjalnej dokumentacji](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html) i [przewodniku po uprawnieniach hdfs](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html)

>[!NOTE]
>Jeśli używasz usługi Azure Databricks zamiast usługi HDInsight i chcesz wchodzić w interakcje z danymi przy użyciu interfejsu wiersza polecenia, możesz użyć interfejsu wiersza polecenia Databricks do interakcji z systemem plików Databricks. Zobacz [Databricks CLI](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html).

## <a name="use-the-hdfs-cli-with-an-hdinsight-hadoop-cluster-on-linux"></a>Użyj interfejsu wiersza interfejsu twardego HDFS z klastrem HDInsight Hadoop w systemie Linux

Po pierwsze, ustanowienie [zdalnego dostępu do usług](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-information#remote-access-to-services). Jeśli wybierzesz [SSH](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix) przykładowy kod programu PowerShell będzie wyglądać następująco:

```powershell
#Connect to the cluster via SSH.
ssh sshuser@clustername-ssh.azurehdinsight.net
#Execute basic HDFS commands. Display the hierarchy.
hdfs dfs -ls /
#Create a sample directory.
hdfs dfs -mkdir /samplefolder
```
Parametry połączenia można znaleźć w sekcji "SSH + Cluster login" bloku klastra HDInsight w witrynie Azure portal. Poświadczenia SSH zostały określone w momencie tworzenia klastra.

>[!IMPORTANT]
>Rozliczenia klastra HDInsight rozpoczynają się po utworzeniu klastra i zatrzymują się po usunięciu klastra. Opłaty są naliczane za minutę, więc jeśli klaster nie jest używany, należy go usunąć. Aby dowiedzieć się, jak usunąć klaster, zobacz nasz [artykuł na ten temat](../../hdinsight/hdinsight-delete-cluster.md). Jednak dane przechowywane na koncie magazynu z włączoną funkcją Data Lake Storage Gen2 utrzymują się nawet po usunięciu klastra HDInsight.

## <a name="create-a-container"></a>Tworzenie kontenera

    hdfs dfs -D "fs.azure.createRemoteFileSystemDuringInitialization=true" -ls abfs://<container-name>@<storage-account-name>.dfs.core.windows.net/

* Zastąp `<container-name>` symbol zastępczy nazwą, którą chcesz nadać kontenerowi.

* Zastąp symbol zastępczy `<storage-account-name>` nazwą konta magazynu.

## <a name="get-a-list-of-files-or-directories"></a>Wyświetlanie listy plików lub katalogów

    hdfs dfs -ls <path>

Zastąp `<path>` symbol zastępczy identyfikatorem URI folderu kontenera lub kontenera.

Na przykład: `hdfs dfs -ls abfs://my-file-system@mystorageaccount.dfs.core.windows.net/my-directory-name`

## <a name="create-a-directory"></a>Tworzenie katalogu

    hdfs dfs -mkdir [-p] <path>

Zastąp `<path>` symbol zastępczy nazwą kontenera głównego lub folderem w kontenerze.

Na przykład: `hdfs dfs -mkdir abfs://my-file-system@mystorageaccount.dfs.core.windows.net/`

## <a name="delete-a-file-or-directory"></a>Usuwanie pliku lub katalogu

    hdfs dfs -rm <path>

Zastąp `<path>` symbol zastępczy identyfikatorem URI pliku lub folderu, który chcesz usunąć.

Na przykład: `hdfs dfs -rmdir abfs://my-file-system@mystorageaccount.dfs.core.windows.net/my-directory-name/my-file-name`

## <a name="display-the-access-control-lists-acls-of-files-and-directories"></a>Wyświetlanie list kontroli dostępu (ACL) plików i katalogów

    hdfs dfs -getfacl [-R] <path>

Przykład:

`hdfs dfs -getfacl -R /dir`

Zobacz [getfacl](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#getfacl)

## <a name="set-acls-of-files-and-directories"></a>Ustawianie list ACL plików i katalogów

    hdfs dfs -setfacl [-R] [-b|-k -m|-x <acl_spec> <path>]|[--set <acl_spec> <path>]

Przykład:

`hdfs dfs -setfacl -m user:hadoop:rw- /file`

Zobacz [setfacl](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#setfacl)

## <a name="change-the-owner-of-files"></a>Zmienianie właściciela plików

    hdfs dfs -chown [-R] <new_owner>:<users_group> <URI>

Zobacz [chown](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#chown)

## <a name="change-group-association-of-files"></a>Zmienianie skojarzenia plików w grupach

    hdfs dfs -chgrp [-R] <group> <URI>

Zobacz [chgrp](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#chgrp)

## <a name="change-the-permissions-of-files"></a>Zmienianie uprawnień plików

    hdfs dfs -chmod [-R] <mode> <URI>

Zobacz [chmod](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#chmod)

Pełną listę poleceń można wyświetlić na stronie [apache Hadoop 2.4.1 File System Shell Guide.](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html)

## <a name="next-steps"></a>Następne kroki

* [Korzystanie z konta obsługującego usługę Azure Data Lake Storage Gen2 w usłudze Azure Databricks](./data-lake-storage-quickstart-create-databricks-account.md)

* [Dowiedz się więcej o listach kontroli dostępu w plikach i katalogach](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)
