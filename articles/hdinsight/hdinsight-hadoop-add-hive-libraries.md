---
title: Dodawanie bibliotek technologii Hive podczas tworzenia klastra HDInsight — Azure
description: Dowiedz się, jak dodawanie bibliotek technologii Hive (pliki jar), do klastra usługi HDInsight podczas tworzenia klastra.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/27/2018
ms.author: hrasheed
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.openlocfilehash: fe8f97368531ed572083834256d84cd1ed6dd8a1
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62098307"
---
# <a name="add-custom-apache-hive-libraries-when-creating-your-hdinsight-cluster"></a>Dodawanie niestandardowych bibliotek technologii Hive, podczas tworzenia klastra usługi HDInsight

Dowiedz się, jak i wstępne ładowanie [Apache Hive](https://hive.apache.org/) bibliotek w HDInsight. Ten dokument zawiera informacje na temat korzystania z akcji skryptu do wstępnego ładowania bibliotek podczas tworzenia klastra. Biblioteki dodany, korzystając z procedury w tym dokumencie są ogólnie dostępne w gałęzi — nie ma potrzeby używania [Dodaj JAR](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+Cli) załadować je.

## <a name="how-it-works"></a>Jak to działa

Podczas tworzenia klastra, możesz użyć akcji skryptu, aby zmodyfikować węzłów klastra, ponieważ są one tworzone. Skrypt w tym dokumencie przyjmuje jeden parametr, czyli lokalizacji biblioteki. Lokalizacja musi być na koncie usługi Azure Storage, a bibliotekami muszą być przechowywane jako pliki jar.

Podczas tworzenia klastra skrypt wylicza pliki, kopiuje je do `/usr/lib/customhivelibs/` katalogu na węzły główne i proces roboczy, następnie dodanie ich do `hive.aux.jars.path` właściwość `core-site.xml` pliku. W klastrach opartych na systemie Linux, aktualizuje również `hive-env.sh` pliku z lokalizacji plików.

> [!NOTE]  
> Za pomocą akcji skryptu, w tym artykule sprawia, że biblioteki dostępne w następujących scenariuszach:
>
> * **HDInsight opartych na systemie Linux** — za pomocą klienta programu Hive, **WebHCat**, i **usługi HiveServer2**.
> * **HDInsight oparte na Windows** — podczas korzystania z klienta programu Hive i **WebHCat**.

## <a name="the-script"></a>Skrypt

**Lokalizacja skryptu**

Aby uzyskać **opartych na systemie Linux klastrów**: [https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh](https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh)

Aby uzyskać **klastrów Windows**: [https://hdiconfigactions.blob.core.windows.net/setupcustomhivelibsv01/setup-customhivelibs-v01.ps1](https://hdiconfigactions.blob.core.windows.net/setupcustomhivelibsv01/setup-customhivelibs-v01.ps1)

> [!IMPORTANT]  
> Linux jest jedynym systemem operacyjnym używanym w połączeniu z usługą HDInsight w wersji 3.4 lub nowszą. Aby uzyskać więcej informacji, zobacz sekcję [HDInsight retirement on Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement) (Wycofanie usługi HDInsight w systemie Windows).

**Wymagania**

* Skrypty muszą być stosowane do obu **węzłami głównymi** i **węzłów procesu roboczego**.

* Plikach JAR chcesz zainstalować muszą być przechowywane w usłudze Azure Blob Storage w **jeden kontener**.

* Konto magazynu zawierające biblioteki pliki jar **musi** można połączyć w klaster HDInsight podczas tworzenia. Musi to być domyślne konto magazynu lub konto dodane za pośrednictwem __opcjonalna konfiguracja__.

* Ścieżka WASB do kontenera należy określić jako parametru akcji skryptu. Na przykład, jeśli w plikach JAR są przechowywane w kontenerze o nazwie **libs** na koncie magazynu o nazwie **mój_magazyn**, wartość parametru może być **wasb://libs\@ mystorage.blob.Core.Windows.NET/**.

  > [!NOTE]  
  > W tym dokumencie przyjęto założenie, że masz już utworzone konto magazynu, kontener obiektów blob, a przekazać pliki do niego.
  >
  > Jeśli nie utworzono konta magazynu, możesz zrobić to za pomocą [witryny Azure portal](https://portal.azure.com). Następnie można użyć narzędzia takie jak [Eksploratora usługi Azure Storage](https://storageexplorer.com/) utworzyć kontener w ramach konta i przekazywanie plików do niego.

## <a name="create-a-cluster-using-the-script"></a>Tworzenie klastra przy użyciu skryptu

> [!NOTE]  
> Poniższe kroki umożliwiają utworzenie klastra HDInsight opartych na systemie Linux. Aby utworzyć klaster z systemem Windows, wybierz **Windows** jako klaster systemu operacyjnego, podczas tworzenia klastra, a następnie użyj skryptu Windows (PowerShell), zamiast skryptu powłoki systemowej.
>
> Aby utworzyć klaster przy użyciu tego skryptu, można użyć programu Azure PowerShell lub zestawu .NET SDK HDInsight. Aby uzyskać więcej informacji na temat korzystania z tych metod, zobacz [HDInsight Dostosowywanie klastrów za pomocą akcji skryptów](hdinsight-hadoop-customize-cluster-linux.md).

1. Uruchom aprowizację klastra wykonując kroki opisane w [Provision HDInsight clusters w systemie Linux](hdinsight-hadoop-provision-linux-clusters.md), ale inicjowania obsługi nie jest ukończona.

2. Na **opcjonalna konfiguracja** zaznacz **akcji skryptu**i podaj następujące informacje:

   * **NAZWA**: Wprowadź przyjazną nazwę dla akcji skryptu.

   * **IDENTYFIKATOR URI SKRYPTU**: https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh.

   * **HEAD**: Zaznacz tę opcję.

   * **PROCES ROBOCZY**: Zaznacz tę opcję.

   * **ZOOKEEPER**: Pozostaw to pole puste.

   * **PARAMETRY**: Wprowadź adres WASB kontener i konto magazynu zawierającego plikach JAR. Na przykład **wasb://libs\@mystorage.blob.core.windows.net/**.

3. W dolnej części **akcji skryptu**, użyj **wybierz** przycisk, aby zapisać konfigurację.

4. Na **opcjonalna konfiguracja** zaznacz **połączonych kontach usługi Storage** i wybierz **Dodaj klucz magazynu** łącza. Wybierz konto magazynu, który zawiera plikach JAR. Następnie użyj **wybierz** przycisków, aby zapisać ustawienia i wrócić **opcjonalna konfiguracja**.

5. Aby zapisać konfigurację opcjonalne, należy użyć **wybierz** znajdujący się u dołu **opcjonalna konfiguracja** sekcji.

6. Kontynuuj, inicjowania obsługi klastra zgodnie z opisem w [Provision HDInsight clusters w systemie Linux](hdinsight-hadoop-provision-linux-clusters.md).

Po zakończeniu tworzenia klastra, jesteś w stanie używać plikach JAR dodanego przez ten skrypt z programu Hive, bez konieczności używania `ADD JAR` instrukcji.

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji na temat pracy z technologią Hive, zobacz [używanie programu Apache Hive z HDInsight](hadoop/hdinsight-use-hive.md)
