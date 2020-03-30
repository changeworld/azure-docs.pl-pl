---
title: Nie można zalogować się do klastra usługi Azure HDInsight
description: Rozwiązywanie problemów, dlaczego nie można zalogować się do klastra Apache Hadoop w usłudze Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/31/2019
ms.openlocfilehash: 2099d1b7583017733498946a5866ab37de43ba9c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75894053"
---
# <a name="scenario-unable-to-log-into-azure-hdinsight-cluster"></a>Scenariusz: nie można zalogować się do klastra usługi Azure HDInsight

W tym artykule opisano kroki rozwiązywania problemów i możliwe rozwiązania problemów podczas interakcji z klastrami usługi Azure HDInsight.

## <a name="issue"></a>Problem

Nie można zalogować się do klastra usługi Azure HDInsight.

## <a name="cause"></a>Przyczyna

Przyczyny mogą się różnić. Należy pamiętać, że podczas logowania do pulpitów nawigacyjnych klastra lub aplikacji użyj "logowania do klastra" lub poświadczeń HTTP. Podczas zdalnego nawiązywania połączenia użyj poświadczeń Secure Shell (SSH) lub poświadczeń pulpitu zdalnego.

## <a name="resolution"></a>Rozwiązanie

Aby rozwiązać typowe problemy, spróbuj wykonać poniższe czynności.

* Spróbuj otworzyć pulpit nawigacyjny klastra na nowej karcie przeglądarki w trybie prywatności.

* Jeśli nie można odwołać poświadczeń SSH, można [zresetować poświadczenia w interfejsie użytkownika Ambari](../hdinsight-administer-use-portal-linux.md#change-passwords).

## <a name="next-steps"></a>Następne kroki

Jeśli nie widzisz problemu lub nie możesz rozwiązać problemu, odwiedź jeden z następujących kanałów, aby uzyskać więcej pomocy technicznej:

* Uzyskaj odpowiedzi od ekspertów platformy Azure za pośrednictwem [pomocy technicznej platformy Azure Community.](https://azure.microsoft.com/support/community/)

* Połącz [@AzureSupport](https://twitter.com/azuresupport) się z — oficjalnym kontem platformy Microsoft Azure w celu poprawy jakości obsługi klienta, łącząc społeczność platformy Azure z odpowiednimi zasobami: odpowiedziami, pomocą techniczną i ekspertami.

* Jeśli potrzebujesz więcej pomocy, możesz przesłać żądanie pomocy z [witryny Azure portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Wybierz **pozycję Obsługa z** paska menu lub otwórz centrum pomocy + pomocy **technicznej.** Aby uzyskać bardziej szczegółowe informacje, zapoznaj się z [instrukcjami tworzenia żądania pomocy technicznej platformy Azure.](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) Dostęp do obsługi zarządzania subskrypcjami i rozliczeń jest dołączony do subskrypcji platformy Microsoft Azure, a pomoc techniczna jest świadczona za pośrednictwem jednego z [planów pomocy technicznej platformy Azure.](https://azure.microsoft.com/support/plans/)
