---
title: Python UDF z ula Apache i Świnia Apache - Azure HDInsight
description: Dowiedz się, jak korzystać z funkcji zdefiniowanych przez użytkownika języka Python (UDF) z aplikacji Apache Hive i Apache Pig w programie HDInsight, stosie technologii Apache Hadoop na platformie Azure.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 11/15/2019
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.openlocfilehash: 201bb40e5024442587f5508886da7e844f35be40
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74148392"
---
# <a name="use-python-user-defined-functions-udf-with-apache-hive-and-apache-pig-in-hdinsight"></a>Korzystanie z funkcji zdefiniowanych przez użytkownika języka Python (UDF) z gałęzią Apache i świnią Apache w formacie HDInsight

Dowiedz się, jak korzystać z funkcji zdefiniowanych przez użytkownika języka Python (UDF) z apache hive i apache pig w Apache Hadoop na platformie Azure HDInsight.

## <a name="python-on-hdinsight"></a><a name="python"></a>Python na HDInsight

Python2.7 jest instalowany domyślnie w programie HDInsight 3.0 i nowszych. Apache Hive może być używany z tą wersją języka Python do przetwarzania strumienia. Przetwarzanie strumienia używa STDOUT i STDIN do przekazywania danych między gałęzią a UDF.

HDInsight zawiera również Jython, który jest implementacją Języka Python napisane w języku Java. Jython działa bezpośrednio na maszynie wirtualnej Java i nie używa przesyłania strumieniowego. Jython jest zalecanym interpreterem Pythona podczas używania Pythona z Pig.

## <a name="prerequisites"></a>Wymagania wstępne

