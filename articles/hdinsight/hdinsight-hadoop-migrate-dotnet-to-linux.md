---
title: .NET za pomocą MapReduce z Hadoop w usłudze HDInsight opartych na systemie Linux - Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak korzystać z aplikacji .NET do przesyłania strumieniowego MapReduce na HDInsight opartych na systemie Linux.
services: hdinsight
documentationCenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: ''
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 02/27/2018
ms.author: larryfr
ms.openlocfilehash: 36b8f51122bad6614e63dfc58e09e5c1ca08f83d
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
ms.locfileid: "31400072"
---
# <a name="migrate-net-solutions-for-windows-based-hdinsight-to-linux-based-hdinsight"></a>Migracja rozwiązań .NET dla komputerów z systemem Windows usługi HDInsight w usłudze HDInsight opartych na systemie Linux

Opartych na systemie Linux klastrów usługi HDInsight użyj [Mono (https://mono-project.com) ](https://mono-project.com) na uruchamianie aplikacji .NET. Mono umożliwia .NET składników, takich jak MapReduce aplikacje za pomocą usługi HDInsight opartej na systemie Linux. W tym dokumencie Dowiedz się, jak przeprowadzić migrację rozwiązania .NET utworzone dla komputerów z systemem Windows w usłudze hdinsight do pracy z Mono na HDInsight opartych na systemie Linux.

## <a name="mono-compatibility-with-net"></a>Mono zgodności z platformą .NET

Wersja mono 4.2.1 jest uwzględniona w usłudze HDInsight w wersji 3,6. Aby uzyskać więcej informacji w wersji Mono dołączone do usługi HDInsight, zobacz [wersji składnika usługi HDInsight](hdinsight-component-versioning.md). Aby zainstalować określoną wersję Mono, zobacz [Zainstaluj lub zaktualizuj Mono](hdinsight-hadoop-install-mono.md) dokumentu.

Aby uzyskać więcej informacji o zgodności Mono i .NET, zobacz [Mono zgodności (http://www.mono-project.com/docs/about-mono/compatibility/) ](http://www.mono-project.com/docs/about-mono/compatibility/) dokumentu.

> [!IMPORTANT]
> W ramach SCP.NET jest zgodny z Mono. Aby uzyskać więcej informacji o używaniu SCP.NET z Mono, zobacz [program Visual Studio umożliwia tworzenie topologii C# dla Apache Storm w usłudze HDInsight](storm/apache-storm-develop-csharp-visual-studio-topology.md).

## <a name="automated-portability-analysis"></a>Przenośność zautomatyzowanych analiz

[Analizator przenośność .NET](https://marketplace.visualstudio.com/items?itemName=ConnieYau.NETPortabilityAnalyzer) może służyć do generowania raportu dotyczącego niezgodności między aplikacji i Mono. Do skonfigurowania analizatora do sprawdzenia aplikacji przenośności Mono, wykonaj następujące kroki:

1. Zainstaluj [analizator przenośność .NET](https://marketplace.visualstudio.com/items?itemName=ConnieYau.NETPortabilityAnalyzer). Podczas instalacji wybierz wersję programu Visual Studio do użycia.

2. Z programu Visual Studio 2015, wybierz __Analizuj__ > __ustawień analizatora przenośność__i upewnij się, że __4.5__ zaewidencjonowania __Mono__ sekcji.

    ![4.5 zaewidencjonowany Mono sekcji ustawień analizatora](./media/hdinsight-hadoop-migrate-dotnet-to-linux/portability-analyzer-settings.png)

    Wybierz __OK__ Aby zapisać konfigurację.

3. Wybierz __analizowanie__ > __analizowanie przenośności zestawu__. Wybierz zestaw, który zawiera rozwiązania, a następnie wybierz __Otwórz__ rozpocząć analizy.

4. Po zakończeniu analizy wybierz __Analizuj__ > __przeglądać raporty analityczne__. W __wyniki analizy przenośność__, wybierz pozycję __Otwórz raport__ aby otworzyć raport.

    ![Przenośność analizatora wyniki w oknie dialogowym](./media/hdinsight-hadoop-migrate-dotnet-to-linux/portability-analyzer-results.png)

> [!IMPORTANT]
> Analizatora nie może przechwycić każdy problem z rozwiązania. Na przykład ścieżka pliku `c:\temp\file.txt` jest uznawany za poprawny, jeśli Mono działa w systemie Windows. Tej samej ścieżki nie jest prawidłowy na platformie systemu Linux.

## <a name="manual-portability-analysis"></a>Ręczne przenośność analizy

Wykonaj inspekcji ręcznej w kodzie, korzystając z informacji w [przenośność aplikacji (http://www.mono-project.com/docs/getting-started/application-portability/) ](http://www.mono-project.com/docs/getting-started/application-portability/) dokumentu.

## <a name="modify-and-build"></a>Zmodyfikuj i kompilacji

Można nadal używać programu Visual Studio do tworzenia rozwiązań .NET dla usługi HDInsight. Należy jednak upewnij się, że projekt jest skonfigurowany do używania programu .NET Framework 4.5.

## <a name="deploy-and-test"></a>Wdrażanie i testowanie

Po zmodyfikowaniu rozwiązania przy użyciu zalecenia z analizatora przenośność .NET lub ręcznej analizy, należy przetestować z usługą HDInsight. Testowanie rozwiązania w klastrze usługi HDInsight opartej na systemie Linux może ujawnić niewielkie problemy, które muszą zostać poprawione. Firma Microsoft zaleca włączyć dodatkowe rejestrowanie w Twojej aplikacji podczas testowania go.

Aby uzyskać więcej informacji dotyczących uzyskiwania dostępu do dzienników można znaleźć w następujących dokumentach:

* [Uzyskiwanie dostępu do dzienników aplikacji usługi YARN w usłudze HDInsight opartej na systemie Linux](hdinsight-hadoop-access-yarn-app-logs-linux.md)

## <a name="next-steps"></a>Kolejne kroki

* [Używanie języka C# z MapReduce w usłudze HDInsight](hadoop/apache-hadoop-dotnet-csharp-mapreduce-streaming.md)

* [C# zdefiniowane przez użytkownika funkcji za pomocą technologii Hive i Pig](hadoop/apache-hadoop-hive-pig-udf-dotnet-csharp.md)

* [Tworzenie topologii C# dla Storm w usłudze HDInsight](storm/apache-storm-develop-csharp-visual-studio-topology.md)
