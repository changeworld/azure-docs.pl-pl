---
title: Opis i rozwiązywanie błędów WebHCat w programie HDInsight — Azure
description: Dowiedz się, jak o typowych błędach zwracanych przez WebHCat w programie HDInsight i jak je rozwiązać.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 01/01/2020
ms.openlocfilehash: 011ef4f192bbae12be7d2464d5b0526f584821a6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75638854"
---
# <a name="understand-and-resolve-errors-received-from-webhcat-on-hdinsight"></a>Understand and resolve errors received from WebHCat on HDInsight (Opis błędów i rozwiązywanie problemów dotyczących interfejsu API WebHCat w usłudze HDInsight)

Dowiedz się więcej o błędach odebranych podczas korzystania z funkcji WebHCat z programem HDInsight oraz o tym, jak je rozwiązać. WebHCat jest używany wewnętrznie przez narzędzia po stronie klienta, takie jak Azure PowerShell i Narzędzia usługi Data Lake dla programu Visual Studio.

## <a name="what-is-webhcat"></a>Co to jest WebHCat

[WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) jest interfejsem API REST dla [HCatalog](https://cwiki.apache.org/confluence/display/Hive/HCatalog), tabeli i warstwy zarządzania pamięcią masową dla Apache Hadoop. WebHCat jest domyślnie włączona w klastrach HDInsight i jest używany przez różne narzędzia do przesyłania zadań, uzyskania stanu zadania i tak dalej, bez logowania się do klastra.

## <a name="modifying-configuration"></a>Modyfikowanie konfiguracji

Kilka błędów wymienionych w tym dokumencie występuje, ponieważ skonfigurowana maksymalna została przekroczona. Gdy krok rozpoznawania wspomina, że można zmienić wartość, użyj Apache Ambari (web lub REST API), aby zmodyfikować wartość. Aby uzyskać więcej informacji, zobacz [Zarządzanie dostępem hdinsight za pomocą apache Ambari](hdinsight-hadoop-manage-ambari.md)

### <a name="default-configuration"></a>Konfiguracja domyślna

W przypadku przekroczenia następujących wartości domyślnych może to obniżyć wydajność webhcat lub spowodować błędy:

| Ustawienie | Wyniki działania | Wartość domyślna |
| --- | --- | --- |
| [yarn.scheduler.capacity.maximum-applications][maximum-applications] |Maksymalna liczba zadań, które mogą być aktywne jednocześnie (oczekujące lub uruchomione) |10 000 |
| [templeton.exec.max-procs][max-procs] |Maksymalna liczba żądań, które mogą być obsługiwane jednocześnie |20 |
| [mapreduce.jobhistory.max-age-ms][max-age-ms] |Liczba dni, przez które historia zadań jest zachowywana |7 dni |

## <a name="too-many-requests"></a>Zbyt wiele żądań

**Kod stanu HTTP**: 429

| Przyczyna | Rozwiązanie |
| --- | --- |
| Przekroczono maksymalną liczbę równoczesnych żądań obsługiwanych przez WebHCat na minutę (domyślnie 20) |Zmniejsz obciążenie, aby upewnić się, że nie przesyłasz więcej niż maksymalna liczba równoczesnych `templeton.exec.max-procs`żądań lub zwiększ limit równoczesnych żądań, modyfikując program . Aby uzyskać więcej informacji, zobacz [Modyfikowanie konfiguracji](#modifying-configuration) |

## <a name="server-unavailable"></a>Serwer niedostępny

**Kod stanu HTTP**: 503

| Przyczyna | Rozwiązanie |
| --- | --- |
| Ten kod stanu zwykle występuje podczas pracy awaryjnej między głównym i pomocniczym HeadNode dla klastra |Odczekaj dwie minuty, a następnie ponów próbę wykonania operacji |

## <a name="bad-request-content-could-not-find-job"></a>Nieprawidłowa treść żądania: nie można znaleźć pracy

**Kod stanu HTTP:** 400

| Przyczyna | Rozwiązanie |
| --- | --- |
| Szczegóły pracy zostały oczyszczone przez sprzątaczkę historii pracy |Domyślny okres przechowywania dla historii zadań wynosi 7 dni. Domyślny okres przechowywania można zmienić, modyfikując `mapreduce.jobhistory.max-age-ms`program . Aby uzyskać więcej informacji, zobacz [Modyfikowanie konfiguracji](#modifying-configuration) |
| Praca została zabita z powodu pracy awaryjnej |Ponów próbę złożenia zadania przez maksymalnie dwie minuty |
| Użyto nieprawidłowego identyfikatora zadania |Sprawdź, czy identyfikator zadania jest poprawny |

## <a name="bad-gateway"></a>Zła brama

**Kod stanu HTTP**: 502

| Przyczyna | Rozwiązanie |
| --- | --- |
| Wewnętrzna zbiórka elementów bezużytecznych występuje w procesie WebHCat |Poczekaj na zakończenie wyrzucania elementów bezużytecznych lub ponowne uruchomienie usługi WebHCat |
| Limit czasu oczekiwania na odpowiedź z usługi ResourceManager. Ten błąd może wystąpić, gdy liczba aktywnych aplikacji idzie skonfigurowane maksimum (domyślnie 10,000) |Poczekaj na ukończenie bieżących zadań lub zwiększenie limitu równoczesnych zadań przez zmodyfikowanie `yarn.scheduler.capacity.maximum-applications`pliku . Aby uzyskać więcej informacji, zobacz sekcję [Modyfikowanie konfiguracji.](#modifying-configuration) |
| Próba pobrania wszystkich zadań za pośrednictwem wywołania `Fields` [GET /jobs,](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference+Jobs) gdy jest ustawiona na`*` |Nie pobieraj *wszystkich* szczegółów zadania. Zamiast tego `jobid` służy do pobierania szczegółów dla zadań tylko większe niż niektóre identyfikatory zadania. Lub nie używaj`Fields` |
| Usługa WebHCat jest wyłączna podczas pracy awaryjnej HeadNode |Odczekaj dwie minuty i ponów próbę wykonania operacji |
| Istnieje ponad 500 oczekujących ofert pracy przesłanych za pośrednictwem WebHCat |Poczekaj, aż aktualnie oczekujące zadania zostaną ukończone przed przesłaniem większej liczby zadań |

[maximum-applications]: https://docs.cloudera.com/HDPDocuments/HDP2/HDP-2.1.3/bk_system-admin-guide/content/setting_application_limits.html
[max-procs]: https://cwiki.apache.org/confluence/display/Hive/WebHCat+Configure#WebHCatConfigure-WebHCatConfiguration
[max-age-ms]: https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.0.6.0/ds_Hadoop/hadoop-mapreduce-client/hadoop-mapreduce-client-core/mapred-default.xml
