---
title: Komputerami z systemem Windows za pomocą usługi Hadoop w usłudze HDInsight - Azure | Dokumentacja firmy Microsoft
description: Praca z Komputerami z systemem Windows w Hadoop w usłudze HDInsight. Zarządzanie i klastrów zapytania przy użyciu narzędzi programu PowerShell, Visual Studio i Linux. Tworzenie rozwiązania typu big data z platformą .NET.
services: hdinsight
keywords: hadoop w systemie windows, usługi hadoop dla systemu windows
author: cjgronlund
manager: jhubbard
ms.author: cgronlun
ms.date: 05/17/2017
ms.topic: article
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.openlocfilehash: c9e4476334db95c66650f663dc3d8d13df2c5b52
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2018
---
# <a name="work-in-the-hadoop-ecosystem-on-hdinsight-from-a-windows-pc"></a>Praca w ekosystemie Hadoop w usłudze HDInsight z komputera z systemem Windows

Więcej informacji na temat projektowania i opcji zarządzania na komputerze z systemem Windows do pracy w ekosystemie Hadoop w usłudze HDInsight. 

HDInsight jest oparta na Apache Hadoop i składniki platformy Hadoop, technologii open source opracowany w systemie Linux. HDInsight w wersji 3.4 i wyższych używa dystrybucji Ubuntu Linux jako podstawowy system operacyjny dla klastra. Można jednak pracować z usługą HDInsight, z poziomu klienta systemu Windows lub środowiska projektowego systemu Windows.

## <a name="use-powershell-for-deployment-and-management-tasks"></a>Przy użyciu programu PowerShell dla zadania wdrażania i zarządzania
Program Azure PowerShell jest środowisko obsługi skryptów, który służy do kontrolowania i automatyzowania wdrażania i zarządzania zadaniami w usłudze HDInsight z systemu Windows.

Przykłady zadania, które można wykonać przy użyciu programu PowerShell:

* [Tworzenie klastrów za pomocą programu PowerShell](hdinsight-hadoop-create-linux-clusters-azure-powershell.md)
* [Uruchamianie zapytań Hive przy użyciu programu PowerShell](hadoop/apache-hadoop-use-hive-powershell.md)
* [Zarządzanie klastrami przy użyciu programu PowerShell](hdinsight-administer-use-powershell.md)

Wykonaj kroki, aby [Instalowanie i konfigurowanie programu Azure Powershell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps) można pobrać najnowszą wersję. Jeśli masz skrypty wymagające modyfikacji w celu użycia nowych poleceń cmdlet dla usługi Azure Resource Manager, zobacz [Migrowanie do narzędzi programistycznych opartych na usłudze Azure Resource Manager dla klastrów usługi HDInsight](hdinsight-hadoop-development-using-azure-resource-manager.md).

