---
title: 'Szybki start: Apache Hadoop, Apache Hive & portalu Usługi Azure HDInsight'
description: W tym przewodniku Szybki start można użyć portalu Azure do utworzenia klastra programu HDInsight Hadoop
keywords: wprowadzenie do usługi hadoop,hadoop linux,hadoop szybki start,wprowadzenie do usługi hive,hive szybki start
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: quickstart
ms.custom: hdinsightactive,hdiseo17may2017,mvc,seodec18
ms.date: 02/24/2020
ms.openlocfilehash: 8939d9b342094342d576c00cf02e622286c8fc0f
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "80130565"
---
# <a name="quickstart-create-apache-hadoop-cluster-in-azure-hdinsight-using-azure-portal"></a>Szybki start: tworzenie klastra Apache Hadoop w usłudze Azure HDInsight przy użyciu witryny Azure portal

W tym artykule dowiesz się, jak tworzyć klastry usługi Apache Hadoop w usłudze HDInsight przy użyciu witryny Azure Portal, a następnie uruchamiać zadania usługi Apache Hive w usłudze HDInsight. Większość zadań usługi Hadoop to zadania wsadowe. Tworzysz klaster, uruchamiasz pewne zadania, a następnie usuwasz klaster. W tym artykule wykonasz wszystkie trzy zadania. Aby uzyskać szczegółowe objaśnienia dostępnych konfiguracji, zobacz [Konfigurowanie klastrów w programie HDInsight](../hdinsight-hadoop-provision-linux-clusters.md). Aby uzyskać więcej informacji dotyczących używania portalu do tworzenia klastrów, zobacz [Tworzenie klastrów w portalu](../hdinsight-hadoop-create-linux-clusters-portal.md).

W tym przewodniku Szybki start użyjesz witryny Azure Portal do utworzenia klastra Hadoop w usłudze HDInsight. Klaster możesz utworzyć również przy użyciu [szablonu usługi Azure Resource Manager](apache-hadoop-linux-tutorial-get-started.md).

Obecnie usługa HDInsight zawiera [siedem różnych typów klastrów.](../hdinsight-overview.md#cluster-types-in-hdinsight) Każdy typ klastra obsługuje inny zestaw składników. Wszystkie typy klastrów obsługują technologię Hive. Aby uzyskać listę składników obsługiwanych w usłudze HDInsight, zobacz artykuł [Nowości w wersjach klastra Hadoop dostarczanych z usługą HDInsight](../hdinsight-component-versioning.md).  

Jeśli nie masz subskrypcji platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed rozpoczęciem.

## <a name="create-an-apache-hadoop-cluster"></a>Tworzenie klastra Apache Hadoop

W tej sekcji utworzysz klaster usługi Hadoop w usłudze HDInsight przy użyciu witryny Azure Portal.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com).

1. Z górnego menu wybierz pozycję **+ Utwórz zasób**.

    ![Tworzenie klastra HDInsight zasobów](./media/apache-hadoop-linux-create-cluster-get-started-portal/azure-portal-create-resource.png "Tworzenie klastra HDInsight zasobów")

1. Wybierz **Analytics** > **Azure HDInsight,** aby przejść do strony **Tworzenie klastra HDInsight.**

1. Na karcie **Podstawy** podaj następujące informacje:

    |Właściwość  |Opis  |
    |---------|---------|
    |Subskrypcja    |  Z listy rozwijanej wybierz subskrypcję platformy Azure, która jest używana dla klastra. |
    |Grupa zasobów     | Z listy rozwijanej wybierz istniejącą grupę zasobów lub wybierz pozycję **Utwórz nowy**.|
    |Nazwa klastra   | Podaj globalnie unikatową nazwę. Nazwa może składać się z maksymalnie 59 znaków, w tym liter, cyfr i łączników. Pierwszy i ostatni znak nazwy nie może być łącznikami. |
    |Region    | Z listy rozwijanej wybierz region, w którym jest tworzony klaster.  Wybierz lokalizację znajdującą się blisko, aby zapewnić lepszą wydajność. |
    |Typ klastra| Wybierz **wybierz wybierz wybierz typ klastra**. Następnie wybierz **Opcję Hadoop** jako typ klastra.|
    |Wersja|Z listy rozwijanej wybierz **wersję**. Użyj wersji domyślnej, jeśli nie wiesz, co wybrać.|
    |Nazwa użytkownika i hasło logowania do klastra    | Domyślną nazwą logowania jest **admin**. Hasło musi mieć co najmniej 10 znaków i musi zawierać co najmniej jedną cyfrę, jedną wielką literę i jedną wielką literę, jedną znak niealtalumeryczny (z wyjątkiem znaków " " \). Upewnij się, że **nie zostało podane** typowe hasło, takie jak „Pass@word1”.|
    |Nazwa użytkownika protokołu SSH (Secure Shell) | Domyślna nazwa użytkownika to **sshuser**.  Możesz podać inną nazwę użytkownika protokołu SSH. |
    |Używanie hasła logowania klastra dla SSH| Zaznacz to pole wyboru, aby używać tego samego hasła dla użytkownika SSH, które zostało podane dla użytkownika logowania do klastra.|

    ![Wprowadzenie systemu HDInsight Linux zapewnia podstawowe wartości klastra](./media/apache-hadoop-linux-create-cluster-get-started-portal/azure-portal-cluster-basics.png "Podaj podstawowe wartości do tworzenia klastra HDInsight")

    Wybierz **przycisk Dalej: >>magazynu,** aby przejść do ustawień magazynu.

