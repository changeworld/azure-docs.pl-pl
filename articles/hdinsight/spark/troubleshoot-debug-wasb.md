---
title: Debugowanie operacji plików WASB w usłudze Azure HDInsight
description: W tym artykule opisano kroki rozwiązywania problemów i możliwe rozwiązania problemów podczas interakcji z klastrami usługi Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 02/18/2020
ms.openlocfilehash: f1707c7f8d6324678c8bf5a470bbded1e58c719e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77470721"
---
# <a name="debug-wasb-file-operations-in-azure-hdinsight"></a>Debugowanie operacji plików WASB w usłudze Azure HDInsight

Istnieją chwile, kiedy można zrozumieć, jakie operacje sterownik WASB rozpoczął z usługą Azure Storage. Po stronie klienta sterownik WASB tworzy dzienniki dla każdej operacji systemu plików na poziomie **DEBUG.** Sterownik WASB używa log4j do kontrolowania poziomu rejestrowania, a domyślnym poziomem info jest poziom **INFO.** W przypadku dzienników analizy po stronie serwera usługi Azure Storage zobacz [Rejestrowanie analizy usługi Azure Storage](../../storage/common/storage-analytics-logging.md).

Wyprodukowany dziennik będzie wyglądał podobnie do:

```log
18/05/13 04:15:55 DEBUG NativeAzureFileSystem: Moving wasb://xxx@yyy.blob.core.windows.net/user/livy/ulysses.txt/_temporary/0/_temporary/attempt_20180513041552_0000_m_000000_0/part-00000 to wasb://xxx@yyy.blob.core.windows.net/user/livy/ulysses.txt/part-00000
```

## <a name="turn-on-wasb-debug-log-for-file-operations"></a>Włączanie dziennika debugowania WASB dla operacji na plikach

1. W przeglądarce internetowej `https://CLUSTERNAME.azurehdinsight.net/#/main/services/SPARK2/configs`przejdź `CLUSTERNAME` do miejsca , gdzie jest nazwa klastra Platformy Spark.

1. Przejdź do **zaawansowanych właściwości spark2-log4j.**

    1. Zmodyfikuj `log4j.appender.console.Threshold=INFO` do `log4j.appender.console.Threshold=DEBUG`.

    1. Dodaj `log4j.logger.org.apache.hadoop.fs.azure.NativeAzureFileSystem=DEBUG`.

1. Przejdź do **zaawansowanego livy2-log4j-properties**.

    Dodaj `log4j.logger.org.apache.hadoop.fs.azure.NativeAzureFileSystem=DEBUG`.

1. Zapisz zmiany.

## <a name="additional-logging"></a>Dodatkowe rejestrowanie

Powyższe dzienniki powinny zapewniać wysoki poziom zrozumienia operacji systemu plików. Jeśli powyższe dzienniki nadal nie dostarczają przydatnych informacji lub jeśli chcesz `fs.azure.storage.client.logging=true` zbadać `core-site`wywołania interfejsu api magazynu obiektów blob, dodaj do pliku . To ustawienie włączy dzienniki java sdk dla sterownika magazynu wasb i wydrukuje każde wywołanie serwera magazynu obiektów blob. Usuń ustawienie po przeprowadzeniu badań, ponieważ może to szybko zapełnić dysk i spowolnić proces.

Jeśli zaplecze jest oparte na usłudze Azure Data Lake, użyj następującego ustawienia log4j dla składnika(na przykład spark/tez/hdfs):

```
log4j.logger.com.microsoft.azure.datalake.store=ALL,adlsFile
log4j.additivity.com.microsoft.azure.datalake.store=true
log4j.appender.adlsFile=org.apache.log4j.FileAppender
log4j.appender.adlsFile.File=/var/log/adl/adl.log
log4j.appender.adlsFile.layout=org.apache.log4j.PatternLayout
log4j.appender.adlsFile.layout.ConversionPattern=%p\t%d{ISO8601}\t%r\t%c\t[%t]\t%m%n
```

Poszukaj dzienników. `/var/log/adl/adl.log`

## <a name="next-steps"></a>Następne kroki

Jeśli nie widzisz problemu lub nie możesz rozwiązać problemu, odwiedź jeden z następujących kanałów, aby uzyskać więcej pomocy technicznej:

* Uzyskaj odpowiedzi od ekspertów platformy Azure za pośrednictwem [pomocy technicznej platformy Azure Community.](https://azure.microsoft.com/support/community/)

* Połącz [@AzureSupport](https://twitter.com/azuresupport) się z — oficjalnym kontem platformy Microsoft Azure w celu poprawy jakości obsługi klienta. Łączenie społeczności platformy Azure z odpowiednimi zasobami: odpowiedziami, pomocą techniczną i ekspertami.

* Jeśli potrzebujesz więcej pomocy, możesz przesłać żądanie pomocy z [witryny Azure portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Wybierz **pozycję Obsługa z** paska menu lub otwórz centrum pomocy + pomocy **technicznej.** Aby uzyskać bardziej szczegółowe informacje, zapoznaj [się z instrukcjami tworzenia żądania pomocy technicznej platformy Azure.](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request) Dostęp do obsługi zarządzania subskrypcjami i rozliczeń jest dołączony do subskrypcji platformy Microsoft Azure, a pomoc techniczna jest świadczona za pośrednictwem jednego z [planów pomocy technicznej platformy Azure.](https://azure.microsoft.com/support/plans/)
