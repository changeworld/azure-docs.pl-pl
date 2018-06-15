---
title: Korzystanie z interfejsu użytkownika aplikacji Tez z usługą HDInsight opartych na systemie Windows - Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak i debugowanie aplikacji Tez zadania w usłudze HDInsight HDInsight dla komputerów z systemem Windows przy użyciu interfejsu użytkownika aplikacji Tez.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: cgronlun
editor: cgronlun
ms.assetid: a55bccb9-7c32-4ff2-b654-213a2354bd5c
ms.service: hdinsight
ms.devlang: na
ms.topic: conceptual
ms.date: 01/17/2017
ms.author: larryfr
ROBOTS: NOINDEX
ms.openlocfilehash: 4201fb76ef9b0e711fd48972db86c356d72e6671
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
ms.locfileid: "31406511"
---
# <a name="use-the-tez-ui-to-debug-tez-jobs-on-windows-based-hdinsight"></a>Debugowanie aplikacji Tez zadania w usłudze HDInsight z systemem Windows przy użyciu interfejsu użytkownika aplikacji Tez
Interfejs użytkownika aplikacji Tez może służyć do debugowania zadań Hive, korzystających z aplikacji Tez jako aparat wykonywania. Interfejs użytkownika aplikacji Tez wizualizuje zadanie wykres połączonych elementów, przejdź do każdego elementu, a pobieranie statystyki i rejestrowania informacji.

> [!IMPORTANT]
> Kroki opisane w tym dokumencie wymagają klastra usługi HDInsight z systemem Windows. Linux jest jedynym systemem operacyjnym używanym w połączeniu z usługą HDInsight w wersji 3.4 lub nowszą. Aby uzyskać więcej informacji, zobacz sekcję [HDInsight retirement on Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement) (Wycofanie usługi HDInsight w systemie Windows).

## <a name="prerequisites"></a>Wymagania wstępne
* Klaster usługi HDInsight opartej na systemie Windows. Aby uzyskać instrukcje dotyczące tworzenia nowego klastra, zobacz [rozpocząć korzystanie z usługi HDInsight opartej na systemie Windows](hdinsight-hadoop-tutorial-get-started-windows.md).

  > [!IMPORTANT]
  > Interfejs użytkownika aplikacji Tez jest dostępna tylko w klastrach HDInsight opartych na systemie Windows utworzone po 8 lutego 2016 r.
  >
  >
* Klient pulpitu zdalnego z systemem Windows.

## <a name="understanding-tez"></a>Opis aplikacji Tez
Tez jest rozszerzalną strukturą do przetwarzania danych w Hadoop i zapewnia większe szybkości niż tradycyjne przetwarzania MapReduce. W tym następujący tekst jako część zapytania programu Hive, można włączyć Tez:

    set hive.execution.engine=tez;

Tez tworzy skierowane acykliczne Graph (DAG) opisujący kolejność wykonywania czynności wymaganych przez zadanie. Poszczególne działania są nazywane wierzchołki i wykonaj część ogólnej zadania. Rzeczywiste wykonywania pracy opisanego przez wierzchołek nosi nazwę zadania i mogą być rozproszone na wielu węzłach w klastrze.

### <a name="understanding-the-tez-ui"></a>Opis interfejsu użytkownika aplikacji Tez
Interfejs użytkownika aplikacji Tez jest strony sieci web zawiera informacje dotyczące procesów, które korzystają z aplikacji Tez. Może on oferować informacje przydatne w następujących scenariuszach:

* Monitorowanie długotrwałe przetwarza, wyświetlanie postęp mapowania i zmniejszyć zadania.
* Analizowanie danych historycznych dla procesów powiodły się czy nie dowiedzieć się, jak można poprawić przetwarzania lub przyczyny niepowodzenia.

## <a name="generate-a-dag"></a>Generowanie DAG
Interfejs użytkownika aplikacji Tez zawiera dane, jeśli zadanie, które używa aparatu Tez jest obecnie uruchomiony lub został były uruchamiane w przeszłości. Proste zapytań programu Hive można rozpoznawać zwykle bez korzystania z aplikacji Tez. Bardziej złożone kwerendy, które filtrowanie, grupowanie, porządkowanie, sprzężeń, itp. wymagają Tez.

