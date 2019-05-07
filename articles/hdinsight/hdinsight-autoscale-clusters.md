---
title: Automatyczne skalowanie klastrów Azure HDInsight (wersja zapoznawcza)
description: Funkcja automatycznego skalowania HDInsight umożliwia automatyczne skalowanie klastrów
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: hrasheed
ms.openlocfilehash: 92f4616a415c4ddebd0d9b2e466536b2f30c486f
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2019
ms.locfileid: "65146396"
---
# <a name="automatically-scale-azure-hdinsight-clusters-preview"></a>Automatyczne skalowanie klastrów Azure HDInsight (wersja zapoznawcza)

Funkcja automatycznego skalowania klastra Azure HDInsight automatycznie skaluje liczbę węzłów procesu roboczego w klastrze górę i w dół. Nie można aktualnie skalować inne rodzaje węzłów w klastrze.  Podczas tworzenia nowego klastra HDInsight można ustawić minimalną i maksymalną liczbę węzłów procesu roboczego. Automatyczne skalowanie następnie monitoruje wymagań dotyczących zasobów obciążenia analizy i liczby węzłów procesu roboczego jest skalowana w górę lub w dół. Nie ma dodatkowych opłat dla tej funkcji.

## <a name="cluster-compatibility"></a>Zgodność z klastra

W poniższej tabeli opisano typy klastrów i wersje, które są zgodne z funkcją automatycznego skalowania.

| Wersja | platforma Spark | Hive | LLAP | HBase | Kafka | Storm | ML |
|---|---|---|---|---|---|---|---|
| HDInsight 3.6 bez ESP | Yes | Yes | Nie | Nie | Nie | Nie | Nie |
| HDInsight 4.0 bez ESP | Yes | Yes | Nie | Nie | Nie | Nie | Nie |
| HDInsight 3.6 z ESP | Yes | Yes | Nie | Nie | Nie | Nie | Nie |
| HDInsight 3.6 z ESP | Yes | Yes | Nie | Nie | Nie | Nie | Nie |

## <a name="how-it-works"></a>Jak to działa

Można wybrać skalowanie na podstawie obciążenia lub na podstawie harmonogramu dla klastra usługi HDInsight. Skalowanie na podstawie obciążenia zmianę liczby węzłów w klastrze, w zakresie, który zostanie ustawiona, aby zapewnić optymalne wykorzystanie procesora CPU i zminimalizowania kosztów uruchomione.

Oparte na harmonogramie zmiany skalowania liczby węzłów w klastrze na podstawie warunków, które zostały wprowadzone w określonym czasie. Te warunki skalowania klastra żądaną liczbę węzłów.

### <a name="metrics-monitoring"></a>Metryki monitorowania

Automatyczne skalowanie stale monitoruje klaster i zbiera następujące metryki:

* **Łączna liczba oczekujących Procesora**: Całkowita liczba rdzeni wymagana do uruchamiania wykonywania wszystkich oczekujących kontenerów.
* **Łączna liczba oczekujących pamięci**: Całkowita pamięć (w MB) wymagane do rozpoczęcia realizacji wszystkie oczekujące kontenerów.
* **Łączna liczba bezpłatnych Procesora**: Suma wszystkich rdzeni nieużywane na węzłach aktywnego procesu roboczego.
* **Łączna ilość wolnej pamięci**: Suma nieużywanej pamięci (w MB) na węzłach aktywnego procesu roboczego.
* **Użycie pamięci na węzeł**: Obciążenia na węzeł procesu roboczego. Węzeł procesu roboczego, na którym jest używane 10 GB pamięci, uznaje się w warunkach obciążenia więcej niż 2 GB pamięci używanej pamięci procesu roboczego.
* **Liczba wzorców aplikacji w każdym węźle**: Liczba kontenerów aplikacji Master (AM) działającym w węźle procesu roboczego. Węzłem procesu roboczego, który jest hostem dwa kontenery AM, uznaje się ważniejsze niż węzłem procesu roboczego, który jest hostem zero kontenery AM.

Powyższe metryki są sprawdzane co 60 sekund. Automatyczne skalowanie sprawia, że decyzje skalowanie w górę i w dół na podstawie tych metryk.

### <a name="load-based-cluster-scale-up"></a>Skalowanie na podstawie obciążenia klastra w górę

Po wykryciu następujące warunki automatycznego skalowania będzie wysłać żądanie skalowania w górę:

* Całkowita liczba oczekujących procesora CPU jest większy niż łączny czas Procesora bezpłatne dla więcej niż trzy minuty.
* Całkowita liczba oczekujących pamięci jest większa od całkowitej ilości wolnej pamięci dla więcej niż trzy minuty.

Usługa HDInsight oblicza liczbę nowych węzłów procesu roboczego są nadal będą spełnione, bieżący procesor CPU i wymagania dotyczące pamięci, a następnie generuje żądanie skalowania w górę, aby dodać wymaganej liczby węzłów.

### <a name="load-based-cluster-scale-down"></a>Na podstawie obciążenia klastra skalowania w dół

