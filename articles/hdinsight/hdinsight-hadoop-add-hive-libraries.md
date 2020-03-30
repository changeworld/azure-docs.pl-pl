---
title: Biblioteki hive Apache podczas tworzenia klastra — Usługa Azure HDInsight
description: Dowiedz się, jak dodać biblioteki (pliki jar) programu Apache hive do klastra HDInsight podczas tworzenia klastra.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.date: 02/14/2020
ms.openlocfilehash: 0b746963cea5a950ba47d8b4dfeb074cb0910436
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77471027"
---
# <a name="add-custom-apache-hive-libraries-when-creating-your-hdinsight-cluster"></a>Dodawanie niestandardowych bibliotek gałęzi Apache podczas tworzenia klastra usługi HDInsight

Dowiedz się, jak wstępnie załadować [biblioteki gałęzi Apache](https://hive.apache.org/) w programie HDInsight. Ten dokument zawiera informacje dotyczące używania akcji skryptu do wstępnego ładowania bibliotek podczas tworzenia klastra. Biblioteki dodane przy użyciu kroków w tym dokumencie są globalnie dostępne w gałęzi — nie ma potrzeby używania [ADD JAR,](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+Cli) aby je załadować.

## <a name="how-it-works"></a>Jak to działa

Podczas tworzenia klastra można użyć akcji skryptu, aby zmodyfikować węzły klastra podczas ich tworzenia. Skrypt w tym dokumencie akceptuje pojedynczy parametr, który jest lokalizacją bibliotek. Ta lokalizacja musi znajdować się na koncie usługi Azure Storage, a biblioteki muszą być przechowywane jako pliki jar.

Podczas tworzenia klastra skrypt wylicza pliki, kopiuje je do `/usr/lib/customhivelibs/` katalogu na węzłach `hive.aux.jars.path` head i `core-site.xml` worker, a następnie dodaje je do właściwości w pliku. W klastrach opartych na systemie `hive-env.sh` Linux aktualizuje również plik z lokalizacją plików.

Użycie akcji skryptu w tym artykule powoduje udostępnieniu bibliotek podczas korzystania z klienta hive dla **programów WebHCat**i **HiveServer2**.

## <a name="the-script"></a>Skrypt

**Lokalizacja skryptu**

[https://hdiconfigactions.blob.core.windows.net/setupcustomhivelibsv01/setup-customhivelibs-v01.ps1](https://hdiconfigactions.blob.core.windows.net/setupcustomhivelibsv01/setup-customhivelibs-v01.ps1)

### <a name="requirements"></a>Wymagania

* Skrypty muszą być stosowane zarówno do **węzłów głównego,** jak i **węzłów worker**.

* Słoiki, które chcesz zainstalować, muszą być przechowywane w usłudze Azure Blob Storage w **jednym kontenerze.**

* Konto magazynu zawierające bibliotekę plików jar **musi** być połączone z klastrem HDInsight podczas tworzenia. Musi to być domyślne konto magazynu lub konto dodane za pośrednictwem __ustawień konta magazynu__.

* Ścieżka WASB do kontenera musi być określona jako parametr akcji skryptu. Na przykład, jeśli słoiki są przechowywane w kontenerze o nazwie libs na **koncie** magazynu o nazwie **mystorage,** parametrem będzie `wasbs://libs@mystorage.blob.core.windows.net/`.

  > [!NOTE]  
  > W tym dokumencie założono, że utworzono już konto magazynu, kontener obiektów blob i przekażesz do niego pliki.
  >
  > Jeśli konto magazynu nie zostało utworzone, można to zrobić za pośrednictwem [witryny Azure portal](https://portal.azure.com). Następnie można użyć narzędzia, takiego jak [Eksplorator usługi Azure Storage,](https://storageexplorer.com/) aby utworzyć kontener na koncie i przekazać do niego pliki.

## <a name="create-a-cluster-using-the-script"></a>Tworzenie klastra przy użyciu skryptu

1. Rozpocznij inicjowanie obsługi administracyjnej klastra przy użyciu kroków w [programie Provision HDInsight klastrów w systemie Linux](hdinsight-hadoop-provision-linux-clusters.md), ale nie zakończyć inicjowania obsługi administracyjnej. Można również użyć programu Azure PowerShell lub HDInsight .NET SDK do utworzenia klastra przy użyciu tego skryptu. Aby uzyskać więcej informacji na temat korzystania z tych metod, zobacz [Dostosowywanie klastrów HDInsight za pomocą akcji skryptów](hdinsight-hadoop-customize-cluster-linux.md). W przypadku witryny Azure portal na karcie **Konfiguracja + cennik** wybierz **akcję + Dodaj skrypt**.

1. W przypadku **magazynu**, jeśli konto magazynu zawierające bibliotekę plików jar będzie inne niż konto używane dla klastra, wykonaj **dodatkowe konta magazynu**.

1. W przypadku **akcji skryptu**podaj następujące informacje:

    |Właściwość |Wartość |
    |---|---|
    |Typ skryptu|- Niestandardowe|
    |Nazwa|Biblioteki |
    |Identyfikator URI skryptu bash|`https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh`|
    |Typy węzłów|Głowa, Pracownik|
    |Parametry|Wprowadź adres WASB do konta kontenera i magazynu, który zawiera słoiki. Na przykład `wasbs://libs@mystorage.blob.core.windows.net/`.|

    > [!NOTE]
    > W przypadku programu Apache Spark 2.1 `https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v00.sh`użyj tego skryptu URI skryptu bash: .

1. Kontynuuj inicjowanie obsługi administracyjnej klastra zgodnie z opisem w [programie Provision HDInsight clusters w systemie Linux](hdinsight-hadoop-provision-linux-clusters.md).

Po zakończeniu tworzenia klastra można używać słoików dodanych za pośrednictwem tego skryptu `ADD JAR` z hive bez konieczności używania instrukcji.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat pracy z gałęzią, zobacz [Używanie gałęzi Apache z hdinsight](hadoop/hdinsight-use-hive.md)
