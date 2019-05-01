---
title: Tworzenie klastrów usługi Apache Hadoop za pomocą przeglądarki sieci web usługi Azure HDInsight
description: Dowiedz się, jak utworzyć klastry Apache Hadoop, Apache HBase, Apache Storm lub Apache Spark w systemie Linux dla HDInsight przy użyciu przeglądarki sieci web i portalu Azure w wersji zapoznawczej.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 12/28/2018
ms.author: hrasheed
ms.openlocfilehash: 9da9c6c438aac2c160a9ec7bc658e5d7b4ea207e
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64715189"
---
# <a name="create-linux-based-clusters-in-hdinsight-by-using-the-azure-portal"></a>Tworzenie klastrów opartych na systemie Linux w HDInsight przy użyciu witryny Azure portal
[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Azure portal to narzędzie do zarządzania opartego na sieci web dla usług i zasobów przechowywanych w chmurze Microsoft Azure. W tym artykule dowiesz się, jak tworzyć klastry HDInsight Azure opartych na systemie Linux przy użyciu portalu.

## <a name="prerequisites"></a>Wymagania wstępne
[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

* **Subskrypcja platformy Azure**. Zobacz [sposobu uzyskania usługi Azure bezpłatnej wersji próbnej do testowania usługi Hadoop w HDInsight](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* **Nowoczesne przeglądarki**. Azure portal używa języków HTML5 i JavaScript. Go może nie działać poprawnie w starszych przeglądarkach sieci web.

## <a name="create-clusters"></a>Tworzenie klastrów
Witryna Azure portal udostępnia większość właściwości klastra. Za pomocą szablonów usługi Azure Resource Manager, można ukryć wiele szczegółów. Aby uzyskać więcej informacji, zobacz [tworzenie technologii Apache Hadoop clusters w HDInsight przy użyciu szablonów usługi Resource Manager](hdinsight-hadoop-create-linux-clusters-arm-templates.md).

[!INCLUDE [secure-transfer-enabled-storage-account](../../includes/hdinsight-secure-transfer.md)]


1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

1. W menu po lewej stronie wybierz pozycję **+ Utwórz zasób**.

1.  W obszarze **portalu Azure Marketplace**, wybierz opcję **analizy**.

1.  W obszarze **Polecane** wybierz pozycję **HDInsight**.
   
    ![Tworzenie nowego klastra w witrynie Azure portal](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster.png "tworzenia nowego klastra w witrynie Azure portal")

1. Na **HDInsight** wybierz opcję **niestandardowe (rozmiar, ustawienia, aplikacje)**.

1. Wybierz **1 podstawy**. Następnie wprowadź następujące informacje.

    ![Konfigurowanie ustawień podstawowych](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster-basics.png "tworzenia nowego klastra w witrynie Azure portal")

    * Wprowadź **nazwa klastra**. Ta nazwa musi być unikatowa w skali globalnej.

    * Z **subskrypcji** listy rozwijanej wybierz subskrypcję platformy Azure, która jest używana dla klastra.

    * Wybierz **typ klastra**. Następnie wybierz typ klastra, który chcesz utworzyć. Przykładami są usługi Hadoop i Apache Spark. **Systemu operacyjnego** będzie **Linux**. Następnie wybierz wersję typu klastra. Użyj wersji domyślnej, jeśli nie masz pewności co do wyboru. Więcej informacji można znaleźć w temacie [HDInsight cluster versions](hdinsight-component-versioning.md) (Wersje klastrów usługi HDInsight).
     
        > [!IMPORTANT]  
        > HDInsight mogą mieć różnych typów klastrów. Odpowiadają one obciążenie, czy klaster jest ona dostrojona dla technologii. Nie istnieje obsługiwana metoda do tworzenia klastra, który łączy wiele typów. Przykładami są Storm i bazy danych HBase w jednym klastrze.
        
    * Aby uzyskać **nazwa użytkownika logowania klastra** i **hasło logowania klastra**, podaj nazwę użytkownika i hasło administratora.

    * Wprowadź **nazwy użytkownika SSH**. Takie samo jak hasło administratora określone wcześniej hasło SSH, zaznacz **Użyj tego samego hasła podczas logowania do klastra** pole wyboru. Jeśli nie, wprowadź wartość **hasło** lub **klucza publicznego** do uwierzytelnienia użytkownika SSH. Klucz publiczny jest zalecane podejście. Wybierz **wybierz** u dołu, aby zapisać konfigurację poświadczeń.
   
        Aby uzyskać więcej informacji, zobacz [nawiązywanie połączenia z HDInsight (Apache Hadoop) przy użyciu protokołu SSH](hdinsight-hadoop-linux-use-ssh-unix.md).

    * W obszarze **Grupa zasobów** określ, czy chcesz utworzyć nową grupę zasobów, czy użyć istniejącej.

    * Określ centrum danych **lokalizacji** gdzie został utworzony klaster.

    * Wybierz **dalej** aby przejść do następnej strony.

4. Z **zabezpieczenia 2 + sieć**, klaster można połączyć z siecią wirtualną, przy użyciu podanego menu rozwijanego. Wybierz sieć wirtualną platformy Azure i podsieć, jeśli chcesz umieścić klastra w sieci wirtualnej. Aby uzyskać informacje na temat używania HDInsight z siecią wirtualną, zobacz [HDInsight rozszerzanie możliwości za pomocą usługi Azure Virtual Network](hdinsight-extend-hadoop-virtual-network.md). Artykuł zawiera określone wymagania konfiguracji dla sieci wirtualnej. 

    Jeśli chcesz używać **pakiet Enterprise Security**, wykonaj te instrukcje: [Konfigurowanie klastra HDInsight z pakietem Enterprise Security za pomocą usługi Azure Active Directory Domain Services](https://docs.microsoft.com/azure/hdinsight/domain-joined/apache-domain-joined-configure-using-azure-adds).

    Wybierz **dalej** aby przejść do następnej strony.


5. Z **magazynu 3**, określ, czy usługa Azure Storage lub Azure Data Lake Storage jako magazynu domyślnego. Aby uzyskać więcej informacji zobacz w poniższej tabeli.

     ![Wprowadź ustawienia magazynu](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster-storage.png "tworzenia nowego klastra w witrynie Azure portal")

     | Magazyn                                      | Opis |
     |----------------------------------------------|-------------|
     | **Obiekty BLOB platformy Azure Storage jako magazynu domyślnego**   | <ul><li>Aby uzyskać **podstawowy typ magazynu**, wybierz opcję **usługi Azure Storage**. Aby uzyskać **metodę wyboru**, wybierz **Moje subskrypcje** Jeśli chcesz określić konto magazynu, który jest częścią subskrypcji platformy Azure. Wybierz konto magazynu. W przeciwnym razie wybierz **klucz dostępu**. Następnie podaj informacje dla konta magazynu, który chcesz dokonać wyboru spoza Twojej subskrypcji platformy Azure.</li><li>Aby uzyskać **domyślny kontener**, wybierz nazwę kontenera domyślnej, zaproponowana przez portal lub określić własny.</li><li>Jeśli usługi Azure Blob storage z magazynem domyślnym, możesz również wybrać **dodatkowych kont magazynu** do określenia dodatkowych kont magazynu do skojarzenia z klastrem. Aby uzyskać **klucze magazynu Azure**, wybierz opcję **Dodaj klucz magazynu**. Następnie możesz podać konto magazynu z subskrypcji platformy Azure lub z innych subskrypcji. Podaj klucz dostępu konta magazynu.</li><li>Jeśli usługi Blob storage to magazyn domyślny, możesz również wybrać **dostępu do usługi Data Lake Storage** do określenia usługi Azure Data Lake Storage jako magazynu dodatkowego. Aby uzyskać więcej informacji, zobacz temat [Szybki start: Konfigurowanie klastrów w usłudze HDInsight](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md).</li></ul> |
     | **Magazyn usługi Azure Data Lake jako magazyn domyślny** | Aby uzyskać **podstawowy typ magazynu**, wybierz opcję **usługi Azure Data Lake Storage Gen1** lub **usługi Azure Data Lake Storage Gen2**. Następnie można znaleźć w artykule [Szybki Start: Konfigurowanie klastrów w HDInsight](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md) instrukcje. |
     | **Zewnętrzne magazyny metadanych**                      | Opcjonalnie określ bazę danych SQL można zapisać metadanych Apache Hive i Apache Oozie skojarzonego z klastrem. Aby uzyskać **wybierz bazę danych SQL dla usługi Hive**, wybierz bazę danych SQL. Następnie podaj nazwę użytkownika i hasło dla bazy danych. Powtórz te czynności dla metadanych programu Oozie.<br><br>Pewne zagadnienia dotyczące korzystania z bazy danych Azure SQL dla magazyny metadanych są następujące: <ul><li>Bazy danych Azure SQL, który służy do magazynu metadanych muszą zezwalać na łączność z innymi usługami platformy Azure, w tym usługi Azure HDInsight. Po prawej stronie pulpitu nawigacyjnego z bazy danych Azure SQL wybierz nazwę serwera. Ten serwer jest ten, który wystąpienie usługi SQL database działa na. W trakcie widoku serwerów, wybierz **Konfiguruj**. Następnie dla **usług platformy Azure**, wybierz opcję **tak**. Następnie wybierz pozycję **Zapisz**.</li><li>Podczas tworzenia metadanych nie nazwa bazy danych za pomocą łączników i łączniki. Te znaki może spowodować niepowodzenie procesu tworzenia klastra.</li></ul> |

     > [!WARNING]  
     > Za pomocą dodatkowe konto magazynu w innej lokalizacji niż klaster HDInsight nie jest obsługiwane.

     Wybierz **dalej** aby przejść do następnej strony.


6. Z **4 aplikacji (opcjonalnie)**, zaznacz wszystkie aplikacje, które chcesz. Te aplikacje można tworzyć firmy Microsoft, niezależnych dostawców oprogramowania (ISV) lub użytkownik. Aby uzyskać więcej informacji, zobacz [instalowanie aplikacji podczas tworzenia klastra](hdinsight-apps-install-applications.md#install-applications-during-cluster-creation).

    Wybierz **dalej** aby przejść do następnej strony.


6. **Rozmiar klastra w 5** Wyświetla informacje o węzłach, które są używane dla tego klastra. Ustaw liczbę węzłów procesu roboczego, które są potrzebne dla klastra. Szacowany koszt klastra jest również wyświetlany.
   
    ![Określ węzeł warstw cenowych](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster-nodes.png "Określ liczbę węzłów klastra")
   
   > [!IMPORTANT]  
   > Jeśli planowane jest na więcej niż 32 węzły procesu roboczego, wybierz rozmiar węzła głównego z co najmniej 8 rdzeniami i 14 GB pamięci RAM. Zaplanuj węzły, podczas tworzenia klastra lub przy użyciu skalowania klastra po utworzeniu. 
   > 
   > Aby uzyskać więcej informacji na temat rozmiary węzłów i powiązanych z nimi kosztów, zobacz [cennika usługi Azure HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).
   
    Wybierz **dalej** aby przejść do następnej strony.

8. Z **6 akcji skryptu**, można dostosować klaster, aby zainstalować składniki niestandardowe. Ta opcja działa, jeśli chcesz użyć niestandardowego skryptu do dostosowywania klastra, podczas tworzenia klastra. Aby uzyskać więcej informacji na temat akcji skryptu, zobacz [HDInsight opartych na systemie Linux z Dostosowywanie klastrów za pomocą akcji skryptu](hdinsight-hadoop-customize-cluster-linux.md).

   Wybierz **dalej** aby przejść do następnej strony.

9. Z **7 Podsumowanie**, sprawdź informacje, wprowadzony wcześniej. Następnie wybierz przycisk **Utwórz**.

     ![Potwierdź konfiguracje](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster-summary.png "Określ liczbę węzłów klastra")
    
    > [!NOTE]  
    > Tworzenie klastra zajmuje trochę czasu, zwykle około 20 minut. Monitor **powiadomienia** do zapoznania się na proces inicjowania obsługi administracyjnej.

10. Po zakończeniu procesu tworzenia, wybierz **przejdź do zasobu** z **wdrażanie zakończyło się pomyślnie** powiadomień. W oknie klastra zawiera następujące informacje.
    
    ![Interfejs klastra](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster-completed.png "właściwości klastra")
    
    Ikony w oknie są wyjaśnione w następujący sposób:
    
    * **Przegląd** karta zawiera wszystkie niezbędne informacje dotyczące klastra. Przykłady to nazwa grupy zasobów, do której należy, lokalizacji, systemu operacyjnego i adres URL pulpit nawigacyjny klastra.
    * **Pulpit nawigacyjny** kieruje użytkownika do portalu Ambari skojarzonego z klastrem.
    * **Bezpieczna powłoka** udostępnia informacje niezbędne do uzyskania dostępu do klastra przy użyciu protokołu SSH.
    * Za pomocą **Skaluj klaster**, możesz zwiększyć liczbę węzłów procesu roboczego skojarzonego z klastrem.
    * **Usuń** usuwa klaster HDInsight.
    

## <a name="customize-clusters"></a>Dostosowywanie klastrów
* [Dostosowywanie klastrów HDInsight za pomocą narzędzia Bootstrap](hdinsight-hadoop-customize-cluster-bootstrap.md)
* [Dostosowywanie klastrów HDInsight opartych na systemie Linux przy użyciu akcji skryptu](hdinsight-hadoop-customize-cluster-linux.md)

## <a name="delete-the-cluster"></a>Usuwanie klastra
[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="troubleshoot"></a>Rozwiązywanie problemów

W razie problemów podczas tworzenia klastrów usługi HDInsight zapoznaj się z [wymaganiami dotyczącymi kontroli dostępu](hdinsight-hadoop-create-linux-clusters-portal.md).

## <a name="next-steps"></a>Kolejne kroki
Pomyślnie utworzono klaster usługi HDInsight. Teraz Dowiedz się, jak pracować z klastrem.

### <a name="apache-hadoop-clusters"></a>Klastry platformy Apache Hadoop
* [Use Apache Hive z HDInsight](hadoop/hdinsight-use-hive.md)
* [Apache Pig za pomocą HDInsight](hadoop/hdinsight-use-pig.md)
* [Używanie technologii MapReduce z HDInsight](hadoop/hdinsight-use-mapreduce.md)

### <a name="apache-hbase-clusters"></a>Klastry Apache HBase
* [Rozpoczynanie pracy z usługą Apache HBase na HDInsight](hbase/apache-hbase-tutorial-get-started-linux.md)
* [Twórz aplikacje Java dla bazy danych Apache HBase na HDInsight](hbase/apache-hbase-build-java-maven-linux.md)

### <a name="apache-storm-clusters"></a>Klastrów Apache Storm
* [Opracowywanie topologii języka Java dla usługi Storm Apache na HDInsight](storm/apache-storm-develop-java-topology.md)
* [Używanie składników języka Python w Storm Apache na HDInsight](storm/apache-storm-develop-python-topology.md)
* [Wdrażanie i monitorowanie topologii za pomocą Storm Apache na HDInsight](storm/apache-storm-deploy-monitor-topology-linux.md)

### <a name="apache-spark-clusters"></a>Klastry platformy Apache Spark
* [Tworzenie autonomicznych aplikacji przy użyciu języka Scala](spark/apache-spark-create-standalone-application.md)
* [Zdalne uruchamianie zadań w klastrze usługi Apache Spark przy użyciu usługi Livy Apache](spark/apache-spark-livy-rest-interface.md)
* [Platforma Apache Spark przy użyciu Power BI: Wykonaj interakcyjna analiza danych przy użyciu platformy Spark w HDInsight przy użyciu narzędzi do analizy Biznesowej](spark/apache-spark-use-bi-tools.md)
* [Platforma Apache Spark w usłudze Machine Learning: Korzystanie z platformy Spark w HDInsight do przewidywania wyników kontroli żywności](spark/apache-spark-machine-learning-mllib-ipython.md)