Po wykryciu następujące warunki automatycznego skalowania będzie wysłać żądanie skalowania w dół:

* Całkowita liczba oczekujących procesora CPU jest mniejsza niż łączny czas Procesora bezpłatne przez więcej niż 10 minut.
* Całkowita liczba oczekujących pamięci jest mniejsza od całkowitej ilości wolnej pamięci na więcej niż 10 minut.

Automatyczne skalowanie na podstawie liczby kontenerów AM na węzeł i bieżący procesor CPU i wymagania dotyczące pamięci, problemy z wniosek o usunięcie niektórych liczbę węzłów. Usługa wykrywa także, które węzły są kandydatami do usunięcia, w oparciu o bieżące wykonywania zadania. Operacja skalowania w dół najpierw umożliwia wycofanie z eksploatacji węzłów, a następnie usuwa je z klastra.

## <a name="get-started"></a>Rozpoczęcie pracy

### <a name="create-a-cluster-with-load-based-autoscaling"></a>Tworzenie klastra przy użyciu automatycznego skalowania na podstawie obciążenia

Aby włączyć funkcję automatycznego skalowania ze skalowaniem na podstawie obciążenia, wykonaj następujące czynności w ramach procesu tworzenia klastra normalne:

1. Wybierz **niestandardowe (rozmiar, ustawienia, aplikacje)** zamiast **szybkie tworzenie**.
1. Na **niestandardowe** kroku 5 (**rozmiar klastra**), sprawdź **automatyczne skalowanie węzłów procesu roboczego** pola wyboru.
1. Wybierz opcję **na podstawie obciążenia** w obszarze **typu skalowania automatycznego**.
1. Wprowadź odpowiednie wartości dla następujących właściwości:  

    * Początkowa **węzłów liczba procesów roboczych**.  
    * **Co najmniej** liczby węzłów procesu roboczego.  
    * **Maksymalna** liczby węzłów procesu roboczego.  

    ![Włącz opcję automatycznego skalowania opartego na obciążenie węzła procesu roboczego](./media/hdinsight-autoscale-clusters/usingAutoscale.png)

Początkowa liczba węzłów procesu roboczego musi przypadać między minimalną i maksymalną, włącznie. Ta wartość Określa początkowy rozmiar klastra podczas jego tworzenia. Minimalna liczba węzłów procesu roboczego musi być większa niż zero.

### <a name="create-a-cluster-with-schedule-based-autoscaling"></a>Tworzenie klastra przy użyciu automatycznego skalowania na podstawie harmonogramu

Aby włączyć funkcję automatycznego skalowania ze skalowaniem na podstawie harmonogramu, wykonaj następujące czynności w ramach procesu tworzenia klastra normalne:

1. Wybierz **niestandardowe (rozmiar, ustawienia, aplikacje)** zamiast **szybkie tworzenie**.
1. Na **niestandardowe** kroku 5 (**rozmiar klastra**), sprawdź **automatyczne skalowanie węzłów procesu roboczego** pola wyboru.
1. Wprowadź **węzłów liczba procesów roboczych**, która kontroluje limit skalowania w górę do klastra.
1. Wybierz opcję **oparte na harmonogramie** w obszarze **typu skalowania automatycznego**.
1. Kliknij przycisk **Konfiguruj** otworzyć **konfiguracji skalowania automatycznego** okna.
1. Wybierz Twojej strefie czasowej, a następnie kliknij przycisk **+ Dodaj warunek**
1. Wybierz dni tygodnia, w których mają dotyczyć nowy warunek.
1. Edytuj warunek powinno zająć efekt i liczbę węzłów klastra powinien być skalowane do czasu.
1. Jeśli to konieczne, należy dodać więcej warunków.

    ![Włącz opcję Automatyczne skalowanie na podstawie harmonogramu węzła procesu roboczego](./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-schedule-creation.png)

Liczba węzłów musi być między 1 a liczbę węzłów procesu roboczego, wprowadzone przed dodaniem warunków.

### <a name="final-creation-steps"></a>Kroki tworzenia końcowego

Skalowanie na podstawie obciążenia i na podstawie harmonogramu, wybierz typ maszyny Wirtualnej dla węzłów procesu roboczego, klikając **rozmiar węzła procesu roboczego** i **rozmiar węzła w elemencie Head**. Po wybraniu typu maszyny Wirtualnej dla każdego typu węzła, zobaczysz zakres szacowany koszt dla całego klastra. Należy dostosować typy maszyn wirtualnych, dopasowany do Twojego budżetu.

![Włącz opcję Automatyczne skalowanie na podstawie harmonogramu węzła procesu roboczego](./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-node-size-selection.png)

Twoja subskrypcja ma limit przydziału pojemności dla każdego regionu. Łączna liczba rdzeni węzły główne w połączeniu z maksymalną liczbę węzłów procesu roboczego nie może przekraczać limit przydziału pojemności. Jednak ten limit przydziału jest zmienny limit; zawsze możesz utworzyć bilet pomocy technicznej, jego zwiększenia, łatwo.

