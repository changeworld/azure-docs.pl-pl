---
title: Opracowywanie akcji skryptu, dostosowywać klastry usługi Azure HDInsight
description: Dowiedz się, jak dostosowywanie klastrów HDInsight za pomocą skryptów powłoki Bash. Akcje skryptu umożliwiają uruchamianie skryptów w trakcie lub po utworzeniu klastra, aby zmienić ustawienia konfiguracji klastra lub instalowania dodatkowego oprogramowania.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/22/2019
ms.openlocfilehash: 66132a2a6a7b5b89bca0767efe7c194ca3dec051
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60590796"
---
# <a name="script-action-development-with-hdinsight"></a>Opracowywanie akcji skryptu za pomocą HDInsight

Dowiedz się, jak dostosować klastra HDInsight przy użyciu skryptów powłoki Bash. Akcje skryptu to sposób dostosować HDInsight podczas lub po utworzeniu klastra.

## <a name="what-are-script-actions"></a>Co to są akcje skryptu

Akcje skryptu to skrypty powłoki Bash, które platforma Azure opiera się na węzłach klastra można dokonać zmian konfiguracji lub instalacja oprogramowania. Akcja skryptu jest wykonywane jako użytkownik główny i zapewnia pełne prawa dostępu do węzłów klastra.

Akcje skryptu można zastosować za pomocą następujących metod:

| Użyj tej metody, aby zastosować skrypt... | Podczas tworzenie klastra... | Na działającego klastra... |
| --- |:---:|:---:|
| Azure Portal |✓ |✓ |
| Azure PowerShell |✓ |✓ |
| Klasyczny interfejs wiersza polecenia Azure |&nbsp; |✓ |
| Zestaw SDK dla platformy .NET usługi HDInsight |✓ |✓ |
| Szablon usługi Azure Resource Manager |✓ |&nbsp; |

Aby uzyskać więcej informacji na temat korzystania z tych metod do zastosowania akcji skryptu, zobacz [HDInsight Dostosowywanie klastrów za pomocą akcji skryptu](hdinsight-hadoop-customize-cluster-linux.md).

## <a name="bestPracticeScripting"></a>Najlepsze rozwiązania dotyczące tworzenia skryptów

Podczas tworzenia niestandardowego skryptu dla klastra usługi HDInsight istnieje kilka najlepszych rozwiązań, które należy uwzględnić:

