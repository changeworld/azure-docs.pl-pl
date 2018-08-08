---
title: Python funkcji zdefiniowanej przez użytkownika przy użyciu Apache Hive i Pig — usługa Azure HDInsight
description: Dowiedz się, jak używać języka Python określone funkcje użytkownika (UDF) z technologiami Hive i Pig w HDInsight, stosu technologii Hadoop na platformie Azure.
services: hdinsight
ms.service: hdinsight
author: jasonwhowell
ms.author: jasonh
editor: jasonwhowell
ms.topic: conceptual
ms.date: 02/27/2018
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.openlocfilehash: 3bb8546ccf15788317370ccfe654fddbd0fb0cf9
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/07/2018
ms.locfileid: "39592358"
---
# <a name="use-python-user-defined-functions-udf-with-hive-and-pig-in-hdinsight"></a>Użyj języka Python funkcje zdefiniowane użytkownika (UDF) z technologiami Hive i Pig w HDInsight

Dowiedz się, jak używać języka Python funkcje zdefiniowane przez użytkownika (UDF) przy użyciu Apache Hive i Pig na platformie Hadoop w usłudze Azure HDInsight.

## <a name="python"></a>Python na HDInsight

Python2.7 jest instalowany domyślnie w HDInsight 3.0 lub nowszym. Apache Hive można za pomocą tej wersji języka Python do przetwarzania strumieniowego. Przetwarzanie Stream używa STDOUT i STDIN do przekazywania danych między Hive, jak i funkcji definiowanych przez użytkownika.

HDInsight obejmuje również Jython, który jest implementacją języka Python, napisane w języku Java. Jython działa bezpośrednio na maszynie wirtualnej Java i nie korzysta z przesyłania strumieniowego. Jython jest zalecane interpreter języka Python, korzystając z języka Python przy użyciu programu Pig.

