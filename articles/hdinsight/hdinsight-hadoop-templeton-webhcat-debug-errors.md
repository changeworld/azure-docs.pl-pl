---
title: Omówienie i rozwiązywanie błędów usługi WebHCat na HDInsight — Azure
description: Dowiedz się, jak na temat typowych błędów zwrócony przy użyciu usługi WebHCat na HDInsight i ich rozwiązania.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/16/2018
ms.author: hrasheed
ms.openlocfilehash: 683580ba65ad775ccec105c78cc1af66fbb63c37
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64691872"
---
# <a name="understand-and-resolve-errors-received-from-webhcat-on-hdinsight"></a>Omówienie i rozwiązywanie błędów odebranych z usługi WebHCat na HDInsight

Więcej informacji na temat błędów odebranych podczas przy użyciu usługi WebHCat, HDInsight i ich rozwiązania. Usługi WebHCat jest używana wewnętrznie przez narzędzia po stronie klienta, takich jak Azure PowerShell i narzędzi Data Lake Tools for Visual Studio.

## <a name="what-is-webhcat"></a>Co to jest WebHCat

[WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) to API typu REST do [HCatalog](https://cwiki.apache.org/confluence/display/Hive/HCatalog), tabeli i magazynu warstwę zarządzania dla usługi Apache Hadoop. Usługi WebHCat jest włączona domyślnie w klastrach HDInsight i jest używany przez różne narzędzia do przesyłania zadań, Pobierz stan zadania, itp., bez potrzeby logowania do klastra.

## <a name="modifying-configuration"></a>Modyfikowanie konfiguracji

> [!IMPORTANT]  
> Wystąpić kilka błędów wymienione w niniejszym dokumencie, ponieważ przekroczono maksymalny skonfigurowany. Gdy krok rozpoznawania uwagi, można zmienić wartości, musi być jedną z następujących dokonać zmiany:

* Aby uzyskać **Windows** klastrów: Aby skonfigurować wartość podczas tworzenia klastra, należy użyć akcji skryptu. Aby uzyskać więcej informacji, zobacz [opracowywanie akcji skryptu](hdinsight-hadoop-script-actions-linux.md).

* Aby uzyskać **Linux** klastrów: Za pomocą systemu Apache Ambari (sieć web lub interfejsu API REST), aby zmodyfikować wartość. Aby uzyskać więcej informacji, zobacz [Zarządzanie HDInsight przy użyciu narzędzia Apache Ambari](hdinsight-hadoop-manage-ambari.md)

> [!IMPORTANT]  
> Linux jest jedynym systemem operacyjnym używanym w połączeniu z usługą HDInsight w wersji 3.4 lub nowszą. Aby uzyskać więcej informacji, zobacz sekcję [HDInsight retirement on Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement) (Wycofanie usługi HDInsight w systemie Windows).

### <a name="default-configuration"></a>Konfiguracja domyślna

Przekroczeniu następujące wartości domyślne go obniżyć wydajność usługi WebHCat, lub spowodować błędy:

| Ustawienie | Wyniki działania | Wartość domyślna |
| --- | --- | --- |
| [yarn.scheduler.capacity.maximum-applications][maximum-applications] |Maksymalna liczba zadań, które mogą być wykonywane jednocześnie (oczekujące na zatwierdzenie lub uruchomione) |10 000 |
| [templeton.exec.max-procs][max-procs] |Maksymalna liczba żądań, które mogą być udostępniane jednocześnie |20 |
| [mapreduce.jobhistory.max-age-ms][max-age-ms] |Liczba dni, Historia zadania, które zostaną zachowane. |7 dni |

## <a name="too-many-requests"></a>Za dużo żądań

**Kod stanu HTTP**: 429

| Przyczyna | Rozwiązanie |
| --- | --- |
| Przekroczono maksymalną równoczesnych żądań obsługiwanych przez usługi WebHCat na minutę (domyślnie 20) |Zmniejszenie obciążenia, aby upewnić się, że nie przesłać więcej niż maksymalna liczba jednoczesnych żądań lub zwiększ limit współbieżnych żądań, modyfikując `templeton.exec.max-procs`. Aby uzyskać więcej informacji, zobacz [modyfikowanie konfiguracji](#modifying-configuration) |

## <a name="server-unavailable"></a>Serwer jest niedostępny

**Kod stanu HTTP**: 503

| Przyczyna | Rozwiązanie |
| --- | --- |
| Ten kod stanu występuje przeważnie podczas trybu failover między podstawowego i pomocniczego węzła głównego klastra |Zaczekaj dwie minuty, a następnie spróbuj ponownie wykonać operację |

## <a name="bad-request-content-could-not-find-job"></a>Nieprawidłowe żądanie zawartości: Nie można odnaleźć zadania

**Kod stanu HTTP**: 400

| Przyczyna | Rozwiązanie |
| --- | --- |
| Szczegóły zadania zostały wyczyszczone, Historia zadania czyszcząca |Domyślny okres przechowywania historii zadań wynosi 7 dni. Domyślny okres przechowywania można zmienić, modyfikując `mapreduce.jobhistory.max-age-ms`. Aby uzyskać więcej informacji, zobacz [modyfikowanie konfiguracji](#modifying-configuration) |
| Zadania został zamknięty z powodu przejścia w tryb failover |Ponów zadanie przesyłania dla dwóch minut |
| Nieprawidłowy identyfikator zadania został użyty. |Sprawdź, czy identyfikator zadania jest poprawny |

## <a name="bad-gateway"></a>Zła brama

**Kod stanu HTTP**: 502

| Przyczyna | Rozwiązanie |
| --- | --- |
| Wewnętrzny wyrzucania elementów bezużytecznych ma miejsce w ramach procesu usługi WebHCat |Poczekaj, aż wyrzucania elementów bezużytecznych zakończyć lub ponownego uruchomienia usługi WebHCat |
| Przekroczono limit czasu oczekiwania na odpowiedź z usługi Menedżera zasobów. Ten błąd może wystąpić, gdy liczba aktywnych aplikacji skonfigurowaną liczbę maksymalną (domyślnie 10 000) |Poczekaj, aż aktualnie uruchomione zadania, aby ukończyć lub zwiększ limit współbieżnych zadań, modyfikując `yarn.scheduler.capacity.maximum-applications`. Aby uzyskać więcej informacji, zobacz [modyfikowanie konfiguracji](#modifying-configuration) sekcji. |
| Próby pobrania wszystkich zadań za pośrednictwem [GET /jobs](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference+Jobs) wywołania podczas `Fields` jest równa `*` |Nie pobierają *wszystkich* szczegóły zadania. Zamiast tego użyć `jobid` można pobrać szczegółów dla zadania tylko większa niż określone identyfikatory zadań. Nie używaj `Fields` |
| Usługi WebHCat nie działa podczas trybu failover węzła głównego |Poczekaj na dwie minuty, a następnie spróbuj ponownie wykonać operację |
| Istnieje więcej niż 500 oczekujące zadania przesłane za pośrednictwem usługi WebHCat |Zaczekaj, aż obecnie w stanie oczekiwania zadania zostały zakończone przed przesłaniem jednego zadania |

[maximum-applications]: https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.3/bk_system-admin-guide/content/setting_application_limits.html
[max-procs]: https://cwiki.apache.org/confluence/display/Hive/WebHCat+Configure#WebHCatConfigure-WebHCatConfiguration
[max-age-ms]: https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.0.6.0/ds_Hadoop/hadoop-mapreduce-client/hadoop-mapreduce-client-core/mapred-default.xml
