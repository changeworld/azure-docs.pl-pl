---
title: Za pomocą usługi Azure Data Lake Storage Gen2 przy użyciu interfejsu wiersza polecenia systemu plików HDFS
description: Wprowadzenie do systemu plików HDFS interfejs wiersza polecenia dla Gen2 Lake magazynu danych
services: storage
author: artemuwka
ms.service: storage
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: artek
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: 051681150501f7c5737f335f8eb48144b08bb990
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2019
ms.locfileid: "58482670"
---
# <a name="using-the-hdfs-cli-with-data-lake-storage-gen2"></a>Przy użyciu interfejsu wiersza polecenia systemu plików HDFS z Gen2 Lake magazynu danych

Azure Data Lake magazynu Gen2 pozwala zarządzać i uzyskiwać dostęp do danych, tak samo, jak za pomocą [pliku System (HDFS, Hadoop Distributed)](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html). Czy istnieje klastra usługi HDInsight, dołączony, lub uruchomić zadanie platformy Apache Spark przy użyciu usługi Azure Databricks w celu przeprowadzenia analizy danych przechowywanych na koncie usługi Azure Storage, można użyć interfejsu wiersza polecenia (CLI) do pobrania i manipulowania nimi załadowanych danych.

## <a name="hdfs-cli-with-hdinsight"></a>System plików HDFS interfejsu wiersza polecenia przy użyciu HDInsight

Usługa HDInsight zapewnia dostęp do rozproszonego systemu plików, który jest lokalnie dołączony do węzłów obliczeniowych. W tym systemie plików jest możliwy za pomocą powłoki, który wchodzi w interakcję bezpośrednio z systemu plików HDFS i innych systemów plików, które obsługuje platformy Hadoop. Poniżej przedstawiono typowe polecenia i linki do przydatnych zasobów.

>[!IMPORTANT]
>Klaster HDInsight rozliczeń rozpoczyna się po utworzeniu klastra i zatrzymuje, gdy klaster jest usuwany. Opłaty są naliczane za minutę, więc jeśli klaster nie jest używany, należy go usunąć. Aby dowiedzieć się, jak usunąć klaster, zobacz nasze [artykuł na temat](../../hdinsight/hdinsight-delete-cluster.md). Jednak dane przechowywane na koncie magazynu w usłudze Data Lake magazynu Gen2 — włączone będzie nadal występował mimo klastra usługi HDInsight zostanie usunięty.

### <a name="create-a-file-system"></a>Tworzenie systemu plików

    hdfs dfs -D "fs.azure.createRemoteFileSystemDuringInitialization=true" -ls abfs://<file-system-name>@<storage-account-name>.dfs.core.windows.net/

* Zamień symbol zastępczy `<file-system-name>` na nazwę, którą chcesz nadać systemowi plików.

* Zastąp symbol zastępczy `<storage-account-name>` nazwą konta magazynu.

### <a name="get-a-list-of-files-or-directories"></a>Pobierz listę plików lub katalogów

    hdfs dfs -ls <path>

Zastąp `<path>` zastępczego identyfikatorów URI systemu plików lub folderu systemu plików.

Na przykład: `hdfs dfs -ls abfs://my-file-system@mystorageaccount.dfs.core.windows.net/my-directory-name`

### <a name="create-a-directory"></a>Tworzenie katalogu

    hdfs dfs -mkdir [-p] <path>

Zastąp `<path>` symbol zastępczy przy użyciu głównej nazwy systemu plików lub folder w systemie plików.

Na przykład: `hdfs dfs -mkdir abfs://my-file-system@mystorageaccount.dfs.core.windows.net/`

### <a name="delete-a-file-or-directory"></a>Usuwanie pliku lub katalogu

    hdfs dfs -rm <path>

Zastąp `<path>` symbolu zastępczego z identyfikatorem URI pliku lub folderu, który chcesz usunąć.

Na przykład: `hdfs dfs -rmdir abfs://my-file-system@mystorageaccount.dfs.core.windows.net/my-directory-name/my-file-name`