Uruchamianie zapytań programu Hive, która używa Tez, wykonaj następujące kroki.

1. W przeglądarce sieci web, przejdź do https://CLUSTERNAME.azurehdinsight.net, gdzie **CLUSTERNAME** jest nazwą klastra usługi HDInsight.
2. Wybierz z menu w górnej części strony **Edytor Hive**. Spowoduje to wyświetlenie strony z następujące przykładowe zapytanie.

        Select * from hivesampletable

    ERASE przykładowe zapytanie i zastąp go poniżej.

        set hive.execution.engine=tez;
        select market, state, country from hivesampletable where deviceplatform='Android' group by market, country, state;
3. Wybierz **przesyłania** przycisku. **Sesji zadania** sekcji w dolnej części strony wyświetlany jest stan zapytania. Gdy stan zmienia się na **Ukończono**, wybierz pozycję **Wyświetl szczegóły** łącze, aby wyświetlić wyniki. **Dane wyjściowe zadania** powinien być podobny do następującego:

        en-GB   Hessen      Germany
        en-GB   Kingston    Jamaica
        en-GB   Nairobi Area    Kenya

## <a name="use-the-tez-ui"></a>Korzystanie z interfejsu użytkownika aplikacji Tez
> [!NOTE]
> Interfejs użytkownika aplikacji Tez jest dostępna wyłącznie z pulpitu głównymi węzłami klastra, trzeba używać pulpitu zdalnego nawiązywania połączenia z węzłów głównych.
>
>

