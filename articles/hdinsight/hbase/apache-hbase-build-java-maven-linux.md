---
title: Tworzenie klienta bazy danych HBase w języku Java przy użyciu narzędzia Apache Maven — Azure HDInsight
description: Informacje o sposobie tworzenia aplikacji opartych na języku Java bazy danych Apache HBase, a następnie wdrożyć ją do bazy danych HBase w usłudze Azure HDInsight przy użyciu narzędzia Apache Maven.
services: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,seodec18
ms.topic: conceptual
ms.date: 11/27/2018
ms.openlocfilehash: fa831ad878d214515849787988ccb32f6c57ce20
ms.sourcegitcommit: 223604d8b6ef20a8c115ff877981ce22ada6155a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/22/2019
ms.locfileid: "58361765"
---
# <a name="build-java-applications-for-apache-hbase"></a>Tworzenie aplikacji bazy danych Apache HBase w języku Java

Dowiedz się, jak utworzyć [bazy danych Apache HBase](https://hbase.apache.org/) aplikacji w języku Java. Następnie można używać aplikacji z bazą danych HBase w usłudze Azure HDInsight.

Kroki opisane w tej dokumentów użyj [narzędzia Apache Maven](https://maven.apache.org/) utworzyć i skompilować projekt. Maven to zarządzanie projektami oprogramowania oraz narzędzie zrozumienie, które umożliwia tworzenie oprogramowania, dokumentację i raporty dla projektów języka Java.

> [!NOTE]  
> Kroki opisane w niniejszym dokumencie zostały ostatnio przetestowana HDInsight 3.6.

> [!IMPORTANT]  
> Procedura przedstawiona w tym dokumencie wymaga klastra usługi HDInsight używającego systemu Linux. Linux jest jedynym systemem operacyjnym używanym w połączeniu z usługą HDInsight w wersji 3.4 lub nowszą. Aby uzyskać więcej informacji, zobacz sekcję [HDInsight retirement on Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement) (Wycofanie usługi HDInsight w systemie Windows).

## <a name="requirements"></a>Wymagania

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

* [Platforma Java JDK](https://aka.ms/azure-jdks) 8 lub nowszy.

    > [!NOTE]  
    > HDInsight 3.5 i większa wymaga Java 8. Wcześniejszych wersjach HDInsight wymagają Java 7.

* [Apache Maven](https://maven.apache.org/)

* [Klaster HDInsight Azure opartych na systemie Linux przy użyciu bazy danych Apache HBase](apache-hbase-tutorial-get-started-linux.md#create-apache-hbase-cluster)

## <a name="create-the-project"></a>Tworzenie projektu

1. W wierszu polecenia w środowisku deweloperskim Zmień katalogi na lokalizację, w której chcesz utworzyć projekt, na przykład `cd code\hbase`.

2. Użyj **mvn** polecenia, który jest instalowany za pomocą narzędzia Maven, można wygenerować funkcją szkieletów dla projektu.

    ```bash
    mvn archetype:generate -DgroupId=com.microsoft.examples -DartifactId=hbaseapp -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

    > [!NOTE]  
    > Jeśli używasz programu PowerShell, należy ująć `-D` parametrów w cudzysłów.
    >
    > `mvn archetype:generate "-DgroupId=com.microsoft.examples" "-DartifactId=hbaseapp" "-DarchetypeArtifactId=maven-archetype-quickstart" "-DinteractiveMode=false"`

    To polecenie tworzy katalog o takiej samej nazwie jak **artifactID** parametru (**hbaseapp** w tym przykładzie.) Ten katalog zawiera następujące elementy:

   * **pom.xml**:  Model obiektu projektu ([POM](https://maven.apache.org/guides/introduction/introduction-to-the-pom.html)) zawiera informacje i szczegóły konfiguracji używane do tworzenia projektu.
   * **SRC**: Katalog, który zawiera **main/java/com/microsoft/przykłady** katalogu, w którym można utworzyć aplikacji.

3. Usuń `src/test/java/com/microsoft/examples/apptest.java` pliku. Nie jest on używany w tym przykładzie.

## <a name="update-the-project-object-model"></a>Aktualizowanie modelu obiektu projektu

1. Edytuj `pom.xml` pliku i Dodaj następujący kod wewnątrz `<dependencies>` sekcji:

   ```xml
    <dependency>
        <groupId>org.apache.hbase</groupId>
        <artifactId>hbase-client</artifactId>
        <version>1.1.2</version>
    </dependency>
    <dependency>
        <groupId>org.apache.phoenix</groupId>
        <artifactId>phoenix-core</artifactId>
        <version>4.4.0-HBase-1.1</version>
    </dependency>
   ```

    W tej sekcji oznacza, że projekt musi **klienta bazy danych hbase** i **phoenix core** składników. W czasie kompilacji te zależności są pobierane z repozytorium Maven domyślne. Możesz użyć [centralnej wyszukiwania repozytorium narzędzia Maven](https://search.maven.org/#artifactdetails%7Corg.apache.hbase%7Chbase-client%7C0.98.4-hadoop2%7Cjar) Aby dowiedzieć się więcej na temat tej zależności.

   > [!IMPORTANT]  
   > Numer wersji klienta bazy danych hbase, musi być zgodny z wersją Apache HBase, który znajduje się z klastrem usługi HDInsight. Skorzystaj z poniższej tabeli, aby znaleźć numer poprawnej wersji.

   | Wersja klastra HDInsight | Wersja bazy danych Apache HBase do użycia |
   | --- | --- |
   | 3.2 |0.98.4-hadoop2 |
   | 3.3, 3.4, 3.5 i 3.6 |1.1.2 |

    Aby uzyskać więcej informacji na temat wersji HDInsight i składników, zobacz [różnych składnikach platformy Apache Hadoop, udostępniono HDInsight](../hdinsight-component-versioning.md).

3. Dodaj następujący kod do **pom.xml** pliku. Ten tekst musi znajdować się wewnątrz `<project>...</project>` tagów w pliku, na przykład między `</dependencies>` i `</project>`.

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
                    <version>3.3</version>
            <configuration>
                <source>1.8</source>
                <target>1.8</target>
            </configuration>
            </plugin>
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-shade-plugin</artifactId>
            <version>2.3</version>
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

4. Zapisz plik `pom.xml`.

5. Utwórz katalog o nazwie `conf` w `hbaseapp` katalogu. Ten katalog jest używany do przechowywania informacji o konfiguracji dotyczące nawiązywania połączenia bazy danych HBase.

6. Użyj następującego polecenia, aby skopiować konfigurację bazy danych HBase z klastra HBase na `conf` katalogu. Zastąp `USERNAME` nazwą logowania użytkownika protokołu SSH. Zastąp `CLUSTERNAME` nazwą klastra usługi HDInsight:

    ```bash
    scp USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:/etc/hbase/conf/hbase-site.xml ./conf/hbase-site.xml
    ```

   Aby uzyskać więcej informacji na temat korzystania z `ssh` i `scp`, zobacz [użycia protokołu SSH w usłudze HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="create-the-application"></a>Tworzenie aplikacji

1. Przejdź do `hbaseapp/src/main/java/com/microsoft/examples` katalogu i Zmień nazwę app.java plik `CreateTable.java`.

2. Otwórz `CreateTable.java` plik i zastąpić istniejącą zawartość z następującym tekstem:

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

    Ten kod jest **CreateTable** klasy, która tworzy tabelę o nazwie **osób** i wypełnić ją za pomocą wstępnie zdefiniowanych użytkowników.

3. Zapisz plik `CreateTable.java`.

4. W `hbaseapp/src/main/java/com/microsoft/examples` katalogu Utwórz plik o nazwie `SearchByEmail.java`. Użyj następującego tekstu jako zawartości tego pliku:

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

    **SearchByEmail** klasy może służyć do kwerendy dla wierszy przy użyciu adresu e-mail. Ponieważ używa ona filtr z wyrażeniem regularnym, możesz podać ciąg lub wyrażenie regularne, korzystając z tej klasy.

5. Zapisz plik `SearchByEmail.java`.

6. W `hbaseapp/src/main/hava/com/microsoft/examples` katalogu Utwórz plik o nazwie `DeleteTable.java`. Użyj następującego tekstu jako zawartości tego pliku:

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

    Ta klasa czyści tabele bazy danych HBase utworzone w tym przykładzie wyłączenie i upuszczając tabelę utworzoną przez `CreateTable` klasy.

7. Zapisz plik `DeleteTable.java`.

## <a name="build-and-package-the-application"></a>Tworzenie i utworzyć pakiet aplikacji

1. Z `hbaseapp` katalogu, użyj następującego polecenia do tworzenia pliku JAR, który zawiera aplikację:

    ```bash
    mvn clean package
    ```

    To polecenie tworzy i pakiety aplikacji do pliku JAR.

2. Po zakończeniu wykonywania polecenia `hbaseapp/target` katalog zawiera plik o nazwie `hbaseapp-1.0-SNAPSHOT.jar`.

   > [!NOTE]  
   > `hbaseapp-1.0-SNAPSHOT.jar` Plik znajduje się plik jar uber. Zawiera ona wszystkie zależności, które są wymagane do uruchomienia aplikacji.


## <a name="upload-the-jar-and-run-jobs-ssh"></a>Przekaż plik JAR i uruchamianie zadań (SSH)

Następujące kroki użycia `scp` można skopiować plik JAR z podstawowym węzłem usługi Apache HBase w klastrze HDInsight. `ssh` Polecenia jest następnie używany do łączenia z klastrem i uruchomić przykład bezpośrednio w węźle głównym.

1. Aby przekazać plik jar do klastra, użyj następującego polecenia:

    ```bash
    scp ./target/hbaseapp-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:hbaseapp-1.0-SNAPSHOT.jar
    ```

    Zastąp `USERNAME` nazwą logowania użytkownika protokołu SSH. Zastąp `CLUSTERNAME` nazwą klastra usługi HDInsight.

2. Do łączenia z klastrem HBase, użyj następującego polecenia:

    ```bash
    ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
    ```

    Zastąp `USERNAME` nazwę logowania użytkownika protokołu SSH. Zastąp `CLUSTERNAME` nazwą klastra usługi HDInsight.

3. Do tworzenia tabel HBase przy użyciu aplikacji języka Java, użyj następującego polecenia:

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

Poniższe kroki przekazać plik JAR do domyślnego magazynu klastra Apache HBase przy użyciu programu Azure PowerShell. Polecenia cmdlet HDInsight są następnie używane do zdalnego uruchamiania przykładów.

1. Po zainstalowaniu i skonfigurowaniu programu Azure PowerShell Utwórz plik o nazwie `hbase-runner.psm1`. Użyj następującego tekstu jako zawartości tego pliku:

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
            throw "No active Azure subscription found! If you have a subscription, use the Connect-AzAccount cmdlet to login to your subscription."
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

2. Zapisz plik `hbase-runner.psm1`.

3. Otwórz nowe okno programu Azure PowerShell, zmień katalog na `hbaseapp` katalogu, a następnie uruchom następujące polecenie:

    ```powershell
    PS C:\ Import-Module c:\path\to\hbase-runner.psm1
    ```

    Zmień ścieżkę do lokalizacji `hbase-runner.psm1` pliku utworzonego wcześniej. To polecenie rejestruje moduł przy użyciu programu Azure PowerShell.

4. Użyj następującego polecenia do przekazania `hbaseapp-1.0-SNAPSHOT.jar` do klastra.

    ```powershell
    Add-HDInsightFile -localPath target\hbaseapp-1.0-SNAPSHOT.jar -destinationPath example/jars/hbaseapp-1.0-SNAPSHOT.jar -clusterName hdinsightclustername
    ```

    Zastąp ciąg `hdinsightclustername` nazwą klastra. Po wyświetleniu monitu wprowadź nazwę logowania (administratora) klastra i hasło. Polecenie przekazuje `hbaseapp-1.0-SNAPSHOT.jar` do `example/jars` lokalizacji w podstawowy magazyn dla klastra.

5. Aby utworzyć tabelę za pomocą `hbaseapp`, użyj następującego polecenia:

    ```powershell
    Start-HBaseExample -className com.microsoft.examples.CreateTable -clusterName hdinsightclustername
    ```

    Zastąp ciąg `hdinsightclustername` nazwą klastra. Po wyświetleniu monitu wprowadź nazwę logowania (administratora) klastra i hasło.

    To polecenie umożliwia utworzenie tabeli o nazwie **osób** w bazie danych HBase w klastrze usługi HDInsight. To polecenie nie wyświetla żadnych danych wyjściowych w oknie konsoli.

6. Aby poszukać wpisów w tabeli, użyj następującego polecenia:

    ```powershell
    Start-HBaseExample -className com.microsoft.examples.SearchByEmail -clusterName hdinsightclustername -emailRegex contoso.com
    ```

    Zastąp ciąg `hdinsightclustername` nazwą klastra. Po wyświetleniu monitu wprowadź nazwę logowania (administratora) klastra i hasło.

    To polecenie używa `SearchByEmail` klasy, aby wyszukać wszystkie wiersze gdzie `contactinformation` rodziny kolumn i `email` kolumny zawiera ciąg `contoso.com`. Powinny pojawić się następujące wyniki:

          Franklin Holtz - ID: 2
          Franklin Holtz - franklin@contoso.com - ID: 2
          Rae Schroeder - ID: 4
          Rae Schroeder - rae@contoso.com - ID: 4
          Gabriela Ingram - ID: 6
          Gabriela Ingram - gabriela@contoso.com - ID: 6

    Za pomocą **fabrikam.com** dla `-emailRegex` wartość zwraca użytkowników, którzy mają **fabrikam.com** w polu wiadomości e-mail. Można również użyć wyrażeń regularnych, jako wyszukiwany termin. Na przykład **^ r** zwraca wiadomości e-mail, adresy, które zaczynają się od litery "r".

### <a name="no-results-or-unexpected-results-when-using-start-hbaseexample"></a>Nie wyników lub nieoczekiwane wyniki, korzystając z Start HBaseExample

Użyj `-showErr` parametru, aby wyświetlić błędzie standardowym (STDERR), które są generowane podczas wykonywania zadania.

## <a name="delete-the-table"></a>Usuń tabelę

Gdy wszystko będzie gotowe do przykładu, należy użyć następującego, można usunąć **osób** Tabela używana w tym przykładzie:

__Z `ssh` sesji__:

`yarn jar hbaseapp-1.0-SNAPSHOT.jar com.microsoft.examples.DeleteTable`

__Za pomocą programu Azure PowerShell__:

`Start-HBaseExample -className com.microsoft.examples.DeleteTable -clusterName hdinsightclustername`

## <a name="next-steps"></a>Kolejne kroki

[Dowiedz się, jak używać SQuirreL SQL z bazy danych Apache HBase](apache-hbase-phoenix-squirrel-linux.md)
