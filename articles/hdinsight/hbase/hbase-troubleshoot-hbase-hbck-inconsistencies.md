---
title: hbase hbck zwraca niespójności w usłudze Azure HDInsight
description: hbase hbck zwraca niespójności w usłudze Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/08/2019
ms.openlocfilehash: fa02ac0dfe229f3e82d1c1c62d83ca06a81efca6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75887329"
---
# <a name="scenario-hbase-hbck-command-returns-inconsistencies-in-azure-hdinsight"></a>Scenariusz: `hbase hbck` polecenie zwraca niespójności w usłudze Azure HDInsight

W tym artykule opisano kroki rozwiązywania problemów i możliwe rozwiązania problemów podczas interakcji z klastrami usługi Azure HDInsight.

## <a name="issue-region-is-not-in-hbasemeta"></a>Problem: Region nie jest w`hbase:meta`

Region xxx w systemie plików HDFS, ale nie jest wymieniony ani `hbase:meta` wdrożony na żadnym serwerze regionu.

### <a name="cause"></a>Przyczyna

Różni się.

### <a name="resolution"></a>Rozwiązanie

1. Napraw meta tabelę, uruchamiając:

    ```
    hbase hbck -ignorePreCheckPermission –fixMeta
    ```

1. Przypisz regiony do regionservers, uruchamiając:

    ```
    hbase hbck -ignorePreCheckPermission –fixAssignment
    ```
---

## <a name="issue-region-is-offline"></a>Problem: Region jest w trybie offline

Region xxx nie został wdrożony na żadnym serwerze regionu. Oznacza to, że `hbase:meta`region jest w , ale w trybie offline.

### <a name="cause"></a>Przyczyna

Różni się.

### <a name="resolution"></a>Rozwiązanie

Przewiń regiony w tryb online, uruchamiając:

```
hbase hbck -ignorePreCheckPermission –fixAssignment
```

---

## <a name="issue-regions-have-the-same-startend-keys"></a>Problem: regiony mają te same klucze początkowe/końcowe

### <a name="cause"></a>Przyczyna

Różni się.

### <a name="resolution"></a>Rozwiązanie

Ręczne scalanie tych nakładających się regionów. Przejdź do sekcji tabeli interfejsu użytkownika sieci Web HBase HMaster, wybierz łącze do tabeli, w którym występuje problem. Zostanie wyświetlony klucz początkowy/końcowy każdego regionu należącego do tej tabeli. Następnie scal te nakładające się regiony. W powłoce `merge_region 'xxxxxxxx','yyyyyyy', true`HBase wykonaj . Przykład:

```
RegionA, startkey:001, endkey:010,

RegionB, startkey:001, endkey:080,

RegionC, startkey:010, endkey:080.
```

W tym scenariuszu należy scalić RegionA i RegionC i uzyskać RegionD z tego samego zakresu klucza jako RegionB, a następnie scalać RegionB i RegionD. xxxxxxx i yyyyyy to ciąg hash na końcu każdej nazwy regionu. Należy zachować ostrożność w tym miejscu, aby nie łączyć dwa nieciągłe regiony. Po każdym scaleniu, takich jak scalanie A i C, HBase rozpocznie zagęszczanie na RegionD. Poczekaj na zakończenie zagęszczania przed wykonaniem kolejnej korespondencji seryjnej z RegionD. Stan zagęszczania można znaleźć na tej stronie serwera regionu w interfejsie HBase HMaster.

---

## <a name="issue-cant-load-regioninfo"></a>Problem: nie można załadować`.regioninfo`

Nie można `.regioninfo` załadować `/hbase/data/default/tablex/regiony`dla regionu .

### <a name="cause"></a>Przyczyna

Jest to najprawdopodobniej spowodowane częściowym usunięciem regionu po awarii regionu lub ponownym uruchomieniu maszyny Wirtualnej. Obecnie usługa Azure Storage jest płaskim systemem plików obiektów blob, a niektóre operacje na plikach nie są niepodzielne.

### <a name="resolution"></a>Rozwiązanie

Ręcznie oczyść te pozostałe pliki i foldery:

1. Wykonaj, `hdfs dfs -ls /hbase/data/default/tablex/regiony` aby sprawdzić, jakie foldery /pliki są nadal pod nim.

1. Wykonywanie `hdfs dfs -rmr /hbase/data/default/tablex/regiony/filez` w celu usunięcia wszystkich plików/folderów podrzędnych

1. Wykonaj, `hdfs dfs -rmr /hbase/data/default/tablex/regiony` aby usunąć folder regionu.

---

## <a name="next-steps"></a>Następne kroki

Jeśli nie widzisz problemu lub nie możesz rozwiązać problemu, odwiedź jeden z następujących kanałów, aby uzyskać więcej pomocy technicznej:

* Uzyskaj odpowiedzi od ekspertów platformy Azure za pośrednictwem [pomocy technicznej platformy Azure Community.](https://azure.microsoft.com/support/community/)

* Połącz [@AzureSupport](https://twitter.com/azuresupport) się z — oficjalnym kontem platformy Microsoft Azure w celu poprawy jakości obsługi klienta. Łączenie społeczności platformy Azure z odpowiednimi zasobami: odpowiedziami, pomocą techniczną i ekspertami.

* Jeśli potrzebujesz więcej pomocy, możesz przesłać żądanie pomocy z [witryny Azure portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Wybierz **pozycję Obsługa z** paska menu lub otwórz centrum pomocy + pomocy **technicznej.** Aby uzyskać bardziej szczegółowe informacje, zapoznaj [się z instrukcjami tworzenia żądania pomocy technicznej platformy Azure.](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) Dostęp do obsługi zarządzania subskrypcjami i rozliczeń jest dołączony do subskrypcji platformy Microsoft Azure, a pomoc techniczna jest świadczona za pośrednictwem jednego z [planów pomocy technicznej platformy Azure.](https://azure.microsoft.com/support/plans/)
