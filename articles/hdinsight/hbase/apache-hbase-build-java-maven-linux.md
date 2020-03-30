---
title: Tworzenie klienta Java HBase dla usługi Azure HDInsight za pomocą apache Maven
description: Dowiedz się, jak używać apache Maven do tworzenia aplikacji Apache HBase opartej na języku Java, a następnie wdrażania jej w usłudze HBase w usłudze Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,seodec18
ms.date: 12/24/2019
ms.openlocfilehash: 3e9b23ce450e45dfedcee8b20e09b1c2b52b6e68
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75495783"
---
# <a name="build-java-applications-for-apache-hbase"></a>Tworzenie aplikacji Java dla Apache HBase

Dowiedz się, jak utworzyć aplikację [Apache HBase](https://hbase.apache.org/) w języku Java. Następnie użyj aplikacji z HBase na platformie Azure HDInsight.

Kroki w tym dokumencie używają [Apache Maven](https://maven.apache.org/) do tworzenia i tworzenia projektu. Maven to narzędzie do zarządzania projektami i zrozumienia oprogramowania, które pozwala na tworzenie oprogramowania, dokumentacji i raportów dla projektów Java.

## <a name="prerequisites"></a>Wymagania wstępne

* Klaster Apache HBase w programie HDInsight. Zobacz [Wprowadzenie do apache HBase](./apache-hbase-tutorial-get-started-linux.md).

* [Zestaw Java Developer Kit (JDK) w wersji 8](https://aka.ms/azure-jdks).

* [Apache Maven](https://maven.apache.org/download.cgi) prawidłowo [zainstalowany](https://maven.apache.org/install.html) zgodnie z Apache.  Maven to system budowania projektów java.

* Klient SSH. Aby uzyskać więcej informacji, zobacz [Łączenie się z usługą HDInsight (Apache Hadoop) przy użyciu protokołu SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

* Jeśli korzystasz z programu PowerShell, potrzebny jest [moduł AZ](https://docs.microsoft.com/powershell/azure/overview).

* Edytor tekstów. W tym artykule użyto notatnika firmy Microsoft.

## <a name="test-environment"></a>Środowisko testowe

W tym artykule użyto komputera z systemem Windows 10.  Polecenia zostały wykonane w wierszu polecenia, a różne pliki były edytowane za pomocą Notatnika. Odpowiednio zmodyfikuj dla swojego środowiska.

W wierszu polecenia wprowadź poniższe polecenia, aby utworzyć środowisko pracy:

```cmd
IF NOT EXIST C:\HDI MKDIR C:\HDI
cd C:\HDI
```

## <a name="create-a-maven-project"></a>Tworzenie projektu Maven

1. Wprowadź następujące polecenie, aby utworzyć projekt Maven o nazwie **hbaseapp:**

    ```cmd
    mvn archetype:generate -DgroupId=com.microsoft.examples -DartifactId=hbaseapp -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false

    cd hbaseapp
    mkdir conf
    ```

    To polecenie tworzy katalog `hbaseapp` o nazwie w bieżącej lokalizacji, który zawiera podstawowy projekt Maven. Drugie polecenie zmienia katalog roboczy na `hbaseapp`. Trzecie polecenie tworzy nowy katalog, `conf`który będzie używany później. Katalog `hbaseapp` zawiera następujące elementy:

    * `pom.xml`: Model obiektu projektu[(POM)](https://maven.apache.org/guides/introduction/introduction-to-the-pom.html)zawiera informacje i szczegóły konfiguracji używane do tworzenia projektu.
    * `src\main\java\com\microsoft\examples`: Zawiera kod aplikacji.
    * `src\test\java\com\microsoft\examples`: Zawiera testy dla aplikacji.

2. Usuń wygenerowany przykładowy kod. Usuń wygenerowane pliki `AppTest.java`testowe `App.java` i aplikacyjne oraz wprowadzając poniższe polecenia:

    ```cmd
    DEL src\main\java\com\microsoft\examples\App.java
    DEL src\test\java\com\microsoft\examples\AppTest.java
    ```

## <a name="update-the-project-object-model"></a>Aktualizowanie modelu obiektu projektu

Aby uzyskać pełne odniesienie do pliku pom.xml, zobacz https://maven.apache.org/pom.html.  Otwórz, `pom.xml` wpisując poniższe polecenie:

```cmd
notepad pom.xml
```

### <a name="add-dependencies"></a>Dodawanie zależności

W `pom.xml`sekcji dodaj następujący `<dependencies>` tekst:

```xml
<dependency>
    <groupId>org.apache.hbase</groupId>
    <artifactId>hbase-shaded-client</artifactId>
    <version>1.1.2</version>
</dependency>
<dependency>
    <groupId>org.apache.phoenix</groupId>
    <artifactId>phoenix-core</artifactId>
    <version>4.14.1-HBase-1.1</version>
</dependency>
```  

W tej sekcji wskazuje, że projekt wymaga **składników hbase-client** i **phoenix-core.** W czasie kompilacji te zależności są pobierane z domyślnego repozytorium Maven. Możesz użyć [wyszukiwania maven centralnego repozytorium,](https://search.maven.org/artifact/org.apache.hbase/hbase-client/1.1.2/jar) aby dowiedzieć się więcej o tej zależności.

> [!IMPORTANT]  
> Numer wersji klienta hbase musi być zgodny z wersją bazy danych Apache HBase dostarczoną z klastrem HDInsight. Użyj poniższej tabeli, aby znaleźć poprawny numer wersji.

| Wersja klastra HDInsight | Apache HBase wersja do użycia |
| --- | --- |
| 3.6 | 1.1.2 |
| 4.0 | 2.0.0 |

Aby uzyskać więcej informacji na temat wersji i składników HDInsight, zobacz [Jakie są różne komponenty Apache Hadoop dostępne w programie HDInsight](../hdinsight-component-versioning.md).

### <a name="build-configuration"></a>Konfiguracja kompilacji

Wtyczki Maven umożliwiają dostosowanie etapów budowy projektu. Ta sekcja służy do dodawania wtyczek, zasobów i innych opcji konfiguracji kompilacji.

Dodaj następujący kod `pom.xml` do pliku, a następnie zapisz i zamknij plik. Ten tekst musi `<project>...</project>` znajdować się wewnątrz znaczników w `</dependencies>` `</project>`pliku, na przykład między i .

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
                <version>3.8.1</version>
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

W tej sekcji skonfigurowano zasób (`conf/hbase-site.xml`) zawierający informacje o konfiguracji bazy danych HBase.

> [!NOTE]  
> Można również ustawić wartości konfiguracji za pomocą kodu. Zobacz komentarze w `CreateTable` przykładzie.

W tej sekcji skonfigurowano również [Wtyczkę Kompilatora Apache Maven](https://maven.apache.org/plugins/maven-compiler-plugin/) i [wtyczkę Apache Maven Shade](https://maven.apache.org/plugins/maven-shade-plugin/). Wtyczka kompilatora służy do kompilowania topologii. Wtyczka cienia służy do zapobiegania powielaniu licencji w pakiecie JAR, który jest zbudowany przez Maven. Ta wtyczka jest używana, aby zapobiec "zduplikowane pliki licencji" błąd w czasie wykonywania w klastrze HDInsight. Korzystanie z maven-shade-plugin z `ApacheLicenseResourceTransformer` implementacją zapobiega błędowi.

Maven-shade-plugin produkuje również uber jar, który zawiera wszystkie zależności wymagane przez aplikację.

### <a name="download-the-hbase-sitexml"></a>Pobierz hbase-site.xml

Użyj następującego polecenia, aby skopiować konfigurację bazy `conf` danych HBase z klastra HBase do katalogu. Zamień `CLUSTERNAME` nazwę klastra HDInsight, a następnie wprowadź polecenie:

```cmd
scp sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/etc/hbase/conf/hbase-site.xml ./conf/hbase-site.xml
```

## <a name="create-the-application"></a>Tworzenie aplikacji

### <a name="implement-a-createtable-class"></a>Implementowanie klasy CreateTable

Wprowadź poniższe polecenie, aby utworzyć `CreateTable.java`i otworzyć nowy plik . Wybierz **pozycję Tak** w wierszu polecenia, aby utworzyć nowy plik.

```cmd
notepad src\main\java\com\microsoft\examples\CreateTable.java
```

Następnie skopiuj i wklej poniższy kod java do nowego pliku. Następnie zamknij plik.

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

Ten kod `CreateTable` jest klasą, która `people` tworzy tabelę o nazwie i wypełnić go z niektórych wstępnie zdefiniowanych użytkowników.

### <a name="implement-a-searchbyemail-class"></a>Implementowanie klasy SearchByEmail

Wprowadź poniższe polecenie, aby utworzyć `SearchByEmail.java`i otworzyć nowy plik . Wybierz **pozycję Tak** w wierszu polecenia, aby utworzyć nowy plik.

```cmd
notepad src\main\java\com\microsoft\examples\SearchByEmail.java
```

Następnie skopiuj i wklej poniższy kod java do nowego pliku. Następnie zamknij plik.

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

Klasa `SearchByEmail` może służyć do wykonywania zapytań o wiersze według adresu e-mail. Ponieważ używa filtru wyrażenia regularnego, można podać ciąg lub wyrażenie regularne podczas korzystania z klasy.

### <a name="implement-a-deletetable-class"></a>Implementowanie klasy DeleteTable

Wprowadź poniższe polecenie, aby utworzyć `DeleteTable.java`i otworzyć nowy plik . Wybierz **pozycję Tak** w wierszu polecenia, aby utworzyć nowy plik.

```cmd
notepad src\main\java\com\microsoft\examples\DeleteTable.java
```

Następnie skopiuj i wklej poniższy kod java do nowego pliku. Następnie zamknij plik.

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

Klasa `DeleteTable` czyści tabele HBase utworzone w tym przykładzie przez wyłączenie `CreateTable` i upuszczenie tabeli utworzonej przez klasę.

## <a name="build-and-package-the-application"></a>Tworzenie i pakowanie aplikacji

1. Z `hbaseapp` katalogu użyj następującego polecenia, aby utworzyć plik JAR zawierający aplikację:

    ```cmd
    mvn clean package
    ```

    To polecenie tworzy i pakuje aplikację do pliku .jar.

2. Po zakończeniu polecenia `hbaseapp/target` katalog zawiera plik o `hbaseapp-1.0-SNAPSHOT.jar`nazwie .

   > [!NOTE]  
   > Plik `hbaseapp-1.0-SNAPSHOT.jar` jest uber jar. Zawiera wszystkie zależności wymagane do uruchomienia aplikacji.

## <a name="upload-the-jar-and-run-jobs-ssh"></a>Prześlij jar i uruchom zadania (SSH)

Poniższe kroki `scp` można wykonać, aby skopiować JAR do głównego węzła głównego bazy danych Apache HBase w klastrze HDInsight. Polecenie `ssh` jest następnie używane do łączenia się z klastrem i uruchamiania przykładu bezpośrednio w węźle głównym.

1. Przekaż słoik do klastra. Zamień `CLUSTERNAME` na nazwę klastra USŁUGI HDInsight, a następnie wprowadź następujące polecenie:

    ```cmd
    scp ./target/hbaseapp-1.0-SNAPSHOT.jar sshuser@CLUSTERNAME-ssh.azurehdinsight.net:hbaseapp-1.0-SNAPSHOT.jar
    ```

2. Połącz się z klastrem HBase. Zamień `CLUSTERNAME` na nazwę klastra USŁUGI HDInsight, a następnie wprowadź następujące polecenie:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

3. Aby utworzyć tabelę HBase przy użyciu aplikacji Java, użyj następującego polecenia w otwartym połączeniu ssh:

    ```bash
    yarn jar hbaseapp-1.0-SNAPSHOT.jar com.microsoft.examples.CreateTable
    ```

    To polecenie tworzy tabelę HBase o nazwie **osoby**i wypełnia ją danymi.

4. Aby wyszukać adresy e-mail przechowywane w tabeli, użyj następującego polecenia:

    ```bash
    yarn jar hbaseapp-1.0-SNAPSHOT.jar com.microsoft.examples.SearchByEmail contoso.com
    ```

    Otrzymujesz następujące wyniki:

        Franklin Holtz - ID: 2
        Franklin Holtz - franklin@contoso.com - ID: 2
        Rae Schroeder - ID: 4
        Rae Schroeder - rae@contoso.com - ID: 4
        Gabriela Ingram - ID: 6
        Gabriela Ingram - gabriela@contoso.com - ID: 6

5. Aby usunąć tabelę, użyj następującego polecenia:

    ```bash
    yarn jar hbaseapp-1.0-SNAPSHOT.jar com.microsoft.examples.DeleteTable
    ```

## <a name="upload-the-jar-and-run-jobs-powershell"></a>Przekazywanie jar i uruchamianie zadań (PowerShell)

Poniższe kroki używają [modułu AZ](https://docs.microsoft.com/powershell/azure/new-azureps-module-az) programu Azure PowerShell do przekazania jar do magazynu domyślnego dla klastra Apache HBase. Polecenia cmdlet HDInsight są następnie używane do zdalnego uruchamiania przykładów.

1. Po zainstalowaniu i skonfigurowaniu modułu AZ należy utworzyć plik o nazwie `hbase-runner.psm1`. Użyj następującego tekstu jako zawartości tego pliku:

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

   * **Add-HDInsightFile** — służy do przekazywania plików do klastra
   * **Start-HBaseExample** - używany do uruchamiania klas utworzonych wcześniej

2. Zapisz `hbase-runner.psm1` plik w `hbaseapp` katalogu.

3. Zarejestruj moduły za pomocą programu Azure PowerShell. Otwórz nowe okno programu Azure PowerShell i `CLUSTERNAME` edytuj poniższe polecenie, zastępując nazwą klastra. Następnie wprowadź następujące polecenia:

    ```powershell
    cd C:\HDI\hbaseapp
    $myCluster = "CLUSTERNAME"
    Import-Module .\hbase-runner.psm1
    ```

4. Użyj następującego polecenia, `hbaseapp-1.0-SNAPSHOT.jar` aby przekazać je do klastra.

    ```powershell
    Add-HDInsightFile -localPath target\hbaseapp-1.0-SNAPSHOT.jar -destinationPath example/jars/hbaseapp-1.0-SNAPSHOT.jar -clusterName $myCluster
    ```

    Po wyświetleniu monitu wprowadź nazwę i hasło logowania klastra (administratora). Polecenie przekazuje `hbaseapp-1.0-SNAPSHOT.jar` do `example/jars` lokalizacji w magazynie podstawowym dla klastra.

5. Aby utworzyć tabelę `hbaseapp`za pomocą polecenia , użyj następującego polecenia:

    ```powershell
    Start-HBaseExample -className com.microsoft.examples.CreateTable -clusterName $myCluster
    ```

    Po wyświetleniu monitu wprowadź nazwę i hasło logowania klastra (administratora).

    To polecenie tworzy tabelę o nazwie **osoby** w bazie danych HBase w klastrze usługi HDInsight. To polecenie nie pokazuje żadnych danych wyjściowych w oknie konsoli.

6. Aby wyszukać wpisy w tabeli, użyj następującego polecenia:

    ```powershell
    Start-HBaseExample -className com.microsoft.examples.SearchByEmail -clusterName $myCluster -emailRegex contoso.com
    ```

    Po wyświetleniu monitu wprowadź nazwę i hasło logowania klastra (administratora).

    To polecenie używa `SearchByEmail` klasy do wyszukiwania wierszy, w `contactinformation` `email` których rodzina kolumn `contoso.com`i kolumna zawiera ciąg . Należy otrzymać następujące wyniki:

          Franklin Holtz - ID: 2
          Franklin Holtz - franklin@contoso.com - ID: 2
          Rae Schroeder - ID: 4
          Rae Schroeder - rae@contoso.com - ID: 4
          Gabriela Ingram - ID: 6
          Gabriela Ingram - gabriela@contoso.com - ID: 6

    Użycie **fabrikam.com** dla `-emailRegex` wartości zwraca użytkowników, którzy mają **fabrikam.com** w polu wiadomości e-mail. Jako wyszukiwany termin można również użyć wyrażeń regularnych. Na przykład **^r** zwraca adresy e-mail, które zaczynają się od litery "r".

7. Aby usunąć tabelę, użyj następującego polecenia:

    ```PowerShell
    Start-HBaseExample -className com.microsoft.examples.DeleteTable -clusterName $myCluster
    ```

### <a name="no-results-or-unexpected-results-when-using-start-hbaseexample"></a>Brak wyników lub nieoczekiwanych wyników podczas korzystania z Start-HBaseExample

Użyj `-showErr` parametru, aby wyświetlić błąd standardowy (STDERR), który jest produkowany podczas uruchamiania zadania.

## <a name="next-steps"></a>Następne kroki

[Dowiedz się, jak korzystać z usługi SQLLine z apache HBase](apache-hbase-query-with-phoenix.md)
