---
title: 'Szybki Start: Apache Hadoop i Apache Hive w usłudze HDInsight przy użyciu Azure Portal'
description: W tym przewodniku szybki start użyjesz Azure Portal, aby utworzyć klaster usługi HDInsight Hadoop
keywords: Wprowadzenie do usługi Hadoop, Hadoop Linux, Hadoop — Szybki Start, wprowadzenie do usługi Hive, Hive — Szybki Start
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017,mvc,seodec18
ms.topic: quickstart
ms.date: 09/25/2019
ms.openlocfilehash: 5d87cc7fdcd9c8065c2a9886b970b406df0d8fc8
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/30/2019
ms.locfileid: "71677911"
---
# <a name="quickstart-create-apache-hadoop-cluster-in-azure-hdinsight-using-azure-portal"></a>Szybki Start: Tworzenie klastra Apache Hadoop w usłudze Azure HDInsight przy użyciu Azure Portal

W tym artykule dowiesz się, jak tworzyć klastry [Apache Hadoop](https://hadoop.apache.org/) w usłudze HDInsight przy użyciu Azure Portal, a następnie uruchamiać zadania Apache Hive w usłudze HDInsight. Większość zadań Hadoop to zadania wsadowe. Tworzysz klaster, uruchamiasz niektóre zadania, a następnie usuwasz klaster. W tym artykule wykonywane są wszystkie trzy zadania.

W tym przewodniku szybki start utworzysz klaster usługi HDInsight Hadoop przy użyciu Azure Portal. Możesz również utworzyć klaster przy użyciu [szablonu Azure Resource Manager](apache-hadoop-linux-tutorial-get-started.md).

Obecnie Usługa HDInsight zawiera [siedem różnych typów klastrów](../hdinsight-overview.md#cluster-types-in-hdinsight). Każdy typ klastra obsługuje inny zestaw składników. Wszystkie typy klastrów obsługują gałąź Hive. Aby uzyskać listę obsługiwanych składników w usłudze HDInsight, zobacz [co nowego w wersjach klastra Apache Hadoop udostępnianych przez usługi HDInsight?](../hdinsight-component-versioning.md)  

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [Utwórz bezpłatne konto](https://azure.microsoft.com/free/) .

## <a name="create-an-apache-hadoop-cluster"></a>Tworzenie klastra Apache Hadoop

W tej sekcji utworzysz klaster Hadoop w usłudze HDInsight przy użyciu Azure Portal.

1. Zaloguj się do [Azure Portal](https://portal.azure.com).

1. W Azure Portal przejdź do pozycji **Tworzenie zasobu** > **Analiza** > **HDInsight**.

    ![Tworzenie klastra usługi HDInsight zasobów](./media/apache-hadoop-linux-create-cluster-get-started-portal/create-hdinsight-cluster.png "Tworzenie klastra usługi HDInsight")

1. W obszarze **podstawowe**wpisz lub wybierz następujące wartości:

    |Właściwość  |Opis  |
    |---------|---------|
    |Ramach    |  Wybierz swoją subskrypcję platformy Azure. |
    |Grupa zasobów     | Utwórz grupę zasobów lub wybierz istniejącą grupę zasobów.  Grupa zasobów jest kontenerem składników platformy Azure.  W takim przypadku Grupa zasobów zawiera klaster HDInsight i zależne konto usługi Azure Storage. |
    |Nazwa klastra   | Wprowadź nazwę klastra Hadoop. Ponieważ wszystkie klastry w usłudze HDInsight współużytkują tę samą przestrzeń nazw DNS, ta nazwa musi być unikatowa. Nazwa może składać się z maksymalnie 59 znaków, w tym liter, cyfr i łączników. Pierwsze i ostatnie znaki nazwy nie mogą być łącznikami. |
    |Lokalizacja    | Wybierz lokalizację platformy Azure, w której chcesz utworzyć klaster.  Aby uzyskać lepszą wydajność, wybierz lokalizację bliższą. |
    |Typ klastra| Wybierz pozycję **Wybierz typ klastra**. Następnie wybierz pozycję **Hadoop** jako typ klastra.|
    |Wersja|Zostanie określona wersja domyślna dla typu klastra. Wybierz z listy rozwijanej, jeśli chcesz określić inną wersję.|
    |Nazwa użytkownika i hasło logowania klastra    | Domyślna nazwa logowania to **admin**. Hasło musi składać się z co najmniej 10 znaków i musi zawierać co najmniej jedną cyfrę, jedną wielką i jedną małą literę, jeden znak inny niż alfanumeryczny (z wyjątkiem znaków ' "' \). Upewnij się, że **nie są** używane typowe hasła, takie jak "Pass@word1".|
    |Nazwa użytkownika Secure Shell (SSH) | Domyślna nazwa użytkownika to **sshuser**.  Dla nazwy użytkownika SSH można podać inną nazwę. |
    |Użyj hasła logowania do klastra dla protokołu SSH| Zaznacz to pole wyboru, aby użyć tego samego hasła dla użytkownika SSH, który został podany dla użytkownika logowania klastra.|

    Wprowadzenie do usługi ![HDInsight Linux udostępnianie podstawowych wartości klastra](./media/apache-hadoop-linux-create-cluster-get-started-portal/azure-portal-cluster-basics-blank.png "zapewnia podstawowe wartości na potrzeby tworzenia klastra usługi HDInsight")

    Wybierz pozycję **Dalej: > magazynu >** , aby przejść do ustawień magazynu.

1. Na karcie **Magazyn** podaj następujące wartości:

    |Właściwość  |Opis  |
    |---------|---------|
    |Podstawowy typ magazynu|Użyj wartości domyślnej **usługi Azure Storage**.|
    |Metoda wyboru|Użyj wartości domyślnej **Wybierz z listy**.|
    |Podstawowe konto magazynu|Użyj listy rozwijanej, aby wybrać istniejące konto magazynu, lub wybierz pozycję **Utwórz nowe**. Jeśli utworzysz nowe konto, nazwa musi mieć długość od 3 do 24 znaków i może zawierać tylko cyfry i małe litery|
    |Wbudowane|Użyj autowypełnianej wartości.|

    Wprowadzenie do usługi ![HDInsight Linux zapewnia wartości magazynu klastra](./media/apache-hadoop-linux-create-cluster-get-started-portal/azure-portal-cluster-storage-blank.png "zapewniają wartości magazynu do tworzenia klastra usługi HDInsight")

    Wybierz kartę **Recenzja + tworzenie** .

1. Na karcie **Recenzja + tworzenie** Sprawdź wartości wybrane w poprzednich krokach.

    Wprowadzenie do usługi ![HDInsight Linux podsumowanie klastra]wprowadzenie do usługi HDInsight w systemie(./media/apache-hadoop-linux-create-cluster-get-started-portal/azure-portal-cluster-review-create-hadoop.png "Linux — podsumowanie")

1. Wybierz pozycję **Utwórz**. Utworzenie klastra trwa około 20 minut.

Po utworzeniu klastra zostanie wyświetlona strona przegląd klastra w Azure Portal.

![Wprowadzenie do usługi HDInsight Linux ustawienia klastra](./media/apache-hadoop-linux-create-cluster-get-started-portal/cluster-settings-overview.png "właściwości klastra usługi HDInsight")

Każdy klaster ma [konto usługi Azure Storage](../hdinsight-hadoop-use-blob-storage.md) lub [Azure Data Lake zależności konta](../hdinsight-hadoop-use-data-lake-store.md) . Jest ono określane jako domyślne konto magazynu. Klaster usługi HDInsight i jego domyślne konto magazynu muszą znajdować się w tym samym regionie platformy Azure. Usunięcie klastrów nie powoduje usunięcia konta magazynu.

> [!NOTE]  
> Aby poznać inne metody tworzenia klastrów i zapoznać się z właściwościami używanymi w tym przewodniku Szybki Start, zobacz [Tworzenie klastrów usługi HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).

## <a name="run-apache-hive-queries"></a>Uruchamianie zapytań Apache Hive

[Apache Hive](hdinsight-use-hive.md) jest najpopularniejszym składnikiem używanym w usłudze HDInsight. Istnieje wiele sposobów uruchamiania zadań Hive w usłudze HDInsight. W tym przewodniku szybki start użyjesz widoku programu Hive Ambari z poziomu portalu. Aby poznać inne metody przesyłania zadań Hive, zobacz [Korzystanie z usługi Hive w usłudze HDInsight](hdinsight-use-hive.md).

1. Aby otworzyć Ambari z poprzedniego zrzutu ekranu, wybierz pozycję **pulpit nawigacyjny klastra**.  Możesz również przejść do `https://ClusterName.azurehdinsight.net`, gdzie `ClusterName` to klaster utworzony w poprzedniej sekcji.

    ![Pulpit nawigacyjny wprowadzenie do usługi HDInsight Linux klaster]wprowadzenie do usługi HDInsight(./media/apache-hadoop-linux-create-cluster-get-started-portal/hdinsight-linux-get-started-open-cluster-dashboard.png "Linux wprowadzenie do pulpitu nawigacyjnego klastra")

2. Wprowadź nazwę użytkownika i hasło usługi Hadoop, które zostały określone podczas tworzenia klastra. Domyślna nazwa użytkownika to **admin**.

3. Otwórz **Widok Hive** , jak pokazano na poniższym zrzucie ekranu:

    ![Wybieranie opcji Widok Hive z poziomu menu Ambari]usługi(./media/apache-hadoop-linux-create-cluster-get-started-portal/hdi-select-hive-view.png "HDInsight Hive")

4. Na karcie **zapytanie** Wklej następujące instrukcje HiveQL do arkusza:

    ```sql
    SHOW TABLES;
    ```

    ![Widok Hive usługi HDInsight — Edytor zapytań]usługi(./media/apache-hadoop-linux-create-cluster-get-started-portal/hdi-apache-hive-view1.png "HDInsight widok Hive — Edytor zapytań")

5. Wybierz pozycję **Wykonaj**. Karta **wyniki** pojawia się poniżej karty **zapytanie** i wyświetla informacje o zadaniu. 

    Po zakończeniu zapytania na karcie **zapytanie** są wyświetlane wyniki operacji. Zobaczysz jedną tabelę o nazwie **hivesampletable**. Ta Przykładowa tabela Hive zawiera wszystkie klastry usługi HDInsight.

    Apache Hive usługi HDInsight — ![Wyświetlanie wyników](./media/apache-hadoop-linux-create-cluster-get-started-portal/hdinsight-hive-views.png "Apache Hive widok") usługi HDInsight

6. Powtórz kroki 4 i 5, aby uruchomić następujące zapytanie:

    ```sql
    SELECT * FROM hivesampletable;
    ```

7. Można również zapisać wyniki zapytania. Wybierz przycisk menu po prawej stronie i określ, czy chcesz pobrać wyniki jako plik CSV, czy zapisać je na koncie magazynu skojarzonym z klastrem.

    ![Zapisz wynik zapytania Apache Hive](./media/apache-hadoop-linux-create-cluster-get-started-portal/hdinsight-linux-hive-view-save-results.png "zapisz wynik zapytania Apache Hive")

Po zakończeniu zadania programu Hive można [wyeksportować wyniki do usługi Azure SQL Database lub bazy danych SQL Server Database](apache-hadoop-use-sqoop-mac-linux.md), można również [wizualizować wyniki przy użyciu programu Excel](apache-hadoop-connect-excel-power-query.md). Aby uzyskać więcej informacji o korzystaniu z usługi Hive w usłudze HDInsight, zobacz [używanie Apache Hive i HiveQL z Apache Hadoop w usłudze HDInsight do analizowania przykładowego pliku Apache Log4J](hdinsight-use-hive.md).

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Po zakończeniu przewodnika Szybki Start możesz chcieć usunąć klaster. Dzięki usłudze HDInsight dane są przechowywane w usłudze Azure Storage, dzięki czemu można bezpiecznie usunąć klaster, gdy nie jest używany. Opłaty są naliczone również za klaster usługi HDInsight, nawet wtedy, gdy nie jest on używany. Ze względu na to, że opłaty za klaster są dużo razy większe niż opłaty za magazyn, sprawia to, że należy usunąć klastry, gdy nie są używane.

> [!NOTE]  
> Jeśli masz *natychmiast* do następnego artykułu, aby dowiedzieć się, jak uruchamiać operacje ETL przy użyciu usługi Hadoop w usłudze HDInsight, możesz kontynuować działanie klastra. Jest to spowodowane tym, że w samouczku należy ponownie utworzyć klaster usługi Hadoop. Jeśli jednak nie przejdziesz od razu do następnego artykułu, musisz usunąć klaster teraz.

### <a name="to-delete-the-cluster-andor-the-default-storage-account"></a>Aby usunąć klaster i/lub domyślne konto magazynu

1. Wróć do karty przeglądarki, w której znajduje się Azure Portal. Użytkownik powinien znajdować się na stronie Przegląd klastra. Jeśli chcesz tylko usunąć klaster, ale zachować domyślne konto magazynu, wybierz pozycję **Usuń**.

    ![Usuwanie klastra z usługi Azure HDInsight]Usuń klaster(./media/apache-hadoop-linux-create-cluster-get-started-portal/hdinsight-delete-cluster.png "usługi Azure HDInsight")

2. Jeśli chcesz usunąć klaster, a także domyślne konto magazynu, wybierz nazwę grupy zasobów (wyróżnioną na poprzednim zrzucie ekranu), aby otworzyć stronę Grupa zasobów.

3. Wybierz pozycję **Usuń grupę zasobów** , aby usunąć grupę zasobów zawierającą klaster i domyślne konto magazynu. Uwaga Usunięcie grupy zasobów powoduje usunięcie konta magazynu. Jeśli chcesz zachować konto magazynu, wybierz opcję usunięcia klastra.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start przedstawiono, jak utworzyć klaster usługi HDInsight oparty na systemie Linux przy użyciu szablonu Menedżer zasobów i jak wykonywać podstawowe zapytania programu Hive. W następnym artykule przedstawiono sposób wykonywania operacji wyodrębniania, transformacji i ładowania (ETL) przy użyciu platformy Hadoop w usłudze HDInsight.

> [!div class="nextstepaction"]
> [Wyodrębnianie, przekształcanie i ładowanie danych przy użyciu interakcyjnych zapytań w usłudze HDInsight](../interactive-query/interactive-query-tutorial-analyze-flight-data.md)