* **Klaster Hadoop na HDInsight**. Zobacz [Wprowadzenie do HDInsight w systemie Linux](apache-hadoop-linux-tutorial-get-started.md).
* **Klient SSH**. Aby uzyskać więcej informacji, zobacz [Łączenie się z usługą HDInsight (Apache Hadoop) przy użyciu protokołu SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).
* [Schemat identyfikatorów URI](../hdinsight-hadoop-linux-information.md#URI-and-scheme) dla magazynu podstawowego klastrów. Byłoby to `wasb://` dla usługi `abfs://` Azure Storage, dla usługi Azure Data Lake Storage Gen2 lub adl:// dla usługi Azure Data Lake Storage Gen1. Jeśli bezpieczny transfer jest włączony dla usługi Azure Storage, identyfikator URI zostanie wasbs://.  Zobacz też [bezpieczny transfer](../../storage/common/storage-require-secure-transfer.md).
* **Możliwa zmiana konfiguracji magazynu.**  Zobacz [Konfiguracja magazynu,](#storage-configuration) `BlobStorage`jeśli używasz konta magazynu .
* Element opcjonalny.  Jeśli planujesz używać programu PowerShell, musisz zainstalować [moduł AZ.](https://docs.microsoft.com/powershell/azure/new-azureps-module-az)

> [!NOTE]  
> Konto magazynu używane w tym artykule było usługi `wasbs` Azure Storage z [włączonym bezpiecznym transferem](../../storage/common/storage-require-secure-transfer.md) i w związku z tym jest używane w całym artykule.

## <a name="storage-configuration"></a>Konfiguracja usługi Storage

Nie jest wymagana żadna akcja, jeśli `Storage (general purpose v1)` `StorageV2 (general purpose v2)`używane konto magazynu jest rodzaju lub .  Proces w tym artykule będzie `/tezstaging`produkować dane wyjściowe do co najmniej .  Domyślna konfiguracja hadoop `/tezstaging` będzie zawierać w zmiennej `fs.azure.page.blob.dir` konfiguracji w `core-site.xml` dla usługi `HDFS`.  Ta konfiguracja spowoduje, że dane wyjściowe do katalogu są obiektami blob strony, które nie są obsługiwane dla rodzaju `BlobStorage`konta magazynu .  Aby `BlobStorage` użyć tego artykułu, `/tezstaging` `fs.azure.page.blob.dir` usuń ze zmiennej konfiguracji.  Dostęp do konfiguracji można uzyskać z [interfejsu użytkownika Ambari](../hdinsight-hadoop-manage-ambari.md).  W przeciwnym razie zostanie wyświetlony komunikat o błędzie:`Page blob is not supported for this account type.`

> [!WARNING]  
> Kroki opisane w tym dokumencie stanowią następujące założenia:  
>
> * Skrypty języka Python można utworzyć w lokalnym środowisku programistycznym.
> * Przekazujesz skrypty do usługi HDInsight `scp` za pomocą polecenia lub dostarczonego skryptu programu PowerShell.
>
> Jeśli chcesz używać [usługi Azure Cloud Shell (bash)](https://docs.microsoft.com/azure/cloud-shell/overview) do pracy z hdinsight, następnie należy:
>
> * Tworzenie skryptów wewnątrz środowiska powłoki chmury.
> * Służy `scp` do przekazywania plików z powłoki chmury do usługi HDInsight.
> * Użyj `ssh` powłoki chmury, aby połączyć się z hdinsight i uruchomić przykłady.

## <a name="apache-hive-udf"></a><a name="hivepython"></a>Ul Apache UDF

Python może służyć jako UDF z hive za `TRANSFORM` pośrednictwem HiveQL instrukcji. Na przykład następujące HiveQL wywołuje `hiveudf.py` plik przechowywany w domyślnym koncie usługi Azure Storage dla klastra.

```hiveql
add file wasbs:///hiveudf.py;

SELECT TRANSFORM (clientid, devicemake, devicemodel)
    USING 'python hiveudf.py' AS
    (clientid string, phoneLabel string, phoneHash string)
FROM hivesampletable
ORDER BY clientid LIMIT 50;
```

Oto, co robi w tym przykładzie:

1. Instrukcja `add file` na początku pliku dodaje `hiveudf.py` plik do rozproszonej pamięci podręcznej, dzięki czemu jest dostępny dla wszystkich węzłów w klastrze.
2. Instrukcja `SELECT TRANSFORM ... USING` wybiera dane `hivesampletable`z pliku . Przekazuje również clientid, devicemake i devicemodel `hiveudf.py` wartości do skryptu.
3. Klauzula `AS` opisuje pola `hiveudf.py`zwrócone z .

<a name="streamingpy"></a>

### <a name="create-file"></a>Utwórz plik

W środowisku programistycznym utwórz plik tekstowy o nazwie `hiveudf.py`. Użyj następującego kodu jako zawartości pliku:

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

Ten skrypt wykonuje następujące akcje:

1. Odczytuje wiersz danych z STDIN.
2. Końcowy znak nowego bohatera `string.strip(line, "\n ")`jest usuwany za pomocą programu .
3. Podczas przetwarzania strumienia pojedynczy wiersz zawiera wszystkie wartości ze znakiem tabulacji między każdą wartością. Tak `string.split(line, "\t")` może być używany do dzielenia danych wejściowych na każdej karcie, zwracając tylko pola.
4. Po zakończeniu przetwarzania dane wyjściowe muszą być zapisywane w STDOUT jako pojedynczy wiersz, z kartą między każdym polem. Na przykład `print "\t".join([clientid, phone_label, hashlib.md5(phone_label).hexdigest()])`.
5. Pętla `while` powtarza się, dopóki nie `line` zostanie odczytany.

Dane wyjściowe skryptu jest łączenie wartości `devicemake` `devicemodel`wejściowych dla i , i skrót wartości łączonej.

### <a name="upload-file-shell"></a>Przekaż plik (powłoka)

W poniższych poleceniach `sshuser` zamień na rzeczywistą nazwę użytkownika, jeśli jest inna.  Zamień `mycluster` na rzeczywistą nazwę klastra.  Upewnij się, że katalog roboczy znajduje się w miejscu, w którym znajduje się plik.

1. Służy `scp` do kopiowania plików do klastra HDInsight. Edytuj i wprowadź poniższe polecenie:

    ```cmd
    scp hiveudf.py sshuser@mycluster-ssh.azurehdinsight.net:
    ```

2. Użyj SSH, aby połączyć się z klastrem.  Edytuj i wprowadź poniższe polecenie:

    ```cmd
    ssh sshuser@mycluster-ssh.azurehdinsight.net
    ```

3. Z sesji SSH dodaj pliki języka Python przekazane wcześniej do magazynu klastra.

    ```bash
    hdfs dfs -put hiveudf.py /hiveudf.py
    ```

### <a name="use-hive-udf-shell"></a>Użyj hive UDF (powłoki)

1. Aby połączyć się z gałęzią, użyj następującego polecenia z otwartej sesji SSH:

    ```bash
    beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http'
    ```

    To polecenie uruchamia klienta Beeline.

2. W wierszu polecenia `0: jdbc:hive2://headnodehost:10001/>` wprowadź następującą kwerendę:

   ```hive
   add file wasbs:///hiveudf.py;
   SELECT TRANSFORM (clientid, devicemake, devicemodel)
       USING 'python hiveudf.py' AS
       (clientid string, phoneLabel string, phoneHash string)
   FROM hivesampletable
   ORDER BY clientid LIMIT 50;
   ```

3. Po wprowadzeniu ostatniego wiersza zadanie powinno się rozpocząć. Po zakończeniu zadania zwraca dane wyjściowe podobne do następującego przykładu:

        100041    RIM 9650    d476f3687700442549a83fac4560c51c
        100041    RIM 9650    d476f3687700442549a83fac4560c51c
        100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9
        100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9
        100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9

4. Aby wyjść z beeline, wprowadź następujące polecenie:

    ```hive
    !q
    ```

### <a name="upload-file-powershell"></a>Przekaż plik (PowerShell)

Program PowerShell może również służyć do zdalnego uruchamiania zapytań hive. Upewnij się, że `hiveudf.py` katalog roboczy znajduje się w miejscu, w którym się znajduje.  Użyj następującego skryptu programu PowerShell, aby `hiveudf.py` uruchomić kwerendę hive, która używa skryptu:

```PowerShell
# Login to your Azure subscription
# Is there an active Azure subscription?
$sub = Get-AzSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Connect-AzAccount
}

# If you have multiple subscriptions, set the one to use
# Select-AzSubscription -SubscriptionId "<SUBSCRIPTIONID>"

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
> Aby uzyskać więcej informacji na temat przekazywania plików, zobacz [przekazywanie danych dla apache Hadoop zadania w dokumencie HDInsight.](../hdinsight-upload-data.md)

#### <a name="use-hive-udf"></a>Korzystanie z ul UDF

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

# If you have multiple subscriptions, set the one to use
# Select-AzSubscription -SubscriptionId "<SUBSCRIPTIONID>"

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

Dane wyjściowe dla zadania **hive** powinny być podobne do następującego przykładu:

    100041    RIM 9650    d476f3687700442549a83fac4560c51c
    100041    RIM 9650    d476f3687700442549a83fac4560c51c
    100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9
    100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9
    100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9

## <a name="apache-pig-udf"></a><a name="pigpython"></a>Świnia Apache UDF

Skrypt języka Python może służyć jako UDF `GENERATE` z Pig za pośrednictwem instrukcji. Skrypt można uruchomić przy użyciu Jython lub C Python.

* Jython działa na JVM, i może być natywnie wywoływane z Pig.
* C Python jest procesem zewnętrznym, więc dane z Pig na JVM są wysyłane do skryptu uruchomionego w procesie Pythona. Dane wyjściowe skryptu Pythona są wysyłane z powrotem do pig.

Aby określić interpreter `register` języka Python, należy użyć podczas odwoływania się do skryptu Języka Python. Poniższe przykłady rejestrują skrypty w pig jako: `myfuncs`

* **Aby użyć Jython:**`register '/path/to/pigudf.py' using jython as myfuncs;`
* **Aby użyć C Pythona:**`register '/path/to/pigudf.py' using streaming_python as myfuncs;`

> [!IMPORTANT]  
> Podczas korzystania z Jython, ścieżka do pliku pig_jython może być ścieżką lokalną lub ścieżką WASBS://. Jednak podczas korzystania z języka C Python, należy odwołać się do pliku w lokalnym systemie plików węzła, który jest używany do przesyłania pig pracy.

Po wcześniejszej rejestracji, Pig Latin dla tego przykładu jest taka sama dla obu:

```pig
LOGS = LOAD 'wasbs:///example/data/sample.log' as (LINE:chararray);
LOG = FILTER LOGS by LINE is not null;
DETAILS = FOREACH LOG GENERATE myfuncs.create_structure(LINE);
DUMP DETAILS;
```

Oto, co robi w tym przykładzie:

1. Pierwszy wiersz ładuje przykładowy `sample.log` plik `LOGS`danych do . Definiuje również każdy rekord `chararray`jako plik .
2. Następny wiersz odfiltruje wszelkie wartości null, przechowując wynik operacji w . `LOG`
3. Następnie iteruje rekordy `LOG` i używa `GENERATE` do wywołania `create_structure` metody zawartej w skrypcie Python/Jython załadowany jako `myfuncs`. `LINE`służy do przekazywania bieżącego rekordu do funkcji.
4. Na koniec dane wyjściowe są po cenach `DUMP` dumpingowych do STDOUT za pomocą polecenia. To polecenie wyświetla wyniki po zakończeniu operacji.

### <a name="create-file"></a>Utwórz plik

W środowisku programistycznym utwórz plik tekstowy o nazwie `pigudf.py`. Użyj następującego kodu jako zawartości pliku:

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

W przykładzie Pig `LINE` Latin dane wejściowe jest zdefiniowany jako chararray, ponieważ nie ma spójnego schematu dla danych wejściowych. Skrypt języka Python przekształca dane w spójny schemat dla danych wyjściowych.

1. Instrukcja `@outputSchema` definiuje format danych, które są zwracane do Pig. W tym przypadku jest to **worek danych**, który jest typem danych Świnia. Torba zawiera następujące pola, z których wszystkie są chararray (ciągi):

   * data - data utworzenia wpisu dziennika
   * czas — czas utworzenia wpisu dziennika
   * classname - nazwa klasy, dla
   * poziom - poziom dziennika
   * szczegóły — szczegółowe informacje dotyczące wpisu dziennika

2. Następnie `def create_structure(input)` definiuje funkcję, do którą pig przekazuje elementy zamówienia.

3. Przykładowe dane `sample.log`, w większości jest zgodny z datą, godziną, nazwa klasy, poziom i schemat szczegółów. Jednak zawiera kilka wierszy, `*java.lang.Exception*`które zaczynają się od . Te wiersze muszą być modyfikowane, aby dopasować schemat. Instrukcja `if` sprawdza dla tych, a następnie masuje dane wejściowe, aby przenieść `*java.lang.Exception*` ciąg do końca, dostosowując dane do oczekiwanego schematu danych wyjściowych.

4. Następnie `split` polecenie służy do dzielenia danych w pierwszych czterech znakach spacji. Dane wyjściowe są `date` `time`przypisane `level`do `detail`, , `classname`, i .

5. Na koniec wartości są zwracane do Pig.

Gdy dane są zwracane do Pig, ma spójny `@outputSchema` schemat, zgodnie z definicją w instrukcji.

### <a name="upload-file-shell"></a>Przekaż plik (powłoka)

W poniższych poleceniach `sshuser` zamień na rzeczywistą nazwę użytkownika, jeśli jest inna.  Zamień `mycluster` na rzeczywistą nazwę klastra.  Upewnij się, że katalog roboczy znajduje się w miejscu, w którym znajduje się plik.

1. Służy `scp` do kopiowania plików do klastra HDInsight. Edytuj i wprowadź poniższe polecenie:

    ```cmd
    scp pigudf.py sshuser@mycluster-ssh.azurehdinsight.net:
    ```

2. Użyj SSH, aby połączyć się z klastrem.  Edytuj i wprowadź poniższe polecenie:

    ```cmd
    ssh sshuser@mycluster-ssh.azurehdinsight.net
    ```

3. Z sesji SSH dodaj pliki języka Python przekazane wcześniej do magazynu klastra.

    ```bash
    hdfs dfs -put pigudf.py /pigudf.py
    ```

### <a name="use-pig-udf-shell"></a>Użyj Pig UDF (powłoka)

1. Aby połączyć się z świnią, użyj następującego polecenia z otwartej sesji SSH:

    ```bash
    pig
    ```

2. Wprowadź następujące instrukcje `grunt>` w wierszu polecenia:

   ```pig
   Register wasbs:///pigudf.py using jython as myfuncs;
   LOGS = LOAD 'wasbs:///example/data/sample.log' as (LINE:chararray);
   LOG = FILTER LOGS by LINE is not null;
   DETAILS = foreach LOG generate myfuncs.create_structure(LINE);
   DUMP DETAILS;
   ```

3. Po wprowadzeniu następującego wiersza zadanie powinno się rozpocząć. Po zakończeniu zadania zwraca dane wyjściowe podobne do następujących danych:

        ((2012-02-03,20:11:56,SampleClass5,[TRACE],verbose detail for id 990982084))
        ((2012-02-03,20:11:56,SampleClass7,[TRACE],verbose detail for id 1560323914))
        ((2012-02-03,20:11:56,SampleClass8,[DEBUG],detail for id 2083681507))
        ((2012-02-03,20:11:56,SampleClass3,[TRACE],verbose detail for id 1718828806))
        ((2012-02-03,20:11:56,SampleClass3,[INFO],everything normal for id 530537821))

4. Użyj, `quit` aby wyjść z powłoki Grunt, a następnie użyj następujących czynności, aby edytować plik pigudf.py w lokalnym systemie plików:

    ```bash
    nano pigudf.py
    ```

5. Raz w edytorze, odkomentuj następujący `#` wiersz, usuwając znak z początku wiersza:

    ```bash
    #from pig_util import outputSchema
    ```

    Ten wiersz modyfikuje skrypt języka Python do pracy z C Python zamiast Jython. Po wprowadzeniu zmiany użyj **klawiszy Ctrl+X,** aby zamknąć edytor. Wybierz **pozycję Y**, a następnie **pozycję Enter,** aby zapisać zmiany.

6. Użyj `pig` polecenia, aby ponownie uruchomić powłokę. Gdy jesteś w `grunt>` wierszu polecenia, użyj następujących czynności, aby uruchomić skrypt języka Python przy użyciu interpretera Języka C Python.

   ```pig
   Register 'pigudf.py' using streaming_python as myfuncs;
   LOGS = LOAD 'wasbs:///example/data/sample.log' as (LINE:chararray);
   LOG = FILTER LOGS by LINE is not null;
   DETAILS = foreach LOG generate myfuncs.create_structure(LINE);
   DUMP DETAILS;
   ```

    Po zakończeniu tego zadania powinien zostać wyświetlony ten sam wynik wyjściowy, co podczas wcześniejszego uruchomionego skryptu przy użyciu Jython.

### <a name="upload-file-powershell"></a>Przekaż plik (PowerShell)

Program PowerShell może również służyć do zdalnego uruchamiania zapytań hive. Upewnij się, że `pigudf.py` katalog roboczy znajduje się w miejscu, w którym się znajduje.  Użyj następującego skryptu programu PowerShell, aby `pigudf.py` uruchomić kwerendę hive, która używa skryptu:

```PowerShell
# Login to your Azure subscription
# Is there an active Azure subscription?
$sub = Get-AzSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Connect-AzAccount
}

# If you have multiple subscriptions, set the one to use
# Select-AzSubscription -SubscriptionId "<SUBSCRIPTIONID>"

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

### <a name="use-pig-udf-powershell"></a>Użyj Pig UDF (PowerShell)

> [!NOTE]  
> Podczas zdalnego przesyłania zadania przy użyciu programu PowerShell nie jest możliwe użycie języka C Python jako interpretera.

Program PowerShell może również służyć do uruchamiania zadań Pig Latin. Aby uruchomić zadanie Pig Latin, `pigudf.py` które używa skryptu, użyj następującego skryptu programu PowerShell:

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

Dane wyjściowe dla zadania **Świnia** powinny być podobne do następujących danych:

    ((2012-02-03,20:11:56,SampleClass5,[TRACE],verbose detail for id 990982084))
    ((2012-02-03,20:11:56,SampleClass7,[TRACE],verbose detail for id 1560323914))
    ((2012-02-03,20:11:56,SampleClass8,[DEBUG],detail for id 2083681507))
    ((2012-02-03,20:11:56,SampleClass3,[TRACE],verbose detail for id 1718828806))
    ((2012-02-03,20:11:56,SampleClass3,[INFO],everything normal for id 530537821))

## <a name="troubleshooting"></a><a name="troubleshooting"></a>Rozwiązywanie problemów

### <a name="errors-when-running-jobs"></a>Błędy podczas uruchamiania zadań

Podczas uruchamiania zadania gałęzi może wystąpić błąd podobny do następującego tekstu:

    Caused by: org.apache.hadoop.hive.ql.metadata.HiveException: [Error 20001]: An error occurred while reading or writing to your custom script. It may have crashed with an error.

Ten problem może być spowodowany przez zakończenia wierszy w pliku Python. Wiele edytorów systemu Windows domyślnie używa CRLF jako zakończenia linii, ale aplikacje Linux zwykle oczekują LF.

Następujące instrukcje programu PowerShell można użyć do usunięcia znaków CR przed przekazaniem pliku do programu HDInsight:

[!code-powershell[main](../../../powershell_scripts/hdinsight/run-python-udf/run-python-udf.ps1?range=148-150)]

### <a name="powershell-scripts"></a>Skrypty środowiska PowerShell

Oba przykładowe skrypty programu PowerShell używane do uruchamiania przykładów zawierają skomentowany wiersz, który wyświetla dane wyjściowe błędu dla zadania. Jeśli nie widzisz oczekiwanych danych wyjściowych dla zadania, odkomentuj następujący wiersz i sprawdź, czy informacje o błędzie wskazują na problem.

[!code-powershell[main](../../../powershell_scripts/hdinsight/run-python-udf/run-python-udf.ps1?range=135-139)]

Informacje o błędzie (STDERR) i wynik zadania (STDOUT) są również rejestrowane w pamięci hdinsight.

| Do tej pracy... | Spójrz na te pliki w kontenerze obiektów blob |
| --- | --- |
| Hive |/HivePython/stderr<p>/HivePython/stdout |
| Pig |/PigPython/stderr<p>/PigPython/stdout |

## <a name="next-steps"></a><a name="next"></a>Następne kroki

Jeśli chcesz załadować moduły języka Python, które nie są dostarczane domyślnie, zobacz [Jak wdrożyć moduł w usłudze Azure HDInsight](https://blogs.msdn.com/b/benjguin/archive/2014/03/03/how-to-deploy-a-python-module-to-windows-azure-hdinsight.aspx).

Aby uzyskać inne sposoby używania pig, hive i dowiedzieć się więcej o użyciu MapReduce, zobacz następujące dokumenty:

* [Korzystanie z programu Apache Hive z usługą HDInsight](hdinsight-use-hive.md)
* [Korzystanie z mapReduce z HDInsight](hdinsight-use-mapreduce.md)
