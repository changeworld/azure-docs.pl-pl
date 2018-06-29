---
title: Skrypt programowanie akcji z usługą HDInsight opartą na systemie Linux - Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skrypty powłoki systemowej umożliwia dostosowywanie klastrów usługi HDInsight opartych na systemie Linux. Funkcja Akcja skryptu HDInsight pozwala na uruchamianie skryptów podczas lub po utworzeniu klastra. Skrypty można zmienić ustawienia konfiguracji klastra lub zainstalować dodatkowe oprogramowanie.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: cgronlun
editor: cgronlun
ms.assetid: cf4c89cd-f7da-4a10-857f-838004965d3e
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/10/2018
ms.author: larryfr
ms.openlocfilehash: 5f6609018f2582ebfc6e2ecbe48bde6a6f2d0f5d
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/29/2018
ms.locfileid: "37098264"
---
# <a name="script-action-development-with-hdinsight"></a>Tworzenie akcji skryptu za pomocą usługi HDInsight

Dowiedz się, jak dostosować z klastrem usługi HDInsight przy użyciu skrypty Bash. Akcje skryptu to sposób, aby dostosować HDInsight podczas lub po utworzeniu klastra.

> [!IMPORTANT]
> Procedura przedstawiona w tym dokumencie wymaga klastra usługi HDInsight używającego systemu Linux. Linux jest jedynym systemem operacyjnym używanym w połączeniu z usługą HDInsight w wersji 3.4 lub nowszą. Aby uzyskać więcej informacji, zobacz sekcję [HDInsight retirement on Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement) (Wycofanie usługi HDInsight w systemie Windows).

## <a name="what-are-script-actions"></a>Co to są akcji skryptu

Akcje skryptu to skrypty Bash, które Azure działa na węzłach klastra dokonać zmian konfiguracji lub instalacja oprogramowania. Akcja skryptu jest wykonywany jako główny i zapewnia pełne prawa dostępu do węzłów klastra.

Akcje skryptu można zastosować za pomocą następujących metod:

| Ta metoda umożliwia zastosowanie skryptu... | Tworzenie klastra podczas... | W klastrze uruchomione... |
| --- |:---:|:---:|
| Azure Portal |✓ |✓ |
| Azure PowerShell |✓ |✓ |
| Interfejs wiersza polecenia platformy Azure CLI w wersji 1.0 |&nbsp; |✓ |
| Zestaw SDK dla platformy .NET usługi HDInsight |✓ |✓ |
| Szablon usługi Azure Resource Manager |✓ |&nbsp; |

Aby uzyskać więcej informacji o używaniu tych metod do zastosowania akcji skryptu, zobacz [HDInsight dostosować klastry za pomocą akcji skryptu](hdinsight-hadoop-customize-cluster-linux.md).

## <a name="bestPracticeScripting"></a>Najlepsze rozwiązania dotyczące tworzenia skryptów

Podczas opracowywania niestandardowego skryptu dla klastra usługi HDInsight, istnieje kilka najlepszych rozwiązań, które należy wziąć pod uwagę:

