---
title: Wyjątek podczas uruchamiania zapytań z widoku gałęzi Apache Ambari w usłudze Azure HDInsight
description: Kroki rozwiązywania problemów podczas uruchamiania zapytań gałęzi Apache za pośrednictwem apache Ambari Hive View w usłudze Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 12/23/2019
ms.openlocfilehash: 809b2e383eb57b730fd76ec2194764178aa810c0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75895052"
---
# <a name="exception-when-running-queries-from-apache-ambari-hive-view-in-azure-hdinsight"></a>Wyjątek podczas uruchamiania zapytań z widoku gałęzi Apache Ambari w usłudze Azure HDInsight

W tym artykule opisano kroki rozwiązywania problemów i możliwe rozwiązania problemów podczas interakcji z klastrami usługi Azure HDInsight.

## <a name="issue"></a>Problem

Podczas uruchamiania kwerendy gałęzi Apache z apache Ambari Hive View, pojawia się następujący komunikat o błędzie sporadycznie:

```error
Cannot create property 'errors' on string '<!DOCTYPE html PUBLIC '-//W3C//DTD XHTML 1.0 Strict//EN' 'http://www.w3.org/TR/xhtml1/DTD/xhtml1-strict.dtd'>
<html xmlns='http://www.w3.org/1999/xhtml'>
<head>
<title>IIS 8.5 Detailed Error - 502.3 - Bad Gateway</title>
```

## <a name="cause"></a>Przyczyna

Limit czasu bramy.

Wartość limitu czasu bramy wynosi 2 minuty. Zapytania z Ambari Hive View są `/hive2` przesyłane do punktu końcowego za pośrednictwem bramy. Po pomyślnym skompilowaniu i zaakceptowaniu kwerendy `queryid`program HiveServer zwraca plik . Klienci następnie zachować sondowania stanu kwerendy. Podczas tego procesu jeśli serwer HiveServer nie zwróci odpowiedzi HTTP w ciągu 2 minut, brama HDI zgłasza błąd limitu czasu bramy 502.3 do obiektu wywołującego. Błędy mogą się zdarzyć, gdy kwerenda jest przesyłana do przetwarzania (bardziej prawdopodobne), a także w wywołaniu stanu get (mniej prawdopodobne). Użytkownicy mogli zobaczyć jedną z nich.

Wątek obsługi http ma być szybki: przygotować `queryid`zadanie i zwrócić . Jednak z kilku powodów wszystkie wątki obsługi może być zajęty w wyniku limitów czasu dla nowych zapytań i wywołania stanu get.

### <a name="responsibilities-of-the-http-handler-thread"></a>Obowiązki wątku obsługi HTTP

Gdy klient przesyła kwerendę do HiveServer, wykonuje następujące czynności w wątku pierwszego planu:

* Przeanalizyj żądanie, wykonaj weryfikację semantyczną
* Zdobądź blokadę
* W razie potrzeby wyszukiwanie metastore
* Kompilowanie kwerendy (DDL lub DML)
* Przygotowywanie planu kwerend
* Wykonywanie autoryzacji (uruchamianie wszystkich odpowiednich zasad ranger w bezpiecznych klastrach)

## <a name="resolution"></a>Rozwiązanie

Kilka ogólnych zaleceń, aby poprawić sytuację:

* Jeśli używasz zewnętrznego magazynu mete hive, sprawdź metryki bazy danych i upewnij się, że baza danych nie jest przeciążony. Należy rozważyć skalowanie warstwy bazy danych magazynu metastore.

* Upewnij się, że operacje równoległe jest włączona (dzięki temu wątki obsługi HTTP do uruchamiania równolegle). Aby zweryfikować wartość, uruchom [apache Ambari](../hdinsight-hadoop-manage-ambari.md) i przejdź do **hive** > **configs** > **Advanced** > Custom**hive-site**. Wartość dla `hive.server2.parallel.ops.in.session` powinna `true`być .

* Upewnij się, że jednostka SKU maszyny Wirtualnej klastra nie jest zbyt mała dla obciążenia. Należy rozważyć podział pracy między wiele klastrów. Aby uzyskać więcej informacji, zobacz [Wybieranie typu klastra](../hdinsight-capacity-planning.md#choose-a-cluster-type).

* Jeśli Ranger jest zainstalowany w klastrze, sprawdź, czy istnieje zbyt wiele zasad Ranger, które muszą być oceniane dla każdej kwerendy. Poszukaj zduplikowanych lub niepotrzebnych zasad.

* Sprawdź **wartość Rozmiar sterty HiveServer2** z Ambari. Przejdź do **optymalizacji** > **ustawień** > **konfiguracji gałęzi** > **.** Upewnij się, że wartość jest większa niż 10 GB. Dostosuj w razie potrzeby, aby zoptymalizować wydajność.

* Upewnij się, że zapytanie hive jest dobrze dostrojony. Aby uzyskać więcej informacji, zobacz [Optymalizowanie zapytań hive apache w usłudze Azure HDInsight](../hdinsight-hadoop-optimize-hive-query.md).

## <a name="next-steps"></a>Następne kroki

Jeśli nie widzisz problemu lub nie możesz rozwiązać problemu, odwiedź jeden z następujących kanałów, aby uzyskać więcej pomocy technicznej:

* Uzyskaj odpowiedzi od ekspertów platformy Azure za pośrednictwem [pomocy technicznej platformy Azure Community.](https://azure.microsoft.com/support/community/)

* Połącz [@AzureSupport](https://twitter.com/azuresupport) się z — oficjalnym kontem platformy Microsoft Azure w celu poprawy jakości obsługi klienta. Łączenie społeczności platformy Azure z odpowiednimi zasobami: odpowiedziami, pomocą techniczną i ekspertami.

* Jeśli potrzebujesz więcej pomocy, możesz przesłać żądanie pomocy z [witryny Azure portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Wybierz **pozycję Obsługa z** paska menu lub otwórz centrum pomocy + pomocy **technicznej.** Aby uzyskać bardziej szczegółowe informacje, zapoznaj [się z instrukcjami tworzenia żądania pomocy technicznej platformy Azure.](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) Dostęp do obsługi zarządzania subskrypcjami i rozliczeń jest dołączony do subskrypcji platformy Microsoft Azure, a pomoc techniczna jest świadczona za pośrednictwem jednego z [planów pomocy technicznej platformy Azure.](https://azure.microsoft.com/support/plans/)
