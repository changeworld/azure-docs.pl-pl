---
title: Zainstaluj lub zaktualizuj Mono w usłudze HDInsight - Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać określonej wersji Mono z klastrem usługi HDInsight. Mono służy do uruchamiania aplikacji .NET w klastrach HDInsight opartych na systemie Linux.
services: hdinsight
documentationCenter: ''
author: Blackmist
manager: cgronlun
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.devlang: ''
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/10/2018
ms.author: larryfr
ms.custom: hdinsightactive
ms.openlocfilehash: 165f1d8175c7c7b58a5eec02a208b81fe73cb5f9
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
ms.locfileid: "31400439"
---
# <a name="install-or-update-mono-on-hdinsight"></a>Zainstaluj lub zaktualizuj Mono w usłudze HDInsight

Dowiedz się, jak zainstalować określoną wersję [Mono](https://www.mono-project.com) HDInsight 3.4 lub nowszego.

Mono jest zainstalowany w wersji 3.4 HDInsight lub nowszy i służy do uruchamiania aplikacji .NET. Uzyskać informacji o wersji Mono dołączone do każdej wersji usługi HDInsight, zobacz [przechowywanie wersji składnika usługi HDInsight](hdinsight-component-versioning.md). Aby zainstalować w innej wersji w klastrze, należy użyć akcji skryptu w tym dokumencie. 

## <a name="how-it-works"></a>Jak to działa

Ten skrypt akceptuje następującego parametru:

* __Numer wersji mono__: wersja Mono do zainstalowania. Wersja musi być dostępny z [ https://download.mono-project.com/repo/debian/dists/wheezy/snapshots/ ](https://download.mono-project.com/repo/debian/dists/wheezy/snapshots/).

Skrypt instaluje Mono następujących pakietów:

* __Zakończenie mono__

* __ca-certificates-mono__

## <a name="the-script"></a>Skrypt

__Lokalizacja skryptu__: [https://hdiconfigactions.blob.core.windows.net/install-mono/install-mono.bash](https://hdiconfigactions.blob.core.windows.net/install-mono/install-mono.bash)

__Wymagania dotyczące__:

* Skrypt musi zostać zastosowana na __węzły główne__ i __węzłów procesu roboczego__.

## <a name="to-use-the-script"></a>Aby użyć skryptu

Aby uzyskać informacje dotyczące sposobu używania tego skryptu z usługą HDInsight, zobacz [klastrów usługi HDInsight opartej na dostosowanie systemu Linux przy użyciu akcji skryptu](hdinsight-hadoop-customize-cluster-linux.md#apply-a-script-action-to-a-running-cluster) dokumentu. Możesz użyć skryptu, za pośrednictwem portalu Azure, programu Azure PowerShell lub wiersza polecenia platformy Azure.

Podczas dokument akcji skryptu, użyj następującego identyfikatora URI:

    https://hdiconfigactions.blob.core.windows.net/install-mono/install-mono.bash

Aby określić Mono wersji, który jest zainstalowany, użyj numeru wersji w __parametry__ pola. Na przykład wprowadź `5.4` do zainstalowania Mono 5.4.

> [!NOTE]
> Podczas konfigurowania usługi HDInsight za pomocą tego skryptu należy oznaczyć skrypt jako __Persisted__. To ustawienie umożliwia HDInsight zastosować skrypt do węzłów procesu roboczego dodanych do skalowania operacji.

## <a name="next-steps"></a>Kolejne kroki

Zapoznaniu uaktualnić lub zainstalować określoną wersję Mono w usłudze HDInsight. Aby uzyskać więcej informacji na temat używania aplikacji .NET z Mono w usłudze HDInsight można znaleźć w następujących dokumentach:

* [Użyj .NET do przesyłania strumieniowego MapReduce w usłudze HDInsight](hadoop/apache-hadoop-dotnet-csharp-mapreduce-streaming.md)
* [.NET za pomocą technologii Hive i Pig w usłudze HDInsight](hadoop/apache-hadoop-hive-pig-udf-dotnet-csharp.md)
* [Tworzenie rozwiązań C# z systemu Storm w usłudze HDInsight](storm/apache-storm-develop-csharp-visual-studio-topology.md)
* [Migracja rozwiązań platformy .NET do usługi HDInsight opartej na systemie Linux](hdinsight-hadoop-migrate-dotnet-to-linux.md)

Aby uzyskać więcej informacji dotyczących za pomocą akcji skryptu, zobacz [klastrów usługi HDInsight opartej na dostosowanie systemu Linux przy użyciu akcji skryptu](hdinsight-hadoop-customize-cluster-linux.md)