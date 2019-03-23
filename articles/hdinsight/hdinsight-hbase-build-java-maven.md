---
title: Tworzenie aplikacji bazy danych HBase w języku Java na podstawie Windows Azure HDInsight
description: Informacje o sposobie tworzenia aplikacji opartych na języku Java bazy danych Apache HBase, a następnie wdrożyć ją do klastra z systemem Windows Azure HDInsight przy użyciu narzędzia Apache Maven.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/05/2017
ms.author: hrasheed
ROBOTS: NOINDEX
ms.openlocfilehash: c0ecfd3f148cecae713740ef37d4fe7a2e2f184f
ms.sourcegitcommit: 223604d8b6ef20a8c115ff877981ce22ada6155a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/22/2019
ms.locfileid: "58361816"
---
# <a name="use-apache-maven-to-build-java-applications-that-use-apache-hbase-with-windows-based-hdinsight-apache-hadoop"></a>Tworzenie aplikacji Java za pomocą usług bazy danych Apache HBase HDInsight dla komputerów z systemem Windows (Apache Hadoop) przy użyciu narzędzia Apache Maven
Dowiedz się, jak utworzyć i skompilować [bazy danych Apache HBase](https://hbase.apache.org/) aplikacji w języku Java przy użyciu narzędzia Apache Maven. Następnie za pomocą aplikacji usługi Azure HDInsight (Apache Hadoop).

[Narzędzia Apache Maven](https://maven.apache.org/) to oprogramowanie projektu zarządzania i zrozumienie narzędzie, które umożliwia tworzenie oprogramowania, dokumentację i raporty dla projektów języka Java. W tym artykule dowiesz się, jak z niej korzystać do tworzenia podstawowych aplikacji Java, która tworzy, zapytań i usuwa tabel HBase w klastrze usługi Azure HDInsight.

> [!IMPORTANT]  
> Kroki opisane w tym dokumencie wymagają klastra usługi HDInsight, który używa Windows. Linux jest jedynym systemem operacyjnym używanym w połączeniu z usługą HDInsight w wersji 3.4 lub nowszą. Aby uzyskać więcej informacji, zobacz sekcję [HDInsight retirement on Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement) (Wycofanie usługi HDInsight w systemie Windows).

## <a name="requirements"></a>Wymagania

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

* [Platforma Java JDK](https://aka.ms/azure-jdks) 7 lub nowszy
* [Apache Maven](https://maven.apache.org/)
* Klaster HDInsight z systemem Windows z bazą danych HBase

    > [!NOTE]  
    > Kroki opisane w niniejszym dokumencie zostały przetestowane z wersji klastra HDInsight 3.2 i 3.3. Wartości domyślnych w przykładach są dla klastra HDInsight 3.3.

## <a name="create-the-project"></a>Tworzenie projektu
1. W wierszu polecenia w środowisku deweloperskim Zmień katalogi na lokalizację, w której chcesz utworzyć projekt, na przykład `cd code\hdinsight`.
2. Użyj **mvn** polecenia, który jest instalowany za pomocą narzędzia Maven, można wygenerować funkcją szkieletów dla projektu.

        mvn archetype:generate -DgroupId=com.microsoft.examples -DartifactId=hbaseapp -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false

    To polecenie tworzy katalog w bieżącej lokalizacji o nazwie określonej przez **artifactID** parametru (**hbaseapp** w tym przykładzie.) Ten katalog zawiera następujące elementy:

   * **pom.xml**:  Model obiektu projektu ([POM](https://maven.apache.org/guides/introduction/introduction-to-the-pom.html)) zawiera informacje i szczegóły konfiguracji używane do tworzenia projektu.
   * **SRC**: Katalog, który zawiera **main\java\com\microsoft\examples** katalogu, w którym będzie utworzyć aplikację.
3. Usuń **src\test\java\com\microsoft\examples\apptest.java** pliku, ponieważ nie jest używany w tym przykładzie.

## <a name="update-the-project-object-model"></a>Aktualizowanie modelu obiektu projektu
1. Edytuj **pom.xml** pliku i Dodaj następujący kod wewnątrz `<dependencies>` sekcji:

        <dependency>
          <groupId>org.apache.hbase</groupId>
          <artifactId>hbase-client</artifactId>
          <version>1.1.2</version>
        </dependency>

    W tej sekcji opisano narzędzia Maven, że projekt wymaga **klienta bazy danych hbase** wersji **1.1.2**. W czasie kompilacji Ta zależność zostanie pobrane z repozytorium Maven domyślne. Możesz użyć [Apache centralne repozytorium funkcji wyszukiwania narzędzia Maven](https://search.maven.org/#artifactdetails%7Corg.apache.hbase%7Chbase-client%7C0.98.4-hadoop2%7Cjar) Aby dowiedzieć się więcej na temat tej zależności.

   > [!IMPORTANT]  
   > Numer wersji musi być zgodny z wersją bazy danych HBase, który znajduje się z klastrem usługi HDInsight. Skorzystaj z poniższej tabeli, aby znaleźć numer poprawnej wersji.
   >
   >

   | Wersja klastra HDInsight | Wersja bazy danych HBase do użycia |
   | --- | --- |
   | 3.2 |0.98.4-hadoop2 |
   | 3.3 |1.1.2 |

    Aby uzyskać więcej informacji na temat wersji HDInsight i składników, zobacz [różnych składnikach platformy Apache Hadoop, udostępniono HDInsight](hdinsight-component-versioning.md).
2. Jeśli używasz klastra usługi HDInsight 3.3, musisz również dodać następujące polecenie, aby `<dependencies>` sekcji:

        <dependency>
            <groupId>org.apache.phoenix</groupId>
            <artifactId>phoenix-core</artifactId>
            <version>4.4.0-HBase-1.1</version>
        </dependency>

    Ta zależność załaduje phoenix podstawowe składniki, które są używane przez wersję bazy danych Hbase 1.1.x.
3. Dodaj następujący kod do **pom.xml** pliku. W tej sekcji musi znajdować się wewnątrz `<project>...</project>` tagów w pliku, na przykład między `</dependencies>` i `</project>`.

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
                    <source>1.7</source>
                    <target>1.7</target>
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

    `<resources>` Sekcji konfiguruje zasobu (**conf\hbase-site.xml**) zawierający informacje o konfiguracji dla bazy danych HBase.

   > [!NOTE]  
   > Można również ustawić wartości konfiguracji za pomocą kodu. Zobacz komentarze w **CreateTable** przykład znajdujący się jak to zrobić.
   >
   >

    To `<plugins>` konfiguruje sekcji [wtyczki programu Apache Maven kompilatora](https://maven.apache.org/plugins/maven-compiler-plugin/) i [wtyczki programu Apache Maven odcień](https://maven.apache.org/plugins/maven-shade-plugin/). Wtyczka kompilatora jest używana do kompilowania topologii. Wtyczka odcień używany w celu zapobiegania duplikatów licencji w pakiet JAR, który jest kompilowany przez narzędzia Maven. Przyczyny, dla której jest on używany jest, że pliki licencji zduplikowane powodują wystąpienie błędu w czasie wykonywania w klastrze HDInsight. Przy użyciu narzędzia maven odcień wtyczki za pomocą `ApacheLicenseResourceTransformer` implementacji zapobiega tego błędu.

    Wtyczki odcień maven tworzy również plik jar uber (lub fat jar) zawierający wszystkie zależności, które są wymagane przez aplikację.
4. Zapisz plik **pom.xml**.
5. Utwórz nowy katalog o nazwie **conf** w **hbaseapp** katalogu. W **conf** katalogu Utwórz plik o nazwie **hbase-site.xml**. Zawartość pliku należy zastosować następujące:

        <?xml version="1.0"?>
        <?xml-stylesheet type="text/xsl" href="configuration.xsl"?>
        <!--
        /**
          * Copyright 2010 The Apache Software Foundation
          *
          * Licensed to the Apache Software Foundation (ASF) under one
          * or more contributor license agreements.  See the NOTICE file
          * distributed with this work for additional information
          * regarding copyright ownership.  The ASF licenses this file
          * to you under the Apache License, Version 2.0 (the
          * "License"); you may not use this file except in compliance
          * with the License.  You may obtain a copy of the License at
          *
          *     https://www.apache.org/licenses/LICENSE-2.0
          *
          * Unless required by applicable law or agreed to in writing, software
          * distributed under the License is distributed on an "AS IS" BASIS,
          * WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
          * See the License for the specific language governing permissions and
          * limitations under the License.
          */
        -->
        <configuration>
          <property>
            <name>hbase.cluster.distributed</name>
            <value>true</value>
          </property>
          <property>
            <name>hbase.zookeeper.quorum</name>
            <value>zookeeper0,zookeeper1,zookeeper2</value>
          </property>
          <property>
            <name>hbase.zookeeper.property.clientPort</name>
            <value>2181</value>
          </property>
        </configuration>

    Ten plik będzie używany do załadowania konfiguracji bazy danych HBase dla klastra usługi HDInsight.

   > [!NOTE]  
   > Jest to plik bazy danych hbase minimal-site.xml, i zawiera bez ustawień minimalny dla klastra HDInsight.

6. Zapisz **hbase-site.xml** pliku.

## <a name="create-the-application"></a>Tworzenie aplikacji
1. Przejdź do **hbaseapp\src\main\java\com\microsoft\examples** katalogu i Zmień nazwę app.java plik **CreateTable.java**.
2. Otwórz **CreateTable.java** plik i zastąpić istniejącą zawartość następującym kodem:

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
            // The following sets the znode root for Linux-based HDInsight
            //config.set("zookeeper.znode.parent","/hbase-unsecure");

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

    Jest to **CreateTable** klasy, które spowoduje utworzenie tabeli o nazwie **osób** i wypełnić ją za pomocą wstępnie zdefiniowanych użytkowników.
3. Zapisz **CreateTable.java** pliku.
4. W **hbaseapp\src\main\java\com\microsoft\examples** katalogu, Utwórz nowy plik o nazwie **SearchByEmail.java**. Użyj poniższego kodu, jako zawartość tego pliku:

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

            // Create a new regex filter
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

    **SearchByEmail** klasy może służyć do kwerendy dla wierszy przy użyciu adresu e-mail. Ponieważ używa ona filtr z wyrażeniem regularnym, możesz podać ciąg lub wyrażenie regularne, korzystając z tej klasy.
5. Zapisz **SearchByEmail.java** pliku.
6. W **hbaseapp\src\main\hava\com\microsoft\examples** katalogu, Utwórz nowy plik o nazwie **DeleteTable.java**. Użyj poniższego kodu, jako zawartość tego pliku:

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

    Ta klasa jest wyłączenie i upuszczając tabelę utworzoną przez oczyszczania, w tym przykładzie **CreateTable** klasy.
7. Zapisz **DeleteTable.java** pliku.

## <a name="build-and-package-the-application"></a>Tworzenie i utworzyć pakiet aplikacji
1. Otwórz wiersz polecenia i zmień katalog na **hbaseapp** katalogu.
2. Użyj następującego polecenia do tworzenia pliku JAR, który zawiera aplikację:

        mvn clean package

    To czyści wszystkie poprzednie artefaktów kompilacji, pobierze wszelkie zależności, które nie są już zainstalowane, a następnie tworzy i pakiety aplikacji.
3. Po zakończeniu wykonywania polecenia **hbaseapp\target** katalog zawiera plik o nazwie **hbaseapp-1.0 — SNAPSHOT.jar**.

   > [!NOTE]  
   > **Hbaseapp-1.0 — SNAPSHOT.jar** plik jest uber jar (czasami nazywany fat pliku jar,), który zawiera wszystkie zależności, które są wymagane do uruchomienia aplikacji.

## <a name="upload-the-jar-file-and-start-a-job"></a>Przekazywanie pliku JAR i uruchamianie zadania
Istnieje wiele sposobów, aby przekazać plik do klastra usługi HDInsight, zgodnie z opisem w [przekazywanie danych na potrzeby zadań usługi Apache Hadoop w HDInsight](hdinsight-upload-data.md). Poniższe kroki przy użyciu programu Azure PowerShell.

[!INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

1. Po zainstalowaniu i skonfigurowaniu programu Azure PowerShell Utwórz nowy plik o nazwie **hbase runner.psm1**. Zawartość tego pliku należy zastosować następujące:

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

    Ten plik zawiera dwa moduły:

   * **Dodaj HDInsightFile** — używany do przekazywania plików do HDInsight
   * **Start-HBaseExample** — używane do uruchamiania klasy utworzonej wcześniej
2. Zapisz **hbase runner.psm1** pliku.
3. Otwórz nowe okno programu Azure PowerShell, zmień katalog na **hbaseapp** katalogu, a następnie uruchom następujące polecenie.

        PS C:\ Import-Module c:\path\to\hbase-runner.psm1

    Zmień ścieżkę do lokalizacji **hbase runner.psm1** pliku utworzonego wcześniej. Rejestruje moduł tę sesję programu Azure PowerShell.
4. Użyj następującego polecenia do przekazania **hbaseapp-1.0 — SNAPSHOT.jar** do klastra usługi HDInsight.

        Add-HDInsightFile -localPath target\hbaseapp-1.0-SNAPSHOT.jar -destinationPath example/jars/hbaseapp-1.0-SNAPSHOT.jar -clusterName hdinsightclustername

    Zastąp **nazwa_klastra_usługi_hdinsight** nazwą klastra usługi HDInsight. Polecenie przekazuje **hbaseapp-1.0 — SNAPSHOT.jar** do **przykład/plikach JAR** lokalizacji w podstawowy magazyn dla klastra usługi HDInsight.
5. Po przekazaniu plików, użyj następującego kodu, aby utworzyć tabelę za pomocą **hbaseapp**:

        Start-HBaseExample -className com.microsoft.examples.CreateTable -clusterName hdinsightclustername

    Zastąp **nazwa_klastra_usługi_hdinsight** nazwą klastra usługi HDInsight.

    To polecenie tworzy nową tabelę o nazwie **osób** w klastrze usługi HDInsight. To polecenie nie wyświetla żadnych danych wyjściowych w oknie konsoli.
6. Aby poszukać wpisów w tabeli, użyj następującego polecenia:

        Start-HBaseExample -className com.microsoft.examples.SearchByEmail -clusterName hdinsightclustername -emailRegex contoso.com

    Zastąp **nazwa_klastra_usługi_hdinsight** nazwą klastra usługi HDInsight.

    To polecenie używa **SearchByEmail** klasy, aby wyszukać wszystkie wiersze gdzie **contactinformation** rodziny kolumn i **e-mail** kolumny zawiera ciąg **contoso.com**. Powinny pojawić się następujące wyniki:

          Franklin Holtz - ID: 2
          Franklin Holtz - franklin@contoso.com - ID: 2
          Rae Schroeder - ID: 4
          Rae Schroeder - rae@contoso.com - ID: 4
          Gabriela Ingram - ID: 6
          Gabriela Ingram - gabriela@contoso.com - ID: 6

    Za pomocą **fabrikam.com** dla `-emailRegex` wartość zwraca użytkowników, którzy mają **fabrikam.com** w polu wiadomości e-mail. Ponieważ to wyszukiwanie jest implementowany przy użyciu filtru oparte na wyrażeniach regularnych, możesz również wprowadzić wyrażeń regularnych, takie jak **^ r**, które zwraca wpisy, których adres e-mail zaczyna się od litery "r".

## <a name="delete-the-table"></a>Usuń tabelę
Gdy wszystko będzie gotowe do przykładu, użyj następującego polecenia w sesji programu Azure PowerShell do usunięcia **osób** Tabela używana w tym przykładzie:

    Start-HBaseExample -className com.microsoft.examples.DeleteTable -clusterName hdinsightclustername

Zastąp **nazwa_klastra_usługi_hdinsight** nazwą klastra usługi HDInsight.

## <a name="troubleshooting"></a>Rozwiązywanie problemów
### <a name="no-results-or-unexpected-results-when-using-start-hbaseexample"></a>Nie wyników lub nieoczekiwane wyniki, korzystając z Start HBaseExample
Użyj `-showErr` parametru, aby wyświetlić błędzie standardowym (STDERR), które są generowane podczas wykonywania zadania.
