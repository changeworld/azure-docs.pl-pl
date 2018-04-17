---
title: W usłudze Azure HDInsight z planowaniem pojemności klastra | Dokumentacja firmy Microsoft
description: Jak określać klastra usługi HDInsight na pojemność i wydajność.
services: hdinsight
documentationcenter: ''
tags: azure-portal
author: maxluk
manager: jhubbard
editor: cgronlun
ms.assetid: ''
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.date: 09/22/2017
ms.author: maxluk
ms.openlocfilehash: ce8764e79d24d36eb3b162fb5f3f688b3e9fab3f
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="capacity-planning-for-hdinsight-clusters"></a>Planowanie wydajności dla klastrów usługi HDInsight

Przed wdrożeniem klastra usługi HDInsight, planowania pojemności klastra określając odpowiednią wydajność i skalę. Dzięki temu planowania zoptymalizować zarówno użyteczność, jak i koszty. Niektóre decyzje dotyczące pojemności klastra nie można zmienić po wdrożeniu. Zmiana parametrów wydajności klastra można zdemontowane i utworzona ponownie bez utraty danych przechowywanych.

Klucza pytania dotyczące planowania pojemności są:

* W danym regionie geograficznym należy wdrażać klastra?
* Ile miejsca do magazynowania potrzebujesz?
* Typ klastra powinna należy wdrożyć?
* Jakie rozmiar i typ maszyny wirtualnej (VM) powinny używać węzły klastra?
* Liczbę węzłów procesu roboczego powinien mieć w klastrze?

## <a name="choose-an-azure-region"></a>Wybierz region platformy Azure

Region platformy Azure Określa, gdzie fizycznie zainicjowaniu obsługi klastra. Aby zminimalizować opóźnienie odczyty i zapisy, klaster powinny znajdować się w pobliżu danych.

