---
title: Użyj interfejsu użytkownika programu Apache Tez z systemem Windows HDInsight — Azure
description: Dowiedz się, jak debugowanie zadań tez przy na podstawie Windows HDInsight HDInsight przy użyciu interfejsu użytkownika programu Apache Tez.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 01/17/2017
ms.author: hrasheed
ROBOTS: NOINDEX
ms.openlocfilehash: ecb613dd2f624b9e0910a0ddcdf931f4b266aa96
ms.sourcegitcommit: f0c2758fb8ccfaba76ce0b17833ca019a8a09d46
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/06/2018
ms.locfileid: "51036587"
---
# <a name="use-the-apache-tez-ui-to-debug-tez-jobs-on-windows-based-hdinsight"></a>Debugowanie zadań tez przy na podstawie Windows HDInsight przy użyciu interfejsu użytkownika Tez Apache
Interfejsu użytkownika Tez może służyć do debugowania zadań Hive, które używają Tez jako aparatu wykonywania. Interfejsu użytkownika Tez wizualizuje zadania wykres połączonych elementów, można przejść do każdego elementu i pobierania statystyk i rejestrowania informacji.

> [!IMPORTANT]
> Kroki opisane w tym dokumencie wymagają klastra usługi HDInsight, który używa Windows. Linux jest jedynym systemem operacyjnym używanym w połączeniu z usługą HDInsight w wersji 3.4 lub nowszą. Aby uzyskać więcej informacji, zobacz sekcję [HDInsight retirement on Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement) (Wycofanie usługi HDInsight w systemie Windows).

## <a name="prerequisites"></a>Wymagania wstępne
* Klaster HDInsight z systemem Windows. Aby uzyskać instrukcje dotyczące tworzenia nowego klastra, zobacz [rozpoczęcie korzystania z systemem Windows HDInsight](hdinsight-hadoop-tutorial-get-started-windows.md).

  > [!IMPORTANT]
  > Interfejsu użytkownika programu Apache Tez jest dostępna tylko w klastrach HDInsight oparte na Windows utworzonych po 8 lutego 2016 r.
  >
  >
* Klient pulpitu zdalnego z systemem Windows.

## <a name="understanding-apache-tez"></a>Opis Apache Tez
Tez jest rozszerzalna struktura do przetwarzania danych na platformie Apache Hadoop i zapewnia większą wyższe szybkości niż tradycyjne przetwarzania MapReduce. Należy włączyć Tez, umieszczając następujący tekst jako część zapytania programu Hive:

    set hive.execution.engine=tez;

Tez tworzy skierowany acykliczne wykresu (DAG), wykonywania działań Opisuje kolejność wykonywania czynności wymagane przez zadanie. Poszczególne działania są nazywane wierzchołków i wykonaj część ogólnej zadania. Rzeczywiste wykonanie pracy opisanego przez wierzchołek nosi nazwę zadania i mogą być rozproszone na wielu węzłach w klastrze.

### <a name="understanding-the-tez-ui"></a>Omówienie interfejsu użytkownika Tez
Interfejsu użytkownika Tez jest strony sieci web udostępnia informacje na temat procesów, które używają aplikacji Tez. Może on oferować przydatne informacje w następujących scenariuszach:

* Monitorowanie długo działających procesów i wyświetlania postępu mapy i zmniejszyć zadania.
* Analizowanie danych historycznych dla procesów powodzeniem lub niepowodzeniem dowiedzieć się, jak można poprawić przetwarzania lub przyczyny niepowodzenia.

## <a name="generate-a-dag"></a>Generowanie grafu DAG
Interfejsu użytkownika Tez zawiera dane, jeśli zadanie, które używa aparatu Tez jest obecnie uruchomiony lub został został uruchomiony w przeszłości. Prostych zapytań programu Hive można rozpoznać zwykle bez korzystania z aplikacji Tez. Bardziej złożone zapytania, które obsługują filtrowania, grupowania, porządkowanie, połączeń itp wymaga Tez.

Wykonaj następujące kroki, aby uruchomić zapytanie programu Hive, który używa aplikacji Tez.

