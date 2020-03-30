---
title: Problemy z platformą JDBC/ODBC & Apache Thrift — Azure HDInsight
description: Nie można pobrać dużych zestawów danych przy użyciu platformy oprogramowania JDBC/ODBC i Apache Thrift w usłudze Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/29/2019
ms.openlocfilehash: 23693dcae2f361b88440ec88ca39fd8ed229d85a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75894258"
---
# <a name="unable-to-download-large-data-sets-using-jdbcodbc-and-apache-thrift-software-framework-in-hdinsight"></a>Nie można pobrać dużych zestawów danych przy użyciu platformy oprogramowania JDBC/ODBC i Apache Thrift w hdinsight

W tym artykule opisano kroki rozwiązywania problemów i możliwe rozwiązania problemów podczas korzystania ze składników Platformy Apache Spark w klastrach usługi Azure HDInsight.

## <a name="issue"></a>Problem

Podczas próby pobrania dużych zestawów danych przy użyciu JDBC/ODBC i apache Thrift struktury oprogramowania w usłudze Azure HDInsight, pojawi się komunikat o błędzie podobny w następujący sposób:

```
org.apache.spark.SparkException: Kryo serialization failed:
Buffer overflow. Available: 0, required: 36518. To avoid this, increase spark.kryoserializer.buffer.max value.
```

## <a name="cause"></a>Przyczyna

Ten wyjątek jest spowodowany przez proces serializacji próbuje użyć więcej miejsca w buforze niż jest to dozwolone. W programie Spark 2.0.0 klasa `org.apache.spark.serializer.KryoSerializer` jest używana do serializacji obiektów, gdy dane są dostępne za pośrednictwem platformy oprogramowania Apache Thrift. Inna klasa jest używana dla danych, które będą wysyłane za pośrednictwem sieci lub buforowane w formie serializowanej.

## <a name="resolution"></a>Rozwiązanie

Zwiększ `Kryoserializer` wartość buforu. Dodaj klucz `spark.kryoserializer.buffer.max` o nazwie `2048` i ustaw go `Custom spark2-thrift-sparkconf`na in spark2 config pod .

## <a name="next-steps"></a>Następne kroki

Jeśli nie widzisz problemu lub nie możesz rozwiązać problemu, odwiedź jeden z następujących kanałów, aby uzyskać więcej pomocy technicznej:

* Uzyskaj odpowiedzi od ekspertów platformy Azure za pośrednictwem [pomocy technicznej platformy Azure Community.](https://azure.microsoft.com/support/community/)

* Połącz [@AzureSupport](https://twitter.com/azuresupport) się z — oficjalnym kontem platformy Microsoft Azure w celu poprawy jakości obsługi klienta, łącząc społeczność platformy Azure z odpowiednimi zasobami: odpowiedziami, pomocą techniczną i ekspertami.

* Jeśli potrzebujesz więcej pomocy, możesz przesłać żądanie pomocy z [witryny Azure portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Wybierz **pozycję Obsługa z** paska menu lub otwórz centrum pomocy + pomocy **technicznej.** Aby uzyskać bardziej szczegółowe informacje, zapoznaj się z [instrukcjami tworzenia żądania pomocy technicznej platformy Azure.](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) Dostęp do obsługi zarządzania subskrypcjami i rozliczeń jest dołączony do subskrypcji platformy Microsoft Azure, a pomoc techniczna jest świadczona za pośrednictwem jednego z [planów pomocy technicznej platformy Azure.](https://azure.microsoft.com/support/plans/)