1. Z [portalu Azure](https://portal.azure.com), wybierz z klastrem usługi HDInsight. W górnej części bloku HDInsight, wybierz **pulpitu zdalnego** ikony. Link ten będzie wyświetlać blok pulpitu zdalnego

    ![Ikony pulpitu zdalnego](./media/hdinsight-debug-tez-ui/remotedesktopicon.png)
2. W bloku pulpitu zdalnego, wybierz **Connect** nawiązać połączenia z głównym węzłem klastra. Po wyświetleniu monitu użyj nazwa użytkownika pulpitu zdalnego klastra i hasło do uwierzytelniania połączenia.

    ![Ikona połączenia pulpitu zdalnego](./media/hdinsight-debug-tez-ui/remotedesktopconnect.png)

   > [!NOTE]
   > Jeśli połączenie pulpitu zdalnego nie jest włączone, wprowadź nazwę użytkownika, hasło oraz Data wygaśnięcia, a następnie wybierz **włączyć** można włączyć pulpitu zdalnego. Po jej włączeniu, skorzystaj z poprzednich kroków, aby połączyć.
   >
   >
3. Po nawiązaniu połączenia, Otwórz program Internet Explorer na pulpicie zdalnym, wybierz koło zębate ikonę w prawym górnym rogu przeglądarki, a następnie wybierz **ustawienia widoku zgodności**.
4. W dolnej części **ustawienia widoku zgodności**, usuń zaznaczenie pola wyboru dla **wyświetlić witryn intranetowych w widoku zgodności** i **listy zgodności użycia Microsoft**, i następnie wybierz **Zamknij**.
5. W programie Internet Explorer przejdź do http://headnodehost:8188/tezui/#/. Spowoduje to wyświetlenie interfejsu użytkownika aplikacji Tez

    ![Interfejs użytkownika aplikacji tez](./media/hdinsight-debug-tez-ui/tezui.png)

    Podczas ładowania interfejsu użytkownika aplikacji Tez, zobacz się, że lista grupy DAG, które są aktualnie uruchomione lub zostały uruchomione w klastrze. Domyślny widok zawiera nazwę grupy DAG, identyfikatora, osoba przesyłająca, stan, czas rozpoczęcia, godziny zakończenia, czas trwania, identyfikator aplikacji i kolejki. Można dodać więcej kolumn, za pomocą ikony koło zębate po prawej stronie.

    Jeśli masz tylko jeden wpis jest zapytania uruchomionego w poprzedniej sekcji. Jeśli masz wiele wpisów, możesz wyszukać, wpisując kryteria wyszukiwania w polach powyżej grupy DAG, a następnie kliknij przycisk **Enter**.
6. Wybierz **Nazwa grupy Dag** najnowszych wpisu grupy DAG. Link ten wyświetla informacje o DAG, a także opcję Pobierz zip pliki w formacie JSON, które zawierają informacje dotyczące grupy DAG.

    ![Szczegóły grupy DAG.](./media/hdinsight-debug-tez-ui/dagdetails.png)
7. Powyżej **szczegóły DAG** są kilka łącza, które mogą służyć do wyświetlania informacji dotyczących grupy DAG.

   * **Liczniki DAG** Wyświetla liczniki informacje dla tej grupy DAG.
   * **Widok graficzny** Wyświetla graficzną reprezentację tej grupy DAG.
   * **Wszystkich wierzchołków** Wyświetla listę wierzchołków w tej grupy DAG.
   * **Wszystkie zadania** Wyświetla listę zadań dla wszystkich wierzchołków w tej grupy DAG.
   * **Wszystkie TaskAttempts** Wyświetla informacje o próby uruchomienia zadania dla tej grupy DAG.

     > [!NOTE]
     > Wyświetl kolumny do wierzchołków, zadania i TaskAttempts podczas przewijania, zwróć uwagę, czy łącza, aby wyświetlić **liczniki** i **wyświetlić lub pobrać dzienniki** dla każdego wiersza.
     >
     >

     Jeśli wystąpił błąd z zadaniem, szczegóły DAG wyświetlany stan Niepowodzenie, oraz linki do informacji dotyczących zadań zakończonych niepowodzeniem. Informacje diagnostyczne jest wyświetlony poniżej szczegółów grupy DAG.
8. Wybierz **widoku graficznego**. Spowoduje to wyświetlenie graficzną reprezentację DAG. Można umieścić wskaźnik myszy przez każdy wierzchołek w widoku, aby wyświetlić informacje o nim.

    ![Widok graficzny](./media/hdinsight-debug-tez-ui/dagdiagram.png)
9. Kliknięcie wierzchołek ładuje **szczegóły wierzchołków** dla tego elementu. Polecenie **1 mapy** wierzchołków, aby wyświetlić szczegóły dla tego elementu. Wybierz **Potwierdź** o potwierdzenie w obszarze nawigacji.

    ![Szczegóły wierzchołków](./media/hdinsight-debug-tez-ui/vertexdetails.png)
10. Należy zauważyć, że możesz teraz łączy w górnej części strony, które są związane z wierzchołki i zadania.

    > [!NOTE]
    > Tej strony można również przyjeździe, przechodząc do **szczegółów grupy DAG**, wybierając żądane **szczegóły wierzchołków**, a następnie wybierając **1 mapy** wierzchołka.
    >
    >

    * **Liczniki wierzchołków** Wyświetla informacje o liczniku tym wierzchołka.
    * **Zadania** Wyświetla zadania dla tego wierzchołka.
    * **Zadanie prób** Wyświetla informacje dotyczące prób wykonania zadania dla tego wierzchołka.
    * **Źródłem & wychwytywanie** wyświetla źródła danych i sink tego wierzchołka.

      > [!NOTE]
      > Jako mogą być przewijane Wyświetl kolumny do zadania, prób zadań i źródeł & Sinks__ wyświetlić łącza do dodatkowych informacji dla każdego elementu z poprzedniego menu.
      >
      >
11. Wybierz **zadania**, a następnie wybierz element o nazwie **00_000000**. Link ten będzie wyświetlać **szczegóły zadania** dla tego zadania. Na tym ekranie można wyświetlić **liczniki zadań** i **zadań prób**.

    ![Szczegóły zadania](./media/hdinsight-debug-tez-ui/taskdetails.png)

## <a name="next-steps"></a>Następne kroki
Teraz, kiedy znasz sposobu korzystania z widoku aplikacji Tez, Dowiedz się więcej o [przy użyciu Hive w usłudze HDInsight](hadoop/hdinsight-use-hive.md).

Aby uzyskać szczegółowe informacje techniczne na temat aplikacji Tez, zobacz [Tez strony o Hortonworks](http://hortonworks.com/hadoop/tez/).
