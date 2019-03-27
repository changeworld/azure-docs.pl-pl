---
title: Python funkcji zdefiniowanej przez użytkownika za pomocą programu Apache Hive i Apache Pig — usługa Azure HDInsight
description: Dowiedz się, jak używać języka Python określone funkcje użytkownika (UDF) z Apache Hive i Apache Pig w HDInsight, stosu technologii Apache Hadoop na platformie Azure.
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: conceptual
ms.date: 03/15/2019
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.openlocfilehash: adcfb308bbbc8e3de456c4e7a71c543f988db02a
ms.sourcegitcommit: f24fdd1ab23927c73595c960d8a26a74e1d12f5d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2019
ms.locfileid: "58497996"
---
# <a name="use-python-user-defined-functions-udf-with-apache-hive-and-apache-pig-in-hdinsight"></a>Funkcje (UDF) zdefiniowane przez użytkownika języka Python korzystanie z programu Apache Hive i Apache Pig w HDInsight

Dowiedz się, jak używać języka Python funkcje zdefiniowane przez użytkownika (UDF) przy użyciu Apache Hive i Apache Pig na platformie Apache Hadoop w usłudze Azure HDInsight.

## <a name="python"></a>Python na HDInsight

Python2.7 jest instalowany domyślnie w HDInsight 3.0 lub nowszym. Apache Hive można za pomocą tej wersji języka Python do przetwarzania strumieniowego. Przetwarzanie Stream używa STDOUT i STDIN do przekazywania danych między Hive, jak i funkcji definiowanych przez użytkownika.

HDInsight obejmuje również Jython, który jest implementacją języka Python, napisane w języku Java. Jython działa bezpośrednio na maszynie wirtualnej Java i nie korzysta z przesyłania strumieniowego. Jython jest zalecane interpreter języka Python, korzystając z języka Python przy użyciu programu Pig.

## <a name="prerequisites"></a>Wymagania wstępne

