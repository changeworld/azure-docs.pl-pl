---
title: Instalowanie aplikacji innych firm w usłudze Azure HDInsight
description: Dowiedz się, jak instalować aplikacje platformy Hadoop innych firm w usłudze Azure HDInsight.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/06/2018
ms.author: hrasheed
ms.openlocfilehash: 993d3dc065b792cc6d4ca0b1321cb1950cea85d8
ms.sourcegitcommit: e68df5b9c04b11c8f24d616f4e687fe4e773253c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/20/2018
ms.locfileid: "53652624"
---
# <a name="install-third-party-apache-hadoop-applications-on-azure-hdinsight"></a>Instalowanie aplikacji Apache Hadoop innych firm w usłudze Azure HDInsight

Informacje o instalowaniu innych firm [Apache Hadoop](https://hadoop.apache.org/) aplikacji w usłudze Azure HDInsight. Aby uzyskać instrukcje instalowania własnej aplikacji, zobacz [Install custom HDInsight applications](hdinsight-apps-install-custom-applications.md) (Instalowanie niestandardowych aplikacji usługi HDInsight).

Aplikacji HDInsight to aplikacja, którą użytkownicy mogą zainstalować w klastrze usługi HDInsight. Te aplikacje mogą być opracowane przez firmę Microsoft, niezależnych dostawców oprogramowania (ISV) lub samodzielnie.  

Na poniższej liście przedstawiono opublikowane aplikacje:

* **Platforma analiz danych AtScale** włącza klaster HDInsight do serwera OLAP skalowalnego w poziomie. Aplikacja umożliwia zapytania miliardów wierszy danych interaktywnie przy użyciu narzędzi analizy Biznesowej z programu Microsoft Excel, Power BI, Tableau Software QlikView.
* **Cask CDAP for HDInsight** zapewnia pierwszą ujednoliconą platformę integracji dla danych big data, która skraca czas produkcji dla danych aplikacji i usług data Lake o 80%. Ta aplikacja obsługuje tylko standardowe klastry bazy danych HBase 3.4.
* **DATAIKU DDS w HDInsight** umożliwia informatykom danych prototypów, tworzenie i wdrażanie wysoce specjalizowane usługi przekształcające surowe dane w trafne przewidywania biznesowe.
* **Datameer** jest skalowalna platforma samoobsługowa do przygotowywania, eksplorowania, i dotyczących danych na potrzeby analizy przyspiesza Przekształcanie złożonych z wielu źródeł danych w cenne informacje biznesowe, dostarczanie szybciej i MĄDRZEJ szczegółowych informacji w skali przedsiębiorstwa.
* **H2O sztucznej inteligencji dla HDInsight (wersja Beta)** H2O Sparkling Water obsługuje następujące algorytmów rozproszonych: GLM, Bayesa, rozproszonych Random lasu, gradientu zwiększania wyniku maszyny, głębokie sieci neuronowe uczenia głębokiego, K-średnich, analizie PCA, uogólniony niski ranga modele, wykrywanie anomalii i Autoencoders.
* **Kyligence Enterprise**, obsługiwane przez Apache Kylin, zapewnia błyskawiczny wgląd w dane na ogromnych zestawów danych dla analityków danych i użytkowników biznesowych. Przy użyciu uczenia maszynowego najnowocześniejszych technologii i inteligentnym danym i funkcje modelowania znacznie zwiększa wydajność analizy danych big data. 
* **Samoobsługa Adaptive przygotowywania danych**
* **Serwer zadań Spark dla wykonawcy Spark KNIME** serwer zadań Spark dla wykonawcy Spark KNIME służy do łączenia platforma analityczna KNIME klastrów HDInsight.
* **Gwiazda Presto** Presto jest szybka i skalowalna rozproszonych aparatu zapytań SQL. Zaprojektowana z myślą o separacji magazynu i mocy obliczeniowej, Presto to idealne rozwiązanie dla wykonywanie zapytań o dane w usługi Azure Data Lake Storage, bazy danych usługi Azure Blob Storage, SQL i NoSQL oraz innych źródeł danych.
* **Streamsets Data Collector for HDnsight** zapewnia kompleksowe zintegrowane środowisko projektowe (IDE) umożliwiające projektowanie, testowanie i wdrażanie potoków pozyskiwania typu dowolny-dowolny (które łączą dane strumieniowe i dane partii oraz obejmują różne przekształcenia strumienia) oraz zarządzanie tymi potokami — wszystko to bez konieczności pisania niestandardowego kodu. 
* **Striim** (wymawiane "przesyłanie strumieniowe") jest end-to-end przesyłania strumieniowego integracji danych + platforma analiz danych, włączenie ciągłego wprowadzania, przetwarzania i analizy danych z różnych strumieni.
* **[Trifacta](https://www.trifacta.com/)**  umożliwia inżynierami danych i analitykom wydajniej eksplorować i przygotować zróżnicowanych danych już dziś, korzystając z uczenia maszynowego, aby zapewnić środowisko użytkownika przełomowe, przepływu pracy i architektury.
* **Platforma danych Unifi** bezproblemowo zintegrowany zestaw narzędzi samoobsługi danych mających na celu zwiększenie możliwości dostępnych dla użytkowników biznesowych czoła wyzwaniom danych przychodów tego dysku, zmniejszyć koszty i złożoność operacyjną. 
* **Platforma WANdisco Fusion HDI aplikacji** umożliwia stałe połączenie spójne z danymi, jak zmieniają się wszędzie tam, gdzie znajduje się. Udostępnia dzięki dostępowi do danych w dowolnym momencie i dowolnym miejscu bez przestojów i nie przerw w działaniu.
* **Wodnej** katalogach, organizuje i kontroluje danych za pomocą sztucznej Inteligencji do danych Auto-oznaczanie za pomocą terminów biznesowych. Wykaz posługiwać się biznesowych firmy wodnej jest składnikiem Powodzenie krytycznych, samoobsługowej analizy, zgodności i nadzoru i w celu zarządzania.

W instrukcjach podanych w tym artykule jest używana witryna Azure Portal. Można również wyeksportować szablon usługi Azure Resource Manager z portalu lub uzyskać kopię szablonu usługi Resource Manager od dostawców i użyj programu Azure PowerShell i klasycznego wiersza polecenia platformy Azure, aby wdrożyć szablon.  Zobacz [tworzenie technologii Apache Hadoop klastrów HDInsight za pomocą szablonów usługi Resource Manager](hdinsight-hadoop-create-linux-clusters-arm-templates.md).

## <a name="prerequisites"></a>Wymagania wstępne
Jeśli chcesz zainstalować aplikacje usługi HDInsight w istniejącym klastrze usługi HDInsight, musisz dysponować klastrem usługi HDInsight. Aby go utworzyć, zobacz artykuł [Tworzenie klastrów](hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster). Możesz także zainstalować aplikacje usługi HDInsight podczas tworzenia klastra usługi HDInsight.

## <a name="install-applications-to-existing-clusters"></a>Instalowanie aplikacji w istniejących klastrach
Poniższa procedura pokazuje sposób instalacji aplikacji usługi HDInsight w istniejącym klastrze usługi HDInsight.

**Instalowanie aplikacji usługi HDInsight**

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Kliknij pozycję **Klastry usługi HDInsight** w lewym menu.
3. Kliknij klaster usługi HDInsight.  Jeśli nie masz klastra, musisz go najpierw utworzyć.  Zobacz [Tworzenie klastrów](hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster).
4. Kliknij pozycję **Aplikacje** w kategorii **Konfiguracje**. Można wyświetlić listę zainstalowanych aplikacji. Jeśli nie możesz znaleźć aplikacji, oznacza to, że dla tej wersji klastra HDInsight nie ma aplikacji.
   
    ![Aplikacje usługi HDInsight — menu portalu](./media/hdinsight-apps-install-applications/hdinsight-apps-portal-menu.png)
5. Kliknij przycisk **Dodaj** z menu. Zostanie wyświetlona lista istniejących aplikacji usługi HDInsight.
   
    ![Aplikacje usługi HDInsight — dostępne aplikacje](./media/hdinsight-apps-install-applications/hdinsight-apps-list.png)
6. Kliknij jedną z dostępnych aplikacji, a następnie postępuj zgodnie z instrukcjami, aby zaakceptować warunki prawne.

Możesz zobaczyć stan instalacji w powiadomieniach portalu (kliknij ikonę dzwonka w górnej części portalu). Po zainstalowaniu aplikacji, aplikacja zostanie wyświetlona na liście zainstalowane aplikacje.

## <a name="install-applications-during-cluster-creation"></a>Instalowanie aplikacji podczas tworzenia klastra
Aplikacje usługi HDInsight możesz także zainstalować podczas tworzenia klastra. W trakcie procesu aplikacje usługi HDInsight są instalowane po utworzeniu i uruchomieniu klastra. Instalowanie aplikacji podczas tworzenia klastra przy użyciu witryny Azure portal, używasz opcji--niestandardowe — zamiast domyślnego — szybkie tworzenie — opcji.

## <a name="list-installed-hdinsight-apps-and-properties"></a>Lista zainstalowanych aplikacji HDInsight i ich właściwości
Portal zawiera listę zainstalowanych aplikacji usługi HDInsight dla klastra oraz właściwości poszczególnych zainstalowanych aplikacji.

**Listę aplikacji HDInsight i wyświetlić właściwości**

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Kliknij pozycję **Klastry usługi HDInsight** w lewym menu. 
3. Kliknij klaster usługi HDInsight.
4. Z **ustawienia**, kliknij przycisk **aplikacje** w obszarze **konfiguracji** kategorii. Zainstalowane aplikacje są wyświetlane po prawej stronie. 
   
    ![Aplikacje usługi HDInsight — zainstalowane aplikacje](./media/hdinsight-apps-install-applications/hdinsight-apps-installed-apps-with-apps.png)
5. Kliknij jedną z zainstalowanych aplikacji, aby wyświetlić właściwości. Listy właściwości:
   
   * Nazwa aplikacji: nazwa aplikacji.
   * Stan: stan aplikacji. 
   * Strona sieci Web: Adres URL aplikacji sieci web wdrożoną w węźle brzegowym. Poświadczenie jest identyczne z poświadczeniami użytkownika protokołu HTTP skonfigurowanymi dla klastra.
   * Punkt końcowy HTTP: Poświadczenie jest identyczne z poświadczeniami użytkownika protokołu HTTP skonfigurowanymi dla klastra. 
   * Punkt końcowy SSH: Za używanie protokołu SSH Połącz się z węzłem krawędzi. Poświadczenia protokołu SSH są identyczne z poświadczeniami użytkownika protokołu SSH skonfigurowanymi dla klastra. Aby uzyskać informacje, zobacz [Używanie protokołu SSH w usłudze HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).
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

