---
title: Błąd interpretera Zeppelin Apache Hive — usługa Azure HDInsight
description: Interpreter JDBCa Apache Zeppelin Hive wskazuje na nieprawidłowy adres URL w usłudze Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/30/2019
ms.openlocfilehash: dd5a45cb885d800d9922e7c8967f20b63b92be60
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73494171"
---
# <a name="scenario-apache-hive-zeppelin-interpreter-gives-a-zookeeper-error-in-azure-hdinsight"></a>Scenariusz: Apache Hive interpreter Zeppelin daje błąd dozorcy w usłudze Azure HDInsight

W tym artykule opisano kroki rozwiązywania problemów oraz możliwe rozwiązania problemów występujących w przypadku używania interakcyjnych składników zapytań w klastrach usługi Azure HDInsight.

## <a name="issue"></a>Problem

W klastrze programu Apache Hive LLAP interpreter Zeppelin wyświetla następujący komunikat o błędzie podczas próby wykonania zapytania:

```
java.sql.SQLException: org.apache.hive.jdbc.ZooKeeperHiveClientException: Unable to read HiveServer2 configs from ZooKeeper
```

## <a name="cause"></a>Przyczyna

Interpreter JDBCa Zeppelin Hive wskazuje na nieprawidłowy adres URL.

## <a name="resolution"></a>Rozdzielczość

1. Przejdź do podsumowania składnika Hive i skopiuj adres URL "Hive JDBC" do Schowka.

1. Przejdź do `https://clustername.azurehdinsight.net/zeppelin/#/interpreter`

1. Edytuj ustawienia JDBC: zaktualizuj wartość Hive. URL do adresu URL JDBC programu Hive skopiowanego w kroku 1

1. Zapisz, a następnie ponów próbę wykonania zapytania

## <a name="next-steps"></a>Następne kroki

Jeśli problem nie został wyświetlony lub nie można rozwiązać problemu, odwiedź jeden z następujących kanałów, aby uzyskać więcej pomocy:

* Uzyskaj odpowiedzi od ekspertów platformy Azure za pośrednictwem [pomocy technicznej dla społeczności platformy Azure](https://azure.microsoft.com/support/community/).

* Połącz się z [@AzureSupport](https://twitter.com/azuresupport) — oficjalne Microsoft Azure konto, aby usprawnić obsługę klienta, łącząc społeczność platformy Azure z właściwymi zasobami: odpowiedziami, pomocą techniczną i ekspertami.

* Jeśli potrzebujesz więcej pomocy, możesz przesłać żądanie pomocy technicznej z [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Na pasku menu wybierz pozycję **Obsługa** , a następnie otwórz Centrum **pomocy i obsługi technicznej** . Aby uzyskać szczegółowe informacje, zobacz [Jak utworzyć żądanie obsługi na platformie Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Dostęp do pomocy technicznej dotyczącej zarządzania subskrypcjami i rozliczeniami jest oferowany w ramach subskrypcji platformy Microsoft Azure, a pomoc techniczna jest świadczona w ramach jednego z [planów pomocy technicznej platformy Azure](https://azure.microsoft.com/support/plans/).
