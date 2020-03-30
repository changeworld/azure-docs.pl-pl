---
title: Korzystanie z grawany w usłudze Azure HDInsight
description: Dowiedz się, jak uzyskać dostęp do pulpitu nawigacyjnego Grafana za pomocą klastrów Apache Hadoop w usłudze Azure HDInsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 12/27/2019
ms.openlocfilehash: cd515bfd1dc57e78a041ed96686e1ba692bf6d3f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79082867"
---
# <a name="access-grafana-in-azure-hdinsight"></a>Dostęp do narzędzia Grafana w usłudze Azure HDInsight

[Grafana](https://grafana.com/) jest popularnym, open-source wykres i kreator deski rozdzielczej. Grafana jest bogata w funkcje; nie tylko pozwala użytkownikom tworzyć konfigurowalne i współdzielone pulpity nawigacyjne, ale także oferuje szablonowe / skryptowe pulpity nawigacyjne, integrację LDAP, wiele źródeł danych i wiele innych.

Obecnie w usłudze Azure HDInsight grawana jest obsługiwana za pomocą typów klastra Spark, HBase, Kafka i Interactive Query. Nie jest obsługiwany dla klastrów z włączonym pakietem enterprise security pack.

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.

## <a name="create-an-apache-hadoop-cluster"></a>Tworzenie klastra Apache Hadoop

Zobacz [Tworzenie klastrów Apache Hadoop przy użyciu portalu Azure](../hdinsight-hadoop-create-linux-clusters-portal.md). W przypadku **typu klastra**wybierz opcję **Iskre**, **Kafka**, **HBase**lub **Zapytanie interaktywne**.

## <a name="access-the-grafana-dashboard"></a>Dostęp do pulpitu nawigacyjnego grawanu

1. Z przeglądarki internetowej przejdź `https://CLUSTERNAME.azurehdinsight.net/grafana/` do miejsca, w którym nazwa klastra jest nazwą klastra.

1. Wprowadź poświadczenia użytkownika klastra Hadoop.

1. Pulpit nawigacyjny Grafana pojawia się i wygląda jak w tym przykładzie:

    ![Pulpit nawigacyjny sieci Web HDInsight Grafana](./media/hdinsight-grafana/hdinsight-grafana-dashboard.png "HdInsight Grafana deski rozdzielczej")

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli nie zamierzasz nadal korzystać z tej aplikacji, usuń utworzony klaster, wykonując następujące kroki:

1. Zaloguj się do [Portalu Azure](https://portal.azure.com/).

1. W polu **Wyszukaj** w górnej części wpisz **HDInsight**.

1. Wybierz pozycję **Klastry usługi HDInsight** w obszarze **Usługi**.

1. Na wyświetlona lista klastrów HDInsight wybierz **...** obok utworzonego klastra.

1. Wybierz pozycję **Usuń**. Wybierz **pozycję Tak**.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat analizowania danych za pomocą usługi HDInsight, zobacz następujące artykuły:

* [Użyj Apache Hive z HDInsight](../hadoop/hdinsight-use-hive.md).

* [Użyj MapReduce z HDInsight](../hadoop/hdinsight-use-mapreduce.md).

* [Wprowadzenie do korzystania z narzędzi programu Visual Studio Hadoop dla programu HDInsight](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).
