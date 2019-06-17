---
title: Windows komputera za pomocą usługi Hadoop w HDInsight — Azure
description: Działa z Windows komputera na platformie Hadoop w HDInsight. Zarządzanie i zapytania klastrów przy użyciu narzędzia programu PowerShell, Visual Studio i systemie Linux. Twórz rozwiązania typu big data przy użyciu platformy .NET.
author: hrasheed-msft
ms.author: hrasheed
ms.topic: conceptual
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 04/24/2019
ms.openlocfilehash: 5045c48a00c51a16d37dcf4b7f72f25633f23b3f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64926028"
---
# <a name="work-in-the-apache-hadoop-ecosystem-on-hdinsight-from-a-windows-pc"></a>Działa w ekosystemie usługi Apache Hadoop w HDInsight z komputera z systemem Windows

Więcej informacji na temat tworzenia i opcje zarządzania na komputerze Windows do pracy w ekosystemie usługi Apache Hadoop w HDInsight. 

HDInsight jest oparty na technologii Apache Hadoop i składniki usługi Hadoop, technologii open source opracowany w systemie Linux. HDInsight w wersji 3.4 lub nowszej używa dystrybucji Ubuntu Linux jako podstawowego systemu operacyjnego klastra. Możesz jednak pracować z HDInsight z klienta Windows lub Windows, środowisko programistyczne.

## <a name="use-powershell-for-deployment-and-management-tasks"></a>Zadania wdrażania oraz zarządzania nimi przy użyciu programu PowerShell
Azure PowerShell to środowisko skryptów, w którym można użyć do kontrolowania i automatyzację wdrażania i zadania zarządzania infrastrukturą HDInsight z Windows.

Przykłady zadań, które można wykonać przy użyciu programu PowerShell:

* [Tworzenie klastrów przy użyciu programu PowerShell](hdinsight-hadoop-create-linux-clusters-azure-powershell.md).
* [Uruchamianie zapytania usługi Apache Hive przy użyciu programu PowerShell](hadoop/apache-hadoop-use-hive-powershell.md).
* [Zarządzanie klastrami przy użyciu programu PowerShell](hdinsight-administer-use-powershell.md).