* [Docelowa wersja platformy Hadoop](#bPS1)
* [Docelowa wersja systemu operacyjnego](#bps10)
* [Podaj stabilna linki do zasobów skryptu](#bPS2)
* [Użyj wstępnie skompilowanym zasobów](#bPS4)
* [Upewnij się, że skrypt dostosowywania klastra jest idempotentności](#bPS3)
* [Zapewni to wysoką dostępność architektury klastra](#bPS5)
* [Konfigurowanie niestandardowych składników można używać magazynu obiektów Blob platformy Azure](#bPS6)
* [Zapisywanie informacji o STDOUT i STDERR.](#bPS7)
* [Zapisz pliki jako ASCII z końców LF](#bps8)
* [Logika ponawiania umożliwia odzyskanie w przypadku błędów przejściowych](#bps9)

> [!IMPORTANT]
> Akcje skryptu należy zakończyć w ciągu 60 minut lub proces nie powiedzie się. Podczas inicjowania obsługi węzła, skrypt zostanie uruchomiony równocześnie z innymi procesami instalacji i konfiguracji. Konkurowanie o zasoby, takie jak czas lub sieci przepustowości Procesora może powodować skrypt potrwać dłużej niż w środowisku projektowania.

### <a name="bPS1"></a>Docelowa wersja platformy Hadoop

Różne wersje HDInsight korzystają z różnych wersji usług Hadoop i zainstalowanych składników. Jeśli skrypt oczekuje określoną wersję usługi lub składnika, skrypt należy używać tylko z wersją HDInsight, która zawiera wymagane składniki. Można znaleźć informacje o wersji składników uwzględnionych w usłudze HDInsight przy użyciu [przechowywanie wersji składnika usługi HDInsight](hdinsight-component-versioning.md) dokumentu.

### <a name="bps10"></a> Docelowa wersja systemu operacyjnego

HDInsight opartych na systemie Linux jest oparta na dystrybucji Ubuntu Linux. Różnych wersji usługi hdinsight korzystają z różnych wersji systemu Ubuntu, która może zmienić sposób działania skryptu. Na przykład HDInsight 3.4 i starsze wersje są oparte na wersji Ubuntu, które używają Upstart. W wersji 3.5 lub nowszej są oparte na 16.04 Ubuntu, który używa Systemd. Systemd i Upstart zależne inne polecenia, aby skrypt powinien być zapisywany do pracy z programem.

Inna ważna różnica między HDInsight 3.4 i 3.5 jest to, że `JAVA_HOME` teraz wskazuje Java 8.

Wersja systemu operacyjnego można sprawdzić za pomocą `lsb_release`. Poniższy kod przedstawia sposób określania, czy skrypt jest uruchomiony na Ubuntu 14 lub 16:

```bash
OS_VERSION=$(lsb_release -sr)
if [[ $OS_VERSION == 14* ]]; then
    echo "OS verion is $OS_VERSION. Using hue-binaries-14-04."
    HUE_TARFILE=hue-binaries-14-04.tgz
elif [[ $OS_VERSION == 16* ]]; then
    echo "OS verion is $OS_VERSION. Using hue-binaries-16-04."
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

Można znaleźć pełnej skrypt, który zawiera te wstawki na https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh.

Wersja Ubuntu, który jest używany przez usługi HDInsight dla [składnika usługi HDInsight w wersji](hdinsight-component-versioning.md) dokumentu.

Aby poznać różnice między Systemd i Upstart, zobacz [Systemd dla użytkowników Upstart](https://wiki.ubuntu.com/SystemdForUpstartUsers).

### <a name="bPS2"></a>Podaj stabilna linki do zasobów skryptu

Skrypt i skojarzonych zasobów musi być dostępna przez cały cykl życia klastra. Te zasoby są wymagane, jeśli zostaną dodane nowe węzły do klastra podczas operacji skalowania.

Najlepszym rozwiązaniem jest, aby pobrać i zarchiwizowanie wszystkich na koncie magazynu Azure w ramach subskrypcji.

> [!IMPORTANT]
> Konto magazynu używane musi być domyślne konto magazynu dla klastra lub kontener publiczne, tylko do odczytu na inne konto magazynu.

Na przykład przykłady obsługiwane przez firmę Microsoft są przechowywane w [ https://hdiconfigactions.blob.core.windows.net/ ](https://hdiconfigactions.blob.core.windows.net/) konta magazynu. Ta lokalizacja jest publiczny, tylko do odczytu kontenera obsługiwanego przez zespół usługi HDInsight.

### <a name="bPS4"></a>Użyj wstępnie skompilowanym zasobów

Aby skrócić czas wymagany do uruchomienia skryptu, należy unikać operacji kompilowane zasobów z kodu źródłowego. Na przykład wstępnie skompilować zasobów i przechowywać je w obiekcie blob konta magazynu Azure w tym samym centrum danych jako HDInsight.

### <a name="bPS3"></a>Upewnij się, że skrypt dostosowywania klastra jest idempotentności

Skrypty musi być idempotentności. Jeśli skrypt jest uruchamiany wiele razy, jego powinien zwrócić klastra do tego samego stanu zawsze.

Na przykład skrypt, który modyfikuje pliki konfiguracji nie dodać zduplikowanych wpisów, gdy uruchomiono wiele razy.

### <a name="bPS5"></a>Zapewni to wysoką dostępność architektury klastra

Klastry HDInsight opartych na systemie Linux zapewniają dwa węzły head, które są aktywne w klastrze, a akcji skryptu, uruchom na obu węzłów. Jeśli składniki, które będą instalowane oczekuje tylko jednego węzła głównego, nie należy instalować na obu węzłów głównych składników.

> [!IMPORTANT]
> Usługi w ramach usługi HDInsight zostały zaprojektowane do pracy awaryjnej między dwoma węzłami head, zgodnie z potrzebami. Ta funkcja nie jest rozszerzony do niestandardowe składniki zainstalowane za pomocą akcji skryptu. Jeśli potrzebujesz wysokiej dostępności dla niestandardowych składników, musisz zaimplementować własny mechanizm pracy awaryjnej.

### <a name="bPS6"></a>Konfigurowanie niestandardowych składników można używać magazynu obiektów Blob platformy Azure

Składniki, które można zainstalować w klastrze może być konfigurację domyślną, która korzysta z magazynu systemu Distributed plików Hadoop (HDFS). HDInsight używa magazynu Azure lub usługi Data Lake Store jako domyślnego magazynu. Podaj zarówno systemu plików zgodny system plików HDFS, który będzie się powtarzał danych, nawet jeśli klaster zostanie usunięty. Może być konieczne skonfigurowanie składników, które można zainstalować, aby użyć WASB lub ADL zamiast systemu plików HDFS.

Dla większości operacji nie trzeba określić system plików. Na przykład następujące kopiuje plik giraph-examples.jar z lokalnego systemu plików do magazynu klastra:

```bash
hdfs dfs -put /usr/hdp/current/giraph/giraph-examples.jar /example/jars/
```

W tym przykładzie `hdfs` polecenie niewidocznie używa domyślnego magazynu klastra. Dla niektórych operacji może być konieczne Określ identyfikator URI. Na przykład `adl:///example/jars` dla usługi Data Lake Store lub `wasb:///example/jars` usługi Azure Storage.

### <a name="bPS7"></a>Zapisywanie informacji o STDOUT i STDERR.

HDInsight rejestruje dane wyjściowe skryptu, które są zapisywane do strumienia wyjściowego STDOUT i STDERR. Można wyświetlić te informacje przy użyciu interfejsu użytkownika sieci web Ambari.

> [!NOTE]
> Ambari jest dostępna tylko jeśli klaster został utworzony pomyślnie. Jeśli używasz akcji skryptu podczas tworzenia klastra i utworzenie kończy się niepowodzeniem, zobacz sekcję dotyczącą rozwiązywania problemów [HDInsight dostosować klastry za pomocą akcji skryptu](hdinsight-hadoop-customize-cluster-linux.md#troubleshooting) dotyczące innych metod uzyskiwania dostępu do zarejestrowane informacje.

Większość narzędzi i pakiety instalacyjne już zapisuje informacje o do STDOUT i STDERR, jednak można dodać dodatkowe rejestrowanie. Aby wysłać tekst do STDOUT, należy użyć `echo`. Na przykład:

```bash
echo "Getting ready to install Foo"
```

Domyślnie `echo` wysyła ciąg do STDOUT. Aby skierować ją stderr, Dodaj `>&2` przed `echo`. Na przykład:

```bash
>&2 echo "An error occurred installing Foo"
```

To przekierowuje informacje zapisane zamiast tego do STDOUT stderr (2). Aby uzyskać więcej informacji dotyczących przekierowania we/wy, zobacz [ http://www.tldp.org/LDP/abs/html/io-redirection.html ](http://www.tldp.org/LDP/abs/html/io-redirection.html).

Aby uzyskać więcej informacji o wyświetlaniu informacji zarejestrowanych przez akcje skryptu, zobacz [HDInsight dostosować klastry za pomocą akcji skryptu](hdinsight-hadoop-customize-cluster-linux.md#troubleshooting)

### <a name="bps8"></a> Zapisz pliki jako ASCII z końców LF

Skrypty powłoki systemowej powinny być przechowywane w formacie ASCII, liniami został przerwany przez wysuwu wiersza. Pliki, które są przechowywane w formacie UTF-8, lub użyj CRLF jako zakończenie wiersza może zakończyć się niepowodzeniem z powodu następującego błędu:

```
$'\r': command not found
line 1: #!/usr/bin/env: No such file or directory
```

### <a name="bps9"></a> Logika ponawiania umożliwia odzyskanie w przypadku błędów przejściowych

Pobieranie plików, instalowanie pakietów przy użyciu stanie get lub innych działań, których dane są przesyłane za pośrednictwem Internetu, akcja może zakończyć się niepowodzeniem z powodu przejściowych błędów sieci. Na przykład zasób zdalny, które komunikują się z może być w trakcie awarii węzła kopii zapasowej.

Aby skrypt odporność błędów przejściowych, można implementację logiki ponawiania próby. Następująca funkcja demonstracja implementację logiki ponawiania próby. Ponowną operacji trzy razy przed niepowodzeniem.

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

Metody pomocnicze akcji skryptu są narzędzia, które można użyć podczas pisania skryptów niestandardowych. Te metody są zawarte w[ https://hdiconfigactions.blob.core.windows.net/linuxconfigactionmodulev01/HDInsightUtilities-v01.sh ](https://hdiconfigactions.blob.core.windows.net/linuxconfigactionmodulev01/HDInsightUtilities-v01.sh) skryptu. Pobranie i użycie ich jako część skryptu należy wykonać następujące kroki:

```bash
# Import the helper method module.
wget -O /tmp/HDInsightUtilities-v01.sh -q https://hdiconfigactions.blob.core.windows.net/linuxconfigactionmodulev01/HDInsightUtilities-v01.sh && source /tmp/HDInsightUtilities-v01.sh && rm -f /tmp/HDInsightUtilities-v01.sh
```

Następujące pomocników dostępne do użycia w skrypcie:

| Użycie pomocnika | Opis |
| --- | --- |
| `download_file SOURCEURL DESTFILEPATH [OVERWRITE]` |Pobiera ze źródła identyfikatora URI pliku określona ścieżka pliku. Domyślnie go nie zastępuj istniejącego pliku. |
| `untar_file TARFILE DESTDIR` |Wyodrębnia plik tar (przy użyciu `-xf`) do katalogu docelowego. |
| `test_is_headnode` |Jeśli został uruchomiony na węzła głównego klastra zwracany 1; w przeciwnym razie wartość 0. |
| `test_is_datanode` |Jeśli bieżący węzeł jest węzłem danych (proces roboczy), zwraca 1; w przeciwnym razie wartość 0. |
| `test_is_first_datanode` |W przypadku bieżącego węzła jest pierwsze dane (proces roboczy) węzła (o nazwie workernode0) zwraca 1; w przeciwnym razie wartość 0. |
| `get_headnodes` |Zwraca w pełni kwalifikowaną nazwę domeny z headnodes w klastrze. Nazwy są rozdzielone przecinkami. Ciąg pusty jest zwracana w przypadku błędu. |
| `get_primary_headnode` |Pobiera nazwę FQDN headnode podstawowego. Ciąg pusty jest zwracana w przypadku błędu. |
| `get_secondary_headnode` |Pobiera nazwę FQDN headnode dodatkowej. Ciąg pusty jest zwracana w przypadku błędu. |
| `get_primary_headnode_number` |Pobiera liczbowego sufiksu podstawowej headnode. Ciąg pusty jest zwracana w przypadku błędu. |
| `get_secondary_headnode_number` |Pobiera sufiks numeryczny z headnode dodatkowej. Ciąg pusty jest zwracana w przypadku błędu. |

## <a name="commonusage"></a>Wspólne wzorce użycia

Ta sekcja zawiera wskazówki dotyczące implementowania niektóre typowe wzorce użycia, które możesz napotkać podczas pisania skryptu niestandardowego.

### <a name="passing-parameters-to-a-script"></a>Przekazywanie parametrów do skryptu

W niektórych przypadkach skrypt może wymagać parametrów. Na przykład mogą być potrzebne hasło administratora klastra, korzystając z interfejsu API REST Ambari.

Parametry przekazywane do skryptu są określane jako *parametrów pozycyjnych*i są przypisane do `$1` jako pierwszy parametr `$2` sekundy i dlatego w przypadku. `$0` zawiera nazwę sam skrypt.

Wartości przekazywane do skryptu jako parametry powinna zostać ujęta w apostrofy ('). Daje to gwarancję, że przekazana wartość jest traktowany jako literału.

### <a name="setting-environment-variables"></a>Ustawianie zmiennych środowiskowych

Ustawienie zmiennej środowiskowej jest wykonywane przez następująca instrukcja:

    VARIABLENAME=value

Gdzie NAZWA_ZMIENNEJ to nazwa zmiennej. Aby uzyskać dostęp do zmiennej, należy użyć `$VARIABLENAME`. Na przykład można przypisać dla wartości dostarczonej przez parametrów pozycyjnych jako zmienną środowiskową o nazwie HASŁA, należy użyć następującej instrukcji:

    PASSWORD=$1

Następnie można użyć kolejnych dostępu do informacji `$PASSWORD`.

Zmienne środowiskowe w skrypt istnieje tylko w zakresie skryptu. W niektórych przypadkach może być konieczne dodanie zmiennych środowiskowych na poziomie całego systemu, zachowywanych po zakończeniu działania skryptu. Aby dodać zmiennych środowiskowych na poziomie całego systemu, należy dodać zmienną `/etc/environment`. Na przykład następująca instrukcja dodaje `HADOOP_CONF_DIR`:

```bash
echo "HADOOP_CONF_DIR=/etc/hadoop/conf" | sudo tee -a /etc/environment
```

### <a name="access-to-locations-where-the-custom-scripts-are-stored"></a>Dostęp do lokalizacji, w którym są przechowywane niestandardowe skrypty

Skrypty używane w celu dostosowania klastra musi być przechowywany w jednym z następujących lokalizacji:

* __Konta magazynu Azure__ skojarzonego z klastrem.

* __Konta magazynu dodatkowe__ skojarzony z klastrem.

* A __publicznie można odczytać identyfikatora URI__. Na przykład adres URL, do danych przechowywanych na OneDrive, Dropbox lub innych plików obsługującego usługę.

* __Konta usługi Azure Data Lake Store__ skojarzonego z klastrem usługi HDInsight. Aby uzyskać więcej informacji na temat używania usługi Azure Data Lake Store z usługą HDInsight, zobacz [Szybki Start: Ustawianie klastrów w usłudze HDInsight](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md).

    > [!NOTE]
    > Nazwy głównej usługi, które HDInsight używa do uzyskiwania dostępu usługi Data Lake Store musi mieć dostęp do odczytu do skryptu.

Zasoby używane przez skrypt również musi być publicznie dostępny.

Przechowywanie plików w koncie magazynu Azure lub usługi Azure Data Lake Store zapewnia szybki dostęp, jako zarówno w ramach sieci platformy Azure.

> [!NOTE]
> Format identyfikatora URI używany w celu skrypt różni się w zależności od używanej usługi. Dla konta magazynu skojarzone z klastrem usługi HDInsight, użyj `wasb://` lub `wasbs://`. Identyfikatory URI publicznie do odczytu, użyj `http://` lub `https://`. Data Lake Store, użyj `adl://`.

### <a name="checking-the-operating-system-version"></a>Sprawdzanie wersji systemu operacyjnego

Różne wersje HDInsight korzystają z określonych wersji systemu Ubuntu. Może to być różnice między systemami Operacyjnymi, które muszą sprawdzaj w skrypcie. Na przykład konieczne może zainstalować pliku binarnego, który jest powiązany z wersją Ubuntu.

Aby sprawdzić wersję systemu operacyjnego, należy użyć `lsb_release`. Na przykład poniższy skrypt pokazuje, jak odwołać plik tar określonych w zależności od wersji systemu operacyjnego:

```bash
OS_VERSION=$(lsb_release -sr)
if [[ $OS_VERSION == 14* ]]; then
    echo "OS verion is $OS_VERSION. Using hue-binaries-14-04."
    HUE_TARFILE=hue-binaries-14-04.tgz
elif [[ $OS_VERSION == 16* ]]; then
    echo "OS verion is $OS_VERSION. Using hue-binaries-16-04."
    HUE_TARFILE=hue-binaries-16-04.tgz
fi
```

## <a name="deployScript"></a>Lista kontrolna wdrażania akcji skryptu

Oto wykonaj kroki podczas przygotowywania wdrożenia skryptu:

* Umieścić pliki, które zawierają niestandardowe skrypty w miejscu, który jest dostępny dla węzłów klastra podczas wdrażania. Na przykład domyślny magazyn dla klastra. Pliki mogą być również przechowywane w publicznie do odczytu usług hostingu.
* Sprawdź, czy skrypt idempotentności. Dzięki temu skryptu do wykonania wiele razy w tym samym węźle.
* Użyj /tmp katalogu plików tymczasowych, aby zachować pobrane pliki używane przez skrypty i następnie wyczyść je po wykonaniu skryptów.
* Ustawienia na poziomie systemu operacyjnego lub plików konfiguracyjnych usługi Hadoop są zmieniane, można ponownie uruchomić usługi HDInsight.

## <a name="runScriptAction"></a>Jak uruchomić akcję skryptu

Akcje skryptu umożliwia dostosowywanie klastrów usługi HDInsight przy użyciu następujących metod:

* Azure Portal
* Azure PowerShell
* Szablony usługi Azure Resource Manager
* Zestaw .NET SDK usługi HDInsight.

Aby uzyskać więcej informacji na temat używania każdej z metod, zobacz [sposób użycia akcji skryptu](hdinsight-hadoop-customize-cluster-linux.md).

## <a name="sampleScripts"></a>Przykłady niestandardowych skryptów

Firma Microsoft udostępnia przykładowe skrypty, aby zainstalować składniki w klastrze usługi HDInsight. Zobacz następujące linki dla więcej przykład akcji skryptu.

* [Zainstalować i używać Hue w klastrach HDInsight](hdinsight-hadoop-hue-linux.md)
* [Zainstalować i używać Solr w klastrach HDInsight](hdinsight-hadoop-solr-install-linux.md)
* [Zainstalować i używać Giraph w klastrach HDInsight](hdinsight-hadoop-giraph-install-linux.md)
* [Zainstaluj lub Uaktualnij Mono w klastrach HDInsight](hdinsight-hadoop-install-mono.md)

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Błędy, które mogą wystąpić podczas za pomocą skryptów, które zostały opracowane są następujące:

**Błąd**: `$'\r': command not found`. Czasami następuje `syntax error: unexpected end of file`.

*Przyczyna*: ten błąd występuje, gdy wierszy w skrypcie kończyć CRLF. Systemy UNIX oczekiwać tylko LF jako zakończenia linii.

Ten problem najczęściej występuje, gdy skrypt został utworzony w środowisku Windows CRLF jest linii Kończenie dla wielu edytory tekstów w systemie Windows.

*Rozdzielczość*: Jeśli jest to opcja w edytorze tekstu, wybierz Unix format lub LF na koniec wiersza. Aby zmienić CRLF LF może także użyć następujących poleceń w systemie Unix:

> [!NOTE]
> Poniższe polecenia są w przybliżeniu w tym powinien Zmień zakończenia wierszy CRLF wysuwu wiersza. Wybierz jedno z narzędzi dostępnych w systemie w oparciu.

| Polecenie | Uwagi |
| --- | --- |
| `unix2dos -b INFILE` |Kopii zapasowej oryginalnego pliku z. Rozszerzeniem BAK |
| `tr -d '\r' < INFILE > OUTFILE` |PLIKWYJŚCIOWY zawiera wersję z tylko LF zakończenia |
| `perl -pi -e 's/\r\n/\n/g' INFILE` | Modyfikuje plik bezpośrednio |
| ```sed 's/$'"/`echo \\\r`/" INFILE > OUTFILE``` |PLIKWYJŚCIOWY zawiera wersję z tylko LF zakończenia. |

**Błąd**: `line 1: #!/usr/bin/env: No such file or directory`.

*Przyczyna*: ten błąd występuje, gdy skrypt został zapisany jako UTF-8 z znacznik kolejności bajtów (BOM).

*Rozdzielczość*: Zapisz plik w formacie ASCII lub jako UTF-8 bez BOM. Aby utworzyć plik bez BOM może także użyć następującego polecenia w systemie Linux lub Unix:

    awk 'NR==1{sub(/^\xef\xbb\xbf/,"")}{print}' INFILE > OUTFILE

Zastąp `INFILE` z pliku zawierającego BOM. `OUTFILE` powinien być nową nazwę pliku, który zawiera skrypt bez BOM.

## <a name="seeAlso"></a>Następne kroki

* Dowiedz się, jak [HDInsight dostosować klastry za pomocą akcji skryptu](hdinsight-hadoop-customize-cluster-linux.md)
* Użyj [odwołania do zestawu SDK .NET usługi HDInsight](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight) Aby dowiedzieć się więcej o tworzeniu aplikacji .NET, które zarządzają HDInsight
* Użyj [interfejsu API REST usługi HDInsight](https://msdn.microsoft.com/library/azure/mt622197.aspx) więcej informacji na temat używania REST do wykonywania akcji związanych z zarządzaniem w klastrach usługi HDInsight.
