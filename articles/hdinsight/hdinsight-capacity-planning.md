---
title: Planowanie pojemności w usłudze Azure HDInsight klastra
description: Jak określić klastra usługi HDInsight dla pojemności i wydajności.
services: hdinsight
author: maxluk
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 09/22/2017
ms.author: maxluk
ms.openlocfilehash: 4438cff0dcf5e896f39729d9871d4deb3207b4b8
ms.sourcegitcommit: f6e2a03076679d53b550a24828141c4fb978dcf9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/27/2018
ms.locfileid: "43107999"
---
# <a name="capacity-planning-for-hdinsight-clusters"></a>Planowanie pojemności dla klastrów HDInsight

Przed wdrożeniem klastra usługi HDInsight, planowania pojemności żądany klaster, ustalając, wydajność i skalę. Ta pomaga planowania optymalizować koszty i użyteczność. Nie można zmienić niektóre decyzje dotyczące pojemności klastra po wdrożeniu. W przypadku zmiany parametrów wydajności, klaster można demontażu i utworzony ponownie bez utraty danych przechowywanych.

Kluczowe pytania dotyczące planowania pojemności są następujące:

* W poszczególnych regionach geograficznych czy należy wdrożyć klaster?
* Jaka jest pojemność magazynu jest wymagana?
* Jakiego typu klastra należy wdrożyć?
* Jakie rozmiar i typ maszyny wirtualnej (VM) powinny używać węzły klastra?
* Ilu węzłów procesu roboczego klastra powinien mieć?

## <a name="choose-an-azure-region"></a>Wybierz region platformy Azure

Region platformy Azure Określa, gdzie fizycznie aprowizacji klastra. Aby zminimalizować opóźnienie operacji odczytu i zapisu, klastra powinien znajdować się w pobliżu danych.