> [!Note]  
> Jeśli przekroczysz limit przydziału rdzeni całkowity, otrzymasz komunikat o błędzie informujący o tym, "maksymalna węzła przekroczyła dostępnych rdzeni w tym regionie, wybierz inny region lub skontaktuj się z pomocą techniczną, aby zwiększyć limit przydziału."

Aby uzyskać więcej informacji na temat tworzenia klastra HDInsight w witrynie Azure portal, zobacz [opartych na systemie Linux z Tworzenie klastrów w HDInsight przy użyciu witryny Azure portal](hdinsight-hadoop-create-linux-clusters-portal.md).  

### <a name="create-a-cluster-with-a-resource-manager-template"></a>Tworzenie klastra przy użyciu szablonu usługi Resource Manager

#### <a name="load-based-autoscaling"></a>Automatycznego skalowania na podstawie obciążenia

Utworzeniem klastra usługi HDInsight przy użyciu automatycznego skalowania na podstawie obciążenia szablonu usługi Azure Resource Manager, dodając `autoscale` węzeł `computeProfile`  >  `workernode` sekcji z właściwościami `minInstanceCount` i `maxInstanceCount` jako wyświetlane w poniższy fragment kodu json.

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

Aby uzyskać więcej informacji na temat tworzenia klastrów przy użyciu szablonów usługi Resource Manager, zobacz [tworzenie technologii Apache Hadoop clusters w HDInsight przy użyciu szablonów usługi Resource Manager](hdinsight-hadoop-create-linux-clusters-arm-templates.md).  

#### <a name="schedule-based-autoscaling"></a>Na podstawie harmonogramu automatycznego skalowania

Utworzeniem klastra usługi HDInsight przy użyciu automatycznego skalowania na podstawie harmonogramu szablonu usługi Azure Resource Manager, dodając `autoscale` węzeł `computeProfile`  >  `workernode` sekcji. `autoscale` Zawiera węzeł `recurrence` zawierający `timezone` i `schedule` opisujący, gdy zmiany będą miały miejsce.

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

Aby włączyć automatyczne skalowanie na działającego klastra, zaznacz **rozmiar klastra** w obszarze **ustawienia**. Następnie kliknij przycisk **włączyć Skalowanie automatyczne**. Wybierz typ skalowania automatycznego ma, a następnie wprowadź opcje skalowania obciążenia lub harmonogramu. Na koniec kliknij **Zapisz**.

![Włącz opcję Automatyczne skalowanie na podstawie harmonogramu węzła procesu roboczego](./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-enable-running-cluster.png)

## <a name="monitoring"></a>Monitorowanie

### <a name="cluster-status"></a>Stan klastra

Stan klastra, wymienione w witrynie Azure portal może pomóc monitorować działania automatycznego skalowania.

![Włącz opcję automatycznego skalowania opartego na obciążenie węzła procesu roboczego](./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-cluster-status.png)

Wszystkie komunikaty o stanie klastra, które można napotkać są szczegółowo opisane na poniższej liście.

| Stan klastra | Wyjaśnienie |
|---|---|
| Działanie | Klaster działa normalnie. Wszystkie poprzednie działania automatycznego skalowania została ukończona pomyślnie. |
| Aktualizowanie  | Trwa aktualizowanie konfiguracji automatycznego skalowania klastra.  |
| Konfiguracja usługi HdInsight  | Skalowanie klastra w górę lub trwa operacja skalowania w dół.  |
| Błąd podczas aktualizowania  | HDInsight napotkano problemy podczas aktualizowania konfiguracji skalowania automatycznego. Klienci mogą wybrać ponowić próbę aktualizacji lub wyłączyć automatyczne skalowanie.  |
| Błąd  | Coś jest nie tak z klastrem, a nie jest używany. Usunąć ten klaster i Utwórz nowe.  |

Aby wyświetlić bieżącą liczbę węzłów w klastrze, przejdź do **rozmiar klastra** wykres na **Przegląd** strony dla klastra, lub kliknij przycisk **rozmiar klastra** w obszarze  **Ustawienia**.

### <a name="operation-history"></a>Operacja historii

Możesz wyświetlić historię skalowanie w górę i w dół klastra jako część metryki klastra. Wszystkie akcje skalowania można również podać za pośrednictwem poprzedniego dnia, tygodnia lub innych okres czasu.

Wybierz **metryki** w obszarze **monitorowania**. Następnie kliknij przycisk **Dodaj metrykę** i **liczba aktywnych pracowników** z **metryki** polu listy rozwijanej. Kliknij przycisk w prawym górnym rogu, aby zmienić zakres czasu.

![Włącz opcję Automatyczne skalowanie na podstawie harmonogramu węzła procesu roboczego](./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-chart-metric.png)


## <a name="next-steps"></a>Kolejne kroki

* Przeczytaj najlepsze praktyki dotyczące skalowania ręcznie w klastrach [najlepsze rozwiązania dotyczące skalowania](hdinsight-scaling-best-practices.md)
