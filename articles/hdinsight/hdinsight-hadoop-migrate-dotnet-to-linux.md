---
title: Używanie platformy .NET przy użyciu MapReduce usługi Hadoop w HDInsight opartych na systemie Linux — Azure
description: Dowiedz się, jak korzystać z aplikacji .NET do przesyłania strumieniowego MapReduce na HDInsight opartych na systemie Linux.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/27/2018
ms.author: hrasheed
ms.openlocfilehash: a1d1488840ca2b17c83f380af4fa24105bb36202
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64729484"
---
# <a name="migrate-net-solutions-for-windows-based-hdinsight-to-linux-based-hdinsight"></a>Migrowanie rozwiązań .NET dla oparte na Windows HDInsight do HDInsight opartych na systemie Linux

HDInsight opartych na systemie Linux klastrów użyj [Mono (https://mono-project.com) ](https://mono-project.com) do uruchamiania aplikacji .NET. Narzędzie mono umożliwia składniki .NET, takich jak MapReduce aplikacje za pomocą HDInsight opartych na systemie Linux. W tym dokumencie sposób Migrowanie rozwiązań .NET utworzone dla klastrów HDInsight z systemem Windows do pracy z platformy Mono na HDInsight opartych na systemie Linux.

## <a name="mono-compatibility-with-net"></a>Zgodność platformy mono przy użyciu platformy .NET

Wersja platformy mono 4.2.1 jest dołączony do HDInsight w wersji 3.6. Aby uzyskać więcej informacji na wersję platformy Mono dołączone HDInsight, zobacz [wersje składników HDInsight](hdinsight-component-versioning.md).

Aby uzyskać więcej informacji na temat zgodności platformy Mono i .NET, zobacz [zgodności platformy Mono (https://www.mono-project.com/docs/about-mono/compatibility/) ](https://www.mono-project.com/docs/about-mono/compatibility/) dokumentu.

> [!IMPORTANT]  
> W ramach platformy SCP.NET jest zgodny z platformy Mono. Aby uzyskać więcej informacji na temat korzystania z platformy SCP.NET dotyczącą platformy Mono, zobacz [Użyj programu Visual Studio umożliwiające tworzenie topologii języka C# dla Storm Apache na HDInsight](storm/apache-storm-develop-csharp-visual-studio-topology.md).

## <a name="automated-portability-analysis"></a>Analiza automatycznego przenoszenia

[Narzędzia .NET Portability Analyzer](https://marketplace.visualstudio.com/items?itemName=ConnieYau.NETPortabilityAnalyzer) może służyć do Generowanie raportu niezgodności między aplikacją i platformy Mono. Poniższe kroki umożliwiają konfigurowanie analizatora do sprawdzenia aplikacji przenośności Mono:

1. Zainstaluj [narzędzia .NET Portability Analyzer](https://marketplace.visualstudio.com/items?itemName=ConnieYau.NETPortabilityAnalyzer). Podczas instalacji wybierz wersję programu Visual Studio do użycia.

2. W programie Visual Studio 2015 wybierz __analizy__ > __Portability Analyzer ustawienia__i upewnij się, że __4.5__ jest zaewidencjonowany __Mono__ sekcji.

    ![4.5 zaewidencjonowany Mono sekcję ustawień analizatora](./media/hdinsight-hadoop-migrate-dotnet-to-linux/portability-analyzer-settings.png)

    Wybierz __OK__ Aby zapisać konfigurację.

3. Wybierz __analizowanie__ > __analizowanie przenośności zestawu__. Wybierz zestaw, który zawiera rozwiązanie, a następnie wybierz __Otwórz__ aby rozpocząć analizę.

4. Po zakończeniu analizy wybierz __analizy__ > __przeglądać raporty analityczne__. W __wyniki analizy przenośność__, wybierz opcję __Otwórz raport__ otwierania raportu.

    ![Okno dialogowe wyniki analizator przenośności](./media/hdinsight-hadoop-migrate-dotnet-to-linux/portability-analyzer-results.png)

> [!IMPORTANT]  
> Analizator nie może przechwytywać wszystkich problemów z rozwiązaniem. Na przykład ścieżka pliku `c:\temp\file.txt` jest uznawany za poprawny, jeśli narzędzie Mono jest systemem Windows. Tej samej ścieżce nie nadaje się na platformie Linux.

## <a name="manual-portability-analysis"></a>Przenośność ręcznej analizy

Wykonaj ręczne audyt kodu przy użyciu informacji w [przenośność aplikacji (https://www.mono-project.com/docs/getting-started/application-portability/) ](https://www.mono-project.com/docs/getting-started/application-portability/) dokumentu.

## <a name="modify-and-build"></a>Modyfikowanie i tworzenie

Można nadal używać programu Visual Studio do tworzenia rozwiązań .NET dla HDInsight. Należy jednak upewnić się, że projekt jest skonfigurowany do używania programu .NET Framework 4.5.

## <a name="deploy-and-test"></a>Wdrażanie i testowanie

Po zmodyfikowaniu rozwiązania, korzystając z zaleceniami z narzędzia .NET Portability Analyzer lub ręcznej analizy należy przetestować przy użyciu HDInsight. Rozwiązania w klastrze HDInsight opartych na systemie Linux do testowania może spowodować ujawnienie drobne problemy, które muszą zostać poprawione. Firma Microsoft zaleca, aby włączyć dodatkowe rejestrowanie w Twojej aplikacji podczas testowania.

Aby uzyskać więcej informacji na temat uzyskiwania dostępu do dzienników zobacz następujące dokumenty:

* [Dostęp do usługi Apache Hadoop YARN application loguje HDInsight opartych na systemie Linux](hdinsight-hadoop-access-yarn-app-logs-linux.md)

## <a name="next-steps"></a>Kolejne kroki

* [Używanie języka C# za pomocą technologii MapReduce w HDInsight](hadoop/apache-hadoop-dotnet-csharp-mapreduce-streaming.md)

* [Użyj C# funkcje zdefiniowane przez użytkownika przy użyciu Apache Hive i Apache Pig](hadoop/apache-hadoop-hive-pig-udf-dotnet-csharp.md)

* [Twórz C# topologii STORM Apache na HDInsight](storm/apache-storm-develop-csharp-visual-studio-topology.md)
