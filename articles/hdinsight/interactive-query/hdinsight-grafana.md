---
title: Korzystanie z Grafana w usłudze Azure HDInsight
description: Dowiedz się, jak uzyskać dostęp do pulpitu nawigacyjnego Grafana za pomocą klastrów Apache Hadoop w usłudze Azure HDInsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 12/11/2018
ms.openlocfilehash: dae9c47f535d87214c9e1583562b4c0419cd44cf
ms.sourcegitcommit: 8a2949267c913b0e332ff8675bcdfc049029b64b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/21/2019
ms.locfileid: "74305436"
---
# <a name="access-grafana-in-azure-hdinsight"></a>Dostęp do usługi Grafana w usłudze Azure HDInsight

[Grafana](https://grafana.com/) to popularny, oparty na grafie i Konstruktor pulpitu nawigacyjnego. Grafana to funkcja rozbudowana; Umożliwia to nie tylko użytkownikom tworzenie dostosowywalnych i udostępnianych pulpitów nawigacyjnych, a także udostępnia pulpity nawigacyjne z szablonami, integrację z protokołem LDAP, wiele źródeł danych i nie tylko.

Obecnie w usłudze Azure HDInsight Grafana jest obsługiwana w przypadku typów klastrów HBase, Kafka i Interactive zapytania. Nie jest obsługiwana w przypadku klastrów z włączonym pakietem Enterprise Security Pack.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="create-an-apache-hadoop-cluster"></a>Tworzenie klastra Apache Hadoop

W tej sekcji utworzysz interaktywny klaster zapytań w usłudze HDInsight przy użyciu szablonu Azure Resource Manager. Znajomość szablonów usługi Resource Manager nie jest wymagana do korzystania z tego artykułu. 

1. Kliknij poniższy przycisk **Wdróż na platformie Azure**, aby zalogować się do platformy Azure i otworzyć szablon usługi Resource Manager w witrynie Azure Portal.
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-interactive-hive%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-grafana/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

2. Wprowadź lub wybierz wartości widoczne na poniższym zrzucie ekranu:

    > [!NOTE]  
    > Te wartości muszą być unikatowe i zgodne z wytycznymi dotyczącymi nazewnictwa. Szablon nie wykonuje testów walidacyjnych. Jeśli okaże się, że podane wartości są już używane lub nie są zgodne z wytycznymi, po przesłaniu szablonu wystąpi błąd.       
    > 
    >
    
    ![Szablon Menedżer zasobów wprowadzenie do usługi HDInsight Linux w portalu](./media/hdinsight-grafana/hdinsight-linux-get-started-arm-template-on-portal.png "Wdrażanie klastra Hadoop w programie HDInsigut przy użyciu Azure Portal i szablonu Menedżera grupy zasobów")

    Wprowadź lub wybierz poniższe wartości:
    
    |Właściwość  |Opis  |
    |---------|---------|
    |**Subskrypcja**     |  Wybierz swoją subskrypcję platformy Azure. |
    |**Grupa zasobów**     | Utwórz grupę zasobów lub wybierz istniejącą grupę zasobów.  Grupa zasobów jest kontenerem składników platformy Azure.  W tym przypadku grupa zasobów zawiera klaster usługi HDInsight i zależne konto usługi Azure Storage. |
    |**Lokalizacja**     | Wybierz lokalizację platformy Azure, w której chcesz utworzyć klaster.  Wybierz lokalizację znajdującą się blisko, aby zapewnić lepszą wydajność. |
    |**Typ klastra**     | Wybierz pozycję **hadoop**. |
    |**Nazwa klastra**     | Wprowadź nazwę dla klastra Apache Hadoop. Ponieważ wszystkie klastry w usłudze HDInsight używają tej samej przestrzeni nazw DNS, ta nazwa musi być unikatowa. Nazwa może składać się z maksymalnie 59 znaków, w tym liter, cyfr i łączników. Łącznik nie może być pierwszym ani ostatnim znakiem nazwy. |
    |**Nazwa użytkownika i hasło logowania do klastra**     | Domyślna nazwa logowania to **admin**. Hasło musi składać się z co najmniej 10 znaków i musi zawierać co najmniej jedną cyfrę, jedną wielką i jedną małą literę oraz jeden znak inny niż alfanumeryczny (z wyjątkiem znaków "" "\). Upewnij się, że **nie zostało podane** typowe hasło, takie jak „Pass@word1”.|
    |**Nazwa użytkownika i hasło protokołu SSH**     | Domyślna nazwa użytkownika to **sshuser**.  Nazwę użytkownika SSH można zmienić.  Hasło użytkownika SSH ma te same wymagania co hasło logowania klastra.|

    Niektóre właściwości zostały umieszczone w kodzie w szablonie.  Te wartości można skonfigurować z szablonu. Aby uzyskać więcej informacji o tych właściwościach, zobacz artykuł [Create Hadoop clusters in HDInsight](../hdinsight-hadoop-provision-linux-clusters.md) (Tworzenie klastrów platformy Hadoop w usłudze HDInsight).

3. Wybierz pozycję **Wyrażam zgodę na powyższe warunki i postanowienia** i pozycję **Przypnij do pulpitu nawigacyjnego**, a następnie wybierz przycisk **Kup**. Na pulpicie nawigacyjnym portalu powinien zostać wyświetlony nowy kafelek zatytułowany **Przekazywanie wdrożenia**. Utworzenie klastra trwa około 20 minut.

    ![Postęp Template deployment platformy Azure](./media/hdinsight-grafana/deployment-progress-tile.png "Postęp Template deployment platformy Azure")

4. Po utworzeniu klastra napis na kafelku zmieni się na podaną nazwę grupy zasobów. Kafelek zawiera także nazwę klastra usługi HDInsight, utworzonego w grupie zasobów.

    ![Grupa zasobów wprowadzenie do usługi HDInsight w systemie Linux](./media/hdinsight-grafana/hdinsight-linux-get-started-resource-group.png "Grupa zasobów klastra usługi Azure HDInsight")

5. Kafelek zawiera także nazwę magazynu domyślnego, skojarzonego z klastrem. Każdy klaster zależy od [konta usługi Azure Storage](../hdinsight-hadoop-use-blob-storage.md) lub od [konta usługi Azure Data Lake](../hdinsight-hadoop-use-data-lake-store.md). Jest ono określane jako domyślne konto magazynu. Klaster usługi HDInsight i jego domyślne konto magazynu muszą znajdować się w tym samym regionie platformy Azure. Usunięcie klastrów nie powoduje usunięcia konta magazynu.
    

> [!NOTE]  
> Aby poznać inne metody tworzenia klastrów i zrozumieć właściwości używane w tym artykule, zobacz [Tworzenie klastrów usługi HDInsight](../hdinsight-hadoop-provision-linux-clusters.md). 

## <a name="access-the-grafana-dashboard"></a>Dostęp do pulpitu nawigacyjnego Grafana

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

2. Wybierz pozycję **Klastry usługi HDInsight**, a następnie wybierz nazwę klastra utworzoną w ostatniej sekcji.

3. W obszarze **szybkie linki**kliknij pozycję **pulpit nawigacyjny klastra**.

    ![Portal pulpitu nawigacyjnego klastra usługi HDInsight](./media/hdinsight-grafana/hdinsight-portal-cluster-dashboard.png "Pulpit nawigacyjny klastra usługi HDInsight w portalu")

4. Na pulpicie nawigacyjnym kliknij kafelek **Grafana** . Alternatywnie przejdź do ścieżki `/grafana/` adresu URL klastra. Na przykład `https://<clustername>.azurehdinsight.net/grafana/`.

5. Wprowadź poświadczenia użytkownika klastra usługi Hadoop.

6. Zostanie wyświetlony pulpit nawigacyjny Grafana, który wygląda następująco:

    ![Pulpit nawigacyjny sieci Web usługi HDInsight Grafana](./media/hdinsight-grafana/hdinsight-grafana-dashboard.png "Pulpit nawigacyjny usługi HDInsight Grafana")

## <a name="clean-up-resources"></a>Oczyszczanie zasobów
Po ukończeniu korzystania z artykułu warto usunąć klaster. Dzięki usłudze HDInsight dane są przechowywane w usłudze Azure Storage, więc można bezpiecznie usunąć klaster, gdy nie jest używany. Opłaty za klaster usługi HDInsight są naliczane nawet wtedy, gdy nie jest używany. Ponieważ opłaty za klaster są wielokrotnie większe niż opłaty za magazyn, ze względów ekonomicznych warto usuwać klastry, gdy nie są używane. 

> [!NOTE]  
> Jeśli chcesz *natychmiast* przejść do następnego samouczka, aby dowiedzieć się, jak uruchomić operacje ETL przy użyciu usługi Hadoop w usłudze HDInsight, warto zachować działający klaster. Jest to spowodowane tym, że w samouczku trzeba ponownie utworzyć klaster usługi Hadoop. Jeśli jednak nie chcesz od razu przechodzić do następnego samouczka, musisz teraz usunąć klaster.

**Usuwanie klastra i/lub domyślnego konta magazynu**

1. Wróć do karty przeglądarki, na której znajduje się witryna Azure Portal. Musisz mieć otwartą stronę omówienia klastra. Jeśli chcesz tylko usunąć klaster, zachowując domyślne konto magazynu, wybierz pozycję **Usuń**.

    ![Ikona usuwania klastra Azure Portal](./media/hdinsight-grafana/hdinsight-delete-cluster.png "Usuwanie klastra usługi HDInsight")

2. Jeśli chcesz usunąć klaster oraz domyślne konto magazynu, wybierz nazwę grupy zasobów (wyróżnioną na poprzednim zrzucie ekranu), aby otworzyć stronę grupy zasobów.

3. Wybierz pozycję **Usuń grupę zasobów**, aby usunąć grupę zasobów zawierającą klaster i domyślne konto magazynu. Uwaga: usunięcie grupy zasobów powoduje usunięcie konta magazynu. Jeśli chcesz zachować konta magazynu, wybierz opcję usunięcia tylko klastra.

## <a name="next-steps"></a>Następne kroki
W tym artykule przedstawiono sposób tworzenia klastra usługi HDInsight opartej na systemie Linux przy użyciu szablonu Menedżer zasobów i wykonywania podstawowych zapytań Apache Hive. W następnym artykule dowiesz się, jak przeprowadzić operację wyodrębniania, transformacji i ładowania (ETL, extract, transform, and load) przy użyciu usługi Hadoop w usłudze HDInsight.

> [!div class="nextstepaction"]
>[Wyodrębnianie, przekształcanie i ładowanie danych przy użyciu interakcyjnych zapytań w usłudze HDInsight](../interactive-query/interactive-query-tutorial-analyze-flight-data.md)

Jeśli chcesz już zacząć korzystać z własnych danych i dowiedzieć się więcej o sposobie przechowywania danych w usłudze HDInsight lub sposobie pobierania danych do usługi HDInsight, zobacz następujące artykuły:

* Aby uzyskać informacje o sposobie używania usługi Azure Storage przez usługę HDInsight, zobacz [Używanie usługi Azure Storage z usługą HDInsight](../hdinsight-hadoop-use-blob-storage.md).
* Aby uzyskać informacje na temat przekazywania danych do usługi HDInsight, zobacz [Przekazywanie danych do usługi HDInsight](../hdinsight-upload-data.md).

Aby dowiedzieć się więcej na temat analizowania danych za pomocą usługi HDInsight, zobacz następujące artykuły:

* Aby dowiedzieć się więcej o korzystaniu z programu Hive z usługą HDInsight, w tym poznać sposoby wykonywania zapytań Hive z programu Visual Studio, zobacz artykuł [Korzystanie z programu Apache Hive z usługą HDInsight](../hdinsight-use-hive.md).
* Aby dowiedzieć się więcej na temat języka Pig używanego do przekształcania danych, zobacz artykuł [Korzystanie z języka Pig z usługą HDInsight](../hdinsight-use-pig.md).
* Aby dowiedzieć się więcej o MapReduce, czyli sposobie pisania programów przetwarzających dane w usłudze Hadoop, zobacz [Używanie MapReduce z usługą HDInsight](../hdinsight-use-mapreduce.md).
* Aby dowiedzieć się więcej o używaniu narzędzi HDInsight Tools for Visual Studio do analizowania danych w usłudze HDInsight, zobacz [Wprowadzenie do używania narzędzi Visual Studio Hadoop dla usługi HDInsight](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).



Jeśli chcesz dowiedzieć się więcej o tworzeniu klastra usługi HDInsight i zarządzaniu nim, zobacz następujące artykuły:

* Aby uzyskać więcej informacji na temat zarządzania opartym na systemie Linux klastrem usługi HDInsight, zobacz [Zarządzanie klastrami usługi HDInsight za pomocą narzędzia Ambari](../hdinsight-hadoop-manage-ambari.md).
* Aby dowiedzieć się więcej na temat opcji, które można wybrać podczas tworzenia klastra usługi HDInsight, zobacz [Tworzenie klastra usługi HDInsight w systemie Linux przy użyciu niestandardowych opcji](../hdinsight-hadoop-provision-linux-clusters.md).


[1]: ../HDInsight/apache-hadoop-visual-studio-tools-get-started.md

[hdinsight-provision]: hdinsight-provision-linux-clusters.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md
