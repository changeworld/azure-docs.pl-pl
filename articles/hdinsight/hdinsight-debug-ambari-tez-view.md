---
title: Użyj widoku Tez Ambari z usługą HDInsight - Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skorzystać z widoku Ambari Tez do debugowania aplikacji Tez zadania w usłudze HDInsight.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 9c39ea56-670b-4699-aba0-0f64c261e411
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 02/27/2018
ms.author: larryfr
ms.openlocfilehash: 98874377f31a435e7dd9736410c123ef623928d0
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
ms.locfileid: "31401594"
---
# <a name="use-ambari-views-to-debug-tez-jobs-on-hdinsight"></a>Debugowanie aplikacji Tez zadania w usłudze HDInsight przy użyciu widoków Ambari

Interfejs użytkownika sieci Web Ambari HDInsight zawiera widok Tez, który może służyć do zrozumienia i debugowanie zadań, które używają aplikacji Tez. Widok Tez umożliwia wizualizacji zadaniem w postaci wykresu połączonych elementów, przejdź do każdego elementu i pobrać statystyk i informacje o rejestrowaniu.

> [!IMPORTANT]
> Kroki opisane w tym dokumencie wymagają klastra usługi HDInsight, który używa systemu Linux. Linux jest jedynym systemem operacyjnym używanym w połączeniu z usługą HDInsight w wersji 3.4 lub nowszą. Aby uzyskać więcej informacji, zobacz [przechowywanie wersji składnika usługi HDInsight](hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a name="prerequisites"></a>Wymagania wstępne

* Klaster usługi HDInsight opartej na systemie Linux. Aby uzyskać instrukcje dotyczące tworzenia klastra, zobacz [rozpocząć korzystanie z usługi HDInsight opartej na systemie Linux](hadoop/apache-hadoop-linux-tutorial-get-started.md).
* Nowoczesna przeglądarka sieci Web, obsługująca język HTML5.

## <a name="understanding-tez"></a>Opis aplikacji Tez

Tez jest rozszerzalną strukturą do przetwarzania danych w Hadoop, która zapewnia większe szybkości niż tradycyjne przetwarzania MapReduce. W przypadku klastrów usługi HDInsight opartej na systemie Linux jest domyślny aparat gałęzi.

Tez tworzy skierowane acykliczne wykresu (DAG) opisujący kolejność akcji wymaganych przez zadania. Poszczególne działania są nazywane wierzchołki i wykonaj część ogólnej zadania. Rzeczywiste wykonywania pracy opisanego przez wierzchołek nosi nazwę zadania i mogą być rozproszone na wielu węzłach w klastrze.

### <a name="understanding-the-tez-view"></a>Opis widoku aplikacji Tez

Widok Tez zawiera zarówno informacje historyczne i po procesów, które są uruchomione. Te informacje pokazują, jak zadanie jest dystrybuowana do klastrów. Wyświetla również liczniki używane przez zadania i wierzchołki i powiązany z zadaniem informacje o błędzie. Może on oferować informacje przydatne w następujących scenariuszach:

* Monitorowanie długotrwałe przetwarza, wyświetlanie postęp mapowania i zmniejszyć zadania.
* Analizowanie danych historycznych dla procesów powiodły się czy nie dowiedzieć się, jak można poprawić przetwarzania lub przyczyny niepowodzenia.

## <a name="generate-a-dag"></a>Generowanie DAG

Widok Tez zawiera dane tylko jeśli używa aparatu Tez jest obecnie uruchomiony lub został wcześniej uruchomione zadanie. Proste zapytań programu Hive można rozpoznawać bez korzystania z aplikacji Tez. Bardziej złożone kwerendy tego czy filtrowanie, grupowanie, kolejność, sprzężenia, itp. Użyj aparatu Tez.

Uruchamianie zapytań programu Hive, która używa Tez, wykonaj następujące kroki:

1. W przeglądarce sieci web, przejdź do https://CLUSTERNAME.azurehdinsight.net, gdzie **CLUSTERNAME** jest nazwą klastra usługi HDInsight.

2. Wybierz z menu w górnej części strony **widoków** ikony. Ta ikona wygląda jak seria kwadratów. W wyświetlonym menu rozwijanego wybierz **Hive widoku**.

    ![Wybranie widoku Hive](./media/hdinsight-debug-ambari-tez-view/selecthive.png)

3. Załadowanie widoku Hive, wklej poniższe zapytanie w edytorze zapytań, a następnie kliknij przycisk **wykonania**.

        select market, state, country from hivesampletable where deviceplatform='Android' group by market, country, state;

    Po ukończeniu zadania powinien zostać wyświetlony wyświetlany w danych wyjściowych **wyniki przetwarzania zapytania** sekcji. Wyniki powinny być podobne do następującego tekstu:

        market  state       country
        en-GB   Hessen      Germany
        en-GB   Kingston    Jamaica

4. Wybierz **dziennika** kartę. Zostaną wyświetlone informacje podobne do następującego tekstu:

        INFO : Session is already open
        INFO :

        INFO : Status: Running (Executing on YARN cluster with App id application_1454546500517_0063)

    Zapisz **identyfikator aplikacji** wartość, jak ta wartość jest używana w następnej sekcji.

## <a name="use-the-tez-view"></a>Użyj widoku aplikacji Tez

1. Wybierz z menu w górnej części strony **widoków** ikony. W wyświetlonym menu rozwijanego wybierz **widoku Tez**.

    ![Wybranie widoku aplikacji Tez](./media/hdinsight-debug-ambari-tez-view/selecttez.png)

2. Po załadowaniu widoku aplikacji Tez, można znaleźć listę zapytań hive, które są aktualnie uruchomione lub były uruchomione w klastrze.

    ![Wszystkie grupy DAG](./media/hdinsight-debug-ambari-tez-view/tez-view-home.png)

3. Jeśli masz tylko jeden wpis jest zapytania uruchomionego w poprzedniej sekcji. Jeśli masz wiele wpisów można wyszukiwać za pomocą pola w górnej części strony.

4. Wybierz **identyfikator zapytania** dla zapytania programu Hive. Zostaną wyświetlone informacje o kwerendzie.

    ![Szczegóły grupy DAG.](./media/hdinsight-debug-ambari-tez-view/query-details.png)

5. Karty na tej stronie umożliwiają wyświetlić następujące informacje:

    * **Zapytanie szczegóły**: szczegóły dotyczące zapytania Hive.
    * **Oś czasu**: dowiedzieć się, jak długo trwało każdego etapu przetwarzania.
    * **Konfiguracje**: Konfiguracja użyta dla tego zapytania.

    Z __szczegóły kwerendy__ można użyć linków, aby uzyskać informacje na temat __aplikacji__ lub __DAG__ dla tego zapytania.
    
    * __Aplikacji__ link Wyświetla informacje o aplikacji YARN dla tego zapytania. W tym miejscu można uzyskać dostępu do dzienników YARN aplikacji.
    * __DAG__ łącze Wyświetla informacje o ukierunkowanego wykresu acyklicznego. dla tego zapytania. W tym miejscu można wyświetlić graficzną reprezentację DAG. Można również znaleźć informacji o wierzchołków w obrębie grupy DAG.

## <a name="next-steps"></a>Następne kroki

Teraz, kiedy znasz sposobu korzystania z widoku aplikacji Tez, Dowiedz się więcej o [przy użyciu Hive w usłudze HDInsight](hadoop/hdinsight-use-hive.md).

Aby uzyskać szczegółowe informacje techniczne na temat aplikacji Tez, zobacz [Tez strony o Hortonworks](http://hortonworks.com/hadoop/tez/).

Aby uzyskać więcej informacji na temat używania narzędzia Ambari z usługą HDInsight, zobacz [Zarządzanie klastrami usługi HDInsight przy użyciu interfejsu użytkownika sieci Web Ambari](hdinsight-hadoop-manage-ambari.md)