1. W przeglądarce internetowej przejdź do https://CLUSTERNAME.azurehdinsight.net, gdzie **CLUSTERNAME** jest nazwą klastra usługi HDInsight.
2. W menu w górnej części strony wybierz **Edytor Hive**. Spowoduje to wyświetlenie strony za pomocą Poniższe przykładowe zapytanie.

        Select * from hivesampletable

    Wymaż przykładowe zapytanie i zastąp go poniżej.

        set hive.execution.engine=tez;
        select market, state, country from hivesampletable where deviceplatform='Android' group by market, country, state;
3. Wybierz **przesyłania** przycisku. **Sesji zadania** sekcji w dolnej części strony wyświetli stan zapytania. Gdy stan zmieni się na **Ukończono**, wybierz opcję **Wyświetl szczegóły** link, aby wyświetlić wyniki. **Dane wyjściowe zadania** powinien być podobny do następującego:

        en-GB   Hessen      Germany
        en-GB   Kingston    Jamaica
        en-GB   Nairobi Area    Kenya

## <a name="use-the-tez-ui"></a>Korzystanie z interfejsu użytkownika Tez
> [!NOTE]
> Interfejsu użytkownika Tez jest dostępny na pulpicie głównymi węzłami klastra, tylko w przypadku, więc musisz połączyć się z węzłami głównymi, używając pulpitu zdalnego.
>
>

