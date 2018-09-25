---
title: Instalowanie lub aktualizowanie środowiska Mono na HDInsight — Azure
description: Dowiedz się, jak korzystać z określoną wersją platformy Mono z klastrem HDInsight. Narzędzie mono jest używane do uruchamiania aplikacji .NET w klastrach HDInsight opartych na systemie Linux.
services: hdinsight
author: jasonwhowell
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/10/2018
ms.author: jasonh
ms.custom: hdinsightactive
ms.openlocfilehash: 54e5a5b72627dc9cd2d842ccb24d10e2f9ab9dce
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46957180"
---
# <a name="install-or-update-mono-on-hdinsight"></a>Instalowanie lub aktualizowanie środowiska Mono na HDInsight

Dowiedz się, jak zainstalować określoną wersję [Mono](https://www.mono-project.com) HDInsight 3.4 lub nowszej.

Narzędzie mono jest zainstalowany na HDInsight 3.4 lub nowszej i służy do uruchamiania aplikacji .NET. Aby uzyskać informacji o wersji platformy Mono, dołączone do każdej wersji HDInsight, zobacz [przechowywanie wersji składnika HDInsight](hdinsight-component-versioning.md). Aby zainstalować inną wersję w klastrze, należy użyć akcji skryptu w tym dokumencie. 

## <a name="how-it-works"></a>Jak to działa

Ten skrypt akceptuje następujący parametr:

* __Numer wersji środowiska mono__: wersja platformy Mono do zainstalowania. Wersja musi być dostępny z [ https://download.mono-project.com/repo/debian/dists/wheezy/snapshots/ ](https://download.mono-project.com/repo/debian/dists/wheezy/snapshots/).

Skrypt instaluje następujące pakiety narzędzia Mono:

* __Wykonaj platformy mono__

* __ca-certificates-mono__

## <a name="the-script"></a>Skrypt

__Lokalizację skryptu__: [https://hdiconfigactions.blob.core.windows.net/install-mono/install-mono.bash](https://hdiconfigactions.blob.core.windows.net/install-mono/install-mono.bash)

__Wymagania dotyczące__:

* Skrypt musi zostać zastosowana na __węzłami głównymi__ i __węzłów procesu roboczego__.

## <a name="to-use-the-script"></a>Aby użyć skryptu

Aby uzyskać informacje na temat tego skryptu za pomocą HDInsight, zobacz [HDInsight opartych na systemie Linux z Dostosowywanie klastrów za pomocą akcji skryptu](hdinsight-hadoop-customize-cluster-linux.md#apply-a-script-action-to-a-running-cluster) dokumentu. Można użyć skryptu za pomocą witryny Azure portal, programu Azure PowerShell lub interfejsu wiersza polecenia do klasycznego Azure.

Podczas poniższy dokument akcji skryptu, użyj następujących identyfikatora URI:

    https://hdiconfigactions.blob.core.windows.net/install-mono/install-mono.bash

Aby określić wersję platformy Mono, która jest zainstalowana, użyj numeru wersji __parametry__ pola. Na przykład, wprowadź `5.4` zainstalował 5.4 platformy Mono.

> [!NOTE]
> Podczas konfigurowania HDInsight za pomocą tego skryptu należy oznaczyć skrypt jako __utrwalone__. To ustawienie umożliwia HDInsight zastosować skrypt do węzłów procesu roboczego dodanych do skalowania operacji.

## <a name="next-steps"></a>Kolejne kroki

Masz pokazaliśmy ci, jak uaktualnić lub zainstalować określoną wersję platformy Mono na HDInsight. Aby uzyskać więcej informacji na temat korzystania z aplikacji .NET za pomocą platformy Mono na HDInsight zobacz następujące dokumenty:

* [Do przesyłania strumieniowego MapReduce na HDInsight przy użyciu .NET](hadoop/apache-hadoop-dotnet-csharp-mapreduce-streaming.md)
* [Używanie platformy .NET przy użyciu technologii Hive i Pig na HDInsight](hadoop/apache-hadoop-hive-pig-udf-dotnet-csharp.md)
* [Tworzenie rozwiązań języka C# przy użyciu systemu Storm w HDInsight](storm/apache-storm-develop-csharp-visual-studio-topology.md)
* [Migrowanie rozwiązań .NET do HDInsight opartych na systemie Linux](hdinsight-hadoop-migrate-dotnet-to-linux.md)

Aby uzyskać więcej informacji na temat korzystania z akcji skryptu, zobacz [HDInsight opartych na systemie Linux z Dostosowywanie klastrów za pomocą akcji skryptu](hdinsight-hadoop-customize-cluster-linux.md)