1. Na karcie **Magazyn** podaj następujące wartości:

    |Właściwość  |Opis  |
    |---------|---------|
    |Podstawowy typ magazynu|Użyj wartości domyślnej **usługi Azure Storage**.|
    |Metoda wybierania|Użyj wartości domyślnej **Wybierz z listy**.|
    |Konto magazynu podstawowego|Użyj listy rozwijanej, aby wybrać istniejące konto magazynu, lub wybierz pozycję **Utwórz nowe**. Jeśli utworzysz nowe konto, nazwa musi mieć długość od 3 do 24 znaków i może zawierać tylko cyfry i małe litery.|
    |Kontener|Użyj wartości autopopulowanej.|

    ![Wprowadzenie systemu HDInsight Linux zapewnia wartości pamięci masowej klastra](./media/apache-hadoop-linux-create-cluster-get-started-portal/azure-portal-cluster-storage.png "Podaj wartości pamięci masowej do tworzenia klastra HDInsight")

    Każdy klaster zależy od [konta usługi Azure Storage](../hdinsight-hadoop-use-blob-storage.md) lub od [konta usługi Azure Data Lake](../hdinsight-hadoop-use-data-lake-store.md). Jest on określany jako domyślne konto magazynu. Klaster USŁUGI HDInsight i jego domyślne konto magazynu muszą być współlokowane w tym samym regionie platformy Azure. Usunięcie klastrów nie powoduje usunięcia konta magazynu.

    Wybierz kartę **Recenzja + utwórz.**

1. Na karcie **Recenzja + utwórz** sprawdź wartości wybrane we wcześniejszych krokach.

    ![Podsumowanie klastra w systemie HDInsight Linux](./media/apache-hadoop-linux-create-cluster-get-started-portal/azure-portal-cluster-review-create-hadoop.png "Podsumowanie klastra w systemie HDInsight Linux")

1. Wybierz **pozycję Utwórz**. Utworzenie klastra trwa około 20 minut.

    Po utworzeniu klastra w witrynie Azure Portal zostanie wyświetlona strona przeglądu klastra.

    ![HDInsight Linux wprowadzenie do ustawień klastra](./media/apache-hadoop-linux-create-cluster-get-started-portal/cluster-settings-overview.png "Właściwości klastra HDInsight")

## <a name="run-apache-hive-queries"></a>Uruchamianie zapytań technologii Apache Hive

[Apache Hive](hdinsight-use-hive.md) jest najbardziej popularnym składnikiem używanym w usłudze HDInsight Istnieje wiele sposobów uruchamiania zadań Hive w usłudze HDInsight. W tym przewodniku Szybki start można użyć widoku Gałąź Ambari z portalu. Aby poznać inne metody przesyłania zadań Hive, zobacz temat [Używanie Hive w usłudze HDInsight](hdinsight-use-hive.md).

> [!NOTE]
> Apache Hive View nie jest dostępny w umiań HDInsight 4.0.

1. Aby otworzyć narzędzie Ambari, na ekranie pokazanym na poprzednim zrzucie ekranu wybierz pozycję **Pulpit nawigacyjny klastra**.  Można również przejść `https://ClusterName.azurehdinsight.net` do `ClusterName` miejsca, w którym znajduje się klaster utworzony w poprzedniej sekcji.

    ![HdInsight Linux wprowadzenie pulpitu nawigacyjnego klastra](./media/apache-hadoop-linux-create-cluster-get-started-portal/hdinsight-linux-get-started-open-cluster-dashboard.png "HdInsight Linux wprowadzenie pulpitu nawigacyjnego klastra")

2. Wprowadź nazwę użytkownika Hadoop i hasło określone w podczas tworzenia klastra. Domyślna nazwa użytkownika to **admin**.

3. Otwórz widok **Hive View** pokazany na poniższym zrzucie ekranu:

    ![Wybieranie widoku gałęzi z ambari](./media/apache-hadoop-linux-create-cluster-get-started-portal/hdi-select-hive-view.png "Menu Przeglądarka gałęzi HDInsight")

4. Na karcie **QUERY** (ZAPYTANIE) wklej poniższe instrukcje HiveQL do arkusza:

    ```sql
    SHOW TABLES;
    ```

    ![Edytor zapytań widoku gałęzi HDInsight](./media/apache-hadoop-linux-create-cluster-get-started-portal/hdi-apache-hive-view1.png "Edytor zapytań widoku gałęzi HDInsight")

