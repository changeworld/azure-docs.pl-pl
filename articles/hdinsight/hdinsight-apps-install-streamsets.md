---
title: Instalowanie opublikowanej aplikacji — StreamSets Data Collector — Azure HDInsight
description: Zainstalować i używać aplikacji platformy Apache Hadoop innych firm StreamSets Data Collector.
services: hdinsight
author: ashishthaps
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: ashish
ms.openlocfilehash: f963ae53e1396b1ef6279f2bd6502e5ab0cd23a1
ms.sourcegitcommit: f0c2758fb8ccfaba76ce0b17833ca019a8a09d46
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/06/2018
ms.locfileid: "51034561"
---
# <a name="install-published-application---streamsets-data-collector"></a>Instalowanie opublikowanej aplikacji — StreamSets Data Collector

W tym artykule opisano sposób instalowania i uruchamiania [StreamSets Data Collector for HDInsight](https://streamsets.com/) opublikowanych aplikacji Apache Hadoop w usłudze Azure HDInsight. Omówienie platformy aplikacji HDInsight i listę z dostępnych niezależnym dostawcą oprogramowania (ISV) opublikowanych aplikacji, zobacz [instalowanie aplikacji platformy Hadoop innych firm](hdinsight-apps-install-applications.md). Aby uzyskać instrukcje instalowania własnej aplikacji, zobacz [Install custom HDInsight applications](hdinsight-apps-install-custom-applications.md) (Instalowanie niestandardowych aplikacji usługi HDInsight).

## <a name="about-streamsets-data-collector"></a>Temat StreamSets Data Collector

StreamSets Data Collector wdraża się na podstawie aplikacji usługi Azure HDInsight. Rozwiązanie StreamSets Data Collector zapewnia w pełni funkcjonalne zintegrowanym środowisku programistycznym (IDE) pozwala na projektowanie, testowanie, wdrażanie i zarządzanie dowolna dowolna potoków pozyskiwania. Te potoki można siatki strumieniowe i dane partii oraz obejmują różne przekształcenia strumienia wszystko to bez konieczności pisania kodu niestandardowego.

Rozwiązanie StreamSets Data Collector umożliwia przy użyciu wielu składników danych Big Data, takich jak system plików HDFS, Kafka, Solr, Hive, HBASE i Kudu przepływów danych kompilacji. Gdy rozwiązanie StreamSets Data Collector jest uruchomiona na serwerze krawędzi lub w klastrze usługi Hadoop, uzyskasz monitorowania zarówno dla danych anomalie i danych działań przepływu w czasie rzeczywistym. Monitorowanie obejmuje alerty oparte na wartościach progowych, wykrywanie anomalii i automatyczne korygowanie rekordów błędów.

Rozwiązanie StreamSets Data Collector jest przeznaczony do logicznie izolowania każdego etapu w potoku, dzięki czemu można spełnia nowe wymagania biznesowe odrzucając bez kodowania i przy minimalnych przestojach w nowe procesory i łączników.

### <a name="streamsets-resource-links"></a>Linki do zasobów StreamSets

* [Dokumentacja](https://streamsets.com/documentation/datacollector/latest/help/#Getting_Started/GettingStarted_Title.html)
* [Blog](https://streamsets.com/blog/)
* [Samouczki](https://github.com/streamsets/tutorials)
* [Forum pomocy technicznej Developer](https://groups.google.com/a/streamsets.com/forum/#!forum/sdc-user)
* [Kanał StreamSets publicznego Slack](https://streamsetters.slack.com/)
* [Kod źródłowy](https://github.com/streamsets)

## <a name="prerequisites"></a>Wymagania wstępne

Aby zainstalować tę aplikację w nowym klastrze HDInsight lub istniejącego klastra, musisz mieć następującą konfigurację:

* Warstwy klastrów: Standard lub Premium
* Wersje klastra: 3.5 i nowsze wersje

## <a name="install-the-streamsets-data-collector-published-application"></a>Instalowanie StreamSets Data Collector opublikowanej aplikacji

Aby uzyskać instrukcje krok po kroku dotyczące instalowania tego i innych dostępnych aplikacji niezależnych dostawców oprogramowania, przeczytaj [instalowanie aplikacji platformy Hadoop innych firm](hdinsight-apps-install-applications.md).

## <a name="launch-streamsets-data-collector"></a>Uruchom StreamSets Data Collector

1. Po zakończeniu instalacji możesz uruchomić StreamSets z klastrem w witrynie Azure portal, przechodząc do **ustawienia** okienka, a następnie wybierając pozycję **aplikacje** w obszarze **ogólne** Kategoria. W okienku zainstalowane aplikacje zawiera listę zainstalowanych aplikacji.

    ![StreamSets zainstalowanych aplikacji](./media/hdinsight-apps-install-streamsets/streamsets.png)

2. Po wybraniu StreamSets Data Collector, zostanie wyświetlony link do strony sieci web i ścieżka punktu końcowego protokołu SSH. Wybierz łącze strony sieci Web.

3. W oknie dialogowym logowania, użyj poniższych poświadczeń, aby zalogować się: `admin` i `admin`.

4. Na stronie wprowadzenie kliknij **Utwórz nowy potok**.

    ![Utwórz nowy potok](./media/hdinsight-apps-install-streamsets/get-started.png)

5. W oknie Nowy potok, wprowadź nazwę dla potoku ("Hello World"), opcjonalnie wprowadź opis i wybierz **Zapisz**.

6. Konsola modułów zbierających dane jest wyświetlany. Na panelu Właściwości są wyświetlane właściwości potoku.
 
    ![Konsola modułu zbierającego dane](./media/hdinsight-apps-install-streamsets/pipeline-canvas.png)

7. Teraz można przystąpić do wykonania [samouczek StreamSets](https://streamsets.com/documentation/datacollector/latest/help/#Tutorial/Tutorial-title.html). Samouczek zawiera szczegółowe wskazówki dotyczące tworzenia pierwszego potoku.

## <a name="next-steps"></a>Kolejne kroki

* [Dokumentacja StreamSets Data Collector](https://streamsets.com/documentation/datacollector/latest/help/#Getting_Started/GettingStarted_Title.html#concept_htw_ghg_jq).
* [Instalowanie niestandardowych aplikacji HDInsight](hdinsight-apps-install-custom-applications.md): Dowiedz się, jak wdrożyć nieopublikowane aplikację HDInsight w usłudze HDInsight.
* [Publikowanie aplikacji usługi HDInsight](hdinsight-apps-publish-applications.md): dowiedz się, jak opublikować niestandardowe aplikacje usługi HDInsight w portalu Azure Marketplace.
* [MSDN: instalowanie aplikacji usługi HDInsight](https://msdn.microsoft.com/library/mt706515.aspx): dowiedz się, jak zdefiniować aplikacje usługi HDInsight.
* [Dostosowywanie klastrów HDInsight opartych na systemie Linux za pomocą akcji skryptu](hdinsight-hadoop-customize-cluster-linux.md): Dowiedz się, jak instalować dodatkowe aplikacje za pomocą akcji skryptu.
* [Używanie pustych węzłów brzegowych w HDInsight](hdinsight-apps-use-edge-node.md): Dowiedz się, jak użyć pustego węzła krawędzi do uzyskiwania dostępu do klastrów HDInsight, a także do testowania i obsługi aplikacji HDInsight.