* **Klaster Hadoop w HDInsight**. Zobacz [Rozpoczynanie pracy z usługą HDInsight w systemie Linux](apache-hadoop-linux-tutorial-get-started.md).
* **Klient SSH**. Aby uzyskać więcej informacji, zobacz [Łączenie się z usługą HDInsight (Apache Hadoop) przy użyciu protokołu SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).
* [Schemat identyfikatora URI](../hdinsight-hadoop-linux-information.md#URI-and-scheme) do obsługi klastrów magazynu podstawowego. Takie rozwiązanie byłoby wasb: / / dla usługi Azure Storage, abfs: / / dla usługi Azure Data Lake Storage Gen2 lub systemu plików adl: / / dla usługi Azure Data Lake Storage Gen1. Bezpieczny transfer jest włączona dla usługi Azure Storage lub Data Lake Storage Gen2, identyfikator URI będzie mieć wasbs: / / lub abfss: / / odpowiednio Zobacz też [bezpieczny transfer](../../storage/common/storage-require-secure-transfer.md).
* **Możliwe zmiany w konfiguracji magazynu.**  Zobacz [konfiguracji magazynu](#storage-configuration) korzystania z rodzaju konta magazynu `BlobStorage`.
* Opcjonalny.  Jeśli jest planowane przy użyciu programu PowerShell, konieczne będzie [modułu AZ](https://docs.microsoft.com/powershell/azure/new-azureps-module-az) zainstalowane.

> [!NOTE]  
> Konto magazynu używane w tym artykule było usługi Azure Storage z [bezpieczny transfer](../../storage/common/storage-require-secure-transfer.md) włączone i w związku z tym `wasbs` jest używana w całej tego artykułu.

## <a name="storage-configuration"></a>Konfiguracja usługi Storage
Jeśli konto magazynu używane na tego rodzaju, jest wymagana żadna akcja `Storage (general purpose v1)` lub `StorageV2 (general purpose v2)`.  Ten proces w tym artykule generuje dane wyjściowe do co najmniej `/tezstaging`.  Domyślna konfiguracja hadoop będzie zawierać `/tezstaging` w `fs.azure.page.blob.dir` konfiguracyjną w `core-site.xml` usługi `HDFS`.  Ta konfiguracja spowoduje, że dane wyjściowe do katalogu jako stronicowe obiekty BLOB, które nie są obsługiwane dla rodzaju konta magazynu `BlobStorage`.  Aby użyć `BlobStorage` w tym artykule, należy usunąć `/tezstaging` z `fs.azure.page.blob.dir` zmiennej konfiguracyjnej.  Konfiguracja jest możliwy z [interfejsu użytkownika Ambari](../hdinsight-hadoop-manage-ambari.md).  W przeciwnym razie zostanie wyświetlony komunikat o błędzie: `Page blob is not supported for this account type.`

> [!WARNING]  
> Kroki opisane w tym dokumencie upewnij następujące założenia:  
>
> * Tworzenia skryptów w języku Python na lokalne Środowisko deweloperskie.
> * Przekaż skryptów do HDInsight przy użyciu `scp` polecenia lub dostarczonego skryptu programu PowerShell.
>
> Jeśli chcesz używać [usługi Azure Cloud Shell (powłoka bash)](https://docs.microsoft.com/azure/cloud-shell/overview) można pracować z usługą HDInsight, należy:
>
> * Utwórz skrypty w środowisku powłoki w chmurze.
> * Użyj `scp` przekazywania plików z usługi cloud shell do HDInsight.
> * Użyj `ssh` z usługi cloud shell, aby nawiązać połączenie z HDInsight i uruchamianie przykładów.

## <a name="hivepython"></a>Apache Hive UDF

Python mogą być używane jako funkcji zdefiniowanej przez użytkownika z programu Hive za pośrednictwem HiveQL `TRANSFORM` instrukcji. Na przykład wywołuje następujące HiveQL `hiveudf.py` pliku przechowywanego w domyślne konto usługi Azure Storage dla klastra.

```hiveql
add file wasbs:///hiveudf.py;

SELECT TRANSFORM (clientid, devicemake, devicemodel)
    USING 'python hiveudf.py' AS
    (clientid string, phoneLabel string, phoneHash string)
FROM hivesampletable
ORDER BY clientid LIMIT 50;
```

Poniżej przedstawiono, jak działa w tym przykładzie:

1. `add file` Dodaje oświadczenie na początku pliku `hiveudf.py` pliku do rozproszonej pamięci podręcznej, więc nie jest dostępny przez wszystkie węzły w klastrze.
2. `SELECT TRANSFORM ... USING` Instrukcja wybiera dane z `hivesampletable`. Przekazuje wartości clientid, devicemake i devicemodel `hiveudf.py` skryptu.
3. `AS` Klauzuli w tym artykule opisano pola, zwrócone w wyniku `hiveudf.py`.

<a name="streamingpy"></a>

### <a name="create-file"></a>Utwórz plik

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

1. Odczytuje wiersz danych z STDIN.
2. Końcowego znaku nowego wiersza zostanie usunięty, za pomocą `string.strip(line, "\n ")`.
3. Podczas wykonywania przetwarzania strumienia, pojedynczy wiersz zawiera wszystkie wartości zawierającym znak tabulacji od każdej wartości. Dlatego `string.split(line, "\t")` może służyć do dzielenia danych wejściowych na poszczególnych kartach zwracanie tylko pola.
4. Po zakończeniu przetwarzania danych wyjściowych musi być przystosowana do strumienia wyjściowego STDOUT jako pojedynczy wiersz z kartą między każdego pola. Na przykład `print "\t".join([clientid, phone_label, hashlib.md5(phone_label).hexdigest()])`.
5. `while` Powtórzeniu pętli, dopóki nie `line` jest do odczytu.

Dane wyjściowe skryptu jest łączenia wartości wejściowych dla `devicemake` i `devicemodel`oraz skrót wartości połączonych.

### <a name="upload-file-shell"></a>Przekaż plik (powłoka)
W poniższych poleceń Zastąp `sshuser` rzeczywiste nazwy użytkownika, jeśli jest inna.  Zastąp `mycluster` nazwą rzeczywistego klastra.  Upewnij się, że katalog roboczy jest, gdzie znajduje się plik.

1. Użyj `scp` można skopiować plików do klastra usługi HDInsight. Edytuj, a następnie wprowadź poniższe polecenie:

    ```cmd
    scp hiveudf.py sshuser@mycluster-ssh.azurehdinsight.net:
    ```

2. Łączenie z klastrem za pomocą protokołu SSH.  Edytuj, a następnie wprowadź poniższe polecenie:

    ```cmd
    ssh sshuser@mycluster-ssh.azurehdinsight.net
    ```

3. W sesji SSH należy dodać pliki języka python, wcześniej przekazane do magazynu klastra.

    ```bash
    hdfs dfs -put hiveudf.py /hiveudf.py
    ```

### <a name="use-hive-udf-shell"></a>Użyj programu Hive funkcji zdefiniowanej przez użytkownika (powłoka)

1. Aby połączyć z programu Hive, użyj następującego polecenia z otwartych sesji protokołu SSH:

    ```bash
    beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http'
    ```

    To polecenie uruchamia klienta z usługi Beeline.

2. Wprowadź następujące zapytanie w `0: jdbc:hive2://headnodehost:10001/>` wiersz:

   ```hive
   add file wasbs:///hiveudf.py;
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

4. Aby wyjść z usługi Beeline, wprowadź następujące polecenie:

    ```hive
    !q
    ```

### <a name="upload-file-powershell"></a>Przekaż plik (PowerShell)

> [!IMPORTANT]  
> Te skrypty programu PowerShell nie będą działać, jeśli [bezpieczny transfer](../../storage/common/storage-require-secure-transfer.md) jest włączona.  Użyj poleceń powłoki, albo wyłącz bezpieczny transfer.

Program PowerShell mogą również zdalnie uruchamiać zapytania Hive. Upewnij się, katalog roboczy gdzie `hiveudf.py` znajduje się.  Poniższy skrypt programu PowerShell umożliwia uruchomienie zapytania programu Hive, który używa `hiveudf.py` skryptu:

```PowerShell
# Login to your Azure subscription
# Is there an active Azure subscription?
$sub = Get-AzSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Connect-AzAccount
}

# Revise file path as needed
$pathToStreamingFile = ".\hiveudf.py"

# Get cluster info
$clusterName = Read-Host -Prompt "Enter the HDInsight cluster name"
$clusterInfo = Get-AzHDInsightCluster -ClusterName $clusterName
$resourceGroup = $clusterInfo.ResourceGroup
$storageAccountName=$clusterInfo.DefaultStorageAccount.split('.')[0]
$container=$clusterInfo.DefaultStorageContainer
$storageAccountKey=(Get-AzStorageAccountKey `
   -ResourceGroupName $resourceGroup `
   -Name $storageAccountName)[0].Value

# Create an Azure Storage context
$context = New-AzStorageContext `
    -StorageAccountName $storageAccountName `
    -StorageAccountKey $storageAccountKey

# Upload local files to an Azure Storage blob
Set-AzStorageBlobContent `
    -File $pathToStreamingFile `
    -Blob "hiveudf.py" `
    -Container $container `
    -Context $context
```

> [!NOTE]  
> Aby uzyskać więcej informacji na temat przekazywania plików, zobacz [przekazywanie danych na potrzeby zadań usługi Apache Hadoop w HDInsight](../hdinsight-upload-data.md) dokumentu.


#### <a name="use-hive-udf"></a>Use Hive funkcji zdefiniowanej przez użytkownika


```PowerShell
# Script should stop on failures
$ErrorActionPreference = "Stop"

# Login to your Azure subscription
# Is there an active Azure subscription?
$sub = Get-AzSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Connect-AzAccount
}

# Get cluster info
$clusterName = Read-Host -Prompt "Enter the HDInsight cluster name"
$creds=Get-Credential -UserName "admin" -Message "Enter the login for the cluster"

$HiveQuery = "add file wasbs:///hiveudf.py;" +
                "SELECT TRANSFORM (clientid, devicemake, devicemodel) " +
                "USING 'python hiveudf.py' AS " +
                "(clientid string, phoneLabel string, phoneHash string) " +
                "FROM hivesampletable " +
                "ORDER BY clientid LIMIT 50;"

# Create Hive job object
$jobDefinition = New-AzHDInsightHiveJobDefinition `
    -Query $HiveQuery

# For status bar updates
$activity="Hive query"

# Progress bar (optional)
Write-Progress -Activity $activity -Status "Starting query..."

# Start defined Azure HDInsight job on specified cluster.
$job = Start-AzHDInsightJob `
    -ClusterName $clusterName `
    -JobDefinition $jobDefinition `
    -HttpCredential $creds

# Progress bar (optional)
Write-Progress -Activity $activity -Status "Waiting on query to complete..."

# Wait for completion or failure of specified job
Wait-AzHDInsightJob `
    -JobId $job.JobId `
    -ClusterName $clusterName `
    -HttpCredential $creds

# Uncomment the following to see stderr output
<#
Get-AzHDInsightJobOutput `
   -Clustername $clusterName `
   -JobId $job.JobId `
   -HttpCredential $creds `
   -DisplayOutputType StandardError
#>

# Progress bar (optional)
Write-Progress -Activity $activity -Status "Retrieving output..."

# Gets the log output
Get-AzHDInsightJobOutput `
    -Clustername $clusterName `
    -JobId $job.JobId `
    -HttpCredential $creds
```

Dane wyjściowe **Hive** zadania powinien wyglądać podobnie do następująco:

    100041    RIM 9650    d476f3687700442549a83fac4560c51c
    100041    RIM 9650    d476f3687700442549a83fac4560c51c
    100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9
    100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9
    100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9


## <a name="pigpython"></a>Apache Pig UDF

Skrypt języka Python może służyć jako funkcji zdefiniowanej przez użytkownika z języka Pig za pośrednictwem `GENERATE` instrukcji. Można uruchomić skrypt z użyciem Jython lub C Python.

* Jython działa na maszyna JVM i natywnie można wywołać z języka Pig.
* C Python to proces zewnętrzny, dlatego dane z języka Pig na maszyna JVM zostanie wysłane do skryptu działający w procesie języka Python. Dane wyjściowe skryptu języka Python jest wysyłany do Pig.

Aby określić interpreter języka Python, użyj `register` podczas odwoływania się do skryptu języka Python. Poniższe przykłady zarejestrować skrypty przy użyciu programu Pig jako `myfuncs`:

* **Aby użyć Jython**: `register '/path/to/pigudf.py' using jython as myfuncs;`
* **Do używania środowiska Python C**: `register '/path/to/pigudf.py' using streaming_python as myfuncs;`

> [!IMPORTANT]  
> Korzystając z Jython, ścieżka do pliku pig_jython może być ścieżką lokalną lub WASBS: / / ścieżki. Jednak gdy przy użyciu języka Python w języku C, musi odwoływać plik na lokalnym systemie plików, którego używasz, można przesłać zadania Pig węzła.

Raz po rejestracji, Pig Latin, w tym przykładzie jest taki sam zarówno dla:

```pig
LOGS = LOAD 'wasbs:///example/data/sample.log' as (LINE:chararray);
LOG = FILTER LOGS by LINE is not null;
DETAILS = FOREACH LOG GENERATE myfuncs.create_structure(LINE);
DUMP DETAILS;
```

Poniżej przedstawiono, jak działa w tym przykładzie:

1. Pierwszy wiersz ładuje przykładowy plik danych `sample.log` do `LOGS`. Definiuje również każdego wybranego rekordu jako `chararray`.
2. Następny wiersz odfiltrowuje wszystkie wartości null, wynik operacji do przechowywania `LOG`.
3. Następnie go wykonuje iterację na rekordy w `LOG` i używa `GENERATE` do wywołania `create_structure` metoda zawarty w skrypcie języka Python/Jython załadowany jako `myfuncs`. `LINE` Służy do przekazania bieżącego rekordu do funkcji.
4. Na koniec dane wyjściowe są zrzucany stdout przy użyciu `DUMP` polecenia. To polecenie wyświetla wyników po zakończeniu operacji.

### <a name="create-file"></a>Utwórz plik

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



### <a name="upload-file-shell"></a>Przekaż plik (powłoka)

W poniższych poleceń Zastąp `sshuser` rzeczywiste nazwy użytkownika, jeśli jest inna.  Zastąp `mycluster` nazwą rzeczywistego klastra.  Upewnij się, że katalog roboczy jest, gdzie znajduje się plik.

1. Użyj `scp` można skopiować plików do klastra usługi HDInsight. Edytuj, a następnie wprowadź poniższe polecenie:

    ```cmd
    scp pigudf.py sshuser@mycluster-ssh.azurehdinsight.net:
    ```

2. Łączenie z klastrem za pomocą protokołu SSH.  Edytuj, a następnie wprowadź poniższe polecenie:

    ```cmd
    ssh sshuser@mycluster-ssh.azurehdinsight.net
    ```

3. W sesji SSH należy dodać pliki języka python, wcześniej przekazane do magazynu klastra.

    ```bash
    hdfs dfs -put pigudf.py /pigudf.py
    ```


### <a name="use-pig-udf-shell"></a>Użyj technologii Pig funkcji zdefiniowanej przez użytkownika (powłoka)

1. Aby połączyć z języka pig, użyj następującego polecenia z otwartych sesji protokołu SSH:

    ```bash
    pig
    ```

2. Wprowadź następujące instrukcje w `grunt>` wiersz:

   ```pig
   Register wasbs:///pigudf.py using jython as myfuncs;
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
   LOGS = LOAD 'wasbs:///example/data/sample.log' as (LINE:chararray);
   LOG = FILTER LOGS by LINE is not null;
   DETAILS = foreach LOG generate myfuncs.create_structure(LINE);
   DUMP DETAILS;
   ```

    Po wykonaniu tego zadania jako po wcześniej uruchomiono skrypt z użyciem Jython powinien być widoczny ten sam wynik.


### <a name="upload-file-powershell"></a>Przekaż plik (PowerShell)

> [!IMPORTANT]  
> Te skrypty programu PowerShell nie będą działać, jeśli [bezpieczny transfer](../../storage/common/storage-require-secure-transfer.md) jest włączona.  Użyj poleceń powłoki, albo wyłącz bezpieczny transfer.

Program PowerShell mogą również zdalnie uruchamiać zapytania Hive. Upewnij się, katalog roboczy gdzie `pigudf.py` znajduje się.  Poniższy skrypt programu PowerShell umożliwia uruchomienie zapytania programu Hive, który używa `pigudf.py` skryptu:

```PowerShell
# Login to your Azure subscription
# Is there an active Azure subscription?
$sub = Get-AzSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Connect-AzAccount
}

# Revise file path as needed
$pathToJythonFile = ".\pigudf.py"


# Get cluster info
$clusterName = Read-Host -Prompt "Enter the HDInsight cluster name"
$clusterInfo = Get-AzHDInsightCluster -ClusterName $clusterName
$resourceGroup = $clusterInfo.ResourceGroup
$storageAccountName=$clusterInfo.DefaultStorageAccount.split('.')[0]
$container=$clusterInfo.DefaultStorageContainer
$storageAccountKey=(Get-AzStorageAccountKey `
   -ResourceGroupName $resourceGroup `
   -Name $storageAccountName)[0].Value

# Create an Azure Storage context
$context = New-AzStorageContext `
    -StorageAccountName $storageAccountName `
    -StorageAccountKey $storageAccountKey

# Upload local files to an Azure Storage blob
Set-AzStorageBlobContent `
    -File $pathToJythonFile `
    -Blob "pigudf.py" `
    -Container $container `
    -Context $context
```

### <a name="use-pig-udf-powershell"></a>Korzystanie z języka Pig funkcji zdefiniowanej przez użytkownika (PowerShell)

> [!NOTE]  
> Podczas zdalnego przesyłania zadania przy użyciu programu PowerShell, nie jest możliwe użycie języka Python w języku C jako interpreter.

Program PowerShell można również uruchamiać zadania Pig Latin. Do uruchomienia zadania Pig Latin, który używa `pigudf.py` skryptu, użyj następującego skryptu programu PowerShell:

```PowerShell
# Script should stop on failures
$ErrorActionPreference = "Stop"

# Login to your Azure subscription
# Is there an active Azure subscription?
$sub = Get-AzSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Connect-AzAccount
}

