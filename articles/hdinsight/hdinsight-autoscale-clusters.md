---
title: Automatyczne skalowanie klastrów usługi Azure HDInsight (wersja zapoznawcza)
description: Automatyczne skalowanie klastrów przy użyciu funkcji automatycznego skalowania usługi HDInsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: hrasheed
ms.openlocfilehash: 333eecb11f0bd20c747bc44419fea26765f886c5
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/15/2019
ms.locfileid: "69509104"
---
# <a name="automatically-scale-azure-hdinsight-clusters-preview"></a>Automatyczne skalowanie klastrów usługi Azure HDInsight (wersja zapoznawcza)

> [!Important]
> Funkcja skalowania automatycznego działa tylko w przypadku klastrów Spark, Hive i MapReduce utworzonych po 8 maja 2019. 

Funkcja automatycznego skalowania klastra usługi Azure HDInsight automatycznie skaluje liczbę węzłów procesu roboczego w klastrze. Obecnie nie można skalować innych typów węzłów w klastrze.  Podczas tworzenia nowego klastra usługi HDInsight można ustawić minimalną i maksymalną liczbę węzłów procesu roboczego. Funkcja automatycznego skalowania monitoruje następnie wymagania dotyczące zasobów obciążeń analitycznych i skaluje liczbę węzłów procesu roboczego w górę lub w dół. Ta funkcja nie ma dodatkowych opłat.

## <a name="cluster-compatibility"></a>Zgodność klastra

W poniższej tabeli opisano typy i wersje klastra, które są zgodne z funkcją skalowania automatycznego.

| Version | platforma Spark | Hive | LLAP | HBase | Kafka | Storm | ML |
|---|---|---|---|---|---|---|---|
| HDInsight 3,6 bez ESP | Tak | Yes | Nie | Nie | Nie | Nie | Nie |
| HDInsight 4,0 bez ESP | Tak | Yes | Nie | Nie | Nie | Nie | Nie |
| HDInsight 3,6 z ESP | Tak | Yes | Nie | Nie | Nie | Nie | Nie |
| HDInsight 3,6 z ESP | Tak | Yes | Nie | Nie | Nie | Nie | Nie |

## <a name="how-it-works"></a>Jak to działa

Można wybrać skalowanie oparte na ładowaniu lub skalowanie dla klastra usługi HDInsight. Skalowanie oparte na obciążeniu zmienia liczbę węzłów w klastrze w określonym zakresie, aby zapewnić optymalne użycie procesora i zminimalizować koszt działania.

Skalowanie oparte na harmonogramie zmienia liczbę węzłów w klastrze na podstawie warunków, które zaczną obowiązywać o określonych godzinach. Warunki te umożliwiają skalowanie klastra do żądanej liczby węzłów.

### <a name="metrics-monitoring"></a>Monitorowanie metryk

Automatyczne skalowanie w sposób ciągły monitoruje klaster i zbiera następujące metryki:

* **Łączny czas oczekiwania na procesor**: Łączna liczba rdzeni wymaganych do rozpoczęcia wykonywania wszystkich oczekujących kontenerów.
* **Całkowita liczba oczekujących pamięci**: Całkowita ilość pamięci (w MB) wymagana do uruchomienia wszystkich oczekujących kontenerów.
* **Łączny bezpłatny procesor CPU**: Suma wszystkich nieużywanych rdzeni w aktywnych węzłach procesu roboczego.
* **Całkowita ilość wolnej pamięci**: Suma nieużywanej pamięci (w MB) w węzłach aktywnych procesów roboczych.
* **Użyta pamięć na węzeł**: Obciążenie węzła procesu roboczego. Węzeł procesu roboczego, na którym jest używana 10 GB pamięci, jest uznawany za większy niż proces roboczy z 2 GB używanej pamięci.
* **Liczba wzorców aplikacji na węzeł**: Liczba kontenerów wzorca aplikacji (AM) uruchomionych w węźle procesu roboczego. Węzeł procesu roboczego obsługujący dwa kontenery AM jest uznawany za ważniejszy niż węzeł roboczy obsługujący kontenery zero AM.

