---
title: Tworzenie klastrów Apache Hadoop przy użyciu przeglądarki sieci Web, Azure HDInsight
description: Dowiedz się, jak tworzyć klastry Apache Hadoop, Apache HBase, Apache Storm lub Apache Spark w systemie Linux dla usługi HDInsight przy użyciu przeglądarki sieci Web i Azure Portal.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/13/2019
ms.openlocfilehash: 820ddb8d06cfd2aac2b053305f23ad330e4fd7c3
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74215911"
---
# <a name="create-linux-based-clusters-in-hdinsight-by-using-the-azure-portal"></a>Tworzenie klastrów opartych na systemie Linux w usłudze HDInsight przy użyciu Azure Portal

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Azure Portal to oparte na sieci Web narzędzie do zarządzania usługami i zasobami hostowanymi w chmurze Microsoft Azure. W tym artykule dowiesz się, jak tworzyć klastry usługi Azure HDInsight oparte na systemie Linux przy użyciu portalu.

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="prerequisites"></a>Wymagania wstępne

* **Subskrypcja platformy Azure**. Zobacz [, jak uzyskać bezpłatną wersję próbną platformy Azure do testowania usługi Hadoop w usłudze HDInsight](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* **Nowoczesnej przeglądarki sieci Web**. Azure Portal używa HTML5 i JavaScript. Może nie działać poprawnie w starszych przeglądarkach sieci Web.

## <a name="create-clusters"></a>Tworzenie klastrów

Azure Portal uwidacznia większość właściwości klastra. Za pomocą szablonów Azure Resource Manager można ukryć wiele szczegółów. Aby uzyskać więcej informacji, zobacz [Tworzenie klastrów Apache Hadoop w usłudze HDInsight przy użyciu szablonów Menedżer zasobów](hdinsight-hadoop-create-linux-clusters-arm-templates.md).

[!INCLUDE [secure-transfer-enabled-storage-account](../../includes/hdinsight-secure-transfer.md)]

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

1. W menu po lewej stronie przejdź do **+ Utwórz zasób** >  **Analytics** > **usługi Azure HDInsight**.

    ![Utwórz nowy klaster w Azure Portal](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster.png "Tworzenie nowego klastra w Azure Portal")

1. Na stronie **Tworzenie klastra usługi HDInsight** wybierz pozycję **Przejdź do klasycznego tworzenia środowiska**.

    ![Przejdź do klasycznego środowiska tworzenia](./media/hdinsight-hadoop-create-linux-clusters-portal/azure-portal-cluster-create-classic.png)

1. Na stronie **HDInsight** wybierz pozycję **niestandardowe (rozmiar, ustawienia, aplikacje)** .

1. Wybierz **1 podstawowe**elementy. Następnie wprowadź następujące informacje:

    |Właściwość |Opis |
    |---|---|
    |Nazwa klastra|Ta nazwa musi być unikatowa w skali globalnej.|
    |Subskrypcja|Z listy rozwijanej wybierz subskrypcję platformy Azure używaną na potrzeby klastra.|
    |Typ klastra|Wybierz typ klastra, który chcesz utworzyć. Przykłady to Hadoop i Apache Spark. **System operacyjny** to **Linux**. Następnie wybierz wersję typu klastra. Użyj wersji domyślnej, jeśli nie wiesz, co wybrać. Więcej informacji można znaleźć w temacie [HDInsight cluster versions](hdinsight-component-versioning.md) (Wersje klastrów usługi HDInsight).|
    |Nazwa użytkownika logowania klastra|Podaj nazwę użytkownika — wartość domyślna to **admin**.|
    |Hasło logowania klastra|Podaj hasło.|
    |Nazwa użytkownika protokołu SSH (Secure Shell)|Wartość domyślna to **sshuser**. Jeśli chcesz użyć tego samego hasła SSH jako hasła administratora określonego wcześniej, zaznacz pole wyboru **Użyj hasła logowania klastra dla SSH** . Jeśli nie, podaj **hasło** lub **klucz publiczny** , aby uwierzytelnić użytkownika ssh. Zaleca się użycie klucza publicznego. Wybierz **pozycję Wybierz** u dołu, aby zapisać konfigurację poświadczeń.  Aby uzyskać więcej informacji, zobacz [nawiązywanie połączenia z usługą HDInsight (Apache Hadoop) przy użyciu protokołu SSH](hdinsight-hadoop-linux-use-ssh-unix.md).|
    |Grupa zasobów|Określ, czy chcesz utworzyć nową grupę zasobów, czy użyć istniejącej grupy.|
    |Lokalizacja|Określ centrum danych, w którym jest tworzony klaster.|

    ![Podstawowe informacje dotyczące tworzenia klastra usługi HDInsight](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster-basics.png "Tworzenie nowego klastra w Azure Portal")

    > [!IMPORTANT]  
    > Klastry usługi HDInsight są dostępne w różnych typach. Są one zgodne z obciążeniem lub technologią, dla której jest dostrojony klaster. Nie ma obsługiwanej metody tworzenia klastra, który łączy wiele typów. Przykłady to burza i HBase w jednym klastrze.

    Wybierz pozycję **dalej** , aby przejść do następnej strony.

1. W przypadku **2 zabezpieczeń i sieci**można połączyć klaster z siecią wirtualną za pomocą podanego menu rozwijanego. Wybierz sieć wirtualną platformy Azure i podsieć, jeśli chcesz umieścić klaster w sieci wirtualnej. Aby uzyskać informacje na temat korzystania z usługi HDInsight z siecią wirtualną, zobacz [Planowanie wdrożenia sieci wirtualnej dla klastrów usługi Azure HDInsight](hdinsight-plan-virtual-network-deployment.md). Artykuł zawiera określone wymagania dotyczące konfiguracji dla sieci wirtualnej.

    Jeśli chcesz użyć **pakiet Enterprise Security**, wykonaj następujące instrukcje: [skonfiguruj klaster usługi HDInsight z pakiet Enterprise Security przy użyciu Azure Active Directory Domain Services](https://docs.microsoft.com/azure/hdinsight/domain-joined/apache-domain-joined-configure-using-azure-adds).

    Wybierz pozycję **dalej** , aby przejść do następnej strony.

1. Z poziomu **3 magazynu**dla **ustawień konta magazynu**Określ, czy chcesz, aby usługa Azure Storage lub Azure Data Lake Storage jako magazyn domyślny. Aby uzyskać więcej informacji, zobacz poniższą tabelę.

    | Podstawowy typ magazynu | Opis |
    |------------------|-------------|
    | Azure Storage   |  * Wybierz pozycję **Moje subskrypcje** , aby określić konto **magazynu, które**jest częścią subskrypcji platformy Azure. Następnie wybierz konto magazynu. W przeciwnym razie wybierz pozycję **klucz dostępu**. Następnie podaj informacje dotyczące konta magazynu, które chcesz wybrać spoza subskrypcji platformy Azure.</br></br> * W przypadku **kontenera domyślnego**wybierz domyślną nazwę kontenera sugerowaną przez portal lub określ własny.</br></br> * Jeśli magazyn obiektów blob platformy Azure jest magazynem domyślnym, możesz również wybrać **dodatkowe konta magazynu** , aby określić dodatkowe konta magazynu do skojarzenia z klastrem. W przypadku **kluczy usługi Azure Storage**wybierz pozycję **Dodaj klucz magazynu**. Następnie możesz podać konto magazynu z subskrypcji platformy Azure lub z innych subskrypcji. Podaj klucz dostępu do konta magazynu.</br></br> * Jeśli magazyn obiektów BLOB jest domyślnym magazynem, możesz również wybrać opcję **Data Lake Storage dostęp** , aby określić Azure Data Lake Storage jako dodatkowy magazyn. Aby uzyskać więcej informacji, zobacz [Szybki Start: Konfigurowanie klastrów w usłudze HDInsight](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md).</li></ul> |
    | Azure Data Lake Storage | Wybierz **Azure Data Lake Storage Gen1** lub **Azure Data Lake Storage Gen2**. Następnie zapoznaj się z artykułem [Szybki Start: Konfigurowanie klastrów w usłudze HDInsight](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md) , aby uzyskać instrukcje. |

    **Ustawienia magazynu metadanych (opcjonalnie)**

    Jako opcję należy określić bazę danych SQL, w której mają zostać zapisane metadane Apache Hive i Apache Oozie skojarzone z klastrem. Wybierz bazę danych SQL, aby **wybrać bazę danych SQL dla usługi Hive**. Podaj nazwę użytkownika i hasło dla bazy danych. Powtórz te kroki dla metadanych Oozie.

    Niektóre zagadnienia dotyczące korzystania z usługi Azure SQL Database dla magazynu metadanych są następujące:
    * Baza danych Azure SQL Database, która jest używana na potrzeby magazynu metadanych, musi zezwalać na łączność z innymi usługami platformy Azure, w tym usługą Azure HDInsight. Po prawej stronie pulpitu nawigacyjnego usługi Azure SQL Database wybierz nazwę serwera. Ten serwer jest serwerem, na którym działa wystąpienie bazy danych SQL. Po zakończeniu korzystania z widoku serwer wybierz pozycję **Konfiguruj**. W obszarze **usługi platformy Azure**wybierz pozycję **tak**. Następnie wybierz pozycję **Zapisz**.
    * Podczas tworzenia magazynu metadanych nie należy nazwać bazy danych za pomocą łączników ani łączników. Te znaki mogą spowodować niepowodzenie procesu tworzenia klastra.

    ![Magazyn tworzenia klastra usługi HDInsight](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster-storage.png "Tworzenie nowego klastra w Azure Portal")

    > [!WARNING]  
    > Użycie dodatkowego konta magazynu w innej lokalizacji niż klaster usługi HDInsight nie jest obsługiwane.

    Wybierz pozycję **dalej** , aby przejść do następnej strony.

1. Z **4 aplikacji (opcjonalnie)** wybierz wszystkie aplikacje, które chcesz. Firma Microsoft, niezależni dostawcy oprogramowania (ISV) lub mogą opracowywać te aplikacje. Aby uzyskać więcej informacji, zobacz [Instalowanie aplikacji podczas tworzenia klastra](hdinsight-apps-install-applications.md#install-applications-during-cluster-creation).

    Wybierz pozycję **dalej** , aby przejść do następnej strony.

1. **5 rozmiar klastra** wyświetla informacje o węzłach, które są używane dla tego klastra. Ustaw liczbę węzłów procesu roboczego, które są potrzebne w klastrze. Zostanie również wyświetlony szacowany koszt uruchomienia klastra.

    ![Tworzenie węzłów klastra usługi HDInsight](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster-nodes.png "Określ liczbę węzłów klastra")

   > [!IMPORTANT]  
   > Jeśli planujesz więcej niż 32 węzłów procesu roboczego, wybierz rozmiar węzła głównego z co najmniej 8 rdzeniami i 14 GB pamięci RAM. Planowanie węzłów podczas tworzenia klastra lub skalowanie klastra po utworzeniu.
   >
   > Aby uzyskać więcej informacji na temat rozmiarów węzłów i skojarzonych z nimi kosztów, zobacz [Cennik usługi Azure HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

    Wybierz pozycję **dalej** , aby przejść do następnej strony.

1. Z **6 akcji skryptu**można dostosować klaster, aby zainstalować składniki niestandardowe. Ta opcja działa, jeśli chcesz użyć niestandardowego skryptu w celu dostosowania klastra podczas tworzenia klastra. Aby uzyskać więcej informacji na temat akcji skryptu, zobacz [Dostosowywanie klastrów usługi HDInsight opartych na systemie Linux za pomocą akcji skryptu](hdinsight-hadoop-customize-cluster-linux.md).

   Wybierz pozycję **dalej** , aby przejść do następnej strony.

1. W **podsumowaniu 7**Sprawdź wprowadzone wcześniej informacje. Następnie wybierz przycisk **Utwórz**.

    ![Podsumowanie tworzenia klastra usługi HDInsight](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster-summary.png "Określ liczbę węzłów klastra")

    > [!NOTE]  
    > Tworzenie klastra zajmuje trochę czasu, zwykle około 20 minut. Monitoruj **powiadomienia** , aby sprawdzić proces aprowizacji.

1. Po zakończeniu procesu tworzenia wybierz pozycję **Przejdź do zasobu** w ramach powiadomienia o **pomyślnym wdrożeniu** . Okno klaster zawiera poniższe informacje.

    ![HDI klaster Azure Portal — Omówienie](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster-completed.png "Właściwości klastra")

    Niektóre ikony w oknie są wyjaśnione w następujący sposób:

    |Właściwość | Opis |
    |---|---|
    |Omówienie|Zawiera wszystkie podstawowe informacje o klastrze. Przykładami są nazwa, Grupa zasobów, do której należy, lokalizacja, system operacyjny i adres URL pulpitu nawigacyjnego klastra.|
    |Pulpity nawigacyjne klastra|Kieruje użytkownika do portalu Ambari skojarzonego z klastrem.|
    |SSH + logowanie do klastra|Zapewnia informacje konieczne do uzyskania dostępu do klastra przy użyciu protokołu SSH.|
    |Usuwanie|Usuwa klaster usługi HDInsight.|

## <a name="customize-clusters"></a>Dostosowywanie klastrów

* [Dostosowywanie klastrów usługi HDInsight przy użyciu narzędzia Bootstrap](hdinsight-hadoop-customize-cluster-bootstrap.md)
* [Dostosowywanie klastrów usługi HDInsight opartych na systemie Linux za pomocą akcji skryptu](hdinsight-hadoop-customize-cluster-linux.md)

## <a name="delete-the-cluster"></a>Usuwanie klastra

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="troubleshoot"></a>Rozwiązywanie problemów

W razie problemów podczas tworzenia klastrów usługi HDInsight zapoznaj się z [wymaganiami dotyczącymi kontroli dostępu](hdinsight-hadoop-create-linux-clusters-portal.md).

## <a name="next-steps"></a>Następne kroki

Klaster usługi HDInsight został pomyślnie utworzony. Teraz Dowiedz się, jak korzystać z klastra.

### <a name="apache-hadoop-clusters"></a>Klastry Apache Hadoop

* [Korzystanie z Apache Hive z usługą HDInsight](hadoop/hdinsight-use-hive.md)
* [Korzystanie z MapReduce z usługą HDInsight](hadoop/hdinsight-use-mapreduce.md)

### <a name="apache-hbase-clusters"></a>Klastry Apache HBase

* [Wprowadzenie do platformy Apache HBase w usłudze HDInsight](hbase/apache-hbase-tutorial-get-started-linux.md)
* [Tworzenie aplikacji Java dla platformy Apache HBase w usłudze HDInsight](hbase/apache-hbase-build-java-maven-linux.md)

### <a name="apache-storm-clusters"></a>Klastry Apache Storm

* [Tworzenie topologii języka Java dla Apache Storm w usłudze HDInsight](storm/apache-storm-develop-java-topology.md)
* [Używanie składników języka Python w Apache Storm w usłudze HDInsight](storm/apache-storm-develop-python-topology.md)
* [Wdrażanie i monitorowanie topologii za pomocą Apache Storm w usłudze HDInsight](storm/apache-storm-deploy-monitor-topology-linux.md)

### <a name="apache-spark-clusters"></a>Klastry Apache Spark

* [Tworzenie aplikacji autonomicznej za pomocą Scala](spark/apache-spark-create-standalone-application.md)
* [Zdalne uruchamianie zadań w klastrze Apache Spark przy użyciu oprogramowania Apache usługi Livy](spark/apache-spark-livy-rest-interface.md)
* [Apache Spark z usługą BI: wykonywanie interakcyjnej analizy danych przy użyciu platformy Spark w usłudze HDInsight przy użyciu narzędzi analizy biznesowej](spark/apache-spark-use-bi-tools.md)
* [Apache Spark z Machine Learning: korzystanie z platformy Spark w usłudze HDInsight do przewidywania wyników inspekcji żywności](spark/apache-spark-machine-learning-mllib-ipython.md)
