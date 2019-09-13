---
title: Planowanie pojemności klastra w usłudze Azure HDInsight
description: Zidentyfikuj kluczowe pytania dotyczące planowania pojemności i wydajności klastra usługi Azure HDInsight.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: hrasheed
ms.openlocfilehash: 64de4078fb529140859f1d4ff2e973fd081a5400
ms.sourcegitcommit: 083aa7cc8fc958fc75365462aed542f1b5409623
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/11/2019
ms.locfileid: "70916560"
---
# <a name="capacity-planning-for-hdinsight-clusters"></a>Planowanie pojemności klastrów usługi HDInsight

Przed wdrożeniem klastra usługi HDInsight zaplanuj odpowiednią pojemność klastra, określając potrzebną wydajność i skalę. Takie planowanie pozwala zoptymalizować zarówno użyteczność, jak i koszty. Niektórych decyzji dotyczących pojemności klastra nie można zmienić po wdrożeniu. W przypadku zmiany parametrów wydajności klaster może zostać rozbudowany i utworzony bez utraty przechowywanych danych.

Kluczowe pytania dotyczące planowania pojemności są następujące:

* W którym regionie geograficznym należy wdrożyć klaster?
* Ile miejsca do magazynowania potrzebujesz?
* Jakiego typu klastra należy wdrożyć?
* Jakiego rozmiaru i typu maszyny wirtualnej należy używać w węzłach klastra?
* Ile węzłów procesu roboczego ma mieć klaster?

## <a name="choose-an-azure-region"></a>Wybierz region platformy Azure

Region platformy Azure określa, gdzie klaster jest fizycznie zainicjowany. Aby zminimalizować opóźnienie operacji odczytu i zapisu, klaster powinien znajdować się w sąsiedztwie danych.

