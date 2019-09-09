---
title: Problemy z pobieraniem przy użyciu JDBC/ODBC i Apache Thrift Framework — Azure HDInsight
description: Nie można pobrać dużych zestawów danych przy użyciu funkcji JDBC/ODBC i oprogramowania Apache Thrift w usłudze Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 07/29/2019
ms.openlocfilehash: 97aaec9d42baa3e94be72a748e82345d159f4583
ms.sourcegitcommit: 97605f3e7ff9b6f74e81f327edd19aefe79135d2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/06/2019
ms.locfileid: "70736241"
---
# <a name="scenario-unable-to-download-large-data-sets-using-jdbcodbc-and-apache-thrift-software-framework-in-azure-hdinsight"></a>Scenariusz: Nie można pobrać dużych zestawów danych przy użyciu funkcji JDBC/ODBC i oprogramowania Apache Thrift w usłudze Azure HDInsight

W tym artykule opisano kroki rozwiązywania problemów oraz możliwe rozwiązania problemów występujących w przypadku używania składników Apache Spark w klastrach usługi Azure HDInsight.

## <a name="issue"></a>Problem

Podczas próby pobrania dużych zestawów danych przy użyciu JDBC/ODBC i środowiska oprogramowania Apache Thrift w usłudze Azure HDInsight zostanie wyświetlony komunikat o błędzie podobny do poniższego:

```
org.apache.spark.SparkException: Kryo serialization failed:
Buffer overflow. Available: 0, required: 36518. To avoid this, increase spark.kryoserializer.buffer.max value.
```

## <a name="cause"></a>Przyczyna

Ten wyjątek jest spowodowany przez proces serializacji próbujący użyć większej ilości miejsca w buforze niż dozwolona. W programie Spark 2.0.0 Klasa `org.apache.spark.serializer.KryoSerializer` jest używana do serializacji obiektów, gdy dostęp do danych odbywa się za pomocą platformy Apache Thrift. Inna klasa jest używana w przypadku danych, które będą wysyłane za pośrednictwem sieci lub w pamięci podręcznej w postaci serializowanej.

## <a name="resolution"></a>Rozwiązanie

Zwiększ wartość `Kryoserializer` buforu. Dodaj klucz o nazwie `spark.kryoserializer.buffer.max` i ustaw go na `2048` wartość w spark2 config `Custom spark2-thrift-sparkconf`.

## <a name="next-steps"></a>Następne kroki

Jeśli problem nie został wyświetlony lub nie można rozwiązać problemu, odwiedź jeden z następujących kanałów, aby uzyskać więcej pomocy:

* Uzyskaj odpowiedzi od ekspertów platformy Azure za pośrednictwem [pomocy technicznej dla społeczności platformy Azure](https://azure.microsoft.com/support/community/).

* Połącz się [@AzureSupport](https://twitter.com/azuresupport) za pomocą — oficjalnego konta Microsoft Azure, aby zwiększyć komfort obsługi klienta, łącząc społeczność platformy Azure z właściwymi zasobami: odpowiedziami, pomocą techniczną i ekspertami.

* Jeśli potrzebujesz więcej pomocy, możesz przesłać żądanie pomocy technicznej z [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Na pasku menu wybierz pozycję **Obsługa** , a następnie otwórz Centrum **pomocy i obsługi technicznej** . Aby uzyskać szczegółowe informacje, zobacz [jak utworzyć żądanie pomocy technicznej platformy Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Dostęp do pomocy w zakresie zarządzania subskrypcjami i rozliczeń jest dostępny w ramach subskrypcji Microsoft Azure, a pomoc techniczna jest świadczona za pomocą jednego z [planów pomocy technicznej systemu Azure](https://azure.microsoft.com/support/plans/).
