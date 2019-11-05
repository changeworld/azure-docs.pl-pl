---
title: Apache Hive bibliotek podczas tworzenia klastra — Azure HDInsight
description: Dowiedz się, jak dodać biblioteki Apache Hive (pliki jar) do klastra usługi HDInsight podczas tworzenia klastra.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/27/2018
ms.author: hrasheed
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.openlocfilehash: 51a93aaec4abdb2dd9d8fad042c079a48d4ea7a3
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73494836"
---
# <a name="add-custom-apache-hive-libraries-when-creating-your-hdinsight-cluster"></a>Dodawanie niestandardowych bibliotek Apache Hive podczas tworzenia klastra usługi HDInsight

Dowiedz się, jak wstępnie ładować biblioteki [Apache Hive](https://hive.apache.org/) w usłudze HDInsight. Ten dokument zawiera informacje na temat używania akcji skryptu do wstępnego ładowania bibliotek podczas tworzenia klastra. Biblioteki dodane przy użyciu kroków opisanych w tym dokumencie są globalnie dostępne w programie Hive — nie ma potrzeby [dodawania pliku JAR](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+Cli) w celu ich załadowania.

## <a name="how-it-works"></a>Jak to działa

Podczas tworzenia klastra można użyć akcji skryptu, aby zmodyfikować węzły klastra podczas ich tworzenia. Skrypt w tym dokumencie przyjmuje jeden parametr, który jest lokalizacją bibliotek. Ta lokalizacja musi znajdować się na koncie usługi Azure Storage, a biblioteki muszą być przechowywane jako pliki jar.

Podczas tworzenia klastra skrypt wylicza pliki, kopiuje je do katalogu `/usr/lib/customhivelibs/` w węzłach głównych i procesów roboczych, a następnie dodaje je do właściwości `hive.aux.jars.path` w pliku `core-site.xml`. W klastrach opartych na systemie Linux program aktualizuje również plik `hive-env.sh` przy użyciu lokalizacji plików.

> [!NOTE]  
> Użycie akcji skryptu w tym artykule sprawia, że biblioteki są dostępne w następujących scenariuszach:
>
> * Usługa **HDInsight oparta na systemie Linux** — w przypadku korzystania z klienta Hive, **WebHCat**i **serwera hiveserver2**.
> * Usługa **HDInsight oparta na systemie Windows** — w przypadku korzystania z klienta Hive i **WebHCat**.

## <a name="the-script"></a>Skrypt

**Lokalizacja skryptu**

W przypadku **klastrów opartych na systemie Linux**: [https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh](https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh)

W przypadku **klastrów opartych na systemie Windows**: [https://hdiconfigactions.blob.core.windows.net/setupcustomhivelibsv01/setup-customhivelibs-v01.ps1](https://hdiconfigactions.blob.core.windows.net/setupcustomhivelibsv01/setup-customhivelibs-v01.ps1)

**Wymagania**

* Skrypty muszą być stosowane zarówno do **węzłów głównych** , jak i **węzłów procesu roboczego**.

* Jars, które chcesz zainstalować, muszą być przechowywane w usłudze Azure Blob Storage w **jednym kontenerze**.

* Podczas tworzenia konta magazynu zawierającego bibliotekę plików jar **należy** połączyć z klastrem usługi HDInsight. Musi to być domyślne konto magazynu lub konto dodane za pomocą __konfiguracji opcjonalnej__.

* Ścieżka WASB do kontenera musi być określona jako parametr akcji skryptu. Na przykład jeśli Jars są przechowywane w kontenerze o nazwie **libs** na koncie magazynu o nazwie Moja **Magazyn**, parametr będzie **wasb://libs\@MyStorage.blob.Core.Windows.NET/** .

  > [!NOTE]  
  > W tym dokumencie przyjęto założenie, że utworzono już konto magazynu, kontener obiektów blob i przekazano do niego pliki.
  >
  > Jeśli nie utworzono konta magazynu, możesz to zrobić za pomocą [Azure Portal](https://portal.azure.com). Następnie można użyć narzędzia, takiego jak [Eksplorator usługi Azure Storage](https://storageexplorer.com/) , aby utworzyć kontener na koncie i przekazać do niego pliki.

## <a name="create-a-cluster-using-the-script"></a>Tworzenie klastra przy użyciu skryptu

> [!NOTE]  
> W poniższych krokach opisano tworzenie klastra usługi HDInsight opartej na systemie Linux. Aby utworzyć klaster oparty na systemie Windows, wybierz pozycję **Windows** jako system operacyjny klastra podczas tworzenia klastra i Użyj skryptu Windows (PowerShell) zamiast skryptu bash.
>
> Do utworzenia klastra korzystającego z tego skryptu można również użyć Azure PowerShell lub zestawu .NET SDK usługi HDInsight. Aby uzyskać więcej informacji na temat korzystania z tych metod, zobacz [Dostosowywanie klastrów usługi HDInsight za pomocą akcji skryptu](hdinsight-hadoop-customize-cluster-linux.md).

1. Rozpocznij Inicjowanie obsługi klastra, wykonując czynności opisane w temacie [Inicjowanie obsługi klastrów usługi HDInsight w systemie Linux](hdinsight-hadoop-provision-linux-clusters.md), ale nie Ukończ aprowizacji.

2. W sekcji **Konfiguracja opcjonalna** wybierz pozycję **Akcje skryptu**, a następnie podaj następujące informacje:

   * **Nazwa**: wprowadź przyjazną nazwę akcji skryptu.

   * **Identyfikator URI skryptu**: https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh.

   * **Nagłówek**: zaznacz tę opcję.

   * **Proces roboczy**: zaznacz tę opcję.

   * **Dozorcy**: pozostaw to pole puste.

   * **Parametry**: wprowadź adres WASB do kontenera i konta magazynu, które zawiera Jars. Na przykład **wasb://libs\@MyStorage.blob.Core.Windows.NET/** .

3. W dolnej części **akcji skryptu**Użyj przycisku **Wybierz** , aby zapisać konfigurację.

4. W sekcji **Konfiguracja opcjonalna** wybierz pozycję **połączone konta magazynu** i wybierz łącze **Dodaj klucz magazynu** . Wybierz konto magazynu, które zawiera Jars. Następnie użyj przycisku **Wybierz** , aby zapisać ustawienia i zwrócić **opcjonalną konfigurację**.

5. Aby zapisać opcjonalną konfigurację, użyj przycisku **Wybierz** w dolnej części sekcji **Konfiguracja opcjonalna** .

6. Kontynuuj Inicjowanie obsługi klastra zgodnie z opisem w temacie [Inicjowanie obsługi klastrów usługi HDInsight w systemie Linux](hdinsight-hadoop-provision-linux-clusters.md).

Po zakończeniu tworzenia klastra możesz użyć Jars dodanego za pośrednictwem tego skryptu z Hive, bez konieczności używania instrukcji `ADD JAR`.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat pracy z usługą Hive, zobacz [Korzystanie z Apache Hive z usługą HDInsight](hadoop/hdinsight-use-hive.md)
