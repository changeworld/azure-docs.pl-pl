---
title: Zrozumienia i rozwiązania błędów WebHCat w usłudze HDInsight - Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak do około typowe błędy zwrócone przez WebHCat w usłudze HDInsight oraz sposobów ich rozwiązywania.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 1b3d94b1-207d-4550-aece-21dc45485549
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 05/16/2018
ms.author: larryfr
ms.openlocfilehash: 7349d60177982ced68b0ebb83f7e85f19e43ec15
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/29/2018
ms.locfileid: "37100200"
---
# <a name="understand-and-resolve-errors-received-from-webhcat-on-hdinsight"></a>Zrozumienia i rozwiązania błędów, odbierane z usługi WebHCat w usłudze HDInsight

Więcej informacji na temat błędów podczas przy użyciu usługi WebHCat w usłudze HDInsight i sposobu ich rozwiązania. WebHCat jest używana wewnętrznie przez klienta narzędzi, takich jak Azure PowerShell i narzędzi Data Lake Tools dla programu Visual Studio.

## <a name="what-is-webhcat"></a>Co to jest WebHCat

[WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) to interfejs API REST dla [HCatalog](https://cwiki.apache.org/confluence/display/Hive/HCatalog), tabeli i magazynu warstwa zarządzania dla platformy Hadoop. WebHCat jest domyślnie włączone w klastrach HDInsight i jest używany przez różnych narzędzi do przesyłania zadań, Pobierz stan zadania, itp., bez potrzeby logowania do klastra.

## <a name="modifying-configuration"></a>Modyfikowanie konfiguracji

> [!IMPORTANT]
> Występuje kilka błędów wymienione w niniejszym dokumencie, ponieważ została przekroczona maksymalna skonfigurowana. Podczas kroku rozpoznawania uwagi, można zmienić wartości, należy użyć jednej z poniższych Aby dokonać zmiany:

* Dla **Windows** klastrów: Aby skonfigurować wartość podczas tworzenia klastra, użyj akcji skryptu. Aby uzyskać więcej informacji, zobacz [tworzenie akcji skryptów](hdinsight-hadoop-script-actions.md).

* Dla **Linux** klastrów: Użyj Ambari (sieć web lub interfejsu API REST), aby zmodyfikować wartość. Aby uzyskać więcej informacji, zobacz [Zarządzanie HDInsight przy użyciu narzędzia Ambari](hdinsight-hadoop-manage-ambari.md)

> [!IMPORTANT]
> Linux jest jedynym systemem operacyjnym używanym w połączeniu z usługą HDInsight w wersji 3.4 lub nowszą. Aby uzyskać więcej informacji, zobacz sekcję [HDInsight retirement on Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement) (Wycofanie usługi HDInsight w systemie Windows).

### <a name="default-configuration"></a>Domyślna konfiguracja

W przypadku przekroczenia następujące wartości domyślne, możesz obniżyć wydajność usługi WebHCat lub powodować błędy:

| Ustawienie | Wyniki działania | Wartość domyślna |
| --- | --- | --- |
| [yarn.scheduler.capacity.maximum-applications][maximum-applications] |Maksymalna liczba zadań, które mogą być jednocześnie aktywne (oczekiwanie lub uruchomiona) |10 000 |
| [templeton.exec.max-procs][max-procs] |Maksymalna liczba żądań, które mogą być przekazywane jednocześnie |20 |
| [mapreduce.jobhistory.max-age-ms][max-age-ms] |Liczba dni, które Historia zadania są zachowywane. |7 dni |

## <a name="too-many-requests"></a>Za dużo żądań

**Kod stanu HTTP**: 429

| Przyczyna | Rozwiązanie |
| --- | --- |
| Przekroczono maksymalną równoczesnych żądań obsłużonych przez WebHCat na minutę (domyślnie 20) |Zmniejsz obciążenie w taki sposób, aby upewnić się, że nie przesłać więcej niż maksymalna liczba jednoczesnych żądań lub zwiększ limit równoczesnych żądań przez zmodyfikowanie `templeton.exec.max-procs`. Aby uzyskać więcej informacji, zobacz [modyfikowanie konfiguracji](#modifying-configuration) |

## <a name="server-unavailable"></a>Serwer jest niedostępny

**Kod stanu HTTP**: 503

| Przyczyna | Rozwiązanie |
| --- | --- |
| Ten kod stanu zazwyczaj występuje w trybie failover między HeadNode podstawowe i pomocnicze dla klastra |Poczekaj 2 minuty, a następnie spróbuj ponownie wykonać operację |

## <a name="bad-request-content-could-not-find-job"></a>Nieprawidłowe żądanie zawartości: nie można odnaleźć zadania

**Kod stanu HTTP**: 400

| Przyczyna | Rozwiązanie |
| --- | --- |
| Szczegóły zadania zostały wyczyszczone w historii zadań czyszcząca |Domyślny okres przechowywania historii zadań wynosi 7 dni. Domyślny okres przechowywania można zmienić, modyfikując `mapreduce.jobhistory.max-age-ms`. Aby uzyskać więcej informacji, zobacz [modyfikowanie konfiguracji](#modifying-configuration) |
| Zadania został zamknięty z powodu pracy awaryjnej |Spróbuj ponownie przesyłanie zadań do dwóch minut |
| Nieprawidłowy identyfikator zadania został użyty. |Sprawdź, czy identyfikator zadania jest prawidłowa |

## <a name="bad-gateway"></a>Zły bramy

**Kod stanu HTTP**: 502

| Przyczyna | Rozwiązanie |
| --- | --- |
| Wewnętrzny wyrzucanie elementów bezużytecznych jest wykonywana w ramach procesu usługi WebHCat |Poczekaj na wyrzucanie elementów bezużytecznych Zakończ lub ponownego uruchomienia usługi WebHCat |
| Upłynął limit czasu oczekiwania na odpowiedź z usługi ResourceManager. Ten błąd może wystąpić, gdy liczba aktywnych aplikacji skonfigurowane maksimum (domyślnie 10 000) |Poczekaj, aż aktualnie uruchomionych zadań do wykonania lub zwiększ limit równoczesnych zadań, modyfikując `yarn.scheduler.capacity.maximum-applications`. Aby uzyskać więcej informacji, zobacz [modyfikowanie konfiguracji](#modifying-configuration) sekcji. |
| Podjęto próbę pobrania wszystkich zadań za pomocą [GET /jobs](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference+Jobs) wywołania podczas `Fields` ma ustawioną wartość `*` |Nie pobierają *wszystkie* szczegóły zadania. Zamiast tego użyj `jobid` można pobrać szczegółów zadań większe tylko niektórych identyfikator zadania. Nie używaj `Fields` |
| Usługi WebHCat nie działa w trybie failover HeadNode |Poczekaj 2 minuty, a następnie spróbuj ponownie wykonać operację |
| Istnieje więcej niż 500 oczekujące zadania przesłane za pośrednictwem usługi WebHCat |Poczekaj, aż obecnie oczekujące zadania zostały ukończone przed przesłaniem więcej zadań |

[maximum-applications]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.3/bk_system-admin-guide/content/setting_application_limits.html
[max-procs]: https://cwiki.apache.org/confluence/display/Hive/WebHCat+Configure#WebHCatConfigure-WebHCatConfiguration
[max-age-ms]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.0.6.0/ds_Hadoop/hadoop-mapreduce-client/hadoop-mapreduce-client-core/mapred-default.xml
