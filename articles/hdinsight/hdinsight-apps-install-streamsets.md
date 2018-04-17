---
title: Zainstaluj opublikowana aplikacja — moduł zbierający dane o StreamSets - Azure HDInsight | Dokumentacja firmy Microsoft
description: Instalowanie i korzystanie z aplikacji przez moduł zbierający dane StreamSets innych firm Hadoop.
services: hdinsight
documentationcenter: ''
author: ashishthaps
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: ''
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.date: 01/10/2018
ms.author: ashish
ms.openlocfilehash: e433de82576f8b943988881ed0b6673c0dccd77e
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="install-published-application---streamsets-data-collector"></a>Instalowanie aplikacji opublikowanych - StreamSets modułów zbierających dane

W tym artykule opisano, jak instalować i uruchamiać [StreamSets modułów zbierających dane dla usługi HDInsight](https://streamsets.com/) opublikowana aplikacja platformy Hadoop w usłudze Azure HDInsight. Przegląd platformy aplikacji usługi HDInsight oraz listę z dostępnych niezależnie od producenta oprogramowania (ISV) opublikowanych aplikacji, zobacz [instalacji aplikacji innych firm Hadoop](hdinsight-apps-install-applications.md). Aby uzyskać instrukcje instalowania własnej aplikacji, zobacz [Install custom HDInsight applications](hdinsight-apps-install-custom-applications.md) (Instalowanie niestandardowych aplikacji usługi HDInsight).

## <a name="about-streamsets-data-collector"></a>Moduł zbierający dane StreamSets — informacje

Moduł zbierający dane StreamSets wdraża się na górze aplikację usługi Azure HDInsight. Moduł zbierający dane StreamSets zawiera kompletne zintegrowane środowisko programistyczne (IDE) czy umożliwia projektowanie, testowanie, wdrażania i zarządzania dowolny z każdym pozyskiwania potoków. Potoki te można siatki danych strumienia i partii i obejmują różne przekształcenia-stream, wszystko to bez konieczności pisania kodu niestandardowego.

Moduł zbierający dane StreamSets umożliwia przy użyciu wielu składników danych Big Data, takich jak system plików HDFS, Kafka, Solr, Hive, HBASE i Kudu przepływów danych kompilacji. Gdy moduł zbierający dane StreamSets jest uruchomiona na serwerze granicznym lub w klastrze platformy Hadoop, możesz uzyskać w czasie rzeczywistym monitorowania dla danych anomalii i danych przepływu. Monitorowanie obejmuje alerty oparte na wartościach progowych, wykrywanie anomalii i automatycznego korygowania rekordów błędów.

Moduł zbierający dane StreamSets zaprojektowano w celu logicznie izolować każdego etapu w potoku, więc można spełnić nowe wymagania biznesowe przez usunięcie łączników i nowe procesory bez kodowania i z minimalnym czasem przestojów.

### <a name="streamsets-resource-links"></a>Linki do zasobów StreamSets

* [Dokumentacja](https://streamsets.com/documentation/datacollector/latest/help/#Getting_Started/GettingStarted_Title.html)
* [Blog](https://streamsets.com/blog/)
* [Samouczki](https://github.com/streamsets/tutorials)
* [Forum pomocy technicznej Developer](https://groups.google.com/a/streamsets.com/forum/#!forum/sdc-user)
* [Kanał StreamSets publicznego zapas czasu](https://streamsetters.slack.com/)
* [Kod źródłowy](https://github.com/streamsets)

## <a name="prerequisites"></a>Wymagania wstępne

Aby zainstalować tę aplikację w nowym klastrze HDInsight lub istniejącego klastra, musi mieć następującą konfigurację:

* Klaster tier(s): Standard lub Premium
* Klaster wersje: 3.5 i powyżej.

## <a name="install-the-streamsets-data-collector-published-application"></a>Zainstaluj moduł zbierający dane StreamSets opublikowane aplikacji

Aby uzyskać instrukcje krok po kroku dotyczące instalowania tego i innych aplikacjach dostępnych niezależnego dostawcy oprogramowania, przeczytaj [instalacji aplikacji innych firm Hadoop](hdinsight-apps-install-applications.md).

## <a name="launch-streamsets-data-collector"></a>Uruchom moduł zbierający dane StreamSets

1. Po zakończeniu instalacji, możesz uruchomić StreamSets z klastrem w portalu Azure, przechodząc do **ustawienia** okienku, wybierając **aplikacji** w obszarze **ogólne** Kategoria. W okienku zainstalowane aplikacje Wyświetla listę zainstalowanych aplikacji.

    ![StreamSets zainstalowanych aplikacji](./media/hdinsight-apps-install-streamsets/streamsets.png)

2. Po wybraniu StreamSets modułów zbierających dane, zostanie wyświetlony link do strony sieci web i ścieżkę punktu końcowego protokołu SSH. Wybierz łącze strony sieci Web.

3. W oknie dialogowym logowania, użyj poniższych poświadczeń do logowania się w: `admin` i `admin`.

4. Na stronie wprowadzenie kliknij **Utwórz nowy potok**.

    ![Utwórz nowy potok](./media/hdinsight-apps-install-streamsets/get-started.png)

5. W oknie Nowy potok, wprowadź nazwę dla potoku ("Hello World"), opcjonalnie wprowadź opis i wybierz **zapisać**.

6. Moduł zbierający dane konsoli jest wyświetlana. Na panelu Właściwości są wyświetlane właściwości potoku.
 
    ![Konsola modułów zbierających dane](./media/hdinsight-apps-install-streamsets/pipeline-canvas.png)

7. Teraz można przystąpić do wykonania [samouczek StreamSets](https://streamsets.com/documentation/datacollector/latest/help/#Tutorial/Tutorial-title.html). Samouczek zawiera szczegółowe wskazówki dotyczące tworzenia swój pierwszy potok.

## <a name="next-steps"></a>Kolejne kroki

* [Moduł zbierający dane StreamSets dokumentacji](https://streamsets.com/documentation/datacollector/latest/help/#Getting_Started/GettingStarted_Title.html#concept_htw_ghg_jq).
* [Instalowanie niestandardowych aplikacji usługi HDInsight](hdinsight-apps-install-custom-applications.md): Dowiedz się, jak wdrożyć aplikację usługi HDInsight nieopublikowane do usługi HDInsight.
* [Publikowanie aplikacji usługi HDInsight](hdinsight-apps-publish-applications.md): dowiedz się, jak opublikować niestandardowe aplikacje usługi HDInsight w portalu Azure Marketplace.
* [MSDN: instalowanie aplikacji usługi HDInsight](https://msdn.microsoft.com/library/mt706515.aspx): dowiedz się, jak zdefiniować aplikacje usługi HDInsight.
* [Dostosowywanie klastrów usługi HDInsight opartej na systemie Linux przy użyciu akcji skryptu](hdinsight-hadoop-customize-cluster-linux.md): Dowiedz się, jak instalować dodatkowe aplikacje przy użyciu akcji skryptu.
* [Użyj węzłami pusty krawędzi w usłudze HDInsight](hdinsight-apps-use-edge-node.md): Dowiedz się, jak uzyskać dostęp do klastrów usługi HDInsight i do badania i obsługiwania aplikacji usługi HDInsight za pomocą węzła krawędzi puste.
