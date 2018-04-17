---
title: Instalowanie aplikacji platformy Hadoop innych firm w usłudze Azure HDInsight | Microsoft Docs
description: Dowiedz się, jak instalować aplikacje platformy Hadoop innych firm w usłudze Azure HDInsight.
services: hdinsight
documentationcenter: ''
author: mumian
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: eaf5904d-41e2-4a5f-8bec-9dde069039c2
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 02/16/2018
ms.author: jgao
ms.openlocfilehash: bd74be9c78744619f60606b509ea16d000bd3998
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="install-third-party-hadoop-applications-on-azure-hdinsight"></a>Instalowanie aplikacji platformy Hadoop innych firm w usłudze Azure HDInsight

Dowiedz się, jak zainstalować aplikacji innych firm Hadoop w usłudze Azure HDInsight. Aby uzyskać instrukcje instalowania własnej aplikacji, zobacz [Install custom HDInsight applications](hdinsight-apps-install-custom-applications.md) (Instalowanie niestandardowych aplikacji usługi HDInsight).

Aplikację usługi HDInsight to aplikacja, którą użytkownicy mogą zainstalować w klastrze usługi HDInsight. Te aplikacje mogą być opracowane przez firmę Microsoft, niezależnych dostawców oprogramowania (ISV) lub samodzielnie.  

Na poniższej liście przedstawiono opublikowanych aplikacji:

* **Platforma analizy AtScale** włącza z klastrem usługi HDInsight do skalowalnego w poziomie serwera OLAP. Aplikacja umożliwia miliardów wierszy danych interaktywnego przy użyciu narzędzi do analizy Biznesowej z programu Microsoft Excel, usługi Power Bi i Tableau oprogramowania, aby QlikView zapytania.
* **Pojemnika transportowego CDAP dla usługi HDInsight** zapewnia platformę pierwszy ujednoliconego integracji dla danych big data, która ogranicza czas do produkcji dla danych aplikacji i danych jeziora o 80%. Ta aplikacja obsługuje tylko standardowe klastry bazy danych HBase 3.4.
* **DDS DATAIKU w usłudze HDInsight** pozwala specjalistom danych prototyp, tworzenie i wdrażanie wysokiej określonych usług, które przekształcenia danych pierwotnych w prognoz istotna biznesowych.
* **Analiza sztucznego H2O dla usługi HDInsight (Beta)** H2O musujących wody obsługuje następujące algorytmy rozproszonej: GLM, prostym algorytmie Bayesa, lasu losowe rozproszone, gradientu zwiększania maszyny, głębokie sieci neuronowe, bezpośrednich uczenia K-średnich, PCA, Uogólniony niski rangi modeli, wykrywanie anomalii i Autoencoders.
* **Platforma do analiz Kyligence** Kyligence Analytics Platform (KAP) to magazyn danych gotowe enterprise, obsługiwane przez Apache Kylin i Apache Hadoop, co zapewnia podrzędne sekundę zapytania czas oczekiwania na ogromną skalę zestawu danych i upraszcza analiza danych Użytkownicy biznesowi i analityków. 
* **Przygotowanie danych Paxata samoobsługi**
* **Serwer zadań Spark dla modułu wykonującego Spark KNIME** serwer zadań Spark dla modułu wykonującego Spark KNIME służy do łączenia klastrów usługi HDInsight KNIME Analytics Platform.
* **Streamsets Data Collector for HDnsight** zapewnia kompleksowe zintegrowane środowisko projektowe (IDE) umożliwiające projektowanie, testowanie i wdrażanie potoków pozyskiwania typu dowolny-dowolny (które łączą dane strumieniowe i dane partii oraz obejmują różne przekształcenia strumienia) oraz zarządzanie tymi potokami — wszystko to bez konieczności pisania niestandardowego kodu. 
* **[Trifacta](http://www.trifacta.com/)**  umożliwia inżynierów danych i analityków wydajniej Eksploruj i przygotowanie różnorodnych danych współczesnych wykorzystując Podaj przełomowe środowisko użytkownika, przepływu pracy i architektura uczenia maszynowego.
* **Aplikacja HDI Fusion WANdisco** umożliwia stałe połączenie spójne z danymi, jak zmieniają się wszędzie tam, gdzie znajduje się. Umożliwia dostęp do danych w dowolnym momencie i dowolnego miejsca bez przestojów i nie przerw w działaniu.

W instrukcjach podanych w tym artykule jest używana witryna Azure Portal. Można również wyeksportować szablon usługi Azure Resource Manager z portalu lub uzyskać kopię szablonu usługi Resource Manager od dostawców i użyć programu Azure PowerShell oraz interfejsu wiersza polecenia platformy Azure do wdrożenia szablonu.  Zobacz [klastrów utworzyć Hadoop w usłudze HDInsight przy użyciu szablonów usługi Resource Manager](hdinsight-hadoop-create-linux-clusters-arm-templates.md).

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
6. Kliknij jedną z dostępnych aplikacji, a następnie postępuj zgodnie z instrukcjami, aby zaakceptować postanowienia prawne.

Możesz zobaczyć stan instalacji w powiadomieniach portalu (kliknij ikonę dzwonka w górnej części portalu). Po zainstalowaniu aplikacji, aplikacja zostanie wyświetlona na liście zainstalowane aplikacje.

## <a name="install-applications-during-cluster-creation"></a>Instalowanie aplikacji podczas tworzenia klastra
Aplikacje usługi HDInsight możesz także zainstalować podczas tworzenia klastra. W trakcie procesu aplikacje usługi HDInsight są instalowane po utworzeniu i uruchomieniu klastra. Umożliwia instalowanie aplikacji podczas tworzenia klastra przy użyciu portalu Azure, można użyć opcji--niestandardowe — zamiast domyślnego — szybkie tworzenie — opcja.

## <a name="list-installed-hdinsight-apps-and-properties"></a>Lista zainstalowanych aplikacji HDInsight i ich właściwości
Portal zawiera listę zainstalowanych aplikacji usługi HDInsight dla klastra oraz właściwości poszczególnych zainstalowanych aplikacji.

**Lista aplikacji usługi HDInsight i Wyświetl właściwości**

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Kliknij pozycję **Klastry usługi HDInsight** w lewym menu. 
3. Kliknij klaster usługi HDInsight.
4. Z **ustawienia**, kliknij przycisk **aplikacji** w obszarze **konfiguracji** kategorii. Zainstalowane aplikacje są wyświetlane po prawej stronie. 
   
    ![Aplikacje usługi HDInsight — zainstalowane aplikacje](./media/hdinsight-apps-install-applications/hdinsight-apps-installed-apps-with-apps.png)
5. Kliknij jedną z zainstalowanych aplikacji, aby wyświetlić właściwości. Listy właściwości:
   
   * Nazwa aplikacji: nazwa aplikacji.
   * Stan: stan aplikacji. 
   * Strony sieci Web: Adres URL aplikacji sieci web, która została wdrożona z węzłem krawędzi. Poświadczenie jest identyczne z poświadczeniami użytkownika protokołu HTTP skonfigurowanymi dla klastra.
   * Punkt końcowy protokołu HTTP: poświadczenie jest identyczne z poświadczeniami użytkownika protokołu HTTP skonfigurowanymi dla klastra. 
   * Punkt końcowy SSH: SSH umożliwia łączenie z węzłem krawędzi. Poświadczenia protokołu SSH są identyczne z poświadczeniami użytkownika protokołu SSH skonfigurowanymi dla klastra. Aby uzyskać informacje, zobacz [Używanie protokołu SSH w usłudze HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).
6. Aby usunąć aplikację, kliknij prawym przyciskiem myszy aplikację, a następnie kliknij przycisk **usunąć** z menu kontekstowego.

## <a name="connect-to-the-edge-node"></a>Łączenie z węzłem krawędzi
Z węzłem krawędzi możesz połączyć się przy użyciu protokołów HTTP i SSH. Informacje o punkcie końcowym można znaleźć w [portalu](#list-installed-hdinsight-apps-and-properties). Aby uzyskać informacje, zobacz [Używanie protokołu SSH w usłudze HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

Poświadczenia punktu końcowego protokołu HTTP to poświadczenia użytkownika protokołu HTTP skonfigurowane dla klastra usługi HDInsight. Poświadczenia punktu końcowego protokołu SSH to poświadczenia SSH skonfigurowane dla klastra usługi HDInsight.

## <a name="troubleshoot"></a>Rozwiązywanie problemów
Zobacz [Troubleshoot the installation](hdinsight-apps-install-custom-applications.md#troubleshoot-the-installation) (Rozwiązywanie problemów z instalacją).

## <a name="next-steps"></a>Kolejne kroki
* [Instalowanie niestandardowych aplikacji usługi HDInsight](hdinsight-apps-install-custom-applications.md): Dowiedz się, jak wdrożyć aplikację usługi HDInsight nieopublikowane do usługi HDInsight.
* [Publikowanie aplikacji usługi HDInsight](hdinsight-apps-publish-applications.md): dowiedz się, jak opublikować niestandardowe aplikacje usługi HDInsight w portalu Azure Marketplace.
* [MSDN: instalowanie aplikacji usługi HDInsight](https://msdn.microsoft.com/library/mt706515.aspx): dowiedz się, jak zdefiniować aplikacje usługi HDInsight.
* [Dostosowywanie klastrów usługi HDInsight opartych na systemie Linux przy użyciu akcji skryptu](hdinsight-hadoop-customize-cluster-linux.md): dowiedz się, jak instalować dodatkowe aplikacje za pomocą akcji skryptu.
* [Create Linux-based Hadoop clusters in HDInsight using Resource Manager templates](hdinsight-hadoop-create-linux-clusters-arm-templates.md) (Tworzenie klastrów Hadoop w usłudze HDInsight opartych na systemie Linux przy użyciu szablonów usługi Resource Manager): dowiedz się, jak wywoływać szablony usługi Resource Manager w celu tworzenia klastrów usługi HDInsight.
* [Use empty edge nodes in HDInsight](hdinsight-apps-use-edge-node.md) (Używanie pustych węzłów krawędzi w usłudze HDInsight): dowiedz się, jak za pomocą pustego węzła krawędzi uzyskiwać dostęp do klastra usługi HDInsight oraz testować i hostować aplikacje usługi HDInsight.