Powyższe metryki są sprawdzane co 60 sekund. Funkcja automatycznego skalowania umożliwia skalowanie w górę i w dół w oparciu o te metryki.

### <a name="load-based-cluster-scale-up"></a>Skalowanie klastra opartego na obciążeniach

Po wykryciu następujących warunków automatyczne skalowanie wystawia żądanie skalowania:

* Łączny czas oczekiwania procesora CPU jest większy niż całkowity bezpłatny procesor CPU przez więcej niż 3 minuty.
* Całkowita liczba oczekujących pamięci jest większa niż całkowita ilość wolnej pamięci przez więcej niż 3 minuty.

Usługa HDInsight oblicza, ile nowych węzłów procesu roboczego jest potrzebnych do spełnienia bieżących wymagań procesora i pamięci, a następnie wystawia żądanie skalowania, aby dodać wymaganą liczbę węzłów.

### <a name="load-based-cluster-scale-down"></a>Skalowanie klastra opartego na obciążeniach — w dół

Po wykryciu następujących warunków automatyczne skalowanie wystawia żądanie skalowania w dół:

* Całkowita liczba oczekujących procesorów CPU jest mniejsza niż całkowity bezpłatny procesor CPU przez więcej niż 10 minut.
* Całkowita ilość oczekujących pamięci jest mniejsza niż całkowita ilość wolnej pamięci przez więcej niż 10 minut.

W oparciu o liczbę kontenerów AM na węzeł i bieżące wymagania dotyczące procesora CPU i pamięci, automatyczne skalowanie wystawia żądanie usunięcia pewnej liczby węzłów. Usługa wykrywa również, które węzły są kandydatami do usunięcia na podstawie bieżącego wykonywania zadania. Operacja skalowania w dół najpierw likwidowanie węzłów, a następnie usunięcie ich z klastra.

## <a name="get-started"></a>Wprowadzenie

### <a name="create-a-cluster-with-load-based-autoscaling"></a>Tworzenie klastra z automatycznym skalowaniem na podstawie obciążenia

Aby włączyć funkcję automatycznego skalowania z skalowaniem opartym na obciążeniu, wykonaj następujące czynności w ramach normalnego procesu tworzenia klastra:

1. Wybierz opcję **niestandardowe (rozmiar, ustawienia, aplikacje)** , a nie **szybkie tworzenie**.
1. W obszarze **niestandardowy** krok 5 (**rozmiar klastra**) zaznacz pole wyboru **Automatyczne skalowanie węzła roboczego** .
1. Wybierz opcję opartą na **ładowaniu** w obszarze **Typ automatycznego skalowania**.
1. Wprowadź odpowiednie wartości dla następujących właściwości:  

    * Początkowa **Liczba węzłów procesu roboczego**.  
    * **Minimalna** liczba węzłów procesu roboczego.  
    * **Maksymalna** liczba węzłów procesu roboczego.  

    ![Włącz opcję automatycznego skalowania na podstawie obciążenia węzła procesu roboczego](./media/hdinsight-autoscale-clusters/usingAutoscale.png)

Początkowa liczba węzłów procesu roboczego musi należeć do zakresu od minimum do maksimum włącznie. Ta wartość definiuje początkowy rozmiar klastra podczas jego tworzenia. Minimalna liczba węzłów procesu roboczego musi być większa od zera.

### <a name="create-a-cluster-with-schedule-based-autoscaling"></a>Tworzenie klastra z funkcją automatycznego skalowania opartego na harmonogramie

Aby włączyć funkcję automatycznego skalowania z skalowaniem opartym na harmonogramie, wykonaj następujące czynności w ramach normalnego procesu tworzenia klastra:

