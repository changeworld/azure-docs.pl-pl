---
title: Planowanie pojemności klastra w usłudze Azure HDInsight
description: Identyfikowanie kluczowych pytań dotyczących planowania pojemności i wydajności klastra usługi Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 04/07/2020
ms.openlocfilehash: 4ede8833fdbdbd57654e6c02147f53e58a17b1de
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80886997"
---
# <a name="capacity-planning-for-hdinsight-clusters"></a>Planowanie wydajności klastrów usługi HDInsight

Przed wdrożeniem klastra HDInsight należy zaplanować dla zamierzonej pojemności klastra, określając wymaganą wydajność i skalę. To planowanie pomaga zoptymalizować zarówno użyteczność, jak i koszty. Niektóre decyzje dotyczące pojemności klastra nie można zmienić po wdrożeniu. Jeśli zmienią się parametry wydajności, klaster można zdemontować i ponownie utworzyć bez utraty przechowywanych danych.

Kluczowe pytania, które należy zadać w celu planowania zdolności produkcyjnych, to:

* W jakim regionie geograficznym należy wdrożyć klaster?
* Ile miejsca potrzebujesz?
* Jaki typ klastra należy wdrożyć?
* Jaki rozmiar i typ maszyny wirtualnej (VM) powinny być używane przez węzły klastra?
* Ile węzłów procesu roboczego powinien mieć klaster?

## <a name="choose-an-azure-region"></a>Wybieranie regionu platformy Azure

Region platformy Azure określa, gdzie klaster jest fizycznie aprowizacji. Aby zminimalizować opóźnienie odczytów i zapisów, klaster powinien znajdować się w pobliżu danych.