# Get cluster info
$clusterName = Read-Host -Prompt "Enter the HDInsight cluster name"
$creds=Get-Credential -UserName "admin" -Message "Enter the login for the cluster"


$PigQuery = "Register wasbs:///pigudf.py using jython as myfuncs;" +
            "LOGS = LOAD 'wasbs:///example/data/sample.log' as (LINE:chararray);" +
            "LOG = FILTER LOGS by LINE is not null;" +
            "DETAILS = foreach LOG generate myfuncs.create_structure(LINE);" +
            "DUMP DETAILS;"

# Create Pig job object
$jobDefinition = New-AzHDInsightPigJobDefinition -Query $PigQuery

# For status bar updates
$activity="Pig job"

# Progress bar (optional)
Write-Progress -Activity $activity -Status "Starting job..."

# Start defined Azure HDInsight job on specified cluster.
$job = Start-AzHDInsightJob `
    -ClusterName $clusterName `
    -JobDefinition $jobDefinition `
    -HttpCredential $creds

# Progress bar (optional)
Write-Progress -Activity $activity -Status "Waiting for the Pig job to complete..."

# Wait for completion or failure of specified job
Wait-AzHDInsightJob `
    -Job $job.JobId `
    -ClusterName $clusterName `
    -HttpCredential $creds