1. Wybierz opcję **niestandardowe (rozmiar, ustawienia, aplikacje)** , a nie **szybkie tworzenie**.
1. W obszarze **niestandardowy** krok 5 (**rozmiar klastra**) zaznacz pole wyboru **Automatyczne skalowanie węzła roboczego** .
1. Wprowadź **liczbę węzłów procesu roboczego**, która kontroluje limit skalowania klastra w górę.
1. Wybierz opcję **oparte** na harmonogramie w obszarze **Typ automatycznego skalowania**.
1. Kliknij przycisk **Konfiguruj** , aby otworzyć okno **konfiguracji skalowania automatycznego** .
1. Wybierz strefę czasową, a następnie kliknij pozycję **+ Dodaj warunek**
1. Wybierz dni tygodnia, do których ma zostać zastosowany nowy warunek.
1. Edytuj czas, w którym warunek powinien obowiązywać, oraz liczbę węzłów, do których należy przeskalować klaster.
1. W razie konieczności Dodaj więcej warunków.

    ![Włącz opcję automatycznego skalowania opartego na harmonogramie węzłów procesu roboczego](./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-schedule-creation.png)

Liczba węzłów musi należeć do zakresu od 1 do liczby wprowadzonych węzłów procesu roboczego przed dodaniem warunków.

### <a name="final-creation-steps"></a>Ostateczne kroki tworzenia

W przypadku skalowania opartego na ładowaniu i harmonogramie wybierz typ maszyny wirtualnej dla węzłów procesu roboczego, klikając pozycję **rozmiar węzła procesu roboczego** i **rozmiar węzła głównego**. Po wybraniu typu maszyny wirtualnej dla każdego typu węzła można zobaczyć szacowany zakres kosztów dla całego klastra. Dostosuj typy maszyn wirtualnych tak, aby pasowały do budżetu.

![Włącz opcję automatycznego skalowania opartego na harmonogramie węzłów procesu roboczego](./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-node-size-selection.png)

Twoja subskrypcja ma przydział pojemności dla każdego regionu. Łączna liczba rdzeni węzłów głównych połączonych z maksymalną liczbą węzłów procesu roboczego nie może przekroczyć limitu przydziału pojemności. Ten limit przydziału jest jednak limitem nieelastycznym; zawsze możesz utworzyć bilet pomocy technicznej, aby ułatwić jego zwiększenie.

> [!Note]  
> W przypadku przekroczenia całkowitego limitu przydziału rdzeni zostanie wyświetlony komunikat o błędzie z informacją o tym, że "maksymalny węzeł przekroczył dostępne rdzenie w tym regionie, wybierz inny region lub skontaktuj się z pomocą techniczną, aby zwiększyć przydział".

Aby uzyskać więcej informacji na temat tworzenia klastra usługi HDInsight przy użyciu Azure Portal, zobacz [Tworzenie klastrów opartych na systemie Linux w usłudze HDInsight przy użyciu Azure Portal](hdinsight-hadoop-create-linux-clusters-portal.md).  

### <a name="create-a-cluster-with-a-resource-manager-template"></a>Tworzenie klastra przy użyciu szablonu Menedżer zasobów

#### <a name="load-based-autoscaling"></a>Skalowanie automatyczne przy użyciu obciążenia

Można utworzyć klaster usługi HDInsight z użyciem automatycznego skalowania Azure Resource Manager szablonu, dodając `autoscale` węzeł `computeProfile` `minInstanceCount`  >  `workernode` do sekcji z właściwościami i `maxInstanceCount` jako przedstawiono w poniższym fragmencie kodu JSON.

```json
{
  "name": "workernode",
  "targetInstanceCount": 4,
  "autoscale": {
      "capacity": {
          "minInstanceCount": 2,
          "maxInstanceCount": 10
      }
  },
  "hardwareProfile": {
      "vmSize": "Standard_D13_V2"
  },
  "osProfile": {
      "linuxOperatingSystemProfile": {
          "username": "[parameters('sshUserName')]",
          "password": "[parameters('sshPassword')]"
      }
  },
  "virtualNetworkProfile": null,
  "scriptActions": []
}
```

