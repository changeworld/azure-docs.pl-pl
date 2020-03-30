---
title: Wyjątek magazynu po zresetowaniu połączenia w usłudze Azure HDInsight
description: Wyjątek magazynu po zresetowaniu połączenia w usłudze Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/08/2019
ms.openlocfilehash: a7af6407191577112f936bfb9048985e85c868ea
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75887227"
---
# <a name="scenario-storage-exception-after-connection-reset-in-azure-hdinsight"></a>Scenariusz: wyjątek magazynu po zresetowaniu połączenia w usłudze Azure HDInsight

W tym artykule opisano kroki rozwiązywania problemów i możliwe rozwiązania problemów podczas interakcji z klastrami usługi Azure HDInsight.

## <a name="issue"></a>Problem

Nie można utworzyć nowej tabeli Apache HBase.

## <a name="cause"></a>Przyczyna

Podczas procesu obcinania tabeli wystąpił problem z połączeniem magazynu. Wpis tabeli został usunięty w tabeli metadanych bazy danych HBase. Usunięto tylko jeden plik obiektu blob.

Chociaż nie było folderu `/hbase/data/default/ThatTable` blob o nazwie siedząc w magazynie. Sterownik WASB znaleziono istnienie powyższego pliku obiektu blob i nie pozwoli `/hbase/data/default/ThatTable` na utworzenie dowolnego obiektu blob wywołane, ponieważ zakłada, że foldery nadrzędne istniały, tworząc w ten sposób tabelę zakończy się niepowodzeniem.

## <a name="resolution"></a>Rozwiązanie

1. Z apache Ambari UI, uruchom ponownie aktywny HMaster. Pozwoli to jednemu z dwóch rezerwowych HMaster stać się aktywnym, a nowy aktywny HMaster przeładuje informacje o tabeli metadanych. W ten sposób `already-deleted` nie zobaczysz tabeli w interfejsie HMaster.

1. Plik osieroconego obiektu blob można znaleźć z narzędzi `hdfs dfs -ls /xxxxxx/yyyyy`interfejsu użytkownika, takich jak Cloud Explorer lub uruchomione polecenie, takie jak . Uruchom, `hdfs dfs -rmr /xxxxx/yyyy` aby usunąć ten obiekt blob. Na przykład `hdfs dfs -rmr /hbase/data/default/ThatTable/ThatFile`.

Teraz możesz utworzyć nową tabelę o tej samej nazwie w HBase.

## <a name="next-steps"></a>Następne kroki

Jeśli nie widzisz problemu lub nie możesz rozwiązać problemu, odwiedź jeden z następujących kanałów, aby uzyskać więcej pomocy technicznej:

* Uzyskaj odpowiedzi od ekspertów platformy Azure za pośrednictwem [pomocy technicznej platformy Azure Community.](https://azure.microsoft.com/support/community/)

* Połącz [@AzureSupport](https://twitter.com/azuresupport) się z — oficjalnym kontem platformy Microsoft Azure w celu poprawy jakości obsługi klienta. Łączenie społeczności platformy Azure z odpowiednimi zasobami: odpowiedziami, pomocą techniczną i ekspertami.

* Jeśli potrzebujesz więcej pomocy, możesz przesłać żądanie pomocy z [witryny Azure portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Wybierz **pozycję Obsługa z** paska menu lub otwórz centrum pomocy + pomocy **technicznej.** Aby uzyskać bardziej szczegółowe informacje, zapoznaj [się z instrukcjami tworzenia żądania pomocy technicznej platformy Azure.](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) Dostęp do obsługi zarządzania subskrypcjami i rozliczeń jest dołączony do subskrypcji platformy Microsoft Azure, a pomoc techniczna jest świadczona za pośrednictwem jednego z [planów pomocy technicznej platformy Azure.](https://azure.microsoft.com/support/plans/)