HDInsight jest dostępna w wielu regionach platformy Azure. Najbliższego regionu, możesz znaleźć *HDInsight Linux* wpis w *dane + analiza* w [Azure produktów dostępnych według regionu](https://azure.microsoft.com/regions/services/).

## <a name="choose-storage-location-and-size"></a>Wybierz lokalizację magazynu i rozmiar

### <a name="location-of-default-storage"></a>Lokalizacja magazynu domyślnego

Domyślny magazyn, konto usługi Azure Storage lub Azure Data Lake Store, musi być w tej samej lokalizacji co klaster. Usługa Azure Storage jest dostępna we wszystkich lokalizacjach. Data Lake Store jest dostępna w niektórych regionach — Zobacz bieżącej dostępności Data Lake Store w ramach *magazynu* w [Azure produktów dostępnych według regionu](https://azure.microsoft.com/regions/services/).

### <a name="location-of-existing-data"></a>Lokalizacja istniejących danych

Jeśli już masz konto magazynu lub Data Lake Store, zawierających dane użytkownika i chcesz używać tego magazynu jako magazynu domyślnego klastra, należy wdrożyć w klastrze w tej samej lokalizacji.

### <a name="storage-size"></a>Rozmiar magazynu

Po utworzeniu klastra usługi HDInsight, wdrożone, możesz dołączyć dodatkowe konta usługi Azure Storage lub dostęp do innych Data Lake Store. Wszystkie konta magazynu muszą znajdować się w tej samej lokalizacji co klaster. Data Lake Store może być w innej lokalizacji, chociaż może to powodować pewne opóźnienie odczytu i zapisu danych.

Usługa Azure Storage ma kilka [limitów pojemności](../azure-subscription-service-limits.md#storage-limits), podczas gdy Data Lake Store jest praktycznie nieograniczona.

Klaster mogą uzyskiwać dostęp z kombinacją różnych kont magazynu. Typowe przykłady:

* Gdy ilość danych prawdopodobnie przekracza pojemność magazynu jednego kontenera magazynu obiektów blob.
* Kiedy szybkość dostępu do kontenera obiektów blob może przekroczyć próg, gdzie występuje ograniczenie.
* W przypadku danych przekazano już kontener obiektów blob, które jest dostępne dla klastra.
* Kiedy chcesz, aby wyodrębnić różne części magazynu ze względów bezpieczeństwa lub w celu ułatwienia administracji.

Klaster z węzłami 48 zalecamy 4 do 8 kont magazynu. Mimo że może już być wystarczające, całkowita ilość miejsca, każde konto magazynu zapewnia dodatkową przepustowość sieci dla węzłów obliczeniowych. Jeśli masz wiele kont magazynu, należy użyć nazwy losowe dla każdego konta magazynu bez prefiksu. Celem losowych nazw jest zmniejszenie prawdopodobieństwo wąskich gardeł pamięci masowej (ograniczanie przepustowości) lub tryb typowych błędów dla wszystkich kont. Lepszą wydajność należy użyć tylko jednego kontenera na koncie magazynu.

## <a name="choose-a-cluster-type"></a>Wybierz typ klastra

Typ klastra określa obciążenia, jakie klastra usługi HDInsight jest skonfigurowany do uruchamiania, takich jak Hadoop, Storm, Kafka i Spark. Aby uzyskać szczegółowy opis typów dostępnych klastrów, zobacz [wprowadzenie do usługi Azure HDInsight](hadoop/apache-hadoop-introduction.md#cluster-types-in-hdinsight). Każdy typ klastra ma topologię wdrażania, która spełnia wymagania dotyczące rozmiaru i liczby węzłów.

## <a name="choose-the-vm-size-and-type"></a>Wybierz rozmiar maszyny Wirtualnej i typ

Każdy typ klastra ma zbiór typy węzłów i każdy typ węzła ma określonych opcji dla ich rozmiar maszyny Wirtualnej i typu.

Aby określić rozmiar klastra optymalne dla aplikacji, można testu porównawczego pojemność klastra i zwiększyć rozmiar wskazane. Na przykład, można użyć symulowanego obciążenia lub *zapytanie canary*. Przy użyciu symulowanego obciążenia możesz uruchamiać oczekiwanego obciążenia w klastrach użycia innego rozmiaru stopniowo zwiększając rozmiar, aż do osiągnięcia żądanego wydajności. Zapytanie canary mogą być wstawiane okresowo wśród innych zapytań produkcji, aby pokazać, czy klaster ma za mało zasobów.

Rozmiar maszyny Wirtualnej i typ jest określany przez wykorzystanie Procesora mocy obliczeniowej, pamięci RAM i opóźnienie sieci:

* Procesor CPU: Rozmiar maszyny Wirtualnej decyduje o liczbie rdzeni. Każdy węzeł można osiągnąć, więcej rdzeni, wyższy stopień obliczeń równoległych. Ponadto niektóre typy maszyn wirtualnych mają szybsze rdzeni.

* Pamięć RAM: Rozmiar maszyny Wirtualnej również połączenia z opisywanym ilość pamięci RAM dostępnej na maszynie wirtualnej. W przypadku obciążeń, które przechowuje dane w pamięci do przetwarzania, a nie odczytu z dysku, upewnij się, węzłów procesu roboczego ma za mało pamięci, aby dopasować dane.

* Sieć: Dla większości typów klastra, danych przetworzonych przez klaster jest nie na dysku lokalnym, ale raczej w usłudze magazynu zewnętrznego, takie jak Data Lake Store lub Azure Storage. Należy wziąć pod uwagę przepustowość sieci i przepustowość między węzłem maszyny Wirtualnej i usługi storage. Przepustowość sieci dostępną dla maszyny Wirtualnej zwykle zwiększa się o większych rozmiarach. Aby uzyskać więcej informacji, zobacz [maszyny Wirtualne o rozmiarach Przegląd](https://docs.microsoft.com/azure/virtual-machines/linux/sizes).

## <a name="choose-the-cluster-scale"></a>Wybór skali klastra

Skalowanie klastra jest określana przez liczbę jej węzłów maszyny Wirtualnej. W przypadku wszystkich typów w klastrze istnieją typy węzłów, które mają określoną skalę i typów węzłów, które obsługują skalowalnego w poziomie. Na przykład klaster może wymagać dokładnie trzy węzły dozorcy lub dwóch węzłów głównych. Węzły procesu roboczego, wykonujących przetwarzanie danych w sposób rozproszonych mogą korzystać z skalowania w poziomie, dodając dodatkowe węzły procesów roboczych.

W zależności od typu klastra zwiększając liczbę węzłów procesu roboczego dodaje dodatkowe możliwości obliczeniowe (takie jak większa liczba rdzeni), ale może również dodać do łączna ilość pamięci wymaganej dla całego klastra do obsługi magazynu w pamięci przetwarzanych danych. Podobnie jak w przypadku wybranego rozmiaru maszyny Wirtualnej i typu, wybierając w skali klastra prawo zazwyczaj osiągnięto empirically, przy użyciu symulowanego obciążenia lub canary zapytania.

Można skalować w poziomie do klastra, aby obsłużenia szczytowego zapotrzebowania obciążenia, a następnie skalować wróci, gdy te dodatkowe węzły nie są już potrzebne. Aby uzyskać więcej informacji, zobacz [klastrów HDInsight skalowania](hdinsight-scaling-best-practices.md).

### <a name="cluster-lifecycle"></a>Cykl życia klastra

Opłaty są naliczane za okres istnienia klastra. Jeśli są tylko określone godziny należy Twojego klastra i działa, możesz to zrobić [Tworzenie klastrów na żądanie przy użyciu usługi Azure Data Factory](hdinsight-hadoop-create-linux-clusters-adf.md). Można również utworzyć skrypty programu PowerShell aprowizować i usunięciu klastra, a następnie Zaplanuj te skrypty przy użyciu [usługi Azure Automation](https://azure.microsoft.com/services/automation/).

> [!NOTE]
> Po usunięciu klastra jej domyślny magazyn metadanych Hive są także usuwane. Aby zachować magazynu metadanych dla następnego ponownego utworzenia klastra, Użyj zewnętrznego magazynu metadanych, takich jak bazy danych Azure lub Oozie.
<!-- see [Using external metadata stores](hdinsight-using-external-metadata-stores.md). -->

### <a name="isolate-cluster-job-errors"></a>Izolowanie błędy zadań klastra

Czasami błędów może wystąpić z powodu równoległe wykonywanie wielu mapowań i ograniczyć składników w klastrze z wieloma węzłami. Aby wyizolować problem, należy spróbować rozproszonego testowania przez uruchomienie współbieżnych wiele zadań w klastrze z jednym węzłem, rozwiń to podejście do uruchamiania wielu zadań jednocześnie na klastrach zawierających więcej niż jeden węzeł. Aby utworzyć jednowęzłowy klaster HDInsight na platformie Azure, użyj *zaawansowane* opcji.

Można również zainstalować środowisko programistyczne z jednym węzłem na komputerze lokalnym i przetestować rozwiązanie. Hortonworks zapewnia z jednym węzłem lokalnego środowiska deweloperskiego dla rozwiązań opartych na usłudze Hadoop, jest przydatne w przypadku weryfikacji koncepcji, początkowego projektowania i testowania. Aby uzyskać więcej informacji, zobacz [Piaskownicą Hortonworks](http://hortonworks.com/products/hortonworks-sandbox/).

Aby zidentyfikować problem na lokalny klaster jednowęzłowy możesz ponownie uruchomić zadania zakończone niepowodzeniem i dostosować dane wejściowe lub użyć mniejszych zestawów danych. Jak uruchamiać te zadania zależy od platformy i typu aplikacji.

## <a name="quotas"></a>Przydziały

Po ustaleniu, klaster docelowy rozmiar maszyny Wirtualnej, skalę i typ., sprawdzić aktualne limity pojemności limit przydziału subskrypcji. W przypadku osiągnięcia limitu przydziału nie można wdrażać nowych klastrów lub skalowania w poziomie z istniejących klastrów, dodając większą liczbę węzłów procesu roboczego. Najbardziej typowe osiągnięto limit przydziału jest limit przydziału rdzeni procesora CPU, który istnieje w subskrypcji, regionów i poziomy serii maszyny Wirtualnej. Na przykład Twoja subskrypcja może mieć 200 łączny limit liczby rdzeni, z limitem rdzeni 30 w Twoim regionie i limitem rdzeni 30 na wystąpieniach maszyn wirtualnych. Możesz [skontaktuj się z pomocą techniczną, aby zażądać zwiększenia limitu przydziału](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request).

Jednak istnieją pewne ograniczenia stały limit przydziału, na przykład pojedynczej subskrypcji platformy Azure może mieć co najwyżej 10 000 rdzeni. Aby uzyskać więcej informacji o tych limitach, zobacz [subskrypcji platformy Azure i limity, przydziały i ograniczenia](https://docs.microsoft.com/azure/azure-subscription-service-limits#limits-and-the-azure-resource-manager).

## <a name="next-steps"></a>Kolejne kroki

* [Konfigurowanie klastrów w HDInsight przy użyciu usługi Hadoop, Spark, Kafka i](hdinsight-hadoop-provision-linux-clusters.md): informacje o sposobie instalowania i konfigurowania klastrów w HDInsight przy użyciu usługi Hadoop, Spark, Kafka, Interactive Hive, HBase, usługi ML i Storm.
* [Monitorowanie wydajności klastra](hdinsight-key-scenarios-to-monitor.md): więcej informacji na temat kluczowych scenariuszy, do monitorowania dla klastra usługi HDInsight, które mogą mieć wpływ na wydajność klastra.