Aby uzyskać więcej informacji na temat tworzenia klastrów przy użyciu szablonów Menedżer zasobów, zobacz [Tworzenie klastrów Apache Hadoop w usłudze HDInsight przy użyciu szablonów Menedżer zasobów](hdinsight-hadoop-create-linux-clusters-arm-templates.md).  

#### <a name="schedule-based-autoscaling"></a>Skalowanie automatyczne oparte na harmonogramie

Można utworzyć klaster usługi HDInsight z użyciem harmonogramu `autoscale` automatycznego skalowania szablonu Azure Resource Manager, dodając węzeł `computeProfile`  >  `workernode` do sekcji. `autoscale` Węzeł zawiera`timezone` i ,`schedule` który opisuje, kiedy zmiana `recurrence` zostanie przeprowadzona.

```json
{
  "autoscale": {
    "recurrence": {
      "timeZone": "Pacific Standard Time",
      "schedule": [
        {
          "days": [
            "Monday",
            "Tuesday",
            "Wednesday",
            "Thursday",
            "Friday"
          ],
          "timeAndCapacity": {
            "time": "11:00",
            "minInstanceCount": 10,
            "maxInstanceCount": 10
          }
        },
      ]
    }
  },
  "name": "workernode",
  "targetInstanceCount": 4,
}
```

### <a name="enable-and-disable-autoscale-for-a-running-cluster"></a>Włączanie i wyłączanie skalowania automatycznego dla działającego klastra

#### <a name="using-the-azure-portal"></a>Korzystanie z witryny Azure Portal
Aby włączyć automatyczne skalowanie w uruchomionym klastrze, wybierz pozycję **rozmiar klastra** w obszarze **Ustawienia**. Następnie kliknij pozycję **Włącz automatyczne skalowanie**. Wybierz odpowiedni typ automatycznego skalowania i wprowadź opcje skalowania opartego na załadowaniu lub na podstawie harmonogramu. Na koniec kliknij przycisk **Zapisz**.

![Włącz opcję automatycznego skalowania opartego na harmonogramie węzłów procesu roboczego](./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-enable-running-cluster.png)

#### <a name="using-the-rest-api"></a>Korzystanie z interfejsu API REST
Aby włączyć lub wyłączyć funkcję automatycznego skalowania w uruchomionym klastrze przy użyciu interfejsu API REST, należy wysłać żądanie POST do punktu końcowego automatycznego skalowania, jak pokazano w poniższym fragmencie kodu:

```
https://management.azure.com/subscriptions/{subscription Id}/resourceGroups/{resourceGroup Name}/providers/Microsoft.HDInsight/clusters/{CLUSTERNAME}/roles/workernode/autoscale?api-version=2018-06-01-preview
```

Użyj odpowiednich parametrów w ładunku żądania. Poniżej można włączyć automatyczne skalowanie przy użyciu poniższego ładunku JSON. Użyj ładunku `{autoscale: null}` , aby wyłączyć automatyczne skalowanie.

```json
{ autoscale: { capacity: { minInstanceCount: 1, maxInstanceCount: 2 } } }
```

