---
title: Zainstaluj opublikowana aplikacja — H2O musujących wody - Azure HDInsight | Dokumentacja firmy Microsoft
description: Zainstalować i używać aplikacji Hadoop H2O musujących wody innych firm.
services: hdinsight
documentationcenter: ''
author: ashishthaps
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: ''
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.date: 01/10/2018
ms.author: ashish
ms.openlocfilehash: 9a03588b3327c3ab231f5c2cae17488f4d63bde7
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="install-published-application---h2o-sparkling-water"></a>Instalowanie aplikacji opublikowanych - wody musujących H2O

W tym artykule opisano, jak instalować i uruchamiać [wody musujących H20](http://www.h2o.ai/) opublikowana aplikacja platformy Hadoop w usłudze Azure HDInsight. Przegląd platformy aplikacji usługi HDInsight oraz listę z dostępnych niezależnie od producenta oprogramowania (ISV) opublikowanych aplikacji, zobacz [instalacji aplikacji innych firm Hadoop](hdinsight-apps-install-applications.md). Aby uzyskać instrukcje instalowania własnej aplikacji, zobacz [Install custom HDInsight applications](hdinsight-apps-install-custom-applications.md) (Instalowanie niestandardowych aplikacji usługi HDInsight).

## <a name="about-h2o-sparkling-water"></a>Temat limitu górnego musujących H2O

Wody musujących H2O jest typu open source, platformy learning pełni rozproszonych w pamięci komputera z skalowalność liniowa. Wody musujących H2O umożliwiają łączenie algorytmów H2O z funkcjami programu Spark uczenia maszynowego szybkie i skalowalne. Musujących wody użytkownicy mogą dysków obliczeń od Scala R i Python za pomocą interfejsu użytkownika przepływ H2O.

Udostępnia H2O musujących wody:

* **WebUI łatwy w użyciu i znanych interfejsów** — ustawione w górę, a następnie rozpocznij szybko przy użyciu albo H2O intuicyjne opartych na sieci web przepływu graficznego interfejsu użytkownika lub środowiskach, takich jak R, Python, Java, Scala, JSON i interfejsów API H2O programowania.
* **Obsługa danych pochodzącego od dowolnego wszystkie popularne typy bazy danych i pliku** — łatwo eksplorować i dużych danych z poziomu programu Microsoft Excel, R Studio, Tableau i modelu. Połącz się z danymi z systemu plików HDFS, S3, SQL i NoSQL źródeł danych.
* **Skalowalna na ogromną skalę munging danych Big Data i analiza** — sprzężenia Big H2O można wykonywać 7 x szybsze niż operacje data.table R i liniowo skalować MLD 10 x 10 miliard wierszy sprzężenia.
* **Dane w czasie rzeczywistym oceniania** — szybkie wdrażanie modeli do środowiska produkcyjnego, przy użyciu starego zwykłego Java obiektów (typu POJO), zoptymalizowane pod kątem modelu obiektów języka Java (MOJO) lub interfejsu API REST H2O.

### <a name="resource-links"></a>Linki do zasobów

* [H2O.ai Engineering Roadmap](https://jira.h2o.ai/)
* [Strona główna H2O.AI](http://www.h2o.ai/)
* [Dokumentacja H2O.AI](http://docs.h2o.ai/)
* [Obsługa H2O.AI](https://support.h2o.ai/)
* [Codebase typu Open Source H2O.AI](https://github.com/h2oai/)

## <a name="prerequisites"></a>Wymagania wstępne

Aby zainstalować tę aplikację w nowym klastrze HDInsight lub istniejącego klastra, musi mieć następującą konfigurację:

* Klaster tier(s): Standard lub Premium
* Typ klastra: Spark
* Klaster wersje: w wersji 3.5 lub 3,6

## <a name="install-the-h2o-sparkling-water-published-application"></a>Zainstaluj wody musujących H2O opublikowane aplikacji

Aby uzyskać instrukcje krok po kroku dotyczące instalowania tego i innych aplikacjach dostępnych niezależnego dostawcy oprogramowania, przeczytaj [instalacji aplikacji innych firm Hadoop](hdinsight-apps-install-applications.md).

## <a name="launch-h2o-sparkling-water"></a>Uruchamianie wody musujących H2O

1. Po zakończeniu instalacji, możesz rozpocząć używać H2O musujących wody (h2o sparklingwater) z klastrem w portalu Azure, otwierając notesów Jupyter (`https://<ClusterName>.azurehdinsight.net/jupyter`). Można także uzyskać do Jupyter wybierając **pulpit nawigacyjny klastra** z okienka z klastra, w portalu, wybierając **notesu Jupyter**. Monit o podanie poświadczeń. Wprowadź poświadczenia usługi Hadoop klastra określoną na utworzenie klastra.

2. W oprogramowaniu Jupyter, zobacz trzy foldery: H2O-PySparkling — przykłady, przykłady PySpark i Scala przykłady. Wybierz **H2O-PySparkling — przykłady** folderu.

    ![Macierzysty notesów Jupyter](./media/hdinsight-apps-install-h2o/jupyter-home.png)

3. Pierwszym krokiem podczas tworzenia nowego notesu jest skonfigurowanie środowiska Spark. Te informacje zostaną zawarte w **Sentiment_analysis_with_Sparkling_Water** przykład. Podczas konfigurowania środowiska Spark, należy użyć poprawne jar i podaj adres IP podał dane wyjściowe pierwszej komórki.

    ![Macierzysty notesów Jupyter](./media/hdinsight-apps-install-h2o/spark-config.png)

4. Uruchomienie klastra H2O.

    ![Uruchom klastra](./media/hdinsight-apps-install-h2o/start-cluster.png)

5. Po skonfigurowaniu i uruchomieniu klastra H2O Otwórz H2O przepływu, przechodząc do **`https://<ClusterName>-h2o.apps.azurehdinsight.net:443`**.

    > [!NOTE]
    > Jeśli nie można otworzyć H2O przepływu, spróbuj wyczyścić pamięć podręczną przeglądarki. Jeśli jednak nie można uzyskać do niej dostęp, prawdopodobnie nie masz za mało zasobów w klastrze. Spróbuj zwiększyć liczbę węzłów procesu roboczego w obszarze **klaster w skali** opcji w okienku klastra.

    ![Przepływ H2O pulpitu nawigacyjnego](./media/hdinsight-apps-install-h2o/h2o-flow.png)

6. Wybierz **Million_Songs.flow** przykład z menu po prawej stronie. Gdy zostanie wyświetlony monit z ostrzeżeniem, kliknij przycisk **notesu obciążenia**. Ten pokaz jest przeznaczone do uruchamiania w ciągu kilku minut przy użyciu danych rzeczywistych. Celem jest określenie danych, czy utworu został zwolniony przed lub po 2004 przy użyciu klasyfikacji binarnej.

    ![Wybierz Million_Songs.flow](./media/hdinsight-apps-install-h2o/million-songs.png)

7. Znajdź ścieżkę zawierającą **milsongs-cls-train.csv.gz**i Zastąp całą ścieżkę z **https://h2o-public-test-data.s3.amazonaws.com/bigdata/laptop/milsongs/milsongs-cls-train.csv.gz**.

8. Znajdź ścieżkę zawierającą **milsongs-cls-test.csv.gz** i zastąpić go ciągiem **https://h2o-public-test-data.s3.amazonaws.com/bigdata/laptop/milsongs/milsongs-cls-test.csv.gz**.

9. Aby wykonać wszystkie instrukcje w komórkach notesu, wybierz **Uruchom wszystkie** przycisk na pasku narzędzi.

    ![Uruchom wszystkie](./media/hdinsight-apps-install-h2o/run-all.png)

10. Po kilku minutach powinny pojawić się dane wyjściowe podobne do następującego.

    ![Dane wyjściowe](./media/hdinsight-apps-install-h2o/output.png)

Gotowe. Już wykorzystana analizy sztuczne w łączniku Spark w ciągu kilku minut. Teraz można eksplorować więcej przykładów w H2O przepływ, które wykazują różnego rodzaju algorytmów uczenia maszynowego.

## <a name="next-steps"></a>Kolejne kroki

* [Dokumentacja H2O](http://docs.h2o.ai/h2o/latest-stable/h2o-docs/index.html)
* [Instalowanie niestandardowych aplikacji usługi HDInsight](hdinsight-apps-install-custom-applications.md): Dowiedz się, jak wdrożyć aplikację usługi HDInsight nieopublikowane do usługi HDInsight.
* [Publikowanie aplikacji usługi HDInsight](hdinsight-apps-publish-applications.md): dowiedz się, jak opublikować niestandardowe aplikacje usługi HDInsight w portalu Azure Marketplace.
* [MSDN: instalowanie aplikacji usługi HDInsight](https://msdn.microsoft.com/library/mt706515.aspx): dowiedz się, jak zdefiniować aplikacje usługi HDInsight.
* [Dostosowywanie klastrów usługi HDInsight opartej na systemie Linux przy użyciu akcji skryptu](hdinsight-hadoop-customize-cluster-linux.md): Dowiedz się, jak instalować dodatkowe aplikacje przy użyciu akcji skryptu.
* [Użyj węzłami pusty krawędzi w usłudze HDInsight](hdinsight-apps-use-edge-node.md): Dowiedz się, jak uzyskać dostęp do klastrów usługi HDInsight i do badania i obsługiwania aplikacji usługi HDInsight za pomocą węzła krawędzi puste.
