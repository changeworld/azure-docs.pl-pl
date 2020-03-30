---
title: Apache Spark wolno, gdy magazyn usługi Azure HDInsight ma wiele plików
description: Zadanie Apache Spark działa wolno, gdy kontener magazynu platformy Azure zawiera wiele plików w usłudze Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/21/2019
ms.openlocfilehash: e389c05a6de85287bc86eff510e137f470837e56
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75894326"
---
# <a name="apache-spark-job-run-slowly-when-the-azure-storage-container-contains-many-files-in-azure-hdinsight"></a>Zadania platformy Apache Spark działają wolno, gdy kontener usługi Azure Storage zawiera wiele plików w usłudze HDInsight

W tym artykule opisano kroki rozwiązywania problemów i możliwe rozwiązania problemów podczas korzystania ze składników Platformy Apache Spark w klastrach usługi Azure HDInsight.

## <a name="issue"></a>Problem

Podczas uruchamiania klastra HDInsight zadanie Apache Spark, które zapisuje w kontenerze magazynu platformy Azure staje się powolne, gdy istnieje wiele plików/podfolderów. Na przykład trwa 20 sekund podczas zapisywania do nowego kontenera, ale około 2 minut podczas zapisywania do kontenera, który ma pliki 200k.

## <a name="cause"></a>Przyczyna

Jest to znany problem Spark. Powolność pochodzi z `ListBlob` `GetBlobProperties` i operacji podczas wykonywania zadania Platformy Spark.

Aby śledzić partycje, Spark `FileStatusCache` musi zachować, który zawiera informacje o strukturze katalogów. Za pomocą tej pamięci podręcznej, Spark można przeanalizować ścieżki i być świadomi dostępnych partycji. Zaletą śledzenia partycji jest to, że platforma Spark dotyka tylko niezbędnych plików podczas odczytywania danych. Aby te informacje były aktualne, podczas zapisywania nowych danych spark musi wyświetlić listę wszystkich plików w katalogu i zaktualizować tę pamięć podręczną.

W programie Spark 2.1, podczas gdy nie musimy aktualizować pamięci podręcznej po każdym zapisie, Spark sprawdzi, czy istniejąca kolumna partycji pasuje do kolumny partycji z proponowaną w bieżącym żądaniu zapisu, więc doprowadzi to również do operacji wyświetlania listy na początku każdego zapisu.

W spark 2.2 podczas zapisywania danych w trybie dołączania ten problem z wydajnością należy rozwiązać.

## <a name="resolution"></a>Rozwiązanie

Podczas tworzenia podzielonego na partycje zestawu danych ważne jest użycie schematu partycjonowania, który ograniczy `FileStatusCache`liczbę plików, które spark musi wyświetlić, aby zaktualizować program .

Dla każdej mikropartii Nth, gdzie N % 100 == 0 (100 jest tylko przykładem), przenieś istniejące dane do innego katalogu, który może być załadowany przez Spark.

## <a name="next-steps"></a>Następne kroki

Jeśli nie widzisz problemu lub nie możesz rozwiązać problemu, odwiedź jeden z następujących kanałów, aby uzyskać więcej pomocy technicznej:

* Uzyskaj odpowiedzi od ekspertów platformy Azure za pośrednictwem [pomocy technicznej platformy Azure Community.](https://azure.microsoft.com/support/community/)

* Połącz [@AzureSupport](https://twitter.com/azuresupport) się z — oficjalnym kontem platformy Microsoft Azure w celu poprawy jakości obsługi klienta, łącząc społeczność platformy Azure z odpowiednimi zasobami: odpowiedziami, pomocą techniczną i ekspertami.

* Jeśli potrzebujesz więcej pomocy, możesz przesłać żądanie pomocy z [witryny Azure portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Wybierz **pozycję Obsługa z** paska menu lub otwórz centrum pomocy + pomocy **technicznej.** Aby uzyskać bardziej szczegółowe informacje, zapoznaj się z [instrukcjami tworzenia żądania pomocy technicznej platformy Azure.](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) Dostęp do obsługi zarządzania subskrypcjami i rozliczeń jest dołączony do subskrypcji platformy Microsoft Azure, a pomoc techniczna jest świadczona za pośrednictwem jednego z [planów pomocy technicznej platformy Azure.](https://azure.microsoft.com/support/plans/)