5. Wybierz pozycję **Wykonaj**. Poniżej karty **QUERY** (ZAPYTANIE) zostanie wyświetlona karta **RESULTS** (WYNIKI) z informacjami o zadaniu. 

    Po zakończeniu przetwarzania zapytania na karcie **QUERY** (ZAPYTANIE) są wyświetlane wyniki operacji. Powinna być widoczna jedna tabela o nazwie **hivesampletable**. Ta przykładowa tabela składnika Hive jest dostarczana z wszystkimi klastrami usługi HDInsight.

    ![Wyniki widoku ula HDInsight Apache](./media/apache-hadoop-linux-create-cluster-get-started-portal/hdinsight-hive-views.png "Wyniki widoku ula HDInsight Apache")

6. Powtórz kroki 4 i 5, aby uruchomić następujące zapytanie:

    ```sql
    SELECT * FROM hivesampletable;
    ```

7. Można także zapisać wyniki zapytania. Wybierz przycisk menu z prawej strony i określ, czy chcesz pobrać wyniki jako plik CSV, czy zapisać je na koncie magazynu skojarzonym z klastrem.

    ![Zapisz wynik kwerendy gałąź Apache](./media/apache-hadoop-linux-create-cluster-get-started-portal/hdinsight-linux-hive-view-save-results.png "Zapisz wynik kwerendy gałąź Apache")

Po zakończeniu zadania gałęzi można [wyeksportować wyniki do bazy danych SQL Database lub bazy danych SQL Server,](apache-hadoop-use-sqoop-mac-linux.md)można również [wizualizować wyniki za pomocą programu Excel](apache-hadoop-connect-excel-power-query.md). Aby uzyskać więcej informacji o korzystaniu z programu Hive w usłudze HDInsight, zobacz artykuł [Używanie Apache Hive i HiveQL z usługą Apache Hadoop w usłudze HDInsight do analizy przykładowego pliku Apache log4j](hdinsight-use-hive.md).

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Po zakończeniu szybkiego startu można usunąć klaster. Dzięki funkcji HDInsight dane są przechowywane w usłudze Azure Storage, dzięki czemu można bezpiecznie usunąć klaster, gdy nie jest używany. Naliczana jest również opłata za klaster HDInsight, nawet jeśli nie jest używana. Ponieważ opłaty za klaster są wielokrotnie większe niż opłaty za magazyn, ma ekonomiczny sens usuwanie klastrów, gdy nie są używane.

> [!NOTE]  
> Jeśli *natychmiast* przechodzisz do następnego artykułu, aby dowiedzieć się, jak uruchomić operacje ETL przy użyciu usługi Hadoop w programie HDInsight, możesz chcieć utrzymać klaster uruchomiony. To dlatego, że w tutorialu trzeba ponownie utworzyć klaster Hadoop. Jeśli jednak nie przechodzisz przez następny artykuł od razu, musisz teraz usunąć klaster.

### <a name="to-delete-the-cluster-andor-the-default-storage-account"></a>Usuwanie klastra i/lub domyślnego konta magazynu

1. Wróć do karty przeglądarki, na której znajduje się witryna Azure Portal. Musisz mieć otwartą stronę omówienia klastra. Jeśli chcesz tylko usunąć klaster, zachowując domyślne konto magazynu, wybierz pozycję **Usuń**.

    ![Klaster usuwania usługi Azure HDInsight](./media/apache-hadoop-linux-create-cluster-get-started-portal/hdinsight-delete-cluster.png "Usuwanie klastra usługi Azure HDInsight")

2. Jeśli chcesz usunąć klaster oraz domyślne konto magazynu, wybierz nazwę grupy zasobów (wyróżnioną na poprzednim zrzucie ekranu), aby otworzyć stronę grupy zasobów.

3. Wybierz pozycję **Usuń grupę zasobów**, aby usunąć grupę zasobów zawierającą klaster i domyślne konto magazynu. Uwaga: usunięcie grupy zasobów powoduje usunięcie konta magazynu. Jeśli chcesz zachować konta magazynu, wybierz opcję usunięcia tylko klastra.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start dowiesz się, jak utworzyć klaster HDInsight oparty na systemie Linux przy użyciu szablonu Menedżera zasobów i jak wykonywać podstawowe zapytania gałęzi. W następnym artykule dowiesz się, jak przeprowadzić operację wyodrębniania, transformacji i ładowania (ETL, extract, transform, and load) przy użyciu usługi Hadoop w usłudze HDInsight.

> [!div class="nextstepaction"]
> [Wyodrębnianie, przekształcanie i ładowanie danych przy użyciu interaktywnej kwerendy w programie HDInsight](../interactive-query/interactive-query-tutorial-analyze-flight-data.md)
