---
title: Instalowanie aplikacji innych firm w usłudze Azure HDInsight
description: Dowiedz się, jak zainstalować aplikacje Apache Hadoop innej firmy na platformie Azure HDInsight.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/17/2019
ms.author: hrasheed
ms.openlocfilehash: 22d27d50a7d2c304e5d1a04a9a5eaa03d2f0bfa6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79366346"
---
# <a name="install-third-party-apache-hadoop-applications-on-azure-hdinsight"></a>Instalowanie aplikacji Apache Hadoop innych firm w usłudze Azure HDInsight

Dowiedz się, jak zainstalować aplikację [Apache Hadoop](https://hadoop.apache.org/) innej firmy na platformie Azure HDInsight. Aby uzyskać instrukcje instalowania własnej aplikacji, zobacz [Install custom HDInsight applications](hdinsight-apps-install-custom-applications.md) (Instalowanie niestandardowych aplikacji usługi HDInsight).

Aplikacja HDInsight to aplikacja, którą użytkownicy mogą zainstalować w klastrze HDInsight. Te aplikacje mogą być opracowane przez firmę Microsoft, niezależnych dostawców oprogramowania (ISV) lub samodzielnie.  

Na poniższej liście przedstawiono opublikowane aplikacje:

|Aplikacja |Typy klastrów | Opis |
|---|---|---|
|[Platforma wywiadowcza AtScale](https://azuremarketplace.microsoft.com/marketplace/apps/atscaleinc.atscale) |Hadoop |AtScale zamienia klaster HDInsight w skalowalny w poziomie serwer OLAP, umożliwiając interaktywne wykonywanie zapytań o miliardy wierszy danych przy użyciu narzędzi analizy biznesowej, które już znasz, posiadasz i kochasz — od programu Microsoft Excel, Power BI, Tableau Software do QlikView. |
|[CDAP dla HDInsight](https://azuremarketplace.microsoft.com/marketplace/apps/cask.cdap-for-hdinsight) |HBase |CDAP jest pierwszą zunifikowaną platformą integracyjną dla dużych zbiorów danych, która skraca czas do wartości dla Hadoop i umożliwia IT dostarczanie danych samoobsługowych. CdAP open source i rozszerzalne usuwa bariery dla innowacji. Wymagania: 4 węzły regionu, min D3 v2. |
|[Datameer (właso)](https://azuremarketplace.microsoft.com/marketplace/apps/datameer.datameer) |Hadoop |Samoobsługowa skalowalna platforma datameer do przygotowywania, eksplorowania i zarządzania danymi do celów analitycznych przyspiesza przekształcanie złożonych danych wieloźródłowych w cenne informacje gotowe do pracy w biznesie, zapewniając szybsze i inteligentniejsze informacje w skali przedsiębiorstwa. |
|[Dataiku DSS na HDInsight](https://azuremarketplace.microsoft.com/marketplace/apps/dataiku.dss-on-hdi) |Hadoop, Iskra |Dataiku DSS na platformie do nauki o danych w przedsiębiorstwie, która umożliwia analitykom danych i analitykom danych współpracę w celu efektywniejszego projektowania i uruchamiania nowych produktów i usług danych, przekształcając surowe dane w skuteczne prognozy. |
|[Aplikacja WANdisco Fusion HDI](https://azuremarketplace.microsoft.com/marketplace/apps/wandisco.fusion-hdi-app) |Hadoop, Iskra, HBase, Burza, Kafka |Utrzymywanie danych w spójnym środowisku rozproszonym jest ogromnym wyzwaniem w zakresie operacji danych. WANdisco Fusion, platforma oprogramowania klasy korporacyjnej, rozwiązuje ten problem, umożliwiając spójność danych bez struktury w dowolnym środowisku. |
|[H2O SparklingWater dla HDInsight](https://azuremarketplace.microsoft.com/marketplace/apps/h2o-ai.h2o-sparklingwater) |platforma Spark |H2O Sparkling Water obsługuje następujące algorytmy rozproszone: GLM, Naiwne Zatoki, Rozproszony Losowy Las, Gradient Boosting Machine, Głębokie sieci neuronowe, Głębokie uczenie się, K-środki, PCA, Uogólnione modele niskiej rangi, wykrywanie anomalii, autoenkodery. |
|[Striim do integracji danych w czasie rzeczywistym z HDInsight](https://azuremarketplace.microsoft.com/marketplace/apps/striim.hdinsightintegration) |Hadoop,HBase,Burza,Iskra,Kafka |Striim (wymawiane "strumień") to kompleksowa integracja danych strumieniowych + platforma analizy, umożliwiająca ciągłe pozyskiwania, przetwarzania i analizy różnych strumieni danych. |
|[Jumbune Enterprise-Przyspieszenie BigData Analytics](https://azuremarketplace.microsoft.com/marketplace/apps/impetus-infotech-india-pvt-ltd.impetus_jumbune) |Hadoop, Iskra |Na wysokim poziomie, Jumbune pomaga przedsiębiorstwom przez, 1. Przyspieszenie Tez, MapReduce & wydajność pracy opartej na silniku Spark, Java, Scala. 2. Proaktywne monitorowanie klastra Hadoop, 3. Ustanawianie zarządzania jakością danych w rozproszonym systemie plików. |
|[Kyligence Enterprise](https://azuremarketplace.microsoft.com/marketplace/apps/kyligence.kyligence) |Hadoop,HBase,Iskra |Zasilany przez Apache Kylin, Kyligence Enterprise umożliwia BI na Big Data. Jako silnik OLAP przedsiębiorstwa na Hadoop, Kyligence Enterprise upoważnia analityka biznesowego do architekta BI na Hadoop ze standardowym w branży hurtownią danych i metodologią bi. |
|[Starburst Presto dla usługi Azure HDInsight](https://azuremarketplace.microsoft.com/marketplace/apps/starburst.starburst-presto) |Hadoop |Presto to szybki i skalowalny rozproszony aparat zapytań SQL. Zaprojektowany do separacji magazynu i obliczeń, Presto jest idealny do wykonywania zapytań o dane w usłudze Azure Data Lake Storage, usłudze Azure Blob Storage, bazach danych SQL i NoSQL oraz innych źródłach danych. |
|[Moduł zbierający dane StreamSets dla usługi HDInsight Cloud](https://azuremarketplace.microsoft.com/marketplace/apps/streamsets.streamsets-data-collector-hdinsight) |Hadoop,HBase,Iskra,Kafka |StreamSets Data Collector to lekki, wydajny aparat, który przesyła dane strumieniowo w czasie rzeczywistym. Użyj modułu zbierającego dane do rozsyłania i przetwarzania danych w strumieniach danych. Pochodzi z 30-dniową licencją próbną. |
|[Trifacta Wrangler Enterprise](https://azuremarketplace.microsoft.com/marketplace/apps/trifacta.tr01) |Hadoop, Iskra, HBase |Trifacta Wrangler Enterprise for HDInsight obsługuje w całym przedsiębiorstwie wrangling danych dla dowolnej skali danych. Koszt uruchomienia trifacta na platformie Azure jest kombinacją kosztów subskrypcji Trifacta plus koszty infrastruktury platformy Azure dla maszyn wirtualnych. |
|[Zunifikowana platforma danych](https://unifisoftware.com/platform/) |Hadoop,HBase,Burza,Iskra |Unifi Data Platform to bezproblemowo zintegrowany zestaw samoobsługowych narzędzi do transmisji danych, zaprojektowany w celu umożliwienia użytkownikowi biznesowemu sprostania wyzwaniom związanym z danymi, które napędzają przyrostowe przychody, obniżają koszty lub złożoność operacyjną. |
|[Unraveldata APM](https://azuremarketplace.microsoft.com/marketplace/apps/unravel-data.unravel-app) |platforma Spark |Rozwikłać dane aplikacji dla klastra HDInsight Spark. |
|[Katalog danych oparty na AI linii wodnej](https://azuremarketplace.microsoft.com/marketplace/apps/waterline_data.waterline_data) |platforma Spark |Linia brzegowa kataloguje, organizuje i reguluje dane za pomocą ai do automatycznego oznaczania danych za pomocą warunków biznesowych. Katalog literatów biznesowych Waterline jest kluczowym elementem sukcesu w zakresie samoobsługowej analizy, zgodności i zarządzania oraz inicjatyw w zakresie zarządzania IT. |

W instrukcjach podanych w tym artykule jest używana witryna Azure Portal. Można również wyeksportować szablon Usługi Azure Resource Manager z portalu lub uzyskać kopię szablonu Usługi Resource Manager od dostawców i użyć narzędzia Azure PowerShell i klasycznego interfejsu wiersza polecenia platformy Azure do wdrożenia szablonu.  Zobacz [Tworzenie klastrów Apache Hadoop w programie HDInsight przy użyciu szablonów Menedżera zasobów](hdinsight-hadoop-create-linux-clusters-arm-templates.md).

## <a name="prerequisites"></a>Wymagania wstępne
Jeśli chcesz zainstalować aplikacje usługi HDInsight w istniejącym klastrze usługi HDInsight, musisz dysponować klastrem usługi HDInsight. Aby go utworzyć, zobacz artykuł [Tworzenie klastrów](hadoop/apache-hadoop-linux-tutorial-get-started.md). Możesz także zainstalować aplikacje usługi HDInsight podczas tworzenia klastra usługi HDInsight.

## <a name="install-applications-to-existing-clusters"></a>Instalowanie aplikacji w istniejących klastrach
Poniższa procedura pokazuje sposób instalacji aplikacji usługi HDInsight w istniejącym klastrze usługi HDInsight.

**Instalowanie aplikacji HDInsight**

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).
2. W menu po lewej stronie przejdź do **pozycji Wszystkie usługi** > **Analytics** > **HDInsight klastrów**.
3. Wybierz klaster HDInsight z listy.  Jeśli nie masz klastra, musisz go najpierw utworzyć.  Zobacz [Tworzenie klastrów](hadoop/apache-hadoop-linux-tutorial-get-started.md).
4. W kategorii **Ustawienia** wybierz pozycję **Aplikacje**. W oknie głównym można wyświetlić listę zainstalowanych aplikacji. 
   
    ![Aplikacje usługi HDInsight — menu portalu](./media/hdinsight-apps-install-applications/hdinsight-apps-portal-menu.png)
5. Wybierz **+Dodaj** z menu. Możesz wyświetlić listę dostępnych aplikacji.  Jeśli **+Add** jest wyszarzona, oznacza to, że nie ma żadnych aplikacji dla tej wersji klastra HDInsight.
   
    ![Aplikacje usługi HDInsight — dostępne aplikacje](./media/hdinsight-apps-install-applications/hdinsight-apps-list1.png)
6. Wybierz jedną z dostępnych aplikacji, a następnie postępuj zgodnie z instrukcjami, aby zaakceptować warunki prawne.

Stan instalacji można zobaczyć w powiadomieniach portalu (wybierz ikonę dzwonka w górnej części portalu). Po zainstalowaniu aplikacji aplikacja pojawi się na liście Zainstalowane aplikacje.

## <a name="install-applications-during-cluster-creation"></a>Instalowanie aplikacji podczas tworzenia klastra

Aplikacje usługi HDInsight możesz także zainstalować podczas tworzenia klastra. W trakcie procesu aplikacje usługi HDInsight są instalowane po utworzeniu i uruchomieniu klastra. Aby zainstalować aplikacje podczas tworzenia klastra za pomocą portalu Azure, na karcie **Konfiguracja + cennik** wybierz + **Dodaj aplikację**.

![Aplikacje konfiguracji klastra portalu Azure](./media/hdinsight-apps-install-applications/azure-portal-cluster-configuration-applications.png)

## <a name="list-installed-hdinsight-apps-and-properties"></a>Lista zainstalowanych aplikacji HDInsight i ich właściwości
Portal zawiera listę zainstalowanych aplikacji usługi HDInsight dla klastra oraz właściwości poszczególnych zainstalowanych aplikacji.

**Wyświetlanie właściwości aplikacji i wyświetlania HDInsight**

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).
2. W menu po lewej stronie przejdź do **pozycji Wszystkie usługi** > **Analytics** > **HDInsight klastrów**.
3. Wybierz klaster HDInsight z listy.
4. W kategorii **Ustawienia** wybierz pozycję **Aplikacje**. W oknie głównym można wyświetlić listę zainstalowanych aplikacji. 
   
    ![Aplikacje usługi HDInsight — zainstalowane aplikacje](./media/hdinsight-apps-install-applications/hdinsight-apps-installed-apps-with-apps.png)
5. Wybierz jedną z zainstalowanych aplikacji, aby wyświetlić właściwość. Właściwość zawiera następujące listy:

    |Właściwość | Opis |
    |---|---|
    |Nazwa aplikacji |Nazwa aplikacji. |
    |Stan |Stan aplikacji. |
    |Strony sieci web |Adres URL aplikacji sieci web, który został wdrożony w węźle brzega. Poświadczenie jest identyczne z poświadczeniami użytkownika protokołu HTTP skonfigurowanymi dla klastra. |
    |Punkt końcowy SSH |Za pomocą funkcji SSH można połączyć się z węzłem brzegu. Poświadczenia protokołu SSH są identyczne z poświadczeniami użytkownika protokołu SSH skonfigurowanymi dla klastra. Aby uzyskać informacje, zobacz [Używanie protokołu SSH w usłudze HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md). |
    |Opis | Opis aplikacji. |

6. Aby usunąć aplikację, kliknij ją prawym przyciskiem myszy, a następnie kliknij polecenie **Usuń** z menu kontekstowego.

## <a name="connect-to-the-edge-node"></a>Łączenie z węzłem krawędzi
Z węzłem krawędzi możesz połączyć się przy użyciu protokołów HTTP i SSH. Informacje o punkcie końcowym można znaleźć w [portalu](#list-installed-hdinsight-apps-and-properties). Aby uzyskać informacje, zobacz [Używanie protokołu SSH w usłudze HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

Poświadczenia punktu końcowego protokołu HTTP to poświadczenia użytkownika protokołu HTTP skonfigurowane dla klastra usługi HDInsight. Poświadczenia punktu końcowego protokołu SSH to poświadczenia SSH skonfigurowane dla klastra usługi HDInsight.

## <a name="troubleshoot"></a>Rozwiązywanie problemów
Zobacz [Troubleshoot the installation](hdinsight-apps-install-custom-applications.md#troubleshoot-the-installation) (Rozwiązywanie problemów z instalacją).

## <a name="next-steps"></a>Następne kroki
* [Instalowanie niestandardowych aplikacji HDInsight](hdinsight-apps-install-custom-applications.md): dowiedz się, jak wdrożyć nieopublikowane aplikacje HDInsight w udojaźniowej aplikacji HDInsight.
* [Publikowanie aplikacji usługi HDInsight](hdinsight-apps-publish-applications.md): dowiedz się, jak opublikować niestandardowe aplikacje usługi HDInsight w portalu Azure Marketplace.
* [MSDN: instalowanie aplikacji usługi HDInsight](https://msdn.microsoft.com/library/mt706515.aspx): dowiedz się, jak zdefiniować aplikacje usługi HDInsight.
* [Dostosowywanie klastrów usługi HDInsight opartych na systemie Linux przy użyciu akcji skryptu](hdinsight-hadoop-customize-cluster-linux.md): dowiedz się, jak instalować dodatkowe aplikacje za pomocą akcji skryptu.
* [Tworzenie klastrów Apache Hadoop opartych na systemie Linux w systemie HDInsight przy użyciu szablonów Menedżera zasobów](hdinsight-hadoop-create-linux-clusters-arm-templates.md): dowiedz się, jak wywoływać szablony Menedżera zasobów w celu tworzenia klastrów HDInsight.
* [Use empty edge nodes in HDInsight](hdinsight-apps-use-edge-node.md) (Używanie pustych węzłów krawędzi w usłudze HDInsight): dowiedz się, jak za pomocą pustego węzła krawędzi uzyskiwać dostęp do klastra usługi HDInsight oraz testować i hostować aplikacje usługi HDInsight.

