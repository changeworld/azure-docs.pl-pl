---
title: Rozwiązywanie problemów z systemem plików HDFS w usłudze Azure HDInsight
description: Uzyskaj odpowiedzi na często zadawane pytania dotyczące pracy z systemem plików HDFS i usługą Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 09/30/2019
ms.custom: seodec18
ms.openlocfilehash: 1c5d9f665c9b3e7a439a09f4259f304f8f8b1a0a
ms.sourcegitcommit: a19f4b35a0123256e76f2789cd5083921ac73daf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/02/2019
ms.locfileid: "71718328"
---
# <a name="troubleshoot-apache-hadoop-hdfs-by-using-azure-hdinsight"></a>Rozwiązywanie problemów z Apache Hadoop HDFS przy użyciu usługi Azure HDInsight

Poznaj najważniejsze problemy i ich rozwiązania podczas pracy z ładunkiem rozproszony system plików (HDFS) w usłudze Apache Ambari. Aby zapoznać się z pełną listą poleceń, zobacz [Przewodnik po poleceniach](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HDFSCommands.html) [systemu plików HDFS i podręczniku](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/FileSystemShell.html)dotyczącego powłoki.

## <a name="how-do-i-access-local-hdfs-from-inside-a-cluster"></a>Jak mogę uzyskać dostęp do lokalnego systemu plików HDFS z klastra?

### <a name="issue"></a>Problem

Dostęp do lokalnego systemu plików HDFS z poziomu wiersza polecenia i kodu aplikacji zamiast programu przy użyciu magazynu obiektów blob platformy Azure lub Azure Data Lake Storage z klastra usługi HDInsight.

### <a name="resolution-steps"></a>Kroki rozwiązywania problemów

1. W wierszu polecenia Użyj `hdfs dfs -D "fs.default.name=hdfs://mycluster/" ...` dosłownie, jak w poniższym poleceniu:

    ```output
    hdfs dfs -D "fs.default.name=hdfs://mycluster/" -ls /
    Found 3 items
    drwxr-xr-x   - hdiuser hdfs          0 2017-03-24 14:12 /EventCheckpoint-30-8-24-11102016-01
    drwx-wx-wx   - hive    hdfs          0 2016-11-10 18:42 /tmp
    drwx------   - hdiuser hdfs          0 2016-11-10 22:22 /user
    ```

2. W kodzie źródłowym Użyj identyfikatora URI `hdfs://mycluster/` dosłownie, jak w przypadku następującej przykładowej aplikacji:

    ```Java
    import java.io.IOException;
    import java.net.URI;
    import org.apache.commons.io.IOUtils;
    import org.apache.hadoop.conf.Configuration;
    import org.apache.hadoop.fs.*;

    public class JavaUnitTests {

        public static void main(String[] args) throws Exception {

            Configuration conf = new Configuration();
            String hdfsUri = "hdfs://mycluster/";
            conf.set("fs.defaultFS", hdfsUri);
            FileSystem fileSystem = FileSystem.get(URI.create(hdfsUri), conf);
            RemoteIterator<LocatedFileStatus> fileStatusIterator = fileSystem.listFiles(new Path("/tmp"), true);
            while(fileStatusIterator.hasNext()) {
                System.out.println(fileStatusIterator.next().getPath().toString());
            }
        }
    }
    ```

3. Uruchom skompilowany plik JAR (na przykład plik o nazwie `java-unit-tests-1.0.jar`) w klastrze usługi HDInsight przy użyciu następującego polecenia:

    ```apache
    hadoop jar java-unit-tests-1.0.jar JavaUnitTests
    hdfs://mycluster/tmp/hive/hive/5d9cf301-2503-48c7-9963-923fb5ef79a7/inuse.info
    hdfs://mycluster/tmp/hive/hive/5d9cf301-2503-48c7-9963-923fb5ef79a7/inuse.lck
    hdfs://mycluster/tmp/hive/hive/a0be04ea-ae01-4cc4-b56d-f263baf2e314/inuse.info
    hdfs://mycluster/tmp/hive/hive/a0be04ea-ae01-4cc4-b56d-f263baf2e314/inuse.lck
    ```

## <a name="du"></a>du

[-Du](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/FileSystemShell.html#du) polecenie wyświetla rozmiary plików i katalogów znajdujących się w danym katalogu lub długość pliku w przypadku, gdy jest tylko plikiem.

Opcja `-s` generuje zagregowane podsumowanie długości plików, które są wyświetlane.  
Opcja `-h` formatuje rozmiary plików.

Przykład:

```bash
hdfs dfs -du -s -h hdfs://mycluster/
hdfs dfs -du -s -h hdfs://mycluster/tmp
```

## <a name="rm"></a>RM

Polecenie [-RM](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/FileSystemShell.html#rm) usuwa pliki określone jako argumenty.

Przykład:

```bash
hdfs dfs -rm hdfs://mycluster/tmp/testfile
```

## <a name="next-steps"></a>Następne kroki

Jeśli problem nie został wyświetlony lub nie można rozwiązać problemu, odwiedź jeden z następujących kanałów, aby uzyskać więcej pomocy:

* Uzyskaj odpowiedzi od ekspertów platformy Azure za pośrednictwem [pomocy technicznej dla społeczności platformy Azure](https://azure.microsoft.com/support/community/).

* Połącz się z [@AzureSupport](https://twitter.com/azuresupport) — oficjalnego konta Microsoft Azure w celu poprawy obsługi klienta. Połączenie społeczności platformy Azure z właściwymi zasobami: odpowiedziami, wsparciem i ekspertami.

* Jeśli potrzebujesz więcej pomocy, możesz przesłać żądanie pomocy technicznej z [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Na pasku menu wybierz pozycję **Obsługa** , a następnie otwórz Centrum **pomocy i obsługi technicznej** . Aby uzyskać szczegółowe informacje, zapoznaj [się z tematem jak utworzyć żądanie pomocy technicznej platformy Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Dostęp do pomocy w zakresie zarządzania subskrypcjami i rozliczeń jest dostępny w ramach subskrypcji Microsoft Azure, a pomoc techniczna jest świadczona za pomocą jednego z [planów pomocy technicznej systemu Azure](https://azure.microsoft.com/support/plans/).
