---
title: Instalowanie aplikacji innych firm w usłudze Azure HDInsight
description: Dowiedz się, jak instalować aplikacje Apache Hadoop innych firm w usłudze Azure HDInsight.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/17/2019
ms.author: hrasheed
ms.openlocfilehash: 8ed854a5775dfc20e258c14de51fe2066fa6caad
ms.sourcegitcommit: fa4852cca8644b14ce935674861363613cf4bfdf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/09/2019
ms.locfileid: "70810277"
---
# <a name="install-third-party-apache-hadoop-applications-on-azure-hdinsight"></a>Instalowanie aplikacji Apache Hadoop innych firm w usłudze Azure HDInsight

Informacje o instalowaniu innych firm [Apache Hadoop](https://hadoop.apache.org/) aplikacji w usłudze Azure HDInsight. Aby uzyskać instrukcje instalowania własnej aplikacji, zobacz [Install custom HDInsight applications](hdinsight-apps-install-custom-applications.md) (Instalowanie niestandardowych aplikacji usługi HDInsight).

Aplikacji HDInsight to aplikacja, którą użytkownicy mogą zainstalować w klastrze usługi HDInsight. Te aplikacje mogą być opracowane przez firmę Microsoft, niezależnych dostawców oprogramowania (ISV) lub samodzielnie.  

Na poniższej liście przedstawiono opublikowane aplikacje:

|Aplikacja |Typy klastrów | Opis |
|---|---|---|
|[Platforma analizy AtScale](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/atscale.atscale) |Hadoop |AtScale przekształca klaster usługi HDInsight w skalowalny w poziomie serwer OLAP, co umożliwia interakcyjne wykonywanie zapytań o miliardy wierszy danych przy użyciu narzędzi analizy biznesowej, które już znasz, własnych i miłości — od programu Microsoft Excel, Power BI, Tableau Software to QlikView. |
|[CDAP dla usługi HDInsight](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/cask.cdap-for-hdinsight) |HBase |CDAP to pierwsza ujednolicona platforma integracji dla danych Big Data, która skraca czas do wartości dla usługi Hadoop i umożliwia udostępnienie danych samoobsługi. Polecenie Open Source i rozszerzalne, CDAP usuwa przeszkody dla innowacji. Wymagania: cztery regiony regionu, minimum D3 v2. |
|[Datameer](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/datameer.datameer) |Hadoop |Samoobsługowa platforma Datameer na potrzeby przygotowywania, eksplorowania i zarządzania danymi na potrzeby analiz przyspiesza przekształcanie złożonych danych z wielu źródeł w cenne informacje dostępne dla działalności firmy, dostarczając szybszy, inteligentniejszy wgląd w skalę przedsiębiorstwa. |
|[Dataiku DSS w usłudze HDInsight](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/dataiku.dss-on-hdi) |Hadoop, Spark |Dataiku DSS na platformie do nauki o danych w przedsiębiorstwie, która umożliwia analitykom danych i specjalistom ds. danych współpracę w celu projektowania i uruchamiania nowych produktów i usług danych w bardziej wydajny sposób, dzięki czemu nieprzetworzone dane mają wpływ na przewidywania. |
|[Aplikacja WANdisco Fusion HDI](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/wandisco.fusion-hdi-app) |Hadoop, Spark, HBase, burza, Kafka |Utrzymywanie spójnych danych w środowisku rozproszonym jest wyzwaniem wielu operacji na danych. WANdisco Fusion, platforma oprogramowania klasy korporacyjnej, rozwiązuje ten problem przez włączenie niestrukturalnej spójności danych w dowolnym środowisku. |
|[H2O SparklingWater dla usługi HDInsight](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/h2o-ai.h2o-sparklingwater) |platforma Spark |W przypadku wody musującej z obsługą następujących algorytmów rozproszonych: GLM, algorytmie Bayesa, rozproszony Las losowy, komputer do zwiększania gradientu, głębokie Neuronowyche sieci, uczenie głębokie, K-oznacza, PPW, uogólnione modele o niskiej rangi, wykrywanie anomalii, autokodery. |
|[Striim do integracji danych w czasie rzeczywistym z usługą HDInsight](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/striim.hdinsightintegration) |Hadoop, HBase, burza, Spark, Kafka |Striim (wymawiane "Stream") to kompleksowa integracja danych przesyłania strumieniowego i platforma analizy, umożliwiająca ciągłe pozyskiwanie, przetwarzanie i analizowanie różnorodnych strumieni danych. |
|[Jumbune analizy BigData w przedsiębiorstwie](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/impetus-infotech-india-pvt-ltd.impetus_jumbune) |Hadoop, Spark |Na wysokim poziomie Jumbune wspiera przedsiębiorstwa przez, 1. Przyspieszenie tez, & MapReduce, oparty na aparacie Spark, środowisko Java, Scala wydajność obciążeń. 2. Proaktywne monitorowanie klastra Hadoop, 3. Ustanawianie zarządzania jakością danych w rozproszonym systemie plików. |
|[Kyligence Enterprise](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/kyligence.kyligence) |Hadoop,HBase,Spark |Obsługiwane przez Apache Kylin, usługa Kyligence Enterprise umożliwia korzystanie z analizy danych Big Data. Jako aparat OLAP przedsiębiorstwa na platformie Hadoop firma Kyligence Enterprise umożliwia analitykom biznesowym tworzenie architektów w usłudze Hadoop dzięki standardowym branżom danych i metodologiom analizy biznesowej. |
|[Gwiazda Presto dla usługi Azure HDInsight](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/starburst.starburst-presto) |Hadoop |Presto to szybki i skalowalny, rozproszony aparat zapytań SQL. Zaprojektowana z myślą o separacji magazynu i mocy obliczeniowej, Presto to idealne rozwiązanie dla wykonywanie zapytań o dane w usługi Azure Data Lake Storage, bazy danych usługi Azure Blob Storage, SQL i NoSQL oraz innych źródeł danych. |
|[Moduł zbierający dane StreamSets dla chmury usługi HDInsight](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/streamsets.streamsets-data-collector-hdinsight) |Hadoop,HBase,Spark,Kafka |Moduł zbierający dane StreamSets to lekki, zaawansowany aparat, który przesyła strumieniowo dane w czasie rzeczywistym. Użyj modułu zbierającego dane, aby kierować i przetwarzać dane w strumieniach danych. Jest on dostarczany z 30-dniową licencją próbną. |
|[Trifacta Wrangler Enterprise](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/trifacta.tr01) |Hadoop, Spark, HBase |Usługa Trifacta Wrangler Enterprise for HDInsight obsługuje dane korporacyjne dla całego przedsiębiorstwa w przypadku dowolnej skali danych. Kosztem korzystania z Trifacta na platformie Azure jest kombinacja kosztów subskrypcji Trifacta plus koszty infrastruktury platformy Azure dla maszyn wirtualnych. |
|[Platforma danych Unifi](https://unifisoftware.com/platform/) |Hadoop, HBase, burza, Spark |Platforma danych Unifi to bezproblemowo zintegrowany pakiet narzędzi do samodzielnej obsługi danych, który umożliwia użytkownikom biznesowym sprostanie wyzwaniom związanym z danymi, które zwiększają zyski, obniżają koszty lub złożoność operacyjną. |
|[Unraveldata APM](https://azuremarketplace.microsoft.com/marketplace/apps/unravel-data.unravel-app) |platforma Spark |Aplikacja danych Unravel dla klastra usługi HDInsight Spark. |
|[Data Catalogy z linią wodną](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/waterline_data.waterline_data) |platforma Spark |Wykazy, organizują i zarządza danymi przy użyciu AI do autotagów danych z warunkami biznesowymi. Wykaz posługiwać się biznesowych firmy wodnej jest składnikiem Powodzenie krytycznych, samoobsługowej analizy, zgodności i nadzoru i w celu zarządzania. |

W instrukcjach podanych w tym artykule jest używana witryna Azure Portal. Można również wyeksportować szablon usługi Azure Resource Manager z portalu lub uzyskać kopię szablonu usługi Resource Manager od dostawców i użyj programu Azure PowerShell i klasycznego wiersza polecenia platformy Azure, aby wdrożyć szablon.  Zobacz [tworzenie technologii Apache Hadoop klastrów HDInsight za pomocą szablonów usługi Resource Manager](hdinsight-hadoop-create-linux-clusters-arm-templates.md).

## <a name="prerequisites"></a>Wymagania wstępne
Jeśli chcesz zainstalować aplikacje usługi HDInsight w istniejącym klastrze usługi HDInsight, musisz dysponować klastrem usługi HDInsight. Aby go utworzyć, zobacz artykuł [Tworzenie klastrów](hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster). Możesz także zainstalować aplikacje usługi HDInsight podczas tworzenia klastra usługi HDInsight.

## <a name="install-applications-to-existing-clusters"></a>Instalowanie aplikacji w istniejących klastrach
Poniższa procedura pokazuje sposób instalacji aplikacji usługi HDInsight w istniejącym klastrze usługi HDInsight.

**Instalowanie aplikacji usługi HDInsight**

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Z menu po lewej stronie przejdź do strony **wszystkie usługi** > **analizy na platformie** > **HDInsight**.
3. Wybierz klaster usługi HDInsight z listy.  Jeśli nie masz klastra, musisz go najpierw utworzyć.  Zobacz [Tworzenie klastrów](hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster).
4. W kategorii **Ustawienia** wybierz pozycję **aplikacje**. W oknie głównym można zobaczyć listę zainstalowanych aplikacji. 
   
    ![Aplikacje usługi HDInsight — menu portalu](./media/hdinsight-apps-install-applications/hdinsight-apps-portal-menu.png)
5. Wybierz pozycję **+ Dodaj** z menu. Zostanie wyświetlona lista dostępnych aplikacji.  Jeśli **dodatek + Add** jest wyszarzony, oznacza to, że nie ma żadnych aplikacji dla tej wersji klastra usługi HDInsight.
   
    ![Aplikacje usługi HDInsight — dostępne aplikacje](./media/hdinsight-apps-install-applications/hdinsight-apps-list.png)
6. Wybierz jedną z dostępnych aplikacji, a następnie postępuj zgodnie z instrukcjami, aby zaakceptować postanowienia prawne.

Możesz zobaczyć stan instalacji w powiadomieniach portalu (wybierz ikonę dzwonka w górnej części portalu). Po zainstalowaniu aplikacji, aplikacja zostanie wyświetlona na liście zainstalowane aplikacje.

## <a name="install-applications-during-cluster-creation"></a>Instalowanie aplikacji podczas tworzenia klastra
Aplikacje usługi HDInsight możesz także zainstalować podczas tworzenia klastra. W trakcie procesu aplikacje usługi HDInsight są instalowane po utworzeniu i uruchomieniu klastra. Aby zainstalować aplikacje podczas tworzenia klastra przy użyciu Azure Portal, użyj opcji **niestandardowej** zamiast domyślnej opcji **szybkie tworzenie** .

## <a name="list-installed-hdinsight-apps-and-properties"></a>Lista zainstalowanych aplikacji HDInsight i ich właściwości
Portal zawiera listę zainstalowanych aplikacji usługi HDInsight dla klastra oraz właściwości poszczególnych zainstalowanych aplikacji.

**Listę aplikacji HDInsight i wyświetlić właściwości**

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Z menu po lewej stronie przejdź do strony **wszystkie usługi** > **analizy na platformie** > **HDInsight**.
3. Wybierz klaster usługi HDInsight z listy.
4. W kategorii **Ustawienia** wybierz pozycję **aplikacje**. W oknie głównym można zobaczyć listę zainstalowanych aplikacji. 
   
    ![Aplikacje usługi HDInsight — zainstalowane aplikacje](./media/hdinsight-apps-install-applications/hdinsight-apps-installed-apps-with-apps.png)
5. Wybierz jedną z zainstalowanych aplikacji, aby wyświetlić właściwość. Listy właściwości:

    |Właściwość | Opis |
    |---|---|
    |Nazwa aplikacji |Nazwa aplikacji. |
    |State |Stan aplikacji. |
    |Strona sieci Web |Adres URL aplikacji sieci Web wdrożonej w węźle brzegowym. Poświadczenie jest identyczne z poświadczeniami użytkownika protokołu HTTP skonfigurowanymi dla klastra. |
    |Punkt końcowy SSH |Do łączenia się z węzłem brzegowym można użyć protokołu SSH. Poświadczenia protokołu SSH są identyczne z poświadczeniami użytkownika protokołu SSH skonfigurowanymi dla klastra. Aby uzyskać informacje, zobacz [Używanie protokołu SSH w usłudze HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md). |
    |Opis | Opis aplikacji. |

6. Aby usunąć aplikację, kliknij prawym przyciskiem myszy aplikację, a następnie kliknij przycisk **Usuń** z menu kontekstowego.

## <a name="connect-to-the-edge-node"></a>Łączenie z węzłem krawędzi
Z węzłem krawędzi możesz połączyć się przy użyciu protokołów HTTP i SSH. Informacje o punkcie końcowym można znaleźć w [portalu](#list-installed-hdinsight-apps-and-properties). Aby uzyskać informacje, zobacz [Używanie protokołu SSH w usłudze HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

Poświadczenia punktu końcowego protokołu HTTP to poświadczenia użytkownika protokołu HTTP skonfigurowane dla klastra usługi HDInsight. Poświadczenia punktu końcowego protokołu SSH to poświadczenia SSH skonfigurowane dla klastra usługi HDInsight.

## <a name="troubleshoot"></a>Rozwiązywanie problemów
Zobacz [Troubleshoot the installation](hdinsight-apps-install-custom-applications.md#troubleshoot-the-installation) (Rozwiązywanie problemów z instalacją).

## <a name="next-steps"></a>Kolejne kroki
* [Instalowanie niestandardowych aplikacji HDInsight](hdinsight-apps-install-custom-applications.md): Dowiedz się, jak wdrożyć nieopublikowane aplikację HDInsight w usłudze HDInsight.
* [Publikowanie aplikacji usługi HDInsight](hdinsight-apps-publish-applications.md): Dowiedz się, jak publikować niestandardowe aplikacje usługi HDInsight w portalu Azure Marketplace.
* [MSDN: Zainstaluj aplikację](https://msdn.microsoft.com/library/mt706515.aspx)usługi HDInsight: Dowiedz się, jak definiować aplikacje usługi HDInsight.
* [Dostosowywanie klastrów usługi HDInsight opartych na systemie Linux przy użyciu akcji skryptu](hdinsight-hadoop-customize-cluster-linux.md): dowiedz się, jak instalować dodatkowe aplikacje za pomocą akcji skryptu.
* [Tworzenie klastrów opartych na systemie Linux Apache Hadoop w HDInsight przy użyciu szablonów usługi Resource Manager](hdinsight-hadoop-create-linux-clusters-arm-templates.md): Dowiedz się, jak wywoływać szablony usługi Resource Manager w celu tworzenia klastrów HDInsight.
* [Use empty edge nodes in HDInsight](hdinsight-apps-use-edge-node.md) (Używanie pustych węzłów krawędzi w usłudze HDInsight): dowiedz się, jak za pomocą pustego węzła krawędzi uzyskiwać dostęp do klastra usługi HDInsight oraz testować i hostować aplikacje usługi HDInsight.