HDInsight są dostępne w wielu regionach platformy Azure. Aby znaleźć najbliższy region, zobacz *HDInsight Linux* wpis w *dane i analiza* w [Azure produktów dostępnych według regionu](https://azure.microsoft.com/regions/services/).

## <a name="choose-storage-location-and-size"></a>Wybierz lokalizację magazynu i rozmiar

### <a name="location-of-default-storage"></a>Lokalizacja domyślna magazynu

Domyślny magazyn, konto magazynu Azure lub usługi Azure Data Lake Store, musi być w tej samej lokalizacji co klaster. Usługa Azure Storage jest dostępna we wszystkich lokalizacjach. Data Lake Store jest dostępny w pewnych regionach — Zobacz bieżącej dostępności usługi Data Lake Store w obszarze *magazynu* w [Azure produktów dostępnych według regionu](https://azure.microsoft.com/regions/services/).

### <a name="location-of-existing-data"></a>Lokalizacja istniejących danych

Jeśli już masz konta magazynu lub Data Lake Store z danymi i chcesz używać tego magazynu jako klastra domyślny magazyn, należy wdrożyć klastra w tej samej lokalizacji.

### <a name="storage-size"></a>Rozmiar magazynu

Po wdrożeniu klastra usługi HDInsight, możesz dołączyć dodatkowe konta usługi Azure Storage lub dostęp do innych magazynów Lake danych. Wszystkie konta magazynu musi znajdować się w tej samej lokalizacji co klaster. Data Lake Store może być w innej lokalizacji, chociaż może to powodować pewne opóźnienie odczytu/zapisu danych.

Magazyn Azure ma kilka [limity pojemności](../azure-subscription-service-limits.md#storage-limits), podczas gdy usługi Data Lake Store jest praktycznie nieograniczona.

Klastra mogą uzyskać dostęp z kombinacją różnych kont magazynu. Typowe przykłady obejmują:

* Gdy ilość danych prawdopodobnie przekracza pojemność kontenera magazynu pojedynczego obiektu blob.
* Gdy szybkość dostępu do kontenera obiektów blob może przekroczyć próg, gdzie występuje ograniczenie.
* Jeśli chcesz udostępnić dane już przekazane do kontenera obiektów blob, dostępne dla klastra.
* Gdy chcesz do izolowania różnych części magazynu ze względów bezpieczeństwa lub ułatwienia administracji.

48-węzła klastra zalecamy 4 do 8 kont magazynu. Mimo że może już być wystarczające całkowita ilość miejsca, każde konto magazynu zapewnia dodatkowe ograniczenie użycia przepustowości sieci dla węzłów obliczeniowych. Jeśli masz wiele kont magazynu, należy użyć nazwy losowe dla każdego konta magazynu, bez prefiksu. Celem losowe nazewnictwa jest zmniejsza ryzyko wąskich gardeł magazynu (ograniczanie) lub tryb typowe błędy wszystkich kont. W celu poprawy wydajności użyj tylko jednego kontenera na koncie magazynu.

## <a name="choose-a-cluster-type"></a>Wybierz typ klastra

Typ klastra określa obciążenia klastra usługi HDInsight, jest skonfigurowana do uruchamiania, takie jak Hadoop, Storm, Kafka lub Spark. Aby uzyskać szczegółowy opis dostępnych typów, zobacz [wprowadzenie do usługi Azure HDInsight](hadoop/apache-hadoop-introduction.md#cluster-types-in-hdinsight). Każdy typ klastra ma topologię wdrażania, która zawiera wymagania dotyczące rozmiaru i liczby węzłów.

## <a name="choose-the-vm-size-and-type"></a>Wybierz rozmiar maszyny Wirtualnej i typ

Każdy typ klastra ma zestaw typy węzłów, a każdy typ węzła ma konkretne opcje ich rozmiar maszyny Wirtualnej i typu.

Aby określić rozmiar klastra optymalne dla aplikacji, możesz testu wydajności pojemności klastra i zwiększyć rozmiar wskazane. Na przykład można użyć symulowane obciążenia, lub *mozgi zapytania*. Z obciążenia symulowanego możesz uruchomić oczekiwanego obciążeń w klastrach inny rozmiar stopniowego zwiększenia rozmiaru, aż do osiągnięcia żądaną wydajność. Mozgi zapytania mogą być wstawiane okresowo między inne zapytania produkcji, aby pokazać, czy klaster ma za mało zasobów.

Rozmiar maszyny Wirtualnej i typ jest określany przez procesor CPU przetwarzania zasilania, rozmiar pamięci RAM i opóźnienie sieci:

* Procesor: Rozmiar maszyny Wirtualnej nakazują liczby rdzeni. Większa liczba rdzeni, wyższy stopień równoległych obliczeń można osiągnąć każdego węzła. Ponadto niektóre typy maszyny Wirtualnej mają szybszy rdzeni.

* Ilość pamięci RAM: Rozmiar maszyny Wirtualnej decyduje również ilość pamięci RAM dostępnej w maszynie Wirtualnej. W przypadku obciążeń, które przechowuje dane w pamięci do przetwarzania, a nie odczytu z dysku, upewnij się, węzłów procesu roboczego ma za mało pamięci do danych.

* Sieć: W przypadku większości typów klastra, danych przetworzonych przez klaster jest nie na dysku lokalnym, ale raczej w usłudze magazynu zewnętrznego, takie jak Data Lake — magazyn lub magazyn Azure. Należy wziąć pod uwagę przepustowość sieci i przepustowość między węzeł maszyny Wirtualnej i usługi magazynu. Przepustowość sieci dostępną dla maszyn wirtualnych zwykle zwiększa o dużym rozmiarze. Aby uzyskać więcej informacji, zobacz [omówienie rozmiary maszyny Wirtualnej](https://docs.microsoft.com/azure/virtual-machines/linux/sizes).

## <a name="choose-the-cluster-scale"></a>Wybór skalowania klastra

Skalowanie klastra jest określany przez ilość jego węzły maszyny Wirtualnej. Dla wszystkich typów klastra są typy węzłów, które mają określoną i typów węzłów, które obsługują skalowalnego w poziomie. Na przykład klastra może wymagać dokładnie trzy węzły dozorcy lub dwa węzły Head. Węzłami procesów roboczych, które wykonują przetwarzania danych w sposób rozproszonej mogą korzystać z skalowania, dodając węzłów procesu roboczego dodatkowe.

W zależności od danego typu klastra zwiększenie liczby węzłów procesu roboczego dodaje dodatkowe wydajność obliczeniową (na przykład większej liczby rdzeni), ale może również dodać do łączną ilość pamięci wymaganej dla całego klastra do obsługi magazynu w pamięci przetwarzanych danych. Podobnie jak w przypadku wyboru rozmiar maszyny Wirtualnej i typ Wybieranie skali prawo klastra zwykle osiągnięciu empirycznie, przy użyciu symulowane obciążeń lub mozgi zapytania.

Możesz skalować w poziomie z klastrem w celu spełnienia wymagań obciążenia szczytowego, a następnie skalować powrotem po tych dodatkowych węzłów nie są już potrzebne. Aby uzyskać więcej informacji, zobacz [klastrów usługi HDInsight skali](hdinsight-scaling-best-practices.md).

### <a name="cluster-lifecycle"></a>Cykl życia klastra

Naliczane są opłaty za klaster okres istnienia. Jeśli są tylko określone godziny należy sieci klastra w górę i działa, możesz [Tworzenie klastrów na żądanie przy użyciu fabryki danych Azure](hdinsight-hadoop-create-linux-clusters-adf.md). Można również tworzyć skrypty programu PowerShell, które udostępniania i usunąć klaster, a następnie zaplanować te skrypty przy użyciu [usługi Automatyzacja Azure](https://azure.microsoft.com/services/automation/).

> [!NOTE]
> Po usunięciu klastra powoduje również usunięcie jego potrzeby magazynu metadanych Hive domyślne. Aby zachować na potrzeby magazynu metadanych dla następnego ponownego tworzenia klastra, należy użyć magazynu zewnętrznego metadanych, takich jak bazy danych platformy Azure lub Oozie.
<!-- see [Using external metadata stores](hdinsight-using-external-metadata-stores.md). -->

### <a name="isolate-cluster-job-errors"></a>Wyizolować problemy z zapisywaniem klastra

Czasami błędów mogą wystąpić z powodu równoległe wykonywanie wielu map i zmniejszyć składników na klastrze z wieloma węzłami. Aby problemu, spróbuj rozproszonej testowania uruchamiając równoczesnych wielu zadań na jednym węźle klastra, rozwiń tej metody, aby uruchomić wielu zadań jednocześnie w klastrach zawierających więcej niż jeden węzeł. Aby utworzyć jednowęzłowy klastra usługi HDInsight na platformie Azure, użyj *zaawansowane* opcji.

Można również zainstalować Środowisko deweloperskie jednowęzłowej na komputerze lokalnym i przetestować rozwiązania. Hortonworks udostępnia środowisko deweloperskie lokalnego jednowęzłowej dla rozwiązań opartych na platformie Hadoop, który jest przydatne w przypadku potwierdzającego koncepcji, początkowego projektowania i testowania. Aby uzyskać więcej informacji, zobacz [piaskownicy Hortonworks](http://hortonworks.com/products/hortonworks-sandbox/).

Aby zidentyfikować problem, na jednym węźle klastra lokalnego możesz ponownie uruchomić zadania zakończone niepowodzeniem oraz dostosować danych wejściowych lub użyć mniejsze zestawy danych. Sposób uruchamiania tych zadań zależy od platformy i typu aplikacji.

## <a name="quotas"></a>Przydziały

Po ustaleniu klaster docelowy rozmiar maszyny Wirtualnej, skalę i typ, sprawdź bieżące limity pojemności przydziału subskrypcji. Po osiągnięciu limitu przydziału nie można wdrożyć nowych klastrów lub skalowanie istniejących klastrów, dodając więcej węzłów procesu roboczego. Osiągnięto limit przydziału najczęściej jest limit przydziału rdzeni Procesora, który istnieje w subskrypcji, regionu i poziomy serii maszyny Wirtualnej. Na przykład subskrypcji może być limit całkowita liczba rdzeni 200, limit 30 core w danym regionie i limit 30 rdzeni od wystąpień maszyn wirtualnych. Możesz [skontaktuj się z pomocą techniczną, aby zażądać zwiększenia limitu przydziału](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request).

Jednak brak niektóre limity przydziału stałej, na przykład jedna subskrypcja platformy Azure może mieć maksymalnie 10 000 rdzeni. Szczegółowe informacje dotyczące tych ograniczeń, zawiera [subskrypcji platformy Azure i usługi limity, przydziały i ograniczenia](https://docs.microsoft.com/azure/azure-subscription-service-limits#limits-and-the-azure-resource-manager).

## <a name="next-steps"></a>Kolejne kroki

* [Ustawianie klastrów w usłudze HDInsight Hadoop, Spark, Kafka i](hdinsight-hadoop-provision-linux-clusters.md): informacje o sposobie instalowania i konfigurowania klastrów w usłudze HDInsight Hadoop, Spark, Kafka, interakcyjne Hive, HBase, R Server lub Storm.
* [Monitorowanie wydajności klastra](hdinsight-key-scenarios-to-monitor.md): więcej informacji na temat kluczowych scenariuszy monitorowania dla klastra usługi HDInsight, co może wpłynąć na wydajność sieci klastra.