# Uncomment the following to see stderr output
<#
Get-AzHDInsightJobOutput `
    -Clustername $clusterName `
    -JobId $job.JobId `
    -HttpCredential $creds `
    -DisplayOutputType StandardError
#>

# Progress bar (optional)
Write-Progress -Activity $activity "Retrieving output..."

# Gets the log output
Get-AzHDInsightJobOutput `
    -Clustername $clusterName `
    -JobId $job.JobId `
    -HttpCredential $creds
```

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
| Hive |/HivePython/stderr<p>/ HivePython/stdout |
| Pig |/PigPython/stderr<p>/PigPython/stdout |

## <a name="next"></a>Następne kroki

Jeśli musisz załadować modułów języka Python, które nie są domyślnie dostępne, zobacz [sposób wdrażania modułu w usłudze Azure HDInsight](https://blogs.msdn.com/b/benjguin/archive/2014/03/03/how-to-deploy-a-python-module-to-windows-azure-hdinsight.aspx).

Inne sposoby użycia Pig, Hive i aby dowiedzieć się więcej o korzystaniu z MapReduce, zobacz następujące dokumenty:

* [Use Apache Hive z HDInsight](hdinsight-use-hive.md)
* [Apache Pig za pomocą HDInsight](hdinsight-use-pig.md)
* [Używanie technologii MapReduce z HDInsight](hdinsight-use-mapreduce.md)
