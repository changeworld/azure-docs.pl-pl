---
title: Korzystanie z komputera z systemem Windows z usługą Hadoop na platformie HDInsight — Azure
description: Praca z komputera z systemem Windows w Hadoop na HDInsight. Zarządzanie klastrami i wykonywanie zapytań za pomocą narzędzi programu PowerShell, Visual Studio i Linux. Opracowywać rozwiązania dla dużych zbiorów danych za pomocą platformy .NET.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 12/20/2019
ms.openlocfilehash: 3ec50acc693452fe73d929effcea98b12fc5ff8b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75933934"
---
# <a name="work-in-the-apache-hadoop-ecosystem-on-hdinsight-from-a-windows-pc"></a>Praca w ekosystemie Apache Hadoop na HDInsight z komputera z systemem Windows

Dowiedz się więcej o opcjach tworzenia i zarządzania na komputerze z systemem Windows do pracy w ekosystemie Apache Hadoop w programie HDInsight.

HDInsight opiera się na komponentach Apache Hadoop i Hadoop, technologiach open source opracowanych na Linuksie. HDInsight w wersji 3.4 i nowszej używa dystrybucji Ubuntu Linux jako bazowego systemu operacyjnego dla klastra. Można jednak pracować z programem HDInsight z klienta systemu Windows lub środowiska programistycznego systemu Windows.

## <a name="use-powershell-for-deployment-and-management-tasks"></a>Używanie programu PowerShell do wdrażania i zarządzania zadaniami

Azure PowerShell to środowisko skryptów, którego można używać do kontrolowania i automatyzacji zadań wdrażania i zarządzania w programie HDInsight z systemu Windows.

Przykłady zadań wykonywanych za pomocą programu PowerShell:

* [Tworzenie klastrów przy użyciu programu PowerShell](hdinsight-hadoop-create-linux-clusters-azure-powershell.md).
* [Uruchom kwerendy gałęzi Apache przy użyciu programu PowerShell](hadoop/apache-hadoop-use-hive-powershell.md).
* [Zarządzanie klastrami za pomocą programu PowerShell](hdinsight-administer-use-powershell.md).

