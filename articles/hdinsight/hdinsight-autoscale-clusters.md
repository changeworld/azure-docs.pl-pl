---
title: Automatyczne skalowanie klastrów Azure HDInsight (wersja zapoznawcza)
description: Funkcja automatycznego skalowania HDInsight umożliwia automatyczne skalowanie klastrów
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/21/2019
ms.author: hrasheed
ms.openlocfilehash: 977466d77bee2d6fe49c0438aa1d9d4489f8eb5f
ms.sourcegitcommit: b4755b3262c5b7d546e598c0a034a7c0d1e261ec
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/24/2019
ms.locfileid: "54888230"
---
# <a name="automatically-scale-azure-hdinsight-clusters-preview"></a>Automatyczne skalowanie klastrów Azure HDInsight (wersja zapoznawcza)

Klaster usługi Azure HDInsight automatycznie skaluje liczbę węzłów procesu roboczego w klastrze w górę i w dół w funkcji skalowania automatycznego na podstawie obciążenia w ramach wstępnie zdefiniowanego zakresu. Podczas tworzenia nowego klastra HDInsight można ustawić minimalną i maksymalną liczbę węzłów procesu roboczego. Automatyczne skalowanie, a następnie monitoruje analizami wymagania dotyczące zasobów obciążenia i skalowanie liczby węzłów procesu roboczego w górę lub w dół odpowiednio. Nie ma dodatkowych opłat dla tej funkcji.

## <a name="getting-started"></a>Wprowadzenie

### <a name="create-cluster-with-azure-portal"></a>Tworzenie klastra przy użyciu witryny Azure portal

> [!Note]
> Automatyczne skalowanie jest obecnie obsługiwana tylko w przypadku usługi Azure HDInsight Hive, MapReduce i platformy Spark klastrów w wersji 3.6.

Kroki tworzenia klastra HDInsight w kompletny przy użyciu witryny Azure Portal znajduje się w temacie [opartych na systemie Linux z Tworzenie klastrów w HDInsight przy użyciu witryny Azure portal](hdinsight-hadoop-create-linux-clusters-portal.md).  Włączanie automatycznego skalowania podczas procesu tworzenia wymaga kilku odchyleń od kroki zwykle instalacji.  

1. Wybierz **niestandardowe (rozmiar, ustawienia, aplikacje)** zamiast **szybkie tworzenie**.
2. Krok 5 — niestandardowe **rozmiar klastra**, sprawdź **automatyczne skalowanie węzłów procesu roboczego** pole wyboru.
3. Wprowadź odpowiednie wartości dla:  
  &#8226;Początkowa **węzłów liczba procesów roboczych**.  
  &#8226;**Minimum** liczba węzłów procesu roboczego.  
  &#8226;**Maksymalna** liczby węzłów procesu roboczego.  

![Włącz opcję automatycznego skalowania węzłów procesu roboczego](./media/hdinsight-autoscale-clusters/usingAutoscale.png)


Początkowa liczba węzłów procesu roboczego musi przypadać między minimalną i maksymalną, włącznie. Ta wartość Określa początkowy rozmiar klastra podczas jego tworzenia. Minimalna liczba węzłów procesu roboczego musi być większa niż zero.

Po wybraniu typu maszyny Wirtualnej dla każdego typu węzła, będzie można wyświetlić zakres szacowany koszt dla całego klastra. Następnie można dostosować te ustawienia dopasowany do Twojego budżetu.

Twoja subskrypcja ma limit przydziału pojemności dla każdego regionu. Łączna liczba rdzeni węzły główne w połączeniu z maksymalną liczbę węzłów procesu roboczego nie może przekraczać limit przydziału pojemności. Jednak ten limit przydziału jest zmienny limit; zawsze możesz utworzyć bilet pomocy technicznej, jego zwiększenia, łatwo.

> [!Note]  
> Jeśli przekroczysz limit przydziału rdzeni całkowity, otrzymasz komunikat o błędzie informujący o tym, "maksymalna węzła przekroczyła dostępnych rdzeni w tym regionie, wybierz inny region lub skontaktuj się z pomocą techniczną, aby zwiększyć limit przydziału."

### <a name="create-cluster-with-an-resource-manager-template"></a>Tworzenie klastra przy użyciu szablonu usługi Resource Manager

Kroki tworzenia klastra HDInsight w kompletny przy użyciu szablonów usługi Resource Manager znajduje się w temacie [tworzenie technologii Apache Hadoop clusters w HDInsight przy użyciu szablonów usługi Resource Manager](hdinsight-hadoop-create-linux-clusters-arm-templates.md).  Podczas tworzenia klastra usługi HDInsight przy użyciu szablonu usługi Azure Resource Manager, należy dodać następujące ustawienia w sekcji "workernode" "computeProfile" i odpowiednio ją edytować:

```json
{                            
    "name": "workernode",
    "targetInstanceCount": 4,
    "autoscale": {
        "minInstanceCount": 2,
        "maxInstanceCount": 10
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

### <a name="enable-and-disabling-autoscale-for-a-running-cluster"></a>Włączanie i wyłączanie funkcji automatycznego skalowania działającego klastra

Włączanie automatycznego skalowania dla działającego klastra nie jest obsługiwana w prywatnej wersji zapoznawczej. Musi być włączona podczas tworzenia klastra.

Wyłączanie automatycznego skalowania lub modyfikowanie ustawień automatycznego skalowania działającego klastra nie jest obsługiwana w prywatnej wersji zapoznawczej. Należy usunąć klaster i Utwórz nowy klaster, aby usunąć lub zmodyfikować ustawienia.

## <a name="monitoring"></a>Monitorowanie

Możesz wyświetlić historię jako część metryki klastra w górę i w dół w skali klastra. Możesz wyświetlić listę wszystkich akcji skalowania w ciągu ostatnich dni, tygodni lub dłuższy czas.

## <a name="how-it-works"></a>Jak to działa

### <a name="metrics-monitoring"></a>Metryki monitorowania

Automatyczne skalowanie stale monitoruje klaster i zbiera następujące metryki:

1. **Łączna liczba oczekujących Procesora**: Całkowita liczba rdzeni wymagana do uruchamiania wykonywania wszystkich oczekujących kontenerów.
2. **Łączna liczba oczekujących pamięci**: Całkowita pamięć (w MB) wymagane do rozpoczęcia realizacji wszystkie oczekujące kontenerów.
3. **Łączna liczba bezpłatnych Procesora**: Suma wszystkich rdzeni nieużywane na węzłach aktywnego procesu roboczego.
4. **Łączna ilość wolnej pamięci**: Suma nieużywanej pamięci (w MB) na węzłach aktywnego procesu roboczego.
5. **Użycie pamięci na węzeł**: Obciążenia na węzeł procesu roboczego. Węzeł procesu roboczego, na którym jest używane 10 GB pamięci, uznaje się w warunkach obciążenia więcej niż 2 GB pamięci używanej pamięci procesu roboczego.
6. **Liczba wzorców aplikacji w każdym węźle**: Liczba kontenerów aplikacji Master (AM) działającym w węźle procesu roboczego. Węzeł procesu roboczego, w którym kontenery 2: 00, jest uznawana za ważniejsze niż węzeł procesu roboczego, w którym 0 AM kontenerów.

Powyższe metryki są sprawdzane co 60 sekund. Skalowanie automatyczne spowoduje tworzą skalowania i skalowanie w dół decyzje na podstawie tych metryk.

### <a name="cluster-scale-up"></a>Skalowanie klastra w górę

Po wykryciu następujące warunki skalowania automatycznego wystawi skalowania w górę żądania:

* Całkowita liczba oczekujących procesora CPU jest większy niż łączny czas Procesora bezpłatne więcej niż 1 minuty.
* Całkowita liczba oczekujących pamięci jest większa od całkowitej ilości wolnej pamięci ponad 1 minuty.

Firma Microsoft będzie obliczać czy N nowych węzłów procesu roboczego są wymagane do spełnić bieżące wymagania dotyczące procesora CPU i pamięci, a następnie wystawiania skalowania w górę żądania przez zażądanie N nowych węzłach procesów roboczych.

### <a name="cluster-scale-down"></a>Skalowanie klastra w dół

Po wykryciu następujące warunki skalowania automatycznego wystawi skalowania w dół na żądanie:

* Całkowita liczba oczekujących procesora CPU jest mniejsza niż łączny czas Procesora bezpłatne przez więcej niż 10 minut.
* Całkowita liczba oczekujących pamięci jest mniejsza od całkowitej ilości wolnej pamięci na więcej niż 10 minut.

Na podstawie liczby kontenerów AM na węzeł, a także bieżące wymagania dotyczące procesora CPU i pamięci, automatycznego skalowania będzie wystawiać wniosek o usunięcie węzłów N, określając węzły, które są potencjalnymi kandydatami do usunięcia. Domyślnie zostanie usunięty z uwzględnieniem dwóch węzłów w jednym cyklu.

## <a name="next-steps"></a>Kolejne kroki

* Przeczytaj najlepsze praktyki dotyczące skalowania ręcznie w klastrach [najlepsze rozwiązania dotyczące skalowania](hdinsight-scaling-best-practices.md)