Usługa HDInsight jest dostępna w wielu regionach świadczenia usługi Azure. Aby znaleźć najbliższy region, zobacz wpis usługi *HDInsight* w obszarze *Analiza* w obszarze [dostępne produkty według regionów](https://azure.microsoft.com/regions/services/).

## <a name="choose-storage-location-and-size"></a>Wybierz lokalizację i rozmiar magazynu

### <a name="location-of-default-storage"></a>Lokalizacja magazynu domyślnego

Domyślny magazyn, konto usługi Azure Storage lub Azure Data Lake Storage, musi znajdować się w tej samej lokalizacji co klaster. Usługa Azure Storage jest dostępna we wszystkich lokalizacjach. Data Lake Storage Gen1 jest dostępny w niektórych regionach — Sprawdź bieżącą dostępność Data Lake Storage w obszarze *Magazyn* w obszarze [produkty platformy Azure dostępne według regionów](https://azure.microsoft.com/regions/services/).

### <a name="location-of-existing-data"></a>Lokalizacja istniejących danych

Jeśli masz już konto magazynu lub Data Lake Storage zawierające dane i chcesz użyć tego magazynu jako domyślnego magazynu klastra, musisz wdrożyć klaster w tej samej lokalizacji.

### <a name="storage-size"></a>Rozmiar magazynu

Po wdrożeniu klastra usługi HDInsight można dołączyć dodatkowe konta magazynu platformy Azure lub uzyskać dostęp do innych Data Lake Storage. Wszystkie konta magazynu muszą znajdować się w tej samej lokalizacji co klaster. Data Lake Storage może znajdować się w innej lokalizacji, chociaż może to spowodować opóźnienie odczytu/zapisu danych.

Usługa Azure Storage ma pewne [limity pojemności](../azure-subscription-service-limits.md#storage-limits), a Data Lake Storage Gen1 jest praktycznie nieograniczona.

Klaster może uzyskać dostęp do kombinacji różnych kont magazynu. Typowe przykłady to:

* Gdy ilość danych prawdopodobnie przekroczy pojemność magazynu pojedynczego kontenera magazynu obiektów BLOB.
* Gdy częstotliwość dostępu do kontenera obiektów BLOB może przekroczyć próg, w którym występuje ograniczenie przepustowości.
* Gdy chcesz wprowadzić dane, przekazano już do kontenera obiektów BLOB dostępnego w klastrze.
* Jeśli chcesz wyizolować różne części magazynu z powodu zabezpieczeń lub uprościć administrację.

W przypadku klastra z 48 węzłami zalecamy od 4 do 8 kont magazynu. Mimo że może już być wystarczająca ilość miejsca w magazynie, każde konto magazynu oferuje dodatkową przepustowość sieci dla węzłów obliczeniowych. Jeśli masz wiele kont magazynu, użyj nazwy losowej dla każdego konta magazynu, bez prefiksu. Celem przypadkowego nazewnictwa jest zmniejszenie ryzyka związanego z wąskimi gardłami magazynowania (ograniczanie) lub awariami trybu wspólnego na wszystkich kontach. Aby uzyskać lepszą wydajność, należy użyć tylko jednego kontenera na konto magazynu.

## <a name="choose-a-cluster-type"></a>Wybierz typ klastra

Typ klastra określa obciążenie skonfigurowane do uruchamiania klastra usługi HDInsight, takie jak [Apache Hadoop](https://hadoop.apache.org/), [Apache Storm](https://storm.apache.org/), [Apache Kafka](https://kafka.apache.org/)lub [Apache Spark](https://spark.apache.org/). Aby uzyskać szczegółowy opis dostępnych typów klastrów, zobacz [wprowadzenie do usługi Azure HDInsight](hdinsight-overview.md#cluster-types-in-hdinsight). Każdy typ klastra ma określoną topologię wdrożenia, która obejmuje wymagania dotyczące rozmiaru i liczby węzłów.

## <a name="choose-the-vm-size-and-type"></a>Wybierz rozmiar i typ maszyny wirtualnej

Każdy typ klastra ma zestaw typów węzłów, a każdy typ węzła ma konkretne opcje rozmiaru i typu maszyny wirtualnej.

Aby określić optymalny rozmiar klastra dla aplikacji, można testować pojemność klastra i zwiększyć rozmiar zgodnie z oczekiwaniami. Można na przykład użyć symulowanego obciążenia lub zapytania w języku *Kanaryjskie*. Dzięki symulowanemu obciążeniu można uruchamiać oczekiwane obciążenia w różnych klastrach rozmiaru, stopniowo zwiększając rozmiar do momentu osiągnięcia żądanej wydajności. Zapytanie typu Kanaryjskie można wstawiać okresowo między innymi zapytaniami produkcyjnymi, aby pokazać, czy klaster ma wystarczającą ilość zasobów.

Rozmiar i typ maszyny wirtualnej są określane przez procesor CPU, rozmiar pamięci RAM i opóźnienie sieci:

* CPU: Rozmiar maszyny wirtualnej określa liczbę rdzeni. Im więcej rdzeni, tym większy stopień równoległych obliczeń każdego węzła. Ponadto niektóre typy maszyn wirtualnych mają szybsze rdzenie.

* Pamięć RAM: Rozmiar maszyny wirtualnej określa również ilość pamięci RAM dostępnej w maszynie wirtualnej. W przypadku obciążeń, które przechowują dane w pamięci do przetworzenia, zamiast odczytywania z dysku, upewnij się, że węzły procesu roboczego mają wystarczającą ilość pamięci, aby dopasować dane.

* NFS W przypadku większości typów klastrów dane przetwarzane przez klaster nie są przechowywane na dysku lokalnym, ale raczej w zewnętrznej usłudze magazynu, takiej jak Data Lake Storage lub Azure Storage. Należy wziąć pod uwagę przepustowość sieci i przepływność między węzłową maszyną wirtualną a usługą magazynu. Przepustowość sieci dostępna dla maszyny wirtualnej zwykle wzrasta o większych rozmiarach. Aby uzyskać szczegółowe informacje, zobacz [Omówienie rozmiarów maszyn wirtualnych](https://docs.microsoft.com/azure/virtual-machines/linux/sizes).

## <a name="choose-the-cluster-scale"></a>Wybierz skalę klastra

Skalowanie klastra zależy od liczby węzłów maszyn wirtualnych. Dla wszystkich typów klastrów istnieją typy węzłów, które mają określoną skalę i typy węzłów obsługujące skalowanie w poziomie. Na przykład klaster może wymagać dokładnie trzech [Apache ZooKeeper](https://zookeeper.apache.org/) węzłów lub dwóch węzłów głównych. Węzły procesu roboczego, które przetwarzają przetwarzanie danych w sposób rozproszony, mogą korzystać z skalowania w górę, dodając kolejne węzły procesu roboczego.

W zależności od typu klastra zwiększenie liczby węzłów procesu roboczego dodaje dodatkową pojemność obliczeniową (na przykład więcej rdzeni), ale może również dodać do łącznej ilości pamięci wymaganej do obsługi magazynu danych przez cały klaster. Podobnie jak w przypadku wybrania rozmiaru i typu maszyny wirtualnej, wybranie odpowiedniej skali klastra jest zwykle osiągane z użyciem symulowanych obciążeń lub zapytań Kanaryjskich.

Klaster można skalować w poziomie, aby spełniał wymagania dotyczące obciążenia szczytowego, a następnie skalować go z powrotem, gdy te dodatkowe węzły nie będą już potrzebne. [Funkcja automatycznego skalowania](hdinsight-autoscale-clusters.md) umożliwia automatyczne skalowanie klastra na podstawie wstępnie określonych metryk i chronometrażu. Aby uzyskać więcej informacji na temat ręcznego skalowania klastrów, zobacz [skalowanie klastrów usługi HDInsight](hdinsight-scaling-best-practices.md).

### <a name="cluster-lifecycle"></a>Cykl życia klastra

Opłata jest naliczana za okres istnienia klastra. W przypadku wystąpienia tylko określonych przypadków, w których klaster jest potrzebny, można [utworzyć klastry na żądanie przy użyciu Azure Data Factory](hdinsight-hadoop-create-linux-clusters-adf.md). Możesz również utworzyć skrypty programu PowerShell, które inicjują i usuwają klaster, a następnie Zaplanuj te skrypty za pomocą [Azure Automation](https://azure.microsoft.com/services/automation/).

> [!NOTE]  
> Po usunięciu klastra zostanie również usunięty jego domyślny magazyn metadanych Hive. Aby zachować magazyn metadanych dla następnego ponownego tworzenia klastra, Użyj zewnętrznego magazynu danych, takiego jak Azure Database lub [Apache Oozie](https://oozie.apache.org/).
<!-- see [Using external metadata stores](hdinsight-using-external-metadata-stores.md). -->

### <a name="isolate-cluster-job-errors"></a>Izolowanie błędów zadań klastra

Czasami mogą wystąpić błędy spowodowane równoległym wykonywaniem wielu map i zmniejszeniem składników w klastrze z wieloma węzłami. Aby ułatwić wyizolowanie problemu, spróbuj przeprowadzić przeprowadzenie testów rozproszonych, uruchamiając współbieżne wiele zadań w klastrze węzłów procesu roboczego, a następnie rozwiń to podejście, aby jednocześnie uruchamiać wiele zadań w klastrach zawierających więcej niż jeden węzeł. Aby utworzyć klaster HDInsight z jednym węzłem na platformie Azure, użyj opcji *niestandardowe (rozmiar, ustawienia, aplikacje)* i użyj wartości 1 dla *liczby węzłów procesu roboczego* w sekcji **rozmiar klastra** podczas aprowizacji nowego klastra w portalu.


## <a name="quotas"></a>Przydziały

Po określeniu rozmiaru, skali i typu docelowej maszyny wirtualnej klastra sprawdź bieżące limity pojemności przydziału dla subskrypcji. Po osiągnięciu limitu przydziału może nie być możliwe wdrożenie nowych klastrów lub skalowanie istniejących klastrów przez dodanie więcej węzłów procesu roboczego. Jedynym limitem przydziału jest przydział rdzeni procesora CPU, który istnieje na poziomie regionu dla każdej subskrypcji. Na przykład subskrypcja może mieć limit 30 rdzeni w regionie Wschodnie stany USA. Jeśli musisz zażądać zwiększenia limitu przydziału, wykonaj następujące czynności:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
1. Wybierz pozycję **Pomoc i obsługa techniczna** w lewej dolnej części strony.
1. Wybierz pozycję **nowe żądanie obsługi**.
1. Na stronie **nowe żądanie obsługi** w obszarze **podstawowe** karty wybierz następujące opcje:
   - **Typ problemu**: **Limity usług i subskrypcji (przydziały)**
   - **Subskrypcja**: subskrypcja, którą chcesz zmodyfikować
   - **Typ limitu przydziału**: **HDInsight**
    
     ![Utwórz żądanie obsługi, aby zwiększyć limit przydziału rdzeni usługi HDInsight](./media/hdinsight-capacity-planning/hdinsight-quota-support-request.png)

1. Wybierz opcję **Dalej: Rozwiązania > >** .
1. Na stronie **szczegóły** wprowadź opis problemu, wybierz ważność problemu, preferowaną metodę kontaktu i inne wymagane pola.
1. Wybierz opcję **Dalej: Przejrzyj i Utwórz >** >.
1. Na karcie **Recenzja i tworzenie** wybierz pozycję **Utwórz**.

> [!NOTE]  
> Jeśli potrzebujesz zwiększyć przydział rdzenia usługi HDInsight w regionie prywatnym, [Prześlij żądanie dozwolonych](https://aka.ms/canaryintwhitelist).

Możesz [skontaktować się z pomocą techniczną, aby zażądać zwiększenia limitu przydziału](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request).

Istnieją jednak pewne limity przydziałów, na przykład jedna subskrypcja platformy Azure może mieć co najwyżej 10 000 rdzeni. Aby uzyskać szczegółowe informacje dotyczące tych limitów, zobacz [limity subskrypcji i usług platformy Azure, przydziały i ograniczenia](https://docs.microsoft.com/azure/azure-subscription-service-limits).

## <a name="next-steps"></a>Następne kroki

* [Skonfiguruj klastry w usłudze HDInsight przy użyciu Apache Hadoop, Spark, Kafka i innych](hdinsight-hadoop-provision-linux-clusters.md): Dowiedz się, jak skonfigurować i skonfigurować klastry w usłudze HDInsight przy użyciu usług Apache Hadoop, Spark, Kafka, Interactive Hive, HBase, ML Services lub burzy.
* [Monitorowanie wydajności klastra](hdinsight-key-scenarios-to-monitor.md): Informacje na temat kluczowych scenariuszy monitorowania klastra usługi HDInsight, które mogą mieć wpływ na wydajność klastra.
