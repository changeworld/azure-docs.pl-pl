---
title: Automatyczne skalowanie klastrów Azure HDInsight (wersja zapoznawcza)
description: Funkcja automatycznego skalowania HDInsight umożliwia automatyczne skalowanie klastrów
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/19/2019
ms.author: hrasheed
ms.openlocfilehash: 6642f80a40343546285770531ac42423bee779b8
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/12/2019
ms.locfileid: "59526499"
---
# <a name="automatically-scale-azure-hdinsight-clusters-preview"></a>Automatyczne skalowanie klastrów Azure HDInsight (wersja zapoznawcza)

>[!Important]
>Funkcja automatycznego skalowania HDInsight jest obecnie w wersji zapoznawczej. Wyślij wiadomość e-mail na adres hdiautoscalepm@microsoft.com mieć skalowania automatycznego jest włączona dla Twojej subskrypcji.

Klaster usługi Azure HDInsight automatycznie skaluje liczbę węzłów procesu roboczego w klastrze w górę i w dół w funkcji skalowania automatycznego na podstawie obciążenia w ramach wstępnie zdefiniowanego zakresu. Podczas tworzenia nowego klastra HDInsight można ustawić minimalną i maksymalną liczbę węzłów procesu roboczego. Automatyczne skalowanie, a następnie monitoruje analizami wymagania dotyczące zasobów obciążenia i skalowanie liczby węzłów procesu roboczego w górę lub w dół odpowiednio. Nie ma dodatkowych opłat dla tej funkcji.

## <a name="getting-started"></a>Wprowadzenie

### <a name="create-a-cluster-with-the-azure-portal"></a>Tworzenie klastra przy użyciu witryny Azure portal

> [!Note]
> Automatyczne skalowanie jest obecnie obsługiwana tylko w przypadku usługi Azure HDInsight Hive, MapReduce i platformy Spark klastrów w wersji 3.6.

Aby włączyć funkcję automatycznego skalowania, wykonaj następujące czynności w ramach procesu tworzenia klastra normalne:

1. Wybierz **niestandardowe (rozmiar, ustawienia, aplikacje)** zamiast **szybkie tworzenie**.
2. Na **niestandardowe** kroku 5 (**rozmiar klastra**) sprawdź **automatyczne skalowanie węzłów procesu roboczego** pola wyboru.
3. Wprowadź odpowiednie wartości dla następujących właściwości:  

    * Początkowa **węzłów liczba procesów roboczych**.  
    * **Co najmniej** liczby węzłów procesu roboczego.  
    * **Maksymalna** liczby węzłów procesu roboczego.  

![Włącz opcję automatycznego skalowania węzłów procesu roboczego](./media/hdinsight-autoscale-clusters/usingAutoscale.png)

Początkowa liczba węzłów procesu roboczego musi przypadać między minimalną i maksymalną, włącznie. Ta wartość Określa początkowy rozmiar klastra podczas jego tworzenia. Minimalna liczba węzłów procesu roboczego musi być większa niż zero.

Po wybraniu typu maszyny Wirtualnej dla każdego typu węzła, będzie można wyświetlić zakres szacowany koszt dla całego klastra. Następnie można dostosować te ustawienia dopasowany do Twojego budżetu.

Twoja subskrypcja ma limit przydziału pojemności dla każdego regionu. Łączna liczba rdzeni węzły główne w połączeniu z maksymalną liczbę węzłów procesu roboczego nie może przekraczać limit przydziału pojemności. Jednak ten limit przydziału jest zmienny limit; zawsze możesz utworzyć bilet pomocy technicznej, jego zwiększenia, łatwo.

> [!Note]  
> Jeśli przekroczysz limit przydziału rdzeni całkowity, otrzymasz komunikat o błędzie informujący o tym, "maksymalna węzła przekroczyła dostępnych rdzeni w tym regionie, wybierz inny region lub skontaktuj się z pomocą techniczną, aby zwiększyć limit przydziału."

Aby uzyskać więcej informacji na temat tworzenia klastra HDInsight w witrynie Azure portal, zobacz [opartych na systemie Linux z Tworzenie klastrów w HDInsight przy użyciu witryny Azure portal](hdinsight-hadoop-create-linux-clusters-portal.md).  

### <a name="create-a-cluster-with-a-resource-manager-template"></a>Tworzenie klastra przy użyciu szablonu usługi Resource Manager

