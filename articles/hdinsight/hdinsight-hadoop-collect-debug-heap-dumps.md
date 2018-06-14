---
title: Debugowania i analizowania usług Hadoop w usłudze zrzuty stosu - Azure | Dokumentacja firmy Microsoft
description: Automatycznie zbieranie zrzutów sterty dla usług Hadoop i umieścić wewnątrz konta magazynu obiektów Blob platformy Azure do debugowania i analizy.
services: hdinsight
documentationcenter: ''
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: e4ec4ebb-fd32-4668-8382-f956581485c4
ms.service: hdinsight
ms.devlang: na
ms.topic: conceptual
ms.date: 05/25/2017
ms.author: jgao
ROBOTS: NOINDEX
ms.openlocfilehash: 0721d20987008e5cc6370c6e853440ce93edcfa9
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
ms.locfileid: "31398122"
---
# <a name="collect-heap-dumps-in-blob-storage-to-debug-and-analyze-hadoop-services"></a>Sterta zbieranie zrzutów w magazynie obiektów Blob do debugowania i analizowania usługi Hadoop
[!INCLUDE [heapdump-selector](../../includes/hdinsight-selector-heap-dump.md)]

Zrzuty stosu zawierającego migawkę pamięci aplikacji, w tym wartości zmiennych w czasie tworzenia zrzutu. Tak, aby były przydatne do diagnozowania problemów występujących w czasie wykonywania. Zrzuty stosu mogą być automatycznie zbierane na potrzeby usług Hadoop i umieszczony wewnątrz konta magazynu obiektów Blob platformy Azure przez użytkownika w obszarze HDInsightHeapDumps /.

Kolekcja zrzuty stosu dla różnych usług musi być włączony dla usług w poszczególnych klastrach. Wartość domyślna dla tej funkcji jest wyłączone dla klastra. Te zrzuty stosu może być duży, dlatego zaleca się monitorowanie konta magazynu obiektów Blob których one są zapisywane po włączeniu kolekcji.

> [!IMPORTANT]
> Linux jest jedynym systemem operacyjnym używanym w połączeniu z usługą HDInsight w wersji 3.4 lub nowszą. Aby uzyskać więcej informacji, zobacz sekcję [HDInsight retirement on Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement) (Wycofanie usługi HDInsight w systemie Windows). Informacje przedstawione w tym artykule dotyczą tylko HDInsight opartych na systemie Windows. Aby informacji na temat usługi HDInsight opartej na systemie Linux, zobacz [zrzuty stosu Włącz usługi Hadoop w HDInsight opartych na systemie Linux](hdinsight-hadoop-collect-debug-heap-dump-linux.md)


## <a name="eligible-services-for-heap-dumps"></a>Kwalifikujących się usług dla zrzuty stosu
Można włączyć zrzuty stosu dla następujących usług:

* **hcatalog** -tempelton
* **gałąź** -serwera hiveserver2, potrzeby magazynu metadanych, derbyserver
* **mapreduce** -jobhistoryserver
* **yarn** -resourcemanager nodemanager, timelineserver
* **System plików hdfs** -datanode secondarynamenode, namenode

## <a name="configuration-elements-that-enable-heap-dumps"></a>Elementy konfiguracji, które umożliwiają zrzuty stosu
Aby włączyć zrzuty stosu dla usługi, należy ustawić w sekcji dla tej usługi, które jest określone przez elementy odpowiednia konfiguracja **service_name**.

    "javaargs.<service_name>.XX:+HeapDumpOnOutOfMemoryError" = "-XX:+HeapDumpOnOutOfMemoryError",
    "javaargs.<service_name>.XX:HeapDumpPath" = "-XX:HeapDumpPath=c:\Dumps\<service_name>_%date:~4,2%%date:~7,2%%date:~10,2%%time:~0,2%%time:~3,2%%time:~6,2%.hprof"

Wartość **service_name** może być usług wymieniona w tym miejscu: tempelton, serwera hiveserver2, potrzeby magazynu metadanych, derbyserver, jobhistoryserver, resourcemanager, nodemanager, timelineserver, datanode, secondarynamenode, lub namenode.

## <a name="enable-using-azure-powershell"></a>Włącz przy użyciu programu Azure PowerShell
Na przykład aby włączyć zrzuty stosu przy użyciu programu Azure PowerShell dla jobhistoryserver, można użyć następujący skrypt:

[!INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

    $MapRedConfigValues = new-object 'Microsoft.WindowsAzure.Management.HDInsight.Cmdlet.DataObjects.AzureHDInsightMapReduceConfiguration'

    $MapRedConfigValues.Configuration = @{ "javaargs.jobhistoryserver.XX:+HeapDumpOnOutOfMemoryError"="-XX:+HeapDumpOnOutOfMemoryError" ; "javaargs.jobhistoryserver.XX:HeapDumpPath" = "-XX:HeapDumpPath=c:\\Dumps\\jobhistoryserver_%date:~4,2%_%date:~7,2%_%date:~10,2%_%time:~0,2%_%time:~3,2%_%time:~6,2%.hprof" }

## <a name="enable-using-net-sdk"></a>Włącz przy użyciu zestawu .NET SDK
Na przykład aby włączyć zrzuty stosu przy użyciu zestawu .NET SDK usługi Azure HDInsight dla jobhistoryserver, można użyć poniższego kodu:

    clusterInfo.MapReduceConfiguration.ConfigurationCollection.Add(new KeyValuePair<string, string>("javaargs.jobhistoryserver.XX:+HeapDumpOnOutOfMemoryError", "-XX:+HeapDumpOnOutOfMemoryError"));

    clusterInfo.MapReduceConfiguration.ConfigurationCollection.Add(new KeyValuePair<string, string>("javaargs.jobhistoryserver.XX:HeapDumpPath", "-XX:HeapDumpPath=c:\\Dumps\\jobhistoryserver_%date:~4,2%_%date:~7,2%_%date:~10,2%_%time:~0,2%_%time:~3,2%_%time:~6,2%.hprof"));
