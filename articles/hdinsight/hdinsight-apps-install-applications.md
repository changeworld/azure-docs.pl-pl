---
title: Instalowanie aplikacji innych firm w usłudze Azure HDInsight
description: Dowiedz się, jak instalować aplikacje platformy Hadoop innych firm w usłudze Azure HDInsight.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/17/2019
ms.author: hrasheed
ms.openlocfilehash: 9d25996db9c13a12781d369327f2b43a41fb4115
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/10/2019
ms.locfileid: "67786516"
---
# <a name="install-third-party-apache-hadoop-applications-on-azure-hdinsight"></a>Instalowanie aplikacji Apache Hadoop innych firm w usłudze Azure HDInsight

Informacje o instalowaniu innych firm [Apache Hadoop](https://hadoop.apache.org/) aplikacji w usłudze Azure HDInsight. Aby uzyskać instrukcje instalowania własnej aplikacji, zobacz [Install custom HDInsight applications](hdinsight-apps-install-custom-applications.md) (Instalowanie niestandardowych aplikacji usługi HDInsight).

Aplikacji HDInsight to aplikacja, którą użytkownicy mogą zainstalować w klastrze usługi HDInsight. Te aplikacje mogą być opracowane przez firmę Microsoft, niezależnych dostawców oprogramowania (ISV) lub samodzielnie.  

Na poniższej liście przedstawiono opublikowane aplikacje:

|Aplikacja |Typy klastrów | Opis |
|---|---|---|
|[Platforma analiz danych AtScale](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/atscale.atscale) |Hadoop |AtScale włącza klastra HDInsight na serwerze OLAP skalowalnego w poziomie, co pozwala na zapytanie miliardów wierszy danych interaktywnie przy użyciu narzędzi analizy Biznesowej już wiedzieć, właścicielem i lubianego z programu Microsoft Excel, Power BI, Tableau Software do QlikView. |
|[CDAP for HDInsight](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/cask.cdap-for-hdinsight) |HBase |CDAP jest pierwszą ujednoliconą platformę integracji dla danych big data skrócenie czasu wprowadzania wartości dla usługi Hadoop i umożliwiają IT w celu zapewnienia samoobsługi danych. Typu open source i rozszerzalnych, CDAP usuwa przeszkód dla innowacji. Wymagania: 4 węzły region, min D3, wersja 2. |
|[Datameer](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/datameer.datameer) |Hadoop |Firmy Datameer skalowalna platforma samoobsługowa do przygotowywania, eksplorowania i dotyczących danych na potrzeby analizy przyspiesza, przekształcanie złożonych z wielu źródeł danych w cenne informacje biznesowe, dostarczanie szczegółowych informacji szybciej i MĄDRZEJ na skalę przedsiębiorstwa. |
|[Dataiku DSS na HDInsight](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/dataiku.dss-on-hdi) |Hadoop, Spark |Dataiku DSS w platforma analizy danych przedsiębiorstwa, która umożliwia analitykom danych i analityków danych współpracują w celu projektowania i zwiększającym nowych danych produktów i usług, przekształcanie danych pierwotnych w prognozy skuteczny. |
|[Platforma WANdisco Fusion HDI aplikacji](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/wandisco.fusion-hdi-app) |Hadoop, Spark, HBase, Storm, Kafka |Przechowywanie danych w spójne w środowisku rozproszonym jest trudne operacje duża ilość danych. Platforma WANdisco Fusion platformy oprogramowania klasy korporacyjnej, rozwiązuje ten problem, włączając wyjaśnienie pojęcia spójności danych bez struktury w każdym środowisku. |
|[SparklingWater H2O for HDInsight](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/h2o-ai.h2o-sparklingwater) |platforma Spark |H2O Sparkling Water obsługuje następujące algorytmów rozproszonych: GLM Bayesa, rozproszonych Random lasu, gradientu zwiększania wyniku maszyny, głębokie sieci neuronowe uczenia głębokiego, K-średnich, analizie PCA, uogólniony modeli ranga niski, wykrywanie anomalii, Autoencoders. |
|[Striim Integracja danych w czasie rzeczywistym z usługą HDInsight](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/striim.hdinsightintegration) |Hadoop,HBase,Storm,Spark,Kafka |Striim (Wymowa: "strumień") jest end-to-end przesyłania strumieniowego integracji danych + platforma analiz danych, co pozwala ciągłego wprowadzania, przetwarzania i analizy danych z różnych strumieni. |
|[Jumbune Enterprise-Accelerating BigData Analytics](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/impetus-infotech-india-pvt-ltd.impetus_jumbune) |Hadoop, Spark |Na wysokim poziomie Jumbune pomaga przedsiębiorstwom o 1. Przyspieszanie Tez, aparat MapReduce i modułem Spark na podstawie gałęzi, Java, Scala wydajność obciążenia. 2. Proaktywne klastra usługi Hadoop, monitorowania i 3. Ustanawianie zarządzania dobrej jakości danych w rozproszonym systemie plików. |
|[Kyligence Enterprise](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/kyligence.kyligence) |Hadoop,HBase,Spark |Obsługiwane przez Apache Kylin, Kyligence Enterprise umożliwia analizy Biznesowej danych Big Data. Jako mechanizm OLAP enterprise na platformie Hadoop Kyligence Enterprise upoważnia Analityk biznesowy architektury analizy Biznesowej w usłudze Hadoop przy użyciu magazynu danych będące standardami branżowymi i metodologia Power BI. |
|[Serwer zadań Spark dla wykonawcy Spark KNIME](https://azuremarketplace.microsoft.com/marketplace/apps/knime.spark-job-server-for-knime-spark-executor) |platforma Spark |Serwer zadań Spark dla wykonawcy Spark KNIME umożliwia platforma analityczna KNIME nawiązać połączenie z klastrami HDInsight. |
|[Gwiazda Presto dla usługi Azure HDInsight](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/starburst.starburst-presto) |Hadoop |Presto jest szybka i skalowalna rozproszonych aparatu zapytań SQL. Zaprojektowana z myślą o separacji magazynu i mocy obliczeniowej, Presto to idealne rozwiązanie dla wykonywanie zapytań o dane w usługi Azure Data Lake Storage, bazy danych usługi Azure Blob Storage, SQL i NoSQL oraz innych źródeł danych. |
|[StreamSets Data Collector for HDInsight chmury](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/streamsets.streamsets-data-collector-hdinsight) |Hadoop,HBase,Spark,Kafka |StreamSets Data Collector to lekki, zaawansowany aparat strumieni danych w czasie rzeczywistym. Użyj modułu zbierającego dane, aby trasy i przetwarzać dane w strumienie danych. Chodzi o 30-dniowej licencji próbnej. |
|[Trifacta Wrangler przedsiębiorstwa](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/trifacta.tr01) |Hadoop, Spark, HBase |Trifacta Wrangler Enterprise for HDInsight obsługuje dane korporacyjne inteligencji dla każdej skali danych. Koszt uruchamiania Trifacta na platformie Azure jest kombinacją Trifacta koszty subskrypcji oraz koszty infrastruktury platformy Azure dla maszyn wirtualnych. |
|[Platforma danych Unifi](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/unifi-software.unifi-data-catalog) |Hadoop,HBase,Storm,Spark |Platforma danych Unifi jest bezproblemowo zintegrowany pakiet narzędzi samoobsługi danych mających na celu zwiększenie możliwości dostępnych użytkownika biznesowego, czoła wyzwaniom danych dla tego dysku przychodów, zmniejszyć koszty i złożoność operacyjną. |
|[Unraveldata APM](https://azuremarketplace.microsoft.com/marketplace/apps/unravel-data.unravel-app) |platforma Spark |Unravel aplikacji do obsługi danych dla klastra HDInsight Spark. |
|[Wykaz danych oparte na sztucznej Inteligencji wodnej](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/waterline_data.waterline_data) |platforma Spark |Wodnej Skatalogowano organizuje i kontroluje danych za pomocą sztucznej Inteligencji do danych tagu automatycznie za pomocą terminów biznesowych. Wykaz posługiwać się biznesowych firmy wodnej jest składnikiem Powodzenie krytycznych, samoobsługowej analizy, zgodności i nadzoru i w celu zarządzania. |

W instrukcjach podanych w tym artykule jest używana witryna Azure Portal. Można również wyeksportować szablon usługi Azure Resource Manager z portalu lub uzyskać kopię szablonu usługi Resource Manager od dostawców i użyj programu Azure PowerShell i klasycznego wiersza polecenia platformy Azure, aby wdrożyć szablon.  Zobacz [tworzenie technologii Apache Hadoop klastrów HDInsight za pomocą szablonów usługi Resource Manager](hdinsight-hadoop-create-linux-clusters-arm-templates.md).

## <a name="prerequisites"></a>Wymagania wstępne
Jeśli chcesz zainstalować aplikacje usługi HDInsight w istniejącym klastrze usługi HDInsight, musisz dysponować klastrem usługi HDInsight. Aby go utworzyć, zobacz artykuł [Tworzenie klastrów](hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster). Możesz także zainstalować aplikacje usługi HDInsight podczas tworzenia klastra usługi HDInsight.

## <a name="install-applications-to-existing-clusters"></a>Instalowanie aplikacji w istniejących klastrach
Poniższa procedura pokazuje sposób instalacji aplikacji usługi HDInsight w istniejącym klastrze usługi HDInsight.

**Instalowanie aplikacji usługi HDInsight**

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. W menu po lewej stronie przejdź do **wszystkich usług** > **Analytics** > **klastry HDInsight**.
3. Wybierz klaster usługi HDInsight z listy.  Jeśli nie masz klastra, musisz go najpierw utworzyć.  Zobacz [Tworzenie klastrów](hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster).
4. W obszarze **ustawienia** kategorii, wybierz opcję **aplikacji**. Można wyświetlić listę zainstalowanych aplikacji w głównym oknie. 
   
    ![Aplikacje usługi HDInsight — menu portalu](./media/hdinsight-apps-install-applications/hdinsight-apps-portal-menu.png)
5. Wybierz **+ Dodaj** z menu. Można wyświetlić listę dostępnych aplikacji.  Jeśli **+ Dodaj** jest nieaktywna, czy oznacza, że żadne aplikacje dla tej wersji klastra HDInsight.
   
    ![Aplikacje usługi HDInsight — dostępne aplikacje](./media/hdinsight-apps-install-applications/hdinsight-apps-list.png)
6. Wybierz jedną z dostępnych aplikacji, a następnie postępuj zgodnie z instrukcjami, aby zaakceptować warunki prawne.

Można zobaczyć stan instalacji w powiadomieniach portalu (Wybierz ikonę dzwonka w górnej części portalu). Po zainstalowaniu aplikacji, aplikacja zostanie wyświetlona na liście zainstalowane aplikacje.

## <a name="install-applications-during-cluster-creation"></a>Instalowanie aplikacji podczas tworzenia klastra
Aplikacje usługi HDInsight możesz także zainstalować podczas tworzenia klastra. W trakcie procesu aplikacje usługi HDInsight są instalowane po utworzeniu i uruchomieniu klastra. Aby zainstalować aplikacje, podczas tworzenia klastra przy użyciu witryny Azure portal, należy użyć **niestandardowe** opcji zamiast domyślnego **szybkie tworzenie** opcji.

## <a name="list-installed-hdinsight-apps-and-properties"></a>Lista zainstalowanych aplikacji HDInsight i ich właściwości
Portal zawiera listę zainstalowanych aplikacji usługi HDInsight dla klastra oraz właściwości poszczególnych zainstalowanych aplikacji.

**Listę aplikacji HDInsight i wyświetlić właściwości**

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. W menu po lewej stronie przejdź do **wszystkich usług** > **Analytics** > **klastry HDInsight**.
3. Wybierz klaster usługi HDInsight z listy.
4. W obszarze **ustawienia** kategorii, wybierz opcję **aplikacji**. Można wyświetlić listę zainstalowanych aplikacji w głównym oknie. 
   
    ![Aplikacje usługi HDInsight — zainstalowane aplikacje](./media/hdinsight-apps-install-applications/hdinsight-apps-installed-apps-with-apps.png)
5. Wybierz jedną z zainstalowanych aplikacji, aby wyświetlić właściwości. Listy właściwości:

    |Właściwość | Opis |
    |---|---|
    |Nazwa aplikacji |Nazwa aplikacji. |
    |Stan |Stan aplikacji. |
    |Strony sieci Web |Adres URL aplikacji sieci web wdrożoną w węźle brzegowym. Poświadczenie jest identyczne z poświadczeniami użytkownika protokołu HTTP skonfigurowanymi dla klastra. |
    |Punkt końcowy SSH |Za używanie protokołu SSH Połącz się z węzłem krawędzi. Poświadczenia protokołu SSH są identyczne z poświadczeniami użytkownika protokołu SSH skonfigurowanymi dla klastra. Aby uzyskać informacje, zobacz [Używanie protokołu SSH w usłudze HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md). |
    |Opis | Opis aplikacji. |

6. Aby usunąć aplikację, kliknij prawym przyciskiem myszy aplikację, a następnie kliknij przycisk **Usuń** z menu kontekstowego.

## <a name="connect-to-the-edge-node"></a>Łączenie z węzłem krawędzi
Z węzłem krawędzi możesz połączyć się przy użyciu protokołów HTTP i SSH. Informacje o punkcie końcowym można znaleźć w [portalu](#list-installed-hdinsight-apps-and-properties). Aby uzyskać informacje, zobacz [Używanie protokołu SSH w usłudze HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

Poświadczenia punktu końcowego protokołu HTTP to poświadczenia użytkownika protokołu HTTP skonfigurowane dla klastra usługi HDInsight. Poświadczenia punktu końcowego protokołu SSH to poświadczenia SSH skonfigurowane dla klastra usługi HDInsight.

## <a name="troubleshoot"></a>Rozwiązywanie problemów
Zobacz [Troubleshoot the installation](hdinsight-apps-install-custom-applications.md#troubleshoot-the-installation) (Rozwiązywanie problemów z instalacją).

## <a name="next-steps"></a>Kolejne kroki
* [Instalowanie niestandardowych aplikacji HDInsight](hdinsight-apps-install-custom-applications.md): Dowiedz się, jak wdrożyć nieopublikowane aplikację HDInsight w usłudze HDInsight.
* [Publikowanie aplikacji HDInsight](hdinsight-apps-publish-applications.md): Dowiedz się, jak opublikować niestandardowe aplikacje HDInsight w portalu Azure Marketplace.
* [MSDN: Instalowanie aplikacji usługi HDInsight](https://msdn.microsoft.com/library/mt706515.aspx): Dowiedz się, jak zdefiniować aplikacje HDInsight.
* [Dostosowywanie klastrów usługi HDInsight opartych na systemie Linux przy użyciu akcji skryptu](hdinsight-hadoop-customize-cluster-linux.md): dowiedz się, jak instalować dodatkowe aplikacje za pomocą akcji skryptu.
* [Tworzenie klastrów opartych na systemie Linux Apache Hadoop w HDInsight przy użyciu szablonów usługi Resource Manager](hdinsight-hadoop-create-linux-clusters-arm-templates.md): Dowiedz się, jak wywoływać szablony usługi Resource Manager w celu tworzenia klastrów HDInsight.
* [Use empty edge nodes in HDInsight](hdinsight-apps-use-edge-node.md) (Używanie pustych węzłów krawędzi w usłudze HDInsight): dowiedz się, jak za pomocą pustego węzła krawędzi uzyskiwać dostęp do klastra usługi HDInsight oraz testować i hostować aplikacje usługi HDInsight.