* [Docelowa wersja platformy Apache Hadoop](#bPS1)
* [Docelowa wersja systemu operacyjnego](#bps10)
* [Podaj stałe linki do zasobów skryptu](#bPS2)
* [Użyj wstępnie skompilowanym zasobów](#bPS4)
* [Upewnij się, że skrypt dostosowania klastra idempotentne](#bPS3)
* [Zapewni to wysoką dostępność architektury klastra](#bPS5)
* [Konfigurowanie niestandardowych składników, aby korzystać z magazynu obiektów Blob platformy Azure](#bPS6)
* [Zapisywanie informacji o STDOUT i STDERR](#bPS7)
* [Zapisz pliki jako plik ASCII przy użyciu LF końce wierszy](#bps8)
* [Aby odzyskać z błędami przejściowymi, użyj logikę ponawiania próby](#bps9)

> [!IMPORTANT]  
> Akcje skryptu musi zostać zakończone w ciągu 60 minut lub proces ten ulegnie awarii. Podczas inicjowania obsługi węzłów, skrypt jest uruchamiany równolegle innych procesów instalacji i konfiguracji. Konkurencję w odniesieniu do zasobów, takich jak czas lub sieci przepustowości Procesora może powodować skryptu, aby zająć dłużej niż w środowisku programistycznym.

### <a name="bPS1"></a>Docelowa wersja platformy Apache Hadoop

Różne wersje HDInsight mają różne wersje usług Hadoop i zainstalowanych składników. Jeśli skrypt oczekuje określoną wersję usługi lub składnika, skrypt należy używać tylko z wersją HDInsight, który zawiera wymagane składniki. Można znaleźć informacje na temat wersji składnika dołączone HDInsight przy użyciu [przechowywanie wersji składnika HDInsight](hdinsight-component-versioning.md) dokumentu.

### <a name="checking-the-operating-system-version"></a>Sprawdzanie wersji systemu operacyjnego

Różne wersje HDInsight opierają się na określonych wersji systemu Ubuntu. Może to być różnice między wersjami systemu operacyjnego, które musisz sprawdzać w skrypcie. Na przykład może być konieczne zainstalowanie plik binarny, który jest powiązany z wersją systemu Ubuntu.

Aby sprawdzić wersję systemu operacyjnego, należy użyć `lsb_release`. Na przykład poniższy skrypt pokazuje, jak odwołać się do pliku tar określonych w zależności od wersji systemu operacyjnego:

```bash
OS_VERSION=$(lsb_release -sr)
if [[ $OS_VERSION == 14* ]]; then
    echo "OS version is $OS_VERSION. Using hue-binaries-14-04."
    HUE_TARFILE=hue-binaries-14-04.tgz
elif [[ $OS_VERSION == 16* ]]; then
    echo "OS version is $OS_VERSION. Using hue-binaries-16-04."
    HUE_TARFILE=hue-binaries-16-04.tgz
fi
```

### <a name="bps10"></a> Docelowa wersja systemu operacyjnego

HDInsight opartych na systemie Linux jest oparta na dystrybucji Ubuntu Linux. Różne wersje HDInsight opierają się w różnych wersjach Ubuntu, który może zmienić sposób działania skryptu. Na przykład HDInsight 3.4 lub starszym zależą od wersji Ubuntu, które używają Upstart. W wersji 3.5 i większa opierają się na Ubuntu 16.04, który używa Systemd. Systemd i Upstart korzystają innego polecenia, dzięki czemu skryptu będą zapisywane w pracy z programem.

Inna ważna różnica między HDInsight 3.4 i 3.5 jest to, że `JAVA_HOME` wskazuje teraz Java 8. Poniższy kod ilustruje sposób określania, czy skrypt jest uruchomiony na systemie Ubuntu 14 lub 16:

```bash
OS_VERSION=$(lsb_release -sr)
if [[ $OS_VERSION == 14* ]]; then
    echo "OS version is $OS_VERSION. Using hue-binaries-14-04."
    HUE_TARFILE=hue-binaries-14-04.tgz
elif [[ $OS_VERSION == 16* ]]; then
    echo "OS version is $OS_VERSION. Using hue-binaries-16-04."
    HUE_TARFILE=hue-binaries-16-04.tgz
fi
...
if [[ $OS_VERSION == 16* ]]; then
    echo "Using systemd configuration"
    systemctl daemon-reload
    systemctl stop webwasb.service    
    systemctl start webwasb.service
else
    echo "Using upstart configuration"
    initctl reload-configuration
    stop webwasb
    start webwasb
fi
...
if [[ $OS_VERSION == 14* ]]; then
    export JAVA_HOME=/usr/lib/jvm/java-7-openjdk-amd64
elif [[ $OS_VERSION == 16* ]]; then
    export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64
fi
```

Możesz znaleźć pełny skrypt, który zawiera te fragmenty kodu w https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh.

Wersja systemu Ubuntu, który jest używany przez HDInsight, zobacz [wersja składnika HDInsight](hdinsight-component-versioning.md) dokumentu.

Aby zrozumieć różnice między Systemd i Upstart, zobacz [Systemd dla użytkowników Upstart](https://wiki.ubuntu.com/SystemdForUpstartUsers).

### <a name="bPS2"></a>Podaj stałe linki do zasobów skryptu

Skrypt i powiązane zasoby muszą pozostać dostępne przez cały czas życia klastra. Te zasoby są wymagane, jeśli zostaną dodane nowe węzły do klastra podczas operacji skalowania.

Najlepszym rozwiązaniem jest do pobrania i Archiwizuj wszystko na koncie magazynu platformy Azure w ramach Twojej subskrypcji.

> [!IMPORTANT]  
> Konto magazynu używane na musi być domyślne konto magazynu dla klastra lub kontener publiczną, tylko do odczytu na inne konto magazynu.

Na przykład, przykłady, obsługiwane przez firmę Microsoft są przechowywane w [ https://hdiconfigactions.blob.core.windows.net/ ](https://hdiconfigactions.blob.core.windows.net/) konta magazynu. Ta lokalizacja jest publiczny, tylko do odczytu kontenera, obsługiwane przez zespół usługi HDInsight.

### <a name="bPS4"></a>Użyj wstępnie skompilowanym zasobów

Aby skrócić czas potrzebny do uruchomienia skryptu, unikać operacji, które kompilują zasobów z kodu źródłowego. Na przykład przeprowadzić prekompilacje zasobów i przechowywać je w obiekt blob konta magazynu Azure, w tym samym centrum danych jako HDInsight.

### <a name="bPS3"></a>Upewnij się, że skrypt dostosowania klastra idempotentne

Skrypty muszą być idempotentne. Jeśli skrypt zostanie uruchomiony wielokrotnie, powinna zwrócić klastra stanu, w każdym razem.

Na przykład skrypt, który modyfikuje pliki konfiguracji nie dodać zduplikowane wpisy, gdy uruchomiono wiele razy.

### <a name="bPS5"></a>Zapewni to wysoką dostępność architektury klastra

Klastry HDInsight opartych na systemie Linux zapewniają dwa węzły główne, które są aktywne w klastrze, a akcji skryptu, uruchom na obu węzłach. Jeśli składniki, które należy zainstalować oczekuje tylko jednego węzła głównego, nie należy instalować na obu węzłów głównych składników.

> [!IMPORTANT]  
> Usługi w ramach HDInsight są przeznaczone do trybu failover między dwa węzły główne, zgodnie z potrzebami. Ta funkcja nie jest rozszerzony do niestandardowe składniki zainstalowane za pomocą akcji skryptu. Jeśli potrzebujesz wysokiej dostępności dla składników niestandardowych, należy zaimplementować własny mechanizm pracy awaryjnej.

### <a name="bPS6"></a>Konfigurowanie niestandardowych składników, aby korzystać z magazynu obiektów Blob platformy Azure

Składniki, które można zainstalować w klastrze może mieć konfigurację domyślną, która korzysta z magazynu Apache Hadoop Distributed pliku System (HDFS). HDInsight korzysta z usługi Azure Storage lub usługi Data Lake Storage jako magazynu domyślnego. Podaj zarówno systemu zgodnego systemem plików HDFS, która utrwala dane, nawet jeśli klaster jest usuwany. Może być konieczne skonfigurowanie składników, które można zainstalować, aby użyć WASB lub ADL zamiast systemu plików HDFS.

W przypadku większości operacji jest konieczne określanie systemu plików. Na przykład następujące kopiuje plik hadoop common.jar z lokalnego systemu plików do magazynu klastra:

```bash
hdfs dfs -put /usr/hdp/current/hadoop-client/hadoop-common.jar /example/jars/
```

W tym przykładzie `hdfs` polecenia w sposób niewidoczny dla użytkownika używa domyślnego magazynu klastra. W przypadku niektórych operacji może być konieczne Określ identyfikator URI. Na przykład `adl:///example/jars` dla usługi Azure Data Lake Storage Gen1 `abfs:///example/jars` for Data Lake Storage Gen2 lub `wasb:///example/jars` dla usługi Azure Storage.

### <a name="bPS7"></a>Zapisywanie informacji o STDOUT i STDERR

HDInsight rejestruje dane wyjściowe skryptu, który jest zapisywany do strumienia wyjściowego STDOUT i STDERR. Możesz wyświetlić te informacje przy użyciu interfejsu użytkownika sieci web Ambari.

> [!NOTE]  
> Apache Ambari jest dostępna tylko po pomyślnym utworzeniu klastra. Jeśli używasz podczas tworzenia klastra i kończy się niepowodzeniem tworzenia akcji skryptu, zobacz sekcję dotyczącą rozwiązywania problemów [HDInsight Dostosowywanie klastrów za pomocą akcji skryptu](hdinsight-hadoop-customize-cluster-linux.md#troubleshooting) dla innych sposobów uzyskiwania dostępu do zarejestrowanych informacji.

Większość narzędzi i pakiety instalacyjne już zapisywanie informacji o STDOUT i STDERR, jednak warto dodać dodatkowe rejestrowanie. Aby wysłać tekst stdout, należy użyć `echo`. Na przykład:

```bash
echo "Getting ready to install Foo"
```

Domyślnie `echo` wysyła ciąg znaków do strumienia wyjściowego STDOUT. Aby skierować je do strumienia wyjściowego STDERR, należy dodać `>&2` przed `echo`. Na przykład:

```bash
>&2 echo "An error occurred installing Foo"
```

To przekierowuje informacje zapisywane do strumienia wyjściowego STDOUT, stderr (2) zamiast tego. Aby uzyskać więcej informacji na temat przekierowania we/wy, zobacz [ https://www.tldp.org/LDP/abs/html/io-redirection.html ](https://www.tldp.org/LDP/abs/html/io-redirection.html).

Aby uzyskać więcej informacji o wyświetlaniu informacji rejestrowanych przez akcji skryptu, zobacz [HDInsight Dostosowywanie klastrów za pomocą akcji skryptu](hdinsight-hadoop-customize-cluster-linux.md#troubleshooting)

### <a name="bps8"></a> Zapisz pliki jako plik ASCII przy użyciu LF końce wierszy

Skrypty powłoki bash, powinny być przechowywane w formacie ASCII z liniami został przerwany przez LF. Pliki, które są przechowywane w formacie UTF-8, lub użyj CRLF jako koniec wiersza może zakończyć się niepowodzeniem z powodu następującego błędu:

```
$'\r': command not found
line 1: #!/usr/bin/env: No such file or directory
```

### <a name="bps9"></a> Aby odzyskać z błędami przejściowymi, użyj logikę ponawiania próby

Pobieranie plików, instalowanie pakietów przy użyciu polecenia apt-get lub innych działań, które przesyłają dane przez internet, akcja może zakończyć się niepowodzeniem z powodu przejściowych błędów sieci. Na przykład zasób zdalny, które komunikują się z może być w trakcie przechodzenia w tryb failover do tworzenia kopii zapasowej węzła.

Aby skrypt odporne na błędy przejściowe błędy, można zaimplementować logikę ponawiania próby. Poniższa funkcja pokazuje, jak implementować logikę ponawiania próby. Kolejną próbą operacji trzykrotnie zakończy się niepowodzeniem.

```bash
#retry
MAXATTEMPTS=3

retry() {
    local -r CMD="$@"
    local -i ATTMEPTNUM=1
    local -i RETRYINTERVAL=2

    until $CMD
    do
        if (( ATTMEPTNUM == MAXATTEMPTS ))
        then
                echo "Attempt $ATTMEPTNUM failed. no more attempts left."
                return 1
        else
                echo "Attempt $ATTMEPTNUM failed! Retrying in $RETRYINTERVAL seconds..."
                sleep $(( RETRYINTERVAL ))
                ATTMEPTNUM=$ATTMEPTNUM+1
        fi
    done
}
```

Poniższe przykłady pokazują, jak użyć tej funkcji.

```bash
retry ls -ltr foo

retry wget -O ./tmpfile.sh https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh
```

## <a name="helpermethods"></a>Metody pomocnicze dla niestandardowych skryptów

Metody pomocnicze akcji skryptu są narzędzia, których można używać podczas pisania skryptów niestandardowych. Te metody są zawarte w [ https://hdiconfigactions.blob.core.windows.net/linuxconfigactionmodulev01/HDInsightUtilities-v01.sh ](https://hdiconfigactions.blob.core.windows.net/linuxconfigactionmodulev01/HDInsightUtilities-v01.sh) skryptu. Aby pobrać i używać ich jako części skryptu, należy użyć następujących:

```bash
# Import the helper method module.
wget -O /tmp/HDInsightUtilities-v01.sh -q https://hdiconfigactions.blob.core.windows.net/linuxconfigactionmodulev01/HDInsightUtilities-v01.sh && source /tmp/HDInsightUtilities-v01.sh && rm -f /tmp/HDInsightUtilities-v01.sh
```

Następujących pomocników dostępne do użycia w skrypcie:

| Użycie pomocnika | Opis |
| --- | --- |
| `download_file SOURCEURL DESTFILEPATH [OVERWRITE]` |Pobiera plik z identyfikatora URI źródła do określonej ścieżki pliku. Domyślnie go nie zastępuj istniejącego pliku. |
| `untar_file TARFILE DESTDIR` |Wyodrębnia plik tar (przy użyciu `-xf`) do katalogu docelowego. |
| `test_is_headnode` |Jeżeli uruchomiono na węzła głównego klastra zwracanej 1; w przeciwnym razie 0. |
| `test_is_datanode` |Jeśli bieżący węzeł jest węzłem danych (proces roboczy), zwraca 1; w przeciwnym razie 0. |
| `test_is_first_datanode` |Jeśli bieżącego węzła jest pierwsze dane (proces roboczy) węzłów (o nazwie workernode0) zwracają 1; w przeciwnym razie 0. |
| `get_headnodes` |Zwraca w pełni kwalifikowaną nazwę domeny z węzłów głównych w klastrze. Nazwy są rozdzielane przecinkami. W przypadku błędu, zwracany jest pusty ciąg. |
| `get_primary_headnode` |Pobiera w pełni kwalifikowaną nazwę domeny z podstawowym węzłem głównym. W przypadku błędu, zwracany jest pusty ciąg. |
| `get_secondary_headnode` |Pobiera w pełni kwalifikowana nazwa domeny dodatkowy węzeł główny. W przypadku błędu, zwracany jest pusty ciąg. |
| `get_primary_headnode_number` |Pobiera sufiks numeryczny z podstawowym węzłem głównym. W przypadku błędu, zwracany jest pusty ciąg. |
| `get_secondary_headnode_number` |Pobiera liczbowych sufiks dodatkowy węzeł główny. W przypadku błędu, zwracany jest pusty ciąg. |

## <a name="commonusage"></a>Typowe wzorce użycia

Ta sekcja zawiera wskazówki dotyczące implementowania niektóre typowe wzorce użycia, które możesz napotkać podczas zapisywania skryptu niestandardowego.

### <a name="passing-parameters-to-a-script"></a>Przekazywanie parametrów do skryptu

W niektórych przypadkach skrypt może wymagać parametrów. Na przykład wymagać hasła administratora dla klastra przy użyciu interfejsu API REST Ambari.

Parametry przekazane do skryptu są znane jako *parametry pozycyjne*i zostały przypisane do `$1` jako pierwszy parametr `$2` dla drugiego i dlatego jednokrotnego. `$0` zawiera nazwę sam skrypt.

Wartości przekazywane do skryptu, ponieważ parametry powinny być ujęta w cudzysłów pojedynczy ('). Daje to gwarancję, że przekazana wartość jest traktowany jako literał.

### <a name="setting-environment-variables"></a>Ustawianie zmiennych środowiskowych

Ustawienie zmiennej środowiskowej odbywa się według następującej instrukcji:

    VARIABLENAME=value

Gdzie NAZWA_ZMIENNEJ to nazwa zmiennej. Aby uzyskać dostęp do zmiennej, należy użyć `$VARIABLENAME`. Na przykład do przypisania wartości dostarczone przez parametr pozycyjne jako zmienną środowiskową o nazwie HASŁA, należy użyć następującej instrukcji:

    PASSWORD=$1

Kolejne dostęp do informacji można używać `$PASSWORD`.

Zmienne środowiskowe ustawione w skrypcie istnieją tylko w zakresie skryptu. W niektórych przypadkach może być konieczne dodanie zmienne środowiskowe systemowe, które będą zachowywane po zakończeniu działania skryptu. Aby dodać zmienne środowiskowe na poziomie systemu, należy dodać zmienną `/etc/environment`. Na przykład poniższa instrukcja dodaje `HADOOP_CONF_DIR`:

```bash
echo "HADOOP_CONF_DIR=/etc/hadoop/conf" | sudo tee -a /etc/environment
```

### <a name="access-to-locations-where-the-custom-scripts-are-stored"></a>Dostęp do lokalizacji, w którym są przechowywane niestandardowe skrypty

Skrypty służące do dostosowywania klastra musi być przechowywany w jednym z następujących lokalizacji:

* __Konta usługi Azure Storage__ skojarzonym z klastrem.

* __Dodatkowe konto magazynu__ skojarzonego z klastrem.

* A __publicznie do odczytu URI__. Na przykład adres URL do danych przechowywanych w usłudze OneDrive, Dropbox lub inny plik innej usługi hostingu.

* __Konta usługi Azure Data Lake Storage__ skojarzonym z klastrem HDInsight. Aby uzyskać więcej informacji dotyczących korzystania z usługi Azure Data Lake Storage z usługą HDInsight, zobacz [Szybki Start: Konfigurowanie klastrów w usłudze HDInsight](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md).

    > [!NOTE]  
    > Nazwa główna usługi HDInsight używane do dostępu do usługi Data Lake Storage przez musi mieć dostęp do odczytu do skryptu.

Zasoby używane przez skrypt również muszą być dostępne publicznie.

Przechowywanie plików w usłudze Azure Storage konta lub usługi Azure Data Lake Storage zapewnia szybki dostęp, oba w ramach sieci platformy Azure.

> [!NOTE]  
> Format identyfikatora URI używany w celu skrypt różni się w zależności od używanej usługi. W przypadku konta magazynu skojarzonego z klastrem HDInsight, użyj `wasb://` lub `wasbs://`. Identyfikatory URI publicznie do odczytu, użyj `http://` lub `https://`. W przypadku usługi Data Lake Storage, użyj `adl://`.

## <a name="deployScript"></a>Lista kontrolna wdrażania akcji skryptu

Poniżej przedstawiono podejmij kroki podczas przygotowywania wdrożenia skryptu:

* Umieść pliki, które zawierają niestandardowych skryptów w miejscu, który jest dostępny dla węzłów klastra podczas wdrażania. Na przykład domyślny magazyn dla klastra. Pliki mogą być również przechowywane w publicznie do odczytu usługi hostingu.
* Sprawdź, czy skrypt jest idempotentny. Dzięki temu skrypt do wykonania wiele razy w innym węźle.
* Użyj tmp katalogu plików tymczasowych, aby zachować pobrane pliki, które są używane przez skrypty i następnie oczyścić je po wykonaniu skryptów.
* Jeśli zostaną zmienione ustawienia na poziomie systemu operacyjnego lub plików konfiguracyjnych usługi Hadoop, można ponownie uruchomić usługi HDInsight.

## <a name="runScriptAction"></a>Jak uruchomić akcji skryptu

Akcje skryptu umożliwia dostosowywanie klastrów HDInsight za pomocą następujących metod:

* Azure Portal
* Azure PowerShell
* Szablony usługi Azure Resource Manager
* Zestaw .NET SDK HDInsight.

Aby uzyskać więcej informacji na temat korzystania z poszczególnych metod, zobacz [sposób użycia akcji skryptu](hdinsight-hadoop-customize-cluster-linux.md).

## <a name="sampleScripts"></a>Przykłady skryptów niestandardowych

Firma Microsoft oferuje przykładowe skrypty do instalowania składników w klastrze usługi HDInsight. Skorzystaj z następujących linków, aby uzyskać więcej akcji skryptu w przykładzie.

* [Instalowanie i korzystanie z rozwiązania Hue w klastrach HDInsight](hdinsight-hadoop-hue-linux.md)
* [Instalowanie i używanie Apache Giraph w klastrach HDInsight](hdinsight-hadoop-giraph-install-linux.md)

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Błędy, które mogą wystąpić, gdy za pomocą skryptów, które zostały opracowane są następujące:

**Błąd**: `$'\r': command not found`. Czasami następuje `syntax error: unexpected end of file`.

*Przyczyna*: Gdy wierszy w skrypcie kończyć się znakiem CRLF, jest przyczyną tego błędu. Systemy UNIX oczekiwać, że tylko LF jako koniec wiersza.

Ten problem najczęściej występuje, gdy skrypt został utworzony w środowisku Windows CRLF jest linii Kończenie dla wielu edytorów tekstu na Windows.

*Rozwiązanie*: Jest to opcja w edytorze tekstu, na koniec wiersza wybierz format systemu Unix lub LF. Aby zmienić CRLF LF można także użyć następujących poleceń w systemie Unix:

> [!NOTE]  
> Następujące polecenia są mniej więcej odpowiednikami, w tym, że ich należy zmienić CRLF końce wierszy na LF. Wybierz jedną, w oparciu o narzędzi dostępnych w systemie.

| Polecenie | Uwagi |
| --- | --- |
| `unix2dos -b INFILE` |Oryginalny plik Kopia zapasowa jest tworzona przy użyciu. Rozszerzenie BAK |
| `tr -d '\r' < INFILE > OUTFILE` |PLIKWYJŚCIOWY zawiera wersję przy użyciu tylko LF końce |
| `perl -pi -e 's/\r\n/\n/g' INFILE` | Modyfikuje plik bezpośrednio |
| ```sed 's/$'"/`echo \\\r`/" INFILE > OUTFILE``` |PLIKWYJŚCIOWY zawiera wersję przy użyciu tylko końce LF. |

**Błąd**: `line 1: #!/usr/bin/env: No such file or directory`.

*Przyczyna*: Ten błąd występuje, gdy skrypt został zapisany w formacie UTF-8 za pomocą znacznika kolejności bajtów (BOM).

*Rozwiązanie*: Zapisz plik w formacie ASCII lub jako UTF-8 bez BOM. Aby utworzyć plik bez BOM można także użyć następującego polecenia w systemie Linux lub Unix:

    awk 'NR==1{sub(/^\xef\xbb\xbf/,"")}{print}' INFILE > OUTFILE

Zastąp `INFILE` za pomocą pliku zawierającego znak BOM. `OUTFILE` powinna być nową nazwę pliku, który zawiera skrypt bez BOM.

## <a name="seeAlso"></a>Następne kroki

* Dowiedz się, jak [HDInsight Dostosowywanie klastrów za pomocą akcji skryptu](hdinsight-hadoop-customize-cluster-linux.md)
* Użyj [dokumentacji zestawu .NET SDK HDInsight](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight) Aby dowiedzieć się więcej na temat tworzenia aplikacji platformy .NET, które zarządzają HDInsight
* Użyj [interfejsu API REST HDInsight](https://msdn.microsoft.com/library/azure/mt622197.aspx) Aby dowiedzieć się, jak wykonywać operacje zarządzania w klastrach HDInsight przy użyciu architektury REST.
