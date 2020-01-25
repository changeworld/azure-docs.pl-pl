---
title: Nieodświeżone alerty Apache Ambari w usłudze Azure HDInsight
description: Dyskusja i analiza możliwych przyczyn i rozwiązań dotyczących starych alertów Apache Ambari w usłudze HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/22/2020
ms.openlocfilehash: f19d499b5e50fbb5030a0f396296eed46fc6eee3
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/24/2020
ms.locfileid: "76722813"
---
# <a name="scenario-apache-ambari-stale-alerts-in-azure-hdinsight"></a>Scenariusz: usługa Apache Ambari nieodświeżone alerty w usłudze Azure HDInsight

W tym artykule opisano kroki rozwiązywania problemów oraz możliwe rozwiązania problemów występujących w przypadku współpracy z klastrami usługi Azure HDInsight.

## <a name="issue"></a>Problem

W interfejsie użytkownika Apache Ambari może zostać wyświetlony alert podobny do następującego:

![Przykład nieodświeżonego alertu Apache Ambari](./media/apache-ambari-troubleshoot-stale-alerts/ambari-stale-alerts-example.png)

## <a name="cause"></a>Przyczyna

Agenci Ambari stale wykonują kontrole kondycji, aby monitorować kondycję wielu zasobów. Każdy alert jest skonfigurowany do uruchamiania w wstępnie zdefiniowanych okresach. Po wykonaniu każdego alertu agenci Ambari zgłaszają stan do serwera Ambari. W tym momencie, jeśli serwer Ambari wykryje, że którykolwiek z alertów nie był uruchomiony w odpowiednim czasie, wyzwala "alerty serwera Ambari". Istnieją różne przyczyny, dla których Sprawdzenie kondycji może nie być wykonywane w zdefiniowanym interwale:

* Gdy hosty mają duże wykorzystanie (duże użycie procesora CPU), istnieje możliwość, że Agent Ambari nie mógł uzyskać wystarczającej ilości zasobów systemowych do wykonywania alertów w odpowiednim czasie.

* Klaster jest zajęty wykonywaniem wielu zadań/usług podczas dużego obciążenia.

* Niektóre hosty w klastrze mogą obsługiwać wiele składników i dlatego będą wymagane do uruchamiania wielu alertów. Jeśli liczba składników jest duża, istnieje możliwość, że zadania alertów mogą pominąć zaplanowane interwały

## <a name="resolution"></a>Rozdzielczość

### <a name="increase-alert-interval-time"></a>Zwiększ czas interwału alertu

Możesz wybrać opcję zwiększenia wartości poszczególnych interwałów alertów na podstawie czasu odpowiedzi klastra i jego obciążenia.

1. W interfejsie użytkownika Apache Ambari wybierz kartę **alerty** .
1. Wybierz żądaną nazwę definicji alertu.
1. Z definicji wybierz pozycję **Edytuj**.
1. Zmodyfikuj wartość **interwału sprawdzania** zgodnie z potrzebami, a następnie wybierz pozycję **Zapisz**.

### <a name="increase-alert-interval-time-for-ambari-server-alerts"></a>Zwiększ czas interwału alertów dla alertów serwera Ambari

1. W interfejsie użytkownika Apache Ambari wybierz kartę **alerty** .
1. Z listy rozwijanej **grupy** wybierz pozycję **AMBARI default (domyślne**).
1. Wybierz pozycję **alerty alertów serwera Ambari**.
1. Z definicji wybierz pozycję **Edytuj**.
1. Zmodyfikuj wartość **interwału sprawdzania** zgodnie z potrzebami.
1. Zmodyfikuj wartość **mnożnik interwału** zgodnie z potrzebami, a następnie wybierz pozycję **Zapisz**.

### <a name="disable-and-enable-the-alert"></a>Wyłączanie i włączanie alertu

Można wyłączyć i ponownie włączyć alert, aby odrzucić wszystkie nieodświeżone alerty.

1. W interfejsie użytkownika Apache Ambari wybierz kartę **alerty** .
1. Wybierz żądaną nazwę definicji alertu.
1. Z definicji wybierz pozycję **włączone** znajdującą się po prawej stronie.
1. W oknie podręcznym **potwierdzenia** wybierz pozycję **Potwierdź wyłączenie**.
1. Odczekaj kilka sekund, aby wszystkie alerty "wystąpienia" widoczne na stronie zostały wyczyszczone.
1. Z definicji wybierz pozycję **wyłączone** znajdujące się po prawej stronie.
1. W oknie podręcznym **potwierdzenia** wybierz pozycję **Potwierdź włączenie**.

### <a name="increase-alert-grace-time"></a>Zwiększ czas prolongaty alertu

Zanim agent Ambari zgłosi, że skonfigurowany alert został pominięty, zostanie zastosowany czas prolongaty. Nawet jeśli alert minął zaplanowaną godzinę, ale został wyzwolony w czasie prolongaty alertu, nieodświeżony alert nie jest uruchamiany.

Wartość domyślna `alert_grace_period` wynosi 5 sekund. To ustawienie `alert_grace_period` można skonfigurować w `/etc/ambari-agent/conf/ambari-agent.ini`. Dla tych hostów, na których są uruchamiane nieodświeżone alerty w regularnych odstępach czasu, spróbuj zwiększyć wartość 10. Następnie ponownie uruchom agenta Ambari

## <a name="next-steps"></a>Następne kroki

Jeśli problem nie został wyświetlony lub nie można rozwiązać problemu, odwiedź jeden z następujących kanałów, aby uzyskać więcej pomocy:

* Uzyskaj odpowiedzi od ekspertów platformy Azure za pośrednictwem [pomocy technicznej dla społeczności platformy Azure](https://azure.microsoft.com/support/community/).

* Połącz się z [@AzureSupport](https://twitter.com/azuresupport) — oficjalnego Microsoft Azure konta, aby zwiększyć komfort obsługi klienta. Połączenie społeczności platformy Azure z właściwymi zasobami: odpowiedziami, wsparciem i ekspertami.

* Jeśli potrzebujesz więcej pomocy, możesz przesłać żądanie pomocy technicznej z [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Na pasku menu wybierz pozycję **Obsługa** , a następnie otwórz Centrum **pomocy i obsługi technicznej** . Aby uzyskać szczegółowe informacje, zapoznaj [się z tematem jak utworzyć żądanie pomocy technicznej platformy Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Dostęp do pomocy w zakresie zarządzania subskrypcjami i rozliczeń jest dostępny w ramach subskrypcji Microsoft Azure, a pomoc techniczna jest świadczona za pomocą jednego z [planów pomocy technicznej systemu Azure](https://azure.microsoft.com/support/plans/).
