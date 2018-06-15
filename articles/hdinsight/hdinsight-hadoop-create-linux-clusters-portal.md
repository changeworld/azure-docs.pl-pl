---
title: Tworzenie klastrów platformy Hadoop za pomocą przeglądarki sieci web - Azure HDInsight | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć klastry Hadoop, HBase, Storm i Spark w systemie Linux dla usługi HDInsight przy użyciu przeglądarki sieci web i portalu Azure w wersji zapoznawczej.
services: hdinsight
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 697278cf-0032-4f7c-b9b2-a84c4347659e
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 02/21/2018
ms.author: nitinme
ms.openlocfilehash: 13f746697a7e694da79a6e376b45f95529049a44
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
ms.locfileid: "31401965"
---
# <a name="create-linux-based-clusters-in-hdinsight-using-the-azure-portal"></a>Tworzenie klastrów z systemem Linux w usłudze HDInsight przy użyciu portalu Azure
[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Azure portal to narzędzie do zarządzania opartych na sieci web dla usług i zasobów przechowywanych w chmurze Microsoft Azure. W tym artykule należy informacje o sposobie tworzenia opartych na systemie Linux klastrów usługi HDInsight przy użyciu portalu.

## <a name="prerequisites"></a>Wymagania wstępne
[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

* **Subskrypcja platformy Azure**. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* **Nowoczesna przeglądarka sieci web**. Azure portal używa HTML5 i Javascript i mogą nie działać poprawnie w starszych przeglądarek sieci web.

## <a name="create-clusters"></a>Tworzenie klastrów
Azure portal udostępnia większość właściwości klastra. Przy użyciu szablonu usługi Azure Resource Manager można ukryć szczegóły wiele. Aby uzyskać więcej informacji, zobacz [utworzyć Linux opartych klastrów Hadoop w usłudze HDInsight przy użyciu szablonów usługi Azure Resource Manager](hdinsight-hadoop-create-linux-clusters-arm-templates.md).

[!INCLUDE [secure-transfer-enabled-storage-account](../../includes/hdinsight-secure-transfer.md)]


1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Kliknij przycisk **+**, kliknij przycisk **analizy i analiza**, a następnie kliknij przycisk **HDInsight**.
   
    ![Tworzenie nowego klastra w portalu Azure](./media/hdinsight-hadoop-create-linux-cluster-portal/hdinsight-create-cluster.png "tworzenia nowego klastra w portalu Azure")

3. W **HDInsight** bloku, kliknij przycisk **niestandardowe (rozmiar, ustawienia aplikacji)**, kliknij przycisk **podstawy**, a następnie wprowadź następujące informacje.

    ![Tworzenie nowego klastra w portalu Azure](./media/hdinsight-hadoop-create-linux-cluster-portal/hdinsight-create-cluster-basics.png "tworzenia nowego klastra w portalu Azure")

    * Wprowadź wartość w polu **Nazwa klastra**: ta nazwa musi być globalnie unikatowa.

    * Z **subskrypcji** listy rozwijanej, wybierz subskrypcję platformy Azure, które jest używane dla klastra.

    * Kliknij przycisk **typ klastra**, a następnie wybierz typ klastra (Hadoop, Spark, itp.), do którego chcesz utworzyć. Aby uzyskać **systemu operacyjnego**, kliknij przycisk **Linux** , a następnie wybierz wersję. Użyj wersji domyślnej, jeśli nie wiesz, co do wyboru. Więcej informacji można znaleźć w temacie [HDInsight cluster versions](hdinsight-component-versioning.md) (Wersje klastrów usługi HDInsight).

        Dla typów klastra usługi Hadoop, Spark i interaktywne zapytania, można również wybrać do zainstalowania **pakiet zabezpieczeń Enterprise**. Pakiet zabezpieczeń przedsiębiorstwa włącza funkcje zabezpieczeń, takich jak integracja z usługą Azure Active Directory i zakres Apache dla klastrów. Aby uzyskać więcej informacji, zobacz [pakiet zabezpieczeń przedsiębiorstwa w usłudze Azure HDInsight](./domain-joined/apache-domain-joined-introduction.md).

        ![Włącz pakiet zabezpieczeń Enterprise](./media/hdinsight-hadoop-create-linux-cluster-portal/hdinsight-enable-enterprise-security-package.png "Włącz pakiet zabezpieczeń przedsiębiorstwa")
     
        > [!IMPORTANT]
        > HDInsight mogą mieć różnych typów, które odpowiadają obciążenia lub technologii, która jest dostosowana klastra na potrzeby klastrów. Nie istnieje metoda obsługiwanych do utworzenia klastra, który łączy wiele typów, takie jak Storm i bazy danych HBase w jednym klastrze. 
        > 
        > 
        
    * Aby uzyskać **nazwa użytkownika logowania klastra** i **hasło logowania klastra**, podaj nazwę użytkownika i hasło użytkownika dla administratora.

    * Wprowadź **nazwa użytkownika SSH** , a jeśli chcesz, aby hasło SSH, wybierz pozycję takie samo jak hasło administratora, określony wcześniej **Użyj tego samego hasła jak logowania do klastra** pole wyboru. Jeśli nie, wprowadź wartość **hasło** lub **klucz PUBLICZNY**, który będzie używany do uwierzytelniania użytkownika SSH. Zaleca się użycie klucza publicznego. Kliknij pozycję **Wybierz** u dołu, aby zapisać konfigurację poświadczeń.
   
    Aby uzyskać informacje, zobacz [Używanie protokołu SSH w usłudze HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

    * W obszarze **Grupa zasobów** określ, czy chcesz utworzyć nową grupę zasobów, czy użyć istniejącej.

    * Określ centrum danych **lokalizacji** gdzie tworzenia klastra.

    * Kliknij przycisk **Dalej**.

4. Aby uzyskać **magazynu**, określ, czy usługi Azure Storage (WASB) lub usługi Data Lake Store jako domyślnego magazynu. Szukaj w poniższej tabeli, aby uzyskać więcej informacji.

    ![Tworzenie nowego klastra w portalu Azure](./media/hdinsight-hadoop-create-linux-cluster-portal/hdinsight-create-cluster-storage.png "tworzenia nowego klastra w portalu Azure")

    | Magazyn                                      | Opis |
    |----------------------------------------------|-------------|
    | **Obiektów blob magazynu Azure jako domyślnego magazynu**   | <ul><li>Dla **typu podstawowego magazynu**, wybierz pozycję **usługi Azure Storage**. Po wykonaniu tej dla **metodę wyboru**, można wybrać **Moje subskrypcje** Jeśli chcesz określić konto magazynu, który jest częścią subskrypcji platformy Azure, a następnie wybierz konto magazynu. W przeciwnym razie kliknij przycisk **klucz dostępu** i podaj informacje dotyczące konta magazynu, który chcesz wybrać poza subskrypcją platformy Azure.</li><li>Aby uzyskać **domyślny kontener**, można przejść z domyślną nazwą kontenera sugerowane w portalu lub określić własne.</li><li>Jeśli używasz WASB jako domyślny magazyn, (opcjonalnie) można kliknąć **dodatkowych kont magazynu** do określenia dodatkowych kont magazynu do skojarzenia z klastrem. Dla **klucze magazynu Azure**, kliknij przycisk **Dodaj klucz magazynu**, a następnie można podać konto magazynu z subskrypcjami platformy Azure lub inne subskrypcje (przez dostarczanie klucz dostępu do konta magazynu).</li><li>Jeśli używasz WASB jako domyślny magazyn, (opcjonalnie) można kliknąć **dostępu do usługi Data Lake Store** umożliwia określenie Azure Data Lake Store jako dodatkowego magazynu. Aby uzyskać więcej informacji, zobacz [tworzenia klastra usługi HDInsight z Data Lake Store za pomocą portalu Azure](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md).</li></ul> |
    | **Azure Data Lake Store jako domyślnego magazynu** | Dla **typu podstawowego magazynu**, wybierz pozycję **usługi Data Lake Store** , a następnie zapoznaj się z artykułem [tworzenia klastra usługi HDInsight z Data Lake Store za pomocą portalu Azure](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md) instrukcje. |
    | **Zewnętrznych magazynów metadanych**                      | Opcjonalnie można określić bazy danych SQL można zapisać metadanych Hive i Oozie skojarzony z klastrem. Aby uzyskać **wybierz bazę danych SQL dla gałęzi** wybierz bazę danych SQL, a następnie podaj nazwę użytkownika/hasło dla bazy danych. Powtórz te kroki dla metadanych Oozie.<br><br>Niektóre kwestie wymagające rozważenia podczas korzystania z bazy danych Azure SQL dla magazynów metadanych. <ul><li>Baza danych Azure SQL używana na potrzeby magazynu metadanych muszą zezwalać na łączności z innymi usługami Azure, w tym Azure HDInsight. Na pulpicie nawigacyjnym bazy danych Azure SQL, po prawej stronie kliknij nazwę serwera. Jest to serwer, na którym działa wystąpienie bazy danych SQL. Po przejściu do widoku serwerów, kliknij przycisk **Konfiguruj**, a następnie **usług Azure**, kliknij przycisk **tak**, a następnie kliknij przycisk **zapisać**.</li><li>Podczas tworzenia potrzeby magazynu metadanych, nie należy używać nazwy bazy danych, która zawiera łączniki lub łączników, ponieważ może to spowodować niepowodzenie procesu tworzenia klastra.</li></ul> |

    Kliknij przycisk **Dalej**. 

    > [!WARNING]
    > Korzystanie z dodatkowego konta magazynu w innej lokalizacji niż klaster usługi HDInsight nie jest obsługiwane.

5. Opcjonalnie kliknij **aplikacji** do zainstalowania aplikacji, które współpracują z klastrami usługi HDInsight. Te aplikacje mogą być opracowane przez firmę Microsoft, niezależnych dostawców oprogramowania (ISV) lub samodzielnie. Aby uzyskać więcej informacji, zobacz [HDInsight zainstalować aplikacje](hdinsight-apps-install-applications.md#install-applications-during-cluster-creation).


6. Kliknij przycisk **rozmiar klastra** do wyświetlania informacji o węzłach, które są używane dla tego klastra. Ustaw liczbę węzłów procesu roboczego, które są potrzebne dla klastra. Wyświetlane są również szacowany koszt uruchomionych w klastrze.
   
    ![Węzeł warstw cenowych](./media/hdinsight-hadoop-create-linux-cluster-portal/hdinsight-create-cluster-nodes.png "Określ liczbę węzłów klastra")
   
   > [!IMPORTANT]
   > Jeśli planowane jest więcej niż 32 węzłami procesów roboczych, podczas tworzenia klastra lub przez skalowanie klastra po utworzeniu, musi wybierz rozmiar węzła głównego z co najmniej 8 rdzeniami i 14 GB pamięci RAM.
   > 
   > Aby uzyskać więcej informacji na węzeł rozmiary i koszty, zobacz [cennik usługi HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).
   > 
   > 
   
   Kliknij przycisk **dalej** zapisać cenową konfiguracji węzła.

7. Kliknij przycisk **Zaawansowane ustawienia** skonfigurować inne opcjonalne ustawienia, takie jak przy użyciu **akcji skryptu** dostosować klastra, aby zainstalować składniki niestandardowe lub dołączenie **sieci wirtualnej**. Szukaj w poniższej tabeli, aby uzyskać więcej informacji.

    ![Węzeł warstw cenowych](./media/hdinsight-hadoop-create-linux-cluster-portal/hdinsight-create-cluster-advanced.png "Określ liczbę węzłów klastra")

    | Opcja | Opis |
    |--------|-------------|
    | **Akcje skryptu** | Użyj tej opcji, jeśli chcesz użyć niestandardowego skryptu do dostosowywania klastra, tworzenia klastra. Aby uzyskać więcej informacji na temat akcji skryptu, zobacz [HDInsight dostosować klastry za pomocą akcji skryptu](hdinsight-hadoop-customize-cluster-linux.md). |
    | **Virtual Network** | Wybierz sieć wirtualną platformy Azure i podsieć, jeśli chcesz umieścić w klastrze w sieci wirtualnej. Informacji dotyczących korzystania z usługi HDInsight z siecią wirtualną, łącznie z określonymi wymaganiami konfiguracji dla sieci wirtualnej, zobacz [możliwości rozszerzania HDInsight przy użyciu sieci wirtualnej platformy Azure](hdinsight-extend-hadoop-virtual-network.md). |

    Kliknij przycisk **Dalej**.

8. Aby uzyskać **Podsumowanie**, sprawdź wprowadzony wcześniej, a następnie kliknij przycisk **Utwórz**.

    ![Węzeł warstw cenowych](./media/hdinsight-hadoop-create-linux-cluster-portal/hdinsight-create-cluster-summary.png "Określ liczbę węzłów klastra")
    
    > [!NOTE]
    > Dopiero po pewnym czasie dla klastra, który ma zostać utworzony, zazwyczaj około 15 minut. Użyj kafelka na tablicy startowej, lub **powiadomienia** wpis po lewej stronie, aby sprawdzić w procesie inicjowania obsługi administracyjnej.
    > 
    > 
12. Po zakończeniu procesu tworzenia, kliknij Kafelek klastra z tablicy startowej. Okno klastra zawiera następujące informacje.
    
    ![Interfejs klastra](./media/hdinsight-hadoop-create-linux-cluster-portal/hdinsight-create-cluster-completed.png "właściwości klastra")
    
    Użyj następującego polecenia do zrozumienia ikony na początku.
    
    * **Omówienie** karta zawiera ważne informacje o klastrze, takich jak nazwa, należy do grupy zasobów, lokalizacji, system operacyjny, adres URL dla pulpitu nawigacyjnego w klastrze, itd.
    * **Pulpit nawigacyjny** kieruje użytkownika do portalu Ambari skojarzony z klastrem.
    * **Secure Shell**: informacje niezbędne do uzyskania dostępu do klastra przy użyciu protokołu SSH.
    * **Klaster w skali** pozwala zwiększyć liczbę węzłów procesu roboczego związane z klastrem.
    * **Usuń**: usuwa klastra usługi HDInsight.
    

## <a name="customize-clusters"></a>Dostosowywanie klastrów
* Zobacz [HDInsight dostosować klastry za pomocą początkowego](hdinsight-hadoop-customize-cluster-bootstrap.md).
* Zobacz [HDInsight dostosować klastry za pomocą akcji skryptu](hdinsight-hadoop-customize-cluster-linux.md).

## <a name="delete-the-cluster"></a>Usuwanie klastra
[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="troubleshoot"></a>Rozwiązywanie problemów

W razie problemów podczas tworzenia klastrów usługi HDInsight zapoznaj się z [wymaganiami dotyczącymi kontroli dostępu](hdinsight-administer-use-portal-linux.md#create-clusters).

## <a name="next-steps"></a>Kolejne kroki
Teraz, że pomyślnie utworzono klaster usługi HDInsight, aby dowiedzieć się, jak pracować z klastra należy użyć następującego:

### <a name="hadoop-clusters"></a>Klastry Hadoop
* [Korzystanie z programu Hive z usługą HDInsight](hadoop/hdinsight-use-hive.md)
* [Korzystanie z języka Pig z usługą HDInsight](hadoop/hdinsight-use-pig.md)
* [Korzystać z usługi MapReduce z usługą HDInsight](hadoop/hdinsight-use-mapreduce.md)

### <a name="hbase-clusters"></a>Klastrów HBase
* [Wprowadzenie do usługi HBase w usłudze HDInsight](hbase/apache-hbase-tutorial-get-started-linux.md)
* [Tworzenie aplikacji Java bazy danych hbase w usłudze HDInsight](hbase/apache-hbase-build-java-maven-linux.md)

### <a name="storm-clusters"></a>Klastry STORM
* [Tworzenie topologii Java dla Storm w usłudze HDInsight](storm/apache-storm-develop-java-topology.md)
* [Użyj składników języka Python w Storm w usłudze HDInsight](storm/apache-storm-develop-python-topology.md)
* [Wdrażanie i monitorowanie topologii z systemu Storm w usłudze HDInsight](storm/apache-storm-deploy-monitor-topology-linux.md)

### <a name="spark-clusters"></a>Klastry Spark
* [Tworzenie autonomicznych aplikacji przy użyciu języka Scala](spark/apache-spark-create-standalone-application.md)
* [Zdalne uruchamianie zadań w klastrze Spark przy użyciu programu Livy](spark/apache-spark-livy-rest-interface.md)
* [Platforma Spark i analiza biznesowa: interakcyjna analiza danych na platformie Spark w usłudze HDInsight z użyciem narzędzi do analizy biznesowej](spark/apache-spark-use-bi-tools.md)
* [Platforma Spark i usługa Machine Learning: korzystanie z platformy Spark w usłudze HDInsight do przewidywania wyników kontroli żywności](spark/apache-spark-machine-learning-mllib-ipython.md)