## <a name="utilities-you-can-run-in-a-browser"></a>Narzędzia można uruchomić w przeglądarce
Poniższe narzędzia ma interfejsu użytkownika, który jest uruchamiany w przeglądarce sieci web:
* **[Powłoka chmury Azure (wersja zapoznawcza)](https://docs.microsoft.com/azure/cloud-shell/quickstart)**  jest interaktywny, wiersza polecenia powłoki, uruchomioną w przeglądarce, a za pomocą portalu Azure.
* **[Interfejs sieci Web Ambari](hdinsight-hadoop-manage-ambari.md)**  jest zarządzania i monitorowania narzędzia dostępne w portalu Azure, który może służyć do zarządzania różnych rodzajów zadań, takich jak:
    * [Ambari za pomocą interfejsu API REST](hdinsight-hadoop-manage-ambari-rest-api.md)
    * [Widok hive narzędzia Ambari](hadoop/apache-hadoop-use-hive-ambari-view.md)
    * [Widok tez w Ambari](hdinsight-debug-ambari-tez-view.md)

## <a name="data-lake-hadoop-tools-for-visual-studio"></a>Narzędzia Data Lake (Hadoop) dla programu Visual Studio
Data Lake Tools dla programu Visual Studio umożliwia wdrażanie i zarządzanie topologiami Storm. Narzędzia Data Lake Tools instaluje zestaw SDK SCP.NET, co pozwala na tworzenie topologii Storm C# w programie Visual Studio.

Przed przejściem do poniższych przykładach [zainstalować i spróbować narzędzi Data Lake Tools dla programu Visual Studio](hadoop/apache-hadoop-visual-studio-tools-get-started.md). 

Przykłady zadań, które można zrobić za pomocą programu Visual Studio i narzędzi Data Lake Tools dla programu Visual Studio:
* [Wdrażanie i zarządzanie topologiami Storm w programie Visual Studio](storm/apache-storm-deploy-monitor-topology-linux.md)
* [Tworzenie topologii C# dla Storm przy użyciu programu Visual Studio](storm/apache-storm-develop-csharp-visual-studio-topology.md). Bity obejmują szablony przykład topologii Storm, które możesz nawiązać połączenie bazy danych, takie jak bazy danych Azure rozwiązania Cosmos i bazy danych SQL.

## <a name="visual-studio-and-the-net-sdk"></a>Program Visual Studio i zestawu .NET SDK 

Visual Studio przy użyciu zestawu .NET SDK służy do zarządzania klastrami i tworzyć aplikacje danych big data. Można użyć innych IDEs dla następujących zadań, ale przykłady są wyświetlane w programie Visual Studio.

Przykłady zadania, które można wykonać przy użyciu zestawu .NET SDK w programie Visual Studio:
* [Tworzenie klastrów i pracy w usłudze HDInsight z poziomu aplikacji .NET Framework](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md)
* [Uruchamianie zapytań Hive przy użyciu zestawu .NET SDK](hadoop/apache-hadoop-use-hive-dotnet-sdk.md)
* [C# zdefiniowane przez użytkownika funkcji za pomocą technologii Hive i Pig przesyłania strumieniowego na platformie Hadoop](hadoop/apache-hadoop-hive-pig-udf-dotnet-csharp.md)

> Porada Jeśli korzystasz z rozwiązań .NET z klastrami HDInsight opartych na systemie Windows, jest odpowiedni moment, aby planowania migracji do opartych na systemie Linux klastrów. Aby uzyskać więcej informacji, zobacz [migracji .NET rozwiązania dla systemu Windows usługi HDInsight w usłudze HDInsight opartych na systemie Linux](hdinsight-hadoop-migrate-dotnet-to-linux.md).

## <a name="intellij-idea-and-eclipse-ide-for-spark-clusters"></a>Intellij IDEA i Eclipse IDE for klastry Spark
Zarówno [Intellij IDEA](https://www.jetbrains.com/idea/download) i [Eclipse IDE](https://www.eclipse.org/downloads/) można używać do:
* Tworzenie i przesyłanie aplikacji Scala Spark w klastrze Spark w usłudze HDInsight.
* Dostęp do zasobów klastra Spark.
* Tworzenie i uruchamianie aplikacji Scala Spark lokalnie.

Te artykuły Pokaż jak: 
* Intellij IDEA: [aplikacji Spark Utwórz za pomocą narzędzi Azure dla wtyczkę Intellij i Scala zestawu SDK.](spark/apache-spark-intellij-tool-plugin.md)
* Zaćmienie-IDE lub Scala IDE dla programu Eclipse: [Spark tworzenia aplikacji i zestawu narzędzi platformy Azure dla programu Eclipse](spark/apache-spark-eclipse-tool-plugin.md) 


## <a name="notebooks-on-spark-for-data-scientists"></a>Notesów na Spark dla analityków danych 
Klastry platformy Apache Spark w usłudze HDInsight obejmują notesów Zeppelin i jądra, które mogą być używane z notesów Jupyter. 

* [Dowiedz się, jak używać jądra na klastry Spark z notesów Jupyter do testowania aplikacji Spark](spark/apache-spark-zeppelin-notebook.md)
* [Dowiedz się, jak korzystanie z notesów Zeppelin w klastrze Spark do uruchomienia zadań Spark](spark/apache-spark-jupyter-notebook-kernels.md) 


## <a name="run-linux-based-tools-and-technologies-on-windows"></a>Uruchamianie narzędzi opartych na systemie Linux i technologii w systemie Windows

W razie wystąpienia sytuacji, w których należy użyć narzędzia lub technologii, która jest dostępna tylko w systemie Linux, należy wziąć pod uwagę następujące opcje:

* **Bash (beta) w systemie Windows 10** zapewnia podsystemu systemu Linux w systemie Windows. Bash umożliwia bezpośrednio uruchomić narzędzia Linux bez konieczności obsługi dedykowanych instalacja systemu Linux. [Zainstaluj i uruchom Bash beta w systemie Windows 10](https://msdn.microsoft.com/commandline/wsl/install_guide)
* **Docker dla systemu Windows** zapewnia dostęp do wielu narzędzi opartych na systemie Linux i można je uruchomić bezpośrednio z systemu Windows. Na przykład można użyć Docker Uruchom klienta Beeline gałęzi bezpośrednio z systemu Windows. Można również użyć do uruchamiania lokalnego notesu Jupyter Docker i zdalne połączenia z platformy Spark w usłudze HDInsight. [Rozpoczynanie pracy z rozwiązaniem Docker dla systemu Windows](https://docs.docker.com/docker-for-windows/)
* **[MobaXTerm](http://mobaxterm.mobatek.net/)**  zezwala na używanie przeglądanie klastra systemu plików za pośrednictwem połączenia SSH.

## <a name="next-steps"></a>Kolejne kroki
Jeśli masz doświadczenia w pracy w klastrach opartych na systemie Linux, zobacz artykuły wykonaj:
* [Konfigurowanie usługi Hadoop, Kafka, Spark lub innych klastrów](hdinsight-hadoop-provision-linux-clusters.md)
* [Porady dotyczące klastrów usługi HDInsight w systemie Linux](hdinsight-hadoop-linux-information.md)