> [!WARNING]
> Kroki opisane w tym dokumencie upewnij następujące założenia: 
>
> * Tworzenia skryptów w języku Python na lokalne Środowisko deweloperskie.
> * Przekaż skryptów do HDInsight przy użyciu `scp` polecenia lokalnej sesji programu Bash lub dostarczonego skryptu programu PowerShell.
>
> Jeśli chcesz używać [usługi Azure Cloud Shell (powłoka bash)](https://docs.microsoft.com/azure/cloud-shell/overview) pracować HDInsight, należy w wersji zapoznawczej:
>
> * Utwórz skrypty w środowisku powłoki w chmurze.
> * Użyj `scp` przekazywania plików z usługi cloud shell do HDInsight.
> * Użyj `ssh` z usługi cloud shell, aby nawiązać połączenie z HDInsight i uruchamianie przykładów.

## <a name="hivepython"></a>Hive funkcji zdefiniowanej przez użytkownika

Python mogą być używane jako funkcji zdefiniowanej przez użytkownika z programu Hive za pośrednictwem HiveQL `TRANSFORM` instrukcji. Na przykład wywołuje następujące HiveQL `hiveudf.py` pliku przechowywanego w domyślne konto usługi Azure Storage dla klastra.

**HDInsight opartych na systemie Linux**

```hiveql
add file wasb:///hiveudf.py;

SELECT TRANSFORM (clientid, devicemake, devicemodel)
    USING 'python hiveudf.py' AS
    (clientid string, phoneLable string, phoneHash string)
FROM hivesampletable
ORDER BY clientid LIMIT 50;
```

**HDInsight z systemem Windows**

```hiveql
add file wasb:///hiveudf.py;

SELECT TRANSFORM (clientid, devicemake, devicemodel)
    USING 'D:\Python27\python.exe hiveudf.py' AS
    (clientid string, phoneLable string, phoneHash string)
FROM hivesampletable
ORDER BY clientid LIMIT 50;
```

> [!NOTE]
> W klastrach HDInsight oparte na Windows `USING` klauzuli należy określić pełną ścieżkę do python.exe.

Poniżej przedstawiono, jak działa w tym przykładzie:

1. `add file` Dodaje oświadczenie na początku pliku `hiveudf.py` pliku do rozproszonej pamięci podręcznej, więc nie jest dostępny przez wszystkie węzły w klastrze.
2. `SELECT TRANSFORM ... USING` Instrukcja wybiera dane z `hivesampletable`. Przekazuje wartości clientid, devicemake i devicemodel `hiveudf.py` skryptu.
3. `AS` Klauzuli w tym artykule opisano pola, zwrócone w wyniku `hiveudf.py`.

<a name="streamingpy"></a>

### <a name="create-the-hiveudfpy-file"></a>Utwórz plik hiveudf.py


W środowisku programowania Utwórz plik tekstowy o nazwie `hiveudf.py`. Użyj poniższego kodu, jako zawartość pliku:

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

1. Przeczytaj wiersz danych stdin.
2. Końcowego znaku nowego wiersza zostanie usunięty, za pomocą `string.strip(line, "\n ")`.
3. Podczas wykonywania przetwarzania strumienia, pojedynczy wiersz zawiera wszystkie wartości zawierającym znak tabulacji od każdej wartości. Dlatego `string.split(line, "\t")` może służyć do dzielenia danych wejściowych na poszczególnych kartach zwracanie tylko pola.
4. Po zakończeniu przetwarzania danych wyjściowych musi być przystosowana do strumienia wyjściowego STDOUT jako pojedynczy wiersz z kartą między każdego pola. Na przykład `print "\t".join([clientid, phone_label, hashlib.md5(phone_label).hexdigest()])`.
5. `while` Powtórzeniu pętli, dopóki nie `line` jest do odczytu.

Dane wyjściowe skryptu jest łączenia wartości wejściowych dla `devicemake` i `devicemodel`oraz skrót wartości połączonych.

Zobacz [uruchamiania przykładów](#running) dotyczące sposobu przeprowadzania tego przykładu w klastrze usługi HDInsight.

## <a name="pigpython"></a>Pig UDF

Skrypt języka Python może służyć jako funkcji zdefiniowanej przez użytkownika z języka Pig za pośrednictwem `GENERATE` instrukcji. Można uruchomić skrypt z użyciem Jython lub C Python.

* Jython działa na maszyna JVM i natywnie można wywołać z języka Pig.
* C Python to proces zewnętrzny, dlatego dane z języka Pig na maszyna JVM zostanie wysłane do skryptu działający w procesie języka Python. Dane wyjściowe skryptu języka Python jest wysyłany do Pig.

Aby określić interpreter języka Python, użyj `register` podczas odwoływania się do skryptu języka Python. Poniższe przykłady zarejestrować skrypty przy użyciu programu Pig jako `myfuncs`:

* **Aby użyć Jython**: `register '/path/to/pigudf.py' using jython as myfuncs;`
* **Do używania środowiska Python C**: `register '/path/to/pigudf.py' using streaming_python as myfuncs;`

> [!IMPORTANT]
> Korzystając z Jython, ścieżka do pliku pig_jython może być ścieżką lokalną lub WASB: / / ścieżki. Jednak gdy przy użyciu języka Python w języku C, musi odwoływać plik na lokalnym systemie plików, którego używasz, można przesłać zadania Pig węzła.

Raz po rejestracji, Pig Latin, w tym przykładzie jest taki sam zarówno dla:

```pig
LOGS = LOAD 'wasb:///example/data/sample.log' as (LINE:chararray);
LOG = FILTER LOGS by LINE is not null;
DETAILS = FOREACH LOG GENERATE myfuncs.create_structure(LINE);
DUMP DETAILS;
```

Poniżej przedstawiono, jak działa w tym przykładzie:

1. Pierwszy wiersz ładuje przykładowy plik danych `sample.log` do `LOGS`. Definiuje również każdego wybranego rekordu jako `chararray`.
2. Następny wiersz odfiltrowuje wszystkie wartości null, wynik operacji do przechowywania `LOG`.
3. Następnie go wykonuje iterację na rekordy w `LOG` i używa `GENERATE` do wywołania `create_structure` metoda zawarty w skrypcie języka Python/Jython załadowany jako `myfuncs`. `LINE` Służy do przekazania bieżącego rekordu do funkcji.
4. Na koniec dane wyjściowe są zrzucany stdout przy użyciu `DUMP` polecenia. To polecenie wyświetla wyników po zakończeniu operacji.

### <a name="create-the-pigudfpy-file"></a>Utwórz plik pigudf.py

W środowisku programowania Utwórz plik tekstowy o nazwie `pigudf.py`. Użyj poniższego kodu, jako zawartość pliku:

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

W tym przykładzie Pig Latin `LINE` danych wejściowych jest zdefiniowany jako chararray, ponieważ nie istnieje żaden spójny schemat dla danych wejściowych. Skrypt w języku Python przekształca je w spójny schemat dla danych wyjściowych.

1. `@outputSchema` Instrukcja definiuje format danych, które są zwracane do Pig. W tym przypadku ma **pakiet danych**, który jest typem danych Pig. Pakiet zawiera następujące pola, które są chararray (parametry):

   * Data — Data utworzenia wpisu dziennika
   * czas — od czasu utworzenia wpisu dziennika
   * klasy — nazwy klasy wpis został utworzony dla
   * poziom — poziom dziennika
   * Szczegóły — pełne szczegóły wpisu dziennika

2. Następnie `def create_structure(input)` definiuje funkcję, która Pig przekazuje pozycji do.

3. Przykładowe dane, `sample.log`przede wszystkim jest zgodny z dzień, godzina classname poziomu oraz szczegółowo schematu. Jednak zawiera kilka wierszy, które zaczynają się od `*java.lang.Exception*`. Te wiersze należy zmodyfikować w taki sposób, w celu dostosowania do schematu. `if` Instrukcji sprawdza, czy dla osób, a następnie massages dane wejściowe, aby przenieść `*java.lang.Exception*` ciągu do końca, przywracanie danych wbudowane ze schematem oczekiwanych danych wyjściowych.

4. Następnie `split` polecenie służy do dzielenia danych na pierwsze cztery spacjami. Wynik jest przypisany do `date`, `time`, `classname`, `level`, i `detail`.

5. Na koniec wartości są zwracane do Pig.

Gdy dane są zwracane do Pig, ma spójny schemat, zgodnie z definicją w `@outputSchema` instrukcji.

## <a name="running"></a>Przekaż i uruchom przykłady

> [!IMPORTANT]
> **SSH** kroki działają tylko z klastrem HDInsight opartych na systemie Linux. **PowerShell** kroki pracy z klastrów systemu Linux albo systemem Windows HDInsight, ale wymaga klienta Windows.

### <a name="ssh"></a>Protokół SSH

Aby uzyskać więcej informacji na temat korzystania z protokołu SSH, zobacz [użycia protokołu SSH w usłudze HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

1. Użyj `scp` można skopiować plików do klastra usługi HDInsight. Na przykład następujące polecenie kopiuje pliki do klastra o nazwie **mycluster**.

    ```bash
    scp hiveudf.py pigudf.py myuser@mycluster-ssh.azurehdinsight.net:
    ```

2. Łączenie z klastrem za pomocą protokołu SSH.

    ```bash
    ssh myuser@mycluster-ssh.azurehdinsight.net
    ```

    Aby uzyskać więcej informacji, zobacz dokument [Używanie protokołu SSH w usłudze HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

3. W sesji SSH należy dodać pliki języka python, wcześniej przekazane do magazynu WASB dla klastra.

    ```bash
    hdfs dfs -put hiveudf.py /hiveudf.py
    hdfs dfs -put pigudf.py /pigudf.py
    ```

Po przekazaniu plików, wykonaj następujące kroki, aby uruchamiać zadania Hive i Pig.

#### <a name="use-the-hive-udf"></a>Korzystanie z programu Hive w funkcji zdefiniowanej przez użytkownika

1. Połączyć się z programu Hive, użyj następującego polecenia:

    ```bash
    beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http'
    ```

    To polecenie uruchamia klienta z usługi Beeline.

2. Wprowadź następujące zapytanie w `0: jdbc:hive2://headnodehost:10001/>` wiersz:

   ```hive
   add file wasb:///hiveudf.py;
   SELECT TRANSFORM (clientid, devicemake, devicemodel)
       USING 'python hiveudf.py' AS
       (clientid string, phoneLabel string, phoneHash string)
   FROM hivesampletable
   ORDER BY clientid LIMIT 50;
   ```

3. Po wprowadzeniu ostatni wiersz, zadanie powinno zostać uruchomione. Po zakończeniu zadania zwraca dane wyjściowe podobne do poniższego przykładu:

        100041    RIM 9650    d476f3687700442549a83fac4560c51c
        100041    RIM 9650    d476f3687700442549a83fac4560c51c
        100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9
        100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9
        100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9

4. Aby wyjść z usługi Beeline, użyj następującego polecenia:

    ```hive
    !q
    ```

#### <a name="use-the-pig-udf"></a>Korzystanie z języka Pig funkcji zdefiniowanej przez użytkownika

1. Aby połączyć z języka pig, użyj następującego polecenia:

    ```bash
    pig
    ```

2. Wprowadź następujące instrukcje w `grunt>` wiersz:

   ```pig
   Register wasb:///pigudf.py using jython as myfuncs;
   LOGS = LOAD 'wasb:///example/data/sample.log' as (LINE:chararray);
   LOG = FILTER LOGS by LINE is not null;
   DETAILS = foreach LOG generate myfuncs.create_structure(LINE);
   DUMP DETAILS;
   ```

3. Po wprowadzeniu następujący wiersz, zadanie powinno zostać uruchomione. Po zakończeniu zadania zwraca dane wyjściowe podobne do następujących danych:

        ((2012-02-03,20:11:56,SampleClass5,[TRACE],verbose detail for id 990982084))
        ((2012-02-03,20:11:56,SampleClass7,[TRACE],verbose detail for id 1560323914))
        ((2012-02-03,20:11:56,SampleClass8,[DEBUG],detail for id 2083681507))
        ((2012-02-03,20:11:56,SampleClass3,[TRACE],verbose detail for id 1718828806))
        ((2012-02-03,20:11:56,SampleClass3,[INFO],everything normal for id 530537821))

4. Użyj `quit` aby zakończyć powłoki Grunt, a następnie edytuj plik pigudf.py na lokalny system plików należy wykonać następujące kroki:

    ```bash
    nano pigudf.py
    ```

5. Jeden raz w edytorze, Usuń komentarz następujący wiersz, usuwając `#` znaków od początku linii:

    ```bash
    #from pig_util import outputSchema
    ```

    Ten wiersz modyfikuje skrypt języka Python do pracy z językiem Python C, zamiast Jython. Po dokonaniu zmian, użyj **Ctrl + X** aby zakończyć działanie edytora. Wybierz **Y**, a następnie **Enter** Aby zapisać zmiany.

6. Użyj `pig` polecenie, aby ponownie uruchomić powłoki. Po przejściu na `grunt>` monit, użyj następującego do uruchomienia skryptu języka Python za pomocą interpreter języka Python C.

   ```pig
   Register 'pigudf.py' using streaming_python as myfuncs;
   LOGS = LOAD 'wasb:///example/data/sample.log' as (LINE:chararray);
   LOG = FILTER LOGS by LINE is not null;
   DETAILS = foreach LOG generate myfuncs.create_structure(LINE);
   DUMP DETAILS;
   ```

    Po wykonaniu tego zadania jako po wcześniej uruchomiono skrypt z użyciem Jython powinien być widoczny ten sam wynik.

### <a name="powershell-upload-the-files"></a>Program PowerShell: Przekazywanie plików

Aby przekazać pliki do serwera HDInsight, można użyć programu PowerShell. Aby przekazać pliki języka Python, użyj następującego skryptu:

> [!IMPORTANT] 
> Kroki opisane w tej sekcji przy użyciu programu Azure PowerShell. Aby uzyskać więcej informacji na temat korzystania z programu Azure PowerShell, zobacz [jak zainstalować i skonfigurować program Azure PowerShell](/powershell/azure/overview).

[!code-powershell[main](../../../powershell_scripts/hdinsight/run-python-udf/run-python-udf.ps1?range=5-41)]

> [!IMPORTANT]
> Zmiana `C:\path\to` wartość do ścieżki do plików w środowisku deweloperskim.

Ten skrypt pobiera informacje o klastrze HDInsight, a następnie wyodrębnia konta i klucz dla domyślnego konta magazynu i przekazuje te pliki w katalogu głównym kontenera.

> [!NOTE]
> Aby uzyskać więcej informacji na temat przekazywania plików, zobacz [przekazywanie danych na potrzeby zadań usługi Hadoop w HDInsight](../hdinsight-upload-data.md) dokumentu.

#### <a name="powershell-use-the-hive-udf"></a>Program PowerShell: Użyj gałąź funkcji zdefiniowanej przez użytkownika

Program PowerShell mogą również zdalnie uruchamiać zapytania Hive. Poniższy skrypt programu PowerShell umożliwia uruchomienie zapytania programu Hive, który używa **hiveudf.py** skryptu:

> [!IMPORTANT]
> Przed uruchomieniem, skrypt wyświetli monit o podanie informacji o koncie HTTPs/administratora dla klastra usługi HDInsight.

[!code-powershell[main](../../../powershell_scripts/hdinsight/run-python-udf/run-python-udf.ps1?range=45-94)]

Dane wyjściowe **Hive** zadania powinien wyglądać podobnie do następująco:

    100041    RIM 9650    d476f3687700442549a83fac4560c51c
    100041    RIM 9650    d476f3687700442549a83fac4560c51c
    100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9
    100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9
    100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9

#### <a name="pig-jython"></a>Pig (Jython)

Program PowerShell można również uruchamiać zadania Pig Latin. Do uruchomienia zadania Pig Latin, który używa **pigudf.py** skryptu, użyj następującego skryptu programu PowerShell:

> [!NOTE]
> Podczas zdalnego przesyłania zadania przy użyciu programu PowerShell, nie jest możliwe użycie języka Python w języku C jako interpreter.

[!code-powershell[main](../../../powershell_scripts/hdinsight/run-python-udf/run-python-udf.ps1?range=98-144)]

Dane wyjściowe **Pig** zadania powinien wyglądać podobnie do następujących danych:

    ((2012-02-03,20:11:56,SampleClass5,[TRACE],verbose detail for id 990982084))
    ((2012-02-03,20:11:56,SampleClass7,[TRACE],verbose detail for id 1560323914))
    ((2012-02-03,20:11:56,SampleClass8,[DEBUG],detail for id 2083681507))
    ((2012-02-03,20:11:56,SampleClass3,[TRACE],verbose detail for id 1718828806))
    ((2012-02-03,20:11:56,SampleClass3,[INFO],everything normal for id 530537821))

## <a name="troubleshooting"></a>Rozwiązywanie problemów

### <a name="errors-when-running-jobs"></a>Błędy podczas uruchamiania zadania

Podczas uruchamiania zadania hive, może wystąpić błąd podobny do następującego tekstu:

    Caused by: org.apache.hadoop.hive.ql.metadata.HiveException: [Error 20001]: An error occurred while reading or writing to your custom script. It may have crashed with an error.

Ten problem może być spowodowane końce wierszy w pliku języka Python. Wiele Windows edytory domyślną wartość zwykle sygnalizowany znakiem CRLF jako koniec wiersza, ale aplikacje dla systemu Linux oczekiwać LF.

Następujące instrukcje programu PowerShell służy do usuwania znaków CR przed przekazaniem go do usługi HDInsight:

[!code-powershell[main](../../../powershell_scripts/hdinsight/run-python-udf/run-python-udf.ps1?range=148-150)]

### <a name="powershell-scripts"></a>Skrypty programu PowerShell

Przykład skryptów programu PowerShell umożliwia uruchamianie przykłady zawierają zakomentowanego wiersza, który wyświetla dane wyjściowe błędu dla zadania. Jeśli nie widzisz oczekiwanych danych wyjściowych dla zadania, Usuń komentarz następujący wiersz i zobaczyć, jeśli informacje o błędzie wskazuje na problem.

[!code-powershell[main](../../../powershell_scripts/hdinsight/run-python-udf/run-python-udf.ps1?range=135-139)]

Informacje o błędzie (STDERR) oraz wynik zadania (STDOUT) również są rejestrowane w magazynie usługi HDInsight.

| Dla tego zadania... | Przyjrzyj się tych plików w kontenerze obiektów blob |
| --- | --- |
| Hive |/ HivePython/stderr<p>/ HivePython/stdout |
| Pig |/ PigPython/stderr<p>/ PigPython/stdout |

## <a name="next"></a>Następne kroki

Jeśli musisz załadować modułów języka Python, które nie są domyślnie dostępne, zobacz [sposób wdrażania modułu w usłudze Azure HDInsight](http://blogs.msdn.com/b/benjguin/archive/2014/03/03/how-to-deploy-a-python-module-to-windows-azure-hdinsight.aspx).

Inne sposoby użycia Pig, Hive i aby dowiedzieć się więcej o korzystaniu z MapReduce, zobacz następujące dokumenty:

* [Korzystanie z programu Hive z usługą HDInsight](hdinsight-use-hive.md)
* [Korzystanie z języka Pig z usługą HDInsight](hdinsight-use-pig.md)
* [Używanie technologii MapReduce z HDInsight](hdinsight-use-mapreduce.md)
