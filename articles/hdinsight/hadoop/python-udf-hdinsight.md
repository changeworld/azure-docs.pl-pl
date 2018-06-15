---
title: Python funkcji zdefiniowanej przez użytkownika z Apache Hive i wieprzowa — usługa Azure HDInsight | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać języka Python użytkownika określone funkcje (UDF) z Hive i Pig w usłudze HDInsight Hadoop stosu technologii na platformie Azure.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: c44d6606-28cd-429b-b535-235e8f34a664
ms.service: hdinsight
ms.devlang: python
ms.topic: conceptual
ms.date: 02/27/2018
ms.author: larryfr
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.openlocfilehash: c508ffcdd5cccaad5c7b0717d33e2ea1449ab522
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
ms.locfileid: "31405497"
---
# <a name="use-python-user-defined-functions-udf-with-hive-and-pig-in-hdinsight"></a>Funkcje (UDF) za pomocą technologii Hive i Pig zdefiniowane przez użytkownika Python użycia w usłudze HDInsight

Dowiedz się, jak używać języka Python funkcje zdefiniowane przez użytkownika (UDF) z Apache Hive i Pig na platformie Hadoop w usłudze Azure HDInsight.

## <a name="python"></a>Python w usłudze HDInsight

Python2.7 jest instalowany domyślnie na HDInsight 3.0 i nowszych. Apache Hive można użyć z tą wersją języka Python dla przetwarzania strumienia. Przetwarzanie strumienia używa STDOUT i STDIN do przekazywania danych między Hive i funkcję zdefiniowaną przez użytkownika.

HDInsight obejmuje również Jython, który jest implementacją języka Python, napisany w języku Java. Jython działa bezpośrednio na maszynie wirtualnej Java i nie używa przesyłania strumieniowego. Jython jest zalecane interpreter języka Python, korzystając z języka Python z języka Pig.

