---
title: Nie można zalogować się do usługi Zeppelin w usłudze Azure HDInsight
description: Nie można zalogować się do usługi Zeppelin w usłudze Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 08/12/2019
ms.openlocfilehash: 14f4cd7588405dbf351124b1e469fd96f9b38274
ms.sourcegitcommit: 62bd5acd62418518d5991b73a16dca61d7430634
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/13/2019
ms.locfileid: "68977406"
---
# <a name="scenario-unable-to-sign-in-to-apache-zeppelin-in-azure-hdinsight"></a>Scenariusz: Nie można zalogować się do platformy Apache Zeppelin w usłudze Azure HDInsight

W tym artykule opisano kroki rozwiązywania problemów oraz możliwe rozwiązania problemów występujących w przypadku współpracy z klastrami usługi Azure HDInsight.

## <a name="issue"></a>Problem

Nie można zalogować się do usługi Apache Zeppelin po zmianie hasła w usłudze Active Directory.

## <a name="cause"></a>Przyczyna

Użytkownik wymieniony w `activeDirectoryRealm.systemUsername` `shiro_ini` pliku zmienił hasło usługi Active Directory.

## <a name="resolution"></a>Rozwiązanie

1. Sprawdź, czy zmienione hasło jest główną przyczyną przez dołączenie `activeDirectoryRealm.systemPassword = <new password>` go do konfiguracji Zeppelin `shiro_ini` w Ambari. `activeDirectoryRealm.hadoopSecurityCredentialPath` Usuń ustawienie. Poniżej znajduje się lokalizacja `shiro ini`.

    ![Shiro](./media/domain-joined-zeppelin-signin/shiro.png)

1. Jeśli użytkownicy mogą teraz logować się do Zeppelin po kroku 1, Utwórz nowy `jceks` plik z nowym hasłem i `activeDirectoryRealm.hadoopSecurityCredentialPath` zastąp go nowym plikiem.

## <a name="next-steps"></a>Następne kroki

Jeśli problem nie został wyświetlony lub nie można rozwiązać problemu, odwiedź jeden z następujących kanałów, aby uzyskać więcej pomocy:

* Uzyskaj odpowiedzi od ekspertów platformy Azure za pośrednictwem [pomocy technicznej dla społeczności platformy Azure](https://azure.microsoft.com/support/community/).

* Połącz się [@AzureSupport](https://twitter.com/azuresupport) z programem — oficjalnego konta Microsoft Azure, aby zwiększyć komfort obsługi klienta. Połączenie społeczności platformy Azure z właściwymi zasobami: odpowiedziami, wsparciem i ekspertami.

* Jeśli potrzebujesz więcej pomocy, możesz przesłać żądanie pomocy technicznej z [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Na pasku menu wybierz pozycję **Obsługa** , a następnie otwórz Centrum **pomocy i obsługi technicznej** . Aby uzyskać szczegółowe informacje, zapoznaj [się z tematem jak utworzyć żądanie pomocy technicznej platformy Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Dostęp do pomocy w zakresie zarządzania subskrypcjami i rozliczeń jest dostępny w ramach subskrypcji Microsoft Azure, a pomoc techniczna jest świadczona za pomocą jednego z [planów pomocy technicznej systemu Azure](https://azure.microsoft.com/support/plans/).