Wykonaj kroki, aby [zainstalować i skonfigurować program Azure Powershell,](https://docs.microsoft.com/powershell/azure/install-az-ps) aby uzyskać najnowszą wersję.

## <a name="utilities-you-can-run-in-a-browser"></a>Narzędzia, które można uruchomić w przeglądarce

Następujące narzędzia mają interfejs użytkownika sieci Web, który działa w przeglądarce:
* **[Usługa Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview)** to interaktywna powłoka wiersza polecenia, która działa w przeglądarce i z poziomu witryny Azure portal.

* **[Apache Ambari Web UI](hdinsight-hadoop-manage-ambari.md)** to narzędzie do zarządzania i monitorowania dostępne w witrynie Azure portal, które może służyć do zarządzania różnymi rodzajami zadań, takimi jak:
    * [Użyj Apache Ambari z interfejsem API REST](hdinsight-hadoop-manage-ambari-rest-api.md)
    * [Apache Hive Widok w Apache Ambari](hadoop/apache-hadoop-use-hive-ambari-view.md)
    * [Apache Tez Zobacz w Apache Ambari](hdinsight-debug-ambari-tez-view.md)

## <a name="data-lake-hadoop-tools-for-visual-studio"></a>Narzędzia usługi Data Lake (Hadoop) dla programu Visual Studio

Narzędzia usługi Data Lake Tools dla programu Visual Studio umożliwia wdrażanie topologii burzy i zarządzanie nimi. Usługa Data Lake Tools instaluje również SCP.NET zestaw SDK, który umożliwia tworzenie topologii burzy języka C# w programie Visual Studio.

Przed udaniem się do poniższych przykładów [należy zainstalować i wypróbować narzędzia usługi Data Lake Tools for Visual Studio](hadoop/apache-hadoop-visual-studio-tools-get-started.md).

Przykłady zadań, które można wykonać za pomocą programu Visual Studio i narzędzia usługi Data Lake Tools dla programu Visual Studio:
* [Wdrażanie topologii storm i zarządzanie nimi w programie Visual Studio](storm/apache-storm-deploy-monitor-topology-linux.md)
* [Opracowanie topologii języka C# dla programu Storm przy użyciu programu Visual Studio](storm/apache-storm-develop-csharp-visual-studio-topology.md). Bity obejmują przykładowe szablony topologii storm, które można połączyć z bazami danych, takimi jak Usługa Azure Cosmos DB i baza danych SQL.

## <a name="visual-studio-and-the-net-sdk"></a>Visual Studio i sdk .NET

Program Visual Studio za pomocą zestawu .NET SDK służy do zarządzania klastrami i tworzenia aplikacji do dużych zbiorów danych. Można użyć innych identyfikatorów dla następujących zadań, ale przykłady są wyświetlane w programie Visual Studio.

Przykłady zadań, które można wykonać za pomocą pliku .NET SDK w programie Visual Studio:
* [Zestaw SDK usługi Azure HDInsight dla platformy .NET](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight?view=azure-dotnet).
* [Uruchom kwerendy gałęzi Apache przy użyciu pliku .NET SDK](hadoop/apache-hadoop-use-hive-dotnet-sdk.md).
* [Użyj funkcji zdefiniowanych przez użytkownika języka C# z apache hive i apache pig strumieniowego na Apache Hadoop](hadoop/apache-hadoop-hive-pig-udf-dotnet-csharp.md).

## <a name="intellij-idea-and-eclipse-ide-for-spark-clusters"></a>Intellij IDEA i Eclipse IDE dla klastrów Spark

Zarówno [Intellij IDEA,](https://www.jetbrains.com/idea/download) jak i [Eclipse IDE](https://www.eclipse.org/downloads/) mogą być używane do:
* Tworzenie i przesyłanie aplikacji Scala Spark w klastrze HDInsight Spark.
* Dostęp do zasobów klastra Platformy Spark.
* Lokalnie twórz i uruchamiaj aplikację Scala Spark.

W tych artykułach pokazano, jak:
* Intellij IDEA: [Tworzenie aplikacji Apache Spark przy użyciu zestawu narzędzi Azure dla wtyczki Intellij i scala SDK.](spark/apache-spark-intellij-tool-plugin.md)
* Eclipse IDE lub Scala IDE for Eclipse: [Tworzenie aplikacji Apache Spark i zestawu narzędzi Azure dla programu Eclipse](spark/apache-spark-eclipse-tool-plugin.md)

## <a name="notebooks-on-spark-for-data-scientists"></a>Notesy na platformie Spark dla analityków danych

Klastry Apache Spark w HDInsight obejmują notebooki Apache Zeppelin i jądra, które mogą być używane z notebookami Jupyter.

* [Dowiedz się, jak używać jąder w klastrach Apache Spark z notesami Jupyter do testowania aplikacji Spark](spark/apache-spark-zeppelin-notebook.md)
* [Dowiedz się, jak uruchamiać zadania programu Spark za pomocą notesów Apache Zeppelin w klastrach Apache Spark](spark/apache-spark-jupyter-notebook-kernels.md)

## <a name="run-linux-based-tools-and-technologies-on-windows"></a>Uruchamianie narzędzi i technologii opartych na systemie Linux w systemie Windows

Jeśli natkniesz się na sytuację, w której musisz użyć narzędzia lub technologii, która jest dostępna tylko w systemie Linux, rozważ następujące opcje:

* **Bash na Ubuntu w systemie Windows 10** zapewnia podsystem Linux w systemie Windows. Bash pozwala na bezpośrednie uruchamianie narzędzi Linuksa bez konieczności utrzymywania dedykowanej instalacji Linuksa. Aby uzyskać instrukcje instalacji, zobacz [Podręcznik instalacji systemu Windows dla systemu Linux dla systemu Windows 10.](https://docs.microsoft.com/windows/wsl/install-win10)  Inne [powłoki Unix](https://www.gnu.org/software/bash/) będzie działać, jak również.
* **Docker dla systemu Windows** zapewnia dostęp do wielu narzędzi opartych na systemie Linux i można go uruchamiać bezpośrednio z systemu Windows. Na przykład można użyć platformy Docker do uruchomienia klienta Beeline dla gałęzi bezpośrednio z systemu Windows. Za pomocą platformy Docker można również uruchomić lokalny notes Jupyter i zdalnie połączyć się z spark na hdinsight. [Wprowadzenie do platformy Docker dla systemu Windows](https://docs.docker.com/docker-for-windows/)
* **[MobaXTerm](https://mobaxterm.mobatek.net/)** umożliwia graficzne przeglądanie systemu plików klastra za oknem SSH.

## <a name="cross-platform-tools"></a>Narzędzia międzyplatformowe

Interfejs wiersza polecenia platformy Azure to wieloplatformowe środowisko wiersza polecenia do zarządzania zasobami platformy Azure.  Aby uzyskać więcej informacji, zobacz [Interfejs wiersza polecenia platformy Azure (CLI)](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest).

## <a name="next-steps"></a>Następne kroki

Jeśli jesteś nowy w pracy w klastrach opartych na systemie Linux, zobacz następujące artykuły:
* [Konfigurowanie Apache Hadoop, Apache Kafka, Apache Spark lub innych klastrów](hdinsight-hadoop-provision-linux-clusters.md)
* [Porady dotyczące klastrów HDInsight w systemie Linux](hdinsight-hadoop-linux-information.md)