Usługa HDInsight jest dostępna w wielu regionach platformy Azure. Aby znaleźć najbliższy region, zobacz [Produkty dostępne według regionu](https://azure.microsoft.com/global-infrastructure/services/?products=hdinsight).

## <a name="choose-storage-location-and-size"></a>Wybieranie lokalizacji i rozmiaru magazynu

### <a name="location-of-default-storage"></a>Lokalizacja domyślnego magazynu

Domyślny magazyn, konto usługi Azure Storage lub usługi Azure Data Lake Storage, musi znajdować się w tej samej lokalizacji co klaster. Usługa Azure Storage jest dostępna we wszystkich lokalizacjach. Data Lake Storage Gen1 jest dostępna w niektórych regionach — zobacz bieżącą [dostępność magazynu usługi Data Lake.](https://azure.microsoft.com/global-infrastructure/services/?products=storage)

### <a name="location-of-existing-data"></a>Lokalizacja istniejących danych

Jeśli chcesz użyć istniejącego konta magazynu lub magazynu usługi Data Lake jako domyślnego magazynu klastra, należy wdrożyć klaster w tej samej lokalizacji.

### <a name="storage-size"></a>Rozmiar magazynu

W wdrożonym klastrze można dołączyć dodatkowe konta usługi Azure Storage lub uzyskać dostęp do innego magazynu usługi Data Lake. Wszystkie konta magazynu muszą znajdować się w tej samej lokalizacji co klaster. Magazyn usługi Data Lake może znajdować się w innej lokalizacji, ale duże odległości mogą powodować pewne opóźnienia.

Usługa Azure Storage ma pewne [limity pojemności,](../azure-resource-manager/management/azure-subscription-service-limits.md#storage-limits)podczas gdy usługa Data Lake Storage Gen1 jest prawie nieograniczona.

Klaster może uzyskać dostęp do kombinacji różnych kont magazynu. Typowe przykłady obejmują:

* Gdy ilość danych może przekroczyć pojemność magazynu pojedynczego kontenera magazynu obiektów blob.
* Gdy szybkość dostępu do kontenera obiektów blob może przekroczyć próg, w którym występuje ograniczanie przepustowości.
* Jeśli chcesz udostępnić dane, zostały już przekazane do kontenera obiektów blob dostępne dla klastra.
* Jeśli chcesz izolować różne części magazynu ze względów bezpieczeństwa lub uprościć administrację.

Aby uzyskać lepszą wydajność, należy używać tylko jednego kontenera na konto magazynu.

## <a name="choose-a-cluster-type"></a>Wybieranie typu klastra

Typ klastra określa obciążenie, które klaster HDInsight jest skonfigurowany do uruchamiania. Typy obejmują [Apache Hadoop](./hadoop/apache-hadoop-introduction.md), [Apache Storm](./storm/apache-storm-overview.md), [Apache Kafka](./kafka/apache-kafka-introduction.md)lub [Apache Spark](./spark/apache-spark-overview.md). Aby uzyskać szczegółowy opis dostępnych typów klastrów, zobacz [Wprowadzenie do usługi Azure HDInsight](hdinsight-overview.md#cluster-types-in-hdinsight). Każdy typ klastra ma określoną topologię wdrażania, która zawiera wymagania dotyczące rozmiaru i liczby węzłów.

## <a name="choose-the-vm-size-and-type"></a>Wybierz rozmiar i typ maszyny Wirtualnej

Każdy typ klastra ma zestaw typów węzłów, a każdy typ węzła ma określone opcje dla ich rozmiaru i typu maszyny Wirtualnej.

Aby określić optymalny rozmiar klastra dla aplikacji, można porównać pojemność klastra i zwiększyć rozmiar, jak wskazano. Na przykład można użyć symulowanego obciążenia lub *kwerendy kanarycznej*. Uruchamianie symulowanych obciążeń w klastrach o różnych rozmiarach. Stopniowo zwiększaj rozmiar, aż do osiągnięcia zamierzonej wydajności. Kwerendę kanaryjną można okresowo wstawiać między innymi zapytaniami produkcyjnymi, aby pokazać, czy klaster ma wystarczającą ilość zasobów.

Aby uzyskać więcej informacji na temat wybierania odpowiedniej rodziny maszyn wirtualnych dla obciążenia, zobacz [Wybieranie odpowiedniego rozmiaru maszyny Wirtualnej dla klastra](hdinsight-selecting-vm-size.md).

## <a name="choose-the-cluster-scale"></a>Wybieranie skali klastra

Skala klastra jest określana przez ilość jego węzłów maszyn wirtualnych. Dla wszystkich typów klastrów istnieją typy węzłów, które mają określoną skalę i typy węzłów, które obsługują skalowanie w poziomie. Na przykład klaster może wymagać dokładnie trzy [węzły Apache ZooKeeper](https://zookeeper.apache.org/) lub dwa węzły główne. Węzły procesu roboczego, które wykonują przetwarzanie danych w sposób rozproszony, korzystają z dodatkowych węzłów procesu roboczego.

W zależności od typu klastra zwiększenie liczby węzłów procesu roboczego dodaje dodatkową pojemność obliczeniową (na przykład więcej rdzeni). Więcej węzłów zwiększy całkowitą ilość pamięci wymaganej dla całego klastra do obsługi przechowywania w pamięci przetwarzanych danych. Podobnie jak w razie wyboru rozmiaru i typu maszyny Wirtualnej, wybranie odpowiedniej skali klastra jest zazwyczaj osiągane empirycznie. Użyj symulowanych obciążeń lub zapytań kanaryjnych.

Można skalować w poziomie klastra, aby sprostać wymaganiom obciążenia szczytowego. Następnie skaluj go z powrotem w dół, gdy te dodatkowe węzły nie są już potrzebne. [Funkcja skalowania automatycznego](hdinsight-autoscale-clusters.md) umożliwia automatyczne skalowanie klastra na podstawie wcześniej ustalonych metryk i chronometrażu. Aby uzyskać więcej informacji na temat ręcznego skalowania klastrów, zobacz [Skalowanie klastrów HDInsight](hdinsight-scaling-best-practices.md).

### <a name="cluster-lifecycle"></a>Cykl życia klastra

Opłata jest naliczana za cały okres istnienia klastra. Jeśli istnieją tylko określone godziny, które są potrzebne klastra, [należy utworzyć klastry na żądanie przy użyciu usługi Azure Data Factory](hdinsight-hadoop-create-linux-clusters-adf.md). Można również utworzyć skrypty programu PowerShell, które aprowizują i usuwaj klaster, a następnie zaplanować te skrypty przy użyciu [usługi Azure Automation](https://azure.microsoft.com/services/automation/).

> [!NOTE]  
> Po usunięciu klastra jego domyślny magazyn mete hive jest również usuwany. Aby utrwalić magazyn meta dla następnego ponownego utworzenia klastra, należy użyć zewnętrznego magazynu metadanych, takiego jak Azure Database lub [Apache Oozie](https://oozie.apache.org/).
<!-- see [Using external metadata stores](hdinsight-using-external-metadata-stores.md). -->

### <a name="isolate-cluster-job-errors"></a>Wyizolowanie błędów zadań klastra

Czasami mogą wystąpić błędy z powodu równoległego wykonywania wielu map i zmniejszyć składniki w klastrze wielowęzłowym. Aby ułatwić wyizolowanie problemu, spróbuj przetestować rozproszone. Uruchom równoczesnych wielu zadań w klastrze węzła pojedynczego procesu roboczego. Następnie rozwiń to podejście, aby uruchomić wiele zadań jednocześnie w klastrach zawierających więcej niż jeden węzeł. Aby utworzyć klaster HDInsight z jednym *`Custom(size, settings, apps)`* węzłem na platformie Azure, użyj tej opcji i użyj wartości 1 dla *liczby węzłów procesu roboczego* w sekcji **Rozmiar klastra** podczas inicjowania obsługi administracyjnej nowego klastra w portalu.

## <a name="quotas"></a>Przydziały

Po określeniu rozmiaru, skalowania i typu maszyny Wirtualnej klastra docelowego sprawdź bieżące limity pojemności przydziału subskrypcji. Po osiągnięciu limitu przydziału nie można wdrożyć nowych klastrów. Lub skaluj w poziomie istniejące klastry, dodając więcej węzłów procesu roboczego. Jedynym limitem przydziału jest przydział rdzeni procesora CPU, który istnieje na poziomie regionu dla każdej subskrypcji. Na przykład subskrypcja może mieć limit 30 rdzeni w regionie wschodnich stanów USA.

Aby sprawdzić dostępne rdzenie, wykonaj następujące czynności:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
2. Przejdź do strony **Przegląd** klastra HDInsight.
3. W menu po lewej stronie wybierz pozycję **Limity przydziałów**.

   Na stronie wyświetlana jest liczba używanych rdzeni, liczba dostępnych rdzeni i całkowita liczba rdzeni.

Jeśli chcesz poprosić o zwiększenie przydziału, wykonaj następujące czynności:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
1. Wybierz **pomoc + wsparcie** w lewym dolnym rogu strony.
1. Wybierz **pozycję Nowe żądanie pomocy technicznej**.
1. Na stronie **Nowe żądanie pomocy technicznej** w obszarze **Podstawy** wybierz następujące opcje:

   - **Typ problemu:** **Limity usług i subskrypcji (przydziały)**
   - **Subskrypcja**: subskrypcja, którą chcesz zmodyfikować
   - **Typ przydziału**: **HDInsight**

     ![Tworzenie żądania pomocy technicznej w celu zwiększenia przydziału podstawowego HDInsight](./media/hdinsight-capacity-planning/hdinsight-quota-support-request.png)

1. Wybierz **dalej: Rozwiązania >>**.
1. Na stronie **Szczegóły** wprowadź opis problemu, wybierz ważność problemu, preferowaną metodę kontaktu i inne wymagane pola.
1. Wybierz **dalej: Przejrzyj + utwórz >>**.
1. Na karcie **Recenzja + utwórz** wybierz pozycję **Utwórz**.

> [!NOTE]  
> Jeśli chcesz zwiększyć podstawowy przydział HDInsight w regionie prywatnym, [prześlij żądanie białej listy](https://aka.ms/canaryintwhitelist).

Możesz [skontaktować się z pomocą techniczną, aby poprosić o zwiększenie przydziału](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request).

Istnieją pewne stałe limity przydziału. Na przykład pojedyncza subskrypcja platformy Azure może mieć co najwyżej 10 000 rdzeni. Aby uzyskać szczegółowe informacje na temat tych limitów, zobacz [Limity subskrypcji i usług platformy Azure, przydziały i ograniczenia](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits).

## <a name="next-steps"></a>Następne kroki

* [Konfigurowanie klastrów w udziale HDInsight za pomocą programu Apache Hadoop, Spark, Kafka i innych](hdinsight-hadoop-provision-linux-clusters.md): Dowiedz się, jak skonfigurować i skonfigurować klastry w udziale HDInsight.
* [Monitorowanie wydajności klastra:](hdinsight-key-scenarios-to-monitor.md)dowiedz się więcej o kluczowych scenariuszach monitorowania klastra HDInsight, które mogą mieć wpływ na pojemność klastra.
