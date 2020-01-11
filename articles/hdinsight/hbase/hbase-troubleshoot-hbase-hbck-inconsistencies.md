---
title: HBase hbck zwraca niespójności w usłudze Azure HDInsight
description: HBase hbck zwraca niespójności w usłudze Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/08/2019
ms.openlocfilehash: fa02ac0dfe229f3e82d1c1c62d83ca06a81efca6
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/11/2020
ms.locfileid: "75887329"
---
# <a name="scenario-hbase-hbck-command-returns-inconsistencies-in-azure-hdinsight"></a>Scenariusz: polecenie `hbase hbck` zwraca niespójności w usłudze Azure HDInsight

W tym artykule opisano kroki rozwiązywania problemów oraz możliwe rozwiązania problemów występujących w przypadku współpracy z klastrami usługi Azure HDInsight.

## <a name="issue-region-is-not-in-hbasemeta"></a>Problem: region nie znajduje się w `hbase:meta`

Region xxx w systemie plików HDFS, ale nie jest wymieniony w `hbase:meta` ani wdrożony na żadnym z serwerów regionów.

### <a name="cause"></a>Przyczyna

Różni się.

### <a name="resolution"></a>Rozdzielczość

1. Popraw tabelę meta, uruchamiając:

    ```
    hbase hbck -ignorePreCheckPermission –fixMeta
    ```

1. Przypisz regiony do RegionServers, uruchamiając:

    ```
    hbase hbck -ignorePreCheckPermission –fixAssignment
    ```
---

## <a name="issue-region-is-offline"></a>Problem: region jest w trybie offline

Region XXX nie został wdrożony na żadnym RegionServer. Oznacza to, że region jest w `hbase:meta`, ale offline.

### <a name="cause"></a>Przyczyna

Różni się.

### <a name="resolution"></a>Rozdzielczość

Przenieś regiony w tryb online, uruchamiając:

```
hbase hbck -ignorePreCheckPermission –fixAssignment
```

---

## <a name="issue-regions-have-the-same-startend-keys"></a>Problem: regiony mają te same klucze Start/End

### <a name="cause"></a>Przyczyna

Różni się.

### <a name="resolution"></a>Rozdzielczość

Ręcznie scal te nakładające się regiony. Przejdź do sekcji tabela interfejsu użytkownika sieci Web HBase serwera hmaster, wybierz łącze tabela, która zawiera problem. Zostanie wyświetlony klucz Start/klucz końcowy każdego regionu należącego do tej tabeli. Następnie Scal te nakładające się regiony. W HBase Shell, zrób `merge_region 'xxxxxxxx','yyyyyyy', true`. Przykład:

```
RegionA, startkey:001, endkey:010,

RegionB, startkey:001, endkey:080,

RegionC, startkey:010, endkey:080.
```

W tym scenariuszu należy scalić region i RegionC i uzyskać region z tym samym zakresem kluczy co RegionB, a następnie scalić RegionB i region. xxxxxxx i yyyyyy to ciąg skrótu na końcu nazwy każdego regionu. Należy zachować ostrożność, aby nie scalać dwóch nieciągłych regionów. Po każdym scalaniu, takim jak Scale A i C, HBase rozpocznie kompaktowanie w regionie. Poczekaj na zakończenie kompaktowania przed kolejną scaleniem z regionem. Stan kompaktowania można znaleźć na stronie serwer regionu w interfejsie użytkownika HBase serwera hmaster.

---

## <a name="issue-cant-load-regioninfo"></a>Problem: nie można załadować `.regioninfo`

Nie można załadować `.regioninfo` dla `/hbase/data/default/tablex/regiony`regionów.

### <a name="cause"></a>Przyczyna

Najprawdopodobniej jest to spowodowane częściowym usunięciem regionu podczas RegionServer awarii lub ponownego uruchamiania maszyny wirtualnej. Obecnie usługa Azure Storage to prosty system plików obiektów blob, a niektóre operacje na plikach nie są niepodzielne.

### <a name="resolution"></a>Rozdzielczość

Ręcznie Wyczyść te pozostałe pliki i foldery:

1. Wykonaj `hdfs dfs -ls /hbase/data/default/tablex/regiony`, aby sprawdzić, jakie foldery i pliki nadal znajdują się w nim.

1. Wykonaj `hdfs dfs -rmr /hbase/data/default/tablex/regiony/filez`, aby usunąć wszystkie pliki i foldery podrzędne

1. Wykonaj `hdfs dfs -rmr /hbase/data/default/tablex/regiony`, aby usunąć folder region.

---

## <a name="next-steps"></a>Następne kroki

Jeśli problem nie został wyświetlony lub nie można rozwiązać problemu, odwiedź jeden z następujących kanałów, aby uzyskać więcej pomocy:

* Uzyskaj odpowiedzi od ekspertów platformy Azure za pośrednictwem [pomocy technicznej dla społeczności platformy Azure](https://azure.microsoft.com/support/community/).

* Połącz się z [@AzureSupport](https://twitter.com/azuresupport) — oficjalnego Microsoft Azure konta, aby zwiększyć komfort obsługi klienta. Połączenie społeczności platformy Azure z właściwymi zasobami: odpowiedziami, wsparciem i ekspertami.

* Jeśli potrzebujesz więcej pomocy, możesz przesłać żądanie pomocy technicznej z [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Na pasku menu wybierz pozycję **Obsługa** , a następnie otwórz Centrum **pomocy i obsługi technicznej** . Aby uzyskać szczegółowe informacje, zapoznaj [się z tematem jak utworzyć żądanie pomocy technicznej platformy Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Dostęp do pomocy w zakresie zarządzania subskrypcjami i rozliczeń jest dostępny w ramach subskrypcji Microsoft Azure, a pomoc techniczna jest świadczona za pomocą jednego z [planów pomocy technicznej systemu Azure](https://azure.microsoft.com/support/plans/).