### <a name="use-the-hdfs-cli-with-an-hdinsight-hadoop-cluster-on-linux"></a>Należy użyć interfejsu wiersza polecenia systemu plików HDFS z klastra usługi HDInsight Hadoop w systemie Linux

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

Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia systemu plików HDFS, zobacz [oficjalnej dokumentacji](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html) i [Przewodnik po uprawnieniach systemu plików HDFS](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html). Aby dowiedzieć się więcej na temat list ACL w usłudze Databricks, zobacz [wpisów tajnych interfejsu wiersza polecenia](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#secrets-cli).

## <a name="hdfs-cli-with-azure-databricks"></a>System plików HDFS interfejsu wiersza polecenia za pomocą usługi Azure Databricks

Databricks zapewnia łatwy w użyciu interfejsu wiersza polecenia platformy korzystających z interfejsu API REST usługi Databricks. Projekt typu open-source znajduje się na [GitHub](https://github.com/databricks/databricks-cli). Poniżej przedstawiono często używanych poleceń.

### <a name="get-a-list-of-files-or-directories"></a>Pobierz listę plików lub katalogów

    dbfs ls [-l]

### <a name="create-a-directory"></a>Tworzenie katalogu

    dbfs mkdirs

### <a name="delete-a-file"></a>Usuwanie pliku

    dbfs rm [-r]

Inny sposób interakcji z usługi Databricks są notesy. Chociaż notesu ma podstawowy język, określając język % magiczne polecenie języka na początku komórka można łączyć różne języki. W szczególności % sh umożliwia wykonanie kodu powłoki w notesie podobne w przykładzie HDInsight we wcześniejszej części tego artykułu.

### <a name="get-a-list-of-files-or-directories"></a>Pobierz listę plików lub katalogów

    %sh ls <args>

### <a name="create-a-directory"></a>Tworzenie katalogu

    %sh mkdir [-p] <paths>

### <a name="delete-a-file-or-a-directory"></a>Usuwanie pliku lub katalogu

    %sh rm [-skipTrash] URI [URI ...]

Po uruchomieniu klastra Spark w usłudze Azure Databricks, utworzysz nowy notes. Przykładowy skrypt notesu będzie wyglądać w następujący sposób:

    #Execute basic HDFS commands invoking the shell. Display the hierarchy.
    %sh ls /
    #Create a sample directory.
    %sh mkdir /samplefolder
    #Get the ACL of the newly created directory.
    hdfs dfs -getfacl /samplefolder

Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia Databricks, zobacz [oficjalną dokumentacją](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html). Aby uzyskać więcej informacji na temat notesów, zobacz [notesów](https://docs.azuredatabricks.net/user-guide/notebooks/index.html) sekcji dokumentacji.

## <a name="set-file-and-directory-level-permissions"></a>Ustaw uprawnienia na poziomie plików i katalogów

Ustaw i Uzyskaj dostęp do uprawnień na poziomie plików i katalogów. Oto kilka poleceń, aby rozpocząć pracę. 

Aby dowiedzieć się więcej na temat plików i katalogów uprawnień na poziomie do systemu plików usługi Azure Data Lake Gen2, zobacz [kontrola dostępu w usługach Azure Data Lake Storage Gen2](storage-data-lake-storage-access-control.md).

### <a name="display-the-access-control-lists-acls-of-files-and-directories"></a>Wyświetlanie list kontroli dostępu (ACL) plików i katalogów

    hdfs dfs -getfacl [-R] <path>

Przykład:

`hdfs dfs -getfacl -R /dir`

Zobacz [getfacl](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#getfacl)

### <a name="set-acls-of-files-and-directories"></a>Ustaw listy ACL plików i katalogów

    hdfs dfs -setfacl [-R] [-b|-k -m|-x <acl_spec> <path>]|[--set <acl_spec> <path>]

Przykład:

`hdfs dfs -setfacl -m user:hadoop:rw- /file`

Zobacz [setfacl](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#setfacl)

### <a name="change-the-owner-of-files"></a>Zmień właściciela pliku

    hdfs dfs -chown [-R] <new_owner>:<users_group> <URI>

Zobacz [zmienić właściciela](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#chown)

### <a name="change-group-association-of-files"></a>Zmień powiązanie grupy plików

    hdfs dfs -chgrp [-R] <group> <URI>

Zobacz [chgrp](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#chgrp)

### <a name="change-the-permissions-of-files"></a>Zmienianie uprawnień pliku

    hdfs dfs -chmod [-R] <mode> <URI>

Zobacz [chmod](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#chmod)

Można wyświetlić pełną listę poleceń na [podręczniku powłoki System plików Apache Hadoop 2.4.1](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html) witryny sieci Web.

## <a name="next-steps"></a>Kolejne kroki

[Użyj konta usługi Azure Data Lake Storage Gen2 możliwością w usłudze Azure Databricks](./data-lake-storage-quickstart-create-databricks-account.md) 
