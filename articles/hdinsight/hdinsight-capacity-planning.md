---
title: Planowanie pojemności w usłudze Azure HDInsight klastra
description: Jak określić klastra usługi HDInsight dla pojemności i wydajności.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: hrasheed
ms.openlocfilehash: c910ed9f1160d30e1d4bda2e85b029eb2ad85b02
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/27/2019
ms.locfileid: "66237152"
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

HDInsight jest dostępna w wielu regionach platformy Azure. Aby znaleźć najbliższy region, zobacz *HDInsight* wpis w *Analytics* w [dostępne produkty według regionów](https://azure.microsoft.com/regions/services/).

## <a name="choose-storage-location-and-size"></a>Wybierz lokalizację magazynu i rozmiar

### <a name="location-of-default-storage"></a>Lokalizacja magazynu domyślnego

Domyślny magazyn, konto usługi Azure Storage lub usługi Azure Data Lake Storage, musi być w tej samej lokalizacji co klaster. Usługa Azure Storage jest dostępna we wszystkich lokalizacjach. Data Lake Storage Gen1 jest dostępna w niektórych regionach — Zobacz bieżącej dostępności usługi Data Lake Storage, w obszarze *magazynu* w [Azure produktów dostępnych według regionu](https://azure.microsoft.com/regions/services/).

### <a name="location-of-existing-data"></a>Lokalizacja istniejących danych

Jeśli już masz konto magazynu lub zawierających dane użytkownika usługi Data Lake Storage i chcesz używać tego magazynu jako magazynu domyślnego klastra, należy wdrożyć w klastrze w tej samej lokalizacji.

### <a name="storage-size"></a>Rozmiar magazynu

Po utworzeniu klastra usługi HDInsight, wdrożone, możesz dołączyć dodatkowe konta usługi Azure Storage lub dostęp do innego magazynu usługi Data Lake. Wszystkie konta magazynu muszą znajdować się w tej samej lokalizacji co klaster. Usługi Data Lake Storage może być w innej lokalizacji, chociaż może to powodować pewne opóźnienie odczytu i zapisu danych.

Usługa Azure Storage ma kilka [limitów pojemności](../azure-subscription-service-limits.md#storage-limits), podczas gdy Data Lake Storage Gen1 jest praktycznie nieograniczona.

Klaster mogą uzyskiwać dostęp z kombinacją różnych kont magazynu. Typowe przykłady:

* Gdy ilość danych prawdopodobnie przekracza pojemność magazynu jednego kontenera magazynu obiektów blob.
* Kiedy szybkość dostępu do kontenera obiektów blob może przekroczyć próg, gdzie występuje ograniczenie.
* W przypadku danych przekazano już kontener obiektów blob, które jest dostępne dla klastra.
* Kiedy chcesz, aby wyodrębnić różne części magazynu ze względów bezpieczeństwa lub w celu ułatwienia administracji.

48-węzeł klastra firma Microsoft zaleca 4 do 8 kont magazynu. Mimo że może już być wystarczające, całkowita ilość miejsca, każde konto magazynu zapewnia dodatkową przepustowość sieci dla węzłów obliczeniowych. Jeśli masz wiele kont magazynu, należy użyć nazwy losowe dla każdego konta magazynu bez prefiksu. Celem losowych nazw jest zmniejszenie prawdopodobieństwo wąskich gardeł pamięci masowej (ograniczanie przepustowości) lub tryb typowych błędów dla wszystkich kont. Lepszą wydajność należy użyć tylko jednego kontenera na koncie magazynu.

## <a name="choose-a-cluster-type"></a>Wybierz typ klastra

Określa typ klastra, obciążenia klastra usługi HDInsight jest skonfigurowany do uruchamiania, takie jak [Apache Hadoop](https://hadoop.apache.org/), [Apache Storm](https://storm.apache.org/), [platformy Apache Kafka](https://kafka.apache.org/), lub [ Platforma Apache Spark](https://spark.apache.org/). Aby uzyskać szczegółowy opis typów dostępnych klastrów, zobacz [wprowadzenie do usługi Azure HDInsight](hadoop/apache-hadoop-introduction.md#cluster-types-in-hdinsight). Każdy typ klastra ma topologię wdrażania, która spełnia wymagania dotyczące rozmiaru i liczby węzłów.

## <a name="choose-the-vm-size-and-type"></a>Wybierz rozmiar maszyny Wirtualnej i typ

Każdy typ klastra ma zbiór typy węzłów i każdy typ węzła ma określonych opcji dla ich rozmiar maszyny Wirtualnej i typu.

Aby określić rozmiar klastra optymalne dla aplikacji, można testu porównawczego pojemność klastra i zwiększyć rozmiar wskazane. Na przykład, można użyć symulowanego obciążenia lub *zapytanie canary*. Przy użyciu symulowanego obciążenia możesz uruchamiać oczekiwanego obciążenia w klastrach użycia innego rozmiaru stopniowo zwiększając rozmiar, aż do osiągnięcia żądanego wydajności. Zapytanie canary mogą być wstawiane okresowo wśród innych zapytań produkcji, aby pokazać, czy klaster ma za mało zasobów.

Rozmiar maszyny Wirtualnej i typ jest określany przez wykorzystanie Procesora mocy obliczeniowej, pamięci RAM i opóźnienie sieci:

* CPU: Rozmiar maszyny Wirtualnej decyduje o liczbie rdzeni. Każdy węzeł można osiągnąć, więcej rdzeni, wyższy stopień obliczeń równoległych. Ponadto niektóre typy maszyn wirtualnych mają szybsze rdzeni.

* Pamięć RAM: Rozmiar maszyny Wirtualnej decyduje również o ilość pamięci RAM dostępnej na maszynie wirtualnej. W przypadku obciążeń, które przechowuje dane w pamięci do przetwarzania, a nie odczytu z dysku, upewnij się, węzłów procesu roboczego ma za mało pamięci, aby dopasować dane.

* Network: Dla większości typów klastrów danych przetworzonych przez klaster jest nie na dysku lokalnym, ale raczej w usłudze magazynu zewnętrznego, takie jak magazyn usługi Data Lake lub usługi Azure Storage. Należy wziąć pod uwagę przepustowość sieci i przepustowość między węzłem maszyny Wirtualnej i usługi storage. Przepustowość sieci dostępną dla maszyny Wirtualnej zwykle zwiększa się o większych rozmiarach. Aby uzyskać więcej informacji, zobacz [maszyny Wirtualne o rozmiarach Przegląd](https://docs.microsoft.com/azure/virtual-machines/linux/sizes).

## <a name="choose-the-cluster-scale"></a>Wybór skali klastra

Skalowanie klastra jest określana przez liczbę jej węzłów maszyny Wirtualnej. W przypadku wszystkich typów w klastrze istnieją typy węzłów, które mają określoną skalę i typów węzłów, które obsługują skalowalnego w poziomie. Na przykład klaster może wymagać dokładnie trzech [Apache ZooKeeper](https://zookeeper.apache.org/) węzłów lub dwóch węzłów głównych. Węzły procesu roboczego, wykonujących przetwarzanie danych w sposób rozproszonych mogą korzystać z skalowania w poziomie, dodając dodatkowe węzły procesów roboczych.

W zależności od typu klastra zwiększając liczbę węzłów procesu roboczego dodaje dodatkowe możliwości obliczeniowe (takie jak większa liczba rdzeni), ale może również dodać do łączna ilość pamięci wymaganej dla całego klastra do obsługi magazynu w pamięci przetwarzanych danych. Podobnie jak w przypadku wybranego rozmiaru maszyny Wirtualnej i typu, wybierając w skali klastra prawo zazwyczaj osiągnięto empirically, przy użyciu symulowanego obciążenia lub canary zapytania.

Można skalować w poziomie do klastra, aby obsłużenia szczytowego zapotrzebowania obciążenia, a następnie skalować wróci, gdy te dodatkowe węzły nie są już potrzebne. Aby uzyskać więcej informacji, zobacz [klastrów HDInsight skalowania](hdinsight-scaling-best-practices.md).

### <a name="cluster-lifecycle"></a>Cykl życia klastra

Opłaty są naliczane za okres istnienia klastra. Jeśli są tylko określone godziny należy Twojego klastra i działa, możesz to zrobić [Tworzenie klastrów na żądanie przy użyciu usługi Azure Data Factory](hdinsight-hadoop-create-linux-clusters-adf.md). Można również utworzyć skrypty programu PowerShell aprowizować i usunięciu klastra, a następnie Zaplanuj te skrypty przy użyciu [usługi Azure Automation](https://azure.microsoft.com/services/automation/).

> [!NOTE]  
> Po usunięciu klastra jej domyślny magazyn metadanych Hive są także usuwane. Można utrwalić magazynu metadanych dla następnego ponownego utworzenia klastra, Użyj zewnętrznego magazynu metadanych takich jak bazy danych Azure lub [Apache Oozie](https://oozie.apache.org/).
<!-- see [Using external metadata stores](hdinsight-using-external-metadata-stores.md). -->

### <a name="isolate-cluster-job-errors"></a>Izolowanie błędy zadań klastra

Czasami błędów może wystąpić z powodu równoległe wykonywanie wielu map i ograniczyć składników w klastrze z wieloma węzłami. Aby wyizolować problem, należy spróbować rozproszonego testowania przez uruchomienie współbieżnych wiele zadań w klastrze z jednym węzłem, rozwiń to podejście do uruchamiania wielu zadań jednocześnie na klastrach zawierających więcej niż jeden węzeł. Aby utworzyć jednowęzłowy klaster HDInsight na platformie Azure, użyj *zaawansowane* opcji.

Można również zainstalować środowisko programistyczne z jednym węzłem na komputerze lokalnym i przetestować rozwiązanie. Hortonworks zapewnia z jednym węzłem lokalnego środowiska deweloperskiego dla rozwiązań opartych na usłudze Hadoop, jest przydatne w przypadku weryfikacji koncepcji, początkowego projektowania i testowania. Aby uzyskać więcej informacji, zobacz [Piaskownicą Hortonworks](https://hortonworks.com/products/hortonworks-sandbox/).

Aby zidentyfikować problem na lokalny klaster jednowęzłowy możesz ponownie uruchomić zadania zakończone niepowodzeniem i dostosować dane wejściowe lub użyć mniejszych zestawów danych. Jak uruchamiać te zadania zależy od platformy i typu aplikacji.

## <a name="quotas"></a>Przydziały

Po ustaleniu, klaster docelowy rozmiar maszyny Wirtualnej, skalę i typ., sprawdzić aktualne limity pojemności limit przydziału subskrypcji. W przypadku osiągnięcia limitu przydziału nie można wdrażać nowych klastrów lub skalowania w poziomie z istniejących klastrów, dodając większą liczbę węzłów procesu roboczego. Limit przydziału tylko jest limit przydziału rdzeni procesora CPU, znajdującą się na poziomie regionu dla każdej subskrypcji. Na przykład Twoja subskrypcja może mieć 30 limit liczby rdzeni w regionie wschodnie stany USA. Jeśli potrzebujesz zażądać zwiększenia limitu przydziału, wykonaj następujące czynności:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
1. Wybierz **Pomoc i obsługa techniczna** w lewym dolnym rogu strony.
1. Wybierz **nowe żądanie obsługi**.
1. Na **nowe żądanie obsługi** w obszarze **podstawy** , a następnie wybierz następujące opcje:
   - **Typ problemu**: **Limity usług i subskrypcji (przydziały)**
   - **Subskrypcja**: subskrypcji, którą chcesz zmodyfikować
   - **Typ limitu przydziału**: **HDInsight**
    
     ![Utwórz żądanie obsługi, aby zwiększyć limit przydziału rdzeni HDInsight](./media/hdinsight-capacity-planning/hdinsight-quota-support-request.png)

1. Wybierz opcję **Dalej: Rozwiązania >>** .
1. Na **szczegóły** strony, wprowadź opis problemu, wybierz ważność problemu, preferowaną metodę kontaktu i inne wymagane pola.
1. Wybierz opcję **Dalej: Przeglądanie + Tworzenie >>** .
1. Na **przeglądu + Utwórz** zaznacz **Utwórz**.

> [!NOTE]  
> Jeśli chcesz zwiększyć limit przydziału rdzeni HDInsight w regionie prywatnych, [wniosek o dozwolonych](https://aka.ms/canaryintwhitelist).

Możesz [skontaktuj się z pomocą techniczną, aby zażądać zwiększenia limitu przydziału](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request).

Jednak istnieją pewne ograniczenia stały limit przydziału, na przykład pojedynczej subskrypcji platformy Azure może mieć co najwyżej 10 000 rdzeni. Aby uzyskać więcej informacji o tych limitach, zobacz [subskrypcji platformy Azure i limity, przydziały i ograniczenia](https://docs.microsoft.com/azure/azure-subscription-service-limits).

## <a name="next-steps"></a>Kolejne kroki

* [Konfigurowanie klastrów w HDInsight przy użyciu technologii Apache Hadoop, Spark, Kafka i](hdinsight-hadoop-provision-linux-clusters.md): Informacje o sposobie instalowania i konfigurowania klastrów w HDInsight przy użyciu technologii Apache Hadoop, Spark, Kafka, Interactive Hive, HBase, usługi ML i Storm.
* [Monitorowanie wydajności klastra](hdinsight-key-scenarios-to-monitor.md): Więcej informacji na temat kluczowych scenariuszy, do monitorowania dla klastra usługi HDInsight, które mogą mieć wpływ na wydajność klastra.
