---
title: Dostęp do konta nie obsługuje błędu http w usłudze Azure HDInsight
description: W tym artykule opisano kroki rozwiązywania problemów i możliwe rozwiązania problemów podczas interakcji z klastrami usługi Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 02/06/2020
ms.openlocfilehash: 14c43e4557275d6a425127acfee7495f68d1d354
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77165555"
---
# <a name="the-account-being-accessed-does-not-support-http-error-in-azure-hdinsight"></a>Dostęp do konta nie obsługuje błędu http w usłudze Azure HDInsight

W tym artykule opisano kroki rozwiązywania problemów i możliwe rozwiązania problemów podczas interakcji z klastrami usługi Azure HDInsight.

## <a name="issue"></a>Problem

Zostanie wyświetlony następujący komunikat o błędzie:

```
com.microsoft.azure.storage.StorageException: The account being accessed does not support http.
```

## <a name="cause"></a>Przyczyna

Istnieje wiele powodów, dla których komunikat o błędzie jest odbierany:

* Konto magazynu ma [włączony bezpieczny transfer](../../storage/common/storage-require-secure-transfer.md) i używany jest niepoprawny schemat [identyfikatorów URI.](../hdinsight-hadoop-linux-information.md#URI-and-scheme)

* Klaster został utworzony przy wyłączonym koncie magazynu, na które *wyłączono*bezpieczny transfer . Następnie na koncie magazynu włączono bezpieczny transfer.

## <a name="resolution"></a>Rozwiązanie

Jeśli bezpieczny transfer jest włączony dla usługi Azure Storage lub Data `wasbs://` `abfss://`Lake Storage Gen2, identyfikator URI będzie lub , odpowiednio.  Zobacz też [bezpieczny transfer](../../storage/common/storage-require-secure-transfer.md).

W przypadku nowych klastrów należy użyć konta magazynu, które ma już żądane ustawienie bezpiecznego transferu. Nie należy zmieniać ustawienia bezpiecznego transferu dla konta magazynu, które jest używane przez istniejący klaster.

## <a name="next-steps"></a>Następne kroki

Jeśli nie widzisz problemu lub nie możesz rozwiązać problemu, odwiedź jeden z następujących kanałów, aby uzyskać więcej pomocy technicznej:

* Uzyskaj odpowiedzi od ekspertów platformy Azure za pośrednictwem [pomocy technicznej platformy Azure Community.](https://azure.microsoft.com/support/community/)

* Połącz [@AzureSupport](https://twitter.com/azuresupport) się z — oficjalnym kontem platformy Microsoft Azure w celu poprawy jakości obsługi klienta. Łączenie społeczności platformy Azure z odpowiednimi zasobami: odpowiedziami, pomocą techniczną i ekspertami.

* Jeśli potrzebujesz więcej pomocy, możesz przesłać żądanie pomocy z [witryny Azure portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Wybierz **pozycję Obsługa z** paska menu lub otwórz centrum pomocy + pomocy **technicznej.** Aby uzyskać bardziej szczegółowe informacje, zapoznaj [się z instrukcjami tworzenia żądania pomocy technicznej platformy Azure.](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request) Dostęp do obsługi zarządzania subskrypcjami i rozliczeń jest dołączony do subskrypcji platformy Microsoft Azure, a pomoc techniczna jest świadczona za pośrednictwem jednego z [planów pomocy technicznej platformy Azure.](https://azure.microsoft.com/support/plans/)
