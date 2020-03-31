---
title: Apache Ambari starych alertów w usłudze Azure HDInsight
description: Omówienie i analiza możliwych przyczyn i rozwiązań dla alertów Apache Ambari w HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/22/2020
ms.openlocfilehash: f9dfcb930e3fe4f862f9f51ff00270d0eb0c66ca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77539114"
---
# <a name="scenario-apache-ambari-stale-alerts-in-azure-hdinsight"></a>Scenariusz: Apache Ambari starych alertów w usłudze Azure HDInsight

W tym artykule opisano kroki rozwiązywania problemów i możliwe rozwiązania problemów podczas interakcji z klastrami usługi Azure HDInsight.

## <a name="issue"></a>Problem

W interfejsie apache Ambari może pojawić się alert w ten sposób:

![Apache Ambari nieświeży przykład alertu](./media/apache-ambari-troubleshoot-stale-alerts/ambari-stale-alerts-example.png)

## <a name="cause"></a>Przyczyna

Agenci Ambari stale monitorują kondycję wielu zasobów. *Alerty* można skonfigurować tak, aby powiadamiały, czy określone właściwości klastra mieszczą się w określonych progach. Po uruchomieniu każdego sprawdzania zasobów, jeśli warunek alertu jest spełniony, agenci Ambari zgłaszają stan z powrotem na serwer Ambari i wyzwalają alert. Jeśli alert nie jest sprawdzany zgodnie z interwałem w profilu alertów, serwer wyzwala alert *Alerty serwera Ambari.*

Istnieją różne powody, dla których sprawdzanie kondycji może nie działać w zdefiniowanym przedziale czasu:

* Hosty są intensywnie używane (wysokie użycie procesora CPU), dzięki czemu agent Ambari nie może uzyskać wystarczającej ilości zasobów systemowych, aby uruchomić alerty na czas.

* Klaster jest zajęty wykonywanie wielu zadań lub usług w okresie dużego obciążenia.

* Niewielka liczba hostów w klastrze są hosting wiele składników i tak są wymagane do uruchamiania wielu alertów. Jeśli liczba składników jest duża, zadania alertów mogą przegapić zaplanowane interwały.

## <a name="resolution"></a>Rozwiązanie

Wypróbuj następujące metody rozwiązywania problemów z alertami Ambari.

### <a name="increase-the-alert-interval-time"></a>Wydłużyć czas interwału alertów

Można zwiększyć wartość poszczególnych interwałów alertów, na podstawie czasu odpowiedzi i obciążenia klastra:

1. W interfejsie apache ambari wybierz kartę **Alerty.**
1. Wybierz odpowiednią nazwę definicji alertu.
1. Z definicji wybierz pozycję **Edytuj**.
1. Zwiększ wartość **Interwał sprawdzania,** a następnie wybierz pozycję **Zapisz**.

### <a name="increase-the-alert-interval-time-for-ambari-server-alerts"></a>Wydłużyć czas interwału alertów alertów serwera Ambari

1. W interfejsie apache ambari wybierz kartę **Alerty.**
1. Z listy rozwijanej **Grupy** wybierz pozycję **DOMYŚLNA WARTOŚĆ AMBARI**.
1. Wybierz alert **Alerty serwera Ambari.**
1. Z definicji wybierz pozycję **Edytuj**.
1. Zwiększ wartość **Interwał kontrolny.**
1. Zwiększ wartość **mnożnika interwału,** a następnie wybierz pozycję **Zapisz**.

### <a name="disable-and-reenable-the-alert"></a>Wyłączanie i ponowne wynajęcie alertu

Aby odrzucić nieświeże alert, wyłącz go, a następnie ponownie go skonserwować:

1. W interfejsie apache ambari wybierz kartę **Alerty.**
1. Wybierz odpowiednią nazwę definicji alertu.
1. Z definicji wybierz **enabled** w skrajnej prawej części interfejsu użytkownika.
1. W oknie podręcznym **Potwierdzenie** wybierz pozycję **Potwierdź wyłącz .**
1. Poczekaj kilka sekund, aż wszystkie "wystąpienia" alertu pokazane na stronie zostaną wyczyszczone.
1. Z definicji wybierz **wyłączone** w skrajnej prawej części interfejsu użytkownika.
1. W oknie podręcznym **Potwierdzenie** wybierz pozycję **Potwierdź włącz .**

### <a name="increase-the-alert-grace-period"></a>Zwiększenie okresu prolongaty alertu

Istnieje okres prolongaty, zanim agent Ambari zgłasza, że skonfigurowany alert nie pochylił się zgodnie z harmonogramem. Jeśli alert nie został określony, ale został uruchomiony w okresie prolongaty, nie zostanie wygenerowany nieaktualne alerty.

Wartość `alert_grace_period` domyślna to 5 sekund. To ustawienie można skonfigurować w /etc/ambari-agent/conf/ambari-agent.ini. Dla hostów, dla których stare alerty występują w regularnych odstępach czasu, spróbuj zwiększyć wartość do 10. Następnie uruchom ponownie agenta Ambari.

## <a name="next-steps"></a>Następne kroki

Jeśli nie wspomniano tutaj o problemie lub nie możesz go rozwiązać, odwiedź jeden z następujących kanałów, aby uzyskać więcej pomocy:

* Uzyskaj odpowiedzi od ekspertów platformy Azure na [pomoc techniczną platformy Azure Community.](https://azure.microsoft.com/support/community/)

* Połącz [@AzureSupport](https://twitter.com/azuresupport) się z twitterem. Jest to oficjalne konto platformy Microsoft Azure w celu poprawy jakości obsługi klienta. Łączy społeczność platformy Azure z odpowiednimi zasobami: odpowiedziami, pomocą techniczną i ekspertami.

* Jeśli potrzebujesz więcej pomocy, prześlij żądanie pomocy technicznej z [witryny Azure portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Aby się tam dostać, wybierz pomoc (**?**) z menu portalu lub otwórz okienko **pomocy + pomocy.** Aby uzyskać więcej informacji, zobacz [Jak utworzyć żądanie pomocy technicznej platformy Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). 

  Pomoc techniczna w zakresie zarządzania subskrypcjami i rozliczeń jest dołączona do subskrypcji platformy Microsoft Azure. Pomoc techniczna jest dostępna za pośrednictwem [planów pomocy technicznej platformy Azure.](https://azure.microsoft.com/support/plans/)
