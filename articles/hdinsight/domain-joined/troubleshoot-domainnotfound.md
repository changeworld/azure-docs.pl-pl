---
title: Tworzenie klastra kończy się niepowodzeniem z błędem DomainNotFound w usłudze Azure HDInsight
description: Rozwiązywanie kroków i możliwych rozwiązań problemów podczas interakcji z klastrami usługi Azure HDInsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/23/2020
ms.openlocfilehash: adcdafbc07fa0a8cc6970ab227b52aee798b084f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76776237"
---
# <a name="scenario-cluster-creation-fails-with-domainnotfound-error-in-azure-hdinsight"></a>Scenariusz: tworzenie klastra kończy się niepowodzeniem z błędem DomainNotFound w usłudze Azure HDInsight

W tym artykule opisano kroki rozwiązywania problemów i możliwe rozwiązania problemów podczas interakcji z klastrami usługi Azure HDInsight.

## <a name="issue"></a>Problem

Tworzenie klastra HDI Secure (Enterprise `DomainNotFound` Security Package) kończy się niepowodzeniem z komunikatem o błędzie.

## <a name="cause"></a>Przyczyna

Nieprawidłowe ustawienia DNS.

## <a name="resolution"></a>Rozwiązanie

Po wdrożeniu klastrów przyłączonych do domeny interfejs HDI tworzy wewnętrzną nazwę użytkownika i hasło w UAD DS (dla każdego klastra) i łączy wszystkie węzły klastra z tą domeną. Sprzężenie domeny odbywa się za pomocą narzędzi Samba. Upewnij się, że spełnione są następujące wymagania wstępne:

* Nazwa domeny powinna zostać rozwiązana za pośrednictwem systemu DNS.
* Adres IP kontrolerów domeny powinien być ustawiony w ustawieniach DNS dla sieci wirtualnej, w której jest wdrażany klaster.
* Jeśli sieć wirtualna jest równorzędna z siecią wirtualną usługi AAD DS, należy to zrobić ręcznie.
* Jeśli używasz usług przesyłania dalej DNS, nazwa domeny musi zostać poprawnie rozwiązana w sieci wirtualnej.
* Zasady zabezpieczeń (NSG) nie powinny blokować dołączania do domeny.

### <a name="additional-debugging-steps"></a>Dodatkowe kroki debugowania

* Wdrażanie maszyny Wirtualnej systemu Windows w tej samej podsieci, dołączanie do domeny przy użyciu nazwy użytkownika i hasła (można to zrobić za pośrednictwem interfejsu użytkownika panelu sterowania) lub

* Wdrażanie maszyny wirtualnej Ubuntu w tej samej podsieci i przyłączanie do komputera
  * SSH do maszyny
  * sudo su
  * Uruchamianie skryptu przy użyciu nazwy użytkownika i hasła
  * Skrypt będzie pingować, tworzyć wymagane pliki konfiguracyjne, a następnie domeny. Jeśli to się powiedzie, ustawienia DNS są dobre.

## <a name="next-steps"></a>Następne kroki

Jeśli nie widzisz problemu lub nie możesz rozwiązać problemu, odwiedź jeden z następujących kanałów, aby uzyskać więcej pomocy technicznej:

* Uzyskaj odpowiedzi od ekspertów platformy Azure za pośrednictwem [pomocy technicznej platformy Azure Community.](https://azure.microsoft.com/support/community/)

* Połącz [@AzureSupport](https://twitter.com/azuresupport) się z — oficjalnym kontem platformy Microsoft Azure w celu poprawy jakości obsługi klienta. Łączenie społeczności platformy Azure z odpowiednimi zasobami: odpowiedziami, pomocą techniczną i ekspertami.

* Jeśli potrzebujesz więcej pomocy, możesz przesłać żądanie pomocy z [witryny Azure portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Wybierz **pozycję Obsługa z** paska menu lub otwórz centrum pomocy + pomocy **technicznej.** Aby uzyskać bardziej szczegółowe informacje, zapoznaj [się z instrukcjami tworzenia żądania pomocy technicznej platformy Azure.](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request) Dostęp do obsługi zarządzania subskrypcjami i rozliczeń jest dołączony do subskrypcji platformy Microsoft Azure, a pomoc techniczna jest świadczona za pośrednictwem jednego z [planów pomocy technicznej platformy Azure.](https://azure.microsoft.com/support/plans/)