Wykonaj kroki, aby [Instalowanie i konfigurowanie programu Azure Powershell](https://docs.microsoft.com/powershell/azure/install-az-ps) można pobrać najnowszą wersję.

## <a name="utilities-you-can-run-in-a-browser"></a>Narzędzia, które można uruchomić w przeglądarce
Poniższe narzędzia mają interfejs użytkownika, który działa w przeglądarce sieci web:
* **[Usługa Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview)**  jest interaktywny, wiersza polecenia powłoki, który działa w przeglądarce, a także z poziomu witryny Azure portal.
* **[Apache Ambari Web UI](hdinsight-hadoop-manage-ambari.md)**  zarządzania i dostępnych w witrynie Azure portal, który może służyć do różnych rodzajów zadań zarządzania, takimi jak narzędzia do monitorowania:
    * [Apache Ambari za pomocą interfejsu API REST](hdinsight-hadoop-manage-ambari-rest-api.md)
    * [Apache Hive widoku Apache Ambari](hadoop/apache-hadoop-use-hive-ambari-view.md)
    * [Apache Tez widoku w Apache Ambari](hdinsight-debug-ambari-tez-view.md)

## <a name="data-lake-hadoop-tools-for-visual-studio"></a>Data Lake (Hadoop) Tools for Visual Studio
Narzędzia Data Lake Tools for Visual Studio umożliwia wdrażanie topologii Storm i zarządzanie. Narzędzia Data Lake Tools instaluje zestaw SDK platformy SCP.NET, co pozwala na opracowywanie topologii Storm języka C# za pomocą programu Visual Studio.

Przed przejściem do poniższych przykładach [zainstalować i spróbować narzędzi Data Lake Tools for Visual Studio](hadoop/apache-hadoop-visual-studio-tools-get-started.md). 

Przykłady zadań, które można wykonać za pomocą programu Visual Studio i narzędzi Data Lake Tools dla programu Visual Studio:
* [Wdrażanie topologii Storm i zarządzanie w programie Visual Studio](storm/apache-storm-deploy-monitor-topology-linux.md)
* [Opracowywanie topologii języka C# dla Storm przy użyciu programu Visual Studio](storm/apache-storm-develop-csharp-visual-studio-topology.md). Bity obejmują przykładowych szablonów dla topologii systemu Storm, możesz łączyć z bazami danych, takich jak Azure Cosmos DB i bazy danych SQL.

## <a name="visual-studio-and-the-net-sdk"></a>Program Visual Studio i zestawu .NET SDK 

Za pomocą programu Visual Studio i zestawu .NET SDK do zarządzania klastrami i twórz aplikacje danych big data. Można użyć innego IDE dla następujących zadań, ale przykłady są wyświetlane w programie Visual Studio.

Przykłady zadań, które można wykonać przy użyciu zestawu SDK platformy .NET w programie Visual Studio:
* [Tworzenie klastrów oraz działa w HDInsight z poziomu aplikacji .NET Framework](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md).
* [Uruchamianie zapytania usługi Apache Hive przy użyciu zestawu .NET SDK](hadoop/apache-hadoop-use-hive-dotnet-sdk.md).
* [Użyj C# funkcje zdefiniowane przez użytkownika przy użyciu Apache Hive i Apache Pig, przesyłanie strumieniowe na technologii Apache Hadoop](hadoop/apache-hadoop-hive-pig-udf-dotnet-csharp.md).

## <a name="intellij-idea-and-eclipse-ide-for-spark-clusters"></a>Intellij IDEA i Eclipse IDE for klastrów Spark
Zarówno [Intellij IDEA](https://www.jetbrains.com/idea/download) i [środowiska IDE Eclipse](https://www.eclipse.org/downloads/) można używać do:
* Programuj i przesyłaj aplikację Scala Spark w klastrze usługi HDInsight Spark.
* Dostęp do zasobów klastra Spark.
* Twórz i uruchamiaj lokalnie aplikację Scala Spark.

Te artykuły pokazują, jak: 
* Intellij IDEA: [Tworzenie aplikacji platformy Apache Spark przy użyciu zestawu narzędzi platformy Azure dla środowiska Intellij wtyczki i Scala zestawu SDK.](spark/apache-spark-intellij-tool-plugin.md)
* Eclipse IDE lub Scala ze środowiska IDE dla środowiska Eclipse: [Tworzenie aplikacji platformy Apache Spark i zestawu narzędzi platformy Azure dla środowiska Eclipse](spark/apache-spark-eclipse-tool-plugin.md) 


## <a name="notebooks-on-spark-for-data-scientists"></a>Notesy na platformie Spark dla analityków danych 
Klastry Apache Spark w HDInsight obejmują z notesów Apache Zeppelin i jądra, które mogą być używane z notesów programu Jupyter. 

* [Dowiedz się, jak używać jądra za pomocą notesów programu Jupyter w klastrach platformy Apache Spark do testowania aplikacji Spark](spark/apache-spark-zeppelin-notebook.md)
* [Dowiedz się, jak korzystanie z notesów Apache Zeppelin w klastrach platformy Apache Spark, uruchamianie zadań Spark](spark/apache-spark-jupyter-notebook-kernels.md) 

## <a name="run-linux-based-tools-and-technologies-on-windows"></a>Uruchamianie narzędzi opartych na systemie Linux i technologii na Windows

Jeśli napotkasz sytuację, w którym należy użyć narzędzia lub technologia, która jest dostępna tylko w systemie Linux, należy wziąć pod uwagę następujące opcje:

* **Powłoka bash w systemie Ubuntu w systemie Windows 10** zawiera podsystemu systemu Linux na Windows. Bash umożliwia bezpośrednio uruchomić narzędzia Linux bez konieczności obsługi dedykowanych instalacja systemu Linux. Zobacz [podsystemu Windows dla systemu Linux instalacji Guide for Windows 10](https://docs.microsoft.com/windows/wsl/install-win10) dla czynności instalacyjne.  Inne [powłoki systemu Unix](https://www.gnu.org/software/bash/) będzie działać tak dobrze.
* **Docker for Windows** zapewnia dostęp do wielu narzędzi opartych na systemie Linux i mogą być uruchamiane bezpośrednio z Windows. Na przykład można używać platformy Docker Aby uruchomić klienta z usługi Beeline gałęzi bezpośrednio z Windows. Można również korzystać z aparatu Docker do uruchamiania lokalnego notesu programu Jupyter i zdalne łączenie z platformą Spark na HDInsight. [Rozpoczynanie pracy z usługą Docker for Windows](https://docs.docker.com/docker-for-windows/)
* **[MobaXTerm](https://mobaxterm.mobatek.net/)**  umożliwia graficzne przeglądania systemu plików klastra za pośrednictwem połączenia SSH.

## <a name="cross-platform-tools"></a>Narzędzia dla wielu platform

Interfejs wiersza polecenia platformy Azure to wieloplatformowe środowisko wiersza polecenia do zarządzania zasobami platformy Azure.  Aby uzyskać więcej informacji, zobacz [interfejsu wiersza polecenia platformy Azure (CLI)](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest).

## <a name="next-steps"></a>Kolejne kroki
Jeśli dopiero zaczynasz pracę w klastrach opartych na systemie Linux, zobacz artykuły wykonaj:
* [Konfigurowanie technologii Apache Hadoop, Apache Kafka, Apache Spark lub inne klastry](hdinsight-hadoop-provision-linux-clusters.md)
* [Porady dotyczące klastrów HDInsight w systemie Linux](hdinsight-hadoop-linux-information.md)
