---
title: Dodawanie bibliotek technologii Hive podczas tworzenia klastra usługi HDInsight - Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak dodać biblioteki gałąź (pliki jar), do klastra usługi HDInsight podczas tworzenia klastra.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 2fd74b8d-c006-45c6-a9e2-72ff5d2d978a
ms.service: hdinsight
ms.devlang: na
ms.topic: conceptual
ms.date: 02/27/2018
ms.author: larryfr
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.openlocfilehash: 71e2859085dc4a9f4fa327d88faff4fecf5108ef
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="add-custom-hive-libraries-when-creating-your-hdinsight-cluster"></a>Dodawanie niestandardowych bibliotek technologii Hive, podczas tworzenia klastra usługi HDInsight

Dowiedz się, jak wstępne ładowanie bibliotek technologii Hive w usłudze HDInsight. Ten dokument zawiera informacje dotyczące wstępnie załadować biblioteki podczas tworzenia klastra przy użyciu akcji skryptu. Biblioteki dodane przy użyciu kroków w tym dokumencie są globalnie dostępną w gałęzi — nie trzeba używać [dodać JAR](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+Cli) załadować je.

## <a name="how-it-works"></a>Jak to działa

Podczas tworzenia klastra, można użyć akcji skryptu można zmodyfikować węzłów klastra, jak będą one tworzone. Skrypt w tym dokumencie przyjmuje jeden parametr, który jest lokalizacja biblioteki. Lokalizacja musi być na koncie magazynu Azure i biblioteki muszą być przechowywane jako pliki jar.

Podczas tworzenia klastra, skrypt wylicza pliki, kopiuje je do `/usr/lib/customhivelibs/` katalogu w węzłach head i proces roboczy, następnie dodanie ich do `hive.aux.jars.path` właściwości w `core-site.xml` pliku. W klastrach opartych na systemie Linux, aktualizuje również `hive-env.sh` pliku z lokalizacji plików.

> [!NOTE]
> Za pomocą akcji skryptu w tym artykule udostępnia biblioteki w następujących scenariuszach:
>
> * **HDInsight opartych na systemie Linux** — za pomocą klienta Hive **WebHCat**, i **serwera HiveServer2**.
> * **HDInsight opartych na systemie Windows** — za pomocą klienta programu Hive i **WebHCat**.

## <a name="the-script"></a>Skrypt

**Lokalizacja skryptu**

Aby uzyskać **opartych na systemie Linux klastrów**: [https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh](https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh)

Aby uzyskać **klastry z systemem Windows**: [https://hdiconfigactions.blob.core.windows.net/setupcustomhivelibsv01/setup-customhivelibs-v01.ps1](https://hdiconfigactions.blob.core.windows.net/setupcustomhivelibsv01/setup-customhivelibs-v01.ps1)

> [!IMPORTANT]
> Linux jest jedynym systemem operacyjnym używanym w połączeniu z usługą HDInsight w wersji 3.4 lub nowszą. Aby uzyskać więcej informacji, zobacz sekcję [HDInsight retirement on Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement) (Wycofanie usługi HDInsight w systemie Windows).

**Wymagania**

* Skrypty należy zastosować do obu **węzły główne** i **węzłów procesu roboczego**.

* Słoików chcesz zainstalować musi być przechowywany w magazynie obiektów Blob Azure w **jeden kontener**.

* Konto magazynu zawierające biblioteki pliki jar **musi** można połączyć się z klastrem usługi HDInsight podczas tworzenia. Musi być domyślne konto magazynu, lub dodać konta za pośrednictwem __konfiguracji opcjonalnej__.

* Jako parametr akcji skryptu można określić ścieżkę WASB do kontenera. Na przykład, jeśli słoików są przechowywane w kontenerze o nazwie **libs** na konto magazynu o nazwie **mystorage**, będzie parametr **wasb://libs@mystorage.blob.core.windows.net/**.

  > [!NOTE]
  > Tym dokumencie przyjęto założenie, że masz już utworzone konto magazynu, kontenera obiektów blob, a przekazać pliki do niego.
  >
  > Jeśli nie utworzono konto magazynu, możesz to zrobić za pomocą programu tak [portalu Azure](https://portal.azure.com). Następnie można użyć narzędzia takie jak [Eksploratora usługi Storage Azure](http://storageexplorer.com/) utworzyć kontener w ramach konta i przekazać pliki do niego.

## <a name="create-a-cluster-using-the-script"></a>Tworzenie klastra przy użyciu skryptu

> [!NOTE]
> Poniższe kroki tworzenia klastra usługi HDInsight opartej na systemie Linux. Do utworzenia klastra z systemem Windows, zaznacz **Windows** jako klaster systemu operacyjnego podczas tworzenia klastra, a następnie użyj skryptu systemu Windows (PowerShell) zamiast skryptu bash.
>
> Aby utworzyć klaster przy użyciu tego skryptu, można użyć programu Azure PowerShell lub zestawu .NET SDK usługi HDInsight. Aby uzyskać więcej informacji o używaniu tych metod, zobacz [Dostosowywanie klastrów usługi HDInsight z akcji skryptu](hdinsight-hadoop-customize-cluster-linux.md).

1. Uruchom inicjowania obsługi klastra, wykonując kroki opisane w [Obsługa administracyjna klastrów HDInsight w systemie Linux](hdinsight-hadoop-provision-linux-clusters.md), kroków inicjowania obsługi administracyjnej.

2. Na **konfiguracji opcjonalnej** zaznacz **akcji skryptu**i podaj następujące informacje:

   * **Nazwa**: Wprowadź przyjazną nazwę dla akcji skryptu.

   * **IDENTYFIKATOR URI SKRYPTU**: https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh

   * **HEAD**: Zaznaczenie tego pola wyboru.

   * **Proces ROBOCZY**: Zaznaczenie tego pola wyboru.

   * **DOZORCY**: to pole pozostanie puste.

   * **Parametry**: Wprowadź adres WASB do wybranego konta magazynu i kontenera zawiera słoików. Na przykład **wasb://libs@mystorage.blob.core.windows.net/**.

3. W dolnej części **akcji skryptu**, użyj **wybierz** przycisk, aby zapisać konfigurację.

4. Na **konfiguracji opcjonalnej** zaznacz **połączonych kontach magazynu** i wybierz **Dodaj klucz magazynu** łącza. Wybierz konto magazynu, który zawiera słoików. Następnie użyj **wybierz** przycisków, aby zapisać ustawienia i wrócić **konfiguracji opcjonalnej**.

5. Aby zapisać konfiguracji opcjonalnej, użyj **wybierz** przycisk w dolnej części **konfiguracji opcjonalnej** sekcji.

6. Kontynuuj, inicjowania obsługi klastra, zgodnie z opisem w [Obsługa administracyjna klastrów HDInsight w systemie Linux](hdinsight-hadoop-provision-linux-clusters.md).

Po zakończeniu tworzenia klastra, będą mogli używać słoików dodane za pomocą tego skryptu z Hive, bez konieczności używania `ADD JAR` instrukcji.

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji na temat pracy z Hive, zobacz [używanie Hive z usługą HDInsight](hadoop/hdinsight-use-hive.md)
