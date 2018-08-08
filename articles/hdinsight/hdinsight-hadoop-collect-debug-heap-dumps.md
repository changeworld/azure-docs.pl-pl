---
title: Debugowanie i analizowanie usługi Hadoop z zrzutów stosu - Azure
description: Zbieranie zrzutów sterty dla usług Hadoop i automatycznie umieścić wewnątrz konta magazynu obiektów Blob platformy Azure do debugowania i analizy.
services: hdinsight
author: jasonwhowell
editor: jasonwhowell
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/25/2017
ms.author: jasonh
ROBOTS: NOINDEX
ms.openlocfilehash: 484cdcb45a835a0a3b76e2c3aeca7500af7c9b7b
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/07/2018
ms.locfileid: "39597008"
---
# <a name="collect-heap-dumps-in-blob-storage-to-debug-and-analyze-hadoop-services"></a>Zbieranie zrzutów stosów w magazynie obiektów Blob, debugowanie i analizowanie usługi Hadoop
[!INCLUDE [heapdump-selector](../../includes/hdinsight-selector-heap-dump.md)]

Zrzuty sterty zawierają migawkę pamięci aplikacji, w tym wartości zmiennych w czasie tworzenia zrzutu. Tak, aby były przydatne do diagnozowania problemów, które występują w czasie wykonywania. Zrzuty sterty może być automatycznie zbierane dla usług Hadoop i umieszczone wewnątrz konta magazynu obiektów Blob platformy Azure przez użytkownika w obszarze HDInsightHeapDumps /.

Zbieranie zrzutów stosów na potrzeby różnych usług musi być włączona dla usług w poszczególnych klastrach. Wartość domyślna dla tej funkcji jest wyłączone dla klastra. Te zrzuty stosu mogą być duże, zaleca się monitorowanie konta usługi Blob storage gdzie one są zapisywane po włączeniu kolekcji.

> [!IMPORTANT]
> Linux jest jedynym systemem operacyjnym używanym w połączeniu z usługą HDInsight w wersji 3.4 lub nowszą. Aby uzyskać więcej informacji, zobacz sekcję [HDInsight retirement on Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement) (Wycofanie usługi HDInsight w systemie Windows). Informacje przedstawione w tym artykule dotyczy tylko HDInsight z systemem Windows. Aby uzyskać informacji na temat HDInsight opartych na systemie Linux, zobacz [zrzutów stosów włączyć na potrzeby usługi Hadoop w HDInsight opartych na systemie Linux](hdinsight-hadoop-collect-debug-heap-dump-linux.md)


## <a name="eligible-services-for-heap-dumps"></a>Kwalifikujących się usług dla zrzutów stosu
Można włączyć zrzutów sterty dla następujących usług:

* **hcatalog** -tempelton
* **gałąź** — serwera hiveserver2, Magazyn metadanych, derbyserver
* **mapreduce** -jobhistoryserver
* **yarn** -resourcemanager nodemanager, timelineserver
* **System plików hdfs** -datanode secondarynamenode, namenode

## <a name="configuration-elements-that-enable-heap-dumps"></a>Elementy konfiguracji, które Włączanie zrzutów stosu
Aby włączyć zrzutów stosów na potrzeby usługi, musisz ustawić elementy prawidłowej konfiguracji w sekcji za daną usługę, która jest określona przez **service_name**.

    "javaargs.<service_name>.XX:+HeapDumpOnOutOfMemoryError" = "-XX:+HeapDumpOnOutOfMemoryError",
    "javaargs.<service_name>.XX:HeapDumpPath" = "-XX:HeapDumpPath=c:\Dumps\<service_name>_%date:~4,2%%date:~7,2%%date:~10,2%%time:~0,2%%time:~3,2%%time:~6,2%.hprof"

Wartość **service_name** może być dowolną z usług wymienione: tempelton, serwera hiveserver2, Magazyn metadanych, derbyserver, jobhistoryserver, resourcemanager, nodemanager, timelineserver, datanode, secondarynamenode, lub namenode.

## <a name="enable-using-azure-powershell"></a>Włącz przy użyciu programu Azure PowerShell
Na przykład aby włączyć zrzutów sterty przy użyciu programu Azure PowerShell dla jobhistoryserver, służy poniższy skrypt:

[!INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

    $MapRedConfigValues = new-object 'Microsoft.WindowsAzure.Management.HDInsight.Cmdlet.DataObjects.AzureHDInsightMapReduceConfiguration'

    $MapRedConfigValues.Configuration = @{ "javaargs.jobhistoryserver.XX:+HeapDumpOnOutOfMemoryError"="-XX:+HeapDumpOnOutOfMemoryError" ; "javaargs.jobhistoryserver.XX:HeapDumpPath" = "-XX:HeapDumpPath=c:\\Dumps\\jobhistoryserver_%date:~4,2%_%date:~7,2%_%date:~10,2%_%time:~0,2%_%time:~3,2%_%time:~6,2%.hprof" }

## <a name="enable-using-net-sdk"></a>Włącz przy użyciu zestawu .NET SDK
Na przykład aby włączyć zrzutów sterty przy użyciu zestawu .NET SDK usługi Azure HDInsight dla jobhistoryserver, służy poniższy kod:

    clusterInfo.MapReduceConfiguration.ConfigurationCollection.Add(new KeyValuePair<string, string>("javaargs.jobhistoryserver.XX:+HeapDumpOnOutOfMemoryError", "-XX:+HeapDumpOnOutOfMemoryError"));

    clusterInfo.MapReduceConfiguration.ConfigurationCollection.Add(new KeyValuePair<string, string>("javaargs.jobhistoryserver.XX:HeapDumpPath", "-XX:HeapDumpPath=c:\\Dumps\\jobhistoryserver_%date:~4,2%_%date:~7,2%_%date:~10,2%_%time:~0,2%_%time:~3,2%_%time:~6,2%.hprof"));