Aby utworzyć klaster usługi HDInsight przy użyciu szablonu usługi Azure Resource Manager, należy dodać `autoscale` węzeł `computeProfile`  >  `workernode` sekcji z właściwościami `minInstanceCount` i `maxInstanceCount` pokazany na poniższy fragment kodu json.

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

### <a name="enable-and-disable-autoscale-for-a-running-cluster"></a>Włączanie i wyłączanie skalowania automatycznego dla działającego klastra

Tylko można włączyć lub wyłączyć automatyczne skalowanie dla nowych klastrów HDInsight.

## <a name="monitoring"></a>Monitorowanie

Możesz wyświetlić historię skalowanie w górę i w dół klastra jako część metryki klastra. Możesz także wyświetlić listę wszystkich akcji skalowania w ciągu ostatnich dni, tygodni lub dłuższy czas.

## <a name="how-it-works"></a>Jak to działa

### <a name="metrics-monitoring"></a>Metryki monitorowania

Automatyczne skalowanie stale monitoruje klaster i zbiera następujące metryki:

1. **Łączna liczba oczekujących Procesora**: Całkowita liczba rdzeni wymagana do uruchamiania wykonywania wszystkich oczekujących kontenerów.
2. **Łączna liczba oczekujących pamięci**: Całkowita pamięć (w MB) wymagane do rozpoczęcia realizacji wszystkie oczekujące kontenerów.
3. **Łączna liczba bezpłatnych Procesora**: Suma wszystkich rdzeni nieużywane na węzłach aktywnego procesu roboczego.
4. **Łączna ilość wolnej pamięci**: Suma nieużywanej pamięci (w MB) na węzłach aktywnego procesu roboczego.
5. **Użycie pamięci na węzeł**: Obciążenia na węzeł procesu roboczego. Węzeł procesu roboczego, na którym jest używane 10 GB pamięci, uznaje się w warunkach obciążenia więcej niż 2 GB pamięci używanej pamięci procesu roboczego.
6. **Liczba wzorców aplikacji w każdym węźle**: Liczba kontenerów aplikacji Master (AM) działającym w węźle procesu roboczego. Węzłem procesu roboczego, który jest hostem dwa kontenery AM, uznaje się ważniejsze niż węzłem procesu roboczego, który jest hostem zero kontenery AM.

Powyższe metryki są sprawdzane co 60 sekund. Skalowanie automatyczne spowoduje, że decyzje skalowanie w górę i w dół na podstawie tych metryk.

### <a name="cluster-scale-up"></a>Skalowanie klastra w górę

Po wykryciu następujące warunki automatycznego skalowania będzie wysłać żądanie skalowania w górę:

* Całkowita liczba oczekujących procesora CPU jest większy niż łączny czas Procesora bezpłatne więcej niż 3 minuty.
* Całkowita liczba oczekujących pamięci jest większa od całkowitej ilości wolnej pamięci w więcej niż 3 minuty.

Firma Microsoft będzie obliczać, ilu nowych węzłów procesu roboczego są wymagane do spełnić bieżące wymagania dotyczące procesora CPU i pamięci, a następnie wysłać żądanie skalowania w górę, który dodaje tę liczbę nowych węzłach procesów roboczych.

### <a name="cluster-scale-down"></a>Skalowanie klastra w dół

Po wykryciu następujące warunki automatycznego skalowania będzie wysłać żądanie skalowania w dół:

* Całkowita liczba oczekujących procesora CPU jest mniejsza niż łączny czas Procesora bezpłatne przez więcej niż 10 minut.
* Całkowita liczba oczekujących pamięci jest mniejsza od całkowitej ilości wolnej pamięci na więcej niż 10 minut.

Na podstawie liczby kontenerów AM na węzeł i bieżący procesor CPU i wymagania dotyczące pamięci, automatycznego skalowania będzie wystawiać wniosek o usunięcie niektórych liczbę węzłów, określając węzły, które są potencjalnymi kandydatami do usunięcia. Skalowanie w dół wyzwoli likwidowania węzłów, a po węzły zostaną całkowicie zlikwidowane, zostaną one usunięte.

## <a name="next-steps"></a>Kolejne kroki

* Przeczytaj najlepsze praktyki dotyczące skalowania ręcznie w klastrach [najlepsze rozwiązania dotyczące skalowania](hdinsight-scaling-best-practices.md)