Zobacz poprzednią sekcję na temat [włączania automatycznego skalowania na podstawie obciążenia](#load-based-autoscaling) , aby uzyskać pełny opis wszystkich parametrów ładunku.

## <a name="best-practices"></a>Najlepsze praktyki

### <a name="choosing-load-based-or-schedule-based-scaling"></a>Wybieranie skalowania opartego na ładowaniu lub harmonogramie

Przed podjęciem decyzji o wyborze trybu należy wziąć pod uwagę następujące czynniki:

* WARIANCJA obciążenia: czy obciążenie klastra ma spójny wzorzec w określonych godzinach w określonych dniach. W przeciwnym razie planowanie oparte na obciążeniu jest lepszą opcją.
* Wymagania umowy SLA: Skalowanie automatyczne jest aktywne, a nie predykcyjne. Czy istnieje wystarczające opóźnienie między momentem, kiedy obciążenie zacznie się zwiększać i kiedy klaster musi mieć rozmiar docelowy? Jeśli istnieją ścisłe wymagania umowy SLA, a obciążenie jest stałym znanym wzorcem, jest to lepsza opcja.

### <a name="consider-the-latency-of-scale-up-or-scale-down-operations"></a>Rozważ opóźnienie operacji skalowania w górę lub w dół

Ukończenie operacji skalowania może potrwać od 10 do 20 minut. Podczas konfigurowania niestandardowego harmonogramu należy zaplanować to opóźnienie. Na przykład jeśli potrzebujesz rozmiaru klastra o wartości 20 o godzinie 9:00, ustaw dla wyzwalacza harmonogramu wcześniejszy czas, taki jak 8:30 AM, tak aby operacja skalowania została zakończona przez 9:00 AM.

### <a name="preparation-for-scaling-down"></a>Przygotowanie do skalowania w dół

Podczas skalowania klastra w dół automatyczne skalowanie spowoduje zlikwidowanie węzłów w celu spełnienia rozmiaru docelowego. Jeśli na tych węzłach są uruchomione zadania, Skalowanie automatyczne będzie oczekiwać do czasu ukończenia zadań. Ponieważ każdy węzeł roboczy również pełni rolę w systemie plików HDFS, dane tymczasowe zostaną przesunięte do pozostałych węzłów. Dlatego należy upewnić się, że jest wystarczająca ilość miejsca na pozostałych węzłach do hostowania wszystkich danych tymczasowych. 

Uruchomione zadania będą nadal wykonywane i kończone. Oczekujące zadania będą oczekiwać na zaplanowanie jako normalne z mniejszą liczbą dostępnych węzłów procesu roboczego.

## <a name="monitoring"></a>Monitorowanie

### <a name="cluster-status"></a>Stan klastra

Stan klastra wymieniony w Azure Portal może pomóc w monitorowaniu działań skalowania automatycznego.

![Włącz opcję automatycznego skalowania na podstawie obciążenia węzła procesu roboczego](./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-cluster-status.png)

Na poniższej liście objaśniono wszystkie komunikaty o stanie klastra, które mogą zostać wyświetlone.

| Stan klastra | Wyjaśnienie |
|---|---|
| Działanie | Klaster działa normalnie. Wszystkie poprzednie działania automatycznego skalowania zostały wykonane pomyślnie. |
| Aktualizowanie  | Trwa aktualizowanie konfiguracji automatycznego skalowania klastra.  |
| Konfiguracja usługi HdInsight  | Operacja skalowania w górę lub w dół w dół jest w toku.  |
| Błąd aktualizacji  | Usługa HDInsight napotkała problemy podczas aktualizacji konfiguracji skalowania automatycznego. Klienci mogą zrezygnować z aktualizacji lub wyłączyć automatyczne skalowanie.  |
| Błąd  | Wystąpił problem z klastrem i nie można go użyć. Usuń ten klaster i Utwórz nowy.  |

Aby wyświetlić bieżącą liczbę węzłów w klastrze, przejdź do wykresu **rozmiar klastra** na stronie **Przegląd** klastra lub kliknij pozycję **rozmiar klastra** w obszarze **Ustawienia**.

### <a name="operation-history"></a>Historia operacji

Możesz wyświetlić historię skalowania i skalowania w poziomie klastra w ramach metryk klastra. Możesz również wyświetlić listę wszystkich akcji skalowania w ciągu ostatniego dnia, tygodnia lub innego czasu.

Wybierz pozycję metryki w obszarze **monitorowanie**. Następnie w polu listy rozwijanej **Metryka** kliknij pozycję **Dodaj metrykę** i **liczbę aktywnych procesów roboczych** . Kliknij przycisk w prawym górnym rogu, aby zmienić zakres czasu.

![Włącz opcję automatycznego skalowania opartego na harmonogramie węzłów procesu roboczego](./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-chart-metric.png)


## <a name="next-steps"></a>Następne kroki

* Przeczytaj o najlepszych rozwiązaniach dotyczących skalowania klastrów ręcznie przy [skalowaniu najlepszych](hdinsight-scaling-best-practices.md) rozwiązań
