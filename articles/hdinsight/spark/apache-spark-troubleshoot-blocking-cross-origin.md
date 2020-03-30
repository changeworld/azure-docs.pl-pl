---
title: Błąd Jupyter 404 - "Blokowanie interfejsu API cross origin" - Azure HDInsight
description: Jupyter server 404 "Nie znaleziono" błąd z powodu "Blokowanie cross origin API" w usłudze Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/29/2019
ms.openlocfilehash: e241657186582955d21981f7dfe18856724aa692
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75894409"
---
# <a name="scenario-jupyter-server-404-not-found-error-due-to-blocking-cross-origin-api-in-azure-hdinsight"></a>Scenariusz: Błąd "Nie znaleziono" serwera Jupyter 404 z powodu "Blokowania interfejsu API cross origin" w usłudze Azure HDInsight

W tym artykule opisano kroki rozwiązywania problemów i możliwe rozwiązania problemów podczas korzystania ze składników Platformy Apache Spark w klastrach usługi Azure HDInsight.

## <a name="issue"></a>Problem

Po dotarciu do usługi Jupyter w programie HDInsight jest widoczne okno błędu z napisem "Nie znaleziono". Jeśli sprawdzisz logi Jupytera, zobaczysz coś takiego:

```log
[W 2018-08-21 17:43:33.352 NotebookApp] 404 PUT /api/contents/PySpark/notebook.ipynb (10.16.0.144) 4504.03ms referer=https://pnhr01hdi-corpdir.msappproxy.net/jupyter/notebooks/PySpark/notebook.ipynb
Blocking Cross Origin API request.  
Origin: https://xxx.xxx.xxx, Host: pnhr01.j101qxjrl4zebmhb0vmhg044xe.ax.internal.cloudapp.net:8001
```

Adres IP może również pojawić się w polu "Pochodzenie" w dzienniku Jupyter.

## <a name="cause"></a>Przyczyna

Ten błąd może być spowodowany przez kilka rzeczy:

- Jeśli skonfigurowano reguły sieciowej grupy zabezpieczeń (NSG) w celu ograniczenia dostępu do klastra. Ograniczenie dostępu za pomocą reguł sieciowej grupy sieciowej nadal umożliwia bezpośredni dostęp do Apache Ambari i innych usług przy użyciu adresu IP, a nie nazwy klastra. Jednak podczas uzyskiwania dostępu do Jupyter, można zobaczyć 404 "Nie znaleziono" błąd.

- Jeśli brama HDInsight nadano dostosowanej nazwie DNS `xxx.azurehdinsight.net`innej niż standardowa .

## <a name="resolution"></a>Rozwiązanie

1. Zmodyfikuj pliki jupyter.py w tych dwóch miejscach:

    ```bash
    /var/lib/ambari-server/resources/common-services/JUPYTER/1.0.0/package/scripts/jupyter.py
    /var/lib/ambari-agent/cache/common-services/JUPYTER/1.0.0/package/scripts/jupyter.py
    ```

1. Znajdź linię, która `NotebookApp.allow_origin='\"https://{2}.{3}\"'` mówi: I `NotebookApp.allow_origin='\"*\"'`zmień ją na: .

1. Uruchom ponownie usługę Jupyter z Ambari.

1. Wpisanie `ps aux | grep jupyter` w wierszu polecenia powinno pokazać, że umożliwia dowolne połączenie się z nim dowolnego adresu URL.

Jest to mniej bezpieczne niż ustawienie, które już mieliśmy na miejscu. Ale zakłada się, że dostęp do klastra jest ograniczony i że jeden z zewnątrz może łączyć się z klastrem, ponieważ mamy sieciowej grupy sieciowej w miejscu.

## <a name="next-steps"></a>Następne kroki

Jeśli nie widzisz problemu lub nie możesz rozwiązać problemu, odwiedź jeden z następujących kanałów, aby uzyskać więcej pomocy technicznej:

* Uzyskaj odpowiedzi od ekspertów platformy Azure za pośrednictwem [pomocy technicznej platformy Azure Community.](https://azure.microsoft.com/support/community/)

* Połącz [@AzureSupport](https://twitter.com/azuresupport) się z — oficjalnym kontem platformy Microsoft Azure w celu poprawy jakości obsługi klienta, łącząc społeczność platformy Azure z odpowiednimi zasobami: odpowiedziami, pomocą techniczną i ekspertami.

* Jeśli potrzebujesz więcej pomocy, możesz przesłać żądanie pomocy z [witryny Azure portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Wybierz **pozycję Obsługa z** paska menu lub otwórz centrum pomocy + pomocy **technicznej.** Aby uzyskać bardziej szczegółowe informacje, zapoznaj się z [instrukcjami tworzenia żądania pomocy technicznej platformy Azure.](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) Dostęp do obsługi zarządzania subskrypcjami i rozliczeń jest dołączony do subskrypcji platformy Microsoft Azure, a pomoc techniczna jest świadczona za pośrednictwem jednego z [planów pomocy technicznej platformy Azure.](https://azure.microsoft.com/support/plans/)