1. Z [witryny Azure portal](https://portal.azure.com), wybierz klaster usługi HDInsight. W górnej części bloku HDInsight, wybierz **pulpitu zdalnego** ikony. Link ten będzie wyświetlać blok usług pulpitu zdalnego

    ![Ikony pulpitu zdalnego](./media/hdinsight-debug-tez-ui/remotedesktopicon.png)
2. W bloku pulpitu zdalnego, wybierz **Connect** nawiązać połączenia z głównym węzłem klastra. Po wyświetleniu monitu użyj nazwy użytkownika pulpitu zdalnego klastra i hasło do uwierzytelniania połączenia.

    ![Ikona połączenia pulpitu zdalnego](./media/hdinsight-debug-tez-ui/remotedesktopconnect.png)

   > [!NOTE]
   > Jeśli nie włączono połączenia pulpitu zdalnego, podaj nazwę użytkownika, hasło i datę wygaśnięcia, a następnie wybierz **Włącz** można włączyć pulpitu zdalnego. Po jej włączeniu, skorzystaj z poprzednich kroków, aby połączyć.
   >
   >
3. Po nawiązaniu połączenia, Otwórz program Internet Explorer na pulpicie zdalnym, wybierz ikonę koła zębatego w prawym górnym rogu przeglądarki, a następnie wybierz **ustawienia widoku zgodności**.
4. W dolnej części **ustawienia widoku zgodności**, usuń zaznaczenie pola wyboru dla **wyświetlania witryny intranetowe w widoku zgodności** i **Microsoft użycie listy zgodności**, i następnie wybierz pozycję **Zamknij**.
5. W programie Internet Explorer przejdź do http://headnodehost:8188/tezui/#/. Spowoduje to wyświetlenie interfejsu użytkownika Tez

    ![Interfejsu użytkownika tez](./media/hdinsight-debug-tez-ui/tezui.png)

    Podczas ładowania interfejsu użytkownika Tez, zobaczysz listę grupy DAG, które są aktualnie uruchomione lub zostały uruchomione w klastrze. Widok domyślny zawiera nazwę grupy DAG, identyfikator, osoba przesyłająca, stan, czas rozpoczęcia, czas zakończenia, czas trwania, identyfikator aplikacji i kolejki. Można dodać więcej kolumn, przy użyciu ikony koła zębatego w prawym rogu strony.

    Jeśli masz tylko jeden wpis, jest zapytanie, które uruchomiono w poprzedniej sekcji. Jeśli masz wiele wpisów, możesz wyszukać, wpisując kryteria wyszukiwania w polach powyżej grupy DAG, kliknij przycisk **Enter**.
6. Wybierz **nazwy grupy Dag** uzyskać najnowszy wpis DAG. Link ten będzie wyświetlać informacje o grupę DAG, a także opcję, aby pobrać plik zip pliki w formacie JSON, które zawierają informacje dotyczące grupy DAG.

    ![Szczegóły grupy DAG](./media/hdinsight-debug-tez-ui/dagdetails.png)
7. Powyżej **szczegóły DAG** linki, które mogą służyć do wyświetlania informacji na temat grafu DAG.

   * **Liczniki DAG** Wyświetla informacje dotyczące liczników dla tej grupy DAG.
   * **Widok graficzny** Wyświetla graficzną reprezentację Graf DAG.
   * **Wszystkie wierzchołki** Wyświetla listę wierzchołki w grafie DAG.
   * **Wszystkie zadania** Wyświetla listę zadań dla wszystkich wierzchołków w grafie DAG.
   * **Wszystkie TaskAttempts** przedstawia informacje na temat próby uruchomienia zadań dla tej grupy DAG.

     > [!NOTE]
     > Po przewinięciu wyświetlanie kolumny dla wierzchołków, zadania i TaskAttempts Zauważ, że są linków umożliwiających wyświetlenie **liczniki** i **wyświetlanie lub pobieranie dzienników** dla każdego wiersza.
     >
     >

     Jeśli wystąpił błąd przy użyciu zadań, szczegóły grupy DAG wyświetlenie stanu nie powiodło się wraz z łączami do informacji o zadaniu nie powiodło się. Informacje diagnostyczne są wyświetlane poniżej szczegóły grupy DAG.
8. Wybierz **widok graficzny**. Spowoduje to wyświetlenie graficzną reprezentację grafu DAG. Można umieścić wskaźnik myszy nad każdego wierzchołka widok, aby wyświetlić informacje o nim.

    ![Widok graficzny](./media/hdinsight-debug-tez-ui/dagdiagram.png)
9. Klikając wierzchołek ładuje **szczegóły wierzchołka** dla tego elementu. Kliknij pozycję **1 mapy** wierzchołek, aby wyświetlić szczegóły dla tego elementu. Wybierz **Potwierdź** o potwierdzenie w nawigacji.

    ![Szczegóły wierzchołka](./media/hdinsight-debug-tez-ui/vertexdetails.png)
10. Należy pamiętać, powstał łączy w górnej części strony, które są powiązane z wierzchołków i zadania.

    > [!NOTE]
    > Mogą również pojawić się na tej stronie, przechodząc do **szczegółów grupy DAG**, wybierając opcję **szczegóły wierzchołka**, a następnie wybierając **1 mapy** wierzchołka.
    >
    >

    * **Liczniki wierzchołka** Wyświetla informacje o liczniku dla tego wierzchołka.
    * **Zadania** Wyświetla zadania dla tego wierzchołka.
    * **Zadanie prób** Wyświetla informacje dotyczące prób uruchomienia zadania dla tego wierzchołka.
    * **Źródła & wychwytywanie** wyświetla źródła danych i sink dla tego wierzchołka.

      > [!NOTE]
      > Jak z poprzednim menu, można przewijać Wyświetl kolumny do zadań, zadań prób źródeł i Sinks__ do wyświetlić linki do dodatkowych informacji dla każdego elementu.
      >
      >
11. Wybierz **zadania**, a następnie wybierz element o nazwie **00_000000**. Link ten będzie wyświetlać **szczegóły zadania** dla tego zadania. Na tym ekranie można wyświetlić **liczniki zadań** i **zadań prób**.

    ![Szczegóły zadania](./media/hdinsight-debug-tez-ui/taskdetails.png)

## <a name="next-steps"></a>Następne kroki
Teraz, gdy wiesz jak przy użyciu widoku aplikacji Tez, Dowiedz się więcej o [przy użyciu programu Hive HDInsight](hadoop/hdinsight-use-hive.md).

Aby uzyskać szczegółowe informacje techniczne na temat aplikacji Tez, zobacz [strony aplikacji Tez w Hortonworks](http://hortonworks.com/hadoop/tez/).
