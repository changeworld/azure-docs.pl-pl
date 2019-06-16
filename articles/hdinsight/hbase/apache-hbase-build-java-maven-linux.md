---
title: Tworzenie klienta bazy danych HBase w języku Java dla usługi Azure HDInsight przy użyciu narzędzia Apache Maven
description: Informacje o sposobie tworzenia aplikacji opartych na języku Java bazy danych Apache HBase, a następnie wdrożyć ją do bazy danych HBase w usłudze Azure HDInsight przy użyciu narzędzia Apache Maven.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,seodec18
ms.topic: conceptual
ms.date: 04/16/2019
ms.openlocfilehash: a4c601e81390efa3bb53a6f07225bb6e939bc9bb
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64726446"
---
# <a name="build-java-applications-for-apache-hbase"></a>Tworzenie aplikacji bazy danych Apache HBase w języku Java

Dowiedz się, jak utworzyć [bazy danych Apache HBase](https://hbase.apache.org/) aplikacji w języku Java. Następnie można używać aplikacji z bazą danych HBase w usłudze Azure HDInsight.

Kroki opisane w tej dokumentów użyj [narzędzia Apache Maven](https://maven.apache.org/) utworzyć i skompilować projekt. Maven to zarządzanie projektami oprogramowania oraz narzędzie zrozumienie, które umożliwia tworzenie oprogramowania, dokumentację i raporty dla projektów języka Java.

## <a name="prerequisites"></a>Wymagania wstępne

* Klaster bazy danych Apache HBase HDInsight. Zobacz [Rozpoczynanie pracy z usługą Apache HBase](./apache-hbase-tutorial-get-started-linux.md).

* [Java Developer Kit (JDK) w wersji 8](https://aka.ms/azure-jdks).

* [Narzędzia Apache Maven](https://maven.apache.org/download.cgi) prawidłowo [zainstalowane](https://maven.apache.org/install.html) zgodnie z Apache.  Maven to projekt system kompilacji dla projektów Java.

* Klient SSH. Aby uzyskać więcej informacji, zobacz [Łączenie się z usługą HDInsight (Apache Hadoop) przy użyciu protokołu SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

* Jeśli przy użyciu programu PowerShell, konieczne będzie [modułu AZ](https://docs.microsoft.com/powershell/azure/overview).

* Edytor tekstów. Microsoft Notepad korzysta z tego artykułu.

> [!IMPORTANT]  
> Polecenia cmdlet programu Azure PowerShell [Get AzHDInsightCluster](https://docs.microsoft.com/powershell/module/az.hdinsight/get-azhdinsightcluster) i [Get AzHDInsightJobOutput](https://docs.microsoft.com/powershell/module/az.hdinsight/get-azhdinsightjoboutput) aktualnie nie działają po [bezpieczny transfer](../../storage/common/storage-require-secure-transfer.md) jest włączona na koncie magazynu .

## <a name="test-environment"></a>Środowisko testowe
Środowiska używanego na potrzeby tego artykułu jest komputer z systemem Windows 10.  Polecenia zostały wykonane w wierszu polecenia, a różne pliki zostały zmienione w Notatniku. Zmodyfikuj odpowiednio dla danego środowiska.

W wierszu polecenia wpisz poniższe polecenia, aby utworzyć środowiska pracy:

```cmd
IF NOT EXIST C:\HDI MKDIR C:\HDI
cd C:\HDI
```

## <a name="create-a-maven-project"></a>Utwórz projekt narzędzia Maven

1. Wprowadź następujące polecenie, aby utworzyć projekt narzędzia Maven o nazwie **hbaseapp**:

    ```cmd
    mvn archetype:generate -DgroupId=com.microsoft.examples -DartifactId=hbaseapp -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false

    cd hbaseapp
    mkdir conf
    ```

    To polecenie tworzy katalog o nazwie `hbaseapp` w bieżącej lokalizacji, która zawiera podstawowe Projekt narzędzia Maven. Drugie polecenie zmienia katalog roboczy `hbaseapp`. Trzecie polecenie tworzy nowy katalog `conf`, które będą używane później. `hbaseapp` Katalog zawiera następujące elementy:

    * `pom.xml`:  Model obiektu projektu ([POM](https://maven.apache.org/guides/introduction/introduction-to-the-pom.html)) zawiera informacje i szczegóły konfiguracji używane do tworzenia projektu.
    * `src\main\java\com\microsoft\examples`: Zawiera kod aplikacji.
    * `src\test\java\com\microsoft\examples`: Zawiera testy dla aplikacji.

2. Usuń wygenerowane przykładowy kod. Usuwanie wygenerowanego testu i pliki aplikacji `AppTest.java`, i `App.java` , wprowadzając poniższe polecenia:

    ```cmd
    DEL src\main\java\com\microsoft\examples\App.java
    DEL src\test\java\com\microsoft\examples\AppTest.java
    ```

## <a name="update-the-project-object-model"></a>Aktualizowanie modelu obiektu projektu

Aby uzyskać pełną dokumentację plik pom.xml, zobacz https://maven.apache.org/pom.html.  Otwórz `pom.xml` , wprowadzając poniższe polecenie:

```cmd
notepad pom.xml
```

### <a name="add-dependencies"></a>Dodaj zależności

W `pom.xml`, Dodaj następujący tekst w `<dependencies>` sekcji:

```xml
<dependency>
    <groupId>org.apache.hbase</groupId>
    <artifactId>hbase-client</artifactId>
    <version>1.1.2</version>
</dependency>
<dependency>
    <groupId>org.apache.phoenix</groupId>
    <artifactId>phoenix-core</artifactId>
    <version>4.14.1-HBase-1.1</version>
</dependency>
```  

W tej sekcji oznacza, że projekt musi **klienta bazy danych hbase** i **phoenix core** składników. W czasie kompilacji te zależności są pobierane z repozytorium Maven domyślne. Możesz użyć [centralnej wyszukiwania repozytorium narzędzia Maven](https://search.maven.org/artifact/org.apache.hbase/hbase-client/1.1.2/jar) Aby dowiedzieć się więcej na temat tej zależności.

> [!IMPORTANT]  
> Numer wersji klienta bazy danych hbase, musi być zgodny z wersją Apache HBase, który znajduje się z klastrem usługi HDInsight. Skorzystaj z poniższej tabeli, aby znaleźć numer poprawnej wersji.

| Wersja klastra HDInsight | Wersja bazy danych Apache HBase do użycia |
| --- | --- |
| 3.6 | 1.1.2 |
| 4.0 | 2.0.0 |

Aby uzyskać więcej informacji na temat wersji HDInsight i składników, zobacz [różnych składnikach platformy Apache Hadoop, udostępniono HDInsight](../hdinsight-component-versioning.md).

### <a name="build-configuration"></a>Konfiguracja kompilacji

Wtyczki maven umożliwiają dostosowanie etapy kompilacji generują projektu. Ta sekcja umożliwia dodawanie dodatków plug-in, zasoby i inne opcje konfiguracji kompilacji.

Dodaj następujący kod do `pom.xml` pliku, a następnie zapisz i zamknij plik. Ten tekst musi znajdować się wewnątrz `<project>...</project>` tagów w pliku, na przykład między `</dependencies>` i `</project>`.

```xml
<build>
    <sourceDirectory>src</sourceDirectory>
    <resources>
    <resource>
        <directory>${basedir}/conf</directory>
        <filtering>false</filtering>
        <includes>
        <include>hbase-site.xml</include>
        </includes>
    </resource>
    </resources>
    <plugins>
    <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-compiler-plugin</artifactId>
                <version>3.8.0</version>
        <configuration>
            <source>1.8</source>
            <target>1.8</target>
        </configuration>
        </plugin>
    <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-shade-plugin</artifactId>
        <version>3.2.1</version>
        <configuration>
        <transformers>
            <transformer implementation="org.apache.maven.plugins.shade.resource.ApacheLicenseResourceTransformer">
            </transformer>
        </transformers>
        </configuration>
        <executions>
        <execution>
            <phase>package</phase>
            <goals>
            <goal>shade</goal>
            </goals>
        </execution>
        </executions>
    </plugin>
    </plugins>
</build>
```

W tej sekcji konfiguruje zasobu (`conf/hbase-site.xml`) zawierający informacje o konfiguracji dla bazy danych HBase.

> [!NOTE]  
> Można również ustawić wartości konfiguracji za pomocą kodu. Zobacz komentarze w `CreateTable` przykład.

Konfiguruje również w tej sekcji [wtyczki programu Apache Maven kompilatora](https://maven.apache.org/plugins/maven-compiler-plugin/) i [wtyczki programu Apache Maven odcień](https://maven.apache.org/plugins/maven-shade-plugin/). Wtyczka kompilatora jest używana do kompilowania topologii. Wtyczka odcień używany w celu zapobiegania duplikatów licencji w pakiet JAR, który jest kompilowany przez narzędzia Maven. Ta wtyczka używany w celu zapobiegania błąd "zduplikowane licencji files" w czasie wykonywania w klastrze HDInsight. Przy użyciu narzędzia maven odcień wtyczki za pomocą `ApacheLicenseResourceTransformer` implementacji zapobiega błędu.

Wtyczki odcień maven tworzy również jar uber, który zawiera wszystkie zależności, które są wymagane przez aplikację.

### <a name="download-the-hbase-sitexml"></a>Pobierz site.xml bazy danych hbase

Użyj następującego polecenia, aby skopiować konfigurację bazy danych HBase z klastra HBase na `conf` katalogu. Zastąp `CLUSTERNAME` za pomocą usługi HDInsight nazwa klastra, a następnie wpisz polecenie:

```cmd
scp sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/etc/hbase/conf/hbase-site.xml ./conf/hbase-site.xml
```

## <a name="create-the-application"></a>Tworzenie aplikacji

### <a name="implement-a-createtable-class"></a>Implementowanie klasy CreateTable

Wprowadź polecenie, aby utworzyć i otworzyć nowy plik `CreateTable.java`. Wybierz **tak** w wierszu, aby utworzyć nowy plik.

```cmd
notepad src\main\java\com\microsoft\examples\CreateTable.java
```

Następnie skopiuj i wklej poniższy kod języka java do nowego pliku. Następnie zamknij plik.

```java
package com.microsoft.examples;
import java.io.IOException;

import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.hbase.HBaseConfiguration;
import org.apache.hadoop.hbase.client.HBaseAdmin;
import org.apache.hadoop.hbase.HTableDescriptor;
import org.apache.hadoop.hbase.TableName;
import org.apache.hadoop.hbase.HColumnDescriptor;
import org.apache.hadoop.hbase.client.HTable;
import org.apache.hadoop.hbase.client.Put;
import org.apache.hadoop.hbase.util.Bytes;

public class CreateTable {
    public static void main(String[] args) throws IOException {
    Configuration config = HBaseConfiguration.create();

    // Example of setting zookeeper values for HDInsight
    // in code instead of an hbase-site.xml file
    //
    // config.set("hbase.zookeeper.quorum",
    //            "zookeepernode0,zookeepernode1,zookeepernode2");
    //config.set("hbase.zookeeper.property.clientPort", "2181");
    //config.set("hbase.cluster.distributed", "true");
    //
    //NOTE: Actual zookeeper host names can be found using Ambari:
    //curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/hosts"

    //Linux-based HDInsight clusters use /hbase-unsecure as the znode parent
    config.set("zookeeper.znode.parent","/hbase-unsecure");

    // create an admin object using the config
    HBaseAdmin admin = new HBaseAdmin(config);

    // create the table...
    HTableDescriptor tableDescriptor = new HTableDescriptor(TableName.valueOf("people"));
    // ... with two column families
    tableDescriptor.addFamily(new HColumnDescriptor("name"));
    tableDescriptor.addFamily(new HColumnDescriptor("contactinfo"));
    admin.createTable(tableDescriptor);

    // define some people
    String[][] people = {
        { "1", "Marcel", "Haddad", "marcel@fabrikam.com"},
        { "2", "Franklin", "Holtz", "franklin@contoso.com" },
        { "3", "Dwayne", "McKee", "dwayne@fabrikam.com" },
        { "4", "Rae", "Schroeder", "rae@contoso.com" },
        { "5", "Rosalie", "burton", "rosalie@fabrikam.com"},
        { "6", "Gabriela", "Ingram", "gabriela@contoso.com"} };

    HTable table = new HTable(config, "people");

    // Add each person to the table
    //   Use the `name` column family for the name
    //   Use the `contactinfo` column family for the email
    for (int i = 0; i< people.length; i++) {
        Put person = new Put(Bytes.toBytes(people[i][0]));
        person.add(Bytes.toBytes("name"), Bytes.toBytes("first"), Bytes.toBytes(people[i][1]));
        person.add(Bytes.toBytes("name"), Bytes.toBytes("last"), Bytes.toBytes(people[i][2]));
        person.add(Bytes.toBytes("contactinfo"), Bytes.toBytes("email"), Bytes.toBytes(people[i][3]));
        table.put(person);
    }
    // flush commits and close the table
    table.flushCommits();
    table.close();
    }
}
```

Ten kod jest `CreateTable` klasy, która tworzy tabelę o nazwie `people` i wypełnić ją za pomocą wstępnie zdefiniowanych użytkowników.

### <a name="implement-a-searchbyemail-class"></a>Implementowanie klasy SearchByEmail

Wprowadź polecenie, aby utworzyć i otworzyć nowy plik `SearchByEmail.java`. Wybierz **tak** w wierszu, aby utworzyć nowy plik.

```cmd
notepad src\main\java\com\microsoft\examples\SearchByEmail.java
```

Następnie skopiuj i wklej poniższy kod języka java do nowego pliku. Następnie zamknij plik.

```java
package com.microsoft.examples;
import java.io.IOException;

import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.hbase.HBaseConfiguration;
import org.apache.hadoop.hbase.client.HTable;
import org.apache.hadoop.hbase.client.Scan;
import org.apache.hadoop.hbase.client.ResultScanner;
import org.apache.hadoop.hbase.client.Result;
import org.apache.hadoop.hbase.filter.RegexStringComparator;
import org.apache.hadoop.hbase.filter.SingleColumnValueFilter;
import org.apache.hadoop.hbase.filter.CompareFilter.CompareOp;
import org.apache.hadoop.hbase.util.Bytes;
import org.apache.hadoop.util.GenericOptionsParser;

public class SearchByEmail {
    public static void main(String[] args) throws IOException {
    Configuration config = HBaseConfiguration.create();

    // Use GenericOptionsParser to get only the parameters to the class
    // and not all the parameters passed (when using WebHCat for example)
    String[] otherArgs = new GenericOptionsParser(config, args).getRemainingArgs();
    if (otherArgs.length != 1) {
        System.out.println("usage: [regular expression]");
        System.exit(-1);
    }

    // Open the table
    HTable table = new HTable(config, "people");

    // Define the family and qualifiers to be used
    byte[] contactFamily = Bytes.toBytes("contactinfo");
    byte[] emailQualifier = Bytes.toBytes("email");
    byte[] nameFamily = Bytes.toBytes("name");
    byte[] firstNameQualifier = Bytes.toBytes("first");
    byte[] lastNameQualifier = Bytes.toBytes("last");

    // Create a regex filter
    RegexStringComparator emailFilter = new RegexStringComparator(otherArgs[0]);
    // Attach the regex filter to a filter
    //   for the email column
    SingleColumnValueFilter filter = new SingleColumnValueFilter(
        contactFamily,
        emailQualifier,
        CompareOp.EQUAL,
        emailFilter
    );

    // Create a scan and set the filter
    Scan scan = new Scan();
    scan.setFilter(filter);

    // Get the results
    ResultScanner results = table.getScanner(scan);
    // Iterate over results and print  values
    for (Result result : results ) {
        String id = new String(result.getRow());
        byte[] firstNameObj = result.getValue(nameFamily, firstNameQualifier);
        String firstName = new String(firstNameObj);
        byte[] lastNameObj = result.getValue(nameFamily, lastNameQualifier);
        String lastName = new String(lastNameObj);
        System.out.println(firstName + " " + lastName + " - ID: " + id);
        byte[] emailObj = result.getValue(contactFamily, emailQualifier);
        String email = new String(emailObj);
        System.out.println(firstName + " " + lastName + " - " + email + " - ID: " + id);
    }
    results.close();
    table.close();
    }
}
```

`SearchByEmail` Klasy może służyć do kwerendy dla wierszy przy użyciu adresu e-mail. Ponieważ używa ona filtr z wyrażeniem regularnym, możesz podać ciąg lub wyrażenie regularne, korzystając z tej klasy.

### <a name="implement-a-deletetable-class"></a>Implementowanie klasy DeleteTable

Wprowadź polecenie, aby utworzyć i otworzyć nowy plik `DeleteTable.java`. Wybierz **tak** w wierszu, aby utworzyć nowy plik.

```cmd
notepad src\main\java\com\microsoft\examples\DeleteTable.java
```

Następnie skopiuj i wklej poniższy kod języka java do nowego pliku. Następnie zamknij plik.

```java
package com.microsoft.examples;
import java.io.IOException;

import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.hbase.HBaseConfiguration;
import org.apache.hadoop.hbase.client.HBaseAdmin;

public class DeleteTable {
    public static void main(String[] args) throws IOException {
    Configuration config = HBaseConfiguration.create();

    // Create an admin object using the config
    HBaseAdmin admin = new HBaseAdmin(config);

    // Disable, and then delete the table
    admin.disableTable("people");
    admin.deleteTable("people");
    }
}
```

`DeleteTable` Klasy czyści tabele bazy danych HBase utworzone w tym przykładzie wyłączenie i upuszczając tabelę utworzoną przez `CreateTable` klasy.

## <a name="build-and-package-the-application"></a>Tworzenie i utworzyć pakiet aplikacji

1. Z `hbaseapp` katalogu, użyj następującego polecenia do tworzenia pliku JAR, który zawiera aplikację:

    ```cmd
    mvn clean package
    ```

    To polecenie tworzy i pakiety aplikacji do pliku JAR.

2. Po zakończeniu wykonywania polecenia `hbaseapp/target` katalog zawiera plik o nazwie `hbaseapp-1.0-SNAPSHOT.jar`.

   > [!NOTE]  
   > `hbaseapp-1.0-SNAPSHOT.jar` Plik znajduje się plik jar uber. Zawiera ona wszystkie zależności, które są wymagane do uruchomienia aplikacji.

## <a name="upload-the-jar-and-run-jobs-ssh"></a>Przekaż plik JAR i uruchamianie zadań (SSH)

Następujące kroki użycia `scp` można skopiować plik JAR z podstawowym węzłem usługi Apache HBase w klastrze HDInsight. `ssh` Polecenia jest następnie używany do łączenia z klastrem i uruchomić przykład bezpośrednio w węźle głównym.

1. Przekaż plik jar w klastrze. Zastąp `CLUSTERNAME` za pomocą usługi HDInsight nazwa klastra, a następnie wprowadź następujące polecenie:

    ```cmd
    scp ./target/hbaseapp-1.0-SNAPSHOT.jar sshuser@CLUSTERNAME-ssh.azurehdinsight.net:hbaseapp-1.0-SNAPSHOT.jar
    ```

2. Łączenie z klastrem HBase. Zastąp `CLUSTERNAME` za pomocą usługi HDInsight nazwa klastra, a następnie wprowadź następujące polecenie:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

 3. Do tworzenia tabel HBase przy użyciu aplikacji języka Java, użyj następującego polecenia z otwartym ssh połączenia:

    ```bash
    yarn jar hbaseapp-1.0-SNAPSHOT.jar com.microsoft.examples.CreateTable
    ```

    To polecenie umożliwia utworzenie tabeli HBase, o nazwie **osób**i wypełnia ją z danymi.

4. Aby znaleźć adresy e-mail, przechowywane w tabeli, użyj następującego polecenia:

    ```bash
    yarn jar hbaseapp-1.0-SNAPSHOT.jar com.microsoft.examples.SearchByEmail contoso.com
    ```

    Pojawi się następujące wyniki:

        Franklin Holtz - ID: 2
        Franklin Holtz - franklin@contoso.com - ID: 2
        Rae Schroeder - ID: 4
        Rae Schroeder - rae@contoso.com - ID: 4
        Gabriela Ingram - ID: 6
        Gabriela Ingram - gabriela@contoso.com - ID: 6

5. Aby usunąć tabeli, użyj następującego polecenia:

    ```bash
    yarn jar hbaseapp-1.0-SNAPSHOT.jar com.microsoft.examples.DeleteTable
    ```

## <a name="upload-the-jar-and-run-jobs-powershell"></a>Przekaż plik JAR i uruchamianie zadań (PowerShell)

Poniższe kroki przy użyciu programu Azure PowerShell [modułu AZ](https://docs.microsoft.com/powershell/azure/new-azureps-module-az) można przekazać plik JAR do domyślnego magazynu klastra Apache HBase. Polecenia cmdlet HDInsight są następnie używane do zdalnego uruchamiania przykładów.

1. Po zainstalowaniu i skonfigurowaniu moduł AZ utworzyć plik o nazwie `hbase-runner.psm1`. Użyj następującego tekstu jako zawartości tego pliku:

   ```powershell
    <#
    .SYNOPSIS
    Copies a file to the primary storage of an HDInsight cluster.
    .DESCRIPTION
    Copies a file from a local directory to the blob container for
    the HDInsight cluster.
    .EXAMPLE
    Start-HBaseExample -className "com.microsoft.examples.CreateTable"
    -clusterName "MyHDInsightCluster"

    .EXAMPLE
    Start-HBaseExample -className "com.microsoft.examples.SearchByEmail"
    -clusterName "MyHDInsightCluster"
    -emailRegex "contoso.com"

    .EXAMPLE
    Start-HBaseExample -className "com.microsoft.examples.SearchByEmail"
    -clusterName "MyHDInsightCluster"
    -emailRegex "^r" -showErr
    #>

    function Start-HBaseExample {
    [CmdletBinding(SupportsShouldProcess = $true)]
    param(
    #The class to run
    [Parameter(Mandatory = $true)]
    [String]$className,

    #The name of the HDInsight cluster
    [Parameter(Mandatory = $true)]
    [String]$clusterName,

    #Only used when using SearchByEmail
    [Parameter(Mandatory = $false)]
    [String]$emailRegex,

    #Use if you want to see stderr output
    [Parameter(Mandatory = $false)]
    [Switch]$showErr
    )

    Set-StrictMode -Version 3

    # Is the Azure module installed?
    FindAzure

    # Get the login for the HDInsight cluster
    $creds=Get-Credential -Message "Enter the login for the cluster" -UserName "admin"

    # The JAR
    $jarFile = "wasb:///example/jars/hbaseapp-1.0-SNAPSHOT.jar"

    # The job definition
    $jobDefinition = New-AzHDInsightMapReduceJobDefinition `
        -JarFile $jarFile `
        -ClassName $className `
        -Arguments $emailRegex

    # Get the job output
    $job = Start-AzHDInsightJob `
        -ClusterName $clusterName `
        -JobDefinition $jobDefinition `
        -HttpCredential $creds
    Write-Host "Wait for the job to complete ..." -ForegroundColor Green
    Wait-AzHDInsightJob `
        -ClusterName $clusterName `
        -JobId $job.JobId `
        -HttpCredential $creds
    if($showErr)
    {
    Write-Host "STDERR"
    Get-AzHDInsightJobOutput `
                -Clustername $clusterName `
                -JobId $job.JobId `
                -HttpCredential $creds `
                -DisplayOutputType StandardError
    }
    Write-Host "Display the standard output ..." -ForegroundColor Green
    Get-AzHDInsightJobOutput `
                -Clustername $clusterName `
                -JobId $job.JobId `
                -HttpCredential $creds
    }

    <#
    .SYNOPSIS
    Copies a file to the primary storage of an HDInsight cluster.
    .DESCRIPTION
    Copies a file from a local directory to the blob container for
    the HDInsight cluster.
    .EXAMPLE
    Add-HDInsightFile -localPath "C:\temp\data.txt"
    -destinationPath "example/data/data.txt"
    -ClusterName "MyHDInsightCluster"
    .EXAMPLE
    Add-HDInsightFile -localPath "C:\temp\data.txt"
    -destinationPath "example/data/data.txt"
    -ClusterName "MyHDInsightCluster"
    -Container "MyContainer"
    #>

    function Add-HDInsightFile {
        [CmdletBinding(SupportsShouldProcess = $true)]
        param(
            #The path to the local file.
            [Parameter(Mandatory = $true)]
            [String]$localPath,

            #The destination path and file name, relative to the root of the container.
            [Parameter(Mandatory = $true)]
            [String]$destinationPath,

            #The name of the HDInsight cluster
            [Parameter(Mandatory = $true)]
            [String]$clusterName,

            #If specified, overwrites existing files without prompting
            [Parameter(Mandatory = $false)]
            [Switch]$force
        )

        Set-StrictMode -Version 3

        # Is the Azure module installed?
        FindAzure

        # Get authentication for the cluster
        $creds=Get-Credential

        # Does the local path exist?
        if (-not (Test-Path $localPath))
        {
            throw "Source path '$localPath' does not exist."
        }

        # Get the primary storage container
        $storage = GetStorage -clusterName $clusterName

        # Upload file to storage, overwriting existing files if -force was used.
        Set-AzStorageBlobContent -File $localPath `
            -Blob $destinationPath `
            -force:$force `
            -Container $storage.container `
            -Context $storage.context
    }

    function FindAzure {
        # Is there an active Azure subscription?
        $sub = Get-AzSubscription -ErrorAction SilentlyContinue
        if(-not($sub))
        {
            Connect-AzAccount
        }
    }

    function GetStorage {
        param(
            [Parameter(Mandatory = $true)]
            [String]$clusterName
        )
        $hdi = Get-AzHDInsightCluster -ClusterName $clusterName
        # Does the cluster exist?
        if (!$hdi)
        {
            throw "HDInsight cluster '$clusterName' does not exist."
        }
        # Create a return object for context & container
        $return = @{}
        $storageAccounts = @{}

        # Get storage information
        $resourceGroup = $hdi.ResourceGroup
        $storageAccountName=$hdi.DefaultStorageAccount.split('.')[0]
        $container=$hdi.DefaultStorageContainer
        $storageAccountKey=(Get-AzStorageAccountKey `
            -Name $storageAccountName `
        -ResourceGroupName $resourceGroup)[0].Value
        # Get the resource group, in case we need that
        $return.resourceGroup = $resourceGroup
        # Get the storage context, as we can't depend
        # on using the default storage context
        $return.context = New-AzStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
        # Get the container, so we know where to
        # find/store blobs
        $return.container = $container
        # Return storage accounts to support finding all accounts for
        # a cluster
        $return.storageAccount = $storageAccountName
        $return.storageAccountKey = $storageAccountKey

        return $return
    }
    # Only export the verb-phrase things
    export-modulemember *-*
   ```

    Ten plik zawiera dwa moduły:

   * **Dodaj HDInsightFile** — używany do przekazywania plików do klastra
   * **Start-HBaseExample** — używane do uruchamiania klasy utworzonej wcześniej

2. Zapisz `hbase-runner.psm1` w pliku `hbaseapp` katalogu.

3. Rejestrowanie modułów przy użyciu programu Azure PowerShell. Otwórz nowe okno programu Azure PowerShell i edytować poniższe polecenie, zastępując `CLUSTERNAME` nazwą klastra. Następnie wprowadź następujące polecenia:

    ```powershell
    cd C:\HDI\hbaseapp
    $myCluster = "CLUSTERNAME"
    Import-Module .\hbase-runner.psm1
    ```

4. Użyj następującego polecenia do przekazania `hbaseapp-1.0-SNAPSHOT.jar` do klastra.

    ```powershell
    Add-HDInsightFile -localPath target\hbaseapp-1.0-SNAPSHOT.jar -destinationPath example/jars/hbaseapp-1.0-SNAPSHOT.jar -clusterName $myCluster
    ```

    Po wyświetleniu monitu wprowadź nazwę logowania (administratora) klastra i hasło. Polecenie przekazuje `hbaseapp-1.0-SNAPSHOT.jar` do `example/jars` lokalizacji w podstawowy magazyn dla klastra.

5. Aby utworzyć tabelę za pomocą `hbaseapp`, użyj następującego polecenia:

    ```powershell
    Start-HBaseExample -className com.microsoft.examples.CreateTable -clusterName $myCluster
    ```

    Po wyświetleniu monitu wprowadź nazwę logowania (administratora) klastra i hasło.

    To polecenie umożliwia utworzenie tabeli o nazwie **osób** w bazie danych HBase w klastrze usługi HDInsight. To polecenie nie wyświetla żadnych danych wyjściowych w oknie konsoli.

6. Aby poszukać wpisów w tabeli, użyj następującego polecenia:

    ```powershell
    Start-HBaseExample -className com.microsoft.examples.SearchByEmail -clusterName $myCluster -emailRegex contoso.com
    ```

    Po wyświetleniu monitu wprowadź nazwę logowania (administratora) klastra i hasło.

    To polecenie używa `SearchByEmail` klasy, aby wyszukać wszystkie wiersze gdzie `contactinformation` rodziny kolumn i `email` kolumny zawiera ciąg `contoso.com`. Powinny pojawić się następujące wyniki:

          Franklin Holtz - ID: 2
          Franklin Holtz - franklin@contoso.com - ID: 2
          Rae Schroeder - ID: 4
          Rae Schroeder - rae@contoso.com - ID: 4
          Gabriela Ingram - ID: 6
          Gabriela Ingram - gabriela@contoso.com - ID: 6

    Za pomocą **fabrikam.com** dla `-emailRegex` wartość zwraca użytkowników, którzy mają **fabrikam.com** w polu wiadomości e-mail. Można również użyć wyrażeń regularnych, jako wyszukiwany termin. Na przykład **^ r** zwraca wiadomości e-mail, adresy, które zaczynają się od litery "r".

7. Aby usunąć tabeli, użyj następującego polecenia:

    ```PowerShell
    Start-HBaseExample -className com.microsoft.examples.DeleteTable -clusterName $myCluster
    ```

### <a name="no-results-or-unexpected-results-when-using-start-hbaseexample"></a>Nie wyników lub nieoczekiwane wyniki, korzystając z Start HBaseExample

Użyj `-showErr` parametru, aby wyświetlić błędzie standardowym (STDERR), które są generowane podczas wykonywania zadania.

## <a name="next-steps"></a>Kolejne kroki

[Dowiedz się, jak używać SQuirreL SQL z bazy danych Apache HBase](apache-hbase-phoenix-squirrel-linux.md)
