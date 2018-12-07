---
title: Apache Ambari Tez widok za pomocą HDInsight — Azure
description: Dowiedz się, jak debugowanie zadań tez przy na HDInsight przy użyciu widoku Apache Ambari Tez.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/27/2018
ms.author: hrasheed
ms.openlocfilehash: 312b476f8809d1d6375cc20035901d8d11c32173
ms.sourcegitcommit: 698ba3e88adc357b8bd6178a7b2b1121cb8da797
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/07/2018
ms.locfileid: "53012355"
---
# <a name="use-apache-ambari-views-to-debug-apache-tez-jobs-on-hdinsight"></a>Debugowanie zadań tez przy Apache na HDInsight przy użyciu widoków Ambari Apache

[Apache Ambari](https://ambari.apache.org/) zawiera interfejs użytkownika sieci Web dla HDInsight [Apache TEZ](https://tez.apache.org/) widoku, który może służyć do zrozumienia i debugowanie zadań, które używają aplikacji Tez. Widok aplikacji Tez umożliwia wizualizowanie zadania jako wykres połączonych elementów, przejść do każdego elementu i pobierania statystyk i rejestrowania informacji.

> [!IMPORTANT]
> Procedura przedstawiona w tym dokumencie wymaga klastra usługi HDInsight używającego systemu Linux. Linux jest jedynym systemem operacyjnym używanym w połączeniu z usługą HDInsight w wersji 3.4 lub nowszą. Aby uzyskać więcej informacji, zobacz [przechowywanie wersji składnika HDInsight](hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a name="prerequisites"></a>Wymagania wstępne

* Klaster HDInsight opartych na systemie Linux. Aby uzyskać instrukcje dotyczące tworzenia klastra, zobacz [rozpoczęcie korzystania z HDInsight opartych na systemie Linux](hadoop/apache-hadoop-linux-tutorial-get-started.md).
* Nowoczesna przeglądarka sieci Web, obsługująca język HTML5.

## <a name="understanding-apache-tez"></a>Opis Apache Tez

Tez jest rozszerzalna struktura do przetwarzania danych na platformie Apache Hadoop, który zapewnia większe szybkości niż tradycyjne przetwarzania MapReduce. W przypadku klastrów HDInsight opartych na systemie Linux jest domyślny aparat programu Hive.

Tez tworzy skierowany acykliczne wykresu (DAG), wykonywania działań Opisuje kolejność akcji wymaganych przez zadania. Poszczególne działania są nazywane wierzchołków i wykonaj część ogólnej zadania. Rzeczywiste wykonanie pracy opisanego przez wierzchołek nosi nazwę zadania i mogą być rozproszone na wielu węzłach w klastrze.

### <a name="understanding-the-tez-view"></a>Opis widoku aplikacji Tez

Widok Tez zawiera zarówno informacje historyczne i procesów, które są uruchomione. Te informacje pokazują, jak zadanie jest rozłożona na klastrach. Wyświetla również liczniki używane przez zadania oraz wierzchołków i informacje o błędzie powiązanych z zadaniem. Może on oferować przydatne informacje w następujących scenariuszach:

* Monitorowanie długo działających procesów i wyświetlania postępu mapy i zmniejszyć zadania.
* Analizowanie danych historycznych dla procesów powodzeniem lub niepowodzeniem dowiedzieć się, jak można poprawić przetwarzania lub przyczyny niepowodzenia.

## <a name="generate-a-dag"></a>Generowanie grafu DAG

Widok aplikacji Tez zawiera dane tylko, jeśli zadanie, które używa aparatu Tez jest obecnie uruchomiony lub został były uruchomione poprzednio. Prostych zapytań programu Hive można rozpoznawać bez korzystania z aplikacji Tez. Bardziej złożone wysyła zapytanie do tego czy filtrowania, grupowania, porządkowanie, sprzężeń itp. Użyj aparatu Tez.

Umożliwia uruchomienie zapytania programu Hive, który używa Tez następujące czynności:

1. W przeglądarce internetowej przejdź do https://CLUSTERNAME.azurehdinsight.net, gdzie **CLUSTERNAME** jest nazwą klastra usługi HDInsight.

2. W menu w górnej części strony wybierz **widoków** ikony. Ta ikona wygląda jak seria kwadratów. W wyświetlonym menu rozwijanym wybierz **programu Hive widoku**.

    ![Wybieranie widoku Hive](./media/hdinsight-debug-ambari-tez-view/selecthive.png)

3. Załadowanie widoku Hive, wklej poniższe zapytanie w edytorze zapytań, a następnie kliknij **wykonania**.

        select market, state, country from hivesampletable where deviceplatform='Android' group by market, country, state;

    Po zakończeniu zadania, dane wyjściowe powinny być wyświetlane w **wyniki przetwarzania zapytania** sekcji. Wyniki powinny wyglądać podobnie do następującego tekstu:

        market  state       country
        en-GB   Hessen      Germany
        en-GB   Kingston    Jamaica

4. Wybierz **dziennika** kartę. Zostaną wyświetlone informacje podobne do następującego tekstu:

        INFO : Session is already open
        INFO :

        INFO : Status: Running (Executing on YARN cluster with App id application_1454546500517_0063)

    Zapisz **identyfikatora aplikacji** wartość, ta wartość jest używana w następnej sekcji.

## <a name="use-the-tez-view"></a>Użyj widoku aplikacji Tez

1. W menu w górnej części strony wybierz **widoków** ikony. W wyświetlonym menu rozwijanym wybierz **widoku Tez**.

    ![Wybieranie aplikacji Tez widoku](./media/hdinsight-debug-ambari-tez-view/selecttez.png)

2. Po załadowaniu widoku aplikacji Tez, zobaczysz listę zapytań programu hive, które są aktualnie uruchomione lub zostały uruchomione w klastrze.

    ![Wszystkie grupy DAG](./media/hdinsight-debug-ambari-tez-view/tez-view-home.png)

3. Jeśli masz tylko jeden wpis, jest zapytanie, które uruchomiono w poprzedniej sekcji. Jeśli masz wiele wpisów, możesz wyszukiwać według przy użyciu pól w górnej części strony.

4. Wybierz **identyfikator zapytania** dla zapytania programu Hive. Zostaną wyświetlone informacje o kwerendzie.

    ![Szczegóły grupy DAG](./media/hdinsight-debug-ambari-tez-view/query-details.png)

5. Karty na tej stronie umożliwiają wyświetlanie następujące informacje:

    * **Szczegóły zapytań**: szczegóły dotyczące zapytania programu Hive.
    * **Oś czasu**: informacje o czas trwania każdego etapu przetwarzania.
    * **Konfiguracje**: Konfiguracja użyta dla tego zapytania.

    Z __szczegóły kwerendy__ łącza można użyć, aby uzyskać informacje na temat __aplikacji__ lub __DAG__ dla tego zapytania.
    
    * __Aplikacji__ link Wyświetla informacje dotyczące aplikacji usługi YARN dla tego zapytania. W tym miejscu możesz uzyskać dostęp, dzienniki aplikacji usługi YARN.
    * __DAG__ link Wyświetla informacje o skierowanym grafie acyklicznym dla tego zapytania. W tym miejscu możesz wyświetlić graficzną reprezentację grafu DAG. Można również znaleźć informacji na temat wierzchołków w obrębie grafu DAG.

## <a name="next-steps"></a>Następne kroki

Teraz, gdy wiesz jak przy użyciu widoku Apache Tez, Dowiedz się więcej o [przy użyciu Apache Hive on HDInsight](hadoop/hdinsight-use-hive.md).

Aby uzyskać szczegółowe informacje techniczne na temat Apache Tez, zobacz [strony Apache Tez w Hortonworks](https://hortonworks.com/hadoop/tez/).

Aby uzyskać więcej informacji dotyczących korzystania z systemu Apache Ambari z usługą HDInsight, zobacz [HDInsight Zarządzanie klastrami przy użyciu Interfejsu sieci Web Apache Ambari](hdinsight-hadoop-manage-ambari.md)