> [!WARNING]
> Kroki opisane w tym dokumencie mieć następujące wartości domyślne: 
>
> * Można utworzyć skrypty języka Python na środowiska deweloperskiego lokalnego.
> * Możesz przekazać do usługi HDInsight przy użyciu skryptów `scp` polecenie sesję Bash lokalnego lub udostępnionego skryptu programu PowerShell.
>
> Jeśli chcesz użyć [powłoki chmury Azure (bash)](https://docs.microsoft.com/azure/cloud-shell/overview) Podgląd, aby pracować z usługą HDInsight, a następnie należy:
>
> * Tworzenie skryptów w środowisku chmury powłoki.
> * Użyj `scp` przekazywania plików z poziomu powłoki chmury do usługi HDInsight.
> * Użyj `ssh` z poziomu powłoki chmury, aby podłączyć się do usługi HDInsight i uruchamiania przykładów.

## <a name="hivepython"></a>Hive funkcji zdefiniowanej przez użytkownika

Python, mogą być używane jako UDF z Hive za pośrednictwem HiveQL `TRANSFORM` instrukcji. Na przykład następujące HiveQL wywołuje `hiveudf.py` plików przechowywanych w domyślne konto magazynu Azure dla klastra.

**HDInsight opartych na systemie Linux**

```hiveql
add file wasb:///hiveudf.py;

SELECT TRANSFORM (clientid, devicemake, devicemodel)
    USING 'python hiveudf.py' AS
    (clientid string, phoneLable string, phoneHash string)
FROM hivesampletable
ORDER BY clientid LIMIT 50;
```

**HDInsight opartych na systemie Windows**

```hiveql
add file wasb:///hiveudf.py;

SELECT TRANSFORM (clientid, devicemake, devicemodel)
    USING 'D:\Python27\python.exe hiveudf.py' AS
    (clientid string, phoneLable string, phoneHash string)
FROM hivesampletable
ORDER BY clientid LIMIT 50;
```

> [!NOTE]
> W klastrach HDInsight opartych na systemie Windows `USING` klauzuli należy określić pełną ścieżkę do python.exe.

Oto, co oznacza w tym przykładzie:

1. `add file` Dodaje oświadczenie na początku pliku `hiveudf.py` plik do rozproszonej pamięci podręcznej, więc nie jest dostępny przez wszystkie węzły w klastrze.
2. `SELECT TRANSFORM ... USING` Instrukcja wybiera dane z `hivesampletable`. Przekazuje także wartości clientid, devicemake i devicemodel `hiveudf.py` skryptu.
3. `AS` Klauzuli opisano pola zwrócony z `hiveudf.py`.

<a name="streamingpy"></a>

### <a name="create-the-hiveudfpy-file"></a>Utwórz plik hiveudf.py


Na środowiska deweloperskiego, Utwórz plik tekstowy o nazwie `hiveudf.py`. Zawartość pliku, należy użyć poniższego kodu:

```python
#!/usr/bin/env python
import sys
import string
import hashlib

while True:
    line = sys.stdin.readline()
    if not line:
        break

    line = string.strip(line, "\n ")
    clientid, devicemake, devicemodel = string.split(line, "\t")
    phone_label = devicemake + ' ' + devicemodel
    print "\t".join([clientid, phone_label, hashlib.md5(phone_label).hexdigest()])
```

Ten skrypt wykonuje następujące czynności:

1. Odczytaj wiersz danych stdin.
2. Końcowy znak nowego wiersza zostanie usunięty, za pomocą `string.strip(line, "\n ")`.
3. W trakcie przetwarzania strumieni, pojedynczy wiersz zawiera wszystkie wartości z znak tabulacji od każdej wartości. Dlatego `string.split(line, "\t")` można podzielić na każdej karcie zwracanie tylko pola danych wejściowych.
4. Po zakończeniu przetwarzania danych wyjściowych musi być przystosowana do STDOUT jako pojedynczy wiersz z kartą między każdego pola. Na przykład `print "\t".join([clientid, phone_label, hashlib.md5(phone_label).hexdigest()])`.
5. `while` Pętli jest powtarzany do momentu nie `line` jest do odczytu.

Dane wyjściowe skryptu jest złączeniem wartości wejściowe dla `devicemake` i `devicemodel`oraz skrót połączonych wartości.

Zobacz [uruchamiania przykładów](#running) instrukcje do uruchomienia tego przykładu w klastrze usługi HDInsight.

## <a name="pigpython"></a>Pig UDF

Skrypt w języku Python może służyć jako UDF z Pig za pośrednictwem `GENERATE` instrukcji. Można uruchomić skryptu za pomocą Jython lub C Python.

* Jython działa na JVM i natywnie można wywołać z Pig.
* C Python to proces zewnętrzny, dlatego dane z Pig na maszyna JVM są wysyłane do skrypt uruchomiony w procesie Python. Dane wyjściowe skryptu Python są wysyłane do Pig.

Aby określić interpreter języka Python, użyj `register` podczas odwoływania się do skryptu języka Python. Poniższe przykłady Zarejestruj skrypty Pig jako `myfuncs`:

* **Aby użyć Jython**: `register '/path/to/pigudf.py' using jython as myfuncs;`
* **Aby użyć C Python**: `register '/path/to/pigudf.py' using streaming_python as myfuncs;`

> [!IMPORTANT]
> Podczas korzystania z Jython, ścieżka do pliku pig_jython może być ścieżką lokalną lub WASB: / / ścieżki. Jednak podczas korzystania z języka Python C, możesz odwoływać pliku w systemie pliku lokalnego węzła, który używasz przesłać zadania programu Pig.

Raz po rejestracji, Pig Latin w tym przykładzie jest taki sam zarówno dla:

```pig
LOGS = LOAD 'wasb:///example/data/sample.log' as (LINE:chararray);
LOG = FILTER LOGS by LINE is not null;
DETAILS = FOREACH LOG GENERATE myfuncs.create_structure(LINE);
DUMP DETAILS;
```

Oto, co oznacza w tym przykładzie:

1. Pierwszy wiersz ładuje przykładowy plik danych `sample.log` do `LOGS`. Definiuje również każdego rekordu jako `chararray`.
2. Następnego wiersza odfiltrowuje wszystkie wartości null, wynik operacji do przechowywania `LOG`.
3. Następnie przechodzi on przez rekordy w `LOG` i używa `GENERATE` do wywołania `create_structure` metody zawarte w skrypcie Python/Jython załadowany jako `myfuncs`. `LINE` Służy do przekazania bieżącego rekordu do funkcji.
4. Na koniec utworzyć zrzutu dane wyjściowe stdout przy użyciu `DUMP` polecenia. To polecenie wyświetla wyniki po zakończeniu operacji.

### <a name="create-the-pigudfpy-file"></a>Utwórz plik pigudf.py

Na środowiska deweloperskiego, Utwórz plik tekstowy o nazwie `pigudf.py`. Zawartość pliku, należy użyć poniższego kodu:

<a name="streamingpy"></a>

```python
# Uncomment the following if using C Python
#from pig_util import outputSchema

@outputSchema("log: {(date:chararray, time:chararray, classname:chararray, level:chararray, detail:chararray)}")
def create_structure(input):
    if (input.startswith('java.lang.Exception')):
        input = input[21:len(input)] + ' - java.lang.Exception'
    date, time, classname, level, detail = input.split(' ', 4)
    return date, time, classname, level, detail
```

W tym przykładzie Pig Latin `LINE` danych wejściowych jest zdefiniowany jako chararray, ponieważ nie istnieje zgodny schematu dla danych wejściowych. Skrypt w języku Python przekształca danych w spójne schematu dla danych wyjściowych.

1. `@outputSchema` Instrukcji definiuje format danych, która jest zwracana do Pig. W takim przypadku ma **pakiet danych**, który jest typem danych Pig. Zbiorze zawiera następujące pola, które są chararray (ciągi):

   * Data — Data utworzenia wpisu dziennika
   * Czas — czas utworzenia wpisu dziennika
   * klasy — Nazwa klasy zapis został utworzony dla
   * poziom — poziom dziennika
   * Szczegóły — pełne szczegóły wpisu dziennika

2. Następnie `def create_structure(input)` definiuje funkcję, która Pig przekazuje pozycji do.

3. Przykładowe dane `sample.log`, przede wszystkim odpowiada dzień, godzina classname poziomu i szczegółów schematu. Jednak zawiera kilka wierszy, które zaczynają się od `*java.lang.Exception*`. Należy zmodyfikować te wiersze, aby być zgodna ze schematem. `if` Instrukcji sprawdza, czy te, a następnie massages dane wejściowe, aby przenieść `*java.lang.Exception*` ciąg w celu wprowadzenia danych w tekście ze schematem oczekiwane dane wyjściowe.

4. Następnie `split` polecenie służy do dzielenia danych na pierwszy znaków czterech spacji. Dane wyjściowe jest przypisany do `date`, `time`, `classname`, `level`, i `detail`.

5. Na koniec wartości są zwracane do Pig.

Gdy dane są zwracane do Pig, ma schemat spójny, zgodnie z definicją w `@outputSchema` instrukcji.

## <a name="running"></a>Przekazywanie i uruchamiania przykładów

> [!IMPORTANT]
> **SSH** kroki pracować tylko z klastra usługi HDInsight opartej na systemie Linux. **PowerShell** kroki pracy z klastra z systemem Linux lub HDInsight opartych na systemie Windows, ale wymaga klienta systemu Windows.

### <a name="ssh"></a>Protokół SSH

Aby uzyskać więcej informacji o korzystaniu z protokołu SSH, zobacz [używanie SSH z usługą HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

1. Użyj `scp` do kopiowania plików do klastra usługi HDInsight. Na przykład następujące polecenie kopiuje pliki do klastra o nazwie **mycluster**.

    ```bash
    scp hiveudf.py pigudf.py myuser@mycluster-ssh.azurehdinsight.net:
    ```

2. Używanie protokołu SSH, aby połączyć się z klastrem.

    ```bash
    ssh myuser@mycluster-ssh.azurehdinsight.net
    ```

    Aby uzyskać więcej informacji, zobacz dokument [Używanie protokołu SSH w usłudze HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

3. W sesji SSH należy dodać pliki python wcześniej przekazany do magazynu WASB dla klastra.

    ```bash
    hdfs dfs -put hiveudf.py /hiveudf.py
    hdfs dfs -put pigudf.py /pigudf.py
    ```

Po przekazaniu plików, wykonaj następujące kroki, aby uruchamiać zadania Hive i Pig.

#### <a name="use-the-hive-udf"></a>Korzystanie z programu Hive funkcji zdefiniowanej przez użytkownika

1. Aby połączyć się Hive, użyj następującego polecenia:

    ```bash
    beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http'
    ```

    To polecenie uruchamia Beeline klienta.

2. Wpisz poniższe zapytanie na `0: jdbc:hive2://headnodehost:10001/>` wiersza:

   ```hive
   add file wasb:///hiveudf.py;
   SELECT TRANSFORM (clientid, devicemake, devicemodel)
       USING 'python hiveudf.py' AS
       (clientid string, phoneLabel string, phoneHash string)
   FROM hivesampletable
   ORDER BY clientid LIMIT 50;
   ```

3. Po wprowadzeniu ostatni wiersz, należy uruchomić zadanie. Po zakończeniu zadania zwraca dane wyjściowe podobne do poniższego przykładu:

        100041    RIM 9650    d476f3687700442549a83fac4560c51c
        100041    RIM 9650    d476f3687700442549a83fac4560c51c
        100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9
        100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9
        100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9

4. Aby zakończyć Beeline, użyj następującego polecenia:

    ```hive
    !q
    ```

#### <a name="use-the-pig-udf"></a>Korzystanie z języka Pig funkcji zdefiniowanej przez użytkownika

1. Aby połączyć się pig, użyj następującego polecenia:

    ```bash
    pig
    ```

2. Wprowadź następujące instrukcje w `grunt>` wierszu:

   ```pig
   Register wasb:///pigudf.py using jython as myfuncs;
   LOGS = LOAD 'wasb:///example/data/sample.log' as (LINE:chararray);
   LOG = FILTER LOGS by LINE is not null;
   DETAILS = foreach LOG generate myfuncs.create_structure(LINE);
   DUMP DETAILS;
   ```

3. Po wprowadzeniu z następującego wiersza, należy uruchomić zadanie. Po zakończeniu zadania zwraca dane wyjściowe podobne do następujących danych:

        ((2012-02-03,20:11:56,SampleClass5,[TRACE],verbose detail for id 990982084))
        ((2012-02-03,20:11:56,SampleClass7,[TRACE],verbose detail for id 1560323914))
        ((2012-02-03,20:11:56,SampleClass8,[DEBUG],detail for id 2083681507))
        ((2012-02-03,20:11:56,SampleClass3,[TRACE],verbose detail for id 1718828806))
        ((2012-02-03,20:11:56,SampleClass3,[INFO],everything normal for id 530537821))

4. Użyj `quit` aby zakończyć powłoki Grunt, a następnie przeprowadź edycję pliku pigudf.py w lokalnym systemie plików należy wykonać następujące kroki:

    ```bash
    nano pigudf.py
    ```

5. Raz w edytorze, usuń znaczniki komentarza następujący wiersz przez usunięcie `#` znaku od początku wiersza:

    ```bash
    #from pig_util import outputSchema
    ```

    Ten wiersz modyfikuje skrypt w języku Python do pracy z Python C zamiast Jython. Po dokonaniu zmian, użyj **Ctrl + X** aby zakończyć działanie edytora. Wybierz **Y**, a następnie **Enter** Aby zapisać zmiany.

6. Użyj `pig` polecenie, aby ponownie uruchomić powłoki. Po przejściu na `grunt>` monit, użyj następującego do uruchamiania skryptu języka Python za pomocą interpreter języka Python C.

   ```pig
   Register 'pigudf.py' using streaming_python as myfuncs;
   LOGS = LOAD 'wasb:///example/data/sample.log' as (LINE:chararray);
   LOG = FILTER LOGS by LINE is not null;
   DETAILS = foreach LOG generate myfuncs.create_structure(LINE);
   DUMP DETAILS;
   ```

    Po zakończeniu tego zadania, co za wcześniej skryptu, za pomocą Jython powinna zostać wyświetlona tych samych danych wyjściowych.

### <a name="powershell-upload-the-files"></a>Środowiska PowerShell: Przekazywanie plików

Można przekazać pliki do serwera HDInsight za pomocą programu PowerShell. Aby przekazać pliki języka Python, użyj następującego skryptu:

> [!IMPORTANT] 
> Kroki opisane w tej sekcji, użyj programu Azure PowerShell. Aby uzyskać więcej informacji na temat używania programu Azure PowerShell, zobacz [jak instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azure/overview).

[!code-powershell[main](../../../powershell_scripts/hdinsight/run-python-udf/run-python-udf.ps1?range=5-41)]

> [!IMPORTANT]
> Zmień `C:\path\to` wartość do ścieżki do plików na środowiska deweloperskiego.

Ten skrypt powoduje pobranie informacji o dla klastra usługi HDInsight, a następnie wyodrębnia konta i klucz dla domyślnego konta magazynu i operacji przekazywania plików do katalogu głównego kontenera.

> [!NOTE]
> Aby uzyskać więcej informacji dotyczących przekazywania plików, zobacz [przekazywanie danych dotyczących zadań Hadoop w usłudze HDInsight](../hdinsight-upload-data.md) dokumentu.

#### <a name="powershell-use-the-hive-udf"></a>Środowiska PowerShell: Korzystanie z programu Hive funkcji zdefiniowanej przez użytkownika

Zdalne uruchamianie zapytań Hive można również programu PowerShell. Użyj następującego skryptu programu PowerShell do uruchamiania zapytań programu Hive, która używa **hiveudf.py** skryptu:

> [!IMPORTANT]
> Przed uruchomieniem, skrypt monituje o informacje o koncie HTTPs/Admin dla klastra usługi HDInsight.

[!code-powershell[main](../../../powershell_scripts/hdinsight/run-python-udf/run-python-udf.ps1?range=45-94)]

Dane wyjściowe dla **Hive** zadanie powinno wyglądać podobnie do poniższego przykładu:

    100041    RIM 9650    d476f3687700442549a83fac4560c51c
    100041    RIM 9650    d476f3687700442549a83fac4560c51c
    100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9
    100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9
    100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9

#### <a name="pig-jython"></a>Pig (Jython)

PowerShell mogą służyć do uruchamiania zadań Pig Latin. Aby uruchomić zadanie Pig Latin, która używa **pigudf.py** skryptów, użyj następującego skryptu programu PowerShell:

> [!NOTE]
> Gdy zdalne przesyłania zadania przy użyciu programu PowerShell, nie jest możliwe do użycia jako interpreter języka Python C.

[!code-powershell[main](../../../powershell_scripts/hdinsight/run-python-udf/run-python-udf.ps1?range=98-144)]

Dane wyjściowe dla **Pig** zadania powinna wyglądać podobnie do następujących danych:

    ((2012-02-03,20:11:56,SampleClass5,[TRACE],verbose detail for id 990982084))
    ((2012-02-03,20:11:56,SampleClass7,[TRACE],verbose detail for id 1560323914))
    ((2012-02-03,20:11:56,SampleClass8,[DEBUG],detail for id 2083681507))
    ((2012-02-03,20:11:56,SampleClass3,[TRACE],verbose detail for id 1718828806))
    ((2012-02-03,20:11:56,SampleClass3,[INFO],everything normal for id 530537821))

## <a name="troubleshooting"></a>Rozwiązywanie problemów

### <a name="errors-when-running-jobs"></a>Błędy podczas uruchamiania zadania

Podczas wykonywania zadania hive, może wystąpić błąd podobny do następującego tekstu:

    Caused by: org.apache.hadoop.hive.ql.metadata.HiveException: [Error 20001]: An error occurred while reading or writing to your custom script. It may have crashed with an error.

Ten problem może być spowodowane zakończenia wierszy w pliku języka Python. Wiele domyślne edytory systemu Windows przy użyciu CRLF jako zakończenie wiersza, ale aplikacje w systemie Linux zwykle oczekiwać wysuwu wiersza.

Poniższe instrukcje środowiska PowerShell umożliwia Usuń znaki CR przed przekazaniem go do usługi HDInsight:

[!code-powershell[main](../../../powershell_scripts/hdinsight/run-python-udf/run-python-udf.ps1?range=148-150)]

### <a name="powershell-scripts"></a>Skrypty programu PowerShell

Zawierają zarówno przykładowe skrypty środowiska PowerShell służące do uruchamiania w przykładach komentarze wiersza, który wyświetla dane wyjściowe błędów dla zadania. Jeśli nie widzisz oczekiwane dane wyjściowe zadania, usuń znaczniki komentarza z następującego wiersza i zobacz, czy informacje o błędzie wskazuje na problem.

[!code-powershell[main](../../../powershell_scripts/hdinsight/run-python-udf/run-python-udf.ps1?range=135-139)]

Informacje o błędzie (STDERR) oraz wyniku zadania (STDOUT) również są rejestrowane w magazynie z usługi HDInsight.

| Dla tego zadania... | Przyjrzyj się te pliki w kontenerze obiektów blob |
| --- | --- |
| Hive |/ HivePython/stderr<p>/ HivePython/stdout |
| Pig |/ PigPython/stderr<p>/ PigPython/stdout |

## <a name="next"></a>Następne kroki

Jeśli musisz załadować modułów środowiska Python, które domyślnie nie są dostępne, zobacz [wdrażanie modułu do usługi Azure HDInsight](http://blogs.msdn.com/b/benjguin/archive/2014/03/03/how-to-deploy-a-python-module-to-windows-azure-hdinsight.aspx).

Dla innych sposobów korzystania wieprzowy, Hive i aby dowiedzieć się więcej o korzystaniu z MapReduce, można znaleźć w następujących dokumentach:

* [Korzystanie z programu Hive z usługą HDInsight](hdinsight-use-hive.md)
* [Korzystanie z języka Pig z usługą HDInsight](hdinsight-use-pig.md)
* [Korzystać z usługi MapReduce z usługą HDInsight](hdinsight-use-mapreduce.md)
