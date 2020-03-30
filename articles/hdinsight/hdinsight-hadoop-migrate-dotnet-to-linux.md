---
title: Użyj platformy .NET z programem Hadoop MapReduce na platformie HDInsight opartej na systemie Linux — Azure
description: Dowiedz się, jak używać aplikacji platformy .NET do przesyłania strumieniowego MapReduce na podstawie systemu Linux HDInsight.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/27/2018
ms.author: hrasheed
ms.openlocfilehash: 4b402975a151d26e8f335c07930274c156ac52fb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79272372"
---
# <a name="migrate-net-solutions-for-windows-based-hdinsight-to-linux-based-hdinsight"></a>Migrowanie rozwiązań platformy .NET dla systemu Windows HDInsight do systemu HDInsight opartych na systemie Linux

Klastry HDInsight oparte na systemie Linux używają [mono (https://mono-project.com) ](https://mono-project.com) do uruchamiania aplikacji .NET. Mono umożliwia korzystanie ze składników .NET, takich jak aplikacje MapReduce z systemem HDInsight opartym na systemie Linux. W tym dokumencie dowiesz się, jak przeprowadzić migrację rozwiązań platformy .NET utworzonych dla klastrów HDInsight opartych na systemie Windows, aby pracować z mono na podstawie systemu Linux HDInsight.

## <a name="mono-compatibility-with-net"></a>Zgodność mono z .NET

Mono w wersji 4.2.1 jest dołączony do hdinsight w wersji 3.6. Aby uzyskać więcej informacji na temat wersji mono dołączonej do programu HDInsight, zobacz [wersje składników HDInsight](hdinsight-component-versioning.md).

Aby uzyskać więcej informacji na temat zgodności między mono i .NET, zobacz [zgodność monohttps://www.mono-project.com/docs/about-mono/compatibility/) (dokument.](https://www.mono-project.com/docs/about-mono/compatibility/)

> [!IMPORTANT]  
> Struktura SCP.NET jest zgodna z Mono. Aby uzyskać więcej informacji na temat używania SCP.NET za pomocą mono, zobacz [Używanie programu Visual Studio do tworzenia topologii języka C# dla usługi Apache Storm w programie HDInsight](storm/apache-storm-develop-csharp-visual-studio-topology.md).

## <a name="automated-portability-analysis"></a>Zautomatyzowana analiza przenośności

[Analizator przenośności .NET](https://marketplace.visualstudio.com/items?itemName=ConnieYau.NETPortabilityAnalyzer) może służyć do generowania raportu niezgodności między aplikacją i mono. Aby skonfigurować analizator, należy wykonać następujące czynności, aby sprawdzić, czy aplikacja nie jest w stanie przenosić się do aplikacji:

1. Zainstaluj [analizator przenośności .NET](https://marketplace.visualstudio.com/items?itemName=ConnieYau.NETPortabilityAnalyzer). Podczas instalacji wybierz wersję programu Visual Studio do użycia.

2. W programie Visual Studio 2015 wybierz pozycję __Analizowanie__ > __ustawień analizatora przenośności__i upewnij się, że __4.5__ jest zaznaczone w sekcji __Mono.__

    ![4.5 sprawdzone w sekcji Mono dla ustawień analizatora](./media/hdinsight-hadoop-migrate-dotnet-to-linux/portability-analyzer-settings.png)

    Wybierz __przycisk OK,__ aby zapisać konfigurację.

3. Wybierz __opcję Analizuj__ > __przenośność zespołu analizy__. Wybierz zestaw zawierający rozwiązanie, a następnie __wybierz__ otwórz, aby rozpocząć analizę.

4. Po zakończeniu analizy wybierz pozycję __Analizuj__ > __raporty analizy widoku__. W __obszarze Wyniki analizy przenośności__wybierz pozycję Otwórz __raport,__ aby otworzyć raport.

    ![Okno dialogowe wyników analizatora przenośności](./media/hdinsight-hadoop-migrate-dotnet-to-linux/portability-analyzer-results.png)

> [!IMPORTANT]  
> Analizator nie można złapać każdy problem z rozwiązaniem. Na przykład ścieżka pliku `c:\temp\file.txt` jest uważany za OK, jeśli mono jest uruchomiony w systemie Windows. Ta sama ścieżka nie jest prawidłowa na platformie Linux.

## <a name="manual-portability-analysis"></a>Ręczna analiza przenośności

Wykonaj ręczny audyt kodu przy użyciu informacji zawartych w dokumencie [Przenoszenie aplikacji .https://www.mono-project.com/docs/getting-started/application-portability/) ](https://www.mono-project.com/docs/getting-started/application-portability/)

## <a name="modify-and-build"></a>Modyfikowanie i tworzenie

Program Visual Studio można nadal używać do tworzenia rozwiązań platformy .NET dla programu HDInsight. Należy jednak upewnić się, że projekt jest skonfigurowany do używania programu .NET Framework 4.5.

## <a name="deploy-and-test"></a>Wdrażanie i testowanie

Po zmodyfikowaniu rozwiązania przy użyciu zaleceń z analizatora przenoszenia .NET lub z analizy ręcznej, należy przetestować go za pomocą usługi HDInsight. Testowanie rozwiązania w klastrze HDInsight opartym na systemie Linux może ujawnić subtelne problemy, które należy poprawić. Zaleca się włączenie dodatkowego rejestrowania w aplikacji podczas testowania.

Aby uzyskać więcej informacji na temat uzyskiwania dostępu do dzienników, zobacz następujące dokumenty:

* [Dostęp do dzienników aplikacji Apache Hadoop YARN na bazie Linux HDInsight](hdinsight-hadoop-access-yarn-app-logs-linux.md)

## <a name="next-steps"></a>Następne kroki

* [Użyj języka C# z mapreduce na HDInsight](hadoop/apache-hadoop-dotnet-csharp-mapreduce-streaming.md)

* [Użyj funkcji zdefiniowanych przez użytkownika języka C# z Apache Hive i Apache Pig](hadoop/apache-hadoop-hive-pig-udf-dotnet-csharp.md)

* [Opracowanie topologii C# dla Apache Storm na HDInsight](storm/apache-storm-develop-csharp-visual-studio-topology.md)
